---
title: Nemrég törölt felhasználó visszaállítása vagy végleges eltávolítása – Azure AD
description: A helyreállítható felhasználók megtekintése, törölt felhasználók visszaállítása vagy a felhasználó végleges törlése Azure Active Directory használatával.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 10/23/2020
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd4d42e1cafa58aaf6a47188c9e86c24ac175047
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92514389"
---
# <a name="restore-or-remove-a-recently-deleted-user-using-azure-active-directory"></a>Nemrég törölt felhasználó visszaállítása vagy eltávolítása a Azure Active Directory használatával
A felhasználó törlése után a fiók 30 napig felfüggesztett állapotban marad. A 30 napos időszak alatt a felhasználói fiók visszaállítható az összes tulajdonságával együtt. A 30 napos időszak elteltével az állandó törlési folyamat automatikusan elindul.

Megtekintheti a visszaállítható felhasználókat, visszaállíthat egy törölt felhasználót, vagy véglegesen törölhet egy felhasználót Azure Active Directory (Azure AD) használatával a Azure Portal.

>[!Important]
>Sem Ön, sem a Microsoft ügyfélszolgálata nem állíthatja vissza véglegesen törölt felhasználókat.

## <a name="required-permissions"></a>Szükséges engedélyek
A felhasználók visszaállításához és végleges törléséhez a következő szerepkörök egyikét kell használnia.

- Globális rendszergazda

- Partneri Tier1-támogatás

- Partneri szint-támogatás

- Felhasználói rendszergazda

## <a name="view-your-restorable-users"></a>Helyreállítható felhasználók megtekintése
Megtekintheti az összes olyan felhasználót, amely 30 napnál régebben törölve lett. Ezeket a felhasználókat vissza lehet állítani.

### <a name="to-view-your-restorable-users"></a>A helyreállítható felhasználók megtekintése
1. Jelentkezzen be a [Azure Portal](https://portal.azure.com/) globális rendszergazdai fiókkal a szervezet számára.

2. Válassza a **Azure Active Directory** lehetőséget, válassza a **felhasználók**, majd a **törölt felhasználók** elemet.

    Tekintse át a visszaállítani kívánt felhasználók listáját.

    ![Felhasználók – törölt felhasználók lap, amely továbbra is visszaállítható felhasználók számára](media/active-directory-users-restore/users-deleted-users-view-restorable.png)

## <a name="restore-a-recently-deleted-user"></a>Nemrég törölt felhasználó visszaállítása

Ha töröl egy felhasználói fiókot a szervezetből, a fiók felfüggesztett állapotba kerül, és az összes kapcsolódó szervezeti információ megőrződik. Egy felhasználó visszaállításakor a rendszer a szervezeti adatokat is visszaállítja.

> [!Note]
> A felhasználó visszaállítása után a rendszer a törléskor a felhasználóhoz rendelt licenceket is visszaállítja, még akkor is, ha nincsenek elérhető helyek a licencekhez. Ha ezt követően több licencet használ, mint amennyit megvásárolt, a szervezet átmenetileg nem felel meg a licencek használatának.

### <a name="to-restore-a-user"></a>Felhasználó visszaállítása
1. A **felhasználók által törölt felhasználók** lapon keresse meg és válassza ki az elérhető felhasználók egyikét. Például: _Mary Parker_.

2. Válassza a **felhasználó visszaállítása** lehetőséget.

    ![Felhasználók – törölt felhasználók lap, a felhasználó visszaállítása lehetőség kiemelve](media/active-directory-users-restore/users-deleted-users-restore-user.png)

## <a name="permanently-delete-a-user"></a>Felhasználó végleges törlése
Véglegesen törölheti a felhasználót a szervezetből anélkül, hogy az automatikus törlés 30 napját kellene várnia. Egy véglegesen törölt felhasználót nem állítható vissza Ön, egy másik rendszergazda vagy a Microsoft ügyfélszolgálata.

>[!Note]
>Ha véglegesen töröl egy felhasználót tévedésből, létre kell hoznia egy új felhasználót, és manuálisan kell megadnia az összes korábbi információt. Az új felhasználók létrehozásával kapcsolatos további információkért lásd: [felhasználók hozzáadása vagy törlése](add-users-azure-active-directory.md).

### <a name="to-permanently-delete-a-user"></a>Felhasználó végleges törlése

1. A **felhasználók által törölt felhasználók** lapon keresse meg és válassza ki az elérhető felhasználók egyikét. Például: _Rae Huff_.

2. Válassza a **véglegesen törlés** lehetőséget.

    ![Felhasználók – törölt felhasználók lap, a felhasználó törlése lehetőség kiemelve](media/active-directory-users-restore/users-deleted-users-permanent-delete-user.png)

## <a name="next-steps"></a>Következő lépések
Miután visszaállította vagy törölte a felhasználókat, a következőket teheti:

- [Felhasználók hozzáadása vagy törlése](add-users-azure-active-directory.md)

- [Szerepkörök hozzárendelése felhasználókhoz](active-directory-users-assign-role-azure-portal.md)

- [Profil adatainak hozzáadása vagy módosítása](active-directory-users-profile-azure-portal.md)

- [Vendég felhasználók hozzáadása egy másik szervezethez](../external-identities/what-is-b2b.md)

További információ az [Azure ad](../enterprise-users/index.yml)-beli felhasználói felügyeleti feladatokról.
