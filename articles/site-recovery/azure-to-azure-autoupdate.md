---
title: Automatische Updates von Mobility Service bei der Notfallwiederherstellung zwischen Azure-Regionen | Microsoft-Dokumentation
description: Überblick über automatische Updates von Mobility Service bei der Replikation virtueller Azure-Computer mit Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 05/20/2019
ms.author: rajanaki
ms.openlocfilehash: 1d36145b2a38c0f1106b4468eab226996e270ae1
ms.sourcegitcommit: d73c46af1465c7fd879b5a97ddc45c38ec3f5c0d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65922101"
---
# <a name="automatic-update-of-the-mobility-service-in-azure-to-azure-replication"></a>Automatische Updates von Mobility Service bei der Replikation zwischen Azure-Regionen

Jeden Monat werden Azure Site Recovery-Releases veröffentlicht, um Probleme zu beheben, Funktionen zu verbessern und neue Funktion hinzuzufügen. Wenn Sie immer die aktuellste Version des Dienstes verwenden möchten, sollten Sie jeden Monat Patchimplementierungen einplanen. Wenn Sie sich nicht um jedes Update kümmern möchten, können Sie stattdessen Azure Site Recovery die Komponentenupdates verwalten lassen.

Wie unter [Architektur der Notfallwiederherstellung von Azure zu Azure](azure-to-azure-architecture.md) beschrieben, wird beim Replizieren virtueller Computer von einer Azure-Region in einer anderen Mobility Service auf allen Azure-VMs installiert. Wenn Sie automatische Updates verwenden, wird Mobility Service durch jedes neue Release aktualisiert.
 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="how-automatic-updates-work"></a>Funktionsweise automatischer Updates

Wenn Site Recovery Ihre Updates verwaltet, wird ein globales Runbook (das von Azure-Diensten verwendet wird) über ein Automatisierungskonto bereitgestellt, das im selben Abonnement wie der Tresor erstellt wird. Jeder Tresor verwendet ein Automatisierungskonto. Das Runbook überprüft für jeden virtuellen Computer in einem Tresor, ob automatische Updates aktiviert sind, und aktualisiert die Erweiterung Mobility Service wenn eine neuere Version verfügbar ist.

Standardmäßig führt das Runbook dies jeden Tag um 0:00 Uhr in der Zeitzone des geografischen Raums der replizierten VM durch. Sie können diese Zeit über das Automatisierungskonto anpassen.

> [!NOTE]
> Ab dem Updaterollup 35 können Sie ein vorhandenes Automatisierungskonto auswählen, um es für Updates zu verwenden. Vor diesem Update wurde dieses Konto standardmäßig von Site Recovery erstellt. Diese Option ist verfügbar, wenn Sie die Replikation für einen virtuellen Computer aktivieren. Wenn Sie die Einstellung ändern, gilt sie für alle Azure-VMs, die in demselben Tresor geschützt werden.
 
> Das Aktivieren automatischer Updates erfordert keinen Neustart der Azure-VMs und hat keinen Einfluss auf eine laufende Replikation.

> Die Abrechnung des Automatisierungskontos erfolgt auf Grundlage der Gesamtzahl an Minuten, die pro Monat zur Auftragsausführung verwendet wurden. Standardmäßig verfügt jedes Automatisierungskonto über 500 kostenlose Minuten. Die Auftragsausführung kann jeden Tag zwischen wenigen Sekunden und einer Minute in Anspruch nehmen und ist in den kostenlosen Einheiten enthalten.

| Inbegriffenen kostenlosen Einheiten (pro Monat) | Preis |
|---|---|
| 500 Minuten Auftragsausführung | ₹0,14/Minute

## <a name="enable-automatic-updates"></a>Aktivieren automatischer Updates

Sie können das Verwalten von Updates durch Site Recovery folgendermaßen zulassen:

### <a name="manage-as-part-of-the-enable-replication-step"></a>Verwaltung im Rahmen der Aktivieren der Replikation

