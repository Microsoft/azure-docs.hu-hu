---
title: Foglalások kezelése az Azure-erőforrásokhoz
description: Ismerje meg, hogyan tekintheti meg az Azure Reservationst az Azure Portalon. Foglalások és kihasználtság megtekintése az API-k, a PowerShell, a parancssori felület és a Power BI használatával.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 03/17/2021
ms.author: banders
ms.openlocfilehash: 1c666602f764e8274cb2a30df204e97479c85ba3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104583054"
---
# <a name="view-azure-reservations"></a>Azure-foglalások megtekintése

Ismerje meg, hogyan tekintheti meg az Azure Reservationst az Azure Portalon. A megvásárolt foglalásokat az Azure Portalon tekintheti meg és kezelheti.

## <a name="who-can-manage-a-reservation-by-default"></a>Alapértelmezés szerint kik kezelhetik a foglalásokat?

Alapértelmezés szerint a következő felhasználók tekinthetik meg és kezelhetik a foglalásokat:

- A foglalást vásárló személyt és a foglalás vásárlásához használt számlázási előfizetés fiókadminisztrátorát a rendszer hozzáadja a foglalási rendeléshez.
- Nagyvállalati Szerződéssel és Microsoft Ügyfélszerződéssel rendelkező számlázási rendszergazdák.

Két lehetősége van, ha más személyeknek is engedélyezni szeretné a foglalások kezelését:

- Hozzáférés-kezelés delegálása egy egyedi foglalási rendeléshez:
    1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
    1. Válassza a **Minden szolgáltatás** > **Foglalások** lehetőséget azoknak a foglalásoknak a megtekintéséhez, amelyekhez hozzáféréssel rendelkezik.
    1. Válassza ki azt a foglalást, amelyhez való hozzáférést delegálni szeretné más felhasználóknak.
    1. A foglalási adatoknál válassza ki a foglalási rendelést.
    1. Válassza a **Hozzáférés-vezérlés (IAM)** lehetőséget.
    1. Válassza a **Szerepkör-hozzárendelés hozzáadása** > **Szerepkör** > **Tulajdonos** lehetőséget. Ha korlátozott hozzáférést szeretne biztosítani, válasszon egy másik szerepkört.
    1. Írja be annak a felhasználónak az e-mail-címét, akit tulajdonosként kíván hozzáadni.
    1. Válassza ki a felhasználót, majd válassza a **Mentés** lehetőséget.

- Adjon hozzá egy felhasználót számlázási rendszergazdaként egy Nagyvállalati Szerződéshez vagy egy Microsoft Ügyfélszerződéshez:
    - Nagyvállalati Szerződés esetén vegye fel a felhasználókat _vállalati rendszergazda_ szerepkörbe az összes foglalási rendelés megtekintéséhez és kezeléséhez, amelyekre a Nagyvállalati Szerződés vonatkozik. A vállalati rendszergazdák megtekinthetik és kezelhetik **Cost Management + számlázással** kapcsolatos foglalásokat, és nem **foglalásokat**. A _Vállalati rendszergazda (csak olvasási)_ szerepkörrel rendelkező felhasználók csak megtekinthetik a foglalásokat. A részleg rendszergazdái és a fióktulajdonosai nem tekinthetik meg a foglalásokat _kivéve_, ha kifejezetten a hozzáférés-vezérlés (IAM) használatával lettek hozzáadva. További információért lásd: [Az Azure Enterprise szerepköreinek kezelése](../manage/understand-ea-roles.md).

        _A vállalati rendszergazdák tulajdonukba vehetik a foglalási rendeléseket, és a hozzáférés-vezérlés (IAM) használatával más felhasználókat is hozzáadhatnak a foglalásokhoz._
    - Microsoft Ügyfélszerződés esetén a számlázási profil tulajdonosi szerepkörével vagy a számlázási profil közreműködői szerepkörrel rendelkező felhasználók a számlázási profillal végzett összes foglalásvásárlást kezelhetik. A számlázási profil olvasói és a számlakezelők a számlázási profillal kifizetett összes foglalást megtekinthetik. A foglalásokat azonban nem módosíthatják.
    További információkért lásd [a számlázási profil szerepköreit és azok feladatait](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks) ismertető cikket.

