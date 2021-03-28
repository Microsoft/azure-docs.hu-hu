---
title: Eszköz frissítése az Azure IoT Hub oktatóanyaghoz az Ubuntu Server 18,04 x64 Package Agent használatával | Microsoft Docs
description: Ismerkedjen meg az Azure IoT Hub eszköz frissítésével az Ubuntu Server 18,04 x64 Package Agent használatával.
author: vimeht
ms.author: vimeht
ms.date: 2/16/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: ee09928cab6419d799d06de9cf2f69987e42d157
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2021
ms.locfileid: "105644439"
---
# <a name="device-update-for-azure-iot-hub-tutorial-using-the-package-agent-on-ubuntu-server-1804-x64"></a>Eszköz frissítése az Azure IoT Hub oktatóanyaghoz az Ubuntu Server 18,04 x64-es csomag ügynökének használatával

A IoT Hub eszköz frissítése két frissítési formát támogat – a rendszerkép-alapú és a Package-alapú.

A Package-alapú frissítések olyan célzott frissítések, amelyek csak egy adott összetevőt vagy alkalmazást módosítanak az eszközön. Ez csökkenti a sávszélesség-használatot, és csökkenti a frissítés letöltésének és telepítésének idejét. A csomagok frissítései általában lehetővé teszik, hogy az eszközök kevesebb állásidőt alkalmazzanak, amikor frissítést alkalmaznak, és elkerülik a lemezképek létrehozásának terhelését.

Ez az oktatóanyag végigvezeti azokon a lépéseken, amelyekkel teljes körű, a IoT Hub eszköz frissítésén keresztül végezhető el a csomagon alapuló frissítés. Ebben az oktatóanyagban egy Ubuntu Server 18,04 x64 rendszert futtató Azure IoT Edge és az eszköz-frissítési csomag ügynökét használjuk. Az oktatóanyag bemutatja a minta-csomagok frissítését, de a hasonló lépések használatával más csomagokat is frissíthet, például Azure IoT Edge vagy az általa használt tároló motort.

Az oktatóanyag eszközei és fogalmai még akkor is érvényesek, ha eltérő operációsrendszer-platform-konfigurációt tervez használni. Fejezze be ezt a bevezetést egy teljes körű frissítési folyamatra, majd válassza ki a frissítés és az operációs rendszer platform előnyben részesített formáját a részletek kiszámításához.

Ezen oktatóanyag segítségével megtanulhatja a következőket:
> [!div class="checklist"]
> * Az eszköz frissítési ügynökének és függőségeinek letöltése és telepítése
> * Címke hozzáadása az eszközhöz
> * Frissítés importálása
> * Eszközcsoport létrehozása
> * Csomag frissítésének központi telepítése
> * A frissítés központi telepítésének figyelése

## <a name="prerequisites"></a>Előfeltételek