Wenn Sie die Replikation für einen virtuellen Computer aktivieren, indem Sie entweder [von der Ansicht der VM](azure-to-azure-quickstart.md) oder [vom Recovery Services-Tresor](azure-to-azure-how-to-enable-replication.md) aus starten, können Sie optional auswählen, entweder das Verwalten von Updates für die Site Recovery-Erweiterung durch Site Recovery zuzulassen oder diese manuell zu verwalten.

![Erweiterungseinstellungen](./media/azure-to-azure-autoupdate/enable-rep.png)

### <a name="toggle-the-extension-update-settings-inside-the-vault"></a>Durch Ein-/Ausschalten der Erweiterungsupdateeinstellungen im Tresor

1. Navigieren Sie im Tresor zu **Manage** > **Site Recovery Infrastructure** (Verwalten > Site Recovery-Infrastruktur).
2. Aktivieren Sie unter **For Azure Virtual Machines** > **Extension Update Settings** (Für Azure-VMs > Erweiterungsupdateinstellungen) die Option **Verwaltung durch Site Recovery zulassen**. Deaktivieren Sie diese Option, um die Verwaltung manuell durchzuführen. 
3. Wählen Sie **Speichern** aus.

![Erweiterungsupdateeinstellungen](./media/azure-to-azure-autoupdate/vault-toggle.png)

> [!Important]
> Wenn Sie **Verwaltung durch Site Recovery zulassen** auswählen, wird die Einstellung auf alle virtuellen Computer im entsprechenden Tresor angewendet.


> [!Note]
> Bei beiden Optionen werden Sie über das Automatisierungskonto informiert, das zum Verwalten der Updates verwendet wird. Wenn Sie dieses Feature zum ersten Mal in einem Tresor verwenden, wird standardmäßig ein neues Automatisierungskonto erstellt. Alternativ können Sie die Einstellung anpassen und ein vorhandenes Automatisierungskonto auswählen. Alle nachfolgend im selben Tresor aktivierten Replikationen verwenden das zuvor erstellte Konto.

Für ein benutzerdefiniertes Automatisierungskonto können Sie das folgende Skript verwenden:

