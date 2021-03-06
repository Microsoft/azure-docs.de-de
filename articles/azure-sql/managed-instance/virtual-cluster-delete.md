---
title: Löschen eines Subnetzes nach dem Löschen von SQL Managed Instance
description: Hier erfahren Sie, wie Sie nach dem Löschen einer Instanz von Azure SQL Managed Instance auch ein virtuelles Azure-Netzwerk löschen.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: deployment-configuration
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: urosmil
ms.author: urmilano
ms.reviewer: mathoma
ms.date: 06/26/2019
ms.openlocfilehash: 6fa75e4fa081c40ebfd49119bca11a5cf9e5b88a
ms.sourcegitcommit: b11257b15f7f16ed01b9a78c471debb81c30f20c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/08/2021
ms.locfileid: "111591690"
---
# <a name="delete-a-subnet-after-deleting-an-azure-sql-managed-instance"></a>Löschen eines Subnetzes nach dem Löschen einer Instanz von Azure SQL Managed Instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Dieser Artikel enthält Richtlinien zum manuellen Löschen eines Subnetzes nach dem Löschen der letzten darin enthaltenen Instanz von Azure SQL Managed Instance.

SQL Managed Instances werden in [virtuellen Clustern](connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture) bereitgestellt. Jeder virtuelle Cluster ist einem Subnetz zugeordnet. Der virtuelle Cluster bleibt nach dem Löschen der letzten Instanz noch 12 Stunden erhalten, damit Sie im gleichen Subnetz schneller SQL Managed Instances erstellen können. Das Beibehalten eines leeren virtuellen Clusters ist kostenlos. Während dieses Zeitraums kann der mit dem Subnetz verbundene virtuelle Cluster nicht gelöscht werden.

Wenn Sie keine 12 Stunden warten, sondern den virtuellen Cluster sowie das Subnetz früher löschen möchten, können Sie dies manuell tun. Löschen Sie den virtuellen Cluster manuell über das Azure-Portal oder die API für virtuelle Cluster.

> [!IMPORTANT]
> - Damit der Löschvorgang erfolgreich ist, sollte der virtuelle Cluster keine SQL Managed Instances enthalten. 
> - Das Löschen eines virtuellen Clusters ist ein zeitintensiver Vorgang mit einer Dauer von etwa 1,5 Stunden. (Aktuelle Angaben zur Löschdauer für virtuelle Cluster finden Sie unter [SQL Managed Instance-Verwaltungsvorgänge](./sql-managed-instance-paas-overview.md#management-operations).) Während dieser Zeit ist der virtuelle Cluster im Portal weiterhin sichtbar, bis der Vorgang abgeschlossen wurde.

## <a name="delete-a-virtual-cluster-from-the-azure-portal"></a>Löschen eines virtuellen Clusters aus dem Azure-Portal

Suchen Sie nach den Ressourcen eines virtuellen Clusters, um diesen über das Azure-Portal zu löschen.

![Screenshot: Azure-Portal mit hervorgehobenem Suchfeld](./media/virtual-cluster-delete/virtual-clusters-search.png)

Nachdem Sie den virtuellen Cluster gefunden haben, den Sie löschen möchten, wählen Sie diese Ressource aus, und klicken Sie auf **Löschen**. Sie werden aufgefordert, das Löschen des virtuellen Clusters zu bestätigen.

![Screenshot: Dashboard „Virtuelle Cluster“ im Azure-Portal mit hervorgehobener Option „Löschen“](./media/virtual-cluster-delete/virtual-clusters-delete.png)

In den Azure-Portalbenachrichtigungen wird eine Bestätigung dafür angezeigt, dass die Anforderung zum Löschen des virtuellen Clusters erfolgreich übermittelt wurde. Der eigentliche Löschvorgang dauert etwa 1,5 Stunden. Während dieser Zeit ist der virtuelle Cluster im Portal weiterhin sichtbar. Nach Abschluss des Vorgangs ist der virtuelle Cluster nicht mehr sichtbar, und das zugehörige Subnetz wird zur erneuten Verwendung freigegeben.

> [!TIP]
> Wenn im virtuellen Cluster keine SQL Managed Instances angezeigt werden und Sie den virtuellen Cluster nicht löschen können, vergewissern Sie sich, dass kein Instanzenbereitstellungsvorgang mehr ausgeführt wird. Dies umfasst gestartete und abgebrochene Bereitstellungen, die noch nicht abgeschlossen sind. Der Grund: Diese Vorgänge verwenden weiterhin den virtuellen Cluster, was dazu führt, dass er nicht gelöscht werden kann. Auf der Registerkarte **Bereitstellungen** der Ressourcengruppe, in der die Instanz bereitgestellt wurde, werden Bereitstellungen angezeigt, die noch nicht abgeschlossen sind. Warten Sie in diesem Fall, bis die Bereitstellung abgeschlossen ist, und löschen Sie dann SQL Managed Instance und anschließend den virtuellen Cluster.

## <a name="delete-a-virtual-cluster-by-using-the-api"></a>Löschen eines virtuellen Clusters mithilfe der API

Verwenden Sie die in der [Methode zum Löschen virtueller Cluster](/rest/api/sql/virtualclusters/delete) angegebenen URI-Parameter, um einen virtuellen Cluster mithilfe der API zu löschen.

## <a name="next-steps"></a>Nächste Schritte

- Eine Übersicht finden Sie unter [Was ist Azure SQL Managed Instance?](sql-managed-instance-paas-overview.md).
- Machen Sie sich mit der [Verbindungsarchitektur in SQL Managed Instance](connectivity-architecture-overview.md) vertraut.
- Unter [Ändern eines vorhandenen virtuellen Netzwerks für SQL Managed Instance](vnet-existing-add-subnet.md) erfahren Sie, wie Sie Änderungen vornehmen können.
- Ein Tutorial zum Erstellen eines virtuellen Netzwerks, dem Erstellen von Azure SQL Managed Instance und dem Wiederherstellen einer Datenbank aus einer Datenbanksicherung finden Sie unter [Erstellen einer Instanz von Azure SQL Managed Instance (Portal)](instance-create-quickstart.md).
- Informationen zu Problemen im Zusammenhang mit dem DNS finden Sie unter [Konfigurieren eines benutzerdefinierten DNS](custom-dns-configure.md).