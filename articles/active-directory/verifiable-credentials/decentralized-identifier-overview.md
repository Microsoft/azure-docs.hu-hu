---
title: Azure Active Directory ellenőrizhető hitelesítő adatok (előzetes verzió) bemutatása
description: Az Azure-ban ellenőrizhető hitelesítő adatok áttekintése.
services: active-directory
author: barclayn
manager: daveba
editor: ''
ms.service: identity
ms.subservice: verifiable-credentials
ms.topic: overview
ms.date: 04/01/2021
ms.author: barclayn
ms.reviewer: ''
ms.openlocfilehash: 4ae7ad186e2ed8868be6205d48e9cfbef525d810
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2021
ms.locfileid: "106172802"
---
# <a name="introduction-to-azure-active-directory-verifiable-credentials-preview"></a>Azure Active Directory ellenőrizhető hitelesítő adatok (előzetes verzió) bemutatása

> [!IMPORTANT]
> Azure Active Directory ellenőrizhető hitelesítő adatok jelenleg nyilvános előzetes verzióban érhetők el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A digitális és a fizikai életünk egyre nagyobb mértékben összefügg az olyan alkalmazásokkal, szolgáltatásokkal és eszközökkel, amelyeket a funkciók széles választékának elérésére használunk. Ez a digitális átalakítás lehetővé teszi, hogy több száz vállalatot és több ezer más felhasználót is felhasználjon olyan módokon, amelyek korábban elképzelhetetlenek voltak.

Az azonosító adatok azonban túl gyakran jelentek meg a biztonsági rések során. Ezek a szabálysértések kihatnak a társadalmi, szakmai és pénzügyi életünket befolyásoló emberek életére. A Microsoft úgy véli, hogy jobb megoldás. Minden személynek van olyan identitása, amely a saját és a tulajdonában van, az egyik, hogy biztonságosan tárolja a digitális identitásuk elemeit, és megőrzi az adatvédelmet. Ez az útmutató bemutatja, hogyan kapcsolódunk a különböző közösségekhez egy nyitott, megbízható, interoperábilis és szabványokon alapuló decentralizált (DID-) megoldás létrehozásához az egyéni felhasználók és szervezetek számára.

## <a name="why-we-need-decentralized-identity"></a>Miért van szükség decentralizált identitásra 

Jelenleg a digitális identitást használjuk a munkahelyi, otthoni és minden alkalmazás, szolgáltatás és eszköz között. Mindent megteszünk, amit mondunk, csinálunk és tapasztalunk az életünkben: jegyek vásárlása egy eseményhez, egy szállodai ellenőrzés vagy akár az ebéd megrendelése. Jelenleg az identitásunk és az összes digitális interakciónk más felek tulajdonában és ellenőrzése alatt áll, akik közül néhányat még nem is ismerünk.

A felhasználók általában több alkalmazáshoz és eszközhöz biztosítanak jóváhagyást. Ennek a megközelítésnek nagy fokú éberségre van szüksége a felhasználó részéről annak nyomon követéséhez, hogy ki férhet hozzá az adatokhoz. A vállalati előfizetésben a fogyasztókkal és partnerekkel való együttműködés magas szintű beavatkozást igényel az adatok biztonságos cseréjéhez oly módon, hogy az adatvédelmet és a biztonságot az összes érintett felhasználó számára fenntartsa.

Úgy véljük, hogy egy szabványon alapuló decentralizált identitásrendszer feloldja az új felhasználói élményeket, amelyek lehetővé tehetik, hogy a felhasználók és a szervezetek nagyobb mértékben szabályozhatják az adataikat, és magasabb fokú megbízhatóságot és biztonságot nyújtsanak az alkalmazások, eszközök és szolgáltatók számára

## <a name="lead-with-open-standards"></a>Érdeklődő nyílt szabványokkal 

Elkötelezettek vagyunk arra, hogy az ügyfelekkel, partnerekkel és a Közösséggel szorosan együttműködve feloldják a decentralizált identitás-alapú élmények következő generációját, és örömmel vesszük a kapcsolatot azon személyekkel és szervezetekkel, akik hihetetlen hozzájárulást tesznek ezen a helyen. Ha a DID-ökoszisztéma növekedni fog, a szabványoknak, a technikai összetevőknek és a kód-szolgáltatásoknak nyílt forráskódúnek kell lenniük, és mindenki számára elérhetőnek kell lenniük.

A Microsoft aktívan együttműködik a decentralizált identitási alaprendszer (különböző) tagjaival, a W3C hitelesítő adatokkal foglalkozó közösségi csoporttal és a szélesebb identitású Közösséggel. Ezekkel a csoportokkal a kritikus szabványok azonosítására és fejlesztésére dolgozunk, és a következő szabványokat Implementáljuk a Szolgáltatásainkban.

