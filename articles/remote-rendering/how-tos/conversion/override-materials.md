---
title: Anyagok felülírása a modellátalakítás során
description: A munkafolyamatot átváltási időben mutatja be
author: florianborn71
ms.author: flborn
ms.date: 02/13/2020
ms.topic: how-to
ms.openlocfilehash: 11bd79a1bc88d2605a20744f5a6b6536d754c100
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91576642"
---
# <a name="override-materials-during-model-conversion"></a>Anyagok felülírása a modellátalakítás során

A forrás modell anyag-beállításai a megjelenítő által használt pbr- [anyagok](../../overview/features/pbr-materials.md) meghatározására szolgálnak.
Előfordulhat, hogy az [alapértelmezett konverzió](../../reference/material-mapping.md) nem adja meg a kívánt eredményeket, és módosítania kell a módosításokat.
Ha egy modellt az Azure Remote rendering szolgáltatásban való használatra konvertál, megadhat egy anyag-felülbírálási fájlt, amellyel testre szabhatja, hogy a rendszer milyen módon végezze el az anyagok konverzióját.
Ha egy nevű fájl található a bemeneti `<modelName>.MaterialOverrides.json` tárolóban a bemeneti modell mellett `<modelName>.<ext>` , akkor a rendszer az anyagként szolgáló felülbírálási fájlként fogja használni.

## <a name="the-override-file-used-during-conversion"></a>Az átalakítás során használt felülbírálási fájl

Egyszerű Példaként tegyük fel, hogy egy box-modell egyetlen, "default" nevű anyagot tartalmaz.
Emellett tegyük fel, hogy a albedó színét az ARR-ben való használathoz módosítani kell.
Ebben az esetben a `box.MaterialOverrides.json` következő módon hozhat létre egy fájlt:

```json
[
    {
        "name": "Default",
        "albedoColor": {
            "r": 0.33,
            "g": 0.33,
            "b": 0.33,
            "a": 1.0
        }
    }
]
```

A `box.MaterialOverrides.json` fájl a következő beviteli tárolóba kerül `box.fbx` , amely azt jelzi, hogy a konverziós szolgáltatás alkalmazza az új beállításokat.

### <a name="color-materials"></a>Színes anyagok

A [színanyag](../../overview/features/color-materials.md) modell egy állandóan árnyékolt felületet ír le, amely független a megvilágítástől.
A színanyagok a photogrammetry algoritmusok által készített eszközök esetében hasznosak, például:.
Az anyag-felülbírálási fájlokban egy anyag beállítható úgy, hogy a következőre van beállítva: `unlit` `true` .

```json
[
    {
        "name": "Photogrametry_mat1",
        "unlit" : true
    },
    {
        "name": "Photogrametry_mat2",
        "unlit" : true
    }
]
```

### <a name="ignore-specific-texture-maps"></a>Adott textúratérkép figyelmen kívül hagyása

Esetenként előfordulhat, hogy az átalakítási folyamat figyelmen kívül hagyja az adott textúra-térképeket. Ez akkor fordulhat elő, ha a modellt olyan eszköz hozta létre, amely speciális térképeket hoz létre, amelyeket a megjelenítő nem megfelelően értelmezett. Például egy "OpacityMap", amely nem az opacitást definiálja, vagy egy olyan modellt, amelyben a "NormalMap" a "BumpMap" néven van tárolva. (Az utóbbi esetben figyelmen kívül hagyja a "NormalMap" kifejezést, ami azt eredményezi, hogy a konverter "BumpMap"-t használ "NormalMap"-ként.)

Az elv egyszerű. Csak vegyen fel egy nevű tulajdonságot `ignoreTextureMaps` , és adja hozzá a figyelmen kívül hagyni kívánt textúrát:

```json
[
    {
        "name": "Default",
        "ignoreTextureMaps": ["OpacityMap", "NormalMap"]
    }
]
```

Az figyelmen kívül hagyható textúratérkép teljes listájáért tekintse meg az alábbi JSON-sémát.

### <a name="applying-the-same-overrides-to-multiple-materials"></a>Ugyanazon felülbírálások alkalmazása több anyagra

