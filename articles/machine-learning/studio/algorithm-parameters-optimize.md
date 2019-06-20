---
title: Optimieren von Algorithmen
titleSuffix: Azure Machine Learning Studio
description: Hier erfahren Sie, wie Sie die optimalen Parameter für einen Algorithmus in Azure Machine Learning Studio auswählen.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 11/29/2017
ms.openlocfilehash: 6dc9476f603d5664b7ea23489042b69f86647cf5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60752195"
---
# <a name="choose-parameters-to-optimize-your-algorithms-in-azure-machine-learning-studio"></a>Auswählen von Parametern zur Optimierung von Algorithmen in Azure Machine Learning Studio

In diesem Thema wird das Auswählen des richtigen Hyperparametersatzes für einen Algorithmus in Azure Machine Learning Studio beschrieben. Für die meisten Machine Learning-Algorithmen müssen Parameter festgelegt werden. Wenn Sie ein Modell trainieren, müssen Sie Werte für diese Parameter bereitstellen. Die Wirksamkeit des trainierten Modells ist abhängig von den gewählten Parametern. Das Ermitteln des optimalen Parametersatzes wird als *Modellauswahl* bezeichnet.



Es gibt verschiedene Möglichkeiten zur Modellauswahl. Beim maschinellen Lernen ist die Kreuzvalidierung eine der am häufigsten verwendeten Methoden für die Modellauswahl und der Standardmechanismus für die Modellauswahl in Azure Machine Learning Studio. Da Azure Machine Learning Studio sowohl R als auch Python unterstützt, können Sie ihren eigenen Modellauswahlmechanismus in R oder Python implementieren.

Das Ermitteln des besten Parametersatzes umfasst vier Schritte:

1. **Definieren des Parameterraums**: Für den Algorithmus legen Sie zunächst die genauen Parameterwerte fest, die berücksichtigt werden sollen.
2. **Definieren der Einstellungen für die Kreuzvalidierung**: Für das Dataset müssen Sie entscheiden, wie Teilmengen für die Kreuzvalidierung ausgewählt werden.
3. **Definieren der Metrik**: Anschließend entscheiden Sie, welche Metrik zum Ermitteln des besten Parametersatzes verwendet werden soll, z.B. Richtigkeit, mittlere quadratische Abweichung, Genauigkeit, Sensitivität oder F-Maß.
4. **Trainieren, Evaluieren und Vergleichen**: Für jede eindeutige Kombination der Parameterwerte wird eine Kreuzvalidierung anhand der von Ihnen definierten Fehlermetrik durchgeführt. Nach Auswertung und Vergleich können Sie das leistungsfähigste Modell auswählen.

Die folgende Abbildung veranschaulicht, wie dies in Azure Machine Learning Studio erzielt werden kann.

![Suchen des besten Parametersatzes](./media/algorithm-parameters-optimize/fig1.png)

## <a name="define-the-parameter-space"></a>Definieren des Parameterraums
Der Parametersatz kann bei der Initialisierung des Modells definiert werden. Der Parameterbereich aller Machine Learning-Algorithmen weist zwei Trainingsmodi auf: *Single Parameter* und *Parameter Range*. Wählen Sie den Modus „Parameter Range“ aus. Im Modus „Parameter Range“ können Sie für jeden Parameter mehrere Werte eingeben. Sie können in das Textfeld durch Trennzeichen getrennte Werte eingeben.

![Two-Class Boosted Decision Tree, Single Parameter](./media/algorithm-parameters-optimize/fig2.png)

 Alternativ können **Use Range Builder** verwenden, um den Maximum- und Minimumpunkt des Rasters und die Gesamtzahl der zu generierenden Punkte zu definieren. Standardmäßig werden die Parameterwerte auf einer linearen Skala generiert. Wenn jedoch das Kontrollkästchen **Log Scale** aktiviert ist, werden die Werte in logarithmischer Skalierung generiert (d.h., das Verhältnis zwischen den benachbarten Punkten ist konstant anstelle ihrer Differenz). Für als ganze Zahl angegebene Parameter können Sie einen Bereich mithilfe eines Bindestrichs definieren. „1-10“ bedeutet beispielsweise, dass alle ganzen Zahlen von 1 bis 10 den Parametersatz bilden. Ein gemischter Modus wird ebenfalls unterstützt. Der Parametersatz „1-10, 20, 50“ enthält z.B. die ganzen Zahlen 1 bis 10, 20 und 50.

