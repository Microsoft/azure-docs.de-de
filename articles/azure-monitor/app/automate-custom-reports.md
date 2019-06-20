---
title: Automatisieren von benutzerdefinierten Berichten mit Azure Application Insights-Daten
description: Automatisieren von benutzerdefinierten täglichen, wöchentlichen oder monatlichen Berichten mit Azure Application Insights-Daten
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/20/2019
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: f22cb620bf8cf56110bec60a4dd809066393a8ff
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67067656"
---
# <a name="automate-custom-reports-with-azure-application-insights-data"></a>Automatisieren von benutzerdefinierten Berichten mit Azure Application Insights-Daten

Regelmäßig erstellte Berichte halten ein Team darüber im Laufenden, wie sich ihre unternehmenskritischen Dienste entwickeln. Dank automatisierter Berichte, die zuverlässig Informationen liefern, ohne dass sich alle beim Portal anmelden müssen, können Entwickler, DevOps/SRE-Teams und deren Vorgesetzte produktiv arbeiten. Derartige Berichte können auch dabei unterstützen, allmähliche Erhöhungen bei Latenzen, Lasten oder Fehlerraten zu ermitteln, die möglicherweise keine Warnungsregeln auslösen.

Jedes Unternehmen stellt einzigartige Berichterstellungsanforderungen wie etwa Folgende: 

* Aggregationen von Metriken mit einem bestimmten Quantil oder benutzerdefinierte Metriken in einem Bericht
* Verschiedene Berichte für tägliche, wöchentliche und monatliche Rollups von Daten für verschiedene Zielgruppen
* Segmentierung durch benutzerdefinierte Attribute wie Region oder Umgebung 
* Gruppierung einiger KI-Ressourcen in einem einzelnen Bericht, auch wenn sie eventuell in verschiedenen Abonnements oder Ressourcengruppen usw. enthalten sind
* Separate Berichte mit vertraulichen Metriken, die an ausgewählte Zielgruppe gesendet werden
* Berichte für Projektbeteiligte, die womöglich keinen Zugriff auf die Ressourcen des Portals haben

> [!NOTE] 
> Bei der Application Insights-E-Mail mit der wöchentlichen Zusammenfassung waren keine Anpassungen möglich, weshalb diese zugunsten der unten aufgeführten benutzerdefinierten Optionen eingestellt wird. Die letzte E-Mail mit wöchentlicher Zusammenfassung wird am 11. Juni 2018 versendet. Konfigurieren Sie eine der folgenden Optionen, um ähnliche benutzerdefinierte Berichte zu erhalten (verwenden Sie die unten vorgeschlagene Abfrage).

## <a name="to-automate-custom-report-emails"></a>Automatisieren von benutzerdefinierten Berichts-E-Mails

