---
title: Durchführen eines Upgrades auf ein Speicherkonto vom Typ „Allgemein v2“ – Azure Storage | Microsoft-Dokumentation
description: Führen Sie ein Upgrade auf ein Speicherkonto vom Typ „Allgemein v2“ durch.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/26/2019
ms.author: tamram
ms.openlocfilehash: 2d6a5c96bf99439520e26fc905668835944cee29
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66115644"
---
# <a name="upgrade-to-a-general-purpose-v2-storage-account"></a>Durchführen eines Upgrades auf ein Speicherkonto vom Typ „Allgemein v2“

Speicherkonten vom Typ „Allgemein v2“ unterstützen die neuesten Azure Storage-Features und umfassen die gesamte Funktionalität von Konten des Typs „Allgemein v1“ und von Blob-Speicherkonten. Konten vom Typ „Allgemein v2“ werden für die meisten Speicherszenarien empfohlen. Konten vom Typ „Allgemein v2“ bieten die niedrigsten Preise pro Gigabyte für Azure Storage sowie wettbewerbsfähige Transaktionspreise.

Das Durchführen eines Upgrades auf ein Speicherkonto vom Typ „Allgemein v2“ von einem Speicherkonto vom Typ „Allgemein v1“ oder einem Blob-Speicherkonto ist einfach. Sie können für das Upgrade das Azure-Portal, PowerShell oder die Azure CLI verwenden.

> [!IMPORTANT]
> Ein Upgrade eines Speicherkontos vom Typ „Allgemein v1“ oder eines Blob-Speicherkontos auf „Allgemein v2“ ist dauerhaft und kann nicht rückgängig gemacht werden.

## <a name="upgrade-using-the-azure-portal"></a>Upgrade mithilfe des Azure-Portals

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Navigieren Sie zum Speicherkonto.
3. Klicken Sie im Abschnitt **Einstellungen** auf **Konfiguration**.
4. Klicken Sie unter **Kontoart** auf **Upgrade**.
5. Geben Sie unter **Confirm upgrade** (Upgrade bestätigen) den Namen Ihres Kontos ein.
6. Klicken Sie unten auf dem Blatt auf **Upgrade durchführen**.

    ![Upgrade für Konto durchführen](../blobs/media/storage-blob-account-upgrade/upgrade-to-gpv2-account.png)

## <a name="upgrade-with-powershell"></a>Aktualisieren mit PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Wenn Sie für ein Konto vom Typ „Allgemein v1“ mit PowerShell ein Upgrade auf ein Konto vom Typ „Allgemein v2“ durchführen möchten, sollten Sie zuerst PowerShell aktualisieren, damit die aktuelle Version des Moduls **Az.Storage** verwendet wird. Informationen zur Installation von PowerShell finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps).

Rufen Sie anschließend den folgenden Befehl auf, um das Konto zu aktualisieren, und ersetzen Sie den Namen Ihrer Ressourcengruppe und des Speicherkontos:

```powershell
Set-AzStorageAccount -ResourceGroupName <resource-group> -AccountName <storage-account> -UpgradeToStorageV2
```

## <a name="upgrade-with-azure-cli"></a>Aktualisieren per Azure CLI

