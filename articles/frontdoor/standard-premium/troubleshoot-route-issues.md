---
title: Behandeln von Problemen bei der Konfiguration von Azure Front Door Standard/Premium
description: In diesem Tutorial erfahren Sie, wie Sie einige häufige Probleme bei Ihrer Azure Front Door Standard/Premium-Instanz behandeln können.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: 4690a513494d794377ee0c2e8cfb101e8fd66a0f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101098080"
---
# <a name="troubleshooting-common-routing-problems-with-azure-front-door-standardpremium"></a>Behandeln von häufigen Routingproblemen bei Azure Front Door Standard/Premium

In diesem Artikel wird beschrieben, wie Sie häufige Routingprobleme in Ihrer Azure Front Door-Konfiguration beheben können.

## <a name="503-response-from-azure-front-door-after-a-few-seconds"></a>Antwort des Typs 503 von Azure Front Door nach einigen Sekunden

### <a name="symptom"></a>Symptom

* Reguläre Anforderungen, die ohne Azure Front Door an das Back-End gesendet werden, sind erfolgreich. Das Durchlaufen von Azure Front Door führt zu 503-Fehlermeldungen.
* Der Fehler von Azure Front Door wird in der Regel nach ungefähr 30 Sekunden angezeigt.

### <a name="cause"></a>Ursache

Für dieses Problem gibt es zwei mögliche Ursachen:
 
* Ihr Ursprung benötigt länger als das konfigurierte Timeout (Standardwert: 30 Sekunden), um die Anforderung von Azure Front Door zu erhalten.
* Die zum Beantworten der Anforderung von Azure Front Door erforderliche Zeit überschreitet den Timeoutwert. 

### <a name="troubleshooting-steps"></a>Schritte zur Problembehandlung

* Senden Sie die Anforderung direkt an Ihr Back-End (ohne Azure Front Door). Beobachten Sie, wie lange es normalerweise dauert, bis Ihr Back-End antwortet.
* Senden Sie die Anforderung über Azure Front Door, und prüfen Sie, ob Sie Antworten des Typs 503 erhalten. Falls nicht, ist das Timeout möglicherweise nicht das Problem. Wenden Sie sich an den Support.
* Wenn bei der Verwendung von Azure Front Door ein Antwortcode mit Fehlern des Typs 503 zurückgegeben wird, konfigurieren Sie das `sendReceiveTimeout`-Feld für Azure Front Door. Sie können das Standardtimeout auf bis zu vier Minuten (240 Sekunden) verlängern. Die Einstellung befindet sich unter `Endpoint Setting` und heißt `Origin response timeout`. 

## <a name="requests-sent-to-the-custom-domain-return-a-400-status-code"></a>Für an die benutzerdefinierte Domäne gesendete Anforderungen wird der Statuscode 400 zurückgegeben

### <a name="symptom"></a>Symptom

* Sie haben eine Azure Front Door-Instanz erstellt, aber für eine Anforderung an die Domäne oder den Front-End-Host wird der HTTP-Statuscode 400 zurückgegeben.
* Sie haben eine DNS-Zuordnung für eine benutzerdefinierte Domäne zu dem von Ihnen konfigurierten Front-End-Host erstellt. Beim Senden einer Anforderung an den Hostnamen der benutzerdefinierten Domäne wird jedoch ein HTTP-Statuscode 400 zurückgegeben. Außerdem wird offenbar kein Routing zu dem von Ihnen konfigurierten Back-End durchgeführt.

### <a name="cause"></a>Ursache

Das Problem tritt auf, wenn Sie keine Routingregel für die benutzerdefinierte Domäne konfiguriert haben, die als Front-End-Host hinzugefügt wurde. Für diesen Front-End-Host muss explizit eine Routingregel hinzugefügt werden. Selbst wenn bereits eine Routingregel für den Front-End-Host unter der Azure Front Door-Unterdomäne (*.azurefd.net) konfiguriert wurde, muss dies erfolgen.

### <a name="troubleshooting-steps"></a>Schritte zur Problembehandlung

Fügen Sie eine Routingregel für die benutzerdefinierte Domäne hinzu, um Datenverkehr an die ausgewählten Ursprungsgruppe weiterzuleiten.

## <a name="azure-front-door-doesnt-redirect-http-to-https"></a>Azure Front Door leitet HTTP oder HTTPS nicht weiter

### <a name="symptom"></a>Symptom

Für Azure Front Door ist eine Routingregel vorhanden, nach der HTTP zu HTTPS umgeleitet wird, doch beim Zugriff auf die Domäne wird HTTP als Protokoll beibehalten.

### <a name="cause"></a>Ursache

Dieses Verhalten kann auftreten, wenn Sie die Routingregeln für Azure Front Door nicht ordnungsgemäß konfiguriert haben. Im Grunde genommen ist Ihre aktuelle Konfiguration nicht spezifisch und kann miteinander in Konflikt stehende Regeln aufweisen.

### <a name="troubleshooting-steps"></a>Schritte zur Problembehandlung


## <a name="request-to-the-frontend-host-name-returns-a-411-status-code"></a>Eine Anforderung an den Front-End-Hostnamen gibt den Statuscode 411 zurück

### <a name="symptom"></a>Symptom

Sie haben eine Azure Front Door Standard/Premium-Instanz erstellt und einen Front-End-Host, eine Ursprungsgruppe mit mindestens einem Ursprung und eine Routingregel konfiguriert, die den Front-End-Host mit der Ursprungsgruppe verbindet. Ihre Inhalte sind offenbar nicht verfügbar, wenn Sie eine Anforderung an den konfigurierten Front-End-Host senden, da der HTTP-Statuscode 411 zurückgegeben wird.

Antworten auf diese Anforderungen können auch eine HTML-Fehlerseite mit einer Erläuterung im Antworttext enthalten. Beispiel: `HTTP Error 411. The request must be chunked or have a content length`.

### <a name="cause"></a>Ursache

Dieses Symptom kann verschiedene Ursachen haben. Grundsätzlich liegt es daran, dass Ihre HTTP-Anforderung nicht vollständig RFC-konform ist. 

Ein Beispiel für Nichtkonformität ist eine `POST`-Anforderung, die ohne einen `Content-Length`- oder `Transfer-Encoding`-Header gesendet wird (z. B. mit `curl -X POST https://example-front-door.domain.com`). Diese Anforderung erfüllt nicht die Voraussetzungen, die in [RFC 7230](https://tools.ietf.org/html/rfc7230#section-3.3.2)festgelegt sind. Azure Front Door würde sie mit einer HTTP 411-Antwort blockieren.

Dieses Verhalten ist unabhängig von der WAF-Funktionalität (Web Application Firewall) von Azure Front Door. Derzeit gibt es keine Möglichkeit, dieses Verhalten zu deaktivieren. Alle HTTP-Anforderungen müssen die Anforderungen erfüllen, selbst wenn die WAF-Funktion nicht verwendet wird.

### <a name="troubleshooting-steps"></a>Schritte zur Problembehandlung

- Vergewissern Sie sich, dass Ihre Anforderungen die Vorgaben in den entsprechenden RFCs erfüllen.

- Notieren Sie sich den HTML-Nachrichtentext, der als Antwort auf Ihre Anforderung zurückgegeben wird. Ein Nachrichtentext erläutert häufig genau, *in welcher Hinsicht* Ihre Anforderung nicht kompatibel ist.

## <a name="next-steps"></a>Nächste Schritte

Informieren Sie sich über das [Erstellen einer Instanz von Front Door Standard/Premium](create-front-door-portal.md).
