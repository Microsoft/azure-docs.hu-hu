---
title: Az Azure arc használatára képes SQL felügyelt példány funkciói és képességei
description: Az Azure arc használatára képes SQL felügyelt példány funkciói és képességei
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 9c42acb69e13cc1eb0fbba3fcafaec1451bc4d77
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97589219"
---
# <a name="features-and-capabilities-of-azure-arc-enabled-sql-managed-instance"></a>Az Azure arc használatára képes SQL felügyelt példány funkciói és képességei

Az Azure arc használatára képes SQL felügyelt példány közös programkódot oszt meg a SQL Server legújabb stabil verziójával. A szabványos SQL-nyelv, a lekérdezés-feldolgozás és az adatbázis-kezelési funkciók többsége azonos. A SQL Server és SQL Database, illetve az SQL felügyelt példányai között gyakori funkciók a következők:

- Nyelvi funkciók – [a flow nyelvi kulcsszavainak, a](/sql/t-sql/language-elements/control-of-flow) [kurzorok](/sql/t-sql/language-elements/cursors-transact-sql), [az adattípusok](/sql/t-sql/data-types/data-types-transact-sql), a [DML-utasítások](/sql/t-sql/queries/queries), [predikátumok](/sql/t-sql/queries/predicates), [sorszámok](/sql/relational-databases/sequence-numbers/sequence-numbers), [tárolt eljárások](/sql/relational-databases/stored-procedures/stored-procedures-database-engine)és [változók](/sql/t-sql/language-elements/variables-transact-sql)vezérlése.
- Adatbázis-funkciók [– automatikus hangolás (tervezési kényszer)](/sql/relational-databases/automatic-tuning/automatic-tuning), [változások követése](/sql/relational-databases/track-changes/about-change-tracking-sql-server), [adatbázis-rendezés](/sql/relational-databases/collations/set-or-change-the-database-collation), [tárolt adatbázisok](/sql/relational-databases/databases/contained-databases), [tárolt felhasználók](/sql/relational-databases/security/contained-database-users-making-your-database-portable), [adattömörítés](/sql/relational-databases/data-compression/data-compression), [adatbázis-konfigurációs beállítások](/sql/t-sql/statements/alter-database-scoped-configuration-transact-sql), [online indexelési műveletek](/sql/relational-databases/indexes/perform-index-operations-online), [particionálás](/sql/relational-databases/partitions/partitioned-tables-and-indexes)és [időbeli táblázatok](/sql/relational-databases/tables/temporal-tables) ([lásd az első lépéseket ismertető útmutatót](/sql/relational-databases/tables/getting-started-with-system-versioned-temporal-tables)).
- Biztonsági funkciók – [alkalmazás-szerepkörök](/sql/relational-databases/security/authentication-access/application-roles), [dinamikus adatmaszkolás](/sql/relational-databases/security/dynamic-data-masking) ([első lépések SQL Database dinamikus adatmaszkolás a Azure Portal](../../azure-sql/database/dynamic-data-masking-configure-portal.md)), a [sorok szintjének biztonsága](/sql/relational-databases/security/row-level-security)
- Többmodelles funkciók – [gráf feldolgozása](/sql/relational-databases/graphs/sql-graph-overview), [JSON-adattartalom](/sql/relational-databases/json/json-data-sql-server), [OpenXML](/sql/t-sql/functions/openxml-transact-sql), [térbeli](/sql/relational-databases/spatial/spatial-data-sql-server), [openjson utasítással](/sql/t-sql/functions/openjson-transact-sql)és [XML-indexek](/sql/t-sql/statements/create-xml-index-transact-sql).



