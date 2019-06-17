---
title: Az Azure Cosmos DB-ben konzisztenciaszintek
description: Az Azure Cosmos DB öt konzisztenciaszint egyenleg végleges konzisztencia, a rendelkezésre állás és a késleltetés kompromisszummal segítségével rendelkezik.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/20/2019
ms.openlocfilehash: dcd51756a9c5a5a24a082862bb911cc2d2605d61
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65954373"
---
# <a name="consistency-levels-in-azure-cosmos-db"></a>Az Azure Cosmos DB-ben konzisztenciaszintek

Elosztott adatbázisok által használt, magas rendelkezésre állás, alacsony késleltetésű, illetve mindkettőt, replikációs győződjön meg az olvasás következetes és a rendelkezésre állási, teljesítmény és késés alapvető magával. A legtöbb kereskedelmi forgalomban kapható elosztott adatbázisok kérje meg a fejlesztők számára, hogy a két szélsőséges konzisztencia modell közül választhat: *erős* konzisztencia és *végleges* konzisztencia. A  [linearizálhatósági](https://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf) , vagy az erős konzisztencia modell adatok programozhatóság gold színvonalát. De áron (a stabil állapotot) nagyobb késést hozzáadja, és csökkenteni a rendelkezésre állás (meghibásodások). Végleges konzisztencia, másrészt magasabb rendelkezésre állás és jobb teljesítményt nyújt, de megkönnyíti rögzített alkalmazások. 

Az Azure Cosmos DB választási helyett két szélsőséges spektrumát, az adatkonzisztencia közelíti meg. Erős konzisztencia és a végleges konzisztencia az értéktartományon végén, de vannak számos konzisztenciaszint mentén az értéktartományon. A fejlesztők ezek a beállítások használatával pontos lehetőségeket és a részletes és kompromisszumot kínál a magas rendelkezésre állás és teljesítmény. 

Az Azure Cosmos DB öt jól definiált konzisztenciamodellekkel konzisztencia spektrum fejlesztők is választhat. A legerősebb több enyhe, hogy a modellek között *erős*, *korlátozott frissesség*, *munkamenet*, *konzisztens előtag*, és *végleges* konzisztencia. A modellek jól definiált és intuitív és bizonyos valós forgatókönyvek esetén is használható. Minden modell biztosít [rendelkezésre állás és teljesítmény kompromisszumot kínál a](consistency-levels-tradeoffs.md) és az SLA-k használatával. Az alábbi képen látható a különböző konzisztenciaszintet egy átfogó.

![A spektrum, konzisztencia](./media/consistency-levels/five-consistency-levels.png)

A konzisztenciaszintek régiófüggetlen, és garantáltan a régiót, amelyből az olvasási és írási szolgáltatás szolgálja ki, az Azure Cosmos-fiókjához társított régiók számának függetlenül minden művelet, vagy hogy a fiók egyetlen van beállítva vagy több írási régiót.

## <a name="scope-of-the-read-consistency"></a>Az olvasás következetes hatóköre

Partíciókulcs tartományt és logikai partíció belül hatóköre egyetlen olvasási művelet olvasásának következetessége vonatkozik. Az olvasási műveletet egy távoli ügyfélen vagy egy tárolt eljárást adhatnak ki.

## <a name="configure-the-default-consistency-level"></a>Az alapértelmezett konzisztenciaszint beállítása

Az alapértelmezett konzisztenciaszint bármikor konfigurálhatja a Azure Cosmos-fiókjában. Az alapértelmezett konzisztenciaszint a fiók konfigurálva minden Azure Cosmos-adatbázis és az adott fiók tárolók vonatkozik. Olvasási és a egy tároló vagy adatbázis állították lekérdezések használják alapértelmezés szerint a megadott konzisztencia szintjét. További tudnivalókért lásd: hogyan [konfigurálhatja az alapértelmezett konzisztenciaszint](how-to-manage-consistency.md#configure-the-default-consistency-level).

## <a name="guarantees-associated-with-consistency-levels"></a>Konzisztenciaszintek társított garanciák

Az átfogó SLA-k az Azure Cosmos DB garantálja, hogy az olvasási kérések 100 %-os teljesíti-e megfelelő konzisztenciagaranciának bármely konzisztenciaszint választja által biztosított. A konzisztenciaszint társított összes konzisztenciagaranciákat teljesülése esetén egy olvasási kérést megfelel-e a konzisztencia SLA-t. Az Azure Cosmos DB-öt konzisztenciaszintekről pontos meghatározását a [TLA + specifikációs nyelv](https://lamport.azurewebsites.net/tla/tla.html) szerepelnek a [azure-cosmos-tla](https://github.com/Azure/azure-cosmos-tla) GitHub-adattárban. 

Az öt konzisztenciaszintek szemantikáját ebben a témakörben találhatók:

- **Erős**: Erős konzisztencia kínálja a [linearizálhatósági](https://aphyr.com/posts/313-strong-consistency-models) garantálja. Az olvasások garantáltan adja vissza egy elem legutóbbi véglegesített verzióba. Egy ügyfél soha nem lát egy nem véglegesített vagy részleges írása. Felhasználók mindig garantáltan olvassa el a legutóbbi véglegesített írása.

- **Korlátozott frissesség**: Tartsa tiszteletben a konzisztens előtag garantálja az olvasások garantáltan. Az olvasási késés jelentkezhet írásokat legfeljebb *"K"* verziók (azaz a "frissítések") egy elem vagy által *"T"* időtartam alatt. Más szóval ha úgy dönt, korlátozott frissesség, a "frissesség" az két módon konfigurálhatók: 

  * Verzióinak száma (*K*) elem
  * Az időintervallum (*T*) által, amely az olvasási késés jelentkezhet az írási műveletek 

  Korlátozott frissesség ajánlatok teljes globális rendelési kivételével a "frissesség időszakban." A monoton olvasási garancia egy adott régión belül és a frissesség időszakon kívül is belül léteznek. Erős konzisztencia rendelkezik azzal, korlátozott frissesség által kínált azonos szemantikáját. A frissesség időszak nem lehet negatív érték. Korlátozott frissesség is idő – késleltetett linearizálhatósági nevezik. Amikor egy ügyfél ugyanabban a régióban, amely írási olvasási műveleteket végez, korlátozott frissesség konzisztencia által nyújtott garanciák megegyeznek biztosítékok által az erős konzisztencia.

- **Munkamenet**: Tartsa tiszteletben a konzisztens előtag (feltéve, hogy egyetlen "író" munkamenet), monoton olvasások, monoton írások, olvassa el az írásokat, és írási-követi-olvasások garantálja az olvasások garantáltan. Egy ügyfél-munkamenet munkamenet-konzisztencia hatókörét.

- **Konzisztens előtag**: Visszaadott frissítések nincsenek szünetek és az összes frissítés néhány előtagja tartalmaznak. Konzisztens előtag konzisztenciaszint garantálja, hogy olvasási out soron kívüli írások soha nem jelenik meg.

- **Eventual**: Nincs sorbarendezésre garanciát az olvasásokhoz. A további írási műveleteket hiányában a replikák végül szerveződik.

## <a name="consistency-levels-explained-through-baseball"></a>Konzisztenciaszintek a Baseball példáján

Vessünk egy baseball játék forgatókönyv példaként. Tegyük fel, amelyek a pontszám a baseball játék írási műveletek sorozata. A sor a inning-inning pontszám leírt a [replikált adatok konzisztencia baseball](https://www.microsoft.com/en-us/research/wp-content/uploads/2011/10/ConsistencyAndBaseballReport.pdf) tanulmány. A képzeletbeli baseball játékot jelenleg éppen a hetedik inning. A fenti – inning stretch. A látogatók vannak mögött egy 2 – 5 pontszámát az alább látható módon:

| | **1** | **2** | **3** | **4** | **5** | **6** | **7** | **8** | **9** | **Futtatások** |
| - | - | - | - | - | - | - | - | - | - | - |
| **Látogatók** | 0 | 0 | 1 | 0 | 1 | 0 | 0 |  |  | 2 |
| **Kezdőlap** | 1 | 0 | 1 | 1 | 0 | 2 |  |  |  | 5 |

Az Azure Cosmos-tárolókat a látogatók és az otthoni csapatok a futtatási összegek tárolja. Amíg folyamatban van a játék, különböző olvasási garancia eredményezheti, hogy az ügyfelek különböző pontszámok olvasása. Az alábbi táblázat felsorolja, előfordulhat, hogy a látogatók és az egyes a öt konzisztenciagaranciákat otthoni pontszámok olvasásával visszaadandó eredmények teljes készletét. A látogatói pontszám jelenik meg először. Különböző lehetséges visszatérési értékek vesszővel kell elválasztani.

| **Konzisztenciaszint** | **Pontszámmodell (látogatók, otthoni)** |
| - | - |
| **Erős** | 2 – 5 |
| **Korlátozott frissesség** | Olyan pontszámokat, amelyek legfeljebb egy elavult inning: 2-3, 2-4, 2-5 |
| **Munkamenet** | <ul><li>Az író: a 2 – 5</li><li> Bárki a író eltérő: 0-0, 0 és 1, 0-2, 0 – 3, és 4 közötti 0, 0 – 5, 1-0, 1-1, 1-2, 1 – 3, 1 – 4, 1 – 5, 2-0, 2-1, 2-2, 2-3, 2 – 4, 2 – 5</li><li>1 – 3 elolvasásával: 1-3, 1-4, 1-5, 2-3, 2-4, 2-5</li> |
| **Konzisztens előtag** | 0-0, 0 – 1, 1-1, 1-2, 1 – 3, 2-3, 2 – 4, 2 – 5 |
| **Végleges** | 0-0, 0 és 1, 0-2, 0 – 3, és 4 közötti 0, 0 – 5, 1-0, 1-1, 1-2, 1 – 3, 1 – 4, 1 – 5, 2-0, 2-1, 2-2, 2-3, 2 – 4, 2 – 5 |

## <a name="additional-reading"></a>További olvasnivaló

Konzisztencia fogalmak kapcsolatos további információkért olvassa el a következő cikkeket:

- [Az Azure Cosmos DB által kínált öt konzisztenciaszintekről magas szintű TLA + előírásai](https://github.com/Azure/azure-cosmos-tla)
- [A replikált adatok konzisztencia kifejtett keresztül Baseball (videó) Doug Terry által](https://www.youtube.com/watch?v=gluIh8zd26I)
- [A replikált adatok konzisztencia kifejtett keresztül Baseball (tanulmány) Doug Terry által](https://www.microsoft.com/en-us/research/publication/replicated-data-consistency-explained-through-baseball/?from=http%3A%2F%2Fresearch.microsoft.com%2Fpubs%2F157411%2Fconsistencyandbaseballreport.pdf)
- [Munkamenet garanciákat, kis mértékben konzisztens replikált adatok](https://dl.acm.org/citation.cfm?id=383631)
- [Konzisztencia kompromisszumot kínál a Modern elosztott adatbázis-rendszerek kialakítása: SAPKA a történetet csak egy részét képezi.](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
- [A gyakorlati részleges határozatképességére valószínűségi korlátozott frissesség (PBS)](https://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf)
- [Végül konzisztens – javított változat](https://www.allthingsdistributed.com/2008/12/eventually_consistent.html)

## <a name="next-steps"></a>További lépések

Az Azure Cosmos DB-ben konzisztenciaszintek kapcsolatos további információkért olvassa el a következő cikkeket:

* [Az alkalmazás a megfelelő konzisztenciaszint kiválasztása](consistency-levels-choosing.md)
* [Konzisztenciaszintek az Azure Cosmos DB API-k között](consistency-levels-across-apis.md)
* [Kompromisszumot kínál a különböző konzisztenciaszintet rendelkezésre állás és teljesítmény](consistency-levels-tradeoffs.md)
* [Az alapértelmezett konzisztenciaszint konfigurálása](how-to-manage-consistency.md#configure-the-default-consistency-level)
* [Bírálja felül az alapértelmezett konzisztenciaszint](how-to-manage-consistency.md#override-the-default-consistency-level)

