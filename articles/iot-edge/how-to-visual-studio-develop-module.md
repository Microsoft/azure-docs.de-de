---
title: Entwickeln und Debuggen von Modulen in Visual Studio – Azure IoT Edge | Microsoft-Dokumentation
description: Entwickeln und Debuggen von Modulen für Azure IoT Edge mithilfe von Visual Studio 2019
services: iot-edge
author: shizn
manager: philmea
ms.author: xshi
ms.date: 05/27/2019
ms.topic: article
ms.service: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 4014827366afc492d73757a0ac5e1acb64262c51
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66474785"
---
# <a name="use-visual-studio-2019-to-develop-and-debug-modules-for-azure-iot-edge-preview"></a>Entwickeln und Debuggen von Modulen für Azure IoT Edge mithilfe von Visual Studio 2019 (Vorschau)

Sie können Ihre Geschäftslogik in Azure IoT Edge-Module umwandeln. In diesem Artikel erfahren Sie, wie Sie Visual Studio 2019 als Haupttool zum Entwickeln und Debuggen von Modulen verwenden.

Die Azure IoT Edge-Tools für Visual Studio bietet die folgenden Vorteile:

- Sie können Azure IoT Edge-Lösungen und -Module auf Ihrem lokalen Entwicklungscomputer erstellen, bearbeiten, kompilieren, ausführen und debuggen.
- Sie können Ihre Azure IoT Edge-Lösung über den Azure IoT Hub auf einem Azure IoT Edge-Gerät bereitstellen.
- Sie können Ihre Azure IoT-Module in C oder C# programmieren und dabei von sämtlichen Vorteilen der Entwicklung mit Visual Studio profitieren.
- Sie können Ihre Azure IoT Edge-Geräte und -Module in der Benutzeroberfläche verwalten.

In diesem Artikel erfahren Sie, wie Sie Ihre IoT Edge-Module mithilfe der Azure IoT Edge-Tools für Visual Studio 2019 entwickeln. Außerdem erfahren Sie, wie Sie Ihr Projekt auf Ihrem Azure IoT Edge-Gerät bereitstellen.

> [!TIP]
> Die von Visual Studio erstellte Struktur eines IoT Edge-Projekts ist nicht die gleiche wie in Visual Studio Code.
  
## <a name="prerequisites"></a>Voraussetzungen

In diesem Artikel wird davon ausgegangen, dass Sie einen Computer oder virtuellen Computer unter Windows als Entwicklungscomputer verwenden. Ihr IoT Edge-Gerät kann ein anderes physisches Gerät sein.

