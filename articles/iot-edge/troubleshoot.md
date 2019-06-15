---
title: Hibaelhárítás – az Azure IoT Edge |} A Microsoft Docs
description: Ebben a cikkben megismerheti standard diagnosztikai képességek az Azure IoT Edge, például összetevő-állapot és a naplók beolvasása, és a leggyakoribb hibák elhárításához
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 04/26/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 00147002317f15345f01c88e81973837d16e6669
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65797612"
---
# <a name="common-issues-and-resolutions-for-azure-iot-edge"></a>Az Azure IoT Edge gyakori problémái és azok megoldásai

Ha a környezetében az Azure IoT Edge futtatásakor problémákat tapasztal, ezt a cikket útmutatóként használhatja a hibaelhárításhoz és a megoldáshoz.

## <a name="run-the-iotedge-check-command"></a>A iotedge 'ellenőrzése"parancs futtatása

Az első lépés az IoT Edge hibaelhárítása során lehet használni a `check` parancsot, amely elvégzi a konfiguráció és a kapcsolat tesztek gyűjteménye felmerülő gyakori problémákra. A `check` parancs [1.0.7 kiadási](https://github.com/Azure/azure-iotedge/releases/tag/1.0.7) és újabb verziók.

Futtathatja a `check` paranccsal a következőképpen, vagy tartalmaznak a `--help` jelzőt a beállítások teljes listáját lásd:

* Linux:

  ```bash
  sudo iotedge check
  ```

* Windows rendszeren:

  ```powershell
  iotedge check
  ```

Ellenőrzi az eszköz által futtatott típusú besorolva is:

* Konfigurációjának ellenőrzései: Megvizsgálja, amely megakadályozhatja, hogy a peremhálózati eszközök csatlakoztatása a felhőhöz, beleértve a problémák részleteit *config.yaml* és a tároló-motor.
* Kapcsolatok ellenőrzése: Ellenőrzi az IoT Edge-futtatókörnyezet férhet hozzá az állomás eszközön portok és az IoT Hub az IoT Edge-összetevők csatlakozhatnak.
* Éles készültség-ellenőrzés: Ajánlott üzemi jelenségeket, például az eszköz hitelesítésszolgáltató (CA) tanúsítvány és a modul naplófájl-konfiguráció keres.

Diagnosztikai ellenőrzéseket teljes listáját lásd: [beépített funkciók hibaelhárítási](https://github.com/Azure/iotedge/blob/master/doc/troubleshoot-checks.md).

## <a name="standard-diagnostic-steps"></a>Általános diagnosztikai lépések

Ha problémát tapasztal, további az IoT Edge-eszköz állapotáról a tárolónaplók és az üzeneteket, hogy az eszköz áttekintésével. Az ebben a szakaszban szereplő parancsokkal és eszközökkel további információt gyűjthet.

### <a name="check-the-status-of-the-iot-edge-security-manager-and-its-logs"></a>Az IoT Edge Security Manager és a naplók állapotának ellenőrzése

Linux:
- Az IoT Edge-biztonságkezelő állapotának megtekintése:

   ```bash
   sudo systemctl status iotedge
   ```

- A naplók az IoT Edge-biztonságkezelő megtekintéséhez:

    ```bash
    sudo journalctl -u iotedge -f
    ```

- Továbbiak megtekintése részletes naplók az IoT Edge-biztonságkezelő:

   - Szerkessze a iotedge démon beállításokat:

      ```bash
      sudo systemctl edit iotedge.service
      ```
   
   - Frissítse a következő sorokat:
    
      ```
      [Service]
      Environment=IOTEDGE_LOG=edgelet=debug
      ```
    
   - Az IoT Edge biztonsági démon újraindításához:
    
      ```bash
      sudo systemctl cat iotedge.service
      sudo systemctl daemon-reload
      sudo systemctl restart iotedge
      ```

Windows rendszeren:
- Az IoT Edge-biztonságkezelő állapotának megtekintése:

   ```powershell
   Get-Service iotedge
   ```

- A naplók az IoT Edge-biztonságkezelő megtekintéséhez:

   ```powershell
   . {Invoke-WebRequest -useb aka.ms/iotedge-win} | Invoke-Expression; Get-IoTEdgeLog
   ```

### <a name="if-the-iot-edge-security-manager-is-not-running-verify-your-yaml-configuration-file"></a>Ha az IoT Edge biztonsági kezelője nem fut, ellenőrizze a yaml-konfigurációs fájl

> [!WARNING]
> YAML-fájlok nem lehetnek behúzás lapokat. Használja helyette a 2 szóközöket.

Linux:

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

Windows rendszeren:

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

### <a name="check-container-logs-for-issues"></a>Ellenőrizze a tároló naplóinak problémák

Miután az IoT Edge biztonsági démon fut, tekintse meg a naplókat a tárolók a hibák észlelése. Kezdje az üzembe helyezett tárolókkal, majd tekintse meg az IoT Edge-futtatókörnyezet alkotó tárolókat: edgeAgent és edgeHub. Az IoT Edge agent-naplók általában az egyes tárolók életciklusáról nyújtanak információt. Az IoT Edge hub-naplók üzenetküldésről és az útválasztásról nyújtanak információt. 

   ```cmd
   iotedge logs <container name>
   ```

### <a name="view-the-messages-going-through-the-iot-edge-hub"></a>Az IoT Edge hubon áthaladó üzeneteket megtekintése

Megtekintheti az IoT Edge hubon áthaladó üzeneteket, és gyűjtsön információt a futtatókörnyezet tárolóiból származó részletes naplók a. Ezek a tárolók a részletes naplók bekapcsolásához állítsa `RuntimeLogLevel` a yaml-konfigurációs fájlban. A fájl megnyitásához:

Linux:

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

Windows rendszeren:

   ```cmd
   notepad C:\ProgramData\iotedge\config.yaml
   ```

Alapértelmezés szerint a `agent` elem a következő példához hasonlóan fog kinézni:

   ```yaml
   agent:
     name: edgeAgent
     type: docker
     env: {}
     config:
       image: mcr.microsoft.com/azureiotedge-agent:1.0
       auth: {}
   ```

Cserélje le `env: {}` együtt:

   ```yaml
   env:
     RuntimeLogLevel: debug
   ```

   > [!WARNING]
   > YAML-fájlok nem lehetnek identation lapokon. Használja helyette a 2 szóközöket.

Mentse a fájlt, és indítsa újra a IoT Edge-kezelő.

Az IoT Hub és az IoT Edge-eszközök között küldött üzeneteket is ellenőrizheti. Ezek az üzenetek megtekintéséhez használja a [Azure IoT Hub-eszközkészlet](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) Visual Studio Code-bővítmény (korábbi nevén Azure IoT-eszközkészlet bővítmény). További információkért lásd: [hasznos eszközt az Azure IoT fejlesztése során](https://blogs.msdn.microsoft.com/iotdev/2017/09/01/handy-tool-when-you-develop-with-azure-iot/).

### <a name="restart-containers"></a>Indítsa újra a tárolók
Miután megvizsgálta a naplók és üzenetek információit, próbálja meg újraindítani a tárolók:

```
iotedge restart <container name>
```

Indítsa újra az IoT Edge-futtatókörnyezet tárolóiból:

```
iotedge restart edgeAgent && iotedge restart edgeHub
```

### <a name="restart-the-iot-edge-security-manager"></a>Indítsa újra az IoT Edge-biztonságkezelő

Ha a probléma továbbra is átlátni, próbálja meg újraindítani az IoT Edge-biztonságkezelő.

Linux:

   ```cmd
   sudo systemctl restart iotedge
   ```

Windows rendszeren:

   ```powershell
   Stop-Service iotedge -NoWait
   sleep 5
   Start-Service iotedge
   ```

## <a name="iot-edge-agent-stops-after-about-a-minute"></a>IoT Edge agent körülbelül egy perc után leáll.

A edgeAgent modul elindul, és sikeresen fut körülbelül egy percet, majd leáll. A naplók jelzik, hogy az IoT Edge agent megpróbál kapcsolódni az IoT hubhoz amqp-n keresztül, és ezután megpróbál csatlakozni az AMQP használatával websocketen. Ha nem sikerül, az IoT Edge agent kilép. 

Példa edgeAgent naplói:

```output
2017-11-28 18:46:19 [INF] - Starting module management agent. 
2017-11-28 18:46:19 [INF] - Version - 1.0.7516610 (03c94f85d0833a861a43c669842f0817924911d5) 
2017-11-28 18:46:19 [INF] - Edge agent attempting to connect to IoT Hub via AMQP... 
2017-11-28 18:46:49 [INF] - Edge agent attempting to connect to IoT Hub via AMQP over WebSocket... 
```

### <a name="root-cause"></a>Gyökérok
A gazdagép hálózati egy hálózati konfiguráció megakadályozza, hogy az IoT Edge-ügynök éri el a hálózaton. Az ügynök először megpróbál AMQP-n keresztül csatlakozni (az 5671-es porton). Ha a kapcsolódás sikertelen, a websockettel (a 443-as porton).

Az IoT Edge-futtatókörnyezet minden modulon beállít egy-egy hálózatot a kommunikációhoz. Linux rendszeren ez a hálózat egy hídhálózat. Windows rendszeren NAT-ot használ. Ez a probléma gyakoribb a NAT-hálózatot használó Windows-tárolókat igénybe vevő windowsos eszközökön. 

### <a name="resolution"></a>Megoldás:
Győződjön meg arról, hogy elérhető egy útvonal az internethez az ehhez a híd-/NAT-hálózathoz rendelt IP-címek esetén. Néha a gazdagépen lévő VPN-konfiguráció felülbírálja az IoT Edge-hálózatot. 

## <a name="iot-edge-hub-fails-to-start"></a>IoT Edge hub nem indul el

A kezdő és a következő üzenetet a naplókba írja edgeHub modul meghiúsul: 

```output
One or more errors occurred. 
(Docker API responded with status code=InternalServerError, response=
{\"message\":\"driver failed programming external connectivity on endpoint edgeHub (6a82e5e994bab5187939049684fb64efe07606d2bb8a4cc5655b2a9bad5f8c80): 
Error starting userland proxy: Bind for 0.0.0.0:443 failed: port is already allocated\"}\n) 
```

### <a name="root-cause"></a>Gyökérok
A gazdagépen egy másik folyamat foglalja le a 443-as portot. Az IoT Edge hub az 5671, és az átjáró-forgatókönyvekhez használja a 443-as. Ez a portleképezés sikertelen, ha egy másik folyamat már lefoglalta a portot. 

### <a name="resolution"></a>Megoldás:
Keresse meg, és állítsa le a 443-as portot használó folyamatot. Ez a folyamat általában a webkiszolgáló.

## <a name="iot-edge-agent-cant-access-a-modules-image-403"></a>IoT Edge agent nem éri el egy modul rendszerképét (403)
A tároló nem fut, és a edgeAgent naplók megjelenítése a 403-as hibát. 

### <a name="root-cause"></a>Gyökérok
Az Iot Edge-ügynök nincs engedélye egy modul rendszerképének eléréséhez. 

### <a name="resolution"></a>Megoldás:
Győződjön meg arról, hogy a tárolójegyzék hitelesítő adatainak helyesen vannak megadva a manifest nasazení

## <a name="iot-edge-security-daemon-fails-with-an-invalid-hostname"></a>IoT Edge biztonsági démon meghiúsul, és a egy érvénytelen állomásnév

A parancs `sudo journalctl -u iotedge` meghiúsul, és kinyomtatja a következő üzenet: 

```output
Error parsing user input data: invalid hostname. Hostname cannot be empty or greater than 64 characters
```

### <a name="root-cause"></a>Gyökérok
Az IoT Edge-futtatókörnyezet csak támogatja, amelyek 64 karakternél rövidebb gazdanévvel. Fizikai gépek általában nem rendelkezik hosszú állomásnevek, de a probléma gyakoribb a virtuális gépen. Az automatikusan létrehozott gazdanevek Windows virtuális gépek az Azure-ban üzemeltetett, általában hosszú. 

### <a name="resolution"></a>Megoldás:
Ha ezt a hibát látja, feloldhatja konfigurálásával a virtuális gép DNS-nevét, és beállítja a DNS-nevét a setup parancs az állomásnevet.

1. Az Azure Portalon lépjen a virtuális gép áttekintés oldalán. 
2. Válassza ki **konfigurálása** DNS-neve alatt. Ha a virtuális géphez már tartozik egy DNS-név konfigurálva, nem kell egy új konfigurálása. 

   ![Konfigurálja a virtuális gép DNS-neve](./media/troubleshoot/configure-dns.png)

3. Adjon meg egy értéket a **DNS-névcímke** válassza **mentése**.
4. Másolja a következő formátumban kell lennie új DNS-név  **\<DNSnamelabel\>.\< vmlocation\>. cloudapp.Azure.com formát követi**.
5. A virtuális gépen belül a következő parancs használatával állítsa be az IoT Edge-futtatókörnyezet, a DNS-névvel:

   - Linux:

      ```bash
      sudo nano /etc/iotedge/config.yaml
      ```

   - Windows rendszeren:

      ```cmd
      notepad C:\ProgramData\iotedge\config.yaml
      ```

## <a name="stability-issues-on-resource-constrained-devices"></a>Erőforrás stabilitási problémák korlátozott eszközök 
Felmerülhet korlátozott eszközökön, például a Raspberry Pi-októl stabilitását, különösen akkor, ha az átjáróként használt. Memória kivételeket az edge hub modul kívül tünetei, alsóbb rétegbeli eszközök nem csatlakoznak, vagy az eszköz nem a telemetriai üzeneteket küld a néhány óra múlva.

### <a name="root-cause"></a>Gyökérok
Az IoT Edge hub, az IoT Edge-futtatókörnyezet része, amely alapértelmezés szerint a teljesítmény optimalizáltuk, és megpróbálja nagy mennyiségű memóriát lefoglalni. Az optimalizálás nem ideális korlátozott peremhálózati eszközökre, és stabilitását problémákat okozhat.

### <a name="resolution"></a>Megoldás:
Az IoT Edge hubot a környezeti változó értéke **OptimizeForPerformance** való **hamis**. Ehhez két módja van:

A felhasználói felületen: 

A portálon lépjen a **eszközadatok** > **modulok beállítása** > **speciális Edge-futtatókörnyezet-beállítások konfigurálása**. Hozzon létre egy környezeti változót az Edge Hub modulhoz nevű *OptimizeForPerformance* , amely *hamis*.

![OptimizeForPerformance "false" értékűre.](./media/troubleshoot/optimizeforperformance-false.png)

**VAGY**

A manifest nasazení:

```json
  "edgeHub": {
    "type": "docker",
    "settings": {
      "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
      "createOptions": <snipped>
    },
    "env": {
      "OptimizeForPerformance": {
          "value": "false"
      }
    },
```
## <a name="cant-get-the-iot-edge-daemon-logs-on-windows"></a>Nem olvasható be az IoT Edge démon naplóit a Windows
Ha egy EventLogException használatakor `Get-WinEvent` Windows, ellenőrizze a beállításjegyzékbeli bejegyzéseket.

### <a name="root-cause"></a>Gyökérok
A `Get-WinEvent` PowerShell-paranccsal támaszkodik egy beállításjegyzékbeli bejegyzést jelen naplók keresése egy adott `ProviderName`.

### <a name="resolution"></a>Megoldás:
Állítsa be az IoT Edge-démon egy beállításjegyzék-bejegyzést. Hozzon létre egy **iotedge.reg** fájlt az alábbi tartalommal, és a Windows beállításjegyzék importálása a dupla kattintással vagy használatával a `reg import iotedge.reg` parancsot:

```
Windows Registry Editor Version 5.00

[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\EventLog\Application\iotedged]
"CustomSource"=dword:00000001
"EventMessageFile"="C:\\ProgramData\\iotedge\\iotedged.exe"
"TypesSupported"=dword:00000007
```

## <a name="iot-edge-module-fails-to-send-a-message-to-the-edgehub-with-404-error"></a>IoT Edge-modul egy üzenet küldéséhez az edgeHub 404-es hibaüzenettel meghiúsul

IoT Edge-modul nem tud üzenetet küldeni a edgeHub a 404-es egyéni `Module not found` hiba. Az IoT Edge-démon a naplókat az alábbi üzenetet jelenít meg: 

```output
Error: Time:Thu Jun  4 19:44:58 2018 File:/usr/sdk/src/c/provisioning_client/adapters/hsm_client_http_edge.c Func:on_edge_hsm_http_recv Line:364 executing HTTP request fails, status=404, response_buffer={"message":"Module not found"}u, 04 ) 
```

### <a name="root-cause"></a>Gyökérok
Az IoT Edge-démon érvényesíti a folyamat azonosítóját összes modult a biztonsági okokból edgeHub csatlakozik. Ellenőrzi, hogy egy modul által küldött összes üzenet származik-e a fő a modul Folyamatazonosítója. Egy üzenet egy másik folyamat azonosítója, mint az eredetileg létrehozott a modul által küldött, ha azt az üzenetet a 404-es hibaüzenettel elutasítja.

### <a name="resolution"></a>Megoldás:
Győződjön meg arról, hogy az azonos Folyamatazonosító mindig használják az egyéni IoT Edge-modul üzeneteket küldeni a edgeHub. Például, ügyeljen arra, hogy `ENTRYPOINT` helyett `CMD` parancsot a Docker-fájlban, mivel `CMD` eredményezi egy folyamat. a modul és a bash-parancs a fő program fut, mivel egy másik folyamat azonosító `ENTRYPOINT` lesz egy egyetlen folyamat azonosítója.


## <a name="firewall-and-port-configuration-rules-for-iot-edge-deployment"></a>Üzemelő IoT Edge-példány konfigurációs szabályokat tűzfal- és portbeállítások
Az Azure IoT Edge lehetővé teszi, hogy az Azure-felhőben az IoT Hub támogatott protokollok használatával és egy helyszíni kiszolgáló közötti kommunikáció, lásd: [kommunikációs protokoll kiválasztása](../iot-hub/iot-hub-devguide-protocols.md). A fokozott biztonság érdekében a kommunikációs csatornák között az Azure IoT Edge és az Azure IoT Hub mindig konfigurálni kell a kimenő. Ez a konfiguráció alapján a [szolgáltatások támogatott kommunikációs mintát](https://blogs.msdn.microsoft.com/clemensv/2014/02/09/service-assisted-communication-for-connected-devices/), amely minimálisra csökkenti a támadási felületet a rosszindulatú entitás megismerése. Bejövő kommunikáció csak akkor szükséges, ahol az Azure IoT Hub kell üzenetek leküldése az Azure IoT Edge-eszköz bizonyos forgatókönyvek esetén. Felhőből az eszközre irányuló üzenetek biztonságos TLS-csatorna használatával véd, és további segítségével biztosítható X.509-tanúsítványokat és a TPM-eszköz modulok. Az Azure IoT Edge biztonsági Manager szabályozza, hogy ez a kommunikáció hogyan lehet létrehozni, tekintse meg [IoT Edge-biztonságkezelő](../iot-edge/iot-edge-security-manager.md).

IoT Edge biztosít továbbfejlesztett konfigurálása az Azure IoT Edge-futtatókörnyezet, és üzembe helyezett modulokat, az továbbra is függ az alapul szolgáló machine és a hálózati konfiguráció. Ezért rendkívül fontos biztosítani kell a megfelelő hálózati és tűzfalszabályok vannak beállítva a kommunikációs felhőbe biztonságos peremhálózati. Az alábbi táblázat használhatja útmutatóként tűzfalszabályok konfigurálása az alapul szolgáló kiszolgálók az Azure IoT Edge-futtatókörnyezet üzemeltető:

|Protocol|Port|bejövő|Kimenő|Útmutatás|
|--|--|--|--|--|
|MQTT|8883|TILTOTT (alapértelmezett)|TILTOTT (alapértelmezett)|<ul> <li>Konfigurálja a kimenő (kimenő) kell nyílt, amikor az MQTT protokoll használatával.<li>az MQTT 1883 IoT Edge által nem támogatott. <li>Bejövő (bejövő) kapcsolatok le kell tiltani.</ul>|
|AMQP|5671|TILTOTT (alapértelmezett)|NYÍLT (alapértelmezett)|<ul> <li>Alapértelmezett kommunikációs protokollt az IoT Edge-hez. <li> Nyissa meg kell, ha más támogatott protokollok nincs konfigurálva az Azure IoT Edge vagy az AMQP protokoll kívánt kell konfigurálni.<li>az AMQP 5672 IoT Edge által nem támogatott.<li>Tiltsa le ezt a portot, ha az Azure IoT Edge használja egy másik IoT Hub protokoll támogatott.<li>Bejövő (bejövő) kapcsolatok le kell tiltani.</ul></ul>|
|HTTPS|443|TILTOTT (alapértelmezett)|NYÍLT (alapértelmezett)|<ul> <li>Konfigurálja a kimenő (kimenő) kell nyissa meg a 443-as kiépítése az IoT Edge. Ez a konfiguráció manuális parancsprogramokkal vagy az Azure IoT Device Provisioning Service (DPS) szükség. <li>Bejövő (bejövő) kapcsolatot kell lennie a nyílt csak az adott forgatókönyveket: <ul> <li>  Ha a levéleszközök is küldhet kéréseket metódus, amely transzparens átjáró. Ebben az esetben 443-as portot nem kell megnyitni a külső hálózatokhoz IoTHub csatlakozni, vagy adja meg az IoTHub-szolgáltatások az Azure IoT Edge segítségével. Így a bejövő szabály csak megnyitásához a belső hálózatról bejövő (bejövő) korlátozott lehet. <li> -Ügyfél (C2D) eszközök esetén.</ul><li>IoT Edge által nem támogatott a 80-as HTTP-hez.<li>Ha nem HTTP-protokoll (például AMQP és MQTT) nem konfigurálható a vállalat; az üzenetek küldhetők a websockets protokoll. 443-as portot ebben az esetben WebSocket-kommunikációhoz fogja használni.</ul>|

## <a name="edge-agent-module-continually-reports-empty-config-file-and-no-modules-start-on-the-device"></a>Edge-ügynök modul folyamatosan "üres konfigurációs fájl" jelentéseket, és nem modulok indítsa el az eszközön

Hiba történt a központi telepítésben definiált modulok elindítása van az eszközön. Csak a edgeAgent fut, de folyamatosan reporting "üres konfigurációs fájl...".

### <a name="potential-root-cause"></a>Lehetséges okát
Alapértelmezés szerint az IoT Edge modulok saját elkülönített tároló hálózati indítja el. Az eszköz problémái lehetnek DNS-névfeloldás a magánhálózaton belül.

### <a name="resolution"></a>Megoldás:

**1. lehetőség: DNS-kiszolgáló a tárolóban állítsa be a motor beállításai**

Adja meg a DNS-kiszolgáló, a környezetnek a tároló motor beállítására a motor által indított összes tároló-modulokkal. Hozzon létre egy fájlt `daemon.json` adja meg a DNS-kiszolgáló használatára. Példa:

```
{
    "dns": ["1.1.1.1"]
}
```

A fenti példában a DNS-kiszolgáló egy nyilvánosan elérhető-e DNS-szolgáltatás állítja be. Ha az edge-eszköz nem érhető el az IP-a környezetből, cserélje le az elérhető DNS-kiszolgáló címét.

Hely `daemon.json` a platformnak megfelelő helyen: 

| Platform | Location egység |
| --------- | -------- |
| Linux | `/etc/docker` |
| Windows-gazdagépen a Windows-tárolókkal | `C:\ProgramData\iotedge-moby\config` |

Ha már tartalmazza a hely `daemon.json` fájlt, adja hozzá a **dns** billentyűt, és mentse a fájlt.

*Indítsa újra a frissítések érvénybe lépéséhez a tároló motor*

| Platform | Parancs |
| --------- | -------- |
| Linux | `sudo systemctl restart docker` |
| Windows (Admin Powershell) | `Restart-Service iotedge-moby -Force` |

**2. lehetőség: Állítsa be a DNS-kiszolgáló az IoT Edge-példányban modulonként**

Beállíthatja a DNS-kiszolgáló az egyes modulok *createOptions* az IoT Edge-telepítésben. Példa:

```
"createOptions": {
  "HostConfig": {
    "Dns": [
      "x.x.x.x"
    ]
  }
}
```

Győződjön meg arról, hogy ezt a a *edgeAgent* és *edgeHub* modulokat is. 

## <a name="next-steps"></a>További lépések
Úgy gondolja, hogy hibát talált az IoT Edge platformon? [Küldje el a problémát](https://github.com/Azure/iotedge/issues) , hogy továbbra is javíthatja. 

Ha további kérdése van, hozzon létre egy [támogatási kérelem](https://portal.azure.com/#create/Microsoft.Support) segítséget. 

