---
title: Bejelentkezés automatikus gyorsításának megakadályozása az Azure AD-ban a kezdőlap-felderítési szabályzat használatával
description: Megtudhatja, hogyan akadályozhatja meg domain_hint az automatikus gyorsítást az összevont IP-kbe.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 02/12/2021
ms.author: iangithinji
ms.reviewer: hirsin
ms.openlocfilehash: b89e0e1c8bd8109fac8b4b7c05a845a3e234b617
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107375550"
---
# <a name="disable-auto-acceleration-to-a-federated-idp-during-user-sign-in-with-home-realm-discovery-policy"></a>Az automatikus gyorsítás letiltása összevont idP-re a felhasználói bejelentkezés során a Home Realm Discovery szabályzatával

[A Kezdőlap tartományfelderítési szabályzata](/graph/api/resources/homeRealmDiscoveryPolicy) (HRD) több módszert is kínál a rendszergazdáknak a felhasználók hitelesítésének módja és helyének szabályozása érdekében. A HRD-szabályzat szakasza segít az összevont felhasználók felhőben felügyelt hitelesítő adatokba (például a FIDO-ba) való áttelepítésében, mivel biztosítja, hogy mindig felkeresik az Azure AD bejelentkezési oldalát, és a tartományi tippek miatt ne gyorsítsák fel automatikusan az összevont `domainHintPolicy` idP-t. [](../authentication/howto-authentication-passwordless-security-key.md)

