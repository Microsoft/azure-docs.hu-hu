---
title: Helyreállítási pontok kezelése
description: Ismerje meg, hogyan kezeli a Azure Backup szolgáltatás a virtuális gépek helyreállítási pontjait
ms.topic: conceptual
ms.date: 11/08/2020
ms.openlocfilehash: 256df693aba0f799c24bcba6defe846e5c37ccaa
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94428630"
---
# <a name="manage-recovery-points"></a>Helyreállítási pontok kezelése

Ez a cikk azt ismerteti, hogyan működik a megőrzés a virtuális gépeken. Amikor a biztonsági másolatok történnek, a helyreállítási pontok jönnek létre, amelyekről a visszaállítási műveletek végezhetők.

Virtuális gépek esetén a kezdeti biztonsági mentés egy teljes biztonsági mentés, a következő biztonsági másolatok pedig növekményes biztonsági másolatok.

## <a name="recovery-points-and-retention"></a>Helyreállítási pontok és adatmegőrzés

### <a name="scheduled-initial-and-incremental-backup"></a>Ütemezett kezdeti és növekményes biztonsági mentés

Tegyük fel, hogy a virtuális gép *v1* egyszerűsített példája egy négy blokkból álló adatlemez: 1. blokk, 2. blokk, 3. blokk, 4. blokk. Mindegyik blokk 16 KB méretű.

![Virtuális gép négy blokktal](./media/manage-recovery-points/four-blocks.png)

**1. lépés – kezdeti biztonsági mentés:** A kezdeti biztonsági mentés teljes biztonsági mentés. Alapkonfigurációként működik, amelyen a további növekményes biztonsági mentések lesznek alkalmazva. Tegyük fel, hogy az 1. és a 2. blokkba írt adatforrások a forrásoldali virtuális gépen vannak. Ugyanazokat az adattárakat a rendszer a Recovery Services tároló tárterületén a D1-es és a D2-ként fogja replikálni.

![A kezdeti biztonsági mentés replikálódik](./media/manage-recovery-points/initial-backup.png)

**2. lépés – növekményes biztonsági mentés 1:** Vegye figyelembe, hogy a virtuális gép 3. blokkjában új adatmennyiség van hozzáadva. Ugyanazokat az adatfájlokat a rendszer a következő növekményes biztonsági mentéskor replikálja, és csak a módosított blokkot D3-ként tárolja a rendszer.  Az egyes lépések során még akkor is, ha a blokk 1 KB-a változik, a rendszer feltölti a teljes 16 KB-os blokkot a helyreállítási pontba.

![Első növekményes biztonsági mentés](./media/manage-recovery-points/first-incremental-backup.png)

**3. lépés – növekményes biztonsági mentés 2:**  Most gondolja át, hogy a forrás virtuális gépen van-e adatváltozás a 3. blokkban és a 2. blokkban. Ezek a változások a következő növekményes biztonsági másolaton lesznek replikálva: D3 "és D2".

![Második növekményes biztonsági mentés](./media/manage-recovery-points/second-incremental-backup.png)

### <a name="on-demand-backup"></a>Igény szerinti biztonsági mentés

A védelem beállítása után bármikor futtathatja a virtuális gép igény szerinti biztonsági mentését.

- Az igény szerinti biztonsági mentés teljes biztonsági mentést tesz elérhetővé, ha az első ütemezett kezdeti biztonsági mentés előtt aktiválódik.
- Ha a kezdeti biztonsági mentés elkészült, és a rendszer egy igény szerinti biztonsági mentést indít el, akkor növekményes biztonsági mentést készít.
- Az igény szerinti biztonsági mentéshez létrehozott helyreállítási pontok megőrzési ideje a biztonsági mentés indításakor megadott megőrzési idő.

### <a name="storage-cost"></a>Tárolási költség

A kezdeti biztonsági mentéshez létrehozott **helyreállítási pont** az összes olyan blokkot tartalmazza, amely az összes adattal rendelkezik. A következő növekményes helyreállítási pontok csak a módosított adatblokkokat tartalmazzák. A tárolási költségek megfelelnek az összes helyreállítási pontra kiterjedő blokk összegének.

A fenti példa segítségével megismerheti a tárolási költségeket az egyes lépések után:

|Lépés  |Biztonsági mentés típusa  |Blokkok módosítva  |Tárolási típus |
|------|---------|---------|---------|
|1     |     Kezdeti biztonsági mentés    | 1. blokk, 2. blokk        |    1. helyreállítási pontnak (D1 + D2) megfelelő     |
|2     |  Növekményes biztonsági mentés 1       |  3. blokk       |   Az 1. helyreállítási pontnak (D1 + D2) és a 2. helyreállítási pontnak (D3) megfelelő      |
|3     |    Növekményes biztonsági mentés 2     |    2. blokk, 3. blokk     |   Az 1. helyreállítási pontnak (D1 + D2) és a 2. helyreállítási pontnak (D3) és a 3. helyreállítási pontnak (D2 + D3) megfelelő      |

