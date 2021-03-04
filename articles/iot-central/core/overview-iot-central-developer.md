---
title: Az Azure IoT Central eszközének fejlesztése | Microsoft Docs
description: Az Azure IoT Central egy IoT-alkalmazásplatform, amely megkönnyíti IoT-megoldások létrehozását. Ez a cikk áttekintést nyújt a IoT Central-alkalmazáshoz való kapcsolódáshoz szükséges eszközök fejlesztéséről. Az eszközök a telemetria használatával küldenek adatfolyam-adatátviteli és-tulajdonságokat az eszköz állapotának jelentéséhez. A IOT Central eszköz állapotának beállításához írható tulajdonságok és hívási parancsok használhatók az eszközökön.
author: dominicbetts
ms.author: dobett
ms.date: 05/05/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom:
- mvc
- device-developer
ms.openlocfilehash: f69bbecfc2acc24cd63b87212197342b28723a9f
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102043099"
---
# <a name="iot-central-device-development-guide"></a>IoT Central eszköz fejlesztési útmutatója

*Ez a cikk az eszközök fejlesztőire vonatkozik.*

Egy IoT Central alkalmazás lehetővé teszi több millió eszköz figyelését és kezelését életciklusuk során. Ez az útmutató olyan eszköz-fejlesztőknek készült, akik a IoT Centralhoz csatlakozó eszközökön futtatnak programkódot.

Az eszközök az alábbi primitívek használatával kommunikálnak IoT Central alkalmazásokkal:

- A _telemetria_ olyan adatokat mutatnak be, amelyeket az eszköz a IoT Centralnek küld. Például egy fedélzeti érzékelőből származó hőmérsékleti értékek streamje.
- A _Tulajdonságok_ olyan állapotinformációkat jelentenek, amelyeket az eszköz IoT Central. Például az eszköz aktuális belső vezérlőprogram-verziója. Használhat olyan írható tulajdonságokat is, amelyeket IoT Central frissíthet az eszközön, például a cél hőmérsékletét.
- Az eszközök viselkedésének szabályozására a rendszer a IoT Central _parancsokat_ hívja meg. Előfordulhat például, hogy a IoT Central alkalmazás meghív egy parancsot egy eszköz újraindítására.

A megoldás-szerkesztő feladata az irányítópultok és nézetek konfigurálása a IoT Central webes felületen a telemetria, a tulajdonságok kezelése és a hívási parancsok megjelenítéséhez.

## <a name="types-of-device"></a>Az eszköz típusai

A következő szakaszok ismertetik a IoT Central alkalmazáshoz csatlakoztatható eszközök fő típusait:

### <a name="standalone-device"></a>Önálló eszköz

Egy önálló eszköz közvetlenül csatlakozik IoT Centralhoz. Az önálló eszközök általában telemetria küldenek a bevezető vagy csatlakoztatott érzékelőkről a IoT Central alkalmazásba. Az önálló eszközök is jelenthetik a tulajdonságértékek jelentését, az írható tulajdonságértékek fogadását és a parancsok megválaszolását.

### <a name="gateway-device"></a>Átjáró eszköz

Az átjáró-eszköz egy vagy több alsóbb rétegbeli eszközt kezel, amely a IoT Central alkalmazáshoz csatlakozik. A IoT Central az alárendelt eszközök és az átjáró-eszköz közötti kapcsolatok konfigurálására használható. További információért lásd: [új IoT-átjáró eszköz típusának meghatározása az Azure IoT Central alkalmazásban](./tutorial-define-gateway-device-type.md).

### <a name="edge-device"></a>Peremhálózati eszköz

Az Edge-eszköz közvetlenül csatlakozik IoT Centralhoz, de közvetítőként működik más, _levélként_ ismert eszközökhöz. Az Edge-eszközök általában közel vannak azon Leaf-eszközökhöz, amelyek közvetítőként működnek. A peremhálózati eszközöket használó forgatókönyvek a következők:

- Olyan eszközök engedélyezése, amelyek nem tudnak közvetlenül kapcsolódni IoT Centralhoz a peremhálózati eszközön való kapcsolódáshoz. Előfordulhat például, hogy egy levél eszköz Bluetooth-kapcsolattal csatlakozik a peremhálózati eszközhöz, majd az interneten keresztül csatlakozik a IoT Centralhoz.
- A IoT Centralbe való elküldése előtt összesített telemetria. Ez a megközelítés segít csökkenteni az adatok IoT Centralba való küldésének költségeit.
- A Leaf-eszközök helyi vezérlése a IoT Central az interneten keresztüli csatlakozáshoz kapcsolódó késés elkerüléséhez.