```azurepowershell
param(
    [Parameter(Mandatory=$true)]
    [String] $VaultResourceId,

    [Parameter(Mandatory=$true)]
    [ValidateSet("Enabled",'Disabled')]
    [Alias("Enabled or Disabled")]
    [String] $AutoUpdateAction,

    [Parameter(Mandatory=$false)]
    [String] $AutomationAccountArmId
)

$SiteRecoveryRunbookName = "Modify-AutoUpdateForVaultForPatner"
$TaskId = [guid]::NewGuid().ToString()
$SubscriptionId = "00000000-0000-0000-0000-000000000000"
$AsrApiVersion = "2018-01-10"
$RunAsConnectionName = "AzureRunAsConnection"
$ArmEndPoint = "https://management.azure.com"
$AadAuthority = "https://login.windows.net/"
$AadAudience = "https://management.core.windows.net/"
$AzureEnvironment = "AzureCloud"
$Timeout = "160"

function Throw-TerminatingErrorMessage
{
    Param
    (
        [Parameter(Mandatory=$true)]
        [String]
        $Message
    )

    throw ("Message: {0}, TaskId: {1}.") -f $Message, $TaskId
}

function Write-Tracing
{
    Param
    (
        [Parameter(Mandatory=$true)]      
        [ValidateSet("Informational", "Warning", "ErrorLevel", "Succeeded", IgnoreCase = $true)]
        [String]
        $Level,

        [Parameter(Mandatory=$true)]
        [String]
        $Message,

        [Switch]
        $DisplayMessageToUser
    )

    Write-Output $Message

}

function Write-InformationTracing
{
    Param
    (
        [Parameter(Mandatory=$true)]
        [String]
        $Message
    )

    Write-Tracing -Message $Message -Level Informational -DisplayMessageToUser
}

function ValidateInput()
{
    try
    {
        if(!$VaultResourceId.StartsWith("/subscriptions", [System.StringComparison]::OrdinalIgnoreCase))
        {
            $ErrorMessage = "The vault resource id should start with /subscriptions."
            throw $ErrorMessage
        }

        $Tokens = $VaultResourceId.SubString(1).Split("/")
        if(!($Tokens.Count % 2 -eq 0))
        {
            $ErrorMessage = ("Odd Number of tokens: {0}." -f $Tokens.Count)
            throw $ErrorMessage
        }

        if(!($Tokens.Count/2 -eq 4))
        {
            $ErrorMessage = ("Invalid number of resource in vault ARM id expected:4, actual:{0}." -f ($Tokens.Count/2))
            throw $ErrorMessage
        }

        if($AutoUpdateAction -ieq "Enabled" -and [string]::IsNullOrEmpty($AutomationAccountArmId))
        {
            $ErrorMessage = ("The automation account ARM id should not be null or empty when AutoUpdateAction is enabled.")
            throw $ErrorMessage
        }
    }
    catch
    {
        $ErrorMessage = ("ValidateInput failed with [Exception: {0}]." -f $_.Exception)
        Write-Tracing -Level ErrorLevel -Message $ErrorMessage -DisplayMessageToUser
        Throw-TerminatingErrorMessage -Message $ErrorMessage
    }
}

function Initialize-SubscriptionId()
{
    try
    {
        $Tokens = $VaultResourceId.SubString(1).Split("/")

        $Count = 0
        $ArmResources = @{}
        while($Count -lt $Tokens.Count)
        {
            $ArmResources[$Tokens[$Count]] = $Tokens[$Count+1]
            $Count = $Count + 2
        }
        
        return $ArmResources["subscriptions"]
    }
    catch
    {
        Write-Tracing -Level ErrorLevel -Message ("Initialize-SubscriptionId: failed with [Exception: {0}]." -f $_.Exception) -DisplayMessageToUser
        throw
    }
}

function Invoke-InternalRestMethod($Uri, $Headers, [ref]$Result)
{
    $RetryCount = 0
    $MaxRetry = 3
    do
    {
        try
        {
            $ResultObject = Invoke-RestMethod -Uri $Uri -Headers $Headers    
            ($Result.Value) += ($ResultObject)
            break
        }
        catch
        {
            Write-InformationTracing ("Retry Count: {0}, Exception: {1}." -f $RetryCount, $_.Exception)
            $RetryCount++
            if(!($RetryCount -le $MaxRetry))
            {
                throw
            }

            Start-Sleep -Milliseconds 2000
        }
    }while($true)
}

function Invoke-InternalWebRequest($Uri, $Headers, $Method, $Body, $ContentType, [ref]$Result)
{
    $RetryCount = 0
    $MaxRetry = 3
    do
    {
        try
        {
            $ResultObject = Invoke-WebRequest -Uri $UpdateUrl -Headers $Header -Method 'PATCH' `
                -Body $InputJson  -ContentType "application/json" -UseBasicParsing
            ($Result.Value) += ($ResultObject)
            break
        }
        catch
        {
            Write-InformationTracing ("Retry Count: {0}, Exception: {1}." -f $RetryCount, $_.Exception)
            $RetryCount++
            if(!($RetryCount -le $MaxRetry))
            {
                throw
            }

            Start-Sleep -Milliseconds 2000
        }
    }while($true)
}

function Get-Header([ref]$Header, $AadAudience, $AadAuthority, $RunAsConnectionName){
    try 
    {
        $RunAsConnection = Get-AutomationConnection -Name $RunAsConnectionName
        $TenantId = $RunAsConnection.TenantId
        $ApplicationId = $RunAsConnection.ApplicationId
        $CertificateThumbprint = $RunAsConnection.CertificateThumbprint
        $Path = "cert:\CurrentUser\My\{0}" -f $CertificateThumbprint
        $Secret = Get-ChildItem -Path $Path
        $ClientCredential = New-Object Microsoft.IdentityModel.Clients.ActiveDirectory.ClientAssertionCertificate(
                $ApplicationId,
                $Secret)

        # Trim the forward slash from the AadAuthority if it exist.
        $AadAuthority = $AadAuthority.TrimEnd("/")
        $AuthContext = New-Object Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext(
            "{0}/{1}" -f $AadAuthority, $TenantId )
        $AuthenticationResult = $authContext.AcquireToken($AadAudience, $Clientcredential)
        $Header.Value['Content-Type'] = 'application\json'
        $Header.Value['Authorization'] = $AuthenticationResult.CreateAuthorizationHeader()
        $Header.Value["x-ms-client-request-id"] = $TaskId + "/" + (New-Guid).ToString() + "-" + (Get-Date).ToString("u")
    }
    catch
    {
        $ErrorMessage = ("Get-BearerToken: failed with [Exception: {0}]." -f $_.Exception)
        Write-Tracing -Level ErrorLevel -Message $ErrorMessage -DisplayMessageToUser
        Throw-TerminatingErrorMessage -Message $ErrorMessage
    }
}

