---
title: A modellátalakítás konfigurálása
description: Az összes modell-átalakítási paraméter leírása
author: florianborn71
ms.author: flborn
ms.date: 03/06/2020
ms.topic: how-to
ms.openlocfilehash: 1cb5312e164bac09930497c377f1590b6a77ca05
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "92205319"
---
# <a name="configure-the-model-conversion"></a>A modellátalakítás konfigurálása

Ez a fejezet a modell átalakításának lehetőségeit dokumentálja.

## <a name="settings-file"></a>Beállítási fájl

Ha egy nevű fájl található a bemeneti `<modelName>.ConversionSettings.json` tárolóban a bemeneti modell mellett `<modelName>.<ext>` , akkor a rendszer a modell átalakítási folyamatának további konfigurációját fogja használni.
Például a `box.ConversionSettings.json` konvertáláskor használható `box.gltf` .

A fájl tartalmának meg kell felelnie a következő JSON-sémának:

```json
{
    "$schema" : "http://json-schema.org/schema#",
    "description" : "ARR ConversionSettings Schema",
    "type" : "object",
    "properties" :
    {
        "scaling" : { "type" : "number", "exclusiveMinimum" : 0, "default" : 1.0 },
        "recenterToOrigin" : { "type" : "boolean", "default" : false },
        "opaqueMaterialDefaultSidedness" : { "type" : "string", "enum" : [ "SingleSided", "DoubleSided" ], "default" : "DoubleSided" },
        "gammaToLinearMaterial" : { "type" : "boolean", "default" : false },
        "gammaToLinearVertex" : { "type" : "boolean", "default" : false },
        "sceneGraphMode": { "type" : "string", "enum" : [ "none", "static", "dynamic" ], "default" : "dynamic" },
        "generateCollisionMesh" : { "type" : "boolean", "default" : true },
        "unlitMaterials" : { "type" : "boolean", "default" : false },
        "fbxAssumeMetallic" : { "type" : "boolean", "default" : true },
        "deduplicateMaterials" : { "type" : "boolean", "default" : true },
        "axis" : {
            "type" : "array",
            "items" : {
                "type" : "string",
                "enum" : ["default", "+x", "-x", "+y", "-y", "+z", "-z"]
            },
            "minItems": 3,
            "maxItems": 3
        },
        "metadataKeys": {
            "type": "array",
            "items": {
                "type": "string"
            }
        }
    },
    "additionalProperties" : false
}
```

Lehetséges például, `box.ConversionSettings.json` hogy a fájl:

```json
{
    "scaling" : 0.01,
    "recenterToOrigin" : true,
    "material-override" : "box_materials_override.json"
}
```

### <a name="geometry-parameters"></a>Geometriai paraméterek

* `scaling` – Ez a paraméter egységesen méretezi a modellt. A skálázás felhasználható egy modell növelésére vagy összekapcsolására, például egy kiépítési modell megjelenítésére egy tábla tetején.
A skálázás akkor is fontos, ha a modell a mérőműszertől eltérő egységben van definiálva, mivel a renderelési motor mérőszámokat vár.
Ha például egy modell centiméterben van definiálva, akkor a 0,01-es méret alkalmazása esetén a modellt a megfelelő méretben kell megjeleníteni.
Bizonyos forrásadatok formátuma (például. FBX) egy egység skálázási mutatót biztosít, amely esetben az átalakítás implicit módon méretezi a modellt a mérő egységekre. A forrás formátuma által biztosított implicit skálázás a skálázási paraméter tetején lesz alkalmazva.
A végső skálázási tényező a geometriai csúcspontokra és a Scene Graph-csomópontok helyi átalakítására lesz alkalmazva. A gyökérszintű entitás átalakításának skálázása változatlan marad.

* `recenterToOrigin` – Azt állítja be, hogy egy modellt át kell alakítani, hogy a határolókeret középpontba kerüljön a forráson.
Ha a forrás modell a forrástól távol esik, a lebegőpontos pontossággal kapcsolatos hibák okozhatnak megjelenítési összetevőket.
A modell középpontba való benyújtása segíthet ebben a helyzetben.

