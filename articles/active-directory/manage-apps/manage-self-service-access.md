---
title: Önkiszolgáló alkalmazás-hozzárendelés konfigurálása | Microsoft Docs
description: Önkiszolgáló alkalmazás-hozzáférés engedélyezése, hogy a felhasználók megtalálják a saját alkalmazásukat
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 04/20/2020
ms.author: iangithinji
ms.collection: M365-identity-device-management
ms.openlocfilehash: 29c3043cc38c8ab3d3387b171ea6f3793d485730
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107373961"
---
# <a name="how-to-configure-self-service-application-assignment"></a>Önkiszolgáló alkalmazás-hozzárendelés konfigurálása

Ahhoz, hogy a felhasználók saját alkalmazásokat derítsen  fel a Saját alkalmazások, engedélyeznie kell az önkiszolgáló alkalmazás-hozzáférést minden olyan alkalmazáshoz, amely számára engedélyezni szeretné, hogy a felhasználók önálló felderítést és hozzáférést kérnek. Ez a funkció olyan alkalmazásokhoz érhető el, amelyek az Azure AD katalógusából, az [Azure AD-alkalmazásproxy](./application-proxy.md) vagy felhasználói vagy rendszergazdai jóváhagyással [voltak hozzáadva.](../develop/application-consent-experience.md) [](./add-application-portal.md) 

Ez a funkció remek módja az it-csoportként időt és pénzt takarít meg, és kifejezetten ajánlott a modern alkalmazástelepítés részeként a Azure Active Directory.

Ez a szolgáltatás lehetővé teszi:

-   Lehetővé teszi a felhasználók számára, hogy az Saját alkalmazások [az](https://myapps.microsoft.com/) it-csoport zavarása nélkül derítsék fel az alkalmazásokat.

-   Adja hozzá ezeket a felhasználókat egy előre konfigurált csoporthoz, így láthatja, hogy ki kért hozzáférést, hogyan távolíthatja el a hozzáférést, és hogyan kezelheti a hozzájuk rendelt szerepköröket.

-   Azt is engedélyezheti, hogy egy üzleti jóváhagyó jóváhagyja az alkalmazás-hozzáférési kérelmeket, hogy az it-csoportnak ne legyen rá szükség.

-   Ha szükséges, akár 10 olyan személyt is konfigurálhat, akik jóváhagyhatják az alkalmazáshoz való hozzáférést.

-   Azt is engedélyezheti, hogy egy üzleti jóváhagyó beállítsa azokat a jelszavakat, amelyek használatával a felhasználók bejelentkeznek az alkalmazásba, közvetlenül a vállalati jóváhagyó [Saját alkalmazások.](https://myapps.microsoft.com/)

-   Igény szerint automatikusan hozzárendelheti az önkiszolgálóan hozzárendelt felhasználókat egy alkalmazás-szerepkörhöz közvetlenül.

> [!NOTE]
> A prémium szintű Azure Active Directory (P1 vagy P2) licenc szükséges ahhoz, hogy a felhasználók önkiszolgáló alkalmazáshoz csatlakozzanak, a tulajdonosok pedig jóváhagyhatják vagy elutasíthatják a kérelmeket. Licenc nélkül prémium szintű Azure Active Directory felhasználók nem adhatnak hozzá önkiszolgáló alkalmazásokat.

## <a name="enable-self-service-application-access-to-allow-users-to-find-their-own-applications"></a>Önkiszolgáló alkalmazás-hozzáférés engedélyezése, hogy a felhasználók megtalálják a saját alkalmazásukat

Az önkiszolgáló alkalmazás-hozzáféréssel a felhasználók saját maga is felfedezhetik az alkalmazásokat, és engedélyezhetik, hogy az üzleti csoport jóváhagyja az alkalmazásokhoz való hozzáférést. A jelszavas egyszeri bejelentkezéses alkalmazások esetében azt is engedélyezheti, hogy az üzleti csoport kezelje a felhasználókhoz rendelt hitelesítő adatokat a saját Saját alkalmazások paneljükről.

Ha engedélyezni szeretné az önkiszolgáló alkalmazás-hozzáférést egy alkalmazáshoz, kövesse az alábbi lépéseket:

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) globális rendszergazdaként.

2. Válassza a **Azure Active Directory** lehetőséget. A bal oldali navigációs menüben válassza a Vállalati **alkalmazások lehetőséget.**

3. Válassza ki az alkalmazást a listából. Ha nem látja az alkalmazást, kezdje el beírni a nevét a keresőmezőbe. Vagy a szűrővezérlőkkel kiválaszthatja az alkalmazás típusát, állapotát vagy láthatóságát, majd válassza az **Alkalmaz lehetőséget.**

4. A bal oldali navigációs menüben válassza az **Önkiszolgáló lehetőséget.**

5. Ha engedélyezni szeretné az önkiszolgáló alkalmazás-hozzáférést ehhez az alkalmazáshoz, kapcsolja be  az Igen lehetőséget. 

6. A **Mely csoporthoz kell hozzáadni a felhasználókat?** mellett kattintson a **Csoport kiválasztása elemre.** Válasszon ki egy csoportot, majd kattintson a **Kijelölés gombra.** A felhasználó kérelmének jóváhagyása után a rendszer hozzáadja azt a csoporthoz. A csoport tagságának megtekintésekor láthatja, hogy ki kapott hozzáférést az alkalmazáshoz önkiszolgáló hozzáféréssel.
  
    > [!NOTE]
    > Ez a beállítás nem támogatja a helyszínről szinkronizált csoportokat.

7. **Nem kötelező:** Ha üzleti jóváhagyásra van szüksége ahhoz, hogy a felhasználók hozzáférnek, állítsa a Jóváhagyás megkövetelése az alkalmazáshoz való hozzáférés megadása **előtt?** beállításnál az **Igen kapcsolót.**

8. **Nem kötelező:** Ha csak jelszavas egyszeri bejelentkezést használó alkalmazásokhoz engedélyezi a vállalati jóváhagyók számára az alkalmazásnak a jóváhagyott felhasználók számára küldött jelszavak megadását, állítsa a Jóváhagyók számára a felhasználói jelszavak beállításának engedélyezése ehhez az **alkalmazáshoz?** kapcsolót Igen beállításra.

9. **Nem kötelező:** Azon üzleti jóváhagyók megadásához, akik számára engedélyezett az alkalmazáshoz való hozzáférés jóváhagyása, a Who is allowed to approve access to this **application?**(Ki jóváhagyhatja az alkalmazáshoz való hozzáférést? ) mellett kattintson a **Select approvers**(Jóváhagyók kiválasztása) elemre, majd válasszon ki legfeljebb 10 egyéni üzleti jóváhagyót. Ezután kattintson a **Kiválasztás** elemre.

    >[!NOTE]
    >A csoportok nem támogatottak. Legfeljebb 10 egyéni üzleti jóváhagyó közül választhat. Ha több jóváhagyó van megadásával, bármelyik jóváhagyó jóváhagyhat egy hozzáférési kérelmet.

10. **Nem kötelező:** A szerepköröket elérhetővé t használó alkalmazások esetében az önkiszolgáló jóváhagyott felhasználók szerepkörhöz való hozzárendeléséhez a Mely szerepkörhöz kell felhasználókat hozzárendelni ebben az **alkalmazásban?** mellett kattintson a **Szerepkör** kiválasztása elemre, majd válassza ki azt a szerepkört, amelyhez a felhasználók hozzá lesznek rendelve. Ezután kattintson a **Kiválasztás** elemre.

11. A **befejezéshez** kattintson a panel tetején található Mentés gombra.

Az önkiszolgáló alkalmazáskonfiguráció befejezése után [](https://myapps.microsoft.com/) a felhasználók a saját alkalmazásukhoz Saját alkalmazások az **Önkiszolgáló** alkalmazások hozzáadása gombra kattintva megkeresheti az önkiszolgáló hozzáféréssel elérhető alkalmazásokat. Az üzleti jóváhagyók egy értesítést is látnak a [Saját alkalmazások.](https://myapps.microsoft.com/) Engedélyezhet egy e-mailt, amely értesíti őket, ha egy felhasználó hozzáférést kért egy olyan alkalmazáshoz, amelyhez jóváhagyásra van szükség.

## <a name="next-steps"></a>Következő lépések
[Az Azure Active Directory beállítása önkiszolgáló csoportkezelésre](../enterprise-users/groups-self-service-management.md)