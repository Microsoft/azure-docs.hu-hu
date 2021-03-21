---
title: Azure Service Fabric fordított proxy
description: Használja Service Fabric fordított proxyját a fürtön belüli és kívüli szolgáltatásokhoz való kommunikációhoz.
author: BharatNarasimman
ms.topic: conceptual
ms.date: 11/03/2017
ms.author: bharatn
ms.custom: devx-track-csharp
ms.openlocfilehash: 012a49762596adee39988614ed0c1020cd8bc104
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98791104"
---
# <a name="reverse-proxy-in-azure-service-fabric"></a>Fordított proxy az Azure-ban Service Fabric
Az Azure Service Fabric beépített fordított proxy segít felderíteni a Service Fabric-fürtökön futó és a http-végpontokat használó más szolgáltatásokkal való kommunikációt.

## <a name="microservices-communication-model"></a>A szolgáltatások kommunikációs modellje
A Service Fabric a fürt csomópontjainak egy részhalmazán futnak, és különböző okok miatt áttelepíthetők a csomópontok között. Ennek eredményeképpen a webszolgáltatás-végpontok dinamikusan megváltozhatnak. A következő lépésekkel derítheti fel és kommunikálhat a fürt többi szolgáltatásával:

1. Oldja meg a szolgáltatás helyét a névadási szolgáltatáson keresztül.
2. Kapcsolódjon a szolgáltatáshoz.
3. Az előző lépések becsomagolása egy olyan hurokban, amely megvalósítja a szolgáltatás feloldását, és újrapróbálkozási szabályzatokat alkalmaz a kapcsolódási hibákra

További információ: [kapcsolat és kommunikáció a szolgáltatásokkal](service-fabric-connect-and-communicate-with-services.md).

### <a name="communicating-by-using-the-reverse-proxy"></a>Kommunikáció fordított proxy használatával
A fordított proxy egy olyan szolgáltatás, amely minden csomóponton fut, és a végpontok feloldását, az automatikus újrapróbálkozást és az egyéb csatlakozási hibákat kezeli az ügyfélszolgáltatás nevében. A fordított proxy konfigurálható úgy, hogy különböző házirendeket alkalmazzon, miközben az ügyfél-szolgáltatásokból érkező kéréseket kezeli. A fordított proxy használata lehetővé teszi, hogy az ügyfélszolgáltatás bármilyen ügyféloldali HTTP kommunikációs kódtárat használjon, és nem igényel speciális feloldási és újrapróbálkozási logikát a szolgáltatásban. 

A fordított proxy egy vagy több végpontot tesz elérhetővé a helyi csomóponton olyan ügyfélszolgáltatás számára, amelyet más szolgáltatásoknak küldött kérések küldésére használhat.

![Belső kommunikáció][1]

> [!NOTE]
> **Támogatott platformok**
>
> A fordított proxy Service Fabric jelenleg a következő platformokat támogatja
> * *Windows-fürt*: Windows 8 és újabb, vagy windows Server 2012 és újabb verziók
> * *Linux-fürt*: a fordított proxy jelenleg nem érhető el Linux-fürtökhöz
>

## <a name="reaching-microservices-from-outside-the-cluster"></a>A nem a fürtön kívüli szolgáltatások elérése
Az alapértelmezett külső kommunikációs modell egy olyan opt-in modell, amelyben az egyes szolgáltatások nem érhetők el közvetlenül a külső ügyfelektől. [Azure Load Balancer](../load-balancer/load-balancer-overview.md), amely a hálózat és a külső ügyfelek közötti hálózati határ, végrehajtja a hálózati címfordítást, és külső kéréseket továbbít a belső IP-címekre: Port végpontok. Ahhoz, hogy a szolgáltatás végpontja közvetlenül elérhető legyen a külső ügyfelek számára, először be kell állítania Load Balancert, hogy továbbítsa a forgalmat a fürt által használt összes portra. Emellett a legtöbb webszolgáltatás, különösen az állapot-nyilvántartó szolgáltatás, nem a fürt összes csomópontján él. A szolgáltatások a feladatátvételi csomópontok között válthatnak. Ilyen esetekben Load Balancer nem tudja hatékonyan meghatározni azon replikák célhelyét, amelyeknek továbbítania kell a forgalmat.

