---
title: Datenerfassungstools
titleSuffix: Azure Data Science Virtual Machine
description: Erfahren Sie mehr über die Datenerfassungstools und Hilfsprogramme, die in der Data Science Virtual Machine vorinstalliert sind.
keywords: Data Science-Tools, virtuelle Computer für Data Science, Tools für Data Science, Linux Data Science
services: machine-learning
ms.service: data-science-vm
author: timoklimmer
ms.author: tklimmer
ms.topic: conceptual
ms.date: 05/12/2021
ms.openlocfilehash: 77968c7a611cdfade3d2f1eb2cb6e9b0a3768728
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/19/2021
ms.locfileid: "110070946"
---
# <a name="data-science-virtual-machine-data-ingestion-tools"></a>Datenerfassungstools für die Data Science-VM

Als einen der ersten technischen Schritte in einem Data Science- oder KI-Projekt müssen Sie die zu verwendenden Datasets bestimmen und diese in Ihre Analyseumgebung importieren. Die Data Science Virtual Machine (DSVM) stellt Tools und Bibliotheken bereit, um Daten aus verschiedenen Quellen lokal in einen analytischen Datenspeicher auf der DSVM oder Daten in eine cloudbasierte oder lokale Datenplattform zu importieren.

Nachstehend sind einige der Datenverschiebungstools aufgeführt, die in der DSVM verfügbar sind.

## <a name="azure-cli"></a>Azure CLI

| Category | Wert |
|--|--|
| Was ist das? | Ein Verwaltungstool für Azure. Enthält auch Befehlsverben zum Verschieben von Daten aus Azure-Datenplattformen wie Azure-Blobspeicher und Azure Data Lake Store. |
| Unterstützte DSVM-Versionen | Windows, Linux |
| Typische Verwendung | Importieren und Exportieren von Daten in bzw. aus Azure Storage und Azure Data Lake Store. |
| Verwendung/Ausführung | Öffnen Sie eine Eingabeaufforderung, und geben Sie `az` ein, um die Hilfe anzuzeigen. |
| Links zu Beispielen | [Verwenden der Azure-Befehlszeilenschnittstelle](/cli/azure) |


## <a name="azcopy"></a>AzCopy

| Category | Wert |
|--|--|
| Was ist das? | Ein Tool zum Kopieren von Daten in lokale und aus lokalen Dateien sowie in und aus Azure-Blobspeicher, -Dateien und -Tabellen. |
| Unterstützte DSVM-Versionen | Windows |
| Typische Verwendung | Kopieren von Dateien in Azure-Blobspeicher und Kopieren von Blobs zwischen Konten. |
| Verwendung/Ausführung | Öffnen Sie eine Eingabeaufforderung, und geben Sie `azcopy` ein, um die Hilfe anzuzeigen. |
| Links zu Beispielen | [AzCopy unter Windows](../../storage/common/storage-use-azcopy-v10.md) |


## <a name="azure-cosmos-db-data-migration-tool"></a>Azure Cosmos DB-Datenmigrationstool

|--|--|
| ------------- | ------------- |
| Was ist das? | Tool, mit dem Daten aus verschiedenen Quellen in Azure Cosmos DB importiert werden können, eine NoSQL-Datenbank in der Cloud. Zu diesen Quellen gehören JSON-Dateien, CSV-Dateien, SQL, MongoDB, Azure-Tabellenspeicher, Amazon DynamoDB und Azure Cosmos DB-SQL-API-Sammlungen. |
| Unterstützte DSVM-Versionen | Windows |
| Typische Verwendung | Importieren von Dateien von einem virtuellen Computer in CosmosDB, Importieren von Daten aus Azure-Tabellenspeicher in CosmosDB und Importieren von Daten aus einer Microsoft SQL Server-Datenbank in CosmosDB |
| Verwendung/Ausführung | Öffnen Sie zum Verwenden der Befehlszeilenversion eine Eingabeaufforderung, und geben Sie `dt` ein. Öffnen Sie zum Verwenden des GUI-Tools eine Eingabeaufforderung, und geben Sie `dtui` ein. |
| Links zu Beispielen | [Importieren von Daten in Azure Cosmos DB mit der DocumentDB-API](../../cosmos-db/import-data.md) |

## <a name="azure-storage-explorer"></a>Azure Storage-Explorer

| Category | Wert |
|--|--|
| Was ist das? | Grafische Benutzeroberfläche für die Interaktion mit Dateien, die in der Azure-Cloud gespeichert sind. |
| Unterstützte DSVM-Versionen | Windows |
| Typische Verwendung | Importieren und Exportieren von Daten aus der DSVM. |
| Verwendung/Ausführung | Suchen Sie im Startmenü nach „Azure Storage-Explorer“. |
| Links zu Beispielen | [Azure Storage-Explorer](vm-do-ten-things.md#access-azure-data-and-analytics-services) |

## <a name="bcp"></a>bcp

| Category | Wert |
|--|--|
| Was ist das? | SQL Server-Tool zum Kopieren von Daten zwischen SQL Server und einer Datendatei. |
| Unterstützte DSVM-Versionen | Windows |
| Typische Verwendung | Importieren einer CSV-Datei in eine SQL Server-Tabelle und Exportieren einer SQL Server-Tabelle in eine Datei. |
| Verwendung/Ausführung | Öffnen Sie eine Eingabeaufforderung, und geben Sie `bcp` ein, um die Hilfe anzuzeigen. |
| Links zu Beispielen | [bcp-Hilfsprogramm](/sql/tools/bcp-utility) |

## <a name="blobfuse"></a>blobfuse

| Category | Wert |
|--|--|
| Was ist das? | Ein Tool zum Einbinden eines Azure-Blobspeichercontainers in das Linux-Dateisystem. |
| Unterstützte DSVM-Versionen | Linux |
| Typische Verwendung | Lesen aus und Schreiben in Blobs in einem Container. |
| Verwenden und Ausführen | Führen Sie _blobfuse_ auf einem Terminal aus. |
| Links zu Beispielen | [blobfuse auf GitHub](https://github.com/Azure/azure-storage-fuse) |
