---
title: Eszközfejlesztés Azure IoT Central | Microsoft Docs
description: Az Azure IoT Central egy IoT-alkalmazásplatform, amely megkönnyíti IoT-megoldások létrehozását. Ez a cikk áttekintést nyújt a saját alkalmazásához IoT Central eszközök fejlesztéséhez. Az eszközök telemetriai adatokat és tulajdonságokat küldenek az eszköz állapotának jelentéséhez. Az Iot Central írható tulajdonságokkal állíthatja be az eszköz állapotát, és parancsokat hívhat meg az eszközön.
author: dominicbetts
ms.author: dobett
ms.date: 05/05/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom:
- mvc
- device-developer
ms.openlocfilehash: ebd2759d4dfb8ee79130f9b4876eba8d45226d04
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718790"
---
# <a name="iot-central-device-development-guide"></a>IoT Central eszközfejlesztési útmutató

*Ez a cikk az eszközfejlesztőkre vonatkozik.*

Egy IoT Central alkalmazással eszközök millióit figyelheti és kezelheti azok életciklusa során. Ez az útmutató olyan eszközfejlesztőknek szól, akik olyan kódot implementáljanak, amely az eszközhöz IoT Central.

Az eszközök a következő IoT Central kommunikálnak egy alkalmazással:

- _A telemetria_ az eszköz által a IoT Central. Például egy hőmérsékletértékek streamje egy alaplapi érzékelőtől.
- _A_ tulajdonságok olyan állapotértékek, amelyekről az eszköz jelentést IoT Central. Például az eszköz aktuális belsővezérlőprogram-verziója. Emellett olyan írható tulajdonságokkal is IoT Central, amelyek frissíthetőek az eszközön, például a célhőmérsékletet.
- _A parancsokat_ a IoT Central az eszköz viselkedésének vezérlése érdekében. Előfordulhat például, hogy IoT Central-alkalmazás egy parancsot hív meg az eszköz újraindításához.

A megoldáskészítő feladata, hogy irányítópultokat és nézeteket konfiguráljon a IoT Central webes felhasználói felületén a telemetria megjelenítéséhez, a tulajdonságok kezeléséhez és a parancsok hívásához.

## <a name="types-of-device"></a>Eszköztípusok

A következő szakaszok ismertetik a fő eszköztípusokat, amelyekhez egy IoT Central csatlakozhat:

### <a name="standalone-device"></a>Önálló eszköz

A különálló eszközök közvetlenül csatlakoznak a IoT Central. Az önálló eszközök általában telemetriai adatokat küldnek az alaplapról vagy a csatlakoztatott érzékelőkről az IoT Central alkalmazásba. Az önálló eszközök tulajdonságértékeket is jelenteni tudnak, írható tulajdonságértékeket fogadhatnak, és válaszolnak a parancsokra.

### <a name="gateway-device"></a>Átjáróeszköz

Az átjáróeszköz egy vagy több lefelé irányuló eszközt kezel, amelyek csatlakoznak az IoT Central alkalmazáshoz. A IoT Central konfigurálhatja az lefelé irányuló eszközök és az átjáróeszköz közötti kapcsolatokat. További információ: [Új IoT-átjáróeszköz](./tutorial-define-gateway-device-type.md)típusának definiálás a Azure IoT Central alkalmazásban.

### <a name="edge-device"></a>Edge-eszköz

A peremhálózati eszközök közvetlenül csatlakoznak a IoT Central, de közvetítőként viselkednek más eszközök, más néven _levéleszközök esetében._ A peremhálózati eszközök általában a levéleszközök közelében találhatók, amelyeknél közvetítőként működik. A peremeszközökön a következő forgatókönyvek használhatók:

- A peremeszközön keresztüli csatlakozás engedélyezése az IoT Central nem tud közvetlenül csatlakozni az eszközhöz. Egy levéleszköz például Bluetooth-kapcsolattal csatlakozhat a peremeszközhöz, amely ezután az interneten keresztül csatlakozik a IoT Central.
- Összesíti a telemetriát, mielőtt a rendszer elküldi IoT Central. Ez a megközelítés segíthet csökkenteni az adatküldés költségeit a IoT Central.
- A levéleszközök helyi szabályozásával elkerülheti a csatlakozással járó késést, IoT Central az interneten keresztül.

