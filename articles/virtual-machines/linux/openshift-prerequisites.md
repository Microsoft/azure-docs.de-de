---
title: Voraussetzungen für OpenShift in Azure | Microsoft-Dokumentation
description: Voraussetzungen für die Bereitstellung von OpenShift in Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/19/2019
ms.author: haroldw
ms.openlocfilehash: d8a9b82e51c837af6343ddf851545d02299aa527
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61473884"
---
# <a name="common-prerequisites-for-deploying-openshift-in-azure"></a>Allgemeine Voraussetzungen für die Bereitstellung von OpenShift in Azure

In diesem Artikel werden allgemeine Voraussetzungen für die Bereitstellung von OpenShift Container Platform oder OKD in Azure beschrieben.

Die Installation von OpenShift verwendet Ansible-Playbooks. Ansible stellt die Verbindung zu allen Clusterhosts über Secure Shell (SSH) her, um die Installationsschritte abzuschließen.

Beim Herstellen der SSH-Verbindung mit den Remotehosts kann Ansible kein Kennwort eingeben. Daher darf dem privaten Schlüssel kein Kennwort (Passphrase) zugeordnet sein. Andernfalls ist die Bereitstellung nicht erfolgreich.

Da die virtuellen Computer (VMs) über Azure Resource Manager-Vorlagen bereitgestellt werden, wird der gleiche öffentliche Schlüssel für den Zugriff auf alle virtuellen Computer verwendet. Der entsprechende private Schlüssel muss auf dem virtuellen Computer, der auch alle Playbooks ausführt, vorhanden sein. Aus Sicherheitsgründen wird der private Schlüssel über eine Azure Key Vault-Instanz an den virtuellen Computer übergeben.

Wenn für Container beständiger Speicher benötigt wird, sind persistente Volumes erforderlich. OpenShift unterstützt für persistente Volumes virtuelle Azure-Festplatten (VHDs), dazu muss Azure jedoch zunächst als Cloudanbieter konfiguriert werden.

In diesem Modell führt OpenShift folgende Schritte durch:

- Erstellen eines VHD-Objekts in einem Azure Storage-Konto oder auf einem verwalteten Datenträger
- Einbinden der VHD in einen virtuellen Computer und Formatieren des Volumes
- Einbinden des Volumes in den Pod

Damit diese Konfiguration funktioniert, muss OpenShift zur Durchführung dieser Aufgaben in Azure berechtigt sein. Für diesen Zweck wird ein Dienstprinzipal verwendet. Der Dienstprinzipal ist ein Sicherheitskonto in Azure Active Directory, dem Berechtigungen für Ressourcen gewährt werden.

Der Dienstprinzipal benötigt Zugriff auf die Speicherkonten und virtuellen Computer, die den Cluster bilden. Wenn alle OpenShift-Clusterressourcen in einer einzelnen Ressourcengruppe bereitgestellt werden, können dem Dienstprinzipal Berechtigungen für diese Ressourcengruppe gewährt werden.

In diesem Leitfaden erfahren Sie, wie Sie die Artefakte für die Voraussetzungen erstellen.

> [!div class="checklist"]
> * Erstellen Sie einen Schlüsseltresor (Key Vault) zum Verwalten von SSH-Schlüsseln für den OpenShift-Cluster.
> * Erstellen Sie einen Dienstprinzipal für den Azure-Cloudanbieter.

Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

## <a name="sign-in-to-azure"></a>Anmelden bei Azure 
Melden Sie sich mit dem Befehl [az login](/cli/azure/reference-index) bei Ihrem Azure-Abonnement an, und befolgen Sie die Anweisungen auf dem Bildschirm, oder klicken Sie auf **Ausprobieren**, um Cloud Shell zu verwenden.

```azurecli 
az login
```
## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Erstellen Sie mit dem Befehl [az group create](/cli/azure/group) eine Ressourcengruppe. Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden. Sie sollten zum Hosten des Schlüsseltresors eine dedizierte Ressourcengruppe verwenden. Diese Gruppe ist von der Ressourcengruppe getrennt, in der die OpenShift-Clusterressourcen bereitgestellt werden.

