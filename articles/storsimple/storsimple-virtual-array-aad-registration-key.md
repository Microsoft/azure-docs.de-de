---
title: Neue Authentifizierung für StorSimple Virtual Arrays | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie die AAD-basierte Authentifizierung für Ihren Dienst verwenden, einen neuen Registrierungsschlüssel generieren und eine manuelle Registrierung der Geräte durchführen.
services: storsimple
documentationcenter: ''
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: alkohli
ms.openlocfilehash: 080f49ca1078858462264f229e9acfee6fad17d1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61387661"
---
# <a name="use-the-new-authentication-for-your-storsimple"></a>Verwenden der neuen Authentifizierung für StorSimple

## <a name="overview"></a>Übersicht

Der StorSimple-Geräte-Manager-Dienst wird in Microsoft Azure ausgeführt. Er stellt Verbindungen mit mehreren StorSimple Virtual Arrays her. Bislang hat der StorSimple-Geräte-Manager-Dienst einen Zugriffssteuerungsdienst (Access Control Service, ACS) verwendet, um den Dienst bei Ihrem StorSimple-Gerät zu authentifizieren. Der ACS-Mechanismus wird in Kürze ausgemustert und durch eine AAD-Authentifizierung (Azure Active Directory) basiert.

Die in diesem Artikel enthaltenen Informationen gelten nur für die beiden StorSimple Virtual Arrays der 1200-Serie. Der Artikel behandelt die Details der AAD-Authentifizierung und den zugeordneten neuen Dienstregistrierungsschlüssel sowie Änderungen an den Firewallregeln im Zusammenhang mit den StorSimple-Geräten.

Die AAD-Authentifizierung findet in StorSimple Virtual Arrays (Modell 1200) mit Update 1 oder einer höheren Version statt.

Die Einführung der AAD-Authentifizierung hat Änderungen in folgenden Bereichen zur Folge:

- URL-Muster für Firewallregeln
- Dienstregistrierungsschlüssel

Diese Änderungen werden in den folgenden Abschnitten ausführlich erläutert.

## <a name="url-changes-for-aad-authentication"></a>URL-Änderungen für AAD-Authentifizierung

Um zu gewährleisten, dass der Dienst die AAD-basierte Authentifizierung verwendet, müssen alle Benutzer die neuen Authentifizierungs-URLs in ihre Firewallregeln einschließen.

Stellen Sie bei Verwendung von StorSimple Virtual Array sicher, dass die folgende URL in den Firewallregeln enthalten ist:

| URL-Muster                         | Cloud | Komponente/Funktionalität         |
|------------------------------------|-------|---------------------------------|
| `https://login.windows.net`        | Azure – Öffentlich |AAD-Authentifizierungsdienst      |
| `https://login.microsoftonline.us` | US Government |AAD-Authentifizierungsdienst      |

