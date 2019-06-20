---
title: Planen von wiederkehrenden Aufgaben mit dem Trigger „Gleitendes Fenster“ – Azure Logic Apps
description: Planen und Ausführen von wiederkehrenden automatisierten Aufgaben und Workflows mit dem Trigger „Gleitendes Fenster“ in Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: deli, klam, LADocs
ms.topic: conceptual
ms.date: 05/25/2019
ms.openlocfilehash: 44944955019fcf81fb0d296592577e2b00a15928
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66299590"
---
# <a name="create-schedule-and-run-recurring-tasks-and-workflows-with-the-sliding-window-trigger-in-azure-logic-apps"></a>Erstellen, Planen und Ausführen von wiederkehrenden Aufgaben und Workflows mit dem Trigger „Gleitendes Fenster“ in Azure Logic Apps

Um regelmäßig Aufgaben, Prozesse oder Aufträge auszuführen, die Daten in kontinuierlichen Blöcken verarbeiten müssen, können Sie Ihren Logik-App-Workflow mit dem Trigger **Gleitendes Fenster – Zeitplan** starten. Sie können ein Datum, eine Uhrzeit und eine Zeitzone zum Starten des Workflows sowie eine Serie zum Wiederholen dieses Workflows festlegen. Wenn Wiederholungen aus irgendeinem Grund nicht ausgeführt werden, verarbeitet dieser Trigger diese fehlenden Wiederholungen. Verwenden Sie den Trigger „Gleitendes Fenster“ beispielsweise beim Synchronisieren von Daten zwischen Ihrer Datenbank und dem Sicherungsspeicher, damit die Daten lückenlos synchronisiert werden. Weitere Informationen zu den integrierten Zeitplantriggern und -aktionen finden Sie unter [Planen und Ausführen von wiederkehrenden automatisierten Aufgaben und Workflows mit Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

Im Folgenden finden Sie einige der Muster, die von diesem Trigger unterstützt werden:

* Sofortige Ausführung und Wiederholung jeweils nach *n* Sekunden, Minuten oder Stunden.

* Start an einem bestimmten Datum zu einer bestimmten Uhrzeit und anschließende Ausführung und Wiederholung jeweils nach *n* Sekunden, Minuten oder Stunden. Mit diesem Trigger können Sie eine Startzeit in der Vergangenheit angeben, wodurch alle Wiederholungen der Vergangenheit ausgeführt werden.

* Geben Sie für jede Wiederholung eine Verzögerung für eine bestimmte Zeitspanne vor der Ausführung an.

Die Unterschiede zwischen diesem Trigger und dem Serientrigger sowie weitere Informationen zum Planen von wiederkehrenden Workflows finden Sie unter [Planen und Ausführen von wiederkehrenden automatisierten Aufgaben, Prozessen und Workflows in Azure Logic Apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md).

> [!TIP]
> Unter [Nur einmaliges Ausführen eines Auftrags](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#run-once) erfahren Sie, wie Sie Ihre Logik-App auslösen und danach nur einmal ausführen.

## <a name="prerequisites"></a>Voraussetzungen

* Ein Azure-Abonnement. Falls Sie kein Abonnement besitzen, können Sie sich [für ein kostenloses Azure-Konto registrieren](https://azure.microsoft.com/free/).

* Grundlegende Kenntnisse zu [Logik-Apps](../logic-apps/logic-apps-overview.md). Falls Sie noch nicht mit Logik-Apps gearbeitet haben, sollten Sie zunächst die Schnellstartanleitung zum [Erstellen Ihrer ersten Logik-App](../logic-apps/quickstart-create-first-logic-app-workflow.md) lesen.

## <a name="add-sliding-window-trigger"></a>Hinzufügen des Triggers „Gleitendes Fenster“

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. Erstellen einer leeren Logik-App

1. Wenn der Designer für Logik-Apps angezeigt wird, geben Sie im Suchfeld „Gleitendes Fenster“ als Filter ein. Wählen Sie aus der Triggerliste den folgenden Trigger als ersten Schritt Ihres Logik-App-Workflows aus: **Gleitendes Fenster**

   ![Auswählen des Triggers „Gleitendes Fenster“](./media/connectors-native-sliding-window/add-sliding-window-trigger.png)

1. Legen Sie das Intervall und die Häufigkeit für die Wiederholung fest. Legen Sie in diesem Beispiel diese Eigenschaften fest, um Ihren Workflow jede Woche auszuführen.

   ![Festlegen von Intervall und Häufigkeit](./media/connectors-native-sliding-window/sliding-window-trigger-details.png)

   | Eigenschaft | Erforderlich | JSON-Name | Type | BESCHREIBUNG |
   |----------|----------|-----------|------|-------------|
   | **Intervall** | Ja | interval | Integer | Eine positive ganze Zahl, die beschreibt, wie oft der Workflow basierend auf der Häufigkeit ausgeführt wird. Zulässige Mindest- und Maximalintervalle: <p>– Stunde: 1–12.000 Stunden </br>– Minute: 1–72.000 Minuten </br>- Sekunde: 1–9.999.999 Sekunden<p>Wenn das Intervall beispielsweise auf „6“ und die Häufigkeit auf „Stunde“ festgelegt ist, erfolgt die Wiederholung alle sechs Stunden. |
   | **Frequency** | Ja | frequency | Zeichenfolge | Die Zeiteinheit für die Wiederholung: **Sekunde**, **Minute** oder **Stunde**. |
   ||||||

   ![Erweiterte Wiederholungsoptionen](./media/connectors-native-sliding-window/sliding-window-trigger-more-options-details.png)

   Um weitere Optionen für die Wiederholung zu erhalten, öffnen Sie die Liste **Neuen Parameter hinzufügen**. 
   Alle von Ihnen ausgewählten Optionen werden nach der Auswahl im Trigger angezeigt.

   | Eigenschaft | Erforderlich | JSON-Name | Type | BESCHREIBUNG |
   |----------|----------|-----------|------|-------------|
   | **Verzögern** | Nein | delay | Zeichenfolge | Die Dauer der Verzögerung für jede Wiederholung im [ISO 8601-Format für Datums-/Uhrzeitangaben](https://en.wikipedia.org/wiki/ISO_8601#Durations). |
   | **Zeitzone** | Nein | timeZone | Zeichenfolge | Nur relevant, wenn Sie eine Startzeit angeben, da dieser Trigger keine [UTC-Abweichung](https://en.wikipedia.org/wiki/UTC_offset) akzeptiert. Wählen Sie die anzuwendende Zeitzone aus. |
   | **Startzeit** | Nein | startTime | Zeichenfolge | Geben Sie Startdatum und -uhrzeit im folgenden Format an: <p>JJJJ-MM-TTThh:mm:ss (bei Auswahl einer Zeitzone) <p>Oder <p>JJJJ-MM-TTThh:mm:ssZ (wenn keine Zeitzone ausgewählt wird) <p>Für den 18. September 2017 um 14:00 Uhr würden Sie also „2017-09-18T14:00:00“ angeben und eine Zeitzone (z.B. „Pacific Standard Time“) auswählen. Alternativ können Sie „2017-09-18T14:00:00Z“ ohne Zeitzone angeben. <p>**Hinweis:** Diese Startzeit muss dem [ISO 8601-Format für Datums-/Uhrzeitangaben](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) entsprechen und im [UTC-Datums-/Zeitformat](https://en.wikipedia.org/wiki/Coordinated_Universal_Time) angegeben werden, jedoch ohne [UTC-Abweichung](https://en.wikipedia.org/wiki/UTC_offset). Wenn Sie keine Zeitzone auswählen, müssen Sie den Buchstaben „Z“ ohne Leerzeichen anhängen. „Z“ bezieht sich auf die entsprechende [nautische Zeit](https://en.wikipedia.org/wiki/Nautical_time). <p>Bei einfachen Zeitplänen ist die Startzeit das erste Vorkommen. Bei erweiterten Serien wird der Trigger frühestens zur Startzeit ausgelöst. [*Wie kann ich Startdatum und -uhrzeit verwenden?* ](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md#start-time) |
   |||||

1. Nun erstellen Sie den restlichen Workflow mit weiteren Aktionen. Weitere Aktionen, die Sie hinzufügen können, finden Sie unter [Connectors für Azure Logic Apps](../connectors/apis-list.md).

## <a name="workflow-definition---sliding-window"></a>Workflowdefinition – gleitendes Fenster

In der zugrunde liegenden Workflowdefinition – im JSON-Format – können Sie die Definition des Triggers „Gleitendes Fenster“ mit den von Ihnen ausgewählten Optionen anzeigen. Um diese Definition anzuzeigen, wählen Sie auf der Designer-Symbolleiste die Option **Codeansicht** aus. Um zum Designer zurückzukehren, wählen Sie auf der Designer-Symbolleiste die Option **Designer** aus.

Dieses Beispiel zeigt, wie die Definition eines Triggers „Gleitendes Fenster“ in einer zugrunde liegenden Workflowdefinition aussehen kann, wenn die Wiederholung stündlich ausgeführt wird und die Verzögerung für jede Wiederholung fünf Sekunden beträgt:

``` json
"triggers": {
   "Recurrence": {
      "type": "SlidingWindow",
      "Sliding_Window": {
         "inputs": {
            "delay": "PT5S"
         },
         "recurrence": {
            "frequency": "Hour",
            "interval": 1,
            "startTime": "2019-05-13T14:00:00Z",
            "timeZone": "Pacific Standard Time"
         }
      }
   }
}
```

## <a name="next-steps"></a>Nächste Schritte

* [Verzögern der nächsten Aktion in Workflows](../connectors/connectors-native-delay.md)
* [Connectors für Logic Apps](../connectors/apis-list.md)