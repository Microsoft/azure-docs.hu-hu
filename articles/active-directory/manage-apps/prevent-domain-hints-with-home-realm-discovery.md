---
title: A bejelentkezés automatikus gyorsulásának letiltása az Azure AD-ben a Kezdőlap tartomány-felderítési házirend használatával
description: Ismerje meg, hogyan akadályozhatja meg domain_hint az automatikus gyorsítást az összevont IDP.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/12/2021
ms.author: hirsin
ms.openlocfilehash: 67cb1003e139a085d45d01617cd44647bad420f5
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101693125"
---
# <a name="disable-auto-acceleration-to-a-federated-idp-during-user-sign-in-with-home-realm-discovery-policy"></a>Az automatikus gyorsítás letiltása egy összevont IDENTITÁSSZOLGÁLTATÓ a Kezdőlap tartomány-felderítési házirenddel való felhasználói bejelentkezés során

A [Kezdőlap tartományának felderítési szabályzata](https://docs.microsoft.com/graph/api/resources/homeRealmDiscoveryPolicy) (HRD) a rendszergazdák számára több módon szabályozza a felhasználók hitelesítésének módját és helyét. A `domainHintPolicy` HRD szabályzat szakasza segítséget nyújt az összevont felhasználók átirányításához a felhőben felügyelt [](../authentication/howto-authentication-passwordless-security-key.md)hitelesítő adatokkal (például:...), ezzel biztosítva, hogy mindig felkeresik az Azure ad bejelentkezési oldalát, és a tartományi javaslatok miatt nem gyorsítják fel automatikusan az összevont identitásszolgáltató.

Erre a szabályzatra olyan helyzetekben van szükség, amikor a rendszergazda nem tudja ellenőrizni vagy frissíteni a tartományhoz való hozzáadást a bejelentkezés során.  Például `outlook.com/contoso.com` elküldi a felhasználót egy bejelentkezési oldalra a `&domain_hint=contoso.com` hozzáfűzött paraméterrel, hogy automatikusan felgyorsítsa a felhasználót a tartomány összevont identitásszolgáltató `contoso.com` . Az összevont IDENTITÁSSZOLGÁLTATÓ eljuttatott felügyelt hitelesítő adatokkal rendelkező felhasználók nem jelentkezhetnek be a felügyelt hitelesítő adataik használatával, így csökkentve a biztonságot, és bosszantó bejelentkezési élményekkel kezelhetik a felhasználókat. A felügyelt hitelesítő adatokkal rendelkező rendszergazdáknak [is be kell állítania ezt a házirendet](#suggested-use-within-a-tenant) annak biztosításához, hogy a felhasználók mindig használhassák a felügyelt hitelesítő adataikat.

## <a name="domainhintpolicy-details"></a>DomainHintPolicy részletei

A HRD szabályzat DomainHintPolicy szakasza egy JSON-objektum, amely lehetővé teszi, hogy a rendszergazda bizonyos tartományokat és alkalmazásokat is letiltson a tartomány-emlékeztető használatával.  Ez a funkció azt jelzi, hogy az Azure AD bejelentkezési lapja úgy viselkedik, mintha a `domain_hint` bejelentkezési kérelem egyik paramétere sem volt jelen.

### <a name="the-respect-and-ignore-policy-sections"></a>A szabályzatok betartása és figyelmen kívül hagyása

|Section | Értelmezés | Értékek |
|--------|---------|--------|
|`IgnoreDomainHintForDomains` |Ha ezt a tartományi emlékeztetőt elküldi a kérelemben, hagyja figyelmen kívül. |Tartományi címek tömbje (például `contoso.com` ). Szintén támogatja `all_domains`|
|`RespectDomainHintForDomains`| Ha a kérelemben ezt a tartományi emlékeztetőt küldi el a rendszer, akkor is vegye figyelembe, `IgnoreDomainHintForApps` hogy a kérésben szereplő alkalmazás ne legyen automatikusan felgyorsítva. Ezzel lelassíthatja az elavult tartományi útmutatók bevezetését a hálózaton belül – jelezheti, hogy egyes tartományokat továbbra is fel kell gyorsítani. | Tartományi címek tömbje (például `contoso.com` ). Szintén támogatja `all_domains`|
|`IgnoreDomainHintForApps`| Ha az alkalmazástól érkező kérelem egy tartományi emlékeztetővel érkezik, hagyja figyelmen kívül. | Alkalmazás-azonosítók (GUID-EK) tömbje. Szintén támogatja `all_apps`|
|`RespectDomainHintForApps` |Ha az alkalmazástól érkező kérelem egy tartományi emlékeztetőt tartalmaz, akkor is vegye figyelembe, `IgnoreDomainHintForDomains` hogy az adott tartományt is tartalmazza. Annak biztosítására szolgál, hogy egyes alkalmazások továbbra is működőképesek legyenek, ha a felderítése tartományi javaslatok nélkül történik. | Alkalmazás-azonosítók (GUID-EK) tömbje. Szintén támogatja `all_apps`|

### <a name="policy-evaluation"></a>Szabályzat kiértékelése

A DomainHintPolicy logika minden olyan bejövő kérelemnél fut, amely egy tartományi emlékeztetőt tartalmaz, és felgyorsítja a kérésben szereplő két adat – a tartomány a tartományi útmutatóban, valamint az ügyfél-azonosító (az alkalmazás) alapján. Egy tartomány vagy alkalmazás rövid – "tiszteletben" tartása elsőbbséget élvez az utasítással szemben az adott tartományra vagy alkalmazásra vonatkozó tartományi célzásra.

1. Ha nincs tartományi tipp-házirend, vagy ha a 4 szakaszban egyik sem hivatkozik az alkalmazásra vagy a tartományi javaslatra, [a rendszer kiértékeli a többi HRD-házirendet](configure-authentication-for-federated-users-portal.md#priority-and-evaluation-of-hrd-policies).
1. Ha egy vagy több (vagy mindkettő) `RespectDomainHintForApps` vagy `RespectDomainHintForDomains` szakasz tartalmazza a kérelemben szereplő alkalmazást vagy a tartományhoz tartozó emlékeztetőt, akkor a rendszer automatikusan felgyorsítja a felhasználót az összevont identitásszolgáltató.
1. Ha egy vagy több (vagy mindkettő) `IgnoreDomainHintsForApps` vagy `IgnoreDomainHintsForDomains` hivatkozik rá az alkalmazásra, illetve a kérelemben szereplő tartományra, és nem hivatkoznak a "tisztelet" szakaszra, akkor a kérés nem lesz automatikusan felgyorsítva, és a felhasználó az Azure ad bejelentkezési oldalán marad, hogy adjon meg egy felhasználónevet.

Miután a felhasználó megadta a felhasználónevet a bejelentkezési oldalon, használhatja a felügyelt hitelesítő adatait, ha vannak regisztrálva.  Ha úgy dönt, hogy nem használ felügyelt hitelesítő adatokat, vagy nincsenek regisztrálva, akkor a rendszer a szokásos módon a hitelesítő adatok bevitelére vonatkozó összevont IDENTITÁSSZOLGÁLTATÓ fogja elvégezni.

## <a name="suggested-use-within-a-tenant"></a>Javasolt használat a bérlőn belül

Az összevont tartományok rendszergazdáinak be kell állítania a HRD szabályzat ezen szakaszát egy négyéves tervben. A terv célja, hogy végül a bérlő összes felhasználója számára elérhető legyen a saját felügyelt hitelesítő adatai, függetlenül a tartománytól vagy az alkalmazástól. mentse azokat az alkalmazásokat, amelyek nem rendelkeznek a használattal rögzített függőségekkel `domain_hint` .  Ez a csomag segít a rendszergazdáknak megkeresni ezeket az alkalmazásokat, felvenni őket az új szabályzat alól, és folytatni a többi bérlőre való váltást.

1. Válasszon ki egy tartományt, hogy először ezt a változást állítsa be.  Ez lesz a tesztelési tartománya, ezért válasszon egyet, amely jobban megoldható lehet az UX-ben történt változásokkal kapcsolatban (azaz egy másik bejelentkezési oldalt lát).  Ez figyelmen kívül hagyja a tartománynevet használó összes alkalmazás összes tartományi emlékeztetőjét. A szabályzat [beállítása](#configuring-policy-through-graph-explorer) a bérlőben – alapértelmezett HRD házirend:

```json
 "DomainHintPolicy": { 
    "IgnoreDomainHintForDomains": [ "testDomain.com" ], 
    "RespectDomainHintForDomains": [], 
    "IgnoreDomainHintForApps": [], 
    "RespectDomainHintForApps": [] 
} 
```

2. Visszajelzés küldése a tartomány felhasználóinak. Gyűjtsön adatokat azokról az alkalmazásokról, amelyek a változás eredményeképpen kitörtek – függőségük van a tartományhoz való használattal, és frissíteni kell. Egyelőre adja hozzá őket a következő `RespectDomainHintForApps` szakaszhoz:

```json
 "DomainHintPolicy": { 
    "IgnoreDomainHintForDomains": [ "testDomain.com" ], 
    "RespectDomainHintForDomains": [], 
    "IgnoreDomainHintForApps": [], 
    "RespectDomainHintForApps": ["app1-clientID-Guid", "app2-clientID-Guid] 
} 
```

3. Folytassa a szabályzat bevezetésének új tartományokra való kiterjesztését, és gyűjtsön további visszajelzést.

```json
 "DomainHintPolicy": { 
    "IgnoreDomainHintForDomains": [ "testDomain.com", "otherDomain.com", "anotherDomain.com"], 
    "RespectDomainHintForDomains": [], 
    "IgnoreDomainHintForApps": [], 
    "RespectDomainHintForApps": ["app1-clientID-Guid", "app2-clientID-Guid] 
} 
```

4. Fejezze be a bevezetést – a minden tartományra kiterjedő célt, amely alól a továbbiakban is fel kell gyorsítani a következőket:

```json
 "DomainHintPolicy": { 
    "IgnoreDomainHintForDomains": [ "*" ], 
    "RespectDomainHintForDomains": ["guestHandlingDomain.com"], 
    "IgnoreDomainHintForApps": [], 
    "RespectDomainHintForApps": ["app1-clientID-Guid", "app2-clientID-Guid] 
} 
```

Miután a 4. lépés elvégezte az összes felhasználót, kivéve a (z `guestHandlingDomain.com` ) rendszert, bejelentkezhet az Azure ad bejelentkezési oldalára, még akkor is, ha a tartományi javaslatok egyébként egy összevont identitásszolgáltató automatikus gyorsítását okozzák.  Ez alól kivételt képez, ha a bejelentkezést kérő alkalmazás az egyik kivétel alá esik – ezen alkalmazások esetében az összes tartományi mutatót továbbra is elfogadjuk.

## <a name="configuring-policy-through-graph-explorer"></a>Házirend konfigurálása a Graph Explorerben

A [HRD házirendet](https://docs.microsoft.com/graph/api/resources/homeRealmDiscoveryPolicy) a szokásos módon állítsa be Microsoft Graph használatával.  

1. Adja meg a Policy. ReadWrite. ApplicationConfiguration engedélyt a [Graph Explorerben](https://developer.microsoft.com/graph/graph-explorer).  
1. Az URL-cím használata `https://graph.microsoft.com/v1.0/policies/homeRealmDiscoveryPolicies`
1. Tegye közzé az új szabályzatot erre az URL-címre, vagy a javítást, `/policies/homerealmdiscoveryPolicies/{policyID}` Ha felülírja egy meglévőt.

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

A Graph használatakor ügyeljen arra, hogy perjel használatával elkerülje a `Definition` JSON szakaszt.  

`isOrganizationDefault` igaznak kell lennie, de a displayName és a definíció is változhat.

## <a name="next-steps"></a>Következő lépések

* [Jelszó nélküli biztonsági kulcs bejelentkezésének engedélyezése](../authentication/howto-authentication-passwordless-security-key.md)
* [Jelszó nélküli bejelentkezés engedélyezése a Microsoft Authenticator alkalmazással](../authentication/howto-authentication-passwordless-phone.md)
