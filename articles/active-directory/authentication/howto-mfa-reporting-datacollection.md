---
title: Azure AD MFA felhasználói adatgyűjtési szolgáltatás – Azure Active Directory
description: Milyen információkat használ a felhasználók hitelesítéséhez az Azure AD Multi-Factor Authentication?
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: c2124ca9fae76d87a695a2c453f7b222e7a08794
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96742793"
---
# <a name="azure-ad-multi-factor-authentication-user-data-collection"></a>Azure AD Multi-Factor Authentication felhasználói adatgyűjtés

Ez a dokumentum ismerteti, hogyan keresheti meg az Azure Multi-Factor Authentication-kiszolgáló (MFA-kiszolgáló) és az Azure AD MFA (felhőalapú) által gyűjtött felhasználói adatokat abban az esetben, ha el szeretné távolítani.

[!INCLUDE [gdpr-hybrid-note](../../../includes/gdpr-hybrid-note.md)]

## <a name="information-collected"></a>Összegyűjtött információk

Az MFA-kiszolgáló, az NPS-bővítmény és a Windows Server 2016 Azure AD MFA AD FS adapter az alábbi adatokat gyűjti és tárolja 90 napig.

Hitelesítési kísérletek (jelentéskészítéshez és hibaelhárításhoz használatos):

- Időbélyeg
- Felhasználónév
- Utónév
- Vezetéknév
- E-mail-cím
- Felhasználói csoport
- Hitelesítési módszer (telefonhívás, szöveges üzenet, Mobile App, eskü token)
- Telefonhívási mód (standard, PIN)
- Szöveges üzenet iránya (egyirányú, kétirányú)
- Szöveges üzenet mód (OTP, OTP + PIN)
- Mobile App Mode (standard, PIN)
- ESKÜ token mód (standard, PIN)
- Hitelesítés típusa
- Alkalmazásnév
- Elsődleges hívási országkód
- Elsődleges hívás telefonszáma
- Elsődleges hívási bővítmény
- Elsődleges hívás hitelesítve
- Elsődleges hívás eredménye
- Biztonsági mentési hívás országkód
- Biztonságimásolat-hívás telefonszáma
- Biztonságimásolat-hívások bővítménye
- Biztonsági mentési hívás hitelesítve
- Biztonsági mentési hívás eredménye
- Teljes hitelesítés
- Teljes eredmény
- Results (Eredmények)
- Hitelesített
- Eredmény
- IP-cím kezdeményezése
- Eszközök
- Eszköz jogkivonata
- Eszköz típusa
- Mobile App-verzió
- Operációs rendszer verziója
- Eredmény
- Értesítés használatban

Aktiválások (a Microsoft Authenticator Mobile alkalmazásban lévő fiók aktiválását kísérli meg):
- Felhasználónév
- Fiók neve
- Időbélyeg
- Aktivációs kód eredményének beolvasása
- Sikeres aktiválás
- Aktiválási hiba
- Aktiválási állapot eredménye
- Eszköz neve
- Eszköz típusa
- Alkalmazásverzió
- ESKÜ-token engedélyezve

Blokkok (a blokkolt állapot és a jelentéskészítés megállapításához használatos):

- Időbélyeg letiltása
- Blokkolás Felhasználónév szerint
- Felhasználónév
- Országkód
- Telefonszám
- Telefonszám formázva
- Mellék
- Tiszta bővítmény
- Blokkolva
- Blokkolás oka
- Befejezés időbélyege
- Befejezés oka
- Fiókzárolás
- Csalási riasztás
- Csalási riasztás nincs letiltva
- Nyelv

Megkerülések (jelentéskészítéshez használatos):

- Mellőzési időbélyeg
- Mellőzési másodpercek
- Mellőzés Felhasználónév alapján
- Felhasználónév
- Országkód
- Telefonszám
- Telefonszám formázva
- Mellék
- Tiszta bővítmény
- Mellőzés oka
- Befejezés időbélyege
- Befejezés oka
- Használat mellőzése

Módosítások (az MFA-kiszolgálóra vagy az Azure AD-ra történő felhasználói változások szinkronizálásához használatos):

- Időbélyeg módosítása
- Felhasználónév
- Új országkód
- Új telefonszám
- Új bővítmény
- Új biztonságimásolat-országkód
- Új tartalék telefonszám
- Új biztonsági mentési bővítmény
- Új PIN-kód
- PIN-kód módosítása kötelező
- Régi eszköz tokenje
- Új eszköz jogkivonata

## <a name="gather-data-from-mfa-server"></a>Adatok összegyűjtése az MFA-kiszolgálóról

Az MFA-kiszolgáló 8,0-es vagy újabb verziója esetén az alábbi folyamat lehetővé teszi a rendszergazdák számára az összes felhasználó exportálását a felhasználók számára:

