---
title: Registrieren für Azure NetApp Files | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie sich für Azure NetApp Files registrieren, indem Sie eine Wartelistenanforderung übermitteln und den Azure-Ressourcenanbieter für Azure NetApp Files registrieren.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 06/09/2020
ms.author: b-juche
ms.openlocfilehash: e37b0a2657c3e3f388ad94eb7da864c1b03023bb
ms.sourcegitcommit: 32ee8da1440a2d81c49ff25c5922f786e85109b4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/12/2021
ms.locfileid: "109790017"
---
# <a name="register-for-azure-netapp-files"></a>Registrieren für Azure NetApp Files

> [!IMPORTANT] 
> Sie müssen eine E-Mail vom Azure NetApp Files-Team erhalten haben, in der Ihnen der Zugriff auf den Dienst bestätigt wird, bevor Sie den Ressourcenanbieter für Azure NetApp Files registrieren können. 

In diesem Artikel erfahren Sie, wie Sie sich für Azure NetApp Files registrieren, um den Dienst verwenden zu können.

## <a name="submit-a-waitlist-request-for-accessing-the-service"></a><a name="waitlist"></a>Übermitteln einer Wartelistenanforderung für den Zugriff auf den Dienst

1. Wechseln Sie zur folgenden Seite, und senden Sie eine Wartelistenanforderung für den Zugriff auf den Azure NetApp Files-Dienst:  
    [**Seite zum Senden einer Azure NetApp Files-Warteliste**](https://aka.ms/azurenetappfiles) 

    Wenn Sie sich auf die Warteliste setzen lassen, ist das noch keine Garantie dafür, dass Sie sofort Zugriff auf den Dienst erhalten. 

2. Warten Sie auf eine offizielle Bestätigungs-E-Mail des Azure NetApp Files-Teams, bevor Sie andere Aufgaben ausführen. 

## <a name="register-the-netapp-resource-provider"></a><a name="resource-provider"></a>Registrieren des NetApp-Ressourcenanbieters

Um den Dienst zu verwenden, müssen Sie den Azure-Ressourcenanbieter für Azure NetApp Files registrieren.

> [!NOTE] 
> Sie können sich auch für den NetApp-Ressourcenanbieter registrieren, wenn Sie noch keinen Zugriff auf den Dienst haben. Ohne Zugriffsautorisierung wird allerdings ein Fehler mit der folgenden Fehlermeldung angezeigt, wenn Sie über das Azure-Portal oder eine API eine Anforderung zum Erstellen eines NetApp-Kontos oder einer anderen Azure NetApp Files-Ressource übermitteln:  
>
> `{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"NotFound","message":"{\r\n \"error\": {\r\n \"code\": \"InvalidResourceType\",\r\n \"message\": \"The resource type could not be found in the namespace 'Microsoft.NetApp' for api version '2017-08-15'.\"\r\n }\r\n}"}]}`


1. Klicken Sie im Azure-Portal in der rechten oberen Ecke auf das Symbol für die Azure Cloud Shell:

      ![Azure Cloud Shell-Symbol](../media/azure-netapp-files/azure-netapp-files-azure-cloud-shell.png)

2. Wenn Sie in Ihrem Azure-Konto über mehrere Abonnements verfügen, wählen Sie das Abonnement aus, das für Azure NetApp Files genehmigt wurde:
    
    ```azurecli
    az account set --subscription <subscriptionId>
    ```

3. Geben Sie in der Azure Cloud Shell-Konsole den folgenden Befehl ein, um zu überprüfen, ob Ihr Abonnement genehmigt wurde:
    
    ```azurecli
    az feature list | grep NetApp
    ```

   Die Ausgabe des Befehls sieht wie folgt aus:
   
    ```output
    "id": "/subscriptions/<SubID>/providers/Microsoft.Features/providers/Microsoft.NetApp/features/ANFGA",  
    "name": "Microsoft.NetApp/ANFGA" 
    ```
       
   `<SubID>` ist Ihre Abonnement-ID.

    Wenn Ihnen nicht der Featurename `Microsoft.NetApp/ANFGA` angezeigt wird, haben Sie keinen Zugriff auf den Dienst. Sie sollten dann zuerst die unter [Übermitteln einer Wartelistenanforderung für den Zugriff auf den Dienst](#waitlist) beschriebenen Anweisungen befolgen, um Zugriff auf den Dienst anzufordern, bevor Sie fortfahren. 

4. Geben Sie in der Azure Cloud Shell-Konsole den folgenden Befehl ein, um den Azure-Ressourcenanbieter zu registrieren: 
    
    ```azurecli
    az provider register --namespace Microsoft.NetApp --wait
    ```

   Der `--wait`-Parameter weist die Konsole an, auf den Abschluss der Registrierung zu warten. Der Registrierungsvorgang kann einige Zeit in Anspruch nehmen.

5. Geben Sie in der Azure Cloud Shell-Konsole den folgenden Befehl ein, um zu überprüfen, ob der Azure-Ressourcenanbieter registriert wurde: 
    
    ```azurecli
    az provider show --namespace Microsoft.NetApp
    ```

   Die Ausgabe des Befehls sieht wie folgt aus:
   
    ```output
    {
     "id": "/subscriptions/<SubID>/providers/Microsoft.NetApp",
     "namespace": "Microsoft.NetApp", 
     "registrationState": "Registered", 
     "resourceTypes": […. 
    ```

   `<SubID>` ist Ihre Abonnement-ID.  Der Parameterwert `state` gibt `Registered` an.

6. Klicken Sie im Azure-Portal auf das Blatt **Abonnements**.
7. Klicken Sie auf dem Blatt „Abonnements“ auf Ihre Abonnement-ID. 
8. Klicken Sie in den Einstellungen des Abonnements auf **Ressourcenanbieter**, um zu überprüfen, ob der Microsoft.NetApp-Anbieter den Status „Registriert“ aufweist: 

      ![Registrierte Microsoft.NetApp](../media/azure-netapp-files/azure-netapp-files-registered-resource-providers.png)


## <a name="next-steps"></a>Nächste Schritte

[Erstellen eines NetApp-Kontos](azure-netapp-files-create-netapp-account.md)
