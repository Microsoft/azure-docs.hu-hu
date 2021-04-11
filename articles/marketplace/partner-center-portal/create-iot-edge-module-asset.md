---
title: A IoT Edge modul technikai eszközeinek előkészítése – Azure Marketplace
description: Ismerje meg az eszközök internetes hálózata (IoT) Edge-modul technikai eszközeinek technikai és konfigurációs követelményeit, mielőtt közzéteszi őket az Azure Marketplace-en.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 04/03/2020
ms.openlocfilehash: f8eddd5d346003af0c2b41d65fc0aa09c86f48c7
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2021
ms.locfileid: "106167447"
---
# <a name="prepare-your-iot-edge-module-technical-assets"></a>A IoT Edge modul technikai eszközeinek előkészítése

Ez a cikk azokat a követelményeket ismerteti, amelyekkel az Azure Marketplace-en való közzététel előtt meg kell felelniük az eszközök internetes hálózata (IoT) Edge-modul technikai eszközeinek.

## <a name="get-started"></a>Bevezetés

Az IoT Edge modul egy IoT Edge eszközön futó Docker-kompatibilis tároló.

- IoT Edge modulokról további információt a [Azure IoT Edge modulok ismertetése](../../iot-edge/iot-edge-modules.md)című témakörben talál.
- A IoT Edge modul fejlesztésének megkezdéséhez tekintse [meg a saját IoT Edge modulok fejlesztése](../../iot-edge/module-development.md)című témakört.

## <a name="technical-requirements"></a>Technikai követelmények

A IoT Edge modulnak meg kell felelnie az alábbi műszaki követelményeknek, hogy az Azure Marketplace-en kell hitelesíteni és közzétenni.

### <a name="platform-support"></a>Platformtámogatás

A IoT Edge-modulnak támogatnia kell a következő platform-lehetőségek egyikét:

#### <a name="tier-1-platforms-supported-by-iot-edge"></a>A IoT Edge által támogatott 1. szintű platformok

A modulnak támogatnia kell az IoT Edge által támogatott összes 1. szintű platformot ( [Azure IoT Edge-támogatásban](../../iot-edge/support.md)rögzített módon). Ezt a lehetőséget javasoljuk, mert jobb felhasználói élményt nyújt. A feltételnek megfelelő modulok bemutatásra kerülnek. A platformot használó modulnak a következőket kell tennie:

