---
title: Az alkalmazás megtervezése – QnA Maker
description: Megtudhatja, hogyan tervezze meg QnA Maker alkalmazását. Ismerje meg, hogy az QnA Maker hogyan működik, és hogyan kommunikálhat más Azure-szolgáltatásokkal és néhány ismereti alapfogalmakkal.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 07/2/2020
ms.openlocfilehash: 84e4d6907c9036503f43cd607b54577fd3d97444
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/06/2020
ms.locfileid: "91776935"
---
# <a name="plan-your-qna-maker-app"></a>A QnA Maker alkalmazás megtervezése

A QnA Maker alkalmazás megtervezéséhez ismernie kell, hogy QnA Maker hogyan működik, és hogyan kommunikál más Azure-szolgáltatásokkal. Emellett a tudásalapú fogalmakat is alaposan fel kell ismernie.

## <a name="azure-resources"></a>Azure-erőforrások

A QnA Makertel létrehozott összes [Azure-erőforrásnak](azure-resources.md#resource-purposes) konkrét célja van. Minden erőforrás saját célra, korlátokra és [díjszabási szintjére](azure-resources.md#pricing-tier-considerations)vonatkozik. Fontos megérteni ezeknek az erőforrásoknak a funkcióját, hogy ezt a tudást a tervezési folyamatba lehessen használni.

| Erőforrás | Cél |
|--|--|
| Erőforrás [QnA Maker](azure-resources.md#qna-maker-resource) | Szerzői műveletek és lekérdezések előrejelzése |
| Erőforrás [Cognitive Search](azure-resources.md#cognitive-search-resource) | Adattárolás és keresés |
| [Erőforrás-és alkalmazáscsomag-szolgáltatási erőforrás app Service](azure-resources.md#app-service-and-app-service-plan) | Előrejelzési végpont lekérdezése |
| Erőforrás [Application Insights](azure-resources.md#application-insights) | Lekérdezés-előrejelzési telemetria |

### <a name="resource-planning"></a>Erőforrás-tervezés

Az `F0` egyes erőforrások ingyenes szintje működik, és a szerzői és a lekérdezési előrejelzési élményt is lehetővé teszi. Ezt a szintet használhatja a szerzői műveletek és a lekérdezések előrejelzésének megismeréséhez. Éles vagy élő forgatókönyvre való áttéréskor újra kell értékelnie az erőforrás-kijelölést.

#### <a name="qna-maker-resource"></a>Erőforrás QnA Maker

Egyetlen QnA Maker erőforrás több tudásbázist is képes tárolni. A tudásbázisok számát a Cognitive Search árképzési csomag által támogatott indexek mennyisége határozza meg. További információk az [Indexek és a tudásbázisok kapcsolatáról](azure-resources.md#index-usage).

#### <a name="knowledge-base-size-and-throughput"></a>Tudásbázis mérete és átviteli sebessége

Valódi alkalmazás létrehozásakor tervezze meg a Tudásbázis méretének megfelelő erőforrásokat, valamint a várt lekérdezés-előrejelzési kérelmeket.

A Tudásbázis méretét az alábbiak vezérlik:
* Az erőforrás-díjszabási szintek [Cognitive Search](../../../search/search-limits-quotas-capacity.md)
* [QnA Maker korlátok](../limits.md)

A Tudásbázis lekérdezés-előrejelzési kérelmét a webalkalmazás-csomag és a webalkalmazás vezérli. A díjszabási csomag megtervezéséhez tekintse meg az [ajánlott beállítások](azure-resources.md#recommended-settings) című témakört.

### <a name="resource-sharing"></a>Erőforrás-megosztás

Ha már rendelkezik néhány használatban lévő erőforrással, érdemes megfontolnia az erőforrások megosztását. Itt megtudhatja, hogy mely erőforrások [oszthatók](azure-resources.md#share-services-with-qna-maker) meg az erőforrás-megosztás speciális forgatókönyvének megismerésével.

Az ugyanabban a QnA Maker erőforrásban létrehozott tudásbázisok ugyanazt a **tesztelési** lekérdezési előrejelzési végpontot használják.

### <a name="understand-the-impact-of-resource-selection"></a>Az erőforrás-kijelölés hatásának megismerése

A megfelelő erőforrás-kiválasztás azt jelenti, hogy az Ön tudásbázisa sikeresen válaszol a lekérdezések előrejelzésére.

Ha a Tudásbázis nem működik megfelelően, a probléma általában nem megfelelő erőforrás-kezelés.

Nem megfelelő erőforrás-kiválasztás esetén vizsgálat szükséges annak megállapításához, hogy melyik [erőforrást kell módosítani](azure-resources.md#when-to-change-a-pricing-tier).

## <a name="knowledge-bases"></a>Tudásbázisok

A Tudásbázis közvetlenül a QnA Maker erőforráshoz kötődik. Tartalmazza a lekérdezés-előrejelzési kérelmek megválaszolásához használt kérdés-válasz (QnA) párokat.

### <a name="language-considerations"></a>Nyelvi megfontolások

A QnA Maker erőforráson létrehozott első Tudásbázis az erőforrás nyelvét állítja be. Egy QnA Maker erőforráshoz csak egy nyelv tartozhat.

A QnA Maker erőforrásokat nyelv szerint strukturálhatja, vagy a [fordító](../../translator/translator-info-overview.md) segítségével más nyelvről is módosíthatja a lekérdezéseket a Tudásbázis nyelvén, mielőtt elküldené a lekérdezést a lekérdezés előrejelzési végpontjának.

### <a name="ingest-data-sources"></a>Adatforrások betöltése

Egy Tudásbázis létrehozásához a következő betöltött [adatforrások](knowledge-base.md) egyikét használhatja:

* Nyilvános URL-cím
* Privát SharePoint URL-címe
* Fájl

A betöltési folyamat a [támogatott tartalomtípusokat](content-types.md) Markdown alakítja. A válasz további szerkesztése a Markdown- *mel* történik. A Tudásbázis létrehozása után szerkesztheti a [QnA párokat](question-answer-set.md) a QnA Maker-portálon, [Rich Text authoring](../how-to/edit-knowledge-base.md#rich-text-editing-for-answer)használatával.

### <a name="data-format-considerations"></a>Adatformátumra vonatkozó megfontolások

Mivel a QnA pár végleges formátuma Markdown, fontos megérteni a [Markdown-támogatást](../reference-markdown-format.md).

A csatolt lemezképeknek elérhetőnek kell lenniük egy nyilvános URL-címről, amely megjelenik a QnA Maker portál tesztelés ablaktábláján vagy egy ügyfélalkalmazás alkalmazásban. A QnA Maker nem biztosít hitelesítést a tartalomhoz, beleértve a lemezképeket is.

### <a name="bot-personality"></a>Bot-személyiség

Adjon hozzá egy robot-személyiséget a tudásbázishoz a [Chit-Chat](../how-to/chit-chat-knowledge-base.md)használatával. Ez a személyiség egy bizonyos társalgási hangon, például a *Professional* és a *Friendly*szolgáltatásban elérhető válaszokat tartalmaz. Ez a Chit-Chat olyan társalgási készletként van megadva, amelynek teljes hozzáférése van a hozzáadáshoz, szerkesztéshez és eltávolításhoz.

A bot-személyiség akkor javasolt, ha a robot a tudásbázishoz csatlakozik. Kiválaszthatja, hogy a saját Tudásbázisban használja a Chit-Chat szolgáltatást, még akkor is, ha más szolgáltatásokhoz is csatlakozik, de érdemes áttekintenie, hogyan kommunikál a bot szolgáltatás, ha ez a megfelelő építészeti kialakítás a használathoz.

### <a name="conversation-flow-with-a-knowledge-base"></a>Beszélgetési folyamat tudásbázissal

A beszélgetési folyamat általában egy felhasználó megszólításával kezdődik, például `Hi` vagy `Hello` . A Tudásbázis választ kaphat az általános válaszokra, például a `Hi, how can I help you` következőre: a beszélgetés folytatására is lehetőséget nyújt.

Meg kell terveznie a beszélgetési folyamatát egy hurkoval, hogy a felhasználó tudja, hogyan használja a robotot, és ne hagyhatja el a beszélgetésben a robot. Az [utólagos utasítások](../how-to/multiturn-conversation.md) összekapcsolják a QnA párok közötti összekapcsolást, amelyek lehetővé teszik a beszélgetési folyamat használatát.

### <a name="authoring-with-collaborators"></a>Szerzői műveletek

A közreműködők más fejlesztők is lehetnek, akik megosztják a Tudásbázis-alkalmazás teljes fejlesztői veremét, vagy csak a tudásbázist használják.

A Tudásbázis authoring számos [szerepköralapú hozzáférési engedélyt](../how-to/collaborate-knowledge-base.md) támogat, amelyeket a Azure Portal alkalmaz a közreműködő képességei hatókörének korlátozására.

## <a name="integration-with-client-applications"></a>Integráció az ügyfélalkalmazások alkalmazásával

Az [ügyfélalkalmazások](integration-with-other-applications.md) közötti integráció az előrejelzési futtatókörnyezet végpontjának lekérdezését küldi el. A rendszer egy lekérdezést küld az adott tudásbázisba egy SDK-val vagy REST-alapú kéréssel a QnA Maker webalkalmazás-végpontjának.

Az ügyfélalkalmazás megfelelő hitelesítéséhez az ügyfélalkalmazás a megfelelő hitelesítő adatokat és Tudásbázis-azonosítót kell küldenie. Ha Azure Bot Service használ, ezeket a beállításokat a Azure Portal bot-konfigurációjának részeként adja meg.

### <a name="conversation-flow-in-a-client-application"></a>Beszélgetési folyamat egy ügyfélalkalmazás

A beszélgetési folyamat egy [ügyfélalkalmazás](integration-with-other-applications.md), például egy Azure bot esetében a tudásbázissal való interakció előtt és után is megkövetelheti a funkciót.

Támogatja az ügyfélalkalmazás a beszélgetési folyamatokat, vagy ha alternatív módszert biztosít a követő kérések kezelésére, vagy akár a Chit-Chitra? Ha igen, tervezze meg ezeket a korai lépéseket, és győződjön meg róla, hogy az ügyfélalkalmazás lekérdezését megfelelően kezeli egy másik szolgáltatás, vagy ha a tudásbázisba érkezik.

### <a name="dispatch-between-qna-maker-and-language-understanding-luis"></a>Küldés QnA Maker és Language Understanding között (LUIS)

Az ügyfélalkalmazás több funkciót is biztosíthat, amelyek közül csak egy Tudásbázis válaszol. A többi funkciónak továbbra is ismernie kell a társalgási szöveget, és ki kell olvasnia azt.

Az ügyfélalkalmazások általános architektúrája QnA Maker és [Language Understanding (Luis)](../../LUIS/what-is-luis.md) együttes használata. A LUIS megadja a szöveg besorolását és kinyerését bármely lekérdezéshez, beleértve az egyéb szolgáltatásokat is. QnA Maker a Tudásbázisban választ ad a válaszokra.

Ilyen [megosztott architektúrák](../choose-natural-language-processing-service.md) esetén a két szolgáltatás közötti [kiküldést](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch) a bot Framework feladó eszköze végzi.

### <a name="active-learning-from-a-client-application"></a>Az ügyfélalkalmazás aktív tanulása

A QnA Maker az _aktív tanulással_ fejleszti a tudásbázist, ha alternatív kérdéseket javasol a válaszra. Az ügyfélalkalmazás felelős az [aktív tanulás](active-learning-suggestions.md)egy részéért. A társalgási kérések segítségével az ügyfélalkalmazás megállapíthatja, hogy a Tudásbázis olyan választ adott vissza, amely nem hasznos a felhasználó számára, és jobb választ tud adni. Az ügyfélalkalmazás [ezt az információt vissza kell küldenie a tudásbázisba](active-learning-suggestions.md#how-you-give-explicit-feedback-with-the-train-api) az előrejelzési minőség javítása érdekében.

### <a name="providing-a-default-answer"></a>Alapértelmezett válasz megadása

Ha a Tudásbázis nem talál választ, az _alapértelmezett választ_adja vissza. Ez a válasz konfigurálható a QnA Maker portál **Beállítások** lapján vagy az [API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update#request-body)-kon.

Ez az alapértelmezett válasz eltér az Azure bot alapértelmezett válaszával. Az Azure bot alapértelmezett válaszát a Azure Portal a konfigurációs beállítások részeként konfigurálhatja. Ha a pontszám küszöbértéke nem teljesül, a függvény visszaadja.

## <a name="prediction"></a>Előrejelzés

Az előrejelzés a Tudásbázis válasza, és több információt tartalmaz, mint amennyit csak a válasz. A lekérdezés-előrejelzési válasz beszerzéséhez használja a [GENERATEANSWER API](query-knowledge-base.md)-t.

### <a name="prediction-score-fluctuations"></a>Előrejelzési pontszámok ingadozásai

A pontszám több tényező alapján is változhat:

* A tulajdonsággal [GenerateAnswer](query-knowledge-base.md) válaszként kért válaszok száma `top`
* Számos elérhető alternatív kérdés
* Metaadatok szűrése
* A lekérdezés elküldése `test` vagy `production` Tudásbázis

[Kétfázisú válasz rangsorolása](query-knowledge-base.md#how-qna-maker-processes-a-user-query-to-select-the-best-answer):
- Cognitive Search – első rangsor. Állítsa be, hogy a _válaszok_ száma elég nagy legyen ahhoz, hogy a Cognitive Search a legjobb válaszokat adja vissza, majd a QnA Maker ranker-ba kerüljön át.
- QnA Maker második rangot. A featurization és a gépi tanulás alkalmazásával határozhatja meg a legjobb választ.

### <a name="service-updates"></a>Szolgáltatási hírek

Alkalmazza a [legújabb futtatókörnyezeti frissítéseket](../how-to/set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates) a szolgáltatás frissítéseinek automatikus kezelésére.

### <a name="scaling-throughput-and-resiliency"></a>Skálázás, átviteli sebesség és rugalmasság

A skálázást, az átviteli sebességet és a rugalmasságot az [Azure-erőforrások](../how-to/set-up-qnamaker-service-azure.md), a díjszabási szintek, valamint a környező architektúrák, például a [Traffic Manager](../how-to/set-up-qnamaker-service-azure.md#business-continuity-with-traffic-manager)határozzák meg.

### <a name="analytics-with-application-insights"></a>Analitika Application Insights

A Tudásbázis összes lekérdezését Application Insights tárolja. A mérőszámok megismeréséhez használja a [legfontosabb lekérdezéseket](../how-to/get-analytics-knowledge-base.md) .

## <a name="development-lifecycle"></a>Fejlesztési életciklus

A Tudásbázis [fejlesztési életciklusa](development-lifecycle-knowledge-base.md) folyamatban van: a Tudásbázis szerkesztése, tesztelése és közzététele.

### <a name="knowledge-base-development-of-qna-maker-pairs"></a>QnA Maker pár tudásbázisának fejlesztése

Az [QnA-párokat](question-answer-set.md) az ügyfélalkalmazás használata alapján kell megtervezni és kifejleszteni.

Minden pár tartalmazhatja a következőket:
* Metaadatok – szűrheti a lekérdezés során, hogy lehetővé tegye a QnA-párok címkézését az adatok forrásával, tartalmával, formátumával és céljával kapcsolatos további információkkal.
* Utólagos utasítások – segít meghatározni az elérési utat a Tudásbázisban, hogy a felhasználó a megfelelő válaszra jusson.
* Alternatív kérdések – fontos, hogy a keresés megegyezzen a kérdés különböző formáinak válaszával. Az [aktív tanulási javaslatok](active-learning-suggestions.md) alternatív kérdéseket tesznek fel.

### <a name="devops-development"></a>DevOps-fejlesztés

Az DevOps-folyamatba beszúrandó Tudásbázis létrehozásához a tudásbázist a [Batch-tesztelés](../quickstarts/batch-testing.md)során el kell különíteni.

A Tudásbázis megosztja a Cognitive Search indexet az QnA Maker erőforrás minden más tudásbázisával. Amíg a Tudásbázis el van különítve a partícióval, az index megosztása a közzétett tudásbázishoz képest különbséget eredményezhet a pontszámban.

Ahhoz, hogy _ugyanaz a pontszám_ legyen a `test` és a `production` Tudásbázisban, el kell különíteni egy QnA Maker erőforrást egyetlen tudásbázisba. Ebben az architektúrában az erőforrásnak csak az elkülönített batch-teszt után kell élnie.

## <a name="next-steps"></a>Következő lépések

* [Azure-erőforrások](../how-to/set-up-qnamaker-service-azure.md)
* [Kérdés-válasz párok](question-answer-set.md)