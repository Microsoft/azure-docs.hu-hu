---
title: Beszéd szöveggel kapcsolatos gyakori kérdések
titleSuffix: Azure Cognitive Services
description: Válaszok a szöveges szolgáltatás beszédével kapcsolatos gyakori kérdésekre.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/01/2021
ms.author: panosper
ms.openlocfilehash: 21924ad5da8833ca5cf8373270ed1bfd3facfdc9
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100388617"
---
# <a name="speech-to-text-frequently-asked-questions"></a>Beszéd szöveggel kapcsolatos gyakori kérdések

Ha nem talál választ a kérdéseire ebben a GYIK-ban, tekintse meg az [egyéb támogatási lehetőségeket](../cognitive-services-support-options.md?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext%253fcontext%253d%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext).

## <a name="general"></a>Általános

**K: mi a különbség az alapmodell és a szöveges modell egyéni beszéde között?**

**A**: az alapmodell a Microsoft tulajdonában lévő adataival lett kiképezve, és már telepítve van a felhőben. Egyéni modell használatával úgy alakíthatja ki a modelleket, hogy jobban illeszkedjen egy adott környezethez, amely adott környezeti zajt vagy nyelvet tartalmaz. A gyári szintekhez, autókhoz vagy zajos utcákhoz alkalmazkodó akusztikai modellre van szükség. A biológia, a fizika, a radiológia, a terméknév és az egyéni betűszók által használt témákhoz alkalmazkodó nyelvi modellre van szükség. Egyéni modell betanításakor a kapcsolódó szöveggel kell kezdenie a speciális kifejezések és kifejezések felismerésének javítását.

**K: hol kezdjek, ha alapmodellt szeretnék használni?**

