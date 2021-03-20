---
title: Erőforrások áthelyezése új régióba
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Megtudhatja, hogyan helyezheti át az adatbázist vagy a felügyelt példányt egy másik régióba.
services: sql-database
ms.service: sql-db-mi
ms.subservice: data-movement
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 06/25/2019
ms.openlocfilehash: ae6c87c9eabea837ba9c43676d4ca712caa385cb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94594164"
---
# <a name="move-resources-to-new-region---azure-sql-database--azure-sql-managed-instance"></a>Erőforrások áthelyezése új régióba – Azure SQL Database & Azure SQL felügyelt példánya
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Ez a cikk általános munkafolyamatot mutat be az adatbázis vagy a felügyelt példány új régióba való áthelyezéséhez.

## <a name="overview"></a>Áttekintés

Különböző helyzetekben érdemes áthelyezni a meglévő adatbázist vagy felügyelt példányt az egyik régióból a másikba. Például bővítheti vállalatát egy új régióra, és optimalizálni szeretné az új ügyfélkörre. Vagy a műveleteket egy másik régióba kell áthelyeznie megfelelőségi okokból. Az Azure új régiót adott ki, amely jobb közelséget biztosít, és javítja a felhasználói élményt.  

Ez a cikk általános munkafolyamatot biztosít az erőforrások másik régióba való áthelyezéséhez. A munkafolyamat a következő lépésekből áll:

1. Ellenőrizze az áthelyezés előfeltételeit.
1. Felkészülés az erőforrások hatókörbe való áthelyezésére.
1. Az előkészítési folyamat figyelése.
1. Az áthelyezési folyamat tesztelése.
1. Kezdeményezzen tényleges áthelyezést.
1. Távolítsa el az erőforrásokat a forrás régióból.

> [!NOTE]
> Ez a cikk az Azure nyilvános felhőben vagy ugyanazon a szuverén felhőben található áttelepítésekre vonatkozik.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="move-a-database"></a>Adatbázis áthelyezése

### <a name="verify-prerequisites"></a>Előfeltételek ellenőrzése

1. Hozzon létre egy célkiszolgáló minden forráskiszolgálón.
1. Konfigurálja a tűzfalat a megfelelő kivételekkel a [PowerShell](scripts/create-and-configure-database-powershell.md)használatával.  
1. Konfigurálja a kiszolgálókat a megfelelő bejelentkezésekkel. Ha nem Ön az előfizetés rendszergazdája vagy az SQL Server rendszergazdája, akkor a rendszergazdával együttműködve rendelje hozzá a szükséges engedélyeket. További információ: [a Azure SQL Database biztonság kezelése](active-geo-replication-security-configure.md)a vész-helyreállítás után.
1. Ha az adatbázisok transzparens adattitkosítással vannak titkosítva, és a Azure Key Vault saját titkosítási kulcsát használja, ügyeljen arra, hogy a megfelelő titkosítási anyagok legyenek kiépítve a célcsoportokban. További információ: [Azure SQL transzparens adattitkosítás az ügyfél által felügyelt kulcsokkal Azure Key Vault](transparent-data-encryption-byok-overview.md).
1. Ha az adatbázis-szintű naplózás engedélyezve van, tiltsa le, és engedélyezze a kiszolgálói szintű naplózást. A feladatátvételt követően az adatbázis-szintű naplózáshoz szükség van a régiók közötti forgalomra, amely az áthelyezés után nem szükséges vagy lehetséges.
1. A kiszolgálói szintű auditok esetében ügyeljen a következőre:
   - A rendszer áthelyezi a tárolót, Log Analytics vagy az Event hub-t a meglévő naplókba.
   - A naplózás konfigurálva van a célkiszolgálón. További információ: Ismerkedés [a SQL Database naplózással](../../azure-sql/database/auditing-overview.md).
