---
title: Az Azure Disk Backup áttekintése
description: További információ az Azure Disk Backup megoldásról.
ms.topic: conceptual
ms.date: 01/07/2021
ms.openlocfilehash: 9449fdc57909cb143d381ae074913c79d24c8893
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/25/2021
ms.locfileid: "105107295"
---
# <a name="overview-of-azure-disk-backup"></a>Az Azure Disk Backup áttekintése

Az Azure Disk Backup egy natív, felhőalapú biztonsági mentési megoldás, amely megvédi az adatait a felügyelt lemezeken. Ez egy egyszerű, biztonságos és költséghatékony megoldás, amely néhány lépésben lehetővé teszi a felügyelt lemezek védelmének konfigurálását. Gondoskodik arról, hogy az adatok vészhelyzeti helyzetben is helyreállíthatók legyenek.

Az Azure Disk Backup kulcsrakész megoldást kínál, amely pillanatfelvétel-életciklus-kezelést biztosít a felügyelt lemezekhez a pillanatképek rendszeres létrehozásának automatizálásával és a beállított időtartamra vonatkozó biztonsági mentési házirend használatával. A lemez-pillanatképeket az infrastrukturális költségek nélkül kezelheti, és nincs szükség egyéni parancsfájlok vagy felügyeleti terhelésre. Ez egy összeomlás-konzisztens biztonsági mentési megoldás, amely egy felügyelt lemez adott időpontban történő biztonsági mentését teszi lehetővé [növekményes Pillanatképek](../virtual-machines/disks-incremental-snapshots.md) használatával, és naponta több biztonsági mentést támogat. Ez egy ügynök nélküli megoldás is, amely nem befolyásolja az üzemi alkalmazások teljesítményét. Támogatja az operációs rendszer és az adatlemezek (beleértve a megosztott lemezeket is) biztonsági mentését és visszaállítását, függetlenül attól, hogy jelenleg egy futó Azure-beli virtuális géphez vannak-e csatolva.

Ha az alkalmazással konzisztens biztonsági másolatot szeretne készíteni a virtuális gépről, beleértve az adatlemezeket is, vagy egy teljes virtuális gép biztonsági mentésből való visszaállításának lehetőségét, egy fájl vagy mappa visszaállítását, vagy egy másodlagos régióba való visszaállítást, akkor az [Azure virtuális gép biztonsági mentési](backup-azure-vms-introduction.md) megoldását kell használnia. A Azure Backup támogatja a felügyelt lemezek biztonsági mentésének párhuzamos támogatását a lemezes biztonsági mentéssel az Azure-beli [virtuális gépek biztonsági mentési](./backup-azure-vms-introduction.md) megoldásai mellett. Ez akkor hasznos, ha a virtuális gépekre vonatkozó, egynapos alkalmazás-konzisztens biztonsági másolatokra van szükség, valamint az operációsrendszer-lemezekről vagy egy olyan adatlemezről, amely az összeomlás konzisztens, és nem befolyásolja az éles alkalmazások teljesítményét.

Az Azure Disk Backup integrálva van a Backup Centerbe, amely **egyetlen egységes felügyeleti élményt** biztosít az Azure-ban a nagyvállalatok számára a biztonsági másolatok szabályozására, figyelésére, üzemeltetésére és elemzésére.

## <a name="key-benefits-of-disk-backup"></a>A lemezes biztonsági mentés főbb előnyei

Az Azure Disk Backup olyan ügynök nélküli és összeomolhat konzisztens megoldás, amely [növekményes pillanatképeket](../virtual-machines/disks-incremental-snapshots.md) használ, és a következő előnyöket kínálja:

- Gyakoribb és gyors biztonsági mentés a virtuális gép megszakítása nélkül.
- Nem befolyásolja az üzemi alkalmazás teljesítményét.
- Nincs biztonsági probléma, mivel nem igényli egyéni parancsfájlok futtatását vagy ügynökök telepítését.
- Költséghatékony megoldás adott lemezek biztonsági mentésére, ha összehasonlítjuk a teljes virtuális gép biztonsági mentését.

