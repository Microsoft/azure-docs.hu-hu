---
title: Az Azure Virtual Machines SAP HANA biztonsági mentési útmutatója | Microsoft Docs
description: A SAP HANA biztonsági mentési útmutatója két fő biztonsági mentési lehetőséget biztosít az Azure-beli virtuális gépek SAP HANA
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ums.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/01/2020
ms.author: juergent
ms.openlocfilehash: 0004afb5895d7549e5db8ad5e53b52fa17991520
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "101667903"
---
# <a name="backup-guide-for-sap-hana-on-azure-virtual-machines"></a>Az Azure-beli SAP HANA biztonsági mentési útmutatója Virtual Machines

## <a name="getting-started"></a>Első lépések

Az Azure Virtual Machines szolgáltatásban futó SAP HANA biztonsági mentési útmutatója csak az Azure-specifikus témaköröket fogja leírni. Az általános SAP HANA biztonsági mentéshez kapcsolódó elemek esetében olvassa el a SAP HANA dokumentációját. Várjuk, hogy Ismerje meg az adatbázis-biztonsági mentési stratégiákat, az okokat és a motivációkat a hang-és érvényes biztonsági mentési stratégiával kapcsolatban, és Ismerje meg a vállalat által a biztonsági mentési eljáráshoz szükséges követelményeket, a biztonsági mentések megőrzési idejét és a visszaállítási eljárást.

A SAP HANA a különböző Azure-beli virtuálisgép-típusok, például az Azure M sorozatok esetében hivatalosan támogatott. A SAP HANA Certified Azure-beli virtuális gépek és a HANA nagyméretű példány-egységek teljes listájáért tekintse meg a [Certified IaaS-platformok keresése](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)című fejezetet. Microsoft Azure számos egységet biztosít, ahol a SAP HANA a fizikai kiszolgálókon nem virtualizált fut. A szolgáltatás neve [HANA Large instances](hana-overview-architecture.md). Ez az útmutató nem fedi le a HANA nagyméretű példányok biztonsági mentési folyamatait és eszközeit. Az Azure-beli virtuális gépek azonban korlátozottak lesznek. A HANA nagyméretű példányokkal rendelkező biztonsági mentési és visszaállítási folyamatokkal kapcsolatos részletekért olvassa el a [HLI biztonsági mentése és visszaállítása](./hana-backup-restore.md)című cikket.

Ez a cikk a SAP HANA Azure-beli virtuális gépekre vonatkozó három biztonsági mentési lehetőségére összpontosít:

- HANA Backup [Azure Backup-szolgáltatásokon](../../../backup/backup-overview.md) keresztül 
- HANA biztonsági másolat készítése egy Azure-beli linuxos virtuális gépen található fájlrendszerre (lásd: [SAP HANA Azure Backup a fájl szintjén](sap-hana-backup-file-level.md))
- A HANA biztonsági mentése a tárolási Pillanatképek alapján az Azure Storage blob Snapshot funkciójának manuális vagy Azure Backup szolgáltatása használatával


