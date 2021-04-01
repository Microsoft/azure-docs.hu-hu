---
title: Mi a hibrid identitás a Azure Active Directory?
description: A hibrid identitás közös felhasználói identitással rendelkezik a hitelesítéshez és az engedélyezéshez mind a helyszínen, mind a felhőben.
keywords: az Azure AD Connect bemutatása, az Azure AD Connect áttekintése, mi az Azure AD Connect, az Active Directory telepítése
services: active-directory
author: billmath
manager: daveba
ms.assetid: 59bd209e-30d7-4a89-ae7a-e415969825ea
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 05/17/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 895355b5acebffe6ad24b15b0c709d21e8f20be4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "90016648"
---
# <a name="what-is-hybrid-identity-with-azure-active-directory"></a>Mi a hibrid identitás a Azure Active Directory?

Napjainkban a vállalatok és a vállalatok egyre többen használják a helyszíni és a felhőalapú alkalmazásokat.  A felhasználóknak a helyszíni és a Felhőbeli alkalmazásokhoz is hozzá kell férniük. A felhasználók helyszíni és Felhőbeli kezelése is kihívást jelentő forgatókönyveket jelent. 

A Microsoft személyazonossági megoldásai a helyszíni és a felhőalapú funkciókat is kiterjedhetnek.  Ezek a megoldások közös felhasználói identitást hoznak létre hitelesítéshez és engedélyezéshez minden erőforráshoz, a helytől függetlenül. Ezt a **hibrid identitást** hívjuk.

A hibrid identitás az Azure AD-vel és a hibrid Identitáskezelés kezelésével ezek a forgatókönyvek válnak elérhetővé.

Ha hibrid identitást szeretne elérni az Azure AD-vel, a forgatókönyvek függvényében a három hitelesítési módszer egyikét használhatja.   A három módszer a következők: 

- **[Jelszó kivonatának szinkronizálása (PHS)](whatis-phs.md)**  
- **[Átmenő hitelesítés (PTA)](how-to-connect-pta.md)**  
- **[Összevonás (AD FS)](whatis-fed.md)** 

Ezek a hitelesítési módszerek [egyszeri bejelentkezési](how-to-connect-sso.md) képességeket is biztosítanak.  Az egyszeri bejelentkezés automatikusan aláírja a felhasználókat a vállalati eszközökön a vállalati hálózathoz csatlakozó felhasználók számára.

További információ: [válassza ki a megfelelő hitelesítési módszert a Azure Active Directory Hybrid Identity megoldáshoz](./choose-ad-authn.md). 

## <a name="common-scenarios-and-recommendations"></a>Gyakori forgatókönyvek és javaslatok 

Szeretnénk bemutatni néhány gyakori hibrid identitás- és hozzáféréskezelési helyzetet és a hozzájuk ajánlott hibrid identitáskezelési lehetősége(ke)t. 

|Cél:|PHS és SSO<sup>1</sup>| PTA és SSO<sup>2</sup> | AD FS<sup>3</sup>| 
|-----|-----|-----|-----| 
|A helyszíni Active Directoryban létrehozott új felhasználói, kapcsolattartói és csoportfiókok automatikus szinkronizálása a felhőbe|![Ajánlott](./media/whatis-hybrid-identity/ic195031.png)| ![Ajánlott](./media/whatis-hybrid-identity/ic195031.png) |![Ajánlott](./media/whatis-hybrid-identity/ic195031.png)| 
|A bérlő beállítása Microsoft 365 hibrid forgatókönyvek esetén.|![Ajánlott](./media/whatis-hybrid-identity/ic195031.png)| ![Ajánlott](./media/whatis-hybrid-identity/ic195031.png) |![Ajánlott](./media/whatis-hybrid-identity/ic195031.png)| 
|Lehetővé teheti a felhasználók számára a bejelentkezést és a felhőalapú szolgáltatások elérését a helyszíni jelszavuk használatával.|![Ajánlott](./media/whatis-hybrid-identity/ic195031.png)| ![Ajánlott](./media/whatis-hybrid-identity/ic195031.png) |![Ajánlott](./media/whatis-hybrid-identity/ic195031.png)| 
|Egyszeri bejelentkezés implementálása vállalati hitelesítő adatok használatával.|![Ajánlott](./media/whatis-hybrid-identity/ic195031.png)| ![Ajánlott](./media/whatis-hybrid-identity/ic195031.png) |![Ajánlott](./media/whatis-hybrid-identity/ic195031.png)|  
|Ügyeljen arra, hogy a rendszer ne tárolja a jelszó-kivonatokat a felhőben.| |![Ajánlott](./media/whatis-hybrid-identity/ic195031.png)|![Ajánlott](./media/whatis-hybrid-identity/ic195031.png)| 
|Felhőalapú multi-Factor Authentication megoldások engedélyezése.|![Ajánlott](./media/whatis-hybrid-identity/ic195031.png)|![Ajánlott](./media/whatis-hybrid-identity/ic195031.png)|![Ajánlott](./media/whatis-hybrid-identity/ic195031.png)| 
|Helyszíni multi-Factor Authentication megoldások engedélyezése.| | |![Ajánlott](./media/whatis-hybrid-identity/ic195031.png)| 
|Támogatás intelligens kártyás hitelesítés a saját felhasználók számára. <sup>4</sup>| | |![Ajánlott](./media/whatis-hybrid-identity/ic195031.png)| 
|A jelszó lejárati értesítéseinek megjelenítése az Office-Portálon és a Windows 10 asztalán.| | |![Ajánlott](./media/whatis-hybrid-identity/ic195031.png)| 

> <sup>1</sup> Jelszókivonat-szinkronizálás egyszeri bejelentkezéssel. 
> 
> <sup>2</sup> Átmenő hitelesítés és egyszeri bejelentkezés.  
> 
> <sup>3</sup> Összevont egyszeri bejelentkezés AD FS-sel.  
>  
> <sup>4</sup> Az AD FS a vállalati nyilvános kulcsú infrastruktúrával is integrálható, hogy tanúsítványokkal is be lehessen jelentkezni. Ezek a tanúsítványok lehetnek megbízható regisztrációs csatornákon (például MDM-en vagy csoportházirend-objektumon) keresztül kiosztott szoftveres tanúsítványok, intelligenskártya-tanúsítványok (beleértve a PIV/CAC-kártyákat) vagy Vállalati Windows Hello- (tanúsítvány-megbízhatósági) megoldások. Az intelligens kártyás hitelesítés támogatásával kapcsolatos további információkat [ebben a blogban](/archive/blogs/samueld/adfs-certauth-aad-o365) talál. 
> 

## <a name="license-requirements-for-using-azure-ad-connect"></a>A Azure AD Connect használatára vonatkozó licencfeltételek

[!INCLUDE [active-directory-free-license.md](../../../includes/active-directory-free-license.md)]

## <a name="next-steps"></a>Következő lépések 

- [Mi az Azure AD Connect és a Connect Health?](whatis-azure-ad-connect.md) 
- [Mi az a jelszó-kivonat szinkronizálása (PHS)?](whatis-phs.md) 
- [Mi az átmenő hitelesítés (PTA)?](how-to-connect-pta.md) 
- [Mi az összevonás?](whatis-fed.md) 
- [Mi az az egyszeri bejelentkezés?](how-to-connect-sso.md)