---
title: Verwenden von Diensttags
titleSuffix: Azure SignalR Service
description: Verwenden von Diensttags für das Zulassen von ausgehendem Datenverkehr zu Ihrer Azure SignalR Service-Instanz
services: signalr
author: ArchangelSDY
ms.service: signalr
ms.topic: article
ms.date: 05/06/2020
ms.author: dayshen
ms.openlocfilehash: aaa97c2cb062f30b1260ec7f80f85a3caccf932f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92152312"
---
# <a name="use-service-tags-for-azure-signalr-service"></a>Verwenden von Diensttags für Azure SignalR Service

Sie können [Diensttags](../virtual-network/network-security-groups-overview.md#service-tags) für Azure SignalR Service verwenden, wenn Sie [Netzwerksicherheitsgruppen](../virtual-network/network-security-groups-overview.md#network-security-groups) konfigurieren. So können Sie Sicherheitsregeln für ausgehenden Datenverkehr zu Azure SignalR Service-Endpunkten definieren, ohne dass dafür IP-Adressen hartcodiert werden müssen.

Azure SignalR Service verwaltet diese Diensttags. Sie können kein eigenes Diensttag erstellen oder ein vorhandenes Tag ändern. Microsoft verwaltet diese Adresspräfixe, die dem Diensttag entsprechen, und aktualisiert das Diensttag automatisch, wenn sich die Adressen ändern.

## <a name="use-service-tag-on-portal"></a>Verwenden von Diensttags im Portal

Sie können ausgehenden Datenverkehr zu Azure SignalR Service zulassen, indem Sie eine neue Sicherheitsregel für ausgehenden Datenverkehr im Netzwerk festlegen:

1. Rufen Sie die Netzwerksicherheitsgruppe auf.

1. Klicken Sie auf das Einstellungsmenü namens **Ausgangssicherheitsregeln**.

1. Klicken Sie oben auf die Schaltfläche **+ Hinzufügen**.

1. Klicken Sie unter **Ziel** auf die Option **Diensttag**.

1. Klicken Sie unter **Zieldiensttag** auf **AzureSignalR**.

1. Geben Sie für **Zielportbereiche** den Port **443** ein.

    ![Erstellen einer Ausgangssicherheitsregel](media/howto-service-tags/portal-add-outbound-security-rule.png)

1. Nehmen Sie an den anderen Feldern entsprechend Ihrer Anforderungen Anpassungen vor.

1. Klicken Sie auf **Hinzufügen**.


## <a name="next-steps"></a>Nächste Schritte

- [Netzwerksicherheitsgruppen: Diensttags](../virtual-network/network-security-groups-overview.md#security-rules)