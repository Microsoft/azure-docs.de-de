---
title: Verwenden eines vorhandenen IoT Hubs mit dem Solution Accelerator für Gerätesimulation – Azure | Microsoft-Dokumentation
description: Dieser Artikel beschreibt, wie Sie ein benutzerdefiniertes Gerätemodell im Solution Accelerator für Gerätesimulation konfigurieren.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/25/2018
ms.topic: conceptual
ms.openlocfilehash: 38cde750ce07741a433baa1b8607a584f94ad9b1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62123657"
---
# <a name="use-an-existing-iot-hub-with-the-device-simulation-solution-accelerator"></a>Verwenden eines vorhandenen IoT Hubs mit dem Solution Accelerator für Gerätesimulation

Wenn Sie die Gerätesimulation bereitstellen, können Sie auch einen IoT-Hub zur Verwendung Ihrer Simulation bereitstellen. Mit dieser Option wird ein [IoT-Hub im Tarif S2 mit einer einzelnen Skalierungseinheit](../iot-hub/iot-hub-scaling.md) bereitgestellt. Wenn Sie diesen optionalen IoT-Hub bereitstellen, können Sie weiterhin einen anderen IoT-Hub als Ziel für eine Ausführung der Simulation auswählen.

Wenn Sie den optionalen IoT-Hub nicht bereitstellen, müssen Sie Ihren eigenen Hub für alle ausgeführten Simulationen verwenden.

Wenn Sie keinen IoT Hub besitzen, können Sie jederzeit im [Azure-Portal](https://portal.azure.com) einen erstellen.

Um einen bereits vorhandenen IoT-Hub verwenden zu können, benötigen Sie die Verbindungszeichenfolge für die SAS-Richtlinie **iothubowner**. Sie können diese Verbindungszeichenfolge aus dem [Azure-Portal](https://portal.azure.com) abrufen:

1. Klicken Sie auf der Konfigurationsseite des Hubs im Portal auf **Freigegebene Zugriffsrichtlinien**.

1. Klicken Sie auf **iothubowner**.

1. Kopieren Sie die primäre oder die sekundäre Verbindungszeichenfolge.

[![Abrufen der Verbindungszeichenfolge](./media/iot-accelerators-device-simulation-choose-hub/connectionstring-inline.png)](./media/iot-accelerators-device-simulation-choose-hub/connectionstring-expanded.png#lightbox)

Verwenden Sie beim Konfigurieren der Simulation die Verbindungszeichenfolge, die Sie kopiert haben:

![Konfigurieren der Simulation](./media/iot-accelerators-device-simulation-choose-hub/configuresimulation.png)

### <a name="next-steps"></a>Nächste Schritte

In diesem Leitfaden haben Sie gelernt, wie Sie einen vorhandenen IoT Hub in einer Simulation verwenden. Als Nächstes sollten Sie lernen, wie Sie [ein erweitertes Gerätemodell für eine Simulation erstellen](iot-accelerators-device-simulation-advanced-device.md).
