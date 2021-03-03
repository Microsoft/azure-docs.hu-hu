---
title: 'SAP az Azure-on: támogatott forgatókönyvek Azure-beli virtuális gépekkel'
description: Az Azure Virtual Machines támogatott forgatókönyvek SAP-munkaterheléssel
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.assetid: d7c59cc1-b2d0-4d90-9126-628f9c7a5538
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/11/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 274db058f9f291d720fc350bb23f7bfdde2791e9
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101670932"
---
# <a name="sap-workload-on-azure-virtual-machine-supported-scenarios"></a>SAP-tevékenységprofil az Azure-beli virtuális gépek támogatott forgatókönyveiben
Az SAP NetWeaver, a Business One `Hybris` vagy a S/4HANA rendszerek Azure-architektúrájának tervezése számos különböző lehetőséget kínál a különböző architektúrák és eszközök számára, amelyek segítségével méretezhető, hatékony és nagy mértékben elérhető üzembe helyezést érhet el. Bár a használt operációs rendszertől vagy adatbázis-kezelőtől függ, korlátozások vannak érvényben. A helyszíni támogatás nem minden esetben támogatott ugyanúgy az Azure-ban. Ez a dokumentum a támogatott nem magas rendelkezésre állású konfigurációkon és a magas rendelkezésre állású konfigurációkon és architektúrán keresztül kizárólag az Azure-beli virtuális gépeket használja. A [Hana Large-példányokkal](./hana-overview-architecture.md)támogatott forgatókönyvek esetében tekintse [meg a Hana Large instances által támogatott forgatókönyvek](./hana-supported-scenario.md)című cikket. 


