---
title: A IoT hub-adatai valós idejű adatvizualizációja egy webalkalmazásban
description: Egy webalkalmazás használatával megjelenítheti az érzékelőből összegyűjtött hőmérséklet-és páratartalom-adatokat, és elküldheti azokat az IOT hubhoz.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 05/31/2019
ms.author: robinsh
ms.custom:
- 'Role: Cloud Development'
- 'Role: Data Analytics'
- devx-track-azurecli
ms.openlocfilehash: 53b5add7526b0c20487e8fe3adb0b8ebe207a2ce
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102200038"
---
# <a name="visualize-real-time-sensor-data-from-your-azure-iot-hub-in-a-web-application"></a>Valós idejű érzékelők adatainak megjelenítése az Azure IoT hub-ban egy webalkalmazásban

![Végpontok közötti diagram](./media/iot-hub-live-data-visualization-in-web-apps/1_iot-hub-end-to-end-diagram.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>Ismertetett témák

Ebből az oktatóanyagból megtudhatja, hogyan jelenítheti meg a valós idejű érzékelők adatait, amelyeket az IoT hub a helyi számítógépen futó node.js webalkalmazáshoz kap. A webalkalmazás helyi futtatása után igény szerint követheti a webalkalmazás futtatásának lépéseit Azure App Serviceban. Ha Power BI használatával szeretné megjeleníteni az IoT hub adatait, tekintse meg a [Power bi használata a valós idejű érzékelők adatainak megjelenítéséhez az Azure IoT hub](iot-hub-live-data-visualization-in-power-bi.md).

## <a name="what-you-do"></a>Teendők

* Vegyen fel egy fogyasztói csoportot az IoT hubhoz, amelyet a webalkalmazás az érzékelő adatai olvasásához használ
* A webalkalmazás kódjának letöltése a GitHubról
* A webalkalmazás kódjának vizsgálata
* Környezeti változók konfigurálása a webalkalmazáshoz szükséges IoT Hub-összetevők tárolásához
* A webalkalmazás futtatása a fejlesztői gépen
* Nyisson meg egy weblapot az IoT hub valós idejű hőmérséklet-és páratartalom-adatainak megjelenítéséhez
* Választható A webalkalmazás üzemeltetése az Azure CLI használatával Azure App Service

## <a name="what-you-need"></a>Amire szükség lesz

* Fejezze be a [málna PI online szimulátor](iot-hub-raspberry-pi-web-simulator-get-started.md) oktatóanyagát vagy az eszköz egyik oktatóanyagát; például a [málna PI és a node.js](iot-hub-raspberry-pi-kit-node-get-started.md). Ezek az alábbi követelményekre vonatkoznak:

  * Aktív Azure-előfizetés
  * Az előfizetés alá tartozó IOT hub
  * Egy ügyfélalkalmazás, amely üzeneteket küld az IOT hubhoz

* [A git letöltése](https://www.git-scm.com/downloads)

* A cikkben ismertetett lépések feltételezik a Windows fejlesztői gépeket; ezeket a lépéseket azonban egyszerűen elvégezheti az előnyben részesített rendszerhéjban található Linux rendszeren is.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="add-a-consumer-group-to-your-iot-hub"></a>Fogyasztói csoport hozzáadása az IoT hub-hoz

A [fogyasztói csoportok](../event-hubs/event-hubs-features.md#event-consumers) független nézeteket biztosítanak az esemény-adatfolyamhoz, amely lehetővé teszi, hogy az alkalmazások és az Azure-szolgáltatások egymástól függetlenül használják az adott Event hub-végpont adatait. Ebben a szakaszban egy fogyasztói csoportot ad hozzá az IoT hub beépített végpontához, amelyet a webalkalmazás az adatok olvasására fog használni.

A következő parancs futtatásával vegyen fel egy fogyasztói csoportot az IoT hub beépített végpontján:

```azurecli-interactive
az iot hub consumer-group create --hub-name YourIoTHubName --name YourConsumerGroupName
```

Jegyezze fel a kiválasztott nevet, majd az oktatóanyag későbbi részében szüksége lesz rá.

## <a name="get-a-service-connection-string-for-your-iot-hub"></a>IoT hub szolgáltatáshoz tartozó szolgáltatási kapcsolatok karakterláncának beolvasása

A IoT hubok több alapértelmezett hozzáférési házirenddel jönnek létre. Az egyik ilyen szabályzat a **szolgáltatási** szabályzat, amely megfelelő engedélyeket biztosít a szolgáltatás számára az IoT hub végpontjának olvasásához és írásához. Futtassa a következő parancsot, hogy lekérje a IoT hub kapcsolódási karakterláncát, amely megfelel a szolgáltatási házirendnek:

```azurecli-interactive
az iot hub show-connection-string --hub-name YourIotHub --policy-name service
```

A kapcsolódási karakterláncnak a következőhöz hasonlóan kell kinéznie:

```javascript
"HostName={YourIotHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"
```

Jegyezze fel a szolgáltatási kapcsolatok karakterláncát, ezért az oktatóanyag későbbi részében szüksége lesz rá.

## <a name="download-the-web-app-from-github"></a>A webalkalmazás letöltése a GitHubról

Nyisson meg egy parancssorablakot, és írja be a következő parancsokat a minta GitHubról való letöltéséhez és a minta könyvtárra való váltáshoz:

```cmd
git clone https://github.com/Azure-Samples/web-apps-node-iot-hub-data-visualization.git
cd web-apps-node-iot-hub-data-visualization
```

## <a name="examine-the-web-app-code"></a>A webalkalmazás kódjának vizsgálata

A Web-Apps-Node-IOT-hub-adatvizualizáció könyvtárában nyissa meg a webalkalmazást a kedvenc szerkesztőjében. Az alábbi ábrán a VS Code-ban megtekintett fájl szerkezete látható:

![Webalkalmazás-fájl szerkezete](./media/iot-hub-live-data-visualization-in-web-apps/web-app-files.png)

Szánjon egy kis időt a következő fájlok vizsgálatára:

* A **Server.js** egy olyan szolgáltatás-oldali parancsfájl, amely inicializálja a webes szoftvercsatornát és az Event hub burkoló osztályát. Visszahívást biztosít az Event hub burkoló osztályához, amelyet az osztály a bejövő üzenetek webes szoftvercsatornára való szórására használ.

* A **Event-hub-reader.js** egy olyan szolgáltatás-oldali parancsfájl, amely az IoT hub beépített végpontját a megadott kapcsolati sztring és fogyasztói csoport használatával köti össze. Kibontja a DeviceId és a EnqueuedTimeUtc a beérkező üzenetek metaadataiból, majd továbbítja az üzenetet a server.js által regisztrált visszahívási módszer használatával.

* **Chart-device-data.js** egy ügyféloldali parancsfájl, amely figyeli a webes szoftvercsatornát, nyomon követi az egyes DeviceID-ket, és minden eszközön tárolja az utolsó 50 pontot a bejövő adathoz. Ezután a kiválasztott eszközhöz tartozó adategységeket a diagram objektumhoz köti.

* **Index.html** kezeli a weblap felhasználói felületének elrendezését, és az ügyféloldali logikához szükséges parancsfájlokra hivatkozik.

## <a name="configure-environment-variables-for-the-web-app"></a>Környezeti változók konfigurálása a webalkalmazáshoz

Az IoT hub adatainak beolvasásához a webalkalmazásnak szüksége van az IoT hub kapcsolati karakterláncára és annak a fogyasztói csoportnak a nevére, amelyet át kell olvasnia. Ezeket a karakterláncokat a folyamat-környezetből kapja meg a következő sorokban server.js:

```javascript
const iotHubConnectionString = process.env.IotHubConnectionString;
const eventHubConsumerGroup = process.env.EventHubConsumerGroup;
```

Állítsa be a környezeti változókat a parancsablakban a következő parancsokkal. Cserélje le a helyőrző értékeket az IoT hub szolgáltatási kapcsolatok karakterláncára, valamint a korábban létrehozott fogyasztói csoport nevére. Ne idézze a karakterláncokat.

```cmd
set IotHubConnectionString=YourIoTHubConnectionString
set EventHubConsumerGroup=YourConsumerGroupName
```

## <a name="run-the-web-app"></a>A webalkalmazás futtatása

1. Győződjön meg arról, hogy az eszköz fut és adatokat küld.

2. A parancssorablakban futtassa a következő sorokat a hivatkozott csomagok letöltéséhez és telepítéséhez, majd indítsa el a webhelyet:

   ```cmd
   npm install
   npm start
   ```

3. Meg kell jelennie a kimenetnek a konzolon, amely azt jelzi, hogy a webalkalmazás sikeresen csatlakozott az IoT hub-hoz, és a 3000-es porton figyeli a következőt:

   ![A webalkalmazás elindult a konzolon](./media/iot-hub-live-data-visualization-in-web-apps/web-app-console-start.png)

## <a name="open-a-web-page-to-see-data-from-your-iot-hub"></a>Nyisson meg egy weblapot az IoT hub adatainak megjelenítéséhez

Nyisson meg egy böngészőt a következőhöz: `http://localhost:3000` .

Az eszköz **kiválasztása** listából válassza ki az eszközt, és tekintse meg az eszköz által az IoT hubhoz eljuttatott utolsó 50 hőmérséklet-és páratartalom-adatpontok egy futó ábráját.

![Webalkalmazás lap valós idejű hőmérsékletet és páratartalmat mutat](./media/iot-hub-live-data-visualization-in-web-apps/web-page-output.png)

Azt is meg kell jelennie a konzolon, hogy a webalkalmazás által a böngésző ügyfelének küldött üzenetek megjelenjenek:  

![A webalkalmazás szórási kimenete a konzolon](./media/iot-hub-live-data-visualization-in-web-apps/web-app-console-broadcast.png)

## <a name="host-the-web-app-in-app-service"></a>A webalkalmazás üzemeltetése App Service

A [Azure App Service Web Apps szolgáltatása](../app-service/overview.md) a webalkalmazások üzemeltetésére szolgáló platformként szolgál (a Pásti). A Azure App Serviceban üzemeltetett webalkalmazások kihasználhatják a hatékony Azure-funkciókat, például a további biztonságot, a terheléselosztást és a méretezhetőséget, valamint az Azure-és partneri DevOps-megoldásokat, például a folyamatos üzembe helyezést, a csomagok felügyeletét stb. Azure App Service támogatja a számos népszerű nyelven fejlesztett és Windows-vagy Linux-infrastruktúrán üzembe helyezett webalkalmazást.

Ebben a szakaszban egy webalkalmazást hoz létre a App Serviceban, és üzembe helyezi a kódját az Azure CLI-parancsok használatával. Az az [WebApp](/cli/azure/webapp) dokumentációjában használt parancsok részleteit megtekintheti. Mielőtt elkezdené, győződjön meg arról, hogy elvégezte az [erőforráscsoport hozzáadásának](#add-a-consumer-group-to-your-iot-hub)lépéseit az IoT hub-hoz, [szerezzen be egy szolgáltatás-kapcsolati karakterláncot az IoT hub számára](#get-a-service-connection-string-for-your-iot-hub), és [töltse le a webalkalmazást a githubról](#download-the-web-app-from-github).

1. Az [app Service-csomag](../app-service/overview-hosting-plans.md) számítási erőforrásokat határoz meg app Service által futtatott alkalmazáshoz. Ebben az oktatóanyagban a fejlesztő/ingyenes szintet használjuk a webalkalmazás üzemeltetéséhez. Az ingyenes szinten a webalkalmazás a megosztott Windows-erőforrásokon fut más App Service alkalmazásokkal, többek között más ügyfelek alkalmazásaival. Az Azure App Service terveket is kínál a webalkalmazások Linux számítási erőforrásokon való üzembe helyezéséhez. Ezt a lépést kihagyhatja, ha már rendelkezik a használni kívánt App Service-csomaggal.

   Ha App Service csomagot szeretne létrehozni a Windows ingyenes szintjével, futtassa a következő parancsot. Használja ugyanazt az erőforráscsoportot, amelyben az IoT hub található. A szolgáltatási csomag neve kis-és nagybetűket, számokat és kötőjeleket tartalmazhat.

   ```azurecli-interactive
   az appservice plan create --name <app service plan name> --resource-group <your resource group name> --sku FREE
   ```

2. Most hozzon létre egy webalkalmazást a App Service tervben. A `--deployment-local-git` paraméter lehetővé teszi a webalkalmazás kódjának feltöltését és üzembe helyezését a helyi gépen található git-adattárból. A webalkalmazás nevének globálisan egyedinek kell lennie, és kis-és nagybetűket, számokat és kötőjeleket tartalmazhat. Ügyeljen arra, hogy a (z) paraméterhez a (z) 10,6-es vagy újabb csomópontot `--runtime` használja a használt Node.js futtatókörnyezet verziójától függően. A `az webapp list-runtimes` parancs segítségével lekérheti a támogatott futtatókörnyezetek listáját.

   ```azurecli-interactive
   az webapp create -n <your web app name> -g <your resource group name> -p <your app service plan name> --runtime "node|10.6" --deployment-local-git
   ```

3. Most adja meg az IoT hub kapcsolati karakterláncot és az Event hub fogyasztói csoportot megadó környezeti változók alkalmazás-beállításait. Az egyes beállítások a paraméterben vannak elválasztva `-settings` . Használja a IoT hub szolgáltatási kapcsolatok karakterláncát és az oktatóanyagban korábban létrehozott fogyasztói csoportot. Ne idézze az értékeket.

   ```azurecli-interactive
   az webapp config appsettings set -n <your web app name> -g <your resource group name> --settings EventHubConsumerGroup=<your consumer group> IotHubConnectionString="<your IoT hub connection string>"
   ```

4. Engedélyezze a web Sockets protokollt a webalkalmazáshoz, és állítsa be úgy a webalkalmazást, hogy csak HTTPS-kérelmeket fogadjon (a HTTP-kérelmeket a rendszer a HTTPS-re irányítja át)

   ```azurecli-interactive
   az webapp config set -n <your web app name> -g <your resource group name> --web-sockets-enabled true
   az webapp update -n <your web app name> -g <your resource group name> --https-only true
   ```

5. A kód App Serviceba való üzembe helyezéséhez [felhasználói szintű központi telepítési hitelesítő adatait](../app-service/deploy-configure-credentials.md)fogja használni. A felhasználói szintű központi telepítési hitelesítő adatai eltérnek az Azure-beli hitelesítő adataitól, és a git helyi és FTP-környezetekben való üzembe helyezéséhez használatosak a webalkalmazások számára. A beállítás után az Azure-fiók összes előfizetésében érvényesek lesznek az összes App Service alkalmazásban. Ha korábban beállította a felhasználói szintű központi telepítési hitelesítő adatokat, használhatja azokat.

   Ha korábban még nem állította be a felhasználói szintű központi telepítési hitelesítő adatokat, vagy nem emlékszik a jelszavára, futtassa a következő parancsot. Az üzembe helyezési felhasználónévnek egyedinek kell lennie az Azure-ban, és nem tartalmazhat " \@ " szimbólumot a helyi git leküldéséhez. Amikor a rendszer kéri, írja be és erősítse meg az új jelszót. A jelszónak legalább nyolc karakterből kell állnia, és a következő három elem közül kettőnek kell lennie: betűk, számok és szimbólumok.

   ```azurecli-interactive
   az webapp deployment user set --user-name <your deployment user name>
   ```

6. Kérje meg a git URL-címét, hogy a kódot a App Serviceba küldje el.

   ```azurecli-interactive
   az webapp deployment source config-local-git -n <your web app name> -g <your resource group name>
   ```

7. Adjon hozzá egy távolit a klónhoz, amely a App Service webalkalmazás git-tárházára hivatkozik. A esetében \<Git clone URL\> használja az előző lépésben visszaadott URL-címet. Futtassa a következő parancsot a parancsablakban.

   ```cmd
   git remote add webapp <Git clone URL>
   ```

8. A kód App Serviceba való telepítéséhez írja be a következő parancsot a parancsablakban. Ha a rendszer hitelesítő adatokat kér, adja meg az 5. lépésben létrehozott felhasználói szintű központi telepítési hitelesítő adatokat. Győződjön meg arról, hogy leküldi a App Service távoli főágra.

    ```cmd
    git push webapp main:main
    ```

9. A központi telepítés előrehaladása a parancsablakban fog frissülni. A sikeres üzembe helyezés a következő kimenethez hasonló sorokkal végződik:

    ```cmd
    remote:
    remote: Finished successfully.
    remote: Running post deployment command(s)...
    remote: Deployment successful.
    To https://contoso-web-app-3.scm.azurewebsites.net/contoso-web-app-3.git
    6b132dd..7cbc994  main -> main
    ```

10. Futtassa a következő parancsot a webalkalmazás állapotának lekéréséhez, és győződjön meg arról, hogy az fut:

    ```azurecli-interactive
    az webapp show -n <your web app name> -g <your resource group name> --query state
    ```

11. Egy böngészőben nyissa meg a `https://<your web app name>.azurewebsites.net` oldalt. A webalkalmazás helyi futtatásakor megjelenő weboldalhoz hasonló weblap. Feltételezve, hogy az eszköz fut és adatokat küld, látnia kell az eszköz által küldött, a 50 legutóbbi hőmérséklet-és páratartalom-olvasások futó ábráját.

## <a name="troubleshooting"></a>Hibaelhárítás

Ha bármilyen probléma merül fel ezzel a mintával, próbálkozzon az alábbi részekben ismertetett lépésekkel. Ha továbbra is problémákat tapasztal, küldjön visszajelzést a témakör alján.

### <a name="client-issues"></a>Ügyfelekkel kapcsolatos problémák

* Ha egy eszköz nem jelenik meg a listában, vagy nem készül gráf, ellenőrizze, hogy az eszközön fut-e a kód.

* A böngészőben nyissa meg a fejlesztői eszközöket (számos böngészőben az F12 billentyű megnyithatja), és keresse meg a konzolt. Keresse meg az ott kinyomtatott figyelmeztetéseket és hibákat.

* Az ügyféloldali szkriptek hibakeresése a/JS/chat-device-data.jsban végezhető el.

### <a name="local-website-issues"></a>Helyi webhely problémái

* Tekintse meg az ablakban azt a kimenetet, amelyen a konzol kimenetéhez tartozó csomópontot indította.

* Hibakeresés a kiszolgáló kódjával, pontosabban server.js és/Scripts/event-hub-reader.js.

### <a name="azure-app-service-issues"></a>Azure App Service problémák

* A Azure Portalban nyissa meg a webalkalmazást. A bal oldali ablaktábla **figyelés** területén válassza a **app Service naplók** lehetőséget. Kapcsolja be az **alkalmazás naplózása (fájlrendszer)** beállítást be értékre, állítsa a hiba **szintet** , majd kattintson a **Mentés** gombra. Ezután nyissa meg a **naplózási adatfolyamot** (a **figyelés** alatt).

* A Azure Portal webalkalmazásában a **fejlesztői eszközök**  területen válassza ki a  **konzolt** , és ellenőrizze a csomópont-és NPM verzióit a és a segítségével `node -v` `npm -v` .

* Ha a csomag nem talál hibát, lehetséges, hogy a lépéseket a sorrend szerint futtatta. Ha a hely telepítve van (a `git push` -vel), az App Service fut `npm install` , amely a csomópont aktuális verziója alapján fut. Ha később módosítja a konfigurációt, értelmetlen módosítást kell végeznie a kódban, és újra le kell küldenie.

## <a name="next-steps"></a>Következő lépések

Sikeresen használta a webalkalmazást az IoT hub valós idejű érzékelői adatainak megjelenítéséhez.

Az Azure IoT Hubból származó adatok megjelenítésének másik módja: [a Power bi használata a valós idejű érzékelők adatainak megjelenítéséhez az IoT hub-ból](iot-hub-live-data-visualization-in-power-bi.md).

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