Im folgenden Beispiel wird eine Ressourcengruppe namens *keyvaultrg* am Standort *eastus* erstellt:

```azurecli 
az group create --name keyvaultrg --location eastus
```

## <a name="create-a-key-vault"></a>Erstellen eines Schlüsseltresors
Erstellen Sie einen Schlüsseltresor zum Speichern der SSH-Schlüssel für den Cluster, indem Sie den Befehl [az keyvault create](/cli/azure/keyvault) verwenden. Der Key Vault-Name muss global eindeutig sein.

Im folgenden Beispiel wird ein Schlüsseltresor namens *keyvault* in der Ressourcengruppe *keyvaultrg* erstellt:

```azurecli 
az keyvault create --resource-group keyvaultrg --name keyvault \
       --enabled-for-template-deployment true \
       --location eastus
```

## <a name="create-an-ssh-key"></a>Erstellen eines SSH-Schlüssels 
Ein SSH-Schlüssel ist erforderlich, um den Zugriff auf den OpenShift-Cluster zu schützen. Erstellen Sie mithilfe des Befehls `ssh-keygen` (Linux oder Mac OS) ein SSH-Schlüsselpaar:
 
 ```bash
ssh-keygen -f ~/.ssh/openshift_rsa -t rsa -N ''
```

> [!NOTE]
> Ihr SSH-Schlüsselpaar darf kein Kennwort/keine Passphrase enthalten.

Weitere Informationen zu SSH-Schlüsseln unter Windows finden Sie unter [Verwenden von SSH-Schlüsseln in Windows](/azure/virtual-machines/linux/ssh-from-windows). Exportieren Sie den privaten Schlüssel im OpenSSH-Format.

## <a name="store-the-ssh-private-key-in-azure-key-vault"></a>Speichern des privaten SSH-Schlüssels in Azure Key Vault
Die OpenShift-Bereitstellung verwendet den erstellten SSH-Schlüssel zum Sicherstellen des Zugriffs auf den OpenShift-Master. Damit die Bereitstellung den SSH-Schlüssel sicher abrufen kann, speichern Sie den Schlüssel im Key Vault. Verwenden Sie hierzu den folgenden Befehl:

```azurecli
az keyvault secret set --vault-name keyvault --name keysecret --file ~/.ssh/openshift_rsa
```

## <a name="create-a-service-principal"></a>Erstellen eines Dienstprinzipals 
OpenShift kommuniziert mit Azure unter Verwendung von Benutzername und Kennwort oder eines Dienstprinzipals. Ein Azure-Dienstprinzipal ist eine Sicherheitsidentität, die Sie mit Apps, Diensten und Automatisierungstools wie OpenShift verwenden können. Sie steuern und definieren die Berechtigungen hinsichtlich der Vorgänge, die der Dienstprinzipal in Azure ausführen können soll. Es wird empfohlen, die Berechtigungen des Dienstprinzipals auf bestimmte Ressourcengruppen und nicht das gesamte Abonnement festzulegen.

Erstellen Sie mit [az ad sp create-for-rbac](/cli/azure/ad/sp) einen Dienstprinzipal, und geben Sie die Anmeldeinformationen aus, die OpenShift benötigt.

Im folgenden Beispiel wird ein Dienstprinzipal erstellt und mit Berechtigungen vom Typ „Mitwirkender“ für eine Ressourcengruppe namens „openshiftrg“ ausgestattet.

Erstellen Sie zunächst die Ressourcengruppe „openshiftrg“:

```azurecli
az group create -l eastus -n openshiftrg
```

Erstellen Sie den Dienstprinzipal:

```azurecli
scope=`az group show --name openshiftrg --query id`
az ad sp create-for-rbac --name openshiftsp \
      --role Contributor --password {Strong Password} \
      --scopes $scope
```
Falls Sie Windows verwenden, führen Sie ```az group show --name openshiftrg --query id``` aus, und verwenden Sie die Ausgabe anstelle von „$scope“.

