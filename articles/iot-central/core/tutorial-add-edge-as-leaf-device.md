---
title: Oktatóanyag – Azure IoT Edge eszköz hozzáadása az Azure IoT Centralhoz | Microsoft Docs
description: Oktatóanyag – kezelőként Azure IoT Edge eszköz hozzáadása az Azure IoT Central-alkalmazáshoz
author: rangv
ms.author: rangv
ms.date: 05/29/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom:
- mvc
- device-developer
- iot-edge
ms.openlocfilehash: 373d144b4df818a075f0088e9cbf31cb5027e747
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101724880"
---
# <a name="tutorial-add-an-azure-iot-edge-device-to-your-azure-iot-central-application"></a>Oktatóanyag: Azure IoT Edge-eszköz hozzáadása az Azure IoT Central-alkalmazáshoz

*Ez a cikk a kezelők, a megoldás-építők és az eszközök fejlesztőire vonatkozik.*

Ebből az oktatóanyagból megtudhatja, hogyan konfigurálhat és adhat hozzá egy Azure IoT Edge-eszközt az Azure IoT Central-alkalmazáshoz. Az oktatóanyag egy IoT Edge-kompatibilis linuxos virtuális gépet (VM) használ egy IoT Edge-eszköz szimulálására. A IoT Edge eszköz szimulált környezeti telemetria létrehozó modult használ. A telemetria a IoT Central alkalmazásban található irányítópulton tekintheti meg.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Eszköz sablonjának létrehozása IoT Edge eszközhöz
> * IoT Edge-eszköz létrehozása IoT Central
> * Szimulált IoT Edge eszköz üzembe helyezése Linux rendszerű virtuális gépen

## <a name="prerequisites"></a>Előfeltételek

Fejezze be az [Azure IoT Central-alkalmazás létrehozása](./quick-deploy-iot-central.md) című rövid útmutatót egy IoT Central-alkalmazás létrehozásához az **egyéni alkalmazás > egyéni alkalmazásspecifikus** sablon használatával.

Az oktatóanyag lépéseinek elvégzéséhez aktív Azure-előfizetésre van szükség.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

