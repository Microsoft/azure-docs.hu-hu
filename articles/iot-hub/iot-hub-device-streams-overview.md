---
title: Azure IoT Hub eszköz streamek | Microsoft Docs
description: Az Azure IoT Hub-eszközök streamek áttekintése, amelyek a biztonságos kétirányú TCP-alagutakat a különböző felhőből eszközre irányuló kommunikációs forgatókönyvek esetében teszik lehetővé.
author: robinsh
services: iot-hub
ms.service: iot-hub
ms.topic: conceptual
ms.date: 01/15/2019
ms.author: robinsh
ms.custom:
- 'Role: Cloud Development'
- 'Role: IoT Device'
- 'Role: Technical Support'
ms.openlocfilehash: 9487fc562fa099d2650aabc8d15fc1449c7fcb5c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97825166"
---
# <a name="iot-hub-device-streams-preview"></a>IoT Hub eszköz streamek (előzetes verzió)

Az Azure IoT Hub- *eszközök adatfolyamai* lehetővé teszik a biztonságos kétirányú TCP-alagutak létrehozását számos felhőből az eszközre irányuló kommunikációs forgatókönyvek esetében. Az adatfolyamot egy IoT Hub *adatfolyam-végpont* kíséri, amely proxyként működik az eszköz és a szolgáltatás végpontja között. Ez a beállítás, amely az alábbi ábrán látható, különösen akkor hasznos, ha az eszközök hálózati tűzfal mögött vannak, vagy egy magánhálózat belsejében találhatók. Ennek megfelelően a IoT Hub-adatfolyamok segítenek megszólítani az ügyfeleket a IoT-eszközök tűzfalon keresztüli eléréséhez, és nincs szükség a bejövő vagy kimenő hálózati tűzfal portjainak széles körű megnyitására.

!["IoT Hub eszköz streamek áttekintése"](./media/iot-hub-device-streams-overview/iot-hub-device-streams-overview.png )

IoT Hub-adatfolyamok használata esetén az eszközök biztonságban maradnak, és csak a 443-as porton keresztül kell megnyitnia a kimenő TCP-kapcsolatokat az IoT hub folyamatos átviteli végpontján. A stream létrehozása után a szolgáltatás-és az eszközön futó alkalmazások programozott hozzáféréssel rendelkeznek egy WebSocket-ügyfél-objektumhoz, hogy a nyers bájtokat egy másikra küldjék és fogadják. Az alagút által biztosított megbízhatósági és rendezési garanciák a TCP-vel egyenrangúak.

## <a name="benefits"></a>Előnyök

Az eszközök IoT Hub a következő előnyöket nyújtják:

* **Tűzfal-barát biztonságos kapcsolat:** A IoT-eszközök elérhetők a szolgáltatási végpontokról a bejövő tűzfal portjának az eszközön vagy a hálózati peremhálózaton való megnyitása nélkül (a 443-as porton csak a IoT Hub kimenő kapcsolat szükséges).

* **Hitelesítés:** Az alagút mindkét eszközét és szolgáltatását hitelesíteni kell IoT Hub a hozzájuk tartozó hitelesítő adatok használatával.

* **Titkosítás:** Alapértelmezés szerint IoT Hub az eszközökön lévő adatfolyamok TLS-kompatibilis kapcsolatokat használnak. Ez biztosítja, hogy a forgalom mindig titkosítva legyen, függetlenül attól, hogy az alkalmazás titkosítást használ-e.

* **A kapcsolat egyszerűsége:** Sok esetben az eszközök használata nem teszi lehetővé a virtuális magánhálózatok összetett beállítását a IoT-eszközökhöz való csatlakozás engedélyezéséhez.

* **Kompatibilitás a TCP/IP-veremmel:** IoT Hub az eszközökön lévő adatfolyamok a TCP/IP-alkalmazások forgalmát is kielégítik. Ez azt jelenti, hogy a szabadalmaztatás és a szabványokon alapuló protokollok széles köre kihasználhatja ezt a funkciót.

* **Könnyű használat a magánhálózati beállításokban:** Az eszköz IP-címe helyett a szolgáltatás képes kommunikálni az eszközzel az eszköz azonosítójával való hivatkozással. Ez olyan helyzetekben hasznos, amikor egy eszköz egy magánhálózaton belül található, és magánhálózati IP-címmel rendelkezik, vagy az IP-címe dinamikusan van hozzárendelve, és ismeretlen a szolgáltatás oldalán.

