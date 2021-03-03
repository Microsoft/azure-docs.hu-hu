---
title: ESKÜ-tokenek hitelesítési módszere – Azure Active Directory
description: További információ az eskü-tokenek Azure Active Directoryban való használatáról a bejelentkezési események javítása és biztonságossá tétele érdekében
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: justinha
author: justinha
manager: daveba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2270ff360c7bb923555c9b4ffb0c35ccd4382d0e
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101647489"
---
# <a name="authentication-methods-in-azure-active-directory---oath-tokens"></a>Hitelesítési módszerek a Azure Active Directory-eskü-tokenekben

Az eskü TOTP (időalapú egyszeri jelszó) egy nyílt szabvány, amely meghatározza, hogy az egyszeri jelszavas (OTP) kódok hogyan jönnek létre. Az eskü TOTP a kódok létrehozásához szoftver vagy hardver használatával valósítható meg. Az Azure AD nem támogatja az eskü HOTP, amely egy másik kód generálási szabvány.

## <a name="oath-software-tokens"></a>Az eskü szoftver jogkivonatai

A szoftveres eskü-tokenek jellemzően olyan alkalmazások, mint a Microsoft Authenticator alkalmazás és más hitelesítő alkalmazások. Az Azure AD létrehozza a titkos kulcsot, vagy a magot, amely az alkalmazásba belép, és az egyes OTP-k létrehozásához használatos.

A hitelesítő alkalmazás automatikusan generál kódokat, ha úgy állítja be a leküldéses értesítéseket, hogy egy felhasználó biztonsági másolattal rendelkezzen, még akkor is, ha az eszköz nem rendelkezik kapcsolattal. A más gyártóktól származó, ESKÜt használó alkalmazások is használhatók TOTP létrehozásához.

Néhány ESKÜt TOTP, ami azt jelenti, hogy a hardver-jogkivonatok programozhatók, ezért nem tartoznak hozzá előre programozott titkos kulcs vagy mag. Ezek a programozható hardveres tokenek a szoftver-jogkivonat telepítési folyamata által beszerzett titkos kulccsal vagy mag használatával állíthatók be. Az ügyfelek vásárolhatják meg ezeket a jogkivonatokat a választott gyártótól, és a titkos kulcsot vagy magot használják a gyártó telepítési folyamatában.

## <a name="oath-hardware-tokens"></a>OATH hardveres jogkivonatok

Az Azure AD az eskü-TOTP SHA-1 tokenek használatát támogatja, amelyek 30 vagy 60 másodpercenként frissítik a kódokat. Az ügyfelek megvásárolhatják ezeket a jogkivonatokat a választott gyártótól.

Az eskü TOTP-tokenek általában titkos kulccsal, vagy a tokenben előre programozott magokkal rendelkeznek. Ezeket a kulcsokat az alábbi lépésekben leírtaknak megfelelően be kell állítani az Azure AD-be. A titkos kulcsok legfeljebb 128 karakterből állhatnak, amelyek nem kompatibilisek az összes jogkivonattal. A titkos kulcs csak az *a-z* , a-z és *a-z* , valamint a *2-7* karakterből állhat, és a *Base32*-ben kell kódolni.

A feldolgozható, programozható eskü TOTP a szoftveres jogkivonat telepítési folyamatában is beállítható az Azure AD-ben.

![ESKÜ-tokenek feltöltése az MFA-eskü tokenek paneljére](media/concept-authentication-methods/mfa-server-oath-tokens-azure-ad.png)

A jogkivonatok beszerzése után az alábbi példában látható módon fel kell tölteni egy vesszővel tagolt (CSV) fájlformátumot, beleértve az UPN-t, a sorozatszámot, a titkos kulcsot, az időintervallumot, a gyártót és a modellt.

```csv
upn,serial number,secret key,time interval,manufacturer,model
Helga@contoso.com,1234567,2234567abcdef1234567abcdef,60,Contoso,HardwareKey
```  

> [!NOTE]
> Ügyeljen rá, hogy a fejlécsor szerepeljen a CSV-fájlban. Ha egy egyszerű felhasználónév egyetlen idézettel rendelkezik, akkor egy másik idézőjelbe kell kerülnie. Ha például az egyszerű felhasználónév a " user@domain.com , akkor módosítsa a saját" "értékre user@domain.com a fájl feltöltésekor.

Miután megfelelően formázott CSV-fájlként, a rendszergazda bejelentkezhet a Azure Portalba, és megnyithatja **Azure Active Directory > biztonsági > MFA > eskü-tokeneket**, és feltöltheti az eredményül kapott CSV-fájlt.

A CSV-fájl méretétől függően a folyamat eltarthat néhány percig. Kattintson a **frissítés** gombra az aktuális állapot lekéréséhez. Ha bármilyen hiba van a fájlban, letöltheti azt a CSV-fájlt, amely felsorolja a feloldható hibákat. A letöltött CSV-fájl mezőinek neve eltér a feltöltött verziótól.  

A hibák elhárítása után a rendszergazda ezután aktiválhatja az egyes kulcsokat, ha a jogkivonat **aktiválása** elemre kattint, és beírja a tokenen megjelenő OTP elemet. 5 percenként legfeljebb 200 ESKÜs tokent lehet aktiválni. 

Előfordulhat, hogy a felhasználók legfeljebb öt ESKÜvel rendelkező hardver-tokent vagy hitelesítő alkalmazást (például a Microsoft Authenticator alkalmazást) kombinálnak, amelyet bármikor használatra konfiguráltak.

## <a name="next-steps"></a>Következő lépések

További információ a hitelesítési módszerek konfigurálásáról a [Microsoft Graph REST API Beta](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta)használatával.