### <a name="how-billing-administrators-view-or-manage-reservations"></a>A foglalások megtekintése és kezelése a számlázási rendszergazdák számára

Ha az Azure RBAC-hozzáféréssel rendelkezik a foglalásokhoz vagy a foglalási rendelésekhez, akkor a foglalások megnyitásakor csak a foglalási tranzakciók egy részhalmazát láthatja. Az alábbi lépések segítségével megtekintheti és kezelheti az összes foglalást és foglalási tranzakciót.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és navigáljon a **Cost Management + számlázás** elemre.
    - Ha Ön egy nagyvállalati rendszergazda, a bal oldali menüben válassza ki a **Számlázási hatókörök** elemet, majd a számlázási hatókörök listájában válasszon egyet.
    - Ha Ön a Microsoft ügyfél-szerződés számlázási profiljának tulajdonosa, a bal oldali menüben válassza a **Számlázási profilok** lehetőséget. A számlázási profilok listájában válasszon egyet.
1. A bal oldali menüben válassza a **foglalási tranzakciók** elemet. Megjelenik a foglalási tranzakciók listája.
1. A lap tetején található szalagcím beolvassa a *Számlázási rendszergazdák a foglalásokat kezelhetik. Kattintson ide a foglalások kezeléséhez.* Válassza ki a szalagcímet.
1. Megjelenik az EA-regisztrációhoz vagy a számlázási profilhoz tartozó foglalások teljes listája.
1. Ha szeretné, hogy a foglalás tulajdonosa legyen, válassza ki. Ezután az engedélyek beállítása lapon válassza a **hozzáférés engedélyezése** lehetőséget. Tulajdonosi hozzáférést kap a foglaláshoz és a foglalási rendeléshez.

## <a name="view-reservation-and-utilization-in-the-azure-portal"></a>Foglalás és kihasználtság megtekintése az Azure Portalon

Foglalás megtekintése Tulajdonosként vagy Olvasóként

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Lépjen a [Foglalások](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) területre.
3. A listán szerepel az összes olyan foglalás, amelyhez Tulajdonos vagy Olvasó szerepkörben hozzáfér. Mindegyik foglalás mellett a kihasználtság legutóbb ismert százalékos értéke látható.
4. Kattintson a kihasználtság százalékára a kihasználtság előzményeinek és részleteinek megtekintéséhez. Az alábbi videóban talál részleteket.
   > [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4sYwk] 

## <a name="get-reservations-and-utilization-using-apis-powershell-and-cli"></a>Foglalások és kihasználtság lekérése API-k, a PowerShell és a parancssori felület használatával

Az összes foglalás listájának lekérése a következő erőforrásokkal:

- [API: Foglalásrendelések listája](/rest/api/reserved-vm-instances/reservationorder/list)
- [PowerShell: Foglalásrendelések listája](/powershell/module/azurerm.reservations/get-azurermreservationorder)
- [Parancssori felület: Foglalásrendelések listája](/cli/azure/reservations/reservation-order#az-reservations-reservation-order-list)

A [foglalás kihasználtsága](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) ezenkívül a Reserved Instance használati API használatával is megállapítható. 

## <a name="see-reservations-and-utilization-in-power-bi"></a>Foglalások és kihasználtság megtekintése a Power BI-ban

Két lehetőség érhető el a Power BI-felhasználók számára
- Tartalomcsomag: A foglalás vásárlási ideje és a kihasználtsági adatok a [Consumption Insights Power BI-tartalomcsomagban](/power-bi/desktop-connect-azure-cost-management) érhetők el. A tartalomcsomaggal hozhatja létre a kívánt jelentéseket. 
- Cost Management alkalmazás: A [Cost Management alkalmazást](https://appsource.microsoft.com/product/power-bi/costmanagement.azurecostmanagementapp) további testreszabásra kész, előre létrehozott jelentésekhez használhatja.

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Kapcsolat

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések

- [Az Azure Reservations kezelése](manage-reserved-vm-instance.md).
- [A foglalási kihasználtság ismertetése használatalapú fizetéses előfizetésnél](understand-reserved-instance-usage.md).
- [A foglalási kihasználtság ismertetése vállalati regisztrációnál](understand-reserved-instance-usage-ea.md).
- [A foglalási kihasználtság ismertetése CSP-előfizetésnél](/partner-center/azure-reservations).

