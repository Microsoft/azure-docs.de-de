---
title: Azure Multi-Factor Authentication – Erfassen von Benutzerdaten – Azure Active Directory
description: Welche Informationen verwendet Azure Multi-Factor Authentication, um Benutzer zu authentifizieren?
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: e2b8d68cc348ce8e157c7d58424eaebb06940335
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60359040"
---
# <a name="azure-multi-factor-authentication-user-data-collection"></a>Azure Multi-Factor Authentication – Erfassen von Benutzerdaten

Dieses Dokument erläutert, wie Sie Benutzerdaten finden, die vom Azure Multi-Factor Authentication-Server (MFA-Server) und von Azure MFA (cloudbasiert) erfasst wurden, falls Sie diese entfernen möchten.

[!INCLUDE [gdpr-hybrid-note](../../../includes/gdpr-hybrid-note.md)]

## <a name="information-collected"></a>Erfasste Informationen

Der MFA-Server, die NPS-Erweiterung und der Windows Server 2016 Azure MFA AD FS-Adapter erfassen und speichern die folgenden Informationen 90 Tage lang.

Authentifizierungsversuche (für Berichterstellung und Problembehandlung):

- Timestamp
- Username
- First Name (Vorname)
- Last Name (Nachname)
- E-Mail-Adresse
- Benutzergruppe
- Authentifizierungsmethode (Telefonanruf, SMS, mobile App, OATH-Token)
- Telefonanrufmodus (Standard, PIN)
- SMS-Anweisung (unidirektional, bidirektional)
- SMS-Modus (OTP, OTP + PIN)
- Mobiler Appmodus (Standard, PIN)
- OATH-Tokenmodus (Standard, PIN)
- Authentifizierungstyp
- Anwendungsname
- Landesvorwahl der Hauptrufnummer
- Hauptrufnummer
- Durchwahl der Hauptrufnummer
- Hauptanruf authentifiziert
- Ergebnis des Hauptanrufs
- Landesvorwahl der Ersatzrufnummer
- Ersatzrufnummer
- Durchwahl der Ersatzrufnummer
- Ersatzanruf authentifiziert
- Ergebnis des Ersatzanrufs
- Authentifiziert (insgesamt)
- Ergebnis (insgesamt)
- Ergebnisse
- Authentifiziert
- Ergebnis
- Initialisierungs-IP-Adresse
- Geräte
- Gerätetoken
- Gerätetyp
- Version der mobilen App
- Betriebssystemversion
- Ergebnis
- Verwendete Benachrichtigungsüberprüfung

Aktivierungen (Versuch, ein Konto in der mobilen Microsoft Authenticator-App zu aktivieren):
- Username
- Kontoname
- Timestamp
- Abrufen des Aktivierungcodeergebnisses
- Aktivierung erfolgreich
- Fehler beim Aktivieren
- Ergebnis des Aktivierungsstatus
- Gerätename
- Gerätetyp
- Appversion
- OATH-Token aktiviert

Blockierungen (für gesperrten Zustand und Berichterstellung):

- Zeitstempel der Blockierung
- Blockierung nach Benutzernamen
- Username
- Landesvorwahl
- Rufnummer
- Formatierte Rufnummer
- Durchwahl
- Durchwahl bereinigen
- Blockiert
- Grund der Blockierung
- Zeitstempel des Abschlusses
- Grund des Abschlusses
- Kontosperrung
- Betrugswarnung
- Betrugswarnung nicht blockiert
- Sprache

Umgehungen (für Berichterstellung)

- Zeitstempel der Umgehung
- Umgehung in Sekunden
- Umgehung nach Benutzernamen
- Username
- Landesvorwahl
- Rufnummer
- Formatierte Rufnummer
- Durchwahl
- Durchwahl bereinigen
- Grund der Umgehung
- Zeitstempel des Abschlusses
- Grund des Abschlusses
- Verwendete Umgehung

Änderungen (zum Synchronisieren der Benutzerdaten mit MFA-Server oder AAD):

- Zeitstempel der Änderung
- Username
- Neue Landesvorwahl
- Neue Rufnummer
- Neue Durchwahl
- Neue Landesvorwahl der Ersatzrufnummer
- Neue Ersatzrufnummer
- Neue Durchwahl der Ersatzrufnummer
- Neue PIN
- PIN-Änderung erforderlich
- Altes Gerätetoken
- Neues Gerätetoken

## <a name="gather-data-from-mfa-server"></a>Erfassen von Daten vom MFA-Server

Administratoren können mithilfe der folgenden Anleitung alle Benutzerdaten vom MFA-Server exportieren (Version 8.0 oder höher):

