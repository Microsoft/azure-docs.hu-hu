---
title: Hozzon létre & törölheti Azure AD B2C fogyasztói felhasználói fiókjait a Azure Portal
description: Ebből a témakörből megtudhatja, hogyan hozhat létre és törölhet felhasználói felhasználókat a Azure AD B2C könyvtárban a Azure Portal használatával.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/03/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ace0ccb8372ff21a2d3e8721baf09bab539846c2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102033664"
---
# <a name="use-the-azure-portal-to-create-and-delete-consumer-users-in-azure-ad-b2c"></a>A Azure Portal használatával hozhat létre és törölhet felhasználói felhasználókat a Azure AD B2C

Lehetnek olyan helyzetek, amikor manuálisan szeretné létrehozni a fogyasztói fiókokat a Azure Active Directory B2C (Azure AD B2C) címtárban. Habár a Azure AD B2C könyvtárban lévő fogyasztói fiókok leggyakrabban akkor jönnek létre, amikor a felhasználók az egyik alkalmazás használatára jelentkeznek, programozott módon és a Azure Portal használatával is létrehozhatók. Ez a cikk a felhasználók létrehozásának és törlésének Azure Portal módszerét tárgyalja.

Felhasználók hozzáadásához vagy törléséhez a fióknak hozzá kell rendelnie a *felhasználói rendszergazda* vagy a *globális rendszergazdai* szerepkört.

## <a name="types-of-user-accounts"></a>Felhasználói fiókok típusai

A [Azure ad B2C felhasználói fiókjainak áttekintése](user-overview.md)című cikkben leírtak szerint három típusú felhasználói fiók hozható létre egy Azure ad B2C könyvtárban:

* Feladatok
* Vendég
* Ügyfélszolgáltatások

Ez a cikk a Azure Portal **felhasználói fiókjainak** használatáról szól. A munkahelyi és a vendég fiókok létrehozásával és törlésével kapcsolatos információkért lásd: [felhasználók hozzáadása vagy törlése Azure Active Directory használatával](../active-directory/fundamentals/add-users-azure-active-directory.md).

## <a name="create-a-consumer-user"></a>Fogyasztói felhasználó létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Válassza ki a **címtár + előfizetés** szűrőt a felső menüben, majd válassza ki azt a könyvtárat, amely a Azure ad B2C bérlőjét tartalmazza.
1. A bal oldali menüben válassza a **Azure ad B2C** lehetőséget. Vagy válassza a **minden szolgáltatás** lehetőséget, és keresse meg, majd válassza a **Azure ad B2C** lehetőséget.
1. A **Kezelés** menüpontban válassza a **Felhasználók** lehetőséget.
1. Válassza az **új felhasználó** lehetőséget.
1. Válassza a **létrehozás Azure ad B2C a felhasználó** lehetőséget.
1. Válasszon **bejelentkezési módszert** , és adjon meg egy **e-mail-** címet vagy egy **felhasználónevet** az új felhasználó számára. Az itt kiválasztott bejelentkezési módszernek meg kell egyeznie az Azure ad B2C bérlő *helyi fiókjának* identitás-szolgáltatójának megadott beállítással (lásd : az  >  **identitás-szolgáltatók** kezelése a Azure ad B2C-bérlőben).
1. Adja meg a felhasználó **nevét** . Ez általában a felhasználó teljes neve (adott és vezetékneve).
1. Választható Ha szeretné, hogy a felhasználó ne jelentkezzen be, tiltsa le a **bejelentkezést** . A bejelentkezést később is engedélyezheti, ha szerkeszti a felhasználó **profilját** a Azure Portal.
1. Válassza az **autogenerált jelszó** lehetőséget, vagy **hozzon létre jelszót**.
1. Adja **meg a** felhasználó vezetéknevét és **vezetéknevét**.
1. Válassza a **Létrehozás** lehetőséget.

Ha nem jelölte be a **Bejelentkezés blokkolását**, a felhasználó bejelentkezhet a megadott bejelentkezési módszerrel (e-mail vagy Felhasználónév).

## <a name="reset-a-users-password"></a>Új felhasználói jelszó létrehozása

Rendszergazdaként alaphelyzetbe állíthatja a felhasználó jelszavát, ha a felhasználó elfelejti a jelszavát. A felhasználó jelszavának alaphelyzetbe állításakor a rendszer automatikusan létrehoz egy ideiglenes jelszót a felhasználó számára. Az ideiglenes jelszó soha nem jár le. Amikor a felhasználó legközelebb bejelentkezik, a jelszó továbbra is működni fog, függetlenül attól, hogy mennyi idő telt el az ideiglenes jelszó létrehozása óta. Ezután a felhasználónak egy állandó jelszót kell visszaállítania. 

> [!IMPORTANT]
> A felhasználó jelszavának alaphelyzetbe állítása előtt [állítson be egy kényszerített jelszó-visszaállítási folyamatot Azure Active Directory B2Cban](force-password-reset.md), ellenkező esetben a felhasználó nem fog tudni bejelentkezni.

A felhasználó jelszavának alaphelyzetbe állítása:

1. A Azure AD B2C könyvtárban válassza a **felhasználók** lehetőséget, majd válassza ki azt a felhasználót, akinek alaphelyzetbe kívánja állítani a jelszót.
1. Keresse meg és válassza ki azt a felhasználót, akinek az alaphelyzetbe állítása szükséges, majd válassza a **jelszó visszaállítása** lehetőséget.

    Megjelenik az **Alain Charon-profil** lap a **jelszó alaphelyzetbe állítása** beállítással.

    ![Felhasználói profil lapja, a jelszó alaphelyzetbe állítása lehetőség kiemelve](media/manage-users-portal/user-profile-reset-password-link.png)

1. A **jelszó alaphelyzetbe** állítása lapon válassza a **jelszó alaphelyzetbe állítása** lehetőséget.
1. Másolja a jelszót, és adja meg a felhasználónak. A következő bejelentkezési folyamat során a felhasználónak meg kell változtatnia a jelszót.


## <a name="delete-a-consumer-user"></a>Fogyasztói felhasználó törlése

1. A Azure AD B2C könyvtárban válassza a **felhasználók** lehetőséget, majd válassza ki a törölni kívánt felhasználót.
1. Válassza a **Törlés** lehetőséget, majd az **Igen** gombot a törlés megerősítéséhez.

A felhasználók a törlést követő 30 napon belüli visszaállításával, illetve a felhasználók végleges törlésével kapcsolatos részletekért lásd: [a közelmúltban törölt felhasználó visszaállítása vagy eltávolítása a Azure Active Directory használatával](../active-directory/fundamentals/active-directory-users-restore.md).

## <a name="next-steps"></a>Következő lépések

Az automatikus felhasználói felügyeleti forgatókönyvek esetében például a felhasználók áttelepítése egy másik identitás-szolgáltatótól a Azure AD B2C könyvtárba [: Azure ad B2C: felhasználói áttelepítés](user-migration.md).
