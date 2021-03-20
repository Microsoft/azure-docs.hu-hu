---
title: Az Azure hatáskörébe tartozó betekintő jelentések ismertetése
description: Ez a cikk az Azure hatáskörébe tartozó bepillantást ismerteti.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 12/02/2020
ms.openlocfilehash: d841fa336b2702a02f3215f97a6403217986d7e0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96553058"
---
# <a name="understand-insights-in-azure-purview"></a>Elemzések értelmezése az Azure Purview-ban

Ez a cikk áttekintést nyújt az Azure hatáskörébe tartozó információkról.

Az eredmények a hatáskörébe tartozó kulcsfontosságú alappillérek egyike. A szolgáltatás lehetővé teszi, hogy az ügyfelek egyetlen ablaktábla legyenek a katalógusba, és további célokat szolgálnak az adatforrás-rendszergazdák, az üzleti felhasználók, az adatkezelők, az adatkezelő és a biztonsági rendszergazdák számára. A hatáskörébe jelenleg a következő, a nyilvános előzetes verzióban elérhető bejelentési jelentések tartoznak.

## <a name="asset-insights"></a>Eszköz-bepillantást

Ez a jelentés egy madár szem nézetét jeleníti meg az adatkészletből, valamint a forrás típusa szerint történő eloszlást, besorolással, valamint a fájlméretek egyes méretei alapján. Ez a jelentés különböző típusú felhasználókat tartalmaz, akik a hatáskörébe tartozó fiókokat kezelhetik, és olyan vizsgálatokat vagy üzleti felhasználókat futtatnak, akiket érdekelheti, hogy hány eszköz létezik a szervezeti adatbirtokon belüli bizonyos besorolással. 

A jelentés széles körű betekintést nyújt a gráfok és a KPI-k használatával, és később részletesen ismerteti az olyan rendellenességeket, mint például a rossz helyen lévő fájlok. A jelentés emellett teljes körű felhasználói élményt is biztosít, ahol az ügyfél megtekintheti az adott besorolású eszközök számát, a forrás típusaik és a legfelső szintű mappák alapján lebonthatja az információkat, valamint megtekintheti az eszközök listáját a további vizsgálathoz.

## <a name="scan-insights"></a>Az észlelések vizsgálata

A jelentés lehetővé teszi, hogy a rendszergazdák megértsék a vizsgálatok általános állapotát – hány sikeres, hány sikertelen, hány megszakítás történt. Ez a jelentés az elmúlt hét nap vagy az utolsó 30 nap során a hatáskörébe tartozó fiókban végrehajtott vizsgálatokban állapot-frissítést ad.

A jelentés azt is lehetővé teszi, hogy a rendszergazdák mélyen elmerülhetnek, és megvizsgálják, hogy mely vizsgálatok sikertelenek és milyen típusú forrásokra. Ahhoz, hogy a felhasználók továbbra is megvizsgálják a vizsgálatokat, a jelentés segít a "források" felhasználói felületén navigálni a vizsgálati előzmények oldalára.

## <a name="glossary-insights"></a>Szószedet-áttekintés

Ez a jelentés az üzleti felhasználók és az adatközpontok számára ad jelentést a szószedetről. A felhasználók megtekinthetik ezt a jelentést a szószedeti kifejezések állapot szerinti eloszlásának megismerése érdekében, megtudhatja, hogy hány szószedeti feltétel van csatolva az eszközökhöz, és hogy hány még nincs csatlakoztatva egyetlen eszközhöz sem. Az üzleti felhasználók is megismerhetik a Szószedet feltételeinek teljességét. 

Ez a jelentés összefoglalja a legfontosabb elemeket, amelyeket egy üzleti felhasználónak vagy adatkezelőnek figyelnie kell, hogy teljes és használható szószedetet hozzon létre a saját szervezete számára. A felhasználók a "Szószedet elemzése" felület "Szószedet" felületén is megkereshetik a módosításokat egy adott Szószedet-kifejezésen.

## <a name="classification-insights"></a>Besorolási ismeretek

Ez a jelentés részletesen ismerteti a besorolási adatok helyét, a vizsgálat során talált besorolásokat, valamint a besorolt fájlok részletezését. Lehetővé teszi a biztonsági rendszergazdák számára, hogy megértsék a szervezeti adatközpontban található információk típusait. 

Az Azure-ban a besorolások hasonlók a tulajdonosi címkékhez, és az adatkészletben lévő adott típusú tartalmak megjelölésére és azonosítására szolgálnak.

A besorolási megállapítások jelentés segítségével azonosíthatja a tartalmat adott besorolásokkal, és megismerheti a szükséges műveleteket, például további biztonságot adhat hozzá a tárházhoz, vagy áthelyezheti a tartalmat egy biztonságosabb helyre.

További információkért lásd: [Az Azure hatáskörébe tartozó adatok besorolása](classification-insights.md).

## <a name="sensitivity-labeling-insights"></a>Az adatfelismerések érzékenységének címkézése

Ez a jelentés részletes információkat tartalmaz a vizsgálat során talált érzékenységi címkékről, valamint a címkével ellátott fájlok részletezéséről. Lehetővé teszi a biztonsági rendszergazdák számára a szervezeti adatközpontban található információk biztonságának biztosítását. 

Az Azure-ban az érzékenységi címkék segítségével azonosíthatók a besorolási típusok kategóriái, valamint az egyes kategóriákra alkalmazni kívánt biztonsági szabályzatok.

A címkézési megállapítások jelentés segítségével azonosíthatja a tartalomban található érzékenységi címkéket, és megismerheti a szükséges műveleteket, például a hozzáférés kezelését adott adattárakhoz vagy fájlokhoz.

További információ: az [Azure hatáskörébe tartozó adatok érzékenységének címkézése](sensitivity-insights.md).

## <a name="file-extension-insights"></a>Fájlkiterjesztés-felismerések

Ez a jelentés részletes információkat tartalmaz a fájlok bővítményeiről, illetve a vizsgálat során talált fájltípusokról, valamint a fájlokra vonatkozó részletezésről. 

A fájlkiterjesztés-elemzésekről szóló jelentés segítségével megtudhatja, hogy az egyes időpontokban hány fájl van, hol vannak a fájlok, és hogy bizalmas adatok leolvasható-e.

További információ: [fájlkiterjesztés-elemzések az Azure hatáskörébe tartozó adatokról](file-extension-insights.md).

## <a name="next-steps"></a>Következő lépések

* [Szószedet-áttekintés](glossary-insights.md)
* [Az észlelések vizsgálata](scan-insights.md)
* [Besorolási ismeretek](./classification-insights.md)