* `opaqueMaterialDefaultSidedness` – A renderelési motor azt feltételezi, hogy az átlátszatlan anyagok kétoldalasak.
Ha a feltételezés nem igaz egy adott modell esetében, akkor ezt a paramétert "SingleSided" értékre kell beállítani. További információ: [ :::no-loc text="single sided"::: rendering](../../overview/features/single-sided-rendering.md).

### <a name="material-de-duplication"></a>Anyag – ismétlődés

* `deduplicateMaterials` – Ezzel a paraméterrel engedélyezheti vagy letilthatja az olyan anyagok automatikus ismétlődését, amelyek ugyanazokat a tulajdonságokat és textúrákat használják. A Duplikálás az anyagi Felülbírálások feldolgozása után következik be. Alapértelmezés szerint engedélyezve van.

* Ha a modell többszöri duplikálása után is több mint 65 535 anyag van, akkor a szolgáltatás hasonló tulajdonságokkal rendelkező anyagokat próbál egyesíteni. Utolsóként a határértéket meghaladó anyagokat a rendszer piros hibával helyettesíti.

![A képen két, 68 921 színű háromszög látható.](media/mat-dedup.png?raw=true)

Két kocka 68 921 színes háromszög. Balra: a 68 921-es színanyagokkal való megkettőzés előtt. Jobb: az 64 000-es színanyagokkal való megkettőzés után. A korlát 65 535 anyag. (Lásd a [korlátozásokat](../../reference/limits.md).)

### <a name="color-space-parameters"></a>Színtér paraméterei

A renderelési motor a színértékeket a lineáris térben várja.
Ha a modell a gamma szóköz használatával van definiálva, akkor ezeket a beállításokat igaz értékre kell állítani.

* `gammaToLinearMaterial` – Az anyag színeinek konvertálása a gamma-területről a lineáris helyre
* `gammaToLinearVertex` – :::no-loc text="vertex"::: Színek konvertálása a gamma-területről a lineáris helyre

> [!NOTE]
> FBX-fájlok esetén ezek a beállítások `true` alapértelmezés szerint vannak beállítva. Az összes többi fájltípus esetében az alapértelmezett érték a következő: `false` .

### <a name="scene-parameters"></a>Jelenet paraméterei

* `sceneGraphMode` -Meghatározza, hogy a program hogyan konvertálja a színtér gráfját:
  * `dynamic` (alapértelmezett): a fájlban lévő összes objektum az API- [ban szerepel, és](../../concepts/entities.md) egymástól függetlenül alakítható át. A csomópont-hierarchia futásidőben azonos a forrásfájl struktúrájával.
  * `static`: Az összes objektum elérhető az API-ban, de nem alakítható át egymástól függetlenül.
  * `none`: A jelenet gráf egyetlen objektumba van összecsukva.

Az egyes üzemmódok különböző futásidejű teljesítménnyel rendelkeznek. A `dynamic` módban a teljesítmény a gráfban lévő [entitások](../../concepts/entities.md) számával lineárisan méretezhető, még akkor is, ha egyetlen rész sem kerül áthelyezésre. A `dynamic` módot csak akkor használja, ha külön kell áthelyeznie a részeket, például egy "Alábontás nézet" animációhoz.

A `static` mód exportálja a teljes jelenet gráfot, de az ebben a gráfban található részek állandó átalakítóval rendelkeznek a gyökérszintű részhez képest. Az objektum legfelső szintű csomópontja azonban továbbra is áthelyezhető, elforgatható vagy méretezhető, és nincs jelentős teljesítménybeli díj. Emellett a [térbeli lekérdezések](../../overview/features/spatial-queries.md) az egyes részeket adják vissza, és az egyes részeket az [állapot felülbírálásai](../../overview/features/override-hierarchical-state.md)segítségével lehet módosítani. Ebben a módban az objektum futásidejű terhelése elhanyagolható. Ideális olyan nagy méretű jeleneteknél, ahol továbbra is szükség van az objektumon belüli ellenőrzésre, de az objektum-átalakítás nem változik.

A `none` mód a legalacsonyabb futtatókörnyezettel rendelkezik, és valamivel jobb betöltési időt is igénybe venni. Az önálló objektumok vizsgálata és átalakítása ebben a módban nem lehetséges. A használati esetek például olyan photogrammetry modellek, amelyeken nem szerepelnek az értelmes Scene gráfok az első helyen.

