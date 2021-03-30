---
title: Kapacitás megtervezése a VMware vész-helyreállításhoz Azure Site Recovery
description: Ez a cikk segít megtervezni a kapacitást és a skálázást a VMware virtuális gépek Azure-ba való vész-helyreállításának Azure Site Recovery használatával történő beállításakor.
author: nsoneji
manager: garavd
ms.service: site-recovery
ms.date: 4/9/2019
ms.topic: conceptual
ms.author: ramamill
ms.openlocfilehash: 4b86d0c189bcf0687a703f2338188df2090feaf0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92368026"
---
# <a name="plan-capacity-and-scaling-for-vmware-disaster-recovery-to-azure"></a>Kapacitás megtervezése és méretezése a VMware vész-helyreállításhoz az Azure-ba

Ebből a cikkből megtervezheti a kapacitást és a skálázást, amikor a helyszíni VMware virtuális gépeket és fizikai kiszolgálókat az Azure-ba replikálja [Azure site Recovery](site-recovery-overview.md)használatával.

## <a name="how-do-i-start-capacity-planning"></a>Hogyan megkezdi a kapacitás megtervezését?

A Azure Site Recovery infrastrukturális követelmények megismeréséhez a [Azure site Recovery Deployment Planner](./site-recovery-deployment-planner.md) a VMware-replikációhoz való futtatásával gyűjtsön információkat a replikációs környezetről. További információ: [tudnivalók Site Recovery Deployment Planner VMware-ről Azure-](site-recovery-deployment-planner.md)ra. 

A Site Recovery Deployment Planner egy olyan jelentést biztosít, amely teljes körű információkkal szolgál a kompatibilis és nem kompatibilis virtuális gépekről, a virtuális gépekről és a lemezeken tárolt adatokról. Az eszköz a hálózati sávszélességre vonatkozó követelményeket is összegzi, hogy megfeleljen a RPO és a sikeres replikáláshoz és feladatátvételi teszthez szükséges Azure-infrastruktúrának.

## <a name="capacity-considerations"></a>Kapacitással kapcsolatos szempontok

Összetevő | Részletek
--- | ---
**Replikáció** | **Maximális napi változási arány**: egy védett gép csak egy folyamat-kiszolgálót tud használni. Egy folyamatkiszolgáló legfeljebb 2 TB napi változást tud kezelni. Így a 2 TB a védett gépek által támogatott maximális napi adatváltozási sebesség.<br /><br /> **Maximális átviteli sebesség**: a replikált gép egy Azure-beli Storage-fiókhoz tartozhat. Egy standard Azure Storage-fiók másodpercenként legfeljebb 20 000 kérelmet képes kezelni. Azt javasoljuk, hogy a másodpercenkénti bemeneti/kimeneti műveletek másodpercenkénti számát (IOPS) korlátozza 20 000-re. Ha például van egy olyan forrásoldali számítógépe, amely öt lemezzel rendelkezik, és mindegyik lemez 120 IOPS (8 K méretű) hoz létre a forrásoldali gépen, akkor a forrásszámítógép a 500-es Azure-lemezes IOPS-korláton belül van. (A szükséges Storage-fiókok száma a 20 000-as számú teljes forrásoldali gépi IOPS egyenlő.)
**Konfigurációs kiszolgáló** | A konfigurációs kiszolgálónak képesnek kell lennie kezelni a napi változási arányt a védett gépeken futó összes munkaterhelésen. A konfigurációs gépnek elegendő sávszélességgel kell rendelkeznie ahhoz, hogy folyamatosan replikálja az Azure Storage-ba.<br /><br /> Az ajánlott eljárás az, hogy a konfigurációs kiszolgálót ugyanarra a hálózati és LAN-szegmensre helyezze, mint a védelemmel ellátni kívánt gépeket. A konfigurációs kiszolgálót egy másik hálózaton helyezheti el, de a védelemmel ellátni kívánt gépeknek 3. rétegbeli hálózati láthatósággal kell rendelkezniük.<br /><br /> A konfigurációs kiszolgáló méretére vonatkozó javaslatokat a következő szakaszban található táblázat foglalja össze.
**Folyamatkiszolgáló** | Alapértelmezés szerint az első folyamat-kiszolgáló települ a konfigurációs kiszolgálón. A környezet skálázásához további folyamat-kiszolgálókat is telepíthet. <br /><br /> A Process kiszolgáló fogadja a replikációs adatok védelmét a védett gépekről. A Process Server gyorsítótárazással, tömörítéssel és titkosítással optimalizálja az adatátvitelt. Ezután a Process Server elküldi az adatokat az Azure-nak. A folyamat-kiszolgáló számítógépnek elegendő erőforrással kell rendelkeznie a feladatok végrehajtásához.<br /><br /> A Process Server lemezes gyorsítótárat használ. Egy különálló, 600 GB-os gyorsítótár-lemez használatával kezelheti az adatváltozásokat, amelyeket a hálózati torlódás vagy leállás esetén tárol.

