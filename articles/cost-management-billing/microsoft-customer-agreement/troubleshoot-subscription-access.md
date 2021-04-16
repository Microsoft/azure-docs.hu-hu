---
title: Előfizetéshez való hozzáférés hibaelhárítása a Microsoft Ügyfélszerződés után – Azure
description: Ez a cikk segítséget nyújt az előfizetéshez való hozzáférés hibaelhárításához, miután új előfizetést Microsoft Ügyfélszerződés.
author: bandersmsft
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: troubleshooting
ms.date: 04/07/2021
ms.author: banders
ms.reviewer: baolcsva
ms.openlocfilehash: cccc43c2133a0c83329c2a519b8e1c88e52eb16a
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107493066"
---
# <a name="troubleshoot-subscription-access-after-you-sign-a-microsoft-customer-agreement"></a>Előfizetés-hozzáférés hibaelhárítása a regisztráció Microsoft Ügyfélszerződés

Ez a cikk segítséget nyújt az előfizetéshez való hozzáférés hibaelhárításához, miután új előfizetést Microsoft Ügyfélszerződés. Az alábbi információk segítségével elháríthatja és megoldhatja a gyakori problémákat.

## <a name="troubleshoot-subscription-access"></a>Előfizetés-hozzáférés hibaelhárítása

Győződjön meg arról, hogy a számlázási fiók típusa Microsoft Ügyfélszerződés. A számlázási fiók típusát a következő Azure Portal **Cost Management + Billing.** További információ: [Ellenőrizze, hogy rendelkezik-e hozzáféréssel az MCA-hoz.](../understand/mca-understand-your-usage.md#check-access-to-a-microsoft-customer-agreement)

## <a name="troubleshoot-viewing-your-billing-account"></a>A számlázási fiók megtekintésével kapcsolatos hibák elhárítása

Ha nem tudja megtekintetni a számlázási fiókját, és több bérlője van, próbálja meg váltani a Azure Portal.

1. A jobb felső sarokban válassza Azure Portal Azure-fiókját.
1. Válassza a Címtár váltása lehetőséget.  
    :::image type="content" source="./media/troubleshoot-subscription-access/switch-directory.png" alt-text="Képernyőkép a Címtár váltása lehetőségről." lightbox="./media/troubleshoot-subscription-access/switch-directory.png" :::
1. A Címtár és előfizetés ablakban válassza ki a másik címtárat, hogy átváltson rá.  
    :::image type="content" source="./media/troubleshoot-subscription-access/select-directory.png" alt-text="Képernyőkép egy másik címtár kiválasztásának helyének megjelenítéséről." lightbox="./media/troubleshoot-subscription-access/select-directory.png" :::

## <a name="troubleshoot-account-access"></a>Fiókhoz való hozzáférés hibaelhárítása

Több Microsoft-fiókkal is lehet, amelyek ugyanazt az e-mail-címet használják. Lehet, hogy van egy személyes fiókja és egy munkahelyi fiókja. A Microsoft Ügyfélszerződés munkahelyi vagy iskolai fiókját használja. További információ: Győződjön meg arról, hogy munkahelyi vagy iskolai [fiókjával jelentkezik be.](https://support.microsoft.com/office/which-account-do-you-want-to-use-2b5bbd7a-7df6-4283-beff-8015e28eb7b9)

- Jelentkezzen be munkahelyi vagy iskolai fiókjával.  
    :::image type="content" source="./media/troubleshoot-subscription-access/two-accounts.png" alt-text="Képernyőkép a munkahelyi vagy iskolai fiók kiválasztásáról." lightbox="./media/troubleshoot-subscription-access/two-accounts.png" :::

## <a name="next-steps"></a>Következő lépések

- Olvassa el [a Microsoft Ügyfélszerződés dokumentációját.](https://docs.microsoft.com/azure/cost-management-billing/microsoft-customer-agreement/) 