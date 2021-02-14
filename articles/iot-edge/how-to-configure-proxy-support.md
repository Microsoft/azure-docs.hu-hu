---
title: Eszközök konfigurálása hálózati proxyk számára – Azure IoT Edge | Microsoft Docs
description: A Azure IoT Edge futtatókörnyezet és az internetre irányuló IoT Edge modulok konfigurálása a proxykiszolgálón keresztüli kommunikációhoz.
author: kgremban
ms.author: kgremban
ms.date: 09/03/2020
ms.topic: how-to
ms.service: iot-edge
services: iot-edge
ms.custom:
- amqp
- contperf-fy21q1
ms.openlocfilehash: 7fc57b46055281c64b39767047f6b7cb5b748ad2
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100373827"
---
# <a name="configure-an-iot-edge-device-to-communicate-through-a-proxy-server"></a>IoT Edge-eszköz konfigurálása proxykiszolgálón keresztüli kommunikációra

IoT Edge eszközök HTTPS-kérelmeket küldenek a IoT Hubsal való kommunikációhoz. Ha az eszköz proxykiszolgálót használó hálózathoz csatlakozik, az IoT Edge futtatókörnyezetet úgy kell konfigurálnia, hogy a kiszolgálón keresztül kommunikáljon. A proxykiszolgálók az egyes IoT Edge modulokat is befolyásolhatják, ha olyan HTTP-vagy HTTPS-kérelmeket tesznek elérhetővé, amelyek nem az IoT Edge hub-on keresztül vannak átirányítva

Ez a cikk bemutatja a következő négy lépést a proxykiszolgáló mögötti IoT Edge eszköz konfigurálásához és kezeléséhez:

1. [**A IoT Edge futtatókörnyezet telepítése az eszközön**](#install-the-runtime-through-a-proxy)

   A IoT Edge telepítési parancsfájlok lekérik a csomagokat és fájlokat az internetről, így az eszköznek a proxykiszolgálón keresztül kell kommunikálnia a kérések elvégzéséhez. Windows-eszközök esetén a telepítési parancsfájl offline telepítési lehetőséget is biztosít.

   Ez a lépés egy egyszeri folyamat a IoT Edge eszköz konfigurálásához az első beállításakor. A IoT Edge futtatókörnyezet frissítésekor ugyanezek a kapcsolatok is szükségesek.

2. [**A Docker-démon és a IoT Edge démon konfigurálása az eszközön**](#configure-the-daemons)

   IoT Edge két démont használ az eszközön, és mindkettőnek a proxykiszolgálón keresztül kell webes kéréseket tennie. A IoT Edge démon felelős a IoT Hubekkel folytatott kommunikációért. A Moby Daemon feladata a tárolók kezelése, így kommunikál a tároló-beállításjegyzékkel.

   Ez a lépés egy egyszeri folyamat a IoT Edge eszköz konfigurálásához az első beállításakor.

3. [**A IoT Edge ügynök tulajdonságainak konfigurálása a config. YAML fájlban az eszközön**](#configure-the-iot-edge-agent)

   A IoT Edge démon először elindítja a edgeAgent modult. Ezután a edgeAgent modul lekéri az üzembe helyezési jegyzéket a IoT Hubból, és elindítja az összes többi modult. Ahhoz, hogy a IoT Edge-ügynök a kezdeti kapcsolattal IoT Hub, konfigurálja a edgeAgent modul környezeti változóit manuálisan az eszközön. A kezdeti kapcsolat után távolról is konfigurálhatja a edgeAgent modult.

   Ez a lépés egy egyszeri folyamat a IoT Edge eszköz konfigurálásához az első beállításakor.

4. [**A modul összes jövőbeli üzembe helyezése esetén állítsa be a környezeti változókat a proxyn keresztül kommunikáló modulok esetében.**](#configure-deployment-manifests)

   Miután beállította IoT Edge eszközét, és csatlakoztatva van IoT Hub a proxykiszolgálón keresztül, meg kell őriznie a kapcsolatot az összes jövőbeli modul-telepítésben.

   Ez a lépés egy folyamatban lévő folyamat, amely lehetővé teszi, hogy minden új modul vagy központi telepítés frissítése fenntartsa az eszköznek a proxykiszolgálón keresztüli kommunikációt.

## <a name="know-your-proxy-url"></a>A proxy URL-címének megismerése

Mielőtt elkezdené a cikkben ismertetett lépéseket, ismernie kell a proxy URL-címét.

A proxy URL-címei a következő formátumot végzik el: **protokoll**://**proxy_host**:**proxy_port**.

* A **protokoll** http vagy HTTPS. A Docker-démon bármely protokollt használhat a tároló beállításjegyzék-beállításaitól függően, de a IoT Edge démon és a futásidejű tárolók mindig HTTP-vel csatlakozhatnak a proxyhoz.

* A **proxy_host** a proxykiszolgáló címe. Ha a proxykiszolgáló hitelesítést igényel, a proxykiszolgáló részeként megadhatja a hitelesítő adatait a következő formátumban: **felhasználó**:**Password** \@ **proxy_host**.

* A **proxy_port** az a hálózati port, amelyen a proxy válaszol a hálózati forgalomra.

## <a name="install-the-runtime-through-a-proxy"></a>A futtatókörnyezet telepítése proxyn keresztül

Azt jelzi, hogy a IoT Edge-eszköz Windows vagy Linux rendszeren fut-e, a proxykiszolgálón keresztül kell hozzáférnie a telepítési csomagokhoz. Az operációs rendszertől függően hajtsa végre az IoT Edge futtatókörnyezetnek a proxykiszolgálón keresztüli telepítéséhez szükséges lépéseket.

### <a name="linux-devices"></a>Linuxos eszközök

Ha Linux rendszerű eszközön telepíti a IoT Edge futtatókörnyezetet, konfigurálja a csomagkezelő szolgáltatást a proxykiszolgáló eléréséhez a telepítési csomaghoz való hozzáféréshez. Tegyük fel például, [hogy az apt-get http-proxy használatára van beállítva](https://help.ubuntu.com/community/AptGet/Howto/#Setting_up_apt-get_to_use_a_http-proxy). A csomagkezelő konfigurálása után kövesse a [Azure IoT Edge futtatókörnyezet telepítése](how-to-install-iot-edge.md) a szokásos módon című témakör utasításait.

### <a name="windows-devices"></a>Windows rendszerű eszközök

Ha Windows rendszerű eszközön telepíti a IoT Edge futtatókörnyezetet, kétszer kell megadnia a proxykiszolgálót. Az első csatlakozás letölti a telepítő parancsfájlt, a második pedig a telepítés során a szükséges összetevők letöltéséhez. A proxybeállításokat a Windows beállításaiban konfigurálhatja, vagy közvetlenül a PowerShell-parancsokban is megadhatja a proxy adatait.

Az alábbi lépések a Windows telepítésének példáját szemléltetik a következő `-proxy` argumentum használatával:

1. A Invoke-WebRequest parancsnak proxy információra van szüksége a telepítési parancsfájl eléréséhez. Ezután a Deploy-IoTEdge parancsnak szüksége van a proxy adataira a telepítési fájlok letöltéséhez.

   ```powershell
   . {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; Deploy-IoTEdge -proxy <proxy URL>
   ```

2. A Initialize-IoTEdge parancsnak nem kell átesnie a proxykiszolgálót, így a második lépés csak a Request-webkéréshez szükséges proxy-információkat igényli.

   ```powershell
   . {Invoke-WebRequest -proxy <proxy URL> -useb aka.ms/iotedge-win} | Invoke-Expression; Initialize-IoTEdge
   ```

Ha az URL-címben nem szereplő proxykiszolgáló esetében bonyolult hitelesítő adatokkal rendelkezik, használja a paramétert a következőn `-ProxyCredential` belül: `-InvokeWebRequestParameters` . Példa:

```powershell
$proxyCredential = (Get-Credential).GetNetworkCredential()
. {Invoke-WebRequest -proxy <proxy URL> -ProxyCredential $proxyCredential -useb aka.ms/iotedge-win} | Invoke-Expression; `
Deploy-IoTEdge -InvokeWebRequestParameters @{ '-Proxy' = '<proxy URL>'; '-ProxyCredential' = $proxyCredential }
```

A proxy paraméterekkel kapcsolatos további információkért lásd: [meghívó-webkérelem](/powershell/module/microsoft.powershell.utility/invoke-webrequest). További információ a Windows telepítési paraméterekről: [PowerShell-parancsfájlok IoT Edge Windows rendszeren](reference-windows-scripts.md).

## <a name="configure-the-daemons"></a>A démonok konfigurálása

A IoT Edge a IoT Edge eszközön futó két démonra támaszkodik. A Moby Daemon lehetővé teszi a webes kérések számára a tárolói lemezképek lekérését a tároló-beállításjegyzékből. A IoT Edge démon webes kérelmeket tesz lehetővé IoT Hubekkel való kommunikációhoz.

A Moby és a IoT Edge démonokat is konfigurálni kell, hogy a proxykiszolgálót használják a folyamatos eszköz működéséhez. Ez a lépés a IoT Edge eszközön történik a kezdeti eszköz beállítása során.

### <a name="moby-daemon"></a>Moby Daemon

Mivel a Moby a Docker-re épül, tekintse meg a Docker dokumentációját, és konfigurálja a Moby Daemon-t környezeti változók használatával. A legtöbb tároló-nyilvántartás (beleértve a DockerHub és az Azure Container nyilvántartót is) támogatja a HTTPS-kérelmeket, így a beállított paraméter **HTTPS_PROXY**. Ha olyan beállításjegyzékből húz le képeket, amely nem támogatja a TLS-t, akkor a **HTTP_PROXY** paramétert kell beállítania.

Válassza ki a IoT Edge eszköz operációs rendszerére vonatkozó cikket:

* [Docker-démon konfigurálása Linuxon](https://docs.docker.com/config/daemon/systemd/#httphttps-proxy) A Linux-eszközökön a Moby Daemon megőrzi a Docker nevet.
* [A Docker-démon konfigurálása Windows](/virtualization/windowscontainers/manage-docker/configure-docker-daemon#proxy-configuration) rendszeren A Windows-eszközökön a Moby Daemon neve iotedge-Moby. A nevek eltérőek, mert lehetséges a Docker Desktop és a Moby párhuzamos futtatása Windows-eszközön.

### <a name="iot-edge-daemon"></a>IoT Edge démon

A IoT Edge démon a Moby démonhoz hasonló módon van konfigurálva. A következő lépésekkel állíthatja be a szolgáltatás környezeti változóját az operációs rendszer alapján.

A IoT Edge démon mindig HTTPS-t használ, hogy kéréseket küldjön a IoT Hubnak.

#### <a name="linux"></a>Linux

Nyisson meg egy szerkesztőt a terminálon a IoT Edge démon konfigurálásához.

```bash
sudo systemctl edit iotedge
```

Adja meg a következő szöveget, **\<proxy URL>** és cserélje le a proxykiszolgáló-címe és a port elemre. Ezután mentse és lépjen ki.

```ini
[Service]
Environment="https_proxy=<proxy URL>"
```

Frissítse a Service Managert az IoT Edge új konfigurációjának kiválasztásához.

```bash
sudo systemctl daemon-reload
```

A módosítások érvénybe léptetéséhez indítsa újra IoT Edge.

```bash
sudo systemctl restart iotedge
```

Ellenőrizze, hogy a környezeti változó létrejött-e, és hogy az új konfiguráció be lett-e töltve.

```bash
systemctl show --property=Environment iotedge
```

#### <a name="windows"></a>Windows

Nyisson meg egy PowerShell-ablakot rendszergazdaként, és futtassa a következő parancsot a beállításjegyzék új környezeti változóval való szerkesztéséhez. Cserélje le a helyére a **\<proxy url>** proxykiszolgáló-címe és portját.

```powershell
reg add HKLM\SYSTEM\CurrentControlSet\Services\iotedge /v Environment /t REG_MULTI_SZ /d https_proxy=<proxy URL>
```

A módosítások érvénybe léptetéséhez indítsa újra IoT Edge.

```powershell
Restart-Service iotedge
```

## <a name="configure-the-iot-edge-agent"></a>A IoT Edge-ügynök konfigurálása

A IoT Edge ügynök az első modul, amely bármely IoT Edge eszközön elindul. Először a IoT Edge config. YAML fájlban található információk alapján indult el. A IoT Edge ügynök ezután csatlakozik a IoT Hubhoz az üzembe helyezési jegyzékek lekéréséhez, amely azt deklarálja, hogy az eszközön milyen más modulok legyenek telepítve.

Ez a lépés a IoT Edge eszközön a kezdeti eszköz beállításakor kerül megrendezésre.

1. Nyissa meg a config. YAML fájlt a IoT Edge eszközön. Linux rendszereken a fájl a következő helyen található: **/etc/iotedge/config.YAML**. Windows rendszereken ez a fájl a következő helyen található: **C:\ProgramData\iotedge\config.YAML**. A konfigurációs fájl védett, ezért rendszergazdai jogosultságok szükségesek az eléréséhez. Linux rendszereken használja a `sudo` parancsot, mielőtt megnyitja a fájlt a kívánt szövegszerkesztőben. Windows rendszeren nyisson meg egy szövegszerkesztőt, például a Jegyzettömböt rendszergazdaként, majd nyissa meg a fájlt.

2. A config. YAML fájlban keresse meg az **Edge Agent moduljának spec** szakaszát. A IoT Edge ügynök definíciója tartalmaz egy **env** paramétert, ahol környezeti változókat adhat hozzá.

3. Távolítsa el az ENV paraméter helyőrzőit tartalmazó kapcsos zárójeleket, és vegye fel az új változót egy új sorba. Ne feledje, hogy a YAML behúzása két szóköz.

   ```yaml
   https_proxy: "<proxy URL>"
   ```

4. A IoT Edge futtatókörnyezet alapértelmezés szerint a AMQP-t használja a IoT Hubsal való kommunikációhoz. Egyes proxykiszolgálók blokkolja a AMQP-portokat. Ha ez a helyzet, akkor a edgeAgent-t is konfigurálnia kell, hogy a AMQP-t használja WebSocket-kapcsolaton keresztül. Adjon hozzá egy második környezeti változót.

   ```yaml
   UpstreamProtocol: "AmqpWs"
   ```

   ![edgeAgent-definíció környezeti változókkal](./media/how-to-configure-proxy-support/edgeagent-edited.png)

5. Mentse a változtatásokat a config. YAML fájlba, és zárjuk be a szerkesztőt. A módosítások érvénybe léptetéséhez indítsa újra IoT Edge.

   * Linux:

      ```bash
      sudo systemctl restart iotedge
      ```

   * Windows:

      ```powershell
      Restart-Service iotedge
      ```

## <a name="configure-deployment-manifests"></a>Üzembe helyezési jegyzékek konfigurálása  

Ha a IoT Edge eszköz konfigurálva van a proxykiszolgálóhoz való együttműködésre, akkor továbbra is deklarálnia kell a HTTPS_PROXY környezeti változót a jövőbeli üzembe helyezési jegyzékekben. Az üzembe helyezési jegyzékeket szerkesztheti a Azure Portal varázslóval vagy a telepítési jegyzékfájl JSON-fájljának szerkesztésével is.

Mindig konfigurálja a két futásidejű modult, a edgeAgent-t és a edgeHub-t úgy, hogy az a proxykiszolgálón keresztül kommunikáljon, hogy a kapcsolatot a IoT Hubával is karban lehessen tartani. Ha eltávolítja a edgeAgent modulból a proxy adatait, a kapcsolat újbóli létrehozásához szerkessze a config. YAML fájlt az eszközön az előző szakaszban leírtak szerint.

A edgeAgent és a edgeHub modulon kívül más moduloknak is szükségük lehet a proxy konfigurálására. Az Azure-erőforrásokhoz IoT Hub, például a blob Storage-hoz való hozzáféréshez szükséges moduloknak a telepítési jegyzékfájlban megadott HTTPS_PROXY változóval kell rendelkezniük.

A IoT Edge eszköz teljes élettartama során a következő eljárás alkalmazható.

### <a name="azure-portal"></a>Azure Portal

Ha a **modulok beállítása** varázslóval hoz létre központi telepítéseket IoT Edge eszközökhöz, minden modulhoz tartozik egy **környezeti változók** szakasz, ahol konfigurálhatja a proxykiszolgáló kapcsolatait.

A IoT Edge-ügynök és a IoT Edge hub-modulok konfigurálásához válassza a futtatási **Beállítások** lehetőséget a varázsló első lépésében.

![Speciális Edge-futtatókörnyezet beállításainak konfigurálása](./media/how-to-configure-proxy-support/configure-runtime.png)

Adja hozzá a **https_proxy** környezeti változót mind a IoT Edge Agent, mind a IoT Edge hub modul-definícióhoz. Ha a **UpstreamProtocol** környezeti változót a IoT Edge eszköz config. YAML fájljában is felveszi, adja hozzá az IoT Edge Agent modul definícióját is.

![Https_proxy környezeti változó beállítása](./media/how-to-configure-proxy-support/edgehub-environmentvar.png)

Az üzembe helyezési jegyzékbe felvett összes többi modul ugyanazt a mintát követi.

### <a name="json-deployment-manifest-files"></a>JSON-telepítési jegyzékfájlok fájljai

Ha a Visual Studio Code-ban lévő sablonokkal vagy a JSON-fájlok manuális létrehozásával hozza létre a IoT Edge eszközökhöz készült központi telepítéseket, a környezeti változókat közvetlenül is hozzáadhatja az egyes modulok definíciói számára.

Használja a következő JSON-formátumot:

```json
"env": {
    "https_proxy": {
        "value": "<proxy URL>"
    }
}
```

A környezeti változók között a modul definíciójának a következő edgeHub hasonlóan kell kinéznie:

```json
"edgeHub": {
    "type": "docker",
    "settings": {
        "image": "mcr.microsoft.com/azureiotedge-hub:1.1",
        "createOptions": ""
    },
    "env": {
        "https_proxy": {
            "value": "http://proxy.example.com:3128"
        }
    },
    "status": "running",
    "restartPolicy": "always"
}
```

Ha a **UpstreamProtocol** környezeti változót a IoT Edge eszköz config. YAML fájljában is felveszi, adja hozzá az IoT Edge Agent modul definícióját is.

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

## <a name="working-with-traffic-inspecting-proxies"></a>A forgalom kezelése – proxyk vizsgálata

Ha a használni kívánt proxy TLS-védelemmel ellátott kapcsolatokon végzi a forgalom ellenőrzését, fontos megjegyezni, hogy az X. 509 tanúsítvánnyal történő hitelesítés nem működik. IoT Edge egy TLS-csatornát hoz létre, amelyet a rendszer a megadott tanúsítvánnyal és kulccsal végződik titkosított. Ha a csatorna megszakadt a forgalom ellenőrzésekor, a proxy nem tudja újra létrehozni a megfelelő hitelesítő adatokkal rendelkező csatornát, IoT Hub és a IoT Hub Device kiépítési szolgáltatás `Unauthorized` hibát ad vissza.

Ha olyan proxyt szeretne használni, amely forgalom-ellenőrzést hajt végre, akkor a vizsgálat elkerülése érdekében használja a közös hozzáférésű aláírások hitelesítését, vagy IoT Hub és a IoT Hub Device kiépítési szolgáltatást egy engedélyezési listához.

## <a name="next-steps"></a>Következő lépések

További információ a [IoT Edge futtatókörnyezet](iot-edge-runtime.md)szerepköreiről.

Telepítési és konfigurációs hibák elhárítása [gyakori problémákkal és](troubleshoot.md) megoldásokkal a Azure IoT Edge
