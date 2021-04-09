---
title: Azure Service Fabric Mesh Maven-dokumentáció
description: A Maven beépülő modul Service Fabric rácsvonalhoz való használatának referenciáját tartalmazza
author: suhuruli
ms.author: suhuruli
ms.date: 11/26/2018
ms.topic: reference
ms.custom: devx-track-java
ms.openlocfilehash: dd42752dc7ef2c7686073b7a2d780fe9356425c7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99625583"
---
# <a name="maven-plugin-for-service-fabric-mesh"></a>Maven beépülő modul a Service Fabric Meshhoz

> [!IMPORTANT]
> Az Azure Service Fabric Mesh előzetes verziója ki lett vonva. Az új központi telepítések többé nem lesznek engedélyezve a Service Fabric Mesh API-n keresztül. A meglévő központi telepítések támogatása a 2021. április 28. után folytatódik.
> 
> Részletekért lásd: az [Azure Service Fabric Mesh előzetes verziójának nyugdíjazása](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

## <a name="prerequisites"></a>Előfeltételek

- Java SDK
- Maven
- Azure CLI Mesh bővítménnyel
- Service Fabric parancssori felület

## <a name="goals"></a>Célok

### `azure-sfmesh:init`
- Létrehoz egy `servicefabric` mappát, amely tartalmazza `appresources` a fájlt tartalmazó mappát `application.yaml` . 

### `azure-sfmesh:addservice`
- Létrehoz egy mappát `servicefabric` a mappán belül a szolgáltatás nevével, és létrehozza a szolgáltatás YAML-fájlját. 

### `azure-sfmesh:addnetwork`
- Létrehoz egy `network` YAML a megadott hálózati névvel a mappában. `appresources` 

### `azure-sfmesh:addgateway`
- Létrehoz egy `gateway` YAML a megadott átjáró nevével a `appresources` mappában. 

#### `azure-sfmesh:addvolume`
- Létrehoz egy `volume` YAML a megadott kötet nevével a `appresources` mappában.

### `azure-sfmesh:addsecret`
- Létrehoz egy `secret` YAML a megadott titkos névvel a mappában. `appresources` 

### `azure-sfmesh:addsecretvalue`
- Létrehoz egy `secretvalue` YAML a megadott titkos és titkos érték nevével a `appresources` mappában. 

### `azure-sfmesh:deploy`
- Egyesíti a yamls a `servicefabric` mappából, és létrehoz egy Azure Resource Manager sablon JSON-t az aktuális mappában.
- Az összes erőforrás üzembe helyezése az Azure Service Fabric Mesh-környezetben 

### `azure-sfmesh:deploytocluster`
- Létrehoz egy mappát ( `meshDeploy` ), amely tartalmazza a yamls létrehozott telepítési JSON-ket, amelyek Service Fabric fürtökre alkalmazhatók
- Az összes erőforrás üzembe helyezése a Service Fabric-fürtön
 

## <a name="usage"></a>Használat

Ha a Maven Java-alkalmazásban szeretné használni a Maven beépülő modult, adja hozzá a következő kódrészletet a pom.xml fájlhoz:

```XML
<project>
  ...
  <build>
    ...
    <plugins>
      ...
      <plugin>
        <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-sfmesh-maven-plugin</artifactId>
          <version>0.1.0</version>
      </plugin>
    </plugins>
  </build>
</project>
```

## <a name="common-configuration"></a>Közös konfiguráció

A Maven beépülő modul jelenleg nem támogatja az Azure-hoz készült Maven beépülő modulok általános konfigurációit.

## <a name="how-to"></a>Útmutató

### <a name="initialize-maven-project-for-azure-service-fabric-mesh"></a>Maven-projekt inicializálása az Azure Service Fabric Meshhoz
Futtassa az alábbi parancsot az alkalmazás-erőforrás YAML fájl létrehozásához.

```cmd
mvn azure-sfmesh:init -DapplicationName=helloworldserver
```

- Létrehoz egy nevű mappát `servicefabric->appresources` a gyökérkönyvtárban, amely egy nevű alkalmazás-YAML tartalmaz. `app_helloworldserver`

### <a name="add-resource-to-your-application"></a>Erőforrás hozzáadása az alkalmazáshoz

#### <a name="add-a-new-network-to-your-application"></a>Új hálózat hozzáadása az alkalmazáshoz
Futtassa az alábbi parancsot egy hálózati erőforrás YAML létrehozásához. 

```cmd
mvn azure-sfmesh:addnetwork -DnetworkName=helloworldservicenetwork -DnetworkAddressPrefix=10.0.0.0/22
```

- Létrehoz egy hálózati YAML a (z) nevű mappában. `servicefabric->appresources``network_helloworldservicenetwork`

#### <a name="add-a-new-service-to-your-application"></a>Új szolgáltatás hozzáadása az alkalmazáshoz
A szolgáltatás YAML létrehozásához futtassa az alábbi parancsot. 

```cmd
mvn azure-sfmesh:addservice -DapplicationName=helloworldserver -DserviceName=helloworldservice -DimageName=helloworldserver:latest -DlistenerPort=8080 -DnetworkRef=helloworldservicenetwork
```

- Létrehoz egy YAML a nevű mappában `servicefabric->helloworldservice` `service_helloworldservice` , amely `helloworldservicenetwork` & az `helloworldserver` alkalmazásra hivatkozik
- A szolgáltatás a 8080-es portot figyeli.
- A szolgáltatás a ***helloworldserver: Latest*** használja, mivel a tároló képe.

#### <a name="add-a-new-gateway-resource-to-your-application"></a>Új átjáró-erőforrás hozzáadása az alkalmazáshoz
Futtassa az alábbi parancsot egy átjáró-erőforrás YAML létrehozásához. 

```cmd
mvn azure-sfmesh:addgateway -DapplicationName=helloworldserver -DdestinationNetwork=helloworldservicenetwork -DgatewayName=helloworldgateway -DlistenerName=helloworldserviceListener -DserviceName=helloworldservice -DsourceNetwork=open -DtcpPort=80
```

- Új átjáró YAML létrehozása a (z) `servicefabric->appresources` nevű mappában `gateway_helloworldgateway`
- `helloworldservicelistener`Az átjárótól érkező hívásokat figyelő szolgáltatás-figyelőre mutató hivatkozások. A a `helloworldservice` szolgáltatásra, `helloworldservicenetwork` a hálózatra és `helloworldserver` az alkalmazásra is hivatkozik. 
- Az 80-es porton érkező kérések figyelése

#### <a name="add-a-new-volume-to-your-application"></a>Új kötet hozzáadása az alkalmazáshoz
Futtassa az alábbi parancsot egy mennyiségi erőforrás YAML létrehozásához. 

```cmd
mvn azure-sfmesh:addvolume -DvolumeAccountKey=key -DvolumeAccountName=name -DvolumeName=vol1 -DvolumeShareName=share
```

- Létrehoz egy kötet YAML a (z) nevű mappában. `servicefabric->appresources``volume_vol1`
- Beállítja a szükséges paraméterek tulajdonságait, `volumeAccountKey` és `volumeShareName` a fentiek szerint
- A létrehozott kötetre való hivatkozással kapcsolatos további információkért tekintse meg a következőt, és [telepítse az alkalmazást Azure Files kötet használatával](service-fabric-mesh-howto-deploy-app-azurefiles-volume.md)

#### <a name="add-a-new-secret-resource-to-your-application"></a>Új titkos erőforrás hozzáadása az alkalmazáshoz
Az alábbi parancs futtatásával hozzon létre egy titkos erőforrás-YAML. 

```cmd
mvn azure-sfmesh:addsecret -DsecretName=secret1
```

- Létrehoz egy titkos YAML a (z) nevű mappában. `servicefabric->appresources``secret_secret1`
- A létrehozott titokra való hivatkozással kapcsolatos további információkért tekintse meg a következőt: [titkok kezelése](service-fabric-mesh-howto-manage-secrets.md)

#### <a name="add-a-new-secretvalue-resource-to-your-application"></a>Új secretvalue-erőforrás hozzáadása az alkalmazáshoz
Futtassa az alábbi parancsot egy secretvalue erőforrás-YAML létrehozásához. 

```cmd
mvn azure-sfmesh:addsecretvalue -DsecretValue=someVal -DsecretValueName=secret1/v1
```

- Hozzon létre egy secretvalue-YAML a (z) `servicefabric->appresources` nevű mappában `secretvalue_secret1_v1`

### <a name="run-the-application-locally"></a>Az alkalmazás helyi futtatása

A cél segítségével `azure-sfmesh:deploytocluster` helyileg is futtathatja az alkalmazást az alábbi parancs használatával:

```cmd
mvn azure-sfmesh:deploytocluster
```

Alapértelmezés szerint ez a cél telepíti az erőforrásokat a helyi fürtre. Ha helyi fürtre telepíti a szolgáltatást, feltételezi, hogy rendelkezik egy helyi Service Fabric-fürttel. Az erőforrásokhoz tartozó helyi Service Fabric-fürt jelenleg csak [Windows](service-fabric-mesh-howto-setup-developer-environment-sdk.md)rendszeren támogatott.

- A yamls származó JSON-ket hoz létre, amelyek Service Fabric fürtökre alkalmazhatók
- Üzembe helyezés a fürt végpontján

### <a name="deploy-application-to-azure-service-fabric-mesh"></a>Alkalmazás üzembe helyezése az Azure Service Fabric Meshban

A cél segítségével az `azure-sfmesh:deploy` alábbi parancs futtatásával üzembe helyezheti Service Fabric Mesh-környezetet:

```cmd
mvn azure-sfmesh:deploy -DresourceGroup=todoapprg -Dlocation=eastus
```

- Létrehoz egy nevű erőforráscsoportot `todoapprg` , ha az nem létezik.
- Létrehoz egy Azure Resource Manager sablon JSON-t a YAMLs egyesítésével. 
- Üzembe helyezi a JSON-t az Azure Service Fabric Mesh-környezetben.
