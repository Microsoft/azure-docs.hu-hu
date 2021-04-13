---
title: Ajánlott eljárások a rendellenesség-Kiderítő többváltozós API használatához
titleSuffix: Azure Cognitive Services
description: Ajánlott eljárások az anomália-Kiderítő többváltozós API-k használatához az idősorozat-adataira vonatkozó anomáliák észleléséhez.
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: mbullwin
keywords: anomáliák észlelése, gépi tanulás, algoritmusok
ms.openlocfilehash: 7de25b4a099c706c05b32b52492096923033f822
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107315612"
---
# <a name="multivariate-time-series-anomaly-detector-best-practices"></a>Többváltozós idősorozat-anomáliák – ajánlott eljárások

Ez a cikk útmutatást nyújt az ajánlott eljárásokról, amelyekkel követheti a többváltozós anomália-érzékelő API-k használatát.

## <a name="how-to-prepare-data-for-training"></a>Az adatképzés előkészítése

Az anomáliák többváltozós API-k használatához az észlelés használata előtt be kell tanítani a saját modelljét. A betanításhoz használt adatmennyiség az idősorozatok egy kötege, minden egyes idősorozatnak CSV formátumúnak kell lennie két oszloppal, egy időbélyeggel és egy értékkel. Az összes idősorozatot egy zip-fájlba kell csomagolni, és fel kell tölteni az Azure Blob Storage-ba. Alapértelmezés szerint a rendszer a fájlnevet fogja használni az idősorozat változójának ábrázolásához. Azt is megteheti, hogy a fájl egy további meta.jsis tartalmazhatja a zip-fájlban, ha azt szeretné, hogy a változó neve eltér a. zip-fájl nevétől. A [blob sas (közös hozzáférésű aláírások) URL-címének](../../../storage/common/storage-sas-overview.md)létrehozása után felhasználhatja azt a betanításhoz.

## <a name="data-quality-and-quantity"></a>Az adatminőség és a mennyiség

A rendellenesség-érzékelő többváltozós API legmodernebb neurális hálózatokkal tanulja meg a múltbeli adatok normál mintáit, és jelzi, hogy a jövőbeli értékek anomáliák-e. Az optimális modell betanításához fontos a betanítási adatmennyiség minősége és mennyisége. Mivel a modell a korábbi adatokból származó normál mintákat tanulja meg, a betanítási adatoknak a rendszer teljes normál állapotát kell képviselnie. Nehéz a modellnek megtanulnia az ilyen típusú mintákat, ha a betanítási adat megtelik a rendellenességektől. Emellett a modell több millió paramétert is tartalmaz, és minimális számú adatpontra van szükség a paraméterek optimális készletének megismeréséhez. Az általános szabály az, hogy legalább 15 000 adatpontot kell megadnia egy változón a modell megfelelő betanításához. Minél több az információ, annál jobb a modell.

Gyakran előfordul, hogy számos idősorozatban hiányoznak az értékek, ami befolyásolhatja a betanított modellek teljesítményét. Az egyes idősorozatok hiányzó arányát ésszerű értékkel kell vezérelni. A hiányzó 90%-os értékkel rendelkező idősorozatok kevés információt biztosítanak a rendszer normál mintáinak. Még rosszabb, a modell normális mintázatként is megtekintheti a kitöltött értékeket, amelyek általában egyenes szegmensek vagy állandó értékek. Amikor új adatforgalom van a-ben, az adatsérüléseket észlelni lehet.

Egy javasolt maximális hiányzó érték küszöbértéke 20%, de bizonyos körülmények között a magasabb küszöbérték is elfogadható. Ha például egy idősorozat egy perces részletességgel és egy másik idősorozattal rendelkezik óránkénti részletességgel.  Óránként 60 adatpont van, és 1 adatpont az óránkénti adatmennyiséghez, ami azt jelenti, hogy az óránkénti adatmennyiségek hiányzó aránya 98,33%. Azonban az óránkénti adatsorozatok kitöltése az egyetlen értékkel történik, ha az óradíjak általában nem ingadoznak.