Da in diesem Artikel Visual Studio 2019 als Hauptentwicklungstool verwendet wird, installieren Sie Visual Studio. Schließen Sie die Workloads **Azure-Entwicklung** und **Desktopentwicklung mit C++** in Ihre Visual Studio 2019-Installation ein. Sie können [Visual Studio 2019 ändern](https://docs.microsoft.com/visualstudio/install/modify-visual-studio?view=vs-2019), um die erforderlichen Workloads hinzuzufügen.

Wenn Visual Studio 2019 bereit ist, benötigen Sie auch die folgenden Tools und Komponenten:

- Laden Sie die [Azure IoT Edge-Tools (Vorschau)](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools) vom Visual Studio Marketplace herunter, und installieren Sie sie, um ein IoT Edge-Projekt in Visual Studio 2019 erstellen zu können.

> [!TIP]
> Wenn Sie Visual Studio 2017 verwenden, laden Sie die [Azure IoT Edge-Tools (Vorschau)](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vsiotedgetools) für VS 2017 vom Visual Studio Marketplace herunter, und installieren Sie sie.

- Laden Sie die [Docker Community Edition](https://docs.docker.com/install/) auf Ihren Entwicklungscomputer herunter, und installieren Sie sie, um Ihre Modulimages erstellen und ausführen zu können. Sie müssen für die Docker CE festlegen, dass sie entweder im Linux-Containermodus oder im Windows-Containermodus ausgeführt wird.

- Richten Sie Ihre lokale Entwicklungsumgebung zum Debuggen, Ausführen und Testen Ihrer IoT Edge-Lösung ein, indem Sie das [Entwicklertool für Azure IoT EdgeHub](https://pypi.org/project/iotedgehubdev/) installieren. Installieren Sie [Python (2.7/3.6) und Pip](https://www.python.org/) und anschließend das **iotedgehubdev**-Paket, indem Sie in Ihrem Terminal den folgenden Befehl ausführen. Stellen Sie sicher, dass Sie eine Version des Azure IoT EdgeHub-Entwicklertools von höher als 0.3.0 verwenden.

   ```cmd
   pip install --upgrade iotedgehubdev
   ```

- Klonen Sie das Repository, installieren Sie den vcpkg-Bibliotheks-Manager, und installieren Sie anschließend das Paket **azure-iot-sdk-c** für Windows.

  ```cmd
  git clone https://github.com/Microsoft/vcpkg
  cd vcpkg
  bootstrap-vcpkg.bat
  ```

  ```cmd
  vcpkg.exe install azure-iot-sdk-c:x64-windows
  vcpkg.exe --triplet x64-windows integrate install
  ```

- [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) oder [Docker Hub](https://docs.docker.com/docker-hub/repos/#viewing-repository-tags).

  > [!TIP]
  > Sie können anstelle einer Cloudregistrierung auch eine lokale Docker-Registrierung für Prototyp- bzw. Testzwecke verwenden.

- Zum Testen Ihres Moduls auf einem Gerät benötigen Sie einen aktiven IoT Hub mit mindestens einem IoT Edge-Gerät. Um Ihren Computer als IoT Edge-Gerät zu verwenden, führen Sie die Schritte in der Schnellstartanleitung für [Linux](quickstart-linux.md) oder [Windows](quickstart.md) aus. Wenn Sie den IoT Edge-Daemon auf Ihrem Entwicklungscomputer ausführen, müssen Sie möglicherweise EdgeHub und EdgeAgent beenden, bevor Sie mit der Entwicklung in Visual Studio beginnen.

### <a name="check-your-tools-version"></a>Überprüfen Ihrer Tools-Version

1. Wählen Sie im Menü **Extras** die Option **Erweiterungen und Updates** aus. Erweitern Sie **Installiert > Tools**. Dort finden Sie **Azure IoT Edge-Tools** und **Cloud-Explorer für Visual Studio**.

1. Notieren Sie sich die installierte Version. Sie können diese Version mit der neuesten Version in Visual Studio Marketplace vergleichen ([Cloud-Explorer](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.CloudExplorerForVS2019), [Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16iotedgetools)).

1. Wenn Ihre Version älter ist als die Versionen, die auf dem Visual Studio Marketplace zur Verfügung stehen, aktualisieren Sie Ihre Tools in Visual Studio, wie im folgenden Abschnitt gezeigt wird.

### <a name="update-your-tools"></a>Aktualisieren Ihrer Tools

1. Erweitern Sie im Dialogfeld **Erweiterungen und Updates** die Option **Updates > Visual Studio Marketplace**, wählen Sie **Azure IoT Edge-Tools** oder **Cloud-Explorer für Visual Studio** und dann **Aktualisieren** aus.

1. Nachdem das Toolupdate heruntergeladen wurde, schließen Sie Visual Studio, damit die Tools mit dem VSIX-Installationsprogramm aktualisiert werden.

1. Wählen Sie im Installationsprogramm **OK** aus, um den Vorgang zu starten, und dann **Ändern**, um die Tools zu aktualisieren.

1. Nachdem das Update abgeschlossen ist, wählen Sie **Schließen** aus, und starten Sie Visual Studio neu.

### <a name="create-an-azure-iot-edge-project"></a>Erstellen eines Azure IoT Edge-Projekts

Die Azure IoT Edge-Projektvorlage in Visual Studio erstellt ein Projekt, das auf Azure IoT Edge-Geräten im Azure IoT Hub bereitgestellt werden kann. Sie erstellen zunächst eine Azure IoT Edge-Lösung und generieren anschließend das erste Modul in dieser Lösung. Jede IoT Edge-Lösung kann mehrere Module enthalten.

1. Suchen Sie im Visual Studio-Dialogfeld „Neues Projekt“ das Projekt **Azure IoT Edge**, wählen Sie es aus, und klicken Sie auf **Weiter**. Im Fenster zum Konfigurieren des Projekts geben Sie einen Namen und den Speicherort für Ihr Projekt ein, und wählen Sie dann **Erstellen** aus. Der Standardname für das Projekt ist **AzureIoTEdgeApp1**.

   ![Erstellen eines neuen Projekts](./media/how-to-visual-studio-develop-csharp-module/create-new.png)

1. Wählen Sie im Fenster **IoT Edge-Anwendung und -Modul hinzufügen** entweder **C#-Modul** oder **C++-Modul** aus, und geben Sie den Namen und das Imagerepository für Ihr Modul an. Visual Studio füllt den Modulnamen automatisch mit **localhost:5000/<Name Ihres Moduls\>** aus. Ersetzen Sie ihn durch Ihre eigenen Registrierungsinformationen. Wenn Sie eine lokale Docker-Registrierung zum Testen verwenden, können Sie **localhost** nutzen. Nutzen Sie den Anmeldeserver aus Ihren Registrierungseinstellungen, wenn Sie Azure Container Registry verwenden. Der Anmeldeserver hat die Form * **\<Registrierungsname\>*.azurecr.io**. Ersetzen Sie nur den Teil **localhost:5000** der Zeichenfolge, damit das endgültige Ergebnis die folgende Form hat: * *\<* Registrierungsname *\>.azurecr.io/* \<Name Ihres Moduls\>***. Der Standardname für das Modul ist **IoTEdgeModule1**.

   ![Anwendung und Modul hinzufügen](./media/how-to-visual-studio-develop-csharp-module/add-application-and-module.png)

1. Wählen Sie **OK** aus, um die Azure IoT Edge-Lösung mit einem Modul zu erstellen, das entweder C# oder C verwendet.

Ihre Lösung enthält nun das Projekt **AzureIoTEdgeApp1.Linux.Amd64** oder das Projekt **AzureIoTEdgeApp1.Windows.Amd64** sowie das Projekt **IoTEdgeModule1**. Jedes Projekt vom Typ **AzureIoTEdgeApp1** enthält eine Datei namens `deployment.template.json`. Diese Datei definiert die Module, die Sie für Ihre IoT Edge-Lösung erstellen und bereitstellen möchten, sowie die Routen zwischen Modulen. Die Standardlösung besteht aus einem **TempSensor**-und einem **IoTEdgeModule1**-Modul. Das **TempSensor**-Modul generiert simulierte Daten für das **IoTEdgeModule1**-Modul, und der Standardcode im **IoTEdgeModule1**-Modul sendet empfangene Nachrichten direkt an den Azure IoT Hub.

Das Projekt **IoTEdgeModule1** ist eine .NET Core 2.1-Konsolenanwendung, wenn es sich um ein C#-Modul handelt. Es enthält die erforderlichen Dockerfiles, die Sie für Ihr IoT Edge-Gerät benötigen, das entweder mit einem Windows-Container oder einem Linux-Container läuft. Die Datei `module.json` beschreibt die Metadaten eines Moduls. Der eigentliche Modulcode, der das Azure IoT-Geräte-SDK als Abhängigkeit verwendet, befindet sich in der Datei `Program.cs` oder `main.c`.

## <a name="develop-your-module"></a>Entwickeln Ihres Moduls

Der in der Lösung enthaltene Standardmodulcode befindet sich unter **IoTEdgeModule1** > **Program.cs** (C#) oder **main.c** (C). Das Modul und die Datei `deployment.template.json` werden so eingerichtet, dass Sie die Lösung erstellen, in Ihre Containerregistrierung verschieben und zum Starten des Tests für ein Gerät bereitstellen können, ohne Code ändern zu müssen. Das Modul ist so konzipiert, dass es eine Eingabe aus einer Quelle akzeptiert (in diesem Fall das Daten simulierende **tempSensor**-Modul) und an den Azure IoT Hub sendet.

Wenn Sie bereit sind, die Modulvorlage mit Ihrem eigenen Code anzupassen, erstellen Sie mit den [Azure IoT Hub SDKs](../iot-hub/iot-hub-devguide-sdks.md) Module, die die wesentlichen Anforderungen für IoT-Lösungen wie Sicherheit, Geräteverwaltung und Zuverlässigkeit berücksichtigen.

## <a name="initialize-iotedgehubdev-with-iot-edge-device-connection-string"></a>Initialisieren von „iotedgehubdev“ mit der IoT Edge-Geräteverbindungszeichenfolge

1. Kopieren Sie die Verbindungszeichenfolge eines beliebigen IoT Edge-Geräts aus **primäre Verbindungszeichenfolge** in den Visual Studio Cloud-Explorer. Kopieren Sie nicht die Verbindungszeichenfolge eines Nicht-Edge-Geräts, da das Symbol eines IoT Edge-Geräts anders als das Symbol eines Nicht-Edge-Geräts ist.

   ![Kopieren der Edge-Geräteverbindungszeichenfolge](./media/how-to-visual-studio-develop-csharp-module/copy-edge-conn-string.png)

1. Wechseln Sie zu **Tools** > **Azure IoT Edge-Tools** > **IoT Edge Simulator einrichten**, fügen Sie die Verbindungszeichenfolge ein, und klicken Sie auf **OK**.

   ![Öffnen des Fensters „Set Edge Device Connection String“ (Edge-Geräteverbindungszeichenfolge festlegen)](./media/how-to-visual-studio-develop-csharp-module/set-edge-conn-string.png)

1. Geben Sie die Verbindungszeichenfolge aus dem ersten Schritt ein, und wählen Sie **OK** aus.

> [!NOTE]
> Sie müssen diese Schritte auf Ihrem Entwicklungscomputer nur einmal ausführen, weil die Ergebnisse auf alle nachfolgenden Azure IoT Edge-Lösungen automatisch angewendet werden. Diese Schritte können erneut ausgeführt werden, wenn Sie in eine andere Verbindungszeichenfolge ändern müssen.

## <a name="build-and-debug-single-module"></a>Erstellen und Debuggen eines einzelnen Moduls

In der Regel möchten Sie jedes Modul testen und debuggen, bevor Sie es in einer Gesamtlösung mit mehreren Modulen ausführen.

1. Klicken Sie mit der rechten Maustaste auf **IoTEdgeModule1**, und wählen Sie im Kontextmenü **Als Startprojekt festlegen** aus.

   ![Startprojekt festlegen](./media/how-to-visual-studio-develop-csharp-module/module-start-up-project.png)

1. Drücken Sie **F5**, oder klicken Sie auf die Schaltfläche unten, um das Modul auszuführen. Beim ersten Mal kann dies 10&ndash;20 Sekunden dauern.

   ![Modul ausführen](./media/how-to-visual-studio-develop-csharp-module/run-module.png)

1. Wenn das Modul erfolgreich initialisiert wurde, sollte eine .NET Core-Konsolenanwendung gestartet werden.

   ![Modul wird ausgeführt](./media/how-to-visual-studio-develop-csharp-module/single-module-run.png)

1. Legen Sie bei Verwendung von C# in **Program.cs** einen Haltepunkt in der Funktion `PipeMessage()` fest. Bei Verwendung von C muss der Haltepunkt in **main.c** in der Funktion `InputQueue1Callback()` festgelegt werden. Zu Testzwecken können Sie eine Nachricht senden. Führen Sie dazu in einer **Git Bash**- oder **WSL Bash**-Shell den folgenden Befehl aus. (Den `curl`-Befehl können Sie nicht aus einer PowerShell oder Eingabeaufforderung ausführen.)

    ```bash
    curl --header "Content-Type: application/json" --request POST --data '{"inputName": "input1","data":"hello world"}' http://localhost:53000/api/v1/messages
    ```

   ![Debuggen eines Einzelmoduls](./media/how-to-visual-studio-develop-csharp-module/debug-single-module.png)

    Es sollte ein Breakpoint ausgelöst werden. Sie können Variablen in Visual Studio im Fenster **Lokal** ansehen.

   > [!TIP]
   > Sie können auch [PostMan](https://www.getpostman.com/) oder andere API-Tools (anstelle von `curl`) zum Senden von Nachrichten verwenden.

1. Drücken Sie zum Beenden des Debuggen auf **STRG + F5** oder klicken Sie auf die Schaltfläche „Beenden“.

## <a name="build-and-debug-iot-edge-solution-with-multiple-modules"></a>Erstellen und Debuggen einer IoT Edge-Lösung mit mehreren Modulen

Nachdem Sie ein Einzelmodul entwickelt haben, möchten Sie jetzt vielleicht eine Gesamtlösung mit mehreren Modulen ausführen und debuggen.

1. Fügen Sie der Lösung ein zweites Modul hinzu, indem Sie mit der rechten Maustaste auf **AzureIoTEdgeApp1** klicken und **Hinzufügen** > **Neues IoT Edge-Modul** auswählen. Der Standardname des zweiten Moduls lautet **IoTEdgeModule2**, und es fungiert als ein weiteres Pipemodul.

1. Öffnen Sie die Datei `deployment.template.json`. Dann sehen Sie, dass **IoTEdgeModule2** im Abschnitt **Module** hinzugefügt wurde. Ersetzen Sie den Abschnitt **routes** durch Folgendes: Wenn Sie Ihre Modulnamen angepasst haben, stellen Sie sicher, dass Sie diese Namen entsprechend aktualisieren.

    ```json
        "routes": {
          "IoTEdgeModule1ToIoTHub": "FROM /messages/modules/IoTEdgeModule1/outputs/* INTO $upstream",
          "sensorToIoTEdgeModule1": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IoTEdgeModule1/inputs/input1\")",
          "IoTEdgeModule2ToIoTHub": "FROM /messages/modules/IoTEdgeModule2/outputs/* INTO $upstream",
          "sensorToIoTEdgeModule2": "FROM /messages/modules/tempSensor/outputs/temperatureOutput INTO BrokeredEndpoint(\"/modules/IoTEdgeModule2/inputs/input1\")"
        },
    ```

1. Klicken Sie mit der rechten Maustaste auf **AzureIoTEdgeApp1**, und wählen Sie im Kontextmenü **Als Startprojekt festlegen** aus.

1. Erstellen Sie Ihre Breakpoints, und drücken Sie **F5**, um mehrere Module gleichzeitig auszuführen und zu debuggen. Daraufhin sollten mehrere Fenster der .NET Core-Konsolen-App angezeigt werden, wobei jedes Fenster ein anderes Modul darstellt.

   ![Debuggen mehrerer Module](./media/how-to-visual-studio-develop-csharp-module/debug-multiple-modules.png)

1. Drücken Sie zum Beenden des Debuggens **STRG+F5**, oder wählen Sie die Schaltfläche „Beenden“ aus.

## <a name="build-and-push-images"></a>Erstellen und Übermitteln des Images mithilfe von Push

1. Stellen Sie sicher, dass **AzureIoTEdgeApp1** das Startprojekt ist. Wählen Sie entweder **Debuggen** oder **Version** als Konfiguration zum Kompilieren Ihrer Modulimages aus.

    > [!NOTE]
    > Bei der Auswahl von **Debuggen** verwendet Visual Studio `Dockerfile.(amd64|windows-amd64).debug` zum Erstellen von Docker-Images. Dies bindet den .NET Core-Befehlszeilendebugger VSDBG beim Erstellen in Ihr Containerimage ein. Wir empfehlen Ihnen, für produktionsbereite IoT Edge-Module die Konfiguration **Version** zu verwenden, die `Dockerfile.(amd64|windows-amd64)` ohne VSDBG verwendet.

1. Wenn Sie eine private Registrierung wie Azure Container Registry verwenden, melden Sie sich dort mit dem folgenden Docker-Befehl an. Wenn Sie eine lokale Registrierung verwenden, können Sie [eine lokale Registrierung ausführen](https://docs.docker.com/registry/deploying/#run-a-local-registry).

    ```cmd
    docker login -u <ACR username> -p <ACR password> <ACR login server>
    ```

1. Wenn Sie eine private Registrierung wie Azure Container Registry verwenden, müssen Sie Ihre Anmeldeinformationen für die Registrierung den Laufzeiteinstellungen hinzufügen, die in der Datei `deployment.template.json` enthalten sind. Ersetzen Sie die Platzhalter durch Ihren tatsächlichen Benutzernamen, das Kennwort und den Registrierungsnamen des ACR-Administrators.

    ```json
          "settings": {
            "minDockerVersion": "v1.25",
            "loggingOptions": "",
            "registryCredentials": {
              "registry1": {
                "username": "<username>",
                "password": "<password>",
                "address": "<registry name>.azurecr.io"
              }
            }
          }
    ```

1. Klicken Sie mit der rechten Maustaste auf **AzureIoTEdgeApp1**, und wählen Sie die Option **Edge-Lösung kompilieren und pushen** aus, um das Docker-Image für jedes Modul zu kompilieren und zu pushen.

   ![Erstellen und des Images mithilfe von Push](./media/how-to-visual-studio-develop-csharp-module/build-and-push.png)

## <a name="deploy-the-solution"></a>Bereitstellen der Lösung

Im Schnellstartartikel, anhand dessen Sie Ihr IoT Edge-Gerät eingerichtet haben, haben Sie ein Modul über das Azure-Portal bereitgestellt. Sie können Module auch mit dem Cloud-Explorer für Visual Studio bereitstellen. Weil Sie ein Bereitstellungsmanifest für Ihr Szenario und die Datei `deployment.json` schon vorbereitet haben, müssen Sie lediglich ein Gerät auswählen, um die Bereitstellung zu empfangen.

1. Öffnen Sie den **Cloud-Explorer** durch Klicken auf **Ansicht** > **Cloud-Explorer**. Vergewissern Sie sich, dass Sie bei Visual Studio 2019 angemeldet sind.

1. Erweitern Sie in **Cloud-Explorer** Ihr Abonnement, suchen Sie Ihren Azure IoT Hub und das Azure IoT Edge-Gerät, das Sie bereitstellen möchten.

1. Klicken Sie mit der rechten Maustaste auf das IoT Edge-Gerät, um eine Bereitstellung dafür zu erstellen. Sie müssen unter `$AzureIoTEdgeAppSolutionDir\config\deployment.(amd64|amd64.debug|windows-amd64).json` die Bereitstellungsmanifestdatei auswählen.

   > [!NOTE]
   > Wählen Sie auf keinen Fall `$AzureIoTEdgeAppSolutionDir\config\deployment_for_local_debug.json` aus.

1. Klicken Sie auf die Schaltfläche „Aktualisieren“, um sich anzusehen, wie die neuen Module zusammen mit dem **TempSensor**-Modul sowie **$edgeAgent** und **$edgeHub** ausgeführt werden.

## <a name="view-generated-data"></a>Anzeigen generierter Daten

1. Um die D2C-Nachricht für ein bestimmtes Gerät zu überwachen, wählen Sie dieses Gerät in der Liste aus, und klicken Sie im Fenster **Aktion** auf **Überwachen des integrierten Ereignisendpunkts starten**.

1. Um die Überwachung von Daten zu beenden, wählen Sie das Gerät in der Liste aus. Wählen Sie dann im Fenster **Aktion** die Option **Überwachen des integrierten Ereignisendpunkts beenden** aus.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zum Entwickeln von benutzerdefinierten Modulen für Ihre IoT Edge-Geräte finden Sie unter [Verstehen und Verwenden von Azure IoT Hub SDKs](../iot-hub/iot-hub-devguide-sdks.md).
