---
title: BLOB Storage üzembe helyezése modulon az eszközön – Azure IoT Edge
description: Helyezzen üzembe egy Azure Blob Storage modult a IoT Edge eszközön, hogy az adatait a peremhálózat szélén tárolja.
author: kgremban
ms.author: kgremban
ms.date: 3/10/2020
ms.topic: conceptual
ms.service: iot-edge
ms.reviewer: arduppal
ms.openlocfilehash: f766a77ee55351e498a379146826ba1d5507bc93
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2021
ms.locfileid: "103201172"
---
# <a name="deploy-the-azure-blob-storage-on-iot-edge-module-to-your-device"></a>Az Azure Blob Storage az IoT Edge-ben modul üzembe helyezése az eszközön

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

A modulok több módon is üzembe helyezhetők egy IoT Edge eszközön, és mindegyikük az Azure Blob Storage IoT Edge-modulokban való működéséhez. A két legegyszerűbb módszer a Azure Portal vagy a Visual Studio Code-sablonok használata.

## <a name="prerequisites"></a>Előfeltételek

- Egy [IoT hub](../iot-hub/iot-hub-create-through-portal.md) az Azure-előfizetésében.
- Egy IoT Edge eszköz.

  Ha nincs beállítva IoT Edge eszköz, létrehozhat egyet egy Azure-beli virtuális gépen. A [virtuális Linux-eszköz létrehozásához](quickstart-linux.md) vagy [virtuális Windows-eszköz létrehozásához](quickstart.md)kövesse az egyik rövid útmutató lépéseit.

