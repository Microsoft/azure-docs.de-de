---
title: 'Tutorial: Konfigurieren von Workday für die automatische Benutzerbereitstellung in Azure Active Directory | Microsoft-Dokumentation'
description: Erfahren Sie, wie Sie Azure Active Directory für das automatische Bereitstellen und Aufheben der Bereitstellung von Benutzerkonten in Workday konfigurieren.
services: active-directory
author: cmmdesai
documentationcenter: na
manager: daveba
ms.assetid: 1a2c375a-1bb1-4a61-8115-5a69972c6ad6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/16/2019
ms.author: chmutali
ms.collection: M365-identity-device-management
ms.openlocfilehash: 31cf1f6da515aa9b453987383e78f466c5ba4fb9
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65827290"
---
# <a name="tutorial-configure-workday-for-automatic-user-provisioning"></a>Tutorial: Konfigurieren von Workday für die automatische Benutzerbereitstellung

In diesem Tutorial werden die Schritte vorgestellt, die Sie zum Importieren von Workerprofilen aus Workday in Active Directory und Azure Active Directory ausführen müssen, wobei einige E-Mail-Adressen und Benutzernamen optional in Workday zurückgeschrieben werden.

## <a name="overview"></a>Übersicht

Der [Azure Active Directory-Benutzerbereitstellungsdienst](../manage-apps/user-provisioning.md) ist zum Bereitstellen von Benutzerkonten mit der [Workday Human Resources-API](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html) integriert. Azure AD nutzt diese Verbindung zum Ermöglichen der folgenden Workflows für die Benutzerbereitstellung:

* **Bereitstellung von Benutzern in Active Directory:** Sie können ausgewählte Gruppen von Benutzern aus Workday in eine oder mehrere Active Directory-Domänen bereitstellen.

* **Bereitstellung reiner Cloudbenutzer in Azure Active Directory**: In Szenarien, in denen kein lokales Active Directory verwendet wird, können Benutzer mithilfe des Azure AD-Benutzerbereitstellungsdiensts direkt aus Workday in Azure Active Directory bereitgestellt werden.

* **Zurückschreiben von E-Mail-Adressen und Benutzernamen in Workday:** Der Azure AD-Benutzerbereitstellungsdienst kann die E-Mail-Adressen und den Benutzernamen von Azure AD in Workday zurückschreiben.

### <a name="what-human-resources-scenarios-does-it-cover"></a>Welche Szenarien im Personalwesen werden unterstützt?

Die vom Azure AD-Benutzerbereitstellungsdienst unterstützten Workday-Workflows zur Benutzerbereitstellung ermöglichen die Automatisierung der folgenden Szenarien im Personalwesen und bei der Verwaltung des Lebenszyklus von Identitäten:

* **Einstellung neuer Mitarbeiter**: Wenn Workday ein neuer Mitarbeiter hinzugefügt wird, wird in Active Directory, Azure Active Directory und optional Office 365 sowie [anderen von Azure AD unterstützten SaaS-Anwendungen](../manage-apps/user-provisioning.md) automatisch ein Benutzerkonto erstellt. Die E-Mail-Adresse wird dabei in Workday zurückgeschrieben.

* **Aktualisierungen von Mitarbeiterattributen und -profil**: Wenn ein Mitarbeiterdatensatz in Workday aktualisiert wird (wie z.B. der Name, Titel oder Vorgesetzte), wird das entsprechende Benutzerkonto in Active Directory, Azure Active Directory und optional Office 365 und [anderen von Azure AD unterstützten SaaS-Anwendungen](../manage-apps/user-provisioning.md) automatisch aktualisiert.

* **Kündigungen von Mitarbeitern** : Wenn einem Mitarbeiter in Workday gekündigt wird, wird das entsprechende Benutzerkonto in Active Directory, Azure Active Directory und optional Office 365 und [anderen von Azure AD unterstützten SaaS-Anwendungen](../manage-apps/user-provisioning.md) automatisch deaktiviert.

* **Erneute Einstellung von Mitarbeitern**: Wenn ein Mitarbeiter in Workday erneut eingestellt wird, kann sein altes Konto in Active Directory, Azure Active Directory und optional Office 365 und [anderen von Azure AD unterstützten SaaS-Anwendungen](../manage-apps/user-provisioning.md) automatisch reaktiviert oder erneut bereitgestellt werden (je nachdem, was Sie bevorzugen).

### <a name="who-is-this-user-provisioning-solution-best-suited-for"></a>Für wen ist diese Benutzerbereitstellungslösung am besten geeignet?

Diese Workday-Benutzerbereitstellungslösung eignet sich ideal für:

* Organisationen, die eine vorgefertigte, cloudbasierte Lösung für die Workday-Benutzerbereitstellung verwenden möchten

* Organisationen, bei denen Benutzer direkt aus Workday in Active Directory oder Azure Active Directory bereitgestellt werden müssen

* Organisationen, bei denen Benutzer mithilfe von Daten bereitgestellt werden müssen, die aus dem HCM-Modul von Workday abgerufen werden (siehe [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html))

* Organisationen, bei denen Benutzer beim Beitreten, Verschieben und Verlassen nur auf Grundlage von Änderungsinformationen, die im HCM-Modul von Workday erkannt werden, mit einer oder mehreren Active Directory-Gesamtstrukturen, -Domänen und -Organisationseinheiten synchronisiert werden müssen (siehe [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html))

* Organisationen, die Office 365 für E-Mail verwenden

## <a name="solution-architecture"></a>Lösungsarchitektur

In diesem Abschnitt wird die Lösungsarchitektur der End-to-End-Benutzerbereitstellung für häufige Hybridumgebungen beschrieben. Es gibt zwei zugehörige Flows:

* **Autoritativer Personaldatenfluss – von Workday in ein lokales Active Directory-Verzeichnis**: In diesem Flow treten mitarbeiterbezogene Ereignisse (z.B. Neueinstellungen, Wechsel, Kündigungen) zuerst im Cloud-HR-Mandanten von Workday auf und werden dann über Azure AD und den Bereitstellungs-Agent in ein lokales Active Directory-Verzeichnis übertragen. Abhängig vom Ereignis kann dies dann in Active Directory zu Erstellungs-, Aktualisierungs-, Aktivierungs- oder Deaktivierungsvorgängen führen.
* **Zurückschreibeflow für E-Mail-Adressen und Benutzernamen– von lokalen Active Directory-Instanzen zu Workday:** Nach Abschluss der Kontoerstellung in Active Directory wird das Konto über Azure AD Connect mit Azure AD synchronisiert. Anschließend können die Attribute für die E-Mail-Adresse und den Benutzernamen aus Active Directory in Workday zurückgeschrieben werden.

![Übersicht](./media/workday-inbound-tutorial/wd_overview.png)

### <a name="end-to-end-user-data-flow"></a>End-to-End-Benutzerdatenfluss

1. Das Team der Personalabteilung führt Mitarbeitertransaktionen (Einstellungen/Wechsel/Kündigungen) in Workday HCM aus.
2. Der Azure AD-Bereitstellungsdienst führt geplante Synchronisierungen von Identitäten aus Workday HR aus und ermittelt Änderungen, die für eine Synchronisierung mit dem lokalen Active Directory verarbeitet werden müssen.
3. Der Azure AD-Bereitstellungsdienst ruft den lokalen AAD Connect-Bereitstellungs-Agent mit einer Anforderungsnutzlast auf, die die Erstellungs-, Aktualisierungs-, Aktivierungs- oder Deaktivierungsvorgänge für das AD-Konto enthält.
4. Der Azure AD Connect-Bereitstellungs-Agent verwendet ein Dienstkonto zum Hinzufügen/Aktualisieren von AD-Kontodaten.
5. Die Azure AD Connect-/AAD Sync-Engine führt eine Deltasynchronisierung aus, um Updates in Active Directory zu pullen.
6. Die Active Directory-Updates werden mit Azure Active Directory synchronisiert.
7. Wenn der Workday Writeback-Connector konfiguriert ist, schreibt er das E-Mail-Attribut und den Benutzernamen zurück in Workday, wenn das verwendete Attribut übereinstimmt.

## <a name="planning-your-deployment"></a>Planen der Bereitstellung

Überprüfen Sie vor Beginn der Workday-Integration die folgenden Voraussetzungen, und lesen Sie die folgende Anleitung zum Erfüllen Ihrer aktuellen Anforderungen an die Active Directory-Architektur und -Benutzerbereitstellung mithilfe der von Azure Active Directory gebotenen Lösungen. Außerdem steht Ihnen ein umfassender [Bereitstellungsplan](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-deployment-plans) mit Planungsarbeitsblättern zur Verfügung, um Sie bei der Zusammenarbeit mit Ihrem Workday-Integrationspartner und HR-Beteiligten zu unterstützen.

Dieser Abschnitt enthält die folgenden Aspekte bezüglich der Planung:

