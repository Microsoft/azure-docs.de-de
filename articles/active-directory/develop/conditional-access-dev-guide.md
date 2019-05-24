---
title: Anleitung für Entwickler zum bedingten Zugriff mit Azure Active Directory
description: Anleitung und Szenarien für Entwickler zum bedingten Zugriff mit Azure AD
services: active-directory
keywords: ''
author: rwike77
manager: CelesteDG
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda
ms.date: 02/28/2019
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0674934f7105df3874048308e98fd582d32e72bc
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65962836"
---
# <a name="developer-guidance-for-azure-active-directory-conditional-access"></a>Anleitung für Entwickler zum bedingten Zugriff mit Azure Active Directory

Das Feature für bedingten Zugriff in Azure Active Directory (Azure AD) ist eine von mehreren Möglichkeiten, wie Sie Ihre App und einen Dienst schützen können. Der bedingte Zugriff ermöglicht Entwicklern und Unternehmenskunden den Schutz von Diensten auf unterschiedliche Weise, einschließlich:

* Multi-Factor Authentication
* Ermöglicht nur bei Intune registrierten Geräten den Zugriff auf bestimmte Dienste
* Einschränken von Benutzerstandorten und IP-Adressbereichen

Weitere Informationen über alle Funktionen des bedingten Zugriffs finden Sie unter [Bedingter Zugriff in Azure Active Directory](../active-directory-conditional-access-azure-portal.md).

Für Entwickler, die Apps für Azure AD erstellen, wird in diesem Artikel veranschaulicht, wie der bedingte Zugriff eingesetzt werden kann. Außerdem werden die Auswirkungen des Zugriffs auf Ressourcen beschrieben, über die Sie keine Kontrolle haben und auf die ggf. Richtlinien für den bedingten Zugriff angewendet werden. Darüber hinaus geht es im Artikel um die Auswirkungen des bedingten Zugriffs auf den „Im Auftrag von“-Ablauf, auf Web-Apps, auf den Zugriff auf Microsoft Graph und auf das Aufrufen von APIs.

Dabei werden Kenntnisse über [einzel](quickstart-v1-integrate-apps-with-azure-ad.md)- und [mehrinstanzenfähige](howto-convert-app-to-be-multi-tenant.md) Apps sowie über [allgemeine Authentifizierungsmuster](authentication-scenarios.md) vorausgesetzt.

## <a name="how-does-conditional-access-impact-an-app"></a>Welche Auswirkungen hat der bedingte Zugriff auf eine App?

### <a name="app-types-impacted"></a>Betroffene App-Typen

In den meisten gängigen Fällen verändert der bedingte Zugriff das Verhalten einer App nicht. Er erfordert auch keine Änderungen seitens des Entwicklers. Nur in bestimmten Fällen, wenn eine App im Hintergrund oder indirekt ein Token für einen Dienst anfordert, sind Änderungen am Code einer App erforderlich, um die besonderen Anforderungen des bedingten Zugriffs zu erfüllen. Dafür kann manchmal nur das Ausführen einer Anforderung für die interaktive Anmeldung erforderlich sein.

Insbesondere die folgenden Szenarien erfordern Code zum Behandeln der speziellen Anforderungen des bedingten Zugriffs:

* Apps für den „Im Auftrag von“-Ablauf
* Apps mit Zugriff auf mehrere Dienste/Ressourcen
* Einzelseiten-Apps, die ADAL.js verwenden
* Web-Apps, die eine Ressource aufrufen

Sie können Richtlinien für den bedingten Zugriff auf die App anwenden, aber auch auf eine Web-API, auf die Ihre App zugreift. Weitere Informationen zum Konfigurieren einer Richtlinie für bedingten Zugriff finden Sie unter [Schnellstart: Anfordern der mehrstufigen Authentifizierung (Multi-Factor Authentication, MFA) für bestimmte Apps über den bedingten Zugriff von Azure Active Directory](../conditional-access/app-based-mfa.md).

