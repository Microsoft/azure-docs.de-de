---
title: 'Tutorial: Konfigurieren von Zscaler für die automatische Benutzerbereitstellung mit Azure Active Directory | Microsoft-Dokumentation'
description: Hier erfahren Sie, wie Sie Azure Active Directory zum automatischen Bereitstellen und Aufheben der Bereitstellung von Benutzerkonten in Zscaler konfigurieren.
services: active-directory
documentationcenter: ''
author: zchia
writer: zchia
manager: beatrizd-msft
ms.assetid: 31f67481-360d-4471-88c9-1cc9bdafee24
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: v-ant-msft
ms.openlocfilehash: 3ea502477cc5b380c99a183d9270c2b2e94375a8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67049349"
---
# <a name="tutorial-configure-zscaler-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von Zscaler für die automatische Benutzerbereitstellung

In diesem Tutorial werden die Schritte erläutert, die in Zscaler und Azure Active Directory (Azure AD) ausgeführt werden müssen, um Azure AD zum automatischen Bereitstellen und Aufheben der Bereitstellung von Benutzern und/oder Gruppen in Zscaler zu konfigurieren.

> [!NOTE]
> In diesem Tutorial wird ein Connector beschrieben, der auf dem Benutzerbereitstellungsdienst von Azure AD basiert. Wichtige Details zum Zweck und zur Funktionsweise dieses Diensts sowie häufig gestellte Fragen finden Sie unter [Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](../active-directory-saas-app-provisioning.md).
>

> Dieser Connector befindet sich derzeit in der Public Preview-Phase. Weitere Informationen zu den allgemeinen Nutzungsbedingungen von Microsoft Azure für Previewfunktionen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Voraussetzungen

Das in diesem Tutorial beschriebene Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

* Einen Azure AD-Mandanten
* Einen Zscaler-Mandanten
* Ein Benutzerkonto in Zscaler mit Administratorberechtigungen

> [!NOTE]
> Die Integration der Azure AD-Bereitstellung basiert auf der Zscaler-SCIM-API, die Zscaler-Entwicklern für Konten mit dem Enterprise-Paket zur Verfügung steht.

## <a name="adding-zscaler-from-the-gallery"></a>Hinzufügen von Zscaler über den Katalog

Bevor Sie Zscaler für die automatische Benutzerbereitstellung mit Azure AD konfigurieren, müssen Sie Zscaler aus dem Azure AD-Anwendungskatalog der Liste mit den verwalteten SaaS-Anwendungen hinzufügen.

**Führen Sie die folgenden Schritte aus, um Zscaler aus dem Azure AD-Anwendungskatalog hinzuzufügen:**

