---
title: Az Azure arc-kompatibilis kiszolgálók helyszíni üzembe helyezésének tervezése
description: Megtudhatja, hogyan engedélyezheti a nagy számú gépet az Azure arc használatára képes kiszolgálókon az Azure-ban az alapvető biztonsági, felügyeleti és monitorozási képességek konfigurálásának egyszerűbbé tételéhez.
ms.date: 02/23/2021
ms.topic: conceptual
ms.openlocfilehash: 0e77fc00f94f2f46c60bb2c5dcecc10a4e2e3bc5
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102032228"
---
# <a name="planing-for-an-at-scale-deployment-of-azure-arc-enabled-servers"></a>Az Azure arc-kompatibilis kiszolgálók helyszíni üzembe helyezésének megtervezése

Egy informatikai infrastruktúra-szolgáltatás vagy üzleti alkalmazás üzembe helyezése bármely vállalat számára kihívást jelenthet. Annak érdekében, hogy megfelelően fusson, és elkerülje az esetleges váratlan meglepetéseket és a nem tervezett költségeket, alaposan tervezze meg, hogy a lehető leghamarabb készen álljon. Az Azure arc-kompatibilis kiszolgálók méretezésének megtervezéséhez meg kell felelnie azokat a tervezési és telepítési feltételeket, amelyeket teljesíteni kell ahhoz, hogy sikeresen végre lehessen hajtani a feladatokat, hogy támogassa a nagy léptékű üzembe helyezést.

Ahhoz, hogy az üzembe helyezés zökkenőmentesen járjon el, a tervnek világosan meg kell ismernie a következőket:

* Szerepkörök és felelősségek.
* Fizikai kiszolgálók vagy virtuális gépek leltára annak ellenőrzéséhez, hogy megfelelnek-e a hálózati és rendszerkövetelményeknek.
* Az üzembe helyezés és a folyamatos felügyelet sikeres végrehajtásához szükséges szaktudás és képzés.
* Elfogadási feltételek és a siker nyomon követése.
* Az üzemelő példányok automatizálására szolgáló eszközök és módszerek.
* Azonosított kockázatok és kockázatcsökkentő tervek a késések, fennakadások stb. elkerüléséhez.
* Az üzembe helyezés során felmerülő fennakadások elkerülése.
* Mi a eszkalációs útvonal jelentős probléma esetén?

Ennek a cikknek a célja annak biztosítása, hogy készen áll az Azure arc-kompatibilis kiszolgálók sikeres üzembe helyezésére a környezetben található több éles fizikai kiszolgáló vagy virtuális gép között.

## <a name="prerequisites"></a>Előfeltételek

