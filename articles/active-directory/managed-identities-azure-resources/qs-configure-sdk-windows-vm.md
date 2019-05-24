---
title: Verwenden eines Azure SDK zum Konfigurieren eines virtuellen Computers mit verwalteten Identitäten für Azure-Ressourcen
description: Ausführliche Anweisungen zum Konfigurieren und Verwenden von verwalteten Identitäten für Azure-Ressourcen auf einer Azure-VM über ein Azure SDK
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/28/2017
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 29446f6f0a86a00cc92fe46211caddd22aaa4859
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66112560"
---
# <a name="configure-a-vm-with-managed-identities-for-azure-resources-using-an-azure-sdk"></a>Konfigurieren eines virtuellen Computers mit verwalteten Identitäten für Azure-Ressourcen mithilfe eines Azure SDK

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Verwaltete Dienstidentitäten für Azure-Ressourcen stellen für Azure-Dienste eine automatisch verwaltete Identität in Azure Active Directory (AD) bereit. Sie können diese Identität für die Authentifizierung bei jedem Dienst verwenden, der die Azure AD-Authentifizierung unterstützt. Hierfür müssen keine Anmeldeinformationen im Code enthalten sein. 

In diesem Artikel erfahren Sie, wie Sie verwaltete Identitäten für Azure-Ressourcen für einen virtuellen Azure-Computer mit einem Azure SDK aktivieren und entfernen.

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

## <a name="azure-sdks-with-managed-identities-for-azure-resources-support"></a>Azure SDKs, die verwaltete Identitäten für Azure-Ressourcen unterstützen 

Azure unterstützt mit einer Reihe von [Azure SDKs](https://azure.microsoft.com/downloads) mehrere Programmierplattformen. Mehrere davon wurden aktualisiert, um verwaltete Identitäten für Azure-Ressourcen zu unterstützen, und enthalten entsprechende Beispiele, um die Verwendung zu demonstrieren. Diese Liste wird jeweils aktualisiert, wenn weitere Unterstützung hinzugefügt wird:

| SDK | Beispiel |
| --- | ------ | 
| .NET   | [Verwalten von Ressourcen auf einem virtuellen Computer mit aktivierten verwalteten Identitäten für Azure-Ressourcen](https://azure.microsoft.com/resources/samples/aad-dotnet-manage-resources-from-vm-with-msi/) |
| Java   | [Verwalten von Speicher auf einem virtuellen Computer mit aktivierten verwalteten Identitäten für Azure-Ressourcen](https://azure.microsoft.com/resources/samples/compute-java-manage-resources-from-vm-with-msi-in-aad-group/)|
| Node.js| [Erstellen eines virtuellen Computers mit aktivierter vom System zugewiesener verwalteter Identität](https://azure.microsoft.com/resources/samples/compute-node-msi-vm/) |
| Python | [Erstellen eines virtuellen Computers mit aktivierter vom System zugewiesener verwalteter Identität](https://azure.microsoft.com/resources/samples/compute-python-msi-vm/) |
| Ruby   | [Erstellen eines virtuellen Azure-Computers mit aktivierter vom System zugewiesener Identität](https://azure.microsoft.com/resources/samples/compute-ruby-msi-vm/) |

## <a name="next-steps"></a>Nächste Schritte

- Informationen dazu, wie Sie auch das Azure-Portal, PowerShell, die Befehlszeilenschnittstelle und Ressourcenvorlagen verwenden können, finden Sie in den verwandten Artikeln unter **Konfigurieren der Identität für einen virtuellen Azure-Computer**.
