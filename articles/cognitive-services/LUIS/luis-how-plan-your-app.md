---
title: Az alkalmazás megtervezése – LUIS
description: Körvonalazza a releváns alkalmazás-leképezéseket és entitásokat, majd hozza létre az alkalmazási terveket Language Understanding intelligens szolgáltatásokban (LUIS).
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/14/2020
ms.openlocfilehash: 66df23466694fe8b9caea4e56565cde6d8fd7416
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95018956"
---
# <a name="plan-your-luis-app-schema-with-subject-domain-and-data-extraction"></a>A LUIS-alkalmazás sémájának megtervezése a tulajdonos tartomány és az adatkiemelés alapján

A LUIS-alkalmazás sémája a tárgy [tartományához](luis-glossary.md#domain)kapcsolódó [leképezéseket](luis-glossary.md#intent) és [entitásokat](luis-glossary.md#entity) tartalmaz. A cél a felhasználói [hosszúságú kimondott szöveg](luis-glossary.md#utterance)besorolása, az entitások pedig kinyerik az adatait a felhasználói hosszúságú kimondott szöveg.

## <a name="identify-your-domain"></a>A tartomány azonosítása

A LUIS-alkalmazások a tárgy tartomány körül vannak központosítva. Előfordulhat például, hogy rendelkezik egy utazási alkalmazással, amely a jegyek, a járatok, a szállodák és a bérelt autók foglalását kezeli. Egy másik alkalmazás a gyakorlásával, a fitness-erőfeszítések nyomon követésével és a célok beállításával kapcsolatos tartalmakat is biztosít. A tartomány azonosításával könnyebben megtalálhatja a tartományhoz kapcsolódó szavakat vagy kifejezéseket.

> [!TIP]
> A LUIS [előre elkészített tartományokat](./howto-add-prebuilt-models.md) kínál számos gyakori forgatókönyvhöz. Ellenőrizze, hogy használható-e előre összeépített tartomány az alkalmazás kiindulási pontjaként.

## <a name="identify-your-intents"></a>A szándékok azonosítása

Gondoljon az alkalmazás feladatához fontos [szándékokra](luis-concept-intent.md) .

Tegyük fel például, hogy egy utazási alkalmazás példája egy repülés lefoglalására és az időjárási adatok ellenőrzésére a felhasználó célhelyén. Megadhatja a `BookFlight` műveletek és a `GetWeather` leképezéseket.

A további funkciókat tartalmazó összetettebb alkalmazásokban több szándék áll rendelkezésére, és körültekintően kell meghatároznia azokat, hogy a szándékok ne legyenek túl konkrétak. Előfordulhat például, `BookFlight` `BookHotel` hogy külön leképezéseket kell létrehoznia, de `BookInternationalFlight` `BookDomesticFlight` túl hasonló lehet.

> [!NOTE]
> Az ajánlott eljárás az, hogy csak annyi szándékot használjon, amennyire az alkalmazás funkcióinak elvégzéséhez szükség van. Ha túl sok leképezést határoz meg, akkor a LUIS a hosszúságú kimondott szöveg helyes besorolása érdekében nehezebbé válik. Ha túl kevést ad meg, előfordulhat, hogy az általános átfedésben van.

Ha nincs szüksége az általános felhasználói szándék azonosítására, vegye fel az összes példa felhasználói hosszúságú kimondott szöveg a szándékba `None` . Ha az alkalmazás egyre nagyobb szándékot igényel, később is létrehozhatja őket.

## <a name="create-example-utterances-for-each-intent"></a>Példa hosszúságú kimondott szöveg létrehozása az egyes szándékokhoz

A kezdéshez ne hozzon létre túl sok hosszúságú kimondott szöveg az egyes szándékokhoz. A szándékok meghatározása után hozzon létre 15 – 30 példa hosszúságú kimondott szöveg-t. Minden Kimondás nem lehet azonos a korábban megadott hosszúságú kimondott szöveg. A hosszúságú kimondott szöveg jó választéka magában foglalja a szavak teljes darabszámát, a szavak megválasztását, a művelet időpontját és a [központozást](luis-reference-application-settings.md#punctuation-normalization).

További információ: [a Luis-alkalmazások jó hosszúságú kimondott szöveg megismerése](luis-concept-utterance.md).

## <a name="identify-your-entities"></a>Entitások azonosítása

A példában hosszúságú kimondott szöveg azonosítsa a kibontani kívánt entitásokat. Egy járat lefoglalásához olyan információra van szüksége, mint a cél, a dátum, a légitársaság, a jegy kategóriája és az utazási osztály. Hozzon létre entitásokat ezekhez az adattípusokhoz, majd jelölje meg az [entitásokat](luis-concept-entity-types.md) a példában hosszúságú kimondott szöveg. Az entitások fontosak a szándék teljesítéséhez.

Az alkalmazásban használni kívánt entitások meghatározásakor vegye figyelembe, hogy az Objektumtípusok közötti kapcsolatok rögzítéséhez különböző típusú entitások tartoznak. A [Luis-entitások](luis-concept-entity-types.md) részletesebben ismertetik a különböző típusokat.

> [!TIP]
> A LUIS [előre összeépített entitásokat](./howto-add-prebuilt-models.md) kínál a gyakori, társalgási felhasználói forgatókönyvekhez. Érdemes lehet előre elkészített entitásokat használni az alkalmazásfejlesztés kiindulási pontként.

## <a name="resolution-with-intent-or-entity"></a>Feloldás a szándékkal vagy az entitással?

Sok esetben – különösen a természetes beszélgetésekkel való munka esetén – a felhasználók olyan teljes kiosztást biztosítanak, amely több függvényt vagy szándékot is tartalmazhat. Ennek a megoldásnak az általános szabálya, hogy tisztában legyen azzal, hogy a kimenet ábrázolása mindkét szándékban és entitásban végezhető el. Ezt a leképezhető az ügyfélalkalmazás műveleteire kell korlátozni, és nem szükséges a szándéka.

Az **int-ENT-Ties** azt a koncepciót jelenti, hogy a műveletek (amelyek általában a szándékok szerint vannak értelmezve) a kimenet JSON-ban is szerepelhetnek, és ezen az űrlapon hivatkoznak rá az adott művelethez. A _tagadás_ egy gyakori használat, amely a teljes kinyeréshez a szándékot és az entitást használja.

Vegye figyelembe a következő két hosszúságú kimondott szöveg, amelyek nagyon közel vannak a Word Choice-hoz, de különböző eredményekkel rendelkeznek:

|Beszédelem|
|--|
|`Please schedule my flight from Cairo to Seattle`|
|`Cancel my flight from Cairo to Seattle`|

Ahelyett, hogy két külön szándékot kellene létrehoznia, hozzon létre egyetlen szándékot egy `FlightAction` Machine learning-entitással. A Machine learning-entitásnak ki kell bontania a művelet részleteit az ütemezés és a megszakítási kérelem, valamint a forrás vagy a célhely esetében is.

Az `FlightAction` entitás a Machine learning-entitás és alentitások következő pszeudo-sémájában lesz strukturálva:

* FlightAction
    * Művelet
    * Forrás
    * Cél

Az alentitások hozzáadási funkcióinak kibontásának elősegítése érdekében. A funkciók a felhasználói hosszúságú kimondott szöveg és az előrejelzési válaszban visszaadott értékek alapján lesznek kiválasztva.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [A LUIS fejlesztői lifecylce megismerése](luis-concept-app-iteration.md)