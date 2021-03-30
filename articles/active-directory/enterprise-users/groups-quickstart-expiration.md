---
title: Csoport lejárati szabályzatának rövid útmutatója – Azure AD | Microsoft Docs
description: Microsoft 365 csoportok lejárata – Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: quickstart
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: a6bc76214ca2bedfc66ad3165cafe904584d42db
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96546318"
---
# <a name="quickstart-set-microsoft-365-groups-to-expire-in-azure-active-directory"></a>Gyors útmutató: Microsoft 365 csoportok lejáratának beállítása Azure Active Directory

Ebben a rövid útmutatóban a Microsoft 365-csoportok elévülési szabályzatát állíthatja be. Ha a felhasználók beállíthatják a saját csoportjaikat, megsokszorozódhatnak a nem használt csoportok. A nem használt csoportok kezelésének egy módja, ha elévülési időt állít be hozzájuk, ezzel csökkentve a csoportok manuális törléséhez szükséges karbantartási időt.

Az elévülési szabályzat nem bonyolult:

- A felhasználói tevékenységgel rendelkező csoportok automatikusan megújulnak a közeljövőben
- A csoporttulajdonosok értesítést kapnak, hogy újítsák meg az elévülő csoportjukat
- A meg nem újított csoport törlődik
- Egy törölt Microsoft 365 csoport egy csoport tulajdonosa vagy egy Azure AD-rendszergazda által 30 napon belül visszaállítható.

> [!NOTE]
> A csoportok mostantól az Azure AD Intelligence használatával automatikusan megújulnak, függetlenül attól, hogy a legutóbbi használatban vannak-e. Ez a megújítási döntés a különböző Microsoft 365-szolgáltatások, például az Outlook, a SharePoint, a Teams, a Yammer és egyebek csoportjain belüli felhasználói tevékenységeken alapul.

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisite"></a>Előfeltétel

 A csoport lejáratának beállításához szükséges legkevésbé Kiemelt szerepkör a szervezet felhasználói rendszergazdája.

## <a name="turn-on-user-creation-for-groups"></a>Felhasználói létrehozás bekapcsolása a csoportokhoz

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) egy felhasználói rendszergazdai fiókkal.

2. Válassza a **Csoportok**, majd az **Általános** elemet.
  
   ![Önkiszolgáló csoport beállításai lap](./media/groups-quickstart-expiration/self-service-settings.png)

3. Beállíthatja, hogy a  **felhasználók létrehozhatnak Microsoft 365 csoportokat** **Igen** értékre.

4. Ha elkészült, a csoportbeállítások mentéséhez kattintson a **Mentés** gombra.

## <a name="set-group-expiration"></a>Csoportok elévülésének beállítása

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), és válassza a **Azure Active Directory**  >  **csoportok**  >  **lejárata** lehetőséget a lejárat beállításainak megnyitásához.
  
   ![A csoport lejárati beállításainak lapja](./media/groups-quickstart-expiration/expiration-settings.png)

2. Állítsa be az elévülési intervallumot. Válasszon ki egy előre beállított értéket, vagy adjon meg egy 31 napon túli egyéni értéket. 

3. Adjon meg egy e-mail-címet, ahova a rendszer az elévülési értesítéseket küldheti, ha egy csoportnak nincs tulajdonosa.

4. Ehhez a rövid útmutatóhoz állítsa a **Microsoft 365 csoportok engedélyezése az** **összesre** lehetőséget.

5. Ha elkészült, az elévülési beállítások mentéséhez kattintson a **Mentés** gombra.

Ennyi az egész! Ebben a rövid útmutatóban sikeresen beállította a kijelölt Microsoft 365 csoportok elévülési szabályzatát.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

### <a name="to-remove-the-expiration-policy"></a>Az elévülési szabályzat eltávolítása

1. Győződjön meg arról, hogy bejelentkezett a [Azure Portalba](https://portal.azure.com) egy olyan fiókkal, amely az Azure ad-szervezet globális rendszergazdája.
2. Válassza ki **Azure Active Directory**  >  **csoportok**  >  **lejáratát**.
3. **Ezen Microsoft 365 csoportok engedélyezésének lejáratának** beállítása **none** értékre.

### <a name="to-turn-off-user-creation-for-groups"></a>Felhasználói létrehozás kikapcsolása a csoportokhoz

1. Válassza ki a **Azure Active Directory**  >  **csoportok**  >  **általános** elemet. 
2. A **felhasználók Microsoft 365 csoportokat hozhatnak létre az Azure-portálokon** a **nem** értékre.

## <a name="next-steps"></a>Következő lépések

További információ a Lejáratról, beleértve a PowerShell-utasításokat és a technikai korlátozásokat, lásd a következő cikket:

> [!div class="nextstepaction"]
> [Elévülési szabályzat PowerShell](groups-lifecycle.md)
