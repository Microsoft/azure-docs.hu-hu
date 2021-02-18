---
title: Erőforrások, szerepkörök és hozzáférés-vezérlés az Azure Application Insightsban | Microsoft Docs
description: A szervezet adatai tulajdonosai, közreműködői és olvasói.
ms.topic: conceptual
ms.date: 02/14/2019
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 4254170ad100b634439cc846dc381e305ae71d1f
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100589602"
---
# <a name="resources-roles-and-access-control-in-application-insights"></a>Erőforrások, szerepkörök és hozzáférés-vezérlés a Application Insightsban

Az Azure- [beli szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md)használatával szabályozhatja, hogy ki olvasta és frissítse az adataihoz való hozzáférést az Azure [Application Insightsban][start].

> [!IMPORTANT]
> Rendeljen hozzá hozzáférést az **erőforráscsoport vagy az előfizetés** azon felhasználóihoz, amelyekhez az alkalmazás-erőforrás tartozik – nem az erőforráshoz. Rendelje hozzá a **Application Insights összetevő közreműködői** szerepkört. Ez biztosítja a webes tesztekhez és riasztásokhoz való hozzáférés egységes vezérlését az alkalmazás-erőforrással együtt. [További információ](#access).


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="resources-groups-and-subscriptions"></a>Erőforrások, csoportok és előfizetések

Először is néhány definíció:

* **Erőforrás** – Microsoft Azure szolgáltatás egy példánya. Az Application Insights-erőforrás gyűjti, elemzi és megjeleníti az alkalmazásból elküldett telemetria-adatokat.  Más típusú Azure-erőforrások közé tartoznak a webalkalmazások, adatbázisok és virtuális gépek.
  
    Az erőforrások megjelenítéséhez nyissa meg a [Azure Portal][portal], jelentkezzen be, majd kattintson a minden erőforrás elemre. Egy erőforrás megkereséséhez írja be a neve egy részét a szűrő mezőbe.
  
    ![Azure-erőforrások listája](./media/resources-roles-access-control/10-browse.png)

<a name="resource-group"></a>

* [**Erőforráscsoport**][group] – minden erőforrás egy csoporthoz tartozik. A csoportok kényelmes módon kezelhetők a kapcsolódó erőforrások, különösen a hozzáférés-vezérlés esetében. Egy erőforráscsoport esetében például létrehozhat egy webalkalmazást, egy Application Insights-erőforrást az alkalmazás figyeléséhez, valamint egy tárolási erőforrást az exportált adatkezeléshez.

* [**Előfizetés**](https://portal.azure.com) – Application Insights vagy más Azure-erőforrások használatához jelentkezzen be egy Azure-előfizetésbe. Minden erőforráscsoport egy Azure-előfizetéshez tartozik, ahol kiválaszthatja a csomagot, és ha szervezeti előfizetéssel rendelkezik, válassza ki a tagokat és azok hozzáférési engedélyeit.
* [**Microsoft-fiók**][account] – az Microsoft Azure előfizetésekre, az Xbox Live-ra, a Outlook.com és más Microsoft-szolgáltatásokba való bejelentkezéshez használt Felhasználónév és jelszó.

## <a name="control-access-in-the-resource-group"></a><a name="access"></a> Hozzáférés vezérlése az erőforráscsoporthoz

Fontos tisztában lenni azzal, hogy az alkalmazáshoz létrehozott erőforráson kívül a riasztások és a webes tesztek is külön rejtett erőforrásokkal rendelkeznek. Ezek ugyanahhoz az [erőforráscsoporthoz](#resource-group) vannak csatolva, mint a Application Insights erőforrás. Előfordulhat, hogy más Azure-szolgáltatásokat is elhelyez, például webhelyeket vagy tárhelyet.

## <a name="to-provide-access-to-another-user"></a>Hozzáférés biztosítása egy másik felhasználó számára

Az előfizetéshez vagy az erőforráscsoporthoz tulajdonosi jogosultsággal kell rendelkeznie.

A felhasználónak [Microsoft-fiókkal][account]kell rendelkeznie, vagy hozzá kell férnie a [szervezeti Microsoft-fiókjához](../../active-directory/fundamentals/sign-up-organization.md). Hozzáférést biztosíthat az egyéni felhasználókhoz és a Azure Active Directoryban definiált felhasználói csoportokhoz is.

#### <a name="navigate-to-resource-group-or-directly-to-the-resource-itself"></a>Navigáljon az erőforráscsoporthoz vagy közvetlenül az erőforráshoz

A bal oldali menüben válassza a **hozzáférés-vezérlés (iam)** lehetőséget.

![Képernyőkép a hozzáférés-vezérlési gombról Azure Portal](./media/resources-roles-access-control/0001-access-control.png)

Válassza a **szerepkör-hozzárendelés hozzáadása** elemet.

![Képernyőfelvétel a hozzáférés-vezérlés menüjéről a Hozzáadás gombbal piros színnel](./media/resources-roles-access-control/0002-add.png)

Az alábbi **engedélyek hozzáadása** nézet elsősorban Application Insights erőforrásokra vonatkozik, ha a hozzáférés-vezérlési engedélyeket magasabb szintű (például erőforráscsoportok) nézetben tekinti meg, akkor további nem Application Insights-központú szerepköröket láthat.

Az Azure szerepköralapú hozzáférés-vezérlés beépített szerepköreivel kapcsolatos információk megtekintéséhez használja a [hivatalos hivatkozási tartalmat](../../role-based-access-control/built-in-roles.md).

![Képernyőfelvétel a hozzáférés-vezérlés felhasználói szerepkörök listájáról](./media/resources-roles-access-control/0003-user-roles.png)

#### <a name="select-a-role"></a>Szerepkör kiválasztása

Adott esetben a kapcsolódó hivatalos dokumentációra hivatkozunk.

| Szerepkör | Az erőforráscsoporthoz |
| --- | --- |
| [Tulajdonos](../../role-based-access-control/built-in-roles.md#owner) |Bármit megváltoztathat, beleértve a felhasználói hozzáférést is. |
| [Közreműködő](../../role-based-access-control/built-in-roles.md#contributor) |Bármit szerkeszthet, beleértve az összes erőforrást is. |
| [Application Insights összetevő közreműködője](../../role-based-access-control/built-in-roles.md#application-insights-component-contributor) |Szerkesztheti Application Insights erőforrásait. |
| [Olvasó](../../role-based-access-control/built-in-roles.md#reader) |Megtekintheti, de nem változtathatja meg bármit. |
| [Application Insights Snapshot Debugger](../../role-based-access-control/built-in-roles.md#application-insights-snapshot-debugger) | Engedélyt ad a felhasználónak a Application Insights Snapshot Debugger funkcióinak használatára. Vegye figyelembe, hogy ez a szerepkör sem a tulajdonos, sem a közreműködő szerepkör részét képezi. |
| Az Azure szolgáltatás üzembe helyezésének kiadási felügyeleti közreműködői | Közreműködő szerepkör az Azure Service üzembe helyezésével üzembe helyezett szolgáltatásokhoz. |
| [Adattisztító](../../role-based-access-control/built-in-roles.md#data-purger) | A személyes adattörlés speciális szerepe. További információért tekintse meg a [személyes adatokra vonatkozó útmutatást](../logs/personal-data-mgmt.md) .   |
| ExpressRoute-rendszergazda | Az expressz útvonalak törlését és kezelését is létrehozhatja.|
| [Log Analytics közreműködő](../../role-based-access-control/built-in-roles.md#log-analytics-contributor) | Log Analytics közreműködő képes olvasni az összes figyelési és szerkesztési beállítást. A figyelési beállítások szerkesztése magában foglalja a virtuálisgép-bővítmény virtuális gépekhez való hozzáadását. a Storage-fiók kulcsainak beolvasása az Azure Storage-ból származó naplók gyűjtésének konfigurálásához; Automation-fiókok létrehozása és konfigurálása; megoldások hozzáadása; és az Azure Diagnostics konfigurálása az összes Azure-erőforráshoz.  |
| [Log Analytics olvasó](../../role-based-access-control/built-in-roles.md#log-analytics-reader) | Log Analytics olvasó megtekintheti és megkeresheti az összes figyelési adatait, valamint megtekintheti a figyelési beállításokat, beleértve az Azure Diagnostics konfigurációjának megtekintését az összes Azure-erőforráson. |
| masterreader | Lehetővé teszi a felhasználó számára, hogy megtekintse az összes módosítást, de ne módosítsa azokat. |
| [Közreműködő figyelése](../../role-based-access-control/built-in-roles.md#monitoring-contributor) | Beolvashatja az összes figyelési és frissítési figyelési beállítást.|
| [Figyelési metrikák közzétevője](../../role-based-access-control/built-in-roles.md#monitoring-metrics-publisher) | Lehetővé teszi az Azure-erőforrásokhoz való közzétételi mérőszámok közzétételét. |
| [Figyelő olvasó](../../role-based-access-control/built-in-roles.md#monitoring-reader) | Az összes figyelési adattal elolvasható. |
| Erőforrás-házirend közreműködője (előzetes verzió) | Visszatöltötte-felhasználóktól, az erőforrás-házirend létrehozásához/módosításához, támogatási jegy létrehozásához és erőforrás/hierarchia olvasásához szükséges jogosultságokkal.  |
| [Felhasználói hozzáférés adminisztrátora](../../role-based-access-control/built-in-roles.md#user-access-administrator) | Lehetővé teszi, hogy a felhasználó kezelje más felhasználók hozzáférését az Azure-erőforrásokhoz.|
| [Webhely közreműködői](../../role-based-access-control/built-in-roles.md#website-contributor) | Lehetővé teszi a webhelyek (nem webes csomagok) kezelését, de azokhoz való hozzáférés nélkül.|

A "szerkesztés" magában foglalja a következők létrehozását, törlését és frissítését:

* Források
* Webes tesztek
* Riasztások
* Folyamatos exportálás

#### <a name="select-the-user"></a>Válassza ki a felhasználót

Ha a kívánt felhasználó nem szerepel a címtárban, meghívhat bárkit egy Microsoft-fiók.
(Ha olyan szolgáltatásokat használnak, mint például a Outlook.com, a OneDrive, a Windows Phone-telefon vagy az XBox Live, Microsoft-fiók.)

## <a name="related-content"></a>Kapcsolódó tartalom

* [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md)

## <a name="powershell-query-to-determine-role-membership"></a>PowerShell-lekérdezés a szerepkör tagságának meghatározásához

Mivel bizonyos szerepkörök csatolhatók az értesítésekhez és az e-mailes riasztásokhoz, hasznos lehet az adott szerepkörhöz tartozó felhasználók listájának létrehozása. Az ilyen típusú jegyzékek létrehozásával kapcsolatban a következő, az adott igényeknek megfelelően módosítható lekérdezéseket ajánljuk fel:

### <a name="query-entire-subscription-for-admin-roles--contributor-roles"></a>Teljes előfizetés lekérdezése rendszergazdai szerepkörök + közreműködői szerepkörökhöz

```powershell
(Get-AzRoleAssignment -IncludeClassicAdministrators | Where-Object {$_.RoleDefinitionName -in @('ServiceAdministrator', 'CoAdministrator', 'Owner', 'Contributor') } | Select -ExpandProperty SignInName | Sort-Object -Unique) -Join ", "
```

### <a name="query-within-the-context-of-a-specific-application-insights-resource-for-owners-and-contributors"></a>Lekérdezés egy adott Application Insights erőforrás kontextusában a tulajdonosok és a közreműködők számára

```powershell
$resourceGroup = "RGNAME"
$resourceName = "AppInsightsName"
$resourceType = "microsoft.insights/components"
(Get-AzRoleAssignment -ResourceGroup $resourceGroup -ResourceType $resourceType -ResourceName $resourceName | Where-Object {$_.RoleDefinitionName -in @('Owner', 'Contributor') } | Select -ExpandProperty SignInName | Sort-Object -Unique) -Join ", "
```

### <a name="query-within-the-context-of-a-specific-resource-group-for-owners-and-contributors"></a>Lekérdezés egy adott erőforráscsoport kontextusában a tulajdonosok és a közreműködők számára

```powershell
$resourceGroup = "RGNAME"
(Get-AzRoleAssignment -ResourceGroup $resourceGroup | Where-Object {$_.RoleDefinitionName -in @('Owner', 'Contributor') } | Select -ExpandProperty SignInName | Sort-Object -Unique) -Join ", "
```

<!--Link references-->

[account]: https://account.microsoft.com
[group]: ../../azure-resource-manager/management/overview.md
[portal]: https://portal.azure.com/
[start]: ./app-insights-overview.md
