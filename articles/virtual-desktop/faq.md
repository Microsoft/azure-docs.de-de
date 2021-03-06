---
title: Häufig gestellte Fragen zu Windows Virtual Desktop – Azure
description: Häufig gestellte Fragen und Best Practices für Windows Virtual Desktop.
author: Heidilohr
ms.topic: conceptual
ms.date: 03/09/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 1f5e4cb0d2db30c6b07370be137506f3fe26837f
ms.sourcegitcommit: c2a41648315a95aa6340e67e600a52801af69ec7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106505296"
---
# <a name="windows-virtual-desktop-faq"></a>Häufig gestellte Fragen zu Windows Virtual Desktop

In diesem Artikel werden häufig gestellte Fragen zu Windows Virtual Desktop beantwortet und Best Practices erläutert.

## <a name="what-are-the-minimum-admin-permissions-i-need-to-manage-objects"></a>Welche Administratorberechtigungen benötige ich mindestens, um Objekte zu verwalten?

Wenn Sie Hostpools und andere Objekte erstellen möchten, muss Ihnen die Rolle „Mitwirkender“ in dem Abonnement oder der Ressourcengruppe zugewiesen sein, mit dem bzw. der Sie arbeiten.

Ihnen muss die Rolle „Benutzerzugriffsadministrator“ in einer App-Gruppe zugewiesen sein, damit Sie App-Gruppen für Benutzer oder Benutzergruppen veröffentlichen können.

Um die Berechtigung eines Administrators auf die Verwaltung von Benutzersitzungen zu beschränken – z. B. zum Senden von Nachrichten an Benutzer, zum Abmelden von Benutzern usw. –, können Sie benutzerdefinierte Rollen erstellen. Beispiel:

```powershell
"actions": [
"Microsoft.Resources/deployments/operations/read",
"Microsoft.Resources/tags/read",
"Microsoft.Authorization/roleAssignments/read",
"Microsoft.DesktopVirtualization/hostpools/sessionhosts/usersessions/*",
"Microsoft.DesktopVirtualization/hostpools/sessionhosts/read",
"Microsoft.DesktopVirtualization/hostpools/sessionhosts/write"
],
"notActions": [],
"dataActions": [],
"notDataActions": []
}
```

## <a name="does-windows-virtual-desktop-support-split-azure-active-directory-models"></a>Unterstützt Windows Virtual Desktop das Aufteilen von Azure Active Directory-Modellen?

Wenn ein Benutzer einer App-Gruppe zugewiesen ist, führt der Dienst eine einfache Azure-Rollenzuweisung durch. Folglich müssen sich die Azure AD-Instanz des Benutzers und die Azure AD-Instanz der App-Gruppe am selben Standort befinden. Alle Dienstobjekte, wie z. B. Hostpools, App-Gruppen und Arbeitsbereiche, müssen sich ebenfalls in derselben Azure AD-Instanz befinden wie der Benutzer.

Virtuelle Computer können in einer anderen Azure AD-Instanz erstellt werden, sofern Sie diese Instanz mit der Instanz des Benutzers im selben virtuellen Netzwerk synchronisieren.

## <a name="what-are-location-restrictions"></a>Was sind Standorteinschränkungen?

Allen Dienstressourcen ist ein Standort zugeordnet. Der Standort eines Hostpools bestimmt, in welcher Geografie die Dienstmetadaten für den Hostpool gespeichert werden. Eine App-Gruppe kann nicht ohne Hostpool existieren. Wenn Sie Apps zu einer RemoteApp-App-Gruppe hinzufügen, benötigen Sie auch einen Sitzungshost, um die Startmenü-Apps zu bestimmen. Sie benötigen zudem für alle App-Gruppenaktionen entsprechenden Datenzugriff auf den Hostpool. Um sicherzustellen, dass Daten nicht zwischen mehreren Standorten übertragen werden, muss sich die App-Gruppe am selben Standort befinden wie der Hostpool.

Arbeitsbereiche müssen sich ebenfalls am selben Standort befinden wie die zugehörigen App-Gruppen. Wenn ein Arbeitsbereich aktualisiert wird, wird auch die zugehörige App-Gruppe aktualisiert. Ebenso wie bei App-Gruppen erfordert der Dienst, dass alle Arbeitsbereiche App-Gruppen zugeordnet werden, die am selben Standort erstellt wurden.

