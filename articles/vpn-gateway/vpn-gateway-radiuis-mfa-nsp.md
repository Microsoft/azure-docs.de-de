---
title: Sichere RADIUS-Authentifizierung für das Azure-VPN-Gateway mit NPS-Server für Multi-Factor Authentication | Microsoft-Dokumentation
description: Beschreibt die Integration der RADIUS-Authentifizierung für das Azure-Gateway in den NPS-Server für Multi-Factor Authentication.
services: vpn-gateway
documentationcenter: na
author: ahmadnyasin
manager: willchen
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2018
ms.author: genli
ms.openlocfilehash: 4e11b1bc16f874f892288f9677a71023f483de7c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60458079"
---
# <a name="integrate-azure-vpn-gateway-radius-authentication-with-nps-server-for-multi-factor-authentication"></a>Integrieren der RADIUS-Authentifizierung für das Azure-VPN-Gateway in den NPS-Server für Multi-Factor Authentication 

In diesem Artikel wird beschrieben, wie Sie den Netzwerkrichtlinienserver (Network Policy Server, NPS) in die RADIUS-Authentifizierung für das Azure-VPN-Gateway integrieren, um Multi-Factor Authentication (MFA) für Point-to-Site-VPN-Verbindungen bereitzustellen. 

## <a name="prerequisite"></a>Voraussetzung

Zum Aktivieren von MFA müssen die Benutzer in Azure Active Directory (Azure AD) enthalten sein. Der Azure AD-Dienst muss über die lokale Umgebung oder die Cloudumgebung synchronisiert werden. Darüber hinaus muss der Benutzer bereits die automatische Registrierung für MFA abgeschlossen haben.  Weitere Informationen finden Sie unter [Einrichten meines Kontos für die zweistufige Überprüfung](../active-directory/user-help/multi-factor-authentication-end-user-first-time.md)

## <a name="detailed-steps"></a>Ausführliche Schritte

### <a name="step-1-create-a-virtual-network-gateway"></a>Schritt 1: Erstellen eines Gateways für das virtuelle Netzwerk

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com)an.
2. Wählen Sie im virtuellen Netzwerk, in dem das Gateway des virtuellen Netzwerks gehostet wird, **Subnetze** und dann **Gatewaysubnetz** aus, um ein Subnetz zu erstellen. 

    ![Abbildung zum Hinzufügen eines Gatewaysubnetzes](./media/vpn-gateway-radiuis-mfa-nsp/gateway-subnet.png)
3. Erstellen Sie ein Gateway des virtuellen Netzwerks, indem Sie die folgenden Einstellungen angeben:

    - **Gatewaytyp:** Wählen Sie **VPN** aus.
    - **VPN-Typ:** Wählen Sie **Routenbasiert** aus.
    - **SKU:** Wählen Sie einen SKU-Typ entsprechend Ihren Anforderungen aus.
    - **Virtuelles Netzwerk:** Wählen Sie das virtuelle Netzwerk aus, in dem Sie das Gatewaysubnetz erstellt haben.

        ![Abbildung zu den Einstellungen für das Gateway des virtuellen Netzwerks](./media/vpn-gateway-radiuis-mfa-nsp/create-vpn-gateway.png)


 
### <a name="step-2-configure-the-nps-for-azure-mfa"></a>Schritt 2: Konfigurieren des NPS für Azure MFA

1. [Installieren Sie auf dem NPS-Server die NPS-Erweiterung für Azure MFA](../active-directory/authentication/howto-mfa-nps-extension.md#install-the-nps-extension).
2. Öffnen Sie die NPS-Konsole, klicken Sie mit der rechten Maustaste auf **RADIUS-Clients**, und wählen Sie dann **Neu** aus. Erstellen Sie den RADIUS-Client, indem Sie die folgenden Einstellungen angeben:

    - **Anzeigename:** Geben Sie einen beliebigen Namen ein.
    - **Adresse (IP oder DNS):** Geben Sie das Gatewaysubnetz ein, das Sie in Schritt 1 erstellt haben.
    - **Gemeinsamer geheimer Schlüssel**: Geben Sie einen beliebigen geheimen Schlüssel ein, und merken Sie sich den Schlüssel zur späteren Verwendung.

      ![Abbildung zu den RADIUS-Clienteinstellungen](./media/vpn-gateway-radiuis-mfa-nsp/create-radius-client1.png)

 
3.  Legen Sie auf der Registerkarte **Erweitert** den Herstellernamen auf **RADIUS-Standard** fest, und stellen Sie sicher, dass das Kontrollkästchen **Zusätzliche Optionen** nicht aktiviert ist.

    ![Abbildung zu den erweiterten RADIUS-Clienteinstellungen](./media/vpn-gateway-radiuis-mfa-nsp/create-radius-client2.png)

4. Wechseln Sie zu **Richtlinien** > **Netzwerkrichtlinien**, doppelklicken Sie auf die Richtlinie **Verbindungen mit Microsoft-Routing- und Remotezugriffsserver**, wählen Sie **Zugriff gewähren** aus, und klicken Sie dann auf **OK**.

### <a name="step-3-configure-the-virtual-network-gateway"></a>Schritt 3: Konfigurieren des Gateways des virtuellen Netzwerks

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Öffnen Sie das Gateway des virtuellen Netzwerks, das Sie erstellt haben. Stellen Sie sicher, dass der Gatewaytyp auf **VPN** festgelegt ist und als VPN-Typ **Routenbasiert** ausgewählt ist.
3. Klicken Sie auf **Punkt-zu-Standort-Konfiguration** > **Jetzt konfigurieren**, und geben Sie dann die folgenden Einstellungen an:

    - **Adresspool:** Geben Sie das Gatewaysubnetz ein, das Sie in Schritt 1 erstellt haben.
    - **Authentifizierungstyp:** Wählen Sie **RADIUS-Authentifizierung** aus.
    - **IP-Adresse des Servers:** Geben Sie die IP-Adresse des NPS-Servers ein.

      ![Abbildung zu den Punkt-zu-Standort-Einstellungen](./media/vpn-gateway-radiuis-mfa-nsp/configure-p2s.png)

## <a name="next-steps"></a>Nächste Schritte

- [Azure Multi-Factor Authentication](../active-directory/authentication/multi-factor-authentication.md)
- [Integrieren Ihrer vorhandenen NPS-Infrastruktur in Azure Multi-Factor Authentication – Public Preview](../active-directory/authentication/howto-mfa-nps-extension.md)
