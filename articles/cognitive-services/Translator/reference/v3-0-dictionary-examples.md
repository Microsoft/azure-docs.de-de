---
title: Textübersetzungs-API – Methode für Wörterbuchbeispiele
titlesuffix: Azure Cognitive Services
description: Verwenden Sie die Methode für Wörterbuchbeispiele der Textübersetzungs-API.
services: cognitive-services
author: rajdeep-in
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 03/29/2018
ms.author: v-pawal
ms.openlocfilehash: 526fc3e51691773e27530a0fa775f4f07ee94acd
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66389769"
---
# <a name="translator-text-api-30-dictionary-examples"></a>Textübersetzungs-API 3.0: Wörterbuchbeispiele

Im Folgenden finden Sie Kontextbeispiele zur Verwendungen von Begriffen im Wörterbuch. Dieser Vorgang wird zusammen mit der [Wörterbuchsuche](./v3-0-dictionary-lookup.md) verwendet.

## <a name="request-url"></a>Anfrage-URL

Sendet eine `POST`-Anforderung an:

```HTTP
https://api.cognitive.microsofttranslator.com/dictionary/examples?api-version=3.0
```

## <a name="request-parameters"></a>Anforderungsparameter

Die folgenden Anforderungsparameter werden in der Abfragezeichenfolge übergeben:

<table width="100%">
  <th width="20%">Query parameter (Abfrageparameter)</th>
  <th>BESCHREIBUNG</th>
  <tr>
    <td>api-version</td>
    <td>*Erforderlicher Parameter*.<br/>Die vom Client angeforderte Version der API. Der Wert muss `3.0` sein.</td>
  </tr>
  <tr>
    <td>from</td>
    <td>*Erforderlicher Parameter*.<br/>Gibt die Sprache des Eingabetexts an. Sie müssen eine der zum `dictionary`-Bereich hinzugefügten [unterstützten Sprachen](./v3-0-languages.md) als Quellsprache auswählen.</td>
  </tr>
  <tr>
    <td>zu</td>
    <td>*Erforderlicher Parameter*.<br/>Gibt die Sprache des Ausgabetexts an. Sie müssen eine der zum `dictionary`-Bereich hinzugefügten [unterstützten Sprachen](./v3-0-languages.md) als Zielsprache auswählen.</td>
  </tr>
</table>

Anforderungsheader enthalten Folgendes:

<table width="100%">
  <th width="20%">Header</th>
  <th>BESCHREIBUNG</th>
  <tr>
    <td>Authentifizierungsheader</td>
    <td><em>Erforderlicher Anforderungsheader</em>.<br/>Weitere Informationen finden Sie in den <a href="https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference#authentication">verfügbaren Optionen für die Authentifizierung</a>.</td>
  </tr>
  <tr>
    <td>Content-Typ</td>
    <td>*Erforderlicher Anforderungsheader*.<br/>Gibt den Inhaltstyp der Nutzlast an. Mögliche Werte: `application/json`.</td>
  </tr>
  <tr>
    <td>Content-Length</td>
    <td>*Erforderlicher Anforderungsheader*.<br/>Die Länge des Anforderungstexts.</td>
  </tr>
  <tr>
    <td>X-ClientTraceId</td>
    <td>*Optional*.<br/>Eine vom Client erstellte GUID zur eindeutigen Identifizierung der Anforderung. Sie können diesen Header nur weglassen, wenn Sie die Ablaufverfolgungs-ID in die Abfragezeichenfolge über einen Abfrageparameter namens `ClientTraceId` einschließen.</td>
  </tr>
</table> 

## <a name="request-body"></a>Anforderungstext

Der Anforderungstext ist ein JSON-Array. Jedes Arrayelement ist ein JSON-Objekt mit den folgenden Zeichenfolgeneigenschaften:

  * `Text`: Eine Zeichenfolge, die den Begriff angibt, der nachgeschlagen werden soll. Es sollte sich dabei um den Wert eines `normalizedText`-Felds handeln, der durch die Rückübersetzungen einer vorherigen [Wörterbuchsuchanforderung](./v3-0-dictionary-lookup.md) entstanden ist. Es kann sich auch um den Wert des `normalizedSource`-Felds handeln.

  * `Translation`: Eine Zeichenfolge, die den übersetzten Text angibt, der zuvor vom [Wörterbuchsuchvorgang](./v3-0-dictionary-lookup.md) zurückgegeben wurde. Es sollte sich dabei um den Wert aus dem `normalizedTarget`-Feld der `translations`-Liste der Antwort der [Wörterbuchsuche](./v3-0-dictionary-lookup.md) handeln. Dieser Dienst gibt Beispiele für ein bestimmtes Quelle-Ziel-Wortpaar zurück.

