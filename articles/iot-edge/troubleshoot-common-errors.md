---
title: Gyakori hibák – Azure IoT Edge | Microsoft Docs
description: Ez a cikk a IoT Edge-megoldások telepítésekor észlelt gyakori problémák megoldására használható.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/01/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: cc6d7491d9c38f1ddf4aba2adecad4aaee3c344b
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2021
ms.locfileid: "103489562"
---
# <a name="common-issues-and-resolutions-for-azure-iot-edge"></a>Az Azure IoT Edge gyakori problémái és azok megoldásai

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Ebből a cikkből megtudhatja, milyen lépéseket lehet a IoT Edge megoldások telepítésekor felmerülő gyakori problémák megoldásához. Ha meg szeretné tudni, hogyan találhatja meg a naplókat és a hibákat a IoT Edge eszközéről, tekintse meg [a IoT Edge eszköz hibaelhárítása](troubleshoot.md)című témakört.

## <a name="iot-edge-agent-stops-after-about-a-minute"></a>IoT Edge ügynök egy perc múlva leáll

**Megfigyelt viselkedés:**

A edgeAgent modul egy percen keresztül elindul, és sikeresen fut, majd leáll. A naplók azt jelzik, hogy a IoT Edge ügynök megpróbál csatlakozni a AMQP-on keresztüli IoT Hub, majd a AMQP-en keresztül próbál csatlakozni a WebSocket használatával. Ha ez nem sikerül, a IoT Edge ügynök kilép.

Példa edgeAgent-naplókra:

```output
2017-11-28 18:46:19 [INF] - Starting module management agent.
2017-11-28 18:46:19 [INF] - Version - 1.0.7516610 (03c94f85d0833a861a43c669842f0817924911d5)
2017-11-28 18:46:19 [INF] - Edge agent attempting to connect to IoT Hub via AMQP...
2017-11-28 18:46:49 [INF] - Edge agent attempting to connect to IoT Hub via AMQP over WebSocket...
```

**Alapvető ok:**

A gazdagép hálózatán a hálózati konfiguráció megakadályozza, hogy a IoT Edge-ügynök elérje a hálózatot. Az ügynök először megpróbál AMQP-n keresztül csatlakozni (az 5671-es porton). Ha a kapcsolat meghiúsul, a WebSockets (443-es port) próbálkozik.

Az IoT Edge-futtatókörnyezet minden modulon beállít egy-egy hálózatot a kommunikációhoz. Linux rendszeren ez a hálózat egy hídhálózat. Windows rendszeren NAT-ot használ. Ez a probléma gyakoribb a NAT-hálózatot használó Windows-tárolókat igénybe vevő windowsos eszközökön.

**Megoldás:**

Győződjön meg arról, hogy elérhető egy útvonal az internethez az ehhez a híd-/NAT-hálózathoz rendelt IP-címek esetén. Néha a gazdagépen lévő VPN-konfiguráció felülbírálja az IoT Edge-hálózatot.

## <a name="iot-edge-agent-cant-access-a-modules-image-403"></a>IoT Edge ügynök nem fér hozzá a modul rendszerképéhez (403)

**Megfigyelt viselkedés:**

Egy tároló nem fut, és a edgeAgent-naplók 403 hibát jeleznek.

**Alapvető ok:**

A IoT Edge ügynöknek nincs engedélye a modul rendszerképének elérésére.

**Megoldás:**

Győződjön meg arról, hogy a beállításjegyzékbeli hitelesítő adatok helyesen vannak megadva a telepítési jegyzékben.

## <a name="edge-agent-module-reports-empty-config-file-and-no-modules-start-on-the-device"></a>Az Edge Agent modul "üres konfigurációs fájl" jelentést készít, és egyetlen modul sem indul el az eszközön

**Megfigyelt viselkedés:**

Az eszköz nem rendelkezik a telepítésben definiált modulok indításával. Csak a edgeAgent fut, de folyamatosan jelentést készít az "üres konfigurációs fájlról...".

**Alapvető ok:**

Alapértelmezés szerint a IoT Edge elindítják a modulokat a saját elkülönített tároló hálózatában. Előfordulhat, hogy az eszköz problémát észlelt a DNS-névfeloldás ezen a magánhálózaton belül.