### <a name="reaching-microservices-via-the-reverse-proxy-from-outside-the-cluster"></a>A-szolgáltatások elérése a fürtön kívülről fordított proxyn keresztül
Ahelyett, hogy a Load Balancer egy különálló szolgáltatás portját konfigurálja, beállíthatja, hogy csak a fordított proxy portját Load Balancer. Ez a konfiguráció lehetővé teszi, hogy a fürtön kívüli ügyfelek a fürtön belül elérjék a fordított proxyt a további konfigurálás nélkül.

![Külső kommunikáció][0]

> [!WARNING]
> Ha a fordított proxy portját Load Balancerban konfigurálja, a fürtben lévő összes olyan szolgáltatás, amely a HTTP-végpontot teszi elérhetővé, a fürtön kívülről is címezhető. Ez azt jelenti, hogy a belső használatra szánt szolgáltatásokhoz egy meghatározott kártevő felhasználó is felderíthető. Ez potenciálisan súlyos biztonsági réseket jelenthet, amelyeket kihasználhat. például:
>
> * Egy rosszindulatú felhasználó egy olyan belső szolgáltatás ismételt meghívásával indíthat el szolgáltatásmegtagadási támadást, amely nem rendelkezik megfelelően megerősített támadási felülettel.
> * Előfordulhat, hogy egy rosszindulatú felhasználó helytelenül formázott csomagokat kézbesít egy belső szolgáltatásnak, ami nem szándékolt viselkedést eredményez.
> * A belsőnek szánt szolgáltatás olyan magánjellegű vagy bizalmas adatokat ad vissza, amelyeket nem lehet a fürtön kívüli szolgáltatásoknak kitenni, így ez a bizalmas információ rosszindulatú felhasználónak is kikerül. 
>
> Győződjön meg arról, hogy teljes mértékben megértette és enyhítse a fürt és a rajta futó alkalmazások potenciális biztonsági következményeit, mielőtt a fordított proxy nyilvános portját tenné. 
>


## <a name="uri-format-for-addressing-services-by-using-the-reverse-proxy"></a>URI-formátum a szolgáltatások címzéséhez fordított proxy használatával
A fordított proxy egy adott egységes erőforrás-azonosító (URI) formátumot használ annak a szolgáltatás-partíciónak az azonosításához, amelyhez a bejövő kérelmet továbbítani kell:

```
http(s)://<Cluster FQDN | internal IP>:Port/<ServiceInstanceName>/<Suffix path>?PartitionKey=<key>&PartitionKind=<partitionkind>&ListenerName=<listenerName>&TargetReplicaSelector=<targetReplicaSelector>&Timeout=<timeout_in_seconds>
```

