---
title: Bérlők kezelése a kereskedelmi piactéren – Azure Marketplace
description: Ismerje meg, hogyan kezelheti a kereskedelmi piactér program bérlőit a partner Centerben.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: varsha-sarah
ms.author: vavargh
ms.custom: contperf-fy21q2
ms.date: 04/07/2021
ms.openlocfilehash: 446792b26527126a4db99da14a2585c17cf8610c
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2021
ms.locfileid: "107108327"
---
# <a name="manage-tenants-in-the-commercial-marketplace"></a>Bérlők kezelése a kereskedelmi piactéren

**Megfelelő szerepkörök**

- Manager

Egy Azure Active Directory (AD) bérlő, más néven *munkahelyi fiók* a dokumentációban, a szervezetnek a Azure Portalban beállított képviselete, és segít a Microsoft Cloud Services adott példányának felügyeletében a belső és külső felhasználók számára. Ha a szervezet előfizetett egy Microsoft Cloud Service-re (például Azure, Microsoft Intune vagy Microsoft 365), akkor egy Azure AD-bérlő lett létrehozva.

Több bérlőt is beállíthat a partner centerrel való használatra. Erre akkor lehet szükség, ha a vállalat több Bérlővel rendelkezik (például contoso.com, contoso.uk stb.), a további bérlőket itt is összekapcsolhatja. Ez a társítás lehetővé teszi a további bérlők felhasználóinak hozzáadását és kezelését a kereskedelmi piactér-fiókban.

A partner Center-fiókban a Manager szerepkörrel rendelkező felhasználók hozzáadhatnak és eltávolíthatnak Azure AD-bérlőket a fiókból.

## <a name="add-an-existing-tenant"></a>Meglévő bérlő hozzáadása

Másik Azure AD-bérlő hozzárendelése a partner Center-fiókhoz:

1. A partner központ jobb felső részén válassza a **Beállítások**  >  **Fiókbeállítások** lehetőséget.
1. A **szervezeti profil** területen válassza a **bérlők** lehetőséget. Megjelenik az aktuális bérlői társítások.
1. A **fejlesztő** lapon válassza a **hozzárendelés** lehetőséget.
1. Adja meg a hozzárendelni kívánt bérlő Azure AD-beli hitelesítő adatait.
1. Tekintse át az Azure AD-bérlő szervezetének és tartománynevének nevét. A társítás befejezéséhez kattintson a **Confirm (megerősítés**) gombra.

Ha a társítás sikeres, akkor készen áll a fiók felhasználóinak hozzáadására és felügyeletére a partner Center felhasználók szakaszában. A felhasználók hozzáadásával kapcsolatos további tudnivalókért lásd: [felhasználók kezelése](add-manage-users.md).

## <a name="create-a-new-tenant"></a>Új bérlő létrehozása

Új Azure AD-bérlő létrehozása a partner Center-fiókkal:

1. A partner központ jobb felső részén válassza a **Beállítások**  >  **Fiókbeállítások** lehetőséget.
1. A **szervezeti profil** területen válassza a **bérlők** lehetőséget. Megjelenik az aktuális bérlői társítások.
1. A fejlesztő lapon válassza a **Létrehozás** lehetőséget.
1. Adja meg az új Azure AD-címtár adatait:
    - **Tartománynév**: az Azure ad-tartományhoz használni kívánt egyedi név, a ". onmicrosoft.com" névvel együtt. Ha például a "példa" kifejezést adta meg, az Azure AD-tartománya "example.onmicrosoft.com" lesz.
    - **Kapcsolattartó e-mail** címe: e-mail-cím, ahol szükség esetén felveheti a kapcsolatot a fiókjával.
    - **Globális rendszergazdai felhasználói fiók adatai**: az új globális rendszergazdai fiókhoz használni kívánt keresztnév, vezetéknév, Felhasználónév és jelszó.
1. A Létrehozás gombra kattintva erősítse meg az új tartomány és fiókadatok adatait.
1. A [felhasználók hozzáadásának és kezelésének](add-manage-users.md)megkezdéséhez jelentkezzen be az új Azure ad globális rendszergazdai felhasználónevével és jelszavával.

Ha további információt szeretne arról, hogyan hozhat létre új bérlőket a Azure Portal belül, a partner Center-portál helyett tekintse meg a következő cikket: [új bérlő létrehozása a Azure Active Directoryban](/azure/active-directory/fundamentals/active-directory-access-create-new-tenant).

## <a name="remove-a-tenant"></a>Bérlő eltávolítása

Ha el szeretne távolítani egy bérlőt a partner Center-fiókból, keresse meg a **bérlők** lapon található nevet a **fiók beállításai** között, majd válassza az **Eltávolítás** lehetőséget. A rendszer felszólítja, hogy erősítse meg, hogy el kívánja távolítani a bérlőt. Ezt követően az adott bérlő egyik felhasználója sem tud bejelentkezni a partner Center-fiókba, és a felhasználókhoz konfigurált engedélyek törlődni fognak.

> [!TIP]
> Nem távolíthat el bérlőt, ha a partner központba van bejelentkezve ugyanazzal a Bérlővel. Bérlő eltávolításához be kell jelentkeznie a partner központba egy **másik** , a fiókhoz társított bérlőhöz. Ha a fiókhoz csak egy bérlő van társítva, akkor a bérlő csak a fiókot megnyitó Microsoft-fiók való bejelentkezés után távolítható el.
