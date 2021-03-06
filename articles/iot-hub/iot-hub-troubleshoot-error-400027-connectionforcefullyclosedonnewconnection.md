---
title: Problembehandlung – Azure IoT Hub-Fehler „400027 ConnectionForcefullyClosedOnNewConnection“
description: Grundlegendes zum Beheben des Fehlers „400027 ConnectionForcefullyClosedOnNewConnection“
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.custom:
- mqtt
- fasttrack-edit
- iot
ms.openlocfilehash: cd531abe1a10abead26055c6b0d4dd328cd3e836
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061365"
---
# <a name="400027-connectionforcefullyclosedonnewconnection"></a>400027 ConnectionForcefullyClosedOnNewConnection

In diesem Artikel werden die Ursachen des Fehlers **400027 ConnectionForcefullyClosedOnNewConnection** und die Lösungen dafür beschrieben.

## <a name="symptoms"></a>Symptome

Die Verbindung Ihres Geräts wird unter Verwendung des .NET SDK und des MQTT-Transporttyps mit **Communication_Error** als **ConnectionStatusChangeReason** getrennt.

Ihr Gerät-zu-Cloud-Zwillingsvorgang (z. B. gemeldete Eigenschaften lesen oder patchen) oder der direkte Methodenaufruf schlägt mit dem Fehlercode **400027** fehl.

## <a name="cause"></a>Ursache

Weil von einem anderen Client eine neue Verbindung mit IoT Hub unter Verwendung derselben Anmeldeinformationen erstellt wurde, hat IoT Hub die vorherige Verbindung geschlossen. IoT Hub lässt nicht zu, dass mehr als ein Client eine Verbindung unter Verwendung desselben Satzes von Anmeldeinformationen herstellt.

## <a name="solution"></a>Lösung

Stellen Sie sicher, dass jeder Client eine Verbindung mit IoT Hub unter Verwendung seiner eigenen Identität herstellt.