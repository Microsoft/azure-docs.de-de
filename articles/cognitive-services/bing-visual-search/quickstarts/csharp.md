---
title: 'Schnellstart: Gewinnen von Erkenntnissen zu Bildern mit der REST-API für die visuelle Bing-Suche und C#'
titleSuffix: Azure Cognitive Services
description: Es wird beschrieben, wie Sie ein Bild in die API für die visuelle Bing-Suche hochladen und Erkenntnisse dazu gewinnen.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-visual-search
ms.topic: quickstart
ms.date: 04/26/2019
ms.author: scottwhi
ms.openlocfilehash: b1518af9c37ffe0b8175e741b363d79941e3caaf
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65905703"
---
# <a name="quickstart-get-image-insights-using-the-bing-visual-search-rest-api-and-c"></a>Schnellstart: Gewinnen von Erkenntnissen zu Bildern mit der REST-API für die visuelle Bing-Suche und C#

In dieser Schnellstartanleitung erfahren Sie, wie Sie ein Bild an die API für die visuelle Bing-Suche hochladen und die zurückgegebenen Erkenntnisse anzeigen.

## <a name="prerequisites"></a>Voraussetzungen

* Eine beliebige Edition von [Visual Studio 2019](https://www.visualstudio.com/downloads/).
* [Json.NET-Framework](https://www.newtonsoft.com/json) (verfügbar als NuGet-Paket).
* Unter Linux/macOS können Sie diese Anwendung mit [Mono](https://www.mono-project.com/) ausführen.

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](../../../../includes/cognitive-services-bing-visual-search-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Erstellen und Initialisieren eines Projekts

1. Erstellen Sie in Visual Studio eine neue Konsolenprojektmappe namens „BingSearchApisQuickStart“. Fügen Sie der Hauptcodedatei die folgenden Namespaces hinzu:

    ```csharp
    using System;
    using System.Text;
    using System.Net;
    using System.IO;
    using System.Collections.Generic;
    ```

2. Fügen Sie Variablen für Ihren Abonnementschlüssel, für den Endpunkt und für den Pfad des hochzuladenden Bilds hinzu:

    ```csharp
        const string accessKey = "<my_subscription_key>";
        const string uriBase = "https://api.cognitive.microsoft.com/bing/v7.0/images/visualsearch";
        static string imagePath = @"<path_to_image>";
    ```

3. Erstellen Sie eine Methode namens `GetImageFileName()`, um den Pfad für Ihr Bild abzurufen:
    
    ```csharp
    static string GetImageFileName(string path)
            {
                return new FileInfo(path).Name;
            }
    ```

4. Erstellen Sie eine Methode, um die Binärdaten des Bilds abzurufen:

    ```csharp
    static byte[] GetImageBinary(string path)
    {
        return File.ReadAllBytes(path);
    }
    ```

## <a name="build-the-form-data"></a>Erstellen der Formulardaten

Wenn Sie ein lokales Bild hochladen möchten, müssen Sie zunächst die Formulardaten für die Übermittlung an die API erstellen. Die Formulardaten müssen den Header `Content-Disposition` enthalten, dessen Parameter `name` muss auf „image“ festgelegt sein, und der Parameter `filename` kann auf eine beliebige Zeichenfolge festgelegt werden. Der Inhalt des Formulars umfasst die Binärdaten des Bilds. Das hochzuladende Bild darf maximal 1 MB groß sein.

    ```
    --boundary_1234-abcd
    Content-Disposition: form-data; name="image"; filename="myimagefile.jpg"
    
    ÿØÿà JFIF ÖÆ68g-¤CWŸþ29ÌÄøÖ‘º«™æ±èuZiÀ)"óÓß°Î= ØJ9á+*G¦...
    
    --boundary_1234-abcd--
    ```

1. Fügen Sie Begrenzungszeichenfolgen hinzu, um die POST-Formulardaten zu formatieren. Begrenzungszeichenfolgen dienen zum Definieren der Start-, End- und Zeilenumbruch-Zeichen für die Daten:

    ```csharp
    // Boundary strings for form data in body of POST.
    const string CRLF = "\r\n";
    static string BoundaryTemplate = "batch_{0}";
    static string StartBoundaryTemplate = "--{0}";
    static string EndBoundaryTemplate = "--{0}--";
    ```

2. Verwenden Sie die folgenden Variablen, um den Formulardaten Parameter hinzuzufügen:

    ```csharp
    const string CONTENT_TYPE_HEADER_PARAMS = "multipart/form-data; boundary={0}";
    const string POST_BODY_DISPOSITION_HEADER = "Content-Disposition: form-data; name=\"image\"; filename=\"{0}\"" + CRLF +CRLF;
    ```

3. Erstellen Sie eine Funktion namens `BuildFormDataStart()`, um den Beginn der Formulardaten zu erstellen. Verwenden Sie dabei die Begrenzungszeichenfolgen und den Bildpfad:
    
    ```csharp
        static string BuildFormDataStart(string boundary, string filename)
        {
            var startBoundary = string.Format(StartBoundaryTemplate, boundary);

            var requestBody = startBoundary + CRLF;
            requestBody += string.Format(POST_BODY_DISPOSITION_HEADER, filename);

            return requestBody;
        }
    ```

4. Erstellen Sie eine Funktion namens `BuildFormDataEnd()`, um das Ende der Formulardaten zu erstellen. Verwenden Sie dabei die Begrenzungszeichenfolgen:
    
    ```csharp
        static string BuildFormDataEnd(string boundary)
        {
            return CRLF + CRLF + string.Format(EndBoundaryTemplate, boundary) + CRLF;
        }
    ```

## <a name="call-the-bing-visual-search-api"></a>Aufrufen der API für die visuelle Bing-Suche

1. Erstellen Sie eine Funktion, um den Endpunkt für die visuelle Bing-Suche aufzurufen und die JSON-Antwort zurückzugeben. Die Funktion verwendet den Beginn und das Ende der Formulardaten, ein Bytearray mit den Bilddaten und einen Wert vom Typ `contentType`.

2. Verwenden Sie ein `WebRequest`-Objekt zum Speichern von URI, ContentType-Wert und Headern.  

3. Verwenden Sie `request.GetRequestStream()`, um Ihre Formular- und Bilddaten zu schreiben, und rufen Sie anschließend die Antwort ab. Ihre Funktion sollte in etwa wie folgt aussehen:
        
    ```csharp
        static string BingImageSearch(string startFormData, string endFormData, byte[] image, string contentTypeValue)
        {
            WebRequest request = HttpWebRequest.Create(uriBase);
            request.ContentType = contentTypeValue;
            request.Headers["Ocp-Apim-Subscription-Key"] = accessKey;
            request.Method = "POST";

            // Writes the boundary and Content-Disposition header, then writes
            // the image binary, and finishes by writing the closing boundary.
            using (Stream requestStream = request.GetRequestStream())
            {
                StreamWriter writer = new StreamWriter(requestStream);
                writer.Write(startFormData);
                writer.Flush();
                requestStream.Write(image, 0, image.Length);
                writer.Write(endFormData);
                writer.Flush();
                writer.Close();
            }

            HttpWebResponse response = (HttpWebResponse)request.GetResponseAsync().Result;
            string json = new StreamReader(response.GetResponseStream()).ReadToEnd();

            return json;
        }
    ```

## <a name="create-the-main-method"></a>Erstellen der Methode „Main“

1. Rufen Sie in der Methode `Main` Ihrer Anwendung den Dateinamen und die Binärdaten Ihres Bilds ab:

    ```csharp
    var filename = GetImageFileName(imagePath);
    var imageBinary = GetImageBinary(imagePath);
    ```

2. Richten Sie den POST-Text ein, indem Sie die entsprechende Begrenzung formatieren. Rufen Sie dann `startFormData()` und `endFormData` auf, um die Formulardaten zu erstellen:

    ```csharp
    // Set up POST body.
    var boundary = string.Format(BoundaryTemplate, Guid.NewGuid());
    var startFormData = BuildFormDataStart(boundary, filename);
    var endFormData = BuildFormDataEnd(boundary);
    ```

3. Erstellen Sie den Wert vom Typ `ContentType`, indem Sie `CONTENT_TYPE_HEADER_PARAMS` und die Begrenzung für Formulardaten formatieren:

    ```csharp
    var contentTypeHdrValue = string.Format(CONTENT_TYPE_HEADER_PARAMS, boundary);
    ```

4. Rufen Sie `BingImageSearch()` auf, um die API-Antwort abzurufen, und geben Sie die Antwort aus:

    ```csharp
    var json = BingImageSearch(startFormData, endFormData, imageBinary, contentTypeHdrValue);
    Console.WriteLine(json);
    Console.WriteLine("enter any key to continue");
    Console.readKey();
    ```

## <a name="using-httpclient"></a>Verwenden von HTTPClient

Bei Verwendung von `HttpClient` können die Formulardaten mithilfe der Klasse `MultipartFormDataContent` erstellt werden. Verwenden Sie einfach die folgenden Codeabschnitte, um die entsprechenden Methoden aus dem vorherigen Beispiel zu ersetzen.

Ersetzen Sie die Methode `Main` durch folgenden Code:

```csharp
        static void Main()
        {
            try
            {
                Console.OutputEncoding = System.Text.Encoding.UTF8;

                if (accessKey.Length == 32)
                {
                    if (IsImagePathSet(imagePath))
                    {
                        var filename = GetImageFileName(imagePath);
                        Console.WriteLine("Getting image insights for image: " + filename);
                        var imageBinary = GetImageBinary(imagePath);

                        var boundary = string.Format(BoundaryTemplate, Guid.NewGuid());
                        var json = BingImageSearch(imageBinary, boundary, uriBase, accessKey);

                        Console.WriteLine("\nJSON Response:\n");
                        Console.WriteLine(JsonPrettyPrint(json));
                    }
                }
                else
                {
                    Console.WriteLine("Invalid Bing Visual Search API subscription key!");
                    Console.WriteLine("Please paste yours into the source code.");
                }

                Console.Write("\nPress Enter to exit ");
                Console.ReadLine();
            }
            catch (Exception e)
            {
                Console.WriteLine(e.Message);
            }
        }
```

Ersetzen Sie die Methode `BingImageSearch` durch folgenden Code:

```csharp
        /// <summary>
        /// Calls the Bing visual search endpoint and returns the JSON response.
        /// </summary>
        static string BingImageSearch(byte[] image, string boundary, string uri, string subscriptionKey)
        {
            var requestMessage = new HttpRequestMessage(HttpMethod.Post, uri);
            requestMessage.Headers.Add("Ocp-Apim-Subscription-Key", accessKey);

            var content = new MultipartFormDataContent(boundary);
            content.Add(new ByteArrayContent(image), "image", "myimage");
            requestMessage.Content = content;

            var httpClient = new HttpClient();

            Task<HttpResponseMessage> httpRequest = httpClient.SendAsync(requestMessage, HttpCompletionOption.ResponseContentRead, CancellationToken.None);
            HttpResponseMessage httpResponse = httpRequest.Result;
            HttpStatusCode statusCode = httpResponse.StatusCode;
            HttpContent responseContent = httpResponse.Content;

            string json = null;

            if (responseContent != null)
            {
                Task<String> stringContentsTask = responseContent.ReadAsStringAsync();
                json = stringContentsTask.Result;
            }

            return json;
        }
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erstellen einer Single-Page-Web-App für die visuelle Suche](../tutorial-bing-visual-search-single-page-app.md)
