---
title: Szolgáltatás-távelérés a C# használatával Service Fabric
description: Service Fabric a távelérés lehetővé teszi, hogy az ügyfelek és szolgáltatások a C#-szolgáltatásokkal kommunikáljanak távoli eljáráshívás használatával.
ms.topic: conceptual
ms.date: 09/20/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: a0486a27d76c978a65c4a3cfd81df52a12e4ea1d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98791577"
---
# <a name="service-remoting-in-c-with-reliable-services"></a>Szolgáltatás távelérési szolgáltatása C#-ban Reliable Services

> [!div class="op_single_selector"]
> * [C# Windowson](service-fabric-reliable-services-communication-remoting.md)
> * [Java Linuxon](service-fabric-reliable-services-communication-remoting-java.md)
>
>

Olyan szolgáltatások esetén, amelyek nem egy adott kommunikációs protokollhoz vagy veremhez kötődnek, például webes API-hoz, Windows Communication Foundationhoz vagy másokhoz, a Reliable Services-keretrendszer olyan távelérési mechanizmust biztosít, amely gyorsan és egyszerűen állíthatja be a távoli eljáráshívás szolgáltatást a szolgáltatásokhoz. Ez a cikk azt ismerteti, hogyan állíthat be távoli eljáráshívás szolgáltatást a C# nyelven írt szolgáltatásokhoz.

## <a name="set-up-remoting-on-a-service"></a>Távelérés beállítása a szolgáltatáson

A távelérést két egyszerű lépésben állíthatja be a szolgáltatáshoz:

1. Hozzon létre egy felületet a szolgáltatás megvalósításához. Ez az interfész határozza meg a szolgáltatásban távoli eljárás hívásához elérhető módszereket. A metódusoknak aszinkron metódusok tevékenység-visszaküldési módszereknek kell lenniük. A csatolónak meg kell valósítania `Microsoft.ServiceFabric.Services.Remoting.IService` annak jelzésére, hogy a szolgáltatásnak van-e távelérési felülete.
2. Használjon távelérési figyelőt a szolgáltatásban. A távelérési figyelő egy olyan `ICommunicationListener` implementáció, amely távelérési funkciókat biztosít. A `Microsoft.ServiceFabric.Services.Remoting.Runtime` névtér tartalmazza a bővítmény metódust `CreateServiceRemotingInstanceListeners` mind az állapot nélküli, mind a állapot-nyilvántartó szolgáltatásokhoz, amelyekkel távoli figyelőt lehet létrehozni az alapértelmezett távelérési átviteli protokoll használatával.

>[!NOTE]
>A `Remoting` névtér külön NuGet-csomagként érhető el `Microsoft.ServiceFabric.Services.Remoting` .

A következő állapot nélküli szolgáltatás például egyetlen metódust tesz elérhetővé, amely távoli eljáráshívás esetén "Hello World" beolvasását teszi lehetővé.

```csharp
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Microsoft.ServiceFabric.Services.Remoting;
using Microsoft.ServiceFabric.Services.Remoting.Runtime;
using Microsoft.ServiceFabric.Services.Runtime;

public interface IMyService : IService
{
    Task<string> HelloWorldAsync();
}

class MyService : StatelessService, IMyService
{
    public MyService(StatelessServiceContext context)
        : base (context)
    {
    }

    public Task<string> HelloWorldAsync()
    {
        return Task.FromResult("Hello!");
    }

    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
     return this.CreateServiceRemotingInstanceListeners();
    }
}
```

> [!NOTE]
> A szolgáltatási felületen található argumentumok és visszatérési típusok bármely egyszerű, összetett vagy egyéni típus lehet, de a .NET- [dataContractSerializer](/dotnet/framework/wcf/feature-details/types-supported-by-the-data-contract-serializer)szerializálni kell őket.
>
>

## <a name="call-remote-service-methods"></a>Távoli szolgáltatási módszerek hívása

Ha a távelérési verem használatával metódusokat hív meg egy szolgáltatáson, a szolgáltatás a osztályon keresztül helyi proxy használatával végezhető el `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` . A `ServiceProxy` metódus egy helyi proxyt hoz létre ugyanazzal a kezelőfelülettel, amelyet a szolgáltatás implementál. Ezzel a proxyval a metódusok távolról is meghívhatók az illesztőfelületen.

```csharp

IMyService helloWorldClient = ServiceProxy.Create<IMyService>(new Uri("fabric:/MyApplication/MyHelloWorldService"));

string message = await helloWorldClient.HelloWorldAsync();

```

A távelérési keretrendszer a szolgáltatás által az ügyfél felé terjesztett kivételeket propagálja. Ennek eredményeképpen, amikor a `ServiceProxy` használatban van, a-ügyfél feladata a szolgáltatás által okozott kivételek kezelése.

## <a name="service-proxy-lifetime"></a>Szolgáltatási proxy élettartama

A szolgáltatás-proxy létrehozása egy könnyű művelet, így tetszőleges számú igényt hozhat létre. A Service proxy-példányok a szükséges ideig újra felhasználhatók. Ha egy távoli eljáráshívás kivételt jelez, továbbra is használhatja ugyanazt a proxy-példányt. Minden szolgáltatási proxy tartalmaz egy kommunikációs ügyfelet, amely üzeneteket küld a vezetékes hálózaton keresztül. A távoli hívások meghívása során a rendszer belső ellenőrzéseket végez annak meghatározására, hogy a kommunikációs ügyfél érvényes-e. Az ellenőrzések eredményei alapján a kommunikációs ügyfelet szükség esetén újra létrehozza a rendszer. Ezért ha kivétel történik, nem kell újból létrehoznia `ServiceProxy` .

### <a name="service-proxy-factory-lifetime"></a>Service proxy gyári élettartama

A [ServiceProxyFactory](/dotnet/api/microsoft.servicefabric.services.remoting.client.serviceproxyfactory) egy olyan gyár, amely különböző távelérési felületek számára hoz létre proxy-példányokat. Ha az API `ServiceProxyFactory.CreateServiceProxy` -t használja a proxy létrehozásához, a keretrendszer létrehoz egy egyedi szolgáltatást.
A [IServiceRemotingClientFactory](/dotnet/api/microsoft.servicefabric.services.remoting.v1.client.iserviceremotingclientfactory) tulajdonságainak felülbírálásához hasznos lehet manuálisan létrehozni.
A gyári létrehozás költséges művelet. A Service proxy Factory a kommunikációs ügyfél belső gyorsítótárát tárolja.
Ajánlott eljárás a Service proxy-gyár gyorsítótárazása a lehető leghosszabbra.

## <a name="remoting-exception-handling"></a>Távelérési kivételek kezelését

A szolgáltatás API által okozott összes távoli kivételt AggregateException-ként küldik vissza az ügyfélnek. A távoli kivételeket a DataContract szerializálhatja. Ha nem, akkor a proxy API a szerializálási hibát [ServiceException](/dotnet/api/microsoft.servicefabric.services.communication.serviceexception) .

A szolgáltatás proxyja kezeli a szolgáltatáshoz létrehozott összes feladatátvételi kivételt. Újra feloldja a végpontokat, ha vannak feladatátvételi kivételek (nem átmeneti kivételek), és újrapróbálkozik a hívást a megfelelő végponttal. A feladatátvételi kivételek újrapróbálkozások száma határozatlan.
Átmeneti kivételek esetén a proxy újrapróbálkozik a hívással.

A [OperationRetrySettings](/dotnet/api/microsoft.servicefabric.services.communication.client.operationretrysettings)az alapértelmezett újrapróbálkozási paramétereket is megadja.

A felhasználó ezeket az értékeket úgy konfigurálhatja, hogy átadja a OperationRetrySettings objektumot a ServiceProxyFactory konstruktornak.

## <a name="use-the-remoting-v2-stack"></a>A távelérési szolgáltatás v2-veremének használata

A NuGet távelérési csomag 2,8-es verziójától kezdve lehetősége van a távelérési v2-verem használatára. A távelérési v2-verem jobban teljesít. Olyan szolgáltatásokat is kínál, mint például az egyéni szerializálás és a további csatlakoztatható API-k.
A sablon kódja továbbra is a távelérési v1-verem használatát teszi lehetővé.
A távelérési v2 nem kompatibilis a v1-rel (a korábbi távelérési veremmel). A szolgáltatás rendelkezésre állásának elkerülése érdekében kövesse a cikk a  [v1 – v2](#upgrade-from-remoting-v1-to-remoting-v2) verzióra történő frissítését ismertető cikket.

A v2-verem engedélyezéséhez a következő megközelítések érhetők el.

### <a name="use-an-assembly-attribute-to-use-the-v2-stack"></a>Szerelvény-attribútum használata a v2-verem használatához

Ezek a lépések a sablon kódját úgy változtatják meg, hogy a v2-veremet egy Assembly attribútum használatával használják.

1. Módosítsa a végponti erőforrást a `"ServiceEndpoint"` `"ServiceEndpointV2"` szolgáltatás jegyzékfájljában.

   ```xml
   <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2" />
    </Endpoints>
   </Resources>
   ```

2. A `Microsoft.ServiceFabric.Services.Remoting.Runtime.CreateServiceRemotingInstanceListeners`  kiterjesztési módszer használatával távelérési figyelőket hozhat létre (a v1 és v2 esetében egyaránt egyenlő).

   ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
   ```

3. A távelérési felületet tartalmazó szerelvény megjelölése `FabricTransportServiceRemotingProvider` attribútummal.

   ```csharp
   [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2, RemotingClientVersion = RemotingClientVersion.V2)]
   ```

Nincs szükség kód módosítására az ügyfél projektben.
Hozza létre az ügyfél szerelvényét az interfész szerelvényben, és győződjön meg arról, hogy a korábban megjelenített Assembly attribútum használatban van.

### <a name="use-explicit-v2-classes-to-use-the-v2-stack"></a>Explicit v2 osztályok használata a v2-verem használatához

A Assembly attribútum használatának alternatívájaként a v2-verem is engedélyezhető explicit v2 osztályok használatával.

Ezek a lépések megváltoztatják a sablon kódját, hogy a v2-veremet explicit v2 osztályok használatával használják.

1. Módosítsa a végponti erőforrást a `"ServiceEndpoint"` `"ServiceEndpointV2"` szolgáltatás jegyzékfájljában.

   ```xml
   <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2" />
    </Endpoints>
   </Resources>
   ```

2. Használja a [FabricTransportServiceRemotingListener](/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.runtime.fabrictransportserviceremotinglistener) a `Microsoft.ServiceFabric.Services.Remoting.V2.FabricTransport.Runtime` névtérből.

   ```csharp
   protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return new[]
        {
            new ServiceInstanceListener((c) =>
            {
                return new FabricTransportServiceRemotingListener(c, this);

            })
        };
    }
   ```

3. Ügyfelek létrehozásához használja a [FabricTransportServiceRemotingClientFactory](/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.client.fabrictransportserviceremotingclientfactory) a `Microsoft.ServiceFabric.Services.Remoting.V2.FabricTransport.Client` névtérből.

   ```csharp
   var proxyFactory = new ServiceProxyFactory((c) =>
          {
              return new FabricTransportServiceRemotingClientFactory();
          });
   ```

## <a name="upgrade-from-remoting-v1-to-remoting-v2"></a>Frissítés a távelérési kiszolgálóról a távoli eljáráshívás v2-re

A v1-ről v2-re való frissítéshez kétlépéses frissítés szükséges. Kövesse a jelen szakasz lépéseit.

1. Frissítse a v1 szolgáltatást a v2 szolgáltatásra az attribútum használatával.
Ez a módosítás gondoskodik arról, hogy a szolgáltatás figyelje a v1 és v2 figyelőt.

    a. Adjon hozzá egy "ServiceEndpointV2" nevű végpont-erőforrást a szolgáltatás jegyzékfájljában.
      ```xml
      <Resources>
        <Endpoints>
          <Endpoint Name="ServiceEndpointV2" />  
        </Endpoints>
      </Resources>
      ```

    b. Távelérési figyelő létrehozásához használja a következő kiterjesztési módszert.

    ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
    ```

    c. Adjon hozzá egy Assembly attribútumot a távelérési adapterekhez a v1 és v2 figyelő, valamint a v2-ügyfél használatához.
    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2|RemotingListenerVersion.V1, RemotingClientVersion = RemotingClientVersion.V2)]

      ```
2. Frissítse a v1-ügyfelet egy v2-ügyfélre a v2 Client attribútum használatával.
Ez a lépés gondoskodik arról, hogy az ügyfél a v2 stacket használja.
Nincs szükség módosításra az ügyfél-projektben vagy szolgáltatásban. A frissített illesztőfelület-szerelvényrel rendelkező ügyféloldali projektek kiépítése elegendő.

3. Ez a lépés nem kötelező. Használja a v2 figyelő attribútumot, majd frissítse a v2 szolgáltatást.
Ez a lépés gondoskodik arról, hogy a szolgáltatás csak a v2-figyelőn legyen figyelve.

    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2, RemotingClientVersion = RemotingClientVersion.V2)]
    ```