* [Voraussetzungen](#prerequisites)
* [Auswählen von Bereitstellungsconnector-Apps für die Bereitstellung](#selecting-provisioning-connector-apps-to-deploy)
* [Planen der Bereitstellung des Azure AD Connect-Bereitstellungs-Agents](#planning-deployment-of-azure-ad-connect-provisioning-agent)
* [Integration in mehrere Active Directory-Domänen](#integrating-with-multiple-active-directory-domains)
* [Planen der Benutzerattributzuordnung und Transformationen zwischen Workday und Active Directory](#planning-workday-to-active-directory-user-attribute-mapping-and-transformations)

### <a name="prerequisites"></a>Voraussetzungen

Das in diesem Lernprogramm verwendete Szenario setzt voraus, dass Sie bereits über die folgenden Elemente verfügen:

* Gültiges Azure AD Premium P1-Abonnement oder höher mit globalem Administratorzugriff
* Workday-Implementierungsmandant für Test- und Integrationszwecke
* Administratorberechtigungen in Workday zum Erstellen eines Systemintegrationsbenutzers für Testzwecke und Vornehmen von Änderungen zum Testen von Mitarbeiterdaten
* Für die Benutzerbereitstellung in Active Directory einen Server mit mindestens Windows Server 2012 und der .NET-Runtime 4.7.1 und höher zum Hosten des [lokalen Bereitstellungs-Agents](https://go.microsoft.com/fwlink/?linkid=847801)
* [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) für die Synchronisierung von Benutzern zwischen Active Directory und Azure AD

### <a name="selecting-provisioning-connector-apps-to-deploy"></a>Auswählen von Bereitstellungsconnector-Apps für die Bereitstellung

Um Bereitstellungsworkflows für Workday und Active Directory zu ermöglichen, bietet Azure AD mehrere Bereitstellungsconnector-Apps, die über den Azure AD-App-Katalog hinzugefügt werden können:

![AAD-App-Katalog](./media/workday-inbound-tutorial/wd_gallery.png)

* **Workday to Active Directory User Provisioning:** Diese App vereinfacht die Bereitstellung von Benutzerkonten aus Workday in eine einzelne Active Directory-Domäne. Wenn Sie über mehrere Domänen verfügen, können Sie eine Instanz dieser App aus dem Azure AD-App-Katalog für jede Active Directory-Domäne hinzufügen, in der die Bereitstellung erfolgen soll.

* **Workday to Azure AD User Provisioning**: Wenngleich AAD Connect das Tool ist, das Sie zum Synchronisieren von Active Directory-Benutzern mit Azure Active Directory verwenden sollten, können Sie auch diese App nutzen, um die Bereitstellung reiner Cloudbenutzer aus Workday in einem einzelnen Azure Active Directory-Mandanten zu erleichtern.

* **Workday Writeback:** Diese App vereinfacht das Zurückschreiben von E-Mail-Adressen von Benutzern aus Azure Active Directory in Workday.

> [!TIP]
> Die reguläre „Workday“-App dient zum Einrichten des einmaligen Anmeldens zwischen Workday und Azure Active Directory.

Verwenden Sie das untenstehende Entscheidungsflussdiagramm, um zu ermitteln, welche Workday-Bereitstellungs-App für Ihr Szenario relevant sind.
    ![Entscheidungsflussdiagramm](./media/workday-inbound-tutorial/wday_app_flowchart.png "Entscheidungsflussdiagramm")

Über das Inhaltsverzeichnis können Sie den relevanten Abschnitt aus diesem Tutorial aufrufen.

### <a name="planning-deployment-of-azure-ad-connect-provisioning-agent"></a>Planen der Bereitstellung des Azure AD Connect-Bereitstellungs-Agents

> [!NOTE]
> Dieser Abschnitt ist für Sie nur dann relevant, wenn Sie die Workday to Active Directory User Provisioning-App bereitstellen möchten. Sie können diesen Schritt überspringen, wenn Sie die „Workday Writeback“- oder die „Workday to Azure AD“-Benutzerbereitstellungs-App bereitstellen.

Die Lösung für die Benutzerbereitstellung von Workday zu AD erfordert das Bereitstellen mindestens eines Bereitstellungs-Agents auf Servern unter Windows 2012 R2 oder höher mit mindestens 4 GB RAM und der .NET-Runtime 4.7.1 oder höher. Die folgenden Aspekte müssen vor der Installation des Bereitstellungs-Agents berücksichtigt werden:

* Stellen Sie sicher, dass der Hostserver, auf dem der Bereitstellungs-Agent ausgeführt wird, Netzwerkzugriff auf die AD-Zieldomäne hat.
* Der Konfigurations-Assistent für den Bereitstellungs-Agent registriert den Agent bei Ihrem Azure AD-Mandanten. Für den Registrierungsprozess ist Zugriff auf *.msappproxy.net über SSL-Port 443 notwendig. Stellen Sie sicher, dass die Firewallregeln für ausgehenden Datenverkehr diese Kommunikation erlauben. Der Agent unterstützt die [ausgehende HTTPS-Proxykonfiguration](#how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication).
* Der Bereitstellungs-Agent verwendet ein Dienstkonto für die Kommunikation mit den lokalen AD-Domänen. Vor der Installation des Agents empfiehlt es sich, ein Dienstkonto mit Domänenadministratorberechtigungen und einem Kennwort, das nicht abläuft, zu erstellen.  
* Sie können während der Konfiguration des Bereitstellungs-Agents Domänencontroller auswählen, die Bereitstellungsanforderungen verarbeiten sollen. Wenn Sie über mehrere geografisch verteilte Domänencontroller verfügen, Installieren Sie den Bereitstellungs-Agent am selben Standort wie Ihre bevorzugten Domänencontroller. Damit steigern Sie die Zuverlässigkeit und Leistung der End-to-End-Lösung.
* Für Hochverfügbarkeit können Sie auch mehrere Bereitstellungs-Agents bereitstellen und registrieren, die dann den gleichen Satz von lokalen AD-Domänen behandeln.

> [!IMPORTANT]
> Für Hochverfügbarkeit in Produktionsumgebungen empfiehlt Microsoft mindestens drei Bereitstellungs-Agents, die mit Ihrem Azure AD-Mandanten konfiguriert sind.

### <a name="integrating-with-multiple-active-directory-domains"></a>Integration in mehrere Active Directory-Domänen

> [!NOTE]
> Dieser Abschnitt ist für Sie nur dann relevant, wenn Sie die Workday to Active Directory User Provisioning-App bereitstellen möchten. Sie können diesen Schritt überspringen, wenn Sie die „Workday Writeback“- oder die „Workday to Azure AD“-Benutzerbereitstellungs-App bereitstellen.

Abhängig von Ihrer Active Directory-Topologie müssen Sie die Anzahl der Connector-Apps für die Benutzerbereitstellung und die Anzahl der zu konfigurierenden Bereitstellungs-Agents festlegen. Nachfolgend sind einige der häufigsten Bereitstellungsmuster aufgeführt, die Sie bei der Planung Ihrer Bereitstellung heranziehen können.

#### <a name="deployment-scenario-1--single-workday-tenant---single-ad-domain"></a>Bereitstellungsszenario 1: Einzelner Workday-Mandant -> Einzelne AD-Domäne

In diesem Szenario haben Sie einen Workday-Mandanten und möchten die Benutzer für eine einzelne AD-Zieldomäne bereitstellen. Hier ist die empfohlene Produktionskonfiguration für diese Bereitstellung.

|   |   |
| - | - |
| Nein. der lokal bereitzustellenden Bereitstellungs-Agents | 3 (für Hochverfügbarkeit und Failover) |
| Nein. der im Azure-Portal zu konfigurierenden „Workday to AD“-Benutzerbereitstellungs-Apps | 1 |

  ![Szenario 1](./media/workday-inbound-tutorial/dep_scenario1.png)

#### <a name="deployment-scenario-2--single-workday-tenant---multiple-child-ad-domains"></a>Bereitstellungsszenario 2: Einzelner Workday-Mandant -> Mehrere untergeordnete AD-Domänen

Dieses Szenario beinhaltet die Bereitstellung von Benutzern aus Workday für mehrere untergeordnete AD-Domänen in einer Gesamtstruktur. Hier ist die empfohlene Produktionskonfiguration für diese Bereitstellung.

|   |   |
| - | - |
| Nein. der lokal bereitzustellenden Bereitstellungs-Agents | 3 (für Hochverfügbarkeit und Failover) |
| Nein. der im Azure-Portal zu konfigurierenden „Workday to AD“-Benutzerbereitstellungs-Apps | Eine App pro untergeordneter Domäne |

  ![Szenario 2:](./media/workday-inbound-tutorial/dep_scenario2.png)

#### <a name="deployment-scenario-3--single-workday-tenant---disjoint-ad-forests"></a>Bereitstellungsszenario 3: Einzelner Workday-Mandant -> Nicht zusammenhängende AD-Gesamtstrukturen

Dieses Szenario beinhaltet die Bereitstellung von Benutzern aus Workday für Domänen in nicht zusammenhängenden AD-Gesamtstrukturen. Hier ist die empfohlene Produktionskonfiguration für diese Bereitstellung.

|   |   |
| - | - |
| Nein. der lokal bereitzustellenden Bereitstellungs-Agents | 3 pro nicht zusammenhängender AD-Gesamtstruktur |
| Nein. der im Azure-Portal zu konfigurierenden „Workday to AD“-Benutzerbereitstellungs-Apps | Eine App pro untergeordneter Domäne |

  ![Szenario 3](./media/workday-inbound-tutorial/dep_scenario3.png)

### <a name="planning-workday-to-active-directory-user-attribute-mapping-and-transformations"></a>Planen der Benutzerattributzuordnung und Transformationen zwischen Workday und Active Directory

> [!NOTE]
> Dieser Abschnitt ist für Sie nur dann relevant, wenn Sie die Workday to Active Directory User Provisioning-App bereitstellen möchten. Sie können diesen Schritt überspringen, wenn Sie die „Workday Writeback“- oder die „Workday to Azure AD“-Benutzerbereitstellungs-App bereitstellen.

Beantworten Sie die folgenden Fragen, bevor Sie die Benutzerbereitstellung in einer Active Directory-Domäne konfigurieren. Die Antworten auf diese Fragen bestimmen, wie Ihre Bereichsfilter und Attributzuordnungen festgelegt werden müssen.

* **Welche Benutzer in Workday müssen in dieser Active Directory-Gesamtstruktur bereitgestellt werden?**

  * *Beispiel: Benutzer, bei denen das Workday-Attribut „Company“ den Wert „Contoso“ und das Attribut „Worker_Type“ den Wert „Regular“ enthält*

* **Wie werden Benutzer in verschiedene Organisationseinheiten (OEs) weitergeleitet?**

  * *Beispiel: Benutzer werden wie in den Workday-Attributen „Municipality“ und „Country_Region_Reference“ definiert zu Organisationseinheiten weitergeleitet, die einem Bürostandort entsprechen*

* **Wie müssen die folgenden Attribute in Active Directory aufgefüllt werden?**

  * Allgemeiner Name (cn)
    * *Beispiel: Verwenden Sie den von der Personalabteilung festgelegten Workday-Wert „User_ID“.*

  * Mitarbeiter-ID (employeeId)
    * *Beispiel: Verwenden Sie den Workday-Wert „Worker_ID“.*

  * SAM-Kontoname (sAMAccountName)
    * *Beispiel: Verwenden Sie den Workday-Wert „User_ID“, gefiltert mit einem Azure AD-Bereitstellungsausdruck, um unzulässige Zeichen zu entfernen.*

  * Benutzerprinzipalname (userPrincipalName)
    * *Beispiel: Verwenden Sie den Workday-Wert „User_ID“ mit einem Azure AD-Bereitstellungsausdruck, um einen Domänennamen anzufügen.*

* **Wie müssen Benutzer zwischen Workday und Active Directory zugeordnet werden?**

  * *Beispiel: Benutzer mit einem bestimmten Workday-Wert „Worker_ID“ werden Active Directory-Benutzern zugeordnet, deren „employeeID“ den gleichen Wert enthält. Wenn der Wert von „Worker_ID“ nicht in Active Directory gefunden wird, erstellen Sie einen neuen Benutzer.*
  
* **Enthält die Active Directory-Gesamtstruktur bereits die Benutzer-IDs, die notwendig sind, damit die Zuordnungslogik funktioniert?**

  * *Beispiel: Im Fall einer neuen Workday-Bereitstellung wird empfohlen, Active Directory vorab mit den korrekten Worker_ID-Werten aus Workday (oder einem eindeutigen ID-Wert Ihrer Wahl) aufzufüllen, um die Zuordnungslogik so einfach wie möglich zu halten.*

Das Einrichten und Konfigurieren dieser spezielle Bereitstellungsconnector-Apps ist das Thema in den verbleibenden Abschnitten dieses Tutorials. Welche Apps Sie konfigurieren, hängt davon ab, in welchen Systemen die Bereitstellung erfolgen soll und wie viele Active Directory-Domänen und Azure AD-Mandanten in Ihrer Umgebung vorhanden sind.

## <a name="configure-integration-system-user-in-workday"></a>Konfigurieren eines Integrationssystembenutzers in Workday

Eine häufige Anforderung aller Workday-Bereitstellungsconnectors ist, dass sie die Anmeldeinformationen eines Workday-Integrationssystembenutzers benötigen, um sich mit der Workday Human Resources-API zu verbinden. In diesem Abschnitt wird das Erstellen eines Integrationssystembenutzers in Workday beschrieben. Hier finden Sie folgende Themen:

* [Erstellen eines Integrationssystembenutzers](#creating-an-integration-system-user)
* [Erstellen einer Integrationssicherheitsgruppe](#creating-an-integration-security-group)
* [Konfigurieren der Berechtigungen der Domänensicherheitsrichtlinie](#configuring-domain-security-policy-permissions)
* [Konfigurieren von Sicherheitsrichtlinienberechtigungen für Geschäftsprozesse](#configuring-business-process-security-policy-permissions)
* [Aktivieren von Sicherheitsrichtlinienänderungen](#activating-security-policy-changes)

> [!NOTE]
> Sie können diesen Schritt auslassen und stattdessen ein globales Workday-Administratorkonto als Systemintegrationskonto nutzen. Diese Vorgehensweise ist für Demos einwandfrei, wird jedoch für Produktionsbereitstellungen nicht empfohlen.

### <a name="creating-an-integration-system-user"></a>Erstellen eines Integrationssystembenutzers

**So erstellen Sie einen Integrationssystembenutzer**

1. Melden Sie sich mithilfe eines Administratorkontos bei Ihrem Workday-Mandanten an. Geben Sie in der **Workday-Anwendung** die Suchzeichenfolge „Benutzer erstellen“ in das Suchfeld ein, und klicken Sie dann auf den Link **Create Integration System User** (Integrationssystembenutzer erstellen).

    ![Benutzer erstellen](./media/workday-inbound-tutorial/wd_isu_01.png "Benutzer erstellen")
2. Führen Sie die Aufgabe **Integrationssystembenutzer erstellen** aus, indem Sie einen Benutzernamen und ein Kennwort für einen neuen Integrationssystembenutzer angeben.  
  
* Lassen Sie das Kontrollkästchen **Bei der nächsten Anmeldung neues Kennwort anfordern** deaktiviert. Dieser Benutzer meldet sich programmgesteuert an.
* Übernehmen Sie für **Sitzungstimeout in Minuten** den Standardwert 0. Diese Einstellung verhindert, dass Sitzungen des Benutzers vorzeitig beendet werden.
* Wählen Sie die Option **Do Not Allow UI Sessions** (Keine Sitzungen mit Benutzeroberfläche zulassen) aus. Sie bietet zusätzliche Sicherheit, da sie verhindert, dass sich ein Benutzer mit dem Kennwort für das Integrationssystem bei Workday anmeldet.

    ![Integrationssystembenutzer erstellen](./media/workday-inbound-tutorial/wd_isu_02.png "Integrationssystembenutzer erstellen")

### <a name="creating-an-integration-security-group"></a>Erstellen einer Integrationssicherheitsgruppe

In diesem Schritt erstellen Sie eine uneingeschränkte oder eingeschränkte Sicherheitsgruppe für das Integrationssystem in Workday und ordnen den Integrationssystembenutzer, den Sie im vorherigen Schritt erstellt haben, dieser Gruppe zu.

**So erstellen Sie eine Sicherheitsgruppe**

1. Geben Sie „Sicherheitsgruppe erstellen“ in das Suchfeld ein, und klicken Sie dann auf **Sicherheitsgruppe erstellen**.

    ![Sicherheitsgruppe erstellen](./media/workday-inbound-tutorial/wd_isu_03.png "Sicherheitsgruppe erstellen")
2. Führen Sie die Aufgabe **Sicherheitsgruppe erstellen** aus. 

   * In Workday gibt es zwei Typen von Sicherheitsgruppen:
     * **Uneingeschränkt:** Alle Mitglieder der Sicherheitsgruppe können auf alle Dateninstanzen zugreifen, die durch die Sicherheitsgruppe gesichert sind.
     * **Eingeschränkt:** Alle Mitglieder der Sicherheitsgruppe haben kontextbezogene Zugriff auf eine Teilmenge der Dateninstanzen (Zeilen), auf die die Sicherheitsgruppe zugreifen kann.
   * Bitte wenden Sie sich an Ihren Workday-Integrationspartner, um den geeigneten Sicherheitsgruppentyp für die Integration auszuwählen.
   * Sobald Sie den Gruppentyp kennen, wählen Sie **Integrationssystem-Sicherheitsgruppe (Uneingeschränkt)** oder **Integrationssystem-Sicherheitsgruppe (Eingeschränkt)** aus der Dropdownliste **Typ der Mandantensicherheitsgruppe**.

     ![Sicherheitsgruppe erstellen](./media/workday-inbound-tutorial/wd_isu_04.png "Sicherheitsgruppe erstellen")

3. Nach dem Erstellen der Sicherheitsgruppe wird eine Seite angezeigt, auf der Sie der Sicherheitsgruppe Mitglieder zuweisen können. Fügen Sie den im vorherigen Schritt erstellten neuen Benutzer des Integrationssystems zu dieser Sicherheitsgruppe hinzu. Wenn Sie die Sicherheitsgruppe *eingeschränkt* verwenden, müssen Sie auch den entsprechenden Organisationsbereich auswählen.

    ![Sicherheitsgruppe bearbeiten](./media/workday-inbound-tutorial/wd_isu_05.png "Sicherheitsgruppe bearbeiten")

### <a name="configuring-domain-security-policy-permissions"></a>Konfigurieren der Berechtigungen der Domänensicherheitsrichtlinie

In diesem Schritt gewähren Sie der Sicherheitsgruppe die Berechtigungen der Domänensicherheitsrichtlinie für die Mitarbeiterdaten.

**So konfigurieren Sie die Berechtigungen der Domänensicherheitsrichtlinie**

1. Geben Sie **Domain Security Configuration** (Domänensicherheitskonfiguration) in das Suchfeld ein, und klicken Sie dann auf den Link **Domain Security Configuration Report** (Bericht zur Domänensicherheitskonfiguration).  

    ![Domänensicherheitsrichtlinien](./media/workday-inbound-tutorial/wd_isu_06.png "Domänensicherheitsrichtlinien")  
2. Suchen Sie im Textfeld **Domäne** nach den folgenden Domänen, und fügen Sie sie einzeln dem Filter hinzu.  
   * *External Account Provisioning* (Externe Kontobereitstellung)
   * *Worker Data: Public Worker Reports* (Mitarbeiterdaten: öffentliche Mitarbeiterberichte)
   * *Person Data: Work Contact Information* (Personendaten: Kontaktinformationen von Mitarbeitern)
   * *Worker Data: All Positions* (Mitarbeiterdaten: alle Positionen)
   * *Worker Data: Current Staffing Information* (Mitarbeiterdaten: aktuelle Personalinformationen)
   * *Worker Data: Business Title on Worker Profile* (Mitarbeiterdaten: Berufsbezeichnung in Mitarbeiterprofil)
   * *Workday-Konten*
   
     ![Domänensicherheitsrichtlinien](./media/workday-inbound-tutorial/wd_isu_07.png "Domänensicherheitsrichtlinien")  

     ![Domänensicherheitsrichtlinien](./media/workday-inbound-tutorial/wd_isu_08.png "Domänensicherheitsrichtlinien") 

     Klicken Sie auf **OK**.

3. Wählen Sie im angezeigten Bericht die Auslassungspunkte (...) neben **External Account Provisioning** (Externe Kontobereitstellung) aus, und klicken Sie auf die Menüoption **Domain -> Edit Security Policy Permissions** (Domäne > Berechtigungen für Sicherheitsrichtlinie bearbeiten).

    ![Domänensicherheitsrichtlinien](./media/workday-inbound-tutorial/wd_isu_09.png "Domänensicherheitsrichtlinien")  

4. Scrollen Sie auf der Seite **Edit Domain Security Policy Permissions** (Berechtigungen für Domänensicherheitsrichtlinie bearbeiten) nach unten zum Abschnitt **Integration Permissions** (Integrationsberechtigungen). Klicken Sie auf das Zeichen „+“, um die Integrationssystemgruppe der Liste der Sicherheitsgruppen mit den Integrationsberechtigungen **Get** und **Put** hinzuzufügen.

    ![Berechtigung bearbeiten](./media/workday-inbound-tutorial/wd_isu_10.png "Berechtigung bearbeiten")  

5. Klicken Sie auf das Zeichen „+“, um die Integrationssystemgruppe der Liste der Sicherheitsgruppen mit den Integrationsberechtigungen **Get** und **Put** hinzuzufügen.

    ![Berechtigung bearbeiten](./media/workday-inbound-tutorial/wd_isu_11.png "Berechtigung bearbeiten")  

6. Wiederholen Sie die obigen Schritte 3 bis 5 für jede der folgenden verbleibenden Sicherheitsrichtlinien:

   | Vorgang | Domänensicherheitsrichtlinie |
   | ---------- | ---------- |
   | Get und Put | Mitarbeiterdaten: öffentliche Mitarbeiterberichte |
   | Get und Put | Personendaten: Kontaktinformationen von Mitarbeitern |
   | Get | Mitarbeiterdaten: alle Positionen |
   | Get | Mitarbeiterdaten: aktuelle Personalinformationen |
   | Get | Mitarbeiterdaten: Berufsbezeichnung in Mitarbeiterprofil |
   | Get und Put | Workday-Konten |

### <a name="configuring-business-process-security-policy-permissions"></a>Konfigurieren von Sicherheitsrichtlinienberechtigungen für Geschäftsprozesse

In diesem Schritt gewähren Sie der Sicherheitsgruppe Berechtigungen der Sicherheitsrichtlinien für Geschäftsprozesse für die Mitarbeiterdaten. Dieser Schritt ist für das Einrichten des Workday Writeback-App-Connectors erforderlich.

**So konfigurieren Sie Sicherheitsrichtlinienberechtigungen für Geschäftsprozesse**

1. Geben Sie **Business Process Policy** (Geschäftsprozessrichtlinie) in das Suchfeld ein, und klicken Sie dann auf den Link für die Aufgabe **Edit Business Process Security Policy** (Sicherheitsrichtlinie für Geschäftsprozesse bearbeiten).  

    ![Sicherheitsrichtlinien für Geschäftsprozesse](./media/workday-inbound-tutorial/wd_isu_12.png "Sicherheitsrichtlinien für Geschäftsprozesse")  

2. Suchen Sie im Textfeld **Business Process Type** (Geschäftsprozesstyp) nach *Contact* (Kontakt), wählen Sie den Geschäftsprozess **Contact Change** (Kontakt ändern) aus, und klicken Sie auf **OK**.

    ![Sicherheitsrichtlinien für Geschäftsprozesse](./media/workday-inbound-tutorial/wd_isu_13.png "Sicherheitsrichtlinien für Geschäftsprozesse")  

3. Scrollen Sie auf der Seite **Edit Business Process Security Policy** (Sicherheitsrichtlinien für Geschäftsprozesse bearbeiten) zum Abschnitt **Maintain Contact Information (Web Service)** (Kontaktinformationen verwalten (Webdienst)).

    ![Sicherheitsrichtlinien für Geschäftsprozesse](./media/workday-inbound-tutorial/wd_isu_14.png "Sicherheitsrichtlinien für Geschäftsprozesse")  

4. Wählen Sie die neue Sicherheitsgruppe des Integrationssystems aus, und fügen Sie sie der Liste der Sicherheitsgruppen hinzu, die Anforderungen an Webdienste initiieren können. Klicken Sie auf **Done** (Fertig). 

    ![Sicherheitsrichtlinien für Geschäftsprozesse](./media/workday-inbound-tutorial/wd_isu_15.png "Sicherheitsrichtlinien für Geschäftsprozesse")  

### <a name="activating-security-policy-changes"></a>Aktivieren von Sicherheitsrichtlinienänderungen

**So aktivieren Sie Sicherheitsrichtlinienänderungen**

1. Geben Sie „aktivieren“ in das Suchfeld ein, und klicken Sie dann auf den Link **Ausstehende Sicherheitsrichtlinienänderungen aktivieren**.

    ![Aktivieren](./media/workday-inbound-tutorial/wd_isu_16.png "Aktivieren")

1. Geben Sie zum Ausführen der Aufgabe „Ausstehende Sicherheitsrichtlinienänderungen aktivieren“ zunächst einen Kommentar für Überwachungszwecke ein, und klicken Sie dann auf die Schaltfläche **OK**.
1. Führen Sie die Aufgabe auf dem nächsten Bildschirm aus, indem Sie das Kontrollkästchen **Bestätigen** aktivieren und auf **OK** klicken.

    ![Ausstehende Sicherheitsrichtlinienänderungen aktivieren](./media/workday-inbound-tutorial/wd_isu_18.png "Ausstehende Sicherheitsrichtlinienänderungen aktivieren")  

## <a name="configuring-user-provisioning-from-workday-to-active-directory"></a>Konfiguration der Benutzerbereitstellung aus Workday in Active Directory

Dieser Abschnitt enthält die Schritte zum Konfigurieren der Bereitstellung von Benutzerkonten aus Workday in jeder Active Directory-Domäne im Geltungsbereich Ihrer Integration.

* [Installieren und Konfigurieren der lokalen Bereitstellungs-Agents](#part-1-install-and-configure-on-premises-provisioning-agents)
* [Hinzufügen der Bereitstellungsconnector-App und Herstellen der Verbindung mit Workday](#part-2-adding-the-provisioning-connector-app-and-creating-the-connection-to-workday)
* [Konfigurieren von Attributzuordnungen](#part-3-configure-attribute-mappings)
* [Aktivieren und Starten der Benutzerbereitstellung](#enable-and-launch-user-provisioning)

### <a name="part-1-install-and-configure-on-premises-provisioning-agents"></a>Teil 1: Installieren und Konfigurieren der lokalen Bereitstellungs-Agents

Um Active Directory lokal bereitzustellen, muss ein Agent auf einem Server installiert mit .NET Framework 4.7.1 oder höher und Netzwerkzugriff auf die gewünschten Active Directory-Domänen installiert werden.

> [!TIP]
> Sie können die Version von .NET Framework auf dem Server mithilfe der Anweisungen [hier](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) überprüfen.
> Wenn auf dem Server nicht .NET 4.7.1 oder höher installiert ist, können Sie es [hier](https://support.microsoft.com/help/4033342/the-net-framework-4-7-1-offline-installer-for-windows) herunterladen.  

Nachdem Sie .NET 4.7.1 oder höher bereitgestellt haben, können Sie den **[lokalen Bereitstellungs-Agent hier](https://go.microsoft.com/fwlink/?linkid=847801)** herunterladen. Führen Sie die folgenden Schritte aus, um die Agent-Konfiguration abzuschließen.

1. Melden Sie sich bei dem Windows Server an, auf dem Sie den neuen Agent installieren möchten.

1. Starten Sie den Installer für den Bereitstellungs-Agent, akzeptieren Sie die Bedingungen, und klicken Sie auf die Schaltfläche **Installieren**.

   ![Installationsbildschirm](./media/workday-inbound-tutorial/pa_install_screen_1.png "Installationsbildschirm")
   
1. Nachdem die Installation abgeschlossen ist, wird der Assistent gestartet, und der Bildschirm **Azure AD verbinden** wird angezeigt. Klicken Sie auf die Schaltfläche **Authentifizieren**, um eine Verbindung mit Ihrer Azure AD-Instanz herzustellen.

   ![Azure AD verbinden](./media/workday-inbound-tutorial/pa_install_screen_2.png "Azure AD verbinden")
   
1. Authentifizieren Sie Ihre Azure AD-Instanz mit den Anmeldeinformationen eines globalen Administrators.

   ![Administratorauthentifizierung](./media/workday-inbound-tutorial/pa_install_screen_3.png "Administratorauthentifizierung")

   > [!NOTE]
   > Die Azure AD-Administratoranmeldeinformationen dienen nur zur Herstellung einer Verbindung mit Ihrem Azure AD-Mandanten. Der Agent speichert die Anmeldeinformationen nicht lokal auf dem Server.

1. Nach der erfolgreichen Authentifizierung mit Azure AD wird der Bildschirm **Active Directory verbinden** angezeigt. In diesem Schritt geben Sie Ihren AD-Domänennamen an und klicken auf die Schaltfläche **Verzeichnis hinzufügen**.

   ![Verzeichnis hinzufügen](./media/workday-inbound-tutorial/pa_install_screen_4.png "Verzeichnis hinzufügen")
  
1. Sie werden daraufhin aufgefordert, die Anmeldeinformationen für die Verbindung mit der AD-Domäne einzugeben. Auf dem gleichen Bildschirm können Sie die **Domänencontrollerpriorität auswählen**, um Domänencontroller anzugeben, die der Agent zum Senden von Bereitstellungsanforderungen verwenden soll.

   ![Anmeldeinformationen für die Domäne](./media/workday-inbound-tutorial/pa_install_screen_5.png)
   
1. Nach dem Konfigurieren der Domäne zeigt der Installer eine Liste der konfigurierten Domänen an. Auf diesem Bildschirm können Sie Schritt 5 und 6 wiederholen, um weitere Domänen hinzuzufügen, oder auf **Weiter** klicken, um mit der Registrierung des Agents fortzufahren.

   ![Konfigurierte Domänen](./media/workday-inbound-tutorial/pa_install_screen_6.png "Konfigurierte Domänen")

   > [!NOTE]
   > Wenn Sie über mehrere AD-Domänen verfügen (z.B. na.contoso.com, emea.contoso.com), fügen Sie jede Domäne einzeln der Liste hinzu.
   > Es reicht nicht aus, nur die übergeordnete Domäne (z.B. „contoso.com“) hinzufügen. Sie müssen jede untergeordnete Domäne mit dem Agent registrieren.
   
1. Überprüfen Sie die Konfigurationsdetails, und klicken Sie auf **Bestätigen**, um den Agent zu registrieren.
  
   ![Bestätigungsbildschirm](./media/workday-inbound-tutorial/pa_install_screen_7.png "Bestätigungsbildschirm")
   
1. Der Konfigurations-Assistent zeigt den Fortschritt der Agent-Registrierung an.
  
   ![Agent-Registrierung](./media/workday-inbound-tutorial/pa_install_screen_8.png "Agent-Registrierung")
   
1. Nach der erfolgreichen Agent-Registrierung können Sie auf **Beenden** klicken, um den Assistenten zu beenden.
  
   ![Abschlussbildschirm](./media/workday-inbound-tutorial/pa_install_screen_9.png "Abschlussbildschirm")
   
1. Überprüfen Sie die Installation des Agents, und stellen Sie sicher, dass er ausgeführt wird, indem Sie das Snap-In „Dienste“ öffnen und nach dem Dienst mit der Bezeichnung „Microsoft Azure AD Connect Provisioning Agent“ suchen.
  
   ![Dienste](./media/workday-inbound-tutorial/services.png)

### <a name="part-2-adding-the-provisioning-connector-app-and-creating-the-connection-to-workday"></a>Teil 2: Hinzufügen der Bereitstellungsconnector-App und Herstellen der Verbindung mit Workday

**So konfigurieren Sie die Bereitstellung aus Workday in Active Directory**

1. Besuchen Sie <https://portal.azure.com>.

2. Wählen Sie auf der linken Navigationsleiste **Azure Active Directory** aus.

3. Klicken Sie auf **Unternehmensanwendungen** und dann auf **Alle Anwendungen**.

4. Klicken Sie auf **Anwendung hinzufügen**, und wählen Sie die Kategorie **Alle** aus.

5. Suchen Sie nach **Workday Provisioning to Active Directory**, und fügen Sie die App aus dem Katalog hinzu.

6. Sobald die App hinzugefügt wurde und der Bildschirm mit den App-Details angezeigt wird, wählen Sie **Bereitstellung** aus.

7. Legen Sie **Bereitstellungsmodus** **auf** **Automatisch** fest.

8. Vervollständigen Sie den Abschnitt **Administratoranmeldeinformationen** wie folgt:

   * **Administratorbenutzername**: Geben Sie den Benutzernamen des Workday-Systemintegrationskontos mit angefügtem Mandantendomänennamen ein. Es sollte etwa so aussehen: **benutzername\@mandantenname**

   * **Administratorkennwort**: Geben Sie das Kennwort des Workday-Systemintegrationskontos ein.

   * **Mandanten-URL**: Geben Sie die URL des Workday-Webdienstendpunkts für Ihren Mandanten ein. Dieser Wert sollte wie folgt lauten: https://wd3-impl-services1.workday.com/ccx/service/contoso4. Dabei wird *contoso4* durch den Namen Ihres Mandanten und *wd3-impl* durch die ordnungsgemäße Umgebungszeichenfolge ersetzt.

   * **Active Directory-Gesamtstruktur:** Der „Name“ Ihrer Active Directory-Domäne, mit dem diese beim Agent registriert wurde. Wählen Sie über die Dropdownliste die Zieldomäne für die Bereitstellung aus. Dieser Wert ist meist eine Zeichenfolge wie: *contoso.com*

   * **Active Directory-Container:** Geben Sie den DN des Containers an, in dem der Agent Benutzerkonten standardmäßig erstellen soll.
        Beispiel: *OU=Standard Users,OU=Users,DC=contoso,DC=test*
        
     > [!NOTE]
     > Diese Einstellung wird nur für die Benutzerkontoerstellung verwendet, wenn das Attribut *parentDistinguishedName* nicht in den Attributzuordnungen konfiguriert ist. Diese Einstellung wird nicht zum Suchen von Benutzern oder für Updatevorgänge verwendet. Der Suchvorgang schließt die gesamte Domänenteilstruktur ein.

   * **Benachrichtigungs-E-Mail**: Geben Sie Ihre E-Mail-Adresse ein, und aktivieren Sie das Kontrollkästchen „E-Mail senden, wenn Fehler auftritt“.

     > [!NOTE]
     > Der Azure AD-Bereitstellungsdienst sendet eine E-Mail-Benachrichtigung, wenn der Bereitstellungsauftrag in den Zustand [Quarantäne](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning#quarantine) wechselt.

   * Klicken Sie auf die Schaltfläche **Verbindung testen**. Wenn der Verbindungstest erfolgreich ist, klicken Sie oben auf die Schaltfläche **Speichern**. Überprüfen Sie bei einem Fehler, ob die Workday-Anmeldeinformationen und die AD-Anmeldeinformationen, die beim Einrichten des Agents angegeben wurden, gültig sind.

     ![Azure-Portal](./media/workday-inbound-tutorial/wd_1.png)

   * Sobald die Anmeldeinformationen erfolgreich gespeichert wurden, wird im Abschnitt **Zuordnungen** die Standardzuordnung **Workday-Worker mit lokalem Active Directory synchronisieren** angezeigt.

### <a name="part-3-configure-attribute-mappings"></a>Teil 3: Konfigurieren von Attributzuordnungen

In diesem Abschnitt konfigurieren Sie den Fluss von Benutzerdaten aus Workday in Active Directory.

1. Klicken Sie auf der Registerkarte „Bereitstellung“ unter **Zuordnungen** auf **Workday-Worker in lokalem Active Directory synchronisieren**.

1. Im Feld **Quellobjektbereich** können Sie die Benutzergruppen in Workday für die Bereitstellung in Active Directory auswählen, indem Sie verschiedene attributbasierte Filter definieren. Die Standardoption ist „Alle Benutzer in Workday“. Beispielfilter:

   * Beispiel: Auswählen der Benutzer mit Worker-IDs von 1000000 bis 2000000 (außer 2000000)

      * Attribut: WorkerID

      * Operator: REGEX Match

      * Wert: (1[0-9][0-9][0-9][0-9][0-9][0-9])

   * Beispiel: Nur Mitarbeiter und keine vorübergehend Beschäftigten

      * Attribut: EmployeeID

      * Operator: IS NOT NULL

   > [!TIP]
   > Wenn Sie die Bereitstellungs-App zum ersten Mal konfigurieren, müssen Sie Ihre Attributzuordnungen und Ausdrücke testen und überprüfen, um sicherzustellen, dass sie damit das gewünschte Ergebnis erzielen. Microsoft empfiehlt, die Bereichsfilter unter **Quellobjektbereich** zu verwenden, um Ihre Zuordnungen mit einigen Testbenutzern von Workday zu testen. Sobald Sie sich vergewissert haben, dass die Zuordnungen funktionieren, können Sie den Filter entweder entfernen oder schrittweise erweitern, um mehr Benutzer einzubinden.

1. Im Feld **Zielobjektaktionen** können Sie global filtern, welche Aktionen auf Active Directory angewendet werden. **Erstellen** und **Aktualisieren** erfolgen am häufigsten.

1. Im Abschnitt **Attributzuordnungen** können Sie definieren, wie einzelne Workday-Attribute Active Directory-Attributen zugeordnet werden.

1. Klicken Sie auf eine vorhandene Attributzuordnung, um sie zu aktualisieren. Oder klicken Sie am unteren Bildschirmrand auf **Neue Zuordnung hinzufügen**, um neue Zuordnungen hinzuzufügen. Eine einzelne Attributzuordnung unterstützt die folgenden Eigenschaften:

      * **Zuordnungstyp**

         * **Direkt**: Schreibt den Wert des Workday-Attributs unverändert in das AD-Attribut.

         * **Konstant**: Schreibt einen statischen, konstanten Zeichenfolgenwert in das AD-Attribut.

         * **Ausdruck**: Ermöglicht das Schreiben eines benutzerdefinierten Werts basierend auf einem oder mehreren Workday-Attributen in das AD-Attribut. [Weitere Informationen finden Sie im Artikel zu Ausdrücken](../manage-apps/functions-for-customizing-application-data.md).

      * **Quellattribut**: Das Benutzerattribut aus Workday. Wenn das gesuchte Attribut nicht vorhanden ist, finden Sie weitere Informationen unter [Anpassen der Liste der Workday-Benutzerattribute](#customizing-the-list-of-workday-user-attributes).

      * **Standardwert**: Optional. Wenn das Quellattribut einen leeren Wert aufweist, wird von der Zuordnung stattdessen dieser Wert geschrieben.
            Die meisten Konfigurationen sehen vor, dieses Feld leer zu lassen.

      * **Zielattribut**: Das Benutzerattribut in Active Directory.

      * **Objekte mit diesem Attribut abgleichen**: Gibt an, ob diese Zuordnung zum eindeutigen Bestimmen von Benutzern zwischen Workday und Active Directory verwendet werden soll. Dieser Wert wird meist auf das Feld „Worker-ID“ für Workday festgelegt, das in der Regel den „Mitarbeiter-ID“-Attributen in Active Directory zugeordnet wird.

      * **Rangfolge für Abgleich**: Es können mehrere Attribute für den Abgleich festgelegt werden. Falls mehrere vorhanden sind, werden sie entsprechend der in diesem Feld festgelegten Reihenfolge ausgewertet. Sobald eine Übereinstimmung gefunden wird, werden keine weiteren Attribute für den Abgleich mehr ausgewertet.

      * **Diese Zuordnung anwenden**

         * **Immer**: Wenden Sie diese Zuordnung sowohl bei der Aktion zum Erstellen eines Benutzers als auch bei der zum Aktualisieren eines Benutzers an.

         * **Nur während der Erstellung**: Wenden Sie diese Zuordnung nur bei der Aktion zum Erstellen eines Benutzers an.

1. Klicken Sie oben im Abschnitt „Attributzuordnung“ auf **Speichern**, um Ihre Zuordnungen zu speichern.

   ![Azure-Portal](./media/workday-inbound-tutorial/wd_2.png)

#### <a name="below-are-some-example-attribute-mappings-between-workday-and-active-directory-with-some-common-expressions"></a>Nachstehend finden Sie einige Beispiele für Attributzuordnungen zwischen Workday und Active Directory sowie einige häufig verwendete Ausdrücke.

* Der Ausdruck für die Zuordnung zum Attribut *parentDistinguishedName* wird verwendet, um Benutzer basierend auf mindestens einem Workday-Quellattribut in anderen Organisationseinheiten bereitzustellen. In diesem Beispiel werden Benutzer basierend auf dem Ort in verschiedenen Organisationseinheiten platziert.

* Das Attribut *userPrincipalName* im Active Directory wird mit der Deduplizierungsfunktion [SelectUniqueValue](../manage-apps/functions-for-customizing-application-data.md#selectuniquevalue) erzeugt, die das Vorhandensein eines generierten Wertes in der Ziel-AD-Domäne überprüft und ihn nur dann festlegt, wenn er eindeutig ist.  

* [Hier finden Sie Dokumentation zum Schreiben von Ausdrücken](../manage-apps/functions-for-customizing-application-data.md). Dieser Abschnitt enthält auch Beispiele zum Entfernen von Sonderzeichen.

| WORKDAY-ATTRIBUT | ACTIVE DIRECTORY-ATTRIBUT |  ÜBEREINSTIMMENDE ID? | ERSTELLEN/AKTUALISIEREN |
| ---------- | ---------- | ---------- | ---------- |
| **WorkerID**  |  EmployeeID | **Ja** | Wird nur bei der Erstellung geschrieben |
| **PreferredNameData**    |  cn    |   |   Wird nur bei der Erstellung geschrieben |
| **SelectUniqueValue( Join("\@", Join(".", \[FirstName\], \[LastName\]), "contoso.com"), Join("\@", Join(".", Mid(\[FirstName\], 1, 1), \[LastName\]), "contoso.com"), Join("\@", Join(".", Mid(\[FirstName\], 1, 2), \[LastName\]), "contoso.com"))**   | userPrincipalName     |     | Wird nur bei der Erstellung geschrieben 
| **Replace(Mid(Replace(\[UserID\], , "(\[\\\\/\\\\\\\\\\\\\[\\\\\]\\\\:\\\\;\\\\\|\\\\=\\\\,\\\\+\\\\\*\\\\?\\\\&lt;\\\\&gt;\])", , "", , ), 1, 20), , "([\\\\.)\*\$](file:///\\.)*$)", , "", , )**      |    sAMAccountName            |     |         Wird nur bei der Erstellung geschrieben |
| **Switch(\[Active\], , "0", "True", "1", "False")** |  accountDisabled      |     | Erstellen und aktualisieren |
| **Vorname**   | givenName       |     |    Erstellen und aktualisieren |
| **Nachname**   |   sn   |     |  Erstellen und aktualisieren |
| **PreferredNameData**  |  displayName |     |   Erstellen und aktualisieren |
| **Company**         | company   |     |  Erstellen und aktualisieren |
| **SupervisoryOrganization**  | department  |     |  Erstellen und aktualisieren |
| **ManagerReference**   | manager  |     |  Erstellen und aktualisieren |
| **BusinessTitle**   |  title     |     |  Erstellen und aktualisieren | 
| **AddressLineData**    |  streetAddress  |     |   Erstellen und aktualisieren |
| **Municipality**   |   l   |     | Erstellen und aktualisieren |
| **CountryReferenceTwoLetter**      |   co |     |   Erstellen und aktualisieren |
| **CountryReferenceTwoLetter**    |  c  |     |         Erstellen und aktualisieren |
| **CountryRegionReference** |  st     |     | Erstellen und aktualisieren |
| **WorkSpaceReference** | physicalDeliveryOfficeName    |     |  Erstellen und aktualisieren |
| **PostalCode**  |   postalCode  |     | Erstellen und aktualisieren |
| **PrimaryWorkTelephone**  |  telephoneNumber   |     | Erstellen und aktualisieren |
| **Fax**      | facsimileTelephoneNumber     |     |    Erstellen und aktualisieren |
| **Mobile**  |    mobile       |     |       Erstellen und aktualisieren |
| **LocalReference** |  preferredLanguage  |     |  Erstellen und aktualisieren |                                               
| **Switch(\[Municipality\], "OU=Standard Users,OU=Users,OU=Default,OU=Locations,DC=contoso,DC=com", "Dallas", "OU=Standard Users,OU=Users,OU=Dallas,OU=Locations,DC=contoso,DC=com", "Austin", "OU=Standard Users,OU=Users,OU=Austin,OU=Locations,DC=contoso,DC=com", "Seattle", "OU=Standard Users,OU=Users,OU=Seattle,OU=Locations,DC=contoso,DC=com", “London", "OU=Standard Users,OU=Users,OU=London,OU=Locations,DC=contoso,DC=com")**  | parentDistinguishedName     |     |  Erstellen und aktualisieren |

Sobald Ihre Attributzuordnungskonfiguration abgeschlossen ist, können Sie jetzt [den Benutzerbereitstellungsdienst aktivieren und starten](#enable-and-launch-user-provisioning).

## <a name="configuring-user-provisioning-to-azure-ad"></a>Konfigurieren der Benutzerbereitstellung in Azure AD

Die folgenden Abschnitte beschreiben Schritte zur Konfiguration der Benutzerbereitstellung von Workday zu Azure AD für reine Cloudbereitstellungen.

* [Hinzufügen der Bereitstellungsconnector-App für Azure AD und Herstellen der Verbindung mit Workday](#part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday)
* [Konfigurieren von Workday und Azure AD-Attributzuordnungen](#part-2-configure-workday-and-azure-ad-attribute-mappings)
* [Aktivieren und Starten der Benutzerbereitstellung](#enable-and-launch-user-provisioning)

> [!IMPORTANT]
> Führen Sie diese Schritte nur aus, wenn es reine Cloudbenutzer gibt, die in Azure AD und nicht in lokalem Active Directory bereitgestellt werden müssen.

### <a name="part-1-adding-the-azure-ad-provisioning-connector-app-and-creating-the-connection-to-workday"></a>Teil 1: Hinzufügen der Bereitstellungsconnector-App für Azure AD und Herstellen der Verbindung mit Workday

**So konfigurieren Sie die Workday-zu-Azure Active Directory-Bereitstellung für reine Cloudbenutzer**

1. Wechseln Sie zur Adresse <https://portal.azure.com>.

2. Wählen Sie auf der linken Navigationsleiste **Azure Active Directory** aus.

3. Klicken Sie auf **Unternehmensanwendungen** und dann auf **Alle Anwendungen**.

4. Klicken Sie auf **Anwendung hinzufügen**, und wählen Sie dann die Kategorie **Alle** aus.

5. Suchen Sie nach **Workday to Azure AD provisioning**, und fügen Sie die App aus dem Katalog hinzu.

6. Sobald die App hinzugefügt wurde und der Bildschirm mit den App-Details angezeigt wird, wählen Sie **Bereitstellung** aus.

7. Legen Sie **Bereitstellungsmodus** **auf** **Automatisch** fest.

8. Vervollständigen Sie den Abschnitt **Administratoranmeldeinformationen** wie folgt:

   * **Administratorbenutzername**: Geben Sie den Benutzernamen des Workday-Systemintegrationskontos mit angefügtem Mandantendomänennamen ein. Dies sollte ungefähr wie folgt aussehen: username@contoso4

   * **Administratorkennwort**: Geben Sie das Kennwort des Workday-Systemintegrationskontos ein.

   * **Mandanten-URL**: Geben Sie die URL des Workday-Webdienstendpunkts für Ihren Mandanten ein. Dieser Wert sollte wie folgt lauten: https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources. Dabei wird *contoso4* durch den Namen Ihres Mandanten und *wd3-impl* durch die ordnungsgemäße Umgebungszeichenfolge ersetzt. Ist diese URL nicht bekannt, erkundigen Sie sich bei Ihrem Workday-Integrationspartner oder bei einem Supportmitarbeiter nach der korrekten URL.

   * **Benachrichtigungs-E-Mail**: Geben Sie Ihre E-Mail-Adresse ein, und aktivieren Sie das Kontrollkästchen „E-Mail senden, wenn Fehler auftritt“.

   * Klicken Sie auf die Schaltfläche **Verbindung testen**.

   * Wenn der Verbindungstest erfolgreich ist, klicken Sie oben auf die Schaltfläche **Speichern**. Falls nicht, überprüfen Sie, ob die Workday-URL und -Anmeldeinformationen in Workday gültig sind.

### <a name="part-2-configure-workday-and-azure-ad-attribute-mappings"></a>Teil 2: Konfigurieren von Workday und Azure AD-Attributzuordnungen

In diesem Abschnitt konfigurieren Sie den Fluss von Benutzerdaten aus Workday in Azure Active Directory für reine Cloudbenutzer.

1. Klicken Sie auf der Registerkarte „Bereitstellung“ unter **Zuordnungen** auf **Mitarbeiter in Azure AD synchronisieren**.

2. Im Feld **Quellobjektbereich** können Sie die Benutzergruppen in Workday für die Bereitstellung in Azure Active Directory auswählen, indem Sie verschiedene attributbasierte Filter definieren. Der Standardbereich ist „Alle Benutzer in Workday“. Beispielfilter:

   * Beispiel: Auswählen der Benutzer mit Mitarbeiter-IDs von 1000000 bis 2000000

      * Attribut: WorkerID

      * Operator: REGEX Match

      * Wert: (1[0-9][0-9][0-9][0-9][0-9][0-9])

   * Beispiel: Nur vorübergehend Beschäftigte und keine regulären Mitarbeiter

      * Attribut: ContingentID

      * Operator: IS NOT NULL

3. Im Feld **Zielobjektaktionen** können Sie global filtern, welche Aktionen auf Azure AD angewendet werden. **Erstellen** und **Aktualisieren** erfolgen am häufigsten.

4. Im Abschnitt **Attributzuordnungen** können Sie definieren, wie einzelne Workday-Attribute Active Directory-Attributen zugeordnet werden.

5. Klicken Sie auf eine vorhandene Attributzuordnung, um sie zu aktualisieren. Oder klicken Sie am unteren Bildschirmrand auf **Neue Zuordnung hinzufügen**, um neue Zuordnungen hinzuzufügen. Eine einzelne Attributzuordnung unterstützt die folgenden Eigenschaften:

   * **Zuordnungstyp**

      * **Direkt**: Schreibt den Wert des Workday-Attributs unverändert in das AD-Attribut.

      * **Konstant**: Schreibt einen statischen, konstanten Zeichenfolgenwert in das AD-Attribut.

      * **Ausdruck**: Ermöglicht das Schreiben eines benutzerdefinierten Werts basierend auf einem oder mehreren Workday-Attributen in das AD-Attribut. [Weitere Informationen finden Sie im Artikel zu Ausdrücken](../manage-apps/functions-for-customizing-application-data.md).

   * **Quellattribut**: Das Benutzerattribut aus Workday. Wenn das gesuchte Attribut nicht vorhanden ist, finden Sie weitere Informationen unter [Anpassen der Liste der Workday-Benutzerattribute](#customizing-the-list-of-workday-user-attributes).

   * **Standardwert**: Optional. Wenn das Quellattribut einen leeren Wert aufweist, wird von der Zuordnung stattdessen dieser Wert geschrieben.
            Die meisten Konfigurationen sehen vor, dieses Feld leer zu lassen.

   * **Zielattribut**: Das Benutzerattribut in Azure AD.

   * **Objekte mit diesem Attribut abgleichen**: Gibt an, ob dieses Attribut zum eindeutigen Bestimmen von Benutzern zwischen Workday und Azure AD verwendet werden soll. Diese Wert wird meist auf das Feld „Worker-ID“ für Workday festgelegt, das in der Regel dem „Worker-ID“-Attribut (neu) oder einem Erweiterungsattribut in Azure AD zugeordnet wird.

   * **Rangfolge für Abgleich**: Es können mehrere Attribute für den Abgleich festgelegt werden. Falls mehrere vorhanden sind, werden sie entsprechend der in diesem Feld festgelegten Reihenfolge ausgewertet. Sobald eine Übereinstimmung gefunden wird, werden keine weiteren Attribute für den Abgleich mehr ausgewertet.

   * **Diese Zuordnung anwenden**

     * **Immer**: Wenden Sie diese Zuordnung sowohl bei der Aktion zum Erstellen eines Benutzers als auch bei der zum Aktualisieren eines Benutzers an.

     * **Nur während der Erstellung**: Wenden Sie diese Zuordnung nur bei der Aktion zum Erstellen eines Benutzers an.

6. Klicken Sie oben im Abschnitt „Attributzuordnung“ auf **Speichern**, um Ihre Zuordnungen zu speichern.

Sobald Ihre Attributzuordnungskonfiguration abgeschlossen ist, können Sie jetzt [den Benutzerbereitstellungsdienst aktivieren und starten](#enable-and-launch-user-provisioning).

## <a name="configuring-azure-ad-attribute-writeback-to-workday"></a>Konfigurieren der Azure AD-Attributzurückschreibung in Workday

Befolgen Sie diese Anweisungen, um die Zurückschreibung der E-Mail-Adressen und Benutzernamen der Benutzer aus Azure Active Directory in Workday zu konfigurieren.

* [Hinzufügen der Writeback-Connector-App und Herstellen der Verbindung mit Workday](#part-1-adding-the-writeback-connector-app-and-creating-the-connection-to-workday)
* [Konfigurieren von Writeback-Attributzuordnungen](#part-2-configure-writeback-attribute-mappings)
* [Aktivieren und Starten der Benutzerbereitstellung](#enable-and-launch-user-provisioning)

### <a name="part-1-adding-the-writeback-connector-app-and-creating-the-connection-to-workday"></a>Teil 1: Hinzufügen der Writeback-Connector-App und Herstellen der Verbindung mit Workday

**So konfigurieren Sie den Workday Writeback-Connector**

1. Besuchen Sie <https://portal.azure.com>.

2. Wählen Sie auf der linken Navigationsleiste **Azure Active Directory** aus.

3. Klicken Sie auf **Unternehmensanwendungen** und dann auf **Alle Anwendungen**.

4. Klicken Sie auf **Anwendung hinzufügen**, und wählen Sie dann die Kategorie **Alle** aus.

5. Suchen Sie nach **Workday Writeback**, und fügen Sie die App aus dem Katalog hinzu.

6. Sobald die App hinzugefügt wurde und der Bildschirm mit den App-Details angezeigt wird, wählen Sie **Bereitstellung** aus.

7. Legen Sie **Bereitstellungsmodus** **auf** **Automatisch** fest.

8. Vervollständigen Sie den Abschnitt **Administratoranmeldeinformationen** wie folgt:

   * **Administratorbenutzername**: Geben Sie den Benutzernamen des Workday-Systemintegrationskontos mit angefügtem Mandantendomänennamen ein. Sollte etwa so aussehen: *benutzername\@contoso4*

   * **Administratorkennwort**: Geben Sie das Kennwort des Workday-Systemintegrationskontos ein.

   * **Mandanten-URL**: Geben Sie die URL des Workday-Webdienstendpunkts für Ihren Mandanten ein. Dieser Wert sollte wie folgt lauten: https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources. Dabei wird *contoso4* durch den Namen Ihres Mandanten und *wd3-impl* (bei Bedarf) durch die ordnungsgemäße Umgebungszeichenfolge ersetzt.

   * **Benachrichtigungs-E-Mail**: Geben Sie Ihre E-Mail-Adresse ein, und aktivieren Sie das Kontrollkästchen „E-Mail senden, wenn Fehler auftritt“.

   * Klicken Sie auf die Schaltfläche **Verbindung testen**. Wenn der Verbindungstest erfolgreich ist, klicken Sie oben auf die Schaltfläche **Speichern**. Falls nicht, überprüfen Sie, ob die Workday-URL und -Anmeldeinformationen in Workday gültig sind.

### <a name="part-2-configure-writeback-attribute-mappings"></a>Teil 2: Konfigurieren von Writeback-Attributzuordnungen

In diesem Abschnitt werden Sie konfigurieren, wie die Writebackattribute von Azure AD zu Workday übertragen werden. Derzeit unterstützt der Connector nur die Zurückschreibung von E-Mail-Adressen und Benutzernamen in Workday.

1. Klicken Sie auf der Registerkarte „Bereitstellung“ unter **Zuordnungen** auf **Azure Active Directory-Benutzer in Workday synchronisieren**.

2. Im Feld **Quellobjektbereich** können Sie optional filtern, für welche Gruppen von Benutzern in Azure Active Directory E-Mail-Adressen in Workday zurückgeschrieben werden sollen. Die Standardoption ist „Alle Benutzer in Azure AD“.

3. Aktualisieren Sie im Abschnitt **Attributzuordnungen** die entsprechende ID, um das Attribut in Azure Active Directory anzugeben, in dem die Mitarbeiter-ID von Workday gespeichert ist. Eine gängige Methode für den Abgleich ist das Synchronisieren der Mitarbeiter-ID von Workday mit „extensionAttribute“ 1-15 in Azure AD und das anschließende Verwenden dieses Attributs in Azure AD, um die Benutzer wieder mit Workday abzugleichen.

4. In der Regel ordnen Sie das Azure AD-Attribut *userPrincipalName* dem Workday-Attribut *UserID* und das Azure AD-Attribut *mail* dem Workday-Attribut *EmailAddress* zu. Klicken Sie oben im Abschnitt „Attributzuordnung“ auf **Speichern**, um Ihre Zuordnungen zu speichern.

Sobald Ihre Attributzuordnungskonfiguration abgeschlossen ist, können Sie jetzt [den Benutzerbereitstellungsdienst aktivieren und starten](#enable-and-launch-user-provisioning).

## <a name="enable-and-launch-user-provisioning"></a>Aktivieren und Starten der Benutzerbereitstellung

Nachdem die Konfiguration der Workday-Bereitstellungs-App abgeschlossen ist, können Sie den Bereitstellungsdienst im Azure-Portal aktivieren.

> [!TIP]
> Beim Aktivieren des Bereitstellungsdiensts werden Bereitstellungsvorgänge für alle Benutzer im Bereich initiiert. Wenn Fehler in der Zuordnung oder Workday-Datenprobleme auftreten, kann der Bereitstellungsauftrag fehlschlagen und in den Quarantänezustand wechseln. Um dies zu vermeiden, empfehlen wir, den Filter **Quellobjektbereich** zu konfigurieren und Ihre Attributzuordnungen mit einigen Testbenutzern zu testen, bevor Sie die vollständige Synchronisierung für alle Benutzer starten. Sobald Sie sich vergewissert haben, dass die Zuordnungen funktionieren und Sie die gewünschten Ergebnisse erhalten, können Sie den Filter entweder entfernen oder schrittweise erweitern, um mehr Benutzer einzubinden.

1. Legen Sie auf der Registerkarte **Bereitstellung** die Einstellung **Bereitstellungsstatus** auf **Ein** fest.

2. Klicken Sie auf **Speichern**.

3. Dieser Vorgang startet die erste Synchronisierung, die abhängig von der Anzahl der Benutzer im Workday-Mandanten eine variable Anzahl von Stunden dauern kann. 

4. Im Azure-Portal können Sie sich auf der Registerkarte **Überwachungsprotokolle** jederzeit ansehen, welche Aktionen der Bereitstellungsdienst ausgeführt hat. Die Überwachungsprotokolle enthalten alle einzelnen Synchronisierungsereignisse des Bereitstellungsdiensts – beispielsweise, welche Benutzer in Workday gelesen und anschließend Active Directory hinzugefügt oder dort aktualisiert wurden. Im Abschnitt „Problembehandlung“ finden Sie Anweisungen, wie Sie die Überwachungsprotokolle überprüfen und Bereitstellungsfehler beheben können.

5. Nach Abschluss der ersten Synchronisierung wird auf der Registerkarte **Bereitstellung** ein Überwachungszusammenfassungsbericht ausgegeben:

   ![Azure-Portal](./media/workday-inbound-tutorial/wd_3.png)

## <a name="frequently-asked-questions-faq"></a>Häufig gestellte Fragen (FAQ)

* **Fragen zur Lösungsfunktion**
  * [Wie legt die Lösung bei der Bearbeitung einer Neueinstellung aus Workday das Kennwort für das neue Benutzerkonto in Active Directory fest?](#when-processing-a-new-hire-from-workday-how-does-the-solution-set-the-password-for-the-new-user-account-in-active-directory)
  * [Unterstützt die Lösung das Senden von E-Mail-Benachrichtigungen nach Abschluss des Bereitstellungsvorgangs?](#does-the-solution-support-sending-email-notifications-after-provisioning-operations-complete)
  * [Wie kann ich die Zustellung von Kennwörtern für Neueinstellungen verwalten und sicher einen Mechanismus zum Zurücksetzen des Kennworts bereitstellen?](#how-do-i-manage-delivery-of-passwords-for-new-hires-and-securely-provide-a-mechanism-to-reset-their-password)
  * [Speichert die Lösung Workday-Benutzerprofile in der Azure AD Cloud oder auf der Ebene des Bereitstellungs-Agents zwischen?](#does-the-solution-cache-workday-user-profiles-in-the-azure-ad-cloud-or-at-the-provisioning-agent-layer)
  * [Unterstützt die Lösung die Zuweisung von lokalen AD-Gruppen an den Benutzer?](#does-the-solution-support-assigning-on-premises-ad-groups-to-the-user)
  * [Welche Workday-APIs verwendet die Lösung, um Workday-Workerprofile abzufragen und zu aktualisieren?](#which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles)
  * [Kann ich meinen Workday-HCM-Mandanten mit zwei Azure AD-Mandanten konfigurieren?](#can-i-configure-my-workday-hcm-tenant-with-two-azure-ad-tenants)
  * [Warum wird die „Workday to Azure AD“-Benutzerbereitstellungs-App nicht unterstützt, wenn wir Azure AD Connect implementiert haben?](#why-workday-to-azure-ad-user-provisioning-app-is-not-supported-if-we-have-deployed-azure-ad-connect)
  * [Wie kann ich Verbesserungen vorschlagen oder neue Funktionen im Zusammenhang mit der Workday- und Azure AD-Integration anfordern?](#how-do-i-suggest-improvements-or-request-new-features-related-to-workday-and-azure-ad-integration)

* **Fragen zum Bereitstellungs-Agent**
  * [Wie lautet die allgemein verfügbare Version des Bereitstellungs-Agents?](#what-is-the-ga-version-of-the-provisioning-agent)
  * [Wie kann ich die Version meines Bereitstellungs-Agents ermitteln?](#how-do-i-know-the-version-of-my-provisioning-agent)
  * [Überträgt Microsoft automatisch Updates des Bereitstellungs-Agents per Push?](#does-microsoft-automatically-push-provisioning-agent-updates)
  * [Kann ich den Bereitstellungs-Agent auf demselben Server installieren, auf dem AAD Connect ausgeführt wird?](#can-i-install-the-provisioning-agent-on-the-same-server-running-aad-connect)
  * [Wie konfiguriere ich den Bereitstellungs-Agent, damit dieser einen Proxyserver für die ausgehende HTTP-Kommunikation verwendet?](#how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication)
  * [Wie stelle ich sicher, dass der Bereitstellungs-Agent mit dem Azure AD-Mandanten kommunizieren kann, und dass keine Firewalls die vom Agenten benötigten Ports blockieren?](#how-do-i-ensure-that-the-provisioning-agent-is-able-to-communicate-with-the-azure-ad-tenant-and-no-firewalls-are-blocking-ports-required-by-the-agent)
  * [Wie hebe ich die Registrierung der meinem Bereitstellungs-Agent zugeordneten Domäne wieder auf?](#how-do-i-de-register-the-domain-associated-with-my-provisioning-agent)
  * [Wie deinstalliere ich den Bereitstellungs-Agent?](#how-do-i-uninstall-the-provisioning-agent)
  
* **Fragen zur Zuordnung und Konfiguration des „Workday to AD“-Attributs**
  * [Wie kann ich eine Arbeitskopie der Zuordnung und des Schemas meines Workday-Bereitstellungsattributs sichern und exportieren?](#how-do-i-back-up-or-export-a-working-copy-of-my-workday-provisioning-attribute-mapping-and-schema)
  * [Ich habe benutzerdefinierte Attribute in Workday und Active Directory. Wie konfiguriere ich die Lösung, damit diese mit meinen benutzerdefinierten Attributen arbeitet?](#i-have-custom-attributes-in-workday-and-active-directory-how-do-i-configure-the-solution-to-work-with-my-custom-attributes)
  * [Kann ich Benutzerfotos aus Workday für Active Directory bereitstellen?](#can-i-provision-users-photo-from-workday-to-active-directory)
  * [Wie synchronisieren ich Mobilfunknummern aus Workday basierend auf der Benutzereinwilligung für die öffentliche Verwendung?](#how-do-i-sync-mobile-numbers-from-workday-based-on-user-consent-for-public-usage)
  * [Wie formatiere ich Anzeigenamen in AD basierend auf den Attributen für Abteilung/Land/Stadt des Benutzers und wie gehe mit regionalen Abweichungen um?](#how-do-i-format-display-names-in-ad-based-on-the-users-departmentcountrycity-attributes-and-handle-regional-variances)
  * [Wie kann ich mit „SelectUniqueValue eindeutige Werte für das Attribut „samAccountName“ generieren?](#how-can-i-use-selectuniquevalue-to-generate-unique-values-for-samaccountname-attribute)
  * [Wie entferne ich Zeichen mit diakritischen Zeichen und konvertiere sie in normale englische Schriftzeichen?](#how-do-i-remove-characters-with-diacritics-and-convert-them-into-normal-english-alphabets)

### <a name="solution-capability-questions"></a>Fragen zur Lösungsfunktion

#### <a name="when-processing-a-new-hire-from-workday-how-does-the-solution-set-the-password-for-the-new-user-account-in-active-directory"></a>Wie legt die Lösung bei der Bearbeitung einer Neueinstellung aus Workday das Kennwort für das neue Benutzerkonto in Active Directory fest?

Wenn der lokale Bereitstellungs-Agent eine Anforderung zur Erstellung eines neuen AD-Kontos erhält, generiert er automatisch ein komplexes Zufallskennwort, das den vom AD-Server definierten Anforderungen an die Komplexität des Kennworts entspricht, und legt dieses auf das Benutzerobjekt fest. Dieses Kennwort wird nirgendwo protokolliert.

#### <a name="does-the-solution-support-sending-email-notifications-after-provisioning-operations-complete"></a>Unterstützt die Lösung das Senden von E-Mail-Benachrichtigungen nach Abschluss des Bereitstellungsvorgangs?

Nein, das Senden von E-Mail-Benachrichtigungen nach Abschluss der Bereitstellungsvorgänge wird in der aktuellen Version nicht unterstützt.

#### <a name="how-do-i-manage-delivery-of-passwords-for-new-hires-and-securely-provide-a-mechanism-to-reset-their-password"></a>Wie kann ich die Zustellung von Kennwörtern für Neueinstellungen verwalten und sicher einen Mechanismus zum Zurücksetzen des Kennworts bereitstellen?

Einer der letzten Schritte bei der Bereitstellung eines neuen AD-Kontos ist die Bereitstellung des temporären Kennworts, das dem AD-Konto des Benutzers zugewiesen wurde. Viele Unternehmen verwenden immer noch den traditionellen Ansatz, bei dem das temporäre Kennwort an den Manager des Benutzers übermittelt wird, der dann das Kennwort an den neuen Mitarbeiter übergibt. Dieser Prozess hat einen inhärenten Sicherheitsfehler und es gibt eine Option, um einen besseren Ansatz mit Hilfe der Azure AD-Funktionen zu implementieren.

Im Rahmen des Einstellungsprozesses führen die HR-Teams in der Regel eine Hintergrundüberprüfung durch und überprüfen die Mobilfunknummer des neuen Mitarbeiters. Mit der Integration der „Workday to AD“-Benutzerbereitstellungs-App können Sie darauf aufbauen und bereits am ersten Tag eine Funktion für die Self-Service-Kennwortzurücksetzung für den Benutzer einführen. Dies erfolgt durch die Weitergabe des Attributs „Mobil Number“ des neuen Mitarbeiters von Workday zu AD und dann von AD zu Azure AD über AAD Connect. Sobald dieses Attribut in Azure AD vorhanden ist, können Sie die [Self-Service-Kennwortzurücksetzung (Self-Service Password Reset, SSPR)](../authentication/howto-sspr-authenticationdata.md) für das Konto des Benutzers aktivieren, sodass ein neuer Mitarbeiter am ersten Tag die registrierte und verifizierte Mobilfunknummer zur Authentifizierung verwenden kann.

#### <a name="does-the-solution-cache-workday-user-profiles-in-the-azure-ad-cloud-or-at-the-provisioning-agent-layer"></a>Speichert die Lösung Workday-Benutzerprofile in der Azure AD Cloud oder auf der Ebene des Bereitstellungs-Agents zwischen?

Nein, in der Lösung werden keine Benutzerprofile zwischengespeichert. Der Azure AD-Bereitstellungsdienst fungiert einfach als Datenprozessor, liest Daten von Workday und schreibt in das Active Directory oder Azure AD. Details zum Datenschutz und der Datenaufbewahrung finden Sie unter [Verwalten personenbezogener Daten](#managing-personal-data).

#### <a name="does-the-solution-support-assigning-on-premises-ad-groups-to-the-user"></a>Unterstützt die Lösung die Zuweisung von lokalen AD-Gruppen an den Benutzer?

Diese Funktion wird derzeit nicht unterstützt. Eine empfohlene Problemumgehung besteht darin, ein PowerShell-Skript bereitzustellen, das im Azure AD-Graph-API-Endpunkt Überwachungsprotokolldaten abfragt und diese verwendet, um Szenarien wie die Gruppenzuweisung auszulösen. Dieses PowerShell-Skript kann an einen Taskplaner angefügt und auf demselben Computer mit dem Bereitstellungs-Agent bereitgestellt werden.  

#### <a name="which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles"></a>Welche Workday-APIs verwendet die Lösung, um Workday-Workerprofile abzufragen und zu aktualisieren?

Die Lösung verwendet derzeit die folgenden Workday-APIs:

* Get_Workers (v21.1) zum Abrufen von Workerinformationen
* Maintain_Contact_Information (v26.1) für die Writeback-Funktion für geschäftliche E-Mails
* Update_Workday_Account (v31.2) für das Feature zum Zurückschreiben von Benutzernamen

#### <a name="can-i-configure-my-workday-hcm-tenant-with-two-azure-ad-tenants"></a>Kann ich meinen Workday-HCM-Mandanten mit zwei Azure AD-Mandanten konfigurieren?

Ja, diese Konfiguration wird unterstützt. Hier sind die allgemeinen Schritte zum Konfigurieren dieses Szenarios:

* Bereitstellen des Bereitstellungs-Agent 1, und registrieren in Azure AD-Mandanten 1.
* Bereitstellen des Bereitstellungs-Agent 2, und registrieren in Azure AD-Mandanten 2.
* Konfigurieren Sie die einzelnen Agents mit der/den Domäne(n) basierend auf den „untergeordneten Domänen“, die jeder Bereitstellungs-Agent verwalten wird. Ein Agent kann mehrere Domänen verarbeiten.
* Richten Sie im Azure-Portal für jeden Mandanten die „Workday to AD“-Benutzerbereitstellungs-App ein und konfigurieren Sie sie mit den entsprechenden Domänen.

#### <a name="why-workday-to-azure-ad-user-provisioning-app-is-not-supported-if-we-have-deployed-azure-ad-connect"></a>Warum wird die „Workday to Azure AD“-Benutzerbereitstellungs-App nicht unterstützt, wenn wir Azure AD Connect implementiert haben?

Wenn Azure AD im Hybridmodus verwendet wird (mit einer Mischung aus Cloud- + lokalen Benutzern), ist es wichtig, eine klare Definition der „Autoritätsquelle“ zu haben. Hybridszenarien erfordern in der Regel die Bereitstellung von Azure AD Connect. Wenn Azure AD Connect bereitgestellt wird, ist das lokale AD die Autoritätsquelle. Die Einführung des Workday to Azure AD-Connectors kann dazu führen, dass Workday-Attributwerte die von Azure AD Connect eingestellten Werte möglicherweise überschreiben. Daher wird die Verwendung der „Workday to Azure AD“-Benutzerbereitstellungs-App nicht unterstützt, wenn Azure AD Connect aktiviert ist? In derartigen Situationen empfehlen wir die Verwendung der „Workday to AD“-Benutzerbereitstellungs-App, um Benutzer in das lokale AD einzubinden, und sie anschließend in Azure AD mit Azure AD Connect zu synchronisieren.

#### <a name="how-do-i-suggest-improvements-or-request-new-features-related-to-workday-and-azure-ad-integration"></a>Wie kann ich Verbesserungen vorschlagen oder neue Funktionen im Zusammenhang mit der Workday- und Azure AD-Integration anfordern?

Ihr Feedback ist uns sehr wichtig, da es uns hilft, die Weichen für die zukünftigen Releases und Verbesserungen zu stellen. Wir freuen uns über jedes Feedback und ermutigen Sie, Ihre Ideen oder Verbesserungsvorschläge im [Feedbackforum von Azure AD](https://feedback.azure.com/forums/169401-azure-active-directory) einzureichen. Für spezifisches Feedback im Zusammenhang mit der Workday-Integration wählen Sie die Kategorie *SaaS-Anwendungen* und suchen Sie anhand des Stichworts *Workday*, um vorhandenes Feedback zum Workday zu finden.

![UserVoice-SaaS-Apps](media/workday-inbound-tutorial/uservoice_saas_apps.png)

![UserVoice-Workday](media/workday-inbound-tutorial/uservoice_workday_feedback.png)

Wenn Sie eine neue Idee vorschlagen, überprüfen Sie bitte, ob eine ähnliche Funktion vorgeschlagen wurde. In diesem Fall können Sie die Funktion oder die Ergänzungsanforderung bewerten. Sie können auch einen Kommentar zu Ihrem spezifischen Anwendungsfall hinterlassen, um Ihre Unterstützung für die Idee zu zeigen und zu veranschaulichen, wie die Funktion auch für Sie wertvoll ist.

### <a name="provisioning-agent-questions"></a>Fragen zum Bereitstellungs-Agent

#### <a name="what-is-the-ga-version-of-the-provisioning-agent"></a>Wie lautet die allgemein verfügbare Version des Bereitstellungs-Agents?

* Die allgemein verfügbare Version des Bereitstellungs-Agents ist 1.1.30 und höher.
* Wenn Ihre Agent-Version kleiner als 1.1.30 ist, führen Sie die öffentliche Vorschauversion aus und sie wird automatisch auf die allgemein verfügbare Version aktualisiert, wenn sich auf dem Server, auf dem der Agent gehostet wird, .NET 4.7.1 Runtime befindet.
  * Sie können [überprüfen, welche .NET-Version](https://docs.microsoft.com/dotnet/framework/migration-guide/how-to-determine-which-versions-are-installed) auf Ihrem Server installiert ist. Wenn auf Ihrem Server nicht .NET 4.7.1 ausgeführt wird, [können Sie es herunterladen und installieren](https://support.microsoft.com/help/4033342/the-net-framework-4-7-1-offline-installer-for-windows). Nach der Installation von .NET 4.7.1 wird der Bereitstellungs-Agent automatisch auf die allgemein verfügbare Version aktualisiert.

#### <a name="how-do-i-know-the-version-of-my-provisioning-agent"></a>Wie kann ich die Version meines Bereitstellungs-Agents ermitteln?

* Melden Sie sich bei dem Windows Server an, auf dem der Bereitstellungs-Agent installiert ist.
* Wechseln Sie zu **Systemsteuerung** -> **Deinstallieren oder Ändern eines Programms**.
* Suchen Sie nach der Version für den Eintrag **Microsoft Azure AD Connect-Bereitstellungs-Agent**.

  ![Azure-Portal](./media/workday-inbound-tutorial/pa_version.png)

#### <a name="does-microsoft-automatically-push-provisioning-agent-updates"></a>Überträgt Microsoft automatisch Updates des Bereitstellungs-Agents per Push?

Ja, Microsoft aktualisiert den Bereitstellungs-Agent automatisch. Sie können automatische Updates deaktivieren, indem Sie den Windows-Dienst **Microsoft Azure AD Connect Agent Updater** beenden.

#### <a name="can-i-install-the-provisioning-agent-on-the-same-server-running-aad-connect"></a>Kann ich den Bereitstellungs-Agent auf demselben Server installieren, auf dem AAD Connect ausgeführt wird?

Ja, Sie können den Bereitstellungs-Agent auf demselben Server installieren, auf dem AAD Connect ausgeführt wird?

#### <a name="at-the-time-of-configuration-the-provisioning-agent-prompts-for-azure-ad-admin-credentials-does-the-agent-store-the-credentials-locally-on-the-server"></a>Bei der Konfiguration fordert der Bereitstellungs-Agent die Azure AD-Administratoranmeldeinformationen an. Speichert der Agent die Anmeldeinformationen lokal auf dem Server?

Bei der Konfiguration fordert der Bereitstellungs-Agent die Azure AD-Administratoranmeldeinformationen nur an, um eine Verbindung zum Azure AD-Mandanten herzustellen. Die Anmeldeinformationen werden nicht lokal auf dem Server gespeichert. Die Anmeldeinformationen für die Verbindung mit der lokalen *Active Directory-Domäne* werden jedoch in einem lokalen Windows-Kennworttresor aufbewahrt.

#### <a name="how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication"></a>Wie konfiguriere ich den Bereitstellungs-Agent, damit dieser einen Proxyserver für die ausgehende HTTP-Kommunikation verwendet?

Der Agent-Bereitstellung unterstützt die Verwendung von Proxys für ausgehenden Datenverkehr. Dies können Sie durch das Konfigurieren der Konfigurationsdatei des Agent **C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\AADConnectProvisioningAgent.exe.config** konfigurieren. Fügen Sie die folgenden Zeilen zum Ende der Datei unmittelbar vor dem `</configuration>`-Tag ein.
Ersetzen Sie der Variablen [[proxy-server] ] und [proxy-port] durch den Namen Ihres Proxyservers und die Portwerte.

```xml
    <system.net>
          <defaultProxy enabled="true" useDefaultCredentials="true">
             <proxy
                usesystemdefault="true"
                proxyaddress="http://[proxy-server]:[proxy-port]"
                bypassonlocal="true"
             />
         </defaultProxy>
    </system.net>
```

#### <a name="how-do-i-ensure-that-the-provisioning-agent-is-able-to-communicate-with-the-azure-ad-tenant-and-no-firewalls-are-blocking-ports-required-by-the-agent"></a>Wie stelle ich sicher, dass der Bereitstellungs-Agent mit dem Azure AD-Mandanten kommunizieren kann, und dass keine Firewalls die vom Agenten benötigten Ports blockieren?

Sie können auch überprüfen, ob alle erforderlichen Ports geöffnet sind, indem Sie das [Testtool für Connectorports](https://aadap-portcheck.connectorporttest.msappproxy.net/) über Ihr lokales Netzwerk öffnen. Eine größere Anzahl grüner Häkchen bedeutet eine größere Resilienz.

Stellen Sie Folgendes sicher, damit Ihnen das Tool die richtigen Ergebnisse liefert:

* Öffnen Sie das Tool auf einem Server, auf dem Sie den Bereitstellungs-Agent installiert haben, in einem Browser.
* Stellen Sie sicher, dass alle Proxys oder Firewalls, die für den Bereitstellungs-Agent relevant sind, auch auf diese Seite angewendet werden. Navigieren Sie dazu in Internet Explorer zu **Einstellungen > Internetoptionen > Verbindungen > LAN-Einstellungen**. Auf dieser Seite wird das Feld „Proxyserver für das LAN verwenden“ angezeigt. Aktivieren Sie dieses Kontrollkästchen, und fügen Sie die Proxyadresse in das Feld „Adresse“ ein.

#### <a name="can-one-provisioning-agent-be-configured-to-provision-multiple-ad-domains"></a>Kann ein Bereitstellungs-Agent für die Bereitstellung mehrerer AD-Domänen konfiguriert werden?

Ja, ein Bereitstellungs-Agent kann konfiguriert werden, um mehrere AD-Domänen zu verwalten, solange der Agent uneingeschränkten Zugriff auf den jeweiligen Domänencontrollern hat. Microsoft empfiehlt die Einrichtung einer Gruppe von 3 Bereitstellungs-Agents, die denselben Satz von AD-Domänen bedienen, um Hochverfügbarkeit und Failover-Support zu gewährleisten.

#### <a name="how-do-i-de-register-the-domain-associated-with-my-provisioning-agent"></a>Wie hebe ich die Registrierung der meinem Bereitstellungs-Agent zugeordneten Domäne wieder auf?

* Rufen Sie über das Azure-Portal die *Mandanten-ID* Ihres Azure AD-Mandanten ab.
* Melden Sie sich bei dem Windows Server an, auf dem der Bereitstellungs-Agent ausgeführt wird.
* Öffnen Sie PowerShell als Windows-Administrator.
* Wechseln Sie in das Verzeichnis mit den Registrierungsskripten und führen Sie die folgenden Befehle aus, um den Parameter \[tenant ID\] durch den Wert Ihrer Mandanten-ID zu ersetzen.

  ```powershell
  cd “C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\RegistrationPowershell\Modules\PSModulesFolder”
  Import-Module "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\RegistrationPowershell\Modules\PSModulesFolder\AppProxyPSModule.psd1"
  Get-PublishedResources -TenantId "[tenant ID]"
  ```

* Kopieren Sie den Wert des Feldes „id“ aus der Liste der angezeigten Agents aus der Ressource, deren *resourceName* Ihrem AD-Domänennamen entspricht.
* Fügen Sie den ID-Wert in den folgenden Befehl ein, und führen sie ihn in PowerShell aus.

  ```powershell
  Remove-PublishedResource -ResourceId "[resource ID]" -TenantId "[tenant ID]"
  ```

* Führen Sie den Assistenten für die Agent-Konfiguration erneut aus.
* Alle anderen Agents, die zuvor dieser Domäne zugeordnet waren, müssen neu konfiguriert werden.

#### <a name="how-do-i-uninstall-the-provisioning-agent"></a>Wie deinstalliere ich den Bereitstellungs-Agent?

* Melden Sie sich bei dem Windows Server an, auf dem der Bereitstellungs-Agent installiert ist.
* Wechseln Sie zu **Systemsteuerung** -> **Deinstallieren oder Ändern eines Programms**.
* Deinstallieren Sie die folgenden Programme:
  * Microsoft Azure AD Connect-Bereitstellungs-Agent
  * Microsoft Azure AD Connect Agent Updater
  * Microsoft Azure AD Connect-Bereitstellungs-Agent-Paket

### <a name="workday-to-ad-attribute-mapping-and-configuration-questions"></a>Fragen zur Zuordnung und Konfiguration des „Workday to AD“-Attributs

#### <a name="how-do-i-back-up-or-export-a-working-copy-of-my-workday-provisioning-attribute-mapping-and-schema"></a>Wie kann ich eine Arbeitskopie der Zuordnung und des Schemas meines Workday-Bereitstellungsattributs sichern und exportieren?

Sie können die Microsoft Graph-API verwenden, um Ihre Workday-Benutzerbereitstellungs-Konfiguration zu exportieren. Weitere Informationen finden Sie in den Schritten im Abschnitt [Exportieren und Importieren Ihrer Konfiguration für die Workday-Benutzerbereitstellungs-Attributzuordnung](#exporting-and-importing-your-configuration).

#### <a name="i-have-custom-attributes-in-workday-and-active-directory-how-do-i-configure-the-solution-to-work-with-my-custom-attributes"></a>Ich habe benutzerdefinierte Attribute in Workday und Active Directory. Wie konfiguriere ich die Lösung, damit diese mit meinen benutzerdefinierten Attributen arbeitet?

Die Lösung unterstützt benutzerdefinierte Workday- und Active Directory-Attribute. Um Ihre benutzerdefinierten Attribute dem Zuordnungsschema hinzuzufügen, öffnen Sie das Blatt **Attributzuordnung** und scrollen Sie nach unten, um den Abschnitt **Erweiterte Optionen anzeigen** zu erweitern. 

![Attributliste bearbeiten](./media/workday-inbound-tutorial/wd_edit_attr_list.png)

Um Ihre benutzerdefinierten Workday-Attribute hinzuzufügen, wählen Sie die Option *Attributliste für Workday bearbeiten*, und um Ihre benutzerdefinierten AD-Attribute hinzuzufügen, wählen Sie die Option *Attributliste für lokales Active Directory bearbeiten*.

Weitere Informationen:

* [Anpassen der Liste der Workday-Benutzerattribute](#customizing-the-list-of-workday-user-attributes)

#### <a name="how-do-i-configure-the-solution-to-only-update-attributes-in-ad-based-on-workday-changes-and-not-create-any-new-ad-accounts"></a>Wie konfiguriere ich die Lösung so, dass sie nur Attribute im AD aktualisiert, die auf Änderungen an Workday basieren, und keine neuen AD-Konten erstellt?

Diese Konfiguration kann vorgenommen werden, indem die **Zielobjektaktionen** im Blatt **Attributzuordnung** wie unten gezeigt eingestellt werden:

![Aktualisierungsaktion](./media/workday-inbound-tutorial/wd_target_update_only.png)

Aktivieren Sie das Kontrollkästchen „Update“, damit nur Aktualisierungsvorgänge von Workday zu AD durchgeführt werden. 

#### <a name="can-i-provision-users-photo-from-workday-to-active-directory"></a>Kann ich Benutzerfotos aus Workday für Active Directory bereitstellen?

Die Lösung unterstützt derzeit keine Einstellung von binären Attributen wie *thumbnailPhoto* und *jpegPhoto* in Active Directory.

#### <a name="how-do-i-sync-mobile-numbers-from-workday-based-on-user-consent-for-public-usage"></a>Wie synchronisieren ich Mobilfunknummern aus Workday basierend auf der Benutzereinwilligung für die öffentliche Verwendung?

* Wechseln Sie zum Blatt „Bereitstellung“ Ihrer Workday-Bereitstellungs-App.
* Klicken Sie auf die Attributzuordnungen. 
* Wählen Sie unter **Zuordnungen** die Option **Workday-Worker mit lokalem Active Directory synchronisieren** (oder **Workday-Worker mit Azure AD synchronisieren**).
* Scrollen Sie auf der Seite „Attributzuordnungen“ nach unten und aktivieren Sie das Kontrollkästchen „Erweiterte Optionen anzeigen“.  Klicken Sie auf **Attributliste für Workday bearbeiten**.
* Suchen Sie auf dem Blatt, das geöffnet wird, das Attribut „Mobile“, und klicken Sie auf die Zeile, sodass Sie den **API-Ausdruck** und ![Mobile GDPR](./media/workday-inbound-tutorial/mobile_gdpr.png) bearbeiten können.

* Ersetzen Sie den **API-Ausdruck** durch den folgenden neuen Ausdruck. Damit wird die Arbeitsmobilfunknummer nur dann abgerufen, wenn „Public Usage Flag“ auf „True“ festgelegt ist.

    ```
     wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Contact_Data/wd:Phone_Data[translate(string(wd:Phone_Device_Type_Reference/@wd:Descriptor),'abcdefghijklmnopqrstuvwxyz','ABCDEFGHIJKLMNOPQRSTUVWXYZ')='MOBILE' and translate(string(wd:Usage_Data/wd:Type_Data/wd:Type_Reference/@wd:Descriptor),'abcdefghijklmnopqrstuvwxyz','ABCDEFGHIJKLMNOPQRSTUVWXYZ')='WORK' and string(wd:Usage_Data/@wd:Public)='1']/@wd:Formatted_Phone
    ```

* Speichern Sie die Attributliste.
* Speichern Sie die Attributzuordnung.
* Löschen Sie den aktuellen Status, und starten Sie die vollständige Synchronisierung neu.

#### <a name="how-do-i-format-display-names-in-ad-based-on-the-users-departmentcountrycity-attributes-and-handle-regional-variances"></a>Wie formatiere ich Anzeigenamen in AD basierend auf den Attributen für Abteilung/Land/Stadt des Benutzers und wie gehe mit regionalen Abweichungen um?

Es ist eine häufige Anforderung, das Attribut *displayName* in AD so zu konfigurieren, dass es auch Informationen über die Abteilung und das Land / die Region des Benutzers liefert. Wenn John Smith z.B. in der Marketingabteilung in den USA arbeitet, kann sein *displayName* als *Smith, John (Marketing-US)* angezeigt werden.

So können Sie solche Anforderungen für die Konstruktion von *CN* oder *displayName* verarbeiten, um Attribute wie Unternehmen, Geschäftseinheit, Stadt oder Land/Region aufzunehmen.

* Jedes Workday-Attribut wird über einen zugrundeliegenden XPATH-API-Ausdruck abgerufen, der in **Attributzuordnung -> Abschnitt „Erweitert“ -> Attributliste für Workday bearbeiten** konfigurierbar ist. Hier ist der XPATH-API-Standardausdruck für die Workday-Attribute *PreferredFirstName*, *PreferredLastName*, *Company* und *SupervisoryOrganization*.

     | Workday-Attribut | API-XPATH-Ausdruck |
     | ----------------- | -------------------- |
     | PreferredFirstName | wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Name_Data/wd:Preferred_Name_Data/wd:Name_Detail_Data/wd:First_Name/text() |
     | PreferredLastName | wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Name_Data/wd:Preferred_Name_Data/wd:Name_Detail_Data/wd:Last_Name/text() |
     | Unternehmen | wd:Worker/wd:Worker_Data/wd:Organization_Data/wd:Worker_Organization_Data[wd:Organization_Data/wd:Organization_Type_Reference/wd:ID[@wd:type='Organization_Type_ID']='Company']/wd:Organization_Reference/@wd:Descriptor |
     | SupervisoryOrganization | wd:Worker/wd:Worker_Data/wd:Organization_Data/wd:Worker_Organization_Data/wd:Organization_Data[wd:Organization_Type_Reference/wd:ID[@wd:type='Organization_Type_ID']='Supervisory']/wd:Organization_Name/text() |
  
   Vergewissern Sie sich bei Ihrem Workday-Team, dass der obige API-Ausdruck für Ihre Workday-Mandantenkonfiguration gültig ist. Bei Bedarf können Sie sie gemäß den Schritten in [Anpassen der Liste der Workday-Benutzerattribute](#customizing-the-list-of-workday-user-attributes) bearbeiten.

* Auf ähnliche Weise werden die in Workday vorhandenen Länderinformationen mit dem folgenden XPATH abgerufen: *wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference*

     Es gibt 5 länderbezogene Attribute, die im Abschnitt „Workday-Attributliste“ verfügbar sind.

     | Workday-Attribut | API-XPATH-Ausdruck |
     | ----------------- | -------------------- |
     | CountryReference | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/wd:ID[@wd:type='ISO_3166-1_Alpha-3_Code']/text() |
     | CountryReferenceFriendly | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/@wd:Descriptor |
     | CountryReferenceNumeric | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/wd:ID[@wd:type='ISO_3166-1_Numeric-3_Code']/text() |
     | CountryReferenceTwoLetter | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Reference/wd:ID[@wd:type='ISO_3166-1_Alpha-2_Code']/text() |
     | CountryRegionReference | wd:Worker/wd:Worker_Data/wd:Employment_Data/wd:Position_Data/wd:Business_Site_Summary_Data/wd:Address_Data/wd:Country_Region_Reference/@wd:Descriptor |

  Vergewissern Sie sich bei Ihrem Workday-Team, dass die obigen API-Ausdrücke für Ihre Workday-Mandantenkonfiguration gültig ist. Bei Bedarf können Sie sie gemäß den Schritten in [Anpassen der Liste der Workday-Benutzerattribute](#customizing-the-list-of-workday-user-attributes) bearbeiten.

* Um den richtigen Attributzuordnungsausdruck zu erstellen, identifizieren Sie, welches Workday-Attribut „autoritativ“ den Vornamen, Nachnamen, das Land / die Region und die Abteilung des Benutzers darstellt. Nehmen wir an, die Attribute sind *PreferredFirstName*, *PreferredLastName*, *CountryReferenceTwoLetter* und *SupervisoryOrganization*. Sie können dies verwenden, um einen Ausdruck für das AD-Attribut *displayName* wie folgt aufzubauen, um einen Anzeigenamen wie *Smith, John (Marketing-US)* zu erhalten.

    ```
     Append(Join(", ",[PreferredLastName],[PreferredFirstName]), Join(""," (",[SupervisoryOrganization],"-",[CountryReferenceTwoLetter],")"))
    ```
    Sobald Sie den richtigen Ausdruck gefunden haben, bearbeiten Sie die Attributzuordnungstabelle und ändern Sie die Attributzuordnung *displayName* wie unten gezeigt:   ![DisplayName-Zuordnung](./media/workday-inbound-tutorial/wd_displayname_map.png)

* In Erweiterung des obigen Beispiels möchten Sie Städtenamen, die von Workday kommen, in Kurzwerte umwandeln und damit Anzeigenamen wie *Smith, John (CHI)* oder *Doe, Jane (NYC)* erstellen, dann kann dieses Ergebnis mit einem Switch-Ausdruck mit dem Workday-Attribut *Municipality* als determinante Variable erreicht werden.

     ```
    Switch
    (
      [Municipality],
      Join(", ", [PreferredLastName], [PreferredFirstName]),  
           "Chicago", Append(Join(", ",[PreferredLastName], [PreferredFirstName]), "(CHI)"),
           "New York", Append(Join(", ",[PreferredLastName], [PreferredFirstName]), "(NYC)"),
           "Phoenix", Append(Join(", ",[PreferredLastName], [PreferredFirstName]), "(PHX)")
    )
     ```
    Weitere Informationen:
  * [Wechseln der Funktionssyntax](../manage-apps/functions-for-customizing-application-data.md#switch)
  * [Verknüpfen der Funktionssyntax](../manage-apps/functions-for-customizing-application-data.md#join)
  * [Anfügen der Funktionssyntax](../manage-apps/functions-for-customizing-application-data.md#append)

#### <a name="how-can-i-use-selectuniquevalue-to-generate-unique-values-for-samaccountname-attribute"></a>Wie kann ich mit „SelectUniqueValue eindeutige Werte für das Attribut „samAccountName“ generieren?

Nehmen wir an, Sie möchten eindeutige Werte für das Attribut *samAccountName* unter Verwendung einer Kombination der Attribute *FirstName* und *LastName* aus Workday generieren. Nachfolgend finden Sie einen Ausdruck, mit dem Sie starten können:

```
SelectUniqueValue(
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,1), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , ),
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,2), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , ),
    Replace(Mid(Replace(NormalizeDiacritics(StripSpaces(Join("",  Mid([FirstName],1,3), [LastName]))), , "([\\/\\\\\\[\\]\\:\\;\\|\\=\\,\\+\\*\\?\\<\\>])", , "", , ), 1, 20), , "(\\.)*$", , "", , )
)
```

So funktioniert der obige Ausdruck: Wenn der Benutzer John Smith ist, wird zuerst versucht „JSmith“ zu erstellen. Ist dieser Ausdruck bereits vorhanden, wird „JoSmith“ generiert. Ist dieser schon vorhanden, wird „JohSmith“ generiert. Der Ausdruck stellt auch sicher, dass der generierte Wert den Längen- und Sonderzeichenbeschränkungen für *samAccountName* entspricht.

Weitere Informationen:

* [Mid-Funktionssyntax](../manage-apps/functions-for-customizing-application-data.md#mid)
* [Ersetzen der Funktionssyntax](../manage-apps/functions-for-customizing-application-data.md#replace)
* [SelectUniqueValue-Funktionssyntax](../manage-apps/functions-for-customizing-application-data.md#selectuniquevalue)

#### <a name="how-do-i-remove-characters-with-diacritics-and-convert-them-into-normal-english-alphabets"></a>Wie entferne ich Zeichen mit diakritischen Zeichen und konvertiere sie in normale englische Schriftzeichen?

Verwenden Sie die Funktion [NormalizeDiacritics](../manage-apps/functions-for-customizing-application-data.md#normalizediacritics), um Sonderzeichen im Vor- und Nachnamen des Benutzers zu entfernen, wenn Sie die E-Mail-Adresse oder den CN-Wert für den Benutzer erstellen.

## <a name="troubleshooting-tips"></a>Tipps zur Problembehandlung

Dieser Abschnitt enthält spezielle Hinweise zur Behebung von Bereitstellungsproblemen mit Ihrer Workday-Integration mithilfe der Protokolle „Azure AD-Überwachungsprotokolle“ und „Protokolle für die Windows Server-Ereignisanzeige“. Sie bauen auf allgemeinen Schritten zur Problembehandlung und den Konzepten in [Tutorial: Meldung zur automatischen Benutzerkontobereitstellung](../manage-apps/check-status-user-account-provisioning.md) auf.

Dieser Abschnitt enthält die folgenden Aspekte bezüglich der Problembehandlung:

* [Einrichten der Windows-Ereignisanzeige für die Problembehandlung bei Agents](#setting-up-windows-event-viewer-for-agent-troubleshooting)
* [Einrichten von Azure-Portal-Überwachungsprotokollen für die Problembehandlung bei einem Dienst](#setting-up-azure-portal-audit-logs-for-service-troubleshooting)
* [Grundlegendes zu Protokollen für Erstellungsvorgänge für ein AD-Benutzerkonto](#understanding-logs-for-ad-user-account-create-operations)
* [Grundlegendes zu Protokollen für Aktualisierungsvorgänge für Manager](#understanding-logs-for-manager-update-operations)
* [Beheben von häufig auftretenden Fehlern](#resolving-commonly-encountered-errors)

### <a name="setting-up-windows-event-viewer-for-agent-troubleshooting"></a>Einrichten der Windows-Ereignisanzeige für die Problembehandlung bei Agents

* Melden Sie sich beim Windows Server-Computer an, auf dem der Bereitstellungs-Agent bereitgestellt ist.
* Öffnen Sie die Desktop-App **Windows Server-Ereignisanzeige**.
* Klicken Sie auf **Windows-Protokolle > Anwendung**.
* Verwenden Sie die Option **Aktuelles Protokoll filtern...** , um alle Ereignisse anzuzeigen, die unter der Quelle **AAD.Connect.ProvisioningAgent** protokolliert sind, anzuzeigen, und schließen Sie Ereignisse mit der Ereignis-ID „5“ aus, indem Sie den Filter „5“ angeben, wie unten dargestellt.

  ![Windows-Ereignisanzeige](media/workday-inbound-tutorial/wd_event_viewer_01.png))

* Klicken Sie auf **OK**, um die Ergebnisansicht anhand der Spalte **Datum und Uhrzeit** zu filtern.

### <a name="setting-up-azure-portal-audit-logs-for-service-troubleshooting"></a>Einrichten von Azure-Portal-Überwachungsprotokollen für die Problembehandlung bei einem Dienst

* Starten Sie das [Azure-Portal](https://portal.azure.com), und navigieren Sie zum Abschnitt **Überwachungsprotokolle** Ihrer Workday-Bereitstellungs-App.
* Verwenden Sie die Schaltfläche **Spalten** auf der Seite „Überwachungsprotokolle“, um nur die folgenden Spalten in der Ansicht anzuzeigen (Datum, Aktivität, Status, Statusgrund). Mit dieser Konfiguration stellen Sie sicher, dass Sie sich nur auf Daten konzentrieren, die für die Problembehandlung relevant sind.

  ![Spalte „Überwachungsprotokoll“](media/workday-inbound-tutorial/wd_audit_logs_00.png)

* Verwenden Sie die Abfrageparameter **Ziel** und **Datumsbereich** zum Filtern der Ansicht. 
  * Legen Sie den Abfrageparameter **Ziel** auf „Worker-ID“ oder „Mitarbeiter-ID“ des Workday-Workerobjekts fest.
  * Legen Sie den **Datumsbereich** auf einen entsprechenden Zeitraum fest, den Sie auf Fehler oder Probleme bei der Bereitstellung überprüfen möchten.

  ![Überwachungsprotokollfilter](media/workday-inbound-tutorial/wd_audit_logs_01.png)

### <a name="understanding-logs-for-ad-user-account-create-operations"></a>Grundlegendes zu Protokollen für Erstellungsvorgänge für ein AD-Benutzerkonto

Wenn ein neuer Mitarbeiter in Workday erkannt wird (z.B. mit der Mitarbeiter-ID *21023*), versucht der Azure AD-Bereitstellungsdienst, ein neues AD-Benutzerkonto für den Mitarbeiter zu erstellen und erstellt dabei 4 Datensätze für das Überwachungsprotokoll, wie unten beschrieben:

  [![Erstellungsvorgänge für Überwachungsprotokolle](media/workday-inbound-tutorial/wd_audit_logs_02.png)](media/workday-inbound-tutorial/wd_audit_logs_02.png#lightbox)

Wenn Sie auf einen Datensatz im Überwachungsprotokoll klicken, wird die Seite **Aktivitätsdetails** geöffnet. Das wird auf der Seite **Aktivitätsdetails** für jeden Protokolldatensatztyp angezeigt.

* Datensatz **Workday-Import**: Dieser Protokolldatensatz zeigt die aus Workday abgerufenen Workerinformationen an. Anhand der Informationen im Abschnitt *Zusätzliche Details* des Protokolldatensatzes können Sie Probleme beim Abrufen von Daten aus Workday beheben. Unten finden Sie einen Beispieldatensatz sowie Hinweise für die Interpretation der einzelnen Felder.

  ```JSON
  ErrorCode : None  // Use the error code captured here to troubleshoot Workday issues
  EventName : EntryImportAdd // For full sync, value is "EntryImportAdd" and for delta sync, value is "EntryImport"
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID (usually the Worker ID or Employee ID field)
  SourceAnchor : a071861412de4c2486eb10e5ae0834c3 // set to the WorkdayID (WID) associated with the record
  ```

* Datensatz **AD-Import**: Dieser Protokolldatensatz zeigt aus AD abgerufene Kontoinformationen an. Da es bei der ersten Benutzererstellung kein AD-Konto gibt, wird unter *Statusgrund Aktivität* angegeben, dass kein Konto mit dem Attributwert mit einer übereinstimmenden ID im Active Directory gefunden wurde. Anhand der Informationen im Abschnitt *Zusätzliche Details* des Protokolldatensatzes können Sie Probleme beim Abrufen von Daten aus Workday beheben. Unten finden Sie einen Beispieldatensatz sowie Hinweise für die Interpretation der einzelnen Felder.

  ```JSON
  ErrorCode : None // Use the error code captured here to troubleshoot Workday issues
  EventName : EntryImportObjectNotFound // Implies that object was not found in AD
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID
  ```

  Um die Protokolldatensätze des Bereitstellungs-Agent zu finden, die diesem AD-Importvorgang entsprechen, öffnen Sie die Protokolle der Windows-Ereignisanzeige, und verwenden Sie die Option **Suche....** , um Protokolleinträge mit dem Attributwert für die übereinstimmende ID/Verknüpfungseigenschaft zu suchen (in diesem Fall *21023*).

  ![Suchen](media/workday-inbound-tutorial/wd_event_viewer_02.png)

  Suchen Sie nach dem Eintrag mit *Event ID = 9*, der Ihnen den LDAP-Suchfilter zur Verfügung stellt, mit dem der Agent das AD-Konto abruft. Sie können überprüfen, ob dies der richtige Suchfilter ist, um eindeutige Benutzereingaben zu erhalten.

  ![LDAP-Suche](media/workday-inbound-tutorial/wd_event_viewer_03.png)

  Der Datensatz, der unmittelbar darauf mit *Event ID = 2* folgt, erfasst das Ergebnis des Suchvorgangs und stellt fest, inwieweit Ergebnisse geliefert wurden.

  ![LDAP-Ergebnisse](media/workday-inbound-tutorial/wd_event_viewer_04.png)

* Datensatz **Synchronisierungsregelaktion**: Dieser Protokolldatensatz zeigt die Ergebnisse der Attributzuordnungsregeln und konfigurierten Bereichsfilter sowie die Bereitstellungsaktion an, die zur Verarbeitung des eingehenden Workday-Ereignisses durchgeführt wird. Anhand der Informationen im Abschnitt *Zusätzliche Details* des Protokolldatensatzes können Sie Probleme mit der Synchronisierungsaktion beheben. Unten finden Sie einen Beispieldatensatz sowie Hinweise für die Interpretation der einzelnen Felder.

  ```JSON
  ErrorCode : None // Use the error code captured here to troubleshoot sync issues
  EventName : EntrySynchronizationAdd // Implies that the object will be added
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID
  SourceAnchor : a071861412de4c2486eb10e5ae0834c3 // set to the WorkdayID (WID) associated with the profile in Workday
  ```

  Wenn es Probleme mit Ihren Attributzuordnungsausdrücken gibt oder die eingehenden Workday-Daten Fehler enthalten (z.B. Leer- oder Nullwert für erforderliche Attribute), dann wird an dieser Stelle ein Fehler ausgegeben, wobei der ErrorCode Details über den Fehler liefert.

* Datensatz **AD-Export**: Dieser Protokolldatensatz zeigt das Ergebnis der AD-Kontoerstellung zusammen mit den Attributwerten, die im Prozess festgelegt wurden. Anhand der Informationen im Abschnitt *Zusätzliche Details* des Protokolldatensatzes können Sie Probleme mit der Kontoerstellung beheben. Unten finden Sie einen Beispieldatensatz sowie Hinweise für die Interpretation der einzelnen Felder. Im Abschnitt „Zusätzliche Details“ wird der „EventName“ auf „EntryExportAdd“, die „JoiningProperty“ auf den Wert des übereinstimmenden ID-Attributs, der „SourceAnchor“ auf die dem Datensatz zugeordnete WorkdayID (WID) und der „TargetAnchor“ auf den Wert des AD-Attributs „ObjectGuid“ des neu erstellten Benutzers festgelegt. 

  ```JSON
  ErrorCode : None // Use the error code captured here to troubleshoot AD account creation issues
  EventName : EntryExportAdd // Implies that object will be created
  JoiningProperty : 21023 // Value of the Workday attribute that serves as the Matching ID
  SourceAnchor : a071861412de4c2486eb10e5ae0834c3 // set to the WorkdayID (WID) associated with the profile in Workday
  TargetAnchor : 83f0156c-3222-407e-939c-56677831d525 // set to the value of the AD "objectGuid" attribute of the new user
  ```

  Um die Protokolldatensätze des Bereitstellungs-Agent zu finden, die diesem AD-Exportvorgang entsprechen, öffnen Sie die Protokolle der Windows-Ereignisanzeige, und verwenden Sie die Option **Suche....** , um Protokolleinträge mit dem Attributwert für die übereinstimmende ID/Verknüpfungseigenschaft zu suchen (in diesem Fall *21023*).  

  Suchen Sie nach einem HTTP POST-Datensatz, der dem Zeitstempel des Exportvorgangs mit *Event ID = 2* entspricht. Dieser Datensatz enthält die Attributwerte, die der Bereitstellungsdienst an den Bereitstellungs-Agent gesendet hat.

  [![SCIM hinzufügen](media/workday-inbound-tutorial/wd_event_viewer_05.png)](media/workday-inbound-tutorial/wd_event_viewer_05.png#lightbox)

  Unmittelbar nach dem obigen Ereignis sollte es ein weiteres Ereignis geben, das die Antwort des AD-Kontoerstellungsvorgangs erfasst. Dieses Ereignis gibt die neue „objectGuid“ zurück, die in AD erstellt wurde, und es wird als TargetAnchor-Attribut im Bereitstellungsdienst festgelegt.

  [![SCIM hinzufügen](media/workday-inbound-tutorial/wd_event_viewer_06.png)](media/workday-inbound-tutorial/wd_event_viewer_06.png#lightbox)

### <a name="understanding-logs-for-manager-update-operations"></a>Grundlegendes zu Protokollen für Aktualisierungsvorgänge für Manager

Das Manager-Attribut ist ein Verweisattribut in Active Directory. Der Bereitstellungsdienst legt das Manager-Attribut nicht als Teil des Erstellungsvorgangs des Benutzers fest. Das Manager-Attribut wird stattdessen als Teil des *Aktualisierungsvorgangs* nach der AD-Kontoerstellung für den Benutzer festgelegt. Um das obige Beispiel zu erweitern, nehmen wir an, dass ein neuer Mitarbeiter mit der Mitarbeiter-ID „21451“ in Workday aktiviert ist und der Manager des neuen Mitarbeiters (*21023*) bereits ein AD-Konto hat. In diesem Szenario werden bei der Suche im Überwachungsprotokolle für Benutzer 21451 5 Einträge ausgegeben.

  [![Manager-Update](media/workday-inbound-tutorial/wd_audit_logs_03.png)](media/workday-inbound-tutorial/wd_audit_logs_03.png#lightbox)

Die ersten 4 Datensätze sind wie diejenigen, die wir im Rahmen des Benutzererstellungsvorgangs untersucht haben. Der 5. Datensatz ist der Export, der dem Update des Manager-Attributs zugeordnet ist. Der Protokolldatensatz zeigt das Ergebnis der Aktualisierung des AD-Kontenmanagers an, die mit dem Attribut *objectGuid* des Managers durchgeführt wird.

  ```JSON
  // Modified Properties
  Name : manager
  New Value : "83f0156c-3222-407e-939c-56677831d525" // objectGuid of the user 21023

  // Additional Details
  ErrorCode : None // Use the error code captured here to troubleshoot AD account creation issues
  EventName : EntryExportUpdate // Implies that object will be created
  JoiningProperty : 21451 // Value of the Workday attribute that serves as the Matching ID
  SourceAnchor : 9603bf594b9901693f307815bf21870a // WorkdayID of the user
  TargetAnchor : 43b668e7-1d73-401c-a00a-fed14d31a1a8 // objectGuid of the user 21451

  ```

### <a name="resolving-commonly-encountered-errors"></a>Beheben von häufig auftretenden Fehlern

Dieser Abschnitt behandelt häufig auftretende Fehler bei der Bereitstellung von Workday-Benutzern und erläutert, wie Sie diese beheben können. Die Fehler werden wie folgt gruppiert:

* [Fehler beim Bereitstellungs-Agent](#provisioning-agent-errors)
* [Verbindungsfehler](#connectivity-errors)
* [Fehler bei der Erstellung des AD-Benutzerkontos](#ad-user-account-creation-errors)
* [Fehler bei der Aktualisierung des AD-Benutzerkontos](#ad-user-account-update-errors)

#### <a name="provisioning-agent-errors"></a>Fehler beim Bereitstellungs-Agent

|#|Fehlerszenario |Mögliche Ursachen|Empfohlene Lösung|
|--|---|---|---|
|1.| Fehler bei der Installation des Bereitstellungs-Agent mit Fehlermeldung:  *Dienst „Microsoft Azure AD Connect Provisioning Agent“ (AADConnectProvisioningAgent) konnte nicht gestartet werden. Überprüfen Sie, ob Sie über ausreichend Rechte verfügen, um das System zu starten.* | Dieser Fehler tritt normalerweise auf, wenn Sie versuchen, den Bereitstellungs-Agent auf einem Domänencontroller zu installieren, und die Gruppenrichtlinie verhindert, dass der Dienst gestartet wird.  Es wird auch angezeigt, ob Sie eine frühere Version des Agent ausführen und ihn nicht deinstalliert haben, bevor Sie eine neue Installation starten.| Installieren Sie den Bereitstellungs-Agent auf einem Nicht-DC-Server. Stellen Sie sicher, dass frühere Versionen des Agents deinstalliert werden, bevor Sie den neuen Agent installieren.|
|2.| Der Windows-Dienst „Microsoft Azure AD Connect Provisioning Agent“ befindet sich im Zustand *Wird gestartet* und wechselt nicht in den Zustand *Wird ausgeführt*. | Als Teil der Installation erstellt der Agent-Assistent ein lokales Konto (**NT Service\\AADConnectProvisioningAgent**) auf dem Server und dies ist das **Anmeldekonto**, das zum Starten des Dienstes verwendet wird. Wenn eine Sicherheitsrichtlinie auf Ihrem Windows Server verhindert, dass lokale Konten die Dienste ausgeführt werden, wird dieser Fehler angezeigt. | Öffnen Sie die *Dienstkonsole*. Klicken Sie mit der rechten Maustaste auf den Windows-Dienst „Microsoft Azure AD Connect Provisioning Agent“ und geben Sie auf der Registerkarte „Anmelden“ das Konto eines Domänenadministrators an, um den Dienst auszuführen. Starten Sie den Dienst neu. |
|3.| Wenn Sie den Bereitstellungs-Agent mit Ihrer AD-Domäne im Schritt *Active Directory verbinden* konfigurieren, dauert es lange, bis der Assistent versucht, das AD-Schema zu laden und schließlich abbricht. | Dieser Fehler wird in der Regel angezeigt, wenn der Assistent aufgrund von Firewallproblemen den Server des AD-Domänencontrollers nicht kontaktieren kann. | Auf dem Assistentenbildschirm *Active Directory verbinden* gibt es beim Bereitstellen der Anmeldeinformationen für Ihre AD-Domäne eine Option namens *Domänencontrollerpriorität auswählen*. Verwenden Sie diese Option, um einen Domänencontroller auszuwählen, der sich auf der gleichen Site wie der Agent-Server befindet, und stellen Sie sicher, dass es keine Firewallregeln gibt, die die Kommunikation blockieren. |

#### <a name="connectivity-errors"></a>Verbindungsfehler

Wenn der Bereitstellungsdienst keine Verbindung zu Workday oder Active Directory herstellen kann, kann dies dazu führen, dass die Bereitstellung in einen Quarantänezustand versetzt wird. Verwenden Sie die Tabelle unten zum Behandeln von Konnektivitätsproblemen.

|#|Fehlerszenario |Mögliche Ursachen|Empfohlene Lösung|
|--|---|---|---|
|1.| Beim Klicken auf **Verbindung testen** wird die folgende Fehlermeldung angezeigt: *Fehler beim Herstellen einer Verbindung mit Active Directory. Stellen Sie sicher, dass der lokale Bereitstellung-Agent ausgeführt wird und mit der richtigen Active Directory-Domäne konfiguriert ist.* | Dieser Fehler tritt in der Regel auf, wenn der Bereitstellungs-Agent nicht ausgeführt wird oder eine Firewall die Kommunikation zwischen Azure AD und dem Bereitstellungs-Agent blockiert. Dieser Fehler wird auch angezeigt, wenn die Domäne nicht im Agent-Assistenten konfiguriert ist. | Öffnen Sie die *Dienstkonsole* von Windows Server, um zu überprüfen, ob der Agent ausgeführt wird. Öffnen Sie den Assistenten des Bereitstellungs-Agent und überprüfen Sie, ob die richtige Domäne für den Agent registriert ist.  |
|2.| Der Bereitstellungsauftrag geht an den Wochenenden (Fr-Sa) in den Quarantänezustand und wir erhalten eine E-Mail-Benachrichtigung, dass ein Fehler bei der Synchronisation vorliegt. | Eine der häufigsten Ursachen für diesen Fehler ist die geplante Workday-Ausfallzeit. Wenn Sie einen Workday-Implementierungsmandanten verwenden, beachten Sie bitte, dass Workday für seine Implementierungsmandanten an Wochenenden Ausfallzeiten eingeplant hat (in der Regel von Freitagabend bis Samstagmorgen), und während dieses Zeitraums können die Workday-Bereitstellungs-Apps in den Quarantänezustand übergehen, da sie keine Verbindung zum Workday herstellen können. Sie kehrt in den Normalzustand zurück, sobald der Mandant der Workday-Implementierung wieder online ist. In seltenen Fällen kann dieser Fehler auch auftreten, wenn sich das Kennwort des Integrationssystembenutzers durch Mandantenaktualisierung geändert hat oder wenn sich das Konto im gesperrten oder abgelaufenen Zustand befindet. | Erkundigen Sie sich bei Ihrem Workday-Administrator oder Integrationspartner, wann Workday die Ausfallzeit plant, um Warnmeldungen während der Ausfallzeit zu ignorieren und die Verfügbarkeit zu bestätigen, sobald die Workday-Instanz wieder online ist.  |


#### <a name="ad-user-account-creation-errors"></a>Fehler bei der Erstellung des AD-Benutzerkontos

|#|Fehlerszenario |Mögliche Ursachen|Empfohlene Lösung|
|--|---|---|---|
|1.| Fehler beim Exportvorgang im Überwachungsprotokoll mit der Meldung *Fehler: OperationsError-SvcErr: Es ist ein Vorgangsfehler aufgetreten. Im Verzeichnisdienst wurde kein übergeordneter Verweis konfiguriert. Der Verzeichnisdienst kann daher keine Verweise auf Objekte außerhalb dieser Gesamtstruktur ausgeben.* | Dieser Fehler tritt in der Regel auf, wenn die Organisationseinheit *Active Directory-Container* nicht korrekt eingestellt ist oder wenn es Probleme mit der für *parentDistinguishedName* verwendeten Ausdruckszuordnung gibt. | Überprüfen Sie den Parameter der Organisationseinheit *Active Directory-Container* auf Tippfehler. Wenn Sie *parentDistinguishedName* in der Attributzuordnung verwenden, müssen Sie sicherstellen, das die Auswertung immer hinsichtlich eines bekannten Containers innerhalb der AD-Domäne ausgeführt wird. Überprüfen Sie im Ereignis *Export* des Überwachungsprotokolls den generierten Wert. |
|2.| Fehler beim Exportvorgang im Überwachungsprotokoll mit dem Fehlercode: *SystemForCrossDomainIdentityManagementBadResponse* und der Meldung *Fehler: ConstraintViolation-AtrErr: Ein Wert in der Anforderung ist ungültig. Ein Wert für das Attribut befindet sich nicht im zulässigen Wertebereich. \nFehlerdetails: CONSTRAINT_ATT_TYPE - company*. | Dieser Fehler wird zwar meist spezifisch für das Attribut *company* angezeigt, kann aber auch bei Attributen wie *CN* auftreten. Dieser Fehler tritt aufgrund von AD erzwungenen Schemaeinschränkungen auf. Standardmäßig gibt es für die Attribute wie *company* und *CN* in AD eine Obergrenze von 64 Zeichen. Wenn der aus Workday stammende Wert mehr als 64 Zeichen enthält, wird diese Fehlermeldung angezeigt. | Überprüfen Sie das Ereignis *Export* in den Überwachungsprotokollen, um den Wert für das in der Fehlermeldung gemeldete Attribut anzuzeigen. Sie sollten in Betracht ziehen, den aus Workday kommenden Wert mit der Funktion [Mid](../manage-apps/functions-for-customizing-application-data.md#mid) zu kürzen oder die Zuordnungen in ein AD-Attribut zu ändern, das keine ähnlichen Längenbeschränkungen hat.  |

#### <a name="ad-user-account-update-errors"></a>Fehler bei der Aktualisierung des AD-Benutzerkontos

Während des Aktualisierungsprozesses des AD-Benutzerkontos liest der Bereitstellungsdienst Informationen sowohl vom Workday als auch vom AD, führt die Attributzuordnungsregeln aus und bestimmt, ob eine Änderung wirksam werden muss. Entsprechend wird ein Aktualisierungsereignis ausgelöst. Wenn bei einem dieser Schritte ein Fehler auftritt, wird dieser in den Überwachungsprotokollen protokolliert. Verwenden Sie die Tabelle unten zum Behandeln von häufigen Aktualisierungsfehlern.

|#|Fehlerszenario |Mögliche Ursachen|Empfohlene Lösung|
|--|---|---|---|
|1.| Fehler bei der Synchronisationsregelaktion im Überwachungsprotokoll mit der Meldung *EventName = EntrySynchronizationError and ErrorCode = EndpointUnavailable*. | Dieser Fehler wird angezeigt, wenn der Bereitstellungsdienst aufgrund eines Verarbeitungsfehlers beim lokalen Bereitstellungs-Agent nicht in der Lage ist, Benutzerprofildaten aus Active Directory abzurufen. | Überprüfen Sie die Protokolle der Ereignisanzeige des Bereitstellungs-Agent auf Fehlerereignisse, die auf Probleme mit dem Lesevorgang hinweisen (Filter nach Ereignis-ID #2). |
|2.| Das Manager-Attribut in AD wird für bestimmte Benutzer in AD nicht aktualisiert. | Dieser Fehler tritt am häufigsten auf, wenn Sie Bereichsregeln verwenden und der Manager des Benutzers nicht Teil des Bereichs ist. Dieses Problem kann auch dann auftreten, wenn das übereinstimmende ID-Attribut des Managers (z.B. EmployeeID) nicht in der AD-Zieldomäne gefunden oder nicht auf den richtigen Wert gesetzt wurde. | Überprüfen Sie den Bereichsfilter, und fügen Sie im Bereich den Managerbenutzer hinzu. Überprüfen Sie das Profil des Managers in AD, um sicherzustellen, dass es einen Wert für das übereinstimmende ID-Attribut gibt. |

## <a name="managing-your-configuration"></a>Verwalten Ihrer Konfiguration

Dieser Abschnitt beschreibt, wie Sie Ihre Workday-gesteuerte Konfiguration für die Benutzerbereitstellung weiter erweitern, anpassen und verwalten können. Es werden die folgenden Themen behandelt:

* [Anpassen der Liste der Workday-Benutzerattribute](#customizing-the-list-of-workday-user-attributes)  
* [Exportieren und Importieren Ihrer Konfiguration](#exporting-and-importing-your-configuration)

### <a name="customizing-the-list-of-workday-user-attributes"></a>Anpassen der Liste der Workday-Benutzerattribute

Die Workday-Bereitstellungs-Apps für Active Directory und Azure AD umfassen jeweils eine Liste mit Workday-Benutzerattributen, aus denen Sie auswählen können. Diese Listen sind jedoch nicht sehr umfangreich. Workday unterstützt mehrere Hundert möglicher Benutzerattribute, die entweder als Standardattribute oder als eindeutige Attribute für Ihren Workday-Mandanten eingerichtet werden können.

Der Azure AD-Bereitstellungsdienst unterstützt die Möglichkeit, Ihre Liste der Workday-Attribute so anzupassen, dass alle im [Get_Workers](https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Get_Workers.html)-Vorgang der Human Resources-API verfügbar gemachten Attribute enthalten sind.

Um diese Änderung vorzunehmen, müssen Sie [Workday Studio](https://community.workday.com/studio-download) verwenden, um die XPath-Ausdrücke zu extrahieren, die die gewünschten Attribute repräsentieren. Anschließend verwenden Sie den erweiterten Attribut-Editor im Azure-Portal, um sie zu Ihrer Bereitstellungskonfiguration hinzuzufügen.

**So rufen Sie einen XPath-Ausdruck für ein Workday-Benutzerattribut ab:**

1. Laden Sie [Workday Studio](https://community.workday.com/studio-download), herunter, und installieren Sie es. Sie benötigen ein Workday-Communitykonto, um auf das Installationsprogramm zuzugreifen.

2. Laden Sie die WSDL-Datei „Workday Human_Resources“ über diese URL herunter: https://community.workday.com/sites/default/files/file-hosting/productionapi/Human_Resources/v21.1/Human_Resources.wsdl

3. Starten Sie Workday Studio.

4. Wählen Sie in der Befehlsleiste die Option **Workday > Test Web Service in Tester** (Workday > Webdienst im Tester testen) aus.

5. Wählen Sie **External** und dann die WSDL-Datei „Human_Resources“ aus, die Sie in Schritt 2 heruntergeladen haben.

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio1.png)

6. Legen Sie das Feld **Location** auf `https://IMPL-CC.workday.com/ccx/service/TENANT/Human_Resources` fest, ersetzen Sie jedoch „IMPL-CC“ durch den tatsächlichen Typ Ihrer Instanz und „TENANT“ durch den echten Namen Ihres Mandanten.

7. Legen Sie **Operation** auf **Get_Workers** fest.

8.  Klicken Sie auf den **configure**-Link unterhalb der Bereiche „Request“ (Anforderung) und „Response“ (Antwort), um Ihre Workday-Anmeldeinformationen festzulegen. Aktivieren Sie das Kontrollkästchen **Authentifizierung**, und geben Sie den Benutzernamen und das Kennwort für Ihr Systemkonto für die Workday-Integration ein. Stellen Sie sicher, dass der Benutzername das Format „name\@mandant“ aufweist, und behalten Sie die Auswahl der Option **WS-Security UsernameToken** bei.

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio2.png)

9. Klicken Sie auf **OK**.

10. Fügen Sie im Bereich **Anforderung** den unten stehenden XML-Code ein, und legen Sie **Employee_ID** auf die Mitarbeiter-ID eines realen Benutzers in Ihrem Workday-Mandanten fest. Wählen Sie einen Benutzer aus, für den das Attribut aufgefüllt ist, das Sie extrahieren möchten.

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <env:Envelope xmlns:env="http://schemas.xmlsoap.org/soap/envelope/" xmlns:xsd="https://www.w3.org/2001/XMLSchema">
      <env:Body>
        <wd:Get_Workers_Request xmlns:wd="urn:com.workday/bsvc" wd:version="v21.1">
          <wd:Request_References wd:Skip_Non_Existing_Instances="true">
            <wd:Worker_Reference>
              <wd:ID wd:type="Employee_ID">21008</wd:ID>
            </wd:Worker_Reference>
          </wd:Request_References>
          <wd:Response_Group>
            <wd:Include_Reference>true</wd:Include_Reference>
            <wd:Include_Personal_Information>true</wd:Include_Personal_Information>
            <wd:Include_Employment_Information>true</wd:Include_Employment_Information>
            <wd:Include_Management_Chain_Data>true</wd:Include_Management_Chain_Data>
            <wd:Include_Organizations>true</wd:Include_Organizations>
            <wd:Include_Reference>true</wd:Include_Reference>
            <wd:Include_Transaction_Log_Data>true</wd:Include_Transaction_Log_Data>
            <wd:Include_Photo>true</wd:Include_Photo>
            <wd:Include_User_Account>true</wd:Include_User_Account>
          <wd:Include_Roles>true</wd:Include_Roles>
          </wd:Response_Group>
        </wd:Get_Workers_Request>
      </env:Body>
    </env:Envelope>
    ```

11. Klicken Sie auf **Send Request** (Anforderung senden, grüner Pfeil), um den Befehl auszuführen. Bei erfolgreicher Ausführung wird die Antwort im Bereich **Response** angezeigt. Überprüfen Sie die Antwort, um sicherzustellen, dass sie die Daten des eingegebenen Benutzers enthält und fehlerfrei ist.

12. Wenn alles richtig ist, kopieren Sie den XML-Code aus dem Bereich **Response**, und speichern Sie ihn als XML-Datei.

13. Wählen Sie in der Befehlsleiste von Workday Studio **File > Open File...** (Datei > Datei öffnen...) aus, und öffnen Sie die gespeicherte XML-Datei. Durch diese Aktion wird die Datei im XML-Editor von Workday Studio geöffnet.

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio3.png)

14. Navigieren Sie in der Dateistruktur durch **/env: Envelope > env: Body > wd:Get_Workers_Response > wd:Response_Data > wd: Worker**, um die Daten zu Ihrem Benutzer zu finden.

15. Suchen Sie unter **wd: Worker** das Attribut, das Sie hinzufügen möchten, und wählen Sie es aus.

16. Kopieren Sie den XPath-Ausdruck für das ausgewählte Attribut aus dem Feld **Document Path** (Dokumentpfad).

17. Entfernen Sie das Präfix **/env:Envelope/env:Body/wd:Get_Workers_Response/wd:Response_Data/** aus dem kopierten Ausdruck.

18. Wenn es sich beim letzten Element im kopierten Ausdruck um einen Knoten handelt (z.B. „/wd: Birth_Date“), dann fügen Sie **/text()** an das Ende des Ausdrucks an. Dies ist nicht erforderlich, wenn das letzte Element ein Attribut ist (Beispiel: „/@wd: type“).

19. Das Ergebnis sollte in etwa wie folgt aussehen: `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()`. Diesen Wert kopieren Sie in das Azure-Portal.

**So fügen Sie Ihr benutzerdefiniertes Workday-Benutzerattribut zu Ihrer Bereitstellungskonfiguration hinzu:**

1. Starten Sie das [Azure-Portal](https://portal.azure.com), und navigieren Sie zum Bereitstellungsbereich Ihrer Workday-Bereitstellungsanwendung, wie oben in diesem Tutorial beschrieben.

2. Legen Sie den **Bereitstellungsstatus** auf **Aus** fest, und klicken Sie dann auf **Speichern**. Mit diesem Schritt können Sie sicherstellen, dass Ihre Änderungen erst dann wirksam werden, wenn Sie bereit sind.

3. Wählen Sie unter **Zuordnungen** die Option **Workday-Worker mit lokalem Active Directory synchronisieren** (oder **Workday-Worker mit Azure AD synchronisieren**).

4. Scrollen Sie im nächsten Bildschirm nach unten, und wählen Sie **Erweiterte Optionen anzeigen** aus.

5. Wählen Sie **Attributliste für Workday bearbeiten** aus.

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio_aad1.png)

6. Scrollen Sie zu den Eingabefeldern am Ende der Attributliste.

7. Geben Sie unter **Name** einen Anzeigenamen für Ihr Attribut ein.

8. Wählen Sie einen **Typ** aus, der zu Ihrem Attribut passt (**Zeichenfolge** ist der am häufigsten verwendete Typ).

9. Geben Sie unter **API-Ausdruck** den XPath-Ausdruck ein, den Sie aus Workday Studio kopiert haben. Beispiel: `wd:Worker/wd:Worker_Data/wd:Personal_Data/wd:Birth_Date/text()`

10. Wählen Sie **Attribut hinzufügen** aus.

    ![Workday Studio](./media/workday-inbound-tutorial/wdstudio_aad2.png)

11. Wählen Sie oben **Speichern** aus, und klicken Sie im angezeigten Dialogfeld auf **Ja**. Schließen Sie den Bildschirm „Attributzuordnung“, falls dieser noch geöffnet ist.

12. Wählen Sie auf der Registerkarte **Bereitstellung** erneut die Option **Workday-Worker mit lokalem Active Directory synchronisieren** (oder **Worker mit Azure AD synchronisieren**).

13. Wählen Sie **Neue Zuordnung hinzufügen** aus.

14. Das neue Attribut sollte jetzt in der Liste **Quellattribut** angezeigt werden.

15. Fügen Sie wie gewünscht eine Zuordnung für Ihr neues Attribut hinzu.

16. Denken Sie nach Abschluss der Konfiguration daran, den **Bereitstellungsstatus** wieder auf **Ein** festzulegen und zu speichern.

### <a name="exporting-and-importing-your-configuration"></a>Exportieren und Importieren Ihrer Konfiguration

In diesem Abschnitt wird beschrieben, wie Sie mit der Microsoft Graph-API und dem Graph-Tester Ihre Zuordnungen und Schemata Ihres Workday-Bereitstellungsattributs in eine JSON-Datei exportieren und wieder in Azure AD importieren können.

#### <a name="step-1-retrieve-your-workday-provisioning-app-service-principal-id-object-id"></a>Schritt 1: Abrufen Ihrer Dienstprinzipal-ID für die Workday-Bereitstellungs-App (Objekt-ID)

1. Starten Sie das [Azure-Portal](https://portal.azure.com), und navigieren Sie zum Abschnitt „Eigenschaften“ Ihrer Workday-Bereitstellungs-App.
1. Kopieren Sie im Abschnitt „Eigenschaften“ Ihrer Bereitstellungs-App den GUID-Wert, der dem Feld *Objekt-ID* zugeordnet ist. Dieser Wert wird auch als die **ServicePrincipalId** Ihrer App bezeichnet, und wird in Graph-Tester-Vorgängen verwendet.

   ![Dienstprinzipal-ID der Workday-App](./media/workday-inbound-tutorial/wd_export_01.png)

#### <a name="step-2-sign-into-microsoft-graph-explorer"></a>Schritt 2: Anmelden bei Microsoft Graph-Tester

1. Starten Sie den [Microsoft Graph-Tester](https://developer.microsoft.com/graph/graph-explorer).
1. Klicken Sie auf die Schaltfläche „Mit Microsoft anmelden“, und melden Sie sich mit den globalen Azure AD-Anmeldeinformationen oder mit den Anmeldeinformationen für den App-Administrator an.

    ![Graph-Anmeldung](./media/workday-inbound-tutorial/wd_export_02.png)

1. Nach der erfolgreichen Anmeldung sehen Sie die Details des Benutzerkontos im linken Bereich.

#### <a name="step-3-retrieve-the-provisioning-job-id-of-the-workday-provisioning-app"></a>Schritt 3: Abrufen der Auftrags-ID für die Bereitstellung für die Workday-Bereitstellungs-App

Führen Sie im Microsoft Graph-Tester die folgende GET-Abfrage durch und ersetzen Sie [servicePrincipalId] durch die in [Schritt 1](#step-1-retrieve-your-workday-provisioning-app-service-principal-id-object-id) extrahierte **ServicePrincipalId**.

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs
```

Sie erhalten eine Antwort, wie unten dargestellt. Kopieren Sie das „id attribut“ aus der Antwort. Dieser Wert ist die **ProvisioningJobId** und wird zum Abrufen der zugrundeliegenden Schemametadaten verwendet.

   [![ID des Bereitstellungsauftrags](./media/workday-inbound-tutorial/wd_export_03.png)](./media/workday-inbound-tutorial/wd_export_03.png#lightbox)

#### <a name="step-4-download-the-provisioning-schema"></a>Schritt 4: Herunterladen des Bereitstellungsschemas

Führen Sie im Microsoft Graph-Tester die folgende GET-Abfrage durch und ersetzen Sie [servicePrincipalId] und [ProvisioningJobId] durch die in den vorherigen Schritten abgerufen Werten für „ServicePrincipalId“ und „ProvisioningJobId“.

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

Kopieren Sie das JSON-Objekt aus der Antwort, und speichern Sie sie in einer Datei, um eine Sicherung des Schemas zu erstellen.

#### <a name="step-5-import-the-provisioning-schema"></a>Schritt 5: Importieren des Bereitstellungsschemas

> [!CAUTION]
> Führen Sie diesen Schritt nur aus, wenn Sie das Schema für eine Konfiguration ändern müssen, die nicht über das Azure-Portal geändert werden kann, oder wenn Sie die Konfiguration aus einer zuvor gesicherten Datei mit einem gültigen und funktionierenden Schema wiederherstellen müssen.

Konfigurieren Sie im Microsoft Graph-Tester die folgende PUT-Abfrage durch und ersetzen Sie [servicePrincipalId] und [ProvisioningJobId] durch die in den vorherigen Schritten abgerufen Werten für „ServicePrincipalId“ und „ProvisioningJobId“.

```http
    PUT https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

Kopieren Sie auf der Registerkarte „Anforderungstext“ den Inhalt der JSON-Schemadatei.

   [![Anforderungstext](./media/workday-inbound-tutorial/wd_export_04.png)](./media/workday-inbound-tutorial/wd_export_04.png#lightbox)

Fügen Sie auf der Registerkarte „Anforderungsheader“ das Content-Type-Header-Attribut mit dem Wert „application/json“ ein.

   [![Anforderungsheader](./media/workday-inbound-tutorial/wd_export_05.png)](./media/workday-inbound-tutorial/wd_export_05.png#lightbox)

Klicken Sie auf die Schaltfläche „Abfrage ausführen“, um das neue Schema zu importieren.

## <a name="managing-personal-data"></a>Verwalten von personenbezogenen Daten

Die Workday-Bereitstellungslösung für Active Directory erfordert die Installation eines Bereitstellungs-Agent auf einem lokalen Windows Server. Dieser Agent erstellt Protokolle im Windows-Ereignisprotokoll, die abhängig von den Attributzuordnungen von Workday zu AD personenbezogene Daten enthalten können. Um den Datenschutzverpflichtungen des Benutzers nachzukommen, können Sie sicherstellen, dass über 48 Stunden hinaus keine Daten in den Ereignisprotokollen gespeichert werden, indem Sie eine von Windows geplante Aufgabe zum Löschen des Ereignisprotokolls einrichten.

Der Azure AD-Bereitstellungsdienst fällt in die Kategorie **Datenverarbeitung** der DSGVO-Klassifizierung. Als eine Datenverarbeitungs-Pipeline stellt der Dienst Datenverarbeitungsdienste für wichtige Partner und Endbenutzer bereit. Der Azure AD-Bereitstellungsdienst generiert keine Benutzerdaten und kann nicht unabhängig steuern, welche personenbezogenen Daten erfasst und wie diese verwendet werden. Datenabruf, Aggregation, Analyse und Berichterstellung im Azure AD-Bereitstellungsdienst basieren auf vorhandenen Unternehmensdaten.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-hybrid-note.md)]

In Bezug auf die Datenaufbewahrung erstellt der Azure AD-Bereitstellungsdienst keine Berichte, führt keine Analysen durch und liefert keine Einblicke über 30 Tage hinaus. Aus diesem Grund erfolgt im Azure AD-Bereitstellungsdienst keine Speicherung, Verarbeitung oder Beibehaltung von Daten über 30 Tage hinaus. Dieser Entwurf ist mit den DSGVO-Vorschriften, den Microsoft-Richtlinien für Datenschutz und Compliance und den Azure AD-Richtlinien zur Datenaufbewahrung konform.

## <a name="next-steps"></a>Nächste Schritte

* [Erfahren Sie, wie Sie Protokolle überprüfen und Berichte zu Bereitstellungsaktivitäten abrufen.](../manage-apps/check-status-user-account-provisioning.md)
* [Lesen Sie, wie Sie das einmalige Anmelden zwischen Workday und Azure Active Directory konfigurieren.](workday-tutorial.md)
* [Erfahren Sie, wie Sie andere SaaS-Anwendungen in Azure Active Directory integrieren.](tutorial-list.md)
* [Erfahren Sie, wie Sie Microsoft Graph-APIs verwenden, um die Bereitstellungskonfigurationen zu verwalten](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/synchronization-overview)
