---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 06/11/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: ae13ed8f850b7432fa72f23bfbd7b29fe5b80df7
ms.sourcegitcommit: 942a1c6df387438acbeb6d8ca50a831847ecc6dc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/11/2021
ms.locfileid: "112019405"
---
|Name<br /><sub>(Azure-Portal)</sub> |Beschreibung |Auswirkungen |Version<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Stack Edge-Geräte sollten doppelte Verschlüsselung verwenden.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb4ac1030-89c5-4697-8e00-28b5ba6a8811) |Stellen Sie zum Schutz der ruhenden Daten auf dem Gerät Folgendes sicher: Die Daten sind doppelt verschlüsselt, der Zugriff auf Daten wird gesteuert, und nach der Deaktivierung des Geräts werden die Daten auf sichere Weise von den Datenträgern gelöscht. Bei der Mehrfachverschlüsselung werden zwei Verschlüsselungsebenen verwendet: Verschlüsselung vom Typ „BitLocker XTS-AES 256-Bit“ auf den Datenvolumes und integrierte Verschlüsselung der Festplatten. Weitere Informationen finden Sie in der Dokumentation zur Sicherheitsübersicht für das jeweilige Stack Edge-Gerät. |Audit, Deny, Disabled |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Azure%20Stack%20Edge/AzureStackEdge_DoubleEncryption_Audit.json) |
