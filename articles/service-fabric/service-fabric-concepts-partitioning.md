---
title: Service Fabric szolgáltatások particionálása
description: Ismerje meg, hogyan particionálhat Service Fabric állapot nélküli és állapot-nyilvántartó szolgáltatásokat
ms.topic: conceptual
ms.date: 06/30/2017
ms.custom: devx-track-csharp
ms.openlocfilehash: 199ae9d9844149c1931da638633110f717fe0517
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97915895"
---
# <a name="partition-service-fabric-reliable-services"></a>A Service Fabric Reliable Services particionálása
Ez a cikk bevezetést nyújt az Azure Service Fabric megbízható szolgáltatások particionálásának alapvető fogalmait illetően. A particionálás lehetővé teszi az adattárolást a helyi gépeken, hogy az adatok és a számítások együtt is méretezhetők legyenek.

> [!TIP]
> A cikkben található kód [teljes mintája](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/classic/Services/AlphabetPartitions) elérhető a githubon.

## <a name="partitioning"></a>Particionálás
A particionálás nem egyedi a Service Fabric. Valójában a skálázható szolgáltatások létrehozásának egyik alapvető mintázata. Tágabb értelemben azt gondoljuk, hogy a particionálási állapot (adatok) felosztása és a kisebb elérhető egységekre való számítás fogalma a méretezhetőség és a teljesítmény javítása érdekében. A particionálás jól ismert formája az [adatparticionálás][wikipartition], más néven horizontális felskálázás.

### <a name="partition-service-fabric-stateless-services"></a>Service Fabric állapot nélküli szolgáltatások particionálása
Az állapot nélküli szolgáltatások esetében úgy gondolja, hogy egy partíció olyan logikai egység, amely a szolgáltatás egy vagy több példányát tartalmazza. Az 1. ábrán egy állapot nélküli szolgáltatás látható, amely egy partíció használatával öt példányban oszlik meg a fürtben.

![Állapot nélküli szolgáltatás](./media/service-fabric-concepts-partitioning/statelessinstances.png)

Valójában két típusú állapot nélküli szolgáltatási megoldás létezik. Az első egy olyan szolgáltatás, amely külsőleg is megőrzi az állapotát, például egy Azure SQL Databaseban található adatbázisban (például egy olyan webhelyhez, amely a munkamenet adatait és adatait tárolja). A második a csak számítási szolgáltatások (például a számológép vagy a képminiatűr), amely nem kezel állandó állapotot.

Az állapot nélküli szolgáltatások particionálása mindkét esetben nagyon ritka forgatókönyv – a méretezhetőség és a rendelkezésre állás általában további példányok hozzáadásával valósul meg. Az állapot nélküli szolgáltatási példányok esetében egyetlen alkalommal érdemes több partíciót figyelembe venni, ha a speciális útválasztási kérelmeket meg kell felelnie.

Példaként vegyünk például egy olyan esetet, amikor egy bizonyos tartományba tartozó azonosítóval rendelkező felhasználókat csak egy adott szolgáltatási példány számára kell kiszolgálni. Egy másik példa az állapot nélküli szolgáltatás particionálására, ha valóban particionált háttérrel rendelkezik (például SQL Database), és szeretné szabályozni, hogy melyik szolgáltatási példánynak kell írnia az adatbázisba, vagy más előkészítési munkát hajtson végre az állapot nélküli szolgáltatáson belül, amely a háttérben használt particionálási adatokat igényli. Az ilyen típusú forgatókönyvek különböző módokon is megoldhatók, és nem feltétlenül szükségesek a szolgáltatások particionálásához.

Az útmutató hátralévő része az állapot-nyilvántartó szolgáltatásokra koncentrál.

### <a name="partition-service-fabric-stateful-services"></a>Service Fabric állapot-nyilvántartó szolgáltatások particionálása
A Service Fabric megkönnyíti a méretezhető állapot-nyilvántartó szolgáltatások fejlesztését azáltal, hogy az állapot (az adatfeldolgozás) első osztályú módját kínálja. Elméletileg úgy gondolja, hogy egy állapot-nyilvántartó szolgáltatás partíciója olyan méretezési egység, amely nagyon megbízható a fürt csomópontjain elosztott és kiegyensúlyozott [replikák](service-fabric-availability-services.md) révén.

