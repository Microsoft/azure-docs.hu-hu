---
title: Az engedélyezett kiszolgálók Azure Arc üzembe helyezése
description: Megtudhatja, hogyan engedélyezheti nagy számú gép számára, hogy engedélyező Azure Arc az Azure alapvető biztonsági, felügyeleti és monitorozási képességeinek konfigurálásán.
ms.date: 04/21/2021
ms.topic: conceptual
ms.openlocfilehash: e3f8fe410da56f627ceab5f17c980f2daa1a262c
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831977"
---
# <a name="plan-and-deploy-arc-enabled-servers"></a>Arc-kompatibilis kiszolgálók megterv helyezése és üzembe helyezése

Az it-infrastruktúra-szolgáltatások vagy üzleti alkalmazások üzembe helyezése minden vállalat számára kihívást jelent. A jó végrehajtás és a nem várt meglepetések és nem tervezett költségek elkerülése érdekében alaposan meg kell tervezni, hogy a lehető legnagyobb mértékben felkészült legyen. Az engedélyezett Azure Arc nagy léptékű üzembe helyezésének tervezéséhez meg kell tervezni a feladatok sikeres végrehajtásához szükséges tervezési és telepítési feltételeket.

Ahhoz, hogy az üzembe helyezés zökkenőmentesen folytatódjon, a tervnek a következő ismereteket kell egyértelműen megértenie:

* Szerepkörök és felelősségek.
* Fizikai kiszolgálók vagy virtuális gépek leltára annak ellenőrzéséhez, hogy megfelelnek-e a hálózati és rendszerkövetelményeknek.
* A sikeres üzembe helyezéshez és a felügyelethez szükséges készségkészlet és képzés.
* Elfogadási feltételek és a sikeresség nyomon követése.
* Az üzembe helyezés automatizálása érdekében használható eszközök vagy módszerek.
* Azonosított kockázatok és kockázatcsökkentési tervek a késések, kimaradások stb. elkerülése érdekében.
* Hogyan kerülheti el a kimaradást az üzembe helyezés során.
* Mi az eszkalációs útvonal jelentős probléma esetén?

Ennek a cikknek az a célja, hogy felkészült legyen az engedélyezett Azure Arc több éles fizikai kiszolgálón vagy a környezetben lévő virtuális gépeken történő sikeres telepítésére.

## <a name="prerequisites"></a>Előfeltételek