## <a name="how-do-you-expand-an-objects-properties-in-powershell"></a>Wie erweitere ich die Eigenschaften eines Objekts in PowerShell?

Wenn Sie ein PowerShell-Cmdlet ausführen, sehen Sie nur den Ressourcennamen und den Standort.

Beispiel:

```powershell
Get-AzWvdHostPool -Name 0224hp -ResourceGroupName 0224rg

Location Name   Type
-------- ----   ----
westus   0224hp Microsoft.DesktopVirtualization/hostpools
```

Um alle Eigenschaften einer Ressource anzuzeigen, fügen Sie am Ende des Cmdlets entweder `format-list` oder `fl` hinzu.

Beispiel:

```powershell
Get-AzWvdHostPool -Name 0224hp -ResourceGroupName 0224rg |fl
```

Um bestimmte Eigenschaften anzuzeigen, fügen Sie die entsprechenden Eigenschaftennamen nach `format-list` oder `fl` hinzu.

Beispiel:

```powershell
Get-AzWvdHostPool -Name demohp -ResourceGroupName 0414rg |fl CustomRdpProperty

CustomRdpProperty : audiocapturemode:i:0;audiomode:i:0;drivestoredirect:s:;redirectclipboard:i:1;redirectcomports:i:0;redirectprinters:i:1;redirectsmartcards:i:1;screen modeid:i:2;
```

## <a name="does-windows-virtual-desktop-support-guest-users"></a>Unterstützt Windows Virtual Desktop Gastbenutzer?

Windows Virtual Desktop unterstützt keine Azure AD-Gastbenutzerkonten. Ein Beispiel: Einige Gastbenutzer verfügen über Pro-Benutzer-Lizenzen für Microsoft 365 E3, Pro-Benutzer-Lizenzen für Windows E3 oder WIN VDA-Lizenzen in ihrem eigenen Unternehmen, sind aber Gastbenutzer in der Azure AD-Instanz eines anderen Unternehmens. In diesem Fall verwaltet das andere Unternehmen die Benutzerobjekte der Gastbenutzer sowohl in Azure AD als auch in Active Directory wie lokale Konten.

Sie können nicht Ihre eigenen Lizenzen zugunsten einer dritten Partei verwenden. Windows Virtual Desktop bietet derzeit keine Unterstützung für MSA (Microsoft-Konto).

## <a name="why-dont-i-see-the-client-ip-address-in-the-wvdconnections-table"></a>Warum wird mir die Client-IP-Adresse in der WVDConnections-Tabelle nicht angezeigt?

Wir verfügen derzeit über keine zuverlässige Möglichkeit, die IP-Adressen von Webclients zu erfassen, daher ist dieser Wert in der Tabelle nicht enthalten.

## <a name="how-does-windows-virtual-desktop-handle-backups"></a>Wie verarbeitet Windows Virtual Desktop Sicherungen?

In Azure gibt es mehrere Optionen, Sicherungen auszuführen. Sie können Azure Backup, Site Recovery und Momentaufnahmen verwenden.

## <a name="does-windows-virtual-desktop-support-third-party-collaboration-apps"></a>Unterstützt Windows Virtual Desktop Drittanbieter-Apps für die Zusammenarbeit?

Windows Virtual Desktop ist derzeit für Teams optimiert. Microsoft unterstützt derzeit keine Drittanbieter-Apps für die Zusammenarbeit wie z. B. Zoom. Drittanbieterorganisationen sind dafür verantwortlich, ihren Kunden Richtlinien zu Kompatibilität bereitzustellen. Auch Skype for Business wird von Windows Virtual Desktop nicht unterstützt.

## <a name="can-i-change-from-pooled-to-personal-host-pools"></a>Kann ich den Typ des Hostpools von „In Pool“ zu „Persönlich“ ändern?

Nachdem Sie einen Pool erstellt haben, können Sie seinen Typ nicht mehr ändern. Sie können jedoch VMs, die Sie bei einem Hostpool registrieren, zu einem anderen Hostpooltyp verschieben.

