---
title: A microsoftos eszközök eszközfrissítésének IoT Hub IoT-Plug and Play | Microsoft Docs
description: Az eszközfrissítési IoT Hub a segítségével felderíti és felügyeli a vezeték nélküli frissítésre képes eszközöket.
author: ValOlson
ms.author: valls
ms.date: 2/14/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: fbc3502952e11830ef9abb06cb709fcc60288343
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739529"
---
# <a name="device-update-for-iot-hub-and-iot-plug-and-play"></a>Eszközfrissítés IoT Hub IoT-Plug and Play

Az eszközfrissítési IoT Hub [IoT-Plug and Play](../iot-pnp/index.yml) a vezeték nélküli frissítésre képes eszközök felderítésére és kezelésére. Az eszközfrissítési szolgáltatás PnP-felületeket használó eszközökre küld és fogad tulajdonságokat és üzeneteket. A szolgáltatáshoz IoT Hub eszközfrissítés megköveteli, hogy az IoT-eszközök az alábbi interfészeket és modellazonosítókat implementálja az alábbiakban leírtak szerint.

## <a name="adu-core-interface"></a>ADU Core Interface

Az "ADUCoreInterface" felület frissítési műveletek és metaadatok elküldését teszi lehetővé az eszközöknek, és frissítési állapotot fogad az eszközöktől. Az "ADU Core" felület két objektumtulajdonságra van felosztva.

A modellben a várt összetevőnév **az "azureDeviceUpdateAgent"** lesz az interfész megvalósításakor. [További információ az Azure IoT PnP összetevőiről](../iot-pnp/concepts-components.md)

### <a name="agent-metadata"></a>Ügynök metaadatai

Az ügynök metaadatai olyan mezőket tartalmaznak, amelyek segítségével az eszköz vagy az eszközfrissítési ügynök információkat és állapotadatokat küld az eszközfrissítési szolgáltatásoknak.

