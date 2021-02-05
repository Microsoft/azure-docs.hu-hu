---
title: Az Azure Management áttekintése – Azure-irányítás
description: Az Azure-alkalmazások és-erőforrások kezelésével kapcsolatos területek áttekintése, amelyek az Azure felügyeleti eszközein található tartalmakra mutató hivatkozásokat tartalmaznak.
ms.date: 02/05/2021
ms.topic: overview
ms.openlocfilehash: 754fe496b790a7ee32eb840109a60a04ee66ef97
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2021
ms.locfileid: "99593925"
---
# <a name="what-are-the-azure-management-areas"></a>Mik az Azure felügyeleti területei?

Az Azure-beli irányítás az Azure-felügyelet egyik aspektusa. Ez a cikk az erőforrások Azure-beli üzembe helyezésére és karbantartására szolgáló különböző felügyeleti területeket ismerteti.

Felügyelet alatt azokat a feladatokat és folyamatokat értjük, amelyekre szükség van az üzleti alkalmazások és az azokat támogató erőforrások kezeléséhez. Az Azure számos szolgáltatással és eszközzel működik együtt a teljes körű felügyelet biztosításához. Ezek a szolgáltatások nem csak az Azure-beli erőforrásokhoz, hanem más felhőben és a helyszínen is elérhetők. A teljes felügyeleti környezet kialakításának első lépése a különböző eszközök és azok együttműködésének ismertetése.

A következő ábra azt illusztrálja, hogy milyen felügyeleti területekre van szükség az alkalmazások és erőforrások kezeléséhez. Ezek a különböző területek életciklusként is megtekinthetők. Minden területnek folyamatos egymásutánban kell lennie egy erőforrás élettartama alatt. Ez az erőforrás-életciklus a kezdeti üzembe helyezéssel, a folyamatos művelettel, végül pedig a kivonással kezdődik.

:::image type="complex" source="../monitoring/media/management-overview/management-capabilities.png" alt-text="Az Azure-beli felügyeleti szabályok diagramja." border="false":::
   Diagram, amely bemutatja az Azure-ban a felügyeletet és a szabályozást támogató szolgáltatások kerékének áttelepíteni, biztonságossá tételét, védelmét, figyelését, konfigurálását és irányítását. A biztonságos biztonsági felügyelet és veszélyforrások elleni védelem alárendelt elemekként szolgál. A védelem a biztonsági mentéssel és a vész-helyreállítással is rendelkezik alárendelt elemként. A figyelő az alkalmazással, az infra-és a hálózati figyeléssel, valamint a Log Analytics és a diagnosztika elemmel is rendelkezik. A konfigurálással a konfiguráció, az Update Management, az Automation és a parancsfájlok alárendelt elemekként jelennek meg. És az irányítás a szabályzatok kezelésével és a költséghatékonysággal rendelkezik alárendelt elemekként.
:::image-end:::

Egyetlen Azure-szolgáltatás sem tölti ki teljesen az adott felügyeleti terület követelményeit. Ehelyett több szolgáltatás is működik együtt. Egyes szolgáltatások, például a Application Insights, a webalkalmazások számára biztosítanak célként szolgáló figyelési funkciókat. Mások, például Azure Monitor naplók, más szolgáltatásokhoz tartozó felügyeleti adattárakat tárolnak. Ez a funkció lehetővé teszi a különböző szolgáltatások által gyűjtött különböző típusú adatok elemzését.

A következő szakaszok röviden ismertetik az egyes felügyeleti területeket, és az ezek kezelésére hivatott Azure-szolgáltatások részletes ismertetésére mutató hivatkozásokat adnak meg.

## <a name="monitor"></a>Monitor

A monitorozás az adatok összegyűjtésére és elemzésére szolgál az erőforrások teljesítményének, állapotának és rendelkezésre állásának naplózása érdekében. Egy hatékony figyelési stratégia segíti az összetevők működésének megértését és az értesítésekkel való üzemidő növelését. A monitoring áttekintése, amely az [Azure-alkalmazások és-erőforrások figyeléséhez](../azure-monitor/overview.md)használt különböző szolgáltatásokat fedi le.

