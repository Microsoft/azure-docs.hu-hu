---
title: A felhőszolgáltatói program megfontolandó szempontjai
description: A CSP-partnerek esetében az Azure-beli delegált erőforrás-kezelés a részletes engedélyek engedélyezésével segíti a biztonságot és a szabályozást.
ms.date: 03/12/2021
ms.topic: conceptual
ms.openlocfilehash: 8736cf913739f2bd16fb519aed98fd336f6876a5
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103419388"
---
# <a name="azure-lighthouse-and-the-cloud-solution-provider-program"></a>Az Azure Lighthouse és a Cloud Solution Provider program

Ha Ön CSP- [(felhőalapú szolgáltatói)](/partner-center/csp-overview) partner, akkor már hozzáférhet az ÜGYFELEKNEK a CSP programon keresztül létrehozott Azure-előfizetésekhez a (z [) (AOBO)](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO) funkció használatával. Ez a hozzáférés lehetővé teszi az ügyfelek előfizetésének közvetlen támogatását, konfigurálását és felügyeletét.

Az [Azure Lighthouse](../overview.md)használatával az Azure-beli delegált erőforrás-kezelést és az AOBO-et is használhatja. Ez segít javítani a biztonságot, és csökkenti a szükségtelen hozzáférést azáltal, hogy részletesebb engedélyeket biztosít a felhasználók számára. Emellett nagyobb hatékonyságot és méretezhetőséget is biztosít, mivel a felhasználók több ügyfél-előfizetésben is működhetnek, ha egyetlen bejelentkezést használnak a bérlőben.

> [!TIP]
> Az ügyfelek erőforrásainak védelme érdekében mindenképpen tekintse át és kövesse az [ajánlott biztonsági eljárásokat](recommended-security-practices.md) a [partneri biztonsági követelményekkel](/partner-center/partner-security-requirements)együtt.

## <a name="administer-on-behalf-of-aobo"></a>Felügyelet a következő nevében (AOBO)

A AOBO esetében a bérlő [rendszergazdai ügynök](/partner-center/permissions-overview#manage-commercial-transactions-in-partner-center-azure-ad-and-csp-roles) szerepkörrel rendelkező felhasználója a CSP-program keretében létrehozott Azure-előfizetésekhez is hozzáférhet. Minden olyan felhasználónak, akinek hozzáférésre van szüksége bármely ügyfél előfizetéséhez, a csoport tagjának kell lennie. A AOBO nem teszi lehetővé a rugalmasságot olyan különálló csoportok létrehozására, amelyek különböző ügyfelekkel működnek, vagy különböző szerepköröket engedélyeznek a csoportok vagy felhasználók számára.

![A bérlők felügyeletét a AOBO használatával bemutató ábra.](../media/csp-1.jpg)

## <a name="azure-delegated-resource-management"></a>Az Azure által delegált erőforrás-kezelés

Az Azure Lighthouse használatával különböző csoportokat rendelhet hozzá különböző ügyfelekhez vagy szerepkörökhöz, ahogy az alábbi ábrán is látható. Mivel a felhasználók a megfelelő szintű hozzáféréssel rendelkeznek az Azure-beli delegált erőforrás-kezelésen keresztül, csökkentheti a rendszergazdai ügynök szerepkörrel rendelkező felhasználók számát (és így teljes AOBO-hozzáféréssel rendelkezik). Ez segít a biztonság javításában azáltal, hogy korlátozza az ügyfelek erőforrásainak szükségtelen hozzáférését. Emellett nagyobb rugalmasságot biztosít több ügyfél felügyeletéhez.

A CSP programon keresztül létrehozott előfizetés bevezetése az [előfizetés Azure Lighthouse-be történő](../how-to/onboard-customer.md)bevezetésének lépéseit követi. A bérlő rendszergazdai ügynök szerepkörrel rendelkező felhasználója elvégezheti ezt a bevezetést.

![A bérlők felügyeletét bemutató ábra az AOBO és az Azure által delegált erőforrás-kezelés használatával.](../media/csp-2.jpg)

> [!TIP]
> A [felügyelt szolgáltatási ajánlatok](managed-services-offers.md) privát csomagokkal nem támogatottak a Cloud Solution Provider (CSP) program viszonteladóján keresztül létesített előfizetésekkel. Ezeket az előfizetéseket az Azure Lighthouse-be [Azure Resource Manager sablonok használatával](../how-to/onboard-customer.md)is előkészítheti.

> [!NOTE]
> A [Azure Portal **saját ügyfelek** lapján](../how-to/view-manage-customers.md) már szerepel a **Cloud Solution Provider (előzetes verzió)** szakasz, amely megjeleníti a számlázási adatokat és erőforrásokat a [Microsoft ügyfél-szerződést (MCA) aláírt](/partner-center/confirm-customer-agreement) CSP-ügyfelek számára, és [Az Azure-csomag alatt](/partner-center/azure-plan-get-started)található. További információ: Ismerkedés a [Microsoft partneri szerződés számlázási fiókjával](../../cost-management-billing/understand/mpa-overview.md).
>
> A CSP-ügyfelek ebben a szakaszban jelenhetnek meg, függetlenül attól, hogy az Azure Lighthouse-ban is szerepelnek-e. Ha vannak, akkor az **ügyfelek** szakaszban is megjelennek az ügyfelek [és a delegált erőforrások megtekintése és kezelése](../how-to/view-manage-customers.md)című témakörben leírtak szerint. Hasonlóképpen, a CSP-ügyfélnek nem kell megjelennie az **ügyfelek** **Cloud Solution Provider (előzetes verzió)** szakaszában, hogy az Azure világítótoronyba bekerüljön.

## <a name="next-steps"></a>Következő lépések

- További információ a [bérlők közötti felügyeleti élményekről](cross-tenant-management-experience.md).
- Ismerje meg, hogyan lehet [előfizetést bevezetni az Azure Lighthouse](../how-to/onboard-customer.md)szolgáltatásba.
- Tudnivalók a [Cloud Solution Provider programról](/partner-center/csp-overview).
