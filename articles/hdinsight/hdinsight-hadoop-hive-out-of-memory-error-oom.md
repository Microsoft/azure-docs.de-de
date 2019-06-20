---
title: Beheben eines Fehlers vom Typ „Nicht genügend Hive-Arbeitsspeicher“ in Azure HDInsight
description: Beheben Sie einen Fehler vom Typ „Nicht genügend Hive-Arbeitsspeicher“ in HDInsight. Das Kundenszenario ist eine viele große Tabellen übergreifende Abfrage.
keywords: Fehler „Nicht genügend Arbeitsspeicher“, OOM, Hive-Einstellungen
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: hrasheed
ms.openlocfilehash: 2e7328b95aecc8e644d7b9e2ec407a62551fff79
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64712793"
---
# <a name="fix-an-apache-hive-out-of-memory-error-in-azure-hdinsight"></a>Beheben eines Fehlers vom Typ „Nicht genügend Apache Hive-Arbeitsspeicher“ in Azure HDInsight

Erfahren Sie, wie Sie einen Fehler vom Typ „Nicht genügend Apache Hive-Arbeitsspeicher (OOM)“, der beim Verarbeiten großer Tabellen auftreten kann, durch Konfigurieren der Hive-Arbeitsspeichereinstellungen beheben.

## <a name="run-apache-hive-query-against-large-tables"></a>Ausführen einer Apache Hive-Abfrage für große Tabellen

