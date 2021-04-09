---
title: Egyéni Azure Active Directory jelszavas védelmi listájainak konfigurálása
description: Ebből az oktatóanyagból megtudhatja, hogyan konfigurálhat egyéni tiltott jelszavas védelmi listát a Azure Active Directory számára a környezetben előforduló gyakori szavak korlátozásához.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 07/13/2020
ms.author: justinha
author: justinha
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 89abdcf4c76c92c2ed967594527d323b2cd8be32
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96741184"
---
# <a name="tutorial-configure-custom-banned-passwords-for-azure-active-directory-password-protection"></a>Oktatóanyag: egyéni tiltott jelszavak konfigurálása Azure Active Directory jelszavas védelemhez

A felhasználók gyakran olyan gyakori helyi szavakat (például iskolát, sport csapatot vagy híres személyt) használó jelszavakat hoznak létre. Ezek a jelszavak könnyen kiolvashatók és gyengék a szótáron alapuló támadásokkal szemben. A szervezet erős jelszavainak kényszerítéséhez a Azure Active Directory (Azure AD) egyéni tiltott jelszavas listája lehetővé teszi adott karakterláncok hozzáadását a kiértékeléshez és a blokkoláshoz. A jelszó-módosítási kérelem meghiúsul, ha egyezés szerepel az egyéni tiltott jelszavak listájában.

Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Egyéni tiltott jelszavak engedélyezése
> * Bejegyzések hozzáadása az egyéni tiltott jelszavak listájához
> * Jelszó-módosítások tesztelése tiltott jelszóval

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következő erőforrásokra és jogosultságokra van szüksége:

* Egy működő Azure AD-bérlő legalább egy prémium szintű Azure AD P1 vagy próbaverziós licenccel.
    * Ha szükséges, [hozzon létre egyet ingyen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* *Globális rendszergazdai* jogosultságokkal rendelkező fiók.
* Egy nem rendszergazda felhasználó, aki ismeri a jelszót, például *tesztfelhasználó*. Ebben az oktatóanyagban ezt a fiókot használja a jelszó-módosítási esemény teszteléséhez.
    * Ha létre kell hoznia egy felhasználót, tekintse meg a rövid útmutató [: új felhasználók hozzáadása a Azure Active Directoryhoz](../fundamentals/add-users-azure-active-directory.md)című témakört.
    * A jelszó-módosítási művelet tiltott jelszóval való teszteléséhez az Azure AD-bérlőt konfigurálni kell az [önkiszolgáló jelszó-visszaállításhoz](tutorial-enable-sspr.md).

## <a name="what-are-banned-password-lists"></a>Mik a tiltott jelszavak listája?

Az Azure AD globálisan tiltott jelszavak listáját tartalmazza. A globálisan tiltott jelszavak listájának tartalma nem a külső adatforrásokon alapul. Ehelyett a globálisan tiltott jelszavak listája az Azure AD biztonsági telemetria és elemzésének folyamatos eredményein alapul. Amikor egy felhasználó vagy rendszergazda megpróbálja megváltoztatni vagy alaphelyzetbe állítani a hitelesítő adatait, a rendszer ellenőrzi a kívánt jelszót a tiltott jelszavak listáján. A jelszó-módosítási kérelem meghiúsul, ha egyezés szerepel a globálisan tiltott jelszavak listájában. Ez az alapértelmezett globális tiltott jelszó lista nem módosítható.

Ha rugalmasságot szeretne biztosítani, hogy milyen jelszavak engedélyezettek, megadhat egyéni tiltott jelszavakat is. Az egyéni tiltott jelszavak listája a globálisan tiltott jelszavak mellett működik, hogy erős jelszavakat kényszerítse ki a szervezetében. A szervezetre vonatkozó feltételek hozzáadhatók az egyéni tiltott jelszavak listájához, például az alábbi példákban:

* Márkanevek
* Terméknév
* Helyek, például a vállalati központ
* Vállalatra vonatkozó belső feltételek
* Adott céges jelentéssel rendelkező rövidítések

Amikor a felhasználó a globális vagy az egyéni tiltott jelszavak listáján lévőre próbál jelszót visszaállítani, a következő hibaüzenetek egyike jelenik meg:

* *Sajnos a jelszó olyan szót, kifejezést vagy mintát tartalmaz, amely könnyen kitalálhatja a jelszavát. Próbálkozzon újra egy másik jelszóval.*
* *Sajnos nem használhatja ezt a jelszót, mert olyan szavakat vagy karaktereket tartalmaz, amelyeket a rendszergazda letiltott. Próbálkozzon újra egy másik jelszóval.*

Az egyéni tiltott jelszavak listája legfeljebb 1000 feltételre korlátozódik. A rendszer nem blokkolja a jelszavak nagyméretű listáját. Az egyéni tiltott jelszavak listájának használatának maximalizálása érdekében tekintse át az [Egyéni tiltott jelszavak listáját](concept-password-ban-bad.md#custom-banned-password-list) és a [jelszó-értékelési algoritmus áttekintését](concept-password-ban-bad.md#how-are-passwords-evaluated).

## <a name="configure-custom-banned-passwords"></a>Egyéni tiltott jelszavak konfigurálása

Engedélyezze az egyéni tiltott jelszavak listáját, és adjon hozzá néhány bejegyzést. Bármikor hozzáadhat további bejegyzéseket az egyéni tiltott jelszavak listájához.

Ha engedélyezni szeretné az egyéni tiltott jelszavak listáját, és hozzá szeretne adni bejegyzéseket, hajtsa végre a következő lépéseket:

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) *globális rendszergazdai* jogosultságokkal rendelkező fiókkal.
1. Keresse meg és válassza ki a **Azure Active Directory**, majd a bal oldali menüben válassza a **Biztonság** elemet.
1. A **kezelés** menü fejlécében válassza a **hitelesítési módszerek**, majd a **jelszavas védelem** lehetőséget.
1. Állítsa be az **Egyéni lista kikényszerítés** beállítását *Igen* értékre.
1. Karakterláncokat adhat hozzá az **Egyéni tiltott jelszavak listájához**, soronként egy karakterláncot. A következő megfontolások és korlátozások érvényesek az egyéni tiltott jelszavak listájára:

    * Az egyéni tiltott jelszavak listája akár 1000 kifejezést is tartalmazhat.
    * Az egyéni tiltott jelszavak listája kis-és nagybetűk megkülönböztetése.
    * Az egyéni tiltott jelszavak listája közös karakteres helyettesítést (például "o" és "0", "a" és "" \@ ) tekint.
    * A karakterlánc minimális hossza négy karakter, a maximális érték pedig 16 karakter.

    Adja meg saját egyéni jelszavait a tiltáshoz, ahogy az a következő példában látható

    [![Módosítsa az egyéni tiltott jelszavak listáját a hitelesítési módszerek területen a Azure Portal ](media/tutorial-configure-custom-password-protection/enable-configure-custom-banned-passwords-cropped.png)](media/tutorial-configure-custom-password-protection/enable-configure-custom-banned-passwords.png#lightbox)

1. Hagyja **meg a Windows Serveren a jelszavas védelem engedélyezése beállítást Active Directory** a *nem* értékre.
1. Az egyéni tiltott jelszavak és a bejegyzések engedélyezéséhez válassza a **Mentés** lehetőséget.

A rendszer az egyéni tiltott jelszavak listájának frissítéséhez több órát is igénybe vehet.

Hibrid környezetekben az [Azure ad jelszavas védelmet is üzembe](howto-password-ban-bad-on-premises-deploy.md)helyezheti egy helyszíni környezetben. Ugyanazokat a globális és egyéni tiltott jelszavakat használja a Felhőbeli és a helyszíni jelszó-módosítási kérelmek esetében is.

## <a name="test-custom-banned-password-list"></a>Egyéni tiltott jelszavak listájának tesztelése

Ha szeretné megtekinteni az egyéni tiltott jelszavak listáját a műveletben, próbálja meg módosítani a jelszót az előző szakaszban hozzáadott egyik változatra. Amikor az Azure AD megpróbálja feldolgozni a jelszó módosítását, a rendszer a jelszót az egyéni tiltott jelszavak listájában szereplő bejegyzéssel egyezteti. Ekkor a rendszer hibaüzenetet jelenít meg a felhasználó számára.

> [!NOTE]
> Ahhoz, hogy a felhasználó alaphelyzetbe tudja állítani a jelszavát a webes portálon, konfigurálni kell az Azure AD-bérlőt az [önkiszolgáló jelszó-visszaállításhoz](tutorial-enable-sspr.md). Ha szükséges, a felhasználó regisztrálhat a [SSPR címen https://aka.ms/ssprsetup ](https://aka.ms/ssprsetup).

1. Nyissa meg a **saját alkalmazások** lapot a következő címen: [https://myapps.microsoft.com](https://myapps.microsoft.com) .
1. A jobb felső sarokban válassza ki a nevét, majd válassza a **profil** lehetőséget a legördülő menüből.

    ![Profil kiválasztása](media/tutorial-configure-custom-password-protection/myapps-profile.png)

1. A **profil** lapon válassza a **jelszó módosítása** lehetőséget.
1. A **jelszó módosítása** lapon adja meg a meglévő (régi) jelszót. Adjon meg és erősítsen meg egy új jelszót, amely az előző szakaszban meghatározott egyéni tiltott jelszavak listáján szerepel, majd válassza a **Küldés** lehetőséget.
1. A rendszer hibaüzenetet küld, amely azt jelzi, hogy a rendszergazda letiltotta a jelszót, ahogy az az alábbi példában is látható:

    ![Hibaüzenet jelenik meg, ha olyan jelszót próbál használni, amely az egyéni tiltott jelszavak listáján szerepel](media/tutorial-configure-custom-password-protection/password-change-error.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nem szeretné használni az oktatóanyag részeként konfigurált egyéni tiltott jelszavak listáját, hajtsa végre a következő lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Keresse meg és válassza ki a **Azure Active Directory**, majd a bal oldali menüben válassza a **Biztonság** elemet.
1. A **kezelés** menü fejlécében válassza a **hitelesítési módszerek**, majd a **jelszavas védelem** lehetőséget.
1. Állítsa be az **Egyéni lista érvényesítésének** lehetőségét a *nem* értékre.
1. Az egyéni tiltott jelszó konfigurációjának frissítéséhez válassza a **Mentés** lehetőséget.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban engedélyezte és konfigurálta az egyéni jelszavas védelem listáját az Azure AD-hez. Megtanulta végrehajtani az alábbi műveleteket:

> [!div class="checklist"]
> * Egyéni tiltott jelszavak engedélyezése
> * Bejegyzések hozzáadása az egyéni tiltott jelszavak listájához
> * Jelszó-módosítások tesztelése tiltott jelszóval

> [!div class="nextstepaction"]
> [A kockázatalapú Azure AD többtényezős hitelesítés engedélyezése](./tutorial-enable-azure-mfa.md)