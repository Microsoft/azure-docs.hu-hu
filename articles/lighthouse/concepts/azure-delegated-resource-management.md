---
title: Az Azure által delegált erőforrás-kezelés
description: Az Azure-beli delegált erőforrás-kezelés kulcsfontosságú része a Azure Lighthouse, lehetővé téve a szolgáltatók számára a delegált erőforrások nagy léptékben, agilis és precíz kezelését.
ms.date: 10/19/2020
ms.topic: conceptual
ms.openlocfilehash: 93a3de257fe88de4915eff3582ff38fc03ef380e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767762"
---
# <a name="azure-delegated-resource-management"></a>Az Azure által delegált erőforrás-kezelés

Az Azure-beli delegált erőforrás-kezelés az egyik fő összetevője a [Azure Lighthouse.](../overview.md) Az Azure-beli delegált erőforrás-kezelés segítségével a szolgáltatók leegyszerűsítik az ügyfelek bevonása és a bevezetés élményét, miközben a delegált erőforrásokat nagy méretekben, agilis és precízen kezelik. Az ügyfelek szabályozni tudják, hogy ki férhet hozzá a bérlőhöz, milyen erőforrásokhoz férhetnek hozzá, és milyen műveleteket lehet tenni.

## <a name="what-is-azure-delegated-resource-management"></a>Mi az az Azure által delegált erőforrás-kezelés?

Az Azure-beli delegált erőforrás-kezelés lehetővé teszi az erőforrások logikai leképezését az egyik bérlőről egy másik bérlőre. Ez lehetővé teszi, hogy az egyik Azure Active Directory (Azure AD-bérlő) jogosult felhasználói felügyeleti műveleteket végezzenek az ügyfeleikhez tartozó különböző Azure AD-bérlőkben. A szolgáltatók bejelentkezhet a saját Azure AD-bérlőjükbe, és engedélyt kaphatnak a delegált ügyfél-előfizetésekkel és erőforráscsoportokkal való munkára. Ez lehetővé teszi számukra, hogy felügyeleti műveleteket végezzenek az ügyfeleik nevében anélkül, hogy minden egyes ügyfélbérlőbe be kellene jelentkezniük.

> [!TIP]
> Az Azure-beli delegált erőforrás-kezelés olyan vállalaton belül is használható, amely több [Azure AD-bérlővel](enterprise.md) rendelkezik a bérlők közötti felügyelet egyszerűsítése érdekében.

Az Azure által delegált erőforrás-kezelés segítségével a jogosult felhasználók közvetlenül az ügyfél-előfizetés környezetében dolgozhatnak anélkül, hogy fiókkal rendelkező ügyfél bérlőjéhez vagy az ügyfél bérlőjéhez társtulajdonosa volna.

A [több-bérlős felügyeleti](cross-tenant-management-experience.md) funkcióval hatékonyabban dolgozhat olyan Azure-beli felügyeleti szolgáltatásokkal, mint Azure Policy, Azure Security Center stb. A rendszer minden szolgáltatói tevékenységet nyomon követ a tevékenységnaplóban, amelyet az ügyfél bérlője tárol (és a kezelő bérlő felhasználói megtekinthetik). Ez azt jelenti, hogy a felügyelet és a felügyelt bérlő felhasználói könnyen azonosítják a változásokhoz társított felhasználót.

[Közzéteheti az új felügyeltszolgáltatás-ajánlattípust, hogy Azure Marketplace](../how-to/publish-managed-services-offers.md) az ügyfeleket a Azure Lighthouse. Alternatív megoldásként az üzembe helyezési folyamatot is befejezheti, ha üzembe Azure Resource Manager [sablonokat.](../how-to/onboard-customer.md)

## <a name="how-azure-delegated-resource-management-works"></a>Az Azure által delegált erőforrás-kezelés működése

Az Azure által delegált erőforrás-kezelés működése magas szinten a következő:

1. Először meg kell határoznia a hozzáférést (szerepköröket), amelyekre a csoportoknak, szolgáltatásnévnek vagy felhasználóknak az ügyfél Azure-erőforrásainak kezeléséhez szükségük lesz. A hozzáférési definíció tartalmazza a kezelő bérlőazonosítót, valamint a bérlőből származó **principalId** identitásokat, amelyek beépített [ **roleDefinition** értékekre](../../role-based-access-control/built-in-roles.md) (Közreműködő, Virtuális gép közreműködője, Olvasó stb.) leképezve.
2. Ezt a hozzáférést kell megadnia, és az ügyfél Azure Lighthouse kétféleképpen:
   - [Az ügyfél által Azure Marketplace (privát](../how-to/publish-managed-services-offers.md) vagy nyilvános) felügyelt szolgáltatási ajánlat közzététele
   - [Üzembe helyezhet Azure Resource Manager sablont](../how-to/onboard-customer.md) az ügyfél bérlője számára egy vagy több adott előfizetéshez vagy erőforráscsoporthoz

3. Az ügyfél regisztrációja után az arra jogosult felhasználók bejelentkeznek a bérlőbe, és az Ön által meghatározott hozzáférés alapján az adott ügyfélhatókörben hajthat végre feladatokat. Az ügyfelek áttekinthetik a szolgáltatói műveleteket, és szükség esetén eltávolíthatják a hozzáférést.

> [!NOTE]
> A különböző régiókban található delegált erőforrásokat [kezelheti.](../../availability-zones/az-overview.md#regions) Az előfizetések delegálása azonban az országos felhőben és [az](../../active-directory/develop/authentication-national-cloud.md) Azure nyilvános felhőben, illetve két különálló országos felhőben nem támogatott.

## <a name="support-for-azure-delegated-resource-management"></a>Az Azure-beli delegált erőforrás-kezelés támogatása

Ha segítségre van szüksége az Azure által delegált erőforrás-kezeléshez, nyisson egy támogatási kérést a Azure Portal. A **Probléma típusaként válassza** a Technikai **lehetőséget.** Válasszon ki egy előfizetést, majd válassza a **Lighthouse** lehetőséget (a **Monitoring & Management alatt).**

## <a name="next-steps"></a>Következő lépések

- Ismerje meg [a bérlők közötti felügyeleti élményt.](cross-tenant-management-experience.md)
- További információ [a felügyelt szolgáltatási ajánlatokról a Azure Marketplace.](managed-services-offers.md)