- Adjon meg egy legújabb címkét és egy Version címkét (például: 1.0.1), amelyek a [GitHub manifest-Tool eszközzel](https://github.com/estesp/manifest-tool)létrehozott manifest-címkék.

- A [partner központ](https://partner.microsoft.com/dashboard/commercial-marketplace) ajánlatok listája lapján a **hasznos hivatkozások** szakaszban található hivatkozásokat adhat hozzá a [Azure IoT Edge minősített eszköz katalógusához](https://devicecatalog.azure.com/devices?certificationBadgeTypes=IoTEdgeCompatible).

#### <a name="a-subset-of-tier-1-platforms-supported-by-iot-edge"></a>Az 1. szintű platform egy részhalmaza, amelyet a IoT Edge támogat

A modulnak támogatnia kell a IoT Edge által támogatott 1. rétegbeli platformokat ( [Azure IoT Edge-támogatásban](../../iot-edge/support.md)rögzített módon). A platformot használó modulnak a következőket kell tennie:

- Adjon meg egy legújabb címkét és egy Version (például 1.0.1) címkét, amely a GitHub [manifest-Tool eszközzel](https://github.com/estesp/manifest-tool) létrehozott manifest-címkék, ha több platform is támogatott. A jegyzékfájl címkéi csak akkor választhatók, ha egy platform támogatott.
- A [partner központ](https://partner.microsoft.com/dashboard/commercial-marketplace) ajánlatok listája lapján adhat hozzá egy hivatkozást a **hasznos hivatkozások** szakasz alatt legalább egy IoT Edge eszközre a [Azure IoT Edge minősített eszköz katalógusában](https://devicecatalog.azure.com/).

:::image type="content" source="media/iot-edge-module-technical-assets-offer-listing.png" alt-text="Ez a partner Center ajánlati lista szakaszának képe":::

### <a name="device-dimensions"></a>Eszköz méretei

IoT Edge a modul méretei (például a processzor, a RAM, a tárolás és a GPU) a célként megadott IoT Edge-eszközökön a következő követelményeknek kell megfelelniük:

- A modulnak működnie kell legalább egy IoT Edge eszközzel a [Azure IoT Edge minősített eszköz katalógusában](https://devicecatalog.azure.com/).

- A minimális hardverkövetelmények dokumentációját az ajánlat leírásában szereplő utolsó bekezdésnek kell megadnia (a [partner Center](https://partner.microsoft.com/dashboard/commercial-marketplace)ajánlati lista lapján). Igény szerint a javasolt hardverkövetelmények is kiválaszthatók, ha azok jelentősen eltérnek. Adja meg például a következő szakaszt az ajánlat leírásának végén:

Másolja ezt a HTML-szöveget, vagy használja a szerkesztési ablakban a megfelelő Rich Text függvényeket.

```html
<p><u>Minimum hardware requirements:</u> Linux x64 and arm32 OS, 1GB of RAM, 500 Mb of storage</p>
```

### <a name="configuration"></a>Konfiguráció

A modulnak tartalmaznia kell az alapértelmezett konfigurációs beállításokat, hogy az üzembe helyezés egy IoT Edge eszközön legyen a lehető legegyértelműbb módon. Ezek az információk a csomag **technikai konfiguráció** lapján, a [partner Centerben](https://partner.microsoft.com/dashboard/commercial-marketplace)találhatók. A tároló a IoT Edge modul SDK-val is rendelkezhet, amely lehetővé teszi a kommunikációt a peremhálózat és a IoT Hub között.

#### <a name="default-configuration"></a>Alapértelmezett konfiguráció

IoT Edge moduloknak képesnek kell lenniük a csomag **technikai konfiguráció** lapján megadott alapértelmezett beállításokkal való indulásra [.](https://partner.microsoft.com/dashboard/commercial-marketplace) A következő alapértelmezett beállítások érhetők el:

- Alapértelmezett **útvonalak**
- Alapértelmezett **modul – Twin kívánt tulajdonságok**
- Alapértelmezett **környezeti változók**
- Alapértelmezett **tároló létrehozási beállításai**

Olyan esetben, amikor egy alapértelmezett értékhez szükséges paraméter nem értelmezhető (például az ügyfél kiszolgálójának IP-címe), adjon hozzá egy paramétert alapértelmezett értékként. Ez az érték nagybetűs, szögletes zárójelek közé. Ebben a példában a következő alapértelmezett környezeti változót kell beállítania:

```
ServerIPAddress = <MY_SERVER_IP_ADDRESS>
```

#### <a name="configuration-documentation"></a>Konfigurációs dokumentáció

Egy IoT Edge modul összes konfigurációs beállítását egyértelműen dokumentálni kell. Tegyük fel például, hogyan kell használni az útvonalakat, a dupla kívánt tulajdonságokat, a környezeti változókat, a createOptions stb. Meg kell adnia egy hivatkozást a dokumentációhoz, vagy az ajánlat vagy a csomag leírását kell használnia. Ezeket az információkat a [partner Center](https://partner.microsoft.com/dashboard/commercial-marketplace) **ajánlati lista** és **terv Listázás** lapján adhatja meg.

#### <a name="tags-and-versioning"></a>Címkék és verziószámozás

Az ügyfeleknek egyszerűen üzembe kell helyezniük egy modult, és automatikusan le kell kérniük a frissítéseket a piactérről (fejlesztői forgatókönyv esetén). Emellett képesnek kell lenniük arra, hogy a tesztelt pontos verziót is használják (éles környezetben).

Az ügyfelek elvárásainak kielégítéséhez és a piactéren való közzétételéhez IoT Edge moduloknak meg kell felelniük az alábbi követelményeknek

- Vegyen fel egy manifest legújabb címkét, amely a legújabb verzióra mutat az összes támogatott platformon.
- A verziók címkéi az X. Y. Z formátumban legyenek, ahol az X, az Y és a Z egész számok.
- Adjon meg egy "version" címkét, például a 1.0.1-et, amely az összes támogatott platformon egy adott verzióra mutat.
- Ne frissítse a "version" címkéit, például a 1.0.1-et, mert nem kell módosítani.

> [!NOTE]
> Igény szerint a verziószámozás tartalmazhat "Rolling version" címkéket, például 2,0 és 1,0. Ez támogatja több főverzió párhuzamos fenntartását.

### <a name="telemetry"></a>Telemetria

A IoT modul SDK-t használó moduloknak az egyedi modul azonosítóját kell beállítania a PublisherId. OfferId. SkuId telemetria célokra. Egy egyedi azonosító segíti az Azure Marketplace-t a futtatott modulok számának azonosításában.

A IoT modul SDK-k használatával az alábbi módszerek egyikével állíthatja be a productinfo olvasása erre az azonosítóra:

- [C#](/dotnet/api/microsoft.azure.devices.client.deviceclient.productinfo#Microsoft_Azure_Devices_Client_DeviceClient_ProductInfo)
- [C](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Python](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/Iothub_sdk_options.md)
- [Java](/java/api/com.microsoft.azure.sdk.iot.device.productinfo)

A IoT modul SDK-t nem használó modulok esetében a kevésbé pontos adatellenőrzések a partner centeren keresztül érhetők el, például a letöltések száma.

### <a name="security"></a>Biztonság

IoT Edge moduloknak el kell kerülniük a [Kiemelt modulokat](https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities). Ehelyett a lehető legkevesebb jogosultsággal rendelkező hozzáférést kérje a gazdagéphez.

### <a name="module-iot-sdk"></a>Modul IoT SDK

A IoT modul SDK-val együtt nem előfeltétel a minősítés. A IoT modul SDK-val együtt azonban jobb felhasználói élményt biztosíthat. Például az Útválasztás és az üzenetek felhőbe való küldésének támogatásához.

A IoT modul SDK-nak szüksége van a rendszerű telemetria adatainak lekérésére.

## <a name="recertification-process"></a>Újraminősítési folyamat

A partnerek értesítést kapnak, ha a modulokat érintő megszakítási változás történik, például:

- A IoT Edge által támogatott 1. szintű operációs rendszer/Arch támogatás mátrixa
- IoT modul SDK
- IoT Edge-futtatókörnyezet
- IoT Edge modul minősítési irányelvei

A partnereknek frissíteniük és hitelesíteniük kell az ajánlatokat, ha újra közzéteszik őket a [partner Centerben](https://partner.microsoft.com/dashboard/commercial-marketplace).

Az ajánlat a frissítése után is újra lesz hitelesítve, például új képcímkék hozzáadásával.

## <a name="host-module-in-azure-container-registry"></a>Gazdagép modulja Azure Container Registry

IoT Edge moduljának Azure Marketplace-re való feltöltéséhez először egy [Azure Container Registry](https://azure.microsoft.com/services/container-registry/) (ACR-ben) kell üzemeltetni. A modulnak tartalmaznia kell az összes közzétenni kívánt címkét, beleértve a jegyzékfájl által hivatkozott képcímkéket is. További információkért tekintse meg az [Azure Container Registry létrehozása és a tároló rendszerképének leküldése](../../container-instances/container-instances-tutorial-prepare-acr.md)című oktatóanyagot.

## <a name="next-steps"></a>Következő lépések

- [IoT Edge-modulajánlat létrehozása](azure-iot-edge-module-creation.md)