* Ha még nem tette meg, hozzon létre egy [eszköz-frissítési fiókot és-példányt](create-device-update-account.md), beleértve a IoT hub konfigurálását.
* [IoT Edge eszközhöz tartozó kapcsolatok karakterlánca](../iot-edge/how-to-register-device.md?view=iotedge-2020-11&preserve-view=true#view-registered-devices-and-retrieve-connection-strings).

## <a name="prepare-a-device"></a>Eszköz előkészítése
### <a name="using-the-automated-deploy-to-azure-button"></a>Az automatikus üzembe helyezés az Azure-ban gomb használata

Ez az oktatóanyag egy [Cloud-init](../virtual-machines/linux/using-cloud-init.md)-alapú [Azure Resource Manager sablonnal](../azure-resource-manager/templates/overview.md) segíti az Ubuntu 18,04 LTS virtuális gép gyors beállítását. Telepíti mind a Azure IoT Edge futtatókörnyezetet, mind az eszköz-frissítési csomag ügynököt, majd automatikusan konfigurálja az eszközt a kiépítési adatokkal egy IoT Edge eszközhöz (előfeltételként) használt eszköz-csatlakoztatási karakterlánccal. Ezzel elkerülhető, hogy a telepítés befejezéséhez el kell indítania egy SSH-munkamenetet.

1. A kezdéshez kattintson az alábbi gombra:

   [![Az iotedge-vm-deploy sablon Üzembe helyezés az Azure-ban gombja](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure%2Fiotedge-vm-deploy%2Fdevice-update-tutorial%2FedgeDeploy.json)

1. Az újonnan elindított ablakban töltse ki a rendelkezésre álló űrlapmezők mezőket:

    > [!div class="mx-imgBorder"]
    > [![Az iotedge-vm-deploy sablon képernyőképe](../iot-edge/media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-deploy.png)](../iot-edge/media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-deploy.png)

    **Előfizetés**: a virtuális gép üzembe helyezéséhez szükséges aktív Azure-előfizetés.

    **Erőforráscsoport**: egy meglévő vagy újonnan létrehozott erőforráscsoport, amely tartalmazza a virtuális gépet és a hozzá társított erőforrásokat.

    **DNS-címke előtagja**: a választott érték, amelyet a virtuális gép állomásnévének előtagjaként kell használni.

    **Rendszergazdai Felhasználónév**: a Felhasználónév, amely rendszergazdai jogosultságokkal fog szolgálni az üzembe helyezéshez.

    **Eszköz-összekapcsolási karakterlánc**: egy eszközhöz tartozó, a kívánt [IoT Hubban](../iot-hub/about-iot-hub.md)létrehozott eszköz- [kapcsolatok karakterlánca](../iot-edge/how-to-register-device.md) .

    **VM-méret**: a telepítendő virtuális gép [mérete](../cloud-services/cloud-services-sizes-specs.md)

    **Ubuntu operációs rendszer verziója**: az Ubuntu operációs rendszer azon verziója, amelyet az alapszintű virtuális gépre kell telepíteni. Hagyja változatlanul az alapértelmezett értéket, mivel az Ubuntu 18,04-LTS értékre lesz beállítva.

    **Hely**: az a [földrajzi régió](https://azure.microsoft.com/global-infrastructure/locations/) , amelybe a virtuális gépet üzembe helyezi, ez az érték alapértelmezés szerint a kiválasztott erőforráscsoport helyét jelöli.

    **Hitelesítés típusa**: válassza a **sshPublicKey** vagy a **jelszó** lehetőséget a beállítástól függően.

    **Rendszergazdai jelszó vagy kulcs**: az SSH nyilvános kulcs értéke vagy a jelszó értéke a választott hitelesítési típustól függően.

    Ha az összes mezőt kitöltötte, jelölje be az oldal alján található jelölőnégyzetet a feltételek elfogadásához, majd válassza a **vásárlás** lehetőséget a telepítés megkezdéséhez.

1. Ellenőrizze, hogy az üzembe helyezés sikeresen befejeződött-e. Az üzembe helyezés után néhány percet is igénybe vehet, hogy a telepítés után és a konfigurációban befejeződjön a IoT Edge és az eszköz-csomag frissítési ügynökének telepítése.

   A kiválasztott erőforráscsoportban meg kellett történnie egy virtuális gép erőforrás üzembe helyezésnek.  Jegyezze fel a gép nevét, amelynek formátuma a következő: `vm-0000000000000` . Jegyezze fel a hozzá tartozó **DNS-név** értékét is. Ennek formátuma: `<dnsLabelPrefix>`.`<location>`.cloudapp.azure.com.

    A **DNS-név** az újonnan üzembe helyezett virtuális gép **Áttekintés** szakaszában állapítható meg az Azure Portalon.

    > [!div class="mx-imgBorder"]
    > [![A iotedge virtuális gép DNS-nevét bemutató képernyőkép](../iot-edge/media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)](../iot-edge/media/how-to-install-iot-edge-ubuntuvm/iotedge-vm-dns-name.png)

   > [!TIP]
   > Ha a telepítés után SSH-t szeretne használni a virtuális gépen, használja a társított **DNS-nevet** a következő paranccsal: `ssh <adminUsername>@<DNS_Name>`

### <a name="optional-manually-prepare-a-device"></a>Választható Eszköz manuális előkészítése
Az eszköz telepítésének és konfigurálásának alábbi manuális lépései egyenértékűek a [Cloud-init parancsfájl](https://github.com/Azure/iotedge-vm-deploy/blob/1.2.0-rc4/cloud-init.txt)által automatikusan használt beállításokkal. Felhasználhatók fizikai eszközök előkészítésére is.

1. Az [Azure IoT Edge futtatókörnyezet telepítéséhez](../iot-edge/how-to-install-iot-edge.md?view=iotedge-2020-11&preserve-view=true)kövesse az utasításokat.
   > [!NOTE]
   > Az eszköz frissítési csomagjának ügynöke nem függ IoT Edgetól. Ez azonban a IoT Edge (1.2.0 és újabb) rendszerű IoT Identity Service démonra támaszkodik az identitás beszerzéséhez és a IoT Hubhoz való kapcsolódáshoz.
   >
   > Bár ez az oktatóanyag nem foglalkozik, a [IoT Identity Service démon a Linux-alapú IoT-eszközökön is telepíthető](https://azure.github.io/iot-identity-service/packaging.html). A telepítési ügyek sora. Az eszköz-frissítési csomag ügynökét telepíteni kell a IoT Identity Service _után_ . Ellenkező esetben a csomag ügynöke nem lesz regisztrálva meghatalmazott összetevőként a IoT Hubhoz való kapcsolat létesítéséhez.

1. Ezután telepítse az eszköz Update Agent. deb csomagokat.

   ```bash
   sudo apt-get install deviceupdate-agent deliveryoptimization-plugin-apt 
   ```

Az Azure IoT Hub szoftvercsomag eszközének frissítése a következő licencfeltételek hatálya alá tartozik:
  * [IoT Hub licenccel rendelkező eszköz frissítése](https://github.com/Azure/iot-hub-device-update/blob/main/LICENSE.md)
  * [Kézbesítési optimalizálási ügyféllicenc](https://github.com/microsoft/do-client/blob/main/LICENSE)

A licencfeltételek elolvasása a csomag használata előtt. A csomagok telepítése és használata a jelen feltételek elfogadását jelenti. Ha nem fogadja el a licencfeltételeket, ne használja ezt a csomagot.

## <a name="add-a-tag-to-your-device"></a>Címke hozzáadása az eszközhöz

1. Jelentkezzen be [Azure Portalba](https://portal.azure.com) , és navigáljon a IoT hub.

2. A bal oldali navigációs ablaktáblán található "IoT Edge" listából keresse meg IoT Edge eszközét, és navigáljon a Twin (eszközök) panelre.

3. A Twin (eszközök) készletben törölje a meglévő eszköz frissítési címke értékét a NULL értékre állításával.

4. Vegyen fel egy új eszköz frissítési címke értékét az alább látható módon.

```JSON
    "tags": {
            "ADUGroup": "<CustomTagValue>"
            },
```

## <a name="import-update"></a>Frissítés importálása

1. Nyissa meg az [eszköz frissítési kiadásait](https://github.com/Azure/iot-hub-device-update/releases) a githubon, és kattintson az "eszközök" legördülő listára.

3. A letöltéséhez `Edge.package.update.samples.zip` kattintson rá.

5. Bontsa ki a mappa tartalmát, hogy felderítse a frissítési mintát és a hozzá tartozó importálási jegyzékeket. 

2. Azure Portal válassza ki az eszközök frissítései lehetőséget az automatikus eszközkezelés lehetőségnél a IoT Hub bal oldali navigációs sávján.

3. Válassza a frissítések lapot.

4. Válassza az "+ új frissítés importálása" lehetőséget.

5. Válassza ki a mappa ikont vagy szövegmezőt a "Select a manifest file List" (fájl importálása) elem alatt. Ekkor megjelenik egy fájl-választó párbeszédpanel. Válassza ki az `sample-1.0.1-aziot-edge-importManifest.json` importálási jegyzékfájlt a korábban letöltött mappából. Ezután válassza a mappa ikont vagy a szövegmezőt a "válasszon ki egy vagy több frissítési fájlt" területen. Ekkor megjelenik egy fájl-választó párbeszédpanel. Válassza ki az `sample-1.0.1-aziot-edge-apt-manifest.json` apt manifest Update fájlt a korábban letöltött mappából.
Ez a frissítés frissíti a `aziot-identity-service` és a `aziot-edge` csomagokat az eszközön az 1.2.0 ~ RC4-1 verzióra.

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

1. Válassza ki az eszközök frissítései lehetőséget a bal oldali navigációs sávon az automatikus eszközkezelés lehetőség alatt.

1. Válassza a csoportok fület az oldal tetején.

1. Új csoport létrehozásához kattintson a Hozzáadás gombra.

1. Válassza ki az előző lépésben létrehozott IoT Hub címkét a listából. Válassza a frissítési csoport létrehozása lehetőséget.

   :::image type="content" source="media/create-update-group/select-tag.PNG" alt-text="A címke kijelölését bemutató képernyőfelvétel" lightbox="media/create-update-group/select-tag.PNG":::

[További](create-update-group.md) információ a címkék hozzáadásáról és a frissítési csoportok létrehozásáról

## <a name="deploy-update"></a>Frissítés telepítése

1. Miután létrejött a csoport, megjelenik egy új frissítés az eszközcsoport számára, amely a frissítésre mutató hivatkozást is tartalmazza az _elérhető frissítések_ oszlopban. Előfordulhat, hogy egyszer kell frissítenie.

1. Kattintson a rendelkezésre álló frissítésre mutató hivatkozásra.

1. Ellenőrizze, hogy a megfelelő csoport van-e kiválasztva a célcsoportként, és ütemezze az üzembe helyezést

   :::image type="content" source="media/deploy-update/select-update.png" alt-text="Frissítés kiválasztása" lightbox="media/deploy-update/select-update.png":::

   > [!TIP]
   > Alapértelmezés szerint a kezdő dátum/idő 24 óra az aktuális időpontból. Ha azt szeretné, hogy a központi telepítés hamarabb kezdődjön, válasszon másik dátumot és időpontot.

1. Válassza a frissítés központi telepítése lehetőséget.

1. Tekintse meg a megfelelőségi diagramot. Ekkor már folyamatban van a frissítés. 

   :::image type="content" source="media/deploy-update/update-in-progress.png" alt-text="Frissítés folyamatban" lightbox="media/deploy-update/update-in-progress.png":::

1. Az eszköz sikeres frissítése után a megfelelőségi diagram és az üzembe helyezési adatok frissítése megjelenik, hogy az tükrözze ugyanezt. 

   :::image type="content" source="media/deploy-update/update-succeeded.png" alt-text="Sikeres frissítés" lightbox="media/deploy-update/update-succeeded.png":::

## <a name="monitor-an-update-deployment"></a>Frissítések központi telepítésének figyelése

1. Válassza a központi telepítések fület az oldal tetején.

   :::image type="content" source="media/deploy-update/deployments-tab.png" alt-text="Központi telepítések lap" lightbox="media/deploy-update/deployments-tab.png":::

1. Válassza ki a létrehozott központi telepítést a központi telepítés részleteinek megtekintéséhez.

   :::image type="content" source="media/deploy-update/deployment-details.png" alt-text="Központi telepítés részletei" lightbox="media/deploy-update/deployment-details.png":::

1. Kattintson a frissítés elemre a legutóbbi állapot részleteinek megtekintéséhez. Folytassa ezt a folyamatot, amíg az állapot sikeresen nem változik.

Sikeresen befejezte a teljes csomag frissítését az eszköz frissítésével IoT Hub egy Ubuntu Server 18,04 x64-es eszközön. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az eszköz frissítési fiókját, példányát, IoT Hub és a IoT Edge eszközt (ha a virtuális gépet az üzembe helyezés az Azure-ban gomb használatával hozta létre). Ezt úgy teheti meg, hogy az egyes erőforrásokat és a "Törlés" lehetőséget választja. Vegye figyelembe, hogy az eszköz frissítési fiókjának tisztítása előtt törölnie kell egy eszköz frissítési példányát.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Kép frissítése a málna PI 3 B + oktatóanyagban](device-update-raspberry-pi.md)
