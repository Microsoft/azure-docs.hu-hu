---
title: Azure Automation Hybrid Runbook Worker – áttekintés
description: Ez a cikk áttekintést nyújt a hibrid Runbook-feldolgozóról, amellyel runbookok futtathatók a helyi adatközpontban vagy a felhőalapú szolgáltatóban található gépeken.
services: automation
ms.subservice: process-automation
ms.date: 01/22/2021
ms.topic: conceptual
ms.openlocfilehash: 7cf18b6b677daaf97d425c86a0cad91b3abcb225
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98896952"
---
# <a name="hybrid-runbook-worker-overview"></a>Hibrid runbook-feldolgozó – áttekintés

Előfordulhat, hogy a Azure Automation runbookok nem fér hozzá más felhőben vagy a helyszíni környezetben található erőforrásokhoz, mert az Azure Cloud platformon futnak. A Azure Automation Hybrid Runbook Worker szolgáltatásával a runbookok közvetlenül a szerepkört üzemeltető gépen és a környezet erőforrásain keresztül futtathatja a helyi erőforrások kezeléséhez. A runbookok tárolása és kezelése Azure Automation történik, majd egy vagy több hozzárendelt géphez érkezik.

## <a name="runbook-worker-types"></a>Runbook Worker típusai

Kétféle Runbook-feldolgozó létezik – rendszer és felhasználó. A következő táblázat ismerteti a kettő közötti különbséget.

|Típus | Description |
|-----|-------------|
|**Rendszer** |A támogatja a Update Management szolgáltatás által használt rejtett runbookok, amelyek a Windows és Linux rendszerű gépeken a felhasználó által megadott frissítések telepítéséhez lettek kialakítva.<br> Az ilyen típusú hibrid Runbook-feldolgozó nem tagja a hibrid Runbook Worker csoportnak, ezért nem futtatja a Runbook-munkavégző csoportra irányuló runbookok. |
|**Felhasználó** |Támogatja a felhasználó által definiált runbookok, amely közvetlenül a Windows és Linux rendszerű gépen fut, amely egy vagy több Runbook Worker-csoport tagja. |

A hibrid Runbook-feldolgozók a Windows vagy a Linux operációs rendszeren is futtathatók, és ez a szerepkör a [log Analytics ügynökre](../azure-monitor/platform/log-analytics-agent.md) támaszkodik Azure monitor [log Analytics munkaterületre](../azure-monitor/platform/design-logs-deployment.md). A munkaterület nem csak a támogatott operációs rendszerhez tartozó gép figyelésére használható, hanem a hibrid Runbook-feldolgozó telepítéséhez szükséges összetevők letöltésére is.

Ha Azure Automation [Update Management](./update-management/overview.md) engedélyezve van, az log Analytics munkaterülethez csatlakoztatott összes gép automatikusan rendszer hibrid Runbook-feldolgozóként van konfigurálva. A Windows hibrid Runbook-feldolgozóként való konfigurálásáról lásd: a [Windows Hybrid Runbook Worker](automation-windows-hrw-install.md) és a Linux telepítése. a [Linux Hybrid Runbook Worker üzembe helyezése](automation-linux-hrw-install.md)című témakörben talál további információt.

## <a name="how-does-it-work"></a>Hogyan működik?

![Hibrid runbook-feldolgozó – áttekintés](media/automation-hybrid-runbook-worker/automation.png)

Minden felhasználó hibrid Runbook-feldolgozó tagja egy hibrid Runbook Worker csoportnak, amelyet a feldolgozó telepítésekor adott meg. Egy csoport egyetlen feldolgozót tartalmazhat, de a magas rendelkezésre állás érdekében több feldolgozót is felvehet egy csoportba. Minden gép egyetlen Automation-fiókba tud bejelenteni egy hibrid Runbook Worker-jelentést; a hibrid feldolgozót nem lehet több Automation-fiókban regisztrálni. A hibrid feldolgozók csak egyetlen Automation-fiókból tudják figyelni a feladatokat. A Update Management által felügyelt, rendszerhibrid Runbook-feldolgozót futtató gépekhez hozzáadhatók egy hibrid Runbook Worker csoportjához. Azonban ugyanazt az Automation-fiókot kell használnia mind a Update Management, mind a hibrid Runbook-feldolgozói csoporttagság esetében.

