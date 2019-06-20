---
title: Administratoreinwilligung für LinkedIn-Kontoverbindungen – Azure Active Directory | Microsoft-Dokumentation
description: Dieser Artikel erläutert, wie Sie die LinkedIn-Integrationskontoverbindungen in Microsoft-Apps in Azure Active Directory aktivieren oder deaktivieren.
services: active-directory
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 04/29/2019
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1130885cc3168582935264ffaad9fd7a8ba3c60b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64920267"
---
# <a name="integrate-linkedin-account-connections-in-azure-active-directory"></a>Integrieren von LinkedIn-Kontoverbindungen in Azure Active Directory

Sie können Benutzern in Ihrer Organisation den Zugriff auf ihre LinkedIn-Verbindungen in einigen Microsoft-Anwendungen gestatten. Es werden keine Daten freigegeben, bis der Benutzer der Verbindung seiner Konten zustimmt. Sie können Ihre Organisation in das Azure Active Directory (Azure AD) [Admin Center](https://aad.portal.azure.com) integrieren.

> [!IMPORTANT]
> Die Einstellung für LinkedIn-Kontoverbindungen wird derzeit auf Azure AD-Organisationen ausgeweitet. Nachdem die Funktionen für Ihre Organisation zur Verfügung stehen, sind sie standardmäßig aktiviert.
> 
> Ausnahmen:
> * Die Einstellung ist nicht für Kunden mit Microsoft Cloud für die US-Regierung, Microsoft Cloud Deutschland oder Azure und Office 365, betrieben von 21Vianet in China, verfügbar.
> * Die Einstellung ist standardmäßig für Mandanten, die in Deutschland bereitgestellt werden, deaktiviert. Beachten Sie, dass die Einstellung nicht für Kunden mit Microsoft Cloud Deutschland verfügbar ist.
> * Die Einstellung ist standardmäßig für Mandanten, die in Frankreich bereitgestellt werden, deaktiviert.
>
> Nachdem LinkedIn-Kontoverbindungen für Ihre Organisation aktiviert wurden, funktionieren die Kontoverbindungen, nachdem die Benutzer zugestimmt haben, dass Apps in ihrem Auftrag auf Unternehmensdaten zugreifen. Informationen zur Einstellung für die Benutzereinwilligung finden Sie unter [Aufheben des Zugriffs eines Benutzers auf eine Anwendung](https://docs.microsoft.com/azure/active-directory/application-access-assignment-how-to-remove-assignment).

## <a name="enable-linkedin-account-connections-in-the-azure-portal"></a>Aktivieren von LinkedIn-Kontoverbindungen im Azure-Portal

Sie können LinkedIn-Kontoverbindungen nur für die Benutzer aktivieren, auf die Sie Zugriff erhalten möchten – von der gesamten Organisation bis hin zu ausgewählten Benutzern in Ihrer Organisation.

1. Melden Sie sich beim [Azure AD Admin Center](https://aad.portal.azure.com/) mit dem Konto eines globalen Administrators für die Azure AD-Organisation an.
1. Wählen Sie **Benutzer** aus.
1. Wählen Sie auf dem Blatt **Benutzer** die Option **Benutzereinstellungen** aus.
1. Gestatten Sie Benutzern unter **LinkedIn-Kontoverbindungen** das Verbinden ihrer Konten, damit sie in einigen Microsoft-Anwendungen auf ihre LinkedIn-Verbindungen zugreifen können. Es werden keine Daten freigegeben, bis der Benutzer der Verbindung seiner Konten zustimmt.

    * Wählen Sie **Ja** aus, um den Dienst für alle Benutzer in Ihrer Organisation zu aktivieren.
    * Wählen Sie **Ausgewählt** aus, um nur eine Gruppe von ausgewählten Benutzern in Ihrer Organisation zu aktivieren.
    * Wählen Sie **Nein** aus, um die Zustimmung für alle Benutzer in Ihrer Organisation zu widerrufen.

    ![Integrieren von LinkedIn-Kontoverbindungen in der Organisation](./media/linkedin-integration/linkedin-integration.png)

1. Wenn Sie fertig sind, wählen Sie **Speichern** aus, um Ihre Einstellungen zu speichern.

> [!Important]
> Die LinkedIn-Integration wird erst dann vollständig für Ihre Benutzer aktiviert, wenn diese ihre Einwilligung zum Verbinden ihrer Konten gegeben haben. Wenn Sie die Kontoverbindungen für Ihre Benutzer aktivieren, werden keine Daten freigegeben.

### <a name="assign-selected-users-with-a-group"></a>Zuweisen ausgewählter Benutzer mithilfe einer Gruppe
Die Option „Ausgewählt“, die eine Liste von Benutzern enthält, wurde durch die Option zum Auswählen einer Gruppe von Benutzern ersetzt, damit Sie die Möglichkeit haben, LinkedIn- und Microsoft-Konten anstatt für viele einzelne Benutzer für eine einzelne Gruppe zu aktivieren. Wenn Sie für ausgewählte einzelne Benutzer keine LinkedIn-Kontoverbindungen aktiviert haben, müssen Sie nichts weiter tun. Wenn Sie zuvor LinkedIn-Kontoverbindungen für ausgewählte einzelne Benutzer aktiviert haben, sollten Sie folgende Schritte ausführen:

1. Rufen Sie die aktuelle Liste mit den einzelnen Benutzern ab.
1. Verschieben Sie die aktuell aktivierten einzelnen Benutzer in eine Gruppe.
1. Verwenden Sie in der LinkedIn-Kontoverbindungseinstellung im Azure AD-Verwaltungscenter die obige Gruppe als ausgewählte Gruppe.

> [!NOTE]
> Auch wenn Sie die aktuell ausgewählten einzelnen Benutzer nicht in eine Gruppe verschieben, werden diesen weiterhin LinkedIn-Informationen in Microsoft-Apps angezeigt.

### <a name="get-the-current-list-of-selected-users"></a>Abrufen der aktuellen Liste mit den ausgewählten Benutzern

1. Melden Sie sich bei Microsoft 365 mit Ihrem Administratorkonto an.
1. Wechseln Sie zur Adresse https://linkedinselectedusermigration.azurewebsites.net/. Die Liste der Benutzer, die für LinkedIn-Kontoverbindungen ausgewählt wurden, wird angezeigt.
1. Exportieren Sie die Liste in eine CSV-Datei.

### <a name="move-the-currently-selected-individual-users-to-a-group"></a>Verschieben der aktuell ausgewählten einzelnen Benutzer in eine Gruppe

1. Starten von PowerShell
1. Installieren Sie das Azure AD Modul, indem Sie `Install-Module AzureAD` ausführen.
1. Führen Sie das folgende Skript aus:

  ``` PowerShell
  $groupId = "GUID of the target group"
  
  $users = Get-Content 
  Path to the CSV file
  
  $i = 1
  foreach($user in $users} { Add-AzureADGroupMember -ObjectId $groupId -RefObjectId $user ; Write-Host $i Added $user ; $i++ ; Start-Sleep -Milliseconds 10 }
  ```

Unter [Aktivieren von LinkedIn-Kontoverbindungen im Azure-Portal](#enable-linkedin-account-connections-in-the-azure-portal) finden Sie Informationen, wie Sie die Gruppe aus Schritt 2 in der LinkedIn-Kontoverbindungseinstellung im Azure AD-Verwaltungscenter als ausgewählte Gruppe verwenden können.

## <a name="use-group-policy-to-enable-linkedin-account-connections"></a>Verwenden der Gruppenrichtlinie zum Aktivieren von LinkedIn-Kontoverbindungen

1. Laden Sie die [Dateien mit den administrativen Vorlagen für Office 2016 (ADMX/ADML)](https://www.microsoft.com/download/details.aspx?id=49030) herunter.
1. Extrahieren Sie die **ADMX**-Dateien, und kopieren Sie sie in Ihren zentralen Speicher.
1. Öffnen Sie die Gruppenrichtlinienverwaltung.
1. Erstellen Sie ein Gruppenrichtlinienobjekt mit folgender Einstellung: **Benutzerkonfiguration** > **Administrative Vorlagen** > **Microsoft Office 2016** > **Verschiedenes** > **LinkedIn-Integration zulassen**.
1. Wählen Sie **Aktiviert** oder **Deaktiviert** aus.
  
   Zustand | Wirkung
   ------ | ------
   **Aktiviert** | Die Einstellung **LinkedIn Funktionen in Office-Anwendungen anzeigen** ist in den Optionen für Office 2016 aktiviert. Benutzer in Ihrer Organisation können LinkedIn-Features in ihren Office 2016-Anwendungen verwenden.
   **Disabled** | Die Einstellung **LinkedIn Funktionen in Office-Anwendungen anzeigen** ist in den Optionen für Office 2016 deaktiviert, und Endbenutzer können diese Einstellung nicht ändern. Benutzer in Ihrer Organisation können LinkedIn-Features nicht in ihren Office 2016-Anwendungen verwenden.

Diese Gruppenrichtlinie betrifft nur Office 2016-Apps für einen lokalen Computer. Wenn Benutzer LinkedIn in ihren Office 2016-Anwendungen deaktivieren, werden die LinkedIn-Features in Office 365 weiterhin angezeigt.

## <a name="next-steps"></a>Nächste Schritte

* [Benutzereinwilligung und Datenfreigabe für LinkedIn](linkedin-user-consent.md)

* [LinkedIn information and features in your Microsoft apps (LinkedIn-Informationen und -funktionen in Ihren Microsoft-Apps)](https://go.microsoft.com/fwlink/?linkid=850740)

* [LinkedIn-Hilfe](https://www.linkedin.com/help/linkedin)

* [Anzeigen Ihrer aktuellen Einstellung für die LinkedIn-Integration im Azure-Portal](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/UserSettings)
