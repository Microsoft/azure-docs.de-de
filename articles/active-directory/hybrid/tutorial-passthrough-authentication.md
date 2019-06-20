---
title: 'Tutorial:  Integrieren einer einzelnen AD-Gesamtstruktur in Azure mithilfe von PTA'
description: Veranschaulicht, wie Sie eine Hybrididentitätsumgebung mithilfe einer Pass-Through-Authentifizierung einrichten.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 05/31/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 96846d75111fe11b225704a248baeb006a3df3fb
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66473007"
---
# <a name="tutorial--integrate-a-single-ad-forest-using-pass-through-authentication-pta"></a>Tutorial:  Integrieren einer AD-Gesamtstruktur mithilfe der Pass-Through-Authentifizierung (PTA)

![Erstellen](media/tutorial-passthrough-authentication/diagram.png)

Das folgende Tutorial führt Sie durch das Erstellen einer Hybrididentitätsumgebung mithilfe einer Pass-Through-Authentifizierung.  Diese Umgebung kann dann zu Testzwecken verwendet werden, oder um sich mit der Funktionsweise einer Hybrididentität vertraut zu machen.

## <a name="prerequisites"></a>Voraussetzungen
Im Folgenden finden Sie die erforderlichen Komponenten für die Durchführung dieses Tutorials:
- Ein Computer mit installiertem [Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/hyper-v-technology-overview).  Es wird empfohlen, dies entweder auf einem [Windows 10](https://docs.microsoft.com/virtualization/hyper-v-on-windows/about/supported-guest-os)- oder einem [Windows Server 2016](https://docs.microsoft.com/windows-server/virtualization/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows)-Computer durchzuführen.
- [Ein Azure-Abonnement](https://azure.microsoft.com/free)
- - Ein [externer Netzwerkadapter](https://docs.microsoft.com/virtualization/hyper-v-on-windows/quick-start/connect-to-network), um dem virtuellen Computer die Kommunikation mit dem Internet zu ermöglichen.
- Eine Kopie von Windows Server 2016.
- Eine [benutzerdefinierte Domäne](../../active-directory/fundamentals/add-custom-domain.md), die verifiziert werden kann.

> [!NOTE]
> In diesem Tutorial werden PowerShell-Skripts verwendet, sodass Sie die Tutorialumgebung in kürzester Zeit erstellen können.  Jedes der Skripts verwendet Variablen, die am Anfang jedes Skripts deklariert werden.  Sie können und sollten die Variablen entsprechend Ihrer Umgebung ändern.
>
>Die verwendeten Skripts erstellen eine allgemeine Active Directory-Umgebung, bevor Azure AD Connect installiert wird.  Sie sind für alle Tutorials relevant.
>
> Kopien der in diesem Tutorial verwendeten PowerShell-Skripts sind unter [diesem Link](https://github.com/billmath/tutorial-phs) auf GitHub verfügbar.

## <a name="create-a-virtual-machine"></a>Erstellen eines virtuellen Computers
Um unsere Hybrididentitätsumgebung einzurichten und auszuführen, müssen wir als Erstes einen virtuellen Computer erstellen, der als unser lokaler Active Directory-Server verwendet wird.  

>[!NOTE]
>Wenn Sie noch nie ein Skript in PowerShell auf Ihrem Hostcomputer ausgeführt haben, müssen Sie vor dem Ausführen von Skripts `Set-ExecutionPolicy remotesigned` ausführen und in PowerShell mit „Ja“ antworten.

Gehen Sie wie folgt vor:

1. Öffnen Sie die PowerShell ISE als Administrator.
2. Führen Sie das folgende Skript aus.

```powershell
#Declare variables
$VMName = 'DC1'
$Switch = 'External'
$InstallMedia = 'D:\ISO\en_windows_server_2016_updated_feb_2018_x64_dvd_11636692.iso'
$Path = 'D:\VM'
$VHDPath = 'D:\VM\DC1\DC1.vhdx'
$VHDSize = '64424509440'

#Create New Virtual Machine
New-VM -Name $VMName -MemoryStartupBytes 16GB -BootDevice VHD -Path $Path -NewVHDPath $VHDPath -NewVHDSizeBytes $VHDSize  -Generation 2 -Switch $Switch  

#Set the memory to be non-dynamic
Set-VMMemory $VMName -DynamicMemoryEnabled $false

#Add DVD Drive to Virtual Machine
Add-VMDvdDrive -VMName $VMName -ControllerNumber 0 -ControllerLocation 1 -Path $InstallMedia

#Mount Installation Media
$DVDDrive = Get-VMDvdDrive -VMName $VMName

#Configure Virtual Machine to Boot from DVD
Set-VMFirmware -VMName $VMName -FirstBootDevice $DVDDrive 
```

## <a name="complete-the-operating-system-deployment"></a>Durchführen der Betriebssystembereitstellung
Um das Erstellen des virtuellen Computers durchzuführen, müssen Sie die Installation des Betriebssystems abschließen.

1. Doppelklicken Sie im Hyper-V-Manager auf den virtuellen Computer.
2. Klicken Sie auf die Schaltfläche „Starten“.
3. Sie werden zu Folgendem aufgefordert: „Drücken Sie eine beliebige Taste, um von CD oder DVD zu starten“. Fahren Sie fort, und tun Sie dies.
4. Wählen Sie auf dem Windows Server-Startbildschirm Ihre Sprache aus, und klicken Sie auf **Weiter**.
5. Klicken Sie auf **Jetzt installieren**.
6. Geben Sie Ihren Lizenzschlüssel ein, und klicken Sie auf **Weiter**.
7. Aktivieren Sie „Ich stimme den Lizenzbedingungen zu“, und klicken Sie auf **Weiter**.
8. Wählen Sie **Benutzerdefiniert:  Nur Windows installieren (Erweitert)** aus.
9. Klicken Sie unten auf der Seite auf **Weiter**
10. Nach Abschluss der Installation starten Sie den virtuellen Computer neu, melden sich an und führen Windows-Updates aus, um sicherzustellen, dass die VM auf dem neuesten Stand ist.  Installieren Sie die neuesten Updates.

## <a name="install-active-directory-prerequisites"></a>Installieren der erforderlichen Active Directory-Komponenten
Nachdem nun ein virtueller Computer ausgeführt wird, müssen wir noch ein paar Vorbereitungen vor der Installation von Active Directory treffen.  Das heißt, dass wir den virtuellen Computer umbenennen, eine statische IP-Adresse und DNS-Informationen festlegen und die Remoteserver-Verwaltungstools installieren müssen.   Gehen Sie wie folgt vor:

1. Öffnen Sie die PowerShell ISE als Administrator.
2. Führen Sie `Set-ExecutionPolicy remotesigned` aus, und beantworten Sie alles mit Ja [A].  Drücken Sie die EINGABETASTE.
3. Führen Sie das folgende Skript aus.

```powershell
#Declare variables
$ipaddress = "10.0.1.117" 
$ipprefix = "24" 
$ipgw = "10.0.1.1" 
$ipdns = "10.0.1.117"
$ipdns2 = "8.8.8.8" 
$ipif = (Get-NetAdapter).ifIndex 
$featureLogPath = "c:\poshlog\featurelog.txt" 
$newname = "DC1"
$addsTools = "RSAT-AD-Tools" 

#Set static IP address
New-NetIPAddress -IPAddress $ipaddress -PrefixLength $ipprefix -InterfaceIndex $ipif -DefaultGateway $ipgw 

# Set the DNS servers
Set-DnsClientServerAddress -InterfaceIndex $ipif -ServerAddresses ($ipdns, $ipdns2)

#Rename the computer 
Rename-Computer -NewName $newname -force 

#Install features 
New-Item $featureLogPath -ItemType file -Force 
Add-WindowsFeature $addsTools 
Get-WindowsFeature | Where installed >>$featureLogPath 

#Restart the computer 
Restart-Computer
```

## <a name="create-a-windows-server-ad-environment"></a>Erstellen einer Windows Server AD-Umgebung
Nachdem nun die VM erstellt ist, umbenannt wurde und über eine statische IP-Adresse verfügt, können wir fortfahren und die Active Directory Domain Services installieren und konfigurieren.  Gehen Sie wie folgt vor:

1. Öffnen Sie die PowerShell ISE als Administrator.
2. Führen Sie das folgende Skript aus.

```powershell 
#Declare variables
$DatabasePath = "c:\windows\NTDS"
$DomainMode = "WinThreshold"
$DomainName = "contoso.com"
$DomaninNetBIOSName = "CONTOSO"
$ForestMode = "WinThreshold"
$LogPath = "c:\windows\NTDS"
$SysVolPath = "c:\windows\SYSVOL"
$featureLogPath = "c:\poshlog\featurelog.txt" 
$Password = "Pass1w0rd"
$SecureString = ConvertTo-SecureString $Password -AsPlainText -Force

#Install AD DS, DNS and GPMC 
start-job -Name addFeature -ScriptBlock { 
Add-WindowsFeature -Name "ad-domain-services" -IncludeAllSubFeature -IncludeManagementTools 
Add-WindowsFeature -Name "dns" -IncludeAllSubFeature -IncludeManagementTools 
Add-WindowsFeature -Name "gpmc" -IncludeAllSubFeature -IncludeManagementTools } 
Wait-Job -Name addFeature 
Get-WindowsFeature | Where installed >>$featureLogPath

#Create New AD Forest
Install-ADDSForest -CreateDnsDelegation:$false -DatabasePath $DatabasePath -DomainMode $DomainMode -DomainName $DomainName -SafeModeAdministratorPassword $SecureString -DomainNetbiosName $DomainNetBIOSName -ForestMode $ForestMode -InstallDns:$true -LogPath $LogPath -NoRebootOnCompletion:$false -SysvolPath $SysVolPath -Force:$true
```

## <a name="create-a-windows-server-ad-user"></a>Erstellen eines Windows Server AD-Benutzers
Nachdem nun unsere Active Directory-Umgebung vorhanden ist, benötigen wir ein Testkonto.  Dieses Konto wird in unserer lokalen Active Directory-Umgebung erstellt und dann mit Azure AD synchronisiert.  Gehen Sie wie folgt vor:

1. Öffnen Sie die PowerShell ISE als Administrator.
2. Führen Sie das folgende Skript aus.

```powershell 
#Declare variables
$Givenname = "Allie"
$Surname = "McCray"
$Displayname = "Allie McCray"
$Name = "amccray"
$Password = "Pass1w0rd"
$Identity = "CN=ammccray,CN=Users,DC=contoso,DC=com"
$SecureString = ConvertTo-SecureString $Password -AsPlainText -Force


#Create the user
New-ADUser -Name $Name -GivenName $Givenname -Surname $Surname -DisplayName $Displayname -AccountPassword $SecureString

#Set the password to never expire
Set-ADUser -Identity $Identity -PasswordNeverExpires $true -ChangePasswordAtLogon $false -Enabled $true
```

## <a name="create-an-azure-ad-tenant"></a>Erstellen eines Azure AD-Mandanten
Jetzt müssen wir einen Azure AD-Mandanten erstellen, damit wir unsere Benutzer mit der Cloud synchronisieren können.  Gehen Sie zum Erstellen eines neuen Azure AD-Mandanten wie folgt vor:

1. Wechseln Sie zum [Azure-Portal](https://portal.azure.com), und melden Sie sich mit einem Konto an, das über ein Azure-Abonnement verfügt.
2. Wählen Sie das **Plussymbol (+)** aus, und suchen Sie nach **Azure Active Directory**.
3. Wählen Sie in den Suchergebnissen **Azure Active Directory** aus.
4. Klicken Sie auf **Erstellen**.</br>
![Erstellen](media/tutorial-password-hash-sync/create1.png)</br>
5. Geben Sie einen **Namen für die Organisation** an, zusammen mit dem **Namen der Anfangsdomäne**. Klicken Sie anschließend auf **Erstellen**. Hierdurch wird Ihr Verzeichnis erstellt.
6. Sobald dies abgeschlossen ist, klicken Sie auf **diesen Link**, um das Verzeichnis zu verwalten.

## <a name="create-a-global-administrator-in-azure-ad"></a>Erstellen eines globalen Administrators in Azure AD
Nachdem nun ein Azure AD-Mandant vorhanden ist, erstellen wir ein globales Administratorkonto.  Dieses Konto dient zum Erstellen des Azure AD Connector-Kontos während der Installation von Azure AD Connect.  Das Azure AD Connector-Konto wird zum Schreiben von Informationen in Azure AD verwendet.   Gehen Sie zum Erstellen des globalen Administratorkontos wie folgt vor:

1.  Wählen Sie unter **Verwalten** die Option **Benutzer** aus.</br>
![Erstellen](media/tutorial-password-hash-sync/gadmin1.png)</br>
2.  Wählen Sie **Alle Benutzer** und dann **+ Neuer Benutzer** aus.
3.  Geben Sie für diesen Benutzer einen Namen und Benutzernamen an. Dieser wird Ihr globaler Administrator für den Mandanten. Sie sollten außerdem die **Verzeichnisrolle** in **Globaler Administrator** ändern. Sie können auch das temporäre Kennwort anzeigen. Wenn Sie fertig sind, wählen Sie **Erstellen** aus.</br>
![Erstellen](media/tutorial-password-hash-sync/gadmin2.png)</br>
4. Sobald dies abgeschlossen ist, öffnen Sie einen neuen Webbrowser, und melden Sie sich mit dem neuen globalen Administratorkonto und dem temporären Kennwort bei „myapps.microsoft.com“ an.
5. Ändern Sie das Kennwort für den globalen Administrator in einen Wert, den Sie sich merken können.

## <a name="add-the-custom-domain-name-to-your-directory"></a>Fügen Sie Ihrem Verzeichnis den benutzerdefinierten Domänennamen hinzu.
Nachdem wir nun über einen Mandanten und einen globalen Administrator verfügen, müssen wir unsere benutzerdefinierte Domäne hinzufügen, damit Azure sie verifizieren kann.  Gehen Sie wie folgt vor:

1. Stellen Sie im [Azure-Portal](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) sicher, dass das Blatt **Alle Benutzer** geschlossen ist.
2. Wählen Sie links **Benutzerdefinierte Domänennamen**.
3. Wählen Sie **Benutzerdefinierte Domäne hinzufügen**.</br>
![Benutzerdefiniert](media/tutorial-federation/custom1.png)</br>
4. Geben Sie unter **Benutzerdefinierte Domänennamen** den Namen Ihrer benutzerdefinierten Domäne in das Feld ein, und klicken Sie auf **Domäne hinzufügen**.
5. Im Bildschirm mit dem benutzerdefinierten Domänennamen erhalten Sie entweder TXT- oder MX-Informationen.  Diese Informationen müssen den DNS-Informationen der Domänenregistrierungsstelle unter Ihrer Domäne hinzugefügt werden.  Sie müssen also zu Ihrer Domänenregistrierungsstelle wechseln und dort entweder die TXT- oder die MX-Informationen in den DNS-Einstellungen für Ihre Domäne eingeben.  Dies gestattet Azure die Verifizierung Ihrer Domäne.  Es kann bis zu 24 Stunden dauern, bis Azure die Verifizierung abgeschlossen hat.  Weitere Informationen finden Sie in der Dokumentation unter [Hinzufügen einer benutzerdefinierten Domäne](../../active-directory/fundamentals/add-custom-domain.md).</br>
![Benutzerdefiniert](media/tutorial-federation/custom2.png)</br>
6. Klicken Sie auf die Schaltfläche „Überprüfen“, um sicherzustellen, dass sie verifiziert wurde.</br>
![Benutzerdefiniert](media/tutorial-federation/custom3.png)</br>

## <a name="download-and-install-azure-ad-connect"></a>Herunterladen und Installieren von Azure AD Connect
Jetzt müssen Sie Azure AD Connect herunterladen und installieren.  Nachdem es installiert wurde, führen Sie die Expressinstallation durch.  Gehen Sie wie folgt vor:

1. Laden Sie [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) herunter.
2. Navigieren Sie zu **AzureADConnect.msi**, und doppelklicken Sie darauf.
3. Aktivieren Sie auf der Willkommensseite das Kontrollkästchen zum Zustimmen zu den Lizenzbedingungen, und klicken Sie auf **Weiter**.  
4. Klicken Sie im Bildschirm „Expresseinstellungen“ auf **Anpassen**.  
5. Im Bildschirm zum Installieren der erforderlichen Komponenten. Klicken Sie auf **Installieren**.  
6. Wählen Sie auf dem Bildschirm „Benutzeranmeldung“ **Passthrough-Authentifizierung** und **Einmaliges Anmelden aktivieren** aus, und klicken Sie auf **Weiter**.</br>
![PTA](media/tutorial-passthrough-authentication/pta1.png)</b>
7. Geben Sie im Bildschirm „Mit Azure AD verbinden“ den Benutzernamen und das Kennwort des globalen Administrators ein, den wir weiter oben erstellt haben, und klicken Sie auf **Weiter**.
2. Klicken Sie im Bildschirm „Verzeichnisse verbinden“ auf **Verzeichnis hinzufügen**.  Wählen Sie dann **Neues AD-Konto erstellen** aus, und geben Sie den contoso\Administrator-Benutzernamen und das Kennwort ein, und klicken Sie auf **OK**.
3. Klicken Sie auf **Weiter**.
4. Wählen Sie im Azure AD-Anmeldebildschirm **Ohne Abgleich aller UPN-Suffixe mit überprüften Domänen fortfahren** aus, und klicken Sie auf **Weiter**.
5. Klicken Sie im Bildschirm „Filtern von Domänen und Organisationseinheiten“ auf **Weiter**.
6. Klicken Sie im Bildschirm „Ihre Benutzer werden eindeutig identifiziert“ auf **Weiter**.
7. Klicken Sie im Bildschirm „Benutzer und Geräte filtern“ auf **Weiter**.
8. Klicken Sie im Bildschirm „Optionale Features“ auf **Weiter**.
9. Geben Sie auf der Seite „Anmeldeinformationen für einmaliges Anmelden aktivieren“ den contoso\Administrator-Benutzernamen und das Kennwort ein, und klicken Sie auf **Weiter**.
10. Klicken Sie im Bildschirm "Bereit zur Konfiguration" auf **Installieren**.
11. Klicken Sie nach Abschluss der Installation auf **Beenden**.
12. Melden Sie sich nach Abschluss der Installation ab und wieder an, ehe Sie den Synchronization Service Manager oder Synchronisierungsregel-Editor verwenden.


## <a name="verify-users-are-created-and-synchronization-is-occurring"></a>Überprüfen, ob Benutzer erstellt wurden und die Synchronisierung erfolgt
Wir überprüfen jetzt, ob die Benutzer, die in unserem lokalen Verzeichnis vorhanden waren, synchronisiert wurden und jetzt im Azure AD-Mandanten vorhanden sind.  Seien Sie sich im Klaren darüber, dass dies einige Stunden dauern kann.  Gehen Sie wie folgt vor, um zu überprüfen, ob Benutzer synchronisiert werden:


1. Wechseln Sie zum [Azure-Portal](https://portal.azure.com), und melden Sie sich mit einem Konto an, das über ein Azure-Abonnement verfügt.
2. Wählen Sie im linken Bereich **Azure Active Directory** aus.
3. Wählen Sie unter **Verwalten** die Option **Benutzer** aus.
4. Überprüfen Sie, ob die neuen Benutzer in der ![Synchronisierung](media/tutorial-password-hash-sync/synch1.png) unseres Mandanten angezeigt werden.

## <a name="test-signing-in-with-one-of-our-users"></a>Testanmeldung mit einem unserer Benutzer

1. Rufen Sie [https://myapps.microsoft.com](https://myapps.microsoft.com) auf.
2. Melden Sie sich mit einem Benutzerkonto an, das in unserem neuen Mandanten erstellt wurde.  Sie müssen sich mit folgendem Format anmelden: (user@domain.onmicrosoft.com). Verwenden Sie dasselbe Kennwort, mit dem sich der Benutzer lokal anmeldet.
   ![Überprüfen](media/tutorial-password-hash-sync/verify1.png)

Sie haben nun erfolgreich eine Hybrididentitätsumgebung eingerichtet, die Sie zum Testen verwenden können, und um sich mit den Möglichkeiten von Azure vertraut zu machen.

## <a name="next-steps"></a>Nächste Schritte


- [Voraussetzungen für Azure AD Connect](how-to-connect-install-prerequisites.md) 
- [Benutzerdefinierte Einstellungen](how-to-connect-install-custom.md)
- [Passthrough-Authentifizierung](how-to-connect-pta.md)
