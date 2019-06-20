---
title: Migrieren zu MSAL.NET | Azure
description: Erfahren Sie mehr über die Unterschiede zwischen der Microsoft-Authentifizierungsbibliothek für .NET (MSAL.NET) und der Azure AD-Authentifizierungsbibliothek für .NET (ADAL.NET) und über die Migration zu MSAL.NET.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f9be13ac22e6eda32668d635032ebcccf417b6c7
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65785217"
---
# <a name="migrating-applications-to-msalnet"></a>Migrieren von Anwendungen zu MSAL.NET

Sowohl die Microsoft-Authentifizierungsbibliothek für .NET (MSAL.NET) als auch die Azure AD-Authentifizierungsbibliothek für .NET (ADAL.NET) werden zum Authentifizieren von Azure AD-Entitäten und zum Anfordern von Token von Azure AD verwendet. Bisher haben die meisten Entwickler die Azure AD für Entwickler-Plattform (v1.0) genutzt, um Azure AD-Identitäten (Geschäfts-, Schul- und Unikonten) zu authentifizieren, indem sie unter Verwendung der Azure AD-Authentifizierungsbibliothek (ADAL) Token anforderten. Ab jetzt können Sie mithilfe von MSAL.NET eine größere Gruppe von Microsoft-Identitäten (Azure AD-Identitäten und Microsoft-Konten sowie Social Media- und lokale Konten über Azure AD B2C) authentifizieren, indem Sie den Microsoft Identity Platform-Endpunkt verwenden. 

Dieser Artikel leistet Hilfestellung bei der Entscheidung zwischen der Microsoft-Authentifizierungsbibliothek für .NET (MSAL.NET) und der Azure AD-Authentifizierungsbibliothek für .NET (ADAL.NET) und bietet eine Gegenüberstellung der beiden Bibliotheken.  

## <a name="differences-between-adal-and-msal-apps"></a>Unterschiede zwischen ADAL- und MSAL-Apps
In den meisten Fällen möchten Sie wahrscheinlich MSAL.NET und den Microsoft Identity Platform-Endpunkt verwenden, da es sich hierbei um die neueste Generation von Microsoft-Authentifizierungsbibliotheken handelt. Mithilfe von MSAL.NET rufen Sie Token für Benutzer ab, die sich mit Azure AD (Geschäfts-, Schul- und Unikonten), (persönlichen) Microsoft-Konten (MSA) oder Azure AD B2C bei Ihrer Anwendung anmelden. 

Wenn Sie bereits mit dem Azure AD für Entwickler (v1.0)-Endpunkt (und ADAL.NET) vertraut sind, könnte Sie der Artikel zu den [Neuerungen des Microsoft Identity Platform (v2.0)-Endpunkts](active-directory-v2-compare.md) interessieren.