* A gépek [támogatott operációs rendszert](agent-overview.md#supported-operating-systems) futtatnak a csatlakoztatott gépi ügynökhöz.
* A gépek közvetlenül vagy egy proxykiszolgálón keresztül kapcsolódnak a helyszíni hálózatról vagy más felhőalapú környezetről az Azure-beli erőforrásokhoz.
* Az arc-kompatibilis kiszolgálók csatlakoztatott számítógép-ügynök, egy emelt szintű (azaz rendszergazda vagy gyökér) jogosultsággal rendelkező fiók telepítése és konfigurálása a gépeken.
* A gépek bevezetéséhez Ön az **Azure Connected Machine** bevezetési szerepkör tagja.
* Egy gép olvasásához, módosításához és törléséhez az **Azure Connected machine erőforrás-rendszergazdai** szerepkör tagja.

## <a name="pilot"></a>Próbaüzem

Mielőtt az összes üzemi gépre telepítené az üzembe helyezést, kezdje a telepítési folyamat kiértékelésével, mielőtt széles körben befogadja a környezetét. Egy próba esetében azonosítsa a vállalatoknak a vállalatok üzleti tevékenysége szempontjából nem kritikus fontosságú, reprezentatív mintavételét. Ügyeljen arra, hogy elegendő időt hagyjon a próbaüzem futtatására és a hatás értékelésére: legalább 30 napot ajánlunk.

Hozzon létre egy formális tervet, amely leírja a pilóta hatókörét és részleteit. Az alábbi példa egy olyan tervet tartalmaz, amelyet az első lépések megtételéhez érdemes megtervezni.

* Célkitűzések – azokat az üzleti és technikai illesztőprogramokat ismerteti, amelyek a próbaüzem meghozatalához szükségesek.
* Kiválasztási feltételek – azokat a feltételeket adja meg, amelyekkel kiválaszthatja, hogy a megoldás mely szempontjait kell bemutatni a próbaüzem segítségével.
* Hatókör – ismerteti a pilóta hatókörét, amely magában foglalja a megoldás-összetevőkre, a várt ütemtervre, a próbaüzem időtartamára és a célként kijelölt gépek számára vonatkozó korlátozásokat.
* Sikerességi feltételek és mérőszámok – határozza meg a próbaüzem sikerességi feltételeit és a siker szintjének meghatározására szolgáló konkrét mértékeket.
* Képzési terv – az Azure-ban és az IT-szolgáltatásokban új, a kísérleti időszakban a rendszermérnökök, a rendszergazdák stb. betanítási terve.
* Áttérési terv – a próbaüzem és az éles környezet közötti áttérés irányításához használt stratégia és feltételek leírása.
* Visszaállítás – a pilóta előzetes üzembe helyezési állapotba való visszaállításának eljárásait ismerteti.
* Kockázatok – felsorolja az összes azonosított kockázatot a próbaüzem végrehajtásához és az éles környezetben való üzembe helyezéshez.

## <a name="phase-1-build-a-foundation"></a>1. fázis: alaprendszer létrehozása

Ebben a fázisban a rendszermérnökök vagy a rendszergazdák lehetővé teszik, hogy a szervezetük Azure-előfizetésének alapvető funkciói a gépeket az arc-kompatibilis kiszolgálók és más Azure-szolgáltatások általi felügyeletre engedélyezzék.

|Feladat |Részletek |Időtartam |
|-----|-------|---------|
| [Erőforráscsoport létrehozása](../../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups) | Egy dedikált erőforráscsoport, amely csak az ív használatára képes kiszolgálókat tartalmazza, és központosítja ezeknek az erőforrásoknak a felügyeletét és figyelését. | Egy óra |
| [Címkék](../../azure-resource-manager/management/tag-resources.md) alkalmazása a gépek rendszerezéséhez. | Kiértékelheti és kifejlesztheti az ÁLTALa igazított [címkézési stratégiát](/cloud-adoption-framework/decision-guides/resource-tagging/) , amely csökkentheti az ív-kompatibilis kiszolgálók kezelésének bonyolultságát, és egyszerűbbé teheti a felügyeleti döntések meghozatalát. | Egy nap |
| [Azure monitor naplók](../../azure-monitor/logs/data-platform-logs.md) megtervezése és üzembe helyezése | A [tervezési és telepítési szempontok](../../azure-monitor/logs/design-logs-deployment.md) kiértékelésével megállapíthatja, hogy a szervezetnek meglévő vagy egy másik log Analytics-munkaterületet kell-e használnia az összegyűjtött naplófájlok hibrid kiszolgálókról és gépekről történő tárolásához. <sup>1</sup> | Egy nap |
| [Azure Policy](../../governance/policy/overview.md) irányítási terv kidolgozása | Határozza meg, hogyan fogja megvalósítani a hibrid kiszolgálók és gépek irányítását az előfizetésben vagy az erőforráscsoport hatókörében Azure Policy. | Egy nap |
| [Szerepköralapú hozzáférés-vezérlés](../../role-based-access-control/overview.md) konfigurálása (RBAC) | Kialakíthat egy hozzáférési tervet, amellyel szabályozhatja, hogy ki férhet hozzá az arc-kompatibilis kiszolgálók kezeléséhez, és hogy a többi Azure-szolgáltatásból és-megoldásból származó adatok megtekinthessék. | Egy nap |
| A már telepített Log Analytics ügynökkel rendelkező gépek azonosítása | Futtassa a következő naplózási lekérdezést a [log Analyticsban](../../azure-monitor/logs/log-analytics-overview.md) , hogy támogassa a meglévő log Analytics ügynök telepítéseit a bővítmény által felügyelt ügynökre:<br> Szívverés <br> &#124;, ahol a TimeGenerated > ezelőtt (30d) <br> &#124;, ahol a ResourceType = = "Machines" és a (ComputerEnvironment = = "nem Azure") <br> &#124; a számítógép, a ResourceProvider, a ResourceType, a ComputerEnvironment és az összefoglalás alapján | Egy óra |

<sup>1</sup> a log Analytics munkaterület kialakításának kiértékelésének részeként fontos szempont, hogy a Update Management és a Change Tracking és a leltár funkció, valamint Azure Security Center és az Azure Sentinel támogatásával Azure Automation integrációja. Ha a szervezete már rendelkezik egy Automation-fiókkal, és engedélyezte a felügyeleti funkcióit egy Log Analytics munkaterülethez társítva, akkor kiértékelheti, hogy központosíthatja és egyszerűsítheti a felügyeleti műveleteket, valamint csökkentheti a költségeket a meglévő erőforrások használatával, illetve duplikált fiók, munkaterület stb. létrehozásával.

## <a name="phase-2-deploy-arc-enabled-servers"></a>2. fázis: az ív használatára képes kiszolgálók üzembe helyezése

Ezt követően az 1. fázisban az üzembe helyezés előkészítésével és az ügynök telepítésének elvégzésével hozzáadjuk az alapítványhoz.

|Feladat |Részletek |Időtartam |
|-----|-------|---------|
| Az előre definiált telepítési parancsfájl letöltése | Tekintse át és szabja testre az előre definiált telepítési parancsfájlt a csatlakoztatott számítógép-ügynök helyszíni telepítéséhez, hogy támogassa az automatikus üzembe helyezési követelményeket.<br><br> Példa a bevezetési erőforrások méretezésére:<br><br> <ul><li> [Alapszintű üzembe helyezési parancsfájl](onboard-service-principal.md)</ul></li> <ul><li>[VMware vSphere Windows Server rendszerű virtuális gépek méretezése](https://github.com/microsoft/azure_arc/blob/master/azure_arc_servers_jumpstart/docs/vmware_scaled_powercli_win.md)</ul></li> <ul><li>[Nagyszabású bevezetési VMware vSphere Linux rendszerű virtuális gépek](https://github.com/microsoft/azure_arc/blob/master/azure_arc_servers_jumpstart/docs/vmware_scaled_powercli_linux.md)</ul></li> <ul><li>[Integrált AWS EC2-példányok méretezése a Ansible használatával](https://github.com/microsoft/azure_arc/blob/master/azure_arc_servers_jumpstart/docs/aws_scale_ansible.md)</ul></li> <ul><li>[Központi telepítés a PowerShell távelérési szolgáltatásával](https://docs.microsoft.com/azure/azure-arc/servers/onboard-powershell) (csak Windows)</ul></li>| Egy vagy több nap a követelményektől, a szervezeti folyamattól (például a módosítási és kiadási felügyelettől) és az Automation-módszertől függően. |
| [Egyszerű szolgáltatás létrehozása](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) |Hozzon létre egy egyszerű szolgáltatást, amely nem interaktív módon, Azure PowerShell vagy a portálon keresztül csatlakozik a gépekhez.| Egy óra |
| A csatlakoztatott számítógép ügynökének üzembe helyezése a célkiszolgálón és a gépeken |Az Automation eszközzel telepítse a parancsfájlokat a kiszolgálókra, és kapcsolódjon az Azure-hoz.| Egy vagy több nap a kiadási tervtől függően, és a szakaszos bevezetést követően. |

## <a name="phase-3-manage-and-operate"></a>3. fázis: kezelés és üzemeltetés

A 3. fázis olyan rendszergazdákat vagy rendszermérnököket lát el, amelyek lehetővé teszik a manuális feladatok automatizálását a csatlakoztatott gép ügynökének és a gépnek az életciklusuk alatt történő kezeléséhez

|Feladat |Részletek |Időtartam |
|-----|-------|---------|
|Resource Health riasztás létrehozása |Ha egy kiszolgáló 15 percnél hosszabb ideig nem küld szívveréseket az Azure-nak, az azt jelenti, hogy offline állapotban van, a hálózati kapcsolat le lett tiltva, vagy az ügynök nem fut. Dolgozzon ki egy tervet az incidensek megválaszolásához és kivizsgálásához, valamint [Resource Health riasztások](../..//service-health/resource-health-alert-monitor-guide.md) használatával, hogy értesítést kapjon az indításakor.<br><br> A riasztás konfigurálásakor a következőket kell megadnia:<br> **Erőforrás típusa**  =  **Azure arc-kompatibilis kiszolgálók**<br> **Aktuális erőforrás állapota**  =  Nem **érhető el**<br> **Korábbi erőforrás állapota**  =  **Elérhető** | Egy óra |
|Azure Advisor riasztás létrehozása | A legjobb megoldás és a legújabb biztonsági és hibajavítások esetében ajánlott az Azure arc-kompatibilis kiszolgálók ügynökének naprakészen tartása. Az elavult ügynökök [Azure Advisor riasztással](../../advisor/advisor-alerts-portal.md)lesznek azonosítva.<br><br> A riasztás konfigurálásakor a következőket kell megadnia:<br> **Javaslat típusa**  =  **Frissítés az Azure Connected Machine Agent legújabb verziójára** | Egy óra |
|[Azure-szabályzatok kiosztása](../../governance/policy/assign-policy-portal.md) az előfizetéshez vagy az erőforráscsoport hatóköréhez |Az előfizetés vagy az erőforráscsoport hatóköréhez rendelje hozzá a **Azure monitor for VMS** [házirend](../../azure-monitor/vm/vminsights-enable-policy.md) engedélyezése (és az igényeinek megfelelő többi) beállítást. Azure Policy lehetővé teszi, hogy olyan szabályzat-definíciókat rendeljen hozzá, amelyek a szükséges ügynököket telepítik Azure Monitor for VMs a környezetében.| Változó |
|[Update Management engedélyezése az ív használatára képes kiszolgálókon](../../automation/update-management/enable-from-automation-account.md) |Update Management konfigurálása Azure Automation az arc-kompatibilis kiszolgálókon regisztrált Windows-és Linux-alapú virtuális gépek operációsrendszer-frissítéseinek kezeléséhez. | 15 perc |

## <a name="next-steps"></a>Következő lépések

* A hibaelhárítási információ a [csatlakoztatott gép ügynökének hibaelhárítása című útmutatóban](troubleshoot-agent-onboard.md)található.

* Megtudhatja, hogyan egyszerűsítheti az üzembe helyezést más Azure-szolgáltatásokkal, például az Azure Automation [állapot-konfigurációval](../../automation/automation-dsc-overview.md) és más támogatott Azure virtuálisgép- [bővítményekkel](manage-vm-extensions.md)