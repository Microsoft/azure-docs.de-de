---
title: Problembehandlung – Azure IoT Hub-Fehler „504101 GatewayTimeout“
description: Grundlegendes zum Beheben des Fehlers „504101 GatewayTimeout“
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.custom: amqp
ms.openlocfilehash: 5d5962c43a8a3cd97b31ad3fa50bce774e2626cb
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106060940"
---
# <a name="504101-gatewaytimeout"></a>504101 GatewayTimeout

In diesem Artikel werden die Ursachen des Fehlers **504101 GatewayTimeout** und die Lösungen dafür beschrieben.

## <a name="symptoms"></a>Symptome

Wenn Sie versuchen, eine direkte Methode von IoT Hub für ein Gerät aufzurufen, schlägt die Anforderung mit der Meldung **504101 GatewayTimeout** fehl.

## <a name="cause"></a>Ursache

### <a name="cause-1"></a>Ursache 1

IoT Hub hat einen Fehler festgestellt und konnte nicht bestätigen, ob die direkte Methode vor dem Timeout abgeschlossen wurde.

### <a name="cause-2"></a>Ursache 2

Wenn eine frühere Version des Azure IoT C# SDKs (< 1.19.0) verwendet wird, kann der AMQP-Link zwischen dem Gerät und IoT Hub aufgrund eines Fehlers automatisch gelöscht werden.

## <a name="solution"></a>Lösung

### <a name="solution-1"></a>Lösung 1

Geben Sie eine Wiederholung aus.

### <a name="solution-2"></a>Lösung 2

Führen Sie ein Upgrade auf die neueste Version des Azure IoT C# SDKs durch.