Alapértelmezés szerint az anyag-felülbírálási fájlban szereplő bejegyzés akkor érvényes, ha a neve pontosan egyezik az anyag nevével.
Mivel meglehetősen gyakori, hogy ugyanazt a felülbírálást több anyagra is alkalmazni kell, igény szerint megadhat egy reguláris kifejezést is a bejegyzés neveként.
A mező `nameMatching` alapértelmezett értékkel rendelkezik `exact` , de úgy is beállítható, hogy `regex` a bejegyzés minden megfelelő anyagra vonatkozzon.
A használt szintaxis ugyanaz, mint a JavaScript használatakor. Az alábbi példa egy felülbírálást mutat be, amely a "Material2", a "Material01" és a "Material999" nevű anyagokra vonatkozik.

```json
[
    {
        "name": "Material[0-9]+",
        "nameMatching": "regex",
        "albedoColor": {
            "r": 0.0,
            "g": 0.0,
            "b": 1.0,
            "a": 1.0
        }
    }
]
```

Egy anyag-felülbírálási fájlban legfeljebb egy bejegyzés egyetlen anyagra vonatkozik.
Ha pontos egyezés van (azaz `nameMatching` hiányzik vagy egyenlő `exact` ) az anyag nevéhez, akkor a rendszer a bejegyzést választja.
Ellenkező esetben a fájl első olyan regex-bejegyzése van kiválasztva, amely megfelel az anyag nevének.

### <a name="getting-information-about-which-entries-applied"></a>Információk beolvasása az alkalmazott bejegyzésekről

A kimeneti tárolóba írt [információs fájl](get-information.md#information-about-a-converted-model-the-info-file) a megadott felülbírálások számával és a felülbírált anyagok számával kapcsolatos információkat tartalmaz.

## <a name="json-schema"></a>JSON-séma

Az anyagokhoz tartozó fájlok teljes JSON-sémája itt van megadva. A és a esetében a (z) és a (z) és a (z) a (z) és a (z) a (z) és a (z) és a (z `unlit` `ignoreTextureMaps` ) [](../../overview/features/color-materials.md) [](../../overview/features/pbr-materials.md)

```json
{
    "definitions" :
    {
        "color":
        {
            "type" : "object",
            "description" : "Color as 4 components vector",
            "properties":
            {
                "r": {"type":"number"},
                "g": {"type":"number"},
                "b": {"type":"number"},
                "a": {"type":"number"}
            },
            "required": ["r", "g", "b"]
        },
        "alpha":
        {
            "type" : "object",
            "description" : "Alpha channel for color",
            "properties":
            {
                "a": {"type":"number"}
            },
            "required": ["a"]
        },
        "colorOrAlpha":
        {
            "anyOf": [
                {"$ref": "#/definitions/color"},
                {"$ref": "#/definitions/alpha"}
            ]
        },
        "listOfMaps":
        {
            "type": "array",
            "items": {
                "type": "string",
                "enum": ["AlbedoMap",
                            "EmissiveMap",
                            "NormalMap",
                            "OcclusionMap",
                            "RoughnessMap",
                            "MetalnessMap",
                            "ReflectivityMap",
                            "BumpMap",
                            "OpacityMap",
                            "DiffuseMap",
                            "SpecularMap",
                            "ShininessMap",
                            "MetallicRoughnessMap",
                            "SpecularGlossinessMap"]
            }
        }
    },
    "type" : "array",
    "description" : "List of materials to override",
    "items":
    {
        "type" : "object",
        "description" : "List of parameters to override",
        "properties":
        {
            "name": { "type" : "string"},
            "nameMatching" : { "type" : "string", "enum" : ["exact", "regex"] },
            "unlit": { "type" : "boolean" },
            "albedoColor": { "$ref": "#/definitions/colorOrAlpha" },
            "roughness": { "type": "number" },
            "metalness": { "type": "number" },
            "transparent": { "type" : "boolean" },
            "alphaClipEnabled": { "type" : "boolean" },
            "alphaClipThreshold": { "type": "number" },
            "useVertexColor": { "type" : "boolean" },
            "isDoubleSided": { "type" : "boolean" },
            "ignoreTextureMaps": { "$ref" : "#/definitions/listOfMaps" }
        },
        "required": ["name"],
        "additionalProperties" : false
    }
}
```

## <a name="next-steps"></a>Következő lépések

* [Színes anyagok](../../overview/features/color-materials.md)
* [PBR-anyagok](../../overview/features/pbr-materials.md)