A peremhálózati eszközök saját telemetriát is küldhetnek, jelentést küldhetnek a tulajdonságaikról, és válaszolhat az írható tulajdonságok frissítésére és parancsára.

IoT Central csak a peremeszközt látja, a peremeszközhöz csatlakoztatott levéleszközöket nem.

További információ: [Új Azure IoT Edge hozzáadása a Azure IoT Central alkalmazáshoz.](./tutorial-add-edge-as-leaf-device.md)

## <a name="connect-a-device"></a>Eszköz csatlakoztatása

Azure IoT Central a Azure IoT Hub [Device Provisioning Service (DPS)](../../iot-dps/about-iot-dps.md) segítségével kezeli az összes eszközregisztrációt és -kapcsolatot.

A DPS használata a következőt teszi lehetővé:

- IoT Central támogatja az eszközök nagy léptékű be- és csatlakoztatását.
- Létrehozhatja az eszköz hitelesítő adatait, és offline konfigurálhatja az eszközöket anélkül, hogy regisztrálnia IoT Central felhasználói felületen.
- Az eszközök regisztrálásához saját eszköz-IoT Central. A saját eszköz-adattitkok használata leegyszerűsíti a meglévő háttérrendszerekkel való integrációt.
- Egyetlen, konzisztens módszer az eszközök és a IoT Central.

További tudnivalókért lásd: [Csatlakozás](./concepts-get-connected.md) a Azure IoT Central és [az ajánlott eljárásokhoz.](concepts-best-practices.md)

### <a name="security"></a>Biztonság