A SAP HANA biztonsági mentési API-t biztosít, amely lehetővé teszi, hogy a külső gyártótól származó biztonsági mentési eszközök közvetlenül a SAP HANA integrálva legyenek. Azok a termékek, mint a Azure Backup szolgáltatás, vagy a [CommVault](https://azure.microsoft.com/resources/protecting-sap-hana-in-azure/) ezt a védett felületet használják SAP HANA-adatbázis aktiválására vagy a naplók biztonsági másolatainak megismétlésére. 


Az Azure-ban támogatott SAP-szoftvereket az Azure-beli [üzembe helyezések által támogatott SAP-szoftvereket](./sap-supported-product-on-azure.md)ismertető cikkben találja.

## <a name="azure-backup-service"></a>Azure Backup szolgáltatás

Az első forgatókönyv azt mutatja be, hogy Azure Backup szolgáltatás vagy a SAP HANA felületen keresztül `backint` végez adatfolyam-mentést egy SAP HANA-adatbázisból. Vagy a Azure Backup szolgáltatás általánosabb funkciójának használatával létrehozhat egy alkalmazás-konzisztens lemez-pillanatképet, és azt a Azure Backup szolgáltatásba kell átvinni.

A Azure Backup a [backint](https://www.sap.com/dmc/exp/2013_09_adpd/enEN/#/d/solutions?id=8f3fd455-a2d7-4086-aa28-51d8870acaa5)nevű tulajdonosi SAP HANA felülettel integrálja és minősítette SAP HANA biztonsági mentési megoldásként. A megoldással, annak képességeivel és az elérhető Azure-régiókkal kapcsolatos további információkért olvassa el a [SAP HANA adatbázisok Azure-beli virtuális gépeken történő biztonsági mentését](../../../backup/sap-hana-backup-support-matrix.md#scenario-support)ismertető cikket. A HANA Azure Backup szolgáltatásával kapcsolatos részletekért és alapelvekért olvassa el az [Azure-beli virtuális gépeken futó SAP HANA adatbázis biztonsági mentéséről szóló](../../../backup/sap-hana-db-about.md)cikket. 

A második lehetőség a Azure Backup szolgáltatás kihasználása az alkalmazások konzisztens biztonsági mentésének létrehozása az Azure Premium Storage lemezes pillanatképei használatával. Más HANA minősítésű Azure-tárolók, például az [Azure Ultra Disk](../../disks-enable-ultra-ssd.md) és a [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) nem támogatják ezt a pillanatképet Azure Backup szolgáltatáson keresztül. A cikkek olvasása:

- [Virtuális gép biztonsági infrastruktúrájának megtervezése az Azure-ban](../../../backup/backup-azure-vms-introduction.md)
- [Azure-beli linuxos virtuális gépek alkalmazáskonzisztens biztonsági mentése](../../../backup/backup-azure-linux-app-consistent.md) 

Ez a tevékenységi folyamat a következő:

- Azure Backup végre kell hajtania egy pillanatkép előtti parancsfájlt, amely az alkalmazást, ebben az esetben SAP HANA konzisztens állapotban helyezi el.
- Mivel a rendszer megerősíti ezt az állandó állapotot, Azure Backup végrehajtja a lemez-pillanatképeket
- A pillanatképek befejezése után a Azure Backup visszavonja a pillanatkép előtti parancsfájlban végzett tevékenységet.
- A sikeres végrehajtás után a Azure Backup továbbítja az adatstreamet a Backup-tárolóba

SAP HANA esetén a legtöbb ügyfél az Azure-írásgyorsító használja az SAP HANA újra naplót tartalmazó kötetekhez. A Azure Backup szolgáltatás automatikusan kizárja ezeket a köteteket a pillanatképekről. Ez a kizárás nem károsítja a HANA visszaállításának képességét. Bár ez letiltja a helyreállítást szinte minden más SAP által támogatott adatbázis-kezelővel.

Ennek a lehetőségnek a hátránya a tény, hogy létre kell hoznia a saját előzetes és pillanatkép utáni szkriptjét. A pillanatkép előtti parancsfájlnak létre kell hoznia egy HANA-pillanatképet, és kezelnie kell a végső kivétel eseteit. Míg a pillanatkép utáni parancsfájlnak újra törölnie kell a HANA-pillanatképet. A logikával kapcsolatos további részletekért tekintse meg a következőt [: SAP támogatási megjegyzés #2039883](https://launchpad.support.sap.com/#/notes/2039883). A jelen cikk "SAP HANA adatkonzisztencia a tárolási Pillanatképek készítésekor" szakaszának szempontjait teljes mértékben alkalmazni kell az ilyen típusú biztonsági mentésre.

> [!NOTE]
> Lemezes pillanatkép-alapú biztonsági másolatok SAP HANAhoz olyan központi telepítések esetén, ahol több adatbázis-tároló van használatban, a HANA 2,0 SP04 minimális kiadására van szükség
> 

Tekintse meg a jelen dokumentum későbbi részében található tárolási Pillanatképek részleteit.

![Ez az ábra két lehetőséget mutat a virtuális gép állapotának mentésére](media/sap-hana-backup-guide/azure-backup-service-for-hana.png)

## <a name="other-hana-backup-methods"></a>Egyéb HANA biztonsági mentési módszerek
Három további biztonsági mentési módszer vagy elérési út is megtekinthető:

- Biztonsági mentés egy Azure NetApp Files (ANF)-alapú NFS-megosztáson. A ANF ismét képes pillanatképeket készíteni a biztonsági másolatokat tároló kötetekről. A megoldás a biztonsági mentések megírásához szükséges átviteli sebesség miatt költséges módszer lehet. Bár a HANA könnyen hozható létre, mert a HANA közvetlenül az Azure natív NFS-megosztásba írhatja a biztonsági mentéseket
- A HANA Backup végrehajtása a standard SSD vagy az Azure Premium Storage virtuális gépekhez csatlakoztatott lemezei között. A következő lépésként átmásolhatja ezeket a biztonságimásolat-fájlokat az Azure Blob Storage szolgáltatásba. Ez a stratégia vonzó lehet a kedvező áron
- A HANA Backup végrehajtása a standard SSD vagy az Azure Premium Storage virtuális gépekhez csatlakoztatott lemezei között. A következő lépésként a lemez rendszeres időközönként pillanatfelvétel válik. Az első pillanatkép után növekményes pillanatképeket használhat a költségek csökkentése érdekében

![Ez az ábra azokat a beállításokat mutatja be, amelyekkel a SAP HANA fájlok biztonsági mentése a virtuális gépen belül](media/sap-hana-backup-guide/other-hana-backup-paths.png)

Ez az ábra azokat a lehetőségeket mutatja be, amelyekkel egy SAP HANA fájl biztonsági mentését elvégezheti a virtuális gépen, majd más eszközök használatával tárolhatja azt a HANA-beli biztonsági mentési fájlokat. Azonban a harmadik féltől származó biztonsági mentési szolgáltatást vagy Azure Backup szolgáltatást nem érintő összes megoldás közösen van. Néhányat Felsorolhat, például az adatmegőrzés felügyeletét, az Automatikus visszaállítási folyamatot, valamint az automatikus időpontra történő helyreállítást Azure Backup szolgáltatásként vagy más, harmadik féltől származó, külső biztonsági mentési lakosztályok és szolgáltatások számára. A harmadik féltől származó szolgáltatások közül sokan képesek az Azure-on futni. 


## <a name="sap-resources-for-hana-backup"></a>A HANA biztonsági mentésének SAP-erőforrásai

### <a name="sap-hana-backup-documentation"></a>SAP HANA biztonsági mentési dokumentáció

- [Bevezetés a SAP HANA felügyeletbe](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.00/en-US)
- [A biztonsági mentési és helyreállítási stratégia megtervezése](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm)
- [A HANA biztonsági mentésének ütemterve ABAP DBACOCKPIT](https://www.hanatutorials.com/p/schedule-hana-backup-using-abap.html)
- [Az adatbiztonsági másolatok ütemezése (SAP HANA pilótafülke)](https://help.sap.com/saphelp_hanaplatform/helpdata/en/6d/385fa14ef64a6bab2c97a3d3e40292/frameset.htm)
- Gyakori kérdések SAP HANA biztonsági mentésről az [SAP-megjegyzés 1642148](https://launchpad.support.sap.com/#/notes/1642148)
- Gyakori kérdések SAP HANA adatbázis-és tárolási pillanatképekről az [SAP-megjegyzés 2039883](https://launchpad.support.sap.com/#/notes/2039883)
- Nem megfelelő hálózati fájlrendszerek biztonsági mentéshez és helyreállításhoz a 1820529-as [SAP-megjegyzésben](https://launchpad.support.sap.com/#/notes/1820529)

### <a name="how-to-verify-correctness-of-sap-hana-backup"></a>SAP HANA biztonsági mentés helyességének ellenőrzése
A biztonsági mentési módszertől függetlenül a tesztek egy másik rendszerre való visszaállításának futtatása abszolút szükségszerű. Ezzel a módszerrel biztosítható, hogy a biztonsági mentés helyes legyen, és a biztonsági mentés és a visszaállítás belső folyamatai a várt módon működjenek. A biztonsági mentések visszaállítása a helyszíni infrastruktúra-követelmények miatt is akadályt jelenthet, így a felhőben sokkal egyszerűbben valósítható meg a szükséges erőforrások erre a célra. Helyes, hogy a HANA olyan eszközöket tartalmaz, amelyek lehetővé teszik a biztonságimásolat-fájlok visszaállítását. A gyakori visszaállítási gyakorlatok célja azonban az, hogy tesztelje az adatbázis-visszaállítás folyamatát, és az operatív munkatársakkal dolgozzon.

Ne feledje, hogy egyszerű visszaállítást és ellenőrzést végez, ha a HANA nem működik megfelelően. A tábla konzisztencia-ellenőrzésének futtatásával győződjön meg arról, hogy a visszaállított adatbázis rendben van. SAP HANA számos konzisztencia-ellenőrzést kínál a 1977584-es [SAP-megjegyzésben](https://launchpad.support.sap.com/#/notes/1977584)ismertetett módon.

A tábla konzisztencia-ellenőrzésével kapcsolatos információk az SAP webhelyén [, a Table és a Catalog konzisztencia-ellenőrzéseken](https://help.sap.com/saphelp_hanaplatform/helpdata/en/25/84ec2e324d44529edc8221956359ea/content.htm#loio9357bf52c7324bee9567dca417ad9f8b)is megtalálhatók.

### <a name="pros-and-cons-of-hana-backup-versus-storage-snapshot"></a>A HANA Backup és a Storage pillanatkép előnyei és hátrányai

Az SAP nem&#39;t használ a HANA Backup és a Storage pillanatképének beállítására. Felsorolja azok előnyeit és hátrányait, így az a helyzettől és a rendelkezésre álló tárolási technológiától függően eldöntheti, hogy melyiket érdemes használni (lásd [a biztonsági mentési és helyreállítási stratégia megtervezése](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.05/en-US/ef085cd5949c40b788bba8fd3c65743e.html)című témakört).

Az Azure-ban vegye figyelembe, hogy az Azure Blob pillanatkép-funkciója nem&#39;a fájlrendszer konzisztenciáját több lemez között (lásd: [blob-Pillanatképek használata a PowerShell használatával](/archive/blogs/cie/using-blob-snapshots-with-powershell)). 

Emellett az egyiknek meg kell ismernie a számlázási szempontokat, amikor gyakran dolgozik a blob-pillanatképekkel a következő cikkben leírtak szerint: a [Pillanatképek felmerülésének ismertetése](/rest/api/storageservices/understanding-how-snapshots-accrue-charges)– az Azure-beli virtuális lemezek használata során nyilvánvalónak&#39;t

### <a name="sap-hana-data-consistency-when-taking-storage-snapshots"></a>Adatkonzisztencia SAP HANA a tárolási Pillanatképek készítésekor

A korábban leírtaknak megfelelően a Azure Backup, a fájlrendszer és az alkalmazások konzisztenciájának pillanatkép-készítési képességeinek ismertetése a tárolási Pillanatképek készítésekor kötelező. A problémák elkerülésének legegyszerűbb módja a SAP HANA leállítása, vagy akár a teljes virtuális gép is. Egy éles példány esetében nem valósítható meg valami.

> [!NOTE]
> Lemezes pillanatkép-alapú biztonsági másolatok SAP HANAhoz olyan központi telepítések esetén, ahol több adatbázis-tároló van használatban, a HANA 2,0 SP04 minimális kiadására van szükség
> 

Az Azure Storage nem biztosít fájlrendszerbeli konzisztenciát több lemezre vagy kötetre a pillanatkép-készítési folyamat során. Ez azt jelenti, hogy az alkalmazásnak konzisztensnek kell lennie az alkalmazásban, ebben az esetben SAP HANA magát. Az 2039883-es [SAP-Megjegyzés](https://launchpad.support.sap.com/#/notes/2039883) fontos információkat tartalmaz a tárolási pillanatképek SAP HANA biztonsági mentéséről. A XFS fájlrendszerek esetében például a **XFS \_ rögzítése** szükséges a tárolási pillanatkép elindításához az alkalmazás konzisztenciájának biztosítása érdekében (lásd: [XFS \_ Freeze (8) – Linux man oldal](https://linux.die.net/man/8/xfs_freeze) a **XFS \_ befagyasztásával** kapcsolatos részletekért.

Feltételezve, hogy a XFS fájlrendszer négy Azure virtuális lemezzel rendelkezik, a következő lépések egységes pillanatképet biztosítanak, amely a HANA-adatterületet jelöli:

1. HANA-adatok pillanatkép-előkészítésének létrehozása
1. Az összes lemez/kötet fájlrendszerének rögzítése (például **XFS \_ Freeze** használata)
1. Az összes szükséges blob-pillanatkép létrehozása az Azure-ban
1. A fájlrendszer rögzítésének feloldása
1. A HANA-adatok pillanatképének megerősítése (törli a pillanatképet)

Ha a Azure Backup képes az alkalmazások konzisztens pillanatképes biztonsági mentésére, akkor a pillanatkép-készítéshez szükséges lépéseket #1 kell végrehajtania az előzetes pillanatfelvétel-parancsfájl használatához. Azure Backup szolgáltatás végrehajtja a lépéseket #2 és #3. Az #4 és #5 lépéseit újra meg kell adni a kód a pillanatkép utáni parancsfájlban. Ha nem az Azure Backup szolgáltatást használja, akkor a (z) #2 kódot vagy parancsfájlt kell használnia, és #3 saját maga is.
A HANA-adatpillanatképek létrehozásával kapcsolatos további információkért tekintse meg a következő cikkeket:

- [HANA-adatpillanatképek] (https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/en-US/ac114d4b34d542b99bc390b34f8ef375.html
- További részletek a lépés végrehajtásához #1 a következő cikkben találhatók: [Adatpillanatkép létrehozása (NATÍV SQL)](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/en-US/9fd1c8bb3b60455caa93b7491ae6d830.html) 
- Az [adatok pillanatképének létrehozása (NATÍV SQL)](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/en-US/9fd1c8bb3b60455caa93b7491ae6d830.html) című cikkben található, a HANA-adatpillanatképek igény szerinti megerősítésére és törlésére vonatkozó részletek a #5 lépésben olvashatók. 

Fontos, hogy erősítse meg a HANA-pillanatképet. A &quot; másolási és írási művelet miatt előfordulhat, hogy a &quot; Pillanatkép-előkészítési módban a SAP HANA nem igényel további lemezterületet. &#39;s nem lehet új biztonsági mentéseket elindítani, amíg meg nem történik a SAP HANA pillanatkép megerősítése.


### <a name="sap-hana-backup-scheduling-strategy"></a>SAP HANA biztonsági mentési ütemezési stratégia

A [biztonsági mentési és helyreállítási stratégia megtervezésére](https://help.sap.com/saphelp_hanaplatform/helpdata/en/ef/085cd5949c40b788bba8fd3c65743e/content.htm) szolgáló SAP HANA cikk egy alapszintű tervet készít a biztonsági mentések elvégzéséhez. A HANA-hoz készült SAP-dokumentációra és a más adatbázis-kezelői funkciókra támaszkodva a SAP HANA biztonsági mentési/helyreállítási stratégiájának és folyamatának definiálásával. A különböző típusú biztonsági másolatok, a megőrzési idő pedig nagymértékben függ a megadható SLA-tól.


### <a name="sap-hana-backup-encryption"></a>SAP HANA biztonsági másolat titkosítása

SAP HANA az adattitkosítást és a naplót is biztosítja. Ha SAP HANA adatokat és naplókat nem titkosítja a rendszer, akkor a biztonsági mentések alapértelmezés szerint nem lesznek titkosítva. A SAP HANA azonban egy különálló biztonsági mentési titkosítást biztosít [SAP HANA biztonsági mentési titkosításban](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.03/en-US/5f837a57ce5e468d9db21c8683bc84da.html)dokumentált módon. Ha a SAP HANA régebbi kiadásait futtatja, előfordulhat, hogy ellenőriznie kell, hogy a biztonsági másolat titkosítása a már megadott funkciók részét képezte-e.  


## <a name="next-steps"></a>Következő lépések
* [SAP HANA Azure Backup a fájl szintjén](sap-hana-backup-file-level.md) leírja a fájl alapú biztonsági mentési lehetőséget.
* Ha meg szeretné tudni, hogyan hozhat létre magas rendelkezésre állást, és hogyan tervezheti meg az Azure-beli SAP HANA vész-helyreállítását (nagyméretű példányok), tekintse meg [a SAP HANA (nagyméretű példányok) magas rendelkezésre állását és a](hana-overview-high-availability-disaster-recovery.md)
