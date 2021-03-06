---
title: Azure Media Player-Lokalisierung
description: Unterstützung mehrerer Sprachen für Benutzer aus nicht englischsprachigen Gebieten.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/05/2021
ms.openlocfilehash: e78ff237fb488a995d9161814fe61590fb1c6d5b
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449838"
---
# <a name="azure-media-player-localization"></a>Azure Media Player-Lokalisierung #

Die Unterstützung mehrerer Sprachen ermöglicht Benutzern aus nicht englischsprachigen Gebieten die native Interaktion mit dem Player. Azure Media Player wird mit einem globalen Sprachwörterbuch instanziiert, wodurch lokalisierte Fehlermeldungen ausgehend von der Sprache der Seite zur Verfügung stehen. Entwickler können außerdem eine Playerinstanz mit einer erzwungenen festgelegten Sprache erstellen. Die Standardsprache ist Englisch (en).

> [!NOTE]
> Diese Funktion unterliegt noch fortlaufenden Tests und weist daher noch Fehler auf.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin" data-setup='{"language":"es"}'>...</video>
```

Azure Media Player unterstützt aktuell die folgenden Sprachen mit ihren jeweiligen Sprachcodes:

| Sprache            | Code | Sprache                | Code   | Sprache                | Code         |
|---------------------|------|-------------------------|--------|-------------------------|--------------|
| Englisch (Standard)   | en   | Kroatisch                | hr     | Rumänisch                | ro           |
| Arabisch              | ar   | Ungarisch               | hu     | Slowakisch                  | sk           |
| Bulgarisch           | bg   | Indonesisch              | id     | Slowenisch                 | sl           |
| Katalanisch             | ca   | Isländisch               | stimmt      | Serbisch (Kyrillisch)      | sr-cyrl-cs   |
| Tschechisch               | cs   | Italienisch                 | it     | Serbisch (Lateinisch)         | sr-latn-rs   |
| Dänisch              | da   | Japanisch                | ja     | Russisch                 | ru           |
| Deutsch              | de   | Kasachisch                  | kk     | Schwedisch                 | sv           |
| Griechisch               | el   | Koreanisch                  | ko     | Thailändisch                    | th           |
| Spanisch             | es   | Litauisch              | lt     | Tagalog                 | tl           |
| Estnisch            | et   | Lettisch                 | lv     | Türkisch                 | tr           |
| Baskisch              | eu   | Malaiisch               | ms     | Ukrainisch               | uk           |
| Persisch/Dari               | fa   | Norwegisch (Bokmål)     | nb     | Urdu                    | ur           |
| Finnisch             | fi   | Niederländisch                   | nl     | Vietnamesisch              | vi           |
| Französisch              | fr   | Norwegisch (Nynorsk)     | nn     | Chinesisch (vereinfacht)    | zh-hans      |
| Galizisch            | gl   | Polnisch                  | pl     | Chinesisch (traditionell)   | zh-hant      |
| Hebräisch              | he   | Portugiesisch (Brasilien)     | pt-br  |                         |              |
| Hindi               | hi   | Portugiesisch (Portugal)   | pt-pt  |                         |              |


> [!NOTE]
> Wenn Sie Lokalisierung grundsätzlich verhindern möchten, müssen Sie die Spracheinstellung auf Englisch durchsetzen.

## <a name="next-steps"></a>Nächste Schritte ##

- [Schnellstart für Azure Media Player](azure-media-player-quickstart.md)
