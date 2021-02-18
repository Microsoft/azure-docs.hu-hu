---
title: Configuration Manager összekötése Azure Monitorhoz | Microsoft Docs
description: Ez a cikk bemutatja, hogyan csatlakozhat Configuration Manager a munkaterülethez Azure Monitor és megkezdheti az adatok elemzését.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/30/2020
ms.openlocfilehash: 540ca293afdd7514793b8eec390b4751885859df
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100618458"
---
# <a name="connect-configuration-manager-to-azure-monitor"></a>Configuration Manager összekötése a Azure Monitor
A Microsoft Endpoint Configuration Manager-Azure Monitor környezet összekapcsolásával szinkronizálhatja az eszköz-gyűjtési adatait, és Azure Monitor és Azure Automation is hivatkozhat ezekre a gyűjteményekre.  

> [!IMPORTANT]
> A Configuration Manager 2010-es verziótól kezdődően ez a szolgáltatás elavult.<!-- 8269855 --> További információ: [Configuration Manager eltávolított és elavult funkciói](/mem/configmgr/core/plan-design/changes/deprecated/removed-and-deprecated-cmfeatures).

## <a name="prerequisites"></a>Előfeltételek

Azure Monitor támogatja Configuration Manager aktuális ág, 1606-es és újabb verzióját.