**Megoldás:**

**1. lehetőség: DNS-kiszolgáló beállítása a tároló motorjának beállításaiban**

Adja meg a környezethez tartozó DNS-kiszolgálót a tároló motorjának beállításaiban, amelyek a motor által indított összes tároló modulra érvényesek lesznek. Hozzon létre egy nevű fájlt `daemon.json` a használni kívánt DNS-kiszolgáló megadásával. Például:

```json
{
    "dns": ["1.1.1.1"]
}
```

A fenti példa egy nyilvánosan elérhető DNS-szolgáltatásra állítja be a DNS-kiszolgálót. Ha a peremhálózati eszköz nem fér hozzá ehhez az IP-címhez a környezetében, cserélje le az elérhető DNS-kiszolgáló címére.

Helyezze a `daemon.json` megfelelő helyre a platformhoz:

| Platform | Hely |
| --------- | -------- |
| Linux | `/etc/docker` |
| Windows-gazdagép Windows-tárolókkal | `C:\ProgramData\iotedge-moby\config` |

Ha a hely már tartalmaz `daemon.json` fájlt, adja hozzá a **DNS-** kulcsot, és mentse a fájlt.

A frissítések érvénybe léptetéséhez indítsa újra a tároló motorját.

| Platform | Parancs |
| --------- | -------- |
| Linux | `sudo systemctl restart docker` |
| Windows (rendszergazdai PowerShell) | `Restart-Service iotedge-moby -Force` |

**2. lehetőség: a DNS-kiszolgáló beállítása IoT Edge üzembe helyezés modulban**

A DNS-kiszolgáló minden modul *createOptions* beállítható a IoT Edge üzemelő példányban. Például:

```json
"createOptions": {
  "HostConfig": {
    "Dns": [
      "x.x.x.x"
    ]
  }
}
```

Ügyeljen arra, hogy ezt a konfigurációt a *edgeAgent* és a *edgeHub* modulhoz is állítsa be.

## <a name="iot-edge-hub-fails-to-start"></a>IoT Edge hub nem indul el

**Megfigyelt viselkedés:**

Nem sikerült elindítani a edgeHub modult. A naplókban a következő hibákhoz hasonló üzenet jelenhet meg:

```output
One or more errors occurred.
(Docker API responded with status code=InternalServerError, response=
{\"message\":\"driver failed programming external connectivity on endpoint edgeHub (6a82e5e994bab5187939049684fb64efe07606d2bb8a4cc5655b2a9bad5f8c80):
Error starting userland proxy: Bind for 0.0.0.0:443 failed: port is already allocated\"}\n)
```

Vagy

```output
info: edgelet_docker::runtime -- Starting module edgeHub...
warn: edgelet_utils::logging -- Could not start module edgeHub
warn: edgelet_utils::logging --     caused by: failed to create endpoint edgeHub on network nat: hnsCall failed in Win32:  
        The process cannot access the file because it is being used by another process. (0x20)
```

**Alapvető ok:**

A gazdagép egy másik folyamata egy olyan portot kötött, amelyet a edgeHub modul megpróbál kötni. Az IoT Edge hub a 443, 5671 és 8883 portot használja az átjárói forgatókönyvekben való használatra. A modul nem indul el, ha egy másik folyamat már kötött egy ilyen portot.

**Megoldás:**

A probléma kétféleképpen oldható fel:

Ha a IoT Edge eszköz átjáróként működik, akkor meg kell keresnie és le kell állítania a 443, 5671 vagy 8883 portot használó folyamatot. A 443-es port hibája általában azt jelenti, hogy a másik folyamat egy webkiszolgáló.

Ha nem szükséges a IoT Edge eszköz átjáróként való használata, akkor eltávolíthatja a port kötéseit a edgeHub modul létrehozási beállításaiból. A létrehozási beállításokat a Azure Portalban, vagy közvetlenül a fájlban lévő deployment.jslehet módosítani.

Az Azure Portalon:

1. Navigáljon az IoT hubhoz, és válassza a **IoT Edge** lehetőséget.

2. Válassza ki a frissíteni kívánt IoT Edge eszközt.

3. Válassza a **modulok beállítása** lehetőséget.

4. Válassza a **futtatókörnyezet beállításait**.

