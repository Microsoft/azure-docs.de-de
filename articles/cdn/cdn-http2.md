---
title: HTTP/2-Unterstützung in Azure CDN | Microsoft-Dokumentation
description: Erhalten Sie Informationen zur HTTP/2- und CDN-Unterstützung.
services: cdn
documentationcenter: ''
author: lichard
manager: erikre
editor: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/04/2017
ms.author: rli
ms.openlocfilehash: 2d27cd54486a08e18fe74c852af29d5cf6432023
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60737073"
---
# <a name="http2-support-in-azure-cdn"></a>HTTP/2-Unterstützung in Azure CDN

HTTP/2 ist eine größere Überarbeitung von HTTP/1.1\. Sie bietet eine höhere Webleistung, kürzere Antwortzeiten und eine verbesserte Benutzeroberfläche, während gleichzeitig die gewohnten HTTP-Methoden, Statuscodes und Semantik beibehalten werden. Obwohl HTTP/2 darauf ausgelegt ist, unter HTTP und HTTPS zu funktionieren, unterstützen viele Clientwebbrowser HTTP/2 nur über TLS.

### <a name="http2-benefits"></a>Vorteile von HTTP/2

HTTP/2 bietet unter anderem folgende Vorteile:

*   **Multiplexing und Parallelität**

    Bei der Verwendung von HTTP 1.1 benötigt man für die Anforderung mehrerer Ressourcen mehrere TCP-Verbindungen, und mit jeder Verbindung ist Leistungsaufwand verbunden. HTTP/2 ermöglicht die Anforderung mehrerer Ressourcen über eine einzelne TCP-Verbindung.

*   **Header-Komprimierung**

    Durch das Komprimieren von HTTP-Headern für bereitgestellte Ressourcen wird die Zeit bei der Übertragung erheblich reduziert.

*   **Datenstrom-Abhängigkeiten**

    Datenstromabhängigkeiten ermöglichen dem Client, dem Server anzugeben, welche Ressourcen Vorrang haben.


## <a name="http2-browser-support"></a>HTTP/2-Browserunterstützung

Alle wichtigen Browser haben HTTP/2-Unterstützung in ihren aktuellen Versionen implementiert. Nicht unterstützte Browser führen ein automatisches Fallback auf HTTP/1.1 durch.

|"Browser"|Mindestversion|
|-------------|------------|
|Microsoft Edge| 12|
|Google Chrome| 43|
|Mozilla Firefox| 38|
|Opera| 32|
|Safari| 9|

## <a name="enabling-http2-support-in-azure-cdn"></a>Aktivieren von HTTP/2-Unterstützung in Azure CDN

Derzeit ist HTTP/2-Unterstützung für alle Azure CDN-Profile aktiviert. Es ist keine weitere Aktion vom Kunden erforderlich.

## <a name="next-steps"></a>Nächste Schritte

Sehen Sie sich [diese Demo von Akamai](https://http2.akamai.com/demo) an, um die Vorteile der HTTP/2 in Aktion sehen zu können.

Weitere Informationen zu HTTP/2 finden Sie in den folgenden Ressourcen:

*   [HTTP/2-Homepage](https://http2.github.io/) (in englischer Sprache)
*   [Häufig gestellte Fragen zu HTTP/2 (offiziell)](https://http2.github.io/faq/) (in englischer Sprache)
*   [Akamai HTTP/2-Informationen](https://http2.akamai.com/) (in englischer Sprache)

Weitere Informationen zu verfügbaren Azure CDN-Features finden Sie in der [Übersicht über das Azure CDN](https://azure.microsoft.com/documentation/articles/cdn-overview/).