## <a name="parameters"></a>Paraméterek

### <a name="sliding-window"></a>Ablak csúsztatása

A többváltozós anomáliák észlelése az adatpontok egy szegmensét veszi fel `slidingWindow` bemenetként, és eldönti, hogy a következő adatpont egy anomália-e. Minél nagyobb a mintavételezési hossz, a rendszer a további adatmennyiséget fogja figyelembe venni. A megfelelő érték kiválasztásakor érdemes két dolgot figyelembe vennie `slidingWindow` : a bemeneti adatok tulajdonságai, valamint a képzés/következtetések és a lehetséges teljesítmény-fejlesztés között elkövetett kompromisszum. `slidingWindow` egy 28 és 2880 közötti egész számot tartalmaz. Dönthet úgy, hogy hány adatpontot használ fel bemenetként az adatok rendszeres időközönkénti felhasználásához, valamint az adatok mintavételi sebességének meghatározásához.

Az adatok rendszeres időközönként 1-3 ciklust is tartalmazhatnak bemenetként, és ha az adatok nagy gyakorisággal (kis részletességgel), például perc vagy második szintű adatokba kerülnek, az adatok bemenetként is kiválaszthatók. Egy másik probléma, hogy a hosszabb bemenetek hosszabb betanítást vagy következtetést okozhatnak, és nincs garancia arra, hogy a nagyobb bemeneti pontok a teljesítmény növekedéséhez vezethetnek. Míg a túl kevés adatpont, a modell megnehezíti az optimális megoldás megszervezését. Például nehéz észlelni a rendellenességeket, ha a bemeneti adatok csak két ponttal rendelkeznek.

### <a name="align-mode"></a>Igazítási mód

A paraméter `alignMode` használatával jelezhető, hogy az időbélyegzők több időpontját hogyan szeretné igazítani. Ennek az az oka, hogy sok idősorozatban hiányoznak az értékek, és a további feldolgozás előtt össze kell őket hangolni egymással. A paraméternek két lehetősége van: `inner join` és `outer join` . `inner join` azt jelenti, hogy a rendszer jelentést készít az olyan időbélyegzők eredményeiről, amelyekben **minden idősorozat** értékkel rendelkezik, míg az `outer join` azt jelenti, hogy a rendszer jelentést készít az időbélyegekről az adott értékkel rendelkező **idősorokhoz** .  **A `alignMode` szintén hatással van a modell bemeneti sorrendjére**, ezért válasszon ki egy megfelelőt a `alignMode` forgatókönyvhöz, mert az eredmények jelentős mértékben eltérőek lehetnek.

Itt egy példát mutatunk be a különböző `alignModel` értékek magyarázatára.

#### <a name="series1"></a>Series1

|időbélyeg | érték|
----------| -----|
|`2020-11-01`| 1  
|`2020-11-02`| 2  
|`2020-11-04`| 4  
|`2020-11-05`| 5

#### <a name="series2"></a>Series2

időbélyeg | érték  
--------- | -
`2020-11-01`| 1  
`2020-11-02`| 2  
`2020-11-03`| 3  
`2020-11-04`| 4

#### <a name="inner-join-two-series"></a>Belső illesztés két sorozathoz
  
időbélyeg | Series1 | Series2
----------| - | -
`2020-11-01`| 1 | 1
`2020-11-02`| 2 | 2
`2020-11-04`| 4 | 4

#### <a name="outer-join-two-series"></a>Külső illesztés két sorozathoz

időbélyeg | series1 | series2
--------- | - | -
`2020-11-01`| 1 | 1
`2020-11-02`| 2 | 2
`2020-11-03`| NA | 3
`2020-11-04`| 4 | 4
`2020-11-05`| 5 | NA

