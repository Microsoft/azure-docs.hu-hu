---
title: A teljesítményre vonatkozó ajánlott eljárások és konfigurációs irányelvek – Azure SQL Edge
description: Ismerje meg az Azure SQL Edge teljesítményének bevált gyakorlatait és konfigurációs irányelveit
keywords: SQL Edge, adatmegőrzés
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 02f22883a0989714d8b74f778cacf1ba2c65d0b4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93392010"
---
# <a name="performance-best-practices-and-configuration-guidelines"></a>A teljesítményre vonatkozó ajánlott eljárások és konfigurációs irányelvek

Az Azure SQL Edge számos funkciót és képességet kínál, amelyek használatával javítható az SQL Edge-környezet teljesítménye. Ez a cikk néhány ajánlott eljárást és javaslatot tartalmaz a teljesítmény maximalizálása érdekében. 

## <a name="best-practices"></a>Ajánlott eljárások 

### <a name="configure-multiple-tempdb-data-files"></a>Több tempdb-adatfájl konfigurálása

Az Azure SQL Edge alapértelmezés szerint csak egy tempdb-adatfájlt hoz létre a tároló inicializálásának részeként. Javasoljuk, hogy hozzon létre több tempdb-adatfájlt az üzembe helyezés után. További információkért tekintse meg a cikk útmutatását, [amely a SQL Server tempdb-adatbázis foglalásának csökkentését ajánlja](https://support.microsoft.com/help/2154845/recommendations-to-reduce-allocation-contention-in-sql-server-tempdb-d).

### <a name="use-clustered-columnstore-indexes-where-possible"></a>Fürtözött oszlopcentrikus indexek használata, ahol lehetséges

A IoT és az Edge-eszközök általában nagy mennyiségű adat előállítására szolgálnak, amelyeket általában egy kis idő alatt összesítenek az elemzéshez. Az egyes adatsorokat ritkán használják az elemzésekhez. A oszlopcentrikus indexek ideálisak az ilyen nagyméretű adathalmazok tárolásához és lekérdezéséhez. Ez az index az oszlop alapú adattárolást és a lekérdezések feldolgozását használja, hogy a lekérdezési teljesítmény akár 10 alkalommal is elérhető legyen a hagyományos, sorokra épülő tárolón. Akár 10 alkalommal is elérheti az adattömörítést a tömörítetlen adatmérethez képest. További információ: [Oszlopcentrikus indexek](/sql/relational-databases/indexes/columnstore-indexes-overview)

Emellett az Azure SQL Edge egyéb funkciói, például az adatátviteli és adatmegőrzési előnyök az adatok beszúrását és az adatok eltávolítását ismertető oszlopcentrikus-optimalizálásokból származnak. 

### <a name="simple-recovery-model"></a>Egyszerű helyreállítási modell

Mivel a tárterület az Edge-eszközökön korlátozható, az Azure SQL Edge összes felhasználói adatbázisa alapértelmezés szerint az egyszerű helyreállítási modellt használja. Az egyszerű helyreállítási modell automatikusan visszaállítja a naplózási helyet, hogy a lemezterület-követelmények kicsik maradjanak, ami lényegében nem szükséges a tranzakciós napló területének kezeléséhez. A korlátozott tárterülettel rendelkező peremhálózati eszközökön ez hasznos lehet. Az egyszerű helyreállítási modellel és más elérhető helyreállítási modellekkel kapcsolatos további információkért lásd: [helyreállítási modellek](/sql/relational-databases/backup-restore/recovery-models-sql-server)

Az egyszerű helyreállítási modell nem támogatja az olyan műveleteket, mint például a naplók szállítása és az időponthoz tartozó visszaállítások, amelyek a tranzakciónapló biztonsági mentését igénylik.  

## <a name="advanced-configuration"></a>Speciális konfiguráció 

### <a name="setting-memory-limits"></a>Memória korlátainak beállítása

Az Azure SQL Edge akár 64 GB memóriát is támogat a pufferek számára, míg az SQL Edge-tárolón belül futó műholdas folyamatok további memóriát igényelhetnek. A korlátozott memóriával rendelkező kisebb peremhálózati eszközökön célszerű korlátozni az SQL Edge-tárolók számára elérhető memóriát, így a Docker-gazdagép és más peremhálózati folyamatok vagy modulok megfelelően működhetnek. Az SQL Edge számára elérhető teljes memória a következő mechanizmusok használatával vezérelhető. 

- Az SQL Edge-tárolók számára elérhető memória korlátozása: az SQL Edge-tároló számára elérhető teljes memória a tároló futásidejű konfigurációs beállításával korlátozható `--memory` . További információ az SQL Edge-tároló számára elérhető memória korlátozásáról: [futtatókörnyezeti beállítások memóriával, processzorokkal és GPU](https://docs.docker.com/config/containers/resource_constraints/)-k használatával.

- A tárolón belüli SQL-folyamat számára elérhető memória korlátozása: alapértelmezés szerint a tárolóban lévő SQL-folyamat az elérhető fizikai memória 80%-át használja. Az üzemelő példányok többsége esetén az alapértelmezett konfiguráció rendben lesz. Előfordulhat azonban, hogy az adatátvitelhez és az SQL Edge-tárolókban futó ONNX folyamatokhoz további memória szükséges. Ilyen esetekben az SQL-folyamat számára elérhető memória az `memory.memorylimitmb` MSSQL-conf fájl beállításával szabályozható. További információ: configure az [MSSQL. conf fájllal](configure.md#configure-by-using-an-mssqlconf-file).

A memória korlátjának beállításakor ügyeljen arra, hogy ne legyen túl alacsony az érték. Ha nem állít be elég memóriát az SQL-folyamathoz, akkor az SQL-teljesítményt súlyosan befolyásolhatja.

### <a name="delayed-durability"></a>Késleltetett tartósság

Az Azure SQL Edge-beli tranzakció lehet teljesen tartós, a SQL Server alapértelmezett vagy késleltetett tartós (más néven lusta véglegesítés).

A teljes körű tranzakció-véglegesítés szinkronban van, és a sikeres végrehajtást jelenti, és csak azután küldi vissza a vezérlést az ügyfélnek, miután a tranzakcióhoz tartozó naplóbejegyzéseket lemezre írja. A késleltetett tartós tranzakciós véglegesítés aszinkron módon történik, és a rendszer sikeresen bejelenti a véglegesítés sikerességét, mielőtt a tranzakció rekordjai lemezre íródnak. Ahhoz, hogy a tranzakciók tartósak legyenek, a tranzakciós napló bejegyzéseinek lemezre írása szükséges. A késleltetett tartós tranzakciók tartósak lesznek, ha a tranzakciónapló-bejegyzéseket lemezre ürítik. 

Az üzemelő példányokban, ahol **egyes adatvesztések** tolerálható, illetve a lassú tárolású peremhálózati eszközökön a késleltetett tartósság az adatok betöltését és az adatmegőrzési alapú tisztítás optimalizálására használható. További információ: a [tranzakciók tartósságának szabályozása](/sql/relational-databases/logs/control-transaction-durability).


### <a name="linux-os-configurations"></a>Linux operációsrendszer-konfigurációk 

Érdemes lehet a következő [Linux operációs rendszer konfigurációs](/sql/linux/sql-server-linux-performance-best-practices#linux-os-configuration) beállításait használni az SQL-telepítés legjobb teljesítményének megtapasztalása érdekében.