Ha a runbook egy felhasználói hibrid Runbook-feldolgozón indítja el, akkor azt a csoportot kell megadnia, amelyen az fut. A csoport minden munkatársa lekérdezi Azure Automation, hogy van-e elérhető feladat. Ha egy feladatot elérhetővé tesz, az első feldolgozónak kell megszereznie a feladatot. A feladatok várólistájának feldolgozási ideje a hibrid feldolgozói hardverprofil és a betöltéstől függ. Nem adhat meg egy adott dolgozót. A hibrid feldolgozó egy lekérdezési mechanizmuson működik (30 mp), és az első lépések sorrendjét követi. Attól függően, hogy mikor lett leküldve a feladatok, attól függően, hogy a hibrid feldolgozók pingelése felveszi-e a feladatot. Egyetlen hibrid feldolgozó általában négy feladatot tud felvenni ping (azaz 30 másodpercenként). Ha a feladatok végrehajtásának sebessége 30 másodpercenként meghaladja a négyet, akkor magas lehetőség van egy másik hibrid feldolgozóra is a hibrid Runbook Worker csoportban.

A hibrid Runbook-feldolgozók nem rendelkeznek sok Azure-beli [sandbox](automation-runbook-execution.md#runbook-execution-environment) -erőforrással a lemezterületre, a memóriára vagy a hálózati szoftvercsatornára [vonatkozóan](../azure-resource-manager/management/azure-subscription-service-limits.md#automation-limits) . A hibrid feldolgozók korlátai csak a munkavégző saját erőforrásaihoz kapcsolódnak, és nem korlátozzák az Azure-beli munkaterületek [valós megosztási](automation-runbook-execution.md#fair-share) korlátját.

A hibrid Runbook-feldolgozók runbookok eloszlásának szabályozására, valamint a feladatok aktiválásának vagy módjának a megadásával a hibrid feldolgozó a különböző hibrid Runbook-feldolgozói csoportokba is regisztrálható az Automation-fiókon belül. Az adott csoportra vagy csoportokra irányuló feladatok célzása a végrehajtási elrendezés támogatásához.

## <a name="hybrid-runbook-worker-installation"></a>Hibrid Runbook Worker telepítése

A felhasználó hibrid Runbook-feldolgozó telepítésének folyamata az operációs rendszertől függ. Az alábbi táblázat a központi telepítési típusokat határozza meg.

|Operációs rendszer  |Központi telepítési típusok  |
|---------|---------|
|Windows     | [Automatizált](automation-windows-hrw-install.md#automated-deployment)<br>[Kézi](automation-windows-hrw-install.md#manual-deployment)        |
|Linux     | [Kézi](automation-linux-hrw-install.md#install-a-linux-hybrid-runbook-worker)        |

A Windows rendszerű gépek ajánlott telepítési módszere egy Azure Automation runbook használata, amellyel teljesen automatizálható a konfigurálásának folyamata. Ha ez nem valósítható meg, a szerepkör manuális telepítéséhez és konfigurálásához hajtsa végre a lépésenkénti útmutatót. A Linux rendszerű gépeken egy Python-szkriptet futtathat, amely telepíti az ügynököt a gépre.

## <a name="network-planning"></a><a name="network-planning"></a>Hálózattervezés

A hibrid Runbook-feldolgozóhoz szükséges portok, URL-címek és egyéb hálózatkezelési adatok részletes adatainak megtekintéséhez [Azure Automation hálózati konfigurációt](automation-network-configuration.md#network-planning-for-hybrid-runbook-worker) .

### <a name="proxy-server-use"></a>Proxykiszolgáló használata

Ha a Azure Automation és a Log Analytics ügynököt futtató gépek közötti kommunikációhoz proxykiszolgálót használ, győződjön meg arról, hogy a megfelelő erőforrások elérhetők. A hibrid Runbook Worker és Automation szolgáltatásokból érkező kérések időtúllépése 30 másodperc. Három próbálkozás után egy kérelem meghiúsul.

### <a name="firewall-use"></a>Tűzfal használata

Ha tűzfal használatával korlátozza az internethez való hozzáférést, a tűzfalat úgy kell konfigurálnia, hogy engedélyezze a hozzáférést. Ha a Log Analytics-átjárót proxyként használja, győződjön meg arról, hogy a hibrid Runbook-feldolgozók számára van konfigurálva. Lásd: [a log Analytics átjáró konfigurálása az Automation hibrid Runbook-feldolgozók számára](../azure-monitor/platform/gateway.md).

### <a name="service-tags"></a>Szolgáltatáscímkék

A Azure Automation támogatja az Azure Virtual Network szolgáltatás címkéit, a szolgáltatási címke [GuestAndHybridManagement](../virtual-network/service-tags-overview.md)kezdve. A szolgáltatás-címkék használatával hálózati [biztonsági csoportokon](../virtual-network/network-security-groups-overview.md#security-rules) vagy [Azure Firewallon](../firewall/service-tags.md)is meghatározhat hálózati hozzáférés-vezérlést. A szolgáltatás címkéi a biztonsági szabályok létrehozásakor a megadott IP-címek helyett használhatók. Ha a szolgáltatási címke nevét **GuestAndHybridManagement**  adja meg egy szabály megfelelő forrás vagy cél mezőjében, engedélyezheti vagy megtagadhatja az Automation szolgáltatás forgalmát. Ez a szolgáltatási címke nem támogatja az IP-címtartományok egy adott régióra való korlátozásával a részletesebb szabályozás engedélyezését.

A Azure Automation szolgáltatáshoz tartozó szolgáltatás címkéje csak a következő forgatókönyvekhez használt IP-címeket biztosítja:

* Webhookok indítása a virtuális hálózaton belülről
* A hibrid Runbook-feldolgozók vagy állami konfigurációs ügynökök engedélyezése a VNet az Automation szolgáltatással való kommunikációhoz

>[!NOTE]
>A **GuestAndHybridManagement** szolgáltatás jelenleg nem támogatja a runbook-feladatok végrehajtását egy Azure-beli homokozóban, csak egy hibrid runbook-feldolgozón.

## <a name="support-for-impact-level-5-il5"></a>5. szintű Impact-támogatás (IL5)

Azure Automation Hybrid Runbook Worker Azure Government használható a következő két konfiguráció egyikében az 5. szintű munkaterhelések támogatásához:

* [Elkülönített virtuális gép](../azure-government/documentation-government-impact-level-5.md#isolated-virtual-machines). Üzembe helyezéskor a rendszer az adott gép teljes fizikai gazdagépét használja fel a IL5-munkaterhelések támogatásához szükséges elkülönítési szint biztosításához.

* Az [Azure dedikált gazdagépei](../azure-government/documentation-government-impact-level-5.md#azure-dedicated-host)olyan fizikai kiszolgálókat biztosítanak, amelyek egy vagy több virtuális gép üzemeltetésére képesek egy Azure-előfizetéshez.

>[!NOTE]
>A hibrid Runbook-feldolgozói szerepkörrel való számítási elkülönítés az Azure kereskedelmi és az USA kormányzati felhők számára érhető el.

### <a name="update-management-addresses-for-hybrid-runbook-worker"></a>A hibrid Runbook-feldolgozók Update Management címei

A hibrid Runbook-feldolgozóhoz szükséges szabványos címek és portok mellett Update Management a [hálózati tervezés](./update-management/overview.md#ports) szakaszban leírt egyéb hálózati konfigurációs követelmények is szerepelnek.

## <a name="azure-automation-state-configuration-on-a-hybrid-runbook-worker"></a>Állapot-konfiguráció Azure Automation hibrid Runbook-feldolgozón

[Azure Automation állapot konfigurációját](automation-dsc-overview.md) futtathatja hibrid Runbook-feldolgozón is. A hibrid Runbook-feldolgozót támogató kiszolgálók konfigurációjának kezeléséhez a kiszolgálókat DSC-csomópontként kell hozzáadnia. Lásd: [a gépek Azure Automation állapot-konfiguráció általi felügyeletének engedélyezése](automation-dsc-onboarding.md).

## <a name="runbook-worker-limits"></a>Runbook-feldolgozói korlátok

Az Automation-fiókhoz tartozó hibrid feldolgozói csoportok maximális száma 4000, és a rendszer & felhasználói hibrid feldolgozók esetében is alkalmazható. Ha több mint 4 000 gépet szeretne kezelni, javasoljuk, hogy hozzon létre egy másik Automation-fiókot.

## <a name="runbooks-on-a-hybrid-runbook-worker"></a>Runbookok hibrid Runbook-feldolgozón

Lehetnek olyan runbookok, amelyek a helyi gépen lévő erőforrásokat felügyelik, vagy a helyi környezet erőforrásain futnak, ahol a felhasználó hibrid Runbook-feldolgozója telepítve van. Ebben az esetben dönthet úgy, hogy egy Automation-fiók helyett a hibrid feldolgozón futtatja a runbookok. A hibrid Runbook-feldolgozón futó runbookok az Automation-fiókban futtatott struktúrában azonosak. Lásd: [Runbookok futtatása hibrid Runbook-feldolgozón](automation-hrw-run-runbooks.md).

### <a name="hybrid-runbook-worker-jobs"></a>Hibrid Runbook Worker-feladatok

A hibrid Runbook-feldolgozói feladatok **a helyi** rendszerfiók alatt futnak a Windows rendszeren vagy a Linuxon futó [nxautomation-fiókban](automation-runbook-execution.md#log-analytics-agent-for-linux) . A Azure Automation a feladatokat a hibrid Runbook-feldolgozókon eltérően kezeli az Azure-beli munkaterületeken futó feladatoktől. Lásd: [Runbook végrehajtási környezet](automation-runbook-execution.md#runbook-execution-environment).

Ha a hibrid Runbook Worker gazdagépe újraindul, a futó Runbook-feladatok a kezdetektől vagy a PowerShell-munkafolyamat runbookok utolsó ellenőrzőpontján indulnak újra. A runbook-feladatok többszöri újraindítása után a rendszer felfüggeszti a műveletet.

### <a name="runbook-permissions-for-a-hybrid-runbook-worker"></a>Runbook engedélyek a hibrid Runbook-feldolgozók számára

Mivel a nem Azure-beli erőforrásokhoz férnek hozzá, a runbookok-t használó hibrid Runbook-feldolgozók nem használhatják az Azure-erőforrásokhoz való runbookok-hitelesítéssel jellemzően használt hitelesítési mechanizmust. A runbook lehetővé teszi a saját hitelesítését a helyi erőforrásokhoz, vagy a hitelesítést [Az Azure-erőforrások felügyelt identitásai](../active-directory/managed-identities-azure-resources/tutorial-windows-vm-access-arm.md#grant-your-vm-access-to-a-resource-group-in-resource-manager)segítségével konfigurálja. Megadhat egy futtató fiókot is, amely felhasználói környezetet biztosít az összes runbookok számára.

## <a name="view-system-hybrid-runbook-workers"></a>A System Hybrid Runbook-feldolgozók megtekintése

Miután a Update Management funkció engedélyezve van a Windows vagy Linux rendszerű gépeken, leltárba veheti a rendszerek hibrid Runbook-feldolgozói csoportjának listáját a Azure Portalban. A portálon akár 2 000 feldolgozót is megtekintheti, ha a kiválasztott Automation-fiók bal oldali paneljén a hibrid feldolgozók **csoport** lap **rendszer hibrid feldolgozók** csoportja elemére kattint.

:::image type="content" source="./media/automation-hybrid-runbook-worker/system-hybrid-workers-page.png" alt-text="Automation-fiókrendszer Hybrid Worker-csoportok lapja" border="false" lightbox="./media/automation-hybrid-runbook-worker/system-hybrid-workers-page.png":::

Ha több mint 2 000 hibrid feldolgozóval rendelkezik, az alábbi PowerShell-szkriptet futtathatja:

```powershell
"Get-AzSubscription -SubscriptionName "<subscriptionName>" | Set-AzContext
$workersList = (Get-AzAutomationHybridWorkerGroup -ResourceGroupName "<resourceGroupName>" -AutomationAccountName "<automationAccountName>").Runbookworker
$workersList | export-csv -Path "<Path>\output.csv" -NoClobber -NoTypeInformation"
```

## <a name="next-steps"></a>Következő lépések

* Ha szeretné megtudni, hogyan konfigurálhatja a runbookok a helyszíni adatközpontban vagy más felhőalapú környezetben lévő folyamatok automatizálására, tekintse meg a [Runbookok futtatása hibrid Runbook-feldolgozón](automation-hrw-run-runbooks.md)című témakört.

* A hibrid Runbook-feldolgozók hibaelhárításával kapcsolatos további információkért lásd: [hibrid Runbook-feldolgozói problémák elhárítása](troubleshoot/hybrid-runbook-worker.md#general).
