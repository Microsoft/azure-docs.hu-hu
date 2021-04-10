---
title: 'Azure AD Connect: átmenő hitelesítés – gyakori kérdések | Microsoft Docs'
description: Válaszok Azure Active Directory átmenő hitelesítéssel kapcsolatos gyakori kérdésekre
services: active-directory
keywords: Azure AD Connect átmenő hitelesítés, telepítési Active Directory, szükséges összetevők az Azure AD-hez, egyszeri bejelentkezéshez, egyszeri bejelentkezéshez
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 06/09/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 75f797c40a276323cea9983c5340d2d854160c83
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97368477"
---
# <a name="azure-active-directory-pass-through-authentication-frequently-asked-questions"></a>Azure Active Directory átmenő hitelesítés: gyakori kérdések

Ez a cikk az Azure Active Directory (Azure AD) átmenő hitelesítéssel kapcsolatos gyakori kérdéseket tárgyalja. A frissített tartalom ellenőrzésének visszatartása.

## <a name="which-of-the-methods-to-sign-in-to-azure-ad-pass-through-authentication-password-hash-synchronization-and-active-directory-federation-services-ad-fs-should-i-choose"></a>Hogyan kell bejelentkezni az Azure AD-be, az átmenő hitelesítésre, a jelszó-kivonat szinkronizálására és a Active Directory összevonási szolgáltatások (AD FS) (AD FS)?

Tekintse át [ezt az útmutatót](./choose-ad-authn.md) a különböző Azure ad bejelentkezési módszereinek összehasonlításához, és hogyan választhatja ki a szervezete megfelelő bejelentkezési módszerét.

## <a name="is-pass-through-authentication-a-free-feature"></a>Az áteresztő hitelesítés ingyenes szolgáltatás?

Az átmenő hitelesítés ingyenes szolgáltatás. Nincs szüksége az Azure AD fizetős kiadásaira a használatához.

## <a name="does-conditional-access-work-with-pass-through-authentication"></a>Működik a [feltételes hozzáférés](../conditional-access/overview.md) átmenő hitelesítéssel?

Igen. Az összes feltételes hozzáférési képesség, beleértve az Azure AD-Multi-Factor Authentication is, az átmenő hitelesítéssel dolgozhat.

## <a name="does-pass-through-authentication-support-alternate-id-as-the-username-instead-of-userprincipalname"></a>Az áteresztő hitelesítés támogatja a "másodlagos azonosító" nevet a "userPrincipalName" helyett?
Igen, a bejelentkezés nem UPN-értékkel, például egy másodlagos e-mail-címmel is támogatott az átmenő hitelesítés (PTA) és a jelszó-kivonat szinkronizálása (PHS) esetében. További információ a [Másodlagos bejelentkezési azonosítóról](../authentication/howto-authentication-use-email-signin.md).

## <a name="does-password-hash-synchronization-act-as-a-fallback-to-pass-through-authentication"></a>A jelszó-kivonatolási szinkronizálás tartalékként viselkedik az átmenő hitelesítéshez?

Nem. Az átmenő hitelesítés _nem végez_ automatikusan feladatátvételt a jelszó-kivonat szinkronizálására. A felhasználók bejelentkezési hibáinak elkerülése érdekében a [magas rendelkezésre álláshoz](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability)az átmenő hitelesítést kell beállítani.

## <a name="what-happens-when-i-switch-from-password-hash-synchronization-to-pass-through-authentication"></a>Mi történik, ha átváltok a jelszó-kivonatolási szinkronizálásról az átmenő hitelesítésre?

Ha a Azure AD Connect használatával vált át a bejelentkezési módszer a jelszó-kivonatos szinkronizálásról az átmenő hitelesítésre, az átmenő hitelesítés lesz az elsődleges bejelentkezési módszer a felügyelt tartományokban lévő felhasználók számára. Vegye figyelembe, hogy a jelszó-kivonatoló szinkronizálás által korábban szinkronizált összes felhasználó jelszavas kivonata továbbra is az Azure AD-ben tárolódik.

