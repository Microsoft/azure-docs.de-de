---
title: Herstellen einer Verbindung mit der Bing-Suche – Azure Logic Apps
description: Suchen von Nachrichten mit den REST-APIs für die Bing-Suche und Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 05/21/2018
tags: connectors
ms.openlocfilehash: 7146e59eabf9e30fa263f957f1c546414ad0fe26
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60952602"
---
# <a name="find-news-with-bing-search-and-azure-logic-apps"></a>Suchen von Nachrichten mit der Bing-Suche und Azure Logic Apps

In diesem Artikel wird gezeigt, wie Sie mit dem Bing-Suche-Connector innerhalb einer Logik-App Nachrichten, Videos und andere Elemente über die Bing-Suche finden können. Auf diese Weise können Sie Logik-Apps erstellen, die Aufgaben und Workflows zur Verarbeitung von Suchergebnissen automatisieren und diese Elemente für andere Aktionen zur Verfügung stellen. 

Sie können z.B. Nachrichtenelemente basierend auf Suchkriterien finden und diese Elemente in Twitter in Ihrem Twitter-Feed als Tweets bereitstellen.

Wenn Sie nicht über ein Azure-Abonnement verfügen, können Sie sich <a href="https://azure.microsoft.com/free/" target="_blank">für ein kostenloses Azure-Konto registrieren</a>. Falls Sie noch nicht mit Logik-Apps vertraut sind, finden Sie weitere Informationen unter [Was ist Azure Logic Apps?](../logic-apps/logic-apps-overview.md) und [Schnellstart: Erstellen Ihres ersten automatisierten Workflows mit Azure Logic Apps – Azure-Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md).
Connectorspezifische technische Informationen finden Sie in der <a href="https://docs.microsoft.com/connectors/bingsearch/" target="blank">Referenz zum Bing-Suche-Connector</a>.

## <a name="prerequisites"></a>Voraussetzungen

* Ein [Cognitive Services-Konto](../cognitive-services/cognitive-services-apis-create-account.md)