## <a name="device-stream-workflows"></a>Eszköz stream-munkafolyamatok

Az adatfolyam akkor indul el, ha a szolgáltatás az eszköz AZONOSÍTÓjának megadásával kéri az eszközhöz való kapcsolódást. Ez a munkafolyamat különösen illeszkedik egy ügyfél/kiszolgáló kommunikációs modellbe, beleértve az SSH-t és az RDP-t, ahol a felhasználók távolról csatlakozhatnak az eszközön futó SSH-vagy RDP-kiszolgálóhoz SSH-vagy RDP-ügyfélprogram használatával.

Az eszköz adatfolyam-létrehozási folyamata az eszköz, a szolgáltatás, a IoT hub fő és a streaming végpontok közötti egyeztetést foglalja magában. Habár a IoT hub fő végpontja összehangolja az eszköz adatfolyamának létrehozását, a folyamatos átviteli végpont kezeli a szolgáltatás és az eszköz közötti forgalmat.

### <a name="device-stream-creation-flow"></a>Eszköz adatfolyam-létrehozási folyamata

Az eszköz SDK-val való programozott létrehozása a következő lépésekkel jár, amelyek az alábbi ábrán is láthatók:

!["Eszköz stream-kézfogás folyamata"](./media/iot-hub-device-streams-overview/iot-hub-device-streams-handshake.png)

1. Az eszköz a visszahívást előzetesen regisztrálja, hogy értesítést kapjon, amikor új adatfolyamot kezdeményeznek az eszközre. Ez a lépés általában akkor történik meg, amikor az eszköz elindul, és csatlakozik IoT Hubhoz.

2. Ha szükséges, az eszköz AZONOSÍTÓját (_nem_ az IP-címet) adja meg a szolgáltatási oldali program.

3. Az IoT hub az 1. lépésben regisztrált visszahívás meghívásával értesíti az eszközön található programot. Az eszköz elfogadhatja vagy elutasíthatja a stream kezdeményezési kérelmét. Ez a logika az alkalmazási forgatókönyvre is jellemző lehet. Ha az eszköz elutasítja az adatfolyam-kérelmet, IoT Hub ennek megfelelően értesíti a szolgáltatást; Ellenkező esetben kövesse az alábbi lépéseket.

4. Az eszköz a 443-as porton keresztül biztonságos kimenő TCP-csatlakozást hoz létre az adatfolyam-végponthoz, és frissíti a csatlakozást egy WebSocket-kapcsolattal. Az adatfolyam-végpont URL-címe és a hitelesítéshez használt hitelesítő adatok egyaránt IoT Hub a 3. lépésben küldött kérelem részeként.

5. A szolgáltatás értesítést kap arról, hogy az eszköz hogyan fogadja el az adatfolyamot, és hogyan hozza létre saját WebSocket-ügyfelet a folyamatos átviteli végponthoz. Hasonlóképpen megkapja az adatfolyam-végpont URL-címét és a hitelesítési adatokat IoT Hubból.

A fenti kézfogási folyamat során:

* A kézfogás folyamatának 60 másodpercen belül kell haladnia (2 – 5. lépés), ellenkező esetben a kézfogás meghiúsul, és a szolgáltatás ennek megfelelően értesítést kap.

* Miután a fenti stream-létrehozási folyamat befejeződik, a folyamatos átviteli végpont proxyként fog működni, és továbbítja a forgalmat a szolgáltatás és az eszköz között a megfelelő websocketeken keresztül.

* Az eszköznek és a szolgáltatásnak egyaránt kimenő kapcsolatra van szüksége IoT Hub fő végpontján, valamint a streaming végpontot a 443-as porton keresztül. A végpontok URL-címe a IoT Hub portáljának *Áttekintés* lapján érhető el.

* Egy meglévő stream megbízhatósága és rendezési garanciái a TCP-vel egyenrangúak.

* A IoT Hub és a streaming végponthoz való csatlakozás TLS-t használ, és titkosítva van.

### <a name="termination-flow"></a>Megszakítási folyamat