Ein Kunde hat die folgende Hive-Abfrage ausgeführt:

    SELECT
        COUNT (T1.COLUMN1) as DisplayColumn1,
        …
        …
        ….
    FROM
        TABLE1 T1,
        TABLE2 T2,
        TABLE3 T3,
        TABLE5 T4,
        TABLE6 T5,
        TABLE7 T6
    where (T1.KEY1 = T2.KEY1….
        …
        …

Bestimmte Aspekte dieser Abfrage:

* T1 ist ein Alias für eine große Tabelle, TABLE1, die viele STRING-Spaltentypen enthält.
* Andere Tabellen sind nicht so groß, enthalten aber viele Spalten.
* Alle Tabellen sind miteinander verbunden, in einigen Fällen mit mehreren Spalten in TABLE1 und anderen.

Die Ausführung der Hive-Abfrage in einem HDInsight-A3-Cluster mit 24 Knoten dauerte 26 Minuten. Der Kunde erhielt die folgenden Warnmeldungen:

    Warning: Map Join MAPJOIN[428][bigTable=?] in task 'Stage-21:MAPRED' is a cross product
    Warning: Shuffle Join JOIN[8][tables = [t1933775, t1932766]] in Stage 'Stage-4:MAPRED' is a cross product

Bei Verwendung der Apache Tez-Ausführungs-Engine. Die gleiche Abfrage wurde 15 Minuten ausgeführt, und dann wurde die folgende Fehlermeldung ausgegeben:

    Status: Failed
    Vertex failed, vertexName=Map 5, vertexId=vertex_1443634917922_0008_1_05, diagnostics=[Task failed, taskId=task_1443634917922_0008_1_05_000006, diagnostics=[TaskAttempt 0 failed, info=[Error: Failure while running task:java.lang.RuntimeException: java.lang.OutOfMemoryError: Java heap space
        at
    org.apache.hadoop.hive.ql.exec.tez.TezProcessor.initializeAndRunProcessor(TezProcessor.java:172)
        at org.apache.hadoop.hive.ql.exec.tez.TezProcessor.run(TezProcessor.java:138)
        at
    org.apache.tez.runtime.LogicalIOProcessorRuntimeTask.run(LogicalIOProcessorRuntimeTask.java:324)
        at
    org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable$1.run(TezTaskRunner.java:176)
        at
    org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable$1.run(TezTaskRunner.java:168)
        at java.security.AccessController.doPrivileged(Native Method)
        at javax.security.auth.Subject.doAs(Subject.java:415)
        at org.apache.hadoop.security.UserGroupInformation.doAs(UserGroupInformation.java:1628)
        at
    org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable.call(TezTaskRunner.java:168)
        at
    org.apache.tez.runtime.task.TezTaskRunner$TaskRunnerCallable.call(TezTaskRunner.java:163)
        at java.util.concurrent.FutureTask.run(FutureTask.java:262)
        at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1145)
        at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:615)
        at java.lang.Thread.run(Thread.java:745)
    Caused by: java.lang.OutOfMemoryError: Java heap space

Bei Verwendung eines größeren virtuellen Computers (wie z. B. D12) wird die Fehlermeldung weiterhin angezeigt.


## <a name="debug-the-out-of-memory-error"></a>Debuggen des Fehlers „Nicht genügend Arbeitsspeicher“

Unsere Support- und Entwicklerteams fanden heraus, dass eines der Probleme, das den Fehler vom Typ „Nicht genügend Arbeitsspeicher“ verursacht, ein [in der Apache JIRA beschriebenes bekanntes Problem](https://issues.apache.org/jira/browse/HIVE-8306) war:

    When hive.auto.convert.join.noconditionaltask = true we check noconditionaltask.size and if the sum  of tables sizes in the map join is less than noconditionaltask.size the plan would generate a Map join, the issue with this is that the calculation doesn't take into account the overhead introduced by different HashTable implementation as results if the sum of input sizes is smaller than the noconditionaltask size by a small margin queries will hit OOM.

In der Datei „hive-site.xml“ war **hive.auto.convert.join.noconditionaltask** auf **true** festgelegt:

```xml
<property>
    <name>hive.auto.convert.join.noconditionaltask</name>
    <value>true</value>
    <description>
            Whether Hive enables the optimization about converting common join into mapjoin based on the input file size.
            If this parameter is on, and the sum of size for n-1 of the tables/partitions for a n-way join is smaller than the
            specified size, the join is directly converted to a mapjoin (there is no conditional task).
    </description>
</property>
```

Wahrscheinlich war „Map Join“ die Ursache für den Java-Heapspeicher-OOM-Fehler. Wie im Blogbeitrag [Hadoop Yarn memory settings in HDInsight](https://blogs.msdn.com/b/shanyu/archive/2014/07/31/hadoop-yarn-memory-settings-in-hdinsigh.aspx) (Hadoop Yarn-Arbeitsspeichereinstellungen in HDInsight) erläutert, gehört der bei Einsatz der Tez-Ausführungs-Engine verwendete Heapspeicher tatsächlich zum Tez-Container. In der folgenden Abbildung ist der Tez-Containerspeicher dargestellt.

![Diagramm zum Tez-Containerspeicher: Fehler vom Typ „Nicht genügenden Hive-Arbeitsspeicher“](./media/hdinsight-hadoop-hive-out-of-memory-error-oom/hive-out-of-memory-error-oom-tez-container-memory.png)

Wie im Blogbeitrag schon erwähnt, definieren die folgenden zwei Einstellungen den Containerspeicher für den Heap: **hive.tez.container.size** und **hive.tez.java.opts**. Nach unserer Erfahrung bedeutet die Ausnahme „Nicht genügend Arbeitsspeicher“ nicht, dass der Container zu klein ist. Es bedeutet, dass der Java-Heap (hive.tez.java.opts) zu klein ist. Wenn Sie also eine Fehlermeldung vom Typ „Nicht genügend Arbeitsspeicher“ erhalten, können Sie versuchen, **hive.tez.java.opts** heraufzusetzen. Ggf. müsse Sie **hive.tez.container.size** heraufsetzen. Die Einstellung **java.opts** sollte ungefähr 80 Prozent von **container.size** betragen.

> [!NOTE]  
> Die Einstellung **hive.tez.java.opts** muss stets kleiner sein als **hive.tez.container.size**.
> 
> 

Da ein D12-Computer über einen Arbeitsspeicher von 28 GB verfügt, haben wir beschlossen, eine Containergröße von 10 GB (10.240 MB) zu verwenden und „java.opts“ 80 % zuzuweisen:

    SET hive.tez.container.size=10240
    SET hive.tez.java.opts=-Xmx8192m

Mit den neuen Einstellungen wurde die Abfrage in weniger als 10 Minuten erfolgreich ausgeführt.

## <a name="next-steps"></a>Nächste Schritte

Eine OOM-Fehlermeldung zu erhalten, bedeutet nicht unbedingt, dass der Container zu klein ist. Konfigurieren Sie stattdessen die Einstellungen für den Arbeitsspeicher, sodass die Heapgröße erhöht wird und mindestens 80 % der Größe des Containerspeichers entspricht. Informationen zum Optimieren von Hive-Abfragen finden Sie unter [Optimieren von Apache Hive-Abfragen für Apache Hadoop in HDInsight](hdinsight-hadoop-optimize-hive-query.md).
