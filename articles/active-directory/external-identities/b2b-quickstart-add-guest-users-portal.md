---
title: 'Gyors útmutató: vendég felhasználók hozzáadása a Azure Portal-Azure AD-ben'
description: Ez a rövid útmutató bemutatja, hogyan vehetnek fel az Azure AD rendszergazdák B2B vendégfelhasználókat az Azure portálon és végigvezet a B2B-meghívási munkafolyamat lépésein.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: quickstart
ms.date: 08/05/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 12f134a11b6d0a561a8b53156c927906afe2874a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "87906088"
---
# <a name="quickstart-add-guest-users-to-your-directory-in-the-azure-portal"></a>Rövid útmutató: Vendégfelhasználók felvétele a címtárhoz az Azure portálon

A címtárba vendégfelhasználóként felvéve bárkit meghívhat a szervezetben végzett közös munkára. Ezután egy érvényesítési hivatkozást tartalmazó meghívó e-mailt küldhet, vagy küldhet közvetlen hivatkozást is a megosztani kívánt alkalmazáshoz. A vendégfelhasználók saját munkahelyi, iskolai vagy közösségi identitásukkal jelentkezhetnek be. Ezzel a rövid útmutatóval többet is megtudhat arról, hogyan adhat hozzá vendég felhasználókat [a Azure Portal, a](add-users-administrator.md) [PowerShell](b2b-quickstart-invite-powershell.md)segítségével vagy [tömegesen](tutorial-bulk-invite.md).

Ebben a rövid útmutatóban egy új vendég felhasználót ad hozzá az Azure AD-címtárhoz a Azure Portalon keresztül, meghívót küld, és megtekintheti, hogy a vendég felhasználó meghívójának beváltási folyamata hogyan néz ki.

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

 - Egy szerepkör, amely lehetővé teszi felhasználók létrehozását a bérlő címtárban, mint amilyen például a globális rendszergazdai szerepkör vagy bármely korlátozott rendszergazdai címtári szerepkör.
 - Érvényes e-mail-fiók, amit a bérlő címtárhoz adhat, és amelyben fogadni tudja a teszt meghívó e-mailt.

## <a name="add-a-new-guest-user-in-azure-ad"></a>Új vendégfelhasználó felvétele Azure AD-ben

