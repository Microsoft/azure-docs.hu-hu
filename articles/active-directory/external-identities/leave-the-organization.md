---
title: A szervezetet vendég felhasználóként hagyja Azure Active Directory
description: Azt mutatja, hogy az Azure AD B2B vendég felhasználó hogyan hagyhatja el a szervezetet a hozzáférési panel használatával.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 06/13/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: fc3ef7d168c17ec10fe64925adbda7044a2a4c82
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "87909311"
---
# <a name="leave-an-organization-as-a-guest-user"></a>Szervezet elhagyása vendégfelhasználóként

Egy Azure Active Directory (Azure AD) B2B vendég felhasználó dönthet úgy, hogy bármikor elhagyja a szervezetet, ha már nincs szükségük az adott szervezettől származó alkalmazások használatára vagy a társítás fenntartására. Egy felhasználó saját maga is hagyhatja a szervezetét anélkül, hogy kapcsolatba kellene lépnie a rendszergazdával.

> [!NOTE]
> A vendég felhasználó nem hagyhat szervezetet, ha a fiókja le van tiltva a Kezdőlap bérlőben vagy az erőforrás-bérlőben. Ha a fiókja le van tiltva, a vendég felhasználónak kapcsolatba kell lépnie a bérlői rendszergazdával, aki törölheti a vendég fiókot, vagy engedélyezheti a vendég fiókját, hogy a felhasználó elhagyhatja a szervezetet.

## <a name="leave-an-organization"></a>Szervezet elhagyása

A szervezet elhagyásához kövesse az alábbi lépéseket.

1. A következő lépések egyikével lépjen a hozzáférési panel profilja oldalra:
   
   - A [Azure Portal](https://portal.azure.com)kattintson a jobb felső sarokban található nevére, majd válassza a **fiók megtekintése** lehetőséget.
   - Nyissa meg a [hozzáférési panelt](https://myapps.microsoft.com), kattintson a jobb felső sarokban található nevére, majd a **szervezetek** elem mellett válassza a beállítások ikont (fogaskerék).
 
   ![A hozzáférési panel felhasználói beállításait bemutató képernyőkép](media/leave-the-organization/UserSettings.png) 

   > [!NOTE]
   > Ha még nem jelentkezett be a meghagyni kívánt szervezetbe, a **szervezetek** területen kattintson a Bejelentkezés gombra a szervezet neve melletti **céges hivatkozás elhagyása érdekében** . Miután bejelentkezett, kattintson a nevére a jobb felső sarokban és a **szervezetek** mellett, majd válassza a beállítások ikont (fogaskerék).

3. A **szervezetek** területen keresse meg a kihagyni kívánt szervezetet, és válassza a **szervezet elhagyása** lehetőséget.

   ![A felhasználói felületen a kilépés a szervezeti felületről lehetőséget bemutató képernyőkép](media/leave-the-organization/LeaveOrg.png)

4. Ha a rendszer megerősítést kér, válassza a **Kilépés** lehetőséget. 

## <a name="account-removal"></a>Fiók eltávolítása

Amikor egy felhasználó elhagyja a szervezetet, a felhasználói fiók "Soft Deleted" a címtárban. Alapértelmezés szerint a felhasználói objektum az Azure AD **törölt felhasználók** területén kerül át, de 30 napig nem törlődik véglegesen. Ez a törlés lehetővé teszi, hogy a rendszergazda visszaállítsa a felhasználói fiókot (beleértve a csoportokat és az engedélyeket is), ha a felhasználó a 30 napos időszakon belül kéri a fiók visszaállítását.

Ha szükséges, a bérlői rendszergazda a 30 napos időszakban bármikor véglegesen törölheti a fiókot. Ehhez tegye a következőket:

1. Az [Azure Portalon](https://portal.azure.com) válassza az **Azure Active Directory** lehetőséget.
2. A **Kezelés** menüpontban válassza a **Felhasználók** lehetőséget.
3. Válassza a **törölt felhasználók** lehetőséget.
4. Jelölje be a törölt felhasználó melletti jelölőnégyzetet, majd válassza a **véglegesen törlés** lehetőséget.

Ha véglegesen töröl egy felhasználót, ez a művelet visszavonhatatlan.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>Következő lépések

- Az Azure AD B2B áttekintése: [Mi az az Azure ad B2B Collaboration?](what-is-b2b.md)