Im folgenden Code wird ein Beispiel veranschaulicht:

```json
[
    {"Text":"fly", "Translation":"volar"}
]
```

Es gelten die folgenden Einschränkungen:

* Das Array darf höchstens 10 Elemente enthalten.
* Der Textwert eines Arrayelements kann 100 Zeichen (einschließlich Leerzeichen) nicht überschreiten.

## <a name="response-body"></a>Antworttext

Eine erfolgreiche Antwort ist ein JSON-Array mit einem Ergebnis für jede Zeichenfolge im Eingabearray. Ein Ergebnisobjekt enthält die folgenden Eigenschaften:

  * `normalizedSource`: Eine Zeichenfolge, die die normalisierte Form des Ausgangsbegriffs angibt. In der Regel sollte dieser Wert mit dem Wert des `Text`-Felds übereinstimmen, das Bestandteil des entsprechenden Listenindex im Anforderungstext ist.
    
  * `normalizedTarget`: Eine Zeichenfolge, die die normalisierte Form des Zielbegriffs angibt. In der Regel sollte dieser Wert mit dem Wert des `Translation`-Felds übereinstimmen, das Bestandteil des entsprechenden Listenindex im Anforderungstext ist.
  
  * `examples`: Eine Liste von Beispielen für das Begriffspaar (Begriff in der Ausgangssprache + Begriff in der Zielsprache). Jedes Listenelement ist ein Objekt mit den folgenden Zeichenfolgeneigenschaften:

    * `sourcePrefix`: Die Zeichenfolge, die _vor_ dem Wert von `sourceTerm` verkettet werden soll, um ein vollständiges Beispiel zu bilden. Fügen Sie kein Leerzeichen hinzu, da erforderliche Leerzeichen bereits hinzugefügt worden sein sollten. Bei diesem Wert kann es sich auch um eine leere Zeichenfolge handeln.

    * `sourceTerm`: Eine Zeichenfolge, die dem tatsächlich nachgeschlagenen Begriff entspricht. Die Zeichenfolge wird durch `sourcePrefix` und `sourceSuffix` hinzugefügt, um ein vollständiges Beispiel zu erstellen. Der Wert der Zeichenfolge wird unterteilt, sodass dieser auf einer Benutzeroberfläche z.B. durch Fettformatierung markiert werden kann.

    * `sourceSuffix`: Die Zeichenfolge, die _hinter_ dem Wert von `sourceTerm` verkettet werden soll, um ein vollständiges Beispiel zu bilden. Fügen Sie kein Leerzeichen hinzu, da erforderliche Leerzeichen bereits hinzugefügt worden sein sollten. Bei diesem Wert kann es sich auch um eine leere Zeichenfolge handeln.

    * `targetPrefix`: Eine Zeichenfolge, die `sourcePrefix` ähnelt, aber dem Ziel zugewiesen ist.

    * `targetTerm`: Eine Zeichenfolge, die `sourceTerm` ähnelt, aber dem Ziel zugewiesen ist.

    * `targetSuffix`: Eine Zeichenfolge, die `sourceSuffix` ähnelt, aber dem Ziel zugewiesen ist.

    > [!NOTE]
    > Wenn es keine Beispiele im Wörterbuch gibt, lautet die Antwort zwar „200 (OK)“, aber die `examples`-Liste ist leer.

## <a name="examples"></a>Beispiele

Das folgende Beispiel zeigt, wie Sie Beispiele für das Paar nachschlagen, das aus dem englischen Begriff `fly` und dessen spanischer Übersetzung `volar` besteht.

# <a name="curltabcurl"></a>[curl](#tab/curl)

```
curl -X POST "https://api.cognitive.microsofttranslator.com/dictionary/examples?api-version=3.0&from=en&to=es" -H "Ocp-Apim-Subscription-Key: <client-secret>" -H "Content-Type: application/json" -d "[{'Text':'fly', 'Translation':'volar'}]"
```

---

Dann wird folgende Antwort zurückgegeben (zur besseren Veranschaulichung gekürzt):

```
[
    {
        "normalizedSource":"fly",
        "normalizedTarget":"volar",
        "examples":[
            {
                "sourcePrefix":"They need machines to ",
                "sourceTerm":"fly",
                "sourceSuffix":".",
                "targetPrefix":"Necesitan máquinas para ",
                "targetTerm":"volar",
                "targetSuffix":"."
            },      
            {
                "sourcePrefix":"That should really ",
                "sourceTerm":"fly",
                "sourceSuffix":".",
                "targetPrefix":"Eso realmente debe ",
                "targetTerm":"volar",
                "targetSuffix":"."
            },
            //
            // ...list abbreviated for documentation clarity
            //
        ]
    }
]
```