Notieren Sie die „appId“-Eigenschaft, die der Befehl zurückgibt:
```json
{
  "appId": "11111111-abcd-1234-efgh-111111111111",
  "displayName": "openshiftsp",
  "name": "http://openshiftsp",
  "password": {Strong Password},
  "tenant": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"
}
```
 > [!WARNING] 
 > Achten Sie darauf, ein sicheres Kennwort zu erstellen. Befolgen Sie die Anleitung unter [Kennwortrichtlinien und -einschränkungen in Azure Active Directory](/azure/active-directory/active-directory-passwords-policy).

Weitere Informationen über Dienstprinzipale finden Sie unter [Erstellen eines Azure-Dienstprinzipals mit Azure CLI](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest).

## <a name="prerequisites-applicable-only-to-resource-manager-template"></a>Voraussetzungen, die nur die Resource Manager-Vorlage betreffen

Geheimnisse müssen für den privaten SSH-Schlüssel (**sshPrivateKey**), das Azure AD-Clientgeheimnis (**aadClientSecret**), das OpenShift-Administratorkennwort (**openshiftPassword**) und das Kennwort oder den Aktivierungsschlüssel für den Red Hat Subscription Manager (**rhsmPasswordOrActivationKey**) erstellt werden.  Wenn darüber hinaus benutzerdefinierte SSL-Zertifikate verwendet werden, müssen sechs zusätzliche Geheimnisse erstellt werden: **routingcafile**, **routingcertfile**, **routingkeyfile**, **mastercafile**, **mastercertfile** und **masterkeyfile**.  Diese Parameter werden weiter unten eingehend erläutert.

Die Vorlage verweist auf die spezifischen Geheimnisnamen, daher **müssen** Sie die oben aufgeführten fett formatierten Namen (unter Beachtung der Groß-/Kleinschreibung) verwenden.

### <a name="custom-certificates"></a>Benutzerdefinierte Zertifikate

Standardmäßig stellt die Vorlage einen OpenShift-Cluster unter Verwendung selbstsignierter Zertifikate für die OpenShift-Webkonsole und die Routingdomäne bereit. Wenn Sie benutzerdefinierte SSL-Zertifikate verwenden möchten, legen Sie „routingCertType“ auf „custom“ und „masterCertType“ auf „custom“ fest.  Sie benötigen die CA-, CERT- und Schlüsseldateien im PEM-Format für die Zertifikate.  Es ist möglich, benutzerdefinierte Zertifikate für einen, jedoch nicht den anderen Bereich zu verwenden.

Sie müssen diese Dateien in Key Vault-Geheimnissen speichern.  Verwenden Sie denselben Schlüsseltresor, den Sie für den privaten Schlüssel verwendet haben.  Anstatt sechs zusätzliche Eingaben für die Geheimnisnamen zu erfordern, ist die Vorlage auf spezifische Geheimnisnamen für die SSL-Zertifikatsdateien fest codiert.  Speichern Sie die Zertifikatdaten mit den Informationen aus der folgenden Tabelle.

| Geheimnisname      | Zertifikatsdatei   |
|------------------|--------------------|
| mastercafile     | Master-CA-Datei     |
| mastercertfile   | Maste-CERT-Datei   |
| masterkeyfile    | Master-Schlüsseldatei    |
| routingcafile    | Routing-CA-Datei    |
| routingcertfile  | Routing-CERT-Datei  |
| routingkeyfile   | Routing-Schlüsseldatei   |

Erstellen Sie die Geheimnisse mithilfe der Azure-Befehlszeilenschnittstelle. Unten ist ein Beispiel aufgeführt.

```bash
az keyvault secret set --vault-name KeyVaultName -n mastercafile --file ~/certificates/masterca.pem
```

## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel wurden folgende Themen behandelt:
> [!div class="checklist"]
> * Erstellen Sie einen Schlüsseltresor (Key Vault) zum Verwalten von SSH-Schlüsseln für den OpenShift-Cluster.
> * Erstellen Sie einen Dienstprinzipal für Azure Cloud Solution Provider.

Als Nächstes können Sie einen OpenShift-Cluster bereitstellen:

- [Bereitstellen von OpenShift Container Platform](./openshift-container-platform.md)
- [Bereitstellen eines selbstverwalteten OpenShift Container Platform-Marketplace-Angebots](./openshift-marketplace-self-managed.md)