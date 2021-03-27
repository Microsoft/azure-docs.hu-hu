---
title: Azure-előfizetési szabályzatok kezelése
description: Ismerje meg, hogyan kezelheti az Azure-előfizetési szabályzatokat az Azure-előfizetések címtárakba való áthelyezésének szabályozására.
author: anuragdalmia
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 03/10/2021
ms.reviewer: banders
ms.author: andalmia
ms.openlocfilehash: 105e090655021ed4046f8880ef9816d7f7ff559f
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2021
ms.locfileid: "105631861"
---
# <a name="manage-azure-subscription-policies"></a>Azure-előfizetési szabályzatok kezelése

>[!NOTE]
>Ez a funkció jelenleg előzetes verzióban érhető el, és fokozatosan ki van kapcsolva, így a Azure Portal még nem mindenki láthatja ezt a folyamatot.

Ebből a cikkből megtudhatja, hogyan konfigurálhatja az Azure-előfizetési szabályzatokat az előfizetési műveletekhez az Azure-előfizetések címtárakba való áthelyezésének

## <a name="prerequisites"></a>Előfeltételek

- Csak a címtár [globális rendszergazdái](../../active-directory/roles/permissions-reference.md#global-administrator) módosíthatják az előfizetési házirendeket. Az előfizetési szabályzatok szerkesztése előtt a globális rendszergazdának az [összes Azure-előfizetés és-felügyeleti csoport kezeléséhez](../../role-based-access-control/elevate-access-global-admin.md)meg kell adnia a hozzáférést. Ezután módosíthatják az előfizetési szabályzatokat.
- Az összes többi felhasználó csak az aktuális házirend-beállítást tudja olvasni.

## <a name="available-subscription-policy-settings"></a>Elérhető előfizetési szabályzatok beállításai

A következő házirend-beállításokkal szabályozhatja az Azure-előfizetések könyvtárakba való áthelyezését.

### <a name="subscriptions-leaving-aad-directory"></a>HRE könyvtárat elhagyó előfizetések

A házirend lehetővé teszi vagy leállítja a felhasználók számára az előfizetések áthelyezését az aktuális könyvtárból. Az [előfizetések tulajdonosai](../../role-based-access-control/built-in-roles.md#owner) [megváltoztathatják az Azure-előfizetések könyvtárát](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md) egy másikra, ahol a tagok. Irányítási kihívásokat jelent, így a globális rendszergazdák engedélyezhetik vagy letiltják a címtár felhasználói számára a címtár módosítását.

### <a name="subscriptions-entering-aad-directory"></a>HRE-címtárba belépő előfizetések

A házirend lehetővé teszi vagy leállítja a más címtárakból származó felhasználókat, akik hozzáféréssel rendelkeznek a jelenlegi címtárban, hogy az előfizetéseket az aktuális könyvtárba helyezze át. Az [előfizetések tulajdonosai](../../role-based-access-control/built-in-roles.md#owner) [megváltoztathatják az Azure-előfizetések könyvtárát](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md) egy másikra, ahol a tagok. Irányítási kihívásokat jelent, így a globális rendszergazdák engedélyezhetik vagy letiltják a címtár felhasználói számára a címtár módosítását.

### <a name="exempted-users"></a>Mentesített felhasználók

Az irányítási okok miatt a globális rendszergazdák letilthatják az összes előfizetési könyvtárat – a jelenlegi könyvtárból. Azonban előfordulhat, hogy bizonyos felhasználók számára engedélyezni szeretné a műveletet. Mindkét esetben beállíthatja a kivétel alá eső felhasználók listáját, amely lehetővé teszi a felhasználók számára, hogy megkerüljék a mindenki másnak érvényes házirend-beállítást.

## <a name="setting-subscription-policy"></a>Előfizetési szabályzat beállítása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. Navigáljon az **előfizetésekhez**. A **szabályzatok kezelése** a parancssorban látható.  
    :::image type="content" source="./media/manage-azure-subscription-policy/subscription-blade-manage-policies.png" alt-text="Képernyőfelvétel: az előfizetések kezelésével kapcsolatos házirendek." lightbox="./media/manage-azure-subscription-policy/subscription-blade-manage-policies.png" :::
1. Válassza a **házirendek kezelése** lehetőséget a címtárhoz beállított aktuális előfizetési szabályzatok részleteinek megtekintéséhez. Egy [emelt szintű engedélyekkel](../../role-based-access-control/elevate-access-global-admin.md) rendelkező globális rendszergazda módosíthatja a beállításokat, beleértve a kivétel alá eső felhasználók hozzáadását vagy eltávolítását.  
    :::image type="content" source="./media/manage-azure-subscription-policy/subscription-blade-policies.png" alt-text="A megadott házirend-beállításokat és a kivétel alá eső felhasználókat bemutató képernyőkép." lightbox="./media/manage-azure-subscription-policy/subscription-blade-policies.png" :::
1. A módosítások mentéséhez válassza az alján a **módosítások mentése** elemet. A módosítások azonnal érvénybe lépnek.

## <a name="read-subscription-policy"></a>Előfizetési szabályzat beolvasása

A nem globális rendszergazdák továbbra is megkereshetik az előfizetési házirend területét, hogy megtekintsék a címtár házirend-beállításait. Nem végezhetnek módosításokat. Az adatvédelmi okokból nem látják a mentesített felhasználók listáját. Megtekinthetik a globális rendszergazdáik számára, hogy elküldjék a szabályzat módosítására irányuló kéréseket, feltéve, hogy a címtár beállításai lehetővé teszik azokat.

:::image type="content" source="./media/manage-azure-subscription-policy/subscription-blade-policies-reader.png" alt-text="Képernyőfelvétel: az előfizetésekben lévő szabályzatok kezelése olvasóként." lightbox="./media/manage-azure-subscription-policy/subscription-blade-policies-reader.png" :::

## <a name="next-steps"></a>Következő lépések

- Olvassa el a [Cost Management + számlázási dokumentációt](../index.yml)