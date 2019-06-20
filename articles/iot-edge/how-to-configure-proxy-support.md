---
title: Konfigurieren von Geräten für Netzwerkproxys – Azure IoT Edge | Microsoft-Dokumentation
description: Erläutert das Konfigurieren der Azure IoT Edge-Runtime und der IoT Edge-Module mit Internetverbindung für die Kommunikation über einen Proxyserver.
author: kgremban
manager: ''
ms.author: kgremban
ms.date: 06/05/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 1c0da1a768b894f543b9089643622c31d6a8758d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66730143"
---
# <a name="configure-an-iot-edge-device-to-communicate-through-a-proxy-server"></a>Konfigurieren eines IoT Edge-Geräts für die Kommunikation über einen Proxyserver

IoT Edge-Geräte senden HTTPS-Anforderungen, um mit IoT Hub zu kommunizieren. Wenn Ihr Gerät mit einem Netzwerk verbunden ist, das einen Proxyserver verwendet, müssen Sie die IoT Edge-Runtime für die Kommunikation über den Server konfigurieren. Proxyserver können auch einzelne IoT Edge-Module beeinflussen, wenn sie HTTP- oder HTTPS-Anforderungen senden, die nicht über den IoT Edge-Hub weitergeleitet werden. 

Dieser Artikel geht die folgenden vier Schritte zum Konfigurieren und anschließenden Verwalten eines IoT Edge-Geräts durch, das sich hinter einem Proxyserver befindet: 