> [!TIP]
> Számos alkalmazás több modellt is betölt. Az egyes modellekhez tartozó konverziós paramétereket a használatuk módjától függően érdemes optimalizálni. Ha például egy autó modelljét szeretné megjeleníteni a felhasználó számára, és részletesen megvizsgálja azt, akkor a módot kell konvertálnia `dynamic` . Ha azonban azt is szeretné, hogy az autó egy show Room-környezetben legyen elhelyezve, a modell a készlet és a beállítás között is konvertálható `sceneGraphMode` `static` `none` .

### <a name="physics-parameters"></a>Fizikai paraméterek

* `generateCollisionMesh` – Ha a modellen a [térbeli lekérdezésekhez](../../overview/features/spatial-queries.md) támogatásra van szüksége, ezt a beállítást engedélyezni kell. A legrosszabb esetben az ütközési háló létrehozása megduplázhatja az átalakítási időt. Az ütközési hálókkal rendelkező modellek a betöltéshez és a Scene Graph használatakor hosszabb időt is igénybe vehetik `dynamic` . Az optimális teljesítmény érdekében tiltsa le ezt a beállítást minden olyan modellen, amelyen nincs szükség térbeli lekérdezésekre.

### <a name="unlit-materials"></a>Kivilágított anyagok

* `unlitMaterials` – Alapértelmezés szerint a konverzió szívesebben hozza létre a [pbr-anyagokat](../../overview/features/pbr-materials.md). Ez a beállítás azt jelzi, hogy a konverter az összes anyagot [színanyagként](../../overview/features/color-materials.md) kezeli. Ha olyan adatokkal rendelkezik, amelyek már befoglalják a világítást, például a photogrammetry használatával létrehozott modelleket, ez a beállítás lehetővé teszi, hogy gyorsan érvényesítse az összes anyag helyes átalakítását anélkül, hogy az [egyes anyagokat felül kellene bírálni](override-materials.md) .

### <a name="converting-from-older-fbx-formats-with-a-phong-material-model"></a>Konvertálás régebbi FBX-formátumokból

* `fbxAssumeMetallic` – A FBX-formátum régebbi verziói az anyagokat egy, egy vagy több anyagot tartalmazó modell használatával határozzák meg. Az átalakítási folyamatnak azt a következtetést kell kimutatnia, hogy ezek az anyagok hogyan képezik le a megjelenítő [pbr-modelljét](../../overview/features/pbr-materials.md). Ez általában jól működik, de a kétértelműség akkor merülhet fel, ha egy anyag nem tartalmaz textúrákat, nagy fényvisszaverődési értékeket és nem szürke albedó-színt. Ebben a körülmények között a konverziónak választania kell a nagy teljesítményű értékek rangsorolása, a nagy mértékben tükröző fémes anyagok meghatározásával, ahol a albedó színe megszűnik, vagy rangsorolja a albedó színét, ami a fényes színes műanyaghoz hasonló. Alapértelmezés szerint az átalakítási folyamat azt feltételezi, hogy a nagyon fényvisszaverődési értékek fémes anyagokat jelentenek azokban az esetekben, ahol a kétértelműség vonatkozik. Ez a paraméter beállítható úgy, hogy `false` átváltson a másikra.

### <a name="coordinate-system-overriding"></a>Koordinátarendszer-felülbírálás

* `axis` – A koordináta rendszeregység – vektorok felülbírálása. Az alapértelmezett értékek: `["+x", "+y", "+z"]` . Elméletileg a FBX formátuma tartalmaz egy fejlécet, ahol ezek a vektorok definiálva vannak, és a konverzió ezt az információt használja a jelenet átalakításához. A glTF formátuma rögzített koordináta-rendszereket is meghatároz. A gyakorlatban bizonyos adategységek helytelen információval rendelkeznek a fejlécben, vagy egy másik koordináta-rendszeregyezménnyel lettek mentve. Ez a beállítás lehetővé teszi a koordináta-rendszerek felülbírálását a kompenzálása érdekében. Például: `"axis" : ["+x", "+z", "-y"]` kicseréli a Z-tengelyt és az y tengelyt, és megtartja a koordináta-rendszer kézhasználat az Y tengely irányának invertálása mellett.

### <a name="node-meta-data"></a>Csomópont-Meta adat