function Get-ProtectionContainerToBeModified([ref] $ContainerMappingList)
{
    try 
    {
        Write-InformationTracing ("Get protection container mappings : {0}." -f $VaultResourceId)
        $ContainerMappingListUrl = $ArmEndPoint + $VaultResourceId + "/replicationProtectionContainerMappings" + "?api-version=" + $AsrApiVersion
        
        Write-InformationTracing ("Getting the bearer token and the header.")
        Get-Header ([ref]$Header) $AadAudience $AadAuthority $RunAsConnectionName
        
        $Result = @()
        Invoke-InternalRestMethod -Uri $ContainerMappingListUrl -Headers $header -Result ([ref]$Result)
        $ContainerMappings = $Result[0]

        Write-InformationTracing ("Total retrieved container mappings: {0}." -f $ContainerMappings.Value.Count)
        foreach($Mapping in $ContainerMappings.Value)
        {
            if(($Mapping.properties.providerSpecificDetails -eq $null) -or ($Mapping.properties.providerSpecificDetails.instanceType -ine "A2A"))
            {
                Write-InformationTracing ("Mapping properties: {0}." -f ($Mapping.properties))
                Write-InformationTracing ("Ignoring container mapping: {0} as the provider does not match." -f ($Mapping.Id))
                continue;
            }

            if($Mapping.Properties.State -ine "Paired")
            {
                Write-InformationTracing ("Ignoring container mapping: {0} as the state is not paired." -f ($Mapping.Id))
                continue;
            }

            Write-InformationTracing ("Provider specific details {0}." -f ($Mapping.properties.providerSpecificDetails))
            $MappingAutoUpdateStatus = $Mapping.properties.providerSpecificDetails.agentAutoUpdateStatus
            $MappingAutomationAccountArmId = $Mapping.properties.providerSpecificDetails.automationAccountArmId
            $MappingHealthErrorCount = $Mapping.properties.HealthErrorDetails.Count

            if($AutoUpdateAction -ieq "Enabled" -and
                ($MappingAutoUpdateStatus -ieq "Enabled") -and
                ($MappingAutomationAccountArmId -ieq $AutomationAccountArmId) -and
                ($MappingHealthErrorCount -eq 0))
            {
                Write-InformationTracing ("Provider specific details {0}." -f ($Mapping.properties))
                Write-InformationTracing ("Ignoring container mapping: {0} as the auto update is already enabled and is healthy." -f ($Mapping.Id))
                continue;
            }

            ($ContainerMappingList.Value).Add($Mapping.id)
        }
    }
    catch
    {
        $ErrorMessage = ("Get-ProtectionContainerToBeModified: failed with [Exception: {0}]." -f $_.Exception)
        Write-Tracing -Level ErrorLevel -Message $ErrorMessage -DisplayMessageToUser
        Throw-TerminatingErrorMessage -Message $ErrorMessage
    }
}

$OperationStartTime = Get-Date
$ContainerMappingList = New-Object System.Collections.Generic.List[System.String]
$JobsInProgressList = @()
$JobsCompletedSuccessList = @()
$JobsCompletedFailedList = @()
$JobsFailedToStart = 0
$JobsTimedOut = 0
$Header = @{}

