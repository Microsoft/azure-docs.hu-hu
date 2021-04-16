---
title: IoT Hub-adatok valós idejű adatvizualizációja egy webalkalmazásban
description: Egy webalkalmazással vizualizálhatja az érzékelőktől gyűjtött és az Iot Hubnak küldött hőmérséklet- és páratartalom-adatokat.
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
ms.openlocfilehash: 4f2f0678b421ac6965b2848cc25564b4e95c7c6b
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567063"
---
# <a name="visualize-real-time-sensor-data-from-your-azure-iot-hub-in-a-web-application"></a>Az Azure IoT Hub valós idejű érzékelői adatainak vizualizációja egy webalkalmazásban

![Végpontok között diagram](./media/iot-hub-live-data-visualization-in-web-apps/1_iot-hub-end-to-end-diagram.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

Ebből a cikkből megtudhatja, hogyan vizualizálhatja az IoT Hub által kapott valós idejű érzékelőadatokat egy helyi számítógépen futó node.js-webalkalmazással. A webalkalmazás helyi futtatása után a lépéseket követve futtathatja a webalkalmazást a Azure App Service. Ha az IoT Hubban található adatokat az Power BI használatával szeretné vizualizálni, tekintse meg a Power BI használata az IoT Hub valós idejű érzékelői adatainak vizualizációját a [Azure IoT Hub.](iot-hub-live-data-visualization-in-power-bi.md)

## <a name="prerequisites"></a>Előfeltételek

* Töltse ki [a Raspberry Pi online szimulátor oktatóanyagát](iot-hub-raspberry-pi-web-simulator-get-started.md) vagy az eszköz egyik oktatóanyagát. A [Raspberry Pi-hoz ](iot-hub-raspberry-pi-kit-node-get-started.md) például használhatja a node.jsvagy a [Telemetria](quickstart-send-telemetry-dotnet.md) küldése rövid útmutatók valamelyikét. Ezek a cikkek a következő követelményeket fedik le:

  * Aktív Azure-előfizetés
  * Egy Iot Hub az előfizetése alatt
  * Egy ügyfélalkalmazás, amely üzeneteket küld az Iot Hubnak

* [A Git letöltése](https://www.git-scm.com/downloads)

* A cikkben található lépések Windows rendszerű fejlesztői gépet feltételeznek; Ezeket a lépéseket azonban könnyedén végrehajthatja egy Linux rendszeren a kívánt rendszerhéjban.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="add-a-consumer-group-to-your-iot-hub"></a>Fogyasztói csoport hozzáadása az IoT Hubhoz

[A fogyasztói csoportok](../event-hubs/event-hubs-features.md#event-consumers) független nézeteket biztosítanak az eseménystreambe, amelyek lehetővé teszik az alkalmazások és az Azure-szolgáltatások számára, hogy függetlenül használják fel az ugyanattól az eseményközpont-végponttól származó adatokat. Ebben a szakaszban egy fogyasztói csoportot ad hozzá az IoT Hub beépített végpontjához, amelyből a webalkalmazás adatokat fog olvasni.

A következő parancs futtatásával adjon hozzá egy fogyasztói csoportot az IoT Hub beépített végponthoz:

```azurecli-interactive
az iot hub consumer-group create --hub-name YourIoTHubName --name YourConsumerGroupName
```

Jegyezze fel a választott nevet, mert később szüksége lesz rá az oktatóanyagban.

## <a name="get-a-service-connection-string-for-your-iot-hub"></a>Szolgáltatáskapcsolati sztring lekérte az IoT Hubhoz

Az IoT Hubok több alapértelmezett hozzáférési szabályzatot is létrehoznak. Az egyik ilyen  szabályzat a szolgáltatás szabályzata, amely megfelelő engedélyeket biztosít a szolgáltatás számára az IoT Hub végpontjainak olvasásához és írásához. Futtassa a következő parancsot, hogy lekért egy kapcsolati sztringet az IoT Hubhoz, amely megfelel a szolgáltatási szabályzatnak:

```azurecli-interactive
az iot hub show-connection-string --hub-name YourIotHub --policy-name service
```

A kapcsolati sztringnek az alábbihoz hasonlóan kell kinéznie:

```javascript
"HostName={YourIotHubName}.azure-devices.net;SharedAccessKeyName=service;SharedAccessKey={YourSharedAccessKey}"
```

Jegyezze fel a szolgáltatás kapcsolati sztringet, mert később szüksége lesz rá az oktatóanyagban.

## <a name="download-the-web-app-from-github"></a>A webalkalmazás letöltése a GitHubról

Nyisson meg egy parancsablakot, és írja be a következő parancsokat a minta GitHubról való letöltéséhez és a mintakönyvtárra való váltáshoz:

```cmd
git clone https://github.com/Azure-Samples/web-apps-node-iot-hub-data-visualization.git
cd web-apps-node-iot-hub-data-visualization
```

## <a name="examine-the-web-app-code"></a>A webalkalmazás kódának vizsgálata

A web-apps-node-iot-hub-data-visualization könyvtárban nyissa meg a webalkalmazást a kedvenc szerkesztőjével. Az alábbiakban a VS Code-ban megtekintett fájlstruktúra látható:

![Webalkalmazás fájlstruktúrája](./media/iot-hub-live-data-visualization-in-web-apps/web-app-files.png)

Vizsgálja meg a következő fájlokat:

* **Server.js** egy szolgáltatásoldali szkript, amely inicializálja a webes szoftvercsatornát és az Eseményközpont burkoló osztályát. Visszahívási lehetőséget biztosít az eseményközpont burkoló osztályához, amely segítségével az osztály a bejövő üzeneteket a webes szoftvercsatornára közvetíti.

* **Event-hub-reader.js** egy szolgáltatásoldali szkript, amely a megadott kapcsolati sztring és fogyasztói csoport használatával csatlakozik az IoT Hub beépített végpontjára. Kinyeri a DeviceId és az EnqueuedTimeUtc metódust a bejövő üzenetek metaadataiból, majd továbbítja az üzenetet az server.js.

* **Chart-device-data.js** egy ügyféloldali szkript, amely a webes szoftvercsatornán figyel, nyomon követi az egyes DeviceId-eket, és minden eszköz utolsó 50 bejövő adatpontját tárolja. Ezután a kijelölt eszközadatokat a diagramobjektumhoz köti.

* **Index.html kezeli** a weboldal felhasználói felületi elrendezését, és hivatkozik az ügyféloldali logikához szükséges szkriptre.

## <a name="configure-environment-variables-for-the-web-app"></a>Környezeti változók konfigurálása a webalkalmazáshoz

Ahhoz, hogy adatokat olvasson az IoT Hubról, a webalkalmazásnak szüksége van az IoT Hub kapcsolati sztringjére és annak a fogyasztói csoportnak a nevére, amelyről olvasnia kell. Ezeket a sztringeket a folyamatkörnyezetből a következő sorokban kapja meg a server.js:

```javascript
const iotHubConnectionString = process.env.IotHubConnectionString;
const eventHubConsumerGroup = process.env.EventHubConsumerGroup;
```

Állítsa be a környezeti változókat a parancsablakban az alábbi parancsokkal. Cserélje le a helyőrző értékeket az IoT Hub szolgáltatáskapcsolati sztringjére és a korábban létrehozott fogyasztói csoport nevére. Ne idézzük a sztringeket.

```cmd
set IotHubConnectionString=YourIoTHubConnectionString
set EventHubConsumerGroup=YourConsumerGroupName
```

## <a name="run-the-web-app"></a>A webalkalmazás futtatása

1. Győződjön meg arról, hogy az eszköz fut, és adatokat küld.

2. A parancsablakban futtassa a következő sorokat a hivatkozott csomagok letöltéséhez és telepítéséhez, majd indítsa el a webhelyet:

   ```cmd
   npm install
   npm start
   ```

3. A konzolon a kimenetnek kell látsza, amely azt jelzi, hogy a webalkalmazás sikeresen csatlakozott az IoT Hubhoz, és a 3000-es porton figyel:

   ![Webalkalmazás elindítva a konzolon](./media/iot-hub-live-data-visualization-in-web-apps/web-app-console-start.png)

## <a name="open-a-web-page-to-see-data-from-your-iot-hub"></a>Weblap megnyitása az IoT Hubról származó adatokhoz

Nyisson meg egy böngészőt a `http://localhost:3000` fájlban.

Az Eszköz **kiválasztása listában** válassza ki az eszközt az eszköz által az IoT Hubnak küldött utolsó 50 hőmérséklet- és páratartalom-adatpont futó ábrázolásaként.

![A webalkalmazás valós idejű hőmérsékletét és páratartalmát megjelenítő oldal](./media/iot-hub-live-data-visualization-in-web-apps/web-page-output.png)

A konzolon a kimenetnek is meg kell lennie, amely azokat az üzeneteket jeleníti meg, amelyek a webalkalmazás által a böngésző ügyfelének küldve megjelenik:  

![Webalkalmazás szórási kimenete a konzolon](./media/iot-hub-live-data-visualization-in-web-apps/web-app-console-broadcast.png)

## <a name="host-the-web-app-in-app-service"></a>A webalkalmazást a App Service

A [Web Apps szolgáltatás Azure App Service](../app-service/overview.md) platformszolgáltatást (PAAS) biztosít a webalkalmazások üzemeltetéséhez. A Azure App Service-ban üzemeltetett webalkalmazások olyan hatékony Azure-funkciókat használhatnak, mint a további biztonság, a terheléselosztás és a méretezhetőség, valamint az Azure- és partner DevOps-megoldások, például a folyamatos üzembe helyezés, a csomagkezelés stb. Azure App Service számos népszerű nyelven fejlesztett, Windows- vagy Linux-infrastruktúrán üzembe helyezett webalkalmazást támogat.

Ebben a szakaszban üzembe helyez egy webalkalmazást a App Service és üzembe helyezheti rajta a kódot Azure CLI-parancsokkal. Az az webapp dokumentációjában megtalálja a használt [parancsok részleteit.](/cli/azure/webapp) Mielőtt hozzá szeretne menni, győződjön meg arról, hogy befejezte az erőforráscsoport hozzáadásának lépéseit az [IoT Hubhoz,](#add-a-consumer-group-to-your-iot-hub)le kell töltenie egy szolgáltatáskapcsolati sztringet az [IoT Hubhoz,](#get-a-service-connection-string-for-your-iot-hub)és le kell töltenie a webalkalmazást a [GitHubról.](#download-the-web-app-from-github)

1. A [App Service csomag](../app-service/overview-hosting-plans.md) számítási erőforrások készletét határozza meg egy, a App Service üzemeltetett alkalmazás számára. Ebben az oktatóanyagban a Fejlesztői/Ingyenes szintet használjuk a webalkalmazáshoz. Az Ingyenes szinten a webalkalmazás megosztott Windows-erőforrásokon fut más App Service alkalmazásokkal, beleértve más ügyfelek alkalmazásait is. Az Azure emellett App Service webalkalmazások Linux számítási erőforrásokon való üzembe helyezését is. Ezt a lépést kihagyhatja, ha már rendelkezik App Service használni kívánt tervvel.

   Az ingyenes Windows App Service csomag létrehozásához futtassa a következő parancsot. Használja ugyanazt az erőforráscsoportot, mint az IoT Hub. A szolgáltatásterv neve kis- és nagybetűket, számokat és kötőjeleket tartalmazhat.

   ```azurecli-interactive
   az appservice plan create --name <app service plan name> --resource-group <your resource group name> --sku FREE
   ```

2. Most pedig üzembe kell App Service webalkalmazást. A paraméter lehetővé teszi a webalkalmazás-kód feltöltését és üzembe helyezését a helyi gépen található `--deployment-local-git` Git-adattárból. A webalkalmazás nevének globálisan egyedinek kell lennie, és kis- és nagybetűket, számokat és kötőjeleket tartalmazhat. A paraméterhez a Node 10.6-os vagy újabb verzióját adja meg, a használt Node.js `--runtime` verziójától függően. Az paranccsal `az webapp list-runtimes` lekért lista a támogatott futásidőkről.

   ```azurecli-interactive
   az webapp create -n <your web app name> -g <your resource group name> -p <your app service plan name> --runtime "node|10.6" --deployment-local-git
   ```

3. Most adja hozzá az Alkalmazásbeállításokat a környezeti változókhoz, amelyek meghatározzák az IoT Hub kapcsolati sztringet és az Eseményközpont fogyasztói csoportját. Az egyes beállítások a paraméterben szóközrel vannak `-settings` elválasztva. Használja az IoT Hub és az oktatóanyagban korábban létrehozott fogyasztói csoport szolgáltatáskapcsolati sztringet. Ne idézzük az értékeket.

   ```azurecli-interactive
   az webapp config appsettings set -n <your web app name> -g <your resource group name> --settings EventHubConsumerGroup=<your consumer group> IotHubConnectionString="<your IoT hub connection string>"
   ```

4. Engedélyezze a Web Sockets protokollt a webalkalmazáshoz, és állítsa be, hogy a webalkalmazás csak HTTPS-kéréseket fogad(a HTTP-kérések a HTTPS-re vannak átirányítva).

   ```azurecli-interactive
   az webapp config set -n <your web app name> -g <your resource group name> --web-sockets-enabled true
   az webapp update -n <your web app name> -g <your resource group name> --https-only true
   ```

5. A kód üzembe helyezéséhez App Service felhasználói szintű üzembe helyezési hitelesítő [adatait fogja használni.](../app-service/deploy-configure-credentials.md) A felhasználói szintű üzembe helyezési hitelesítő adatok eltérnek az Azure hitelesítő adataitól, és helyi Git- és FTP-példányok webalkalmazásba történő üzembe helyezéséhez használatosak. Ha be van állítva, érvényesek az összes App Service alkalmazásra az Azure-fiókjában lévő összes előfizetésben. Ha korábban már beállított felhasználói szintű üzembe helyezési hitelesítő adatokat, használhatja őket.

   Ha korábban még nem adott meg felhasználói szintű üzembe helyezési hitelesítő adatokat, vagy nem emlékszik a jelszavára, futtassa a következő parancsot. Az üzemelő példány felhasználónevének egyedinek kell lennie az Azure-ban, és nem tartalmazhatja a helyi \@ Git-leküldések "" szimbólumát. Amikor a rendszer kéri, adja meg és erősítse meg az új jelszót. A jelszónak legalább nyolc karakter hosszúságúnak kell lennie, és a következő három elemből kettőnek kell lennie: betűk, számok és szimbólumok.

   ```azurecli-interactive
   az webapp deployment user set --user-name <your deployment user name>
   ```

6. Szerezze be a git URL-címet, amely a kód felküldéséhez használható App Service.

   ```azurecli-interactive
   az webapp deployment source config-local-git -n <your web app name> -g <your resource group name>
   ```

7. Adjon hozzá egy távolit a klónhoz, amely a git-adattárra hivatkozik a webalkalmazáshoz a App Service. A \<Git clone URL\> fájlhoz használja az előző lépésben visszaadott URL-címet. Futtassa a következő parancsot a parancsablakban.

   ```cmd
   git remote add webapp <Git clone URL>
   ```

8. A kód üzembe helyezéséhez App Service a következő parancsot a parancsablakban. Ha a rendszer hitelesítő adatokat kér, adja meg az 5. lépésben létrehozott felhasználói szintű üzembe helyezési hitelesítő adatokat. Győződjön meg arról, hogy a rendszer a távoli ágra App Service le.

    ```cmd
    git push webapp main:main
    ```

9. Az üzembe helyezés állapota a parancsablakban frissül. A sikeres üzembe helyezés az alábbi kimenethez hasonló sorokkal fog végződni:

    ```cmd
    remote:
    remote: Finished successfully.
    remote: Running post deployment command(s)...
    remote: Deployment successful.
    To https://contoso-web-app-3.scm.azurewebsites.net/contoso-web-app-3.git
    6b132dd..7cbc994  main -> main
    ```

10. Futtassa a következő parancsot a webalkalmazás állapotának lekérdezéséhez, és ellenőrizze, hogy fut-e:

    ```azurecli-interactive
    az webapp show -n <your web app name> -g <your resource group name> --query state
    ```

11. Egy böngészőben nyissa meg a `https://<your web app name>.azurewebsites.net` oldalt. A webalkalmazás helyi futtatáskor látotthoz hasonló weblap jelenik meg. Feltételezve, hogy az eszköz fut és adatokat küld, az eszköz által küldött 50 legújabb hőmérséklet- és páratartalom-érték futó ábrázolása látható.

## <a name="troubleshooting"></a>Hibaelhárítás

Ha bármilyen probléma lép fel a mintával kapcsolatban, próbálkozzon a következő szakaszokban található lépésekkel. Ha továbbra is problémákat tapasztal, küldjön visszajelzést a témakör alján.

### <a name="client-issues"></a>Ügyféllel kapcsolatos problémák

* Ha egy eszköz nem jelenik meg a listában, vagy nem rajzol gráfot, ellenőrizze, hogy az eszköz kódja fut-e az eszközön.

* Nyissa meg a böngészőben a fejlesztői eszközöket (sok böngészőben megnyílik az F12 billentyű), és keresse meg a konzolt. Keresse meg az ott kinyomtatott figyelmeztetéseket vagy hibákat.

* Az ügyféloldali szkriptek hibakeresését a /js/chat-device-data.js.

### <a name="local-website-issues"></a>Helyi webhelyekkel kapcsolatos problémák

* Figyelje meg a kimenetet abban az ablakban, amelyben elindította a csomópont konzolkimenetét.

* Hibakeresés a kiszolgálói kódban, különösen a server.js és a /scripts/event-hub-reader.js.

### <a name="azure-app-service-issues"></a>Azure App Service problémák

* A Azure Portal a webalkalmazást. A **bal oldali** panel Figyelés panelen válassza a **Naplók App Service lehetőséget.** Kapcsolja **Application Logging (fájlrendszer) lehetőséget,** állítsa a **Szint** beállítását Hibaértékre, majd válassza a **Mentés lehetőséget.** Ezután nyissa meg **a Naplóstreamet** (a **Figyelés alatt).**

* A webalkalmazásból a Azure Portal **eszközök területén**  válassza a Konzol lehetőséget, és ellenőrizze a csomópont- és npm-verziókat a és `node -v` a `npm -v` használatával.

* Ha hibaüzenetet kap arról, hogy nem található csomag, akkor előfordulhat, hogy a lépéseket nem sorrendben futtatta. A hely telepítésekor (a használatával) az App Service a által konfigurált csomópont aktuális verziójának megfelelően `git push` `npm install` fut. Ha ezt később módosítja a konfigurációban, akkor érthetetlenül módosítania kell a kódot, és újra le kell majdküldést eszközolnia.

## <a name="next-steps"></a>Következő lépések

Sikeresen használta a webalkalmazást az IoT Hub valós idejű érzékelői adatainak megjelenítésére.

A vizualizáció másik módja a Azure IoT Hub adatok vizualizációja: Power BI az IoT Hub valós idejű érzékelői [adatainak vizualizációja.](iot-hub-live-data-visualization-in-power-bi.md)

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
