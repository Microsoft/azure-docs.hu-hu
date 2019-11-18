---
title: Az Azure Management áttekintése – Azure-irányítás
description: A felügyeleti területeket, az Azure-alkalmazások és erőforrások az Azure felügyeleti eszközökhöz a tartalmakra mutató hivatkozásokat áttekintése.
ms.date: 12/06/2018
ms.topic: overview
ms.openlocfilehash: f91e439a5fb7c330fb29d300e83779083641875b
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/16/2019
ms.locfileid: "74132842"
---
# <a name="overview-of-management-services-in-azure"></a>Az Azure-beli felügyeleti szolgáltatások áttekintése

Irányítás az Azure-ban az Azure-felügyelet egyik formája. Ez a cikk ismerteti a különböző felügyeleti területeket, üzembe helyezése és karbantartása az Azure-erőforrások számára.

Felügyelet alatt azokat a feladatokat és folyamatokat értjük, amelyekre szükség van az üzleti alkalmazások és az azokat támogató erőforrások kezeléséhez. Az Azure számos olyan szolgáltatások és eszközök, amelyek együttműködve a teljes körű felügyeletet biztosítanak rendelkezik. Ezek a szolgáltatások csak az Azure-ban, hanem más felhőkben és a helyszíni erőforrások esetében nem. A különböző eszközök, és hogyan működnek együtt egy teljes körű felügyeleti környezet kialakításának első lépése.

A következő ábra azt illusztrálja, hogy milyen felügyeleti területekre van szükség az alkalmazások és erőforrások kezeléséhez. Ezekre a területekre egy életciklus, tekinthető. Minden terület gondolhatunk, egy erőforráscsoport szükséges folyamatos egymás után. Az erőforrás életciklusának kezdődik a kezdeti üzembe helyezést a folyamatos művelet, végül pedig ha már nincs.

![Az Azure-beli felügyeleti szabályok](../monitoring/media/management-overview/management-capabilities.png)

Nincs olyan Azure-szolgáltatás teljesen beírja egy adott felügyeleti területen követelményeinek. Ehelyett minden egyes van, hogy több szolgáltatás együttes használata által. Bizonyos szolgáltatásokhoz, például az Application Insights, adja meg a webes alkalmazásokhoz célzott figyelési funkciókat. Mások, például Azure Monitor naplók, más szolgáltatásokhoz tartozó felügyeleti adattárakat tárolnak. Ez a funkció lehetővé teszi a különböző szolgáltatások által gyűjtött különböző típusú adatok elemzéséhez.

A következő szakaszok röviden ismertetik az egyes felügyeleti területeket, és az ezek kezelésére hivatott Azure-szolgáltatások részletes ismertetésére mutató hivatkozásokat adnak meg.

## <a name="monitor"></a>Monitoring

Figyelés gyűjtése és adatok naplózása, a teljesítményét, állapotát és az erőforrások rendelkezésre állásának elemzése. Egy hatékony monitorozási stratégia segít megérteni a összetevők, és növelni az üzemidőt értesítésekkel a műveletet. Olvasási figyelését, amely ismerteti a különböző szolgáltatások nagy mennyiségben használta [figyelése az Azure-alkalmazások és erőforrások](../monitoring/monitoring-overview.md).

## <a name="configure"></a>Konfigurálás

Konfigurálja a kezdeti telepítés és konfiguráció az erőforrások és folyamatos karbantartást hivatkozik.
Ezek a feladatok automatizálását a redundancia, minimálisra csökkentik az idő és munka, és a pontosság és a hatékonyság növelése kiküszöbölése teszi lehetővé. Az [Azure Automation](../automation/automation-intro.md) tartalmazza azon szolgáltatások nagy részét, amelyek a konfigurációs feladatok automatizálásához szükségesek. Bár a runbookok folyamatautomatizálási kezelni, konfiguráció- és konfigurációjának kezelésével segítik.

## <a name="govern"></a>Irányítás

Az irányítás azokat a mechanizmusok és folyamatokat biztosítja, amelyekre az Azure-beli alkalmazások és erőforrások szabályozásához van szükség. Magában foglalja a kezdeményezések megtervezését és a stratégiai prioritások meghatározását.
Az Azure-ban az irányítás implementálásának feladatát nagyrészt két szolgáltatás végzi el. [Az Azure Policy](./policy/overview.md) hozhat létre, rendelje hozzá és kezelhet szabályzatdefiníciókat az erőforrások szabályok érvényesítése. Ez a funkció biztosítja, hogy ezek az erőforrások megfelelnek-e a vállalati szabványoknak. A [Azure Cost Management](../cost-management/overview-cost-mgt.md) segítségével nyomon követheti az Azure-erőforrások és más felhőalapú szolgáltatók Felhőbeli használatát és kiadásait.

## <a name="secure"></a>Biztonságos

Kezelheti az erőforrásokat és az adatok biztonságát. Egy biztonsági program magában foglalja a fenyegetéseket, gyűjtése és elemzése az adatokat, és az alkalmazások és erőforrások megfelelőségének felméréséhez. Biztonsági figyelési és kockázati elemzések által biztosított [az Azure Security Center](../security-center/security-center-intro.md), foglalkozik, amely egységes biztonsági felügyeletet és fejlett fenyegetésvédelmet a hibrid felhőalapú számítási feladatokhoz. Lásd: [bemutatása az Azure Security](../security/fundamentals/overview.md) átfogó információkat és útmutatást az Azure-erőforrások biztosítása.

## <a name="protect"></a>Védelem

Védelem gondoskodik az alkalmazások és adatok érhető el, még akkor is, amelyek Öntől leállások hivatkozik. Az Azure-ban erről a védelemről két szolgáltatás gondoskodik. Az [Azure Backup](../backup/backup-introduction-to-azure-backup.md) helyreállítható biztonsági másolatokat készít az adatokról, és azokat a felhőben vagy helyszíni gépeken tárolja. [Az Azure Site Recovery](../site-recovery/site-recovery-overview.md) biztosítja az üzletmenet-folytonossági és az azonnali katasztrófa során.

## <a name="migrate"></a>Migrate (Áttelepítés)

A migrálás a helyszíni gépen futó számítási feladatok átvitelét jelenti az Azure-fehőbe.
[Az Azure Migrate](../migrate/migrate-overview.md) egy szolgáltatás, amellyel felmérheti a migrálásra való alkalmasságát, a helyszíni virtuális gépek az Azure-bA. Az Azure Site Recovery áttelepíti a virtuális gépek [helyi](../site-recovery/migrate-tutorial-on-premises-azure.md) vagy [Amazon Web servicesből](../site-recovery/migrate-tutorial-aws-azure.md). [Azure Database Migration](../dms/dms-overview.md) áttelepítése adatbázisforrásokból az Azure-adatplatformokra nyújt segítséget.
