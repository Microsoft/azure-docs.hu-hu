---
title: Biztonsági mentés és visszaállítás – Pillanatképek, Geo-redundáns
description: Ismerje meg, hogyan működik a Backup és a Restore funkció az Azure szinapszis Analytics dedikált SQL-készletében. A biztonsági másolatok használatával visszaállíthatja az adattárházat az elsődleges régióban található visszaállítási pontra. A Geo-redundáns biztonsági mentések használatával egy másik földrajzi régióba állíthatja vissza a visszaállítást.
services: synapse-analytics
author: joannapea
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 11/13/2020
ms.author: joanpo
ms.reviewer: igorstan
ms.custom: seo-lt-2019"
ms.openlocfilehash: 0fa6777dc5b587150f630ed8ccc110d16448cc21
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104602247"
---
# <a name="backup-and-restore-in-azure-synapse-dedicated-sql-pool"></a>Biztonsági mentés és visszaállítás az Azure szinapszis dedikált SQL-készletében

Ismerje meg, hogyan használhatja a biztonsági mentést és a visszaállítást az Azure szinapszis dedikált SQL-készletében. A dedikált SQL Pool visszaállítási pontok használatával helyreállíthatja vagy átmásolhatja az adattárházat az elsődleges régió korábbi állapotába. Az adatraktár geo-redundáns biztonsági másolatai segítségével egy másik földrajzi régióba állíthatja vissza a visszaállítást.

## <a name="what-is-a-data-warehouse-snapshot"></a>Mi az az adatraktár-pillanatkép

Az *adatraktár-pillanatkép* egy visszaállítási pontot hoz létre, amellyel helyreállíthatja vagy átmásolhatja az adattárházat egy korábbi állapotba.  Mivel a dedikált SQL Pool egy elosztott rendszer, az adatraktár-pillanatkép számos, az Azure Storage szolgáltatásban található fájlból áll. A pillanatképek rögzítik az adattárházban tárolt adatok növekményes módosításait.

Az *adatraktár-visszaállítás* egy új adattárház, amely egy meglévő vagy törölt adattárház visszaállítási pontjából lett létrehozva. Az adattárház visszaállítása fontos része az üzletmenet folytonosságának és a vész-helyreállítási stratégiának, mivel a véletlen sérülés vagy törlés után újból létrehozza az adatait. Az adatraktár emellett hatékony mechanizmust biztosít az adattárház másolatának tesztelési vagy fejlesztési célokra történő létrehozásához. A dedikált SQL Pool-visszaállítási díjak a forrás és a cél adatraktár adatbázisának méretétől és helyétől függően változhatnak.

## <a name="automatic-restore-points"></a>Automatikus visszaállítási pontok

A pillanatképek egy beépített szolgáltatás, amely visszaállítási pontokat hoz létre. Nem kell engedélyeznie ezt a funkciót. A dedikált SQL-készletnek azonban aktív állapotban kell lennie a visszaállítási pont létrehozásához. Ha a felfüggesztés gyakran szünetel, előfordulhat, hogy az Automatikus visszaállítási pontok nem hozhatók létre, ezért a dedikált SQL-készlet szüneteltetése előtt hozzon létre felhasználó által definiált visszaállítási pontot. A felhasználók jelenleg nem törölhetik az Automatikus visszaállítási pontokat, mivel a szolgáltatás ezeket a visszaállítási pontokat használja a SLA-nak a helyreállításhoz való fenntartásához.

Az adattárház pillanatképeit a rendszer a nap folyamán hozza létre a hét napig elérhető visszaállítási pontok létrehozásához. Ez a megőrzési időszak nem módosítható. A dedikált SQL-készlet támogatja a 8 órás helyreállítási időkorlátot (RPO). Az adattárházat az elsődleges régióban állíthatja vissza az elmúlt hét napban készített Pillanatképek közül.

Ha meg szeretné tekinteni, hogy mikor indította el az utolsó pillanatképet, futtassa ezt a lekérdezést az online dedikált SQL-készleten.

```sql
select   top 1 *
from     sys.pdw_loader_backup_runs
order by run_id desc
;
```

## <a name="user-defined-restore-points"></a>Felhasználó által definiált visszaállítási pontok

