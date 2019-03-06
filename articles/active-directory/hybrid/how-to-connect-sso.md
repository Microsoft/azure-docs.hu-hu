---
title: 'Azure AD Connect: Zökkenőmentes egyszeri bejelentkezést |} A Microsoft Docs'
description: Ez a témakör ismerteti az Azure Active Directory (Azure AD) zökkenőmentes egyszeri bejelentkezést, és hogyan teszi lehetővé vállalati asztali gépek felhasználóinak a vállalati hálózaton belüli valódi egyszeri bejelentkezést biztosít.
services: active-directory
keywords: Mi az Azure AD Connect, Active Directory telepítése szükséges összetevők SSO, Azure AD egyszeri bejelentkezés
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 00d0332e44b76cc3b883fd0472567f74cfde21b2
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57444973"
---
# <a name="azure-active-directory-seamless-single-sign-on"></a>Az Azure Active Directory zökkenőmentes egyszeri bejelentkezés

## <a name="what-is-azure-active-directory-seamless-single-sign-on"></a>Mi az Azure Active Directory zökkenőmentes egyszeri bejelentkezés?

Az Azure Active Directory közvetlen egyszeri bejelentkezése (Azure AD közvetlen SSO) automatikusan bejelentkezteti a felhasználókat, ha azok a vállalati hálózatra csatlakozó vállalati eszközeiket használják. Ha engedélyezve van, a felhasználók nem kell írja be az jelszavait, hogy jelentkezzen be Azure ad-ben, és általában, még akkor is írja a felhasználónevek. A szolgáltatás használatával a felhasználók könnyen elérhetik a felhőalapú alkalmazásokat anélkül, hogy ehhez további helyszíni összetevőkre lenne szükség.

