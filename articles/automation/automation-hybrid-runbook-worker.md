---
title: Azure Automation hibrid runbook-feldolgozó áttekintése
description: Ez a cikk áttekintést nyújt a hibrid runbook-feldolgozóról, amellyel runbookokat futtathat a helyi adatközpontban vagy felhőszolgáltatóban található gépeken.
services: automation
ms.subservice: process-automation
ms.date: 01/22/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2bb178302d399805eb84b233060d5717e2dba8b3
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830555"
---
# <a name="hybrid-runbook-worker-overview"></a>Hibrid runbook-feldolgozó – áttekintés

Előfordulhat, hogy Azure Automation-runbookok nem férnek hozzá más felhőkben vagy a helyszíni környezetben található erőforrásokhoz, mert az Azure-felhőplatformon futnak. A Azure Automation Hibrid runbook-feldolgozó szolgáltatásának használatával közvetlenül a szerepkört üzemeltető gépen futtathat runbookokat, illetve a környezet erőforrásain ezen helyi erőforrások kezeléséhez. A runbookok tárolása és kezelése a Azure Automation, majd egy vagy több hozzárendelt gépre való kézbesítése.

## <a name="runbook-worker-types"></a>Runbook-feldolgozótípusok

A Runbook-dolgozóknak két típusa van: rendszer és felhasználó. Az alábbi táblázat a kettő közötti különbséget ismerteti.

|Típus | Description |
|-----|-------------|
|**Rendszer** |Támogatja az Update Management funkció által használt rejtett runbookokat, amelyek célja a felhasználó által megadott frissítések telepítése Windows és Linux rendszerű gépeken.<br> Ez a hibrid runbook-feldolgozótípus nem tagja egy hibrid runbook-feldolgozó csoportnak, ezért nem futtat runbookokat, amelyek runbook-feldolgozó csoportot céloznak meg. |
|**Felhasználó** |Támogatja azokat a felhasználó által definiált runbookokat, amelyek közvetlenül a Windows és Linux rendszerű gépen futnak, és amelyek egy vagy több Runbook-feldolgozócsoport tagjai. |

A hibrid runbook-feldolgozók a Windows vagy a Linux operációs rendszeren is futtathatók, és ez a szerepkör a [Log Analytics-ügynök](../azure-monitor/agents/log-analytics-agent.md) jelentésére támaszkodik egy Azure Monitor [Log Analytics-munkaterületen.](../azure-monitor/logs/design-logs-deployment.md) A munkaterület nemcsak a támogatott operációs rendszer számítógépének figyelése, hanem a hibrid runbook-feldolgozó telepítéséhez szükséges összetevők letöltése is.

Ha Azure Automation [Update Management,](./update-management/overview.md) a Log Analytics-munkaterülethez csatlakoztatott összes gép automatikusan hibrid runbook-feldolgozóként lesz konfigurálva. Ha windowsos hibrid runbook-feldolgozóként konfigurálja, lásd: Windows rendszerű hibrid [runbook-feldolgozó](automation-windows-hrw-install.md) üzembe helyezése Linux rendszeren: Linux rendszerű hibrid [runbook-feldolgozó üzembe helyezése.](automation-linux-hrw-install.md)

## <a name="how-does-it-work"></a>Hogyan működik?

![Hibrid runbook-feldolgozó – áttekintés](media/automation-hybrid-runbook-worker/automation.png)

Minden hibrid runbook-feldolgozó felhasználó tagja egy hibrid runbook-feldolgozó csoportnak, amely a feldolgozó telepítésekor adható meg. A csoportok egyetlen feldolgozót is tartalmazhatnak, de a magas rendelkezésre állás érdekében több feldolgozót is tartalmazhatnak. Minden gép egy hibrid runbook-feldolgozót képes futtatni, amely egy Automation-fióknak jelent; nem regisztrálhatja a hibrid feldolgozót több Automation-fiókban. A hibrid feldolgozók csak egyetlen Automation-fiókból figyelik a feladatokat. A hibrid runbook-feldolgozó által felügyelt hibrid runbook-feldolgozót Update Management gépek hozzáadhatók egy hibrid runbook-feldolgozó csoporthoz. Azonban ugyanazt az Automation-fiókot kell használnia a Update Management és a hibrid runbook-feldolgozó csoporttagsághoz is.

