---
title: Anwenden von Empfehlungen zur Leistung für Azure SQL-Datenbank | Microsoft-Dokumentation
description: Im Azure-Portal erhalten Sie Empfehlungen zur Leistungsverbesserung, mit denen Sie die Leistung Ihrer Azure SQL-Datenbank optimieren können.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: jrasnik, carlrab
manager: craigg
ms.date: 12/19/2018
ms.openlocfilehash: d80581aae56fc9d65d6f24d21f2c582cb74b3f2d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61420302"
---
# <a name="find-and-apply-performance-recommendations"></a>Suchen und Anwenden von Empfehlungen zur Leistung

Sie können im Azure-Portal Empfehlungen zur Leistungsverbesserung erhalten, die die Leistung Ihrer Azure SQL-Datenbank verbessern können, oder Sie können in Ihrer Workload erkannte Probleme beheben. Mit der Seite **Empfehlungen zur Leistung** im Azure-Portal können Sie die besten Empfehlungen basierend auf deren möglichen Auswirkungen ermitteln. 

## <a name="viewing-recommendations"></a>Anzeigen von Empfehlungen

Zum Anzeigen und Anwenden von Empfehlungen zur Leistung benötigen Sie die richtigen Berechtigungen für die [rollenbasierte Zugriffssteuerung](../role-based-access-control/overview.md) in Azure. Die Berechtigungen **Leser** und **SQL-DB-Mitwirkender** sind erforderlich, um Empfehlungen anzuzeigen, und die Berechtigungen **Besitzer** und **SQL-DB-Mitwirkender** sind erforderlich, um Aktionen ausführen zu können, Indizes zu erstellen oder zu löschen und die Indexerstellung abzubrechen.

Verwenden Sie die folgenden Schritte, um Empfehlungen zur Leistung im Azure-Portal zu suchen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
2. Wechseln Sie zu **Alle Dienste** > **SQL-Datenbanken**, und wählen Sie Ihre Datenbank aus.
3. Navigieren Sie zu **Empfehlungen zur Leistung**, um die verfügbaren Empfehlungen für die ausgewählte Datenbank anzuzeigen.

Empfehlungen zur Leistung werden in einer Tabelle angezeigt, ähnlich wie in folgender Abbildung dargestellt:

![Empfehlungen](./media/sql-database-advisor-portal/recommendations.png)

Empfehlungen werden nach möglichen Auswirkungen auf die Leistung in die folgenden Kategorien unterteilt:

| Auswirkung | BESCHREIBUNG |
|:--- |:--- |
| Hoch |Empfehlungen für hohe Auswirkungen sollten den größten Einfluss auf die Leistung haben. |
| Mittel |Empfehlungen für mittlere Auswirkungen sollten die Leistung verbessern, jedoch nicht wesentlich. |
| Niedrig |Empfehlungen für geringe Auswirkungen sollten eine bessere Leistung bieten, die Verbesserungen sind möglicherweise jedoch nicht signifikant. |


> [!NOTE]
> Azure SQL-Datenbank muss Aktivitäten mindestens einen Tag lang überwachen, um einige Empfehlungen aufstellen zu können. Der Azure SQL-Datenbank kann leichter für konsistente Abfragemuster optimiert werden als für zufällige, unregelmäßige Aktivitätsspitzen. Wenn gerade keine Empfehlungen verfügbar sind, wird der Grund dafür auf der Seite **Empfehlungen zur Leistung** erläutert.
> 

Außerdem können Sie hier die Verlaufsdaten der Vorgänge sehen. Wählen Sie eine Empfehlung oder einen Status aus, um weitere Informationen anzuzeigen.

Hier sehen Sie die Empfehlung „Index erstellen“ im Azure-Portal.

![Erstellen des Index](./media/sql-database-advisor-portal/sql-database-performance-recommendation.png)

## <a name="applying-recommendations"></a>Anwenden von Empfehlungen
Azure SQL-Datenbank gibt Ihnen vollständige Kontrolle darüber, wie Empfehlungen umgesetzt werden. Dazu stehen Ihnen die folgenden drei Optionen zur Verfügung: 

* Aktivieren Sie einzelne Empfehlungen nacheinander.
* Aktivieren Sie die automatische Optimierung, um die Empfehlungen automatisch anzuwenden.
* Führen Sie das empfohlene T-SQL-Skript für Ihre Datenbank aus, um eine Empfehlung manuell zu implementieren.

Wählen Sie eine beliebige Empfehlung aus, um die zugehörigen Details anzuzeigen. Klicken Sie dann auf **Skript anzeigen**, um genaue Informationen dazu anzuzeigen, wie die Empfehlung erstellt wird.

Die Datenbank bleibt online, während die Empfehlung angewendet wird. Eine Empfehlungen zur Leistung bzw. die automatischen Optimierung führen nie dazu, dass eine Datenbank offline geschaltet wird.

