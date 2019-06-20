---
title: Einrichten des WinRM-Zugriffs für eine Azure-VM | Microsoft-Dokumentation
description: Richten Sie den WinRM-Zugriff für eine Azure-VM ein, die mit dem Ressourcen-Manager-Bereitstellungsmodell erstellt wurde.
services: virtual-machines-windows
documentationcenter: ''
author: singhkays
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 9718e85b-d360-4621-90b8-0b0b84a21208
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 06/16/2016
ms.author: kasing
ms.openlocfilehash: 7e006b8bc05d00de3e5fba20985e30fd5e96c1df
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64708682"
---
# <a name="setting-up-winrm-access-for-virtual-machines-in-azure-resource-manager"></a>Einrichten des Zugriffs auf WinRM für virtuelle Computer in Azure Resource Manager

Sie müssen folgende Schritte ausführen, um einen virtuellen Computer mit WinRM-Verbindung einzurichten:

1. Erstellen eines Schlüsseltresors
2. Erstellen eines selbstsignierten Zertifikats
3. Hochladen eines selbstsignierten Zertifikats in den Schlüsseltresor
4. Abrufen der URL für das selbstsignierte Zertifikat im Schlüsseltresor
5. Verweisen auf die URL für selbstsignierte Zertifikate beim Erstellen eines virtuellen Computers

[!INCLUDE [updated-for-az.md](../../../includes/updated-for-az.md)]

## <a name="step-1-create-a-key-vault"></a>Schritt 1: Erstellen eines Schlüsseltresors
Sie können den folgenden Befehl verwenden, um den Schlüsseltresor zu erstellen.

```
New-AzKeyVault -VaultName "<vault-name>" -ResourceGroupName "<rg-name>" -Location "<vault-location>" -EnabledForDeployment -EnabledForTemplateDeployment
```

## <a name="step-2-create-a-self-signed-certificate"></a>Schritt 2: Erstellen eines selbstsignierten Zertifikats
Sie können mit diesem PowerShell-Skript ein selbstsigniertes Zertifikat erstellen.

```
$certificateName = "somename"

$thumbprint = (New-SelfSignedCertificate -DnsName $certificateName -CertStoreLocation Cert:\CurrentUser\My -KeySpec KeyExchange).Thumbprint

$cert = (Get-ChildItem -Path cert:\CurrentUser\My\$thumbprint)

$password = Read-Host -Prompt "Please enter the certificate password." -AsSecureString

Export-PfxCertificate -Cert $cert -FilePath ".\$certificateName.pfx" -Password $password
```

## <a name="step-3-upload-your-self-signed-certificate-to-the-key-vault"></a>Schritt 3: Hochladen eines selbstsignierten Zertifikats in den Schlüsseltresor
Vor dem Hochladen des Zertifikats in den in Schritt 1 erstellten Schlüsseltresor muss es in ein Format konvertiert werden, das für den Microsoft.Compute-Ressourcenanbieter verständlich ist. Dies können Sie mit dem unten stehenden PowerShell-Skript tun.

```
$fileName = "<Path to the .pfx file>"
$fileContentBytes = Get-Content $fileName -Encoding Byte
$fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)

$jsonObject = @"
{
  "data": "$filecontentencoded",
  "dataType" :"pfx",
  "password": "<password>"
}
"@

$jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
$jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

$secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText –Force
Set-AzureKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>" -SecretValue $secret
```

## <a name="step-4-get-the-url-for-your-self-signed-certificate-in-the-key-vault"></a>Schritt 4: Abrufen der URL für das selbstsignierte Zertifikat im Schlüsseltresor
Der Microsoft.Compute-Ressourcenanbieter benötigt bei der Bereitstellung des virtuellen Computers eine URL für den geheimen Schlüssel im Schlüsseltresor. Dadurch kann der Microsoft.Compute-Ressourcenanbieter den geheimen Schlüssel herunterladen und das entsprechende Zertifikat auf dem virtuellen Computer erstellen.

