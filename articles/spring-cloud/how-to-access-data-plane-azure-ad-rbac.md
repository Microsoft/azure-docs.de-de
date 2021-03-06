---
title: Zugreifen auf Config Server und Service Registry
titleSuffix: Azure Spring Cloud
description: Zugreifen auf Config Server- und Service Registry-Endpunkte mit der rollenbasierten Zugriffssteuerung in Azure Active Directory
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 02/04/2021
ms.custom: devx-track-java, subject-rbac-steps
ms.openlocfilehash: b8474e7f322025609222b4867db988d22aa2bcb9
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/30/2021
ms.locfileid: "108291206"
---
# <a name="access-config-server-and-service-registry"></a>Zugreifen auf Config Server und Service Registry

In diesem Artikel wird erläutert, wie Sie mithilfe der rollenbasierten Zugriffssteuerung (Role-Based Access Control, RBAC) von Azure Active Directory (Azure AD) auf Spring Cloud Config Server und Spring Cloud Service Registry zugreifen, die von Azure Spring Cloud verwaltet werden.

## <a name="assign-role-to-azure-ad-usergroup-msi-or-service-principal"></a>Zuweisen einer Rolle zu einem Azure AD-Benutzer bzw. einer Azure AD-Gruppe, einem MSI oder Dienstprinzipal

Weisen Sie die [azure-spring-cloud-data-reader](../role-based-access-control/built-in-roles.md#azure-spring-cloud-data-reader)-Rolle [user | group | service-principal | managed-identity] unter [management-group | subscription | resource-group | resource] scope zu.

Ausführliche Informationen finden Sie unter [Zuweisen von Azure-Rollen über das Azure-Portal](../role-based-access-control/role-assignments-portal.md).

## <a name="access-config-server-and-service-registry-endpoints"></a>Zugreifen auf Config Server- und Service Registry-Endpunkte

Nach dem Zuweisen der Rolle „Azure Spring Cloud-Datenleser“ können Kunden auf den Spring Cloud Config Server- und den Spring Cloud Service Registry-Endpunkt zugreifen. Verwenden Sie die folgenden Verfahren:

1. Rufen Sie ein Zugriffstoken ab. Nachdem einem Azure AD-Benutzer die Rolle „Azure Spring Cloud-Datenleser“ zugewiesen wurde, können sich Kunden mithilfe der folgenden Befehle bei der Azure CLI mit Benutzeranmeldeinformationen, einem Dienstprinzipal oder einer verwalteten Identität anmelden, um ein Zugriffstoken zu erhalten. Ausführliche Informationen finden Sie unter [Anmelden mit der Azure CLI](/cli/azure/authenticate-azure-cli). 

    ```azurecli
    az login
    az account get-access-token
    ```
2. Erstellen Sie den Endpunkt. Wir unterstützen Standardendpunkte von Spring Cloud Config Server und Spring Cloud Service Registry, die von Azure Spring Cloud verwaltet werden. Weitere Informationen finden Sie unter [Produktionsbereite Endpunkte](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#production-ready-endpoints). Kunden können auch eine vollständige Liste der unterstützten Endpunkte von Spring Cloud Config Server und Spring Cloud Service Registry abrufen, die von Azure Spring Cloud verwaltet werden, indem sie auf Endpunkte zugreifen:

    * *'https://SERVICE_NAME.svc.azuremicroservices.io/eureka/actuator/'*
    * *'https://SERVICE_NAME.svc.azuremicroservices.io/config/actuator/'* 

>[!NOTE]
> Ersetzen Sie bei Verwendung von „Azure China“ `*.azuremicroservices.io` durch `*.microservices.azure.cn`. [Weitere Informationen](/azure/china/resources-developer-guide#check-endpoints-in-azure)

3. Greifen Sie mit dem Zugriffstoken auf den erstellten Endpunkt zu. Platzieren Sie das Zugriffstoken in einem Header, um Autorisierung bereitzustellen.  Nur die GET-Methode wird unterstützt.

    Greifen Sie z. B. auf einen Endpunkt wie *'https://SERVICE_NAME.svc.azuremicroservices.io/eureka/actuator/health '* zu, um den Integritätsstatus von eureka anzuzeigen.

    Wenn die Antwort *401 Unauthorized* (401 Nicht autorisiert) ist, überprüfen Sie, ob die Rolle erfolgreich zugewiesen wurde.  Es dauert einige Minuten, bis die Rolle wirksam wird. Sie können auch überprüfen, ob das Zugriffstoken abgelaufen ist.

## <a name="next-steps"></a>Nächste Schritte
* [Authentifizieren der Azure CLI](/cli/azure/authenticate-azure-cli)
* [Produktionsbereite Endpunkte](https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/#production-ready-endpoints)

## <a name="see-also"></a>Siehe auch
* [Erstellen von Rollen und Berechtigungen](how-to-permissions.md)