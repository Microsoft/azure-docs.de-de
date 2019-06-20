---
title: Verschlüsselung ruhender Daten in Azure Cosmos DB
description: Erfahren Sie, wie Azure Cosmos DB eine Verschlüsselung ruhender Daten bereitstellt und wie diese implementiert wird.
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: b88b32b3ae2d4939d26fc268e25c87b1fb70eee5
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66237221"
---
# <a name="data-encryption-in-azure-cosmos-db"></a>Datenverschlüsselung in Azure Cosmos DB 

„Verschlüsselung ruhender Daten“ ist ein Ausdruck, der sich im Allgemeinen auf die Verschlüsselung von Daten auf permanenten Speichergeräten wie Solid State-Laufwerken (SSDs) und Festplattenlaufwerken (HDDs) bezieht. Cosmos DB speichert die primären Datenbanken auf SSDs. Die Medienanlagen und die Sicherungen werden in Azure Blob Storage gespeichert und in der Regel durch HDDs gesichert. Mit der Veröffentlichung der Verschlüsselung von ruhenden Daten für Cosmos DB werden Ihre gesamten Datenbanken, Medienanhänge und Sicherungen jetzt verschlüsselt. Damit werden Ihre Daten jetzt während der Übertragung (über das Netzwerk) und im Ruhezustand (permanenter Speicher) verschlüsselt, sodass Sie eine End-to-End-Verschlüsselung erhalten.

Als PaaS-Dienst ist Cosmos DB sehr einfach zu verwenden. Da alle in Cosmos DB gespeicherten Benutzerdaten sowohl ruhend als auch während der Übertragung verschlüsselt werden, müssen Sie keine Maßnahmen ergreifen. Anders ausgedrückt: Die Verschlüsselung ruhender Daten ist standardmäßig „aktiviert“. Es gibt keine Möglichkeit, dies ein- oder auszuschalten. Azure Cosmos DB verwendet AES-256-Verschlüsselung in allen Regionen, in denen das Konto ausgeführt wird. Wir stellen dieses Feature bereit, während wir weiterhin unsere [Verfügbarkeits- und Leistungs-SLAs](https://azure.microsoft.com/support/legal/sla/cosmos-db) erfüllen.

## <a name="implementation-of-encryption-at-rest-for-azure-cosmos-db"></a>Implementierung der Verschlüsselung von ruhenden Daten für Azure Cosmos DB

Die Verschlüsselung ruhender Daten wird über eine Reihe von Sicherheitstechnologien implementiert, einschließlich der Speichersysteme für sichere Schlüssel, verschlüsselter Netzwerke und Kryptografie-APIs. Systeme, die Daten verarbeiten und entschlüsseln, müssen mit Systemen kommunizieren, die Schlüssel verwalten. Das Diagramm veranschaulicht die Trennung zwischen der Speicherung von verschlüsselten Daten und der Verwaltung von Schlüsseln. 

![Entwurfsdiagramm](./media/database-encryption-at-rest/design-diagram.png)

Der grundlegende Ablauf bei einer Benutzeranforderung sieht wie folgt aus:
- Das Benutzerdatenbankkonto wird bereitgestellt, und die Speicherschlüssel werden über eine Anforderung an den Ressourcenanbieter für den Verwaltungsdienst abgerufen.
- Ein Benutzer stellt über HTTPS bzw. über den sicheren Transport eine Verbindung mit Cosmos DB her. (Die Details werden in den SDKs zusammengefasst.)
- Der Benutzer sendet ein zu speicherndes JSON-Dokument über die zuvor erstellte sichere Verbindung.
- Das JSON-Dokument wird indiziert, sofern der Benutzer die Indizierung nicht deaktiviert hat.
- Sowohl das JSON-Dokument als auch die Indexdaten werden in den sicheren Speicher geschrieben.
- In regelmäßigen Abständen werden Daten aus dem sicheren Speicher gelesen und im verschlüsselten Azure Blob Storage gesichert.

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

### <a name="q-how-much-more-does-azure-storage-cost-if-storage-service-encryption-is-enabled"></a>F: Wie hoch sind die Mehrkosten für Azure Storage, wenn die Speicherdienstverschlüsselung aktiviert ist?
A: Es fallen keine zusätzlichen Kosten an.

### <a name="q-who-manages-the-encryption-keys"></a>F: Wer verwaltet die Verschlüsselungsschlüssel?
A: Die Schlüssel werden von Microsoft verwaltet.

### <a name="q-how-often-are-encryption-keys-rotated"></a>F: Wie oft werden die Verschlüsselungsschlüssel ausgetauscht?
A: Microsoft verfügt über eine Reihe von internen Richtlinien für die Rotation der Verschlüsselungsschlüssel, die auch für Cosmos DB gelten. Die spezifischen Richtlinien werden nicht veröffentlicht. Microsoft veröffentlicht den [Security Development Lifecycle (SDL)](https://www.microsoft.com/sdl/default.aspx), der als eine Teilmenge der internen Richtlinien gilt und nützliche bewährte Methoden für Entwickler enthält.

### <a name="q-can-i-use-my-own-encryption-keys"></a>F: Kann ich meine eigenen Verschlüsselungsschlüssel verwenden?
A: Cosmos DB ist ein PaaS-Dienst, und wir haben uns sehr bemüht, den Dienst benutzerfreundlich zu gestalten. Wir haben festgestellt, dass diese Frage häufig stellvertretend zur Einhaltung von Complianceanforderungen wie PCI-DSS gestellt wird. Im Rahmen der Erstellung dieses Features haben wir mit Complianceprüfern zusammengearbeitet, um sicherzustellen, dass Kunden, die Cosmos DB verwenden, ihre Anforderungen erfüllen können, ohne selbst Schlüssel verwalten zu müssen.

### <a name="q-what-regions-have-encryption-turned-on"></a>F: Für welche Regionen ist die Verschlüsselung aktiviert?
A: Die Verschlüsselung ist für alle Azure Cosmos DB-Regionen und für alle Benutzerdaten aktiviert.

### <a name="q-does-encryption-affect-the-performance-latency-and-throughput-slas"></a>F: Wirkt sich die Verschlüsselung auf die SLAs hinsichtlich Leistung, Wartezeit und Durchsatz aus?
A: Es gibt keine Auswirkungen oder Änderungen hinsichtlich der Leistungs-SLAs, nachdem die Verschlüsselung ruhender Daten für alle vorhandenen und neuen Konten aktiviert ist. Informationen zu den aktuellsten Garantien finden Sie auf der Seite mit der [SLA für Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db).

### <a name="q-does-the-local-emulator-support-encryption-at-rest"></a>F: Unterstützt der lokale Emulator die Verschlüsselung ruhender Daten?
A: Der Emulator ist ein eigenständiges Entwicklungs-/Testtool und verwendet keine Schlüsselverwaltungsdienste, die vom verwalteten Cosmos DB-Dienst verwendet werden. Unsere Empfehlung ist, BitLocker auf Laufwerken zu aktivieren, auf denen Sie vertrauliche Eumlatortestdaten speichern. Der [Emulator unterstützt das Ändern des Standarddatenverzeichnisses](local-emulator.md) sowie die Verwendung eines bekannten Speicherorts.

## <a name="next-steps"></a>Nächste Schritte

Eine Übersicht über die Cosmos DB-Sicherheit und die neuesten Verbesserungen finden Sie unter [Azure Cosmos DB-Datenbanksicherheit](database-security.md).
Weitere Informationen zu Microsoft-Zertifizierungen finden Sie im [Azure Trust Center](https://azure.microsoft.com/support/trust-center/).
