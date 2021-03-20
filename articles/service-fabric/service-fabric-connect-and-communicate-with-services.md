---
title: Kapcsolódjon és kommunikáljon az Azure-beli szolgáltatásokkal Service Fabric
description: Megtudhatja, hogyan oldható fel, csatlakozhat és kommunikálhat a szolgáltatásokkal Service Fabric.
ms.topic: conceptual
ms.date: 11/01/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: 11f525eba89dc963deee0ba9a86566361ef644de
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96576298"
---
# <a name="connect-and-communicate-with-services-in-service-fabric"></a>A Service Fabric-szolgáltatásokkal való kapcsolattartás és kommunikáció
Service Fabric a szolgáltatás egy Service Fabric fürtön fut valahol, általában több virtuális gépen elosztva. Áthelyezhető az egyik helyről a másikra, akár a szolgáltatás tulajdonosa, akár a Service Fabric automatikusan. A szolgáltatások nem statikusan vannak társítva egy adott géphez vagy címhez.

A Service Fabric alkalmazások általában számos különböző szolgáltatásból állnak, ahol minden szolgáltatás egy speciális feladatot hajt végre. Ezek a szolgáltatások kommunikálhatnak egymással egy teljes függvény létrehozásához, például egy webalkalmazás különböző részeinek megjelenítéséhez. Vannak olyan ügyfélalkalmazások is, amelyek a szolgáltatásokkal csatlakoznak és kommunikálnak. Ebből a dokumentumból megtudhatja, hogyan állíthatja be a szolgáltatásait a és a között a Service Fabricban.

## <a name="bring-your-own-protocol"></a>Saját protokoll használata
Service Fabric segít a szolgáltatások életciklusának kezelésében, de nem tesz döntéseket a szolgáltatásaival kapcsolatban. Ez magában foglalja a kommunikációt. Ha a szolgáltatást a Service Fabric nyitotta meg, akkor a szolgáltatás lehetőséget ad a bejövő kérelmek végpontjának beállítására, bármilyen protokoll vagy kommunikációs verem használatával. A szolgáltatás minden címzési sémával, például egy URI-val figyeli a normál **IP-címet: portszámot** . Több szolgáltatás-példány vagy-replika is megoszthat egy gazdagépet, ebben az esetben eltérő portokat kell használnia, vagy egy port-megosztási mechanizmust kell használnia, például a http.sys kernel-illesztőprogramot a Windowsban. Mindkét esetben a gazdagép-folyamat minden egyes szolgáltatás-példányának vagy replikájának egyedi címzéssel kell rendelkeznie.

![szolgáltatási végpontok][1]

## <a name="service-discovery-and-resolution"></a>Szolgáltatás felderítése és feloldása
Az elosztott rendszerekben a szolgáltatások az egyik gépről a másikra az idő múlásával átléphetnek. Ez különböző okok miatt fordulhat elő, többek között az erőforrások kiegyensúlyozásához, a frissítésekhez, a feladatátvételekhez vagy a kibővítéshez. Ez azt jelenti, hogy a szolgáltatási végpontok címei változnak, mivel a szolgáltatás eltérő IP-címekkel rendelkező csomópontokra vált, és ha a szolgáltatás dinamikusan kiválasztott portot használ, más portokon is megnyitható.

![Szolgáltatások eloszlása][7]

Service Fabric egy elnevezési szolgáltatás nevű felderítési és feloldási szolgáltatást biztosít. A elnevezési szolgáltatás egy olyan táblát tart fenn, amely elnevezett szolgáltatási példányokat képez le a végponti címekre, amelyeket figyelnek. A Service Fabric összes elnevezett szolgáltatás-példányában egyedi nevek szerepelnek URI-ként, például: `"fabric:/MyApplication/MyService"` . A szolgáltatás neve nem változik a szolgáltatás élettartama során, hanem csak azok a végpont-címek, amelyek megváltoztathatják a szolgáltatások áthelyezésekor. Ez hasonló olyan webhelyekhez, amelyek állandó URL-címekkel rendelkeznek, de az IP-cím változhat. És hasonló a webes DNS-hez, amely feloldja a webhely URL-címeit az IP-címekre, Service Fabric rendelkezik egy regisztrátorral, amely a szolgáltatás nevét a végpont címére képezi le.

![Diagram, amely azt mutatja, hogy Service Fabric rendelkezik egy olyan regisztrálóval, amely a szolgáltatás nevét a végponti címmé képezi.][2]

A szolgáltatások feloldásához és a szolgáltatáshoz való csatlakozáshoz a következő lépések futnak egy hurokban:

* **Megoldás**: szerezze be azt a végpontot, amelyet a szolgáltatás közzétett a elnevezési szolgáltatás.
* **Kapcsolódás**: kapcsolódjon a szolgáltatáshoz az adott végponton használt bármely protokollon keresztül.
* **Újrapróbálkozás**: a kapcsolódási kísérlet tetszőleges számú okból meghiúsulhat, például akkor, ha a szolgáltatás a végponti címek legutóbbi feloldása óta elmozdult. Ebben az esetben a fenti feloldási és csatlakozási lépéseket újra kell próbálni, és ez a ciklus addig ismétlődik, amíg a kapcsolat sikeresen be nem fejeződik.

## <a name="connecting-to-other-services"></a>Csatlakozás más szolgáltatásokhoz
A fürtön belül egymáshoz csatlakozó szolgáltatások általában közvetlenül hozzáférhetnek más szolgáltatások végpontjait, mert a fürt csomópontjai ugyanazon a helyi hálózaton találhatók. Ahhoz, hogy könnyebb legyen csatlakozni a szolgáltatásokhoz, Service Fabric a elnevezési szolgáltatást használó további szolgáltatásokat nyújt. Egy DNS-szolgáltatás és egy fordított proxy szolgáltatás.


### <a name="dns-service"></a>DNS-szolgáltatás
Mivel számos szolgáltatás, különösen a tároló szolgáltatások, rendelkezhetnek meglévő URL-névvel, a szabványos DNS protokoll használatával (a elnevezési szolgáltatás protokoll helyett), különösen az alkalmazás "lift and SHIFT" forgatókönyvekben. Pontosan ez a DNS-szolgáltatás. Lehetővé teszi a DNS-nevek hozzárendelését a szolgáltatás neveként, így a végponti IP-címek feloldását. 

Ahogy az a következő ábrán is látható, a Service Fabric-fürtön futó DNS-szolgáltatás leképezi a DNS-neveket a szolgáltatás neveként, amelyeket a elnevezési szolgáltatás feloldott a végpontok címeinek a kapcsolódáshoz való visszaadásához. A szolgáltatás DNS-neve a létrehozás időpontjában van megadva. 

![Diagram, amely bemutatja, hogy a DNS-szolgáltatás hogyan fut a Service Fabric-fürtön, hogyan képezi le a DNS-neveket a szolgáltatás neveként, amelyeket a elnevezési szolgáltatás feloldott a végpontok címeinek a csatlakozáshoz való visszaküldéséhez.][9]

További információ a DNS szolgáltatás használatáról: [DNS szolgáltatás az Azure-ban Service Fabric](service-fabric-dnsservice.md) cikkben.

### <a name="reverse-proxy-service"></a>Fordított proxy szolgáltatás
A fordított proxy a fürtben olyan HTTP-végpontokat tesz elérhetővé, amelyek a HTTPS-t is beleértve. A fordított proxy nagy mértékben leegyszerűsíti az egyéb szolgáltatások és azok módszereinek meghívását egy adott URI-formátummal, és kezeli a feloldási, csatlakozási és újrapróbálkozási lépéseket, amelyek ahhoz szükségesek, hogy az egyik szolgáltatás kommunikáljon a elnevezési szolgáltatás használatával. Más szóval elrejti a elnevezési szolgáltatás, amikor más szolgáltatásokat hív meg, így egyszerűvé teheti az URL-cím meghívását.

![Diagram, amely bemutatja, hogyan kezeli a fordított proxy a fürtben olyan HTTP-végpontokat, mint a HTTPS.][10]

További információ a fordított proxy szolgáltatás használatáról: [fordított proxy az Azure Service Fabric](service-fabric-reverseproxy.md) cikkben.

## <a name="connections-from-external-clients"></a>Külső ügyfelek kapcsolatai
A fürtön belül egymáshoz csatlakozó szolgáltatások általában közvetlenül hozzáférhetnek más szolgáltatások végpontjait, mert a fürt csomópontjai ugyanazon a helyi hálózaton találhatók. Bizonyos környezetekben azonban előfordulhat, hogy egy fürt egy olyan terheléselosztó mögött található, amely egy korlátozott számú porton keresztül irányítja át a forgalmat. Ezekben az esetekben a szolgáltatások továbbra is kommunikálhatnak egymással, és a elnevezési szolgáltatás használatával oldják fel a címeket, de további lépéseket kell végrehajtani ahhoz, hogy a külső ügyfelek csatlakozhassanak a szolgáltatásokhoz.

## <a name="service-fabric-in-azure"></a>Service Fabric az Azure-ban
Az Azure-beli Service Fabric-fürt egy Azure Load Balancer mögé kerül. A fürtön lévő összes külső forgalomnak át kell haladnia a terheléselosztó használatával. A terheléselosztó automatikusan továbbítja a bejövő forgalmat egy adott porton egy véletlenszerű *csomópontra* , amelynek a portja meg van nyitva. A Azure Load Balancer csak a *csomópontokon* megnyitott portokat ismeri, nem ismeri az egyes *szolgáltatások* által megnyitott portokat.

