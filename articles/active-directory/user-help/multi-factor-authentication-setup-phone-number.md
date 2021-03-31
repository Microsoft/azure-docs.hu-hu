---
title: Mobileszköz beállítása kétfaktoros ellenőrzési módszerként – Azure Active Directory | Microsoft Docs
description: Megtudhatja, hogyan állíthatja be a mobileszköz kétfaktoros ellenőrzési módszerét.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 08/12/2019
ms.author: curtand
ms.openlocfilehash: 40af9ac692a226b12f984db447db9635cba655e4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91530255"
---
# <a name="set-up-a-mobile-device-as-your-two-factor-verification-method"></a>Mobileszköz beállítása kétfaktoros ellenőrzési módszerként

Beállíthatja, hogy a mobileszköz a kétfaktoros ellenőrzési módszerként működjön. A mobiltelefonon szöveges üzenetet is kaphat ellenőrző kóddal vagy telefonhívással.

>[!Note]
> Ha a hitelesítő telefon lehetőség szürkén jelenik meg, lehetséges, hogy a szervezet nem teszi lehetővé a telefonszám vagy SMS-üzenet használatát az ellenőrzéshez. Ebben az esetben ki kell választania egy másik módszert, vagy kapcsolatba kell lépnie a rendszergazdával további segítségért.

## <a name="set-up-your-mobile-device-to-use-a-text-message-as-your-verification-method"></a>A mobileszköz beállítása szöveges üzenet használatára ellenőrzési módszerként

1. A **további biztonsági ellenőrzés** lapon válassza az **1. lépés:** a **hitelesítési telefon** lehetőséget, majd válassza ki az országot vagy régiót a legördülő listából, majd írja be a mobileszköz telefonszámát.

2. Válassza a **kód küldése SMS-üzenet** alapján lehetőséget a **metódus** területen, majd kattintson a **tovább** gombra.

    ![A "további biztonsági ellenőrzés" oldalt megjelenítő képernyőkép, amely a "hitelesítés telefon" és a "Küldés SMS-ként" üzenetet jeleníti meg.](media/multi-factor-authentication-verification-methods/multi-factor-authentication-text-message.png)

3. Írja be az ellenőrző kódot a Microsoft által küldött szöveges üzenetből a **2. lépés: elküldtem egy szöveges üzenetet a telefon** területére, majd válassza az **ellenőrzés** lehetőséget.

    ![További biztonsági ellenőrzés oldal, hitelesítő telefonnal és szöveges üzenettel](media/multi-factor-authentication-verification-methods/multi-factor-authentication-text-message-test.png)

4. A **3. lépés: a meglévő alkalmazások használatának megtartása** területen másolja a megadott alkalmazás jelszavát, és illessze be biztonságosba.

    ![A további biztonsági ellenőrzés lap alkalmazások jelszavai területe](media/multi-factor-authentication-verification-methods/multi-factor-authentication-app-passwords.png)

    >[!Note]
    >További információ az alkalmazás jelszavának régebbi alkalmazásokkal való használatáról: [alkalmazások jelszavainak kezelése](multi-factor-authentication-end-user-app-passwords.md). Csak akkor kell használnia az alkalmazáshoz tartozó jelszavakat, ha továbbra is olyan régebbi alkalmazásokat használ, amelyek nem támogatják a kétfaktoros ellenőrzést.

5. Válassza a **Kész** lehetőséget.

## <a name="set-up-your-mobile-device-to-receive-a-phone-call"></a>A mobileszköz beállítása telefonos hívás fogadására

1. A **további biztonsági ellenőrzés** lapon válassza az **1. lépés:** a **hitelesítési telefon** lehetőséget, majd válassza ki az országot vagy régiót a legördülő listából, majd írja be a mobileszköz telefonszámát.

2. Válassza a **metódus** területen a **hívjon** lehetőséget, majd kattintson a **tovább** gombra.

    ![További biztonsági ellenőrzési oldal hitelesítéssel telefon és telefonhívás](media/multi-factor-authentication-verification-methods/multi-factor-authentication-phone-call.png)

3. Telefonhívást fog kapni a Microsofttól, és meg kell kérnie, hogy az Ön személyazonosságának ellenőrzéséhez megnyomja a font (#) jelet a mobileszközön.

    ![A megadott telefonszám tesztelése](media/multi-factor-authentication-verification-methods/multi-factor-authentication-phone-call-test.png)

4. A **3. lépés: a meglévő alkalmazások használatának megtartása** területen másolja a megadott alkalmazás jelszavát, és illessze be biztonságosba.

    ![A további biztonsági ellenőrzés lap alkalmazások jelszavai területe](media/multi-factor-authentication-verification-methods/multi-factor-authentication-app-passwords.png)

    >[!Note]
    >További információ az alkalmazás jelszavának régebbi alkalmazásokkal való használatáról: [alkalmazások jelszavainak kezelése](multi-factor-authentication-end-user-app-passwords.md). Csak akkor kell használnia az alkalmazáshoz tartozó jelszavakat, ha továbbra is olyan régebbi alkalmazásokat használ, amelyek nem támogatják a kétfaktoros ellenőrzést.

5. Válassza a **Kész** lehetőséget.

## <a name="next-steps"></a>Következő lépések

A kétfaktoros ellenőrzési módszer beállítása után további metódusokat adhat hozzá, kezelheti a beállításokat és az alkalmazás jelszavait, bejelentkezhet, vagy segítséget kérhet a kétfaktoros ellenőrzésekkel kapcsolatos gyakori problémákkal kapcsolatban.

- [A kétfaktoros ellenőrzési módszer beállításainak kezelése](multi-factor-authentication-end-user-manage-settings.md)

- [Alkalmazásjelszavak kezelése](multi-factor-authentication-end-user-app-passwords.md)

- [Bejelentkezés kétfaktoros ellenőrzés használatával](multi-factor-authentication-end-user-signin.md)

- [Segítség kérése kétfaktoros ellenőrzéssel](multi-factor-authentication-end-user-troubleshoot.md)