### <a name="apply-an-individual-recommendation"></a>Anwenden einzelner Empfehlungen
Sie können Empfehlungen nacheinander anzeigen und akzeptieren.

1. Wählen Sie auf der Seite **Empfehlungen** eine Empfehlung aus.
2. Klicken Sie auf der Seite **Details** auf **Anwenden**.
   
    ![Anwenden einer Empfehlung](./media/sql-database-advisor-portal/apply.png)

Die ausgewählte Empfehlung wird auf die Datenbank angewendet.

### <a name="removing-recommendations-from-the-list"></a>Entfernen von Empfehlungen aus der Liste

Wenn die Liste der Empfehlungen Einträge enthält, die Sie aus der Liste entfernen möchten, können Sie die Empfehlung verwerfen:

1. Wählen Sie in der Liste **Empfehlungen** eine Empfehlung aus, um die Details zu öffnen.
2. Klicken Sie auf der Seite **Details** auf **Verwerfen**.

Falls gewünscht, können Sie verworfene Einträge wieder zur Liste **Empfehlungen** hinzufügen:

1. Klicken Sie auf der Seite **Empfehlungen** auf **Verworfene anzeigen**.
2. Wählen Sie einen verworfenen Eintrag aus der Liste, um dessen Details anzuzeigen.
3. Klicken Sie optional auf **"Verwerfen" rückgängig machen**, um den Eintrag wieder der Hauptliste der **Empfehlungen** hinzuzufügen.

> [!NOTE]
> Beachten Sie, dass bei Aktivierung von [Automatische Optimierung](sql-database-automatic-tuning.md) für die SQL-Datenbank eine Empfehlung nie automatisch angewendet wird, wenn Sie diese einmal manuell in der Liste verworfen haben. Das Verwerfen einer Empfehlung ist eine praktische Möglichkeit für Benutzer, die automatische Optimierung auch in Fällen aktiviert zu halten, in denen eine bestimmte Empfehlung nicht angewendet werden sollte.
> Sie können dieses Verhalten rückgängig machen, indem Sie mithilfe der Option „"Verwerfen"rückgängig machen“ verworfene Empfehlungen wieder der Liste der Empfehlungen hinzufügen.
> 

### <a name="enable-automatic-tuning"></a>Aktivieren der automatischen Optimierung
Sie können den Azure SQL-Datenbank so konfigurieren, dass Empfehlungen automatisch implementiert werden. Sobald Empfehlungen zur Verfügung stehen, werden sie automatisch angewendet. Wie bei allen vom Dienst verwalteten Empfehlungen wird eine Empfehlung rückgängig gemacht, wenn sie sich negativ auf die Leistung auswirkt.

1. Klicken Sie auf der Seite **Empfehlungen** auf **Automatisieren**:
   
    ![Advisor-Einstellungen](./media/sql-database-advisor-portal/settings.png)
2. Zu automatisierende Aktionen auswählen:
   
    ![Empfohlene Indizes](./media/sql-database-automatic-tuning-enable/server.png)

> [!NOTE]
> Beachten Sie, dass die Option **DROP_INDEX** derzeit nicht kompatibel mit Anwendungen ist, die Partitionswechsel und Indexhinweise verwenden. 
>

Klicken Sie nach der Auswahl der gewünschten Konfiguration auf „Übernehmen“.

### <a name="manually-apply-recommendations-through-t-sql"></a>Manuelles Anwenden von Empfehlungen über T-SQL

Wählen Sie eine beliebige Empfehlung aus, und klicken Sie auf **Skript anzeigen**. Führen Sie dieses Skript für Ihre Datenbank aus, um die Empfehlung manuell anzuwenden.

