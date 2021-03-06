---
title: Data Residency für Kundendaten in Azure IoT Central | Microsoft-Dokumentation
description: In diesem Artikel wird Data Residency von Kundendaten in der Azure IoT Central-Anwendung beschrieben.
author: dominicbetts
ms.author: dobett
ms.date: 11/02/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 9cf803ddd5a3c9329cadefffc35ae6d6999323a1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93280660"
---
# <a name="azure-iot-central-customer-data-residency"></a>Data Residency von Kundendaten in Azure IoT Central

In IoT Central werden keine Kundendaten außerhalb der vom Kunden angegebenen geografischen Region gespeichert, außer in den folgenden Szenarien:

- Wenn einer bestehenden IoT Central-Anwendung ein neuer Benutzer hinzugefügt wird, kann die E-Mail-ID des Benutzers außerhalb der geografischen Region gespeichert werden, bis der eingeladene Benutzer erstmals auf die Anwendung zugreift.

- Auf den Kartenkacheln des IoT Central-Dashboard wird [Azure Maps](../../azure-maps/about-azure-maps.md) verwendet. Wenn Sie einer bestehenden IoT Central-Anwendung eine Kartenkachel hinzufügen, werden die Standortdaten ggf. gemäß den Geolokalisierungsregeln des Diensts Azure Maps verarbeitet oder gespeichert.
