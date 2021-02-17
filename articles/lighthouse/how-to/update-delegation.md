---
title: Delegálás frissítése
description: Megtudhatja, hogyan frissíthet egy korábban az Azure Lighthouse-be bekészített ügyfél delegálását.
ms.date: 02/16/2021
ms.topic: how-to
ms.openlocfilehash: f0ed5222cdbac3d0e4d193941c2a6f233d15938c
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100555773"
---
# <a name="update-a-delegation"></a>Delegálás frissítése

Miután előkészített egy előfizetést (vagy erőforráscsoportot) az Azure világítótoronyhoz, előfordulhat, hogy módosítania kell a módosításokat. Előfordulhat például, hogy az ügyfél további felügyeleti feladatokat kíván végrehajtani, amelyek eltérő Azure beépített szerepkört igényelnek, vagy módosítania kell azt a bérlőt, amelyhez az ügyfél-előfizetés delegálva van.

> [!TIP]
> Bár a jelen témakörben a szolgáltatók és az ügyfelek számára is hivatkozunk, a [több bérlőt kezelő vállalatok](../concepts/enterprise.md) ugyanazt a folyamatot használhatják az Azure Lighthouse beállításához és a kezelési élmény megszilárdításához.

Ha [Azure Resource Manager-sablonokkal (ARM-sablonokkal) végezte el az ügyfelet](onboard-customer.md), az adott ügyfél számára új központi telepítést kell végrehajtania. Attól függően, hogy mit módosít, érdemes lehet frissíteni az eredeti ajánlatot, vagy eltávolítani az eredeti ajánlatot, és újat létrehozni.

- **Ha csak az engedélyeket módosítja**: a delegálást úgy frissítheti, hogy csak az ARM-sablon **engedélyezési** szakaszát módosítja.
- **Ha megváltoztatja a kezelő bérlőt**: létre kell hoznia egy új ARM-sablont az előző ajánlattól eltérő **mspOfferName** használatával.

## <a name="update-your-arm-template"></a>Az ARM-sablon frissítése

A delegálás frissítéséhez telepítenie kell egy ARM-sablont, amely tartalmazza a végrehajtandó módosításokat.