### <a name="recovery-point-expiration"></a>Helyreállítási pont lejárata

Az egyes helyreállítási pontok megőrzési időtartama a biztonsági mentési szabályzatban meghatározott. A karbantartás rendszeres időközönként történik, és a lejárt helyreállítási pontok törlődnek.

Ha a helyreállítási pont lejár, törölve vagy egyesítve lesz.

### <a name="case-1-initial-recovery-point-expires"></a>1. eset: a kezdeti helyreállítási pont lejár

Amikor a kezdeti helyreállítási pont lejár, a következő növekményes helyreállítási ponttal egyesül. A növekményes helyreállítási pontban felülírt összes adatblokk törölve lesz, és a rendszer egyesíti a többiet. A növekményes biztonsági mentés a kezdeti teljes biztonsági mentés lesz. Tekintsük át például a következőket:

- A kezdeti biztonsági mentés során létrehozott *1. helyreállítási pont* teljes biztonsági másolatot KÉSZÍT a virtuális gépről.
- Ha az *1. helyreállítási pont* lejár, a *2. helyreállítási pont* a következő teljes biztonsági mentés.
- A rendszer törli a D1-es *helyreállítási pontot* és a D2-t, mivel a 2. blokkban lévő adatvesztés felül van írva a 2. *helyreállítási pontban*. Ez a módosítás a D2 blokkként van rögzítve.
- A D1-es blokk az egymást követő helyreállítási pontokban marad, amíg a következő biztonsági mentés előtt nem történt módosítás.

![Első eset](./media/manage-recovery-points/first-case.png)

### <a name="case-2-in-between-incremental-recovery-point-expires"></a>2. eset: a növekményes helyreállítási pont között lejár

- Ha a *2* . helyreállítási pont az *1. helyreállítási* pont előtt lejár, a *2* . helyreállítási pontból származó adatok egyesítése a következő elérhető helyreállítási ponttal történik: 3. *helyreállítási* pont. Így a D3 blokk összeolvad a *3. helyreállítási ponttal*.
- Az *1. helyreállítási pont* még mindig a D1-es és a D2-es blokk teljes biztonsági mentése.

![Második eset](./media/manage-recovery-points/second-case.png)

### <a name="case-3-on-demand-recovery-point-expires"></a>3. eset: az igény szerinti helyreállítási pont lejár

Ebben a példában egy ütemezett (napi biztonsági mentés) házirendet ütemeztek a rendszer *n* napos megőrzési időtartammal való futtatásra.  Ha egy igény szerinti biztonsági mentést a következő ütemezett biztonsági mentést megelőző negyedik napon, a megőrzési időtartam pedig 10 nap múlva aktiválják, akkor továbbra is növekményes biztonsági mentés lesz. A helyreállítási pontot (*igény szerinti rp1*) a *3* . helyreállítási pont és a *4. helyreállítási* pont előtt hozza létre a rendszer.  A 14. nap végén az igény szerinti helyreállítási pont (*igény szerinti rp1*) lejár, és a következő elérhető helyreállítási ponttal lesz egyesítve. A kiszolgálón továbbra is az adatblokkok egyesülnek, míg a módosult (felülírt vagy törölt) adatblokkok törlődnek a lejárt helyreállítási pontból.

![Harmadik eset](./media/manage-recovery-points/third-case.png)

### <a name="impact-of-policy-change-on-recovery-points"></a>A helyreállítási pontok házirend-módosításának következményei

