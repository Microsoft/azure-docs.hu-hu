---
title: Anyagleképzés a modellformátumokhoz
description: Leírja a modell forrás formátumának a PBR-anyagokra való alapértelmezett átalakítását
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: reference
ms.openlocfilehash: 8313243bf680ea1a1d63f2719b647149a04935a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "96024044"
---
# <a name="material-mapping-for-model-formats"></a>Anyagleképzés a modellformátumokhoz

Ha a forrásként szolgáló eszközt [modellként alakítja át](../how-tos/conversion/model-conversion.md), a konverter [anyagokat](../concepts/materials.md) hoz létre az egyes [hálók](../concepts/meshes.md)számára. Az anyagok létrehozási módja [felülbírálható](../how-tos/conversion/override-materials.md). Alapértelmezés szerint azonban a konverzió a [pbr-anyagokat](../overview/features/pbr-materials.md)fogja létrehozni. Mivel minden forrásfájl-formátum (például a FBX) saját konvenciókat használ az anyagok definiálásához, ezeket az egyezményeket le kell képezni az Azure távoli renderelés PBR-anyagokra vonatkozó paramétereinek. 

Ez a cikk felsorolja az anyagok forrásként való átalakításához használt pontos leképezéseket a futásidejű anyagokra.

## <a name="gltf"></a>glTF

A glTF 2,0 specifikáció szinte mindent támogat az Azure távoli renderelésben, kivéve a *EmissiveFactor* és a *EmissiveTexture*.

A következő táblázat a leképezést mutatja be:

| glTF | Azure Remote Rendering |
|:-------------------|:--------------------------|
|   baseColorFactor   |   albedoColor              |
|   baseColorTexture  |   albedoMap                |
|   metallicFactor    |   Fémmegmunkálás                |
|   metallicTexture   |   metalnessMap             |
|   roughnessFactor   |   érdesség                |
|   roughnessTexture  |   roughnessMap             |
|   occlusionFactor   |   elzáródás                |
|   occlusionTexture  |   occlusionMap             |
|   normalTexture     |   normalMap                |
|   alphaCutoff       |   alphaClipThreshold       |
|   alphaMode. ÁTLÁTSZATLAN  |   alphaClipEnabled = false, isTransparent = false |
|   alphaMode. MASK    |   alphaClipEnabled = true, isTransparent = false  |
|   alphaMode. BLEND   |   isTransparent = True     |
|   doubleSided       |   isDoubleSided            |
|   emissiveFactor    |   -                        |
|   emissiveTexture   |   -                        |

A glTF minden textúrája tartalmazhat egy `texCoord` értéket, amelyet az Azure távoli renderelési anyagai is támogatnak.

### <a name="embedded-textures"></a>Beágyazott textúrák

A *\* . bin* vagy *\* . borai* fájlokba ágyazott textúrák támogatottak.

### <a name="supported-gltf-extension"></a>Támogatott glTF-bővítmény

Az alapszolgáltatások készletén kívül az Azure Remote rendering a következő glTF-bővítményeket támogatja:

* [MSFT_packing_occlusionRoughnessMetallic](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Vendor/MSFT_packing_occlusionRoughnessMetallic/README.md)
* [KHR_materials_unlit](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Khronos/KHR_materials_unlit/README.md): a [színes anyagoknak](../overview/features/color-materials.md)felel meg. A *Emissive* -anyagok esetében ajánlott ezt a bővítményt használni.
* [KHR_materials_pbrSpecularGlossiness](https://github.com/KhronosGroup/glTF/blob/master/extensions/2.0/Khronos/KHR_materials_pbrSpecularGlossiness/README.md): a fémes-érdes textúrák helyett diffúz ragyogás-textúrákat is biztosíthat. Az Azure távoli renderelési implementációja közvetlenül a bővítmény átalakítási képleteit követi.

## <a name="fbx"></a>FBX

A FBX formátuma zárt – a forrás-és a FBX-anyagok általában nem kompatibilisek a PBR-anyagokkal. A FBX a felületek összetett leírását használja számos egyedi paraméterrel és tulajdonsággal, és **nem mindegyiket használja az Azure távoli renderelési folyamata**.

> [!IMPORTANT]
> Az Azure távoli renderelési modell átalakítási folyamata csak a **2011-es és újabb FBX** támogatja.

A FBX formátuma az anyagok konzervatív megközelítését határozza meg, a hivatalos FBX-specifikációnak csak két típusa van:

* *Lambert* – a már nem gyakran használt sokáig, de továbbra is támogatott, ha az átalakítás időpontjában a (z).
* A *-ben* szinte minden anyag és a legtöbb tartalmi eszköz ezt a típust használja.

Az FBX-modell pontosabb, és *kizárólag* az anyagokhoz használható modellként szolgál. Az alábbiakban *FBX-anyagként* lesz hivatkozva.

> A Maya két egyéni bővítményt használ a FBX-hoz egyéni tulajdonságok definiálásával a PBR és a rája típusú anyagokhoz. Ezek az adatok nem szerepelnek az FBX-specifikációban, ezért az Azure távoli renderelés jelenleg nem támogatja.

A FBX anyagok a diffúz-SpecularLevel koncepciót használják, így a diffúz textúrából egy albedó-térképre való átalakításhoz ki kell számítani a többi paramétert a diffúz kivonáshoz.

> A FBX összes színe és textúrája az sRGB-térben (más néven gamma-terület) van, de az Azure-alapú távoli renderelés lineáris területtel működik a vizualizáció során, és a keret végén átalakítja az összes beállítást az sRGB területre. Az Azure Remote rendering Asset folyamata mindent a lineáris területre konvertál, hogy előkészített adatként küldje el a megjelenítő számára.

Ez a táblázat bemutatja, hogyan képezhetők le textúrák a FBX-anyagokból az Azure távoli renderelési anyagaiba. Némelyiket nem használják közvetlenül, hanem a képletekben részt vevő más textúrákkal (például a diffúz szerkezettel) együtt:

| FBX | Azure Remote Rendering |
|:-----|:----|
| AmbientColor | Elzáródási Térkép   |
| DiffuseColor | *Albedó, fémekhez használatos* |
| TransparentColor | *a albedó alfa-csatornája esetében használatos* |
| TransparencyFactor | *a albedó alfa-csatornája esetében használatos* |
| Homály | *a albedó alfa-csatornája esetében használatos* |
| SpecularColor | *Albedó, Fémesség, érdesség* |
| SpecularFactor| *Albedó, Fémesség, érdesség* |
| ShininessExponent | *Albedó, Fémesség, érdesség* |
| NormalMap | NormalMap |
| Konfigurációkezelő állítható magasabbra | *NormalMap konvertálva* |
| EmissiveColor | - |
| EmissiveFactor | - |
| ReflectionColor | - |
| DisplacementColor | - |

A fenti leképezés az anyag-átalakítás legbonyolultabb része, mivel számos feltételezést kell végrehajtani. Ezeket a feltételezéseket alább mutatjuk be.

Néhány alább használt definíció:

* `Specular` =  `SpecularColor` * `SpecularFactor`
* `SpecularIntensity` = `Specular`. Piros ∗ 0,2125 +  `Specular` . Zöld ∗ 0,7154 + `Specular` . Kék ∗ 0,0721
* `DiffuseBrightness` = 0,299 * `Diffuse` . Piros<sup>2</sup> + 0,587 * `Diffuse` . Zöld<sup>2</sup> + 0,114 * `Diffuse` . Kék<sup>2</sup>
* `SpecularBrightness` = 0,299 * `Specular` . Piros<sup>2</sup> + 0,587 * `Specular` . Zöld<sup>2</sup> + 0,114 * `Specular` . Kék<sup>2</sup>
* `SpecularStrength` = Max ( `Specular` . Piros, `Specular` . Zöld, `Specular` . Kék

A SpecularIntensity képlet [innen](https://en.wikipedia.org/wiki/Luma_(video))származik.
Ez a [specifikáció](http://www.itu.int/dms_pubrec/itu-r/rec/bt/R-REC-BT.601-7-201103-I!!PDF-E.pdf)a fényerő képletét írja le.

### <a name="roughness"></a>Érdesség

`Roughness``Specular` `ShininessExponent` ennek a [képletnek](https://www.cs.cornell.edu/~srm/publications/EGSR07-btdf.pdf)a kiszámítása és használata. A képlet a (z) a következő:

```cpp
Roughness = sqrt(2 / (ShininessExponent * SpecularIntensity + 2))
```

### <a name="metalness"></a>Fémmegmunkálás

`Metalness``Diffuse` `Specular` ezt a [képletet a glTF-specifikáció](https://github.com/bghgary/glTF/blob/gh-pages/convert-between-workflows-bjs/js/babylon.pbrUtilities.js)alapján számítja ki és használja.

Itt az a gondolat, hogy megoldjuk a következő egyenletet: AX<sup>2</sup> + BX + C = 0.
Alapvetően a dielektromos felületek a fény 4%-át tükrözik a fényt tükröző módon, a többi pedig diffúz. A fémes felületek nem tükrözik a fényt a diffúz módon, hanem az egészet.
Ennek a képletnek néhány hátránya van, mivel nem lehet különbséget tenni a fényes műanyagok és a fényes fémes felületek között. Tegyük fel, hogy a felületnek nagy része fémes tulajdonságokkal rendelkezik, ezért a fényes műanyag/gumi felületek nem a várt módon jelennek meg.

```cpp
dielectricSpecularReflectance = 0.04
oneMinusSpecularStrength = 1 - SpecularStrength

A = dielectricSpecularReflectance
B = (DiffuseBrightness * (oneMinusSpecularStrength / (1 - A)) + SpecularBrightness) - 2 * A
C = A - SpecularBrightness
squareRoot = sqrt(max(0.0, B * B - 4 * A * C))
value = (-B + squareRoot) / (2 * A)
Metalness = clamp(value, 0.0, 1.0);
```

### <a name="albedo"></a>Albedó

`Albedo` a, a és a alapján van kiszámítva `Diffuse` `Specular` `Metalness` .

A Metaling című szakaszban leírtak szerint a dielektromos felületek a fény 4%-át tükrözik.  
Az itt található ötlet a és a színek lineáris interpolációja az `Dielectric` `Metal` `Metalness` érték tényezőként való használatával. Ha a fémesség értéke `0.0` , akkor a visszaverődéstől függően sötét színű lesz (ha magas a visszaverődés), vagy a diffúzió nem változik (ha nincs fényvisszaverődés). Ha a fémesség nagy értékű, akkor a diffúz szín eltűnik a fényvisszaverődési szín mellett.

```cpp
dielectricSpecularReflectance = 0.04
oneMinusSpecularStrength = 1 - SpecularStrength

dielectricColor = diffuseColor * (oneMinusSpecularStrength / (1.0f - dielectricSpecularReflectance) / max(1e-4, 1.0 - metalness))
metalColor = (Specular - dielectricSpecularReflectance * (1.0 - metalness)) * (1.0 / max(1e-4, metalness))
albedoRawColor = lerpColors(dielectricColor, metalColor, metalness * metalness)
AlbedoRGB = clamp(albedoRawColor, 0.0, 1.0);
```

`AlbedoRGB` a fenti képlet alapján számítottuk ki, de az alfa-csatorna további számításokat igényel. A FBX formátuma homályos az áttetszőséggel kapcsolatban, és számos módon definiálható. A különböző tartalmi eszközök különböző módszereket használnak. A következő ötlet az, hogy egyesítse őket egyetlen képletbe. A szolgáltatás nem megfelelően átlátszóként jeleníti meg az eszközöket, azonban ha azok nem közös módon jönnek létre.

Ez a következőből lett kiszámítva `TransparentColor` `TransparencyFactor` :,, `Opacity` :

Ha `Opacity` meg van adva, használja közvetlenül a következőket: `AlbedoAlpha`  =  `Opacity` más  
Ha `TransparencyColor` meg van adva, akkor `AlbedoAlpha` = 1,0-(( `TransparentColor` ). Piros + `TransparentColor` . Zöld + `TransparentColor` . Kék)/3,0) egyéb  
If `TransparencyFactor` , then `AlbedoAlpha` = 1,0- `TransparencyFactor`

A végső `Albedo` színnek négy csatornája van, amely a és a együttesét kombinálja `AlbedoRGB` `AlbedoAlpha` .

### <a name="summary"></a>Összefoglalás

Ha itt szeretné összefoglalni, `Albedo` nagyon közel lesz az eredetihöz `Diffuse` , ha `Specular` közel van nullához. Ellenkező esetben a felület egy fémes felületnek fog kinézni, és elveszti a diffúz színt. A felület kifinomultabb és fényvisszaverő lesz, ha `ShininessExponent` elég nagy, és `Specular` világos. Ellenkező esetben a felület durva lesz, és alig tükrözi a környezetet.

### <a name="known-issues"></a>Ismert problémák

* Az aktuális képlet nem működik jól az egyszerű, színes geometria esetében. Ha `Specular` elég fényes, akkor az összes geometriában fényvisszaverő fémes felületek válnak szín nélkül. A megkerülő megoldás az `Specular` eredetitől 30%-ra, a konverziós beállítások [fbxAssumeMetallic](../how-tos/conversion/configure-model-conversion.md#converting-from-older-fbx-formats-with-a-phong-material-model)pedig használható.
* A PBR-anyagok a közelmúltban lettek hozzáadva a `Maya` és a `3DS Max` tartalom-létrehozási eszközökhöz. Egyéni, felhasználó által definiált fekete dobozos tulajdonságokat használnak a FBX való továbbításhoz. Az Azure távoli renderelés nem olvassa el ezeket a további tulajdonságokat, mert nincsenek dokumentálva, és a formátum lezárt – forrás.

## <a name="next-steps"></a>Következő lépések

* [Modell átalakítása](../how-tos/conversion/model-conversion.md)
* [Anyagok felülbírálása a modell átalakítása során](../how-tos/conversion/override-materials.md)