1. Klicken Sie im linken Navigationsbereich des **[Azure-Portals](https://portal.azure.com)** auf das Symbol für **Azure Active Directory**.

    ![Schaltfläche „Azure Active Directory“](common/select-azuread.png)

2. Navigieren Sie zu **Unternehmensanwendungen**, und wählen Sie die Option **Alle Anwendungen** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

3. Klicken Sie oben im Dialogfeld auf die Schaltfläche **Neue Anwendung**, um eine neue Anwendung hinzuzufügen.

    ![Schaltfläche „Neue Anwendung“](common/add-new-app.png)

4. Geben Sie im Suchfeld den Namen **Zscaler** ein, wählen Sie im Ergebnisbereich den Eintrag **Zscaler** aus, und klicken Sie dann auf die Schaltfläche **Hinzufügen**, um die Anwendung hinzuzufügen.

    ![Zscaler in der Ergebnisliste](common/search-new-app.png)

## <a name="assigning-users-to-zscaler"></a>Zuweisen von Benutzern zu Zscaler

Azure Active Directory ermittelt anhand von Zuweisungen, welche Benutzer Zugriff auf bestimmte Apps erhalten sollen. Im Kontext der automatischen Benutzerbereitstellung werden nur die Benutzer und/oder Gruppen synchronisiert, die einer Anwendung in Azure AD zugewiesen wurden.

Vor dem Konfigurieren und Aktivieren der automatischen Benutzerbereitstellung müssen Sie entscheiden, welche Benutzer und/oder Gruppen in Azure AD Zugriff auf Zscaler benötigen. Anschließend können Sie diese Benutzer bzw. Gruppen Zscaler wie folgt zuweisen:

* [Zuweisen eines Benutzers oder einer Gruppe zu einer Unternehmens-App](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-assign-user-azure-portal)

### <a name="important-tips-for-assigning-users-to-zscaler"></a>Wichtige Tipps zum Zuweisen von Benutzern zu Zscaler

* Es wird empfohlen, Zscaler einen einzelnen Azure AD-Benutzer zuzuweisen, um die Konfiguration der automatischen Benutzerbereitstellung zu testen. Später können weitere Benutzer und/oder Gruppen zugewiesen werden.

* Beim Zuweisen eines Benutzers zu Zscaler müssen Sie im Dialogfeld für die Zuweisung eine gültige anwendungsspezifische Rolle auswählen (sofern verfügbar). Benutzer mit der Rolle **Standardzugriff** werden von der Bereitstellung ausgeschlossen.

## <a name="configuring-automatic-user-provisioning-to-zscaler"></a>Konfigurieren der automatischen Benutzerbereitstellung in Zscaler

In diesem Abschnitt werden die Schritte zum Konfigurieren des Azure AD-Bereitstellungsdiensts zum Erstellen, Aktualisieren und Deaktivieren von Benutzern bzw. Gruppen in Zscaler auf der Grundlage von Benutzer- oder Gruppenzuweisungen in Azure AD erläutert.

> [!TIP]
> Sie können auch das SAML-basierte einmalige Anmelden für Zscaler aktivieren. Befolgen Sie dazu die Anweisungen im [SSO-Tutorial zu Zscaler](zscaler-tutorial.md). Einmaliges Anmelden kann unabhängig von der automatischen Benutzerbereitstellung konfiguriert werden, obwohl diese beiden Features einander ergänzen.

### <a name="to-configure-automatic-user-provisioning-for-zscaler-in-azure-ad"></a>Gehen Sie wie folgt vor, um die automatische Benutzerbereitstellung für Zscaler in Azure AD zu konfigurieren:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und wählen Sie **Unternehmensanwendungen**, **Alle Anwendungen** und dann **Zscaler** aus.

    ![Blatt „Unternehmensanwendungen“](common/enterprise-applications.png)

2. Wählen Sie in der Liste der Anwendungen **Zscaler** aus.

    ![Zscaler-Link in der Anwendungsliste](common/all-applications.png)

3. Wählen Sie die Registerkarte **Bereitstellung**.

    ![Zscaler-Bereitstellung](./media/zscaler-provisioning-tutorial/provisioning-tab.png)

4. Legen Sie den **Bereitstellungsmodus** auf **Automatisch** fest.

    ![Zscaler-Bereitstellung](./media/zscaler-provisioning-tutorial/provisioning-credentials.png)

5. Geben Sie im Abschnitt **Administratoranmeldeinformationen** wie in Schritt 6 beschrieben die **Mandanten-URL** und das **geheime Token** Ihres Zscaler-Kontos ein.

6. Navigieren Sie auf der Benutzeroberfläche des Zscaler-Portals zu **Administration > Authentication Settings** (Verwaltung > Authentifizierungseinstellungen), und klicken Sie unter **Authentication Type** (Authentifizierungstyp) auf **SAML**, um die **Mandanten-URL** und das **geheime Token** abzurufen.

    ![Zscaler-Bereitstellung](./media/zscaler-provisioning-tutorial/secret-token-1.png)

    Klicken Sie auf **Configure SAML** (SAML konfigurieren), um die Optionen für die **SAML-Konfiguration**zu öffnen.

    ![Zscaler-Bereitstellung](./media/zscaler-provisioning-tutorial/secret-token-2.png)

    Wählen Sie **Enable SCIM-Based Provisioning** (SCIM-basierte Bereitstellung aktivieren) aus, um die **Basis-URL** und das **Bearertoken** abzurufen, und speichern Sie anschließend die Einstellungen. Kopieren Sie im Azure-Portal die **Basis-URL** in das Feld **Mandanten-URL** und das **Bearertoken** in das Feld **Geheimes Token**.

7. Klicken Sie nach dem Auffüllen der in Schritt 5 gezeigten Felder auf **Verbindung testen**, um sich zu vergewissern, dass Azure AD eine Verbindung mit Zscaler herstellen kann. Vergewissern Sie sich im Falle eines Verbindungsfehlers, dass Ihr Zscaler-Konto über Administratorberechtigungen verfügt, und wiederholen Sie den Vorgang.

    ![Zscaler-Bereitstellung](./media/zscaler-provisioning-tutorial/test-connection.png)

8. Geben Sie im Feld **Benachrichtigungs-E-Mail** die E-Mail-Adresse einer Person oder einer Gruppe ein, die Benachrichtigungen zu Bereitstellungsfehlern erhalten soll, und aktivieren Sie das Kontrollkästchen **Bei Fehler E-Mail-Benachrichtigung senden**.

    ![Zscaler-Bereitstellung](./media/zscaler-provisioning-tutorial/notification.png)

9. Klicken Sie auf **Speichern**.

10. Wählen Sie im Abschnitt **Zuordnungen** die Option **Synchronize Azure Active Directory Users to Zscaler** (Azure Active Directory-Benutzer mit Zscaler synchronisieren) aus.

    ![Zscaler-Bereitstellung](./media/zscaler-provisioning-tutorial/user-mappings.png)

11. Überprüfen Sie im Abschnitt **Attributzuordnungen** die Benutzerattribute, die von Azure AD mit Zscaler synchronisiert werden. Die als **Übereinstimmend** ausgewählten Attribute werden verwendet, um die Benutzerkonten in Zscaler für Updatevorgänge abzugleichen. Wählen Sie die Schaltfläche **Speichern**, um alle Änderungen zu übernehmen.

    ![Zscaler-Bereitstellung](./media/zscaler-provisioning-tutorial/user-attribute-mappings.png)

12. Wählen Sie im Abschnitt **Zuordnungen** die Option **Synchronize Azure Active Directory Groups to Zscaler** (Azure Active Directory-Gruppen mit Zscaler synchronisieren) aus.

    ![Zscaler-Bereitstellung](./media/zscaler-provisioning-tutorial/group-mappings.png)

13. Überprüfen Sie im Abschnitt **Attributzuordnungen** die Gruppenattribute, die von Azure AD mit Zscaler synchronisiert werden. Die als **Übereinstimmend** ausgewählten Attribute werden verwendet, um die Gruppen in Zscaler für Updatevorgänge abzugleichen. Wählen Sie die Schaltfläche **Speichern**, um alle Änderungen zu übernehmen.

    ![Zscaler-Bereitstellung](./media/zscaler-provisioning-tutorial/group-attribute-mappings.png)

14. Wenn Sie Bereichsfilter konfigurieren möchten, lesen Sie die Anweisungen unter [Attributbasierte Anwendungsbereitstellung mit Bereichsfiltern](./../active-directory-saas-scoping-filters.md).

15. Soll der Azure AD-Bereitstellungsdienst für Zscaler aktiviert werden, ändern Sie den **Bereitstellungsstatus** im Abschnitt **Einstellungen** in **Ein**.

    ![Zscaler-Bereitstellung](./media/zscaler-provisioning-tutorial/provisioning-status.png)

16. Legen Sie die Benutzer bzw. Gruppen fest, die in Zscaler bereitgestellt werden sollen. Wählen Sie dazu im Abschnitt **Einstellungen** unter **Bereich** die gewünschten Werte aus.

    ![Zscaler-Bereitstellung](./media/zscaler-provisioning-tutorial/scoping.png)

17. Wenn Sie fertig sind, klicken Sie auf **Speichern**.

    ![Zscaler-Bereitstellung](./media/zscaler-provisioning-tutorial/save-provisioning.png)

Dadurch wird die Erstsynchronisierung aller Benutzer und/oder Gruppen gestartet, die im Abschnitt **Einstellungen** unter **Bereich** definiert sind. Die Erstsynchronisierung dauert länger als nachfolgende Synchronisierungen, die ungefähr alle 40 Minuten erfolgen, solange der Azure AD-Bereitstellungsdienst ausgeführt wird. Im Abschnitt **Synchronisierungsdetails** können Sie den Fortschritt überwachen und Links zu Berichten zur Bereitstellungsaktivität aufrufen. Darin sind alle Aktionen aufgeführt, die vom Azure AD-Bereitstellungsdienst in Zscaler ausgeführt werden.

Weitere Informationen zum Lesen von Azure AD-Bereitstellungsprotokollen finden Sie unter [Tutorial: Meldung zur automatischen Benutzerkontobereitstellung](../active-directory-saas-provisioning-reporting.md).

## <a name="additional-resources"></a>Zusätzliche Ressourcen

* [Verwalten der Benutzerkontobereitstellung für Unternehmens-Apps](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen.](../active-directory-saas-provisioning-reporting.md)

<!--Image references-->
[1]: ./media/zscaler-provisioning-tutorial/tutorial-general-01.png
[2]: ./media/zscaler-provisioning-tutorial/tutorial-general-02.png
[3]: ./media/zscaler-provisioning-tutorial/tutorial-general-03.png
