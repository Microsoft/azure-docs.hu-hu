---
title: Azure Active Directory önkiszolgáló jelszó-visszaállítás engedélyezése
description: Ebből az oktatóanyagból megtudhatja, hogyan engedélyezheti Azure Active Directory önkiszolgáló jelszó-visszaállítást egy felhasználói csoport számára, és tesztelheti a jelszó-visszaállítási folyamatot.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 03/25/2021
ms.author: justinha
author: justinha
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 39eec4fb6e9907b36908a87c09aceabd0dd1a678
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106075166"
---
# <a name="tutorial-enable-users-to-unlock-their-account-or-reset-passwords-using-azure-active-directory-self-service-password-reset"></a>Oktatóanyag: a felhasználók számára lehetővé teszi a fiók zárolásának feloldását vagy a jelszavak alaphelyzetbe állítását Azure Active Directory önkiszolgáló jelszó-visszaállítás használatával

A Azure Active Directory (Azure AD) önkiszolgáló jelszó-visszaállítás (SSPR) lehetővé teszi a felhasználók számára a jelszavuk módosítását vagy alaphelyzetbe állítását, rendszergazdai vagy ügyfélszolgálati beavatkozás nélkül. Ha az Azure AD zárolja a felhasználó fiókját, vagy elfelejti a jelszavát, a megjelenő utasításokat követve letilthatja magukat, és visszatérhet a munkához. Ez a funkció csökkenti az ügyfélszolgálati hívásokat és a termelékenység elvesztését, ha a felhasználó nem tud bejelentkezni az eszközére vagy alkalmazására. Javasoljuk, hogy ezt a videót [hogyan engedélyezheti és konfigurálhatja a SSPR az Azure ad-ben](https://www.youtube.com/watch?v=rA8TvhNcCvQ). Emellett a rendszergazdáknak szóló videóval is rendelkezünk [a hat leggyakoribb végfelhasználói hibaüzenet SSPR való feloldásához](https://www.youtube.com/watch?v=9RPrNVLzT8I).

> [!IMPORTANT]
> Ez az oktatóanyag egy rendszergazdát mutat be, amely lehetővé teszi az önkiszolgáló jelszó-visszaállítást. Ha a végfelhasználó már regisztrálva van az önkiszolgáló jelszó-visszaállításhoz, és vissza kell kérnie a fiókját, lépjen a [Microsoft online jelszó-visszaállítás](https://passwordreset.microsoftonline.com/) lapra.
>
> Ha az informatikai csapat nem engedélyezte a saját jelszavának alaphelyzetbe állítását, további segítségért forduljon az ügyfélszolgálathoz.

Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Önkiszolgáló jelszó-visszaállítás engedélyezése Azure AD-felhasználók egy csoportjára
> * Hitelesítési módszerek és regisztrációs beállítások beállítása
> * A SSPR folyamat tesztelése felhasználóként

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag befejezéséhez a következő erőforrásokra és jogosultságokra van szüksége:

* Egy működő Azure AD-bérlő, amely legalább egy ingyenes Azure AD-vagy próbaverziós licenccel rendelkezik. Az ingyenes szinten a SSPR csak felhőalapú felhasználók számára működik az Azure AD-ben.
    * A sorozat későbbi oktatóanyagai esetén prémium szintű Azure AD P1 vagy próbaverziós licencre van szükség a helyszíni jelszó visszaírási.
    * Ha szükséges, [hozzon létre ingyenes Azure-fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* *Globális rendszergazdai* jogosultságokkal rendelkező fiók.
* Egy nem rendszergazda felhasználó, aki ismeri a jelszót, például a *tesztfelhasználó*. Ebben az oktatóanyagban a fiók használatával tesztelheti a végfelhasználói SSPR élményt.
    * Ha létre kell hoznia egy felhasználót, tekintse meg a rövid útmutató [: új felhasználók hozzáadása a Azure Active Directoryhoz](../fundamentals/add-users-azure-active-directory.md)című témakört.
* Olyan csoport, amelynek a nem rendszergazda felhasználó a tagja, szereti a *SSPR-test-Group*. Ebben az oktatóanyagban engedélyezheti a csoport SSPR.
    * Ha létre kell hoznia egy csoportot, tekintse meg [az alapszintű csoport létrehozása és a tagok hozzáadása a Azure Active Directory használatával](../fundamentals/active-directory-groups-create-azure-portal.md)című témakört.

## <a name="enable-self-service-password-reset"></a>Új jelszó önkiszolgáló kérésének engedélyezése

Az Azure AD lehetővé teszi, hogy a *nincs*, a *kiválasztott* vagy *az összes* felhasználó számára engedélyezze a SSPR. Ez a részletes képesség lehetővé teszi, hogy a felhasználók egy részhalmazát kiválassza a SSPR-regisztrációs folyamat és a munkafolyamat teszteléséhez. Ha már jól ismeri a folyamatot, és az idő arra is jogosult, hogy a felhasználók szélesebb körével kommunikáljanak a követelményekkel, kiválaszthat egy felhasználói csoportot a SSPR engedélyezéséhez. Az Azure AD-bérlőben mindenki számára is engedélyezheti a SSPR.

> [!NOTE]
> Jelenleg csak egy Azure AD-csoportot lehet engedélyezni a SSPR a Azure Portal használatával. A SSPR szélesebb körű üzembe helyezésének részeként az Azure AD támogatja a beágyazott csoportokat. Győződjön meg arról, hogy a kiválasztott csoport (ok) hoz tartozó felhasználók rendelkeznek a megfelelő licencekkel. Ezen licencelési követelmények jelenleg nem rendelkeznek érvényesítési folyamattal.

Ebben az oktatóanyagban egy tesztelési csoportban lévő felhasználók készletének SSPR beállítása. Használja az *SSPR-test-Group* -t, és igény szerint adja meg saját Azure ad-csoportját:

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) *globális rendszergazdai* jogosultságokkal rendelkező fiókkal.
1. Keresse meg és válassza ki a **Azure Active Directory**, majd a bal oldali menüben válassza a **jelszó alaphelyzetbe állítása** lehetőséget.
1. A **Tulajdonságok** lapon, az *önkiszolgáló jelszó-visszaállítás engedélyezése* lehetőségnél válassza a **csoport kiválasztása** lehetőséget.
1. Keresse meg és válassza ki az Azure AD-csoportot, például *SSPR*, majd válassza a *kiválasztás* lehetőséget.

    [![Válasszon ki egy csoportot a Azure Portal, hogy engedélyezze az önkiszolgáló jelszó-visszaállítást](media/tutorial-enable-sspr/enable-sspr-for-group-cropped.png)](media/tutorial-enable-sspr/enable-sspr-for-group.png#lightbox)

1. A SSPR engedélyezéséhez válassza a **Mentés** lehetőséget.

## <a name="select-authentication-methods-and-registration-options"></a>Hitelesítési módszerek és regisztrációs beállítások kiválasztása

Ha a felhasználóknak fel kell oldaniuk a fiók zárolását vagy a jelszavuk alaphelyzetbe állítását, a rendszer egy másik megerősítő módszert kér. Ez az extra hitelesítési tényező gondoskodik arról, hogy az Azure AD csak a jóváhagyott SSPR-eseményeket fogadta el. Kiválaszthatja, hogy mely hitelesítési módszerek legyenek engedélyezve, a felhasználó által biztosított regisztrációs információk alapján.

1. A **hitelesítési módszerek** lap bal oldali menüjében állítsa be az *1* értékre való **visszaállításhoz szükséges metódusok számát** .

    A biztonság növelése érdekében növelheti a SSPR szükséges hitelesítési módszerek számát.

1. Válassza ki **azokat a módszereket,** amelyeket a szervezetnek engedélyezni kíván a felhasználók számára. Ebben az oktatóanyagban a következő módszerekkel engedélyezheti a jelölőnégyzeteket:

    * *Mobile apps – értesítés*
    * *Mobilalkalmazás-kód*
    * *E-mail*
    * *Mobiltelefon*

    Az üzleti igényeknek megfelelően más hitelesítési módszereket, például az *irodai telefonos* vagy *biztonsági kérdéseket* is engedélyezhet.

1. A hitelesítési módszerek alkalmazásához válassza a **Mentés** lehetőséget.

Ahhoz, hogy a felhasználók fel tudják oldani a fiókját vagy a jelszó alaphelyzetbe állítását, regisztrálniuk kell a kapcsolattartási adataikat. Az Azure AD ezt a kapcsolattartási információt használja az előző lépésekben beállított különböző hitelesítési módszerekhez.

A rendszergazda manuálisan megadhatja a kapcsolattartási adatokat, vagy a felhasználók a regisztrációs portálon is megadhatják az információkat. Ebben az oktatóanyagban be kell állítania az Azure AD-t, hogy a következő bejelentkezéskor kérdezze a felhasználókat a regisztrációra.

1. A **regisztrációs** oldal bal oldali menüjében válassza az *Igen* lehetőséget, ha a **felhasználóknak regisztrálniuk kell a bejelentkezéskor**.
1. **A napok száma, amely előtt a rendszer kéri a felhasználóktól a hitelesítő adataik ismételt megerősítését** értékét állítsa *180*-ra.

    Fontos, hogy a kapcsolattartási adatok naprakészek maradjanak. Ha elavult kapcsolattartási adatok állnak rendelkezésre egy SSPR-esemény indításakor, előfordulhat, hogy a felhasználó nem tudja feloldani a fiókját, vagy nem állítja vissza a jelszavát.

1. A regisztrációs beállítások alkalmazásához válassza a **Mentés** lehetőséget.

## <a name="set-up-notifications-and-customizations"></a>Értesítések és testreszabások beállítása

Ahhoz, hogy a felhasználók tájékoztassanak a fiók tevékenységéről, beállíthatja, hogy az Azure AD e-mail-értesítéseket küldjön, amikor SSPR esemény történik. Ezek az értesítések a normál felhasználói fiókokra és a rendszergazdai fiókokra is kiterjednek. A rendszergazdai fiókok esetében ez az értesítés egy másik, a SSPR segítségével alaphelyzetbe állított rendszergazdai fiók jelszavának visszaállítási rétegét nyújtja. Az Azure AD értesíti az összes globális rendszergazdát, ha valaki a SSPR-t használja egy rendszergazdai fiókon.

1. Az **értesítések** lap bal oldali menüjében állítsa be a következő beállításokat:

   * A **Felhasználók értesítése új jelszó kérésekor** beállítást állítsa *Igen* értékre.
   * A **Minden rendszergazda kapjon értesítést, ha más rendszergazdák új jelszót kérnek** beállítást állítsa *Igen* értékre.

1. Az értesítési beállítások alkalmazásához válassza a **Mentés** lehetőséget.

Ha a felhasználóknak további segítségre van szüksége a SSPR folyamathoz, testreszabhatja a "Kapcsolatfelvétel a rendszergazdával" hivatkozást. A felhasználó kiválaszthatja ezt a hivatkozást a SSPR regisztrációs folyamatában, és megnyithatja a fiókját, vagy visszaállíthatja a jelszavát. Annak biztosításához, hogy a felhasználók megkapják a szükséges támogatást, javasoljuk, hogy adjon meg egy egyéni helpdesk-e-mailt vagy URL-címet.

1. A **testreszabási** oldal bal oldalán lévő menüből válassza az **segélyszolgálat testreszabása hivatkozást** *Igen* értékre.
1. Az **Egyéni segélyszolgálat e-mail-címe vagy URL-** címe mezőben adjon meg egy e-mail-címet vagy egy weblap URL-címét, ahol a felhasználók további segítséget kaphatnak a szervezettől, például *https: \/ /support.contoso.com/*
1. Az egyéni hivatkozás alkalmazásához válassza a **Mentés** lehetőséget.

## <a name="test-self-service-password-reset"></a>Önkiszolgáló jelszó-visszaállítás tesztelése

Ha a SSPR engedélyezve van és be van állítva, tesztelje a SSPR folyamatot egy olyan felhasználóval, aki az előző szakaszban kiválasztott csoport része, például a *test-SSPR-Group*. A következő példa a *tesztfelhasználó* fiókot használja. Adja meg saját felhasználói fiókját. Ez az oktatóanyag első szakaszában a SSPR számára engedélyezett csoport része.

> [!NOTE]
> Ha teszteli az önkiszolgáló jelszó-visszaállítást, használjon egy nem rendszergazdai fiókot. Alapértelmezés szerint az Azure AD lehetővé teszi az önkiszolgáló jelszó-visszaállítást a rendszergazdák számára. A jelszavuk alaphelyzetbe állításához két hitelesítési módszert kell használniuk. További információ: [rendszergazdai beállítások visszaállítása a szabályzatban](concept-sspr-policy.md#administrator-reset-policy-differences).

1. A manuális regisztrációs folyamat megtekintéséhez nyisson meg egy új böngészőablakot InPrivate-vagy inkognitóban-módban, és keresse meg a *https: \/ /aka.MS/ssprsetup* lehetőséget. Az Azure AD a felhasználókat a regisztrációs portálra irányítja, amikor a következő alkalommal jelentkeznek be.
1. Jelentkezzen be egy nem rendszergazdai jogosultsággal rendelkező felhasználóval, például a *tesztfelhasználó*-val, és regisztrálja a hitelesítési módszereket a kapcsolattartási adatokhoz.
1. Ha elkészült, jelölje be **a jól látható gombra, és a** böngészőablak bezárásához.
1. Nyisson meg egy új böngészőablakot InPrivate-vagy inkognitóban-módban, és keresse meg a *https: \/ /aka.MS/sspr* lehetőséget.
1. Adja meg a nem rendszergazda felhasználók fiókjának adatait, például a *tesztfelhasználó*, a CAPTCHA karaktereit, majd kattintson a **tovább** gombra.

    ![Adja meg a felhasználói fiók adatait a jelszó alaphelyzetbe állításához](media/tutorial-enable-sspr/password-reset-page.png)

1. A jelszó alaphelyzetbe állításához kövesse az ellenőrzési lépéseket. Ha elkészült, a rendszer e-mailben értesítést küld arról, hogy a jelszó alaphelyzetbe állt.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A sorozat egy későbbi oktatóanyagában beállíthatja a jelszó visszaírási. Ez a szolgáltatás az Azure AD-SSPR visszaadott jelszavakat egy helyszíni AD-környezetbe írja. Ha ezt az oktatóanyag-sorozatot szeretné folytatni a jelszó-visszaírási beállításához, ne tiltsa le a SSPR.

Ha már nem szeretné használni az oktatóanyag részeként beállított SSPR funkciót, állítsa a SSPR állapotot a **none** értékre a következő lépések végrehajtásával:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Keresse meg és válassza ki a **Azure Active Directory**, majd a bal oldali menüben válassza a **jelszó alaphelyzetbe állítása** lehetőséget.
1. A **Tulajdonságok** lapon, az *önkiszolgáló jelszó-visszaállítás engedélyezése* lehetőségnél válassza a **nincs** lehetőséget.
1. A SSPR módosításának alkalmazásához válassza a **Mentés** lehetőséget.

## <a name="faqs"></a>Gyakori kérdések

Ez a szakasz a rendszergazdák és a SSPR kipróbálását végző végfelhasználók gyakori kérdéseit ismerteti:

- Az összevont felhasználók miért várnak akár 2 percet a **jelszó visszaállítását** követően, mielőtt a helyszíni rendszerből szinkronizált jelszavakat használhassanak?

  Azon összevont felhasználók esetében, akiknek a jelszava szinkronizálva van, a jelszavak szolgáltatójának forrása a helyszínen van. Ennek eredményeképpen a SSPR csak a helyszíni jelszavakat frissíti. Az Azure AD-ba való jelszó-kivonatolási szinkronizálás 2 percenként van ütemezve.

- Ha olyan újonnan létrehozott felhasználót, aki előre ki van töltve SSPR-adatokkal, például a telefonos és e-mail-címmel, látogasson el a SSPR regisztrációs oldalára, **ne veszítse el a fiókjához való hozzáférést.** a lap címeként jelenik meg. Miért nem más, előre feltöltött SSPR-adatokkal rendelkező felhasználók láthatják az üzenetet?

  Egy felhasználó, aki **úgy látja, hogy nem veszíti el a fiókjához való hozzáférést.** tagja a bérlőhöz konfigurált SSPR/kombinált regisztrációs csoportoknak. Azok a felhasználók, akik nem látják **, nem veszítik el a fiókjához való hozzáférést.** nem voltak a SSPR/kombinált regisztrációs csoportok részei.

- Ha néhány felhasználó áthalad a SSPR folyamaton, és alaphelyzetbe állítja a jelszavát, akkor miért nem látják a jelszó erősségének jelzőjét?

  Azok a felhasználók, akik nem látják a gyenge/erős jelszó erősségét, engedélyezve vannak a szinkronizált jelszó visszaírási. Mivel a SSPR nem tudja meghatározni az ügyfél helyszíni környezetének jelszavas házirendjét, nem tudja érvényesíteni a jelszó erősségét vagy gyengeségét. 

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban engedélyezte az Azure AD önkiszolgáló jelszó-visszaállítását egy kiválasztott felhasználói csoport számára. Megtanulta végrehajtani az alábbi műveleteket:

> [!div class="checklist"]
> * Önkiszolgáló jelszó-visszaállítás engedélyezése Azure AD-felhasználók egy csoportjára
> * Hitelesítési módszerek és regisztrációs beállítások beállítása
> * A SSPR folyamat tesztelése felhasználóként

> [!div class="nextstepaction"]
> [Az Azure AD többtényezős hitelesítés engedélyezése](./tutorial-enable-azure-mfa.md)