Wenn Benutzer in Ihrer Anwendung mit früheren Versionen der [Active Directory-Verbunddienste (AD FS)](/windows-server/identity/active-directory-federation-services) angemeldet werden müssen, benötigen Sie jedoch weiterhin ADAL.NET. Weitere Details finden Sie im Thema zur [AD FS-Unterstützung](https://aka.ms/msal-net-adfs-support).

In der folgenden Abbildung sind einige Unterschiede zwischen ADAL.NET und MSAL.NET in einem ![Codevergleich](./media/msal-compare-msaldotnet-and-adaldotnet/differences.png) gegenübergestellt.

### <a name="nuget-packages-and-namespaces"></a>NuGet-Pakete und Namespaces

ADAL.NET wird vom NuGet-Paket [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) genutzt. Der zu verwendende Namespace lautet `Microsoft.IdentityModel.Clients.ActiveDirectory`.

Zur Verwendung von MSAL.NET müssen Sie das NuGet-Paket [Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client) hinzufügen und den `Microsoft.Identity.Client`-Namespace verwenden.

### <a name="scopes-not-resources"></a>Geltungsbereiche im Gegensatz zu Ressourcen

ADAL.NET ruft Token für *Ressourcen* ab, während MSAL.NET jedoch Token für *Geltungsbereiche* abruft. Verschiedene AcquireToken-Überschreibungen von MSAL.NET erfordern einen Parameter namens scopes(`IEnumerable<string> scopes`). Bei diesem Parameter handelt es sich um eine einfache Liste von Zeichenfolgen, die die gewünschten Berechtigungen und Ressourcen deklarieren, die angefordert werden. Bekannte Geltungsbereiche sind beispielsweise die [Geltungsbereiche des Microsoft Graph](/graph/permissions-reference).

In MSAL.NET kann auch auf v1.0-Ressourcen zugegriffen werden. Ausführliche Informationen finden Sie im Thema zu [Geltungsbereichen für eine v1.0-Anwendung](#scopes-for-a-web-api-accepting-v10-tokens). 

### <a name="core-classes"></a>Core-Klassen

- ADAL.NET nutzt [AuthenticationContext](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AuthenticationContext:-the-connection-to-Azure-AD) zur Darstellung Ihrer Verbindung mit dem Sicherheitstokendienst oder Autorisierungsserver durch eine Autorität. MSAL.NET wurde im Gegensatz dazu mit dem Fokus auf [Clientanwendungen](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Client-Applications) konzipiert und stellt mit `PublicClientApplication` und `ConfidentialClientApplication` zwei separate Klassen bereit.

- Abrufen von Token: ADAL.NET und MSAL.NET verfügen über dieselben Authentifizierungsaufrufe (`AcquireTokenAsync` und `AcquireTokenSilentAsync` für ADAL.NET sowie `AqquireTokenInteractive` und `AcquireTokenSilent` in MSAL.NET). Allerdings sind unterschiedliche Parameter erforderlich. Ein Unterschied besteht darin, dass Sie in MSAL.NET nicht mehr die `ClientID` Ihrer Anwendung in jedem AcquireTokenXX-Aufruf übergeben müssen. Tatsächlich wird die `ClientID` lediglich einmal beim Erstellen von `IPublicClientApplication` oder `IConfidentialClientApplication` festgelegt.

### <a name="iaccount-not-iuser"></a>IAccount im Gegensatz zu IUser

In ADAL.NET wurden Benutzer bearbeitet. Ein Benutzer ist jedoch eine Person oder ein Software-Agent, kann aber eines oder mehrere Konten im Microsoft-Identitätssystem (mehrere Azure AD-Konten, Azure AD B2C, persönliche Microsoft-Konten) besitzen bzw. dafür verantwortlich sein. 

In MSAL.NET 2.x wird jetzt (durch die IAccount-Schnittstelle) das Konzept des Kontos definiert. Diese wichtige Änderung sorgt für die richtige Semantik, da jetzt derselbe Benutzer über mehrere Konten in verschiedenen Azure AD-Verzeichnissen verfügen kann. Zudem bietet MSAL.NET bessere Informationen in Gastszenarien, da Informationen zum Stammkonto bereitgestellt werden.

Weitere Informationen zu den Unterschieden zwischen IUser und IAccount finden Sie unter [MSAL.NET 2.x](https://aka.ms/msal-net-2-released).

### <a name="exceptions"></a>Ausnahmen

#### <a name="interaction-required-exceptions"></a>Ausnahmen, die eine Interaktion erfordern

MSAL.NET weist mehr explizite Ausnahmen auf. Beispiel: Wenn die automatische Authentifizierung in ADAL fehlschlägt, werden in der Regel die Ausnahme erfasst und der Fehlercode `user_interaction_required` überprüft:

```csharp
catch(AdalException exception)
{
 if (exception.ErrorCode == “user_interaction_required”)
 {
  try
  {“try to authenticate interactively”}}
 }
}
```

Ausführliche Informationen finden Sie unter dem [Empfohlenen Muster zum Abrufen eines Tokens](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AcquireTokenSilentAsync-using-a-cached-token#recommended-pattern-to-acquire-a-token) für ADAL.NET.

In MSAL.NET erfassen Sie `MsalUiRequiredException`, wie in [AcquireTokenSilent](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AcquireTokenSilentAsync-using-a-cached-token) beschrieben.

```csharp
catch(MsalUiRequiredException exception)
{
 try {“try to authenticate interactively”}
}
```

#### <a name="handling-claim-challenge-exceptions"></a>Behandeln von Ausnahmen bei Anspruchsaufforderungen

In ADAL.NET werden Ausnahmen bei Anspruchsaufforderungen auf folgende Weise behandelt:

- `AdalClaimChallengeException` ist eine (von `AdalServiceException` abgeleitete) Ausnahme, die vom Dienst ausgelöst wird, falls eine Ressource zusätzliche Anspruchsdaten vom Benutzer benötigt (z. B. zweistufige Authentifizierung). Das `Claims`-Element enthält ein JSON-Fragment mit den erwarteten Ansprüchen.
- In ADAL.NET muss die öffentliche Clientanwendung, die diese Ausnahme empfängt, immer noch die `AcquireTokenInteractive`-Überschreibung aufrufen, die über einen Claims-Parameter verfügt. Diese Überschreibung von `AcquireTokenInteractive` versucht nicht einmal, einen Treffer im Cache zu erzielen, da es nicht erforderlich ist. Dies liegt daran, dass das Token im Cache nicht die richtigen Ansprüche aufweist (da andernfalls keine `AdalClaimChallengeException` ausgelöst worden wäre). Daher besteht keine Notwendigkeit, den Cache abzufragen. Beachten Sie, dass `ClaimChallengeException` in einer Web-API per OBO empfangen werden kann, während `AcquireTokenInteractive` in einer öffentlichen Clientanwendung aufgerufen werden muss, die diese Web-API aufruft.
- Ausführliche Informationen und Beispiele finden Sie unter der Behandlung von [AdalClaimChallengeException](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Exceptions-in-ADAL.NET#handling-adalclaimchallengeexception).

In MSAL.NET werden Ausnahmen bei Anspruchsaufforderungen auf folgende Weise behandelt:

- `Claims` werden in `MsalServiceException` ausgegeben.
- Es gibt eine `.WithClaim(claims)`-Methode, die auf den `AcquireTokenInteractive`-Builder angewendet werden kann. 

### <a name="supported-grants"></a>Unterstützte Gewährungstypen

In MSAL.NET und dem v2.0-Endpunkt werden noch nicht alle Gewährungstypen unterstützt. In der folgenden Zusammenfassung werden die unterstützten Gewährungstypen von ADAL.NET und MSAL.NET verglichen.

#### <a name="public-client-applications"></a>Öffentliche Clientanwendungen

Die folgenden Gewährungstypen werden in ADAL.NET und MSAL.NET für Desktop- und mobile Anwendungen unterstützt:

Gewährung | ADAL.NET | MSAL.NET
----- |----- | -----
Interactive | [Interaktive Authentifizierung](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-interactively---Public-client-application-flows) | [Interaktiver Abruf von Token in MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Acquiring-tokens-interactively)
Integrierte Windows-Authentifizierung | [Integrierte Authentifizierung in Windows (Kerberos)](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AcquireTokenSilentAsync-using-Integrated-authentication-on-Windows-(Kerberos)) | [Integrierte Windows-Authentifizierung](msal-authentication-flows.md#integrated-windows-authentication)
Benutzername und Kennwort | [Abruf von Token mit Benutzername und Kennwort](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-with-username-and-password)| [Authentifizierung mit Benutzername und Kennwort](msal-authentication-flows.md#usernamepassword)
Gerätecodefluss | [Geräteprofil für Geräte ohne Webbrowser](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Device-profile-for-devices-without-web-browsers) | [Gerätecodeflow](msal-authentication-flows.md#device-code)

#### <a name="confidential-client-applications"></a>Vertrauliche Clientanwendungen

Die folgenden Gewährungstypen werden in ADAL.NET und MSAL.NET für Webanwendungen, Web-APIs und Daemonanwendungen unterstützt:

Anwendungstyp | Gewährung | ADAL.NET | MSAL.NET
----- | ----- | ----- | -----
Web-App, Web-API, Daemon | Clientanmeldeinformationen | [Flow von Clientanmeldeinformationen in ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Client-credential-flows) | [Flow von Clientanmeldeinformationen in MSAL.NET](msal-authentication-flows.md#client-credentials)
Web-API | OBO (Im Namen von) | [Dienst-zu-Dienst-Aufrufe im Namen des Benutzers mit ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Service-to-service-calls-on-behalf-of-the-user) | [OBO in MSAL.NET](msal-authentication-flows.md#on-behalf-of)
Web App | Authentifizierungscode | [Abrufen von Token mit Autorisierungscodes für Web-Apps mit ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-with-authorization-codes-on-web-apps) | [Abrufen von Token mit Autorisierungscodes für Web-Apps mit MSAL.NET](msal-authentication-flows.md#authorization-code)

### <a name="cache-persistence"></a>Cachepersistenz

ADAL.NET unterstützt die Erweiterung der `TokenCache`-Klasse. Auf diese Weise können die gewünschten Persistenzfunktionen auf Plattformen ohne sicheren Speicher (.NET Framework und .NET Core) mithilfe der `BeforeAccess`-Methode und `BeforeWrite`-Methode implementiert werden. Ausführliche Informationen finden Sie im Thema zur [Tokencacheserialisierung in ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization).

In MSAL.NET stellt der Tokencache eine versiegelte Klasse dar und kann daher nicht erweitert werden. Aus diesem Grund muss die Tokencachepersistenz in Form einer Hilfsklasse implementiert werden, die mit dem versiegelten Tokencache interagiert. Diese Interaktion wird im Thema zur [Tokencacheserialisierung in MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/token-cache-serialization) beschrieben.

## <a name="signification-of-the-common-authority"></a>Bedeutung der allgemeinen Autorität

Wenn Sie in v1.0 die allgemeine Autorität (https://login.microsoftonline.com/common) verwenden, können sich Benutzer (für jede Organisation) mit einem beliebigen AAD-Konto anmelden. Siehe das Thema zur [Autoritätsüberprüfung in ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AuthenticationContext:-the-connection-to-Azure-AD#authority-validation).

Bei Verwendung dieser Autorität (https://login.microsoftonline.com/common) in v2.0 geben Sie Benutzern die Möglichkeit, sich mit einer beliebigen AAD-Organisation oder einem persönlichen Microsoft-Konto (MSA) anzumelden. Wenn Sie in MSAL.NET Anmeldungen auf ein beliebiges AAD-Konto beschränken möchten (dasselbe Verhalten wie in ADAL.NET), müssen Sie https://login.microsoftonline.com/organizations verwenden. Ausführliche Informationen finden Sie unter dem `authority`-Parameter in [öffentlichen Clientanwendungen](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Client-Applications#publicclientapplication).

## <a name="v10-and-v20-tokens"></a>v1.0- und v2.0-Token

Es gibt zwei Tokenversionen:
- v1.0-Token
- v2.0-Token 

Der (von ADAL verwendete) v1.0-Endpunkt gibt nur v1.0-Token aus.

Der von (MSAL verwendete) v2.0-Endpunkt gibt jedoch die Tokenversion aus, die von der Web-API akzeptiert wird. Mithilfe einer Eigenschaft im Anwendungsmanifest der Web-API können Entwickler auswählen, welche Tokenversion akzeptiert wird. Informationen finden Sie unter `accessTokenAcceptedVersion` in der Referenzdokumentation zum [Anwendungsmanifest](reference-app-manifest.md).

Weitere Informationen zu v1.0- und v2.0-Token finden Sie im Thema zu [Azure Active Directory-Zugriffstoken](access-tokens.md).

## <a name="scopes-for-a-web-api-accepting-v10-tokens"></a>Geltungsbereiche für eine Web-API, die v1.0-Token akzeptiert

OAuth2-Berechtigungen sind Berechtigungsbereiche, die eine v1.0-Web-API-Anwendung (Ressource) für Clientanwendungen verfügbar macht. Diese Berechtigungsbereiche können Clientanwendungen im Zuge der Zustimmung gewährt werden. Informationen finden Sie im Abschnitt zu oauth2Permissions im [Azure Active Directory-Anwendungsmanifest](active-directory-application-manifest.md).

### <a name="scopes-to-request-access-to-specific-oauth2-permissions-of-a-v10-application"></a>Geltungsbereiche, mit denen der Zugriff auf bestimmte OAuth2-Berechtigungen einer v1.0-Anwendung angefordert wird

Wenn Sie Token für bestimmte Geltungsbereiche einer v1.0-Anwendung abrufen möchten (z. B. den AAD-Graph unter https://graph.windows.net), müssten Sie `scopes` erstellen, indem Sie einen gewünschten Ressourcenbezeichner mit einer gewünschten OAuth2-Berechtigung für die jeweilige Ressource verketten.

Beispiel: Um im Namen des Benutzers auf eine v1.0-Web-API zuzugreifen, deren App-ID-URI `ResourceId` lautet, verwenden Sie folgenden Code:

```csharp
var scopes = new [] {  ResourceId+"/user_impersonation"};
```

Wenn Sie mit Azure Active Directory MSAL.NET unter Verwendung der AAD-Graph-API (https://graph.windows.net/) Lese- und Schreibvorgänge ausführen möchten, erstellen Sie eine Liste von Geltungsbereichen wie im folgenden Codeausschnitt:

```csharp
ResourceId = "https://graph.windows.net/";
var scopes = new [] { ResourceId + “Directory.Read”, ResourceID + “Directory.Write”}
```

#### <a name="warning-should-you-have-one-or-two-slashes-in-the-scope-corresponding-to-a-v10-web-api"></a>Warnung: Verwendung von ein oder zwei Schrägstrichen im Geltungsbereich für eine v1.0-Web-API

Um den Geltungsbereich für die Azure Resource Manager-API (https://management.core.windows.net/) zu schreiben, müssen Sie den folgenden Geltungsbereich anfordern. (Beachten Sie die beiden Schrägstriche.) 

```csharp
var scopes = new[] {"https://management.core.windows.net//user_impersonation"};
var result = await app.AcquireTokenInteractive(scopes).ExecuteAsync();

// then call the API: https://management.azure.com/subscriptions?api-version=2016-09-01
```

Dies liegt daran, dass die Resource Manager-API einen Schrägstrich im `aud`-Anspruch erwartet und dass ein weiterer Schrägstrich erforderlich ist, um den API-Namen vom Geltungsbereich zu trennen.

Die von Azure AD verwendete Logik lautet wie folgt:
- Für einen ADAL (v1.0)-Endpunkt mit einem v1.0-Zugriffstoken (einzige Möglichkeit): aud=resource
- Für einen MSAL (v2.0)-Endpunkt, der ein Zugriffstoken für eine Ressource abfragt, die v2.0-Token akzeptiert: aud=resource.AppId
- Für einen MSAL (v2.0)-Endpunkt, der ein Zugriffstoken für eine Ressource abfragt, die ein v1.0-Zugriffstoken akzeptiert (wie im Fall oben), analysiert Azure AD die gewünschte Zielgruppe aus dem angeforderten Geltungsbereich, indem alles vor dem letzten Schrägstrich als Ressourcenbezeichner interpretiert wird. Wenn „https:\//database.windows.net“ die Zielgruppe „https://database.windows.net/“ erwartet, müssen Sie daher den Geltungsbereich „https:\//database.windows.net//.default“ anfordern. Siehe auch Problem [747](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747): Resource url's trailing slash is omitted, which caused sql auth failure #747 (Bei der URL der Ressource wurde der nachgestellte Schrägstrich entfernt, wodurch bei der SQL-Authentifizierung Fehler 747 aufgetreten ist.)


### <a name="scopes-to-request-access-to-all-the-permissions-of-a-v10-application"></a>Geltungsbereiche, mit denen der Zugriff auf alle Berechtigungen einer v1.0-Anwendung angefordert wird

Zum Abrufen eines Tokens für alle statischen Geltungsbereiche einer v1.0-Anwendung verwenden Sie beispielsweise folgenden Code:

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

### <a name="scopes-to-request-in-the-case-of-client-credential-flow--daemon-app"></a>Geltungsbereiche, die im Fall eines Clientanmeldeinformations-Flows/einer Daemon-App angefordert werden

Bei einem Clientanmeldeinformations-Flow lautet der zu übergebende Geltungsbereich ebenfalls `/.default`. Dies bedeutet für Azure AD: Alle Berechtigungen auf Anwendungsebene, denen der Administrator bei der Anwendungsregistrierung zugestimmt hat.

## <a name="adal-to-msal-migration"></a>Migration von ADAL zu MSAL

In ADAL.NET v2.X wurden die Aktualisierungstoken offengelegt, sodass Sie Lösungen rund um den Tokeneinsatz entwickeln konnten. Dazu wurden die Token zwischengespeichert und die von ADAL 2.x bereitgestellten `AcquireTokenByRefreshToken`-Methoden verwendet. Einige Lösungen wurden beispielsweise in folgenden Szenarien verwendet:
* Zeitintensive Dienste, die verschiedene Aktionen für Benutzer ausführen – beispielsweise Dashboards aktualisieren – während diese Benutzer nicht mehr verbunden sind 
* WebFarm-Szenarien, die es dem Client ermöglichen, das RT dem Webdienst zur Verfügung zu stellen (die Zwischenspeicherung erfolgt auf Client- und nicht auf Serverseite, verschlüsseltes Cookie)

Auf MSAL.NET trifft dies jedoch nicht zu, weil wir aus Sicherheitsgründen mittlerweile davon abraten, das Aktualisierungstoken auf diese Weise zu verwenden. Dadurch würde die Migration zu MSAL 3.x erschwert, weil die API keine Möglichkeit bietet, zuvor abgerufene Aktualisierungstoken zu übergeben. 

Praktischerweise verfügt MSAL.NET nun über eine API, die die Migration früherer Aktualisierungstoken in `IConfidentialClientApplication` unterstützt. 

```CSharp
/// <summary>
/// Acquires an access token from an existing refresh token and stores it and the refresh token into 
/// the application user token cache, where it will be available for further AcquireTokenSilent calls.
/// This method can be used in migration to MSAL from ADAL v2 and in various integration 
/// scenarios where you have a RefreshToken available. 
/// (see https://aka.ms/msal-net-migration-adal2-msal2)
/// </summary>
/// <param name="scopes">Scope to request from the token endpoint. 
/// Setting this to null or empty will request an access token, refresh token and ID token with default scopes</param>
/// <param name="refreshToken">The refresh token from ADAL 2.x</param>
IByRefreshToken.AcquireTokenByRefreshToken(IEnumerable<string> scopes, string refreshToken);
```
 
Dank dieser Methode können Sie das zuvor verwendete Aktualisierungstoken zusammen mit beliebigen Geltungsbereichen (Ressourcen) bereitstellen. Das Aktualisierungstoken wird durch ein neues ersetzt und in Ihrer Anwendung zwischengespeichert.  

Diese Methode ist nicht für typische Szenarien vorgesehen und daher nicht direkt mit `IConfidentialClientApplication` zugänglich. Vielmehr ist zuerst eine Umwandlung in `IByRefreshToken` erforderlich.

Dieser Codeausschnitt enthält Migrationscode in einer vertraulichen Clientanwendung. `GetCachedRefreshTokenForSignedInUser` ruft das Aktualisierungstoken ab. Dieses wurde von einer früheren Version der Anwendung, die zuvor ADAL 2.x genutzt hat, in einem Speicher vorgehalten. `GetTokenCacheForSignedInUser` deserialisiert einen Cache für den angemeldeten Benutzer (da vertrauliche Clientanwendungen einen Cache pro Benutzer aufweisen sollten).

```csharp
TokenCache userCache = GetTokenCacheForSignedInUser();
string rt = GetCachedRefreshTokenForSignedInUser();

IConfidentialClientApplication app;
app = ConfidentialClientApplicationBuilder.Create(clientId)
 .WithAuthority(Authority)
 .WithRedirectUri(RedirectUri)
 .WithClientSecret(ClientSecret)
 .Build();
IByRefreshToken appRt = app as IByRefreshToken;
         
AuthenticationResult result = await appRt.AcquireTokenByRefreshToken(null, rt)
                                         .ExecuteAsync()
                                         .ConfigureAwait(false);
```

In AuthenticationResult werden ein Zugriffstoken und ein ID-Token zurückgegeben, während Ihr neues Aktualisierungstoken im Cache gespeichert wird.

Sie können diese Methode auch für verschiedene Integrationsszenarien verwenden, in denen ein Aktualisierungstoken verfügbar ist.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu den Geltungsbereichen finden Sie im Thema zu [Geltungsbereichen, Berechtigungen und Zustimmung im Microsoft Identity Platform-Endpunkt](v2-permissions-and-consent.md).