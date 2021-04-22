---
title: Új Azure Active Directory új jelszó önkiszolgáló beállításának engedélyezése
description: Ez az oktatóanyag bemutatja, hogyan engedélyezheti Azure Active Directory új jelszó önkiszolgáló visszaállítását a felhasználók egy csoportjánál, és hogyan teszteli a jelszó-visszaállítási folyamatot.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 04/21/2021
ms.author: justinha
author: justinha
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8c18dd231a708030e3a454ab8708e3f0f11dbecf
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107861822"
---
# <a name="tutorial-enable-users-to-unlock-their-account-or-reset-passwords-using-azure-active-directory-self-service-password-reset"></a>Oktatóanyag: Annak engedélyezése, hogy a felhasználók feloldják a fiókjukat vagy új jelszót állítsanak Azure Active Directory új jelszó önkiszolgáló visszaállításával

Azure Active Directory (Azure AD) önkiszolgáló jelszó-visszaállítás (SSPR) lehetővé teszi a felhasználóknak, hogy rendszergazdai vagy ügyfélszolgálati beavatkozás nélkül módosítsák vagy visszaállítsák a jelszavukat. Ha az Azure AD zárolja egy felhasználó fiókját, vagy elfelejti a jelszavát, akkor az utasításokat használva feloldhatja a blokkolást, és vissza is mehet a munkához. Ez a képesség csökkenti az ügyfélszolgálati hívásokat és a hatékonyság elvesztését, ha a felhasználó nem tud bejelentkezni az eszközére vagy egy alkalmazásba. Javasoljuk ezt a videót az SSPR engedélyezéséről és konfigurálásról [az Azure AD-ban.](https://www.youtube.com/watch?v=rA8TvhNcCvQ) Emellett egy videó is készült a rendszergazdáknak a hat leggyakoribb végfelhasználói hibaüzenet [SSPR-sel való feloldásáról.](https://www.youtube.com/watch?v=9RPrNVLzT8I)

> [!IMPORTANT]
> Ez az oktatóanyag bemutatja, hogyan engedélyezheti az új jelszó önkiszolgáló beállítását egy rendszergazdának. Ha Ön olyan végfelhasználó, aki már regisztrálva van az új jelszó önkiszolgáló nevére, és vissza kell mennie a fiókjába, akkor a Microsoft Online jelszó-visszaállítási [oldalára](https://passwordreset.microsoftonline.com/) kell visszatérni.
>
> Ha az it-csapat nem engedélyezte a saját jelszó alaphelyzetbe állításának képességét, további segítségért fel kell kérnie a segélyt.

Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Új jelszó önkiszolgáló beállításának engedélyezése Azure AD-felhasználók egy csoportjához
> * Hitelesítési módszerek és regisztrációs beállítások beállítása
> * Az SSPR-folyamat tesztelése felhasználóként

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag befejezéséhez a következő erőforrásokra és jogosultságra lesz szüksége:

* Működő Azure AD-bérlő legalább ingyenes Vagy próbaverziós Azure AD-licenccel. Az Ingyenes szinten az SSPR csak az Azure AD felhő felhasználói számára működik. A jelszó módosítása az Ingyenes szinten támogatott, a jelszó-visszaállítás azonban nem. 
    * A sorozat későbbi oktatóanyagaihoz szüksége lesz egy P1 prémium szintű Azure AD vagy próbaverziós licencre a helyszíni jelszóvisszaíráshoz.
    * Ha szükséges, [hozzon létre egy ingyenes Azure-fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Globális *rendszergazdai jogosultságokkal* rendelkező fiók.
* Egy nem rendszergazdai felhasználó, aki ismeri a jelszavát, például *testuser*. Ebben az oktatóanyagban ennek a fióknak a használatával fogja tesztelni a végfelhasználói SSPR-élményt.
    * Ha létre kell hoznia egy felhasználót, tekintse meg a [rövid útmutató: Új](../fundamentals/add-users-azure-active-directory.md)felhasználók hozzáadása a Azure Active Directory.
* Egy csoport, amelybe a nem rendszergazda felhasználó tartozik, például *az SSPR-Test-Group.* Ebben az oktatóanyagban engedélyezni fogja az SSPR-t ehhez a csoporthoz.
    * Ha létre kell hoznia egy csoportot, tekintse meg [az Alapszintű](../fundamentals/active-directory-groups-create-azure-portal.md)csoport létrehozása és tagok hozzáadása a Azure Active Directory.

## <a name="enable-self-service-password-reset"></a>Új jelszó önkiszolgáló kérésének engedélyezése

Az Azure AD lehetővé teszi az SSPR *engedélyezését a None (Nincs)*, *Selected*(Kiválasztott) vagy All users *(Minden felhasználó)* esetén. Ez a részletes funkció lehetővé teszi, hogy a felhasználók egy részkészletét válassza ki az SSPR regisztrációs folyamatának és munkafolyamatának tesztelésére. Ha már jól érzi magát a folyamattal, és megfelelő az ideje, hogy a követelményeket a felhasználók szélesebb körének adja meg, kiválaszthatja az SSPR-hez engedélyezni kívánt felhasználói csoportot. Vagy engedélyezheti az SSPR-t az Azure AD-bérlőben mindenki számára.

> [!NOTE]
> Jelenleg csak egy Azure AD-csoportot engedélyezhet az SSPR-hez a Azure Portal. Az SSPR szélesebb körű üzembe helyezésének részeként az Azure AD támogatja a beágyazott csoportokat. Győződjön meg arról, hogy a választott csoport(ök) felhasználóihoz hozzá vannak rendelve a megfelelő licencek. Ezeknek a licencelési követelményeknek jelenleg nincs ellenőrzési folyamata.

Ebben az oktatóanyagban egy tesztcsoport felhasználóinak SSPR-ét állíthatja be. Használja az *SSPR-Test-Group csoportot,* és szükség szerint adja meg saját Azure AD-csoportját:

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) globális rendszergazdai *engedélyekkel rendelkező* fiókkal.
1. Keresse meg és **válassza** Azure Active Directory elemet, majd válassza a jelszó-visszaállítás lehetőséget a bal oldali menüben. 
1. A Tulajdonságok **lapon,** az *Önkiszolgáló jelszóát* állítás engedélyezve lehetőség alatt válassza a **Csoport kiválasztása lehetőséget.**
1. Keresse meg és válassza ki az Azure AD-csoportot, például *az SSPR-Test-Group* csoportot, majd válassza a *Kijelölés lehetőséget.*

    [![Válasszon ki egy csoportot a Azure Portal, amely számára engedélyezni szeretné az új jelszó önkiszolgáló beállítását](media/tutorial-enable-sspr/enable-sspr-for-group-cropped.png)](media/tutorial-enable-sspr/enable-sspr-for-group.png#lightbox)

1. Ha engedélyezni szeretné az SSPR-t a kiválasztott felhasználók számára, válassza a **Mentés lehetőséget.**

## <a name="select-authentication-methods-and-registration-options"></a>Hitelesítési módszerek és regisztrációs beállítások kiválasztása

Ha a felhasználóknak fel kell oldaniuk a fiók zárolását vagy új jelszót kell kérniük, a rendszer újabb megerősítési módszert kér. Ez a további hitelesítési tényező azt jelenti, hogy az Azure AD csak jóváhagyott SSPR-eseményeket hagyott jóvá. A felhasználó által megadott regisztrációs információk alapján kiválaszthatja, hogy mely hitelesítési módszereket engedélyezi.

1. A Hitelesítési módszerek oldal bal  oldalán található menüben állítsa a **Visszaállításhoz** szükséges módszerek száma beállítását *1-re.*

    A biztonság növelése érdekében növelheti az SSPR-hez szükséges hitelesítési módszerek számát.

1. Válassza ki **a szervezet által engedélyezni kíván** felhasználók számára elérhető metódusokat. Ebben az oktatóanyagban jelölje be a jelölőnégyzeteket a következő módszerek engedélyezéséhez:

    * *Mobilalkalmazás-értesítés*
    * *Mobilalkalmazás-kód*
    * *E-mail*
    * *Mobiltelefon*

    Az üzleti igényeinek megfelelően más hitelesítési módszereket is engedélyezhet, például az *Irodai* telefon vagy a Biztonsági kérdések stb.

1. A hitelesítési módszerek alkalmazáshoz válassza a Mentés **lehetőséget.**

Mielőtt a felhasználók feloldanának egy fiókot vagy új jelszót kérnek, regisztrálniuk kell a kapcsolattartási adataikat. Az Azure AD ezeket a kapcsolattartási adatokat használja az előző lépésekben beállított különböző hitelesítési módszerekhez.

A rendszergazda manuálisan is meg tudja adni ezt a kapcsolattartási adatokat, vagy a felhasználók a regisztrációs portálon maguk is meg tudnak adni adatokat. Ebben az oktatóanyagban beállítjuk az Azure AD-t, hogy a következő bejelentkezéskor a felhasználóknak regisztrációt kell kérniük.

1. A Regisztráció oldal bal oldalán található menüben **válassza** az *Igen* lehetőséget a Felhasználók regisztrálásának megkövetele **a bejelentkezéskor beállításnál.**
1. **A napok száma, amely előtt a rendszer kéri a felhasználóktól a hitelesítő adataik ismételt megerősítését** értékét állítsa *180*-ra.

    Fontos naprakészen tartani a kapcsolattartási adatokat. Ha az SSPR-esemény indításakor elavult kapcsolattartási adatok létezik, előfordulhat, hogy a felhasználó nem tudja feloldani a fiókját vagy visszaállítani a jelszavát.

1. A regisztrációs beállítások alkalmazáshoz válassza a **Mentés lehetőséget.**

## <a name="set-up-notifications-and-customizations"></a>Értesítések és testreszabások beállítása

Annak érdekében, hogy a felhasználók értesülni tudjanak a fióktevékenységről, beállíthatja, hogy az Azure AD e-mailes értesítéseket küldjön az SSPR-események bekövetkeztekor. Ezek az értesítések a normál felhasználói fiókokra és a rendszergazdai fiókokra is vonatkoznak. Rendszergazdai fiókok esetén ez az értesítés egy újabb réteget biztosít a rendszer-jogosultságú rendszergazdai fiók jelszavának SSPR használatával való alaphelyzetbe állításakor. Az Azure AD minden globális rendszergazdát értesít, ha valaki SSPR-t használ egy rendszergazdai fiókon.

1. Az Értesítések oldal bal oldalán található **menüben** állítsa be a következő beállításokat:

   * A **Felhasználók értesítése új jelszó kérésekor** beállítást állítsa *Igen* értékre.
   * A **Minden rendszergazda kapjon értesítést, ha más rendszergazdák új jelszót kérnek** beállítást állítsa *Igen* értékre.

1. Az értesítési beállítások alkalmazáshoz válassza a Mentés **lehetőséget.**

Ha a felhasználóknak további segítségre van szükségük az SSPR-folyamattal kapcsolatban, testre szabhatja a "Kapcsolatfelvétel a rendszergazdával" hivatkozást. A felhasználó ezt a hivatkozást az SSPR regisztrációs folyamatában, illetve a fiók feloldásakor vagy a jelszó alaphelyzetbe állításakor választhatja ki. Annak érdekében, hogy a felhasználók biztosan kapják meg a szükséges támogatást, javasoljuk, hogy adjon meg egy egyéni ügyfélszolgálati e-mailt vagy URL-címet.

1. A Testreszabás oldal bal oldalán  található menüben állítsa a **Testreszabási súgóhivatkozást** Igen *beállításra.*
1. Az Egyéni **súgó e-mail-címe** vagy URL-címe mezőben adjon meg egy e-mail-címet vagy egy weblap URL-címét, ahol a felhasználók további segítséget kaphatnak a szervezettől, *például: \/ https: /support.contoso.com/*
1. Az egyéni hivatkozás alkalmazáshoz válassza a Mentés **lehetőséget.**

## <a name="test-self-service-password-reset"></a>Önkiszolgáló jelszó-visszaállítás tesztelése

Ha az SSPR engedélyezve van és be van állítva, tesztelje az SSPR-folyamatot egy olyan felhasználóval, aki tagja az előző szakaszban kiválasztott csoportnak, például *Test-SSPR-Group.* Az alábbi példa a *testuser fiókot* használja. Adja meg a saját felhasználói fiókját. Ez az oktatóanyag első szakaszában az SSPR-hez engedélyezett csoport része.

> [!NOTE]
> Az önkiszolgáló jelszóát állítás tesztelése során használjon nem rendszergazdai fiókot. Alapértelmezés szerint az Azure AD engedélyezi az önkiszolgáló jelszó-visszaállítást a rendszergazdák számára. Két hitelesítési módszert kell használniuk a jelszavuk visszaállításához. További információ: A rendszergazdai [visszaállítási házirendek eltérései.](concept-sspr-policy.md#administrator-reset-policy-differences)

1. A manuális regisztrációs folyamat megnyitásához nyisson meg egy új böngészőablakot InPrivate vagy inkognitó módban, és lépjen a *https: \/ /aka.ms/ssprsetup.* Az Azure AD erre a regisztrációs portálra irányítja a felhasználókat, amikor legközelebb bejelentkeznek.
1. Jelentkezzen be egy nem rendszergazdai tesztfelhasználóval, például a *testuser* felhasználóval, és regisztrálja a hitelesítési módszerek kapcsolattartási adatait.
1. Ha elkészült, kattintson a Jól néz ki **gombra,** és zárja be a böngészőablakot.
1. Nyisson meg egy új böngészőablakot InPrivate vagy inkognitó módban, és lépjen a *https: \/ /aka.ms/sspr.*
1. Adja meg a nem rendszergazdai tesztfelhasználók fiókinformációit, például *testuser*, a CAPTCHA karaktereit, majd válassza a Tovább **lehetőséget.**

    ![Adja meg a felhasználói fiók adatait a jelszó visszaállításához](media/tutorial-enable-sspr/password-reset-page.png)

1. Kövesse az ellenőrzési lépéseket a jelszó visszaállításához. Amikor végzett, e-mailben értesítést kap arról, hogy a jelszó alaphelyzetbe lett állítva.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A sorozat egy későbbi oktatóanyagában be fogja állítani a jelszóvisszaírást. Ez a funkció visszaírja a jelszóváltozásokat az Azure AD SSPR-ről egy helyszíni AD-környezetbe. Ha tovább szeretné folytatni az oktatóanyag-sorozatot a jelszóvisszaírás beállításához, most ne tiltsa le az SSPR-t.

Ha már nem szeretné használni az oktatóanyag részeként beállított SSPR funkciót, állítsa Az  SSPR állapota beállítását None (Nincs) állapotra a következő lépésekkel:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Keresse meg és **válassza** Azure Active Directory lehetőséget, majd válassza a Jelszó visszaállítása lehetőséget a bal oldali menüben. 
1. A Tulajdonságok **lapon,** az *Önkiszolgáló jelszóáttelepítés engedélyezve lehetőség alatt* válassza a Nincs **lehetőséget.**
1. Az SSPR-módosítás alkalmazáshoz válassza a Mentés **lehetőséget.**

## <a name="faqs"></a>Gyakori kérdések

Ez a szakasz az SSPR-t kipróbáló rendszergazdák és végfelhasználók gyakori kérdéseit ismerteti:

- Miért várják meg az összevont felhasználók akár  2 percet is, hogy látják a jelszó alaphelyzetbe állítását, mielőtt használhatnak helyszíni szinkronizálást használó jelszavakat?

  Azon összevont felhasználók esetében, akiknek a jelszava szinkronizálva van, a jelszavak forrása a helyszíni. Ennek eredményeképpen az SSPR csak a helyszíni jelszavakat frissíti. A jelszó kivonatának Azure AD-re való visszaszinkronizálása 2 percenként van ütemezve.

- Amikor egy újonnan létrehozott felhasználó, aki előre ki van töltve SSPR-adatokkal, például telefonnal és e-mail-címmel, felkeresi az SSPR regisztrációs oldalát, ne veszítse el a fiókjához **való hozzáférést!** az oldal címeként jelenik meg. Miért nem látják az üzenetet azok a felhasználók, akik előre ki vannak töltve az SSPR-adatokkal?

  Egy felhasználó, aki látja, ne veszítse el a **fiókjához való hozzáférését!** A a bérlőhöz konfigurált SSPR/kombinált regisztrációs csoportok tagja. Azok a felhasználók, akik nem látják, nem veszítik el **a fiókhoz való hozzáférését!** nem voltak tagjai az SSPR/kombinált regisztrációs csoportoknak.

- Ha egyes felhasználók SSPR-folyamaton mennek keresztül, és új jelszót kérnek, miért nem látják a jelszó erősségét jelző jelölőt?

  Azok a felhasználók, akik nem látják a gyenge/erős jelszóerősséget, engedélyezték a szinkronizált jelszóvisszaírást. Mivel az SSPR nem tudja meghatározni az ügyfél helyszíni környezetének jelszó-szabályzatát, nem tudja ellenőrizni a jelszó erősségét vagy gyengeségét. 

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban engedélyezte az Azure AD önkiszolgáló jelszó-visszaállítást a felhasználók egy kiválasztott csoportja számára. Megtanulta végrehajtani az alábbi műveleteket:

> [!div class="checklist"]
> * Új jelszó önkiszolgáló beállításának engedélyezése Azure AD-felhasználók egy csoportjához
> * Hitelesítési módszerek és regisztrációs beállítások beállítása
> * Az SSPR-folyamat tesztelése felhasználóként

> [!div class="nextstepaction"]
> [Az Azure AD többtényezős hitelesítés engedélyezése](./tutorial-enable-azure-mfa.md)
