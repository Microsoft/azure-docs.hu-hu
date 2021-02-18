---
title: Az Azure-beli számlázási adatokhoz való hozzáférés kezelése
description: Megtudhatja, hogyan biztosíthat hozzáférést az Azure-beli számlázási információkhoz a csapata tagjai számára.
author: vikramdesai01
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 01/26/2021
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: 1574149e0587701323cb9ae5ae0c5cfeaccf47c7
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2021
ms.locfileid: "101096421"
---
# <a name="manage-access-to-billing-information-for-azure"></a>Az Azure-beli számlázási információkhoz való hozzáférés kezelése

A fiókjához tartozó számlázási információkhoz az Azure Portalon biztosíthat hozzáférést másoknak. A számlázási szerepkörök típusai és a számlázási információkhoz való hozzáférés biztosításának utasításai a számlázási fiók típusától függően eltérnek. A számlázási fiók típusának meghatározásával kapcsolatban lásd [a számlázási fiók típusának ellenőrzését](#check-the-type-of-your-billing-account) ismertető cikket.

Ez a cikk a Microsoft Online Services Program-fiókkal rendelkező ügyfelekre vonatkozik. Ha Ön egy Nagyvállalati Szerződéssel (EA) rendelkező ügyfél, és vállalati rendszergazdai szerepkörrel rendelkezik, a részlegszintű rendszergazdáknak és a fióktulajdonosoknak az Enterprise Portalon adhat engedélyeket. További információkért lásd [az Azure Nagyvállalati Szerződés Azure-beli felügyeleti szerepköreinek ismertetését](understand-ea-roles.md). Ha Ön Microsoft-ügyfélszerződéssel rendelkező ügyfél, tekintse meg [a Microsoft-ügyfélszerződés Azure-beli felügyeleti szerepköreinek ismertetését](understand-mca-roles.md).

## <a name="account-administrators-for-microsoft-online-service-program-accounts"></a>A Microsoft Online Services Program-fiók fiókadminisztrátora

A fiókadminisztrátor a Microsoft Online Services Programhoz tartozó számlázási fiók egyedüli tulajdonosa. A rendszer az Azure-ba regisztráló személyhez rendeli hozzá ezt a szerepkört. A fiókadminisztrátor jogosult végrehajtani bizonyos számlázási feladatokat, például az előfizetések létrehozását, a számlák megtekintését vagy az előfizetések számlázásának módosítását.

## <a name="give-others-access-to-view-billing-information"></a>Hozzáférés biztosítása másoknak a számlázási adatok megtekintéséhez

A fiókadminisztrátor úgy biztosíthat hozzáférést az Azure számlázási adataihoz, hogy a következő szerepkörök egyikét hozzárendeli a fiókhoz tartozó egyik előfizetéshez.

- Szolgáltatás-rendszergazda
- Társadminisztrátor
- Tulajdonos
- Közreműködő
- Olvasó
- Számlázási olvasó

Ezek a szerepkörök hozzáféréssel rendelkeznek a számlázási adatokhoz az [Azure Portalon](https://portal.azure.com/). Azok a személyek, akikhez hozzá lett rendelve ez a szerepkör, a [Billing API-k](consumption-api-overview.md#usage-details-api) használatával a számlákat és a használati részleteket is lekérhetik programozott módon.

A szerepkörök hozzárendeléséről [Az Azure-szerepkörök a Azure Portal használatával történő hozzárendelését](../../role-based-access-control/role-assignments-portal.md)ismertető témakörben talál további információt.

**Ha Ön EA-ügyfél, a fióktulajdonos hozzárendelheti a fenti szerepkört a csapata többi felhasználójához. Azonban ahhoz, hogy ezek a felhasználók megtekinthessék a számlázási adatokat, a vállalati rendszergazdának engedélyeznie kell a díjtételek megtekintését a fióktulajdonos számára az Enterprise Portalon.


### <a name="allow-users-to-download-invoices"></a><a name="opt-in"></a> A számlák letöltésének engedélyezése a felhasználóknak

Ha a fiókadminisztrátor hozzárendelte a megfelelő szerepköröket a felhasználókhoz, utóbbiaknak be kell kapcsolniuk a számlák letöltéséhez való hozzáférést az Azure Portalon. A 2016 decemberénél régebbi számlák csak a fiókadminisztrátor számára érhetők el.

1. Jelentkezzen be fiókadminisztrátorként az [Azure Portalra](https://portal.azure.com/).

1. Végezzen keresést a **Költségkezelés + Számlázás** oldalon.

    ![Képernyőkép a Szolgáltatások szakaszban található Cost Management + Billing lehetőség kiemelésével.](./media/manage-billing-access/billing-search-cost-management-billing.png)

1. A bal oldali panelen válassza az **Előfizetések** elemet. A hozzáférésétől függően előfordulhat, hogy csak egy számlázási hatókör kiválasztása után tudja kiválasztani az **Előfizetések** elemet.

    ![Képernyőkép az előfizetések kiválasztásáról](./media/manage-billing-access/billing-select-subscriptions.png)

1. Válassza a **Számlák**, majd a **Számlához való hozzáférés** lehetőséget.

    ![Képernyőkép a számlákhoz való hozzáférés delegálásáról](./media/manage-billing-access/aa-optin01.png)

1. Válassza a **Be** lehetőséget, majd kattintson a Mentés parancsra.

    ![Képernyőkép a számlákhoz való hozzáférés delegálásának be- és kikapcsolásáról](./media/manage-billing-access/aa-optinallow01.png)

A fiókadminisztrátor azt is beállíthatja, hogy a számlákat a rendszer elküldje e-mailben. További tudnivalók: [Számla kérése e-mailben](download-azure-invoice-daily-usage-date.md).

## <a name="give-read-only-access-to-billing"></a>Csak olvasási hozzáférés biztosítása a számlázáshoz

Hozzárendelheti a Számlázási olvasó szerepkört egy olyan felhasználóhoz, akinek olvasási hozzáférésre van szüksége az előfizetés számlázási adataihoz, ugyanakkor egyidejűleg nem biztosítja a számára az Azure-szolgáltatások kezelésének vagy létrehozásának képességét. Ez a szerepkör megfelelő a szervezetek azon felhasználói számára, akik az Azure-előfizetések pénzügyi és költségkezeléséért felelősek.

A Számlázási olvasó funkció előzetes verzióként érhető el, és még nem támogatja a nem globális felhőket.

1. Jelentkezzen be fiókadminisztrátorként az [Azure Portalra](https://portal.azure.com/).

1. Végezzen keresést a **Költségkezelés + Számlázás** oldalon.

    ![Képernyőkép az Azure Portal keresőmezőjéről](./media/manage-billing-access/billing-search-cost-management-billing.png)

1. A bal oldali panelen válassza az **Előfizetések** elemet. A hozzáférésétől függően előfordulhat, hogy csak egy számlázási hatókör kiválasztása után tudja kiválasztani az **Előfizetések** elemet.

    ![Képernyőkép az előfizetések kiválasztásáról](./media/manage-billing-access/billing-select-subscriptions.png)

1. Válassza a **Hozzáférés-vezérlés (IAM)** lehetőséget.
1. A lap tetején válassza a **Hozzáadás** elemet.

    ![Képernyőkép a Szerepkör-hozzárendelés hozzáadása lehetőségre történő kattintásról](./media/manage-billing-access/billing-click-add-role-assignment.png)

1. A **Szerepkör** legördülő listában válassza a **Számlázási olvasó** szerepkört.
1. A **Kiválasztás** szövegmezőbe írja be a hozzáadni kívánt felhasználó nevét vagy e-mail-címét.
1. Válassza ki a felhasználót.
1. Kattintson a **Mentés** gombra.
    ![Képernyőkép a Mentés gomb kiemelésével.](./media/manage-billing-access/billing-save-role-assignment.png)

1. Néhány pillanat múlva a rendszer hozzárendeli a felhasználóhoz az előfizetés Számlázási olvasó szerepkörét.

**Ha Ön EA-ügyfél, akkor a fióktulajdonos vagy a részlegszintű rendszergazda rendelheti hozzá a Számlázási olvasó szerepkört a csapat tagjaihoz. Ahhoz azonban, hogy a Számlázási olvasó megtekinthesse a részleg vagy a fiók számlázási adatait, a vállalati rendszergazdának engedélyeznie kell a díjtételek megtekintésének szabályzatát a **fióktulajdonos** vagy a **részlegszintű rendszergazda** számára az Enterprise Portalon.

## <a name="check-the-type-of-your-billing-account"></a>A számlázási fiók típusának megtekintése
[!INCLUDE [billing-check-account-type](../../../includes/billing-check-account-type.md)]

## <a name="next-steps"></a>További lépések

- A más (például tulajdonosi vagy közreműködői) szerepkörrel rendelkező felhasználók nemcsak a számlázási adatokhoz, hanem az Azure-szolgáltatásokhoz is hozzáférhetnek. A szerepkörök kezeléséhez tekintse meg [Az Azure-szerepkörök a Azure Portal használatával történő hozzárendelését](../../role-based-access-control/role-assignments-portal.md)ismertető témakört.
- További információ a szerepkörökről: [Az Azure beépített szerepkörei](../../role-based-access-control/built-in-roles.md).

## <a name="need-help-contact-us"></a>Segítségre van szüksége? Vegye fel velünk a kapcsolatot.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).
