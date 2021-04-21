---
title: Adatok átalakítása Azure IoT Central | Microsoft Docs
description: Az IoT-eszközök különböző formátumokban küldenek adatokat, amelyek átalakítására lehet szükség. Ez a cikk azt ismerteti, hogyan alakítható át az adatok IoT Central és kifelé is. A leírt forgatókönyvek a következő IoT Edge és Azure Functions.
author: dominicbetts
ms.author: dobett
ms.date: 04/09/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 6032300bd203db78e8cd147cf79300d6dcd9b1dc
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751687"
---
# <a name="transform-data-for-iot-central"></a>Adatok átalakítása IoT Central

*Ez a témakör a megoldáskészítőkre vonatkozik.*

Az IoT-eszközök különböző formátumokban küldenek adatokat. Az eszközadatoknak a IoT Central alkalmazással való használhatja, előfordulhat, hogy átalakítást kell használnia a következőre:

- Az adatok formátumának kompatibilisnek kell lennie a IoT Central alkalmazással.
- Egységek konvertálása.
- Új metrikák kiszámítása.
- Más forrásokból származó adatok gazdagítása.

Ez a cikk bemutatja, hogyan alakíthatja át az eszközadatokat a IoT Central a bejövő vagy a ki- és be- vagy kifelé.

Az alábbi ábrán három útvonal látható az átalakításokat is magukban foglaló adatokhoz:

:::image type="content" source="media/howto-transform-data/transform-data.png" alt-text="Az adatátalakítási útvonalak összegzése a bejövő és a bejövő forgalom esetében is" border="false":::

Az alábbi táblázat három átalakítási típust mutat be:

| Átalakítás | Leírás | Példa  | Megjegyzések |
|------------------------|-------------|----------|-------|
| Üzenetformátum         | Konvertáljon JSON-üzenetekre, vagy manipulálja őket. | CSV–JSON  | Bejövő forgalomnál. IoT Central csak érték JSON-üzeneteket fogad el. További információ: [Telemetria,](concepts-telemetry-properties-commands.md)tulajdonság és hasznos parancsok. |
| Számítások           | Matematikai [függvények, Azure Functions](../../azure-functions/index.yml) végrehajthatók. | Egységkonverzió Fahrenheitről Celsius-fokra.  | Átalakítás a kigressziós mintával, hogy kihasználja a skálázható eszközbegressziót a bejövő forgalom közvetlen kapcsolódásával a IoT Central. Az adatok átalakításával olyan funkciókat IoT Central, mint a vizualizációk és a feladatok. |
| Üzenet gazdagítása     | Az eszköztulajdonságokban vagy a telemetriában nem található külső adatforrások bővítődményei. További információ a belső fejlesztésekről: IoT-adatok exportálása [felhőbeli célhelyre adatexportációval](howto-export-data.md) | Időjárási adatok hozzáadása az üzenetekhez az eszközöktől származó helyadatok használatával. | Átalakítás a kigressziós mintával, hogy kihasználja a skálázható eszközbegressziót a bejövő forgalom közvetlen kapcsolódásával a IoT Central. |

## <a name="prerequisites"></a>Előfeltételek

A cikkben található lépések befejezéséhez aktív Azure-előfizetésre van szükség. Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

A megoldás beállításához szüksége lesz egy IoT Central alkalmazásra. További információ a IoT Central létrehozásáról: Create an Azure IoT Central application (Új alkalmazás [létrehozása).](quick-deploy-iot-central.md)

## <a name="data-transformation-at-ingress"></a>Adatátalakítás bejövő forgalomnál

Az eszközadatok bejövő forgalommal való átalakítására két lehetőség áll rendelkezésre:

- **IoT Edge:** Egy IoT Edge modullal átalakíthatja az adatokat az lefelé irányuló eszközökről, mielőtt elküldné az adatokat a IoT Central alkalmazásnak.

- **IoT Central** híd: Az IoT Central-eszközhíd más IoT-eszközfelhőket, például [a](howto-build-iotc-device-bridge.md) Sigfoxot, a Részecskéket és a The Things Networket csatlakoztatja a IoT Central. Az eszközhíd egy Azure-függvény használatával továbbítja az adatokat, és testreszabhatja a függvényt az eszközadatok átalakításához.

### <a name="use-iot-edge-to-transform-device-data"></a>Eszközadatok IoT Edge eszközadatok átalakítása a IoT Edge használatával

:::image type="content" source="media/howto-transform-data/transform-ingress.png" alt-text="Adatátalakítás bejövő forgalom esetén IoT Edge" border="false":::

Ebben a forgatókönyvben egy IoT Edge modul átalakítja az adatokat az lefelé irányuló eszközökről, mielőtt továbbítja őket az IoT Central alkalmazásba. A forgatókönyv konfigurálásának lépései magas szinten a következőek:

1. **Átjáróeszköz IoT Edge:** Telepítsen és helyezzen üzembe egy IoT Edge-eszközt átjáróként, és csatlakoztassa az átjárót a IoT Central alkalmazásához.

1. **Az lefelé irányuló eszköz csatlakoztatása a IoT Edge eszközhöz:** Csatlakoztassa az lefelé irányuló eszközöket a IoT Edge eszközhöz, és kiépítse őket a IoT Central számára.

