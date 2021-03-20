---
title: Alkalmazások kiszámítható üzembe helyezése ARM-sel
description: Megtudhatja, hogyan helyezhet üzembe több Azure App Service alkalmazást egyetlen egységként és kiszámítható módon az Azure Resource Management-sablonok és a PowerShell-parancsfájlok használatával.
ms.assetid: bb51e565-e462-4c60-929a-2ff90121f41d
ms.topic: article
ms.date: 01/06/2016
ms.custom: seodec18
ms.openlocfilehash: 8742b590af89954cb8480e5282827bcd5228673b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "101095833"
---
# <a name="provision-and-deploy-microservices-predictably-in-azure"></a>Az Azure-ban előre kiépített és üzembe helyezett szolgáltatások üzembe helyezése
Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre és helyezhet üzembe olyan alkalmazásokat, amelyekben a [Azure app Service](https://azure.microsoft.com/services/app-service/) egy egységként, a [JSON-erőforráscsoportok](https://en.wikipedia.org/wiki/Microservices) és a PowerShell-parancsfájlok használatával kiszámítható módon használható. 

A nagy teljesítményű, nagymértékben leválasztott szolgáltatásokból álló alkalmazások kiépítése és üzembe helyezése, valamint az ismételhetőség és a kiszámíthatóság kulcsfontosságú a siker szempontjából. A [Azure app Service](https://azure.microsoft.com/services/app-service/) lehetővé teszi, hogy webalkalmazásokat, mobil-és API-alkalmazásokat is tartalmazó-szolgáltatásokat hozzon létre. A [Azure Resource Manager](../azure-resource-manager/management/overview.md) lehetővé teszi, hogy az összes szolgáltatást egységként kezelje, valamint az erőforrás-függőségeket, például az adatbázis-és a verziókövetés beállításait. Mostantól a JSON-sablonokkal és az egyszerű PowerShell-parancsfájlokkal is üzembe helyezhet egy ilyen alkalmazást. 

## <a name="what-you-will-do"></a>Mit fog tenni?
Az oktatóanyagban egy olyan alkalmazást fog telepíteni, amely a következőket tartalmazza:

* Két App Service alkalmazás (azaz két szolgáltatás)
* Háttérrendszer SQL Database
* Alkalmazásbeállítások, a kapcsolatok karakterláncai és a verziókövetés
* Application bepillantások, riasztások, automatikus skálázási beállítások

## <a name="tools-you-will-use"></a>A használni kívánt eszközök
Ebben az oktatóanyagban az alábbi eszközöket fogja használni. Mivel az eszközök nem teljes körű vitát végeznek, a végpontok közötti forgatókönyvre fogok ragaszkodni, és csak egy rövid bevezetőt adunk hozzá, ahol további információkat talál. 

### <a name="azure-resource-manager-templates-json"></a>Azure Resource Manager sablonok (JSON)
Minden alkalommal, amikor létrehoz egy alkalmazást a Azure App Serviceban, például Azure Resource Manager JSON-sablonnal hozza létre a teljes erőforráscsoportot az összetevő-erőforrásokkal. Az [Azure Marketplace](../marketplace/index.yml) -en található összetett sablon magában foglalhatja az adatbázist, a Storage-fiókokat, a app Service tervet, az alkalmazást, a riasztási szabályokat, az Alkalmazásbeállítások, az automatikusan méretezhető beállításokat és egyebeket, és az összes ilyen sablon elérhető a PowerShell használatával. A Azure Resource Manager-sablonokkal kapcsolatos további információkért lásd: [Azure Resource Manager sablonok készítése](../azure-resource-manager/templates/template-syntax.md)

### <a name="azure-sdk-26-for-visual-studio"></a>A Visual studióhoz készült Azure SDK 2,6
A legújabb SDK a JSON-szerkesztőben a Resource Manager-sablon támogatásának újdonságait tartalmazza. Ezzel gyorsan létrehozhat egy erőforráscsoport-sablont a semmiből, vagy megnyithat egy meglévő JSON-sablont (például egy letöltött katalógus-sablont) a módosításhoz, feltöltheti a paramétereket tartalmazó fájlt, és akár közvetlenül is üzembe helyezheti az erőforráscsoportot egy Azure-erőforráscsoport megoldásból.

További információ: [Azure SDK 2,6 for Visual Studio](https://azure.microsoft.com/blog/2015/04/29/announcing-the-azure-sdk-2-6-for-net/).

### <a name="azure-powershell-080-or-later"></a>Azure PowerShell 0.8.0 vagy újabb
A 0.8.0 verziótól kezdődően a Azure PowerShell telepítés az Azure-modul mellett az Azure Resource Manager modult is tartalmazza. Ez az új modul lehetővé teszi az erőforráscsoportok központi telepítésének parancsfájlját.

További információ: a [Azure PowerShell használata a Azure Resource Manager](../azure-resource-manager/management/manage-resources-powershell.md)

### <a name="azure-resource-explorer"></a>Azure Resource Explorer
Ez az [előnézeti eszköz](https://resources.azure.com) lehetővé teszi az előfizetésben lévő erőforráscsoportok és az egyes erőforrások JSON-definícióinak megismerését. Az eszközben szerkesztheti az erőforrások JSON-definícióit, törölheti az erőforrások teljes hierarchiáját, és új erőforrásokat hozhat létre.  Az eszközön könnyen elérhető információk nagyon hasznosak a sablon készítéséhez, mert megmutatja, hogy milyen tulajdonságokat kell beállítania egy adott típusú erőforráshoz, a helyes értékekhez stb. Az erőforráscsoportot az [Azure Portalon](https://portal.azure.com/)is létrehozhatja, majd megvizsgálhatja a JSON-definíciókat az Explorer eszközben, hogy segítsen az templatize.

### <a name="deploy-to-azure-button"></a>Üzembe helyezés az Azure-ban gomb
Ha a GitHubot használja a verziókövetés számára, a README-ban üzembe helyezheti az Azure-ban [gombot](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-to-azure-button) . Az MD, amely lehetővé teszi egy kulcsrakész üzembe helyezési felület használatát az Azure-ban. Ezt bármely egyszerű alkalmazás esetében kiterjesztheti úgy, hogy lehetővé tegye egy teljes erőforráscsoport üzembe helyezését azáltal, hogy egy azuredeploy.jst helyez el a tárház gyökerében lévő fájlon. Ezt a JSON-fájlt, amely tartalmazza az erőforráscsoport-sablont, az üzembe helyezés az Azure-ban gombra kattintva hozza létre az erőforráscsoportot. Példaként tekintse meg a [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) mintát, amelyet ebben az oktatóanyagban fog használni.

## <a name="get-the-sample-resource-group-template"></a>A minta erőforráscsoport sablonjának beolvasása
Most nézzük meg a jogot.

1. Navigáljon a [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) app Service mintára.
2. A readme.md-ben kattintson az **üzembe helyezés az Azure**-ban lehetőségre.
3. Az [üzembe helyezés az Azure-](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fazure-appservice-samples%2FToDoApp%2Fmaster%2Fazuredeploy.json) ban helyre kerül, és megkérdezi a telepítési paramétereket. Figyelje meg, hogy a mezők többsége az adattár nevével és néhány véletlenszerű sztringtel van feltöltve. Ha szeretné, az összes mezőt megváltoztathatja, de csak az SQL Server rendszergazdai bejelentkezést és jelszót kell megadnia, majd kattintson a **tovább** gombra.
   
   ![Megjeleníti a bemeneti telepítési paramétereket az üzembe helyezés az Azure-ban oldalon.](./media/app-service-deploy-complex-application-predictably/gettemplate-1-deploybuttonui.png)
4. Ezután kattintson a **telepítés** gombra a telepítési folyamat elindításához. Ha a folyamat befejeződött, kattintson az http://todoapp *XXXX*. azurewebsites.net hivatkozásra az üzembe helyezett alkalmazás tallózásához. 
   
   ![Megjeleníti az alkalmazás telepítési folyamatát.](./media/app-service-deploy-complex-application-predictably/gettemplate-2-deployprogress.png)
   
   A felhasználói felület kis mértékben lassú, amikor először megkeresi az alkalmazást, mert az alkalmazások éppen kezdenek, de meggyőzheti magát arról, hogy ez egy teljesen működőképes alkalmazás.
5. A telepítés lapon kattintson a **kezelés** hivatkozásra az új alkalmazás Azure Portalon való megjelenítéséhez.
6. Az **alapvető** erőforrások legördülő menüben kattintson az erőforráscsoport hivatkozásra. Vegye figyelembe azt is, hogy az alkalmazás már csatlakoztatva van a GitHub-adattárhoz a **külső projekt** területen. 
   
   ![Megjeleníti az erőforrás-csoport hivatkozását az Essentials legördülő szakaszban.](./media/app-service-deploy-complex-application-predictably/gettemplate-3-portalresourcegroup.png)
7. Az erőforráscsoport panelen vegye figyelembe, hogy már van két alkalmazás és egy SQL Database az erőforráscsoporthoz.
   
   ![Megjeleníti az erőforráscsoport elérhető erőforrásait.](./media/app-service-deploy-complex-application-predictably/gettemplate-4-portalresourcegroupclicked.png)

Minden, amit néhány rövid percen belül meglátott, egy teljes körűen üzembe helyezett kétcsoportos alkalmazás, amely az összes összetevővel, függőséggel, beállítással, adatbázissal és folyamatos közzétételsel rendelkezik, és a Azure Resource Managerban automatikusan létrejön. Mindezt két dolog hajtja végre:

* Az üzembe helyezés az Azure-ban gomb
* azuredeploy.jsa tárház gyökerében

Ugyanezt az alkalmazásokat több, száz vagy több ezer alkalommal is üzembe helyezheti, és minden alkalommal pontosan ugyanazt a konfigurációt használhatja. Ennek a megközelítésnek az ismételhetősége és kiszámíthatósága lehetővé teszi, hogy könnyedén és magabiztosan helyezzen üzembe nagy méretű alkalmazásokat.

## <a name="examine-or-edit-azuredeployjson"></a>AZUREDEPLOY.JSvizsgálata (vagy szerkesztése)
Most nézzük meg, hogyan állították be a GitHub-tárházat. A JSON-szerkesztőt az Azure .NET SDK-ban fogja használni, így ha még nem telepítette az [Azure .net sdk 2,6](https://azure.microsoft.com/downloads/)-et, tegye meg most.

1. A [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) adattár klónozása a kedvenc git-eszköz használatával. Az alábbi képernyőképen ezt a Visual Studio 2013 Team Explorerban végezem el.
   
   ![Bemutatja, hogyan használható egy git-eszköz a ToDoApp-tárház klónozásához.](./media/app-service-deploy-complex-application-predictably/examinejson-1-vsclone.png)
2. A tárház gyökerében nyissa meg a azuredeploy.jsa Visual Studióban. Ha nem látja a JSON-vázlat ablaktáblát, telepítenie kell az Azure .NET SDK-t.
   
   ![Megjeleníti a Visual Studio JSON-vázlat paneljét.](./media/app-service-deploy-complex-application-predictably/examinejson-2-vsjsoneditor.png)

Nem fogom leírni a JSON formátum minden részletét, de a [További erőforrások](#resources) szakasz hivatkozásokat tartalmaz az erőforráscsoport-sablon nyelvének megismeréséhez. Itt megmutatom az érdekes funkciókat, amelyek segítségével megkezdheti a saját egyéni sablon készítését az alkalmazások telepítéséhez.

### <a name="parameters"></a>Paraméterek
Tekintse meg a parameters (paraméterek) szakaszt, amelyből megtudhatja, hogy a paraméterek többsége az, amit az **Azure-beli üzembe helyezés** gomb kér a bevitelhez. A **telepítés az Azure** -ba gomb mögötti hely feltölti a bemeneti felhasználói felületet a azuredeploy.json paraméterben meghatározott paraméterek használatával. Ezeket a paramétereket az erőforrás-definíciók, például az erőforrások neve, a tulajdonságértékek stb. használják.

### <a name="resources"></a>Erőforrások
A Resources (erőforrások) csomópontban láthatja, hogy 4 legfelső szintű erőforrás van definiálva, beleértve egy SQL Server példányt, egy App Service tervet és két alkalmazást. 

#### <a name="app-service-plan"></a>App Service-csomag
Kezdjük egy egyszerű, legfelső szintű erőforrással a JSON-ben. A JSON-vázlatban kattintson a **[hostingPlanName]** nevű app Service tervre a megfelelő JSON-kód kiemeléséhez. 

![Megjeleníti a JSON-kód [hostingPlanName] szakaszát.](./media/app-service-deploy-complex-application-predictably/examinejson-3-appserviceplan.png)

Vegye figyelembe, hogy az `type` elem egy app Service terv sztringjét határozza meg (ez egy hosszú, hosszú ideje futó kiszolgálófarm volt), és az egyéb elemek és tulajdonságok a JSON-fájlban megadott paraméterekkel vannak kitöltve, és ez az erőforrás nem rendelkezik beágyazott erőforrásokkal.

> [!NOTE]
> Vegye figyelembe azt is, hogy az `apiVersion` Azure azt jelzi, hogy a REST API mely verziója használja a JSON-erőforrás definícióját a alkalmazásban, és befolyásolhatja, hogy az erőforrás hogyan legyen formázva a rendszeren belül `{}` . 
> 
> 

#### <a name="sql-server"></a>SQL Server
Ezután kattintson az **SQLServer** nevű SQL Server-erőforrásra a JSON-vázlatban.

![Megjeleníti a SQLServer nevű SQL Server erőforrást a JSON-vázlatban.](./media/app-service-deploy-complex-application-predictably/examinejson-4-sqlserver.png)

Vegye figyelembe a következőket a Kiemelt JSON-kóddal kapcsolatban:

* A paraméterek használata biztosítja, hogy a létrehozott erőforrások neve és konfigurálása olyan módon történjen, amely konzisztens lesz egymással.
* A SQLServer-erőforrás két beágyazott erőforrással rendelkezik, amelyek mindegyike más értékkel rendelkezik `type` .
* A beágyazott erőforrások `“resources”: […]` , ahol az adatbázis és a tűzfalszabályok definiálva vannak, egy olyan elemmel rendelkeznek, `dependsOn` amely megadja a gyökérszintű SQLServer erőforrás-azonosítóját. Ez azt mondja Azure Resource Manager, "az erőforrás létrehozása előtt, hogy a másik erőforrásnak már léteznie kell; Ha más erőforrás van definiálva a sablonban, akkor hozzon létre egyet.
  
  > [!NOTE]
  > A függvény használatáról a `resourceId()` [Azure Resource Manager sablon functions](../azure-resource-manager/templates/template-functions-resource.md#resourceid)című témakörben olvashat bővebben.
  > 
  > 
* Az elem hatása az, `dependsOn` hogy Azure Resource Manager tudja, hogy mely erőforrások hozhatók létre párhuzamosan, és hogy mely erőforrásokat kell egymás után létrehozni. 

#### <a name="app-service-app"></a>App Service-alkalmazás
Most térjünk át a tényleges alkalmazásokra, amelyek bonyolultabbak. A JSON-vázlatban kattintson a [változók (' apiSiteName ')]] alkalmazásra a JSON-kód kiemeléséhez. Megfigyelheti, hogy a dolgok sokkal érdekesebbek. Erre a célra az alábbi funkciókkal fogunk beszélni:

##### <a name="root-resource"></a>Gyökérszintű erőforrás
Az alkalmazás két különböző erőforrástól függ. Ez azt jelenti, hogy a Azure Resource Manager csak akkor hozza létre az alkalmazást, ha a App Service tervet és a SQL Server példányt is létrehozták.

![Megjeleníti az alkalmazás függőségeit a App Service és a SQL Server példányon.](./media/app-service-deploy-complex-application-predictably/examinejson-5-webapproot.png)

##### <a name="app-settings"></a>Alkalmazásbeállítások
Az Alkalmazásbeállítások beágyazott erőforrásként is definiálva lesznek.

![Megjeleníti a JSON-kódban beágyazott erőforrásként definiált Alkalmazásbeállítások listáját.](./media/app-service-deploy-complex-application-predictably/examinejson-6-webappsettings.png)

A `properties` elemben `config/appsettings` két alkalmazás-beállítás szerepel a formátumban `"<name>" : "<value>"` .

* `PROJECT` a egy [KUDU-beállítás](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) , amely azt jelzi, hogy az Azure üzembe helyezése milyen projektet használ egy többprojektes Visual Studio-megoldásban. Megmutatom, hogy a verziókövetés hogyan van konfigurálva, de mivel a ToDoApp-kód egy többprojektes Visual Studio-megoldásban van, erre a beállításra van szükség.
* `clientUrl` egyszerűen egy alkalmazás-beállítás, amelyet az alkalmazás kód használ.

##### <a name="connection-strings"></a>Kapcsolati sztringek
A kapcsolatok karakterlánca beágyazott erőforrásként is definiálva van.

![Azt mutatja, hogy a rendszer hogyan definiálja a kapcsolatok karakterláncait beágyazott erőforrásként a JSON-kódban.](./media/app-service-deploy-complex-application-predictably/examinejson-7-webappconnstr.png)

A `properties` elemben az `config/connectionstrings` egyes kapcsolódási karakterláncok neve: érték párokként is definiálva vannak, a megadott formátummal `"<name>" : {"value": "…", "type": "…"}` . Az elem esetében a lehetséges értékek a következők:,, `type` `MySql` `SQLServer` `SQLAzure` és `Custom` .

> [!TIP]
> A kapcsolatok karakterlánc-típusainak végleges listáját a következő parancs futtatásával Azure PowerShell: \[ Enum]:: GetNames ("Microsoft. WindowsAzure. commands. Utilities. websites. Services. Webentitások. DatabaseType")
> 
> 

##### <a name="source-control"></a>Verziókövetés
A verziókövetés beállításai beágyazott erőforrásként is definiálva lesznek. Azure Resource Manager ezt az erőforrást használja a folyamatos közzététel konfigurálásához (lásd a figyelmeztetést `IsManualIntegration` később), és az alkalmazás kódjának automatikus üzembe helyezését a JSON-fájl feldolgozása során.

![Azt mutatja, hogy a verziókövetés beállításai hogyan vannak definiálva beágyazott erőforrásként a JSON-kódban.](./media/app-service-deploy-complex-application-predictably/examinejson-8-webappsourcecontrol.png)

`RepoUrl` és `branch` elég intuitívnak kell lennie, és a git-tárházra, valamint annak a fióknak a nevére kell mutatnia, amelyet közzé szeretne tenni. Ezeket a paramétereket a bemeneti paraméterek határozzák meg. 

Vegye figyelembe, hogy a (z `dependsOn` ) és az alkalmazás-erőforrás mellett a (z `sourcecontrols/web` ) és a is függ `config/appsettings` `config/connectionstrings` . Ennek az az oka `sourcecontrols/web` , hogy ha egyszer konfigurálva van, az Azure üzembe helyezési folyamata automatikusan megkísérli az alkalmazás kódjának telepítését, összeállítását és elindítását. Ezért a függőség beírásával gondoskodhat arról, hogy az alkalmazás hozzáférjen a szükséges alkalmazás-beállításokhoz és a kapcsolati karakterláncokhoz az alkalmazás kódjának futtatása előtt. 

> [!NOTE]
> Figyelje meg azt is, hogy a értékre `IsManualIntegration` van állítva `true` . Ez a tulajdonság azért szükséges ebben az oktatóanyagban, mert valójában nem rendelkezik a GitHub-adattárral, és így valójában nem tud engedélyt adni az Azure-nak a [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) folyamatos közzétételének konfigurálására (azaz az automatikus adattár frissítéseinek az Azure-ba történő leküldésére). A megadott tárház alapértelmezett értékét csak akkor használhatja, `false` Ha korábban a tulajdonos GitHub hitelesítő adatait konfigurálta a [Azure Portal](https://portal.azure.com/) . Más szóval, ha már beállította a verziókövetés használatát a GitHubon vagy a BitBucket az [Azure Portalon](https://portal.azure.com/) bármely alkalmazáshoz, a felhasználói hitelesítő adataival, az Azure emlékezni fog a hitelesítő adatokra, és minden alkalommal felhasználja őket, amikor a githubról vagy a BitBucket-ről telepít bármilyen alkalmazást a jövőben. Ha azonban még nem tette meg, akkor a JSON-sablon üzembe helyezése sikertelen lesz, ha Azure Resource Manager megpróbálja konfigurálni az alkalmazás verziókövetés beállításait, mert az nem tud bejelentkezni a GitHubba vagy a BitBucket az adattár tulajdonosának hitelesítő adataival.
> 
> 

## <a name="compare-the-json-template-with-deployed-resource-group"></a>A JSON-sablon összehasonlítása telepített erőforráscsoporthoz
Itt áttekintheti az alkalmazás összes paneljét az [Azure Portalon](https://portal.azure.com/), de egy másik eszköz is hasznos lehet, ha nem. Nyissa meg a [Azure erőforrás-kezelő](https://resources.azure.com) Preview eszközt, amely az előfizetésekben található összes erőforráscsoport JSON-ábrázolását adja meg, mivel azok ténylegesen léteznek az Azure-háttérben. Azt is megtudhatja, hogyan felel meg az erőforráscsoport JSON-hierarchiája az Azure-ban a létrehozásához használt sablonfájl hierarchiájának.

Ha például a [Azure erőforrás-kezelő](https://resources.azure.com) eszközre lép, és kibontja a csomópontokat az Explorerben, megtekintheti az erőforráscsoportot és a saját erőforrástípusok alapján gyűjtött legfelső szintű erőforrásokat.

![Tekintse meg az erőforráscsoportot és a gyökérszintű erőforrásokat a kibontott Azure-erőforrások Explorer eszközében.](./media/app-service-deploy-complex-application-predictably/ARM-1-treeview.png)

Ha egy alkalmazás részletezését szeretné megtekinteni, az alábbi képernyőképhez hasonló módon láthatja el az alkalmazás konfigurációs adatait:

![Részletezéssel megtekintheti a konfigurációs adatokat az alkalmazásban.](./media/app-service-deploy-complex-application-predictably/ARM-2-jsonview.png)

A beágyazott erőforrásokhoz hasonlóan a JSON-sablonfájl egy hierarchiájának is hasonlónak kell lennie, és látnia kell az alkalmazás beállításait, a kapcsolódási karakterláncokat stb., amelyek megfelelően megjelennek a JSON-panelen. Az itt megadott beállítások hiánya jelezheti a JSON-fájllal kapcsolatos problémát, és segíthet a JSON-sablonfájl hibakeresésében.

## <a name="deploy-the-resource-group-template-yourself"></a>Az erőforráscsoport-sablon üzembe helyezése
Az **üzembe helyezés az Azure** -ban gomb nagyszerű, de lehetővé teszi, hogy csak akkor telepítse az erőforráscsoport-sablont azuredeploy.json, ha már leküldte azuredeploy.jsa githubba. Az Azure .NET SDK azt is biztosítja, hogy a JSON-sablonfájlok közvetlenül a helyi gépről is üzembe helyezhetők. Ehhez kövesse az alábbi lépéseket:

1. A Visual Studióban kattintson a **fájl**  >  **új**  >  **projekt** elemre.
2. Kattintson a **Visual C#**  >  **Cloud**  >  **Azure-erőforráscsoport** elemre, majd **az OK** gombra.
   
   ![Hozzon létre egy új projektet Azure-erőforráscsoportként az Azure .NET SDK-ban.](./media/app-service-deploy-complex-application-predictably/deploy-1-vsproject.png)
3. Az **Azure-sablon kiválasztása** területen válassza az **üres sablon** lehetőséget, majd kattintson **az OK gombra**.
4. Húzzon azuredeploy.jsaz új projekt **sablon** mappájába.
   
   ![Megjeleníti a azuredeploy.jsfájlból való húzásának eredményét a projekt sablon mappájába.](./media/app-service-deploy-complex-application-predictably/deploy-2-copyjson.png)
5. A Megoldáskezelőban nyissa meg a másolt azuredeploy.js.
6. A bemutató kedvéért vegyünk fel néhány szabványos alkalmazás-betekintési erőforrást a JSON-fájlba, ehhez kattintson az **erőforrás hozzáadása** lehetőségre. Ha csak a JSON-fájl üzembe helyezését érdekli, ugorjon az üzembe helyezési lépések pontra.
   
   ![Megjeleníti az erőforrás hozzáadása gombot, amellyel szabványos alkalmazás-betekintési erőforrásokat adhat a JSON-fájlhoz.](./media/app-service-deploy-complex-application-predictably/deploy-3-newresource.png)
7. Válassza **a Web Apps Application Insights** lehetőséget, majd győződjön meg arról, hogy egy meglévő app Service csomag és alkalmazás van kiválasztva, majd kattintson a **Hozzáadás** gombra.
   
   ![A Web Apps, a név, a App Service a terv és a webalkalmazás Application Insights kijelölését jeleníti meg.](./media/app-service-deploy-complex-application-predictably/deploy-4-newappinsight.png)
   
   Mostantól számos új erőforrást láthat, amelyek az erőforrástól és a működéstől függően függőségekkel rendelkeznek a App Service-csomagon vagy az alkalmazáson. Ezeket az erőforrásokat a meglévő definíciójuk nem engedélyezi, és ezt fogja módosítani.
   
   ![Tekintse meg a App Service terv vagy alkalmazás függőségeivel rendelkező új erőforrásokat.](./media/app-service-deploy-complex-application-predictably/deploy-5-appinsightresources.png)
8. A JSON-vázlatban kattintson az **appInsights** elemre a JSON-kód kiemeléséhez. Ez a App Service terv méretezési beállítása.
9. A Kiemelt JSON-kódban keresse meg a `location` és a `enabled` tulajdonságokat, majd állítsa be őket az alább látható módon.
   
   ![Megjeleníti a hely és az engedélyezett tulajdonságokat a appInsights autoscale JSON-kódban, valamint azokat az értékeket, amelyeket be kell állítani.](./media/app-service-deploy-complex-application-predictably/deploy-6-autoscalesettings.png)
10. A JSON-vázlatban kattintson a **CPUHigh appInsights** elemre a JSON-kód kiemeléséhez. Ez egy riasztás.
11. Keresse meg a `location` és a `isEnabled` tulajdonságokat, és állítsa be őket az alább látható módon. Ugyanezt hajtsa végre a többi három riasztásnál (lila izzók).
    
    ![Megjeleníti a hely és a isEnabled tulajdonságait a CPUHigh appInsights JSON-kódjában, valamint azokat az értékeket, amelyeket be kell állítani.](./media/app-service-deploy-complex-application-predictably/deploy-7-alerts.png)
12. Most már készen áll a telepítésre. Kattintson a jobb gombbal a projektre , és válassza az  >  **új központi telepítés** telepítése lehetőséget.
    
    ![Bemutatja az új projekt üzembe helyezésének módját.](./media/app-service-deploy-complex-application-predictably/deploy-8-newdeployment.png)
13. Ha még nem tette meg, jelentkezzen be az Azure-fiókjába.
14. Válasszon ki egy meglévő erőforráscsoportot az előfizetésben, vagy hozzon létre egy újat, válassza **aazuredeploy.js** be lehetőséget, majd kattintson a **Paraméterek szerkesztése** elemre.
    
    ![Bemutatja, hogyan szerkesztheti a paramétereket a azuredeploy.jsfájljában.](./media/app-service-deploy-complex-application-predictably/deploy-9-deployconfig.png)
    
    Most már szerkesztheti a sablonban definiált összes paramétert egy szép táblázatban. Az alapértelmezett értékeket meghatározó paraméterek már rendelkeznek alapértelmezett értékekkel, és az engedélyezett értékek listáját meghatározó paraméterek legördülő listaként jelennek meg.
    
    ![Megjeleníti a legördülő listaként engedélyezett értékek listáját meghatározó paramétereket.](./media/app-service-deploy-complex-application-predictably/deploy-10-parametereditor.png)
15. Töltse ki az összes üres paramétert, és használja a GitHub-tárházat a [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp.git) a **repouring** szolgáltatásban. Ezután kattintson a **Mentés** gombra.
    
    ![Megjeleníti a fájl azuredeploy.jsjának újonnan kitöltött paramétereit.](./media/app-service-deploy-complex-application-predictably/deploy-11-parametereditorfilled.png)
    
    > [!NOTE]
    > Az automatikus skálázás a **standard** szintű vagy magasabb szintű szolgáltatás, és az **alapszintű** vagy magasabb szintű riasztások olyan funkciók, amelyeken az **SKU** paramétert **standard** vagy **Premium** értékre kell beállítani, hogy az összes új alkalmazás-megállapítási erőforrás felálljon.
    > 
    > 
16. Kattintson az **Üzembe helyezés** gombra. Ha a **jelszavak mentése** lehetőséget választotta, a jelszót a rendszer **egyszerű szövegként** menti a paraméter fájlba. Ellenkező esetben a rendszer megkéri, hogy adja meg az adatbázis jelszavát a telepítési folyamat során.

Készen is van. Most az [Azure Portalra](https://portal.azure.com/) és a [Azure erőforrás-kezelő](https://resources.azure.com) eszközre kell kattintania, hogy megtekintse a JSON-ban üzembe helyezett alkalmazáshoz hozzáadott új riasztásokat és az autoskálázási beállításokat.

Az ebben a szakaszban ismertetett lépések elsősorban a következőket hajtják végre:

1. Előkészített a sablonfájl
2. Létrehozott egy paramétert tartalmazó fájlt a sablonfájl használatával.
3. A sablonfájl üzembe helyezése a paraméter fájllal

Az utolsó lépést egyszerűen egy PowerShell-parancsmag hajtja végre. Ha szeretné megtekinteni, hogy a Visual Studio Mikor telepítette az alkalmazást, nyissa meg Scripts\Deploy-AzureResourceGroup.ps1. Itt rengeteg kód van, de most Kiemelem az összes releváns kódot, amelyre telepíteni kell a sablonfájlt a file paraméterrel.

![Megjeleníti a parancsfájlban szereplő megfelelő kódot, amelyet a sablonfájl és a paraméter fájljának telepítéséhez kell használni.](./media/app-service-deploy-complex-application-predictably/deploy-12-powershellsnippet.png)

Az utolsó parancsmag, `New-AzureResourceGroup` amely az a művelet, amely ténylegesen végrehajtja a műveletet. Mindez azt mutatja meg, hogy az eszközök segítségével viszonylag egyszerű üzembe helyezni a felhőalapú alkalmazást. Minden alkalommal, amikor ugyanazon a sablonon futtatja a parancsmagot ugyanazzal a fájllal, ugyanazt az eredményt fogja kapni.

## <a name="summary"></a>Összefoglalás
A DevOps-ben az ismételhetőség és a kiszámíthatóság olyan kulcs, amely egy nagy léptékű alkalmazás sikeres üzembe helyezését eredményezi. Ebben az oktatóanyagban egy kétszolgáltatásos alkalmazást helyezett üzembe az Azure-ban egyetlen erőforráscsoportként a Azure Resource Manager sablon használatával. Remélhetőleg megkapta a szükséges tudást ahhoz, hogy megkezdje az alkalmazás átalakítását az Azure-ban egy sablonba, és kiszámítható módon üzembe helyezheti és telepítheti azt. 

<a name="resources"></a>

## <a name="more-resources"></a>További erőforrások
* [Azure Resource Manager sablon nyelve](../azure-resource-manager/templates/template-syntax.md)
* [Azure Resource Manager sablonok készítése](../azure-resource-manager/templates/template-syntax.md)
* [Azure Resource Manager sablon függvények](../azure-resource-manager/templates/template-functions.md)
* [Alkalmazás üzembe helyezése Azure Resource Manager sablonnal](../azure-resource-manager/templates/deploy-powershell.md)
* [Az Azure PowerShell használata az Azure Resource Managerrel](../azure-resource-manager/management/manage-resources-powershell.md)
* [Erőforráscsoport-telepítések hibaelhárítása Az Azure-ban](../azure-resource-manager/templates/common-deployment-errors.md)

## <a name="next-steps"></a>Következő lépések

Az ebben a cikkben üzembe helyezett erőforrástípusok JSON-szintaxisáról és tulajdonságairól további információt a következő témakörben talál:

* [Microsoft. SQL/kiszolgálók](/azure/templates/microsoft.sql/servers)
* [Microsoft. SQL/kiszolgálók/adatbázisok](/azure/templates/microsoft.sql/servers/databases)
* [Microsoft. SQL/kiszolgálók/firewallRules](/azure/templates/microsoft.sql/servers/firewallrules)
* [Microsoft. Web/kiszolgálófarmok](/azure/templates/microsoft.web/serverfarms)
* [Microsoft. Web/Sites](/azure/templates/microsoft.web/sites)
* [Microsoft. Web/Sites/Slots](/azure/templates/microsoft.web/sites/slots)
* [Microsoft. bepillantások/autoscalesettings](/azure/templates/microsoft.insights/autoscalesettings)
