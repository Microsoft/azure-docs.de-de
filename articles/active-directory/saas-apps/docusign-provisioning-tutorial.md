---
title: 'Tutorial: Konfigurieren von DocuSign für die automatische Benutzerbereitstellung in Azure Active Directory | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie das einmalige Anmelden zwischen Azure Active Directory und DocuSign konfigurieren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 294cd6b8-74d7-44bc-92bc-020ccd13ff12
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/26/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 121d147a3f8c91f17e955120b2c14f7dbd3da592
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60280118"
---
# <a name="tutorial-configure-docusign-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von DocuSign für die automatische Benutzerbereitstellung

In diesem Tutorial werden die Schritte beschrieben, die Sie in DocuSign und Azure AD ausführen müssen, um Benutzerkonten von Azure AD in DocuSign automatisch bereitzustellen bzw. deren Bereitstellung automatisch aufzuheben.

## <a name="prerequisites"></a>Voraussetzungen

Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

*   Einen Azure Active Directory-Mandanten
*   Ein DocuSign-Abonnement, für das einmaliges Anmelden aktiviert ist
*   Ein Benutzerkonto in DocuSign mit Teamadministratorberechtigung

## <a name="assigning-users-to-docusign"></a>Zuweisen von Benutzern zu DocuSign

Azure Active Directory ermittelt anhand von Zuweisungen, welche Benutzer Zugriff auf bestimmte Apps erhalten sollen. Im Kontext der automatischen Bereitstellung von Benutzerkonten werden nur die Benutzer und Gruppen synchronisiert, die einer Anwendung in Azure AD zugewiesen wurden.

Vor dem Konfigurieren und Aktivieren des Bereitstellungsdiensts müssen Sie entscheiden, welche Benutzer und/oder Gruppen in Azure AD die Benutzer darstellen, die Zugriff auf Ihre DocuSign-App benötigen. Anschließend können Sie diese Benutzer Ihrer DocuSign-App zuweisen, indem Sie diese Anweisungen befolgen:

[Zuweisen eines Benutzers oder einer Gruppe zu einer Unternehmens-App](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-docusign"></a>Wichtige Tipps zum Zuweisen von Benutzern zu DocuSign

*   Es wird empfohlen, DocuSign einen einzelnen Azure AD-Benutzer zuzuweisen, um die Konfiguration der Bereitstellung zu testen. Später können weitere Benutzer zugewiesen werden.

*   Wenn Sie DocuSign einen Benutzer zuweisen, müssen Sie eine gültige Benutzerrolle auswählen. Die Rolle „Standardzugriff“ funktioniert nicht für die Bereitstellung.

> [!NOTE]
> Azure AD unterstützt nicht die Gruppenbereitstellung mit der Docusign-Anwendung, es können stattdessen nur Benutzer bereitgestellt werden.

## <a name="enable-user-provisioning"></a>Aktivieren der Benutzerbereitstellung

In diesem Abschnitt wird das Herstellen einer Verbindung von Azure AD mit der DocuSign-API zur Bereitstellung von Benutzerkonten sowie das Konfigurieren des Bereitstellungsdiensts für das Erstellen, Aktualisieren und Deaktivieren zugewiesener Benutzerkonten in DocuSign basierend auf der Benutzer- und Gruppenzuweisung in Azure AD beschrieben.

> [!Tip]
> Sie können auch das SAML-basierte einmalige Anmelden für DocuSign aktivieren. Befolgen Sie hierzu die Anweisungen im [Azure-Portal](https://portal.azure.com). Einmaliges Anmelden kann unabhängig von der automatischen Bereitstellung konfiguriert werden, obwohl diese beiden Features einander ergänzen.

### <a name="to-configure-user-account-provisioning"></a>Gehen Sie folgendermaßen vor, um die Bereitstellung von Benutzerkonten zu konfigurieren:

In diesem Abschnitt wird erläutert, wie Sie die Bereitstellung von Active Directory-Benutzerkonten für DocuSign aktivieren.

1. Wechseln Sie im [Azure-Portal](https://portal.azure.com) zum Abschnitt **Azure Active Directory > Unternehmens-Apps > Alle Anwendungen**.

1. Wenn Sie DocuSign bereits für einmaliges Anmelden konfiguriert haben, suchen Sie über das Suchfeld nach Ihrer DocuSign-Instanz. Wählen Sie andernfalls **Hinzufügen** aus, und suchen Sie im Anwendungskatalog nach **DocuSign**. Wählen Sie „DocuSign“ in den Suchergebnissen aus, und fügen Sie es Ihrer Anwendungsliste hinzu.

1. Wählen Sie Ihre DocuSign-Instanz aus, und wählen Sie dann die Registerkarte **Bereitstellung** aus.

1. Legen Sie den **Bereitstellungsmodus** auf **Automatisch** fest. 

    ![Bereitstellung](./media/docusign-provisioning-tutorial/provisioning.png)

1. Geben Sie im Abschnitt **Administratoranmeldeinformationen** die folgenden Konfigurationseinstellungen an:
   
    a. Geben Sie im Textfeld **Administratorbenutzername** den Namen eines DocuSign-Kontos ein, dem das Profil **Systemadministrator** in „DocuSign.com“ zugewiesen ist.
   
    b. Geben Sie im Textfeld **Administratorkennwort** das Kennwort für dieses Konto ein.

1. Klicken Sie im Azure-Portal auf **Verbindung testen**, um sicherzustellen, dass Azure AD eine Verbindung mit Ihrer DocuSign-App herstellen kann.

1. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder einer Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll, und aktivieren Sie das Kontrollkästchen.

1. Klicken Sie auf **Speichern**.

1. Wählen Sie im Abschnitt „Zuordnungen“ die Option **Azure Active Directory-Benutzer mit DocuSign synchronisieren** aus.

1. Überprüfen Sie im Abschnitt **Attributzuordnungen** die Benutzerattribute, die von Azure AD mit DocuSign synchronisiert werden. Die als **übereinstimmende** Eigenschaften ausgewählten Attribute werden für den Abgleich der Benutzerkonten in DocuSign für Updatevorgänge verwendet. Wählen Sie die Schaltfläche „Speichern“, um alle Änderungen zu übernehmen.

1. Um den Azure AD-Bereitstellungsdienst für DocuSign zu aktivieren, ändern Sie den **Bereitstellungsstatus** im Abschnitt „Einstellungen“ in **Ein**.

1. Klicken Sie auf **Speichern**.

Dadurch wird die Erstsynchronisierung aller Benutzer gestartet, die DocuSign im Abschnitt „Benutzer und Gruppen“ zugewiesen sind. Die Erstsynchronisierung dauert länger als nachfolgende Synchronisierungen, die ungefähr alle 40 Minuten erfolgen, solange der Dienst ausgeführt wird. Im Abschnitt **Synchronisierungsdetails** können Sie den Fortschritt überwachen und Links zu Protokollen zur Bereitstellungsaktivität aufrufen. Darin sind alle Aktionen aufgeführt, die vom Bereitstellungsdienst in Ihrer DocuSign-App ausgeführt werden.

Weitere Informationen zum Lesen von Azure AD-Bereitstellungsprotokollen finden Sie unter [Tutorial: Meldung zur automatischen Benutzerkontobereitstellung](../manage-apps/check-status-user-account-provisioning.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](tutorial-list.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurieren des einmaligen Anmeldens](docusign-tutorial.md)