## <a name="size-recommendations-for-the-configuration-server-and-inbuilt-process-server"></a>A konfigurációs kiszolgáló és a beépített folyamat-kiszolgáló méretével kapcsolatos javaslatok

Egy olyan konfigurációs kiszolgáló, amely egy beépített folyamat-kiszolgálót használ a munkaterhelések elleni védelemhez, az alábbi konfigurációk alapján akár 200 virtuális gépet is kezelhet:

CPU | Memory (Memória) | Gyorsítótárazott lemez mérete | Adatváltozási arány | Védett gépek
--- | --- | --- | --- | ---
8 vCPU (2 szoftvercsatorna * 4 mag \@ 2,5 GHz) | 16 GB | 300 GB | 500 GB vagy kevesebb | Kevesebb mint 100 gép replikálására használható.
12 vCPU (2 szoftvercsatorna * 6 mag \@ 2,5 GHz) | 18 GB | 600 GB | 501 GB – 1 TB | 100 és 150 gép közötti replikálásra használható.
16 vCPU (2 szoftvercsatorna * 8 mag \@ 2,5 GHz) | 32 GB | 1 TB | 1 TB >2 TB | 151 és 200 gép közötti replikálásra használható.
Helyezzen üzembe egy másik konfigurációs kiszolgálót egy [OVF-sablon](vmware-azure-deploy-configuration-server.md#deploy-a-configuration-server-through-an-ova-template)használatával. | | | | Ha több mint 200 gépet replikál, helyezzen üzembe egy új konfigurációs kiszolgálót.
Helyezzen üzembe egy másik [Process Servert](vmware-azure-set-up-process-server-scale.md#download-installation-file). | | | 2 TB >| Ha a teljes napi adatváltozási arány meghaladja a 2 TB-ot, helyezzen üzembe egy új kibővíthető folyamat-kiszolgálót.

Ezekben a konfigurációkban:

* Mindegyik forrásoldali gépen három lemez 100 GB.
* 8 közös hozzáférésű, 10 K/s/s fordulatszámú, a gyorsítótár-mérési lemezek mérésére szolgáló RAID 10-es adatátviteli meghajtók teljesítményére használták.

## <a name="size-recommendations-for-the-process-server"></a>A folyamat-kiszolgáló méretével kapcsolatos javaslatok

A Process Server a Azure Site Recovery adatreplikálását kezelő összetevő. Ha a napi változási arány meghaladja a 2 TB-ot, fel kell vennie a kibővített folyamat-kiszolgálókat a replikálási terhelés kezelésére. A vertikális felskálázáshoz a következőket teheti:

* Növelje a konfigurációs kiszolgálók számát egy [OVF-sablon](vmware-azure-deploy-configuration-server.md#deploy-a-configuration-server-through-an-ova-template)használatával történő telepítéssel. Például két konfigurációs kiszolgáló használatával akár 400 gépet is védetté tehet.
* Bővítő [folyamat-kiszolgálók](vmware-azure-set-up-process-server-scale.md#download-installation-file)hozzáadása. A kibővített folyamat-kiszolgálók használatával kezelheti a replikációs forgalmat a konfigurációs kiszolgáló (vagy azon kívül) helyett.

A következő táblázat ismerteti ezt a forgatókönyvet:

* Kibővíthető folyamat-kiszolgálót állíthat be.
* A védett virtuális gépeket a kibővíthető folyamat kiszolgálójának használatára konfigurálta.
* Mindegyik védett forrásoldali gépen három lemez 100 GB.

További folyamat-kiszolgáló | Gyorsítótárazott lemez mérete | Adatváltozási arány | Védett gépek
--- | --- | --- | ---
4 vCPU (2 szoftvercsatorna * 2 mag \@ 2,5 GHz), 8 GB memória | 300 GB | 250 GB vagy kevesebb | 85 vagy kevesebb gép replikálására használható.
8 vCPU (2 szoftvercsatorna * 4 mag \@ 2,5 GHz), 12 GB memória | 600 GB | 251 GB – 1 TB | 86 és 150 gép közötti replikálásra használható.
12 vCPU (2 szoftvercsatorna * 6 mag \@ 2,5 GHz) 24 GB memória | 1 TB | 1 TB >2 TB | 151 és 225 gép közötti replikálásra használható.

A kiszolgálók méretezésének módja a vertikális Felskálázási vagy kibővíthető modell igényeitől függ. A vertikális felskálázáshoz helyezzen üzembe néhány magas szintű konfigurációs kiszolgálót és feldolgozó kiszolgálót. A felskálázáshoz több, kevesebb erőforrással rendelkező kiszolgálót helyezzen üzembe. Ha például a 200-es gépet a 1,5 TB teljes napi adatváltozási arányával szeretné védetté tenni, a következő műveletek egyikét végezheti el:

* Egyetlen folyamat kiszolgálójának beállítása (16 vCPU, 24 GB RAM).
* Két folyamat kiszolgáló beállítása (2 x 8 vCPU, 2 * 12 GB RAM).

## <a name="control-network-bandwidth"></a>Hálózati sávszélesség szabályozása

Miután a [Site Recovery Deployment Planner](site-recovery-deployment-planner.md) használatával kiszámítja a replikációhoz szükséges sávszélességet (a kezdeti replikálást, majd a különbözetet), több lehetőség közül választhat a replikációhoz használt sávszélesség mennyiségének szabályozásához:

* **Sávszélesség szabályozása**: az Azure-ba replikált VMware-forgalom egy adott folyamat-kiszolgálón halad át. A sávszélesség szabályozása a folyamat-kiszolgálóként futó gépeken végezhető el.
* A **sávszélesség befolyásolása**: befolyásolhatja a replikációhoz használt sávszélességet néhány beállításkulcs használatával:
  * A **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM** beállításazonosító meghatározza, hogy hány szálat kell használni a lemez adatátviteléhez (a kezdeti vagy a különbözeti replikációhoz). A magasabb érték növeli a replikáláshoz használt hálózati sávszélességet.
  * A **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\DownloadThreadsPerVM** beállításazonosító meghatározza, hogy hány szálat használ a rendszer az adatátvitelhez a feladat-visszavétel során.

### <a name="throttle-bandwidth"></a>Sávszélesség szabályozása

1. Nyissa meg a Azure Backup MMC beépülő modult a folyamat-kiszolgálóként használt gépen. Alapértelmezés szerint a biztonsági mentés parancsikonja az asztalon vagy a következő mappában érhető el: C:\Program Files\Microsoft Azure Recovery Services Agent\bin.
2. A beépülő modulban válassza a **Tulajdonságok módosítása** lehetőséget.

    ![Képernyőkép a Azure Backup MMC beépülő modul lehetőségről a tulajdonságok módosításához](./media/site-recovery-vmware-to-azure/throttle1.png)
3. A **szabályozás** lapon válassza az **Internet sávszélesség-használat szabályozásának engedélyezése a biztonsági mentési műveletekhez** lehetőséget. A munkamennyiség és a munkaidőn kívüli munkaidő korlátozásának beállítása. Az érvényes tartományok 512 kbps és 1 023 Mbps között vannak.

    ![A Azure Backup tulajdonságai párbeszédpanel képernyőképe](./media/site-recovery-vmware-to-azure/throttle2.png)

A szabályozáshoz a [Set-OBMachineSetting](/previous-versions/windows/powershell-scripting/hh770409(v=wps.640)) parancsmag is használható. Bemutatunk egy példát:

```azurepowershell-interactive
$mon = [System.DayOfWeek]::Monday
$tue = [System.DayOfWeek]::Tuesday
Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)
```

A **Set-OBMachineSetting -NoThrottle** beállítás azt jelenti, hogy nincs szükség szabályozásra.

### <a name="alter-the-network-bandwidth-for-a-vm"></a>A virtuális gép hálózati sávszélességének módosítása

1. A virtuális gép beállításjegyzékében lépjen a **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication** elemre.
   * A replikálási lemez sávszélesség-forgalmának megváltoztatásához módosítsa a **UploadThreadsPerVM** értékét. Ha nem létezik, hozza létre a kulcsot.
   * Ha módosítani szeretné az Azure-beli feladat-visszavételi forgalom sávszélességét, módosítsa a **DownloadThreadsPerVM** értékét.
2. Az egyes kulcsok alapértelmezett értéke **4**. A szükségesnél több erőforrással ellátott hálózatban érdemes módosítani a beállításazonosítók alapértelmezett értékét. A maximálisan használható érték **32**. Az optimális érték kiválasztásához kövesse figyelemmel a forgalmat.

## <a name="set-up-the-site-recovery-infrastructure-to-protect-more-than-500-vms"></a>A Site Recovery infrastruktúra beállítása több mint 500 virtuális gép elleni védelemhez

A Site Recovery-infrastruktúra beállítása előtt a környezettel a következő tényezőket mérhetővé teheti: kompatibilis virtuális gépek, a napi adatváltozási arány, az elérni kívánt RPO szükséges hálózati sávszélesség, a szükséges Site Recovery-összetevők száma, valamint a kezdeti replikálás befejezésének ideje. A szükséges információk összegyűjtéséhez hajtsa végre a következő lépéseket:

1. A paraméterek méréséhez futtassa Site Recovery Deployment Plannert a környezetében. A hasznos útmutatást lásd: [Tudnivalók a VMware – Azure-hoz készült Site Recovery Deployment Plannerról](site-recovery-deployment-planner.md).
2. Helyezzen üzembe olyan konfigurációs kiszolgálót, amely megfelel a [konfigurációs kiszolgáló méretére vonatkozó javaslatoknak](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-configuration-server-and-inbuilt-process-server). Ha az üzemi munkaterhelés meghaladja a 650-es virtuális gépeket, helyezzen üzembe egy másik konfigurációs kiszolgálót.
3. A mért napi adatváltozási arány alapján a [méretre vonatkozó irányelvek](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-process-server)segítségével telepítse a [kibővíthető folyamat-kiszolgálókat](vmware-azure-set-up-process-server-scale.md#download-installation-file) .
4. Ha várhatóan egy lemezes virtuális gép adatváltozási sebessége meghaladja a 2 MBps-ot, győződjön meg arról, hogy prémium szintű felügyelt lemezeket használ. Site Recovery Deployment Planner egy adott időszakban fut. Előfordulhat, hogy a jelentés nem rögzíti az adatváltozási arányban lévő csúcsokat más időpontokban.
5. [Állítsa be a hálózati sávszélességet](site-recovery-plan-capacity-vmware.md#control-network-bandwidth) a elérni kívánt RPO alapján.
6. Ha az infrastruktúra be van állítva, engedélyezze a vész-helyreállítást a munkaterhelés számára. További információ: [a forrás-környezet beállítása VMware-ről Azure-ba történő replikálásra](vmware-azure-set-up-source.md).

## <a name="deploy-additional-process-servers"></a>További folyamat-kiszolgálók üzembe helyezése

Ha az üzembe helyezést az 200-as számú forrásoldali gépen túlra bővíti, vagy ha a teljes napi adatváltozási arány meghaladja a 2 TB-ot, fel kell vennie a folyamat-kiszolgálókat a forgalmi mennyiség kezelésére. Bővítettük a terméket a 9,24-es verzióban, hogy a [Process Server-riasztások](vmware-physical-azure-monitor-process-server.md#process-server-alerts) biztosítva legyenek a kibővíthető folyamat kiszolgálójának beállításakor. [Állítsa be úgy a folyamatot](vmware-azure-set-up-process-server-scale.md) , hogy megvédje az új forrásokkal rendelkező gépeket, vagy [kiegyenlítse a terhelést](vmware-azure-manage-process-server.md#move-vms-to-balance-the-process-server-load).

### <a name="migrate-machines-to-use-the-new-process-server"></a>Gépek migrálása az új Process Server használatára

1. Válassza a **Beállítások**  >  **site Recovery kiszolgálók** lehetőséget. Válassza ki a konfigurációs kiszolgálót, majd bontsa ki a **folyamat-kiszolgálók** elemet.

    ![A folyamat-kiszolgáló párbeszédpanel képernyőképe](./media/site-recovery-vmware-to-azure/migrate-ps2.png)
2. Kattintson a jobb gombbal a jelenleg használt Process Server elemre, majd válassza a **váltás** lehetőséget.

    ![A konfigurációs kiszolgáló párbeszédpanel képernyőképe](./media/site-recovery-vmware-to-azure/migrate-ps3.png)
3. A **cél-feldolgozási kiszolgáló kiválasztása** lapon válassza ki a használni kívánt új folyamatot. Ezután válassza ki azokat a virtuális gépeket, amelyeket a kiszolgáló kezelni fog. Ha információt szeretne kapni a kiszolgálóról, válassza ki az információs ikont. A betöltési döntések elvégzéséhez az egyes kiválasztott virtuális gépeknek az új folyamat-kiszolgálóra való replikálásához szükséges átlagos terület jelenik meg. Jelölje be a jelölőnégyzetet, ha meg szeretné kezdeni az új folyamat-kiszolgálóra való replikálást.

## <a name="deploy-additional-master-target-servers"></a>További fő célkiszolgáló üzembe helyezése

A következő esetekben több fő célkiszolgáló szükséges:

*   Egy Linux-alapú virtuális gépet szeretne védelemmel ellátni.
*   A konfigurációs kiszolgálón elérhető fő célkiszolgáló nem fér hozzá a virtuális gép adattárához.
*   A fő célkiszolgálón lévő lemezek teljes száma (a kiszolgálón található helyi lemezek száma és a védendő lemezek száma) nagyobb, mint 60 lemez.

A Linux-alapú virtuális gépek fő célkiszolgáló hozzáadásával kapcsolatos további információkért lásd: [Linux fő célkiszolgáló telepítése feladat-visszavételhez](vmware-azure-install-linux-master-target.md).

Fő célkiszolgáló hozzáadása Windows-alapú virtuális géphez:

1. Nyissa meg **Recovery Services** tároló  >  **site Recovery infrastruktúra**-  >  **konfigurációs kiszolgálók** lehetőséget.
2. Válassza ki a szükséges konfigurációs kiszolgálót, majd válassza a **fő célkiszolgáló** elemet.

    ![A fő célkiszolgáló hozzáadása gombot megjelenítő képernyőkép](media/site-recovery-plan-capacity-vmware/add-master-target-server.png)
3. Töltse le az egyesített telepítő fájlt, majd futtassa a fájlt a virtuális gépen a fő célkiszolgáló beállításához.
4. Válassza a **fő cél telepítése**  >  **tovább** lehetőséget.

    ![A fő cél telepítése lehetőség kiválasztását bemutató képernyőkép](media/site-recovery-plan-capacity-vmware/choose-MT.PNG)
5. Válassza ki az alapértelmezett telepítési helyet, majd válassza a **telepítés** lehetőséget.

     ![Az alapértelmezett telepítési helyet megjelenítő képernyőkép](media/site-recovery-plan-capacity-vmware/MT-installation.PNG)
6. Ha a fő célt a konfigurációs kiszolgálóval szeretné regisztrálni, válassza a **Folytatás a konfigurációhoz** lehetőséget.

    ![A folytatás a konfigurációhoz gombot megjelenítő képernyőkép](media/site-recovery-plan-capacity-vmware/MT-proceed-configuration.PNG)
7. Adja meg a konfigurációs kiszolgáló IP-címét, majd írja be a jelszót. A hozzáférési kód létrehozásával kapcsolatos további információkért lásd: [konfigurációs kiszolgáló jelszavainak](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase)létrehozása. 

    ![A konfigurációs kiszolgáló IP-címének és jelszavának megadását bemutató képernyőkép](media/site-recovery-plan-capacity-vmware/cs-ip-passphrase.PNG)
8. Válassza a **Regisztráció** lehetőséget. A regisztráció befejeződése után válassza a **Befejezés** lehetőséget.

A regisztráció sikeres befejeződése után a kiszolgáló megjelenik a Azure Portal **Recovery Services** tárolóban  >  **site Recovery infrastruktúra**  >  -**konfigurációs kiszolgálók** csomópontban a konfigurációs kiszolgáló fő célkiszolgálón.

 > [!NOTE]
 > Töltse le a [Windows rendszerhez készült fő célkiszolgáló egyesített telepítési fájljának](https://aka.ms/latestmobsvc)legújabb verzióját.

## <a name="next-steps"></a>Következő lépések

[Site Recovery Deployment Planner](https://aka.ms/asr-deployment-planner)letöltése és futtatása.