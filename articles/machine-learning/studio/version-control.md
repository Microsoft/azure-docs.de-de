---
title: Anwendungslebenszyklusverwaltung
titleSuffix: Azure Machine Learning Studio
description: Anwenden bewährter Application Lifecycle Management-Methoden in Azure Machine Learning Studio
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.date: 10/27/2016
ms.openlocfilehash: 046afaa0e83fa572d6cd43a3717707892b25af69
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66171091"
---
# <a name="application-lifecycle-management-in-azure-machine-learning-studio"></a>Application Lifecycle Management in Azure Machine Learning Studio
Azure Machine Learning Studio ist ein Tool, mit dem Machine Learning-Experimente entwickelt und auf der Azure-Cloudplattform ausgeführt werden. Es ist vergleichbar mit der Zusammenführung der Visual Studio IDE mit skalierbaren Clouddiensten zu einer einzelnen Plattform. Sie können standardmäßige ALM-Vorgehensweisen (Application Lifecycle Management) – von der Versionsverwaltung verschiedener Ressourcen bis zur automatischen Ausführung und Bereitstellung – in Azure Machine Learning Studio integrieren. In diesem Artikel werden einige der Optionen und Ansätze behandelt.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="versioning-experiment"></a>Versionsverwaltungsexperiment
Es gibt zwei empfohlene Methoden zur Verwaltung der Versionen Ihrer Experimente. Sie können sich entweder auf den integrierten Ausführungsverlauf verlassen oder das Experiment in einem JSON-Format exportieren, um es extern zu verwalten. Jeder Ansatz hat Vor- und Nachteile.

### <a name="experiment-snapshots-using-run-history"></a>Momentaufnahmen des Experiments mithilfe des Ausführungsverlaufs
Das Ausführungsmodell des Azure Machine Learning Studio-Experiments sieht vor, das immer dann, wenn Sie im Experiment-Editor auf **Run** klicken, eine unveränderliche Momentaufnahme des Experiments an den Auftragsplaner gesendet wird. Klicken Sie zum Anzeigen der Liste mit den Momentaufnahmen in der Experiment-Editor-Ansicht in der Befehlsleiste auf **Run History**.

![Schaltfläche „RUN HISTORY“](./media/version-control/runhistory.png)

Sie können dann eine Momentaufnahme im gesperrten Modus öffnen, indem Sie auf den Namen des Experiments mit dem Zeitpunkt klicken, zu dem das Experiment zur Ausführung gesendet und die Momentaufnahme aufgezeichnet wurde. Beachten Sie, dass nur das erste Element in der Liste, das das aktuelle Experiment darstellt, bearbeitbar ist. Beachten Sie außerdem, dass jede Momentaufnahme auch in verschiedenen Status vorhanden sein kann, einschließlich „Finished (Partial run)“, „Failed“, „Failed (Partial run)“ oder „Draft“.

![Liste „Run History“](./media/version-control/runhistorylist.png)

Nach dem Öffnen können Sie das Momentaufnahmenexperiment als neues Experiment speichern und dann bearbeiten. Wenn Ihre Experimentmomentaufnahme Ressourcen, z.B. trainierte Modelle, Transformationen, Datasets usw., enthält, deren Versionen mittlerweile aktualisiert wurden, behält die Momentaufnahme die Verweise auf die ursprüngliche Version bei, die zum Zeitpunkt der Momentaufnahme vorlag. Wenn Sie die gesperrte Momentaufnahme als neues Experiment speichern, erkennt Azure Machine Learning Studio das Vorhandensein einer neueren Version dieser Ressourcen und aktualisiert sie automatisch im neuen Experiment.

Beim Löschen des Experiments werden alle Momentaufnahmen dieses Experiments gelöscht.