![Two-Class Boosted Decision Tree, Parameter Range](./media/algorithm-parameters-optimize/fig3.png)

## <a name="define-cross-validation-folds"></a>Definieren der Teilmengen für die Kreuzvalidierung
Das Modul [Partition and Sample][partition-and-sample] kann verwendet werden, um den Daten nach dem Zufallsprinzip Teilmengen zuzuweisen. In der folgenden Beispielkonfiguration für das Modul definieren wir fünf Teilmengen und weisen den Beispielinstanzen die Teilmengennummern nach dem Zufallsprinzip zu.

![Partition und Beispiel](./media/algorithm-parameters-optimize/fig4.png)

## <a name="define-the-metric"></a>Definieren der Metrik
Das Modul [Tune Model Hyperparameters][tune-model-hyperparameters] bietet Unterstützung für die empirische Auswahl des besten Parametersatzes für einen bestimmten Algorithmus und ein bestimmtes Dataset. Der Bereich **Properties** dieses Moduls enthält, zusätzlich zu anderen Informationen zum Trainieren des Modells, die zu verwendende Metrik für die Ermittlung des besten Parametersatzes. Er verfügt über zwei verschiedene Dropdownlistenfelder für Klassifizierungs- und Regressionsalgorithmen. Wenn der untersuchte Algorithmus ein Klassifizierungsalgorithmus ist, wird die Regressionskennzahl ignoriert und umgekehrt. In diesem speziellen Beispiel ist die Metrik **Accuracy**.   

![„Sweep“-Parameter](./media/algorithm-parameters-optimize/fig5.png)

## <a name="train-evaluate-and-compare"></a>Trainieren, Evaluieren und Vergleichen
Das gleiche Modul [Tune Model Hyperparameters][tune-model-hyperparameters] trainiert alle Modelle, die dem Parametersatz entsprechen, wertet verschiedene Kennzahlen aus und erstellt dann anhand der von Ihnen ausgewählten Metrik das am besten trainierte Modell. Dieses Modul hat zwei obligatorische Eingaben:

* Den untrainierten Lernenden
* Das Dataset

Das Modul hat auch eine optionale Dataseteingabe. Verbinden Sie das Dataset mit Teilmengeninformationen mit der obligatorischen Dataseteingabe. Wenn dem Dataset keine Informationen für die Teilmenge zugewiesen ist, erfolgt standardmäßig automatisch eine 10-fache Kreuzvalidierung. Wenn die Zuordnung für die Teilmenge nicht erfolgt und ein Dataset für die Überprüfung am optionalen Datasetport bereitgestellt wird, wird ein Trainingstestmodus ausgewählt, und mit dem ersten Dataset wird das Modell für jede Parameterkombination trainiert.

![Boosted Decision Tree, Klassifizierer](./media/algorithm-parameters-optimize/fig6a.png)

Anschließend wird das Modell für das Validierungsdataset ausgewertet. Der linke Ausgabeport des Moduls weist verschiedene Metriken als Funktionen von Parameterwerten auf. Der rechte Ausgabeport stellt das trainierte Modell anhand des Modells mit der besten Leistung gemäß der ausgewählten Metrik (in diesem Fall **Accuracy**) bereit.  

![Validierungsdataset](./media/algorithm-parameters-optimize/fig6b.png)

Sie können die gewählten Parameter mithilfe einer Visualisierung des rechten Ausgabeports anzeigen. Dieses Modell kann nach dem Speichern als trainiertes Modell für die Bewertung von Testsätzen oder in einem operationalisierten Webdienst verwendet werden.

<!-- Module References -->
[partition-and-sample]: https://msdn.microsoft.com/library/azure/a8726e34-1b3e-4515-b59a-3e4a475654b8/
[tune-model-hyperparameters]: https://msdn.microsoft.com/library/azure/038d91b6-c2f2-42a1-9215-1f2c20ed1b40/
