---
title: Újdonságok – Language Understanding (LUIS)
description: Ez a cikk rendszeresen frissül az Azure Cognitive Services Language Understanding APIával kapcsolatos hírekkel.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: overview
ms.date: 04/07/2021
ms.openlocfilehash: f6c6f2ba20a67cd39d4142ffadddc803927c936c
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2021
ms.locfileid: "107227058"
---
# <a name="whats-new-in-language-understanding"></a>A Language Understanding újdonságai

Ismerje meg a szolgáltatás újdonságait. Ezek közé tartoznak a kibocsátási megjegyzések, videók, blogbejegyzések és más típusú információk. Könyvjelző ezen az oldalon naprakészen tarthatja a szolgáltatást.

## <a name="release-notes"></a>Kibocsátási megjegyzések

### <a name="april-2021"></a>Április 2021

* Észak-Svájc [szerzői régió](luis-reference-regions.md#publishing-to-europe).

### <a name="january-2021"></a>2021. január

* A v3 előrejelzési API mostantól támogatja a [Bing helyesírási API](luis-tutorial-bing-spellcheck.md)-t.
* A regionális portálokat (au.luis.ai és eu.luis.ai) egyetlen portálra és URL-re konszolidálták. Ha ezen portálok egyikét használta, a rendszer automatikusan átirányítja a luis.ai-re.

### <a name="december-2020"></a>2020. december

* Minden LUIS felhasználónak [telepítenie kell a Luis authoring-erőforrást](luis-migration-authoring.md)
* Új [értékelési végpontok](luis-how-to-batch-test.md#batch-testing-using-the-rest-api) , amelyek lehetővé teszik batch-tesztek küldését a REST API használatával, és pontossági eredményeket kaphat a szándékok és entitások számára. A szolgáltatás a v 3.0-Preview LUIS-végponttól kezdődően érhető el.

### <a name="june-2020"></a>2020. június

* [Előzetes verzió – 3,0 authoring](luis-migration-authoring-entities.md) SDK
    * 3.2.0 verziója – előzetes verzió. 3 – [.net-NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Language.LUIS.Authoring/)
    * Version 4.0.0 – előzetes verzió. 3 – [js – NPM](https://www.npmjs.com/package/@azure/cognitiveservices-luis-authoring)
* DevOps-eljárások alkalmazása a LUIS használatával
    * Alapelvek
        * [DevOps eljárások a LUIS számára](luis-concept-devops-sourcecontrol.md)
        * [Folyamatos integráció és folyamatos kézbesítési munkafolyamatok LUIS DevOps](luis-concept-devops-automation.md)
        * [A LUIS DevOps tesztelése](luis-concept-devops-testing.md)
    * Használati útmutató
        * [A DevOps alkalmazása a LUIS-alkalmazások fejlesztésére GitHub-műveletek használatával](luis-how-to-devops-with-github.md)
    * [GitHub-adattár teljes kódja](https://github.com/Azure-Samples/LUIS-DevOps-Template)

### <a name="may-2020---build"></a>Május 2020 – Build

* Megjelent **általánosan elérhetőként** (GA):
    * [Language Understanding tároló](luis-container-howto.md)
    * A [jelenlegi portálra](https://www.luis.ai)előléptetett betekintő portál a [korábbi](https://previous.luis.ai) portál továbbra is elérhető
    * Új gépi tanulási entitás létrehozása és címkézése
    * Összetett és egyszerű entitások közötti [frissítési folyamat](migrate-from-composite-entity.md) gépi tanulási entitásokra
    * A Word-változatok normalizálása támogatásának [beállítása](how-to-application-settings-portal.md)
* Előzetes verziójú API-változások
    * 7. x alkalmazás-séma beágyazott gépi tanulási entitásokhoz
    * [Migrálás a szükséges szolgáltatásba](luis-migration-authoring-entities.md#api-change-constraint-replaced-with-required-feature)
* Új erőforrások fejlesztők számára
    * [Folyamatos integrációs eszközök](developer-reference-resource.md#continuous-integration-tools)
    * Workshop – gyakorlati tanácsok a [ _természetes Language Understanding_ (NLU) a Luis használatával](developer-reference-resource.md#workshops)
* [Ügyfél által felügyelt kulcsok](./encrypt-data-at-rest.md) – a saját kulcs használatával titkosíthatja a Luis-ben használt összes adathalmazt
* [AI show](https://channel9.msdn.com/Shows/AI-Show/New-Features-in-Language-Understanding) (videó) – a Luis új funkcióinak megtekintése



### <a name="march-2020"></a>2020. március

* A TLS 1,2 mostantól a szolgáltatáshoz tartozó összes HTTP-kérelem esetében érvénybe lép. További információ: [Azure Cognitive Services Security](../cognitive-services-security.md).

### <a name="november-4-2019---ignite"></a>November 4., 2019-Ignite

* Video- [Advanced Natural Language Understanding (NLU) modellek a Luis és az Azure Cognitive Services használatával | BRK2188](https://www.youtube.com/watch?v=JdJEV2jV0_Y)

* Továbbfejlesztett fejlesztői hatékonyság
    * Az [előrejelzési végpont v3](luis-migration-api-v3.md)verziójának általános elérhetősége.
    * Alkalmazások importálása és exportálása `.lu` ([LUDown](https://github.com/microsoft/botbuilder-tools/tree/master/packages/Ludown)) formátummal. Ez kikövezi a hatékony CI/CD-folyamat módját.
* Nyelvi bővítés
    * Az [arab és a hindi](luis-language-support.md) nyilvános előzetes verzióban érhető el.
* Előre összeállított modellek
    * Az előre [elkészített tartományok](luis-reference-prebuilt-domains.md) már általánosan elérhetők (GA)
    * Japán előre [elkészített entitások](luis-reference-prebuilt-entities.md#japanese-entity-support) – az életkor, a pénznem, a szám és a százalék nem támogatott a v3-as verzióban.
    * Olasz előre [összeépített entitások](luis-reference-prebuilt-entities.md#italian-entity-support) – az életkor, a pénznem, a dimenzió, a szám és a százalékos felbontás a v2-től módosult.
* Továbbfejlesztett felhasználói élmény a [preview.Luis.ai-portálon](https://preview.luis.ai) – az összetett modellek kiépítése és hibakeresése érdekében feldolgozatlan címkézési élmény. Próbálja ki a betekintő portál oktatóanyagokat:
    * [Csak szándékok](tutorial-intents-only.md)
    * [Felbomló gépi tanulási entitás](tutorial-machine-learned-entity.md)
* Előzetes nyelvi ismeretek – a [kifinomult nyelvi modellek fejlesztése](luis-concept-entity-types.md) kevesebb erőfeszítéssel.
* Megadhatja a gépi tanulási funkciókat a modell szintjén, és engedélyezheti a modellek más modellekhez való használatát, például az entitások szolgáltatásként való használatát a szándékok és más entitások számára.
* Új, kibővített [korlátok](luis-limits.md) – a kifejezések listájának és az összes kifejezésnek, valamint az új modellnek a funkció korlátainál nagyobb maximális száma
* Kinyerheti az adatokat a szövegből a mély hierarchia struktúrájának formátumában, így hatékonyabban fejlesztheti a beszélgetési alkalmazásokat.

    ![gépi tanulási entitás képe](./media/whats-new/deep-entity-extraction-example.png)

### <a name="september-3-2019"></a>Szeptember 3., 2019

* Azure authoring Resource – [Migrálás most](luis-migration-authoring.md).
    * 500 alkalmazás/Azure-erőforrás
    * 100 verzió/alkalmazás
* Török támogatás előre összeépített entitásokhoz
* Olasz datetimeV2-támogatás

### <a name="july-23-2019"></a>2019. július 23.

* A [felismerők frissítése – szöveg](https://github.com/microsoft/Recognizers-Text/releases/tag/dotnet-v1.2.3) – 1.2.3
    * Kor, hőmérséklet, dimenzió és pénznem-felismerők olasz nyelven.
    * A Hálaadás-alapú dátumok helyes kiszámításához az angol nyelvű nyaralás-felismerés javítása.
    * A nem naprakész és a nem időpontú entitások téves pozitív értékének csökkentése érdekében a francia DateTime fejlesztése.
    * A Calendar/School/pénzügyi év és a betűszók támogatása az angol nyelvű DateRange.
    * Továbbfejlesztett telefonszám-felismerés kínai és japán nyelven.
    * Továbbfejlesztett NumberRange-támogatás az angol nyelven.
    * Teljesítménybeli változások.

### <a name="june-24-2019"></a>Június 24., 2019

* A [OrdinalV2 előre elkészített entitást](luis-reference-prebuilt-ordinal-v2.md) támogat, például a következő, az előző és az utolsó sorrendet. Csak angol kultúra.

### <a name="may-6-2019---build-conference"></a>Május 6., 2019 – Build konferencia

A Build 2019 konferencián a következő funkciók jelentek meg:

* [A V3 API áttelepítési útmutatójának előzetes verziója](luis-migration-api-v3.md)
* [Továbbfejlesztett elemzési irányítópult](luis-how-to-use-dashboard.md)
* [Továbbfejlesztett előre összeépített tartományok](luis-reference-prebuilt-domains.md)
* [Dinamikus lista entitásai](schema-change-prediction-runtime.md#dynamic-lists-passed-in-at-prediction-time)
* [Külső entitások](schema-change-prediction-runtime.md#external-entities-passed-in-at-prediction-time)

## <a name="blogs"></a>Blogok

[Robot keretrendszer](https://blog.botframework.com/)

## <a name="videos"></a>Videók

### <a name="2019-ignite-videos"></a>2019 Ignite-videók

[Advanced Natural Language Understanding (NLU) modellek a LUIS és az Azure Cognitive Services használatával | BRK2188](https://www.youtube.com/watch?v=JdJEV2jV0_Y)

### <a name="2019-build-videos"></a>2019 videók készítése

[Az Azure társalgási AI használata az üzlet méretezésére a következő generáció számára](https://www.youtube.com/watch?v=_k97jd-csuk&feature=youtu.be)

## <a name="service-updates"></a>Szolgáltatási hírek

[Cognitive Services Azure Update-hirdetmények](https://azure.microsoft.com/updates/?product=cognitive-services)