Abhängig vom jeweiligen Szenario können Unternehmenskunden jederzeit Richtlinien für den bedingten Zugriff anwenden und entfernen. Damit Ihre App beim Anwenden einer neuen Richtlinie weiterhin funktioniert, müssen Sie die Behandlung der speziellen Anforderungen implementieren. Die folgenden Beispiele veranschaulichen die Behandlung dieser Anforderungen.

### <a name="conditional-access-examples"></a>Beispiele für den bedingten Zugriff

Einige Szenarien erfordern Änderungen am Code, um den bedingten Zugriff nutzen zu können, während andere ohne Änderungen funktionieren. Hier finden Sie einige Szenarien, die den bedingten Zugriff für die mehrstufige Authentifizierung verwenden und Einblick in die Unterschiede geben.

* Sie erstellen eine iOS-App mit nur einem Mandanten und wenden eine Richtlinie für den bedingten Zugriff an. Die App meldet einen Benutzer an, aber fordert keinen Zugriff auf eine API an. Wenn sich der Benutzer anmeldet, wird die Richtlinie automatisch aufgerufen. Der Benutzer muss eine mehrstufige Authentifizierung (Multi-Factor Authentication, MFA) ausführen. 
* Sie erstellen eine native App, die einen Dienst der mittleren Ebene für den Zugriff auf eine Downstream-API verwendet. Ein Unternehmenskunde, der diese App verwendet, wendet eine Richtlinie für die Downstream-API an. Wenn sich ein Endbenutzer anmeldet, fordert die native App Zugriff auf die mittlere Ebene an und sendet das Token. Die mittlere Ebene führt den „Im Auftrag von“-Ablauf aus, um Zugriff auf die Downstream-API anzufordern. An diesem Punkt wird der mittleren Ebene eine Anspruchanforderung übermittelt. Die mittlere Ebene sendet die Anforderung wieder an die native App, die ihrerseits die Richtlinie für den bedingten Zugriff erfüllen muss.

#### <a name="microsoft-graph"></a>Microsoft Graph

Für Microsoft Graph gelten besondere Überlegungen beim Erstellen von Apps in Umgebungen mit bedingtem Zugriff. Im Allgemeinen verhalten sich die Mechanismen des bedingtem Zugriffs gleich, aber die Richtlinien, die Ihre Benutzer sehen, basieren auf den zugrunde liegenden Daten, die Ihre App vom Diagramm anfordert. 

Insbesondere stellen alle Microsoft Graph-Bereiche ein bestimmtes Dataset dar, auf das individuelle Richtlinien angewendet werden können. Da den Richtlinien für den bedingten Zugriff die spezifischen Datasets zugeordnet sind, erzwingt Azure AD die Richtlinien für den bedingten Zugriff auf der Grundlage der Daten die dem Diagramm zugrunde liegen, und nicht auf der Grundlage von Microsoft Graph selbst.

Wenn eine App beispielsweise die folgenden Microsoft Graph-Bereiche anfordert,

```
scopes="Bookings.Read.All Mail.Read"
```

Eine App kann von ihren Benutzern fordern, dass alle Richtlinien erfüllt werden, die in „Buchungen“ und „Exchange“ festgelegt sind. Einigen Bereichen können mehrere Datasets zugeordnet sein, wenn der Zugriff gewährt wird. 

### <a name="complying-with-a-conditional-access-policy"></a>Einhalten einer Richtlinie für den bedingten Zugriff

Bei mehreren verschiedenen App-Topologien wird beim Einrichten der Sitzung eine Richtlinie für den bedingten Zugriff ausgewertet. Da Richtlinien für den bedingten Zugriff auf App- und Dienstebene ausgeführt werden, hängt der Zeitpunkt ihres Aufrufs stark vom jeweiligen Szenario ab.

Wenn Ihre App versucht, über eine Richtlinie für den bedingten Zugriff auf einen Dienst zuzugreifen, muss sie möglicherweise eine Anforderung für den bedingten Zugriff erfüllen. Diese Anforderung wird im `claims`-Parameter codiert, der in einer Antwort von Azure AD empfangen wird. Dies ist ein Beispiel für diesen Anforderungsparameter: 