- Melden Sie sich in Ihrem MFA-Server an, navigieren Sie zur Registerkarte **Benutzer**, wählen Sie den gewünschten Benutzer aus, und klicken Sie auf die Schaltfläche **Bearbeiten**. Erstellen Sie Screenshots (Alt+Druck) von jeder Registerkarte, um Benutzern ihre aktuellen MFA-Einstellungen bereitzustellen.
- Führen Sie über die Befehlszeile des MFA-Servers den folgenden Befehl aus. Ändern Sie den Pfad gemäß Ihrer Installation `C:\Program Files\Multi-Factor Authentication Server\MultiFactorAuthGdpr.exe export <username>`, um eine Datei im JSON-Format zu erstellen.
- Administratoren können auch den GetUserGdpr-Vorgang des Webdienst-SDK verwenden, um alle erfassten MFA-Clouddienstinformationen für einen bestimmten Benutzer zu exportieren oder in eine größere Berichtslösung zu integrieren.
- Suchen Sie nach `C:\Program Files\Multi-Factor Authentication Server\Logs\MultiFactorAuthSvc.log` und allen Sicherungen für „\<Benutzername>“ (Anführungszeichen in die Suche einschließen), um alle Instanzen des Benutzerdatensatzes zu finden, die hinzugefügt oder geändert wurden.
   - Diese Datensätze können begrenzt (jedoch nicht entfernt) werden, indem Sie **„Benutzeränderungen protokollieren“** deaktivieren in der MFA-Server-UX, im Abschnitt „Protokollierung“, auf der Registerkarte „Protokolldateien“.
   - Wenn Syslog konfiguriert und **„Benutzeränderungen protokollieren“** in der MFA-Server-UX, im Abschnitt „Protokollierung“, auf der Registerkarte „Syslog“ aktiviert ist, können Protokolleinträge stattdessen aus Syslog erfasst werden.
- Andere Vorkommen des Benutzernamens im MultiFactorAuthSvc.log und in anderen MFA-Serverprotokolldateien, die sich auf Authentifizierungsversuche beziehen, gelten als operative Daten und Duplikate, die mithilfe des MultiFactorAuthGdpr.exe-Exports oder des Webdienst-SDK GetUserGdpr bereitgestellt wurden.

## <a name="delete-data-from-mfa-server"></a>Löschen von Daten vom MFA-Server

Führen Sie über die Befehlszeile des MFA-Servers den folgenden Befehl aus. Ändern Sie den Pfad gemäß Ihrer Installation `C:\Program Files\Multi-Factor Authentication Server\MultiFactorAuthGdpr.exe delete <username>`, um alle für diesen Benutzer erfassten MFA-Clouddienstinformationen zu löschen.

- Im Export enthaltene Daten werden in Echtzeit gelöscht. Es kann jedoch bis zu 30 Tage dauern, bis operative Daten oder Duplikate vollständig entfernt wurden.
- Administratoren können auch den DeleteUserGdpr-Vorgang des Webdienst-SDK verwenden, um alle erfassten MFA-Clouddienstinformationen für einen bestimmten Benutzer zu löschen oder in eine größere Berichtslösung zu integrieren.

## <a name="gather-data-from-nps-extension"></a>Löschen von Daten aus der NPS-Erweiterung

Verwenden Sie den [Datenschutzabschnitt im Azure-Portal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview), um eine Exportanfrage zu stellen.

- MFA-Informationen sind im Export enthalten, dessen Verarbeitung möglicherweise Stunden oder Tage dauert.
- Vorkommen des Benutzernamens in den Ereignisprotokollen AzureMfa/AuthN/AuthNOptCh AzureMfa/AuthZ/AuthZAdminCh und AzureMfa/AuthZ/AuthZOptCh gelten als operative Daten und Duplikate der im Export enthaltenen Informationen.

## <a name="delete-data-from-nps-extension"></a>Löschen von Daten aus der NPS-Erweiterung

Verwenden Sie den [Datenschutzabschnitt im Azure-Portal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview), um die Anfrage „Konto schließen“ zu stellen, damit alle für diesen Benutzer erfassten MFA-Clouddienstinformationen gelöscht werden.

- Es kann bis zu 30 Tage dauern, bis die Daten vollständig entfernt wurden.

## <a name="gather-data-from-windows-server-2016-azure-mfa-ad-fs-adapter"></a>Erfassen von Daten vom Windows Server 2016 Azure MFA AD FS-Adapter

Verwenden Sie den [Datenschutzabschnitt im Azure-Portal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview), um eine Exportanfrage zu stellen. 

- MFA-Informationen sind im Export enthalten, dessen Verarbeitung möglicherweise Stunden oder Tage dauert.
- Vorkommen des Benutzernamens in den AD FS-Ablaufverfolgungs-/Debugereignisprotokollen (sofern aktiviert) gelten als operative Daten und Duplikate der im Export enthaltenen Informationen.

## <a name="delete-data-from-windows-server-2016-azure-mfa-ad-fs-adapter"></a>Löschen von Daten vom Windows Server 2016 Azure MFA AD FS-Adapter

Verwenden Sie den [Datenschutzabschnitt im Azure-Portal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview), um die Anfrage „Konto schließen“ zu stellen, damit alle für diesen Benutzer erfassten MFA-Clouddienstinformationen gelöscht werden.

- Es kann bis zu 30 Tage dauern, bis die Daten vollständig entfernt wurden.

## <a name="gather-data-for-azure-mfa"></a>Erfassen von Daten für Azure MFA

Verwenden Sie den [Datenschutzabschnitt im Azure-Portal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview), um eine Exportanfrage zu stellen.

- MFA-Informationen sind im Export enthalten, dessen Verarbeitung möglicherweise Stunden oder Tage dauert.

## <a name="delete-data-for-azure-mfa"></a>Löschen von Daten für Azure MFA

Verwenden Sie den [Datenschutzabschnitt im Azure-Portal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview), um die Anfrage „Konto schließen“ zu stellen, damit alle für diesen Benutzer erfassten MFA-Clouddienstinformationen gelöscht werden.

- Es kann bis zu 30 Tage dauern, bis die Daten vollständig entfernt wurden.

## <a name="next-steps"></a>Nächste Schritte

[Berichte für MFA-Server](howto-mfa-reporting.md)
