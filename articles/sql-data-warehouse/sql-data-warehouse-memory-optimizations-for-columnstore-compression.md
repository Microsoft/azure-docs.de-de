---
title: Verbessern der Columnstore-Indexleistung in Azure SQL Data Warehouse | Microsoft-Dokumentation
description: Reduzieren Sie Arbeitsspeicheranforderungen, oder erhöhen Sie den verfügbaren Arbeitsspeicher, um die Anzahl von Zeilen zu maximieren, die ein Columnstore-Index in jede Zeilengruppe komprimieren kann.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: load data
ms.date: 03/22/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 39d0fe06ee0e0230411024833cac7c88308f86c7
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66225384"
---
# <a name="maximizing-rowgroup-quality-for-columnstore"></a>Maximieren der Zeilengruppenqualität für Columnstore

Die Zeilengruppenqualität hängt von der Anzahl von Zeilen in einer Zeilengruppe ab. Mit einer Erhöhung des verfügbaren Arbeitsspeicher können Sie die Anzahl von Zeilen maximieren, die ein Columnstore-Index in jede Zeilengruppe komprimieren kann.  Verwenden Sie diese Methoden, um die Komprimierungsrate und Abfrageleistung für Columnstore-Indizes zu verbessern.

## <a name="why-the-rowgroup-size-matters"></a>Bedeutung der Größe der Zeilengruppe
Da ein Columnstore-Index eine Tabelle durch die Überprüfung der einzelnen Spaltensegmente von einzelnen Zeilengruppen überprüft, verbessert das Maximieren der Zeilenanzahl in jeder Zeilengruppe die Abfrageleistung. Wenn Zeilengruppen über eine hohe Anzahl von Reihen verfügen, verbessert sich die Datenkomprimierung, d.h., es werden weniger Daten vom Datenträger gelesen.