>[!VIDEO https://www.youtube.com/embed/PyeAC85Gm7w]

Közvetlen egyszeri bejelentkezés kombinálva, vagy a [Jelszókivonat-szinkronizálás](how-to-connect-password-hash-synchronization.md) vagy [átmenő hitelesítés](how-to-connect-pta.md) bejelentkezési módszereket. Közvetlen egyszeri bejelentkezés van _nem_ alkalmazható az Active Directory összevonási szolgáltatások (ADFS).

![Közvetlen egyszeri bejelentkezés](./media/how-to-connect-sso/sso1.png)

>[!IMPORTANT]
>Közvetlen egyszeri bejelentkezés kell lennie a felhasználó eszközén **tartományhoz csatlakoztatott**, de nem szükséges lehet az eszköz [Azure AD-csatlakoztatás](../active-directory-azureadjoin-overview.md).

## <a name="key-benefits"></a>Főbb előnyök

- *Nagyszerű felhasználói élmény*
  - Felhasználók automatikusan bejelentkezett a helyszíni és felhőbeli alkalmazásokat is.
  - Adja meg ismételten a jelszavukat a felhasználóknak nem kell.
- *Egyszerű üzembe helyezése és felügyelete*
  - További összetevők szükséges a helyszíni működnek.
  - A felhőalapú hitelesítés – bármely metódusát együttműködik [Jelszókivonat-szinkronizálás](how-to-connect-password-hash-synchronization.md) vagy [átmenő hitelesítés](how-to-connect-pta.md).
  - Egyes is történni vagy csoportházirend segítségével minden felhasználó.
  - A Windows 10-eszközök regisztrálása az Azure ad-vel bármely AD FS-infrastruktúra nélkül. Ez a funkció csak 2.1-es vagy újabb verzióját használja a [munkahelyhez való csatlakozás ügyfél](https://www.microsoft.com/download/details.aspx?id=53554).

## <a name="feature-highlights"></a>A szolgáltatás emeli ki.

- Bejelentkezési felhasználónév vagy a helyszíni alapértelmezett felhasználónév lehet (`userPrincipalName`) vagy egy másik attribútum konfigurálva az Azure AD Connectben (`Alternate ID`). Mindkét esetben munkahelyi használható, mert a zökkenőmentes egyszeri Bejelentkezést használ az `securityIdentifier` keresse ki a megfelelő felhasználói objektum az Azure AD-ben a Kerberos-jegy jogcím.
- Közvetlen egyszeri bejelentkezés az alkalmi szolgáltatása. Ha bármilyen okból nem sikerül, a felhasználói bejelentkezési élmény kerül vissza a normál viselkedési – azaz, a felhasználó kell a jelszavát adja meg a bejelentkezési oldalon.
- Ha egy alkalmazás (például https://myapps.microsoft.com/contoso.com) továbbítja egy `domain_hint` (OpenID Connect) vagy `whr` (SAML) paraméter – a bérlői azonosító vagy `login_hint` paraméter – a felhasználói azonosító az Azure AD bejelentkezési kérés esetén a felhasználók vannak automatikusan megtörténik a őket belépés felhasználónevek vagy jelszavak nélkül.
- A felhasználók is kapnak a beavatkozás nélküli bejelentkezést, ha egy alkalmazás (például https://contoso.sharepoint.com) bejelentkezési kéréseket küld a bérlők – vagyis állíthatja be az Azure AD-végpontokra irányuló https://login.microsoftonline.com/contoso.com/<..> vagy https://login.microsoftonline.com/<tenant_ID>/<..> – helyett az Azure AD közös végpont – vagyis https://login.microsoftonline.com/common/<...>.
- Jelentkezzen ki támogatott. Ez lehetővé teszi a felhasználók kiválaszthatják a bejelentkezéshez, automatikus bejelentkezés folyamatban közvetlen egyszeri bejelentkezés használatával automatikusan helyett egy másik Azure AD-fiókot.
- Office 365 Win32-ügyfelek (Outlook, Word, Excel és mások) vagy újabb verzió 16.0.8730.xxxx rendelkező nem interaktív folyamat használatával támogatott. A onedrive-on, aktiválnia kell a [OneDrive csendes config funkció](https://techcommunity.microsoft.com/t5/Microsoft-OneDrive-Blog/Previews-for-Silent-Sync-Account-Configuration-and-Bandwidth/ba-p/120894) csendes bejelentkezési élményt.
- Az Azure AD Connect használatával engedélyezhető.
- Egy ingyenes szolgáltatás, és nem kell minden fizetős kiadásban az Azure AD használatát.
- A webes böngésző alapú ügyfelek és az Office-ügyfelek, amelyek támogatják a támogatott [modern hitelesítést](https://docs.microsoft.com/office365/enterprise/modern-auth-for-office-2013-and-2016) platformon és böngészők képes a Kerberos-hitelesítés:

| OS\Browser |Internet Explorer|Microsoft Edge|Google Chrome|Mozilla Firefox|Safari|
| --- | --- |--- | --- | --- | -- 
|Windows 10|igen\*|Nem|Igen|igen\*\*\*|–
|Windows 8.1|igen\*|–|Igen|igen\*\*\*|–
|Windows 8|igen\*|–|Igen|igen\*\*\*|–
|Windows 7|igen\*|–|Igen|igen\*\*\*|–
|A Windows Server 2012 R2 vagy újabb|igen\*\*|–|Igen|igen\*\*\*|–
|Mac OS X|–|–|igen\*\*\*|igen\*\*\*|igen\*\*\*


\*Igényli az Internet Explorer verzióinak 10-es vagy újabb

\*\*Igényli az Internet Explorer verzióinak 10-es vagy újabb. Tiltsa le, kibővített védett mód

\*\*\*Szükséges [további konfigurációs](how-to-connect-sso-quick-start.md#browser-considerations)

>[!NOTE]
>A Windows 10 esetén ajánlott használni [az Azure AD Join](../active-directory-azureadjoin-overview.md) az optimális egyszeri bejelentkezést az Azure AD számára.

## <a name="next-steps"></a>További lépések

- [**Gyors üzembe helyezési** ](how-to-connect-sso-quick-start.md) – és az Azure AD közvetlen egyszeri bejelentkezés futtatása.
- [**Telepítési terv** ](https://aka.ms/AuthenticationDeploymentPlan) – részletes telepítési tervét.
- [**Részletes technikai** ](how-to-connect-sso-how-it-works.md) – Ez a funkció működésének megismerése.
- [**Gyakran ismételt kérdések** ](how-to-connect-sso-faq.md) – a gyakran feltett kérdésekre adott válaszok.
- [**Hibaelhárítás** ](tshoot-connect-sso.md) – ismerje meg, a szolgáltatással kapcsolatos gyakori problémák megoldása.
- [**UserVoice** ](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) – új funkcióra vonatkozó javaslata tárolásához.

