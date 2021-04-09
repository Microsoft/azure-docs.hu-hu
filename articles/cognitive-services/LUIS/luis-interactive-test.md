---
title: Alkalmazás tesztelése a LUIS Portalon
description: A Language Understanding (LUIS) használatával folyamatosan dolgozhat az alkalmazáson, és javíthatja annak nyelvi megismerését.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 06/02/2020
ms.openlocfilehash: 31885eba16d59e2e48a08f84c56271b84e6c565f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "98790918"
---
# <a name="test-your-luis-app-in-the-luis-portal"></a>A LUIS-alkalmazás tesztelése a LUIS-portálon

Az alkalmazások [tesztelése](luis-concept-test.md) egy iterációs folyamat. A LUIS-alkalmazás betanítása után tesztelje a mintát hosszúságú kimondott szöveg, és ellenőrizze, hogy a szándékok és entitások felismerése helyesen történt-e. Ha nem, akkor frissítse a LUIS alkalmazást, a betanítást és a tesztelést.

<!-- anchors for H2 name changes -->
<a name="train-your-app"></a>
<a name="test-your-app"></a>
<a name="access-the-test-page"></a>
<a name="luis-interactive-testing"></a>

## <a name="train-before-testing"></a>Tesztelés előtti tanítás

1. Jelentkezzen be a [Luis-portálra](https://www.luis.ai), és válassza ki az **előfizetését** és a **szerzői erőforrást** , hogy megtekintse az adott szerzői erőforráshoz rendelt alkalmazásokat.
1. Nyissa meg az alkalmazást úgy, hogy kiválasztja a nevét a **saját alkalmazások** oldalon.
1. Ha tesztelni szeretné az aktív alkalmazás legújabb verzióját, a tesztelés előtt válassza a **betanítás** lehetőséget a felső menüben.

## <a name="test-an-utterance"></a>Kimondás tesztelése

A teszt kimondása nem egyezhet meg pontosan az alkalmazás egyik hosszúságú kimondott szöveg sem. A teszt kiírásának tartalmaznia kell a felhasználó számára várt szót, kifejezés hosszúságát és az entitások használatát.

1. Jelentkezzen be a [Luis-portálra](https://www.luis.ai), és válassza ki az **előfizetését** és a **szerzői erőforrást** , hogy megtekintse az adott szerzői erőforráshoz rendelt alkalmazásokat.
1. Nyissa meg az alkalmazást úgy, hogy kiválasztja a nevét a **saját alkalmazások** oldalon.

1. A **teszt** kivetítése panel eléréséhez válassza a **tesztelés** lehetőséget az alkalmazás felső paneljén.

    > [!div class="mx-imgBorder"]
    > ![& tesztelési alkalmazás betanítása oldal](./media/luis-how-to-interactive-test/test.png)

1. Adjon meg egy kiírást a szövegmezőben, és válassza az ENTER billentyűt. Annyi teszt hosszúságú kimondott szöveg is beírhat, amennyit csak szeretne a **tesztben**, de egyszerre csak egy Kimondás.

1. A Kimondás, a felső szándék és a pontszám a szövegmezőben a hosszúságú kimondott szöveg listájára kerül.

    > [!div class="mx-imgBorder"]
    > ![Az interaktív tesztelés a helytelen szándékot azonosítja](./media/luis-how-to-interactive-test/test-weather-1.png)

## <a name="inspect-the-prediction"></a>Az előrejelzés vizsgálata

A teszt eredményének részleteit a **vizsgálat panelen** tekintheti meg.

1. Nyissa meg a test slide (tesztelés)  panelt, és válassza a **vizsgálat** lehetőséget az összehasonlítani kívánt kifejezés kiválasztásához.

    > [!div class="mx-imgBorder"]
    > ![A vizsgálati eredmények további részleteinek megtekintéséhez kattintson a vizsgálat gombra.](./media/luis-how-to-interactive-test/inspect.png)

1. Megjelenik a **vizsgálat** panel. A panel tartalmazza a leggyakoribb pontozási szándékot, valamint az azonosított entitásokat. A panel a kiválasztott kifejezés előrejelzését jeleníti meg.

    > [!div class="mx-imgBorder"]
    > ![A tesztelési vizsgálat panel részleges képernyőképe](./media/luis-how-to-interactive-test/inspect-panel.png)

## <a name="add-to-example-utterances"></a>Hozzáadás példa hosszúságú kimondott szöveg

A vizsgálat panelen a Hozzáadás a következőhöz: **például hosszúságú kimondott szöveg** lehetőség kiválasztásával adhatja hozzá a teszt teljes célját.

## <a name="disable-required-features"></a>Szükséges szolgáltatások letiltása

Ezzel a kapcsolóval meghatározhatja, hogy a betanított alkalmazás megfelelően előre megjósolja-e az entitásokat a szükséges funkciók alapján. Az alapértelmezett beállítás szerint az előrejelzés során szükség szerint alkalmazza a szolgáltatást. Ezt a kapcsolót választva megtekintheti, hogy mi a jóslat, ha az alentitás funkciójának megadása nem volt szükséges.

### <a name="when-to-disable-required-features"></a>Mikor kell letiltani a szükséges szolgáltatásokat

Előfordulhat, hogy a betanított alkalmazás a következők valamelyike alapján elkerülheti a géppel megtanult entitást:
* A példa hosszúságú kimondott szöveg címkézése helytelen.
* A szükséges szolgáltatás nem felel meg a szövegnek.

Ilyen például egy géppel megtanult entitás, amely egy személy nevének alentitását adja meg.

:::image type="content" source="media/luis-how-to-interactive-test/disable-required-feature.png" alt-text="Képernyőkép a LUIS Portal Machine-ról – megtanult entitás sémája a szükséges funkcióval":::

A gép által megtanult entitások példájának kimondása: `Assign Bob Jones to work on the new security feature` .

A kinyerés legyen `security feature` a jegy leírása `Bob Jones` , és a mérnök, az entitás két alentitása `Assign ticket` .

Az alentitások sikeres előrejelzéséhez adja hozzá az előkészített entitást AA [PersonName](luis-reference-prebuilt-person.md) az `engineer` alentitáshoz. Ha a funkciót kötelezővé teszi, ez azt jelenti, hogy az alentitás csak akkor lesz kibontva, ha a PersonName előre összeépített entitást jósolnak a szöveghez. Ez azt jelenti, hogy a szövegben az PersonName alentitással nem előre jelzett nevek nem lesznek visszaadott címkével ellátott alentitásként `engineer` .

Ha az interaktív teszt panelt használja, és a szükséges funkcióval látja el az alentitást, akkor nem előre jelezheti ezt a beállítást, hogy megtudja, van-e előre jelezve, hogy nincs-e szükség a szolgáltatásra. Lehetséges, hogy az alentitások megfelelően előre jelezve lesznek anélkül, hogy a funkciót a példa hosszúságú kimondott szöveg helyes címkézése miatt szükség lenne.

## <a name="view-sentiment-results"></a>A hangulat eredményeinek megtekintése

Ha a találatok elemzése a **[közzétételi](luis-how-to-publish-app.md#enable-sentiment-analysis)** oldalon van konfigurálva, a teszt eredményei közé tartozik a teljes **kiértékelésben** talált hangulat.

## <a name="correct-matched-patterns-intent"></a>Megfelelő egyezésű minta szándéka

Ha [mintázatokat](luis-concept-patterns.md) használ, és a kihagyás egy mintázatnak felel meg, de a rossz szándékot jósolta meg, válassza a minta alapján a **Szerkesztés** hivatkozást, majd válassza ki a megfelelő szándékot.

## <a name="compare-with-published-version"></a>Összehasonlítás a közzétett verzióval

Tesztelheti az alkalmazás aktív verzióját a közzétett [végpont](luis-glossary.md#endpoint) verziószámával. Az **ellenőrzés** panelen válassza a **összehasonlítás a közzétetttel** lehetőséget. A közzétett modellel kapcsolatos bármilyen vizsgálatot levonják az Azure-előfizetési kvóta egyenlegéről.

> [!div class="mx-imgBorder"]
> ![Összehasonlítás közzétételsel](./media/luis-how-to-interactive-test/inspect-panel-compare.png)

## <a name="view-endpoint-json-in-test-panel"></a>Végpont JSON megtekintése a teszt panelen
Az összehasonlításhoz a **JSON-nézet megjelenítése** lehetőség kiválasztásával megtekintheti a végpont által VISSZAadott JSON-t.

> [!div class="mx-imgBorder"]
> ![Közzétett JSON-válasz](./media/luis-how-to-interactive-test/inspect-panel-compare-json.png)

## <a name="additional-settings-in-test-panel"></a>További beállítások a teszt panelen

### <a name="luis-endpoint"></a>LUIS-végpont

Ha több LUIS-végponttal rendelkezik, a teszt közzétett paneljén a **További beállítások** hivatkozásra kattintva módosíthatja a teszteléshez használt végpontot. Ha nem biztos abban, hogy melyik végpontot szeretné használni, válassza ki az alapértelmezett **Starter_Key**.

> [!div class="mx-imgBorder"]
> ![A teszt panel további beállítások hivatkozását kiemelve](media/luis-how-to-interactive-test/additional-settings-v3-settings.png)


## <a name="batch-testing"></a>Kötegelt tesztelés
Tekintse meg a Batch-tesztelési [fogalmakat](./luis-how-to-batch-test.md) , és Ismerje meg, [Hogyan](luis-how-to-batch-test.md) tesztelheti a Batch of hosszúságú kimondott szöveg.

## <a name="next-steps"></a>Következő lépések

Ha a tesztelés azt jelzi, hogy a LUIS-alkalmazás nem ismeri fel a megfelelő szándékokat és entitásokat, a további hosszúságú kimondott szöveg címkézésével vagy funkciók hozzáadásával javíthatja a LUIS-alkalmazás pontosságát.

* [Címke javasolt hosszúságú kimondott szöveg a LUIS-vel](luis-how-to-review-endpoint-utterances.md)
* [Szolgáltatások használata a LUIS-alkalmazás teljesítményének növeléséhez](luis-how-to-add-features.md)