$AzureRMProfile = Get-Module -ListAvailable -Name Az.Accounts | Select Name, Version, Path
$AzureRmProfileModulePath = Split-Path -Parent $AzureRMProfile.Path
Add-Type -Path (Join-Path $AzureRmProfileModulePath "Microsoft.IdentityModel.Clients.ActiveDirectory.dll")

$Inputs = ("Tracing inputs VaultResourceId: {0}, Timeout: {1}, AutoUpdateAction: {2}, AutomationAccountArmId: {3}." -f $VaultResourceId, $Timeout, $AutoUpdateAction, $AutomationAccountArmId)
Write-Tracing -Message $Inputs -Level Informational -DisplayMessageToUser
$CloudConfig = ("Tracing cloud configuration ArmEndPoint: {0}, AadAuthority: {1}, AadAudience: {2}." -f $ArmEndPoint, $AadAuthority, $AadAudience)
Write-Tracing -Message $CloudConfig -Level Informational -DisplayMessageToUser
$AutomationConfig = ("Tracing automation configuration RunAsConnectionName: {0}." -f $RunAsConnectionName)
Write-Tracing -Message $AutomationConfig -Level Informational -DisplayMessageToUser

ValidateInput
$SubscriptionId = Initialize-SubscriptionId
Get-ProtectionContainerToBeModified ([ref]$ContainerMappingList)

$Input = @{
  "properties"= @{
    "providerSpecificInput"= @{
        "instanceType" = "A2A"
        "agentAutoUpdateStatus" = $AutoUpdateAction
        "automationAccountArmId" = $AutomationAccountArmId
    }
  }
}
$InputJson = $Input |  ConvertTo-Json

if ($ContainerMappingList.Count -eq 0)
{
    Write-Tracing -Level Succeeded -Message ("Exiting as there are no container mappings to be modified.") -DisplayMessageToUser
    exit
}

Write-InformationTracing ("Container mappings to be updated has been retrieved with count: {0}." -f $ContainerMappingList.Count)

