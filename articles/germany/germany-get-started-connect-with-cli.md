---
title: Herstellen einer Verbindung mit Azure Deutschland über die Azure CLI | Microsoft-Dokumentation
description: Informationen zum Verwalten Ihres Abonnements in Azure Deutschland über die Azure CLI
services: germany
cloud: na
documentationcenter: na
author: gitralf
manager: rainerst
ms.assetid: na
ms.service: germany
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/13/2017
ms.author: ralfwi
ms.openlocfilehash: d2e94a7bd154195e7956df1a28407b0933e0e526
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60780852"
---
# <a name="connect-to-azure-germany-by-using-azure-cli"></a>Herstellen einer Verbindung mit Azure Deutschland über die Azure CLI
Um die Azure-Befehlszeilenschnittstelle (Azure CLI) zu verwenden, müssen Sie eine Verbindung mit Azure Deutschland anstelle der globalen Azure-Umgebung herstellen. Sie können darüber die Azure-Befehlszeilenschnittstelle z. B. zum Verwalten eines umfangreichen Abonnements über Skripts oder für den Zugriff auf Features verwenden, die derzeit im Azure-Portal nicht verfügbar sind. Wenn Sie die Azure-Befehlszeilenschnittstelle bereits in der globalen Azure-Umgebung verwendet haben, ist das Vorgehen größtenteils identisch.  

## <a name="azure-cli"></a>Azure-Befehlszeilenschnittstelle
Es gibt mehrere Möglichkeiten zum [Installieren der Azure-Befehlszeilenschnittstelle](https://docs.microsoft.com/cli/azure/install-az-cli2).  

Zum Herstellen einer Verbindung mit Azure Deutschland müssen Sie die richtige Cloudumgebung festlegen:

```
az cloud set --name AzureGermanCloud
```

Nach dem Festlegen der Cloud können Sie sich anmelden:

```
az login --username your-user-name@your-tenant.onmicrosoft.de
```

Um zu überprüfen, ob die Cloudumgebung ordnungsgemäß auf AzureGermanCloud festgelegt ist, führen Sie einen der folgenden Befehle aus und überprüfen dann, ob das Flag `isActive` für das Element AzureGermanCloud auf `true` festgelegt ist:

```
az cloud list
```

```
az cloud list --output table
```

## <a name="azure-classic-cli"></a>Klassische Azure-Befehlszeilenschnittstelle
Es gibt mehrere Möglichkeiten zum [Installieren der klassischen Azure-Befehlszeilenschnittstelle](../xplat-cli-install.md). Wenn Node.js bereits installiert ist, ist die Installation des npm-Pakets die einfachste Möglichkeit.

Um CLI aus einem npm-Paket zu installieren, müssen Sie die aktuellen Versionen von [Node.js und npm](https://nodejs.org/en/download/package-manager/) herunterladen und installieren. Führen Sie anschließend zum Installieren des Azure CLI-Pakets **npm install** aus:

```bash
npm install -g azure-cli
```

Bei Linux-Distributionen müssen Sie möglicherweise **sudo** wie folgt verwenden, um den Befehl **npm** erfolgreich ausführen zu können:

```bash
sudo npm install -g azure-cli
```

> [!NOTE]
> Falls Sie Node.js und npm für Ihre Linux-Distribution oder Ihr Betriebssystem installieren oder aktualisieren müssen, wird die Installation der aktuellsten LTS-Version von Node.js (4.x) empfohlen. Wenn Sie eine ältere Version verwenden, kommt es möglicherweise zu Fehlern bei der Installation.


Melden Sie sich nach der Installation von Azure CLI bei Azure Deutschland an:

```
azure login --username your-user-name@your-tenant.onmicrosoft.de  --environment AzureGermanCloud
```

Nachdem Sie angemeldet sind, können Sie Befehle der Azure-Befehlszeilenschnittstelle wie gewohnt ausführen:

```
azure webapp list my-resource-group
```

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zum Herstellen der Verbindung mit Azure Deutschland finden Sie in den folgenden Ressourcen:

* [Herstellen einer Verbindung mit Azure Deutschland über PowerShell](./germany-get-started-connect-with-ps.md)
* [Herstellen einer Verbindung mit Azure Deutschland über Visual Studio](./germany-get-started-connect-with-vs.md)
* [Herstellen einer Verbindung mit Azure Deutschland über das Azure-Portal](./germany-get-started-connect-with-portal.md)




