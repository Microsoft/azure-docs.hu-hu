---
title: Az eszköz frissítése IoT Hub frissítési jegyzékfájlhoz | Microsoft Docs
description: Ismerje meg, hogyan történik a tulajdonságok küldése az eszköz frissítési szolgáltatásának az eszközre frissítés közben
author: andrewbrownmsft
ms.author: andbrown
ms.date: 2/17/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: bbd39f7752a1a482350a7231a0bb5a3710591b1d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102030681"
---
# <a name="device-update-for-iot-hub-update-manifest"></a>IoT Hub frissítési jegyzékfájl eszközének frissítése

## <a name="overview"></a>Áttekintés

Az IoT Hub eszköz frissítése _frissítési jegyzékfájlt_ használ a műveletek és a műveleteket támogató metaadatok a [AzureDeviceUpdateCore. OrchestratorMetadata: 4](./device-update-plug-and-play.md)Interface tulajdonságain keresztül történő kommunikációhoz.
Ez a dokumentum ismerteti, hogy az `updateManifest` `AzureDeviceUpdateCore.OrchestratorMetadata:4` eszköz frissítési ügynöke hogyan használja a tulajdonságot az illesztőfelületen. Az `AzureDeviceUpdateCore.OrchestratorMetadata:4` illesztőfelület tulajdonságait a rendszer az eszköz frissítése IoT hub szolgáltatásról az eszköz frissítési ügynökének továbbítja. Az `updateManifest` egy szerializált JSON-objektum, amelyet az eszköz frissítési ügynöke elemez.

### <a name="an-example-update-manifest"></a>Példa frissítési jegyzékfájlra

```JSON
{
    "manifestVersion": "1",
    "updateId": {
        "provider": "DuTest",
        "name": "DuTestUser",
        "version": "2020.611.534.16"
    },
    "updateType": "microsoft/swupdate:1",
    "installedCriteria": "1.0",
    "files": {
        "00000": {
            "fileName": "image.swu",
            "sizeInBytes": 256000,
            "hashes": {
                "sha256": "IhIIxBJpLfazQOk/PVi6SzR7BM0jf4HDqw+6gdZ3vp8="
            }
        }
    },
    "createdDateTime": "2020-06-12T00:38:13.9350278"
}
```

A frissítési jegyzékfájl célja, hogy leírja a frissítés tartalmát, azaz az identitását, típusát, telepítési feltételeit és a fájl frissítése metaadatait. Emellett a frissítési jegyzékfájl kriptográfiai aláírása lehetővé teszi, hogy az eszköz frissítési ügynöke igazolja annak hitelességét. A tartalom biztonságos importálásához tekintse meg az [eszköz frissítése biztonsági](./device-update-security.md) dokumentációt, amely arról nyújt tájékoztatást, hogyan használható a frissítési jegyzékfájl.

## <a name="import-manifest-vs-update-manifest"></a>Jegyzékfájl importálása vs frissítési jegyzékfájl

Fontos megérteni, hogy milyen különbségek vannak az importálási jegyzékfájl és a frissítési jegyzékfájl fogalmai között a IoT Hub eszköz frissítése során. 
* Az [importálási jegyzékfájlt](./import-concepts.md) létrehozta, aki létrehozza a megfelelő frissítést. Ismerteti a frissítés tartalmát, amelyet a rendszer az eszköz frissítéséhez importál IoT Hub. 
* A frissítési jegyzékfájlt automatikusan a IoT Hub szolgáltatáshoz tartozó frissítés hozza létre az importálási jegyzékfájlban definiált egyes tulajdonságok használatával. A frissítési folyamat során a rendszer az eszköz frissítési ügynökével kommunikál a kapcsolódó információk használatával. 

Mindegyik jegyzékfájlhoz saját séma és séma verziója tartozik.

## <a name="update-manifest-properties"></a>Jegyzékfájl tulajdonságainak frissítése

A frissítési jegyzék tulajdonságainak magas szintű definíciói az [itt](./device-update-plug-and-play.md)található interfész-definíciókban találhatók. A mélyebb kontextus érdekében tekintsük át a tulajdonságokat, és hogyan használják őket a rendszeren.

### <a name="updateid"></a>updateId

A (,) és a () tartalmazza az `provider` `name` `version` IoT hub frissítési identitás pontos eszközének frissítését, amely a kompatibilis eszközök frissítéshez való meghatározásához használatos.

### <a name="updatetype"></a>Frissítés típusa

Az adott típusú frissítési kezelő által kezelt frissítés típusát jelöli. A `microsoft/swupdate:1` rendszerkép-alapú frissítés és a `microsoft/apt:1` csomag alapú frissítés formátumát követi (lásd az `Update Handler Types` alábbi szakaszt).

### <a name="installedcriteria"></a>installedCriteria

Egy karakterlánc, amely az eszköz frissítési ügynökének frissítési kezelője által igényelt adatokat tartalmazza annak megállapításához, hogy a frissítés telepítve van-e az eszközön. A `Update Handler Types` szakasz a formátumát dokumentálja a `installedCriteria` IoT hub eszköz frissítése által támogatott minden frissítési típushoz.

### <a name="files"></a>fájlok

Megadja az eszköz frissítési ügynökének, hogy mely fájlok tölthetők le, és hogy a rendszer milyen kivonatot használ a fájlok helyes letöltésének ellenőrzéséhez.
A `files` tulajdonság tartalmának alaposabb áttekintése:

```json
"files":{
        <FILE_ID_STRING>:{
            "fileName":<STRING>,
            "sizeInBytes":<INTEGER>,
            "hashes":{
                <HASH-TYPE>:<HASH-STRING>
            }
        }
    }
```

A on kívül `updateManifest` a `fileUrls` JSON-objektum tömbje.

```json
"fileUrls":{
      <FILE_ID_STRING>: <URL-in-String-Format>
 }
```

Mind a, mind a, mind `FILE_ID_STRING` `fileUrls` a (például a `files` "0000" szerepel a `files` "0000" URL-címen belül `fileUrls` ).

### <a name="manifestversion"></a>manifestVersion

A séma verzióját jelölő karakterlánc.

## <a name="update-handler-types"></a>Kezelői típusok frissítése

|Frissítési módszer|Kezelő típusának frissítése|Frissítés típusa|Telepített feltételek|A közzétételre várt fájlok|
|-------------|-------------------|----------|-----------------|--------------|
|Rendszerkép-alapú|SWUpdate|"Microsoft/swupdate: version"|A hivatkozás képe a/etc/adu-Version-fájlban menti a verzióját.  |a SWUpdate-rendszerképet tartalmazó. SWU fájl|
|Csomag alapú|APT|"Microsoft/apt: version"|`<name>` + "-" + `<version>` (definiált tulajdonságok az apt manifest fájlban|`<APT Update Manifest>`. JSON, amely az APT konfigurációt és a csomagok listáját tartalmazza|

