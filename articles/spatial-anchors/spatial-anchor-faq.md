---
title: Häufig gestellte Fragen zu Azure Spatial Anchors | Microsoft-Dokumentation
description: Azure Spatial Anchors ist ein verwalteter Clouddienst und eine Entwicklerplattform für geräteübergreifende Mixed Reality-Umgebungen mit mehreren Benutzern für HoloLens-, iOS- und Android-Geräte. Bei diesen häufig gestellten Fragen geht es um die technische Seite des Diensts.
author: ramonarguelles
manager: vicenterivera
services: azure-spatial-anchors
ms.author: rgarcia
ms.date: 02/24/2019
ms.topic: overview
ms.service: azure-spatial-anchors
ms.openlocfilehash: cd004e1eb6a40648c52dd3b5aee0ec38bff8f9b3
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/29/2019
ms.locfileid: "66304181"
---
# <a name="frequently-asked-questions-about-azure-spatial-anchors"></a>Häufig gestellte Fragen zu Azure Spatial Anchors

Azure Spatial Anchors ist ein verwalteter Clouddienst und eine Entwicklerplattform für Mixed Reality-Umgebungen mit mehreren Benutzern und räumlichem Bezug für HoloLens-, iOS- und Android-Geräte.

Weitere Informationen finden Sie unter [Azure Spatial Anchors-Übersicht](overview.md).

## <a name="azure-spatial-anchors-product-faqs"></a>Azure Spatial Anchors: Häufig gestellte Fragen zum Produkt

**F: Welche Geräte unterstützt Azure Spatial Anchors?**

**A:** Mit Azure Spatial Anchors können Entwickler Apps für HoloLens, iOS-Geräte mit ARKit-Unterstützung und Android-Geräte mit ARCore-Unterstützung erstellen. Für iOS und Android gilt dies sowohl für Smartphones als auch für Tablets.

**F: Muss eine Verbindung mit der Cloud bestehen, um Azure Spatial Anchors verwenden zu können?**

