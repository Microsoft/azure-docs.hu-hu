---
title: Webjobs-feladatok fejlesztése és üzembe helyezése a Visual Studióval
description: Megtudhatja, hogyan fejlesztheti Azure WebJobs a Visual Studióban, és hogyan helyezheti üzembe azokat Azure App Service, beleértve az ütemezett feladatok létrehozását is.
author: ggailey777
ms.assetid: a3a9d320-1201-4ac8-9398-b4c9535ba755
ms.topic: conceptual
ms.custom: devx-track-csharp, vs-azure
ms.date: 07/30/2020
ms.author: glenga
ms.reviewer: david.ebbo;suwatch;pbatum;naren.soni
ms.openlocfilehash: de10903be86b52b3415b57a53be81e7fd1661f63
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "89226029"
---
# <a name="develop-and-deploy-webjobs-using-visual-studio"></a>Webjobs-feladatok fejlesztése és üzembe helyezése a Visual Studióval

Ez a cikk azt ismerteti, hogyan használható a Visual Studio a konzolos alkalmazások projektjeinek egy webalkalmazásba való üzembe helyezéséhez [Azure app Service](overview.md) [Azure-webjobs](https://go.microsoft.com/fwlink/?LinkId=390226). A webjobs [Azure Portal](https://portal.azure.com)használatával történő telepítésével kapcsolatos további információkért lásd: [háttérben futó feladatok futtatása a Azure app Service webjobs](webjobs-create.md)szolgáltatással.

Dönthet úgy, hogy olyan Webjobs fejleszt, amely [.net Core-alkalmazásként](#webjobs-as-net-core-console-apps) vagy .net- [keretrendszerbeli alkalmazásként](#webjobs-as-net-framework-console-apps)fut. Az [Azure WEBJOBS SDK](webjobs-sdk-how-to.md) 3. x verziója lehetővé teszi, hogy a .net Core-alkalmazásként vagy a .NET-keretrendszer alkalmazásaiként futó webjobs-feladatokat fejlesszen, míg a 2. x verzió csak a .NET-keretrendszert támogatja. A webjobs-projektek üzembe helyezésének módja a .NET-keretrendszer projektjeihez képest eltérő a .NET Core-projektekben.

Több webfeladatot is közzétehet egyetlen webalkalmazásban, ha egy webalkalmazás minden Webjobs egyedi névvel rendelkezik.

## <a name="webjobs-as-net-core-console-apps"></a>Webjobs-feladatok .NET Core Console-alkalmazásként

Az Azure WebJobs SDK 3. x verziójával webjobs-feladatokat hozhat létre és tehet közzé .NET Core Console-alkalmazásként. A .NET Core Console-alkalmazások Azure-beli Webjobs való létrehozásával és közzétételével kapcsolatos részletes utasításokért lásd: Ismerkedés [a Azure WEBJOBS SDK-val eseményvezérelt háttér-feldolgozáshoz](webjobs-sdk-get-started.md).

> [!NOTE]
> A .NET Core webjobs-feladatok nem csatolhatók webes projektekhez. Ha a Webjobs egy webalkalmazással kell üzembe helyeznie, [a webjobs-t .net-keretrendszerbeli konzol alkalmazásként kell létrehoznia](#webjobs-as-net-framework-console-apps).  

### <a name="deploy-to-azure-app-service"></a>Üzembe helyezés az Azure App Service-ben

A .NET Core-Webjobs a Visual studióból való Azure App Service való közzététele ugyanazokat az eszközöket használja, mint a ASP.NET Core alkalmazás közzététele.

[!INCLUDE [webjobs-publish-net-core](../../includes/webjobs-publish-net-core.md)] 

## <a name="webjobs-as-net-framework-console-apps"></a>Webjobs-feladatok .NET-keretrendszerbeli konzol alkalmazásaiként  

Ha a Visual Studióval telepít egy webjobs-kompatibilis .NET-keretrendszer konzolos alkalmazást, a futásidejű fájlokat a webalkalmazás megfelelő mappájába másolja (*App_Data/Jobs/Continuous* a folyamatos webjobs-feladatok számára, és *App_Data/Jobs/Triggered* az ütemezett vagy igény szerinti webjobs-feladatok esetében).

A Visual Studio hozzáadja a következő elemeket egy webjobs-kompatibilis projekthez:

* A [Microsoft. Web. webjobs. publish](https://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) NuGet-csomag.
* A központi telepítés és a Feladatütemező beállításait tartalmazó fájl [webjob-publish-settings.js](#publishsettings) . 

![Ábra, amely bemutatja, hogy a rendszer hogyan adja hozzá a Webjobs az üzembe helyezést a konzol alkalmazáshoz](./media/webjobs-dotnet-deploy-vs/convert.png)

Ezeket az elemeket hozzáadhat egy meglévő konzolos alkalmazás-projekthez, vagy használhat egy sablont egy új webjobs-kompatibilis Console-alkalmazás projekt létrehozásához. 

Saját maga is üzembe helyezhet egy projektet Webjobs, vagy összekapcsolhatja azt egy webes projekttel, hogy az automatikusan üzembe kerüljön a webes projekt telepítésekor. A projektek összekapcsolásához a Visual Studio tartalmazza a webjobs-kompatibilis projekt nevét a webes projektben lévő fájl [webjobs-list.js](#webjobslist) .

![A webes projekthez kapcsolódó Webjobs bemutató diagram](./media/webjobs-dotnet-deploy-vs/link.png)

### <a name="prerequisites"></a>Előfeltételek

Telepítse a Visual Studio 2017 vagy a Visual Studio 2019 alkalmazást az [Azure-fejlesztési](/visualstudio/install/install-visual-studio#step-4---choose-workloads)számítási feladattal.

### <a name="enable-webjobs-deployment-for-an-existing-console-app-project"></a><a id="convert"></a> Webjobs-telepítés engedélyezése meglévő konzolos alkalmazás projekthez

Erre két lehetősége van:

* [Automatikus központi telepítés engedélyezése webes projekttel](#convertlink).

  Konfiguráljon egy meglévő konzolos alkalmazás-projektet, hogy az automatikusan Webjobs telepítsen egy webes projekt telepítésekor. Akkor használja ezt a beállítást, ha a Webjobs ugyanabban a webalkalmazásban szeretné futtatni, amelyben a kapcsolódó webalkalmazást futtatja.

* [Az üzembe helyezést webes projekt nélkül is engedélyezheti](#convertnolink).

  Konfiguráljon egy meglévő konzolos alkalmazás-projektet úgy, hogy saját maga Webjobs, webes projektre mutató hivatkozás nélkül. Akkor használja ezt a beállítást, ha saját maga szeretné futtatni a Webjobs egy webalkalmazásban, és a webalkalmazásban nem fut a webes alkalmazás. Előfordulhat, hogy a webalkalmazás-erőforrásoktól függetlenül szeretné méretezni a Webjobs-erőforrásokat.

#### <a name="enable-automatic-webjobs-deployment-with-a-web-project"></a><a id="convertlink"></a> Automatikus webjobs-telepítés engedélyezése webes projekttel

1. Kattintson a jobb gombbal a webes projektre **megoldáskezelő**, majd válassza a  >  **meglévő projekt hozzáadása Azure webjobs** lehetőséget.
   
    ![Meglévő projekt Azure Webjobs](./media/webjobs-dotnet-deploy-vs/eawj.png)
   
    Megjelenik az [Azure-Webjobs hozzáadása](#configure) párbeszédpanel.
2. A **projekt neve** legördülő listában válassza ki azt a webjobs, amelyet hozzá szeretne adni.
   
    ![A projekt kiválasztása az Azure Webjobs hozzáadása párbeszédpanelen](./media/webjobs-dotnet-deploy-vs/aaw1.png)
3. Fejezze be az [Azure-Webjobs hozzáadása](#configure) párbeszédpanelt, majd kattintson **az OK gombra**. 

#### <a name="enable-webjobs-deployment-without-a-web-project"></a><a id="convertnolink"></a> Webjobs-telepítés engedélyezése webes projekt nélkül
1. Kattintson a jobb gombbal a Console app-projektre **megoldáskezelő**, majd válassza a **Közzététel Azure webjobs** lehetőséget. 
   
    ![Közzététel Azure-Webjobs](./media/webjobs-dotnet-deploy-vs/paw.png)
   
    Megjelenik az [Azure-Webjobs hozzáadása](#configure) párbeszédpanel, ahol a projekt **neve** mezőben ki van választva.
2. Fejezze be az [Azure-Webjobs hozzáadása](#configure) párbeszédpanelt, majd kattintson **az OK gombra**.
   
   Megjelenik a **webes közzététel** varázsló. Ha nem szeretne azonnal közzétenni, a varázsló bezárásával. A megadott beállításokat a rendszer a [projekt központi telepítésekor](#deploy)menti.

### <a name="create-a-new-webjobs-enabled-project"></a><a id="create"></a>Új webjobs-kompatibilis projekt létrehozása
Új webjobs-kompatibilis projekt létrehozásához használja a Console app Project sablont, és engedélyezze a webjobs-telepítést [az előző szakaszban](#convert)leírtak szerint. Másik lehetőségként használhatja a webjobs új-projekt sablont:

* [A webjobs új-Project sablonjának használata egy független Webjobs](#createnolink)
  
    Hozzon létre egy projektet, és konfigurálja úgy, hogy magát Webjobs-ként telepítse, és ne legyen webes projektre mutató hivatkozás. Akkor használja ezt a beállítást, ha saját maga szeretné futtatni a Webjobs egy webalkalmazásban, és a webalkalmazásban nem fut a webes alkalmazás. Előfordulhat, hogy a webalkalmazás-erőforrásoktól függetlenül szeretné méretezni a Webjobs-erőforrásokat.
* [A webjobs új-Project sablonjának használata webes projekthez kapcsolódó Webjobs](#createlink)
  
    Hozzon létre egy projektet, amely úgy van beállítva, hogy automatikusan Webjobs telepítsen, amikor egy webprojektet ugyanarra a megoldásba helyez üzembe. Akkor használja ezt a beállítást, ha a Webjobs ugyanabban a webalkalmazásban szeretné futtatni, amelyben a kapcsolódó webalkalmazást futtatja.

> [!NOTE]
> A webjobs New-Project sablon automatikusan telepíti a NuGet csomagokat, és a *program. cs* tartalmazza a [webjobs SDK](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs)-hoz tartozó kódot. Ha nem kívánja használni a webjobs SDK-t, távolítsa el vagy módosítsa a `host.RunAndBlock` *program. cs* utasításait.
> 
> 

#### <a name="use-the-webjobs-new-project-template-for-an-independent-webjob"></a><a id="createnolink"></a> A webjobs új-Project sablonjának használata egy független Webjobs
1. Válassza a **fájl**  >  **új**  >  **projekt** lehetőséget. A **Crete a New Project (új projekt** ) párbeszédpanelen keresse meg és válassza ki a C# nyelvhez tartozó **Azure webjobs (.NET-keretrendszer)** elemet.
   
2. Kövesse az előző útmutatást, hogy [a Console app Project egy független webjobs-projekt legyen](#convertnolink).

#### <a name="use-the-webjobs-new-project-template-for-a-webjob-linked-to-a-web-project"></a><a id="createlink"></a> A webjobs új-Project sablonjának használata webes projekthez kapcsolódó Webjobs
1. Kattintson a jobb gombbal a webes projektre **megoldáskezelő**, majd válassza az  >  **új Azure webjobs-projekt** hozzáadása lehetőséget.
   
    ![Új Azure Webjobs projekt menü bejegyzés](./media/webjobs-dotnet-deploy-vs/nawj.png)
   
    Megjelenik az [Azure-Webjobs hozzáadása](#configure) párbeszédpanel.
2. Fejezze be az [Azure-Webjobs hozzáadása](#configure) párbeszédpanelt, majd kattintson **az OK gombra**.


### <a name="webjob-publish-settingsjson-file"></a><a id="publishsettings"></a> Fájlwebjob-publish-settings.js
Ha a webjobs-telepítéshez konfigurál egy konzol alkalmazást, a Visual Studio telepíti a [Microsoft. Web. Webjobss. publish](https://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) NuGet csomagot, és az ütemezési információkat a webjobs-projekt Project *Properties* mappájában található fájl *webjob-publish-settings.js* tárolja. Íme egy példa erre a fájlra:

```json
{
  "$schema": "http://schemastore.org/schemas/json/webjob-publish-settings.json",
  "webJobName": "WebJob1",
  "startTime": "null",
  "endTime": "null",
  "jobRecurrenceFrequency": "null",
  "interval": null,
  "runMode": "Continuous"
}
```

Ezt a fájlt közvetlenül szerkesztheti, a Visual Studio pedig IntelliSense-t is biztosít. A fájl sémája a (z) helyen található [https://schemastore.org](http://schemastore.org/schemas/json/webjob-publish-settings.json) , és itt tekinthető meg.  

### <a name="webjobs-listjson-file"></a><a id="webjobslist"></a> Fájlwebjobs-list.js
Amikor webjobs-kompatibilis projektet kapcsol össze egy webes projekthez, a Visual Studio a webes projekt *Tulajdonságok* mappájában tárolja *webjobs-list.jsa* webjobs-projekt nevét. A lista több webjobs-projektet is tartalmazhat, ahogy az alábbi példában is látható:

```json
{
  "$schema": "http://schemastore.org/schemas/json/webjobs-list.json",
  "WebJobs": [
    {
      "filePath": "../ConsoleApplication1/ConsoleApplication1.csproj"
    },
    {
      "filePath": "../WebJob1/WebJob1.csproj"
    }
  ]
}
```

Ezt a fájlt közvetlenül a Visual Studióban, az IntelliSense használatával szerkesztheti. A fájl sémáját a következő helyen tárolja: [https://schemastore.org](http://schemastore.org/schemas/json/webjobs-list.json) .

### <a name="deploy-a-webjobs-project"></a><a id="deploy"></a>Webjobs-projekt üzembe helyezése
Egy webjobs-projekt, amelyet webes projekthez társított, automatikusan üzembe helyezi a webes projekttel. További információ a webes projekt központi telepítéséről: **útmutató** az  >  **alkalmazás üzembe helyezéséhez** a bal oldali navigációs sávon.

Webjobs-projekt saját maga általi üzembe helyezéséhez kattintson a jobb gombbal a projektre **megoldáskezelő** , majd válassza a **Közzététel Azure webjobs** lehetőséget. 

![Közzététel Azure-Webjobs](./media/webjobs-dotnet-deploy-vs/paw.png)

Egy független Webjobs esetében a webes projektekhez használt **közzétételi webes** varázsló jelenik meg, de a módosításhoz kevesebb beállítás áll rendelkezésre.

### <a name="add-azure-webjob-dialog-box"></a><a id="configure"></a>Azure-Webjobs hozzáadása párbeszédpanel
Az **Azure-Webjobs hozzáadása** párbeszédpanelen megadhatja a webjobs nevét és a futtatási mód beállításait a webjobs. 

![Azure-Webjobs hozzáadása párbeszédpanel](./media/webjobs-dotnet-deploy-vs/aaw2.png)

A párbeszédpanel egyes mezői a Azure Portal **Webjobs hozzáadása** párbeszédpanel mezőinek felelnek meg. További információ: [háttérben futó feladatok futtatása webjobs-ben Azure app Service](webjobs-create.md).

Webjobs-telepítési információk:

* További információ a parancssori telepítésről: a [Azure WebJobs parancssori vagy folyamatos kézbesítésének engedélyezése](https://azure.microsoft.com/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/).

* Ha Webjobs telepít, majd eldönti, hogy szeretné-e módosítani a Webjobs típusát és az újbóli üzembe helyezést, törölje a fájlt a *webjobs-publish-settings.js* . Ennek hatására a Visual Studio újra megjeleníti a közzétételi beállításokat, így módosíthatja a Webjobs típusát.

* Ha központilag telepít egy Webjobs, és később a Run (Futtatás) módot folyamatosan, nem folyamatosra módosítja, vagy fordítva, a Visual Studio új Webjobs hoz létre az Azure-ban az újbóli üzembe helyezéskor. Ha módosítja más ütemezési beállításokat, de a futtatási mód nem egyezik meg az ütemezett és az igény szerinti váltással, a Visual Studio új helyett a meglévő feladatot frissíti.

## <a name="webjob-types"></a>Webjobs-típusok

A Webjobs típusa lehet *trigger* vagy *folyamatos*:

- Aktiválva (alapértelmezett): egy aktivált Webjobs egy kötési esemény, egy [Ütemezés](#scheduling-a-triggered-webjob)alapján, vagy manuálisan (igény szerint) aktiválódik. Egyetlen példányon fut, amelyen a webalkalmazás fut.

- Folyamatos: a [folyamatos](#continuous-execution) webjobs azonnal elindul a webjobs létrehozásakor. Alapértelmezés szerint az összes webalkalmazásra méretezett példányon fut, de úgy is konfigurálható, hogy a *Settings. job* fájlon keresztül egyetlen példányként fusson.

[!INCLUDE [webjobs-alwayson-note](../../includes/webjobs-always-on-note.md)]

### <a name="scheduling-a-triggered-webjob"></a>Aktivált WebJobs-feladat ütemezése

Amikor közzétesz egy Console-alkalmazást az Azure-ban, a Visual Studio beállítja az alapértelmezett Webjobs típusát, és egy új *Settings. job* fájlt **ad hozzá a** projekthez. Az aktivált Webjobs-típusok esetén ezt a fájlt használhatja a Webjobs végrehajtási ütemtervének megadásához.

A *Settings. job* fájl használatával állítsa be a webjobs végrehajtási ütemtervét. A következő példa óránként, 9 órától 5 óráig fut le:

```json
{
    "schedule": "0 0 9-17 * * *"
}
```

Ez a fájl a webjobs-mappa gyökerében található a Webjobs parancsfájllal, például `wwwroot\app_data\jobs\triggered\{job name}` vagy `wwwroot\app_data\jobs\continuous\{job name}` . Ha a Visual studióból telepít egy Webjobs, a *beállítások. job* file tulajdonságok a Visual Studióban **másolással, ha újabb**.

Ha [a Azure Portal webjobs hoz létre](webjobs-create.md), a *Settings. job* fájl jön létre.

#### <a name="cron-expressions"></a>CRON-kifejezések

A webjobs ugyanazokat a CRON-kifejezéseket használja az ütemezéshez, mint az időzítő trigger Azure Functionsban. A CRON-támogatással kapcsolatos további információkért lásd: [időzítő trigger a Azure Functionshoz](../azure-functions/functions-bindings-timer.md#ncrontab-expressions).

[!INCLUDE [webjobs-cron-timezone-note](../../includes/webjobs-cron-timezone-note.md)]

#### <a name="settingsjob-reference"></a>Settings. job-hivatkozás

A webjobs a következő beállításokat támogatja:

| **Beállítás** | **Típus**  | **Leírás** |
| ----------- | --------- | --------------- |
| `is_in_place` | Mind | Lehetővé teszi a Webjobs futtatását anélkül, hogy először egy ideiglenes mappába kellene másolni. További információ: [webjobs Working Directory](https://github.com/projectkudu/kudu/wiki/WebJobs#webjob-working-directory). |
| `is_singleton` | Folyamatos | A Webjobs csak egy példányon futtatja, ha a méretezés ki van kapcsolva. További információ: [folyamatos munka beállítása](https://github.com/projectkudu/kudu/wiki/WebJobs-API#set-a-continuous-job-as-singleton)egyszeriként. |
| `schedule` | Kiváltott | Futtassa a Webjobs egy CRON-alapú ütemterven. További információ: NCRONTAB- [kifejezések](../azure-functions/functions-bindings-timer.md#ncrontab-expressions). |
| `stopping_wait_time`| Mind | Lehetővé teszi a leállítási viselkedés vezérlését. További információ: [kecses leállítás](https://github.com/projectkudu/kudu/wiki/WebJobs#graceful-shutdown). |

### <a name="continuous-execution"></a>Folyamatos végrehajtás

Ha **engedélyezve van az Azure** -ban, a Visual Studióval is megváltoztathatja a webjobs, hogy folyamatosan fusson:

1. Ha még nem tette meg, [tegye közzé a projektet az Azure](#deploy-to-azure-app-service)-ban.

1. A **Megoldáskezelőben** kattintson a jobb gombbal a projektre, és válassza a **Publish** (Közzététel) lehetőséget.

1. A **Közzététel** lapon válassza a **Szerkesztés** lehetőséget. 

1. A **Profilbeállítások** párbeszédpanelen válassza a **folyamatos** **webjobs-típust**, majd kattintson a **Mentés** gombra.

    ![Webjobs közzétételi Beállítások párbeszédpanelje](./media/webjobs-dotnet-deploy-vs/publish-settings.png)

1. A **Közzététel** **lapon válassza a közzététel lehetőséget** a webjobs újbóli közzétételéhez a frissített beállításokkal.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [További információ a webjobs SDK-ról](webjobs-sdk-how-to.md)