---
title: A kétfaktoros ellenőrzési módszer és beállítások módosítása – Azure Active Directory
description: A további biztonsági ellenőrzés lapról megtudhatja, hogyan módosíthatja a munkahelyi vagy iskolai fiókjához tartozó biztonsági ellenőrzési módszert és beállításokat.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: richagi
ms.assetid: d3372d9a-9ad1-4609-bdcf-2c4ca9679a3b
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 07/06/2020
ms.author: curtand
ms.openlocfilehash: e0a6c566e8e0dfb77b5899f735020d0f1facf3d1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88798380"
---
# <a name="change-your-two-factor-verification-method-and-settings"></a>A kétfaktoros ellenőrzési módszer és beállítások módosítása

A munkahelyi vagy iskolai fiókjához tartozó biztonsági ellenőrzési módszerek beállítása után a kapcsolódó részletek bármelyikét frissítheti, beleértve a következőket:

- Alapértelmezett biztonsági ellenőrzési módszer

- Biztonsági ellenőrzési módszer részletei, például a telefonszáma

- A hitelesítő alkalmazás beállítása vagy az eszköz törlése a hitelesítő alkalmazásból

## <a name="using-the-additional-security-verification-page"></a>A további biztonsági ellenőrzés lap használata

Ha a szervezete a kétfaktoros ellenőrzés bekapcsolásának és kezelésének konkrét lépéseit is megadja, először kövesse ezeket az utasításokat. Ellenkező esetben a biztonsági ellenőrzési módszer beállításait a [további biztonsági ellenőrzés](./multi-factor-authentication-end-user-first-time.md) oldalon érheti el.

>[!Note]
>Ha a képernyőn megjelenő információk nem egyeznek meg a jelen cikkben ismertetett lépésekkel, az azt jelenti, hogy a rendszergazda bekapcsolta a **biztonsági adatok (előzetes verzió)** felhasználói élményét, vagy hogy a szervezete saját egyéni portált adott meg. További információ az új biztonsági információkkal kapcsolatban: biztonsági adatok [(előzetes verzió) – áttekintés](./security-info-setup-signin.md). Ha többet szeretne megtudni a szervezet egyéni portálján, forduljon a szervezet ügyfélszolgálatához.

### <a name="to-get-to-the-additional-security-verification-page"></a>A további biztonsági ellenőrzés oldalának beolvasása

