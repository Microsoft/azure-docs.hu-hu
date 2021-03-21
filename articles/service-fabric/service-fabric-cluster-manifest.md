---
title: Az Azure Service Fabric önálló fürt konfigurálása
description: Ismerje meg, hogyan konfigurálhatja önálló vagy helyszíni Azure Service Fabric-fürtöt.
ms.topic: conceptual
ms.date: 11/12/2018
ms.openlocfilehash: fd93263b38340ce080cca1aecb98f3a599ff1861
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91843158"
---
# <a name="configuration-settings-for-a-standalone-windows-cluster"></a>Önálló Windows-fürt konfigurációs beállításai
Ez a cikk egy önálló Azure Service Fabric-fürt konfigurációs beállításait ismerteti, amelyek a fájlban *ClusterConfig.js* megadhatók. Ezzel a fájllal adhatja meg a fürt csomópontjaival, a biztonsági konfigurációkkal, valamint a hálózati topológiával kapcsolatos információkat a hibák és a frissítési tartományok tekintetében.  A konfigurációs beállítások módosítása vagy hozzáadása után [létrehozhat egy önálló fürtöt](service-fabric-cluster-creation-for-windows-server.md) , vagy [frissítheti egy önálló fürt konfigurációját](service-fabric-cluster-config-upgrade-windows-server.md).

