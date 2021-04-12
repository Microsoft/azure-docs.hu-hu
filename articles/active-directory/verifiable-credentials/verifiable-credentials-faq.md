---
title: Gyakori kérdések – Azure-beli ellenőrizhető hitelesítő adatok (előzetes verzió)
description: Válaszok a ellenőrizhető hitelesítő adatokkal kapcsolatos gyakori kérdésekre
author: barclayn
manager: davba
ms.service: identity
ms.subservice: verifiable-credentials
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: 3c43cfb537c84fb25a6bf879d32a8034342ff605
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2021
ms.locfileid: "106280717"
---
# <a name="frequently-asked-questions-faq"></a>Gyakori kérdések (GYIK)

Ez az oldal a ellenőrizhető hitelesítő adatokkal és a decentralizált identitással kapcsolatos gyakran feltett kérdéseket tartalmaz. A kérdések a következő fejezetekben vannak rendszerezve.

- [Szókincs és alapismeretek](#the-basics)
- [A decentralizált identitással kapcsolatos elméleti kérdések](#conceptual-questions)
- [A ellenőrizhető hitelesítő adatok előzetes verziójának használatának kérdései](#using-the-preview)

> [!IMPORTANT]
> Azure Active Directory ellenőrizhető hitelesítő adatok jelenleg nyilvános előzetes verzióban érhetők el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="the-basics"></a>Az alapok

### <a name="what-is-a-did"></a>Mi a teendő? 

A decentralizált identifers (DIDs) olyan azonosítók, amelyek az erőforrásokhoz való hozzáférés biztonságossá tételéhez, a hitelesítő adatok aláírásához és ellenőrzéséhez, valamint az alkalmazások adatcseréjének megkönnyítéséhez használhatók. A hagyományos felhasználónevek és e-mail-címektől eltérően a DIDs tulajdonosa és felügyelete maga az entitás (legyen az személy, az eszköz vagy a vállalat). A DIDs minden külső szervezettől vagy megbízható közvetítőtől függetlenül létezik. [A W3C decentralizált azonosító specifikációja](https://www.w3.org/TR/did-core/) részletesebben ismerteti ezt.

### <a name="why-do-we-need-a-did"></a>Miért van szükségünk erre?

A digitális megbízhatóság alapvetően megköveteli, hogy a résztvevők saját identitásukat használják, és az identitás az azonosítótól kezdődik.
A napi, nagy léptékű rendszerek megszegése és a központosított azonosítók honeypots támadása esetén a decentralizált identitás kritikus fontosságú biztonsági igényeknek számít a fogyasztók és a vállalkozások számára.
Az identitások tulajdonosát és ellenőrzését végző személyeknek képesnek kell lenniük a ellenőrizhető adatcsere és-igazolások cseréjére. Az elosztott hitelesítőadat-környezet számos olyan üzleti folyamat automatizálását teszi lehetővé, amelyek jelenleg kézi és munkaigényesek.

### <a name="what-is-a-verifiable-credential"></a>Mi az ellenőrizhető hitelesítő adat? 

A hitelesítő adatok a mindennapi életünk részét képezik; az illesztőprogram-licencek arra szolgálnak, hogy képesek legyenek a gépjárművek működtetésére, az egyetemi diplomák segítségével kiállíthatók az oktatási szintek, és a kormányzat által kiadott útlevelek lehetővé teszik az országok közötti utazást. A ellenőrizhető hitelesítő adatok lehetővé teszik, hogy az ilyen típusú hitelesítő adatokat a weben olyan módon fejezzék ki, amely kriptográfiai szempontból biztonságos, adatvédelmi tiszteletben tartási és gépi ellenőrizhető. [A W3C-ellenőrizhető hitelesítő adatok specifikációja](https://www.w3.org/TR/vc-data-model//) ezt részletesebben ismerteti.


## <a name="conceptual-questions"></a>Fogalmi kérdések

### <a name="what-happens-when-a-user-loses-their-phone-can-they-recover-their-identity"></a>Mi történik, ha egy felhasználó elveszíti a telefont? Visszaállíthatják az identitásukat?

Több módon is kínálhat helyreállítási mechanizmust a felhasználók számára, amelyek mindegyike saját kompromisszumokkal rendelkezik. Jelenleg kiértékeljük a lehetőségeket, és megtervezjük a helyreállítást, amely kényelmet és biztonságot kínál, miközben tiszteletben tartja a felhasználó adatvédelmét és önszuverenitását.

### <a name="how-can-a-user-trust-a-request-from-an-issuer-or-verifier-how-do-they-know-a-did-is-the-real-did-for-an-organization"></a>Hogyan lehet egy felhasználó megbízhatónak nyilvánítani egy kiállító vagy egy ellenőrző kérelmét? Honnan tudhatják, hogy az igazi volt a szervezet számára?

Megvalósítjuk [a decentralizált Identity Foundation jól ismert konfigurációs specifikációját](https://identity.foundation/.well-known/resources/did-configuration/) , hogy az a-t egy nagymértékben ismert meglévő rendszerhez, tartománynevehöz kapcsolódjon. A Azure Active Directory ellenőrizhető hitelesítő adatokkal létrehozott összes művelethez tartozik egy olyan gyökértartomány neve is, amely a DID-dokumentumban lesz kódolva. További információért kövesse a [tartomány csatolása az elosztott azonosítóhoz](how-to-dnsbind.md) című cikket.  

### <a name="why-does-the-verifiable-credential-preview-use-ion-as-its-did-method-and-therefore-bitcoin-to-provide-decentralized-public-key-infrastructure"></a>Miért használja a ellenőrizhető hitelesítő adatok előzetes verzióját az ION módszerként, így a Bitcoin a decentralizált nyilvános kulcsokra épülő infrastruktúrát biztosít?

Az ION decentralizált, nem megfelelő, méretezhető decentralizált azonosító 2. rétegbeli hálózat, amely Bitcoin-t futtat. A szolgáltatás a speciális cryptoasset token, a megbízható validatorok vagy a központosított konszenzusi mechanizmusok nélkül éri el a méretezhetőséget. A decentralizált hálózat erőssége miatt az 1. rétegbeli szubsztráthoz az Bitcoin-t használjuk, hogy magas fokú módosíthatatlansági biztosítson egy időrendi esemény-nyilvántartási rendszer számára.

## <a name="using-the-preview"></a>Az előzetes verzió használata

### <a name="why-must-i-use-nodejs-for-the-verifiable-credentials-preview-any-plans-for-other-programming-languages"></a>Miért kell NodeJS használni a ellenőrizhető hitelesítő adatok előzetes verziójához? Más programozási nyelvekre vonatkozó csomagok? 

Úgy döntöttünk, hogy NodeJS, mivel ez egy rendkívül népszerű platform az alkalmazások fejlesztői számára. A rendszer felszabadít egy REST API-t, amely lehetővé teszi a fejlesztők számára a hitelesítő adatok kiadását és ellenőrzését. 

### <a name="is-any-of-the-code-used-in-the-preview-open-source"></a>Az előzetes verzió nyílt forráskódjában használt kód bármelyike?

Igen! A következő Tárházak a szolgáltatások nyílt forráskódú összetevői.

1. [SideTree a GitHubon](https://github.com/decentralized-identity/sidetree)
2. A [csomóponthoz készült VC SDK a githubon](https://github.com/microsoft/VerifiableCredentials-Verification-SDK-Typescript)
3. Egy [Android SDK a decentralizált személyazonossági tárca létrehozásához a githubon](https://github.com/microsoft/VerifiableCredential-SDK-Android)
4. [IOS SDK a decentralizált személyazonossági tárca létrehozásához a githubon](https://github.com/microsoft/VerifiableCredential-SDK-iOS)


## <a name="what-are-the-licensing-requirements"></a>Mik a licencelési követelmények?

A ellenőrizhető hitelesítő adatok előzetes verziójának használatához Azure AD P2-licenc szükséges. Ez egy ideiglenes követelmény, mivel a szolgáltatás díjszabását a használat alapján számítjuk fel. 


## <a name="next-steps"></a>Következő lépések

- [A Azure Active Directory ellenőrizhető hitelesítő adatainak testreszabása](credential-design.md)
