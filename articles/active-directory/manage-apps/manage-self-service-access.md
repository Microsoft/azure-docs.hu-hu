---
title: Önkiszolgáló alkalmazás-hozzárendelés konfigurálása | Microsoft Docs
description: Az önkiszolgáló alkalmazás-hozzáférés engedélyezése lehetővé teszi, hogy a felhasználók megtalálják a saját alkalmazásaikat
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 04/20/2020
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9742a21cf00733607237c0eaf548f96b434abb33
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99260214"
---
# <a name="how-to-configure-self-service-application-assignment"></a>Önkiszolgáló alkalmazás-hozzárendelés konfigurálása

Ahhoz, hogy a felhasználók maguk is felfedezzék alkalmazásaikat a saját alkalmazásaikban, engedélyeznie kell az **önkiszolgáló alkalmazás-hozzáférést** minden olyan alkalmazáshoz, amely számára engedélyezni szeretné a felhasználók számára a saját felderítését és a hozzáférés kérését. Ez a funkció olyan alkalmazásokhoz érhető el, amelyeket az Azure [ad](./add-application-portal.md)-katalógusból, az [Azure ad Application proxy](./application-proxy.md) -ból vagy [felhasználói vagy rendszergazdai hozzájárulással](../develop/application-consent-experience.md)adott hozzá. 

Ez a funkció nagyszerű lehetőséget nyújt arra, hogy időt és pénzt takarítson meg informatikai csoportként, és a Azure Active Directory használatával korszerű alkalmazások központi telepítésének részeként erősen ajánlott.

Ez a szolgáltatás lehetővé teszi:

-   Lehetővé teszi a felhasználók számára, hogy az IT-csoport kellemetlenség nélkül is felfedezzék az alkalmazásokat a [saját alkalmazásoktól](https://myapps.microsoft.com/) .

-   Adja hozzá ezeket a felhasználókat egy előre konfigurált csoporthoz, így megtekintheti, hogy ki igényelt hozzáférést, távolítsa el a hozzáférést, és kezelje a hozzájuk rendelt szerepköröket.

-   Opcionálisan engedélyezheti az üzleti jóváhagyónak az alkalmazás-hozzáférési kérelmek jóváhagyását, hogy az informatikai csoportnak ne kelljen.

-   Opcionálisan akár 10 személyt is beállíthat, akik jóváhagyják az alkalmazáshoz való hozzáférést.

-   Opcionálisan engedélyezheti az üzleti jóváhagyó számára, hogy beállítsa azokat a jelszavakat, amelyeket a felhasználók használhatnak az alkalmazásba való bejelentkezéshez közvetlenül az üzleti jóváhagyó [saját alkalmazásaiban](https://myapps.microsoft.com/).

-   Opcionálisan automatikusan hozzárendelheti az önkiszolgáló hozzárendelt felhasználókat egy alkalmazás-szerepkörhöz.

> [!NOTE]
> Egy prémium szintű Azure Active Directory (P1 vagy P2) licenc szükséges ahhoz, hogy a felhasználók egy önkiszolgáló alkalmazáshoz csatlakozzanak, és a tulajdonosok jóváhagyják vagy megtagadják a kérelmeket. Prémium szintű Azure Active Directory licenc nélkül a felhasználók nem adhatnak hozzá önkiszolgáló alkalmazásokat.

## <a name="enable-self-service-application-access-to-allow-users-to-find-their-own-applications"></a>Az önkiszolgáló alkalmazás-hozzáférés engedélyezése lehetővé teszi, hogy a felhasználók megtalálják a saját alkalmazásaikat

Az önkiszolgáló alkalmazás-hozzáférés lehetővé teszi a felhasználók számára az alkalmazások önálló felderítését, és opcionálisan lehetővé teszi, hogy az üzleti csoport jóváhagyja az alkalmazásokhoz való hozzáférést. A jelszó-egyszeri bejelentkezés alkalmazásokhoz engedélyezheti az üzleti csoport számára, hogy az adott felhasználókhoz rendelt hitelesítő adatokat a saját alkalmazások hozzáférési panelén is kezelni tudja.

Az önkiszolgáló alkalmazások alkalmazáshoz való hozzáférésének engedélyezéséhez kövesse az alábbi lépéseket:

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) globális rendszergazdaként.

2. Válassza a **Azure Active Directory** lehetőséget. A bal oldali navigációs menüben válassza a **vállalati alkalmazások** lehetőséget.

3. Válassza ki az alkalmazást a listából. Ha nem látja az alkalmazást, kezdje el beírni a nevét a keresőmezőbe. Vagy használja a szűrő vezérlőelemeket az alkalmazás típusának, állapotának vagy láthatóságának kiválasztásához, majd válassza az **alkalmaz** lehetőséget.

4. A bal oldali navigációs menüben válassza az **önkiszolgáló** lehetőséget.

5. Az alkalmazás önkiszolgáló alkalmazásokhoz való hozzáférésének engedélyezéséhez kapcsolja be a **felhasználók számára az alkalmazáshoz való hozzáférés kérését?** váltson át az Igen értékre **.**

6. Válassza ki azt **a csoportot, amelyhez hozzá kívánja adni a felhasználókat?** lapon kattintson a **csoport kiválasztása** elemre. Válasszon ki egy csoportot, majd kattintson a **kiválasztás** elemre. Ha a felhasználó kérelmét jóváhagyják, a rendszer hozzáadja a csoporthoz. A csoport tagságának megtekintésekor láthatja, hogy ki kapta meg az alkalmazáshoz való hozzáférést az önkiszolgáló hozzáférésen keresztül.
  
    > [!NOTE]
    > Ez a beállítás nem támogatja a helyszíni környezetből szinkronizált csoportok használatát.

7. Nem **kötelező:** Ha szeretné megkövetelni, hogy a felhasználók hozzáférhessenek az üzleti jóváhagyáshoz, állítsa be a **jóváhagyás megkövetelése, mielőtt hozzáférést adna ehhez az alkalmazáshoz?** váltson az **Igen** értékre.

8. Nem **kötelező: csak jelszó egyszeri bejelentkezést használó alkalmazások esetében** , hogy az üzleti jóváhagyók megadják az alkalmazásnak a jóváhagyott felhasználók számára eljuttatott jelszavakat, a **jóváhagyók beállításával állíthatja be a felhasználók jelszavait ehhez az alkalmazáshoz?** váltás az **Igen** értékre.

9. Nem **kötelező:** Azon üzleti jóváhagyók megadása, akik számára engedélyezett az alkalmazáshoz való hozzáférés jóváhagyása, annak mellett, hogy **ki jogosult az alkalmazáshoz való hozzáférés jóváhagyására?** kattintson a **jóváhagyók kiválasztása** lehetőségre, majd válassza ki a legfeljebb 10 egyéni üzleti jóváhagyót. Ezután kattintson a **Kiválasztás** elemre.

    >[!NOTE]
    >A csoportok nem támogatottak. Legfeljebb 10 egyéni üzleti jóváhagyót választhat ki. Ha több jóváhagyót is megad, akkor egyetlen jóváhagyó is jóváhagyhat egy hozzáférési kérelmet.

10. Nem **kötelező:** **olyan alkalmazások esetében, amelyek szerepköröket tesznek elérhetővé** az önkiszolgáló jóváhagyott felhasználók szerepkörhöz való hozzárendeléséhez, a **-hoz melyik szerepkört kell hozzárendelni a felhasználókhoz az alkalmazásban?** lapon kattintson a **szerepkör kiválasztása** elemre, majd válassza ki azt a szerepkört, amelyhez ezeket a felhasználókat hozzá szeretné rendelni. Ezután kattintson a **Kiválasztás** elemre.

11. A befejezéshez kattintson a **Mentés** gombra a panel tetején.

Az önkiszolgáló alkalmazás-konfiguráció befejezése után a felhasználók megkereshetik a saját [alkalmazásaikat](https://myapps.microsoft.com/) , és az önkiszolgáló **Alkalmazások hozzáadása** gombra kattintva megtalálhatják azokat az alkalmazásokat, amelyek engedélyezik az önkiszolgáló hozzáférést. Az üzleti jóváhagyók a saját [alkalmazásaikban](https://myapps.microsoft.com/)is láthatnak értesítést. Engedélyezheti az e-mailek értesítését, ha egy felhasználó hozzáférést kért egy olyan alkalmazáshoz, amelyhez jóváhagyás szükséges.

## <a name="next-steps"></a>Következő lépések
[Az Azure Active Directory beállítása önkiszolgáló csoportkezelésre](../enterprise-users/groups-self-service-management.md)