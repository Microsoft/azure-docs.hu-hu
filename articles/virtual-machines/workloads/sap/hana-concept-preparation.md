---
title: A vész-helyreállítási alapelvek és az Azure-beli SAP HANA előkészítése (nagyméretű példányok) | Microsoft Docs
description: A vész-helyreállítási alapelvek és az Azure-beli SAP HANA előkészítése (nagyméretű példányok)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/10/2018
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: babd7c1dcae9d83af1f6c41e756b663d92d6d486
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101677117"
---
# <a name="disaster-recovery-principles"></a>Vész-helyreállítási alapelvek

A HANA nagyméretű példányai vész-helyreállítási funkciókat biztosítanak a különböző Azure-régiókban található HANA nagyméretű példányok bélyegei között. Ha például HANA nagyméretű példányokat helyez üzembe az Azure USA nyugati régiójában, akkor az USA keleti régiójában található HANA nagyméretű példány-egységek vész-helyreállítási egységként használhatók. Ahogy azt korábban említettük, a vész-helyreállítási funkció nincs automatikusan konfigurálva, mert a DR régióban egy másik HANA nagyméretű példány-egységért kell fizetnie. A vész-helyreállítási beállítás a vertikális felskálázáshoz és a kibővített telepítésekhez is használható. 

Az eddig üzembe helyezett forgatókönyvekben az ügyfelek a DR régióban lévő egységet használják a telepített HANA-példányt használó nem üzemi rendszerek futtatásához. A HANA nagyméretű példány-egységnek azonos SKU-nak kell lennie, mint az üzemi célra használt SKU-nak. Az alábbi képen látható, hogy az Azure-beli üzemi régióban és a vész-helyreállítási régióban a kiszolgáló egysége közötti lemez-konfiguráció a következőképpen néz ki:

![DR telepítési konfiguráció a lemezről nézetből](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_setup.PNG)

Ahogy az az áttekintő ábrán is látható, a lemez kötetének második készletét is meg kell rendelnie. A céllemez köteteinek mérete megegyezik a vész-helyreállítási egységek üzemi példányának termelési köteteivel. Ezek a kötetek a HANA nagyméretű példány kiszolgálói egységéhez vannak társítva a vész-helyreállítási helyen. A következő kötetek az üzemi régióból a DR helyre replikálódnak:

- /hana/data
- /hana/logbackups 
- /Hana/Shared (beleértve a/usr/SAP)

A/Hana/log kötet nem replikálódik, mert az SAP HANA tranzakciónapló nem szükséges a kötetek visszaállításának végrehajtásához. 

A felajánlott vész-helyreállítási funkciók alapján a HANA nagyméretű példány-infrastruktúrája által kínált tárolási replikálási funkciókat kell használni. A tárolási oldalon használt funkciók nem állandó adatfolyamként működnek, amelyek aszinkron módon replikálódnak, ahogy a változások történnek a tárolási köteten. Ehelyett ez egy olyan mechanizmus, amely arra támaszkodik, hogy az ilyen kötetekről készített Pillanatképek rendszeres időközönként jönnek létre. A már replikált pillanatkép és egy még nem replikált új pillanatkép közötti különbözetet ezután a rendszer átviszi a vész-helyreállítási helyre a cél lemez kötetekre.  Ezek a pillanatképek a köteteken tárolódnak, és ha vész-helyreállítási feladatátvétel van, akkor ezeket a köteteket vissza kell állítani.  

A kötet teljes adatforgalmának első átvitele előtt kell lennie ahhoz, hogy az adatok mennyisége kisebb legyen, mint a pillanatképek közötti különbözet. Ennek eredményeképpen a DR helyen található kötetek az üzemi helyen végrehajtott összes kötet-pillanatképet tartalmazzák. Végül a DR rendszer használatával egy korábbi állapotba állíthatja vissza az elveszett adatokat az éles rendszer visszaállítása nélkül.

Ha egy HANA nagyméretű példány-egységen több független SAP HANA példánnyal üzemelő MCOD üzemel, akkor a rendszer azt várja, hogy minden SAP HANA példány a DR oldalára replikálja a tárolót.

Azokban az esetekben, amikor a HANA rendszerreplikációt magas rendelkezésre állási funkcióként használja az üzemi helyen, és a DR hely számára a Storage-alapú replikációt használja, a rendszer replikálja az elsődleges helyről a DR-példányra irányuló csomópontok köteteit. További tárterületet kell vásárolnia (az elsődleges csomóponttal azonos méretben) a DR helyen, hogy az elsődleges és a másodlagos kiszolgálóról a DR-re replikálja a replikációt. 