![Azure Load Balancer és Service Fabric topológia][3]

Ha például a **80**-es porton el szeretné fogadni a külső forgalmat, a következő műveleteket kell konfigurálni:

1. Írjon egy olyan szolgáltatást, amely a 80-es portot figyeli. Konfigurálja a 80-es portot a szolgáltatás ServiceManifest.xml, és nyisson meg egy figyelőt a szolgáltatásban, például egy saját üzemeltetésű webkiszolgálón.

    ```xml
    <Resources>
        <Endpoints>
            <Endpoint Name="WebEndpoint" Protocol="http" Port="80" />
        </Endpoints>
    </Resources>
    ```
    ```csharp
        class HttpCommunicationListener : ICommunicationListener
        {
            ...

            public Task<string> OpenAsync(CancellationToken cancellationToken)
            {
                EndpointResourceDescription endpoint =
                    serviceContext.CodePackageActivationContext.GetEndpoint("WebEndpoint");

                string uriPrefix = $"{endpoint.Protocol}://+:{endpoint.Port}/myapp/";

                this.httpListener = new HttpListener();
                this.httpListener.Prefixes.Add(uriPrefix);
                this.httpListener.Start();

                string publishUri = uriPrefix.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);
                return Task.FromResult(publishUri);
            }

            ...
        }

        class WebService : StatelessService
        {
            ...

            protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
            {
                return new[] { new ServiceInstanceListener(context => new HttpCommunicationListener(context))};
            }

            ...
        }
    ```
    ```java
        class HttpCommunicationlistener implements CommunicationListener {
            ...

            @Override
            public CompletableFuture<String> openAsync(CancellationToken arg0) {
                EndpointResourceDescription endpoint =
                    this.serviceContext.getCodePackageActivationContext().getEndpoint("WebEndpoint");
                try {
                    HttpServer server = com.sun.net.httpserver.HttpServer.create(new InetSocketAddress(endpoint.getPort()), 0);
                    server.start();

                    String publishUri = String.format("http://%s:%d/",
                        this.serviceContext.getNodeContext().getIpAddressOrFQDN(), endpoint.getPort());
                    return CompletableFuture.completedFuture(publishUri);
                } catch (IOException e) {
                    throw new RuntimeException(e);
                }
            }

            ...
        }

        class WebService extends StatelessService {
            ...

            @Override
            protected List<ServiceInstanceListener> createServiceInstanceListeners() {
                <ServiceInstanceListener> listeners = new ArrayList<ServiceInstanceListener>();
                listeners.add(new ServiceInstanceListener((context) -> new HttpCommunicationlistener(context)));
                return listeners;       
            }

            ...
        }
    ```
2. Hozzon létre egy Service Fabric fürtöt az Azure-ban, és az **80** -es portot adja meg egyéni végpontként a szolgáltatást futtató csomópont-típushoz. Ha több csomópont-típussal rendelkezik, beállíthatja az *elhelyezési korlátozást* a szolgáltatásban, hogy az csak az egyéni végponti portot tartalmazó csomópont-típuson fusson.

    ![Port megnyitása csomópont-típuson][4]
3. Miután létrehozta a fürtöt, konfigurálja a Azure Load Balancer a fürt erőforráscsoporthoz, hogy továbbítsa a forgalmat az 80-as porton. Amikor a Azure Portalon keresztül hoz létre fürtöt, a rendszer automatikusan beállítja a konfigurált egyéni végpontok portját.

    ![Képernyőfelvétel: a háttér-port mező kiemelése a terheléselosztási szabályok alatt.][5]
4. A Azure Load Balancer mintavételt használ annak megállapítására, hogy egy adott csomópontra küld-e forgalmat. A mintavétel időszakonként ellenőrzi a végpontokat az egyes csomópontokon annak megállapítása érdekében, hogy a csomópont válaszol-e. Ha a mintavétel a beállított számú alkalommal nem tud választ kapni, a terheléselosztó nem küld forgalmat a csomópontnak. Amikor a Azure Portalon keresztül hoz létre fürtöt, a rendszer automatikusan beállítja a mintavételt a konfigurált egyéni végponti portokhoz.

    ![Forgalom továbbítása a Azure Load Balancerban][8]

Fontos megjegyezni, hogy a Azure Load Balancer és a mintavétel csak a *csomópontokon* található, nem pedig a csomópontokon futó *szolgáltatásokkal* kapcsolatos. A Azure Load Balancer mindig forgalmat küld a mintavételre válaszoló csomópontoknak, ezért ügyelni kell arra, hogy a szolgáltatások elérhetők legyenek azokon a csomópontokon, amelyek képesek válaszolni a mintavételre.

