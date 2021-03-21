---
title: Adatgyűjtés
description: Ismerje meg, hogy az alkalmazás fejlesztése során milyen példákat kell gyűjteni az adatok gyűjtéséhez
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/06/2020
ms.openlocfilehash: c901d4024292ad03a9195ad0cbd226f473917ee4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98738084"
---
# <a name="data-collection-for-your-app"></a>Adatgyűjtés az alkalmazáshoz

A Language Understanding (LUIS) alkalmazásnak az alkalmazásfejlesztés részeként kell megfelelnie az adatmennyiségnek.

## <a name="data-used-in-luis"></a>A LUIS-ben használt adatszolgáltatások

A LUIS olyan szöveget használ, amely betanítja és teszteli a LUIS-alkalmazást a [szándékok](luis-concept-intent.md) besorolására és az [entitások](luis-concept-entity-types.md)kinyerésére. Elég nagy mennyiségű adatkészlet szükséges ahhoz, hogy külön adatkészleteket hozzon létre a képzéshez és a teszteléshez, amelyeknek a sokféleségét és terjesztését kifejezetten alább kell elnevezni.  Az egyes készletekbe tartozó adat nem fedi át egymást.

## <a name="training-data-selection-for-example-utterances"></a>Az adatképzések kiválasztásának példája hosszúságú kimondott szöveg

A betanítási csoport hosszúságú kimondott szöveg a következő feltételek alapján választhat:

* **A valós adatértékek a legmegfelelőbbek**:
    * Az **ügyfélalkalmazás valós** adatai: válassza ki azokat a hosszúságú kimondott szöveg, amelyek valós adatok az ügyfélalkalmazás számára.  Ha az ügyfél a mai napon küld egy webes űrlapot, és egy robotot készít, akkor a webes űrlap adataival kezdheti meg.
    * **Tömegből származó adatforrások**: Ha nem rendelkezik meglévő adattal, vegye figyelembe a tömeg beszerzésének hosszúságú kimondott szöveg.  Próbálja ki, hogy a jelenlegi felhasználói populációból kinyerje a forrás hosszúságú kimondott szöveg, hogy az alkalmazás által megjelenő valós adatoknak a legjobb közelítését kapja meg. A tömegből származó emberi hosszúságú kimondott szöveg jobbak, mint a számítógéppel létrehozott hosszúságú kimondott szöveg.  Ha meghatározott mintákon létrehozott szintetikus hosszúságú kimondott szöveg állít össze, akkor nem lesz sok a természetes változat, amelyet a hosszúságú kimondott szöveg létrehozó emberek fognak látni, és az éles környezetben nem végezhető el az általánosítás.
* **Az adatsokféleség**:
    * **Régiók sokfélesége**: gondoskodjon arról, hogy az egyes szándékok minden esetben a lehető legváltozatosabbak legyenek, mint például a _megfogalmazás_ (Word Choice) és a _nyelvtan_.  Ha a nyári napokra vonatkozó HR-szabályzatokkal kapcsolatos szándékot tanít, győződjön meg arról, hogy rendelkezik olyan hosszúságú kimondott szöveg, amelyek az összes Ön által használt régióra vonatkozó feltételeket képviselik.  Az Európai emberek például megkérhetik, hogy `taking a holiday` az Egyesült Államokban és az USA-ban is kérdezzenek `taking vacation days` .
    * **Nyelvi sokszínűség**: Ha olyan felhasználókkal rendelkezik, akik a második nyelven kommunikáló különböző natív nyelvekkel rendelkeznek, ügyeljen arra, hogy a nem natív hangvételt képviselő hosszúságú kimondott szöveg legyenek.
    * A **bemeneti sokszínűség**: vegye figyelembe az adatbevitel elérési útját. Ha egy személyből, részlegből vagy bemeneti eszközből (mikrofonból) gyűjt adatokat, valószínűleg hiányzik a sokféleség, amely fontos lehet az alkalmazás számára az összes bemeneti útvonal megismeréséhez.
    * **Írásjelek sokfélesége**: vegye figyelembe, hogy az emberek különböző szintű írásjeleket használnak a szöveges alkalmazásokban, és meg kell győződnie arról, hogy a központozás milyen módon van használatban. Ha a Speech-ből származó adatokkal rendelkezik, nem tartalmaz írásjeleket, így az adatok nem lehetnek.
