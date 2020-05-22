---
title: Jelszó alaphelyzetbe állítása biztonsági adatok használatával – Azure Active Directory | Microsoft Docs
description: A saját jelszavának alaphelyzetbe állítása, ha elfelejti, a biztonsági adatok és a kétlépéses ellenőrzés használatával.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 01/15/2020
ms.author: curtand
ms.openlocfilehash: b27b046d53a45c91d0a55a468758386437a7a6a5
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83739664"
---
# <a name="reset-your-work-or-school-password-using-security-info"></a>Munkahelyi vagy iskolai jelszavának alaphelyzetbe állítása biztonsági adatok használatával

Ha elfelejtette a munkahelyi vagy iskolai jelszavát, soha nem kapott jelszót a szervezettől, vagy kizárták a fiókjából, a biztonsági adatok és a mobileszköz segítségével alaphelyzetbe állíthatja a munkahelyi vagy iskolai jelszavát. A rendszergazdának be kell kapcsolnia ezt a funkciót ahhoz, hogy be tudja állítani az adatokat, és alaphelyzetbe állíthatja a saját jelszavát.

Ha ismeri a jelszavát, de módosítani szeretné, tekintse meg a jelen cikk [jelszó módosítása lépéseit](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-passwords-update-your-own-password#how-to-change-your-password) ismertető fejezetét.

>[!Important]
>Ez a cikk olyan felhasználók számára készült, akik elfelejtett vagy ismeretlen munkahelyi vagy iskolai fiók jelszavának visszaállítását szeretnék használni. Ha Ön rendszergazda, aki az alkalmazottak vagy más felhasználók önkiszolgáló jelszó-visszaállításának bekapcsolásával kapcsolatos információkat keres, tekintse meg az [Azure ad önkiszolgáló jelszó-visszaállítás üzembe helyezése és egyéb cikkek](https://docs.microsoft.com/azure/active-directory/authentication/howto-sspr-deployment)témakört.

## <a name="how-to-reset-or-unlock-your-password-for-a-work-or-school-account"></a>Munkahelyi vagy iskolai fiók jelszavának alaphelyzetbe állítása vagy zárolásának feloldása

Ha nem fér hozzá a Azure Active Directory (Azure AD) fiókhoz, annak oka az lehet, hogy:

- A jelszó nem működik, és vissza kívánja állítani, vagy

- Ismeri a jelszavát, de a fiókja zárolva van, és fel kell oldania a zárolását.

### <a name="to-reset-your-password-and-get-back-into-your-account"></a>A jelszó alaphelyzetbe állítása és visszahívása a fiókjába

1. A **jelszó megadása** képernyőn válassza az **elfelejtettem a jelszavam**lehetőséget.

2. A **fiók beolvasása** képernyőn írja be a munkahelyi vagy iskolai **felhasználói azonosítóját** (például az e-mail-címét), és igazolja, hogy nem robot a képernyőn megjelenő karakterek beírásával, majd válassza a **tovább**lehetőséget.

   ![Vissza a fiók képernyőjére](media/security-info/security-info-back-into-acct.png)

   >[!NOTE]
   >Ha a rendszergazda nem kapcsolta be a saját jelszavának alaphelyzetbe állítását, akkor a fiók képernyőjének **visszahívása** helyett **forduljon a rendszergazdai** hivatkozáshoz. Ez a hivatkozás lehetővé teszi, hogy kapcsolatba lépjen a rendszergazdájával a jelszó alaphelyzetbe állításával, vagy akár e-mailben, akár egy webportálon.

3. A következő módszerek egyikének kiválasztásával ellenőrizheti az identitását, és módosíthatja a jelszavát. Attól függően, hogy a rendszergazda hogyan állította be a szervezetét, előfordulhat, hogy másodszor is el kell végeznie ezt a folyamatot, és hozzá kell adnia egy második ellenőrzési lépéshez tartozó információt.

    ![Lépjen vissza a fiókjába, ellenőrzési lépés #1](media/security-info/security-info-back-into-acct2.png)

    >[!NOTE]
    >Attól függően, hogy a rendszergazda hogyan állította be a szervezetét, előfordulhat, hogy néhány ellenőrzési lehetőség nem érhető el. Előzőleg be kell állítania a mobileszköz ellenőrzését az alábbi módszerek legalább egyikének használatával.<br><br>Emellett előfordulhat, hogy az új jelszónak meg kell felelnie bizonyos erősségi követelményeknek. Az erős jelszavak általában 8 – 16 karakterből állnak, beleértve a kis-és nagybetűket, legalább egy számot, valamint legalább egy speciális karaktert.

- **Jelszó alaphelyzetbe állítása e-mail-cím használatával.** E-mailt küld a korábban a kétlépéses ellenőrzés vagy biztonsági információ alapján beállított e-mail-címre. Ha a rendszergazda bekapcsolta a biztonsági adatokkal kapcsolatos felhasználói élményt, további információt talál a [biztonsági adatok beállítása e-mailek használatára (előzetes verzió)](security-info-setup-email.md) című cikkben található e-mail-cím beállításával kapcsolatban. Ha még nem használja a biztonsági adatokat, további információt talál az e-mail-cím beállításáról a [fiók beállítása kétlépéses ellenőrzéshez](multi-factor-authentication-end-user-first-time.md) című cikkben. 

    1. Válassza **a másodlagos**e-mail cím lehetőséget, majd válassza az **e-mail**lehetőséget.

    2. Írja be az e-mailben szereplő ellenőrző kódot a mezőbe, majd kattintson a **tovább**gombra.

    3. Írja be és erősítse meg az új jelszót, majd kattintson a **Befejezés gombra**.

- **Jelszó alaphelyzetbe állítása szöveges üzenet használatával.** Szöveges üzenetet küld a korábban a biztonsági adatokban beállított telefonszámra. Ha a rendszergazda bekapcsolta a biztonsági információk felületét, további információt talál a szöveges üzenetküldés beállításáról a [biztonsági információk beállítása szöveges üzenetküldés (előzetes verzió)](security-info-setup-text-msg.md) című cikkben. Ha még nem használja a biztonsági adatokat, a [saját fiók beállítása kétlépéses ellenőrzéshez](multi-factor-authentication-end-user-first-time.md) című cikkben talál további információt a szöveges üzenetküldés beállításáról.

    1. Válassza a **mobil telefon**lehetőséget, írja be a telefonszámát, majd válassza a **text (szöveg**) lehetőséget.

    2. Írja be az ellenőrző kódot a szöveges üzenetből a mezőbe, majd kattintson a **tovább**gombra.

    3. Írja be és erősítse meg az új jelszót, majd kattintson a **Befejezés gombra**.

- **A jelszó alaphelyzetbe állítása telefonszám használatával.** Szöveges üzenetet küld a korábban a biztonsági adatokban beállított telefonszámra. Ha a rendszergazda bekapcsolta a biztonsági adatokkal kapcsolatos élményt, további információt talál a telefonszám beállítása a [biztonsági adatok beállítása telefonhívás (előzetes verzió)](security-info-setup-phone-number.md) című cikkben. Ha még nem használja a biztonsági adatokat, további információt talál a telefonos [fiók beállítása kétlépéses ellenőrzéshez](multi-factor-authentication-end-user-first-time.md) című cikkben.

    1. Válassza a **mobil telefon hívása**lehetőséget, adja meg a telefonszámát, majd válassza a **hívás**lehetőséget.

    2. Válaszolja meg a telefonhívást, és kövesse az utasításokat az identitás ellenőrzéséhez, majd válassza a **tovább**lehetőséget.

    3. Írja be és erősítse meg az új jelszót, majd kattintson a **Befejezés gombra**.

- **A jelszó alaphelyzetbe állítása biztonsági kérdések használatával.** A biztonsági adatokban beállított biztonsági kérdések listáját jeleníti meg. Ha a rendszergazda bekapcsolta a biztonsági adatokkal kapcsolatos felhasználói élményt, további információt talál a biztonsági kérdések beállításáról a biztonsági [információk beállítása az előre definiált biztonsági kérdések használatára (előzetes verzió)](security-info-setup-questions.md) című cikkben. Ha még nem használja a biztonsági adatokat, további információt talál a biztonsági kérdések beállításáról a [fiók beállítása kétlépéses ellenőrzéshez](multi-factor-authentication-end-user-first-time.md) című cikkben.

    1. Válassza a **biztonsági kérdések megválaszolása**lehetőséget, válaszoljon a kérdésekre, majd válassza a **tovább**lehetőséget.

    2. Írja be és erősítse meg az új jelszót, majd kattintson a **Befejezés gombra**.

- **A jelszó alaphelyzetbe állítása a hitelesítő alkalmazásból származó értesítés használatával.** Jóváhagyási értesítés küldése a hitelesítő alkalmazásnak. Ha a rendszergazda bekapcsolta a biztonsági adatokkal kapcsolatos felhasználói élményt, további információt talál a hitelesítő alkalmazások beállításáról, hogy értesítést küldjön a [hitelesítési alkalmazás beállítása (előzetes verzió)](security-info-setup-auth-app.md) című cikkben. Ha még nem használja a biztonsági adatokat, a hitelesítő alkalmazás beállításával kapcsolatos további információkért tekintse meg az értesítéseket a [fiók beállítása kétlépéses ellenőrzéshez](multi-factor-authentication-end-user-first-time.md) című cikkben.

    1. Válassza **az értesítés jóváhagyása a saját hitelesítő alkalmazásban**lehetőséget, majd válassza az **Értesítés küldése**lehetőséget.

    2. Hagyja jóvá a bejelentkezést a hitelesítő alkalmazásból.

    3. Írja be és erősítse meg az új jelszót, majd kattintson a **Befejezés gombra**.

- **A jelszó alaphelyzetbe állítása a hitelesítő alkalmazás kódjának használatával.** A hitelesítési alkalmazás által biztosított véletlenszerű kód elfogadása. Ha a rendszergazda bekapcsolta a biztonsági információk felületét, további információt talál a hitelesítő alkalmazás beállításáról, amely lehetővé teszi, hogy a [hitelesítési alkalmazás (előzetes verzió) használatára vonatkozó biztonsági információ beállítása](security-info-setup-auth-app.md) kód megadásával adja meg a kódot. Ha még nem használja a biztonsági adatokat, a hitelesítő alkalmazás beállításával kapcsolatos további információkért tekintse meg a [saját fiók beállítása kétlépéses ellenőrzéshez](multi-factor-authentication-end-user-first-time.md) című cikkben található kódot.

  1. Válassza **a kód megadása a saját hitelesítő alkalmazásból**lehetőséget, majd válassza az **Értesítés küldése**lehetőséget.

  2. Nyissa meg a hitelesítő alkalmazást, írja be a fiókhoz tartozó ellenőrző kódot a mezőbe, majd kattintson a **tovább**gombra.

  3. Írja be és erősítse meg az új jelszót, majd kattintson a **Befejezés gombra**.

  4. Miután megkapta az üzenetet, amely azt jelzi, hogy a jelszó alaphelyzetbe lett állítva, bejelentkezhet a fiókjába az új jelszó használatával.

     Ha továbbra sem tud hozzáférni a fiókjához, további segítségért forduljon a szervezet rendszergazdájához.

A jelszó alaphelyzetbe állítása után egy visszaigazoló e-mailt kap, amely egy olyan fiókból származik, mint a "Microsoft az \< *your_organization*> nevében." Ha hasonló e-mailt kap, de nemrég nem állította vissza a jelszavát, azonnal kapcsolatba kell lépnie a szervezet rendszergazdájával.

## <a name="how-to-change-your-password"></a>Jelszó módosítása

Ha csak módosítani szeretné a jelszavát, megteheti az Office 365 portálon, az Azure hozzáférési paneljén vagy a Windows 10 bejelentkezési oldalán.

### <a name="to-change-your-password-using-the-office-365-portal"></a>A jelszó módosítása az Office 365-portál használatával

Akkor használja ezt a módszert, ha általában az Office-portálon keresztül fér hozzá az alkalmazásokhoz:

1. Jelentkezzen be az [Office 365-fiókjába](https://portal.office.com)meglévő jelszava használatával.

2. Válassza ki a profilt a jobb felső oldalon, majd válassza a **fiók megtekintése**lehetőséget.

3. Válassza a **biztonsági & adatvédelem**  >  **jelszava**lehetőséget.

4. Írja be a régi jelszót, hozza létre és erősítse meg az új jelszót, majd kattintson a **Submit (elküldés**) gombra.

### <a name="to-change-your-password-from-the-azure-access-panel"></a>Az Azure-hozzáférési panel jelszavának módosítása

Akkor használja ezt a módszert, ha általában az Azure Access panelről (MyApps) fér hozzá az alkalmazásaihoz:

1. Jelentkezzen be az [Azure-hozzáférési panelre](https://myapps.microsoft.com/)meglévő jelszava használatával.

2. Válassza ki a profilt a jobb felső oldalon, majd válassza a **profil**lehetőséget.

3. Válassza a **jelszó módosítása**lehetőséget.

4. Írja be a régi jelszót, hozza létre és erősítse meg az új jelszót, majd kattintson a **Submit (elküldés**) gombra.

### <a name="to-change-your-password-at-windows-sign-in"></a>Jelszó módosítása Windows-bejelentkezéskor

Ha a rendszergazda bekapcsolta a funkciót, megtekintheti a **jelszó alaphelyzetbe állítására** szolgáló hivatkozást a Windows 7, a Windows 8, a Windows 8,1 vagy a Windows 10 bejelentkezési képernyőjén.

1. Válassza a **jelszó alaphelyzetbe állítása** hivatkozást a jelszó-visszaállítási folyamat elindításához anélkül, hogy a szokásos webes élményt kellene használnia.

2. Erősítse meg a felhasználói azonosítót, és kattintson a **Tovább gombra**.

3. Válassza ki és erősítse meg a kapcsolattartási módszert az ellenőrzéshez. Ha szükséges, válasszon egy második ellenőrzési lehetőséget, amely eltér az előzőtől, és töltse ki a szükséges adatokat.

4. Az **új jelszó létrehozása** lapon írja be és erősítse meg az új jelszót, majd kattintson a **tovább**gombra.

    Az erős jelszavak általában 8 – 16 karakterből állnak, beleértve a kis-és nagybetűket, legalább egy számot, valamint legalább egy speciális karaktert.

5. Miután megkapta az üzenetet, amely azt jelzi, hogy a jelszó alaphelyzetbe lett állítva, válassza a **Befejezés**lehetőséget.

    Ha továbbra sem tud hozzáférni a fiókjához, további segítségért forduljon a szervezet rendszergazdájához.

## <a name="common-problems-and-their-solutions"></a>Gyakori problémák és megoldásaik

Íme néhány gyakori hiba eset és megoldásuk:

|Probléma|Leírás|Megoldás|
| --- | --- | --- |
|Ha megpróbálom módosítani a jelszavam, hibaüzenetet kapok. |A jelszó olyan szót, kifejezést vagy mintázatot tartalmaz, amely könnyen kitalálhatja a jelszavát.| Próbálkozzon újra egy erősebb jelszó használatával.|
|A felhasználói azonosító megadását követően egy, a "forduljon a rendszergazdához" nevű oldalra ugorjon.|A Microsoft azt állapította meg, hogy a felhasználói fiók jelszavát a rendszergazda felügyeli a helyszíni környezetben. Ennek eredményeképpen nem állíthatja alaphelyzetbe a jelszavát a "nem lehet hozzáférni a fiókhoz" hivatkozással. |További segítségért forduljon a rendszergazdához.|
|A felhasználói azonosító megadását követően hibaüzenet jelenik meg: "a fiók nincs engedélyezve a jelszó alaphelyzetbe állításához."|A rendszergazda nem állította be a fiókját, így visszaállíthatja a saját jelszavát.|A rendszergazda a (z) "nem tud hozzáférni a fiókhoz" hivatkozásra kattintva nem kapcsolta be a jelszó-visszaállítást, vagy nem rendelkezik licenccel a szolgáltatás használatához.<br><br> A jelszó alaphelyzetbe állításához ki kell választania a "Kapcsolatfelvétel a rendszergazdával" lehetőséget, hogy e-mailt küldjön a vállalati rendszergazdának, és tájékoztassa őket arról, hogy szeretné visszaállítani a jelszavát.|
|A felhasználói azonosító megadását követően hibaüzenet jelenik meg, amely szerint "nem sikerült ellenőrizni a fiókját."|A bejelentkezési folyamat nem tudta ellenőrizni a fiók adatait.|Ennek az üzenetnek két oka lehet.<br><br>1. a rendszergazda bekapcsolta a szervezete jelszavának alaphelyzetbe állítását, de nem regisztrált a szolgáltatás használatára. A jelszó-visszaállításhoz való regisztrációhoz tekintse meg a következő cikkek egyikét az ellenőrzési módszer alapján: [állítsa be a biztonsági adatokat a hitelesítő alkalmazás használatára (előzetes verzió)](security-info-setup-auth-app.md), [állítsa be a biztonsági adatokat telefonhívás használatára (előzetes](security-info-setup-phone-number.md)verzió), a [biztonsági információk beállítása a szöveges üzenetküldés használatára (előzetes](security-info-setup-text-msg.md)verzió), a biztonsági [információk beállítása az e-mailek használatára (](security-info-setup-email.md)előzetes verzió), vagy biztonsági [kérdések (](security-info-setup-questions.md)előzetes verzió) használatára.<br><br>2. a rendszergazda nem kapcsolta be a szervezet jelszavának visszaállítását. Ebben az esetben ki kell választania a "Kapcsolatfelvétel a rendszergazdával" lehetőséget, ha e-mailt szeretne küldeni a rendszergazdának, és a jelszó alaphelyzetbe állítását kéri.|

## <a name="next-steps"></a>További lépések

- A biztonsági adatok [(előzetes verzió) – áttekintés című](user-help-security-info-overview.md) cikkben talál további információt.

- Ha olyan személyes fiókkal próbál visszakapni, mint például az Xbox, a hotmail.com vagy a outlook.com, akkor próbálkozzon a javaslatokkal, [Ha nem tud bejelentkezni a Microsoft-fiók cikkbe](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant).
