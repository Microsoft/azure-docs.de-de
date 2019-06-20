---
title: Tarife in Azure Database for MariaDB
description: In diesem Artikel werden die Tarife in Azure Database for MariaDB beschrieben.
author: jan-eng
ms.author: janeng
ms.service: mariadb
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 7a52d05c77d0aeb8ebeba196df60e59f0647fea9
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66233916"
---
# <a name="azure-database-for-mariadb-pricing-tiers"></a>Azure Database for MariaDB – Tarife

Sie können eine Azure Database for MariaDB-Serverinstanz basierend auf drei unterschiedlichen Tarifen erstellen: „Basic“, „Allgemein“ und „Arbeitsspeicheroptimiert“. Die Tarife unterscheiden sich anhand der bereitstellbaren Menge an Rechenleistung in V-Kernen, des Arbeitsspeichers pro V-Kern und der zum Speichern der Daten verwendeten Speichertechnologie. Alle Ressourcen werden auf der MariaDB-Serverebene bereitgestellt. Ein Server kann über eine oder mehrere Datenbanken verfügen.

|    | **Basic** | **Allgemeiner Zweck** | **Arbeitsspeicheroptimiert** |
|:---|:----------|:--------------------|:---------------------|
| Computegeneration | Gen 5 |Gen 5 | Gen 5 |
| V-Kerne | 1, 2 | 2, 4, 8, 16, 32, 64 |2, 4, 8, 16, 32 |
| Arbeitsspeicher pro V-Kern | 2 GB | 5 GB | 10 GB |
| Speichergröße | 5 GB bis 1 TB | 5 GB bis 4 TB | 5 GB bis 4 TB |
| Speichertyp | Azure-Standardspeicher | Azure Storage Premium | Azure Storage Premium |
| Aufbewahrungszeitraum von Datenbanksicherungen | 7 bis 35 Tage | 7 bis 35 Tage | 7 bis 35 Tage |

Um einen Tarif auszuwählen, verwenden Sie die folgende Tabelle als Ausgangspunkt.

| Tarif | Zielworkloads |
|:-------------|:-----------------|
| Basic | Workloads mit geringen Anforderungen an Rechen- und E/A-Leistung. Beispiele hierfür sind Server, die für die Entwicklung, für Tests oder für kleine, selten verwendete Anwendungen verwendet werden. |
| Allgemeiner Zweck | Geeignet für die meisten Unternehmensworkloads mit gängigen Compute- und Arbeitsspeicheranforderungen und skalierbarem E/A-Durchsatz. Hierzu zählen beispielsweise zum Hosten von Web- und mobilen Apps verwendete Server und andere Unternehmensanwendungen.|
| Arbeitsspeicheroptimiert | Geeignet für Hochleistungs-Datenbankworkloads, für die In-Memory-Leistung erforderlich ist, um eine schnellere Transaktionsverarbeitung und höhere Parallelität zu erzielen. Hierzu zählen beispielsweise Server für die Verarbeitung von Echtzeitdaten und leistungsstarke Transaktions- oder Analyse-Apps.|

