---
title: Reliable Services kommunikáció áttekintése
description: A Reliable Services kommunikációs modell áttekintése, beleértve a figyelők megnyitását, a végpontok feloldását és a szolgáltatások közötti kommunikációt.
ms.topic: conceptual
ms.date: 11/01/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: 3436d29446e963faea9bda47f5a5247b7de7d859
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97912614"
---
# <a name="how-to-use-the-reliable-services-communication-apis"></a>A Reliable Services kommunikációs API-k használata
Az Azure Service Fabric platformként teljes mértékben független a szolgáltatások közötti kommunikációtól. Minden protokoll és verem elfogadható, UDP-ről HTTP-re. A szolgáltatás fejlesztője dönti el, hogyan kommunikálnak a szolgáltatások. A Reliable Services alkalmazás-keretrendszer beépített kommunikációs veremeket és API-kat biztosít, amelyeket az egyéni kommunikációs összetevők kiépítéséhez használhat.

## <a name="set-up-service-communication"></a>A szolgáltatással való kommunikáció beállítása
A Reliable Services API egy egyszerű felületet használ a szolgáltatásokkal való kommunikációhoz. Ha meg szeretné nyitni a szolgáltatáshoz tartozó végpontot, egyszerűen implementálja ezt a felületet:

```csharp

public interface ICommunicationListener
{
    Task<string> OpenAsync(CancellationToken cancellationToken);

    Task CloseAsync(CancellationToken cancellationToken);

    void Abort();
}

```

```java
public interface CommunicationListener {
    CompletableFuture<String> openAsync(CancellationToken cancellationToken);

    CompletableFuture<?> closeAsync(CancellationToken cancellationToken);

    void abort();
}
```

Ezután hozzáadhatja a kommunikációs figyelő megvalósítását úgy, hogy egy szolgáltatás-alapú osztály metódusának felülbírálását adja vissza.

Állapot nélküli szolgáltatások esetén:

```csharp
public class MyStatelessService : StatelessService
{
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        ...
    }
    ...
}
```
```java
public class MyStatelessService extends StatelessService {

    @Override
    protected List<ServiceInstanceListener> createServiceInstanceListeners() {
        ...
    }
    ...
}
```

Állapot-nyilvántartó szolgáltatások esetén:

```java
    @Override
    protected List<ServiceReplicaListener> createServiceReplicaListeners() {
        ...
    }
    ...
```

```csharp
public class MyStatefulService : StatefulService
{
    protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
    {
        ...
    }
    ...
}
```

Mindkét esetben a figyelők gyűjteményét kell visszaadnia. Ez lehetővé teszi a szolgáltatás számára, hogy több figyelő használatával több végponton figyelje a különböző protokollokat. Előfordulhat például, hogy egy HTTP-figyelővel és egy különálló WebSocket-figyelővel rendelkezik. Minden figyelő kap egy nevet, és az eredményül kapott *név: cím* párok JSON-objektumként jelennek meg, amikor egy ügyfél egy szolgáltatási példány vagy egy partíció figyelési címeit kéri le.

Állapot nélküli szolgáltatás esetén a felülbírálás a ServiceInstanceListeners gyűjteményét adja vissza. A `ServiceInstanceListener` tartalmaz egy függvényt, amely létrehoz egy `ICommunicationListener(C#) / CommunicationListener(Java)` nevet, és megadja a nevét. Az állapot-nyilvántartó szolgáltatások esetében a felülbírálás a ServiceReplicaListeners gyűjteményét adja vissza. Ez némileg eltér az állapot nélküli munkatársaitól, mert az a `ServiceReplicaListener` lehetőség, hogy egy `ICommunicationListener` másodlagos replikán is megnyitható. Nem csupán több kommunikációs figyelőt használhat egy szolgáltatásban, de azt is megadhatja, hogy mely figyelők fogadják el a kérelmeket a másodlagos replikák esetében, és melyek csak az elsődleges replikákat figyelik.

