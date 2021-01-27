---
title: Azure Service Fabric – a Service Fabric Application kulcstartó-referenciák használata
description: Ez a cikk azt ismerteti, hogyan használható a Service-Fabric KeyVaultReference támogatása az alkalmazás titkos kulcsaihoz.
ms.topic: article
ms.date: 09/20/2019
ms.openlocfilehash: a0e4ef0decae8cc9ab4dc5f8c69dfef854af81f3
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98898596"
---
# <a name="keyvaultreference-support-for-azure-deployed-service-fabric-applications"></a>Az Azure által telepített Service Fabric alkalmazások KeyVaultReference támogatása

A felhőalapú alkalmazások készítése során gyakran előforduló kihívás a titkok biztonságos elosztása az alkalmazásokban. Előfordulhat például, hogy egy adatbázis-kulcsot szeretne üzembe helyezni az alkalmazásban anélkül, hogy a kulcsot a folyamat során vagy a kezelőben tenné. Service Fabric a KeyVaultReference-támogatás lehetővé teszi, hogy a titkokat egyszerűen üzembe helyezheti az alkalmazásaiban, a Key Vault tárolt titok URL-címére hivatkozva. Service Fabric kezeli a titkos kulcs beolvasását az alkalmazás felügyelt identitásának nevében, és aktiválja az alkalmazást a titkos kulccsal.

> [!NOTE]
> A Service Fabric alkalmazások KeyVaultReference-támogatása a GA (előzetes verzió), amely a Service Fabric 7,2-es CU5-es verziójától kezdődően érhető el. A funkció használata előtt ajánlott erre a verzióra frissíteni.

> [!NOTE]
> Service Fabric alkalmazások KeyVaultReference-támogatása csak a [verzióval](../key-vault/general/about-keys-secrets-certificates.md#objects-identifiers-and-versioning) ellátott titkokat támogatja. A verzióval nem rendelkező titkok nem támogatottak. A Key Vaultnak ugyanahhoz az előfizetéshez kell esnie, mint a Service Fabric-fürtnek. 

## <a name="prerequisites"></a>Előfeltételek

- Felügyelt identitás Service Fabric alkalmazásokhoz

    Service Fabric a KeyVaultReference-támogatás egy alkalmazás felügyelt identitásával kéri le a titkos kulcsokat az alkalmazás nevében, így az alkalmazást a segítségével kell központilag telepítenie, és hozzá kell rendelnie egy felügyelt identitást. Ezt a [dokumentumot](concepts-managed-identity.md) követve engedélyezheti az alkalmazás felügyelt identitását.

- Központi titkok tárolója (CSS).

    A központi titkok tárolója (CSS) Service Fabric titkosított helyi titkok gyorsítótára. Ez a funkció CSS-t használ a titkok védeleméhez és megtartásához a Key Vaultból való beolvasás után. A választható rendszerszolgáltatás engedélyezése a funkció használatához is szükséges. A CSS engedélyezéséhez és konfigurálásához kövesse ezt a [dokumentumot](service-fabric-application-secret-store.md) .

- Az alkalmazás felügyelt identitás-hozzáférési engedélyének megadása a kulcstartóhoz

    Ebből a [dokumentumból](how-to-grant-access-other-resources.md) megtudhatja, hogyan biztosíthatja a felügyelt identitások hozzáférését a kulcstartóhoz. Azt is vegye figyelembe, hogy ha rendszerhez rendelt felügyelt identitást használ, a felügyelt identitás csak az alkalmazás telepítése után jön létre. Ez olyan versenyhelyzet-feltételeket hozhat létre, amelyekben az alkalmazás megpróbál hozzáférni a titkos kulcshoz, mielőtt az identitás hozzáférhessen a tárolóhoz. A rendszerhez rendelt identitás neve lesz `{cluster name}/{application name}/{service name}` .
    
## <a name="use-keyvaultreferences-in-your-application"></a>KeyVaultReferences használata az alkalmazásban
A KeyVaultReferences többféle módon is felhasználható
- [Környezeti változóként](#as-an-environment-variable)
- [Csatolva fájlként a tárolóba](#mounted-as-a-file-into-your-container)
- [A Container repository jelszavának hivatkozása](#as-a-reference-to-a-container-repository-password)

### <a name="as-an-environment-variable"></a>Környezeti változóként

```xml
<EnvironmentVariables>
      <EnvironmentVariable Name="MySecret" Type="KeyVaultReference" Value="<KeyVaultURL>"/>
</EnvironmentVariables>
```

```C#
string secret =  Environment.GetEnvironmentVariable("MySecret");
```

### <a name="mounted-as-a-file-into-your-container"></a>Csatolva fájlként a tárolóba

- Szakasz hozzáadása a settings.xml

    Paraméter definiálása `MySecret` típussal `KeyVaultReference` és értékkel `<KeyVaultURL>`

    ```xml
    <Section Name="MySecrets">
        <Parameter Name="MySecret" Type="KeyVaultReference" Value="<KeyVaultURL>"/>
    </Section>
    ```

- A ApplicationManifest.xml új szakaszának hivatkozása `<ConfigPackagePolicies>`

    ```xml
    <ServiceManifestImport>
        <Policies>
        <IdentityBindingPolicy ServiceIdentityRef="MyServiceMI" ApplicationIdentityRef="MyApplicationMI" />
        <ConfigPackagePolicies CodePackageRef="Code">
            <!--Linux container example-->
            <ConfigPackage Name="Config" SectionName="MySecrets" EnvironmentVariableName="SecretPath" MountPoint="/var/secrets"/>
            <!--Windows container example-->
            <!-- <ConfigPackage Name="Config" SectionName="dbsecrets" EnvironmentVariableName="SecretPath" MountPoint="C:\secrets"/> -->
        </ConfigPackagePolicies>
        </Policies>
    </ServiceManifestImport>
    ```

- A titkok felhasználása a szolgáltatás kódjából

    A (z) alatt felsorolt paraméterek `<Section  Name=MySecrets>` az EnvironmentVariable SecretPath által mutatott mappában lesznek. Az alábbi C# kódrészlet bemutatja, hogyan olvashatja el a keresési kifejezésként az alkalmazásból.

    ```C#
    string secretPath = Environment.GetEnvironmentVariable("SecretPath");
    using (StreamReader sr = new StreamReader(Path.Combine(secretPath, "MySecret"))) 
    {
        string secret =  sr.ReadToEnd();
    }
    ```
    > [!NOTE] 
    > A csatlakoztatási pont szabályozza azt a mappát, ahol a titkos értékeket tartalmazó fájlok csatlakoztatva lesznek.

### <a name="as-a-reference-to-a-container-repository-password"></a>A Container repository jelszavának hivatkozása

```xml
 <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="MyACRUser" Type="KeyVaultReference" Password="<KeyVaultURL>"/>
      </ContainerHostPolicies>
```

## <a name="next-steps"></a>Következő lépések

* [Azure kulcstartó – dokumentáció](../key-vault/index.yml)
* [Tudnivalók a központi titkos tárolóról](service-fabric-application-secret-store.md)
* [Az Service Fabric-alkalmazások felügyelt identitásának megismerése](concepts-managed-identity.md)
