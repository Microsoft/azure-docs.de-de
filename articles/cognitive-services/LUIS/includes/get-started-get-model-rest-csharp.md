---
title: Abrufen eines Modells mit einem REST-Aufruf in C#
titleSuffix: Azure Cognitive Services
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: include
ms.date: 06/03/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: aaf7658796c50bb30305b5fda0527141cb820289
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/30/2021
ms.locfileid: "91534608"
---
[Referenzdokumentation](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c45) | [Beispiel](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/dotnet/LanguageUnderstanding/csharp-model-with-rest/Program.cs)

## <a name="prerequisites"></a>Voraussetzungen

* [.NET Core 3.1](https://dotnet.microsoft.com/download)
* [Visual Studio Code](https://code.visualstudio.com/)

## <a name="example-utterances-json-file"></a>JSON-Datei mit Beispieläußerungen

[!INCLUDE [Quickstart explanation of example utterance JSON file](get-started-get-model-json-example-utterances.md)]

## <a name="create-pizza-app"></a>Erstellen Sie die Pizza-App

[!INCLUDE [Create pizza app](get-started-get-model-create-pizza-app.md)]

## <a name="change-model-programmatically"></a>Programmgesteuertes Ändern des Modells

1. Erstellen Sie eine neue Konsolenanwendung für die Sprache C# mit dem Projekt- und Ordnernamen `csharp-model-with-rest`.

    ```console
    dotnet new console -lang C# -n csharp-model-with-rest
    ```

1. Wechseln Sie zum erstellten Verzeichnis `csharp-model-with-rest`, und installieren Sie erforderlichen Abhängigkeiten mit diesen Befehlen:

    ```console
    cd csharp-model-with-rest
    dotnet add package System.Net.Http
    dotnet add package JsonFormatterPlus
    ```

1. Überschreiben Sie „Program.cs“ mit dem folgenden Code:

    [!code-csharp[Code snippet](~/cognitive-services-quickstart-code/dotnet/LanguageUnderstanding/csharp-model-with-rest/Program.cs)]

1. Ersetzen Sie die Werte, die mit `YOUR-` beginnen, durch Ihre eigenen Werte.

    |Information|Zweck|
    |--|--|
    |`YOUR-APP-ID`| Ihre LUIS-App-ID. |
    |`YOUR-AUTHORING-KEY`|Ihr Erstellungsschlüssel mit 32 Zeichen.|
    |`YOUR-AUTHORING-ENDPOINT`| Ihr URL-Endpunkt für die Erstellung. Beispiel: `https://replace-with-your-resource-name.api.cognitive.microsoft.com/`. Sie haben Ihren Ressourcennamen festgelegt, als Sie die Ressource erstellt haben.|

    Zugewiesene Schlüssel und Ressourcen werden im LUIS-Portal auf der Seite **Azure-Ressourcen** im Abschnitt „Verwalten“ angezeigt. Die App-ID wird auf der Seite **Anwendungseinstellungen** ebenfalls im Abschnitt „Verwalten“ angezeigt.

1. Erstellen Sie die Konsolenanwendung.

    ```console
    dotnet build
    ```

1. Führen Sie die Konsolenanwendung aus.

    ```console
    dotnet run
    ```

1. Überprüfen Sie die Erstellungsantwort:

    ```console
    Added utterances.
    [
        {
            "value": {
                "ExampleId": 1137150691,
                "UtteranceText": "order a pizza"
            },
            "hasError": false
        },
        {
            "value": {
                "ExampleId": 1137150692,
                "UtteranceText": "order a large pepperoni pizza"
            },
            "hasError": false
        },
        {
            "value": {
                "ExampleId": 1137150693,
                "UtteranceText": "i want two large pepperoni pizzas on thin crust"
            },
            "hasError": false
        }
    ]
    Sent training request.
    {
        "statusId": 9,
        "status": "Queued"
    }
    Requested training status.
    [
        {
            "modelId": "edb46abf-0000-41ab-beb2-a41a0fe1630f",
            "details": {
                "statusId": 9,
                "status": "Queued",
                "exampleCount": 0
            }
        },
        {
            "modelId": "a5030be2-616c-4648-bf2f-380fa9417d37",
            "details": {
                "statusId": 9,
                "status": "Queued",
                "exampleCount": 0
            }
        },
        {
            "modelId": "3f2b1f31-a3c3-4fbd-8182-e9d9dbc120b9",
            "details": {
                "statusId": 9,
                "status": "Queued",
                "exampleCount": 0
            }
        },
        {
            "modelId": "e4b6704b-1636-474c-9459-fe9ccbeba51c",
            "details": {
                "statusId": 9,
                "status": "Queued",
                "exampleCount": 0
            }
        },
        {
            "modelId": "031d3777-2a00-4a7a-9323-9a3280a30000",
            "details": {
                "statusId": 9,
                "status": "Queued",
                "exampleCount": 0
            }
        },
        {
            "modelId": "9250e7a1-06eb-4413-9432-ae132ed32583",
            "details": {
                "statusId": 9,
                "status": "Queued",
                "exampleCount": 0
            }
        }
    ]
    ```

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie den Projektordner aus dem Dateisystem, nachdem Sie diese Schnellstartanleitung durchgearbeitet haben.

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Bewährte Methoden für eine App](../luis-concept-best-practices.md)