Ha csak az engedélyek frissítését végzi (például új felhasználói csoport hozzáadása egy korábban még nem tartalmazott szerepkörhöz, vagy egy meglévő felhasználó szerepkörének módosítása), ugyanazt a **mspOfferName** használhatja, mint az előző delegáláshoz használt [ARM-sablonban](onboard-customer.md#create-an-azure-resource-manager-template) . Az előző sablon kiindulási pontként is használható. Ezután végezze el a szükséges módosításokat, például egy Azure beépített szerepkör cseréjét egy másikkal, vagy adjon hozzá egy teljesen új engedélyt a sablonhoz.

Ha módosítja a **mspOfferName**, ez egy új, külön ajánlatnak minősül. Erre akkor van szükség, ha megváltoztatja az ügyvezető bérlőt.

Nem szükséges módosítani a **mspOfferName** , ha a bérlő kezelése változatlan marad. A legtöbb esetben azt javasoljuk, hogy csak egy **mspOfferName** használjon ugyanazon ügyfél és a bérlő kezelése során. Ha úgy dönt, hogy mindenképpen módosítja, ügyeljen arra, hogy az ügyfél előző delegálása el legyen távolítva az új telepítése előtt.

## <a name="remove-the-previous-delegation"></a>Az előző delegálás eltávolítása

Új központi telepítés végrehajtása előtt érdemes lehet [eltávolítani az előző delegáláshoz való hozzáférést](remove-delegation.md). Ez biztosítja, hogy minden korábbi engedély el legyen távolítva, ami lehetővé teszi, hogy megtisztítsa azokat a pontos felhasználókat és csoportokat és szerepköröket, amelyekre alkalmazni kell.

> [!IMPORTANT]
> Ha új **mspOfferName** használ, és megtartja ugyanazt a **principalId** -értéket, el kell távolítania az előző delegáláshoz való hozzáférést az új ajánlat telepítése előtt. Ha nem távolítja el az ajánlatot, a korábban megadott engedélyekkel rendelkező felhasználók az ütköző hozzárendelések miatt teljes mértékben elveszíthetik a hozzáférést.

Ha megváltoztatja a bérlői felügyeletet, akkor az előző ajánlatot is meghagyhatja, ha azt szeretné, hogy a bérlők továbbra is hozzáférjenek. Ha csak azt szeretné, hogy az új bérlő jogosult legyen a hozzáférésre, el kell távolítani a korábbi ajánlatot. Ezt az új ajánlat előkészítése előtt vagy után is elvégezheti.

Ha úgy frissíti az ajánlatot, hogy csak az engedélyeket módosítsa, és megtartja ugyanazt a **mspOfferName**, nem kell eltávolítania az előző delegálást. Az új üzemelő példány felülírja az előző delegálást, és csak a legújabb sablonban lévő engedélyek lesznek érvényesek.

:::image type="content" source="../media/update-delegation.jpg" alt-text="Diagram, amely azt mutatja, hogy mikor kell módosítani a mspOfferName, és el kell távolítani egy korábbi delegálást.":::

A delegáláshoz való hozzáférés eltávolítását bármely olyan felhasználó megteheti, aki a [felügyelt szolgáltatások regisztrációs hozzárendelésének törlési szerepkörét](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) adta meg az eredeti delegálásban. Ha a kezelő bérlő egyik felhasználója sem rendelkezik ezzel a szerepkörrel, megkérheti az ügyfelet, hogy [távolítsa el az ajánlathoz való hozzáférést a Azure Portal](view-manage-service-providers.md#add-or-remove-service-provider-offers).

> [!TIP]
> Ha eltávolította az előző delegálást a fenti lépéseket követve, és továbbra sem tudja telepíteni az új ARM-sablont, előfordulhat, hogy [teljesen el kell távolítania a regisztráció definícióját](/powershell/module/az.managedservices/remove-azmanagedservicesdefinition). Ezt bármely olyan felhasználó megteheti, aki rendelkezik `Microsoft.Authorization/roleAssignments/write` engedéllyel (például [tulajdonossal](../../role-based-access-control/built-in-roles.md#owner)) rendelkező szerepkörrel az ügyfél bérlője számára.  

## <a name="deploy-the-arm-template"></a>Az ARM-sablon üzembe helyezése

Az ügyfél ugyanúgy [telepítheti a frissített sablont](onboard-customer.md#deploy-the-azure-resource-manager-templates) , mint korábban: a Azure Portal a PowerShell használatával vagy az Azure CLI használatával.

A telepítés befejezése után ellenőrizze, hogy a művelet [sikeres volt-e](onboard-customer.md#confirm-successful-onboarding). A frissített engedélyek ezután érvénybe lépnek az ügyfél által delegált előfizetés vagy erőforráscsoport (ok) esetében.

## <a name="updating-managed-service-offers"></a>Felügyelt szolgáltatás ajánlatok frissítése

Ha az ügyfelet az Azure Marketplace-en közzétett felügyelt szolgáltatási ajánlaton keresztül vette fel, és frissíteni kívánja az engedélyeket, akkor a delegálást úgy frissítheti, hogy az [ajánlat új verzióját](../../marketplace/partner-center-portal/update-existing-offer.md) közzéteszi az adott ügyfélre vonatkozó tervben használt [engedélyekkel](../../marketplace/plan-managed-service-offer.md) . Az ügyfél ezután frissíthet a Azure Portal legújabb verziójára.

Ha módosítani szeretné az ügyvezető bérlőt, [létre kell hoznia és közzé kell tennie egy új felügyelt szolgáltatási ajánlatot](../../marketplace/plan-managed-service-offer.md) , amelyet az ügyfélnek el kell fogadnia.

> [!TIP]
> Ahogy korábban említettük, javasoljuk, hogy ne használjon több különböző ajánlatot ugyanahhoz az ügyfélhez és a bérlő kezeléséhez. Ha olyan új ajánlatot tesz közzé ugyanahhoz az ügyfélhez, amely ugyanazt a kezelő bérlőt használja, akkor a korábbi ajánlatot el kell távolítani, mielőtt az ügyfél elfogadja az újabb ajánlatot.

## <a name="next-steps"></a>Következő lépések

- [Megtekintheti és kezelheti az ügyfeleket](view-manage-customers.md) a Azure Portalban lévő **ügyfelekkel** .
- Ismerje meg, hogyan [távolíthatja el a korábban előkészített delegáláshoz való hozzáférést](remove-delegation.md) .
