---
title: A Microsoft Authenticator alkalmazás áttekintése – Azure Active Directory | Microsoft Docs
description: Tudnivalók a Microsoft Authenticator alkalmazást, Mi ez, beleértve a működését, és milyen információkat ebben a szakaszban a tartalom szerepel.
services: active-directory
author: eross-msft
manager: daveba
ms.reviewer: sahenry
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: overview
ms.date: 01/24/2019
ms.author: lizross
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b47b0c5af98198d829c4658877acae2edff5455
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60473719"
---
# <a name="what-is-the-microsoft-authenticator-app"></a>Mi a Microsoft Authenticator alkalmazást?

>[!Important]
>Ezek az anyagok felhasználók számára készültek. Amennyiben Ön rendszergazda, az Azure Active Directory- (Azure AD-) környezet beállításával és kezelésével kapcsolatosan további információt az [Azure Active Directory dokumentációjában](https://docs.microsoft.com/azure/active-directory) talál.

A Microsoft Authenticator alkalmazás segítségével jelentkezzen be a fiókok kétfaktoros ellenőrzési használatakor. A kétfaktoros hitelesítési segítségével a fiókok biztonságosabb, hozzáférhet a különösen bizalmas adatok megtekintésekor. Mivel a jelszavak elfelejtett is, amelyeket elloptak vagy feltört, kéttényezős hitelesítés végrehajtása egy további biztonsági lépés, amely megnehezíti mások feltörhessék fiókja védelme érdekében.

Többféle módon, beleértve a Microsoft Authenticator alkalmazást is használhatja:

- Válasz-hitelesítés kérése után jelentkezik be a felhasználónevét és jelszavát.

- Jelentkezzen be a jelszót, a felhasználónév, az authenticator alkalmazás és a mobil eszköz használata az ujjlenyomatot, arc vagy PIN-kód megadása nélkül.

- Mint bármely más fiókok generátor kódu, amelyek támogatják az authenticator alkalmazásokhoz.

> [!Important]
> A Microsoft Authenticator alkalmazás együttműködik az olyan fiókot, amely kétfaktoros ellenőrzési használ, illetve támogatja az egyszeri jelszó időalapú (TOTP) előírásainak.
> 
> A szervezet szükség lehet, hogy egy hitelesítő alkalmazást be- és a szervezeti adatok és dokumentumok eléréséhez. Bár az alkalmazás jelenhet meg a felhasználónevet, a fiók nincs ténylegesen beállítva egy ellenőrzési módszert funkcionálni, amíg a regisztráció befejezéséhez. További információkért lásd: [adja hozzá munkahelyi vagy iskolai fiókját](user-help-auth-app-add-work-school-account.md).
> 
> Ha bejelentkezik a fiókjába problémákat tapasztal, tekintse meg [mikor nem jelentkezhet be Microsoft-fiókjába](https://support.microsoft.com/help/12429) segítséget. További információ arról, mi a teendő, ha kap a ["a megadott Microsoft-fiók nem létezik"](https://support.microsoft.com/help/13811) jelenik meg, jelentkezzen be Microsoft-fiókjával.

## <a name="terminology"></a>Terminológia

|Időtartam|Leírás|
|----|-----------|
|A kétfaktoros ellenőrzése |A-ellenőrzési folyamatot, amely előírja, hogy kifejezetten csak kétféle hitelesítési adatai, például egy jelszó és a egy PIN-kódot. A Microsoft Authenticator alkalmazás és is támogatja a standard szintű kétfaktoros hitelesítési bejelentkezés beállításának.|
|Multi-factor authentication (MFA)|Minden kétfaktoros Ez a multi-factor authentication használatát igénylő *legalább* kétféle hitelesítési adatai, a szervezet követelményei alapján.|
|Microsoft-fiók (más néven, MSA)|Hozza létre a saját személyes fiókokat, a végfelhasználóra irányuló Microsoft-termékek eléréséhez és a felhőalapú szolgáltatások, például az Outlook, OneDrive, Xbox LIVE- vagy Office 365. A Microsoft-fiók létrehozása és a Microsoft fogyasztói identitás-fiókrendszer a Microsoft által futtatott tárolja.|
|Munkahelyi vagy iskolai fiók|A szervezet által létrehozott munkahelyi vagy iskolai fiókját (például alain@contoso.com), hogy a belső hozzáférési és potenciálisan korlátozott erőforrások, például a Microsoft Azure, a Windows Intune és az Office 365-höz.|
|Ellenőrzőkód|A hat számjegyű kód, amely az authenticator alkalmazás minden egyes új fiók alatt jelenik meg. Az ellenőrző kód 30 másodpercenként meggátolja, hogy valaki használatával a kód többször változik. Ez más néven az egyszer használatos jelszót (OTP).|

## <a name="how-two-factor-verification-works-with-the-app"></a>Az alkalmazással kétfaktoros ellenőrzési működése
Két tényező ellenőrzési együttműködik a Microsoft Authenticator alkalmazást a következő módon:

- **Értesítés.** A felhasználónevét és jelszavát írja be az eszközt éppen jelentkezik be a munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiókjával, és rákérdez arra, hogy értesítést küld a Microsoft Authenticator alkalmazás **hagyhatja jóvá a bejelentkezést**. Válasszon **jóváhagyás** Ha ismeri fel a bejelentkezési kísérlet. Ellenkező esetben válasszon **Megtagadás**. Ha úgy dönt, **Megtagadás**, megjelölheti a csalárd jellegű kérésre.

- **Ellenőrző kódot.** A felhasználónevét és jelszavát írja be az eszközt éppen jelentkezik be a munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiókjával, és másolja a társított ellenőrző kód a **fiókok** a Microsoft képernyő Hitelesítő alkalmazás. Az ellenőrző kód egyszer használatos jelszót (OTP) hitelesítés is nevezik.

- **Bejelentkezés beállításának.** A felhasználónevét írja be az eszköz számára a munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiókjával jelentkezik be van, és a mobileszköz használatával győződjön meg arról, hogy az ujjlenyomat-alapú, a face vagy a PIN-kód használatával. Ezzel a módszerrel nem kell a jelszavát adja meg.

### <a name="whether-to-use-your-devices-biometric-capabilities"></a>Hogy az eszköze biometrikus képességeit használja-e
Ha a hitelesítés befejezéséhez PIN-kódot használ, állíthat be a Microsoft Authenticator alkalmazást, használja inkább az eszköz ujjlenyomattal vagy arcfelismerési (biometrikus) képességeket. Beállíthat Ez az első alkalommal használja az authenticator alkalmazás ellenőrizni a fiókját, az eszköz biometrikus képességeinek használatához PIN-kód helyett azonosítására beállítás kiválasztásával.

## <a name="who-decides-if-you-use-this-feature"></a>Akik úgy dönt, hogy ha a szolgáltatás használata?
A fiók típusától függően a szervezet dönthet, hogy kétfaktoros ellenőrzési kell használnia, vagy előfordulhat, hogy a eldöntheti, hogy saját maga számára.

- **Munkahelyi vagy iskolai fiókjával.** Ha egy munkahelyi vagy iskolai fiókot használ (például alain@contoso.com), akkor működik a szervezet számára e kétfaktoros ellenőrzési azokkal a konkrét hitelesítési módszerekkel együtt kell használni. A Microsoft Authenticator alkalmazást a munkahelyi vagy iskolai fiók hozzáadásával kapcsolatos további információkért lásd: [adja hozzá a munkahelyi vagy iskolai fiókok](user-help-auth-app-add-work-school-account.md).

- **Személyes Microsoft-fiókjával.** Dönthet úgy, hogy állítsa be a kétfaktoros ellenőrzési a személyes Microsoft-fiókok (például alain@outlook.com). A személyes Microsoft-fiók hozzáadásával kapcsolatos további információkért lásd: [adja hozzá a személyes fiókokat](user-help-auth-app-add-personal-ms-account.md).

- **Nem Microsoft-fiók.** Dönthet úgy, hogy állítsa be a kétfaktoros ellenőrzési a nem Microsoft-fiókok (például alain@gmail.com). A nem Microsoft-fiókok nem használhatja a kifejezés, kétfaktoros ellenőrzési, de meg a szolgáltatás belül található képesnek kell lennie a **biztonsági** vagy a **bejelentkezési** beállításait. A Microsoft Authenticator alkalmazás együttműködik az olyan fiókok, amelyek támogatják a TOTP szabványoknak. A nem Microsoft-fiókok hozzáadásával kapcsolatos további információkért lásd: [hozzáadása a nem Microsoft-fiókok](user-help-auth-app-add-non-ms-account.md).

## <a name="in-this-section"></a>Ebben a szakaszban

|Cikk |Leírás |
|------|------------|
|[Az alkalmazás letöltése és telepítése](user-help-auth-app-download-install.md)|Tudhatja meg, hol és hogyan, és telepítse a Microsoft Authenticator alkalmazást Android és iOS rendszerű eszközökre vonatkozó.|
|[A munkahelyi vagy iskolai fiókok hozzáadása](user-help-auth-app-add-work-school-account.md)|Ismerteti, hogyan lehet a különböző munkahelyi vagy iskolai és személyes fiókok hozzáadása a Microsoft Authenticator alkalmazást.|
|[A személyes fiókok hozzáadása](user-help-auth-app-add-personal-ms-account.md)|Ismerteti, hogyan lehet a személyes Microsoft-fiókok hozzáadása a Microsoft Authenticator alkalmazást.|
|[A nem Microsoft-fiókok hozzáadása](user-help-auth-app-add-non-ms-account.md)|Ismerteti, hogyan lehet a nem Microsoft-fiókok hozzáadása a Microsoft Authenticator alkalmazást.|
|[A fiókok manuális hozzáadása](user-help-auth-app-add-account-manual.md)|Ha nem sikerül, a megadott QR-kód bemutatja, hogyan a fiókok manuális hozzáadása a Microsoft Authenticator alkalmazást.|
|[Jelentkezzen be az alkalmazás használatával](user-help-auth-app-sign-in.md)|Ismerteti, hogyan lehet bejelentkezni a különböző fiókokról, a Microsoft Authenticator alkalmazás használatával.|
|[A fiók hitelesítő adatainak biztonsági mentése és helyreállítása](user-help-auth-app-backup-recovery.md)| Információkat biztosít a fiók hitelesítő adatainak biztonsági mentéséhez és visszaállításához a Microsoft Authenticator alkalmazás használatával.|
|[A Microsoft Authenticator alkalmazással kapcsolatos gyakori kérdések](user-help-auth-app-faq.md)|Az alkalmazással kapcsolatos gyakori kérdésekre ad választ.|
