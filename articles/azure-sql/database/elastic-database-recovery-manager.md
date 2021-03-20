---
title: Recovery Manager a szegmenses Térkép problémáinak kijavításához
description: A Recoverymanager osztállyal osztály használata a szilánkokkal kapcsolatos térképek problémáinak megoldásához
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/03/2019
ms.openlocfilehash: 91bcd998849c619a328a198c97bb8c977b9d8232
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92792225"
---
# <a name="using-the-recoverymanager-class-to-fix-shard-map-problems"></a>Horizontális skálázási térképek javítása a RecoveryManager osztállyal
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

A [recoverymanager osztállyal](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager) osztály lehetővé teszi a ADO.NET-alkalmazások számára, hogy könnyen észlelje és javítsa a globális szegmenses Térkép (GSM) és a helyi szegmensek (LSM) közötti következetlenségeket a szilánkokra osztott adatbázis-környezetekben.

A GSM-és a LSM az egyes adatbázisok hozzárendelését egy horizontálisan eltárolt környezetben követheti nyomon. Alkalmanként a GSM és a LSM közötti szünet történik. Ebben az esetben használja a Recoverymanager osztállyal osztályt a törés észleléséhez és javításához.

A Recoverymanager osztállyal osztály a [Elastic Database ügyféloldali kódtár](elastic-database-client-library.md)része.

![Szegmenses Térkép][1]

A kifejezések definícióit lásd: [Elastic Database eszközök Szószedet](elastic-scale-glossary.md). Annak megismeréséhez, hogy a **ShardMapManager** hogyan használható a többrétegű megoldásokban lévő adatkezeléshez, tekintse meg a következőt: szegmenses [Térkép kezelése](elastic-scale-shard-map-management.md).

## <a name="why-use-the-recovery-manager"></a>Miért érdemes a Recovery Managert használni?

A többrétegű adatbázis-környezetekben az adatbázis egy bérlője, a kiszolgálón pedig sok adatbázis található. A környezetben számos kiszolgáló is lehet. Minden adatbázis le van képezve a szegmenses térképen, ezért a rendszer a megfelelő kiszolgálóhoz és adatbázishoz irányítja a hívásokat. Az adatbázisok követése egy horizontális Felskálázási **kulcs** szerint történik, és minden **szegmenshez** hozzá van rendelve egy tartomány. Egy horizontális Felskálázási kulcs például a "D" értékről az "F" értékre nevezheti az ügyfelek nevét. Az összes szegmens hozzárendelését (más néven adatbázisokat) és a leképezési tartományokat a globális szegmenses **Térkép (GSM)** tartalmazza. Minden adatbázis tartalmaz egy térképet is a szegmensben található tartományokból, amelyek a helyi szegmensek **(LSM)** néven ismertek. Amikor egy alkalmazás egy szegmenshez csatlakozik, a leképezést a rendszer a gyors lekéréshez az alkalmazással gyorsítótárazza. A LSM a gyorsítótárazott adatértékek ellenőrzésére szolgál.

Előfordulhat, hogy a GSM-és LSM a következő okok miatt nem lehet szinkronban:

1. Annak a szegmensnek a törlése, amelynek a tartománya már nem használatban van, vagy egy szegmens átnevezése nem lehetséges. A szegmensek törlése árva szegmensek **leképezését** eredményezi. Hasonlóképpen, az átnevezett adatbázis árva szegmensek leképezését is okozhatja. A változás céljától függően előfordulhat, hogy a szegmenst el kell távolítani, vagy frissíteni kell a szegmens helyét. A törölt adatbázisok helyreállításáról a [törölt adatbázis visszaállítása](recovery-using-backups.md)című cikkből tájékozódhat.
2. Földrajzi feladatátvételi esemény történik. A folytatáshoz frissítenie kell a kiszolgáló nevét és az adatbázis nevét az alkalmazásban, majd frissítenie kell a szegmensben lévő szegmensek összes szegmenséhez tartozó szegmens-hozzárendelés részleteit. Ha van földrajzi feladatátvétel, a helyreállítási logikának automatizáltnak kell lennie a feladatátvételi munkafolyamaton belül. A helyreállítási műveletek automatizálása lehetővé teszi a Geo-kompatibilis adatbázisok súrlódás nélküli kezelhetőségét, és elkerüli a manuális emberi műveleteket. Az adatbázisok helyreállításának lehetőségeiről az adatközpont meghibásodása esetén az [üzletmenet folytonossága](business-continuity-high-availability-disaster-recover-hadr-overview.md) és a vész- [helyreállítás](disaster-recovery-guidance.md)című témakörben olvashat.
3. Egy szilánk vagy a ShardMapManager-adatbázis egy korábbi időpontra van visszaállítva. A biztonsági másolatok használatával történő időpontra történő helyreállításról a [biztonsági másolatok használatával történő helyreállítással](recovery-using-backups.md)foglalkozó témakörben olvashat.