Töltse le a IoT Edge manifest-fájlt a GitHubról. Kattintson a jobb gombbal a következő hivatkozásra, majd válassza a **hivatkozás mentése másként**: [EnvironmentalSensorManifest.jsbekapcsolva](https://raw.githubusercontent.com/Azure-Samples/iot-central-docs-samples/master/iotedge/EnvironmentalSensorManifest.json) lehetőséget.

## <a name="create-device-template"></a>Eszköz sablonjának létrehozása

Ebben a szakaszban egy IoT Central-eszköz sablonját hozza létre egy IoT Edge eszközhöz. Egy IoT Edge jegyzékfájlt importálhat az első lépésekhez, majd a sablon módosításával telemetria-definíciókat és-nézeteket adhat hozzá:

### <a name="import-manifest-to-create-template"></a>Jegyzékfájl importálása sablon létrehozásához

Eszköz sablonjának létrehozása IoT Edge jegyzékfájlból:

1. A IoT Central alkalmazásban navigáljon az **eszközök sablonjaihoz** , és válassza az **+ új** lehetőséget.

1. A **sablon típusának kiválasztása** lapon válassza a **Azure IoT Edge** csempét. Ezután válassza a **Tovább: testreszabás** lehetőséget.

1. Az **Azure IoT Edge központi telepítési jegyzék feltöltése** lapon adja meg a *környezeti érzékelő peremhálózati eszközét* az eszköz sablonjának neveként. Ezután válassza a **Tallózás** lehetőséget a korábban letöltött **EnvironmentalSensorManifest.js** feltöltéséhez. Ezután válassza a **Tovább: felülvizsgálat** lehetőséget.

1. Az **Áttekintés** lapon válassza a **Létrehozás** lehetőséget.

1. Válassza a **kezelés** felületet a **SimulatedTemperatureSensor** modulban a jegyzékfájlban definiált két tulajdonság megtekintéséhez:

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/imported-manifest.png" alt-text="IoT Edge jegyzékfájlból létrehozott eszköz sablonja":::

> [!TIP]
> Ez az üzembe helyezési jegyzék lekéri a modul rendszerképeit egy olyan Azure Container Registry adattárból, amely nem igényel hitelesítő adatokat a kapcsolódáshoz. Ha a modul rendszerképeit privát tárházból szeretné használni, állítsa be a tároló beállításjegyzékbeli hitelesítő adatait a jegyzékfájlban.

### <a name="add-telemetry-to-manifest"></a>Telemetria hozzáadása a jegyzékfájlhoz

Egy IoT Edge jegyzékfájl nem határozza meg a telemetria által küldött adatokat. A telemetria-definíciókat a IoT Central eszköz sablonjában adja hozzá. A **SimulatedTemperatureSensor** modul a következő JSON-hoz hasonló telemetria-üzeneteket küld:

```json
{
  "machine": {
    "temperature": 75.0,
    "pressure": 40.2
  },
  "ambient": {
    "temperature": 23.0,
    "humidity": 30.0
  },
  "timeCreated": ""
}
```

A telemetria-definíciók hozzáadása az eszköz sablonhoz:

1. Válassza a **kezelés** felületet a **környezeti érzékelő Edge-eszköz** sablonjában.

1. Válassza a **+ képesség hozzáadása** lehetőséget. Adja  meg a gép **megjelenítendő nevét** , és győződjön meg arról, hogy a **telemetria** **típusa** .

1. Válassza az **objektum** lehetőséget a séma típusaként, majd válassza a **Definiálás** lehetőséget. Az objektum definíciója lapon adja hozzá a *hőmérsékletet* és a *nyomást* a **Double** típusú attribútumokként, majd válassza az **alkalmaz** lehetőséget.

1. Válassza a **+ képesség hozzáadása** lehetőséget. A **megjelenítendő név** mezőben adja meg a *környezeti* értéket, és győződjön meg arról, hogy a **képesség típusa** **telemetria**.

1. Válassza az **objektum** lehetőséget a séma típusaként, majd válassza a **Definiálás** lehetőséget. Az objektum definíciója lapon adja hozzá a *hőmérséklet* és a *páratartalom* értéket a **Double** típusú attribútumoknál, majd válassza az **alkalmaz** lehetőséget.

1. Válassza a **+ képesség hozzáadása** lehetőséget. Írja  be a timeCreated **nevet a megjelenítendő név** értékre, és győződjön meg arról, hogy a **képesség típusa** **telemetria**.

1. Válassza a **datetime** értéket a séma típusaként.

1. A sablon frissítéséhez válassza a **Mentés** lehetőséget.

A **kezelés** felület mostantól tartalmazza a **gép**-, a **környezeti** és a **timeCreated** telemetria-típusokat:

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/manage-interface.png" alt-text="Felület gépi és környezeti telemetria-típusokkal":::

### <a name="add-views-to-template"></a>Nézetek hozzáadása a sablonhoz

Az eszköz sablonja még nem rendelkezik olyan nézettel, amely lehetővé teszi, hogy az operátor megtekintse a telemetria a IoT Edge eszközről. Nézet hozzáadása az eszköz sablonhoz:

1. Válassza a **views (nézetek** ) lehetőséget a **környezeti érzékelő Edge-eszköz** sablonjában.

1. Az **új nézet hozzáadása** lapon válassza az **eszköz megjelenítése** csempét.

1. Módosítsa a nézet nevét *IoT Edge eszköz telemetria megtekintéséhez*.

1. Válassza ki a **környezeti** és a **gépi** telemetria típusait. Ezután válassza a **csempe hozzáadása** lehetőséget.

1. Válassza a **Mentés** lehetőséget a **nézet mentéséhez IoT Edge eszköz telemetria** nézetét.

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/template-telemetry-view.png" alt-text="Eszköz sablonja telemetria nézettel":::

### <a name="publish-the-template"></a>A sablon közzététele

Ahhoz, hogy hozzáadjon egy olyan eszközt, amely a **környezeti érzékelők peremhálózati eszközének** sablonját használja, közzé kell tennie a sablont.

Navigáljon a **környezeti érzékelő Edge-eszköz** sablonhoz, és válassza a **Közzététel** lehetőséget. Az **eszköz sablonjának közzététele az alkalmazás** panelen válassza a **Közzététel** lehetőséget a sablon közzétételéhez:

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/publish-template.png" alt-text="Az eszközsablon közzététele":::

## <a name="add-iot-edge-device"></a>IoT Edge eszköz hozzáadása

Most, hogy közzétette a **környezeti érzékelők peremhálózati eszközének** sablonját, hozzáadhat egy eszközt a IoT Central alkalmazáshoz:

1. A IoT Central alkalmazásban navigáljon az **eszközök** lapra, és válassza a **környezeti érzékelő Edge-eszköz** lehetőséget az elérhető sablonok listájában.

1. Válassza az **+ új** lehetőséget, ha új eszközt szeretne hozzáadni a sablonból. Az **új eszköz létrehozása** lapon válassza a **Létrehozás** lehetőséget.

Most már rendelkezik egy új, **regisztrált** állapottal rendelkező eszközzel:

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/new-device.png" alt-text="Új, regisztrált eszköz":::

### <a name="get-the-device-credentials"></a>Az eszköz hitelesítő adatainak beolvasása

Amikor az oktatóanyag későbbi részében telepíti a IoT Edge eszközt, szüksége lesz a hitelesítő adatokra, amelyek lehetővé teszik, hogy az eszköz csatlakozhasson a IoT Central alkalmazáshoz. Az eszköz hitelesítő adatainak lekérése:

1. Az **eszköz** lapon válassza ki a létrehozott eszközt.

1. Válassza a **Kapcsolódás** lehetőséget.

1. Az **eszköz-kapcsolatok** lapon jegyezze fel az **azonosító hatókörét**, az **eszköz azonosítóját** és az **elsődleges kulcsot**. Ezeket az értékeket később használhatja.

1. Válassza a **Bezárás** gombot.

Ezzel befejezte a IoT Central alkalmazás konfigurálását, hogy lehetővé váljon IoT Edge-eszköz csatlakoztatása.

## <a name="deploy-an-iot-edge-device"></a>IoT Edge eszköz üzembe helyezése

Ebben az oktatóanyagban egy Azure IoT Edge-kompatibilis linuxos virtuális gépet használ, amelyet az Azure-ban hoztak létre egy IoT Edge-eszköz szimulálásához. Az IoT Edge-kompatibilis virtuális gép Azure-előfizetésben való létrehozásához kattintson a következőre:

[![Az iotedge-vm-deploy sablon Üzembe helyezés az Azure-ban gombja](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fiotedge-vm-deploy%2Fmaster%2FedgeDeploy.json)

Az **Egyéni telepítés** lapon:

1. Válassza ki Azure-előfizetését.

1. Válassza az **új létrehozása** elemet, és hozzon létre egy *központi Edge-RG* nevű erőforráscsoportot.

1. Válasszon ki egy régiót a közelben.

1. Adjon hozzá egy egyedi **DNS-címke előtagját** , például *contoso-Central-Edge*.

1. Válassza ki a virtuális gép rendszergazdai felhasználónevét.

1. Adja meg a *temp* értéket a kapcsolatok karakterlánca. Később úgy konfigurálja az eszközt, hogy a DPS használatával kapcsolódjon.

1. Fogadja el a virtuális gép méretének, Ubuntu-verziójának és helyének alapértelmezett értékeit.

1. A hitelesítési típusként válassza a **jelszó** lehetőséget.

1. Adja meg a virtuális gép jelszavát.

1. Ezután válassza a **felülvizsgálat + létrehozás** elemet.

1. Tekintse át a beállításokat, majd válassza a **Létrehozás** lehetőséget:

    :::image type="content" source="media/tutorial-add-edge-as-leaf-device/vm-deployment.png" alt-text="IoT Edge virtuális gép létrehozása":::

A telepítés elvégzése néhány percet vesz igénybe. Az üzembe helyezés befejezésekor navigáljon a Azure Portal **középső szélű RG** erőforráscsoporthoz.

### <a name="configure-the-iot-edge-vm"></a>A IoT Edge virtuális gép konfigurálása

A virtuális gépen IoT Edge konfigurálásához a DPS használatával regisztrálja és kapcsolódjon a IoT Central alkalmazáshoz:

1. A **contoso-Edge-RG** erőforráscsoporthoz válassza ki a virtuálisgép-példányt.

1. A **támogatás + hibaelhárítás** szakaszban válassza a **Serial Console** lehetőséget. Ha a rendszer felszólítja a rendszerindítási diagnosztika konfigurálására, kövesse a portálon megjelenő utasításokat.

1. Nyomja le az **ENTER** billentyűt a parancssor megjelenítéséhez `login:` . A bejelentkezéshez adja meg felhasználónevét és jelszavát.

1. Futtassa a következő parancsot a IoT Edge Runtime verziójának vizsgálatához. Az írás időpontjában a verzió 1.0.9.1:

    ```bash
    sudo iotedge --version
    ```

1. A `nano` szerkesztő segítségével nyissa meg a IoT Edge config. YAML fájlt:

    ```bash
    sudo nano /etc/iotedge/config.yaml
    ```

1. Görgessen lefelé, amíg meg nem jelenik `# Manual provisioning configuration` . Tegye megjegyzésbe a következő három sort az alábbi kódrészletben látható módon:

    ```yaml
    # Manual provisioning configuration
    #provisioning:
    #  source: "manual"
    #  device_connection_string: "temp"
    ```

1. Görgessen lefelé, amíg meg nem jelenik `# DPS symmetric key provisioning configuration` . A következő nyolc sor megjegyzésének visszalépése az alábbi kódrészletben látható módon:

    ```yaml
    # DPS symmetric key provisioning configuration
    provisioning:
      source: "dps"
      global_endpoint: "https://global.azure-devices-provisioning.net"
      scope_id: "{scope_id}"
      attestation:
        method: "symmetric_key"
        registration_id: "{registration_id}"
        symmetric_key: "{symmetric_key}"
    ```

    > [!TIP]
    > Győződjön meg arról, hogy nincs elég hely az előtt `provisioning:`

1. A helyére írja `{scope_id}` be az **azonosító hatókört** , amelyet korábban jegyzett készített.

1. A helyére írja `{registration_id}` be az **eszköz azonosítóját** , amelyet korábban jegyzett készített.

1. Cserélje le az `{symmetric_key}` t az **elsődleges kulcsra** , amelyet korábban jegyzett készített.

1. Mentse a módosításokat (**CTRL-O**), és zárja be (**CTRL-X**) a `nano` szerkesztőt.

1. Futtassa a következő parancsot a IoT Edge démon újraindításához:

    ```bash
    sudo systemctl restart iotedge
    ```

1. A IoT Edge modulok állapotának megtekintéséhez futtassa a következő parancsot:

    ```bash
    iotedge list
    ```

    A következő minta kimenet a futó modulokat mutatja:

    ```bash
    NAME                        STATUS           DESCRIPTION      CONFIG
    SimulatedTemperatureSensor  running          Up 20 seconds    mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0
    edgeAgent                   running          Up 27 seconds    mcr.microsoft.com/azureiotedge-agent:1.0
    edgeHub                     running          Up 22 seconds    mcr.microsoft.com/azureiotedge-hub:1.0
    ```

    > [!TIP]
    > Előfordulhat, hogy várnia kell az összes modul futtatásának megkezdéséhez.

## <a name="view-the-telemetry"></a>A telemetriai adatok megtekintése

A szimulált IoT Edge eszköz mostantól fut a virtuális gépen. A IoT Central alkalmazásban az eszköz állapota most az **eszközök** lapon lett **kiépítve** :

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/provisioned-device.png" alt-text="Kiépített IoT Edge eszköz":::

A telemetria a **IoT Edge eszköz telemetria megtekintése** lapon tekintheti meg az eszközön:

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/device-telemetry-view.png" alt-text="Eszköz telemetria":::

A **modulok** lap az eszközön lévő IoT Edge-modulok állapotát jeleníti meg:

:::image type="content" source="media/tutorial-add-edge-as-leaf-device/edge-module-status.png" alt-text="Eszköz moduljának állapota":::

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt tervezi, hogy folytatja a munkát a IoT Edge virtuális géppel, megtarthatja és újra használhatja az oktatóanyagban használt erőforrásokat. Ellenkező esetben az oktatóanyagban létrehozott erőforrásokat törölheti a további díjak elkerülése érdekében:

* A IoT Edge virtuális gép és a hozzá tartozó erőforrások törléséhez törölje a **contoso-Edge-RG** erőforráscsoportot a Azure Portal.
* A IoT Central alkalmazás törléséhez navigáljon az alkalmazás **Adminisztráció** szakaszának **alkalmazás** lapjára, és válassza a **Törlés** lehetőséget.

Most, hogy megtanulta, hogyan használhatja és felügyelheti IoT Edge eszközeit a IoT Centralban, a következő lépés a következő:

> [!div class="nextstepaction"]
> [Az eszközök telemetria elemzése az eszközök csoportjaival](./tutorial-use-device-groups.md)

## <a name="next-steps"></a>Következő lépések

Most, hogy megtanulta, hogyan dolgozhat és kezelhet IoT Edge-eszközöket IoT Centralban, a következő lépés az, hogy olvassa el a következőket:

> [!div class="nextstepaction"]
> [IoT Edge modulok fejlesztése](../../iot-edge/tutorial-develop-for-linux.md)