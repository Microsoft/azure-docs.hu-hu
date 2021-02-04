---
title: Közzétételi tervezési minták
titleSuffix: Azure Cognitive Services
description: Tervezési minták és ajánlott eljárások a közzétételhez.
services: cognitive-services
author: sharonlo101
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: shlo
ms.openlocfilehash: 425096bf7cb75ae3560c06cbcd32778536dc1952
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2021
ms.locfileid: "99537340"
---
# <a name="disclosure-design-patterns"></a>Közzététel-tervezési minták
Most, hogy&#39;ve meghatározta a szintetikus hangalapú felhasználói felület megfelelő [szintű közzétételét](concepts-disclosure-guidelines.md#disclosure-assessment) , jó időt&#39;s a lehetséges tervezési minták megismerésére.
## <a name="overview"></a>Áttekintés
A különböző közzétételi minták a szintetikus hangvételre is alkalmazhatók. Ha a közzététel kiértékelésének eredménye "magas szintű közzététel" volt, javasoljuk a [**explicit közzétételt**](#explicit-disclosure), ami azt jelenti, hogy a szintetikus hang kiválasztásának elvégzése nem megfelelő. Az [**implicit közzététel**](#implicit-disclosure) olyan célzási és interakciós mintákat tartalmaz, amelyek hasznos tapasztalatokat biztosítanak, függetlenül attól, hogy a szükséges közzétételi szintek magas vagy alacsonyak.
![Közzétételi minták spektruma](media/responsible-ai/disclosure-patterns/affordances.png)






| Explicit közzétételi minták                                                                                                                                                                                    | Implicit közzétételi minták                                                                 |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------|
|[Transzparens bevezetés](#transparent-introduction)<br> [Verbális transzparens bemutatása](#verbal-transparent-introduction)<br>  [Explicit Szerző](#explicit-byline)<br>  [Testreszabás és kalibrálás](#customization-and-calibration)<br> [Szülői közzététel](#parental-disclosure)<br> [A hangvételi lehetőségek megismerése](#providing-opportunities-to-learn-more-about-how-the-voice-was-made) | [Képesség-közzététel](#capability-disclosure)<br>[Implicit útmutatók és visszajelzés](#implicit-cues--feedback)<br> [Társalgási átláthatóság](#conversational-transparency) |



A következő diagram használatával közvetlenül a szintetikus hangra vonatkozó mintákra hivatkozhat. A diagram egyéb feltételei a forgatókönyvre is vonatkozhatnak:<br/>



| Ha a szintetikus hang élménye... | Javaslatok | Tervezési minták |
| --- | --- | --- |
| Magas szintű közzétételt igényel  | Legalább egy explicit mintázatot és implicit célzási lehetőséget kell használnia a felhasználóknak a társítások kiépítéséhez. |[Explicit közzététel](#explicit-disclosure)<br>[Implicit közzététel](#implicit-disclosure)  |
| Kis nyilvánosságra hozatalt igényel | A közzététel minimális vagy szükségtelen lehet, de bizonyos implicit minták is hasznosak lehetnek. | [Képesség-közzététel](#capability-disclosure)<br>[Társalgási átláthatóság](#conversational-transparency)  |
| Magas fokú engagement | A kiépítés hosszú távú, és több belépési pontot kínál a közzétételhez a felhasználói út mentén. Kifejezetten ajánlott bevezetési tapasztalat. | [Transzparens bevezetés](#transparent-introduction)<br>[Testreszabás és kalibrálás](#customization-and-calibration)<br>[Képesség-közzététel](#capability-disclosure) |
| Gyermekeket tartalmaz elsődleges célközönségként | Megcélozhatja a szülőket elsődleges közzétételi célközönségként, és biztosíthatja, hogy hatékonyan kommunikáljanak a gyermekekkel való közzétételsel.  | [Szülői közzététel](#parental-disclosure)<br>[Verbális transzparens bemutatása](#verbal-transparent-introduction)<br> [Implicit közzététel](#implicit-disclosure)<br> [Társalgási átláthatóság](#conversational-transparency)  |
| Olyan vak felhasználókat vagy személyeket foglal magába, akiknek az elsődleges célközönsége a gyengén látó  | Az összes felhasználó belefoglalható, és gondoskodhat arról, hogy a vizualizációk bármilyen formája más szöveg-vagy hanghatásokat is társítson. Tartsa be a kisegítő lehetőségeket a kontraszt arány és a megjelenítési méret tekintetében. A bejelentések közléséhez használjon hallási jegyeket.  | [Verbális transzparens bemutatása](#verbal-transparent-introduction) <br>[Hallási célzások](#implicit-cues--feedback)<br>[Haptic-útmutatók](#implicit-cues--feedback)<br>[Társalgási átláthatóság](#conversational-transparency)<br>[Kisegítő lehetőségek](https://www.microsoft.com/accessibility) |
| A képernyő-kevesebb, az eszköz vagy a hang, mint az elsődleges vagy az egyetlen interakciós mód. | A bejelentések közléséhez használjon hallási jegyeket. | [Verbális transzparens bemutatása](#verbal-transparent-introduction) <br> [Hallási célzások](#implicit-cues--feedback)  |
| Előfordulhat, hogy több felhasználót/figyelőt is tartalmaz (például több háztartásban személyi asszisztens)  | Legyen tisztában a különböző felhasználói környezetekkel és szintekkel, és több lehetőséget kínál a felhasználói úton való közzétételre.  | [Transzparens bevezetés (felhasználó visszaküldése)](#transparent-introduction)<br> [A hangvételi lehetőségek megismerése](#providing-opportunities-to-learn-more-about-how-the-voice-was-made)<br> [Társalgási átláthatóság](#conversational-transparency)  |



## <a name="explicit-disclosure"></a>Explicit közzététel
Ha a szintetikus hangélmény magas szintű nyilvánosságra hozatalt igényel, érdemes a következő explicit minták közül legalább az egyiket használni a szintetikus természet állapotának egyértelmű meghatározásához.
### <a name="transparent-introduction"></a>Transzparens bevezetés

A hangélmény megkezdése előtt vezesse be a digitális asszisztenst úgy, hogy teljesen átlátható legyen a hangjának és képességeinek eredetéről. A minta használatának az optimális pillanata egy új felhasználó bevezetése, illetve új funkciók beléptetése egy visszatérő felhasználó számára. Az implicit célzási útmutatók bevezetése során a felhasználók mentális modellt alkotnak a digitális ügynök szintetikus természetéről.

#### <a name="first-time-user-experience"></a>Felhasználói élmény első alkalommal

![Átlátható bevezetés az első futtatási élmény során](media/responsible-ai/disclosure-patterns/transparent-intro-first.png) <br>
*A szintetikus hang egy új felhasználó bevezetését követően jelent meg.*

Javaslatok
- Írja le, hogy a hang mesterséges (például &quot; digitális &quot; )
- Írja le, hogy az ügynök mire képes
- A hang&#39;i eredetek explicit módon történő meghívása
- A szintetikus hangról további információt a belépési pont nyújt.

#### <a name="returning-user-experience"></a>Felhasználói élmény visszaküldése

Ha a felhasználó kihagyja a bevezetési élményt, folytassa a belépési pontok elérését az átlátható bevezetési élményig, amíg a felhasználó első alkalommal el nem indítja a hangfelvételt.
<br/>

![Átlátható bevezetés a felhasználói élmény visszaküldésekor](media/responsible-ai/disclosure-patterns/transparent-intro-return.png)<br/>
*Konzisztens belépési pontot adjon meg a szintetikus hangélményhez. Lehetővé teszi a felhasználó számára, hogy visszatérjen a bevezetési élményhez, amikor az első alkalommal indítja el a hangot a felhasználói út bármely pontján.*


### <a name="verbal-transparent-introduction"></a>Verbális transzparens bemutatása

A digitális asszisztens&#39;s hangjának eredetét kimondottan arra figyelmezteti, hogy a saját maga a nyilvánosságra hozatalhoz elég egyértelmű. Ez a minta a magas közzétételi forgatókönyvek esetében a legjobb, ha a hang az egyetlen elérhető interakciós mód.
<br/>

![Verbálisan beszélt transzparens bemutatása](media/responsible-ai/disclosure-patterns/spoken-prompt-1.png)
<br/>*Transzparens bevezetést használhat, ha a felhasználói élményben néhány pillanat van, ahol előfordulhat, hogy már bevezette vagy attribútumot&#39;s hangra.*


![Verbálisan beszélt transzparens bevezető az első személyben](media/responsible-ai/disclosure-patterns/spoken-prompt-2.png)<br/>
*További átláthatóság érdekében a hangszínész felfedi a szintetikus hang eredetét az első személyben.*

### <a name="explicit-byline"></a>Explicit Szerző

Akkor használja ezt a mintát, ha a felhasználó egy hanglejátszóval vagy interaktív összetevővel fog kommunikálni a hang elindításához.


![Explicit szerző a hírek adathordozó-forgatókönyvében](media/responsible-ai/disclosure-patterns/explicit-byline.png) <br/>
*Egy explicit szerző az a hely, ahol a hang származik.*

Javaslatok

- Ajánlat belépési pontja további információ a szintetizált hangról

### <a name="customization-and-calibration"></a>Testreszabás és kalibrálás

Adja meg a felhasználók számára, hogy a digitális asszisztens hogyan reagáljon rájuk (azaz hogyan hangzik a hang).  Ha a felhasználó a saját használati feltételeivel és meghatározott céljaival kommunikál a rendszerrel, akkor a definíció szerint már nem valódi személynek&#39;.

#### <a name="user-control"></a>Felhasználói vezérlő

Olyan választási lehetőségeket kínál, amelyek a szintetikus hangélményre nézve értelmes és észrevehető hatással vannak.

![Felhasználói beállítások](media/responsible-ai/disclosure-patterns/customization-user-control.png)<br/>
*A felhasználói beállítások lehetővé teszik a felhasználók számára, hogy testre szabják és javítsák tapasztalataikat.*

Javaslatok

- A hang testreszabásának engedélyezése a felhasználók számára (például a nyelv és a hang típusának kiválasztása)
- Lehetővé teszi a felhasználóknak, hogy megtanítsák a rendszer számára az egyedi hangra (például hangkalibrációra, egyéni parancsokra) való reagálást.
- Optimalizálás felhasználó által generált vagy környezetfüggő interakcióhoz (például emlékeztetők)

#### <a name="persona-customization"></a>Persona testreszabása

Lehetőség van a digitális asszisztens&#39;s hang testreszabására. Ha a hang egy hírességen vagy egy széles körben felismerhető személyen alapul, érdemes lehet vizuális és szóbeli bevezetést használni, amikor a felhasználók előnézete a hang.

![Hang testreszabása](media/responsible-ai/disclosure-patterns/customization-voice-type.png)<br/>
*A hangok kiválasztásának lehetősége segít közvetíteni a mesterséges természetet.*

Javaslatok
- Az egyes hangok hangjának előzetes megtekintésének engedélyezése a felhasználók számára
- Minden hanghoz használjon valódi bevezetést
- Ajánlat belépési pontjai a szintetizált hangokkal kapcsolatos további információkért

### <a name="parental-disclosure"></a>Szülői közzététel

A COPPA-szabályozások betartása mellett a szülők számára is biztosíthatja a közzétételt, ha az elsődleges célközönség fiatal gyermek, és a kitettség szintje magas. Bizalmas felhasználás esetén érdemes kapuzás a tapasztalatot, amíg egy felnőtt elismerte a szintetikus hang használatát. Bátorítsa a szülőket, hogy tájékoztassák az üzenetet a gyermekeik számára.

![A szülők tájékoztatása](media/responsible-ai/disclosure-patterns/parental-disclosure.png)<br/>
*A szülők számára optimalizált transzparens bevezetéssel biztosítható, hogy egy felnőtt tisztában legyen a hang szintetikus természetével, mielőtt a gyermek kommunikál.*

Javaslatok

- A szülők célközönsége elsődleges célközönségként a közzétételhez
- Bátorítsa a szülőket, hogy tájékoztassák gyermekeiket
- Ajánlat belépési pontjai a szintetizált hangokkal kapcsolatos további információkért
- A szülőktől egy egyszerű &quot; védintézkedési kérdés megadásával &quot; megtudhatja, hogy elolvasta a közzétételt

### <a name="providing-opportunities-to-learn-more-about-how-the-voice-was-made"></a>A hangvételi lehetőségek megismerése

A környezetfüggő belépési pont olyan oldalra, előugró vagy külső helyre mutat, amely további információkat nyújt a szintetikus hangtechnológiáról. Felvehet például egy hivatkozást, amely további információt nyújt a bevezetéshez, vagy amikor a felhasználó további információkat kér a beszélgetés során.

![Belépési pont további információért](media/responsible-ai/disclosure-patterns/learn-more-entry-point.png)<br/>
*Példa egy belépési pontra, amely lehetőséget nyújt a szintetizált hangra vonatkozó további információk megismerésére.*

Ha a felhasználó további információt kér a szintetikus hangról, az elsődleges cél a szintetikus hang eredetével kapcsolatos információk benyújtása, valamint a technológia átláthatósága.

![A felhasználók további információkkal szolgálnak a szintetikus hangról](media/responsible-ai/disclosure-patterns/learn-more.png)<br/>
*További információt a külső hely súgójának webhelyén találhat.*

Javaslatok

- Egyszerűsítse az összetett fogalmakat, és ne használja a Legalese és a műszaki szakzsargont
- Ne temetni a tartalmat az adatvédelem és a használati feltételekkel kapcsolatos utasításokban
- A tartalom tömören tartása és a képek használata, ha elérhető

## <a name="implicit-disclosure"></a>Implicit közzététel

A konzisztencia a teljes felhasználói útra kiterjedő, a közzétételt implicit módon megvalósító kulcs. A vizualizációk és a hallási útmutatók konzisztens használata az eszközökön és a beavatkozási módokon segíthet az implicit minták és a explicit közzététel közötti társítások kiépítésében.

![Implicit célzások konzisztenciája](media/responsible-ai/disclosure-patterns/consistency.png)

### <a name="implicit-cues--feedback"></a>Implicit útmutatók & visszajelzés

A antropomorfizmus különböző módokon, az ügynök tényleges vizualizációs ábrázolásán, hangon, hangoknál, a fény mintázatán, a pattogó alakzatokon, vagy akár az eszköz rezgésén is megnyilvánulhat. A Persona meghatározásakor implicit célzási és visszajelzési mintákat használhat, nem pedig egy nagyon emberi jellegű avatárt. Ez az egyik módszer, amellyel csökkentheti a explicit közzététel szükségességét.

![Vizuális útmutatók és visszajelzés](media/responsible-ai/disclosure-patterns/visual-affordances.png)<br/>
*Ezek a útmutatók segítenek az ügynök anthropomorphize, hogy ne legyen túl emberi jellegű. A saját maguk is létrehozhatnak hatékony közzétételi mechanizmusokat, amikor az idő múlásával következetesen használják őket.*

A következő típusú útmutatók beépítésekor vegye figyelembe a tapasztalatok interakciójának különböző módjait:

| Vizuális útmutatók                                                                                                                                                               | Hallási célzások                                                      | Haptic-útmutatók |
|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------|-------------|
|  Avatar <br>Rugalmas valós idejű figyelmeztetések (például animációk)<br> Nem képernyős útmutatók (például az eszközön lévő fények és minták)<br>  | Sonicon (pl. egy rövid megkülönböztető hang, hangjegyzetek sorozata) | Rezgés   |

### <a name="capability-disclosure"></a>Képesség-közzététel

A közzététel implicit módon megvalósítható azáltal, hogy pontos elvárásokat biztosít a digitális asszisztens számára. Adjon meg olyan példákat, amelyekkel a felhasználók megismerhetik a digitális segéd használatát, és a kontextus súgójában további információkat találhat a szintetikus hangról a felhasználói élmény korai szakaszában.

![Példa arra, hogy milyen alapértelmezett válaszokat lehet készíteni egy olyan beszélgetésre, amelyet a Craft.](media/responsible-ai/disclosure-patterns/capability-disclosure.png)<br/>

### <a name="conversational-transparency"></a>Társalgási átláthatóság

Ha a beszélgetések nem várt elérési utakat mutatnak be, érdemes lehet olyan alapértelmezett válaszokat készíteni, amelyek segítenek az elvárások visszaállításában, az átláthatóság erősítésében és a felhasználók a sikeres elérési utakhoz Lehetőség van arra, hogy a kommunikációban is explicit módon használja a közzétételt.

![Nem várt elérési utak feldolgozása](media/responsible-ai/disclosure-patterns/conversational-transparency-1.png)<br/>

<br/>
&quot;Az ügynökre irányuló, off-Task vagy személyes &quot; kérdései jó alkalom arra, hogy emlékeztesse a felhasználókat az ügynök szintetikus természetére, és irányítsa őket, hogy megfelelő módon, vagy egy valós személyre irányítsák őket.

![Feladatok kikapcsolásával kapcsolatos kérdések](media/responsible-ai/disclosure-patterns/conversational-transparency-2.png)<br/>

## <a name="when-to-disclose"></a>Mikor kell közzétenni

A felhasználói utazás során számos lehetőség van a közzétételre. Tervezze meg az első használatot, a második használatot, az n..., de &quot; az &quot; átlátszóság kiemelésének pillanatait is, például ha a rendszer hibát jelez, vagy amikor a felhasználó felfedi az ügynök&#39;s képességeinek korlátozását.

![Közzétételi lehetőségek a felhasználói utazás során](media/responsible-ai/disclosure-patterns/touchpoints.png)<br/>

Példa a szabványos digitális asszisztens felhasználói útra a különböző közzétételi lehetőségek kiemelésével.

### <a name="up-front"></a>Előzetes

A közzététel legelső pillanata, amikor egy személy a szintetikus hanggal kommunikál.Személyes hangasszisztensi forgatókönyv esetén ez a bevezetéskor, vagy az első alkalommal, amikor a felhasználó gyakorlatilag felveszi a funkciót. Más helyzetekben ez az első alkalom, amikor egy szintetikus hang beolvassa a tartalmat egy webhelyen, vagy amikor a felhasználó először kommunikál egy virtuális karakterrel.

- [Transzparens bevezetés](#transparent-introduction)
- [Képesség-közzététel](#capability-disclosure)
- [Testreszabás és kalibrálás](#customization-and-calibration)
- [Implicit útmutatók](#implicit-cues--feedback)

### <a name="upon-request"></a>Igény szerint

A felhasználóknak könnyedén hozzá kell férniük a további információkhoz, a vezérlési beállításokhoz, és az áttekinthető kommunikációhoz bármikor hozzáférhetnek a felhasználói utazás során, amikor erre szükség van.

- [A hangvételi lehetőségek megismerése](#providing-opportunities-to-learn-more-about-how-the-voice-was-made)
- [Testreszabás és kalibrálás](#customization-and-calibration)
- [Társalgási átláthatóság](#conversational-transparency)

### <a name="continuously"></a>Folyamatosan

A felhasználói élményt folyamatosan javító implicit tervezési minták használatával.

- [Képesség-közzététel](#capability-disclosure)
- [Implicit útmutatók](#implicit-cues--feedback)

### <a name="when-the-system-fails"></a>A számítógép meghibásodása esetén

A közzétételi lehetőség használata a zökkenőmentes működés érdekében.

- [Társalgási átláthatóság](#conversational-transparency)
- [A hangvételi lehetőségek megismerése](#providing-opportunities-to-learn-more-about-how-the-voice-was-made)
- [Handoff – emberi](#conversational-transparency)

## <a name="additional-resources"></a>További források
- [Microsoft bot-irányelvek](https://www.microsoft.com/research/uploads/prod/2018/11/Bot_Guidelines_Nov_2018.pdf)
- [Cortana kialakítási útmutató](/cortana/voice-commands/voicecommand-design-guidelines)
- [Microsoft Windows UWP – beszédfelismerési tervezési irányelvek](/windows/uwp/design/input/speech-interactions)
- [A Microsoft Windows vegyes valóság hangutasításokra vonatkozó utasításai](/windows/mixed-reality/voice-design#top-things-users-should-know-about-speech-in-mixed-reality)

## <a name="see-also"></a>Lásd még

* [A hangalapú tehetségek közzététele](https://docs.microsoft.com/legal/cognitive-services/speech-service/disclosure-voice-talent?context=/azure/cognitive-services/speech-service/context/context)
* [Útmutató a szintetikus hangtechnológia felelős üzembe helyezéséhez](concepts-guidelines-responsible-deployment-synthetic.md)
* [A közzététel módja](concepts-disclosure-guidelines.md)