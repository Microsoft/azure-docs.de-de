---
title: Analysieren von Azure Active Directory-Aktivitätsprotokollen mithilfe von Azure Monitor-Protokollen | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie Azure Active Directory-Aktivitätsprotokolle mithilfe von Azure Monitor-Protokollen analysieren.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4535ae65-8591-41ba-9a7d-b7f00c574426
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: priyamo
ms.reviewer: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1651bb2898a2bd1e3e0c3fbbce77dc4106d76e7a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60438247"
---
# <a name="analyze-azure-ad-activity-logs-with-azure-monitor-logs"></a>Analysieren von Azure AD-Aktivitätsprotokollen mithilfe von Azure Monitor-Protokollen

Nachdem Sie [Azure AD-Aktivitätsprotokolle mit Azure Monitor-Protokollen integriert haben](howto-integrate-activity-logs-with-log-analytics.md), können Sie die Leistungsfähigkeit von Azure Monitor-Protokollen nutzen, um Einblicke in Ihre Umgebung zu erhalten. Sie können auch die [Log Analytics-Ansichten für Azure AD-Aktivitätsprotokolle](howto-install-use-log-analytics-views.md) installieren, um Zugriff auf vorgefertigte Berichte zu Überwachungs- und Anmeldeereignissen in Ihrer Umgebung zu erhalten.

In diesem Artikel erfahren Sie, wie Sie die Azure AD-Aktivitätsprotokolle in Ihrem Log Analytics-Arbeitsbereich analysieren. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Voraussetzungen 

Um dies nachvollziehen zu können, benötigen Sie Folgendes:

* Einen Log Analytics-Arbeitsbereich in Ihrem Azure-Abonnement. Informationen zum Erstellen eines Log Analytics-Arbeitsbereichs finden Sie [hier](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace).
* Führen Sie zuerst die Schritte aus, um [die Azure AD-Aktivitätsprotokolle in Ihren Log Analytics-Arbeitsbereich umzuleiten](howto-integrate-activity-logs-with-log-analytics.md).

## <a name="navigate-to-the-log-analytics-workspace"></a>Navigieren zum Log Analytics-Arbeitsbereich

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an. 

2. Wählen Sie **Azure Active Directory** aus, und wählen Sie dann **Protokolle** im Abschnitt **Überwachung** aus, um Ihren Log Analytics-Arbeitsbereich zu öffnen. Der Arbeitsbereich wird mit einer Standardabfrage geöffnet.

    ![Standardabfrage](./media/howto-analyze-activity-logs-log-analytics/defaultquery.png)


## <a name="view-the-schema-for-azure-ad-activity-logs"></a>Anzeigen des Schemas für Azure AD-Aktivitätsprotokollen

Die Protokolle werden per Push an die Tabellen **AuditLogs** und **SigninLogs** im Arbeitsbereich übertragen. So zeigen Sie das Schema für diese Tabellen an

1. Wählen Sie in der Standardabfrageansicht des vorherigen Abschnitts **Schema** aus, und erweitern Sie den Arbeitsbereich. 

2. Erweitern Sie den Abschnitt **Protokollverwaltung**, und erweitern Sie dann entweder **AuditLogs** oder **SignInLogs**, um das Protokollschema anzuzeigen.
    ![Überwachungsprotokolle](./media/howto-analyze-activity-logs-log-analytics/auditlogschema.png) ![Anmeldeprotokolle](./media/howto-analyze-activity-logs-log-analytics/signinlogschema.png)

## <a name="query-the-azure-ad-activity-logs"></a>Abfragen der Azure AD-Aktivitätsprotokolle

Nachdem Sie nun die Protokolle in Ihrem Arbeitsbereich haben, können Sie Abfragen für diese ausführen. Um beispielsweise die in der letzten Woche am häufigsten verwendeten Anwendungen abzurufen, ersetzten Sie die Standardabfrage durch Folgendes, und wählen Sie **Ausführen** aus.

```
SigninLogs 
| where CreatedDateTime >= ago(7d)
| summarize signInCount = count() by AppDisplayName 
| sort by signInCount desc 
```

Um die häufigsten Überwachungsereignisse in der letzten Woche abzurufen, verwenden Sie die folgende Abfrage:

```
AuditLogs 
| where TimeGenerated >= ago(7d)
| summarize auditCount = count() by OperationName 
| sort by auditCount desc 
```
## <a name="alert-on-azure-ad-activity-log-data"></a>Benachrichtigung bei Azure AD-Aktivitätsprotokolldaten

Sie können auch Benachrichtigungen für Ihre Abfrage einrichten. So konfigurieren Sie beispielsweise eine Benachrichtigung, wenn mehr als 10 Anwendungen in der letzten Woche verwendet wurden:

1. Wählen Sie im Arbeitsbereich **Benachrichtigung festlegen** aus, um die Seite **Regel erstellen** zu öffnen.

    ![Warnung festlegen](./media/howto-analyze-activity-logs-log-analytics/setalert.png)

2. Wählen Sie die standardmäßig in der Benachrichtigung erstellten **Benachrichtigungskriterien** aus, und aktualisieren Sie den **Schwellenwert** in der Standardmetrik auf 10.

    ![Benachrichtigungskriterien](./media/howto-analyze-activity-logs-log-analytics/alertcriteria.png)

3. Geben Sie einen Namen und eine Beschreibung für die Benachrichtigung ein, und wählen Sie den Schweregrad aus. In unserem Beispiel könnten wir ihn auf **Information** festlegen.

4. Wählen Sie die **Aktionsgruppe** aus, die benachrichtigt wird, wenn das Signal auftritt. Sie können auch auswählen, Ihr Team per E-Mail oder SMS zu benachrichtigen, oder Sie könnten die Aktion mithilfe von Webhooks, Azure-Funktionen oder Logik-Apps automatisieren. Erfahren Sie mehr über das [Erstellen und Verwalten von Benachrichtigungen im Azure-Portal](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups).

5. Nachdem Sie die Benachrichtigung konfiguriert haben, wählen Sie **Benachrichtigung erstellen**aus, um sie zu aktivieren. 

## <a name="install-and-use-pre-built-views-for-azure-ad-activity-logs"></a>Installieren und Verwenden von vorgefertigten Ansichten für Azure AD-Aktivitätsprotokolle

Sie können auch die vorgefertigten Log Analytics-Ansichten für Azure AD-Aktivitätsprotokolle herunterladen. Die Ansichten bieten mehrere Berichte, die im Zusammenhang mit allgemeinen Szenarien mit Überwachungs- und Anmeldeereignissen stehen. Sie können auch aufgrund jeglicher Daten benachrichtigen, die in den Berichten bereitgestellt werden, indem Sie die im vorherigen Abschnitt beschriebenen Schritte verwenden.

* **Azure AD-Kontobereitstellungsereignisse**: In dieser Ansicht werden Berichte im Zusammenhang mit der Überwachung von Bereitstellungsaktivitäten angezeigt, also z.B. die Anzahl neuer bereitgestellter Benutzer und die Bereitstellungsfehler, die Anzahl aktualisierter Benutzer und die Aktualisierungsfehler sowie die Anzahl aufgehobener Benutzerbereitstellungen und der entsprechenden Fehler.    
* **Anmeldeereignisse**: In dieser Ansicht werden die relevantesten Berichte zur Überwachung von Anmeldeaktivitäten angezeigt, z.B. Anmeldungen nach Anwendungen, Benutzern, Geräten sowie eine Zusammenfassungsansicht, in der die Anzahl der Anmeldungen im Laufe der Zeit nachverfolgt wird.
* **Ihre Einwilligung gebende Benutzer**: In dieser Ansicht werden Berichte im Zusammenhang mit der Benutzereinwilligung angezeigt, z.B. die Einwilligungen nach Benutzern, die Anmeldungen nach Benutzern, die ihre Einwilligung erteilt haben, sowie die Anmeldungen nach Anwendungen für alle einwilligungsabhängigen Anwendungen. 

Informationen zum Installieren und Verwenden von Log Analytics-Ansichten für Azure AD-Aktivitätsprotokolle finden Sie [hier](howto-install-use-log-analytics-views.md). 


## <a name="next-steps"></a>Nächste Schritte

* [Erste Schritte mit Abfragen in Azure Monitor-Protokollen](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries)
* [Erstellen und Verwalten von Benachrichtigungsgruppen im Azure-Portal](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups)
* [Installieren und Verwenden der Log Analytics-Ansichten für Azure Active Directory](howto-install-use-log-analytics-views.md)