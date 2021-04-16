---
title: Lekérdezéstár-forgatókönyvek – Azure Database for PostgreSQL – Flex Server
description: Ez a cikk a Lekérdezéstár néhány forgatókönyvét ismerteti a Azure Database for PostgreSQL - Flex Serverben.
author: ssen-msft
ms.author: ssen
ms.service: postgresql
ms.topic: conceptual
ms.date: 04/01/2021
ms.openlocfilehash: ac0374cf91229e7f1b3f84d967d9825e2a3090f2
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107559153"
---
# <a name="usage-scenarios-for-query-store---flexible-server"></a>Használati forgatókönyvek a rugalmas lekérdezéstárhoz

**A következőkre vonatkozik:** Azure Database for PostgreSQL – 11-es, 12-es verziójú egykiszolgálós verzió

A lekérdezéstárat számos olyan forgatókönyvben használhatja, ahol a kiszámítható számítási feladatok teljesítményének nyomon követése és fenntartása kritikus fontosságú. Tekintse meg a következő példákat: 
- A legnagyobb költséges lekérdezések azonosítása és finomhangolása 
- A/B tesztelés 
- A teljesítmény stabilitása a frissítések során 
- Az alkalmi számítási feladatok azonosítása és javítása 

## <a name="identify-and-tune-expensive-queries"></a>Költséges lekérdezések azonosítása és hangolása 

### <a name="identify-longest-running-queries"></a>A leghosszabb ideig futó lekérdezések azonosítása 
A kiszolgáló azure_sys lekérdezéstárnézeteit használva gyorsan azonosíthatja a leghosszabb ideig futó lekérdezéseket. Ezek a lekérdezések általában a legtöbb erőforrást használják. A leghosszabb ideig futó lekérdezések optimalizálása javíthatja a teljesítményt, ha felszabadítja a rendszeren futó más lekérdezések által használt erőforrásokat. 

### <a name="target-queries-with-performance-deltas"></a>Céllekérdezések teljesítmény-változásokkal 
A Lekérdezéstár időablakok szerint szeleteli a teljesítményadatokat, így nyomon követheti egy lekérdezés teljesítményét az idő során. Ez segít azonosítani, hogy pontosan mely lekérdezések járulnak hozzá a teljes idő növeléséhez. Ennek eredményeképpen célzott hibaelhárítást is futtathat a számítási feladaton.

### <a name="tuning-expensive-queries"></a>Drága lekérdezések finomhangolása 
Ha egy optimálisnál rosszabb teljesítményű lekérdezést azonosít, a művelet a probléma jellegétől függ: 
- Győződjön meg arról, hogy a lekérdezés által használt mögöttes táblák statisztikái naprakészek.
- Fontolja meg a költséges lekérdezések újraírását. Használja ki például a lekérdezésparaméterezés előnyeit, és csökkentse a dinamikus SQL használatát. Optimális logikát valósíthat meg az adatok olvasása során, például adatszűrés alkalmazása adatbázisoldalon, nem pedig alkalmazásoldalon. 


## <a name="ab-testing"></a>A/B tesztelés 
A Lekérdezéstár használatával összehasonlíthatja a számítási feladatok teljesítményét a migrálás előtt és után bevezetni vagy azt megelőzően vagy azt követően módosítani tervez alkalmazásokat. Példaforgatókönyvek a Lekérdezéstár használatával a számítási feladatok teljesítményére gyakorolt hatás felméréséhez: 
- Migrálás PostgreSQL-verziók között. 
- Egy alkalmazás új verziójának létrehozása. 
- További erőforrások hozzáadása a kiszolgálóhoz. 
- Hiányzó indexek létrehozása költséges lekérdezések által hivatkozott táblákon. 
- Migrálás egykiszolgálós kiszolgálóról a Flex Serverre. 
 
A fenti forgatókönyvek bármelyikében alkalmazza a következő munkafolyamatot: 
1. Futtassa a számítási feladatot a Lekérdezéstár használatával a tervezett módosítás előtt a teljesítmény-alapkonfiguráció létrehozásához. 
2. Alkalmazásváltozás(ak) alkalmazása a szabályozott időpontban. 
3. Folytassa a számítási feladat futtatását elég ideig ahhoz, hogy a módosítás után teljesítményképet generáljon a rendszerről. 
4. Hasonlítsa össze a módosítás előtti és utáni eredményeket. 
5. Döntse el, hogy megtartja-e a változtatást vagy a visszaállítást. 


## <a name="identify-and-improve-ad-hoc-workloads"></a>Alkalmi számítási feladatok azonosítása és javítása 
Egyes számítási feladatok nem domináns lekérdezésekkel vannak, amelyek finomhangolásával javítható az alkalmazás általános teljesítménye. Ezeket a számítási feladatokat jellemzően viszonylag nagy számú egyedi lekérdezés jellemzi, amelyek a rendszererőforrások egy részét használják fel. A rendszer ritkán hajt végre minden egyedi lekérdezést, így a futásidejű használatuk nem kritikus fontosságú. Másrészt, mivel az alkalmazás mindig új lekérdezéseket generál, a rendszererőforrások jelentős részét lekérdezés-fordításra költi, ami nem optimális. Ez a helyzet általában akkor fordul elő, ha az alkalmazás lekérdezéseket hoz létre (tárolt eljárások vagy paraméteres lekérdezések használata helyett), vagy ha olyan objektumrelációs leképezési keretrendszerekre támaszkodik, amelyek alapértelmezés szerint lekérdezéseket hoznak létre. 
 
Ha Ön az alkalmazáskód vezérlése alatt áll, érdemes lehet újraírni az adatelérési réteget, hogy tárolt eljárásokat vagy paraméteres lekérdezéseket használjon. Ez a helyzet azonban az alkalmazás módosítása nélkül is javítható, ha a lekérdezésparaméterezést a teljes adatbázisra (az összes lekérdezésre) vagy az azonos lekérdezési kivonattal megadott lekérdezési sablonokra kényszeríti. 

## <a name="next-steps"></a>Következő lépések
- További információ a [Lekérdezéstár használatának ajánlott eljárásairól](concepts-query-store-best-practices.md)
