---
title: Azure Sentinel-munkaterületek kezelése nagy méretekben
description: Ismerje meg, hogyan kezelheti hatékonyan az Azure Sentinelt a delegált felhasználói erőforrásokon.
ms.date: 03/02/2021
ms.topic: how-to
ms.openlocfilehash: 009edaefe021dedb5d9a40a8cc3bac2c2974ae10
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101702521"
---
# <a name="manage-azure-sentinel-workspaces-at-scale"></a>Azure Sentinel-munkaterületek kezelése nagy méretekben

Szolgáltatóként több ügyfél-bérlőt is felkészített az [Azure világítótoronyba](../overview.md). Az Azure Lighthouse lehetővé teszi a szolgáltatók számára, hogy egyszerre több Azure Active Directory (Azure AD) bérlőre kiterjedő műveleteket hajtsanak végre, így hatékonyabbá téve a felügyeleti feladatokat.

Az Azure Sentinel biztonsági elemzési és veszélyforrási intelligenciát biztosít, így egyetlen megoldást nyújt a riasztások észlelésére, a fenyegetések láthatóságára, a proaktív vadászatra és a fenyegetésekre való reagálásra. Az Azure Lighthouse használatával több Azure Sentinel-munkaterületet is kezelhet a bérlők között a skálán. Ez olyan forgatókönyveket tesz lehetővé, mint például a lekérdezések futtatása több munkaterületen, illetve munkafüzetek létrehozása a csatlakoztatott adatforrásokból származó adatok megjelenítéséhez és figyeléséhez. Az IP-címek, például a lekérdezések és a forgatókönyvek megmaradnak a kezelő bérlőben, de az ügyfél-bérlők biztonsági felügyeletét is elvégezheti.

Ez a témakör áttekintést nyújt arról, hogyan használható az [Azure Sentinel](../../sentinel/overview.md) skálázható módon a több-bérlős láthatóság és a felügyelt biztonsági szolgáltatások számára.

> [!TIP]
> Bár a jelen témakörben a szolgáltatók és az ügyfelekre is hivatkozunk, ez az útmutató az [Azure Lighthouse-t használó vállalatoknak is vonatkozik több bérlő kezelésére](../concepts/enterprise.md).

## <a name="architectural-considerations"></a>Építészeti megfontolások

Egy olyan felügyelt biztonsági szolgáltató (MSSP) esetében, amely az Azure Sentinel használatával szeretné kiépíteni a szolgáltatásként nyújtott biztonsági szolgáltatást, egyetlen biztonsági operatív központot (SOC) lehet szükség az egyes ügyfelek bérlői számára üzembe helyezett Azure Sentinel-munkaterületek központilag figyelésére, kezelésére és konfigurálására. Hasonlóképpen, a több Azure AD-Bérlővel rendelkező vállalatok központilag kezelhetik több, a bérlők között üzembe helyezett Azure Sentinel-munkaterületet.

Ez a központi telepítési modell a következő előnyökkel jár:

- Az adattulajdonos az egyes felügyelt bérlők esetében is megmarad.
- A támogatja a földrajzi határokon belül tárolt adattárolási követelményeket.
- Biztosítja az adatelkülönítést, mivel a több ügyfélhez tartozó adategységek nem ugyanabban a munkaterületen vannak tárolva.
- Megakadályozza a felügyelt bérlők adatainak kiszűrése, így biztosítva az adatok megfelelőségét.
- A kapcsolódó költségeket a bérlők kezelése helyett az egyes felügyelt bérlők terhelik.
- Az összes adatforrásból és adatösszekötőből származó, az Azure Sentinel szolgáltatással integrált adatok (például az Azure AD-tevékenységek naplói, az Office 365-naplók vagy a Microsoft Threat Protection-riasztások) az egyes ügyfelek bérlőn belül maradnak.
- Csökkenti a hálózati késést.
- Egyszerűen hozzáadhat vagy eltávolíthat új leányvállalatokat vagy ügyfeleket.

