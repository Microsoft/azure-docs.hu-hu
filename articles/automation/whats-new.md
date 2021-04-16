---
title: A Azure Automation
description: A frissítések jelentős Azure Automation havonta frissülnek.
ms.subservice: ''
ms.topic: overview
author: mgoedtel
ms.author: magoedte
ms.date: 04/09/2021
ms.custom: references_regions
ms.openlocfilehash: f8b4d6965a8a1f046fd2459ce9fe5cce8ea45443
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531084"
---
# <a name="whats-new-in-azure-automation"></a>Újdonságok a Azure Automation?

Azure Automation folyamatosan kap fejlesztéseket. Annak érdekében, hogy naprakész maradjon a legújabb fejlesztésekkel kapcsolatban, ez a cikk a következő információkkal rendelkezik:

- A legújabb kiadások
- Ismert problémák
- Hibajavítások

Ez az oldal havonta frissül, ezért rendszeresen vissza kell látogatni.

## <a name="march-2021"></a>2021. március

### <a name="new-azure-automation-built-in-policies"></a>Új Azure Automation beépített szabályzatok

**Írja be a következőt:** Új funkció

Azure Automation 5 új beépített szabályzatot adott hozzá:

- Az Automation-fiókoknak le kell tiltanunk a nyilvános hálózati hozzáférést,
- Azure Automation fióknak ügyfél által kezelt kulcsokat kell használnia az adatok titkosításához
- A Azure Automation konfigurálása a nyilvános hálózati hozzáférés letiltásához
- Privát végpontkapcsolatok konfigurálása Azure Automation fiókokon
- Engedélyezni kell a privát végpontkapcsolatokat az Automation-fiókokon.

További [részleteket a szabályzatok](./policy-reference.md) referenciáját tartalmazó cikkben talál.

### <a name="support-for-automation-and-state-configuration-declared-ga-in-south-india"></a>Az Automation és a State Configuration deklarált GA támogatása Dél-Indiában

**Írja be a következőt:** Új funkció