```
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

Entwickler können diese Anforderung annehmen und an eine neue Anforderung an Azure AD anfügen. Beim Durchlaufen dieses Zustands werden die Endbenutzer zum Ausführen aller erforderlichen Aktionen zur Einhaltung der Richtlinie für den bedingten Zugriff aufgefordert. In den folgenden Szenarien werden Einzelheiten des Fehlers und das Extrahieren des Parameters erläutert.

## <a name="scenarios"></a>Szenarien

### <a name="prerequisites"></a>Voraussetzungen

Der bedingte Zugriff von Azure AD ist ein Feature von [Azure AD Premium](https://docs.microsoft.com/azure/active-directory/active-directory-whatis). Weitere Informationen zu Lizenzanforderungen finden Sie im [Bericht zu nicht lizenzierter Nutzung](../active-directory-conditional-access-unlicensed-usage-report.md). Entwickler können sich für das [Microsoft Developer Network](https://msdn.microsoft.com/dn308572.aspx) registrieren, das ein kostenloses Abonnement für die Enterprise Mobility Suite mit Azure AD Premium umfasst.

### <a name="considerations-for-specific-scenarios"></a>Überlegungen für bestimmte Szenarien

Die folgenden Informationen gelten nur in diesen speziellen Szenarien:

* Apps für den „Im Auftrag von“-Ablauf
* Apps mit Zugriff auf mehrere Dienste/Ressourcen
* Einzelseiten-Apps, die ADAL.js verwenden

In den folgenden Abschnitten werden allgemeine Szenarien beschrieben, die komplexer sind. Das grundlegende Prinzip dabei ist, dass Richtlinien für den bedingten Zugriff zu dem Zeitpunkt ausgewertet werden, zu dem das Token für einen Dienst mit einer Richtlinie für den bedingten Zugriff angefordert wird.

## <a name="scenario-app-performing-the-on-behalf-of-flow"></a>Szenario: App für den Ablauf vom Typ „Im Auftrag von“

In diesem Szenario wird der Fall behandelt, bei dem eine native App einen Webdienst bzw. eine API aufruft. Der Dienst wiederum führt den Flow „On-Behalf-Of“ aus, um einen Downstreamdienst aufzurufen. In diesem Fall haben wir die Richtlinie für den bedingten Zugriff auf den Downstreamdienst (Web-API 2) angewendet und verwenden eine native App anstelle einer Server-/Daemon-App. 

![Flussdiagramm für Apps mit „Im Auftrag von“-Ablauf](./media/conditional-access-dev-guide/app-performing-on-behalf-of-scenario.png)

Die anfängliche Tokenanforderung für die Web-API 1 fordert den Endbenutzer nicht zur mehrstufigen Authentifizierung auf, da die Web-API 1 nicht immer auf die Downstream-API zugreift. Wenn die Web-API 1 versucht, im Auftrag des Benutzers ein Token für die Web-API 2 anzufordern, führt diese Anforderung zu einem Fehler, da der Benutzer nicht mit der mehrstufigen Authentifizierung angemeldet ist.

Azure AD gibt eine HTTP-Antwort mit interessanten Daten zurück:

> [!NOTE]
> In diesem Fall ist dies eine Fehlerbeschreibung für die mehrstufige Authentifizierung, aber es gibt eine Vielzahl von `interaction_required`-Fehlern mit Bezug auf den bedingten Zugriff.

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API 2 App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

In Web-API 1 fangen wir den Fehler `error=interaction_required` ab und senden die `claims`-Anforderung an die Desktop-App zurück. An diesem Punkt kann die Desktop-App einen neuen `acquireToken()`-Aufruf erstellen und die `claims`-Anforderung als zusätzlichen Parameter an die Abfragezeichenfolge anfügen. Diese neue Anforderung fordert den Benutzer zum Durchführen einer mehrstufigen Authentifizierung auf, sendet das neue Token an die Web-API 1 zurück und schließt den „Im-Auftrag-von“-Ablauf ab.

Wenn Sie dieses Szenario testen möchten, sehen Sie sich unser [.NET-Codebeispiel](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca) an. Es zeigt, wie die Anspruchanforderung von der Web-API 1 zurück an die native App übergeben und in der Client-App eine neue Anforderung erstellt wird.

## <a name="scenario-app-accessing-multiple-services"></a>Szenario: App, die auf mehrere Dienste zugreift

In diesem Szenario wird der Fall behandelt, bei dem eine Web-App auf zwei Dienst zugreift, von denen für einen eine Richtlinie für den bedingten Zugriff gilt. Abhängig von Ihrer App-Logik gibt es möglicherweise einen Pfad, in dem Ihre App keinen Zugriff auf beide Webdienste benötigt. In diesem Szenario spielt die Reihenfolge, in der Sie ein Token anfordern, für den Endbenutzer eine wichtige Rolle.

Wir gehen von den beiden Webdiensten A und B aus, wobei für Webdienst B unsere Richtlinie für den bedingten Zugriff gilt. Während die anfängliche interaktive Authentifizierungsanforderung Zustimmung für beide Dienste erfordert, ist die Richtlinie für den bedingten Zugriff nicht in allen Fällen erforderlich. Wenn die App ein Token für den Webdienst B anfordert, wird die Richtlinie aufgerufen, und nachfolgende Anforderungen für den Webdienst A sind ebenfalls erfolgreich.

![Flussdiagramm für Apps mit Zugriff auf mehrere Dienste](./media/conditional-access-dev-guide/app-accessing-multiple-services-scenario.png)

Wenn andererseits die App zunächst ein Token für den Webdienst A anfordert, ruft der Endbenutzer damit nicht die Richtlinie für den bedingten Zugriff auf. So kann der App-Entwickler die Endbenutzererfahrung steuern, indem er die Richtlinie für den bedingten Zugriff nicht in allen Fällen erzwingt. Schwierig wird es, wenn die Anwendung anschließend ein Token für den Webdienst B anfordert. In diesem Fall muss der Endbenutzer die Richtlinie für den bedingten Zugriff erfüllen. Wenn die App `acquireToken` versucht, wird möglicherweise die folgende Fehlermeldung generiert (in der folgenden Abbildung dargestellt):

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
claims={"access_token":{"polids":{"essential":true,"Values":["<GUID>"]}}}
```