Rendelkezhet például olyan ServiceRemotingListener, amely csak az elsődleges replikák esetében fogad RPC-hívásokat, valamint egy második, egyéni figyelőt, amely olvasási kéréseket küld a másodlagos replikák számára HTTP-n keresztül:

```csharp
protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new[]
    {
        new ServiceReplicaListener(context =>
            new MyCustomHttpListener(context),
            "HTTPReadonlyEndpoint",
            true),

        new ServiceReplicaListener(context =>
            this.CreateServiceRemotingListener(context),
            "rpcPrimaryEndpoint",
            false)
    };
}
```

> [!NOTE]
> Ha több figyelőt hoz létre egy szolgáltatáshoz, minden figyelőnek egyedi **nevet kell adni** .
>
>

Végezetül írja le a szolgáltatáshoz a végpontok szakaszban található [szolgáltatáshoz](service-fabric-application-and-service-manifests.md) szükséges végpontokat.

```xml
<Resources>
    <Endpoints>
      <Endpoint Name="WebServiceEndpoint" Protocol="http" Port="80" />
      <Endpoint Name="OtherServiceEndpoint" Protocol="tcp" Port="8505" />
    <Endpoints>
</Resources>

```

A kommunikációs figyelő hozzáférhet a számára lefoglalt végponti erőforrásokhoz a `CodePackageActivationContext` alkalmazásból `ServiceContext` . A figyelő ezután megkezdheti a kérések figyelését a megnyitásakor.

```csharp
var codePackageActivationContext = serviceContext.CodePackageActivationContext;
var port = codePackageActivationContext.GetEndpoint("ServiceEndpoint").Port;

```
```java
CodePackageActivationContext codePackageActivationContext = serviceContext.getCodePackageActivationContext();
int port = codePackageActivationContext.getEndpoint("ServiceEndpoint").getPort();

```

> [!NOTE]
> A végponti erőforrások a teljes szervizcsomag esetében közösek, és Service Fabric a szervizcsomag aktiválásakor vannak lefoglalva. Ugyanazon a ServiceHost több szolgáltatás replikája is megoszthatja ugyanazt a portot. Ez azt jelenti, hogy a kommunikációs figyelőnek támogatnia kell a portok megosztását. Ennek az az ajánlott módja, hogy a kommunikációs figyelő használja a partíció AZONOSÍTÓját és a replika/példány AZONOSÍTÓját, amikor létrehozza a figyelési címeket.
>
>

### <a name="service-address-registration"></a>Szolgáltatási címek regisztrálása
A *elnevezési szolgáltatás* nevű rendszerszolgáltatás Service Fabric-fürtökön fut. A elnevezési szolgáltatás a szolgáltatások regisztrátora, valamint azok címei, amelyekben a szolgáltatás minden példánya vagy replikája figyeli a szolgáltatást. Ha `OpenAsync(C#) / openAsync(Java)` egy Befejezés metódusa `ICommunicationListener(C#) / CommunicationListener(Java)` befejeződik, a visszatérési értéke a elnevezési szolgáltatásban lesz regisztrálva. A elnevezési szolgáltatás közzétett visszatérési értéke egy olyan karakterlánc, amelynek értéke bármi lehet. Ez a karakterlánc azt látja, hogy mely ügyfelek látják a szolgáltatáshoz tartozó címek megadását a elnevezési szolgáltatás.