Egy létesített adatfolyam leáll, ha az átjáróhoz tartozó TCP-kapcsolatok valamelyike le van választva (a szolgáltatás vagy az eszköz által). Ezt önként is elvégezheti, ha a WebSocketet az eszközön vagy a szolgáltatáson futtatja, vagy a hálózati kapcsolat időtúllépése vagy a folyamat meghibásodása esetén akaratlanul is kizárja. Ha valamelyik eszköz vagy szolgáltatás csatlakozik a folyamatos átviteli végponthoz, a másik TCP-csatlakozás is (kényszerített) megszakad, és a szolgáltatás és az eszköz feladata, hogy szükség esetén újra létrehozza az adatfolyamot.

## <a name="connectivity-requirements"></a>Kapcsolódási követelmények

Az eszköz és a szolgáltatás összes oldalának képesnek kell lennie TLS-kompatibilis kapcsolat létesítésére IoT Hub és annak folyamatos átviteli végpontján. Ehhez a végpontokhoz a 443-es porton keresztül kimenő kapcsolatra van szükség. A végpontokhoz társított állomásnév a IoT Hub *Áttekintés* lapján található, az alábbi ábrán látható módon:

!["Eszköz stream-végpontok"](./media/iot-hub-device-streams-overview/device-stream-in-portal.png)

Másik lehetőségként a végpontok adatait az Azure CLI-vel is lekérheti a hub Properties (Tulajdonságok) szakasza, pontosabban `property.hostname` és `property.deviceStreams` kulcsaival.

```azurecli-interactive
az iot hub devicestream show --name <YourIoTHubName>
```

A kimenet az összes végpont JSON-objektuma, amelyet a hub eszközének és szolgáltatásának csatlakoznia kell ahhoz, hogy az eszköz streamet hozzon létre.

```json
{
  "streamingEndpoints": [
    "https://<YourIoTHubName>.<region-stamp>.streams.azure-devices.net"
  ]
}
```

> [!NOTE]
> Győződjön meg arról, hogy telepítette az Azure CLI 2.0.57 vagy újabb verzióját. A legújabb verziót az [Azure CLI telepítése](/cli/azure/install-azure-cli) oldaláról töltheti le.
>

## <a name="allow-outbound-connectivity-to-the-device-streaming-endpoints"></a>Kimenő kapcsolat engedélyezése az eszköz streaming végpontjai számára

A cikk elején említettek szerint az eszköz kimenő kapcsolatokat hoz létre IoT Hub streaming-végponthoz az adatfolyamok kezdeményezése során. Az eszközön vagy a hálózatán lévő tűzfalaknak engedélyeznie kell a kimenő kapcsolatot a folyamatos átviteli átjáróval az 443-as porton keresztül (vegye figyelembe, hogy a kommunikáció a TLS protokollal titkosított WebSocket-kapcsolaton keresztül történik).

Az eszköz folyamatos átviteli végpontjának állomásneve az Azure IoT Hub portálon, az Áttekintés lapon található. ![ " Eszköz stream-végpontok "](./media/iot-hub-device-streams-overview/device-stream-in-portal.png)

Azt is megteheti, hogy az Azure CLI használatával is megkeresi ezeket az információkat:

```azurecli-interactive
az iot hub devicestream show --name <YourIoTHubName>
```

> [!NOTE]
> Győződjön meg arról, hogy telepítette az Azure CLI 2.0.57 vagy újabb verzióját. A legújabb verziót az [Azure CLI telepítése](/cli/azure/install-azure-cli) oldaláról töltheti le.
>

## <a name="troubleshoot-via-device-streams-resource-logs"></a>Az eszköz-adatfolyamok erőforrás-naplóin keresztüli hibakeresés