Weitere Informationen über Zeilengruppen, finden Sie unter [Beschreibung von Columnstore-Indizez](https://msdn.microsoft.com/library/gg492088.aspx).

## <a name="target-size-for-rowgroups"></a>Zielgröße für Zeilengruppen
Für eine optimale Abfrageleistung besteht das Ziel darin, die Anzahl der Zeilen pro Zeilengruppe in einem Columnstore-Index zu maximieren. Eine Zeilengruppe kann bis zu 1.048.576 Zeilen enthalten. Es ist in Ordnung, nicht die maximale Anzahl von Zeilen pro Zeilengruppe zu verwenden. Columnstore-Indizes erzielen gute Leistung, wenn Zeilengruppen mindestens 100.000 Zeilen aufweisen.

## <a name="rowgroups-can-get-trimmed-during-compression"></a>Zeilengruppen können während der Komprimierung gekürzt werden

Während des Massenladens oder einer Neuerstellung des Columnstore-Indizes ist manchmal nicht genug Arbeitsspeicher verfügbar, um alle Zeilen in die vorgesehenen Zeilengruppen zu komprimieren. Im Fall einer Arbeitsspeicherauslastung kürzen Columnstore-Indizes die Zeilengruppengröße, sodass die Komprimierung im Columnstore erfolgen kann. 

Reicht der Arbeitsspeicher nicht aus, um mindestens 10.000 Reihen in jede Zeilengruppe zu komprimieren, erzeugt SQL Data Warehouse einen Fehler.

Weitere Informationen zum Massenladen, finden Sie unter [Bulk load into a clustered columnstore index (Massenladen in einen gruppierten Columnstore-Index)](https://msdn.microsoft.com/library/dn935008.aspx#Bulk ).

## <a name="how-to-monitor-rowgroup-quality"></a>Überwachen der Zeilengruppenqualität

Die DMV sys.dm_pdw_nodes_db_column_store_row_group_physical_stats ([sys.dm_db_column_store_row_group_physical_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-column-store-row-group-physical-stats-transact-sql) enthält die Ansichtsdefinition, die SQL bis SQL Data Warehouse entspricht), die nützliche Informationen wie die Anzahl der Zeilen in Zeilengruppen und ggf. den Grund für eine Kürzung verfügbar macht. Sie können die folgende Sicht als eine praktische Möglichkeit zum Abfragen dieser DMV erstellen, um Informationen zur Kürzung von Zeilengruppen abzurufen.

```sql
create view dbo.vCS_rg_physical_stats
as 
with cte
as
(
select   tb.[name]                    AS [logical_table_name]
,        rg.[row_group_id]            AS [row_group_id]
,        rg.[state]                   AS [state]
,        rg.[state_desc]              AS [state_desc]
,        rg.[total_rows]              AS [total_rows]
,        rg.[trim_reason_desc]        AS trim_reason_desc
,        mp.[physical_name]           AS physical_name
FROM    sys.[schemas] sm
JOIN    sys.[tables] tb               ON  sm.[schema_id]          = tb.[schema_id]                             
JOIN    sys.[pdw_table_mappings] mp   ON  tb.[object_id]          = mp.[object_id]
JOIN    sys.[pdw_nodes_tables] nt     ON  nt.[name]               = mp.[physical_name]
JOIN    sys.[dm_pdw_nodes_db_column_store_row_group_physical_stats] rg      ON  rg.[object_id]     = nt.[object_id]
                                                                            AND rg.[pdw_node_id]   = nt.[pdw_node_id]
                                        AND rg.[distribution_id]    = nt.[distribution_id]                                          
)
select *
from cte;
```

trim_reason_desc enthält Informationen darüber, ob die Zeilengruppe gekürzt wurde (trim_reason_desc = NO_TRIM bedeutet, dass keine Kürzung erfolgt ist und die Qualität der Zeilengruppe optimal ist). Die folgenden Begründungen für eine Kürzung geben eine vorzeitige Kürzung der Zeilengruppe an:
- BULKLOAD: Dieser Grund für eine Kürzung wird verwendet, wenn der eingehende Zeilenbatch für den Ladevorgang weniger als 1 Million Zeilen umfasst hat. Die Datenbank-Engine erstellt komprimierte Zeilengruppen, wenn mehr als 100.000 Zeilen eingefügt werden (statt sie im Deltaspeicher einzufügen), der Grund für die Kürzung wird jedoch auf BULKLOAD festgelegt. In diesem Szenario soll der Batchladevorgang so erhöht werden, dass mehr Zeilen einbezogen werden. Außerdem wird das Partitionsschema überprüft, um sicherzustellen, dass es nicht zu kleinteilig ist, da Zeilengruppen nicht über Partitionsgrenzen hinausgehen können.
- MEMORY_LIMITATION: Zum Erstellen von Zeilengruppen mit 1 Million Zeilen ist für die Datenbank-Engine eine bestimmte Arbeitsspeichergröße erforderlich. Wenn der verfügbare Speicher für die Ladesitzung kleiner als der erforderliche Arbeitsspeicher ist, werden Zeilengruppen vorzeitig gekürzt. In den folgenden Abschnitten wird erläutert, wie Sie den erforderlichen Speicher schätzen und mehr Speicher zuweisen.
- DICTIONARY_SIZE: Dieser Grund für eine Kürzung gibt an, dass die Zeilengruppenkürzung aufgetreten ist, weil mindestens eine Zeichenfolgenspalte mit breiten Zeichenfolgen und/oder Zeichenfolgen mit hoher Statusanzahl vorhanden war. Die Wörterbuchgröße ist auf 16 MB Arbeitsspeicher beschränkt. Sobald diese Beschränkung erreicht ist, wird die Zeilengruppe komprimiert. In diesem Fall sollten Sie die problematische Spalte in einer separaten Tabelle isolieren.

## <a name="how-to-estimate-memory-requirements"></a>Einschätzen der Arbeitsspeicheranforderungen

<!--
To view an estimate of the memory requirements to compress a rowgroup of maximum size into a columnstore index, download and run the view [dbo.vCS_mon_mem_grant](). This view shows the size of the memory grant that a rowgroup requires for compression in to the columnstore.
-->

Der maximale erforderliche Arbeitsspeicher zum Komprimieren einer Zeilengruppe beträgt ungefähr

- 72 MB +
- \#Zeilen \*\#Spalten \* 8 Byte +
- \#Zeilen \*\#kurze Zeichenfolgenspalten \* 32 Bytes +
- \#lange Zeichenfolgenspalten \* 16 MB für das Komprimierungswörterbuch

wo kurze Zeichenfolgenspalten Zeichenfolgendatentypen mit <= 32 Bytes und lange Zeichenfolgenspalten Zeichenfolgendatentypen mit > 32 Bytes verwenden.

Lange Zeichenfolgen werden mit einer Komprimierungsmethode komprimiert, die für das Komprimieren von Texten entwickelt wurde. Diese Komprimierungsmethode verwendet ein *Wörterbuch* zum Speichern von Textmustern. Die maximale Größe eines Wörterbuchs beträgt 16 MB. Es gibt nur ein Wörterbuch für jede lange Zeichenfolgenspalte in der Zeilengruppe.

Eine ausführliche Diskussion der Columnstore-Arbeitsspeicheranforderungen, finden Sie im Video [Azure SQL Data Warehouse scaling: configuration and guidance (Skalieren von Azure SQL Data Warehouse: Konfiguration und Anleitungen, in englischer Sprache)](https://channel9.msdn.com/Events/Ignite/2016/BRK3291).

## <a name="ways-to-reduce-memory-requirements"></a>Verfahren zum Verringern der Arbeitsspeicheranforderungen

Befolgen Sie die folgenden Vorgehensweisen, um die Arbeitsspeicheranforderungen für die Komprimierung von Zeilengruppen in den Columnstore-Indizes zu reduzieren.

### <a name="use-fewer-columns"></a>Verwenden von weniger Spalten
Entwerfen Sie, wenn möglich, die Tabelle mit weniger Spalten. Wenn eine Zeilengruppe in den Columnstore komprimiert wird, komprimiert der Columnstore-Index jedes Spaltensegment getrennt. Deshalb erhöhen sich die Arbeitsspeicheranforderungen zur Erhöhung einer Zeilengruppe mit der Anzahl von Spalten.


### <a name="use-fewer-string-columns"></a>Verwenden von weniger Zeichenfolgenspalten
Spalten von Zeichenfolgendatentypen benötigen mehr Arbeitsspeicher als nummerische und Datumsdatentypen. Erwägen Sie, Zeichenfolgenspalten aus Faktentabellen zu entfernen und in kleinere Dimensionstabellen zu setzen, um Arbeitsspeicheranforderungen zu reduzieren.

Zusätzliche Arbeitsspeicheranforderungen für die Komprimierung von Zeichenfolgen:

- Zeichenfolgendatentypen von bis zu 32 Zeichen können 32 zusätzliche Bytes pro Wert voraussetzen.
- Zeichenfolgendatentypen mit mehr als 32 Zeichen werden mithilfe von Wörterbuchmethoden komprimiert.  Jede Spalte in der Zeilengruppe kann bis zu zusätzlich 16 MB benötigen, um das Wörterbuch zu erstellen.

### <a name="avoid-over-partitioning"></a>Vermeiden von übermäßige Partitionierung

Columnstore-Indizes erstellen eine oder mehrere Zeilengruppen pro Partition. In SQL Data Warehouse steigt die Anzahl von Partitionen schnell, da die Daten verteilt werden und jede Verteilung partitioniert ist. Wenn die Tabelle zu viele Partitionen besitzt, gibt es möglicherweise nicht genügend Zeilen, um die Zeilengruppen zu füllen. Das Fehlen von Zeilen erzeugt keine Arbeitsspeicherauslastung während der Komprimierung, führt aber zu Zeilengruppen, die nicht die beste Columnstore-Abfrageleistung erreichen.

Ein weiterer Grund, übermäßige Partitionierung zu vermeiden, ist, dass es eine Arbeitsspeicherauslastung beim Laden von Zeilen in den Columnstore-Index in einer partitionierten Tabelle gibt. Während einer Auslastung können viele Partitionen die eingehenden Zeilen aufnehmen, die im Arbeitsspeicher behalten werden, bis jede Partition genug Zeilen komprimiert hat. Zu viele Partitionen erzeugen zusätzliche Arbeitsspeicherauslastung.

### <a name="simplify-the-load-query"></a>Vereinfachen der Ladeabfrage

Die Datenbank teilt die Arbeitsspeicherzuweisung mit allen Operatoren in der Abfrage. Wenn eine Ladeanfrage komplexe Sortierungen und Verknüpfungen hat, reduziert sich dadurch der Arbeitsspeicher, der für die Komprimierung verfügbar ist.

Entwickeln Sie die Ladeabfrage nur zum Laden von Abfragen Wenn Sie Transformationen an der Datei ausführen müssen, führen Sie sie getrennt von der Ladeabfrage aus. Staffeln Sie die Datei, z.B. in einem Heap, führen sie die Transformationen aus, und laden Sie anschließend die Stagingtabelle in den Columnstore-Index. Sie können die Datei auch zuerst laden und dann das MPP-System zum Transformieren der Datei verwenden.

### <a name="adjust-maxdop"></a>Anpassen von MAXDOP

Jede Verteilung komprimiert Zeilengruppen parallel in den Columnstore, sobald ein oder mehrere CPU-Kerne pro Verteilung verfügbar sind. Die Parallelität erfordert zusätzliche Arbeitsspeicherressourcen, was zur Arbeitsspeicherauslastung und Kürzung der Zeilengruppe führen kann.

Sie können den Tipp für MAXDOP-Anfrage anwenden, damit der Ladevorgang gezwungen wird, innerhalb jeder Verteilung im Serienmodus zu arbeiten, um Arbeitsspeicherauslastung zu verringern.

```sql
CREATE TABLE MyFactSalesQuota
WITH (DISTRIBUTION = ROUND_ROBIN)
AS SELECT * FROM FactSalesQuota
OPTION (MAXDOP 1);
```

## <a name="ways-to-allocate-more-memory"></a>Möglichkeiten, um mehr Arbeitsspeicher zu belegen

Die DWU-Größe und die Benutzer-Ressourcenklasse bestimmen zusammen, wie viel Arbeitsspeicher für einen Benutzer zur Verfügung steht. Um die Arbeitsspeicherzuweisung für eine Ladeanfrage zu erhöhen, können Sie entweder die Anzahl der DWUs oder die Ressourcenklasse erhöhen.

- Wie Sie die DWUs erhöhen, erfahren Sie unter [Wie skaliere ich Leistung?](quickstart-scale-compute-portal.md)
- Wie Sie die Ressourcenklasse für eine Abfrage ändern, erfahren Sie unter [Beispiel: Ändern der Ressourcenklasse eines Benutzers](resource-classes-for-workload-management.md#change-a-users-resource-class).

## <a name="next-steps"></a>Nächste Schritte

Weitere Möglichkeiten zum Verbessern der Leistung in SQL Data Warehouse finden Sie unter der [Übersicht über die Leistung](sql-data-warehouse-overview-manage-user-queries.md).

