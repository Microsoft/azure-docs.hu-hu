---
title: Ügyfelek és delegált erőforrások megtekintése és kezelése a Azure Portal
description: Az Azure Lighthouse-t használó szolgáltatóként vagy nagyvállalatként megtekintheti az összes delegált erőforrást és előfizetést, ha a Azure Portalban lévő ügyfelekre kerül.
ms.date: 03/12/2021
ms.topic: how-to
ms.openlocfilehash: 78344015ee027b9844b6339fa7cd95d348488a54
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103419329"
---
# <a name="view-and-manage-customers-and-delegated-resources-in-the-azure-portal"></a>Ügyfelek és delegált erőforrások megtekintése és kezelése a Azure Portal

Az [Azure Lighthouse](../overview.md) szolgáltatást használó szolgáltatók használhatják a [Azure Portal](https://portal.azure.com) **saját ügyfelek** lapját a delegált ügyfelek erőforrásainak és előfizetésének megtekintéséhez.

> [!TIP]
> Noha a szolgáltatók és az ügyfelekre is hivatkozunk, a [több bérlőt kezelő vállalatok](../concepts/enterprise.md) ugyanazt a folyamatot használhatják a kezelési élményük megszilárdítására.

Ha a Azure Portal **saját ügyfelek** lapját szeretné elérni, válassza a **minden szolgáltatás** lehetőséget, majd keresse meg az **ügyfeleket** , és válassza ki azt. Azt is megteheti, hogy beírja az "ügyfelek" kifejezést a Azure Portal tetején található keresőmezőbe.

Ne feledje, hogy a **saját ügyfelek** lap legfelső szintű **ügyfelek** szakasza csak azokat az ügyfeleket mutatja be, akik a Azure Active Directory (Azure ad) bérlőhöz delegált előfizetésekkel vagy erőforrás-csoportokkal rendelkeznek az Azure Lighthouse használatával. Ha más ügyfelekkel dolgozik (például a [Cloud Solution Provider (CSP) programon](/partner-center/csp-overview)keresztül), akkor nem fogja látni az ügyfelekkel kapcsolatos információkat az **ügyfelek** szakaszban, hacsak nem készített bevezetést az [erőforrásait az Azure Lighthouse](onboard-customer.md) szolgáltatásba (bár a [felhőalapú megoldás-szolgáltató (előzetes verzió) szakaszában](#cloud-solution-provider-preview) az oldalon található egyes CSP-ügyfelek adatait láthatja.

> [!NOTE]
> Az ügyfelek megtekinthetik a szolgáltatók adatait úgy, hogy a Azure Portalban **lévő szolgáltatókra** navigálnak. További információ: szolgáltatók [megtekintése és kezelése](view-manage-service-providers.md).

## <a name="view-and-manage-customer-details"></a>Ügyfél adatainak megtekintése és kezelése

Az ügyfél részleteinek megtekintéséhez válassza a **Customers** (ügyfelek) lehetőséget a **Customers (ügyfelek)** oldal bal oldalán.

> [!IMPORTANT]
> Ezen információk megtekintéséhez a felhasználóknak meg kell adni az [olvasó](../../role-based-access-control/built-in-roles.md#reader) szerepkört (vagy egy másik beépített szerepkört, amely olvasói hozzáférést is tartalmaz) a bevezetési folyamat során.

Minden ügyfél esetében látni fogja az ügyfél nevét, az ügyfél AZONOSÍTÓját (bérlői AZONOSÍTÓját), valamint az **ajánlat azonosítóját** és az **ajánlat verzióját** , amely a részvételhez van társítva. A **delegálások** oszlopban látható a delegált előfizetések száma és/vagy a delegált erőforráscsoportok száma.

A lap tetején található beállításokkal meghatározott ügyfelek, ajánlatok és kulcsszavak alapján rendezheti, szűrheti és csoportosíthatja az ügyféladatokat.

Ezen a lapon a következő információkat tekintheti meg:

- Ha meg szeretné tekinteni az ügyfélhez társított összes előfizetést, ajánlatot és delegálást, válassza ki az ügyfél nevét.
- Ha további részleteket szeretne látni egy ajánlatról és annak delegálásáról, válassza az ajánlat nevét.
- A delegált előfizetések vagy erőforráscsoportok szerepkör-hozzárendeléseivel kapcsolatos további részletek megtekintéséhez válassza ki a bejegyzést a **delegálások** oszlopban.

## <a name="view-and-manage-delegations"></a>Delegálások megtekintése és kezelése

A delegálások a delegált előfizetést vagy erőforráscsoportot, valamint a hozzájuk tartozó felhasználókat és engedélyeket jelenítik meg. Az adatok megtekintéséhez válassza a **saját ügyfelek** lap bal oldalán található **delegálások** lehetőséget.

A lap tetején található beállításokkal adott ügyfelek, ajánlatok vagy kulcsszavak alapján rendezheti, szűrheti és csoportosíthatja ezeket az adatokat.

### <a name="view-role-assignments"></a>Szerepkör-hozzárendelések megtekintése

Az egyes delegálásokhoz társított felhasználók és engedélyek a szerepkör- **hozzárendelések** oszlopban jelennek meg. Az egyes bejegyzéseket kiválasztva megtekintheti azoknak a felhasználóknak, csoportoknak és egyszerű szolgáltatásoknak a teljes listáját, amelyek hozzáférést kaptak az előfizetéshez vagy az erőforráscsoporthoz. Az adott felhasználó, csoport vagy egyszerű szolgáltatásnév kiválasztásával további részleteket tudhat meg.

### <a name="remove-delegations"></a>Delegálások eltávolítása

Ha a [felügyelt szolgáltatások regisztrációs hozzárendelésének törlési szerepkörrel](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) rendelkező felhasználók számára bevezet egy ügyfelet az Azure Lighthouse-ba, akkor ezek a felhasználók eltávolíthatnak egy delegálást, ha kiválasztja az adott delegálás sorában megjelenő Kuka ikont. Ha igen, a szolgáltató bérlője egyik felhasználója sem férhet hozzá a korábban delegált erőforrásokhoz.

További információ: [a delegáláshoz való hozzáférés eltávolítása](remove-delegation.md).

## <a name="view-delegation-change-activity"></a>Delegálási módosítási tevékenység megtekintése

A **Customers (saját ügyfelek)** oldal **műveletnapló** szakasza minden alkalommal nyomon követi az ügyfél-előfizetések vagy erőforráscsoportok delegálását, és minden korábban delegált erőforrást eltávolít. Ezeket az információkat csak azok a felhasználók tekinthetik meg, akik [a felügyeleti olvasó szerepkört a gyökérszintű hatókörben rendelték](monitor-delegation-changes.md)el.

További információ: [delegálási változások megtekintése a Azure Portalban](monitor-delegation-changes.md#view-delegation-changes-in-the-azure-portal).

## <a name="work-in-the-context-of-a-delegated-subscription"></a>Meghatalmazott előfizetés kontextusában működik

A Azure Portalon belüli delegált előfizetések kontextusában közvetlenül is dolgozhat, anélkül, hogy átvált a címtárba, amelyre bejelentkezett. Ehhez tegye a következőket:

1. Válassza ki a **könyvtár + előfizetés** ikont a Azure Portal tetején.
2. Az **alapértelmezett előfizetés szűrőben** ügyeljen arra, hogy csak az adott delegált előfizetés mezője legyen kiválasztva. A **jelenlegi + delegált címtárak** legördülő lista használatával csak egy adott címtárban lévő előfizetéseket jelenítheti meg. (Ne használja a **switch Directory kapcsolót** , mert ez megváltoztatja azt a könyvtárat, amelybe be van jelentkezve.)

Ha ezt követően olyan szolgáltatáshoz fér hozzá, amely támogatja a [több-bérlős felügyeletet](../concepts/cross-tenant-management-experience.md), a szolgáltatás alapértelmezés szerint a kiválasztott delegált előfizetés kontextusában fog megjelenni. Ezt a fenti lépések követésével módosíthatja, és az **összes kijelölése jelölőnégyzet bejelölésével** (vagy egy vagy több előfizetést is kiválaszthat).

> [!NOTE]
> Ha egy vagy több erőforráscsoporthoz hozzáférést kapott, és nem fér hozzá egy teljes előfizetéshez, válassza ki azt az előfizetést, amelyhez az erőforráscsoport tartozik. Ezután az előfizetés kontextusában fog működni, de csak a kijelölt erőforráscsoportokat fogja tudni elérni.

A delegált előfizetésekhez vagy erőforráscsoportokhöz kapcsolódó funkciókat a több-bérlős felügyeletet támogató szolgáltatásokon belül is elérheti, ha kijelöli az előfizetést vagy az erőforráscsoportot a szolgáltatáson belül.

## <a name="cloud-solution-provider-preview"></a>Cloud Solution Provider (előzetes verzió)

A **Customers** oldal különálló **felhőalapú megoldás-szolgáltatója (előzetes verzió)** szakasza a számlázási adatokat és erőforrásokat JELENÍTi meg a CSP-ügyfeleknek, akik [aláírták a Microsoft Customer szerződést (MCA)](/partner-center/confirm-customer-agreement) , és [Az Azure-csomag alatt](/partner-center/azure-plan-get-started)találhatók. További információ: Ismerkedés a [Microsoft partneri szerződés számlázási fiókjával](../../cost-management-billing/understand/mpa-overview.md).

Ebben a szakaszban az ilyen CSP-ügyfelek is megjelennek, függetlenül attól, hogy az Azure Lighthouse-ban is szerepel-e. Hasonlóképpen, a CSP-ügyfélnek nem kell megjelennie az **ügyfelek** **Cloud Solution Provider (előzetes verzió)** szakaszában, hogy az Azure világítótoronyba bekerüljön.

## <a name="next-steps"></a>Következő lépések

- További információ a [bérlők közötti felügyeleti élményekről](../concepts/cross-tenant-management-experience.md).
- Ismerje meg, hogy az ügyfelek hogyan [tekinthetik meg és kezelhetik a szolgáltatókat](view-manage-service-providers.md) a Azure Portalban **található szolgáltatók** használatával.
