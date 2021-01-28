---
title: 'Azure AD Connect: zökkenőmentes egyetlen Sign-On | Microsoft Docs'
description: Ez a témakör Azure Active Directory (Azure AD) zökkenőmentes önálló Sign-Onét ismerteti, valamint azt, hogy a vállalati hálózaton belül hogyan biztosíthat valódi egyszeri bejelentkezést a vállalati asztali felhasználók számára.
services: active-directory
keywords: Mi az Azure AD Connect, az Azure AD, az egyszeri bejelentkezéshez szükséges összetevők telepítése Active Directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/13/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 88eae702782e2f1af9c20797676214db458c2adc
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98937621"
---
# <a name="azure-active-directory-seamless-single-sign-on"></a>Közvetlen egyszeri bejelentkezés az Azure Active Directoryba

## <a name="what-is-azure-active-directory-seamless-single-sign-on"></a>Mi az Azure Active Directory zökkenőmentes egyszeri bejelentkezés?

Az Azure Active Directory közvetlen egyszeri bejelentkezése (Azure AD közvetlen SSO) automatikusan bejelentkezteti a felhasználókat, ha azok a vállalati hálózatra csatlakozó vállalati eszközeiket használják. Ha ez a beállítás engedélyezve van, a felhasználóknak nem kell beírniuk a jelszavukat, hogy bejelentkezzenek az Azure AD-be, és általában még begépelik a felhasználóneveket. A szolgáltatás használatával a felhasználók könnyen elérhetik a felhőalapú alkalmazásokat anélkül, hogy ehhez további helyszíni összetevőkre lenne szükség.

