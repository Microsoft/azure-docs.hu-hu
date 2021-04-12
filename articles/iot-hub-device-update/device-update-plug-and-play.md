---
title: Ismerje meg, hogyan használja a IoT Hub az eszköz IoT-Plug and Playét | Microsoft Docs
description: A IoT Hub eszköz frissítése olyan eszközök felderítésére és felügyeletére szolgál, amelyeken a légi frissítés képes.
author: ValOlson
ms.author: valls
ms.date: 2/14/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 0283a84650abaadd454b4f5bca83d1473e443fb8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105561814"
---
# <a name="device-update-for-iot-hub-and-iot-plug-and-play"></a>IoT Hub-és IoT tartozó eszköz frissítése Plug and Play

A IoT Hub eszköz frissítése a [IoT-Plug and Play](../iot-pnp/index.yml) használatával észleli és felügyeli az olyan eszközöket, amelyek a hálózaton kívüli frissítésre képesek. Az eszköz frissítési szolgáltatása PnP-felületek használatával küldi el és fogadja az eszközökre irányuló és onnan érkező tulajdonságokat és üzeneteket. A IoT Hub eszköz-frissítéséhez a IoT-eszközöknek az alább leírtak szerint a következő csatolókat és modell-azonosítót kell alkalmazniuk.

## <a name="adu-core-interface"></a>ADU Core interfész

Az "ADUCoreInterface" interfész a frissítési műveletek és metaadatok küldésére szolgál az eszközökre, és frissítési állapotot fogad az eszközökről. Az "ADU mag" illesztőfelület két objektum tulajdonságaira van felosztva.

A modellben a várt összetevő neve **"azureDeviceUpdateAgent"** a csatoló megvalósításakor. [További információ az Azure IoT PnP-összetevőiről](../iot-pnp/concepts-components.md)

### <a name="agent-metadata"></a>Ügynök metaadatai

Az ügynök metaadatai olyan mezőket tartalmaznak, amelyekkel az eszköz vagy az eszköz frissítési ügynöke adatokat és állapotot küld az eszköz frissítési szolgáltatásainak.

|Name|Séma|Irány|Leírás|Példa|
|----|------|---------|-----------|-----------|
|resultCode|egész szám|eszközről a felhőbe|Az utolsó frissítési művelet eredményével kapcsolatos információkat tartalmazó kód. Akár sikeres, akár sikertelen volt, és a [http-állapotkód specifikációjának](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)kell követnie.|500|
|extendedResultCode|egész szám|eszközről a felhőbe|Az eredménnyel kapcsolatos további információkat tartalmazó kód. Akár sikeres, akár sikertelen lehet.|0x80004005|
|állapot|egész szám|eszközről a felhőbe|Ez egy egész szám, amely az eszköz frissítési ügynökének aktuális állapotát jelzi. Részletekért lásd alább |Tétlen|
|installedUpdateId|sztring|eszközről a felhőbe|A jelenleg telepített frissítés azonosítója (az eszköz frissítése révén). Ez az érték null értékű lesz egy olyan eszköz esetében, amely még nem készített frissítést az eszköz frissítésén keresztül.|Null|
|`deviceProperties`|Térkép|eszközről a felhőbe|A gyártót és a modellt tartalmazó tulajdonságok halmaza.|Részletekért lásd alább

#### <a name="state"></a>Állapot

