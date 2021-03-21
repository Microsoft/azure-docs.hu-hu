---
title: Több szolgáltatásból álló alkalmazás létrehozása, üzembe helyezése Service Fabric Meshban
description: Ebben az oktatóanyagban létrehozhat egy ASP.NET Core-webhelyet tartalmazó, háttér-webszolgáltatással kommunikáló, többszolgáltatású Azure Service Fabric mesh-szolgáltatást, valamint azon helyi hibakeresést végezhet, majd közzéteheti az Azure-on.
author: georgewallace
ms.topic: tutorial
ms.date: 09/18/2018
ms.author: gwallace
ms.custom: mvc, devcenter, devx-track-csharp
ms.openlocfilehash: b0bdb3c09aead812e1c16f4d0d17aae58e141809
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99626793"
---
# <a name="tutorial-create-debug-deploy-and-upgrade-a-multi-service-service-fabric-mesh-app"></a>Oktatóanyag: Többszolgáltatású Service Fabric Meshben-alkalmazás létrehozása, hibaelhárítása, üzembe helyezése és frissítése

> [!IMPORTANT]
> Az Azure Service Fabric Mesh előzetes verziója ki lett vonva. Az új központi telepítések többé nem lesznek engedélyezve a Service Fabric Mesh API-n keresztül. A meglévő központi telepítések támogatása a 2021. április 28. után folytatódik.
> 
> Részletekért lásd: az [Azure Service Fabric Mesh előzetes verziójának nyugdíjazása](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

Ez az oktatóanyag egy sorozat első része. Megismerheti, hogyan állíthat össze egy ASP.NET webes előtérrendszerrel és egy ASP.NET Core Web API háttérszolgáltatással rendelkező Service Fabric Mesh-alkalmazást a Visual Studióban. Ezt követően futtathatja az alkalmazás helyi hibakeresését a helyi fejlesztési fürtön. Az alkalmazást közzéteheti az Azure-on, majd konfigurációs és kódmódosításokat végezhet, és frissítheti az alkalmazást. Végül törölheti a nem használt Azure-erőforrásokat, így csak azért kell fizetnie, amit használ.

Miután elkészült, megismerkedett az alkalmazások életciklus-kezelésének legtöbb szakaszával, és létrehozott egy olyan alkalmazást, amely bemutatja, hogyan intézhet szolgáltatások közti hívást egy Service Fabric Mesh-alkalmazásban.

Ha nem szeretné manuálisan létrehozni a feladatlista-alkalmazást, akkor [letöltheti a forráskódot](https://github.com/azure-samples/service-fabric-mesh) a kész alkalmazáshoz, és folytathatja az [alkalmazás helyi hibakeresésével](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md).

A sorozat első részében a következőkkel ismerkedhet meg:

> [!div class="checklist"]
> * Egy ASP.NET webes előtérrendszerrel rendelkező Service Fabric mesh-alkalmazás létrehozása a Visual Studióval.
> * Egy feladatlista-elemeket megjelenítő modell létrehozása.
> * Egy háttérszolgáltatás létrehozása, majd adatok lekérése.
> * Vezérlő és DataContext környezet hozzáadása a háttérszolgáltatás Model-View-Controller elrendezésének részeként.
> * Weblap létrehozása a feladatlista elemeinek megjelenítéséhez.
> * A háttérszolgáltatást azonosító környezeti változók létrehozása

Ebben az oktatóanyag-sorozatban az alábbiakkal ismerkedhet meg:
> [!div class="checklist"]
> * Service Fabric Mesh-alkalmazás létrehozása Visual Studióban
> * [Egy, a helyi fejlesztési fürtön futó Service Fabric Mesh-alkalmazás hibakeresése](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)
> * [Service Fabric Mesh-alkalmazás üzembe helyezése](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)
> * [Service Fabric Mesh-alkalmazás frissítése](service-fabric-mesh-tutorial-upgrade.md)
> * [A Service Fabric Mesh erőforrásainak eltávolítása](service-fabric-mesh-tutorial-cleanup-resources.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdése előtt:

* Ha nem rendelkezik Azure-előfizetéssel, kezdés előtt [létrehozhat egy ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Győződjön meg arról, hogy [beállította a fejlesztési környezetet](service-fabric-mesh-howto-setup-developer-environment-sdk.md), amely tartalmazza a Service Fabric futtatókörnyezet, az SDK, a Docker, és a Visual Studio 2017 telepítését.

## <a name="create-a-service-fabric-mesh-project-in-visual-studio"></a>Service Fabric Mesh-projekt létrehozása Visual Studióban

Futtassa a Visual studiót, és válassza a **fájl**  >  **új**  >  **projekt elemet...**

A **New Project** (Új projekt) párbeszédpanel fenti **Search** (Keresés) mezőjébe írja be a következőt: `mesh`. Válassza a **Service Fabric Mesh Application** (Service Fabric mesh-alkalmazás) sablont. Ha nem látja a sablont, győződjön meg róla, hogy telepítette a Mesh SDK-t és a VS-eszközök előzetes verzióját a [fejlesztési környezet beállításáról szóló szakaszban](service-fabric-mesh-howto-setup-developer-environment-sdk.md) ismertetett módon.  

A **Name** (Név) mezőbe írja be a(z) `todolistapp` kifejezést, a **Location** (Hely) mezőben pedig adja meg a mappa elérési útját a projekt tárolandó fájljaihoz.

Ügyeljen rá, hogy bejelölje a **Create directory for solution** (Címtár létrehozása a megoldáshoz) jelölőnégyzetet, majd kattintson az **OK** gombra a Service Fabric mesh-projekt létrehozásához.

![Képernyőkép, amely bemutatja, hogyan lehet létrehozni egy Service Fabric Mesh-projektet.](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-new-project.png)

Ezután megjelenik a **New Service Fabric Service** (Új Service Fabric-szolgáltatás) párbeszédpanel.

### <a name="create-the-web-front-end-service"></a>A webes előtér-szolgáltatás létrehozása

A **New Service Fabric Service** (Új Service Fabric-szolgáltatás) párbeszédpanelen válassza ki az **ASP.NET Core** projekttípust, és ügyeljen rá, hogy a **Container OS** (Tároló operációs rendszere) beállítás **Windows** értékre van állítva.

Állítsa a **Service Name** (Szolgáltatás neve) beállítást **WebFrontEnd** értékre. Az ASP.NET Core szolgáltatás létrehozásához kattintson az **OK** gombra.

![A Visual Studio új Service Fabric mesh-projektjének párbeszédpanele](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-new-service-fabric-service.png)

Ezután megjelenik a ASP.NET Core webalkalmazás párbeszédpanel. Válassza a **Web Application** (Webalkalmazás) lehetőséget, majd kattintson az **OK** gombra.

![Képernyőfelvétel a webes Appliccation-sablonról.](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-new-aspnetcore-app.png)

Most már rendelkezik egy Service Fabric mesh-alkalmazással. Következő lépésként hozza létre a modellt a feladatlista-adatokhoz.

## <a name="create-the-to-do-items-model"></a>A feladatlista-modell létrehozása

Az egyszerűség kedvéért a feladatlista elemei egy memórialistában találhatók. Hozzon létre egy osztálytárat a feladatlista elemeihez, valamint egy listát, amelyben tárolhatja őket. A Visual Studióban, amelyen jelenleg a **todolistapp** -megoldás be van töltve, válassza a **fájl**  >    >  **új projekt** hozzáadása elemet.

Az **új projekt hozzáadása** **párbeszédpanel felső** részén írja be a következőt: `C# .net core class` . Válassza aki a **Class Library (.NET Core)** (Osztálytár [.Net Core]) sablont.

A **Name** (Név) mezőbe írja a következőt: `Model`. Az osztálytár létrehozásához kattintson az **OK** gombra.

A Megoldáskezelő **Model** (Modell) területén kattintson a jobb gombbal a **Class1.cs** elemre, és válassza a **Rename** (Átnevezés) lehetőséget. Nevezze át az osztályt **ToDoItem.cs** névre. Ha megjelenik egy üzenet, amely rákérdez, hogy az összes hivatkozást átnevezi-e, kattintson az **Igen** gombra.

Az üres `class ToDoItem` tartalmát cserélje a következőre:

```csharp
public class ToDoItem
{
    public string Description { get; set; }
    public int Index { get; set; }
    public bool Completed { get; set; }

    public ToDoItem(string description)
    {
        Description = description;
        Index = 0;
    }

    public static ToDoItem Load(string description, int index, bool completed)
    {
        ToDoItem newItem = new ToDoItem(description)
        {
            Index = index,
            Completed = completed
        };

        return newItem;
    }
}
```

Ez az osztály a-do elemeket jelképezi.

A feladatelemek listájának létrehozásához a Visual Studióban kattintson a jobb gombbal a **Model** (Modell) osztálytárra, majd az **Add** > **Class...** (Hozzáadás > Osztály...) lehetőségre. Megjelenik az **Add New Item** (Új elem hozzáadása) párbeszédpanel. A **Name** (Név) tulajdonságot állítsa `ToDoList.cs` értékre, és kattintson az **Add** (Hozzáadás) lehetőségre.

A **ToDoList.cs** elemben található üres `class ToDoList` tartalmát cserélje a következőre:

```csharp
public class ToDoList
{
    private List<ToDoItem> _items;

    public string Name { get; set; }
    public IEnumerable<ToDoItem> Items { get => _items; }

    public ToDoList(string name)
    {
        Name = name;
        _items = new List<ToDoItem>();
    }

    public ToDoItem Add(string description)
    {
        var item = new ToDoItem(description);
        _items.Add(item);
        item.Index = _items.IndexOf(item);
        return item;
    }
    public void Add(ToDoItem item)
    {
        _items.Add(item);
        item.Index = _items.Count - 1;
    }

    public ToDoItem RemoveAt(int index)
    {
        if (index >= 0 && index < _items.Count)
        {
            var result = _items[index];
            _items.RemoveAt(index);

            // Reorder items
            for (int i = index; i < _items.Count; i++)
            {
                _items[i].Index = i;
            }

            return result;
        }
        else
        {
            throw new IndexOutOfRangeException();
        }
    }
}
```

Ezután hozza létre a feladatlista elemeit követő Service Fabric-szolgáltatást.

## <a name="create-the-back-end-service"></a>A háttérszolgáltatás létrehozása

A Visual Studio **Solution Explorer** (Megoldáskezelő) ablakában kattintson a jobb gombbal a **todolistapp** elemre, majd az **Add** > **New Service Fabric Service...** (Hozzáadás > Új Service Fabric-szolgáltatás...) lehetőségre

Ekkor megjelenik a **New Service Fabric Service** (Új Service Fabric-szolgáltatás) párbeszédpanel. Válassza ki az **ASP.NET Core** projekttípust, és ügyeljen rá, hogy a **Container OS** (Tároló operációs rendszere) beállítás **Windows** értékre van állítva. Állítsa a **Service Name** (Szolgáltatás neve) beállítást **ToDoService** értékre. Az ASP.NET Core szolgáltatás létrehozásához kattintson az **OK** gombra.

Ezután megjelenik az **New ASP.NET Core Web Application** (Új ASP.NET Core-webalkalmazás) párbeszédpanel. A párbeszédpanelen válassza ki a **API-t**, majd kattintson az **OK** gombra. A szolgáltatás projektje ekkor a szolgáltatás részévé válik.

![Visual Studio új ASP.NET Core-alkalmazás](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-new-webapi.png)

Mivel a háttérszolgáltatáshoz nem tartozik felhasználói felület, kapcsolja ki a szolgáltatás indulásakor elinduló böngésző beállítást. A **Solution Explorer** (Megoldáskezelő) ablakban kattintson a jobb gombbal a **ToDoService** elemre, és válassza a **Properties** (Tulajdonságok) lehetőséget. A megjelenő ablakban kattintson a bal oldali **Debug** (Hibakeresés) lapra, és törölje a jelölést a **Launch browser** (Böngésző indítása) jelölőnégyzetből. A módosítás mentéséhez nyomja le a **Ctrl+S** billentyűkombinációt.

Mivel ez a szolgáltatás tartja fenn a feladatlista adatait, hozzá kell adnia egy referenciát a Model osztálytárjához. A megoldáskezelő kattintson a jobb gombbal a **ToDoService** elemre, majd válassza a hivatkozás **hozzáadása**  >  **...** lehetőséget. Ekkor megjelenik a **Reference Manager** párbeszédpanel.

A **Reference Manager** párbeszédpanelen jelölje be a **Model** jelölőnégyzetét, majd kattintson az **OK** gombra.

### <a name="add-a-data-context"></a>Adatkörnyezet hozzáadása

Ezt követően hozzon létre egy olyan adatkörnyezetet, amely koordinálja az adatmodell adatainak kiszolgálását.

Az adatkörnyezet osztályának hozzáadásához a Solution Explorer (Megoldáskezelő) ablakában kattintson a jobb gombbal a **ToDoService** elemre, majd válassza az **Add** > **Class** (Hozzáadás > Osztály) lehetőséget.
A megjelenő **Add New Item** (Új elem hozzáadása) párbeszédpanelen ügyeljen rá, hogy ki van jelölve a **Class** (Osztály) elem, és állítsa a **Name** (Név) elemet `DataContext.cs` értékre, végül kattintson az **Add** (Hozzáadás) lehetőségre.

A **DataContext.cs** elemben található üres `class DataContext` tartalmát cserélje a következőre:

```csharp
public static class DataContext
{
    public static Model.ToDoList ToDoList { get; } = new Model.ToDoList("Azure learning List");

    static DataContext()
    {
        // Seed to-do list
        ToDoList.Add(Model.ToDoItem.Load("Learn about microservices", 0, true));
        ToDoList.Add(Model.ToDoItem.Load("Learn about Service Fabric", 1, true));
        ToDoList.Add(Model.ToDoItem.Load("Learn about Service Fabric Mesh", 2, false));
    }
}
```

Ez a minimális adatkörnyezet feltölt néhány minta feladatlista-elemet, és hozzáférést biztosít hozzájuk.

### <a name="add-a-controller"></a>Vezérlő hozzáadása

A **ToDoService** projekt létrehozásakor a sablon megadott egy alapértelmezett vezérlőt, amely a HTTP-kérelmeket kezeli, és a HTTP-válaszokat létrehozza. A **Solution Explorer** (Megoldáskezelő) **ToDoService** területén nyissa meg a **Controllers** (Vezérlők) mappát, amelyben megjelenik a **ValuesController.cs** fájl. 

Kattintson a jobb gombbal **ValuesController.cs** elemre, majd a **Rename** (Átnevezés) lehetőségre. Nevezze át a fájlt a következőre: `ToDoController.cs`. Ha egy felugró üzenet megkérdezi, hogy minden referenciát át szeretne-e nevezni, válassza a **Yes** (Igen) lehetőséget.

Nyissa meg a **ToDoController.cs** fájlt, és cserélje le a(z) `class ToDoController` tartalmát a következőre:

```csharp
[Route("api/[controller]")]
public class ToDoController : Controller
{
    // GET api/todo
    [HttpGet]
    public IEnumerable<Model.ToDoItem> Get()
    {
        return DataContext.ToDoList.Items;
    }

    // GET api/todo/5
    [HttpGet("{index}")]
    public Model.ToDoItem Get(int index)
    {
        return DataContext.ToDoList.Items.ElementAt(index);
    }

    //// POST api/values
    //[HttpPost]
    //public void Post([FromBody]string value)
    //{
    //}

    //// PUT api/values/5
    //[HttpPut("{id}")]
    //public void Put(int id, [FromBody]string value)
    //{
    //}

    // DELETE api/values/5
    [HttpDelete("{index}")]
    public void Delete(int index)
    {
    }
}
```

Ez az oktatóanyag nem tartalmazza a hozzáadásról, törlésről és egyéb műveletekről szóló ismertetőket, így az egyéb szolgáltatásokkal való kommunikáció maradhat a középpontban.

## <a name="create-the-web-page-that-displays-to-do-items"></a>A feladatlista elemeit megjelenítő weblap létrehozása

A háttérszolgáltatás implementálása után kódolnia kell a feladatlista elemeit megjelenítő webhelyet. A következő lépések a **WebFrontEnd** projektben végezendők el.

A feladatlista elemeit megjelenítő weblapnak hozzáférésre van szüksége a **ToDoItem** osztályhoz és listához.
A **megoldáskezelő** adjon hozzá egy hivatkozást a modell projekthez úgy, hogy a jobb gombbal a **webfrontend** elemre kattint, és kiválasztja a hivatkozás **hozzáadása**  >  **... lehetőséget.** Ekkor megjelenik a **Reference Manager** párbeszédpanel.

A **Reference Manager** (Referenciakezelő) párbeszédpanelen jelölje be a **Model** jelölőnégyzetét, majd kattintson az **OK** gombra.

A **Solution Explorerben** nyissa meg az Index.cshtml lapot a **WebFrontEnd** > **Pages** > **Index.cshtml** elemre való navigálással. Nyissa meg az **Index.cshtml-t**.

Cserélje le a teljes fájl tartalmát a következő HTML-re, amely egy egyszerű, a feladatlista elemeit megjelenítő táblázatot definiál:

```HTML
@page
@model IndexModel
@{
    ViewData["Title"] = "Home page";
}

<div>
    <table class="table-bordered">
        <thead>
            <tr>
                <th>Description</th>
                <th>Done?</th>
            </tr>
        </thead>
        <tbody>
            @foreach (var item in Model.Items)
            {
                <tr>
                    <td>@item.Description</td>
                    <td>@item.Completed</td>
                </tr>
            }
        </tbody>
    </table>
</div>
```

Kattintson az **index. cshtml** fájl legördülő ikonjára a **megoldáskezelő** , majd nyissa meg az **index. cshtml. cs** fájlt.

Az **Index.cshtml.cs** tetején adja hozzá a következőt: `using System.Net.Http;`

A(z) `public class IndexModel` tartalmát cserélje a következőre:

```csharp
public class IndexModel : PageModel
{
    public Model.ToDoItem[] Items = new Model.ToDoItem[] { };

    public void OnGet()
    {
        HttpClient client = new HttpClient();

        using (HttpResponseMessage response = client.GetAsync(backendUrl).GetAwaiter().GetResult())
        {
            if (response.StatusCode == System.Net.HttpStatusCode.OK)
            {
                Items = Newtonsoft.Json.JsonConvert.DeserializeObject<Model.ToDoItem[]>(response.Content.ReadAsStringAsync().Result);
            }
        }
    }

    private static string backendDNSName = $"{Environment.GetEnvironmentVariable("ToDoServiceName")}";
    private static Uri backendUrl = new Uri($"http://{backendDNSName}:{Environment.GetEnvironmentVariable("ApiHostPort")}/api/todo");
}
```

### <a name="create-environment-variables"></a>Környezeti változók létrehozása

A háttérszolgáltatás URL-címére szükség van a szolgáltatással való kommunikációhoz. A jelen oktatóanyag esetében a következő kódrészlet (amely fent az IndexModel részeként lett definiálva) a környezeti változókat beolvasva állítja elő az URL-t:

```csharp
private static string backendDNSName = $"{Environment.GetEnvironmentVariable("ToDoServiceName")}";
private static Uri backendUrl = new Uri($"http://{backendDNSName}:{Environment.GetEnvironmentVariable("ApiHostPort")}/api/todo");
```

Az URL-cím a szolgáltatás nevéből és a portból áll. Ezek az adatok megtalálhatók a **ToDoService** projekt service.yaml fájljában.

> [!IMPORTANT]
> A következő lépésekben a YAML-fájlok módosítva lesznek.
> A service.yaml fájl változóit szóközökkel, és nem tabulátorokkal kell behúzni, különben nem fognak működni. Előfordulhat, hogy a Visual Studio beszúr tabulátorokat a környezeti változók létrehozásakor. Ezeket cserélje szóközökre. Bár hibák jelennek meg az **Build** debug kimenetében, az alkalmazás továbbra is elindul, de addig nem, amíg át nem alakítja a lapokat a szóközökbe és az újjáépítésbe. Annak biztosítása érdekében, hogy a Service. YAML fájlban ne legyenek tabulátorok, a Visual Studio szerkesztőben szóközöket láthat a   >  **speciális**   >  **nézet szerkesztése területen**.
> Vegye figyelembe, hogy service.yaml fájlok feldolgozása az angol területi beállítással történik. Ha decimális elválasztót kell használnia, használjon vessző helyett egy pontot, például:.

A **Solution Explorerben** lépjen a **ToDoService** projektre, és nyissa meg a **Service Resources** > **service.yaml** (Szolgáltatás-erőforrások > service.yaml) fájlt.

![1. ábra – A ToDoService service.yaml fájlja](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-serviceyaml-port.png)

 A szolgáltatás neve a `ToDoService` `services:` fenti ábrán a lásd: (1) szakaszban található.

* A port a `80` `endpoints:` fenti ábrán a lásd: (2) szakaszban található. A projekt portszáma valószínűleg eltérő lesz.

Ezután meg kell határoznia a szolgáltatás nevét és portszámát képviselő környezeti változókat a webfrontend-projektben, hogy meghívja a háttér-szolgáltatást.

A **Solution Explorerben** lépjen a **WebFrontEnd** > **Service Resources** > **service.yaml** (WebFrontEnd > Szolgáltatás-erőforrások > service.yaml) területre a háttérszolgáltatás címét meghatározó változók definiálásához.

Adja hozzá a következő változókat a Service. YAML fájlhoz `environmentVariables:` (először el kell távolítania a-et a törléshez), hogy `#` `environmentVariables:` a térköz fontos legyen, és illessze be a hozzáadott változókat a más változókkal `environmentVariables:` . Nagyon fontos, hogy a ApiHostPort értéke egyezzen a ToDoServiceListener port értékével, amely korábban a ToDoService Service. YAML fájljában volt látható.

```yaml
- name: ApiHostPort
  value: 
- name: ToDoServiceName
  value: ToDoService
```

> [!Tip]
> A következő két módon adható meg az érték `ToDoServiceName` : 
> - Csak a szolgáltatás neve, amely a Windows 10 hibakeresési forgatókönyvében, valamint a szolgáltatás Azure Service Fabric Meshba való telepítésekor is feloldja a megoldást.
> - Teljes mértékben minősített szolgáltatásnév. AppName. Ez csak akkor működik, ha a Windows 10 rendszeren hibakeresést végez.
> Ajánlott a szolgáltatás nevének használata a szolgáltatás feloldásához.

A **WebFrontEnd** projekt **service.yaml** fájljának ehhez hasonlónak kell lennie, bár a(z) `ApiHostPort` érték az Ön esetében valószínűleg eltérő lesz:

![A service.yaml a WebFrontEnd projektben](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-serviceyaml-envvars.png)


Most már készen áll a Service Fabric mesh-alkalmazás, valamint a háttér-webszolgáltatás fejlesztésére és a helyi fürtön való üzembe helyezésére.

## <a name="next-steps"></a>Következő lépések

Az oktatóanyag jelen részében megismerkedhetett a következőkkel:

> [!div class="checklist"]
> * Egy ASP.NET webes előtérrendszerrel rendelkező Service Fabric Mesh-alkalmazás létrehozása.
> * Egy feladatlista-elemeket megjelenítő modell létrehozása.
> * Egy háttérszolgáltatás létrehozása, majd adatok lekérése.
> * Vezérlő és DataContext környezet hozzáadása a háttérszolgáltatás Model-View-Controller elrendezésének részeként.
> * Weblap létrehozása a feladatlista elemeinek megjelenítéséhez.
> * A háttérszolgáltatást azonosító környezeti változók létrehozása

Folytassa a következő oktatóanyaggal:
> [!div class="nextstepaction"]
> [Egy helyi fejlesztési fürtben futó Service Fabric Mesh-alkalmazás hibakeresése](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)