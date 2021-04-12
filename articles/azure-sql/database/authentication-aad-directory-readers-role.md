---
title: Az Azure SQL Azure Active Directory Directory-olvasói szerepköre
description: Ismerje meg a címtár-olvasó szerepkörét az Azure SQL Azure AD-ben.
ms.service: sql-db-mi
ms.subservice: security
ms.custom: azure-synapse
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 08/14/2020
ms.openlocfilehash: d764c6b6cff6a0ba23d659d4fda63e21aac9b155
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105644184"
---
# <a name="directory-readers-role-in-azure-active-directory-for-azure-sql"></a>Az Azure SQL Azure Active Directory Directory-olvasói szerepköre

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

> [!NOTE]
> A jelen cikkben szereplő funkció **nyilvános előzetes** verzióban érhető el.

Azure Active Directory (Azure AD) bevezetést kapott a [felhőalapú csoportok használatával a szerepkör-hozzárendelések Azure Active Directory (előzetes verzió) kezeléséhez](../../active-directory/roles/groups-concept.md). Ez lehetővé teszi, hogy az Azure AD-szerepkörök hozzá legyenek rendelve a csoportokhoz.

Ha [felügyelt identitást](../../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) engedélyez a Azure SQL Database, az Azure SQL felügyelt példányának vagy az Azure szinapszis Analyticsnek, az Azure ad [**Directory-olvasók**](../../active-directory/roles/permissions-reference.md#directory-readers) szerepkört hozzá kell rendelni ahhoz az identitáshoz, amely lehetővé teszi az [Azure ad-Graph API](/graph/migrate-azure-ad-graph-planning-checklist)olvasási hozzáférését. A SQL Database és az Azure szinapszis felügyelt identitását kiszolgáló identitásának nevezzük. Az SQL felügyelt példány felügyelt identitását felügyelt példány identitásának nevezzük, és a rendszer automatikusan hozzárendeli a példány létrehozásakor. A kiszolgáló identitásának SQL Database vagy az Azure Szinapszishoz való hozzárendelésével kapcsolatos további információkért lásd: [egyszerű szolgáltatásnév engedélyezése az Azure ad-felhasználók létrehozásához](authentication-aad-service-principal.md#enable-service-principals-to-create-azure-ad-users).

A **címtár-olvasók** szerepkör a következőhöz szükséges:

- Azure AD-bejelentkezések létrehozása a felügyelt SQL-példányhoz
- Azure AD-felhasználók megszemélyesítése az Azure SQL-ben
- Windows-hitelesítést használó felhasználók SQL Server migrálása az SQL felügyelt példányra Azure AD-hitelesítéssel (az [Alter User (Transact-SQL)](/sql/t-sql/statements/alter-user-transact-sql?view=azuresqldb-mi-current&preserve-view=true#d-map-the-user-in-the-database-to-an-azure-ad-login-after-migration) parancs használatával)
- Az SQL felügyelt példányához tartozó Azure AD-rendszergazda módosítása
- [Egyszerű szolgáltatások (alkalmazások)](authentication-aad-service-principal.md) engedélyezése Azure ad-felhasználók létrehozásához az Azure SQL-ben

## <a name="assigning-the-directory-readers-role"></a>A Directory-olvasók szerepkör kiosztása

Ahhoz, hogy a [**címtár-olvasók**](../../active-directory/roles/permissions-reference.md#directory-readers) szerepkört identitáshoz rendelje, a [globális rendszergazda](../../active-directory/roles/permissions-reference.md#global-administrator) vagy a [Kiemelt szerepkörű rendszergazda](../../active-directory/roles/permissions-reference.md#privileged-role-administrator) jogosultságokkal rendelkező felhasználóra van szükség. Azok a felhasználók, akik gyakran kezelnek vagy telepítenek SQL Database, SQL felügyelt példányt vagy Azure Szinapszisot, nem férhetnek hozzá ezekhez a magas jogosultsági szintű szerepkörökhöz. Ez gyakran okoz szövődményeket olyan felhasználók számára, akik nem tervezett Azure SQL-erőforrásokat hoznak létre, vagy segítségre van szükségük olyan magas jogosultsági szintű szerepkörű tagoktól, amelyek gyakran nem érhetők el nagyméretű szervezetekben.

SQL felügyelt példány esetén a **címtár-olvasók** szerepkört hozzá kell rendelni a felügyelt példány identitásához, mielőtt beállít [egy Azure ad-rendszergazdát a felügyelt példányhoz](authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance). 

A címtár- **olvasók** szerepkörnek a kiszolgáló identitáshoz való hozzárendeléséhez nincs szükség a SQL Database vagy az Azure szinapszis szolgáltatáshoz a logikai kiszolgálóhoz tartozó Azure ad-rendszergazda beállításakor. Ahhoz azonban, hogy egy Azure ad-objektumot az Azure AD-alkalmazás nevében SQL Database vagy Azure Szinapszisban lehessen létrehozni, szükség van a **címtár-olvasók** szerepkörre. Ha a szerepkör nincs hozzárendelve az SQL logikai kiszolgáló identitásához, az Azure AD-felhasználók létrehozása az Azure SQL-ben sikertelen lesz. További információ: [Azure Active Directory egyszerű szolgáltatásnév az Azure SQL](authentication-aad-service-principal.md)-ben.

## <a name="granting-the-directory-readers-role-to-an-azure-ad-group"></a>A Directory-olvasók szerepkör megadása egy Azure AD-csoportnak

A jelenleg **nyilvános előzetes** verzióban elérhető [globális rendszergazda](../../active-directory/roles/permissions-reference.md#global-administrator) vagy [Kiemelt szerepkörű rendszergazda](../../active-directory/roles/permissions-reference.md#privileged-role-administrator) létrehoz egy Azure ad-csoportot, és hozzárendeli a [**címtár-olvasók**](../../active-directory/roles/permissions-reference.md#directory-readers) engedélyt a csoporthoz. Ez lehetővé teszi a csoport tagjai számára az Azure AD-Graph API elérését. Ezen kívül az Azure AD-felhasználók, akik ennek a csoportnak a tulajdonosai, jogosultak új tagok hozzárendelésére a csoport számára, beleértve az Azure SQL logikai kiszolgálók identitásait is.

Ennél a megoldásnál továbbra is magas jogosultsági szintű felhasználó (globális rendszergazda vagy Kiemelt szerepkörű rendszergazda) létrehozására van szükség a csoportok létrehozásához és a felhasználók egyszeri tevékenységhez való hozzárendeléséhez, de az Azure AD-csoport tulajdonosai további tagokat is hozzárendelhet. Így a jövőben nem kell magas jogosultsági szintű felhasználót bevezetni az Azure AD-bérlőben található összes SQL-adatbázis, SQL-felügyelt példány vagy Azure szinapszis-kiszolgáló konfigurálásához.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: címtár-olvasók szerepkör hozzárendelése Azure AD-csoporthoz és szerepkör-hozzárendelések kezelése](authentication-aad-directory-readers-role-tutorial.md)