Ez az eszköz frissítési ügynöke által jelentett állapot, miután az eszköz frissítési szolgáltatásában műveletet kapott. `State` az az eszköz frissítési `Action` ügynökének küldött, a következőre adott válaszként (lásd `Actions` alább). Tekintse meg az eszköz frissítési szolgáltatása és az eszköz frissítési ügynöke között zajló kérelmek [áttekintő munkafolyamatát](understand-device-update.md#device-update-agent) .

|Name|Érték|Leírás|
|---------|-----|-----------|
|Tétlen|0|Az eszköz készen áll egy művelet fogadására az eszköz frissítési szolgáltatásában. Sikeres frissítés után a rendszer visszaadja az állapotot az `Idle` állapotnak.|
|DownloadSucceeded|2|Sikeres letöltés.|
|InstallSucceeded|4|Sikeres telepítés.|
|Sikertelen|255|Hiba történt A frissítés során.|

#### <a name="device-properties"></a>Eszköztulajdonságok

Ez a gyártót és a modellt tartalmazó tulajdonságok halmaza.

|Name|Séma|Irány|Leírás|
|----|------|---------|-----------|
|gyártó|sztring|eszközről a felhőbe|A által jelentett eszköz gyártója `deviceProperties` . Ezt a tulajdonságot két hely közül kell olvasni – a "AzureDeviceUpdateCore" felület először megpróbálja beolvasni a "aduc_manufacturer" értéket a [konfigurációs fájlból](device-update-configuration-file.md) .  Ha az érték nincs kitöltve a konfigurációs fájlban, akkor alapértelmezés szerint a ADUC_DEVICEPROPERTIES_MANUFACTURER fordítási idejű definícióját fogja jelenteni. Ezt a tulajdonságot a rendszer csak a rendszerindítás során fogja jelenteni.|
|modell|sztring|eszközről a felhőbe|Az eszközön a által jelentett eszköz modellje `deviceProperties` . Ezt a tulajdonságot két hely közül kell olvasni – a AzureDeviceUpdateCore felület először megpróbálja beolvasni a "aduc_model" értéket a [konfigurációs fájlból](device-update-configuration-file.md) .  Ha az érték nincs kitöltve a konfigurációs fájlban, akkor alapértelmezés szerint a ADUC_DEVICEPROPERTIES_MODEL fordítási idejű definícióját fogja jelenteni. Ezt a tulajdonságot a rendszer csak a rendszerindítás során fogja jelenteni.|
|aduVer|sztring|eszközről a felhőbe|Az eszközön futó eszköz frissítési ügynökének verziója. Ezt az értéket csak akkor olvassa a rendszer, ha a fordítás ideje alatt ENABLE_ADU_TELEMETRY_REPORTING értéke 1 (igaz). Az ügyfelek dönthetnek úgy, hogy letiltják a verziószámot 0 (hamis) értékre állítva. [Az eszköz frissítési ügynök tulajdonságainak testreszabása](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-build-agent-code.md)|
|doVer|sztring|eszközről a felhőbe|Az eszközön futó kézbesítési optimalizálási ügynök verziója. Az értéket csak akkor olvassa a rendszer, ha a fordítási idő során ENABLE_ADU_TELEMETRY_REPORTING értéke 1 (igaz). Az ügyfelek dönthetnek úgy, hogy letiltják a verziószámot 0 (false) értékre állítva. [A kézbesítési optimalizálási ügynök tulajdonságainak testreszabása](https://github.com/microsoft/do-client/blob/main/README.md#building-do-client-components)|

### <a name="service-metadata"></a>Szolgáltatás metaadatainak

A szolgáltatás metaadatai olyan mezőket tartalmaznak, amelyeket az eszköz frissítési szolgáltatásai a műveletek és az adatok az eszköz frissítési ügynökével való kommunikációra használnak.

|Name|Séma|Irány|Leírás|
|----|------|---------|-----------|
|művelet|egész szám|felhőből eszközre|Ez egy egész szám, amely megfelel egy olyan műveletnek, amelyet az ügynöknek végre kell hajtania. Alább felsorolt értékek.|
|updateManifest|sztring|felhőből eszközre|Egy frissítés tartalmának leírására szolgál. Az [importálási jegyzékből](import-update.md#create-device-update-import-manifest) létrehozva|
|updateManifestSignature|JSON-objektum|felhőből eszközre|JSON webes aláírás (JWS), amely a forrás ellenőrzéséhez használt JSON-webkulcsokkal rendelkezik.|
|fileUrls|Térkép|felhőből eszközre|Leképezés a következőre: `FileHash` `DownloadUri` . Megadja az ügynököt, a letölteni kívánt fájlokat és a fájlok helyes letöltésének ellenőrzéséhez használt kivonatot.|

#### <a name="action"></a>Művelet

`Actions` az alábbi az eszköz frissítési ügynöke által az eszköz frissítési szolgáltatásának utasításai szerint végrehajtott műveleteket jelöli. Az eszköz frissítési ügynöke a `State` kapott jelentést (lásd a `State` fenti szakaszt) dolgozza fel `Action` . Tekintse meg az eszköz frissítési szolgáltatása és az eszköz frissítési ügynöke között zajló kérelmek [áttekintő munkafolyamatát](understand-device-update.md#device-update-agent) .

|Name|Érték|Leírás|
|---------|-----|-----------|
|Letöltés|0|Közzétett tartalom vagy frissítés és egyéb szükséges tartalom letöltése|
|Telepítés|1|Telepítse a tartalmat vagy a frissítést. Ez általában azt jelenti, hogy meghívja a telepítőt a tartalomhoz vagy a frissítéshez.|
|Alkalmaz|2|Véglegesítse a frissítést. Szükség esetén a rendszer újraindítását jelzi.|
|Mégse|255|Állítsa le az aktuális művelet feldolgozását, és térjen vissza a következőre: `Idle` . Azt is felhasználjuk, hogy az ügynök az `Failed` állapotba lépjen vissza `Idle` .|

## <a name="device-information-interface"></a>Eszköz információs felülete

Az eszköz információi felülete a [IoT Plug and Play architektúrán](../iot-pnp/overview-iot-plug-and-play.md)belül használt fogalom. Az eszközről a felhő tulajdonságaira vonatkozó adatokat tartalmaz, amelyek információkat biztosítanak az eszköz hardveréről és operációs rendszeréről. A IoT Hub eszköz frissítése a DeviceInformation. manufactur és a DeviceInformation. Model tulajdonságot használja a telemetria és a diagnosztika számára. Az eszköz információs felületével kapcsolatos további tudnivalókért tekintse meg ezt a [példát](https://devicemodels.azure.com/dtmi/azure/devicemanagement/deviceinformation-1.json).

A modellben a várt összetevő neve **deviceInformation** a csatoló implementálása során. [További tudnivalók az Azure IoT PnP-összetevőiről](../iot-pnp/concepts-components.md)

|Név|Típus|Séma|Irány|Leírás|Példa|
|----|----|------|---------|-----------|-----------|
|gyártó|Tulajdonság|sztring|eszközről a felhőbe|Az eszköz gyártójának vállalatának neve. Ez lehet ugyanaz, mint az eredeti berendezésgyártó (OEM) neve.|Contoso|
|modell|Tulajdonság|sztring|eszközről a felhőbe|Az eszköz modelljének neve vagy azonosítója.|Eszköz IoT Edge|
|swVersion|Tulajdonság|sztring|eszközről a felhőbe|A szoftver verziója az eszközön. a swVersion lehet a belső vezérlőprogram verziója.|4.15.0-122|
|osName|Tulajdonság|sztring|eszközről a felhőbe|Az eszközön lévő operációs rendszer neve.|Ubuntu Server 18.04|
|processorArchitecture|Tulajdonság|sztring|eszközről a felhőbe|A processzor architektúrája az eszközön.|ARM64|
|processorManufacturer|Tulajdonság|sztring|eszközről a felhőbe|A processzor gyártójának neve az eszközön.|Microsoft|
|totalStorage|Tulajdonság|sztring|eszközről a felhőbe|Az eszközön rendelkezésre álló összes tárterület kilobájtban.|2048|
|totalMemory|Tulajdonság|sztring|eszközről a felhőbe|Az eszközön rendelkezésre álló memória teljes mérete (kilobájtban).|256|

## <a name="model-id"></a>Modellazonosító 

A Model ID azt mutatja be, hogy az intelligens eszközök hogyan reklámozzák képességeiket az Azure IoT-alkalmazásokban a IoT Plug and Play.To [Plug and Play](../iot-pnp/concepts-developer-guide-device.md)használatával

A IoT Hub eszköz frissítése megköveteli, hogy a IoT Plug and Play intelligens eszköz bejelentsen egy **"dtmi: AzureDeviceUpdate; 1"** értékű modell-azonosítót az eszköz kapcsolatának részeként. [Megtudhatja, hogyan jelentheti be a modell azonosítóját](../iot-pnp/concepts-developer-guide-device.md#model-id-announcement).