```csharp
public Task<string> OpenAsync(CancellationToken cancellationToken)
{
    EndpointResourceDescription serviceEndpoint = serviceContext.CodePackageActivationContext.GetEndpoint("ServiceEndpoint");
    int port = serviceEndpoint.Port;

    this.listeningAddress = string.Format(
                CultureInfo.InvariantCulture,
                "http://+:{0}/",
                port);

    this.publishAddress = this.listeningAddress.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);

    this.webApp = WebApp.Start(this.listeningAddress, appBuilder => this.startup.Invoke(appBuilder));

    // the string returned here will be published in the Naming Service.
    return Task.FromResult(this.publishAddress);
}
```
```java
public CompletableFuture<String> openAsync(CancellationToken cancellationToken)
{
    EndpointResourceDescription serviceEndpoint = serviceContext.getCodePackageActivationContext.getEndpoint("ServiceEndpoint");
    int port = serviceEndpoint.getPort();

    this.publishAddress = String.format("http://%s:%d/", FabricRuntime.getNodeContext().getIpAddressOrFQDN(), port);

    this.webApp = new WebApp(port);
    this.webApp.start();

    /* the string returned here will be published in the Naming Service.
     */
    return CompletableFuture.completedFuture(this.publishAddress);
}
```

A Service Fabric olyan API-kat biztosít, amelyek lehetővé teszik az ügyfelek és más szolgáltatások számára, hogy ezt a nevet a szolgáltatásnév alapján kérjék. Ez azért fontos, mert a szolgáltatás címe nem statikus. A szolgáltatások a fürtön vannak áthelyezve erőforrás-kiegyenlítő és rendelkezésre állási célokra. Ez az a mechanizmus, amely lehetővé teszi, hogy az ügyfelek feloldják a szolgáltatás figyelési címeit.

