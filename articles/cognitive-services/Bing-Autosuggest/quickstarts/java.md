---
title: 'Schnellstart: Vorschlagen von Suchabfragen mit der Bing-Vorschlagssuche-REST-API und Java'
titlesuffix: Azure Cognitive Services
description: Erfahren Sie, wie Sie schnell beginnen können, mit der Bing-Vorschlagssuche-API Suchbegriffe in Echtzeit vorzuschlagen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-autosuggest
ms.topic: quickstart
ms.date: 02/20/2019
ms.author: aahi
ms.openlocfilehash: f41e81d948bfa482a016e3770a705344f9c26b0c
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66390430"
---
# <a name="quickstart-suggest-search-queries-with-the-bing-autosuggest-rest-api-and-java"></a>Schnellstart: Vorschlagen von Suchabfragen mit der Bing-Vorschlagssuche-REST-API und Java


Verwenden Sie diese Schnellstartanleitung, um zu beginnen, die Bing-Vorschlagssuche-API aufzurufen und die JSON-Antwort zu erhalten. Diese einfache Java-Anwendung sendet eine partielle Suchabfrage an die API und gibt Vorschläge für Suchen zurück. Diese Anwendung ist zwar in Java geschrieben, an sich ist die API aber ein RESTful-Webdienst, der mit den meisten Programmiersprachen kompatibel ist. Den Quellcode des Beispiels finden Sie auf [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/java/Search/BingAutosuggestv7.java).

## <a name="prerequisites"></a>Voraussetzungen

* Das [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/)
* Die [Gson-Bibliothek](https://github.com/google/gson)

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../../includes/cognitive-services-bing-autosuggest-signup-requirements.md)]

## <a name="create-and-initialize-a-project"></a>Erstellen und Initialisieren eines Projekts

1. Erstellen Sie in Ihrer bevorzugten IDE oder in Ihrem bevorzugten Editor ein neues Java-Projekt, und importieren Sie die folgenden Bibliotheken.

    ```java
    import java.io.*;
    import java.net.*;
    import java.util.*;
    import javax.net.ssl.HttpsURLConnection;
    import com.google.gson.Gson;
    import com.google.gson.GsonBuilder;
    import com.google.gson.JsonObject;
    import com.google.gson.JsonParser;
    ```

