---
title: A nagyszámítógépek Azure-beli virtuális gépeken való üzemeltetése
description: A virtuális gépek (VM) használatával a Microsoft Azureon helyezheti át a nagyszámítógépek munkaterheléseit, például az IBM Z-alapú rendszereket.
services: virtual-machines
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: 7ae20b484b4c22c19b856c237d85efe31dc01407
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102553544"
---
# <a name="mainframe-rehosting-on-azure-virtual-machines"></a>A nagyszámítógépek Azure-beli virtuális gépeken való üzemeltetése

A nagyvállalati környezetek és a felhő közötti munkaterhelések áttelepítése lehetővé teszi az infrastruktúra korszerűsítését és a költségek gyakori megtakarítását. Rengeteg számítási feladat vihető át Az Azure-ba csupán kisebb kódmódosítással, például az adatbázisok nevének frissítésével.

Általánosságban elmondható, hogy a *mainframe* kifejezés egy nagyméretű számítógépes rendszert jelent. A jelenleg használatban lévő túlnyomó többsége az IBM System Z Servers vagy az IBM plug-kompatibilis rendszerek, amelyek MVS, DOS, VSE, OS/390 vagy Z/OS rendszert futtatnak.

Az Azure virtuális gépek (VM-EK) egy adott alkalmazás erőforrásainak elkülönítésére és kezelésére szolgálnak egyetlen példányon. A nagyszámítógépek, például az IBM z/OS, logikai partíciókat (LPARS) használnak erre a célra. A nagyszámítógépek használhatnak egy LPAR egy CICS-régióhoz társított COBOL-programokkal, valamint egy külön LPAR az IBM DB2-adatbázishoz. Az [Azure-](/azure/architecture/reference-architectures/n-tier/n-tier-sql-server) ban egy tipikus n szintű alkalmazás telepíti az Azure-beli virtuális gépeket egy virtuális hálózatba, amely az egyes rétegek alhálózatokra osztható.

Az Azure-beli virtuális gépek olyan nagyszámítógép-emulációs környezeteket és fordítókat futtathatnak, amelyek támogatják a lift és a SHIFT forgatókönyveket. A fejlesztés és a tesztelés gyakran a mainframe-ről az Azure dev/test Environment-ba való Migrálás első munkaterhelései közé tartozik. Az alábbi ábrán láthatók az olyan általános kiszolgáló-összetevők, amelyekkel emulálható az online tranzakciós folyamat (OLTP), a Batch és az adatfeldolgozási rendszerek is.

![Az Azure-beli emulációs környezetek lehetővé teszik a z/OS-alapú rendszerek futtatását.](media/01-overview.png)

