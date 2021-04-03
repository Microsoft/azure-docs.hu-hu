---
title: Feltételes hozzáférési szabályzatok áttelepíthetők a többtényezős hitelesítéssel – Azure Active Directory
description: Ez a cikk bemutatja, hogyan telepítheti át a többtényezős hitelesítést igénylő klasszikus szabályzatot a Azure Portalban.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 05/26/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: nigu
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5cc99241f5fa7c09c69db13dcb3f6c3aaa75e376
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "87027393"
---
# <a name="migrate-a-classic-policy-in-the-azure-portal"></a>Klasszikus szabályzat migrálása a Azure Portal

Ez a cikk bemutatja, hogyan telepíthet át egy klasszikus szabályzatot, amely megköveteli a **többtényezős hitelesítést** a felhőalapú alkalmazásokhoz. Bár ez nem előfeltétel, javasoljuk, hogy olvassa el a klasszikus szabályzatok áttelepítése [a Azure Portalt](policy-migration.md) , mielőtt megkezdené a klasszikus házirendek áttelepítését.

![A Salesforce-alkalmazásra vonatkozó MFA-t igénylő klasszikus szabályzatok részletei](./media/policy-migration/33.png)

Az áttelepítési folyamat a következő lépésekből áll:

1. A konfigurációs beállítások beszerzéséhez [nyissa meg a klasszikus szabályzatot](#open-a-classic-policy) .
1. Hozzon létre egy új Azure AD feltételes hozzáférési szabályzatot a klasszikus szabályzat cseréjéhez. 
1. Tiltsa le a klasszikus szabályzatot.

## <a name="open-a-classic-policy"></a>Klasszikus szabályzat megnyitása

1. A [Azure Portal](https://portal.azure.com)nyissa meg **Azure Active Directory**  >  **biztonsági**  >  **feltételes hozzáférés** lehetőséget.
1. Válassza a **klasszikus szabályzatok** lehetőséget.

   ![Klasszikus szabályzatok nézet](./media/policy-migration-mfa/12.png)

1. A klasszikus szabályzatok listájában válassza ki az áttelepíteni kívánt szabályzatot. Dokumentálja a konfigurációs beállításokat, hogy újból létre lehessen hozni egy új feltételes hozzáférési szabályzatot.

## <a name="create-a-new-conditional-access-policy"></a>Új feltételes hozzáférési szabályzat létrehozása

1. A [Azure Portal](https://portal.azure.com)nyissa meg **Azure Active Directory**  >  **biztonsági**  >  **feltételes hozzáférés** lehetőséget.
1. Új feltételes hozzáférési szabályzat létrehozásához válassza az **új szabályzat** lehetőséget.
1. Az **új** lap **név** szövegmezőbe írja be a szabályzat nevét.
1. A **hozzárendelések** szakaszban kattintson a **felhasználók és csoportok** elemre.
   1. Ha a klasszikus szabályzatban minden felhasználó van kiválasztva, kattintson a **minden felhasználó** elemre. 
   1. Ha a klasszikus szabályzatban csoportok vannak kiválasztva, kattintson a **felhasználók és csoportok kiválasztása** elemre, majd válassza ki a szükséges felhasználókat és csoportokat.
   1. Ha a kizárt csoportok vannak, kattintson a **kizárás** lapra, majd válassza ki a szükséges felhasználókat és csoportokat. 
   1. Válassza a **kész** lehetőséget
1. A **hozzárendelés** szakaszban kattintson a **felhőalapú alkalmazások vagy műveletek** elemre.
1. A **Cloud apps vagy Actions** oldalon hajtsa végre a következő lépéseket:
   1. Kattintson az **alkalmazások kiválasztása** elemre.
   1. Kattintson a **Kiválasztás** elemre.
   1. A **kiválasztás** lapon válassza ki a felhőalapú alkalmazást, majd kattintson a **kiválasztás** elemre.
   1. A **Cloud apps** lapon kattintson a **kész** gombra.
1. Ha a **többtényezős hitelesítés** van kiválasztva:
   1. A **hozzáférés-vezérlések** szakaszban kattintson a **Grant (Engedélyezés**) elemre.
   1. A **támogatás** lapon kattintson a **hozzáférés engedélyezése** elemre, majd kattintson a **többtényezős hitelesítés megkövetelése** lehetőségre.
   1. Kattintson a **Kiválasztás** elemre.
1. Kattintson **a** be gombra a szabályzat engedélyezéséhez, majd válassza a **Mentés** lehetőséget.

   ![Feltételes hozzáférési szabályzat létrehozása](./media/policy-migration-mfa/conditional-access-policy-migration.png)

## <a name="disable-the-classic-policy"></a>A klasszikus szabályzat letiltása

A klasszikus szabályzat letiltásához kattintson a **Letiltás** gombra a **részletek** nézetben.

![Klasszikus szabályzatok letiltása](./media/policy-migration-mfa/14.png)

## <a name="next-steps"></a>Következő lépések

- A klasszikus házirendek áttelepítésével kapcsolatos további információkért lásd: [klasszikus szabályzatok áttelepítése a Azure Portal](policy-migration.md).
- [A feltételes hozzáféréshez csak jelentési módot használhat az új házirend-döntések hatásának megállapításához.](concept-conditional-access-report-only.md)