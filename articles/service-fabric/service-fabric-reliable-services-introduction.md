---
title: Übersicht über das Service Fabric Reliable Services-Programmiermodell | Microsoft Docs
description: Erfahren Sie mehr über das Reliable Services-Programmmodell von Service Fabric, und schreiben Sie eigene Dienste.
services: Service-Fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: vturecek; mani-ramaswamy
ms.assetid: 0c88a533-73f8-4ae1-a939-67d17456ac06
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 3/9/2018
ms.author: masnider
ms.openlocfilehash: 1789c7489e58df09dccfde3e7ab106ef54b5c1ae
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60727011"
---
# <a name="reliable-services-overview"></a>Übersicht über Reliable Services
Azure Service Fabric vereinfacht das Schreiben und Verwalten zustandsloser und zustandsbehafteter Dienste (Reliable Services). Dieses Thema behandelt Folgendes:

* Das Reliable Services-Programmiermodell für zustandslose und zustandsbehaftete Dienste
* Die Optionen, die beim Schreiben von Reliable Services zur Auswahl stehen
* Szenarien und Beispiele für die Verwendung von Reliable Services und wie sie geschrieben werden

Reliable Services zählen zu den in Service Fabric verfügbaren Programmiermodellen. Bei dem anderen Modell handelt es sich um das Programmiermodell für Reliable Actors, das zusätzlich zum Reliable Services-Modell ein Modell zur Programmierung von virtuellen Actors bereitstellt. Weitere Informationen zum Reliable Actors-Programmiermodell finden Sie unter [Einführung in Service Fabric Reliable Actors](service-fabric-reliable-actors-introduction.md).

Service Fabric verwaltet die Lebensdauer von Diensten von der Bereitstellung und Implementierung bis hin zur Aktualisierung und Löschung. Diese Vorgänge erfolgen über die [Anwendungsverwaltung von Service Fabric](service-fabric-deploy-remove-applications.md).

## <a name="what-are-reliable-services"></a>Was sind Reliable Services?
Reliable Services bietet ein einfaches, leistungsfähiges High-Level-Programmiermodell, mit dem Sie alles Wichtige in Ihrer Anwendung programmieren können. Das Reliable Services-Programmiermodell bietet Folgendes:

* Zugriff auf den Rest der Service Fabric-Programmier-APIs. Im Gegensatz zu den als [ausführbare Gastdateien](service-fabric-guest-executables-introduction.md) modellierten Service Fabric-Diensten nutzen Reliable Services den Rest der Service Fabric-APIs direkt. Dies ermöglicht den Diensten Folgendes:
  * Systemabfragen
  * Erstellen von Berichten zur Integrität von Entitäten im Cluster
  * Empfangen von Benachrichtigungen zu Konfigurations- und Codeänderungen
  * Suchen von und Kommunizieren mit anderen Diensten
  * (optional) Verwenden der [Reliable Collections](service-fabric-reliable-services-reliable-collections.md)
  * Gewähren von Zugriff für die Reliable Collections auf viele weitere Funktionen – all dies über ein erstklassiges Programmiermodell in verschiedenen Programmiersprachen
* Ein einfaches Modell für die Ausführung Ihres eigenen Codes, das den gewohnten Programmiermodellen ähnelt. Der Code besitzt einen klar definierten Einstiegspunkt und einen leicht zu verwaltenden Lebenszyklus.
* Ein austauschbares Kommunikationsmodell. Verwenden Sie die gewünschte Transportmethode, z.B. HTTP mit [Web-API](service-fabric-reliable-services-communication-webapi.md), Websockets, benutzerdefinierte TCP-Protokolle oder eine andere Methode. Reliable Services bieten einige nützliche vorkonfigurierte Optionen. Sie können aber auch eigene Optionen bereitstellen.
* Für zustandsbehaftete Dienste ermöglicht das Reliable Services-Programmiermodell eine konsistente und zuverlässige Speicherung des Zustands direkt innerhalb des Diensts anhand von [Reliable Collections](service-fabric-reliable-services-reliable-collections.md). Reliable Collections sind ein einfacher Satz hoch verfügbarer Auflistungsklassen, die jedem vertraut sind, der bereits mit C#-Auflistungen gearbeitet hat. Bisher benötigten Dienste externe Systeme für die zuverlässige Zustandsverwaltung. Mit Reliable Collections können Sie den Zustand zusammen mit Ihren Berechnungen speichern und dabei von der gleichen Hochverfügbarkeit und Zuverlässigkeit profitieren, die Sie von externen Hochverfügbarkeitsspeichern gewohnt sind. Dieses Modell verbessert auch die Latenz, da Sie die Berechnungen und die für die Funktionsweise erforderlichen Zustände zusammenstellen können.