* Adatterjesztés: Ügyeljen arra, hogy a leképezések közötti **adateloszlás** ugyanazokat az adatmennyiségeket adja meg, amelyeket az ügyfélalkalmazás fogad. Ha a LUIS-alkalmazás azon hosszúságú kimondott szöveg sorolja fel, amelyek a szabadság (50%) megadására irányuló kérelmeket fogják besorolni, de azt is látni fogja, hogy a hosszúságú kimondott szöveg hátralévő napok száma (20%), a levelek jóváhagyása (20%) a hatókör és a chit-csevegés (10%) ezt követően az adathalmazban szerepelnie kell az egyes értékek mintájának százalékában.
* Az **összes adatűrlap használata**: Ha a Luis-alkalmazás több űrlapon is elvégzi az adattárolást, ügyeljen arra, hogy a betanítási hosszúságú kimondott szöveg tartalmazza ezeket az űrlapokat. Ha például az ügyfélalkalmazás a beszédet és a beírt szövegbevitelt is elvégzi, a szövegben létrehozott hosszúságú kimondott szöveg és a beírt hosszúságú kimondott szöveg is rendelkeznie kell.  Különböző eltéréseket láthat az emberektől, hogy milyen módon beszélnek, és hogyan használják a beszédfelismerés és az elírások különböző hibáit.  Az összes változatnak szerepelnie kell a betanítási adataiban.
* **Pozitív és negatív példák**: egy Luis-alkalmazás megtanításához meg kell ismernie, hogy mi a szándék (pozitív), és mi nem (negatív). LUIS-ben a hosszúságú kimondott szöveg csak egyetlen szándék esetében lehet pozitív. Ha egy megjelölést adnak hozzá a szándékhoz, a LUIS automatikusan ugyanezt a példát adja meg egy negatív példának az összes többi szándék esetében.
* **Az alkalmazás hatókörén kívüli** adatmennyiség: Ha az alkalmazás olyan hosszúságú kimondott szöveg fog látni, amelyek a definiált szándékon kívül esnek, ügyeljen arra, hogy biztosítsa ezeket. A megadott szándékhoz nem rendelt példák a **none** szándékkal lesznek címkézve.  Fontos, hogy reális példákkal rendelkezzen a **nincs** szándékra a definiált leképezések hatókörén kívül eső hosszúságú kimondott szöveg megfelelő előrejelzéséhez.

    Ha például olyan HR-robotot hoz létre, amely a távozás idejére összpontosít, és három szándékkal rendelkezik:
    * szabadságok beosztása vagy szerkesztése
    * Érdeklődjön az elérhető távozási napokról
    * Kilépés/elhagyás jóváhagyása

    Győződjön meg arról, hogy van olyan hosszúságú kimondott szöveg, amely mindkét szándékra kiterjed, de a hatókörön kívüli lehetséges hosszúságú kimondott szöveg is kiterjed, hogy az alkalmazás a következőhöz hasonló módon szolgáljon:
    * `What are my medical benefits?`
    * `Who is my HR rep?`
    * `tell me a joke`
* **Ritka példák**: az alkalmazásnak ritka példákkal és gyakori példákkal kell rendelkeznie.  Ha az alkalmazás még nem látott ritka példákat, nem fogja tudni azonosítani őket éles környezetben. Ha valós adatmennyiséget használ, pontosabban megjósolhatja, hogyan fog működni a LUIS-alkalmazás az éles környezetben.

### <a name="quality-instead-of-quantity"></a>Minőségi mennyiség helyett a minőség

További adatmennyiség hozzáadása előtt vegye figyelembe a meglévő adatai minőségét.  A LUIS használatával gépi tanítást használ.  A címkék és a gépi tanulás által meghatározott funkciók kombinációja a LUIS-alkalmazás által használt.  Nem csupán a címkék mennyiségére támaszkodik a legjobb előrejelzés érdekében.  A különböző példák és azok ábrázolása, hogy mit láthatnak a LUIS-alkalmazás az éles környezetben, a legfontosabb rész.

### <a name="preprocessing-data"></a>Az adatfeldolgozás