*Indizes, die manuell erstellt wurden, werden nicht durch den Dienst überwacht und auf ihre tatsächlichen Auswirkungen auf die Leistung überprüft*. Es empfiehlt sich daher, diese Indizes nach der Erstellung zu überwachen. So können Sie sicherstellen, dass sie Leistungssteigerungen bieten, und sie gegebenenfalls anpassen oder löschen. Ausführliche Informationen zum Erstellen von Indizes finden Sie unter [CREATE INDEX (Transact-SQL)](https://msdn.microsoft.com/library/ms188783.aspx). Darüber hinaus bleiben manuell angewandte Empfehlungen aktiv und werden 24 bis 48 Stunden lang in der Liste der Empfehlungen angezeigt. Danach zieht sie das System automatisch zurück. Wenn Sie eine Empfehlung früher entfernen möchten, können Sie sie manuell verwerfen.

### <a name="canceling-recommendations"></a>Abbrechen von Empfehlungen

Empfehlungen, die den Status **Ausstehend**, **Wird geprüft** oder **Erfolg** aufweisen, können verworfen werden. Empfehlungen mit dem Status **Wird ausgeführt** können nicht abgebrochen werden.

1. Wählen Sie im Bereich **Optimierungsverlauf** eine Empfehlung aus, um die Seite **Empfehlungsdetails** zu öffnen.
2. Klicken Sie auf **Abbrechen** , um den Vorgang zum Anwenden der Empfehlung abzubrechen.

## <a name="monitoring-operations"></a>Überwachen von Vorgängen

Eine Empfehlung wird möglicherweise nicht umgehend angewendet. Im Portal finden Sie ausführliche Informationen zum Status der Empfehlungen. Indizes können die folgenden Zustände aufweisen:

| Status | BESCHREIBUNG |
|:--- |:--- |
| Ausstehend |Der Befehl zum Anwenden der Empfehlung wurde empfangen und ist für die Ausführung geplant. |
| Wird ausgeführt |Die Empfehlung wird angewendet. |
| Die Überprüfen erfolgt. |Die Empfehlung wurde erfolgreich angewendet, und der Dienst berechnet die Vorteile. |
| Erfolgreich |Die Empfehlung wurde erfolgreich angewendet, und die Vorteile wurden berechnet. |
| Error |Beim Vorgang zum Anwenden der Empfehlung ist ein Fehler aufgetreten. Dies kann ein vorübergehendes Problem sein, oder es handelt sich möglicherweise um eine Schemaänderung an der Tabelle, und das Skript ist nicht mehr gültig. |
| Wird zurückgesetzt |Die Empfehlung wurde angewendet, wird jedoch als nicht leistungsfähig erachtet und automatisch zurückgesetzt. |
| Zurückgesetzt |Die Empfehlung wurde zurückgesetzt. |

Klicken Sie auf eine in Bearbeitung befindliche Empfehlung in der Liste, um weitere Informationen anzuzeigen:

![Empfohlene Indizes](./media/sql-database-advisor-portal/operations.png)

### <a name="reverting-a-recommendation"></a>Zurücksetzen einer Empfehlung
Wenn Sie die Empfehlungen zur Leistung verwendet haben, um die Empfehlung anzuwenden (also nicht das T-SQL-Skript manuell ausgeführt haben), wird die Empfehlung automatisch rückgängig gemacht, wenn die Änderung sich negativ auf die Leistung auswirkt. Wenn Sie eine Empfehlung aus irgendeinem Grund einfach zurücksetzen möchten, können Sie folgendermaßen vorgehen:

1. Wählen Sie eine erfolgreich angewendete Empfehlung im Bereich **Optimierungsverlauf** aus.
2. Klicken Sie auf der Seite **Details zur Empfehlung** auf **Zurücksetzen**.

![Empfohlene Indizes](./media/sql-database-advisor-portal/details.png)

## <a name="monitoring-performance-impact-of-index-recommendations"></a>Überwachen der Auswirkung von Indexempfehlungen auf die Leistung
Nachdem Empfehlungen erfolgreich implementiert wurden (zurzeit nur Empfehlungen für Indexvorgänge und zum Parametrisieren von Abfragen), können Sie auf der Seite mit den Details zur Empfehlung auf **Details abfragen** klicken, um [Query Performance Insights](sql-database-query-performance.md) zu öffnen und die Auswirkungen Ihrer häufigsten Abfragen auf die Leistung anzuzeigen.

![Überwachen der Auswirkung auf die Leistung](./media/sql-database-advisor-portal/query-insights.png)

## <a name="summary"></a>Zusammenfassung
Azure SQL-Datenbank bietet Empfehlungen zur Leistungsverbesserung für die SQL-Datenbank. Durch das Bereitstellen von T-SQL-Skripts erhalten Sie wertvolle Unterstützung bei der Optimierung Ihrer Datenbank und damit Ihrer Abfrageleistung.

## <a name="next-steps"></a>Nächste Schritte
Überwachen Sie Ihre Empfehlungen, und wenden Sie sie weiterhin an, um die Leistung zu optimieren. Datenbankworkloads sind dynamisch und ändern sich ständig. Azure SQL-Datenbank setzt die Überwachung fort und bietet Empfehlungen, mit denen sich die Leistung Ihrer Datenbank verbessern lässt. 

* Weitere Informationen zur automatischen Optimierung von Azure SQL-Datenbank finden Sie im Artikel zur [Automatischen Optimierung](sql-database-automatic-tuning.md).
* Einen Überblick der Empfehlungen zur Leistung von Azure SQL-Datenbank finden Sie im Artikel zu [Empfehlungen zur Leistung](sql-database-advisor.md).
* Unter [Query Performance Insight](sql-database-query-performance.md) erfahren Sie, wie Sie die Auswirkungen Ihrer wichtigsten Abfragen auf die Leistung untersuchen können.

## <a name="additional-resources"></a>Zusätzliche Ressourcen
* [Abfragespeicher](https://msdn.microsoft.com/library/dn817826.aspx)
* [CREATE INDEX](https://msdn.microsoft.com/library/ms188783.aspx)
* [Rollenbasierte Zugriffssteuerung](../role-based-access-control/overview.md)

