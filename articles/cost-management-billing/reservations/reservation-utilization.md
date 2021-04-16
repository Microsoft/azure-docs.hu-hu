---
title: Azure-beli foglalás kihasználtságának megtekintése
description: Megtudhatja, hogyan olvashatja be a foglalás kihasználtságát és részleteit.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 04/15/2021
ms.author: banders
ms.openlocfilehash: 28b61781f0b22e79d10d79c1a46e757737a401cf
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568946"
---
# <a name="view-reservation-utilization-after-purchase"></a>Foglalás kihasználtságának megtekintése vásárlás után

Megtekintheti a foglalás kihasználtságának százalékos arányát, valamint a foglalást a Azure Portal az alkalmazásban Cost Management Power BI erőforrásokat.

## <a name="view-utilization-in-the-azure-portal-with-azure-rbac-access"></a>A kihasználtság megtekintése a Azure Portal Azure RBAC-hozzáféréssel

A foglalás kihasználtságának megtekintéséhez Azure RBAC-hozzáféréssel kell lennie a foglaláshoz, vagy emelt szintű hozzáféréssel kell lennie az összes Azure-előfizetés és felügyeleti csoport kezeléséhez.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Lépjen a [Foglalások](https://portal.azure.com/#blade/Microsoft_Azure_Reservations/ReservationsBrowseBlade) területre.
1. A listán szerepel az összes olyan foglalás, amelyhez Tulajdonos vagy Olvasó szerepkörben hozzáfér. Mindegyik foglalás mellett a kihasználtság legutóbb ismert százalékos értéke látható.
1. Kattintson a kihasználtság százalékára a kihasználtság előzményeinek és részleteinek megtekintéséhez. Az alábbi videó egy példát mutat be.
   > [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4sYwk] 

## <a name="view-utilization-as-billing-administrator"></a>Kihasználtság megtekintése számlázási rendszergazdaként

Egy Nagyvállalati Szerződés (EA) rendszergazda vagy egy Microsoft Ügyfélszerződés (MCA) számlázási rendszergazda megtekintheti a kihasználtságot a **Cost Management + Billing.**

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A **Foglalások Cost Management + Billing**  >  **meg.**
1. Kattintson a kihasználtság százalékára a kihasználtság előzményeinek és részleteinek megtekintéséhez.

## <a name="get-reservations-and-utilization-using-apis-powershell-and-cli"></a>Foglalások és kihasználtság lekérése API-k, a PowerShell és a parancssori felület használatával

A foglalás [kihasználtságát a](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) Reserved Instance usage API-val kaphatja meg.

## <a name="see-reservations-and-utilization-in-power-bi"></a>Foglalások és kihasználtság megtekintése a Power BI-ban

A felhasználók számára két Power BI van:

- Azure Cost Management összekötő Power BI Desktop – A foglalás vásárlási dátuma és kihasználtsági adatai a Azure Cost Management összekötőben érhetők el [a Power BI Desktop.](/power-bi/desktop-connect-azure-cost-management) Hozza létre a kívánt jelentéseket az összekötő használatával.
- Azure Cost Management Power BI alkalmazás – A [Azure Cost Management Power BI Alkalmazás](https://appsource.microsoft.com/product/power-bi/costmanagement.azurecostmanagementapp) használata előre létrehozott jelentésekhez, amelyek további testreszabásra is használhatók.

## <a name="next-steps"></a>További lépések

- [Az Azure Reservations kezelése](manage-reserved-vm-instance.md).
- [A foglalási kihasználtság ismertetése használatalapú fizetéses előfizetésnél](understand-reserved-instance-usage.md).
- [A foglalási kihasználtság ismertetése vállalati regisztrációnál](understand-reserved-instance-usage-ea.md).
- [A foglalási kihasználtság ismertetése CSP-előfizetésnél](/partner-center/azure-reservations).
