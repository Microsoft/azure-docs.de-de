---
title: Integration der Quellcodeverwaltung
description: Professionelle DevOps-Datenbankumgebung für den dedizierten SQL-Pool mit nativer Integration der Quellcodeverwaltung mithilfe von Azure Repos (Git und GitHub)
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql-dw
ms.date: 08/23/2019
ms.author: jrasnick
ms.reviewer: igorstan
ms.openlocfilehash: 53adbc0288c28c3b18632539c3f812bbca82da92
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566153"
---
# <a name="source-control-integration-for-dedicated-sql-pool-in-azure-synapse-analytics"></a>Integration der Quellcodeverwaltung für den dedizierten SQL-Pool in Azure Synapse Analytics

In diesem Tutorial wird erläutert, wie Sie Ihr SSDT-Datenbankprojekt (SQL Server Data Tools) mit Quellcodeverwaltung integrieren.  Die Integration der Quellcodeverwaltung ist der erste Schritt bei der Erstellung Ihrer Continuous Integration- und Continuous Deployment-Pipeline mit der Ressource des dedizierten SQL-Pools in Azure Synapse Analytics.

## <a name="before-you-begin"></a>Voraussetzungen

- Registrieren für eine [Azure DevOps-Organisation](https://azure.microsoft.com/services/devops/)
- Absolvieren des Tutorials [Schnellstart: Erstellen und Abfragen einer Azure SQL Data Warehouse-Instanz im Azure-Portal](create-data-warehouse-portal.md)
- [Installieren von Visual Studio 2019](https://visualstudio.microsoft.com/vs/older-downloads/)

## <a name="set-up-and-connect-to-azure-devops"></a>Einrichten von Azure DevOps und Herstellen einer Verbindung

1. Erstellen Sie in Ihrer Azure DevOps-Organisation ein Projekt, von dem Ihr SSDT-Datenbankprojekt über ein Azure-Repository gehostet wird.

   ![Erstellen des Projekts](./media/sql-data-warehouse-source-control-integration/1-create-project-azure-devops.png "Projekt erstellen")

2. Öffnen Sie Visual Studio, und stellen Sie mit Ihrer Azure DevOps-Organisation und Ihrem Projekt aus Schritt 1 eine Verbindung her, indem Sie **Verbindung verwalten** auswählen.

   ![Verwalten von Verbindungen](./media/sql-data-warehouse-source-control-integration/2-manage-connections.png "Verwalten von Verbindungen")

3. Stellen Sie eine Verbindung mit Ihrem Projekt her, indem Sie **Verbindungen verwalten** und dann **Verbindung mit einem Projekt herstellen** auswählen.
 
    ![Connect1](./media/sql-data-warehouse-source-control-integration/3-connect-project.png "Verbinden")


4. Suchen Sie das Projekt, das Sie in Schritt 1 erstellt haben, und wählen Sie **Verbinden** aus.
 
    ![Connect2](./media/sql-data-warehouse-source-control-integration/3.5-connect.png "Verbinden")


3. Klonen Sie das Azure-DevOps-Repository aus Ihrem Projekt auf dem lokalen Computer.

   ![Klonen des Repositorys](./media/sql-data-warehouse-source-control-integration/4-clone-repo.png "Repository klonen")

Weitere Informationen zum Verbinden von Projekten mithilfe von Visual Studio finden Sie im Thema zum [Herstellen einer Verbindung mit Projekten im Team Explorer](/visualstudio/ide/connect-team-project?view=vs-2019&preserve-view=true). Anleitungen zum Klonen eines Repositorys mithilfe von Visual Studio finden Sie im Artikel [Klonen eines vorhandenen Git-Repositorys](/azure/devops/repos/git/clone?tabs=visual-studio). 

## <a name="create-and-connect-your-project"></a>Erstellen und Verbinden Ihres Projekts

1. Erstellen Sie in Visual Studio ein neues SQL Server-Datenbankprojekt mit einem Verzeichnis und einem lokalen Git-Repository in Ihrem **lokalen geklonten Repository**.

   ![Erstellen eines neuen Projekts](./media/sql-data-warehouse-source-control-integration/5-create-new-project.png "Erstellen eines neuen Projekts")  

2. Klicken Sie mit der rechten Maustaste auf das leere SQL-Projekt, und importieren Sie Ihr Data Warehouse in das Datenbankprojekt.

   ![Importieren des Projekts](./media/sql-data-warehouse-source-control-integration/6-import-new-project.png "Importieren des Projekts")  

3. Committen Sie im Team Explorer in Visual Studio Ihre Änderungen im lokalen Git-Repository.

   ![Commit](./media/sql-data-warehouse-source-control-integration/6.5-commit-push-changes.png "Commit")  

4. Nachdem Sie die Änderungen lokal im geklonten Repository committet haben, synchronisieren Sie die Änderungen, und pushen Sie sie in das Azure-Repository in Ihrem Azure DevOps-Projekt.

   ![Synchronisieren und Pushen: Staging](./media/sql-data-warehouse-source-control-integration/7-commit-push-changes.png "Synchronisieren und Pushen: Staging")

   ![Synchronisieren und Pushen](./media/sql-data-warehouse-source-control-integration/7.5-commit-push-changes.png "Synchronisieren und Pushen")  

## <a name="validation"></a>Überprüfen

1. Überprüfen Sie, ob die Änderungen in das Azure-Repository gepusht wurden, indem Sie eine Tabellenspalte in Ihrem Datenbankprojekt über Visual Studio SQL Server Data Tools (SSDT) aktualisieren.

   ![Überprüfen der Aktualisierungsspalte](./media/sql-data-warehouse-source-control-integration/8-validation-update-column.png "Überprüfen der Aktualisierungsspalte")

2. Committen und pushen Sie die Änderung von Ihrem lokalen Repository in das Azure-Repository.

   ![Pushen der Änderungen](./media/sql-data-warehouse-source-control-integration/9-push-column-change.png "Änderungen pushen")

3. Überprüfen Sie, ob die Änderung in Ihr Azure-Repository gepusht wurde.

   ![Überprüfen](./media/sql-data-warehouse-source-control-integration/10-verify-column-change-pushed.png "Überprüfen der Änderungen")

4. (**Optional**) Verwenden Sie den Schemavergleich, und aktualisieren Sie mithilfe von SSDT die Änderungen an Ihrem dedizierten SQL-Zielpool, um sicherzustellen, dass die Objektdefinitionen in Ihrem Azure-Repository und im lokalen Repository Ihren dedizierten SQL-Pool widerspiegeln.

## <a name="next-steps"></a>Nächste Schritte

- [Entwickeln für den dedizierten SQL-Pool](sql-data-warehouse-overview-develop.md)