>[!NOTE]
>A Configuration Manager Log Analytics munkaterülettel való összekapcsolásának funkciója nem kötelező, és alapértelmezés szerint nincs engedélyezve. A funkció használatához engedélyeznie kell ezt a funkciót. További információ: [Enable optional features from updates](/configmgr/core/servers/manage/install-in-console-updates#bkmk_options).

## <a name="configuration-overview"></a>Konfiguráció áttekintése

A következő lépések összefoglalják a Configuration Manager integráció konfigurálásának lépéseit Azure Monitorokkal.  

1. A Azure Active Directoryban regisztráljon Configuration Manager webalkalmazásként és/vagy webes API-alkalmazásként, és győződjön meg arról, hogy az ügyfél-azonosító és az ügyfél titkos kulcsa a regisztrációból a Azure Active Directory. A lépés végrehajtásával kapcsolatos részletes információkért tekintse meg a [portál használata Active Directory alkalmazás és egyszerű szolgáltatás létrehozása](../../active-directory/develop/howto-create-service-principal-portal.md) című témakört.

2. A Azure Active Directoryban [adja meg a Configuration Manager (a regisztrált webalkalmazás) engedélyt a Azure monitor eléréséhez](#grant-configuration-manager-with-permissions-to-log-analytics).

3. A Configuration Managerban vegyen fel egy-kapcsolatokat az **Azure-szolgáltatások** varázslóval.

4. [Töltse le és telepítse a Windows rendszerhez készült log Analytics-ügynököt](#download-and-install-the-agent) a Configuration Manager szolgáltatási kapcsolódási pont helyrendszer-szerepkört futtató számítógépen. Az ügynök Configuration Manager adatokat küld a Azure Monitor Log Analytics munkaterületére.

5. Azure Monitor a [gyűjtemények importálása Configuration Manager](#import-collections) számítógép-csoportokként.

6. A Azure Monitorban az adatok megtekinthetők a Configuration Manager [számítógép-csoportokként](computer-groups.md).

## <a name="grant-configuration-manager-with-permissions-to-log-analytics"></a>Engedélyezési Configuration Manager engedélyezése Log Analytics

A következő eljárásban a *közreműködő* szerepkört a log Analytics munkaterületen a Configuration Manager korábban létrehozott ad-alkalmazáshoz és egyszerű szolgáltatáshoz adja meg. Ha még nem rendelkezik munkaterülettel, a folytatás előtt tekintse [meg a Munkaterületek létrehozása a Azure monitorban](../learn/quick-create-workspace.md) című témakört. Ez lehetővé teszi, hogy a Configuration Manager hitelesítse és kapcsolódjon a Log Analytics-munkaterülethez.  

> [!NOTE]
> Configuration Manager esetében meg kell adnia az engedélyeket a Log Analytics munkaterületen. Ellenkező esetben hibaüzenet jelenik meg, amikor a Configuration Manager konfigurációs varázslóját használja.
>

1. Az Azure Portal bal felső sarkában kattintson a **Minden szolgáltatás** lehetőségre. Az erőforrások listájába írja be a **Log Analytics** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza a **Log Analytics** elemet.

2. Log Analytics munkaterületek listájában válassza ki a módosítandó munkaterületet.

3. A bal oldali panelen válassza a **hozzáférés-vezérlés (iam)** lehetőséget.

4. A hozzáférés-vezérlés (IAM) lapon kattintson a **szerepkör-hozzárendelés hozzáadása** elemre, és megjelenik a **szerepkör-hozzárendelés hozzáadása** panel.

5. A **szerepkör-hozzárendelés hozzáadása** ablaktáblán a **szerepkör** legördülő listában válassza ki a **közreműködő** szerepkört.  

6. A **hozzáférés kiosztása** legördülő listáról válassza ki a korábban az ad-ben létrehozott Configuration Manager alkalmazást, majd kattintson **az OK** gombra.  

## <a name="download-and-install-the-agent"></a>Az ügynök letöltése és telepítése

Tekintse át a [Windows rendszerű Azure monitor számítógépek csatlakoztatása az Azure-ban](../agents/agent-windows.md) című cikket, hogy megismerje a log Analytics-ügynök Windows rendszeren való telepítéséhez elérhető módszereket a Configuration Manager Service csatlakozási pontjának helyrendszer-szerepkörét futtató számítógépen.  

## <a name="connect-configuration-manager-to-log-analytics-workspace"></a>Configuration Manager összekötése Log Analytics munkaterülettel

>[!NOTE]
> Log Analytics kapcsolat hozzáadásához a Configuration Manager-környezetnek online módba konfigurált [szolgáltatási kapcsolódási ponttal](/configmgr/core/servers/deploy/configure/about-the-service-connection-point) kell rendelkeznie.

> [!NOTE]
> A hierarchiában lévő legfelső szintű helyet Azure Monitorra kell kapcsolni. Ha egy önálló elsődleges helyet csatlakoztat Azure Monitor, majd egy központi adminisztrációs helyet ad hozzá a környezethez, törölnie kell, majd újra létre kell hoznia a kapcsolatot az új hierarchián belül.

1. A Configuration Manager **Adminisztráció** munkaterületén válassza a **felhők szolgáltatások** elemet, majd válassza az **Azure-szolgáltatások** elemet. 

2. Kattintson a jobb gombbal az **Azure-szolgáltatások** elemre, majd válassza az **Azure-szolgáltatások konfigurálása** lehetőséget. Megjelenik az **Azure-szolgáltatások konfigurálása** oldal. 
   
3. Az **általános** képernyőn győződjön meg arról, hogy végrehajtotta a következő műveleteket, és hogy az egyes elemek részletei megtalálhatók, majd válassza a **tovább** lehetőséget.

4. Az Azure-szolgáltatások varázsló Azure-szolgáltatások lapján:

    1. Adja meg az objektum **nevét** Configuration Managerban.
    2. A szolgáltatás azonosításának megkönnyítéséhez adja meg a nem kötelező **leírást** .
    3. Válassza ki az Azure Service **OMS-összekötőt**.

    >[!NOTE]
    >A OMS mostantól olyan Log Analytics néven is ismert, amely Azure Monitor egyik funkciója.

5. Kattintson a **tovább** gombra az Azure-szolgáltatások varázsló Azure-alkalmazás tulajdonságai lapjának folytatásához.

6. Az Azure-szolgáltatások varázsló **alkalmazás** lapján először válassza ki az Azure-környezetet a listából, majd kattintson az **Importálás** elemre.

7. Az **alkalmazások importálása** lapon a következő információkat kell megadnia:

    1. Adja meg az alkalmazáshoz tartozó **Azure ad-bérlő nevét** .

    2. Itt adhatja meg az Azure **ad-bérlő azonosítóját** az Azure ad-bérlő számára. Ezeket az információkat a Azure Active Directory **Tulajdonságok** lapján találja. 

    3. Adja meg az **alkalmazás** nevét az alkalmazás nevében.

    4. A korábban létrehozott létrehozott Azure AD-alkalmazáshoz tartozó **ügyfél-azonosító** megadása.

    5. A **titkos kulcs** megadásához a létrehozott Azure ad-alkalmazás ügyfél titkos kulcsa.

    6. Adja meg a **titkos kulcs lejárati** idejét, a kulcs lejárati dátumát.

    7. Az **alkalmazás-azonosító URI** azonosítójának megadása a korábban létrehozott létrehozott Azure ad-alkalmazáshoz tartozó alkalmazás-azonosító URI-ja.

    8. Válassza az **ellenőrzés** lehetőséget, és a jobb oldalon a **sikeres ellenőrzésnek** megfelelő eredményeket kell megjeleníteni!.

8. A **konfiguráció** lapon tekintse át az **Azure-előfizetések**, az **Azure-erőforráscsoport** és az **Operations Management Suite-munkaterület** mezőinek ellenőrzéséhez szükséges információkat, és jelezze, hogy az Azure ad-alkalmazás megfelelő engedélyekkel rendelkezik az erőforráscsoporthoz. Ha a mezők üresek, azt jelzi, hogy az alkalmazás nem rendelkezik a szükséges jogosultságokkal. Válassza ki a gyűjteni kívánt és a munkaterületre továbbítandó eszközöket, majd válassza a **Hozzáadás** lehetőséget.

9. A kapcsolatok létrehozásának és konfigurálásának megkezdéséhez tekintse át a beállításokat a **Beállítások megerősítése** lapon, majd a **tovább** gombra kattintva.

10. Ha a konfiguráció elkészült, megjelenik a **Befejezés** lap. Válassza a **Bezárás** gombot. 

Miután csatolta Configuration Managert a Azure Monitorhoz, hozzáadhat vagy eltávolíthat gyűjteményeket, és megtekintheti a kapcsolat tulajdonságait.

## <a name="update-log-analytics-workspace-connection-properties"></a>Log Analytics munkaterület-kapcsolatok tulajdonságainak frissítése

Ha a jelszó vagy az ügyfél titkos kulcsa lejár vagy elveszett, manuálisan frissítenie kell a Log Analytics kapcsolati tulajdonságait.

1. A Configuration Manager **Adminisztráció** munkaterületén válassza a **Cloud Services** , majd az **OMS-összekötő** lehetőséget a **OMS-kapcsolatok tulajdonságlapjának** megnyitásához.
2. Ezen az oldalon a **Azure Active Directory** lapra kattintva megtekintheti a **bérlőt**, az **ügyfél-azonosítót**, az **ügyfél titkos kulcsának lejárati** idejét. **Ellenőrizze** , hogy az **ügyfél titkos kulcsa** lejárt-e.

## <a name="import-collections"></a>Gyűjtemények importálása

Miután hozzáadta Log Analytics kapcsolatát az Configuration Managerhoz, és telepítette az ügynököt a Configuration Manager Service kapcsolódási pont helyrendszer-szerepkört futtató számítógépen, a következő lépés a gyűjtemények importálása Azure Monitor számítógép-csoportként Configuration Manager.

Miután befejezte a kezdeti konfigurációt, hogy importálja az eszközöket a hierarchiából, a rendszer 3 óránként beolvassa a gyűjtemény információit a tagság aktuális állapotának megőrzéséhez. Bármikor letilthatja ezt a lehetőséget.

1. Az Azure Portal bal felső sarkában kattintson a **Minden szolgáltatás** lehetőségre. Az erőforrások listájába írja be a **Log Analytics** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza **log Analytics munkaterületek** lehetőséget.
2. Log Analytics munkaterületek listájában válassza ki a munkaterületet, Configuration Manager regisztrálva van a-ben.  
3. Válassza ki a **Speciális beállítások** elemet.
4. Válassza ki a **számítógépcsoportok** elemet, majd válassza a **SCCM** lehetőséget.  
5. Válassza az **importálás Configuration Manager gyűjtemény tagságok** lehetőséget, majd kattintson a **Mentés** gombra.  
   
    ![Képernyőkép a számítógép-csoportok speciális beállításairól, amelyek tartalmazzák az importálási Configuration Manager gyűjtemény tagságának lehetőségét.](./media/collect-sccm/sccm-computer-groups01.png)

## <a name="view-data-from-configuration-manager"></a>Adatok megtekintése Configuration Manager

Miután hozzáadta Log Analytics kapcsolatát az Configuration Managerhoz, és telepítette az ügynököt a Configuration Manager Service kapcsolódási pont helyrendszer-szerepkört futtató számítógépen, a rendszer elküldi az ügynöktől az Azure Monitor Log Analytics munkaterületére. Azure Monitor a Configuration Manager gyűjtemények [számítógépcsoportként](./computer-groups.md)jelennek meg. A csoportokat a **Configuration Manager** lapon tekintheti meg a **Settings\Computer csoportok** területen.

A gyűjtemények importálása után láthatja, hány számítógépet észlelt a gyűjtemény tagsága. Az importált gyűjtemények számát is megtekintheti.

![Képernyőfelvétel: a számítógép-csoportok speciális beállításai a C C M-hez, amely az importálási Configuration Manager gyűjteményi tagságok kiválasztásának lehetőségét mutatja.](./media/collect-sccm/sccm-computer-groups02.png)

Ha rákattint valamelyikre, a naplófájl-szerkesztő megnyílik az összes importált csoport vagy az egyes csoportokhoz tartozó összes számítógép között. A [naplóbeli keresés](../log-query/log-query-overview.md)használatával további részletes elemzéseket végezhet a gyűjtemény tagsági adataiban.

## <a name="next-steps"></a>Következő lépések

A [naplók keresési](../log-query/log-query-overview.md) funkciójának használatával megtekintheti a Configuration Manager adataival kapcsolatos részletes információkat.