Nach der Servererstellung können Sie die Anzahl von virtuellen Kernen und den Tarif (mit Ausnahme eines Wechsels zu oder von Basic) innerhalb weniger Sekunden ändern. Außerdem haben Sie die Möglichkeit, die Speichermenge einzeln zu erhöhen und den Aufbewahrungszeitraum für Sicherungen zu erhöhen oder zu verringern, ohne dass bei der Anwendung Ausfallzeiten auftreten. Der Sicherungsspeichertyp kann nach der Servererstellung nicht mehr geändert werden. Weitere Informationen finden Sie im Abschnitt [Skalieren von Ressourcen](#scale-resources).

## <a name="compute-generations-and-vcores"></a>Computegenerationen und V-Kerne

Computeressourcen werden in Form von virtuellen Kernen bereitgestellt und repräsentieren die logische CPU der zugrunde liegenden Hardware. Logische CPUs der Generation 5 basieren auf Intel-Prozessoren vom Typ E5-2673 v4 (Broadwell) 2,3 GHz.

## <a name="storage"></a>Storage

Der von Ihnen bereitgestellte Speicher definiert die Speicherkapazität, die für Ihren Azure Database for MariaDB-Server zur Verfügung steht. Der Speicher wird für die Datenbankdateien, temporären Dateien, Transaktionsprotokolle und MariaDB-Serverprotokolle verwendet. Außerdem wird durch die Gesamtmenge an bereitgestelltem Speicher die E/A-Kapazität Ihres Servers definiert.

|    | **Basic** | **Allgemeiner Zweck** | **Arbeitsspeicheroptimiert** |
|:---|:----------|:--------------------|:---------------------|
| Speichertyp | Azure-Standardspeicher | Azure Storage Premium | Azure Storage Premium |
| Speichergröße | 5 GB bis 1 TB | 5 GB bis 4 TB | 5 GB bis 4 TB |
| Speicherinkrementgröße | 1 GB | 1 GB | 1 GB |
| IOPS | Variable |3 IOPS/GB<br/>Min. 100 IOPS<br/>Max. 6.000 IOPS | 3 IOPS/GB<br/>Min. 100 IOPS<br/>Max. 6.000 IOPS |

Während und nach der Erstellung des Servers können Sie zusätzliche Speicherkapazität hinzufügen und dem System erlauben, den Speicher auf der Grundlage des Speicherbedarfs Ihrer Workload automatisch zu vergrößern. Der Tarif „Basic“ umfasst keine IOPS-Garantie. Für die Tarife „Allgemein“ und „Arbeitsspeicheroptimiert“ wird der IOPS-Wert gegenüber der bereitgestellten Speichergröße in einem Verhältnis von 3:1 skaliert.

Sie können Ihren E/A-Verbrauch im Azure-Portal oder mit Azure CLI-Befehlen überwachen. Die wichtigen zu überwachenden Metriken sind das [Speicherlimit, der Speicherprozentsatz, der genutzte Speicher und der E/A-Prozentsatz](concepts-monitoring.md).

### <a name="reaching-the-storage-limit"></a>Erreichen der Speicherbegrenzung

Server mit weniger als 100 GB bereitgestelltem Speicher werden als schreibgeschützt gekennzeichnet, wenn der freie Speicher weniger als 512 MB oder fünf Prozent der bereitgestellten Speichergröße beträgt. Server mit mehr als 100 GB bereitgestelltem Speicher werden als schreibgeschützt gekennzeichnet, wenn der freie Speicher weniger als 5 GB beträgt.

Wenn Sie also beispielsweise 110 GB Speicher bereitgestellt haben und die tatsächliche Auslastung 105 GB überschreitet, wird der Server als schreibgeschützt gekennzeichnet. Wenn Sie andererseits 5 GB Speicher bereitgestellt haben, wird der Server als schreibgeschützt gekennzeichnet, wenn der freie Speicher unter 512 MB sinkt.

Während der Dienst versucht, den Server als schreibgeschützt zu kennzeichnen, werden alle neuen Schreibtransaktionsanforderungen blockiert, und bestehende aktive Transaktionen werden weiterhin ausgeführt. Wenn der Server als schreibgeschützt festgelegt ist, führen alle nachfolgenden Schreibvorgänge und die Transaktionscommits zu einem Fehler. Leseabfragen werden weiterhin ununterbrochen fortgesetzt. Nachdem Sie den bereitgestellten Speicher erhöht haben, ist der Server für die erneute Annahme von Schreibtransaktionen bereit.

Sie sollten die automatische Speichervergrößerung aktivieren oder eine Benachrichtigung einrichten, damit Sie informiert werden, wenn sich der Serverspeicher dem Grenzwert nähert. So können Sie den schreibgeschützten Zustand vermeiden. Weitere Informationen finden Sie in der Dokumentation zum [Einrichten einer Benachrichtigung](howto-alert-metric.md).

### <a name="storage-auto-grow"></a>Automatische Speichervergrößerung

Wenn die automatische Speichervergrößerung aktiviert ist, wird der Speicher automatisch ohne Beeinträchtigung der Workload vergrößert. Bei Servern mit weniger als 100 GB bereitgestelltem Speicher wird die bereitgestellte Speichergröße um 5 GB erhöht, sobald der freie Speicher unter 1 GB oder zehn Prozent des bereitgestellten Speichers sinkt. Bei Servern mit mehr als 100 GB bereitgestelltem Speicher wird die bereitgestellte Speichergröße um fünf Prozent erhöht, sobald der freie Speicherplatz unter fünf Prozent der bereitgestellten Speichergröße sinkt. Dabei gelten die maximalen, oben beschriebenen Speichergrenzwerte.

Wenn Sie also beispielsweise 1.000 GB Speicher bereitgestellt haben und die tatsächliche Auslastung 950 GB überschreitet, wird die Speichergröße des Servers auf 1.050 GB erhöht. Bei 10 GB bereitgestelltem Speicher wird die Speichergröße alternativ auf 15 GB erhöht, wenn weniger als 1 GB Speicher frei ist.

## <a name="backup"></a>Backup

Der Dienst erstellt automatisch Sicherungen Ihres Servers. Die Mindestaufbewahrungsdauer für Sicherungen beträgt sieben Tage. Sie können eine Aufbewahrungsdauer von bis zu 35 Tagen festlegen. Die Aufbewahrungsdauer kann während der Lebensdauer des Servers jederzeit angepasst werden. Sie können zwischen lokal redundanten und georedundanten Sicherungen wählen. Georedundante Sicherungen werden auch in der [geografisch gepaarten Region](https://docs.microsoft.com/azure/best-practices-availability-paired-regions) gespeichert, die zur Erstellungsregion Ihres Servers gehört (Regionspaar). Diese Redundanz sorgt in einem Notfall für Schutz. Sie haben auch die Möglichkeit, Ihren Server in einer beliebigen anderen Azure-Region, in der der Dienst verfügbar ist, mit georedundanten Sicherungen wiederherzustellen. Es ist nicht möglich, zwischen den beiden Sicherungsspeicheroptionen zu wechseln, sobald der Server erstellt ist.

## <a name="scale-resources"></a>Skalieren von Ressourcen

Nachdem Sie Ihren Server erstellt haben, können Sie die virtuellen Kerne, den Tarif (mit Ausnahme eines Wechsels zu oder von Basic), die Speichermenge und den Aufbewahrungszeitraum für Sicherungen unabhängig voneinander ändern. Der Sicherungsspeichertyp kann nach der Servererstellung nicht mehr geändert werden. Die Anzahl virtueller Kerne kann zentral hoch- oder herunterskaliert werden. Die Aufbewahrungsdauer für Sicherungen kann von 7 bis zu 35 Tagen zentral hoch- oder herunterskaliert werden. Die Speichergröße kann nur erhöht werden. Die Skalierung der Ressourcen kann entweder über das Portal oder per Azure CLI durchgeführt werden. 

<!--For an example of scaling by using Azure CLI, see [Monitor and scale an Azure Database for MariaDB server by using Azure CLI](scripts/sample-scale-server.md).-->

Beim Ändern der Anzahl von virtuellen Kernen oder des Tarifs wird eine Kopie des ursprünglichen Servers mit der neuen Computezuteilung erstellt. Sobald der neue Server betriebsbereit ist und ausgeführt wird, werden die Verbindungen auf den neuen Server verschoben. Während des Moments, in dem das System den Wechsel zum neuen Server durchführt, können keine neuen Verbindungen hergestellt werden, und für alle Transaktionen ohne Commit erfolgt ein Rollback. Die Länge dieses Zeitfensters variiert, aber normalerweise dauert der Vorgang nicht länger als eine Minute.

Das Skalieren des Speichers und das Ändern der Aufbewahrungsdauer für Sicherungen sind reine Onlinevorgänge. Es gibt keine Ausfallzeit, und Ihre Anwendung wird nicht beeinträchtigt. Da der IOPS-Wert in Abhängigkeit der Größe des bereitgestellten Speichers skaliert wird, können Sie die IOPS-Menge, die für Ihren Server verfügbar ist, durch das zentrale Hochskalieren des Speichers erhöhen.

## <a name="pricing"></a>Preise

Aktuelle Preisinformationen finden Sie auf der Seite [Azure-Datenbank für MySQL – Preise](https://azure.microsoft.com/pricing/details/mariadb/). Informationen zu den Kosten der gewünschten Konfiguration können Sie im [Azure-Portal](https://portal.azure.com/#create/Microsoft.MariaDBServer) anzeigen. Die monatlichen Kosten für die von Ihnen ausgewählten Optionen werden auf der Registerkarte **Tarif** angegeben. Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie den Azure-Preisrechner verwenden, um einen geschätzten Preis zu erhalten. Wählen Sie auf der Website des [Azure-Preisrechners](https://azure.microsoft.com/pricing/calculator/) die Option **Elemente hinzufügen** aus, erweitern Sie die Kategorie **Datenbanken**, und wählen Sie **Azure Database for MariaDB** aus, um die Optionen anzupassen.

## <a name="next-steps"></a>Nächste Schritte
- Informieren Sie sich über die [Beschränkungen](concepts-limits.md).
- Informieren Sie sich, wie Sie [im Azure-Portal einen MariaDB-Server erstellen](quickstart-create-mariadb-server-database-using-azure-portal.md).

<!--
- Learn how to [monitor and scale an Azure Database for MariaDB server by using Azure CLI](scripts/sample-scale-server.md).-->
