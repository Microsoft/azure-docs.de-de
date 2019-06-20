---
title: Übersicht über die Geschäftskontinuität mit Azure Database for MySQL
description: Übersicht über die Geschäftskontinuität mit Azure Database for MySQL
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: a09c1934ecb34518b191a8e730a72efecc85aa2f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60525432"
---
# <a name="overview-of-business-continuity-with-azure-database-for-mysql"></a>Übersicht über die Geschäftskontinuität mit Azure Database for MySQL

Diese Übersicht beschreibt die Funktionen, die Azure Database for MySQL für Geschäftskontinuität und Notfallwiederherstellung bereitstellt. Erfahren Sie etwas über Optionen für die Wiederherstellung nach Störungen, die Datenverluste nach sich ziehen oder dazu führen können, dass Ihre Datenbank und Ihre Anwendungen nicht mehr verfügbar sind. Erfahren Sie, was zu tun ist, wenn ein Benutzer- oder Anwendungsfehler die Datenintegrität gefährdet, eine Azure-Region ausfällt oder Wartungsaufgaben für Ihre Anwendung ausgeführt werden müssen.

## <a name="features-that-you-can-use-to-provide-business-continuity"></a>Features zum Sicherstellen der Geschäftskontinuität

Azure Database for MySQL bietet Features für Geschäftskontinuität, die automatisierte Sicherungen umfassen und Benutzern die Möglichkeit geben, eine Geowiederherstellung zu initiieren. Jedes Feature weist unterschiedliche Eigenschaften für die geschätzte Wiederherstellungszeit (Estimated Recovery Time, ERT) sowie für mögliche Datenverluste auf. Wenn Sie diese Optionen kennen, können Sie die richtigen Optionen auswählen und in unterschiedlichen Szenarien auch miteinander kombinieren. Wenn Sie Ihren Plan für die Geschäftskontinuität entwickeln, müssen Sie ermitteln, wie viel Zeit maximal vergehen darf, bis die Anwendung nach einer Störung vollständig wiederhergestellt ist – diese Zeitspanne ist Ihre RTO (Recovery Time Objective). Sie müssen auch herausfinden, wie viele kürzlich durchgeführte Datenupdates (in einem bestimmten Zeitraum) verloren gehen dürfen, wenn die Anwendung nach einer Störung wiederhergestellt wird – diese Zeitspanne ist Ihre RPO (Recovery Point Objective).

Die folgende Tabelle vergleicht ERT und RPO für die verfügbaren Features:

| **Funktion** | **Basic** | **Allgemeiner Zweck** | **Arbeitsspeicheroptimiert** |
| :------------: | :-------: | :-----------------: | :------------------: |
| Point-in-Time-Wiederherstellung von Sicherung | Beliebiger Wiederherstellungspunkt innerhalb der Aufbewahrungsdauer | Beliebiger Wiederherstellungspunkt innerhalb der Aufbewahrungsdauer | Beliebiger Wiederherstellungspunkt innerhalb der Aufbewahrungsdauer |
| Geowiederherstellung von georeplizierten Sicherungen | Nicht unterstützt | ERT < 12 Stunden<br/>RPO < 1 Stunde | ERT < 12 Stunden<br/>RPO < 1 Stunde |

> [!IMPORTANT]
> Gelöschte Server **können nicht** wiederhergestellt werden. Wenn Sie den Server löschen, werden auch alle Datenbanken gelöscht, die zum Server gehören, und können nicht wiederhergestellt werden.

## <a name="recover-a-server-after-a-user-or-application-error"></a>Wiederherstellen eines Servers nach einem Benutzer- oder Anwendungsfehler

Sie können die Sicherungen des Diensts verwenden, um einen Server nach verschiedenen Störungen wiederherzustellen. Es kann passieren, dass ein Benutzer versehentlich Daten, eine wichtige Tabelle oder sogar eine ganze Datenbank löscht. Es kann auch vorkommen, dass eine Anwendung aufgrund eines Anwendungsfehlers unbeabsichtigt gültige Daten mit ungültigen Daten überschreibt usw.

Sie können eine Point-in-Time-Wiederherstellung ausführen, um eine Kopie Ihres Servers zu einem als fehlerfrei bekannten Zeitpunkt zu erstellen. Dieser Zeitpunkt muss innerhalb der Aufbewahrungszeit für Sicherungen liegen, die Sie für den Server konfiguriert haben. Nach der Wiederherstellung der Daten auf dem neuen Server können Sie entweder den ursprünglichen Server durch den wiederhergestellten Server ersetzen oder die benötigten Daten vom wiederhergestellten Server auf den ursprünglichen Server kopieren.

## <a name="recover-from-an-azure-regional-data-center-outage"></a>Wiederherstellen nach einem Ausfall des regionalen Azure-Rechenzentrums

Es kommt zwar sehr selten vor, aber es ist möglich, dass ein Azure-Rechenzentrum ausfällt. Ein Ausfall kann den Geschäftsbetrieb einige wenige Minuten oder auch mehrere Stunden unterbrechen.

Eine Möglichkeit ist, einfach zu warten, bis der Server wieder online ist, wenn der Rechenzentrumsausfall behoben wurde. Dies funktioniert bei Anwendungen, bei denen der Server für eine längere Zeit offline sein kann, z.B. in einer Entwicklungsumgebung. Wenn ein Rechenzentrum ausfällt, wissen Sie nicht, wie lange der Ausfall dauern kann, daher ist diese Option nur dann in Erwägung zu ziehen, wenn Sie Ihren Server eine Zeit lang nicht benötigen.

Die andere Option ist die Verwendung des Geowiederherstellungsfeatures von Azure Database for MySQL, das den Server mithilfe von georedundanten Sicherungen wiederherstellt. Auf diese Sicherungen kann selbst dann zugegriffen werden, wenn die Region, in der Ihr Server gehostet wird, offline ist. Sie können eine Wiederherstellung aus diesen Sicherungen in eine andere Region ausführen und den Server wieder online schalten.

> [!IMPORTANT]
> Die Geowiederherstellung ist nur möglich, wenn Sie den Server mit einem georedundanten Sicherungsspeicher bereitgestellt haben. Wenn Sie für einen vorhandenen Server von lokal redundanten zu georedundanten Sicherungen wechseln möchten, müssen Sie mit „mysqldump“ ein Speicherabbild Ihres vorhandenen Servers erstellen und dieses auf einem neu erstellten Server wiederherstellen, der mit georedundanten Sicherungen konfiguriert ist.

## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu [automatisierten Sicherungen in Azure Database for MySQL](concepts-backup.md).
- Erfahren Sie, wie Sie eine Wiederherstellung mithilfe des [Azure-Portals](howto-restore-server-portal.md) oder der [Azure CLI](howto-restore-server-cli.md) ausführen.
- Weitere Informationen zu [Lesereplikaten in Azure Database for MySQL](concepts-read-replicas.md).