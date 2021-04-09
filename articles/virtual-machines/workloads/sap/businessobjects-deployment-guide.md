---
title: SAP BusinessObjects BI platform üzembe helyezése az Azure-ban | Microsoft Docs
description: Az SAP BusinessObjects BI platform megtervezése, üzembe helyezése és konfigurálása az Azure-ban
services: virtual-machines-windows,virtual-network,storage,azure-netapp-files,azure-files
documentationcenter: saponazure
author: dennispadia
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/05/2020
ms.author: depadia
ms.openlocfilehash: 3b3c313df5704e49481c66ad682faccd48d180ea
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102505902"
---
# <a name="sap-businessobjects-bi-platform-planning-and-implementation-guide-on-azure"></a>SAP BusinessObjects BI platformtervezési és -megvalósítási útmutató az Azure-on

## <a name="overview"></a>Áttekintés

Ennek az útmutatónak a célja, hogy útmutatást nyújtson az SAP BusinessObjects BI platform, más néven az Azure-beli SAP BOBI platform tervezéséhez, üzembe helyezéséhez és konfigurálásához. Ez az útmutató az SAP BOBI platformhoz tartozó általános Azure-szolgáltatások és-funkciók fedezésére szolgál. Ez az útmutató nem tartalmazza az összes lehetséges konfigurációs beállítást. A tipikus üzembe helyezési forgatókönyvekkel kapcsolatos gyakori megoldásokat is magában foglalja.

Ez az útmutató nem helyettesíti a szabványos SAP BOBI platform telepítési és felügyeleti útmutatók, operációs rendszer vagy bármely adatbázis dokumentációját.

## <a name="plan-and-implement-sap-businessobjects-bi-platform-on-azure"></a>Az SAP BusinessObjects BI platform megtervezése és implementálása az Azure-ban

Microsoft Azure számos szolgáltatást kínál, többek között a számítási, tárolási és hálózatkezelési szolgáltatásokat, és sok más vállalat számára, hogy alkalmazásaikat hosszú beszerzési ciklusok nélkül hozza létre. Az Azure Virtual Machines (VM) segítségével a vállalatok igény szerint és méretezhető számítási erőforrásokat telepíthetnek különböző SAP-alkalmazásokhoz, például az SAP NetWeaver-alapú alkalmazásokhoz, az SAP Hybris, az SAP BusinessObjects BI platformhoz, üzleti igényeik alapján. Az Azure támogatja a létesítmények közötti kapcsolatot is, amely lehetővé teszi, hogy a vállalatok integrálják az Azure-beli virtuális gépeket a helyszíni tartományba, a saját felhőik és az SAP-rendszerük környezetében.

Ez a dokumentum útmutatást nyújt az Azure-beli SAP BusinessObjects BI platform tervezéséhez és megvalósításához. Kiegészíti az SAP-telepítési dokumentációt és az SAP-megjegyzéseket, amelyek az SAP BOBI telepítésének és üzembe helyezésének elsődleges erőforrásait jelölik.

### <a name="architecture-overview"></a>Az architektúra áttekintése

