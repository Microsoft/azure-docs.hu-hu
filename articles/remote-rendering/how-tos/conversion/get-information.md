---
title: Átalakításokkal kapcsolatos információk lekérése
description: Átalakításokkal kapcsolatos információk lekérése
author: malcolmtyrrell
ms.author: matyrr
ms.date: 03/05/2020
ms.topic: how-to
ms.openlocfilehash: 89ec0ad40822785457e988cf9e0f9bd6d00ed81f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91576625"
---
# <a name="get-information-about-conversions"></a>Átalakításokkal kapcsolatos információk lekérése

## <a name="information-about-a-conversion-the-result-file"></a>A konverzióval kapcsolatos információk: az eredmény fájl

Ha az átalakítási szolgáltatás átalakítja az eszközt, az egy "eredmény" fájlba írja az esetleges problémák összegzését. Ha például egy fájl `buggy.gltf` konvertálása történik, a kimeneti tároló egy nevű fájlt fog tartalmazni `buggy.result.json` .

Az eredmény fájl felsorolja az átalakítás során bekövetkezett hibákat és figyelmeztetéseket, valamint az eredmény összegzését, amely a vagy az egyike `succeeded` `failed` `succeeded with warnings` .
Az eredményként szolgáló fájl az objektumok JSON-tömbje, amelyek mindegyike egy,,, és típusú karakterlánc-tulajdonsággal rendelkezik `warning` `error` `internal warning` `internal error` `result` . Legfeljebb egy hiba ( `error` vagy `internal error` ) lesz, és mindig lesz egy `result` .

## <a name="example-result-file"></a>Példa az *eredmény* fájlra

Az alábbi példa egy olyan konverziót ismertet, amely sikeresen generált egy arrAsset. Mivel azonban hiányzik a textúra, az eredményül kapott arrAsset nem lehet a szándéka.

```JSON
[
  {"warning":"4004","title":"Missing texture","details":{"texture":"buggy_baseColor.png","material":"buggy_col"}},
  {"result":"succeeded with warnings"}
]
```

## <a name="information-about-a-converted-model-the-info-file"></a>Az átalakított modellel kapcsolatos információk: az információs fájl

Az átalakítási szolgáltatás által létrehozott arrAsset-fájl kizárólag a renderelési szolgáltatás általi felhasználásra szolgál. Előfordulhat azonban, hogy a modellre vonatkozó információkat a renderelési munkamenet elindítása nélkül szeretné elérni. A munkafolyamat támogatásához a konverziós szolgáltatás egy JSON-fájlt helyez el a kimeneti tároló arrAsset fájlja mellett. Ha például egy fájl `buggy.gltf` konvertálása történik, a kimeneti tároló tartalmazni fog egy nevű fájlt `buggy.info.json` a konvertált eszköz mellett `buggy.arrAsset` . A forrás modellről, a konvertált modellről, valamint magáról a konverzióról tartalmaz információkat.

## <a name="example-info-file"></a>Példa *információs* fájlra

Íme egy példa a fájl konvertálásával létrehozott *információs* fájlra `buggy.gltf` :

```JSON
{
    "files": {
        "input": "Buggy.gltf"
    },
    "conversionSettings": {
        "recenterToOrigin": true
    },
    "inputInfo": {
        "sourceAssetExtension": ".gltf",
        "sourceAssetFormat": "glTF2 Importer",
        "sourceAssetFormatVersion": "2.0",
        "sourceAssetGenerator": "COLLADA2GLTF"
    },
    "inputStatistics": {
        "numMeshes": 148,
        "numFaces": 308306,
        "numVertices": 245673,
        "numMaterial": 149,
        "numFacesSmallestMesh": 2,
        "numFacesBiggestMesh": 8764,
        "numNodes": 206,
        "numMeshUsagesInScene": 236,
        "maxNodeDepth": 3
    },
    "materialOverrides": {
        "numOverrides": 4,
        "numOverriddenMaterials": 4
    },
    "outputInfo": {
        "conversionToolVersion": "3b28d840de9916f9d628342f474d38c3ab949590",
        "conversionHash": "CCDB1F7A4C09F565"
    },
    "outputStatistics": {
        "numMeshPartsCreated": 236,
        "numMeshPartsInstanced": 88,
        "recenteringOffset": [
            -24.1,
            -50.9,
            -16.5974
        ],
        "boundingBox": {
            "min": [
                -43.52,
                -61.775,
                -79.6416
            ],
            "max": [
                43.52,
                61.775,
                79.6416
            ]
        }
    }
}
```

## <a name="information-in-the-info-file"></a>Információ az információs fájlban

### <a name="the-files-section"></a>A *Files (fájlok* ) szakasz

Ez a szakasz tartalmazza a megadott fájlneveket.