A Azure SQL Database Elastic Database eszközökről, a Geo-replikációról és a visszaállításról további információt a következő témakörben talál:

* [Áttekintés: felhőalapú üzletmenet-folytonossági és adatbázis-vész-helyreállítás SQL Database](business-continuity-high-availability-disaster-recover-hadr-overview.md)
* [Ismerkedés a rugalmas adatbázis-eszközökkel](elastic-scale-get-started.md)  
* [ShardMap-kezelés](elastic-scale-shard-map-management.md)

## <a name="retrieving-recoverymanager-from-a-shardmapmanager"></a>Recoverymanager osztállyal beolvasása egy ShardMapManager

Első lépésként hozzon létre egy Recoverymanager osztállyal-példányt. A [GetRecoveryManager metódus](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getrecoverymanager) visszaadja a helyreállítási kezelőt az aktuális [ShardMapManager](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager) -példányhoz. A szegmenses Térkép következetlenségének kezeléséhez először le kell kérnie az adott szegmens Térkép Recoverymanager osztállyal.

   ```java
    ShardMapManager smm = ShardMapManagerFactory.GetSqlShardMapManager(smmConnectionString,  
             ShardMapManagerLoadPolicy.Lazy);
             RecoveryManager rm = smm.GetRecoveryManager();
   ```

Ebben a példában a Recoverymanager osztállyal inicializálva van a ShardMapManager. A ShardMap tartalmazó ShardMapManager is már inicializálva van.

Mivel ez az alkalmazási kód manipulálja magát a szegmenses leképezést, a gyári metódusban használt hitelesítő adatoknak (az előző példában smmConnectionString) olyan hitelesítő adatoknak kell lenniük, amelyeknek írási és olvasási jogosultsággal kell rendelkezniük a kapcsolatok karakterlánca által hivatkozott GSM-adatbázishoz. Ezek a hitelesítő adatok általában eltérnek a kapcsolatoknak az Adatfüggő útválasztáshoz való megnyitásához használt hitelesítő adatoktól. További információ: [a hitelesítő adatok használata a rugalmas adatbázis-ügyfélben](elastic-scale-manage-credentials.md).

## <a name="removing-a-shard-from-the-shardmap-after-a-shard-is-deleted"></a>Szegmens eltávolítása a ShardMap a szegmens törlése után

A [DetachShard metódus](/previous-versions/azure/dn842083(v=azure.100)) leválasztja az adott szegmenst a szegmens térképről, és törli a szegmenshez társított leképezéseket.  

* A Location paraméter a leválasztott szegmens helye, különösen a kiszolgáló neve és az adatbázis neve.
* A shardMapName paraméter a szegmensek közötti Térkép neve. Erre csak akkor van szükség, ha ugyanaz a szegmenses Térkép-kezelő több szegmenses térképet kezel. Választható.

> [!IMPORTANT]
> Ezt a technikát csak akkor használja, ha biztos abban, hogy a frissített leképezés tartománya üres. A fenti metódusok nem ellenőrzik az áthelyezett tartomány adatait, ezért a legjobb megoldás a kód ellenőrzésének belefoglalása.

Ez a példa eltávolítja a szegmenseket a szegmens térképről.

   ```java
   rm.DetachShard(s.Location, customerMap);
   ```

A szegmensek leképezése a szegmensben lévő szegmens helyét tükrözi a GSM-szegmens törlése előtt. Mivel a szegmens törölve lett, feltételezhető, hogy ez szándékos volt, és a skálázási kulcs tartománya már nem használatban van. Ha nem, akkor a visszaállítási időpontot is végrehajthatja. a Szilánk visszaállítása egy korábbi időpontból. (Ebben az esetben tekintse át a következő szakaszt a szilánkok következetlenségének észleléséhez.) A helyreállításhoz lásd: [időponthoz való helyreállítás](recovery-using-backups.md).

