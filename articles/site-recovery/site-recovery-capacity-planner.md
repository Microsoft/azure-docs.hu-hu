---
title: A Hyper-V vész-helyreállítás kapacitásának megtervezése Azure Site Recovery
description: Ebből a cikkből megbecsülheti a kapacitást, amikor a Azure Site Recovery szolgáltatással vész-helyreállítást állít be.
services: site-recovery
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: 0bdc66b46e83a1592d1ea74c1080d6f8080f4635
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "106580663"
---
# <a name="plan-capacity-for-hyper-v-vm-disaster-recovery"></a>Kapacitás megtervezése Hyper-V rendszerű virtuális gépek vészhelyreállításához 

A Hyper-V – Azure üzembe helyezéséhez szükséges [Azure Site Recovery Deployment Planner] (site-recovery-hyper-v-deployment-planner.md) a következőket biztosítja:

* A virtuális gépek támogathatóságának értékelése a lemezek száma, a lemez mérete, a IOPS, a forgalom és néhány virtuálisgép-jellemző alapján
* A hálózatisávszélesség-igény és RPO-elemzés
* Azure infrastruktúra-követelmények
* Helyszíni infrastruktúra-követelmények
* Útmutató a kezdeti replikáció kötegeléséhez
* A vész-helyreállítási költségek becsült teljes költsége az Azure-ban


Azure Site Recovery Capacity Planner segít meghatározni a kapacitásra vonatkozó követelményeket a Hyper-V virtuális gépek Azure Site Recovery használatával történő replikálásakor.

Használja Site Recovery Capacity Planner a forrás-és a számítási feladatok elemzéséhez. Segít megbecsülni a sávszélességi igényeket, a forrás helyéhez szükséges kiszolgálói erőforrásokat, valamint a célhelyen szükséges erőforrásokat (például virtuális gépeket és tárterületet).

Az eszközt két módban is futtathatja:

* **Gyors tervezés**: hálózati és kiszolgáló-kivetítéseket biztosít a virtuális gépek, lemezek, tárolók és a változási arányok átlagos száma alapján.
* **Részletes tervezés**: az egyes számítási feladatok részleteit tartalmazza a virtuális gép szintjén. A virtuális gépek kompatibilitásának elemzése és a hálózati és kiszolgálói előrejelzések beolvasása.

## <a name="before-you-start"></a>Előkészületek

