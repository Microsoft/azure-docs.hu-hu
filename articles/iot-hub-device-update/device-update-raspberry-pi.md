---
title: Eszköz frissítése az Azure IoT Hub oktatóanyaghoz a málna PI 3 B + Reference Yocto-rendszerkép használatával | Microsoft Docs
description: Ismerkedjen meg az Azure IoT Hub Device Update szolgáltatásával a málna PI 3 B + Reference Yocto képpel.
author: valls
ms.author: valls
ms.date: 2/11/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: 143a7c411bea6a451645c860b7b5d12d2aa8d9f5
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2021
ms.locfileid: "106121336"
---
# <a name="device-update-for-azure-iot-hub-tutorial-using-the-raspberry-pi-3-b-reference-image"></a>Eszköz frissítése az Azure IoT Hub oktatóanyaghoz a málna PI 3 B és a Reference kép használatával

A IoT Hub eszköz frissítése két frissítési formát támogat – a rendszerkép-alapú és a Package-alapú.

A rendszerkép frissítései magasabb szintű megbízhatóságot biztosítanak az eszköz állapotában. Általában könnyebb replikálni az éles üzem előtti környezet és az éles környezet közötti rendszerképek eredményeit, mivel az nem ugyanazokat a kihívásokat eredményezi, mint a csomagok és a függőségeik. Az Atom jellegéből adódóan az egyik A/B feladatátvételi modellt is könnyedén elvégezheti.

Ez az oktatóanyag végigvezeti azokon a lépéseken, amelyekkel a IoT Hub eszköz frissítésével teljes körű rendszerkép-alapú frissítést hajthat végre. 

Ezen oktatóanyag segítségével megtanulhatja a következőket:
> [!div class="checklist"]
> * Rendszerkép letöltése
> * Címke hozzáadása a IoT-eszközhöz
> * Frissítés importálása
> * Eszközcsoport létrehozása
> * Lemezkép frissítésének központi telepítése
> * A frissítés központi telepítésének figyelése

## <a name="prerequisites"></a>Előfeltételek
* Ha még nem tette meg, hozzon létre egy [eszköz-frissítési fiókot és-példányt](create-device-update-account.md), beleértve a IoT hub konfigurálását.

## <a name="download-image"></a>Rendszerkép letöltése

