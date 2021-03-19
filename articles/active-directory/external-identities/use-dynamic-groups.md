---
title: Dinamikus csoportok és B2B együttműködés – Azure Active Directory | Microsoft Docs
description: Bemutatja, hogyan használhatók az Azure AD dinamikus csoportok Azure Active Directory B2B együttműködéssel
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 02/28/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b820b8b9606795709d03414fa14ec29a1b5c519
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92441555"
---
# <a name="dynamic-groups-and-azure-active-directory-b2b-collaboration"></a>Dinamikus csoportok és Azure Active Directory B2B-együttműködés

## <a name="what-are-dynamic-groups"></a>Mik azok a dinamikus csoportok?
A Azure Active Directory (Azure AD) biztonsági csoporttagság dinamikus konfigurációja [a Azure Portalben](https://portal.azure.com)érhető el. A rendszergazdák a felhasználói attribútumok (például userType, részleg vagy ország/régió) alapján hozhatnak létre szabályokat az Azure AD-ben létrehozott csoportok feltöltéséhez. A tagok az attribútumok alapján automatikusan hozzáadhatók vagy eltávolíthatók egy biztonsági csoportból. Ezek a csoportok hozzáférést biztosíthatnak az alkalmazásokhoz és a Felhőbeli erőforrásokhoz (SharePoint-webhelyekhez, dokumentumokhoz), valamint licenceket rendelhet a tagokhoz. További információ a dinamikus csoportokról a [Azure Active Directory dedikált csoportjaiban](../fundamentals/active-directory-groups-create-azure-portal.md).

A megfelelő [prémium szintű Azure ad P1 vagy P2 licencelés](https://azure.microsoft.com/pricing/details/active-directory/) szükséges a dinamikus csoportok létrehozásához és használatához. További információt a [dinamikus csoporttagság Azure Active Directoryban való létrehozásához](../enterprise-users/groups-dynamic-membership.md)című cikkben talál.

## <a name="creating-an-all-users-dynamic-group"></a>"Minden felhasználó" dinamikus csoport létrehozása
Létrehozhat egy olyan csoportot, amely a bérlőn belüli összes felhasználót egy tagsági szabály használatával hozza létre. Ha a későbbiekben a felhasználókat hozzáadják vagy eltávolítják a bérlőből, a rendszer automatikusan módosítja a csoport tagságát.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) egy olyan fiókkal, amely a bérlő globális rendszergazdai vagy felhasználói rendszergazdai szerepköréhez van rendelve.
1. Válassza a **Azure Active Directory** lehetőséget.
2. A **kezelés** területen válassza a **csoportok** lehetőséget, majd válassza az **új csoport** lehetőséget.
1. Az **új csoport** lap **csoport típusa** területén válassza a **Biztonság** elemet. Adja meg a **csoport nevét** és a **csoport leírását** az új csoport számára. 
2. A **tagság típusa** területen válassza a **dinamikus felhasználó** lehetőséget, majd válassza a **dinamikus lekérdezés hozzáadása** lehetőséget. 
4. A **szabály szintaxisa** szövegmező felett válassza a **Szerkesztés** lehetőséget. A **szabály szintaxisának szerkesztése** lapon írja be a következő kifejezést a szövegmezőbe:

   ```
   user.objectId -ne null
   ```
1. Válassza az **OK** lehetőséget. A szabály a szabály szintaxisa mezőben jelenik meg:

   ![Szabály szintaxisa az összes felhasználó dinamikus csoportjához](media/use-dynamic-groups/all-user-rule-syntax.png)

1.  Kattintson a **Mentés** gombra. Az új dinamikus csoport mostantól tartalmazza a B2B vendég felhasználókat és a tagokat is.


1. Válassza a **Létrehozás** lehetőséget az **új csoport** lapon a csoport létrehozásához.

## <a name="creating-a-group-of-members-only"></a>Csak tagok csoportjának létrehozása

Ha azt szeretné, hogy a csoport kizárjon vendég felhasználókat, és csak a bérlő tagjait tartalmazza, hozzon létre egy dinamikus csoportot a fentiekben leírtak szerint, de a **szabály szintaxisa** mezőben adja meg a következő kifejezést:

```
(user.objectId -ne null) and (user.userType -eq "Member")
```

Az alábbi képen egy dinamikus csoport szabály-szintaxisa látható, amely csak tagokat tartalmaz, és kizárja a vendégeket.

![Azt a szabályt mutatja, ahol a felhasználó típusa egyenlő tag](media/use-dynamic-groups/all-member-user-rule-syntax.png)

## <a name="creating-a-group-of-guests-only"></a>Csak a vendégek csoportjának létrehozása

Hasznos lehet olyan új dinamikus csoport létrehozása is, amely csak vendég felhasználókat tartalmaz, így házirendeket (például Azure AD feltételes hozzáférési szabályzatokat) alkalmazhat rájuk. Hozzon létre egy dinamikus csoportot a fentiekben leírtak szerint, de a **szabály szintaxisa** mezőben adja meg a következő kifejezést:

```
(user.objectId -ne null) and (user.userType -eq "Guest")
```

Az alábbi képen egy dinamikus csoport szabály-szintaxisa látható, amely csak vendégeket tartalmaz, és kizárja a tagok felhasználóit.

![Azt a szabályt mutatja, ahol a felhasználói típus egyenlő vendég](media/use-dynamic-groups/all-guest-user-rule-syntax.png)

## <a name="next-steps"></a>Következő lépések

- [B2B-együttműködés felhasználói tulajdonságok](user-properties.md)
- [B2B csoportmunka-felhasználó hozzáadása egy szerepkörhöz](add-guest-to-role.md)
- [Feltételes hozzáférés VÁLLALATKÖZI együttműködéssel rendelkező felhasználók számára](conditional-access.md)