* **http (s):** A fordított proxy beállítható úgy, hogy fogadja a HTTP-vagy HTTPS-forgalmat. HTTPS-továbbítás esetén tekintse meg a [biztonságos szolgáltatáshoz való csatlakozást a fordított proxyval](service-fabric-reverseproxy-configure-secure-communication.md) , miután megfordította a proxy beállítását a https-figyeléshez.
* **Fürt teljes tartományneve (FQDN) | belső IP-cím:** Külső ügyfelek esetében beállíthatja a fordított proxyt úgy, hogy az elérhető legyen a fürt tartományán keresztül, például mycluster.eastus.cloudapp.azure.com. A fordított proxy alapértelmezés szerint minden csomóponton fut. A belső forgalom esetében a fordított proxy a localhost vagy a belső csomópontok IP-címén, például a 10.0.0.1 érhető el.
* **Port:** Ez a fordított proxyhoz megadott port, például 19081.
* **Szolgáltatáspéldány neve:** Ez a telepített Service-példány teljesen minősített neve, amelyet a "Fabric:/" nélkül szeretne elérni séma. Például a *Fabric:/SajátPr/myservice/* szolgáltatás eléréséhez használja a *SajátPr/myservice*.

    A szolgáltatás példányának neve megkülönbözteti a kis-és nagybetűket. Ha az URL-címben a szolgáltatási példány neveként egy másik burkolatot használ, a kérések sikertelenek lesznek, 404 (nem található).
* **Utótag elérési útja:** Ez az URL-cím tényleges elérési útja (például *myapi/Values/Add/3*) ahhoz a szolgáltatáshoz, amelyhez csatlakozni szeretne.
* **PartitionKey:** Particionált szolgáltatás esetén ez az elérni kívánt partíció számított partíciós kulcsa. Vegye figyelembe, hogy ez *nem* a Partition ID GUID azonosítója. Ez a paraméter nem szükséges az egyedi partíciós sémát használó szolgáltatásokhoz.
* **PartitionKind:** Ez a szolgáltatás partíciós sémája. Ez lehet "Int64Range" vagy "named". Ez a paraméter nem szükséges az egyedi partíciós sémát használó szolgáltatásokhoz.
* **ListenerName** A szolgáltatáshoz tartozó végpontok {"endpoints": {"Listener1": "Endpoint1", "Listener2": "Endpoint2"...}} formátumúak. Ha a szolgáltatás több végpontot tesz elérhetővé, ez azonosítja azt a végpontot, amelyre az ügyfél kérelmét továbbítani kell. Ez akkor hagyható el, ha a szolgáltatás csak egy figyelővel rendelkezik.
* **TargetReplicaSelector** Ez határozza meg a cél replikájának vagy példányának kiválasztásának módját.
  * Ha a cél szolgáltatás állapot-nyilvántartó, a TargetReplicaSelector a következők egyike lehet: "PrimaryReplica", "RandomSecondaryReplica" vagy "RandomReplica". Ha ez a paraméter nincs megadva, az alapértelmezett érték a "PrimaryReplica".
  * Ha a cél szolgáltatás állapota nem megfelelő, a fordított proxy a szolgáltatás partíciójának véletlenszerűen kiválasztott példányát választja, hogy továbbítsa a kérést.
* **Időtúllépés:**  Ezzel a beállítással adható meg a fordított proxy által a szolgáltatáshoz az ügyfél-kérelem nevében létrehozott HTTP-kérelem időtúllépése. Az alapértelmezett érték 120 másodperc. Ez egy opcionális paraméter.

### <a name="example-usage"></a>Példa a használatra
Vegyük például a *Fabric:/SajátPr/MyService* szolgáltatást, amely a következő URL-címen nyit meg egy http-figyelőt:

```
http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/
```

A szolgáltatás erőforrásai a következők:

* `/index.html`
* `/api/users/<userId>`

Ha a szolgáltatás az egyedi particionálási sémát használja, a *PartitionKey* és a *PartitionKind* lekérdezési karakterlánc paraméterei nem szükségesek, és a szolgáltatás az átjáró használatával érhető el:

* Külsőleg `http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService`
* Belső `http://localhost:19081/MyApp/MyService`

Ha a szolgáltatás az egységes Int64 particionálási sémát használja, a *PartitionKey* és a *PartitionKind* lekérdezési karakterlánc paramétereit kell használni a szolgáltatás egy partíciójának eléréséhez:

* Külsőleg `http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`
* Belső `http://localhost:19081/MyApp/MyService?PartitionKey=3&PartitionKind=Int64Range`

A szolgáltatás által elérhetővé tenni kívánt erőforrások eléréséhez egyszerűen helyezze el az erőforrás elérési útját a szolgáltatás neve után az URL-címen:

* Külsőleg `http://mycluster.eastus.cloudapp.azure.com:19081/MyApp/MyService/index.html?PartitionKey=3&PartitionKind=Int64Range`
* Belső `http://localhost:19081/MyApp/MyService/api/users/6?PartitionKey=3&PartitionKind=Int64Range`

Az átjáró ezután továbbítja ezeket a kéréseket a szolgáltatás URL-címére:

* `http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/index.html`
* `http://10.0.0.5:10592/3f0d39ad-924b-4233-b4a7-02617c6308a6-130834621071472715/api/users/6`

## <a name="special-handling-for-port-sharing-services"></a>Speciális kezelési szolgáltatások a portok megosztásához
A Service Fabric fordított proxy megkísérli újból feloldani a szolgáltatási címeket, és újra próbálkozik a kéréssel, ha egy szolgáltatás nem érhető el. Általában, ha egy szolgáltatás nem érhető el, a szolgáltatási példány vagy a replika egy másik csomópontra került át a normál életciklusa során. Ebben az esetben a fordított proxy hálózati kapcsolódási hibát jelez, amely azt jelzi, hogy egy végpont már nem nyílik meg az eredetileg megoldott címen.

A replikák és a szolgáltatási példányok azonban megoszthatnak egy gazdagépet, és egy http.sys-alapú webkiszolgáló által üzemeltetett portot is megoszthatnak, többek között:

* [System .net. HttpListener](/dotnet/api/system.net.httplistener)
* [ASP.NET Core webfigyelő](https://docs.asp.net/latest/fundamentals/servers.html#weblistener)
* [Katana](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.OwinSelfHost/)

Ebben az esetben valószínű, hogy a webkiszolgáló elérhető a gazdagépen, és válaszol a kérelmekre, de a megoldott szolgáltatási példány vagy replika már nem érhető el a gazdagépen. Ebben az esetben az átjáró HTTP 404 választ fog kapni a webkiszolgálótól. Így a HTTP 404-válasz két különböző jelentésből állhat:

- Case #1: a szolgáltatás címe helyes, de a felhasználó által kért erőforrás nem létezik.
- Case #2: a szolgáltatás címe helytelen, és a felhasználó által kért erőforrás egy másik csomóponton is létezhet.

Az első eset egy normál HTTP 404, amely felhasználói hibának számít. A második esetben azonban a felhasználó olyan erőforrást kért, amely létezik. A fordított proxy nem találta meg, mert maga a szolgáltatás áthelyezte. A fordított proxynak újra fel kell oldania a címeket, majd újra kell próbálkoznia a kéréssel.

A fordított proxynak így különbséget kell tennie a két eset között. Ennek a különbségtételnek a megtétele, hogy a kiszolgáló egy tippet igényel.

* Alapértelmezés szerint a fordított proxy a Case #2t feltételezi, és megkísérli feloldani és kiadni a kérést.
* Ha meg szeretné jelölni a Case #1t a fordított proxyhoz, a szolgáltatásnak a következő HTTP-válasz fejlécet kell visszaadnia:

  `X-ServiceFabric : ResourceNotFound`

Ez a HTTP-válasz fejléce olyan normál HTTP 404-helyzetet jelez, amelyben a kért erőforrás nem létezik, és a fordított proxy nem próbálja meg újból feloldani a szolgáltatási címeket.

## <a name="special-handling-for-services-running-in-containers"></a>A tárolókban futó szolgáltatások speciális feldolgozása

A tárolók belsejében futó szolgáltatások esetében használhatja a környezeti változót a `Fabric_NodeIPOrFQDN` [fordított proxy URL-címének](#uri-format-for-addressing-services-by-using-the-reverse-proxy) létrehozásához a következő kódban látható módon:

```csharp
    var fqdn = Environment.GetEnvironmentVariable("Fabric_NodeIPOrFQDN");
    var serviceUrl = $"http://{fqdn}:19081/DockerSFApp/UserApiContainer";
```
A helyi fürt esetében alapértelmezés szerint a `Fabric_NodeIPOrFQDN` "localhost" értékre van állítva. Indítsa el a helyi fürtöt a `-UseMachineName` paraméterrel, és győződjön meg arról, hogy a tárolók elérik a csomóponton futó fordított proxyt. További információ: [a fejlesztői környezet konfigurálása a tárolók hibakereséséhez](service-fabric-how-to-debug-windows-containers.md#configure-your-developer-environment-to-debug-containers).

Service Fabric Docker-összeállítási tárolókban futó szolgáltatások speciális Docker-compose igényelnek. a YML- *portok szakasz* http: vagy https: Configuration. További információ: [Docker-összeállítás támogatása az Azure Service Fabricban](service-fabric-docker-compose.md).

## <a name="next-steps"></a>Következő lépések
* [Állítsa be és konfigurálja a fordított proxyt egy fürtön](service-fabric-reverseproxy-setup.md).
* [A biztonságos HTTP-szolgáltatás továbbításának beállítása a fordított proxyval](service-fabric-reverseproxy-configure-secure-communication.md)
* [Fordított proxy eseményeinek diagnosztizálása](service-fabric-reverse-proxy-diagnostics.md)
* Tekintse [meg a githubon](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)található, a szolgáltatások közötti http-kommunikáció példáját.
* [Távoli eljáráshívás Reliable Services táveléréssel](service-fabric-reliable-services-communication-remoting.md)
* [OWIN-t használó webes API Reliable Services](./service-fabric-reliable-services-communication-aspnetcore.md)
* [WCF-kommunikáció Reliable Services használatával](service-fabric-reliable-services-communication-wcf.md)

[0]: ./media/service-fabric-reverseproxy/external-communication.png
[1]: ./media/service-fabric-reverseproxy/internal-communication.png