### <a name="fill-not-available-na"></a>A kitöltés nem érhető el (NA)

Ha a változók külső illesztéssel vannak igazítva az időbélyeghez, előfordulhat, hogy néhány `Not Available` ( `NA` ) érték van néhány változóban. Megadhatja ezt a NA értéket. A következő lehetőségei vannak:,,, `fillNAMethod` `Linear` `Previous` `Subsequent`  `Zero` és `Fixed` .

| Beállítás     | Metódus                                                                                           |
| ---------- | -------------------------------------------------------------------------------------------------|
| Lineáris     | NAGY értékek kitöltése lineáris interpoláció alapján                                                           |
| Előző   | A legutóbbi érvényes érték propagálása a hézagok kitöltéséhez. Példa: `[1, 2, nan, 3, nan, 4]` -> `[1, 2, 2, 3, 3, 4]` |
| További | A hézagok kitöltéséhez használja a következő érvényes értéket. Példa: `[1, 2, nan, 3, nan, 4]` -> `[1, 2, 3, 3, 4, 4]`       |
| Eggyel sem       | Adja meg a NA értékeket 0 értékkel.                                                                           |
| Rögzített méretű lemez      | Adja meg a megadott érvényes értékkel rendelkező NA értékeket a alkalmazásban `paddingValue` .          |

## <a name="model-analysis"></a>Modell elemzése

### <a name="training-latency"></a>Betanítási késés

A többváltozós anomáliák észlelésének tanítása időigényes lehet. Különösen akkor, ha nagy mennyiségű időbélyeg van használatban a betanításhoz. Ezért engedélyezzük a betanítási folyamat egy részét aszinkron módon. A felhasználók általában a Train Model API használatával küldenek betanítási feladatot. Ezután lekérdezheti a modell állapotát a használatával `Get Multivariate Model API` . Itt bemutatjuk, hogyan lehet kinyerni a hátralévő időt a képzés befejeződése előtt. A többváltozós modell API-válaszának beolvasása területen található egy nevű elem `diagnosticsInfo` . Ebben az elemben van egy `modelState` elem. A hátralévő idő kiszámításához a és a-t kell használnia `epochIds` `latenciesInSeconds` . Az időpontok egy teljes ciklust jelentenek a betanítási adatként. A rendszer minden 10 időpontban kiadja a kimeneti állapotinformációkat. Összességében a 100-es időpontok betanítása után a késés azt jelzi, hogy mennyi ideig tart a korszak. Ezen információk alapján a modell betanítása előtt hátralévő időt is tudjuk.

### <a name="model-performance"></a>A modell teljesítménye

Többváltozós anomáliák észlelése nem felügyelt modellként. A legjobb módszer az, ha kiértékeli a anomáliák eredményeit manuálisan. A többváltozós modell beolvasása válaszban néhány alapvető információt biztosítunk számunkra a modell teljesítményének elemzéséhez. A `modelState` többváltozós modell API beolvasása által visszaadott elemben a `trainLosses` és `validationLosses` a segítségével kiértékelheti, hogy a modell a várt módon van-e kitanítva. A legtöbb esetben a két veszteség fokozatosan csökkenni fog. Egy másik információ számunkra, hogy elemezzük a modell teljesítményét `variableStates` . A változók állapotának listája csökkenő sorrendben van rangsorolva `filledNARatio` . Minél nagyobb a teljesítménye, általában a lehető legnagyobb mértékben csökkenteniük kell ezt a teljesítményt `NA ratio` . `NA` az oka lehet a hiányzó értékek vagy a nem igazított változók timestamp perspektívából való létrehozása.

## <a name="next-steps"></a>Következő lépések

- Gyors [útmutató.](../quickstarts/client-libraries-multivariate.md)
- [Ismerje meg az olyan alapul szolgáló algoritmusokat, amelyek az energiagazdálkodási rendellenességet érzékelő többváltozós](https://arxiv.org/abs/2009.02040)