> [!NOTE]
> A különböző [régiókban](../../availability-zones/az-overview.md#regions)található delegált erőforrásokat is kezelheti. Azonban az előfizetések delegálása egy [nemzeti felhőben](../../active-directory/develop/authentication-national-cloud.md) és az Azure nyilvános felhőben, vagy két külön nemzeti felhők között nem támogatott.

## <a name="granular-azure-role-based-access-control-azure-rbac"></a>Részletes Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)

Minden ügyfél-előfizetést, amelyet egy MSSP fog kezelni, be kell készíteni [Az Azure világítótoronyba](onboard-customer.md). Ez lehetővé teszi a bérlők számára, hogy az ügyfél-bérlők számára üzembe helyezett Azure Sentinel-munkaterületeken felügyeleti műveleteket férhessenek hozzá és végezzenek el.

Az engedélyek létrehozásakor az Azure Sentinel beépített szerepköreit hozzárendelheti a felhasználókhoz, csoportokhoz vagy egyszerű szolgáltatásokhoz a kezelő bérlőben:

- [Azure Sentinel-olvasó](../../role-based-access-control/built-in-roles.md#azure-sentinel-reader)
- [Azure Sentinel-válaszadó](../../role-based-access-control/built-in-roles.md#azure-sentinel-responder)
- [Azure Sentinel közreműködő](../../role-based-access-control/built-in-roles.md#azure-sentinel-contributor)

További beépített szerepköröket is szeretne rendelni további függvények végrehajtásához. További információ az Azure Sentinel használatával használható szerepkörökről: [engedélyek az Azure sentinelben](../../sentinel/roles.md).

Az ügyfelek bevezetését követően a kijelölt felhasználók bejelentkezhetnek a kezelő bérlőbe, és [közvetlenül hozzáférhetnek az ügyfél Azure Sentinel-munkaterületéhez](../../sentinel/multiple-tenants-service-providers.md) a hozzárendelt szerepkörökkel.

## <a name="view-and-manage-incidents-across-workspaces"></a>Incidensek megtekintése és kezelése munkaterületek között

Ha több ügyfél számára kezel Azure Sentinel-erőforrásokat, egyszerre több bérlőn is megtekintheti és kezelheti az incidenseket több munkaterületen. További információ: az [incidensek használata egyszerre sok munkaterületen](../../sentinel/multiple-workspace-view.md) , és az [Azure Sentinel kibővítése a munkaterületeken és a bérlők között](../../sentinel/extend-sentinel-across-workspaces-tenants.md).

> [!NOTE]
> Győződjön meg arról, hogy a felügyeleti bérlő felhasználói olvasási és írási engedélyekkel rendelkeznek a felügyelt munkaterületekhez. Ha egy felhasználó csak olvasási jogosultsággal rendelkezik egyes munkaterületeken, figyelmeztető üzenetek jelenhetnek meg az incidensek kiválasztásakor az adott munkaterületeken, és a felhasználó nem módosíthatja ezeket az incidenseket, vagy bármely más Ön által választott elemet (még akkor is, ha rendelkezik a többire vonatkozó engedélyekkel).

## <a name="configure-playbooks-for-mitigation"></a>Forgatókönyvek beállítása a mérsékléshez

A forgatókönyvek a riasztások aktiválásakor automatikus mérséklésre [használhatók](../../sentinel/tutorial-respond-threats-playbook.md) . Ezek a forgatókönyvek manuálisan is futtathatók, vagy automatikusan futtathatók, ha adott riasztásokat indítanak el. A forgatókönyvek a bérlő vagy az ügyfél bérlője számára is üzembe helyezhetők, és azok a válaszadási eljárások, amelyek alapján a bérlő felhasználói a biztonsági fenyegetésekre reagálva műveleteket hajtanak végre.

## <a name="create-cross-tenant-workbooks"></a>Több-bérlős munkafüzetek létrehozása

[Azure monitor munkafüzetek az Azure sentinelben](../../sentinel/overview.md#workbooks) a csatlakoztatott adatforrásokból származó adatok vizualizációját és figyelését teszik lehetővé az elemzések megszerzéséhez. Használhatja az Azure Sentinel beépített munkafüzet-sablonjait, vagy létrehozhat egyéni munkafüzeteket a forgatókönyvekhez.

A felügyeleti bérlőben munkafüzetek helyezhetők üzembe, és méretezhető irányítópultokat hozhat létre, amelyekkel figyelheti és lekérdezheti az ügyfelek bérlői közötti adatait. További információ: több [munkaterület figyelése](../../sentinel/extend-sentinel-across-workspaces-tenants.md#using-cross-workspace-workbooks). 

A munkafüzeteket közvetlenül is üzembe helyezheti egy egyéni bérlőben, amelyet az adott ügyfélhez tartozó forgatókönyvek esetében kezel.

## <a name="run-log-analytics-and-hunting-queries-across-azure-sentinel-workspaces"></a>Log Analytics-és vadászati lekérdezések futtatása az Azure Sentinel-munkaterületeken

Hozzon létre és mentsen Log Analytics-lekérdezéseket a fenyegetés észleléséhez központilag a bérlő kezelése, beleértve a [vadászati lekérdezéseket](../../sentinel/extend-sentinel-across-workspaces-tenants.md#cross-workspace-hunting). Ezeket a lekérdezéseket ezután a Union operátor és a munkaterület () kifejezés használatával lehet futtatni az összes ügyfél Azure Sentinel-munkaterületén. További információ: több [munkaterület lekérdezése](../../sentinel/extend-sentinel-across-workspaces-tenants.md#cross-workspace-querying).

## <a name="use-automation-for-cross-workspace-management"></a>Automatizálás használata a munkaterületek közötti felügyelethez

Az Automation használatával több Azure Sentinel-munkaterület is kezelhető, és a [vadászati lekérdezések](../../sentinel/hunting.md), a forgatókönyvek és a munkafüzetek is konfigurálhatók. További információ: a [munkaterület-kezelés automatizálással](../../sentinel/extend-sentinel-across-workspaces-tenants.md#cross-workspace-management-using-automation).

## <a name="monitor-security-of-office-365-environments"></a>Office 365-környezetek biztonságának figyelése

Az Azure-világítótornyot az Azure Sentineltel együtt használva figyelheti az Office 365 környezetek biztonságát a bérlők között. Első lépésként a Box [Office 365 adatösszekötőket engedélyezni kell a felügyelt bérlőben](../../sentinel/connect-office-365.md) , hogy az Exchange-ben és a SharePointban (beleértve a OneDrive-t) használó felhasználói és rendszergazdai tevékenységekkel kapcsolatos információk betölthetők legyenek egy Azure Sentinel-munkaterületre a felügyelt bérlőn belül. Ide tartozik a fájlok letöltésével, a küldött hozzáférési kérelmekkel, az események csoportosításával és a postaláda-műveletekkel kapcsolatos részletek, valamint a műveleteket végrehajtó felhasználók információi. Az [office 365 DLP-riasztások](https://techcommunity.microsoft.com/t5/azure-sentinel/ingest-office-365-dlp-events-into-azure-sentinel/ba-p/1031820) a beépített Office 365-összekötők részeként is támogatottak.

A [Microsoft Cloud app Security (MCAS) összekötőt](../../sentinel/connect-cloud-app-security.md) engedélyezheti a stream-riasztásokhoz, és Cloud Discovery naplókat az Azure sentinelbe. Ez lehetővé teszi a felhőalapú alkalmazások láthatóságát, a kifinomult elemzéseket a előforduló kiberfenyegetésekkel kapcsolatban azonosítására és leküzdésére, valamint az adatmozgások szabályozására. A MCAS tartozó Tevékenységnaplók [a Common Event Format (CEF) használatával](https://techcommunity.microsoft.com/t5/azure-sentinel/ingest-box-com-activity-events-via-microsoft-cloud-app-security/ba-p/1072849)használhatók fel.

Az Office 365-adatösszekötők beállítása után használhatja a több-bérlős Azure Sentinel-funkciókat, például a munkafüzetek adatainak megtekintését és elemzését, a lekérdezések használatával egyéni riasztásokat hozhat létre, és konfigurálhat forgatókönyveket a fenyegetésekre való reagáláshoz.

## <a name="next-steps"></a>Következő lépések

- További információ az [Azure sentinelről](../../sentinel/overview.md).
- Tekintse át az [Azure Sentinel díjszabási oldalát](https://azure.microsoft.com/pricing/details/azure-sentinel/).
- További információ a [bérlők közötti felügyeleti élményekről](../concepts/cross-tenant-management-experience.md).

