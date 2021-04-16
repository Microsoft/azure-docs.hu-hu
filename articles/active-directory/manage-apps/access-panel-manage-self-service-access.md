---
title: Az önkiszolgáló alkalmazás-hozzáférés használata az Azure AD-ban
description: Önkiszolgáló használat engedélyezése, hogy a felhasználók alkalmazásokat találjanak az Azure AD-ban
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 07/11/2017
ms.author: iangithinji
ms.reviewer: japere,asteen
ms.openlocfilehash: 8e3851a702da46d07634a4141c774275845fa44d
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377615"
---
# <a name="how-to-use-self-service-application-access"></a>Az önkiszolgáló alkalmazás-hozzáférés használata

Ahhoz, hogy a felhasználók a saját Saját alkalmazások-oldalukról saját  maga derítsen fel alkalmazásokat, engedélyeznie kell az önkiszolgáló alkalmazás-hozzáférést minden olyan alkalmazáshoz, amely számára engedélyezni szeretné a felhasználók számára az önálló felderítést és a hozzáférés kérését.

Ezzel a funkcióval időt és pénzt takaríthat meg it-csoportként, és kifejezetten ajánlott a modern alkalmazástelepítés részeként, Azure Active Directory.

Ha többet szeretne megtudni a Saját alkalmazások végfelhasználói szempontból való használatával kapcsolatban, tekintse meg a Saját alkalmazások [portál súgóját.](../user-help/my-apps-portal-end-user-access.md)

Ez a szolgáltatás lehetővé teszi:

-   Lehetővé teszi a felhasználók [](https://myapps.microsoft.com/) számára, hogy az Saját alkalmazások az it-csoport zavarása nélkül derítsék fel az alkalmazásokat.
-   Adja hozzá ezeket a felhasználókat egy előre konfigurált csoporthoz, így láthatja, hogy ki kért hozzáférést, hogyan távolíthatja el a hozzáférést, és hogyan kezelheti a hozzájuk rendelt szerepköröket.
-   Azt is engedélyezheti, hogy valaki jóváhagyja az alkalmazás-hozzáférési kérelmeket, hogy az it-csoportnak ne legyen rá szükség.
-   Ha szükséges, akár 10 személyt is konfigurálhat, akik jóváhagyhatják az alkalmazáshoz való hozzáférést.
-   Azt is engedélyezheti, hogy valaki beállítsa azokat a jelszavakat, amelyek használatával a felhasználók bejelentkeznek az alkalmazásba.
-   Igény szerint automatikusan hozzárendelheti az önkiszolgálóan hozzárendelt felhasználókat egy alkalmazás-szerepkörhöz közvetlenül.

## <a name="enable-self-service-application-access-to-allow-users-to-find-their-own-applications"></a>Önkiszolgáló alkalmazás-hozzáférés engedélyezése, hogy a felhasználók megtalálják a saját alkalmazásukat

Az önkiszolgáló alkalmazás-hozzáféréssel a felhasználók önfelfedő alkalmazásokat fedezhet fel, és engedélyezhetik az üzleti csoport számára az alkalmazásokhoz való hozzáférés jóváhagyását. Engedélyezheti az üzleti csoport számára, hogy közvetlenül a saját oldalukról kezelje az adott felhasználókhoz hozzárendelt hitelesítő Single-Sign az Alkalmazások Saját alkalmazások számára.

Ha engedélyezni szeretné az önkiszolgáló alkalmazás-hozzáférést egy alkalmazáshoz, kövesse az alábbi lépéseket:
1. Nyissa meg [**Azure Portal,**](https://portal.azure.com/) és jelentkezzen be globális **rendszergazdaként.**
2. Nyissa meg **Azure Active Directory bővítményt**  a bal oldali fő navigációs menü tetején található Minden szolgáltatás lehetőség kiválasztásával.
3. Írja be **a "Azure Active Directory"** elemet a szűrő keresőmezőbe, és válassza ki **Azure Active Directory** elemet.
4. Az Azure Active Directory bal oldali navigációs menüjében válassza a **Vállalati alkalmazások** elemet.
5. Válassza a **Minden alkalmazás** lehetőséget az összes alkalmazás listájának megtekintéséhez.
   * Ha itt nem látja a kívánt alkalmazást,  használja a Minden alkalmazás lista tetején található  Szűrő vezérlőt, és állítsa a Show (Megjelenítése) beállítást a Minden **alkalmazás lehetőségre.** 
6. Válassza ki azt az alkalmazást, amely számára engedélyezni szeretné az önkiszolgáló hozzáférést a listából.
7. Az alkalmazás betöltése után válassza az **Önkiszolgáló** lehetőséget az alkalmazás bal oldali navigációs menüjében.
8. Ha engedélyezni szeretné az önkiszolgáló alkalmazás-hozzáférést ehhez az alkalmazáshoz, kapcsolja be  az Igen lehetőséget. 
9. Ezután válassza ki azt a csoportot, amelyhez az alkalmazáshoz hozzáférést kérelmező felhasználók hozzá lesznek adva, válassza ki a Címke melletti választót, amelyhez hozzá kell rendelni a **felhasználókat,** majd válasszon egy csoportot.
10. **Nem kötelező:** Ha üzleti jóváhagyást szeretne igényelni, mielőtt a felhasználók hozzáférhetnek, állítsa a Jóváhagyás **megkövetelése az** alkalmazáshoz való hozzáférés megadása előtt? beállításnál az **Igen kapcsolót.**
11. **Választható:** Ha csak jelszavas egyszeri bejelentkezést használó alkalmazások esetén engedélyezni szeretné, hogy az üzleti jóváhagyók megszabadják az alkalmazásnak a jóváhagyott felhasználók számára küldött jelszavakat, állítsa a Jóváhagyók beállítását az alkalmazás felhasználói jelszavának **beállításához?** kapcsolót Igen beállításra.
12. **Nem kötelező:** Adja meg azokat az üzleti jóváhagyókat, akik jóváhagyhatják az alkalmazáshoz való hozzáférést. Válassza **ki, hogy ki jóváhagyhatja az alkalmazáshoz való hozzáférést?** Ezután legfeljebb 10 egyéni üzleti jóváhagyó közül választhat.
    * A csoportok nem támogatottak.
13. **Választható:** A szerepköröket elérhetővé t használó alkalmazások esetében, ha önkiszolgáló jóváhagyott felhasználókat szeretne hozzárendelni egy szerepkörhöz, válassza ki a Milyen szerepkörhöz kell felhasználókat hozzárendelni ebben az **alkalmazásban?** lehetőség kiválasztásával válassza ki azt a szerepkört, amelyhez a felhasználók hozzá lesznek rendelve.
14. A **befejezéshez** kattintson a felül található Mentés gombra.

Miután befejezte az önkiszolgáló alkalmazáskonfigurációt, a felhasználók a Saját alkalmazások és [a](https://myapps.microsoft.com/) **+Hozzáadás** gombra kattintva megkeresheti azokat az alkalmazásokat, amelyekhez engedélyezte az önkiszolgáló hozzáférést. Az üzleti jóváhagyók a saját [oldalukon](https://myapps.microsoft.com/) is Saját alkalmazások értesítést. E-mailben értesítheti őket, ha egy felhasználó hozzáférést kért egy olyan alkalmazáshoz, amelyhez jóváhagyásra van szükség. 

Ezek a jóváhagyások csak az egyszeri jóváhagyási munkafolyamatokat támogatják, ami azt jelenti, hogy ha több jóváhagyó van megadásával, bármely jóváhagyó jóváhagyhatja az alkalmazáshoz való hozzáférést.

## <a name="things-to-check-if-self-service-isnt-working"></a>Ellenőrizze, hogy az önkiszolgáló szolgáltatás nem működik-e
-   Győződjön meg arról, hogy a felhasználó vagy csoport számára engedélyezve van az önkiszolgáló alkalmazás-hozzáférés igénylése.
-   Győződjön meg arról, hogy a felhasználó a megfelelő helyen van az önkiszolgáló alkalmazás-hozzáféréshez. A felhasználók a saját Saját alkalmazások [a](https://myapps.microsoft.com/) **+Hozzáadás** gombra kattintva megkeresheti azokat az alkalmazásokat, amelyekhez önkiszolgáló hozzáférést engedélyezett.
-   Ha az önkiszolgáló alkalmazás-hozzáférés nemrég lett konfigurálva, néhány perc múlva próbáljon meg újra bejelentkezni a felhasználó Saját alkalmazások-be, hogy lássa, megjelentek-e az önkiszolgáló hozzáférés változásai.

## <a name="next-steps"></a>Következő lépések
[Az Azure Active Directory beállítása önkiszolgáló csoportkezelésre](../enterprise-users/groups-self-service-management.md)