Folyamatautomatizálási és állapotkonfigurációs képességek használata Dél-Indiában. További [információért olvassa](https://azure.microsoft.com/updates/azure-automation-in-south-india-region/) el a bejelentést.

### <a name="support-for-automation-and-state-configuration-declared-ga-in-uk-west"></a>Az Automation és a State Configuration ga deklarált támogatása a Egyesült Királyság nyugati régiója

**Írja be a következőt:** Új funkció

A folyamatautomatizálási és állapotkonfigurációs képességeket a Egyesült Királyság nyugati régiója. További információért olvassa el a [bejelentést.](https://azure.microsoft.com/updates/azure-automation-in-uk-west-region/)

### <a name="support-for-automation-and-state-configuration-declared-ga-in-uae-central"></a>Az Automation és a State Configuration deklarált GA támogatása az Egyesült Arab Emírségek középső területén

**Írja be a következőt:** Új funkció

Folyamatautomatizálási és állapotkonfigurációs képességek használata az UAE Centralban. További [információért olvassa](https://azure.microsoft.com/updates/azure-automation-in-uae-central-region/) el a bejelentést.

### <a name="support-for-automation-and-state-configuration-available-in-australia-central-2--norway-west-and-france-south"></a>Az Automation és a State Configuration 2. ausztráliai középső régiója, Nyugat-Ausztrália és Dél-Franciaország területén érhető el

**Írja be a következőt:** Új funkció

Az Egyes régiók földrajzi helyének kiválasztásával további információt az [Adathellyel](https://azure.microsoft.com/global-infrastructure/data-residency/) kapcsolatos oldalon láthat.

### <a name="new-scripts-added-for-installing-hybrid-worker-on-windows-and-linux"></a>Új szkriptek hozzáadva a hibrid feldolgozó Windows és Linux rendszeren való telepítéséhez

**Írja be a következőt:** Új funkció

A Azure Automation GitHub-adattárhoz két [](https://github.com/azureautomation) új szkript lett hozzáadva, amelyek a Azure Automation egyik fő forgatókönyvét, a hibrid runbook-feldolgozó Windows vagy Linux rendszerű gépen való beállítását célják. A szkript létrehoz egy új virtuális gépet, vagy egy meglévőt használ, szükség esetén létrehoz egy Log Analytics-munkaterületet, telepíti a Windowshoz vagy a Linuxhoz használt Log Analytics-ügynököt, és regisztrálja a gépet a Log Analytics-munkaterületen. A Windows-szkript neve **Automation létrehozása Windows HybridWorker,** a Linux-szkript pedig **Create Automation Linux HybridWorker**.

### <a name="invoke-runbook-through-an-azure-resource-manager-template-webhook"></a>Runbook meghívása Azure Resource Manager webhookkal

**Írja be a következőt:** Új funkció

További [részletekért lásd: Use a webhook from an ARM template (Webhook](./automation-webhooks.md#use-a-webhook-from-an-arm-template) használata ARM-sablonból).

### <a name="azure-update-management-now-supports-centos-8x-red-hat-enterprise-linux-server-8x-and-suse-linux-enterprise-server-15"></a>Az Azure Update Management mostantól támogatja a Centos 8.x, Red Hat Enterprise Linux Server 8.x és SUSE Linux Enterprise Server 15

**Írja be a következőt:** Új funkció

További [részletekért tekintse](./update-management/overview.md#supported-operating-systems) meg a támogatott Linux operációs rendszerek teljes listáját.

### <a name="in-region-data-residency-support-for-brazil-south-and-south-east-asia"></a>Régión belül az adatok helyének támogatása Dél- és Dél-Brazília Kelet-Ázsia 

**Írja be a következőt:** Új funkció

Dél- és Délkelet-Ázsia kivételével minden régióban az Azure Automation adatait egy másik régióban (az Azure párosított régiójában) tárolják az üzletmenet-folytonosság és a vészhelyreállítás (BCDR) biztosítása érdekében. Jelenleg csak Brazília és Délkelet-Ázsia esetében tároljuk a Azure Automation adatokat ugyanabban a régióban, hogy igazodni tudjuk az ezekre a régiókra vonatkozó adathellyel kapcsolatos követelményekhez. További [részletek: Georeplikáció](./automation-managing-data.md#geo-replication-in-azure-automation) Azure Automation-ban.

## <a name="february-2021"></a>2021. február

### <a name="support-for-automation-and-state-configuration-declared-ga-in-japan-west"></a>Az Automation és a State Configuration deklarált GA támogatása Nyugat-Japánban

**Írja be a következőt:** Új funkció

Automation-fiók és State Configuration a nyugat-japán régióban. További információért olvassa el a [bejelentést.](https://azure.microsoft.com/updates/azure-automation-in-japan-west-region/)

### <a name="introduced-custom-azure-policy-compliance-to-enforce-runbook-execution-on-hybrid-worker"></a>Egyéni megfelelőségi szabályzatok Azure Policy a runbookok hibrid feldolgozón való végrehajtásának kényszerítéséhez

**Írja be a következőt:** Új funkció

Az új megfelelőségi Azure Policy lehetővé teszi a feladatok, webhookok és feladatütemezők létrehozását, hogy csak hibrid feldolgozói csoportokon fusson.

### <a name="update-management-availability-in-east-us-france-central-and-north-europe-regions"></a>Update Management az USA keleti régiójában, Közép-Franciaországban és Észak-Európában

**Írja be a következőt:** Új funkció

Az Automation Update Management szolgáltatás az USA keleti régiójában, Közép-Franciaországban és Észak-Európában érhető el. A [változást tükröző dokumentáció](how-to/region-mappings.md) frissítéseit a Támogatott régiók leképezése dokumentumban találhatja meg.

## <a name="january-2021"></a>2021. január

### <a name="support-for-automation-and-state-configuration-declared-ga-in-switzerland-west"></a>Automation és State Configuration támogatása Nyugat-Svájcban

**Írja be a következőt:** Új funkció

Automation-fiók és State Configuration rendelkezésre állása a Nyugat-Svájc régióban. További információért olvassa el a [bejelentést.](https://azure.microsoft.com/updates/azure-automation-in-switzerland-west-region/)

### <a name="added-python-3-script-to-import-module-with-multiple-dependencies"></a>Python 3-szkript hozzáadva a modul importáláshoz több függőséggel

**Írja be a következőt:** Új funkció

A szkript letölthető a [GitHub-adattárból.](https://github.com/azureautomation/runbooks/blob/master/Utility/Python/import_py3package_from_pypi.py) 
 
### <a name="hybrid-runbook-worker-role-support-for-centos-8xrhel-8xsles-15"></a>Hibrid runbook-feldolgozói szerepkör támogatása a Centos 8.x/RHEL 8.x/SLES 15-höz

**Típus.** Új funkció

A hibrid runbook-feldolgozó szolgáltatás a CentOS 8.x, a REHL 8.x és az SLES 15 disztribúciókat támogatja csak a folyamatautomatizáláshoz a hibrid runbook-feldolgozókon. A [változásoknak megfelelően a](automation-linux-hrw-install.md#supported-linux-operating-systems) dokumentáció frissítéseit lásd: Supported operating systems (Támogatott operációs rendszerek).

### <a name="update-management-and-change-tracking-availability-in-australia-east-east-asia-west-us-and-central-us-regions"></a>Update Management és Change Tracking kelet-ausztráliai, Kelet-Ázsia, az USA nyugati régiójában és az USA középső régiójában

**Írja be a következőt:** Új funkció

Az Automation-fiók, változáskövetés és leltározás és Update Management kelet-Ausztrália, Kelet-Ázsia, az USA nyugati régiója és az USA középső régiója érhető el. 

### <a name="introduced-public-preview-of-python-3-runbooks-in-us-government-cloud"></a>A Python 3-runbookok nyilvános előzetes verziója az USA kormányzati felhőszolgáltatásában

**Írja be a következőt:** Az új Azure Automation bevezeti a Nyilvános előzetes verzió támogatását a Python 3-felhőhöz és a hibrid runbook-végrehajtáshoz az USA kormányzati felhőrégióiban. További információért tekintse meg a [bejelentést.](https://azure.microsoft.com/updates/azure-automation-python-3-public-preview/)

### <a name="azure-automation-runbooks-moved-from-technet-script-center-to-github"></a>Azure Automation a runbookok átkerültek a TechNet Script Centerből a GitHubba

**Írja be a következőt:** A változás megterve

A TechNet Script Centert a rendszer kiveszi, és a Runbook-katalógusban üzemeltetett összes runbookot áthelyezte az [Automation GitHub-szervezetbe.](https://github.com/azureautomation) További információkért olvassa el a [Azure Automation GitHubra való áthelyezését.](https://techcommunity.microsoft.com/t5/azure-governance-and-management/azure-automation-runbooks-moving-to-github/ba-p/2039337)

## <a name="december-2020"></a>2020. december

### <a name="azure-automation-and-update-management-private-link-ga"></a>Azure Automation és Update Management Private Link GA

**Írja be a következőt:** Új funkció

Azure Automation és Update Management azure-beli globális és kormányzati felhők általánosan általánosan és általánosan általánosan hirdetett támogatása. Azure Automation Private Link lehetővé tette a runbookok biztonságos végrehajtását egy hibrid feldolgozói szerepkörben, a gépek Update Management javításával, a runbook webhookon keresztüli State Configuration-n keresztüli alkalmazásával, valamint a gépek megfelelő használatának biztosítása érdekében egy szolgáltatással. További információért olvassa el a Azure Automation Private Link [cikkét.](https://azure.microsoft.com/updates/azure-automation-private-link)

### <a name="azure-automation-classified-as-grade-c-certified-on-accessibility"></a>Azure Automation C besorolású az akadálymentességről

**Írja be a következőt:** Új funkció

A Microsoft-termékek kisegítő lehetőségei segítenek az ügynökségeknek a globális akadálymentesség követelményeinek való megfelelésben. A [blog bejelentési oldalán](https://cloudblogs.microsoft.com/industry-blog/government/2018/09/11/accessibility-conformance-reports/) keressen rá a Azure Automation **az** Automation szolgáltatás Akadálymentesség-megfelelőség jelentésére.

### <a name="support-for-automation-and-state-configuration-ga-in-uae-north"></a>Az Automation és a State Configuration ga támogatása Egyesült Arab Emírségek északi részén

**Írja be a következőt:** Új funkció

Automation-fiók és State Configuration az Egyesült Arab Emírségek északi régiójában. További információért olvassa el a [bejelentést.](https://azure.microsoft.com/updates/azure-automation-in-uae-north-region/)

### <a name="support-for-automation-and-state-configuration-ga-in-germany-west-central"></a>Az Automation és a State Configuration GA támogatása Nyugat-Közép-Németországban

**Írja be a következőt:** Új funkció

Automation-fiók és State Configuration rendelkezésre állása a nyugat-németországi régióban. További információért olvassa el a [bejelentést.](https://azure.microsoft.com/updates/azure-automation-in-germany-west-central-region/)

### <a name="dsc-support-for-oracle-6-and-7"></a>DSC-támogatás Oracle 6-os és 7-es hez

**Írja be a következőt:** Új funkció

6 Oracle Linux 7 gép kezelése az Automation State Configuration. A [módosításoknak megfelelően a dokumentáció](https://github.com/Azure/azure-linux-extensions/tree/master/DSC#4-supported-linux-distributions) frissítéseit a Támogatott Linux-disztribúciók között találhatja meg.

### <a name="public-preview-for-python3-runbooks-in-automation"></a>Nyilvános előzetes verzió Python3-runbookok számára az Automationben

**Írja be a következőt:** Új funkció

Azure Automation már támogatja a Python 3 felhőalapú és hibrid runbookok nyilvános előzetes verzióban való végrehajtását az Azure globális felhő minden régiójában. További részletekért tekintse meg a [közleményt](( https://azure.microsoft.com/updates/azure-automation-python-3-public-preview/)

## <a name="november-2020"></a>2020. november

### <a name="dsc-support-for-ubuntu-1804"></a>DSC-támogatás az Ubuntu 18.04-hez

**Írja be a következőt:** Új funkció

A változásokat tükröző dokumentáció frissítéseit lásd: Supported [Linux Distros](https://github.com/Azure/azure-linux-extensions/tree/master/DSC#4-supported-linux-distributions) (Támogatott Linux-disztribúciók).

## <a name="october-2020"></a>2020. október

### <a name="support-for-automation-and-state-configuration-ga-in-switzerland-north"></a>Az Automation és a State Configuration ga támogatása Észak-Svájcban

**Írja be a következőt:** Új funkció

Automation-fiók és State Configuration Észak-Svájcban. További információért olvassa el a [bejelentést.](https://azure.microsoft.com/updates/azure-automation-in-switzerland-north-region/)

### <a name="support-for-automation-and-state-configuration-ga-in-brazil-south-east"></a>Az Automation és a State Configuration ga támogatás Dél-Kelet-Brazília területén

**Írja be a következőt:** Új funkció

Automation-fiók és State Configuration rendelkezésre állása Dél-Brazília délkeleti régiója esetében. További információért olvassa el a [bejelentést.](https://azure.microsoft.com/updates/azure-automation-in-brazil-southeast-region/)

### <a name="update-management-availability-in-south-central-us"></a>Update Management rendelkezésre állás az USA déli középső részén

**Írja be a következőt:** Új funkció

Azure Automation régióleképezés frissítve, hogy támogassa Update Management szolgáltatást az USA déli középső régiójában. A [változásnak megfelelő dokumentáció](how-to/region-mappings.md#supported-mappings) frissítéseit a Supported region mapping (Támogatott régióleképezés) dokumentumban találhatja meg.

## <a name="september-2020"></a>2020. szeptember

### <a name="startstop-vms-during-off-hours-runbooks-updated-to-use-azure-az-modules"></a>Start/Stop VMs during off-hours Azure Az modulok használatára frissített runbookok

**Írja be a következőt:** Új funkció

A Start-Stop VM-runbookok frissítve vannak, hogy az Az modulokat használják a Azure Resource Manager modulok helyére. A [Start/Stop VMs during off-hours](automation-solution-vm-management.md) dokumentáció frissítéseit az áttekintésben találhatja meg.

## <a name="august-2020"></a>2020. augusztus

### <a name="published-the-dsc-extension-to-support-azure-arc"></a>Közzétette a DSC-bővítményt a Azure Arc

**Írja be a következőt:** Új funkció

A Azure Automation State Configuration segítségével központilag tárolhatja a konfigurációkat, és fenntarthatja a hibrid csatlakoztatott gépek célállapotát az engedélyezett Azure Arc DSC virtuálisgép-bővítményen keresztül. További információkért olvassa el az [Arc-kompatibilis kiszolgálók virtuálisgép-bővítményeit áttekintő témakört.](../azure-arc/servers/manage-vm-extensions.md)

### <a name="july-2020"></a>2020. július

### <a name="introduced-public-preview-of-private-link-support-in-automation"></a>Az Automation nyilvános előzetes Private Link támogatása

**Írja be a következőt:** Új funkció

A Azure Private Link segítségével biztonságosan csatlakoztathat virtuális hálózatokat Azure Automation privát végpontok használatával. További információért olvassa el a [bejelentést.](https://azure.microsoft.com/updates/public-preview-private-link-azure-automation-is-now-available/)

### <a name="hybrid-runbook-worker-support-for-windows-server-2008-r2"></a>Hibrid runbook-feldolgozó támogatása a Windows Server 2008 R2-hez

**Írja be a következőt:** Új funkció

Az Automation hibrid runbook-feldolgozó támogatja a Windows Server 2008 R2 operációs rendszert. A [változásoknak megfelelően a](automation-windows-hrw-install.md#supported-windows-operating-system) dokumentáció frissítéseit lásd: Supported operating systems (Támogatott operációs rendszerek).

### <a name="update-management-support-for-windows-server-2008-r2"></a>Update Management Windows Server 2008 R2 támogatása

**Írja be a következőt:** Új funkció

Update Management támogatja a Windows Server 2008 R2 operációs rendszer felmérését és javítását. A [változásoknak megfelelően a](update-management/overview.md#clients) dokumentáció frissítéseit lásd: Supported operating systems (Támogatott operációs rendszerek).

### <a name="automation-diagnostic-logs-schema-update"></a>Automation diagnosztikai naplók sémafrissítése

**Írja be a következőt:** Új funkció

Módosította a naplóadatok Azure Automation Log Analytics szolgáltatásban. További információ: Forward Azure Automation job data to Azure Monitor logs (Feladatadatok [továbbítása Azure Monitor naplókba).](automation-manage-send-joblogs-log-analytics.md#filter-job-status-output-converted-into-a-json-object)

### <a name="azure-lighthouse-supports-automation-update-management"></a>Azure Lighthouse Automation-Update Management

**Írja be a következőt:** Új funkció

Azure Lighthouse a delegált erőforrás-kezelés Update Management a szolgáltatók és az ügyfelek számára. További tudnivalók [itt](https://azure.microsoft.com/blog/how-azure-lighthouse-enables-management-at-scale-for-service-providers/).

## <a name="june-2020"></a>2020. június

### <a name="automation-and-update-management-availability-in-the-us-gov-arizona-region"></a>Automatizálás és Update Management rendelkezésre állás a US Gov Arizona régióban

**Írja be a következőt:** Új funkció

Az Automation-Update Management-fiókok és -fiókok a US Gov Arizona. További információért tekintse meg a [bejelentést.](https://azure.microsoft.com/updates/azure-automation-generally-available-in-usgov-arizona-region/)

### <a name="hybrid-runbook-worker-onboarding-script-updated-to-use-az-modules"></a>Hibrid runbook-feldolgozó az Az modulok használatára frissítve

**Írja be a következőt:** Új funkció

A New-OnPremiseHybridWorker runbook frissítve lett az Az modulok támogatásához. További információért tekintse meg a csomagot a [PowerShell-galéria.](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker/1.7)

### <a name="update-management-availability-in-china-east-2"></a>Update Management rendelkezésre állás a Kína 2. keleti régiója

**Írja be a következőt:** Új funkció

Azure Automation régióleképezés frissítve, hogy támogassa Update Management szolgáltatását a Kína 2. keleti régiója régióban. A [változásnak megfelelő dokumentáció](how-to/region-mappings.md#supported-mappings) frissítéseit a Supported region mapping (Támogatott régióleképezés) dokumentumban találhatja meg.

## <a name="may-2020"></a>2020. május

### <a name="updated-automation-service-dns-records-from-region-specific-to-automation-account-specific-urls"></a>Az Automation szolgáltatás régióspecifikus DNS-rekordjai frissítve az Automation-fiókspecifikus URL-címekre

**Írja be a következőt:** Új funkció

Azure Automation DNS-rekordok frissítve vannak a privát hivatkozások támogatásához. További információért olvassa el a [bejelentést.](https://azure.microsoft.com/updates/azure-automation-updateddns-records/)

### <a name="added-capability-to-keep-automation-runbooks-and-dsc-scripts-encrypted-by-default"></a>Új képesség az Automation-runbookok és DSC-szkriptek alapértelmezés szerinti titkosításának beállításához

**Írja be a következőt:** Új funkció

Az eszközök biztonságának javítása mellett a runbookok és a DSC-szkriptek is titkosítva vannak a biztonság Azure Automation érdekében.

## <a name="april-2020"></a>2020. április

### <a name="retirement-of-the-automation-watcher-task"></a>Az Automation figyelőfeladatának kieste

**Írja be a következőt:** A változás megterve

Azure Logic Apps az események figyelése, az ismétlődő feladatok ütemezése és a műveletek aktiválásának ajánlott és támogatott módja. A Watcher-feladat funkcióiba nem kell további befektetéseket bevetni. További információ: Ismétlődő automatizált feladatok ütemezése [és futtatása a Logic Apps.](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)

## <a name="march-2020"></a>2020. március

### <a name="support-for-impact-level-5-il5-compute-isolation-in-azure-commercial-and-government-cloud"></a>Az 5- (IL5-) hatásszint számítási elkülönítésének támogatása az Azure kereskedelmi és kormányzati felhőben

**Típus:**

Azure Automation hibrid runbook-feldolgozó használható a Azure Government az 5. hatásszintű számítási feladatok támogatásához. További tudnivalókért tekintse meg a [dokumentációt.](automation-hybrid-runbook-worker.md#support-for-impact-level-5-il5)

## <a name="february-2020"></a>2020. február

### <a name="introduced-support-for-azure-virtual-network-service-tags"></a>Bevezettük az Azure-beli virtuális hálózati szolgáltatáscímkék támogatását

**Írja be a következőt:** Új funkció

A szolgáltatáscímkék automatizálási támogatása lehetővé teszi vagy megtagadja az Automation szolgáltatás forgalmát a forgatókönyvek egy részéhez. További tudnivalókért tekintse meg a [dokumentációt.](automation-hybrid-runbook-worker.md#service-tags)

### <a name="enable-tls-12-support-for-azure-automation-service"></a>TLS 1.2-támogatás engedélyezése Azure Automation szolgáltatáshoz

**Írja be a következőt:** A változás megterve

Azure Automation teljes mértékben támogatja a TLS 1.2-t és az összes ügyfélhívást (webhookokon, DSC-csomópontokon és hibrid feldolgozón keresztül). A TLS 1.1 és a TLS 1.0 továbbra is támogatott a régebbi ügyfelekkel való visszamenőleges kompatibilitás érdekében, amíg az ügyfelek szabványosítani nem és teljes mértékben át nem állnak a TLS 1.2-re.

## <a name="january-2020"></a>2020. január

### <a name="introduced-public-preview-of-customer-managed-keys-for-azure-automation"></a>Az ügyfél által felügyelt kulcsok nyilvános előzetes verziója a Azure Automation

**Írja be a következőt:** Új funkció

Az ügyfelek saját felügyelt kulcsokkal kezelhetik és Azure Automation az adattitkosítást. További információ: Ügyfél által kezelt [kulcsok használata.](automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account)

### <a name="retirement-of-azure-service-management-asm-rest-apis-for-azure-automation"></a>Az Azure Szolgáltatáskezelési (ASM) REST API-k kivezetése a Azure Automation

**Írja be a következőt:** Nyugdíjba

Az Azure Service Management (ASM) REST API-Azure Automation 2020. január 30. után ki leszvezetésre és támogatásra nem lesz szükség. További információért tekintse meg a [bejelentést.](https://azure.microsoft.com/updates/azure-automation-service-management-rest-apis-are-being-retired-april-30-2019/)

## <a name="next-steps"></a>Következő lépések

Ha szeretne közreműködni a dokumentációban, Azure Automation Docs közreműködői [útmutatójában talál.](/contribute/)