## <a name="use-the-remoting-v2-interface-compatible-stack"></a>A távelérési szolgáltatás (csatolóval kompatibilis) verem használata

 A távelérési szolgáltatás (V2_1) verem a v2 távelérési verem összes funkcióját tartalmazza. A csatoló-verem kompatibilis a távelérés v1-es verziójával, de nem kompatibilis a v2 és a v1 verzióval. Ha a v1-ről V2_1ra szeretne frissíteni anélkül, hogy ez hatással lenne a szolgáltatás rendelkezésre állására, hajtsa végre a következő cikkben ismertetett lépéseket: frissítés v1-ről v2-re


### <a name="use-an-assembly-attribute-to-use-the-remoting-v2-interface-compatible-stack"></a>Egy Assembly attribútum használata a távelérési protokoll (kompatibilis csatoló) verem használatához

Az alábbi lépéseket követve váltson V2_1 veremre.

1. Adjon hozzá egy "ServiceEndpointV2_1" nevű végpont-erőforrást a szolgáltatás jegyzékfájljában.

   ```xml
   <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2_1" />  
    </Endpoints>
   </Resources>
   ```

2. A távelérési bővítmény metódus használatával hozzon létre egy távelérés-figyelőt.

   ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
   ```

3. Adjon hozzá egy [Assembly attribútumot](/dotnet/api/microsoft.servicefabric.services.remoting.fabrictransport.fabrictransportserviceremotingproviderattribute) a távelérési adapterekhez.

   ```csharp
    [assembly:  FabricTransportServiceRemotingProvider(RemotingListenerVersion=  RemotingListenerVersion.V2_1, RemotingClientVersion= RemotingClientVersion.V2_1)]

   ```

Nincs szükség módosításra az ügyfél projektben.
Hozza létre az ügyfél szerelvényét az illesztőfelület-szerelvényben, és győződjön meg arról, hogy az előző szerelvény-attribútum használatban van.

### <a name="use-explicit-remoting-classes-to-create-a-listenerclient-factory-for-the-v2-interface-compatible-version"></a>Használjon explicit távelérési osztályokat figyelő-/ügyfél-előállító létrehozásához a v2 (interfész-kompatibilis) verzióhoz

Kövesse az alábbi lépéseket:

1. Adjon hozzá egy "ServiceEndpointV2_1" nevű végpont-erőforrást a szolgáltatás jegyzékfájljában.

   ```xml
   <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpointV2_1" />  
    </Endpoints>
   </Resources>
   ```

2. Használja a [távelérési v2-figyelőt](/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.runtime.fabrictransportserviceremotinglistener). Az alapértelmezett szolgáltatási végponti erőforrás neve a következő: "ServiceEndpointV2_1." A szolgáltatás jegyzékfájljában definiálni kell.

   ```csharp
   protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return new[]
        {
            new ServiceInstanceListener((c) =>
            {
                var settings = new FabricTransportRemotingListenerSettings();
                settings.UseWrappedMessage = true;
                return new FabricTransportServiceRemotingListener(c, this,settings);

            })
        };
    }
   ```

3. Használja a v2 [Client Factoryt](/dotnet/api/microsoft.servicefabric.services.remoting.v2.fabrictransport.client.fabrictransportserviceremotingclientfactory).
   ```csharp
   var proxyFactory = new ServiceProxyFactory((c) =>
          {
            var settings = new FabricTransportRemotingSettings();
            settings.UseWrappedMessage = true;
            return new FabricTransportServiceRemotingClientFactory(settings);
          });
   ```

## <a name="upgrade-from-remoting-v1-to-remoting-v2-interface-compatible"></a>Frissítés a távelérési kiszolgálóról a távelérési csomagra (kompatibilis interfész)

Ha v1-ről v2-re szeretne frissíteni (kompatibilis illesztőfelület, V2_1), kétlépéses frissítésre van szükség. Kövesse a jelen szakasz lépéseit.

> [!NOTE]
> Ha v1-ről v2-re frissít, győződjön meg arról, hogy a névtér a v2-re `Remoting` frissült. Például: "Microsoft. ServiceFabric. Services. távoli. v2. FabricTransport. Client"
>
>

1. Frissítse a v1 szolgáltatást V2_1 szolgáltatásra a következő attribútum használatával.
Ez a változás biztosítja, hogy a szolgáltatás figyeli a v1-et és a V2_1 figyelőt.

    a. Adjon hozzá egy "ServiceEndpointV2_1" nevű végpont-erőforrást a szolgáltatás jegyzékfájljában.
      ```xml
      <Resources>
        <Endpoints>
          <Endpoint Name="ServiceEndpointV2_1" />  
        </Endpoints>
      </Resources>
      ```

    b. Távelérési figyelő létrehozásához használja a következő kiterjesztési módszert.

    ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return this.CreateServiceRemotingInstanceListeners();
    }
    ```

    c. Adjon hozzá egy Assembly attribútumot a távelérési adapterekhez a v1, V2_1 figyelő és V2_1 ügyfél használatához.
    ```csharp
   [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1 | RemotingListenerVersion.V1, RemotingClientVersion = RemotingClientVersion.V2_1)]

      ```