Mivel a rendszer feltételezi, hogy az adatbázis-törlés szándékos volt, a végső felügyeleti törlési művelettel törölheti a bejegyzést a szegmensben a szegmensben lévő Térkép kezelőjében. Ez megakadályozza, hogy az alkalmazás véletlenül adatokat írjon egy nem várt tartományba.

## <a name="to-detect-mapping-differences"></a>A leképezési különbségek észlelése

A [DetectMappingDifferences metódus](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.detectmappingdifferences) kiválasztja és visszaadja az egyik szegmenses térképet (helyi vagy globális) az igazság forrásaként, és összehangolja mindkét szegmens térképének leképezéseit (GSM és LSM).

   ```java
   rm.DetectMappingDifferences(location, shardMapName);
   ```

* A *hely* a kiszolgáló nevét és az adatbázis nevét adja meg.
* A *shardMapName* paraméter a szegmensek közötti Térkép neve. Erre csak akkor van szükség, ha több szegmenses leképezést is kezelnek ugyanazon a kiosztott Térkép-kezelővel. Választható.

## <a name="to-resolve-mapping-differences"></a>A leképezési különbségek feloldása

A [ResolveMappingDifferences metódus](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.resolvemappingdifferences) kiválasztja az egyik szegmenses leképezést (helyi vagy globális) az igazság forrásaként, és összehangolja mindkét szegmens térképének leképezéseit (GSM és LSM).

   ```java
   ResolveMappingDifferences (RecoveryToken, MappingDifferenceResolution.KeepShardMapping);
   ```

* A *RecoveryToken* paraméter a GSM és az adott szegmens LSM közötti leképezések különbségeit sorolja fel.
* A [MappingDifferenceResolution enumerálás](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.mappingdifferenceresolution) a szegmens-hozzárendelések közötti különbség feloldására szolgál.
* A **MappingDifferenceResolution. KeepShardMapping** javasolt, hogy ha a LSM tartalmazza a pontos leképezést, ezért a szegmensben lévő leképezést kell használni. Ez általában abban az esetben történik, ha feladatátvétel van: a szegmens most egy új kiszolgálón található. Mivel a szegmenst először el kell távolítani a GSM-ről (a Recoverymanager osztállyal. DetachShard metódus használatával), a leképezés már nem létezik a GSM-kiszolgálón. Ezért a LSM újra létre kell hozni a szegmensek leképezésének újbóli létrehozásához.

## <a name="attach-a-shard-to-the-shardmap-after-a-shard-is-restored"></a>Szegmens csatolása a ShardMap egy szegmens visszaállítása után

A [AttachShard metódus](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.recovery.recoverymanager.attachshard) csatolja az adott szegmenst a szegmens térképhez. Ezután észleli a szegmensek közötti leképezések következetlenségeit, és frissíti a leképezéseket, hogy azok megfeleljenek a szegmens visszaállítási pontjának. A rendszer azt feltételezi, hogy az adatbázist a rendszer átnevezi, hogy tükrözze az eredeti adatbázisnevet (a szegmens visszaállítása előtt), mivel az időponthoz tartozó visszaállítás alapértelmezett értéke egy új adatbázis, amely az időbélyeggel van hozzáfűzve.

   ```java
   rm.AttachShard(location, shardMapName)
   ```

* A *Location* paraméter a csatolt szegmens kiszolgálójának és adatbázisának neve.
* A *shardMapName* paraméter a szegmensek közötti Térkép neve. Erre csak akkor van szükség, ha ugyanaz a szegmenses Térkép-kezelő több szegmenses térképet kezel. Választható.

Ez a példa egy olyan szegmenst helyez el a szegmenses térképhez, amely nemrég lett visszaállítva egy korábbi időpontból. Mivel a szegmens (azaz a LSM szegmensének leképezése) vissza lett állítva, a rendszer nem konzisztens a GSM-ben lévő szegmens bejegyzéssel. Ezen a mintakódon kívül a szegmens vissza lett állítva, és átnevezve lett az adatbázis eredeti nevére. A visszaállítás óta a rendszer feltételezi, hogy a LSM leképezése a megbízható leképezés.

   ```java
   rm.AttachShard(s.Location, customerMap);
   var gs = rm.DetectMappingDifferences(s.Location);
      foreach (RecoveryToken g in gs)
       {
       rm.ResolveMappingDifferences(g, MappingDifferenceResolution.KeepShardMapping);
       }
   ```