[Az önálló Service Fabric csomag letöltésekor](service-fabric-cluster-creation-for-windows-server.md#downloadpackage)a rendszer a mintákat is tartalmazza ClusterConfig.js. A nevükben a "DevCluster" nevű minták olyan fürtöt hoznak létre, amely ugyanazon a gépen található mindhárom csomóponttal, logikai csomópontok használatával. Ezen csomópontok közül legalább egy elsődleges csomópontként kell megjelölni. Ez a típusú fürt hasznos fejlesztési vagy tesztelési környezetekhez. Üzemi fürtként nem támogatott. A "MultiMachine" névvel ellátott minták segítenek a termelési szintű fürtök létrehozásában, és mindegyik csomópont külön gépen található. A fürtök elsődleges csomópontjainak száma a fürt [megbízhatósági szintjétől](#reliability)függ. A 5,7-es verzióban az API 05-2017-es verziója eltávolította a megbízhatósági szint tulajdonságot. Ehelyett a kód kiszámítja a fürt leginkább optimalizált megbízhatósági szintjét. Ne próbálkozzon a tulajdonság értékének beállításával a 5,7-es verzióban.

* ClusterConfig.Unsecure.DevCluster.jsbe és ClusterConfig.Unsecure.MultiMachine.jsa bemutatjuk, hogyan kell létrehozni egy nem biztonságos tesztet vagy üzemi fürtöt.

* ClusterConfig.Windows.DevCluster.json és ClusterConfig.Windows.MultiMachine.json bemutatjuk, hogyan hozhatók létre a [Windows biztonsági](service-fabric-windows-cluster-windows-security.md)szolgáltatással védett tesztelési vagy üzemi fürtök.

* ClusterConfig.X509.DevCluster.json és ClusterConfig.X509.MultiMachine.json bemutatjuk, hogyan hozhat létre olyan tesztelési vagy üzemi fürtöket, amelyek a [X509-tanúsítvány alapú biztonság](service-fabric-windows-cluster-x509-security.md)használatával biztonságosak.

Most vizsgáljuk meg egy ClusterConfig.jskülönböző részeit a fájlon.

## <a name="general-cluster-configurations"></a>Általános fürtkonfiguráció
Az általános fürtkonfiguráció a fürtre jellemző konfigurációk körét fedi le, ahogy az a következő JSON-kódrészletben látható:

```json
    "name": "SampleCluster",
    "clusterConfigurationVersion": "1.0.0",
    "apiVersion": "01-2017",
```

A Service Fabric-fürthöz bármilyen felhasználóbarát nevet adhat, ha hozzárendeli azt a name változóhoz. A clusterConfigurationVersion a fürt verziószáma. Minden alkalommal növelje a Service Fabric-fürt frissítését. Hagyja a apiVersion beállítást az alapértelmezett értékre.

## <a name="nodes-on-the-cluster"></a>Csomópontok a fürtön
A csomópontokat a Service Fabric-fürtön a csomópontok szakasz használatával konfigurálhatja, az alábbi kódrészletben látható módon:
```json
"nodes": [{
    "nodeName": "vm0",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType0",
    "faultDomain": "fd:/dc1/r0",
    "upgradeDomain": "UD0"
}, {
    "nodeName": "vm1",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType1",
    "faultDomain": "fd:/dc1/r1",
    "upgradeDomain": "UD1"
}, {
    "nodeName": "vm2",
    "iPAddress": "localhost",
    "nodeTypeRef": "NodeType2",
    "faultDomain": "fd:/dc1/r2",
    "upgradeDomain": "UD2"
}],
```

Egy Service Fabric fürtnek legalább három csomópontot kell tartalmaznia. A beállításnak megfelelően további csomópontokat is hozzáadhat ehhez a szakaszhoz. Az alábbi táblázat az egyes csomópontok konfigurációs beállításait ismerteti:

| **Csomópont-konfiguráció** | **Leírás** |
| --- | --- |
| Csomópontnév |Bármilyen felhasználóbarát nevet adhat a csomópontnak. |
| IP-cím |A csomópont IP-címének megkereséséhez nyisson meg egy parancssori ablakot, és írja be a parancsot `ipconfig` . Jegyezze fel az IPV4-címeket, és rendelje hozzá az IP-cím változóhoz. |
| nodeTypeRef |Mindegyik csomóponthoz egy másik csomópont-típus rendelhető. A [csomópontok típusai](#node-types) a következő szakaszban vannak meghatározva. |
| faultDomain |A tartalék tartományok lehetővé teszik a fürt rendszergazdái számára a közös fizikai függőségek miatti feladatátvételt okozó fizikai csomópontok definiálását. |
| upgradeDomain |A frissítési tartományok olyan csomópont-készleteket határoznak meg, amelyek a Service Fabric frissítéseinek egy időben történő leállításakor állnak le. Kiválaszthatja, hogy mely csomópontok legyenek hozzárendelve a frissítési tartományokhoz, mert nem korlátozzák semmilyen fizikai követelményt. |

## <a name="cluster-properties"></a>Fürt tulajdonságai
A ClusterConfig.json tulajdonságok szakasza a fürt konfigurálására szolgál a következő ábrán látható módon:

### <a name="reliability"></a>Megbízhatóság
A reliabilityLevel fogalma meghatározza a fürt elsődleges csomópontjain futtatható Service Fabric rendszerszolgáltatások replikáinak vagy példányainak számát. Meghatározza a szolgáltatások megbízhatóságát és így a fürtöt. Az értéket a rendszer a fürt létrehozásakor és a frissítés időpontjában számítja ki.

### <a name="diagnostics"></a>Diagnosztika
A diagnosticsStore szakaszban paramétereket konfigurálhat a diagnosztika engedélyezéséhez és a csomópontok vagy fürtök hibáinak elhárításához, ahogy az alábbi kódrészletben is látható: 

```json
"diagnosticsStore": {
    "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
    "dataDeletionAgeInDays": "7",
    "storeType": "FileShare",
    "IsEncrypted": "false",
    "connectionstring": "c:\\ProgramData\\SF\\DiagnosticsStore"
}
```

A metaadatok a fürt diagnosztika leírását jelentik, és a beállításuk szerint állíthatók be. Ezek a változók segítenek a ETW nyomkövetési naplók és az összeomlási memóriaképek, valamint a teljesítményszámlálók összegyűjtésében. További információ a ETW nyomkövetési naplóiról: [TraceLog](/windows-hardware/drivers/devtest/tracelog) és [ETW nyomkövetés](/dotnet/framework/wcf/samples/etw-tracing). Az összes napló, beleértve az [Összeomlási memóriaképeket](https://techcommunity.microsoft.com/t5/ask-the-performance-team/bg-p/AskPerf) és a [teljesítményszámlálókat](/windows/win32/perfctrs/performance-counters-portal), a számítógép ConnectionString mappájába lehet irányítani. A AzureStorage-t is használhatja a diagnosztika tárolásához. Tekintse meg a következő kódrészletet:

```json
"diagnosticsStore": {
    "metadata":  "Please replace the diagnostics store with an actual file share accessible from all cluster machines.",
    "dataDeletionAgeInDays": "7",
    "storeType": "AzureStorage",
    "IsEncrypted": "false",
    "connectionstring": "xstore:DefaultEndpointsProtocol=https;AccountName=[AzureAccountName];AccountKey=[AzureAccountKey]"
}
```

### <a name="security"></a>Biztonság
A biztonsági szakasz a biztonságos önálló Service Fabric-fürthöz szükséges. Az alábbi kódrészlet a szakasz egy részét mutatja be:

```json
"security": {
    "metadata": "This cluster is secured using X509 certificates.",
    "ClusterCredentialType": "X509",
    "ServerCredentialType": "X509",
    . . .
}
```

A metaadatok a biztonságos fürt leírása, és a beállításnak megfelelően állíthatók be. A ClusterCredentialType és a ServerCredentialType határozza meg a fürt és a csomópontok által megvalósított biztonsági típust. A tanúsítvány alapú biztonsági vagy Active Directory *Windows* -alapú biztonság érdekében a *X509* lehet beállítani. A biztonsági szakasz többi része a biztonság típusától függ. További információ a biztonsági szakasz kitöltéséről: [tanúsítványok alapú biztonság önálló fürtben](service-fabric-windows-cluster-x509-security.md) vagy [Windows-Biztonság önálló fürtben](service-fabric-windows-cluster-windows-security.md).

### <a name="node-types"></a>Csomópont-típusok
A nodeTypes szakasz a fürt csomópontjainak típusát írja le. A fürthöz legalább egy csomópont-típust meg kell adni, ahogy az a következő kódrészletben látható: 

```json
"nodeTypes": [{
    "name": "NodeType0",
    "clientConnectionEndpointPort": "19000",
    "clusterConnectionEndpointPort": "19001",
    "leaseDriverEndpointPort": "19002"
    "serviceConnectionEndpointPort": "19003",
    "httpGatewayEndpointPort": "19080",
    "reverseProxyEndpointPort": "19081",
    "applicationPorts": {
        "startPort": "20575",
        "endPort": "20605"
    },
    "ephemeralPorts": {
        "startPort": "20606",
        "endPort": "20861"
    },
    "isPrimary": true
}]
```

A név az adott csomópont típusának rövid neve. A csomópont típusú csomópont létrehozásához rendeljen hozzá egy rövid nevet az adott csomóponthoz tartozó nodeTypeRef változóhoz, ahogy azt [korábban említettük](#nodes-on-the-cluster). Az egyes csomópont-típusoknál adja meg a használt kapcsolatok végpontját. Bármelyik portszámot kiválaszthatja ezekhez a kapcsolatok végpontokhoz, ha nem ütköznek a fürt többi végpontjának. A többcsomópontos fürtben egy vagy több elsődleges csomópont van (azaz a isPrimary értéke *true*), a [reliabilityLevel](#reliability)függően. Ha többet szeretne megtudni az elsődleges és a nem elsődleges csomópont-típusokról, tekintse meg Service Fabric a nodeTypes és a reliabilityLevel kapcsolatos információk a [fürt kapacitásának tervezési szempontjait](service-fabric-cluster-capacity.md) ismertető témakört 

#### <a name="endpoints-used-to-configure-the-node-types"></a>A csomópont-típusok konfigurálásához használt végpontok
* a clientConnectionEndpointPort az ügyfél által a fürthöz való csatlakozáshoz használt port, ha az ügyfél API-jai vannak használatban. 
* a clusterConnectionEndpointPort az a port, ahol a csomópontok kommunikálnak egymással.
* a leaseDriverEndpointPort az a port, amelyet a fürt címbérletének illesztőprogramja használ annak megállapítására, hogy a csomópontok továbbra is aktívak-e. 
* a serviceConnectionEndpointPort a csomóponton telepített alkalmazások és szolgáltatások által használt port, amely az adott csomóponton lévő Service Fabric ügyféllel folytatott kommunikációra szolgál.
* a httpGatewayEndpointPort az Service Fabric Explorer által a fürthöz való csatlakozáshoz használt port.
* a az ephemeralports felülbírálja az [operációs rendszer által használt dinamikus portokat](https://support.microsoft.com/kb/929851). Service Fabric a portok egy részét használja az alkalmazás portjaiként, és a fennmaradók elérhetők az operációs rendszer számára. Ez a tartomány az operációs rendszerben található meglévő tartományhoz is leképezhető, így az összes célra használhatja a JSON-fájlokban megadott tartományokat. Győződjön meg arról, hogy a kezdő és a záró portok közötti különbség legalább 255. Előfordulhat, hogy ütközések merülhetnek fel, ha a különbség túl alacsony, mert ez a tartomány meg van osztva az operációs rendszerrel. A konfigurált Dinamikus porttartomány megjelenítéséhez futtassa a parancsot `netsh int ipv4 show dynamicport tcp` .
* a applicationPorts a Service Fabric-alkalmazások által használt portok. Az alkalmazás portszámának elég nagynak kell lennie ahhoz, hogy le lehessen fedni az alkalmazások végponti követelményeit. Ez a tartomány nem lehet a számítógép Dinamikus porttartomány, azaz a konfigurációban beállított az ephemeralports-tartomány. Service Fabric ezeket a portokat használja, amikor új portokra van szükség, és gondoskodik a portok tűzfalának megnyitásáról. 
* a reverseProxyEndpointPort egy nem kötelező fordított proxy végpont. További információ: [Service Fabric fordított proxy](service-fabric-reverseproxy.md). 

### <a name="log-settings"></a>Naplózási beállítások
A fabricSettings szakaszban megadhatja a Service Fabric-és naplókhoz tartozó gyökérkönyvtárat. Ezeket a címtárakat csak a fürt kezdeti létrehozásakor lehet testreszabni. Tekintse meg a szakasz következő mintáját:

```json
"fabricSettings": [{
    "name": "Setup",
    "parameters": [{
        "name": "FabricDataRoot",
        "value": "C:\\ProgramData\\SF"
    }, {
        "name": "FabricLogRoot",
        "value": "C:\\ProgramData\\SF\\Log"
}]
```

Azt javasoljuk, hogy egy nem operációsrendszer-meghajtót használjon FabricDataRoot és FabricLogRoot. Nagyobb megbízhatóságot biztosít a helyzetek elkerülésében, amikor az operációs rendszer nem válaszol. Ha csak az adatgyökerét testreszabja, a rendszer a napló gyökerét egy szintre helyezi az adatgyökér alá.

### <a name="stateful-reliable-services-settings"></a>Állapot-nyilvántartó Reliable Services beállításai
A KtlLogger szakaszban megadhatja Reliable Services globális konfigurációs beállításait. További információ ezekről a beállításokról: [állapot-nyilvántartó Reliable Services konfigurálása](service-fabric-reliable-services-configuration.md). Az alábbi példa azt mutatja be, hogyan változtatható meg a megosztott tranzakciónapló, amely az állapot-nyilvántartó szolgáltatásokhoz tartozó megbízható gyűjtemények visszaállítására lett létrehozva:

```json
"fabricSettings": [{
    "name": "KtlLogger",
    "parameters": [{
        "name": "SharedLogSizeInMB",
        "value": "4096"
    }]
}]
```

### <a name="add-on-features"></a>Kiegészítő funkciók
A kiegészítő funkciók konfigurálásához konfigurálja a apiVersion 04-2017 vagy újabb értékre, és konfigurálja a addonFeatures az itt látható módon:

```json
"apiVersion": "04-2017",
"properties": {
    "addOnFeatures": [
        "DnsService",
        "RepairManager"
    ]
}
```
Az összes elérhető kiegészítő funkció a [Service Fabric REST API-referenciában](/rest/api/servicefabric/sfrp-model-addonfeatures)látható.

### <a name="container-support"></a>Tárolótámogatás
Ha engedélyezni szeretné a tárolók támogatását a Windows Server-tárolók és a Hyper-V tárolók számára az önálló fürtök esetében, engedélyezni kell a DnsService-bővítmény funkciót.

## <a name="next-steps"></a>Következő lépések
Miután az önálló fürt beállítása szerint konfigurálta a teljes *ClusterConfig.jsa* fájlon, üzembe helyezheti a fürtöt. Kövesse az [önálló Service Fabric-fürt létrehozása](service-fabric-cluster-creation-for-windows-server.md)című témakör lépéseit. 

Ha önálló fürttel rendelkezik, akkor [egy önálló fürt konfigurációját is frissítheti](service-fabric-cluster-config-upgrade-windows-server.md). 

Ismerje meg, hogyan [jelenítheti meg a fürtöt Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)használatával.