## <a name="configure"></a>Konfigurálás

A configure az erőforrások kezdeti üzembe helyezésére és konfigurálására, valamint a folyamatos karbantartásra utal.
Ezeknek a feladatoknak az automatizálásával elkerülhető a redundancia, ami minimalizálja az időt és a fáradságot, és növeli a pontosságot és a hatékonyságot. Az [Azure Automation](../automation/automation-intro.md) tartalmazza azon szolgáltatások nagy részét, amelyek a konfigurációs feladatok automatizálásához szükségesek. Míg a runbookok kezeli a folyamatok automatizálását, a konfiguráció és az Update Management segítségével kezelheti a konfigurációt.

## <a name="govern"></a>Irányítás

Az irányítás azokat a mechanizmusok és folyamatokat biztosítja, amelyekre az Azure-beli alkalmazások és erőforrások szabályozásához van szükség. Magában foglalja a kezdeményezések megtervezését és a stratégiai prioritások meghatározását.
Az Azure-ban az irányítás implementálásának feladatát nagyrészt két szolgáltatás végzi el. [Azure Policy](./policy/overview.md) lehetővé teszi a szabályzat-definíciók létrehozását, hozzárendelését és kezelését az erőforrások szabályainak érvényesítéséhez.
Ez a szolgáltatás a vállalati szabványoknak megfelelően tartja meg ezeket az erőforrásokat.
A [Azure Cost Management](../cost-management-billing/cost-management-billing-overview.md) segítségével nyomon követheti az Azure-erőforrások és más felhőalapú szolgáltatók Felhőbeli használatát és kiadásait.

## <a name="secure"></a>Biztonságos

Kezelheti erőforrásai és adatai biztonságát. A biztonsági program magában foglalja a fenyegetések felmérését, az adatok gyűjtését és elemzését, valamint az alkalmazások és erőforrások megfelelőségét. A biztonsági monitorozási és a veszélyforrások elemzését [Azure Security Center](../security-center/security-center-introduction.md)biztosítja, amely magában foglalja az egységes biztonsági felügyeletet és az összetett veszélyforrások elleni védelmet a hibrid Felhőbeli számítási feladatokban. Lásd: az Azure [Security bemutatása](../security/fundamentals/overview.md) – átfogó információk és útmutatás az Azure-erőforrások biztonságossá tételéhez.

## <a name="protect"></a>Védelem

A védelem arra utal, hogy az alkalmazások és az adatszolgáltatások elérhetők maradnak, még a szabályozáson kívüli kimaradások esetén is. Az Azure-ban erről a védelemről két szolgáltatás gondoskodik. Az [Azure Backup](../backup/backup-overview.md) helyreállítható biztonsági másolatokat készít az adatokról, és azokat a felhőben vagy helyszíni gépeken tárolja. A [Azure site Recovery](../site-recovery/site-recovery-overview.md) az üzletmenet folytonosságát és az azonnali helyreállítást biztosítja a katasztrófák során.

## <a name="migrate"></a>Migrate

A migrálás a helyszíni gépen futó számítási feladatok átvitelét jelenti az Azure-fehőbe.
A [Azure Migrate](../migrate/migrate-services-overview.md) egy olyan szolgáltatás, amely segít a helyszíni virtuális gépek Azure-ba való áttelepítésének értékelésében. [A Azure site Recovery a](../site-recovery/migrate-tutorial-on-premises-azure.md) virtuális gépeket a helyszíni rendszerből vagy [Amazon Web Servicesból](../site-recovery/migrate-tutorial-aws-azure.md)telepíti át. Az [Azure Database Migration](../dms/dms-overview.md) segítséget nyújt az adatbázis-források Azure-beli adatplatformokra való áttelepítésében.

## <a name="next-steps"></a>Következő lépések

Az Azure irányításával kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

- Lásd: az [Azure irányítási központja](./index.yml).
- Tekintse [meg az Azure-beli Cloud bevezetési keretrendszer irányítását](/azure/cloud-adoption-framework/govern/)