2. Erstellen Sie Variablen für Ihren Abonnementschlüssel, den API-Host und Pfad, Ihren [Market Code](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference#market-codes) und eine Suchabfrage.
    
    ```java
    static String subscriptionKey = "enter key here";
    static String host = "https://api.cognitive.microsoft.com";
    static String path = "/bing/v7.0/Suggestions";
    static String mkt = "en-US";
    static String query = "sail";
    ```


## <a name="format-the-response"></a>Formatieren der Antwort

Erstellen Sie eine Methode mit dem Namen `prettify()`, um die von der Bing-Video-API zurückgegebene Antwort zu formatieren. Laden Sie mithilfe von `JsonParser` der Gson-Bibliothek eine JSON-Zeichenfolge, und konvertieren Sie sie in ein Objekt. Verwenden Sie dann `GsonBuilder()` und `toJson()` zum Erstellen der formatierten Zeichenfolge.

```java
// pretty-printer for JSON; uses GSON parser to parse and re-serialize
public static String prettify(String json_text) {
    JsonParser parser = new JsonParser();
    JsonObject json = parser.parse(json_text).getAsJsonObject();
    Gson gson = new GsonBuilder().setPrettyPrinting().create();
    return gson.toJson(json);
}
```

## <a name="construct-and-send-the-search-request"></a>Erstellen und Senden der Suchanforderung

1. Erstellen Sie eine neue Methode namens `get_suggestions()`, und führen Sie die folgenden Schritte aus:

   1. Erstellen Sie die URL für Ihre Anforderung, indem Sie Ihren API-Host und -Pfad kombinieren und Ihre Suchabfrage codieren. Führen Sie unbedingt eine URL-Codierung der Abfrage durch, bevor Sie sie anfügen. Erstellen Sie eine Parameterzeichenfolge für die Abfrage, indem Sie den Market Code dem `mkt=`-Parameter und Ihre Abfrage dem `q=`-Parameter anfügen.
    
      ```java
  
      public static String get_suggestions () throws Exception {
         String encoded_query = URLEncoder.encode (query, "UTF-8");
         String params = "?mkt=" + mkt + "&q=" + encoded_query;
         //...
      }
      ```
    
   2. Erstellen Sie eine neue URL für die Anforderung mit dem API-Host, dem Pfad und den oben erstellten Parametern. 
    
       ```java
       //...
       URL url = new URL (host + path + params);
       //...
       ```
    
   3. Erstellen Sie ein `HttpsURLConnection`-Objekt, und verwenden Sie `openConnection()` zum Erstellen einer Verbindung. Legen Sie die Anforderungsmethode auf `GET`, und fügen Sie Ihren Abonnementschlüssel dem Header `Ocp-Apim-Subscription-Key` hinzu.

      ```java
       //...
       HttpsURLConnection connection = (HttpsURLConnection) url.openConnection();
       connection.setRequestMethod("GET");
       connection.setRequestProperty("Ocp-Apim-Subscription-Key", subscriptionKey);
       connection.setDoOutput(true);
       //...
      ```

   4. Lesen Sie die API-Antwort in einen `StringBuilder` ein. Nachdem die Antwort erfasst wurde, schließen Sie den `InputStreamReader`-Datenstrom, und geben Sie die Antwort zurück.

       ```java
       //...
       StringBuilder response = new StringBuilder ();
       BufferedReader in = new BufferedReader(
       new InputStreamReader(connection.getInputStream()));
       String line;
       while ((line = in.readLine()) != null) {
         response.append(line);
       }
       in.close();
    
       return response.toString();
       ```

2. Rufen Sie in der main-Funktion Ihrer Anwendung `get_suggestions()` auf, und drucken Sie die Antwort mit `prettify()`.
    
    ```java
    public static void main(String[] args) {
      try {
        String response = get_suggestions ();
        System.out.println (prettify (response));
      }
      catch (Exception e) {
        System.out.println (e);
      }
    }
    ```

## <a name="example-json-response"></a>JSON-Beispielantwort

Es wird eine erfolgreiche Antwort im JSON-Format zurückgegeben, wie im folgenden Beispiel gezeigt: 

```json
{
  "_type": "Suggestions",
  "queryContext": {
    "originalQuery": "sail"
  },
  "suggestionGroups": [
    {
      "name": "Web",
      "searchSuggestions": [
        {
          "url": "https://www.bing.com/cr?IG\u003d2ACC4FE8B02F4AACB9182A6502B0E556\u0026CID\u003d1D546424A4CB64AF2D386F26A5CD6583\u0026rd\u003d1\u0026h\u003dgvtP9TS9NwhajSapY2Se6y1eCbP2fq_GiP2n-cxi6OY\u0026v\u003d1\u0026r\u003dhttps%3a%2f%2fwww.bing.com%2fsearch%3fq%3dsailrite%26FORM%3dUSBAPI\u0026p\u003dDevEx,5003.1",
          "displayText": "sailrite",
          "query": "sailrite",
          "searchKind": "WebSearch"
        },
        {
          "url": "https://www.bing.com/cr?IG\u003d2ACC4FE8B02F4AACB9182A6502B0E556\u0026CID\u003d1D546424A4CB64AF2D386F26A5CD6583\u0026rd\u003d1\u0026h\u003dBTS0G6AakxntIl9rmbDXtk1n6rQpsZZ99aQ7ClE7dTY\u0026v\u003d1\u0026r\u003dhttps%3a%2f%2fwww.bing.com%2fsearch%3fq%3dsail%2bsand%2bpoint%26FORM%3dUSBAPI\u0026p\u003dDevEx,5004.1",
          "displayText": "sail sand point",
          "query": "sail sand point",
          "searchKind": "WebSearch"
        },
        {
          "url": "https://www.bing.com/cr?IG\u003d2ACC4FE8B02F4AACB9182A6502B0E556\u0026CID\u003d1D546424A4CB64AF2D386F26A5CD6583\u0026rd\u003d1\u0026h\u003dc0QOA_j6swCZJy9FxqOwke2KslJE7ZRmMooGClAuCpY\u0026v\u003d1\u0026r\u003dhttps%3a%2f%2fwww.bing.com%2fsearch%3fq%3dsailboats%2bfor%2bsale%26FORM%3dUSBAPI\u0026p\u003dDevEx,5005.1",
          "displayText": "sailboats for sale",
          "query": "sailboats for sale",
          "searchKind": "WebSearch"
        },
        {
          "url": "https://www.bing.com/cr?IG\u003d2ACC4FE8B02F4AACB9182A6502B0E556\u0026CID\u003d1D546424A4CB64AF2D386F26A5CD6583\u0026rd\u003d1\u0026h\u003dmnMdREUH20SepmHQH1zlh9Hy_w7jpOlZFm3KG2R_BoA\u0026v\u003d1\u0026r\u003dhttps%3a%2f%2fwww.bing.com%2fsearch%3fq%3dsailing%2banarchy%26FORM%3dUSBAPI\u0026p\u003dDevEx,5006.1",
          "displayText": "sailing anarchy",
          "query": "sailing anarchy",
          "searchKind": "WebSearch"
        },
        {
          "url": "https://www.bing.com/cr?IG\u003d2ACC4FE8B02F4AACB9182A6502B0E556\u0026CID\u003d1D546424A4CB64AF2D386F26A5CD6583\u0026rd\u003d1\u0026h\u003dWLFO-B1GG5qtBGnoU1Bizz02YKkg5fgAQtHwhXn4z8I\u0026v\u003d1\u0026r\u003dhttps%3a%2f%2fwww.bing.com%2fsearch%3fq%3dsailpoint%26FORM%3dUSBAPI\u0026p\u003dDevEx,5007.1",
          "displayText": "sailpoint",
          "query": "sailpoint",
          "searchKind": "WebSearch"
        },
        {
          "url": "https://www.bing.com/cr?IG\u003d2ACC4FE8B02F4AACB9182A6502B0E556\u0026CID\u003d1D546424A4CB64AF2D386F26A5CD6583\u0026rd\u003d1\u0026h\u003dquBMwmKlGwqC5wAU0K7n416plhWcR8zQCi7r-Fw9Y0w\u0026v\u003d1\u0026r\u003dhttps%3a%2f%2fwww.bing.com%2fsearch%3fq%3dsailflow%26FORM%3dUSBAPI\u0026p\u003dDevEx,5008.1",
          "displayText": "sailflow",
          "query": "sailflow",
          "searchKind": "WebSearch"
        },
        {
          "url": "https://www.bing.com/cr?IG\u003d2ACC4FE8B02F4AACB9182A6502B0E556\u0026CID\u003d1D546424A4CB64AF2D386F26A5CD6583\u0026rd\u003d1\u0026h\u003d0udadFl0gCTKCp0QmzQTXS3_y08iO8FpwsoKPHPS6kw\u0026v\u003d1\u0026r\u003dhttps%3a%2f%2fwww.bing.com%2fsearch%3fq%3dsailboatdata%26FORM%3dUSBAPI\u0026p\u003dDevEx,5009.1",
          "displayText": "sailboatdata",
          "query": "sailboatdata",
          "searchKind": "WebSearch"
        },
        {
          "url": "https://www.bing.com/cr?IG\u003d2ACC4FE8B02F4AACB9182A6502B0E556\u0026CID\u003d1D546424A4CB64AF2D386F26A5CD6583\u0026rd\u003d1\u0026h\u003deSSt0MRSbl2V0RFPSuVd-gC7fGOT4717pz55EBUgPec\u0026v\u003d1\u0026r\u003dhttps%3a%2f%2fwww.bing.com%2fsearch%3fq%3dsailor%2b2025%26FORM%3dUSBAPI\u0026p\u003dDevEx,5010.1",
          "displayText": "sailor 2025",
          "query": "sailor 2025",
          "searchKind": "WebSearch"
        }
      ]
    }
  ]
}
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erstellen einer Single-Page-Web-App](../tutorials/autosuggest.md)

- [Worum handelt es sich bei der Bing-Vorschlagssuche?](../get-suggested-search-terms.md)
- [Referenz für die Bing-Vorschlagssuche-API V7](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-autosuggest-api-v7-reference)