> [!NOTE]
> A kommunikációs figyelő megírásának teljes körű bemutatása: [Service Fabric webes API-szolgáltatások a C# OWIN](./service-fabric-reliable-services-communication-aspnetcore.md) , míg a Java esetében a saját http-kiszolgálói implementációját is megírhatja, lásd: EchoServer alkalmazás példája https://github.com/Azure-Samples/service-fabric-java-getting-started .
>
>

## <a name="communicating-with-a-service"></a>Kommunikáció a szolgáltatással
A Reliable Services API a következő könyvtárakat biztosítja a szolgáltatásokkal kommunikáló ügyfelek írásához.

### <a name="service-endpoint-resolution"></a>Szolgáltatás végpontjának feloldása
A szolgáltatással folytatott kommunikáció első lépéseként meg kell oldani annak a szolgáltatásnak a végponti címeit, amelynek a használatával szeretne kommunikálni. A `ServicePartitionResolver(C#) / FabricServicePartitionResolver(Java)` segédprogram osztály egy alapszintű primitív, amely segít az ügyfeleknek a szolgáltatás végpontjának meghatározásában futásidőben. Service Fabric terminológiában a szolgáltatás végpontjának meghatározásának folyamatát a *szolgáltatási végpont feloldásának* nevezzük.

A fürtön belüli szolgáltatásokhoz való kapcsolódáshoz a ServicePartitionResolver az alapértelmezett beállításokkal hozhatók létre. A legtöbb esetben ez az ajánlott használat:

```csharp
ServicePartitionResolver resolver = ServicePartitionResolver.GetDefault();
```
```java
FabricServicePartitionResolver resolver = FabricServicePartitionResolver.getDefault();
```

Ha egy másik fürtben lévő szolgáltatásokhoz szeretne csatlakozni, a ServicePartitionResolver létrehozhatók egy fürt átjáró-végpontokkal. Vegye figyelembe, hogy az átjáró-végpontok csak az ugyanahhoz a fürthöz való csatlakozáshoz használható végpontok. Például:

```csharp
ServicePartitionResolver resolver = new  ServicePartitionResolver("mycluster.cloudapp.azure.com:19000", "mycluster.cloudapp.azure.com:19001");
```
```java
FabricServicePartitionResolver resolver = new  FabricServicePartitionResolver("mycluster.cloudapp.azure.com:19000", "mycluster.cloudapp.azure.com:19001");
```

`ServicePartitionResolver`Azt is megteheti, hogy egy függvényt `FabricClient` hoz létre belső használatra:

```csharp
public delegate FabricClient CreateFabricClientDelegate();
```
```java
public FabricServicePartitionResolver(CreateFabricClient createFabricClient) {
...
}

public interface CreateFabricClient {
    public FabricClient getFabricClient();
}
```

`FabricClient` az a objektum, amely az Service Fabric-fürttel való kommunikációra szolgál a fürt különböző felügyeleti műveleteihez. Ez akkor lehet hasznos, ha jobban meg szeretné határozni, hogy a szolgáltatás partíció-feloldása hogyan kommunikál a fürttel. `FabricClient` belső gyorsítótárazást végez, és általában költséges a létrehozása, ezért fontos, hogy a `FabricClient` példányokat a lehető legnagyobb mértékben újra lehessen használni.

```csharp
ServicePartitionResolver resolver = new  ServicePartitionResolver(() => CreateMyFabricClient());
```
```java
FabricServicePartitionResolver resolver = new  FabricServicePartitionResolver(() -> new CreateFabricClientImpl());
```

Ezt követően a rendszer lekéri a szolgáltatás vagy a particionált szolgáltatások szolgáltatás-partíciójának a címeit.

```csharp
ServicePartitionResolver resolver = ServicePartitionResolver.GetDefault();

ResolvedServicePartition partition =
    await resolver.ResolveAsync(new Uri("fabric:/MyApp/MyService"), new ServicePartitionKey(), cancellationToken);
```
```java
FabricServicePartitionResolver resolver = FabricServicePartitionResolver.getDefault();

CompletableFuture<ResolvedServicePartition> partition =
    resolver.resolveAsync(new URI("fabric:/MyApp/MyService"), new ServicePartitionKey());
```

A szolgáltatási címek könnyen feloldhatók egy ServicePartitionResolver használatával, de a megoldott címek megfelelő használatának biztosításához több munka szükséges. Az ügyfélnek meg kell vizsgálnia, hogy a kapcsolódási kísérlet átmeneti hiba miatt meghiúsult-e, és újrapróbálkozhat-e (például a szolgáltatás áthelyezve vagy átmenetileg nem érhető el), vagy állandó hibát (például a szolgáltatást törölték, vagy a kért erőforrás már nem létezik). A szolgáltatási példányok vagy replikák egyszerre több okból is mozoghatnak a csomópontról a csomópontra. Előfordulhat, hogy a ServicePartitionResolver-n keresztül feloldott szolgáltatási címnek elavultnak kell lennie, amikor az ügyfél kódja megpróbál csatlakozni. Ebben az esetben az ügyfélnek újra fel kell oldania a címeket. Az előző megadása `ResolvedServicePartition` azt jelzi, hogy a feloldónak újra kell próbálkoznia, ahelyett, hogy egyszerűen lekéri a gyorsítótárazott címeket.

Az ügyfél kódjának általában nem kell közvetlenül a ServicePartitionResolver működnie. A rendszer a Reliable Services API-ban hozza létre és továbbítja a kommunikációs ügyfél-gyáraknak. A gyárak belsőleg használják a feloldót egy olyan ügyfél-objektum létrehozásához, amely a szolgáltatásokkal való kommunikációhoz használható.

### <a name="communication-clients-and-factories"></a>Kommunikációs ügyfelek és gyárak
A kommunikációs gyár könyvtára egy tipikus hibatűrő újrapróbálkozási mintát valósít meg, amely megkönnyíti a kapcsolatok megoldott szolgáltatási végpontokhoz való újrapróbálkozását. A gyári függvénytár biztosítja az újrapróbálkozási mechanizmust a hibák megadásakor.

`ICommunicationClientFactory(C#) / CommunicationClientFactory(Java)` meghatározza a kommunikációs ügyfél-előállító által megvalósított alapszintű felületet, amely a Service Fabric szolgáltatással kommunikáló ügyfeleket állít elő. A CommunicationClientFactory megvalósítása a Service Fabric szolgáltatás által használt kommunikációs veremtől függ, amelyben az ügyfél kommunikálni szeretne. A Reliable Services API biztosítja a `CommunicationClientFactoryBase<TCommunicationClient>` . Ez a CommunicationClientFactory felület alapszintű megvalósítását biztosítja, és az összes kommunikációs verem esetében közös feladatokat hajt végre. (Ezek a feladatok egy ServicePartitionResolver használatával határozzák meg a szolgáltatási végpontot). Az ügyfelek általában az absztrakt CommunicationClientFactoryBase osztályt alkalmazzák a kommunikációs veremre jellemző logikák kezelésére.

A kommunikációs ügyfél csak egy címeket kap, és azt használja a szolgáltatáshoz való kapcsolódáshoz. Az ügyfél bármilyen protokollt igénybe vehet.

```csharp
public class MyCommunicationClient : ICommunicationClient
{
    public ResolvedServiceEndpoint Endpoint { get; set; }

    public string ListenerName { get; set; }

    public ResolvedServicePartition ResolvedServicePartition { get; set; }
}
```
```java
public class MyCommunicationClient implements CommunicationClient {

    private ResolvedServicePartition resolvedServicePartition;
    private String listenerName;
    private ResolvedServiceEndpoint endPoint;

    /*
     * Getters and Setters
     */
}
```

Az ügyfél-előállító elsődleges feladata a kommunikációs ügyfelek létrehozása. Azon ügyfelek esetében, amelyek nem tartanak fenn állandó kapcsolatokat, például HTTP-ügyfelet, csak a-ügyfelet kell létrehoznia és visszaadnia. Az állandó kapcsolatokat (például egyes bináris protokollokat) fenntartó protokollokat a gyári ellenőrzésnek is meg kell határoznia `ValidateClient(string endpoint, MyCommunicationClient client)` annak megállapításához, hogy a kapcsolódást újra kell-e létrehozni.  

```csharp
public class MyCommunicationClientFactory : CommunicationClientFactoryBase<MyCommunicationClient>
{
    protected override void AbortClient(MyCommunicationClient client)
    {
    }

    protected override Task<MyCommunicationClient> CreateClientAsync(string endpoint, CancellationToken cancellationToken)
    {
    }

    protected override bool ValidateClient(MyCommunicationClient clientChannel)
    {
    }

    protected override bool ValidateClient(string endpoint, MyCommunicationClient client)
    {
    }
}
```
```java
public class MyCommunicationClientFactory extends CommunicationClientFactoryBase<MyCommunicationClient> {

    @Override
    protected boolean validateClient(MyCommunicationClient clientChannel) {
    }

    @Override
    protected boolean validateClient(String endpoint, MyCommunicationClient client) {
    }

    @Override
    protected CompletableFuture<MyCommunicationClient> createClientAsync(String endpoint) {
    }

    @Override
    protected void abortClient(MyCommunicationClient client) {
    }
}
```

Végezetül, egy kivétel kezelője feladata annak megállapítása, hogy milyen műveletet kell végrehajtani a kivétel bekövetkezésekor. A kivételek az **újrapróbálható** és **nem újrapróbálható** értékekre vannak kategorizálva.

* A **nem újrapróbálható** kivételek egyszerűen visszakerülnek a hívóba.
* az **újrapróbálkozást** lehetővé tevő kivételek további kategorizálva **átmeneti** és **nem átmeneti** jellegűek.
  * Az **átmeneti** kivételek azok, amelyek egyszerűen újra feloldhatók a szolgáltatási végponti cím feloldása nélkül. Ezek közé tartoznak az átmeneti hálózati problémák vagy a szolgáltatás-végpontot jelző, nem létező szolgáltatási hibák.
  * A **nem átmeneti** kivételek azok, amelyekhez a szolgáltatás-végponti címnek újbóli feloldására van szükség. Ezek közé tartoznak azok a kivételek, amelyek jelzik, hogy a szolgáltatási végpont nem érhető el, jelezve, hogy a szolgáltatás egy másik csomópontra került.

Az egy `TryHandleException` adott kivételre vonatkozó döntést tesz. Ha **nem tudja** , hogy milyen döntéseket kell hoznia egy kivételről, **Hamis értéket** kell visszaadnia. Ha **tudja** , hogy milyen döntést kell hoznia, az eredményt ennek megfelelően kell beállítania, és **igaz** értéket kell visszaadnia.

```csharp
class MyExceptionHandler : IExceptionHandler
{
    public bool TryHandleException(ExceptionInformation exceptionInformation, OperationRetrySettings retrySettings, out ExceptionHandlingResult result)
    {
        // if exceptionInformation.Exception is known and is transient (can be retried without re-resolving)
        result = new ExceptionHandlingRetryResult(exceptionInformation.Exception, true, retrySettings, retrySettings.DefaultMaxRetryCount);
        return true;


        // if exceptionInformation.Exception is known and is not transient (indicates a new service endpoint address must be resolved)
        result = new ExceptionHandlingRetryResult(exceptionInformation.Exception, false, retrySettings, retrySettings.DefaultMaxRetryCount);
        return true;

        // if exceptionInformation.Exception is unknown (let the next IExceptionHandler attempt to handle it)
        result = null;
        return false;
    }
}
```
```java
public class MyExceptionHandler implements ExceptionHandler {

    @Override
    public ExceptionHandlingResult handleException(ExceptionInformation exceptionInformation, OperationRetrySettings retrySettings) {

        /* if exceptionInformation.getException() is known and is transient (can be retried without re-resolving)
         */
        result = new ExceptionHandlingRetryResult(exceptionInformation.getException(), true, retrySettings, retrySettings.getDefaultMaxRetryCount());
        return true;


        /* if exceptionInformation.getException() is known and is not transient (indicates a new service endpoint address must be resolved)
         */
        result = new ExceptionHandlingRetryResult(exceptionInformation.getException(), false, retrySettings, retrySettings.getDefaultMaxRetryCount());
        return true;

        /* if exceptionInformation.getException() is unknown (let the next ExceptionHandler attempt to handle it)
         */
        result = null;
        return false;

    }
}
```
### <a name="putting-it-all-together"></a>Végső összeállítás
Egy `ICommunicationClient(C#) / CommunicationClient(Java)` , a `ICommunicationClientFactory(C#) / CommunicationClientFactory(Java)` és `IExceptionHandler(C#) / ExceptionHandler(Java)` egy kommunikációs protokoll köré épülő megoldással `ServicePartitionClient(C#) / FabricServicePartitionClient(Java)` együtt csomagolja össze az összeset, és biztosítja a hibajavítási és szolgáltatás-partíciós címek feloldási hurkot ezen összetevők körül.

```csharp
private MyCommunicationClientFactory myCommunicationClientFactory;
private Uri myServiceUri;

var myServicePartitionClient = new ServicePartitionClient<MyCommunicationClient>(
    this.myCommunicationClientFactory,
    this.myServiceUri,
    myPartitionKey);

var result = await myServicePartitionClient.InvokeWithRetryAsync(async (client) =>
   {
      // Communicate with the service using the client.
   },
   CancellationToken.None);

```
```java
private MyCommunicationClientFactory myCommunicationClientFactory;
private URI myServiceUri;

FabricServicePartitionClient myServicePartitionClient = new FabricServicePartitionClient<MyCommunicationClient>(
    this.myCommunicationClientFactory,
    this.myServiceUri,
    myPartitionKey);

CompletableFuture<?> result = myServicePartitionClient.invokeWithRetryAsync(client -> {
      /* Communicate with the service using the client.
       */
   });

```

## <a name="next-steps"></a>Következő lépések
* [ASP.NET Core a Reliable Services](service-fabric-reliable-services-communication-aspnetcore.md)
* [Távoli eljáráshívás Reliable Services táveléréssel](service-fabric-reliable-services-communication-remoting.md)
* [WCF-kommunikáció Reliable Services használatával](service-fabric-reliable-services-communication-wcf.md)