>[!VIDEO https://www.youtube.com/embed/PyeAC85Gm7w]

A zökkenőmentes egyszeri bejelentkezést a jelszó- [kivonatoló szinkronizálással](how-to-connect-password-hash-synchronization.md) vagy az [átmenő hitelesítési](how-to-connect-pta.md) bejelentkezési módszerekkel kombinálhatja. A zökkenőmentes SSO _nem_ alkalmazható a Active Directory ÖSSZEVONÁSI szolgáltatások (AD FS) (ADFS) szolgáltatásra.

![Zökkenőmentes egyszeri Sign-On](./media/how-to-connect-sso/sso1.png)

## <a name="sso-via-primary-refresh-token-vs-seamless-sso"></a>Egyszeri bejelentkezés az elsődleges frissítési jogkivonat és a zökkenőmentes SSO használatával

A Windows 10 esetében ajánlott az egyszeri bejelentkezést használni az elsődleges frissítési jogkivonat (PRT) használatával. Windows 7 és 8,1 esetén a zökkenőmentes SSO használatát javasoljuk.
A zökkenőmentes egyszeri bejelentkezéshez a felhasználó eszközének tartományhoz csatlakoztatottnak kell lennie, de a Windows 10 [Azure ad-hez csatlakoztatott eszközökön](../devices/concept-azure-ad-join.md) vagy a [hibrid Azure ad-hez csatlakoztatott eszközökön](../devices/concept-azure-ad-join-hybrid.md)nem használható. Egyszeri bejelentkezés az Azure AD-hez csatlakoztatott, hibrid Azure AD-hez csatlakoztatott és az Azure AD által regisztrált eszközök az [elsődleges frissítési jogkivonat (PRT)](../devices/concept-primary-refresh-token.md) alapján működnek

Egyszeri bejelentkezés a PRT-n keresztül, ha az eszközök regisztrálva vannak az Azure AD-ben a hibrid Azure AD-hez, az Azure AD-hez csatlakoztatott vagy a személyes regisztrált eszközökhöz munkahelyi vagy iskolai fiókkal. További információ arról, hogyan működik az SSO a PRT használatával a Windows 10 rendszerben: [elsődleges frissítési jogkivonat (PRT) és Azure ad](../devices/concept-primary-refresh-token.md)


## <a name="key-benefits"></a>Főbb előnyök

- *Nagyszerű felhasználói élmény*
  - A felhasználók automatikusan bejelentkeznek a helyszíni és a felhőalapú alkalmazásokba is.
  - A felhasználóknak nem kell ismételten megadniuk a jelszavukat.
- *Egyszerűen üzembe helyezhető & felügyelet*
  - Ehhez a működéshez nincs szükség a helyszínen további összetevőkre.
  - Bármilyen módszerrel használható a felhőalapú hitelesítéshez – [jelszó-kivonatolási szinkronizálás](how-to-connect-password-hash-synchronization.md) vagy [átmenő hitelesítés](how-to-connect-pta.md).
  - A Csoportházirend használatával egy vagy több felhasználóhoz is beállíthatók.
  - A nem Windows 10-es eszközök regisztrálása az Azure AD-vel anélkül, hogy AD FS infrastruktúrára lenne szükség. Ehhez a funkcióhoz a [munkahelyi csatlakoztatási ügyfél](https://www.microsoft.com/download/details.aspx?id=53554)2,1-es vagy újabb verzióját kell használnia.

## <a name="feature-highlights"></a>A funkciók Kiemelt jellemzői

- A bejelentkezési Felhasználónév lehet a helyszíni alapértelmezett Felhasználónév ( `userPrincipalName` ) vagy a Azure ad Connect () alkalmazásban konfigurált másik attribútum `Alternate ID` . Mindkét használati eset működik, mivel a zökkenőmentes egyszeri bejelentkezés a `securityIdentifier` Kerberos-jegyben lévő jogcímet használja a megfelelő felhasználói objektum kereséséhez az Azure ad-ben.
- A zökkenőmentes SSO egy opportunista funkció. Ha bármilyen okból nem sikerül, a felhasználói bejelentkezési élmény visszatér a szokásos viselkedésére – azaz a felhasználónak meg kell adnia a jelszavát a bejelentkezési oldalon.
- Ha egy alkalmazás (például:  `https://myapps.microsoft.com/contoso.com` ) továbbít egy `domain_hint` (OpenID Connect) vagy `whr` (SAML) paramétert – a bérlő azonosítására, vagy `login_hint` a felhasználó azonosítására szolgáló paramétert – az Azure ad bejelentkezési kérelmében a felhasználók automatikusan bejelentkeznek a felhasználónevek és jelszavak beírása nélkül.
- A felhasználók akkor is csendes bejelentkezési élményt kapnak, ha egy alkalmazás (például: `https://contoso.sharepoint.com` ) a bejelentkezési kéréseket az Azure ad azon végpontjai számára állítja be, amelyek bérlőként vannak beállítva – azaz az `https://login.microsoftonline.com/contoso.com/<..>` `https://login.microsoftonline.com/<tenant_ID>/<..>` Azure ad közös végpontja – azaz `https://login.microsoftonline.com/common/<...>` .
- A kijelentkezés támogatott. Így a felhasználók kiválaszthatnak egy másik Azure AD-fiókot a szolgáltatásba való bejelentkezéshez ahelyett, hogy automatikusan be kellene jelentkezniük a zökkenőmentes egyszeri bejelentkezés automatikus használatával.
- A 16.0.8730. xxxx és újabb verziókkal rendelkező Win32-ügyfelek (Outlook, Word, Excel és egyebek) nem interaktív folyamat használatával támogatottak. Microsoft 365 A OneDrive a csendes bejelentkezési élmény érdekében aktiválni kell a [OneDrive csendes konfigurációs szolgáltatást](https://techcommunity.microsoft.com/t5/Microsoft-OneDrive-Blog/Previews-for-Silent-Sync-Account-Configuration-and-Bandwidth/ba-p/120894) .
- Azure AD Connect használatával engedélyezhető.
- Ez egy ingyenes funkció, és nincs szüksége az Azure AD fizetős kiadásaira a használatához.
- A szolgáltatás olyan webböngésző-alapú ügyfelek és Office-ügyfelek számára támogatott, amelyek támogatják a [modern hitelesítést](/office365/enterprise/modern-auth-for-office-2013-and-2016) a Kerberos-hitelesítésre képes platformokon és böngészőkön:

| OS\Browser |Internet Explorer|Microsoft Edge|Google Chrome|Mozilla Firefox|Safari|
| --- | --- |--- | --- | --- | -- 
|Windows 10|Igen\*|Igen|Yes|Igen\*\*\*|N/A
|Windows 8.1|Yes\*|Igen\*\*\*|Yes|Igen\*\*\*|N/A
|Windows 8|Yes\*|N.A.|Igen|Igen\*\*\*|N/A
|Windows 7|Yes\*|N.A.|Igen|Igen\*\*\*|N/A
|Windows Server 2012 R2 vagy újabb|igen\*\*|N.A.|Igen|Igen\*\*\*|N/A
|Mac OS X|N.A.|N.A.|Igen\*\*\*|Igen\*\*\*|Igen\*\*\*


\*Az Internet Explorer 10-es vagy újabb verzióját igényli.

\*\*Az Internet Explorer 10-es vagy újabb verzióját igényli. Fokozottan védett üzemmód letiltása.

\*\*\*[További konfigurálást](how-to-connect-sso-quick-start.md#browser-considerations)igényel.

\*\*\*\*A Microsoft Edge 77-es vagy újabb verziójára van szükség.

## <a name="next-steps"></a>Következő lépések

- [**Gyorskonfigurálás**](how-to-connect-sso-quick-start.md) – az Azure ad zökkenőmentes egyszeri bejelentkezésének megkezdése és futtatása.
- [**Üzembe helyezési terv**](../manage-apps/plan-sso-deployment.md) – lépésenkénti üzembe helyezési terv.
- [**Technikai**](how-to-connect-sso-how-it-works.md) részletes útmutató – a funkció működésének megismerése.
- [**Gyakori kérdések**](how-to-connect-sso-faq.md) – válaszok a gyakori kérdésekre.
- [**Hibaelhárítás**](tshoot-connect-sso.md) – megismerheti a szolgáltatással kapcsolatos gyakori problémák megoldását.
- [**UserVoice**](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) – új szolgáltatásokra vonatkozó kérelmek bejelentése.
