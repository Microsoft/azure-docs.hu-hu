---
title: Azure Machine Learning rendellenesség észlelése API – csoportos adatelemzési folyamat
description: Az anomália-észlelési API egy olyan Microsoft Azure Machine Learning, amely a Time Series-adatokban az időben egyenletesen elhelyezkedő numerikus értékekkel észlelt rendellenességeket észleli.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=alokkirpal, previous-ms.author=alok
ms.openlocfilehash: e210c1683d5f14181bc0549e73a892eb91d2e746
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "93305703"
---
# <a name="machine-learning-anomaly-detection-api"></a>Machine Learning rendellenesség-észlelési API

> [!NOTE]
> Ez az elem karbantartás alatt áll. Javasoljuk, hogy az Azure Cognitive Services Machine Learning algoritmusait használó [anomália-érzékelő API-szolgáltatást](https://azure.microsoft.com/services/cognitive-services/anomaly-detector/) használja az üzleti, működési és IoT mérőszámokból származó rendellenességek észlelésére.

## <a name="overview"></a>Áttekintés
Az [anomália-észlelési API](https://gallery.azure.ai/MachineLearningAPI/Anomaly-Detection-2) egy olyan Azure Machine learning, amely a Time Series-adatokban az időben egyenletesen elhelyezkedő numerikus értékekkel észlelt rendellenességeket észleli.

Ez az API a következő típusú rendellenes mintákat ismeri fel az idősorozat-adatsorokban:

* **Pozitív és negatív trendek**: Ha például a rendszer a memória használatának figyelése során felhasznál egy emelkedő trendet, előfordulhat, hogy a memória szivárgását jelezhetik,
* **Értékek dinamikus tartományának változásai**: például a Cloud Service által okozott kivételek figyelése esetén az értékek dinamikus tartományának változásai a szolgáltatás állapotának instabilitását jelezhetik, és
* **Tüskék és dipsok**: Ha például egy szolgáltatás bejelentkezési hibáinak számát vagy egy e-kereskedelmi helyen lévő pénztárak számát figyeli, a tüskék vagy a dips rendellenes viselkedést jelezhet.

Ezek a gépi tanulási érzékelők az értékek időbeli változásait követik, és az értékük folyamatos változásait az anomália pontszámként jelentik. Nem igénylik az alkalmi küszöbértékek finomhangolását, és a pontszámok a hamis pozitív arány szabályozására használhatók. A rendellenesség-észlelési API számos olyan forgatókönyvben hasznos, mint például a szolgáltatások figyelése a KPI-k időbeli követésével, a használat monitorozásával, például a keresések számával, a kattintások számával, a teljesítmény figyelésével, többek között a memóriával, a CPU-val, a fájlok olvasásával és

Az anomáliák észlelésére szolgáló ajánlat hasznos eszközöket kínál a kezdéshez.

* A [webalkalmazás](https://anomalydetection-aml.azurewebsites.net/) segítségével kiértékelheti és megjelenítheti az adatrendellenesség-észlelési API-kat az adatain.

> [!NOTE]
> Próbálja ki [az API](https://gallery.azure.ai/MachineLearningAPI/Anomaly-Detection-2) által működtetett **anomália** -alapú megoldást
>
<!-- This Solution is no longer available
> To get this end to end solution deployed to your Azure subscription <a href="https://gallery.cortanaintelligence.com/Solution/Anomaly-Detection-Pre-Configured-Solution-1" target="_blank">**Start here >**</a>
-->

## <a name="api-deployment"></a>API üzembe helyezése
Az API használatához telepítenie kell azt az Azure-előfizetésre, ahol Azure Machine Learning webszolgáltatásként fog futni.  Ezt a [Azure AI Gallery](https://gallery.azure.ai/MachineLearningAPI/Anomaly-Detection-2)teheti meg.  Ez két Azure Machine Learning Studio (klasszikus) webszolgáltatást (és az azokhoz kapcsolódó erőforrásokat) helyez üzembe az Azure-előfizetésében – az egyiket a szezonális észlelés és a szezonális észlelés nélkül.  Az üzembe helyezés befejezése után az API-kat a [Azure Machine learning Studio (klasszikus) webszolgáltatások](https://services.azureml.net/webservices/) oldaláról kezelheti.  Ezen a lapon megtalálhatja a végpontok helyét, az API-kulcsokat, valamint az API meghívására szolgáló mintakód-kódot.  Részletesebb utasítások [itt](../classic/manage-new-webservice.md)érhetők el.

## <a name="scaling-the-api"></a>Az API skálázása
Alapértelmezés szerint az üzembe helyezés ingyenes fejlesztési/tesztelési számlázási csomaggal rendelkezik, amely 1 000 tranzakciót, hónapot és 2 számítási órát/hónapot tartalmaz.  Igény szerint frissíthet más csomagokra is.  A különböző csomagok díjszabásáról a "Production web API díjszabása" [című szakaszban olvashat](https://azure.microsoft.com/pricing/details/machine-learning/) bővebben.

## <a name="managing-aml-plans"></a>A pénzmosás-csomagok kezelése
A számlázási tervet [itt](https://services.azureml.net/plans/)kezelheti.  A csomag neve az API telepítésekor választott erőforráscsoport-név alapján, valamint egy, az előfizetéshez egyedi karakterláncot fog alapulni.  A terv frissítésével kapcsolatos utasítások a "számlázási csomagok kezelése" [szakaszban találhatók.](../classic/manage-new-webservice.md)

## <a name="api-definition"></a>API-definíció
A webszolgáltatás egy REST-alapú API-t biztosít a HTTPS-en keresztül, amely különböző módokon használható, például webes vagy mobil alkalmazások, R, Python, Excel stb. használatával.  Az idősoros adatait REST API híváson keresztül küldi el a szolgáltatásnak, és az alább leírt három rendellenesség-típus kombinációját futtatja.

## <a name="calling-the-api"></a>Az API meghívása
Az API meghívásához ismernie kell a végpont helyét és az API-kulcsot.  Ez a két követelmény, valamint az API meghívásához használható mintakód a [Azure Machine learning Studio (klasszikus) webszolgáltatások](https://services.azureml.net/webservices/) oldaláról érhető el.  Keresse meg a kívánt API-t, majd kattintson a "felhasználás" fülre.  Meghívhatja az API-t egy hencegő API-ként (azaz az URL-paraméterrel `format=swagger` ) vagy nem hencegő API-ként (azaz az `format` URL-paraméter nélkül).  A mintakód a hencegő formátumot használja.  Az alábbi példa egy kérelem és válasz nem hencegő formátumú.  Ezek a példák a szezonális végpontra vonatkoznak.  A nem szezonális végpont hasonló.

### <a name="sample-request-body"></a>Mintául szolgáló kérelem törzse
A kérelem két objektumot tartalmaz: `Inputs` és `GlobalParameters` .  Az alábbi példában szereplő kérelemben bizonyos paraméterek küldése explicit módon megtörténik, míg mások nem (az egyes végpontok összes paraméterének teljes listáját lefelé görgetve).  A kérelemben nem kifejezetten elküldett paraméterek az alább megadott alapértelmezett értékeket fogják használni.

```json
{
            "Inputs": {
                    "input1": {
                            "ColumnNames": ["Time", "Data"],
                            "Values": [
                                    ["5/30/2010 18:07:00", "1"],
                                    ["5/30/2010 18:08:00", "1.4"],
                                    ["5/30/2010 18:09:00", "1.1"]
                            ]
                    }
            },
    "GlobalParameters": {
        "tspikedetector.sensitivity": "3",
        "zspikedetector.sensitivity": "3",
        "bileveldetector.sensitivity": "3.25",
        "detectors.spikesdips": "Both"
    }
}
```

### <a name="sample-response"></a>Mintaválasz
A mező megjelenítéséhez `ColumnNames` URL-paraméterként kell szerepelnie a `details=true` kérelemben.  Tekintse meg az alábbi táblázatokat az egyes mezők mögötti jelentésekhez.

```json
{
    "Results": {
        "output1": {
            "type": "table",
            "value": {
                "Values": [
                    ["5/30/2010 6:07:00 PM", "1", "1", "0", "0", "-0.687952590518378", "0", "-0.687952590518378", "0", "-0.687952590518378", "0"],
                    ["5/30/2010 6:08:00 PM", "1.4", "1.4", "0", "0", "-1.07030497733224", "0", "-0.884548154298423", "0", "-1.07030497733224", "0"],
                    ["5/30/2010 6:09:00 PM", "1.1", "1.1", "0", "0", "-1.30229513613974", "0", "-1.173800281031", "0", "-1.30229513613974", "0"]
                ],
                "ColumnNames": ["Time", "OriginalData", "ProcessedData", "TSpike", "ZSpike", "BiLevelChangeScore", "BiLevelChangeAlert", "PosTrendScore", "PosTrendAlert", "NegTrendScore", "NegTrendAlert"],
                "ColumnTypes": ["DateTime", "Double", "Double", "Double", "Double", "Double", "Int32", "Double", "Int32", "Double", "Int32"]
            }
        }
    }
}
```


## <a name="score-api"></a>Pontszám API
A score API-t a nem szezonális idősorozat-adatsorokon futó anomáliák észlelésére használják. Az API számos anomália-észlelést futtat az adatokon, és visszaadja a anomáliák pontszámait.
Az alábbi ábrán egy példa látható a pontszám API által észlelt rendellenességekre. Ebben az idősorozatban két különböző szintű módosítás és három tüske látható. A piros pontok azt az időpontot mutatják, amikor a rendszer a szint változását észleli, míg a fekete pontok az észlelt tüskéket mutatják.
![Pontszám API][1]

### <a name="detectors"></a>Érzékelők
Az anomáliák észlelése API három széles kategóriában támogatja az érzékelők használatát. Az egyes detektorok megadott bemeneti paramétereinek és kimenetének részletei a következő táblázatban találhatók.

| Detektor kategóriája | Detektor | Leírás | Bemeneti paraméterek | Kimenetek |
| --- | --- | --- | --- | --- |
| Tüske-érzékelők |TSpike-detektor |A tüskék és a dips értékek észlelése az első és a harmadik quartiles alapján |*tspikedetector. érzékenység:* egész értéket vesz igénybe a 1-10 tartományban, alapértelmezett érték: 3; A magasabb értékek több szélsőséges értéket kapnak, így kevésbé érzékenyek |TSpike: bináris értékek – "1", ha a rendszer nyársat/dip-t észlel, máskülönben "0". |
| Tüske-érzékelők | ZSpike-detektor |A tüskék és a dipsok észlelése attól függően, hogy a datapoints mennyi ideig tartanak |*zspikedetector. érzékenység:* egész értéket kell megtennie a 1-10 tartományban, alapértelmezett érték: 3; A magasabb értékek több szélsőséges értéket kapnak, ami kevésbé érzékeny |ZSpike: bináris értékek – "1", ha a rendszer nyársat/dip-t észlel, máskülönben "0". |
| Lassú trend detektor |Lassú trend detektor |A lassú pozitív trend észlelése a beállított érzékenység alapján |*trenddetector. érzékenység:* a detektor pontszámának küszöbértéke (alapértelmezett: 3,25, 3,25 – 5) egy elfogadható tartomány, amely közül választhat. Minél nagyobb a kevésbé érzékeny) |tscore: a trendi anomália pontszámát jelképező lebegőpontos szám |
| Szint változási érzékelők | Kétirányú változás-érzékelő |A növekvő és a csökkenő szintű változások észlelése a beállított érzékenység alapján |*bileveldetector. érzékenység:* a detektor pontszámának küszöbértéke (alapértelmezett: 3,25, 3,25 – 5) egy elfogadható tartomány, amely közül választhat. Minél nagyobb a kevésbé érzékeny) |rpscore: a felfelé és lefelé irányuló anomália pontszámát jelképező lebegőpontos szám |

### <a name="parameters"></a>Paraméterek
A bemeneti paraméterekkel kapcsolatos részletesebb információkat az alábbi táblázat tartalmazza:

| Bemeneti paraméterek | Leírás | Alapértelmezett beállítás | Típus | Érvényes tartomány | Javasolt tartomány |
| --- | --- | --- | --- | --- | --- |
| érzékelők. historywindow |A anomália pontszám számításához használt előzmények (adatpontok száma) |500 |egész szám |10-2000 |Idősorozat-függő |
| érzékelők. spikesdips | Azt határozza meg, hogy csak tüskék, csak dips vagy mindkettő érzékelhető-e |Mindkettő |felsorolt |Mindkettő, tüskék, dips |Mindkettő |
| bileveldetector. érzékenység |A kétirányú adatváltozási detektor érzékenysége. |3,25 |double |Nincsenek |3,25-5 (a kisebb értékek nagyobb érzékenységet jelentenek) |
| trenddetector. érzékenység |A pozitív trend-detektor érzékenysége. |3,25 |double |Nincsenek |3,25-5 (a kisebb értékek nagyobb érzékenységet jelentenek) |
| tspikedetector. érzékenység |TSpike-detektor érzékenysége |3 |egész szám |1-10 |3-5 (a kisebb értékek nagyobb érzékenységet jelentenek) |
| zspikedetector. érzékenység |ZSpike-detektor érzékenysége |3 |egész szám |1-10 |3-5 (a kisebb értékek nagyobb érzékenységet jelentenek) |
| utófeldolgozás. tailRows |A kimeneti eredményekben megőrizni kívánt legfrissebb adatpontok száma |0 |egész szám |0 (az összes adatpont megtartása), vagy az eredményekben megtartani kívánt pontok számának meghatározása |N/A |

### <a name="output"></a>Kimenet
Az API az idősorozat-adatokon futtatja az összes érzékelőt, és minden egyes időpontra vonatkozóan visszaadja a anomália pontszámokat és a bináris csúcs-jelölőket. Az alábbi táblázat az API kimeneteit sorolja fel.

| Kimenetek | Leírás |
| --- | --- |
| Idő |A nyers adatokból, illetve összesített (és/vagy) imputált adatokból származó időbélyegek, ha az Összesítés (és/vagy) hiányzik az adatok imputálási. |
| Adatok |A nyers adatokból, illetve összesített (és/vagy) imputált adatokból származó értékek, ha az Összesítés (és/vagy) hiányzik az adatok imputálási alkalmazása |
| TSpike |Bináris kijelző, amely azt jelzi, hogy a TSpike detektor észleli-e a nyársat |
| ZSpike |Bináris kijelző, amely azt jelzi, hogy a ZSpike detektor észleli-e a nyársat |
| rpscore |A kétszintű változáshoz tartozó anomália pontszámot jelképező lebegőpontos szám |
| rpalert |1/0 érték, amely azt jelzi, hogy a bemeneti érzékenység alapján kétirányú változási rendellenesség van |
| tscore |A pozitív trend anomália pontszámát jelképező lebegőpontos szám |
| talert |1/0 érték, amely azt jelzi, hogy a bemeneti érzékenység alapján pozitív tendenciát észlelt a rendszer |

## <a name="scorewithseasonality-api"></a>ScoreWithSeasonality API
A ScoreWithSeasonality API-t a rendszer a anomáliák észlelésére használja az idősorozatok szezonális mintázatával. Ez az API hasznos az eltérések észleléséhez az idényjellegű mintákban.
Az alábbi ábrán egy példa látható a szezonális idősorokban észlelt rendellenességekre. Az idősorozat egy tüske (az első fekete pont), két dips (a második fekete pont és egy végén), valamint egy szint változás (piros pont). Az idősorozat közepén mind a dip, mind a szint változása csak az időszakos összetevőknek az adatsorozatból való eltávolítása után felismerhető.
![Szezonális API][2]

### <a name="detectors"></a>Érzékelők
A szezonális végpontban található érzékelők hasonlók a nem szezonális végponthoz, de némileg eltérő paraméterek nevei (alább láthatók).

### <a name="parameters"></a>Paraméterek

A bemeneti paraméterekkel kapcsolatos részletesebb információkat az alábbi táblázat tartalmazza:

| Bemeneti paraméterek | Leírás | Alapértelmezett beállítás | Típus | Érvényes tartomány | Javasolt tartomány |
| --- | --- | --- | --- | --- | --- |
| előfeldolgozás. aggregationInterval |Összesítési időköz másodpercben a bemeneti idősorozatok összesítéséhez |0 (nem történt összesítés) |egész szám |0: az Összesítés kihagyása, > 0, máskülönben |5 perc – 1 nap, idősorozat-függő |
| előfeldolgozás. aggregationFunc |Az adatnak a megadott AggregationInterval való összesítéséhez használt függvény |középérték |felsorolt |középérték, összeg, hossz |N/A |
| előfeldolgozás. replaceMissing |Hiányzó adatok eltulajdonítása esetén használt értékek |LKV (utolsó ismert érték) |felsorolt |nulla, LKV, Mean |N/A |
| érzékelők. historywindow |A anomália pontszám számításához használt előzmények (adatpontok száma) |500 |egész szám |10-2000 |Idősorozat-függő |
| érzékelők. spikesdips | Azt határozza meg, hogy csak tüskék, csak dips vagy mindkettő érzékelhető-e |Mindkettő |felsorolt |Mindkettő, tüskék, dips |Mindkettő |
| bileveldetector. érzékenység |A kétirányú adatváltozási detektor érzékenysége. |3,25 |double |Nincsenek |3,25-5 (a kisebb értékek nagyobb érzékenységet jelentenek) |
| postrenddetector. érzékenység |A pozitív trend-detektor érzékenysége. |3,25 |double |Nincsenek |3,25-5 (a kisebb értékek nagyobb érzékenységet jelentenek) |
| negtrenddetector. érzékenység |A negatív trend detektorának érzékenysége. |3,25 |double |Nincsenek |3,25-5 (a kisebb értékek nagyobb érzékenységet jelentenek) |
| tspikedetector. érzékenység |TSpike-detektor érzékenysége |3 |egész szám |1-10 |3-5 (a kisebb értékek nagyobb érzékenységet jelentenek) |
| zspikedetector. érzékenység |ZSpike-detektor érzékenysége |3 |egész szám |1-10 |3-5 (a kisebb értékek nagyobb érzékenységet jelentenek) |
| szezonális. Enable |Azt határozza meg, hogy a szezonális elemzést kell-e elvégezni |true |boolean |igaz, hamis |Idősorozat-függő |
| szezonális. numSeasonality |Az észlelni kívánt időszakos ciklusok maximális száma |1 |egész szám |1, 2 |1-2 |
| szezonális. átalakítás |Azt határozza meg, hogy a szezonális (és) trend-összetevőket el kell-e távolítani az anomáliák észlelése előtt |leszezon |felsorolt |nincs, deszezon, deseasontrend |N/A |
| utófeldolgozás. tailRows |A kimeneti eredményekben megőrizni kívánt legfrissebb adatpontok száma |0 |egész szám |0 (az összes adatpont megtartása), vagy az eredményekben megtartani kívánt pontok számának meghatározása |N/A |

### <a name="output"></a>Kimenet
Az API az idősorozat-adatokon futtatja az összes érzékelőt, és minden egyes időpontra vonatkozóan visszaadja a anomália pontszámokat és a bináris csúcs-jelölőket. Az alábbi táblázat az API kimeneteit sorolja fel.

| Kimenetek | Leírás |
| --- | --- |
| Idő |A nyers adatokból, illetve összesített (és/vagy) imputált adatokból származó időbélyegek, ha az Összesítés (és/vagy) hiányzik az adatok imputálási. |
| OriginalData |A nyers adatokból, illetve összesített (és/vagy) imputált adatokból származó értékek, ha az Összesítés (és/vagy) hiányzik az adatok imputálási alkalmazása |
| ProcessedData |A következő lehetőségek egyike: <ul><li>Szezonálisan beállított idősorozat, ha a rendszer jelentős szezonális elemet észlelt, és kijelöli a kilépési lehetőséget.</li><li>szezonálisan igazított és elválasztott idősorozat, ha a rendszer jelentős szezonális észlelést észlelt, és kiválasztotta a deseasontrend beállítást</li><li>Ellenkező esetben ez a beállítás megegyezik a OriginalData</li> |
| TSpike |Bináris kijelző, amely azt jelzi, hogy a TSpike detektor észleli-e a nyársat |
| ZSpike |Bináris kijelző, amely azt jelzi, hogy a ZSpike detektor észleli-e a nyársat |
| BiLevelChangeScore |A szint változásakor a anomália pontszámot jelképező lebegőpontos szám |
| BiLevelChangeAlert |1/0 érték, amely azt jelzi, hogy a bemeneti érzékenység alapján van egy szint változási rendellenesség |
| PosTrendScore |A pozitív trend anomália pontszámát jelképező lebegőpontos szám |
| PosTrendAlert |1/0 érték, amely azt jelzi, hogy a bemeneti érzékenység alapján pozitív tendenciát észlelt a rendszer |
| NegTrendScore |A negatív trenden alapuló anomália-pontszámot jelölő lebegőpontos szám |
| NegTrendAlert |1/0 érték, amely azt jelzi, hogy a bemeneti érzékenységen alapuló negatív trendi anomália |

[1]: ./media/apps-anomaly-detection-api/anomaly-detection-score.png
[2]: ./media/apps-anomaly-detection-api/anomaly-detection-seasonal.png