---
title: Hozzáférési csomag jóváhagyási beállításainak módosítása az Azure AD-jogosultságok kezelésében – Azure Active Directory
description: Megtudhatja, hogyan módosíthatja egy hozzáférési csomag jóváhagyási és kérelmezői információit a Azure Active Directory jogosultságok kezelésében.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 09/16/2020
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 48b5260e883d85899953240f6ee4f83127681c9e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97591300"
---
# <a name="change-approval-and-requestor-information-preview-settings-for-an-access-package-in-azure-ad-entitlement-management"></a>Hozzáférési csomagra vonatkozó jóváhagyási és kérelmezői adatok (előzetes verzió) beállításainak módosítása az Azure AD-jogosultságok kezelésében

Hozzáférés-csomagkezelőként bármikor módosíthatja egy hozzáférési csomag jóváhagyási és kérelmezői adatait egy meglévő szabályzat szerkesztésével vagy új szabályzat hozzáadásával.

Ez a cikk azt ismerteti, hogyan módosítható egy meglévő hozzáférési csomag jóváhagyási és kérelmezői adatainak beállításai.

## <a name="approval"></a>Jóváhagyás

A jóváhagyás szakaszban megadhatja, hogy szükség van-e jóváhagyásra, ha a felhasználók ezt a hozzáférési csomagot kérik. A jóváhagyási beállítások a következő módon működnek:

- Csak az egyik kiválasztott jóváhagyó vagy tartalék jóváhagyónak kell jóváhagynia egy kérést az egyfázisú jóváhagyáshoz. 
- Az egyes fázisok közül csak az egyik kiválasztott jóváhagyónak kell jóváhagynia a 2. szintű jóváhagyásra vonatkozó kérelmet.
- A jóváhagyó lehet felettes, belső szponzor vagy külső szponzor, attól függően, hogy a házirend milyen módon szabályozza a hozzáférést.
- Az egyetlen vagy 2 fázisú jóváhagyáshoz nem szükséges minden kiválasztott jóváhagyó jóváhagyása.
- A jóváhagyási döntés azon alapul, hogy bármelyik jóváhagyó megtekinti-e először a kérést.

Az alábbi videóból megtudhatja, hogyan adhat hozzá jóváhagyókat a kérelmek házirendjéhez:

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4cZfg]

Az alábbi videóban megtudhatja, hogyan adhat hozzá többfázisú jóváhagyást a kérelmek házirendjéhez:

>[!VIDEO https://www.microsoft.com/videoplayer/embed/RE4d1Jw]


## <a name="change-approval-settings-of-an-existing-access-package"></a>Meglévő hozzáférési csomag jóváhagyási beállításainak módosítása

A következő lépésekkel adhatja meg a hozzáférési csomagra vonatkozó kérelmek jóváhagyási beállításait:

**Előfeltételként szükséges szerepkör:** Globális rendszergazda, felhasználói rendszergazda, katalógus tulajdonosa vagy hozzáférési csomag kezelője

1. A Azure Portal kattintson a **Azure Active Directory** , majd az **identitás-irányítás** elemre.

1. A bal oldali menüben kattintson a **hozzáférési csomagok** elemre, majd nyissa meg a hozzáférési csomagot.

1. Válasszon ki egy szabályzatot, vagy adjon hozzá egy új szabályzatot a hozzáférési csomaghoz
    1. Kattintson a **házirendek** , majd a **házirend hozzáadása** elemre, ha új szabályzatot szeretne létrehozni.
    1. Kattintson a szerkeszteni kívánt szabályzatra, majd kattintson a **Szerkesztés** gombra.

1. Lépjen a **kérelem** lapra.

1. A kiválasztott felhasználóktól érkező kérések jóváhagyásának megköveteléséhez állítsa az **Igen** értékre a **jóváhagyás megkövetelése** kapcsolót. Vagy a kérések automatikus jóváhagyásához állítsa a kapcsolót a **nem** értékre.

1. Ha szeretné megkövetelni, hogy a felhasználók indoklást szolgáltassanak a hozzáférési csomag igényléséhez, állítsa a **kérelmező indoklásának megkövetelése** **beállítást igen** értékre.
    
1. Most állapítsa meg, hogy a kérések csak egyszeres vagy 2 fázisú jóváhagyást igényelnek. Állítsa be, hogy az egyfázisú jóváhagyás hány **szakasza** legyen **1** , vagy a 2. fázis jóváhagyása esetén állítsa a váltást **2** -re.

    ![Hozzáférési csomag – kérelmek – jóváhagyási beállítások](./media/entitlement-management-access-package-approval-policy/approval.png)


A következő lépésekkel adhatja hozzá a jóváhagyókat, miután kiválasztotta, hogy hány lépést kell megadnia: 

### <a name="single-stage-approval"></a>Egyfázisú jóváhagyás

1. Adja hozzá az **első jóváhagyót**:
    
    Ha a házirend úgy van beállítva, hogy a címtárban lévő felhasználók hozzáférését szabályozza, válassza a **felettes jóváhagyóként** lehetőséget. Vagy egy adott felhasználó hozzáadásához kattintson a **Jóváhagyók hozzáadása** lehetőségre, miután kiválasztotta a legördülő menüből a megadott jóváhagyók kiválasztása lehetőséget.
    
    ![Hozzáférési csomag – kérelmek – a címtárbeli felhasználók számára – első jóváhagyó](./media/entitlement-management-access-package-approval-policy/approval-single-stage-first-approver-manager.png)

    Ha a házirend úgy van beállítva, hogy a címtárban nem szereplő felhasználók hozzáférését szabályozza, válassza a **külső szponzor** vagy a **belső szponzor** lehetőséget. Vagy vegyen fel egy adott felhasználót úgy, hogy a **jóváhagyók** vagy csoportok hozzáadása lehetőségre kattint az adott jóváhagyók kiválasztása alatt.
    
    ![Hozzáférési csomag – kérelmek – a címtárban lévő felhasználók számára – első jóváhagyó](./media/entitlement-management-access-package-approval-policy/out-directory-first-approver.png)
    
1. Ha az első jóváhagyóként a **kezelőt** választotta, kattintson a **tartalék hozzáadása** lehetőségre egy vagy több felhasználó vagy csoport tartalék jóváhagyóként való kiválasztásához a címtárban. A tartalék jóváhagyók megkapják a kérést, ha a jogosultságok kezelése nem találja a hozzáférést kérő felhasználó felettesét.

    A kezelőt a jogosultságok kezelése a **Manager** attribútum használatával találja. Az attribútum a felhasználó profiljában szerepel az Azure AD-ben. További információ: [felhasználói profil adatainak hozzáadása vagy frissítése Azure Active Directory használatával](../fundamentals/active-directory-users-profile-azure-portal.md).

1. Ha az **egyes jóváhagyók kiválasztása** lehetőséget választotta, kattintson a **Jóváhagyók hozzáadása** lehetőségre a címtárban lévő felhasználók vagy csoportok jóváhagyásának kiválasztásához.

1. Hány nap múlva **kell** megadnia a jelölőnégyzetet, hogy hány napig kell a jóváhagyónak áttekintenie a hozzáférési csomagra vonatkozó kérelmet.

    Ha egy kérelem nem lett jóváhagyva az adott időszakon belül, a rendszer automatikusan letiltja. A felhasználónak egy másik kérelmet kell benyújtania a hozzáférési csomaghoz.

1. Ahhoz, hogy a jóváhagyók indoklást szolgáltassanak a döntésük meghozatalához, az **Igen** értékre kell állítani a jóváhagyó indoklását.

    Az indoklás a többi jóváhagyó és a kérelmező számára látható.

### <a name="2-stage-approval"></a>2 – fázis jóváhagyása

Ha kétlépcsős jóváhagyást választott, hozzá kell adnia egy második jóváhagyót.

1. Adja hozzá a **második jóváhagyót**: 
    
    Ha a felhasználók a címtárban vannak, vegyen fel egy adott felhasználót a második jóváhagyóként. ehhez kattintson a **Jóváhagyók hozzáadása** lehetőségre a megadott jóváhagyók területen.

    ![Hozzáférési csomag – kérelmek – a címtárban található felhasználókhoz – második jóváhagyó](./media/entitlement-management-access-package-approval-policy/in-directory-second-approver.png)

    Ha a felhasználók nem a címtárban vannak, válassza a **belső szponzor** vagy **külső szponzor** lehetőséget a második jóváhagyóként. A jóváhagyó kiválasztása után adja hozzá a tartalék jóváhagyókat.

    ![Hozzáférési csomag – kérelmek – a címtáron kívüli felhasználók számára – második jóváhagyó](./media/entitlement-management-access-package-approval-policy/out-directory-second-approver.png) 

1. Itt adhatja meg, hogy hány nap elteltével kell jóváhagyni a második jóváhagyónak a kérést a mezőben a döntés alapján, **hány nap múlva kell meghoznia?**. 

1. Állítsa be a jóváhagyó indoklásának megkövetelése **beállítást igen** vagy **nem** értékre.

### <a name="alternate-approvers"></a>Alternatív jóváhagyók

Megadhat alternatív jóváhagyókat is, amelyek a kérelmeket jóváhagyó első és második jóváhagyóhoz hasonlóak. Az alternatív jóváhagyók segítségével biztosítható, hogy a kérések jóváhagyása vagy elutasítása előtt lejárjon (időtúllépés). A kétlépcsős jóváhagyáshoz az első jóváhagyót és a második jóváhagyót is listázhatja. 

Alternatív jóváhagyók megadásával abban az esetben, ha az első vagy a második jóváhagyó nem tudta jóváhagyni vagy megtagadni a kérést, a függőben lévő kérést a rendszer a házirend beállítása során megadott továbbítási ütemterv szerint továbbítja a másodlagos jóváhagyóknak. E-mailt kapnak a függőben lévő kérelem jóváhagyásához vagy elutasításához.

A kérésnek a másodlagos jóváhagyóknak való továbbítása után az első vagy a második jóváhagyó továbbra is jóváhagyhatja vagy megtagadhatja a kérelmet. A másodlagos jóváhagyók ugyanazt a saját hozzáférési helyet használják a függőben lévő kérelem jóváhagyásához vagy elutasításához.

Megadhatjuk, hogy mely személyek és csoportok legyenek jóváhagyók, és hogy az alternatív jóváhagyók. Győződjön meg arról, hogy az első, a második és a másodlagos jóváhagyók különböző készleteit sorolja fel.
Ha például az Alice és a Bob, mint az első jóváhagyó (k) szerepel, a Carol és a Dave listázása alternatív jóváhagyóként. A következő lépésekkel adhat hozzá alternatív jóváhagyókat egy hozzáférési csomaghoz:

1. Az első jóváhagyó, második jóváhagyó vagy mindkettő alatt kattintson a **speciális kérelmek beállításainak megjelenítése** lehetőségre.

    :::image type="content" source="media/entitlement-management-access-package-approval-policy/alternate-approvers-click-advanced-request.png" alt-text="Hozzáférési csomag – szabályzat – speciális kérelmek beállításainak megjelenítése":::

1. Állítsa be **, hogy nem történt-e művelet, továbbítsa a másodlagos jóváhagyóknak?** váltás az **Igen** értékre.

1. Kattintson az **alternatív Jóváhagyók hozzáadása** lehetőségre, és válassza ki a listából a másodlagos jóváhagyó (ka) t.

    ![Hozzáférési csomag – házirend – alternatív Jóváhagyók hozzáadása](./media/entitlement-management-access-package-approval-policy/alternate-approvers-add.png)

    Ha az első jóváhagyóhoz kiválasztja a felettest, akkor a másodlagos jóváhagyó mezőben választhat egy további lehetőséget, a **második szintű felettest** is. Ha ezt a beállítást választja, egy tartalék jóváhagyót kell hozzáadnia a kérelem továbbításához, ha a rendszer nem találja a második szintű kezelőt.

1. A **továbbítás a másodlagos jóváhagyó (ok)** hoz a napok száma után, hogy a jóváhagyóknak hány napig kell jóváhagyni vagy megtagadni a kérést. Ha a kérelem időtartama előtt egyetlen jóváhagyó sem hagyta jóvá vagy nem utasította el a kérelmet, a kérelem lejár (időtúllépés), és a felhasználónak egy másik kérelmet kell benyújtania a hozzáférési csomaghoz. 

    A kérések továbbítása csak a kérelem időtartamát követő nap után lehetséges, és a fő jóváhagyó (k) döntését legalább 4 nappal később el kell végezni. Ha a kérelem időtúllépése kisebb vagy egyenlő, mint 3, nincs elegendő idő arra, hogy a kérést a másodlagos jóváhagyó (k) számára továbbítsa. Ebben a példában a kérelem időtartama 14 nap. Így a kérelem időtartama a 7. napon eléri a felezési időt. Így a kérés nem továbbítható a 8. napnál korábbi időpontig. A kérések időtartamának utolsó napján nem továbbítható kérelmek. Így a példában a legutóbbi kérelem továbbítása a 13. nap.

## <a name="enable-requests"></a>Kérelmek engedélyezése

1. Ha azt szeretné, hogy a hozzáférési csomag azonnal elérhető legyen a kérési házirendben szereplő felhasználók számára a kérelemhez, helyezze át az engedélyezés kapcsolót **Igen** értékre.

    A jövőben bármikor engedélyezheti azt a hozzáférési csomag létrehozása után.

    Ha a nincs lehetőséget választotta **(csak a rendszergazda közvetlen hozzárendelései)** , és az engedélyezés a **nem** értékre van állítva, akkor a rendszergazdák nem tudják közvetlenül hozzárendelni ezt a hozzáférési csomagot.

    ![Hozzáférési csomag – házirend – házirend-beállítás engedélyezése](./media/entitlement-management-access-package-approval-policy/enable-requests.png)

1. Kattintson a **Tovább** gombra.

## <a name="collect-additional-requestor-information-for-approval-preview"></a>További kérelmezői információk gyűjtése jóváhagyásra (előzetes verzió)

Annak érdekében, hogy a felhasználók hozzáférjenek a megfelelő hozzáférési csomagokhoz, megkövetelheti a kérelmezőknek az egyéni szövegmezők vagy a kérés időpontjában megjelenő feleletválasztós kérdések megválaszolását. A szabályzatok legfeljebb 20 kérdéssel rendelkeznek, és legfeljebb 25 választ kaphat a feleletválasztós kérdésekhez. A kérdések ezután megjelennek a jóváhagyók számára, hogy döntéseket hozzanak.

1. Lépjen a **kérelmező adatai** lapra, és kattintson a **kérdések** allapra.
 
1. Írja be, hogy mit szeretne kérni a kérelmezőnek, más néven a lekérdezési karakterláncot a **kérdés** mezőben.

    ![Hozzáférési csomag – házirend – a kérelmező adatainak engedélyezése beállítás](./media/entitlement-management-access-package-approval-policy/add-requestor-info-question.png)

1. Ha a hozzáférési csomaghoz hozzáféréssel rendelkező felhasználók közössége nem rendelkezik közös előnyben részesített nyelvvel, akkor javíthatja a myaccess.microsoft.com hozzáférést kérő felhasználók élményét. A felhasználói élmény javítása érdekében alternatív megjelenítési karakterláncokat adhat meg különböző nyelvekhez. Ha például egy felhasználó webböngészője spanyolra van állítva, és a spanyol megjelenítési karakterláncok konfigurálva vannak, akkor ezek a karakterláncok megjelennek a kérelmező felhasználó számára. A kérelmek honosításának konfigurálásához kattintson a **honosítás hozzáadása** lehetőségre.
    1. Miután a **honosítás hozzáadása a kérdés** panelhez lehetőségre kattint, válassza ki annak a nyelvnek a nyelvi **kódját** , amelyben a kérdést honosítja.
    1. A konfigurált nyelven írja be a kérdést a **honosított szövegmezőbe** .
    1. Miután hozzáadta az összes szükséges honosítást, kattintson a **Mentés** gombra.

    ![Hozzáférési csomag – házirend – honosított szöveg konfigurálása](./media/entitlement-management-access-package-approval-policy/add-localization-question.png)

1. Válassza ki a **Válasz formátumát** , amelyben a kérelmezőknek válaszolniuk kell. A válaszok formátuma a következőkből áll: *rövid szöveg*, *több választási lehetőség* és *hosszú szöveg*.
 
    ![Hozzáférési csomag – házirend – több választási választ tartalmazó formátum megtekintése és szerkesztése](./media/entitlement-management-access-package-approval-policy/answer-format-view-edit.png)
 
1. Ha több lehetőséget választ, kattintson a **megtekintés és szerkesztés** gombra a válasz beállításainak konfigurálásához.
    1. A nézet kiválasztása és a **kérdés** szerkesztése ablaktábla megnyitása után megnyílik.
    1. Írja be azokat a válaszokat, amelyeket a kérelmezőnek meg szeretne adni, amikor választ ad a kérdésre a **Válasz értékek** mezőiben.
    1. Annyi választ adjon meg, amennyire szüksége van, majd kattintson a **Mentés** gombra.
    
    ![Hozzáférési csomag – házirend – több választási lehetőség megadása](./media/entitlement-management-access-package-approval-policy/answer-multiple-choice.png)
  
1. Ha szeretné megkövetelni, hogy a kérelmező megválaszolja ezt a kérdést, amikor hozzáférést kér egy hozzáférési csomaghoz, kattintson a **kötelező** jelölőnégyzetre.

1. Töltse ki a fennmaradó lapokat (pl. életciklus) az igényei alapján.

Miután konfigurálta a kérelmező adatait a hozzáférési csomag házirendjében, megtekintheti a kérelmezőnek a kérdésekre adott válaszait. A kérelmezői információk megtekintésével kapcsolatos útmutatásért tekintse meg a [kérelmező kérdéseire adott válaszokat (előzetes verzió)](entitlement-management-request-approve.md#view-requestors-answers-to-questions-preview).

## <a name="next-steps"></a>Következő lépések
- [Hozzáférési csomag életciklus-beállításainak módosítása](entitlement-management-access-package-lifecycle-policy.md)
- [Hozzáférési csomagra vonatkozó kérelmek megtekintése](entitlement-management-access-package-requests.md)
