---
title: Az Azure Automation bemutatása
description: Ez a cikk azt ismerteti, hogy milyen Azure Automation és hogyan használható az infrastruktúra és az alkalmazások életciklusának automatizálására.
services: automation
ms.subservice: process-automation
keywords: Azure Automation, DSC, PowerShell, állapot-konfiguráció, Update Management, Change Tracking, DSC, leltár, runbookok, Python, grafikus
ms.date: 10/18/2018
ms.custom: mvc
ms.topic: overview
ms.openlocfilehash: e3986b7e8fc70f8662bed40b076897caa6165744
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96182818"
---
# <a name="an-introduction-to-azure-automation"></a>Az Azure Automation bemutatása

A Azure Automation felhőalapú automatizálási és konfigurációs szolgáltatást biztosít, amely támogatja az Azure-beli és nem Azure-beli környezetek egységes felügyeletét. Ez magában foglalja a folyamatok automatizálását, a konfiguráció felügyeletét, az Update managementet, a megosztott képességeket és a heterogén funkciókat. Az Automation szolgáltatás teljes körű irányítást biztosít a számítási feladatok és erőforrások üzembe helyezése, üzemeltetése és leszerelése során.

![Automatizálási képességek](media/automation-overview/automation-overview.png)

## <a name="process-automation"></a>Folyamatautomatizálás

A Azure Automation folyamatok automatizálása lehetővé teszi a gyakori, időigényes és a hibákra hajlamos felhőalapú felügyeleti feladatok automatizálását. Ez a szolgáltatás segít az üzleti értékek hozzáadásával kapcsolatos munkára koncentrálni. A hibák csökkentése és a hatékonyság növelése révén az üzemeltetési költségek is csökkennek. A folyamat-automatizálási operatív környezet részletesen szerepel a [Runbook végrehajtásában Azure Automationban](automation-runbook-execution.md).

A folyamatok automatizálása támogatja az Azure-szolgáltatások és más, a végpontok közötti folyamatok üzembe helyezéséhez, konfigurálásához és kezeléséhez szükséges nyilvános rendszerek integrálását. A szolgáltatás lehetővé teszi a [runbookok](automation-runbook-types.md) grafikusan, a PowerShellben vagy a Python használatával történő létrehozását. [Hibrid Runbook-feldolgozók](automation-hybrid-runbook-worker.md)használatával egyesítheti a felügyeletet a helyszíni környezetek összehangolása révén. A [webhookok](automation-webhooks.md) lehetővé teszik a kérések teljesítését, valamint a folyamatos teljesítést és működést a ITSM, a DevOps és a monitorozási rendszerek automatizálásának elindításával. 

## <a name="configuration-management"></a>Konfigurációkezelés

A Azure Automation konfigurációjának kezelése lehetővé teszi két szolgáltatás elérését:

* Change Tracking és Inventory
* Azure Automation State Configuration

### <a name="change-tracking-and-inventory"></a>Change Tracking és Inventory

A Change Tracking és a leltár ötvözi a Change Tracking and Inventory függvényeket, így nyomon követheti a virtuális gépek és a kiszolgálói infrastruktúra változásait. A szolgáltatás támogatja a változások nyomon követését a környezetben lévő szolgáltatásokon, démonokon, szoftvereken, beállításjegyzékeken és fájlokon, így segít a nemkívánatos módosítások diagnosztizálásában és a riasztások felemelésében. A leltár-támogatás lehetővé teszi a vendég erőforrásainak lekérdezését a telepített alkalmazások és egyéb konfigurációs elemek láthatóságának érdekében. A szolgáltatás részleteiért lásd: [change Tracking és leltár](change-tracking/overview.md).

### <a name="azure-automation-state-configuration"></a>Azure Automation State Configuration

[Azure Automation állapot-konfiguráció](automation-dsc-overview.md) egy felhőalapú szolgáltatás a PowerShell kívánt állapotának konfigurálásához (DSC), amely vállalati környezetekhez biztosít szolgáltatásokat. Ezzel a szolgáltatással felügyelheti a DSC-erőforrásait a Azure Automationban, és konfigurálhatja a virtuális vagy fizikai gépek konfigurációit az Azure-felhőben lévő DSC lekérési kiszolgálóról. 

## <a name="update-management"></a>Frissítéskezelés

A Azure Automation a Windows-és Linux-rendszerek [Update Management](./update-management/overview.md) funkcióját is tartalmazza hibrid környezetek között. A Update Management az Azure-ban és más felhőkben, illetve a helyszínen is elérhetővé teszi a frissítési megfelelőséget. A szolgáltatással olyan ütemezett központi telepítéseket hozhat létre, amelyek egy meghatározott karbantartási időszakon belül hangolják össze a frissítések telepítését. Ha egy frissítést nem kell telepíteni a gépre, Update Management funkcióval kizárhatja azt egy központi telepítésből.

## <a name="shared-capabilities"></a>Közös képességek

Azure Automation számos megosztott képességet kínál, többek között a megosztott erőforrásokat, a szerepköralapú hozzáférés-vezérlést, a rugalmas ütemezést, a verziókövetés integrálását, a naplózást és a címkézést.

### <a name="shared-resources"></a><a name="shared-resources"></a>Megosztott erőforrások

