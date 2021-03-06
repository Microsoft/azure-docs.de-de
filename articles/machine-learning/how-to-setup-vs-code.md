---
title: Einrichten der Visual Studio Code-Erweiterung (Vorschauversion)
titleSuffix: Azure Machine Learning
description: Hier wird beschrieben, wie Sie die Azure Machine Learning-Erweiterung für Visual Studio Code einrichten.
services: machine-learning
author: luisquintanilla
ms.author: luquinta
ms.service: machine-learning
ms.subservice: core
ms.date: 05/25/2021
ms.topic: how-to
ms.openlocfilehash: 80f6849bb4989692e8cd29a3288f9b104bf250bb
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/26/2021
ms.locfileid: "110460084"
---
# <a name="set-up-the-visual-studio-code-azure-machine-learning-extension-preview"></a>Einrichten der Azure Machine Learning-Erweiterung für Visual Studio Code (Vorschauversion)

Hier erfahren Sie, wie Sie die Azure Machine Learning-Erweiterung für Visual Studio Code für Ihre Machine Learning-Workflows einrichten.

> [!div class="mx-imgBorder"]
> ![VS Code-Erweiterung](./media/how-to-setup-vs-code/vs-code-extension.PNG)

Die Azure Machine Learning-Erweiterung für VS Code bietet eine Benutzeroberfläche für Folgendes:

- Verwalten von Azure Machine Learning-Ressourcen (Experimente, virtuelle Computer, Modelle, Bereitstellungen usw.)
- Lokales Entwickeln mithilfe von Remotecompute-Instanzen
- Trainieren von Machine Learning-Modellen
- Lokales Debuggen von Machine Learning-Experimenten
- Schemabasierte Sprachunterstützung, automatische Vervollständigung und Diagnose für die Erstellung von Spezifikationsdateien
- Codeausschnitte für allgemeine Aufgaben

## <a name="prerequisites"></a>Voraussetzungen

- Azure-Abonnement. Wenn Sie keins besitzen, können Sie sich für die [kostenlose oder kostenpflichtige Version von Azure Machine Learning](https://aka.ms/AMLFree) registrieren.
- Visual Studio Code. Sollte diese Komponente noch nicht vorhanden sein, [installieren Sie sie](https://code.visualstudio.com/docs/setup/setup-overview).
- [Python](https://www.python.org/downloads/)
- (Optional) Sie müssen die 2.0 CLI installieren, wenn Sie Ressourcen mithilfe der Erweiterung erstellen möchten. Anweisungen zur Einrichtung finden Sie unter [Installieren, Einrichten und Verwenden der 2.0 CLI (Vorschau)](how-to-configure-cli.md).

## <a name="install-the-extension"></a>Installieren der Erweiterung

1. Öffnen Sie Visual Studio Code.
1. Wählen Sie in der **Aktivitätsleiste** das Symbol **Erweiterungen** aus, um die Ansicht „Erweiterungen“ zu öffnen.
1. Suchen Sie in der Ansicht „Erweiterungen“ nach „Azure Machine Learning“.
1. Wählen Sie **Installieren** aus.

    > [!div class="mx-imgBorder"]
    > ![Installieren der Azure Machine Learning-Erweiterung für VS Code](./media/how-to-setup-vs-code/install-aml-vscode-extension.PNG)

> [!NOTE]
> Alternativ können Sie die Azure Machine Learning-Erweiterung auch über den Visual Studio Marketplace installieren, indem Sie das [Installationsprogramm direkt herunterladen](https://aka.ms/vscodetoolsforai).

Die restlichen Schritte dieses Tutorials wurden mit der aktuellen Version der Erweiterung getestet.

> [!NOTE]
> Die Azure Machine Learning-Erweiterung für VS Code verwendet standardmäßig die Cli 2.0. Wenn Sie zu 1.0 CLI wechseln möchten, legen Sie die Einstellung `azureML.CLI Compatibility Mode` in Visual Studio Code auf `1.0` fest. Weitere Informationen zum Ändern Ihrer Einstellungen in Visual Studio finden Sie in der [Dokumentation zu Benutzer- und Arbeitsbereichseinstellungen](https://code.visualstudio.com/docs/getstarted/settings).

## <a name="sign-in-to-your-azure-account"></a>Anmelden bei Ihrem Azure-Konto

Zum Bereitstellen von Ressourcen und Ausführen von Workloads in Azure müssen Sie sich mit Ihren Anmeldeinformationen für Ihr Azure-Konto anmelden. Zur Unterstützung der Kontoverwaltung wird die Azure-Kontoerweiterung von Azure Machine Learning automatisch installiert. Besuchen Sie die folgende Website, um [mehr zur Azure-Kontoerweiterung zu erfahren](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account).

Wenn Sie sich beim Azure-Konto anmelden möchten, wählen Sie auf der Visual Studio Code-Statusleiste die Schaltfläche **Azure: Anmelden** aus, um den Anmeldevorgang zu starten.

Alternativ können Sie die Befehlspalette verwenden:

1. Öffnen Sie hierzu die Befehlspalette, indem Sie in der Menüleiste **Ansicht > Befehlspalette** auswählen.
1. Geben Sie in der Befehlspalette den Befehl „> Azure: Anmelden“ ein, um den Anmeldevorgang zu starten.

## <a name="choose-your-default-workspace"></a>Auswählen des Standardarbeitsbereichs

Die Auswahl eines Azure Machine Learning-Standardarbeitsbereichs ermöglicht Folgendes beim Erstellen von YAML-Spezifikationsdateien für die 2.0 CLI:

- Schemavalidierung
- Automatische Vervollständigung
- Diagnose

Wenn Sie keinen Arbeitsbereich haben, erstellen Sie einen. Weitere Informationen finden Sie unter [Verwalten von Azure Machine Learning-Ressourcen mit der VS Code-Erweiterung](how-to-manage-resources-vscode.md).

Wählen Sie zum Auswählen Ihres Standardarbeitsbereichs auf der Visual Studio Code-Statusleiste die Schaltfläche **Set Azure ML Workspace** (Azure ML-Arbeitsbereich festlegen) aus, und befolgen Sie die Anweisungen zum Festlegen Ihres Arbeitsbereichs.

Verwenden Sie alternativ den Befehl `> Azure ML: Set Default Workspace` in der Befehlspalette, und befolgen Sie die Anweisungen zum Festlegen Ihres Arbeitsbereichs.

## <a name="next-steps"></a>Nächste Schritte

- [Verwalten Ihrer Azure Machine Learning-Ressourcen](how-to-manage-resources-vscode.md)
- [Lokales Entwickeln in einer Remotecompute-Instanz](how-to-set-up-vs-code-remote.md)
- [Verwenden von Compute-Instanzen als Jupyter-Server](how-to-set-up-vs-code-remote.md)
- [Trainieren eines Bildklassifizierungsmodells unter Verwendung der Visual Studio Code-Erweiterung](tutorial-train-deploy-image-classification-model-vscode.md)
- [Lokales Ausführen und Debuggen von Machine Learning-Experimenten](how-to-debug-visual-studio-code.md)