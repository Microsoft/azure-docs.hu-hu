---
title: 'Rövid útmutató: Vendégfelhasználók hozzáadása a Azure Portal – Azure AD'
description: Ez a rövid útmutató bemutatja, hogyan vehetnek fel az Azure AD rendszergazdák B2B vendégfelhasználókat az Azure portálon és végigvezet a B2B-meghívási munkafolyamat lépésein.
services: active-directory
author: msmimart
ms.author: mimart
manager: celestedg
ms.reviewer: mal
ms.date: 08/05/2020
ms.topic: quickstart
ms.service: active-directory
ms.subservice: B2B
ms.custom:
- it-pro
- seo-update-azuread-jan
- mode-portal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7326a35d07715eae75f70f2f33763f82946c589c
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529859"
---
# <a name="quickstart-add-guest-users-to-your-directory-in-the-azure-portal"></a>Rövid útmutató: Vendégfelhasználók felvétele a címtárhoz az Azure portálon

A címtárba vendégfelhasználóként felvéve bárkit meghívhat a szervezetben végzett közös munkára. Ezután egy érvényesítési hivatkozást tartalmazó meghívó e-mailt küldhet, vagy küldhet közvetlen hivatkozást is a megosztani kívánt alkalmazáshoz. A vendégfelhasználók saját munkahelyi, iskolai vagy közösségi identitásukkal jelentkezhetnek be. A rövid útmutató segítségével többet is megtudhat [](add-users-administrator.md)a vendégfelhasználók hozzáadásáról a Azure Portal , a [PowerShell](b2b-quickstart-invite-powershell.md)vagy tömeges [használatával.](tutorial-bulk-invite.md)

Ebben a rövid útmutatóban új vendégfelhasználót fog hozzáadni az Azure AD-címtárhoz az Azure Portal-n keresztül, meghívót fog küldeni, és látni fogja, hogyan néz ki a vendégfelhasználó meghívó érvényesítési folyamata.

Ha nem rendelkezik Azure-előfizetéssel, kezdés előtt hozzon létre egy [ingyenes](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) fiókot.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

 - Egy szerepkör, amely lehetővé teszi felhasználók létrehozását a bérlő címtárban, mint amilyen például a globális rendszergazdai szerepkör vagy bármely korlátozott rendszergazdai címtári szerepkör.
 - Érvényes e-mail-fiók, amit a bérlő címtárhoz adhat, és amelyben fogadni tudja a teszt meghívó e-mailt.

## <a name="add-a-new-guest-user-in-azure-ad"></a>Új vendégfelhasználó felvétele Azure AD-ben

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com/) Azure AD-rendszergazdaként.
2. A bal oldali panelen válassza az **Azure Active Directory** lehetőséget.
3.  A **Kezelés** menüpontban válassza a **Felhasználók** lehetőséget.

    ![A Felhasználók lehetőség kiválasztásának helyét bemutató képernyőkép](media/quickstart-add-users-portal/quickstart-users-portal-user.png)

4.  Válassza az **Új vendégfelhasználót**.

    ![Az Új vendégfelhasználó lehetőség kiválasztásának helyét bemutató képernyőkép](media/quickstart-add-users-portal/quickstart-users-portal-user-3.png)

5. Az Új **felhasználó lapon** válassza a Felhasználó **meghívása lehetőséget,** majd adja meg a vendégfelhasználó adatait. 

   - **név.** A vendégfelhasználó vezeték- és vezetékneve.
   - **E-mail-cím (kötelező)**. A vendégfelhasználó e-mail-címe.
   - **Személyes üzenet (nem kötelező)** Személyes üdvözlőüzenet küldése a vendégfelhasználónak.
   - **Csoportok:** A vendégfelhasználót hozzáadhatja egy vagy több meglévő csoporthoz, vagy később is.
   - **Címtárbeli** szerepkör: Ha rendszergazdai Azure AD-engedélyekre van szüksége a felhasználó számára, hozzáadhatja őket egy Azure AD-szerepkörhöz. 

6. Válassza ki **Meghívás** elemet az meghívó automatikus elküldéséhez a vendégfelhasználó számára. A jobb felső sarokban megjelenik az értesítés a **Felhasználó meghívása sikerült** üzenettel. 
7.  Miután elküldte a meghívót, a felhasználói fiók automatikusan hozzáadódik a címtárhoz vendégként.

## <a name="assign-an-app-to-the-guest-user"></a>Alkalmazás a hozzárendelése a vendégfelhasználóhoz
Adja hozzá a Salesforce alkalmazást a teszt bérlőhöz és rendelje hozzá a vendégfelhasználót az alkalmazáshoz.
1.  Jelentkezzen be az Azure portálra Azure AD rendszergazdaként.
2.  A bal oldali panelen válassza ki a **Vállalati alkalmazások** elemet.
3.  Válassza az **Új alkalmazás** lehetőséget.
4. A **Hozzáadás a katalógusból** részen keresse meg és válassza ki a **Salesforce** elemet.

    ![Képernyőkép a Hozzáadás a katalógusból keresőmezőről](media/quickstart-add-users-portal/quickstart-users-portal-select-salesforce.png)
5. Válassza a **Hozzáadás** lehetőséget.
6. A **Kezelés** részen jelölje be az **Egyszeri bejelentkezést**, majd az **Egyszeri bejelentkezési mód** részen válassza a **Jelszóalapú bejelentkezést** és kattintson a **Mentés** gombra.
7. A **Kezelés** részen válassza a **Felhasználók és csoportok** > **Felhasználó hozzáadása** > **Felhasználók és csoportok** pontot.
8. A keresőmezőt használva keressen rá a tesztfelhasználóra (ha szükséges), és válassza ki a listából. Ezután kattintson a **Kiválasztás** elemre.
9. Válassza a **Hozzárendelés** elemet. 

## <a name="accept-the-invitation"></a>A meghívás elfogadása
Most jelentkezzen be vendégfelhasználóként, hogy lássa a meghívót.
1.  Jelentkezzen be a teszt vendégfelhasználó e-mail-fiókjába.
2.  A Beérkezett üzenetek mappában található a „Meghívást kapott” e-mail.

    ![A B2B meghívó e-mail képernyőképe](media/quickstart-add-users-portal/quickstart-users-portal-email-small.png)

3.  Az e-mail törzsében válassza az **Első lépések** elemet. Az **Engedélyek felülvizsgálata** lap megnyílik a böngészőben. 

    ![Képernyőkép az Engedélyek áttekintése lapról](media/quickstart-add-users-portal/quickstart-users-portal-accept.png)

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