Az Edge-eszközök a saját telemetria is elküldhetik, bejelenthetik a tulajdonságokat, és reagálnak az írható tulajdonságok frissítéseire és parancsaira.

IoT Central csak a peremhálózati eszközt látja, nem pedig a peremhálózati eszközhöz csatlakozó Leaf-eszközöket.

További információ: Azure IoT Edge- [eszköz hozzáadása az Azure IoT Central alkalmazáshoz](./tutorial-add-edge-as-leaf-device.md).

## <a name="connect-a-device"></a>Eszköz csatlakoztatása

Az Azure IoT Central az [azure IoT hub Device Provisioning Service (DPS)](../../iot-dps/about-iot-dps.md) használatával kezeli az összes eszköz regisztrációját és kapcsolódását.

A DPS használata lehetővé teszi a következőket:

- IoT Central az eszközök nagy léptékű bevezetésének és csatlakoztatásának támogatásához.
- Az eszköz hitelesítő adatainak előállításához és az eszközök offline konfigurálásához az eszközök IoT Central felhasználói felületen keresztül történő regisztrálása nélkül.
- A saját eszközök azonosítóinak használatával regisztrálhatja az eszközöket a IoT Centralban. A saját eszköz-azonosítók használatával egyszerűbbé válik a meglévő Back-Office rendszerekkel való integráció.
- Egyetlen, egységes módszer az eszközök IoT Centralhoz való csatlakoztatására.

További információért lásd: [Csatlakozás az Azure IoT Centralhoz](./concepts-get-connected.md) és az [ajánlott eljárások](concepts-best-practices.md).

### <a name="security"></a>Biztonság

