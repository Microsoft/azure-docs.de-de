---
title: Genehmigte Client-Apps mit bedingtem Zugriff – Azure Active Directory
description: Erfahren Sie, wie Sie die Verwendung genehmigter Client-Apps für den Zugriff auf Cloud-Apps mithilfe des bedingten Zugriffs von Azure Active Directory vorschreiben können.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 03/04/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol, rosssmi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 122cc6a2be17cb35e77b638a60fc5fa4f035c0d4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91266138"
---
# <a name="how-to-require-approved-client-apps-for-cloud-app-access-with-conditional-access"></a>Gewusst wie: Vorschreiben der Verwendung von genehmigten Client-Apps für den Zugriff auf Cloud-Apps mithilfe des bedingten Zugriffs

Viele Menschen verwenden mobile Geräte sowohl für private als auch für berufliche Zwecke. Organisationen möchten natürlich sicherstellen, dass die Mitarbeiter produktiv arbeiten können, aber gleichzeitig auch verhindern, dass Datenverluste durch potenziell unsichere Anwendungen vermieden werden. Mit bedingtem Zugriff können Organisationen den Zugriff auf genehmigte (modern authentifizierungsfähige) Client-Apps einschränken.

Dieser Artikel enthält zwei Szenarien zum Konfigurieren von Richtlinien für den bedingten Zugriff für Ressourcen wie Microsoft 365, Exchange Online und SharePoint Online.

