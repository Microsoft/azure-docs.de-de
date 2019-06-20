---
title: Microsoft Azure Data Box-Datenträger – Häufig gestellte Fragen | Microsoft-Dokumentation
description: Hier finden Sie häufig gestellte Fragen und Antworten zu Azure Data Box Disk, einer Cloudlösung, mit der Sie große Datenmengen an Azure übertragen können.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: overview
ms.date: 05/23/2019
ms.author: alkohli
ms.openlocfilehash: 69068304a6bc72f503ed85edf05229f10e4a8bc0
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66236347"
---
# <a name="what-is-azure-data-box-disk"></a>Was ist ein Azure Data Box-Datenträger?

Mit der Cloudlösung für Microsoft Azure Data Box-Datenträger können Sie Daten im Terabyte-Bereich schnell, kostengünstig und zuverlässig an Azure senden. Diese FAQ-Seite enthält Fragen, die bei der Verwendung von Data Box-Datenträgern im Azure-Portal aufkommen können, und die dazugehörigen Antworten. 

Die Fragen und Antworten sind in folgende Kategorien unterteilt:

- Informationen zum Dienst
- Konfigurieren und Verbinden 
- Nachverfolgen des Status
- Migrieren von Daten 
- Überprüfen und Hochladen von Daten 


## <a name="about-the-service"></a>Informationen zum Dienst

### <a name="q-what-is-azure-data-box-service"></a>F: Was ist der Azure Data Box-Dienst? 
A.  Der Azure Data Box-Dienst wurde für die Offlineerfassung von Daten konzipiert. Mit diesem Dienst wird ein Array mit Produkten verwaltet, die für den Datentransport für unterschiedliche Speicherkapazitäten ausgelegt sind. 

### <a name="q-what-are-azure-data-box-disks"></a>F: Was sind Azure Data Box-Datenträger?
A. Die Azure Data Box-Datenträger ermöglichen eine schnelle, kostengünstige und sichere Übertragung von Daten im Terabyte-Bereich für Azure (ein- und ausgehend). Sie erhalten von Microsoft ein bis fünf Datenträger mit einer maximalen Speicherkapazität von 35 TB. Sie können diese Datenträger über den Data Box-Dienst im Azure-Portal leicht konfigurieren, anschließen und entsperren.  

Datenträger werden mithilfe der Microsoft-BitLocker-Laufwerkverschlüsselung verschlüsselt, und Ihre Verschlüsselungsschlüssel werden über das Azure-Portal verwaltet. Sie kopieren die Daten dann von den Servern des Kunden. Im Rechenzentrum migriert Microsoft Ihre Daten vom Laufwerk zur Cloud, indem ein Link für das schnelle Hochladen ins private Netzwerk verwendet wird, und anschließend werden die Daten in Azure hochgeladen.

### <a name="q-when-should-i-use-data-box-disks"></a>F: Wann sollte ich Data Box-Datenträger nutzen?
A. Sie profitieren von der Verwendung von Data Box-Datenträgern, wenn Sie über 40 TB an Daten (oder weniger) verfügen, die Sie an Azure übertragen möchten.

