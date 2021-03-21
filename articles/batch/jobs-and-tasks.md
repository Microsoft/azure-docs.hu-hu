---
title: Feladatok és feladatok a Azure Batchban
description: Ismerje meg a feladatokat és a feladatokat, valamint azt, hogyan használják őket egy Azure Batch munkafolyamatban fejlesztési szempontból.
ms.topic: conceptual
ms.date: 11/23/2020
ms.openlocfilehash: e1ca721ec7527d9d042c129c22cf0266e57c32e9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95808592"
---
# <a name="jobs-and-tasks-in-azure-batch"></a>Feladatok és feladatok a Azure Batchban

Azure Batch a *feladat* számítási egységet jelöl. A *feladat* ezen feladatok gyűjteménye. További információ a feladatokról és a feladatokról, valamint arról, hogyan használják őket egy Azure Batch munkafolyamatban.

## <a name="jobs"></a>Feladatok

A feladatok tevékenységek gyűjteményei. A feladatok határozzák meg, hogyan végezzék el a hozzájuk tartozó tevékenységek a számítási feladatokat a készlet számítási csomópontjaiban.

A feladatok azt a [készletet](nodes-and-pools.md#pools) határozzák meg, amelyben a munkafolyamatot futtatni szeretné. Az egyes feladatokhoz saját készletet hozhat létre, de egyetlen készletet is használhat több feladathoz. Létrehozhat egy készletet minden egyes feladathoz, amely [egy feladatütemezés társítva van, vagy](#scheduled-jobs)egy készletet a feladatütemezés összes feladatához.

### <a name="job-priority"></a>A feladatok prioritása

Az Ön által létrehozott feladatokhoz választható feladat-prioritást rendelhet. A Batch szolgáltatás a feladat prioritási értékével határozza meg az ütemezés sorrendjét (a feladaton belüli összes feladathoz), wtihin az egyes készleteket.

Egy feladat prioritásának frissítéséhez hívja meg a feladat (batch REST) [tulajdonságainak frissítését](/rest/api/batchservice/job/update) , vagy módosítsa a [CloudJob. priority](/dotnet/api/microsoft.azure.batch.cloudjob) (Batch .net) tulajdonságot. A prioritási értékek a-1000 (legalacsonyabb prioritás) és a 1000 (a legmagasabb prioritás) közé esnek.

Ugyanazon a készleten belül a magasabb prioritású feladatok ütemezési elsőbbséget élveznek az alacsonyabb prioritású feladatokkal szemben. A már futó alacsonyabb prioritású feladatokhoz tartozó feladatokat nem előzik egy magasabb prioritású feladat feladatai. Az azonos prioritási szinttel rendelkező feladatok ütemezése és a feladat-végrehajtás sorrendje nincs meghatározva.

Az egyik készletben futó, magas prioritású értékkel rendelkező feladatok nem befolyásolják a különálló készletben vagy egy másik batch-fiókban futó feladatok ütemezését. A feladat prioritása nem vonatkozik a feladat elküldésekor létrehozott [autopoolokra](nodes-and-pools.md#autopools).

### <a name="job-constraints"></a>Feladatok megkötései

A feladatok korlátozásai segítségével korlátokat szabhat a feladatokhoz:

- Beállíthatja a **maximális valós időt**, ami azt jelenti, hogy ha egy feladat az itt megadott időtartamnál hosszabban fut, a rendszer leállítja a feladatot és a hozzá tartozó összes tevékenységet.
- Megadhatja a **feladat-újrapróbálkozások maximális számát** korlátozásként, beleértve azt is, hogy a feladat mindig újrapróbálkozik-e, vagy soha nem próbálkozik újra. A feladat ismételt kipróbálása azt jelenti, hogy ha a feladat meghiúsul, a rendszer újra várólistára helyezi, hogy újra fusson.

### <a name="job-manager-tasks-and-automatic-termination"></a>Feladatkezelő feladatai és automatikus lezárása

Tevékenységeket az ügyfélalkalmazás is adhat a feladatokhoz, vagy megadhat egy [feladatkezelői tevékenységet](#job-manager-task) is. A feladatkezelői tevékenységek tartalmazzák a feladatok tevékenységeinek létrehozásához szükséges információkat, és a készlet egyik számítási csomópontján futnak. A Feladatkezelő feladatot kifejezetten a Batch kezeli. a rendszer azonnal várólistára helyezi a feladatot, ha az nem sikerül. A feladatütemezés által létrehozott feladatok esetében szükség van egy Feladatkezelő-feladatra [, mert](#scheduled-jobs)ez az egyetlen módszer a feladatok definiálására a feladat példányainak létrehozása előtt.

Alapértelmezés szerint a feladatok akkor is aktív állapotban maradnak, ha már a hozzájuk tartozó összes tevékenység lefutott. Ezt módosíthatja, és beállíthatja, hogy a rendszer automatikusan megszüntesse a feladatot, amikor az ahhoz tartozó összes tevékenység befejeződött. Állítsa be a feladat **onAllTasksComplete** tulajdonságát ([onAllTasksComplete](/dotnet/api/microsoft.azure.batch.cloudjob) a Batch .net-ben) a `terminatejob` * "értékre a feladat automatikus leállításához, ha az összes tevékenység befejeződött állapotban van.

A Batch szolgáltatás olyan feladatot tekint, amely *nem* teljesíti az összes feladatot. Ezért ezt a funkciót általában egy [feladatkezelői tevékenységgel](#job-manager-task) használjuk. Ha az automatikus feladat-leállítást a Feladatkezelő nélkül szeretné használni, először állítsa be az új feladat **onAllTasksComplete** tulajdonságát a értékre `noaction` , majd állítsa a `terminatejob` * "értékre, miután befejezte a feladatok hozzáadását a feladathoz.

### <a name="scheduled-jobs"></a>Ütemezett feladatok

A [Feladatütemezéssel](/rest/api/batchservice/jobschedule) rendszeresen előforduló feladatokat ütemezhet a Batch szolgáltatásban. A feladatütemezés meghatározza, mikor fussanak a feladatok, és tartalmazza a futtatandó feladatok specifikációit. Megadhatja az ütemezés időtartamát (mennyi ideig és mikor van érvényben az ütemezés), valamint azt, hogy a rendszer milyen gyakran hozza létre a feladatokat az ütemezett időszakban.

## <a name="tasks"></a>Feladatok

A tevékenységek olyan számítási egységek, amelyek feladathoz vannak társítva, és egy csomóponton futnak. A tevékenységek egy csomóponthoz vannak társítva futtatáshoz, vagy a várólistán vannak, amíg egy csomópont szabaddá válik. Egyszerűen fogalmazva a tevékenységek egy vagy több programot vagy parancsfájlt futtatnak a számítási csomóponton, és ezzel elvégzik az Önnek szükséges feladatokat.

Amikor létrehozza a tevékenységet, a következőket kell megadnia:

- A tevékenységhez tartozó **parancssort**. Ez a parancssor futtatja az alkalmazást vagy parancsfájlt a számítási csomóponton.

    Fontos megjegyezni, hogy a parancssor nem egy rendszerhéj alatt fut. Így nem tudja natívan kihasználni a rendszerhéj előnyeit, például a [környezeti változók](#environment-settings-for-tasks) értékének behelyettesítését (ide tartozik a `PATH` is). Az ilyen funkciók kihasználásához a rendszerhéjat a parancssorban kell meghívnia, például a `cmd.exe` Windows-csomópontokon vagy Linuxon történő indítással `/bin/sh` :

    `cmd /c MyTaskApplication.exe %MY_ENV_VAR%`

    `/bin/sh -c MyTaskApplication $MY_ENV_VAR`

    Ha a tevékenységeknek olyan alkalmazást vagy parancsfájlt is futtatniuk kell, amely nem tartozik a csomópont `PATH`- vagy referenciakörnyezet-változói közé, hívja meg explicit módon a rendszerhéját a feladat parancssorából.
- A feldolgozni kívánt adatokat tartalmazó **erőforrásfájlok**. Ezeket a fájlokat a rendszer automatikusan a csomópontra másolja egy Azure Storage-fiók Blob Storage-tárhelyéből, mielőtt végrehajtaná a tevékenység parancssorát. További információ: [Start Task](#start-task) and [Files and könyvtárak](files-and-directories.md).
- Az alkalmazás számára szükséges **környezeti változók**. További információ: [környezeti beállítások a feladatokhoz](#environment-settings-for-tasks).
- Azok a **korlátozások**, amelyeken belül a tevékenység végrehajtható. Korlátozás például a maximális időtartam, ameddig a tevékenység futhat, a meghiúsult tevékenységek újrapróbálásának maximális száma, valamint a tevékenység munkakönyvtárában tárolt fájlok megőrzésének maximális ideje.
- Az ütemezett tevékenység futtatására beállított számítási csomópontra üzembe helyezni kívánt **alkalmazáscsomagok**. Az [alkalmazáscsomagok](batch-application-packages.md) leegyszerűsítik a tevékenységek által futtatott alkalmazások üzembe helyezését és verziókezelését. A tevékenységszintű alkalmazáscsomagok különösen megosztott készletes környezetekben hasznosak, ahol a különböző feladatok egy készletben futnak, és a rendszer nem törli a készletet a feladat befejezésekor. Ha a feladatnál a készletben kevesebb a tevékenység, mint a csomópont, az alkalmazáscsomagok használatával csökkentheti az adatátviteli igényt, mivel így a rendszer csak azokon a csomópontokon helyezi üzembe az alkalmazást, amelyek ténylegesen futtatják a tevékenységeket.
- Egy, a Docker Hubban vagy egy privát beállításjegyzékben található **tárolórendszerkép**-referencia és további beállítások egy olyan Docker-tároló létrehozásához, amelyben a feladatok a csomóponton futnak. Ezt az információt csak akkor kell meghatározni, ha a készlet tárolókonfigurációval van beállítva.

> [!NOTE]
> A feladat maximális élettartama, amikor a feladat bekerül a feladatba, 180 nap. A Befejezett feladatok 7 napig maradnak; a maximális élettartamon belül nem befejezett feladatok adatai nem érhetők el.

A csomóponton számítások végrehajtásához definiált feladatok mellett a Batch szolgáltatás számos speciális feladatot is biztosít:

- [Indítási tevékenység](#start-task)
- [Feladatkezelő-tevékenység](#job-manager-task)
- [Feladat-előkészítési és -kiadási tevékenységek](#job-preparation-and-release-tasks)
- [Többpéldányos tevékenységek](#multi-instance-task)
- [Tevékenységfüggőségek](#task-dependencies)

### <a name="start-task"></a>Indítási tevékenység

A készlethez társított indítási tevékenység segítségével előkészítheti a csomópontok működési környezetét. Elvégeztethet például különböző műveleteket, például a tevékenységek által futtatandó alkalmazások telepítését, illetve a háttérfolyamatok elindítását. Az indítási tevékenység minden egyes csomópont indításakor fut, amíg a készletben marad. Ez magában foglalja azt is, hogy a rendszer mikor adja hozzá először a csomópontot a készlethez, illetve mikor induljon újra vagy rendszerképbe.

Az indítási tevékenység elsődleges előnye, hogy tartalmazhatja a számítási csomópontok konfigurálásához szükséges, illetve a feladatok végrehajtásához szükséges alkalmazások telepítéséhez szükséges összes információt. Így a készletekben működő csomópontok számának növelése rendkívül egyszerű, csupán a csomópontok új tervezett számát kell meghatároznia. Az indítási tevékenység biztosítja azokat az információkat, amelyek szükségesek ahhoz, hogy a Batch szolgáltatás konfigurálja az új csomópontokat, és készen álljon a feladatok fogadására.

Ahogy a többi Azure Batch-tevékenységnél, itt is megadhatja az [Azure Storage](../storage/index.yml)-tárterületen tárolt erőforrásfájlokat, illetve a futtatandó parancssort. A Batch szolgáltatás először az erőforrásfájlokat másolja a csomópontra az Azure Storage-tárterületből, majd futtatja a megadott parancssort. A készletekhez tartozó indítási tevékenységek esetében a fájllista általában a tevékenységhez tartozó alkalmazást és annak függőségeit tartalmazza.

Ezeken felül azonban szerepelhetnek az indítási tevékenységben a számítási csomóponton futó összes tevékenység számára felhasználható referenciaadatok is. Egy indítási tevékenység parancssora például végrehajthat egy `robocopy` műveletet, amely a Start tevékenység [munkakönyvtárában](files-and-directories.md) , a **megosztott** mappába, majd egy MSI-fájl vagy a futtatásával másolt alkalmazásfájlok másolására szolgál. `setup.exe`

Általában célszerű, hogy a Batch szolgáltatás megvárja az indítási tevékenység befejezését, mielőtt a csomópontot késznek ítéli tevékenységek hozzárendelésére, de ez konfigurálható.

Ha egy indítási tevékenység meghiúsul egy számítási csomóponton, akkor a csomópont állapota úgy frissül, hogy tükrözze a hibát, és a csomóponthoz nem lesz hozzárendelve egyetlen tevékenység sem. Az indítási tevékenység meghiúsulhat, ha hiba történik az erőforrásfájloknak a tárterületről való másolásakor, illetve ha a parancssor által futtatott folyamat nullától eltérő kilépési kódot ad vissza.

Amikor meglévő készlethez ad indítási tevékenységet, vagy ilyen készlethez tartozó tevékenységet frissít, újra kell indítania a számítási csomópontokat, hogy a rendszer az összes csomópontra alkalmazza az indítási tevékenységet.

>[!NOTE]
> A Batch korlátozza az indítási tevékenységek méretét, amibe az erőforrásfájlok és a környezeti változók is beletartoznak. Ha csökkentenie kell egy indítási tevékenység méretét, a következő két megoldás közül választhat:
>
> 1. Az alkalmazáscsomagok segítségével alkalmazások vagy adatok terjeszthetők a Batch-készlet összes csomópontján. Az alkalmazáscsomagokkal kapcsolatban további információkat a [Batch-alkalmazáscsomagokkal számítási csomópontokra végzett alkalmazástelepítést](batch-application-packages.md) ismertető cikkben talál.
> 2. Manuálisan is létrehozhatja az alkalmazások fájljait tartalmazó tömörített archívumot. Töltse fel a tömörített archívumot az Azure Storage-ba blobként. Adja meg a kezdő tevékenységhez erőforrásfájlként a ZIP-archívumot. A kezdő tevékenység parancssorának futtatása előtt csomagolja ki a parancssorból az archívumot. 
>
>    Az archívum kibontásához használhatja az Ön által választott archiválási eszközt. Mellékelnie kell azt az eszközt, amellyel az archívumot kicsomagolja a kezdő tevékenység erőforrás-fájljaként.

### <a name="job-manager-task"></a>Feladatkezelő tevékenység

A feladatok végrehajtásának szabályozása és/vagy figyelése általában egy Feladatkezelő feladat használatával végezhető el. A Feladatkezelő feladatai például gyakran a feladatok feladatainak létrehozására és elküldésére, a futtatandó további feladatok meghatározására és a munka befejezésének meghatározására szolgálnak.

A feladatkezelői tevékenység azonban más célokra is használható, Ez egy teljes értékű feladat, amely a feladathoz szükséges műveleteket hajthatja végre. A feladatkezelői tevékenység például letöltheti a paraméterként meghatározott fájlt, elemezheti a fájl tartalmát, és további tevékenységeket küldhet el a tartalom alapján.

A rendszer minden más feladat előtt indítja el a feladatkezelői tevékenységeket. Ez a tevékenység a következő jellemzőkkel bír:

- A Batch szolgáltatás automatikusan küldi el ezeket tevékenységekként a feladat létrehozásakor.
- Úgy vannak ütemezve, hogy a feladatok más tevékenységei előtt fussanak.
- A hozzájuk társított csomópontot távolítja el a rendszer utoljára a készletből a készlet méretének csökkentésekor.
- A befejezésük a feladat összes tevékenységének befejezéséhez köthető.
- A feladatkezelői tevékenység a legmagasabb prioritást kapja, ha újra kell indítani. Ha egy tétlen csomópont nem érhető el, a Batch szolgáltatás leállíthatja valamelyik másik futó tevékenységet a készletben, hogy helyet szabadítson fel a feladatkezelői tevékenység futtatásához.
- Az egyik feladat feladatkezelő tevékenysége nem rendelkezik nagyobb prioritással, mint más feladatok tevékenységei. A feladatok között csak a feladatszintű prioritások érvényesek.

### <a name="job-preparation-and-release-tasks"></a>Feladat-előkészítési és -kiadási tevékenységek

A Batch feladat-előkészítési feladatokat biztosít a feladat előtti végrehajtáshoz, valamint a feladat-közzététel utáni feladatokat a feladatok karbantartásához vagy tisztításához.

A feladat-előkészítési tevékenység minden olyan számítási csomóponton fut, amely a feladatok futtatására van ütemezve, a többi feladat végrehajtása előtt. Használhat például egy feladat-előkészítési feladatot az összes feladat által megosztott adatok másolásához, de a feladat egyedi.

Egy feladat befejezése után egy feladat kiadására szolgáló tevékenység fut a készlet minden olyan csomópontján, amely legalább egy feladatot végrehajt. Egy feladat kiadási feladata például törölheti a feladat-előkészítési feladattal másolt adatok körét, vagy tömörítheti és feltöltheti a diagnosztikai napló adatait.

Mind a feladat-előkészítési, mind a feladatkiadási tevékenységeknél megadhat egy parancssort, amelyet futtatni szeretne a tevékenység meghívásakor. Ezek segítségével számos különböző funkciót érhet el, például fájlokat tölthet le, emelt jogosultsági szintű futtatást végezhet, egyéni környezeti változókat adhat meg, illetve beállíthatja a maximális végrehajtási időt, az újrapróbálások számát, illetve a fájlmegőrzési időt.

A feladatelőkészítési és -kiadási tevékenységekkel kapcsolatos további információért lásd: [Feladat-előkészítési és -befejezési műveletek futtatása Azure Batch számítási csomópontokon](batch-job-prep-release.md).

### <a name="multi-instance-task"></a>Többpéldányos tevékenység

A [többpéldányos tevékenységek](batch-mpi.md) olyan tevékenységek, amelyek több számítási csomóponton való egyidejű futtatásra vannak konfigurálva. A többpéldányos feladatok lehetővé teszik olyan nagy teljesítményű számítástechnikai forgatókönyvek használatát, amelyek olyan számítási csomópontok csoportját igénylik, amelyek egy adott munkaterhelés, például az üzenet elküldéses felület (MPI) feldolgozására vannak lefoglalva.

A Batch szolgáltatásban az MPI-feladatok Batch .NET-könyvtárral való futtatásának részletes leírásáért lásd: [Use multi-instance tasks to run Message Passing Interface (MPI) applications in Azure Batch](batch-mpi.md) (Többpéldányos tevékenységek használata Message Passing Interface- (MPI-) alkalmazások futtatásához az Azure Batch szolgáltatásban).

### <a name="task-dependencies"></a>Tevékenységfüggőségek

A [feladat függőségei](batch-task-dependencies.md), ahogy a név is jelenti, lehetővé teszi annak megadását, hogy a feladat a végrehajtás előtt más feladatok teljesítésétől függ. Ez a funkció olyan helyzeteket támogat, amelyekben egy „alsóbb rétegbeli” tevékenység egy „felsőbb rétegbeli” tevékenység kimenetét használja, vagy amikor egy felsőbb rétegbeli tevékenység alsóbb rétegbeli tevékenység által igényelt inicializálást végez.

A szolgáltatás használatához először engedélyeznie kell a [feladat függőségeit](batch-task-dependencies.md#enable-task-dependencies
) a Batch-feladatban. Ezután az egy másik (vagy sok másik) tevékenységtől függő mindegyik tevékenységhez meg kell adnia azokat a tevékenységeket, amelyektől függnek.

A tevékenységfüggőségekkel a következőkhöz hasonló forgatókönyveket konfigurálhat:

- a *taskB* a *Taska* függvénytől függ (a *taskB* nem kezdi meg a végrehajtást, amíg a *taske* nem fejeződött be).
- a *taskC* a *Taska* és a *taskB* függvénytől függ.
- A *taskD* egy tevékenységtartománytól függ, például az *1* – *10.* tevékenység befejeződéséig nem hajtja végre a rendszer.

További részletekért tekintse meg az [Azure-batch-Samples](https://github.com/Azure-Samples/azure-batch-samples) GitHub-tárházban található [feladatok függőségei a Azure batch](batch-task-dependencies.md) és a [TaskDependencies](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp/ArticleProjects/TaskDependencies) kód mintát.

### <a name="environment-settings-for-tasks"></a>Környezeti beállítások tevékenységekhez

A Batch-szolgáltatás által végrehajtott minden egyes feladat hozzáférhet azokhoz a környezeti változókhoz, amelyeket a számítási csomópontokon beállít. Ez magában foglalja a Batch szolgáltatás által definiált környezeti változókat (a[szolgáltatás által definiált](./batch-compute-node-environment-variables.md) és a feladatokhoz definiálható egyéni környezeti változókat is). A tevékenységek által végrehajtott alkalmazások és parancsfájlok a végrehajtás során szintén elérik ezeket a környezeti változókat.

Az egyéni környezeti változókat a tevékenységek és a feladatok szintjén is megadhatja: ehhez töltse ki a kívánt elemek *környezeti beállítások* tulajdonságait. További részletekért tekintse meg a [feladat hozzáadása egy feladathoz](/rest/api/batchservice/task/add?)] műveletet (batch REST API) vagy a [CloudTask. EnvironmentSettings](/dotnet/api/microsoft.azure.batch.cloudtask) és a [CloudJob. CommonEnvironmentSettings](/dotnet/api/microsoft.azure.batch.cloudjob) tulajdonságot a Batch .net-ben.

Az ügyfélalkalmazás vagy szolgáltatás a [Get information about a task](/rest/api/batchservice/task/get) (Tevékenység információinak lekérése) művelet (Batch REST) segítségével, vagy a [CloudTask.EnvironmentSettings](/dotnet/api/microsoft.azure.batch.cloudtask) tulajdonság (Batch .NET) elérésével képes beszerezni a tevékenység (szolgáltatás által meghatározott és egyéni) környezeti változóit. A számítási csomóponton végrehajtott folyamatok például a közismert `%VARIABLE_NAME%` (Windows) vagy a `$VARIABLE_NAME` (Linux) szintaxis segítségével képesek elérni ezeket és más környezeti változókat a csomóponton.

Az összes szolgáltatás által definiált környezeti változót tartalmazó teljes listát megtalálja a [Számítási csomópont környezeti változói](batch-compute-node-environment-variables.md) című részben.

## <a name="next-steps"></a>Következő lépések

- Tudnivalók a [fájlokról és a címtárakról](files-and-directories.md).