- [Szenario 1: Microsoft 365-Apps erfordern eine genehmigte Client-App](#scenario-1-microsoft-365-apps-require-an-approved-client-app)
- [Szenario 2: Exchange Online und SharePoint Online erfordern eine genehmigte Client-App.](#scenario-2-exchange-online-and-sharepoint-online-require-an-approved-client-app)

Bei bedingtem Zugriff wird diese Funktion über eine genehmigte Client-App erzwungen. Eine Liste der genehmigten Client-Apps finden Sie unter [Genehmigte Client-App als Voraussetzung](concept-conditional-access-grant.md#require-approved-client-app).

> [!NOTE]
> Um genehmigte Client-Apps für iOS- und Android-Geräte anzufordern, müssen sich diese Geräte zunächst bei Azure AD registrieren.

## <a name="scenario-1-microsoft-365-apps-require-an-approved-client-app"></a>Szenario 1: Microsoft 365-Apps erfordern eine genehmigte Client-App

In diesem Szenario hat Contoso beschlossen, dass Benutzer, die mobile Geräte verwenden, auf alle Microsoft 365-Dienste zugreifen können, sofern sie genehmigte Client-Apps wie Outlook Mobile, OneDrive und Microsoft Teams verwenden. Alle Benutzer melden sich bereits mit Azure AD-Anmeldeinformationen an. Außerdem wurden ihnen Lizenzen zugewiesen, die Azure AD Premium P1 oder P2 und Microsoft Intune umfassen.

Organisationen müssen die folgenden drei Schritte ausführen, um die Verwendung einer genehmigten Client-App auf mobilen Geräten zu erzwingen.

**Schritt 1: Richtlinie für auf Android und iOS basierende moderne Authentifizierungsclients, die die Verwendung einer genehmigten Clientanwendung für den Zugriff auf Exchange Online erfordern.**

1. Melden Sie sich beim **Azure-Portal** als globaler Administrator, Sicherheitsadministrator oder Administrator für bedingten Zugriff an.
1. Navigieren Sie zu **Azure Active Directory** > **Sicherheit** > **Bedingter Zugriff**.
1. Wählen Sie **Neue Richtlinie**.
1. Benennen Sie Ihre Richtlinie. Es wird empfohlen, dass Unternehmen einen aussagekräftigen Standard für die Namen ihrer Richtlinien erstellen.
1. Wählen Sie unter **Zuweisungen** die Option **Benutzer und Gruppen** aus.
   1. Wählen Sie unter **Einschließen** die Option **Alle Benutzer** oder bestimmte **Benutzer und Gruppen** aus, auf die Sie diese Richtlinie anwenden möchten. 
   1. Wählen Sie **Fertig** aus.
1. Wählen Sie unter **Cloud-Apps oder -aktionen** > **Einschließen** die Option **Office 365** aus.
1. Wählen Sie unter **Bedingungen** die Option **Geräteplattformen** aus.
   1. Legen Sie **Konfigurieren** auf **Ja** fest.
   1. Schließen Sie **Android** und **iOS** ein.
1. Wählen Sie unter **Bedingungen** die Option **Client-Apps (Vorschau)** aus.
   1. Legen Sie **Konfigurieren** auf **Ja** fest.
   1. Wählen Sie **Mobile Apps und Desktopclients** und **Clients mit moderner Authentifizierung** aus.
1. Wählen Sie unter **Zugriffssteuerung** > **Gewähren** die Option **Zugriff gewähren**, dann **Genehmigte Client-App erforderlich** und anschließend **Auswählen** aus.
1. Bestätigen Sie die Einstellungen und legen Sie **Richtlinie aktivieren** auf **Ein** fest.
1. Wählen Sie **Erstellen** aus, um die Richtlinie zu erstellen und zu aktivieren.

**Schritt 2: Konfigurieren einer Azure AD-Richtlinie für bedingten Zugriff für Exchange Online mit ActiveSync (EAS)**

1. Navigieren Sie zu **Azure Active Directory** > **Sicherheit** > **Bedingter Zugriff**.
1. Wählen Sie **Neue Richtlinie**.
1. Benennen Sie Ihre Richtlinie. Es wird empfohlen, dass Unternehmen einen aussagekräftigen Standard für die Namen ihrer Richtlinien erstellen.
1. Wählen Sie unter **Zuweisungen** die Option **Benutzer und Gruppen** aus.
   1. Wählen Sie unter **Einschließen** die Option **Alle Benutzer** oder bestimmte **Benutzer und Gruppen** aus, auf die Sie diese Richtlinie anwenden möchten. 
   1. Wählen Sie **Fertig** aus.
1. Wählen Sie unter **Cloud-Apps oder -aktionen** > **Einschließen** die Option **Office 365 Exchange Online** aus.
1. Unter **Bedingungen**:
   1. **Client-Apps (Vorschau):**
      1. Legen Sie **Konfigurieren** auf **Ja** fest.
      1. Wählen Sie **Mobile Apps und Desktopclients** und **Exchange ActiveSync-Clients** aus.
1. Wählen Sie unter **Zugriffssteuerung** > **Gewähren** die Option **Zugriff gewähren**, dann **Genehmigte Client-App erforderlich** und anschließend **Auswählen** aus.
1. Bestätigen Sie die Einstellungen und legen Sie **Richtlinie aktivieren** auf **Ein** fest.
1. Wählen Sie **Erstellen** aus, um die Richtlinie zu erstellen und zu aktivieren.

**Schritt 3: Konfigurieren der Intune-App-Schutzrichtlinie für iOS- und Android-Clientanwendungen**

Die Schritte zum Erstellen von App-Schutzrichtlinien für Android und iOS finden Sie im Artikel [Erstellen und Zuweisen von App-Schutzrichtlinien](/intune/apps/app-protection-policies). 

## <a name="scenario-2-exchange-online-and-sharepoint-online-require-an-approved-client-app"></a>Szenario 2: Exchange Online und SharePoint Online erfordern eine genehmigte Client-App.

In diesem Szenario hat Contoso beschlossen, dass Benutzer auf mobilen Geräten nur auf E-Mail- und SharePoint-Daten zugreifen dürfen, sofern sie eine genehmigte Client-App wie Outlook Mobile verwenden. Alle Benutzer melden sich bereits mit Azure AD-Anmeldeinformationen an. Außerdem wurden ihnen Lizenzen zugewiesen, die Azure AD Premium P1 oder P2 und Microsoft Intune umfassen.

Organisationen müssen die folgenden drei Schritte ausführen, um die Verwendung einer genehmigten Client-App auf mobilen Geräten und von Exchange ActiveSync-Clients zu erzwingen.

**Schritt 1: Richtlinie für auf Android und iOS basierende moderne Authentifizierungsclients, die die Verwendung einer genehmigten Clientanwendung für den Zugriff auf Exchange Online und SharePoint Online erfordern.**

1. Melden Sie sich beim **Azure-Portal** als globaler Administrator, Sicherheitsadministrator oder Administrator für bedingten Zugriff an.
1. Navigieren Sie zu **Azure Active Directory** > **Sicherheit** > **Bedingter Zugriff**.
1. Wählen Sie **Neue Richtlinie**.
1. Benennen Sie Ihre Richtlinie. Es wird empfohlen, dass Unternehmen einen aussagekräftigen Standard für die Namen ihrer Richtlinien erstellen.
1. Wählen Sie unter **Zuweisungen** die Option **Benutzer und Gruppen** aus.
   1. Wählen Sie unter **Einschließen** die Option **Alle Benutzer** oder bestimmte **Benutzer und Gruppen** aus, auf die Sie diese Richtlinie anwenden möchten. 
   1. Wählen Sie **Fertig** aus.
1. Wählen Sie unter **Cloud-Apps oder -aktionen** > **Einschließen** die Optionen **Office 365 Exchange Online** und **Office 365 SharePoint Online** aus.
1. Wählen Sie unter **Bedingungen** die Option **Geräteplattformen** aus.
   1. Legen Sie **Konfigurieren** auf **Ja** fest.
   1. Schließen Sie **Android** und **iOS** ein.
1. Wählen Sie unter **Bedingungen** die Option **Client-Apps (Vorschau)** aus.
   1. Legen Sie **Konfigurieren** auf **Ja** fest.
   1. Wählen Sie **Mobile Apps und Desktopclients** und **Clients mit moderner Authentifizierung** aus.
1. Wählen Sie unter **Zugriffssteuerung** > **Gewähren** die Option **Zugriff gewähren**, dann **Genehmigte Client-App erforderlich** und anschließend **Auswählen** aus.
1. Bestätigen Sie die Einstellungen und legen Sie **Richtlinie aktivieren** auf **Ein** fest.
1. Wählen Sie **Erstellen** aus, um die Richtlinie zu erstellen und zu aktivieren.

**Schritt 2: Richtlinie für Exchange ActiveSync-Clients, die die Verwendung einer genehmigten Client-App erfordern.**

1. Navigieren Sie zu **Azure Active Directory** > **Sicherheit** > **Bedingter Zugriff**.
1. Wählen Sie **Neue Richtlinie**.
1. Benennen Sie Ihre Richtlinie. Es wird empfohlen, dass Unternehmen einen aussagekräftigen Standard für die Namen ihrer Richtlinien erstellen.
1. Wählen Sie unter **Zuweisungen** die Option **Benutzer und Gruppen** aus.
   1. Wählen Sie unter **Einschließen** die Option **Alle Benutzer** oder bestimmte **Benutzer und Gruppen** aus, auf die Sie diese Richtlinie anwenden möchten. 
   1. Wählen Sie **Fertig** aus.
1. Wählen Sie unter **Cloud-Apps oder -aktionen** > **Einschließen** die Option **Office 365 Exchange Online** aus.
1. Unter **Bedingungen**:
   1. **Client-Apps (Vorschau):**
      1. Legen Sie **Konfigurieren** auf **Ja** fest.
      1. Wählen Sie **Mobile Apps und Desktopclients** und **Exchange ActiveSync-Clients** aus.
1. Wählen Sie unter **Zugriffssteuerung** > **Gewähren** die Option **Zugriff gewähren**, dann **Genehmigte Client-App erforderlich** und anschließend **Auswählen** aus.
1. Bestätigen Sie die Einstellungen und legen Sie **Richtlinie aktivieren** auf **Ein** fest.
1. Wählen Sie **Erstellen** aus, um die Richtlinie zu erstellen und zu aktivieren.

**Schritt 3: Konfigurieren der Intune-App-Schutzrichtlinie für iOS- und Android-Clientanwendungen**

Die Schritte zum Erstellen von App-Schutzrichtlinien für Android und iOS finden Sie im Artikel [Erstellen und Zuweisen von App-Schutzrichtlinien](/intune/apps/app-protection-policies). 

## <a name="next-steps"></a>Nächste Schritte

[Was ist bedingter Zugriff?](overview.md)

[Bedingter Zugriff auf Komponenten](concept-conditional-access-policies.md)

[Allgemeine Richtlinien für bedingten Zugriff](concept-conditional-access-policy-common.md)
