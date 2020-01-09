---
title: Az Azure Automation áttekintése
description: Útmutató az Azure Automation az infrastruktúra és az alkalmazások életciklusának automatizálásához történő használatához.
services: automation
ms.subservice: process-automation
keywords: azure automation, DSC, powershell, desired state configuration, frissítéskezelés, változáskövetés, leltár, runbookok, python, grafikus
ms.date: 10/18/2018
ms.custom: mvc
ms.topic: overview
ms.openlocfilehash: 3037f96464b0902ca3923bdaea2bddff39c47733
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75421944"
---
# <a name="an-introduction-to-azure-automation"></a>Az Azure Automation bemutatása

Az Azure Automation olyan felhőalapú automatizálási és konfigurációs szolgáltatást kínál, amely a teljes Azure-beli és nem Azure-beli környezetre kiterjedően egységes módon teszi lehetővé a felügyeletet és kezelést. Folyamatautomatizálást, frissítéskezelést és konfigurálási szolgáltatásokat foglal magában. Az Azure Automation teljes körű felügyeletet biztosít a számítási feladatok és erőforrások üzembe helyezése, üzemeltetése és leszerelése során.
Ez a cikk röviden áttekinti az Azure Automationt, valamint választ ad néhány gyakran felmerülő kérdésre. A különböző képességekkel kapcsolatos további információért kattintson a jelen áttekintésben található hivatkozásokra.

## <a name="azure-automation-capabilities"></a>Az Azure Automation képességei

![Az Automation képességeinek áttekintése](media/automation-overview/automation-overview.png)

### <a name="process-automation"></a>Folyamatautomatizálás

Az Azure Automation segítségével automatizálhatja a gyakori, időigényes és hibalehetőségekkel teli felhőfelügyeleti feladatokat. Az automatizálásnak köszönhetően így azokra a feladatokra koncentrálhat, amelyek valóban üzleti értéket teremtenek. A hibaarány csökkenése és a hatékonyság növelése az üzemeltetési költségek csökkentéséhez is hozzájárul. A folyamatok teljes körű üzembe helyezéséhez, konfigurálásához és felügyeletéhez szükséges Azure-szolgáltatások és egyéb nyilvános rendszerek integrálhatók. A szolgáltatás segítségével grafikus úton [hozhat létre runbookokat](automation-runbook-types.md) PowerShellben vagy Pythonban. Hibrid runbook-feldolgozók használatával és a helyszíni környezetek irányításának összevonásával egységesítheti a felügyeleti tevékenységeket. A [webhookok](automation-webhooks.md) biztosítják a kérések teljesítését és a folyamatos szolgáltatást és üzemeltetést az ITSM-, DevOps- és monitorozási rendszerek automatizált folyamatainak aktiválásával.

### <a name="configuration-management"></a>Konfigurációkezelés

Az Azure Automation [desired state configuration](automation-dsc-overview.md) egy felhőalapú megoldás a PowerShell DSC-hez, amely vállalati környezetekhez szükséges szolgáltatásokat biztosít. A DSC-erőforrásait kezelheti az Azure Automationben, és konfigurációkat alkalmazhat a virtuális és fizikai gépekre az Azure-felhőben lévő DSC lekérési kiszolgálóról. Gazdag jelentések készíthetők, amelyek tájékoztatást adnak olyan fontos eseményekről, mint például amikor a csomópontok eltérnek a hozzájuk rendelt konfigurációtól. Monitorozhatja és automatizálhatja a gépkonfigurációkat a fizikai és virtuális gépeken, Windows vagy Linux rendszeren, felhőbeli vagy helyszíni környezetekben egyaránt.

A vendégen futtatott erőforrások leltáradatai révén betekintést nyerhet a telepített alkalmazásokról és egyéb konfigurációs elemekről. A rendelkezésre álló gazdag jelentéskészítési és keresési funkciókkal gyorsan lekérhető részletes információk segítségével könnyedén áttekinthető az operációs rendszerek konfigurációja. Nyomon követhetők a szolgáltatások, démonok, szoftverek, beállításjegyzékek és fájlok változásai, így gyorsan azonosítható, hogy mi okozza a hibákat. Emellett a DSC képes diagnosztizálni és jelezni a környezet nem kívánt változásait.

### <a name="update-management"></a>Frissítéskezelés

Az Azure Automationnel frissítheti a windowsos és linuxos rendszereket a hibrid környezetekben. Így láthatóvá válik a frissítési megfelelőség az Azure-beli, a helyszíni és egyéb felhőkben. Ütemezett üzembe helyezéseket hozhat létre a frissítések telepítésének egy adott karbantartási időablakban való végrehajtatásához. Ha egy adott frissítést nem szeretne telepíteni a gépekre, kizárhatja azt az üzembe helyezésből.

### <a name="shared-resources"></a>Megosztott erőforrások

Az Azure Automation megosztott erőforrásainak segítségével könnyebben automatizálhatja és konfigurálhatja a nagy méretű környezeteket.

