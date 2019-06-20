---
title: Verwenden des Azure Data Lake Storage Gen2-URI
description: Verwenden des Azure Data Lake Storage Gen2-URI
services: storage
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.date: 12/06/2018
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.reviewer: jamesbak
ms.openlocfilehash: 3f486da121927be23a6bd86e8567574cd95c541e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64939285"
---
# <a name="use-the-azure-data-lake-storage-gen2-uri"></a>Verwenden des Azure Data Lake Storage Gen2-URI

Der mit Azure Data Lake Storage Gen2 kompatible [Hadoop-Dateisystemtreiber](https://www.aosabook.org/en/hdfs.html) ist unter seinem Schemabezeichner `abfs` (Azure Blob File System) bekannt. Wie andere Hadoop-Dateisystemtreiber verwendet auch der ABFS-Treiber ein URI-Format zur Angabe von Dateien und Verzeichnissen in einem Azure Data Lake Storage Gen2-fähigen Konto.

## <a name="uri-syntax"></a>URI-Syntax

Die URI-Syntax für Data Lake Storage Gen2 hängt davon ab, ob für Ihr Speicherkonto Data Lake Storage Gen2 als Standarddateisystem eingerichtet ist.

Wenn das zu adressierende Data Lake Storage Gen2-fähige Konto während der Kontoerstellung als Standarddateisystem **nicht festgelegt** wurde, lautet die URI-Syntax in der Kurzschreibweise wie folgt:

<pre>abfs[s]<sup>1</sup>://&lt;file_system&gt;<sup>2</sup>@&lt;account_name&gt;<sup>3</sup>.dfs.core.windows.net/&lt;path&gt;<sup>4</sup>/&lt;file_name&gt;<sup>5</sup></pre>

1. **Schemabezeichner**: Das `abfs`-Protokoll wird als Schemabezeichner verwendet. Sie haben die Möglichkeit, eine Verbindung mit oder ohne SSL (Secure Socket Layer) herzustellen. Verwenden Sie `abfss` zum Herstellen einer SSL-Verbindung.

2. **Dateisystem**: Der übergeordnete Speicherort, in dem sich die Dateien und Ordner befinden. Dies entspricht Containern in Azure Storage-Blobs.

3. **Kontoname**: Der Name, der Ihrem Speicherkonto während der Erstellung gegeben wurde.

4. **Pfade**: Eine durch Schrägstriche (`/`) getrennte Darstellung der Verzeichnisstruktur.

5. **Dateiname**: Der Name der jeweiligen Datei. Beim Verweisen auf ein Verzeichnis ist dieser Parameter optional.

Wenn jedoch das Konto, das Sie adressieren möchten, während der Kontoerstellung als Standarddateisystem festgelegt wurde, lautet die URI-Syntax in der Kurzschreibweise wie folgt:

<pre>/&lt;path&gt;<sup>1</sup>/&lt;file_name&gt;<sup>2</sup></pre>

1. **Pfad**: Eine durch Schrägstriche (`/`) getrennte Darstellung der Verzeichnisstruktur.

2. **Dateiname**: Der Name der jeweiligen Datei.


## <a name="next-steps"></a>Nächste Schritte

- [Verwenden von Azure Data Lake Storage Gen2 mit Azure HDInsight-Clustern](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
