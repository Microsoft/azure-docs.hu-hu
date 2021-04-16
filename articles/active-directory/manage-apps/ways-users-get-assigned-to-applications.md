---
title: A felhasználók alkalmazásokhoz való hozzárendelésének Azure Active Directory
description: A felhasználók identitáskezelésre használt alkalmazáshoz Azure Active Directory hozzárendelése.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: reference
ms.date: 01/07/2021
ms.author: iangithinji
ms.openlocfilehash: 84700bca6ff306dbcce01a837c312c4c0c90066d
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376409"
---
# <a name="understand-how-users-are-assigned-to-apps-in-azure-active-directory"></a>A felhasználók alkalmazásokhoz való hozzárendelésének Azure Active Directory
Ez a cikk segít megérteni, hogyan rendeli hozzá a rendszer a felhasználókat egy alkalmazáshoz a bérlőben.

## <a name="how-do-users-get-assigned-to-an-application-in-azure-ad"></a>Hogyan rendelhetők hozzá a felhasználók egy alkalmazáshoz az Azure AD-ban?
Ahhoz, hogy egy felhasználó hozzáférjen egy alkalmazáshoz, először valamilyen módon hozzá kell rendelni. A hozzárendelést egy rendszergazda, egy üzleti delegált, vagy néha maga a felhasználó is el tudja végezni. Az alábbiakban azt ismertetjük, hogy a felhasználók hogyan rendelhetők hozzá alkalmazásokhoz:

*  A rendszergazda [közvetlenül hozzárendel egy felhasználót](./assign-user-or-group-access-portal.md) az alkalmazáshoz
*  A rendszergazda [hozzárendel egy csoportot,](./assign-user-or-group-access-portal.md) amelybe a felhasználó az alkalmazás tagja, beleértve a következőket:
    * A helyszínről szinkronizált csoport
    * A felhőben létrehozott statikus biztonsági csoport
    * A [felhőben](../enterprise-users/groups-dynamic-membership.md) létrehozott dinamikus biztonsági csoport
    * A Microsoft 365 létrehozott Microsoft 365 csoport
    * A [Minden felhasználó](../fundamentals/active-directory-groups-create-azure-portal.md) csoport
*  A rendszergazda engedélyezi [az önkiszolgáló alkalmazás-hozzáférést,](./manage-self-service-access.md) hogy a felhasználók alkalmazásokat adjanak hozzá [Saját alkalmazások](../user-help/my-apps-portal-end-user-access.md) **alkalmazás** hozzáadása funkcióval üzleti jóváhagyás **nélkül**
*  A rendszergazda engedélyezi az [önkiszolgáló](./manage-self-service-access.md) alkalmazás-hozzáférést, hogy a felhasználók alkalmazásokat adjanak hozzá az [Saját alkalmazások](../user-help/my-apps-portal-end-user-access.md) **Alkalmazás** hozzáadása funkcióval, de csak az üzleti jóváhagyók egy kiválasztott készletének előzetes **jóváhagyásával**
*  A rendszergazda engedélyezi [az önkiszolgáló](../enterprise-users/groups-self-service-management.md) csoportkezelést, hogy a felhasználók olyan csoporthoz csatlakozzanak, amelyekhez egy alkalmazás hozzá van **rendelve üzleti jóváhagyás nélkül**
*  A rendszergazda engedélyezi az [önkiszolgáló](../enterprise-users/groups-self-service-management.md) csoportkezelést, hogy a felhasználók olyan csoporthoz csatlakozzanak, amelyekhez egy alkalmazás hozzá van rendelve, de csak az üzleti jóváhagyók egy kiválasztott csoportjának előzetes **jóváhagyásával**
*  A rendszergazda egy licencet rendel egy felhasználóhoz közvetlenül egy külső alkalmazáshoz, például a [Microsoft 365](https://products.office.com/)
*  A rendszergazda hozzárendel egy licencet egy olyan csoporthoz, amely tagja a felhasználónak egy külső alkalmazásnak, például a [Microsoft 365](https://products.office.com/)
*  A [rendszergazda beleegyezik, hogy](../develop/howto-convert-app-to-be-multi-tenant.md) az alkalmazás minden felhasználó számára használható legyen, majd a felhasználó bejelentkezik az alkalmazásba
* A felhasználó [maguk is hozzájárulnak egy alkalmazáshoz](../develop/howto-convert-app-to-be-multi-tenant.md) az alkalmazásba való bejelentkezéssel

## <a name="next-steps"></a>Következő lépések
* [Gyorsútmutató-sorozat az alkalmazáskezelésről](view-applications-portal.md)
* [Mi az alkalmazáskezelés?](what-is-application-management.md)
* [Mi az az egyszeri bejelentkezés?](what-is-single-sign-on.md)