* **[Ütemezések](automation-schedules.md)** – Az ütemezések használatával az automatizmusok előre meghatározott időpontokban aktiválhatók.
* **[Modulok](automation-integration-modules.md)** – a modulok az Azure és más rendszerek felügyeletére szolgálnak. Importálja a Microsoft, harmadik fél, Közösség vagy egyéni meghatározott parancsmagok és DSC-erőforrások Automation-fiókjába.
* **[Modulok gyűjteménye](automation-runbook-gallery.md)** – a PowerShell-Galéria natív integrációja a runbookok megtekintéséhez és az Automation-fiókba való importálásához.
* **[Python 2 csomagok](python-packages.md)** – a Python-Runbookok használható Python 2-csomagok hozzáadása az Automation-fiókhoz.
* **[Hitelesítő adatok](automation-credentials.md)** – A runbookokban és konfigurációkban használt bizalmas adatok biztonságosan tárolhatók.
* **[Kapcsolatok](automation-connections.md)** – Gyakori adatokat tartalmazó név-érték adatpárokat tárolhat a kapcsolati erőforrásokban lévő rendszerekhez való kapcsolódáshoz. A kapcsolatokat a modul létrehozója definiálja a runbookokban és konfigurációkban való futásidejű használatra.
* **[Tanúsítványok](automation-certificates.md)** – A tanúsítványok tárolhatók, és futásidőben elérhetővé tehetők, így hitelesítésre és az üzembe helyezett erőforrások védelmére használhatók.
* **[Változók](automation-variables.md)** – A változókban olyan tartalmakat tárolhat, amelyek több runbookban és konfigurációban is felhasználhatók. Az értékeket anélkül módosíthatja, hogy szerkesztenie kellene az azokra hivatkozó runbookokat és konfigurációkat.

### <a name="source-control-integration"></a>Verziókövetés integrálása

Azure Automation [integrálható a forrás-vezérlőelemmel](source-control-integration.md) , amely a konfigurációt olyan kódként támogatja, amelyben a runbookok vagy a konfigurációk ellenőrizhetők a verziókövetés rendszerében.

### <a name="role-based-access-control"></a>Szerepköralapú hozzáférés-vezérlés

A Azure Automation Access Control támogatja az Automation-fiókhoz és erőforrásaihoz való hozzáférést, valamint az Automation-fiók, a runbookok és a feladatok konfigurációs RBAC kapcsolatos további tudnivalókat az [Azure Automation szerepköralapú hozzáférés-vezérlése](automation-role-based-access-control.md)című témakörben.

### <a name="windows-and-linux"></a>Windows és Linux

Az Azure Automation úgy lett kifejlesztve, hogy együttműködjön a hibrid felhőkörnyezetekkel, valamint a Windows- és Linux-környezetekkel. Egy egységes platformot biztosít az üzembe helyezett számítási feladatok és az azokat futtató operációs rendszerek automatizálásához és konfigurálásához.

### <a name="community-gallery"></a>Közösségi katalógus

Az [Automation-katalógus](automation-runbook-gallery.md) runbookjai és moduljai használatával gyorsan nekiláthat saját folyamatai a PowerShell-galéria és a Microsoft Script Center segítségével történő integrálásának és kialakításának.

## <a name="common-scenarios-for-automation"></a>Általános példák az Automation használatára

Az Azure Automationnel az infrastruktúra és az alkalmazások életciklusát felügyelheti. A használatával beadhatja a rendszerbe a vállalat számítási feladatainak működésével és karbantartásával kapcsolatos adatokat. A forgatókönyveket különféle gyakori nyelveken, például PowerShell, DSC vagy Python nyelven, illetve grafikus runbookként hozhatja létre. Az üzembe helyezett erőforrásokról teljes leltár áll a rendelkezésére célkezelési, jelentéskészítési és megfelelőségi célokra. Azonosíthatja azokat a változtatásokat, amelyek hibás konfigurációt okozhatnak, és javíthatja a működési megfelelőséget.

* **Erőforrások kiépítése és üzembe helyezése** – Üzembe helyezhet virtuális gépeket a hibrid környezetekben runbookok és Azure Resource Manager-sablonok használatával. A rendszert integrálhatja a Jenkinsszel, az Azure DevOpsszal és egyéb fejlesztői eszközökkel.
* **Virtuális gépek konfigurálása** – Felmérheti a windowsos és linuxos gépek állapotát, és konfigurálhatja azokat az infrastruktúrának és az alkalmazásoknak megfelelő kívánt beállításokkal.
* **Monitorozás** – Azonosíthatja a problémákat okozó gépeken ejtett módosításokat, és kijavíthatja azokat, vagy felterjesztheti a felügyeleti rendszerekre.
* **Védelem** – Biztonsági riasztás esetén karanténba helyezheti a virtuális gépeket. Megadhatja a vendégen futtatott rendszerekre vonatkozó követelményeket.
* **Irányítás** – Szerepköralapú hozzáférés-vezérlést állíthat be csapatok számára. Visszanyerheti a nem használt erőforrásokat.

## <a name="pricing-for-automation"></a>Az Automation szolgáltatás díjszabása

Az Azure Automation árait a [díjszabást](https://azure.microsoft.com/pricing/details/automation/) ismertető oldalon tekintheti meg.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Automation-fiók létrehozása](automation-quickstart-create-account.md)