## <a name="what-makes-reliable-services-different"></a>Was unterscheidet Reliable Services von anderen Diensten?
Reliable Services sind nicht wie andere Dienste, die Sie möglicherweise bereits geschrieben haben. Service Fabric bietet Zuverlässigkeit, Verfügbarkeit, Konsistenz und Skalierbarkeit.

* **Zuverlässigkeit**: Ihr Dienst bleibt auch in unzuverlässigen Umgebungen erreichbar, in denen Ihre Computer ausfallen oder auf Netzwerkprobleme stoßen, sowie in Fällen, in denen bei den Diensten selbst Fehler auftreten und diese abstürzen oder ausfallen. Bei zustandsbehafteten Diensten wird der Zustand beibehalten, auch wenn im Netzwerk oder anderen Komponenten Fehler auftreten.
* **Verfügbarkeit**: Ihr Dienst bleibt erreichbar und reaktionsfähig. Service Fabric behält die von Ihnen gewünschte Anzahl von ausgeführten Kopien bei.
* **Skalierbarkeit**: Die Dienste sind nicht an bestimmte Hardwarekomponenten gekoppelt und können durch Hinzufügen oder Entfernen von Hardware oder anderen Ressourcen nach Bedarf vergrößert oder verkleinert werden. Die Dienste lassen sich einfach partitionieren (insbesondere, wenn sie statusbehaftet sind), um sicherzustellen, dass sie skaliert werden und auf Teilausfälle reagieren können. Dienste können mithilfe von Code dynamisch erstellt und gelöscht werden, sodass bei Bedarf – z.B. als Reaktion auf Kundenanfragen – weitere Instanzen in Betrieb genommen werden können. Und nicht zuletzt fördert Service Fabric das Erstellen schlanker Dienste. Mit Service Fabric können in einem einzigen Prozess Tausende von Diensten bereitgestellt werden – Sie müssen nicht mehr ganze Betriebssysteminstanzen oder -prozesse für eine einzige Instanz einrichten oder reservieren.
* **Konsistenz**: Für alle in diesem Dienst gespeicherten Informationen kann Konsistenz garantiert werden. Dies gilt sogar über mehrere Reliable Collections in einem Dienst hinweg. Auflistungsübergreifende Änderungen innerhalb eines Diensts können transaktionsbasiert und atomar vorgenommen werden.

## <a name="service-lifecycle"></a>Dienstlebenszyklus
Ungeachtet dessen, ob der Dienst zustandsbehaftet oder zustandslos ist, bieten Reliable Services einen einfachen Lebenszyklus, mit dem Sie den Code schnell einbinden und beginnen können.  Sie müssen zum Ausführen des Diensts nur eine oder zwei Methoden implementieren.

* **CreateServiceReplicaListeners/CreateServiceInstanceListeners**: Bei dieser Methode definiert der Dienst den bzw. die zu verwendenden Kommunikationsstapel. Der Kommunikationsstapel, z B. [Web-API](service-fabric-reliable-services-communication-webapi.md), definiert den/die lauschenden Endpunkt(e) für den Dienst (sodass er für Clients erreichbar ist). Er definiert auch, wie die angezeigten Nachrichten mit dem Rest des Dienstcodes interagieren.
* **RunAsync**: In dieser Methode führt der Dienst die zugehörige Geschäftslogik aus und startet jegliche Hintergrundtasks, die während der Lebensdauer des Diensts ausgeführt werden sollen. Das bereitgestellte Abbruchtoken dient als Signal, wenn die Arbeit beendet werden soll. Wenn der Dienst beispielsweise Nachrichten aus einer Reliable Queue abrufen und verarbeiten muss, wird dieser Vorgang ausgeführt.

Wenn Sie mit Reliable Services von nicht vertraut sind – lesen Sie weiter! Wenn Sie eine ausführliche exemplarische Vorgehensweise zum Lebenszyklus von Reliable Services suchen, wechseln Sie zu [diesem Artikel](service-fabric-reliable-services-lifecycle.md).

## <a name="example-services"></a>Beispieldienste
Nachdem Sie diese Programmiermodell nun kennen, werfen wir einen Blick auf zwei Dienste, um zu sehen, wie diese Elemente zusammenspielen.