Ezt a hivatkozást a [további biztonsági ellenőrzés lapra](https://account.activedirectory.windowsazure.com/proofup.aspx?proofup=1)is követheti.

![További biztonsági ellenőrzés lap, az elérhető biztonsági ellenőrzési módszer részleteivel](./media/multi-factor-authentication-end-user-manage-settings/mfa-security-verification-page.png)

A **további biztonsági ellenőrzés** oldalt a következő lépésekkel is elérheti:

1. Jelentkezzen be itt: [https://myapps.microsoft.com](https://myapps.microsoft.com).

1. Válassza ki a fiók nevét a jobb felső sarokban, majd válassza a **profil** lehetőséget.

1. Válassza a **további biztonsági ellenőrzés** lehetőséget.  

    ![Saját alkalmazások hivatkozás a további biztonsági ellenőrzés oldalára](./media/multi-factor-authentication-end-user-manage-settings/mfa-myapps-link.png)

>[!Note]
>További információ a **további biztonsági ellenőrzés** lap **alkalmazások jelszavai** szakaszának használatáról: [alkalmazások jelszavainak kezelése kétfaktoros ellenőrzéshez](multi-factor-authentication-end-user-app-passwords.md). Az alkalmazás jelszavait csak olyan alkalmazásokhoz szabad használni, amelyek nem támogatják a kétfaktoros ellenőrzést.

## <a name="change-your-default-security-verification-method"></a>Az alapértelmezett biztonsági ellenőrzési módszer módosítása

Miután bejelentkezett a munkahelyi vagy iskolai fiókjába a felhasználónevével és jelszavával, automatikusan megjelenik a választott biztonsági ellenőrzési módszer. A szervezet követelményeitől függően ez lehet értesítési vagy ellenőrző kód egy hitelesítő alkalmazásban, egy szöveges üzenetben vagy egy telefonhívásban.

Ha úgy dönt, hogy módosítani szeretné a használt alapértelmezett biztonsági ellenőrzési módszert, itt megteheti.

### <a name="to-change-your-default-security-verification-method"></a>Az alapértelmezett biztonsági ellenőrzési módszer módosítása

1. A **további biztonsági ellenőrzés** lapon válassza ki az **előnyben részesített beállítások** listájából használni kívánt módszert. Ekkor megjelenik az összes lehetőség, de kiválaszthatja a szervezete számára elérhetővé tetteket is.

    - **Értesítés az alkalmazáson keresztül**: a rendszer értesítést küld a hitelesítő alkalmazásban, amelyen várakozó ellenőrzési kérés található.

    - **Saját hitelesítési telefon hívása**: telefonhívást kap a mobileszközön, és megkérdezi, hogy ellenőrizze-e az adatokat.

    - **Szöveges kód a saját hitelesítési telefonjára**: egy szöveges üzenet részeként egy ellenőrző kódot fog kapni a mobileszközön. Ezt a kódot kell megadnia a munkahelyi vagy iskolai fiókjához tartozó ellenőrző kérésben.

    - **Munkahelyi telefon hívása**: telefonhívást kap az irodai telefonján, és felszólítja Önt az adatok ellenőrzésére.

    - **Ellenőrző kód használata az alkalmazásból**: a hitelesítő alkalmazás használatával beolvassa a munkahelyi vagy iskolai fiókjából a parancssorba beírni kívánt ellenőrző kódot.

2. Kattintson a **Mentés** gombra.

## <a name="add-or-change-your-phone-number"></a>Telefonszám hozzáadása vagy módosítása

A **további biztonsági ellenőrzés** lapról új telefonszámokat adhat hozzá, vagy frissítheti a meglévő számokat.

>[!Important]
>Javasoljuk, hogy adjon hozzá egy másodlagos telefonszámot, amely megakadályozza, hogy kizárja a fiókját, ha az elsődleges telefon elveszett vagy ellopták, vagy ha új telefont kap, és már nem rendelkezik az eredeti, elsődleges telefonszámmal.

### <a name="to-change-your-phone-numbers"></a>A telefonszámok módosítása

1. A **hogyan szeretne válaszolni?** szakaszban a **további biztonsági ellenőrzés** lapon frissítse a **hitelesítő telefon** (az elsődleges mobileszköz) telefonszámának adatait és az **irodai telefonját**.

1. Jelölje be a **helyettesítő hitelesítő telefon** lehetőség melletti jelölőnégyzetet, majd írja be azt a másodlagos telefonszámot, ahol telefonhívásokat fogadhat, ha nem fér hozzá az elsődleges eszközhöz.

1. Kattintson a **Mentés** gombra.

## <a name="add-a-new-account-to-the-microsoft-authenticator-app"></a>Új fiók felvétele a Microsoft hitelesítő alkalmazásba

Beállíthatja munkahelyi vagy iskolai fiókját az [Android](https://play.google.com/store/apps/details?id=com.azure.authenticator) vagy [iOS](https://apps.apple.com/app/microsoft-authenticator/id983156458)rendszerhez készült Microsoft Authenticator alkalmazásban.

Ha már beállította munkahelyi vagy iskolai fiókját a Microsoft Authenticator alkalmazásban, akkor nem kell újra végrehajtania.

1. A **hogyan szeretne válaszolni?** szakaszban a **további biztonsági ellenőrzés** lapon válassza a **hitelesítő alkalmazás beállítása** lehetőséget.

    ![Munkahelyi vagy iskolai fiók beállítása a Microsoft Authenticator alkalmazásban](./media/multi-factor-authentication-end-user-manage-settings/mfa-security-verification-page-auth-app.png)

1. Kövesse a képernyőn megjelenő utasításokat, például a mobileszköz használatával ellenőrizze a QR-kódot, majd válassza a **tovább** lehetőséget.

    Az adatok ellenőrzéséhez meg kell adnia, hogy jóvá kell hagynia egy értesítést a Microsoft Authenticator alkalmazáson keresztül.

1. Kattintson a **Mentés** gombra.

## <a name="delete-your-account-or-device-from-the-microsoft-authenticator-app"></a>Fiók vagy eszköz törlése a Microsoft Authenticator alkalmazásból

A fiókot törölheti a Microsoft Authenticator alkalmazásból, és törölheti az eszközt a munkahelyi vagy iskolai fiókjából. Általában törli az eszközt az elveszett, ellopott vagy régi eszközök fiókból való végleges eltávolításához, és törli a fiókját, hogy megpróbáljon valamilyen kapcsolódási problémát kijavítani, vagy egy fiók módosítását, például egy új felhasználónevet.

### <a name="to-delete-your-device-from-your-work-or-school-account"></a>Az eszköz törlése munkahelyi vagy iskolai fiókból

1. A **hogyan szeretne válaszolni?** szakaszban a **további biztonsági ellenőrzés** lapon jelölje be a **hitelesítő alkalmazás beállítása** gomb.

1. Kattintson a **Mentés** gombra.

### <a name="to-delete-your-account-from-the-microsoft-authenticator-app"></a>Fiók törlése a Microsoft Authenticator alkalmazásból

A Microsoft Authenticator alkalmazásban kattintson a törölni kívánt eszköz melletti **Törlés** gombra.

## <a name="turn-on-two-factor-verification-prompts-on-a-trusted-device"></a>A kétfaktoros ellenőrzési kérések bekapcsolása egy megbízható eszközön

A szervezeti beállításoktól függően előfordulhat, hogy egy jelölőnégyzet jelenik meg, amely szerint a böngészőn kétfaktoros ellenőrzés végrehajtásakor a rendszer **nem kér újra X napra** . Ha ezt a beállítást választotta a kétfaktoros ellenőrző kérések leállításához, majd elveszíti az eszközét, vagy az eszköz esetleg sérült, akkor a fiók védelmének biztosításához kapcsolja be a kétfaktoros ellenőrzési kéréseket. A kéréseket egyszerre kell bekapcsolni az összes eszközön. Sajnos a kérések nem kapcsolhatók vissza csak egy adott eszközre.

### <a name="to-turn-two-factor-verification-prompts-back-on-for-your-devices"></a>A kétfaktoros ellenőrzés bekapcsolásának visszahívása az eszközökön

A [ **további biztonsági ellenőrzés** lapon](#to-get-to-the-additional-security-verification-page)válassza a **többtényezős hitelesítés visszaállítása korábban megbízható eszközökön** lehetőséget. Amikor legközelebb bejelentkezik bármelyik eszközre, a rendszer a kétfaktoros ellenőrzés elvégzésére kéri.

## <a name="next-steps"></a>Következő lépések

A kétfaktoros ellenőrzési beállítások hozzáadása vagy frissítése után kezelheti az alkalmazás jelszavait, bejelentkezhet, vagy segítséget kérhet a kétfaktoros ellenőrzésekkel kapcsolatos gyakori problémákkal kapcsolatban.

- Az [alkalmazás jelszavainak kezelése kétfaktoros ellenőrzéshez](multi-factor-authentication-end-user-app-passwords.md) minden olyan alkalmazás esetében, amely nem támogatja a kétfaktoros ellenőrzést.

- [Bejelentkezés a kétfaktoros ellenőrzés használatával](multi-factor-authentication-end-user-signin.md)

- [A kétfaktoros ellenőrzés gyakori problémáinak megoldása](multi-factor-authentication-end-user-troubleshoot.md)