* `input`: A forrásfájl neve.
* `output`: A kimeneti fájl neve, ha a felhasználó nem alapértelmezett nevet adott meg.

### <a name="the-conversionsettings-section"></a>A *conversionSettings* szakasz

Ez a szakasz a modell átalakításakor megadott [ConversionSettings](configure-model-conversion.md#settings-file) másolatát tartalmazza.

### <a name="the-inputinfo-section"></a>A *inputInfo* szakasz

Ez a szakasz a forrásfájl formátumával kapcsolatos adatokat rögzíti.

* `sourceAssetExtension`: A forrásfájl fájlkiterjesztés.
* `sourceAssetFormat`: A forrásfájl formátumának leírása.
* `sourceAssetFormatVersion`: A forrásfájl formátumának verziója.
* `sourceAssetGenerator`: A forrásfájlt létrehozó eszköz neve, ha van ilyen.

### <a name="the-materialoverrides-section"></a>A *materialOverrides* szakasz

Ez a szakasz információkkal szolgál arról, hogy az [anyag](override-materials.md) felülbírálása mikor lett megadva a konverziós szolgáltatásnak.
A következő adatokat tartalmazza:
* `numOverrides`: Az anyag-felülbírálási fájlból beolvasott felülbírálási bejegyzések száma.
* `numOverriddenMaterials`: A felülbírált anyagok száma.

### <a name="the-inputstatistics-section"></a>A *inputStatistics* szakasz

Ez a szakasz a forrás jelenetről tartalmaz információkat. Az ebben a szakaszban szereplő értékek és a forrás modellt létrehozó eszköz egyenértékű értékei között gyakran előfordulnak eltérések. Ilyen eltérések várhatók, mert a modell az exportálási és átalakítási lépések során módosul.

* `numMeshes`: A rácsvonalak száma, amelyben az egyes részek hivatkozhatnak egyetlen anyagra.
* `numFaces`: A _háromszögek_ teljes száma a teljes modellben. Vegye figyelembe, hogy a háló a konverzió során háromszögárfolyam-számítást végez. Ez a szám a [szabványos renderelési kiszolgáló méretében](../../reference/vm-sizes.md#how-the-renderer-evaluates-the-number-of-polygons)járul hozzá a sokszög korlátozásához.
* `numVertices`: A teljes modellben lévő csúcspontok teljes száma.
* `numMaterial`: Az anyagok teljes száma a teljes modellben.
* `numFacesSmallestMesh`: A modell legkisebb rácsvonalában található háromszögek száma.
* `numFacesBiggestMesh`: A modell legnagyobb hálójában lévő háromszögek száma.
* `numNodes`: A modell Scene gráf csomópontjainak száma.
* `numMeshUsagesInScene`: A csomópontok száma, amikor a csomópontok hivatkozási rácsvonalak. Több csomópont is hivatkozhat ugyanarra a rácsvonalra.
* `maxNodeDepth`: A jelenet gráfon belüli csomópontok maximális mélysége.

### <a name="the-outputinfo-section"></a>A *outputInfo* szakasz

Ez a szakasz a generált kimenet általános információit rögzíti.

* `conversionToolVersion`: A modell átalakító verziója.
* `conversionHash`: A arrAsset belül található, a rendereléshez hozzájáruló adatokat tartalmazó kivonat. Azt is megtudhatja, hogy az átalakítási szolgáltatás eltérő eredményt adott-e meg, ha ugyanazon a fájlon fut újra.

### <a name="the-outputstatistics-section"></a>A *outputStatistics* szakasz

Ez a szakasz a konvertált eszközről kiszámított adatokat rögzíti.

* `numMeshPartsCreated`: A arrAsset lévő rácsvonalak száma. Ez a `numMeshes` szakasztól eltérő lehet `inputStatistics` , mert a egypéldányos az átalakítási folyamat érinti.
* `numMeshPartsInstanced`: A arrAsset újrafelhasznált rácsvonalak száma.
* `recenteringOffset`: Ha a `recenterToOrigin` [ConversionSettings](configure-model-conversion.md) engedélyezve van, ez az érték a fordítás, amely áthelyezi a konvertált modellt az eredeti helyére.
* `boundingBox`: A modell határai.

## <a name="deprecated-features"></a>Elavult funkciók:

A konverziós szolgáltatás a fájlokat `stdout.txt` és `stderr.txt` a kimeneti tárolót írja, és ezek a figyelmeztetések és hibák egyetlen forrása voltak.
Ezek a fájlok már elavultak. Ehelyett használja az [eredményhalmaz fájljait](#information-about-a-conversion-the-result-file) erre a célra.

## <a name="next-steps"></a>Következő lépések

* [Modell átalakítása](model-conversion.md)
* [A modellátalakítás konfigurálása](configure-model-conversion.md)