## <a name="updating-shard-locations-after-a-geo-failover-restore-of-the-shards"></a>Szegmensek helyeinek frissítése a szegmensek földrajzi feladatátvétele (visszaállítása) után

Ha földrajzi feladatátvételt végez, a másodlagos adatbázis írása elérhetővé válik, és az új elsődleges adatbázis lesz. A kiszolgáló neve és az adatbázis valószínűleg (a konfigurációtól függően) eltérő lehet az eredeti elsődlegestől. Ezért meg kell határozni a szegmenshez tartozó, a GSM-ben és a LSM lévő szegmens leképezési bejegyzéseit. Hasonlóképpen, ha az adatbázist egy másik névre vagy helyre állítja vissza, vagy egy korábbi időpontra, ez inkonzisztencia lehet a szegmenses térképeken. A szegmensek közötti Térkép-kezelő kezeli a nyitott kapcsolatok eloszlását a megfelelő adatbázissal. A eloszlás a szegmenses térképen található, valamint az alkalmazások kérésének céljaként szolgáló horizontális Felskálázási kulcs értékén alapul. A földrajzi feladatátvételt követően ezt az információt frissíteni kell a helyreállított adatbázis pontos kiszolgálójának nevével, az adatbázis nevével és a szegmens hozzárendelésével.

## <a name="best-practices"></a>Ajánlott eljárások

A földrajzi feladatátvétel és a helyreállítás általában az alkalmazások felhőalapú rendszergazdája által felügyelt, Azure SQL Database üzletmenet-folytonossági funkciókat szándékosan használó alkalmazás. Az üzletmenet folytonosságának megtervezése olyan folyamatokat, eljárásokat és mértékeket igényel, amelyek biztosítják, hogy az üzleti műveletek megszakítás nélkül folytatódnak. A Recoverymanager osztállyal osztály részeként elérhető metódusokat ebben a munkafolyamatban kell használni annak biztosítása érdekében, hogy a GSM és a LSM naprakészek legyenek az elvégzett helyreállítási művelet alapján. A GSM és a LSM a feladatátvételi esemény utáni pontos információinak megfelelően öt alapvető lépésből áll. Az ezen lépések végrehajtásához szükséges alkalmazási kód integrálható a meglévő eszközökbe és munkafolyamatba.

1. Kérje le a Recoverymanager osztállyal a ShardMapManager.
2. Válassza le a régi szegmenst a szegmens térképről.
3. Csatolja az új szegmenst a szegmens térképhez, beleértve az új szegmens helyét.
4. A GSM-és LSM közötti leképezésben észlelt következetlenségek észlelése.
5. Oldja meg a GSM és a LSM közötti különbségeket, és bízza meg a LSM.

Ez a példa a következő lépéseket hajtja végre:

1. Eltávolítja a szegmenseket a feladatátvételi esemény előtti szegmenseket tükröző szegmensből.
2. Szegmensek csatolása az új szegmenseket tükröző szegmenses térképhez (a "Configuration. SecondaryServer" paraméter az új kiszolgálónév, de ugyanaz az adatbázis neve).
3. Lekéri a helyreállítási jogkivonatokat, ha a GSM és az egyes szegmensek LSM közötti leképezési különbségeket észleli.
4. Oldja fel a következetlenségeket úgy, hogy megbízik az egyes szegmensek LSM.

   ```java
   var shards = smm.GetShards();
   foreach (shard s in shards)
   {
     if (s.Location.Server == Configuration.PrimaryServer)
         {
          ShardLocation slNew = new ShardLocation(Configuration.SecondaryServer, s.Location.Database);
          rm.DetachShard(s.Location);
          rm.AttachShard(slNew);
          var gs = rm.DetectMappingDifferences(slNew);
          foreach (RecoveryToken g in gs)
            {
               rm.ResolveMappingDifferences(g, MappingDifferenceResolution.KeepShardMapping);
            }
        }
    }
   ```

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/elastic-database-recovery-manager/recovery-manager.png