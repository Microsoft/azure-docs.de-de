---
title: Azure Front Door – URL-Rewrite| Microsoft-Dokumentation
description: In diesem Artikel erläutert, wie Azure Front Door die URL-Rewrite-Funktion durchführt, wenn diese konfiguriert wurde.
services: front-door
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: eb5b4ab8a23a374aec54d65dd5390ab3fec3e905
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91445489"
---
# <a name="url-rewrite-custom-forwarding-path"></a>URL-Rewrite (benutzerdefinierter Weiterleitungspfad)
Azure Front Door unterstützt URL-Rewrite durch Konfigurieren eines optionalen **benutzerdefinierten Weiterleitungspfads**, der beim Erstellen der Anforderung zum Weiterleiten an das Back-End verwendet werden soll. Wenn kein benutzerdefinierter Weiterleitungspfad bereitgestellt wird, kopiert Front Door standardmäßig den URL-Eingangspfad in die URL, die in der weitergeleiteten Anforderung verwendet wurde. Der in der weitergeleiteten Anforderung verwendete Hostheader wird für das ausgewählten Back-End konfiguriert. Informationen zu den Aktionen und zur Konfiguration des Hostheaders finden Sie unter [Back-End-Hostheader](front-door-backend-pool.md#hostheader).

Die Stärke von URL-Rewrite besteht darin, dass der benutzerdefinierte Weiterleitungspfad einen beliebigen Teil des Eingangspfads, der einem Platzhalterpfad entspricht, in den weitergeleiteten Pfad kopiert (diese Pfadsegmente sind die **grün** dargestellten Segmente im folgenden Beispiel):
</br>

:::image type="content" source="./media/front-door-url-rewrite/front-door-url-rewrite-example.jpg" alt-text="URL-Rewrite in Azure Front Door":::

## <a name="url-rewrite-example"></a>Beispiel für URL-Rewrite
Sehen wir uns eine Routingregel mit der folgenden Kombination aus konfigurierten Front-End-Hosts und Pfaden an:

| Hosts      | Paths       |
|------------|-------------|
| www\.contoso.com | /\*   |
|            | /foo        |
|            | /foo/\*     |
|            | /foo/bar/\* |

Die erste Spalte der folgenden Tabelle enthält Beispiele für eingehende Anforderungen, und die zweite Spalte zeigt, welcher Pfad der Route am genauesten übereinstimmt.  Die dritte Spalte und die nachfolgenden Spalten der Tabelle sind Beispiele für konfigurierte **Benutzerdefinierte Weiterleitungspfade**.

Die zweite Zeile zeigt beispielsweise, dass der weitergeleitete Pfad für die eingehende Anforderung `www.contoso.com/sub` für den benutzerdefinierten Weiterleitungspfad `/` folgendermaßen lauten würde: `/sub`. Wenn der benutzerdefinierte Weiterleitungspfad `/fwd/` wäre, würde der weitergeleitete Pfad `/fwd/sub` lauten. Für die übrigen Spalten gilt dasselbe Prinzip. Die **hervorgehobenen** Teile der folgenden Pfade zeigen den Teil, der mit dem Platzhalter übereinstimmt.

| Eingehende Anforderung       | Genaueste Pfadübereinstimmung | /          | /fwd/          | /foo/          | /foo/bar/          |
|------------------------|--------------------------|------------|----------------|----------------|--------------------|
| www\.contoso.com/            | /\*                      | /          | /fwd/          | /foo/          | /foo/bar/          |
| www\.contoso.com/**sub**     | /\*                      | /**sub**   | /fwd/**sub**   | /foo/**sub**   | /foo/bar/**sub**   |
| www\.contoso.com/**a/b/c**   | /\*                      | /**a/b/c** | /fwd/**a/b/c** | /foo/**a/b/c** | /foo/bar/**a/b/c** |
| www\.contoso.com/foo         | /foo                     | /          | /fwd/          | /foo/          | /foo/bar/          |
| www\.contoso.com/foo/        | /foo/\*                  | /          | /fwd/          | /foo/          | /foo/bar/          |
| www\.contoso.com/foo/**bar** | /foo/\*                  | /**bar**   | /fwd/**bar**   | /foo/**bar**   | /foo/bar/**bar**   |

## <a name="optional-settings"></a>Optionale Einstellungen
Es gibt zusätzliche, optionale Einstellungen, die Sie für alle Routingregeleinstellungen angeben können:

* **Cachekonfiguration**: Wenn diese Option deaktiviert oder nicht angegeben ist, versuchen die mit dieser Routingregel übereinstimmenden Anforderungen nicht, den zwischengespeicherten Inhalt zu verwenden. Stattdessen rufen sie die Inhalte immer aus dem Back-End ab. Informationen hierzu finden Sie unter [Zwischenspeicherung mit Front Door](front-door-caching.md).

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über das [Erstellen einer Front Door-Instanz](quickstart-create-front-door.md).
- Informieren Sie sich über die [Funktionsweise von Azure Front Door Service](front-door-routing-architecture.md).