5. Az **peremhálózati** modul beállításainál törölje a mindent a **létrehozási beállítások** szövegmezőből.

6. Mentse a módosításokat, és hozza létre a központi telepítést.

A fájl deployment.js:

1. Nyissa meg a deployment.jst a IoT Edge eszközön alkalmazott fájlon.

2. Keresse meg a `edgeHub` beállításokat a edgeAgent kívánt tulajdonságai szakaszban:

   ```json
   "edgeHub": {
       "settings": {
           "image": "mcr.microsoft.com/azureiotedge-hub:1.1",
           "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
       },
       "type": "docker",
       "status": "running",
       "restartPolicy": "always"
   }
   ```

3. Távolítsa el a `createOptions` sort, és a sor végén található záró vesszőt a következő `image` előtt:

   ```json
   "edgeHub": {
       "settings": {
           "image": "mcr.microsoft.com/azureiotedge-hub:1.1"
       },
       "type": "docker",
       "status": "running",
       "restartPolicy": "always"
   }
   ```

4. Mentse a fájlt, és alkalmazza újra a IoT Edge eszközre.

## <a name="iot-edge-security-daemon-fails-with-an-invalid-hostname"></a>IoT Edge biztonsági démon érvénytelen állomásnévvel meghiúsul

**Megfigyelt viselkedés:**

