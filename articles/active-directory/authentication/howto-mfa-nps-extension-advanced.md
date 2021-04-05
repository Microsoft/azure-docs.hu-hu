---
title: Az Azure AD MFA NPS-bővítmény konfigurálása – Azure Active Directory
description: A hálózati házirend-kiszolgáló bővítmény telepítése után az alábbi lépéseket követve speciális konfigurációt használhat, például az engedélyezett IP-címek listáját és a UPN-helyettesítést.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/11/2018
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 695261ceae9d64be9395e6de082f97be04292078
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98745985"
---
# <a name="advanced-configuration-options-for-the-nps-extension-for-multi-factor-authentication"></a>Speciális konfigurációs beállítások a Multi-Factor Authentication NPS-bővítményéhez

A hálózati házirend-kiszolgáló (NPS) bővítmény kibővíti a felhőalapú Azure AD Multi-Factor Authentication szolgáltatásokat a helyszíni infrastruktúrába. Ez a cikk feltételezi, hogy már telepítette a bővítményt, és most szeretné tudni, hogyan szabhatja testre a szükséges bővítményt.

## <a name="alternate-login-id"></a>Másodlagos bejelentkezési azonosító

Mivel a hálózati házirend-kiszolgáló bővítmény a helyszíni és a Felhőbeli címtárakhoz is csatlakozik, előfordulhat, hogy olyan problémába ütközik, amelyben a helyszíni felhasználónevek (UPN) nem egyeznek a felhőben található nevekkel. A probléma megoldásához használjon alternatív bejelentkezési azonosítókat. 

A hálózati házirend-kiszolgáló bővítményben kijelölhet egy Active Directory attribútumot, amelyet az egyszerű felhasználónév helyett használhat az Azure AD-Multi-Factor Authenticationhoz. Ez lehetővé teszi a helyszíni erőforrások kétlépéses ellenőrzéssel való ellátását a helyszíni egyszerű felhasználónevek módosítása nélkül. 

Az alternatív bejelentkezési azonosítók konfigurálásához lépjen a (z) elemre, `HKLM\SOFTWARE\Microsoft\AzureMfa` és szerkessze a következő beállításjegyzékbeli értékeket:

| Név | Típus | Alapértelmezett érték | Leírás |
| ---- | ---- | ------------- | ----------- |
| LDAP_ALTERNATE_LOGINID_ATTRIBUTE | sztring | Üres | Jelölje ki az egyszerű felhasználónév helyett használni kívánt Active Directory attribútum nevét. Ez az attribútum a AlternateLoginId attribútumként szolgál. Ha a beállításazonosító [érvényes Active Directory attribútumra](/windows/win32/adschema/attributes-all) van beállítva (például E-mail vagy DisplayName), akkor a rendszer az attribútum értékét használja a felhasználó egyszerű felhasználóneve helyett a hitelesítéshez. Ha a beállításazonosító üres vagy nincs konfigurálva, akkor a AlternateLoginId le van tiltva, és a rendszer a felhasználó UPN-azonosítóját használja a hitelesítéshez. |
| LDAP_FORCE_GLOBAL_CATALOG | boolean | Hamis | Ezzel a jelzővel kényszerítheti a globális katalógus használatát az LDAP-keresésekhez, amikor megkeresi a AlternateLoginId. Konfiguráljon egy tartományvezérlőt globális katalógusként, adja hozzá a AlternateLoginId attribútumot a globális katalógushoz, majd engedélyezze ezt a jelzőt. <br><br> Ha LDAP_LOOKUP_FORESTS konfigurálva van (nem üres), **Ez a jelző igaz értékre van kényszerítve**, a beállításjegyzék-beállítás értékétől függetlenül. Ebben az esetben a hálózati házirend-kiszolgáló bővítménye megköveteli, hogy a globális katalógus minden erdő AlternateLoginId attribútumával legyen konfigurálva. |
| LDAP_LOOKUP_FORESTS | sztring | Üres | Adja meg a keresendő erdők pontosvesszővel tagolt listáját. Például: *contoso. com; foobar. com*. Ha a beállításazonosító konfigurálva van, a hálózati házirend-kiszolgáló bővítmény a iteratív megkeresi az összes erdőt a felsorolt sorrendben, és visszaadja az első sikeres AlternateLoginId értéket. Ha a beállításazonosító nincs konfigurálva, a AlternateLoginId-keresés az aktuális tartományra korlátozódik.|

A másodlagos bejelentkezési azonosítókkal kapcsolatos problémák elhárításához használja az [alternatív bejelentkezési azonosítók hibáinak](howto-mfa-nps-extension-errors.md#alternate-login-id-errors)ajánlott lépéseit.

## <a name="ip-exceptions"></a>IP-kivételek

Ha figyelnie kell a kiszolgáló rendelkezésre állását, például ha a terheléselosztó ellenőrzi, hogy mely kiszolgálók futnak a munkaterhelések elküldése előtt, akkor nem szeretné, hogy a rendszer ellenőrzési kérések által blokkolja ezeket az ellenőrzéseket. Ehelyett hozzon létre egy listát az Ön által ismert IP-címekről, és tiltsa le az adott lista Multi-Factor Authentication követelményeit.

Az IP-címek engedélyezési listájának konfigurálásához nyissa meg `HKLM\SOFTWARE\Microsoft\AzureMfa` a következő beállításértéket, és konfigurálja a beállításjegyzéket:

| Név | Típus | Alapértelmezett érték | Leírás |
| ---- | ---- | ------------- | ----------- |
| IP_WHITELIST | sztring | Üres | Adja meg az IP-címek pontosvesszővel tagolt listáját. Adja meg azoknak a számítógépeknek a IP-címeit, amelyeken a szolgáltatás kérelmek származnak, például a NAS/VPN-kiszolgálótól. Az IP-címtartományok és az alhálózatok nem támogatottak. <br><br> Például: *10.0.0.1; 10.0.0.2; 10.0.0.3*.

> [!NOTE]
> A telepítő alapértelmezés szerint nem hozza létre ezt a beállításkulcsot, és egy hiba jelenik meg a AuthZOptCh-naplóban a szolgáltatás újraindításakor. Ez a hiba a naplóban figyelmen kívül hagyható, de ha a beállításkulcs létrejött, és nem szükséges, üresen marad, a hibaüzenet nem jelenik meg.

Ha egy kérelem olyan IP-címről érkezik, amely létezik a-ben `IP_WHITELIST` , a kétlépéses ellenőrzés kimarad. Az IP-listát a RADIUS-kérelem *ratNASIPAddress* attribútumában megadott IP-címhez hasonlítjuk. Ha egy RADIUS-kérelem a ratNASIPAddress attribútum nélkül érkezik, a rendszer figyelmeztetést küld: "IP_WHITE_LIST_WARNING:: az IP-engedélyezési lista figyelmen kívül lesz hagyva, mert a forrás IP-cím hiányzik a RADIUS-kérelem NasIpAddress attribútumában.

## <a name="next-steps"></a>Következő lépések

[Hibák elhárítása az Azure AD-hez tartozó NPS-bővítményből Multi-Factor Authentication](howto-mfa-nps-extension-errors.md)