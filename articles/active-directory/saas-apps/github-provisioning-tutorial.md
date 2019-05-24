---
title: 'Tutorial: Konfigurieren von GitHub für die automatische Benutzerbereitstellung in Azure Active Directory | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie Azure Active Directory für das automatische Bereitstellen und Aufheben der Bereitstellung von Benutzerkonten in GitHub konfigurieren.
services: active-directory
documentationcenter: ''
author: ArvindHarinder1
manager: CelesteDG
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 356af6567cea96efc7edbe8bc4932182d35ebc07
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65963942"
---
# <a name="tutorial-configure-github-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von GitHub für die automatische Benutzerbereitstellung

In diesem Tutorial werden die Schritte beschrieben, die Sie in GitHub und Azure AD ausführen müssen, um Benutzerkonten von Azure AD in GitHub automatisch bereitzustellen bzw. deren Bereitstellung automatisch aufzuheben.

## <a name="prerequisites"></a>Voraussetzungen

Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

* Einen Azure Active Directory-Mandanten
* Eine in [GitHub Enterprise Cloud](https://help.github.com/articles/github-s-products/#github-enterprise) erstellte GitHub-Organisation (erfordert den [Abrechnungsplan für GitHub Enterprise](https://help.github.com/articles/github-s-billing-plans/#billing-plans-for-organizations)).
* Ein Benutzerkonto in GitHub mit Administratorberechtigungen für die Organisation

> [!NOTE]
> Die Integration der Azure AD-Bereitstellung basiert auf der [GitHub-SCIM-API](https://developer.github.com/v3/scim/), die für [GitHub Enterprise Cloud](https://help.github.com/articles/github-s-products/#github-enterprise)-Kunden gemäß dem [GitHub Enterprise-Abrechnungsplan](https://help.github.com/articles/github-s-billing-plans/#billing-plans-for-organizations) zur Verfügung steht.

## <a name="assigning-users-to-github"></a>Zuweisen von Benutzern zu GitHub

Azure Active Directory ermittelt anhand von Zuweisungen, welche Benutzer Zugriff auf bestimmte Apps erhalten sollen. Im Kontext der automatischen Bereitstellung von Benutzerkonten werden nur die Benutzer und Gruppen synchronisiert, die einer Anwendung in Azure AD zugewiesen wurden. 

Vor dem Konfigurieren und Aktivieren des Bereitstellungsdiensts müssen Sie entscheiden, welche Benutzer und/oder Gruppen in Azure AD die Benutzer darstellen, die Zugriff auf Ihre GitHub-App benötigen. Anschließend können Sie diese Benutzer Ihrer GitHub-App zuweisen, indem Sie diese Anweisungen befolgen:

[Zuweisen eines Benutzers oder einer Gruppe zu einer Unternehmens-App](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-github"></a>Wichtige Tipps zum Zuweisen von Benutzern zu GitHub

* Es wird empfohlen, GitHub einen einzelnen Azure AD-Benutzer zuzuweisen, um die Konfiguration der Bereitstellung zu testen. Später können weitere Benutzer und/oder Gruppen zugewiesen werden.

* Beim Zuweisen eines Benutzers zu GitHub müssen Sie entweder die Rolle **Benutzer** oder eine andere gültige anwendungsspezifische Rolle (sofern verfügbar) im Dialogfeld für die Zuweisung auswählen. Die Rolle **Standardzugriff** funktioniert nicht für die Bereitstellung. Diese Benutzer werden übersprungen.

## <a name="configuring-user-provisioning-to-github"></a>Konfigurieren der Benutzerbereitstellung in GitHub

In diesem Abschnitt wird das Herstellen einer Verbindung von Azure AD mit der GitHub-API zur Bereitstellung von Benutzerkonten sowie das Konfigurieren des Bereitstellungsdiensts für das Erstellen, Aktualisieren und Deaktivieren zugewiesener Benutzerkonten in GitHub basierend auf der Benutzer- und Gruppenzuweisung in Azure AD beschrieben.

> [!TIP]
> Sie können auch das SAML-basierte einmalige Anmelden für GitHub aktivieren. Befolgen Sie hierzu die Anweisungen im [Azure-Portal](https://portal.azure.com). Einmaliges Anmelden kann unabhängig von der automatischen Bereitstellung konfiguriert werden, obwohl diese beiden Features einander ergänzen.

### <a name="configure-automatic-user-account-provisioning-to-github-in-azure-ad"></a>Konfigurieren der automatischen Bereitstellung von Benutzerkonten für GitHub in Azure AD

1. Wechseln Sie im [Azure-Portal](https://portal.azure.com) zum Abschnitt **Azure Active Directory > Unternehmens-Apps > Alle Anwendungen**.

2. Suchen Sie über das Suchfeld nach Ihrer GitHub-Instanz, wenn Sie GitHub bereits für einmaliges Anmelden konfiguriert haben. Wählen Sie andernfalls **Hinzufügen**, und suchen Sie im Anwendungskatalog nach **GitHub**. Wählen Sie GitHub in den Suchergebnissen aus, und fügen Sie es Ihrer Anwendungsliste hinzu.

3. Wählen Sie Ihre GitHub-Instanz aus, und wählen Sie dann die Registerkarte **Bereitstellung**.

4. Legen Sie den **Bereitstellungsmodus** auf **Automatisch** fest.

    ![GitHub-Bereitstellung](./media/github-provisioning-tutorial/GitHub1.png)

5. Klicken Sie im Abschnitt **Administratoranmeldeinformationen** auf **Autorisieren**. Mit diesem Vorgang wird ein GitHub-Autorisierungsdialogfeld in einem neuen Browserfenster geöffnet. 

6. Melden Sie sich im neuen Fenster mit Ihrem Administratorkonto bei GitHub an. Wählen Sie im daraufhin angezeigten Autorisierungsdialogfeld das GitHub-Team aus, für das Sie die Bereitstellung aktivieren möchten, und wählen Sie dann **Autorisieren**. Kehren Sie anschließend zum Azure-Portal zurück, um die Konfiguration der Bereitstellung abzuschließen.

    ![Autorisierungsdialogfeld](./media/github-provisioning-tutorial/GitHub2.png)

7. Geben Sie im Azure-Portal die **Mandanten-URL** ein, und klicken Sie auf **Verbindung testen**, um sicherzustellen, dass Azure AD eine Verbindung mit Ihrer GitHub-App herstellen kann. Überprüfen Sie bei einem Verbindungsfehler, ob das GitHub-Konto über Administratorberechtigungen verfügt und die **Mandanten-URL** korrekt eingegeben wurde. Wiederholen Sie dann den Schritt „Autorisieren“. (Sie können die **Mandanten-URL** durch eine Regel ersetzen: `https://api.github.com/scim/v2/organizations/<Organization_name>`. Die Organisationen finden Sie im GitHub-Konto unter **Einstellungen** > **Organisationen**).

    ![Autorisierungsdialogfeld](./media/github-provisioning-tutorial/GitHub3.png)

8. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder einer Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll, und aktivieren Sie das Kontrollkästchen „Bei Fehler E-Mail-Benachrichtigung senden“.

9. Klicken Sie auf **Speichern**.

10. Wählen Sie im Abschnitt „Zuordnungen“ die Option **Azure Active Directory-Benutzer mit GitHub synchronisieren**.

11. Überprüfen Sie im Abschnitt **Attributzuordnungen** die Benutzerattribute, die von Azure AD mit GitHub synchronisiert werden. Beachten Sie, dass die als **übereinstimmende** Eigenschaften ausgewählten Attribute für den Abgleich der Benutzerkonten in GitHub für Updatevorgänge verwendet werden. Wählen Sie die Schaltfläche „Speichern“, um alle Änderungen zu übernehmen.

12. Um den Azure AD-Bereitstellungsdienst für GitHub zu aktivieren, ändern Sie den **Bereitstellungsstatus** im Abschnitt **Einstellungen** in **Ein**.

13. Klicken Sie auf **Speichern**.

Dadurch wird die Erstsynchronisierung aller Benutzer und/oder Gruppen gestartet, die GitHub im Abschnitt „Benutzer und Gruppen“ zugewiesen sind. Die Erstsynchronisierung dauert länger als nachfolgende Synchronisierungen, die ungefähr alle 40 Minuten erfolgen, solange der Dienst ausgeführt wird. Im Abschnitt **Synchronisierungsdetails** können Sie den Fortschritt überwachen und Links zu Protokollen zur Bereitstellungsaktivität aufrufen. Darin sind alle Aktionen aufgeführt, die vom Bereitstellungsdienst ausgeführt werden.

Weitere Informationen zum Lesen von Azure AD-Bereitstellungsprotokollen finden Sie unter [Tutorial: Meldung zur automatischen Benutzerkontobereitstellung](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen.](../manage-apps/check-status-user-account-provisioning.md)
