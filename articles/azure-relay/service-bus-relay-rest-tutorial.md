---
title: 'Oktatóanyag: REST-oktatóanyag a Azure Relay használatával'
description: 'Oktatóanyag: Azure Relay gazdagép-alkalmazás létrehozása, amely egy REST-alapú felületet tesz elérhetővé.'
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.date: 06/23/2020
ms.openlocfilehash: 0620f55650d0e4da0cd7a616649df952f3017455
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "88922327"
---
# <a name="tutorial-azure-wcf-relay-rest-tutorial"></a>Oktatóanyag: Azure WCF Relay REST-oktatóanyag

Ez az oktatóanyag azt ismerteti, hogyan hozhat létre olyan Azure Relay gazda alkalmazást, amely egy REST-alapú felületet tesz elérhetővé. A REST lehetővé teszi egy webes ügyfél, például egy webes böngésző számára, hogy hozzáférjen a HTTP-kérelmeken keresztül a Service Bus alkalmazásprogramozási felületekhez.

Az oktatóanyag a Windows Communication Foundation (WCF) REST programozási modellt használja a REST-szolgáltatás Azure Relayn való létrehozásához. További információ: [WCF Rest programozási modell](/dotnet/framework/wcf/feature-details/wcf-web-http-programming-model) és [szolgáltatások tervezése és implementálása](/dotnet/framework/wcf/designing-and-implementing-services).

Ebben az oktatóanyagban a következő feladatokat hajtja végre:

> [!div class="checklist"]
>
> * Az oktatóanyag előfeltételeinek telepítése.
> * Hozzon létre egy Relay-névteret.
> * Hozzon létre egy REST-alapú WCF szolgáltatási szerződést.
> * A REST-alapú WCF-szerződés implementálása.
> * A REST-alapú WCF szolgáltatás üzemeltetése és futtatása.
> * A szolgáltatás futtatása és tesztelése.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez a következő előfeltételekre lesz szüksége:

* Azure-előfizetés. Ha még nincs előfizetése, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/), mielőtt hozzákezd.
* [Visual Studio 2015 vagy újabb verzió](https://www.visualstudio.com). Az oktatóanyagban szereplő példák a Visual Studio 2019-et használják.
* Azure SDK a .NET-hez. Telepítse az [SDK letöltési oldaláról](https://azure.microsoft.com/downloads/).

## <a name="create-a-relay-namespace"></a>Relay-névtér létrehozása

A Relay-funkciók Azure-ban való használatához először létre kell hoznia egy szolgáltatásnévteret. A névtér egy hatókörkezelési tárolót biztosít az Azure erőforrásainak címzéséhez az alkalmazáson belül. Relay-névtér létrehozásához kövesse az [itt leírt utasításokat](relay-create-namespace-portal.md).

## <a name="define-a-rest-based-wcf-service-contract-to-use-with-azure-relay"></a>A Azure Relayhoz használni kívánt REST-alapú WCF szolgáltatási szerződés megadása

WCF REST-stílusú szolgáltatás létrehozásakor meg kell határoznia a szerződést. A szerződés megadja a gazdagép által támogatott műveleteket. Egy szolgáltatási művelet egy webszolgáltatás-metódushoz hasonlít. Hozzon létre egy szerződést C++, C# vagy Visual Basic interfésszel. A felület minden metódusa egy konkrét szolgáltatási műveletnek felel meg. Alkalmazza a [ServiceContractAttribute](/dotnet/api/system.servicemodel.servicecontractattribute) attribútumot az egyes adapterekre, és alkalmazza a [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute) attribútumot az egyes műveletekre. 

> [!TIP]
> Ha a [ServiceContractAttribute](/dotnet/api/system.servicemodel.servicecontractattribute) rendelkező illesztőfelület egyik metódusa nem rendelkezik [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute), akkor ez a metódus nincs kitéve. A feladatokhoz használt kód az eljárást követő példában látható.

A WCF-szerződések és a REST-stílusú szerződések közötti elsődleges különbség egy tulajdonság hozzáadása a [OperationContractAttribute](/dotnet/api/system.servicemodel.operationcontractattribute): [WebGetAttribute](/dotnet/api/system.servicemodel.web.webgetattribute). Ez a tulajdonság lehetővé teszi a felület egy metódusának leképezését egy, a felület másik oldalán levő metódussá. Ez a példa a [WebGetAttribute](/dotnet/api/system.servicemodel.web.webgetattribute) attribútum használatával kapcsolja össze a metódust `HTTP GET` . Ez a megközelítés lehetővé teszi, hogy Service Bus az interfésznek eljuttatott parancsok pontos lekérését és értelmezését.

### <a name="to-create-a-contract-with-an-interface"></a>Szerződés létrehozása illesztőfelülettel

1. Indítsa el a Microsoft Visual studiót rendszergazdaként. Ehhez kattintson a jobb gombbal a Visual Studio program ikonjára, majd válassza a **Futtatás rendszergazdaként** lehetőséget.
1. A Visual Studióban válassza az **új projekt létrehozása** lehetőséget.
1. A **create a New Project (új projekt létrehozása**) területen válassza a **konzol alkalmazás (.NET-keretrendszer)** lehetőséget a C# számára, és válassza a **tovább** lehetőséget
1. Nevezze el a projekt *ImageListener*. Használja az alapértelmezett **helyet**, majd válassza a **Létrehozás** lehetőséget.

   C#-projekt esetén a Visual Studio létrehoz egy *program. cs* fájlt. Ez az osztály tartalmaz egy üres `Main()` metódust, amely szükséges a konzolalkalmazás projektek helyes létrejöttéhez.

1. **Megoldáskezelő** kattintson a jobb gombbal a **ImageListener** projektre, majd válassza a **NuGet-csomagok kezelése** lehetőséget.
1. Válassza a **Tallózás** elemet, majd keresse meg és válassza a **WindowsAzure. ServiceBus** elemet. Válassza a **telepítés** lehetőséget, és fogadja el a használati feltételeket.

    Ez a lépés Service Bus és *System.ServiceModel.dll* hivatkozásait adja hozzá. Ez a csomag automatikusan hozzáadja a Service Bus-könyvtárakra és a WCF-re mutató hivatkozásokat `System.ServiceModel` .

1. Explicit módon adjon hozzá egy hivatkozást `System.ServiceModel.Web.dll` a projekthez. A **megoldáskezelő** kattintson a jobb gombbal a Project mappában található **referenciák** elemre, majd válassza a **hivatkozás hozzáadása** elemet.
1. A **hivatkozás hozzáadása** lapon válassza a **keretrendszer** lehetőséget, majd írja be a *System. ServiceModel. Web* **kifejezést a keresés** mezőbe. Jelölje be a **System.ServiceModel.Web** jelölőnégyzetet, majd kattintson az **OK** lehetőségre.

Ezután végezze el a következő kód módosítását a projektben:

1. Adja hozzá a következő `using` utasításokat a *program. cs* fájl elejéhez.

    ```csharp
    using System.ServiceModel;
    using System.ServiceModel.Channels;
    using System.ServiceModel.Web;
    using System.IO;
    ```

    * A [System.ServiceModel](/dotnet/api/system.servicemodel) az a névtér, amely lehetővé teszi a programozott hozzáférést a WCF alapszintű szolgáltatásaihoz. WCF Relay a WCF számos objektumát és attribútumát használja a szolgáltatási szerződések definiálásához. Ezt a névteret a továbbító alkalmazásai többségében használhatja.
    * A [System. ServiceModel. Channels](/dotnet/api/system.servicemodel.channels) segít meghatározni a csatornát, amely a Azure Relay és az ügyfél webböngészőjével kommunikáló objektum.
    * A [System. ServiceModel. Web](/dotnet/api/system.servicemodel.web) olyan típusokat tartalmaz, amelyek lehetővé teszik webalapú alkalmazások létrehozását.

1. Nevezze át a névteret a következőre: `ImageListener` `Microsoft.ServiceBus.Samples` .

    ```csharp
    namespace Microsoft.ServiceBus.Samples
    {
        ...
    ```

1. Közvetlenül a névtér deklarációjának nyitó kapcsos zárójele után adjon meg egy nevű új felületet, `IImageContract` és alkalmazza az `ServiceContractAttribute` attribútumot az illesztőfelületre a értékkel `https://samples.microsoft.com/ServiceModel/Relay/RESTTutorial1` . 

    ```csharp
    [ServiceContract(Name = "ImageContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/RESTTutorial1")]
    public interface IImageContract
    {
    }
    ```

    A névtér értéke különbözik a kód tartományában használt névtértől. A névtér értéke a szerződés egyedi azonosítója, és verziószám-információval kell rendelkeznie. További információ: [Service Versioning](/dotnet/framework/wcf/service-versioning) (Szolgáltatás verziószámozása). A névtér explicit meghatározásával megelőzhető az alapértelmezett névtér hozzáadása a szerződésnévhez.

1. Az `IImageContract` illesztőfelületen belül deklaráljon egy metódust az egyetlen művelethez, amelyet a `IImageContract` Szerződés közzétesz a felületen, és alkalmazza az `OperationContract` attribútumot arra a metódusra, amelyet közzé szeretne tenni a nyilvános Service Bus szerződés részeként.

    ```csharp
    public interface IImageContract
    {
        [OperationContract]
        Stream GetImage();
    }
    ```

1. Az `OperationContract` attribútumban adja hozzá az `WebGet` értéket.

    ```csharp
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }
    ```

   Az érték hozzáadásával `WebGet` a Relay szolgáltatás átirányítja a HTTP Get kéréseket a `GetImage` -re, és lefordítja a visszatérési értékeit a `GetImage` `HTTP GETRESPONSE` válaszba. Az oktatóanyag későbbi részében egy webböngésző segítségével érheti el ezt a metódust, és megjelenítheti a rendszerképet a böngészőben.

1. Közvetlenül az `IImageContract` definíciója után deklaráljon egy csatornát, amely örökli az `IImageContract` és az `IClientChannel` felületek tulajdonságait is.

    ```csharp
    public interface IImageChannel : IImageContract, IClientChannel { }
    ```

   A csatorna egy olyan WCF-objektum, amelyen keresztül a szolgáltatás és az ügyfél információkat adnak át egymásnak. Később létrehozza a csatornát a gazda alkalmazásban. Azure Relay ezután ezzel a csatornával továbbítja a HTTP GET kéréseket a böngészőből a `GetImage` megvalósításba. A továbbító a csatornát is használja a visszaadott érték kiszámításához `GetImage` és az `HTTP GETRESPONSE` ügyfél böngészőbe való lefordításához.

1. Válassza a **Build**  >  **Build megoldás** lehetőséget, hogy az eddigi munkája pontosságát erősítse.

### <a name="example-that-defines-a-wcf-relay-contract"></a>WCF Relay szerződést definiáló példa

A következő kód egy alapszintű felületet mutat be, amely meghatározza a WCF Relay szerződést.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "IImageContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

## <a name="implement-the-rest-based-wcf-service-contract"></a>A REST-alapú WCF szolgáltatási szerződés implementálása

REST-stílusú WCF Relay szolgáltatás létrehozásához először hozzon létre egy kapcsolatot egy felület használatával. A következő lépés a felület megvalósítása. Ez az eljárás magában foglalja egy nevű osztály létrehozását `ImageService` , amely megvalósítja a felhasználó által definiált `IImageContract` felületet. A szerződés megvalósítása után a felületet egy *App.config* fájl segítségével konfigurálhatja. A konfigurációs fájl az alkalmazáshoz szükséges információkat tartalmazza. Ez az információ tartalmazza a szolgáltatás nevét, a szerződés nevét, valamint a továbbítási szolgáltatással való kommunikációhoz használt protokoll típusát. A feladatokhoz használt kód az eljárást követő példában látható.

Ahogy az előző lépések is, a REST-stílusú szerződések és a WCF Relay szerződések megvalósítása között kis különbség van.

### <a name="to-implement-a-rest-style-service-bus-contract"></a>REST-stílusú Service Bus szerződés megvalósítása

1. Hozzon létre egy új, `ImageService` nevű osztályt közvetlenül az `IImageContract` felület meghatározása után. Az `ImageService` osztály megvalósítja az `IImageContract` felületet.

    ```csharp
    class ImageService : IImageContract
    {
    }
    ```

    Hasonlóan az egyéb felületi megvalósításokhoz, a definíciót megvalósíthatja egy másik fájlban. Ebben az oktatóanyagban azonban a megvalósítás ugyanabban a fájlban jelenik meg, mint a felületdefiníció és a `Main()` metódus.

1. Alkalmazza a [ServiceBehaviorAttribute attribútum](/dotnet/api/system.servicemodel.servicebehaviorattribute) attribútumot az `IImageService` osztályra, hogy jelezze, hogy az osztály egy WCF-szerződés megvalósítását jelenti.

    ```csharp
    [ServiceBehavior(Name = "ImageService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
    }
    ```

    Ahogy korábban említettük, ez a névtér nem egy hagyományos névtér. Részét képezi a szerződést azonosító WCF-architektúra. További információkért tekintse meg az [adategyezmények nevét](/dotnet/framework/wcf/feature-details/data-contract-names/).

1. Adjon hozzá egy *. jpg* rendszerképet a projekthez. Ez a fájl egy olyan kép, amelyet a szolgáltatás a fogadó böngészőben megjelenít.

   1. Kattintson a jobb gombbal a projektre, és válassza a **Hozzáadás** lehetőséget.
   1. Ezután válassza a **meglévő elemet**.
   1. A **meglévő elem hozzáadása** lehetőséggel tallózással keresse meg a megfelelő. jpg-et, majd válassza a **Hozzáadás** lehetőséget. A fájl hozzáadásakor a **fájl neve** melletti legördülő listából válassza a **minden fájl** elemet.

   Az oktatóanyag többi része azt feltételezi, hogy a rendszerkép neve *image.jpg*. Ha más fájllal rendelkezik, át kell neveznie a képet, vagy módosítania kell a kódot a kompenzálására.

1. Győződjön meg arról, hogy a futó szolgáltatás megtalálja a képfájlt, **megoldáskezelő** kattintson a jobb gombbal a képfájlra, majd válassza a **Tulajdonságok parancsot**. A **Tulajdonságok** területen állítsa a **Másolás a kimeneti könyvtárba** a másolás, **Ha újabb** lehetőséget.

1. A alkalmazásban a eljárással [hozzon létre egy szerződést egy felülettel](#to-create-a-contract-with-an-interface) , amely az *System.Drawing.dll* szerelvényre mutató hivatkozást ad hozzá a projekthez.

1. Adja hozzá a következő társított `using` utasításokat:

    ```csharp
    using System.Drawing;
    using System.Drawing.Imaging;
    using Microsoft.ServiceBus;
    using Microsoft.ServiceBus.Web;
    ```

1. A `ImageService` osztályban adja hozzá a következő konstruktort, amely betölti a bitképet, és előkészíti, hogy küldje el az ügyfél böngészőjébe:

    ```csharp
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";
   
        Image bitmap;
   
        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }
    }
    ```

1. Közvetlenül az előző kód után adja hozzá a következő `GetImage` metódust a `ImageService` osztályban a képet tartalmazó HTTP-üzenet visszaadásához.

    ```csharp
    public Stream GetImage()
    {
        MemoryStream stream = new MemoryStream();
        this.bitmap.Save(stream, ImageFormat.Jpeg);
   
        stream.Position = 0;
        WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";
   
        return stream;
    }
    ```

    Ez a megvalósítás a `MemoryStream` rendszerkép lekérésére és a böngészőbe való továbbításra való előkészítésére használja. Az adatfolyam-pozíciót nulla értékre állítja, az adatfolyam tartalmát *. jpg* néven deklarálja, és továbbítja az információt.

1. Válassza a **Build**  >  **Build megoldás** lehetőséget.

### <a name="to-define-the-configuration-for-running-the-web-service-on-service-bus"></a>A konfiguráció meghatározása a webszolgáltatás Service Buson való futtatásához

1. A **megoldáskezelő** kattintson duplán a **App.config** lehetőségre a fájl megnyitásához a Visual Studio Editorban.

    A *App.config* fájl tartalmazza a szolgáltatás nevét, végpontját és kötését. A végpont az a hely, Azure Relay az ügyfelek és a gazdagépek számára lehetővé teszi az egymással való kommunikációt. A kötés a kommunikációhoz használt protokoll típusa. A fő különbség az, hogy a konfigurált szolgáltatási végpont egy [WebHttpRelayBinding](/dotnet/api/microsoft.servicebus.webhttprelaybinding) kötésre hivatkozik.

1. A `<system.serviceModel>` XML-elem egy WCF-elem, amely egy vagy több szolgáltatást határoz meg. Itt a szolgáltatás nevének és végpontjának definiálására szolgál. A elem alján `<system.serviceModel>` , de továbbra is a `<system.serviceModel>` `<bindings>` következő tartalommal rendelkező elemet vegyen fel:

    ```xml
    <bindings>
        <!-- Application Binding -->
        <webHttpRelayBinding>
            <binding name="default">
                <security relayClientAuthenticationType="None" />
            </binding>
        </webHttpRelayBinding>
    </bindings>
    ```

    Ez a tartalom határozza meg az alkalmazásban használt kötéseket. Több kötést is megadhat, de ebben az oktatóanyagban csak egyet határozhat meg.

    Az előző kód egy WCF Relay [WebHttpRelayBinding](/dotnet/api/microsoft.servicebus.webhttprelaybinding) kötést határoz meg a következőhöz: `relayClientAuthenticationType` `None` . Ez a beállítás azt jelzi, hogy a kötést használó végpontoknak nincs szükségük ügyfél-hitelesítő adatokra.

1. A `<bindings>` elem után adjon hozzá egy `<services>` elemet. A kötésekhez hasonlóan megadhat több szolgáltatást is egyetlen konfigurációs fájlon belül. Ez az oktatóanyag azonban csak egyet ad meg.

    ```xml
    <services>
        <!-- Application Service -->
        <service name="Microsoft.ServiceBus.Samples.ImageService"
             behaviorConfiguration="default">
            <endpoint name="RelayEndpoint"
                    contract="Microsoft.ServiceBus.Samples.IImageContract"
                    binding="webHttpRelayBinding"
                    bindingConfiguration="default"
                    behaviorConfiguration="sbTokenProvider"
                    address="" />
        </service>
    </services>
    ```

    Ez a tartalom olyan szolgáltatást konfigurál, amely a korábban definiált alapértelmezett értéket használja `webHttpRelayBinding` . Az alapértelmezett értéket is használja `sbTokenProvider` , amely a következő lépésben van meghatározva.

1. Az `<services>` elem után hozzon létre egy `<behaviors>` elemet a következő tartalommal, `SAS_KEY` és cserélje le a kifejezést a Shared Access Signature (SAS) kulcsra. A [Azure Portal][Azure portal]sas-kulcsának beszerzéséhez tekintse meg a [felügyeleti hitelesítő adatok beolvasása](service-bus-relay-tutorial.md#get-management-credentials)című témakört.

    ```xml
    <behaviors>
        <endpointBehaviors>
            <behavior name="sbTokenProvider">
                <transportClientEndpointBehavior>
                    <tokenProvider>
                        <sharedAccessSignature keyName="RootManageSharedAccessKey" key="YOUR_SAS_KEY" />
                    </tokenProvider>
                </transportClientEndpointBehavior>
            </behavior>
            </endpointBehaviors>
            <serviceBehaviors>
                <behavior name="default">
                    <serviceDebug httpHelpPageEnabled="false" httpsHelpPageEnabled="false" />
                </behavior>
            </serviceBehaviors>
    </behaviors>
    ```

1. Továbbra is *App.config*, az `<appSettings>` elemben cserélje le a teljes kapcsolattípus értéket a portálról korábban beszerzett kapcsolatok karakterláncára.

    ```xml
    <appSettings>
       <!-- Service Bus specific app settings for messaging connections -->
       <add key="Microsoft.ServiceBus.ConnectionString"
           value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SAS_KEY"/>
    </appSettings>
    ```

1. A teljes megoldás létrehozásához válassza a **Build**  >  **Build-megoldás** lehetőséget.

### <a name="example-that-implements-the-rest-based-wcf-service-contract"></a>Példa a REST-alapú WCF szolgáltatási szerződés megvalósítására

A következő kód a szerződés és a szolgáltatás megvalósítását mutatja be egy REST-alapú szolgáltatás esetében, amely Service Bus fut a `WebHttpRelayBinding` kötés használatával.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;
using System.Drawing;
using System.Drawing.Imaging;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Web;

namespace Microsoft.ServiceBus.Samples
{


    [ServiceContract(Name = "ImageContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    [ServiceBehavior(Name = "ImageService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";

        Image bitmap;

        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }

        public Stream GetImage()
        {
            MemoryStream stream = new MemoryStream();
            this.bitmap.Save(stream, ImageFormat.Jpeg);

            stream.Position = 0;
            WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";

            return stream;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

A következő példában a szolgáltatáshoz társított *App.config* fájl látható.

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
    <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2"/>
    </startup>
    <system.serviceModel>
        <extensions>
            <!-- In this extension section we are introducing all known service bus extensions. User can remove the ones they don't need. -->
            <behaviorExtensions>
                <add name="connectionStatusBehavior"
                    type="Microsoft.ServiceBus.Configuration.ConnectionStatusElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="transportClientEndpointBehavior"
                    type="Microsoft.ServiceBus.Configuration.TransportClientEndpointBehaviorElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="serviceRegistrySettings"
                    type="Microsoft.ServiceBus.Configuration.ServiceRegistrySettingsElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </behaviorExtensions>
            <bindingElementExtensions>
                <add name="netMessagingTransport"
                    type="Microsoft.ServiceBus.Messaging.Configuration.NetMessagingTransportExtensionElement, Microsoft.ServiceBus,  Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="tcpRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.TcpRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="httpRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.HttpRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="httpsRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.HttpsRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="onewayRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.RelayedOnewayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </bindingElementExtensions>
            <bindingExtensions>
                <add name="basicHttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.BasicHttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="webHttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.WebHttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="ws2007HttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.WS2007HttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netOnewayRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetOnewayRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netEventRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetEventRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netMessagingBinding"
                    type="Microsoft.ServiceBus.Messaging.Configuration.NetMessagingBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </bindingExtensions>
        </extensions>
      <bindings>
        <!-- Application Binding -->
        <webHttpRelayBinding>
          <binding name="default">
            <security relayClientAuthenticationType="None" />
          </binding>
        </webHttpRelayBinding>
      </bindings>
      <services>
        <!-- Application Service -->
        <service name="Microsoft.ServiceBus.Samples.ImageService"
             behaviorConfiguration="default">
          <endpoint name="RelayEndpoint"
                  contract="Microsoft.ServiceBus.Samples.IImageContract"
                  binding="webHttpRelayBinding"
                  bindingConfiguration="default"
                  behaviorConfiguration="sbTokenProvider"
                  address="" />
        </service>
      </services>
      <behaviors>
        <endpointBehaviors>
          <behavior name="sbTokenProvider">
            <transportClientEndpointBehavior>
              <tokenProvider>
                <sharedAccessSignature keyName="RootManageSharedAccessKey" key="YOUR_SAS_KEY" />
              </tokenProvider>
            </transportClientEndpointBehavior>
          </behavior>
        </endpointBehaviors>
        <serviceBehaviors>
          <behavior name="default">
            <serviceDebug httpHelpPageEnabled="false" httpsHelpPageEnabled="false" />
          </behavior>
        </serviceBehaviors>
      </behaviors>
    </system.serviceModel>
    <appSettings>
        <!-- Service Bus specific app settings for messaging connections -->
        <add key="Microsoft.ServiceBus.ConnectionString"
            value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=YOUR_SAS_KEY>"/>
    </appSettings>
</configuration>
```

## <a name="host-the-rest-based-wcf-service-to-use-azure-relay"></a>A REST-alapú WCF szolgáltatás üzemeltetése Azure Relay

Ez a szakasz azt ismerteti, hogyan futtathat webszolgáltatásokat egy WCF Relay használatával rendelkező Console-alkalmazással. Az ebben a szakaszban írt kód teljes listája megjelenik az eljárást követő példában.

### <a name="to-create-a-base-address-for-the-service"></a>Alapszintű cím létrehozása a szolgáltatáshoz

1. A `Main()` Function deklarációban hozzon létre egy változót a projekt névterének tárolásához. Ügyeljen arra, hogy a helyére a `yourNamespace` korábban létrehozott Relay-névtér nevét írja.

    ```csharp
    string serviceNamespace = "yourNamespace";
    ```

    A Service Bus a névtér nevét használva létrehoz egy egyedi URI-t.

1. Hozzon létre egy `Uri`-példányt a névtéren alapuló szolgáltatás alapszintű címéhez.

    ```csharp
    Uri address = ServiceBusEnvironment.CreateServiceUri("https", serviceNamespace, "Image");
    ```

### <a name="to-create-and-configure-the-web-service-host"></a>A webszolgáltatásgazda létrehozása és konfigurálása

Még mindig a-ben `Main()` hozza létre a webszolgáltatási gazdagépet a szakasz korábbi részében létrehozott URI-címhez.
  
```csharp
WebServiceHost host = new WebServiceHost(typeof(ImageService), address);
```

A szolgáltatásgazda az a WCF-objektum, amely a gazdaalkalmazást példányosítja. Ez a példa átadja a létrehozni kívánt gazdagép típusát, amely egy `ImageService` , valamint azt a helyet is, amelyen a gazda alkalmazást el szeretné tenni.

### <a name="to-run-the-web-service-host"></a>A webszolgáltatásgazda futtatása

1. Még mindig a-ben `Main()` adja hozzá a következő sort a szolgáltatás megnyitásához.

    ```csharp
    host.Open();
    ```

    A szolgáltatás jelenleg fut.

1. Megjelenít egy üzenetet, amely jelzi, hogy a szolgáltatás fut, valamint a szolgáltatás leállításának módját.

    ```csharp
    Console.WriteLine("Copy the following address into a browser to see the image: ");
    Console.WriteLine(address + "GetImage");
    Console.WriteLine();
    Console.WriteLine("Press [Enter] to exit");
    Console.ReadLine();
    ```

1. Ha kész, zárja be a szolgáltatásgazdát.

    ```csharp
    host.Close();
    ```

### <a name="example-of-the-service-contract-and-implementation"></a>A szolgáltatási szerződés és a megvalósítás példája

Az alábbi példa tartalmazza a szolgáltatási szerződést és a megvalósítását az oktatóanyag előző lépéseiből, és egy konzolalkalmazásban működteti a szolgáltatást. Fordítsa le a következő kódot egy *ImageListener.exe* nevű végrehajtható fájlba.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;
using System.Drawing;
using System.Drawing.Imaging;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Web;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "ImageContract", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    [ServiceBehavior(Name = "ImageService", Namespace = "https://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";

        Image bitmap;

        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }

        public Stream GetImage()
        {
            MemoryStream stream = new MemoryStream();
            this.bitmap.Save(stream, ImageFormat.Jpeg);

            stream.Position = 0;
            WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";

            return stream;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
            string serviceNamespace = "InsertServiceNamespaceHere";
            Uri address = ServiceBusEnvironment.CreateServiceUri("https", serviceNamespace, "Image");

            WebServiceHost host = new WebServiceHost(typeof(ImageService), address);
            host.Open();

            Console.WriteLine("Copy the following address into a browser to see the image: ");
            Console.WriteLine(address + "GetImage");
            Console.WriteLine();
            Console.WriteLine("Press [Enter] to exit");
            Console.ReadLine();

            host.Close();
        }
    }
}
```

## <a name="run-and-test-the-service"></a>A szolgáltatás futtatása és tesztelése

A megoldás létrehozása után az alábbi lépéseket követve futtathatja az alkalmazást:

1. Válassza az F5 lehetőséget, vagy tallózással keresse meg a végrehajtható fájl helyét ( *ImageListener\bin\Debug\ImageListener.exe*) a szolgáltatás futtatásához. Tartsa meg az alkalmazás futását, mert a következő lépéshez szükséges.
1. A kép megtekintéséhez másolja és illessze be a címet a parancssorból egy böngészőbe.
1. Ha elkészült, az alkalmazás bezárásához válassza az ENTER billentyűt a Parancssor ablakban.

## <a name="next-steps"></a>Következő lépések

Most, hogy létrehozott egy alkalmazást, amely a Azure Relay szolgáltatást használja, további információt a következő cikkekben talál:

* [Mi az Azure Relay?](relay-what-is-it.md)
* [Helyszíni WCF REST-szolgáltatás közzététele külső ügyfél számára az Azure WCF Relay használatával](service-bus-relay-tutorial.md)

[Azure portal]: https://portal.azure.com
