---
title: HANA feladatátvételi eljárás a SAP HANA Azure-ban (nagyméretű példányok) lévő vészhelyzeti helyre | Microsoft Docs
description: Feladatátvétel végrehajtása vész-helyreállítási webhelyeken az Azure-beli SAP HANA esetén (nagyméretű példányok)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/22/2019
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2a33340524556f5da1703cae3532f053fbe8ba13
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101670989"
---
# <a name="disaster-recovery-failover-procedure"></a>Vészhelyreállítási feladatátvételi eljárás


>[!IMPORTANT]
>Ez a cikk nem helyettesíti a SAP HANA adminisztrációs dokumentációt vagy az SAP-megjegyzéseket. Reméljük, hogy alapos ismeretekkel rendelkezik a SAP HANA adminisztrációval és műveletekkel kapcsolatban, különösen a biztonsági mentés, a visszaállítás, a magas rendelkezésre állás és a vész-helyreállítás (DR) terén. Ebben a cikkben a SAP HANA studióból származó képernyőképek jelennek meg. A tartalom, a struktúra és az SAP felügyeleti eszközeinek és az eszközöknek a tulajdonságai megváltozhatnak SAP HANA kiadásról a kiadásra.

A DR-helyre történő feladatátvétel során két esetet érdemes figyelembe venni:

- A SAP HANA-adatbázisnak vissza kell térnie az adatmennyiség legújabb állapotára. Ebben az esetben létezik egy önkiszolgáló parancsfájl, amellyel elvégezheti a feladatátvételt anélkül, hogy kapcsolatba kell lépnie a Microsofttal. A feladat-visszavételhez a Microsofttal kell dolgoznia.
- Olyan tárolási pillanatképre kíván visszaállítani, amely nem a legújabb replikált pillanatkép. Ebben az esetben a Microsofttal kell dolgoznia. 

>[!NOTE]
>A következő lépéseket kell elvégezni a HANA nagyméretű példány egységen, amely a DR egységre vonatkozik. 
 
A legújabb replikált tárolási Pillanatképek visszaállításához kövesse a "teljes DR feladatátvétel – azure_hana_dr_failover végrehajtása" című részt az [Azure-beli SAP HANA Microsoft Snapshot Tools eszközében](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.3/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.3.pdf). 

Ha több SAP HANA példány feladatátvételét szeretné végrehajtani, futtassa többször a azure_hana_dr_failover parancsot. Ha szükséges, adja meg a feladatátvételhez és visszaállításhoz használni kívánt SAP HANA SID-t. 


A DR feladatátvételt is tesztelheti a tényleges replikációs kapcsolat befolyásolása nélkül. A feladatátvételi teszt végrehajtásához kövesse az Azure-beli [SAP HANA Microsoft Snapshot-eszközök](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.3/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.3.pdf)"teszt elvégzése azure_hana_test_dr_failover" című szakaszának lépéseit. 

>[!IMPORTANT]
>Ne *futtasson* üzemi tranzakciókat a Dr-helyen a **feladatátvétel tesztelésének** folyamatán keresztül létrehozott példányon. A parancs azure_hana_test_dr_failover olyan kötetek készletét hozza létre, amelyek nem rendelkeznek kapcsolattal az elsődleges hellyel. Ennek eredményeképpen az elsődleges helyhez való szinkronizálás *nem* lehetséges. 

Ha több SAP HANA-példányt szeretne tesztelni, futtassa többször a parancsfájlt. Ha a rendszer kéri, adja meg a feladatátvételi teszthez használni kívánt példány SAP HANA SID-azonosítóját. 

>[!NOTE]
>Ha feladatátvételt szeretne végezni a DR-webhelyre, hogy megmentsen néhány órával korábban törölt adat, és hogy a DR kötetek korábbi pillanatképre legyenek beállítva, akkor ez az eljárás érvényes. 

1. Állítsa le a HANA nem termékre vonatkozó példányát a futtatott HANA nagyméretű példányok vész-helyreállítási egységén. Előre telepítve van egy alvó HANA üzemi példány.
1. Győződjön meg arról, hogy nem fut SAP HANA folyamat. Használja az alábbi parancsot a vizsgálathoz:

      `/usr/sap/hostctrl/exe/sapcontrol –nr <HANA instance number> - function GetProcessList`.

      A kimenetnek le kell mutatnia a **hdbdaemon** folyamatot leállított állapotban, és más HANA-folyamatokat sem futó, sem elindított állapotban kell lennie.
