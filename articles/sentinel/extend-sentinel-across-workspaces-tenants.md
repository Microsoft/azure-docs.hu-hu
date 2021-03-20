---
title: Az Azure Sentinel bővítése munkaterületek és bérlők között | Microsoft Docs
description: Az Azure Sentinel használata az adatterületek és bérlők közötti adatlekérdezésre és-elemzésre.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/11/2020
ms.author: yelevin
ms.openlocfilehash: 49b267d36fb6c365cf2125912c0d27fe7d669474
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100585293"
---
# <a name="extend-azure-sentinel-across-workspaces-and-tenants"></a>Az Azure Sentinel kiterjesztése munkaterületek és bérlők között

## <a name="the-need-to-use-multiple-azure-sentinel-workspaces"></a>Több Azure Sentinel-munkaterület használata szükséges

Az Azure Sentinel egy Log Analytics munkaterületre épül. Figyelje meg, hogy az Azure Sentinel bevezetésének első lépéseként ki kell választania az adott célra használni kívánt Log Analytics munkaterületet.

Az Azure Sentinel-felület összes előnyét akkor használhatja ki, ha egyetlen munkaterületet használ. Ennek ellenére bizonyos esetekben előfordulhat, hogy több munkaterületre van szükség. Az alábbi táblázat felsorolja ezeket a helyzeteket, és ha lehetséges, azt javasolja, hogy a követelmény hogyan lehet megfelelni egyetlen munkaterülettel:

| Követelmény | Leírás | Módszerek a munkaterületek számának csökkentésére |
|-------------|-------------|--------------------------------|
| Szuverenitás és szabályozási megfelelőség | Egy munkaterület egy adott régióhoz kötődik. Ha a szabályozás követelményeinek kielégítéséhez különböző [Azure](https://azure.microsoft.com/global-infrastructure/geographies/) -régiókban kell megőrizni az adatmennyiséget, külön munkaterületekre kell bontani. |  |
| Az adatok tulajdonjoga | Az adattulajdonos határai, például a leányvállalatok vagy a kapcsolt vállalatok, jobban körülhatárolva vannak külön munkaterületek használatával. |  |
| Több Azure-bérlő | Az Azure Sentinel csak a saját Azure Active Directory (Azure AD) bérlői határán belül támogatja a Microsoft és az Azure SaaS-erőforrások adatgyűjtését. Emiatt minden Azure AD-bérlőhöz külön munkaterületre van szükség. |  |
| Részletes adathozzáférés-vezérlés | Előfordulhat, hogy egy szervezetnek a szervezeten belül vagy kívül különböző csoportokat is engedélyeznie kell az Azure Sentinel által összegyűjtött adatok eléréséhez. Például:<br><ul><li>Erőforrás-tulajdonosok hozzáférése az erőforrásokhoz kapcsolódó adatforrásokhoz</li><li>A SOCs regionális vagy leányvállalati hozzáférése a szervezet részeihez kapcsolódóan</li></ul> | Azure- [RBAC](https://techcommunity.microsoft.com/t5/azure-sentinel/controlling-access-to-azure-sentinel-data-resource-rbac/ba-p/1301463) vagy- [szintű Azure-RBAC](https://techcommunity.microsoft.com/t5/azure-sentinel/table-level-rbac-in-azure-sentinel/ba-p/965043) használata |
| Részletes adatmegőrzési beállítások | A különböző adattípusok esetében a több munkaterületet is csak a különböző adatmegőrzési időszakok beállítására lehet beállítani. A táblázat szintű adatmegőrzési beállítások bevezetésének köszönhetően számos esetben már nincs szükség erre. | [Táblázat szintű adatmegőrzési beállítások](https://techcommunity.microsoft.com/t5/azure-sentinel/new-per-data-type-retention-is-now-available-for-azure-sentinel/ba-p/917316) használata vagy [az adatok törlésének](../azure-monitor/logs/personal-data-mgmt.md#how-to-export-and-delete-private-data) automatizálása |
| Felosztott számlázás | A munkaterületek külön előfizetésbe helyezésével különböző feleknek is kiszámlázható. | Használati jelentések készítése és kereszt-díjszámítás |
| Örökölt architektúra | Több munkaterület használata olyan korábbi kialakításokból eredhet, amelyek figyelembe vették a korlátozásokat vagy az ajánlott eljárásokat, amelyek nem rendelkeznek többé igaz értékűek. Lehet akár egy tetszőlegesen meghozott döntés eredménye is, amely módosítható, hogy jobban megfeleljen az Azure Sentinelhez.<br><br>Példák:<br><ul><li>Előfizetés alapértelmezett munkaterületének használata Azure Security Center telepítésekor</li><li>A részletes hozzáférés-vezérlési vagy adatmegőrzési beállítások szükségessége, amelyek viszonylag új megoldások</li></ul> | A munkaterületek architektúrájának újratervezése |

### <a name="managed-security-service-provider-mssp"></a>Felügyelt biztonsági szolgáltató (MSSP)

Egy adott használati eset, amely több munkaterületre bízza a MSSP Azure Sentinel szolgáltatást. Ebben az esetben sok, ha nem az összes fenti követelmény vonatkozik rá, így több munkaterületet is felhasználhat a bérlők között, az ajánlott eljárással. A MSSP az Azure- [világítótorony](../lighthouse/overview.md) használatával kiterjesztheti az Azure Sentinel több-munkaterület képességeit a bérlők között.

## <a name="azure-sentinel-multiple-workspace-architecture"></a>Több-munkaterületes Azure Sentinel-architektúra

A fenti követelményeknek megfelelően vannak olyan esetek, amikor több Azure Sentinel-munkaterületet, potenciálisan Azure Active Directory (Azure AD-) bérlőket kell központilag figyelni és kezelni egyetlen SOC-vel.

- Egy MSSP Azure Sentinel szolgáltatás.

- Egy globális SOC, amely több leányvállalatot is kínál, amelyek mindegyike saját helyi SOC-t használ.

- Egy vállalaton belül több Azure AD-bérlőt tartalmazó SOC-figyelés.

Ennek a követelménynek a megoldásához az Azure Sentinel több munkaterület-képességet kínál, amelyek lehetővé teszik a központi monitorozást, konfigurálást és felügyeletet, és egyetlen panelt biztosítanak a SOC által érintett összes, az alábbi ábrán látható módon.

:::image type="content" source="media/extend-sentinel-across-workspaces-tenants/cross-workspace-architecture.png" alt-text="Több munkaterület architektúrája":::

Ez a modell jelentős előnyt nyújt egy teljes körűen központosított modellhez, amelyben az összes adat egyetlen munkaterületre van másolva:

- Rugalmas szerepkör-hozzárendelés a globális és helyi SOCs, vagy a MSSP ügyfelei számára.

- Kevesebb kihívást jelent az adatok tulajdonlása, az adatok védelme és a szabályozások megfelelősége.

- Minimális hálózati késés és díjak.

- Egyszerű bevezetést és offboarding az új leányvállalatok vagy ügyfelek számára.

A következő szakaszokban bemutatjuk, hogyan működik a modell, és különösen hogyan:

- Központilag figyelheti a több munkaterületet a bérlők között, így biztosítva a SOC-t egyetlen üvegtábla formájában.

- Az Automation használatával központilag konfigurálhatja és kezelheti több munkaterületet, akár a bérlők között.

## <a name="cross-workspace-monitoring"></a>Több munkaterület figyelése

### <a name="manage-incidents-on-multiple-workspaces"></a>Incidensek kezelése több munkaterületen

Az Azure Sentinel több [munkaterület incidens-nézetet](./multiple-workspace-view.md) támogat, amely megkönnyíti a központi incidensek figyelését és felügyeletét több munkaterületen. A központosított incidens nézet lehetővé teszi az incidensek közvetlen kezelését vagy az incidensek részletes részletezését a kezdeményező munkaterület kontextusában.

### <a name="cross-workspace-querying"></a>Több munkaterület lekérdezése

Az Azure Sentinel több munkaterület lekérdezését támogatja [egyetlen lekérdezésben](../azure-monitor/logs/cross-workspace-query.md), így egyetlen lekérdezésben kereshet és korrelálhat több munkaterületről származó adatokkal. 

- A [munkaterület () kifejezés](../azure-monitor/logs/workspace-expression.md) használatával egy másik munkaterületen található táblára hivatkozhat. 
- A munkaterületek () kifejezés mellett használja a [Union operátort](/azure/data-explorer/kusto/query/unionoperator?pivots=azuremonitor) , hogy több munkaterület tábláin is alkalmazza a lekérdezéseket.

A mentett [függvények](../azure-monitor/logs/functions.md) segítségével egyszerűbbé teheti a több munkaterülettel kapcsolatos lekérdezéseket. Ha például egy munkaterületre mutató hivatkozás hosszú, érdemes lehet menteni a kifejezést `workspace("customer-A's-hard-to-remember-workspace-name").SecurityEvent` egy nevű függvényként `SecurityEventCustomerA` . Ezután a lekérdezéseket is írhat `SecurityEventCustomerA | where ...` .

Egy függvény is egyszerűsítheti a gyakran használt Uniót. Például mentheti a következő kifejezést egy nevű függvényként `unionSecurityEvent` :

`union workspace(“hard-to-remember-workspace-name-1”).SecurityEvent, workspace(“hard-to-remember-workspace-name-2”).SecurityEvent`

Ezután megírhat egy lekérdezést mindkét munkaterületre a-től kezdődően `unionSecurityEvent | where ...` .

#### <a name="cross-workspace-analytics-rules"></a>Több-munkaterület elemzési szabályai<a name="scheduled-alerts"></a>
<!-- Bookmark added for backward compatibility with old heading -->
Az ütemezett elemzési szabályokban mostantól több munkaterület-lekérdezés is szerepelhet, a következő korlátozásokkal:

- Akár 20 munkaterület is szerepelhet egyetlen lekérdezésben.
- Az Azure Sentinel szolgáltatást a lekérdezésben hivatkozott összes munkaterületre telepíteni kell.

> [!NOTE] 
> Ha több munkaterületet kérdez le ugyanabban a lekérdezésben, az hatással lehet a teljesítményre, ezért csak akkor javasolt, ha a logikához szükség van erre a funkcióra.

#### <a name="cross-workspace-workbooks"></a>Több munkaterületet tartalmazó munkafüzetek<a name="using-cross-workspace-workbooks"></a>
<!-- Bookmark added for backward compatibility with old heading -->
A [munkafüzetek](./overview.md#workbooks) irányítópultokat és alkalmazásokat biztosítanak az Azure Sentinel számára. Több munkaterülettel végzett munka esetén a munkaterületeken figyelést és műveleteket biztosítanak.

A munkafüzetek a három módszer egyikében biztosíthatnak több munkaterületet érintő lekérdezéseket, amelyek mindegyike különböző szintű végfelhasználói szakértelmet tartalmaz:

| Metódus  | Leírás | Mikor érdemes használni? |
|---------|-------------|--------------------|
| Több munkaterületet tartalmazó lekérdezések írása | A munkafüzet létrehozója a munkafüzetben több munkaterület-lekérdezést is írhat (lásd fent). | Ez a beállítás lehetővé teszi, hogy a munkafüzet-készítők teljes mértékben védjék a felhasználót a munkaterület struktúrájától. |
| Munkaterület-választó hozzáadása a munkafüzethez | A munkafüzet létrehozója a munkafüzet részeként implementálhatja a munkaterület-választót az [itt](https://techcommunity.microsoft.com/t5/azure-sentinel/making-your-azure-sentinel-workbooks-multi-tenant-or-multi/ba-p/1402357)leírtak szerint. | Ez a beállítás lehetővé teszi a felhasználó számára a munkafüzet által megjelenített munkaterületek vezérlését egy könnyen használható legördülő lista segítségével. |
| A munkafüzet interaktív szerkesztése | Egy meglévő munkafüzetet módosító speciális felhasználó szerkesztheti a lekérdezéseket, és kiválasztja a kívánt munkaterületeket a szerkesztő munkaterület-választójának használatával. | Ez a beállítás lehetővé teszi, hogy a Kiemelt felhasználók könnyedén módosíthassák a meglévő munkafüzeteket több munkaterülettel való együttműködéshez. |
|

#### <a name="cross-workspace-hunting"></a>Munkaterületek közötti vadászat

Az Azure Sentinel előre feltöltött lekérdezési mintákat biztosít a kezdéshez, és megismerheti a táblákat és a lekérdezési nyelvet. Ezeket a beépített vadászati lekérdezéseket folyamatosan fejlesztjük a Microsoft biztonsági kutatói, az új lekérdezések hozzáadásával és a meglévő lekérdezések finomhangolásával, így lehetővé teszi az új észlelések keresését és az esetlegesen a biztonsági eszközök által észrevétlenül felderített behatolási jelek azonosítását.  

A több munkaterületre kiterjedő vadászati funkciók lehetővé teszik, hogy a fenyegetésekkel kapcsolatos vadászok új vadászati lekérdezéseket hozzanak létre, vagy meglévőket igazítsanak a több munkaterület lefedéséhez, a Union operátor és a munkaterület () kifejezés használatával.

## <a name="cross-workspace-management-using-automation"></a>Több munkaterület közötti felügyelet az Automation használatával

Több Azure Sentinel-munkaterület konfigurálásához és kezeléséhez automatizálnia kell az Azure Sentinel felügyeleti API használatát. További információ az Azure Sentinel-erőforrások üzembe helyezésének automatizálásáról, beleértve a riasztási szabályokat, a vadászati lekérdezéseket, a munkafüzeteket és a forgatókönyveket, lásd: az [Azure Sentinel kiterjesztése: API-k, integráció és felügyelet automatizálása](https://techcommunity.microsoft.com/t5/azure-sentinel/extending-azure-sentinel-apis-integration-and-management/ba-p/1116885).

Lásd még: az Azure [Sentinel üzembe helyezése és kezelése](https://techcommunity.microsoft.com/t5/azure-sentinel/deploying-and-managing-azure-sentinel-as-code/ba-p/1131928) , valamint az [Azure-világítótorony és az Azure Sentinel együttes használata](https://techcommunity.microsoft.com/t5/azure-sentinel/combining-azure-lighthouse-with-sentinel-s-devops-capabilities/ba-p/1210966) a konszolidált, a Közösség által az Azure Sentinel DevOps való kezeléséhez, valamint az erőforrások saját GitHub-tárházból történő üzembe helyezéséhez és konfigurálásához. 

## <a name="managing-workspaces-across-tenants-using-azure-lighthouse"></a>Munkaterületek kezelése a bérlők között az Azure Lighthouse használatával

A fentiekben említettek szerint a különböző Azure Sentinel-munkaterületek különböző Azure AD-bérlők között lehetnek. Az [Azure Lighthouse](../lighthouse/overview.md) használatával kiterjesztheti a több munkaterületre kiterjedő tevékenységeket a bérlői határokon belül, így az Ön által kezelt bérlő felhasználói az összes bérlőre kiterjedő Azure Sentinel-munkaterületeken is dolgozhatnak. Az Azure Lighthouse bevezetését követően használja a Azure Portal [Directory + előfizetés választóját](./multiple-tenants-service-providers.md#how-to-access-azure-sentinel-in-managed-tenants) a felügyelni kívánt munkaterületeket tartalmazó összes előfizetés kiválasztásához [, hogy](../lighthouse/how-to/onboard-customer.md)azok mind elérhetők legyenek a portál különböző munkaterület-választói között.

Az Azure Lighthouse használata esetén ajánlott minden egyes Azure Sentinel-szerepkörhöz csoportot létrehozni, és az egyes bérlők engedélyeit delegálni ezekre a csoportokra.

## <a name="next-steps"></a>Következő lépések
Ebből a dokumentumból megtudta, hogyan bővíthető az Azure Sentinel képességei több munkaterületre és bérlőre. Az Azure Sentinel több-munkaterület architektúrájának megvalósításával kapcsolatos gyakorlati útmutatásért tekintse meg a következő cikkeket:

- Ismerje meg, hogyan [dolgozhat több Bérlővel](./multiple-tenants-service-providers.md) az Azure sentinelben az Azure Lighthouse használatával.
- Megtudhatja, hogyan [tekintheti meg és kezelheti az incidenseket több munkaterületen](./multiple-workspace-view.md) zökkenőmentesen.