[Application Insights-Daten können programmgesteuert abgefragt werden](https://dev.applicationinsights.io/), um benutzerdefinierte Berichte nach einem Zeitplan zu generieren. Die folgenden Optionen ermöglichen Ihnen einen schnellen Einstieg:

* [Berichte mit Microsoft Flow automatisieren](automate-with-flow.md)
* [Berichte mit Logic Apps automatisieren](automate-with-logic-apps.md)
* Verwenden Sie im Überwachungsszenario die [Azure-Funktionsvorlage](https://docs.microsoft.com/azure/azure-functions/functions-create-first-azure-function) für „geplante Application Insights-Zusammenfassungen“. Bei dieser Funktion werden E-Mails über SendGrid übermittelt. 

    ![Auswählen der Azure-Funktionsvorlage](./media/automate-custom-reports/azure-function-template.png)

## <a name="sample-query-for-a-weekly-digest-email"></a>Beispielabfrage für eine E-Mail mit wöchentlicher Zusammenfassung
Die folgende Abfrage zeigt, wie mehrere Datasets für eine E-Mail mit wöchentlicher Zusammenfassung, wie etwa bei einem Bericht, verknüpft werden können. Passen Sie sie nach Bedarf an, und verwenden Sie sie mit einer der oben aufgeführten Optionen zur Automatisierung eines wöchentlichen Berichts.   

```AIQL
let period=7d;
requests
| where timestamp > ago(period)
| summarize Row = 1, TotalRequests = sum(itemCount), FailedRequests = sum(toint(success == 'False')),
    RequestsDuration = iff(isnan(avg(duration)), '------', tostring(toint(avg(duration) * 100) / 100.0))
| join (
dependencies
| where timestamp > ago(period)
| summarize Row = 1, TotalDependencies = sum(itemCount), FailedDependencies = sum(success == 'False'),
    DependenciesDuration = iff(isnan(avg(duration)), '------', tostring(toint(avg(duration) * 100) / 100.0))
) on Row | join (
pageViews
| where timestamp > ago(period)
| summarize Row = 1, TotalViews = sum(itemCount)
) on Row | join (
exceptions
| where timestamp > ago(period)
| summarize Row = 1, TotalExceptions = sum(itemCount)
) on Row | join (
availabilityResults
| where timestamp > ago(period)
| summarize Row = 1, OverallAvailability = iff(isnan(avg(toint(success))), '------', tostring(toint(avg(toint(success)) * 10000) / 100.0)),
    AvailabilityDuration = iff(isnan(avg(duration)), '------', tostring(toint(avg(duration) * 100) / 100.0))
) on Row
| project TotalRequests, FailedRequests, RequestsDuration, TotalDependencies, FailedDependencies, DependenciesDuration, TotalViews, TotalExceptions, OverallAvailability, AvailabilityDuration
```

## <a name="application-insights-scheduled-digest-report"></a>Bericht „AppInsights: Geplante Übersicht“

1. Klicken Sie im Azure-Portal auf **Ressource erstellen** > **Compute** > **Funktionen-App**.

   ![Screenshot: Erstellen einer Azure-Ressource (Funktionen-App)](./media/automate-custom-reports/function-app-01.png)

2. Geben Sie die erforderlichen Informationen für Ihre App ein, und klicken Sie anschließend auf _Erstellen_. (Application Insights muss nur dann auf _Ein_ festgelegt werden, wenn Sie Ihre neue Funktionen-App mit Application Insights überwachen möchten.)

   ![Screenshot: Erstellen einer Azure-Ressource (Funktionen-App) – Einstellungen](./media/automate-custom-reports/function-app-02.png)

3. Klicken Sie nach Abschluss der Bereitstellung Ihrer neuen Funktionen-App auf **Zu Ressource wechseln**.

4. Klicken Sie auf **Neue Funktion**.

   ![Screenshot: Erstellen einer neuen Funktion](./media/automate-custom-reports/function-app-03.png)

5. Klicken Sie auf die Vorlage **_AppInsights: Geplante Übersicht_** .

     > [!NOTE]
     > Standardmäßig werden Funktionen-Apps mit Runtimeversion 2.x erstellt. Sie müssen [Runtimeversion 1.x von Azure Functions als Ziel einstellen](https://docs.microsoft.com/azure/azure-functions/set-runtime-version), um die Vorlage „AppInsights: Geplante Übersicht“ zu verwenden.

   ![Screenshot: Neue Funktion – Application Insights-Vorlage](./media/automate-custom-reports/function-app-04.png)

6. Geben Sie eine geeignete Empfänger-E-Mail-Adresse für Ihren Bericht ein, und klicken Sie anschließend auf **Erstellen**.

   ![Screenshot: Funktionseinstellungen](./media/automate-custom-reports/function-app-05.png)

7. Klicken Sie auf **<Ihre Funktionen-App>**  > **Plattformfeatures** > **Anwendungseinstellungen**.

    ![Screenshot: Azure-Funktionen-App – Einstellungen](./media/automate-custom-reports/function-app-07.png)

8. Erstellen Sie drei neue Anwendungseinstellungen mit geeigneten entsprechenden Werten (``AI_APP_ID``, ``AI_APP_KEY`` und ``SendGridAPI``). Wählen Sie **Speichern** aus.

     ![Screenshot: Schnittstelle für die Funktionsintegration](./media/automate-custom-reports/function-app-08.png)
    
    (Die Werte vom Typ „AI_“ befinden sich unter „API-Zugriff“ für die Application Insights-Ressource, für die ein Bericht erstellt werden soll. Sollten Sie über keinen Application Insights-API-Schlüssel verfügen, können Sie mithilfe der Option **API-Schlüssel erstellen** einen API-Schlüssel erstellen.)
    
   * AI_APP_ID: Anwendungs-ID
   * AI_APP_KEY: API-Schlüssel
   * SendGridAPI: SendGrid-API-Schlüssel

     > [!NOTE]
     > Falls Sie über kein SendGrid-Konto verfügen, können Sie eines erstellen. Die SendGrid-Dokumentation für Azure Functions finden Sie [hier](https://docs.microsoft.com/azure/azure-functions/functions-bindings-sendgrid). Eine kurze Erläuterung der Vorgehensweise zum Einrichten von SendGrid sowie zum Generieren eines API-Schlüssels finden Sie am Ende dieses Artikels. 

9. Klicken Sie auf **Integrieren** und anschließend unter „Ausgaben“ auf **SendGrid ($return)** .

     ![Screenshot: Ausgabe](./media/automate-custom-reports/function-app-09.png)

10. Wählen Sie unter **App-Einstellung für SendGrid-API-Schlüssel** Ihre neu erstellte App-Einstellung für **SendGridAPI** aus.

     ![Screenshot: Ausführen der Funktionen-App](./media/automate-custom-reports/function-app-010.png)

11. Führen Sie Ihre Funktionen-App aus, und testen Sie sie.

     ![Screenshot: Test](./media/automate-custom-reports/function-app-11.png)

12. Überprüfen Sie Ihren Posteingang, um sich zu vergewissern, dass die Nachricht erfolgreich gesendet/empfangen wurde.

     ![Screenshot: E-Mail-Betreffzeile](./media/automate-custom-reports/function-app-12.png)

## <a name="sendgrid-with-azure"></a>SendGrid mit Azure

Die folgenden Schritte sind nur relevant, wenn Sie noch kein SendGrid-Konto konfiguriert haben.

1. Klicken Sie im Azure-Portal auf **Ressource erstellen**, und suchen Sie nach **E-Mail-Zustellung über SendGrid**. Klicken Sie auf **Erstellen**, und füllen Sie die SendGrid-spezifischen Erstellungsanweisungen aus. 

     ![Screenshot: SendGrid-Ressource](./media/automate-custom-reports/function-app-13.png)

2. Klicken Sie nach Abschluss der Erstellung unter „SendGrid-Konten“ auf **Verwalten**.

     ![Screenshot: Einstellungen – API-Schlüssel](./media/automate-custom-reports/function-app-14.png)

3. Daraufhin wird die SendGrid-Website gestartet. Klicken Sie auf **Einstellungen** > **API-Schlüssel**.

     ![App-Screenshot: Erstellen und Anzeigen des API-Schlüssels](./media/automate-custom-reports/function-app-15.png)

4. Erstellen Sie einen API-Schlüssel, und klicken Sie auf **Erstellen und anzeigen**. (Ermitteln Sie anhand der SendGrid-Dokumentation zu eingeschränktem Zugriff, welche Berechtigungsstufe für Ihren API-Schlüssel geeignet ist. In diesem Beispiel wird Vollzugriff ausgewählt.)

   ![Screenshot: Vollzugriff](./media/automate-custom-reports/function-app-16.png)

5. Kopieren Sie den gesamten Schlüssel. Dieser Wert wird in den Einstellungen Ihrer Funktionen-App als Wert für „SendGridAPI“ benötigt.

   ![Screenshot: Kopieren des API-Schlüssels](./media/automate-custom-reports/function-app-17.png)

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr über die Erstellung von [Analytics-Abfragen](../../azure-monitor/log-query/get-started-queries.md).
* Erfahren Sie mehr über das [programmgesteuerte Abfragen von Application Insights-Daten](https://dev.applicationinsights.io/).
* Informieren Sie sich ausführlicher über [Logic Apps](https://docs.microsoft.com/azure/logic-apps/logic-apps-what-are-logic-apps).
* Erfahren Sie mehr über [Microsoft Flow](https://ms.flow.microsoft.com).
