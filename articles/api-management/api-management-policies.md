---
title: Azure API Management-szabályzatok | Microsoft Docs
description: Ismerkedés az Azure API Management szolgáltatásban elérhető szabályzatokkal. A házirendek lehetővé teszik a közzétevő számára az API viselkedésének módosítását a konfiguráción keresztül.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/17/2021
ms.author: apimpm
ms.openlocfilehash: e809efa9da32da5fe9ca296608c602e770f78265
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103562348"
---
# <a name="api-management-policies"></a>API Management házirendek
Ez a szakasz a következő API Management szabályzatokra mutató hivatkozást tartalmaz. A házirendek hozzáadásával és konfigurálásával kapcsolatos információkért lásd: [szabályzatok API Management](api-management-howto-policies.md).

 A szabályzatok a rendszer hatékony funkciója, amely lehetővé teszi, hogy a közzétevő megváltoztassa az API viselkedését a konfiguráción keresztül. A szabályzatok olyan utasítások gyűjteményei, amelyeket az API-k kérelmén vagy válaszán egymás után hajtanak végre. A népszerű utasítások közé tartoznak az XML-ből a JSON-ra való konvertálás és a hívások gyakoriságának korlátozása, hogy korlátozzák a beérkező hívások mennyiségét a fejlesztőtől. Számos további szabályzat érhető el a mezőben.

 A házirend-kifejezéseket attribútumértékekként vagy szövegértékekként lehet használni bármelyik API Management házirendben, hacsak a házirend másként nem rendelkezik. Néhány házirend, például a [Vezérlés folyamata](api-management-advanced-policies.md#choose) és a [Változó beállítása](api-management-advanced-policies.md#set-variable) házirend-kifejezéseken alapul. További információ: [Speciális szabályzatok](api-management-advanced-policies.md#AdvancedPolicies) és [Szabályzatkifejezések](api-management-policy-expressions.md).

##  <a name="policies"></a><a name="ProxyPolicies"></a> Szabályzatok

-   [Hozzáférés-korlátozási szabályzatok](api-management-access-restriction-policies.md#AccessRestrictionPolicies)
    -   [Http](api-management-access-restriction-policies.md#CheckHTTPHeader) -fejléc keresése – egy HTTP-fejléc létezésének és/vagy értékének betartatása.
    -   A [hívások sebességének korlátozása előfizetéssel](api-management-access-restriction-policies.md#LimitCallRate) – MEGAKADÁLYOZZA az API-használat csúcsait a hívások sebességének korlátozásával, előfizetések alapján.
    -   A [hívások gyakoriságának korlátozása kulcs szerint](api-management-access-restriction-policies.md#LimitCallRateByKey) – MEGAKADÁLYOZZA az API-használat csúcsait a hívások sebességének korlátozásával, kulcs alapján.
    -   A [hívó IP](api-management-access-restriction-policies.md#RestrictCallerIPs) -címeinek korlátozása (engedélyezi vagy megtagadja) a HÍVÁSOKAT adott IP-címekről és/vagy címtartományból.
    -   [Használati kvóta beállítása előfizetéssel](api-management-access-restriction-policies.md#SetUsageQuota) – lehetővé teszi, hogy előfizetések alapján kikényszerítse a megújítható vagy az élettartam szerinti hívások mennyiségét és/vagy sávszélesség-kvótáját.
    -   [Használati kvóta beállítása kulccsal](api-management-access-restriction-policies.md#SetUsageQuotaByKey) – lehetővé teszi a megújítható vagy élettartamos hívások mennyiségi és/vagy sávszélesség-kvótájának kikényszeríthető kulcs alapján.
    -   [JWT ellenőrzése](api-management-access-restriction-policies.md#ValidateJWT) – egy adott http-fejlécből vagy egy megadott lekérdezési paraméterből kinyert JWT létezését és érvényességét kényszeríti ki.
-   [Speciális szabályzatok](api-management-advanced-policies.md#AdvancedPolicies)
    -   [Vezérlési folyamat](api-management-advanced-policies.md#choose) – a rendszer a logikai kifejezések kiértékelése alapján feltételesen alkalmazza a házirend-utasításokat.
    -   [Továbbítási kérelem](api-management-advanced-policies.md#ForwardRequest) – továbbítja a kérést a háttér-szolgáltatásnak.
    -   [Egyidejűség korlátozása](api-management-advanced-policies.md#LimitConcurrency) – megakadályozza, hogy a befoglalt szabályzatok a megadott számú kérelemnél több időpontban legyenek végrehajtva.
    -   [Eseménynapló az Event hub](api-management-advanced-policies.md#log-to-eventhub) -ba – az üzeneteket a megadott formátumban küldi el egy adatgyűjtő entitás által definiált üzenet céljára.
    -   [Modell válasza](api-management-advanced-policies.md#mock-response) – megszakítja a folyamat végrehajtását, és egy kigúnyolt választ ad vissza közvetlenül a hívónak.
    -   [Újrapróbálkozás](api-management-advanced-policies.md#Retry) – újrapróbálkozik a mellékelt házirend-utasítások végrehajtásával, ha a feltétel teljesül. A végrehajtás a megadott időintervallumokban és a megadott újrapróbálkozások számával megismétlődik.
    -   [Visszatérési válasz](api-management-advanced-policies.md#ReturnResponse) – megszakítja a folyamat végrehajtását, és a megadott választ közvetlenül a hívónak adja vissza.
    -   [Egyirányú kérelem küldése](api-management-advanced-policies.md#SendOneWayRequest) – kérés küldése a megadott URL-címre a válaszra való várakozás nélkül.
    -   [Kérelem küldése](api-management-advanced-policies.md#SendRequest) – kérelem küldése a megadott URL-címre.
    -   [Http-proxy beállítása](api-management-advanced-policies.md#SetHttpProxy) – lehetővé teszi a továbbított kérések továbbítását egy http-proxyn keresztül.
    -   [Változó beállítása](api-management-advanced-policies.md#set-variable) – megtarthat egy értéket egy nevesített környezeti változóban a későbbi hozzáférés érdekében.
    -   [Kérelem módszerének beállítása](api-management-advanced-policies.md#SetRequestMethod) – lehetővé teszi a kérések http-metódusának módosítását.
    -   [Állapotkód beállítása](api-management-advanced-policies.md#SetStatus) – a HTTP-állapotkódot a megadott értékre módosítja.
    -   [Trace](api-management-advanced-policies.md#Trace) – egyéni nyomkövetéseket ad az [API Inspector](./api-management-howto-api-inspector.md) kimenetéhez, Application Insights telemetriáiról és az erőforrás-naplókhoz.
    -   [Várakozás](api-management-advanced-policies.md#Wait) a befoglalt [küldési kérelemre](api-management-advanced-policies.md#SendRequest), az [érték beolvasása a gyorsítótárból](api-management-caching-policies.md#GetFromCacheByKey), vagy a folytatás előtt a flow-szabályzatok [ellenőrzése](api-management-advanced-policies.md#choose) .
-   [Hitelesítési szabályzatok](api-management-authentication-policies.md#AuthenticationPolicies)
    -   Egyszerű hitelesítéssel [hitelesítheti alapszintű](api-management-authentication-policies.md#Basic) hitelesítést a háttér-szolgáltatással.
    -   [Hitelesítés az ügyféltanúsítvány](api-management-authentication-policies.md#ClientCertificate) használatával – az Ügyféltanúsítványok segítségével végezzen hitelesítést egy háttér-szolgáltatással.
    -   [Hitelesítés felügyelt identitással](api-management-authentication-policies.md#ManagedIdentity) [– felügyelt](../active-directory/managed-identities-azure-resources/overview.md)identitás használatával végezzen hitelesítést egy háttér-szolgáltatással.
-   [Gyorsítótárazási házirendek](api-management-caching-policies.md#CachingPolicies)
    -   [Beolvasás gyorsítótárból](api-management-caching-policies.md#GetFromCache) – a gyorsítótár végrehajtása megkeresi és érvényes gyorsítótárazott választ ad vissza, ha elérhető.
    -   [Tárolás gyorsítótárba](api-management-caching-policies.md#StoreToCache) – a gyorsítótárak válasza a megadott gyorsítótár-vezérlési konfiguráció alapján történik.
    -   [Érték lekérése a gyorsítótárból](api-management-caching-policies.md#GetFromCacheByKey) – a gyorsítótárazott elemek kulcs szerinti beolvasása.
    -   [Tárolási érték](api-management-caching-policies.md#StoreToCacheByKey) a gyorsítótárban – a gyorsítótárban lévő elemek tárolása kulcs alapján.
    -   [Érték eltávolítása a gyorsítótárból](api-management-caching-policies.md#RemoveCacheByKey) – a kulcsban lévő elem eltávolítása a gyorsítótárból.
-   [Tartományközi szabályzatok](api-management-cross-domain-policies.md#CrossDomainPolicies)
    -   Tartományok [közötti hívások engedélyezése](api-management-cross-domain-policies.md#AllowCrossDomainCalls) – elérhetővé teszi az API-t az Adobe Flash és a Microsoft Silverlight böngésző alapú ügyfeleitől.
    -   [CORS](api-management-cross-domain-policies.md#CORS) – a több eredetű erőforrás-megosztási (CORS) támogatás egy művelethez vagy API-hoz, amely lehetővé teszi a tartományok közötti hívásokat a böngészőalapú ügyfelektől.
    -   [Jsnop támogatással](api-management-cross-domain-policies.md#JSONP) – egy művelet vagy API számára lehetővé teszi a JSON-t a kitöltési (jsnop támogatással) támogatással, hogy engedélyezze a tartományok közötti hívásokat a JavaScript böngésző alapú ügyfelektől.
-   [Átalakítási szabályzatok](api-management-transformation-policies.md#TransformationPolicies)
    -   [A JSON konvertálása XML](api-management-transformation-policies.md#ConvertJSONtoXML) formátumba – átalakítja a kérelem vagy a válasz törzsét a JSON-ből az XML-be.
    -   [XML konvertálása JSON](api-management-transformation-policies.md#ConvertXMLtoJSON) formátumba – átalakítja a kérelem vagy válasz törzsét az XML-ből a JSON-ra.
    -   [Karakterlánc keresése és cseréje a törzsben](api-management-transformation-policies.md#Findandreplacestringinbody) – megkeresi a kérelem vagy válasz alkarakterláncot, és egy másik alkarakterlánccal helyettesíti azt.
    -   Az [URL-címek maszkolása](api-management-transformation-policies.md#MaskURLSContent) a válasz törzsében lévő tartalom-újraírások (maszkok) alapján, hogy az átjárón keresztül az egyenértékű hivatkozásra mutassanak.
    -   [Háttérbeli szolgáltatás beállítása](api-management-transformation-policies.md#SetBackendService) – a háttérrendszer módosítása egy bejövő kérelemnél.
    -   [Törzs beállítása](api-management-transformation-policies.md#SetBody) – az üzenettörzs beállítása a bejövő és kimenő kérésekhez.
    -   [Http-fejléc beállítása](api-management-transformation-policies.md#SetHTTPheader) – egy érték kiosztása egy meglévő válaszhoz és/vagy kérelem fejhez, vagy új válasz és/vagy kérelem fejlécének megadása.
    -   [Lekérdezési karakterlánc paraméterének beállítása](api-management-transformation-policies.md#SetQueryStringParameter) – lekérdezési karakterlánc paraméterének megadása, cseréje vagy törlése.
    -   [URL-cím újraírása](api-management-transformation-policies.md#RewriteURL) – a kérés URL-címét a nyilvános űrlapról a webszolgáltatás által várt űrlapra konvertálja.
    -   [XML átalakítása XSLT használatával](api-management-transformation-policies.md#XSLTransform) – XSL-transzformációt alkalmaz a kérelem vagy válasz törzsében lévő XML formátumra.
- [Dapr-integrációs szabályzatok](api-management-dapr-policies.md)
    - [Kérelem küldése a szolgáltatásnak](api-management-dapr-policies.md#invoke) – a Dapr Runtime használatával megkeresheti és megbízhatóan kommunikálhat egy Dapr-szolgáltatással.
    -  [Üzenet küldése a pub/sub témakörbe](api-management-dapr-policies.md#pubsub) – a Dapr Runtime használatával tesz közzé egy üzenetet közzététel/előfizetés témakörben.
    -  [Kimeneti kötés kiváltása](api-management-dapr-policies.md#bind) – a Dapr Runtime használatával külső rendszereket hívhat meg kimeneti kötés használatával.
- [Ellenőrzési házirendek](validation-policies.md)
    - [Tartalom érvényesítése](validation-policies.md#validate-content) – egy kérelem vagy válasz törzsének méretét vagy JSON-sémáját ellenőrzi az API-sémán.
. 
    - [Paraméterek érvényesítése](validation-policies.md#validate-parameters) – a kérelem fejlécének, lekérdezésének vagy elérési útjának paramétereinek ellenőrzése az API-sémán keresztül.
    - [Fejlécek ellenőrzése](validation-policies.md#validate-headers) – ellenőrzi a válasz fejléceit az API-sémán.
    - [Állapotkód ellenőrzése](validation-policies.md#validate-status-code) – érvényesíti a http-ÁLLAPOTKÓDOT az API-sémára adott válaszokban.

## <a name="next-steps"></a>Következő lépések
További információ a házirendek használatáról:

+ [Szabályzatok API Management](api-management-howto-policies.md)
+ [API-k átalakítása](transform-api.md)
+ [Szabályzatminták](./policy-reference.md)