A szabályzatok módosításakor az új és a meglévő helyreállítási pontokra is alkalmazza a rendszer. További információ: a [házirendek változásának hatása a helyreállítási pontokon](backup-architecture.md#impact-of-policy-change-on-recovery-points).

### <a name="impact-of-stop-protection-on-recovery-points"></a>A helyreállítási pontok elleni védelem leállításának következményei

A virtuális gépek védelmét kétféleképpen állíthatja le:

- **Állítsa le a védelmet, és törölje a biztonsági másolati fájlokat.** Ez a beállítás leállítja az összes jövőbeli biztonsági mentési feladatot a virtuális gép védelmére, és törli az összes helyreállítási pontot. Ha a [Soft delete](backup-azure-security-feature-cloud.md) engedélyezve van, a törölt adat 14 napig megmarad. A helyreállított állapotú elemek esetében nem merül fel díj. Az adatmennyiség 14 nap alatt törölhető. Ha a Soft delete nincs engedélyezve, a rendszer azonnal törli az adatvesztést, és nem fogja tudni visszaállítani a virtuális gépet, vagy használhatja a **biztonsági mentés folytatása** lehetőséget.
- **A védelem leállítása és a biztonsági mentési adat megőrzése.** Ezzel a beállítással leállíthatja a virtuális gép védelmének jövőbeli biztonsági mentési feladatait. A Azure Backup szolgáltatás azonban örökre megőrzi a biztonsági mentés alatt álló helyreállítási pontokat. A helyreállítási pontok megőrzéséhez a tárolóban kell fizetnie (lásd a részletek [Azure Backup díjszabását](https://azure.microsoft.com/pricing/details/backup/) ). Szükség esetén visszaállíthatja a virtuális gépet. Ha úgy dönt, hogy folytatja a virtuális gép védelmét, a **biztonsági mentés folytatása** lehetőséggel is elvégezheti. A biztonsági mentés folytatása után a rendszer az adatmegőrzési szabályokat a lejárati pontokra alkalmazza. A biztonsági másolatok törlése lehetőséggel törölheti is a biztonsági  **másolatban** szereplőket.

## <a name="impact-of-deleting-a-vm-without-stop-protection"></a>A virtuális gép törlésének hatása a védelem leállítása nélkül

A virtuális gép a védelem leállítása nélküli törlése hatással van a helyreállítási pontokra, és nem kívánatos forgatókönyv. Az ideális biztonsági mentéseket le kell állítani a virtuális gép törlése előtt. Mivel az erőforrás nem létezik, az ütemezett biztonsági mentések sikertelenek lesznek a [VMNotFoundV2 hibával](backup-azure-vms-troubleshoot.md#320001-resourcenotfound---could-not-perform-the-operation-as-vm-no-longer-exists--400094-bcmv2vmnotfound---the-virtual-machine-doesnt-exist--an-azure-virtual-machine-wasnt-found). A helyreállítási pontok rendszeres időközönként törlődnek az adatmegőrzési szabályzat alapján, de a virtuális gép utolsó példánya örökre megmarad, és ennek megfelelően lesz kiszámlázva. A forgatókönyvtől függően a következő két lehetőség közül választhat:

- **1. lehetőség:** Állítsa vissza a virtuális gépet a helyreállítási pontok bármelyikének használatával. Ha helyre kívánja állítani a törölt virtuális gépet, a visszaállítást ugyanazzal a névvel és ugyanabban az erőforráscsoporthoz kell elvégezni. Ha a visszaállított virtuális gépet ugyanahhoz a tárolóhoz állítja be, akkor a meglévő helyreállítási pontok automatikusan csatolva lesznek.
- **2. lehetőség:** Nyissa meg az Recovery Services-tárolót, és állítsa le a védelmet az adattörléssel.

### <a name="impact-of-expired-recovery-points-for-items-in-soft-deleted-state"></a>A lejárt helyreállítási pontok hatása a törölt állapotú elemek esetében

Ha a helyreállítási tár esetében engedélyezve van a helyreállított [Törlés](backup-azure-security-feature-cloud.md) , akkor a lejárt helyreállítási pont a törölt állapotban marad, és nem törlődik. Nem számítunk fel díjat, ha a helyreállítási pont nem törölhető állapotban van.

### <a name="impact-of-churn-on-backup-performance"></a>A forgalom biztonsági mentési teljesítményére gyakorolt hatás

Tegyük fel, hogy egy virtuális gép teljes tárterülete 8 TB, a forgalom pedig 5%. Ezután a megfelelő növekményes biztonsági mentési tárterület 0,4 TB-os 8 TB-os 5%-os lesz. A magasabb adatforgalom a későbbi növekményes biztonsági mentések esetében nagyobb háttérbeli tárterületnek felel meg. Az adatforgalom hatással van a biztonsági mentési teljesítményre. Nagyobb a változás, annál lassabb a biztonsági mentési folyamat, és nagyobb a háttérbeli tárolás használata.

Ha szeretné megtudni, hogy a forgalom milyen hatással van a biztonsági mentési teljesítményre, tekintse meg ezt a forgatókönyvet:

|Virtual machines (Virtuális gépek)  |VM1  |VM2  |VM3  |
|---------|---------|---------|---------|
|Adatlemezek száma    | 4 (A1, A2, A3, A4)        | 4 (B1, B2, B3, B4)        |  4 (C1, C2, C3, C4)       |
|Az egyes lemezek mérete   |      4 TB   | 4 TB        |  4 TB       |
|Biztonsági mentési adatforgalom    |   A1 – 4 TB      | B1 – 1 TB; B2 – 1 TB <br> B3 – 1 TB; B4 – 1 TB  |   C1 – 2 TB; C4 – 2 TB      |

A biztonsági mentési teljesítmény a VM2>VM3>VM1 sorrendben történik. Ennek az az oka, hogy az átadott adatforgalom a különböző lemezek között oszlik meg. Mivel a lemezek biztonsági mentése párhuzamosan történik, a VM2 a legjobb teljesítményt fogja mutatni.

## <a name="next-steps"></a>Következő lépések

- [Architektúra és összetevők Azure Backup](backup-architecture.md)