Egyes nagyszámítógép-munkaterhelések áttelepíthetők az Azure-ba viszonylag könnyen, míg mások az Azure-ban egy partneri megoldás használatával áthelyezhetők. A partneri megoldások kiválasztásával kapcsolatos részletes útmutatást az [Azure mainframe Migration Center](https://azure.microsoft.com/migration/mainframe/) nyújt segítséget.

## <a name="mainframe-migration"></a>Nagyszámítógép migrálása

Újrafuttatja, újraépíti, lecseréli vagy kivonja? IaaS vagy Péter? A nagyvállalati alkalmazások megfelelő áttelepítési stratégiájának meghatározásához tekintse meg a [Nagyszámítógépi áttelepítési](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)  útmutatót a Azure Architecture Center.

## <a name="micro-focus-rehosting-platform"></a>A Micro Focus platform újraüzemeltetése

A Micro Focus Enterprise Server az egyik legnagyobb nagyvállalati szolgáltatói platform, amely elérhető. Használhatja a z/OS számítási feladatait egy kevésbé költséges x86 platformon az Azure-ban.

Első lépések:

- [Enterprise Server és Enterprise Developer telepítése az Azure-ban](./microfocus/set-up-micro-focus-azure.md)
- [Az Azure-beli Enterprise Developer CICS BankDemo beállítása](./microfocus/demo.md)
- [Vállalati kiszolgáló futtatása Docker-tárolóban az Azure-ban](./microfocus/run-enterprise-server-container.md)


## <a name="tmaxsoft-openframe-on-azure"></a>TmaxSoft OpenFrame Kiindulópontul az Azure-ban

A TmaxSoft OpenFrame Kiindulópontul egy olyan népszerű nagyszámítógép-áthelyezési megoldás, amelyet lift-és Eltolásos helyzetekben használunk. Az Azure-beli OpenFrame Kiindulópontul-környezetek fejlesztési, bemutatók, tesztelési és éles számítási feladatokhoz használhatók.

Első lépések:

- [Ismerkedés a TmaxSoft OpenFrame Kiindulópontul](./tmaxsoft/get-started.md)
- [Az ebook letöltése](https://azure.microsoft.com/resources/install-tmaxsoft-openframe-on-azure/)

## <a name="ibm-zdt-120"></a>IBM&T 12,0

Az IBM Z fejlesztési és tesztelési környezet (IBM&T) az Azure-ban olyan nem éles környezetben működő környezetet állít be, amely a Z/OS-alapú alkalmazások fejlesztéséhez, teszteléséhez és demói számára használható.

Az Azure-beli emulációs környezet különböző típusú Z-példányokat képes üzemeltetni az Application Developers által vezérelt disztribúciók (ADCDs-EK) segítségével. Az Azure-on és a Azure Stack-on a&T Personal Edition, a Sysplex és a&a következőt is futtathatja: az Azure-ban és a&T Enterprise Edition.

Első lépések:

- [Az IBM&T 12,0 beállítása az Azure-ban](./ibm/install-ibm-z-environment.md)
- [A ADCD beállítása a&T-re](./ibm/demo.md)

## <a name="ibm-db2-purescale-on-azure"></a>IBM DB2-pureScale az Azure-ban

Az IBM DB2 pureScale-környezet az Azure-hoz készült adatbázis-fürtöt biztosít. Nem azonos az eredeti környezettel, de hasonló rendelkezésre állást és méretezést biztosít, mint az IBM DB2 a párhuzamos Sysplex-telepítésben futó z/OS-hez.

Első lépésként tekintse [meg az IBM DB2 pureScale az Azure](.//ibm/ibm-db2-purescale-azure.md)-ban című témakört.

## <a name="considerations"></a>Megfontolandó szempontok

Ha nagyszámítógépes számítási feladatokat telepít át az Azure-infrastruktúra szolgáltatásként (IaaS), többféle igény szerinti, méretezhető számítástechnikai erőforrás közül választhat, beleértve az Azure-beli virtuális gépeket is. Az Azure [Linux](../../linux/overview.md) -és Windows- [alapú](../../windows/overview.md) virtuális gépek széles választékát kínálja.

### <a name="compute"></a>Compute

Az Azure számítási teljesítménye kedvezően hasonlít a nagyszámítógépek kapacitására. Ha úgy gondolja, hogy egy nagyszámítógépi munkaterhelést az Azure-ba helyez át, hasonlítsa össze a 1 000 000 utasítások másodpercenkénti számát (MIPS) a virtuális processzorokra. 

Ismerje meg, hogyan [helyezheti át a nagyszámítógépeket az Azure-](./concepts/mainframe-compute-azure.md)ba.

### <a name="high-availability-and-failover"></a>Magas rendelkezésre állás és feladatátvétel

Az Azure kötelezettségvállalási alapú szolgáltatói szerződéseket (SLA-kat) kínál. A több-kilences rendelkezésre állás az alapértelmezett, a SLA-kat pedig a szolgáltatások helyi vagy geo alapú replikálásával lehet optimalizálni. Az Azure egészére vonatkozó rendelkezésre állási garancia magyarázata a teljes [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/)-ban található.

Az Azure IaaS, például a virtuális gépek esetében az adott rendszerfunkciók feladatátvételi támogatást biztosítanak, például a feladatátvételi fürtszolgáltatás példányai és a rendelkezésre állási készletek. Ha az Azure platformot szolgáltatásként (Pásti) használja, a platform automatikusan kezeli a feladatátvételt. Ilyenek például a [Azure SQL Database](../../../azure-sql/database/sql-database-paas-overview.md) és a [Azure Cosmos db](../../../cosmos-db/introduction.md).

### <a name="scalability"></a>Méretezhetőség

A nagyszámítógépek jellemzően vertikális felskálázást, míg a felhőalapú környezetek felskálázása. Az Azure számos [Linux](../../sizes.md) -és [Windows](../../sizes.md) -méretet kínál, hogy megfeleljen az igényeinek. A felhő a pontos felhasználói specifikációknak megfelelően felfelé vagy lefelé is méretezhető. A számítási teljesítmény, a tárolás és a szolgáltatások igény szerinti [skálázása](/azure/architecture/best-practices/auto-scaling) használaton alapuló számlázási modell keretében történik.

### <a name="storage"></a>Tárolás

A felhőben számos rugalmas és skálázható tárolási lehetőség áll rendelkezésre, és csak azért kell fizetnie, amire szüksége van. Az [Azure Storage](../../../storage/common/storage-introduction.md) egy nagymértékben méretezhető objektum-tárolót biztosít az adatobjektumokhoz, egy fájlrendszer-szolgáltatáshoz a felhőhöz, egy megbízható üzenetkezelési tárolóhoz és egy NoSQL-tárolóhoz. A virtuális gépek esetében a felügyelt és a nem felügyelt lemezek állandó, biztonságos lemezes tárolást biztosítanak.

Ismerje meg, hogyan [helyezheti át a mainframe-tárolót az Azure-](./concepts/mainframe-storage-azure.md)ba.

### <a name="backup-and-recovery"></a>Biztonsági másolat és helyreállítás

A saját vész-helyreállítási hely fenntartása költséges lehet. Az Azure könnyen megvalósítható és költséghatékony lehetőségeket kínál a [biztonsági mentéshez](../../../backup/backup-overview.md), a [helyreállításhoz](../../../site-recovery/site-recovery-overview.md)és a [Redundanciához](../../../storage/common/storage-redundancy.md) helyi vagy regionális szinten, vagy geo-redundancia segítségével.

## <a name="azure-government-for-mainframe-migrations"></a>Azure Government a nagyszámítógépek áttelepítéséhez

Számos állami intézmény szereti a nagyvállalati alkalmazásokat egy modern, rugalmas platformra helyezni. Microsoft Azure Government a globális Microsoft Azure platform fizikailag elkülönített példánya, amely szövetségi, állami és helyi kormányzati rendszerekhez van csomagolva. Világszínvonalú biztonsági, védelmi és megfelelőségi szolgáltatásokat biztosít, különösen Egyesült Államok kormányzati szervek és azok partnerei számára.

Azure Government egy ideiglenes hatóságot (P-ATO) kapott ahhoz, hogy a FedRAMP nagy hatással legyen az ilyen típusú környezetet igénylő rendszerekre.

Első lépésként töltse le [Microsoft Azure Government felhőt a nagyszámítógépek alkalmazásaihoz](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/en-us/).

## <a name="next-steps"></a>Következő lépések

Kérje [partnereit](partner-workloads.md) , hogy segítsenek áttelepíteni vagy áthelyezni a nagyszámítógépű alkalmazásokat. 

Lásd még:

- [A nagyszámítógépekkel kapcsolatos témákról szóló tanulmány](mainframe-white-papers.md)
- [Nagyszámítógép migrálása](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [Hibaelhárítás](../../troubleshooting/index.yml)
- [Demystifying mainframe – Azure Migrálás](https://azure.microsoft.com/resources/demystifying-mainframe-to-azure-migration/)

<!-- INTERNAL LINKS -->
[microfocus-get-started]: /microfocus/get-started.md
[microfocus-setup]: /microfocus/set-up-micro-focus-azure.md
[microfocus-demo]: /microfocus/demo.md
[ibm-get-started]: /ibm/get-started.md
[ibm-install-z]: /ibm/install-ibm-z-environment.md
[ibm-demo]: /ibm/demo.md