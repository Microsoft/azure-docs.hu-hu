---
title: Jelszó nélküli biztonsági kulcs bejelentkezése – Azure Active Directory
description: Jelszó nélküli biztonsági kulcsos bejelentkezés engedélyezése az Azure AD-be FIDO2 biztonsági kulcsokkal
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 04/21/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 436a972693aafd220d277d7411c0da12636e9cc6
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107829799"
---
# <a name="enable-passwordless-security-key-sign-in"></a>Jelszó nélküli biztonsági kulcsos bejelentkezés engedélyezése 

Azok a vállalatok, amelyek jelenleg jelszavakat használnak, és megosztott pc-környezettel is vannak, a biztonsági kulcsok zökkenőmentes lehetőséget biztosítanak a dolgozók hitelesítésére felhasználónév vagy jelszó megadása nélkül. A biztonsági kulcsok nagyobb hatékonyságot biztosítanak a dolgozók számára, és nagyobb biztonságot nyújtanak.

Ez a dokumentum a biztonsági kulcson alapuló jelszó nélküli hitelesítés engedélyezésére összpontosít. A cikk végén be tud majd jelentkezni a webalapú alkalmazásokba az Azure AD-fiókjával egy FIDO2 biztonsági kulccsal.

## <a name="requirements"></a>Követelmények