- Jelentkezzen be az MFA-kiszolgálóra, lépjen a **felhasználók** lapra, válassza ki a szóban forgó felhasználót, majd kattintson a **Szerkesztés** gombra. Készítsen képernyőképeket (Alt-PrtScn) az egyes lapokon, hogy a felhasználó az aktuális MFA-beállításait adja meg.
- Az MFA-kiszolgáló parancssorából futtassa az alábbi parancsot a telepítésnek megfelelően a `C:\Program Files\Multi-Factor Authentication Server\MultiFactorAuthGdpr.exe export <username>` JSON formátumú fájl létrehozásához.
- A rendszergazdák a Web Service SDK GetUserGdpr műveletet is használhatják az adott felhasználó számára gyűjtött összes MFA Cloud Service-információ exportálására, vagy egy nagyobb jelentéskészítési megoldásba való beépítésre.
- A `C:\Program Files\Multi-Factor Authentication Server\Logs\MultiFactorAuthSvc.log` (z) "" (a keresésben szereplő idézőjelekkel együtt) megkeresése és minden biztonsági másolata \<username> a felhasználói rekord hozzáadott vagy módosított példányainak megkereséséhez.
   - Ezek a rekordok korlátozottak (de nem távolíthatók el) a **"felhasználói változások naplózása"** lehetőség törlésével az MFA-kiszolgáló UX-ben, a naplózás szakaszban, a naplófájlok lapon.
   - Ha a syslog konfigurálva van, és a **"felhasználói változások naplózása"** be van JELÖLVE az MFA-kiszolgáló UX-ben, a naplózási szakaszban, a syslog lapon, a naplóbejegyzések begyűjthetők a syslog használatával.
- A MultiFactorAuthSvc. log fájlban és a hitelesítési kísérletekhez tartozó más MFA-kiszolgálók naplófájljaiban található Felhasználónév egyéb előfordulása a MultiFactorAuthGdpr.exe export vagy Web Service SDK-GetUserGdpr használatával biztosított információk működésének és ismétlődőnek tekintendő.

## <a name="delete-data-from-mfa-server"></a>Adatok törlése az MFA-kiszolgálóról

Az MFA-kiszolgáló parancssorából futtassa az alábbi parancsot az elérési út módosításával a telepítésnek megfelelően, `C:\Program Files\Multi-Factor Authentication Server\MultiFactorAuthGdpr.exe delete <username>` hogy törölje az összes MFA Cloud Service-információ a felhasználó számára gyűjtött adatait.

- Az exportálásban szereplő adategységeket valós időben törli a rendszer, de akár 30 napig is eltarthat, amíg az operatív vagy a duplikált adatfeldolgozás teljesen el nem távolítható.
- A rendszergazdák a Web Service SDK DeleteUserGdpr műveletet is használhatják az adott felhasználó számára gyűjtött összes MFA Cloud Service-információ törlésére, vagy egy nagyobb jelentéskészítési megoldásba való beépítésre.

## <a name="gather-data-from-nps-extension"></a>Adatok összegyűjtése az NPS-bővítményből

Az exportálási kérelem elvégzéséhez használja a [Microsoft adatvédelmi portálját](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) .

- Az MFA-információk az Exportálás részét képezik, amely órákat vagy napokat is igénybe vehet.
- Az AzureMfa/AuthN/AuthNOptCh, a AzureMfa/AuthZ/AuthZAdminCh, valamint a AzureMfa/AuthZ/AuthZOptCh eseménynaplókban előforduló Felhasználónév az exportálás során megadott információnak megfelelően működik és duplikált.

## <a name="delete-data-from-nps-extension"></a>Adatok törlése az NPS-bővítményből

A [Microsoft adatvédelmi portálon](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) a fiókhoz tartozó, a felhasználó számára gyűjtött összes MFA Cloud Service-információ törlésére vonatkozó kérést hozhat.

- Akár 30 napig is eltarthat, amíg a rendszer teljesen el nem távolítja az adatgyűjtést.

## <a name="gather-data-from-windows-server-2016-azure-ad-mfa-ad-fs-adapter"></a>Adatok összegyűjtése a Windows Server 2016 Azure AD MFA AD FS adapterről

Az exportálási kérelem elvégzéséhez használja a [Microsoft adatvédelmi portálját](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) . 

- Az MFA-információk az Exportálás részét képezik, amely órákat vagy napokat is igénybe vehet.
- Az AD FS nyomkövetési/hibakeresési eseménynaplókban (ha engedélyezve vannak) a Felhasználónév előfordulása az exportálás során megadott információk működésének és ismétlődőnek tekintendő.

## <a name="delete-data-from-windows-server-2016-azure-ad-mfa-ad-fs-adapter"></a>Adatok törlése a Windows Server 2016 Azure AD MFA AD FS adapterről

A [Microsoft adatvédelmi portálon](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) a fiókhoz tartozó, a felhasználó számára gyűjtött összes MFA Cloud Service-információ törlésére vonatkozó kérést hozhat.

- Akár 30 napig is eltarthat, amíg a rendszer teljesen el nem távolítja az adatgyűjtést.

## <a name="gather-data-for-azure-ad-mfa"></a>Adatok gyűjtése az Azure AD MFA-hoz

Az exportálási kérelem elvégzéséhez használja a [Microsoft adatvédelmi portálját](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) .

- Az MFA-információk az Exportálás részét képezik, amely órákat vagy napokat is igénybe vehet.

## <a name="delete-data-for-azure-ad-mfa"></a>Az Azure AD MFA-hoz tartozó adattörlés

A [Microsoft adatvédelmi portálon](https://portal.azure.com/#blade/Microsoft_Azure_Policy/UserPrivacyMenuBlade/Overview) a fiókhoz tartozó, a felhasználó számára gyűjtött összes MFA Cloud Service-információ törlésére vonatkozó kérést hozhat.

- Akár 30 napig is eltarthat, amíg a rendszer teljesen el nem távolítja az adatgyűjtést.

## <a name="next-steps"></a>Következő lépések

[MFA-kiszolgáló jelentése](howto-mfa-reporting.md)