* [W3C decentralizált azonosítók](https://www.w3.org/TR/did-core/)
* [W3C-ellenőrizhető hitelesítő adatok](https://www.w3.org/TR/vc-data-model/)
* [NEHÉZ Sidetree](https://identity.foundation/sidetree/spec/)
* [Jól ismert konfiguráció](https://identity.foundation/specs/did-configuration/)
* [SIOP](https://identity.foundation/did-siop/)
* [Különböző megjelenítési Exchange](https://identity.foundation/presentation-exchange/)


## <a name="what-are-dids"></a>Mi a DIDs? 

A DIDs megértéséhez segít összehasonlítani azokat a jelenlegi identitás-rendszerekkel. Az e-mail-címek és a közösségi hálózati azonosítók az együttműködés céljára szolgáló, emberi használatra alkalmas aliasok. Ez létrehoz egy lehetséges problémát, mert az azonosítók elérését a külső felek bármikor el tudják távolítani.

A decentralizált azonosítók (DIDs-EK) eltérőek. A DIDs felhasználó által generált, önkiszolgáló, globálisan egyedi azonosítók a decentralizált rendszerek (például ION) gyökerében. Egyedi tulajdonságokkal rendelkeznek, mint például a módosíthatatlansági, a cenzúra-rezisztencia és a illetéktelen evasiveness. Ezek az attribútumok kritikus fontosságúak minden olyan AZONOSÍTÓrendszer esetében, amely a saját tulajdonú és a felhasználói vezérlés biztosítására szolgál. 

A Microsoft ellenőrizhető hitelesítőadat-megoldása decentralizált hitelesítő adatokat (DIDs) használ a kriptográfiai aláíráshoz, amelyet egy függő entitás (ellenőrző) tanúsít a hitelesítő adatok tulajdonosai számára. Ezért a decentralizált azonosítók alapszintű megértése ajánlott a Microsoft ajánlatán alapuló, ellenőrizhető hitelesítő adatok létrehozására.
## <a name="what-are-verifiable-credentials"></a>Mik a ellenőrizhető hitelesítő adatok 

 A napi életünkben azonosítókat használunk. Rendelkezünk olyan illesztőprogram-licencekkel, amelyeket a rendszer az autó üzemeltetésére való képességének bizonyítékaként használ. Az egyetemek olyan okleveleket állítanak ki, amelyek igazolják, hogy az oktatási szintet elértük. A PASSPORTS segítségével bizonyítjuk, hogy kik vagyunk a hatóságok, ahogy más országokba érkezünk. Az adatmodell leírja, hogyan kezelhetjük ezeket a forgatókönyveket az interneten dolgozva, de biztonságos módon, amely tiszteletben tartja a felhasználó adatvédelmét. További információ a 1,0-es [ellenőrizhető hitelesítő adatok Adatmodelljében](https://www.w3.org/TR/vc-data-model/) olvasható.

Röviden, ellenőrizhető hitelesítő adatok olyan adatobjektumok, amelyek a kibocsátó által a tárgyra vonatkozó adatokat tanúsító jogcímeket alkotnak. Ezeket a jogcímeket a séma azonosítja, és tartalmazza a kiállítót és a tárgyat. A kibocsátó egy digitális aláírást hozott létre, amely igazolja, hogy tanúsítják ezeket az információkat.


## <a name="how-does-decentralized-identity-work"></a>Hogyan működik a decentralizált identitás? 

Az identitás új formáját kell megadnia. Olyan identitásra van szükségünk, amely összehasonlítja a technológiákat és a szabványokat a kulcsfontosságú identitási attribútumok, például az öntulajdonlás és a cenzúra elleni védelem biztosításához. Ezeket a képességeket nehéz elérni a meglévő rendszerek használatával.

Ezeknek az ígéreteknek a biztosításához hét kulcsfontosságú újításból álló technikai alapokra van szükségünk. Az egyik kulcsfontosságú innováció a felhasználó tulajdonában lévő azonosítók, az ilyen azonosítókkal társított kulcsok kezelésére szolgáló felhasználói ügynök, valamint a titkosított, felhasználó által vezérelt adattárolók.

![a Microsoft ellenőrizhető hitelesítő adatokkal kapcsolatos környezetének áttekintése](media/decentralized-identifier-overview/microsoft-did-system.png)

**1. a W3C decentralizált azonosítók (DIDs-** azonosítók) felhasználói a szervezettől és a kormányzattól függetlenül hozhatnak létre, a saját és a vezérlőket. A DIDs globálisan egyedi azonosítók vannak társítva a decentralizált nyilvánoskulcs-infrastruktúrához (DPKI), amely a nyilvános kulcsokat, a hitelesítő adatokat és a szolgáltatási végpontokat tartalmazó JSON-dokumentumokból áll.

**2. decentralizált rendszer: az ion (Identity overlay Network)** ion egy 2. rétegbeli nyitott, hitelesítetlen hálózat, amely a tisztán determinisztikus Sidetree protokollon alapul, és amely nem igényel különleges jogkivonatot, megbízható validatort vagy más konszenzusi mechanizmust; a Bitcoin időláncának lineáris előrehaladása a művelethez szükséges. [Nyílt forráskódú NPM-csomagot](https://www.npmjs.com/package/@decentralized-identity/ion-tools) biztosítunk, hogy az ion-hálózat könnyen integrálható legyen alkalmazásaiba és szolgáltatásaiba. A kódtárak közé tartozik az új DID létrehozása, a kulcsok generálása és a tett a Bitcoin blockchain való rögzítése. 

**3. a felhasználói ügynök/mobiltárca: Microsoft Authenticator alkalmazás** lehetővé teszi, hogy a valódi személyek decentralizált identitásokat és ellenőrizhető hitelesítő adatokat használjanak. A hitelesítő DIDs hoz létre, megkönnyíti az igazolható hitelesítő adatok kiállítási és megjelenítési kérelmeit, és egy titkosított mobiltárca-fájlon keresztül kezeli a DID-vetőmagok biztonsági mentését.

**4. a Microsoft resolver** egy API, amely összekapcsolja az ion csomópontot, hogy megkeresse és feloldja a DIDs a metódus használatával, ```did:ion``` és visszaküldi a DID Document objektumot (DDO). A DDO tartalmazza a DPKI metaadatait, például a nyilvános kulcsokat és a szolgáltatási végpontokat. 

**5. Azure Active Directory ellenőrzött hitelesítő adatok szolgáltatása** egy kiállítási és ellenőrzési API és nyílt forráskódú SDK a [W3C által ellenőrizhető hitelesítő adatokhoz](https://www.w3.org/TR/vc-data-model/) , amelyek a ```did:ion``` metódussal vannak aláírva. Lehetővé teszik a személyazonosság-tulajdonosok számára a jogcímek létrehozását, bemutatását és ellenőrzését. Ez a rendszerek felhasználói közötti megbízhatóságot képezi.

## <a name="a-sample-scenario"></a>Példa A forgatókönyvre

Az a forgatókönyv, amelyet a VCs működésének elmagyarázása kapcsán használunk:

- Woodgrove Inc. a vállalat.
- A proseware olyan cég, amely Woodgrove-alkalmazottak számára biztosít kedvezményeket.
- Alice, a Woodgrove, Inc. munkatársa, aki kedvezményt szeretne kapni a proseware



Alice ma felhasználónevet és jelszót biztosít a Woodgrove hálózati környezetbe való bejelentkezéshez. A Woodgrove egy VC-megoldást helyez üzembe, amely hatékonyabban teszi lehetővé az Alice számára, hogy igazolja, hogy a Woodgrove alkalmazottja. A proseware olyan VC-megoldást használ, amely kompatibilis a Woodgrove VC-megoldásával, és a Woodgrove által kiállított hitelesítő adatokat fogadja el a foglalkoztatás igazolása céljából.

A hitelesítő adat kiállítója, a Woodgrove Inc. létrehoz egy nyilvános kulcsot és egy titkos kulcsot. A nyilvános kulcsot az ION tárolja. Ha a kulcsot hozzáadja az infrastruktúrához, a bejegyzés egy blockchain-alapú decentralizált főkönyvben van rögzítve. A kiállító megadja az Alice-nek a mobiltárca-alkalmazásokban tárolt titkos kulcsát. Minden alkalommal, amikor Alice sikeresen a titkos kulcsot használja, a tranzakció a mobiltárca alkalmazásban van naplózva.

![Microsoft-DID – áttekintés](media/decentralized-identifier-overview/did-overview.png)

## <a name="roles-in-a-verifiable-credential-solution"></a>A ellenőrizhető hitelesítőadat-megoldás szerepkörei 

A hitelesíthető hitelesítőadat-megoldás három fő résztvevővel rendelkezik. A következő ábrán:

- **1. lépés**: a **felhasználó** ellenőrizhető hitelesítő adatokat kér egy kiállítótól.
- **2. lépés**: a hitelesítő adat **kiállítója** tanúsítja, hogy a felhasználó által megadott tanúsítvány pontos, és létrehoz egy igazolható hitelesítő adatot, amely a DID-val van aláírva, és a felhasználó a tulajdonos.
- A 3. lépésben a felhasználó egy ellenőrizhető bemutatót (VP) aláír a **művelettel**, és elküldi őket a **hitelesítőnek.** Az ellenőrző ezt követően ellenőrzi a hitelesítő adatokat a DPKI elhelyezett nyilvános kulcs egyeztetésével.

Az ebben a forgatókönyvben szereplő szerepkörök a következők:

![a hitelesíthető hitelesítőadat-környezet szerepkörei](media/decentralized-identifier-overview/issuer-user-verifier.png)

**kiállító** – a kibocsátó olyan szervezet, amely egy felhasználótól információt kérő kiállítási megoldást hoz létre. Az információ a felhasználó identitásának ellenőrzésére szolgál. A Woodgrove, Inc. például egy kiállítási megoldással rendelkezik, amely lehetővé teszi, hogy ellenőrizhető hitelesítő adatokat (VCs-ket) hozzon létre és terjesszen az összes alkalmazottja számára. Az alkalmazott a hitelesítő alkalmazás használatával bejelentkezik felhasználónevével és jelszavával, amely egy azonosító jogkivonatot továbbít a kiállító szolgáltatásnak. Ha a Woodgrove, Inc. ellenőrzi a beküldött azonosító jogkivonatot, a kiállítási megoldás létrehoz egy VC-t, amely tartalmazza az alkalmazotthoz tartozó jogcímeket, és a Woodgrove, Inc.-vel van aláírva. Az alkalmazottnak már van egy ellenőrizhető hitelesítő adata, amelyet a munkaadó aláír.  

**felhasználó** – a felhasználó a VC-t kérő személy vagy entitás. Alice például a Woodgrove, Inc. új munkatársa, és korábban kiállította a munkahelyi ellenőrizhető hitelesítő adatok igazolását. Amikor Alice-nek meg kell igazolnia a munkavégzést, hogy kedvezményt kapjon a proseware-on, a hitelesítő alkalmazásban megadhatja a hitelesítő adatokhoz való hozzáférést egy olyan ellenőrizhető bemutató aláírásával, amely igazolja, hogy Alice a DID tulajdonosa. A proseware képes érvényesíteni a hitelesítő adatokat a Woodgrove, Inc. és Alice a hitelesítő adatok tulajdonosaként. 

**ellenőrző** – az ellenőrző olyan vállalat vagy szervezet, akinek meg kell győződnie arról, hogy a jogcímek egy vagy több megbízható kiállítók. A proseware Trusts Woodgrove, Inc. például megfelelő feladatot biztosít az alkalmazottak személyazonosságának ellenőrzéséhez, valamint a hiteles és érvényes VCs kiállításához. Amikor Alice megpróbálja megrendelni a feladatához szükséges berendezést, a proseware nyílt szabványokat használ, például a SIOP és a megjelenítési Exchange-t, hogy hitelesítő adatokat kérjen a felhasználótól, amely igazolja, hogy a Woodgrove, Inc. alkalmazottja. Előfordulhat például, hogy a proseware egy webhelyre mutató hivatkozást ad meg egy olyan QR-kóddal, amelyet a telefon kamerájával keres. Ezzel kezdeményezi a kérelmet egy adott VC-ra vonatkozóan, amelyet a hitelesítő elemez, és megadja, hogy Alice lehetővé teszi, hogy jóváhagyja a kérelmet, hogy igazolja a proseware. A proseware a ellenőrizhető hitelesítő adatok szolgáltatás API-ját vagy SDK-ját használhatja a ellenőrizhető bemutató hitelességének ellenőrzéséhez. Az Alice által nyújtott információk alapján a kedvezményt adja Alice-nek. Ha más vállalatok és szervezetek tudják, hogy a Woodgrove, Inc. a VCs-t az alkalmazottaik számára kiállítják, létrehozhatnak egy ellenőrző megoldást is, és a Woodgrove, Inc. által ellenőrzött hitelesítő adatokat a Woodgrove, Inc. alkalmazottai számára fenntartott speciális ajánlatok biztosítására használhatják.

## <a name="next-steps"></a>Következő lépések

Most, hogy már ismeri a DIDs és a ellenőrizhető hitelesítő adatokat, az első lépéseket ismertető cikkből vagy az egyik cikkből megismerheti a ellenőrizhető hitelesítő adatokkal kapcsolatos fogalmakat.

- [Ismerkedés a ellenőrizhető hitelesítő adatokkal](get-started-verifiable-credentials.md)
- [A hitelesítő adatok testreszabása](credential-design.md)
- [Ellenőrizhető hitelesítő adatok – gyakori kérdések](verifiable-credentials-faq.md)