### <a name="q-what-is-the-price-of-data-box-disks"></a>F: Was kosten Data Box-Datenträger?
A. Informationen zum Preis von Data Box-Datenträgern finden Sie auf der [Seite mit den Preisen](https://azure.microsoft.com/pricing/details/databox/disk/).

### <a name="q-how-do-i-get-data-box-disks"></a>F: Wie erhalte ich Data Box-Datenträger? 
A.  Melden Sie sich als Nächstes am Azure-Portal an, und erstellen Sie einen Data Box-Auftrag für Datenträger, um Azure Data Box-Datenträger zu erhalten. Geben Sie Ihre Kontaktinformationen und Benachrichtigungsdetails an. Nachdem Sie den Auftrag erstellt haben, werden die Datenträger je nach Verfügbarkeit innerhalb von zehn Tagen an Sie gesendet.

### <a name="q-what-is-the-maximum-amount-of-data-i-can-transfer-with-data-box-disks-in-one-instance"></a>F: Welche Menge von Daten kann ich mit Data Box-Datenträgern auf einmal übertragen?
A. Für fünf Datenträger mit jeweils 8 TB (7 TB nutzbare Kapazität) ergibt sich eine maximal nutzbare Kapazität von 35 TB. Sie können also 35 TB an Daten auf einmal übertragen. Um eine größere Datenmenge zu übertragen, können Sie weitere Datenträger bestellen.

### <a name="q-how-can-i-check-if-data-box-disks-are-available-in-my-region"></a>F: Wie kann ich prüfen, ob Data Box-Datenträger in meiner Region verfügbar sind? 
A.  Unter [Regionale Verfügbarkeit](data-box-disk-overview.md#region-availability) können Sie sehen, in welchen Regionen Data Box-Datenträger derzeit verfügbar sind.  

### <a name="q-which-regions-can-i-store-data-in-with-data-box-disks"></a>F: In welchen Regionen kann ich Daten über Data Box-Datenträger speichern?
A. Data Box Disk wird für alle Regionen in den USA, Kanada, Australien, Westeuropa und Nordeuropa sowie Korea und Japan unterstützt. Es werden nur die öffentlichen Azure-Cloudregionen unterstützt. Azure Government oder andere Sovereign Clouds werden nicht unterstützt.

### <a name="q-will-my-data-box-disk-cross-country-borders-during-shipping"></a>F: Überquert Data Box Disk beim Versand Ländergrenzen?
A. Data Box Disk-Geräte werden aus dem Bestimmungsland versendet, sie überqueren keine internationalen Grenzen. Die einzige Ausnahme betrifft Bestellungen innerhalb der Europäischen Union (EU), wo Datenträger in und aus beliebige(n) EU-Länder(n) gesendet werden können.

### <a name="q-whom-should-i-contact-if-i-encounter-any-issues--with-data-box-disks"></a>F: An wen sollte ich mich wenden, falls Probleme mit Data Box-Datenträgern auftreten?
A. Falls Probleme mit Data Box-Datenträgern auftreten, wenden Sie sich an den [Microsoft-Support](https://docs.microsoft.com/azure/databox/data-box-disk-contact-microsoft-support).

## <a name="configure-and-connect"></a>Konfigurieren und Verbinden
 
### <a name="q-can-i-specify-the-number-of-data-box-disks-in-the-order"></a>F: Kann ich im Auftrag die Anzahl von Data Box-Datenträgern angeben?
A.  Nein. Sie erhalten je nach Datenumfang und Verfügbarkeit einen oder mehrere Datenträger mit 8 TB Speicherplatz (maximal fünf Datenträger).  

### <a name="q-how-do-i-unlock-the-data-box-disks"></a>F: Wie entsperre ich die Data Box-Datenträger? 
A.  Navigieren Sie im Azure-Portal zu Ihrem Auftrag für Data Box-Datenträger und dann zu **Gerätedetails**. Kopieren Sie den Hauptschlüssel. Laden Sie das Tool zum Entsperren von Data Box-Datenträgern für Ihr Betriebssystem über das Azure-Portal herunter, und extrahieren Sie es. Führen Sie das Tool auf dem Computer mit den Daten aus, die Sie auf die Datenträger kopieren möchten. Geben Sie den Hauptschlüssel an, um Ihre Datenträger zu entsperren. Alle Datenträger werden mit demselben Hauptschlüssel entsperrt. 

Eine Schritt-für-Schritt-Anleitung finden Sie unter den Informationen zum [Entsperren von Datenträgern auf einem Windows-Client](data-box-disk-deploy-set-up.md#unlock-disks-on-windows-client) bzw. [Entsperren von Datenträgern auf einem Linux-Client](data-box-disk-deploy-set-up.md#unlock-disks-on-linux-client).

### <a name="q-can-i-use-a-linux-host-computer-to-connect-and-copy-the-data-on-to-the-data-box-disks"></a>F: Kann ich einen Linux-Hostcomputer verwenden, um eine Verbindung herzustellen und die Daten auf die Data Box-Datenträger zu kopieren?
A.  Ja. Sowohl der Linux- als auch der Windows-Client können verwendet werden, um eine Verbindung herzustellen und Daten in Data Box Disk zu kopieren. Weitere Informationen finden Sie in der Liste mit den [unterstützten Betriebssystemen](data-box-disk-system-requirements.md) für Ihren Hostcomputer.

### <a name="q-my-disks-are-dispatched-but-now-i-want-to-cancel-this-order-why-is-the-cancel-button-not-available"></a>F: Meine Datenträger wurden versendet, aber ich möchte den Auftrag jetzt stornieren. Warum ist die Schaltfläche zum Stornieren nicht verfügbar?
A.  Sie können den Auftrag nur stornieren, wenn die Datenträger bestellt, aber noch nicht versendet wurden. Nachdem die Datenträger versendet wurden, ist eine Stornierung des Auftrags nicht mehr möglich. Sie haben aber die Möglichkeit, Ihre Datenträger gegen eine Gebühr zurückzugeben. 

### <a name="q-can-i-connect-multiple-data-box-disks-at-the-same-to-the-host-computer-to-transfer-data"></a>F: Kann ich mehrere Data Box-Datenträger gleichzeitig an denselben Hostcomputer anschließen, um Daten zu übertragen?
A. Ja. Mehrere Data Box-Datenträger können an denselben Hostcomputer angeschlossen werden, um Daten zu übertragen, und es können mehrere Kopieraufträge parallel durchgeführt werden.

## <a name="track-status"></a>Nachverfolgen des Status

### <a name="q-how-do-i-track-the-disks-from-when-i-placed-the-order-to-shipping-the-disks-back"></a>F: Wie kann ich für die Datenträger den Status ab der Erstellung des Auftrags bis zur Rücksendung der Datenträger nachverfolgen? 
A.  Sie können den Status des Auftrags für Data Box-Datenträger im Azure-Portal nachverfolgen. Beim Erstellen des Auftrags werden Sie auch aufgefordert, eine E-Mail-Adresse für Benachrichtigungen anzugeben. Wenn Sie eine E-Mail-Adresse angegeben haben, werden Sie per E-Mail über alle Statusänderungen des Auftrags informiert. [Hier finden Sie weitere Informationen zum Konfigurieren von E-Mail-Benachrichtigungen](data-box-portal-ui-admin.md#edit-notification-details).

### <a name="q-how-do-i-return-the-disks"></a>F: Wie kann ich die Datenträger zurücksenden? 
A.  Microsoft fügt dem Versandpaket für die Data Box-Datenträger ein Versandetikett bei. Befestigen Sie das Etikett am Versandpaket, und geben Sie das sicher verschlossene Paket beim Paketdienst ab. Falls das Etikett beschädigt oder nicht mehr vorhanden ist, können Sie unter **Übersicht > Versandetikett herunterladen** ein neues Versandetikett erhalten.

## <a name="migrate-data"></a>Migrieren von Daten

### <a name="q-what-is-the-maximum-data-size-that-can-be-used-with-data-box-disks"></a>F: Welche Datenmenge kann für Data Box-Datenträger maximal verwendet werden?  
A.  Bei der Lösung mit Data Box-Datenträgern können bis zu fünf Datenträger mit einer maximalen Kapazität von 35 TB genutzt werden. Die Größe der Datenträger beträgt jeweils 8 TB (7 TB nutzbar).

### <a name="q-what-are-the-maximum-block-blob-and-page-blob-sizes-supported-by-data-box-disks"></a>F: Welche Größen für Blockblobs und Seitenblobs werden für Data Box-Datenträger maximal unterstützt? 
A.  Die maximale Größe richtet sich nach den Speichergrenzwerten für Azure. Die maximale Größe für Blockblobs beträgt ca. 4,768 TiB, und die maximale Größe für Seitenblobs beträgt 8 TiB. Weitere Informationen finden Sie unter [Skalierbarkeits- und Leistungsziele für Azure Storage](../storage/common/storage-scalability-targets.md).

### <a name="q-what-is-the-data-transfer-speed-for-data-box-disks"></a>F: Wie hoch ist die Datenübertragungsgeschwindigkeit für Data Box-Datenträger?
A. Beim Testen mit Datenträgern, die über USB 3.0 verbunden waren, betrug die Datenträgerleistung bis zu 430 MB/s. Die tatsächlichen Zahlen variieren abhängig von der Größe der verwendeten Datei. Bei kleineren Dateien ist die Leistung unter Umständen etwas reduziert.

### <a name="q-how-do-i-know-that-my-data-is-secure-during-transit"></a>F: Woher weiß ich, dass meine Daten während der Übertragung geschützt sind? 
A.  Data Box-Datenträger werden per BitLocker-Verschlüsselung (AES-128 Bit) verschlüsselt, und der Hauptschlüssel ist nur über das Azure-Portal verfügbar. Melden Sie sich mit den Anmeldeinformationen für Ihr Konto am Azure-Portal an, um den Hauptschlüssel zu erhalten. Geben Sie diesen Hauptschlüssel an, wenn Sie das Tool zum Entsperren von Data Box-Datenträgern ausführen.

### <a name="q-how-do-i-copy-the-data-to-the-data-box-disks"></a>F: Wie kopiere ich die Daten auf die Data Box-Datenträger? 
A.  Verwenden Sie ein SMB-Kopiertool, z.B. Robocopy, Diskboss oder auch Drag & Drop im Windows-Datei-Explorer, um Daten auf Datenträger zu kopieren.

### <a name="q-are-there-any-tips-to-speed-up-the-data-copy"></a>F: Gibt es Tipps zur Beschleunigung des Datenkopiervorgangs?
A.  Sie können den Kopiervorgang wie folgt beschleunigen:

- Nutzen Sie mehrere Datenströme zum Kopieren von Daten. Verwenden Sie bei Robocopy beispielsweise die Option für Multithreading. Weitere Informationen zu den genauen Befehlen, die jeweils verwendet werden, finden Sie unter [Tutorial: Kopieren von Daten auf die Azure Data Box Disk und Durchführen der Überprüfung](data-box-disk-deploy-copy-data.md#copy-data-to-disks).
- Verwenden Sie mehrere Sitzungen.
- Stellen Sie sicher, dass sich die Daten lokal auf dem Computer befinden, an den die Datenträger angeschlossen sind, anstatt den Kopiervorgang über die Netzwerkfreigabe durchzuführen (um Beeinträchtigungen aufgrund der Netzwerkgeschwindigkeit zu vermeiden).
- Achten Sie darauf, dass Sie während des gesamten Kopiervorgangs USB 3.0 oder höher verwenden. Laden Sie das Tool [USBView](https://docs.microsoft.com/windows-hardware/drivers/debugger/usbview) herunter, und verwenden Sie es, um die USB-Controller und USB-Geräte zu identifizieren, die an den Computer angeschlossen sind.
- Erstellen Sie Benchmarkwerte zur Leistung des Computers, der zum Kopieren der Daten verwendet wird. Laden Sie das [Bluestop-Tool FIO](https://bluestop.org/fio/) herunter, um Benchmarkwerte zur Leistung der Serverhardware zu erhalten.

### <a name="q-how-to-speed-up-the-data-if-the-source-data-has-small-files-kbs-or-few-mbs"></a>F: Wie kann ich für die Daten eine Beschleunigung erreichen, wenn die Quelldaten kleine Dateien (KB oder nur wenige MB) enthalten?
A.  Sie können den Kopiervorgang wie folgt beschleunigen:

- Erstellen Sie im schnellen Speicher eine lokale VHDx oder eine leere VHD auf der HDD/SSD (langsamer).
- Stellen Sie die Daten auf einer VM bereit.
- Kopieren Sie die Dateien auf den Datenträger der VM.

### <a name="q-can-i-use-multiple-storage-accounts-with-data-box-disks"></a>F: Kann ich mehrere Speicherkonten mit Data Box-Datenträgern verwenden?
A.  Nein. Für Data Box-Datenträger wird derzeit nur ein Speicherkonto (allgemein oder klassisch) unterstützt. Es werden sowohl „heiße“ als auch „kalte“ Blobs unterstützt. Derzeit werden nur die Speicherkonten in den USA, Westeuropa und Nordeuropa unterstützt, die Teil der öffentlichen Azure-Cloud sind.

### <a name="q-what-is-the-toolset-available-for-my-data-with-data-box-disks"></a>F: Was umfasst das Toolset, das für meine Daten für Data Box-Datenträger verfügbar ist?
A. Das für Data Box Disk verfügbare Toolset enthält drei Tools:
 - **Data Box Disk-Tool zum Entsperren**: Verwenden Sie dieses Tool, um die verschlüsselten Datenträger zu entsperren, die von Microsoft ausgeliefert werden. Beim Entsperren der Datenträger mit dem Tool müssen Sie einen Hauptschlüssel angeben, der in der Data Box Disk-Bestellung im Azure-Portal enthalten ist. 
 - **Data Box Disk-Überprüfungstool**: Verwenden Sie dieses Tool, um Größe, Format und Blobnamen gemäß den Azure-Benennungskonventionen zu überprüfen. Außerdem werden damit Prüfsummen für die kopierten Daten generiert, die dann genutzt werden, um die in Azure hochgeladenen Daten zu überprüfen.
 - **Data Box Disk-Tool zum Aufteilen/Kopieren**: Verwenden Sie dieses Tool, wenn Sie mehrere Datenträger nutzen und über ein umfangreiches Dataset verfügen, das aufgeteilt und auf alle Datenträger kopiert werden muss. Dieses Tool ist derzeit für Windows verfügbar. Dieses Tool wird für verwaltete Datenträger nicht unterstützt. Mit diesem Tool wird beim Kopieren der Daten auch eine Überprüfung durchgeführt, daher können Sie bei Verwendung des Tools den Überprüfungsschritt überspringen.

Das Toolset steht sowohl für Windows als auch für Linux zur Verfügung. Sie können das Toolset hier herunterladen:
- [Herunterladen des Data Box Disk-Toolsets für Windows](https://aka.ms/databoxdisktoolswin) 
- [Herunterladen des Data Box Disk-Toolsets für Linux](https://aka.ms/databoxdisktoolslinux)
 
### <a name="q-can-i-use-data-box-disk-to-transfer-data-to-azure-files-and-then-use-the-data-with-azure-file-sync"></a>F: Kann ich mit Data Box Disk Daten an Azure Files übertragen und die Daten anschließend mit der Azure-Dateisynchronisierung verwenden? 
A. Azure Files wird für Data Box Disk unterstützt, funktioniert jedoch nicht gut mit der Azure-Dateisynchronisierung. Metadaten werden nicht beibehalten, wenn die Dateidaten mit der Azure-Dateisynchronisierung verwendet werden.


## <a name="verify-and-upload"></a>Überprüfen und Hochladen

### <a name="q-how-soon-can-i-access-my-data-in-azure-once-ive-shipped-the-disks-back"></a>F: Wie schnell kann ich in Azure auf meine Daten zugreifen, nachdem ich die Datenträger gesendet habe? 
A.  Nachdem der Auftragsstatus für das Kopieren der Daten als „Abgeschlossen“ angezeigt wird, sollten Sie sofort auf Ihre Daten zugreifen können.

### <a name="q-where-is-my-data-located-in-azure-after-the-upload"></a>F: Wo befinden sich meine Daten nach dem Upload in Azure?
A.  Wenn Sie die Daten in die Ordner *BlockBlob* und *PageBlob* auf Ihrem Datenträger kopieren, wird unter dem Azure-Speicherkonto für jeden Unterordner von *BlockBlob* und *PageBlob* ein Container erstellt. Falls Sie die Dateien direkt in den Ordnern *BlockBlob* und *PageBlob* abgelegt haben, befinden sich diese im Standardcontainer *$root* unter dem Azure Storage-Konto. Wenn Sie die Daten in einen Order im Ordner *AzureFile* kopieren, wird eine Dateifreigabe erstellt.

### <a name="q-i-just-noticed-that-i-did-not-follow-the-azure-naming-requirements-for-my-containers-will-my-data-fail-to-upload-to-azure"></a>F: Ich habe gerade festgestellt, dass ich mich für meine Container nicht an die Azure-Anforderungen für die Benennung gehalten habe. Können meine Daten deshalb nicht in Azure hochgeladen werden?
A. Falls die Containernamen Großbuchstaben enthalten, werden sie automatisch in Kleinbuchstaben konvertiert. Wenn die Namen auf andere Art nicht konform sind (Sonderzeichen, andere Sprachen usw.), tritt beim Upload ein Fehler auf. Weitere Informationen finden Sie unter den [Benennungskonventionen für Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions).

### <a name="q-how-do-i-verify-the-data-i-copied-onto-multiple-data-box-disks"></a>F: Wie kann ich die Daten überprüfen, die auf mehrere Data Box-Datenträger kopiert wurden?
A.  Nach Abschluss des Kopiervorgangs für die Daten können Sie `DataBoxDiskValidation.cmd` aus dem Ordner *DataBoxDiskImport* ausführen, um Prüfsummen für die Validierung zu generieren. Falls Sie über mehrere Datenträger verfügen, müssen Sie pro Datenträger ein Befehlsfenster öffnen und diesen Befehl ausführen. Beachten Sie, dass dieser Vorgang je nach Größe Ihrer Daten ggf. lange dauern kann (mehrere Stunden).

### <a name="q-what-happens-to-my-data-after-i-have-returned-the-disks"></a>F: Was passiert mit meinen Daten, nachdem ich die Datenträger zurückgesendet habe?
A.  Nach Abschluss des Kopiervorgangs für die Daten nach Azure werden die Daten gemäß den NIST-Richtlinien (SP 800-88 Revision 1) auf sichere Weise gelöscht.  

### <a name="q-how-is-my-data-protected-during-transit"></a>F: Wie sind meine Daten während des Versands geschützt? 
A.  Die Data Box-Datenträger werden per Microsoft-BitLocker-Verschlüsselung (AES-128) verschlüsselt. Es wird ein gemeinsamer Hauptschlüssel benötigt, um alle Datenträger zu entsperren und auf die Daten zuzugreifen.

### <a name="q-do-i-need-to-rerun-checksum-validation-if-i-add-more-data-to-the-data-box-disks"></a>F: Muss ich die Überprüfung der Prüfsumme erneut durchführen, wenn ich den Data Box-Datenträgern weitere Daten hinzufüge?
A. Ja. Wenn Sie sich für die Validierung Ihrer Daten entscheiden (empfohlen), müssen Sie die Validierung erneut durchführen, falls Sie den Datenträgern weitere Daten hinzufügen.

### <a name="q-i-used-all-my-disks-to-transfer-data-and-need-to-order-more-disks-is-there-a-way-to-quickly-place-the-order"></a>F: Ich habe alle Datenträger zum Übertragen von Daten verwendet und muss weitere Datenträger anfordern. Gibt es eine Möglichkeit, die Bestellung ohne viel Zeitaufwand aufzugeben?
A. Sie können Ihren vorherigen Auftrag klonen. Beim Klonen wird derselbe Auftrag noch einmal erstellt, und Sie müssen nur noch die Auftragsdetails angeben, ohne Adresse, Kontaktinformationen und Benachrichtigungsdetails erneut eingeben zu müssen.

### <a name="q-i-copied-data-to-manageddisk-folder-i-dont-see-any-managed-disks-with-the-resource-group-specified-for-managed-disks-was-my-data-uploaded-to-azure-and-how-can-i-locate-it"></a>F: Ich habe Daten in den Ordner „ManagedDisk“ kopiert. Ich kann keine verwalteten Datenträger mit der für verwaltete Datenträger angegebenen Ressourcengruppe sehen. Wurden meine Daten in Azure hochgeladen, und wo befinden sie sich?
A. Ja. Ihre Daten wurden in Azure hochgeladen. Wenn Sie jedoch keine verwalteten Datenträger mit den angegebenen Ressourcengruppen sehen können, waren die Daten wahrscheinlich nicht gültig. Wenn die Seitenblobs, Blockblobs Azure-Dateien und verwalteten Datenträger nicht gültig sind, werden sie in den folgenden Ordnern gespeichert:
 - Seitenblobs werden in einem Blockblobcontainer gespeichert, der mit *databoxdisk-invalid-pb-* beginnt.
 - Azure-Dateien werden in einem Blockblobcontainer gespeichert, der mit *databoxdisk-invalid-af-* beginnt.
 - Verwaltete Datenträger werden in einem Blockblobcontainer gespeichert, der mit *databoxdisk-invalid-md-* beginnt.

## <a name="next-steps"></a>Nächste Schritte

- Sehen Sie sich die [Data Box Disk-Systemanforderungen](data-box-disk-system-requirements.md) an.
- Machen Sie sich mit den [Einschränkungen für Data Box-Datenträger](data-box-disk-limits.md) vertraut.
- Informieren Sie sich über die schnelle Bereitstellung von [Azure Data Box-Datenträgern](data-box-disk-quickstart-portal.md) im Azure-Portal.