## <a name="can-i-install-an-azure-ad-application-proxy-connector-on-the-same-server-as-a-pass-through-authentication-agent"></a>Telepíthetek [Azure ad Application proxy](../manage-apps/application-proxy.md) -összekötőt ugyanarra a kiszolgálóra, mint egy átmenő hitelesítési ügynököt?

Igen. Ez a konfiguráció támogatja az átmenő hitelesítési ügynök (1.5.193.0 vagy újabb verzió) rebranded verzióit.

## <a name="what-versions-of-azure-ad-connect-and-pass-through-authentication-agent-do-you-need"></a>A Azure AD Connect és a továbbított hitelesítési ügynök mely verzióira van szüksége?

Ahhoz, hogy ez a funkció működjön, a 1.1.750.0 vagy újabb verzióra van szükség a Azure AD Connect és a 1.5.193.0, illetve az áteresztő hitelesítési ügynök későbbi verzióihoz. Telepítse az összes szoftvert a kiszolgálókon a Windows Server 2012 R2 vagy újabb verzióval.

## <a name="what-happens-if-my-users-password-has-expired-and-they-try-to-sign-in-by-using-pass-through-authentication"></a>Mi történik, ha a felhasználó jelszava lejárt, és az átmenő hitelesítés használatával megpróbál bejelentkezni?

Ha beállította a [jelszó visszaírási](../authentication/concept-sspr-writeback.md) egy adott felhasználó számára, és ha a felhasználó átmenő hitelesítéssel jelentkezik be, a jelszavukat módosíthatja vagy alaphelyzetbe állíthatja. A jelszavakat a rendszer a várt módon visszaírja a helyszíni Active Directoryba.

Ha nem konfigurálta a jelszó-visszaírási egy adott felhasználóhoz, vagy ha a felhasználónak nincs hozzárendelt érvényes Azure AD-licence, a felhasználó nem tudja frissíteni a jelszavát a felhőben. Nem tudják frissíteni a jelszavukat, még akkor sem, ha a jelszó lejárt. A felhasználó Ehelyett ezt az üzenetet látja: "a szervezet nem teszi lehetővé a jelszó frissítését ezen a webhelyen. Frissítse a szervezet által ajánlott módszernek megfelelően, vagy kérje meg a rendszergazdát, ha segítségre van szüksége. " A felhasználónak vagy a rendszergazdának vissza kell állítania a jelszavát a helyszíni Active Directoryban.

## <a name="how-does-pass-through-authentication-protect-you-against-brute-force-password-attacks"></a>Hogyan biztosítja az átmenő hitelesítés a találgatásos jelszavak elleni támadásokat?

[Olvassa el az intelligens zárolással kapcsolatos információkat](../authentication/howto-password-smart-lockout.md).

## <a name="what-do-pass-through-authentication-agents-communicate-over-ports-80-and-443"></a>Mit jelentenek az átmenő hitelesítési ügynökök a 80-es és a 443-es porton keresztül?

- A hitelesítési ügynökök a 443-as porton keresztül HTTPS-kérelmeket tesznek elérhetővé az összes funkció működéséhez.
- A hitelesítési ügynökök HTTP-kérelmeket tesznek elérhetővé a 80-as porton keresztül a TLS/SSL-visszavont tanúsítványok listáinak (CRL) letöltéséhez.

     >[!NOTE]
     >A legutóbbi frissítések csökkentik a szolgáltatás által igényelt portok számát. Ha a Azure AD Connect vagy a hitelesítési ügynök régebbi verzióit használja, tartsa nyitva ezeket a portokat is: 5671, 8080, 9090, 9091, 9350, 9352 és 10100-10120.

## <a name="can-the-pass-through-authentication-agents-communicate-over-an-outbound-web-proxy-server"></a>Kommunikálhat a továbbított hitelesítési ügynökök egy kimenő webproxy-kiszolgálón?