* Ein [Bing-Suche-API-Schlüssel](https://azure.microsoft.com/try/cognitive-services/?api=bing-news-search-api), der den Zugriff von Ihrer Logik-App auf die Bing-Suche-APIs ermöglicht

* Die Logik-App, in der Sie auf Ihren Event Hub zugreifen möchten. Um Ihre Logik-App mit einem Bing-Suche-Trigger starten zu können, benötigen Sie eine [leere Logik-App](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="add-trigger"></a>

## <a name="add-a-bing-search-trigger"></a>Hinzufügen eines Bing-Suche-Triggers

In Azure Logic Apps muss jede Logik-App mit einem [Trigger](../logic-apps/logic-apps-overview.md#logic-app-concepts) beginnen, der ausgelöst wird, wenn ein bestimmtes Ereignis eintritt oder eine bestimmte Bedingung erfüllt wird. Bei jeder Auslösung des Triggers erstellt das Logic Apps-Modul eine Logik-App-Instanz und startet die Ausführung des Workflows Ihrer App.

1. Erstellen Sie im Azure-Portal oder in Visual Studio eine leere Logik-App, die den Logik-App-Designer öffnet. In diesem Beispiel wird das Azure-Portal verwendet.

2. Geben Sie im Suchfeld den Begriff „Bing-Suche“ als Filter ein. Wählen Sie in der Triggerliste den gewünschten Trigger aus.

   In diesem Beispiel wird folgender Trigger verwendet: **Bing-Suche – Bei neuem Nachrichtenartikel**

   ![Finden des Bing-Suche-Triggers](./media/connectors-create-api-bing-search/add-trigger.png)

3. Wenn Sie zum Angeben von Verbindungsdetails aufgefordert werden, können Sie [jetzt Ihre Bing-Suche-Verbindung erstellen](#create-connection).
Falls die Verbindung bereits besteht, können Sie die erforderlichen Informationen für den Trigger angeben.

   Geben Sie für dieses Beispiel Kriterien für die Rückgabe übereinstimmender Nachrichtenartikel aus der Bing-Suche an.

   | Eigenschaft | Erforderlich | Value | BESCHREIBUNG |
   |----------|----------|-------|-------------|
   | Search Query | Ja | <*Suchbegriffe*> | Geben Sie Suchbegriffe ein, die Sie verwenden möchten. |
   | Market | Ja | <*Gebietsschema*> | Das Gebietsschema für die Suche. Die Standardeinstellung ist „en-US“, aber Sie können einen anderen Wert auswählen. |
   | Safe Search | Ja | <*Suchebene*> | Die Filterebene zum Ausschließen nicht jugendfreier Inhalte. Die Standardeinstellung ist „Mittel“, aber Sie können eine andere Ebene auswählen. |
   | Count | Nein | <*Ergebnisanzahl*> | Hiermit wird die angegebene Anzahl von Ergebnissen zurückgegeben. Der Standardwert ist 20, aber Sie können einen anderen Wert angeben. Die tatsächliche Anzahl zurückgegebener Ergebnisse ist möglicherweise kleiner als die angegebene Anzahl. |
   | Offset | Nein | <*Versatzwert*> | Die Anzahl von Ergebnissen, die übersprungen werden sollen, bevor Ergebnisse zurückgegeben werden |
   |||||

   Beispiel:

   ![Einrichten von Trigger](./media/connectors-create-api-bing-search/bing-search-trigger.png)

4. Wählen Sie das Intervall und die Häufigkeit aus, um anzugeben, wie oft der Trigger nach Ergebnissen suchen soll.

5. Wenn Sie fertig sind, wählen Sie auf der Symbolleiste des Designers die Option **Speichern** aus.

6. Fahren Sie nun damit fort, der Logik-App weitere Aktionen für die Aufgaben hinzuzufügen, die anhand der Triggerergebnisse durchgeführt werden sollen.

<a name="add-action"></a>

## <a name="add-a-bing-search-action"></a>Hinzufügen einer Bing-Suche-Aktion

In Azure Logic Apps handelt es sich bei einer [Aktion](../logic-apps/logic-apps-overview.md#logic-app-concepts) um einen Schritt in Ihrem Workflow, der einem Trigger oder einer anderen Aktion folgt. In diesem Beispiel startet die Logik-App mit einem Bing-Suche-Trigger, der Nachrichtenartikel zurückgibt, die den angegebenen Kriterien entsprechen.

1. Öffnen Sie im Azure-Portal oder in Visual Studio Ihre Logik-App im Logik-App-Designer. In diesem Beispiel wird das Azure-Portal verwendet.

2. Wählen Sie unter dem Trigger oder der Aktion **Neuer Schritt** > **Aktion hinzufügen** aus.

   In diesem Beispiel wird folgender Trigger verwendet:

   **Bing-Suche – Bei neuem Nachrichtenartikel**

   ![Hinzufügen einer Aktion](./media/connectors-create-api-bing-search/add-action.png)

   Um eine Aktion zwischen vorhandenen Schritten hinzuzufügen, bewegen Sie den Mauszeiger über den Verbindungspfeil. 
   Wählen Sie das daraufhin angezeigte Pluszeichen ( **+** ) aus, und wählen Sie dann **Aktion hinzufügen** aus.

3. Geben Sie im Suchfeld den Begriff „Bing-Suche“ als Filter ein.
Wählen Sie in der Liste mit den Aktionen die gewünschte Aktion aus.

   In diesem Beispiel wird diese Aktion verwendet:

   **Bing-Suche – Nachrichten nach Abfrage auflisten**

   ![Finden einer Bing-Suche-Aktion](./media/connectors-create-api-bing-search/bing-search-select-action.png)

4. Wenn Sie zum Angeben von Verbindungsdetails aufgefordert werden, können Sie [jetzt Ihre Bing-Suche-Verbindung erstellen](#create-connection). Falls Ihre Verbindung bereits besteht, können Sie die erforderlichen Informationen für die Aktion angeben.

   In diesem Beispiel geben Sie die Kriterien für die Rückgabe einer Teilmenge der Triggerergebnisse an.

   | Eigenschaft | Erforderlich | Value | BESCHREIBUNG |
   |----------|----------|-------|-------------|
   | Search Query | Ja | <*Suchausdruck*> | Geben Sie einen Ausdruck für die Abfrage der Triggerergebnisse ein. Sie können aus den Feldern der Liste mit dynamischen Inhalten wählen oder mit dem Ausdrucks-Generator einen Ausdruck erstellen. |
   | Market | Ja | <*Gebietsschema*> | Das Gebietsschema für die Suche. Die Standardeinstellung ist „en-US“, aber Sie können einen anderen Wert auswählen. |
   | Safe Search | Ja | <*Suchebene*> | Die Filterebene zum Ausschließen nicht jugendfreier Inhalte. Die Standardeinstellung ist „Mittel“, aber Sie können eine andere Ebene auswählen. |
   | Count | Nein | <*Ergebnisanzahl*> | Hiermit wird die angegebene Anzahl von Ergebnissen zurückgegeben. Der Standardwert ist 20, aber Sie können einen anderen Wert angeben. Die tatsächliche Anzahl zurückgegebener Ergebnisse ist möglicherweise kleiner als die angegebene Anzahl. |
   | Offset | Nein | <*Versatzwert*> | Die Anzahl von Ergebnissen, die übersprungen werden sollen, bevor Ergebnisse zurückgegeben werden |
   |||||

   Nehmen Sie beispielsweise an, dass Sie Ergebnisse benötigen, deren Kategoriename die Zeichenfolge „Tech“ enthält.

   1. Klicken Sie in das Feld **Suchabfrage**, damit die dynamische Inhaltsliste angezeigt wird. 
   Wählen Sie aus dieser Liste die Option **Ausdruck**, um den Ausdrucks-Generator anzuzeigen. 

      ![Bing-Suche-Trigger](./media/connectors-create-api-bing-search/bing-search-action.png)

      Jetzt können Sie mit dem Erstellen des Ausdrucks beginnen.

   2. Wählen Sie in der Liste der Funktionen die Funktion **contains()** . Diese wird im Ausdrucksfeld angezeigt. Klicken Sie auf **Dynamische Inhalte**, um die Feldliste erneut anzuzeigen. Stellen Sie jedoch sicher, dass der Cursor innerhalb der Klammern bleibt.

      ![Auswählen einer Funktion](./media/connectors-create-api-bing-search/expression-select-function.png)

   3. Wählen Sie aus der Feldliste die Option **Kategorie**, die in einen Parameter umgewandelt wird. 
   Fügen Sie nach dem ersten Parameter ein Komma hinzu, und fügen Sie nach dem Komma dieses Wort hinzu: `'tech'` 

      ![Auswählen eines Felds](./media/connectors-create-api-bing-search/expression-select-field.png)

   4. Wählen Sie **OK** aus, wenn Sie fertig sind.

      Der Ausdruck wird jetzt im Feld **Suchabfrage** im folgenden Format angezeigt:

      ![Fertiger Ausdruck](./media/connectors-create-api-bing-search/resolved-expression.png)

      In der Codeansicht wird dieser Ausdruck im folgenden Format angezeigt:

      `"@{contains(triggerBody()?['category'],'tech')}"`

5. Wenn Sie fertig sind, wählen Sie auf der Symbolleiste des Designers die Option **Speichern** aus.

<a name="create-connection"></a>

## <a name="connect-to-bing-search"></a>Herstellen einer Verbindung mit der Bing-Suche

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Wenn Sie zur Eingabe von Verbindungsinformationen aufgefordert werden, geben Sie diese Details an:

   | Eigenschaft | Erforderlich | Value | BESCHREIBUNG |
   |----------|----------|-------|-------------|
   | Verbindungsname | Ja | <*verbindungsname*> | Der Name, der für Ihre Verbindung erstellt werden soll |
   | API-Version | Ja | <*API-Version*> | Standardmäßig wird die Version der Bing-Suche-API auf die aktuelle Version festgelegt. Sie können nach Bedarf eine frühere Version auswählen. |
   | API-Schlüssel | Ja | <*API-Schlüssel*> | Der Schlüssel der Bing-Suche-API, den Sie zuvor erhalten haben. Wenn Sie keinen Schlüssel besitzen, können Sie Ihren [API-Schlüssel jetzt abrufen](https://azure.microsoft.com/try/cognitive-services/?api=bing-news-search-api). |  
   |||||  

   Beispiel:

   ![Erstellen der Verbindung](./media/connectors-create-api-bing-search/bing-search-create-connection.png)

2. Wenn Sie fertig sind, wählen Sie **Erstellen** aus.

## <a name="connector-reference"></a>Connector-Referenz

Technische Details wie Trigger, Aktionen und Limits, wie sie in der OpenAPI-Datei (ehemals Swagger) des Connectors beschrieben werden, finden Sie auf der [Referenzseite des Connectors](/connectors/bingsearch/).

## <a name="get-support"></a>Support

* Sollten Sie Fragen haben, besuchen Sie das [Azure Logic Apps-Forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Wenn Sie Features vorschlagen oder für Vorschläge abstimmen möchten, besuchen Sie die [Website für Logic Apps-Benutzerfeedback](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Nächste Schritte

* Informationen zu anderen [Logic Apps-Connectors](../connectors/apis-list.md)
