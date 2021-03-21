---
title: Külső identitások identitás-szolgáltatói – Azure AD
description: Ismerje meg, hogyan használhatja az Azure AD-t alapértelmezett identitás-szolgáltatóként a külső felhasználókkal való megosztáshoz.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 03/02/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.collection: M365-identity-device-management
ms.openlocfilehash: bdef929b27c636b3908dd7a88eb93adc2382a53f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101687743"
---
# <a name="identity-providers-for-external-identities"></a>Külső identitások identitás-szolgáltatói

> [!NOTE]
> A cikkben említett funkciók némelyike Azure Active Directory nyilvános előzetes verziójú funkciói. További információ az előzetes verziókról: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Az *identitás-szolgáltató* identitási adatokat hoz létre, tart karban és felügyel, miközben hitelesítési szolgáltatásokat biztosít az alkalmazásoknak. Az alkalmazások és az erőforrások külső felhasználókkal való megosztásakor az Azure AD a megosztás alapértelmezett identitás-szolgáltatója. Ez azt jelenti, hogy ha olyan külső felhasználókat nyit meg, akik már rendelkeznek Azure AD-vel vagy Microsoft-fiókval, automatikusan bejelentkezhetnek a rész további konfigurálása nélkül.

Az Azure AD-fiókokon kívül a külső identitások számos különböző identitás-szolgáltatót kínálnak.

- **Microsoft-fiókok** (előzetes verzió): a vendég felhasználók a saját személyes Microsoft-fiók (MSA) HASZNÁLHATJÁK a B2B együttműködési meghívásokat. Önkiszolgáló bejelentkezési felhasználói folyamat beállításakor a [Microsoft-fiókot (előzetes verzió)](microsoft-account.md) hozzáadhatja az egyik engedélyezett identitás-szolgáltatóhoz. Nincs szükség további konfigurálásra ahhoz, hogy ez az identitás-szolgáltató elérhető legyen a felhasználói folyamatok számára.

- **Egyszeri e-mail-jelszó** (előzetes verzió): meghívások beváltása vagy megosztott erőforráshoz való hozzáférés esetén a vendég felhasználó egy ideiglenes kódot kérhet, amelyet a rendszer az e-mail-címére küld. Ezután a kód beírásával folytathatja a bejelentkezést. Az egyszeri e-mail-jelszó funkció hitelesíti a B2B vendég felhasználókat, ha más módon nem hitelesíthetők. Az önkiszolgáló bejelentkezési felhasználói folyamat beállításakor a rendszer **e-mail-One-Time PIN-kódot (előzetes verzió)** adhat hozzá az egyik engedélyezett identitás-szolgáltatóként. Bizonyos beállítások megadása kötelező; Tekintse meg az [e-mailek egyszeri jelszavas hitelesítését](one-time-passcode.md).

- **Google**: a Google Federation lehetővé teszi, hogy a külső felhasználók meghívókat váltsanak be az alkalmazásaiba saját Gmail-fiókkal való bejelentkezéssel. A Google Federation is használható az önkiszolgáló bejelentkezési felhasználói folyamatokban. Tekintse meg a [Google identitás-szolgáltatóként való hozzáadását](google-federation.md)ismertető témakört.
   > [!IMPORTANT]
   > **2021. január 4-én kezdődően** a Google [elavult webnézet-bejelentkezési támogatást jelenít meg](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html). Ha Google-összevonást vagy önkiszolgáló regisztrációt használ a Gmail szolgáltatással, az üzletági [natív alkalmazásokat tesztelje a kompatibilitás](google-federation.md#deprecation-of-webview-sign-in-support)érdekében.

- **Facebook**: egy alkalmazás létrehozásakor konfigurálhatja az önkiszolgáló regisztrációt, és engedélyezheti a Facebook-összevonás használatát, hogy a felhasználók saját Facebook-fiókjaik használatával regisztrálhatják az alkalmazást. A Facebook csak önkiszolgáló bejelentkezési felhasználói folyamatokhoz használható, és nem érhető el bejelentkezési lehetőségként, ha a felhasználók meghívókat váltanak ki. Tekintse meg a [Facebook identitás-szolgáltatóként való hozzáadását](facebook-federation.md)ismertető témakört.

- **Közvetlen összevonás**: az SAML-vagy WS-Fed-protokollt támogató külső identitás-szolgáltatóval is beállíthat közvetlen összevonást. A közvetlen összevonás lehetővé teszi, hogy a külső felhasználók meghívókat váltsanak be az alkalmazásaiba a meglévő közösségi vagy vállalati fiókkal való bejelentkezéssel. Lásd: a [közvetlen összevonás beállítása](direct-federation.md).
   > [!NOTE]
   > A közvetlen összevonási azonosítók nem használhatók önkiszolgáló bejelentkezési felhasználói folyamatokban.

## <a name="adding-social-identity-providers"></a>Közösségi identitás-szolgáltatók hozzáadása

Az Azure AD alapértelmezés szerint engedélyezve van az önkiszolgáló regisztrációhoz, így a felhasználóknak mindig lehetősége van regisztrálni egy Azure AD-fiók használatával. Azonban engedélyezheti más identitás-szolgáltatókat, például a közösségi identitás-szolgáltatókat, például a Google-t vagy a Facebookot. A közösségi identitás-szolgáltatók Azure AD-bérlőben történő beállításához létre kell hoznia egy alkalmazást az identitás-szolgáltatónál, és konfigurálnia kell a hitelesítő adatokat. Szerezze be az ügyfél vagy az alkalmazás AZONOSÍTÓját, valamint az ügyfél vagy az alkalmazás titkos kulcsát, amelyet aztán hozzáadhat az Azure AD-bérlőhöz.

Miután hozzáadta az identitás-szolgáltatót az Azure AD-bérlőhöz:

- Ha egy külső felhasználót meghív a szervezet alkalmazásaira vagy erőforrásaira, a külső felhasználó a saját fiókját használva tud bejelentkezni az adott identitás-szolgáltatóval.
- Ha engedélyezi az [önkiszolgáló regisztrációt](self-service-sign-up-overview.md) az alkalmazásaihoz, a külső felhasználók a saját fiókjaikat a hozzáadott Identity Providers használatával regisztrálhatják az alkalmazásaikban. A regisztrációs oldalon elérhetővé tett közösségi identitás-szolgáltatók közül választhatnak:

   ![A bejelentkezési képernyőt a Google és a Facebook lehetőségeivel ábrázoló képernyőkép](media/identity-providers/sign-in-with-social-identity.png)

Az optimális bejelentkezési élmény érdekében a összevonása, amikor csak lehetséges, a meghívott vendégeknek zökkenőmentes bejelentkezési élményt biztosíthat az alkalmazásai eléréséhez.  

## <a name="next-steps"></a>Következő lépések

Az alábbi cikkekből megtudhatja, hogyan adhat identitás-szolgáltatókat az alkalmazásaiba való bejelentkezéshez:

- [E-mail egyszeri jelszavas hitelesítésének hozzáadása](one-time-passcode.md)
- [Google hozzáadása](google-federation.md) engedélyezett közösségi identitás-szolgáltatóként
- [Facebook hozzáadása](facebook-federation.md) engedélyezett közösségi identitás-szolgáltatóként
- [Közvetlen összevonás beállítása](direct-federation.md) bármely olyan szervezettel, amelynek az identitás-szolgáltatója támogatja az SAML 2,0 vagy WS-Fed protokollt. Vegye figyelembe, hogy a közvetlen összevonás nem választható az önkiszolgáló bejelentkezési felhasználói folyamatok számára.
