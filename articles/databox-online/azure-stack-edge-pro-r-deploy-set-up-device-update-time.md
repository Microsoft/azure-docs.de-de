---
title: Tutorial zum Einrichten der Zeit-, Geräte- und Updateeinstellungen für ein Azure Stack Edge Pro R-Gerät im Azure-Portal
description: In diesem Tutorial zur Bereitstellung von Azure Stack Edge Pro R erfahren Sie, wie Sie die Geräte-, Update- und Zeiteinstellungen für Ihr physisches Gerät einrichten.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 10/18/2020
ms.author: alkohli
ms.openlocfilehash: 94abfd704d000b907158b2559a268718046b6661
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106059614"
---
# <a name="tutorial-configure-the-device-settings-for-azure-stack-edge-pro-r"></a>Tutorial: Konfigurieren der Geräteeinstellungen für Azure Stack Edge Pro R

In diesem Tutorial erfahren Sie, wie Sie gerätebezogene Einstellungen für Ihr Azure Stack Edge Pro R-Gerät konfigurieren. Sie können auf der lokalen Webbenutzeroberfläche den Gerätenamen, Updateserver und Zeitserver einrichten.

Dieser Geräteeinstellungsvorgang kann ca. 5 bis 7 Minuten dauern.

In diesem Tutorial lernen Sie Folgendes kennen:

> [!div class="checklist"]
>
> * Voraussetzungen
> * Konfigurieren von Geräteeinstellungen
> * Konfigurieren des Updates 
> * Konfigurieren der Uhrzeit

## <a name="prerequisites"></a>Voraussetzungen

Vergewissern Sie sich, dass folgende Voraussetzungen erfüllt sind, bevor Sie gerätebezogene Einstellungen für Ihr Azure Stack Edge Pro R-Gerät konfigurieren:

* Für Ihr physisches Gerät:

    - Sie haben das physische Gerät gemäß der Anleitung unter [Tutorial: Installieren von Azure Stack Edge Pro R](azure-stack-edge-pro-r-deploy-install.md) installiert.
    - Sie haben das Netzwerk konfiguriert und das Computenetzwerk auf Ihrem Gerät aktiviert und konfiguriert, wie unter [Tutorial: Konfigurieren des Netzwerks für Azure Stack Edge Pro R](azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy.md) beschrieben.


## <a name="configure-device-settings"></a>Konfigurieren von Geräteeinstellungen

Führen Sie zum Konfigurieren von Geräteeinstellungen die folgenden Schritte aus:

1. Führen Sie auf der Seite **Gerät** die folgenden Schritte aus:

    1. Geben Sie einen Anzeigenamen für Ihr Gerät ein. Der Anzeigename muss aus 1 bis 13 Zeichen bestehen und kann Buchstaben, Zahlen und Bindestriche enthalten.

    2. Geben Sie eine **DNS-Domäne** für Ihr Gerät an. Diese Domäne wird zum Einrichten des Geräts als Dateiserver verwendet.

    3. Klicken Sie auf **Übernehmen**, um die konfigurierten Geräteeinstellungen zu überprüfen und zu übernehmen.

        ![Seite „Gerät“ der lokalen Webbenutzeroberfläche 1](./media/azure-stack-edge-pro-r-deploy-set-up-device-update-time/device-2.png)

        Wenn Sie den Gerätenamen und die DNS-Domäne geändert haben, funktionieren die automatisch generierten selbstsignierten Zertifikate auf dem Gerät nicht. Bei der Konfiguration von Zertifikaten müssen Sie eine der folgenden Optionen wählen: 
        
        - Generieren Sie die Gerätezertifikate, und laden Sie sie herunter. 
        - Stellen Sie Ihre eigenen Zertifikate für das Gerät einschließlich Signaturkette bereit.
    

        ![Seite „Gerät“ der lokalen Webbenutzeroberfläche 2](./media/azure-stack-edge-pro-r-deploy-set-up-device-update-time/device-3.png)

    4. Wenn der Gerätename und die DNS-Domäne geändert werden, wird der SMB-Endpunkt erstellt.  

    5. Wählen Sie **Weiter: Updateserver** aus, nachdem die Einstellungen übernommen wurden.

        ![Seite „Gerät“ der lokalen Webbenutzeroberfläche 3](./media/azure-stack-edge-pro-r-deploy-set-up-device-update-time/device-4.png)