1. Határozza meg, hogy mely pillanatkép-vagy SAP HANA biztonsági mentési AZONOSÍTÓhoz szeretné visszaállítani a vész-helyreállítási helyet. A valós katasztrófa-helyreállítási esetekben ez a pillanatkép általában a legújabb pillanatkép. Ha vissza kell állítania az elveszett adatokat, válasszon egy korábbi pillanatképet.
1. Magas prioritású támogatási kéréssel forduljon az Azure ügyfélszolgálatához. Kérje meg a pillanatkép visszaállítását, valamint a DR helyen található HANA Backup-azonosító nevét és dátumát. Az alapértelmezett érték az, hogy a műveleti oldal csak a/Hana/Data kötetet állítja vissza. Ha azt szeretné, hogy a/Hana/logbackups-kötetek is legyenek, kifejezetten azt kell megadnia. *Ne állítsa vissza a/Hana/Shared kötetet.* Ehelyett a PRD/Hana/Shared-kötetének újracsatlakoztatása után válassza ki az adott fájlokat, például global.ini a **. Snapshot** könyvtárból és annak alkönyvtáraiból. 

   A műveleti oldalon a következő lépések történnek:

   a. A pillanatképek az éles kötetről a vész-helyreállítási kötetekre való replikálása leállt. Előfordulhat, hogy ez a megszakítás már megtörtént, ha az üzemi helyen áramkimaradás történik, ezért a vész-helyreállítási eljárást kell végrehajtania.
   
   b. A rendszer visszaállítja a tárolási pillanatkép nevét vagy pillanatképét a kiválasztott biztonsági mentési AZONOSÍTÓval a vész-helyreállítási köteteken.
   
   c. A visszaállítást követően a vész-helyreállítási kötetek elérhetők lesznek a vész-helyreállítási régióban lévő HANA nagyméretű példány-egységekhez való csatlakoztatáshoz.
      
1. Csatlakoztassa a vész-helyreállítási köteteket a HANA nagyméretű példány egységéhez a vész-helyreállítási helyen. 
1. Indítsa el az alvó SAP HANA éles példányát.
1. Ha úgy dönt, hogy a tranzakciós napló biztonsági mentési naplóit a RPO idő csökkentése érdekében másolja, egyesítse a tranzakciónaplók biztonsági másolatait az újonnan csatlakoztatott DR/Hana/logbackups könyvtárba. Ne írja felül a meglévő biztonsági mentéseket. Olyan újabb biztonsági másolatok másolása, amelyek nem voltak replikálva a tárolási Pillanatképek legutóbbi replikálásával.
1. Az egyetlen fájlt is visszaállíthatja azon Pillanatképek közül, amelyek nem replikálódtak a DR Azure-régióban található/hana/shared/PRD-kötetre.

A következő lépések bemutatják, hogyan állíthatja helyre a SAP HANA üzemi példányt a visszaállított tárolási pillanatkép és a rendelkezésre álló tranzakciónaplók biztonsági másolatai alapján.

1. A SAP HANA Studio használatával módosítsa a biztonsági mentési helyet a **/Hana/logbackups** .

   ![A DR helyreállítás biztonsági mentési helyének módosítása](./media/hana-overview-high-availability-disaster-recovery/change_backup_location_dr1.png)

1. SAP HANA átvizsgálja a biztonságimásolat-fájl helyét, és a legutóbbi tranzakciónapló biztonsági mentését javasolja a visszaállításhoz. A vizsgálat eltarthat néhány percig, amíg a következőhöz hasonló képernyő jelenik meg:

   ![A DR helyreállításhoz szükséges tranzakciónaplók listája](./media/hana-overview-high-availability-disaster-recovery/backup_list_dr2.PNG)

1. Módosítsa az alapértelmezett beállítások némelyikét:

      - Törölje a **különbözeti biztonsági mentések használata** jelölőnégyzet jelölését.
      - Válassza a **naplózási körzet inicializálása** elemet.

   ![A napló inicializálási körzetének beállítása](./media/hana-overview-high-availability-disaster-recovery/initialize_log_dr3.PNG)

1. Válassza a **Befejezés** gombot.

   ![A DR visszaállítás befejezése](./media/hana-overview-high-availability-disaster-recovery/finish_dr4.PNG)