Az Azure Automation megosztott erőforrásainak segítségével könnyebben automatizálhatja és konfigurálhatja a nagy méretű környezeteket.

* **[Menetrendek](./shared-resources/schedules.md)** – automatizálási műveletek elindítása előre megadott időpontokban.
* **[Modulok](./shared-resources/modules.md)** – az Azure és más rendszerek kezelése. Modulokat importálhat a Microsoft, harmadik féltől származó, közösségi és egyéni által definiált parancsmagok és DSC-erőforrások Automation-fiókjába.
* **[Modulok gyűjteménye](automation-runbook-gallery.md)** – támogatja a natív integrációt a PowerShell-Galéria segítségével, így megtekintheti a runbookok, és importálhatja azokat az Automation-fiókba. A katalógus segítségével gyorsan megkezdheti a folyamatok integrálását és készítését a PowerShell-galériából és a Microsoft Script Centerből.
* **[Python 2 csomagok](python-packages.md)** – támogatja a Python 2 Runbookok az Automation-fiókhoz.
* **[Hitelesítő adatok](./shared-resources/credentials.md)** – biztonságosan tárolhatja azokat a bizalmas adatokat, amelyeket a runbookok és a konfigurációk használhatnak futásidőben.
* **[Kapcsolatok](automation-connections.md)** – a rendszerekkel létesített kapcsolatokra vonatkozó közös információk tároló név-érték párok. A modul szerzője definiálja a runbookok-ben és a konfigurációkban a futtatáskor használandó kapcsolatokat.
* **[Tanúsítványok](./shared-resources/certificates.md)** – a hitelesítéshez és a telepített erőforrások biztonságossá tételéhez használandó információk meghatározása a RUNBOOKOK vagy DSC-konfigurációk futásakor. 
* **[Változók](./shared-resources/variables.md)** – a runbookok és a konfigurációkon használható tartalmak tárolására szolgáló tartalom. A változók értékeit módosíthatja anélkül, hogy módosítani kellene a rájuk hivatkozó runbookok vagy konfigurációkat.

### <a name="role-based-access-control"></a>Szerepkör alapú hozzáférés-vezérlés

Azure Automation támogatja az Azure szerepköralapú hozzáférés-vezérlést (Azure RBAC) az Automation-fiókhoz és erőforrásaihoz való hozzáférés szabályozásához. További információ az Azure-RBAC az Automation-fiókon, a runbookok és a feladatokon való konfigurálásáról: [szerepköralapú hozzáférés-vezérlés a Azure Automationhoz](automation-role-based-access-control.md).

### <a name="source-control-integration"></a>Verziókövetés integrálása

Azure Automation támogatja a [verziókövetés integrálását](source-control-integration.md). Ez a funkció elősegíti a konfigurációt olyan kódban, amelyben a runbookok vagy a konfigurációk ellenőrizhetők egy verziókövetés rendszerbe.

## <a name="heterogeneous-support-windows-and-linux"></a>Heterogén támogatás (Windows és Linux)

Az automatizálás úgy lett kialakítva, hogy a hibrid felhőalapú környezet és a Windows-és Linux-rendszerek között is működjön. A szolgáltatás konzisztens módon automatizálja és konfigurálja a telepített számítási feladatokat és az azokat futtató operációs rendszereket.

## <a name="common-scenarios-for-automation"></a>Általános példák az Automation használatára

Azure Automation támogatja a felügyeletet az infrastruktúra és az alkalmazások életciklusa során. Gyakori forgatókönyvek például a következők:

* **Runbookok írása** – a PowerShell, a PowerShell-munkafolyamat, a grafikus, a Python 2 és a DSC runbookok közös nyelveken. 
* **Erőforrások létrehozása és üzembe helyezése** – virtuális gépek üzembe helyezése hibrid környezetekben runbookok és Azure Resource Manager-sablonok használatával. Integrálhatja a fejlesztési eszközöket, például a Jenkinst és az Azure DevOps-t.
* **Virtuális gépek konfigurálása** – a Windows és Linux rendszerű gépek felmérése és konfigurálása az infrastruktúra és az alkalmazás konfigurációjában.
* **Ossza meg tudását** – átviheti az ismereteket a rendszerbe, hogy a szervezet hogyan kézbesíti és fenntartsa a számítási feladatokat. 
* **Leltár beolvasása** – a megcélzott, jelentéskészítési és megfelelőségi üzembe helyezett erőforrások teljes leltárának beolvasása. 
* **Változások keresése** – azonosíthatja a helytelen konfigurációt okozó változásokat, és javíthatja a működési megfelelőséget.
* **Figyelő** – a gép olyan változásainak elkülönítése, amelyek problémákat okoznak, és a felügyeleti rendszereknek kiterjesztik vagy kibővítik azokat.
* **Védelem** – karanténba helyezheti a biztonsági riasztásokat. Megadhatja a vendégen futtatott rendszerekre vonatkozó követelményeket.
* **Szabályozás** – Azure-RBAC beállítása csapatokhoz. Visszanyerheti a nem használt erőforrásokat.

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="pricing-for-azure-automation"></a>A Azure Automation díjszabása

A [díjszabási](https://azure.microsoft.com/pricing/details/automation/) oldalon a Azure Automationhöz kapcsolódó árakat tekintheti át.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Automation-fiók létrehozása](automation-quickstart-create-account.md)