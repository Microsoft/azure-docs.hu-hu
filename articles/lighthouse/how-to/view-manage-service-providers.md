---
title: Szolgáltatók megtekintése és kezelése
description: Az ügyfelek a Azure Portal szolgáltatók lapján tekinthetik meg a szolgáltatók, a szolgáltatói ajánlatok és a delegált erőforrások adatait.
ms.date: 02/16/2021
ms.topic: how-to
ms.openlocfilehash: f6ee5fb154d75ff715acf99c5184cd1652ccdb80
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100555578"
---
# <a name="view-and-manage-service-providers"></a>Szolgáltatók megtekintése és kezelése

A [Azure Portal](https://portal.azure.com) **szolgáltatók** lapja biztosítja az ügyfelek számára az [Azure Lighthouse](../overview.md)szolgáltatást használó szolgáltatók felügyeletét és láthatóságát. Az ügyfelek megtekinthetik a szolgáltatók részleteit, az egyes erőforrások delegálását, az új ajánlatokat, a szolgáltatói hozzáférés eltávolítását stb.

Ha meg szeretné tekinteni a **szolgáltatók lapot a** Azure Portalban, válassza a **minden szolgáltatás** lehetőséget, majd keressen **a szolgáltatók között, és jelölje** ki. Ezt a lapot a Azure Portal tetején található keresőmezőbe írja be a "szolgáltatók" vagy az "Azure Lighthouse" kifejezést.

> [!NOTE]
> **A szolgáltatók lap megtekintéséhez** az ügyfél bérlője számára az [olvasó beépített szerepkörrel](../../role-based-access-control/built-in-roles.md#reader) kell rendelkeznie (vagy egy másik beépített szerepkör, amely olvasói hozzáférést is tartalmaz).
>
> Ajánlatok hozzáadásához vagy frissítéséhez, erőforrások delegálásához és az ajánlatok eltávolításához a felhasználónak rendelkeznie kell egy engedéllyel rendelkező szerepkörrel, `Microsoft.Authorization/roleAssignments/write` például a [tulajdonossal](../../role-based-access-control/built-in-roles.md#owner).

Ne feledje, hogy a **szolgáltatók lap csak** azokat a szolgáltatói információkat jeleníti meg, amelyek az ügyfél előfizetéseit vagy erőforráscsoportait az Azure Lighthouse használatával érik el. Ha az ügyfél olyan további szolgáltatókkal működik együtt, akik nem használják az Azure Lighthouse-t, itt nem jelenik meg információ ezekről a szolgáltatókról.

## <a name="view-service-provider-details"></a>Szolgáltató adatainak megtekintése

Ha az Azure Lighthouse használatával szeretné megtekinteni az ügyfél bérlője számára az aktuális szolgáltatók adatait, válassza a szolgáltatói **ajánlatok** lehetőséget a **szolgáltatók oldal bal** oldalán.

Minden ajánlat esetében megjelenik a szolgáltató neve és a hozzá társított ajánlat. Kiválaszthat egy ajánlatot a leírás és egyéb részletek megtekintéséhez, beleértve a szolgáltató által megadott szerepkör-hozzárendeléseket is.

A **delegálások** oszlopban láthatja, hogy hány előfizetés és/vagy erőforráscsoport van delegálva az adott ajánlat szolgáltatójának. A szolgáltató elérheti és kezelheti ezeket az előfizetéseket és/vagy erőforráscsoportokat az ajánlatban megadott hozzáférési szintnek megfelelően.

## <a name="add-or-remove-service-provider-offers"></a>Szolgáltatói ajánlatok hozzáadása vagy eltávolítása

Ha új szolgáltatói ajánlatot szeretne hozzáadni a szolgáltatói **ajánlatok** lapról, válassza az **ajánlat hozzáadása** lehetőséget. Válassza ki a **privát ajánlatokat** azon ajánlatok megtekintéséhez, amelyeket a szolgáltató közzétett az ügyfél számára. Ezután kiválaszthatja az ajánlatot a **privát ajánlatok** képernyőn, majd a **beállítás + előfizetés** lehetőséget.

A szolgáltatói ajánlatot bármikor eltávolíthatja, ha kiválasztja az ajánlat sorában látható Kuka ikont. A Törlés megerősítése után a szolgáltató már nem fog tudni hozzáférni az adott ajánlathoz korábban delegált erőforrásokhoz.

## <a name="delegate-resources"></a>Erőforrások delegálása

Ahhoz, hogy a szolgáltató hozzáférhessen egy ügyfél erőforrásaihoz, és felügyelni tudja az ügyfelek erőforrásait, egy vagy több konkrét előfizetést és/vagy erőforráscsoportot delegálni kell. Ha egy ügyfél elfogadta az ajánlatot, de még nem delegált erőforrást, akkor a **szolgáltató ajánlatok** szakaszának tetején egy megjegyzés jelenik meg. Ez lehetővé teszi, hogy az ügyfél tudja, hogy el kell végeznie a beavatkozást, mielőtt a szolgáltató hozzáférhet az ügyfél erőforrásaihoz.

Előfizetések vagy erőforráscsoportok delegálása:

1. Jelölje be a szolgáltatót, az ajánlatot és a nevet tartalmazó sor jelölőnégyzetét. Ezután válassza az **erőforrások delegálása** lehetőséget a képernyő tetején.
1. Tekintse át a szolgáltató és az ajánlat részleteit az **erőforrások delegálása** lap **ajánlat részletei** szakaszában. Az ajánlathoz tartozó szerepkör-hozzárendelések áttekintéséhez válassza a **kattintson ide a kiválasztott ajánlat részleteinek megtekintéséhez**.
1. A **delegált** szakaszban válassza az **előfizetések delegálása** vagy az **erőforráscsoportok delegálása** lehetőséget.
1. Válassza ki az ajánlathoz delegálni kívánt előfizetéseket és/vagy erőforráscsoportokat, majd válassza a **Hozzáadás** lehetőséget.
1. Jelölje be a lap alján található jelölőnégyzetet annak megerősítéséhez, hogy a szolgáltató hozzáférést kíván adni a kiválasztott erőforrásokhoz, majd válassza a **delegálás** lehetőséget.

## <a name="update-service-provider-offers"></a>A szolgáltatói ajánlatok frissítése

Miután egy ügyfél hozzáadott egy ajánlatot, a szolgáltató közzéteheti az Azure Marketplace-re vonatkozó ajánlat frissített verzióját. Előfordulhat például, hogy új szerepkör-definíciót szeretne hozzáadni. Ha közzétette az ajánlat új verzióját, a szolgáltató által biztosított **ajánlatok** lapon megjelenik az ajánlat sorában látható "Update" ikon. Válassza ezt az ikont az ajánlat aktuális verziója és az új verzió közötti különbségek megtekintéséhez.

 ![Ajánlat frissítése ikon](../media/update-offer.jpg)

A módosítások áttekintése után az ügyfél dönthet úgy, hogy frissíti az új verzióra. Az új verzióban megadott engedélyek és egyéb beállítások ezután az adott ajánlathoz delegált összes előfizetésre és/vagy erőforrás-csoportra érvényesek lesznek.

## <a name="view-delegations"></a>Delegálások megtekintése

A delegálások azokat a szerepkör-hozzárendeléseket jelölik, amelyek engedélyeket biztosítanak a szolgáltatónak az ügyfelek által delegált erőforrások számára. Az információ megtekintéséhez válassza a **szolgáltatók** lap bal oldalán található **delegálások** lehetőséget.

A lap tetején lévő szűrők lehetővé teszik a delegálási adatok rendezését és csoportosítását. A szűrést meghatározott ügyfelek, ajánlatok vagy kulcsszavak alapján is elvégezheti.

> [!NOTE]
> Az ügyfelek nem látják ezeket a szerepkör-hozzárendeléseket, illetve azon szolgáltatói bérlőtől származó felhasználókat, akik megkapták ezeket a szerepköröket, amikor a Azure Portal vagy API-kon keresztül [megtekintik a delegált hatókörhöz tartozó szerepkör-hozzárendeléseket](../../role-based-access-control/role-assignments-list-portal.md#list-role-assignments-at-a-scope) .

## <a name="audit-delegations-in-your-environment"></a>Delegálások naplózása a környezetben

Előfordulhat, hogy az ügyfelek megtekinthetik az Azure Lighthouse-ra delegált előfizetéseket és/vagy erőforráscsoportokat. Ez különösen hasznos azoknak az ügyfeleknek, akik nagy számú előfizetéssel rendelkeznek, vagy akiknél sok felhasználó végzi a felügyeleti feladatokat.

Egy [Azure Policy beépített szabályzat-definíciót](../../governance/policy/samples/built-in-policies.md#lighthouse) biztosítunk a [hatókörök delegálásának naplózásához egy felügyeleti bérlőhöz](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Lighthouse/Lighthouse_Delegations_Audit.json). Ezt a házirendet hozzárendelheti egy felügyeleti csoporthoz, amely tartalmazza az összes naplózni kívánt előfizetést. Ha bejelöli a szabályzatnak való megfelelést, minden olyan delegált előfizetés és/vagy erőforráscsoport (a felügyeleti csoporton belül, amelyhez a házirend hozzá van rendelve) nem megfelelő állapotban jelenik meg. Ezután ellenőrizheti az eredményeket, és ellenőrizheti, hogy nincsenek-e váratlan delegálások.

Egy másik [beépített szabályzat-definíció](../../governance/policy/samples/built-in-policies.md#lighthouse) lehetővé teszi [a delegálások meghatározott felügyeleti bérlők számára való korlátozását](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Lighthouse/AllowCertainManagingTenantIds_Deny.json). Ez a szabályzat hasonlóképpen alkalmazható egy felügyeleti csoportra is, amely tartalmazza azokat az előfizetéseket, amelyeken korlátozni kívánja a delegálást. A házirend telepítése után a rendszer megtagadja az előfizetés delegálását a megadott bérlőn kívül.

A szabályzatok hozzárendeléséről és a megfelelőségi állapot eredményeinek megjelenítéséről a rövid útmutató [: szabályzat-hozzárendelés létrehozása](../../governance/policy/assign-policy-portal.md)című témakörben olvashat bővebben.

## <a name="next-steps"></a>Következő lépések

- További információ az [Azure Lighthouse](../overview.md)-ról.
- További információ a szolgáltatói [tevékenységek naplózásáról](view-service-provider-activity.md).
- Ismerje meg, hogy a szolgáltatók hogyan [tekinthetik meg és kezelhetik az ügyfeleket](view-manage-customers.md) a Azure Portal **saját ügyfelek** lapján.
- Ismerje meg, hogy a [több bérlőt kezelő vállalatok](../concepts/enterprise.md) hogyan használhatják az Azure Lighthouse-t a kezelési élményük megszilárdítására.