Igen. Ha a webproxy automatikus felderítése (WPAD) engedélyezve van a helyszíni környezetben, a hitelesítési ügynökök automatikusan megpróbálnak megkeresni és használni egy webproxy-kiszolgálót a hálózaton.

Ha nem rendelkezik WPAD-környezettel a környezetben, akkor az áteresztő hitelesítési ügynök az Azure AD-vel való kommunikáció engedélyezéséhez (az alább látható módon) adhat hozzá proxy-információkat:
- Konfigurálja a proxybeállításokat az Internet Explorerben, mielőtt telepítené a továbbítási hitelesítési ügynököt a kiszolgálón. Ez lehetővé teszi a hitelesítési ügynök telepítésének befejezését, de továbbra is **inaktívként** jelenik meg a felügyeleti portálon.
- A kiszolgálón navigáljon a "C:\Program Files\Microsoft Azure AD Connect Authentication Agent" elemre.
- Szerkessze a "AzureADConnectAuthenticationAgentService" konfigurációs fájlt, és adja hozzá a következő sorokat (cserélje le a "http \: //contosoproxy.com:8080" kifejezést a tényleges proxy-címmé):

```
   <system.net>
      <defaultProxy enabled="true" useDefaultCredentials="true">
         <proxy
            usesystemdefault="true"
            proxyaddress="http://contosoproxy.com:8080"
            bypassonlocal="true"
         />
     </defaultProxy>
   </system.net>
```

## <a name="can-i-install-two-or-more-pass-through-authentication-agents-on-the-same-server"></a>Telepíthetek két vagy több áteresztő hitelesítési ügynököt ugyanarra a kiszolgálóra?

Nem, csak egy átmenő hitelesítési ügynököt telepíthet egyetlen kiszolgálóra. Ha a magas rendelkezésre álláshoz szeretne átmenő hitelesítést beállítani, [kövesse az itt található utasításokat](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability).

## <a name="do-i-have-to-manually-renew-certificates-used-by-pass-through-authentication-agents"></a>Kell-e manuálisan megújítani a továbbított hitelesítési ügynökök által használt tanúsítványokat?