### <a name="stateless-reliable-services"></a>Zustandslose Reliable Services
Ein Dienst ist zustandslos, wenn der Dienstzustand nicht aufrufübergreifend im Dienst beibehalten wird. Jeder vorhandene Zustand kann vollständig verworfen werden und erfordert weder Synchronisierung noch Replikation, Persistenz oder Hochverfügbarkeit.

Denken Sie beispielsweise an einen Rechner, der keinen Speicher hat und alle Zahlen und durchzuführenden Operationen gleichzeitig erhält.

In diesem Fall kann die Methode `RunAsync()`(C#) oder `runAsync()` (Java) des Diensts leer sein, da der Dienst keine Hintergrundtasks ausführen muss. Wenn der Rechnerdienst erstellt wird, gibt sie einen `ICommunicationListener` (C#) oder `CommunicationListener` Java (z.B. [Web-API](service-fabric-reliable-services-communication-webapi.md)) zurück, der auf einem Port einen Lauschendpunkt öffnet. Dieser Lauschendpunkt wird mit den verschiedenen Berechnungsmethoden verknüpft (Beispiel: „Add(n1, n2)“), die die öffentliche API des Rechners definieren.

Erfolgt ein Aufruf durch einen Client, wird die entsprechende Methode ausgelöst. Der Rechnerdienst führt an den bereitgestellten Daten die erforderlichen Operationen durch und gibt das Ergebnis zurück. Es wird kein Zustand gespeichert.

Die Tatsache, dass kein interner Zustand gespeichert wird, macht den Beispielrechner sehr einfach. Aber die meisten Dienste sind nicht wirklich zustandslos. Stattdessen lagern sie ihren Zustand in einen anderen Speicher aus. (Beispielsweise ist eine Web-App, die zum Speichern des Sitzungszustands einen Sicherungsspeicher oder Cache verwendet, nicht zustandslos.)

Zustandslose Dienste werden in Service Fabric beispielsweise häufig als Front-End verwendet, das die öffentliche API für eine Webanwendung verfügbar macht. Der Front-End-Dienst kommuniziert in diesem Fall mit zustandsbehafteten Diensten, um eine Benutzeranforderung zu verarbeiten. Aufrufe von Clients werden dabei an einen bekannten Port wie Port 80 geleitet, an dem der zustandslose Dienst lauscht. Dieser zustandslose Dienst erhält den Aufruf und ermittelt, ob dieser von einem vertrauenswürdigen Teilnehmer stammt und für welchen Dienst er bestimmt ist.  Der zustandslose Dienst leitet den Aufruf anschließend an die richtige Partition des zustandsbehafteten Diensts weiter und wartet auf eine Antwort. Sobald der zustandslose Dienst eine Antwort erhält, sendet er eine Antwort an den ursprünglichen Client zurück. Ein Beispiel für einen solchen Dienst finden Sie in unseren Codebeispielen [C#](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started) / [Java](https://github.com/Azure-Samples/service-fabric-java-getting-started). Diese Codebeispiele enthalten nur ein Beispiel dieses Musters, weitere finden Sie in anderen Codebeispielen.

### <a name="stateful-reliable-services"></a>Zustandsbehaftete Reliable Services
Bei einem zustandsbehafteten Dienst muss ein gewisser Teil des Zustands konsistent und präsent sein, damit der Dienst funktioniert. Nehmen wir einen Dienst, der kontinuierlich einen gleitenden Durchschnitt eines Werts berechnet, der regelmäßig aktualisiert wird. Der Dienst benötigt zu diesem Zweck den aktuellen Satz eingehender, zu verarbeitender Anforderungen und den aktuellen Durchschnittswert. Jeder Dienst, der Informationen in einem externen Speicher abruft, verarbeitet und speichert (z. B. in einem modernen Azure-Blob- oder Tabellenspeicher), ist zustandsbehaftet. Er bewahrt seinen Zustand nur im externen Zustandsspeicher auf.

Die meisten Dienste speichern heute ihren Zustand extern, da der externe Speicher die für den Zustand erforderliche Zuverlässigkeit, Verfügbarkeit, Skalierbarkeit und Konsistenz bietet. In Service Fabric müssen Dienste ihren Zustand nicht extern speichern. Service Fabric verarbeitet diese Anforderungen sowohl für den Dienstcode als auch für den Dienstzustand.

Ein Beispiel: Sie möchten einen Dienst schreiben, der Bilder verarbeitet. Zu diesem Zweck übernimmt der Dienst das Bild und die Reihe von Konvertierungen, die für dieses Bild auszuführen sind. Der Dienst gibt einen Kommunikationslistener zurück (beispielsweise eine Web-API), der eine API wie `ConvertImage(Image i, IList<Conversion> conversions)` verfügbar macht. Wenn eine Anforderung eingeht, speichert der Dienst diese in einer `IReliableQueue` und gibt eine ID an den Client zurück, um die Anforderung nachzuverfolgen.

In diesem Dienst könnte `RunAsync()` etwas komplexer ausfallen. Der Dienst weist eine Schleife in `RunAsync()` auf, die Anforderungen aus `IReliableQueue` abruft und die erforderlichen Konvertierungen ausführt. Die Ergebnisse werden in einem `IReliableDictionary` gespeichert, sodass der Client die konvertierten Bilder abrufen kann. Um sicherzustellen, dass das Bild auch bei einem Fehler nicht verloren geht, würde dieser Reliable Service in einer einzige Transaktion das Bild aus der Warteschlange abrufen, die Konvertierungen durchführen und das Ergebnis speichern. In diesem Fall wird die Nachricht aus der Warteschlange entfernt, und die Ergebnisse werden nur dann im Ergebniswörterbuch gespeichert, wenn die Konvertierungen abgeschlossen sind. Alternativ dazu kann der Dienst das Bild auch aus der Warteschlange abrufen und sofort in einem Remotespeicher speichern. Dadurch wird die Menge an Zuständen reduziert, die der Dienst verwalten muss. Allerdings steigt auch die Komplexität, da der Dienst die zum Verwalten des Remotespeichers erforderlichen Metadaten beibehalten muss. Für beide Vorgehensweisen gilt: Wenn während des gesamten Prozesses ein Fehler auftritt, verbleibt die Anforderung in der Warteschlange und wartet auf die Verarbeitung.

Zu diesem Dienst ist zu beachten, dass er wie ein normaler .NET-Dienst erscheint. Der einzige Unterschied besteht darin, dass die verwendeten Datenstrukturen (`IReliableQueue` und `IReliableDictionary`) von Service Fabric bereitgestellt werden und äußerst zuverlässig, verfügbar und konsistent sind.

## <a name="when-to-use-reliable-services-apis"></a>Anwendungsbereiche für Reliable Services-APIs
Wenn einer der folgenden Punkte auf Ihre Anwendungsdienstanforderungen zutrifft, sollten Sie Reliable Services-APIs in Erwägung ziehen:

* Ihr Dienstcode (und optional auch der Dienstzustand) muss in hohem Maß verfügbar und zuverlässig sein.
* Sie benötigen Garantien für Transaktionen über mehrere Zustandseinheiten hinweg (z.B. Aufträge und Auftragsposten).
* Der Zustand Ihrer Anwendung kann auf natürliche Weise als Reliable Dictionaries und Queues modelliert werden.
* Der Code bzw. Zustand Ihrer Anwendung muss hochverfügbar sein und eine geringe Latenz für Lese- und Schreibvorgänge aufweisen.
* Ihre Anwendung muss in einer oder mehreren Reliable Collections die Parallelität bzw. Granularität von ausgeführten Vorgängen steuern.
* Sie möchten die Kommunikation verwalten oder das Partitionierungsschema für Ihren Dienst steuern.
* Ihr Code benötigt eine Laufzeitumgebung mit dem Threadingmodell „Free“.
* Ihre Anwendung muss Reliable Dictionaries oder Queues sowie vollständige Dienste zur Laufzeit dynamisch erstellen oder zerstören.
* Sie müssen die von Service Fabric bereitgestellten Sicherungs- und Wiederherstellungsfunktionen für den Zustand Ihres Diensts per Programmierung steuern.
* Ihre Anwendung muss den Änderungsverlauf ihrer Zustandseinheiten protokollieren.
* Sie möchten benutzerdefinierte Zustandsanbieter selbst entwickeln oder von Drittanbietern nutzen.

## <a name="next-steps"></a>Nächste Schritte
* [Reliable Services – Schnellstart](service-fabric-reliable-services-quick-start.md)
* [Zuverlässige Auflistungen](service-fabric-reliable-services-reliable-collections.md)
* [Das Reliable Actors-Programmiermodell](service-fabric-reliable-actors-introduction.md)