>[!NOTE]
>A HANA nagyméretű példány tárolási replikációs funkciója a tárolási Pillanatképek tükrözését és replikálását végzi. Ha nem hajtja végre a jelen cikk biztonsági mentés és visszaállítás szakaszában bemutatott tárolási pillanatképeket, a vész-helyreállítási helyre nem lehet replikálni. A tárolási Pillanatképek végrehajtásának előfeltétele, hogy a tároló replikálása a vész-helyreállítási helyre történjen.



## <a name="preparation-of-the-disaster-recovery-scenario"></a>A vész-helyreállítási forgatókönyv előkészítése
Ebben az esetben egy éles rendszer fut az éles környezetben működő Azure-régióban található HANA nagyméretű példányokon. A következő lépésekben feltételezzük, hogy a HANA-rendszer SID-je "PRD", és hogy a DR Azure-régióban található HANA Large-példányokon futó, nem éles rendszer fut. Az utóbbi esetében Tételezzük fel, hogy az SID "TST". A következő képen a konfiguráció látható:

![DR telepítő elindítása](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start1.PNG)

Ha a kiszolgálópéldány még nem lett elrendezve a további tárolási kötettel, SAP HANA az Azure Service Management szolgáltatásban a kötetek további készletét csatolja célként az éles replika számára arra a HANA nagyméretű példány-egységre, amelyen a TST HANA-példányt futtatja. Erre a célra meg kell adnia a termelési HANA-példány biztonsági azonosítóját. Miután az Azure-szolgáltatások felügyeletének SAP HANAa megerősíti a kötetek mellékletét, csatlakoztatnia kell ezeket a köteteket a HANA nagyméretű példány egységéhez.

![DR telepítő következő lépése](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start2.PNG)

A következő lépés a második SAP HANA példány telepítése a DR Azure-régióban található HANA nagyméretű példány-egységre, ahol a TST HANA-példányt futtatja. Az újonnan telepített SAP HANA példánynak ugyanazzal a biztonsági azonosítóval kell rendelkeznie. A létrehozott felhasználóknak ugyanazzal az UID-vel és csoportazonosító kell rendelkezniük, mint az üzemi példány. Részletekért olvassa el a [biztonsági mentést és a visszaállítást](hana-backup-restore.md) . Ha a telepítés sikeres volt, a következőket kell tennie:

- Hajtsa végre a [biztonsági mentés és visszaállítás](hana-backup-restore.md)című témakörben leírt tárolási pillanatkép-előkészítés 2. lépését.
- Hozzon létre egy nyilvános kulcsot a HANA nagyméretű példányok DR egységéhez, ha még nem tette meg. Tekintse meg a [biztonsági mentés és visszaállítás](hana-backup-restore.md)című témakör a tárolási pillanatkép-előkészítés 3. lépését.
- Tartsa karban a *HANABackupCustomerDetails.txt* az új HANA-példánnyal, és ellenőrizze, hogy megfelelően működik-e a kapcsolat a tárolóval.  
- Állítsa le az újonnan telepített SAP HANA példányt a HANA nagyméretű példány-egységen a DR Azure-régióban.
- Válassza le ezeket a PRD-köteteket, és lépjen kapcsolatba SAP HANA az Azure Service Management szolgáltatásban. A kötetek nem maradhatnak csatlakoztatva az egységhez, mert nem tudnak hozzáférni a tárolási replikálási célként való működéshez.  

![Az éles környezetbeli Azure-régióban található PRD-kötetek és a DR Azure-régió PRD kötetei közötti replikációs kapcsolatot bemutató diagram.](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start3.PNG)

Az operatív csapat létrehozza a replikálási kapcsolatot az üzemi Azure-régióban található PRD-kötetek és a DR Azure-régióban található PRD-kötetek között.

>[!IMPORTANT]
>A/Hana/log kötet nem replikálódik, mert nem szükséges visszaállítani a replikált SAP HANA adatbázist konzisztens állapotra a vész-helyreállítási helyen.