- A [Visual Studio Code](https://code.visualstudio.com/) és az [Azure IoT eszközei](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) , ha a Visual Studio Code-ból telepítenek.

## <a name="deploy-from-the-azure-portal"></a>Üzembe helyezés a Azure Portal

A Azure Portal végigvezeti az üzembe helyezési jegyzék létrehozásán és az üzembe helyezés egy IoT Edge eszközön való továbbításának végrehajtásán.

### <a name="select-your-device"></a>Eszköz kiválasztása

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és navigáljon az IoT hubhoz.
1. A menüből válassza a **IoT Edge** lehetőséget.
1. Kattintson a céleszköz AZONOSÍTÓJÁRA az eszközök listájából.
1. Válassza a **modulok beállítása** lehetőséget.

### <a name="configure-a-deployment-manifest"></a>Központi telepítési jegyzék konfigurálása

Az üzembe helyezési jegyzék egy JSON-dokumentum, amely leírja, hogy mely modulokat kell telepíteni, hogyan zajlik az adatforgalom a modulok és a modul kívánt tulajdonságai között. A Azure Portal egy varázsló végigvezeti az üzembe helyezési jegyzék létrehozásán. Három lépést tartalmaz a következő lapokon: **modulok**, **útvonalak**, **felülvizsgálat + létrehozás**.

#### <a name="add-modules"></a>Modulok hozzáadása

1. A lap **IoT Edge modulok** szakaszában kattintson a legördülő **listára** , és válassza a **IoT Edge modul** lehetőséget az **IoT Edge modul hozzáadása** lap megjelenítéséhez.

2. A **modul beállításai** lapon adja meg a modul nevét, majd adja meg a tároló rendszerképének URI-ját:

   Angol nyelvű Példák:
  
   - **IoT Edge modul neve**: `azureblobstorageoniotedge`
   - **Rendszerkép URI-ja**: `mcr.microsoft.com/azure-blob-storage:latest`

   ![A képernyőképen az I o T Edge-modul hozzáadása lap modul beállításai lapján látható.](./media/how-to-deploy-blob/addmodule-tab1.png)

   Ne válassza a **Hozzáadás** lehetőséget, amíg meg nem adta a **modul beállításai**, a tároló- **létrehozási beállítások** és a  **modul Twin-beállítások** lapjait a jelen eljárásban leírtak szerint.

   > [!IMPORTANT]
   > A Azure IoT Edge a kis-és nagybetűk megkülönböztetése, ha a modulokra irányuló hívásokat végez, és a Storage SDK is alapértelmezés szerint kisbetűs. Bár az [Azure Marketplace](how-to-deploy-modules-portal.md#deploy-modules-from-azure-marketplace) -en a modul neve **AzureBlobStorageonIoTEdge**, a név kisbetűsre való módosítása segít biztosítani, hogy az Azure Blob Storage IoT Edge modulban való kapcsolatai ne legyenek megszakítva.

3. Nyissa meg a **tároló létrehozása beállítások** lapot.

   ![Képernyőfelvétel: az I o T Edge-modul hozzáadása lap tároló létrehozási beállítások lapja.](./media/how-to-deploy-blob/addmodule-tab3.png)

   Másolja és illessze be a következő JSON-t a mezőbe, hogy megadja a Storage-fiók adatait és a tárolóhoz való csatlakoztatást az eszközön.
  
   ```json
   {
     "Env":[
       "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
       "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>"
     ],
     "HostConfig":{
       "Binds":[
           "<storage mount>"
       ],
       "PortBindings":{
         "11002/tcp":[{"HostPort":"11002"}]
       }
     }
   }
   ```

4. Frissítse a **tároló-létrehozási beállításokba** MÁSOLt JSON-t a következő információkkal:

   - Cserélje le a `<your storage account name>` nevet, és jegyezze fel. A fiókok nevének 3 – 24 karakter hosszúnak kell lennie, kisbetűkkel és számokkal. Nincsenek szóközök.

   - Cserélje le `<your storage account key>` egy 64 bájtos Base64-kulccsal. Létrehozhat egy kulcsot olyan eszközökkel, mint a [GeneratePlus](https://generate.plus/en/base64). Ezeket a hitelesítő adatokat fogja használni a blob Storage más modulokból való eléréséhez.

   - Cserélje le `<storage mount>` at a tároló operációs rendszerének megfelelően. Adja meg egy [kötet](https://docs.docker.com/storage/volumes/) nevét vagy egy meglévő könyvtár abszolút elérési útját a IoT Edge eszközön, ahol a blob-modul tárolja az adatait. A Storage-csatlakoztatás leképezi az eszközön az Ön által megadott helyet a modul egy készletének megfelelő helyére.

     - Linux-tárolók esetén a formátum a következő **\<your storage path or volume> :/blobroot**. Például:
         - a [Volume Mount](https://docs.docker.com/storage/volumes/)használata: `my-volume:/blobroot`
         - [kötési csatlakoztatás](https://docs.docker.com/storage/bind-mounts/)használata: `/srv/containerdata:/blobroot` . Ügyeljen arra, hogy a címtár- [hozzáférés biztosítása a tároló felhasználójának](how-to-store-data-blob.md#granting-directory-access-to-container-user-on-linux) lépéseit kövesse.
     - Windows-tárolók esetén a formátum a következő **\<your storage path or volume> : C:/BlobRoot**. Például:
         - a [Volume Mount](https://docs.docker.com/storage/volumes/)használata: `my-volume:C:/BlobRoot` .
         - [kötési csatlakoztatás](https://docs.docker.com/storage/bind-mounts/)használata: `C:/ContainerData:C:/BlobRoot` .
         - A helyi meghajtó használata helyett leképezheti az SMB hálózati helyét, és további információkat az [SMB-megosztás használata helyi tárolóként](how-to-store-data-blob.md#using-smb-share-as-your-local-storage) című témakörben talál.

     > [!IMPORTANT]
     > Ne módosítsa a tárolási csatlakoztatási érték második felét, amely a IoT Edge modul Blob Storage egy adott helyére mutat. A tárolási csatlakoztatásnak mindig a következővel kell végződnie **:/blobroot** for Linux containers and **: C:/blobroot** for Windows containers.

5. A **modul Twin beállítások** lapján másolja be a következő JSON-t, és illessze be a mezőbe.

   ![A képernyőképen az I o T Edge-modul hozzáadása lap modul Twin beállítások lapja látható.](./media/how-to-deploy-blob/addmodule-tab4.png)

   Konfigurálja az egyes tulajdonságokat megfelelő értékkel, ahogy azt a helyőrzők is jelzik. Ha a IoT Edge szimulátort használja, állítsa be az értékeket a [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) és a [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties)által leírt tulajdonságok kapcsolódó környezeti változóinak megfelelően.

   ```json
   {
     "deviceAutoDeleteProperties": {
       "deleteOn": <true, false>,
       "deleteAfterMinutes": <timeToLiveInMinutes>,
       "retainWhileUploading": <true,false>
     },
     "deviceToCloudUploadProperties": {
       "uploadOn": <true, false>,
       "uploadOrder": "<NewestFirst, OldestFirst>",
       "cloudStorageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>; EndpointSuffix=<your end point suffix>",
       "storageContainersForUpload": {
         "<source container name1>": {
           "target": "<target container name1>"
         }
       },
       "deleteAfterUpload": <true,false>
     }
   }
   ```

   További információ a deviceToCloudUploadProperties és a deviceAutoDeleteProperties konfigurálásáról a modul üzembe helyezése után: [a különálló modul szerkesztése](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin). A kívánt tulajdonságokkal kapcsolatos további információkért lásd: a [kívánt tulajdonságok megadása vagy frissítése](module-composition.md#define-or-update-desired-properties).

6. Válassza a **Hozzáadás** lehetőséget.

7. Válassza a **Tovább: útvonalak** szakaszt az útvonalak szakaszhoz.

#### <a name="specify-routes"></a>Útvonalak meghatározása

Tartsa meg az alapértelmezett útvonalakat, és válassza a **Tovább: felülvizsgálat + létrehozás** lehetőséget a felülvizsgálati szakasz folytatásához.

#### <a name="review-deployment"></a>Központi telepítés áttekintése

A felülvizsgálati szakasz megjeleníti a JSON üzembe helyezési jegyzéket, amelyet az előző két szakaszban megadott beállítások alapján hoztak létre. Két modul is jelent meg, amelyeket nem adott hozzá: **$edgeAgent** és **$edgeHub**. Ez a két modul hozza létre a [IoT Edge futtatókörnyezetet](iot-edge-runtime.md) , és minden központi telepítés esetében kötelező alapértelmezett érték.

Tekintse át az üzembe helyezési adatokat, majd kattintson a **Létrehozás** gombra.

### <a name="verify-your-deployment"></a>Az üzemelő példány ellenőrzése

A központi telepítés létrehozása után térjen vissza az IoT hub **IoT Edge** lapjára.

1. Válassza ki azt a IoT Edge eszközt, amelyet a központi telepítéshez céloz, hogy megnyissa a részleteit.
1. Az eszköz részletei között ellenőrizze, hogy a blob Storage modul a **telepítésben** és az **eszköz által jelentett** módon van-e felsorolva.

Néhány percet is igénybe vehet, amíg a modul elindult az eszközön, majd visszaküldhető a IoT Hubra. Frissítse az oldalt, és tekintse meg a frissített állapotot.

## <a name="deploy-from-visual-studio-code"></a>Üzembe helyezés a Visual Studio Code-ból

A Azure IoT Edge a Visual Studio Code-ban biztosít sablonokat, amelyek segítenek az Edge-megoldások fejlesztésében. A következő lépésekkel hozzon létre egy új IoT Edge-megoldást egy blob Storage-modullal, és konfigurálja az üzembe helyezési jegyzéket.

1. Válassza a  >  **parancs-paletta** megtekintése lehetőséget.

1. A parancskatalógusban írja be és futtassa az **Azure IoT Edge: New IoT Edge solution** parancsot.

   ![Új IoT Edge megoldás futtatása](./media/how-to-develop-csharp-module/new-solution.png)

   Kövesse a parancskatalógusban található utasításokat a megoldás létrehozásához.

   | Mező | Érték |
   | ----- | ----- |
   | Mappa kiválasztása | Válassza ki a helyet a fejlesztői gépen a Visual Studio Code-hoz a megoldás fájljainak létrehozásához. |
   | Provide a solution name (Megoldásnév megadása) | Adjon meg egy leíró nevet a megoldáshoz, vagy fogadja el az alapértelmezett **EdgeSolution**. |
   | Select module template (Modulsablon kiválasztása) | Válasszon egy **meglévő modult (adja meg a teljes képet URL-címet)**. |
   | Provide a module name (Modulnév megadása) | Adjon meg egy teljes kisbetűs nevet a modulhoz, például **azureblobstorageoniotedge**.<br/><br/>Fontos, hogy a IoT Edge modul Azure Blob Storage kisbetűs nevét használja. A IoT Edge a kis-és nagybetűk megkülönböztetésére szolgál, ha a modulokra hivatkozik, és a Storage SDK alapértelmezett értéke kisbetűs. |
   | Docker-rendszerkép megadása a modulhoz | Adja meg a rendszerkép URI-JÁT: **MCR.microsoft.com/Azure-Blob-Storage:Latest** |

   A Visual Studio Code felveszi a megadott adatokat, létrehoz egy IoT Edge megoldást, majd betölti azt egy új ablakban. A megoldás sablonja létrehoz egy üzembe helyezési jegyzékfájlt, amely tartalmazza a blob Storage-modul rendszerképét, de konfigurálnia kell a modul létrehozási beállításait.

1. Nyissa meg *deployment.template.js* az új megoldás-munkaterületen, és keresse meg a **modulok** szakaszt. Végezze el a következő konfigurációs módosításokat:

   1. Törölje a **SimulatedTemperatureSensor** modult, mivel ez nem szükséges ehhez a központi telepítéshez.

   1. Másolja és illessze be a következő kódot a `createOptions` mezőbe:

      ```json
      "Env":[
       "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
       "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>"
      ],
      "HostConfig":{
        "Binds": ["<storage mount>"],
        "PortBindings":{
          "11002/tcp": [{"HostPort":"11002"}]
        }
      }
      ```

      ![CreateOptions modul frissítése – Visual Studio Code](./media/how-to-deploy-blob/create-options.png)

1. Cserélje le a `<your storage account name>` nevet, és jegyezze fel. A fiókok nevének 3 – 24 karakter hosszúnak kell lennie, kisbetűkkel és számokkal. Nincsenek szóközök.

1. Cserélje le `<your storage account key>` egy 64 bájtos Base64-kulccsal. Létrehozhat egy kulcsot olyan eszközökkel, mint a [GeneratePlus](https://generate.plus/en/base64). Ezeket a hitelesítő adatokat fogja használni a blob Storage más modulokból való eléréséhez.

1. Cserélje le `<storage mount>` at a tároló operációs rendszerének megfelelően. Adja meg egy [kötet](https://docs.docker.com/storage/volumes/) nevét vagy a IoT Edge eszköz egyik könyvtárának abszolút elérési útját, amelyen a blob-modul adatait tárolni szeretné. A Storage-csatlakoztatás leképezi az eszközön az Ön által megadott helyet a modul egy készletének megfelelő helyére.  

     - Linux-tárolók esetén a formátum a következő **\<your storage path or volume> :/blobroot**. Például:
         - a [Volume Mount](https://docs.docker.com/storage/volumes/)használata: `my-volume:/blobroot`
         - [kötési csatlakoztatás](https://docs.docker.com/storage/bind-mounts/)használata: `/srv/containerdata:/blobroot` . Ügyeljen arra, hogy a címtár- [hozzáférés biztosítása a tároló felhasználójának](how-to-store-data-blob.md#granting-directory-access-to-container-user-on-linux) lépéseit kövesse.
     - Windows-tárolók esetén a formátum a következő **\<your storage path or volume> : C:/BlobRoot**. Például:
         - A [Volume Mount](https://docs.docker.com/storage/volumes/)használata: `my-volume:C:/BlobRoot` .
         - [Kötési csatlakoztatás](https://docs.docker.com/storage/bind-mounts/)használata: `C:/ContainerData:C:/BlobRoot` .
         - A helyi meghajtó használata helyett leképezheti az SMB hálózati helyét. További információ: az [SMB-megosztás használata helyi tárolóként](how-to-store-data-blob.md#using-smb-share-as-your-local-storage).

     > [!IMPORTANT]
     > Ne módosítsa a tárolási csatlakoztatási érték második felét, amely a IoT Edge modul Blob Storage egy adott helyére mutat. A tárolási csatlakoztatásnak mindig a következővel kell végződnie **:/blobroot** for Linux containers and **: C:/blobroot** for Windows containers.

1. Konfigurálja a [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) és a [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties) a modulhoz úgy, hogy hozzáadja a következő JSON-t a fájl *deployment.template.js* . Konfigurálja az egyes tulajdonságokat megfelelő értékkel, és mentse a fájlt. Ha a IoT Edge szimulátort használja, állítsa be az értékeket a kapcsolódó környezeti változókra ezekhez a tulajdonságokhoz, amelyek a [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) és a [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties) magyarázata című szakaszban találhatók.

   ```json
   "<your azureblobstorageoniotedge module name>":{
     "properties.desired": {
       "deviceAutoDeleteProperties": {
         "deleteOn": <true, false>,
         "deleteAfterMinutes": <timeToLiveInMinutes>,
         "retainWhileUploading": <true, false>
       },
       "deviceToCloudUploadProperties": {
         "uploadOn": <true, false>,
         "uploadOrder": "<NewestFirst, OldestFirst>",
         "cloudStorageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>",
         "storageContainersForUpload": {
           "<source container name1>": {
             "target": "<target container name1>"
           }
         },
         "deleteAfterUpload": <true, false>
       }
     }
   }
   ```

   ![a azureblobstorageoniotedge kívánt tulajdonságainak beállítása – Visual Studio Code](./media/how-to-deploy-blob/devicetocloud-deviceautodelete.png)

   További információ a deviceToCloudUploadProperties és a deviceAutoDeleteProperties konfigurálásáról a modul üzembe helyezése után: [a különálló modul szerkesztése](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin). A tároló-létrehozási lehetőségekről, a szabályzatok újraindításáról és a kívánt állapotról további információt a [EdgeAgent kívánt tulajdonságok](module-edgeagent-edgehub.md#edgeagent-desired-properties)című témakörben talál.

1. Mentse a *deployment.template.json* fájlt.

1. Kattintson a jobb gombbal **adeployment.template.js** elemre, majd válassza a **IoT Edge üzembe helyezési jegyzék előállítása** lehetőséget.

1. A Visual Studio Code megtekinti a *deployment.template.jsban* megadott adatokat, és felhasználja egy új központi telepítési jegyzékfájl létrehozásához. Az üzembe helyezési jegyzék a megoldás munkaterületének új **konfigurációs** mappájában jön létre. Ha ezt a fájlt elvégezte, kövesse a Azure IoT Edge- [modulok üzembe helyezése a Visual Studio Code](how-to-deploy-modules-vscode.md) -ban vagy a [Azure IoT Edge modulok üzembe helyezése az Azure CLI 2,0-vel](how-to-deploy-modules-cli.md)című témakör lépéseit.

## <a name="deploy-multiple-module-instances"></a>Több Modulos példány üzembe helyezése

Ha az Azure Blob Storage több példányát is telepíteni szeretné IoT Edge modulon, meg kell adnia egy másik tárolási útvonalat, és módosítania `HostPort` kell a modulhoz kötődő értéket. A blob Storage-modulok a tárolóban mindig teszik elérhetővé a 11002-es portot, de deklarálhatja, hogy melyik portot kell a gazdagéphez kötni.

Módosítsa a **tároló létrehozási beállításait** (a Azure Portal) vagy a **createOptions** mezőt (a Visual Studio Code-ban található fájl *deployment.template.js* ) a következő érték megváltoztatásához `HostPort` :

```json
"PortBindings":{
  "11002/tcp": [{"HostPort":"<port number>"}]
}
```

Ha további blob Storage-modulokhoz csatlakozik, módosítsa a végpontot úgy, hogy az a frissített gazda portra mutasson.

## <a name="configure-proxy-support"></a>Proxytámogatás konfigurálása

Ha a szervezet proxykiszolgálót használ, konfigurálnia kell a proxy támogatását a edgeAgent és a edgeHub futásidejű moduljaihoz. Ez a folyamat két feladatot foglal magában:

- Konfigurálja a futásidejű démonokat és a IoT Edge ügynököt az eszközön.
- Állítsa be az HTTPS_PROXY környezeti változót a moduloknál az üzembe helyezési jegyzékfájl JSON-fájljában.

Ez a folyamat a [IoT Edge eszköz konfigurálása egy proxykiszolgálón keresztül történő kommunikációra](how-to-configure-proxy-support.md)című témakörben található.

Emellett a blob Storage-modulhoz a HTTPS_PROXY beállítás is szükséges a jegyzékfájl telepítési fájljában. Közvetlenül szerkesztheti a telepítési jegyzékfájlt, vagy használhatja a Azure Portal.

1. Navigáljon az IOT hubhoz a Azure Portal, és válassza a **IoT Edge** lehetőséget a bal oldali ablaktábla menüjében.

1. Válassza ki azt az eszközt, amelynél konfigurálni szeretné a modult.

1. Válassza a **modulok beállítása** lehetőséget.

1. A lap **IoT Edge modulok** szakaszában válassza ki a blob Storage-modult.

1. A **IoT Edge-modul frissítése** lapon válassza a **környezeti változók** lapot.

1. Adja hozzá a `HTTPS_PROXY` **nevet** és a proxy URL-címét az **értékhez**.

      ![Képernyőfelvétel: az I o T Edge-modul frissítése panel, ahol megadhatja a megadott értékeket.](./media/how-to-deploy-blob/https-proxy-config.png)

1. Kattintson a **frissítés** gombra, majd **tekintse át a + létrehozás** menüpontot.

1. Vegye figyelembe, hogy a rendszer hozzáadja a proxyt a modulhoz az üzembe helyezési jegyzékben, és kiválasztja a **Létrehozás** lehetőséget.

1. A beállítás ellenőrzéséhez válassza ki a modult az eszköz adatai lapon, és a **IoT Edge modulok részletek** oldalának alsó részén válassza a **környezeti változók** lapot.

      ![Képernyőfelvétel: környezeti változók lap.](./media/how-to-deploy-blob/verify-proxy-config.png)

## <a name="next-steps"></a>Következő lépések

További információ az [Azure Blob Storage on IoT Edge](how-to-store-data-blob.md).

Az üzembe helyezési jegyzékek működésével és létrehozásával kapcsolatos további információkért lásd: [IoT Edge modulok használatának, konfigurálásának és](module-composition.md)újbóli használatának ismertetése.