### <a name="exportimport-experiment-in-json-format"></a>Exportieren/Importieren des Experiments im JSON-Format
Die „Run History“-Momentaufnahmen dienen zum Beibehalten einer unveränderlichen Version des Experiments in Azure Machine Learning Studio jedes Mal, wenn es zur Ausführung übermittelt wird. Sie können auch eine lokale Kopie des Experiments speichern und sie in Ihrem bevorzugten Quellcodeverwaltungssystem einchecken, z.B. Team Foundation Server, und später anhand dieser lokalen Datei ein Experiment neu erstellen. Sie können hierzu die [Azure Machine Learning PowerShell](https://aka.ms/amlps)-Cmdlets [*Export-AmlExperimentGraph*](https://github.com/hning86/azuremlps#export-amlexperimentgraph) und [*Import-AmlExperimentGraph*](https://github.com/hning86/azuremlps#import-amlexperimentgraph) verwenden.

Die JSON-Datei ist eine Textdarstellung des Experimentdiagramms, die möglicherweise Verweise auf Ressourcen im Arbeitsbereich enthält, z.B. ein Dataset oder ein trainiertes Modell. Sie enthält keine serialisierte Version des Assets. Wenn Sie versuchen, das JSON-Dokument wieder in den Arbeitsbereich zu importieren, müssen die referenzierten Assets bereits mit den gleichen Asset-IDs vorhanden sein, auf die im Experiment verwiesen wird. Andernfalls können Sie nicht auf das importierte Experiment zugreifen.

## <a name="versioning-trained-model"></a>Versionsverwaltung trainierter Modelle
Ein trainiertes Modell in Azure Machine Learning Studio wird in ein als „iLearner-Datei“ (`.iLearner`) bezeichnetes Format serialisiert und in dem Azure Blob Storage-Konto gespeichert, das mit dem Arbeitsbereich verknüpft ist. Eine Möglichkeit zum Abrufen einer Kopie der iLearner-Datei bietet die API zum erneuten Trainieren. [In diesem Artikel](/azure/machine-learning/studio/retrain-machine-learning-model) wird die Funktionsweise der API zum erneuten Trainieren erläutert. Allgemeine Schritte:

1. Richten Sie Ihr Trainingsexperiment ein.
2. Fügen Sie einen Webdienst-Ausgabeport dem Train Model-Modul oder dem Modul hinzu, das das trainierte Modell erzeugt, z.B. Tune Model Hyperparameter oder Create R Model.
3. Führen Sie Ihr Trainingsexperiment aus, und stellen Sie es anschließend als Webdienst zum Modelltraining bereit.
4. Rufen Sie den BES-Endpunkt des Trainingswebdiensts auf, und geben Sie den Namen der gewünschten iLearner-Datei und den Speicherort des Azure-Blobspeicherkontos an, in dem sie gespeichert werden soll.
5. Nach Abschluss des BES-Aufrufs erhalten Sie die erstellte iLearner-Datei.

Sie können die iLearner-Datei auch über das PowerShell-Cmdlet [*Download-AmlExperimentNodeOutput*](https://github.com/hning86/azuremlps#download-amlexperimentnodeoutput) abrufen. Dies ist möglicherweise einfacher, wenn Sie nur eine Kopie der iLearner-Datei abrufen möchten ohne die Notwendigkeit, das Modell programmgesteuert neu zu trainieren.

Sobald Sie über die iLearner-Datei mit dem trainierten Modell verfügen, können Sie Ihre eigene Strategie für die Versionsverwaltung nutzen. Die Strategie kann so einfach sein wie das Hinzufügen eines Präfix/Postfix als Benennungskonvention und bloße Belassen der iLearner-Datei im Blobspeicher oder das Kopieren/Importieren dieser Datei in Ihr Versionskontrollsystem.

Die gespeicherte iLearner-Datei kann dann für die Bewertung über bereitgestellte Webdienste verwendet werden.

## <a name="versioning-web-service"></a>Versionsverwaltung-Webdienst
Sie können zwei Arten von Webdiensten aus einem Azure Machine Learning Studio-Experiment bereitstellen. Der klassische Webdienst ist sowohl mit dem Experiment als auch dem Arbeitsbereich eng verbunden. Der neue Webdienst nutzt das Azure Resource Manager-Framework und ist nicht mehr mit dem ursprünglichen Experiment oder dem Arbeitsbereich verbunden.

### <a name="classic-web-service"></a>Klassischer Webdienst
Zur Versionsverwaltung bei einem klassischen Webdienst können Sie das Webdienst-Endpunktkonstrukt nutzen. Hier sehen Sie einen typischen Ablauf:

1. Von Ihrem Vorhersageexperiment aus stellen Sie einen neuen klassischen Webdienst bereit, der einen standardmäßigen Endpunkt enthält.
2. Erstellen Sie einen neuen Endpunkt mit dem Namen „ep2“, der die aktuelle Version des Experiments/trainierten Modells verfügbar macht.
3. Kehren Sie zurück, und aktualisieren Sie Ihr Vorhersageexperiment und trainiertes Modell.
4. Sie können das Vorhersageexperiment erneut bereitstellen, das dann den Standardendpunkt aktualisiert. Dies ändert „ep2“ jedoch nicht.
5. Sie können jetzt einen zusätzlichen Endpunkt namens „ep3“ erstellen, der die neue Version der Experiments und des trainierten Modells verfügbar macht.
6. Gehen Sie ggf. zurück zu Schritt 3.

Im Lauf der Zeit haben Sie ggf. im selben Webdienst zahlreiche Endpunkte erstellt. Jeder davon stellt eine Kopie des Experiments zu einem bestimmten Zeitpunkt mit der jeweils gültigen Version des trainierten Modells dar. Dann können Sie mittels externer Logik bestimmen, welcher Endpunkt aufgerufen werden soll, was der Auswahl einer Version des trainierten Modells für die Bewertungsausführung entspricht.

Sie können auch viele identische Webdienst-Endpunkte erstellen und dann verschiedene Versionen der iLearner-Datei mit dem Endpunkt patchen, um einen ähnlichen Effekt zu erzielen. In diesem [Artikel](create-models-and-endpoints-with-powershell.md) wird ausführlicher erläutert, wie Sie dies durchführen können.

### <a name="new-web-service"></a>Neuer Webdienst
Wenn Sie einen neuen Webdienst auf Azure Resource Manager-Basis erstellen, ist das Endpunktkonstrukt nicht mehr verfügbar. Sie können stattdessen anhand Ihres Vorhersageexperiments mit dem PowerShell-Cmdlet [Export-AmlWebServiceDefinitionFromExperiment](https://github.com/hning86/azuremlps#export-amlwebservicedefinitionfromexperiment) oder von einem Webdienst auf Basis des Resource Manager aus mit dem PowerShell-Cmdlet [*Export-AzMlWebservice*](https://docs.microsoft.com/powershell/module/az.machinelearning/export-azmlwebservice) WSD-Dateien (Web Service Definition, Webdienstdefinition) im JSON-Format generieren.

Nachdem Sie die WSD-Datei exportiert haben und ihre Version verwalten, können Sie auch die WSD in einem anderen Webdienstplan in einer anderen Azure-Region als neuen Webdienst bereitstellen. Achten Sie nur darauf, dass Sie sowohl die richtige Speicherkontokonfiguration als auch die neue Webdienstplan-ID angeben. Um in verschiedenen iLearner-Dateien zu patchen, können Sie die WSD-Datei ändern und den Speicherortverweis des trainierten Modells aktualisieren und als neuen Webdienst bereitstellen.

## <a name="automate-experiment-execution-and-deployment"></a>Automatisieren von Ausführung und Bereitstellung des Experiments
Ein wichtiger Aspekt von ALM ist die Möglichkeit, den Ausführungs- und Bereitstellungsprozess der Anwendung zu automatisieren. In Azure Machine Learning Studio erreichen Sie dies mithilfe des [PowerShell-Moduls](https://aka.ms/amlps). Hier ist ein Beispiel aller Schritte, die für einen standardmäßigen automatisierten ALM-Ausführungs-/Bereitstellungsprozess mit dem [Azure Machine Learning Studio PowerShell-Modul](https://aka.ms/amlps) relevant sind. Jeder Schritt ist mit mindestens einem PowerShell-Cmdlet verknüpft, das Sie nutzen können, um diesen Schritt auszuführen.

1. [Laden Sie ein Dataset hoch.](https://github.com/hning86/azuremlps#upload-amldataset)
2. Kopieren Sie ein Trainingsexperiment aus einem [Arbeitsbereich](https://github.com/hning86/azuremlps#copy-amlexperiment) oder [Katalog](https://github.com/hning86/azuremlps#copy-amlexperimentfromgallery) in den Arbeitsbereich, oder [importieren](https://github.com/hning86/azuremlps#import-amlexperimentgraph) Sie ein [exportiertes](https://github.com/hning86/azuremlps#export-amlexperimentgraph) Experiment vom lokalen Datenträger.
3. [Aktualisieren Sie das Dataset](https://github.com/hning86/azuremlps#update-amlexperimentuserasset) im Trainingsexperiment.
4. [Führen Sie das Trainingsexperiment aus](https://github.com/hning86/azuremlps#start-amlexperiment).
5. [Stufen Sie das trainierte Modell höher.](https://github.com/hning86/azuremlps#promote-amltrainedmodel)
6. [Kopieren Sie ein Vorhersageexperiment](https://github.com/hning86/azuremlps#copy-amlexperiment) in den Arbeitsbereich.
7. [Aktualisieren Sie das trainierte Modell](https://github.com/hning86/azuremlps#update-amlexperimentuserasset) im Vorhersageexperiment.
8. [Führen Sie das Vorhersageexperiment aus.](https://github.com/hning86/azuremlps#start-amlexperiment)
9. Stellen Sie vom Vorhersageexperiment aus einen [Webdienst bereit](https://github.com/hning86/azuremlps#new-amlwebservice).
10. Testen Sie den [RRS](https://github.com/hning86/azuremlps#invoke-amlwebservicerrsendpoint)- oder [BES](https://github.com/hning86/azuremlps#invoke-amlwebservicebesendpoint)-Endpunkt des Webdiensts.

## <a name="next-steps"></a>Nächste Schritte
* Laden Sie das [Azure Machine Learning Studio PowerShell](https://aka.ms/amlps)-Modul herunter, und starten Sie die Automatisierung Ihrer ALM-Aufgaben.
* Erfahren Sie, wie Sie über PowerShell und die API zum Umtrainieren [eine große Anzahl von ML-Modellen mit nur einem einzigen Experiment erstellen und verwalten](create-models-and-endpoints-with-powershell.md).
* Erfahren Sie mehr über [das Bereitstellen von Azure Machine Learning-Webdiensten](publish-a-machine-learning-web-service.md).
