---
title: Kockázatalapú felhasználói bejelentkezések elleni védelem Azure Active Directory
description: Ebből az oktatóanyagból megtudhatja, hogyan engedélyezheti a felhasználók számára az Azure Identity Protection szolgáltatást, ha a fiókjában kockázatos bejelentkezési viselkedés észlelhető.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 07/13/2020
ms.author: justinha
author: justinha
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 97aabfa39954aa6ba937166eb54c05ac4874ea7e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96741338"
---
# <a name="tutorial-use-risk-detections-for-user-sign-ins-to-trigger-azure-ad-multi-factor-authentication-or-password-changes"></a>Oktatóanyag: kockázati észlelések használata felhasználói bejelentkezésekhez az Azure AD Multi-Factor Authentication vagy a jelszó módosításainak elindításához

A felhasználók biztonsága érdekében az Azure Active Directory (Azure AD) kockázatkezelési házirendjeit konfigurálhatja, amelyek automatikusan reagálnak a kockázatos viselkedésekre. Azure AD Identity Protection szabályzatok automatikusan blokkolják a bejelentkezési kísérleteket, vagy további műveleteket igényelnek, például jelszó megváltoztatását vagy az Azure AD Multi-Factor Authentication megadását. Ezek a szabályzatok a meglévő Azure AD feltételes hozzáférési szabályzatokkal együttműködve további védelmi réteget biztosítanak a szervezet számára. Előfordulhat, hogy a felhasználók nem tudnak kockázatos viselkedést kiváltani ezen házirendek egyikében, de a szervezet védett, ha a biztonság megtámadására tett kísérlet történt.

> [!IMPORTANT]
> Ez az oktatóanyag egy rendszergazdát mutat be a kockázatalapú Azure AD-Multi-Factor Authentication engedélyezéséhez.
>
> Ha az IT-csapat nem engedélyezte az Azure AD-Multi-Factor Authentication használatát, vagy ha a bejelentkezés során problémák merülnek fel, további segítségért forduljon az ügyfélszolgálathoz.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Az Azure AD Identity Protection elérhető házirendjeinek megismerése
> * Az Azure AD Multi-Factor Authentication regisztrációjának engedélyezése
> * Kockázatalapú jelszómódosítások engedélyezése
> * Kockázatalapú többtényezős hitelesítés engedélyezése
> * A felhasználói bejelentkezési kísérletek kockázati alapú házirendjeinek tesztelése

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következő erőforrásokra és jogosultságokra van szüksége:

* Egy működő Azure AD-bérlő, amely legalább egy prémium szintű Azure AD P2 vagy próbaverziós licenccel rendelkezik.
    * Ha szükséges, [hozzon létre egyet ingyen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* *Globális rendszergazdai* jogosultságokkal rendelkező fiók.
* Az Azure AD konfigurálva van az önkiszolgáló jelszó-visszaállításhoz és az Azure AD-Multi-Factor Authentication
    * Ha szükséges, [fejezze be az oktatóanyagot az Azure ad-SSPR engedélyezéséhez](tutorial-enable-sspr.md).
    * Ha szükséges, [fejezze be az oktatóanyagot az Azure AD multi-Factor Authentication engedélyezéséhez](tutorial-enable-azure-mfa.md).

## <a name="overview-of-azure-ad-identity-protection"></a>A Azure AD Identity Protection áttekintése

A Microsoft minden nap a felhasználói bejelentkezési kísérletek részeként gyűjti és elemzi a névtelenül megjelenő jeleket. Ezek a jelek segítenek felépíteni a jó felhasználói bejelentkezési viselkedés mintáit, és azonosítják a lehetséges kockázatos bejelentkezési kísérleteket. Azure AD Identity Protection áttekintheti a felhasználói bejelentkezési kísérleteket, és további műveleteket hajthat végre, ha gyanús a viselkedése:

A következő műveletek némelyike elindíthatja Azure AD Identity Protection kockázat észlelését:

* A kiszivárgott hitelesítő adatokkal rendelkező felhasználók.
* Névtelen IP-címekről érkező bejelentkezések.
* Nem lehet utazni az atipikus helyszínekre.
* Fertőzött eszközökről történő bejelentkezések.
* Gyanús tevékenységet folytató IP-címekről érkező bejelentkezések.
* Ismeretlen helyekről érkező bejelentkezések.

A következő három szabályzat érhető el Azure AD Identity Protectionban a felhasználók elleni védelemhez és a gyanús tevékenységekre való reagáláshoz. Kiválaszthatja, hogy be-vagy kikapcsolja a házirend-kényszerítést, válassza ki azokat a felhasználókat vagy csoportokat, amelyekre alkalmazni kívánja a házirendet, és döntse el, hogy szeretné-e letiltani a hozzáférést a bejelentkezéskor, vagy további műveletre kéri.

* Felhasználói kockázati szabályzat
    * Azonosítja és válaszol azokra a felhasználói fiókokra, amelyek esetlegesen sérült hitelesítő adatokkal rendelkeznek. Megkérheti a felhasználót, hogy hozzon létre egy új jelszót.
* Bejelentkezési kockázati házirend
    * Azonosítja és válaszol a gyanús bejelentkezési kísérletekre. A felszólítja a felhasználót, hogy az Azure AD Multi-Factor Authentication használatával további ellenőrzési formákat biztosítson.
* MFA-regisztrációs szabályzat
    * Ellenőrzi, hogy a felhasználók regisztrálva vannak-e az Azure AD Multi-Factor Authentication. Ha egy bejelentkezési kockázati házirend kéri az MFA-t, a felhasználónak már regisztrálva kell lennie az Azure AD Multi-Factor Authentication.

Ha engedélyezi a házirend felhasználóját vagy a kockázatkezelési szabályzatot, akkor a kockázati szint küszöbértékét is kiválaszthatja – *alacsony és* *közepes* vagy magasabb, illetve *magas*. Ez a rugalmasság lehetővé teszi, hogy eldöntse, milyen agresszíven szeretné kikényszeríteni a gyanús bejelentkezési eseményekre vonatkozó összes vezérlőt.

További információ a Azure AD Identity Protectionről: [Mi az Azure ad Identity Protection?](../identity-protection/overview-identity-protection.md)

## <a name="enable-mfa-registration-policy"></a>MFA regisztrációs szabályzatának engedélyezése

Azure AD Identity Protection tartalmaz egy alapértelmezett szabályzatot, amely segítséget nyújt az Azure AD-Multi-Factor Authentication regisztrált felhasználói számára. Ha a bejelentkezési események elleni védelemhez további szabályzatokat használ, a felhasználóknak már regisztrálniuk kell az MFA-t. Ha engedélyezi ezt a házirendet, nem igényli, hogy a felhasználók minden egyes bejelentkezéskor MFA-t végezzenek. A szabályzat csak a felhasználó regisztrációs állapotát ellenőrzi, és szükség esetén megkéri őket, hogy előzetesen regisztráljanak.

Ajánlott engedélyezni az MFA regisztrációs szabályzatát olyan felhasználók számára, akik számára engedélyezni kell a további Azure AD Identity Protection házirendeket. A szabályzat engedélyezéséhez hajtsa végre a következő lépéseket:

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) globális rendszergazdai fiók használatával.
1. Keresse meg és válassza ki a **Azure Active Directory**, válassza a **Biztonság** lehetőséget, majd a *védelem* menüpont alatt válassza az **Identity Protection** lehetőséget.
1. Válassza ki az **MFA regisztrációs szabályzatot** a bal oldali menüben.
1. Alapértelmezés szerint a szabályzat *minden felhasználóra* érvényes. Szükség esetén válassza a **hozzárendelések** lehetőséget, majd válassza ki azokat a felhasználókat vagy csoportokat, amelyekre alkalmazni szeretné a szabályzatot.
1. A *vezérlők* területen válassza a **hozzáférés** lehetőséget. Győződjön meg arról, hogy az *Azure ad MFA-regisztráció megkövetelése* beállítás **be** van jelölve, majd válassza a kiválasztás lehetőséget.
1. Állítsa be a **szabályzat érvényesítése** beállítást, majd kattintson *a* **Mentés** gombra.

    ![Képernyőkép a felhasználóknak az MFA-regisztráció megköveteléséről a Azure Portal](./media/tutorial-risk-based-sspr-mfa/enable-mfa-registration.png)

## <a name="enable-user-risk-policy-for-password-change"></a>Felhasználói kockázati házirend engedélyezése a jelszó módosítására

A Microsoft kutatókkal, a bűnüldözési hatóságokkal, különféle belső biztonsági csapatokkal és egyéb megbízható forrásokkal együttműködve keres felhasználónév–jelszó párokat. Ha a párok egyike megfelel egy fióknak a környezetben, a rendszer kockázatalapú jelszavas változást kérhet. Ehhez a Szabályzathoz és művelethez a felhasználónak frissítenie kell a jelszavát, mielőtt bejelentkeznek, hogy a korábban közzétett hitelesítő adatok már nem működnek.

A szabályzat engedélyezéséhez hajtsa végre a következő lépéseket:

1. Válassza ki a **felhasználói kockázati házirendet** a bal oldali menüben.
1. Alapértelmezés szerint a szabályzat *minden felhasználóra* érvényes. Szükség esetén válassza a **hozzárendelések** lehetőséget, majd válassza ki azokat a felhasználókat vagy csoportokat, amelyekre alkalmazni szeretné a szabályzatot.
1. A *feltételek* területen válassza  **a feltételek kiválasztása lehetőséget > válassza ki a kockázati szintet**, majd válassza a *közepes vagy újabb* lehetőséget.
1. Válassza a **kiválasztás**, majd a **kész** lehetőséget.
1. A *hozzáférés* területen válassza a **hozzáférés** lehetőséget. Győződjön meg arról, hogy a **hozzáférés engedélyezése** és a *jelszó megkövetelése* beállítás be van jelölve, majd válassza a **kiválasztás** lehetőséget.
1. Állítsa be a **szabályzat érvényesítése** beállítást, majd kattintson *a* **Mentés** gombra.

    ![Képernyőkép a felhasználói kockázati házirend engedélyezéséről a Azure Portal](./media/tutorial-risk-based-sspr-mfa/enable-user-risk-policy.png)

## <a name="enable-sign-in-risk-policy-for-mfa"></a>A bejelentkezési kockázati házirend engedélyezése az MFA-hoz

A legtöbb felhasználó rendelkezik egy normál viselkedéssel, amely nyomon követhető. Ha a jelen normán kívül esnek, akkor kockázatos lehet a sikeres bejelentkezés lehetővé tétele. Ehelyett érdemes lehet letiltani a felhasználót, vagy megkérni őket a többtényezős hitelesítés végrehajtásához. Ha a felhasználó sikeresen befejezte az MFA-kihívást, érdemes lehet érvényes bejelentkezési kísérletet megadnia, és hozzáférést biztosítani az alkalmazáshoz vagy szolgáltatáshoz.

A szabályzat engedélyezéséhez hajtsa végre a következő lépéseket:

1. Válassza ki a **bejelentkezési kockázati szabályzatot** a bal oldali menüben.
1. Alapértelmezés szerint a szabályzat *minden felhasználóra* érvényes. Szükség esetén válassza a **hozzárendelések** lehetőséget, majd válassza ki azokat a felhasználókat vagy csoportokat, amelyekre alkalmazni szeretné a szabályzatot.
1. A *feltételek* területen válassza  **a feltételek kiválasztása lehetőséget > válassza ki a kockázati szintet**, majd válassza a *közepes vagy újabb* lehetőséget.
1. Válassza a **kiválasztás**, majd a **kész** lehetőséget.
1. A *hozzáférés* területen válassza **a vezérlő kiválasztása** lehetőséget. Győződjön meg arról, hogy a **hozzáférés engedélyezése** és a *többtényezős hitelesítés megkövetelése* jelölőnégyzet **be** van jelölve, majd válassza a kiválasztás lehetőséget.
1. Állítsa be a **szabályzat érvényesítése** beállítást, majd kattintson *a* **Mentés** gombra.

    ![Képernyőkép a bejelentkezési kockázati szabályzat engedélyezéséről a Azure Portal](./media/tutorial-risk-based-sspr-mfa/enable-sign-in-risk-policy.png)

## <a name="test-risky-sign-events"></a>Kockázatos aláírási események tesztelése

A legtöbb felhasználói bejelentkezési esemény nem aktiválja az előző lépésekben konfigurált kockázatalapú házirendeket. Előfordulhat, hogy a felhasználó nem lát további MFA-t, vagy a jelszavuk alaphelyzetbe állítására kéri. Ha a hitelesítő adataik biztonságban maradnak, és a viselkedésük konzisztens, a bejelentkezési események sikeresek lesznek.

Az előző lépésekben létrehozott Azure AD Identity Protection szabályzatok teszteléséhez a kockázatos viselkedés vagy a lehetséges támadások szimulálása szükséges. A tesztek végrehajtásának lépései az érvényesíteni kívánt Azure AD Identity Protection szabályzat alapján változnak. További információ a forgatókönyvekről és a lépésekről: [a kockázati észlelések szimulálása Azure ad Identity Protectionban](../identity-protection/howto-identity-protection-simulate-risk.md).

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha befejezte a tesztelést, és már nem szeretné, hogy engedélyezve legyenek a kockázatalapú házirendek, térjen vissza a letiltani kívánt szabályzatokhoz, és állítsa be a **házirend** *kikapcsolását*.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban engedélyezte a Azure AD Identity Protection kockázatalapú felhasználói házirendjeit. Megtanulta végrehajtani az alábbi műveleteket:

> [!div class="checklist"]
> * Az Azure AD Identity Protection elérhető házirendjeinek megismerése
> * Az Azure AD Multi-Factor Authentication regisztrációjának engedélyezése
> * Kockázatalapú jelszómódosítások engedélyezése
> * Kockázatalapú többtényezős hitelesítés engedélyezése
> * A felhasználói bejelentkezési kísérletek kockázati alapú házirendjeinek tesztelése

> [!div class="nextstepaction"]
> [További információ a Azure AD Identity Protection](../identity-protection/overview-identity-protection.md)
