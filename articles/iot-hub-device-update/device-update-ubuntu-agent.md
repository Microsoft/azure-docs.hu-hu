---
title: Eszköz frissítése az Azure IoT Hub oktatóanyaghoz az Ubuntu Server 18,04 x64 Package Agent használatával | Microsoft Docs
description: Ismerkedjen meg az Azure IoT Hub eszköz frissítésével az Ubuntu Server 18,04 x64 Package Agent használatával.
author: vimeht
ms.author: vimeht
ms.date: 2/16/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: 19be3b141fb663ea0f4f11d811bf6ffc33252504
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101664897"
---
# <a name="device-update-for-azure-iot-hub-tutorial-using-the-ubuntu-server-1804-x64-package-agent"></a>Eszköz frissítése az Azure IoT Hub oktatóanyaghoz az Ubuntu Server 18,04 x64 Package Agent használatával

A IoT Hub eszköz frissítése két frissítési formát támogat – a rendszerkép-alapú és a Package-alapú.

A Package-alapú frissítések olyan célzott frissítések, amelyek csak egy adott összetevőt vagy alkalmazást módosítanak az eszközön. Ez csökkenti a sávszélesség-használatot, és csökkenti a frissítés letöltésének és telepítésének idejét. A csomagok frissítései általában lehetővé teszik, hogy az eszközök kevesebb állásidőt alkalmazzanak, amikor frissítést alkalmaznak, és elkerülik a lemezképek létrehozásának terhelését.

Ez az oktatóanyag végigvezeti azokon a lépéseken, amelyekkel teljes körű, a IoT Hub eszköz frissítésén keresztül végezhető el a csomagon alapuló frissítés. Ehhez az oktatóanyaghoz az Ubuntu Server 18,04 x64-es csomag-ügynökét fogjuk használni. Még ha más operációsrendszer-platform konfigurációt is szeretne használni, ez az oktatóanyag továbbra is hasznos lehet a IoT Hub eszköz frissítésében található eszközök és fogalmak megismeréséhez. Fejezze be ezt a bevezetést egy teljes körű frissítési folyamatra, majd válassza ki a frissítés és az operációs rendszer platform előnyben részesített formáját a részletek kiszámításához. Az IoT Hub eszköz frissítésével frissíthet egy Azure-IoT vagy Azure IoT Edge-eszközt az oktatóanyag használatával. 

Ezen oktatóanyag segítségével megtanulhatja a következőket:
> [!div class="checklist"]
> * Az eszköz frissítési csomagjainak tárházának konfigurálása
> * Az eszköz frissítési ügynökének és függőségeinek letöltése és telepítése
> * Címke hozzáadása a IoT-eszközhöz
> * Frissítés importálása
> * Eszközcsoport létrehozása
> * Csomag frissítésének központi telepítése
> * A frissítés központi telepítésének figyelése

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Előfeltételek
* Hozzáférés egy IoT Hubhoz. Javasoljuk, hogy S1 (standard) vagy újabb szintet használjon.
* Egy Azure-IoT vagy Azure IoT Edge Ubuntu Server 18,04 x64-et futtató eszköz, amely IoT Hubhoz csatlakozik.
   * Ha Azure IoT Edge eszközt használ, győződjön meg arról, hogy az a peremhálózati vagy magasabb szintű futtatókörnyezet v 1.2.0 van 
