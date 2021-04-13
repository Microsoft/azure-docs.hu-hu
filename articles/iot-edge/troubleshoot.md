---
title: Hibakeresés – Azure IoT Edge | Microsoft Docs
description: Ebből a cikkből megtudhatja, hogyan sajátíthatja el a Azure IoT Edge általános diagnosztikai képességeit, például az összetevők állapotának és naplóinak beolvasását
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/01/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 6fa49af946a1e5fc631eeb1ee9b9c7c99d3adff8
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107308268"
---
# <a name="troubleshoot-your-iot-edge-device"></a>A IoT Edge eszköz hibáinak megoldása

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Ha a környezetében Azure IoT Edge futtatott problémákat tapasztal, a hibaelhárításhoz és a diagnosztikahez tekintse meg a cikk útmutatását.

## <a name="run-the-check-command"></a>A "pipa" parancs futtatása

Az első lépés a IoT Edge hibaelhárításakor a `check` parancs használata, amely a konfigurációs és kapcsolati tesztek gyűjteményét futtatja a gyakori problémákhoz. A `check` parancs a [kiadási 1.0.7](https://github.com/Azure/azure-iotedge/releases/tag/1.0.7) és újabb verziókban érhető el.

>[!NOTE]
>A hibaelhárítási eszköz nem tudja futtatni a kapcsolati ellenőrzéseket, ha a IoT Edge-eszköz proxykiszolgáló mögött van.

A parancsot a következőképpen futtathatja `check` , vagy belefoglalhatja a `--help` jelzőt a lehetőségek teljes listájának megjelenítéséhez:

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
Linux rendszeren:

```bash
sudo iotedge check
```

Windows rendszeren:

```powershell
iotedge check
```

:::moniker-end
<!-- end 1.1 -->

<!-- 1.1 -->
:::moniker range=">=iotedge-2020-11"

```bash
sudo iotedge check
```

:::moniker-end
<!-- end 1.2 -->

A hibaelhárító eszköz sok olyan ellenőrzést futtat, amelyek a következő három kategóriába vannak rendezve:

* A *konfigurációs ellenőrzések* olyan részleteket vizsgálnak meg, amelyek megakadályozhatják, hogy IoT Edge eszközök csatlakozzanak a felhőhöz, beleértve a konfigurációs fájllal és a tároló motorral kapcsolatos problémákat is.
* A *kapcsolat ellenőrzi* , hogy a IoT Edge futtatókörnyezet hozzáférhet-e a gazdagép-eszköz portjaihoz, és hogy az összes IoT Edge-összetevő csatlakozni tud-e a IoT hubhoz. Ez az ellenőrzés hibákat ad vissza, ha a IoT Edge eszköz proxy mögött van.
* Az *éles üzemi készültségi ellenőrzések* az ajánlott éles környezetek, például az eszközök hitelesítésszolgáltatói tanúsítványai és a modul naplófájljai konfigurációjának állapotát keresik.

A IoT Edge-ellenőrzési eszköz tárolót használ a diagnosztika futtatásához. A tároló képe `mcr.microsoft.com/azureiotedge-diagnostics:latest` a [Microsoft Container Registryon](https://github.com/microsoft/containerregistry)keresztül érhető el. Ha az internethez való közvetlen hozzáférés nélkül kell ellenőriznie az eszközt, az eszköznek hozzá kell férnie a tároló rendszerképéhez.

<!-- <1.2> -->
:::moniker range=">=iotedge-2020-11"

Beágyazott IoT Edge-eszközöket használó forgatókönyv esetén elérheti a diagnosztikai rendszerképet a gyermek eszközökön úgy, hogy átirányítja a rendszerképet a fölérendelt eszközökön.

```bash
sudo iotedge check --diagnostics-image-name <parent_device_fqdn_or_ip>:<port_for_api_proxy_module>/azureiotedge-diagnostics:1.2
```

<!-- </1.2> -->
:::moniker-end

További információ az eszköz által futtatott diagnosztikai ellenőrzésekről, beleértve a teendőket, ha hibaüzenetet vagy figyelmeztetést kap: [IoT Edge-hibakeresési ellenőrzés](https://github.com/Azure/iotedge/blob/master/doc/troubleshoot-checks.md).

## <a name="gather-debug-information-with-support-bundle-command"></a>Hibakeresési adatok összegyűjtése a "support-Bundle" paranccsal

Ha IoT Edge-eszközről kell összegyűjtenie a naplókat, a legkényelmesebb módszer a `support-bundle` parancs használata. Alapértelmezés szerint ez a parancs a modult, IoT Edge a Security Manager és a Container Engine-naplókat, a `iotedge check` JSON-kimenetet és más hasznos hibakeresési információkat gyűjt. Egyetlen fájlba tömöríti őket, így könnyen megosztható. A `support-bundle` parancs a [kiadási 1.0.9](https://github.com/Azure/azure-iotedge/releases/tag/1.0.9) és újabb verziókban érhető el.

Futtassa a `support-bundle` parancsot a `--since` jelzővel annak megadásához, hogy a múltban mennyi ideig szeretné lekérni a naplókat. Például az utolsó hat óra óta az elmúlt hat órában, az elmúlt hat `6h` `6d` `6m` percben és így tovább. A `--help` beállítások teljes listájának megjelenítéséhez adja meg a jelzőt.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

Linux rendszeren:

```bash
sudo iotedge support-bundle --since 6h
```

Windows rendszeren:

```powershell
iotedge support-bundle --since 6h
```

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

```bash
sudo iotedge support-bundle --since 6h
```

:::moniker-end
<!-- end 1.2 -->

[Közvetlen metódussal](how-to-retrieve-iot-edge-logs.md#upload-support-bundle-diagnostics) is meghívhatja az eszközt, hogy feltöltse a support-Bundle parancs kimenetét az Azure Blob Storageba.

> [!WARNING]
> A parancs kimenete `support-bundle` tartalmazhatja a gazdagép, az eszköz és a modul nevét, a modulok által naplózott adatokat stb. Kérjük, vegye figyelembe, hogy ha a kimenetet egy nyilvános fórumon osztja meg.

## <a name="check-your-iot-edge-version"></a>A IoT Edge verziójának keresése

Ha az IoT Edge egy régebbi verzióját futtatja, az új verzióra történő frissítés megoldhatja a problémát. Az `iotedge check` eszköz ellenőrzi, hogy a IoT Edge biztonsági démon a legújabb verzió-e, de nem ellenőrzi az IoT Edge hub és az ügynök moduljainak verzióját. Az eszközön futó futásidejű modulok verziójának vizsgálatához használja a parancsokat és a `iotedge logs edgeAgent` parancsot `iotedge logs edgeHub` . A verziószámot a rendszer a naplókban határozza meg a modul indításakor.

Az eszköz frissítésével kapcsolatos utasításokért tekintse meg [a IoT Edge biztonsági démon és futtatókörnyezet frissítése](how-to-update-iot-edge.md)című témakört.

## <a name="verify-the-installation-of-iot-edge-on-your-devices"></a>IoT Edge telepítésének ellenőrzése az eszközökön

Az eszközökön IoT Edge telepítésének ellenőrzéséhez tekintse meg [a edgeAgent-modul Twin](./how-to-monitor-module-twins.md)utasításait.

Ha a legújabb edgeAgent-modult szeretné beszerezni, futtassa a következő parancsot [Azure Cloud Shell](https://shell.azure.com/):

   ```azurecli-interactive
   az iot hub module-twin show --device-id <edge_device_id> --module-id $edgeAgent --hub-name <iot_hub_name>
   ```

Ezzel a paranccsal a rendszer az összes [jelentett edgeAgent-tulajdonságot](./module-edgeagent-edgehub.md)megjeleníti. Íme néhány hasznos funkció az eszköz állapotának figyeléséhez:

* futtatókörnyezet állapota
* futtatókörnyezet indítási ideje
* futtatókörnyezet utolsó kilépésének időpontja
* futásidejű újraindítások száma

## <a name="check-the-status-of-the-iot-edge-security-manager-and-its-logs"></a>A IoT Edge Security Manager és a naplók állapotának ellenõrzése

A [IoT Edge Security Manager](iot-edge-security-manager.md) felelős az olyan műveletekért, mint a IoT Edge rendszer inicializálásakor az indítási és kiépítési eszközökön. Ha IoT Edge nem indul el, a Security Manager naplói hasznos információkat nyújthatnak.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"
Linux rendszeren:

* A IoT Edge Security Manager állapotának megtekintése:

   ```bash
   sudo systemctl status iotedge
   ```

* A IoT Edge Security Manager naplóinak megtekintése:

   ```bash
   sudo journalctl -u iotedge -f
   ```

* Tekintse meg a IoT Edge Security Manager részletes naplóit:

  1. IoT Edge Daemon-beállítások szerkesztése:

     ```bash
     sudo systemctl edit iotedge.service
     ```

  2. Frissítse a következő sorokat:

     ```bash
     [Service]
     Environment=IOTEDGE_LOG=debug
     ```

  3. Indítsa újra a IoT Edge biztonsági démont:

     ```bash
     sudo systemctl cat iotedge.service
     sudo systemctl daemon-reload
     sudo systemctl restart iotedge
     ```

Windows rendszeren:

* A IoT Edge Security Manager állapotának megtekintése:

   ```powershell
   Get-Service iotedge
   ```

* A IoT Edge Security Manager naplóinak megtekintése:

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
   ```

* Csak a IoT Edge Security Manager-naplók utolsó 5 percét tekintheti meg:

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog -StartTime ([datetime]::Now.AddMinutes(-5))
   ```

* Tekintse meg a IoT Edge Security Manager részletes naplóit:

  1. Rendszerszintű környezeti változó hozzáadása:

     ```powershell
     [Environment]::SetEnvironmentVariable("IOTEDGE_LOG", "debug", [EnvironmentVariableTarget]::Machine)
     ```

  2. Indítsa újra a IoT Edge biztonsági démont:

     ```powershell
     Restart-Service iotedge
     ```

:::moniker-end
<!--end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

* A IoT Edge rendszerszolgáltatások állapotának megtekintése:

   ```bash
   sudo iotedge system status
   ```

* Tekintse meg a IoT Edge rendszerszolgáltatások naplóit:

   ```bash
   sudo iotedge system logs -- -f
   ```

* A IoT Edge rendszerszolgáltatások részletes naplóinak megjelenítéséhez engedélyezze a hibakeresési szintű naplókat:

  1. Hibakeresési szintű naplók engedélyezése.

     ```bash
     sudo iotedge system set-log-level debug
     sudo iotedge system restart
     ```

  1. A hibakeresés után váltson vissza az alapértelmezett információs szintű naplókra.

     ```bash
     sudo iotedge system set-log-level info
     sudo iotedge system restart
     ```

:::moniker-end
<!-- end 1.2 -->

## <a name="check-container-logs-for-issues"></a>A problémákhoz tartozó tároló-naplók keresése

Ha a IoT Edge biztonsági démon fut, tekintse meg a tárolók naplóit a problémák észleléséhez. Indítsa el a telepített tárolókat, majd tekintse meg a IoT Edge futtatókörnyezetet alkotó tárolókat: edgeAgent és edgeHub. A IoT Edge-ügynök naplói általában az egyes tárolók életciklusára vonatkozó információkat biztosítanak. Az IoT Edge hub naplói az üzenetküldéssel és az útválasztással kapcsolatos információkat biztosítanak.

```cmd
iotedge logs <container name>
```

Az eszközön található egyik modulhoz is használhat [közvetlen metódust](how-to-retrieve-iot-edge-logs.md#upload-module-logs) , hogy feltöltse a modul naplóit az Azure Blob Storageba.

## <a name="view-the-messages-going-through-the-iot-edge-hub"></a>Az IoT Edge hub-on keresztül haladó üzenetek megtekintése

<!--1.1 -->
:::moniker range="iotedge-2018-06"

Megtekintheti az IoT Edge hub-on keresztül megjelenő üzeneteket, és bepillantást nyerhet a részletes naplókból a futásidejű tárolóból. A részletes naplók ezen tárolók bekapcsolásához állítsa be a `RuntimeLogLevel` YAML konfigurációs fájlját. A fájl megnyitása:

Linux rendszeren:

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

Windows rendszeren:

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

Alapértelmezés szerint az `agent` elem a következő példához hasonlóan fog kinézni:

   ```yaml
   agent:
     name: edgeAgent
     type: docker
     env: {}
     config:
       image: mcr.microsoft.com/azureiotedge-agent:1.1
       auth: {}
   ```

Csere `env: {}` ezzel:

   ```yaml
   env:
     RuntimeLogLevel: debug
   ```

   > [!WARNING]
   > A YAML-fájlok nem tartalmazhatnak behúzást tartalmazó lapokat. Ehelyett használjon két szóközt. A legfelső szintű elemek nem rendelkezhetnek kezdő szóközökkel.

Mentse a fájlt, és indítsa újra a IoT Edge Security Manager alkalmazást.

<!-- end 1.1 -->
:::moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

Megtekintheti az IoT Edge hub-on keresztül megjelenő üzeneteket, és bepillantást nyerhet a részletes naplókból a futásidejű tárolók között. A részletes naplók ezen tárolók bekapcsolásához állítsa a `RuntimeLogLevel` környezeti változót az üzembe helyezési jegyzékbe.

Az IoT Edge hub-on keresztül haladó üzenetek megtekintéséhez állítsa a `RuntimeLogLevel` környezeti változót a `debug` edgeHub modulra.

A edgeHub és a edgeAgent modulban is szerepel ez a futásidejű naplózási környezeti változó, amely az alapértelmezett értékre van beállítva `info` . Ez a környezeti változó a következő értékeket veheti fel:

* végzetes
* error
* figyelmeztetés
* információ
* debug
* részletes

<!-- end 1.2 -->
:::moniker-end

A IoT Hub-és IoT-eszközök között elküldött üzeneteket is megtekintheti. A [Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit)-hoz készült Azure IoT hub-bővítmény használatával tekintheti meg ezeket az üzeneteket. További információ: [praktikus eszköz az Azure IoT való fejlesztés során](https://blogs.msdn.microsoft.com/iotdev/2017/09/01/handy-tool-when-you-develop-with-azure-iot/).

## <a name="restart-containers"></a>Tárolók újraindítása

A naplók és üzenetek adatainak kivizsgálása után próbálkozzon újra a tárolók újraindításával:

```cmd
iotedge restart <container name>
```

Indítsa újra a IoT Edge futtatókörnyezeti tárolókat:

```cmd
iotedge restart edgeAgent && iotedge restart edgeHub
```

## <a name="check-your-firewall-and-port-configuration-rules"></a>A tűzfal és a port konfigurációs szabályainak megkeresése

Azure IoT Edge lehetővé teszi a helyszíni kiszolgálóról az Azure-felhőbe való kommunikációt a támogatott IoT Hub protokollok használatával: [kommunikációs protokoll kiválasztása](../iot-hub/iot-hub-devguide-protocols.md). A fokozott biztonság érdekében a Azure IoT Edge és az Azure IoT Hub közötti kommunikációs csatornák mindig Kimenőként vannak konfigurálva. Ez a konfiguráció a szolgáltatások által [támogatott kommunikációs minta](/archive/blogs/clemensv/service-assisted-communication-for-connected-devices)alapján történik, ami lekicsinyíti a támadási felületet egy rosszindulatú entitás számára a felderítéshez. A bejövő kommunikációra csak bizonyos esetekben van szükség, amikor az Azure-IoT Hub üzeneteket kell leküldenie az Azure IoT Edge eszköznek. A felhőből az eszközre irányuló üzenetek biztonságos TLS-csatornák használatával védhetők, és az X. 509 tanúsítványokkal és TPM-eszközök moduljaival tovább biztonságossá tehetők. A Azure IoT Edge Security Manager szabályozza, hogyan lehet létrehozni a kommunikációt: [IoT Edge Security Manager](../iot-edge/iot-edge-security-manager.md).

Míg a IoT Edge továbbfejlesztett konfigurációt biztosít Azure IoT Edge futtatókörnyezet és a telepített modulok biztonságossá tételéhez, továbbra is függ a mögöttes gépről és hálózati konfigurációtól. Ezért fontos, hogy a megfelelő hálózati és tűzfalszabályok a Felhőbeli kommunikáció biztonságos peremén legyenek beállítva. A következő táblázat használható a konfigurációs tűzfalszabályok azon alapuló kiszolgálókon való konfigurálásához, amelyeken a Azure IoT Edge Runtime fut:

|Protokoll|Port|Bejövő|Kimenő|Útmutató|
|--|--|--|--|--|
|MQTT|8883|Letiltva (alapértelmezett)|Letiltva (alapértelmezett)|<ul> <li>Konfigurálja a kimenő (kimenő) t, ha a MQTT kommunikációs protokollként való használatakor megnyitható.<li>a IoT Edge nem támogatja a MQTT használatát. 1883 <li>A bejövő (bejövő) kapcsolatokat le kell tiltani.</ul>|
|AMQP|5671|Letiltva (alapértelmezett)|Megnyitás (alapértelmezett)|<ul> <li>Az IoT Edge alapértelmezett kommunikációs protokollja. <li> Úgy kell konfigurálni, hogy nyitva legyen, ha Azure IoT Edge nincs más támogatott protokollhoz konfigurálva, vagy a AMQP a kívánt kommunikációs protokoll.<li>a IoT Edge nem támogatja a AMQP használatát. 5672<li>A port letiltása, ha a Azure IoT Edge egy másik IoT Hub támogatott protokollt használ.<li>A bejövő (bejövő) kapcsolatokat le kell tiltani.</ul></ul>|
|HTTPS|443|Letiltva (alapértelmezett)|Megnyitás (alapértelmezett)|<ul> <li>Konfigurálja a kimenő (kimenő) 443-es megnyitását IoT Edge kiépítés esetén. Ez a konfiguráció kézi parancsfájlok vagy Azure IoT Device kiépítési szolgáltatás (DPS) használata esetén szükséges. <li>A bejövő (bejövő) kapcsolatok csak bizonyos esetekben nyithatók meg: <ul> <li>  Ha olyan transzparens átjáróval rendelkezik, amely olyan Leaf-eszközökkel rendelkezik, amelyek metódus-kérelmeket küldhetnek. Ebben az esetben a 443-es portot nem szabad külső hálózatokhoz csatlakozni a IoTHub-hez való csatlakozáshoz vagy a IoTHub szolgáltatások nyújtásához Azure IoT Edgeon keresztül. Így a Bejövő szabály csak a bejövő (bejövő) és a belső hálózatról nyitható meg. <li> Az ügyfél – eszköz (C2D) forgatókönyvek esetében.</ul><li>a IoT Edge nem támogatja a HTTP-t (80).<li>Ha nem HTTP protokoll (például AMQP vagy MQTT) nem konfigurálható a vállalatban; az üzenetek elküldhetők websocketek használatával. Ebben az esetben a 443-es portot fogja használni a WebSocket-kommunikációhoz.</ul>|

## <a name="next-steps"></a>Következő lépések

Úgy gondolja, hogy hibát talált az IoT Edge platformon? [Küldjön el egy problémát](https://github.com/Azure/iotedge/issues) , hogy tovább javítsuk.

Ha további kérdései vannak, hozzon létre egy [support Request](https://portal.azure.com/#create/Microsoft.Support) segítséget.