Az adott [eszközön a GitHub kiadásában](https://github.com/Azure/iot-hub-device-update/releases)három kép érhető el az "eszközök" részeként. Az alaprendszerkép (adu-Base-rendszerkép) és egy frissítési rendszerkép (adu-Update-rendszerkép) is elérhető, így az eszközön nem szükséges az SD-kártya villanása. Ehhez az importálás részeként fel kell töltenie a frissítési lemezképeket IoT Hub szolgáltatás frissítésére.

## <a name="flash-sd-card-with-image"></a>Flash SD-kártya képpel

A kedvenc operációs rendszerének villogási eszközének használatával telepítse az eszköz frissítése alaprendszerképét (adu-Base-kép) az SD-kártyára, amelyet a málna PI 3 B + eszközben fog használni.

### <a name="using-bmaptool-to-flash-sd-card"></a>A bmaptool használata a Flash SD-kártyával

1. Ha még nem tette meg, telepítse a `bmaptool` segédprogramot.

   ```shell
   sudo apt-get install bmap-tools
   ```

2. Keresse meg az SD-kártya elérési útját a alkalmazásban `/dev` . Az elérési útnak a következőhöz hasonlóan kell kinéznie: `/dev/sd*` vagy `/dev/mmcblk*` . A `dmesg` segédprogram segítségével megkeresheti a helyes elérési utat.

3. A villogás előtt le kell választania az összes csatlakoztatott partíciót.

   ```shell
   sudo umount /dev/<device>
   ```

4. Győződjön meg arról, hogy rendelkezik írási engedéllyel az eszközhöz.

   ```shell
   sudo chmod a+rw /dev/<device>
   ```

5. Választható. A gyorsabb villogáshoz töltse le a bimap fájlt a képfájl mellett, és helyezze őket ugyanabba a könyvtárba.

6. Az SD-kártya villanása.

   ```shell
   sudo bmaptool copy <path to image> /dev/<device>
   ```
   
Az Azure IoT Hub szoftverhez tartozó eszköz frissítése a következő licencfeltételeket illeti:
   * [IoT Hub licenccel rendelkező eszköz frissítése](https://github.com/Azure/iot-hub-device-update/blob/main/LICENSE.md)
   * [Kézbesítési optimalizálási ügyféllicenc](https://github.com/microsoft/do-client/blob/main/LICENSE)
   
Olvassa el az ügynök használata előtti licencfeltételeket. A telepítés és a használat a jelen feltételek elfogadását jelenti. Ha nem fogadja el a licencfeltételeket, ne használja az eszköz frissítését IoT Hub ügynökhöz.

## <a name="create-device-in-iot-hub-and-get-connection-string"></a>Eszköz létrehozása IoT Hubban és a kapcsolatok karakterláncának beolvasása

Az eszközt most fel kell venni az Azure IoT Hubba.  Az Azure IoT Hubban létrejön egy, az eszközhöz tartozó kapcsolatok karakterlánca.

1. A Azure Portal indítsa el az Azure IoT Hubt.
2. Hozzon létre egy új eszközt.
3. A lap bal oldalán navigáljon a "felfedezők" > "IoT-eszközök" > válassza az "új" lehetőséget.
4. Adja meg az eszköz nevét az "eszköz azonosítója" alatt – ellenőrizze, hogy be van-e jelölve az "autogeneráló kulcsok" jelölőnégyzet.
5. Válassza a mentés lehetőséget.
6. Most visszakerül az "eszközök" oldalra, és a létrehozott eszköznek szerepelnie kell a listán. Válassza ki az eszközt.
7. Az eszköz nézetben válassza az "elsődleges kapcsolódási karakterlánc" melletti "másolás" ikont.
8. Illessze be a másolt karaktereket valahol az alábbi lépésekben a későbbi használatra.
   **Ez a másolt karakterlánc az eszköz-kapcsolatok karakterlánca**.

## <a name="provision-connection-string-on-sd-card"></a>A kapcsolatok karakterláncának kiépítése SD-kártyán

1. Győződjön meg arról, hogy a málna Pi3 csatlakozik a hálózathoz.
2. A PowerShellben használja az alábbi parancsot az SSH-ba az eszközön
   ```markdown
   ssh raspberrypi3 -l root
      ```
4. Adja meg a "root" karakterláncot, és a jelszót üresen kell hagyni.
5. Miután sikeresen elvégezte az SSH-t az eszközön, futtassa az alábbi parancsokat
 
Cserélje le a `<device connection string>` karakterláncot a saját kapcsolatok karakterlánccá
 ```markdown
    echo "connection_string=<device connection string>" > /adu/adu-conf.txt  
    echo "aduc_manufacturer=ADUTeam" >> /adu/adu-conf.txt
    echo "aduc_model=RefDevice" >> /adu/adu-conf.txt
   ```

## <a name="connect-the-device-in-device-update-iot-hub"></a>Az eszköz csatlakoztatása az eszköz frissítése IoT Hub

1. Az oldal bal oldalán válassza a "felfedezők" alatt az "IoT-eszközök" lehetőséget.
2. Válassza ki a hivatkozást az eszköz nevével.
3. Az oldal tetején válassza az "eszköz Twin" lehetőséget.
4. Az eszköz Twin tulajdonságainak "jelentett" szakaszában keresse meg a Linux kernel verzióját.
Egy olyan új eszköz esetében, amely nem kapott frissítést az eszköz frissítéséről, a [DeviceManagement: DeviceInformation: 1. swVersion](device-update-plug-and-play.md) érték az eszközön futó belső vezérlőprogram verzióját fogja reprezentálni.  Ha egy frissítést alkalmaztak egy eszközre, az eszköz frissítése a [AzureDeviceUpdateCore: ClientMetadata: 4. installedUpdateId](device-update-plug-and-play.md) tulajdonság értékét fogja használni az eszközön futó belső vezérlőprogram verziójának megjelenítéséhez.
5. Az alap-és frissítési képfájlok verziószáma a fájlnév.

   ```markdown
   adu-<image type>-image-<machine>-<version number>.<extension>
   ```

Használja ezt a verziószámot az alábbi frissítés importálása lépésben.

## <a name="add-a-tag-to-your-device"></a>Címke hozzáadása az eszközhöz

1. Jelentkezzen be [Azure Portalba](https://portal.azure.com) , és navigáljon a IoT hub.

2. A bal oldali navigációs ablaktáblán található "IoT-eszközök" vagy "IoT Edge" közül keresse meg a IoT eszközt, és navigáljon a Twin vagy a modulhoz.

3. Az eszköz-frissítési ügynök moduljának különálló moduljában törölje a meglévő eszköz frissítési címke értékét a NULL értékre állításával. Ha eszköz-identitást használ az eszköz frissítési ügynökével, végezze el ezeket a módosításokat az eszközön.

4. Vegyen fel egy új eszköz frissítési címke értékét az alább látható módon.

```JSON
    "tags": {
            "ADUGroup": "<CustomTagValue>"
            }
```

## <a name="import-update"></a>Frissítés importálása

1. Az alábbi [utasításokat](import-update.md)követve hozzon létre egy importálási jegyzékfájlt.
2. Válassza ki az eszközök frissítései lehetőséget a bal oldali navigációs sávon az automatikus eszközkezelés lehetőség alatt.
3. Válassza a frissítések lapot.
4. Válassza az "+ új frissítés importálása" lehetőséget.
5. Válassza ki a mappa ikont vagy szövegmezőt a "Select a manifest file List" (fájl importálása) elem alatt. Ekkor megjelenik egy fájl-választó párbeszédpanel. Válassza ki a fentiekben létrehozott importálási jegyzékfájlt.  Ezután válassza a mappa ikont vagy a szövegmezőt a "válasszon ki egy vagy több frissítési fájlt" területen. Ekkor megjelenik egy fájl-választó párbeszédpanel. Válassza ki a IoT-eszközökre telepíteni kívánt frissítési fájlt.
   
   :::image type="content" source="media/import-update/select-update-files.png" alt-text="A frissítési fájl kijelölését bemutató képernyőkép." lightbox="media/import-update/select-update-files.png":::

5. Válassza a mappa ikont vagy a szövegmezőt a "Storage-tároló kiválasztása" területen. Ezután válassza ki a megfelelő Storage-fiókot.

6. Ha már létrehozott egy tárolót, újra használhatja azt. (Ellenkező esetben válassza a "+ tároló" lehetőséget a frissítések új tárolójának létrehozásához.).  Válassza ki a használni kívánt tárolót, és kattintson a Kiválasztás gombra.
  
  :::image type="content" source="media/import-update/container.png" alt-text="A tároló kijelölését bemutató képernyőkép." lightbox="media/import-update/container.png":::

7. Az importálási folyamat elindításához válassza a Küldés lehetőséget.

8. Az importálási folyamat megkezdődik, és a képernyő az "importálási előzmények" szakaszra változik. Válassza a frissítés lehetőséget, ha az importálási folyamat befejeződéséig szeretné megtekinteni a folyamatot. A frissítés méretétől függően ez néhány percen belül elvégezhető, de hosszabb időt is igénybe vehet.
   
   :::image type="content" source="media/import-update/update-publishing-sequence-2.png" alt-text="Az importálási folyamat frissítését bemutató képernyőkép." lightbox="media/import-update/update-publishing-sequence-2.png":::

9. Ha az Állapot oszlop jelzi, hogy az importálás sikeres volt, válassza a "készen áll a telepítésre" fejlécet. Ekkor megjelenik az importált frissítés a listában.

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

Sikeresen befejezte a teljes rendszerkép-frissítést a IoT Hub eszköz frissítésével a málna PI 3 B + eszközön. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az eszköz frissítési fiókját, példányát, IoT Hub és IoT eszközét. 

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Szimulátor-hivatkozási ügynök](device-update-simulator.md)