## <a name="reliable-services-built-in-communication-api-options"></a>Reliable Services: beépített kommunikációs API-beállítások
A Reliable Services-keretrendszer számos előre elkészített kommunikációs lehetőséggel rendelkezik. Arról, hogy melyik az Ön számára legmegfelelőbb, a programozási modell, a kommunikációs keretrendszer, valamint a szolgáltatások által írt programozási nyelv típusától függ.

* **Nincs konkrét protokoll:**  Ha nem rendelkezik egy adott kommunikációs keretrendszerrel, de gyorsan szeretne lekérdezni, akkor az ideális megoldás a [szolgáltatás távelérése](service-fabric-reliable-services-communication-remoting.md), amely lehetővé teszi a nagymértékben begépelt távoli eljáráshívás használatát Reliable Services és Reliable Actors. Ez a legegyszerűbb és leggyorsabb módszer a szolgáltatásokkal való kommunikáció megkezdéséhez. A szolgáltatás távelérési szolgáltatása kezeli a szolgáltatási címek, a kapcsolatok, az újrapróbálkozások és a hibakezelés feloldását. Ez mind a C#, mind a Java-alkalmazásokhoz elérhető.
* **Http**: a Language-agnosztikus kommunikációhoz a http egy iparági szabványnak megfelelő választási lehetőséget biztosít, amely számos különböző nyelven elérhető eszközöket és http-kiszolgálókat tartalmaz, amelyeket Service Fabric támogat. A szolgáltatások használhatnak bármely elérhető HTTP-veremet, beleértve a [ASP.net webes API](./service-fabric-reliable-services-communication-aspnetcore.md) -t C#-alkalmazásokhoz. A C# nyelven írt ügyfelek kihasználhatják a `ICommunicationClient` és az `ServicePartitionClient` osztályokat, míg a Javához a `CommunicationClient` és az osztályokat használhatja a szolgáltatás- `FabricServicePartitionClient` [feloldáshoz, a http-kapcsolatokhoz és az újrapróbálkozási ciklusokhoz](service-fabric-reliable-services-communication.md).
* **WCF**: Ha már létezik olyan kód, amely a WCF-t használja kommunikációs keretrendszerként, akkor használhatja a `WcfCommunicationListener` kiszolgálót és az `WcfCommunicationClient` `ServicePartitionClient` osztályt az ügyfélhez. Ez azonban csak Windows-alapú fürtökön elérhető C#-alkalmazásokhoz érhető el. További részletekért tekintse meg [a kommunikációs verem WCF-alapú implementációját](service-fabric-reliable-services-communication-wcf.md)ismertető cikket.

## <a name="using-custom-protocols-and-other-communication-frameworks"></a>Egyéni protokollok és egyéb kommunikációs keretrendszerek használata
A szolgáltatások bármilyen protokollt vagy keretrendszert használhatnak a kommunikációhoz, függetlenül attól, hogy ez egy egyéni bináris protokoll TCP-szoftvercsatornán keresztül, vagy az [azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) vagy az [Azure IoT hub](https://azure.microsoft.com/services/iot-hub/)használatával folyó események. A Service Fabric kommunikációs API-kat biztosít, amelyekkel összekapcsolhatja a kommunikációt. További részletekért tekintse meg ezt a cikket a [megbízható szolgáltatásokkal kapcsolatos kommunikációs modellről](service-fabric-reliable-services-communication.md) .

## <a name="next-steps"></a>Következő lépések
Tudjon meg többet a [Reliable Services kommunikációs modellben](service-fabric-reliable-services-communication.md)elérhető fogalmakról és API-król, majd a [szolgáltatás távelérésének](service-fabric-reliable-services-communication-remoting.md) gyors kezdéséhez, vagy részletesen megtudhatja, hogyan írhat egy kommunikációs figyelőt a [webes API-val a OWIN-alapú](./service-fabric-reliable-services-communication-aspnetcore.md)önkiszolgáló használatával.

[1]: ./media/service-fabric-connect-and-communicate-with-services/serviceendpoints.png
[2]: ./media/service-fabric-connect-and-communicate-with-services/namingservice.png
[3]: ./media/service-fabric-connect-and-communicate-with-services/loadbalancertopology.png
[4]: ./media/service-fabric-connect-and-communicate-with-services/nodeport.png
[5]: ./media/service-fabric-connect-and-communicate-with-services/loadbalancerport.png
[7]: ./media/service-fabric-connect-and-communicate-with-services/distributedservices.png
[8]: ./media/service-fabric-connect-and-communicate-with-services/loadbalancerprobe.png
[9]: ./media/service-fabric-connect-and-communicate-with-services/dns.png
[10]: ./media/service-fabric-reverseproxy/internal-communication.png