![App mit Zugriff auf mehrere Dienste fordert ein neues Token an.](./media/conditional-access-dev-guide/app-accessing-multiple-services-new-token.png)

Wenn die App die ADAL-Bibliothek verwendet, wird bei einem Fehler beim Abrufen des Tokens immer interaktiv ein Wiederholungsversuch ausgeführt. Bei dieser interaktiven Anforderung hat der Endbenutzer die Möglichkeit, die Anforderungen für den bedingten Zugriff zu erfüllen. Dies gilt immer, außer wenn es sich um eine `AcquireTokenSilentAsync`- oder `PromptBehavior.Never`-Anforderung handelt. In diesem Fall muss die App eine interaktive ```AcquireToken```-Anforderung ausführen, damit der Endbenutzer die Richtlinie erfüllen kann.

## <a name="scenario-single-page-app-spa-using-adaljs"></a>Szenario: Single-Page-App (SPA), die ADAL.js verwendet

In diesem Szenario werden Single-Page-Webanwendungen (Single-Page Applications, SPAs) behandelt, die ADAL.js zum Aufrufen einer durch den bedingten Zugriff geschützten Web-API verwendet. Dies ist eine einfache Architektur, die jedoch einige Feinheiten beinhaltet, die bei der Entwicklung für bedingten Zugriff berücksichtigt werden müssen.

In ADAL.js stehen mehrere Funktionen für das Abrufen von Token zur Verfügung: `login()`, `acquireToken(...)`, `acquireTokenPopup(…)` und `acquireTokenRedirect(…)`.

