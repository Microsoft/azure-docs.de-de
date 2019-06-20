---
title: Application Insights-Telemetrie in Visual Studio CodeLens | Microsoft Docs
description: Greifen Sie mit CodeLens in Visual Studio schnell auf die Application Insights-Telemetrie zu Anforderungen und Ausnahmen zu.
services: application-insights
documentationcenter: .net
author: NumberByColors
manager: carmonm
ms.assetid: 93559e44-23cb-4b9d-8425-60f7f0d0a82c
ms.service: application-insights
ms.custom: vs-azure
ms.workload: azure-vs
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 03/17/2017
ms.reviewer: mbullwin
ms.pm_owner: daviste;NumberByColors
ms.author: daviste
ms.openlocfilehash: d4f7cfab8291fcb6d0160035b9e421a8d47b2396
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/28/2019
ms.locfileid: "66255179"
---
# <a name="application-insights-telemetry-in-visual-studio-codelens"></a>Application Insights-Telemetrie in Visual Studio CodeLens
Methoden im Code Ihrer Web-App können mit Telemetrie zu Laufzeitausnahmen und Anforderungsantwortzeiten kommentiert werden. Wenn Sie [Azure Application Insights](../../azure-monitor/app/app-insights-overview.md) in Ihrer Anwendung installieren, wird die Telemetrie in Visual Studio [CodeLens](https://msdn.microsoft.com/library/dn269218.aspx) angezeigt. Hierbei handelt es sich um die praktischen Hinweise am Anfang jeder Funktion, die Aufschluss darüber geben, an wie vielen Stellen auf die Funktion verwiesen wird oder von wem sie zuletzt bearbeitet wurde.

![CodeLens](./media/visual-studio-codelens/codelens-overview.png)

> [!NOTE]
> Application Insights in CodeLens ist ab Visual Studio 2015 Update 3 sowie mit der neuesten Version der [Developer Analytics Tools-Erweiterung](https://visualstudiogallery.msdn.microsoft.com/82367b81-3f97-4de1-bbf1-eaf52ddc635a)verfügbar. CodeLens steht in der Enterprise- und in der Professional-Edition von Visual Studio zur Verfügung.
> 
> 

## <a name="where-to-find-application-insights-data"></a>Anzeigeort von Application Insights-Daten
Application Insights-Telemetrie finden Sie in den CodeLens-Indikatoren der öffentlichen Anforderungsmethoden Ihrer Webanwendung. CodeLens-Indikatoren werden in C#- und Visual Basic-Code über der Methode und anderen Deklarationen angezeigt. Wenn Application Insights-Daten für eine Methode verfügbar sind, werden Indikatoren für Anforderungen und Ausnahmen angezeigt (beispielsweise „100 Anforderungen, 1 % nicht erfolgreich“ oder „10 Ausnahmen“). Klicken Sie auf einen CodeLens-Indikator, um weitere Details anzuzeigen. 

> [!TIP]
> Wenn weitere CodeLens-Indikatoren angezeigt werden, dauert es unter Umständen etwas, bis Application Insights-Indikatoren zu Anforderungen und Ausnahmen geladen werden.
> 
> 

## <a name="exceptions-in-codelens"></a>Ausnahmen in CodeLens
![TBD](./media/visual-studio-codelens/codelens-exceptions.png)

Der CodeLens-Ausnahmenindikator gibt Aufschluss darüber, wie viele Ausnahmen in den letzten 24 Stunden beim Verarbeiten der von der Methode bedienten Anforderung aufgetreten sind. Dabei werden die 15 am häufigsten aufgetretenen Ausnahmen des jeweiligen Zeitraums angezeigt.

Durch Klicken auf den CodeLens-Ausnahmenindikator werden weitere Details angezeigt:

* Die prozentuale Veränderung der Ausnahmenanzahl der letzten 24 Stunden im Vergleich zu den vorherigen 24 Stunden
* Wählen Sie **Gehe zu Code** aus, um zum Quellcode der Funktion zu gelangen, durch die die Ausnahme ausgelöst wird.
* Wählen Sie **Suchen** aus, um für diese Ausnahme alle Instanzen der letzten 24 Stunden abzufragen.
* Wählen Sie **Trend** aus, um für die Ausnahme eine Trendvisualisierung der Vorkommen der letzten 24 Stunden anzuzeigen.
* Wählen Sie **Alle Ausnahmen in dieser App anzeigen** aus, um alle Ausnahmen abzufragen, die in den letzten 24 Stunden aufgetreten sind.
* Wählen Sie **Ausnahmetrends untersuchen** aus, um eine Trendvisualisierung für alle Ausnahmen der letzten 24 Stunden anzuzeigen. 

> [!TIP]
> Wenn in CodeLens keine Ausnahmen angezeigt werden, aber eigentlich Ausnahmen vorliegen müssten, vergewissern Sie sich, dass in CodeLens die richtige Application Insights-Ressource ausgewählt ist. Wenn Sie eine andere Ressource auswählen möchten, klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf Ihr Projekt, und wählen Sie **Application Insights > Telemetriequelle auswählen** aus. CodeLens wird nur für die 15 Ausnahmen angezeigt, die in Ihrer Anwendung innerhalb der letzten 24 Stunden am häufigsten aufgetreten sind. Für Ausnahmen ab dem 16. Häufigkeitsrang wird „0 Ausnahmen“ angezeigt. Ausnahmen aus ASP.NET-Ansichten werden unter Umständen nicht für die Controllermethoden angezeigt, die diese Ansichten generiert haben.
> 
> [!TIP]
> Falls in CodeLens „? Ausnahmen“ angezeigt wird, müssen Sie entweder Ihr Azure-Konto mit Visual Studio verknüpfen, oder Ihre Anmeldeinformationen für das Azure-Konto sind abgelaufen. Klicken Sie in einem solchen Fall auf „? Ausnahmen“, und wählen Sie **Konto hinzufügen** aus, um Ihre Anmeldeinformationen einzugeben.
> 
> 

## <a name="requests-in-codelens"></a>Anforderungen in CodeLens
![TBD](./media/visual-studio-codelens/codelens-requests.png)

Der CodeLens-Anforderungsindikator zeigt die Anzahl von HTTP-Anforderungen, die von einer Methode in den letzten 24 Stunden bedient wurden, sowie den Prozentsatz nicht erfolgreicher Anforderungen an.

Durch Klicken auf den CodeLens-Anforderungsindikator werden weitere Details angezeigt:

* Der absolute Wert und die prozentuale Veränderung der Anzahl von Anforderungen, nicht erfolgreichen Anforderungen und durchschnittlichen Antwortzeiten der letzten 24 Stunden im Vergleich zu den vorherigen 24 Stunden
* Die Zuverlässigkeit der Methode als Prozentsatz der Anforderungen, bei denen in den letzten 24 Stunden kein Fehler aufgetreten ist
* Wählen Sie für Anforderungen oder fehlerhafte Anforderungen die Option **Suche** aus, um alle (nicht erfolgreichen) Anforderungen der letzten 24 Stunden abzufragen.
* Wählen Sie **Trend** aus, um eine Trendvisualisierung für Anforderungen, nicht erfolgreiche Anforderungen oder durchschnittliche Antwortzeiten der letzten 24 Stunden anzuzeigen.
* Wählen Sie links oben in der CodeLens-Detailansicht den Namen der Application Insights-Ressource aus, um eine andere Quellressource für die CodeLens-Daten auszuwählen.

## <a name="next"></a>Nächste Schritte
|  |  |
| --- | --- |
| **[Arbeiten mit Application Insights in Visual Studio](../../azure-monitor/app/visual-studio.md)**<br/>Durchsuchen Sie die Telemetriedaten, zeigen Sie Daten in CodeLens an, und konfigurieren Sie Application Insights. Alles in Visual Studio. |![Klicken Sie mit der rechten Maustaste auf das Projekt, und wählen Sie „Application Insights > Durchsuchen“ aus.](./media/visual-studio-codelens/34.png) |
| **[Hinzufügen weiterer Daten](../../azure-monitor/app/asp-net-more.md)**<br/>Überwachen Sie Nutzung, Verfügbarkeit, Abhängigkeiten und Ausnahmen. Integrieren Sie Ablaufverfolgungen aus Protokollierungsframeworks. Schreiben Sie benutzerdefinierte Telemetriedaten. |![Visual Studio](./media/visual-studio-codelens/64.png) |
| **[Navigation und Dashboards im Application Insights-Portal](../../azure-monitor/app/overview-dashboard.md)**<br/>Enthält Informationen zu Dashboards, leistungsstarken Diagnose- und Analysetools, Warnungen und zum Export von Telemetriedaten sowie eine Livekarte der Abhängigkeiten Ihrer Anwendung. |![Visual Studio](./media/visual-studio-codelens/62.png) |

