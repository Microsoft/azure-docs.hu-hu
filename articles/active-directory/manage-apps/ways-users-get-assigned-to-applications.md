---
title: A felhasználók Azure Active Directory alkalmazásokhoz való hozzárendelésének ismertetése
description: Ismerje meg, hogy a felhasználók hogyan vannak hozzárendelve egy olyan alkalmazáshoz, amely Azure Active Directoryt használ az Identitáskezelés kezeléséhez.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: reference
ms.date: 01/07/2021
ms.author: kenwith
ms.openlocfilehash: 161df0446c9478ca0f2b135c1e426f3786b164fc
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99257440"
---
# <a name="understand-how-users-are-assigned-to-apps-in-azure-active-directory"></a>A felhasználók Azure Active Directory alkalmazásokhoz való hozzárendelésének ismertetése
Ebből a cikkből megtudhatja, hogyan rendelhető hozzá a felhasználók egy alkalmazáshoz a bérlőben.

## <a name="how-do-users-get-assigned-to-an-application-in-azure-ad"></a>Hogyan rendelhetők hozzá a felhasználók az Azure AD-alkalmazásokhoz?
Ahhoz, hogy egy felhasználó hozzáférjen egy alkalmazáshoz, előbb hozzá kell rendelnie azt valamilyen módon. A hozzárendelést egy rendszergazda, egy üzleti meghatalmazott vagy esetenként a felhasználó hajthatja végre. Az alábbiakban a felhasználók az alkalmazásokhoz való hozzárendelésének módjait ismertetjük:

*  A rendszergazda közvetlenül [rendel hozzá egy felhasználót](./assign-user-or-group-access-portal.md) az alkalmazáshoz
*  A rendszergazda [olyan csoportot rendel hozzá](./assign-user-or-group-access-portal.md) , amelynek a felhasználó tagja az alkalmazásnak, beleértve a következőket:
    * A helyszíni környezetből szinkronizált csoport
    * A felhőben létrehozott statikus biztonsági csoport
    * A felhőben létrehozott [dinamikus biztonsági csoport](../enterprise-users/groups-dynamic-membership.md)
    * A felhőben létrehozott Microsoft 365 csoport
    * A [minden felhasználó](../fundamentals/active-directory-groups-create-azure-portal.md) csoport
*  A rendszergazda lehetővé teszi az [önkiszolgáló alkalmazásokhoz való hozzáférést](./manage-self-service-access.md) , lehetővé téve a felhasználók számára, hogy a [saját alkalmazások](../user-help/my-apps-portal-end-user-access.md) **hozzáadása** funkció használatával **üzleti jóváhagyás nélkül** vegyenek fel alkalmazást
*  A rendszergazda lehetővé teszi az [önkiszolgáló alkalmazásokhoz való hozzáférést](./manage-self-service-access.md) , lehetővé téve a felhasználók számára, hogy alkalmazásokat vegyenek fel az [alkalmazások](../user-help/my-apps-portal-end-user-access.md) **hozzáadása** funkció használatával, de csak a **kiválasztott üzleti jóváhagyók előzetes jóváhagyásával**
*  A rendszergazda lehetővé teszi az [önkiszolgáló csoportok felügyeletét](../enterprise-users/groups-self-service-management.md) , hogy a felhasználók az **üzleti jóváhagyás nélkül** legyenek hozzárendelve egy olyan csoporthoz, amelyhez az alkalmazás hozzá van rendelve.
*  A rendszergazda lehetővé teszi az [önkiszolgáló csoportok felügyeletét](../enterprise-users/groups-self-service-management.md) , hogy egy felhasználó olyan csoporthoz csatlakozzon, amelyhez az alkalmazás hozzá van rendelve, de csak a **kiválasztott üzleti jóváhagyók előzetes jóváhagyásával**
*  A rendszergazda közvetlenül az első féltől származó alkalmazáshoz rendel licencet a felhasználóhoz, például [Microsoft 365](https://products.office.com/)
*  A rendszergazdák egy licencet rendelnek egy olyan csoporthoz, amelyhez a felhasználó egy első féltől származó alkalmazás tagja, például [Microsoft 365](https://products.office.com/)
*  Egy [rendszergazda beleegyezett egy alkalmazásba](../develop/howto-convert-app-to-be-multi-tenant.md) , hogy az összes felhasználó használja, majd a felhasználó bejelentkezik az alkalmazásba
* A felhasználó az alkalmazásba való bejelentkezéssel saját maga [is beleegyezik az alkalmazásba](../develop/howto-convert-app-to-be-multi-tenant.md)

## <a name="next-steps"></a>Következő lépések
* [Gyorsindítás sorozat az alkalmazás-kezelésben](view-applications-portal.md)
* [Mi az alkalmazáskezelés?](what-is-application-management.md)
* [Mi az egyszeri bejelentkezés?](what-is-single-sign-on.md)