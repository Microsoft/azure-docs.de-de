---
title: Microsoft Translator-Dienst
titlesuffix: Azure Cognitive Services
description: Integrieren Sie Translator in Ihre Anwendungen, Websites, Tools und in andere Lösungen, um Benutzererlebnisse in mehreren Sprachen bereitzustellen.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.topic: overview
ms.subservice: translator-text
ms.date: 03/15/2021
ms.author: lajanuar
ms.custom: cog-serv-seo-aug-2020
keywords: translator, text translation, machine translation, translation service
ms.openlocfilehash: 8ece17a0f1452c7ea7f90fc5e14758c03ac36651
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/19/2021
ms.locfileid: "110076508"
---
# <a name="what-is-the-translator-service"></a>Was ist der Translator-Dienst?

Translator ist ein Clouddienst für maschinelle Übersetzung und gehört zur [Azure Cognitive Services](../../index.yml?panel=ai&pivot=products)-Familie kognitiver APIs für die Erstellung intelligenter Apps. Translator lässt sich einfach in Ihre Anwendungen, Websites, Tools und Lösungen integrieren. Mit dieser App können mehrsprachige Benutzeroberflächen in [mehr als 90 Sprachen und Dialekten](./language-support.md) bereitgestellt werden. Darüber hinaus kann sie unter jedem Betriebssystem für die Textübersetzung verwendet werden.

Diese Dokumentation enthält die folgenden Arten von Artikeln:

* [**Schnellstarts**](quickstart-translator.md) sind Anleitungen zu den ersten Schritten, die Sie durch das Senden von Anforderungen an den Dienst führen.
* [**Schrittanleitungen**](translator-how-to-signup.md) enthalten Anweisungen zur spezifischeren oder individuelleren Verwendung des Diensts.
* [**Tutorials**](tutorial-wpf-translation-csharp.md) sind ausführlichere Leitfäden, in denen die Verwendung des Diensts als Komponente in umfassenderen Unternehmenslösungen veranschaulicht wird.

## <a name="about-microsoft-translator"></a>Informationen zu Microsoft Translator

Der Übersetzer kommt in zahlreichen Produkten und Diensten von Microsoft zum Einsatz und wird in Anwendungen und Workflows von Tausenden Unternehmen weltweit genutzt.

Die von Translator unterstützte Sprachübersetzung ist ebenfalls über den [Azure Speech-Dienst](../speech-service/index.yml) verfügbar. Sie vereint Funktionen der Sprachübersetzungs-API und von Custom Speech Service  in einem einheitlichen und vollständig anpassbaren Dienst.

## <a name="language-support"></a>Sprachunterstützung

Der Translator-Dienst unterstützt mehrere Sprachen für Textübersetzung, Transliteration, Spracherkennung und Wörterbücher. Eine vollständige Liste finden Sie unter [Sprach- und Regionsunterstützung für die Textübersetzungs-API](language-support.md). Sie können auch programmgesteuert mit der [REST-API](./reference/v3-0-languages.md) auf die Liste zugreifen.

## <a name="microsoft-translator-neural-machine-translation"></a>Neuronale maschinelle Übersetzungen von Microsoft Translator

Neuronale maschinelle Übersetzungen (Neural Machine Translation, NMT) sind der neue Standard für hochwertige, KI-basierte Übersetzungen. Sie ersetzen die veraltete SMT-Technologie (Statistical Machine Translation, statistische maschinelle Übersetzung), die Mitte der 2010er Jahre Ihren Zenit erreicht hat.

Im Vergleich zu SMT liefert NMT bessere Übersetzungen – nicht nur im Hinblick auf die grundsätzliche Übersetzungsqualität, sondern auch im Hinblick auf Textfluss und Natürlichkeit. Der Hauptgrund für diesen Textfluss besteht darin, dass NMT bei der Übersetzung von Wörtern den gesamten Kontext eines Satzes berücksichtigt. Bei SMT wird dagegen nur der unmittelbare Kontext einiger weniger Wörter vor und nach jedem Wort berücksichtigt.

NMT-Modelle sind das Herzstück der API und für Endbenutzer nicht sichtbar. Der einzige wahrnehmbare Unterschied ist die höhere Übersetzungsqualität – insbesondere für Sprachen wie Chinesisch, Japanisch und Arabisch.

Erfahren Sie mehr über die [Funktionsweise der neuronalen maschinellen Übersetzung](https://www.microsoft.com/translator/mt.aspx#nnt).

## <a name="improve-translations-with-custom-translator"></a>Verbessern von Übersetzungen mit dem benutzerdefinierten Translator

 [Benutzerdefinierter Translator](customization.md) ist eine Erweiterung des Übersetzerdiensts und kann verwendet werden, um das neuronale Übersetzungssystem anzupassen und die Übersetzung für Ihre spezifische Terminologie und Ihren individuellen Stil zu verbessern.

Mit dem benutzerdefinierten Translator können Sie Übersetzungssysteme erstellen, die Ihre unternehmens- oder branchenspezifische Terminologie verwenden. Mithilfe des Kategorieparameters können Sie Ihr benutzerdefiniertes Übersetzungssystem ganz einfach über den regulären Übersetzerdienst mit Ihren vorhandenen Anwendungen, Workflows, Websites und Geräten integrieren.

## <a name="next-steps"></a>Nächste Schritte

* [Erstellen Sie einen Übersetzerdienst](./translator-how-to-signup.md), um Ihre Zugriffsschlüssel und Ihren Endpunkt abzurufen.
* Testen Sie den [Schnellstart](quickstart-translator.md), um den Translator-Dienst schnell aufzurufen.
* Die [API-Referenz](./reference/v3-0-reference.md) bietet die technische Dokumentation für die APIs.
* [Preisübersicht](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)