Service Fabric állapot-nyilvántartó szolgáltatások környezetében történő particionálás azt jelenti, hogy egy adott szolgáltatási partíció felelős a szolgáltatás teljes állapotának egy részéért. (Ahogy korábban említettük, a partíció [replikák](service-fabric-availability-services.md)halmaza). Service Fabric nagyszerű dolog, hogy a partíciókat különböző csomópontokon helyezi el. Ez lehetővé teszi a csomópontok erőforrás-korlátjának növekedését. Mivel az adatmennyiség növekedésre szorul, a partíciók növekednek, és Service Fabric a partíciókat a csomópontok között. Ez biztosítja a hardveres erőforrások folyamatos hatékony használatát.

Ha példát szeretne adni, tegyük fel, hogy egy 5 csomópontos fürtöt és egy olyan szolgáltatást indít el, amely úgy van beállítva, hogy 10 partíciót és három replikát céloz meg. Ebben az esetben a Service Fabric kiegyenlíti és elosztja a replikákat a fürtön belül – és két elsődleges [replikával](service-fabric-availability-services.md) fog végződnie.
Ha most ki kell bővíteni a fürtöt 10 csomópontra, Service Fabric az elsődleges [replikákat](service-fabric-availability-services.md) az összes 10 csomóponton át kellene egyenlíteni. Hasonlóképpen, ha visszaméretezi az 5 csomópontot, Service Fabric az összes replikát újra kiegyenlíti az 5 csomópontok között.  

A 2. ábrán a fürt skálázása előtt és után 10 partíció eloszlása látható.

![Állapot-nyilvántartó szolgáltatás](./media/service-fabric-concepts-partitioning/partitions.png)

Ennek eredményeképpen a Kibővítés azért érhető el, mert az ügyfelektől érkező kérések a számítógépek között oszlanak meg, az alkalmazás teljes teljesítménye javult, és az adattömbökhöz való hozzáférés korlátozott.

## <a name="plan-for-partitioning"></a>A particionálás megtervezése
A szolgáltatás megvalósítása előtt mindig tekintse át a kibővíteni kívánt particionálási stratégiát. Különböző módokon, de ezek mindegyike arra koncentrál, hogy mit kell elérnie az alkalmazásnak. A cikk kontextusában tekintsük át a fontosabb szempontokat.

A megfelelő módszer a particionálni kívánt állapot struktúrájának meggondolása, az első lépés.

Vessünk egy egyszerű példát. Ha egy megyei szintű lekérdezéshez szeretne szolgáltatást kiépíteni, létrehozhat egy partíciót a megye minden városához. Ezután az adott városnak megfelelő partícióban tárolhatja a szavazatokat a városban lévő minden személy számára. A 3. ábra a személyek és a város helyét mutatja be.

![Egyszerű partíció](./media/service-fabric-concepts-partitioning/cities.png)

Mivel a városok népessége széles körben változik, előfordulhat, hogy a sok adatmennyiséget (például Seattle) és más, nagyon kevés állapotú partíciókat (például a Kirkland-t) tartalmazó partíciók. Tehát milyen hatással van a partíciók egyenetlen mennyiségű állapotára?

Ha úgy gondolja, hogy a példa ismét látható, egyszerűen láthatja, hogy a Seattle-i szavazatot birtokló partíció nagyobb forgalmat fog kapni, mint a Kirkland. Alapértelmezés szerint a Service Fabric biztosítja, hogy az egyes csomópontokon azonos számú elsődleges és másodlagos replika legyen. Így előfordulhat, hogy a csomópontok olyan replikákat tartanak, amelyek nagyobb forgalmat és másokat is kiszolgálnak, és kevesebb forgalmat szolgálnak ki. Érdemes elkerülni a gyakori és a ritka elérésű helyeket, mint a fürtben.

Ennek elkerüléséhez két dolgot kell tennie a particionálási szempontból:

* Próbálja meg particionálni az állapotot úgy, hogy az egyenletesen legyen elosztva az összes partíció között.
* A szolgáltatás minden replikájának betöltését jelenti. (Erről a cikkről a [metrikák és a betöltések](service-fabric-cluster-resource-manager-metrics.md)című cikkben olvashat bővebben.) Service Fabric lehetőséget biztosít a szolgáltatások által felhasznált terhelés jelentésére, például a memória mennyiségére vagy a rekordok számára. A jelentett mérőszámok alapján Service Fabric észleli, hogy egyes partíciók nagyobb terhelést mutatnak, mint mások, és a replikák több megfelelő csomópontra való áthelyezésével kiegyensúlyozzák a fürtöt, így a teljes csomópont nincs túlterhelve.