|Name|Séma|Irány|Leírás|Példa|
|----|------|---------|-----------|-----------|
|resultCode (eredménykód)|egész szám|eszközről a felhőbe|A legutóbbi frissítési művelet eredményére vonatkozó információkat tartalmazó kód. Sikeres vagy sikertelen állapotra is feltöltheti, és követnie kell a [HTTP-állapotkód specifikációját.](https://www.w3.org/Protocols/rfc2616/rfc2616-sec10.html)|500|
|extendedResultCode|egész szám|eszközről a felhőbe|Egy kód, amely további információkat tartalmaz az eredményről. Sikeres vagy sikertelen adatokkal is feltöltheti.|0x80004005|
|állapot|egész szám|eszközről a felhőbe|Ez egy egész szám, amely az eszközfrissítési ügynök aktuális állapotát jelzi. Részletekért lásd alább |Tétlen|
|installedUpdateId|sztring|eszközről a felhőbe|Az aktuálisan telepített frissítés azonosítója (eszközfrissítésen keresztül). Ez az érték null értékű lesz egy olyan eszköznél, amely még soha nem vett át frissítést az eszközfrissítésen keresztül.|Null|
|`deviceProperties`|Térkép|eszközről a felhőbe|A gyártót és a modellt tartalmazó tulajdonságkészlet.|Részletekért lásd alább

#### <a name="state"></a>Állapot

Ez az eszközfrissítési ügynök által jelentett állapot, miután az eszközfrissítési szolgáltatástól kapott egy műveletet. `State` A az eszközfrissítési szolgáltatás által az eszközfrissítési ügynöknek küldött `Action` (lásd `Actions` alább) válaszként lesz jelentve. Tekintse meg [az eszközfrissítési](understand-device-update.md#device-update-agent) szolgáltatás és az eszközfrissítési ügynök közötti kérelmek áttekintő munkafolyamatát.

|Name|Érték|Leírás|
|---------|-----|-----------|
|Tétlen|0|Az eszköz készen áll arra, hogy megkapja a műveletet az eszközfrissítési szolgáltatástól. A sikeres frissítés után a rendszer visszaadja az `Idle` állapotot.|
|DownloadSucceeded (Sikeres letöltés)|2|Sikeres letöltés.|
|InstallSucceeded (Sikeres telepítés)|4|Sikeres telepítés.|
|Sikertelen|255|Hiba történt a frissítés során.|

#### <a name="device-properties"></a>Eszköztulajdonságok

Ez a gyártót és a modellt tartalmazó tulajdonságok halmaza.

|Name|Séma|Irány|Description|
|----|------|---------|-----------|
|gyártó|sztring|eszközről a felhőbe|Az eszköz eszközgyártója, amely a következőn keresztül van jelentve: `deviceProperties` . Ezt a tulajdonságot két helyről olvassa be a rendszer – az "AzureDeviceUpdateCore" felület először megkísérli beolvassa az "aduc_manufacturer" értéket a [konfigurációs](device-update-configuration-file.md) fájlból.  Ha az érték nincs feltöltve a konfigurációs fájlban, akkor alapértelmezés szerint a jelentéskészítéskor jelenti a fordítási idő definícióját a ADUC_DEVICEPROPERTIES_MANUFACTURER. Ez a tulajdonság csak rendszerindításkor lesz jelentve.|
|modell|sztring|eszközről a felhőbe|Az eszköz eszközmodelle, amely a következőn keresztül jelentve: `deviceProperties` . Ezt a tulajdonságot két hely egyikéről olvassa be a rendszer – az AzureDeviceUpdateCore felület először megkísérli beolvasni a "aduc_model" értéket a [konfigurációs fájlból.](device-update-configuration-file.md)  Ha az érték nincs feltöltve a konfigurációs fájlban, akkor alapértelmezés szerint a jelentéskészítés során a rendszer a fordítási idő definícióját ADUC_DEVICEPROPERTIES_MODEL. Ez a tulajdonság csak rendszerindításkor lesz jelentve.|
|aduVer (aduVer)|sztring|eszközről a felhőbe|Az eszközön futó eszközfrissítési ügynök verziója. Ez az érték csak akkor lesz beolvasva a buildből, ha a fordítási ENABLE_ADU_TELEMETRY_REPORTING értéke 1 (igaz). Az ügyfelek dönthetnek úgy, hogy kikapcsolják a verziójelentést, ha az értéket 0 (hamis) értékre ják. [Az eszközfrissítési ügynök tulajdonságainak testreszabása.](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-build-agent-code.md)|
|Dover|sztring|eszközről a felhőbe|Az eszközön Kézbesítésoptimalizálás ügynök verziója. Az érték csak akkor lesz beolvasva a buildből, ha a fordítási idő ENABLE_ADU_TELEMETRY_REPORTING értéke 1 (igaz). Az ügyfelek dönthetnek úgy, hogy kikapcsolják a verziójelentést, ha a 0 (hamis) értéket választják. [Az ügynök tulajdonságainak Kézbesítésoptimalizálás testreszabása.](https://github.com/microsoft/do-client/blob/main/README.md#building-do-client-components)|

### <a name="service-metadata"></a>Szolgáltatás metaadatai

A szolgáltatás metaadatai olyan mezőket tartalmaznak, amelyek segítségével az eszközfrissítési szolgáltatások műveleteket és adatokat közölnek az eszközfrissítési ügynökkel.

|Name|Séma|Irány|Description|
|----|------|---------|-----------|
|action|egész szám|felhőből az eszközre|Ez egy olyan egész szám, amely egy olyan műveletnek felel meg, amit az ügynöknek el kell végeznie. Az alábbiakban felsorolt értékek.|
|updateManifest|sztring|felhőből az eszközre|Egy frissítés tartalmának leírására használatos. Az importálási [jegyzékből generálva](import-update.md#create-a-device-update-import-manifest)|
|updateManifestSignature|JSON-objektum|felhőből eszközre|JSON webaláírás (JWS) a forrás-ellenőrzéshez használt JSON-webkulcsokkal.|
|Fileurls|Térkép|felhőből eszközre|Leképezés `FileHash` `DownloadUri` a-hoz. Közli az ügynökkel, hogy mely fájlokat és a kivonatot kell használni a fájlok megfelelő letöltésének ellenőrzéséhez.|

#### <a name="action"></a>Művelet

`Actions` Az alábbiakban az eszközfrissítési ügynök által az eszközfrissítési szolgáltatás utasításai szerint eszközfrissítési ügynök által tett műveleteket jelöli. A Device Update Agent jelentést készít a `State` fogadott feldolgozásról (lásd a `State` fenti `Action` szakaszt). Tekintse meg [az eszközfrissítési](understand-device-update.md#device-update-agent) szolgáltatás és az eszközfrissítési ügynök közötti kérelmek áttekintő munkafolyamatát.

|Name|Érték|Leírás|
|---------|-----|-----------|
|Letöltés|0|Közzétett tartalom letöltése vagy frissítés és minden egyéb szükséges tartalom|
|Telepítés|1|A tartalom telepítése vagy frissítése. Ez általában azt jelenti, hogy meg kell hívni a telepítőt a tartalomhoz vagy a frissítéshez.|
|Alkalmaz|2|Véglegesíteni kell a frissítést. Ha szükséges, a rendszer újraindítását jelzi.|
|Mégse|255|Állítsa le az aktuális művelet feldolgozását, és vissza a `Idle` következőre: . Arra is használható, hogy az állapotban van az ügynöknek vissza kell `Failed` mennie a következőre: `Idle` .|

## <a name="device-information-interface"></a>Eszközinformációs felület

Az Eszközinformációs felület az [IoT-architektúrában használt Plug and Play belül.](../iot-pnp/overview-iot-plug-and-play.md) Az eszközről a felhőbe típusú tulajdonságokat tartalmaz, amelyek az eszköz hardveréről és operációs rendszeréről nyújtanak információt. A IoT Hub eszközfrissítése a DeviceInformation.manufacturer és a DeviceInformation.model tulajdonságokat használja a telemetriához és a diagnosztikához. Az eszközinformációs felülettel kapcsolatos további információkért tekintse meg ezt a [példát.](https://devicemodels.azure.com/dtmi/azure/devicemanagement/deviceinformation-1.json)

A modellben a várt összetevőnév a **deviceInformation** az interfész megvalósításakor. [Tudnivalók az Azure IoT PnP összetevőiről](../iot-pnp/concepts-components.md)

|Név|Típus|Séma|Irány|Leírás|Példa|
|----|----|------|---------|-----------|-----------|
|gyártó|Tulajdonság|sztring|eszközről a felhőbe|Az eszköz gyártójának vállalatneve. Ez megegyezik az eredeti hardvergyártó (OEM) nevével.|Contoso|
|modell|Tulajdonság|sztring|eszközről a felhőbe|Eszközmodell neve vagy azonosítója.|IoT Edge eszköz|
|swVersion|Tulajdonság|sztring|eszközről a felhőbe|Az eszközön található szoftver verziója. A swVersion a belső vezérlőprogram verziója lehet.|4.15.0-122|
|osName (osName)|Tulajdonság|sztring|eszközről a felhőbe|Az eszközön található operációs rendszer neve.|Ubuntu Server 18.04|
|processorArchitecture|Tulajdonság|sztring|eszközről a felhőbe|A processzor architektúrája az eszközön.|ARM64|
|processorManufacturer|Tulajdonság|sztring|eszközről a felhőbe|Az eszközön található processzor gyártójának neve.|Microsoft|
|totalStorage|Tulajdonság|sztring|eszközről a felhőbe|Az eszközön elérhető összes tárterület kilobájtban.|2048|
|totalMemory|Tulajdonság|sztring|eszközről a felhőbe|Az eszközön elérhető összes memória kilobájtban.|256|

## <a name="model-id"></a>Modellazonosító 

A modellazonosító az, ahogy az intelligens eszközök meghirdetik a képességeiket az Azure IoT-alkalmazásokban az IoT Plug segítségével, és Play.To további információt arról, hogyan építhet intelligens eszközöket, amelyek meghirdetik képességeiket az Azure IoT-alkalmazások számára, látogasson el az [IoT Plug and Play eszközfejlesztői útmutatójára.](../iot-pnp/concepts-developer-guide-device.md)

A IoT Hub eszközfrissítéséhez az IoT Plug and Play intelligens eszköznek be kell jelentenie egy **"dtmi:AzureDeviceUpdate;1"** értékű modellazonosítót az eszközkapcsolat részeként. [Megtudhatja, hogyan jelentse be a modellazonosítót.](../iot-pnp/concepts-developer-guide-device.md#model-id-announcement)