Ez a funkció lehetővé teszi a pillanatképek manuális indítását az adattárház visszaállítási pontjainak létrehozásához a nagy módosítások előtt és után. Ez a funkció biztosítja, hogy a visszaállítási pontok logikailag konzisztensek legyenek, ami további adatvédelmet biztosít a munkaterhelés-megszakítások vagy a gyors helyreállítási időpontra vonatkozó felhasználói hibák esetén. A felhasználó által definiált visszaállítási pontok hét napig érhetők el, és automatikusan törlődnek az Ön nevében. A felhasználó által definiált visszaállítási pontok megőrzési ideje nem módosítható. **42 a felhasználó által definiált visszaállítási pontok** bármely időpontban garantáltak, így azokat [törölni](/powershell/module/azurerm.sql/remove-azurermsqldatabaserestorepoint) kell egy másik visszaállítási pont létrehozása előtt. A pillanatképek elindításával felhasználó által definiált visszaállítási pontokat hozhat létre a [PowerShell](/powershell/module/az.sql/new-azsqldatabaserestorepoint?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.jsont#examples) vagy a Azure Portal használatával.

> [!NOTE]
> Ha 7 napnál hosszabb visszaállítási pontra van szüksége, szavazzon erre a [lehetőségre](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/35114410-user-defined-retention-periods-for-restore-points). A felhasználó által definiált visszaállítási pontot és a visszaállítást az újonnan létrehozott visszaállítási pontról egy új adattárházra is létrehozhatja. A visszaállítást követően a dedikált SQL-készlet online állapotba kerül, és határozatlan ideig szüneteltetheti a számítási költségek megtakarítását. A szüneteltetett adatbázis az Azure szinapszis Storage-adatforgalmi díjait terheli. Ha a visszaállított adattárház aktív példányára van szüksége, akkor folytathatja, hogy a rendszer csak néhány percet vesz igénybe.

### <a name="restore-point-retention"></a>Visszaállítási pont megőrzése

A következő felsorolás a visszaállítási pontok megőrzési időszakait ismerteti:

1. A dedikált SQL-készlet törli a visszaállítási pontot, amikor eléri a 7 napos megőrzési időszakot **, és** ha legalább 42 teljes visszaállítási pont van (beleértve a felhasználó által definiált és az Automatikust is).
2. A rendszer nem veszi figyelembe a pillanatképeket, ha egy dedikált SQL-készlet szüneteltetve van.
3. A visszaállítási pontok korát a visszaállítási ponttól számított abszolút naptári napok határozzák meg, beleértve az SQL-készlet szüneteltetését.
4. A dedikált SQL-készlet bármely időpontban garantált, hogy akár 42 felhasználó által definiált visszaállítási pontot és 42 Automatikus visszaállítási pontot tud tárolni, feltéve, hogy ezek a visszaállítási pontok nem értek el a 7 napos megőrzési időszakot.
5. Ha pillanatképet készít, a dedikált SQL-készletet a rendszer 7 napnál hosszabb ideig szünetelteti, majd folytatja, a visszaállítási pont mindaddig megmarad, amíg 42 teljes visszaállítási pont (beleértve a felhasználó által definiált és az automatikus frissítést is)

### <a name="snapshot-retention-when-a-sql-pool-is-dropped"></a>Pillanatkép-megőrzés egy SQL-készlet eldobásakor

Egy dedikált SQL-készlet eldobásakor a rendszer a végleges pillanatképet hozza létre és hét napig menti. A dedikált SQL-készletet a törléskor létrehozott utolsó visszaállítási pontra állíthatja vissza. Ha a dedikált SQL-készlet szüneteltetett állapotban van, a rendszer nem készít pillanatképet. Ebben az esetben ügyeljen arra, hogy egy felhasználó által definiált visszaállítási pontot hozzon létre a dedikált SQL-készlet eldobása előtt.

> [!IMPORTANT]
> Ha törli a dedikált SQL-készletet futtató kiszolgálót vagy munkaterületet, a rendszer a kiszolgálóhoz/munkaterülethez tartozó összes adatbázist is törli, és nem állítható helyre. Törölt kiszolgálót nem lehet visszaállítani.

## <a name="geo-backups-and-disaster-recovery"></a>Geo – biztonsági mentések és vész-helyreállítás

A rendszer naponta egyszer hozza létre a Geo-biztonsági mentést egy [párosított adatközpontba](../../best-practices-availability-paired-regions.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). A Geo-visszaállítás RPO 24 óra. Visszaállíthatja a Geo-biztonsági mentést bármely más régióban lévő kiszolgálóra, ahol a dedikált SQL-készlet támogatott. A földrajzi biztonsági mentés lehetővé teszi az adatraktár visszaállítását abban az esetben, ha az elsődleges régióban nem fér hozzá a visszaállítási pontokhoz.

Ha nem igényel geo-biztonsági mentést a dedikált SQL-készlethez, letilthatja őket, és mentheti a vész-helyreállítási tárolási költségeket. Ehhez tekintse meg a [útmutató: Geo-biztonsági mentések letiltása DEDIKÁLT SQL-készlethez (korábban SQL DW) című témakört](disable-geo-backup.md). Vegye figyelembe, hogy ha letiltja a Geo-biztonsági mentést, nem fogja tudni helyreállítani a dedikált SQL-készletet a párosított Azure-régióba, ha az elsődleges Azure-adatközpont nem érhető el. 

> [!NOTE]
> Ha rövidebb RPO van szüksége a földrajzi biztonsági mentéshez, akkor [itt](https://feedback.azure.com/forums/307516-sql-data-warehouse)is szavazzon erre a képességre. A felhasználó által definiált visszaállítási pontot és a visszaállítást az újonnan létrehozott visszaállítási pontról egy másik régióban lévő új adattárházra is létrehozhatja. Miután visszaállította az adattárházat, a szolgáltatás online állapotba kerül, és határozatlan ideig szüneteltetheti a számítási költségek megtakarítását. A szüneteltetett adatbázis az Azure-Premium Storage díjszabása szerint terheli az adattárolási díjat. Ha az adatraktár aktív példányára van szüksége, folytathatja a folytatást, amely mindössze néhány percet vesz igénybe.

## <a name="data-residency"></a>Adattárolási hely 

Ha a párosított adatközpont a földrajzi határán kívül található, gondoskodhat arról, hogy az adatai a földrajzi határán belül maradnak, ha a Geo-redundáns tárolást választja. Ezt akkor teheti meg, ha dedikált SQL-készletet (korábban SQL DW) épít ki a Geo-redundáns tárolási lehetőséggel egy dedikált SQL-készlet (korábban SQL DW) létrehozásakor vagy visszaállításakor. 

Annak ellenőrzéséhez, hogy a párosított adatközpont egy másik országban található-e, tekintse meg az [Azure párosított régiókat](../../best-practices-availability-paired-regions.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json).

## <a name="backup-and-restore-costs"></a>Biztonsági mentési és visszaállítási költségek

Megfigyelheti, hogy az Azure-számla tartalmaz egy sort a Storage szolgáltatáshoz, valamint egy sor tételt a vész-helyreállítási tároláshoz. A tárolási díj az elsődleges régióban tárolt adattárolás teljes költsége, valamint a pillanatképek által rögzített növekményes változások. A pillanatképek felszámolásának részletes ismertetését lásd: a  [Pillanatképek felmerülésének ismertetése](/rest/api/storageservices/Understanding-How-Snapshots-Accrue-Charges?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json). A Geo-redundáns díj magában foglalja a Geo-biztonsági mentések tárolásának költségeit.  

Az elsődleges adattárház teljes díja és a pillanatképek változásának hét napja a legközelebbi TB-ra lesz kerekítve. Ha például az adattárház 1,5 TB-os, és a pillanatképek a 100 GB-ot rögzítik, akkor az Azure Premium Storage díjszabása 2 TB-os adatforgalmi díjat számítunk fel.

Ha földrajzilag redundáns tárolást használ, külön tárolási díjat kap. A Geo-redundáns tárterület számlázása a standard Read-Access földrajzilag redundáns tárolás (RA-GRS) alapján történik.

Az Azure szinapszis díjszabásával kapcsolatos további információkért lásd: az [Azure szinapszis díjszabása](https://azure.microsoft.com/pricing/details/sql-data-warehouse/gen2/). A régiókban való visszaállításkor nem számítunk fel díjat a kimenő adatforgalomért.

## <a name="restoring-from-restore-points"></a>Visszaállítás visszaállítási pontokból

Mindegyik pillanatkép egy visszaállítási pontot hoz létre, amely a pillanatkép elindításának időpontját jelöli. Az adatraktár visszaállításához válasszon egy visszaállítási pontot, és adja meg a visszaállítási parancsot.  

Megtarthatja a visszaállított adattárházat és az aktuálisat, illetve törölheti is az egyiket. Ha az aktuális adattárházat a visszaállított adattárházra szeretné cserélni, átnevezheti azt az [Alter Database](/sql/t-sql/statements/alter-database-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) PARANCCSAL a név módosítása lehetőséggel.

Az adatraktár visszaállításáról további információt [a DEDIKÁLT SQL-készlet visszaállítása](sql-data-warehouse-restore-points.md#create-user-defined-restore-points-through-the-azure-portal)című témakörben talál.

A törölt vagy szüneteltetett adattárházak visszaállításához [létrehozhat egy támogatási jegyet](sql-data-warehouse-get-started-create-support-ticket.md).

## <a name="cross-subscription-restore"></a>Több előfizetés visszaállítása

Ha közvetlenül kell visszaállítani az előfizetést, szavazzon erre a [képességre](https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/36256231-enable-support-for-cross-subscription-restore). Állítsa vissza egy másik kiszolgálóra, és [Helyezze](../../azure-resource-manager/management/move-resource-group-and-subscription.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) át a kiszolgálót az előfizetések között a több előfizetés visszaállításának végrehajtásához.

## <a name="geo-redundant-restore"></a>Geo-redundáns visszaállítás

A [DEDIKÁLT SQL-készletet](sql-data-warehouse-restore-from-geo-backup.md#restore-from-an-azure-geographical-region-through-powershell) bármely olyan régióra visszaállíthatja, amely támogatja a dedikált SQL-készletet a kiválasztott teljesítményszint alapján.

> [!NOTE]
> A Geo-redundáns visszaállítás végrehajtásához nem kell kijelölnie ezt a funkciót.

## <a name="next-steps"></a>Következő lépések

A visszaállítási pontokkal kapcsolatos további információkért lásd: [felhasználó által definiált visszaállítási pontok](sql-data-warehouse-restore-points.md)