Az egyes áteresztő hitelesítési ügynökök és az Azure AD közötti kommunikáció tanúsítványalapú hitelesítéssel védett. Ezeket a [tanúsítványokat az Azure ad minden hónapban automatikusan megújítja](how-to-connect-pta-security-deep-dive.md#operational-security-of-the-authentication-agents). Ezeket a tanúsítványokat nem kell manuálisan megújítani. Szükség szerint törölheti a régebbi lejárt tanúsítványokat.

## <a name="how-do-i-remove-a-pass-through-authentication-agent"></a>Hogyan egy átmenő hitelesítési ügynököt?

Amíg egy átmenő hitelesítési ügynök fut, aktív marad, és folyamatosan kezeli a felhasználói bejelentkezési kérelmeket. Ha el kívánja távolítani a hitelesítési ügynököt, lépjen a **Vezérlőpult – > programok > programok és szolgáltatások elemre** , és távolítsa el mind a **Microsoft Azure ad csatlakozási hitelesítési ügynököt** , mind a **Microsoft Azure ad összekapcsolási ügynök frissítési** programját.

Ha az előző lépés elvégzése után a [Azure Active Directory felügyeleti központban](https://aad.portal.azure.com) bejelöli a átmenő hitelesítés panelt, akkor a hitelesítési ügynök **inaktívként** jelenik meg. Ez a _várt_ érték. A hitelesítési ügynök 10 nap elteltével automatikusan el lesz dobva a listából.

## <a name="i-already-use-ad-fs-to-sign-in-to-azure-ad-how-do-i-switch-it-to-pass-through-authentication"></a>Már használom a AD FS az Azure AD-be való bejelentkezéshez. Hogyan váltani átmenő hitelesítésre?

Ha AD FS (vagy más összevonási technológiákból) áttelepítését továbbítja a hitelesítésre, javasoljuk, hogy kövesse az [itt](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/blob/master/Authentication/Migrating%20from%20Federated%20Authentication%20to%20Pass-through%20Authentication.docx?raw=true)közzétett részletes üzembe helyezési útmutatót.

## <a name="can-i-use-pass-through-authentication-in-a-multi-forest-active-directory-environment"></a>Használhatok áteresztő hitelesítést többerdős Active Directory környezetben?

Igen. A többerdős környezetek akkor támogatottak, ha erdőszintű megbízhatósági kapcsolatok (kétirányú) vannak a Active Directory erdők között, és ha a névutótagok útválasztása megfelelően van konfigurálva.

## <a name="does-pass-through-authentication-provide-load-balancing-across-multiple-authentication-agents"></a>Biztosítja az átmenő hitelesítés több hitelesítési ügynök között a terheléselosztást?

Nem, több áteresztő hitelesítési ügynök telepítése csak [magas rendelkezésre állást](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability)biztosít. Nem biztosít determinisztikus terheléselosztást a hitelesítési ügynökök között. Bármely hitelesítési ügynök (véletlenszerűen) képes feldolgozni egy adott felhasználói bejelentkezési kérést.

## <a name="how-many-pass-through-authentication-agents-do-i-need-to-install"></a>Hány áteresztő hitelesítési ügynököt kell telepíteni?

Több áteresztő hitelesítési ügynök telepítése biztosítja a [magas rendelkezésre állást](how-to-connect-pta-quick-start.md#step-4-ensure-high-availability). Azonban nem biztosít determinisztikus terheléselosztást a hitelesítési ügynökök között.

Vegye figyelembe a bérlőn megtekinteni kívánt bejelentkezési kérelmek maximális és átlagos terhelését. Viszonyítási alapként egyetlen hitelesítési ügynök a 300-400 hitelesítések másodpercenkénti kezelésére alkalmas standard 4 magos CPU, 16 GB RAM-kiszolgáló esetén.

A hálózati forgalom becsléséhez használja a következő méretezési útmutatót:
- Minden kérelemhez tartozik egy adattartalom mérete (0,5 K + 1K * num_of_agents) bájt; például az Azure AD-ből származó adatok a hitelesítési ügynöknek. Itt a "num_of_agents" a bérlőn regisztrált hitelesítési ügynökök számát jelzi.
- Minden válaszhoz a hasznos adatok mérete 1K bájt; azaz a hitelesítési ügynök adatait az Azure AD-be.

A legtöbb ügyfél esetében az összesen kettő vagy három hitelesítési ügynök elegendő a magas rendelkezésre álláshoz és a kapacitáshoz. A bejelentkezési késés javítása érdekében telepítse a hitelesítési ügynököket a tartományvezérlőhöz közel.

>[!NOTE]
>A rendszer legfeljebb 40 hitelesítési ügynököt alkalmaz a bérlők esetében.

## <a name="why-do-i-need-a-cloud-only-global-administrator-account-to-enable-pass-through-authentication"></a>Miért van szükségem egy csak felhőalapú globális rendszergazdai fiókra az átmenő hitelesítés engedélyezéséhez?

Javasoljuk, hogy engedélyezze vagy tiltsa le az átmenő hitelesítést egy csak felhőalapú globális rendszergazdai fiók használatával. További információ [a csak felhőalapú globális rendszergazdai fiók hozzáadásáról](../fundamentals/add-users-azure-active-directory.md). Így biztosíthatja, hogy ne zárja ki a bérlőből.

## <a name="how-can-i-disable-pass-through-authentication"></a>Hogyan lehet letiltani az átmenő hitelesítést?

Futtassa újra a Azure AD Connect varázslót, és változtassa meg a felhasználói bejelentkezési módszert az átmenő hitelesítésből egy másik metódusra. Ez a módosítás letiltja az átmenő hitelesítést a bérlőn, és eltávolítja a hitelesítési ügynököt a kiszolgálóról. A hitelesítési ügynököket manuálisan kell eltávolítania a többi kiszolgálóról.

## <a name="what-happens-when-i-uninstall-a-pass-through-authentication-agent"></a>Mi történik, amikor eltávolítok egy átmenő hitelesítési ügynököt?

Ha egy kiszolgálóról távolít el egy átmenő hitelesítési ügynököt, a kiszolgáló leállítja a bejelentkezési kérések fogadását. Ha el szeretné kerülni a felhasználó bejelentkezési funkciójának a bérlőn való feltörését, ellenőrizze, hogy van-e egy másik hitelesítési ügynök, amely az áteresztő hitelesítési ügynök eltávolítása előtt fut.

## <a name="i-have-an-older-tenant-that-was-originally-setup-using-ad-fs--we-recently-migrated-to-pta-but-now-are-not-seeing-our-upn-changes-synchronizing-to-azure-ad--why-are-our-upn-changes-not-being-synchronized"></a>Olyan régi Bérlővel rendelkezem, amely eredetileg AD FS használatával lett beállítva.  A közelmúltban áttelepítettük a PTA ESP-t, de most nem látják az UPN-módosításokat az Azure AD-vel való szinkronizálásra.  Miért nem szinkronizálja az UPN-módosításokat?

A: a következő esetekben előfordulhat, hogy a helyszíni UPN-módosítások nem szinkronizálhatók, ha:

- Azure AD-bérlőjét a 2015. június 15. előtt hozták létre
- Először összevontuk az Azure AD-Bérlővel AD FS használatával a hitelesítéshez
- Úgy váltott, hogy az ESP hitelesítéssel felügyelt felhasználókat használjon.

Ennek az az oka, hogy a 2015. június 15. előtt létrehozott bérlők alapértelmezett viselkedése az UPN-változások blokkolása volt.  Ha le kell tiltania az UPN-módosítások blokkolását, a következő PowerShell-parancsmagot kell futtatnia:  

`Set-MsolDirSyncFeature -Feature SynchronizeUpnForManagedUsers -Enable $True`

A június 15-től 2015-ig létrehozott bérlők alapértelmezett viselkedése az UPN-változások szinkronizálása.   



## <a name="next-steps"></a>Következő lépések
- [Jelenlegi korlátozások](how-to-connect-pta-current-limitations.md): megtudhatja, hogy mely forgatókönyvek támogatottak, és melyek nem.
- [Gyors üzembe helyezés](how-to-connect-pta-quick-start.md): megkezdheti az Azure ad átmenő hitelesítését.
- [Migrálás ad FSról áteresztő hitelesítésre](https://github.com/Identity-Deployment-Guides/Identity-Deployment-Guides/blob/master/Authentication/Migrating%20from%20Federated%20Authentication%20to%20Pass-through%20Authentication.docx?raw=true) – részletes útmutató a AD FS (vagy más összevonási technológiákból) áttelepített hitelesítéshez.
- [Intelligens zárolás](../authentication/howto-password-smart-lockout.md): megtudhatja, hogyan konfigurálhatja az intelligens zárolási funkciót a bérlőn a felhasználói fiókok védetté tételéhez.
- [Technikai](how-to-connect-pta-how-it-works.md)részletes információ: az átmenő hitelesítés funkciójának megismerése.
- [Hibaelhárítás](tshoot-connect-pass-through-authentication.md): megtudhatja, Hogyan oldhatók fel az áteresztő hitelesítési szolgáltatással kapcsolatos gyakori problémák.
- [Biztonsági](how-to-connect-pta-security-deep-dive.md)részletes információk: részletes technikai információkat kaphat az átmenő hitelesítés funkcióról.
- [Azure ad – zökkenőmentes egyszeri bejelentkezés](how-to-connect-sso.md): További információ erről a kiegészítő funkcióról.
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect): használja a Azure Active Directory fórumot az új szolgáltatásokra vonatkozó kérelmek fájljának megjelenítéséhez.