## <a name="2-tier-configuration"></a>kétrétegű konfiguráció
Az SAP kétszintű konfigurációját úgy kell kiépíteni, hogy az SAP adatbázis-kezelő rendszer és az alkalmazás rétegének egy kombinált rétegében legyen kiépítve, amely ugyanazon a kiszolgálón vagy a virtuálisgép-egységen fut. A második szint a felhasználói felület rétegének tekintendő. Kétszintű konfiguráció esetén az adatbázis-kezelő és az SAP-alkalmazás rétege megosztja az Azure-beli virtuális gép erőforrásait. Ennek eredményeképpen a különböző összetevőket úgy kell konfigurálni, hogy azok ne versenyezzenek az erőforrásokhoz. Emellett körültekintően kell eljárnia, hogy ne kelljen túlfizetni a virtuális gép erőforrásait. Ilyen konfiguráció nem biztosít magas rendelkezésre állást, a különböző Azure-összetevőkre vonatkozó [Azure-szolgáltatói szerződéseken](https://azure.microsoft.com/support/legal/sla/) túl.

Egy ilyen konfiguráció grafikus ábrázolása a következőképpen néz ki:

![Egyszerű, kétrétegű konfiguráció](./media/sap-planning-supported-configurations/two-tier-simple-configuration.png)

Ezeket a konfigurációkat a Windows, a Red Hat, a SUSE és a Oracle Linux támogatja a SQL Server, az Oracle, a DB2, a maxDB és az SAP-alapú adatkezelő rendszerek számára a termelési és nem üzemi esetekben. Az adatbázis-kezelői SAP HANAként az ilyen típusú konfigurációk csak nem üzemi esetekben támogatottak. Ez magában foglalja az [Azure HANA nagyméretű példányainak](./hana-overview-architecture.md) üzembe helyezési esetét is.
Az Azure-ban támogatott összes operációs rendszer/adatbázis-kezelő kombináció esetében ez a típusú konfiguráció támogatott. Azonban kötelező beállítani az adatbázis-kezelő és az SAP-összetevők konfigurációját úgy, hogy az adatbázis-kezelő és az SAP-összetevők ne versenyezzenek a memória-és a CPU-erőforrások esetében, és így meghaladják a fizikai rendelkezésre álló erőforrásokat. Ezt úgy kell végrehajtani, hogy korlátozza a memóriát, amelyet az adatbázis-kezelő le lehet foglalni. Az alkalmazások példányain korlátozni kell az SAP kiterjesztett memóriát is. A virtuális gép CPU-felhasználását is figyelnie kell, hogy az összetevők ne maximalizálják a CPU-erőforrásokat. 

> [!NOTE]
> Az éles környezetben futó SAP-rendszerek esetében további magas rendelkezésre állást és végleges vész-helyreállítási konfigurációkat ajánlunk a jelen dokumentum későbbi részében leírtak szerint.


## <a name="3-tier-configuration"></a>háromrétegű konfiguráció
Ilyen konfigurációk esetén az SAP-alkalmazás réteget és az adatbázis-kezelő réteget különböző virtuális gépekre kell elkülöníteni. Ezt általában nagyobb rendszerek esetében teszi lehetővé, és az SAP-alkalmazási réteg erőforrásainál rugalmasabbak lehetnek. A legegyszerűbb beállítás szerint a különböző Azure-összetevőkön kívüli [Azure-szolgáltatói szerződésekkel](https://azure.microsoft.com/support/legal/sla/) nem érhető el magas rendelkezésre állás. 

A grafikus ábrázolás így néz ki:

![Egy egyszerű, 3 rétegből álló konfigurációt bemutató diagram.](./media/sap-planning-supported-configurations/three-tier-simple-configuration.png)

Az ilyen típusú konfigurációt a Windows, a Red Hat, a SUSE és a Oracle Linux támogatja a SQL Server, az Oracle, a DB2, a SAP HANA, a maxDB és az SAP-alapú Rendszerfelügyeleti webszolgáltatások rendszeréhez a termelési és nem üzemi esetekben. Ez az [Azure HANA nagyméretű példányainak](./hana-overview-architecture.md)alapértelmezett üzembe helyezési konfigurációja. Az SAP-alkalmazási rétegben az SAP központi szolgáltatások és az SAP-párbeszédpanel példányai nem különböztetik meg az egyszerűséget. Ebben az egyszerű, 3 rétegű konfigurációban nem lenne magas rendelkezésre állású védelem az SAP központi szolgáltatásai számára.

> [!NOTE]
> Az éles környezetben futó SAP-rendszerek esetében további magas rendelkezésre állást és végleges vész-helyreállítási konfigurációkat ajánlunk a jelen dokumentum későbbi részében leírtak szerint.


## <a name="multiple-dbms-instances-per-vm-or-hana-large-instance-unit"></a>Több adatbázis-kezelő példány virtuális gépenként vagy a HANA nagyméretű példányának egysége
Ebben a konfigurációban az Azure-beli virtuális gépen vagy a HANA nagyméretű példány-egységen belül több adatbázis-kezelő példányt üzemeltet. A motiváció lehet kevesebb operációs rendszer fenntartása és ezzel csökkentett költségei. Más motivációk nagyobb rugalmasságot és nagyobb hatékonyságot biztosítanak egy nagyobb méretű virtuális gép vagy a HANA nagyméretű példány-egység erőforrásainak több adatbázis-kezelő példány közötti megosztásával. Eddig ezek a konfigurációk többnyire nem éles rendszerekre mutattak.

Egy hasonló konfiguráció, amely a következőképpen néz ki:

![Több adatbázis-kezelő példány egy egységben](./media/sap-planning-supported-configurations/multiple-database-instances.png)

Az ilyen típusú adatbázis-kezelő üzembe helyezését a következő támogatja:

- SQL Server on Windows
- IBM DB2. További részletek a cikkben [több példányban (Linux, UNIX)](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.dbobj.doc/doc/c0004904.html)
- Oracle esetén. Részletekért lásd: [SAP-támogatási megjegyzés #1778431](https://launchpad.support.sap.com/#/notes/1778431) és kapcsolódó SAP-megjegyzések
- Ha SAP HANA, egy virtuális gépen több példány van, az SAP meghívja ezt a telepítési módszert MCOS, amely támogatott. Részletekért tekintse meg az SAP-cikket [több SAP HANA rendszer egy gazdagépen (MCOS)] (https://help.sap.com/viewer/eb3777d5495d46c5b2fa773206bbfb46/2.0.02/
- /b2751fd43bec41a9a14e01913f1edf18.html)

Ha több adatbázis-példányt futtat egy gazdagépen, meg kell győződnie arról, hogy a különböző példányok nem versenyeznek az erőforrásokkal, és így túllépik a virtuális gép fizikai erőforrás-korlátait. Ez különösen akkor igaz, ha a memóriát a virtuális gépet megosztó példányok számára le kell foglalni. Ez a CPU-erőforrások esetében is igaz lehet, ha a különböző adatbázis-példányok képesek kihasználni. Az összes említett adatbázis-kezelő olyan konfigurációval rendelkezik, amelyek lehetővé teszik a memória-kiosztást és a processzor-erőforrásokat egy példány szintjén.
Az Azure-beli virtuális gépek ilyen konfigurációjának támogatásához a rendszer azt várja, hogy a különböző példányok által kezelt adatbázisok adatkezelési és naplófájl-naplófájljaihoz használt lemezek vagy kötetek elkülönítve legyenek. Más szóval a különböző adatbázis-kezelő példányok által felügyelt adatbázisok vagy napló-vagy visszaadott naplófájlok nem ugyanazt a lemezt vagy kötetet használják. 

A HANA nagyméretű példányok lemezes konfigurációja konfigurálva van, és részletesen szerepel a [Hana nagyméretű példányainak támogatott forgatókönyvekben](./hana-supported-scenario.md#single-node-mcos). 

> [!NOTE]
> Az éles környezetben futó SAP-rendszerek esetében további magas rendelkezésre állást és végleges vész-helyreállítási konfigurációkat ajánlunk a jelen dokumentum későbbi részében leírtak szerint. A több adatbázis-kezelő példányokkal rendelkező virtuális gépek nem támogatottak a jelen dokumentum későbbi részében ismertetett magas rendelkezésre állási konfigurációkkal.


## <a name="multiple-sap-dialog-instances-in-one-vm"></a>Több SAP-párbeszédpanel példánya egy virtuális gépen
Sok esetben több párbeszédpanel is üzembe lett helyezve operációs rendszer nélküli kiszolgálókon, vagy akár privát felhőkben futó virtuális gépeken is. Az ilyen konfigurációk esetében az egyes SAP-párbeszédpanelek bizonyos számítási feladatokhoz, üzleti funkciókhoz vagy számítási feladatokhoz való hozzáigazításának oka volt. A példányok elkülönített virtuális gépekre való elkülönítésének oka az operációs rendszer karbantartásának és műveleteinek az erőfeszítése volt. Vagy számos esetben a virtuális gép üzemeltetője vagy kezelője által a virtuális gép által üzemeltetett és adminisztrátora havi díjat kér. Az Azure-ban a Windows, a Red Hat, a SUSE és a Oracle Linux operációs rendszerein belül több SAP-párbeszédpanel példányának üzemeltetése az Egyesült Államokban, amelyek éles és nem éles környezetben is támogatottak. A Windows és modern Linux kerneleken elérhető SAP kernel PHYS_MEMSIZE paramétert úgy kell beállítani, ha több SAP Application Server-példány fut egyetlen virtuális gépen. Azt is javasoljuk, hogy korlátozza az SAP kiterjesztett memória kiterjesztését az operációs rendszereken, például a Windowsban, ahol az SAP kiterjesztett memória automatikus növekedése implementálva van. Ezt az SAP-profil paraméterrel végezheti el `em/max_size_MB` .

Ha az Azure-beli virtuális gépeken több SAP-párbeszédpanelt futtató példány fut, akkor a következő 3 szintű konfigurációban lehet kinézni:

![Az Azure-beli virtuális gépeken futó, 3 rétegből álló konfigurációt megjelenítő diagram.](./media/sap-planning-supported-configurations/multiple-dialog-instances.png)

Az SAP-alkalmazási rétegben az SAP központi szolgáltatások és az SAP-párbeszédpanel példányai nem különböztetik meg az egyszerűséget. Ebben az egyszerű, 3 rétegű konfigurációban nem lenne magas rendelkezésre állású védelem az SAP központi szolgáltatásai számára. Éles rendszerek esetében nem ajánlott a nem védett SAP központi szolgáltatásokat hagyni. Az SAP központi példányai és az ilyen többszörös SID-konfigurációk magas rendelkezésre állása esetén az úgynevezett több SID-konfigurációra vonatkozó részletekért lásd a dokumentum későbbi szakaszait.

## <a name="high-availability-protection-for-the-sap-dbms-layer"></a>Magas rendelkezésre állású védelem az SAP adatbázis-kezelő rétegében
Az SAP éles rendszerek üzembe helyezése során meg kell fontolnia a magas rendelkezésre állású konfigurációk gyors készenléti típusát. Különösen a SAP HANA, ahol a teljes teljesítmény és a méretezhetőség érdekében a memóriába be kell tölteni az adatmennyiséget, a magas rendelkezésre álláshoz nem ideális megoldás az Azure-szolgáltatások gyógyulása. 

A Microsoft általánosságban csak a magas rendelkezésre állású konfigurációk és szoftvercsomagok használatát támogatja, amelyeket a docs.microsoft.com SAP munkaterhelés szakasza ismertet. Ugyanezt az utasítást az SAP Megjegyzés [#1928533](https://launchpad.support.sap.com/#/notes/1928533)is olvashatja. A Microsoft nem nyújt támogatást a harmadik féltől származó olyan szoftveres keretrendszerek támogatásához, amelyeket a Microsoft nem dokumentál az SAP-munkaterhelésekkel együtt. Ilyen esetekben a magas rendelkezésre állási keretrendszer harmadik féltől származó szállítója a magas rendelkezésre állási konfigurációt támogató fél, amelynek az ügyfél által a támogatási folyamathoz kell kapcsolódnia. A kivételeket ebben a cikkben fogjuk megemlíteni. 

A Microsoft általánosságban a magas rendelkezésre állású konfigurációk korlátozott készletét támogatja az Azure-beli virtuális gépeken vagy a HANA Large instances-egységeken. A nagyméretű HANA-példányok támogatott forgatókönyvei esetében olvassa el a [Hana nagyméretű példányok által támogatott forgatókönyvek](./hana-supported-scenario.md)című dokumentumot.

Azure-beli virtuális gépek esetén a következő magas rendelkezésre állású konfigurációk támogatottak az adatbázis-kezelői szinten:

- SAP HANA a rendszer-replikációt a SUSE és a Red Hat Linux-pacemakere alapján. Tekintse meg a részletes cikkeket:
    - [SAP HANA magas rendelkezésre állása Azure-beli virtuális gépeken SUSE Linux Enterprise Server](./sap-hana-high-availability.md)
    - [SAP HANA magas rendelkezésre állása Azure-beli virtuális gépeken Red Hat Enterprise Linux](./sap-hana-high-availability-rhel.md)
- SAP HANA kibővíthető n + m konfigurációk a SUSE és a Red Hat [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) használatával. A részleteket a következő cikkekben találja:
    - [SAP HANA kibővíthető rendszer üzembe helyezése készenléti csomóponttal Azure-beli virtuális gépeken Azure NetApp Files használatával SUSE Linux Enterprise Server}](./sap-hana-scale-out-standby-netapp-files-suse.md)
    - [SAP HANA kibővíthető rendszer üzembe helyezése készenléti csomóponttal Azure-beli virtuális gépeken Azure NetApp Files használatával Red Hat Enterprise Linux](./sap-hana-scale-out-standby-netapp-files-rhel.md)
- SQL Server feladatátvevő fürt a Windows Scale-Out file Services alapján. Bár az éles rendszerekre vonatkozó javaslat a fürtözés helyett a SQL Server always on használata. A SQL Server always on jobb rendelkezésre állást biztosít a különálló tárolók használatával. A részleteket a cikk ismerteti: 
    - [SQL Server feladatátvevő fürt példányának konfigurálása Azure-beli virtuális gépeken](../../../azure-sql/virtual-machines/windows/failover-cluster-instance-storage-spaces-direct-manually-configure.md)
- A SQL Server always on az Azure-beli SQL Server Windows operációs rendszerét támogatja. Ez az Azure-beli éles SQL Server-példányok alapértelmezett ajánlása. A részleteket az alábbi cikkekben ismertetjük:
    - [SQL Server always on rendelkezésre állási csoportok bemutatása az Azure Virtual Machines](../../../azure-sql/virtual-machines/windows/availability-group-overview.md)szolgáltatásban.
    - [Always On rendelkezésre állási csoport konfigurálása az Azure-beli virtuális gépeken különböző régiókban](../../../azure-sql/virtual-machines/windows/availability-group-manually-configure-multiple-regions.md).
    - [Terheléselosztó konfigurálása az Azure always on rendelkezésre állási csoportjához](../../../azure-sql/virtual-machines/windows/availability-group-load-balancer-portal-configure.md).
- Oracle-adatvédelem Windows és Oracle Linux. A Oracle Linux részletei a cikkben találhatók:
    - [Oracle-adatvédelem megvalósítása Azure Linux rendszerű virtuális gépen](../oracle/configure-oracle-dataguard.md)
- Az IBM DB2 HADR a SUSE-es és a RHEL-hez készült részletes dokumentációja a SUSE és a RHEL szolgáltatáshoz a pacemaker használatával:
    - [Az IBM DB2-LUW magas rendelkezésre állása Azure-beli virtuális gépeken SUSE Linux Enterprise Server a pacemakerrel](./dbms-guide-ha-ibm.md)
    - [Az IBM Db2 LUW magas rendelkezésre állása Azure-beli virtuális gépeken Red Hat Enterprise Linux Serveren](./high-availability-guide-rhel-ibm-db2-luw.md)
- SAP-bemenő és SAP-maxDB konfigurálása a következő dokumentumokban részletezett módon:
    - [SAP ASE Azure-beli virtuális gépek DBMS üzembe helyezése SAP számítási feladatokhoz](./dbms_guide_sapase.md)
    - [Az SAP MaxDB, a liveCache és a Content Server üzembe helyezése Azure-beli virtuális gépeken](./dbms_guide_maxdb.md)
- A HANA nagyméretű példányai magas rendelkezésre állási forgatókönyvei részletesen a következőkben találhatók:
    - [A HANA Large instances-HSR által támogatott forgatókönyvek – magas rendelkezésre állású STONITH](./hana-supported-scenario.md#hsr-with-stonith-for-high-availability)
    - [A HANA nagyméretű példányainak támogatott forgatókönyvei – gazdagép automatikus feladatátvétele (1 + 1)](./hana-supported-scenario.md#host-auto-failover-11)

> [!IMPORTANT]
> A fent ismertetett forgatókönyvek egyike sem támogatja több adatbázis-kezelő példány konfigurációját egy virtuális gépen. Azt jelenti, hogy az egyes esetekben csak egy adatbázis-példány telepíthető virtuális gépenként és védett a magas rendelkezésre állási módszerekkel. Ezen a ponton **nem** támogatott több adatbázis-kezelő példány védelme ugyanazon a Windows-vagy pacemaker-feladatátvevő fürtön. Emellett az Oracle-adatvédelmet csak a virtuálisgép-telepítési esetekben támogatja a rendszer. 

A különböző adatbázis-rendszerek lehetővé teszik több adatbázis üzemeltetését egy adatbázis-kezelő példányban. Ahogy SAP HANA esetében, több adatbázis is üzemeltethető több adatbázis-tárolóban (MDC). Azokban az esetekben, amikor ezek a többadatbázisos konfigurációk egy feladatátvevő fürt erőforrásán belül működnek, ezek a konfigurációk támogatottak. A nem támogatott konfigurációk olyan esetek, amikor több fürterőforrás szükséges. Olyan konfigurációk esetében, amelyekben több SQL Server rendelkezésre állási csoportot határoz meg egy SQL Server példány alatt.


![Adatbázis-kezelő HA konfiguráció](./media/sap-planning-supported-configurations/database-high-availability-configuration.png)

A/vagy operációs rendszerek, például az Azure Load Balancer vagy a megoldás architektúrájának részeként szükséges összetevőktől függ. 

Kifejezetten a maxDB esetében a tárolási konfigurációnak eltérőnek kell lennie. A maxDB az adatfájlok és a naplófájlok a magas rendelkezésre állású konfigurációkhoz szükségesek a megosztott tárolóban. A maxDB esetében csak a megosztott tárterület támogatott a magas rendelkezésre állás érdekében. Minden más adatbázis-kezelői különálló tárolási verem esetében az egyetlen támogatott lemez-konfiguráció.

Léteznek más magas rendelkezésre állási keretrendszerek is, és ismertek, hogy Microsoft Azureon is futnak. A Microsoft azonban nem tesztelte ezeket a keretrendszereket. Ha a magas rendelkezésre állású konfigurációt ezekkel a keretrendszerekkel szeretné felépíteni, akkor a szoftver szolgáltatójának kell működnie a következőkhöz:

- Üzembe helyezési architektúra fejlesztése
- Az architektúra üzembe helyezése
- Az architektúra támogatása

> [!IMPORTANT]
> Microsoft Azure Marketplace számos olyan lágy készüléket kínál, amelyek tárolási megoldásokat biztosítanak az Azure natív tárolóján. Ezek a Soft készülékek az NFS-megosztások létrehozásához használhatók, és elméletileg használhatók a SAP HANA kibővíthető központi telepítések esetén, ahol készenléti csomópontra van szükség. Számos ok miatt a Microsoft és az SAP az Azure-ban való telepítésének egyik központi telepítése sem támogatja a Storage Soft-berendezéseket. Az adatbázis-kezelők SMB-megosztásokon való központi telepítése jelenleg nem támogatott. Az adatbázis-kezelő rendszer NFS-megosztásokon üzemelő telepítései a [Azure NETAPP Files](https://azure.microsoft.com/services/netapp/)NFS 4,1-megosztásokra korlátozódnak.


## <a name="high-availability-for-sap-central-service"></a>Magas rendelkezésre állás az SAP Central szolgáltatáshoz
Az SAP Central Services az SAP-konfiguráció második meghibásodási pontja. Ennek eredményeképpen ezeket a központi szolgáltatási folyamatokat is el kell végeznie. Az SAP-munkaterhelések által támogatott és dokumentált ajánlat a következőhöz hasonló:

- Windows feladatátvevő fürt kiszolgálója a Windows kibővített Fájlszolgáltatások használatával a sapmnt és a globális átviteli címtárhoz. A részleteket a cikk ismerteti:
    - [SAP ASCS-/SCS-példány fürthöz való fürtözése Windows feladatátvevő fürtön egy Azure-beli fájlmegosztás használatával](./sap-high-availability-guide-wsfc-file-share.md)
    - [Azure-infrastruktúra előkészítése az SAP magas rendelkezésre állásához Windows feladatátvevő fürt és fájlmegosztás használatával SAP ASCS/SCS-példányok esetén](./sap-high-availability-infrastructure-wsfc-file-share.md)
- A Windows feladatátvételi fürtszolgáltatás SMB-megosztást használ a sapmnt és a globális átviteli címtár [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) alapján. A részleteket a cikk tartalmazza:
    - [Magas rendelkezésre állás a Windows rendszerű Azure-beli virtuális gépeken futó SAP NetWeaver számára az SAP-alkalmazások Azure NetApp Files (SMB) szolgáltatásával](./high-availability-guide-windows-netapp-files-smb.md)
- Windows feladatátvevő fürt kiszolgálója a SIOS alapján `Datakeeper` . Bár a Microsoft dokumentálja, a SIOS-vel való támogatási kapcsolatra van szükség, így a megoldás használatakor SIOS-támogatással is elvégezhető. A részleteket a cikk ismerteti:
    - [Az SAP ASCS/SCS-példányok fürtözése Windows feladatátvevő fürtön az Azure-ban megosztott fürtözött lemez használatával](./sap-high-availability-guide-wsfc-shared-disk.md)
    - [Az Azure-infrastruktúra előkészítése az SAP-hez a Windows feladatátvevő fürt és az SAP ASCS/SCS közös lemezének használatával](./sap-high-availability-infrastructure-wsfc-shared-disk.md)
- Pacemaker a SUSE operációs rendszerhez, amely egy nagyszámú NFS-megosztást hoz létre két SUSE virtuális géppel és `drdb` a fájlreplikációs szolgáltatással. A részleteket a cikk ismerteti.
    - [Magas rendelkezésre állás az Azure-beli virtuális gépeken futó SAP NetWeaver számára SUSE Linux Enterprise Server SAP-alkalmazásokhoz](./high-availability-guide-suse.md)
    - [Magas rendelkezésre állás az NFS-en SUSE Linux Enterprise Server Azure-beli virtuális gépeken](./high-availability-guide-suse-nfs.md)
- Pacemaker SUSE operációs rendszer az [Azure NetApp Files](https://azure.microsoft.com/services/netapp/)által biztosított NFS-megosztások kihasználásával. A részletek dokumentációja:
    - [Magas rendelkezésre állás az Azure-beli virtuális gépeken futó SAP NetWeaver-hez SUSE Linux Enterprise Serveron Azure NetApp Files SAP-alkalmazásokhoz](./high-availability-guide-suse-netapp-files.md)
- A Red Hat operációs rendszer pacemakere egy fürtön üzemeltetett NFS-megosztással `glusterfs` . A részletek a cikkekben találhatók
    - [Az Azure Virtual Machines magas rendelkezésre állása az SAP NetWeaver-on Red Hat Enterprise Linux](./high-availability-guide-rhel.md)
    - [`GlusterFS` Azure-beli virtuális gépeken Red Hat Enterprise Linux for SAP NetWeaver](./high-availability-guide-rhel-glusterfs.md)
- A Red Hat operációs rendszer pacemakere [Azure NetApp Fileson](https://azure.microsoft.com/services/netapp/)ÜZEMELTETett NFS-megosztással. A részleteket a cikk ismerteti.
    - [Az Azure Virtual Machines magas rendelkezésre állása az SAP NetWeaver számára a Red Hat Enterprise Linux SAP-alkalmazásokhoz Azure NetApp Files](./high-availability-guide-rhel-netapp-files.md)

A felsorolt megoldások esetében támogatási kapcsolatra van szükség a SIOS a termék támogatásához `Datakeeper` és a SIOS közvetlen bevonásához problémák esetén. A Windows, a Red Hat és/vagy a SUSE operációs rendszer licencének függvényében az operációs rendszer szolgáltatójának támogatási szerződéssel is rendelkeznie kell, hogy teljes mértékben támogassa a felsorolt magas rendelkezésre állású konfigurációkat.

A konfiguráció a következőhöz hasonló módon is megjeleníthető:

![Adatbázis-kezelő és ASCS HA konfiguráció](./media/sap-planning-supported-configurations/high-available-3-tier-configuration.png)

A grafika jobb oldalán megjelenik a magasan elérhető SAP központi szolgáltatások. Amellett, hogy az SAP központi szolgáltatásai olyan feladatátvevő fürt keretrendszerrel védettek, amely probléma esetén feladatátvételt hajt végre, szükség van egy, a sapmnt és a globális átviteli könyvtár egy adott virtuális gépen való létezéstől független, biztonságos NFS-vagy SMB-megosztásra vagy egy megosztott Windows-lemezre. Az egyes megoldások, például a Windows feladatátvevő fürt és a pacemaker esetében az Azure Load Balancer szükséges ahhoz, hogy közvetlenül vagy átirányítsa a forgalmat egy kifogástalan állapotú csomópontra.

A listában nem szerepel a Oracle Linux operációs rendszer. A Oracle Linux nem támogatja a pacemakert fürt-keretrendszerként. Ha Oracle Linuxon szeretné telepíteni az SAP-rendszerét, és magas rendelkezésre állási keretrendszerre van szüksége a Oracle Linuxhoz, akkor a külső gyártótól származó szállítókkal kell dolgoznia. Az egyik szállító az Azure-beli SAP által támogatott SIOS rendelkezik. További információ: SAP Note [#1662610 – támogatás a SIOS Protection Suite for Linux](https://launchpad.support.sap.com/#/notes/1662610) további részleteiről.



### <a name="supported-storage-with-the-sap-central-services-scenarios-listed-above"></a>Támogatott tárterület a fent felsorolt SAP Central Services-forgatókönyvekkel
Mivel az Azure-beli tárolási típusok csak egy részhalmaza magas rendelkezésre állású NFS-vagy SMB-megosztásokat biztosít az SAP központi szolgáltatási fürtjében való használathoz, a támogatott tárolási típusok listája

- A Windows rendszerű feladatátvevő fürt a Windows kibővített fájlkiszolgálón telepíthető az összes natív Azure-beli tárolási típusra, kivéve a Azure NetApp Files. Azonban javasoljuk, hogy az átviteli sebesség és a IOPS terén a kiváló szolgáltatási szintű szerződések miatt Premium Storage kihasználja a szolgáltatást.
- A Windows feladatátvételi fürtszolgáltatás az SMB-vel Azure NetApp Files a Azure NetApp Fileson támogatott. Az Azure file Services szolgáltatásban az SMB-megosztások **nem** támogatottak ebben az időpontban.
- A Windows rendszerű feladatátvevő fürt a SIOS-alapú Windows megosztott lemezzel telepíthető az `Datakeeper` összes natív Azure-beli tárolási típusra, kivéve a Azure NetApp files. Azonban javasoljuk, hogy az átviteli sebesség és a IOPS terén a kiváló szolgáltatási szintű szerződések miatt Premium Storage kihasználja a szolgáltatást.
- A SUSE vagy a Red Hat pacemaker az NFS-megosztások használatával Azure NetApp Files a Azure NetApp Fileson támogatott. 
- A `drdb` két virtuális gép közötti konfigurációt használó SUSE pacemaker natív Azure Storage-típusok használatával támogatott, a Azure NetApp Files kivételével. Azonban javasoljuk, hogy az átviteli sebesség és a IOPS terén a kiváló szolgáltatási szintű szerződések miatt Premium Storage kihasználja a szolgáltatást.
- Az `glusterfs` NFS-megosztást biztosító Red Hat pacemaker a natív Azure Storage-típusok használatával támogatott, a Azure NetApp Files kivételével. Azonban javasoljuk, hogy az átviteli sebesség és a IOPS terén a kiváló szolgáltatási szintű szerződések miatt Premium Storage kihasználja a szolgáltatást.

> [!IMPORTANT]
> Microsoft Azure Marketplace számos olyan lágy készüléket kínál, amelyek tárolási megoldásokat biztosítanak az Azure natív tárolóján. Ezek a Soft készülékek az NFS-vagy SMB-megosztások létrehozásához használhatók, amelyek elméletileg használhatók a feladatátvevő fürtözött SAP központi szolgáltatásokban is. Ezeket a megoldásokat a Microsoft nem támogatja közvetlenül az SAP-munkaterhelések esetében. Ha úgy dönt, hogy egy ilyen megoldást használ az NFS-vagy SMB-megosztás létrehozásához, akkor az SAP központi szolgáltatás konfigurációjának támogatását a tárolóban található szoftvert birtokló külső gyártónak kell megadnia.


## <a name="multi-sid-sap-central-services-failover-clusters"></a>Többszörös SID SAP Central Services feladatátvételi fürtök
A nagyméretű SAP-tájakon szükséges virtuális gépek számának csökkentése érdekében az SAP lehetővé teszi, hogy a feladatátvevő fürt konfigurációjában több különböző SAP-rendszerből származó SAP központi szolgáltatási példányokat futtasson. Képzelje el azokat az eseteket, ahol 30 vagy több NetWeaver vagy S/4HANA éles rendszer van. Több SID-fürtszolgáltatás nélkül ezek a konfigurációk legalább 60-es virtuális gépet igényelnek 30 vagy több Windows vagy pacemaker feladatátvételi fürt konfigurációjában. Az adatbázis-kezelő feladatátvételi fürtökön kívül szükséges. Ha több SAP központi szolgáltatást helyez üzembe a feladatátvevő fürt két csomópontján, a virtuális gépek száma jelentősen csökkenthető. Azonban a több SAP központi szolgáltatási példány üzembe helyezése egyetlen két csomópontos fürtön is hátrányos. A fürt konfigurációjának egyetlen virtuális gépe által felmerülő problémák több SAP-rendszeren is érvényesek. A fürt konfigurációjában futó vendég operációs rendszer karbantartásának több koordinációra van szüksége, mivel több éles SAP-rendszer is érintett. Az olyan eszközök, mint az SAP láma, nem támogatják a több SID-fürtszolgáltatást a rendszer klónozási folyamatában.

Az Azure-ban a ENSA1 és a ENSA2 Windows operációs rendszer esetén a több SID-alapú fürtkonfiguráció is támogatott. A javaslat nem ötvözi a régebbi sorba helyezni Replication Service Architecture (ENSA1) architektúrát az új architektúrával (ENSA2) egy több SID-fürtön. Az ilyen architektúrával kapcsolatos részletek a cikkekben vannak dokumentálva

- [SAP ASCS/SCS instance multi-SID magas rendelkezésre állás a Windows Server feladatátvételi fürtszolgáltatással és a megosztott lemezzel az Azure-ban](./sap-ascs-ha-multi-sid-wsfc-shared-disk.md) 
- [SAP ASCS/SCS instance multi-SID magas rendelkezésre állás a Windows Server feladatátvételi fürtszolgáltatással és fájlmegosztás az Azure-ban](./sap-ascs-ha-multi-sid-wsfc-file-share.md) 

A SUSE esetében a Pacemakeren alapuló több SID-fürt is támogatott. Eddig a következő konfiguráció támogatott:

- Legfeljebb öt SAP ASCS/SCS-példány
- A régi sorba helyezni-replikációs kiszolgáló Ice Architecture (ENSA1)
- Két csomópontos pacemaker-fürt konfigurációja

A konfigurációt az [Azure-beli virtuális gépeken futó SAP NetWeaver magas rendelkezésre állása ismerteti SUSE Linux Enterprise Server for SAP Applications multi-SID Guide](./high-availability-guide-suse-multi-sid.md)

A sorba helyezni replikációs kiszolgálóval rendelkező több SID-fürt sematikusan úgy néz ki, mint

![A sorba helyezni replikációs kiszolgálóval rendelkező több SID-fürtöt bemutató diagram.](./media/sap-planning-supported-configurations/high-available-multi-system-configuration.png)


## <a name="sap-hana-scale-out-scenarios"></a>Felskálázási forgatókönyvek SAP HANA
A SAP HANA kibővíthető forgatókönyvek a HANA Certified Azure-beli virtuális gépek egy részhalmaza számára támogatottak, ahogy az a [SAP HANA Hardware könyvtárban](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)szerepel. A "fürtözés" oszlopban az "igen" jelölésű összes virtuális gép használható OLAP-vagy S/4HANA-felskálázásra is. A STANDBY nélküli konfigurációk a következők: 

- Azure Premium Storage, beleértve az Azure Write Accelerator-t a/Hana/log-kötethez
- [Ultralemez](../../disks-enable-ultra-ssd.md)
- [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) 

A készenléti csomópont (ok) val rendelkező OLAP-vagy S/4HANA-alapú kibővített konfigurációk kizárólag az Azure NetApp Files-on üzemeltetett NFS-en támogatottak. SAP HANA

A készenléti csomóponttal vagy anélkül történő pontos tárolási konfigurációkról a következő cikkekben talál további információt:

- [SAP HANA Azure-beli virtuális gépek tárkonfigurációi](./hana-vm-operations-storage.md) 
- [SAP HANA kibővíthető rendszer üzembe helyezése készenléti csomóponttal Azure-beli virtuális gépeken Azure NetApp Files használatával SUSE Linux Enterprise Server](./sap-hana-scale-out-standby-netapp-files-suse.md)
- [SAP HANA kibővíthető rendszer üzembe helyezése készenléti csomóponttal Azure-beli virtuális gépeken Azure NetApp Files használatával Red Hat Enterprise Linux](./sap-hana-scale-out-standby-netapp-files-rhel.md)
- [SAP-támogatási Megjegyzés #2080991](https://launchpad.support.sap.com/#/notes/2080991)

A Hana nagyméretű példányai által támogatott HANA-kibővített konfigurációk részleteiről az alábbi dokumentáció vonatkozik:

- [A HANA nagyméretű példányainak támogatott forgatókönyvei készenléti kibővítés esetén](./hana-supported-scenario.md#scale-out-with-standby)
- [A HANA nagyméretű példányainak támogatott forgatókönyvei készenlét nélkül kibővítve](./hana-supported-scenario.md#scale-out-without-standby)


## <a name="disaster-recovery-scenario"></a>Vész-helyreállítási forgatókönyv
Számos különböző vész-helyreállítási forgatókönyv támogatott. A vészhelyzeti architektúrákat olyan architektúrák definiáljuk, amelyek kompenzálják a teljes Azure-régiókat a rácson. Ez azt jelenti, hogy a vész-helyreállítási célpontnak egy másik Azure-régiónak kell lennie, mint célként az SAP-környezet futtatásához. Az adatbázis-kezelő rétegben és a nem adatbázis-kezelő rétegben külön metódusok és konfigurációk vannak elkülönítve. 

### <a name="dbms-layer"></a>Adatbázis-kezelő réteg
Az adatbázis-kezelő réteg esetében az adatbázis-kezelő natív replikációs mechanizmusokat használó konfigurációk, például az Always on, az Oracle-adatvédelem, a DB2-HADR, az SAP-alapú always-on vagy a HANA rendszerreplikáció támogatottak. Kötelező, hogy az ilyen esetekben a replikálási adatfolyam aszinkron módon legyen szinkronban, mint az egyetlen Azure-régióban üzembe helyezett jellemző magas rendelkezésre állási helyzetekben. Egy ilyen támogatott adatbázis-kezelői vész-helyreállítási konfiguráció egy tipikus példája az [Azure-régiók elérhetőségének SAP HANA](./sap-hana-availability-across-regions.md#combine-availability-within-one-region-and-across-regions)cikkében olvasható. A szakasz második ábrája a HANA példáját mutatja be. Az SAP-alkalmazások számára támogatott fő adatbázisok mindegyike képes az ilyen forgatókönyvekben való üzembe helyezésre.

A vész-helyreállítási régióban egy kisebb méretű virtuális gép is használható, mivel a virtuális gép nem használja a teljes munkaterhelés-forgalmat. Ehhez a következő szempontokat kell figyelembe vennie:

- A kisebb virtuálisgép-típusok nem teszik lehetővé, hogy a kisebb virtuális gépeknél több lemez legyen csatlakoztatva
- A kisebb virtuális gépek kevesebb hálózati és tárolási teljesítményt biztosítanak
- A virtuálisgép-családokra való átméretezés problémát jelenthet, ha a különböző virtuális gépeket egy Azure-beli rendelkezésre állási csoportba gyűjti, vagy ha az M sorozatú család és a virtuális gépek Mv2-családja között újraméretezés történik.
- Az adatbázis-példány processzor-és memóriahasználat, amely minimális késleltetéssel és elegendő CPU-és memória-erőforrással képes fogadni a módosításokat, hogy a módosítások csak minimális késleltetéssel legyenek alkalmazva.  

A különböző virtuálisgép-méretek korlátozásával kapcsolatos további részletek [itt](../../sizes.md) találhatók 

Egy másik támogatott módszer a DR cél üzembe helyezéséhez, hogy egy második adatbázis-kezelő példányt telepítsen egy olyan virtuális gépre, amely egy nem éles környezetben működő SAP-példány nem üzemi adatbázis-kezelő példányát futtatja. Ez egy kicsit nagyobb kihívást jelenthet, mivel meg kell állapítania, hogy mi a memória, a processzor-erőforrások, a hálózati sávszélesség és a tárolási sávszélesség a DR forgatókönyvben főpéldányként működni kívánó konkrét cél példányok esetén. Különösen a HANA-ban kifejezetten ajánlott olyan példányt konfigurálni, amely egy megosztott gazdagépen a DR célként funkcionál, hogy az adatok ne legyenek előre betöltve a DR cél példányba.

A HANA nagyméretű példányok DR-forgatókönyvei esetében tekintse meg a következő dokumentumokat:

- [Önálló csomópont a Storage-replikációt használó DR használatával](./hana-supported-scenario.md#single-node-with-dr-using-storage-replication)
- [Egyetlen csomópont DR (többcélú) tároló-replikáció használatával](./hana-supported-scenario.md#single-node-with-dr-multipurpose-using-storage-replication)
- [Egyetlen csomópont DR (többcélú) tároló-replikáció használatával](./hana-supported-scenario.md#single-node-with-dr-multipurpose-using-storage-replication)
- [Magas rendelkezésre állás a HSR és a DR tároló-replikálással](./hana-supported-scenario.md#high-availability-with-hsr-and-dr-with-storage-replication)
- [Méretezés a DR használatával a Storage replikálásával](./hana-supported-scenario.md#scale-out-with-dr-using-storage-replication)
- [Egyetlen csomópont a DR használatával HSR](./hana-supported-scenario.md#single-node-with-dr-using-hsr)
- [Egyetlen csomópontos HSR a DR (Cost optimalizált)](./hana-supported-scenario.md#single-node-hsr-to-dr-cost-optimized)
- [Magas rendelkezésre állás és vész-helyreállítás a HSR](./hana-supported-scenario.md#high-availability-and-disaster-recovery-with-hsr)
- [Magas rendelkezésre állás és vész-helyreállítás a HSR (Cost optimalizált)](./hana-supported-scenario.md#high-availability-and-disaster-recovery-with-hsr-cost-optimized)
- [Vertikális felskálázás DR használatával HSR](./hana-supported-scenario.md#scale-out-with-dr-using-hsr)

> [!NOTE]
> A [Azure site Recovery](https://azure.microsoft.com/services/site-recovery/) használatát nem tesztelték az adatbázis-kezelő üzembe helyezéséhez az SAP munkaterhelés alatt. Ennek eredményeképpen az SAP Systems adatbázis-kezelő rétege jelenleg nem támogatott. A Microsoft és az SAP által nem felsorolt egyéb replikációs módszerek nem támogatottak. Ha külső gyártótól származó szoftvert használ a különböző Azure-régiók közötti SAP-rendszerek adatbázis-kezelő rétegének replikálására, akkor a szoftver gyártójának támogatnia kell a szoftvert, és a Microsoft és az SAP támogatási csatornái nem támogatják azokat. 
 
## <a name="non-dbms-layer"></a>Nem adatbázis-kezelő réteg
Az SAP-alkalmazás rétege és a végsőan szükséges megosztások vagy tárolóhelyek esetében az ügyfelek a következő két fő forgatókönyvet használják:

- A második Azure-régióban a vész-helyreállítási célokat nem használják termelési vagy nem termelési célokra. Ebben az esetben a vész-helyreállítási célként működő virtuális gépek ideális esetben nincsenek telepítve, és az éles környezetben futó SAP-alkalmazási réteg lemezképeit és módosításait a rendszer replikálja a vész-helyreállítási régióba. Egy ilyen feladatot végrehajtó funkció [Azure site Recovery](../../../site-recovery/azure-to-azure-move-overview.md). Azure Site Recovery támogatja az Azure – Azure replikációs forgatókönyveket, például a következőt:. 
- A vész-helyreállítási célok olyan virtuális gépek, amelyeket valójában nem éles üzemi rendszerek használnak. A teljes SAP-környezet két különböző Azure-régióban érhető el, az éles rendszerek általában egy régióban és nem éles környezetben, egy másik régióban. Sok ügyfél-telepítés esetén az ügyfél egy éles rendszernek megfelelő, nem éles környezettel rendelkezik. Az ügyfél az alkalmazási réteg nem éles környezetekben előre telepített éles alkalmazási példányokat tartalmaz. Feladatátvétel esetén a nem éles példányok le lesznek állítva, az éles virtuális gépek virtuális nevei a nem éles virtuális gépekre lettek áthelyezve (miután új IP-címeket rendelt a DNS-ben), az előre telepített éles példányok pedig első lépések

### <a name="sap-central-services-clusters"></a>SAP Central Services-fürtök
A megosztott lemezeket (Windows), az SMB-megosztásokat (Windows) vagy NFS-megosztásokat használó SAP Central Services-fürtök valamivel nehezebben replikálhatók. A Windows-oldalon a Windows Storage-replikáció lehetséges megoldás. A Linux rsync egy életképes megoldás.



## <a name="non-supported-scenario"></a>Nem támogatott forgatókönyv
A forgatókönyvek listája, amelyek nem támogatottak az Azure-architektúrán futó SAP-munkaterhelések esetében. A **nem támogatott** érték azt jelenti, hogy az SAP és a Microsoft nem fogja tudni támogatni ezeket a konfigurációkat, és késleltetni kell egy olyan, az érintett harmadik fél számára, amely az ilyen architektúrák létrehozásához adott szoftvert adott. A kategóriák közül kettő a következőkből áll:

- Storage Soft Appliances: az Azure Marketplace-en több tárolási Soft-eszköz is rendelkezésre áll. Néhány gyártó saját dokumentációval rendelkezik arról, hogyan használhatja ezeket a Storage Soft-berendezéseket az Azure-ban az SAP-szoftverekhez kapcsolódóan. Az ilyen típusú tárolást támogató konfigurációk vagy központi telepítések támogatását a tárolók által biztosított üdítőitalok szállítójának kell biztosítania. Ez a tény az [SAP-támogatási megjegyzésekben](https://launchpad.support.sap.com/#/notes/2015553) is megnyilvánult #2015553
- Magas rendelkezésre állási keretrendszerek: csak a pacemaker és a Windows Server feladatátvevő fürt támogatott magas rendelkezésre állási keretrendszerek az Azure-beli SAP-munkaterhelésekhez. Amint azt korábban említettük, a SIOS megoldását a `Datakeeper` Microsoft ismerteti és dokumentálja. A SIOS összetevőit azonban a `Datakeeper` SIOS-on keresztül kell támogatni, mint az ezeket az összetevőket szolgáltató szolgáltatót. Az SAP más Certified magas rendelkezésre állási keretrendszerek is szerepelnek a különböző SAP-megjegyzésekben. Néhányat a harmadik féltől származó gyártó is tanúsított az Azure-ban. Ennek ellenére az ezeket a termékeket használó konfigurációk támogatását a termék gyártójának kell megadnia. A különböző szállítók eltérő integrációt nyújtanak az SAP támogatási folyamataiba. Tisztázza, hogy milyen támogatási folyamat működik a legjobban az adott gyártónál, mielőtt a terméket az Azure-ban üzembe helyezett SAP-konfigurációkban kívánja használni.
- A megosztott lemezeken az adatbázisfájlok nem támogatottak a maxDB kivételével. Az összes többi adatbázis esetében a támogatott megoldás az, hogy az SMB-vagy NFS-megosztások vagy megosztott lemezek helyett külön tárolóhelyek legyenek a magas rendelkezésre állású forgatókönyvek konfigurálásához

Egyéb forgatókönyvek, amelyek nem támogatottak, például a következők:

- Üzembe helyezési forgatókönyvek, amelyek nagyobb hálózati késést mutatnak be az SAP alkalmazási szintje és az SAP adatbázis-kezelői szintje között az SAP általános architektúrájában, ahogyan azt a NetWeaver, S/4HANA és `Hybris` például. Ide tartoznak az alábbiak:
    - A helyi szintek egyikének üzembe helyezése, míg a másik réteg üzembe helyezése az Azure-ban
    - Egy rendszer SAP-alkalmazási szintjeinek üzembe helyezése egy másik Azure-régióban, mint az adatbázis-kezelői szint
    - Az Azure-ba és az Azure-ban található másik rétegbe tartozó adatközpontok egyikének üzembe helyezése, kivéve, ha az adott architektúrát egy natív Azure-szolgáltatás biztosította
    - Hálózati virtuális berendezések üzembe helyezése az SAP-alkalmazási réteg és az adatbázis-kezelő réteg között
    - Az adatközpontokban üzemeltetett tárolók kihasználása az Azure-adatközpontba az SAP adatbázis-kezelői rétege vagy az SAP globális átviteli könyvtára számára
    - A két réteg üzembe helyezése két különböző felhőalapú szolgáltatóval. Például üzembe helyezheti az adatbázis-kezelői szintet az Oracle Cloud Infrastructure-ben és az Azure-beli alkalmazási szinten
- Többpéldányos HANA pacemaker-fürt konfigurációi
- Windows-fürtök megosztott lemezekkel SOFS vagy SMB használatával a Windows rendszeren támogatott SAP-adatbázisok ANF. Ehelyett azt javasoljuk, hogy az adott adatbázisok natív magas rendelkezésre állású replikálásának használatát és a különböző tárolási veremeket használja.
- A Linux rendszeren támogatott SAP-adatbázisok üzembe helyezése az NFS-megosztásokon található ANF felül, a SAP HANA kivételével
- Az Oracle adatbázis-kezelő rendszer üzembe helyezése bármely más vendég operációs rendszeren, mint a Windows és Oracle Linux. Lásd még: [SAP-támogatás megjegyzés #2039619](https://launchpad.support.sap.com/#/notes/2039619)

Olyan forgatókönyv (ek), amelyek nem teszteltek, ezért nem rendelkezik a hasonló listával:

- Azure Site Recovery az adatbázis-kezelő rétegbeli virtuális gépek replikálását. Ennek eredményeképpen javasoljuk, hogy az adatbázis natív aszinkron replikálási funkcióját kihasználva potenciális vész-helyreállítási konfigurációt lehessen használni
 

## <a name="next-steps"></a>Következő lépések
Az [Azure Virtual Machines tervezésének és megvalósításának](./planning-guide.md) következő lépéseinek elolvasása az SAP NetWeaver-ban




  