- [Azure AD Multi-Factor Authentication](howto-mfa-getstarted.md)
- Kombinált [biztonsági információk regisztrálásának engedélyezése](concept-registration-mfa-sspr-combined.md)
- Kompatibilis [FIDO2 biztonsági kulcsok](concept-authentication-passwordless.md#fido2-security-keys)
- A WebAuthN használatához Windows 10 1903-as vagy újabb verzió szükséges**

Ha biztonsági kulcsokat használ a webalkalmazások és szolgáltatásokba való bejelentkezéshez, olyan böngészővel kell lennie, amely támogatja a WebAuthN protokollt. Ezek közé tartozik a Microsoft Edge, a Chrome, a Firefox és a Safari.


## <a name="prepare-devices"></a>Eszközök előkészítése

Az Azure AD-hez csatlakozott eszközök esetében a legjobb megoldás Windows 10 1903-as vagy újabb verzió.

A hibrid Azure AD-hez Windows 10 2004-es vagy újabb verziójú eszközöknek kell futniuk.

## <a name="enable-passwordless-authentication-method"></a>Jelszó nélküli hitelesítési módszer engedélyezése

### <a name="enable-the-combined-registration-experience"></a>A kombinált regisztrációs folyamat engedélyezése

A jelszó nélküli hitelesítési módszerek regisztrációs funkciói a kombinált regisztrációs funkcióra támaszkodnak. A kombinált regisztráció engedélyezéséhez kövesse a Kombinált biztonsági információk regisztrálásának [engedélyezése](howto-registration-mfa-sspr-combined.md)cikkben található lépéseket.

### <a name="enable-fido2-security-key-method"></a>A FIDO2 biztonságikulcs-módszer engedélyezése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Keresse meg **a Azure Active Directory** hitelesítési  >  **módszerek Hitelesítési** módszer  >    >  **házirendet.**
1. A **FIDO2 biztonsági kulcs módszer alatt** válassza a következő beállításokat:
   1. **Engedélyezés** – Igen vagy Nem
   1. **Cél** – Minden felhasználó vagy Felhasználók kiválasztása
1. **Mentse a** konfigurációt.

## <a name="user-registration-and-management-of-fido2-security-keys"></a>FIDO2 biztonsági kulcsok felhasználói regisztrációja és kezelése

1. Lépjen a [https://myprofile.microsoft.com](https://myprofile.microsoft.com) lapra.
1. Jelentkezzen be, ha még nem.
1. Kattintson **a Biztonsági adatok elemre.**
   1. Ha a felhasználó már regisztrált legalább egy Azure AD Multi-Factor Authentication-módszert, azonnal regisztrálhat egy FIDO2 biztonsági kulcsot.
   1. Ha nem regisztráltak legalább egy Azure AD Multi-Factor Authentication-módszert, hozzá kell adni egyet.
1. Adjon hozzá egy FIDO2 biztonsági kulcsot a **Metódus hozzáadása gombra kattintva,** majd válassza a **Biztonsági kulcs gombra.**
1. Válassza az **USB-eszköz vagy** **az NFC-eszköz lehetőséget.**
1. Készítse elő a kulcsot, és válassza a **Tovább lehetőséget.**
1. Megjelenik egy mező, amely arra kéri a felhasználót, hogy hozzon létre/adjon meg egy PIN-kódot a biztonsági kulcshoz, majd hajtsa végre a kulcshoz szükséges kézmozdulatot biometrikus vagy érintéses adatokat is.
1. A felhasználó visszatér a kombinált regisztrációs felületre, és megkéri, hogy adjon kifejező nevet a kulcsnak, hogy a felhasználó azonosíthatja, melyiket, ha több is van. Kattintson a **Tovább** gombra.
1. A **folyamat befejezéséhez** kattintson a Kész gombra.

## <a name="sign-in-with-passwordless-credential"></a>Bejelentkezés jelszó nélküli hitelesítő adatokkal

Az alábbi példában egy felhasználó már kiépítte a FIDO2 biztonsági kulcsát. A felhasználó a FIDO2 biztonsági kulccsal bejelentkezhet a webe egy támogatott böngészőben az 1903-as vagy újabb Windows 10 verzióban.

![Biztonsági kulcs bejelentkezési Microsoft Edge](./media/howto-authentication-passwordless-security-key/fido2-windows-10-1903-edge-sign-in.png)

## <a name="troubleshooting-and-feedback"></a>Hibaelhárítás és visszajelzés

Ha visszajelzést szeretne küldeni, vagy problémákba ütközik ezzel a funkcióval kapcsolatban, ossza meg a Windows Visszajelzési központ alkalmazáson keresztül a következő lépésekkel:

1. Indítsa **Visszajelzési központ,** és győződjön meg arról, hogy be van jelentkezve.
1. Küldjön visszajelzést az alábbi kategorizálással:
   - Kategória: Biztonság és adatvédelem
   - Alkategória: FIDO
1. A naplók rögzítéséhez használja a Probléma **újbóli létrehozása lehetőséget.**

## <a name="known-issues"></a>Ismert problémák

### <a name="security-key-provisioning"></a>Biztonsági kulcs kiépítése

A biztonsági kulcsok rendszergazdai jogosultságokkal való ellátása és a jogosultságok bontása nem érhető el.

### <a name="cached-logon-on-hybrid-azure-ad-joined-devices"></a>Gyorsítótárazott bejelentkezés hibrid Azure AD-hez csatlakozott eszközökön

A FIDO2-kulcsokkal való gyorsítótárazott bejelentkezés meghiúsul a hibrid Azure AD-hez Windows 10 20H2-es verziójú hibrid eszközökön. Ennek eredményeképpen a felhasználók nem fognak tudni bejelentkezni, ha nem látnak rá a helyszíni tartományvezérlőre. Ez jelenleg vizsgálat alatt áll.

### <a name="upn-changes"></a>UPN-módosítások

Dolgozunk egy olyan funkció támogatásán, amely lehetővé teszi az UPN-változásokat a hibrid Azure AD-hez és az Azure AD-hez csatlakozott eszközökön. Ha egy felhasználó upn-ját módosítja, a FIDO2 biztonsági kulcsait már nem módosíthatja, hogy figyelembe tudja venni a módosítást. A megoldás az eszköz alaphelyzetbe állítása, és a felhasználónak újra kell regisztrálnia.

## <a name="next-steps"></a>Következő lépések

[FIDO2 biztonsági kulcs Windows 10 bejelentkezéshez](howto-authentication-passwordless-security-key-windows.md)

[FIDO2-hitelesítés engedélyezése helyszíni erőforrásokhoz](howto-authentication-passwordless-security-key-on-premises.md)

[További információ az eszközregisztrációról](../devices/overview.md)

[További információ az Azure AD Multi-Factor Authenticationről](../authentication/howto-mfa-getstarted.md)
