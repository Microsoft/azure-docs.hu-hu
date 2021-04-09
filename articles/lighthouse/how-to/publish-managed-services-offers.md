---
title: Felügyelt szolgáltatás ajánlatának közzététele az Azure Marketplace-en
description: Megtudhatja, hogyan tehet közzé egy felügyelt szolgáltatási ajánlatot, amely az ügyfeleket az Azure Lighthouse-be.
ms.date: 02/17/2021
ms.topic: how-to
ms.openlocfilehash: b906ba9961ae4f77ca16890fd0a9036123343111
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101092146"
---
# <a name="publish-a-managed-service-offer-to-azure-marketplace"></a>Felügyelt szolgáltatás ajánlatának közzététele az Azure Marketplace-en

Ebből a cikkből megtudhatja, hogyan tehet közzé nyilvános vagy privát felügyelt szolgáltatást az [Azure Marketplace](https://azuremarketplace.microsoft.com) -en a partner Center [kereskedelmi piactér](../../marketplace/overview.md) programjának használatával. Az ajánlatot megvásároló ügyfelek ezután az előfizetéseket vagy erőforráscsoportokat delegálják, így Ön felügyelheti őket az [Azure Lighthouse](../overview.md)használatával.

## <a name="publishing-requirements"></a>Közzétételi követelmények

Ajánlatok létrehozásához és közzétételéhez érvényes fiókkal kell rendelkeznie a [partner Centerben](../../marketplace/partner-center-portal/create-account.md) . Ha még nem rendelkezik fiókkal, a [regisztrációs folyamat](https://aka.ms/joinmarketplace) végigvezeti a fiók létrehozásának lépésein a partner Centerben és a kereskedelmi piactér programban való regisztrálásban.

A [felügyelt szolgáltatásokra vonatkozó minősítési követelmények](/legal/marketplace/certification-policies#700-managed-services)alapján a felügyelt szolgáltatások ajánlatának közzétételéhez egy [Silver vagy Gold Cloud platform kompetenciával](/partner-center/learn-about-competencies) vagy [Azure szakértői MSP](https://partner.microsoft.com/membership/azure-expert-msp) -vel kell rendelkeznie. Meg kell [adnia egy érdeklődő célhelyet is, amely egy rekordot hoz létre a CRM-rendszerben](../../marketplace/plan-managed-service-offer.md#customer-leads) minden alkalommal, amikor egy ügyfél üzembe helyezi az ajánlatot.

Ha nem szeretne ajánlatot közzétenni az Azure Marketplace-en, vagy nem teljesíti az összes követelményt, manuálisan is bevezetheti az ügyfeleket Azure Resource Manager sablonok használatával. További információ: az ügyfél beléptetése [Az Azure világítótoronyba](onboard-customer.md).

Az alábbi táblázat segítségével meghatározhatja, hogy az ügyfelek felügyelt szolgáltatási ajánlat közzétételével vagy Azure Resource Manager sablonok használatával legyenek bevezetve.

|**Megfontolandó**  |**Felügyelt szolgáltatás ajánlata**  |**ARM-sablonok**  |
|---------|---------|---------|
|[Partner Center-fiók](../../marketplace/partner-center-portal/create-account.md) szükséges   |Igen         |Nem        |
|[Silver vagy Gold Cloud Platform kompetencia](/partner-center/learn-about-competencies) vagy [Azure Expert MSP](https://partner.microsoft.com/membership/azure-expert-msp) szükséges      |Igen         |Nem         |
|Elérhető az új ügyfelek számára az Azure Marketplace-en keresztül     |Igen     |Nem       |
|Korlátozhatja az ajánlatokat adott ügyfelek számára     |Igen (csak privát ajánlatokkal, amelyek nem használhatók a Cloud Solution Provider (CSP) program viszonteladóján keresztül létesített előfizetésekkel.         |Yes         |
|Ügyfél-elfogadás szükséges a Azure Portal     |Igen     |Nem   |
|Az Automation használatával több előfizetést, erőforráscsoportot vagy ügyfelet helyezhet üzembe |Nem     |Igen    |
|Azonnali hozzáférés az új beépített szerepkörökhöz és az Azure Lighthouse szolgáltatásaihoz     |Nem mindig (általánosan elérhető némi késés után)         |Yes         |

> [!NOTE]
> Előfordulhat, hogy a felügyelt szolgáltatási ajánlatok nem érhetők el Azure Government és más nemzeti felhőkben.

## <a name="create-your-offer"></a>Ajánlat létrehozása

Az ajánlat létrehozásával kapcsolatos részletes utasításokért, beleértve az összes szükséges információt és eszközt, tekintse meg [a felügyelt szolgáltatási ajánlat létrehozása](../../marketplace/create-managed-service-offer.md)című témakört.

Ha szeretne többet megtudni az általános közzétételi folyamatról, tekintse át a [kereskedelmi piactér dokumentációját](../../marketplace/overview.md). Tekintse át a [kereskedelmi piactérre vonatkozó minősítési szabályzatokat](/legal/marketplace/certification-policies), különösen a [felügyelt szolgáltatások](/legal/marketplace/certification-policies#700-managed-services) szakaszt.

Ha egy ügyfél felveszi az ajánlatot, egy vagy több előfizetést vagy erőforráscsoportot is delegálhat, amelyek ezután bekerülnek [Az Azure világítótoronyba](#the-customer-onboarding-process).

> [!IMPORTANT]
> A felügyelt szolgáltatási ajánlat minden csomagja tartalmaz egy **jegyzékfájl részleteit** tartalmazó szakaszt, ahol megadhatja a bérlő azon Azure Active Directory (Azure ad) entitásait, amelyek hozzáférnek a megvásárolt erőforrásokhoz és/vagy előfizetésekhez a tervet megvásárló ügyfelek számára. Fontos megjegyezni, hogy az Ön által belefoglalt bármely csoport (vagy felhasználó vagy szolgáltatásnév) ugyanazokkal az engedélyekkel fog rendelkezni minden olyan ügyfélnél, aki megvásárolja a csomagot. Ha különböző csoportokat szeretne hozzárendelni az egyes ügyfelekkel való munkához, közzétehet egy külön [privát csomagot](../../marketplace/private-offers.md) , amely kizárólag az egyes ügyfelek számára érhető el. Ne feledje, hogy a Private-csomagok nem támogatottak a Cloud Solution Provider (CSP) program viszonteladóján keresztül létrehozott előfizetésekkel.

## <a name="publish-your-offer"></a>Ajánlat közzététele

Miután elvégezte az összes szakaszt, a következő lépés az ajánlat közzététele az Azure Marketplace-en. Válassza a **Közzététel** gombot, hogy elindítsa az ajánlatát élőben. A folyamatról további információt [itt](../../marketplace/review-publish-offer.md)találhat.

Bármikor [közzéteheti az ajánlat frissített verzióját](../..//marketplace/partner-center-portal/update-existing-offer.md) . Előfordulhat például, hogy új szerepkör-definíciót szeretne hozzáadni egy korábban közzétett ajánlathoz. Ha így tesz, a már hozzáadott ügyfelek egy ikont láthatnak a Azure Portal [**szolgáltatók**](view-manage-service-providers.md) lapján, amely lehetővé teszi, hogy a frissítés elérhető legyen. Minden ügyfél [áttekintheti a módosításokat](view-manage-service-providers.md#update-service-provider-offers) , és eldöntheti, hogy szeretné-e frissíteni az új verzióra. 

## <a name="the-customer-onboarding-process"></a>Az ügyfél bevezetési folyamata

Miután egy ügyfél felveszi az ajánlatot, [egy vagy több konkrét előfizetést vagy erőforráscsoportot delegálhat](view-manage-service-providers.md#delegate-resources), amelyek ezután az Azure Lighthouse-be lesznek bevezetve. Ha egy ügyfél elfogadta az ajánlatot, de még nem delegált erőforrást, akkor a Azure Portal [**szolgáltatók**](view-manage-service-providers.md) lapján a **szolgáltatói ajánlatok** szakaszának felső részén egy megjegyzés jelenik meg.

> [!IMPORTANT]
> A delegálást olyan nem vendég fiókkal kell végrehajtania az ügyfél bérlője számára, aki rendelkezik a bevezetéshez `Microsoft.Authorization/roleAssignments/write` szükséges engedéllyel (például [tulajdonossal](../../role-based-access-control/built-in-roles.md#owner)) az előfizetéshez tartozó szerepkörrel (vagy amely tartalmazza az előkészítés alatt álló erőforráscsoportokat). Az előfizetést delegáló felhasználók megkereséséhez az ügyfél bérlője számára kiválaszthatja az előfizetést a Azure Portalban, megnyithatja a **hozzáférés-vezérlés (iam)** lehetőséget, és [megtekintheti a tulajdonosi szerepkörrel rendelkező összes felhasználót](../../role-based-access-control/role-assignments-list-portal.md#list-owners-of-a-subscription).

Miután az ügyfél delegált egy előfizetést (vagy egy vagy több erőforráscsoportot egy előfizetésen belül), a **Microsoft. ManagedServices** erőforrás-szolgáltató regisztrálva lesz az adott előfizetéshez, és a bérlő felhasználói hozzáférhetnek a delegált erőforrásokhoz az ajánlatban foglalt jogosultságok alapján.

## <a name="next-steps"></a>Következő lépések

- További információ a [kereskedelmi piactérről](../../marketplace/overview.md).
- [Kapcsolja össze partneri azonosítóját](partner-earned-credit.md) , hogy nyomon követhesse az ügyfelek bevonásait.
- További információ a [bérlők közötti felügyeleti élményekről](../concepts/cross-tenant-management-experience.md).
- [Megtekintheti és kezelheti az ügyfeleket](view-manage-customers.md) a Azure Portalban lévő **ügyfelekkel** .