Wenn Sie für ein Konto vom Typ „Allgemein v1“ mit der Azure CLI ein Upgrade auf ein Konto vom Typ „Allgemein v2“ durchführen möchten, installieren Sie zuerst die aktuelle Version der Azure CLI. Informationen zum Installieren der CLI finden Sie unter [Installieren von Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

Rufen Sie anschließend den folgenden Befehl auf, um das Konto zu aktualisieren, und ersetzen Sie den Namen Ihrer Ressourcengruppe und des Speicherkontos:

```cli
az storage account update -g <resource-group> -n <storage-account> --set kind=StorageV2
```

## <a name="specify-an-access-tier-for-blob-data"></a>Angeben einer Zugriffsebene für Blobdaten

Konten vom Typ „Allgemein v2“ unterstützen alle Azure Storage Services und -Datenobjekte, aber Zugriffsebenen sind nur für Blockblobs im Blobspeicher verfügbar. Wenn Sie ein Upgrade auf ein Speicherkonto vom Typ „Allgemein v2“ durchführen, können Sie eine Zugriffsebene für Ihre Blobdaten angeben.

Zugriffsebenen ermöglichen es, den kostengünstigsten Speicher basierend auf Ihren erwarteten Nutzungsmustern auszuwählen. Blockblobs können auf der heißen, der kalten oder der Archivspeicherebene gespeichert werden. Weitere Informationen zu Zugriffsebenen finden Sie unter [Azure Blob Storage: Speicherebenen „Premium“ (Vorschauversion), „Heiß“, „Kalt“ und „Archiv“](../blobs/storage-blob-storage-tiers.md).

Standardmäßig wird ein neues Speicherkonto auf der Zugriffsebene „Heiß“ (Hot) erstellt, und für ein Speicherkonto vom Typ „Allgemein v1“ wird ein Upgrade auf die Zugriffsebene „Heiß“ durchgeführt. Wenn Sie untersuchen, welche Zugriffsebene nach dem Upgrade Ihrer Daten verwendet werden soll, sollten Sie Ihr Szenario berücksichtigen. Es gibt zwei typische Benutzerszenarien für die Migration auf ein Konto vom Typ „Allgemein v2“:

* Sie verfügen über ein vorhandenes Speicherkonto vom Typ „Allgemein v1“ und möchten ein Upgrade auf ein Speicherkonto vom Typ „Allgemein v2“ mit der richtigen Speicherzugriffsebene für Blobdaten bewerten.
* Sie haben sich für die Nutzung eines Speicherkontos vom Typ „Allgemein v2“ entschieden oder besitzen bereits ein Konto dieser Art und möchten bewerten, ob Sie die Speicherebene „Heiß“ oder „Kalt“ für Blobdaten verwenden sollten.

In beiden Fällen sollten Sie zuerst die Kosten für Speicherung, Zugriff und Arbeiten mit Ihren Daten in einem Speicherkonto vom Typ „Allgemein v2“ schätzen und diesen Betrag mit Ihren derzeitigen Kosten vergleichen.

## <a name="pricing-and-billing"></a>Preise und Abrechnung

Ein Upgrade Ihres Speicherkontos vom Typ „v1“ auf ein Konto vom Typ „Allgemein v2“ ist kostenlos. Das Ändern der Speicherzugriffstarifs kann zu Änderungen an Ihrer Rechnung führen. 

Für alle Speicherkonten wird ein Blobspeicher-Preismodell verwendet, das auf der Ebene der einzelnen Blobs basiert. Bei Verwendung eines Speicherkontos sollten folgende Abrechnungsaspekte berücksichtigt werden:

* **Speicherkosten:** Die Kosten für die Datenspeicherung hängen nicht nur von der gespeicherten Datenmenge ab, sondern auch von der Speicherzugriffsebene. Je „cooler“ die Ebene, desto geringer die Kosten pro GB.

* **Kosten für den Datenzugriff:** Je „kälter“ die Ebene, desto höher die Gebühren für den Datenzugriff. Bei den Speicherzugriffsebenen „Kalt“ und „Archiv“ fallen Zugriffsgebühren für Lesevorgänge pro Gigabyte an.

* **Transaktionskosten:** Für alle Ebenen fällt eine Gebühr pro Transaktion an, die sich erhöht, je „kälter“ die Ebene ist.

* **Datenübertragungskosten bei Georeplikation:** Diese Gebühr gilt nur für Konten mit konfigurierter Georeplikation (einschließlich GRS und RA-GRS). Die Datenübertragung für die Georeplikation wird pro Gigabyte abgerechnet.

* **Kosten für ausgehende Datenübertragungen:** Ausgehende Datenübertragungen (Daten, die aus einer Azure-Region übertragen werden) werden genauso wie bei allgemeinen Speicherkonten nach Bandbreitennutzung pro Gigabyte abgerechnet.

* **Änderung der Speicherzugriffsebene:** Bei einem Wechsel der Zugriffsebene des Speicherkontos von „Kalt“ zu „Heiß“ fällt eine Gebühr an, die den Kosten entspricht, die durch das Lesen aller im Speicherkonto vorhandenen Daten entstehen. Beim Ändern der Zugriffsebene des Kontos von „Heiß“ in „Kalt“ fällt aber eine Gebühr an, die dem Schreiben aller Daten auf die Ebene „Kalt“ entspricht (nur GPv2-Konten).

> [!NOTE]
> Weitere Informationen zum Preismodell für Speicherkonten finden Sie auf der Seite [Preise für Azure Storage](https://azure.microsoft.com/pricing/details/storage/). Weitere Informationen zu den Kosten für ausgehende Datenübertragungen finden Sie auf der Seite [Datenübertragungen – Preisdetails](https://azure.microsoft.com/pricing/details/data-transfers/).

### <a name="estimate-costs-for-your-current-usage-patterns"></a>Schätzen der Kosten für Ihr aktuelles Nutzungsmuster

Zur Ermittlung der Kosten für die Speicherung und den Zugriff auf Blobdaten in einem Speicherkonto vom Typ „Allgemein v2“ auf einer bestimmten Ebene evaluieren Sie Ihr bestehendes Nutzungsmuster, oder schätzen Sie Ihr erwartetes Nutzungsmuster. Dazu benötigen Sie im Allgemeinen folgende Informationen:

* Ihr Blobspeicherverbrauch in GB, einschließlich:
    - Wie viele Daten werden im Speicherkonto gespeichert?
    - Wie ändert sich das Datenvolumen monatlich; werden alte Daten beständig durch neue Daten ersetzt?
* Das primäre Zugriffsmuster für Ihre Blobspeicherdaten, einschließlich:
    - Wie viele Daten werden aus dem Speicherkonto gelesen und in das Speicherkonto geschrieben?
    - Wie viele Lesevorgänge finden im Vergleich zu Schreibvorgängen für die Daten im Speicherkonto statt?

Um die für Ihre Bedürfnisse am besten geeignete Zugriffsebene zu bestimmen, kann es hilfreich sein zu ermitteln, wie viel Kapazität Ihre Blobdaten nutzen und wie diese Daten verwendet werden. Dazu überprüfen Sie am besten die Überwachungsmetriken für Ihr Konto.

### <a name="monitoring-existing-storage-accounts"></a>Überwachen von vorhandenen Speicherkonten

Um Ihre vorhandenen Speicherkonten zu überwachen und diese Daten zu sammeln, können Sie Azure Storage Analytics nutzen. Damit wird eine Protokollierung durchgeführt, und es werden Metrikdaten für ein Speicherkonto bereitgestellt. Von Storage Analytics können Metriken gespeichert werden, zu denen aggregierte Transaktionsstatistiken und Kapazitätsdaten für die an einen Speicherdienst für GPv1-, GPv2- und Blob-Speicherkonten gesendeten Anforderungen zählen. Diese Daten werden in bekannten Tabellen in demselben Speicherkonto gespeichert.

Weitere Informationen finden Sie unter [Informationen zu Metriken der Speicheranalyse](https://msdn.microsoft.com/library/azure/hh343258.aspx) und [Schema der Tabellen für Speicheranalysemetriken](https://msdn.microsoft.com/library/azure/hh343264.aspx).

> [!NOTE]
> Blob-Speicherkonten machen den Tabellenspeicherdienst-Endpunkt nur zum Speichern und Zugreifen auf die Metrikdaten für das Konto verfügbar.

Zur Überwachung des Speicherbedarfs für den Blobspeicher müssen Sie die Kapazitätsmetriken aktivieren.
Wenn dies aktiviert ist, werden täglich Kapazitätsdaten für den Blob-Dienst eines Speicherkontos aufgezeichnet. Sie werden als Tabelleneintrag aufgezeichnet, der in die Tabelle *$MetricsCapacityBlob* desselben Speicherkontos geschrieben wird.

Zum Überwachen des Datenzugriffsmusters für Blobspeicher müssen Sie die Stundentransaktionsmetriken über die API aktivieren. Wenn die Stundentransaktionsmetriken aktiviert sind, werden jede Stunde die Transaktionen pro API aggregiert und als Tabelleneintrag aufgezeichnet, der in die Tabelle *$MetricsHourPrimaryTransactionsBlob* desselben Speicherkontos geschrieben wird. In der Tabelle *$MetricsHourSecondaryTransactionsBlob* werden bei Verwendung von RA-GRS-Speicherkonten die Transaktionen für den sekundären Endpunkt aufgezeichnet.

> [!NOTE]
> Falls Sie über ein allgemeines Speicherkonto verfügen, in dem Sie neben Block- und Anfügeblob-Daten Seitenblobs und Datenträger virtueller Computer oder Warteschlangen, Dateien oder Tabellen gespeichert haben, ist dieser Schätzungsprozess nicht geeignet. Bei den Kapazitätsdaten wird nicht zwischen Blockblobs und anderen Typen unterschieden, und es werden keine Kapazitätsdaten für andere Datentypen bereitgestellt. Bei Verwendung dieser Typen besteht eine alternative Methode darin, sich die Mengen auf der letzten Rechnung anzusehen.

Um eine gute Annäherung des Datenverbrauchs und der Zugriffsmuster zu erhalten, empfehlen wir Ihnen die Auswahl eines Aufbewahrungszeitraums für die Metriken, der für die reguläre Nutzung repräsentativ ist und den Sie dann extrapolieren können. Eine Option besteht darin, die Metrikdaten für sieben Tage aufzubewahren und die Daten jede Woche für die Analyse am Monatsende zu erfassen. Eine andere Möglichkeit ist die Aufbewahrung der Metrikdaten der letzten 30 Tage, um sie dann am Ende der 30 Tage zu erfassen und zu analysieren.

Informationen zum Aktivieren, Erfassen und Anzeigen von Metrikdaten finden Sie unter [Metriken der Speicheranalyse](../common/storage-analytics-metrics.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

> [!NOTE]
> Speicherung, Verwendung und Download von Analysedaten werden genau wie bei regulären Benutzerdaten in Rechnung gestellt.

### <a name="utilizing-usage-metrics-to-estimate-costs"></a>Verwenden von Nutzungsmetriken zum Schätzen von Kosten

#### <a name="capacity-costs"></a>Kapazitätskosten

Der letzte Eintrag in der Kapazitätsmetrikentabelle *$MetricsCapacityBlob* mit dem Zeilenschlüssel *'data'* enthält die von den Benutzerdaten verbrauchte Speicherkapazität. Der letzte Eintrag in der Kapazitätsmetrikentabelle *$MetricsCapacityBlob* mit dem Zeilenschlüssel *'analytics'* enthält die von den Analyseprotokollen verbrauchte Speicherkapazität.

Diese gesamte Kapazität, die von Benutzerdaten und Analyseprotokollen (falls aktiviert) verbraucht wird, kann dann verwendet werden, um die Kosten für das Speichern der Daten im Speicherkonto zu schätzen. Diese Vorgehensweise kann auch zum Schätzen von Speicherkosten in GPv1-Speicherkonten verwendet werden.

#### <a name="transaction-costs"></a>Transaktionskosten

Die Summe von *'TotalBillableRequests'* über alle Einträge für eine API in der Transaktionsmetrikentabelle hinweg gibt die Gesamtzahl der Transaktionen für die jeweilige API an. *Beispiel:* Die Gesamtanzahl von Transaktionen vom Typ *GetBlob* eines bestimmten Zeitraums kann anhand der Gesamtsumme von abrechenbaren Anforderungen für alle Einträge mit dem Zeilenschlüssel *user;GetBlob* berechnet werden.

Zur Ermittlung der ungefähren Transaktionskosten für Blob-Speicherkonten müssen die Transaktionen in drei Gruppen unterteilt werden, da jeweils unterschiedliche Preise gelten.

* Schreibtransaktionen wie *'PutBlob'*, *'PutBlock'*, *'PutBlockList'*, *'AppendBlock'*, *'ListBlobs'*, *'ListContainers'*, *'CreateContainer'*, *'SnapshotBlob'* und *'CopyBlob'*.
* Löschtransaktionen wie *'DeleteBlob'* und *'DeleteContainer'*.
* Alle anderen Transaktionen.

Um die Transaktionskosten für GPv1-Speicherkonten zu schätzen, müssen Sie alle Transaktionen unabhängig vom Vorgang bzw. von der API aggregieren.

#### <a name="data-access-and-geo-replication-data-transfer-costs"></a>Datenübertragungskosten für Datenzugriff und Georeplikation

Die Speicheranalyse liefert zwar nicht die Menge der Daten, die aus einem Speicherkonto gelesen und in das Speicherkonto geschrieben wird, aber dieser Wert kann grob geschätzt werden, indem die Tabelle mit den Transaktionsmetriken verwendet wird. Die Summe von *'TotalIngress'* über alle Einträge für eine API in der Transaktionsmetrikentabelle hinweg gibt die Gesamtmenge der Eingangsdaten in Byte für die jeweilige API an. Analog dazu gibt die Summe von *'TotalEgress'* die Gesamtmenge der Ausgangsdaten in Byte an.

Zur Ermittlung der ungefähren Datenzugriffskosten für Blob-Speicherkonten müssen die Transaktionen in zwei Gruppen unterteilt werden.

* Die Menge der Daten, die aus dem Speicherkonto abgerufen werden, kann geschätzt werden, indem vor allem für die Vorgänge *'GetBlob'* und *'CopyBlob'* die Summe von *'TotalEgress'* geprüft wird.

* Die Menge der Daten, die in das Speicherkonto geschrieben werden, kann anhand der Summe von *'TotalIngress'* für die Vorgänge *'PutBlob'*, *'PutBlock'*, *'CopyBlob'* und *'AppendBlock'* geschätzt werden.

Bei Verwendung eines GRS- oder RA-GRS-Speicherkontos können die Datenübertragungskosten mit Georeplikation für Blob-Speicherkonten auch auf der Grundlage der Schätzung für die Menge an geschriebenen Daten berechnet werden.

> [!NOTE]
> Ein ausführlicheres Beispiel zur Berechnung der Kosten für die Verwendung der Speicherzugriffsebene „Heiß“ oder „Kalt“ finden Sie im FAQ-Bereich mit dem Titel *Was sind die Zugriffsebenen „Heiß“ und „Kalt“, und wie bestimme ich, welche Zugriffsebene ich wählen sollte?* im FAQ-Bereich mit dem Titel [Was sind die Zugriffsebenen „Heiß“ und „Kalt“, und wie bestimme ich, welche Zugriffsebene ich wählen sollte?](https://azure.microsoft.com/pricing/details/storage/)an.

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen eines Speicherkontos](storage-quickstart-create-account.md)
- [Verwalten von Azure Storage-Konten](storage-account-manage.md)