Eine vollständige Liste mit URL-Mustern für StorSimple Virtual Arrays finden Sie unter [URL-Muster für Firewallregeln](storsimple-ova-system-requirements.md#url-patterns-for-firewall-rules).

Wenn die Authentifizierungs-URL nicht über den Ausmusterungstermin hinaus in den Firewallregeln enthalten ist, wird Benutzern eine kritische Warnung mit dem Hinweis angezeigt, dass ihr StorSimple-Gerät sich nicht beim Dienst authentifizieren konnte. Der Dienst kann nicht mit dem Gerät kommunizieren. Wenn die Benutzer diese Warnung erhalten, müssen sie die neue Authentifizierungs-URL aufnehmen. Weitere Informationen zu der Warnung finden Sie unter [Verwenden des StorSimple-Geräte-Managers zum Verwalten von Warnungen für StorSimple Virtual Array](storsimple-virtual-array-manage-alerts.md#networking-alerts).

## <a name="device-version-and-authentication-changes"></a>Änderungen für Geräteversion und Authentifizierung

Ermitteln Sie bei Verwendung eines StorSimple Virtual Arrays anhand der folgenden Tabelle, welche Aktion Sie abhängig von der verwendeten Gerätesoftwareversion ausführen müssen.

| Geräteszenario  | Auszuführende Aktion                                    |
|----------------------------|--------------------------------------------------------------|
| Das Gerät verfügt mindestens über Update 1.0 und ist offline. <br> Eine Warnung mit dem Hinweis, dass die URL nicht in der Zulassungsliste enthalten ist, wird angezeigt.| 1. Ändern Sie die Firewallregeln, um die Authentifizierungs-URL einzuschließen. Siehe [Authentifizierungs-URLs](#url-changes-for-aad-authentication). <br> 2. [Rufen Sie den AAD-Registrierungsschlüssel aus dem Dienst ab](#aad-based-registration-keys). <br> 3. Führen Sie zum [Herstellen einer Verbindung mit der Windows PowerShell-Schnittstelle des virtuellen Arrays](storsimple-virtual-array-deploy2-provision-hyperv.md#step-2-provision-a-virtual-array-in-hypervisor) die Schritte 1 bis 5 aus.<br> 4. Verwenden Sie das Cmdlet `Invoke-HcsReRegister`, um das Gerät über Windows PowerShell zu registrieren. Geben Sie den Schlüssel an, den Sie im vorherigen Schritt abgerufen haben.|
| Das Gerät verfügt mindestens über Update 1.0 und ist online.| Es ist keine Aktion erforderlich.                                       |
| Das Gerät verfügt maximal über Update 0.6 und ist offline. | 1. [Laden Sie Update 1.0 über den Katalogserver herunter.](storsimple-virtual-array-install-update-1.md#download-the-update-or-the-hotfix)<br>2. [Wenden Sie Update 1.0 über die lokale Webbenutzeroberfläche an.](storsimple-virtual-array-install-update-1.md#install-the-update-or-the-hotfix)<br>3. [Rufen Sie den AAD-Registrierungsschlüssel aus dem Dienst ab](#aad-based-registration-keys). <br>4. Führen Sie zum [Herstellen einer Verbindung mit der Windows PowerShell-Schnittstelle des virtuellen Arrays](storsimple-virtual-array-deploy2-provision-hyperv.md#step-2-provision-a-virtual-array-in-hypervisor) die Schritte 1 bis 5 aus.<br>5. Verwenden Sie das Cmdlet `Invoke-HcsReRegister`, um das Gerät über Windows PowerShell zu registrieren. Geben Sie den Schlüssel an, den Sie im vorherigen Schritt abgerufen haben.|
| Das Gerät verfügt maximal über Update 0.6 und ist online. | Ändern Sie die Firewallregeln, um die Authentifizierungs-URL einzuschließen.<br> Installieren Sie Update 1.0 über das Azure-Portal. |

## <a name="aad-based-registration-keys"></a>AAD-basierte Registrierungsschlüssel

Ab Update 1.0 für StorSimple Virtual Arrays werden neue AAD-basierte Registrierungsschlüssel verwendet. Die Registrierungsschlüssel dienen dazu, Ihren StorSimple-Geräte-Manager-Dienst bei dem Gerät zu registrieren.

Bei Verwendung eines StorSimple Virtual Arrays mit Update 0.6 oder einer älteren Version können Sie die neuen AAD-Dienstregistrierungsschlüssel nicht verwenden. Der Dienstregistrierungsschlüssel muss neu generiert werden. Wenn Sie den Schlüssel neu generieren, wird der neue Schlüssel für die Registrierung aller weiteren Geräte verwendet. Der alte Schlüssel ist dann nicht mehr gültig.

- Der neue AAD-Registrierungsschlüssel läuft nach drei Tagen ab.
- Die AAD-Registrierungsschlüssel können nur für StorSimple Virtual Arrays der 1200-Serie verwendet werden, die mindestens über Update 1 verfügen. Der AAD-Registrierungsschlüssel eines StorSimple-Geräts der 8000-Serie funktioniert nicht.
- Die AAD-Registrierungsschlüssel sind länger als die entsprechenden ACS-Registrierungsschlüssel.

Führen Sie die folgenden Schritte aus, um einen AAD-Dienstregistrierungsschlüssel zu generieren.

#### <a name="to-generate-the-aad-service-registration-key"></a>So generieren Sie den AAD-Dienstregistrierungsschlüssel

1. Navigieren Sie in **StorSimple-Geräte-Manager** zu **Verwaltung &gt;** **Schlüssel**.
    
    ![Navigieren zu „Schlüssel“](./media/storsimple-virtual-array-aad-registration-key/aad-registration-key1.png)

2. Klicken Sie auf **Schlüssel generieren**.

    ![Klicken Sie auf „Neu generieren“.](./media/storsimple-virtual-array-aad-registration-key/aad-click-generate-registration-key.png)

3. Kopieren Sie den neuen Schlüssel. Der ältere Schlüssel funktioniert nicht mehr.

    ![Bestätigen des erneuten Generierens](./media/storsimple-virtual-array-aad-registration-key/aad-registration-key2.png)

## <a name="next-steps"></a>Nächste Schritte

* Erfahren Sie, wie Sie [StorSimple Virtual Array](storsimple-virtual-array-deploy1-portal-prep.md) bereitstellen.