1. **Eszközadatok átalakítása a IoT Edge:** Hozzon létre egy IoT Edge modult az adatok átalakításához. Telepítse a modult a IoT Edge átjáróeszközre, amely továbbítja az átalakított eszközadatokat a IoT Central számára.

1. **Ellenőrzés:** Adatokat küldhet egy lefelé irányuló eszközről az átjárónak, és ellenőrizheti, hogy az átalakított eszközadatok elérik-e IoT Central alkalmazást.

A következő szakaszokban leírt példában az lefelé irányuló eszköz a következő formátumban küld CSV-adatokat a IoT Edge átjáróeszköznek:

```csv
"<temperature >, <pressure>, <humidity>"
```

Egy új modullal IoT Edge át az adatokat a következő JSON formátumba, mielőtt a rendszer elküldné őket a IoT Central:

```json
{
  "device": {
      "deviceId": "<downstream-deviceid>"
  },
  "measurements": {
    "temp": <temperature>,
    "pressure": <pressure>,
    "humidity": <humidity>,
  }
}
```

A következő lépések a forgatókönyv beállítását és konfigurálését mutatják be:

### <a name="build-the-custom-module"></a>Az egyéni modul létrehozása

Ebben a forgatókönyvben a IoT Edge egy egyéni modult futtat, amely átalakítja az adatokat az lefelé irányuló eszközről. Mielőtt üzembe helyezné és konfigurálja IoT Edge eszközét, a következőt kell beállítania:

- Az egyéni modul létrehozása.
- Adja hozzá az egyéni modult egy tároló-beállításjegyzékhez.

A IoT Edge-modul egyéni modulokat tölt le egy tároló-beállításjegyzékből, például egy Azure Container Registryből vagy egy Docker Hub. A [Azure Cloud Shell](../../cloud-shell/overview.md) rendelkezik a tároló-beállításjegyzék létrehozásához, a modul létrehozásához és a modulnak a regisztrációs adatbázisba való feltöltéséhez szükséges összes eszközzel:

Tároló-beállításjegyzék létrehozása:

