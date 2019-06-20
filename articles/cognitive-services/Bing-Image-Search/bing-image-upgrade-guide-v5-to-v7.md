---
title: Upgrade der Bing-Bildersuche-API von Version 5 auf Version 7
titleSuffix: Azure Cognitive Services
description: In diesem Upgradeleitfaden werden die Änderungen zwischen Version 5 und Version 7 der Bing-Bildersuche-API beschrieben. Anhand dieses Leitfadens können Sie die Teile Ihrer Anwendung ermitteln, die Sie zur Verwendung von Version 7 aktualisieren müssen.
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: 7F78B91F-F13B-40A4-B8A7-770FDB793F0F
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: article
ms.date: 02/12/2019
ms.author: scottwhi
ms.openlocfilehash: e014910bc34d2c6aaf9904c5a3670e7d7b496e84
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66384382"
---
# <a name="bing-image-search-api-v7-upgrade-guide"></a>Leitfaden zur Durchführung eines Upgrades für die Bing-Bildersuche-API v7

In diesem Upgradeleitfaden sind die Änderungen zwischen Version 5 und Version 7 der Bing-Bildersuche-API angegeben. Anhand dieses Leitfadens können Sie die Teile Ihrer Anwendung ermitteln, die Sie zur Verwendung von Version 7 aktualisieren müssen.

## <a name="breaking-changes"></a>Wichtige Änderungen

### <a name="endpoints"></a>Endpunkte

- Die Versionsnummer des Endpunkts hat sich von v5 in v7 geändert. Beispiel: https:\//api.cognitive.microsoft.com/bing/\*\*v7.0**/images/search.

### <a name="error-response-objects-and-error-codes"></a>Fehlerantwortobjekte und Fehlercodes

- Alle Anforderungen mit Fehlern sollten nun ein `ErrorResponse`-Objekt im Antworttext enthalten.

- Folgende Felder wurden zum `Error`-Objekt hinzugefügt:  
  - `subCode`&mdash;Teilt den Fehlercode nach Möglichkeit in separate Buckets auf
  - `moreDetails`&mdash;Zusätzliche Informationen über den Fehler, der im `message`-Feld beschrieben ist


- Die v5-Fehlercodes wurden durch die folgenden möglichen `code`- und `subCode`-Werte ersetzt.

|Code|SubCode|BESCHREIBUNG
|-|-|-
|ServerError|UnexpectedError<br/>ResourceError<br/>NotImplemented|Bing gibt ServerError zurück, sobald eine der Untercodebedingungen auftritt. Die Antwort enthält diese Fehler, wenn der HTTP-Statuscode 500 lautet.
|InvalidRequest|ParameterMissing<br/>ParameterInvalidValue<br/>HttpNotAllowed<br/>Blockiert|Bing gibt InvalidRequest zurück, wenn ein beliebiger Teil der Anforderung ungültig ist. Beispielsweise, wenn ein erforderlicher Parameter fehlt oder ein Parameterwert ungültig ist.<br/><br/>Wenn der Fehler ParameterMissing oder ParameterInvalidValue ist, lautet der HTTP-Statuscode 400.<br/><br/>Wenn der Fehler HttpNotAllowed ist, lautet der HTTP-Statuscode 410.
|RateLimitExceeded||Bing gibt RateLimitExceeded zurück, wenn Sie Ihr Kontingent für Abfragen pro Sekunde (Queries Per Second, QPS) oder Abfragen pro Monat (Queries Per Month, QPM) überschreiten.<br/><br/>Bing gibt den HTTP-Statuscode 429 zurück, wenn Sie das QPS-Kontingent überschritten haben, und 403, wenn Sie das QPM-Kontingent überschritten haben.
|InvalidAuthorization|AuthorizationMissing<br/>AuthorizationRedundancy|Bing gibt InvalidAuthorization zurück, wenn Bing den Aufrufer nicht authentifizieren kann. Beispielsweise, wenn der `Ocp-Apim-Subscription-Key`-Header fehlt oder der Abonnementschlüssel ungültig ist.<br/><br/>Redundanz tritt auf, wenn Sie mehrere Authentifizierungsmethoden angeben.<br/><br/>Wenn der Fehler InvalidAuthorization ist, lautet der HTTP-Statuscode 401.
|InsufficientAuthorization|AuthorizationDisabled<br/>AuthorizationExpired|Wenn der Aufrufer nicht über Berechtigungen für den Zugriff auf die Ressource verfügt, gibt Bing InsufficientAuthorization zurück. Dies kann der Fall sein, wenn der Abonnementschlüssel deaktiviert wurde oder abgelaufen ist. <br/><br/>Wenn der Fehler InsufficientAuthorization ist, lautet der HTTP-Statuscode 403.

