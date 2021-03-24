---
title: Az Azure arc használatára képes kiszolgálók áttekintése
description: Ismerje meg, hogyan használhatja az Azure arc-kompatibilis kiszolgálókat az Azure-on kívül üzemeltetett kiszolgálók (például Azure-erőforrások) kezelésére.
keywords: Azure Automation, DSC, PowerShell, a kívánt állapot konfigurálása, frissítés kezelése, változások követése, leltár, runbookok, Python, grafikus, hibrid
ms.date: 02/18/2021
ms.topic: overview
ms.openlocfilehash: 863cab073018c5a592cba6e94451a9af038ebd52
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "105023333"
---
# <a name="what-is-azure-arc-enabled-servers"></a>Mi azok az Azure Arc-kompatibilis kiszolgálók?

Az Azure arc-kompatibilis kiszolgálók lehetővé teszik az Azure-on *kívül* üzemeltetett Windows-és Linux-alapú fizikai kiszolgálók és virtuális gépek felügyeletét a vállalati hálózaton vagy más felhőalapú szolgáltatón. Ez a kezelési megoldás úgy lett kialakítva, hogy konzisztens legyen a natív Azure-beli virtuális gépek kezelésével. Ha egy hibrid gép csatlakozik az Azure-hoz, az egy csatlakoztatott gép lesz, és erőforrásként van kezelve az Azure-ban. Minden csatlakoztatott számítógép rendelkezik erőforrás-AZONOSÍTÓval, amely egy erőforráscsoport részét képezi, és a szabványos Azure-konstrukciók, például a Azure Policy és a címkék alkalmazásának előnyei. Az ügyfél helyszíni infrastruktúráját kezelő szolgáltatók a hibrid gépeket ugyanúgy kezelhetik, mint a natív Azure-erőforrásokkal, több ügyfél-környezetben, az [Azure Lighthouse](../../lighthouse/how-to/manage-hybrid-infrastructure-arc.md) és az Azure arc használatával.

Ahhoz, hogy ezt a szolgáltatást az Azure-on kívül üzemeltetett hibrid gépekkel is el lehessen juttatni, az Azure-hoz csatlakoztatott számítógép-ügynököt minden olyan gépre telepíteni kell, amelyet az Azure-hoz való csatlakozásra tervez. Ez az ügynök nem nyújt semmilyen egyéb funkciót, és nem helyettesíti az Azure [log Analytics Agent ügynököt](../../azure-monitor/agents/log-analytics-agent.md). A Windows és Linux rendszerhez készült Log Analytics-ügynökre akkor van szükség, ha proaktívan szeretné figyelni a gépen futó operációs rendszert és munkaterheléseket, felügyelheti azt automatizálási runbookok vagy olyan megoldások használatával, mint például a Update Management, vagy más Azure-szolgáltatásokat is használhat, mint például a [Azure Security Center](../../security-center/security-center-introduction.md).

## <a name="supported-scenarios"></a>Támogatott esetek

Ha a gépet az Azure arc-kompatibilis kiszolgálókhoz kapcsolja, lehetővé teszi a következő konfigurálási és figyelési feladatok végrehajtását:

- Az Azure Virtual Machines szolgáltatáshoz tartozó szabályzat-hozzárendeléssel megegyező módon rendeljen hozzá [Azure Policy vendég konfigurációkat](../../governance/policy/concepts/guest-configuration.md) . Napjainkban a legtöbb vendég konfigurációs házirend nem alkalmaz konfigurációkat, csak a számítógépen lévő beállításokat naplózzák. Az arc-kompatibilis kiszolgálókkal Azure Policy vendég konfigurációs szabályzatok használatának költségét az Azure Policy [díjszabási útmutatójában](https://azure.microsoft.com/pricing/details/azure-policy/)találja.

- A telepített szoftverekkel, a Microsoft-szolgáltatásokkal, a Windows-beállításjegyzékkel és-fájlokkal, valamint a felügyelt kiszolgálókon futó Linux-démonokkal kapcsolatos konfigurációs változásokat a Azure Automation [change Tracking és a leltár](../../automation/change-tracking/overview.md) és a [Azure Security Center fájl integritásának figyelése](../../security-center/security-center-file-integrity-monitoring.md)használatával végezheti el a kiszolgálókon az [Azure Defender](../../security-center/defender-for-servers-introduction.md)

- Figyelheti a csatlakoztatott számítógép vendég operációs rendszerének teljesítményét, és felderítheti az alkalmazás-összetevőket, hogy a folyamatait és függőségeit a [Azure monitor for VMS](../../azure-monitor/vm/vminsights-overview.md)használatával kommunikáló más erőforrásokkal figyelje.

- Egyszerűsítse az üzembe helyezést más Azure-szolgáltatásokkal, például az Azure Automation [állapot-konfigurációval](../../automation/automation-dsc-overview.md) és Azure monitor log Analytics munkaterülettel a nem Azure-beli Windows-vagy Linux-alapú Azure virtuálisgép- [bővítmények](manage-vm-extensions.md) használatával. Ez magában foglalja az üzembe helyezés utáni konfiguráció vagy a Szoftvertelepítés egyéni parancsfájl-bővítmény használatával végzett telepítését is.

- [Update Management](../../automation/update-management/overview.md) használata Azure Automation a Windows-és Linux-kiszolgálók operációsrendszer-frissítéseinek kezeléséhez

    > [!NOTE]
    > Jelenleg a Update Management engedélyezése közvetlenül egy ív-kompatibilis kiszolgálóról nem támogatott. A követelmények megismeréséhez és a kiszolgáló engedélyezéséhez lásd: [Update Management engedélyezése az Automation-fiókból](../../automation/update-management/enable-from-automation-account.md) .

- Vegye fel a nem Azure-kiszolgálókat a fenyegetések észlelésére, és proaktívan figyelje az esetleges biztonsági fenyegetéseket a [Azure Security Center](../../security-center/security-center-introduction.md)használatával.

A hibrid gépről Log Analytics munkaterületen gyűjtött és tárolt adatok naplózása mostantól a géppel kapcsolatos tulajdonságokat is tartalmaz, például egy erőforrás-azonosítót. Ezt az [erőforrás-környezet](../../azure-monitor/logs/design-logs-deployment.md#access-mode) naplózási hozzáférésének támogatásához használhatja.

[!INCLUDE [azure-lighthouse-supported-service](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="supported-regions"></a>Támogatott régiók

Az Azure arc-kompatibilis kiszolgálókkal rendelkező támogatott régiók végleges listáját az Azure- [termékek régiónként](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc) oldalon tekintheti meg.

A legtöbb esetben a telepítési parancsfájl létrehozásakor kiválasztott helyen kell lennie az Azure-régiónak, amelynek földrajzilag legközelebb kell lennie a számítógép helyéhez. Az inaktív adatok tárolása az Ön által megadott régiót tartalmazó Azure-földrajzon belül történik, amely az Ön által választott régióra is hatással lehet, ha rendelkezik az adatok tartózkodási feltételeivel. Ha az Azure-régió, amellyel a gépet csatlakoztatja, a rendszer áramszünetet érint, a csatlakoztatott gép nem lesz hatással, de előfordulhat, hogy az Azure-t használó felügyeleti műveletek nem lesznek végrehajtva. Ha van regionális leállás, és ha több hellyel is rendelkezik, amelyek támogatják a földrajzilag redundáns szolgáltatást, akkor a legjobb, ha az egyes helyekhez tartozó gépeket egy másik Azure-régióhoz kapcsolja.

A rendszer a csatlakoztatott géppel kapcsolatos következő metaadatokat gyűjti és tárolja abban a régióban, ahol az Azure arc machine-erőforrás konfigurálva van:

- Operációs rendszer neve és verziója
- Számítógép neve
- Számítógép teljesen minősített tartományneve (FQDN)
- Csatlakoztatott gépi ügynök verziója

Ha például a gép regisztrálva van az Azure arc-ban az USA keleti régiójában, a rendszer ezeket az adatterületet az Egyesült Államok régiójában tárolja.

### <a name="supported-environments"></a>Támogatott környezetek

Az ív használatára képes kiszolgálók támogatják az Azure-on *kívül* üzemeltetett fizikai kiszolgálók és virtuális gépek kezelését. A virtuális gépeket üzemeltető hibrid felhőalapú környezetek részletes ismertetését lásd: a [csatlakoztatott gép ügynökének előfeltételei](agent-overview.md#supported-environments).

> [!NOTE]
> Az ív használatára képes kiszolgálók nem lettek kialakítva és nem támogatottak az Azure-ban futó virtuális gépek kezelésének engedélyezéséhez.

### <a name="agent-status"></a>Ügynök állapota

A csatlakoztatott gépi ügynök egy normál szívverésű üzenetet küld a szolgáltatásnak 5 percenként. Ha a szolgáltatás nem tudja fogadni ezeket a szívverési üzeneteket a gépről, a gép offline állapotba kerül, és a rendszer 15 – 30 percen belül automatikusan **megszakítja a kapcsolatot** a portálon. Amikor egy későbbi szívverési üzenetet kap a csatlakoztatott számítógép ügynökéről, az állapota automatikusan **csatlakoztatva** lesz.

## <a name="next-steps"></a>Következő lépések

Az arc-kompatibilis kiszolgálók több hibrid gépen való kiértékelése vagy engedélyezése előtt tekintse át a [csatlakoztatott gép ügynökének áttekintése című témakört](agent-overview.md) a követelmények megismeréséhez, az ügynök műszaki részleteihez és a telepítési módszerekhez.