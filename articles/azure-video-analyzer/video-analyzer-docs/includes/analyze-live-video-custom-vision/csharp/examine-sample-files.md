---
author: Juliako
ms.service: azure-video-analyzer
ms.topic: include
ms.date: 04/30/2021
ms.author: juliako
ms.openlocfilehash: b5d950aca0090067d426457cc09495fd0b8344b5
ms.sourcegitcommit: 58e5d3f4a6cb44607e946f6b931345b6fe237e0e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/25/2021
ms.locfileid: "110383746"
---
1. Navigieren Sie in Visual Studio Code zu „src/edge“. Hier befinden sich die erstellte ENV-Datei sowie einige Bereitstellungsvorlagendateien.

   Die Bereitstellungsvorlage verweist mit einigen Platzhalterwerten auf das Bereitstellungsmanifest für das Edgegerät. Die ENV-Datei enthält die Werte für diese Variablen.
2. Navigieren Sie als Nächstes zum Ordner „src/cloud-to-device-console-app“. Hier befindet sich die Datei „appsettings.json“, die Sie zusammen mit folgenden anderen Dateien erstellt haben:

   - c2d-console-app.csproj: Dies ist die Projektdatei für Visual Studio Code.
   - operations.json: In dieser Datei sind die verschiedenen Vorgänge aufgelistet, die vom Programm ausgeführt werden sollen.
   - Program.cs: Dieses Beispiel für den Programmcode führt Folgendes aus:
     - Laden der App-Einstellungen
     - Aufrufen der direkten Methoden des Azure Video Analyzer-Moduls zum Erstellen einer Topologie sowie zum Instanziieren und Aktivieren der Pipeline
     - Anhalten der Ausführung, damit Sie die Ausgabe der Pipeline im **Terminalfenster** und die an den IoT-Hub gesendeten Ereignisse im **Ausgabefenster** untersuchen können
     - Deaktivieren der Livepipeline, Löschen der Livepipeline und Löschen der Topologie