[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="features-of-azure-arc-enabled-sql-managed-instance"></a>Az Azure arc használatára képes SQL felügyelt példány funkciói

###  <a name="rdbms-high-availability"></a><a name="RDBMSHA"></a> RDBMS magas rendelkezésre állása  
  
|Szolgáltatás|Azure Arc-kompatibilis, felügyelt SQL-példány|
|-------------|----------------|
|Naplóküldés|Yes| 
|Biztonsági másolatok tömörítése|Yes|
|Adatbázis-pillanatkép|Yes|
|Always On feladatátvevő fürt<sup>1</sup> . példánya| Nem alkalmazható. Hasonló funkciók érhetők el |
|Always On rendelkezésre állási csoportok<sup>2</sup>|HA a szolgáltatások tervezése megtörténik.|
|Alapszintű rendelkezésre állási csoportok <sup>2</sup>|HA a szolgáltatások tervezése megtörténik.|
|A replika minimális végrehajtási rendelkezésre állási csoportja <sup>2</sup>|HA a szolgáltatások tervezése megtörténik.|
|Fürt nélküli rendelkezésre állási csoport|Yes|
|Online oldal és fájl visszaállítása|Yes|
|Online indexelés|Yes|
|Visszafolytatható online index-Újraépítés|Yes|
|Online séma módosítása|Yes|
|Gyors helyreállítás|Yes|
|Tükrözött biztonsági másolatok|Yes|
|Gyors memória-és CPU-Hozzáadás|Yes|
|Titkosított biztonsági mentés|Yes|
|Hibrid biztonsági mentés az Azure-ba (biztonsági mentés az URL-címre)|Yes|

<sup>1</sup> abban a forgatókönyvben, ahol a pod meghibásodása van, egy új SQL felügyelt példány indul el, és újra csatolva lesz az adatait tartalmazó állandó kötethez. [További információ az állandó kötetek Kubernetes](https://kubernetes.io/docs/concepts/storage/persistent-volumes).

<sup>2</sup> a jövőbeli kiadások az AG képességeit fogják biztosítani 

###  <a name="rdbms-scalability-and-performance"></a><a name="RDBMSSP"></a> RDBMS méretezhetőség és teljesítmény  

| Szolgáltatás | Azure Arc-kompatibilis, felügyelt SQL-példány |
|--|--|
| Oszlopcentrikus | Yes |
| Nagyméretű objektumok bináris fájljai fürtözött oszlopcentrikus indexekben | Yes |
| Online nem fürtözött oszlopcentrikus index újraépítése | Yes |
| Memóriában tárolt OLTP | Yes |
| Állandó fő memória | Yes |
| Tábla-és indexelési particionálás | Yes |
| Adattömörítés | Yes |
| Resource Governor | Yes |
| Particionált tábla párhuzamossága | Yes |
| NUMA-Aware és nagyméretű oldal memóriája és puffer-tömb kiosztása | Yes |
| IO-erőforrás szabályozása | Yes |
| Késleltetett tartósság | Yes |
| Automatikus finomhangolás | Yes |
| Kötegelt módú adaptív illesztések | Yes |
| Kötegelt üzemmód memória-engedélyezési visszajelzés | Yes |
| Többutasításos táblázat értékű függvények egymással megszakított végrehajtása | Yes |
| Tömeges Beszúrás – tökéletesítések | Yes |

###  <a name="rdbms-security"></a><a name="RDBMSS"></a> RDBMS biztonság

| Szolgáltatás | Azure Arc-kompatibilis, felügyelt SQL-példány |
|--|--|
| Sorszintű biztonság | Yes |
| Always Encrypted | Yes |
| Biztonságos enklávékkal ellátott Always Encrypted | No |
| Dinamikus adatmaszkolás | Yes |
| Alapszintű naplózás | Yes |
| Részletes naplózás | Yes |
| Transzparens adatbázis-titkosítás | Yes |
| Felhasználó által definiált szerepkörök | Yes |
| Tartalmazott adatbázisok | Yes |
| Biztonsági másolatok titkosítása | Yes |

###  <a name="rdbms-manageability"></a><a name="RDBMSM"></a> RDBMS kezelhetősége  

| Szolgáltatás | Azure Arc-kompatibilis, felügyelt SQL-példány |
|--|--|
| Dedikált rendszergazdai kapcsolatok | Yes |
| PowerShell-parancsfájlok támogatása | Yes |
| Az adatrétegbeli alkalmazás-összetevő műveleteinek támogatása – kinyerés, üzembe helyezés, frissítés, törlés | Yes |
| Házirend-automatizálás (ellenőrzési ütemterv és módosítás) | Yes |
| Teljesítményadatokat gyűjtő | Yes |
| Normál teljesítményű jelentések | Yes |
| Útmutatók tervezése és a tervek befagyasztásának megtervezése | Yes |
| Indexelt nézetek közvetlen lekérdezése (a deexpand mutató használatával) | Yes |
| Automatikus indexelt nézetek karbantartása | Yes |
| Elosztott particionált nézetek | Yes |
| Párhuzamos indexelt műveletek | Yes |
| Indexelt nézet automatikus használata lekérdezés-optimalizáló használatával | Yes |
| Párhuzamos konzisztencia-ellenőrzés | Yes |

### <a name="programmability"></a><a name="Programmability"></a> Programozhatóság  

| Szolgáltatás | Azure Arc-kompatibilis, felügyelt SQL-példány |
|--|--|
| JSON | Igen |
| Lekérdezéstár | Yes |  |
| Historikus | Yes |  |
| Natív XML-támogatás | Yes |  |
| XML-indexelés | Yes |  |
| & UPSERT-képességek EGYESÍTÉSe | Yes |  |
| Dátum és idő adattípus | Yes |  |
| Honosítási támogatás | Yes |  |
| Teljes szöveges és szemantikai keresés | No |
| A nyelv meghatározása a lekérdezésben | Yes |  |
| Service Broker (üzenetküldés) | Yes |  |
| Transact-SQL-végpontok | Yes |  |
| Graph | Yes |  |
| Machine Learning Services | No |  |
| PolyBase | No |


### <a name="tools"></a>Eszközök

Az Azure arc használatára képes SQL felügyelt példány különböző adateszközöket támogat, amelyek segítségével kezelheti adatait.

| **Eszköz** | Azure Arc-kompatibilis, felügyelt SQL-példány|
| --- | --- | --- |
| Azure Portal <sup>1</sup> | No |
| Azure CLI | No |
| [Azure Data Studio](/sql/azure-data-studio/what-is) | Yes |
| Azure PowerShell | Yes |
| [BACPAC-fájl (exportálás)](/sql/relational-databases/data-tier-applications/export-a-data-tier-application) | Yes |
| [BACPAC-fájl (importálás)](/sql/relational-databases/data-tier-applications/import-a-bacpac-file-to-create-a-new-user-database) | Yes |
| [SQL Server Data Tools (SSDT)](/sql/ssdt/download-sql-server-data-tools-ssdt) | Yes |
| [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) | Yes |
| [SQL Server PowerShell](/sql/relational-databases/scripting/sql-server-powershell) | Yes |
| [SQL Server Profiler](/sql/tools/sql-server-profiler/sql-server-profiler) | Yes |

<sup>1</sup> a Azure Portal csak olvasható módban tekintheti meg az Azure arc használatára képes SQL felügyelt példányokat az előzetes verzió használatakor.


### <a name="unsupported-features--services"></a><a name="Unsupported"></a> Nem támogatott szolgáltatások & szolgáltatások

A következő szolgáltatások és szolgáltatások nem érhetők el az Azure arc használatára képes SQL felügyelt példányhoz. Ezeknek a szolgáltatásoknak a támogatása az idő múlásával egyre nagyobb mértékben engedélyezve lesz.

| Terület | Nem támogatott szolgáltatás vagy szolgáltatás |
|-----|-----|
| **Database engine** | Replikálás egyesítése |
| &nbsp; | A stretch DB |
| &nbsp; | Elosztott lekérdezés külső felek kapcsolataival |
| &nbsp; | Csatolt kiszolgálók a SQL Server és az Azure SQL-termékektől eltérő adatforrásokhoz |
| &nbsp; | Rendszer kiterjesztett tárolt eljárásai (XP_CMDSHELL stb.) |
| &nbsp; | Lefilé, FILESTREAM |
| &nbsp; | CLR-szerelvények EXTERNAL_ACCESS vagy nem biztonságos engedélyekkel |
| &nbsp; | Puffer-készlet kiterjesztése |
| **SQL Server Agent** |  Alrendszerek: CmdExec, PowerShell, üzenetsor-olvasó, SSIS, SSAS, SSRS |
| &nbsp; | Riasztások |
| &nbsp; | Felügyelt biztonsági mentés |
| **Magas rendelkezésre állás** | Adatbázis-tükrözés  |
| **Biztonság** | Bővíthető kulcskezelő |
| &nbsp; | AD-hitelesítés csatolt kiszolgálók esetén | 
| &nbsp; | AD-hitelesítés a rendelkezésre állási csoportokhoz (AGs) | 