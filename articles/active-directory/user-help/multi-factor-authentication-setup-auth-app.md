---
title: Hitelesítő alkalmazás beállítása kétfaktoros ellenőrzési módszerként – Azure Active Directory | Microsoft Docs
description: Ismerje meg, hogyan állíthatja be a Microsoft Authenticator alkalmazást kétfaktoros ellenőrzési módszerként.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 08/12/2019
ms.author: curtand
ms.openlocfilehash: 10b6b788077eccda522c9e8c6e631939ca1819fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91530340"
---
# <a name="set-up-an-authenticator-app-as-your-two-factor-verification-method"></a>Hitelesítő alkalmazás beállítása kétfaktoros ellenőrzési módszerként

Beállíthat egy hitelesítő alkalmazást, hogy értesítést küldjön a mobileszköz számára, vagy egy ellenőrző kódot küldjön a biztonsági ellenőrzési módszerként. Nem kell a Microsoft Authenticator alkalmazást használnia, és a beállítás során másik alkalmazást is kijelölhet. Ez a cikk azonban az Microsoft Authenticator alkalmazást használja.

>[!Important]
>A fiók hozzáadása előtt le kell töltenie és telepítenie kell a Microsoft Authenticator alkalmazást. Ha még nem tette meg, kövesse az [alkalmazás letöltése és telepítése](user-help-auth-app-download-install.md) című cikk lépéseit.

>[!Note]
> Ha a Mobile App beállítás szürkén jelenik meg, lehetséges, hogy a szervezet nem teszi lehetővé hitelesítési alkalmazás használatát az ellenőrzéshez. Ebben az esetben ki kell választania egy másik módszert, vagy kapcsolatba kell lépnie a rendszergazdával további segítségért.

## <a name="set-up-the-microsoft-authenticator-app-to-send-notifications"></a>Az Microsoft Authenticator alkalmazás beállítása értesítések küldéséhez