**A**: először szerezze be az [előfizetési kulcsot](overview.md#try-the-speech-service-for-free). Ha szeretne REST-hívásokat kezdeményezni az előtelepített alapmodellekhez, tekintse meg a [REST API-kat](./overview.md#reference-docs). Ha WebSockets-t szeretne használni, [töltse le az SDK](speech-sdk.md)-t.

**K: mindig létre kell hozni egy egyéni beszédfelismerési modellt?**

**A**: nem. Ha az alkalmazás általános, napról napra kiterjedő nyelvet használ, nem kell testreszabnia a modellt. Ha az alkalmazás olyan környezetben van használatban, ahol kevés vagy nincs háttérzaj, nem kell testreszabnia a modellt.

Üzembe helyezhet alapkonfigurációt és testreszabott modelleket a portálon, majd elvégezheti a pontossági tesztek futtatását. Ezzel a funkcióval mérhető az alapmodell pontossága, és egy egyéni modell is megadható.

**K: Honnan tudom, hogy az adatkészlet vagy a modell feldolgozása befejeződött?**

**A**: jelenleg a modell vagy adatkészlet állapota a táblában az egyetlen lehetőség, hogy megismerje. Ha a feldolgozás befejeződött, az állapot **sikeres** lesz.

**K: Létrehozhatok több modellt is?**

**A**: a gyűjteményben megadható modellek száma nincs korlátozva.

**K: észrevettem, hogy hiba történt. Hogyan megszakítja az adatimportálást vagy a modell létrehozásának folyamatát?**

**A**: jelenleg nem állítható vissza akusztikai vagy nyelvi adaptációs folyamat. Az importált adattípusokat és modelleket törölheti, ha azok egy terminál-állapotban vannak.

**K: több eredményt kapok az egyes kifejezésekhez a részletes kimeneti formátummal. Melyiket érdemes használni?**

**A**: mindig az első eredményt használja, még akkor is, ha egy másik eredmény ("N-Best") nagyobb megbízhatósági értékkel rendelkezik. A beszédfelismerési szolgáltatás az első eredményt a legjobbnak tekinti. Ha a rendszer nem ismerte fel a beszédfelismerést, üres sztring is lehet.

A többi eredmény valószínűleg rosszabb, és előfordulhat, hogy nem rendelkezik teljes körű nagybetűvel és írásjelekkel. Ezek az eredmények olyan speciális forgatókönyvekben hasznosak, mint például a felhasználók számára, hogy kiválasszák a listából vagy a helytelenül felismert parancsok kezelését.

**K: Miért vannak különböző alapmodellek?**

Válasz **: több** alapmodell közül választhat a beszédfelismerési szolgáltatásban. Minden modell neve tartalmazza a hozzáadásának dátumát. Az egyéni modellek betanításakor a legújabb modell segítségével érheti el a legjobb pontosságot. A régebbi alapmodellek továbbra is elérhetők egy új modell elérhetővé tételének ideje alatt. Továbbra is használhatja azt a modellt, amelyet már dolgozott a kivonásig (lásd a [modell életciklusát](custom-speech-overview.md#model-lifecycle)). A jobb pontosság érdekében továbbra is ajánlott a legújabb alapmodellre váltani.

**K: frissíthetem a meglévő modellt (a modell halmozását)?**

**A: a** meglévő modellek nem frissíthetők. Megoldásként egyesítse a régi adatkészletet az új adatkészlettel, és módosítsa újra.

A régi adatkészletet és az új adatkészletet egyetlen. zip fájlban (akusztikai adatokat) vagy egy. txt fájlban (nyelvi adatokat) kell egyesíteni. Az adaptáció befejezése után az új, frissített modellt újra kell telepíteni új végpont beszerzéséhez.

**K: ha elérhető az alapmodell új verziója, az üzembe helyezésem automatikusan frissül?**

**A: az üzemelő** példányok nem frissülnek automatikusan.

Ha kiigazította és telepítette a modellt, akkor az üzemelő példány a következőképpen marad. Leszerelheti az üzembe helyezett modellt, újraigazíthatja az alapmodell újabb verzióját, és a jobb pontosság érdekében újból üzembe helyezheti azokat.

Egy kis idő elteltével az alapmodelleket és az egyéni modelleket is kivonják (lásd a [modell életciklusát](custom-speech-overview.md#model-lifecycle)).

**K: le tudom tölteni a modellt, és helyileg futtatom?**

**A**: helyileg is futtathat egy egyéni modellt egy [Docker-tárolóban](speech-container-howto.md?tabs=cstt).

**K: másolhatok vagy áthelyezhetek adatkészleteket, modelleket és központi telepítéseket egy másik régióba vagy előfizetésbe?**

**A**: a [REST API](https://centralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/CopyModelToSubscription) használatával másolhat egy egyéni modellt egy másik régióba vagy előfizetésbe. Az adatkészletek vagy központi telepítések nem másolhatók. Egy másik előfizetésben is importálhat egy adatkészletet, és létrehozhat végpontokat a modell másolatainak használatával.

**K: a rendszer naplózza a kérelmeket?**

**A**: alapértelmezés szerint a kérelmek nincsenek naplózva (sem hang, sem átírás). Ha szükséges, kiválaszthatja a *tartalom naplózása ebből a végpontból* lehetőséget, amikor [Egyéni végpontot hoz létre](how-to-custom-speech-train-model.md#deploy-a-custom-model). Az egyéni végpontok létrehozása nélkül is engedélyezheti a hangnaplózást a [SPEECH SDK](how-to-use-logging.md) -ban a kérelmek alapján. A kérések hang-és felismerési eredményei mindkét esetben a biztonságos tárolóban lesznek tárolva. A Microsoft tulajdonában lévő tárolót használó előfizetések esetén 30 napig lesznek elérhetők.

A naplózott fájlokat a Speech Studio központi telepítési lapján exportálhatja, ha olyan egyéni végpontot használ, amelyen engedélyezve van a *naplózási tartalom ebből a végpontból* . Ha az SDK-n keresztül engedélyezte a hangnaplózást, hívja meg az [API](https://centralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetBaseModelLogs) -t a fájlok eléréséhez.

**K: a kérelmek szabályozva vannak?**

**A**: lásd a [Speech Services kvótáit és korlátozásait](speech-services-quotas-and-limits.md).

**K: hogyan kell fizetnem a kettős csatornás hangért?**

Válasz **: Ha** az egyes csatornákat külön küldi el (mindegyik csatornát a saját fájljában), a rendszer az egyes fájlok időtartamára díjat számít fel. Ha egyetlen fájlt küld el minden egyes csatornával együtt, akkor az adott fájl időtartamára kell fizetnie. A díjszabással kapcsolatos részletekért tekintse meg az [Azure Cognitive Services díjszabását ismertető oldalt](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

> [!IMPORTANT]
> Ha további adatvédelmi kérdései vannak, amelyek tiltják a Custom Speech Service használatát, lépjen kapcsolatba az egyik támogatási csatornával.

## <a name="increasing-concurrency"></a>Párhuzamosság növelése
Lásd: [a Speech Services kvótái és korlátai](speech-services-quotas-and-limits.md).


## <a name="importing-data"></a>Adatok importálása

**K: mekkora a korlátja egy adatkészlet méretének, és miért korlátozza a korlátot?**

**A**: a korlátot a http-feltöltéshez használt fájl méretének korlátozása okozza. Lásd a tényleges korlátot a [Speech Services kvótái és korlátai](speech-services-quotas-and-limits.md) című részben. Az adatokat több adatkészletbe is feloszthatja, és kiválaszthatja az összeset a modell betanításához.

**K: használhatom a szövegfájlom zip-fájljait, hogy feltölthető legyen egy nagyobb szövegfájl?**

**A**: nem. Jelenleg csak a nem tömörített szövegfájlok engedélyezettek.

**K: az Adatjelentés szerint a hosszúságú kimondott szöveg sikertelen volt. Mi a probléma?**

**A**: nem sikerült feltölteni a hosszúságú kimondott szöveg 100 százalékát egy fájlban, nem probléma. Ha a hosszúságú kimondott szöveg túlnyomó többsége akusztikai vagy nyelvi adatkészletekben (például több mint 95 százalék) sikeresen importálva lett, az adatkészlet használható. Azt azonban javasoljuk, hogy próbálja meg megérteni, miért nem sikerült a hosszúságú kimondott szöveg és elhárítani a problémákat. A leggyakoribb problémák (például a formázási hibák) könnyen orvosolhatók.

## <a name="creating-an-acoustic-model"></a>Akusztikai modell létrehozása

**K: mennyi akusztikai adatmennyiségre van szükségem?**

**A**: javasoljuk, hogy 30 perc és egy órányi akusztikai érték között kezdjen.

**K: milyen adatokat kell gyűjteni?**

**A**: gyűjti az alkalmazási forgatókönyvnek leginkább megfelelő adatokat, és a lehető leghamarabb használja a használati esetet. Az adatgyűjtésnek meg kell egyeznie a célalkalmazás és a felhasználók számára az eszközök, a környezetek és a hangszórók típusai alapján. Általánosságban elmondható, hogy a lehető legszélesebb körben gyűjtsön adatokat a különböző hangszórókból.

**K: Hogyan gyűjthetek akusztikus adatokat?**

**A**: létrehozhat egy önálló adatgyűjtési alkalmazást, vagy használhatja a polcon kívüli hangrögzítési szoftvert is. Létrehozhatja az alkalmazás egy olyan verzióját is, amely naplózza a hangadatokat, majd az adatok használatával.

**K: szeretném magam átírni az adaptációs adatszolgáltatásokat?**

**A**: igen. Saját maga is lemásolhatja, vagy használhat Professional transzkripciós szolgáltatást. Egyes felhasználók előnyben részesítettek a professzionális átiratokkal, mások pedig a közösségi használják, vagy magukra az átírásokra.

**K: mennyi ideig tart a hangadatokkal rendelkező egyéni modell betanítása?**

**A**: hangadatokkal rendelkező modell képzése hosszú folyamat lehet. Az adatmennyiségtől függően több napot is igénybe vehet, hogy egyéni modellt hozzon létre. Ha egy héten belül nem végezhető el, a szolgáltatás megszakíthatja a betanítási műveletet, és sikertelenként jelentheti a modellt.

A gyorsabb eredmény érdekében használja az egyik olyan [régiót](custom-speech-overview.md#set-up-your-azure-account) , ahol a dedikált hardver elérhető a betanításhoz. Általánosságban elmondható, hogy a szolgáltatás naponta körülbelül 10 órányi hangadatot dolgoz fel az ilyen hardverrel rendelkező régiókban. A szolgáltatás csak a napi 1 órányi hangmennyiséget dolgozza fel naponta más régiókban. A teljes körűen betanított modellt egy másik régióba másolhatja a [REST API](https://centralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/CopyModelToSubscription)használatával. A csak szöveggel rendelkező képzés sokkal gyorsabb, és általában percek alatt fejeződik be.

Egyes alapmodellek nem szabhatók testre a hangadatokkal. Számukra a szolgáltatás csak az átirat szövegét fogja használni a betanításhoz, és figyelmen kívül hagyja a hangadatokat. A képzések ezután sokkal gyorsabbak lesznek, és az eredmények ugyanúgy fognak megjelenni, mint a csak szöveggel végzett képzés. A hangadatokkal való képzést támogató alapmodellek listáját a [nyelvi támogatásban](language-support.md#speech-to-text) találhatja meg.

## <a name="accuracy-testing"></a>Pontosság tesztelése

**K: mi a Word Error Rate (WER) és hogyan számítható ki?**

**A**: a wer a beszédfelismerés értékelési mérőszáma. A WER a hibák teljes száma, amely tartalmazza a beszúrásokat, a törléseket és a behelyettesítéseket, a hivatkozás átírásakor a szavak teljes száma szerint elosztva. További információ: [Custom Speech pontosságának kiértékelése](how-to-custom-speech-evaluate-data.md#evaluate-custom-speech-accuracy).

**K: Hogyan megállapítani, hogy jók-e a pontossági tesztek eredményei?**

**A**: az eredmények az alapmodell és a testre szabott modell összehasonlítását mutatják. Érdemes megverni az alapmodellt, hogy a testreszabási lehetőségek is hasznosak legyenek.

**K: Hogyan meghatározhatja az alapmodell WERjét, hogy megtudjam, van-e javulás?**

**A**: az offline teszt eredményei az egyéni modell alapkonfigurációjának pontosságát és az alapkonfigurációt mutatják be.

## <a name="creating-a-language-model"></a>Nyelvi modell létrehozása

**K: mennyi szöveges adatokra van szükség a feltöltéshez?**

Válasz **: attól** függ, hogy az alkalmazásban használt szókincs és kifejezések milyen eltérőek a kezdő nyelvi modelltől. Minden új szó esetében hasznos lehet a szavak használatának lehető legtöbb példáját megadnia. Az alkalmazásban használt általános kifejezések, beleértve a nyelvi adatkifejezéseket is, hasznosak lehetnek, mivel azt jelzi, hogy a rendszer a jelen feltételeket is figyeli. Gyakori, hogy legalább 100, és általában több száz vagy több hosszúságú kimondott szöveg van a nyelvi adatkészletben. Továbbá, ha a lekérdezések bizonyos típusai gyakoribbak, mint mások, a közös lekérdezések több példányát is beillesztheti az adatkészletbe.

**K: csak egy szólistát tölthetek fel?**

**A**: a szavak listájának feltöltése felveszi a szavakat a szókincsbe, de nem fogja tanítani a rendszerre, hogy a szavakat általában hogyan használják. Ha teljes vagy részleges hosszúságú kimondott szöveg (mondatokat vagy kifejezéseket) biztosít a felhasználók számára, akkor a nyelvi modell megismerheti az új szavakat és azok használatát. Az egyéni nyelvi modell nem csupán az új szavak a rendszerhez való hozzáadására, hanem az alkalmazáshoz tartozó ismert szavak valószínűségének módosítására is jó. A teljes hosszúságú kimondott szöveg biztosítása segít a rendszeren a jobb megismerésben.

## <a name="tenant-model-custom-speech-with-microsoft-365-data"></a>Bérlői modell (Custom Speech Microsoft 365 adattal)

**K: milyen információk szerepelnek a bérlői modellben, és hogyan jön létre?**

**A:** A bérlői modell olyan [nyilvános csoportos](https://support.microsoft.com/office/learn-about-microsoft-365-groups-b565caa1-5c40-40ef-9915-60fdb2d97fa2) e-mailek és dokumentumok használatával készült, amelyeket bárki láthat a szervezetében.

**K: milyen beszédfelismerési élményt fejlesztenek a bérlői modell?**

**A:** Ha a bérlői modell engedélyezése, létrehozása és közzététele megtörténik, a rendszer a beszédfelismerési szolgáltatással létrehozott vállalati alkalmazások felismerését javítja. Ez egy felhasználói Azure AD-tokent is továbbít, amely a vállalati tagságot jelzi.

A Microsoft 365 beépített beszédfelismerési élmények, például a diktálás és a PowerPoint-feliratok nem változnak, amikor létrehoz egy bérlői modellt a Speech Service-alkalmazásokhoz.

## <a name="next-steps"></a>Következő lépések

- [Hibaelhárítás](troubleshooting.md)
- [Kibocsátási megjegyzések](releasenotes.md)