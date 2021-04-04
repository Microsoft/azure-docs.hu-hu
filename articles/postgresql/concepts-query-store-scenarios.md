---
title: Lekérdezés-tárolási forgatókönyvek – Azure Database for PostgreSQL – egyetlen kiszolgáló
description: Ez a cikk a Azure Database for PostgreSQL-Single Server lekérdezési tárolójának egyes forgatókönyveit ismerteti.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: ad646e39e9b6a87611dc2f40330a77fe70c92c27
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91710549"
---
# <a name="usage-scenarios-for-query-store"></a>A lekérdezési tároló használati forgatókönyvei

**A következőkre vonatkozik:** Azure Database for PostgreSQL – egykiszolgálós verzió: 9,6, 10, 11

A lekérdezési tároló számos olyan forgatókönyvben használható, amelyben kritikus fontosságú a kiszámítható számítási feladatok teljesítményének nyomon követése és karbantartása. Tekintse meg a következő példákat: 
- A legfontosabb költséges lekérdezések azonosítása és finomhangolása 
- A/B tesztelés 
- A teljesítmény stabil tartása a frissítések során 
- Ad hoc számítási feladatok azonosítása és javítása 

## <a name="identify-and-tune-expensive-queries"></a>Költséges lekérdezések azonosítása és finomhangolása 

### <a name="identify-longest-running-queries"></a>A leghosszabb ideig futó lekérdezések azonosítása 
Használja a Azure Portal [lekérdezési terheléselemző](concepts-query-performance-insight.md) nézetét a leghosszabb ideig futó lekérdezések gyors azonosításához. Ezek a lekérdezések általában jelentős mennyiségű erőforrást használnak. A leghosszabb ideig tartó kérdések optimalizálása a teljesítmény javítása érdekében a rendszeren futó egyéb lekérdezések által használt erőforrások felszabadításával növelheti a teljesítményt. 

### <a name="target-queries-with-performance-deltas"></a>Célzott lekérdezések teljesítménybeli különbözetekkel 
A lekérdezési tároló a teljesítményadatokat a Windows időben tárolja, így az idő múlásával nyomon követheti a lekérdezés teljesítményét. Ez segít azonosítani, hogy pontosan mely lekérdezések járulnak hozzá a teljes töltött idő növekedéséhez. Ennek eredményeképpen megteheti a számítási feladatok megkeresését.

### <a name="tuning-expensive-queries"></a>Költséges lekérdezések finomhangolása 
Ha az optimális teljesítményt biztosító lekérdezést azonosít, a művelet a probléma jellegétől függ: 
- A [teljesítménnyel kapcsolatos javaslatok](concepts-performance-recommendations.md) segítségével állapítsa meg, hogy vannak-e javasolt indexek. Ha igen, hozza létre az indexet, majd a Query Store használatával értékelje ki a lekérdezés teljesítményét az index létrehozása után. 
- Győződjön meg arról, hogy a lekérdezés által használt mögöttes táblák naprakészek a statisztikákban.
- Érdemes lehet költséges lekérdezéseket újraírni. Kihasználhatja például a lekérdezési paraméterezés előnyeit, és csökkentheti a dinamikus SQL használatát. Az Adatszűrés az adatbázis-oldalon való alkalmazásakor, például az alkalmazás oldalán nem alkalmazható, optimális logikát kell megvalósítani. 


## <a name="ab-testing"></a>A/B tesztelés 
A lekérdezési tároló használatával hasonlíthatja össze a számítási feladatok teljesítményét a bevezetéshez szükséges alkalmazások módosítása előtt és után. Példák a lekérdezési tároló használatára a környezet vagy az alkalmazás munkaterhelés-teljesítményre gyakorolt hatásának felméréséhez: 
- Egy alkalmazás új verziójának elkészítése. 
- További erőforrások hozzáadása a kiszolgálóhoz. 
- Hiányzó indexek létrehozása a költséges lekérdezések által hivatkozott táblákon. 
 
Ezen forgatókönyvek bármelyikében alkalmazza a következő munkafolyamatot: 
1. Futtassa a munkaterhelést a Query Store-ban, mielőtt a tervezett módosítással létrehozza a teljesítmény alaptervét. 
2. Alkalmazás-módosítás (ok) alkalmazása az ellenőrzött időpontban. 
3. A módosítás után folytassa a munkaterhelés futtatását a rendszer teljesítményének rendszerképének létrehozásához. 
4. Az eredmények összehasonlítása a módosítás előtt és után. 
5. Döntse el, hogy megtartja-e a módosítást vagy a visszaállítást. 


## <a name="identify-and-improve-ad-hoc-workloads"></a>Ad hoc számítási feladatok azonosítása és fejlesztése 
Bizonyos munkaterhelések nem rendelkeznek olyan domináns lekérdezésekkel, amelyek az alkalmazások teljes teljesítményének javítására használhatók. Ezek a számítási feladatok jellemzően viszonylag nagy számú egyedi lekérdezéssel vannak ellátva, amelyek mindegyike a rendszererőforrások egy részét felhasználja. A rendszer minden egyes egyedi lekérdezést ritkán hajt végre, ezért a futásidejű felhasználás nem kritikus. Másfelől, mivel az alkalmazás minden alkalommal új lekérdezéseket generál, a rendszererőforrások jelentős részét a lekérdezés fordítása tölti le, ami nem optimális. Ez általában akkor fordul elő, ha az alkalmazás lekérdezéseket hoz létre (a tárolt eljárások és a paraméteres lekérdezések használata helyett), vagy ha olyan objektum-összehasonlító leképezési keretrendszerekre támaszkodik, amelyek alapértelmezés szerint generálnak lekérdezéseket. 
 
Az alkalmazás kódjának ellenőrzésekor érdemes lehet átírni az adatelérési réteget a tárolt eljárások vagy a paraméteres lekérdezések használatára. Ez a helyzet azonban javítható az alkalmazások módosítása nélkül is, ha a lekérdezési paraméterezés a teljes adatbázisra (az összes lekérdezésre) vagy az ugyanazon lekérdezési kivonattal rendelkező különálló lekérdezési sablonokra kényszeríti. 

## <a name="next-steps"></a>Következő lépések
- További információ a [query Store használatának ajánlott eljárásairól](concepts-query-store-best-practices.md)