1. Jelentkezzen be az [Azure Portalba](https://portal.azure.com/) Azure ad-rendszergazdaként.
2. A bal oldali panelen válassza az **Azure Active Directory** lehetőséget.
3.  A **Kezelés** menüpontban válassza a **Felhasználók** lehetőséget.

    ![A felhasználók lehetőség kiválasztásának helyét bemutató képernyőfelvétel](media/quickstart-add-users-portal/quickstart-users-portal-user.png)

4.  Válassza az **Új vendégfelhasználót**.

    ![Képernyőfelvétel az új vendég felhasználói lehetőség kiválasztásának helyéről](media/quickstart-add-users-portal/quickstart-users-portal-user-3.png)

5. Az **új felhasználó** lapon válassza a **felhasználó meghívása** lehetőséget, majd adja hozzá a vendég felhasználó adatait. 

   - **név.** A vendég felhasználó vezetékneve és vezetékneve.
   - **E-mail-cím (kötelező)**. A vendég felhasználó e-mail-címe.
   - **Személyes üzenet (nem kötelező)** Személyes üdvözlő üzenet felvétele a vendég felhasználóra.
   - **Csoportok**: felveheti a vendég felhasználót egy vagy több meglévő csoportba, vagy később is megteheti.
   - **Címtárbeli szerepkör**: Ha az Azure ad rendszergazdai engedélyekre van szüksége a felhasználó számára, hozzáadhat egy Azure ad-szerepkörhöz. 

6. Válassza ki **Meghívás** elemet az meghívó automatikus elküldéséhez a vendégfelhasználó számára. A jobb felső sarokban megjelenik az értesítés a **Felhasználó meghívása sikerült** üzenettel. 
7.  Miután elküldte a meghívót, a felhasználói fiók automatikusan hozzáadódik a címtárhoz vendégként.

## <a name="assign-an-app-to-the-guest-user"></a>Alkalmazás a hozzárendelése a vendégfelhasználóhoz
Adja hozzá a Salesforce alkalmazást a teszt bérlőhöz és rendelje hozzá a vendégfelhasználót az alkalmazáshoz.
1.  Jelentkezzen be az Azure portálra Azure AD rendszergazdaként.
2.  A bal oldali panelen válassza ki a **Vállalati alkalmazások** elemet.
3.  Válassza az **Új alkalmazás** lehetőséget.
4. A **Hozzáadás a katalógusból** részen keresse meg és válassza ki a **Salesforce** elemet.

    ![A Hozzáadás a katalógusból keresőmezőt ábrázoló képernyőkép](media/quickstart-add-users-portal/quickstart-users-portal-select-salesforce.png)
5. Válassza a **Hozzáadás** lehetőséget.
6. A **Kezelés** részen jelölje be az **Egyszeri bejelentkezést**, majd az **Egyszeri bejelentkezési mód** részen válassza a **Jelszóalapú bejelentkezést** és kattintson a **Mentés** gombra.
7. A **Kezelés** részen válassza a **Felhasználók és csoportok** > **Felhasználó hozzáadása** > **Felhasználók és csoportok** pontot.
8. A keresőmezőt használva keressen rá a tesztfelhasználóra (ha szükséges), és válassza ki a listából. Ezután kattintson a **Kiválasztás** elemre.
9. Válassza a **Hozzárendelés** elemet. 

## <a name="accept-the-invitation"></a>A meghívás elfogadása
Most jelentkezzen be vendégfelhasználóként, hogy lássa a meghívót.
1.  Jelentkezzen be a teszt vendégfelhasználó e-mail-fiókjába.
2.  A Beérkezett üzenetek mappában található a „Meghívást kapott” e-mail.

    ![A B2B meghívót tartalmazó e-mailt bemutató képernyőfelvétel](media/quickstart-add-users-portal/quickstart-users-portal-email-small.png)

3.  Az e-mail törzsében válassza az **Első lépések** elemet. Az **Engedélyek felülvizsgálata** lap megnyílik a böngészőben. 

    ![A felülvizsgálati engedélyek oldalát ábrázoló képernyőkép](media/quickstart-add-users-portal/quickstart-users-portal-accept.png)

4. Válassza ki az **Elfogadás** lehetőséget. Megnyílik a Hozzáférési Panel, amely megjeleníti azokat az alkalmazásokat, amelyekhez a vendégfelhasználó hozzáférhet.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha már nincs rá szükség, törölje a tesztelése használt vendégfelhasználót és a teszt alkalmazást.
1.  Jelentkezzen be az Azure portálra Azure AD rendszergazdaként.
2.  A bal oldali panelen válassza az **Azure Active Directory** lehetőséget.
3.  A **Kezelés** résznél válassza a **Vállalati alkalmazások** elemet.
4.  Nyissa meg a **Salesforce** alkalmazást, majd válassza a **Törlést**.
5.  A bal oldali panelen válassza az **Azure Active Directory** lehetőséget.
6.  A **Kezelés** menüpontban válassza a **Felhasználók** lehetőséget.
7.  Válassza ki a tesztfelhasználót, majd utána a **Felhasználó törlése** lehetőséget.

## <a name="next-steps"></a>Következő lépések
Ebben az oktatóanyagban létrehozott egy vendégfelhasználót az Azure portálon és meghívót küldött az alkalmazások megosztására. Ezután megtekintette az érvényesítési folyamatot a vendégfelhasználó szempontjából, és ellenőrizte, hogy az alkalmazás valóban megjelent a vendégfelhasználó számára a Hozzáférési panelen. Az együttműködő vendégfelhasználók hozzáadásáról további tudnivalók: [Azure Active Directory B2B együttműködő felhasználók hozzáadása az Azure portálon](add-users-administrator.md).