**A:** Für Azure Spatial Anchors ist derzeit eine Netzwerkverbindung mit dem Internet erforderlich. Wir freuen uns über Ihre Kommentare auf unserer [Website für Feedback](https://feedback.azure.com/forums/919252-azure-spatial-anchors).

**F: Welche Konnektivitätsanforderungen gelten für Azure Spatial Anchors?**

**A:** Azure Spatial Anchors funktioniert für WLAN- und mobile Breitbandverbindungen.

**F: Wie präzise können mit Azure Spatial Anchors Anker gefunden werden?**

**A:** Es gibt viele Faktoren, die sich auf das Präzision beim Auffinden von Ankern auswirken: Lichtverhältnisse, Objekte in der Umgebung und sogar die Oberfläche, auf der der Anker angeordnet ist. Gehen Sie wie folgt vor, um zu ermitteln, ob die Präzision für Ihre Zwecke ausreicht: Probieren Sie die Anker in Umgebungen aus, die für die später verwendeten Umgebungen repräsentativ sind. Falls Umgebungen vorhanden sind, in denen die Präzision Ihre Anforderungen nicht erfüllt, helfen Ihnen die Informationen unter [Logging and diagnostics in Azure Spatial Anchors](./concepts/logging-diagnostics.md) (Protokollierung und Diagnose in Azure Spatial Anchors) weiter.

**F: Wie lange dauert es, Anker zu erstellen und zu finden?**

**A:** Die Dauer für das Erstellen und Finden von Ankern hängt von vielen Faktoren ab: Netzwerkverbindung, Verarbeitungseigenschaften und Last des Geräts und spezifische Umgebung. Unsere Kunden entwickeln Anwendungen in vielen Branchen, z. B. Fertigung, Einzelhandel und Gaming. Dies verdeutlicht, dass der Dienst in all diesen Fällen die Schaffung einer hervorragenden Benutzerumgebung für das jeweilige Szenario ermöglicht.

## <a name="privacy-faq"></a>Datenschutz: Häufig gestellte Fragen

**F: Wenn meine Anwendung einen räumlichen Anker an einem bestimmten Ort platziert, haben dann alle Apps Zugriff darauf?**

**A:** Anker liegen jeweils nach Azure-Konto isoliert vor. Nur Apps, denen Sie Zugriff auf Ihr Konto gewähren, können im Konto auf Anker zugreifen.

**F: Welche Informationen zu einer Umgebung werden bei Verwendung von Azure Spatial Anchors für den Dienst übertragen und gespeichert? Werden Bilder der Umgebung übertragen und gespeichert?**

**A:** Beim Erstellen oder Suchen nach Ankern werden Bilder der Umgebung auf dem Gerät in einem abgeleiteten Format verarbeitet. Dieses abgeleitete Format wird an den Dienst übertragen und darunter gespeichert.

Um dies transparenter darzustellen, ist unten eine Abbildung einer Umgebung und der abgeleiteten Punktwolke mit den Verknüpfungspunkten angegeben. Die Punktwolke veranschaulicht die geometrische Darstellung der Umgebung, die übertragen und unter dem Dienst gespeichert wird. Für jeden Punkt der Punktwolke mit den Verknüpfungspunkten übertragen und speichern wir jeweils einen Hash mit seinen visuellen Merkmalen. Der Hash wird von den Pixeldaten abgeleitet, aber diese sind nicht im Hash enthalten.

Für Azure Spatial Anchors werden [Servicevertrag & Bestimmungen](https://go.microsoft.com/fwLink/?LinkID=522330&amp;amp;clcid=0x9) und die [Datenschutzerklärung von Microsoft](https://go.microsoft.com/fwlink/?LinkId=521839&amp;clcid=0x409) eingehalten.

![Umgebung und abgeleitete Punktwolke mit Verknüpfungspunkten](./media/sparse-point-cloud.png)
*Abbildung 1: Umgebung und abgeleitete Punktwolke mit Verknüpfungspunkten*


**F: Gibt es eine Möglichkeit zum Senden von Diagnoseinformationen an Microsoft?**

**A:** Ja. Azure Spatial Anchors verfügt über einen Diagnosemodus, den Entwickler über die Azure Spatial Anchors-API nutzen können. Dies ist beispielsweise hilfreich, wenn Sie in einer Umgebung Anker nicht auf vorhersagbare Weise erstellen und finden können. Unter Umständen werden Sie von uns gefragt, ob Sie einen Diagnosebericht mit Informationen übermitteln können, die für den Debugvorgang nützlich sind. Weitere Informationen finden Sie unter [Logging and diagnostics in Azure Spatial Anchors](./concepts/logging-diagnostics.md) (Protokollierung und Diagnose in Azure Spatial Anchors).

## <a name="availability-and-pricing-faqs"></a>Verfügbarkeit und Preise: Häufig gestellte Fragen

**F: Wird eine Vereinbarung zum Servicelevel (SLA) bereitgestellt?**

**A:** Wie bei Azure-Diensten üblich, streben wir eine Verfügbarkeit von über 99,9 % an. Beachten Sie, dass sich Azure Spatial Anchors derzeit in der Vorschauphase befindet und daher die Angaben unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) gelten.

**F: Kann ich meine Apps mit Azure Spatial Anchors in App Stores veröffentlichen? Kann ich Azure Spatial Anchors für unternehmenskritische Produktionsszenarien nutzen?**

**A:** Azure Spatial Anchors befindet sich derzeit in der Vorschauphase. Während dieser Phase laden wir Sie ein, Apps zu entwickeln, [Feedback zum Produkt zu geben](https://feedback.azure.com/forums/919252-azure-spatial-anchors) und Ihre Bereitstellungen für die Produktion zu planen.

Die Zeitpunkte für die allgemeine Verfügbarkeit (General Availability, GA) werden in Kürze bekanntgegeben.

**F: Gelten bestimmte Drosselungslimits?**
 
**A:** Ja, es gelten Drosselungslimits.  Es ist nicht zu erwarten, dass Sie diese Limits bei typischen Vorgängen der Anwendungsentwicklung und beim Testen überschreiten. Für Produktionsbereitstellungen haben wir das Ziel, die hohen Skalierungsanforderungen unserer Kunden zu unterstützen. [Nehmen Sie Kontakt mit uns auf](mailto:azuremrs@microsoft.com), um dies mit uns zu besprechen. Während dieser Phase der Vorschauversion haben wir unsere Tiering- und Preisstruktur noch nicht veröffentlicht, aber dies soll in Kürze erfolgen.

**F: In welchen Regionen ist Azure Spatial Anchors verfügbar?**

**A:** Sie können noch heute ein Azure Spatial Anchors-Konto in der Azure-Region „USA, Osten 2“ erstellen. Dies bedeutet, dass sich sowohl die Compute- als auch die Speicherressourcen dieses Diensts in dieser Region befinden. Es gelten aber keine Einschränkungen in Bezug darauf, wo sich Ihre Clients befinden. In Zukunft werden wir die regionale Verfügbarkeit des Diensts auf alle primären Azure-Regionen erweitern.

**F: Fallen für Azure Spatial Anchors Gebühren an? Wann werden diese Gebühren berechnet?**

**A:** Ausführliche Informationen zu den Preisen, die während der Vorschauphase gelten, finden Sie auf unserer [Seite mit den Preisen](https://azure.microsoft.com/pricing/details/spatial-anchors/).

## <a name="technical-faqs"></a>Häufig gestellte Fragen: Technik

**F: Wie funktioniert Azure Spatial Anchors?**

**A:** Azure Spatial Anchors basiert auf der Mixed Reality/Augmented Reality-Nachverfolgung. Diese Nachverfolgungsmodule (Tracker) überwachen die Umgebung mit Kameras und verfolgen das Gerät mit „6DoF“ (6-degrees-of-freedom) nach, während es sich durch den Raum bewegt.

Bei Verwendung eines 6DoF-Trackers als Baustein können Sie mit Azure Spatial Anchors in Ihren realen Umgebungen bestimmte wichtige Punkte als „Ankerpunkte“ festlegen. Beispielsweise können Sie einen Anker verwenden, um Inhalt an einem bestimmten Ort in der realen Welt zu rendern.

Wenn Sie einen Anker erstellen, erfasst das Client-SDK die Umgebungsinformationen in der Nähe dieses Punkts und überträgt sie an den Dienst. Wenn dann ein anderes Gerät an demselben Ort nach dem Anker sucht, werden ähnliche Daten an den Dienst übertragen. Diese Daten werden mit den zuvor gespeicherten Umgebungsdaten abgeglichen. Die Position des Ankers relativ zum Gerät wird dann für die Nutzung in der Anwendung zurückgesendet.

**F: Wie kann Azure Spatial Anchors mit ARKit und ARCore unter iOS und Android integriert werden?**

**A:** Azure Spatial Anchors nutzt die nativen Nachverfolgungsfunktionen von ARKit und ARCore. Darüber hinaus verfügen unsere SDKs für iOS und Android über Funktionen, z. B. die Speicherung von Ankern in einem verwalteten Clouddienst. Ihre Apps können diese Anker dann leicht wiederfinden, indem sie eine Verbindung mit dem Dienst herstellen.

**F: Wie kann Azure Spatial Anchors mit HoloLens integriert werden?**

**A:** Azure Spatial Anchors nutzt die nativen Nachverfolgungsfunktionen von HoloLens. Wir stellen ein Azure Spatial Anchors SDK zum Erstellen von Apps für HoloLens bereit. Das SDK wird mit den nativen HoloLens-Funktionen integriert und stellt zusätzliche Funktionen bereit. Mit diesen Funktionen wird es beispielsweise ermöglicht, dass App-Entwickler Anker in einem verwalteten Clouddienst speichern und Ihre Apps diese Anker wiederfinden können, indem eine Verbindung mit dem Dienst hergestellt wird.

**F: Welche Plattformen und Sprachen werden von Azure Spatial Anchors unterstützt?**

**A:** Entwickler können Apps mit Azure Spatial Anchors erstellen, indem sie vertraute Tools und Frameworks für ihr Gerät verwenden:

- Unity für HoloLens, iOS und Android
- Swift oder Objective-C unter iOS
- Java oder das Android NDK unter Android
- C++/WinRT für HoloLens

[Hier finden Sie Informationen zu den ersten Entwicklungsschritten](index.yml).

**F: Ist die Nutzung mit Unreal möglich?**

**A:** Wir erwarten, dass die Unterstützung für Unreal in Kürze verfügbar ist.

**F: Ist die Nutzung mit Xamarin möglich?**

**A:** Ja. Wir stellen kein Xamarin SDK bereit, aber wir rechnen damit, dass Entwickler Azure Spatial Anchors in ihren Xamarin-Apps nutzen können, indem sie die Integration mit der Azure Spatial Anchors-API durchführen.
