---
title: Élő videó-elemzés üzembe helyezése egy IoT Edge eszközön – Azure
description: Ez a cikk azokat a lépéseket sorolja fel, amelyek segítséget nyújtanak az élő videó-elemzések IoT Edge eszközön való üzembe helyezésében. Ezt például akkor teheti meg, ha rendelkezik hozzáféréssel egy helyi linuxos számítógéphez, és/vagy korábban létrehozott egy Azure Media Services fiókot.
ms.topic: how-to
ms.date: 09/09/2020
ms.openlocfilehash: ff5dbc8e643137008aa7819b455adcf97c05bfc9
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99491790"
---
# <a name="deploy-live-video-analytics-on-an-iot-edge-device"></a>Élő videó-elemzés üzembe helyezése IoT Edge eszközön

Ez a cikk azokat a lépéseket sorolja fel, amelyek segítséget nyújtanak az élő videó-elemzések IoT Edge eszközön való üzembe helyezésében. Ezt például akkor teheti meg, ha rendelkezik hozzáféréssel egy helyi linuxos számítógéphez, és/vagy korábban létrehozott egy Azure Media Services fiókot.

> [!NOTE]
> A ARM64-eszközök támogatása a IoT Edge-buildeken és újabb verziókban elérhető élő videó-elemzésekben érhető el `1.0.4` .
> A Azure IoT Edge Runtime ARM64-eszközökön való futtatásának támogatása [nyilvános előzetes](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)verzióban érhető el.

## <a name="prerequisites"></a>Előfeltételek

