---
title: Schützen webbasierter und mobiler PaaS-Anwendungen mit Azure App Service | Microsoft-Dokumentation
description: 'Erfahren Sie mehr zu den bewährten Methoden in Azure App Service zum Schutz Ihrer webbasierten und mobilen PaaS-Anwendungen. '
services: security
documentationcenter: na
author: techlake
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/28/2018
ms.author: terrylan
ms.openlocfilehash: 6e5034d0ff8f14a9fc381f6fd1a214a91ad4d1ed
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60444400"
---
# <a name="best-practices-for-securing-paas-web-and-mobile-applications-using-azure-app-service"></a>Bewährte Methoden zum Schützen webbasierter und mobiler PaaS-Anwendungen mit Azure App Service

In diesem Artikel erläutern wir eine Sammlung empfohlener Vorgehensweisen in [Azure App Services](../app-service/overview.md) zum Schutz Ihrer webbasierten und mobilen PaaS-Anwendungen. Diese empfohlenen Vorgehensweisen sind aus unseren Erfahrungen mit Azure und den Erfahrungen von Kunden wie Ihnen abgeleitet.

Azure App Service ist ein Platform-as-a-Service-Angebot (PaaS), mit dem Sie webbasierte und mobile Apps für beliebige Plattformen oder Geräte erstellen und eine Verbindung mit Daten herstellen können, die in der Cloud oder lokal gespeichert sind. App Service umfasst die Webfunktionen und mobilen Funktionen, die wir vorher separat als Azure Websites und Azure Mobile Services bereitgestellt haben. Außerdem sind neue Funktionen zum Automatisieren von Geschäftsprozessen und Hosten von Cloud-APIs enthalten. Als einzelner integrierter Dienst stellt App Service einen umfangreichen Satz von Funktionen für mobile, Web- und Integrationsszenarien bereit.

## <a name="authenticate-through-azure-active-directory-ad"></a>Authentifizierung über Azure Active Directory (AD)
App Service bietet einen OAuth 2.0-Dienst für Ihren Identitätsanbieter. In OAuth 2.0 liegt der Schwerpunkt auf der Vereinfachung der Cliententwicklung. Gleichzeitig werden bestimmte Autorisierungsabläufe für Webanwendungen, Desktopanwendungen und Mobiltelefone bereitgestellt. Azure AD verwendet OAuth 2.0, um Ihnen die Autorisierung des Zugriffs auf mobile und Webanwendungen zu ermöglichen. Weitere Informationen finden Sie unter [Authentifizierung und Autorisierung in Azure App Service](../app-service/overview-authentication-authorization.md).

## <a name="restrict-access-based-on-role"></a>Beschränken des Zugriffs auf Grundlage der Rolle 
Das Einschränken des Zugriffs ist für Organisationen zwingend erforderlich, die Sicherheitsrichtlinien für den Datenzugriff durchsetzen möchten. Sie können die rollenbasierte Zugriffssteuerung (RBAC) verwenden, um Benutzern Berechtigungen sowie Anwendungen für einen bestimmen Bereich zu erteilen, zum Beispiel unter den Sicherheitsprinzipien „Need-to-know“ (Kenntnis nur bei Bedarf) und „Ansatz der geringsten Rechte“. Weitere Informationen zum Gewähren des Zugriffs auf Anwendungen finden Sie unter [Authentifizierung und Autorisierung in Azure App Service](../role-based-access-control/overview.md).

## <a name="protect-your-keys"></a>Schützen Ihrer Schlüssel
Ihre Sicherheitsfunktionen können noch so ausgereift sein, wenn Sie Ihre Abonnementschlüssel verlieren. Azure Key Vault unterstützt Sie dabei, kryptografische Schlüssel und Geheimnisse zu schützen, die von Cloudanwendungen und -diensten verwendet werden. Mit Key Vault können Sie Schlüssel und Geheimnisse (beispielsweise Authentifizierungsschlüssel, Schlüssel für Speicherkonten, Datenverschlüsselungsschlüssel, PFX-Dateien und Kennwörter) verschlüsseln, indem Sie Schlüssel verwenden, die durch Hardwaresicherheitsmodule (HSMs) geschützt werden. Zur Steigerung der Sicherheit können Sie Schlüssel in HSMs importieren oder in diesen generieren. Sie können auch Key Vault zum Verwalten Ihrer TLS-Zertifikate mit der automatischen Verlängerung verwenden. Weitere Informationen finden Sie unter [What is Azure Key Vault (Was ist Azure Key Vault)](../key-vault/key-vault-whatis.md). 

## <a name="restrict-incoming-source-ip-addresses"></a>Einschränken eingehender Quell-IP-Adressen
Die [App Service-Umgebung](../app-service/environment/intro.md) verfügt über ein Feature zur Integration virtueller Netzwerke, mit dem Sie eingehende Quell-IP-Adressen über Netzwerksicherheitsgruppen (NSGs) einschränken können. Falls Sie sich mit Azure Virtual Networks (VNETs) noch nicht auskennen, hilft Ihnen vielleicht diese Beschreibung weiter: Es handelt sich um eine Funktion, mit der Sie viele Azure-Ressourcen in einem nicht über das Internet routbaren Netzwerk anordnen können, für das Sie den Zugriff kontrollieren. Weitere Informationen hierzu finden Sie unter [Integrieren Ihrer App in ein Azure Virtual Network](../app-service/web-sites-integrate-with-vnet.md).

## <a name="next-steps"></a>Nächste Schritte
In diesem Artikel wurde Ihnen eine Sammlung empfohlener Vorgehensweisen in Azure App Service zum Schutz Ihrer webbasierten und mobilen PaaS-Anwendungen vorgestellt. Weitere Informationen zum Schutz Ihrer PaaS-Bereitstellungen finden Sie unter:

- [Schützen von PaaS-Bereitstellungen](security-paas-deployments.md)
- [Schützen von PaaS-Datenbanken in Azure](security-paas-applications-using-sql.md)
