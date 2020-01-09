---
title: Azure Service Fabric DNS-szolgáltatás
description: Használja a Service Fabric DNS-szolgáltatását, hogy a rendszer a fürtön belül felfedezzék a szolgáltatásait.
ms.topic: conceptual
ms.date: 7/20/2018
ms.openlocfilehash: 317aa81238ec7a0dc24b69b1d00568901b9bc34f
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75458027"
---
# <a name="dns-service-in-azure-service-fabric"></a>DNS szolgáltatás az Azure-ban Service Fabric
A DNS-szolgáltatás egy opcionális rendszerszolgáltatás, amelyet engedélyezheti a fürtben más szolgáltatások felderítéséhez a DNS protokoll használatával. 

A már meglévő URL-címeken keresztül számos szolgáltatás, különösen a tároló szolgáltatások is elérhetők. Az Service Fabric elnevezési szolgáltatás protokoll helyett a szabványos DNS protokoll használatával tudja megoldani ezeket a szolgáltatásokat. A DNS szolgáltatás lehetővé teszi a DNS-nevek hozzárendelését a szolgáltatás neveként, így a végponti IP-címek feloldását. Az ilyen funkciók megőrzik a tároló szolgáltatások hordozhatóságát a különböző platformokon, és megkönnyítik a "lift és SHIFT" forgatókönyvek használatát azáltal, hogy a meglévő szolgáltatási URL-címeket nem kell a kód átírása helyett használni a elnevezési szolgáltatás kihasználása érdekében. 

A DNS-szolgáltatás leképezi a DNS-neveket a szolgáltatás neveként, amelyet a elnevezési szolgáltatás a szolgáltatás végpontjának visszaküldéséhez feloldott. A szolgáltatás DNS-neve a létrehozás időpontjában van megadva. Az alábbi ábra bemutatja, hogyan működik a DNS-szolgáltatás az állapot nélküli szolgáltatások esetében.

![szolgáltatási végpontok](./media/service-fabric-dnsservice/stateless-dns.png)

A Service Fabric 6,3-es verziójától kezdve a Service Fabric DNS protokoll ki lett bővítve, hogy tartalmazza a particionált állapot-nyilvántartó szolgáltatások kezelésére szolgáló sémát. Ezek a bővítmények feloldják az adott partíciós IP-címeket az állapot-nyilvántartó DNS-név és a partíció neve együttes használatával. Mindhárom particionálási séma támogatott:

- Nevesített particionálás
- Tartományon kívüli particionálás
- Egyedi particionálás

Az alábbi ábra bemutatja, hogyan működik a DNS-szolgáltatás particionált állapot-nyilvántartó szolgáltatásokhoz.

![állapot-nyilvántartó szolgáltatási végpontok](./media/service-fabric-dnsservice/stateful-dns.png)

A DNS-szolgáltatás nem támogatja a dinamikus portokat. A dinamikus portokon elérhetővé tett szolgáltatások megoldásához használja a [fordított proxy szolgáltatást](./service-fabric-reverseproxy.md).

## <a name="enabling-the-dns-service"></a>A DNS-szolgáltatás engedélyezése
> [!NOTE]
> A Service Fabric szolgáltatások DNS szolgáltatása még nem támogatott Linux rendszeren.

Amikor a portál használatával hoz létre fürtöt, a DNS szolgáltatás alapértelmezés szerint engedélyezve van a **fürt konfigurációs** MENÜJÉNEK **DNS-szolgáltatás belefoglalása** jelölőnégyzetében:

![DNS-szolgáltatás engedélyezése a portálon keresztül](./media/service-fabric-dnsservice/enable-dns-service.png)

Ha nem a portál használatával hozza létre a fürtöt, vagy ha meglévő fürtöt frissít, engedélyeznie kell a DNS-szolgáltatást egy sablonban:

- Új fürt üzembe helyezéséhez használhatja a [minta sablonokat](https://github.com/Azure/azure-quickstart-templates/tree/master/service-fabric-secure-cluster-5-node-1-nodetype) , vagy létrehozhat egy saját Resource Manager-sablont. 
- Egy meglévő fürt frissítéséhez navigáljon a fürt erőforráscsoporthoz a portálon, és kattintson az **Automation-parancsfájl** lehetőségre a fürt aktuális állapotát és a csoport többi erőforrását tükröző sablonnal való munkához. További információ: [a sablon exportálása az erőforráscsoporthoz](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template).

A sablon használata után engedélyezheti a DNS-szolgáltatást a következő lépésekkel:

1. Ellenőrizze, hogy a `apiversion` `2017-07-01-preview` vagy újabb értékre van-e állítva a `Microsoft.ServiceFabric/clusters` erőforrás esetében, és ha nem, frissítse az alábbi példában látható módon:

    ```json
    {
        "apiVersion": "2017-07-01-preview",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        ...
    }
    ```

2. Most engedélyezze a DNS szolgáltatást a következő módszerek egyikével:

   - Ha engedélyezni szeretné a DNS-szolgáltatást az alapértelmezett beállításokkal, adja hozzá a `properties` szakasz `addonFeatures` szakaszához az alábbi példában látható módon:

        ```json
          "properties": {
            ...
            "addonFeatures": [
              "DnsService"
              ],
            ...
          }
        ```

   - Ha a szolgáltatást nem az alapértelmezett beállításokkal szeretné engedélyezni, adjon hozzá egy `DnsService` szakaszt a `properties` szakaszban található `fabricSettings` szakaszhoz. Ebben az esetben nem kell hozzáadnia a DnsService `addonFeatures`hoz. A DNS szolgáltatáshoz beállítható tulajdonságokkal kapcsolatos további tudnivalókért lásd: [DNS-szolgáltatás beállításai](./service-fabric-cluster-fabric-settings.md#dnsservice).

       ```json
           "properties": {
             ...  
             "fabricSettings": [
               ...
               {
                 "name": "DnsService",
                 "parameters": [
                   {
                     "name": "IsEnabled",
                     "value": "true"
                   },
                   {
                     "name": "PartitionSuffix",
                     "value": "--"
                   },
                   {
                     "name": "PartitionPrefix",
                     "value": "--"
                   }
                 ]
               },
               ...
              ]
            }
       ```
3. Miután frissítette a fürt sablonját a módosításokkal, alkalmazza őket, és hagyja, hogy a frissítés befejeződjön. Ha a frissítés befejeződött, a DNS-rendszerszolgáltatás elindul a fürtben. A szolgáltatás neve `fabric:/System/DnsService`, **és a Service Fabric Explorer rendszerszolgáltatás** szakasza alatt találhatja meg. 

> [!NOTE]
> Ha a DNS-t Letiltottról engedélyezettre frissíti, előfordulhat, hogy a Service Fabric Explorer nem tükrözi az új állapotot. Az Azure Resource Manager-sablonban található UpgradePolicy módosításával oldja fel újra a csomópontokat. További információkért tekintse meg a [Service Fabric-sablonra vonatkozó referenciát](https://docs.microsoft.com/azure/templates/microsoft.servicefabric/2019-03-01/clusters/applications) .

> [!NOTE]
> A DNS szolgáltatás helyi gépen való fejlesztésekor a DNS-beállítások felülbírálják a DNS-beállításokat. Ha problémákat tapasztal az internethez való csatlakozás során, ellenőrizze a DNS-beállításokat.

## <a name="setting-the-dns-name-for-your-service"></a>A szolgáltatás DNS-nevének beállítása
A szolgáltatások DNS-nevét akár deklaratív módon is beállíthatja az alapértelmezett szolgáltatások ApplicationManifest. xml fájlban vagy PowerShell-parancsokkal.

A szolgáltatás DNS-neve feloldható a fürtben, ezért fontos, hogy biztosítsa a DNS-név egyediségét a fürtön belül. 

Erősen ajánlott `<ServiceDnsName>.<AppInstanceName>`elnevezési sémáját használni; például `service1.application1`. Ha egy alkalmazás a Docker-összeállítás használatával lett telepítve, a rendszer automatikusan hozzárendeli a szolgáltatásokat a DNS-nevekhez ezen elnevezési séma használatával.

### <a name="setting-the-dns-name-for-a-default-service-in-the-applicationmanifestxml"></a>Az alapértelmezett szolgáltatás DNS-nevének beállítása a ApplicationManifest. xml fájlban
Nyissa meg a projektet a Visual Studióban vagy a kedvenc szerkesztőjében, és nyissa meg a ApplicationManifest. xml fájlt. Nyissa meg az alapértelmezett szolgáltatások szakaszt, és mindegyik szolgáltatáshoz adja hozzá a `ServiceDnsName` attribútumot. Az alábbi példa bemutatja, hogyan állíthatja be a szolgáltatás DNS-nevét `service1.application1`

```xml
    <Service Name="Stateless1" ServiceDnsName="service1.application1">
      <StatelessService ServiceTypeName="Stateless1Type" InstanceCount="[Stateless1_InstanceCount]">
        <SingletonPartition />
      </StatelessService>
    </Service>
```
Az alkalmazás üzembe helyezését követően a Service Fabric Explorerben látható szolgáltatási példány megjeleníti a példány DNS-nevét, ahogy az a következő ábrán látható: 

![szolgáltatási végpontok](./media/service-fabric-dnsservice/service-fabric-explorer-dns.png)

A következő példa egy állapot-nyilvántartó szolgáltatás DNS-nevét állítja be `statefulsvc.app`ra. A szolgáltatás nevesített particionálási sémát használ. Figyelje meg, hogy a partíciók nevei kisbetűvel vannak elválasztva. Ez a követelmény a DNS-lekérdezésekben célként megadott partíciók esetében. További információ: [DNS-lekérdezések készítése állapot-nyilvántartó szolgáltatás partícióján](https://docs.microsoft.com/azure/service-fabric/service-fabric-dnsservice#preview-making-dns-queries-on-a-stateful-service-partition).

```xml
    <Service Name="Stateful1" ServiceDnsName="statefulsvc.app" />
      <StatefulService ServiceTypeName="ProcessingType" TargetReplicaSetSize="2" MinReplicaSetSize="2">
        <NamedPartition>
         <Partition Name="partition1" />
         <Partition Name="partition2" />
        </NamedPartition>
      </StatefulService>
    </Service>
```

### <a name="setting-the-dns-name-for-a-service-using-powershell"></a>Egy szolgáltatás DNS-nevének beállítása a PowerShell használatával
A szolgáltatás DNS-nevét a `New-ServiceFabricService` PowerShell-paranccsal hozhatja létre. A következő példa egy új állapot nélküli szolgáltatást hoz létre a DNS-névvel `service1.application1`

```powershell
    New-ServiceFabricService `
    -Stateless `
    -PartitionSchemeSingleton `
    -ApplicationName `fabric:/application1 `
    -ServiceName fabric:/application1/service1 `
    -ServiceTypeName Service1Type `
    -InstanceCount 1 `
    -ServiceDnsName service1.application1
```

## <a name="preview-making-dns-queries-on-a-stateful-service-partition"></a>Előnézet DNS-lekérdezések elkészítése állapot-nyilvántartó szolgáltatás partícióján
A Service Fabric 6,3-es verziójától kezdve a Service Fabric DNS-szolgáltatás támogatja a szolgáltatási partíciók lekérdezéseit.

A DNS-lekérdezésekben használt partíciókhoz a következő elnevezési korlátozások érvényesek:

   - A partíciók nevének DNS-kompatibilisnek kell lennie.
   - A többcímkés partíciók neve (például a dot, a ".", a név) nem használható.
   - A partíciók nevének kisbetűnek kell lennie.

A partíciót tároló DNS-lekérdezések a következőképpen vannak formázva:

```
    <First-Label-Of-Partitioned-Service-DNSName><PartitionPrefix><Target-Partition-Name>< PartitionSuffix>.<Remaining- Partitioned-Service-DNSName>
```
Helyszín:

- *Első – a particionált-Service-DNSName* az első része a szolgáltatás DNS-nevének.
- A *PartitionPrefix* olyan érték, amely a fürt jegyzékfájljának DnsService szakaszában vagy a fürt Resource Manager-sablonján keresztül adható meg. Az alapértelmezett érték a "--". További információ: [DNS-szolgáltatás beállításai](./service-fabric-cluster-fabric-settings.md#dnsservice).
- *Cél – a partíció* neve a partíció neve. 
- A *PartitionSuffix* olyan érték, amely a fürt jegyzékfájljának DnsService szakaszában vagy a fürt Resource Manager-sablonján keresztül adható meg. Az alapértelmezett érték üres karakterlánc. További információ: [DNS-szolgáltatás beállításai](./service-fabric-cluster-fabric-settings.md#dnsservice).
- A *tovább particionált-Service-DNSName* a szolgáltatás DNS-neve hátralévő része.

Az alábbi példák a `PartitionPrefix` és `PartitionSuffix`alapértelmezett beállításait tartalmazó fürtön futó particionált szolgáltatások DNS-lekérdezéseit mutatják be: 

- Ha egy olyan szolgáltatás "0" partícióját szeretné feloldani, amelynek a DNS-neve `backendrangedschemesvc.application`, amely egy tartományhoz tartozó particionálási sémát használ, használja a `backendrangedschemesvc-0.application`.
- Ha egy olyan szolgáltatás "első" partícióját szeretné feloldani, amelynek a DNS-neve `backendnamedschemesvc.application` egy nevesített particionálási sémát használ, használja a `backendnamedschemesvc-first.application`.

A DNS szolgáltatás visszaadja a partíció elsődleges replikájának IP-címét. Ha nincs megadva partíció, a szolgáltatás egy véletlenszerűen kiválasztott partíció elsődleges replikájának IP-címét adja vissza.

## <a name="using-dns-in-your-services"></a>A DNS használata a szolgáltatásokban
Ha több szolgáltatást helyez üzembe, a DNS-név használatával megkeresheti más szolgáltatások végpontját, amelyekkel kommunikálni tud. A DNS-szolgáltatás állapot-nyilvántartó szolgáltatások esetében működik, és a Service Fabric 6,3-es és újabb verzióiban. A 6,3-nál korábbi Service Fabric-verziókban futó állapot-nyilvántartó szolgáltatások esetében az adott szolgáltatás-partíció meghívásához használhatja a beépített [fordított proxy szolgáltatást](./service-fabric-reverseproxy.md) a http-hívásokhoz. 

A DNS-szolgáltatás nem támogatja a dinamikus portokat. A fordított proxy szolgáltatást a dinamikus portokat használó szolgáltatások feloldására használhatja.

A következő kód bemutatja, hogyan hívhat meg állapot nélküli szolgáltatást a DNS-en keresztül. Ez egyszerűen egy normál http-hívás, amelyben a DNS-nevet, a portot és a nem kötelező elérési utat adja meg az URL-cím részeként.

```csharp
public class ValuesController : Controller
{
    // GET api
    [HttpGet]
    public async Task<string> Get()
    {
        string result = "";
        try
        {
            Uri uri = new Uri("http://service1.application1:8080/api/values");
            HttpClient client = new HttpClient();
            var response = await client.GetAsync(uri);
            result = await response.Content.ReadAsStringAsync();
            
        }
        catch (Exception e)
        {
            Console.Write(e.Message);
        }

        return result;
    }
}
```

A következő kód egy állapot-nyilvántartó szolgáltatás egy adott partíciójának hívását mutatja be. Ebben az esetben a DNS-név tartalmazza a partíció nevét (partition1). A hívás a `PartitionPrefix` és `PartitionSuffix`alapértelmezett értékeit feltételezi a fürtben.

```csharp
public class ValuesController : Controller
{
    // GET api
    [HttpGet]
    public async Task<string> Get()
    {
        string result = "";
        try
        {
            Uri uri = new Uri("http://service2-partition1.application1:8080/api/values");
            HttpClient client = new HttpClient();
            var response = await client.GetAsync(uri);
            result = await response.Content.ReadAsStringAsync();
            
        }
        catch (Exception e)
        {
            Console.Write(e.Message);
        }

        return result;
    }
}
```

## <a name="known-issues"></a>Ismert problémák
* A 6,3-es és újabb verziók esetében a DNS-név kötőjelét tartalmazó szolgáltatásnév DNS-keresési szolgáltatásával kapcsolatos probléma a Service Fabric. A hibával kapcsolatos további információkért kövesse a következő GitHub- [problémát](https://github.com/Azure/service-fabric-issues/issues/1197). A javítás ehhez a következő 6,3 frissítéssel érkezik. 

* A Service Fabric szolgáltatások DNS szolgáltatása még nem támogatott Linux rendszeren. A DNS-szolgáltatás a Linux rendszerű tárolók esetében támogatott. A Fabric Client/ServicePartitionResolver manuális feloldása a rendelkezésre álló alternatíva.

## <a name="next-steps"></a>Következő lépések
További információ a fürtön belüli, a [kapcsolattal és a szolgáltatásokkal](service-fabric-connect-and-communicate-with-services.md) való kommunikációval

