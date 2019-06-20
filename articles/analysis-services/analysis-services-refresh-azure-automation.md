---
title: Aktualisieren von Azure Analysis Services-Modellen mit Azure Automation | Microsoft-Dokumentation
description: Informationen zu Aktualisierungen von Codemodellen mithilfe von Azure Automation.
author: chrislound
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: chlound
ms.openlocfilehash: 4cae93cff594ad561973f8029ea7335dc4c60263
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66356999"
---
# <a name="refresh-with-azure-automation"></a>Aktualisieren mit Azure Automation

Mithilfe von Azure Automation und PowerShell-Runbooks können Sie automatisierte Datenaktualisierungsvorgänge für Ihre Azure Analysis-Tabellenmodelle ausführen.  

Im Beispiel in diesem Artikel werden die [PowerShell-SqlServer-Module](https://docs.microsoft.com/powershell/module/sqlserver/?view=sqlserver-ps) verwendet.

Ein PowerShell-Beispielrunbook, das die Aktualisierung eines Modells veranschaulicht, wird weiter unten in diesem Artikel bereitgestellt.  

## <a name="authentication"></a>Authentication

Alle Aufrufe müssen mit einem gültigen Azure Active Directory-Token (OAuth 2) authentifiziert werden.  Im Beispiel in diesem Artikel wird ein Dienstprinzipalname (Service Principal Name, SPN) für die Authentifizierung bei Azure Analysis Services verwendet.

Weitere Informationen zum Erstellen eines Dienstprinzipals finden Sie unter [Erstellen eines Dienstprinzipals im Azure-Portal](../active-directory/develop/howto-create-service-principal-portal.md).

## <a name="prerequisites"></a>Voraussetzungen

> [!IMPORTANT]
> Das folgende Beispiel setzt voraus, dass die Azure Analysis Services-Firewall deaktiviert ist. Wenn die Firewall aktiviert ist, muss die öffentliche IP-Adresse des Anforderungsinitiators in der Whitelist der Firewall enthalten sein.

### <a name="install-sqlserver-modules-from-powershell-gallery"></a>Installieren von SqlServer-Modulen aus dem PowerShell-Katalog

1. Klicken Sie in Ihrem Azure Automation-Konto auf **Module** und anschließend auf **Katalog durchsuchen**.

2. Suchen Sie über die Suchleiste nach **SqlServer**.

    ![Suchen von Modulen](./media/analysis-services-refresh-azure-automation/1.png)

3. Wählen Sie „SqlServer“ aus, und klicken Sie dann auf **Importieren**.
 
    ![Importieren des Moduls](./media/analysis-services-refresh-azure-automation/2.png)

4. Klicken Sie auf **OK**.
 
### <a name="create-a-service-principal-spn"></a>Erstellen eines Dienstprinzipals (SPN)

Informationen zum Erstellen eines Dienstprinzipals finden Sie unter [Erstellen eines Dienstprinzipals im Azure-Portal](../active-directory/develop/howto-create-service-principal-portal.md).

### <a name="configure-permissions-in-azure-analysis-services"></a>Konfigurieren von Berechtigungen in Azure Analysis Services
 
Der von Ihnen erstellte Dienstprinzipal muss über Serveradministratorberechtigungen auf dem Server verfügen. Weitere Informationen finden Sie unter [Hinzufügen eines Dienstprinzipals zur Serveradministratorrolle](analysis-services-addservprinc-admins.md).

## <a name="design-the-azure-automation-runbook"></a>Entwerfen des Azure Automation-Runbooks

1. Erstellen Sie im Automation-Konto eine **Anmeldeinformationsressource**, die verwendet wird, um den Dienstprinzipal sicher zu speichern.

    ![Erstellen von Anmeldeinformationen](./media/analysis-services-refresh-azure-automation/6.png)

2. Geben Sie die Details für die Anmeldeinformationen ein.  Geben Sie für den **Benutzernamen** die **SPN-Client-ID** und für das **Kennwort** das **SPN-Geheimnis** ein.

    ![Erstellen von Anmeldeinformationen](./media/analysis-services-refresh-azure-automation/7.png)

3. Importieren Sie das Automation-Runbook.

    ![Importieren des Runbooks](./media/analysis-services-refresh-azure-automation/8.png)

4. Suchen Sie nach der Datei **Refresh-Model.ps1**, geben Sie einen **Namen** und eine **Beschreibung** an, und klicken Sie dann auf **Erstellen**.

    ![Importieren des Runbooks](./media/analysis-services-refresh-azure-automation/9.png)

5. Nachdem das Runbook erstellt wurde, wechselt es automatisch in den Bearbeitungsmodus.  Wählen Sie **Veröffentlichen**.

    ![Runbook veröffentlichen](./media/analysis-services-refresh-azure-automation/10.png)

    > [!NOTE]
    > Die zuvor erstellte Anmeldeinformationsressource wird mit dem Befehl **Get-AutomationPSCredential** vom Runbook abgerufen.  Dieser Befehl wird dann an den PowerShell-Befehl **Invoke-ProcessASADatabase** übergeben, um die Authentifizierung bei Azure Analysis Services durchzuführen.

6. Klicken Sie auf **Start**, um das Runbook zu testen.

    ![Starten des Runbooks](./media/analysis-services-refresh-azure-automation/11.png)

7. Füllen Sie die Parameter **DATABASENAME**, **ANALYSISSERVER** und **REFRESHTYPE** aus, und klicken Sie dann auf **OK**. Der Parameter **WEBHOOKDATA** ist nicht erforderlich, wenn das Runbook manuell ausgeführt wird.

    ![Starten des Runbooks](./media/analysis-services-refresh-azure-automation/12.png)

Wenn das Runbook erfolgreich ausgeführt wurde, erhalten Sie eine Ausgabe ähnlich der folgenden:

![Erfolgreiche Ausführung](./media/analysis-services-refresh-azure-automation/13.png)

## <a name="use-a-self-contained-azure-automation-runbook"></a>Verwenden eines eigenständigen Azure Automation-Runbooks

Das Runbook kann so konfiguriert werden, dass die Aktualisierung des Azure Analysis Services-Modells nach Zeitplan ausgelöst wird.

Dies kann wie folgt konfiguriert werden:

1. Klicken Sie im Automation-Runbook auf **Zeitpläne** und dann auf **Zeitplan hinzufügen**.
 
    ![Erstellen eines Zeitplans](./media/analysis-services-refresh-azure-automation/14.png)

2. Klicken Sie auf **Zeitplan** > **Neuen Zeitplan erstellen**, und geben Sie dann die Details ein.

    ![Konfigurieren des Zeitplans](./media/analysis-services-refresh-azure-automation/15.png)

3. Klicken Sie auf **Create**.

4. Füllen Sie die Parameter für den Zeitplan aus. Diese werden jedes Mal verwendet, wenn das Runbook ausgelöst wird. Der Parameter **WEBHOOKDATA** muss leer bleiben, wenn die Ausführung nach einem Zeitplan erfolgt.

    ![Konfigurieren von Parametern](./media/analysis-services-refresh-azure-automation/16.png)

5. Klicken Sie auf **OK**.

## <a name="consume-with-data-factory"></a>Nutzen mit Data Factory

Um das Runbook mithilfe von Azure Data Factory zu nutzen, müssen Sie zunächst einen **Webhook** für das Runbook erstellen. Der **Webhook** enthält eine URL, die über eine Azure Data Factory-Webaktivität aufgerufen werden kann.

> [!IMPORTANT]
> Zum Erstellen eines **Webhooks** muss das Runbook den Status **Veröffentlicht** aufweisen.

1. Klicken Sie in Ihrem Automation-Runbook auf **Webhooks** und dann auf **Webhook hinzufügen**.

   ![Hinzufügen des Webhooks](./media/analysis-services-refresh-azure-automation/17.png)

2. Geben Sie für den Webhook einen Namen und ein Ablaufdatum an.  Mit dem Namen wird der Webhook lediglich im Automation-Runbook identifiziert, er ist kein Bestandteil der URL.

   >[!CAUTION]
   >Kopieren Sie die URL vor dem Schließen des Assistenten. Nach dem Schließen können Sie nicht mehr zurückkehren.
    
   ![Konfigurieren des Webhooks](./media/analysis-services-refresh-azure-automation/18.png)

    Die Parameter für den Webhook können leer bleiben.  Wenn Sie die Azure Data Factory-Webaktivität konfigurieren, können die Parameter in den Text des Webaufrufs übergeben werden.

3. Konfigurieren Sie in Data Factory eine **Webaktivität**.

### <a name="example"></a>Beispiel

   ![Beispielwebaktivität](./media/analysis-services-refresh-azure-automation/19.png)

Die **URL** ist die aus dem Webhook erstellte URL.

Der **Text** ist ein JSON-Dokument, das die folgenden Eigenschaften enthalten muss:


|Eigenschaft  |Wert  |
|---------|---------|
|**AnalysisServicesDatabase**     |Name der Azure Analysis Services-Datenbank <br/> Beispiel: AdventureWorksDB         |
|**AnalysisServicesServer**     |Name des Azure Analysis Services-Servers <br/> Beispiel: https:\//westus.asazure.windows.net/servers/myserver/models/AdventureWorks/         |
|**DatabaseRefreshType**     |Typ der auszuführenden Aktualisierung <br/> Beispiel: Vollständig         |

JSON-Beispieltext:

```json
{
    "AnalysisServicesDatabaseName": "AdventureWorksDB",
    "AnalysisServicesServer": "asazure://westeurope.asazure.windows.net/MyAnalysisServer",
    "DatabaseRefreshType": "Full"
}
```

Diese Parameter werden im PowerShell-Skript des Runbooks definiert.  Wenn die Webaktivität ausgeführt wird, wird als JSON-Nutzlast WEBHOOKDATA übergeben.

Diese wird deserialisiert und als PowerShell-Parameter gespeichert, die dann im PowerShell-Befehl „Invoke-ProcesASDatabase“ verwendet werden.

![Deserialisierter Webhook](./media/analysis-services-refresh-azure-automation/20.png)

## <a name="use-a-hybrid-worker-with-azure-analysis-services"></a>Verwenden eines Hybrid Workers mit Azure Analysis Services

Ein virtueller Azure-Computer mit einer statischen öffentlichen IP-Adresse kann als Azure Automation Hybrid Worker verwendet werden.  Diese öffentliche IP-Adresse kann dann der Azure Analysis Services-Firewall hinzugefügt werden.

> [!IMPORTANT]
> Stellen Sie sicher, dass die öffentliche IP-Adresse des virtuellen Computers als statische Adresse konfiguriert ist.
>
>Weitere Informationen zum Konfigurieren von Azure Automation Hybrid Workern finden Sie unter [Automatisieren von Ressourcen im Rechenzentrum oder in der Cloud mit Hybrid Runbook Worker](../automation/automation-hybrid-runbook-worker.md#install-a-hybrid-runbook-worker).

Erstellen Sie nach dem Konfigurieren eines Hybrid Workers einen Webhook entsprechend der Beschreibung im Abschnitt [Nutzen mit Data Factory](#consume-with-data-factory).  Als einzigen Unterschied wählen Sie hier beim Konfigurieren des Webhooks die Option **Ausführen auf** > **Hybrid Worker** aus.

Beispielwebhook mithilfe von Hybrid Worker:

![Beispiel für Hybrid Worker-Webhook](./media/analysis-services-refresh-azure-automation/21.png)

## <a name="sample-powershell-runbook"></a>PowerShell-Beispielrunbook

Der folgende Codeausschnitt ist ein Beispiel dafür, wie die Aktualisierung des Azure Analysis Services-Modells mit einem PowerShell-Runbook durchgeführt werden kann.

```powershell
param
(
    [Parameter (Mandatory = $false)]
    [object] $WebhookData,

    [Parameter (Mandatory = $false)]
    [String] $DatabaseName,
    [Parameter (Mandatory = $false)]
    [String] $AnalysisServer,
    [Parameter (Mandatory = $false)]
    [String] $RefreshType
)

$_Credential = Get-AutomationPSCredential -Name "ServicePrincipal"

# If runbook was called from Webhook, WebhookData will not be null.
if ($WebhookData)
{ 
    # Retrieve AAS details from Webhook request body
    $atmParameters = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)
    Write-Output "CredentialName: $($atmParameters.CredentialName)"
    Write-Output "AnalysisServicesDatabaseName: $($atmParameters.AnalysisServicesDatabaseName)"
    Write-Output "AnalysisServicesServer: $($atmParameters.AnalysisServicesServer)"
    Write-Output "DatabaseRefreshType: $($atmParameters.DatabaseRefreshType)"
    
    $_databaseName = $atmParameters.AnalysisServicesDatabaseName
    $_analysisServer = $atmParameters.AnalysisServicesServer
    $_refreshType = $atmParameters.DatabaseRefreshType
 
    Invoke-ProcessASDatabase -DatabaseName $_databaseName -RefreshType $_refreshType -Server $_analysisServer -ServicePrincipal -Credential $_credential
}
else 
{
    Invoke-ProcessASDatabase -DatabaseName $DatabaseName -RefreshType $RefreshType -Server $AnalysisServer -ServicePrincipal -Credential $_Credential
}
```


## <a name="next-steps"></a>Nächste Schritte

[Beispiele](analysis-services-samples.md)  
[REST-API](https://docs.microsoft.com/rest/api/analysisservices/servers)