* Gyűjtsön információkat a környezetéről, beleértve a virtuális gépeket, a lemezeket/virtuális gépeket, a tárterületet.
* A replikált adatforgalom napi változási arányának meghatározása. Töltse le a [Hyper-V kapacitás-tervezési eszközt](https://www.microsoft.com/download/details.aspx?id=39057) a változási arány eléréséhez. [További információ](./hyper-v-deployment-planner-overview.md) az eszközről. Javasoljuk, hogy az átlagok rögzítése érdekében futtassa az eszközt egy hétre.


## <a name="run-the-quick-planner"></a>A gyors Planner futtatása
1. Töltse le és nyissa meg [Site Recovery Capacity Planner](/samples/browse/?redirectedfrom=TechNet-Gallery). Makrókat kell futtatnia. Amikor a rendszer kéri, válassza a Szerkesztés és a tartalom engedélyezése lehetőséget.

2. A **Planner típusának kiválasztása** listában válassza a **gyors Planner** elemet.

   ![Képernyőkép a tervező típusának kiválasztása lehetőségről, a gyors Planner kiválasztásával.](./media/site-recovery-capacity-planner/getting-started.png)

3. A **Capacity Planner** munkalapon adja meg a szükséges adatokat. Töltse ki az összes piros színnel jelölt mezőt a következő képernyőképen:

   a. A **forgatókönyv kiválasztása** területen válassza a **Hyper-V – Azure** vagy a **VMware/fizikai az Azure-hoz** lehetőséget.

   b. A **napi adatváltozási arány (%)** mezőben adja meg az összegyűjtött adatokat a [Hyper-V kapacitás-tervezési eszköz](./hyper-v-deployment-planner-overview.md) vagy a [site Recovery Deployment Planner](./site-recovery-deployment-planner.md)használatával.

   c. A **tömörítési** beállítás nem használatos a Hyper-V virtuális gépek Azure-ba történő replikálásakor. A tömörítéshez használjon egy külső gyártótól származó készüléket, például Riverbed.

   d. A **megőrzési napok** mezőben határozza meg, hogy hány nap elteltével kell megőrizni a replikákat.

   e. Azon **órák száma, amelyekben a virtuális gépek kezdeti replikálása szükséges** , és a **virtuális gépek száma kezdeti replikálási kötegben**, a kezdeti replikációs követelmények kiszámításához használt beállítások megadása. Site Recovery telepítésekor a rendszer feltölti a teljes kezdeti adatkészletet.

   ![Képernyőkép a Capacity Planner munkalapról, amely a szükséges bemeneti adatokat mutatja.](./media/site-recovery-capacity-planner/inputs.png)

4. A forrás-környezet értékeinek megadása után a megjelenített kimenet a következőket tartalmazza:

   * A **különbözeti replikációhoz szükséges sávszélesség (megabit/mp)**: a különbözeti replikáció hálózati sávszélességét a napi adatváltozások átlagos száma alapján számítjuk ki.
   * A **kezdeti replikáláshoz szükséges sávszélesség (megabit/mp)**: a kezdeti replikálás hálózati sávszélességét a megadott kezdeti replikálási értékek alapján számítjuk ki.
   * **Tárterület szükséges (GB-ban)**: a teljes Azure-tárterület szükséges.
   * A **standard szintű tárterület teljes IOPS**: a rendszer a 8K IOPS egység mérete alapján számítja ki az összes standard Storage-fiókra vonatkozó értéket. A Quick Planner esetében a számot az összes forrásoldali virtuálisgép-lemez és a napi adatváltozási arány alapján számítjuk ki. A részletes Planner esetében a számot a standard Azure-beli virtuális gépekre leképezett virtuális gépek teljes száma, valamint a virtuális gépek adatváltozási sebessége alapján számítjuk ki.
   * A **szükséges standard szintű Storage-fiókok száma**: a virtuális gépek elleni védelemhez szükséges standard Storage-fiókok teljes száma. A standard szintű Storage-fiókok akár 20 000 IOPS is rendelkezhetnek a standard szintű tárolóban lévő összes virtuális gépen. Lemezenként legfeljebb 500 IOPS támogatott.
   * **Szükséges blob-lemezek száma**: az Azure Storage-on létrehozott lemezek száma.
   * A **szükséges prémium szintű fiókok száma**: a virtuális gépek elleni védelemhez szükséges prémium szintű Storage-fiókok teljes száma. A magas IOPS (20 000-nál nagyobb) forrásként szolgáló virtuális gépnek prémium szintű Storage-fiókra van szüksége. A prémium szintű Storage-fiók akár 80 000 IOPS is rendelkezhet.
   * **Premium Storage összes IOPS**: a szám kiszámítása a Premium Storage-fiókok 256K IOPS-egységének mérete alapján történik. A Quick Planner esetében a számot az összes forrásoldali virtuálisgép-lemez és a napi adatváltozási arány alapján számítjuk ki. A részletes Planner esetében a számot a prémium szintű Azure-beli virtuális gépekre (DS és GS sorozat) leképezett virtuális gépek teljes száma, valamint a virtuális gépek adatváltozási sebessége alapján számítjuk ki.
   * **Szükséges konfigurációs kiszolgálók száma**: azt mutatja, hogy hány konfigurációs kiszolgáló szükséges a központi telepítéshez.
   * **További szükséges folyamat-kiszolgálók száma**: azt mutatja, hogy szükség van-e további folyamat-kiszolgálókra a konfigurációs kiszolgálón futó folyamaton kívül alapértelmezés szerint.
   * **100%-os további tárterület a forráson**: azt mutatja, hogy szükséges-e további tárterület a forrás helyén.

      ![Képernyőkép a megjelenített kimenetről a megadott bemenet alapján.](./media/site-recovery-capacity-planner/output.png)

## <a name="run-the-detailed-planner"></a>A részletes Planner futtatása

1. Töltse le és nyissa meg [Site Recovery Capacity Planner](/samples/browse/?redirectedfrom=TechNet-Gallery). Makrókat kell futtatnia. Amikor a rendszer kéri, válassza a Szerkesztés és a tartalom engedélyezése lehetőséget.

2. A **tervező típusának kiválasztása** mezőben válassza a **részletes Planner** elemet a listából.

   ![Képernyőkép a tervező típusának kiválasztása beállításról, a részletes Planner kiválasztásával.](./media/site-recovery-capacity-planner/getting-started-2.png)

3. A **munkaterhelés-minősítés** munkalapon adja meg a szükséges adatokat. Ki kell töltenie az összes megjelölt mezőt.

   a. A **processzor magok** területen határozza meg a forráskiszolgálón lévő magok teljes számát.

   b. A **memória kiosztása (MB)** mezőben határozza meg a forráskiszolgáló RAM-méretét.

   c. A hálózati adapterek **száma** mezőben határozza meg a forráskiszolgálón lévő hálózati adapterek számát.

   d. A **teljes tárterület (GB)** mezőben határozza meg a virtuális gép tárterületének teljes méretét. Ha például a forráskiszolgáló három lemezből áll, amelyek mindegyike 500 GB, a teljes tárterület mérete 1 500 GB.

   e. A **csatlakoztatott lemezek száma** mezőben határozza meg a forráskiszolgáló összes lemezének teljes számát.

   f. A **lemez kapacitása kihasználtsága (%)** mezőben határozza meg az átlagos kihasználtságot.

   : A **napi adatváltozási arány (%)** értéknél a forráskiszolgáló napi adatváltozási arányát kell megadni.

   h. Az **Azure-beli virtuális gép méretének leképezése** mezőben adja meg a leképezni kívánt Azure-beli virtuális gép méretét. Ha ezt manuálisan nem szeretné elvégezni, válassza a **számítási IaaS virtuális gépek** lehetőséget. Ha a manuális beállítást adja meg, majd kiválasztja a **számítási IaaS virtuális gépek** lehetőséget, előfordulhat, hogy a manuális beállítás felül van írva. A számítási folyamat automatikusan azonosítja az Azure-beli virtuális gépek méretének legmegfelelőbb egyezését.

   ![Képernyőkép a munkaterhelés-minősítési munkalapról, amely a szükséges bemeneti adatokat jeleníti meg.](./media/site-recovery-capacity-planner/workload-qualification.png)

4. Ha a **számítási IaaS virtuális gépek** lehetőséget választja, a következőkre van szüksége:

   * Ellenőrzi a kötelező bemeneteket.
   * Kiszámítja a IOPS, és az Azure-ba való replikációra jogosult minden virtuális gép esetében a legjobb Azure-beli virtuálisgép-méretet javasolja. Ha a megfelelő méretű Azure-beli virtuális gép nem észlelhető, hibaüzenet jelenik meg. Ha például a csatlakoztatott lemezek száma 65, akkor egy hibaüzenet jelenik meg, mert az Azure-beli virtuális gép legmagasabb mérete 64.
   * Az Azure-beli virtuális gépekhez használható Storage-fiókot javasolja.
   * Kiszámítja a standard Storage-fiókok és a számítási feladatok számára szükséges prémium szintű Storage-fiókok teljes számát. Görgessen le az Azure Storage-típus és a forráskiszolgáló számára használható Storage-fiók megtekintéséhez.
   * Befejezi és rendezi a táblázat többi részét a virtuális géphez hozzárendelt szükséges tárolási típus (standard vagy prémium) alapján, valamint a csatlakoztatott lemezek számát. Az Azure-ra vonatkozó követelményeket teljesítő összes virtuális gép esetében az oszlop a **VM minősítéssel rendelkezik?** az **Igen értéket** mutatja. Ha egy virtuális gépet nem lehet biztonsági másolatot készíteni az Azure-ba, hibaüzenet jelenik meg.

Az AA – AE oszlopok kimenete és az egyes virtuális gépek adatainak megadása.

![Képernyőfelvétel: AA kimeneti oszlopok az AK-ba.](./media/site-recovery-capacity-planner/workload-qualification-2.png)

### <a name="example"></a>Példa
Ha például hat virtuális gép esetében a táblázatban látható értékek szerepelnek, az eszköz kiszámítja és hozzárendeli a legjobb Azure-beli virtuális gép egyeztetését és az Azure Storage követelményeit.

![A munkaterhelés-minősítési hozzárendeléseket bemutató képernyőkép.](./media/site-recovery-capacity-planner/workload-qualification-3.png)

* A példában a kimenetben vegye figyelembe a következőket:

  * Az első oszlop a virtuális gépek, a lemezek és a forgalom ellenőrzési oszlopa.
  * Öt virtuális géphez két standard Storage-fiókra és egy Premium Storage-fiókra van szükség.
  * A VM3 nem jogosult a védelemre, mert egy vagy több lemez nagyobb, mint 1 TB.
  * A VM1 és a VM2 az első szabványos Storage-fiókot használhatja
  * A VM4 a második szabványos Storage-fiókot is használhatja.
  * A VM5 és a VM6 Premium Storage-fiókra van szükség, és mindkettő egyetlen fiókot is használhat.

    > [!NOTE]
    > A standard és a prémium szintű tároló IOPS a virtuálisgép-szinten, nem pedig a lemez szintjén számítjuk ki. A standard szintű virtuális gép legfeljebb 500 IOPS tud kezelni. Ha a lemez IOPS nagyobb, mint 500, prémium szintű tárterületre van szükség. Ha a lemez IOPS nagyobb, mint 500, de a teljes virtuálisgép-lemezek IOPS a standard szintű Azure-beli virtuálisgép-korlátokon belül vannak, akkor a Planner szabványos virtuális gépet vesz fel, nem pedig a DS vagy a GS sorozatra. (Az Azure virtuális gép korlátai a virtuális gépek mérete, a lemezek száma, az adapterek száma, a processzor és a memória.) Manuálisan kell frissítenie az Azure-beli méretezési cellát a megfelelő DS vagy GS sorozatú virtuális géppel.


Az összes adat megadása után válassza **az adatok elküldése a Planner eszközre** lehetőséget a Capacity Planner megnyitásához. A munkaterhelések ki vannak emelve, hogy megjelenjenek a védelemre jogosultak.

### <a name="submit-data-in-capacity-planner"></a>Az adatküldés Capacity Planner
1. Amikor megnyitja a **Capacity Planner** munkalapot, a rendszer a megadott beállítások alapján tölti fel. A "munkaterhelés" szó az **infra bemenetek forrás** cellájában jelenik meg, hogy a bemenet a **munkaterhelés-minősítési** munkalap legyen.

2. Ha módosításokat szeretne végezni, módosítania kell a **munkaterhelés-minősítési** munkalapot. Ezután válassza **az adatküldés a Planner eszközre** lehetőséget.

   ![A módosított bemeneteket és az eredményül kapott kimeneteket ábrázoló képernyőkép a Capacity Planner munkalapon.](./media/site-recovery-capacity-planner/capacity-planner.png)

## <a name="next-steps"></a>Következő lépések
[Megtudhatja, hogyan futtathatja](./hyper-v-deployment-planner-overview.md) a kapacitás-tervezési eszközt.