* Ha nem Azure IoT Edge eszközt használ, [telepítse a legújabb `aziot-identity-service` csomagot (előzetes verzió) a IoT-eszközön](https://github.com/Azure/iot-identity-service/actions/runs/575919358) 
* [Az eszköz frissítési fiókja és példánya ugyanahhoz a IoT Hubhoz van csatolva, mint a fenti.](create-device-update-account.md)

## <a name="configure-device-update-package-repository"></a>Az eszköz frissítési csomagjainak tárházának konfigurálása

1. Telepítse az eszköz operációs rendszerének megfelelő adattár-konfigurációt. Ebben az oktatóanyagban ez az Ubuntu Server 18,04 lesz. 
   
   ```shell
      curl https://packages.microsoft.com/config/ubuntu/18.04/multiarch/prod.list > ./microsoft-prod.list
   ```

2. Másolja a generált listát a sources. list. d könyvtárba.

   ```shell
      sudo cp ./microsoft-prod.list /etc/apt/sources.list.d/
   ```
   
3. Telepítse a Microsoft GPG nyilvános kulcsot.

   ```shell
      curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
      sudo cp ./microsoft.gpg /etc/apt/trusted.gpg.d/
   ```

## <a name="install-device-update-deb-agent-packages"></a>Az eszköz Update. deb ügynök csomagjainak telepítése

1. Frissítési csomagok listája az eszközön

   ```shell
      sudo apt-get update
   ```

2. A deviceupdate-ügynök csomag és a hozzá tartozó függőségek telepítése

   ```shell
      sudo apt-get install deviceupdate-agent deliveryoptimization-plugin-apt
   ```

Az Azure IoT Hub szoftvercsomag eszközének frissítése a következő licencfeltételek hatálya alá tartozik:
   * [IoT Hub licenccel rendelkező eszköz frissítése](https://github.com/Azure/iot-hub-device-update/blob/main/LICENSE.md)
   * [Kézbesítési optimalizálási ügyféllicenc](https://github.com/microsoft/do-client/blob/main/LICENSE.md)
   
A licencfeltételek elolvasása a csomag használata előtt. A csomagok telepítése és használata a jelen feltételek elfogadását jelenti. Ha nem fogadja el a licencfeltételeket, ne használja ezt a csomagot.

## <a name="configure-device-update-agent-using-azure-iot-identity-service-preview"></a>Az eszköz frissítési ügynökének konfigurálása az Azure IoT Identity Service (előzetes verzió) használatával

Miután telepítette a szükséges csomagokat, létre kell hoznia az eszközt a Felhőbeli identitás-és hitelesítési adataival.

1. A konfigurációs fájl megnyitása

   ```shell
      sudo nano /etc/aziot/config.toml
   ```

2. A fájl kiépítési konfiguráció szakaszának megkeresése. A "manuális kiépítés a kapcsolatok karakterlánctal" szakasz megjegyzésének elutasítása. Frissítse a connection_string értékét a IoT (Edge) eszközhöz tartozó kapcsolatok karakterláncával. Győződjön meg arról, hogy az összes többi kiépítési szakaszt feljegyezték.


   ```toml
      # Manual provisioning configuration using a connection string
      [provisioning]
      source = "manual"
      iothub_hostname = "<REQUIRED IOTHUB HOSTNAME>"
      device_id = "<REQUIRED DEVICE ID PROVISIONED IN IOTHUB>"
      dynamic_reprovisioning = false 
   ```

3. Mentse és zárda be a fájlt a CTRL + X, Y billentyűkombináció használatával

4. Alkalmazza a konfigurációt. 

   Ha IoT Edge eszközt használ, használja a következő parancsot. 
   
   ```shell
      sudo iotedge config apply
   ```
   
   Ha IoT eszközt használ, a `aziot-identity-service` csomag telepítése után használja az alábbi parancsot. 
      
   ```shell
      sudo aziotctl config apply
   ```

5. Igény szerint ellenőrizheti, hogy a szolgáltatások futnak-e

    ```shell
       sudo systemctl list-units --type=service | grep 'adu-agent\.service\|deliveryoptimization-agent\.service'
    ```

    A kimenetnek olvasnia kell:

    ```markdown
       adu-agent.service                   loaded active running Device Update for IoT Hub Agent daemon.

       deliveryoptimization-agent.service               loaded active running deliveryoptimization-agent.service: Performs content delivery optimization tasks   `
    ```

## <a name="add-a-tag-to-your-device"></a>Címke hozzáadása az eszközhöz

1. Jelentkezzen be [Azure Portalba](https://portal.azure.com) , és navigáljon a IoT hub.

2. A bal oldali navigációs ablaktáblán található "IoT-eszközök" vagy "IoT Edge" közül keresse meg a IoT-eszközt, és keresse meg az eszköz Ikerét.

3. A Twin (eszközök) készletben törölje a meglévő eszköz frissítési címke értékét a NULL értékre állításával.

4. Vegyen fel egy új eszköz frissítési címke értékét az alább látható módon.

```JSON
    "tags": {
            "ADUGroup": "<CustomTagValue>"
            }
```

## <a name="import-update"></a>Frissítés importálása

1. Töltse le a következő [apt manifest-fájlt](https://github.com/Azure/iot-hub-device-update/tree/main/docs/sample-artifacts/libcurl4-doc-apt-manifest.json) , és [importálja a jegyzékfájlt](https://github.com/Azure/iot-hub-device-update/tree/main/docs/sample-artifacts/sample-package-update-1.0.1-importManifest.json). Ez az apt-jegyzékfájl a legújabb elérhető verzióját fogja telepíteni a `libcurl4-doc package` IoT-eszközre. 

   Azt is megteheti, hogy letölti ezt az [apt manifest-fájlt](https://github.com/Azure/iot-hub-device-update/tree/main/docs/sample-artifacts/libcurl4-doc-7.58-apt-manifest.json) , és [importálja a jegyzékfájlt](https://github.com/Azure/iot-hub-device-update/tree/main/docs/sample-artifacts/sample-package-update-2-2.0.1-importManifest.json). Ezzel az adott verziójú v 7.58.0 telepíti a `libcurl4-doc package` IoT-eszközre. 

2. Azure Portal válassza ki az eszközök frissítései lehetőséget az automatikus eszközkezelés lehetőségnél a IoT Hub bal oldali navigációs sávján.

3. Válassza a frissítések lapot.

4. Válassza az "+ új frissítés importálása" lehetőséget.

5. Válassza ki a mappa ikont vagy szövegmezőt a "Select a manifest file List" (fájl importálása) elem alatt. Ekkor megjelenik egy fájl-választó párbeszédpanel. Válassza ki a korábban letöltött importálási jegyzékfájlt. Ezután válassza a mappa ikont vagy a szövegmezőt a "válasszon ki egy vagy több frissítési fájlt" területen. Ekkor megjelenik egy fájl-választó párbeszédpanel. Válassza ki a korábban letöltött apt manifest-frissítési fájlt.
   
   :::image type="content" source="media/import-update/select-update-files.png" alt-text="A frissítési fájl kijelölését bemutató képernyőkép." lightbox="media/import-update/select-update-files.png":::

6. Válassza a mappa ikont vagy a szövegmezőt a "Storage-tároló kiválasztása" területen. Ezután válassza ki a megfelelő Storage-fiókot.

7. Ha már létrehozott egy tárolót, újra használhatja azt. (Ellenkező esetben válassza a "+ tároló" lehetőséget a frissítések új tárolójának létrehozásához.).  Válassza ki a használni kívánt tárolót, és kattintson a Kiválasztás gombra.

   :::image type="content" source="media/import-update/container.png" alt-text="A tároló kijelölését bemutató képernyőkép." lightbox="media/import-update/container.png":::

8. Az importálási folyamat elindításához válassza a Küldés lehetőséget.

9. Az importálási folyamat megkezdődik, és a képernyő az "importálási előzmények" szakaszra változik. Válassza a frissítés lehetőséget, ha az importálási folyamat befejeződéséig szeretné megtekinteni a folyamatot. A frissítés méretétől függően ez néhány percen belül elvégezhető, de hosszabb időt is igénybe vehet.
   
   :::image type="content" source="media/import-update/update-publishing-sequence-2.png" alt-text="Az importálási folyamat frissítését bemutató képernyőkép." lightbox="media/import-update/update-publishing-sequence-2.png":::

10. Ha az Állapot oszlop jelzi, hogy az importálás sikeres volt, válassza a "készen áll a telepítésre" fejlécet. Ekkor megjelenik az importált frissítés a listában.

[További](import-update.md) információ a frissítések importálásáról.

## <a name="create-update-group"></a>Frissítési csoport létrehozása

1. Nyissa meg az eszköz frissítési példányához korábban csatlakoztatott IoT Hub.

2. Válassza ki az eszközök frissítései lehetőséget a bal oldali navigációs sávon az automatikus eszközkezelés lehetőség alatt.

3. Válassza a csoportok fület az oldal tetején. 

4. Új csoport létrehozásához kattintson a Hozzáadás gombra.

5. Válassza ki az előző lépésben létrehozott IoT Hub címkét a listából. Válassza a frissítési csoport létrehozása lehetőséget.

   :::image type="content" source="media/create-update-group/select-tag.PNG" alt-text="A címke kijelölését bemutató képernyőfelvétel" lightbox="media/create-update-group/select-tag.PNG":::

[További](create-update-group.md) információ a címkék hozzáadásáról és a frissítési csoportok létrehozásáról


## <a name="deploy-update"></a>Frissítés telepítése

1. Miután létrejött a csoport, megjelenik egy új frissítés az eszközcsoport számára, amely a frissítésre mutató hivatkozást is tartalmazza a függőben lévő frissítések területen. Előfordulhat, hogy egyszer kell frissítenie. 

2. Kattintson az elérhető frissítésre.

3. Ellenőrizze, hogy a megfelelő csoport van-e kiválasztva célcsoportként. Ütemezze az üzemelő példányt, majd válassza a frissítés telepítése lehetőséget.

   :::image type="content" source="media/deploy-update/select-update.png" alt-text="Frissítés kiválasztása" lightbox="media/deploy-update/select-update.png":::

4. Tekintse meg a megfelelőségi diagramot. Ekkor már folyamatban van a frissítés. 

   :::image type="content" source="media/deploy-update/update-in-progress.png" alt-text="Frissítés folyamatban" lightbox="media/deploy-update/update-in-progress.png":::

5. Az eszköz sikeres frissítése után a megfelelőségi diagram és az üzembe helyezési adatok frissítése megjelenik, hogy az tükrözze ugyanezt. 

   :::image type="content" source="media/deploy-update/update-succeeded.png" alt-text="Sikeres frissítés" lightbox="media/deploy-update/update-succeeded.png":::

## <a name="monitor-an-update-deployment"></a>Frissítések központi telepítésének figyelése

1. Válassza a központi telepítések fület az oldal tetején.

   :::image type="content" source="media/deploy-update/deployments-tab.png" alt-text="Központi telepítések lap" lightbox="media/deploy-update/deployments-tab.png":::

2. Válassza ki a létrehozott központi telepítést a központi telepítés részleteinek megtekintéséhez.

   :::image type="content" source="media/deploy-update/deployment-details.png" alt-text="Központi telepítés részletei" lightbox="media/deploy-update/deployment-details.png":::

3. Kattintson a frissítés elemre a legutóbbi állapot részleteinek megtekintéséhez. Folytassa ezt a folyamatot, amíg az állapot sikeresen nem változik.

Sikeresen befejezte a teljes csomag frissítését az eszköz frissítésével IoT Hub egy Ubuntu Server 18,04 x64-es eszközön. 

## <a name="bonus-steps"></a>Bónusz lépései

1. Töltse le a következő [apt manifest-fájlt](https://github.com/Azure/iot-hub-device-update/tree/main/docs/sample-artifacts/libcurl4-doc-remove-apt-manifest.json) , és [importálja a jegyzékfájlt](https://github.com/Azure/iot-hub-device-update/tree/main/docs/sample-artifacts/sample-package-update-1.0.2-importManifest.json). Ez az apt-jegyzékfájl eltávolítja a telepített `libcurl4-doc package` IoT-eszközről. 

2. Ismételje meg a "frissítés importálása" és a "frissítés központi telepítése" szakaszt.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az eszköz frissítési fiókját, példányát, IoT Hub és IoT eszközét. Ezt úgy teheti meg, hogy az egyes erőforrásokat és a "Törlés" lehetőséget választja. Vegye figyelembe, hogy az eszköz frissítési fiókjának tisztítása előtt törölnie kell egy eszköz frissítési példányát. 

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Kép frissítése a málna PI 3 B + oktatóanyagban](device-update-raspberry-pi.md)