Beállíthatja, hogy Azure Monitor összegyűjtse az erőforrás-naplókat a IoT Hub által kibocsátott [eszköz-adatfolyamok számára](monitor-iot-hub-reference.md#device-streams-preview) . Ez nagyon hasznos lehet a hibaelhárítási forgatókönyvekben.

Az alábbi lépéseket követve hozzon létre egy diagnosztikai beállítást, hogy az eszköz stream-naplókat küldjön a IoT Hub Azure Monitor naplókhoz:

1. A Azure Portalban navigáljon az IoT hubhoz. A bal oldali ablaktábla **figyelés** területén válassza a **diagnosztikai beállítások** elemet. Ezután válassza a **diagnosztikai beállítás hozzáadása** lehetőséget.

2. Adja meg a diagnosztikai beállítások nevét, és válassza a **DeviceStreams** lehetőséget a naplók listájából. Ezután válassza **a küldés log Analytics** lehetőséget. Egy meglévő Log Analytics munkaterületet kell választania, vagy újat kell létrehoznia.

    :::image type="content" source="media/iot-hub-device-streams-overview/device-streams-configure-diagnostics.png" alt-text="Eszköz stream-naplóinak engedélyezése":::

3. Miután létrehozta a diagnosztikai beállítást, hogy az eszköz stream-naplóit egy Log Analytics munkaterületre küldje el, a naplók elem kiválasztásával érheti el **a naplókat** **a IoT** hub bal oldali ablaktábláján a Azure Portalban. Az eszköz stream-naplói megjelennek a `AzureDiagnostics` táblában, és rendelkeznek `Category=DeviceStreams` . Vegye figyelembe, hogy a naplókban szereplő művelet végrehajtása több percig is eltarthat a táblázatban.

   Ahogy az alább látható, a megcélzott eszköz identitása és a művelet eredménye is elérhető a naplókban.

   !["A Device stream-naplók elérése"](./media/iot-hub-device-streams-overview/device-streams-view-logs.png)

Ha többet szeretne megtudni a Azure Monitor és a IoT Hub használatáról, tekintse meg a következőt: [monitoring IoT hub](monitor-iot-hub.md). További információ a IoT Hub rendelkezésre álló összes erőforrás-naplóról, metrikáról és táblázatról: az [Azure IoT hub adathivatkozásának figyelése](monitor-iot-hub-reference.md).

## <a name="regional-availability"></a>Regionális elérhetőség

A nyilvános előzetes verzióban IoT Hub eszköz streamek az USA középső régiójában, az USA középső – EUAP, Észak-Európában és Délkelet-ázsiai régióban érhetők el. Győződjön meg arról, hogy az egyik régióban hozza létre a hubot.

## <a name="sdk-availability"></a>SDK rendelkezésre állása

Az egyes streamek két oldala (az eszközön és a szolgáltatáson) a IoT Hub SDK használatával hozza létre az alagutat. A nyilvános előzetes verzióban az ügyfelek a következő SDK-nyelvek közül választhatnak:

* A C és C# SDK az eszköz oldalán lévő adatfolyamokat támogatja.

* A NodeJS és a C# SDK támogatja az eszközök streamjét a szolgáltatás oldalán.

## <a name="iot-hub-device-stream-samples"></a>IoT Hub Device stream-minták

A IoT Hub oldalon két gyors [példa](./index.yml) érhető el. Ezek az eszközökön futó adatfolyamok használatát mutatják be.

* Az *echo* -minta az eszköz-adatfolyamok programozott használatát mutatja be (az SDK API-k közvetlen meghívásával).

* A *helyi proxy* minta azt mutatja be, hogy az eszközön keresztül az ügyfél/kiszolgáló alkalmazások forgalmának (például SSH, RDP vagy web) bújtatása az eszközökön keresztül folyik.

Ezeket a mintákat az alábbi részletesebben tárgyaljuk.

### <a name="echo-sample"></a>Echo minta

Az ECHO minta azt mutatja be, hogy az eszköz-adatfolyamok programozottan használják a szolgáltatás-és eszköz-alkalmazások közötti bájtok küldésére és fogadására. Vegye figyelembe, hogy a szolgáltatás-és eszköz-programokat különböző nyelveken használhatja. Használhatja például a C eszköz programot a C# szolgáltatási programjával.

Az ECHO példák:

* [C# szolgáltatás-és szolgáltatási program](quickstart-device-streams-echo-csharp.md)

* [Node.js szolgáltatási program](quickstart-device-streams-echo-nodejs.md)

* [C eszköz program](quickstart-device-streams-echo-c.md)

### <a name="local-proxy-sample-for-ssh-or-rdp"></a>Helyi proxy minta (SSH vagy RDP esetén)

A helyi proxy minta azt mutatja be, hogyan lehet engedélyezni egy meglévő alkalmazás forgalmának bújtatását, amely magában foglalja az ügyfél és a kiszolgálói program közötti kommunikációt. Ez a beállítás olyan ügyfél-/kiszolgálói protokollok esetében működik, mint például az SSH és az RDP, ahol a szolgáltatás a (z) ügyfélként működik (SSH-vagy RDP-ügyfélprogramok futtatásával), az eszköz pedig kiszolgálóként működik (SSH-démont vagy RDP-kiszolgálói programokat futtatva).

Ez a szakasz azt ismerteti, hogyan használhatók az adatfolyamok a felhasználó számára az eszközökön keresztüli SSH-eszközökre (az RDP vagy más ügyfél/kiszolgáló alkalmazás esetében a protokoll megfelelő portjának használatával).

A telepítő két *helyi proxykiszolgálót* használ az alábbi ábrán látható módon, azaz az *eszköz helyi proxyját* és a *szolgáltatás helyi proxyját*. A helyi proxy programok felelősek az [eszköz stream-kezdeményezési kézfogásának](#device-stream-creation-flow) elvégzéséhez IoT hub használatával, valamint az SSH-ügyfél és az SSH démon és a normál ügyfél/kiszolgáló szoftvercsatornák használatával való interakció.

!["Az eszköz stream-proxy beállítása SSH/RDP-hez"](./media/iot-hub-device-streams-overview/iot-hub-device-streams-ssh.png)

1. A felhasználó a szolgáltatás helyi proxyját futtatva elindítja az eszköz streamjét az eszközre.

2. Az eszköz helyi proxyja elfogadja a stream kezdeményezési kérelmét, és az alagút a IoT Hub folyamatos átviteli végpontjának (a fentiekben leírtak szerint).

3. Az eszköz helyi proxyja csatlakozik az SSH démon-végponthoz, amely a 22-es porton figyel az eszközön.

4. A szolgáltatás helyi proxyja egy kijelölt porton figyeli a felhasználótól érkező új SSH-kapcsolatokat (a példában használt 2222-as portot), de ez más elérhető portra is konfigurálható. A felhasználó az SSH-ügyfelet a helyi helyi proxy portra mutat a localhost-on.

### <a name="notes"></a>Jegyzetek

* A fenti lépések teljes körű bújtatást végeznek az SSH-ügyfél (a jobb oldalon) és az SSH démon között (a bal oldalon). Ennek a végpontok közötti kapcsolatnak egy része a forgalom továbbítását jelenti egy IoT Hub.

* Az ábrán látható nyilak a végpontok közötti kapcsolatok irányát jelzik. Ügyeljen arra, hogy az eszközön ne legyenek bejövő kapcsolatok (ezt gyakran tűzfal blokkolja).

* Az 2222-as port használata a szolgáltatás helyi proxyján tetszőleges választás. A proxy konfigurálható úgy, hogy bármely más elérhető portot használjon.

* A 22-es port választása a protokolltól függ, és az SSH-ra van kiválasztva ebben az esetben. RDP esetén a 3389-es portot kell használni. Ez konfigurálható a megadott példákban.

Az alábbi hivatkozásokra kattintva megtudhatja, hogyan futtathatja a helyi proxy programokat a választott nyelven. Az [echo mintához](#echo-sample)hasonlóan az eszköz-és a szolgáltatás-helyi proxy programok is futtathatók különböző nyelveken, mivel azok teljes mértékben interoperábilisak.

* [C# szolgáltatás-és szolgáltatási program](quickstart-device-streams-proxy-csharp.md)

* [Node.js szolgáltatási program](quickstart-device-streams-proxy-nodejs.md)

* [C eszköz program](quickstart-device-streams-proxy-c.md)

## <a name="next-steps"></a>Következő lépések

Az alábbi hivatkozásokat követve további információkat tudhat meg az eszközök streamekről.

> [!div class="nextstepaction"]
> [IoT-show-ban lévő eszköz streamek (Channel 9)](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fchannel9.msdn.com%2FShows%2FInternet-of-Things-Show%2FAzure-IoT-Hub-Device-Streams&data=02%7C01%7Crezas%40microsoft.com%7Cc3486254a89a43edea7c08d67a88bcea%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C0%7C636831125031268909&sdata=S6u9qiehBN4tmgII637uJeVubUll0IZ4p2ddtG5pDBc%3D&reserved=0)
