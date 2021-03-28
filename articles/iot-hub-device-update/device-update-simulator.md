---
title: Eszköz frissítése az Azure IoT Hub oktatóanyaghoz az Ubuntu (18,04 x64) szimulátor hivatkozási ügynök használatával | Microsoft Docs
description: Ismerkedjen meg az Azure IoT Hub eszköz frissítésével az Ubuntu (18,04 x64) szimulátor-hivatkozási ügynök használatával.
author: valls
ms.author: valls
ms.date: 2/11/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: 8cbc101ec9b5608574b93ea17022a3b0ef02ce4b
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2021
ms.locfileid: "105644415"
---
# <a name="device-update-for-azure-iot-hub-tutorial-using-the-ubuntu-1804-x64-simulator-reference-agent"></a>Eszköz frissítése az Azure IoT Hub oktatóanyaghoz az Ubuntu (18,04 x64) szimulátor hivatkozási ügynök használatával

A IoT Hub eszköz frissítése két frissítési formát támogat – a rendszerkép-alapú és a Package-alapú.

A rendszerkép frissítései magasabb szintű megbízhatóságot biztosítanak az eszköz állapotában. Általában könnyebb replikálni az éles üzem előtti környezet és az éles környezet közötti rendszerképek eredményeit, mivel az nem ugyanazokat a kihívásokat eredményezi, mint a csomagok és a függőségeik. Az Atom jellegéből adódóan az egyik A/B feladatátvételi modellt is könnyedén elvégezheti.

Ez az oktatóanyag végigvezeti azokon a lépéseken, amelyekkel a IoT Hub eszköz frissítésével teljes körű rendszerkép-alapú frissítést hajthat végre. 

Ezen oktatóanyag segítségével megtanulhatja a következőket:
> [!div class="checklist"]
> * Rendszerkép letöltése és telepítése
> * Címke hozzáadása a IoT-eszközhöz
> * Frissítés importálása
> * Eszközcsoport létrehozása
> * Lemezkép frissítésének központi telepítése
> * A frissítés központi telepítésének figyelése

## <a name="prerequisites"></a>Előfeltételek
* Ha még nem tette meg, hozzon létre egy [eszköz-frissítési fiókot és-példányt](create-device-update-account.md), beleértve a IoT hub konfigurálását.

### <a name="download-and-install"></a>Letöltés és telepítés

* Az az (Azure CLI) parancsmagok a PowerShellhez:
  * Nyissa meg a PowerShell > telepítse az Azure CLI-t ("Y", a "nem megbízható" forrásból történő telepítésre vonatkozó kérések esetén)

```powershell
PS> Install-Module Az -Scope CurrentUser
```

### <a name="enable-wsl-on-your-windows-device-windows-subsystem-for-linux"></a>A WSL engedélyezése a Windows-eszközön (Linux Windows alrendszere)

1. Nyissa meg a PowerShellt rendszergazdaként a gépen, és futtassa a következő parancsot (Előfordulhat, hogy az egyes lépések után újra kell indítania az újraindítást, ha a rendszer kéri):

```powershell
PS> Enable-WindowsOptionalFeature -Online -FeatureName VirtualMachinePlatform
PS> Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux
```

(*Előfordulhat, hogy a lépés után újra kell indítania a* rendszert)

