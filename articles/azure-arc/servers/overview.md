---
title: Azure Arc engedélyezett kiszolgálók áttekintése
description: Megtudhatja, hogyan kezelheti az Azure Azure Arc n kívül üzemeltetett kiszolgálókat egy Azure-erőforráshoz hasonló, engedélyezett kiszolgálókkal.
keywords: azure automation, DSC, powershell, célállapot-konfiguráció, frissítéskezelés, változáskövetés, leltár, runbookok, python, grafikus, hibrid
ms.date: 04/21/2021
ms.topic: overview
ms.openlocfilehash: 324f6cc29bd9e4eca1a20413032c213c2618a11e
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831995"
---
# <a name="what-is-azure-arc-enabled-servers"></a>Mi azok az Azure Arc-kompatibilis kiszolgálók?

Azure Arc-kompatibilis kiszolgálók lehetővé teszik a Windows és Linux rendszerű  fizikai kiszolgálók és az Azure-on kívül, a vállalati hálózaton vagy más felhőszolgáltatón üzemeltetett virtuális gépek kezelését. Ez a felügyeleti élmény úgy lett kialakítva, hogy összhangban legyen a natív Azure-beli virtuális gépek kezelésével. Amikor egy hibrid gép csatlakozik az Azure-hoz, csatlakoztatott gép lesz, és erőforrásként lesz kezelve az Azure-ban. Minden csatlakoztatott gép rendelkezik egy erőforrás-azonosítóval, amely egy erőforráscsoportba tartozik, és ki van használva a szabványos Azure-szerkezetek, például a Azure Policy címkék alkalmazása. Az ügyfelek helyszíni infrastruktúráját felügyelő szolgáltatók a maihoz hasonló natív Azure-erőforrásokkal is kezelhetik a [](../../lighthouse/how-to/manage-hybrid-infrastructure-arc.md) hibrid gépeiket több ügyfélkörnyezetben, Azure Lighthouse és Azure Arc.

Ahhoz, hogy ezt a élményt az Azure-on kívül üzemeltetett hibrid gépekkel is biztosítsa, a Azure Connected Machine-ügynököt minden olyan gépre telepítenie kell, amelyről csatlakozni tervez az Azure-hoz. Ez az ügynök semmilyen más funkciót nem nyújt, és nem helyettesíti az Azure [Log Analytics-ügynököt.](../../azure-monitor/agents/log-analytics-agent.md) A Windowshoz és Linuxhoz használható Log Analytics-ügynökre akkor van szükség, ha proaktívan szeretné figyelni a gépen futó operációs rendszert és számítási feladatokat, Automation-runbookokkal vagy megoldásokkal (például Update Management) kezelni, vagy más Azure-szolgáltatásokat, például a [Azure Security Center.](../../security-center/security-center-introduction.md)

## <a name="supported-scenarios"></a>Támogatott esetek

Ha a gépet egy engedélyezett Azure Arc csatlakoztatja, az lehetővé teszi a következő konfigurációkezelési és figyelési feladatok elvégzését:

- Rendeljen [Azure Policy vendégkonfigurációkat](../../governance/policy/concepts/guest-configuration.md) ugyanazokkal a felhasználói élményekkel, mint az Azure-beli virtuális gépek szabályzat-hozzárendelése. Napjainkban a legtöbb vendégkonfigurációs szabályzat nem alkalmaz konfigurációkat, csak a gépen belüli beállításokat naplóz. A vendégkonfigurációs szabályzatok Arc-kompatibilis Azure Policy való használatának költségeiről a Azure Policy [útmutatóban található.](https://azure.microsoft.com/pricing/details/azure-policy/)

- Jelentés a telepített szoftverek, a Microsoft-szolgáltatások, a Windows beállításjegyzék és fájlok, valamint a figyelt kiszolgálók linuxos [](../../security-center/security-center-file-integrity-monitoring.md)démonai konfigurációjának változásairól az Azure Automation [változáskövetés és leltározás](../../automation/change-tracking/overview.md) és Azure Security Center fájlintegritás monitorozását használó kiszolgálókon az Azure Defender kiszolgálókkal engedélyezett kiszolgálók [esetén.](../../security-center/defender-for-servers-introduction.md)

- Figyelje a csatlakoztatott gép vendég operációs rendszerének teljesítményét, és fedezze fel az alkalmazás-összetevőket a folyamatuk és függőségeik figyeléséhez az alkalmazás által a VM-elemzésekkel [kommunikált más erőforrásokkal.](../../azure-monitor/vm/vminsights-overview.md)

- Leegyszerűsítheti az üzembe helyezést más Azure-szolgáltatások, például a Azure Automation [State Configuration](../../automation/automation-dsc-overview.md) és Azure Monitor Log Analytics-munkaterület használatával, a nem Azure-beli Windows vagy Linux rendszerű gépek támogatott [Azure-beli](manage-vm-extensions.md) virtuálisgép-bővítményei segítségével. Ez magában foglalja az üzembe helyezés utáni konfigurációt vagy az egyéni szkriptbővítmény használatával való szoftvertelepítést.

- A [Update Management](../../automation/update-management/overview.md) a Azure Automation windowsos és Linux-kiszolgálók operációsrendszer-frissítésének kezeléséhez

    > [!NOTE]
    > Jelenleg nem támogatott a Update Management arc-kompatibilis kiszolgálóról való közvetlen engedélyezése. A [követelményeket Update Management](../../automation/update-management/enable-from-automation-account.md) és a kiszolgáló számára való engedélyezését lásd: Enable Update Management from your Automation account (Naplózás engedélyezése az Automation-fiókból).

- A fenyegetések észleléséhez és a potenciális biztonsági fenyegetések proaktív figyeléséhez használja a nem Azure-beli kiszolgálókat a [Azure Security Center.](../../security-center/security-center-introduction.md)

A hibrid gépről egy Log Analytics-munkaterületen gyűjtött és tárolt naplóadatok mostantól a gépre jellemző tulajdonságokat, például egy erőforrás-azonosítót tartalmaznak. Ez az erőforrás-környezet [naplóelérésének](../../azure-monitor/logs/design-logs-deployment.md#access-mode) támogatására használható.

[!INCLUDE [azure-lighthouse-supported-service](../../../includes/azure-lighthouse-supported-service.md)]

## <a name="supported-regions"></a>Támogatott régiók

Az engedélyezett kiszolgálókkal rendelkező támogatott régiók Azure Arc tekintse meg az Azure-termékek régiók szerint [oldalt.](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc)

A legtöbb esetben a telepítési szkript létrehozásakor kiválasztott helynek a számítógép helyéhez földrajzilag legközelebbi Azure-régiónak kell lennie. Az adatok az Ön által megadott régiót tartalmazó Azure-beli földrajzi helyen vannak tárolva, ami hatással lehet a választott régióra, ha adateltározási követelményekkel rendelkezik. Ha a géphez csatlakozó Azure-régiót egy szolgáltatáskimaradás érinti, a csatlakoztatott gépre ez nem lesz hatással, de előfordulhat, hogy az Azure-t használó felügyeleti műveleteket nem lehet befejezni. Ha regionális kimaradás van, és több olyan helyről rendelkezik, amelyek támogatják a földrajzilag redundáns szolgáltatást, akkor a legjobb, ha az egyes helyeken található gépeket egy másik Azure-régióhoz csatlakoztatja.

A csatlakoztatott gép alábbi metaadat-információit a rendszer abban a régióban gyűjti és tárolja, Azure Arc gép erőforrása konfigurálva van:

- Operációs rendszer neve és verziója
- Számítógép neve
- Számítógép teljes tartományneve (FQDN)
- Csatlakoztatott gép ügynökének verziója

Ha például a gép regisztrálva van az USA Azure Arc régióban található régióban, akkor az adatok az USA régiójában tárolódnak.

### <a name="supported-environments"></a>Támogatott környezetek

Az Arc-kompatibilis kiszolgálók támogatják az Azure-ban üzemeltetett fizikai kiszolgálók és *virtuális* gépek kezelését. A virtuális gépeket üzemeltető hibrid felhőkörnyezetek konkrét részleteiért lásd: Connected Machine agent prerequisites (A csatlakoztatott gép [ügynökének előfeltételei).](agent-overview.md#supported-environments)

> [!NOTE]
> Az Arc-kompatibilis kiszolgálók nem az Azure-ban futó virtuális gépek felügyeletének engedélyezéséhez vannak kialakítva vagy támogatva.

### <a name="agent-status"></a>Ügynök állapota

A Csatlakoztatott gép ügynök 5 percenként rendszeres szívverési üzenetet küld a szolgáltatásnak. Ha a szolgáltatás nem kap szívverési üzeneteket egy gépről, a rendszer  offline állapotúnak minősül, és az állapot 15–30 percen belül automatikusan Leválasztva állapotra vált a portálon. Amikor a Csatlakoztatott gép ügynöktől egy későbbi szívverési üzenetet kap, az állapota automatikusan Csatlakoztatva **állapotra változik.**

## <a name="next-steps"></a>Következő lépések

Mielőtt kiértékelné vagy engedélyezné az Arc-kompatibilis kiszolgálókat több hibrid gépen, tekintse át a Csatlakoztatottgép-ügynök áttekintése témakört a követelmények, az ügynök technikai részletei és az üzembe helyezési módszerek áttekintéséhez. [](agent-overview.md)