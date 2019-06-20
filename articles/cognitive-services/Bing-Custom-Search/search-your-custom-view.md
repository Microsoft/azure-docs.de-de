---
title: Suche nach einer benutzerdefinierten Ansicht – Benutzerdefinierte Bing-Suche
titlesuffix: Azure Cognitive Services
description: Beschreibt, wie Sie nach einer benutzerdefinierten Ansicht im Web suchen.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 05/09/2019
ms.author: maheshb
ms.openlocfilehash: 9c0c4fc21f6f4b23b14c8020ecd7e95c63dedcbf
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66388547"
---
# <a name="call-your-bing-custom-search-instance-from-the-portal"></a>Aufrufen Ihrer Instanz der benutzerdefinierten Bing-Suche über das Portal

Nachdem Sie Ihre benutzerdefinierte Suche konfiguriert haben, können Sie sie im [Portal](https://customsearch.ai) für die benutzerdefinierte Bing-Suche testen. 

![Screenshot des Portals für die benutzerdefinierte Bing-Suche](media/portal-search-screen.png)
## <a name="create-a-search-query"></a>Erstellen einer Suchabfrage 

Wählen Sie nach der Anmeldung beim [Portal](https://customsearch.ai) für die benutzerdefinierte Bing-Suche Ihre Suchinstanz aus, und klicken Sie auf die Registerkarte **Produktion**. Wählen Sie unter **Endpunkte** einen API-Endpunkt aus (etwa die Web-API). Welche Endpunkte angezeigt werden, hängt von Ihrem Abonnement ab.

Geben Sie zum Erstellen einer Suchabfrage die Parameterwerte für Ihren Endpunkt ein. Beachten Sie, dass die im Portal angezeigten Parameter je nach gewähltem Endpunkt variieren können. Weitere Informationen finden Sie in der [Referenz zur API für die benutzerdefinierte Suche](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#query-parameters). Um das von Ihrer Suchinstanz verwendete Abonnement zu ändern, fügen Sie den entsprechenden Abonnementschlüssel hinzu, und aktualisieren Sie die entsprechenden Parameter für den Markt und/oder die Sprache.

Im Anschluss finden Sie einige wichtige Parameter:


|Parameter  |BESCHREIBUNG  |
|---------|---------|
|Abfragen     | Der gewünschte Suchbegriff. Nur für Endpunkte für die Web-, Bilder-, Video- und Vorschlagssuche verfügbar. |
|Benutzerdefinierte Konfigurations-ID | Die Konfigurations-ID der ausgewählten benutzerdefinierten Suchinstanz. Dieses Feld ist schreibgeschützt. |
|Market     | Der Markt, aus dem die Ergebnisse stammen sollen. Nur für Endpunkte für die Web-, Bilder- und Videosuche und die gehostete Benutzeroberfläche verfügbar.        |
|Abonnementschlüssel | Der Abonnementschlüssel für den Test. Sie können einen Schlüssel in der Dropdownliste auswählen oder manuell einen Schlüssel eingeben.          |

Wenn Sie auf **Zusätzliche Parameter** klicken, werden die folgenden Parameter angezeigt:  

|Parameter  |BESCHREIBUNG  |
|---------|---------|
|Safe Search     | Ein Filter, der Webseiten nach jugendgefährdenden Inhalten durchsucht. Nur für Endpunkte für die Web-, Bilder- und Videosuche und die gehostete Benutzeroberfläche verfügbar.        |
|Benutzeroberflächensprache    | Die Sprache für Zeichenfolgen auf der Benutzeroberfläche. Wenn Sie beispielsweise Bilder und Videos in der gehosteten Benutzeroberfläche aktivieren, wird auf den Registerkarten **Bild** und **Video** die angegebene Sprache verwendet.        |
|Count     | Die Anzahl von Suchergebnissen, die in der Antwort zurückgegeben werden sollen. Nur für Endpunkte für die Web-, Bilder- und Videosuche verfügbar.         |
|Offset    | Die Anzahl von Ergebnissen, die übersprungen werden sollen, bevor Ergebnisse zurückgegeben werden. Nur für Endpunkte für die Web-, Bilder- und Videosuche verfügbar.        |
    
Nachdem Sie alle erforderlichen Optionen angegeben haben, klicken Sie auf **Aufrufen**, um die JSON-Antwort im rechten Bereich anzuzeigen. Wenn Sie den Endpunkt für die gehostete Benutzeroberfläche auswählen, können Sie die Suche im unteren Bereich testen.

## <a name="change-your-bing-custom-search-subscription"></a>Ändern Ihres Abonnements für die benutzerdefinierte Bing-Suche

Sie können das Abonnement ändern, das Ihrer benutzerdefinierten Bing-Suchinstanz zugeordnet ist, ohne eine neue Instanz zu erstellen. Damit API-Aufrufe an ein neues Abonnement gesendet und für dieses berechnet werden, erstellen Sie eine neue Ressource für die benutzerdefinierte Bing-Suche im Azure-Portal. Verwenden Sie den neuen Abonnementschlüssel in Ihren API-Anforderungen zusammen mit der benutzerdefinierten Konfigurations-ID Ihrer Instanz.

## <a name="next-steps"></a>Nächste Schritte

- [Aufrufen der benutzerdefinierten Ansicht mit C#](./call-endpoint-csharp.md)
- [Aufrufen der benutzerdefinierten Ansicht mit Java](./call-endpoint-java.md)
- [Aufrufen der benutzerdefinierten Ansicht mit NodeJs](./call-endpoint-nodejs.md)
- [Aufrufen der benutzerdefinierten Ansicht mit Python](./call-endpoint-python.md)

- [Aufrufen der benutzerdefinierten Ansicht mit dem C# SDK](./sdk-csharp-quick-start.md)
