---
title: Rugalmasság az Azure AD B2C-t használó fejlesztői ajánlott eljárásokkal | Microsoft Docs
description: Rugalmasság az ügyfelek identitásának és hozzáférés-kezelésének fejlesztői bevált eljárásaival Azure AD B2C használatával
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: gargi-sinha
ms.author: gasinh
manager: martinco
ms.reviewer: ''
ms.date: 11/30/2020
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ff7505e7c47b93f32efd9de60463873026247329
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98724925"
---
# <a name="resilience-through-developer-best-practices"></a>Rugalmasság a fejlesztőknek ajánlott eljárások révén

Ebben a cikkben megosztunk néhány olyan megismerést, amely a nagy ügyfelekkel való együttműködés tapasztalatai alapján történik. Ezeket a javaslatokat a szolgáltatások megtervezésében és megvalósításában tekintheti meg.

![A rendszerkép bemutatja a fejlesztői élmény összetevőit](media/resilience-b2c-developer-best-practices/developer-best-practices-architecture.png)

## <a name="use-the-microsoft-authentication-library-msal"></a>A Microsoft Authentication Library (MSAL) használata

A [Microsoft Authentication Library (MSAL)](../develop/msal-overview.md) és a [Microsoft Identity web authentication Library for ASP.net](../develop/reference-v2-libraries.md) egyszerűbbé teszi az alkalmazás által igényelt jogkivonatok beszerzését, kezelését, gyorsítótárazását és frissítését. Ezek a kódtárak kifejezetten a Microsoft-identitás támogatására vannak optimalizálva, beleértve az alkalmazások rugalmasságát javító funkciókat is.

A fejlesztőknek el kell fogadniuk a MSAL legújabb kiadásait, és naprakészen maradhatnak. Lásd: a [hitelesítés és az engedélyezés rugalmasságának javítása](resilience-app-development-overview.md) az alkalmazásokban. Ha lehetséges, kerülje a saját hitelesítési verem megvalósítását, és használjon jól bevált kódtárakat.

## <a name="optimize-directory-reads-and-writes"></a>Könyvtárak olvasásának és írásának optimalizálása

Az Microsoft Azure AD B2C címtárszolgáltatás naponta több milliárd hitelesítést támogat. A szolgáltatás nagy olvasási sebességre van tervezve másodpercenként. Optimalizálja az írásokat a függőségek csökkentése és a rugalmasság növelése érdekében.

### <a name="how-to-optimize-directory-reads-and-writes"></a>A címtár olvasásának és írásának optimalizálása

