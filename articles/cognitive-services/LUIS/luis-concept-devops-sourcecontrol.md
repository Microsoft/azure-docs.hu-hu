---
title: Verziókövetés és fejlesztési ágak – LUIS
description: A Language Understanding (LUIS) alkalmazás karbantartása a verziókövetés alatt. Frissítések alkalmazása a LUIS-alkalmazásokhoz fejlesztési ág használata közben.
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/18/2020
ms.openlocfilehash: 68d88ef667da9f22d3e3a17f10036693fcca0c3f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "98932526"
---
# <a name="devops-practices-for-luis"></a>DevOps eljárások a LUIS számára

A Language Understanding (LUIS) alkalmazást fejlesztő szoftverfejlesztők DevOps eljárásokat alkalmazhatnak a [verziókövetés](luis-concept-devops-sourcecontrol.md), az [automatizált buildek](luis-concept-devops-automation.md), a [tesztelés](luis-concept-devops-testing.md)és a [kiadási felügyelet terén](luis-concept-devops-automation.md#release-management) az alábbi irányelvek alapján.

## <a name="source-control-and-branch-strategies-for-luis"></a>A LUIS verziókövetési és elágaztatási stratégiái

A DevOps sikerességének egyik fő tényezője a [verziókövetés](/azure/devops/user-guide/source-control). A verziókövetés rendszer lehetővé teszi a fejlesztők számára, hogy a kódban működjenek együtt, és nyomon kövessék a módosításokat. Az ágak használata lehetővé teszi a fejlesztők számára a kód különböző verzióinak közötti váltást, és a csapat többi tagjától függetlenül működnek. Ha a fejlesztők egy [lekéréses kérelmet](https://help.github.com/github/collaborating-with-issues-and-pull-requests/about-pull-requests) (PR) állítanak elő a frissítések egyik ágban a másikba való előléptetéséhez, vagy ha a változások egyesülnek, akkor az [automatizált](luis-concept-devops-automation.md) buildek számára a kód kiépítéséhez és folyamatos teszteléséhez használható trigger lehet.

A jelen dokumentumban ismertetett fogalmakat és útmutatást követve kifejlesztheti a LUIS-alkalmazást, miközben nyomon követheti a verziókövetés rendszerének változásait, és követheti ezeket a szoftveres mérnöki ajánlott eljárásokat:

- **Verziókövetés**
  - A LUIS-alkalmazás forráskódja olvasható formában van.
  - A modell a forrásból is ismételhető módon építhető ki.
  - A forráskódot a forráskód-tárház kezelheti.
  - A hitelesítő adatokat és a titkos kódokat, például a szerzői és előfizetési kulcsokat soha nem a forráskódban tárolja a rendszer.

- **Elágazás és egyesítés**
  - A fejlesztők a független ágakból is dolgozhatnak.
  - A fejlesztők egyszerre több ág is dolgozhatnak.
  - A LUIS-alkalmazások módosításait a rendszer újraindulás vagy egyesítés útján integrálhatja egy másik ágra.
  - A fejlesztők egyesíteni tudnak egy PR-t a fölérendelt ágra.

- **Verziókezelés**
  - A nagyméretű alkalmazások minden összetevőjét egymástól függetlenül kell megadni, így a fejlesztők csak a verziószám alapján tudják felderíteni a feltörési változásokat vagy a frissítéseket.

- **Kódok felülvizsgálata**
  - A kérelemben szereplő változások emberi olvasási forráskódként jelennek meg, amelyet a rendszer a PR elfogadása előtt áttekintheti.

## <a name="source-control"></a>Verziókövetés

Ha a LUIS-alkalmazás [alkalmazás-sémájának definícióját](./app-schema-definition.md) szeretné karbantartani egy forráskód-felügyeleti rendszeren, használja az alkalmazás [LUDown Format ( `.lu` )](/azure/bot-service/file-format/bot-builder-lu-file-format)  reprezentációját. `.lu` a formátum inkább a `.json` formátum, mert az ember számára olvasható, így könnyebben lehet elvégezni és áttekinteni a kérelmek változásait.

### <a name="save-a-luis-app-using-the-ludown-format"></a>LUIS-alkalmazás mentése a LUDown formátum használatával

A LUIS-alkalmazás `.lu` formátumba való mentéséhez és a verziókövetés alá helyezéséhez:

- VAGY: [exportálja az alkalmazás verzióját](./luis-how-to-manage-versions.md#other-actions) `.lu` a [Luis-portálról](https://www.luis.ai/) , és adja hozzá a forrás-ellenőrzési adattárhoz

- VAGY: egy szövegszerkesztővel hozzon létre egy `.lu` fájlt a Luis-alkalmazáshoz, és adja hozzá a forrás-ellenőrzési adattárhoz

> [!TIP]
> Ha a LUIS-alkalmazás JSON-exportálásával dolgozik, [átalakíthatja a LUDown](https://github.com/microsoft/botframework-cli/tree/master/packages/luis#bf-luisconvert).  Ezzel a `--sort` beállítással biztosíthatja, hogy a szándékok és a hosszúságú kimondott szöveg betűrendben legyenek rendezve.  
> Vegye figyelembe, hogy a **.** A Luis-portálba épített Lu-exportálási képesség már rendezi a kimenetet.

### <a name="build-the-luis-app-from-source"></a>A LUIS-alkalmazás létrehozása a forrástól

Egy LUIS-alkalmazás esetében a *forrásból való kiépítéshez* [egy új Luis-alkalmazás verziójának létrehozásához a `.lu` forrás importálásával](./luis-how-to-manage-versions.md#import-version) , [a verzió betanításához](./luis-how-to-train.md) és a [közzétételéhez](./luis-how-to-publish-app.md). Ezt a LUIS portálon vagy a parancssorban teheti meg:

- A LUIS portál használatával [importálhatja az alkalmazás `.lu` verzióját](./luis-how-to-manage-versions.md#import-version) a forrás vezérlőelemből, és [betaníthatja](./luis-how-to-train.md) és [közzéteheti](./luis-how-to-publish-app.md) az alkalmazást.

- Használja a [bot Framework parancssori felületét a Luis-hez](https://github.com/microsoft/botbuilder-tools/tree/master/packages/LUIS) a parancssorban vagy egy CI/CD-munkafolyamatban az alkalmazás verziójának a forrás-vezérlőelemből egy Luis-alkalmazásba való [importálásához](https://github.com/microsoft/botframework-cli/blob/master/packages/luis/README.md#bf-luisversionimport) `.lu` , valamint az alkalmazás [betanításához](https://github.com/microsoft/botframework-cli/blob/master/packages/luis/README.md#bf-luistrainrun) és [közzétételéhez](https://github.com/microsoft/botframework-cli/blob/master/packages/luis/README.md#bf-luisapplicationpublish) .

### <a name="files-to-maintain-under-source-control"></a>A verziókövetés alatt karbantartott fájlok

A LUIS-alkalmazás következő típusú fájljait kell fenntartani a verziókövetés alatt:

- `.lu` a LUIS-alkalmazás fájlja

- [Egység test definition Files](luis-concept-devops-testing.md#writing-tests) (hosszúságú kimondott szöveg és várt eredmények)

- A teszteléshez használt [Batch](./luis-how-to-batch-test.md#batch-test-file) -hosszúságú kimondott szöveg (és várt eredmények)

### <a name="credentials-and-keys-are-not-checked-in"></a>A hitelesítő adatok és kulcsok nincsenek bejelölve

Ne foglalja bele az előfizetési kulcsokat vagy a hasonló bizalmas értékeket a tárházba bejelentkező fájlokba, ahol azok jogosulatlan személyek számára is láthatóvá válnak. A bejelentkezni kívánt kulcsoknak és egyéb értékeknek a következőket kell megelőzniük:

- LUIS authoring és előrejelzési kulcsok
- LUIS authoring és előrejelzési végpontok
- Azure-előfizetési kulcsok
- Hozzáférési tokenek, például az Automation-hitelesítéshez használt Azure- [szolgáltatásnév](/cli/azure/ad/sp) jogkivonata

#### <a name="strategies-for-securely-managing-secrets"></a>Stratégiák a titkok biztonságos kezeléséhez

A titkok biztonságos kezelésére szolgáló stratégiák a következők:

- Ha a git Version Controlt használja, a futásidejű titkokat egy helyi fájlban tárolhatja, és megakadályozhatja a fájl beadását egy [. gitignore](https://git-scm.com/docs/gitignore) -fájl fájlnevének megfelelő minta hozzáadásával.
- Az Automation-munkafolyamatokban biztonságosan tárolhatók a titkok az Automation-technológia által kínált paraméterek konfigurációjában. Ha például [GitHub-műveleteket](https://github.com/features/actions)használ, a titkokat biztonságosan tárolhatja a GitHub- [titkokban](https://help.github.com/en/actions/configuring-and-managing-workflows/creating-and-storing-encrypted-secrets).

## <a name="branching-and-merging"></a>Elágazás és egyesítés

Az elosztott verziókövető rendszerek, például a git rugalmasságot biztosítanak a csapattagok által a másokkal megosztott fejlesztési ágakon keresztül történő közzétételi, megosztási, felülvizsgálati és iterációs lépések során. A csapatának megfelelő [git-elágazási stratégia](/azure/devops/repos/git/git-branching-guidance) bevezetése.

Az Ön által elfogadott elágazási stratégia egyik legfontosabb elve, hogy a csapattagok a megoldáson belül dolgozhatnak az adott *szolgáltatási ágban* , függetlenül attól, hogy milyen munkát végeznek más ágakban.

A független munkavégzés támogatása a LUIS-projekttel:

- **A fő ág saját LUIS-alkalmazással rendelkezik.** Ez az alkalmazás képviseli a megoldás aktuális állapotát a projekthez, és a jelenlegi aktív verziója mindig a `.lu` fő ág forrására van leképezve. Az alkalmazás forrásának összes frissítését `.lu` felül kell vizsgálni és tesztelni kell, hogy az alkalmazás üzembe helyezhető legyen, hogy olyan környezeteket hozzon létre, mint például a termelési szolgáltatás. Ha a rendszer a `.lu` szolgáltatásban lévő frissítéseket egyesíti a főkiszolgálóról, hozzon létre egy új verziót a Luis alkalmazásban, és [a verziószámot](#versioning).

- **Minden egyes szolgáltatás-ág a Luis-alkalmazás saját példányát kell használnia**. A fejlesztők ezzel az alkalmazással együttműködve a szolgáltatási ágban nem érintik a más ágakban dolgozó fejlesztőket. Ez a "dev Branch" alkalmazás egy olyan működő példány, amelyet törölni kell a szolgáltatási ág törlésekor.

![Git-funkciók ága](./media/luis-concept-devops-sourcecontrol/feature-branch.png)

### <a name="developers-can-work-from-independent-branches"></a>A fejlesztők a független ágakból is dolgozhatnak

A fejlesztők a következő módon dolgozhatnak a LUIS-alkalmazások frissítésein:

1. Egy szolgáltatási ág létrehozása a fő ág alapján (a fiókirodai stratégia, általában a fő vagy a fejlesztés) függvényében.

1. [Hozzon létre egy új Luis-alkalmazást a Luis-portálon](./luis-how-to-start-new-app.md) (a "*dev Branch app*") kizárólag a szolgáltatás üzletágban végzett munka támogatásához.

   * Ha a `.lu` megoldás forrása már létezik a ágban, mert a projekt korábbi részében történt munka után lett mentve, a fájl importálásával hozza létre a dev Branch Luis alkalmazást `.lu` .

   * Ha új projekten indítja el a munkát, még nem lesz a `.lu` fő Luis-alkalmazás forrása a tárházban. A fájl létrehozásához `.lu` exportálja a fejlesztői ág alkalmazást a portálról, amikor befejezte a szolgáltatási ág működését, és beküldi azt a lekéréses kérelem részeként.

1. A szükséges módosítások végrehajtásához a dev Branch alkalmazás aktív verzióján dolgozhat. Javasoljuk, hogy csak a fejlesztői ág egyetlen verziójában működjön együtt az összes funkciós ág működéséhez. Ha több verziót hoz létre a fejlesztői ág alkalmazásban, ügyeljen arra, hogy nyomon kövesse, melyik verzió tartalmazza azokat a módosításokat, amelyeket be szeretne állítani a PR-ban.

1. A frissítések tesztelése – lásd: a [Luis DevOps tesztelése](luis-concept-devops-testing.md) a fejlesztői ág alkalmazásának tesztelésével kapcsolatos részletekért.

1. Exportálja a dev Branch alkalmazás aktív verzióját `.lu` a [verziók listából](./luis-how-to-manage-versions.md).

1. Tekintse át a frissítéseket, és hívja meg a frissítések partneri áttekintését. Ha a GitHubot használja, egy [lekéréses kérelmet](https://help.github.com/en/github/collaborating-with-issues-and-pull-requests/about-pull-requests)fog felhívni.

1. A módosítások jóváhagyásakor egyesítse a frissítéseket a fő ágra. Ezen a ponton a *fő* Luis alkalmazás egy új [verzióját](./luis-how-to-manage-versions.md) fogja létrehozni a frissítés `.lu` a főoldalán. A verzió nevének beállításával kapcsolatos megfontolásokat lásd: [verziószámozás](#versioning) .

1. A szolgáltatási ág törlését követően érdemes törölni a szolgáltatás-ág működéséhez létrehozott fejlesztői ág LUIS-alkalmazását.

### <a name="developers-can-work-in-multiple-branches-concurrently"></a>A fejlesztők egyidejűleg dolgozhatnak egyszerre több ág alatt

Ha követi a fentiekben ismertetett mintát, a [fejlesztők a független ágakból is dolgozhatnak](#developers-can-work-from-independent-branches), akkor minden egyes szolgáltatási ág egyedi Luis-alkalmazást fog használni. Egyetlen fejlesztő egyszerre több ág esetében is működhet, feltéve, hogy a megfelelő fejlesztői ág LUIS-alkalmazásra váltanak az aktuálisan használt ágban.

Azt javasoljuk, hogy ugyanazt a nevet használja mind a szolgáltatási ág, mind a fejlesztési ág LUIS-alkalmazásához, amelyet a szolgáltatási ág munkájához hoz létre, így kevésbé valószínű, hogy a helytelen alkalmazást fogja használni.

Ahogy fentebb említettük, javasoljuk, hogy az egyszerűség kedvéért egyetlen verzióban működjön az egyes fejlesztői ág-alkalmazásokban. Ha több verziót használ, ügyeljen arra, hogy aktiválja a megfelelő verziót a fejlesztői ág-alkalmazások közötti váltás során.

### <a name="multiple-developers-can-work-on-the-same-branch-concurrently"></a>Egyszerre több fejlesztő is dolgozhat ugyanazon a fióktelepen

Egyszerre több fejlesztő is dolgozhat ugyanazon a szolgáltatási ágban:

- A fejlesztők ugyanazokat a szolgáltatási ágakat és leküldéses és lekéréses változtatásokat is megtekintik, amelyeket a saját maguk és más fejlesztők is elküldtek, a szokásos módon.

- Ha követi a fent ismertetett mintát a [fejlesztőktől független ágakból](#developers-can-work-from-independent-branches), akkor ez az ág egy egyedi Luis-alkalmazást fog használni a fejlesztés támogatásához. A "dev Branch" LUIS-alkalmazást a fejlesztői csapat első tagja hozza létre, aki elkezd dolgozni a szolgáltatási ágban.

- [Csoporttagok hozzáadása közreműködőként](./luis-how-to-collaborate.md) a dev Branch Luis alkalmazáshoz.

- Ha a szolgáltatási ág működése befejeződött, exportálja a dev Branch LUIS alkalmazás aktív verzióját `.lu` a [verziók listáról](./luis-how-to-manage-versions.md), mentse a frissített `.lu` fájlt a tárházba, és jelentkezzen be, és hajtsa végre a módosításokat.

### <a name="incorporating-changes-from-one-branch-to-another-with-rebase-or-merge"></a>Az egyik ág és a másik közötti változások átépítése vagy egyesítése

Előfordulhat, hogy a csapat egy másik munkahelyén dolgozó többi fejlesztő frissítette a `.lu` forrást, és összevonta őket a fő ágra a szolgáltatási ág létrehozása után. Mielőtt folytatja a saját módosításait a szolgáltatási ágban, érdemes beépíteni a módosításokat a munkaverzióba. Ezt úgy teheti meg, [hogy a főadatbázisba való](https://git-scm.com/book/en/v2/Git-Branching-Rebasing) átállítással vagy más kóddal megegyező módon egyesíti a főverziót. Mivel a LUDown-formátumú LUIS-alkalmazás emberi olvasásra alkalmas, a szabványos egyesítési eszközök használatával támogatja az egyesítést.

Kövesse ezeket a tippeket, ha a LUIS-alkalmazást átalakítja egy szolgáltatási ágban:

- Az újraindulás vagy az egyesítés előtt győződjön meg arról, hogy az `.lu` alkalmazás forrásának helyi példánya rendelkezik a Luis-portálon alkalmazott legújabb módosításokkal, majd először exportálja az alkalmazást a portálról. Így biztos lehet benne, hogy a portálon elvégzett módosítások és még nincs exportálva nem vesznek el.

- Az egyesítés során szabványos eszközöket használhat az egyesítési ütközések feloldásához.

- Az újraindulás vagy az egyesítés befejezése után ne feledkezzen meg az alkalmazás újbóli importálásáról a portálra, így a frissített alkalmazással továbbra is alkalmazhatja a saját módosításait.

### <a name="merge-prs"></a>A PRs egyesítése

A lekéréses kérelem jóváhagyása után egyesítheti a módosításokat a fő ágra. A LUIS-alkalmazás LUDown-forrására nem vonatkozik speciális szempont: ez az ember által olvasható, és így támogatja az egyesítést a standard Merge Tools használatával. Az egyesítési ütközések ugyanúgy oldhatók fel, mint a többi forrásfájl esetében.

A PR egyesítése után javasolt a tisztítás:

- Az ág törlése a tárházban

- Törölje a szolgáltatási ág munkája során létrehozott "dev Branch" LUIS-alkalmazást.

Ugyanúgy, mint az Application Code-eszközök esetében, az egységhez tartozó teszteket kell írnia a LUIS-alkalmazás frissítéseihez. A teszteléshez folyamatos integrációs munkafolyamatokat kell alkalmaznia:

- A lekéréses kérelemben szereplő, a lekéréses kérelmeket egyesítő frissítések
- A fő ág LUIS-alkalmazás jóvá lett hagyva egy PR jóváhagyása után, és a módosítások a Mainba vannak egyesítve.

A LUIS DevOps tesztelésével kapcsolatos további információkért lásd: a [DevOps for Luis kipróbálása](luis-concept-devops-testing.md). A munkafolyamatok megvalósításával kapcsolatos további információkért lásd: [az Automation-munkafolyamatok a Luis DevOps](luis-concept-devops-automation.md).

## <a name="code-reviews"></a>Kódok véleményezése

A LUDown-formátumú LUIS-alkalmazások emberi olvasást biztosítanak, amely támogatja az olyan, felülvizsgálatra alkalmas PR-változások kommunikációját. Az egység tesztelési fájljait LUDown formátumban is meg kell írni, és a rendszer könnyen megtekinthető egy PR-ban is.

## <a name="versioning"></a>Verziókezelés

Az alkalmazások több összetevőből állnak, amelyek olyan dolgokból állhatnak, mint például a [Azure bot Service](/azure/bot-service/bot-service-overview-introduction), a [QnA Maker](https://www.qnamaker.ai/), az [Azure Speech Service](../speech-service/overview.md)és más eszközökön futó robot. A lazán összekapcsolt alkalmazások céljának eléréséhez használja a [verziókövetés](/azure/devops/learn/git/what-is-version-control) használatát, hogy az alkalmazás minden összetevője egymástól függetlenül legyen elvégezve, így a fejlesztők csak a verziószám alapján tudják felderíteni a feltörési változásokat vagy a frissítéseket. A LUIS-alkalmazás más összetevőktől függetlenül is egyszerűbb, ha a saját tárházában tartja karban.

A fő ág LUIS-alkalmazásának verziószámozási sémával kell rendelkeznie. Ha egy LUIS-alkalmazáshoz egyesíti a frissítéseket a `.lu` Main-ba, ezt a frissített forrást egy új verzióba importálhatja a fő ág Luis-alkalmazásában.

Azt javasoljuk, hogy a fő LUIS-alkalmazás verziószámához használjon numerikus verziószámozási sémát, például:

`major.minor[.build[.revision]]`

A verziószám minden frissítése az utolsó számjegynél nő.

A fő/alverzió használatával jelezheti a LUIS-alkalmazás funkcióinak változási körét:

* Főverzió: jelentős változás, például egy új [szándék](./luis-concept-intent.md) vagy [entitás](./luis-concept-entity-types.md) támogatása
* Alverzió: visszamenőlegesen kompatibilis kisebb változás, például a jelentős új képzés után
* Build: nincs funkcionalitás-változás, csak egy másik Build.

Miután meghatározta a fő Luis-alkalmazás legújabb változatának verziószámát, létre kell hoznia és tesztelni kell az új alkalmazás verzióját, és közzé kell tennie egy olyan végponton, ahol különböző Build-környezetekben, például minőségbiztosítási vagy éles környezetben is használható. Erősen ajánlott a folyamatos integrációs (CI) munkafolyamat összes lépésének automatizálása.

Lásd:
- [Automatizálási munkafolyamatok](luis-concept-devops-automation.md) , amelyekből megtudhatja, hogyan implementálhat egy CI-munkafolyamatot egy Luis-alkalmazás teszteléséhez és kiadásához.
- [Kiadási felügyelet](luis-concept-devops-automation.md#release-management) a Luis-alkalmazás üzembe helyezésével kapcsolatos információkért.

### <a name="versioning-the-feature-branch-luis-app"></a>A "feature Branch" LUIS-alkalmazás verziószámozása

Ha olyan "fejlesztői ág" LUIS-alkalmazással dolgozik, amelyet a szolgáltatásban végzett munka támogatásához hozott létre, akkor exportálja az alkalmazást, amikor befejezte a munkát, és tartalmazza a frissítését is `'lu` . A tárházban található ágat, a "dev Branch" LUIS alkalmazást pedig törölni kell, miután a rendszer beolvadt a Mainba. Mivel ez az alkalmazás kizárólag a szolgáltatási ág munkájának támogatásához szükséges, nincs szükség az alkalmazáson belüli, adott verziószámozási sémára.

Ha a lekéréses eljárásban végzett módosítások a főverzióba vannak egyesítve, akkor a verziószámozást kell alkalmazni, hogy a Main összes frissítése egymástól függetlenül legyen.

## <a name="next-steps"></a>Következő lépések

* További információ a [Luis DevOps teszteléséről](luis-concept-devops-testing.md)
* Ismerje meg, hogyan valósítható meg a [DevOps for Luis a GitHub](luis-how-to-devops-with-github.md) használatával