Amikor egy felhasználó hibrid runbook-feldolgozóján indít el egy runbookot, meg kell adnia azt a csoportot, amelyn fut. A csoport minden feldolgozója Azure Automation, hogy vannak-e elérhető feladatok. Ha egy feladat elérhető, a feladatot lekért első feldolgozó veszi át. A feladatok várólistájának feldolgozási ideje a hibrid feldolgozó hardverprofiltól és -terheléstől függ. Egy adott feldolgozót nem lehet megadni. A hibrid feldolgozó egy lekérdezési mechanizmuson (30 másodpercenként) dolgozik, és követi a first-come,first-serve sorrendet. Attól függően, hogy mikor lett lekért feladat, attól függően, hogy melyik hibrid feldolgozó pingelést végez, az Automation szolgáltatás felveszi a feladatot. Egyetlen hibrid feldolgozó általában pingelésenként négy (30 másodpercenként) feladatokat képes felvenni. Ha a lekért feladatok lekért sebessége 30 másodpercenként négynél magasabb, akkor fennáll a lehetőség, hogy a hibrid runbook-feldolgozó csoport egy másik hibrid feldolgozója felvette a feladatot.

A hibrid runbook-feldolgozók nem [](automation-runbook-execution.md#runbook-execution-environment) vonatkoznak sok Azure-beli erőforráskorlátra [a](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits) lemezterületre, a memóriára vagy a hálózati szoftvercsatornákra. A hibrid feldolgozók korlátai csak a feldolgozó saját erőforrásaihoz kapcsolódnak, és nem [](automation-runbook-execution.md#fair-share) korlátozzák őket az Azure-védőkeretek igazságos időkorlátja.

A hibrid runbook-feldolgozókon futó runbookok terjesztésének szabályozásához, valamint a feladatok aktiválásának helyzetéhez és mikéntjéhez regisztrálhatja a hibrid feldolgozót az Automation-fiók különböző hibrid runbook-feldolgozó csoportjaiban. A feladatok adott csoportra vagy csoportokra való célzása a végrehajtási elrendezés támogatása érdekében.

## <a name="hybrid-runbook-worker-installation"></a>Hibrid runbook-feldolgozó telepítése

A felhasználó hibrid runbook-feldolgozó telepítésének folyamata az operációs rendszertől függ. Az alábbi táblázat a központi telepítési típusokat határozza meg.

|Operációs rendszer  |Központi telepítési típusok  |
|---------|---------|
|Windows     | [Automatizált](automation-windows-hrw-install.md#automated-deployment)<br>[Kézi](automation-windows-hrw-install.md#manual-deployment)        |
|Linux     | [Kézi](automation-linux-hrw-install.md#install-a-linux-hybrid-runbook-worker)        |

A Windows rendszerű gépek ajánlott telepítési módszere egy Azure Automation runbook használata a konfigurálási folyamat teljes automatizálására. Ha ez nem megvalósítható, a szerepkör manuális telepítéséhez és konfiguráláshoz kövesse a részletes eljárást. Linux rendszerű gépeken egy Python-szkript futtatásával telepítheti az ügynököt a gépre.

## <a name="network-planning"></a><a name="network-planning"></a>Hálózattervezés

A Azure Automation runbook-feldolgozóhoz szükséges portokkal, URL-címekkel és egyéb hálózati adatokkal kapcsolatos részletes információkért tekintse meg a hálózati konfigurációt. [](automation-network-configuration.md#network-planning-for-hybrid-runbook-worker)

### <a name="proxy-server-use"></a>Proxykiszolgáló használata

Ha proxykiszolgálót használ az Azure Automation Log Analytics-ügynököt futtató gépek közötti kommunikációhoz, győződjön meg arról, hogy a megfelelő erőforrások elérhetők. A hibrid runbook-feldolgozó és az Automation szolgáltatásból származó kérések időkorlátja 30 másodperc. Három kísérlet után a kérés meghiúsul.

### <a name="firewall-use"></a>Tűzfal használata

Ha tűzfalat használ az internethez való hozzáférés korlátozására, a tűzfalat úgy kell konfigurálnia, hogy engedélyezi a hozzáférést. Ha a Log Analytics-átjárót használja proxyként, győződjön meg arról, hogy hibrid runbook-dolgozókhoz van konfigurálva. Lásd: [Configure the Log Analytics gateway for Automation Hybrid Runbook Workers (Log Analytics-átjáró konfigurálása hibrid runbook-dolgozókhoz).](../azure-monitor/agents/gateway.md)

### <a name="service-tags"></a>Szolgáltatáscímkék

Azure Automation támogatja az Azure-beli virtuális hálózati szolgáltatáscímkéket, kezdve a [GuestAndHybridManagement szolgáltatáscímkével.](../virtual-network/service-tags-overview.md) A szolgáltatáscímkékkel hálózati biztonsági csoportok vagy hálózati biztonsági csoportok hálózati hozzáférés-vezérlését definiálhatja, [vagy](../virtual-network/network-security-groups-overview.md#security-rules) [Azure Firewall.](../firewall/service-tags.md) A szolgáltatáscímkék adott IP-címek helyett használhatók biztonsági szabályok létrehozásakor. Ha a szabály megfelelő forrás- vagy célmezőben megadja a **GuestAndHybridManagement**  szolgáltatáscímkenevet, engedélyezheti vagy megtagadhatja a forgalmat az Automation szolgáltatás számára. Ez a szolgáltatáscímke nem támogatja részletesebb vezérlést az IP-tartományok egy adott régióra való korlátozásával.

Az Azure Automation szolgáltatás szolgáltatáscímkéje csak a következő forgatókönyvekhez használt IP-címeket biztosítja:

* Webhookok aktiválása a virtuális hálózaton belülről
* A hibrid runbook-dolgozók vagy State Configuration virtuális hálózat ügynökei kommunikálhatnak az Automation szolgáltatással

>[!NOTE]
>A **GuestAndHybridManagement** szolgáltatáscímke jelenleg nem támogatja a runbook-feladatok végrehajtását azure-beli védőfalon, csak közvetlenül egy hibrid runbook-feldolgozón.

## <a name="support-for-impact-level-5-il5"></a>Az 5. hatásszint (IL5) támogatása

Azure Automation hibrid runbook-feldolgozó használható a Azure Government a hatásszint 5 számítási feladatainak támogatásához az alábbi két konfiguráció valamelyikében:

* [Elkülönített virtuális gép.](../azure-government/documentation-government-impact-level-5.md#isolated-virtual-machines) Az üzembe helyezéskor a gép teljes fizikai gazdagépét felhasználják, biztosítva az IL5 számítási feladatok támogatásához szükséges elkülönítési szintet.

* [Azure Dedicated Hosts](../azure-government/documentation-government-impact-level-5.md#azure-dedicated-host), amely fizikai kiszolgálókat biztosít, amelyek egy vagy több, egy Azure-előfizetéshez dedikált virtuális gépet képesek szolgáltatni.

>[!NOTE]
>A hibrid runbook-feldolgozói szerepkör által biztosított számítási elkülönítés elérhető az Azure kereskedelmi és USA kormányzati felhőiben.

### <a name="update-management-addresses-for-hybrid-runbook-worker"></a>Update Management hibrid runbook-feldolgozóhoz való Update Management címek

A hibrid runbook-feldolgozóhoz szükséges szabványos címek és portok mellett a Update Management a hálózattervezés szakaszban ismertetett egyéb hálózati konfigurációs [követelményekkel is](./update-management/overview.md#ports) rendelkezik.

## <a name="azure-automation-state-configuration-on-a-hybrid-runbook-worker"></a>Azure Automation State Configuration runbook-feldolgozón való futtatás

Futtathat [Azure Automation State Configuration](automation-dsc-overview.md) hibrid runbook-feldolgozón. A hibrid runbook-feldolgozót támogató kiszolgálók konfigurációjának kezeléséhez DSC-csomópontként kell hozzáadnia a kiszolgálókat. Lásd: [Gépek felügyeletének engedélyezése Azure Automation State Configuration.](automation-dsc-onboarding.md)

## <a name="runbook-worker-limits"></a>Runbook Worker limits (Runbook-feldolgozó korlátai)

Az Automation-fiókonkénti hibrid feldolgozócsoportok maximális száma 4000, és mindkét rendszer- és & hibrid feldolgozókra vonatkozik. Ha több mint 4000 gépet kell kezelnie, javasoljuk, hogy hozzon létre egy másik Automation-fiókot.

## <a name="runbooks-on-a-hybrid-runbook-worker"></a>Runbookok hibrid runbook-feldolgozón

Lehetnek olyan runbookok, amelyek a helyi gépen kezelik az erőforrásokat, vagy olyan helyi környezetben található erőforrásokon futnak, ahol a felhasználó hibrid runbook-feldolgozót telepít. Ebben az esetben dönthet úgy, hogy a runbookokat egy Automation-fiók helyett hibrid feldolgozón futtatja. A hibrid runbook-feldolgozón futó runbookok struktúrája megegyezik az Automation-fiókban futtatott forgatókönyvekkel. Lásd: [Runbookok futtatása hibrid runbook-feldolgozón.](automation-hrw-run-runbooks.md)

### <a name="hybrid-runbook-worker-jobs"></a>Hibrid runbook-feldolgozó feladatok

A hibrid runbook-feldolgozó  feladatok a helyi Rendszer fiók alatt futnak Windows rendszeren vagy [az nxautomation fiókban](automation-runbook-execution.md#log-analytics-agent-for-linux) Linuxon. Azure Automation a hibrid runbook-dolgozókon futó feladatokat másképp kezeli, mint az Azure-beli sandboxesban futó feladatokat. Lásd: [Runbook végrehajtási környezete.](automation-runbook-execution.md#runbook-execution-environment)

Ha a hibrid runbook-feldolgozó gazdagép újraindul, a futó runbook-feladatok az elejétől vagy a PowerShell-alapú munkafolyamat-runbookok utolsó ellenőrzőpontjétől indulnak újra. A runbook-feladat háromnál többszöri újraindítása után a rendszer felfüggeszti.

### <a name="runbook-permissions-for-a-hybrid-runbook-worker"></a>Runbook-engedélyek hibrid runbook-feldolgozóhoz

Mivel nem Azure-erőforrásokhoz férnek hozzá, a hibrid runbook-feldolgozón futó runbookok nem tudják használni az Azure-erőforrásokhoz hitelesítő runbookok által jellemzően használt hitelesítési mechanizmust. A runbook saját hitelesítést biztosít a helyi erőforrásokhoz, vagy konfigurálja a hitelesítést az Azure-erőforrások felügyelt [identitásai használatával.](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager) A runbookok felhasználói környezetének megadásához megadhat egy runbookot is.

## <a name="view-system-hybrid-runbook-workers"></a>Rendszer hibrid runbook-dolgozók megtekintése

Miután az Update Management szolgáltatás engedélyezve van Windows vagy Linux rendszerű gépeken, leltárt kaphat a rendszer hibrid runbook-dolgozók csoportjáról a Azure Portal. Akár 2000 dolgozót is megtekinthet a portálon, ha a kiválasztott  Automation-fiók bal oldali panelének Hibrid dolgozók csoportjának Rendszer hibrid dolgozók csoportját választja. 

:::image type="content" source="./media/automation-hybrid-runbook-worker/system-hybrid-workers-page.png" alt-text="Automation-fiókrendszer hibrid feldolgozói csoportjai oldal" border="false" lightbox="./media/automation-hybrid-runbook-worker/system-hybrid-workers-page.png":::

Ha több mint 2000 hibridmunkással van, az összes listáját a következő PowerShell-szkript futtatásával kaphatja meg:

```powershell
"Get-AzSubscription -SubscriptionName "<subscriptionName>" | Set-AzContext
$workersList = (Get-AzAutomationHybridWorkerGroup -ResourceGroupName "<resourceGroupName>" -AutomationAccountName "<automationAccountName>").Runbookworker
$workersList | export-csv -Path "<Path>\output.csv" -NoClobber -NoTypeInformation"
```

## <a name="next-steps"></a>Következő lépések

* A Runbookok hibrid runbook-feldolgozón való futtatása hibrid runbook-feldolgozón cikkből megtudhatja, hogyan konfigurálhatja a forgatókönyveket a helyszíni adatközpontban vagy más felhőalapú környezetben futó folyamatok [automatizálására.](automation-hrw-run-runbooks.md)

* További információ a hibrid runbook-feldolgozók hibaelhárításáról: [Hibrid runbook-feldolgozóval kapcsolatos problémák elhárítása.](troubleshoot/hybrid-runbook-worker.md#general)