Ekkor megjelenik egy folyamatjelző ablak, például az itt látható. Ne feledje, hogy a példa egy három csomópontos kibővíthető SAP HANA konfiguráció vész-helyreállítási visszaállítására szolgál.

![Visszaállítási folyamat](./media/hana-overview-high-availability-disaster-recovery/restore_progress_dr5.PNG)

Ha a visszaállítás nem válaszol a **befejezési** képernyőn, és nem jeleníti meg a folyamatjelzőt, győződjön meg arról, hogy a munkavégző csomópontokon lévő összes SAP HANA példány fut. Ha szükséges, indítsa el manuálisan a SAP HANA példányokat.


## <a name="failback-from-a-dr-to-a-production-site"></a>Feladat-visszavétel DR-ről üzemi helyre
A DR-ről egy üzemi helyre végezhető el a feladat-visszavétel. Nézzük meg azt a forgatókönyvet, amelyben a vész-helyreállítási helyre irányuló feladatátvételt az éles környezetbeli Azure-régióban felmerülő problémák okozták, nem pedig az elveszett adatok helyreállításához szükséges. 

Az SAP éles számítási feladatait egy ideig futtatta a vész-helyreállítási helyen. Ahogy az üzemi helyen felmerülő problémák megoldhatók, az éles helyre vissza kíván térni. Mivel nem veszítheti el az adatvesztést, az üzemi helyre történő visszalépés több lépést is magában foglal, és szoros együttműködést folytat az Azure Operations csapat SAP HANAával. Az operatív csapatot úgy indíthatja el, hogy a problémák megoldása után ismét szinkronizálja az üzemi helyre.

Kövesse az alábbi lépéseket:

1. Az Azure Operations csapatának SAP HANA beolvassa az indítást, hogy szinkronizálja az üzemi tárolási köteteket a vész-helyreállítási tárolási kötetektől, amelyek mostantól a termelési állapotot jelölik. Ebben az állapotban az üzemi helyen lévő HANA nagyméretű példány egység leáll.
1. Az Azure Operations csapatának SAP HANA figyeli a replikálást, és gondoskodik róla, hogy a rendszer a tájékoztatása előtt megtekintse.
1. Leállíthatja azokat az alkalmazásokat, amelyek a vész-helyreállítási helyen a termelési HANA-példányt használják. Ezután elvégezheti a HANA-tranzakciónapló biztonsági mentését. Ezután állítsa le a Hana-példányt, amely a vész-helyreállítási helyen lévő HANA nagyméretű példányokon fut.
1. Ha a vész-helyreállítási helyen található HANA nagyméretű példány egységben futó HANA-példány leáll, az operatív csapat manuálisan szinkronizálja a lemez köteteit.
1. Az Azure Operations csapata SAP HANA újra megkezdi a HANA nagyméretű példány egységét az üzemi helyen. Átadják Önnek. Győződjön meg arról, hogy az SAP HANA-példány leállítási állapotban van a HANA nagyméretű példány-egység indítási ideje alatt.
1. Ugyanazokat az adatbázis-visszaállítási lépéseket hajtja végre, amelyeket korábban a feladatátvételi helyreállítási helyhez adott át.

## <a name="monitor-disaster-recovery-replication"></a>Vész-helyreállítási replikáció figyelése

A tárolási replikálási folyamat állapotának figyeléséhez futtassa a parancsfájlt `azure_hana_replication_status` . Ezt a parancsot a vész-helyreállítási helyen futó egységből kell futtatni, hogy az a várt módon működjön. A parancs működése függetlenül attól, hogy aktív-e a replikáció. A parancs a bérlő minden HANA nagyméretű példánya esetében futtatható a vész-helyreállítási helyen. Nem használható a rendszerindító kötet részleteinek beszerzéséhez. 

A paranccsal és a kimenetével kapcsolatos további információkért tekintse meg a "DR replikáció állapotának beolvasása – azure_hana_replication_status" című részt a [Microsoft Snapshot Tools for SAP HANA on Azure](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/snapshot_tools_v4.3/Microsoft%20Snapshot%20Tools%20for%20SAP%20HANA%20on%20Azure%20v4.3.pdf)-ban.


## <a name="next-steps"></a>Következő lépések
- Lásd: [a HANA oldal figyelése és hibáinak megoldása](hana-monitor-troubleshoot.md).