Az Azure Disk Backup megoldás a következő esetekben hasznos:

- Gyakori biztonsági mentések naponta a nyugalmi alkalmazás nélkül
- Fürtben futó alkalmazások: a Windows Server feladatátvevő fürt és a Linux-fürtök egyaránt a megosztott lemezekre vannak írva
- Az ügynök nélküli biztonsági mentés speciális igénye az alkalmazás biztonsági vagy teljesítménnyel kapcsolatos problémái miatt
- Az alkalmazások konzisztens biztonsági mentése nem valósítható meg, mivel az üzletági alkalmazások nem támogatják Kötet árnyékmásolata szolgáltatás (VSS) használatát.

Tekintse át az Azure-lemez biztonsági mentését olyan helyzetekben, ahol:

- a kritikus fontosságú alkalmazások egy olyan Azure-beli virtuális gépen futnak, amely naponta több biztonsági mentést igényel a helyreállítási pont célkitűzésének kielégítése érdekében, de az éles környezet vagy az alkalmazás teljesítményének befolyásolása nélkül.
- a szervezet vagy az iparági szabályozás biztonsági okokból korlátozza az ügynökök telepítését
- Egyéni vagy közzétételi parancsfájlok futtatása, valamint a befagyasztást és a felolvasztás meghívása Linux rendszerű virtuális gépeken az alkalmazás-konzisztens biztonsági mentés túlzott terhelést eredményez az éles munkaterhelések rendelkezésre állása miatt
- Az Azure Kubernetes szolgáltatásban (ak-beli fürtön) futó, tárolóba helyezett alkalmazások állandó tárterületként használják a felügyelt lemezeket. Ma a felügyelt lemezről biztonsági másolatot kell készítenie a nehezen kezelhető Automation-parancsfájlok segítségével.
- a felügyelt lemezek fontos üzleti, fájlmegosztási vagy adatbázis-biztonságimásolat-fájlokat tartalmaznak, és az Azure-beli virtuális gépek biztonsági mentésének mellőzésével optimalizálni szeretnék a biztonsági mentési költségeket.
- Számos Linux-és Windows egylemezes virtuális gép (azaz egy olyan virtuális gép, amely csak egy operációsrendszer-lemezzel rendelkezik, és nincs csatlakoztatott adatlemez), amelyek a webkiszolgáló vagy az állapot nélküli gépeket üzemeltetik, vagy átmeneti környezetként szolgálnak az alkalmazás konfigurációs beállításaival, és költséghatékony biztonsági mentési megoldásra van szükség az operációsrendszer-lemez védelme érdekében. Ha például egy gyors, igény szerinti biztonsági mentést szeretne elindítani a virtuális gép frissítése vagy javítása előtt
- a virtuális gép olyan operációsrendszer-konfigurációt futtat, amelyet az Azure virtuális gép biztonsági mentési megoldása nem támogat (például Windows 2008 32 bites kiszolgáló)

## <a name="how-the-backup-and-restore-process-works"></a>A biztonsági mentési és visszaállítási folyamat működése

- Az Azure Managed Disks szolgáltatás biztonsági mentésének konfigurálásának első lépése a [Backup-tároló](backup-vault-overview.md)létrehozása. A tároló a különböző munkaterhelések között konfigurált biztonsági másolatok összevont nézetét jeleníti meg.

- Ezután hozzon létre egy biztonsági mentési szabályzatot, amely lehetővé teszi a biztonsági mentési gyakoriság és a megőrzés időtartamának konfigurálását.

- A biztonsági mentés konfigurálásához nyissa meg a Backup-tárolót, rendeljen hozzá egy biztonsági mentési szabályzatot, válassza ki azt a felügyelt lemezt, amelyről biztonsági másolatot szeretne készíteni, és adjon meg egy erőforráscsoportot, ahol a pillanatképek tárolása és kezelése történik. Azure Backup automatikusan elindítja az ütemezett biztonsági mentési feladatokat, amelyek a biztonsági mentés gyakorisága alapján létrehozzák a lemez növekményes pillanatképét. A régebbi Pillanatképek a biztonsági mentési szabályzatban megadott megőrzési időtartamnak megfelelően törlődnek.

