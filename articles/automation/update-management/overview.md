---
title: Azure Automation Update Management áttekintése
description: Ez a cikk áttekintést nyújt a Windows és Linux rendszerű gépek frissítéseinek megvalósítására szolgáló Update Management szolgáltatásról.
services: automation
ms.subservice: update-management
ms.date: 03/08/2021
ms.topic: conceptual
ms.openlocfilehash: 0a79be9d879e9ccb7ae4583d0674cf2bb23aafa4
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102485673"
---
# <a name="update-management-overview"></a>Az Update Management áttekintése

Az Azure-ban, a helyszíni környezetekben és más felhőalapú környezetekben lévő Windows-és Linux-alapú virtuális gépek operációsrendszer-frissítéseinek kezeléséhez Azure Automation Update Management is használhatja. Gyorsan felbecsülheti az összes ügynökön elérhető frissítések állapotát, és kezelheti a kiszolgálók szükséges frissítéseinek telepítésének folyamatát.

> [!NOTE]
> A Update Management konfigurált gépek nem használhatók a Azure Automation egyéni parancsfájljainak futtatásához. Ez a számítógép csak a Microsoft által aláírt frissítési parancsfájlt futtathatja.

> [!NOTE]
> Jelenleg a Update Management engedélyezése közvetlenül egy ív-kompatibilis kiszolgálóról nem támogatott. A követelmények megismeréséhez és a kiszolgáló engedélyezéséhez lásd: [Update Management engedélyezése az Automation-fiókból](../../automation/update-management/enable-from-automation-account.md) .

Ha az Azure-beli virtuális gépen automatikusan le szeretné tölteni és telepíteni kívánja a *kritikus* és *biztonsági* javításokat, tekintse át a Windows rendszerű virtuális gépekhez készült automatikus virtuálisgép- [javítás](../../virtual-machines/windows/automatic-vm-guest-patching.md)

A Update Management üzembe helyezése és a gépek felügyelethez való engedélyezése előtt győződjön meg arról, hogy az alábbi részben található információk ismerete szükséges.  

## <a name="about-update-management"></a>Tudnivalók Update Management

Az Update Management által felügyelt gépek a következők alapján értékelik az értékelést és telepítik a frissítéseket:

* Windows vagy Linux rendszerhez készült [log Analytics ügynök](../../azure-monitor/agents/log-analytics-agent.md)
* PowerShell-célállapotkonfiguráció (DSC) Linux rendszerre
* Automation Hybrid Runbook Worker (automatikusan települ, amikor engedélyezi a Update Management a gépen)
* Microsoft Update vagy [Windows Server Update Services](/windows-server/administration/windows-server-update-services/get-started/windows-server-update-services-wsus) (WSUS) Windows rendszerű gépekhez
* A Linux rendszerű gépek magán-vagy nyilvános frissítési tárháza

Az alábbi ábra azt szemlélteti, hogy a Update Management hogyan vizsgálja és alkalmazza a biztonsági frissítéseket a munkaterület összes csatlakoztatott Windows Server-és Linux-kiszolgálójára vonatkozóan:

![Update Management munkafolyamat](./media/overview/update-mgmt-updateworkflow.png)

A Update Management használatával natív módon telepíthetők a több előfizetésben lévő gépekre ugyanabban a bérlőben.

A csomag felszabadítása után 2 – 3 órát vesz igénybe, hogy a javítás megjelenjen a Linux rendszerű gépek értékeléséhez. A Windows rendszerű gépek esetében 12 – 15 órát vesz igénybe, hogy a javítás megjelenjen az értékelés után. Amikor egy gép befejezi a frissítések megfelelőségi vizsgálatát, az ügynök tömegesen továbbítja az adatokat Azure Monitor naplókhoz. Windows rendszerű gépen a megfelelőségi vizsgálat alapértelmezés szerint 12 óránként fut. Linux rendszerű gépek esetén a megfelelőségi vizsgálat alapértelmezés szerint óránként történik. Ha a Log Analytics ügynök újraindítása megtörténik, a rendszer 15 percen belül elindít egy megfelelőségi vizsgálatot.

A vizsgálati ütemterven kívül a frissítés megfelelőségének vizsgálata a Log Analytics ügynök újraindítása után 15 percen belül elindul, a frissítés telepítése előtt és a frissítés telepítése után.