- Nachfolgend sind die vorherigen Fehlercodes den neuen Codes zugeordnet. Wenn Sie eine Abhängigkeit von v5-Fehlercodes eingerichtet haben, aktualisieren Sie den Code entsprechend.

|Version 5-Code|Version 7-Code.untergeordneter Code
|-|-
|RequestParameterMissing|InvalidRequest.ParameterMissing
RequestParameterInvalidValue|InvalidRequest.ParameterInvalidValue
ResourceAccessDenied|InsufficientAuthorization
ExceededVolume|RateLimitExceeded
ExceededQpsLimit|RateLimitExceeded
Deaktiviert|InsufficientAuthorization.AuthorizationDisabled
UnexpectedError|ServerError.UnexpectedError
DataSourceErrors|ServerError.ResourceError
AuthorizationMissing|InvalidAuthorization.AuthorizationMissing
HttpNotAllowed|InvalidRequest.HttpNotAllowed
UserAgentMissing|InvalidRequest.ParameterMissing
NotImplemented|ServerError.NotImplemented
InvalidAuthorization|InvalidAuthorization
InvalidAuthorizationMethod|InvalidAuthorization
MultipleAuthorizationMethod|InvalidAuthorization.AuthorizationRedundancy
ExpiredAuthorizationToken|InsufficientAuthorization.AuthorizationExpired
InsufficientScope|InsufficientAuthorization
Blockiert|InvalidRequest.Blocked



### <a name="query-parameters"></a>Abfrageparameter

- Der Abfrageparameter `modulesRequested` wurde in [modules](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) umbenannt.  

- Die Anmerkungen wurden in Tags umbenannt. Weitere Informationen zum Abfrageparameter „modules“ für Tags finden Sie [hier](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference).  

- Die Liste der unterstützten Märkte des Filterwerts „ShoppingSources“ wurde in das Gebietsschema „en-US“ geändert. Weitere Informationen finden Sie unter [imageType](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetype).  


### <a name="image-insights-changes"></a>Änderungen an Bildauswertungen

- Das Feld `annotations` von [ImagesInsights](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsightsresponse) wurde in `imageTags` umbenannt.  

- Das Objekt `AnnotationModule` wurde in [ImageTagsModule](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetagsmodule) umbenannt.  

