---
title: Licenc-csomagok módosítása felhasználók és csoportok számára – Azure AD | Microsoft Docs
description: Felhasználók áttelepítésének módja egy csoporton belül különböző szolgáltatási csomagokra a csoportos licencelés használatával Azure Active Directory
services: active-directory
keywords: Az Azure AD licencelése
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: enterprise-users
ms.topic: how-to
ms.workload: identity
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 050ae95c79e7ecb98f8508c2fdb41b90fc1b1da0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96546539"
---
# <a name="change-license-assignments-for-a-user-or-group-in-azure-active-directory"></a>Felhasználóra vagy csoportra vonatkozó licenc-hozzárendelések módosítása Azure Active Directory

Ez a cikk azt ismerteti, hogyan helyezhet át felhasználókat és csoportokat a szolgáltatási licencek között a Azure Active Directory (Azure AD) szolgáltatásban. Az Azure AD célja annak biztosítása, hogy a licenc változása során ne legyen elvesztése a szolgáltatás vagy az adat. A felhasználóknak zökkenőmentesen kell váltaniuk a szolgáltatások között. A cikkben ismertetett licencfeltételek a felhasználó vagy csoport módosítását ismertetik az Office 365 E1 és az Office 365 E3 között, de a lépések az összes licenccel érvényesek. Amikor egy felhasználóra vagy csoportra frissíti a licenc-hozzárendeléseket, a licenc-hozzárendelések eltávolítása és az új hozzárendelések egyidejűleg történnek, így a felhasználók nem veszítik el a szolgáltatásokhoz való hozzáférést a licencek módosítása során, vagy megtekinthetik a csomagok közötti licencfeltételeket.

## <a name="before-you-begin"></a>Előkészületek

A licenc-hozzárendelések frissítése előtt fontos ellenőrizni, hogy egyes feltételezések teljesülnek-e az összes frissítendő felhasználóhoz vagy csoporthoz. Ha a feltételezések nem teljesülnek a csoport összes felhasználója esetében, előfordulhat, hogy a Migrálás sikertelen lesz. Ennek eredményeképpen előfordulhat, hogy egyes felhasználók elveszítik a hozzáférést a szolgáltatásokhoz vagy az adatokhoz. Győződjön meg a következőket:

- A felhasználók rendelkeznek az aktuális licenccel (ebben az esetben az Office 365 e1), amelyet egy csoporthoz rendeltek, és amelyeket a felhasználó örököl, és nem közvetlenül van hozzárendelve.

- Elegendő licenccel rendelkezik a hozzárendelt licencelési csomaghoz. Ha nem rendelkezik elegendő licenccel, előfordulhat, hogy egyes felhasználók nem kapják meg az új licencszerződést. Megtekintheti a rendelkezésre álló licencek számát.

- A felhasználók nem rendelkeznek más hozzárendelt szolgáltatási licenccel, amelyek ütközhetnek a kívánt licenccel, vagy megakadályozzák az aktuális licenc eltávolítását. Például egy olyan szolgáltatástól származó licenc, mint például a munkahelyi Analitika vagy a Project online, amely más szolgáltatásokkal való függőséggel rendelkezik.

- Ha a helyszíni csoportokat felügyeli, és szinkronizálja őket az Azure AD-be a Azure AD Connect-on keresztül, akkor a helyszíni rendszer használatával hozzáadhatja vagy eltávolíthatja a felhasználókat. Eltarthat egy ideig, amíg a módosítások az Azure AD-vel való szinkronizálása csoportos licenceléssel is elvégezhető.

- Ha Azure AD-beli dinamikus csoporttagság-tagságot használ, a felhasználókat az attribútumaik módosításával adhatja hozzá vagy távolíthatja el, de a licenc-hozzárendelések frissítési folyamata változatlan marad.

## <a name="change-user-license-assignments"></a>Felhasználói licenc-hozzárendelések módosítása

Ha úgy látja, hogy egyes jelölőnégyzetek nem érhetők el, a **licenc-hozzárendelések frissítése** oldalon láthatja azokat a szolgáltatásokat, amelyek nem módosíthatók, mert örökölnek egy csoporttól származó licenccel.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com/) az Azure ad-szervezetben található licenc-rendszergazdai fiók használatával.
1. Válassza ki **Azure Active Directory**  >  **felhasználók** elemet, majd nyissa meg egy felhasználó **profil** lapját.
1. Válassza a **licencek** lehetőséget.
1. Válassza a **hozzárendelések** lehetőséget a felhasználó vagy csoport licenc-hozzárendelésének szerkesztéséhez. A **hozzárendelések** lap a licenc-hozzárendelési ütközések feloldására szolgál.
1. Jelölje be az Office 365 E3 jelölőnégyzetét, és győződjön meg arról, hogy legalább a felhasználóhoz rendelt összes E1 szolgáltatás ki van választva.
1. Törölje az Office 365 E1 jelölőnégyzet jelölését.

    ![a licenc-hozzárendelések lapja, amely az Office 365 E1 és az Office 365 E3 jelölésű felhasználóra mutat](./media/licensing-groups-change-licenses/update-user-license-assignments.png)

1. Kattintson a **Mentés** gombra.

Az Azure AD az új licenceket alkalmazza, és egyszerre eltávolítja a régi licenceket a szolgáltatás folytonosságának biztosításához.

## <a name="change-group-license-assignments"></a>Csoport licenc-hozzárendeléseinek módosítása

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com/) az Azure ad-szervezetben található licenc-rendszergazdai fiók használatával.
1. Válassza ki **Azure Active Directory**  >  **csoportokat**, majd nyissa meg egy csoport **Áttekintés** lapját.
1. Válassza a **licencek** lehetőséget.
1. Kattintson a **hozzárendelések** parancsra a felhasználó vagy csoport licenc-hozzárendelésének szerkesztéséhez.
1. Jelölje be az Office 365 E3 jelölőnégyzetét. A szolgáltatás folytonosságának fenntartása érdekében válassza ki az összes olyan E1-szolgáltatást, amely már hozzá van rendelve a felhasználóhoz.
1. Törölje az Office 365 E1 jelölőnégyzet jelölését.

    ![Válassza a hozzárendelések parancsot egy felhasználói vagy csoportos licencek oldalon.](./media/licensing-groups-change-licenses/update-group-license-assignments.png)

1. Kattintson a **Mentés** gombra.

A szolgáltatás folytonosságának biztosítása érdekében az Azure AD alkalmazza az új licenceket, és a csoport összes felhasználójára egyidejűleg eltávolítja a régi licenceket.

## <a name="next-steps"></a>Következő lépések

További tudnivalók a licencek kezelésével kapcsolatos egyéb forgatókönyvekről a csoportok segítségével a következő cikkekben:

- [Licencek hozzárendelése egy csoporthoz az Azure Active Directoryban](licensing-groups-assign.md)
- [A csoportok licencproblémáinak azonosítása és megoldása az Azure Active Directoryban](licensing-groups-resolve-problems.md)
- [Egyéni licenccel rendelkező felhasználók áttelepítésének engedélyezése a Azure Active Directory](licensing-groups-migrate-users.md)
- [További forgatókönyvek Azure Active Directory csoport licencelése](licensing-group-advanced.md)
- [PowerShell-példák a csoportos licencelésre Azure Active Directory](licensing-ps-examples.md)