try
{
    Write-InformationTracing ("Start the modify container mapping jobs.")
    ForEach($Mapping in $ContainerMappingList)
    {
    try {
            $UpdateUrl = $ArmEndPoint + $Mapping + "?api-version=" + $AsrApiVersion
            Get-Header ([ref]$Header) $AadAudience $AadAuthority $RunAsConnectionName
            
            $Result = @()
            Invoke-InternalWebRequest -Uri $UpdateUrl -Headers $Header -Method 'PATCH' `
                -Body $InputJson  -ContentType "application/json" -Result ([ref]$Result)
            $Result = $Result[0]

            $JobAsyncUrl = $Result.Headers['Azure-AsyncOperation']
            Write-InformationTracing ("The modify container mapping job invoked with async url: {0}." -f $JobAsyncUrl)
            $JobsInProgressList += $JobAsyncUrl;

            # Rate controlling the set calls to maximum 60 calls per minute.
            # ASR throttling for set calls is 200 in 1 minute.
            Start-Sleep -Milliseconds 1000
        }
        catch{
            Write-InformationTracing ("The modify container mappings job creation failed for: {0}." -f $Ru)
            Write-InformationTracing $_
            $JobsFailedToStart++
        }
    }

    Write-InformationTracing ("Total modify container mappings has been initiated: {0}." -f $JobsInProgressList.Count)
}
catch
{
    $ErrorMessage = ("Modify container mapping jobs failed with [Exception: {0}]." -f $_.Exception)
    Write-Tracing -Level ErrorLevel -Message $ErrorMessage -DisplayMessageToUser
    Throw-TerminatingErrorMessage -Message $ErrorMessage
}

try
{
    while($JobsInProgressList.Count -ne 0)
    {
        Sleep -Seconds 30
        $JobsInProgressListInternal = @()
        ForEach($JobAsyncUrl in $JobsInProgressList)
        {
            try
            {
                Get-Header ([ref]$Header) $AadAudience $AadAuthority $RunAsConnectionName
                $Result = Invoke-RestMethod -Uri $JobAsyncUrl -Headers $header
                $JobState = $Result.Status
                if($JobState -ieq "InProgress")
                {
                    $JobsInProgressListInternal += $JobAsyncUrl
                }
                elseif($JobState -ieq "Succeeded" -or `
                    $JobState -ieq "PartiallySucceeded" -or `
                    $JobState -ieq "CompletedWithInformation")
                {
                    Write-InformationTracing ("Jobs succeeded with state: {0}." -f $JobState)
                    $JobsCompletedSuccessList += $JobAsyncUrl
                }
                else
                {
                    Write-InformationTracing ("Jobs failed with state: {0}." -f $JobState)
                    $JobsCompletedFailedList += $JobAsyncUrl
                }
            }
            catch
            {
                Write-InformationTracing ("The get job failed with: {0}. Ignoring the exception and retrying the next job." -f $_.Exception)

                # The job on which the tracking failed, will be considered in progress and tried again later.
                $JobsInProgressListInternal += $JobAsyncUrl
            }

            # Rate controlling the get calls to maximum 120 calls each minute.
            # ASR throttling for get calls is 10000 in 60 minutes.
            Start-Sleep -Milliseconds 500
        }

        Write-InformationTracing ("Jobs remaining {0}." -f $JobsInProgressListInternal.Count)

        $CurrentTime = Get-Date
        if($CurrentTime -gt $OperationStartTime.AddMinutes($Timeout))
        {
            Write-InformationTracing ("Tracing modify cloud pairing jobs has timed out.")
            $JobsTimedOut = $JobsInProgressListInternal.Count
            $JobsInProgressListInternal = @()
        }

        $JobsInProgressList = $JobsInProgressListInternal
    }
}
catch
{
    $ErrorMessage = ("Tracking modify cloud pairing jobs failed with [Exception: {0}]." -f $_.Exception)
    Write-Tracing -Level ErrorLevel -Message $ErrorMessage  -DisplayMessageToUser
    Throw-TerminatingErrorMessage -Message $ErrorMessage 
}

Write-InformationTracing ("Tracking modify cloud pairing jobs completed.")
Write-InformationTracing ("Modify cloud pairing jobs success: {0}." -f $JobsCompletedSuccessList.Count)
Write-InformationTracing ("Modify cloud pairing jobs failed: {0}." -f $JobsCompletedFailedList.Count)
Write-InformationTracing ("Modify cloud pairing jobs failed to start: {0}." -f $JobsFailedToStart)
Write-InformationTracing ("Modify cloud pairing jobs timedout: {0}." -f $JobsTimedOut)

if($JobsTimedOut -gt  0)
{
    $ErrorMessage = "One or more modify cloud pairing jobs has timedout."
    Write-Tracing -Level ErrorLevel -Message ($ErrorMessage)   
    Throw-TerminatingErrorMessage -Message $ErrorMessage
}
elseif($JobsCompletedSuccessList.Count -ne $ContainerMappingList.Count)
{
    $ErrorMessage = "One or more modify cloud pairing jobs failed."
    Write-Tracing -Level ErrorLevel -Message ($ErrorMessage)
    Throw-TerminatingErrorMessage -Message $ErrorMessage
}

Write-Tracing -Level Succeeded -Message ("Modify cloud pairing completed.") -DisplayMessageToUser
```

### <a name="manage-updates-manually"></a>Manuelles Verwalten von Updates

1. Wenn es neue Updates für die Mobility Service-Instanz auf Ihrer VM gibt, wird die folgende Benachrichtigung angezeigt: „Ein neues Site Recovery-Replikations-Agent-Update ist verfügbar. Klicken Sie, um es zu installieren.“

     ![Fenster „Replizierte Elemente“](./media/vmware-azure-install-mobility-service/replicated-item-notif.png)
2. Klicken Sie auf die Benachrichtigung, um die Auswahlseite für VMs zu öffnen.
3. Wählen Sie die VM, die Sie aktualisieren möchten, und klicken Sie dann auf **OK**. Das Update für Mobility Service wird für jede ausgewählte VM durchgeführt.

     ![VM-Liste mit replizierten Elementen](./media/vmware-azure-install-mobility-service/update-okpng.png)


## <a name="common-issues-and-troubleshooting"></a>Häufige Probleme und Problembehandlungen

Wenn ein Problem mit den automatischen Updates auftritt, werden Sie mit einer Fehlermeldung unter **Konfigurationsprobleme** im Tresordashboard benachrichtigt.

Wenn Sie keine automatischen Updates aktivieren konnten, finden Sie in den häufigen Problemen und empfohlenen Maßnahmen weitere Informationen finden:

- **Fehler**: Sie verfügen nicht über die Berechtigungen, um ein ausführendes Azure-Konto (Dienstprinzipal) zu erstellen und dem Dienstprinzipal die Rolle „Mitwirkender“ zuzuweisen.

   **Empfohlene Maßnahme:** Achten Sie darauf, dass das angemeldete Konto als Mitwirkender zugewiesen ist, und versuchen Sie es erneut. Im Abschnitt mit den erforderlichen Berechtigungen unter [Erstellen einer Azure AD-Anwendung und eines Dienstprinzipals mit Ressourcenzugriff über das Portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions) finden Sie weitere Informationen zum Zuweisen von Berechtigungen.
 
   Die meisten Probleme nach dem Aktivieren von automatischen Updates können Sie mit **Reparieren** beheben. Für den Fall, dass die Schaltfläche „Reparieren“ nicht verfügbar ist, beachten Sie die Fehlermeldung, die im Bereich „Erweiterungsupdateeinstellungen“ angezeigt wird.

   ![Schaltfläche „Reparieren“ im Site Recovery-Dienst in den Erweiterungsupdateeinstellungen](./media/azure-to-azure-autoupdate/repair.png)

- **Fehler**: Das ausführende Konto verfügt nicht über die Berechtigung zum Zugriff auf die Recovery Services-Ressource.

    **Empfohlene Maßnahme:** Löschen Sie das Konto, und [erstellen Sie das ausführende Konto neu](https://docs.microsoft.com/azure/automation/automation-create-runas-account). Stellen Sie alternativ sicher, dass das ausführende Automatisierungskonto der Azure Active Directory-Anwendung Zugriff auf die Recovery Services-Ressource hat.

- **Fehler**: Das ausführende Konto wurde nicht gefunden. Eine der folgenden Komponenten wurde gelöscht oder nicht erstellt – Azure Active Directory-Anwendung, Dienstprinzipal, Rolle, Automation-Zertifikatasset, Automation-Verbindungsasset – oder der Fingerabdruck im Zertifikat ist nicht identisch mit dem der Verbindung. 

    **Empfohlene Maßnahme:** Löschen Sie das Konto, und [erstellen Sie das ausführende Konto neu](https://docs.microsoft.com/azure/automation/automation-create-runas-account).

-  **Fehler**: Das vom Automatisierungskonto verwendete Zertifikat für das ausführende Azure-Konto wird bald ablaufen. 

    Das für das ausführende Konto erstellte selbstsignierte Zertifikat läuft ein Jahr nach dem Datum seiner Erstellung ab. Sie können es vor dem Ablaufdatum jederzeit erneuern. Wenn Sie sich für E-Mail-Benachrichtigungen registriert haben, erhalten Sie außerdem E-Mails, wenn Ihrerseits eine Maßnahme erforderlich ist. Dieser Fehler wird 2 Monate vor dem Ablaufdatum angezeigt und in einen kritischen Fehler geändert, wenn das Zertifikat abgelaufen ist. Nachdem das Zertifikat abgelaufen ist, funktioniert die automatische Aktualisierung so lange nicht mehr, bis sie es erneuert haben.

   **Empfohlene Maßnahme:** Klicken Sie auf „Reparieren“ und dann auf „Zertifikat erneuern“, um dieses Problem zu beheben.
    
   ![Zertifikat erneuern](media/azure-to-azure-autoupdate/automation-account-renew-runas-certificate.PNG)

> [!NOTE]
> Nachdem Sie das Zertifikat erneuert haben, aktualisieren Sie bitte die Seite, damit der aktuelle Status aktualisiert wird.