A következő előfeldolgozási lépések segítenek a jobb LUIS-alkalmazások létrehozásában:

* **Duplikált elemek eltávolítása**: a duplikált hosszúságú kimondott szöveg nem fognak fájni, de nem segítenek sem, így az eltávolításkor a címkézési idő is mentésre kerül.
* **Azonos ügyfél-alkalmazás előfeldolgozás alkalmazása**: Ha az ügyfélalkalmazás, amely meghívja a Luis előrejelzési végpontot, alkalmazza az adatfeldolgozást a futtatókörnyezetbe, mielőtt elküldi a szöveget Luis-nek, a Luis-alkalmazást ugyanúgy kell betanítania a feldolgozott adatokra. Ha például az ügyfélalkalmazás [Bing spell Checkt](../bing-spell-check/overview.md) használ a Luis-bemenetek helyesírásának javítására, javítsa ki a betanítási és tesztelési hosszúságú kimondott szöveg, mielőtt HOZZÁADJA a Luis-hoz.
* **Ne alkalmazzon olyan új karbantartási folyamatokat, amelyeket az ügyfélalkalmazás nem használ**: Ha az ügyfélalkalmazás a beszéd által generált szöveget közvetlenül a törlés, például a nyelvtan vagy a központozás nélkül is elfogadja, a hosszúságú kimondott szöveg azonosnak kell lennie, beleértve a hiányzó írásjeleket, és minden más, a számára szükséges hibás felismerést.
* **Ne törölje az adatokat: ne távolítsa** el a helytelenül formázott adatbevitelt, hogy felmerülhet a beszédfelismerés, a véletlen lenyomások vagy a hibásan beírt vagy helytelenül írt szöveg. Ha az alkalmazás az alábbihoz hasonló bemeneteket fog látni, fontos, hogy kitanítsa és tesztelje őket. Ha nem szeretné, hogy az alkalmazás megértse, _helytelenül formázott beviteli_ szándékot adjon hozzá. Az adatok címkével megcímkézheti a LUIS-alkalmazást, és megjósolhatja a megfelelő választ futásidőben. Az ügyfélalkalmazás választhat egy megfelelő választ a érthetetlen hosszúságú kimondott szöveg, például a következőre: `Please try again` .

### <a name="labeling-data"></a>Az Adatfeliratok

* A **felirat szövege, ha helyes volt**: a példában szereplő hosszúságú kimondott szöveg a címkével ellátott entitás összes formáját kell tartalmaznia. Ez magában foglalja a hibásan írt, helytelenül beírt és rosszul lefordított szöveget is.

### <a name="data-review-after-luis-app-is-in-production"></a>Az adatellenőrzés a LUIS-alkalmazás éles környezetben

[Tekintse át a végpontok hosszúságú kimondott szöveg](luis-concept-review-endpoint-utterances.md) a valós teljes forgalom figyeléséhez, miután üzembe helyezte az alkalmazást az éles környezetben.  Ez lehetővé teszi, hogy a valós adataival frissítse a betanítási hosszúságú kimondott szöveg, ami javítja az alkalmazást. A kiépített vagy nem valós forgatókönyvekkel létrehozott alkalmazásokat valós használatuk alapján kell javítani.

## <a name="test-data-selection-for-batch-testing"></a>A Batch-tesztelési célú adatkijelölés tesztelése

A betanítási hosszúságú kimondott szöveg fent felsorolt [alapelvek a hosszúságú kimondott szöveg](./luis-how-to-batch-test.md)való használatra vonatkoznak. Győződjön meg arról, hogy a leképezések és az entitások eloszlása a lehető legszorosabban tükrözze a valós eloszlást.

Ne használja újra a hosszúságú kimondott szöveg a betanítási készletből. Ez helytelenül torzítja az eredményeket, és nem ad Önnek megfelelő információt arról, hogy a LUIS-alkalmazás hogyan fog működni az éles környezetben.

Miután közzétette az alkalmazás első verzióját, frissítenie kell a hosszúságú kimondott szöveg a valós forgalomból, hogy a tesztelési készlet tükrözze az éles eloszlást, és az idő múlásával nyomon követhető a reális teljesítmény.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Ismerje meg, hogyan változtatja meg a LUIS az adatait az előrejelzés előtt](luis-concept-data-alteration.md)