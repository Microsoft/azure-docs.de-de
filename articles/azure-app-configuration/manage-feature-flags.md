---
title: Tutorial zur Verwaltung von Featureflags mithilfe von Azure App Configuration | Microsoft-Dokumentation
description: In diesem Tutorial erfahren Sie, wie Sie Featureflags mithilfe von Azure App Configuration getrennt von Ihrer Anwendung verwalten.
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 04/19/2019
ms.author: yegu
ms.custom: mvc
ms.openlocfilehash: b7fbf9add67a45c0db89fc11cee5c10bc537ab63
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393568"
---
# <a name="tutorial-manage-feature-flags-in-azure-app-configuration"></a>Tutorial: Verwalten von Featureflags in Azure App Configuration

Sie können alle Featureflags in Azure App Configuration speichern und von einem zentralen Ort aus verwalten. Die verfügbare Portal-Benutzeroberfläche namens **Feature-Manager** in App Configuration ist speziell für Featureflags konzipiert. Darüber hinaus wird das Datenschema für .NET Core-Featureflags von App Configuration nativ unterstützt.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Definieren und Verwalten von Featureflags in App Configuration
> * Zugreifen auf Featureflags über Ihre Anwendung

## <a name="create-feature-flags"></a>Erstellen von Featureflags

Der Feature-Manager im Azure-Portal für App Configuration bietet eine Benutzeroberfläche zum Erstellen und Verwalten von Featureflags, die Sie in Ihren Anwendungen verwenden.

So fügen Sie ein neues Featureflag hinzu:

1. Wählen Sie **Feature-Manager** >  **+Hinzufügen** aus, um ein Featureflag hinzuzufügen.

    ![Liste mit Featureflags](./media/azure-app-configuration-feature-flags.png)

1. Geben Sie einen eindeutigen Schlüsselnamen für das Featureflag ein. Dieser Name wird benötigt, um im Code auf das Flag zu verweisen.

1. Sie können eine Beschreibung für das Featureflag eingeben.

1. Legen Sie den Anfangszustand des Featureflags fest. Dieser Zustand lautet in der Regel *Aus* oder *Ein*. Der Zustand *Ein* ändert sich in *Conditional* (Bedingt), wenn Sie dem Featureflag einen Filter hinzufügen.

    ![Erstellung des Featureflags](./media/azure-app-configuration-feature-flag-create.png)

1. Wenn der Zustand *Ein* lautet, können Sie mit **+Filter hinzufügen** zusätzliche Bedingungen zur Zustandsqualifizierung angeben. Geben Sie einen integrierten oder benutzerdefinierten Filterschlüssel ein, und wählen Sie dann **+Parameter hinzufügen** aus, um dem Filter mindestens einen Parameter zuzuordnen. Zu den integrierten Filtern zählen folgende:

    | Schlüssel | JSON-Parameter |
    |---|---|
    | Microsoft.Percentage | {"Value": 0–100 Prozent} |
    | Microsoft.TimeWindow | {"Start": UTC-Zeit, "End": UTC-Zeit} |

    ![Featureflagfilter](./media/azure-app-configuration-feature-flag-filter.png)

## <a name="update-feature-flag-states"></a>Aktualisieren von Featureflagzuständen

So ändern Sie den Zustandswert eines Featureflags:

1. Wählen Sie **Feature-Manager** aus.

1. Wählen Sie rechts neben dem zu ändernden Featureflag die Schaltfläche mit den Auslassungspunkten ( **...** ) und anschließend **Bearbeiten** aus.

1. Legen Sie einen neuen Zustand für das Featureflag fest.

## <a name="access-feature-flags"></a>Zugreifen auf Featureflags

Vom Feature-Manager erstellte Featureflags werden als reguläre Schlüssel-Wert-Paare gespeichert und abgerufen. Sie befinden sich unter einem speziellen Namespacepräfix: `.appconfig.featureflag`. Die zugrunde liegenden Schlüssel-Wert-Paare können im Konfigurations-Explorer angezeigt werden. Ihre Anwendung kann zum Abrufen dieser Werte die App Configuration-Konfigurationsanbieter, SDKs, Befehlszeilenerweiterungen und REST-APIs verwenden.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie gelernt, wie Sie Featureflags und deren Zustand mithilfe von App Configuration verwalten. Die folgenden Artikel enthalten weitere Informationen zur Unterstützung der Featureverwaltung in App Configuration und ASP.NET Core:

* [Tutorial: Use feature flags in a .NET Core app](./use-feature-flags-dotnet-core.md) (Tutorial: Verwenden von Featureflags in einer .NET Core-App)