Előfordulhat, hogy nem tudja, hogy mennyi adat lesz egy adott partícióban. Ezért az általános javaslat az, hogy mindkettőt – először egy particionálási stratégia bevezetésével, amely egyenletesen osztja el az adategységeket a partíciók között, és másodszor is a bejelentések betöltésével.  Az első módszer megakadályozza a szavazási példában ismertetett helyzeteket, míg a második a hozzáférés vagy a betöltés átmeneti eltéréseit segíti az idő múlásával.

A partíciók megtervezésének egy másik aspektusa, hogy kiválassza a megfelelő számú partíciót a kezdéshez.
Egy Service Fabric perspektívából semmi sem akadályozza meg, hogy a forgatókönyvhöz vártnál nagyobb számú partíciót indítson el.
Valójában, feltételezve, hogy a partíciók maximális száma érvényes megközelítés.

Ritka esetekben előfordulhat, hogy az eredetileg kiválasztottnál több partíciót kell megadnia. Mivel a partíciók száma nem módosítható a tény után, néhány speciális partíciós módszert kell alkalmaznia, például egy új, azonos szolgáltatástípus-példány létrehozását. Emellett olyan ügyféloldali logikát is végre kell hajtania, amely a kérelmeket a megfelelő szolgáltatási példányra irányítja, az ügyfél kódjának megőrzéséhez szükséges ügyféloldali ismeret alapján.

A particionálás megtervezésének másik szempontja a rendelkezésre álló számítógép-erőforrások. Az állapot eléréséhez és tárolásához kötve kell lennie a következőknek:

* Hálózati sávszélesség korlátai
* Rendszermemória korlátai
* Lemezes tárterület korlátai

Tehát mi történik, ha erőforrás-korlátozásokat futtat egy futó fürtben? A válasz az, hogy egyszerűen kibővítheti a fürtöt az új követelmények kielégítése érdekében.

[A kapacitás-tervezési útmutató](service-fabric-capacity-planning.md) útmutatást nyújt annak meghatározásához, hogy a fürt hány csomópontot igényel.

## <a name="get-started-with-partitioning"></a>Ismerkedés a particionálással
Ez a szakasz a szolgáltatás particionálásának első lépéseit ismerteti.

A Service Fabric három partíciós séma közül választhat:

* Tartományon kívüli particionálás (más néven UniformInt64Partition).
* Nevesített particionálás. Az ezt a modellt használó alkalmazások általában a rögzített készleten belüli, gyűjtővel ellátható adattal rendelkeznek. A nevesített partíciós kulcsokként használt adatmezők néhány gyakori példája: régiók, irányítószámok, vevőcsoportok vagy más üzleti határok.
* Egyedi particionálás. Az Egypéldányos partíciókat jellemzően akkor használja a rendszer, ha a szolgáltatás nem igényel további útválasztást. Például az állapot nélküli szolgáltatások alapértelmezés szerint ezt a particionálási sémát használják.

A nevesített és az egyedi particionálási sémák a tartományba tartozó partíciók speciális formái. Alapértelmezés szerint a Service Fabric Visual Studio-sablonjai a tartományon alapuló particionálást használják, mivel ez a leggyakoribb és leghasznosabb. A cikk további része a tartományon alapuló particionálási sémára összpontosít.

### <a name="ranged-partitioning-scheme"></a>Tartományon kívüli particionálási séma
Ezzel a beállítással adható meg az egész szám (alacsony kulcs és magas kulcs azonosítva) és számos partíció (n). N partíciót hoz létre, amelyek mindegyike felelős a partíciós kulcs teljes tartományának nem átfedésben lévő altartományához. Például egy olyan tartományba tartozó particionálási séma, amelynek alacsony kulcsa a 0, a 99 magas kulcsa, a 4. számú partíció pedig négy partíciót hoz létre az alább látható módon.

![Tartomány particionálás](./media/service-fabric-concepts-partitioning/range-partitioning.png)