* `metadataKeys` – Lehetővé teszi, hogy megadhatja az átalakítás eredményében megőrizni kívánt csomópont-metaadat-tulajdonságok kulcsait. Megadhatja a pontos kulcsokat vagy a helyettesítő kulcsokat. A helyettesítő kulcsok "ABC *" formátumúak, és az "ABC" kezdetű kulcsnak felelnek meg. A támogatott metaadatok értékei a következők:,, `bool` `int` `float` és `string` .

    A GLTF-fájlok esetében ezek az adatok a [csomópontok extrák objektumában](https://github.com/KhronosGroup/glTF/tree/master/specification/2.0#nodeextras)származnak. A FBX-fájlok esetében ezek az adatok az `Properties70` adatokból származnak `Model nodes` . További részletekért tekintse meg a 3D Asset eszköz dokumentációját.

### <a name="no-loc-textvertex-format"></a>:::no-loc text="Vertex"::: formátumban

Lehetőség van a :::no-loc text="vertex"::: háló formátumának módosítására a memória megtakarításának pontossága érdekében. Az alacsonyabb memória-lábnyom lehetővé teszi nagyobb modellek betöltését vagy jobb teljesítmény elérését. Az adataitól függően azonban a helytelen formátum jelentős hatással lehet a renderelés minőségére.

> [!CAUTION]
> Ha a :::no-loc text="vertex"::: modell nem fér bele a memóriába, vagy ha a lehető legjobb teljesítményre van optimalizálva, a formátum módosításának utolsónek kell lennie. A változtatások könnyen bevezethetik a renderelési összetevőket, amelyek egyaránt egyértelműek és finomak. Ha nem tudja, mit kell keresnie, ne módosítsa az alapértelmezett értéket.

Ezek a módosítások lehetségesek:

* Az adott adatfolyamok explicit módon szerepelhetnek vagy zárhatók ki.
* Az adatfolyamok pontossága csökkenthető a memória helyigényének csökkentése érdekében.

A `vertex` fájl következő szakasza `.json` nem kötelező. Minden explicit módon megadott résznél az átalakítási szolgáltatás visszaáll az alapértelmezett értékre.

```json
{
    ...
    "vertex" : {
        "position"  : "32_32_32_FLOAT",
        "color0"    : "NONE",
        "color1"    : "NONE",
        "normal"    : "NONE",
        "tangent"   : "NONE",
        "binormal"  : "NONE",
        "texcoord0" : "32_32_FLOAT",
        "texcoord1" : "NONE"
    },
    ...
```

Ha az összetevőt arra kényszeríti `NONE` , hogy a kimeneti rácsvonal nem rendelkezik a megfelelő streamtel.

#### <a name="component-formats-per-no-loc-textvertex-stream"></a>Összetevő-formátumok :::no-loc text="vertex"::: adatfolyamként

Ezek a formátumok a megfelelő összetevők esetében engedélyezettek:

| :::no-loc text="Vertex"::: -összetevő | Támogatott formátumok (Bold = default) |
|:-----------------|:------------------|
|pozíció| **32_32_32_FLOAT**, 16_16_16_16_FLOAT |
|color0| **8_8_8_8_UNSIGNED_NORMALIZED**, nincs |
|szín1| 8_8_8_8_UNSIGNED_NORMALIZED, **nincs**|
|normál| **8_8_8_8_SIGNED_NORMALIZED**, 16_16_16_16_FLOAT, nincs |
|tangensét| **8_8_8_8_SIGNED_NORMALIZED**, 16_16_16_16_FLOAT, nincs |
|binormal| **8_8_8_8_SIGNED_NORMALIZED**, 16_16_16_16_FLOAT, nincs |
|texcoord0| **32_32_FLOAT**, 16_16_FLOAT, nincs |
|texcoord1| **32_32_FLOAT**, 16_16_FLOAT, nincs |

#### <a name="supported-component-formats"></a>Támogatott összetevők formátumai

A formátumok memória-lábnyomai a következők:

| Formátum | Leírás | Bájt/ :::no-loc text="vertex"::: |
|:-------|:------------|:---------------|
|32_32_FLOAT|két összetevő teljes lebegőpontos pontossága|8
|16_16_FLOAT|két összetevős fél lebegőpontos pontossága|4
|32_32_32_FLOAT|három összetevőből teljes lebegőpontos pontosság|12
|16_16_16_16_FLOAT|négy összetevős fél lebegőpontos pontossága|8
|8_8_8_8_UNSIGNED_NORMALIZED|négy összetevős bájt, normalizálva a `[0; 1]` tartományhoz|4
|8_8_8_8_SIGNED_NORMALIZED|négy összetevős bájt, normalizálva a `[-1; 1]` tartományhoz|4

#### <a name="best-practices-for-component-format-changes"></a>Ajánlott eljárások az összetevő formátumának változásaihoz

* `position`: Ritkán fordul elő, hogy a kisebb pontosság elegendő. a **16_16_16_16_FLOAT** a nagy méretű modellek esetében is bevezeti a észlelhető kvantálási összetevőket.
* `normal`, `tangent` , `binormal` : Általában ezek az értékek együtt változnak. Ha a normál kvantálást eredményező, észrevehetően megvilágított összetevők nem indokolják meg a pontosság növelését. Bizonyos esetekben azonban ezek az összetevők a **none** értékre állíthatók:
  * `normal`, `tangent` és `binormal` csak akkor szükséges, ha a modellben legalább egy anyagot meg kell világítani. Az ARR-ben ez a helyzet akkor, ha egy [pbr-anyagot](../../overview/features/pbr-materials.md) bármikor használ a modellben.
  * `tangent` és `binormal` csak akkor szükséges, ha a megvilágított anyagok bármelyike normál Térkép textúrát használ.
* `texcoord0``texcoord1`: A textúra koordinátái használhatnak kisebb pontosságot (**16_16_FLOAT**), ha az értékek a tartományon maradnak, `[0; 1]` és ha a kezelt textúrák maximális mérete 2048 x 2048 képpont. Ha túllépi a korlátokat, a textúra-leképezés minősége is csökkenni fog.

#### <a name="example"></a>Példa

Tegyük fel, hogy van egy photogrammetry-modellje, amely a textúrákba besütött világítással rendelkezik. A modell megjelenítéséhez szükséges összes érték a :::no-loc text="vertex"::: pozíciók és a textúra koordinátái.

Alapértelmezés szerint a konvertálónak feltételezni kell, hogy egy modellben a pbr-anyagokat egy időben szeretné használni, így Ön is létrehozhatja, és felhasználhatja `normal` `tangent` `binormal` azokat. Következésképpen a vertex memóriahasználat `position` (12 bájt) + `texcoord0` (8 bájt) + (4 bájt) + (4 bájt) + ( `normal` 4 bájt `tangent` `binormal` ) = 32 bájt. Az ilyen típusú nagyobb modellek egyszerűen több millió olyan :::no-loc text="vertices"::: modellt eredményeznek, amely akár több gigabájt memóriát is igénybe vehet. Ilyen nagy mennyiségű adat befolyásolja a teljesítményt, és előfordulhat, hogy elfogyott a memória.

Tudván, hogy soha nem szükséges a modell dinamikus megvilágítása, és annak ismerete, hogy az összes textúra koordinátái a `[0; 1]` tartományon belül vannak megadva `normal` `tangent` `binormal` `NONE` `texcoord0` `16_16_FLOAT` :::no-loc text="vertex"::: A (többek között) Mesh-adat kivágása lehetővé teszi a nagyobb modellek betöltését és a teljesítmény növelését.

## <a name="memory-optimizations"></a>Memória optimalizálása

A betöltött tartalom memóriájának felhasználása szűk keresztmetszetet jelenthet a renderelési rendszeren. Ha a memória hasznos tartalma túl nagy lesz, akkor veszélyeztetheti a renderelési teljesítményt, vagy ha a modell nem töltődik be együtt. Ez a bekezdés néhány fontos stratégiát tárgyal a memória helyigényének csökkentése érdekében.

### <a name="instancing"></a>Egypéldányos

A egypéldányos olyan fogalom, amelyben a rácsvonalak a különböző térbeli átalakításokkal rendelkező részek számára újra használatban vannak, nem pedig a saját egyedi geometriára hivatkozó összes részre. A egypéldányos jelentős hatással van a memória lábnyomára.
Példa egypéldányos az építészeti modellben lévő motor-modellekben vagy székeknél használt használati esetek.

> [!NOTE]
> A egypéldányos jelentősen növelheti a memória-használatot (és így a betöltési időt), azonban a renderelési teljesítmény oldalának továbbfejlesztései nem jelentősek.

A konverziós szolgáltatás tiszteletben tartja a egypéldányos, ha a részek ennek megfelelően vannak megjelölve a forrásfájlban. A konverzió azonban nem végez további mély elemzést a rácsvonalak adatairól az újrafelhasználható részek azonosítására. Így a tartalom-létrehozási eszköz és az exportálási folyamat a megfelelő egypéldányos-telepítés döntő feltételei.

Egy egyszerű módszer annak tesztelésére, hogy a egypéldányos adatai megmaradnak-e az átalakítás során, hogy megtekintse a [kimeneti statisztikát](get-information.md#example-info-file), konkrétan a `numMeshPartsInstanced` tagot. Ha a értéke `numMeshPartsInstanced` nagyobb nullánál, akkor azt jelzi, hogy a rácsvonalak több példány között vannak elosztva.

#### <a name="example-instancing-setup-in-3ds-max"></a>Példa: egypéldányos-telepítő a 3ds Max-ban

Az [Autodesk 3ds Max](https://www.autodesk.de/products/3ds-max) különböző objektum-klónozási módokat (ún **`Copy`** .) tartalmaz, **`Instance`** **`Reference`** amelyek eltérően működnek az exportált fájlban lévő egypéldányos kapcsolatban `.fbx` .

![Klónozás a 3ds Max-ban](./media/3dsmax-clone-object.png)

* **`Copy`** : Ebben a módban a rácsvonal klónozása megtörténik, ezért a rendszer nem használ egypéldányos ( `numMeshPartsInstanced` = 0).
* **`Instance`** : A két objektum ugyanazt a hálót használja, ezért a rendszer egypéldányos használ ( `numMeshPartsInstanced` = 1).
* **`Reference`** : A geometriák esetében különálló módosítók alkalmazhatók, így az exportőr konzervatív megközelítést választ, és nem használja a egypéldányos ( `numMeshPartsInstanced` = 0).


### <a name="depth-based-composition-mode"></a>Mélység-alapú kompozíció mód

Ha a memória aggodalomra ad okot, konfigurálja a renderelést a [mélység-alapú kompozíciós móddal](../../concepts/rendering-modes.md#depthbasedcomposition-mode). Ebben a módban a GPU hasznos adatai több GPU-ban oszlanak el.

### <a name="decrease-vertex-size"></a>Csúcspont méretének csökkentése

Az [összetevők formátumának változásai](configure-model-conversion.md#best-practices-for-component-format-changes) című szakaszban ismertetett eljárás szerint a csúcspont formátumának módosítása csökkentheti a memória helyigényét. Ez a beállítás azonban a legvégső megoldás.

### <a name="texture-sizes"></a>Textúra mérete

A forgatókönyv típusától függően a textúra-adatmennyiség meghaladhatja a rácsvonalak esetében felhasznált memóriát. A photogrammetry modellek jelöltek.
Az átalakítás konfigurációja nem teszi lehetővé a textúrák automatikus méretezését. Ha szükséges, a textúra skálázást ügyféloldali előzetes feldolgozási lépésként kell elvégezni. Az átalakítási lépés a megfelelő [textúra tömörítési formátumának](/windows/win32/direct3d11/texture-block-compression-in-direct3d-11)kiválasztása:

* `BC1` átlátszatlan színes textúrák esetén
* `BC7` a forrás színtextúrák alfa-csatornával

Mivel a formátum `BC7` kétszer is megtelt a memória-lábnyomhoz képest `BC1` , fontos, hogy a bemeneti textúrák ne nyújtsanak szükségtelen alfa-csatornát.

## <a name="typical-use-cases"></a>Jellemző használati esetek

Egy adott használati eset megfelelő importálási beállításainak megkeresése unalmas folyamat lehet. Másfelől előfordulhat, hogy a konverziós beállítások jelentős hatással lehetnek a futtatókörnyezet teljesítményére.

Vannak bizonyos használati esetek, amelyek adott optimalizálásokra érvényesek. Néhány példát alább találhat.

### <a name="use-case-architectural-visualization--large-outdoor-maps"></a>Használati eset: építészeti vizualizáció/nagyméretű kültéri térképek

* Ezek a típusú jelenetek általában statikusak, ami azt jelenti, hogy nincs szükségük mozgó részekre. Ennek megfelelően a állítható be `sceneGraphMode` `static` vagy akár akár is `none` , ami javítja a futtatókörnyezet teljesítményét. A `static` módban a jelenet legfelső szintű csomópontja továbbra is áthelyezhető, elforgatható és méretezhető, például dinamikusan válthat a 1:1 skála (első személy nézet) és a tábla felső nézete között.

* Ha a körüli részeket át kell helyeznie, ez általában azt is jelenti, hogy támogatásra van szüksége a raycasts vagy más [térbeli lekérdezésekhez](../../overview/features/spatial-queries.md), így az első helyen kiválaszthatja ezeket a részeket. Ha azonban nem kívánja áthelyezni a körülötte lévőket, valószínűleg nem kell a térbeli lekérdezésekben részt vennie, és így ki is kapcsolhatja a `generateCollisionMesh` jelzőt. Ez a kapcsoló jelentős hatással van az átalakítási időpontokra, a betöltési időpontokra, valamint az egyes kereteken belüli frissítési költségekre is.

* Ha az alkalmazás nem használ [kivágási síkokat](../../overview/features/cut-planes.md), a `opaqueMaterialDefaultSidedness` jelzőt ki kell kapcsolni. A teljesítmény-nyereség általában 20%-30%. A kivágott síkok továbbra is használhatók, de nem lesznek háttérbeli arcok, amikor az objektumok belső részeire néznek, és ez a számláló intuitív. További információ: [ :::no-loc text="single sided"::: rendering](../../overview/features/single-sided-rendering.md).

### <a name="use-case-photogrammetry-models"></a>Használati eset: photogrammetry-modellek

A photogrammetry-modellek renderelése esetén általában nincs szükség a jelenet gráfra, így beállíthatja a `sceneGraphMode` -t `none` . Mivel ezek a modellek ritkán tartalmaznak egy összetett színtér-diagramot, amely a következővel kezdődik, a lehetőség hatásának jelentéktelennek kell lennie, mégis.

Mivel a megvilágítás már besütött a textúrákba, nem szükséges dinamikus megvilágítás. Ezért:

* Állítsa be a `unlitMaterials` jelölőt úgy, hogy az `true` összes anyagot kivilágítatlan [színanyagokba](../../overview/features/color-materials.md)kapcsolja.
* Szüntesse meg a nem szükséges adatok csúcspont-formátumból való eltávolítását. Lásd a fenti [példát](#example) .

### <a name="use-case-visualization-of-compact-machines-etc"></a>Használati eset: kompakt gépek megjelenítése stb.

Ezekben a használati esetekben a modellek gyakran nagyon nagy részletességgel rendelkeznek egy kis köteten belül. A megjelenítő nagy mértékben van optimalizálva az ilyen esetek kezelésére. Az előző használati esetben említett optimalizálások többsége azonban itt nem érvényes:

* Az egyes részeknek kijelölhető és mozgathatónak kell lenniük, így a `sceneGraphMode` kötelezően meg kell maradnia `dynamic` .
* A Ray-öntvények általában az alkalmazás szerves részét képezik, ezért az ütközési hálókat kell létrehozni.
* A kivágott síkok jobban kitűnnek a `opaqueMaterialDefaultSidedness` jelzővel.

## <a name="deprecated-features"></a>Elavult funkciók:

A nem modellre jellemző fájlnevet használó beállítások `conversionSettings.json` továbbra is támogatottak, de elavultak.
Ehelyett használja a modell-specifikus fájlnevet `<modelName>.ConversionSettings.json` .

A `material-override` konverziós beállítások fájlban lévő [anyag-felülbírálási fájl](override-materials.md) azonosítására szolgáló beállítás használata továbbra is támogatott, de elavult. Ehelyett használja a modell-specifikus fájlnevet `<modelName>.MaterialOverrides.json` .

## <a name="next-steps"></a>Következő lépések

* [Modell átalakítása](model-conversion.md)
* [Színes anyagok](../../overview/features/color-materials.md)
* [PBR-anyagok](../../overview/features/pbr-materials.md)
* [Anyagok felülírása a modellátalakítás során](override-materials.md)