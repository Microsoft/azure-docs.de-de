---
title: 'Azure Service Fabric CLI: sfctl'
description: Erfahren Sie mehr über sfctl, die Azure Service Fabric-Befehlszeilenschnittstelle. Enthält eine Liste der Befehle und Untergruppen.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 4721598961ae912e8f0a9ef2f61022e5feb39e6c
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/26/2021
ms.locfileid: "110462248"
---
# <a name="sfctl"></a>sfctl
Befehle zum Verwalten von Service Fabric-Clustern und -Entitäten. Diese Version ist mit der Service Fabric 7.0-Runtime kompatibel.

Befehle folgen dem Muster „Nomen-Verb“. Weitere Informationen finden Sie in den Untergruppen.

## <a name="subgroups"></a>Untergruppen
|Untergruppe|BESCHREIBUNG|
| --- | --- |
| [application](service-fabric-sfctl-application.md) | Ermöglicht es, Anwendungen und Anwendungstypen zu erstellen, zu löschen und zu verwalten. |
| [chaos](service-fabric-sfctl-chaos.md) | Ermöglicht es, den Chaos-Testdienst zu starten und zu beenden sowie seine Berichte anzuzeigen. |
| [cluster](service-fabric-sfctl-cluster.md) | Ermöglicht es, Service Fabric-Cluster auszuwählen, zu verwalten und zu betreiben. |
| [compose](service-fabric-sfctl-compose.md) | Ermöglicht es, Docker Compose Anwendungen zu erstellen, zu löschen und zu verwalten. |
| [container](service-fabric-sfctl-container.md) | Führt containerbezogene Befehle auf einem Clusterknoten aus. |
| [events](service-fabric-sfctl-events.md) | Ruft Ereignisse aus dem Ereignisspeicher ab (wenn der EventStore-Dienst bereits installiert ist). |
| [is](service-fabric-sfctl-is.md) | Ermöglicht es, Befehle an den Infrastrukturdienst abzufragen und zu senden. |
| [Node](service-fabric-sfctl-node.md) | Ermöglicht es, Knoten zu verwalten, die einen Cluster bilden. |
| [partition](service-fabric-sfctl-partition.md) | Ermöglicht es, Partitionen für jeden Dienst abzufragen und zu verwalten. |
| [property](service-fabric-sfctl-property.md) | Speichern und Abfragen von Eigenschaften unter Service Fabric-Namen. |
| [replica](service-fabric-sfctl-replica.md) | Ermöglicht es, Replikate zu verwalten, die zu Servicepartitionen gehören. |
| [rpm](service-fabric-sfctl-rpm.md) | Ermöglicht es, Befehle an den Reparatur-Manager-Dienst abzufragen und zu senden. |
| [sa-cluster](service-fabric-sfctl-sa-cluster.md) | Verwaltet eigenständige Service Fabric-Cluster. |
| [service](service-fabric-sfctl-service.md) | Ermöglicht es, Dienste, Diensttypen und Dienstpakete zu erstellen, zu löschen und zu verwalten. |
| [settings](service-fabric-sfctl-settings.md) | Konfigurieren von Einstellungen, die für diese Instanz von sfctl lokal sind. |
| [store](service-fabric-sfctl-store.md) | Ermöglicht es, grundlegende Vorgänge auf Dateiebene für den Clusterimagespeicher auszuführen. |

## <a name="next-steps"></a>Nächste Schritte
- [Einrichten](service-fabric-cli.md) der Service Fabric-Befehlszeilenschnittstelle
- Informationen zum Verwenden der Service Fabric-Befehlszeilenschnittstelle mit den [Beispielskripts](./scripts/sfctl-upgrade-application.md)