Az SAP BusinessObjects BI platform egy olyan önálló rendszer, amely egyetlen Azure-beli virtuális gépen létezhet, vagy számos Azure-Virtual Machines különböző összetevőket futtató fürtbe méretezhető. Az SAP BOBI platform hat fogalmi szinten áll: ügyféloldali, webes szinten, felügyeleti szinten, tárolási szinten, feldolgozási szinten és adatszinten. (Az egyes szintek részletes ismertetését lásd: [SAP BusinessObjects Business Intelligence platform](https://help.sap.com/viewer/product/SAP_BUSINESSOBJECTS_BUSINESS_INTELLIGENCE_PLATFORM/4.3/en-US) Súgó portál). Az egyes szinteken az alábbi magas szintű adatok szerepelnek:

- **Ügyfél szintje:** Minden olyan asztali ügyfélprogramot tartalmaz, amely a BI platformmal együttműködve különböző jelentéskészítési, elemzési és felügyeleti képességeket biztosít.
- **Webes rétegek:** A JAVA-webalkalmazás-kiszolgálókra telepített webalkalmazásokat tartalmaz. A webalkalmazások BI platform-funkciókat biztosítanak a végfelhasználók számára a webböngésző használatával.
- **Felügyeleti szintek:** Koordinálja és szabályozza az összes olyan összetevőt, amely a BI platformot teszi elérhetővé. Magában foglalja a központi felügyeleti kiszolgálót (CMS), az Event Servert és a kapcsolódó szolgáltatásokat
- **Tárolási rétegek:** A fájlok, például dokumentumok és jelentések kezelésére felelős. Emellett a jelentés-gyorsítótárazást is kezeli a rendszererőforrások mentésére a felhasználói hozzáférés jelentésekor.
- **Feldolgozási szintek:** Elemzi az adatokat, és jelentéseket és más kimeneti típusokat hoz létre. Ez az egyetlen olyan csomag, amely hozzáfér a jelentési adatforrásokat tartalmazó adatbázisokhoz.
- **Adatcsomag:** A CMS rendszeradatbázisokat és a naplózási adattárt üzemeltető adatbázis-kiszolgálókat tartalmazza.

Az SAP BI platform egy vagy több gazdagépen futó kiszolgálók gyűjteményéből áll. Fontos, hogy a megfelelő üzembe helyezési stratégiát a környezet méretének, az üzleti igényeknek és a típusnak megfelelően válassza. A kisebb telepítésekhez, például a fejlesztéshez és a teszteléshez használhat egyetlen Azure-beli virtuális gépet a webalkalmazás-kiszolgáló, az adatbázis-kiszolgáló és az összes BI platform-kiszolgáló számára. Ha az Azure-ban az adatbázis-szolgáltatás (DBaaS) szolgáltatást használja, az adatbázis-kiszolgáló külön fog futni a többi összetevőtől. Közepes és nagyméretű telepítés esetén több Azure-beli virtuális gépen futó kiszolgáló is lehet.

Az alábbi ábrán látható, hogy az SAP BOBI platform nagyméretű üzembe helyezésének architektúrája az Azure Virtual Machines szolgáltatásban megjelenő, ahol az egyes összetevők elosztása és elhelyezése olyan rendelkezésre állási csoportokban történik, amelyek a szolgáltatások meghibásodása esetén is fenntarthatók.

![SAP BusinessObjects BI platform architektúrája az Azure-ban](./media/businessobjects-deployment-guide/businessobjects-architecture-on-azure.png)

#### <a name="architecture-details"></a>Architektúra részletei

- Terheléselosztóval

  Az SAP BOBI többpéldányos telepítése esetén a webalkalmazás-kiszolgálók (vagy a webes réteg) két vagy több gazdagépen futnak. Ha a felhasználói terhelést egyenletesen szeretné terjeszteni a webkiszolgálókon, a végfelhasználók és a webkiszolgálók között terheléselosztó is használható. Az Azure-ban [Azure Load Balancer](../../../load-balancer/load-balancer-overview.md) vagy [Azure Application Gateway](../../../application-gateway/overview.md) használatával kezelheti a webkiszolgálók forgalmát.

- Webalkalmazás-kiszolgálók

  A webkiszolgáló az SAP BOBI platform webalkalmazásait (például a CMC és a BI Launch pad) üzemelteti. A webkiszolgáló magas rendelkezésre állásának eléréséhez legalább két webalkalmazás-kiszolgálót kell telepítenie a redundancia és a terheléselosztás kezeléséhez. Az Azure-ban ezek a webalkalmazás-kiszolgálók a rendelkezésre állási csoportokban vagy a rendelkezésre állási zónákban helyezhetők el a jobb rendelkezésre állás érdekében.

  A Tomcat az SAP BI platform alapértelmezett webalkalmazása. Ha magas rendelkezésre állást szeretne elérni a Tomcat számára, engedélyezze a munkamenet-replikációt az Azure-beli [statikus tagsági Interceptor](https://tomcat.apache.org/tomcat-9.0-doc/config/cluster-membership.html#Static_Membership_Attributes) használatával. Gondoskodik arról, hogy a felhasználó akkor is hozzáférhessen az SAP BI webalkalmazáshoz, ha a Tomcat szolgáltatás megszakad.

  > [!Important]
  > Alapértelmezés szerint a Tomcat csoportos küldési IP-címet és portot használ a fürtözéshez, amely nem támogatott az Azure-ban (SAP-Megjegyzés [2764907](https://launchpad.support.sap.com/#/notes/2764907)).

- BI platform-kiszolgálók

  A BI platform-kiszolgálók tartalmazzák az SAP BOBI alkalmazás részét képező összes szolgáltatást (felügyeleti szintek, feldolgozási szintek és tárolási szintek). Amikor egy webkiszolgáló megkapja a kérést, az észleli az egyes BI platform-kiszolgálókat (különösen a fürt összes CMS-kiszolgálóját), és automatikusan betölti a kéréseiket. Abban az esetben, ha a BI-platform egyik gazdagépe meghibásodik, a webkiszolgáló automatikusan küldi a kéréseket a másik gazdagépnek.

  Ha magas rendelkezésre állást vagy redundanciát szeretne elérni a BI platformon, legalább két Azure-beli virtuális gépen üzembe kell helyeznie az alkalmazást. A méretezés alapján a BI platformot több Azure-beli virtuális gépen is futtathatja.

- File adattár-kiszolgáló (FRS)

  A file repository-kiszolgáló tartalmazza az összes létrehozott jelentést és más BI-dokumentumot. A többpéldányos telepítésben a BI platform-kiszolgálók több virtuális gépen futnak, és minden virtuális gépnek hozzá kell férnie ezekhez a jelentésekhez és más BI-dokumentumokhoz. Ezért a fájlrendszert meg kell osztani az összes BI platform-kiszolgáló között.

  Az Azure-ban az [Azure Premium-fájlokat](../../../storage/files/storage-files-introduction.md) vagy a [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) is használhatja a file adattár-kiszolgáló számára. Mindkét Azure-szolgáltatás beépített redundanciával rendelkezik.

  > [!Important]
  > Az SMB protokoll Azure Files általánosan elérhető, de a Azure Files NFS protokoll támogatása jelenleg előzetes verzióban érhető el. További információ: az [NFS 4,1-támogatás a Azure Files számára már előzetes](https://azure.microsoft.com/en-us/blog/nfs-41-support-for-azure-files-is-now-in-preview/) verzióban érhető el

- CMS & naplózási adatbázis
  
  Az SAP BOBI platformhoz szükség van egy adatbázisra a rendszerértékek tárolásához, amelyet CMS-adatbázisnak nevezünk. A szolgáltatás a BI platformmal kapcsolatos információk, például a felhasználók, a kiszolgálók, a mappák, a dokumentumok, a konfiguráció és a hitelesítés részleteinek tárolására szolgál.

  Az Azure a [MySQL](https://azure.microsoft.com/en-us/services/mysql/) -adatbázis és az [Azure SQL Database](https://azure.microsoft.com/en-us/services/sql-database/) szolgáltatás (DBaaS) ajánlatát KÍNÁLJA, amely a CMS-adatbázishoz és a naplózási adatbázisokhoz használható. Mivel ez egy Pásti-ajánlat, az ügyfeleknek nem kell aggódniuk az adatbázisok működésével, rendelkezésre állásával és karbantartásával kapcsolatban. Az ügyfél az üzleti igényeknek megfelelően kiválaszthatja a CMS-hez és a naplózási tárházhoz tartozó saját adatbázisát is.

## <a name="support-matrix"></a>Támogatási mátrix

Ez a szakasz a különböző SAP BOBI-összetevők támogatását ismerteti, például az SAP BusinessObjects BI platform verzióját, az operációs rendszert és az Azure-beli adatbázisokat.

### <a name="sap-businessobjects-bi-platform"></a>SAP BusinessObjects BI platform

Az Azure-beli infrastruktúra-szolgáltatás (IaaS) lehetővé teszi az SAP BusinessObjects BI platform üzembe helyezését és konfigurálását az Azure-beli számítási feladatokban. Az SAP BOBI platform következő verzióját támogatja:

- SAP BusinessObjects BI platform 4,3
- SAP BusinessObjects BI platform 4,2 SP04 +
- SAP BusinessObjects BI platform 4,1 SP05 +

Az SAP BI platform különböző operációs rendszereken és adatbázisokon fut. Az SAP BOBI platform operációs rendszer és adatbázis-verzió közötti támogatása az SAP BOBI [termék rendelkezésre állási mátrixában](https://apps.support.sap.com/sap/support/pam) érhető el.

### <a name="operating-system"></a>Operációs rendszer

Az Azure az SAP BusinessObjects BI platform üzembe helyezéséhez a következő operációs rendszereket támogatja.

- Microsoft Windows Server
- SUSE Linux Enterprise Server (SLES)
- Red Hat Enterprise Linux (RHEL)
- Oracle Linux (OL)

Az [SAP BUSINESSOBJECTS bi platformhoz tartozó Product rendelkezésre állási mátrixban (PAM)](https://support.sap.com/pam) felsorolt operációs rendszer verziója támogatott, ha kompatibilisek az Azure-infrastruktúrán való futtatással.

### <a name="databases"></a>Adatbázisok

A BI platformnak a CMS-hez és a naplózási adattárhoz szükséges adatbázisra van szüksége, amely az SAP- [termékek rendelkezésre állási mátrixában](https://support.sap.com/pam) felsorolt összes támogatott adatbázisra telepíthető, amely a következőket tartalmazza:

- Microsoft SQL Server

- [Azure SQL Database](https://azure.microsoft.com/en-us/services/sql-database/) (csak az SAP Bobi platformhoz támogatott adatbázis Windows rendszeren)

  Ez egy teljes körűen felügyelt SQL Server adatbázismotor, amely a SQL Server legújabb stabil Enterprise kiadásán alapul. Az Azure SQL Database kezeli a legtöbb adatbázis-kezelési funkciót, például a frissítését, a javítást és a figyelést felhasználói beavatkozás nélkül. Az Azure SQL Database használatával magas rendelkezésre állású és nagy teljesítményű adattároló réteget hozhat létre az Azure-ban az alkalmazások és a megoldások számára. További részletekért olvassa el [Azure SQL Database](../../../azure-sql/azure-sql-iaas-vs-paas-what-is-overview.md) dokumentációját.

- [Azure Database for MySQL](https://azure.microsoft.com/en-us/services/mysql/) (a MySQL AB-ben említett kompatibilitási irányelvek követése az SAP PAM-ban)

  Ez egy, a MySQL Community Edition által működtetett, kapcsolódó adatbázis-szolgáltatás. Egy teljes körűen felügyelt adatbázis-szolgáltatásként (DBaaS), amely kritikus fontosságú számítási feladatokat képes kezelni kiszámítható teljesítménnyel és dinamikus skálázhatósággal. Beépített magas rendelkezésre állással, automatikus biztonsági mentéssel, szoftveres javítással, automatikus hibák észlelésével és az időponthoz való visszaállítással akár 35 napig, ami jelentősen csökkenti a műveleti feladatokat. További részletekért olvassa el [Azure Database for MySQL](../../../mysql/overview.md) dokumentációját.

- SAP HANA

- SAP ASE

- IBM DB2

- Oracle (verzióhoz és korlátozáshoz, ellenőrizze az SAP Megjegyzés [2039619](https://launchpad.support.sap.com/#/notes/2039619))

- MaxDB

Ez a dokumentum bemutatja az **SAP Bobi platform Windows rendszeren való** üzembe helyezésének irányelveit a Azure SQL Database és az **SAP Bobi platformmal Linux** rendszeren a Azure Database for MySQL használatával. Az SAP BusinessObjects BI platform Azure-on való futtatásának ajánlott módszere is.

## <a name="sizing"></a>Méretezés

A méretezés az alkalmazás hatékony futtatásához szükséges hardverkövetelmények meghatározásának folyamata. Az SAP BOBI platform esetében a méretezést a [gyors méretezést](https://www.sap.com/about/benchmark/sizing.quick-sizer.html#quick-sizer)használó SAP-méretezési eszközzel kell elvégezni. Az eszköz a bemenet alapján biztosítja az SAP-t, amelyet ezután az SAP-hez hitelesített Azure-beli virtuálisgép-típusokhoz kell rendelni. A [1928533](https://launchpad.support.sap.com/#/notes/1928533) -es SAP-Megjegyzés tartalmazza a támogatott SAP-termékek és az Azure-beli virtuálisgép-típusok listáját az SAP-val együtt. A méretezéssel kapcsolatos további információkért tekintse meg az [SAP BI méretezési útmutatóját](https://wiki.scn.sap.com/wiki/display/BOBJ/Sizing+and+Deploying+SAP+BusinessObjects+BI+4.x+Platform+and+Add-Ons).

Az Azure az SAP BOBI platform tárolási igényéhez különböző típusú [Managed Disks](../../managed-disks-overview.md)biztosít. Az SAP BOBI telepítési könyvtára esetén ajánlott a prémium szintű felügyelt lemez használata, valamint a virtuális gépeken futó adatbázis esetében az adatbázis-kezelő rendszerbe [állítása az SAP](dbms_guide_general.md)számítási feladathoz című témakörben ismertetett útmutatást követve.

Az Azure két DBaaS kínál az SAP BOBI platform adatrétegéhez – [Azure SQL Database](https://azure.microsoft.com/en-us/services/sql-database) (Windows rendszeren futó bi-alkalmazás) és [Azure Database for MySQL](https://azure.microsoft.com/en-us/services/mysql) (a Linux és Windows rendszeren futó bi-alkalmazás). A méretezési eredmény alapján tehát kiválaszthatja az igényeinek leginkább megfelelő vásárlási modellt.

> [!Tip]
> A gyors méretezési hivatkozáshoz vegye fontolóra az 800 SAP = 1 vCPU, miközben az SAP BOBI platform adatbázis-rétegének SAP-eredményét az Azure adatbázis-szolgáltatásként (Azure SQL Database vagy Azure Database for MySQL) térképezi fel.

### <a name="sizing-models-for-azure-sql-database"></a>Méretezési modellek az Azure SQL Database-hez

Azure SQL Database a következő három beszerzési modellt kínálja:

- Virtuálismag-alapú

  Lehetővé teszi, hogy kiválassza a virtuális mag számát, a memória mennyiségét, valamint a tárterület mennyiségét és sebességét. A virtuális mag-alapú vásárlási modell azt is lehetővé teszi, hogy a megtakarítások megszerzéséhez a [SQL Server Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) is használja. Ez a modell megfelel a rugalmasságot, a vezérlést és az átláthatóságot biztosító ügyfelek számára.

  A virtuális mag-modellben három [szolgáltatási rétegbeli lehetőség](../../../azure-sql/database/service-tiers-vcore.md#service-tiers) áll rendelkezésre, amelyek a következőket tartalmazzák: általános célú, üzletileg kritikus és nagy kapacitású. A szolgáltatási szinten a rendelkezésre állással és a vész-helyreállítással kapcsolatos tárolási architektúra, terület, I/O-korlátok és üzletmenet-folytonossági lehetőségek határozzák meg. A következő magas szintű részletek az egyes szolgáltatási szinteknél –

  1. **Általános célú** szolgáltatási szinten a legmegfelelőbb az üzleti számítási feladatokhoz. Költségvetés-orientált, kiegyensúlyozott és méretezhető számítási és tárolási lehetőségeket kínál. További információt az erőforrás- [beállítások és a korlátozások](../../../azure-sql/database/resource-limits-vcore-single-databases.md#general-purpose---provisioned-compute---gen5)című témakörben talál.
  2. **Üzletileg kritikus** szolgáltatási szinten számos elszigetelt replika használatával biztosítható, hogy az üzleti alkalmazások a lehető legnagyobb rugalmasságot biztosítsák a hibák számára, és az adatbázis-replika legmagasabb I/O-teljesítményét adja meg. További információt az erőforrás- [beállítások és a korlátozások](../../../azure-sql/database/resource-limits-vcore-single-databases.md#business-critical---provisioned-compute---gen5)című témakörben talál.
  3. A **nagy kapacitású** szolgáltatási réteg a kiválóan méretezhető tárolási és olvasási méretezési követelményekkel rendelkező üzleti számítási feladatok esetében ajánlott. Nagyobb rugalmasságot biztosít a hibákhoz, mivel több elkülönített adatbázis-replika konfigurációját is lehetővé teszi. További információt az erőforrás- [beállítások és a korlátozások](../../../azure-sql/database/resource-limits-vcore-single-databases.md#hyperscale---provisioned-compute---gen5)című témakörben talál.

- DTU-alapú

  A DTU-alapú vásárlási modell a számítási, a memória-és az I/O-erőforrások keverékét kínálja három szolgáltatási szinten a könnyű és nagy adatbázis-számítási feladatok támogatásához. Az egyes szintjein belüli számítási méretek különböző mennyiségű erőforrást biztosítanak, amelyhez további tárolási erőforrásokat adhat hozzá. A legjobb megoldás az olyan ügyfelek számára, akik egyszerű, előre konfigurált erőforrás-beállításokat szeretnének használni.

  A DTU-alapú vásárlási modellben a [szolgáltatási szintek](../../../azure-sql/database/service-tiers-dtu.md#compare-the-dtu-based-service-tiers) különböző számítási méretekből állnak, amelyek rögzített mennyiségű foglalt tárterülettel, rögzített megőrzési időtartammal és rögzített árral rendelkeznek.

- Kiszolgáló nélküli

  A kiszolgáló nélküli modell automatikusan méretezi a számítási feladatok igénye alapján történő számítást, és a másodpercenként felhasznált számítások mennyiségére vonatkozó számlákat. A kiszolgáló nélküli számítási rétegek automatikusan szüneteltetik az adatbázisokat az inaktív időszakok során, amikor csak a tárterületet számlázzák, és automatikusan folytatják az adatbázisokat, amikor a tevékenység visszatér. További információt az erőforrás- [beállítások és a korlátozások](../../../azure-sql/database/resource-limits-vcore-single-databases.md#general-purpose---serverless-compute---gen5)című témakörben talál.
  
  Sokkal alkalmasabb az időszakos, kiszámíthatatlan használathoz, és az idő múlásával alacsony átlagos számítási kihasználtságot biztosít. Így ez a modell nem éles környezetben üzemelő SAP BOBI-telepítéshez használható.

> [!Note]
> Az SAP BOBI esetében érdemes virtuális mag-alapú modellt használni, és az üzleti igények alapján általános célú vagy üzletileg kritikus szolgáltatási szintet választhat.

### <a name="sizing-models-for-azure-database-for-mysql"></a>Méretezési modellek a MySQL-hez készült Azure Database-hez

A Azure Database for MySQL három különböző díjszabási szintet tartalmaz. Ezeket a számítások a virtuális mag, a memória virtuális mag, valamint a dátum tárolására használt tárolási technológia alapján differenciálják. A beállítások magas szintű részletei és a különböző attribútumokkal kapcsolatos további részletekért tekintse meg a Azure Database for MySQL [díjszabási szintjét](../../../mysql/concepts-pricing-tiers.md) .

- Alapszintű

  Ez a cél számítási feladatokhoz használható, amelyek könnyű számítást és I/O-teljesítményt igényelnek.

- Általános célú

  A szolgáltatás a legtöbb olyan üzleti számítási feladat számára alkalmas, amely kiegyensúlyozott számítást és memóriát igényel a méretezhető I/O-átviteli sebességgel.

- Memóriaoptimalizált

  A nagy teljesítményű adatbázis-munkaterhelések esetében, amelyek memóriabeli teljesítményt igényelnek a gyorsabb tranzakció-feldolgozáshoz és a nagyobb egyidejűséghez.

> [!Note]
> Az SAP BOBI esetében célszerű a általános célú vagy a memória optimalizált díjszabási szintjét használni az üzleti munkaterhelés alapján.

## <a name="azure-resources"></a>Azure-erőforrások

### <a name="choosing-regions"></a>Régiók kiválasztása

Az Azure-régió egy vagy több adatközpont, amely tartalmazza a különböző Azure-szolgáltatások futtatásához és üzemeltetéséhez szükséges infrastruktúrát. Ez az infrastruktúra nagy számú csomópontot foglal magában, amelyek számítási csomópontként vagy tárolási csomópontként működnek, vagy futtatják a hálózati funkciókat. Nem minden régió nyújt ugyanazokat a szolgáltatásokat.

Az SAP BI platform különböző összetevőket tartalmaz, amelyek bizonyos virtuálisgép-típusokat, tárolókat, például Azure Files vagy Azure NetApp Files vagy adatbázis-szolgáltatást (DBaaS) igényelnek az adatszintjéhez, amely esetleg nem érhető el bizonyos régiókban. Megtalálhatja a virtuálisgép-típusokkal, az Azure Storage-típusokkal vagy a [régiókban elérhető](https://azure.microsoft.com/en-us/global-infrastructure/services/) egyéb Azure-szolgáltatásokkal kapcsolatos pontos információkat. Ha már futtatta az SAP-rendszereit az Azure-ban, valószínűleg a régiója azonosítható. Ebben az esetben először meg kell vizsgálnia, hogy a szükséges szolgáltatások elérhetők-e ezekben a régiókban az SAP BI platform architektúrájának eldöntéséhez.

### <a name="availability-zones"></a>Rendelkezésreállási zónák

Availability Zones fizikailag különálló helyet az Azure-régión belül. Az egyes rendelkezésre állási zónák egy vagy több olyan adatközpontból állnak, amelyek független energiaellátással, hűtéssel és hálózatkezeléssel rendelkeznek.

Ha magas rendelkezésre állást szeretne biztosítani az SAP BI platform minden egyes szintjénél, a virtuális gépeket a rendelkezésre állási zónák között terjesztheti a magas rendelkezésre állási keretrendszer megvalósításával, amely az Azure-ban biztosíthatja a legjobb SLA-t Az Azure-beli virtuális gépekre vonatkozó SLA-ban keresse meg a [virtuális gépek SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/)-i legújabb verzióját.

Az adatcsomagok esetében az Azure Database as Service (DBaaS) szolgáltatás alapértelmezés szerint magas rendelkezésre állási keretrendszert biztosít. Csak ki kell választania a régiót és a szolgáltatást, amely magas rendelkezésre állást, redundanciát és rugalmasságot biztosít a tervezett és nem tervezett leállások miatt, anélkül, hogy további összetevőket kellene konfigurálnia. Az Azure-ban támogatott DBaaS-ajánlatokra vonatkozó SLA-val kapcsolatos további részletekért tekintse meg a [magas rendelkezésre állást](../../../mysql/concepts-high-availability.md) a Azure SQL Database Azure Database for MySQL és [magas rendelkezésre állását](../../../azure-sql/database/high-availability-sla.md).

### <a name="availability-sets"></a>Rendelkezésre állási csoportok

A rendelkezésre állási csoport logikai csoportosítási funkciója a virtuális gépek (VM) erőforrásainak üzembe helyezése egymástól. Az Azure gondoskodik arról, hogy a rendelkezésre állási csoporton belüli virtuális gépek több fizikai kiszolgáló, számítási állvány, tárolási egység és hálózati kapcsolók között fussanak. Ha hardveres vagy szoftveres hiba történik, a rendszer csak a virtuális gépek egy részhalmazát érinti, és a teljes megoldás működőképes marad. Így ha a virtuális gépek a rendelkezésre állási csoportokban vannak elhelyezve, az Azure Fabric Controller különböző [hibatűrési](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/planning-guide.md#fault-domains) és [frissítési](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/planning-guide.md#upgrade-domains) tartományok között osztja el a virtuális gépeket, hogy megakadályozza, hogy az összes virtuális gép elérhetetlenné váljon az infrastruktúra karbantartása vagy meghibásodása miatt egy tartalék tartományon belül.

Az SAP BI platform számos különböző összetevőt tartalmaz, és az architektúra megtervezése során meg kell győződnie arról, hogy a jelen összetevő mindegyike rugalmasan akadályozza a fennakadást. Ez úgy érhető el, hogy az Azure Virtual Machines szolgáltatást helyezi üzembe a rendelkezésre állási csoportokban található egyes összetevőkön. Ne feledje, ha a különböző virtuálisgép-családokból álló virtuális gépeket egy rendelkezésre állási csoporton belül keveri, előfordulhat, hogy olyan problémák merülhetnek fel, amelyek megakadályozzák, hogy egy adott virtuálisgép-típust is tartalmazzon az ilyen rendelkezésre állási Tehát külön rendelkezésre állási csoporttal rendelkezik a webalkalmazáshoz, a BI-alkalmazás az SAP BI platformhoz az architektúra áttekintésében.

Azon frissítési és tartalék tartományok száma, amelyek Azure-beli rendelkezésre állási csoporton belül is használhatók, véges. Így ha továbbra is egyetlen rendelkezésre állási csoportba helyezi a virtuális gépeket, két vagy több virtuális gép végül ugyanabban a hiba-vagy frissítési tartományban fog megjelenni. További információ: az Azure Virtual Machines tervezésének és megvalósításának Azure-beli [rendelkezésre állási készletek](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/planning-guide.md#azure-availability-sets) című szakasza az SAP-dokumentumhoz.

Az Azure rendelkezésre állási csoportok fogalmának megismeréséhez és a rendelkezésre állási csoportok hibákhoz és frissítési tartományokhoz való kapcsolódásához olvassa el a [rendelkezésre állás kezelése](../../availability.md) című cikket.

> [!Important]
> A Azure Availability Zones és az Azure rendelkezésre állási csoportjaira vonatkozó fogalmak kölcsönösen kizárják egymást. Ez azt jelenti, hogy egy pár vagy több virtuális gépet telepít egy adott rendelkezésre állási zónába vagy egy Azure-beli rendelkezésre állási csoportba. De nem mindkettő.

### <a name="virtual-machines"></a>Virtual machines (Virtuális gépek)

Az Azure Virtual Machine egy olyan szolgáltatás, amely lehetővé teszi Egyéni rendszerképek üzembe helyezését az Azure-ban infrastruktúra-szolgáltatási (IaaS) példányként. Leegyszerűsíti az alkalmazások karbantartását és üzemeltetését azáltal, hogy igény szerinti számítási és tárolási kapacitást biztosít a webalkalmazások és a csatlakoztatott alkalmazások üzemeltetéséhez, méretezéséhez és kezeléséhez.

Az Azure számos virtuális gépet kínál az alkalmazás igényeinek megfelelően. Az SAP számítási feladataihoz azonban az Azure leszűkítte a kijelölést olyan különböző virtuálisgép-családokra, amelyek az SAP-munkaterheléshez megfelelőek, és pontosabban SAP HANA munkaterhelést További információk: az Azure-beli [üzembe helyezések által támogatott SAP-szoftverek](sap-supported-product-on-azure.md).

Az SAP BI platform méretezése alapján le kell képeznie az Azure-beli virtuális gépre vonatkozó követelményt, amelyet az Azure az SAP-termék esetében támogat. Az [1928533](https://launchpad.support.sap.com/#/notes/1928533) -es SAP-Megjegyzés jó kiindulási pont, amely az SAP-termékek támogatott Azure-beli virtuálisgép-típusait sorolja fel Windows és Linux rendszeren. Azt is vegye figyelembe, hogy a tisztán támogatott virtuálisgép-típusok kiválasztása után is meg kell vizsgálnia, hogy az adott virtuális gépek típusa elérhető-e az adott régióban. A virtuális gép típusának rendelkezésre állását a [régiók oldalon elérhető termékek területen](https://azure.microsoft.com/global-infrastructure/services/) tekintheti meg. A díjszabási modell kiválasztásához tekintse meg az SAP számítási feladatait [Az Azure Virtual Machines szolgáltatásban](planning-guide.md#azure-virtual-machines-for-sap-workload)

### <a name="storage"></a>Tárolás

Az Azure Storage egy Azure által felügyelt felhőalapú szolgáltatás, amely kiválóan elérhető, biztonságos, tartós, méretezhető és redundáns tárolást biztosít. Egyes tárolási típusok esetében korlátozott az SAP-forgatókönyvek használata. Számos Azure-beli tárolási típus azonban jól alkalmazható, vagy speciális SAP-munkaterhelési forgatókönyvekhez van optimalizálva. További információkért tekintse meg [Az Azure Storage-típusok az SAP](planning-guide-storage.md) számítási feladatokhoz című útmutatót, mivel ez kiemeli az SAP-hoz elérhető különböző tárolási lehetőségeket.

Az Azure Storage-ban különböző tárolási típusok érhetők el az ügyfelek számára, és ugyanazokat az adatokat is megtekintheti a cikkben, hogy [milyen típusú lemezek érhetők el az Azure-ban?](../../disks-types.md). Az SAP BOBI platform a következő Azure Storage-t használja az alkalmazás létrehozásához:

- Azure által felügyelt lemezek

  Ez egy, az Azure által felügyelt blokk szintű tárolási kötet. Az Azure Virtual Machines szolgáltatásban az SAP BOBI platform Application Server-kiszolgálókhoz és-adatbázisokhoz is használhatja a lemezeket. Különböző típusú [Azure-Managed Disks](../../managed-disks-overview.md) érhető el, de ajánlott [prémium SSD](../../disks-types.md#premium-ssd) -ket használni az SAP Bobi platform-alkalmazáshoz és-adatbázishoz.

  Az alábbi példában a prémium SSD-k használhatók a BOBI platform telepítési könyvtárához. A virtuális gépre telepített adatbázisok esetében a felügyelt lemezeket az irányelvek alapján, az adatelemzéshez és a kötethez is használhatja. A CMS-és naplózási adatbázisok általában kicsik, és nem ugyanaz a tárolási teljesítménybeli követelmények, mint más SAP OLTP/OLAP-adatbázisok esetén.

- Prémium szintű Azure-fájlok vagy Azure NetApp Files

  Az SAP BOBI platformon a file adattár-kiszolgáló (FRS) arra a lemez-könyvtárakra vonatkozik, ahol a tartalmak, például a jelentések, a világegyetemek és a kapcsolatok tárolódnak, amelyeket az adott rendszer összes alkalmazás-kiszolgálója használ. Az [Azure Premium Files](../../../storage/files/storage-files-introduction.md) vagy [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) Storage használható megosztott fájlrendszerként az SAP Bobi-alkalmazásokhoz. Mivel ez a tárolási ajánlat nem érhető el az összes régióban, tekintse meg a [régiók hely által elérhető termékek](https://azure.microsoft.com/en-us/global-infrastructure/services/) című témakört a naprakész információk megkereséséhez.

  Ha a szolgáltatás nem érhető el a régióban, létrehozhat NFS-kiszolgálót, amelyről megoszthatja a fájlrendszert az SAP BOBI alkalmazással. Emellett a magas rendelkezésre állást is figyelembe kell vennie.

![SAP BusinessObjects BI platform Storage-elrendezés az Azure-ban](media/businessobjects-deployment-guide/businessobjects-storage-layout.png)

### <a name="networking"></a>Hálózatkezelés

Az SAP BOBI olyan jelentéskészítési és elemzési BI-platform, amely nem rendelkezik üzleti adattal. Így a rendszer a többi adatbázis-kiszolgálóhoz csatlakozik, ahonnan beolvassa az összes információt, és betekintést nyújt a felhasználók számára. Az Azure egy hálózati infrastruktúrát biztosít, amely lehetővé teszi az SAP BI platformmal megvalósítható összes forgatókönyv leképezését, például a helyszíni rendszerhez való csatlakozást, a különböző virtuális hálózatok rendszereit és egyebeket. További információ: [Microsoft Azure hálózatkezelés az SAP-Munkaterheléshez](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/planning-guide.md#microsoft-azure-networking).

Az adatbázis-szolgáltatásként kínált szolgáltatások esetében minden újonnan létrehozott adatbázisnak (Azure SQL Database vagy Azure Database for MySQL) van olyan tűzfala, amely blokkolja az összes külső kapcsolatot. Ahhoz, hogy a BI platform virtuális gépei hozzáférhessenek a DBaaS szolgáltatáshoz, meg kell adnia egy vagy több kiszolgálói szintű tűzfalszabályok használatát, hogy engedélyezze a hozzáférést a DBaaS-kiszolgálóhoz. További információ: az Azure SQL Database Azure Database for MySQL és [hálózati hozzáférés-vezérlési](../../../azure-sql/database/network-access-controls-overview.md) szakaszának [Tűzfalszabályok](../../../mysql/concepts-firewall-rules.md) .

## <a name="next-steps"></a>Következő lépések

- [SAP BusinessObjects BI platform Linux rendszeren való üzembe helyezése](businessobjects-deployment-guide-linux.md)
- [Azure Virtual Machines az SAP tervezéséhez és megvalósításához](planning-guide.md)
- [Azure Virtual Machines üzembe helyezés az SAP-ban](deployment-guide.md)
- [Azure Virtual Machines adatbázis-kezelői telepítés az SAP-hoz](./dbms_guide_general.md)