Az eszköz és a IoT Central közötti kapcsolatot közös [](./concepts-get-connected.md#sas-group-enrollment) hozzáférésű jogosultságok vagy iparági szabványnak megfelelő [X.509-tanúsítványok biztosítják.](./concepts-get-connected.md#x509-group-enrollment)

### <a name="communication-protocols"></a>Kommunikációs protokollok

A kommunikációs protokollok, amelyek segítségével az eszköz IoT Central az MQTT, az AMQP és a HTTPS protokollokat. Belsőleg a IoT Central IoT Hubot használ az eszközkapcsolat engedélyezéséhez. Az eszközkapcsolathoz támogatott kommunikációs protokollokkal kapcsolatos IoT Hub lásd: [Kommunikációs protokoll kiválasztása.](../../iot-hub/iot-hub-devguide-protocols.md)

## <a name="implement-the-device"></a>Az eszköz megvalósítása

A IoT Central eszközsablon tartalmaz egy _modellt,_ amely meghatározza, hogy az adott típusú eszköznek milyen viselkedést kell megvalósítania. A viselkedések közé tartozik a telemetria, a tulajdonságok és a parancsok.

> [!TIP]
> A modellt exportálhatja IoT Central [DTDL (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl) Digital Twins JSON-fájlként.

Minden modell egyedi  ikereszközmodell-azonosítóval (DTMI) rendelkezik, például: `dtmi:com:example:Thermostat;1` . Amikor egy eszköz csatlakozik a IoT Central, elküldi az implementálja modell DTMI-ját. IoT Central a megfelelő eszközsablont társíthatja az eszközhöz.

[Az IoT Plug and Play](../../iot-pnp/overview-iot-plug-and-play.md) definiálja azokat a konvenciókat, amelyek alapján az eszközöknek követniük kell a DTDL-modell megvalósításakor.

Az [Azure IoT eszköz-SDK-k](#languages-and-sdks) támogatják az IoT Plug and Play konvencióit.

### <a name="device-model"></a>Eszközmodell

Az eszközmodell a [DTDL használatával van definiálva.](https://github.com/Azure/opendigitaltwins-dtdl) Ezzel a nyelvvel a következőt határozhatja meg:

- Az eszköz által küldött telemetria. A definíció tartalmazza a telemetria nevét és adattípusát. Egy eszköz például kettős hőmérsékleti telemetriát küld.
- A tulajdonságok, amelyekről az eszköz jelentést IoT Central. A tulajdonságdefiníció tartalmazza a nevét és adattípusát. Egy eszköz például logikai értékként jelenti egy szerelvény állapotát.
- Az eszköz által az eszköz által a IoT Central. A tulajdonságot írhatóként is megjelölheti. Például a IoT Central a célhőmérsékletet kettősértékként küldi el egy eszköznek.
- Az eszköz által megválaszolt parancsok. A definíció tartalmazza a parancs nevét, valamint a paraméterek nevét és adattípusát. Egy eszköz például egy újraindítási parancsra válaszol, amely meghatározza, hogy hány másodpercet kell várni az újraindítás előtt.

A DTDL-modell lehet nem _összetevő vagy_ _több összetevőből álló_ modell:

- Összetevőt nem tartalmazó modell: Egy egyszerű modell nem használ beágyazott vagy egymásra épülő összetevőket. Minden telemetria, tulajdonság és parancs egyetlen alapértelmezett összetevőt _határoz meg._ Példaként tekintse meg a [Termosztát modellt.](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json)
- Több összetevőből álló modell. Egy összetettebb modell, amely kettő vagy több összetevőt tartalmaz. Ezek az összetevők egyetlen alapértelmezett összetevőt és egy vagy több további beágyazott összetevőt tartalmaznak. Példaként tekintse meg a [Hőmérsékletvezérlő modellt.](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json)

További információ: [IoT Plug and Play modellezési útmutató](../../iot-pnp/concepts-modeling-guide.md)

### <a name="conventions"></a>Konvenciók

Az eszközöknek követnie kell az IoT Plug and Play konvencióit, amikor adatokat IoT Central. A konvenciók a következők:

- Küldje el a DTMI-t, amikor csatlakozik IoT Central.
- Helyesen formázott JSON-hasznos adatokat és metaadatokat küldhet a IoT Central.
- Helyesen reagáljon az írható tulajdonságokra és parancsokra a IoT Central.
- Kövesse az összetevőkre vonatkozó parancsok elnevezési konvencióit.

> [!NOTE]
> Jelenleg a IoT Central nem támogatja teljes mértékben a  DTDL-tömb és a **térinformatikai** adattípusokat.

Ha többet szeretne megtudni a JSON-üzenetek formátumával kapcsolatban, amelyek az eszköz és a IoT Central, a telemetria, a tulajdonság és a parancsok hasznos [adatainak formátumával kapcsolatban.](concepts-telemetry-properties-commands.md)

További információ az IoT Plug and Play konvencióiról: [IoT Plug and Play konvenciók.](../../iot-pnp/concepts-convention.md)

### <a name="device-sdks"></a>Eszköz-SDK-k

Az eszköz viselkedésének megvalósításához használja az [Egyik Azure IoT eszköz-SDK-t.](../../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks) A kódnak a következőnek kell lennie:

- Regisztrálja az eszközt a DPS-ben, és a DPS információival csatlakozzon a belső IoT Hubhoz a IoT Central alkalmazásban.
- Jelentse be az eszköz által implementálja modell DTMI-ját.
- Telemetria küldése az eszközmodell által megadott formátumban. IoT Central az eszközsablon modelljével határozzák meg, hogyan használható a telemetria vizualizációkhoz és elemzésekhez.
- Szinkronizálja a tulajdonságértékeket az eszköz és a IoT Central. A modell megadja a tulajdonságneveket és adattípusokat, hogy IoT Central meg tudja jelenni az információkat.
- Implementálja a modellben megadott parancsok parancskezelőit. A modell határozza meg az eszköz által használt parancsneveket és paramétereket.

További információ az eszközsablonok szerepköréről: Mik azok az [eszközsablonok?](./concepts-device-templates.md).

Néhány mintakódért lásd: [Ügyfélalkalmazás létrehozása és csatlakoztatása.](./tutorial-connect-device.md)

### <a name="languages-and-sdks"></a>Nyelvek és SDK-k

További információ a támogatott nyelvekről és az SDK-król: [Az Azure IoT Hub használata.](../../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks)

## <a name="next-steps"></a>Következő lépések

Ha Ön eszközfejlesztő, és szeretne belemerülni néhány kódba, a javasolt következő lépés az ügyfélalkalmazás létrehozása és csatlakoztatása a Azure IoT Central [alkalmazáshoz.](./tutorial-connect-device.md)

Ha többet szeretne megtudni a IoT Central használatával kapcsolatban, a következő javasolt lépések a gyorsútmutatók, kezdve a Create an Azure IoT Central application (Új alkalmazás [létrehozása) Azure IoT Central lépéssel.](./quick-deploy-iot-central.md)