1. **Installieren Sie die IoT Edge-Runtime auf Ihrem Gerät.**

   Die IoT Edge-Installationsskripts pullen Pakete und Dateien aus dem Internet, weshalb Ihr Gerät über den Proxyserver kommunizieren können muss, um diese Anforderungen auszuführen. Ausführliche Schritte finden Sie im Abschnitt [Installieren der Runtime durch einen Proxy](#install-the-runtime-through-a-proxy) in diesem Artikel. Windows-Geräten bietet das Installationsskript außerdem eine Option für die [Offlineinstallation](how-to-install-iot-edge-windows.md#offline-installation). 

   Dieser Schritt ist ein einmaliger Prozess, der auf dem IoT Edge-Gerät ausgeführt wird, wenn Sie es zum ersten Mal einrichten. Dieselben Verbindungen sind ebenfalls erforderlich, wenn Sie IoT Edge-Runtime aktualisieren. 

2. **Konfigurieren Sie den Docker-Daemon und den IoT Edge-Daemon auf Ihrem Gerät.**

   IoT Edge verwendet zwei Daemons auf dem Gerät, die beide Webanforderungen über den Proxyserver stellen müssen. Der IoT Edge-Daemon ist verantwortlich für die Kommunikation mit dem IoT Hub. Der Moby-Daemon ist verantwortlich für die Containerverwaltung, kommuniziert also mit Containerregistrierungen. Ausführliche Schritte finden Sie im Abschnitt [Konfigurieren des Daemons](#configure-the-daemons) in diesem Artikel. 

   Dieser Schritt ist ein einmaliger Prozess, der auf dem IoT Edge-Gerät ausgeführt wird, wenn Sie es zum ersten Mal einrichten.

3. **Konfigurieren Sie die IoT Edge-Agent-Eigenschaften in der Datei „config.yaml“ auf Ihrem Gerät.**

   Der IoT Edge-Daemon startet anfänglich das edgeAgent-Modul, doch dann ist das edgeAgent-Modul verantwortlich für das Abrufen des Bereitstellungsmanifests vom IoT Hub sowie das Starten aller anderen Module. Damit der IoT Edge-Agent die anfängliche Verbindung mit dem IoT Hub herstellen kann, konfigurieren Sie die Umgebungsvariablen des edgeAgent-Moduls manuell auf dem Gerät selbst. Nach der ersten Verbindung können Sie das edgeAgent-Modul remote konfigurieren. Ausführliche Schritte finden Sie im Abschnitt [Konfigurieren des IoT Edge-Agents](#configure-the-iot-edge-agent) in diesem Artikel.

   Dieser Schritt ist ein einmaliger Prozess, der auf dem IoT Edge-Gerät ausgeführt wird, wenn Sie es zum ersten Mal einrichten.

4. **Legen Sie für alle zukünftigen Modulbereitstellungen Umgebungsvariablen für jedes Modul fest, das durch den Proxy kommuniziert.**

   Nachdem Ihr IoT Edge-Gerät eingerichtet und durch den Proxyserver mit IoT Hub verbunden ist, müssen Sie die Verbindung in allen zukünftigen Modulbereitstellungen beibehalten. Ausführliche Schritte finden Sie im Abschnitt [Konfigurieren von Bereitstellungsmanifesten](#configure-deployment-manifests) in diesem Artikel. 

   Dieser Schritt ist ein fortlaufender Prozess, der remote ausgeführt wird, sodass jedes neue Modul oder Bereitstellungsupdate die Fähigkeit des Geräts beibehält, über den Proxyserver zu kommunizieren. 

## <a name="know-your-proxy-url"></a>Ermitteln der Webproxy-URL

Bevor Sie mit einem der Schritte in diesem Artikel beginnen, müssen Sie Ihre Proxy-URL kennen.

Proxy-URLs haben das folgende Format: **Protokoll**://**Proxyhost**:**Proxyport**.

* Das **Protokoll** ist entweder HTTP oder HTTPS. Der Docker-Daemon kann abhängig von den Einstellungen Ihrer Containerregistrierung beide Protokolle verwenden, aber für den IoT Edge-Daemon und die Runtimecontainer sollte immer HTTPS verwendet werden.

* Der **Proxyhost** ist eine Adresse für den Proxyserver. Wenn Ihr Proxyserver eine Authentifizierung erfordert, können Sie Ihre Anmeldeinformationen als Teil von „Proxyhost“ mit dem folgenden Format eingeben: **Benutzer**:**Kennwort**\@**Proxyhost**.

* Der **Proxyport** ist der Netzwerkport, an dem der Proxy auf Netzwerkdatenverkehr antwortet.

## <a name="install-the-runtime-through-a-proxy"></a>Installieren der Runtime durch einen Proxy

Ob Ihr IoT Edge-Gerät unter Windows oder Linux ausgeführt wird, Sie müssen über den Proxyserver auf die Installationspakete zugreifen. Befolgen Sie in Abhängigkeit von Ihrem Betriebssystem die Schritte zum Installieren der IoT Edge-Runtime durch einen Proxyserver. 

### <a name="linux"></a>Linux

Wenn Sie IoT Edge-Runtime auf einem Linux-Gerät installieren, konfigurieren Sie den Paket-Manager so, dass er über den Proxyserver auf das Installationspaket zugreift. Beispiel: [Richten Sie „apt-get“ zur Verwendung eines http-Proxys ein](https://help.ubuntu.com/community/AptGet/Howto/#Setting_up_apt-get_to_use_a_http-proxy). Sobald Ihr Paket-Manager konfiguriert ist, befolgen Sie einfach die Anweisungen in [Installieren von Azure IoT Edge-Runtime unter Linux (ARM32v7/armhf)](how-to-install-iot-edge-linux-arm.md) oder [Installieren von Azure IoT Edge-Runtime unter Linux (x64)](how-to-install-iot-edge-linux.md).

### <a name="windows"></a>Windows

Wenn Sie IoT Edge-Runtime auf einem Windows-Gerät installieren, müssen Sie den Proxyserver zwei Mal durchlaufen. Mit der ersten Verbindung wird die Installationsskriptdatei heruntergeladen, und die zweite Verbindung erfolgt während der Installation, um die erforderlichen Komponenten herunterzuladen. Sie können Proxyinformationen in den Windows-Einstellungen konfigurieren oder Ihre Proxyinformationen direkt in die PowerShell-Befehle aufnehmen. 

Die folgenden Schritte zeigen ein Beispiel für eine Windows-Installation mithilfe des `-proxy`-Arguments:

1. Der Befehl Invoke-WebRequest benötigt Proxyinformationen, um auf das Installationsprogrammskript zuzugreifen. Anschließend benötigt der Befehl Deploy-IoTEdge die Proxyinformationen, um die Installationsdateien herunterzuladen. 

   ```powershell
   . {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; Deploy-IoTEdge -proxy <proxy URL>
   ```

2. Der Befehl Initialize-IoTEdge muss nicht über den Proxyserver gehen, daher benötigt der zweite Schritt nur Proxyinformationen für Invoke-WebRequest.

   ```powershell
   . {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; Initialize-IoTEdge

If you have complicated credentials for the proxy server that can't be included in the URL, use the `-ProxyCredential` parameter within `-InvokeWebRequestParameters`. For example,

```powershell
$proxyCredential = (Get-Credential).GetNetworkCredential()
. {Invoke-WebRequest -proxy <proxy URL> -ProxyCredential $proxyCredential -useb aka.ms/iotedge-win} | Invoke-Expression; `
Deploy-IoTEdge -InvokeWebRequestParameters @{ '-Proxy' = '<proxy URL>'; '-ProxyCredential' = $proxyCredential }
```

Weitere Informationen zu Proxyparametern finden Sie unter [Invoke-WebRequest](https://docs.microsoft.com/powershell/module/microsoft.powershell.utility/invoke-webrequest). Weitere Informationen zu Windows-Installationsoptionen, einschließlich der Offlineinstallation, finden Sie unter [Installieren der Azure IoT Edge-Runtime unter Windows](how-to-install-iot-edge-windows.md).

## <a name="configure-the-daemons"></a>Konfigurieren der Daemons

IoT Edge basiert auf zwei Daemons, die auf dem IoT Edge-Gerät ausgeführt werden. Der Moby-Daemon ruft mit Webanforderungen Containerimages aus Containerregistrierungen ab. Der IoT Edge-Daemon kommuniziert über Webanforderungen mit IoT Hub.

Sowohl der Moby- als auch der IoT Edge-Daemon muss für die Verwendung des Proxyservers konfiguriert sein, damit das Gerät fortwährend funktionieren kann. Dieser Schritt erfolgt auf dem IoT Edge-Gerät während der anfänglichen Geräteeinrichtung. 

### <a name="moby-daemon"></a>Moby-Daemon

Da Moby auf Docker basiert, finden Sie in der Docker-Dokumentation Informationen, wie der Moby-Daemon mit Umgebungsvariablen konfiguriert wird. Die meisten Containerregistrierungen (einschließlich DockerHub und Azure Container Registry-Instanzen) unterstützen HTTPS-Anforderungen, daher sollten Sie den Parameter **HTTPS_PROXY** festlegen. Wenn Sie Images aus einer Registrierung abrufen, die Transport Layer Security (TLS) nicht unterstützt, sollten Sie den Parameter **HTTP_PROXY** festlegen. 

Wählen Sie den Artikel aus, der für Ihr IoT Edge-Gerätebetriebssystem gilt: 

* [Konfigurieren des Docker-Daemons unter Linux](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy)
    * Der Moby-Daemon auf Linux-Geräten behält den Namen „Docker“.
* [Konfigurieren des Docker-Daemons unter Windows](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon#proxy-configuration)
    * Der Moby-Daemon auf Windows-Geräten wird als „iotedge-moby“ bezeichnet. Die Namen sind verschieden, da es möglich ist, Docker Desktop und Moby gleichzeitig auf einem Windows-Gerät auszuführen. 

### <a name="iot-edge-daemon"></a>IoT Edge-Daemon

Der IoT Edge-Daemon wird in ähnlicher Weise wie der Moby-Daemon konfiguriert. Verwenden Sie je nach Betriebssystem die folgenden Schritte, um eine Umgebungsvariable für den Dienst festzulegen. 

Der IoT Edge-Daemon verwendet immer HTTPS, um Anforderungen an IoT Hub zu senden.

#### <a name="linux"></a>Linux

Öffnen Sie einen Editor im Terminal, um den IoT Edge-Daemon zu konfigurieren. 

```bash
sudo systemctl edit iotedge
```

Geben Sie den folgenden Text ein, und ersetzen Sie dabei **\<proxy URL>** mit der Adresse und dem Port Ihres Proxyservers. Wählen Sie dann „Speichern“ und „Beenden“ aus. 

```ini
[Service]
Environment="https_proxy=<proxy URL>"
```

Aktualisieren Sie den Dienst-Manager, um die neue Konfiguration für IoT Edge zu übernehmen.

```bash
sudo systemctl daemon-reload
```

Starten Sie IoT Edge neu, damit die Änderungen wirksam werden.

```bash
sudo systemctl restart iotedge
```

Stellen Sie sicher, dass die Umgebungsvariable erstellt wurde und die neue Konfiguration geladen wurde. 

```bash
systemctl show --property=Environment iotedge
```

#### <a name="windows"></a>Windows

Öffnen Sie ein PowerShell-Fenster als Administrator, und führen Sie den folgenden Befehl zum Bearbeiten der Registrierung mit der neuen Umgebungsvariable aus. Ersetzen Sie **\<proxy URL>** mit der Adresse und dem Port Ihres Proxyservers. 

```powershell
reg add HKLM\SYSTEM\CurrentControlSet\Services\iotedge /v Environment /t REG_MULTI_SZ /d https_proxy=<proxy URL>
```

Starten Sie IoT Edge neu, damit die Änderungen wirksam werden.

```powershell
Restart-Service iotedge
```

## <a name="configure-the-iot-edge-agent"></a>Konfigurieren des IoT Edge-Agents

Der IoT Edge-Agent ist das erste Modul, das auf einem IoT Edge-Gerät gestartet wird. Es wird zum ersten Mal mit den Informationen in der IoT Edge-Datei „config.yaml“ gestartet. Der IoT Edge-Agent stellt anschließend eine Verbindung mit IoT Hub her, um Bereitstellungsmanifeste abzurufen, die deklarieren, welche weiteren Module auf dem Gerät bereitgestellt werden sollen.

Dieser Schritt erfolgt einmalig auf dem IoT Edge-Gerät während der anfänglichen Geräteeinrichtung. 

1. Öffnen Sie die Datei „config.yaml“ auf Ihrem IoT Edge-Gerät. In Linux-Systemen befindet sich diese Datei unter **/etc/iotedge/config.yaml**. In Windows-Systemen befindet sich diese Datei unter **C:\ProgramData\iotedge\config.yaml**. Die Konfigurationsdatei ist geschützt. Sie benötigen also Administratorrechte, um darauf zuzugreifen. Auf Linux-Systemen verwenden Sie den Befehl `sudo`, bevor Sie die Datei in Ihrem bevorzugten Text-Editor öffnen. Unter Windows öffnen Sie einen Text-Editor wie Editor als Administrator und öffnen dann die Datei. 

2. Suchen Sie in der Datei „config.yaml“ nach dem Abschnitt **Edge Agent module spec**. Die IoT Edge-Agent-Definition enthält einen **env**-Parameter, dem Sie Umgebungsvariablen hinzufügen können. 

3. Entfernen Sie die geschweiften Klammern, die Platzhalter für den env-Parameter darstellen, und fügen Sie die neue Variable in einer neuen Zeile hinzu. Denken Sie daran, dass Einzüge in YAML zwei Leerzeichen sind. 

   ```yaml
   https_proxy: "<proxy URL>"
   ```

4. Die IoT Edge-Runtime verwendet standardmäßig AMQP für die Kommunikation mit IoT Hub. Einige Proxyserver blockieren AMQP-Ports. Wenn dies der Fall ist, müssen Sie „edgeAgent“ auch so konfigurieren, dass AMQP über WebSocket verwendet wird. Fügen Sie eine zweite Umgebungsvariable hinzu.

   ```yaml
   UpstreamProtocol: "AmqpWs"
   ```

   ![edgeAgent-Definition mit Umgebungsvariablen](./media/how-to-configure-proxy-support/edgeagent-edited.png)

5. Speichern Sie die Änderungen an „config.yaml“, und schließen Sie den Editor. Starten Sie IoT Edge neu, damit die Änderungen wirksam werden. 

   * Linux: 

      ```bash
      sudo systemctl restart iotedge
      ```

   * Windows:

      ```powershell
      Restart-Service iotedge
      ```

## <a name="configure-deployment-manifests"></a>Konfigurieren von Bereitstellungsmanifesten  

Nachdem Ihr IoT Edge-Gerät für die Verwendung Ihres Proxyservers konfiguriert wurde, müssen Sie die Umgebungsvariablen auch in allen zukünftigen Bereitstellungsmanifesten deklarieren. Sie können Bereitstellungsmanifeste entweder mit dem Azure-Portal-Assistenten bearbeiten, oder indem Sie eine JSON-Bereitstellungsmanifestdatei bearbeiten. 

Konfigurieren Sie die beiden Runtimemodule („edgeAgent“ und „edgeHub“) immer so, dass sie über den Proxyserver kommunizieren, damit sie eine Verbindung mit IoT Hub aufrechterhalten können. Wenn Sie die Proxyinformationen aus dem edgeAgent-Modul entfernen, besteht die einzige Möglichkeit zum Wiederherstellen der Verbindung darin, die Datei „config.yaml“ auf dem Gerät zu bearbeiten, wie im vorherigen Abschnitt beschrieben. 

Andere IoT Edge-Module, die eine Verbindung mit dem Internet herstellen, sollten ebenfalls für die Kommunikation durch den Proxyserver konfiguriert werden. Module, die ihre Nachrichten über „edgeHub“ weiterleiten oder nur mit anderen Modulen auf dem Gerät kommunizieren, benötigen die Details zum Proxyserver nicht. 

Dieser Schritt muss über die gesamte Lebensdauer des IoT Edge-Geräts fortwährend ausgeführt werden. 

### <a name="azure-portal"></a>Azure-Portal

Wenn Sie den Assistenten **Module festlegen** zum Erstellen von Bereitstellungen für IoT Edge-Geräte verwenden, verfügt jedes Modul über einen Abschnitt **Umgebungsvariablen**, mit dem Sie Proxyserververbindungen konfigurieren können. 

Wählen Sie zum Konfigurieren der IoT Edge-Agent- und der IoT Edge-Hubmodule **Erweiterte Einstellungen für die Edge-Laufzeit konfigurieren** im ersten Schritt des Assistenten aus. 

![Erweiterte Einstellungen für die Edge-Laufzeit konfigurieren](./media/how-to-configure-proxy-support/configure-runtime.png)

Fügen Sie die Umgebungsvariable **https_proxy** den Definitionen der IoT Edge-Agent- und IoT Edge-Hub-Module hinzu. Wenn Sie die Umgebungsvariable **UpstreamProtocol** in die Datei „config.yaml“ auf Ihrem IoT Edge-Gerät eingefügt haben, fügen Sie sie auch der IoT Edge-Agent-Moduldefinition hinzu. 

![Festlegen der https_proxy-Umgebungsvariable](./media/how-to-configure-proxy-support/edgehub-environmentvar.png)

Alle anderen Module, die Sie einem Bereitstellungsmanifest hinzufügen, folgen dem gleichen Muster. Auf der Seite, auf der Sie den Modulnamen und das Image festlegen, gibt es einen Abschnitt für Umgebungsvariablen.

### <a name="json-deployment-manifest-files"></a>JSON-Bereitstellungsmanifestdateien

Wenn Sie Bereitstellungen für IoT Edge-Geräte mithilfe von Vorlagen in Visual Studio Code oder durch das manuelle Erstellen von JSON-Dateien erstellen, können Sie die Umgebungsvariablen direkt jeder Moduldefinition hinzufügen. 

Verwenden Sie das folgende JSON-Format: 

```json
"env": {
    "https_proxy": {
        "value": "<proxy URL>"
    }
}
```

Mit den Umgebungsvariablen sollte Ihre Moduldefinition wie das folgende edgeHub-Beispiel aussehen:

```json
"edgeHub": {
    "type": "docker",
    "settings": {
        "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
        "createOptions": ""
    },
    "env": {
        "https_proxy": {
            "value": "https://proxy.example.com:3128"
        }
    },
    "status": "running",
    "restartPolicy": "always"
}
```

Wenn Sie die Umgebungsvariable **UpstreamProtocol** in die Datei „confige.yaml“ auf Ihrem IoT Edge-Gerät eingefügt haben, fügen Sie sie auch der IoT Edge-Agent-Moduldefinition hinzu. 

```json
"env": {
    "https_proxy": {
        "value": "<proxy URL>"
    },
    "UpstreamProtocol": {
        "value": "AmqpWs"
    }
}
```

## <a name="next-steps"></a>Nächste Schritte

Erfahren Sie mehr über die Rollen der [IoT Edge-Runtime](iot-edge-runtime.md).

Behandeln Sie Installations- und Konfigurationsfehler mit [Häufig auftretende Probleme und Lösungen für Azure IoT Edge](troubleshoot.md).

