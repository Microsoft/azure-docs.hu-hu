---
title: IoT Plug and Play-eszközök tanúsítása | Microsoft Docs
description: Eszköz-szerkesztőként megtudhatja, hogyan futtathat teszteket, és hogyan küldhet el egy eszközt a minősítéshez
author: konichi3
ms.author: koichih
ms.date: 08/21/2020
ms.topic: how-to
ms.custom: mvc, devx-track-azurecli
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 43a568149ccf85579b4f8190974fff872d6e09ea
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102199645"
---
# <a name="how-to-certify-iot-plug-and-play-devices"></a>Az IoT Plug and Play-eszközök minősítése

Az IoT Plug and Play eszközminősítési program olyan eszközöket tartalmaz, amelyekkel ellenőrizhető, hogy egy eszköz megfelel-e az IoT Plug and Play minősítési követelményeinek. Az eszközök segítséget nyújtanak a szervezeteknek a IoT Plug and Play-eszközök elérhetőségének megismerésében. Ezek a hitelesített eszközök IoT-megoldásokhoz vannak szabva, és segítenek csökkenteni a piacra kerülési időt.

Ez a cikk a következőkhöz nyújt útmutatást:

- Az Azure IoT parancssori eszköz bővítményének telepítése az Azure CLI-hez
- A IoT Plug and Play tesztek futtatásával érvényesítheti az eszköz alkalmazását a fejlesztés fázisában  
- Az eszköz alkalmazásának ellenőrzése az Azure Certified-eszköz portál használatával

## <a name="prepare-your-device"></a>Az eszköz előkészítése

A IoT futó alkalmazás kódjának a következőnek kell lennie Plug and Play:

- Kapcsolódjon az Azure IoT Hubhoz a [Device kiépítési szolgáltatás (DPS)](../iot-dps/about-iot-dps.md)használatával.
- Kövesse a [IoT plug an Play konvenciókat](concepts-convention.md) a telemetria, tulajdonságok és parancsok megvalósításához.

Az alkalmazás olyan szoftver, amelyet az operációs rendszertől függetlenül telepítenek, vagy az operációs rendszerbe van csomagolva egy, az eszközre mutató belső vezérlőprogram-rendszerképben.

A minősítési folyamat ellenőrzi, hogy az eszköz IoT Plug and Play kompatibilis-e, ha ellenőrzi, hogy az eszköz implementációja megfelel-e a [digitális Twins Definition Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl) modellben meghatározott telemetria, tulajdonságoknak és parancsoknak, valamint arról, hogy a modell elérhető az [Azure IoT Public Model repositoryban](concepts-model-repository.md).

A minősítési követelmények teljesítéséhez az eszköznek a következőket kell tennie:

- Csatlakozás az Azure IoT Hubhoz a [DPS](../iot-dps/about-iot-dps.md)használatával.
- Telemetria, tulajdonságok vagy parancsok implementálása a IoT Plug and Play konvenciót követve.
- Írja le az eszköz interakcióit egy [DTDL v2](https://aka.ms/dtdl) -modellel.
- Tegye közzé a modellt és az összes szükséges felületet az Azure IoT nyilvános modell adattárában.
- Küldje el a modell AZONOSÍTÓját a DPS- [regisztráció](./concepts-developer-guide-device.md#dps-payload) során a DPS kiépítési hasznos adattartalomban.
- Bejelenti a modell AZONOSÍTÓját a [MQTT-kapcsolatban](./concepts-developer-guide-device.md#model-id-announcement).
- Minden eszköz modellnek kompatibilisnek kell lennie az [Azure IoT Centralával](../iot-central/core/overview-iot-central-developer.md).

> [!NOTE]
> Az Azure IoT Central jelenleg nem támogatja teljes mértékben a **tömb** -és **térinformatikai** DTDL adattípusait.

## <a name="test-with-the-azure-iot-extension-cli"></a>Tesztelés az Azure IoT-bővítmény parancssori felületével

Az [Azure IOT CLI bővítményével](/cli/azure/ext/azure-iot/iot/product) ellenőrizheti, hogy az eszköz implementációja megfelel-e a modellnek, mielőtt beküldi az eszközt a minősítéshez az Azure Certified Device Portalon keresztül.

A következő lépések bemutatják, hogyan készítheti elő és futtathatja a minősítési teszteket a parancssori felület használatával:

### <a name="install-the-azure-iot-extension-for-the-azure-cli"></a>Az Azure IoT bővítmény telepítése az Azure CLI-hez

Tekintse meg a telepítési utasításokat az [Azure CLI](/cli/azure) környezetbe állításához.

Az Azure IoT bővítmény telepítéséhez futtassa a következő parancsot:

```azurecli
az extension add --name azure-iot
```

További információ: [Azure CLI az Azure IoT](/cli/azure/azure-cli-reference-for-iot)-hoz.

### <a name="create-a-new-product-test"></a>Új termék tesztelésének létrehozása

A következő parancs egy tesztet hoz létre a DPS használatával szimmetrikus kulcsú igazolási módszerrel:

- Létrehoz egy új terméket a teszteléshez, és létrehoz egy tesztelési konfigurációt. A kimenet megjeleníti azokat a DPS-információkat, amelyeket az eszköznek használnia kell az üzembe helyezéshez: az elsődleges kulcs, az eszköz azonosítója és az azonosító hatóköre.
- A modellt leíró DTDL-fájlokat tartalmazó mappát adja meg.

```azurecli
az iot product test create --badge-type Pnp --at SymmetricKey --device-type FinishedProduct --models {local folder name}
```

> [!NOTE]
> A CLI használatakor be kell [jelentkeznie](/cli/azure/authenticate-azure-cli) az előfizetésbe.

A parancs JSON-kimenete tartalmazza a `primaryKey` , `registrationId` , és az `scopeID` eszköz csatlakoztatásakor használni kívánt elemet.

Várt kimenet:

```json
"deviceType": "FinishedProduct",
"id": "d45d53d9-656d-4be7-9bbf-140bc87e98dc",
"provisioningConfiguration": {
  "symmetricKeyEnrollmentInformation": {
    "primaryKey":"Ci/Ghpqp0n4m8FD5PTicr6dEikIfM3AtVWzAhObU7yc=",
    "registrationId": "d45d53d9-656d-4be7-9bbf-140bc87e98dc",
    "scopeId": "0ne000FFA42"
  }
}
```

### <a name="connect-your-device"></a>Az eszköz csatlakoztatása

Az előző parancs által megadott DPS Information kimenettel az eszközt a test IoT Hub-példánnyal összekapcsolhatjuk.

### <a name="manage-and-configure-the-product-tests"></a>A termékek tesztelésének kezelése és konfigurálása

Ha az eszköz csatlakoztatva van, és készen áll az IoT hub használatával való interakcióra, állítson elő egy termékmodell-konfigurációs fájlt. A fájl létrehozása:

- Használja az `id` előző parancs kimenetében található tesztet.
- Használja a `--wait` paramétert a tesztelési eset beszerzéséhez.

```azurecli
az iot product test task create --type GenerateTestCases --test-id d45d53d9-656d-4be7-9bbf-140bc87e98dc --wait
```

Várt kimenet:

```json
{
  "deviceTestId": "d45d53d9-656d-4be7-9bbf-140bc87e98dc",
  "error": null,
  "id": "526da38e-91fc-4e20-a761-4f04b392c42b",
  "resultLink": "/deviceTests/d45d53d9-656d-4be7-9bbf-140bc87e98dc/TestCases",
  "status": "Completed",
  "type": "GenerateTestCases"
}
```

A parancsot használhatja a `az iot product test case update` teszt konfigurációs fájljának módosítására.

### <a name="run-the-tests"></a>A tesztek futtatása

A tesztelési konfiguráció létrehozása után a következő lépés a tesztek futtatása. A `devicetestId` tesztek futtatásához használja az előző parancsokkal megegyezőt a paraméterrel. Ellenőrizze a teszt eredményeit, és győződjön meg arról, hogy minden teszt rendelkezik állapottal `Passed` .

```azurecli
az iot product test task create --type QueueTestRun --test-id d45d53d9-656d-4be7-9bbf-140bc87e98dc --wait
```

Példa teszt futtatása kimenetre

```json
      "validationTasks": [
        {
          "componentName": "Default component",
          "endTime": "2020-08-25T05:18:49.5224772+00:00",
          "interfaceId": "dtmi:com:example:TemperatureController;1",
          "logs": [
            {
              "message": "Waiting for telemetry from the device",
              "time": "2020-08-25T05:18:37.3862586+00:00"
            },
            {
              "message": "Validating PnP properties",
              "time": "2020-08-25T05:18:37.3875168+00:00"
            },
            {
              "message": "Validating PnP commands",
              "time": "2020-08-25T05:18:37.3894343+00:00"
            },
            {
              "message": "{\"propertyName\":\"serialNumber\",\"expectedSchemaType\":null,\"actualSchemaType\":null,\"message\":\"Property is successfully validated\",\"passed\":true,\"time\":\"2020-08-25T05:18:37.4205985+00:00\"}",
              "time": "2020-08-25T05:18:37.4205985+00:00"
            },
            {
              "message": "PnP interface properties validation passed",
              "time": "2020-08-25T05:18:37.4206964+00:00"
            },
```

## <a name="test-using-the-azure-certified-device-portal"></a>Tesztelés az Azure Certified Device portállal

Az alábbi lépések bemutatják, hogyan használhatja az [Azure Certified Device Portalt](https://aka.ms/acdp) a bevezetéshez, a termék adatainak regisztrálásához, az első lépéseket ismertető útmutató elküldéséhez, valamint a minősítési tesztek futtatásához.

### <a name="onboarding"></a>Előkészítés

A [minősítési portál](https://aka.ms/acdp)használatához Azure Active Directory kell használnia a munkahelyi vagy iskolai bérlőtől.

Ha közzé szeretné tenni a modelleket az [Azure IoT nyilvános modell adattárában](https://github.com/Azure/iot-plugandplay-models), a fióknak a [Microsoft Partner Network](https://partner.microsoft.com)tagjának kell lennie. A rendszer ellenőrzi, hogy a Microsoft Partner Network-azonosító létezik-e, és hogy a fiók teljes mértékben át lett-e adva az eszköz-katalógusba való közzététel előtt.

### <a name="company-profile"></a>Vállalati profil

A céges profilt a bal oldali navigációs menüből kezelheti. A vállalati profil tartalmazza a vállalati URL-címet, az e-mail-címet és a vállalati emblémát. Ezen a lapon el kell fogadni a programra vonatkozó szerződést, mielőtt bármilyen minősítési műveletet futtatná.

A vállalati profil adatai az eszköz katalógusában bemutatott eszköz leírásában használatosak.

### <a name="create-new-project"></a>Új projekt létrehozása

Egy eszköz hitelesítéséhez először létre kell hoznia egy új projektet.

Navigáljon a [minősítési portálra](https://aka.ms/acdp). A **projektek** lapon válassza az *+ új projekt létrehozása* lehetőséget. Ezután adja meg a projekt nevét, az eszköz nevét, és válassza ki az eszköz osztályát.

A minősítési folyamat során megadott termékinformációk négy kategóriába sorolhatók:

- Eszköz adatai. Adatokat gyűjt az eszközről, például annak nevét, leírását, tanúsítványait és operációs rendszerét.
- Az **első lépések** útmutatója. Az útmutatót a rendszergazda által az eszköz közzététele előtt jóvá kell hagynia PDF-dokumentumként.
- Marketing részletei. Adja meg az eszközre vonatkozó, az ügyfél számára elérhető marketing-információkat. A marketing információi közé tartozik a leírás, a fénykép és a terjesztők.
- További iparági minősítések. Ez a választható szakasz további információk megadását teszi lehetővé az eszköz által kapott egyéb tanúsítványokról.

### <a name="connect-and-test"></a>Kapcsolat és tesztelés

A csatlakozási és tesztelési lépés ellenőrzi, hogy az eszköz megfelel-e a IoT Plug and Play minősítési követelményeinek.

Három lépést kell végrehajtania:

1. Kapcsolódási és felderítési felületek. Az eszköznek a DPS-n keresztül kell csatlakoznia az Azure IoT Certificate szolgáltatáshoz. Válassza ki a hitelesítési módszert (X. 509 tanúsítvány, szimmetrikus kulcs vagy platformmegbízhatósági modul), és frissítse az eszközt a DPS-információkkal.
1. Tekintse át a csatolókat. Tekintse át az illesztőfelületet, és győződjön meg arról, hogy mindegyik rendelkezik olyan hasznos adatokkal, amelyek a tesztelés szempontjából ésszerűek.
1. Teszt. A rendszer minden eszköz modelljét teszteli, és ellenőrzi, hogy a modellben ismertetett telemetria, tulajdonságok és parancsok követik-e a IoT Plug and Play konvencióit. Ha a teszt elkészült, a **naplók megtekintése** hivatkozásra kattintva megtekintheti a telemetria az eszközről és a nyers adatok IoT hub eszköz Twin tulajdonságai között.

### <a name="submit-and-publish"></a>Küldés és közzététel

Az utolsó szükséges szakasz a projekt véleményezésre való elküldése. Ez a lépés értesíti az Azure Certified eszköz csapattagot, hogy áttekintse a projekt teljességét, beleértve az eszköz és a marketing részleteit, valamint az első lépéseket ismertető útmutatót. A csapattag felveheti Önnel a kapcsolatot a korábban a kérdésekkel vagy a kérések szerkesztése előtt megadott vállalati e-mail-címen a jóváhagyás előtt.

Ha az eszközön további manuális ellenőrzésre van szükség a minősítés részeként, akkor a rendszer jelenleg értesítést fog kapni.

Egy eszköz minősítése **esetén a termék** összegzése lapon közzéteheti a termék részleteit az Azure Certified-eszköz katalógusában.

## <a name="next-steps"></a>Következő lépések

Az eszköz beküldésének befejezése után a következő lépésekkel léphet kapcsolatba az eszköz minősítési csapatával, [iotcert@microsoft.com](mailto:iotcert@microsoft.com) amely tartalmazza a Microsoft Partner Network tagságának ellenőrzését és az első lépéseket ismertető útmutatók áttekintését. Ha az összes követelmény teljesül, dönthet úgy, hogy az eszközt belefoglalja az [Azure IoT-eszközökre tanúsított tanúsítvánnyal](https://aka.ms/devicecatalog).