Sikertelen volt [a IoT Edge Security Manager-naplók ellenőrzésének](troubleshoot.md#check-the-status-of-the-iot-edge-security-manager-and-its-logs) megkísérlése, és a következő üzenetet nyomtatja ki:

```output
Error parsing user input data: invalid hostname. Hostname cannot be empty or greater than 64 characters
```

**Alapvető ok:**

A IoT Edge futtatókörnyezet csak 64 karakternél rövidebb gazdagépeket támogat. A fizikai gépek általában nem rendelkeznek hosszú állomásnévvel, de a probléma gyakoribb a virtuális gépen. Az Azure-ban üzemeltetett Windows rendszerű virtuális gépek automatikusan generált állomásneve általában hosszúak.

**Megoldás:**

Ha ezt a hibát látja, akkor a virtuális gép DNS-nevének konfigurálásával, majd a DNS-név beállítása állomásnévként a telepítési parancsban is megoldható.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

1. A Azure Portal navigáljon a virtuális gép áttekintés lapjára.
2. Válassza a **Konfigurálás** a DNS-név alatt lehetőséget. Ha a virtuális gépnek már van konfigurált DNS-neve, nem kell újat konfigurálnia.

   ![A virtuális gép DNS-nevének konfigurálása](./media/troubleshoot/configure-dns.png)

3. Adjon meg egy értéket a **DNS-név címkéhez** , majd válassza a **Mentés** lehetőséget.
4. Másolja az új DNS-nevet, amelynek formátuma legyen **\<DNSnamelabel\> . \<vmlocation\> cloudapp.azure.com**.
5. A virtuális gépen belül a következő paranccsal állíthatja be a IoT Edge futtatókörnyezetet a DNS-névvel:

   * Linux rendszeren:

      ```bash
      sudo nano /etc/iotedge/config.yaml
      ```

   * Windows rendszeren:

      ```cmd
      notepad C:\ProgramData\iotedge\config.yaml
      ```

:::moniker-end
<!-- end 1.1 -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

1. A Azure Portal navigáljon a virtuális gép áttekintés lapjára.

2. Válassza a **Konfigurálás** a DNS-név alatt lehetőséget. Ha a virtuális gépnek már van konfigurált DNS-neve, nem kell újat konfigurálnia.

   ![A virtuális gép DNS-nevének konfigurálása](./media/troubleshoot/configure-dns.png)

3. Adjon meg egy értéket a **DNS-név címkéhez** , majd válassza a **Mentés** lehetőséget.

4. Másolja az új DNS-nevet, amelynek formátuma legyen **\<DNSnamelabel\> . \<vmlocation\> cloudapp.azure.com**.

5. A IoT Edge eszközön nyissa meg a konfigurációs fájlt.

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

6. Cserélje le a értéket a `hostname` DNS-nevére.

7. Mentse és zárjuk be a fájlt, majd alkalmazza a módosításokat IoT Edgera.

   ```bash
   sudo iotedge config apply
   ```

:::moniker-end
<!-- end 1.2 -->

## <a name="cant-get-the-iot-edge-daemon-logs-on-windows"></a>Nem lehet beolvasni a IoT Edge Daemon-naplókat a Windows rendszerben

**Megfigyelt viselkedés:**

A Windows rendszeren való használatakor EventLogException kap `Get-WinEvent` .

**Alapvető ok:**

A `Get-WinEvent` PowerShell-parancs egy beállításjegyzékbeli bejegyzésre támaszkodik, amely a naplók egy adott alapján való megtalálásához szükséges `ProviderName` .

**Megoldás:**

Adja meg a IoT Edge démon beállításjegyzékbeli bejegyzését. Hozzon létre egy **iotedge. reg** fájlt a következő tartalommal, és importálja a Windows beállításjegyzékbe úgy, hogy duplán rákattint rá, vagy használja a `reg import iotedge.reg` parancsot:

```reg
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\EventLog\Application\iotedged]
"CustomSource"=dword:00000001
"EventMessageFile"="C:\\ProgramData\\iotedge\\iotedged.exe"
"TypesSupported"=dword:00000007
```

## <a name="stability-issues-on-smaller-devices"></a>Stabilitási problémák a kisebb eszközökön

**Megfigyelt viselkedés:**

Az erőforrás által korlátozott eszközök, például a málna PI stabilitási problémáit tapasztalhatja, különösen ha átjáróként használják. A tünetek többek között a IoT Edge hub-modulban található, nem a kapcsolódást nem okozó, illetve az eszköz néhány óra elteltével nem küld telemetria üzeneteket.

**Alapvető ok:**

A IoT Edge futtatókörnyezet részét képező IoT Edge hub alapértelmezés szerint a teljesítményre van optimalizálva, és nagy mennyiségű memóriát próbál lefoglalni. Ez az optimalizálás nem ideális a korlátozott peremhálózat-eszközökhöz, és stabilitási problémákat okozhat.

**Megoldás:**

Az IoT Edge hub esetében állítsa be a **OptimizeForPerformance** környezeti változót **hamis** értékre. A környezeti változók két módon állíthatók be:

Az Azure Portalon:

A IoT hub válassza ki a IoT Edge eszközt, majd az eszköz adatai lapon válassza a **modulok**  >  **futtatókörnyezet-beállítások** megadása lehetőséget. Hozzon létre egy környezeti változót a *OptimizeForPerformance* nevű IoT Edge hub-modulhoz, amely *hamis* értékre van beállítva.

![A OptimizeForPerformance hamis értékre van állítva](./media/troubleshoot/optimizeforperformance-false.png)

Az üzembe helyezési jegyzékben:

```json
"edgeHub": {
  "type": "docker",
  "settings": {
    "image": "mcr.microsoft.com/azureiotedge-hub:1.1",
    "createOptions": <snipped>
  },
  "env": {
    "OptimizeForPerformance": {
      "value": "false"
    }
  },
```

## <a name="iot-edge-module-fails-to-send-a-message-to-edgehub-with-404-error"></a>IoT Edge modul nem tud üzenetet küldeni a edgeHub 404-as hibával

**Megfigyelt viselkedés:**

Egy egyéni IoT Edge modul nem tud üzenetet küldeni az IoT Edge hubhoz 404-as `Module not found` hibával. A IoT Edge démon a következő üzenetet nyomtatja ki a naplókba:

```output
Error: Time:Thu Jun  4 19:44:58 2018 File:/usr/sdk/src/c/provisioning_client/adapters/hsm_client_http_edge.c Func:on_edge_hsm_http_recv Line:364 executing HTTP request fails, status=404, response_buffer={"message":"Module not found"}u, 04 )
```

**Alapvető ok:**

A IoT Edge démon a edgeHub csatlakozó összes modulhoz biztonsági okokból kényszeríti a folyamat azonosítását. Ellenőrzi, hogy a modul által küldött összes üzenet a modul fő folyamatának azonosítójával származik-e. Ha egy modul egy másik, az eredetileg létrejött folyamat-AZONOSÍTÓtól érkező üzenetet küld, a rendszer 404-es hibaüzenettel elutasítja az üzenetet.

**Megoldás:**

A verzió 1.0.7 kezdve az összes modul-folyamat jogosult a kapcsolódásra. További információ: a [1.0.7 kiadásának changelog](https://github.com/Azure/iotedge/blob/master/CHANGELOG.md#iotedged-1).

Ha a 1.0.7-re való frissítés nem lehetséges, végezze el a következő lépéseket. Győződjön meg arról, hogy az egyéni IoT Edge modul ugyanazt a folyamat-azonosítót használja az üzenetek edgeHub való küldéséhez. Ügyeljen például arra, hogy a `ENTRYPOINT` `CMD` Docker-fájlban lévő parancs helyett a parancsot adja meg. A `CMD` parancs egy folyamat azonosítóját vezeti a modulhoz, valamint egy másik folyamat-azonosítót a fő programot futtató bash parancshoz, de `ENTRYPOINT` egyetlen folyamat-azonosítóhoz vezet.

## <a name="iot-edge-module-deploys-successfully-then-disappears-from-device"></a>IoT Edge modul üzembe helyezése sikeresen megtörtént, majd eltűnik az eszközről

**Megfigyelt viselkedés:**

Miután beállította a modulokat egy IoT Edge eszközhöz, a modulok üzembe helyezése sikeresen megtörtént, de néhány perc elteltével eltűnik az eszközről és az eszköz adatairól a Azure Portal. A definiált egyéb modulok is megjelenhetnek az eszközön.

**Alapvető ok:**

Ha az automatikus központi telepítés egy eszközt céloz meg, akkor az egy adott eszközhöz tartozó modulok manuális beállításával elsőbbséget élvez. A **modulok beállítása** a Azure Portal, vagy a Visual Studio Code-ban egy **eszközre történő központi telepítés létrehozása** egy pillanatra lép érvénybe. A megadott modulok megjelennek az eszközön. Ezután az automatikus központi telepítés prioritása beindul, és felülírja az eszköz kívánt tulajdonságait.

**Megoldás:**

Eszközökön csak egyféle telepítési mechanizmust használhat, automatikus központi telepítéssel vagy egyedi eszközök telepítésével. Ha több automatikus központi telepítéssel is rendelkezik egy eszközhöz, megváltoztathatja a prioritási vagy a cél leírását, hogy a megfelelő egy adott eszközre vonatkozzon. Az eszköz két példányát is frissítheti, ha már nem egyezik az automatikus központi telepítés céljának leírásával.

További információ: [IoT Edge automatikus központi telepítésének ismertetése egyetlen eszközön vagy nagy méretekben](module-deployment-monitoring.md).

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

## <a name="iot-edge-behind-a-gateway-cannot-perform-http-requests-and-start-edgeagent-module"></a>Az átjáró mögötti IoT Edge nem végezhet HTTP-kéréseket, és nem indítja el a edgeAgent modult

**Megfigyelt viselkedés:**

A IoT Edge démon érvényes konfigurációs fájllal aktív, de nem tudja elindítani a edgeAgent modult. A parancs `iotedge list` üres listát ad vissza. A IoT Edge démon naplózza a jelentést `Could not perform HTTP request` .

**Alapvető ok:**

Az átjáró mögött található eszközök IoT Edge a `parent_hostname` konfigurációs fájl mezőjében megadott szülő IoT Edge eszközről kapják meg a modul lemezképeit. A `Could not perform HTTP request` hiba azt jelenti, hogy az alárendelt eszköz nem tudja elérni a szülő eszközét http-n keresztül.

**Megoldás:**

Győződjön meg arról, hogy a szülő IoT Edge eszköz fogadhat bejövő kéréseket a gyermek IoT Edge eszközről. Nyissa meg a hálózati forgalmat a 443-es és a 6617-es porton a gyermek eszközről érkező kérelmek esetében.

:::moniker-end

## <a name="next-steps"></a>Következő lépések

Úgy gondolja, hogy hibát talált az IoT Edge platformon? [Küldjön el egy problémát](https://github.com/Azure/iotedge/issues) , hogy tovább javítsuk.

Ha további kérdései vannak, hozzon létre egy [support Request](https://portal.azure.com/#create/Microsoft.Support) segítséget.