1. Ha a példány hosszú távú adatmegőrzési szabályzattal (LTR) rendelkezik, a meglévő LTR biztonsági mentések továbbra is az aktuális kiszolgálóhoz lesznek társítva. Mivel a célkiszolgáló eltérő, a forrás-régióban lévő régebbi LTR biztonsági mentéseket a forráskiszolgáló használatával is elérheti, még akkor is, ha a kiszolgáló törölve lett.

      > [!NOTE]
      > Ez nem lesz elegendő a szuverén felhő és a nyilvános régió közötti váltáshoz. Ilyen áttelepítés esetén a LTR biztonsági mentéseket a célkiszolgálón kell áthelyezni, amely jelenleg nem támogatott.

### <a name="prepare-resources"></a>Erőforrások előkészítése

1. Hozzon létre egy [feladatátvételi csoportot](failover-group-add-single-database-tutorial.md#2---create-the-failover-group) a forrás kiszolgálója és a cél kiszolgálója között.  
1. Adja hozzá azokat az adatbázisokat, amelyeket át szeretne helyezni a feladatátvételi csoportba.
  
    A rendszer automatikusan kezdeményezi az összes hozzáadott adatbázis replikálását. További információ: [ajánlott eljárások az önálló adatbázisokkal rendelkező feladatátvételi csoportok használatához](auto-failover-group-overview.md#best-practices-for-sql-database).

### <a name="monitor-the-preparation-process"></a>Az előkészítési folyamat figyelése

Rendszeres időközönként meghívhatja a [Get-AzSqlDatabaseFailoverGroup-](/powershell/module/az.sql/get-azsqldatabasefailovergroup) t az adatbázisok replikációjának figyelésére a forrásról a célra. A kimeneti objektum `Get-AzSqlDatabaseFailoverGroup` tartalmazza a **ReplicationState** tulajdonságát:

- A **ReplicationState = 2** (CATCH_UP) azt jelzi, hogy az adatbázis szinkronizálva van, és a rendszer biztonságosan feladatátvételt hajt végre.
- **ReplicationState = 0** (előkészítés) – azt jelzi, hogy az adatbázis még nincs bevezetve, és a feladatátvételi kísérlet sikertelen lesz.

### <a name="test-synchronization"></a>Szinkronizálás tesztelése

A **ReplicationState** után `2` minden adatbázishoz vagy adatbázis-részhalmazhoz kapcsolódhat a másodlagos végpont használatával, `<fog-name>.secondary.database.windows.net` és bármilyen lekérdezést végrehajthat az adatbázisokon a kapcsolat, a megfelelő biztonsági konfiguráció és az adatreplikáció biztosítása érdekében.

### <a name="initiate-the-move"></a>Az áthelyezés kezdeményezése

1. Kapcsolódjon a célkiszolgálóra a másodlagos végpont használatával `<fog-name>.secondary.database.windows.net` .
1. A [switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) használatával váltson át a másodlagos felügyelt példányra, hogy az elsődleges legyen teljes szinkronizálással. A művelet sikeres lesz, vagy visszaállítja.
1. Győződjön meg arról, hogy a parancs sikeresen befejeződött a használatával `nslook up <fog-name>.secondary.database.windows.net` annak megállapításához, hogy a DNS-CNAME bejegyzés a célként megadott régió IP-címére mutat. Ha a Switch parancs sikertelen, a CNAME nem frissül.

### <a name="remove-the-source-databases"></a>A forrás-adatbázisok eltávolítása

Az áthelyezés befejeződése után távolítsa el a forrás régió erőforrásait a szükségtelen költségek elkerülése érdekében.

1. Törölje a feladatátvételi csoportot a [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup)használatával.
1. Törölje az összes forrásoldali adatbázist a [Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase) használatával a forráskiszolgálón található összes adatbázishoz. Ekkor a rendszer automatikusan leállítja a Geo-replikációs hivatkozásokat.
1. A [Remove-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver)használatával törölje a forráskiszolgáló-kiszolgálót.
1. Távolítsa el a Key vaultot, a naplózási tárolókat, az Event hub-t, az Azure Active Directory (Azure AD-példányt) és a többi függő erőforrást, hogy ne kelljen fizetniük.

## <a name="move-elastic-pools"></a>Rugalmas készletek áthelyezése

### <a name="verify-prerequisites"></a>Előfeltételek ellenőrzése

1. Hozzon létre egy célkiszolgáló minden forráskiszolgálón.
1. Konfigurálja a tűzfalat a megfelelő kivételekkel a [PowerShell](scripts/create-and-configure-database-powershell.md)használatával.
1. Konfigurálja a kiszolgálókat a megfelelő bejelentkezésekkel. Ha nem Ön az előfizetés rendszergazdája vagy a kiszolgáló rendszergazdája, akkor a rendszergazdával együttműködve rendelje hozzá a szükséges engedélyeket. További információ: [a Azure SQL Database biztonság kezelése](active-geo-replication-security-configure.md)a vész-helyreállítás után.
1. Ha az adatbázisok transzparens adattitkosítással vannak titkosítva, és a Azure Key Vault saját titkosítási kulcsát használja, ügyeljen arra, hogy a megfelelő titkosítási anyagok legyenek kiépítve a céltartományban.
1. Hozzon létre egy cél rugalmas készletet minden forrás rugalmas készlethez, és győződjön meg arról, hogy a készlet ugyanabban a szolgáltatási szinten jön létre, ugyanazzal a névvel és ugyanazzal a mérettel.
1. Ha engedélyezve van az adatbázis-szintű naplózás, tiltsa le, és engedélyezze a kiszolgálói szintű naplózást. A feladatátvételt követően az adatbázis-szintű naplózáshoz a régión kívüli forgalom szükséges, amely nem kívánatos, vagy lehetséges az áthelyezés után is.
1. A kiszolgálói szintű auditok esetében ügyeljen a következőre:
    - A rendszer áthelyezi a tárolót, Log Analytics vagy az Event hub-t a meglévő naplókba.
    - A naplózási konfiguráció a célkiszolgálón van konfigurálva. További információ: [SQL Database naplózás](../../azure-sql/database/auditing-overview.md).
1. Ha a példány hosszú távú adatmegőrzési szabályzattal (LTR) rendelkezik, a meglévő LTR biztonsági mentések továbbra is az aktuális kiszolgálóhoz lesznek társítva. Mivel a célkiszolgáló eltérő, a forrás-régióban lévő régebbi LTR biztonsági mentéseket a forráskiszolgáló használatával érheti el, még akkor is, ha a kiszolgáló törölve lett.

      > [!NOTE]
      > Ez nem lesz elegendő a szuverén felhő és a nyilvános régió közötti váltáshoz. Ilyen áttelepítés esetén a LTR biztonsági mentéseket a célkiszolgálón kell áthelyezni, amely jelenleg nem támogatott.

### <a name="prepare-to-move"></a>Felkészülés az áthelyezésre

1. Hozzon létre egy külön [feladatátvételi csoportot](failover-group-add-elastic-pool-tutorial.md#3---create-the-failover-group) a forráskiszolgáló minden rugalmas készlete és a hozzá tartozó, a célkiszolgálón lévő, a fogadó fél rugalmas készlete között.
1. Adja hozzá a készletben lévő összes adatbázist a feladatátvételi csoporthoz.

    A rendszer automatikusan kezdeményezi a hozzáadott adatbázisok replikálását. További információ: [ajánlott eljárások a rugalmas készletekkel rendelkező feladatátvételi csoportokhoz](auto-failover-group-overview.md#best-practices-for-sql-database).

      > [!NOTE]
      > Habár több rugalmas készletet tartalmazó feladatátvételi csoportot is létrehozhat, javasoljuk, hogy mindegyik készlethez hozzon létre egy külön feladatátvételi csoportot. Ha nagy számú adatbázist használ több rugalmas készletben, amelyeket át kell helyeznie, akkor párhuzamosan futtathatja az előkészítési lépéseket, majd elindíthatja az áthelyezés lépést párhuzamosan. Ez a folyamat jobb skálázást tesz lehetővé, és kevesebb időt vesz igénybe, mint a több rugalmas készlet ugyanabban a feladatátvételi csoportban.

### <a name="monitor-the-preparation-process"></a>Az előkészítési folyamat figyelése

Rendszeres időközönként meghívhatja a [Get-AzSqlDatabaseFailoverGroup-](/powershell/module/az.sql/get-azsqldatabasefailovergroup) t az adatbázisok replikációjának figyelésére a forrásról a célra. A kimeneti objektum `Get-AzSqlDatabaseFailoverGroup` tartalmazza a **ReplicationState** tulajdonságát:

- A **ReplicationState = 2** (CATCH_UP) azt jelzi, hogy az adatbázis szinkronizálva van, és a rendszer biztonságosan feladatátvételt hajt végre.
- **ReplicationState = 0** (előkészítés) – azt jelzi, hogy az adatbázis még nincs bevezetve, és a feladatátvételi kísérlet sikertelen lesz.

### <a name="test-synchronization"></a>Szinkronizálás tesztelése

Ha **ReplicationState** `2` , kapcsolódjon az egyes adatbázisokhoz vagy adatbázisokhoz a másodlagos végpont használatával, `<fog-name>.secondary.database.windows.net` és hajtson végre bármilyen lekérdezést az adatbázisokon a kapcsolat, a megfelelő biztonsági konfiguráció és az adatreplikáció biztosítása érdekében.

### <a name="initiate-the-move"></a>Az áthelyezés kezdeményezése

1. Kapcsolódjon a célkiszolgálóra a másodlagos végpont használatával `<fog-name>.secondary.database.windows.net` .
1. A [switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) használatával váltson át a másodlagos felügyelt példányra, hogy az elsődleges legyen teljes szinkronizálással. A művelet vagy sikeres lesz, vagy visszaállítja.
1. Győződjön meg arról, hogy a parancs sikeresen befejeződött a használatával `nslook up <fog-name>.secondary.database.windows.net` annak megállapításához, hogy a DNS-CNAME bejegyzés a célként megadott régió IP-címére mutat. Ha a Switch parancs sikertelen, a CNAME nem frissül.

### <a name="remove-the-source-elastic-pools"></a>A forrás rugalmas készletek eltávolítása

Az áthelyezés befejeződése után távolítsa el a forrás régió erőforrásait a szükségtelen költségek elkerülése érdekében.

1. Törölje a feladatátvételi csoportot a [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup)használatával.
1. Törölje az összes forrás rugalmas készletet a forráskiszolgálón a [Remove-AzSqlElasticPool](/powershell/module/az.sql/remove-azsqlelasticpool)használatával.
1. A [Remove-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver)használatával törölje a forráskiszolgáló-kiszolgálót.
1. Távolítsa el a Key vaultot, a naplózási tárolókat, az Event hub-t, az Azure AD-példányt és az egyéb függő erőforrásokat, hogy ne kelljen fizetniük.

## <a name="move-a-managed-instance"></a>Felügyelt példány áthelyezése

### <a name="verify-prerequisites"></a>Előfeltételek ellenőrzése

1. Az egyes forrásokkal felügyelt példányok esetében hozzon létre egy célként megadott méretű SQL felügyelt példány példányát a célként megadott régióban.  
1. A hálózat konfigurálása felügyelt példányhoz. További információ: [hálózati konfiguráció](../managed-instance/how-to-content-reference-guide.md#network-configuration).
1. Konfigurálja a célként szolgáló főkiszolgáló-adatbázist a megfelelő bejelentkezésekkel. Ha nem Ön az előfizetés vagy az SQL felügyelt példányának rendszergazdája, akkor a rendszergazdával együttműködve rendelje hozzá a szükséges engedélyeket.
1. Ha az adatbázisok transzparens adattitkosítással vannak titkosítva, és saját titkosítási kulcsot használnak a Azure Key Vaultban, győződjön meg arról, hogy az azonos titkosítási kulccsal rendelkező Azure Key Vault a forrás-és a célként megadott régiókban is megtalálható. További információ: [transzparens adattitkosítás az ügyfél által felügyelt kulcsokkal Azure Key Vaultban](transparent-data-encryption-byok-overview.md).
1. Ha a felügyelt példány naplózása engedélyezve van, ügyeljen arra, hogy:
    - A rendszer áthelyezi a tárolási tárolót vagy az Event hub-t a meglévő naplókba.
    - A naplózás konfigurálva van a cél példányon. További információ: [naplózás az SQL felügyelt példányával](../managed-instance/auditing-configure.md).
1. Ha a példány hosszú távú adatmegőrzési szabályzattal (LTR) rendelkezik, a meglévő LTR biztonsági mentések továbbra is az aktuális példánnyal lesznek társítva. Mivel a célként megadott példány különbözik, a forrás-régióban lévő régebbi LTR biztonsági mentéseket a forrásoldali példány használatával érheti el, még akkor is, ha a példány törölve van.

  > [!NOTE]
  > Ez nem lesz elegendő a szuverén felhő és a nyilvános régió közötti váltáshoz. Ilyen áttelepítés esetén a LTR biztonsági mentéseket a célként megadott példányra kell helyezni, amely jelenleg nem támogatott.

### <a name="prepare-resources"></a>Erőforrások előkészítése

Hozzon létre egy feladatátvételi csoportot az egyes források által felügyelt példányok és az SQL felügyelt példányának megfelelő példánya között.

Az egyes példányokon lévő összes adatbázis replikációja automatikusan megkezdődik. További információ: [automatikus feladatátvételi csoportok](auto-failover-group-overview.md).

### <a name="monitor-the-preparation-process"></a>Az előkészítési folyamat figyelése

Rendszeres időközönként meghívhatja a [Get-AzSqlDatabaseFailoverGroup-](/powershell/module/az.sql/get-azsqldatabasefailovergroup) t az adatbázisok replikációjának figyelésére a forrásról a célra. A kimeneti objektum `Get-AzSqlDatabaseFailoverGroup` tartalmazza a **ReplicationState** tulajdonságát:

- A **ReplicationState = 2** (CATCH_UP) azt jelzi, hogy az adatbázis szinkronizálva van, és a rendszer biztonságosan feladatátvételt hajt végre.
- A **ReplicationState = 0** (beültetés) azt jelzi, hogy az adatbázis még nincs felszámítva, és a feladatátvételi kísérlet sikertelen lesz.

### <a name="test-synchronization"></a>Szinkronizálás tesztelése

Ha **ReplicationState** `2` , kapcsolódjon az egyes adatbázisokhoz vagy az adatbázisok egy részhalmazához a másodlagos végpont használatával, `<fog-name>.secondary.database.windows.net` és hajtson végre bármilyen lekérdezést az adatbázisokon a kapcsolat, a megfelelő biztonsági konfiguráció és az adatreplikáció biztosítása érdekében.

### <a name="initiate-the-move"></a>Az áthelyezés kezdeményezése

1. Kapcsolódjon a cél felügyelt példányhoz a másodlagos végpont használatával `<fog-name>.secondary.database.windows.net` .
1. A [switch-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/switch-azsqldatabasefailovergroup) használatával váltson át a másodlagos felügyelt példányra, hogy az elsődleges legyen teljes szinkronizálással. A művelet sikeres lesz, vagy visszaállítja.
1. Győződjön meg arról, hogy a parancs sikeresen befejeződött a használatával `nslook up <fog-name>.secondary.database.windows.net` annak megállapításához, hogy a DNS-CNAME bejegyzés a célként megadott régió IP-címére mutat. Ha a Switch parancs sikertelen, a CNAME nem frissül.

### <a name="remove-the-source-managed-instances"></a>A forrás felügyelt példányainak eltávolítása

Az áthelyezés befejeződése után távolítsa el a forrás régió erőforrásait a szükségtelen költségek elkerülése érdekében.

1. Törölje a feladatátvételi csoportot a [Remove-AzSqlDatabaseFailoverGroup](/powershell/module/az.sql/remove-azsqldatabasefailovergroup)használatával. Ezzel elkerüli a feladatátvételi csoport konfigurációját, és leállítja a Geo-replikációs kapcsolatokat a két példány között.
1. Törölje a forrás felügyelt példányt a [Remove-AzSqlInstance](/powershell/module/az.sql/remove-azsqlinstance)használatával.
1. Távolítsa el az erőforráscsoport további erőforrásait, például a virtuális fürtöt, a virtuális hálózatot és a biztonsági csoportot.

## <a name="next-steps"></a>Következő lépések

Az adatbázis [kezelése](manage-data-after-migrating-to-database.md) az áttelepítés után.