Ezután állítsa be, vagy állítsa be a tárolási pillanatkép biztonsági mentési ütemtervét, hogy a RTO és a RPO a katasztrófa esetén. A helyreállítási pont célkitűzésének minimalizálásához állítsa be a következő replikációs intervallumokat a HANA nagyméretű példány-szolgáltatásban:
- A kombinált pillanatkép által érintett kötetek esetében (a **Hana** típusú pillanatkép típusa) állítsa be úgy a 15 percenkénti replikálást, hogy a vész-helyreállítási helyen lévő megfelelő tárolási kötetre irányuló célkitűzések legyenek.
- A tranzakciónapló biztonsági mentési kötete (pillanatkép típusú **naplók**) beállításnál állítsa be, hogy 3 percenként replikálódjon a vész-helyreállítási helyen található megfelelő tárolási kötetre.

A helyreállítási pont célkitűzésének minimalizálásához állítsa be a következőket:
- Hajtson végre egy **Hana** Type Storage-pillanatképet (lásd a "7. lépés: Pillanatképek elvégzése") 30 percenként 1 órára.
- SAP HANA tranzakciós napló biztonsági mentésének végrehajtása 5 percenként.
- Hajtson végre egy **napló** típusú tárolási pillanatképet 5-15 percenként. Ebben az intervallumban egy körülbelül 15-25 perces RPO érhet el.

Ezzel a beállítással a tranzakciónaplók biztonsági másolatai, a tárolási Pillanatképek, valamint a HANA-tranzakciónapló biztonsági mentési kötet-és/Hana/Data replikálása, valamint a/Hana/Shared (beleértve a/usr/SAP is) a következő ábrán láthatóhoz hasonló módon jelenhetnek meg:

 ![Tranzakciós napló biztonsági mentési pillanatképe és egy időtengelyen található beépülőmodul-tükör közötti kapcsolat](./media/hana-overview-high-availability-disaster-recovery/snapmirror.PNG)

A vész-helyreállítási eset még jobb RPO érdekében a HANA-tranzakciónapló biztonsági másolatait a SAP HANA Azure-ban (nagyméretű példányok) másolja át a másik Azure-régióba. A további RPO-csökkentés érdekében hajtsa végre a következő lépéseket:

1. A HANA-tranzakciós napló biztonsági mentése a lehető legtöbbször a/Hana/logbackups.
1. Az rsync használatával másolja át a tranzakciónaplók biztonsági másolatait az NFS-megosztáson tárolt Azure-beli virtuális gépekre. A virtuális gépek Azure-beli virtuális hálózatokban találhatók az Azure éles régióban és a DR régiókban. Mindkét Azure-beli virtuális hálózatot csatlakoztatnia kell az üzemi HANA nagyméretű példányainak az Azure-hoz való csatlakoztatásához. Tekintse meg a hálózati megfontolásokban található ábrákat a [nagy méretű HANA-példányokkal](#Network-considerations-for-disaster recovery-with-HANA-Large-Instances) kapcsolatos vész-helyreállításhoz. 
1. Tartsa meg a tranzakciónapló biztonsági másolatait az NFS exportált tárolóhoz csatolt virtuális gépen lévő régióban.
1. A vész-feladatátvételi esetekben a/Hana/logbackups-köteten található, a vész-helyreállítási helyen található NFS-megosztáson a közelmúltban végrehajtott tranzakciónapló-biztonsági másolatokkal kiegészítheti a tranzakciós napló biztonsági másolatait. 
1. Indítsa el a tranzakciós napló biztonsági mentését, és állítsa vissza a legújabb biztonsági mentést, amely a DR régióba menthető.

Ha a HANA nagyméretű példányainak műveletei megerősítik a replikálási kapcsolat beállítását, és elindítja a futtatási tároló pillanatképének biztonsági másolatait, az adatreplikáció megkezdődik.

![DR telepítési lépés a replikáció létrehozása előtt](./media/hana-overview-high-availability-disaster-recovery/disaster_recovery_start4.PNG)

A replikálás előrehaladtával a DR Azure-régiókban található PRD-kötetek pillanatképei nem lesznek visszaállítva. Ezeket csak a tárolja. Ha a kötetek ilyen állapotban vannak csatlakoztatva, azok az állapotot jelölik, amelyben leválasztja ezeket a köteteket a PRD SAP HANA példánynak a DR Azure-régióban lévő kiszolgálói egységbe való telepítése után. Azokat a tárolási biztonsági másolatokat is képviselik, amelyek még nincsenek visszaállítva.

Feladatátvétel esetén a legújabb tárolási pillanatkép helyett a korábbi tárolási pillanatképre is visszaállíthatja a visszaállítást.

## <a name="next-steps"></a>Következő lépések

- Tekintse át a vész- [helyreállítási feladatátvételi eljárást](hana-failover-procedure.md).
