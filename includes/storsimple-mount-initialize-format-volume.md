---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 592131bf6cca4c4c3c827de23742e8d52bcb4d1c
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66118195"
---
#### <a name="to-mount-initialize-and-format-a-volume"></a>So stellen Sie ein Volume bereit und initialisieren und formatieren dieses
1. Starten Sie den Microsoft iSCSI-Initiator.
2. Klicken Sie im Fenster **Eigenschaften des iSCSI-Initiators** auf der Registerkarte **Erkennung** auf **Portal ermitteln**.
3. Geben Sie im Dialogfeld **Zielportal ermitteln** die IP-Adresse Ihrer iSCSI-aktivierten Netzwerkschnittstelle an, und klicken Sie dann auf **OK**. 
4. Suchen Sie auf der Registerkarte **Ziele** des Fensters **Eigenschaften des iSCSI-Initiators** nach **Ermittelte Ziele**. Als Gerätestatus sollte **Inaktiv**angezeigt werden.
5. Wählen Sie das Zielgerät aus, und klicken Sie dann auf **Verbinden**. Nachdem das Gerät verbunden ist, sollte sich der Status in **Verbunden**ändern. (Weitere Informationen zum Verwenden des Microsoft iSCSI-Initiators finden Sie unter [Installieren und Konfigurieren des Microsoft iSCSI-Initiators][1].)
6. Drücken Sie auf Ihrem Windows-Host die Windows-Logo-Taste+X, und klicken Sie dann auf **Ausführen**. 
7. Geben Sie im Dialogfeld **Ausführen** die Angabe **Diskmgmt.msc** ein. Klicken Sie auf **OK**. Das Dialogfeld **Datenträgerverwaltung** wird angezeigt. Im rechten Bereich werden die Volumes auf Ihrem Host angezeigt.
8. Im Fenster **Datenträgerverwaltung** werden die bereitgestellten Volumes wie in der folgenden Abbildung dargestellt angezeigt. Klicken Sie mit der rechten Maustaste auf das ermittelte Volume (klicken Sie auf den Datenträgernamen), und klicken Sie dann auf **Online**.
   
     ![Initialisieren und Formatieren von Volumes](./media/storsimple-mount-initialize-format-volume/HCS_InitializeFormatVolume-include.png) 
9. Klicken Sie erneut mit der rechten Maustaste auf das Volume (klicken Sie auf den Datenträgernamen), und klicken Sie dann auf **Initialisieren**.
10. Führen Sie die folgenden Schritte aus, um ein einfaches Volume zu formatieren.
    
    1. Wählen Sie das Volume aus, klicken Sie mit der rechten Maustaste darauf (klicken Sie im rechten Bereich), und klicken Sie dann auf **Neues einfaches Volume**.
    2. Geben Sie im Assistenten für neue einfache Volumes die Volumegröße und den Laufwerkbuchstaben an, und konfigurieren Sie das Volume dann als Dateisystemtyp "NTFS".
    3. Geben Sie 64 KB als Zuordnungseinheitsgröße an. Diese Zuordnungseinheitsgröße eignet sich gut für die Deduplikationsalgorithmen, die in der StorSimple-Lösung verwendet werden.
    4. Führen Sie eine Schnellformatierung durch.

![Video verfügbar](./media/storsimple-mount-initialize-format-volume/Video_icon.png)**Video verfügbar**

Sie können sich [hier](https://azure.microsoft.com/documentation/videos/mount-initialize-and-format-a-storsimple-volume/)ein Video anschauen, in dem das Bereitstellen, Initialisieren und Formatieren eines StorSimple-Volumes demonstriert wird.

<!--Link references-->
[1]: https://technet.microsoft.com/library/ee338480(WS.10).aspx
