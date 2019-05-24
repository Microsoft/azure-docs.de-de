---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: a5c62c67cd401c043352b06e6e6070a7fc0f1296
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66118332"
---
#### <a name="to-connect-through-the-serial-console"></a>So stellen Sie über die serielle Konsole eine Verbindung her
1. Schließen Sie das serielle Kabel (direkt oder über einen seriellen USB-Adapter) an das Gerät an.
2. Öffnen Sie die **Systemsteuerung**, und öffnen Sie dann den **Geräte-Manager**.
3. Identifizieren Sie den COM-Anschluss, wie in der folgenden Abbildung dargestellt.
   
     ![Herstellen einer Verbindung über die serielle Konsole](./media/storsimple-use-putty/HCS_ConnectingDeviceS-include.png)
4. Starten Sie PuTTY. 
5. Ändern Sie im rechten Bereich den **Verbindungstyp** in **Seriell**.
6. Geben Sie im rechten Bereich den entsprechenden COM-Anschluss ein. Stellen Sie sicher, dass die Parameter für die serielle Konfiguration wie folgt festgelegt sind:
   
   * Geschwindigkeit: 115.200
   * Datenbits: 8
   * Stoppbits: 1
   * Parität: Keine
   * Flusssteuerung: Keine
     
     Diese Einstellungen werden in der folgenden Abbildung gezeigt.
     
     ![PuTTY-Einstellungen](./media/storsimple-use-putty/HCS_PuttyConfig-include.png) 
     
     > [!NOTE]
     > Wenn die Standardeinstellungen für die Flusssteuerung nicht funktionieren, versuchen Sie, die Flusssteuerung auf "XON/XOFF" festzulegen.
     > 
     > 
7. Klicken Sie auf **Öffnen** , um eine serielle Sitzung zu starten.

