---
title: .NET-alkalmazás létrehozása Service Fabric az Azure-ban
description: Ez az oktatóanyag azt ismerteti, hogyan hozhat létre egy alkalmazást az ASP.NET Core kezelőfelülete és egy megbízható állapotalapú háttérszolgáltatás segítségével, majd hogyan helyezheti üzembe az alkalmazást egy fürtön.
ms.topic: tutorial
ms.date: 07/10/2019
ms.custom: mvc, devx-track-js, devx-track-csharp
ms.openlocfilehash: 8fe9f1fcb85e58122290f89819aa721c8f0e632a
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102035326"
---
# <a name="tutorial-create-and-deploy-an-application-with-an-aspnet-core-web-api-front-end-service-and-a-stateful-back-end-service"></a>Oktatóanyag: Alkalmazás létrehozása és üzembe helyezése egy ASP.NET Core Web API kezelőfelületi szolgáltatás és egy állapotalapú háttérszolgáltatás segítségével

Ez az oktatóanyag egy sorozat első része.  Megtudhatja, hogyan hozhat létre egy Azure Service Fabric-alkalmazást egy ASP.NET Core Web API kezelőfelületi és egy állapotalapú háttérszolgáltatás segítségével az adatok tárolásához. Az útmutató elvégzése után rendelkezni fog egy ASP.NET Core webes kezelőfelületes szavazóalkalmazással, amely egy, a fürtben található állapotalapú háttérszolgáltatásba menti a szavazati adatokat. Az oktatóanyag-sorozathoz Windowsos fejlesztői számítógép szükséges. Ha nem szeretné manuálisan létrehozni a szavazóalkalmazást, akkor [letöltheti a forráskódot](https://github.com/Azure-Samples/service-fabric-dotnet-quickstart/) a kész alkalmazáshoz, és folytathatja a [mintául szolgáló szavazóalkalmazás bemutatásával](#walkthrough_anchor).  Ha szeretné, megtekintheti az oktatóanyag [útmutató videóját](https://channel9.msdn.com/Events/Connect/2017/E100).

![AngularJS + ASP. NET API előtér, Service Fabric-beli állapot-nyilvántartó háttér-szolgáltatáshoz való csatlakozás](./media/service-fabric-tutorial-create-dotnet-app/application-diagram.png)

A sorozat első részében a következőkkel ismerkedhet meg:

> [!div class="checklist"]
> * ASP.NET Core Web API-szolgáltatás létrehozása állapotalapú megbízható szolgáltatásként
> * ASP.NET Core Web-alkalmazásszolgáltatás létrehozása állapot nélküli webszolgáltatásként
> * A fordított proxy használata az állapotalapú szolgáltatással folytatott kommunikációhoz

Ebben az oktatóanyag-sorozatban az alábbiakkal ismerkedhet meg:
> [!div class="checklist"]
> * .NET Service Fabric-alkalmazás létrehozása
> * [Az alkalmazás üzembe helyezése egy távoli fürtön](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * [HTTPS-végpont hozzáadása egy ASP.NET Core előtér-szolgáltatáshoz](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
> * [A CI/CD konfigurálása az Azure Pipelines használatával](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
> * [Figyelés és diagnosztika beállítása az alkalmazáshoz](service-fabric-tutorial-monitoring-aspnet.md)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdése előtt:
* Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Telepítse a Visual Studio 2019](https://www.visualstudio.com/) 15,5-es vagy újabb verzióját az **Azure fejlesztési** és **ASP.net, valamint a webes fejlesztési** számítási feladatokkal.
* [A Service Fabric SDK telepítése](service-fabric-get-started.md)

## <a name="create-an-aspnet-web-api-service-as-a-reliable-service"></a>ASP.NET Web API-szolgáltatás létrehozása megbízható szolgáltatásként

Először hozza létre a szavazóalkalmazás webes kezelőfelületét az ASP.NET Core használatával. Az ASP.NET Core egy egyszerűsített, platformfüggetlen webes fejlesztési keretrendszer, amely segítségével modern webes felhasználói felületeket és webes API-kat hozhat létre. Az ASP.NET Core Service Fabricbe történő integrálásának teljes megértéséhez mindenképpen javasolt [az ASP.NET Core a Service Fabric Reliable Services szolgáltatásban](service-fabric-reliable-services-communication-aspnetcore.md) való használatát ismertető cikk alapos elolvasása. Egyelőre az első lépésekhez elég, ha elvégzi ezt az oktatóanyagot. Az ASP.NET Core-ról az [ASP.NET Core dokumentációjában](/aspnet/core/) találhat további információt.

1. Indítsa el a Visual studiót **rendszergazdaként**.

2. Hozzon létre egy projektet a **fájl** -> **új** -> **projekttel**.

3. Az **Új projekt** párbeszédpanelen válassza a **Felhő > Service Fabric-alkalmazás** elemet.

4. Adjon nevet az alkalmazásnak (**Voting**), majd kattintson az **OK** gombra.

   ![A Visual Studio Új projekt párbeszédpanelje](./media/service-fabric-tutorial-create-dotnet-app/new-project-dialog.png)

5. Az **Új Service Fabric-szolgáltatás** oldalon válassza ki az **Állapot nélküli ASP.NET Core** elemet, adjon nevet a szolgáltatásnak (**VotingWeb**), majd kattintson az **OK** gombra.
   
   ![Az ASP.NET-webszolgáltatás kiválasztása az új szolgáltatás párbeszédpanelen](./media/service-fabric-tutorial-create-dotnet-app/new-project-dialog-2.png) 

6. A következő oldalon az ASP.NET Core projektsablonjai találhatók. A jelen oktatóanyag esetében válassza ki a **Webalkalmazás (Model-View-Controller)** lehetőséget, majd kattintson az **OK** gombra.
   
   ![ASP.NET-projekt típusának kiválasztása](./media/service-fabric-tutorial-create-dotnet-app/vs-new-aspnet-project-dialog.png)

   A Visual Studio létrehoz egy alkalmazás- és egy szolgáltatásprojektet, és megjeleníti őket a Megoldáskezelőben.

   ![A Megoldáskezelő az alkalmazás az ASP.NET Core Web API szolgáltatással történő létrehozása után]( ./media/service-fabric-tutorial-create-dotnet-app/solution-explorer-aspnetcore-service.png)

### <a name="update-the-sitejs-file"></a>A site.js fájl frissítése
Nyissa meg a **wwwroot/js/site.js** fájlt.  Cserélje le annak tartalmát a kezdőlapnézetek által használt következő JavaScripttel, majd mentse a módosításokat.

```javascript
var app = angular.module('VotingApp', ['ui.bootstrap']);
app.run(function () { });

app.controller('VotingAppController', ['$rootScope', '$scope', '$http', '$timeout', function ($rootScope, $scope, $http, $timeout) {

    $scope.refresh = function () {
        $http.get('api/Votes?c=' + new Date().getTime())
            .then(function (data, status) {
                $scope.votes = data;
            }, function (data, status) {
                $scope.votes = undefined;
            });
    };

    $scope.remove = function (item) {
        $http.delete('api/Votes/' + item)
            .then(function (data, status) {
                $scope.refresh();
            })
    };

    $scope.add = function (item) {
        var fd = new FormData();
        fd.append('item', item);
        $http.put('api/Votes/' + item, fd, {
            transformRequest: angular.identity,
            headers: { 'Content-Type': undefined }
        })
            .then(function (data, status) {
                $scope.refresh();
                $scope.item = undefined;
            })
    };
}]);
```

### <a name="update-the-indexcshtml-file"></a>Az Index.cshtml fájl frissítése

Nyissa meg a **Views/Home/Index.cshtml** fájlt, amely a kezdőlapvezérlő egyedi nézete.  Cserélje le annak tartalmát a következőkkel, majd mentse a módosításokat.

```html
@{
    ViewData["Title"] = "Service Fabric Voting Sample";
}

<div ng-controller="VotingAppController" ng-init="refresh()">
    <div class="container-fluid">
        <div class="row">
            <div class="col-xs-8 col-xs-offset-2 text-center">
                <h2>Service Fabric Voting Sample</h2>
            </div>
        </div>

        <div class="row">
            <div class="col-xs-8 col-xs-offset-2">
                <form class="col-xs-12 center-block">
                    <div class="col-xs-6 form-group">
                        <input id="txtAdd" type="text" class="form-control" placeholder="Add voting option" ng-model="item"/>
                    </div>
                    <button id="btnAdd" class="btn btn-default" ng-click="add(item)">
                        <span class="glyphicon glyphicon-plus" aria-hidden="true"></span>
                        Add
                    </button>
                </form>
            </div>
        </div>

        <hr/>

        <div class="row">
            <div class="col-xs-8 col-xs-offset-2">
                <div class="row">
                    <div class="col-xs-4">
                        Click to vote
                    </div>
                </div>
                <div class="row top-buffer" ng-repeat="vote in votes.data">
                    <div class="col-xs-8">
                        <button class="btn btn-success text-left btn-block" ng-click="add(vote.key)">
                            <span class="pull-left">
                                {{vote.Key}}
                            </span>
                            <span class="badge pull-right">
                                {{vote.Value}} Votes
                            </span>
                        </button>
                    </div>
                    <div class="col-xs-4">
                        <button class="btn btn-danger pull-right btn-block" ng-click="remove(vote.key)">
                            <span class="glyphicon glyphicon-remove" aria-hidden="true"></span>
                            Remove
                        </button>
                    </div>
                </div>
            </div>
        </div>
    </div>
</div>
```

### <a name="update-the-_layoutcshtml-file"></a>A _Layout.cshtml fájl frissítése

Nyissa meg a **Views/Shared/_Layout.cshtml** fájlt, amely az ASP.NET-alkalmazás alapértelmezett elrendezését tartalmazza.  Cserélje le annak tartalmát a következőkkel, majd mentse a módosításokat.


```html
<!DOCTYPE html>
<html ng-app="VotingApp" xmlns:ng="https://angularjs.org">
<head>
    <meta charset="utf-8"/>
    <meta name="viewport" content="width=device-width, initial-scale=1.0"/>
    <title>@ViewData["Title"]</title>

    <link href="~/lib/bootstrap/dist/css/bootstrap.css" rel="stylesheet"/>
    <link href="~/css/site.css" rel="stylesheet"/>

</head>
<body>
<div class="container body-content">
    @RenderBody()
</div>

<script src="~/lib/jquery/dist/jquery.js"></script>
<script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/angular.js/1.7.2/angular.min.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/angular-ui-bootstrap/2.5.0/ui-bootstrap-tpls.js"></script>
<script src="~/js/site.js"></script>

@RenderSection("Scripts", required: false)
</body>
</html>
```

### <a name="update-the-votingwebcs-file"></a>A VotingWeb.cs fájl frissítése

Nyissa meg a *VotingWeb.cs* fájlt, amely létrehozza az ASP.NET Core WebHostot az állapotmentes szolgáltatáson belül a WebListener webkiszolgáló használatával.

Adja hozzá az `using System.Net.Http;` direktívát a fájl elejéhez.

Cserélje le a `CreateServiceInstanceListeners()` függvényt a következő kóddal, majd mentse a módosításokat.

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    return new ServiceInstanceListener[]
    {
        new ServiceInstanceListener(
            serviceContext =>
                new KestrelCommunicationListener(
                    serviceContext,
                    "ServiceEndpoint",
                    (url, listener) =>
                    {
                        ServiceEventSource.Current.ServiceMessage(serviceContext, $"Starting Kestrel on {url}");

                        return new WebHostBuilder()
                            .UseKestrel()
                            .ConfigureServices(
                                services => services
                                    .AddSingleton<HttpClient>(new HttpClient())
                                    .AddSingleton<FabricClient>(new FabricClient())
                                    .AddSingleton<StatelessServiceContext>(serviceContext))
                            .UseContentRoot(Directory.GetCurrentDirectory())
                            .UseStartup<Startup>()
                            .UseServiceFabricIntegration(listener, ServiceFabricIntegrationOptions.None)
                            .UseUrls(url)
                            .Build();
                    }))
    };
}
```

Adja meg a következő `GetVotingDataServiceName` metódust is a `CreateServiceInstanceListeners()` alatt, és mentse a módosításokat. A `GetVotingDataServiceName` lekérdezéskor visszaadja a szolgáltatás nevét.

```csharp
internal static Uri GetVotingDataServiceName(ServiceContext context)
{
    return new Uri($"{context.CodePackageActivationContext.ApplicationName}/VotingData");
}
```

### <a name="add-the-votescontrollercs-file"></a>A VotesController.cs fájl hozzáadása

Adjon hozzá egy vezérlőt, amely meghatározza a szavazási műveleteket. Kattintson a jobb gombbal a **Vezérlők** mappára, majd válassza a **Hozzáadás->Új elem->Visual C#->ASP.NET Core->Osztály** lehetőséget. A fájlnak adja a **VotesController.cs** nevet, majd kattintson a **Hozzáadás** gombra.  

Cserélje le a *VotesController.cs* fájl tartalmát a következőkre, majd mentse a módosításokat.  A későbbiekben, a [VotesController.cs fájl frissítése](#updatevotecontroller_anchor) során ez a fájl úgy módosul, hogy a háttérszolgáltatásból a szavazás adatait olvasni és írni is tudja.  Egyelőre a vezérlő statikus sztringadatokat ad vissza a nézetben.

```csharp
namespace VotingWeb.Controllers
{
    using System;
    using System.Collections.Generic;
    using System.Fabric;
    using System.Fabric.Query;
    using System.Linq;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Mvc;
    using Newtonsoft.Json;

    [Produces("application/json")]
    [Route("api/Votes")]
    public class VotesController : Controller
    {
        private readonly HttpClient httpClient;

        public VotesController(HttpClient httpClient)
        {
            this.httpClient = httpClient;
        }

        // GET: api/Votes
        [HttpGet]
        public async Task<IActionResult> Get()
        {
            List<KeyValuePair<string, int>> votes= new List<KeyValuePair<string, int>>();
            votes.Add(new KeyValuePair<string, int>("Pizza", 3));
            votes.Add(new KeyValuePair<string, int>("Ice cream", 4));

            return Json(votes);
        }
     }
}
```

### <a name="configure-the-listening-port"></a>A figyelőport konfigurálása

A VotingWeb kezelőfelületi szolgáltatás létrehozásakor a Visual Studio véletlenszerűen kiválaszt egy portot, amelyen a szolgáltatás a figyelést végzi.  A VotingWeb szolgáltatás az alkalmazás kezelőfelületeként működik, és fogadja a külső forgalmat, úgyhogy rendeljük ezt a szolgáltatást egy rögzített, jól ismert porthoz.  A [szolgáltatásjegyzék](service-fabric-application-and-service-manifests.md) deklarálja a szolgáltatásvégpontokat.

Megoldáskezelő nyissa meg a  *VotingWeb/PackageRoot/ServiceManifest.xml*.  Keresse meg az **Endpoint** (Végpont) elemet a **Resources** (Erőforrások) szakaszban, és módosítsa a **Port** értékét **8080**-ra. Az alkalmazás helyi üzembe helyezéséhez és futtatásához az alkalmazásfigyelő-portnak a számítógépen megnyitva és elérhető állapotban kell lennie.

```xml
<Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" Port="8080" />
    </Endpoints>
  </Resources>
```

Az alkalmazás URL-címének tulajdonságértékét is frissítse a szavazási projektben, így a webböngésző a megfelelő portot nyitja meg, amikor hibakeresést hajt végre az alkalmazásban.  A Megoldáskezelőben válassza ki a **Voting** projektet, és frissítse az **Alkalmazás URL-címe** tulajdonságot **8080**-ra.

### <a name="deploy-and-run-the-voting-application-locally"></a>A szavazóalkalmazás helyi üzembe helyezése és futtatása
Most már futtathatja a szavazóalkalmazást a hibakereséshez. A Visual Studióban nyomja le az **F5** billentyűt, hogy hibakeresési módban üzembe helyezze az alkalmazást a helyi Service Fabric-fürtön. Az alkalmazást nem lehet majd futtatni, ha korábban nem **rendszergazdaként** nyitotta meg a Visual Studiót.

> [!NOTE]
> Az alkalmazás első helyi üzembe helyezésekor a Visual Studio létrehoz egy helyi hibakeresési Service Fabric-fürtöt.  A fürt létrehozása eltarthat egy ideig. A fürt létrehozási állapota a Visual Studio kimeneti ablakában jelenik meg.

A szavazóalkalmazás helyi Service Fabric-fürtön való üzembe helyezése után a webalkalmazás automatikusan megnyílik egy böngészőlapon, és a következőképpen kell kinéznie:

![ASP.NET Core-kezelőfelület](./media/service-fabric-tutorial-create-dotnet-app/debug-front-end.png)

Az alkalmazás hibakeresésének leállításához lépjen vissza a Visual Studióba, és nyomja le a **Shift + F5** billentyűparancsot.

## <a name="add-a-stateful-back-end-service-to-your-application"></a>Állapotalapú háttérszolgáltatás hozzáadása az alkalmazáshoz

Most, hogy az alkalmazásban egy ASP.NET Web API-szolgáltatás fut, adjon hozzá egy állapotalapú megbízható szolgáltatást, hogy adatokat tárolhasson az alkalmazásban.

A Service Fabric megbízható gyűjtemények használatával konzisztens módon és megbízhatóan tárolja az adatokat a szolgáltatásban. A megbízható gyűjtemények olyan, magas rendelkezésre állású és megbízható gyűjteményosztályok, amelyeket mindenki jól ismerhet, aki már használt C#-gyűjteményeket.

Ebben az oktatóanyagban létrehoz egy szolgáltatást, amely egy számlálóértéket tárol egy megbízható gyűjteményben.

1. A Megoldáskezelőben kattintson a jobb gombbal a **Szolgáltatások** elemre a Voting alkalmazásprojektben, és válassza a **Hozzáadás -> Új Service Fabric-szolgáltatás...** lehetőséget.
    
2. Az **Új Service Fabric-szolgáltatás** párbeszédablakban válassza az **Állapotalapú ASP.NET Core** lehetőséget, nevezze el a szolgáltatást (**VotingData**), majd nyomja le az **OK** gombot.

    A szolgáltatásprojekt létrehozása után az alkalmazás két szolgáltatást fog tartalmazni. Miközben létrehozza az alkalmazást, hasonlóképpen adhat hozzá további szolgáltatásokat. Mindegyik – a többitől függetlenül – verziószámmal ellátható és frissíthető.

3. A következő oldalon az ASP.NET Core projektsablonjai találhatók. A jelen oktatóanyaghoz válassza az **API** lehetőséget.

    A Visual Studio létrehoz egy VotingData nevű szolgáltatási projektet, és megjeleníti azt a Megoldáskezelőben.

    ![Megoldáskezelő](./media/service-fabric-tutorial-create-dotnet-app/solution-explorer-aspnetcore-webapi-service.png)

### <a name="add-the-votedatacontrollercs-file"></a>A VoteDataController.cs fájl hozzáadása

A **VotingData** projektben kattintson a jobb gombbal a **Vezérlők** mappára, majd válassza a **Hozzáadás->Új elem->Osztály** lehetőséget. A fájlnak adja a **VoteDataController.cs** nevet, és kattintson a **Hozzáadás** parancsra. Cserélje le a fájl tartalmát a következőkkel, majd mentse a módosításokat.

```csharp
namespace VotingData.Controllers
{
    using System.Collections.Generic;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.ServiceFabric.Data;
    using Microsoft.ServiceFabric.Data.Collections;

    [Route("api/[controller]")]
    public class VoteDataController : Controller
    {
        private readonly IReliableStateManager stateManager;

        public VoteDataController(IReliableStateManager stateManager)
        {
            this.stateManager = stateManager;
        }

        // GET api/VoteData
        [HttpGet]
        public async Task<IActionResult> Get()
        {
            CancellationToken ct = new CancellationToken();

            IReliableDictionary<string, int> votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

            using (ITransaction tx = this.stateManager.CreateTransaction())
            {
                Microsoft.ServiceFabric.Data.IAsyncEnumerable<KeyValuePair<string, int>> list = await votesDictionary.CreateEnumerableAsync(tx);

                Microsoft.ServiceFabric.Data.IAsyncEnumerator<KeyValuePair<string, int>> enumerator = list.GetAsyncEnumerator();

                List<KeyValuePair<string, int>> result = new List<KeyValuePair<string, int>>();

                while (await enumerator.MoveNextAsync(ct))
                {
                    result.Add(enumerator.Current);
                }

                return this.Json(result);
            }
        }

        // PUT api/VoteData/name
        [HttpPut("{name}")]
        public async Task<IActionResult> Put(string name)
        {
            IReliableDictionary<string, int> votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

            using (ITransaction tx = this.stateManager.CreateTransaction())
            {
                await votesDictionary.AddOrUpdateAsync(tx, name, 1, (key, oldvalue) => oldvalue + 1);
                await tx.CommitAsync();
            }

            return new OkResult();
        }

        // DELETE api/VoteData/name
        [HttpDelete("{name}")]
        public async Task<IActionResult> Delete(string name)
        {
            IReliableDictionary<string, int> votesDictionary = await this.stateManager.GetOrAddAsync<IReliableDictionary<string, int>>("counts");

            using (ITransaction tx = this.stateManager.CreateTransaction())
            {
                if (await votesDictionary.ContainsKeyAsync(tx, name))
                {
                    await votesDictionary.TryRemoveAsync(tx, name);
                    await tx.CommitAsync();
                    return new OkResult();
                }
                else
                {
                    return new NotFoundResult();
                }
            }
        }
    }
}
```

## <a name="connect-the-services"></a>A szolgáltatások összekapcsolása

A következő lépésben két szolgáltatást fog összekapcsolni, majd beállítani az előtér-webalkalmazást úgy, hogy az lekérje a szavazási információt a háttérszolgáltatásból, majd beállítsa azt.

A Service Fabric teljes rugalmasságot biztosít a megbízható szolgáltatásokkal folytatott kommunikáció terén. Egy alkalmazáson belül előfordulhat, hogy TCP-n keresztül elérhető szolgáltatások vannak. Elképzelhető, hogy más szolgáltatások egy HTTP REST API-n keresztül, megint más szolgáltatások pedig webes szoftvercsatornákon keresztül érhetők el. A rendelkezésre álló lehetőségekről és azok kompromisszumairól a [szolgáltatásokkal folytatott kommunikációt](service-fabric-connect-and-communicate-with-services.md) ismertető részben találhat további információt.

Ez az oktatóanyag az [ASP.NET Core Web API-t](service-fabric-reliable-services-communication-aspnetcore.md) és a [Service Fabric fordított proxyt](service-fabric-reverseproxy.md) használja, hogy a VotingWeb webes kezelőfelületi szolgáltatás kommunikálhasson a háttérbeli VotingData szolgáltatással. A fordított proxy alapértelmezés szerint a 19081-es port használatára van konfigurálva, és alkalmasnak kell lennie ehhez az oktatóanyaghoz. A fordított proxy portja a fürt beállításához használt Azure Resource Manager sablonban van beállítva. A használt portot a **Microsoft.ServiceFabric/clusters** erőforrás fürtsablonblonjában tudja megkeresni: 

```json
"nodeTypes": [
          {
            ...
            "httpGatewayEndpointPort": "[variables('nt0fabricHttpGatewayPort')]",
            "isPrimary": true,
            "vmInstanceCount": "[parameters('nt0InstanceCount')]",
            "reverseProxyEndpointPort": "[parameters('SFReverseProxyPort')]"
          }
        ],
```
A helyi fejlesztési fürtben használt fordított proxy port megkereséséhez tekintse meg a **HttpApplicationGatewayEndpoint** elemet a helyi Service Fabric-fürt jegyzékfájljában:
1. Nyisson meg egy böngészőablakot, és navigáljon a http: \/ /localhost: 19080 elemre a Service Fabric Explorer eszköz megnyitásához.
2. Válassza ki a **cluster-> jegyzékfájlt**.
3. Jegyezze fel a HttpApplicationGatewayEndpoint elem portját. Ez alapértelmezés szerint a 19081-es port. Ha mégsem az, akkor módosítania kell a portot a következő VotesController.cs kód GetProxyAddress metódusában.

<a id="updatevotecontroller" name="updatevotecontroller_anchor"></a>

### <a name="update-the-votescontrollercs-file"></a>A VotesController.cs fájl frissítése

A **VotingWeb** projektben nyissa meg a *Controllers/VotesController.cs* fájlt.  Cserélje le a `VotesController` osztálydefiníció tartalmát a következőkkel, majd mentse a módosításokat. Ha az előző lépésben kikeresett fordítottproxy-port nem a 19081-es, módosítsa a GetProxyAddress metódusban használt portot a 19081-esről a talált portra.

```csharp
public class VotesController : Controller
{
    private readonly HttpClient httpClient;
    private readonly FabricClient fabricClient;
    private readonly StatelessServiceContext serviceContext;

    public VotesController(HttpClient httpClient, StatelessServiceContext context, FabricClient fabricClient)
    {
        this.fabricClient = fabricClient;
        this.httpClient = httpClient;
        this.serviceContext = context;
    }

    // GET: api/Votes
    [HttpGet("")]
    public async Task<IActionResult> Get()
    {
        Uri serviceName = VotingWeb.GetVotingDataServiceName(this.serviceContext);
        Uri proxyAddress = this.GetProxyAddress(serviceName);

        ServicePartitionList partitions = await this.fabricClient.QueryManager.GetPartitionListAsync(serviceName);

        List<KeyValuePair<string, int>> result = new List<KeyValuePair<string, int>>();

        foreach (Partition partition in partitions)
        {
            string proxyUrl =
                $"{proxyAddress}/api/VoteData?PartitionKey={((Int64RangePartitionInformation) partition.PartitionInformation).LowKey}&PartitionKind=Int64Range";

            using (HttpResponseMessage response = await this.httpClient.GetAsync(proxyUrl))
            {
                if (response.StatusCode != System.Net.HttpStatusCode.OK)
                {
                    continue;
                }

                result.AddRange(JsonConvert.DeserializeObject<List<KeyValuePair<string, int>>>(await response.Content.ReadAsStringAsync()));
            }
        }

        return this.Json(result);
    }

    // PUT: api/Votes/name
    [HttpPut("{name}")]
    public async Task<IActionResult> Put(string name)
    {
        Uri serviceName = VotingWeb.GetVotingDataServiceName(this.serviceContext);
        Uri proxyAddress = this.GetProxyAddress(serviceName);
        long partitionKey = this.GetPartitionKey(name);
        string proxyUrl = $"{proxyAddress}/api/VoteData/{name}?PartitionKey={partitionKey}&PartitionKind=Int64Range";

        StringContent putContent = new StringContent($"{{ 'name' : '{name}' }}", Encoding.UTF8, "application/json");
        putContent.Headers.ContentType = new MediaTypeHeaderValue("application/json");

        using (HttpResponseMessage response = await this.httpClient.PutAsync(proxyUrl, putContent))
        {
            return new ContentResult()
            {
                StatusCode = (int) response.StatusCode,
                Content = await response.Content.ReadAsStringAsync()
            };
        }
    }

    // DELETE: api/Votes/name
    [HttpDelete("{name}")]
    public async Task<IActionResult> Delete(string name)
    {
        Uri serviceName = VotingWeb.GetVotingDataServiceName(this.serviceContext);
        Uri proxyAddress = this.GetProxyAddress(serviceName);
        long partitionKey = this.GetPartitionKey(name);
        string proxyUrl = $"{proxyAddress}/api/VoteData/{name}?PartitionKey={partitionKey}&PartitionKind=Int64Range";

        using (HttpResponseMessage response = await this.httpClient.DeleteAsync(proxyUrl))
        {
            if (response.StatusCode != System.Net.HttpStatusCode.OK)
            {
                return this.StatusCode((int) response.StatusCode);
            }
        }

        return new OkResult();
    }


    /// <summary>
    /// Constructs a reverse proxy URL for a given service.
    /// Example: http://localhost:19081/VotingApplication/VotingData/
    /// </summary>
    /// <param name="serviceName"></param>
    /// <returns></returns>
    private Uri GetProxyAddress(Uri serviceName)
    {
        return new Uri($"http://localhost:19081{serviceName.AbsolutePath}");
    }

    /// <summary>
    /// Creates a partition key from the given name.
    /// Uses the zero-based numeric position in the alphabet of the first letter of the name (0-25).
    /// </summary>
    /// <param name="name"></param>
    /// <returns></returns>
    private long GetPartitionKey(string name)
    {
        return Char.ToUpper(name.First()) - 'A';
    }
}
```

<a id="walkthrough" name="walkthrough_anchor"></a>

## <a name="walk-through-the-voting-sample-application"></a>A mintául szolgáló szavazóalkalmazás bemutatása

A szavazóalkalmazás két szolgáltatásból áll:

* Webes kezelőfelületi szolgáltatás (VotingWeb) – Olyan ASP.NET Core webes kezelőfelületi szolgáltatás, amely kiszolgálja a weboldalt, és webes API-kat biztosít a háttérszolgáltatással folytatott kommunikációhoz.
* Háttérszolgáltatás (VotingData) – Olyan ASP.NET Core webszolgáltatás, amely egy API-t biztosít a szavazási eredmények tárolásához egy megbízható, lemezen őrzött szótárban.

![Alkalmazásdiagram](./media/service-fabric-tutorial-create-dotnet-app/application-diagram.png)

Amikor szavazatot adnak le az alkalmazásban, az alábbi eseményekre kerül sor:

1. A JavaScript HTTP PUT kérelemként elküldi a szavazati kérést a webes API-nak a webes kezelőfelületi szolgáltatásban.

2. A webes kezelőfelületi szolgáltatás egy proxy segítségével megkeresi és továbbítja a HTTP PUT kérelmet a háttérszolgáltatásnak.

3. A háttérszolgáltatás fogadja a bejövő kérelmet, és egy megbízható szótárban tárolja a frissített eredményt, amelyet a fürt több csomópontjára is replikál, és egy lemezen őriz. Az alkalmazás összes adata a fürtön tárolódik, így nincs szükség adatbázisra.

## <a name="debug-in-visual-studio"></a>Hibakeresés a Visual Studióban

A Visual Studióban történő hibakeresés során egy helyi Service Fabric fejlesztési fürtöt használ. Arra is lehetősége van, hogy a hibakeresési folyamatot a saját forgatókönyvéhez igazítsa. Ebben az alkalmazásban az adatokat a háttérszolgáltatásban egy megbízható szótár segítségével tárolja. A Visual Studio alapértelmezés szerint a hibakereső leállításakor eltávolítja az alkalmazást. Az alkalmazás eltávolításával a háttérszolgáltatásban tárolt adatok is el lesznek távolítva. Ha szeretné megtartani az adatokat a hibakeresési munkamenetek között, akkor módosítania kell az **Application Debug Mode** (Alkalmazás hibakeresési módja) tulajdonságot a **Voting** (Szavazás) projektben a Visual Studióban.

Ha szeretné megtekinteni, hogy mi történik a kódban, hajtsa végre a következő lépéseket:

1. Nyissa meg a **VotingWeb\VotesController.cs** fájlt, és állítson be egy töréspontot a webes API **put** metódusában (72. sor).

2. Nyissa meg a **VotingData\VoteDataController.cs** fájlt, és állítson be egy töréspontot a webes API **put** metódusában (54. sor).

3. Nyomja le az **F5** billentyűt az alkalmazás hibakeresési módban való elindításához.

4. Térjen vissza a böngészőhöz, és kattintson egy szavazási lehetőségre vagy adjon meg egy újat. Az első töréspont a webes kezelőfelület API-vezérlőjében jelentkezik.
    

   1. A böngészőben futó JavaScript ekkor küld kérelmet a webes kezelőfelületi szolgáltatás API-vezérlőjének.

      ![Szavazási kezelőfelületi szolgáltatás hozzáadása](./media/service-fabric-tutorial-create-dotnet-app/addvote-frontend.png)

   2. Elsőként hozza létre a ReverseProxyra mutató URL-címet a háttérszolgáltatás számára **(1)**.
   3. Ezután küldje el a HTTP PUT kérelmet a ReverseProxy **(2)**.
   4. Végül küldje vissza a választ a háttérszolgáltatásból az ügyfélhez **(3)**.

5. A folytatáshoz nyomja le az **F5** billentyűt.
   1. Ezzel elérte a háttérszolgáltatás töréspontját.

      ![Szavazási háttérszolgáltatás hozzáadása](./media/service-fabric-tutorial-create-dotnet-app/addvote-backend.png)

   2. A metódus első sorában **(1)** a `StateManager` használatával egy `counts` nevű megbízható szótárt kérhet le vagy adhat hozzá.
   3. A megbízható szótárakban tárolt értékekkel folytatott mindennemű interakcióhoz tranzakcióra van szükség, amelyet ez a using utasítás **(2)** hoz létre.
   4. A tranzakcióban frissítse a szavazási lehetőséghez tartozó kulcs értékét, majd véglegesítse a műveletet **(3)**. Ha a véglegesítési metódus visszatért, az adatok frissülnek a szótárban, és a fürt egyéb csomópontjaira is replikálódnak. Az adatok ettől fogva biztonságosan tárolódnak a fürtön, és a háttérszolgáltatás feladatait más csomópontok is átvehetik, míg az adatok továbbra is elérhetők maradnak.
6. A folytatáshoz nyomja le az **F5** billentyűt.

A hibakeresési munkamenet leállításához nyomja le a **Shift+F5** billentyűkombinációt.

## <a name="next-steps"></a>Következő lépések

Az oktatóanyag jelen részében megismerkedhetett a következőkkel:

> [!div class="checklist"]
> * ASP.NET Core Web API-szolgáltatás létrehozása állapotalapú megbízható szolgáltatásként
> * ASP.NET Core Web-alkalmazásszolgáltatás létrehozása állapot nélküli webszolgáltatásként
> * A fordított proxy használata az állapotalapú szolgáltatással folytatott kommunikációhoz

Folytassa a következő oktatóanyaggal:
> [!div class="nextstepaction"]
> [Az alkalmazás üzembe helyezése az Azure-ban](service-fabric-tutorial-deploy-app-to-party-cluster.md)
