---
title: Azure-igazolás – alapfogalmak
description: Az Azure-igazolás alapvető fogalmai.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 95f59b73682e461a350410b38e3a021226cd7db6
ms.sourcegitcommit: 003ac3b45abcdb05dc4406661aca067ece84389f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/07/2020
ms.locfileid: "96748688"
---
# <a name="basic-concepts"></a>Alapfogalmak

Az alábbiakban néhány, az Microsoft Azure igazolással kapcsolatos alapfogalmakat talál.

## <a name="json-web-token-jwt"></a>JSON Web Token (JWT)

A [JSON web token](https://jwt.io/) (JWT) egy nyílt szabványú [RFC7519](https://tools.ietf.org/html/rfc7519) módszer, amely a felek között JavaScript Object Notation (JSON) objektumként való biztonságos továbbítására szolgál. Ez az információ ellenőrizhető és megbízható, mert digitálisan alá van írva. A JWTs titkos vagy nyilvános/titkos kulcspár használatával lehet aláírni.

## <a name="json-web-key-jwk"></a>JSON-webkulcs (JWK)

A [JSON webkulcs](https://tools.ietf.org/html/rfc7517) (JWK) egy olyan JSON-adatstruktúra, amely titkosítási kulcsot jelöl. Ez a specifikáció egy JWK-készlet JSON-adatstruktúrát is definiál, amely JWKs jelöl.

## <a name="attestation-provider"></a>Igazolási szolgáltató

Az igazolási szolgáltató a Microsoft. igazolás nevű Azure-erőforrás-szolgáltatóhoz tartozik. Az erőforrás-szolgáltató egy olyan szolgáltatási végpont, amely Azure igazolási REST-szerződést biztosít, és [Azure Resource Manager](../azure-resource-manager/management/overview.md)használatával van üzembe helyezve. Minden igazolási szolgáltató egy konkrét, felderíthető házirendet tart fenn. 

Az igazoló szolgáltatók minden egyes TEE-típushoz alapértelmezett szabályzattal jönnek létre (vegye figyelembe, hogy a VBS enklávé nem tartalmaz alapértelmezett szabályzatot). A SGX ENKLÁVÉHOZ vonatkozó alapértelmezett szabályzattal kapcsolatos további részletekért tekintse meg az [igazolási szabályzat példáit](policy-examples.md) .

### <a name="regional-default-provider"></a>Regionális alapértelmezett szolgáltató

Az Azure-igazolás minden régióban alapértelmezett szolgáltatót biztosít. Az ügyfelek dönthetnek úgy, hogy az alapértelmezett szolgáltatót használják az igazoláshoz, vagy egyéni szabályzatokkal hoznak létre saját szolgáltatókat. Az alapértelmezett szolgáltatók bármely Azure AD-felhasználó számára elérhetők, és az alapértelmezett szolgáltatóhoz tartozó szabályzat nem módosítható.

| Régió | Tanúsító URI | 
|--|--|
| Az Egyesült Királyság déli régiója | `https://shareduks.uks.attest.azure.net` | 
| USA 2. keleti régiója | `https://sharedeus2.eus2.attest.azure.net` | 
| USA középső régiója | `https://sharedcus.cus.attest.azure.net` | 
| USA keleti régiója| `https://sharedeus.eus.attest.azure.net` | 
| Közép-Kanada | `https://sharedcac.cac.attest.azure.net` | 

## <a name="attestation-request"></a>Igazolási kérelem

Az igazolási kérelem az ügyfélalkalmazás által az igazolási szolgáltatónak küldendő szerializált JSON-objektum. A SGX ENKLÁVÉHOZ enklávé kérelem objektumának két tulajdonsága van: 
- "Quota" – az "quote" tulajdonság értéke egy olyan karakterlánc, amely az igazolási idézet Base64URL kódolt ábrázolását tartalmazza.
- "EnclaveHeldData" – a "EnclaveHeldData" tulajdonság értéke egy olyan karakterlánc, amely az enklávéban tárolt adat Base64URL-kódolású ábrázolását tartalmazza.

Az Azure-igazolás ellenőrzi a megadott "quota" értéket a PÓLÓból, majd gondoskodik arról, hogy a megadott enklávéban tárolt adatok SHA256 kivonata a reportData mező első 32 bájtjában legyen kifejezve az idézőjelben. 

## <a name="attestation-policy"></a>Igazolási szabályzat

Az igazolási szabályzat az igazolási tanúsítványok feldolgozására szolgál, és az ügyfelek által konfigurálható. Az Azure-igazolás középpontjában egy olyan házirend-motor található, amely a bizonyítékokat alkotó jogcímeket dolgozza fel. A szabályzatok segítségével megállapítható, hogy az Azure-igazolás igazoló jogkivonatot állít ki a bizonyítékok alapján (vagy nem), és ezáltal jóváhagyja az igazolást (vagy nem). Ennek megfelelően az összes házirend átadásának meghiúsulása esetén a rendszer nem ad ki JWT-jogkivonatot.

Ha az igazolási szolgáltató alapértelmezett TEE-szabályzata nem felel meg az igényeknek, az ügyfelek az Azure-igazolás által támogatott összes régióban létrehozhatnak egyéni házirendeket. A házirend-kezelés az Azure-igazolás által az ügyfeleknek biztosított kulcsfontosságú szolgáltatás. A szabályzatok TEE-specifikusak lesznek, és felhasználhatók a enklávék azonosítására, illetve jogcímek hozzáadására a kimeneti jogkivonathoz, vagy a jogcímek módosítása kimeneti jogkivonatban. 

Tekintse meg az alapértelmezett házirend-tartalomra és-mintákra vonatkozó [igazolási szabályzat példáit](policy-examples.md) .

## <a name="benefits-of-policy-signing"></a>A szabályzat aláírásának előnyei

Az igazolási házirend végső soron meghatározza, hogy az Azure-igazolás alapján ki kell-e állítani az igazolási jogkivonatot. A házirend meghatározza az igazolási jogkivonatban létrehozandó jogcímeket is. Ezért rendkívül fontos, hogy a szolgáltatás által kiértékelt házirend valójában a rendszergazda által írt szabályzatot, és azt a külső entitások nem módosították vagy módosították. 

A megbízhatósági modell meghatározza az igazolási szolgáltató engedélyezési modelljét a szabályzat definiálásához és frissítéséhez.  Két modell támogatott – az egyik az Azure AD-hitelesítésen alapul, az ügyfél által felügyelt titkosítási kulcsok (például elkülönített modell) birtokában.  Az elkülönített modell lehetővé teszi az Azure-igazolást annak biztosítására, hogy az ügyfél által beküldött házirend ne legyen módosítva.

Az elkülönített modellben a rendszergazda létrehoz egy tanúsító szolgáltatót, amely egy fájlban megbízható aláírási X. 509 tanúsítványokat határoz meg. A rendszergazda Ezután hozzáadhat egy aláírt szabályzatot az igazolási szolgáltatóhoz. Az igazolási kérelem feldolgozása során az Azure-igazolás ellenőrzi a szabályzat aláírását a fejléc "jwk" vagy "x5c" paramétere által jelzett nyilvános kulccsal.  Az Azure-igazolás azt is ellenőrzi, hogy a kérelem fejlécében található nyilvános kulcs szerepel-e az igazolási szolgáltatóhoz társított megbízható aláíró tanúsítványok listáján. Így a függő entitás (Azure-igazolás) megbízhatónak minősítheti az általa ismert X. 509 tanúsítványokat használó szabályzatot. 

Lásd: [példák a házirend-aláíró tanúsítványára](policy-signer-examples.md) mintákhoz.

## <a name="attestation-token"></a>Igazolási jogkivonat

Az Azure igazolási válasz egy JSON-karakterlánc lesz, amelynek értéke JWT tartalmaz. Az Azure igazolása becsomagolja a jogcímeket, és létrehoz egy aláírt JWT. Az aláírási művelet egy önaláírt tanúsítvány használatával történik, amelynek a tulajdonos neve megegyezik az igazolási szolgáltató AttestUri elemével.

Az OpenID-metaadatok beolvasása API az OpenID [Connect Discovery protokoll](https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderConfig)által megadott OpenID konfigurációs választ ad vissza. Az API az Azure-igazolás által használt aláíró tanúsítványokra vonatkozó metaadatokat kérdezi le.

SGX ENKLÁVÉHOZ enklávéhoz generált JWT-példa:

```
{
  “alg”: “RS256”,
  “jku”: “https://tradewinds.us.attest.azure.net/certs”,
  “kid”: “f1lIjBlb6jUHEUp1/Nh6BNUHc6vwiUyMKKhReZeEpGc=”,
  “typ”: “JWT”
}.{
  “maa-ehd”: <input enclave held data>,
  “exp”: 1568187398,
  “iat”: 1568158598,
  “is-debuggable”: false,
  “iss”: “https://tradewinds.us.attest.azure.net”,
  “nbf”: 1568158598,
  “product-id”: 4639,
  “sgx-mrenclave”: “”,
  “sgx-mrsigner”: “”,
  “svn”: 0,
  “tee”: “sgx”
}.[Signature]
```
Az olyan jogcímeket, mint az "exp", a "IAT", az "ISS", a "NBF", a [JWT RFC](https://tools.ietf.org/html/rfc7517) által definiált, és a fennmaradó értéket az Azure-igazolás hozza létre. További információkért lásd: [Az Azure-igazolás által kiállított jogcímek](claim-sets.md) .

## <a name="encryption-of-data-at-rest"></a>Inaktív adatok titkosítása

Az ügyféladatok védelme érdekében az Azure-tanúsítványok megőrzik az Azure Storage-ban tárolt adattárolási szolgáltatásait. Az Azure Storage az adatközpontokban tárolt adatok titkosítását biztosítja, és visszafejti az ügyfelek számára az elérését. Ez a titkosítás egy Microsoft által felügyelt titkosítási kulcs használatával történik. 

Az Azure Storage-ban tárolt adatok védelme mellett az Azure igazolása Azure Disk Encryption (ADE) szolgáltatást is használ a szolgáltatásbeli virtuális gépek titkosításához. Az olyan Azure-igazolások esetében, amelyek az Azure-beli bizalmas számítástechnikai környezetekben üzemelő enklávéban futnak, az ADE-bővítmény jelenleg nem támogatott. Ilyen esetekben az adatok memóriában való tárolásának megakadályozása érdekében a lapozófájl le van tiltva. 

Az Azure igazolási példány helyi merevlemez-meghajtóján nem maradnak meg ügyféladatok.


## <a name="next-steps"></a>További lépések

- [Igazolási szabályzat létrehozása és aláírása](author-sign-policy.md)
- [Az Azure-igazolás beállítása a PowerShell használatával](quickstart-powershell.md)
