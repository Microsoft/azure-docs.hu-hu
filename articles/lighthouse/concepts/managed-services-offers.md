---
title: Felügyelt szolgáltatási ajánlatok az Azure Marketplace-en
description: A felügyelt szolgáltatás lehetővé teszi, hogy erőforrás-kezelési ajánlatokat értékesítsen az ügyfeleknek az Azure Marketplace-en.
ms.date: 02/17/2021
ms.topic: conceptual
ms.openlocfilehash: e66a87751e0375804031e777a40c5fc1d0e8a7e2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "101092997"
---
# <a name="managed-service-offers-in-azure-marketplace"></a>Felügyelt szolgáltatási ajánlatok az Azure Marketplace-en

Ez a cikk a **felügyelt szolgáltatások** ajánlatának típusát mutatja be az [Azure piactéren](https://azuremarketplace.microsoft.com). A felügyelt szolgáltatás lehetővé teszi, hogy erőforrás-kezelési szolgáltatásokat nyújtson az ügyfeleknek az [Azure Lighthouse](../overview.md)használatával. Ezeket az ajánlatokat az összes potenciális ügyfél számára elérhetővé teheti, vagy csak egy vagy több konkrét ügyfél számára. Mivel az ügyfeleket közvetlenül az ezekkel a felügyelt szolgáltatásokkal kapcsolatos költségekre számlázza, a Microsoft nem számít fel díjat.

## <a name="understand-managed-service-offers"></a>A felügyelt szolgáltatások ajánlatának ismertetése

A felügyelt szolgáltatás megkönnyíti az ügyfelek Azure világítótoronyba való bevezetésének folyamatát. Amikor egy ügyfél ajánlatot vásárol az Azure Marketplace-en, meg tudják határozni, hogy mely előfizetéseket és/vagy erőforráscsoportokat kell előkészíteni.

Ezt követően a cégen belüli felhasználók az ajánlat létrehozásakor meghatározott hozzáférésnek megfelelően az Azure-beli [delegált erőforrás-kezelésen](azure-delegated-resource-management.md)keresztül képesek lesznek dolgozni ezen erőforrásokon. Ez egy olyan jegyzékfájlon keresztül történik, amely megadja az ügyfelek erőforrásaihoz hozzáférő Azure Active Directory (Azure AD) felhasználókat, csoportokat és egyszerű szolgáltatásokat, valamint a hozzáférési szintet meghatározó [szerepköröket](tenants-users-roles.md) .

> [!NOTE]
> Előfordulhat, hogy a felügyelt szolgáltatási ajánlatok nem érhetők el Azure Government és más nemzeti felhőkben.

## <a name="public-and-private-offers"></a>Nyilvános és privát ajánlatok

Minden egyes felügyelt szolgáltatáshoz tartozik egy vagy több csomag. A csomagok lehetnek magán-vagy nyilvánosak is.

Ha korlátozni szeretné az ajánlatát bizonyos ügyfeleknek, közzétehet egy privát csomagot. Ha így tesz, a terv csak az Ön által megadott előfizetési azonosítók esetében vásárolható meg. További információ: [privát ajánlatok](../../marketplace/private-offers.md).

> [!NOTE]
> A nyilvános ajánlatok nem támogatottak a Cloud Solution Provider (CSP) program viszonteladóján keresztül létesített előfizetésekkel.

A nyilvános csomagok lehetővé teszik a szolgáltatások új ügyfelekhez való előléptetését. Ezek általában megfelelőbbek, ha csak korlátozott hozzáférésre van szüksége az ügyfél bérlője számára. Ha kapcsolatot létesített egy ügyféllel, ha úgy dönt, hogy további hozzáférést biztosítanak a szervezetnek, akkor ezt akár egy új privát csomag közzétételével is megteheti, akár [Azure Resource Manager-sablonok használatával történő további hozzáféréshez](../how-to/onboard-customer.md).

Ha szükséges, a nyilvános és a saját csomagok is felhasználhatók ugyanabban az ajánlatban.

> [!IMPORTANT]
> Miután közzétett egy csomagot nyilvánosként, nem módosíthatja magánjellegűre. Egy privát csomag használatával szabályozhatja, hogy mely ügyfelek fogadhatják el az ajánlatot, és hogyan delegálhat erőforrásokat. Egy nyilvános csomaggal nem korlátozhatja a rendelkezésre állást bizonyos ügyfelek számára, vagy akár bizonyos számú ügyfél számára is (bár ha úgy dönt, hogy teljesen megszüntetheti a csomag értékesítését). A [delegáláshoz való hozzáférést csak akkor távolíthatja el,](../how-to/remove-delegation.md) ha az ügyfél csak akkor fogadja el az **ajánlatot, ha** az ajánlat közzétételekor a [felügyelt szolgáltatások regisztrációs hozzárendelésének törlési szerepkörét](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) beállította a **szerepkör-definícióban** . Az ügyfelet is elérheti, és megkérheti, hogy [távolítsa el a hozzáférését](../how-to/view-manage-service-providers.md#add-or-remove-service-provider-offers).

## <a name="publish-managed-service-offers"></a>Felügyelt szolgáltatás-ajánlatok közzététele

A felügyelt szolgáltatások ajánlatának közzétételével kapcsolatos további információkért lásd: [felügyelt szolgáltatás ajánlatának közzététele az Azure Marketplace](../how-to/publish-managed-services-offers.md)-en.

## <a name="next-steps"></a>Következő lépések

- Ismerje meg az Azure-beli [delegált erőforrás-kezelést](azure-delegated-resource-management.md) és a [több-bérlős felügyeleti tapasztalatokat](cross-tenant-management-experience.md).
- [Felügyelt szolgáltatásbeli ajánlatok közzététele](../how-to/publish-managed-services-offers.md) az Azure Marketplace-en.
