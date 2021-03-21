---
title: A Azure Automation újdonságai
description: A Azure Automation havonta frissülő jelentős frissítések.
ms.subservice: ''
ms.topic: overview
author: mgoedtel
ms.author: magoedte
ms.date: 02/23/2021
ms.custom: references_regions
ms.openlocfilehash: 899249c98c3ce0fdf061b1e689182f71c120aa13
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101729394"
---
# <a name="whats-new-in-azure-automation"></a>A Azure Automation újdonságai

Azure Automation folyamatosan fejleszti a fejlesztéseket. A legújabb fejleményekkel naprakészen tarthatja a következő információkat:

- A legújabb kiadások
- Ismert problémák
- Hibajavítások

Ez az oldal havonta frissül, ezért rendszeresen újra kell látogatnia.

## <a name="february-2021"></a>2021. február

### <a name="support-for-automation-and-state-configuration-declared-ga-in-japan-west"></a>Az Automation és az állapot konfigurációjának támogatása a japán nyugati régiójában

**Írja be a következőt:** Új funkció

Az Automation-fiók és az állapot-konfiguráció rendelkezésre állása Japánban nyugati régiójában. További információért olvassa el a [bejelentést](https://azure.microsoft.com/updates/azure-automation-in-japan-west-region/).

### <a name="introduced-custom-azure-policy-compliance-to-enforce-runbook-execution-on-hybrid-worker"></a>Az egyéni Azure Policy megfelelőségének bevezetése a runbook végrehajtásának érvénybe léptetéséhez hibrid feldolgozón

**Írja be a következőt:** Új funkció

Az új Azure Policy megfelelőségi szabály lehetővé teszi a feladatok, webhookok és feladatütemezés futtatását csak hibrid feldolgozói csoportokon.

### <a name="update-management-availability-in-east-us-france-central-and-north-europe-regions"></a>Update Management rendelkezésre állás az USA keleti régiójában, Közép-és Észak-Európában

**Írja be a következőt:** Új funkció

Az Automation Update Management funkció az USA keleti régiójában, Közép-és Észak-Európában érhető el. A módosítást tükröző dokumentáció a [támogatott régiók leképezése](how-to/region-mappings.md) a frissítésekhez című témakörben található.

## <a name="january-2021"></a>2021. január

### <a name="support-for-automation-and-state-configuration-declared-ga-in-switzerland-west"></a>Az Automation és az állapot konfigurációjának támogatása a Nyugat-Svájcban

**Írja be a következőt:** Új funkció

Az Automation-fiók és az állapot konfigurációjának rendelkezésre állása a Nyugat-Svájc régióban. További információért olvassa el a [bejelentést](https://azure.microsoft.com/updates/azure-automation-in-switzerland-west-region/).

### <a name="added-python-3-script-to-import-module-with-multiple-dependencies"></a>Python 3-szkript hozzáadva több függőséggel rendelkező modul importálásához

**Írja be a következőt:** Új funkció

A szkript a [GitHub-adattárból](https://github.com/azureautomation/runbooks/blob/master/Utility/Python/import_py3package_from_pypi.py)tölthető le. 
 
### <a name="hybrid-runbook-worker-role-support-for-centos-8xrhel-8xsles-15"></a>Hibrid Runbook-feldolgozói szerepkör támogatása CentOS 8. x/RHEL 8. x/SLES 15

**Típusa.** Új funkció

A hibrid Runbook Worker szolgáltatás a CentOS 8. x, a REHL 8. x és a SLES 15 disztribúciót támogatja a hibrid Runbook-feldolgozók esetében csak a folyamatok automatizálásához.  A módosítások megjelenítéséhez tekintse meg a [támogatott operációs rendszerek](automation-linux-hrw-install.md#supported-linux-operating-systems) a dokumentáció frissítéseihez című témakört.

### <a name="update-management--change-tracking-availability-in-australia-east-east-asia-west-us--central-us-regions"></a>Update Management & Change Tracking rendelkezésre állása Kelet-Ausztrália, Kelet-Ázsia, USA nyugati régiója & Közép-amerikai régióban

**Írja be a következőt:** Új funkció

Az Automation-fiók, a Change Tracking és a leltár, valamint a Update Management a Kelet-Ausztrália, Kelet-Ázsia, az USA nyugati régiója & középső régiójában érhető el. 

### <a name="introduced-public-preview-of-python-3-runbooks-in-us-government-cloud"></a>Megjelent a Python 3 runbookok nyilvános előzetese az USA kormányzati felhőben

**Írja be a következőt:** Az új funkció Azure Automation a Python 3 felhő és a hibrid runbook-végrehajtás nyilvános előzetes támogatását mutatja be az USA kormányzati felhő régióiban.  További információkért lásd a [bejelentést](https://azure.microsoft.com/updates/azure-automation-python-3-public-preview/).

### <a name="azure-automation-runbooks-moved-from-technet-script-center-to-github"></a>Azure Automation runbookok a TechNet script Centerből a GitHubra költözött

**Írja be a következőt:** Tervezze meg a változást

A TechNet Script Center kivonásra kerül, és az Runbook-katalógusban üzemeltetett összes runbookok át lett helyezve az [Automation GitHub-szervezetbe](https://github.com/azureautomation). További információkért olvassa el [Azure Automation runbookok a githubra való áthelyezését](https://techcommunity.microsoft.com/t5/azure-governance-and-management/azure-automation-runbooks-moving-to-github/ba-p/2039337)ismertető témakört.

## <a name="december-2020"></a>2020. december

### <a name="azure-automation-and-update-management-private-link-ga"></a>Azure Automation és Update Management Private link GA

**Írja be a következőt:** Új funkció

Azure Automation és Update Management támogatja az Azure globális és kormányzati felhőkhöz készült GA-t. Azure Automation engedélyezte a privát kapcsolatok támogatását a runbook egy hibrid feldolgozói szerepkörön történő biztonságossá tételéhez, Update Management használatával a runbook egy webhookon keresztül, és az állapot-konfigurációs szolgáltatás használatával megtarthatja a gépekkel kapcsolatos panaszait. További információért olvassa el [Azure Automation privát hivatkozás támogatása](https://azure.microsoft.com/updates/azure-automation-private-link) című témakört.

### <a name="azure-automation-classified-as-grade-c-certified-on-accessibility"></a>A kisegítő lehetőségeknek minősülő C minősítéssel rendelkező Azure Automation

**Írja be a következőt:** Új funkció

A Microsoft-termékek kisegítő lehetőségei a globális kisegítő lehetőségekkel foglalkoznak. A [blogbejegyzés](https://cloudblogs.microsoft.com/industry-blog/government/2018/09/11/accessibility-conformance-reports/) lapon keressen rá **Azure Automationre** , és olvassa el az Automation szolgáltatáshoz tartozó hozzáférhetőségi megfelelőségi jelentést.

### <a name="support-for-automation-and-state-configuration-ga-in-uae-north"></a>Az Automation és az állapot konfigurációjának támogatása az Észak-Egyesült Államokban

**Írja be a következőt:** Új funkció

Az Automation-fiók és az állapot-konfiguráció rendelkezésre állása az Egyesült Arab Emírségek északi régiójában. További információért olvassa el a [bejelentést](https://azure.microsoft.com/updates/azure-automation-in-uae-north-region/).

### <a name="support-for-automation-and-state-configuration-ga-in-germany-west-central"></a>Az Automation és az állapot konfigurációjának támogatása a Középnyugat-Németország-ban

**Írja be a következőt:** Új funkció

Az Automation-fiók és az állapot-konfiguráció elérhetősége Németország nyugati régiójában. További információért olvassa el a [bejelentést](https://azure.microsoft.com/updates/azure-automation-in-germany-west-central-region/).

### <a name="dsc-support-for-oracle-6-and-7"></a>DSC-támogatás az Oracle 6 és 7 rendszerhez

**Írja be a következőt:** Új funkció

Oracle Linux 6 és 7 rendszerű gépek kezelése Automation állapot-konfigurációval. A módosítások megjelenítéséhez tekintse meg a [támogatott linuxos disztribúciókat](https://github.com/Azure/azure-linux-extensions/tree/master/DSC#4-supported-linux-distributions) a dokumentáció frissítéseihez.

### <a name="public-preview-for-python3-runbooks-in-automation"></a>A Python3 runbookok nyilvános előzetes verziója az Automationben

**Írja be a következőt:** Új funkció

A Azure Automation mostantól támogatja a Python 3 Cloud & hibrid runbook-megvalósítást nyilvános előzetes verzióban az Azure globális felhő összes régiójában. Tekintse meg a [közleményt] ( https://azure.microsoft.com/updates/azure-automation-python-3-public-preview/) További részletekért.

## <a name="november-2020"></a>2020. november

### <a name="dsc-support-for-ubuntu-1804"></a>DSC-támogatás Ubuntu 18,04

**Írja be a következőt:** Új funkció

Tekintse át a [támogatott Linux-disztribúciókat](https://github.com/Azure/azure-linux-extensions/tree/master/DSC#4-supported-linux-distributions) a jelen módosításokat tükröző dokumentáció frissítéseihez.

## <a name="october-2020"></a>2020. október

### <a name="support-for-automation-and-state-configuration-ga-in-switzerland-north"></a>Az Automation és az állapot konfigurációjának támogatása a Észak-Svájc-ban

**Írja be a következőt:** Új funkció

Az Automation-fiók és az állapot konfigurációjának rendelkezésre állása Észak-Svájc. További információért olvassa el a [bejelentést](https://azure.microsoft.com/updates/azure-automation-in-switzerland-north-region/).

### <a name="support-for-automation-and-state-configuration-ga-in-brazil-south-east"></a>Támogatás az automatizáláshoz és az állapot-konfigurációhoz a Dél-brazíliai Kelet-Brazíliában

**Írja be a következőt:** Új funkció

Az Automation-fiók és az állapot-konfiguráció elérhetősége Brazíliában, Dél-Keleten. További információért olvassa el a [bejelentést](https://azure.microsoft.com/updates/azure-automation-in-brazil-southeast-region/).

### <a name="update-management-availability-in-south-central-us"></a>Update Management rendelkezésre állás az USA déli középső régiójában

**Írja be a következőt:** Új funkció

A Azure Automation régió-hozzárendelés frissítve lett, hogy támogassa Update Management funkciót az USA déli középső régiójában. A módosítás megjelenítéséhez tekintse meg a [támogatott régiók leképezése](how-to/region-mappings.md#supported-mappings) a dokumentáció frissítéseihez című témakört.

## <a name="september-2020"></a>2020. szeptember

### <a name="startstop-vms-during-off-hours-runbooks-updated-to-use-azure-az-modules"></a>Start/Stop VMs during off-hours runbookok frissítve az Azure az modulok használatára

**Írja be a következőt:** Új funkció

A virtuálisgép-runbookok elindítása/leállítása a Azure Resource Manager modulok helyett az az modulok használatára lett frissítve. A változtatások megjelenítéséhez tekintse meg a dokumentáció frissítéseinek [Start/Stop VMS During off-hours](automation-solution-vm-management.md) áttekintése című témakört.

## <a name="august-2020"></a>2020. augusztus

### <a name="published-the-dsc-extension-to-support-azure-arc"></a>Közzétette a DSC-bővítményt az Azure arc támogatásához

**Írja be a következőt:** Új funkció

Azure Automation állapot konfigurációjának használatával központilag tárolhatja a konfigurációkat, és megtarthatja az Azure arc-kompatibilis kiszolgálók DSC virtuálisgép-bővítményével engedélyezett hibrid csatlakoztatott gépek kívánt állapotát. További információért olvassa el az [arc-kompatibilis kiszolgálók virtuálisgép-bővítmények áttekintése](../azure-arc/servers/manage-vm-extensions.md)című témakört.

### <a name="july-2020"></a>2020. július

### <a name="introduced-public-preview-of-private-link-support-in-automation"></a>Nyilvános előzetes verzióban megjelent a privát hivatkozások támogatása az Automationben

**Írja be a következőt:** Új funkció

Az Azure Private link használatával biztonságosan csatlakoztathatja a virtuális hálózatokat a Azure Automation magánhálózati végpontok használatával. További információért olvassa el a [bejelentést](https://azure.microsoft.com/updates/public-preview-private-link-azure-automation-is-now-available/).

### <a name="hybrid-runbook-worker-support-for-windows-server-2008-r2"></a>Hibrid Runbook-feldolgozói támogatás a Windows Server 2008 R2 rendszerhez

**Írja be a következőt:** Új funkció

Az Automation Hybrid Runbook Worker támogatja a Windows Server 2008 R2 operációs rendszert. A módosítások megjelenítéséhez tekintse meg a [támogatott operációs rendszerek](automation-windows-hrw-install.md#supported-windows-operating-system) a dokumentáció frissítéseihez című témakört.

### <a name="update-management-support-for-windows-server-2008-r2"></a>Update Management a Windows Server 2008 R2 támogatása

**Írja be a következőt:** Új funkció

Update Management támogatja a Windows Server 2008 R2 operációs rendszer értékelését és javítását. A módosítások megjelenítéséhez tekintse meg a [támogatott operációs rendszerek](update-management/overview.md#clients) a dokumentáció frissítéseihez című témakört.

### <a name="automation-diagnostic-logs-schema-update"></a>Automation diagnosztikai naplók sémájának frissítése

**Írja be a következőt:** Új funkció

Módosította Azure Automation naplózási adatkészletének sémáját a Log Analytics szolgáltatásban. További információ: [Azure Automation feladatainak továbbítása Azure monitor naplókhoz](automation-manage-send-joblogs-log-analytics.md#filter-job-status-output-converted-into-a-json-object).

### <a name="azure-lighthouse-supports-automation-update-management"></a>Az Azure Lighthouse támogatja az Automation Update Management

**Írja be a következőt:** Új funkció

Az Azure Lighthouse lehetővé teszi a delegált erőforrás-kezelést Update Management a szolgáltatók és az ügyfelek számára. További tudnivalók [itt](https://azure.microsoft.com/blog/how-azure-lighthouse-enables-management-at-scale-for-service-providers/).

## <a name="june-2020"></a>2020. június

### <a name="automation-and-update-management-availability-in-the-us-gov-arizona-region"></a>Automatizálás és Update Management rendelkezésre állás a US Gov Arizona régióban

**Írja be a következőt:** Új funkció

Az Automation-fiók és a Update Management US Gov Arizona érhető el. További információ: [közlemény](https://azure.microsoft.com/updates/azure-automation-generally-available-in-usgov-arizona-region/).

### <a name="hybrid-runbook-worker-onboarding-script-updated-to-use-az-modules"></a>A hibrid Runbook Worker bevezetési parancsfájl frissítése az az modulok használatára

**Írja be a következőt:** Új funkció

A New-OnPremiseHybridWorker runbook frissítve lett az az modulok támogatásához. További információ: csomag a [PowerShell-galériaban](https://www.powershellgallery.com/packages/New-OnPremiseHybridWorker/1.7).

### <a name="update-management-availability-in-china-east-2"></a>A Update Management rendelkezésre állása Kelet-Kína 2

**Írja be a következőt:** Új funkció

A Azure Automation régió-hozzárendelés frissítve lett, hogy támogassa Update Management funkciót Kelet-Kína 2 régióban. A módosítás megjelenítéséhez tekintse meg a [támogatott régiók leképezése](how-to/region-mappings.md#supported-mappings) a dokumentáció frissítéseihez című témakört.

## <a name="may-2020"></a>2020. május

### <a name="updated-automation-service-dns-records-from-region-specific-to-automation-account-specific-urls"></a>Az Automation szolgáltatás DNS-rekordjainak frissítése az Automation-fiókra jellemző URL-címek alapján

**Írja be a következőt:** Új funkció

Azure Automation DNS-rekordok frissültek a privát hivatkozások támogatásához. További információért olvassa el a [bejelentést](https://azure.microsoft.com/updates/azure-automation-updateddns-records/).

### <a name="added-capability-to-keep-automation-runbooks--dsc-scripts-encrypted-by-default"></a>Hozzáadott képesség az Automation runbookok & a DSC-parancsfájlok alapértelmezés szerint titkosítva

**Írja be a következőt:** Új funkció

Az eszközök biztonságának javítása mellett a runbookok & DSC-parancsfájlok is titkosítva vannak a Azure Automation biztonság növelése érdekében.

## <a name="april-2020"></a>2020. április

### <a name="retirement-of-the-automation-watcher-task"></a>Az Automation Watcher feladat kivonása

**Írja be a következőt:** Tervezze meg a változást

A Azure Logic Apps mostantól a javasolt és támogatott módon figyeli az eseményeket, ütemezhet ismétlődő feladatokat és aktiválhat műveleteket. A figyelő feladat funkciói nem lesznek további beruházások. További információ: [ismétlődő automatizált feladatok ütemezett és futtatása a Logic apps](../logic-apps/concepts-schedule-automated-recurring-tasks-workflows.md)használatával.

## <a name="march-2020"></a>2020. március

### <a name="support-for-impact-level-5-il5-compute-isolation-in-azure-commercial-and-government-cloud"></a>Az 5. szint (IL5) számítási elkülönítésének támogatása az Azure kereskedelmi és kormányzati felhőben

**Típusa**

Azure Automation Hybrid Runbook Worker Azure Government használható az 5. szintű munkaterhelések támogatásához. További információért tekintse meg a [dokumentációt](automation-hybrid-runbook-worker.md#support-for-impact-level-5-il5).

## <a name="february-2020"></a>2020. február

### <a name="introduced-support-for-azure-virtual-network-service-tags"></a>Bevezetett támogatás az Azure Virtual Network Service-címkékhez

**Írja be a következőt:** Új funkció

A szolgáltatási címkék automatizálási támogatása a forgatókönyvek egy részhalmaza számára engedélyezi vagy megtagadja az Automation szolgáltatás forgalmát. További információt a [dokumentációban](automation-hybrid-runbook-worker.md#service-tags)talál.

### <a name="enable-tls-12-support-for-azure-automation-service"></a>A TLS 1,2-támogatás engedélyezése Azure Automation szolgáltatáshoz

**Írja be a következőt:** Tervezze meg a változást

A Azure Automation teljes mértékben támogatja a TLS 1,2-et és az összes ügyfél-hívást (webhookok, DSC-csomópontok és hibrid feldolgozók). A TLS 1,1 és a TLS 1,0 továbbra is támogatott a régebbi ügyfelekkel való visszamenőleges kompatibilitás érdekében, amíg az ügyfelek nem szabványosítják és teljes mértékben áttelepítik a 1,2 TLS-t

## <a name="january-2020"></a>2020. január

### <a name="introduced-public-preview-of-customer-managed-keys-for-azure-automation"></a>Az ügyfél által felügyelt kulcsok nyilvános előzetes verziójának bevezetése Azure Automation

**Írja be a következőt:** Új funkció

Az ügyfelek a saját felügyelt kulcsaikat használva kezelhetik és biztonságossá tehetik Azure Automation-eszközök titkosítását. További információ: [az ügyfél által felügyelt kulcsok használata](automation-secure-asset-encryption.md#use-of-customer-managed-keys-for-an-automation-account).

### <a name="retirement-of-azure-service-management-asm-rest-apis-for-azure-automation"></a>Azure Service Management-(ASM-) REST API-k kivonása Azure Automation

**Írja be a következőt:** Nyugdíjba

Az Azure Service Management (ASM) REST API-k a Azure Automation kivonásra kerülnek, és a továbbiakban nem támogatottak a 2020. január 30-ig. További információért lásd a [bejelentést](https://azure.microsoft.com/updates/azure-automation-service-management-rest-apis-are-being-retired-april-30-2019/).

## <a name="next-steps"></a>Következő lépések

Ha szeretne hozzájárulni Azure Automation dokumentációhoz, tekintse meg a [docs közreműködői útmutatóját](/contribute/).