Az eszköz és a IoT Central alkalmazás közötti kapcsolat a [megosztott hozzáférési aláírások](./concepts-get-connected.md#sas-group-enrollment) vagy az iparági szabványnak megfelelő [X. 509 tanúsítványok](./concepts-get-connected.md#x509-group-enrollment)használatával biztosítható.

### <a name="communication-protocols"></a>Kommunikációs protokollok

Azok a kommunikációs protokollok, amelyeket az eszköz használhat a IoT Centralhoz való kapcsolódáshoz, beleértve a MQTT, a AMQP és a HTTPS-t. Belsőleg a IoT Central egy IoT hubot használ az eszközök kapcsolatának engedélyezéséhez. A IoT Hub által támogatott kommunikációs protokollokról további információt a [kommunikációs protokoll kiválasztása](../../iot-hub/iot-hub-devguide-protocols.md)című témakörben talál.

## <a name="implement-the-device"></a>Az eszköz implementálása

Az IoT Central-eszköz sablonja olyan _modellt_ tartalmaz, amely meghatározza az adott típusú eszköz viselkedését. A viselkedések közé tartoznak a telemetria, a tulajdonságok és a parancsok.

> [!TIP]
> A modellt IoT Central [digitális Twins Definition Language (DTDL) v2](https://github.com/Azure/opendigitaltwins-dtdl) JSON-fájlként exportálhatja.

Mindegyik modellhez egyedi _eszköz – Twin Model-azonosító_ (DTMI) tartozik, például: `dtmi:com:example:Thermostat;1` . Amikor egy eszköz csatlakozik a IoT Centralhoz, elküldi az általa megvalósított modell DTMI. A IoT Central ezután társíthatja az eszközhöz a megfelelő sablont.

A [IoT Plug and Play](../../iot-pnp/overview-iot-plug-and-play.md) olyan konvenciókat határoz meg, amelyeket az eszköznek követnie kell a DTDL-modell megvalósításakor.

Az [Azure IoT Device SDK](#languages-and-sdks) -k támogatják a IoT Plug and Play konvenciókat.

### <a name="device-model"></a>Eszközmodell

Az eszköz modellje a [DTDL](https://github.com/Azure/opendigitaltwins-dtdl)használatával van definiálva. Ez a nyelv lehetővé teszi, hogy meghatározza a következőket:

- Az eszköz által küldött telemetria. A definíció tartalmazza a telemetria nevét és adattípusát. Egy eszköz például a hőmérséklet telemetria küldi el dupla értékként.
- Az eszköz által a IoT Centralra jelentett tulajdonságok. A tulajdonságok definíciója tartalmazza a nevét és az adattípust. Egy eszköz például logikai értékként jelenti egy szelep állapotát.
- Az eszköz által a IoT Centraltól fogadott tulajdonságok. Lehetőség van arra is, hogy egy tulajdonságot írhatóként jelölhet meg. A IoT Central például a célként megadott hőmérsékletet dupla értékre küldi egy eszköznek.
- Az eszköz által válaszoló parancsok. A definíció tartalmazza a parancs nevét, valamint a paraméterek nevét és adattípusát. Egy eszköz például egy újraindítási parancsra válaszol, amely meghatározza, hogy hány másodpercig kell várni az újraindítás előtt.

A DTDL-modell lehet _nem összetevő_ vagy _többösszetevős_ modell:

- Nem-összetevő modell: egy egyszerű modell nem használ beágyazott vagy lépcsőzetes összetevőket. Az összes telemetria, tulajdonság és parancs egyetlen _alapértelmezett összetevőt_ határoz meg. Példaként tekintse meg a [termosztát](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) modelljét.
- Több összetevőből álló modell. Összetettebb modell, amely két vagy több összetevőt tartalmaz. Ezek az összetevők egyetlen alapértelmezett összetevőt tartalmaznak, valamint egy vagy több további beágyazott összetevőt. Példaként tekintse meg a [hőmérséklet-vezérlő](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) modelljét.

További információ: [IoT Plug and Play Components in models](../../iot-pnp/concepts-components.md)

### <a name="conventions"></a>Konvenciók

Az eszközöknek a IoT Plug and Play konvenciókat kell követniük, amikor az IoT Centralsal végeznek adatcserét. A konvenciók a következők:

- Küldje el a DTMI, amikor csatlakozik IoT Centralhoz.
- Megfelelően formázott JSON-adattartalom és metaadatok küldése IoT Centralra.
- Megfelelően válaszoljon az írható tulajdonságokra és parancsokra IoT Centralból.
- Kövesse az összetevő-parancsok elnevezési konvencióit.

> [!NOTE]
> A IoT Central jelenleg nem támogatja teljes mértékben a DTDL- **tömb** és a **térinformatikai** adattípusok használatát.

Ha többet szeretne megtudni arról, hogy a JSON-üzenetek milyen formátumúak a IoT Centralsal való kommunikációhoz, tekintse meg a következőt: [telemetria, Property és Command hasznos](concepts-telemetry-properties-commands.md)adatok.

Ha többet szeretne megtudni a IoT Plug and Play konvenciókkal kapcsolatban, tekintse meg a következő témakört: [IoT Plug and Play Conventions](../../iot-pnp/concepts-convention.md).

### <a name="device-sdks"></a>Eszköz SDK-k

Az eszköz működésének megvalósításához használja az [Azure IoT Device SDK](#languages-and-sdks) -k egyikét. A kódnak a következőket kell tennie:

- Regisztrálja az eszközt a DPS-vel, és használja a DPS-ből származó információkat a IoT Central alkalmazás belső IoT hubhoz való kapcsolódáshoz.
- Bejelenti az eszköz által megvalósított modell DTMI.
- Küldje el a telemetria az eszköz modellje által megadott formátumban. IoT Central a modell használatával határozza meg, hogyan használható a telemetria a vizualizációk és az elemzések számára.
- Az eszköz és a IoT Central közötti tulajdonságértékek szinkronizálása. A modell megadja a tulajdonságokat és az adattípusokat, hogy a IoT Central megjelenjenek az adatok.
- A modellben megadott parancsokhoz tartozó parancssori kezelők implementálása. A modell megadja az eszköz által használandó parancsokat és paramétereket.

További információ az eszközök sablonjainak szerepköréről: Mik az [eszközök sablonjai?](./concepts-device-templates.md).

Néhány mintakód: [ügyfélalkalmazás létrehozása és összekapcsolása](./tutorial-connect-device.md).

### <a name="languages-and-sdks"></a>Nyelvek és SDK-k

További információ a támogatott nyelvekről és SDK-k használatáról: az [Azure IoT hub Device SDK-k megismerése és használata](../../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks).

## <a name="next-steps"></a>Következő lépések

Ha Ön egy eszköz fejlesztője, és szeretne belemerülni egy kódra, a javasolt következő lépés egy [ügyfélalkalmazás létrehozása és csatlakoztatása az Azure IoT Central-alkalmazáshoz](./tutorial-connect-device.md).

Ha többet szeretne megtudni a IoT Central használatáról, a javasolt következő lépések az [Azure IoT Central-alkalmazás létrehozása](./quick-deploy-iot-central.md)című rövid útmutatók kipróbálása.