2. Nyissa meg a Microsoft Store a weben, és telepítse az [Ubuntu 18,04 LTS](https://www.microsoft.com/p/ubuntu-1804-lts/9n9tngvndl3q?activetab=pivot:overviewtab`)-et.

3. Indítsa el az "Ubuntu 18,04 LTS" parancsot, és telepítse.

4. Ha telepítve van, a rendszer megkéri, hogy adja meg a gyökér nevét (username) és a jelszót. Ügyeljen arra, hogy egy emlékezetes root name jelszót használjon.

5. A PowerShellben futtassa a következő parancsot az Ubuntu alapértelmezett Linux-disztribúcióként való beállításához:

```powershell
PS> wsl --setdefault Ubuntu-18.04
```

6. Sorolja fel az összes Linux-disztribúciót, győződjön meg arról, hogy az Ubuntu az alapértelmezett.

```powershell
PS> wsl --list
```

7. A következőnek kell megjelennie: **Ubuntu-18,04 (alapértelmezett)**

## <a name="download-device-update-ubuntu-1804-x64-simulator-reference-agent"></a>Az eszköz frissítésének Ubuntu (18,04 x64) szimulátor-hivatkozási ügynök letöltése

Az Ubuntu Update-rendszerkép letölthető az *eszközök* szakaszból a [kibocsátási megjegyzésekből](https://github.com/Azure/iot-hub-device-update/releases).

Az ügynöknek két verziója van. Ha rendszerkép-alapú forgatókönyvet használ, használja a AducIotAgentSim-Microsoft-swupdate, és ha csomag-alapú forgatókönyvet használ, használja a AducIotAgentSim-Microsoft-apt-t.

## <a name="install-device-update-agent-simulator"></a>Az eszköz frissítési ügynökének szimulátor telepítése

1. Indítsa el az Ubuntu WSL, és írja be a következő parancsot (vegye figyelembe, hogy a végén extra szóköz és pont).

  ```shell
  explorer.exe .
  ```

2. Másolja a AducIotAgentSim-Microsoft-swupdate (vagy a AducIotAgentSim-Microsoft-apt) mappát a helyi mappájából, ahol a WSL-ben letöltötte a/mnt-ban a saját mappájába.

3. Futtassa a következő parancsot a bináris fájlok végrehajtható fájljának elvégzéséhez.

  ```shell
  sudo chmod u+x AducIotAgentSim-microsoft-swupdate
  ```

  vagy

  ```shell
  sudo chmod u+x AducIotAgentSim-microsoft-apt
  ```
Az Azure IoT Hub szoftverhez tartozó eszköz frissítése a következő licencfeltételeket illeti:
   * [IoT Hub licenccel rendelkező eszköz frissítése](https://github.com/Azure/iot-hub-device-update/blob/main/LICENSE.md)
   * [Kézbesítési optimalizálási ügyféllicenc](https://github.com/microsoft/do-client/blob/main/LICENSE)
   
Olvassa el az ügynök használata előtti licencfeltételeket. A telepítés és a használat a jelen feltételek elfogadását jelenti. Ha nem fogadja el a licencfeltételeket, ne használja az eszköz frissítését IoT Hub ügynökhöz.

## <a name="add-device-to-azure-iot-hub"></a>Eszköz hozzáadása az Azure IoT Hubhoz

Ha az eszköz frissítési ügynöke IoT-eszközön fut, az eszközt fel kell venni az Azure IoT Hubba.  Az Azure IoT Hubon egy adott eszközhöz létrejön egy kapcsolatok karakterlánca.

1. A Azure Portal indítsa el az eszköz frissítési IoT Hubját.
2. Hozzon létre egy új eszközt.
3. A lap bal oldalán navigáljon a "felfedezők" > "IoT-eszközök" > válassza az "új" lehetőséget.
4. Adja meg az eszköz nevét az "eszköz azonosítója" alatt – ellenőrizze, hogy be van-e jelölve az "autogeneráló kulcsok" jelölőnégyzet.
5. Válassza a mentés lehetőséget.
6. Most visszakerül az "eszközök" oldalra, és a létrehozott eszköznek szerepelnie kell a listán. Válassza ki az eszközt.
7. Az eszköz nézetben válassza az "elsődleges kapcsolódási karakterlánc" melletti "másolás" ikont.
8. Illessze be a másolt karaktereket valahol az alábbi lépésekben a későbbi használatra. **Ez a másolt karakterlánc az eszköz-kapcsolatok karakterlánca**.

## <a name="add-connection-string-to-simulator"></a>Kapcsolódási sztring hozzáadása a szimulátorhoz

Indítsa el az eszköz frissítési ügynökét az új szoftvereket futtató eszközökön.

1. Indítsa el az Ubuntut.
2. Futtassa az eszköz frissítési ügynökét, és adja meg az előző szakasz aposztrófokkal becsomagolt eszköz-csatlakoztatási karakterláncát:

Cserélje le a `<device connection string>` karakterláncot a saját kapcsolatok karakterlánccá
```shell
./AducIotAgentSim-microsoft-swupdate -c '<device connection string>'
```

vagy

```shell
./AducIotAgentSim-microsoft-apt -c '<device connection string>'
```

3. Görgessen felfelé, és keresse meg azt a karakterláncot, amely azt jelzi, hogy az eszköz "tétlen" állapotban van. Az "inaktív" állapot azt jelenti, hogy az eszköz készen áll a szolgáltatási parancsokra:

```markdown
Agent running. [main]
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

1. Az alábbi [utasításokat](import-update.md)követve hozzon létre egy importálási jegyzékfájlt.
2. Válassza ki az eszközök frissítései lehetőséget a bal oldali navigációs sávon az automatikus eszközkezelés lehetőség alatt.

3. Válassza a frissítések lapot.

4. Válassza az "+ új frissítés importálása" lehetőséget.

5. Válassza ki a mappa ikont vagy szövegmezőt a "Select a manifest file List" (fájl importálása) elem alatt. Ekkor megjelenik egy fájl-választó párbeszédpanel. Válassza ki a fentiekben létrehozott importálási jegyzékfájlt.  Ezután válassza a mappa ikont vagy a szövegmezőt a "válasszon ki egy vagy több frissítési fájlt" területen. Ekkor megjelenik egy fájl-választó párbeszédpanel. Válassza ki a korábban letöltött Ubuntu Update-rendszerképet. 

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

A IoT Hub az Ubuntu (18,04 x64) szimulátor hivatkozási ügynök használatával sikeresen befejezte a teljes rendszerkép frissítését az eszköz frissítésével.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az eszköz frissítési fiókját, példányát, IoT Hub és IoT eszközét. 

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Hibaelhárítás](troubleshoot-device-update.md)

