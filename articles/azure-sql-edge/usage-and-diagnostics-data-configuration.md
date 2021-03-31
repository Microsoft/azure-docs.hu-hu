---
title: Az Azure SQL Edge használati és diagnosztikai adatok konfigurálása
description: Megtudhatja, hogyan konfigurálhatja a használati és diagnosztikai adatokat az Azure SQL Edge-ben.
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 08/04/2020
ms.openlocfilehash: 81b2aabbae148faec06c9ab420bdfecde475b4bb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97605001"
---
# <a name="azure-sql-edge-usage-and-diagnostics-data-configuration"></a>Az Azure SQL Edge használati és diagnosztikai adatok konfigurálása

Alapértelmezés szerint az Azure SQL Edge adatokat gyűjt arról, hogy az ügyfelek hogyan használják az alkalmazást. Az Azure SQL Edge az üzembe helyezési élményről, a használatról és a teljesítményről gyűjt információkat. Ez az információ segít a Microsoftnak a termék fejlesztésében, hogy jobban megfeleljen az ügyfelek igényeinek. A Microsoft például információt gyűjt arról, hogy milyen típusú hibakódok vannak az ügyfelek számára, hogy javítsuk a kapcsolódó hibákat, az Azure SQL Edge használatának módjáról szóló dokumentációt, valamint annak meghatározását, hogy a szolgáltatásokat hozzá kell-e adni a termékhez az ügyfelek jobb kiszolgálásához.

Pontosabban, a Microsoft nem küldi el a következő típusú információkat a mechanizmuson keresztül:

- A felhasználói táblákban található értékek.
- Bármely bejelentkezési hitelesítő adat vagy egyéb hitelesítési adat.
- A személyes vagy az ügyféladatok.

Az alábbi példa a funkciók használati adatait tartalmazza, amelyek segítenek a termék fejlesztésében.

Alább látható egy példa a használati és diagnosztikai adatok gyűjtéséhez használt lekérdezésekre. A lekérdezés az Azure SQL Edge-ben használt különböző adatfolyam-adatforrások számát és típusát azonosítja. Ezek az információk segítik a Microsoftot abban, hogy azonosítsa, hogy mely adatfolyam-adatforrásokat használják általában, így a Microsoft javíthatja az adatforrásokhoz kapcsolódó teljesítményt és felhasználói élményt. 

```sql
select 
count(*) as [count], sum(inputs) as inputs, sum(outputs) as outputs, sum(linked_to_job) 
as linked_to_job, data_source_type
from ( 
select isnull(value,'unknown') as data_source_type, inputs, outputs, linked_to_job
from 
    ( 
        select 
        convert(sysname, lower(substring(ds.location, 0, charindex('://', ds.location))), 1) as data_source_type, 
        isnull(inputs, 0) as inputs, isnull(outputs, 0) as outputs, isnull(js.stream_id/js.stream_id, 0) as linked_to_job 
        from sys.external_streams es
        join sys.external_data_sources ds 
             on es.data_source_id = ds.data_source_id
        left join 
            ( 
            select stream_id, max(cast(is_input as int)) inputs, max(cast(is_output as int)) outputs 
            from sys.external_job_streams group by stream_id 
            ) js
             on js.stream_id = es.object_id 
    ) ds
left join 
    (
        select value from string_split('edgehub,sqlserver,kafka', ',')) as known_ep on data_source_type = value 
    ) known_ds
group by data_source_type
```

## <a name="disable-usage-and-diagnostic-data-collection"></a>Használati és diagnosztikai adatok gyűjtésének letiltása

Az Azure SQL Edge használati és diagnosztikai adatok gyűjtése az alábbi módszerek bármelyikével letiltható.

> [!NOTE]
> A fejlesztői verzióban nem lehet letiltani a használati és diagnosztikai adatokat.

### <a name="disable-usage-and-diagnostics-using-environment-variables"></a>A használat és a diagnosztika letiltása környezeti változók használatával

Ha le szeretné tiltani a használati és diagnosztikai adatok gyűjtését az Azure SQL Edge-ben, adja hozzá a következő környezeti változót, és állítsa be a értékét `*False*` . Az Azure SQL Edge környezeti változók használatával történő konfigurálásával kapcsolatos további információkért tekintse meg a [Konfigurálás környezeti változók használatával](configure.md#configure-by-using-environment-variables)című témakört.

`MSSQL_TELEMETRY_ENABLED = TRUE | FALSE`

- TRUE – lehetővé teszi a használati és diagnosztikai adatok gyűjtését. Ez az alapértelmezett beállítás.
- FALSE (hamis) – letiltja a használati és diagnosztikai adatok gyűjtését.

### <a name="disable-usage-and-diagnostics-using-mssqlconf-file"></a>A használat és a diagnosztika letiltása az MSSQL. conf fájllal

Ha le szeretné tiltani a használati és diagnosztikai adatok gyűjtését az Azure SQL Edge-ben, adja hozzá a következő sorokat az MSSQL. conf fájlhoz az SQL Edge-modul/var/opt/MSSQL/mappájához rendelt állandó tárolóeszközön. Az Azure SQL Edge az MSSQL. conf fájllal történő konfigurálásával kapcsolatos további információkért tekintse meg az [MSSQL. conf fájl használatával történő konfigurálást](configure.md#configure-by-using-an-mssqlconf-file)ismertető témakört.

```ini
[telemetry]
customerfeedback = false
```

## <a name="local-audit-of-usage-and-diagnostic-data-collection"></a>A használati és diagnosztikai adatok gyűjtésének helyi naplózása

Az Azure SQL Edge használati és diagnosztikai adatgyűjtés helyi naplózási összetevője a szolgáltatás által gyűjtött adatokat egy kijelölt mappába írhatja, amely a Microsoft számára elküldhető adatokat (naplókat) jelenti. A helyi audit célja, hogy az ügyfelek lássák, hogy a Microsoft milyen adatokat gyűjt a szolgáltatással, megfelelőségi, szabályozási vagy adatvédelmi okokból.

### <a name="enable-local-audit-of-usage-and-diagnostics-data"></a>A használati és diagnosztikai adatok helyi naplózásának engedélyezése

A helyi naplózás használatának és a diagnosztikai adatoknak az Azure SQL Edge-ben való engedélyezése

1. Hozzon létre egy célként megadott könyvtárat az új helyi naplózási naplófájlok tárolásához. Ez a célkönyvtár lehet a gazdagépen vagy a tárolón belül. Az alábbi példában a cél könyvtár ugyanabban a csatlakoztatási kötetben jön létre, amely az SQL Edge/var/opt/MSSQL/elérési útjára van leképezve.

   ```bash
   sudo mkdir <host mount path>/audit
   ```

2. A használati és diagnosztikai adatok naplózásának beállítása környezeti változók vagy MSSQL. conf fájl használatával.

   - Környezeti változók használata – adja hozzá a következő környezeti változót az SQL Edge-telepítéshez, és adja meg a naplófájlok célmappáját.
   
     `*MSSQL_TELEMETRY_DIR = <host mount path>/audit*`
   
   - Az MSSQL. conf fájl használata – adja hozzá a következő sorokat az MSSQL. conf fájlhoz, és adja meg a naplófájlok célmappáját.
       ```ini
       [telemetry]
       userrequestedlocalauditdirectory  = <host mount path>/audit
       ```  

## <a name="next-steps"></a>Következő lépések

- [Kapcsolódás az Azure SQL Edge-hez](connect.md)
- [Teljes körű IoT-megoldás kiépítése az SQL Edge használatával](tutorial-deploy-azure-resources.md)