- Azure Backup a felügyelt lemez [növekményes pillanatképeit](../virtual-machines/disks-incremental-snapshots.md#restrictions) használja. A növekményes Pillanatképek a felügyelt lemezek költséghatékony, időponthoz kapcsolódó biztonsági mentése, amely az utolsó pillanatkép óta a lemez változásait terheli. A rendszer mindig a leggazdaságosabb tárterületen tárolja a standard HDD-tárterületet, függetlenül a szülő lemezek tárolási típusától. A lemez első pillanatképe elfoglalja a lemez használt méretét, és az egymást követő növekményes Pillanatképek a legutóbbi pillanatkép óta a lemez változásait is a lemezen tárolják.

- Miután konfigurálta a felügyelt lemez biztonsági mentését, a Backup-tárolóban létrejön egy biztonsági mentési példány. A biztonsági mentési példány használatával megtalálhatja a biztonsági mentési műveletek állapotát, elindíthatja az igény szerinti biztonsági mentést, és elvégezheti a visszaállítási műveleteket. Megtekintheti a biztonsági másolatok állapotát több tárolóban és biztonsági mentési példányban is a Backup Center használatával, amely egyetlen ablaktáblát biztosít az üveg nézetben.

- A visszaállításhoz csak válassza ki azt a helyreállítási pontot, amelyről vissza kívánja állítani a lemezt. Adja meg azt az erőforráscsoportot, amelyben a visszaállított lemezt létre kell hozni a pillanatképből. A Azure Backup azonnali visszaállítási élményt nyújt, mivel a pillanatképeket az előfizetésében helyileg tárolják.

- A Backup-tároló felügyelt identitás használatával fér hozzá más Azure-erőforrásokhoz. A felügyelt lemez biztonsági mentésének konfigurálásához és a korábbi biztonsági mentésből való visszaállításhoz a Backup-tár felügyelt identitásához engedélyekre van szükség a forrásoldali lemezen, a pillanatkép-erőforráscsoport létrehozásához és kezeléséhez, valamint a cél erőforráscsoporthoz, ahol vissza kívánja állítani a biztonsági mentést. Az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával engedélyeket adhat a felügyelt identitásnak. A felügyelt identitás olyan speciális típusú szolgáltatásnév, amelyet csak az Azure-erőforrásokkal lehet használni. További információ a [felügyelt identitásokról](../active-directory/managed-identities-azure-resources/overview.md).

- Az Azure Disk Backup jelenleg támogatja a felügyelt lemezek működési biztonsági mentését, és nem másolja a biztonsági másolatokat a Backup-tárolóba. Tekintse át a [támogatási mátrixot](disk-backup-support-matrix.md)a támogatott és nem támogatott forgatókönyvek, valamint a régiók rendelkezésre állása részletes listájához.

## <a name="pricing"></a>Díjszabás

Azure Backup egy pillanatkép-életciklus-kezelési megoldást kínál az Azure-lemezek védelméhez. A Azure Backup által létrehozott lemez-pillanatképeket az Azure-előfizetésében található erőforráscsoport tárolja, és a **Pillanatképek tárolásával** kapcsolatos díjat számítunk fel. A pillanatkép díjszabásával kapcsolatos további információkért tekintse meg a [felügyelt lemez díjszabását](https://azure.microsoft.com/pricing/details/managed-disks/) . Mivel a pillanatképeket nem másolja a Backup-tárolóba, Azure Backup nem számít fel díjat a **védett példányok** díja, és a **biztonsági mentési tárolási** költségek nem érvényesek. Emellett a növekményes Pillanatképek a legutóbbi pillanatkép óta elfoglalják a változási változásokat, és a szülő által felügyelt lemezek tárolási típusától függetlenül mindig a standard szintű tárolóban tárolódnak, és a standard szintű tárolás díjszabása alapján számítunk fel díjat. Így költséghatékony megoldás az Azure Disk Backup.

## <a name="next-steps"></a>Következő lépések

- [Az Azure Disk Backup támogatási mátrixa](disk-backup-support-matrix.md)