## <a name="whats-the-largest-profile-size-fslogix-can-handle"></a>Welches ist die größte Profilgröße, die FSLogix verarbeiten kann?

Beschränkungen oder Kontingente in FSLogix hängen von dem Speicherfabric ab, das zum Speichern der VHD- oder VHDX-Dateien mit Benutzerprofilen verwendet wird.

In der folgenden Tabelle finden Sie Beispiele dafür, wie viele IOPS ein FSLogix-Profil zur Unterstützung jedes einzelnen Benutzers benötigt. Die Anforderungen können je nach Benutzer, Anwendungen und Aktivität in jedem Profil stark variieren.

| Resource | Anforderung |
|---|---|
| IOPS im stabilen Zustand | 10 |
| IOPS für Anmelde-/Abmeldevorgänge | 50 |

Das Beispiel in dieser Tabelle gilt für einen einzelnen Benutzer, kann jedoch zur Einschätzung der Anforderungen für die Gesamtzahl von Benutzern in Ihrer Umgebung herangezogen werden. Für 100 Benutzer würden Sie beispielsweise etwa 1.000 IOPS im stabilen Zustand und 5.000 IOPS bei Anmelde- und Abmeldevorgänge benötigen.

## <a name="is-there-a-scale-limit-for-host-pools-created-in-the-azure-portal"></a>Gibt es ein Skalierungslimit für im Azure-Portal erstellte Hostpools?

Die folgenden Faktoren können sich auf das Skalierungslimit für Hostpools auswirken:

- Die Azure-Vorlage ist auf 800 Objekte begrenzt. Weitere Informationen finden Sie unter [Grenzwerte für Azure-Abonnements und Dienste, Kontingente und Einschränkungen](../azure-resource-manager/management/azure-subscription-service-limits.md#template-limits). Jede VM erstellt zusätzlich etwa sechs Objekte, daher können Sie bei jeder Ausführung der Vorlage etwa 132 VMs erstellen.

- Auch die Anzahl von Kernen, die Sie pro Region und Abonnement erstellen können, ist begrenzt. In einem Enterprise Agreement-Abonnement können Sie z. B. 350 Kerne erstellen. Diese Zahl von 350 Kernen müssen Sie durch die Standardanzahl von Kernen pro VM oder Ihren eigenen Grenzwert für Kerne dividieren, um zu ermitteln, wie viele VMs Sie bei jeder Ausführung der Vorlage erstellen können. Mehr dazu erfahren Sie unter [Grenzwerte für virtuelle Computer – Azure Resource Manager](../azure-resource-manager/management/azure-subscription-service-limits.md#virtual-machines-limits---azure-resource-manager).

- Der VM-Präfixname umfasst zusammen mit der VM-Nummer weniger als 15 Zeichen. Weitere Informationen finden Sie unter [Benennungsregeln und -einschränkungen für Azure-Ressourcen](../azure-resource-manager/management/resource-name-rules.md#microsoftcompute).

## <a name="can-i-manage-windows-virtual-desktop-environments-with-azure-lighthouse"></a>Kann ich Windows Virtual Desktop-Umgebungen mit Azure Lighthouse verwalten?

Azure Lighthouse bietet keine vollständige Unterstützung für die Verwaltung von Windows Virtual Desktop-Umgebungen. Da Lighthouse derzeit keine Benutzerverwaltung über mehrere Azure AD-Mandanten hinweg unterstützt, müssen sich Lighthouse-Kunden weiterhin bei der Azure AD-Instanz anmelden, die sie zum Verwalten von Benutzern verwenden.

Es ist auch nicht möglich, CSP-Sandboxabonnements mit dem Windows Virtual Desktop-Dienst zu verwenden. Weitere Informationen finden Sie unter [Integration Sandbox-Konto](/partner-center/develop/set-up-api-access-in-partner-center#integration-sandbox-account).

Wenn Sie den Ressourcenanbieter mit dem CSP-Besitzerkonto aktiviert haben, kann dieser durch die CSP-Kundenkonten nicht geändert werden.

## <a name="how-often-should-i-turn-my-vms-on-to-prevent-registration-issues"></a>Wie oft sollte ich meine VMs aktivieren, um Registrierungsprobleme zu vermeiden?

Nachdem Sie einen virtuellen Computer in einem Hostpool Windows Virtual Desktop-Diensts registriert haben, aktualisiert der Agent das Token der VM regelmäßig, wenn der virtuelle Computer aktiv ist. Das Zertifikat für das Registrierungstoken ist 90 Tage lang gültig. Aufgrund dieses Grenzwerts von 90 Tagen wird empfohlen, dass virtuelle Computer alle 90 Tage für 20 Minuten online geschaltet werden, damit der Computer seine Token aktualisieren und den Agent sowie parallele Stapelkomponenten aktualisieren kann. Wenn Sie Ihre VM innerhalb dieses Zeitraums aktivieren, wird verhindert, dass das Registrierungstoken abläuft oder ungültig wird. Wenn Sie Ihre VM nach mehr als 90 Tagen gestartet haben und Registrierungsprobleme auftreten, befolgen Sie die Anweisungen im [Leitfaden zur Problembehandlung für den Windows Virtual Desktop-Agent](troubleshoot-agent.md#your-issue-isnt-listed-here-or-wasnt-resolved), um die VM aus dem Hostpool zu entfernen, den Agent neu zu installieren und ihn erneut beim Pool zu registrieren.

## <a name="can-i-set-availability-options-when-creating-host-pools"></a>Kann ich beim Erstellen von Hostpools Verfügbarkeitsoptionen festlegen?

Ja. Beim Erstellen einer VM können Sie bei Windows Virtual Desktop-Hostpools entweder eine Verfügbarkeitsgruppe oder Verfügbarkeitszonen auswählen. Diese Verfügbarkeitsoptionen sind identisch mit den von Azure Compute verwendeten. Wenn Sie eine Zone für die in einem Hostpool erstellte VM auswählen, gilt die Einstellung automatisch für alle VMs, die Sie in dieser Zone erstellen. Wenn Sie Ihre Hostpool-VMs lieber auf mehrere Zonen verteilen möchten, müssen Sie die Anweisungen unter [Hinzufügen von virtuellen Computern mit dem Azure-Portal](expand-existing-host-pool.md#add-virtual-machines-with-the-azure-portal) befolgen, um manuell eine neue Zone für jede neu erstellte VM auszuwählen.

## <a name="which-availability-option-is-best-for-me"></a>Welche Verfügbarkeitsoption eignet sich am besten für mich?

Welche Verfügbarkeitsoption Sie für Ihre VMs verwenden sollten, hängt vom Speicherort Ihres Images und seinen verwalteten Datenträgerfeldern ab. In der folgenden Tabelle wird der Zusammenhang zwischen den einzelnen Einstellungen und diesen Variablen erläutert, damit Sie ermitteln können, welche Option für Ihre Bereitstellung am besten geeignet ist. 

| Verfügbarkeitsoption | Imagespeicherort | Optionsfeld „Verwalteten Datenträger verwenden“ |
|---|---|---|
| Keine | Galerie | Deaktiviert mit „Ja“ als Standardeinstellung |
| Keine | Blob-Speicher | Aktiviert mit „Nein“ als Standardeinstellung |
| Verfügbarkeitszone | Katalog (Blobspeicheroption deaktiviert) | Deaktiviert mit „Ja“ als Standardeinstellung |
| Verfügbarkeitsgruppe mit verwalteter SKU (verwalteter Datenträger) | Galerie | Deaktiviert mit „Ja“ als Standardeinstellung |
| Verfügbarkeitsgruppe mit verwalteter SKU (verwalteter Datenträger) | Blob-Speicher | Aktiviert mit „Nein“ als Standardeinstellung |
| Verfügbarkeitsgruppe mit verwalteter SKU (verwalteter Datenträger) | Blobspeicher (Katalogoption deaktiviert) | Deaktiviert mit „Nein“ als Standardeinstellung |
| Verfügbarkeitsgruppe (von Benutzer neu erstellt) | Galerie | Deaktiviert mit „Ja“ als Standardeinstellung |
| Verfügbarkeitsgruppe (von Benutzer neu erstellt) | Blob-Speicher | Aktiviert mit „Nein“ als Standardeinstellung |