> [!NOTE]
> Die URL des geheimen Schlüssels muss auch die Version enthalten. Ein Beispiel für eine URL lautet: \//contosovault.vault.azure.net:443/secrets/contososecret/01h9db0df2cd4300a20ence585a6s7ve

#### <a name="templates"></a>Vorlagen
Sie erhalten den Link zur URL in der Vorlage mit folgendem Code:

    "certificateUrl": "[reference(resourceId(resourceGroup().name, 'Microsoft.KeyVault/vaults/secrets', '<vault-name>', '<secret-name>'), '2015-06-01').secretUriWithVersion]"

#### <a name="powershell"></a>PowerShell
Sie erhalten diese URL durch den folgenden PowerShell-Befehl:

    $secretURL = (Get-AzureKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id

## <a name="step-5-reference-your-self-signed-certificates-url-while-creating-a-vm"></a>Schritt 5: Verweisen auf die URL für selbstsignierte Zertifikate beim Erstellen eines virtuellen Computers
#### <a name="azure-resource-manager-templates"></a>Azure Resource Manager-Vorlagen
Beim Erstellen eines virtuellen Computers mittels Vorlagen wird im Abschnitt mit dem geheimen Schlüssel und im Abschnitt „winRM“ wie folgt auf das Zertifikat verwiesen:

    "osProfile": {
          ...
          "secrets": [
            {
              "sourceVault": {
                "id": "<resource id of the Key Vault containing the secret>"
              },
              "vaultCertificates": [
                {
                  "certificateUrl": "<URL for the certificate you got in Step 4>",
                  "certificateStore": "<Name of the certificate store on the VM>"
                }
              ]
            }
          ],
          "windowsConfiguration": {
            ...
            "winRM": {
              "listeners": [
                {
                  "protocol": "http"
                },
                {
                  "protocol": "https",
                  "certificateUrl": "<URL for the certificate you got in Step 4>"
                }
              ]
            },
            ...
          }
        },

Eine Vorlage zum oben genannten Beispiel finden Sie unter [201-vm-winrm-keyvault-windows](https://azure.microsoft.com/documentation/templates/201-vm-winrm-keyvault-windows)

Den Quellcode für diese Vorlage finden Sie auf [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-winrm-keyvault-windows)

#### <a name="powershell"></a>PowerShell
    $vm = New-AzVMConfig -VMName "<VM name>" -VMSize "<VM Size>"
    $credential = Get-Credential
    $secretURL = (Get-AzureKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id
    $vm = Set-AzVMOperatingSystem -VM $vm -Windows -ComputerName "<Computer Name>" -Credential $credential -WinRMHttp -WinRMHttps -WinRMCertificateUrl $secretURL
    $sourceVaultId = (Get-AzKeyVault -ResourceGroupName "<Resource Group name>" -VaultName "<Vault Name>").ResourceId
    $CertificateStore = "My"
    $vm = Add-AzVMSecret -VM $vm -SourceVaultId $sourceVaultId -CertificateStore $CertificateStore -CertificateUrl $secretURL

## <a name="step-6-connecting-to-the-vm"></a>Schritt 6: Herstellen einer Verbindung mit dem virtuellen Computer
Bevor Sie eine Verbindung mit dem virtuellen Computer herstellen können, müssen Sie sicherstellen, dass Ihr Computer für die WinRM-Remoteverwaltung konfiguriert ist. Starten Sie PowerShell als Administrator, und führen Sie den folgenden Befehl aus, um zu überprüfen, ob das Setup abgeschlossen ist.

    Enable-PSRemoting -Force

> [!NOTE]
> Wenn die oben genannte Methode nicht funktioniert, müssen Sie sicherstellen, dass der WinRM-Dienst ausgeführt wird. Verwenden Sie dazu `Get-Service WinRM`
> 
> 

Wenn das Setup abgeschlossen ist, können Sie mit dem folgenden Befehl eine Verbindung mit dem virtuellen Computer erstellen:

    Enter-PSSession -ConnectionUri https://<public-ip-dns-of-the-vm>:5986 -Credential $cred -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck -SkipRevocationCheck) -Authentication Negotiate