Update Management a jelentést arról, hogy a gép milyen naprakészen van, hogy milyen forrásra van konfigurálva a szinkronizáláshoz. Ha a Windows rendszerű gép úgy van konfigurálva, hogy [Windows Server Update Services](/windows-server/administration/windows-server-update-services/get-started/windows-server-update-services-wsus) (WSUS) jelentésre legyen állítva, attól függően, hogy mikor szinkronizálta a WSUS-t a Microsoft Update, az eredmények eltérhetnek a Microsoft Update láthatótól. Ez a viselkedés ugyanaz, mint a Linux rendszerű gépeken, amelyek úgy vannak konfigurálva, hogy nyilvános tárház helyett helyi tárházba jelentsenek.

> [!NOTE]
> Ahhoz, hogy a szolgáltatás megfelelően jelentsen, Update Management szükség van bizonyos URL-címekre és portokra. Ha többet szeretne megtudni ezekről a követelményekről, tekintse meg a [hálózati konfiguráció](../automation-hybrid-runbook-worker.md#network-planning)című témakört.

A frissítéseket egy ütemezett telepítés létrehozásával telepítheti és telepítheti a frissítésekhez szükséges számítógépeken. A választhatóként besorolt frissítések nem szerepelnek a Windows rendszerű gépek központi telepítési hatókörében. A központi telepítési hatókörben csak a szükséges frissítések szerepelnek.

Az ütemezett telepítés meghatározza, hogy mely célszámítógépek kapják meg a megfelelő frissítéseket. Ezt az egyes gépek explicit módon történő megadásával, vagy egy olyan [számítógépcsoport](../../azure-monitor/logs/computer-groups.md) kiválasztásával hajtja végre, amely egy adott gép (vagy egy olyan [Azure-lekérdezésen](query-logs.md) alapul, amely dinamikusan kiválasztja az Azure-beli virtuális gépeket a megadott feltételek alapján). Ezek a csoportok eltérnek a [hatókör-konfigurációtól](../../azure-monitor/insights/solution-targeting.md), amellyel szabályozható a Update Management engedélyezése a konfigurációt fogadó gépek célzása. Ezzel megakadályozhatja, hogy a frissítés megfelelőségét és a jóváhagyott szükséges frissítéseket telepítse.

A központi telepítés meghatározásakor meg kell adnia egy ütemtervet is a jóváhagyáshoz, és beállíthatja azt az időszakot, amely alatt a frissítések telepíthetők. Ezt az időtartamot karbantartási időszaknak nevezzük. A karbantartási időszak 20 perces időszaka újraindítások számára van fenntartva, feltételezve, hogy az egyik szükséges, és a megfelelő újraindítási beállítást választotta. Ha a javítás a vártnál hosszabb időt vesz igénybe, és a karbantartási időszakban kevesebb mint 20 perc van, a rendszer újraindítást hajt végre.

A telepítést az Azure Automation runbookjai végzik. Ezeket a runbookok nem lehet megtekinteni, és nem igényelnek semmilyen konfigurálást. Egy frissítés központi telepítésének létrehozásakor egy ütemezést hoz létre, amely a főfrissítési runbook a befoglalt gépekhez megadott időpontban indítja el. A fő runbook elindít egy gyermek runbook az egyes ügynökökön a szükséges frissítések telepítéséhez.

A központi telepítésben megadott napon és időpontban a célszámítógépeken párhuzamosan hajtják végre a telepítést. A telepítés előtt a rendszer ellenőrzi, hogy a frissítések továbbra is szükségesek-e. WSUS-ügyfélszámítógépek esetén, ha a frissítések nem lettek jóváhagyva a WSUS-ben, a frissítés telepítése sikertelen lesz.

Ha a Update Management több Log Analytics munkaterületen (más néven többhelyű) van regisztrálva a gépen, nem támogatott.

## <a name="clients"></a>Ügyfelek

### <a name="supported-operating-systems"></a>Támogatott operációs rendszerek

A következő táblázat felsorolja a frissítési felmérések és a javítások támogatott operációs rendszereit. A javításhoz a System Hybrid Runbook Worker szükséges, amely automatikusan települ, amikor engedélyezi a virtuális gépet vagy a kiszolgálót Update Management általi felügyeletre. A hibrid Runbook-feldolgozói rendszerkövetelményekkel kapcsolatos információkért lásd: [Windows Hybrid Runbook Worker](../automation-windows-hrw-install.md) üzembe helyezése és [Linux Hybrid Runbook Worker üzembe helyezése](../automation-linux-hrw-install.md).

> [!NOTE]
> A Linux rendszerű gépek frissítési felmérése csak bizonyos régiókban támogatott, az Automation-fiók és a Log Analytics munkaterület- [hozzárendelések táblázatban](../how-to/region-mappings.md#supported-mappings)láthatóak szerint.

|Operációs rendszer  |Jegyzetek  |
|---------|---------|
|Windows Server 2019 (Datacenter/standard, beleértve a Server Core-t)<br><br>Windows Server 2016 (Datacenter/standard, a Server Core kivételével)<br><br>Windows Server 2012 R2 (Datacenter/standard)<br><br>Windows Server 2012 | |
|Windows Server 2008 R2 (RTM és SP1 standard)| Update Management támogatja az operációs rendszer értékeléseit és javításait. A [hibrid Runbook-feldolgozót](../automation-windows-hrw-install.md) a Windows Server 2008 R2 támogatja. |
|CentOS 6 és 7 (x64)      | A Linux-ügynököknek hozzáférésre van szükségük egy frissítési tárházhoz. A besoroláson alapuló javításokhoz olyan `yum` biztonsági adatforrásokat kell visszaadnia, amelyeket a CentOS nem tartalmaz a RTM kiadásokban. A CentOS besoroláson alapuló javításával kapcsolatos további információkért lásd: [frissítési besorolások Linux](view-update-assessments.md#linux)rendszeren.          |
|Red Hat Enterprise 6 és 7 (x64)     | A Linux-ügynököknek hozzáférésre van szükségük egy frissítési tárházhoz.        |
|SUSE Linux Enterprise Server 12, 15 és 15,1 (x64)     | A Linux-ügynököknek hozzáférésre van szükségük egy frissítési tárházhoz. A SUSE 15. x esetében a Python 3 szükséges a gépen.      |
|Ubuntu 14,04 LTS, 16,04 LTS és 18,04 LTS (x64)      |A Linux-ügynököknek hozzáférésre van szükségük egy frissítési tárházhoz.         |

> [!NOTE]
> A Update Management nem támogatja az Azure virtuálisgép-méretezési csoport összes példányának biztonságos automatizálását. Az operációs rendszer rendszerképének [automatikus frissítése](../../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md) a méretezési csoporton az operációsrendszer-képek frissítésének ajánlott módja.

### <a name="unsupported-operating-systems"></a>Nem támogatott operációs rendszerek

A következő táblázat a Update Management által nem támogatott operációs rendszereket sorolja fel:

|Operációs rendszer  |Jegyzetek  |
|---------|---------|
|Windows-ügyfél     | Az ügyféloldali operációs rendszerek (például a Windows 7 és a Windows 10) nem támogatottak.<br> Az Azure Windows Virtual Desktop (WVD) esetében ajánlott módszer<br> a frissítések kezeléséhez a [Microsoft Endpoint Configuration Manager](../../virtual-desktop/configure-automatic-updates.md) a Windows 10-es ügyfélszámítógépek javításának kezeléséhez. |
|Windows Server 2016 Nano Server     | Nem támogatott.       |
|Azure Kubernetes szolgáltatási csomópontok | Nem támogatott. Használja a [biztonsági és kernel-frissítések alkalmazása Linux-csomópontokra az Azure Kubernetes szolgáltatásban (ak)](../../aks/node-updates-kured.md) című témakörben ismertetett javítási folyamatot|

### <a name="system-requirements"></a>System requirements (Rendszerkövetelmények)

Az alábbi információk az operációs rendszerre vonatkozó követelményeket ismertetik. További útmutatásért lásd: [hálózati tervezés](#ports). A TLS 1,2 követelményeinek megismeréséhez lásd: [tls 1,2 kényszerítés Azure Automation](../automation-managing-data.md#tls-12-enforcement-for-azure-automation).

#### <a name="windows"></a>Windows

A szoftverre vonatkozó követelmények:

- A .NET-keretrendszer 4,6-es vagy újabb verziójára van szükség. ([Töltse le a .NET-keretrendszert](/dotnet/framework/install/guide-for-developers).
- Windows PowerShell 5,1 szükséges (a[Windows Management Framework 5,1 letöltése](https://www.microsoft.com/download/details.aspx?id=54616))

A Windows-ügynököket úgy kell konfigurálni, hogy a WSUS-kiszolgálóval kommunikáljanak, vagy hozzáférést igényelnek a Microsoft Updatehoz. Hibrid gépek esetében javasoljuk, hogy Log Analytics a Windows-ügynököt az [Azure arc-kompatibilis kiszolgálókhoz](../../azure-arc/servers/overview.md)csatlakoztassa, majd a Azure Policy használatával rendelje hozzá a log Analytics- [ügynököt a Windows Azure arc-gépek](../../governance/policy/samples/built-in-policies.md#monitoring) beépített házirendjéhez. Ha a gépeket Azure Monitor for VMs használatával kívánja figyelni, Ehelyett használja a [Azure monitor for VMS engedélyezése](../../governance/policy/samples/built-in-initiatives.md#monitoring) kezdeményezést.

A Update Management a Microsoft Endpoint Configuration Manager használatával végezheti el. Az integrációs forgatókönyvekkel kapcsolatos további tudnivalókért lásd: [a Update Management integrálása a Windows Endpoint Configuration Manager](mecmintegration.md)használatával. A Windows rendszerhez készült [log Analytics ügynök](../../azure-monitor/agents/agent-windows.md) szükséges a Configuration Manager-környezetben található helyek által felügyelt Windows-kiszolgálókhoz.

Alapértelmezés szerint az Azure Marketplace-ről üzembe helyezett Windows-alapú virtuális gépek a Windows Update szolgáltatásból származó automatikus frissítések fogadására vannak beállítva. Ez a viselkedés nem változik, ha Windows rendszerű virtuális gépeket ad hozzá a munkaterülethez. Ha nem kezeli aktívan a frissítéseket Update Management használatával, a rendszer az alapértelmezett viselkedést alkalmazza (a frissítések automatikus érvénybe lépéséhez).

> [!NOTE]
> Módosíthatja Csoportházirend úgy, hogy a számítógép újraindítását csak a felhasználó hajtsa végre, nem pedig a rendszer. A felügyelt gépek elakadnak abban az esetben, ha Update Management nem rendelkezik jogosultsággal a gép újraindításához a felhasználó manuális beavatkozása nélkül. További információ: [csoportházirend beállítások konfigurálása az automatikus frissítésekhez](/windows-server/administration/windows-server-update-services/deploy/4-configure-group-policy-settings-for-automatic-updates).

#### <a name="linux"></a>Linux

A szoftverre vonatkozó követelmények:

- A gépnek privát vagy nyilvános frissítési tárházhoz kell hozzáférnie.
- A Update Managementhoz való interakcióhoz TLS 1,1 vagy TLS 1,2 szükséges.
- A Python 2. x telepítve van.

> [!NOTE]
> A Linux rendszerű gépek frissítési felmérése csak bizonyos régiókban támogatott. Tekintse meg az Automation-fiók és a Log Analytics munkaterület- [hozzárendelések táblát](../how-to/region-mappings.md#supported-mappings).

Hibrid gépek esetében javasoljuk, hogy a Log Analytics-ügynök Linux rendszerre való telepítését először csatlakoztassa a gépet az [Azure arc-kompatibilis kiszolgálókhoz](../../azure-arc/servers/overview.md), majd a Azure Policy használatával rendelje hozzá a [log Analytics-ügynök üzembe helyezését a Linux Azure arc-gépek](../../governance/policy/samples/built-in-policies.md#monitoring) beépített házirendjéhez. Ha a gépeket Azure Monitor for VMs használatával kívánja figyelni, Ehelyett használja a [Azure monitor for VMS engedélyezése](../../governance/policy/samples/built-in-initiatives.md#monitoring) kezdeményezést.

Az Azure Marketplace-en elérhető, igény szerinti Red Hat Enterprise Linux (RHEL) lemezképből létrehozott virtuális gépek regisztrálva vannak az Azure-ban üzembe helyezett [Red Hat frissítési infrastruktúrához (RHUI)](../../virtual-machines/workloads/redhat/redhat-rhui.md) . Minden más Linux-disztribúciót a terjesztés által támogatott módszerek használatával kell frissíteni a disztribúció online fájl-tárházában.

## <a name="permissions"></a>Engedélyek

A frissítési központi telepítések létrehozásához és kezeléséhez konkrét engedélyekre van szükség. További információ ezekről az engedélyekről: [szerepköralapú hozzáférés – Update Management](../automation-role-based-access-control.md#update-management-permissions).

## <a name="update-management-components"></a>Összetevők Update Management

Update Management az ebben a szakaszban ismertetett erőforrásokat használja. Ezeket az erőforrásokat a rendszer automatikusan hozzáadja az Automation-fiókhoz, amikor engedélyezi a Update Management.

### <a name="hybrid-runbook-worker-groups"></a>Hibrid Runbook Worker-csoportok

A Update Management engedélyezése után minden olyan Windows-gép, amely közvetlenül csatlakozik a Log Analytics munkaterülethez, automatikusan rendszer-hibrid Runbook-feldolgozóként van konfigurálva a Update Management támogató runbookok támogatásához.

Az Update Management által felügyelt összes Windows-gép a hibrid munkavégző csoportok ablaktáblán, az Automation-fiókhoz tartozó rendszer hibrid feldolgozói csoportként jelenik meg. A csoportok az `Hostname FQDN_GUID` elnevezési konvenciót használják. Ezek a csoportok nem állíthatók be a runbookok a fiókjában. Ha próbálkozik, a kísérlet sikertelen lesz. Ezek a csoportok kizárólag a Update Management támogatására szolgálnak. A hibrid Runbook-feldolgozóként konfigurált Windows-gépek listájának megtekintéséhez tekintse meg a [hibrid Runbook-feldolgozók megtekintése](../automation-hybrid-runbook-worker.md#view-system-hybrid-runbook-workers)című témakört.

A Windows rendszerű gépet hozzáadhatja az Automation-fiókjában lévő User Hybrid Runbook Worker csoporthoz az Automation-runbookok támogatásához, ha ugyanazt a fiókot használja a Update Management és a hibrid Runbook Worker Group tagsághoz. Ez a funkció a hibrid Runbook-feldolgozó verziójának 7.2.12024.0 lett hozzáadva.

### <a name="management-packs"></a>Felügyeleti csomagok

Ha a Operations Manager felügyeleti csoport [egy log Analytics munkaterülethez csatlakozik](../../azure-monitor/agents/om-agents.md), a következő felügyeleti csomagok lesznek telepítve a Operations Managerban. Ezek a felügyeleti csomagok a közvetlenül csatlakoztatott Windows rendszerű gépek Update Management is telepítve vannak. A felügyeleti csomagokat nem szükséges konfigurálni vagy felügyelni.

* Microsoft System Center Advisor Update Assessment Intelligence Pack (Microsoft.IntelligencePacks.UpdateAssessment)
* Microsoft.IntelligencePack.UpdateAssessment.Configuration (Microsoft.IntelligencePack.UpdateAssessment.Configuration)
* Frissítéstelepítő felügyeleti csomag

> [!NOTE]
> Ha van olyan Operations Manager 1807 vagy 2019 felügyeleti csoport, amely egy Log Analytics munkaterülethez van csatlakoztatva, és a felügyeleti csoportban konfigurált ügynökökkel gyűjti a naplózási adatokat, akkor felül kell bírálnia a paramétert, `IsAutoRegistrationEnabled` és igaz értékre kell állítania a **Microsoft.IntelligencePacks.AzureAutomation.HybridAgent.Init** szabályban.

A felügyeleti csomagok frissítéseivel kapcsolatos további információkért lásd: [Operations Manager Összekötése Azure monitor naplókhoz](../../azure-monitor/agents/om-agents.md).

> [!NOTE]
> Ahhoz, Update Management hogy a gépek teljes mértékben felügyelhetők legyenek a Log Analytics-ügynökkel, frissítenie kell a Log Analytics ügynökre a Windows rendszerhez vagy a Linux Log Analytics-ügynökéhez. Az ügynök frissítésével kapcsolatos további információkért lásd: [Operations Manager-ügynök frissítése](/system-center/scom/deploy-upgrade-agents). A Operations Managert használó környezetekben System Center Operations Manager 2012 R2-es vagy újabb verzióját kell futtatnia.

## <a name="data-collection"></a>Adatgyűjtés

### <a name="supported-sources"></a>Támogatott források

A következő táblázat ismerteti a Update Management által támogatott csatlakoztatott forrásokat:

| Csatlakoztatott forrás | Támogatott | Leírás |
| --- | --- | --- |
| Windows-ügynökök |Yes |Update Management adatokat gyűjt a Windows-ügynököktől a rendszerfrissítésekről, majd elindítja a szükséges frissítések telepítését. |
| Linux-ügynökök |Yes |Update Management adatokat gyűjt a Linux-ügynököktől a rendszerfrissítésekről, majd elindítja a szükséges frissítések telepítését a támogatott disztribúciók esetében. |
| Az Operations Manager felügyeleti csoportja |Yes |Update Management adatokat gyűjt a csatlakoztatott felügyeleti csoportban lévő ügynököktől származó rendszerfrissítésekről.<br/><br/>Nincs szükség közvetlen kapcsolódásra a Operations Manager ügynöktől a Azure Monitor naplókhoz. Az adatok továbbítása a felügyeleti csoportból a Log Analytics munkaterületre történik. |

### <a name="collection-frequency"></a>A gyűjtés gyakorisága

A Update Management a következő szabályok használatával vizsgálja a felügyelt gépeket az adatkezeléshez. 30 perc és 6 óra között eltarthat, amíg az irányítópult a felügyelt gépekről származó frissített adatok megjelenítésére is képes.

* Minden Windows-gép – Update Management naponta kétszer ellenőrzi az egyes gépeket.

* Minden linuxos gép – Update Management óránkénti vizsgálatot végez.

A Update Management-t használó gépek átlagos adatfelhasználása Azure Monitor-naplók havonta körülbelül 25 MB. Ez az érték csak egy közelítés, és változhat a környezettől függően. Javasoljuk, hogy figyelje a környezetét, hogy nyomon követhesse a pontos használatot. A Azure Monitor naplók adatfelhasználásának elemzésével kapcsolatos további információkért lásd: [a használat és a költséghatékonyság kezelése](../../azure-monitor/logs/manage-cost-storage.md).

## <a name="network-planning"></a><a name="ports"></a>Hálózattervezés

A Update Managementhoz szükséges portok, URL-címek és egyéb hálózatkezelési adatok részletes ismertetését [Azure Automation hálózati konfigurációban](../automation-network-configuration.md#hybrid-runbook-worker-and-state-configuration) találja.

Windows rendszerű gépek esetén a Windows Update által igényelt végpontokra is engedélyeznie kell a forgalmat. A szükséges végpontok frissített listáját a [http/proxy szolgáltatással kapcsolatos problémák](/windows/deployment/update/windows-update-troubleshooting#issues-related-to-httpproxy)között találja. Ha helyi [Windows Update-kiszolgálóval](/windows-server/administration/windows-server-update-services/plan/plan-your-wsus-deployment)rendelkezik, engedélyeznie kell a forgalmat a [WSUS-kulcsban](/windows/deployment/update/waas-wu-settings#configuring-automatic-updates-by-editing-the-registry)megadott kiszolgálóra is.

A Red Hat Linux rendszerű gépek esetében tekintse meg [az IP-címek a RHUI](../../virtual-machines/workloads/redhat/redhat-rhui.md#the-ips-for-the-rhui-content-delivery-servers) a szükséges végpontokhoz című témakört. Más Linux-disztribúciók esetében tekintse meg a szolgáltatói dokumentációt.

A hibrid Runbook-feldolgozók számára szükséges portokkal kapcsolatos további információkért lásd: [Update Management-címek a hibrid Runbook-feldolgozók](../automation-hybrid-runbook-worker.md#update-management-addresses-for-hybrid-runbook-worker)számára.

Ha az IT-biztonsági szabályzatok nem engedélyezik a hálózatban lévő gépek számára az internethez való kapcsolódást, beállíthat egy [log Analytics átjárót](../../azure-monitor/agents/gateway.md) , majd beállíthatja, hogy a számítógép az átjárón keresztül kapcsolódjon Azure Automation és Azure monitor.

## <a name="update-classifications"></a>Frissítési besorolások

A következő táblázat azokat a besorolásokat határozza meg, amelyeket Update Management támogat a Windows-frissítésekhez.

|Besorolás  |Leírás  |
|---------|---------|
|Kritikus frissítések     | Egy adott problémára vonatkozó frissítés, amely kritikus, nem biztonsággal kapcsolatos hibára vonatkozik.        |
|Biztonsági frissítések     | Egy termékre vonatkozó, biztonsággal kapcsolatos probléma frissítése.        |
|Kumulatív frissítések     | Az egyszerű üzembe helyezéshez együtt csomagolt gyorsjavítások összesített készlete.        |
|Funkciócsomagok     | A termék kiadásán kívül terjesztett új termék-szolgáltatások.        |
|Szervizcsomagok     | Egy alkalmazásra alkalmazott gyorsjavítások kumulatív készlete.        |
|Definíciófrissítések     | Vírus-vagy egyéb definíciós fájlok frissítése.        |
|Eszközök     | Olyan segédprogram vagy szolgáltatás, amely egy vagy több feladat elvégzését segíti elő.        |
|Frissítések     | Egy aktuálisan telepített alkalmazás vagy fájl frissítése.        |

A következő táblázat a Linux-frissítések támogatott besorolásait határozza meg.

|Besorolás  |Leírás  |
|---------|---------|
|Kritikus vagy biztonsági frissítések     | Adott probléma vagy termékspecifikus, biztonsággal kapcsolatos probléma frissítései.         |
|Egyéb frissítések     | Minden egyéb olyan frissítés, amely nem kritikus jellegű, vagy amelyek nem biztonsági frissítések.        |

>[!NOTE]
>A Linux rendszerű gépek frissítési besorolása csak a támogatott Azure-beli nyilvános Felhőbeli régiókban használható. A következő nemzeti Felhőbeli régiókban a Update Management használatakor nincs szükség Linux-frissítések besorolására:
>
>* Azure US Government
>* 21Vianet Kínában
>
> A besorolás helyett a frissítések a **többi frissítés** kategóriában jelennek meg.
>
> A Update Management a támogatott disztribúciók által közzétett, kifejezetten a kiadott [ovális](https://oval.mitre.org/) (biztonsági sebezhetőségi és értékelési nyelvi) fájlokat használja. Mivel az Internet-hozzáférés ezen országos felhőktől korlátozódik, Update Management nem fér hozzá a fájlokhoz.

A Linux rendszerben a Update Management a Felhőbeli adatgazdagítás miatt kiértékelheti a kritikus **frissítések és a** Felhőbeli biztonsági frissítések közötti különbséget.  A javításhoz a Update Management a gépen elérhető besorolási értékekre támaszkodik. A többi disztribúciótól eltérően a CentOS nem rendelkezik ezekkel az információkkal az RTM verziójában. Ha olyan CentOS-számítógépekkel rendelkezik, amelyek a következő parancs biztonsági értékének visszaadására vannak konfigurálva, a Update Management a besorolások alapján javíthatja a javítást.

```bash
sudo yum -q --security check-update
```

Jelenleg nem támogatott a natív besorolás engedélyezése – az adatelérhetőség a CentOS-ben. Jelenleg korlátozott támogatást biztosít azon ügyfelek számára, akik saját maguk engedélyezték ezt a szolgáltatást.

A Red Hat Enterprise 6-os verziójának frissítéseinek besorolásához telepítenie kell a yum-Security beépülő modult. Red Hat Enterprise Linux 7-én a beépülő modul már maga is a yum része, és semmit nem kell telepítenie. További információkért lásd a Red Hat [Tudásbázis következő cikkét](https://access.redhat.com/solutions/10021).

Ha egy Linux rendszerű gépen futtat egy frissítést, az például úgy van konfigurálva, hogy csak a **biztonsági** besorolásnak megfelelő frissítéseket telepítse, a telepített frissítések ettől eltérőek lehetnek, vagy a frissítések egy részhalmaza a besorolásnak. Ha a linuxos gépen függőben lévő operációsrendszer-frissítések értékelését végzi, a rendszer a Linux-disztribúciós gyártó által biztosított [biztonsági réseket és Assessment Language](https://oval.mitre.org/) (ovális) fájlokat használja Update Management besorolásra.

A kategorizálás a Linux-frissítések **biztonsági** vagy **egyéb** , az ovális fájlokon alapuló frissítéseihez készült, beleértve a biztonsági problémákkal vagy biztonsági résekkal foglalkozó frissítéseket is. A frissítési ütemterv futtatásakor azonban a megfelelő csomagkezelő, például a YUM, az APT vagy a ZYPPER használatával hajtja végre a telepítést a Linux gépen. A Linux-disztribúcióhoz tartozó csomagkezelő rendelkezhet egy másik módszerrel a frissítések besorolásához, ahol az eredmények eltérhetnek az OVÁLIS fájlokból beszerzett Update Management alapján. A számítógép kézi ellenőrzéséhez és a csomagkezelő által érintett frissítések megismeréséhez tekintse meg a [Linux-frissítések központi telepítésének hibáit](../troubleshoot/update-management.md#updates-linux-installed-different)ismertető témakört.

## <a name="integrate-update-management-with-configuration-manager"></a>Update Management integrálása Configuration Manager

A Microsoft Endpoint Configuration Managerban a számítógépek, kiszolgálók és mobileszközök kezeléséhez befektetett ügyfelek is a Configuration Manager erősségét és érettségét használják a szoftverfrissítések kezeléséhez. A Update Management és a Configuration Manager integrálásával kapcsolatban lásd: [a Update Management integrálása a Windows-végponttal Configuration Manager](mecmintegration.md).

## <a name="third-party-updates-on-windows"></a>Harmadik féltől származó frissítések Windows rendszeren

Update Management a helyileg konfigurált frissítési adattárra támaszkodik, hogy frissítse a támogatott Windows-rendszereket, akár a WSUS-t, akár a Windows Update. Az olyan eszközök, mint a [System Center Updates Publisher](/configmgr/sum/tools/updates-publisher) lehetővé teszik egyéni frissítések importálását és közzétételét a WSUS használatával. Ez a forgatókönyv lehetővé teszi, hogy Update Management frissítse a Configuration Managert használó gépeket a harmadik féltől származó szoftverrel rendelkező frissítési adattárként. Az Updates Publisher konfigurálásával kapcsolatos további információkért lásd: a [frissítések közzétevő telepítése](/configmgr/sum/tools/install-updates-publisher).

## <a name="enable-update-management"></a>Az Update Management engedélyezése

Az alábbi módokon engedélyezheti Update Management és kiválaszthatja a felügyelni kívánt gépeket:

- Az Azure [Resource Manager-sablonok](enable-from-template.md) használatával Update Management telepíthet egy új vagy meglévő Automation-fiókra, és Azure monitor log Analytics munkaterületet az előfizetésében. Nem konfigurálja a felügyelni kívánt gépek hatókörét, ez a sablon használata után külön lépésként történik.

- Az [Automation-fiókból](enable-from-automation-account.md) egy vagy több Azure-beli és nem Azure-beli gépen, beleértve az ív használatára képes kiszolgálókat is.

- Az **enable-AutomationSolution** [runbook](enable-from-runbook.md) metódus használata.

- Egy [kiválasztott Azure](enable-from-vm.md) -beli virtuális gép számára a Azure Portal **Virtual Machines** oldaláról. Ez a forgatókönyv Linux és Windows rendszerű virtuális gépek esetében érhető el.

- [Több Azure](enable-from-portal.md) -beli virtuális gép esetén válassza ki őket a Azure Portal **Virtual Machines** lapján.

> [!NOTE]
> A Update Management Log Analytics munkaterület összekapcsolását igényli az Automation-fiókhoz. A támogatott régiók végleges listáját az [Azure Workspace-hozzárendelések](../how-to/region-mappings.md)című témakörben tekintheti meg. A régió-hozzárendelések nem befolyásolják a virtuális gépek az Automation-fióktól eltérő régióban való felügyeletének képességét.

## <a name="next-steps"></a>Következő lépések

* A Update Management használatáról a [virtuális gépek frissítéseinek kezelése](manage-updates-for-vm.md)című témakörben olvashat bővebben.

* Tekintse át a [Azure Automation gyakori](../automation-faq.md)kérdéseivel Update Management kapcsolatos gyakori kérdéseket.
