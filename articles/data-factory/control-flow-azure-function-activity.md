---
title: Aktivität „Azure Function“ in Azure Data Factory | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie die Aktivität „Azure Function“ verwenden, um eine Azure-Funktion in einer Data Factory-Pipeline auszuführen
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/09/2019
author: sharonlo101
ms.author: shlo
manager: craigg
ms.openlocfilehash: 82786b8f01ce409179f4ddd37127679f9357cd0e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64727053"
---
# <a name="azure-function-activity-in-azure-data-factory"></a>Aktivität „Azure Function“ in Azure Data Factory

Mit der Aktivität „Azure Function“ können Sie [Azure-Funktionen](../azure-functions/functions-overview.md) in einer Data Factory-Pipeline ausführen. Um eine Azure-Funktion auszuführen, müssen Sie einen verknüpften Dienst erstellen sowie eine Aktivität, die die auszuführende Azure-Funktion angibt.

Das folgende Video enthält eine achtminütige Einführung und Demonstration dieses Features:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Run-Azure-Functions-from-Azure-Data-Factory-pipelines/player]

## <a name="azure-function-linked-service"></a>Verknüpfter Dienst der Azure-Funktion

Der Rückgabetyp der Azure-Funktion muss ein gültiges `JObject` sein. (Beachten Sie, dass [JArray](https://www.newtonsoft.com/json/help/html/T_Newtonsoft_Json_Linq_JArray.htm) *kein* `JObject` ist.) Jeder andere Rückgabetyp als `JObject` führt zu dem Benutzerfehler *Antwortinhalt ist kein gültiges JObject*.

| **Eigenschaft** | **Beschreibung** | **Erforderlich** |
| --- | --- | --- |
| type   | Die type-Eigenschaft muss auf Folgendes festgelegt werden: **AzureFunction** | Ja |
| Funktions-App-URL | URL für die Azure-Funktions-App. Das Format lautet `https://<accountname>.azurewebsites.net`. Diese URL ist der Wert unter dem Abschnitt **URL**, wenn Sie Ihre Funktions-App im Azure-Portal anzeigen.  | Ja |
| Funktionsschlüssel | Der Zugriffsschlüssel für die Azure-Funktion. Klicken Sie in den Abschnitt **Verwalten** der jeweiligen Funktion, und kopieren Sie entweder den **Funktionsschlüssel** oder den **Hostschlüssel**. Weitere Informationen finden Sie hier: [HTTP-Trigger und -Bindungen in Azure Functions](../azure-functions/functions-bindings-http-webhook.md#authorization-keys) | Ja |
|   |   |   |

## <a name="azure-function-activity"></a>Aktivität „Azure Function“

| **Eigenschaft**  | **Beschreibung** | **Zulässige Werte** | **Erforderlich** |
| --- | --- | --- | --- |
| name  | Name der Aktivität in der Pipeline  | string | Ja |
| type  | Typ der Aktivität ist „AzureFunctionActivity“ | string | Ja |
| Verknüpfter Dienst | Der mit der Azure-Funktion verknüpfte Dienst für die entsprechende Azure-Funktions-App  | Verweis auf den verknüpften Dienst | Ja |
| Funktionsname  | Name der Funktion in der Azure-Funktions-App, die diese Aktivität aufruft. | string | Ja |
| method  | REST-API-Methode für den Funktionsaufruf | Unterstützte Zeichenfolgentypen: „GET“, „POST“, „PUT“   | Ja |
| Header  | Header, die in der Anforderung gesendet werden. So legen Sie beispielsweise die Sprache und den Typ für eine Anforderung fest: "headers": { "Accept-Language": "en-us", "Content-Type": "application/json" } | Zeichenfolge (oder ein Ausdruck mit resultType der Zeichenfolge) | Nein |
| body  | Text, der zusammen mit der Anforderung an die API-Methode der Funktion gesendet wird  | Zeichenfolge (oder Ausdruck mit resultType der Zeichenfolge) oder Objekt.   | Erforderlich für PUT/POST-Methoden |
|   |   |   | |

Weitere Informationen finden Sie in den Details zum Schema der Anforderungsnutzlast im Abschnitt  [Schema der Anforderungsnutzlast](control-flow-web-activity.md#request-payload-schema) .

## <a name="routing-and-queries"></a>Routing und Abfragen

Die Azure-Funktionsaktivität unterstützt **Routing**. Wenn Ihre Azure-Funktion z. B. den Endpunkt `https://functionAPP.azurewebsites.net/api/<functionName>/<value>?code=<secret>` aufweist, dann ist der in der Azure-Funktionsaktivität zu verwendende `functionName` entsprechend `<functionName>/<value>`. Sie können diese Funktion parametrisieren, um zur Laufzeit den gewünschten `functionName` bereitzustellen.

Die Azure-Funktionsaktivität unterstützt auch **Abfragen**. Eine Abfrage muss als Teil des `functionName` einbezogen werden. Wenn der Funktionsname z. B. `HttpTriggerCSharp` lautet und die einzubeziehende Abfrage `name=hello` ist, dann können Sie den `functionName` in der Azure-Funktionsaktivität als `HttpTriggerCSharp?name=hello` konstruieren. Diese Funktion kann parametrisiert werden, sodass der Wert zur Laufzeit bestimmt werden kann.

## <a name="timeout-and-long-running-functions"></a>Zeitlimit und zeitintensive Funktionen

Azure Functions erreicht nach 230 Sekunden den Timeout, unabhängig von der Einstellung `functionTimeout`, die Sie in den Einstellungen vorgenommen haben. [hier finden Sie weitere Informationen](../azure-functions/functions-versions.md#timeout) Um dieses Verhalten zu umgehen, folgen Sie einem asynchronen Muster oder verwenden Sie Durable Functions. Der Vorteil von Durable Functions besteht darin, dass es einen eigenen Mechanismus für die Zustandsnachverfolgung bereitstellt, sodass Sie keinen eigenen Mechanismus implementieren müssen.

Weitere Informationen zu Durable Functions finden Sie in [diesem Artikel](../azure-functions/durable/durable-functions-overview.md). Sie können eine Azure-Funktionsaktivität einrichten, um Durable Function aufzurufen, das eine Antwort mit einer anderen URI zurückgibt, wie z. B. [dieses Beispiel](../azure-functions/durable/durable-functions-http-api.md#http-api-url-discovery). Da `statusQueryGetUri` den HTTP-Status 202 zurückgibt, während die Funktion ausgeführt wird, können Sie den Status der Funktion mithilfe einer Webaktivität abfragen. Richten Sie einfach eine Webaktivität ein, bei der das Feld `url` auf `@activity('<AzureFunctionActivityName>').output.statusQueryGetUri` festgelegt ist. Wenn Durable Function abgeschlossen ist, entspricht die Ausgabe der Funktion der Ausgabe der Webaktivität.


## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über Aktivitäten in Data Factory in [Pipelines und Aktivitäten in Azure Data Factory](concepts-pipelines-activities.md).