1. Nyissa meg [Azure Cloud Shell,](https://shell.azure.com/) és jelentkezzen be Az Azure-előfizetésbe.

1. Futtassa a következő parancsokat egy Azure Container Registry létrehozásához:

    ```azurecli
    REGISTRY_NAME="{your unique container registry name}"
    az group create --name ingress-scenario --location eastus
    az acr create -n $REGISTRY_NAME -g ingress-scenario --sku Standard --admin-enabled true
    az acr credential show -n $REGISTRY_NAME
    ```

    Jegyezze fel a `username` és `password` a értéket, mert később még használnia kell őket.

Az egyéni modul létrehozása a [Azure Cloud Shell:](https://shell.azure.com/)

1. A [Azure Cloud Shell](https://shell.azure.com/)keresse meg a megfelelő mappát.
1. A modul forráskódját tartalmazó GitHub-adattár klónozáshoz futtassa a következő parancsot:

    ```azurecli
    git clone https://github.com/iot-for-all/iot-central-transform-with-iot-edge
    ```

1. Az egyéni modul létrehozásához futtassa a következő parancsokat a Azure Cloud Shell:

    ```azurecli
    cd iot-central-transform-with-iot-edge/custommodule/transformmodule
    az acr build --registry $REGISTRY_NAME --image transformmodule:0.0.1-amd64 -f Dockerfile.amd64 .
    ```

    Az előző parancsok futtatása több percig is eltarthat.

### <a name="set-up-an-iot-edge-device"></a>Eszköz IoT Edge beállítása

Ez a forgatókönyv egy IoT Edge átjáróeszköz segítségével alakítja át az adatokat bármely lefelé irányuló eszközről. Ez a szakasz azt ismerteti, hogyan hozhat létre IoT Central eszközsablonokat az átjáróhoz és a lefelé irányuló eszközökhöz a IoT Central alkalmazásban. IoT Edge-eszközök üzembe helyezési jegyzék használatával konfigurálják a modulokat.

Ha eszközsablont hoz létre az lefelé irányuló eszközhöz, ez a forgatókönyv egy egyszerű termosztátos eszközmodellt használ:

1. Töltse le [a termosztát eszköz modelljét](https://raw.githubusercontent.com/Azure/iot-plugandplay-models/main/dtmi/com/example/thermostat-2.json) a helyi gépre.

1. Jelentkezzen be a IoT Central alkalmazásba, és lépjen az **Eszközsablonok oldalra.**

1. Válassza **az + Új** lehetőséget, válassza az **IoT-eszköz** lehetőséget, majd a **Tovább: Testreszabás lehetőséget.**

1. Adja *meg a Termosztát* nevet a sablon neveként, majd válassza a **Tovább: Áttekintés lehetőséget.** Ezután kattintson a **Létrehozás** elemre.

1. Válassza **a Modell importálása lehetőséget,** és *importáljathermostat-2.jskorábban* letöltött fájlban található adatokat.

1. Az **új eszközsablon** közzétételéhez válassza a Közzététel lehetőséget.

Eszközsablon létrehozása a IoT Edge eszközhöz:

1. Mentse az üzembe helyezési jegyzék másolatát a helyi fejlesztői gépen: kattintson a [moduledeployment.jsgombra.](https://raw.githubusercontent.com/iot-for-all/iot-central-transform-with-iot-edge/main/edgemodule/moduledeployment.json)

1. Nyissa meg a fájl helyi *moduledeployment.jsa jegyzékfájlban* egy szövegszerkesztőben.

1. Keresse meg a szakaszt, és cserélje le a helyőrzőket azOkkal az értékekkel, amelyekre az `registryCredentials` Azure Container Registry létrehozásakor fel lett jegyezve. Az `address` érték így néz `<username>.azurecr.io` ki: .

1. Keresse meg `settings` a `transformmodule` szakaszát. Cserélje `<acr or docker repo>` le a helyére az előző lépésben használt `address` értéket. Mentse a módosításokat.

1. A saját IoT Central nyissa meg az **Eszközsablonok** lapot.

1. Válassza **az + Új** lehetőséget, válassza a **Azure IoT Edge,** majd a **Tovább: Testreszabás lehetőséget.**

1. Adja *IoT Edge az átjáróeszközt* az eszközsablon neveként. Válassza **az Ez egy átjáróeszköz lehetőséget.** A **Tallózás gombra** kattintvamoduledeployment.jsa korábban *szerkesztett* üzembe helyezési jegyzékfájlhoz szükséges adatokat.

1. Az üzembe helyezési jegyzék érvényesítése után válassza a **Tovább: Áttekintés** lehetőséget, majd a **Létrehozás lehetőséget.**

1. A **Modell alatt** válassza a Kapcsolatok **lehetőséget.** Válassza **a + Kapcsolat hozzáadása lehetőséget.** A *megjelenítendő névként adja* meg az Lefelé irányuló eszköz nevet, célként pedig válassza a **Termosztát** lehetőséget. Kattintson a **Mentés** gombra.

1. Az **eszközsablon közzétételéhez** válassza a Közzététel lehetőséget.

Most már két eszközsablon található a IoT Central alkalmazásban. Az **IoT Edge átjáróeszköz sablonját,** a **Termosztát** sablont pedig lefelé irányuló eszközként.

Átjáróeszköz regisztrálása a IoT Central:

1. A saját IoT Central nyissa meg az Eszközök **lapot.**

1. Válassza **IoT Edge átjáróeszközt,** majd az **Eszköz létrehozása lehetőséget.** Adja *IoT Edge az* átjáróeszközt az eszköz neveként, adja meg a  *gateway-01* nevet az eszköz azonosítójaként, és győződjön meg arról, hogy IoT Edge átjáróeszköz van kiválasztva az eszközsablonként. Válassza a **Létrehozás** lehetőséget.

1. Az eszközök listájában kattintson a IoT Edge **átjáróeszközre,** majd válassza a **Csatlakozás lehetőséget.**

1. Jegyezze fel az átjáróeszköz azonosító-hatókörének, eszközazonosítójának **és** **elsődleges kulcsának IoT Edge értékét.**   Később még használnia kell őket.

Lefelé irányuló eszköz regisztrálása a IoT Central:

1. A saját IoT Central nyissa meg az Eszközök **lapot.**

1. Válassza **a Termosztát,** majd **az Eszköz létrehozása lehetőséget.** Adja *meg a Termosztát* nevet az eszköz neveként, az eszköz azonosítójaként pedig *a downstream-01* nevet, és győződjön meg arról, hogy eszközsablonként a **Termosztát** van kiválasztva. Válassza a **Létrehozás** lehetőséget.

1. Az eszközök listájában válassza a **Termosztát,** majd a **Csatolás átjáróhoz lehetőséget.** Válassza ki **IoT Edge átjáróeszköz sablonját** és a **IoT Edge-eszközpéldányt.** Válassza a **Csatolás lehetőséget.**

1. Az eszközök listájában kattintson a **Termosztát** elemre, majd válassza a **Csatlakozás lehetőséget.**

1. Jegyezze fel a Termosztát eszköz Azonosító **hatóköre,** **Eszközazonosító** és **Elsődleges** **kulcs értékeit.** Később még használnia kell őket.

### <a name="deploy-the-gateway-and-downstream-devices"></a>Az átjáró és a lefelé irányuló eszközök üzembe helyezése

Az egyszerűség kedvéért ez a cikk Azure-beli virtuális gépeket használ az átjáró és a lefelé irányuló eszközök futtatásához. A két Azure-beli virtuális gép létrehozásához válassza az alábbi Üzembe helyezés az **Azure-ban** gombot, és az alábbi táblázatban található információk alapján töltse ki az Egyéni üzembe **helyezés űrlapot:**

| Mező | Érték |
| ----- | ----- |
| Erőforráscsoport | `ingress-scenario` |
| DNS-címkeelőtag-átjáró | A gép egyedi DNS-neve, például `<your name>edgegateway` |
| Lefelé irányuló DNS-címkeelőtag | A gép egyedi DNS-neve, például: `<your name>downstream` |
| Hatókör-azonosító | Az azonosító hatóköre, amelyet korábban feljegyett |
| Eszközazonosító IoT Edge Átjáróhoz | `gateway-01` |
| Eszközkulcs IoT Edge átjáróhoz | Az elsődleges kulcs értéke, amelyet korábban feljegyett |
| Hitelesítés típusa | Jelszó |
| Rendszergazdai jelszó vagy kulcs | Az Ön által választott jelszó az **AzureUser-fiókhoz** mindkét virtuális gépen. |

<a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fiot-central-docs-samples%2Fmaster%2Ftransparent-gateway%2FDeployGatewayVMs.json" target="_blank">
    <img src="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png" alt="Deploy to Azure button" />
</a>

Válassza **az Áttekintés + létrehozás,** majd a Létrehozás **lehetőséget.** A virtuális gépek létrehozása a bejövő forgalom **erőforráscsoportban** néhány percet vesz igénybe.

Annak ellenőrzése, hogy IoT Edge eszköz megfelelően fut-e:

1. Nyissa meg IoT Central alkalmazást. Ezután lépjen a **IoT Edge átjáróeszközre** az Eszközök lapon az eszközök **listájában.**

1. Válassza a **Modulok** lapot, és ellenőrizze a három modul állapotát. A virtuális gép IoT Edge néhány percet vesz igénybe. Az első lépésekben a három modul állapota **Fut.** Ha a IoT Edge futásidejű rendszer nem indul el, tekintse meg a következő témakört: A IoT Edge [hibaelhárítása.](../../iot-edge/troubleshoot.md)

Ahhoz, IoT Edge eszköz átjáróként működn, tanúsítványra van szüksége, hogy igazolja az identitását az összes lefelé irányuló eszközön. Ez a cikk bemutató tanúsítványokat használ. Éles környezetben használja a hitelesítésszolgáltatótól származó tanúsítványokat.

A bemutató tanúsítványok létrehozásához és az átjáróeszközön való telepítéséhez:

1. Csatlakozzon az SSH-hoz, és jelentkezzen be az átjáróeszköz virtuális gépére. A virtuális gép DNS-nevét a következő Azure Portal. Keresse meg **az edgegateway virtuális** gépet a bejövő forgalom **erőforráscsoportban.**

    > [!TIP]
    > Előfordulhat, hogy mindkét virtuális gépen meg kell nyitnia a 22-es portot az SSH-hozzáféréshez, mielőtt SSH-kapcsolattal csatlakozhat a helyi gépről vagy a Azure Cloud Shell.

1. A következő parancsok futtatásával klónozza a IoT Edge tárházat, és hozza létre a bemutató tanúsítványokat:

    ```bash
    # Clone the repo
    cd ~
    git clone https://github.com/Azure/iotedge.git

    # Generate the demo certificates
    mkdir certs
    cd certs
    cp ~/iotedge/tools/CACertificates/*.cnf .
    cp ~/iotedge/tools/CACertificates/certGen.sh .
    ./certGen.sh create_root_and_intermediate
    ./certGen.sh create_edge_device_ca_certificate "mycacert"
    ```

    Az előző parancsok futtatása után a következő fájlok készen állnak a következő lépésekben való használatra:

    - *~/certs/certs/azure-iot-test-only.root.ca.cert.pem* – Az összes többi bemutatótanúsítványhoz használt legfelső szintű hitelesítésszolgáltatói tanúsítvány IoT Edge forgatókönyv teszteléséhez.
    - *~/certs/certs/iot-edge-device-mycacert-full-chain.cert.pem* – A *config.yaml* fájlból hivatkozott eszköz ca tanúsítványa. Átjáróforgatókönyvben ez a hitelesítésszolgáltatói tanúsítvány az, IoT Edge eszköz ellenőrzi az identitását az lefelé irányuló eszközökön.
    - *~/certs/private/iot-edge-device-mycacert.key.pem* – Az eszköz hitelesítésszolgáltatói tanúsítványával társított titkos kulcs.

    További információ ezekről a bemutató tanúsítványokról: Bemutató tanúsítványok létrehozása az [IoT Edge teszteléséhez.](../../iot-edge/how-to-create-test-certificates.md)

1. Nyissa meg a *config.yaml fájlt* egy szövegszerkesztőben. Például:

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

1. Keresse meg a `Certificate settings` beállításokat. A tanúsítványbeállításokat a következőképpen módosíthatja:

    ```text
    certificates:
      device_ca_cert: "file:///home/AzureUser/certs/certs/iot-edge-device-ca-mycacert-full-chain.cert.pem"
      device_ca_pk: "file:///home/AzureUser/certs/private/iot-edge-device-ca-mycacert.key.pem"
      trusted_ca_certs: "file:///home/AzureUser/certs/certs/azure-iot-test-only.root.ca.cert.pem"
    ```

    A fenti példa feltételezi, hogy **AzureUser-ként** jelentkezett be, és létrehozott egy "mycacert" nevű eszköztanúsítványt.

1. Mentse a módosításokat, és indítsa újra IoT Edge futásidejű számítógépet:

    ```bash
    sudo systemctl restart iotedge
    ```

Ha a IoT Edge futásidejű modul sikeresen elindul a módosítások  után, a $edgeAgent és a $edgeHub **futó** állapotúra **változik.** Ezeket az állapotértékeket  az átjáróeszköz Modulok lapján láthatja a IoT Central.

Ha a futásidő nem indul el, ellenőrizze a *config.yaml* fájlban végrehajtott módosításokat, és tekintse meg a következő témakört: A IoT Edge [hibaelhárítása.](../../iot-edge/troubleshoot.md)

### <a name="connect-downstream-device-to-iot-edge-device"></a>Lefelé irányuló eszköz csatlakoztatása IoT Edge eszközhöz

Lefelé irányuló eszköz csatlakoztatása a IoT Edge átjáróeszközhöz:

1. Csatlakozzon az SSH-hoz, és jelentkezzen be az lefelé irányuló eszköz virtuális gépére. A virtuális gép DNS-nevét a következő Azure Portal. Navigáljon a **leafdevice virtuális** géphez a **bejövő forgalom erőforráscsoportban.**

    > [!TIP]
    > Előfordulhat, hogy mindkét virtuális gépen meg kell nyitnia a 22-es portot az SSH-hozzáféréshez, mielőtt SSH-t használhat a helyi gépről vagy a Azure Cloud Shell.

1. A GitHub-adattárnak a lefelé irányuló mintaeszköz forráskódját tartalmazó klónozásához futtassa a következő parancsot:

    ```bash
    cd ~
    git clone https://github.com/iot-for-all/iot-central-transform-with-iot-edge
    ```

1. A szükséges tanúsítvány az átjáróeszközről való másolásához futtassa a következő `scp` parancsokat. Ez `scp` a parancs az állomásnév használatával azonosítja az `edgegateway` átjáróként használt virtuális gépet. A rendszer kérni fogja a jelszavát:

    ```bash
    cd ~/iot-central-transform-with-iot-edge
    scp AzureUser@edgegateway:/home/AzureUser/certs/certs/azure-iot-test-only.root.ca.cert.pem leafdevice/certs
    ```

1. Keresse meg *a leafdevice mappát,* és telepítse a szükséges csomagokat. Ezután futtassa a és a szkriptet az eszköz üzembe építéséhez és az `build` `start` átjáróhoz való csatlakoztatáshoz:

    ```bash
    cd ~/iot-central-transform-with-iot-edge/leafdevice
    sudo apt update
    sudo apt install nodejs npm node-typescript
    npm install
    npm run-script build
    npm run-script start
    ```

1. Adja meg a korábban létrehozott lefelé irányuló eszköz eszközazonosítóját, hatókör-azonosítóját és SAS-kulcsát. Az állomásnév mezőben adja meg a következőt: `edgegateway` . A parancs kimenete a következő:

    ```output
    Registering device downstream-01 with scope 0ne00284FD9
    Registered device downstream-01.
    Connecting device downstream-01
    Connected device downstream-01
    Sent telemetry for device downstream-01
    Sent telemetry for device downstream-01
    Sent telemetry for device downstream-01
    Sent telemetry for device downstream-01
    Sent telemetry for device downstream-01
    Sent telemetry for device downstream-01
    ```

### <a name="verify"></a>Ellenőrzés

A forgatókönyv futásának ellenőrzéséhez keresse meg IoT Edge **átjáróeszközét a** IoT Central:

:::image type="content" source="media/howto-transform-data/transformed-data.png" alt-text="Az átalakított adatokat az Eszközök lapon bemutató képernyőkép.":::

- Válassza a **Modulok lehetőséget.** Ellenőrizze, hogy fut-e IoT Edge **modul**$edgeAgent , **$edgeHub** **és transformmodule** modul.
- Válassza az **Lefelé irányuló eszközök lehetőséget,** és ellenőrizze, hogy az lefelé irányuló eszköz ki van-e építve.
- Válassza a **Nyers adatok lehetőséget.** A Nem módosított adatok **oszlopban** található telemetriaadatok a következőre hasonlítnak:

    ```json
    {"device":{"deviceId":"downstream-01"},"measurements":{"temperature":85.21208,"pressure":59.97321,"humidity":77.718124,"scale":"farenheit"}}
    ```

Mivel a IoT Edge eszköz átalakítja az adatokat az lefelé irányuló eszközről, a telemetria az átjáróeszközhöz lesz társítva a IoT Central. A telemetria megjelenítéséhez hozza létre a **IoT Edge-eszközsablon** új verzióját a telemetriatípusok definícióival.

## <a name="data-transformation-at-egress"></a>Adatátalakítás a bejövő forgalomnál

Csatlakoztathatja az eszközöket a IoT Central, exportálhatja az eszközadatokat egy számítási motorba az átalakításhoz, majd visszaküldheti az átalakított adatokat a IoT Central eszközkezeléshez és elemzéshez. Például:

- Az eszközök helyadatokat küldenek a IoT Central.
- IoT Central egy számítási motorba exportálja az adatokat, amely az időjárási adatokkal javítja a helyadatokat.
- A számítási motor visszaküldi a továbbfejlesztett adatokat a IoT Central.

Az eszközhíd [IoT Central használhatja](https://github.com/Azure/iotc-device-bridge) számítási motorként a gépről exportált adatok IoT Central.

A ki- és beülrőlt adatok átalakításának egyik előnye, hogy az eszközök közvetlenül csatlakoznak a IoT Central-hoz, ami megkönnyíti a parancsok eszközökre való elküldését vagy az eszköztulajdonságok frissítését. Ezzel a módszerrel azonban több üzenetet használhat, mint a havi lefizetés, és megnövelheti a számítási Azure IoT Central.

### <a name="use-the-iot-central-device-bridge-to-transform-device-data"></a>Eszközadatok IoT Central eszközhíd használatával

:::image type="content" source="media/howto-transform-data/transform-egress.png" alt-text="Adatátalakítás a bejövő forgalomon IoT Edge" border="false":::

Ebben a forgatókönyvben a számítási motor átalakítja a IoT Central exportált eszközadatokat, mielőtt visszaküldi őket a IoT Central alkalmazásnak. A forgatókönyv konfigurálásának lépései magas szinten a következőek:

1. **A számítási motor beállítása:** Hozzon létre IoT Central eszközhidat, amely az adatátalakítás számítási motorjaként működik.

1. **Eszközadatok átalakítása az eszközhídban:** Az eszközhídban található adatok átalakításához módosítsa az eszközhíd-függvény kódját az adatátalakítási esethez.

1. **Adatfolyam engedélyezése a IoT Central és az eszközhíd között:** Exportálja az adatokat a IoT Central eszközhídra átalakításhoz. Ezután továbbküldi az átalakított adatokat a IoT Central. Az adatexportál létrehozásakor üzenettulajdonság-szűrőkkel exportálja csak a nem átformált adatokat.

1. **Ellenőrzés:** Csatlakoztassa az eszközt a IoT Central alkalmazáshoz, és ellenőrizze a nyers eszközadatokat és az átalakított adatokat is a IoT Central.

<!-- To Do - doesn't the device send JSON data? -->
A következő szakaszokban leírt példában az eszköz a következő formátumban küld CSV-adatokat a IoT Edge átjáróeszköznek:

```csv
"<temperature in degrees C>, <humidity>, <latitude>, <longitude>"
```

Az eszközhidat az eszközadatok átalakítására használhatja a következővel:

- A hőmérséklet mértékegységének módosítása agrade-ről fahrenheitre.
- Az eszközadatok az [Open Weather](https://openweathermap.org/) szolgáltatásból a szélességi és hosszúsági értékekhez lekért időjárási adatokkal való gazdagítása.

Az eszközhíd ezután elküldi az átalakított adatokat IoT Central következő formátumban:

```json
{
  "temp": <temperature in degrees F>,
  "humidity": <humidity>,
  "lat": <latitude>,
  "lon": <logitude>,
  "weather": {
    "weather_temp": <temperature at lat/lon>,
    "weather_humidity": <humidity at lat/lon>,
    "weather_pressure": <pressure at lat/lon>,
    "weather_windspeed": <wind speed at lat/lon>,
    "weather_clouds": <cloud cover at lat/lon>,
    "weather_uvi": <UVI at lat/lon>
  }
}
```

A következő lépések a forgatókönyv beállítását és konfigurálését mutatják be:

### <a name="retrieve-your-iot-central-connection-settings"></a>A IoT Central beállításainak lekérése

A forgatókönyv beállítása előtt be kell szereznie néhány kapcsolati beállítást a IoT Central alkalmazásból:

1. Jelentkezzen be az IoT Central alkalmazásba.

1. Lépjen az **Adminisztráció > eszközkapcsolathoz.**

1. Jegyezze fel az azonosító **hatókörét.** Ezt az értéket később is használnia kell.

1. Válassza ki az **SaS-IoT-Devices** regisztrációs csoportot. Jegyezze fel a közös hozzáférésű jogosultság jogosultságának elsődleges kulcsát. Ezt az értéket később is használnia kell.

### <a name="set-up-a-compute-engine"></a>Számítási motor beállítása

Ez a forgatókönyv ugyanazt az üzembe helyezési Azure Functions használja, mint IoT Central eszközhíd. Az eszközhíd üzembe helyezéséhez válassza az alábbi Üzembe helyezés az **Azure-ban** gombot, és az alábbi táblázatban található információk segítségével töltse ki az Egyéni üzembe **helyezés űrlapot:**

| Mező | Érték |
| ----- | ----- |
| Erőforráscsoport | Hozzon létre egy új erőforráscsoportot néven `egress-scenario` |
| Region | Válassza ki az Önhöz legközelebb eső régiót. |
| Hatókör-azonosító | Használja a **korábban** feljegyett azonosítóhatókört. |
| IoT Central SAS-kulcs | Használja a közös hozzáférésű jogosultság jogosultságának elsődleges kulcsát a **SaS-IoT-Devices** regisztrációs csoporthoz. Ezt az értéket korábban jegyezze fel. |

[ ![ Üzembe helyezés az Azure-ban.](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fiotc-device-bridge%2Fmaster%2Fazuredeploy.json)

Válassza **az Áttekintés + létrehozás,** majd a Létrehozás **lehetőséget.** Az Azure-függvény és a kapcsolódó erőforrások létrehozása a kigressziós forgatókönyv **erőforráscsoportban** néhány percet vesz igénybe.

### <a name="transform-device-data-in-the-device-bridge"></a>Eszközadatok átalakítása az eszközhidon

Az eszközhíd konfigurálása az exportált eszközadatok átalakítására:

1. Szerezzen be egy alkalmazás API-kulcsát az Open Weather szolgáltatás. A fiók ingyenes, és a szolgáltatás korlátozottan van használatban. Alkalmazás API-kulcsának létrehozásához hozzon létre egy fiókot az [Open Weather szolgáltatás portálon,](https://openweathermap.org/) és kövesse az utasításokat. Később az Open Weather API-kulcsot kell használnia.

1. A Azure Portal navigáljon a függvényalkalmazáshoz a **kigressziós forgatókönyv** erőforráscsoportban.

1. A bal oldali navigációs sávon a **Fejlesztői eszközök menüben** válassza a App Service Editor **(előzetes verzió) lehetőséget.**

1. Kattintson **&rarr; az Ugrás** gombra a **App Service Editor** megnyitásához. Hajtsa végre a következő módosításokat:

    1. Nyissa meg *a wwwroot/IoTCIntegration/index.js* fájlt. Cserélje le a fájlban található összes kódot a [fájlbanindex.js. ](https://raw.githubusercontent.com/iot-for-all/iot-central-compute/main/Azure_function/index.js)

    1. Az új *index.js* frissítse a `openWeatherAppId` változófájlt a korábban lekért Open Weather API-kulccsal.

        ```javascript
        const openWeatherAppId = '<Your Open Weather API Key>'
        ```

    1. Adjon hozzá egy message (üzenet) tulajdonságot a függvény által az IoT Central. IoT Central ezt a tulajdonságot használja az átalakított adatok exportálásának megakadályozására. A módosítás hoz nyissa meg a *wwwroot/IoTCIntegration/lib/engine.js* fájlt. Keresse meg a következő kódot:

        ```javascript
        if (timestamp) {
          message.properties.add('iothub-creation-time-utc', timestamp);
        }
        ```

        Adja hozzá a következő kódot az előző kódrészletben a kód után:

        ```javascript
        // add a message property that we can look for in data export to not re-compute computed telemetry
        message.properties.add('computed', true);
        ```

        Referenciaként megtekintheti a teljes példát a [](https://raw.githubusercontent.com/iot-for-all/iot-central-compute/main/Azure_function/lib/engine.js)engine.jsfájlra.

1. A bal **App Service Editor** válassza a **Konzol** lehetőséget. Futtassa a következő parancsokat a szükséges csomagok telepítéséhez:

    ```bash
    cd IoTCIntegration
    npm install
    ```

    A parancs futtatása eltarthat néhány percig.

1. Térjen vissza az **Azure-függvény áttekintési oldalára,** és indítsa újra a függvényt:

    :::image type="content" source="media/howto-transform-data/azure-function.png" alt-text="A függvény újraindítása":::

1. A **bal oldali navigációs** sávon válassza a Függvények lehetőséget. Ezután válassza az **IoTCIntegration lehetőséget.** Válassza a **Kód + teszt lehetőséget.**

1. Jegyezze fel a függvény URL-címét, mert később szüksége lesz erre az értékre:

    :::image type="content" source="media/howto-transform-data/get-function-url.png" alt-text="A függvény URL-címének lekért címe":::

### <a name="enable-data-flow-from-iot-central-to-the-device-bridge"></a>Adatfolyam engedélyezése a IoT Central és az eszközhíd között

Ez a szakasz a Azure IoT Central beállítását ismerteti.

Először mentse az [eszközmodell-fájlt](https://raw.githubusercontent.com/iot-for-all/iot-central-compute/main/model.json) a helyi gépre.

Ha eszközsablont szeretne hozzáadni a IoT Central alkalmazáshoz, lépjen a IoT Central alkalmazáshoz, majd:

1. Jelentkezzen be a IoT Central alkalmazásba, és lépjen az **Eszközsablonok lapra.**

1. Válassza **az + Új** lehetőséget, válassza az **IoT-eszköz** lehetőséget, válassza a **Tovább: Testreszabás** lehetőséget, és adja meg a *Számítási* modell nevet a sablon neveként. Válassza a **Next: Review** (Tovább: Áttekintés) lehetőséget. Ezután kattintson a **Létrehozás** elemre.

1. Válassza **a Modell importálása lehetőséget,** és keresse meg *model.jskorábban* letöltött fájlban található adatokat.

1. A modell importálása után válassza a **Közzététel lehetőséget** a Számítási modell **eszközsablon** közzétételéhez.

Az adatok exportálásának beállítása az eszközhidra történő adatexportmal való küldéshez:

1. A saját IoT Central válassza az **Adatexportáció lehetőséget.**

1. Válassza **az + Új cél** lehetőséget az eszközhídhoz használni kívánt cél létrehozásához. Hívja meg a *cél számítási függvényt, a* Cél típusa **mezőben válassza** a **Webhook lehetőséget.** A Visszahívási URL-cím mezőben válassza a beillesztés lehetőséget a függvény URL-címében, amelyet korábban feljegyett. Az **Engedélyezést hagyja** **No Auth (Nincs hitelesítés) ként.**

1. Mentse a módosításokat.

1. Válassza az **+ Új exportálás lehetőséget,** és hozzon létre egy Compute export nevű *adatexportot.*

1. Adjon hozzá egy szűrőt, amely csak a használt eszközsablonhoz exportál eszközadatokat. Válassza **a + Szűrés** lehetőséget, válassza az elem **eszközsablont,** válassza ki az **Egyenlő** operátort, majd válassza ki az előbb létrehozott **Számítási** modell eszközsablont.

1. Adjon hozzá egy üzenetszűrőt az átalakított és a nem átformált adatok megkülönböztetésére. Ez a szűrő megakadályozza az átalakított értékek visszaküldését az eszközhidra. Válassza **a + Üzenet tulajdonságszűrő** lehetőséget, és adja meg a kiszámított névértéket, majd válassza a Nem **létezik operátort.**  A `computed` sztring kulcsszóként használatos az eszközhíd-példakódban.

1. A célhelynél válassza ki a **korábban** létrehozott Compute-függvénycélt.

1. Mentse a módosításokat. Egy perc múlva az **Exportálás** állapota Kifogástalan **lesz.**

### <a name="verify"></a>Ellenőrzés

A forgatókönyv teszteléséhez használt mintaeszköz a következő Node.js. Győződjön meg arról, Node.js és az NPM telepítve van a helyi gépen. Ha nem szeretné telepíteni ezeket az előfeltételeket, használja a[Azure Cloud Shell,](https://shell.azure.com/) amely előre telepítette őket.

A forgatókönyvet tesztelő mintaeszköz futtatása:

1. Klónozza a mintakódot tartalmazó GitHub-adattárat, és futtassa a következő parancsot:

    ```bash
    git clone https://github.com/iot-for-all/iot-central-compute
    ```

1. Ha csatlakoztatni szeretné a mintaeszközt a IoT Central-alkalmazáshoz, szerkessze a kapcsolati beállításokat az *iot-central-compute/device/device.js* fájlban. Cserélje le a hatókör-azonosítót és a csoport SAS-kulcsát a korábban feljegyett értékekre:

    ```javascript
    // These values need to be filled in from your Azure IoT Central application
    //
    const scopeId = "<IoT Central Scope Id value>";
    const groupSasKey = "<IoT Central Group SAS key>";
    //
    ```

    Mentse a módosításokat.

1. A szükséges csomagok telepítéséhez és az eszköz futtatásához használja a következő parancsokat:

    ```bash
    cd ~/iot-central-compute/device
    npm install
    node device.js
    ```

1. A parancs eredménye a következő kimenethez hasonló:

    ```output
    registration succeeded
    assigned hub=iotc-2bd611b0....azure-devices.net
    deviceId=computeDevice
    Client connected
    send status: MessageEnqueued [{"data":"33.23, 69.09, 30.7213, -61.1192"}]
    send status: MessageEnqueued [{"data":"2.43, 75.86, -2.6358, 162.935"}]
    send status: MessageEnqueued [{"data":"6.19, 76.55, -14.3538, -82.314"}]
    send status: MessageEnqueued [{"data":"33.26, 48.01, 71.9172, 48.6606"}]
    send status: MessageEnqueued [{"data":"40.5, 36.41, 14.6043, 14.079"}]
    ```

1. A saját IoT Central keresse meg a **computeDevice nevű eszközt.** A Nyers **adatok nézetben** két különböző telemetriai adatfolyam látható öt másodpercenként. A modell nélküli adatokat tartalmazó stream az eredeti telemetria, a modellbe modellező adatokkal pedig a függvény által átalakított adatok:

    :::image type="content" source="media/howto-transform-data/egress-telemetry.png" alt-text="Képernyőkép az eredeti és átalakított nyers adatokról.":::

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége az útmutató lépéseit követve létrehozott Azure-erőforrásokra, törölje az erőforráscsoportokat a [Azure Portal.](https://portal.azure.com/?r=1#blade/HubsExtension/BrowseResourceGroups)

Az útmutatóban használt két erőforráscsoport a bejövő **és** a bejövő forgalom **forgatókönyve.**

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben a bejövő és a IoT Central eszközadatok átalakításának különböző lehetőségeit tanulta meg. A cikk két konkrét forgatókönyvhöz tartalmaz bemutatókat:

- Egy IoT Edge használatával átalakíthatja az adatokat az lefelé irányuló eszközökről, mielőtt az adatokat a rendszer IoT Central az alkalmazásnak.
- A Azure Functions adatokat a külső IoT Central. Ebben a forgatókönyvben a IoT Central adatexportál használatával küld bejövő adatokat egy átalakítható Azure-függvénynek. A függvény visszaküldi az átalakított adatokat a IoT Central alkalmazásnak.

Most, hogy megtanulta, hogyan alakíthat át eszközadatokat a Azure IoT Central-alkalmazáson kívül, megtudhatja, hogyan elemezheti az eszközadatokat az [IoT Central.](howto-create-analytics.md)