2. Frissítse a v1-ügyfelet az V2_1-ügyfélre a V2_1 Client attribútum használatával.
Ez a lépés gondoskodik arról, hogy az ügyfél a V2_1 stacket használja.
Nincs szükség módosításra az ügyfél-projektben vagy szolgáltatásban. A frissített illesztőfelület-szerelvényrel rendelkező ügyféloldali projektek kiépítése elegendő.

3. Ez a lépés nem kötelező. Távolítsa el a v1 figyelő verzióját az attribútumból, majd frissítse a v2 szolgáltatást.
Ez a lépés gondoskodik arról, hogy a szolgáltatás csak a v2-figyelőn legyen figyelve.

    ```csharp
    [assembly: FabricTransportServiceRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1, RemotingClientVersion = RemotingClientVersion.V2_1)]
    ```
  
### <a name="use-custom-serialization-with-a-remoting-wrapped-message"></a>Egyéni szerializálás használata távelérési burkolt üzenettel

A becsomagolt távelérésű üzenetek esetében egyetlen burkolt objektumot hozunk létre, amely az összes paramétert egy mezőként jeleníti meg.
Kövesse az alábbi lépéseket:

1. Implementálja a `IServiceRemotingMessageSerializationProvider` felületet az egyéni szerializálás megvalósításának biztosításához.
    Ez a kódrészlet azt mutatja be, hogy a megvalósítás milyen módon fog kinézni.

      ```csharp
      public class ServiceRemotingJsonSerializationProvider : IServiceRemotingMessageSerializationProvider
      {
        public IServiceRemotingMessageBodyFactory CreateMessageBodyFactory()
        {
          return new JsonMessageFactory();
        }

        public IServiceRemotingRequestMessageBodySerializer CreateRequestMessageSerializer(Type serviceInterfaceType, IEnumerable<Type> requestWrappedType, IEnumerable<Type> requestBodyTypes = null)
        {
          return new ServiceRemotingRequestJsonMessageBodySerializer();
        }

        public IServiceRemotingResponseMessageBodySerializer CreateResponseMessageSerializer(Type serviceInterfaceType, IEnumerable<Type> responseWrappedType, IEnumerable<Type> responseBodyTypes = null)
        {
          return new ServiceRemotingResponseJsonMessageBodySerializer();
        }
      }
      ```
      ```csharp
        class JsonMessageFactory : IServiceRemotingMessageBodyFactory
            {

              public IServiceRemotingRequestMessageBody CreateRequest(string interfaceName, string methodName, int numberOfParameters, object wrappedRequestObject)
              {
                return new JsonBody(wrappedRequestObject);
              }

              public IServiceRemotingResponseMessageBody CreateResponse(string interfaceName, string methodName, object wrappedRequestObject)
              {
                return new JsonBody(wrappedRequestObject);
              }
            }
      ```
      ```csharp
      class ServiceRemotingRequestJsonMessageBodySerializer : IServiceRemotingRequestMessageBodySerializer
        {
            private JsonSerializer serializer;

            public ServiceRemotingRequestJsonMessageBodySerializer()
            {
              serializer = JsonSerializer.Create(new JsonSerializerSettings()
              {
                TypeNameHandling = TypeNameHandling.All
                });
              }

              public IOutgoingMessageBody Serialize(IServiceRemotingRequestMessageBody serviceRemotingRequestMessageBody)
             {
               if (serviceRemotingRequestMessageBody == null)
               {
                 return null;
               }          
               using (var writeStream = new MemoryStream())
               {
                 using (var jsonWriter = new JsonTextWriter(new StreamWriter(writeStream)))
                 {
                   serializer.Serialize(jsonWriter, serviceRemotingRequestMessageBody);
                   jsonWriter.Flush();
                   var bytes = writeStream.ToArray();
                   var segment = new ArraySegment<byte>(bytes);
                   var segments = new List<ArraySegment<byte>> { segment };
                   return new OutgoingMessageBody(segments);
                 }
               }
              }

              public IServiceRemotingRequestMessageBody Deserialize(IIncomingMessageBody messageBody)
             {
               using (var sr = new StreamReader(messageBody.GetReceivedBuffer()))
               {
                 using (JsonReader reader = new JsonTextReader(sr))
                 {
                   var ob = serializer.Deserialize<JsonBody>(reader);
                   return ob;
                 }
               }
             }
            }
      ```
      ```csharp
      class ServiceRemotingResponseJsonMessageBodySerializer : IServiceRemotingResponseMessageBodySerializer
       {
         private JsonSerializer serializer;

        public ServiceRemotingResponseJsonMessageBodySerializer()
        {
          serializer = JsonSerializer.Create(new JsonSerializerSettings()
          {
              TypeNameHandling = TypeNameHandling.All
            });
          }

          public IOutgoingMessageBody Serialize(IServiceRemotingResponseMessageBody responseMessageBody)
          {
            if (responseMessageBody == null)
            {
              return null;
            }

            using (var writeStream = new MemoryStream())
            {
              using (var jsonWriter = new JsonTextWriter(new StreamWriter(writeStream)))
              {
                serializer.Serialize(jsonWriter, responseMessageBody);
                jsonWriter.Flush();
                var bytes = writeStream.ToArray();
                var segment = new ArraySegment<byte>(bytes);
                var segments = new List<ArraySegment<byte>> { segment };
                return new OutgoingMessageBody(segments);
              }
            }
          }

          public IServiceRemotingResponseMessageBody Deserialize(IIncomingMessageBody messageBody)
          {

             using (var sr = new StreamReader(messageBody.GetReceivedBuffer()))
             {
               using (var reader = new JsonTextReader(sr))
               {
                 var obj = serializer.Deserialize<JsonBody>(reader);
                 return obj;
               }
             }
           }
       }
    ```
    ```csharp
    class JsonBody : WrappedMessage, IServiceRemotingRequestMessageBody, IServiceRemotingResponseMessageBody
    {
          public JsonBody(object wrapped)
          {
            this.Value = wrapped;
          }

          public void SetParameter(int position, string parameName, object parameter)
          {  //Not Needed if you are using WrappedMessage
            throw new NotImplementedException();
          }

          public object GetParameter(int position, string parameName, Type paramType)
          {
            //Not Needed if you are using WrappedMessage
            throw new NotImplementedException();
          }

          public void Set(object response)
          { //Not Needed if you are using WrappedMessage
            throw new NotImplementedException();
          }

          public object Get(Type paramType)
          {  //Not Needed if you are using WrappedMessage
            throw new NotImplementedException();
          }
    }
    ```

2. Felülbírálja az alapértelmezett szerializálási szolgáltatót a `JsonSerializationProvider` távelérés-figyelőhöz.

   ```csharp
   protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
   {
       return new[]
       {
           new ServiceInstanceListener((c) =>
           {
               return new FabricTransportServiceRemotingListener(c, this,
                   new ServiceRemotingJsonSerializationProvider());
           })
       };
   }
   ```

3. Felülbírálja az alapértelmezett szerializálási szolgáltatót a `JsonSerializationProvider` távelérési ügyfél-előállítóval.

    ```csharp
    var proxyFactory = new ServiceProxyFactory((c) =>
    {
        return new FabricTransportServiceRemotingClientFactory(
        serializationProvider: new ServiceRemotingJsonSerializationProvider());
      });
      ```

## <a name="next-steps"></a>Következő lépések

* [Webes API a Reliable Services OWIN](./service-fabric-reliable-services-communication-aspnetcore.md)
* [Kommunikáció Windows Communication Foundation a Reliable Services](service-fabric-reliable-services-communication-wcf.md)
* [Biztonságos kommunikáció Reliable Services](service-fabric-reliable-services-secure-communication.md)