## <a name="configure-update"></a>Konfigurieren des Updates

1. Auf der Seite **Update** können Sie nun den Speicherort konfigurieren, von dem die Updates für Ihr Gerät heruntergeladen werden sollen.  

    - Sie können die Updates direkt über den **Microsoft Update-Server** beziehen.

        ![Seite „Updateserver“ auf der lokalen Webbenutzeroberfläche](./media/azure-stack-edge-pro-r-deploy-set-up-device-update-time/update-2.png)

        Sie können Updates auch über **Windows Server Update Services** (WSUS) bereitstellen. Geben Sie den Pfad zum WSUS-Server an.
        
        ![Seite „Updateserver“ auf der lokalen Webbenutzeroberfläche 2](./media/azure-stack-edge-pro-r-deploy-set-up-device-update-time/update-3.png)

        > [!NOTE] 
        > Wenn ein separater Windows Update-Server konfiguriert ist und Sie eine Verbindung über *https* (statt *http*) wählen, werden für die Verbindung mit dem Updateserver Signaturkettenzertifikate benötigt. Informationen zum Erstellen und Hochladen von Zertifikaten finden Sie unter [Verwalten von Zertifikaten](azure-stack-edge-gpu-manage-certificates.md).         
        > Wenn Sie in einem getrennten Modus arbeiten, aktivieren Sie die WSUS-Option. Ein Beispiel hierfür wäre etwa das Tiering Ihres Azure Stack Edge-Geräts mit Modular Data Center. Da das Gerät während der Aktivierung nach Updates sucht, ist die Aktivierung ohne eingerichteten Server nicht erfolgreich. 


2. Wählen Sie **Übernehmen**.
3. Wählen Sie nach der Konfiguration des Updateservers **Weiter: Zeit** aus.
    

## <a name="configure-time"></a>Konfigurieren der Uhrzeit

Führen Sie die folgenden Schritte aus, um die Zeiteinstellungen auf Ihrem Gerät zu konfigurieren. 

> [!IMPORTANT]
> Obwohl die Zeiteinstellungen optional sind, empfehlen wir dringend, dass Sie für Ihr Gerät einen primären und sekundären NTP-Server im lokalen Netzwerk konfigurieren. Wenn der lokale Server nicht verfügbar ist, können öffentliche NTP-Server konfiguriert werden.

NTP-Server sind für die Zeitsynchronisierung erforderlich, damit Ihr Gerät bei den Clouddienstanbietern authentifiziert werden kann.

1. Auf der Seite **Zeit** können Sie die Zeitzone sowie den primären und sekundären NTP-Server für Ihr Gerät auswählen.  
    
    1. Wählen Sie aus der Dropdownliste die **Zeitzone** aus, die dem geografischen Standort entspricht, an dem das Gerät bereitgestellt wird.
        Die Standardzeitzone für Ihr Gerät ist „PST“. Ihr Gerät verwendet diese Zeitzone für alle geplanten Vorgänge.

    2. Geben Sie im Feld **Primärer NTP-Server** den primären Server für das Gerät ein, oder übernehmen Sie den Standardwert „time.windows.com“.  
        Stellen Sie sicher, dass Ihr Netzwerk NTP-Datenverkehr vom Rechenzentrum ins Internet zulässt.

    3. Geben Sie optional im Feld **Sekundärer NTP-Server** einen sekundären Server für Ihr Gerät ein.

    4. Klicken Sie auf **Übernehmen**, um die konfigurierten Uhrzeiteinstellungen zu überprüfen und anzuwenden.

        ![Seite „Zeit“ auf der lokalen Webbenutzeroberfläche](./media/azure-stack-edge-pro-r-deploy-set-up-device-update-time/time-2.png)

2. Wählen Sie **Weiter: Zertifikate** aus.


## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial lernen Sie Folgendes kennen:

> [!div class="checklist"]
>
> * Voraussetzungen
> * Konfigurieren von Geräteeinstellungen
> * Konfigurieren des Updates 
> * Konfigurieren der Uhrzeit

Informationen zum Konfigurieren von Zertifikaten für Ihr Azure Stack Edge Pro R-Gerät finden Sie hier:

> [!div class="nextstepaction"]
> [Konfigurieren von Zertifikaten](./azure-stack-edge-pro-r-deploy-configure-certificates-vpn-encryption.md)
