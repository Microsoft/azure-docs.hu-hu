---
title: Hibrid gépek összekötése az Azure-ba a Windows felügyeleti központból
description: Ebből a cikkből megtudhatja, hogyan telepítheti az ügynököt, és hogyan csatlakoztathatók a gépek az Azure-ba az Azure arc-kompatibilis kiszolgálók használatával a Windows felügyeleti központból.
ms.date: 10/12/2020
ms.topic: conceptual
ms.openlocfilehash: 5fa7a61b1e3b22503377cbcbe308a82be89dac72
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100584768"
---
# <a name="connect-hybrid-machines-to-azure-from-windows-admin-center"></a>Hibrid gépek összekötése az Azure-ba a Windows felügyeleti központból

A környezet egy vagy több Windows rendszerű számítógépén engedélyezheti az Azure arc-kompatibilis kiszolgálókat, ha a lépéseket manuálisan hajtja végre. Vagy használhatja a [Windows felügyeleti központot](/windows-server/manage/windows-admin-center/understand/what-is) a csatlakoztatott számítógép-ügynök üzembe helyezéséhez és a helyszíni kiszolgálók regisztrálásához anélkül, hogy az eszközön kívül kellene végrehajtania semmilyen lépést.

## <a name="prerequisites"></a>Előfeltételek

* Arc-kompatibilis kiszolgálók – tekintse át az [előfeltételeket](agent-overview.md#prerequisites) , és ellenőrizze, hogy az előfizetése, az Azure-fiókja és az erőforrásai megfelelnek-e a követelményeknek.

* Windows felügyeleti központ – tekintse át az Azure-integráció üzembe helyezésére és [konfigurálására ](/windows-server/manage/windows-admin-center/azure/azure-integration)szolgáló [környezet előkészítésének](/windows-server/manage/windows-admin-center/deploy/prepare-environment) követelményeit.

* Azure-előfizetés. Ha még nem rendelkezik ilyennel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

* A felügyelni kívánt cél Windows-kiszolgálóknak internetkapcsolattal kell rendelkezniük az Azure eléréséhez.

### <a name="security"></a>Biztonság

Ez a telepítési módszer megköveteli, hogy rendszergazdai jogosultságokkal rendelkezzen a cél Windows-gépen vagy-kiszolgálón az ügynök telepítéséhez és konfigurálásához. Emellett az [**átjáró felhasználói**](/windows-server/manage/windows-admin-center/plan/user-access-options#gateway-access-roles) szerepkör tagjának kell lennie.

## <a name="deploy"></a>Üzembe helyezés

Az alábbi lépések végrehajtásával konfigurálja a Windows Servert az ív használatára képes kiszolgálókon.

1. Jelentkezzen be a Windows felügyeleti központba.

1. Az **Áttekintés** lap kapcsolatok listájában a csatlakoztatott Windows-kiszolgálók listájában válasszon ki egy kiszolgálót a listából a csatlakozáshoz.

1. A bal oldali ablaktáblában válassza az **Azure Hybrid Services** elemet.

1. Az **Azure Hybrid Services** lapon válassza az **Azure-szolgáltatások felderítése** elemet.

1. Az Azure- **szolgáltatások felderítése** oldalon, az **Azure-szabályzatok és-megoldások kihasználása a kiszolgálók Azure arc használatával történő kezeléséhez** területen válassza a **beállítás** lehetőséget.

1. Ha a rendszer kéri az Azure-ban a **Settings\Azure arc for Servers (kiszolgálók** összekapcsolása) lapot, majd az **első lépések** lehetőséget választja.

1. A **kiszolgáló összekapcsolása az Azure** -ba lapon adja meg a következőket:

    1. Az **Azure-előfizetés** legördülő listában válassza ki az Azure-előfizetést.
    1. Az **erőforráscsoport** beállításnál válassza az **új** lehetőséget új erőforráscsoport létrehozásához, vagy az **erőforráscsoport** legördülő listából válassza ki azt a meglévő erőforráscsoportot, amelyről regisztrálni és felügyelni kívánja a gépet.
    1. A **régió** legördülő listában válassza ki az Azure-régiót a kiszolgálók metaadatainak tárolásához.
    1. Ha a számítógép vagy a kiszolgáló proxykiszolgálón keresztül kommunikál az internethez, válassza a **proxykiszolgáló használata** lehetőséget. Adja meg a proxykiszolgáló IP-címét vagy nevét, valamint azt a portszámot, amelyet a gép a proxykiszolgálóhoz való kommunikációhoz használni fog.

1. Válassza a **beállítás** lehetőséget, hogy folytassa a Windows Server Azure arc-kompatibilis kiszolgálókkal való konfigurálásának folytatását.

A Windows Server csatlakozik az Azure-hoz, letölti a csatlakoztatott Machine Agent ügynököt, telepíti és regisztrálja az Azure arc-kompatibilis kiszolgálókon. A folyamat nyomon követéséhez válassza a menü **értesítések** elemét.

A csatlakoztatott gépi ügynök telepítésének ellenőrzéséhez a Windows felügyeleti központban válassza az [**események**](/windows-server/manage/windows-admin-center/use/manage-servers#events) lehetőséget a bal oldali ablaktáblán a *MsiInstaller* -események áttekintéséhez az alkalmazás eseménynaplójában.

## <a name="verify-the-connection-with-azure-arc"></a>Az Azure Arc csatlakozásának ellenőrzése

Miután telepítette az ügynököt, és konfigurálta az Azure arc-kompatibilis kiszolgálókhoz való csatlakozáshoz, lépjen a Azure Portal, és ellenőrizze, hogy a kiszolgáló sikeresen csatlakozott-e. Megtekintheti a gépet a [Azure Portalban](https://portal.azure.com).

:::image type="content" source="./learn/media/quick-enable-hybrid-vm/enabled-machine.png" alt-text="A számítógép sikeres csatlakoztatása" border="false":::

## <a name="next-steps"></a>Következő lépések

* A hibaelhárítási információ a [csatlakoztatott gép ügynökének hibaelhárítása című útmutatóban](troubleshoot-agent-onboard.md)található.

* Megtudhatja, hogyan kezelheti a gépet [Azure Policy](../../governance/policy/overview.md)használatával, például a virtuális gép [vendég konfigurációjában](../../governance/policy/concepts/guest-configuration.md), ellenőrizheti, hogy a gép a várt log Analytics munkaterületről jelent-e jelentést, lehetővé teszi a figyelést a virtuális [gépekkel Azure monitor](../../azure-monitor/vm/vminsights-enable-policy.md)és sok más további műveletet.

* További információ a [log Analytics-ügynökről](../../azure-monitor/agents/log-analytics-agent.md). A Windows és Linux rendszerhez készült Log Analytics ügynökre akkor van szükség, ha az operációs rendszer és a számítási feladatok figyelésére szolgáló adatokat szeretné összegyűjteni, az Automation-runbookok vagy-szolgáltatásokkal, például a Update Managementekkel, vagy más Azure-szolgáltatások, például a [Azure Security Center](../../security-center/security-center-introduction.md)használatával