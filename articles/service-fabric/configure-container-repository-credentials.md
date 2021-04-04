---
title: Azure Service Fabric – tároló-adattár hitelesítő adatainak konfigurálása
description: Adattár hitelesítő adatainak konfigurálása a lemezképek a tároló beállításjegyzékből való letöltéséhez
ms.topic: conceptual
ms.date: 12/09/2019
ms.openlocfilehash: 0c6421fed88a3909db717c13a6b3faf51c4491cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96574819"
---
# <a name="configure-repository-credentials-for-your-application-to-download-container-images"></a>Az alkalmazás adattárbeli hitelesítő adatainak konfigurálása a tároló lemezképének letöltéséhez

Konfigurálja a tároló beállításjegyzékének hitelesítését `RepositoryCredentials` az `ContainerHostPolicies` alkalmazás jegyzékfájljának szakaszába való hozzáadásával. Adja hozzá a tároló-beállításjegyzékhez tartozó fiókot és jelszót (a *myregistry.azurecr.IO* az alábbi példában), amely lehetővé teszi, hogy a szolgáltatás letöltse a tároló rendszerképét az adattárból.

```xml
<ServiceManifestImport>
    ...
    <Policies>
        <ContainerHostPolicies CodePackageRef="Code">
            <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
            <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
        </ContainerHostPolicies>
    </Policies>
    ...
</ServiceManifestImport>
```

Javasoljuk, hogy Titkosítsa a tárház jelszavát egy olyan titkosítási-tanúsítvánnyal, amely a fürt minden csomópontjára telepítve van. Amikor a Service Fabric üzembe helyezi a szervizcsomagot a fürtön, a titkosítási tanúsítvánnyal fejti vissza a titkosított szöveget. Az Invoke-ServiceFabricEncryptText parancsmaggal hozhat létre titkosított szöveget a jelszóhoz, amelyet a rendszer hozzáad az ApplicationManifest.xml fájlhoz.
A tanúsítványokkal és a titkosítási szemantikagal kapcsolatos további információkért lásd: [Secret Management](service-fabric-application-secret-management.md) .

## <a name="configure-cluster-wide-credentials"></a>A fürtre kiterjedő hitelesítő adatok konfigurálása

Service Fabric lehetővé teszi a teljes fürtre kiterjedő hitelesítő adatok konfigurálását, amelyek az alkalmazások alapértelmezett tárházbeli hitelesítő adataiként használhatók.

Ez a funkció engedélyezhető vagy letiltható úgy, `UseDefaultRepositoryCredentials` hogy az attribútumot a ( `ContainerHostPolicies` ApplicationManifest.xml `true` `false` ) értékkel együtt adja hozzá a következőhöz:.

```xml
<ServiceManifestImport>
    ...
    <Policies>
        <ContainerHostPolicies CodePackageRef="Code" UseDefaultRepositoryCredentials="true">
            <PortBinding ContainerPort="80" EndpointRef="Guest1TypeEndpoint"/>
        </ContainerHostPolicies>
    </Policies>
    ...
</ServiceManifestImport>
```

A Service Fabric ezután az alapértelmezett adattár hitelesítő adatait használja, amelyek a szakasz ClusterManifest adhatók meg `Hosting` .  Ha `UseDefaultRepositoryCredentials` a értéke `true` , Service Fabric beolvassa a következő értékeket a ClusterManifest:

* DefaultContainerRepositoryAccountName (karakterlánc)
* DefaultContainerRepositoryPassword (karakterlánc)
* IsDefaultContainerRepositoryPasswordEncrypted (bool)
* DefaultContainerRepositoryPasswordType (karakterlánc)

Itt látható egy példa arra, hogy mire lehet felvenni a `Hosting` fájl ClusterManifestTemplate.jsszakaszának részében. A `Hosting` szakasz a fürt létrehozásakor vagy később is hozzáadható a konfiguráció frissítéséhez. További információ: az [azure Service Fabric-fürt beállításainak módosítása](service-fabric-cluster-fabric-settings.md) és az [Azure Service Fabric alkalmazás-titkok kezelése](service-fabric-application-secret-management.md)

```json
"fabricSettings": [
    ...,
    {
        "name": "Hosting",
        "parameters": [
          {
            "name": "EndpointProviderEnabled",
            "value": "true"
          },
          {
            "name": "DefaultContainerRepositoryAccountName",
            "value": "someusername"
          },
          {
            "name": "DefaultContainerRepositoryPassword",
            "value": "somepassword"
          },
          {
            "name": "IsDefaultContainerRepositoryPasswordEncrypted",
            "value": "false"
          },
          {
            "name": "DefaultContainerRepositoryPasswordType",
            "value": "PlainText"
          }
        ]
      },
]
```

## <a name="use-tokens-as-registry-credentials"></a>Tokenek használata beállításjegyzékbeli hitelesítő adatokként

Service Fabric támogatja a tokenek használatát hitelesítő adatként a tárolók képeinek letöltéséhez.  Ez a szolgáltatás kihasználja a mögöttes virtuálisgép-méretezési csoport *felügyelt identitását* , hogy hitelesítse magát a beállításjegyzékben, így nincs szükség a felhasználói hitelesítő adatok kezeléséhez.  További információért lásd: [felügyelt identitások az Azure-erőforrásokhoz](../active-directory/managed-identities-azure-resources/overview.md) .  A szolgáltatás használatához a következő lépések szükségesek:

1. Győződjön meg arról, hogy a *rendszerhez rendelt felügyelt identitás* engedélyezve van a virtuális gépen.

    ![Azure Portal: virtuálisgép-méretezési csoport identitási beállításának létrehozása](./media/configure-container-repository-credentials/configure-container-repository-credentials-acr-iam.png)

> [!NOTE]
> A felhasználó által hozzárendelt felügyelt identitás esetén ugorja át ezt a lépést. Az alábbi további lépések ugyanúgy működnek, amíg a méretezési csoport csak egyetlen felhasználó által hozzárendelt felügyelt identitáshoz van társítva.

2. Adja meg az engedélyeket a virtuálisgép-méretezési csoportnak a lemezképek lekérése/olvasása céljából a beállításjegyzékből. A Azure Portal Azure Container Registry Access Control (IAM) paneljén vegyen fel egy *szerepkör-hozzárendelést* a virtuális géphez:

    ![VM-tag hozzáadása az ACR-hez](./media/configure-container-repository-credentials/configure-container-repository-credentials-vmss-identity.png)

3. Ezután módosítsa az alkalmazás jegyzékfájlját. A `ContainerHostPolicies` szakaszban adja hozzá az attribútumot `‘UseTokenAuthenticationCredentials=”true”` .

    ```xml
      <ServiceManifestImport>
          <ServiceManifestRef ServiceManifestName="NodeServicePackage" ServiceManifestVersion="1.0"/>
      <Policies>
        <ContainerHostPolicies CodePackageRef="NodeService.Code" Isolation="process" UseTokenAuthenticationCredentials="true">
          <PortBinding ContainerPort="8905" EndpointRef="Endpoint1"/>
        </ContainerHostPolicies>
        <ResourceGovernancePolicy CodePackageRef="NodeService.Code" MemoryInMB="256"/>
      </Policies>
      </ServiceManifestImport>
    ```

    > [!NOTE]
    > A True ( `UseDefaultRepositoryCredentials` igaz) érték TRUE (igaz) értékre van állítva az `UseTokenAuthenticationCredentials` üzembe helyezés során.

## <a name="next-steps"></a>Következő lépések

* További információ a [tároló-beállításjegyzék hitelesítéséről](../container-registry/container-registry-authentication.md).