Erre a szabályzatra olyan helyzetekben van szükség, amikor az alkalmazások nem tudják szabályozni vagy frissíteni a tartományi tippek hozzáadását a bejelentkezés során.  A például egy olyan bejelentkezési oldalra küldi a felhasználót, amely a paramétert fűzi hozzá, hogy automatikusan felgyorsítsa a felhasználót közvetlenül a tartomány összevont `outlook.com/contoso.com` `&domain_hint=contoso.com` idP-je `contoso.com` számára. Az összevont idP-nek küldött felügyelt hitelesítő adatokkal rendelkező felhasználók nem tudnak bejelentkezni a felügyelt hitelesítő adataik használatával, ami csökkenti a biztonságot, és frusztrálja a felhasználókat a véletlenszerű bejelentkezési élményekkel. A rendszergazdáknak a [](#suggested-use-within-a-tenant) felügyelt hitelesítő adatokat is be kell állítaniuk, hogy a felhasználók mindig használják a felügyelt hitelesítő adataikat.

## <a name="domainhintpolicy-details"></a>DomainHintPolicy részletei

A HRD-szabályzat DomainHintPolicy szakasza egy JSON-objektum, amely lehetővé teszi, hogy a rendszergazda letiltson bizonyos tartományokat és alkalmazásokat a tartománymutató használata ellen.  Funkcionálisan ez arra utasítja az Azure AD bejelentkezési oldalát, hogy úgy viselkedjen, mintha a bejelentkezési kérelem egyik paramétere `domain_hint` nem lenne jelen.

### <a name="the-respect-and-ignore-policy-sections"></a>A Figyelembe és figyelmen kívül hagyás szabályzat szakasz

|Section | Értelmezés | Értékek |
|--------|---------|--------|
|`IgnoreDomainHintForDomains` |Ha a kérésben ez a tartománymutató van elküldve, hagyja figyelmen kívül. |Tartománycímek tömbje (például `contoso.com` ). Emellett támogatja a `all_domains`|
|`RespectDomainHintForDomains`| Ha a kérésben ez a tartománymutató van elküldve, akkor is tartsa tiszteletben, ha azt jelzi, hogy a kérésben található alkalmazás nem `IgnoreDomainHintForApps` gyorsulhat automatikusan. Ezzel lelassíthatja a hálózaton belüli elavult tartománymutatók bevezetési folyamatát – jelezheti, hogy egyes tartományokat továbbra is fel kell gyorsítani. | Tartománycímek tömbje (például `contoso.com` ). Emellett támogatja a `all_domains`|
|`IgnoreDomainHintForApps`| Ha az alkalmazástól származó kérés tartományi tippet is ad, hagyja figyelmen kívül. | Alkalmazás-azonosítók (GUID-k) tömbje. Emellett támogatja a `all_apps`|
|`RespectDomainHintForApps` |Ha az alkalmazástól származó kérés tartományi tippet tartalmaz, akkor is tartsa tiszteletben, ha az ebbe a `IgnoreDomainHintForDomains` tartományba tartozik. Arra használható, hogy bizonyos alkalmazások továbbra is megfelelően tudjanak dolgozni, ha azt tapasztalja, hogy tartománymutatók nélkül törnek el. | Alkalmazás-azonosítók (GUID-k) tömbje. Emellett támogatja a `all_apps`|

### <a name="policy-evaluation"></a>Szabályzatok kiértékelése

A DomainHintPolicy logika minden olyan bejövő kérésen fut, amely tartománymutatót tartalmaz, és a kérés két adata – a tartománymutató tartománya és az ügyfél-azonosító (az alkalmazás) alapján gyorsul. Röviden: egy tartományra vagy alkalmazásra vonatkozó "Figyelembe" utasítás elsőbbséget élvez az adott tartományra vagy alkalmazásra vonatkozó tartománymutató figyelmen kívül hagyása utasítással.

1. Tartománymutató-szabályzat hiányában, vagy ha a 4 szakasz egyike sem hivatkozik az említett alkalmazásra vagy tartománymutatóra, a rendszer a [többi HRD-szabályzatot is kiértékeli.](configure-authentication-for-federated-users-portal.md#priority-and-evaluation-of-hrd-policies)
1. Ha a vagy a szakasz egyik (vagy mindkét) része tartalmazza az alkalmazás- vagy tartománymutatót a kérésben, akkor a felhasználó automatikusan fel lesz gyorsulva az összevont `RespectDomainHintForApps` `RespectDomainHintForDomains` idP-be, amint a rendszer kéri.
1. Ha a vagy az egyik (vagy mindkettő) az alkalmazásra vagy a tartománymutatóra hivatkozik a kérésben, és a "Tiszteletben" szakaszok nem hivatkoznak rá, akkor a kérés nem lesz automatikusan felgyorsítva, és a felhasználó az Azure AD bejelentkezési oldalán marad, és meg kell adnia `IgnoreDomainHintsForApps` egy felhasználónevet. `IgnoreDomainHintsForDomains`

Miután a felhasználó megadta a felhasználónevet a bejelentkezési oldalon, használhatja a felügyelt hitelesítő adatait, ha regisztrált már.  Ha úgy dönt, hogy nem használ felügyelt hitelesítő adatokat, vagy nincs regisztrálva, a rendszer a szokásos módon a hitelesítő adatok megadásához az összevont idP-hez fogja használni őket.

## <a name="suggested-use-within-a-tenant"></a>Bérlőn belüli javasolt használat

Az összevont tartományok rendszergazdái egy négyfázisú tervben állíthatják be a HRD-szabályzat ezen szakaszát. Ennek a tervnek az a célja, hogy a bérlő összes felhasználója használva legyen a felügyelt hitelesítő adataik tartománytól vagy alkalmazástól függetlenül, mentse azokat az alkalmazásokat, amelyek erősen függnek a `domain_hint` használattól.  Ez a csomag segít a rendszergazdáknak megtalálni ezeket az alkalmazásokat, mentesíteni őket az új szabályzat alól, és folytatni a módosítás a bérlő többi részét.

1. Válasszon egy tartományt a módosítás kezdeti bevezetéséhez.  Ez lesz a teszttartománya, ezért válasszon egy olyant, amely a felhasználói felület változásaira jobban reagál (azaz egy másik bejelentkezési oldalt lát).  Ez figyelmen kívül hagyja az összes olyan alkalmazás tartományi tippjét, amely ezt a tartománynevet használja. [Állítsa](#configuring-policy-through-graph-explorer) be ezt a szabályzatot a bérlő alapértelmezett HRD-szabályzatában:

```json
 "DomainHintPolicy": { 
    "IgnoreDomainHintForDomains": [ "testDomain.com" ], 
    "RespectDomainHintForDomains": [], 
    "IgnoreDomainHintForApps": [], 
    "RespectDomainHintForApps": [] 
} 
```

2. Gyűjtsön visszajelzést a teszttartomány felhasználóitól. Gyűjtse össze az olyan alkalmazások adatait, amelyek ennek a változásnak az következtében felsülnek – függőségi viszonyban vannak a tartománymutatók használatáról, és frissíteni kell őket. Most adja hozzá őket a `RespectDomainHintForApps` szakaszhoz:

```json
 "DomainHintPolicy": { 
    "IgnoreDomainHintForDomains": [ "testDomain.com" ], 
    "RespectDomainHintForDomains": [], 
    "IgnoreDomainHintForApps": [], 
    "RespectDomainHintForApps": ["app1-clientID-Guid", "app2-clientID-Guid] 
} 
```

3. Folytassa a szabályzat új tartományokra való kiszélesedő bővítését, és gyűjtsön további visszajelzéseket.

```json
 "DomainHintPolicy": { 
    "IgnoreDomainHintForDomains": [ "testDomain.com", "otherDomain.com", "anotherDomain.com"], 
    "RespectDomainHintForDomains": [], 
    "IgnoreDomainHintForApps": [], 
    "RespectDomainHintForApps": ["app1-clientID-Guid", "app2-clientID-Guid] 
} 
```

4. A bevezetés befejezése – az összes tartomány megcélzása, a további gyorsítást folytató tartományok mentesítve:

```json
 "DomainHintPolicy": { 
    "IgnoreDomainHintForDomains": [ "*" ], 
    "RespectDomainHintForDomains": ["guestHandlingDomain.com"], 
    "IgnoreDomainHintForApps": [], 
    "RespectDomainHintForApps": ["app1-clientID-Guid", "app2-clientID-Guid] 
} 
```

Miután a 4. lépés befejeződött, az összes felhasználó bejelentkezhet az Azure AD bejelentkezési oldalán, kivéve az összes felhasználót, még akkor is, ha a tartománymutatók automatikusan gyorsítást okoznak egy összevont `guestHandlingDomain.com` idP-re.  Ez alól kivételt képez, ha a bejelentkezést kérő alkalmazás az egyik kivételt képezte – ezekhez az alkalmazásokhoz minden tartományi tipp továbbra is el lesz fogadva.

## <a name="configuring-policy-through-graph-explorer"></a>Szabályzat konfigurálása a Graph Explorerrel

Állítsa be [a HRD-szabályzatot](/graph/api/resources/homeRealmDiscoveryPolicy) a szokásos módon a Microsoft Graph.  

1. Adja meg a Policy.ReadWrite.ApplicationConfiguration engedélyt a [Graph Explorerben.](https://developer.microsoft.com/graph/graph-explorer)  
1. Az URL-cím használata `https://graph.microsoft.com/v1.0/policies/homeRealmDiscoveryPolicies`
1. TEGYE közzé az új szabályzatot erre az URL-címre, vagy a PATCH-et, ha felülír `/policies/homerealmdiscoveryPolicies/{policyID}` egy meglévőt.

POST vagy PATCH tartalma:

```json
{
    "displayName":"Home Realm Discovery Domain Hint Exclusion Policy",
    "definition":[
        "{\"HomeRealmDiscoveryPolicy\" : {\"DomainHintPolicy\": { \"IgnoreDomainHintForDomains\": [ \"Contoso.com\" ], \"RespectDomainHintForDomains\": [], \"IgnoreDomainHintForApps\": [\"sample-guid-483c-9dea-7de4b5d0a54a\"], \"RespectDomainHintForApps\": [] } } }"
    ],
    "isOrganizationDefault":true
}
```

A Graph használata esetén mindenképpen használjon perjeleket `Definition` a JSON-szakasz kivédésében.  

`isOrganizationDefault` Igaznak kell lennie, de a displayName és a definíció változhat.

## <a name="next-steps"></a>Következő lépések

* [Jelszó nélküli biztonsági kulcsos bejelentkezés engedélyezése](../authentication/howto-authentication-passwordless-security-key.md)
* [Jelszó nélküli bejelentkezés engedélyezése a Microsoft Authenticator alkalmazással](../authentication/howto-authentication-passwordless-phone.md)