- Das Objekt `Annotation` wurde in [Tag](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#tag) umbenannt, und das Feld `confidence` wurde entfernt.  

- Das Feld `insightsSourcesSummary` des [Image](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image)-Objekts wurde in `insightsMetadata` umbenannt.  

- Das Objekt `InsightsSourcesSummary` wurde in [InsightsMetadata](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightsmetadata) umbenannt.  

- Der Endpunkt `https://api.cognitive.microsoft.com/bing/v7.0/images/details` wurde hinzugefügt. Verwenden Sie diesen Endpunkt, um anstelle des Endpunkts „/images/search“ Bildauswertungen anzufordern. Weitere Informationen finden Sie unter [Abrufen von Bildauswertungen](./image-insights.md).

- Die folgenden Abfrageparameter sind ab sofort nur noch mit dem Endpunkt `/images/details` gültig.  

    -   [insightsToken](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken)  
    -   [modules](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference)  
    -   [imgUrl](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imgurl)  
    -   [cab](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cab)  
    -   [cal](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cal)  
    -   [car](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#car)  
    -   [cat](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cat)  
    -   [ct](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#ct)  

- Das Objekt `ImageInsightsResponse` wurde in [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsights) umbenannt.  

- Die Datentypen der folgenden Felder im Objekt [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsights) wurden geändert.  

    -   Der Typ des Felds `relatedCollections` wurde von `ImageGallery[]` in [RelatedCollectionsModule](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#relatedcollectionsmodule) geändert.  

    -   Der Typ des Felds `pagesIncluding` wurde von `Image[]` in [ImagesModule](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagesmodule) geändert.  

    -   Der Typ des Felds `relatedSearches` wurde von `Query[]` in [RelatedSearchesModule](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#relatedsearchesmodule) geändert.  

    -   Der Typ des Felds `recipes` wurde von `Recipe[]` in [RecipesModule](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#recipesmodule) geändert.  

    -   Der Typ des Felds `visuallySimilarImages` wurde von `Image[]` in [ImagesModule](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagesmodule) geändert.  

    -   Der Typ des Felds `visuallySimilarProducts` wurde von `ProductSummaryImage[]` in [ImagesModule](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagesmodule) geändert.  

    -   Das Objekt `ProductSummaryImage` wurde entfernt, und die produktbezogenen Felder im Objekt [Image](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image) wurden verschoben. Das Objekt `Image` enthält nur produktbezogene Felder, wenn das Bild als Bestandteil von visuell ähnliche Produkten in einer Bildauswertungsantwort enthalten ist.  

    -   Der Typ des Felds `recognizedEntityGroups` wurde von `RecognizedEntityGroup[]` in [RecognizedEntitiesModule](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#recognizedentitiesmodule) geändert.  

-   Das Feld `categoryClassification` von [ImageInsights](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsightsresponse) wurde in `annotations` umbenannt, und dessen Typ wurde in `AnnotationsModule` geändert.  

### <a name="images-answer"></a>Bildantwort

-   Die Felder „displayShoppingSourcesBadges“ und „displayRecipeSourcesBadges“ wurden aus [Images](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) entfernt.  

-   Das Feld `nextOffsetAddCount` von [Images](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) wurde in `nextOffset` umbenannt. Die Art und Weise, wie Sie das Offset verwenden, wurde ebenfalls geändert. Vorher haben Sie den Abfrageparameter [offset](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#offset) auf den Wert `nextOffsetAddCount`, den vorherigen Offsetwert sowie die Anzahl der Bilder im Ergebnis festgelegt. Nun legen Sie `offset` auf den Wert `nextOffset` fest.  


## <a name="non-breaking-changes"></a>Geringfügige Änderungen

### <a name="query-parameters"></a>Abfrageparameter

- „Transparent“ wurde als möglicher [imageType](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagetype)-Filterwert hinzugefügt. Der Transparent-Filter gibt nur Bilder mit einem transparenten Hintergrund zurück.

- „Any“ wurde als möglicher [license](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#license)-Filterwert hinzugefügt. Der Any-Filter gibt nur die unter Lizenz stehenden Bilder zurück.

- Die Abfrageparameter [maxFileSize](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#maxfilesize) und [minFileSize](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#minfilesize) wurden hinzugefügt. Verwenden Sie diese Filter, um Bilder innerhalb eines Bereichs von Dateigrößen zurückzugeben.  

- Die Abfrageparameter [maxHeight](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#maxheight), [minHeight](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#minheight), [maxWidth](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#maxwidth) und [minWidth](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#minwidth) wurden hinzugefügt. Verwenden Sie diese Filter, um Bilder innerhalb eines Bereichs von Höhen und Breiten zurückzugeben.  

### <a name="object-changes"></a>Änderungen an Objekten

- Die Felder `description` und `lastUpdated` wurden zum Objekt [Offer](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#offer) hinzugefügt.  

- Das Feld `name` wurde zum Objekt [ImageGallery](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imagegallery) hinzugefügt.  

- `similarTerms` wurde zum Objekt [Bilder](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) hinzugefügt. Dieses Feld enthält eine Liste von Benennungen, die in ihrer Bedeutung Ähnlichkeiten mit der Abfragezeichenfolge des Benutzers haben.  
