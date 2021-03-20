---
title: Azure Service Fabric Java ügyféloldali API-k
description: Service Fabric Java ügyféloldali API-k előállítása és használata Service Fabric ügyfél REST API-specifikáció használatával
author: rapatchi
ms.topic: conceptual
ms.date: 11/27/2017
ms.custom: devx-track-java
ms.author: rapatchi
ms.openlocfilehash: 24ee4a3d8109626bf93e01fbd10c00140762c9c6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "87324624"
---
# <a name="azure-service-fabric-java-client-apis"></a>Azure Service Fabric Java ügyféloldali API-k

Service Fabric ügyfél API-k lehetővé teszik a Service-alapú alkalmazások és tárolók üzembe helyezését és kezelését az Azure-ban, a helyszínen, helyi fejlesztési gépen vagy más felhőben lévő Service Fabric-fürtökön. Ez a cikk azt ismerteti, hogyan lehet Service Fabric Java ügyféloldali API-kat előállítani és használni a Service Fabric ügyfél REST API-jai esetében

## <a name="generate-the-client-code-using-autorest"></a>Az ügyfél kódjának előállítása az autorest használatával

Az [autorest](https://github.com/Azure/autorest) egy olyan eszköz, amely ügyféloldali kódtárakat hoz létre a REST-alapú webszolgáltatásokhoz való hozzáféréshez. Az automatikus Rest bemenetbe való bevitel olyan specifikáció, amely a OpenAPI-specifikáció formátumával ismerteti a REST API. [Service Fabric ügyfél REST API-jai](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/servicefabric/data-plane) ezt a specifikációt követik.

Kövesse az alábbi lépéseket az Service Fabric Java-ügyfél kódjának az autorest eszközzel történő létrehozásához.

1. A node.js és az NPM telepítése a gépre

    Ha Linuxot használ, akkor:
    ```bash
    sudo apt-get install npm
    sudo apt install nodejs
    ```
    Ha Mac OS X-et használ, akkor:
    ```bash
    brew install node
    ```

2. Az autorest telepítése a NPM használatával.
    ```bash
    npm install -g autorest
    ```

3. Az [Azure-REST-API-specs](https://github.com/Azure/azure-rest-api-specs)  adattárat a helyi gépen, majd a klónozott helyet a számítógép terminálján nyissa meg.


4. Nyissa meg az alább említett helyet a klónozott tárházban.
    ```bash
    cd specification\servicefabric\data-plane\Microsoft.ServiceFabric\stable\6.0
    ```

    > [!NOTE]
    > Ha a fürt verziója nem 6,0. *, akkor nyissa meg a megfelelő könyvtárat a stabil mappában.
    >   

5. Futtassa a következő autorest parancsot a Java-ügyfél kódjának létrehozásához.
    
    ```bash
    autorest --input-file= servicefabric.json --java --output-folder=[output-folder-name] --namespace=[namespace-of-generated-client]
    ```
   Az alábbi példa az autorest használatát szemlélteti.
   
    ```bash
    autorest --input-file=servicefabric.json --java --output-folder=java-rest-api-code --namespace=servicefabricrest
    ```
   
   A következő parancs a ``servicefabric.json`` specifikációs fájlt bemenetként veszi fel, és Java-ügyfélprogramot hoz létre a ``java-rest-api-     code`` mappában, és a kódot a  ``servicefabricrest`` névtérbe foglalja. Ennek a lépésnek a megkeresése után két mappát ``models`` , ``implementation`` a mappában pedig két fájlt fog ``ServiceFabricClientAPIs.java`` ``package-info.java`` létrehozni ``java-rest-api-code`` .


## <a name="include-and-use-the-generated-client-in-your-project"></a>A generált ügyfél belefoglalása és használata a projektben

1. Adja hozzá megfelelően a generált kódot a projekthez. Javasoljuk, hogy hozzon létre egy könyvtárat a generált kóddal, és vegye fel ezt a kódtárat a projektbe.
2. Ha egy könyvtárat hoz létre, a következő függőséget adja meg a könyvtár projektben. Ha egy másik módszert követ, akkor a függőség megfelelőnek kell lennie.

    ```
        GroupId:  com.microsoft.rest
        Artifactid: client-runtime
        Version: 1.2.1
    ```
    Ha például a Maven Build rendszerét használja, a következőt kell megadnia a ``pom.xml`` fájlban:

    ```xml
        <dependency>
          <groupId>com.microsoft.rest</groupId>
          <artifactId>client-runtime</artifactId>
          <version>1.2.1</version>
        </dependency>
    ```

3. Hozzon létre egy RestClient a következő kóddal:

    ```java
        RestClient simpleClient = new RestClient.Builder()
            .withBaseUrl("http://<cluster-ip or name:port>")
            .withResponseBuilderFactory(new ServiceResponseBuilder.Factory())
            .withSerializerAdapter(new JacksonAdapter())
            .build();
        ServiceFabricClientAPIs client = new ServiceFabricClientAPIsImpl(simpleClient);
    ```
4. Használja az ügyfél objektumot, és szükség szerint végezze el a megfelelő hívásokat. Íme néhány példa, amely bemutatja az ügyfél-objektum használatát. Feltételezzük, hogy az alkalmazáscsomag az alábbi API-k használata előtt lett felépítve és feltöltve a rendszerkép-tárolóba.
    * Alkalmazás kiépítése
    
        ```java
            ApplicationTypeImageStorePath imageStorePath = new ApplicationTypeImageStorePath();
            imageStorePath.withApplicationTypeBuildPath("<application-path-in-image-store>");
            client.provisionApplicationType(imageStorePath);
        ```
    * Alkalmazás létrehozása

        ```java
            ApplicationDescription applicationDescription = new ApplicationDescription();
            applicationDescription.withName("<application-uri>");
            applicationDescription.withTypeName("<application-type>");
            applicationDescription.withTypeVersion("<application-version>");
            client.createApplication(applicationDescription);
        ```

## <a name="understanding-the-generated-code"></a>A generált kód ismertetése
Minden API-hoz négy túlterhelést talál a megvalósításhoz. Ha választható paraméterek vannak, akkor négy további változatot talál, beleértve a választható paramétereket is. Vegyük például az API-t ``removeReplica`` .
 1. **nyilvános Void removeReplica (karakterlánc csomópontnév, UUID partitionId, karakterlánc replicaId, logikai forceRemove, hosszú időkorlát)**
    * Ez a removeReplica API-hívás szinkron változata
 2. **nyilvános ServiceFuture \<Void> removeReplicaAsync (karakterlánc csomópontnév, UUID partitionId, karakterlánc replicaId, logikai forceRemove, hosszú időkorlát, végső ServiceCallback \<Void> ServiceCallback)**
    * Az API-hívás ezen változata akkor használható, ha a jövőbeli aszinkron programozást szeretné használni, és visszahívásokat használ
 3. **nyilvános megfigyelhető \<Void> removeReplicaAsync (karakterlánc csomópontnév, UUID partitionId, karakterlánc replicaId)**
    * Az API-hívás ezen változata akkor használható, ha reaktív aszinkron programozást szeretne használni
 4. **nyilvános megfigyelhető \<ServiceResponse\<Void>> removeReplicaWithServiceResponseAsync (string csomópontnév, UUID partitionId, karakterlánc replicaId)**
    * Az API-hívás ezen változata akkor használható, ha reaktív aszinkron programozást szeretne használni, és a nyers Rest-választal foglalkozik.

## <a name="next-steps"></a>Következő lépések
* A [Service FABRIC REST API](/rest/api/servicefabric/) -k megismerése
