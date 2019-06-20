---
title: Verwenden leerer Edgeknoten in Apache Hadoop-Clustern in HDInsight – Azure
description: Hier erfahren Sie, wie Sie einem HDInsight-Cluster einen leeren Edgeknoten hinzufügen, der als Client verwendet werden kann, und wie Sie Ihre HDInsight-Anwendungen testen/hosten.
ms.reviewer: jasonh
author: hrasheed-msft
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 06/13/2019
ms.author: hrasheed
ms.openlocfilehash: ee9b5b651f440bc7d179b1a06c7ddbd3f9137a0d
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165897"
---
# <a name="use-empty-edge-nodes-on-apache-hadoop-clusters-in-hdinsight"></a>Verwenden leerer Edgeknoten in Apache Hadoop-Clustern in HDInsight

Hier erfahren Sie, wie Sie einem HDInsight-Cluster einen leeren Edgeknoten hinzufügen. Ein leerer Edgeknoten ist ein virtueller Linux-Computer, auf dem die gleichen Clienttools installiert und konfiguriert sind wie auf den Hauptknoten, aber keine [Apache Hadoop](https://hadoop.apache.org/)-Dienste ausgeführt werden. Sie können den Edgeknoten zum Zugreifen auf den Cluster sowie zum Testen und Hosten Ihrer Clientanwendungen verwenden. 

Sie können einen leeren Edgeknoten zu einem vorhandenen HDInsight-Cluster oder zu einem neuen Cluster hinzufügen, wenn Sie den Cluster erstellen. Einen leeren Edgeknoten fügen Sie mit der Azure Resource Manager-Vorlage hinzu.  Im folgenden Beispiel sehen Sie, wie dies mithilfe einer Vorlage erfolgt:

    "resources": [
        {
            "name": "[concat(parameters('clusterName'),'/', variables('applicationName'))]",
            "type": "Microsoft.HDInsight/clusters/applications",
            "apiVersion": "2015-03-01-preview",
            "dependsOn": [ "[concat('Microsoft.HDInsight/clusters/',parameters('clusterName'))]" ],
            "properties": {
                "marketPlaceIdentifier": "EmptyNode",
                "computeProfile": {
                    "roles": [{
                        "name": "edgenode",
                        "targetInstanceCount": 1,
                        "hardwareProfile": {
                            "vmSize": "Standard_D3"
                        }
                    }]
                },
                "installScriptActions": [{
                    "name": "[concat('emptynode','-' ,uniquestring(variables('applicationName')))]",
                    "uri": "[parameters('installScriptAction')]",
                    "roles": ["edgenode"]
                }],
                "uninstallScriptActions": [],
                "httpsEndpoints": [],
                "applicationType": "CustomApplication"
            }
        }
    ],

Wie im Beispiel gezeigt, können Sie optional eine [Skriptaktion](hdinsight-hadoop-customize-cluster-linux.md) aufrufen, um zusätzliche Konfigurationsschritte wie z.B. die Installation von [Apache Hue](hdinsight-hadoop-hue-linux.md) in den Edgeknoten auszuführen. Das Skriptaktionsskript muss öffentlich im Internet verfügbar sein.  Wenn das Skript beispielsweise in Azure Storage gespeichert ist, verwenden Sie öffentliche Container oder öffentliche Blobs.

Die Größe des Edgeknotens des virtuellen Computers muss den VM-Größenanforderungen des HDInsight-Clusterworkerknotens entsprechen. Die empfohlene VM-Größe des Workerknotens finden Sie unter [Erstellen von Apache Hadoop-Clustern in HDInsight](hdinsight-hadoop-provision-linux-clusters.md#cluster-types).

Nach der Erstellung eines Edgeknotens können Sie über SSH eine Verbindung zum Edgeknoten herstellen und Client-Tools ausführen, um auf den Hadoop-Cluster in HDInsight zuzugreifen.

> [!WARNING]   
> Für auf dem Edgeknoten installierte benutzerdefinierte Komponenten stellt Microsoft wirtschaftlich angemessenen Support bereit. Dadurch werden unter Umständen bei Ihnen auftretende Probleme behoben. Alternativ erhalten Sie unter Umständen weitere Unterstützung über Communityressourcen. Im Anschluss finden Sie einige besonders aktive Websites, auf denen Sie Unterstützung von der Community erhalten:
>
> * [MSDN-Forum für HDInsight](https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight)
> * [https://stackoverflow.com](https://stackoverflow.com).
>
> Bei Verwendung einer Apache-Technologie finden Sie hilfreiche Informationen ggf. auf den Apache-Projektwebsites unter [https://apache.org](https://apache.org), beispielsweise auf der Website für [Apache Hadoop](https://hadoop.apache.org/).

> [!IMPORTANT]
> Ubuntu-Images stehen für die Erstellung neuer HDInsight-Cluster innerhalb von 3 Monaten nach der Veröffentlichung zur Verfügung. Seit Januar 2019 werden laufende Cluster (einschließlich Edgeknoten) **nicht** automatisch gepatcht. Kunden müssen Skriptaktionen oder andere Mechanismen verwenden, um einen laufenden Cluster zu patchen.  Weitere Informationen finden Sie unter [Patchen des Betriebssystems für HDInsight](./hdinsight-os-patching.md).

## <a name="add-an-edge-node-to-an-existing-cluster"></a>Hinzufügen eines Edgeknotens zu einem vorhandenen Cluster
In diesem Abschnitt verwenden Sie eine Resource Manager-Vorlage, um einen Edgeknoten zu einem vorhandenen HDInsight-Cluster hinzuzufügen.  Die Resource Manager-Vorlage finden Sie in [GitHub](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-add-edge-node/). Die Resource Manager-Vorlage ruft eine Skriptaktion auf, die sich unter https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-hdinsight-linux-add-edge-node/scripts/EmptyNodeSetup.sh befindet. Das Skript führt keine Aktionen aus.  Es demonstriert das Aufrufen der Skriptaktion aus einer Resource Manager-Vorlage.

**Hinzufügen eines Edgeknotens zu einem vorhandenen Cluster**

1. Klicken Sie auf die folgende Abbildung, um sich bei Azure anzumelden und die Azure Resource Manager-Vorlage im Azure-Portal zu öffnen. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-add-edge-node%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-use-edge-node/deploy-to-azure.png" alt="Deploy to Azure"></a>
3. Konfigurieren Sie die folgenden Eigenschaften:
   
   * **Abonnement**: Wählen Sie ein Azure-Abonnement aus, das zum Erstellen des Clusters verwendet wird.
   * **Ressourcengruppe**: Wählen Sie die Ressourcengruppe aus, die für den vorhandenen HDInsight-Cluster verwendet wird.
   * **Standort**: Wählen Sie den Standort des vorhandenen HDInsight-Clusters aus.
   * **Clustername:** Geben Sie den Namen eines vorhandenen HDInsight-Clusters ein.
   * **Edge Node Size** (Edgeknotengröße): Wählen Sie eine der VM-Größen aus. Die Größe des virtuellen Computers muss den VM-Größenanforderungen des Workerknotens entsprechen. Die empfohlene VM-Größe des Workerknotens finden Sie unter [Erstellen von Apache Hadoop-Clustern in HDInsight](hdinsight-hadoop-provision-linux-clusters.md#cluster-types).
   * **Edge Node Prefix** (Edgeknotenpräfix): Der Standardwert lautet **new**.  Wenn Sie den Standardwert verwenden, heißt der Edgeknoten **new-edgenode**.  Sie können das Präfix im Portal anpassen. Sie können auch den vollständigen Namen in der Vorlage anpassen.

4. Wählen Sie **Ich stimme den oben genannten Geschäftsbedingungen zu**, und klicken Sie anschließend auf **Kaufen**, um den Edgeknoten zu erstellen.

>[!IMPORTANT]  
> Stellen Sie sicher, dass Sie die Azure-Ressourcengruppe für den vorhandenen HDInsight-Cluster auswählen.  Andernfalls erhalten Sie die Fehlermeldung „Angeforderter Vorgang kann nicht auf die geschachtelte Ressource angewendet werden. Übergeordnete Ressource &lt;ClusterName > nicht gefunden.“

## <a name="add-an-edge-node-when-creating-a-cluster"></a>Hinzufügen eines Edgeknotens beim Erstellen eines Clusters
In diesem Abschnitt verwenden Sie eine Resource Manager-Vorlage, um HDInsight-Cluster mit einem Edgeknoten zu erstellen.  Die Resource Manager-Vorlage finden Sie im [Katalog der Azure-Schnellstartvorlagen](https://azure.microsoft.com/documentation/templates/101-hdinsight-linux-with-edge-node/). Die Resource Manager-Vorlage ruft eine Skriptaktion auf, die sich unter https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-hdinsight-linux-with-edge-node/scripts/EmptyNodeSetup.sh befindet. Das Skript führt keine Aktionen aus.  Es demonstriert das Aufrufen der Skriptaktion aus einer Resource Manager-Vorlage.

**So erstellen Sie einen HDInsight-Cluster mit einem Edgeknoten**

1. Erstellen Sie einen HDInsight-Cluster, wenn Sie noch keinen besitzen.  Weitere Informationen finden Sie unter [Erste Schritte mit Hadoop in HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).
2. Klicken Sie auf die folgende Abbildung, um sich bei Azure anzumelden und die Azure Resource Manager-Vorlage im Azure-Portal zu öffnen. 
   
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-edge-node%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apps-use-edge-node/deploy-to-azure.png" alt="Deploy to Azure"></a>
3. Konfigurieren Sie die folgenden Eigenschaften:
   
   * **Abonnement**: Wählen Sie ein Azure-Abonnement aus, das zum Erstellen des Clusters verwendet wird.
   * **Ressourcengruppe**: Erstellen Sie eine neue Ressourcengruppe für den Cluster.
   * **Standort**: Wählen Sie einen Speicherort für die Ressourcengruppe aus.
   * **Clustername:** Geben Sie einen Namen für den neu zu erstellenden Cluster ein.
   * **Benutzername für Clusteranmeldung**: Geben Sie den Namen des Hadoop-HTTP-Benutzers ein.  Der Standardname lautet **admin**.
   * **Kennwort für Clusteranmeldung**: Geben Sie das Kennwort des Hadoop-HTTP-Benutzers ein.
   * **SSH-Benutzername:** Geben Sie den Namen des SSH-Benutzers ein. Der Standardname ist **sshuser**.
   * **SSH-Kennwort:** Geben Sie das Kennwort des SSH-Benutzers ein.
   * **Install Script Action** (Skriptaktion installieren): Behalten Sie für die Bearbeitung dieses Tutorials den Standardwert bei.
     
     Einige Eigenschaften wurden in der Vorlage hartcodiert: „Clustertyp“, „Anzahl der Workerknoten im Cluster“, „Edge node size“ (Edgeknotengröße) und „Edge node name“ (Edgeknotenname).
4. Wählen Sie **Ich stimme den oben genannten Geschäftsbedingungen zu**, und klicken Sie anschließend auf **Kaufen**, um den Cluster mit dem Edgeknoten zu erstellen.

## <a name="add-multiple-edge-nodes"></a>Hinzufügen mehrerer Edgeknoten

Sie können zu einem HDInsight-Cluster mehrere Edgeknoten hinzufügen.  Die Konfiguration mehrerer Edgeknoten kann nur mithilfe von Azure Resource Manager-Vorlagen durchgeführt werden.  Sehen Sie sich auch das Vorlagenbeispiel am Anfang dieses Artikels an.  Sie müssen **targetInstanceCount** auf die Anzahl der zu erstellenden Edgeknoten aktualisieren.

## <a name="access-an-edge-node"></a>Zugreifen auf einen Edgeknoten
Der ssh-Endpunkt des Edgeknotens ist &lt;Edgeknotenname>.&lt;Clustername>-ssh.azurehdinsight.net:22.  Beispiel: new-edgenode.myedgenode0914-ssh.azurehdinsight.net:22.

Der Edgeknoten wird als Anwendung im Azure-Portal angezeigt.  Im Portal erfahren Sie, wie Sie über SSH auf den Edgeknoten zuzugreifen.

**Überprüfen des SSH-Endpunkts der Edgeknoten**

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com)an.
2. Öffnen Sie den HDInsight-Cluster mit einem Edgeknoten.
3. Klicken Sie auf **Anwendungen**. Der Edgeknoten sollte angezeigt werden.  Der Standardname lautet **new-edgenode**.
4. Klicken Sie auf den Edgeknoten. Der SSH-Endpunkt sollte angezeigt werden.

**Verwenden von Hive auf dem Edgeknoten**

1. Stellen Sie über SSH eine Verbindung mit dem Edgeknoten her. Informationen hierzu finden Sie unter [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Linux, Unix oder OS X](hdinsight-hadoop-linux-use-ssh-unix.md).

2. Verwenden Sie nach dem Herstellen einer Verbindung zum Edgeknoten über SSH den folgenden Befehl, um die Hive-Konsole zu öffnen:
   
        hive
3. Führen Sie den folgenden Befehl aus, um Hive-Tabellen im Cluster anzuzeigen:
   
        show tables;

## <a name="delete-an-edge-node"></a>Löschen eines Edgeknotens
Sie können einen Edgeknoten aus dem Azure-Portal löschen.

**Zugreifen auf einen Edgeknoten**

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com)an.
2. Öffnen Sie den HDInsight-Cluster mit einem Edgeknoten.
3. Klicken Sie auf **Anwendungen**. Eine Liste mit Edgeknoten sollte angezeigt werden.  
4. Klicken Sie mit der rechten Maustaste auf den Edgeknoten, den Sie löschen möchten, und klicken Sie dann auf **Löschen**.
5. Klicken Sie auf **Ja**, um zu bestätigen.

## <a name="next-steps"></a>Nächste Schritte
In diesem Artikel haben Sie erfahren, wie Sie einen Edgeknoten hinzufügen und auf den Edgeknoten zuzugreifen. Weitere Informationen finden Sie in den folgenden Artikeln:

* [Installieren von HDInsight-Anwendungen:](hdinsight-apps-install-applications.md) Erfahren Sie, wie Sie eine HDInsight-Anwendung auf Ihren Clustern installieren.
* [Installieren benutzerdefinierter HDInsight-Anwendungen](hdinsight-apps-install-custom-applications.md): Hier erfahren Sie, wie Sie eine nicht veröffentlichte HDInsight-Anwendung in HDInsight bereitstellen.
* [Veröffentlichen von HDInsight-Anwendungen](hdinsight-apps-publish-applications.md): Hier erfahren Sie, wie Sie benutzerdefinierte HDInsight-Anwendungen im Azure Marketplace veröffentlichen.
* [MSDN: Installieren einer HDInsight-Anwendung](https://msdn.microsoft.com/library/mt706515.aspx): Erfahren Sie, wie HDInsight-Anwendungen definiert werden.
* [Anpassen Linux-basierter HDInsight-Cluster mithilfe von Skriptaktionen](hdinsight-hadoop-customize-cluster-linux.md): Hier erfahren Sie, wie Sie mithilfe der Skriptaktion zusätzliche Anwendungen installieren.
* [Erstellen von Linux-basierten Apache Hadoop-Clustern in HDInsight mit Resource Manager-Vorlagen](hdinsight-hadoop-create-linux-clusters-arm-templates.md): Hier erfahren Sie, wie Sie Resource Manager-Vorlagen für die Erstellung von HDInsight-Clustern aufrufen.