1. A [további biztonsági ellenőrzés lapon](https://account.activedirectory.windowsazure.com/proofup.aspx?proofup=1) **válassza az** **1. lépés: How to Contact You (útmutató)** lehetőséget.

2. Válassza az **értesítések fogadása az ellenőrzéshez** lehetőséget, **hogy a hogyan szeretné használni a mobileszköz-alkalmazást** , majd válassza a **beállítás** lehetőséget.

    ![A "további biztonsági ellenőrzés" oldalt megjelenítő képernyőkép, amely a "Mobile App" és a "Receive Notifications for ellenőrzés" elemet mutatja.](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-notification.png)

    Megjelenik a **Mobile App konfigurálása** oldal.

    ![A QR-kódot biztosító képernyő](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-barcode.png)

3. Nyissa meg a Microsoft Authenticator alkalmazást, válassza a **fiók hozzáadása** lehetőséget a **Testreszabás és vezérlés** ikonban a jobb felső sarokban, majd válassza a **munkahelyi vagy iskolai fiók** lehetőséget.

    >[!Note]
    >Ha első alkalommal állítja be a Microsoft Authenticator alkalmazást, megkérdezheti, hogy engedélyezi-e az alkalmazásnak a kamera (iOS) elérését, vagy hogy az alkalmazás képeket készítsen, és videót rögzítsen (Android). Az **Engedélyezés lehetőséget** kell választania, hogy a hitelesítő alkalmazás hozzáférhessen a kamerához, hogy a következő lépésben képet készítsen a QR-kódból. Ha nem engedélyezi a kamerát, akkor továbbra is beállíthatja a hitelesítő alkalmazást, de a kód adatait manuálisan kell hozzáadnia. A kód manuális hozzáadásával kapcsolatos további információkért lásd: [fiók manuális hozzáadása az alkalmazáshoz](user-help-auth-app-add-account-manual.md).

4. Az eszköz kamerájának használatával beszkennelheti a QR-kódot a **mobil alkalmazás konfigurálása** képernyőről a számítógépén, majd a **tovább** gombra.

5. Térjen vissza a számítógéphez és a **további biztonsági ellenőrzés** lapra, győződjön meg arról, hogy a konfiguráció sikerességét jelző üzenet jelenik meg, majd kattintson a **tovább** gombra.

    ![A "további biztonsági ellenőrzés" oldalt megjelenítő képernyőkép a "Mobile App configured..." a sikeres üzenet kiemelve.](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-notification-confirm.png)

    A hitelesítő alkalmazás tesztként küld értesítést a mobileszköz számára.

6. A mobileszközön válassza a **jóváhagyás** lehetőséget.

7. A számítógépen adja hozzá a mobileszköz telefonszámát a **3. lépés: abban az esetben, ha elveszti a hozzáférést a Mobile App (alkalmazás** ) területen, majd kattintson a **tovább** gombra.

    Javasoljuk, hogy a mobileszköz-telefonszámot úgy adja hozzá, hogy biztonsági mentést végezzen, ha bármilyen okból nem fér hozzá a Mobile alkalmazáshoz, vagy nem használhatja azt.

8. A **4. lépés: a meglévő alkalmazások használatának megtartása** területen másolja a megadott alkalmazás jelszavát, és illessze be biztonságosba.

    ![A további biztonsági ellenőrzés lap alkalmazások jelszavai területe](media/multi-factor-authentication-verification-methods/multi-factor-authentication-app-passwords.png)

    >[!Note]
    >További információ az alkalmazás jelszavának régebbi alkalmazásokkal való használatáról: [alkalmazások jelszavainak kezelése](multi-factor-authentication-end-user-app-passwords.md). Csak akkor kell használnia az alkalmazáshoz tartozó jelszavakat, ha továbbra is olyan régebbi alkalmazásokat használ, amelyek nem támogatják a kétfaktoros ellenőrzést.

9. Válassza a **Kész** lehetőséget.

## <a name="set-up-the-microsoft-authenticator-app-to-use-verification-codes"></a>Az Microsoft Authenticator alkalmazás beállítása az ellenőrző kódok használatára

1. A **további biztonsági ellenőrzés** **lapon válassza az** 1. **lépés: How to Contact You (útmutató)** lehetőséget.

2. Válassza az **ellenőrző kód használata** lehetőséget, hogy a **hogyan szeretné használni a mobileszköz-alkalmazást** , majd válassza a **beállítás** lehetőséget.

    ![További biztonsági ellenőrzés lap a Mobile App és az értesítések lehetőséggel](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-verification-code.png)

    Megjelenik a **Mobile App konfigurálása** oldal.

    ![A QR-kódot biztosító képernyő](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-barcode.png)

3. Nyissa meg a Microsoft Authenticator alkalmazást, válassza a **fiók hozzáadása** lehetőséget a **Testreszabás és vezérlés** ikonban a jobb felső sarokban, majd válassza a **munkahelyi vagy iskolai fiók** lehetőséget.

    >[!Note]
    >Ha első alkalommal állítja be a Microsoft Authenticator alkalmazást, megkérdezheti, hogy engedélyezi-e az alkalmazásnak a kamera (iOS) elérését, vagy hogy az alkalmazás képeket készítsen, és videót rögzítsen (Android). Az **Engedélyezés lehetőséget** kell választania, hogy a hitelesítő alkalmazás hozzáférhessen a kamerához, hogy a következő lépésben képet készítsen a QR-kódból. Ha nem engedélyezi a kamerát, akkor továbbra is beállíthatja a hitelesítő alkalmazást, de a kód adatait manuálisan kell hozzáadnia. A kód manuális hozzáadásával kapcsolatos további információkért lásd: [fiók manuális hozzáadása az alkalmazáshoz](user-help-auth-app-add-account-manual.md).

4. Az eszköz kamerájának használatával beszkennelheti a QR-kódot a **mobil alkalmazás konfigurálása** képernyőről a számítógépén, majd a **tovább** gombra.

5. Térjen vissza a számítógéphez és a **további biztonsági ellenőrzés** lapra, győződjön meg arról, hogy a konfiguráció sikerességét jelző üzenet jelenik meg, majd kattintson a **tovább** gombra.

    ![További biztonsági ellenőrzés oldal, sikeres üzenet](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-verification-confirm.png)

    A hitelesítő alkalmazás tesztként kérni fogja az ellenőrző kódot.

6. A Microsoft Authenticator alkalmazásban görgessen le a munkahelyi vagy iskolai fiókjába, másolja és illessze be a 6 számjegyű kódot az alkalmazásból a **2. lépés: adja meg az ellenőrző kódot a Mobile App (mobileszköz** ) mezőben a számítógépén, majd válassza az **ellenőrzés** lehetőséget.

    ![További biztonsági ellenőrzés lap ellenőrző kód tesztelésével](media/multi-factor-authentication-verification-methods/multi-factor-authentication-auth-app-verification-test.png)

7. A számítógépen adja hozzá a mobileszköz telefonszámát a **3. lépés: abban az esetben, ha elveszti a hozzáférést a Mobile App (alkalmazás** ) területen, majd kattintson a **tovább** gombra.

    Javasoljuk, hogy a mobileszköz-telefonszámot úgy adja hozzá, hogy biztonsági mentést végezzen, ha bármilyen okból nem fér hozzá a Mobile alkalmazáshoz, vagy nem használhatja azt.

8. A **4. lépés: a meglévő alkalmazások használatának megtartása** területen másolja a megadott alkalmazás jelszavát, és illessze be biztonságosba.

    ![A további biztonsági ellenőrzés lap alkalmazások jelszavai területe](media/multi-factor-authentication-verification-methods/multi-factor-authentication-app-passwords.png)

    >[!Note]
    >További információ az alkalmazás jelszavának régebbi alkalmazásokkal való használatáról: [alkalmazások jelszavainak kezelése](multi-factor-authentication-end-user-app-passwords.md). Csak akkor kell használnia az alkalmazáshoz tartozó jelszavakat, ha továbbra is olyan régebbi alkalmazásokat használ, amelyek nem támogatják a kétfaktoros ellenőrzést.

9. Válassza a **Kész** lehetőséget.

## <a name="next-steps"></a>Következő lépések

A kétfaktoros ellenőrzési módszer beállítása után további metódusokat adhat hozzá, kezelheti a beállításokat és az alkalmazás jelszavait, bejelentkezhet, vagy segítséget kérhet a kétfaktoros ellenőrzésekkel kapcsolatos gyakori problémákkal kapcsolatban.

- [A kétfaktoros ellenőrzési módszer beállításainak kezelése](multi-factor-authentication-end-user-manage-settings.md)

- [Alkalmazásjelszavak kezelése](multi-factor-authentication-end-user-app-passwords.md)

- [Bejelentkezés kétfaktoros ellenőrzés használatával](multi-factor-authentication-end-user-signin.md)

- [Segítség kérése kétfaktoros ellenőrzéssel](multi-factor-authentication-end-user-troubleshoot.md)