* X86-64 vagy ARM64-eszköz, amely a [támogatott Linux operációs rendszerek](../../iot-edge/support.md#operating-systems) egyikét futtatja
* Azure-előfizetés, amelyhez [tulajdonosi jogosultsággal](../../role-based-access-control/built-in-roles.md#owner) rendelkezik
* [IoT Hub létrehozása és beállítása](../../iot-hub/iot-hub-create-through-portal.md)
* [IoT Edge eszköz regisztrálása](../../iot-edge/how-to-manual-provision-symmetric-key.md)
* [Az Azure IoT Edge-futtatókörnyezet telepítése Debian-alapú Linux rendszereken](../../iot-edge/how-to-install-iot-edge.md)
* [Azure Media Services-fiók létrehozása](../latest/create-account-howto.md)

    * Használja a következő régiók egyikét: USA 2. keleti régiója, USA középső régiója, USA északi középső régiója, Kelet-Japán, USA nyugati régiója, USA 2. nyugati régiója, az USA nyugati középső régiója, Kelet-Kanada, Egyesült Királyság déli régiója, Közép-Franciaország, Dél-Németország, Észak-Svájc, Nyugat-Svájc és Nyugat-Japán.
    * Az általános célú v2-(GPv2-) Storage-fiókok használata javasolt

## <a name="configuring-azure-resources-for-using-live-video-analytics"></a>Azure-erőforrások konfigurálása a Live Video Analytics használatához

### <a name="create-custom-azure-resource-manager-role"></a>Egyéni Azure Resource Manager-szerepkör létrehozása

Lásd: [egyéni Azure Resource Manager szerepkör létrehozása](create-custom-azure-resource-manager-role-how-to.md) és hozzárendelés egy egyszerű szolgáltatáshoz az élő videó Analytics használatához.

### <a name="set-up-a-premium-streaming-endpoint"></a>Prémium szintű streaming-végpont beállítása

Ha élő videó-elemzést szeretne használni a videók felhőbe való folyamatos rögzítéséhez, és ezt követően használja a [lekérdezési API-kat](playback-recordings-how-to.md#query-api) a lejátszás előtt, javasoljuk, hogy frissítse a Media Service-t egy [prémium szintű streaming-végpont](../latest/streaming-endpoint-concept.md#types)használatára.  

Ez egy választható lépés. Ezt az Azure CLI-parancsot használhatja:

```azurecli
az ams streaming-endpoint scale --resource-group $RESOURCE_GROUP --account-name $AMS_ACCOUNT -n default --scale-units 1
```

Ezt a parancsot használhatja a folyamatos átviteli végpont elindításához. 

> [!IMPORTANT]
> Az előfizetés ekkor kezdi meg a számlázást.

```azurecli
az ams streaming-endpoint start --resource-group $RESOURCE_GROUP --account-name $AMS_ACCOUNT -n default --no-wait
```

A Media Service API-k eléréséhez szükséges hitelesítő adatok beszerzéséhez kövesse az ebben a cikkben ismertetett lépéseket: [a Media Service API-k eléréséhez](../latest/access-api-howto.md?tabs=portal) válassza a portál lapot.

## <a name="create-and-use-local-user-account-for-deployment"></a>Helyi felhasználói fiók létrehozása és használata központi telepítéshez
Ha az élő videó-elemzést IoT Edge modulon szeretné futtatni, hozzon létre egy helyi felhasználói fiókot, amely a lehető legkevesebb jogosultsággal rendelkezik. Futtassa például a következő parancsokat a Linux rendszerű gépen:

```
sudo groupadd -g 1010 localuser
sudo adduser --home /home/edgeuser --uid 1010 -gid 1010 edgeuser
```

## <a name="granting-permissions-to-device-storage"></a>Engedélyek megadása az eszköz tárterületéhez

Most, hogy létrehozott egy helyi felhasználói fiókot, 

* Az alkalmazás konfigurációs adatfájljainak tárolásához helyi mappára lesz szüksége. Hozzon létre egy mappát, és adjon engedélyeket a LocalUser-fióknak az alábbi parancsokkal a mappába való íráshoz:

```
sudo mkdir /var/lib/azuremediaservices
sudo chown -R edgeuser /var/lib/azuremediaservices
```

* Emellett egy olyan mappára is szüksége lesz, amely [egy helyi fájlba rögzíti a videókat](event-based-video-recording-concept.md#video-recording-based-on-events-from-other-sources). Hozzon létre egy helyi mappát a következő parancsokkal:

```
sudo mkdir /var/media
sudo chown -R edgeuser /var/media
```

## <a name="deploy-live-video-analytics-edge-module"></a>Élő videó Analytics Edge-modul üzembe helyezése

A IoT Edge élő videó-elemzési szolgáltatás a modul [Twin konfigurációs sémájában](module-twin-configuration-schema.md)dokumentált modulok kettős tulajdonságait teszi elérhetővé. 

### <a name="deploy-using-the-azure-portal"></a>Üzembe helyezés az Azure Portalon

A Azure Portal végigvezeti az üzembe helyezési jegyzék létrehozásán és az üzembe helyezés egy IoT Edge eszközön való továbbításának végrehajtásán.  
#### <a name="select-your-device-and-set-modules"></a>Válassza ki az eszközt, és állítsa be a modulokat

1. Jelentkezzen be a [Azure Portalba](https://ms.portal.azure.com/) , és navigáljon az IoT hubhoz.
1. A menüből válassza a **IoT Edge** lehetőséget.
1. Kattintson a céleszköz AZONOSÍTÓJÁRA az eszközök listájából.
1. Válassza a **modulok beállítása** lehetőséget.

#### <a name="configure-a-deployment-manifest"></a>Központi telepítési jegyzék konfigurálása

Az üzembe helyezési jegyzék egy JSON-dokumentum, amely leírja, hogy mely modulokat kell telepíteni, hogyan zajlik az adatforgalom a modulok és a modul kívánt tulajdonságai között. A Azure Portal egy varázsló végigvezeti az üzembe helyezési jegyzék létrehozásán. Három lépést tartalmaz a következő lapokon: **modulok**, **útvonalak**, **felülvizsgálat + létrehozás**.

#### <a name="add-modules"></a>Modulok hozzáadása

1. A lap **IoT Edge modulok** szakaszában kattintson a legördülő **listára** , és válassza a **IoT Edge modul** lehetőséget az **IoT Edge modul hozzáadása** lap megjelenítéséhez.
1. A **modul beállításai** lapon adja meg a modul nevét, majd adja meg a tároló rendszerképének URI-ját:   
    Angol nyelvű Példák:
    
    * **IoT Edge modul neve**: lvaEdge
    * **Rendszerkép URI-ja**: MCR.microsoft.com/Media/Live-Video-Analytics:2.0    
    
    ![Képernyőfelvétel: a modul beállításai lap.](./media/deploy-iot-edge-device/add.png)
    
    > [!TIP]
    > Ne válassza a **Hozzáadás** lehetőséget, amíg meg nem adta a **modul beállításai**, a tároló- **létrehozási beállítások** és a **modul Twin-beállítások** lapjait a jelen eljárásban leírtak szerint.
    
    > [!WARNING]
    > A Azure IoT Edge a kis-és nagybetűk megkülönböztetése, amikor hívásokat kezdeményez a modulokra. Jegyezze fel a modul neveként használt pontos karakterláncot.

1. Nyissa meg a **környezeti változók** lapot.
   
   Adja hozzá a következő értékeket a beviteli mezőkben a környezeti változók megjelenítéséhez. ![](./media/deploy-iot-edge-device/environment-variables.png) 

1. Nyissa meg a **tároló létrehozása beállítások** lapot.

    ![Tároló-létrehozási beállítások](./media/deploy-iot-edge-device/container-create-options.png)
 
    Másolja és illessze be a következő JSON-t a mezőbe, hogy korlátozza a modul által létrehozott naplófájlok méretét.
    
    ```    
    {
        "HostConfig": {
            "LogConfig": {
                "Type": "",
                "Config": {
                    "max-size": "10m",
                    "max-file": "10"
                }
            },
            "Binds": [
               "/var/lib/azuremediaservices:/var/lib/azuremediaservices",
               "/var/media:/var/media"
            ]
        }
    }
    ````
   
   A JSON "kötések" szakasza 2 bejegyzést tartalmaz:
   1. "/var/lib/azuremediaservices:/var/lib/azuremediaservices": ezzel a beállítással lehet az állandó alkalmazás-konfigurációs adatait a tárolóból kötni, és a peremhálózati eszközön tárolni.
   1. "/var/Media:/var/media": ez köti össze az adathordozó-mappákat a peremhálózati eszköz és a tároló között. Ez a videó felvételek tárolására szolgál, ha olyan Media Graph-topológiát futtat, amely támogatja a videoklipek peremhálózati eszközön való tárolását.
   
1. A **modul Twin beállítások** lapján másolja be a következő JSON-t, és illessze be a mezőbe.
 
    ![Dupla beállítások](./media/deploy-iot-edge-device/twin-settings.png)

    A IoT Edge élő videó-elemzési funkciójának a futtatásához kötelező Twin tulajdonságokat kell megadnia, ahogy az a [modul Twin konfigurációs sémában](module-twin-configuration-schema.md)szerepel.  

    A modul Twin-beállítások szerkesztése mezőbe beírt JSON a következőképpen fog kinézni:    
    ```
    {
        "applicationDataDirectory": "/var/lib/azuremediaservices",
        "azureMediaServicesArmId": "/subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/microsoft.media/mediaservices/{AMS-account-name}",
        "aadTenantId": "{the-ID-of-your-tenant}",
        "aadServicePrincipalAppId": "{the-ID-of-the-service-principal-app-for-ams-account}",
        "aadServicePrincipalSecret": "{secret}"
    }
    ```
    Ezek a **szükséges** tulajdonságok és a fenti JSON-hoz  
    * {subscriptionID} – ez az Azure-előfizetés azonosítója
    * {resourceGroupName} – ez az erőforráscsoport, amelyhez a Media Service-fiók tartozik
    * {AMS-Account-Name} – ez a Media Services fiók neve
    
    A többi érték beszerzéséhez tekintse meg a [hozzáférés Azure Media Services API](../latest/access-api-howto.md?tabs=portal) -t, és válassza a portál lapot.  
    * aadTenantId – ez a bérlő azonosítója, és ugyanaz, mint a fenti hivatkozás "AadTenantId".
    * aadServicePrincipalAppId – ez a Media Service-fiókhoz tartozó egyszerű szolgáltatásnév, amely megegyezik a fenti hivatkozásban található "AadClientId" AZONOSÍTÓval.
    * aadServicePrincipalSecret – ez az egyszerű szolgáltatásnév jelszava, és ugyanaz, mint a fenti hivatkozás "AadSecret".

    Az alábbiakban néhány további **javasolt** tulajdonságot is HOZZÁADHAT a JSON-hoz, és segít a modul figyelésében. További információkért lásd: [figyelés és naplózás](monitoring-logging.md):
    
    ```
    "diagnosticsEventsOutputName": "lvaEdgeDiagnostics",
    "OperationalEventsOutputName": "lvaEdgeOperational",
    "logLevel": "Information",
    "logCategories": "Application,Events"
    ```
    
    A következő néhány **opcionális** tulajdonságot HOZZÁADHAT a JSON-hoz:
    
    ```
    "aadEndpoint": "https://login.microsoftonline.com",
    "aadResourceId": "https://management.core.windows.net/",
    "armEndpoint": "https://management.azure.com/",
    "allowUnsecuredEndpoints": true
    ```

   > [!Note]
   > Az **allowUnsecuredEndpoints** Twin tulajdonsága igaz értékre van állítva az oktatóanyagok és a rövid útmutatók esetében.   
   Éles környezetben való futtatáskor ezt a tulajdonságot **false** értékre kell állítani. Ez biztosítja, hogy az alkalmazás letiltja az összes nem biztonságos végpontot, és a Graph-topológiák futtatásához érvényes hitelesítő adatokra lesz szükség.  
   
    Válassza a Hozzáadás lehetőséget a modul Twin tulajdonságainak hozzáadásához.
1. Válassza a **Tovább: útvonalak** szakaszt az útvonalak szakaszhoz.
    Útvonalak meghatározása.

Tartsa meg az alapértelmezett útvonalakat, és válassza a **Tovább: felülvizsgálat + létrehozás** lehetőséget a felülvizsgálati szakasz folytatásához.

#### <a name="review-deployment"></a>Központi telepítés áttekintése

A felülvizsgálati szakasz megjeleníti a JSON üzembe helyezési jegyzéket, amelyet az előző két szakaszban megadott beállítások alapján hoztak létre. Két modul is jelent meg, amelyeket nem adott hozzá: $edgeAgent és $edgeHub. Ez a két modul hozza létre a IoT Edge futtatókörnyezetet, és minden központi telepítés esetében kötelező alapértelmezett érték.

Tekintse át az üzembe helyezési adatokat, majd kattintson a Létrehozás gombra.

### <a name="verify-your-deployment"></a>Az üzemelő példány ellenőrzése

A központi telepítés létrehozása után térjen vissza az IoT hub IoT Edge lapjára.

1. Válassza ki azt a IoT Edge eszközt, amelyet a központi telepítéshez céloz, hogy megnyissa a részleteit.
2. Az eszköz részletei között ellenőrizze, hogy a blob Storage modul a **telepítésben és az eszköz által jelentett** módon van-e felsorolva.

Néhány percet is igénybe vehet, amíg a modul elindult az eszközön, majd visszaküldhető a IoT Hubra. Frissítse az oldalt, és tekintse meg a frissített állapotot.
Állapotkód: 200 – az OK érték azt jelenti, hogy [az IoT Edge futtatókörnyezet](../../iot-edge/iot-edge-runtime.md) kifogástalan, és működik.

![A képernyőfelvétel egy IoT Edge futtatókörnyezet állapotának értékét jeleníti meg.](./media/deploy-iot-edge-device/status.png)

#### <a name="invoke-a-direct-method"></a>Közvetlen metódus meghívása

A következő lépésként lehetővé teszi a minta tesztelését egy közvetlen metódus meghívásával. A lvaEdge modul által biztosított közvetlen módszerek megismeréséhez olvassa el az [IoT Edge Live Video Analytics közvetlen metódusait](direct-methods.md) .

1. A létrehozott Edge-modulra kattintva megnyílik a konfigurációs lapja.  

    ![Képernyőfelvétel: egy Edge-modul konfigurációs lapja.](./media/deploy-iot-edge-device/modules.png)
1. Kattintson a közvetlen metódus menüpontra.

    > [!NOTE] 
    > Az aktuális oldalon látható módon hozzá kell adnia egy értéket a kapcsolódási karakterláncok részeiben. A **beállítás neve** szakaszban nem kell elrejtenie vagy módosítania a semmit. Az OK, hogy nyilvánosak legyenek.

    ![Közvetlen metódus](./media/deploy-iot-edge-device/module-details.png)
1. Ezután írja be a "GraphTopologyList" kifejezést a metódus neve mezőbe.
1. Ezután másolja és illessze be az alábbi JSON-adattartalmat a hasznos adatok mezőbe.
    
    ```
    {
        "@apiVersion" : "2.0"
    }
    ```
1. Kattintson a "metódus meghívása" lehetőségre az oldal tetején.

    ![Közvetlen metódusok](./media/deploy-iot-edge-device/direct-method.png)
1. A Result (eredmény) mezőben a 200-es állapotú üzenetnek kell megjelennie.

    ![Az 200-es állapotú üzenet](./media/deploy-iot-edge-device/connection-timeout.png) 

## <a name="next-steps"></a>Következő lépések

Próbálja ki a rövid útmutató [: első lépések – élő videó Analytics IoT Edge](get-started-detect-motion-emit-events-quickstart.md#deploy-modules-on-your-edge-device)

> [!TIP]
> Ha a fenti rövid útmutatóval folytatja a közvetlen metódusok Visual Studio Code használatával történő meghívását, akkor az alapértelmezett beállítás helyett a IoT Hubhoz hozzáadott eszközt fogja használni `lva-sample-device` .