- **Kerülje a függvényeket a bejelentkezéshez a címtárba**: soha ne hajtson végre írási lehetőséget az egyéni házirendekben az előfeltétel (IF záradék) nélküli bejelentkezéshez. Egy olyan használati eset, amely a bejelentkezéshez szükséges írást igényli, a [felhasználói jelszavak igény szerinti áttelepítése](https://github.com/azure-ad-b2c/user-migration/tree/master/seamless-account-migration). Kerülje az összes olyan forgatókönyvet, amely minden bejelentkezéshez szükséges.

  - A felhasználói úton megjelenő [Előfeltételek](../../active-directory-b2c/userjourneys.md) a következőképpen néznek ki:

  ``
  <Precondition Type="ClaimEquals" ExecuteActionsIf="true"> 
  <Value>requiresMigration</Value>
  ...
  < Precondition/>
  ``
  - A [CAPTCHA-rendszer integrálásával](https://github.com/azure-ad-b2c/samples/tree/master/policies/captcha-integration)kiépítheti a robot vezérelt bejelentkezések elleni rezisztenciát.

  - [Terheléses tesztelési minta](../../active-directory-b2c/best-practices.md#testing) használatával szimulálhatja a regisztrációt és a bejelentkezést. 

- A **szabályozás ismertetése**: a címtár az alkalmazás-és a bérlői szintű szabályozási szabályokat is implementálja. Az olvasási/lekérési, írási/közzétételi, frissítési/PUT és törlési/TÖRLÉSi műveletekhez további díjszabási korlátok tartoznak.

  - Egy írás a bejelentkezés időpontjában új felhasználóknak szóló bejegyzés alá esik, vagy a meglévő felhasználókra kerül.

  - Egy egyéni szabályzat, amely minden bejelentkezéskor létrehoz vagy frissít egy felhasználót, esetleg elérheti az alkalmazás szintjének PUT vagy POST korlátját. Ugyanezek a korlátozások érvényesek a címtárobjektumok Azure AD-n vagy Microsoft Graphon keresztüli frissítésekor. Hasonlóképpen vizsgálja meg a beolvasások számát, hogy az összes bejelentkezés a minimumra legyen beolvasva.

  - Becsülje meg a maximális terhelést a címtárbeli írások arányának előrejelzéséhez és a szabályozás elkerüléséhez. A maximális forgalom becsült értékének tartalmaznia kell a regisztrációhoz, a bejelentkezéshez és a többtényezős hitelesítéshez (MFA) kapcsolódó műveletekre vonatkozó becsléseket. Ügyeljen arra, hogy a Azure AD B2C rendszer és az alkalmazás is tesztelje a maximális forgalmat. Lehetséges, hogy a Azure AD B2C szabályozás nélkül tudja kezelni a terhelést, ha az alsóbb rétegbeli alkalmazások vagy szolgáltatások nem.

  - Ismerje meg és tervezze meg az áttelepítési ütemtervet. Ha a felhasználók áttelepítését tervezi Azure AD B2C Microsoft Graph használatával, vegye figyelembe az alkalmazás-és bérlői korlátokat a felhasználók áttelepítésének befejezéséhez szükséges idő kiszámításához. Ha két alkalmazás használatával bontotta a felhasználói létrehozási feladatot vagy parancsfájlt, használhatja a per Application limitet. Továbbra is a bérlői küszöbérték alatt kell maradnia.

  - Ismerje meg az áttelepítési feladatok más alkalmazásokra gyakorolt hatását. Tekintse át a más függőben lévő alkalmazások által kiszolgált élő forgalmat, így biztosítva, hogy ne okozzon szabályozást a bérlői szinten és az erőforrás-éhezést az élő alkalmazáshoz. További információ: [Microsoft Graph szabályozási útmutató](/graph/throttling).
  
## <a name="extend-token-lifetimes"></a>Jogkivonat élettartamának kiterjesztése

Nem valószínű esemény esetén, ha a Azure AD B2C hitelesítési szolgáltatás nem tudja befejezni az új regisztrációkat és bejelentkezéseket, továbbra is megadhatja a bejelentkezett felhasználók enyhítését. A [konfiguráció](../../active-directory-b2c/configure-tokens.md)segítségével engedélyezheti a már bejelentkezett felhasználók számára, hogy az alkalmazásban észlelt megszakítás nélkül is használhassa az alkalmazást, amíg a felhasználó kijelentkezik az alkalmazásból, vagy a [munkamenet](../../active-directory-b2c/session-behavior.md) időtúllépés miatt nem jár le.

Az üzleti igényeknek és a kívánt Végfelhasználói élménynek meg kell határoznia a tokenek frissítésének gyakoriságát mind a web-, mind az egyoldalas alkalmazások (SPAs) esetében.

### <a name="how-to-extend-token-lifetimes"></a>A jogkivonat élettartamának kiterjesztése

- **Webalkalmazások**: azon webalkalmazások esetében, amelyeken a hitelesítési jogkivonat ellenőrzése a bejelentkezés elején történik, az alkalmazás a munkamenet-cookie-tól függ, hogy továbbra is ki kell-e terjeszteni a munkamenet érvényességét.

  - A felhasználók továbbra is bejelentkezve maradhatnak a működés közbeni munkamenetek bevezetésével, amely a felhasználói tevékenység alapján továbbra is megújítja a munkameneteket Ha hosszú távú jogkivonat-kiállítási leállás áll fenn, a munkamenetek időpontját tovább növelheti az alkalmazás egykori konfigurációja. Tartsa meg a munkamenet élettartamát a maximálisan megengedettnél.

- **Gyógyfürdők**: a Spa a hozzáférési jogkivonatok függ, hogy hívásokat kezdeményez az API-khoz. A SPA hagyományosan az implicit folyamatot használja, amely nem eredményez frissítési jogkivonatot. A SPA rejtett iframe-sel is elvégezhet új jogkivonat-kérelmeket az engedélyezési végponton, ha a böngésző továbbra is aktív munkamenettel rendelkezik a Azure AD B2C. A fürdők esetében van néhány lehetőség, amely lehetővé teszi, hogy a felhasználó továbbra is használhassa az alkalmazást.

  - A hozzáférési jogkivonat érvényességi időtartamának kibővítése az üzleti igények kielégítése érdekében.

  - Hozza létre az alkalmazást, hogy az API-átjárót használja hitelesítési proxyként. Ebben a konfigurációban a SPA hitelesítés nélkül töltődik be, és az API-hívások az API-átjárón történnek. Az API-átjáró bejelentkezési folyamaton keresztül küldi el a [felhasználót egy szabályzat alapján,](https://oauth.net/2/grant-types/authorization-code/) és hitelesíti a felhasználót. Ezt követően az API-átjáró és az ügyfél közötti hitelesítési munkamenet fenntartása egy hitelesítési cookie használatával történik. Az API-k az API-átjáró használatával vannak kiszolgálva az API-átjáró vagy valamilyen más közvetlen hitelesítési módszer, például tanúsítványok, ügyfél-hitelesítő adatok vagy API-kulcsok által beszerzett jogkivonattal.

  - [Telepítse át a Spa-t az implicit támogatásból](https://developer.microsoft.com/identity/blogs/msal-js-2-0-supports-authorization-code-flow-is-now-generally-available/) az [engedélyezési kód engedélyezési kódjába](../../active-directory-b2c/implicit-flow-single-page-application.md) a Code Exchange (PKCE) és az egyéb eredetű erőforrás-megosztás (CORS) támogatása érdekében. Telepítse át az alkalmazást MSAL.js 1. x MSAL.js 2. x verzióra a webalkalmazások rugalmasságának megvalósításához.

  - A mobileszközök esetében ajánlott kiterjeszteni a frissítési és a hozzáférési jogkivonat élettartamát is.

- **Háttér-vagy Service-alkalmazások**: mivel a háttér-(Daemon-) alkalmazások nem interaktívak, és nem felhasználói környezetben vannak, a jogkivonat-lopás várhatóan jelentősen csökken. Javasoljuk, hogy egyensúlyt teremteni a biztonság és az élettartam között, és állítsa be a hosszú jogkivonat élettartamát.

## <a name="configure-single-sign-on"></a>Egyszeri bejelentkezés konfigurálása

[Egyszeri bejelentkezéssel (SSO)](../manage-apps/what-is-single-sign-on.md)a felhasználók egyetlen fiókkal jelentkeznek be, és több alkalmazáshoz is hozzáférhetnek. Az alkalmazás lehet web-, mobil-vagy egy egyoldalas alkalmazás (SPA), függetlenül a platformtól vagy a tartománynévtől. Amikor a felhasználó először bejelentkezik egy alkalmazásba, Azure AD B2C megőrzi a [cookie-alapú munkamenetet](../../active-directory-b2c/session-behavior.md).

Későbbi hitelesítési kérelmek esetén Azure AD B2C beolvassa és érvényesíti a cookie-alapú munkamenetet, és a felhasználó értesítése nélkül kiadja a hozzáférési jogkivonatot. Ha az egyszeri bejelentkezés korlátozott hatókörrel van konfigurálva egy házirendben vagy alkalmazásban, akkor a későbbiekben más szabályzatokhoz és alkalmazásokhoz való hozzáféréshez új hitelesítés szükséges.

### <a name="how-to-configure-sso"></a>Az egyszeri bejelentkezés konfigurálása

[Konfigurálja az egyszeri bejelentkezést](../hybrid/how-to-connect-sso-quick-start.md) úgy, hogy a bérlői szintű legyen (alapértelmezett), így több alkalmazás és felhasználói folyamat is megosztható a bérlőn ugyanazzal a felhasználói munkamenettel. A bérlői szintű konfiguráció a legtöbb rugalmasságot biztosítja a friss hitelesítéshez.  

## <a name="safe-deployment-practices"></a>Biztonságos üzembehelyezési gyakorlatok

A leggyakoribb adatzavarok a kód és a konfiguráció változásai. A folyamatos integráció és a folyamatos teljesítés (vel) folyamatainak és eszközeinek bevezetése nagy léptékű gyors üzembe helyezést és a tesztelés és az üzembe helyezés során csökkenti az emberi hibákat. VEL elfogadása a hibák csökkentése, hatékonyság és konzisztencia érdekében. Az [Azure-folyamatok](/azure/devops/pipelines/apps/cd/azure/cicd-data-overview) például a vel.

## <a name="web-application-firewall"></a>Webalkalmazási tűzfal

Az alkalmazások védelmet biztosíthat az ismert biztonsági rések, például az elosztott szolgáltatásmegtagadási (DDoS) támadások, az SQL-injektálások, a helyek közötti parancsfájlkezelés, a távoli kódfuttatást és sok más, a [OWASP Top 10](https://owasp.org/www-project-top-ten/)-ben dokumentált módon. A webalkalmazási tűzfal (WAF) üzembe helyezése megvédheti a közös támadásokat és biztonsági réseket.

- Használja az Azure [WAF](../../web-application-firewall/overview.md), amely központosított védelmet biztosít a támadásokkal szemben.

- A WAF az Azure AD [Identity Protection és a feltételes hozzáférés használatával biztosít többrétegű védelmet a](../../active-directory-b2c/conditional-access-identity-protection-overview.md) Azure ad B2C használatakor.  

## <a name="secrets-rotation"></a>Titkos kulcsok rotálása

A Azure AD B2C az alkalmazásokhoz, API-khoz, házirendekhez és titkosításhoz használ titkokat. A titkos kulcsok biztonságos hitelesítés, külső interakciók és tárterület. A National Institute of Standards and Technology (NIST) meghívja azt az időtartományt, amelyben egy adott kulcsot a cryptoperiod megbízható entitások használhatnak. Válassza ki a megfelelő hosszúságú [cryptoperiod](https://csrc.nist.gov/publications/detail/sp/800-57-part-1/rev-5/final) az üzleti igények kielégítése érdekében. A fejlesztőknek manuálisan kell megadniuk a lejáratot, és a titkokat a lejárat előtt is el kell forgatni.

### <a name="how-to-implement-secret-rotation"></a>A titkos elforgatás megvalósítása

- [Felügyelt identitások](../managed-identities-azure-resources/overview.md) használata a támogatott erőforrásokhoz az Azure ad-hitelesítést támogató bármely szolgáltatáshoz való hitelesítéshez. Felügyelt identitások használatakor az erőforrásokat automatikusan kezelheti, beleértve a hitelesítő adatok rotációját is.

- Készítsen leltárt a Azure AD B2C [konfigurált kulcsokról és tanúsítványokról](../../active-directory-b2c/policy-keys-overview.md) . Ez a lista valószínűleg az egyéni házirendekben, az [API](../../active-directory-b2c/secure-rest-api.md)-kon, az aláíró azonosító jogkivonatában és az SAML-tanúsítványokban használt kulcsokat tartalmazza.

- A vel használatával elforgathatja azokat a titkokat, amelyek hamarosan lejárnak a várható főszezontól számított két hónapon belül. A tanúsítványhoz tartozó titkos kulcsok ajánlott maximális cryptoperiod egy év.

- Proaktív módon figyelheti és elforgathatja az API-hozzáférési hitelesítő adatokat, például a jelszavakat és a tanúsítványokat.

## <a name="test-rest-apis"></a>REST API-k tesztelése

A rugalmasság kontextusában a REST API-k tesztelésének tartalmaznia kell a – HTTP-kódok, a válasz adattartalma, a fejlécek és a teljesítmény ellenőrzését. A tesztelés nem tartalmazhat csak a Happy Path-teszteket, de azt is ellenőrzi, hogy az API szabályosan kezeli-e a problémákat.

### <a name="how-to-test-apis"></a>API-k tesztelése

Javasoljuk, hogy a tesztelési terv [átfogó API-teszteket](../../active-directory-b2c/best-practices.md#testing)tartalmazzon. Ha az előléptetés vagy az üdülési forgalom miatt várhatóan nagy lendületet szeretne, a terheléses tesztelést az új becslésekkel kell módosítania. Az API-k és Content Delivery Network (CDN) terheléses tesztelését végezheti el fejlesztői környezetben, nem pedig éles környezetben.

## <a name="next-steps"></a>Következő lépések

- [Rugalmassági erőforrások Azure AD B2C-fejlesztőknek](resilience-b2c.md)
  - [Rugalmas végfelhasználói élmény](resilient-end-user-experience.md)
  - [Rugalmas felületek külső folyamatokkal](resilient-external-processes.md)
  - [Rugalmasság a monitorozás és az elemzés révén](resilience-with-monitoring-alerting.md)
- [Rugalmasság kialakítása a hitelesítési infrastruktúrában](resilience-in-infrastructure.md)
- [A hitelesítés és az engedélyezés rugalmasságának fokozása az alkalmazásokban](resilience-app-development-overview.md)