Gyakori módszer az adathalmazon belüli egyedi kulcs alapján létrehozott kivonat létrehozása. Néhány gyakori példa a kulcsok azonosítására (VIN), egy alkalmazotti AZONOSÍTÓra vagy egy egyedi karakterláncra. Ezt az egyedi kulcsot használva létrehoz egy kivonatoló kódot, amely a kulcs tartományát használja a kulcshoz. Megadhatja az engedélyezett kulcs tartományának alsó és felső határát.

### <a name="select-a-hash-algorithm"></a>Kivonatoló algoritmus kiválasztása
A kivonatolás fontos része a kivonatoló algoritmus kiválasztása. Figyelembe kell venni, hogy a cél a hasonló kulcsok csoportosítása egymáshoz (a helyi megkülönböztető kivonatok), vagy ha a tevékenységet széles körben szét kell osztani az összes partíción (terjesztési kivonat), ami gyakoribb.

A jó elosztási kivonatoló algoritmus jellemzői, hogy könnyű kiszámítani, néhány ütközést tartalmaz, és egyenletesen osztja el a kulcsokat. A hatékony kivonatoló algoritmus jó példája a [FNV-1](https://en.wikipedia.org/wiki/Fowler%E2%80%93Noll%E2%80%93Vo_hash_function) kivonatoló algoritmus.

Egy jó erőforrás az általános kivonatoló kód algoritmusának kiválasztásához a [wikipedia oldal a kivonatoló függvényeknél](https://en.wikipedia.org/wiki/Hash_function).

## <a name="build-a-stateful-service-with-multiple-partitions"></a>Hozzon létre egy állapot-nyilvántartó szolgáltatást több partícióval
Hozzuk létre az első megbízható állapot-nyilvántartó szolgáltatást több partícióval. Ebben a példában egy nagyon egyszerű alkalmazást fog létrehozni, amelyben az összes olyan vezetéknevet tárolni szeretné, amely ugyanazzal a betűvel kezdődik ugyanabban a partícióban.

A kód írása előtt gondolja át a partíciókat és a partíciós kulcsokat. 26 partícióra van szüksége (egy az ábécében szereplő minden betűhöz), de mi a helyzet az alacsony és a magas kulcsokkal?
Ahogy a szó szoros értelmében egy partícióra van szükségünk, az alacsony kulcsként 0, a 25 pedig a magas kulcs, mivel minden betű a saját kulcsa.

> [!NOTE]
> Ez egy egyszerűsített forgatókönyv, ahogy a valóságban a terjesztés egyenetlen lenne. Az "S" vagy "M" betűvel kezdődő vezetéknevek gyakoribbak, mint az "X" vagy "Y" kezdetű nevek.
> 
> 

1. Nyissa meg a **Visual Studio**-  >  **fájl**  >  **új**  >  **projektjét**.
2. Az **új projekt** párbeszédpanelen válassza a Service Fabric alkalmazást.
3. Hívja meg a "AlphabetPartitions" projektet.
4. A **szolgáltatás létrehozása** párbeszédpanelen válassza az **állapot-nyilvántartó** szolgáltatás lehetőséget, és hívja meg az "ABC. Processing" kifejezést.
5. Állítsa be a partíciók számát. Nyissa meg a AlphabetPartitions-projekt ApplicationPackageRoot mappájában található Applicationmanifest.xml fájlt, és frissítse a paramétert a Processing_PartitionCount az alább látható módon.
   
    ```xml
    <Parameter Name="Processing_PartitionCount" DefaultValue="26" />
    ```
   
    A ApplicationManifest.xml StatefulService elemének LowKey és HighKey tulajdonságait is frissítenie kell az alább látható módon.
   
    ```xml
    <Service Name="Processing">
      <StatefulService ServiceTypeName="ProcessingType" TargetReplicaSetSize="[Processing_TargetReplicaSetSize]" MinReplicaSetSize="[Processing_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[Processing_PartitionCount]" LowKey="0" HighKey="25" />
      </StatefulService>
    </Service>
    ```
6. Ahhoz, hogy a szolgáltatás elérhető legyen, nyisson meg egy végpontot a porton a ServiceManifest.xml (a PackageRoot mappában található) végpont elemének hozzáadásával az ábécé. Processing szolgáltatáshoz az alábbi ábrán látható módon:
   
    ```xml
    <Endpoint Name="ProcessingServiceEndpoint" Port="8089" Protocol="http" Type="Internal" />
    ```
   
    A szolgáltatás most úgy van konfigurálva, hogy 26 partíciót tartalmazó belső végpontot hallgasson.
7. Ezután felül kell bírálnia a `CreateServiceReplicaListeners()` feldolgozási osztály metódusát.
   
   > [!NOTE]
   > Ebben a példában feltételezzük, hogy egyszerű HttpCommunicationListener használ. A megbízható szolgáltatásokkal folytatott kommunikációról a [megbízható szolgáltatás kommunikációs modellje](service-fabric-reliable-services-communication.md)című témakörben olvashat bővebben.
   > 
   > 
8. A replika által figyelt URL-cím ajánlott mintája a következő: `{scheme}://{nodeIp}:{port}/{partitionid}/{replicaid}/{guid}` .
    Ezért úgy konfigurálja a kommunikációs figyelőt, hogy a helyes végpontokat figyelje, és ezzel a mintával.
   
    Előfordulhat, hogy a szolgáltatás több replikája ugyanazon a számítógépen található, ezért a címnek egyedinek kell lennie a replikában. Ezért az URL-cím a Partition ID + replika azonosítója. A HttpListener ugyanazon a porton több címet is megfigyelheti, ha az URL-előtag egyedi.
   
    A további GUID-azonosító egy speciális eset, amelyben a másodlagos replikák is figyelik a csak olvasási kérelmeket. Ebben az esetben meg kell győződnie arról, hogy a rendszer új egyedi címeket használ az elsődlegesről másodlagosra történő áttéréskor, hogy az ügyfelek újra feloldják a címeket. a "+" címet használja a rendszer, hogy a replika figyelje az összes elérhető gazdagépet (IP, FQDN, localhost stb.). Az alábbi kód egy példát mutat be.
   
    ```csharp
    protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
    {
         return new[] { new ServiceReplicaListener(context => this.CreateInternalListener(context))};
    }
    private ICommunicationListener CreateInternalListener(ServiceContext context)
    {
   
         EndpointResourceDescription internalEndpoint = context.CodePackageActivationContext.GetEndpoint("ProcessingServiceEndpoint");
         string uriPrefix = String.Format(
                "{0}://+:{1}/{2}/{3}-{4}/",
                internalEndpoint.Protocol,
                internalEndpoint.Port,
                context.PartitionId,
                context.ReplicaOrInstanceId,
                Guid.NewGuid());
   
         string nodeIP = FabricRuntime.GetNodeContext().IPAddressOrFQDN;
   
         string uriPublished = uriPrefix.Replace("+", nodeIP);
         return new HttpCommunicationListener(uriPrefix, uriPublished, this.ProcessInternalRequest);
    }
    ```
   
    Azt is érdemes megjegyezni, hogy a közzétett URL-cím némileg eltér a figyelő URL-előtagtól.
    A figyelő URL-cím a HttpListener számára lett megadva. A közzétett URL-cím a Service Fabric elnevezési szolgáltatás közzétett URL-cím, amelyet a rendszer a szolgáltatás felderítéséhez használ. Az ügyfelek a felderítési szolgáltatáson keresztül kérik ezt a címeket. Az ügyfelek által lekérdezett címnek a csomópont tényleges IP-címének vagy teljes tartománynevének kell lennie ahhoz, hogy csatlakozni lehessen. Ezért a "+" értéket a csomópont IP-címével vagy teljes tartománynevével kell helyettesítenie a fenti ábrán látható módon.
9. Az utolsó lépés a feldolgozási logika hozzáadása a szolgáltatáshoz az alább látható módon.
   
    ```csharp
    private async Task ProcessInternalRequest(HttpListenerContext context, CancellationToken cancelRequest)
    {
        string output = null;
        string user = context.Request.QueryString["lastname"].ToString();
   
        try
        {
            output = await this.AddUserAsync(user);
        }
        catch (Exception ex)
        {
            output = ex.Message;
        }
   
        using (HttpListenerResponse response = context.Response)
        {
            if (output != null)
            {
                byte[] outBytes = Encoding.UTF8.GetBytes(output);
                response.OutputStream.Write(outBytes, 0, outBytes.Length);
            }
        }
    }
    private async Task<string> AddUserAsync(string user)
    {
        IReliableDictionary<String, String> dictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<String, String>>("dictionary");
   
        using (ITransaction tx = this.StateManager.CreateTransaction())
        {
            bool addResult = await dictionary.TryAddAsync(tx, user.ToUpperInvariant(), user);
   
            await tx.CommitAsync();
   
            return String.Format(
                "User {0} {1}",
                user,
                addResult ? "successfully added" : "already exists");
        }
    }
    ```
   
    `ProcessInternalRequest` beolvassa a partíció meghívásához használt lekérdezési karakterlánc paraméter értékeit, és meghívja a `AddUserAsync` LastName hozzáadását a megbízható szótárhoz `dictionary` .
10. Hozzunk létre egy állapot nélküli szolgáltatást a projekthez, amelyből megtudhatja, hogyan hívhat meg egy adott partíciót.
    
    Ez a szolgáltatás egy egyszerű webes felület, amely a LastName lekérdezési karakterlánc paraméterként való fogadására szolgál, meghatározza a partíciós kulcsot, és elküldi az Ábécébe. feldolgozási szolgáltatás feldolgozásra.
11. A **szolgáltatás létrehozása** párbeszédpanelen válassza az **állapot nélküli** szolgáltatás lehetőséget, és hívja meg az "ABC. Web" kifejezést az alábbi ábrán látható módon.
    
    ![Állapot nélküli szolgáltatás képernyőképe](./media/service-fabric-concepts-partitioning/createnewstateless.png).
12. Frissítse a végpont adatait az ABC. WebApi szolgáltatás ServiceManifest.xmljában, hogy az alább látható módon nyisson meg egy portot.
    
    ```xml
    <Endpoint Name="WebApiServiceEndpoint" Protocol="http" Port="8081"/>
    ```
13. A ServiceInstanceListeners gyűjteményét kell visszaadnia a web osztályban. Egy egyszerű HttpCommunicationListener is megvalósíthat.
    
    ```csharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        return new[] {new ServiceInstanceListener(context => this.CreateInputListener(context))};
    }
    private ICommunicationListener CreateInputListener(ServiceContext context)
    {
        // Service instance's URL is the node's IP & desired port
        EndpointResourceDescription inputEndpoint = context.CodePackageActivationContext.GetEndpoint("WebApiServiceEndpoint")
        string uriPrefix = String.Format("{0}://+:{1}/alphabetpartitions/", inputEndpoint.Protocol, inputEndpoint.Port);
        var uriPublished = uriPrefix.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);
        return new HttpCommunicationListener(uriPrefix, uriPublished, this.ProcessInputRequest);
    }
    ```
14. Most végre kell hajtania a feldolgozási logikát. A HttpCommunicationListener meghívja a `ProcessInputRequest` -t, amikor egy kérelem érkezik. Nézzük meg, és adjuk hozzá az alábbi kódot.
    
    ```csharp
    private async Task ProcessInputRequest(HttpListenerContext context, CancellationToken cancelRequest)
    {
        String output = null;
        try
        {
            string lastname = context.Request.QueryString["lastname"];
            char firstLetterOfLastName = lastname.First();
            ServicePartitionKey partitionKey = new ServicePartitionKey(Char.ToUpper(firstLetterOfLastName) - 'A');
    
            ResolvedServicePartition partition = await this.servicePartitionResolver.ResolveAsync(alphabetServiceUri, partitionKey, cancelRequest);
            ResolvedServiceEndpoint ep = partition.GetEndpoint();
    
            JObject addresses = JObject.Parse(ep.Address);
            string primaryReplicaAddress = (string)addresses["Endpoints"].First();
    
            UriBuilder primaryReplicaUriBuilder = new UriBuilder(primaryReplicaAddress);
            primaryReplicaUriBuilder.Query = "lastname=" + lastname;
    
            string result = await this.httpClient.GetStringAsync(primaryReplicaUriBuilder.Uri);
    
            output = String.Format(
                    "Result: {0}. <p>Partition key: '{1}' generated from the first letter '{2}' of input value '{3}'. <br>Processing service partition ID: {4}. <br>Processing service replica address: {5}",
                    result,
                    partitionKey,
                    firstLetterOfLastName,
                    lastname,
                    partition.Info.Id,
                    primaryReplicaAddress);
        }
        catch (Exception ex) { output = ex.Message; }
    
        using (var response = context.Response)
        {
            if (output != null)
            {
                output = output + "added to Partition: " + primaryReplicaAddress;
                byte[] outBytes = Encoding.UTF8.GetBytes(output);
                response.OutputStream.Write(outBytes, 0, outBytes.Length);
            }
        }
    }
    ```
    
    Nézzük végig lépésről lépésre. A kód beolvassa a lekérdezési karakterlánc paraméterének első betűjét `lastname` egy karakterbe. Ezután meghatározza a levél partíciós kulcsát úgy, hogy kivonja a hexadecimális értéket az `A` utolsó név "első betűje" karakterből.
    
    ```csharp
    string lastname = context.Request.QueryString["lastname"];
    char firstLetterOfLastName = lastname.First();
    ServicePartitionKey partitionKey = new ServicePartitionKey(Char.ToUpper(firstLetterOfLastName) - 'A');
    ```
    
    Ne feledje, hogy ebben a példában 26 partíciót használunk partícióként egy partíciós kulccsal.
    Ezt követően a kulcshoz tartozó szolgáltatás-partíciót az `partition` objektum metódusának használatával szerezjük be `ResolveAsync` `servicePartitionResolver` . `servicePartitionResolver` definiálva
    
    ```csharp
    private readonly ServicePartitionResolver servicePartitionResolver = ServicePartitionResolver.GetDefault();
    ```
    
    A `ResolveAsync` metódus a szolgáltatás URI-ját, a partíciós kulcsot és a lemondási tokent paraméterként veszi át. A feldolgozási szolgáltatáshoz tartozó szolgáltatás URI-ja `fabric:/AlphabetPartitions/Processing` . Ezután lekérdezjük a partíció végpontját.
    
    ```csharp
    ResolvedServiceEndpoint ep = partition.GetEndpoint()
    ```
    
    Végezetül felépítjük a végpont URL-címét és a querystring, és meghívjuk a feldolgozási szolgáltatást.
    
    ```csharp
    JObject addresses = JObject.Parse(ep.Address);
    string primaryReplicaAddress = (string)addresses["Endpoints"].First();
    
    UriBuilder primaryReplicaUriBuilder = new UriBuilder(primaryReplicaAddress);
    primaryReplicaUriBuilder.Query = "lastname=" + lastname;
    
    string result = await this.httpClient.GetStringAsync(primaryReplicaUriBuilder.Uri);
    ```
    
    Ha elkészült a feldolgozással, a kimenetet a rendszer visszaírja.
15. Az utolsó lépés a szolgáltatás tesztelése. A Visual Studio alkalmazás-paramétereket használ a helyi és a Felhőbeli üzembe helyezéshez. A szolgáltatás helyi 26 partícióval történő teszteléséhez frissítenie kell a `Local.xml` fájlt a AlphabetPartitions-projekt ApplicationParameters mappájába az alábbi ábrán látható módon:
    
    ```xml
    <Parameters>
      <Parameter Name="Processing_PartitionCount" Value="26" />
      <Parameter Name="WebApi_InstanceCount" Value="1" />
    </Parameters>
    ```
16. Miután befejezte a telepítést, megtekintheti a szolgáltatást és annak összes partícióját a Service Fabric Explorer.
    
    ![Képernyőkép Service Fabric Explorer](./media/service-fabric-concepts-partitioning/sfxpartitions.png)
17. A böngészőben a particionálási logikát a beírásával ellenőrizheti `http://localhost:8081/?lastname=somename` . Látni fogja, hogy az azonos betűvel kezdődő összes vezetéknevet ugyanazon a partíción tárolja a rendszer.
    
    ![Böngésző képernyőképe](./media/service-fabric-concepts-partitioning/samplerunning.png)

A cikkben használt kód teljes megoldása itt érhető el: https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/classic/Services/AlphabetPartitions .

## <a name="next-steps"></a>Következő lépések
További információ a Service Fabric szolgáltatásairól:

* [A Service Fabric-szolgáltatásokkal való kapcsolattartás és kommunikáció](service-fabric-connect-and-communicate-with-services.md)
* [Service Fabric szolgáltatások rendelkezésre állása](service-fabric-availability-services.md)
* [Service Fabric szolgáltatások skálázhatósága](service-fabric-concepts-scalability.md)

[wikipartition]: https://en.wikipedia.org/wiki/Partition_(database)

[1]: ./media/service-fabric-create-your-first-application-in-visual-studio/new-project-dialog-2.png
