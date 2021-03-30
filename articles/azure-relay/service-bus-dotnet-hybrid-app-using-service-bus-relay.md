---
title: Azure Windows Communication Foundation-(WCF-) továbbító hibrid helyszíni/Felhőbeli alkalmazás (.NET) | Microsoft Docs
description: Megtudhatja, hogyan teheti elérhetővé a helyszíni WCF szolgáltatást a felhőben lévő webalkalmazásokban a Azure Relay használatával
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 06/23/2020
ms.openlocfilehash: 22b582634b623b39545eca225c8df0130606c2bb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "90983893"
---
# <a name="expose-an-on-premises-wcf-service-to-a-web-application-in-the-cloud-by-using-azure-relay"></a>Helyszíni WCF-szolgáltatás közzététele a Felhőbeli webalkalmazásokban Azure Relay használatával

Ez a cikk azt mutatja be, hogyan készíthet felhőbeli hibridalkalmazást a Microsoft Azure és a Visual Studio használatával. Olyan alkalmazást hoz létre, amely több Azure-erőforrást használ a felhőben. Ez az oktatóanyag segítséget nyújt a következők megismeréséhez:

* Meglévő webszolgáltatás létrehozása vagy alakítása a használathoz egy webes megoldással.
* Az Azure Windows Communication Foundation-(WCF-) továbbító szolgáltatás használata az Azure-alkalmazások és a máshol tárolt webszolgáltatások közötti adatmegosztásra.

Ebben az oktatóanyagban a következő feladatokat hajtja végre:

> [!div class="checklist"]
>
> * Az oktatóanyag előfeltételeinek telepítése.
> * Tekintse át a forgatókönyvet.
> * Hozzon létre egy névteret.
> * Hozzon létre egy helyszíni kiszolgálót.
> * Hozzon létre egy ASP .NET-alkalmazást.
> * Futtassa helyben az alkalmazást.
> * Telepítse a webalkalmazást az Azure-ba.
> * Futtassa az alkalmazást az Azure-ban.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez a következő előfeltételekre lesz szüksége:

* Azure-előfizetés. Ha még nincs előfizetése, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/), mielőtt hozzákezd.
* [Visual Studio 2015 vagy újabb verzió](https://www.visualstudio.com). Az oktatóanyagban szereplő példák a Visual Studio 2019-et használják.
* Azure SDK a .NET-hez. Telepítse az [SDK letöltési oldaláról](https://azure.microsoft.com/downloads/).

## <a name="how-azure-relay-helps-with-hybrid-solutions"></a>Hogyan segít az Azure Relay a hibrid megoldások terén?

Az üzleti megoldások általában egyéni kódok és meglévő funkciók kombinációjából állnak. Az egyéni kód az új és az egyedi üzleti követelményeket kezeli. A már bevezetett megoldások és rendszerek biztosítják a meglévő funkciókat.

A megoldások tervezői elkezdték a felhőt használni a méretezési követelmények egyszerűbb kezelése és az alacsonyabb működési költségek érdekében. Ennek során úgy látják, hogy a meglévő szolgáltatási eszközök, amelyeket a megoldásaink építőelemeként használnak, a vállalati tűzfalon belül vannak, és a felhőalapú megoldás könnyen elérhetővé válnak. Számos belső szolgáltatás nem építhető ki vagy nem üzemeltethető úgy, hogy könnyen elérhetővé válnak a vállalati hálózat peremén.

[Azure Relay](https://azure.microsoft.com/services/service-bus/) a meglévő WCF-webszolgáltatásokat veszi igénybe, és biztonságosan elérhetővé teszi ezeket a szolgáltatásokat a vállalati hálózaton kívüli megoldások számára anélkül, hogy zavaró módosításokat kellene megkövetelni a vállalati hálózati infrastruktúrában. Ezek a Relay-szolgáltatások továbbra is a meglévő környezeten belül vannak tárolva, de átadják a bejövő munkamenetek és a kérések figyelését a felhőn tárolt Relay-szolgáltatásnak. A Azure Relay a [megosztott hozzáférés-aláírási (SAS-)](../service-bus-messaging/service-bus-sas.md) hitelesítés használatával is megvédi ezeket a szolgáltatásokat a jogosulatlan hozzáféréstől.

## <a name="review-the-scenario"></a>A forgatókönyv áttekintése

Ebben az oktatóanyagban egy ASP.NET-webhelyet hoz létre, amely lehetővé teszi, hogy megtekintse a termékek listáját a termék leltározása lapon.

![Eset][0]

Az oktatóanyag feltételezi, hogy egy meglévő helyi rendszeren elérhetők a termékek adatai, és hogy az Azure Relay közvetítőn keresztül éri el ezt a rendszert. Egy egyszerű konzolos alkalmazásban futó webszolgáltatás szimulálja ezt a helyzetet. A termék memóriában tárolt készletét tartalmazza. Ezt a konzol alkalmazást futtathatja saját számítógépén, és üzembe helyezheti a webes szerepkört az Azure-ban. Ezzel megtudhatja, hogyan fut az Azure-adatközpontban futó webes szerepkör a számítógépén. Ez a hívás akkor is előfordul, ha a számítógép majdnem legalább egy tűzfalon és egy hálózati címfordítási (NAT-) réteg mögött található.

## <a name="set-up-the-development-environment"></a>A fejlesztési környezet kialakítása

Az Azure-alkalmazások fejlesztésének megkezdése előtt töltse le az eszközöket és állítsa be a fejlesztési környezetet:

1. Telepítse az Azure SDK for .NET-et az SDK [letöltési oldaláról](https://azure.microsoft.com/downloads/).
1. A **.net** oszlopban válassza ki a használt [Visual Studio](https://www.visualstudio.com) -verziót. Ez az oktatóanyag a Visual Studio 2019-et használja.
1. Amikor a rendszer kéri a telepítő futtatását vagy mentését, válassza a **Futtatás** lehetőséget.
1. A **webplatform-telepítő** párbeszédpanelen válassza a **telepítés** lehetőséget, és folytassa a telepítést.

A telepítés befejezése után minden szükséges az alkalmazás fejlesztésének megkezdéséhez. Az SDK olyan eszközöket tartalmaz, amelyekkel könnyedén fejleszthet Azure-alkalmazásokat a Visual Studióban.

## <a name="create-a-namespace"></a>Névtér létrehozása

Első lépésként létre kell hoznia egy névteret, és meg kell szereznie egy [közös hozzáférésű aláírás (SAS)](../service-bus-messaging/service-bus-sas.md) kulcsát. A névtér egy alkalmazás-határt biztosít minden olyan alkalmazáshoz, amelyet a továbbítási szolgáltatás tesz elérhetővé. A rendszer automatikusan létrehoz egy SAS-kulcsot a szolgáltatás névterének létrehozásakor. A szolgáltatási névtér és az SAS-kulcs kombinációja biztosítja az Azure számára az alkalmazásokhoz való hozzáférés hitelesítéséhez szükséges hitelesítő adatokat.

[!INCLUDE [relay-create-namespace-portal](../../includes/relay-create-namespace-portal.md)]

## <a name="create-an-on-premises-server"></a>Helyszíni kiszolgáló létrehozása

Először létrehoz egy szimulált helyszíni Termékkatalógus-rendszerrendszert.  Ez a projekt egy Visual Studio-konzolalkalmazás, és az [Azure Service Bus NuGet-csomagot](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) használja a Service Bus-kódtárak és konfigurációs beállítások belefoglalása érdekében. <a name="create-the-project"></a>

1. Indítsa el a Microsoft Visual studiót rendszergazdaként. Ehhez kattintson a jobb gombbal a Visual Studio program ikonjára, majd válassza a **Futtatás rendszergazdaként** lehetőséget.
1. A Visual Studióban válassza az **új projekt létrehozása** lehetőséget.
1. A **create a New Project (új projekt létrehozása**) területen válassza a **konzol alkalmazás (.NET-keretrendszer)** lehetőséget a C# és a **tovább** gombra.
1. Nevezze el a projekt *ProductsServer* , és válassza a **Létrehozás** lehetőséget.

   ![Az új projekt konfigurálása][11]

1. **Megoldáskezelő** kattintson a jobb gombbal a **ProductsServer** projektre, majd válassza a **NuGet-csomagok kezelése** lehetőséget.
1. Válassza a **Tallózás** elemet, majd keresse meg és válassza a **WindowsAzure. ServiceBus** elemet. Válassza a **telepítés** lehetőséget, és fogadja el a használati feltételeket.

   ![NuGet-csomag kiválasztása][13]

   A szükséges ügyfél-szerelvények már hivatkoznak rá.

1. Adjon egy új osztályt a termékszerződéshez. A **megoldáskezelő** kattintson a jobb gombbal a **ProductsServer** projektre, és válassza az  >  **osztály** hozzáadása elemet.
1. A **név** mezőben adja meg a *ProductsContract. cs* nevet, majd válassza a **Hozzáadás** lehetőséget.

Hajtsa végre a következő programkód-módosításokat a megoldásban:

1. A *ProductsContract.cs* fájlban cserélje le a névtér definícióját a következő kódra, amely meghatározza a szolgáltatás szerződését.

    ```csharp
    namespace ProductsServer
    {
        using System.Collections.Generic;
        using System.Runtime.Serialization;
        using System.ServiceModel;

        // Define the data contract for the service
        [DataContract]
        // Declare the serializable properties.
        public class ProductData
        {
            [DataMember]
            public string Id { get; set; }
            [DataMember]
            public string Name { get; set; }
            [DataMember]
            public string Quantity { get; set; }
        }

        // Define the service contract.
        [ServiceContract]
        interface IProducts
        {
            [OperationContract]
            IList<ProductData> GetProducts();

        }

        interface IProductsChannel : IProducts, IClientChannel
        {
        }
    }
    ```

1. A *program. cs* esetében cserélje le a névtér definícióját a következő kódra, amely hozzáadja a profil szolgáltatást és a gazdagépet.

    ```csharp
    namespace ProductsServer
    {
        using System;
        using System.Linq;
        using System.Collections.Generic;
        using System.ServiceModel;

        // Implement the IProducts interface.
        class ProductsService : IProducts
        {

            // Populate array of products for display on website
            ProductData[] products =
                new []
                    {
                        new ProductData{ Id = "1", Name = "Rock",
                                         Quantity = "1"},
                        new ProductData{ Id = "2", Name = "Paper",
                                         Quantity = "3"},
                        new ProductData{ Id = "3", Name = "Scissors",
                                         Quantity = "5"},
                        new ProductData{ Id = "4", Name = "Well",
                                         Quantity = "2500"},
                    };

            // Display a message in the service console application
            // when the list of products is retrieved.
            public IList<ProductData> GetProducts()
            {
                Console.WriteLine("GetProducts called.");
                return products;
            }

        }

        class Program
        {
            // Define the Main() function in the service application.
            static void Main(string[] args)
            {
                var sh = new ServiceHost(typeof(ProductsService));
                sh.Open();

                Console.WriteLine("Press ENTER to close");
                Console.ReadLine();

                sh.Close();
            }
        }
    }
    ```

1. A **megoldáskezelő** kattintson duplán a **App.config** lehetőségre a fájl megnyitásához a Visual Studio Editorban. A elem alján, de még a (z) területen `<system.ServiceModel>` is `<system.ServiceModel>` adja hozzá a következő XML-kódot. Ügyeljen arra, hogy a helyére a `yourServiceNamespace` névtér nevét és `yourKey` a korábban a portálról LEkért sas-kulcsot cserélje le:

    ```xml
    <system.serviceModel>
    ...
      <services>
         <service name="ProductsServer.ProductsService">
           <endpoint address="sb://yourServiceNamespace.servicebus.windows.net/products" binding="netTcpRelayBinding" contract="ProductsServer.IProducts" behaviorConfiguration="products"/>
         </service>
      </services>
      <behaviors>
         <endpointBehaviors>
           <behavior name="products">
             <transportClientEndpointBehavior>
                <tokenProvider>
                   <sharedAccessSignature keyName="RootManageSharedAccessKey" key="yourKey" />
                </tokenProvider>
             </transportClientEndpointBehavior>
           </behavior>
         </endpointBehaviors>
      </behaviors>
    </system.serviceModel>
    ```

    > [!NOTE]
    > A hiba oka `transportClientEndpointBehavior` csupán egy figyelmeztetés, és ez a példa nem blokkolja a problémát.

1. Továbbra is *App.config*, az `<appSettings>` elemben cserélje le a kapcsolatok karakterlánc értékét a korábban a portálról beszerzett kapcsolatok karakterláncára.

    ```xml
    <appSettings>
       <!-- Service Bus specific app settings for messaging connections -->
       <add key="Microsoft.ServiceBus.ConnectionString"
           value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey"/>
    </appSettings>
    ```

1. Válassza a CTRL + SHIFT + B billentyűkombinációt, **vagy válassza a Build**  >  **Build megoldás** lehetőséget az alkalmazás létrehozásához és az eddigi munkája pontosságának ellenőrzéséhez.

## <a name="create-an-aspnet-application"></a>ASP.NET-alkalmazás létrehozása

Ebben a szakaszban egy egyszerű ASP.NET-alkalmazást hoz létre, amely a termék szolgáltatásból beolvasott adatok megjelenítésére szolgál.

### <a name="create-the-project"></a>A projekt létrehozása

1. Győződjön meg arról, hogy a Visual Studio rendszergazdaként fut.
1. A Visual Studióban válassza az **új projekt létrehozása** lehetőséget.
1. Az **új projekt létrehozása** lapon válassza a **ASP.net webalkalmazás (.NET-keretrendszer)** lehetőséget a C# nyelvhez, majd válassza a **tovább** lehetőséget.
1. Nevezze el a projekt *ProductsPortal* , és válassza a **Létrehozás** lehetőséget.
1. Az **új ASP.net-Webalkalmazás létrehozása lapon** válassza az **MVC** lehetőséget, és válassza a **módosítás** a **hitelesítés** alatt lehetőséget.

   ![ASP .NET-alapú webalkalmazás kiválasztása][16]

1. A **hitelesítés módosítása** területen válassza a **Nincs hitelesítés** lehetőséget, majd kattintson **az OK gombra**. Ebben az oktatóanyagban olyan alkalmazást telepít, amelyhez nincs szükség felhasználói bejelentkezésre.

    ![Hitelesítés meghatározása][18]

1. **Hozzon létre egy új ASP.net-webalkalmazást**, és válassza a **Létrehozás** lehetőséget az MVC-alkalmazás létrehozásához.
1. Azure-erőforrások konfigurálása új webalkalmazáshoz. Kövesse a [webalkalmazás közzététele](../app-service/quickstart-dotnet-framework.md#launch-the-publish-wizard)című témakör lépéseit. Ezután térjen vissza ehhez az oktatóanyaghoz, és folytassa a következő lépéssel.
1. **Megoldáskezelő** kattintson a jobb gombbal a **modellek** elemre, majd válassza az  >  **osztály** hozzáadása elemet.
1. Nevezze el a *Product. cs* osztályt, majd válassza a **Hozzáadás** lehetőséget.

    ![Termékmodell létrehozása][17]

### <a name="modify-the-web-application"></a>A webalkalmazás módosítása

1. A Visual Studióban található *Product. cs* fájlban cserélje le a meglévő névtér-definíciót a következő kódra:

   ```csharp
    // Declare properties for the products inventory.
    namespace ProductsWeb.Models
    {
       public class Product
       {
           public string Id { get; set; }
           public string Name { get; set; }
           public string Quantity { get; set; }
       }
    }
    ```

1. A **megoldáskezelő** bontsa ki a **vezérlők** csomópontot, majd kattintson duplán a **HomeController. cs** fájlra, és nyissa meg a fájlt a Visual Studióban.
1. A *HomeController. cs* esetében cserélje le a meglévő névtér-definíciót a következő kódra:

    ```csharp
    namespace ProductsWeb.Controllers
    {
        using System.Collections.Generic;
        using System.Web.Mvc;
        using Models;

        public class HomeController : Controller
        {
            // Return a view of the products inventory.
            public ActionResult Index(string Identifier, string ProductName)
            {
                var products = new List<Product>
                    {new Product {Id = Identifier, Name = ProductName}};
                return View(products);
            }
         }
    }
    ```

1. A **megoldáskezelő** bontsa ki a megosztott **nézetek** csomópontot  >  , majd kattintson duplán a **_Layout. cshtml** fájlra a Visual Studio Editorban való megnyitásához.
1. A `My ASP.NET Application` *Northwind Traders-termékek* összes előfordulásának módosítása.
1. Távolítsa el a `Home` , `About` és a `Contact` hivatkozásokat. A következő példában törölje a kiemelt kódot.

    ![A generált listaelemek törlése][41]

1. A **megoldáskezelő** területen bontsa ki a **nézetek**  >  **kezdőlapja** elemet, majd kattintson duplán az **index. cshtml** fájlra, és nyissa meg a fájlt a Visual Studio Editorban. Cserélje le a fájl teljes tartalmát a következő kódra:

   ```html
   @model IEnumerable<ProductsWeb.Models.Product>

   @{
            ViewBag.Title = "Index";
   }

   <h2>Prod Inventory</h2>

   <table>
             <tr>
                 <th>
                     @Html.DisplayNameFor(model => model.Name)
                 </th>
                 <th></th>
                 <th>
                     @Html.DisplayNameFor(model => model.Quantity)
                 </th>
             </tr>

   @foreach (var item in Model) {
             <tr>
                 <td>
                     @Html.DisplayFor(modelItem => item.Name)
                 </td>
                 <td>
                     @Html.DisplayFor(modelItem => item.Quantity)
                 </td>
             </tr>
   }

   </table>
   ```

1. Az eddigi munkája pontosságának ellenőrzéséhez kiválaszthatja a CTRL + SHIFT + B billentyűkombinációt a projekt felépítéséhez.

### <a name="run-the-app-locally"></a>Az alkalmazás futtatása helyben

Futtassa az alkalmazást a működése ellenőrzéséhez.

1. Győződjön meg arról, hogy a **ProductsPortal** az aktív projekt. Kattintson a jobb gombbal a projekt nevére a **megoldáskezelő** , majd válassza a **beállítás indítási projektként** lehetőséget.
1. A Visual Studióban válassza az F5 elemet.

Az alkalmazásának meg kell jelennie egy böngészőben.

![A képernyőképen egy olyan alkalmazás látható, amely egy, a kijelölt URL-címmel rendelkező böngészőben fut.][21]

## <a name="put-the-pieces-together"></a>Az egyes alkotórészek összeállítása teljes egésszé

A következő lépés, hogy a helyszíni termékkiszolgálót az ASP.NET-alkalmazáshoz csatlakoztassuk.

1. Ha még nincs megnyitva, a Visual Studióban nyissa meg az [ASP.NET-alkalmazás létrehozása](#create-an-aspnet-application) szakaszban létrehozott **ProductsPortal** projektet.
1. A helyszíni [kiszolgáló létrehozása](#create-an-on-premises-server) szakasz lépéséhez hasonlóan adja hozzá a NuGet csomagot a projekt hivatkozásaihoz. **Megoldáskezelő** kattintson a jobb gombbal a **ProductsPortal** projektre, majd válassza a **NuGet-csomagok kezelése** lehetőséget.
1. Keressen a *WindowsAzure.ServiceBus* kifejezésre, és válassza ki az **WindowsAzure.ServiceBus** elemet. Ezután fejezze be a telepítést, és zárjuk be a párbeszédpanelt.
1. **Megoldáskezelő** kattintson a jobb gombbal a **ProductsPortal** projektre, majd válassza a  >  **meglévő elem hozzáadása elemet**.
1. Keresse meg a *ProductsContract.cs* fájlt a **ProductsServer** konzolprojektben. Emelje ki a *ProductsContract. cs* elemet. Kattintson a **Hozzáadás** elem melletti lefelé mutató nyílra, majd válassza a **Hozzáadás hivatkozásként** lehetőséget.

   ![Hozzáadás hivatkozásként][24]

1. Ezután nyissa meg a *HomeController.cs* fájlt a Visual Studio szerkesztőjében, és a névtér definícióját cserélje az alábbi kódra. Ügyeljen arra, hogy a helyére a `yourServiceNamespace` szolgáltatás névterének nevét és az `yourKey` sas-kulcsát írja. Ez a kód lehetővé teszi, hogy az ügyfél meghívja a helyszíni szolgáltatást, és visszaadja a hívás eredményét.

   ```csharp
   namespace ProductsWeb.Controllers
   {
       using System.Linq;
       using System.ServiceModel;
       using System.Web.Mvc;
       using Microsoft.ServiceBus;
       using Models;
       using ProductsServer;

       public class HomeController : Controller
       {
           // Declare the channel factory.
           static ChannelFactory<IProductsChannel> channelFactory;

           static HomeController()
           {
               // Create shared access signature token credentials for authentication.
               channelFactory = new ChannelFactory<IProductsChannel>(new NetTcpRelayBinding(),
                   "sb://yourServiceNamespace.servicebus.windows.net/products");
               channelFactory.Endpoint.Behaviors.Add(new TransportClientEndpointBehavior {
                   TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(
                       "RootManageSharedAccessKey", "yourKey") });
           }

           public ActionResult Index()
           {
               using (IProductsChannel channel = channelFactory.CreateChannel())
               {
                   // Return a view of the products inventory.
                   return this.View(from prod in channel.GetProducts()
                                    select
                                        new Product { Id = prod.Id, Name = prod.Name,
                                            Quantity = prod.Quantity });
               }
           }
       }
   }
   ```

1. A **Megoldáskezelőban** kattintson a jobb gombbal a **ProductsPortal** -megoldásra. Győződjön meg arról, hogy a jobb gombbal a megoldásra kattint, és nem a projektre. Válassza a  >  **meglévő projekt** hozzáadása lehetőséget.
1. Keresse meg a **ProductsServer** projektet, és kattintson duplán a *ProductsServer.csproj* megoldásfájlra annak hozzáadásához.
1. A **ProductsServer** futnia kell a **ProductsPortal** lévő információk megjelenítéséhez. **Megoldáskezelő** kattintson a jobb gombbal a **ProductsPortal** -megoldásra, és válassza a **Tulajdonságok** lehetőséget a **tulajdonságlapok** megjelenítéséhez.
1. Válassza az **Általános tulajdonságok**  >  **indítási projekt** elemet, és válassza a **több indítási projekt** lehetőséget. Győződjön meg arról, hogy a **ProductsServer** és a **ProductsPortal** megjelenik ebben a sorrendben, valamint hogy a **művelet** mindkét esetben **elindul**.

      ![Több indítási projekt][25]

1. Válassza a **közös tulajdonságok**  >  **projekt függőségei** lehetőséget a bal oldalon.
1. A **projektek** esetében válassza a **ProductsPortal** lehetőséget. Győződjön meg arról, hogy a **ProductsServer** ki van jelölve.

    ![Projekt függőségei][26]

1. A **projektek** esetében válassza a **ProductsServer** lehetőséget. Győződjön meg arról, hogy a **ProductsPortal** nincs kijelölve, majd kattintson **az OK** gombra a módosítások mentéséhez.

## <a name="run-the-project-locally"></a>A projekt helyi futtatása

Az alkalmazás helyi teszteléséhez a Visual Studióban válassza az F5 elemet. Először a helyszíni kiszolgálót, a **ProductsServer** kell elindítania, majd a **ProductsPortal** alkalmazásnak egy böngészőablakban kell kezdődnie. Ekkor láthatja, hogy a termékek leltára a termék helyszíni rendszerből beolvasott adatait listázza.

![Webalkalmazás][10]

Válassza a **frissítés** lehetőséget a **ProductsPortal** oldalon. Minden alkalommal, amikor frissíti a lapot, megjelenik a kiszolgálói alkalmazás, ha a `GetProducts()` **ProductsServer** nevű üzenet jelenik meg.

A következő szakasz folytatása előtt zárjunk be mindkét alkalmazást.

## <a name="deploy-the-productsportal-project-to-an-azure-web-app"></a>A ProductsPortal projekt telepítése egy Azure-webalkalmazásba

A következő lépés az Azure-webalkalmazás **ProductsPortal** -felületének újbóli közzététele:

1. A **megoldáskezelő** kattintson a jobb gombbal a **ProductsPortal** projektre, és válassza a **Közzététel** lehetőséget. A **Közzététel** lapon válassza a **Közzététel** elemet.

   > [!NOTE]
   > Előfordulhat, hogy egy hibaüzenet jelenik meg a böngészőablakban, amikor a **ProductsPortal** webprojekt automatikusan elindul a telepítés után. Emiatt nem kell aggódnia, ugyanis az okozza, hogy a **ProductsServer** alkalmazás még nem fut.
   >

1. Másolja az üzembe helyezett webalkalmazás URL-címét. Később szüksége lesz az URL-címre. Ezt az URL-címet a Visual Studióban található **Azure app Service tevékenység** ablakból is elérheti:

   ![Az üzembe helyezett alkalmazás URL-címe][9]

1. Zárja be a böngészőablakot a futó alkalmazás leállításához.

<a name="set-productsportal-as-web-app"></a>Az alkalmazás felhőben való futtatása előtt gondoskodnia kell arról, hogy a **ProductsPortal** a Visual studióból webalkalmazásként induljon el.

1. A Visual Studióban kattintson a jobb gombbal a **ProductsPortal** projektre, és válassza a **Tulajdonságok** lehetőséget.
1. Válassza a **web** lehetőséget. A **Start művelet** alatt válassza a **kezdő URL-cím** elemet. Adja meg a korábban üzembe helyezett webalkalmazás URL-címét, ebben a példában a következőt: `https://productsportal20190906122808.azurewebsites.net/` .

    ![Kezdő URL-cím][27]

1. Válassza   >  **a fájl mentés összes mentése** lehetőséget.
1. Válassza a **Build újjáépített**  >  **megoldás** lehetőséget.

## <a name="run-the-application"></a>Az alkalmazás futtatása

Az alkalmazás létrehozásához és futtatásához nyomja le az F5 billentyűt. A helyszíni kiszolgálónak, amely a **ProductsServer** -konzol alkalmazás, először el kell indulnia, majd a **ProductsPortal** alkalmazásnak egy böngészőablakban kell kezdődnie, ahogy az itt látható:

   ![A webalkalmazás futtatása az Azure-ban][1]

A termék leltára felsorolja a termék helyszíni rendszerből beolvasott adatait, és megjeleníti ezeket az információkat a webalkalmazásban. Ellenőrizze az URL-címet, és győződjön meg arról, hogy a **ProductsPortal** Azure-webalkalmazás fut a felhőben.

   > [!IMPORTANT]
   > A **ProductsServer** konzolalkalmazásnak futnia kell, és képesnek kell lennie biztosítani az adatokat a **ProductsPortal** alkalmazás számára. Ha a böngésző hibaüzenetet jelenít meg, várjon néhány másodpercet, hogy a **ProductsServer** betöltse a következő üzenetet, és megjeleníti a böngészőt, majd frissítse azt.
   >

A böngészőben frissítse a **ProductsPortal** lapot. Minden alkalommal, amikor frissíti a lapot, megjelenik a kiszolgálói alkalmazás, ha a `GetProducts()` **ProductsServer** nevű üzenet jelenik meg.

![Frissített kimenet][38]

## <a name="next-steps"></a>Következő lépések

Folytassa a következő oktatóanyaggal:

> [!div class="nextstepaction"]
>[Helyszíni WCF-szolgáltatás közzététele egy saját hálózaton kívüli WCF-ügyfélen](service-bus-relay-tutorial.md)

[0]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hybrid.png
[1]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/run-web-app.png
[NuGet]: https://nuget.org

[11]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/configure-productsserver.png
[13]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/install-nuget-service-bus-productsserver.png
[15]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-2.png
[16]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/choose-web-application-template.png
[17]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/add-class-productsportal.png
[18]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/change-authentication.png
[9]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/web-publish-activity.png
[10]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/run-web-app-locally.png

[21]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/run-web-app-locally-no-content.png
[24]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/add-existing-item-link.png
[25]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-13.png
[26]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-14.png
[27]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/launch-app-as-web-app.png

[36]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App2.png
[37]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-service1.png
[38]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-service2.png
[41]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-40.png
[43]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-43.png