* `login()` ruft über eine interaktive Anmeldeanforderung ein ID-Token, aber keine Zugriffstoken für Dienste ab (einschließlich einer durch den bedingten Zugriff geschützten Web-API).
* `acquireToken(…)` kann dann verwendet werden, um im Hintergrund ein Zugriffstoken abzurufen – also ohne eine Anzeige auf der Benutzeroberfläche.
* `acquireTokenPopup(…)` und `acquireTokenRedirect(…)` werden beide verwendet, um interaktiv ein Token für eine Ressource anzufordern – beide zeigen also immer eine Benutzeroberfläche für die Anmeldung an.

Wenn eine App zum Aufrufen einer Web-API ein Zugriffstoken benötigt, versucht sie einen Aufruf von `acquireToken(…)`. Wenn die Tokensitzung abgelaufen ist oder eine Richtlinie für den bedingten Zugriff eingehalten werden muss, führt die *acquireToken*-Funktion zu einem Fehler, und die App verwendet `acquireTokenPopup()` oder `acquireTokenRedirect()`.

![Flussdiagramm für Einzelseiten-Apps mit ADAL](./media/conditional-access-dev-guide/spa-using-adal-scenario.png)

Im Folgenden finden Sie ein Beispiel für das Szenario für den bedingten Zugriff. Der Endbenutzer hat die Website gerade erst aufgerufen und verfügt noch nicht über eine Sitzung. Wir führen einen Aufruf von `login()` durch und erhalten ohne mehrstufige Authentifizierung ein ID-Token. Anschließend wählt der Benutzer eine Schaltfläche aus, für die die App Daten von einer Web-API anfordern muss. Die App versucht, einen `acquireToken()`-Aufruf durchzuführen. Dieser führt jedoch zu einem Fehler, da der Benutzer keine mehrstufige Authentifizierung durchgeführt hat, aber die Richtlinie für bedingten Zugriff einhalten muss.

Azure AD sendet die folgende HTTP-Antwort zurück:

```
HTTP 400; Bad Request
error=interaction_required
error_description=AADSTS50076: Due to a configuration change made by your administrator, or because you moved to a new location, you must use multi-factor authentication to access '<Web API App/Client ID>'.
```

Die App muss `error=interaction_required` abfangen. Die Anwendung kann dann `acquireTokenPopup()` oder `acquireTokenRedirect()` für dieselbe Ressource verwenden. Der Benutzer muss eine mehrstufige Authentifizierung durchführen. Nachdem der Benutzer die mehrstufige Authentifizierung abgeschlossen hat, wird der App ein neues Zugriffstoken für die angeforderte Ressource ausgestellt.

Wenn Sie dieses Szenario testen möchten, sehen Sie sich unser [“Im-Auftrag-von“-Codebeispiel für eine JS-SPA](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof-ca) an. Dieses Codebeispiel verwendet die Richtlinie für den bedingten Zugriff und die Web-API, die Sie zuvor mit einer JS-SPA registriert haben, um dieses Szenario zu veranschaulichen. Es zeigt die ordnungsgemäße Behandlung der Anspruchanforderung und das Abrufen eines Zugriffstokens, das für Ihre Web-API verwendet werden kann. Alternativ können Sie sich auch das allgemeine [Angular.js-Codebeispiel](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp) ansehen, das Anweisungen für eine Angular-SPA enthält.

## <a name="see-also"></a>Weitere Informationen

* Weitere Informationen zu den Funktionen finden Sie unter [Bedingter Zugriff in Azure Active Directory](../active-directory-conditional-access-azure-portal.md).
* Weitere Azure AD-Codebeispiele finden Sie im [GitHub-Repository mit Codebeispielen](https://github.com/azure-samples?utf8=%E2%9C%93&q=active-directory).
* Weitere Informationen zum ADAL-SDK und zum Zugriff auf die Referenzdokumentation finden Sie in der [Bibliotheksanleitung](active-directory-authentication-libraries.md).
* Weitere Informationen zu Szenarien mit mehreren Mandanten finden Sie unter [Anmelden von Benutzern mit dem mehrinstanzenfähigen Anwendungsmuster](howto-convert-app-to-be-multi-tenant.md).