* A gépek egy támogatott [operációs rendszert futtatnak](agent-overview.md#supported-operating-systems) a Csatlakoztatott gép ügynök számára.
* A gépek közvetlenül vagy proxykiszolgálón keresztül is csatlakoztatva vannak a helyszíni hálózatról vagy más felhőalapú környezetből az Azure-beli erőforrásokhoz.
* Az Arc-kompatibilis kiszolgálók csatlakoztatottgép-ügynökének telepítéséhez és konfiguráláshoz emelt szintű (azaz rendszergazdai vagy rendszergazdai) jogosultságokkal rendelkező fióknak kell lennie a gépeken.
* A gépek az Azure Connected Machine **szerepkörbe való bevetésében vannak bevetve.**
* A gépek olvasása, módosítása és törlése az **erőforrás-rendszergazdai Azure Connected Machine tagja.**

## <a name="pilot"></a>Próbaüzem

Mielőtt az összes éles gépre üzembe helyezi az üzembe helyezést, először kiértékelheti ezt az üzembe helyezési folyamatot, mielőtt széles körben beveszi a környezetében. A próba során azonosítsa azon gépek reprezentatív mintavételezését, amelyek nem kritikus fontosságúak a vállalat üzleti tevékenységének szempontjából. Mindenképpen hagyjon elegendő időt a próba futtatására és a hatás felmérésére: javasoljuk, hogy legalább 30 napot tartsunk.

Hozzon létre egy formális tervet, amely leírja a próbaterv hatókörét és részleteit. Az alábbiakban egy példa arra, hogy mit kell tartalmaznia egy tervnek az első lépésekhez.

* Célkitűzések – Azokat az üzleti és technikai hajtókat ismerteti, amelyek a próbakra vonatkozó döntéshez vezettek.
* Kiválasztási feltételek – Meghatározza azokat a kritériumokat, amelyek alapján kiválasztható, hogy a megoldás mely aspektusait mutatja be egy próba.
* Hatókör – A próbaterv hatókörét ismerteti, beleértve többek között a megoldás összetevőit, a tervezett ütemezést, a próbaterv időtartamát és a megcélzható gépek számát.
* Sikerességi feltételek és mérőszámok – A próba próba sikerességi feltételeinek és a siker szintjének meghatározásához használt konkrét intézkedések meghatározása.
* Képzési terv – Ismerteti azokat a rendszermérnököket, rendszergazdákat stb. betanító tervet, akik még nem írják le az Azure-t és az informatikai szolgáltatásokat a próbaterv során.
* Áttérési terv – A próbatervről az éles környezetbe való áttérést útmutató stratégiát és kritériumokat ismerteti.
* Visszaállítás – A próba próbatelepítés előtti állapotra való visszaállításának eljárásait ismerteti.
* Kockázatok – Sorolja fel a próba elvégzésével és az éles üzembe helyezéssel kapcsolatos összes azonosított kockázatot.

## <a name="phase-1-build-a-foundation"></a>1. fázis: Alap kiépítése

Ebben a fázisban a rendszermérnökök vagy rendszergazdák lehetővé teszik, hogy a szervezetük Azure-előfizetésének alapvető funkciói elindítsák az alaprendszert, mielőtt engedélyezték volna a gépek számára az Arc-kompatibilis kiszolgálók és más Azure-szolgáltatások felügyeletét.

|Feladat |Részletek |Időtartam |
|-----|-------|---------|
| [Erőforráscsoport létrehozása](../../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups) | Dedikált erőforráscsoport, amely csak Arc-kompatibilis kiszolgálókat tartalmaz, és központosítja ezeknek az erőforrásoknak a felügyeletét és monitorozását. | Egy óra |
| Címkék [alkalmazása a](../../azure-resource-manager/management/tag-resources.md) gépek rendszerezésének segítése. | Értékelje ki és fejlessze ki az iterált címkézési stratégiát, amely csökkentheti az Arc-kompatibilis kiszolgálók kezelésének összetettségét, és leegyszerűsítheti a felügyeleti döntések meghozatalát. [](/azure/cloud-adoption-framework/decision-guides/resource-tagging/) | Egy nap |
| A naplók tervezése [és Azure Monitor telepítése](../../azure-monitor/logs/data-platform-logs.md) | Értékelje [ki a tervezési és üzembe helyezési](../../azure-monitor/logs/design-logs-deployment.md) szempontokat annak meghatározásához, hogy a szervezet használhat-e meglévő Log Analytics-munkaterületet, vagy implementálja-e egy másik Log Analytics-munkaterületet a hibrid kiszolgálókról és gépekről gyűjtött naplóadatok tárolására. <sup>1</sup> | Egy nap |
| [Cégirányítási Azure Policy](../../governance/policy/overview.md) kidolgozása | Határozza meg, hogyan valósítja meg a hibrid kiszolgálók és gépek irányítását az előfizetés vagy az erőforráscsoport hatókörében a Azure Policy. | Egy nap |
| Szerepköralapú [hozzáférés-vezérlés](../../role-based-access-control/overview.md) (RBAC) konfigurálása | Egy hozzáférési terv kidolgozása annak szabályozására, hogy ki férhet hozzá az Arc-kompatibilis kiszolgálók kezeléséhez, és hogy más Azure-szolgáltatásokból és -megoldásokból származó adatokat is megtekinthet. | Egy nap |
| Gépek azonosítása, amelyeken már telepítve van a Log Analytics-ügynök | Futtassa a következő naplólekérdezéseket a [Log Analyticsben](../../azure-monitor/logs/log-analytics-overview.md) a meglévő Log Analytics-ügynöktelepítések bővítmény által felügyelt ügynökre konvertálásának támogatásához:<br> Szívverés <br> &#124; a TimeGenerated > ago(30d) <br> &#124; resourceType == "machines" és (ComputerEnvironment == "Non-Azure") <br> &#124;, ResourceProvider, ResourceType, ComputerEnvironment szerint | Egy óra |

<sup>1</sup> Fontos szempont a Log Analytics-munkaterület kialakításának kiértékelése során, hogy integrálni kell az Azure Automation-t az Update Management és változáskövetés és leltározás funkció, valamint a Azure Security Center és Azure Sentinel. Ha a szervezet már rendelkezik Automation-fiókkal, és engedélyezte a Log Analytics-munkaterülethez kapcsolt felügyeleti funkcióit, értékelje ki, hogy központosíthatja és egyszerűsítheti-e a felügyeleti műveleteket, valamint minimalizálhatja-e a költségeket, ha ezeket a meglévő erőforrásokat használja, vagy duplikált fiókot, munkaterületet stb. hoz létre.

## <a name="phase-2-deploy-arc-enabled-servers"></a>2. fázis: Arc-kompatibilis kiszolgálók üzembe helyezése

Ezután az 1. fázisban lefektetett alaphoz az Arc-kompatibilis kiszolgálók előkészítésével és üzembe helyezésével adjuk hozzá a Connected Machine ügynököt.

|Feladat |Részletek |Időtartam |
|-----|-------|---------|
| Az előre definiált telepítési szkript letöltése | Tekintse át és szabja testre az előre meghatározott telepítési szkriptet a Csatlakoztatott gép ügynök nagy léptékű telepítéséhez az automatikus központi telepítési követelmények támogatása érdekében.<br><br> Példa nagy léptékű be- és beiratás erőforrásaira:<br><br> <ul><li> [Nagy léptékű alapszintű üzembe helyezési szkript](onboard-service-principal.md)</ul></li> <ul><li>[Nagy léptékű VMware vSphere Windows Server rendszerű virtuális gépekhez](https://github.com/microsoft/azure_arc/blob/main/docs/azure_arc_jumpstart/azure_arc_servers/scaled_deployment/vmware_scaled_powercli_win/_index.md)</ul></li> <ul><li>[Linux rendszerű virtuális gépek VMware vSphere nagy léptékű telepítése](https://github.com/microsoft/azure_arc/blob/main/docs/azure_arc_jumpstart/azure_arc_servers/scaled_deployment/vmware_scaled_powercli_linux/_index.md)</ul></li> <ul><li>[AWS EC2-példányok nagy léptékű, Ansible-hez való használata](https://github.com/microsoft/azure_arc/blob/main/docs/azure_arc_jumpstart/azure_arc_servers/scaled_deployment/aws_scaled_ansible/_index.md)</ul></li> <ul><li>[Nagy léptékű üzembe helyezés PowerShell-alapú eltolás használatával](./onboard-powershell.md) (csak Windowson)</ul></li>| A követelményektől, a szervezeti folyamatoktól (például a változás- és kiadáskezeléstől) és a használt automatizálási módszertől függően egy vagy több nap. |
| [Egyszerű szolgáltatás létrehozása](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) |Hozzon létre egy egyszerű szolgáltatást a gépek nem interaktív csatlakoztatására a Azure PowerShell vagy a portálról.| Egy óra |
| A Connected Machine-ügynök telepítése a célkiszolgálókra és gépekre |Az automatizálási eszközzel üzembe helyezheti a szkripteket a kiszolgálókon, és csatlakoztathatja őket az Azure-hoz.| A kiadási tervtől és a szakaszos bevezetéstől függően egy vagy több nap. |

## <a name="phase-3-manage-and-operate"></a>3. fázis: Kezelés és működés

A 3. fázisban a rendszergazdák vagy a rendszermérnökök lehetővé teszik a manuális feladatok automatizálását a csatlakoztatott gép ügynökének és a gépnek az életciklusuk során való kezeléséhez és működtetéséhez.

|Feladat |Részletek |Időtartam |
|-----|-------|---------|
|Riasztás Resource Health létrehozása |Ha egy kiszolgáló 15 percnél tovább nem küld szívveréseket az Azure-nak, az azt jelentheti, hogy offline állapotban van, a hálózati kapcsolat le lett tiltva, vagy az ügynök nem fut. Tervezze meg, hogyan reagál majd ezekre az [](../..//service-health/resource-health-alert-monitor-guide.md) incidensekre, és hogyan vizsgálja meg ezeket az Resource Health, hogy értesítést kapjon, amikor elindulnak.<br><br> A riasztás konfigurálásakor adja meg a következőket:<br> **Erőforrástípus**  =  **Azure Arc engedélyezett kiszolgálók**<br> **Az erőforrás aktuális állapota**  =  **Nem érhető el**<br> **Előző erőforrás állapota**  =  **Elérhető** | Egy óra |
|Riasztás Azure Advisor létrehozása | A legjobb élmény, valamint a legújabb biztonsági és hibajavítások érdekében javasoljuk, hogy a Azure Arc-kompatibilis kiszolgálók ügynökét naprakészen tartsa. Az elavult ügynököket a rendszer egy új [riasztással Azure Advisor azonosítja.](../../advisor/advisor-alerts-portal.md)<br><br> A riasztás konfigurálásakor adja meg a következőket:<br> **Javaslat típusa**  =  **Frissítsen a legújabb verzióra** Azure Connected Machine Agent | Egy óra |
|[Azure-szabályzatok hozzárendelése](../../governance/policy/assign-policy-portal.md) az előfizetés vagy az erőforráscsoport hatóköréhez |Rendelje hozzá **az Azure Monitor for VMs** [szabályzatot](../../azure-monitor/vm/vminsights-enable-policy.md) (és az igényeinek megfelelőket) az előfizetés vagy az erőforráscsoport hatóköréhez. Azure Policy lehetővé teszi olyan szabályzatdefiníciók hozzárendelését, amelyek a virtuális gépek elemzéséhez szükséges ügynököket telepítik a környezetben.| Változó |
|[Az Update Management engedélyezése az Arc-kompatibilis kiszolgálókhoz](../../automation/update-management/enable-from-automation-account.md) |Konfigurálja Update Management-Azure Automation az Arc-kompatibilis kiszolgálókon regisztrált Windows és Linux rendszerű virtuális gépek operációsrendszer-frissítéseit. | 15 perc |

## <a name="next-steps"></a>Következő lépések

* A hibaelhárítási információkat a Csatlakoztatott gép ügynök hibaelhárítása [útmutatóban talál.](troubleshoot-agent-onboard.md)

* Megtudhatja, hogyan egyszerűsítheti az üzembe helyezést más Azure-szolgáltatásokkal, például Azure Automation [State Configuration](../../automation/automation-dsc-overview.md) és más támogatott Azure-beli virtuálisgép-bővítményekkel. [](manage-vm-extensions.md)