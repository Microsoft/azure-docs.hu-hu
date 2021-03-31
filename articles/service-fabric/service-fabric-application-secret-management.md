---
title: Az Azure Service Fabric alkalmazás titkainak kezelése
description: Megtudhatja, hogyan védheti meg a titkos értékeket egy Service Fabric alkalmazásban (platform – agnosztikus).
ms.topic: conceptual
ms.date: 01/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: a11869c3b606ed9e74ce4f598109139fa1bb4164
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "89012823"
---
# <a name="manage-encrypted-secrets-in-service-fabric-applications"></a>Titkosított titkok kezelése Service Fabric alkalmazásokban
Ez az útmutató végigvezeti a Service Fabric alkalmazásban található titkok kezelésének lépésein. A titkok lehetnek bármilyen bizalmas információk, például a tárolási kapcsolatok karakterláncai, jelszavai vagy más olyan értékek, amelyeket nem szabad egyszerű szövegben kezelni.

A Service Fabric alkalmazásokban a titkosított titkos kulcsok három lépést foglalnak magukban:
* Titkosítási tanúsítvány beállítása és a titkok titkosítása.
* Titkosított titkos kódok megadását egy alkalmazásban.
* Titkosítatlan titkok visszafejtése a szolgáltatási kódból.

## <a name="set-up-an-encryption-certificate-and-encrypt-secrets"></a>Titkosítási tanúsítvány beállítása és a titkos kulcsok titkosítása
A titkosítási tanúsítvány beállítása és a titkok titkosítása a Windows és a Linux között változhat.
* [Titkosítási tanúsítvány beállítása és a titkok titkosítása Windows-fürtökön.][secret-management-windows-specific-link]
* [Titkosítási tanúsítvány beállítása és a titkok titkosítása Linux-fürtökön.][secret-management-linux-specific-link]

## <a name="specify-encrypted-secrets-in-an-application"></a>Titkosított titkok meghatározása egy alkalmazásban
Az előző lépés leírja, hogyan titkosíthatja a titkos kódot egy tanúsítvánnyal, és hogyan hozhat létre Base-64 kódolású karakterláncot az alkalmazásokban való használathoz. Ez az alapszintű 64-kódolású karakterlánc a szolgáltatás Settings.xml titkosított [paramétereként][parameters-link] vagy egy szolgáltatás ServiceManifest.xml titkosított [környezeti változóként][environment-variables-link] adható meg.

A szolgáltatás Settings.xml konfigurációs fájljában adja meg a titkosított [paramétert][parameters-link] a következő `IsEncrypted` értékkel `true` :

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MySettings">
    <Parameter Name="MySecret" IsEncrypted="true" Value="I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=" />
  </Section>
</Settings>
```
A szolgáltatás ServiceManifest.xml fájljában adja meg a titkosított [környezeti változót][environment-variables-link] a következő `Type` értékkel `Encrypted` :
```xml
<CodePackage Name="Code" Version="1.0.0">
  <EnvironmentVariables>
    <EnvironmentVariable Name="MyEnvVariable" Type="Encrypted" Value="I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=" />
  </EnvironmentVariables>
</CodePackage>
```

A titkokat a Service Fabric alkalmazásban is fel kell venni egy tanúsítvány megadásával az alkalmazás jegyzékfájljában. Adjon hozzá egy **SecretsCertificate** elemet a **ApplicationManifest.xmlhoz** , és adja meg a kívánt tanúsítvány ujjlenyomatát.

```xml
<ApplicationManifest … >
  ...
  <Certificates>
    <SecretsCertificate Name="MyCert" X509FindType="FindByThumbprint" X509FindValue="[YourCertThumbrint]"/>
  </Certificates>
</ApplicationManifest>
```
> [!NOTE]
> Ha olyan alkalmazást aktivál, amely egy SecretsCertificate határoz meg, Service Fabric megkeresi a megfelelő tanúsítványt, és megadja azt az identitást, amelyet az alkalmazás a tanúsítvány titkos kulcsához tartozó teljes engedélyekkel futtat. A Service Fabric a tanúsítványokat is figyeli a változásokhoz, majd ennek megfelelően újra alkalmazza az engedélyeket. A köznapi névvel deklarált tanúsítványok változásainak észleléséhez Service Fabric egy időszakos feladatot futtat, amely megkeresi az összes megfelelő tanúsítványt, és összehasonlítja azt a ujjlenyomatai megfelelnek gyorsítótárazott listájával. Amikor új ujjlenyomatot észlel, az azt jelenti, hogy megújítják a tulajdonos által kiadott tanúsítványt. A feladat percenként egyszer fut a fürt mindegyik csomópontján.
>
> Míg a SecretsCertificate engedélyezi a tárgyon alapuló deklarációkat, ügyeljen arra, hogy a titkosított beállítások a beállítás az ügyfélen való titkosításához használt kulcspár számára legyenek kötve. Győződjön meg arról, hogy az eredeti titkosítási tanúsítvány (vagy ezzel egyenértékű) megfelel a tárgy-alapú deklarációnak, és hogy telepítve van-e, beleértve a hozzá tartozó titkos kulcsot is a fürt minden olyan csomópontján, amely az alkalmazást futtathatja. Minden, a tárgyon alapuló deklarációnak megfelelő, érvényes tanúsítvány, amely az eredeti titkosítási tanúsítvánnyal egyenértékűnek számít.
>

### <a name="inject-application-secrets-into-application-instances"></a>Alkalmazási titkok behelyezése az alkalmazás példányaiba
Ideális esetben a különböző környezetekben történő üzembe helyezésnek a lehető legautomatizáltnak kell lennie. Ezt úgy teheti meg, hogy a titkos titkosítást egy Build környezetben hajtja végre, és a titkosított titkokat paraméterekként adja meg az alkalmazás példányainak létrehozásakor.

#### <a name="use-overridable-parameters-in-settingsxml"></a>Overridable paraméterek használata a Settings.xmlban
A Settings.xml konfigurációs fájl lehetővé teszi az alkalmazások létrehozási idején elérhető Overridable paraméterek használatát. A `MustOverride` paraméter értékének megadása helyett használja az attribútumot:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<Settings xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MySettings">
    <Parameter Name="MySecret" IsEncrypted="true" Value="" MustOverride="true" />
  </Section>
</Settings>
```

A Settings.xml értékeinek felülbírálásához deklaráljon egy felülbírálási paramétert a szolgáltatáshoz a ApplicationManifest.xmlban:

```xml
<ApplicationManifest ... >
  <Parameters>
    <Parameter Name="MySecret" DefaultValue="" />
  </Parameters>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="Stateful1Pkg" ServiceManifestVersion="1.0.0" />
    <ConfigOverrides>
      <ConfigOverride Name="Config">
        <Settings>
          <Section Name="MySettings">
            <Parameter Name="MySecret" Value="[MySecret]" IsEncrypted="true" />
          </Section>
        </Settings>
      </ConfigOverride>
    </ConfigOverrides>
  </ServiceManifestImport>
 ```

Az érték mostantól az alkalmazás egy példányának létrehozásakor is megadható *Application paraméterként* . Az alkalmazás-példányok létrehozásához a PowerShell vagy a C# nyelven írt parancsfájl használatával lehet egyszerű integrációt létrehozni egy összeállítási folyamat során.

A PowerShell használatával a paramétert a parancs a következő `New-ServiceFabricApplication` [kivonatoló táblázatként](/previous-versions/windows/it-pro/windows-powershell-1.0/ee692803(v=technet.10))biztosítja:

```powershell
New-ServiceFabricApplication -ApplicationName fabric:/MyApp -ApplicationTypeName MyAppType -ApplicationTypeVersion 1.0.0 -ApplicationParameter @{"MySecret" = "I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM="}
```

A C# használatával az alkalmazás paramétereinek megadása a `ApplicationDescription` következőképpen történik `NameValueCollection` :

```csharp
FabricClient fabricClient = new FabricClient();

NameValueCollection applicationParameters = new NameValueCollection();
applicationParameters["MySecret"] = "I6jCCAeYCAxgFhBXABFxzAt ... gNBRyeWFXl2VydmjZNwJIM=";

ApplicationDescription applicationDescription = new ApplicationDescription(
    applicationName: new Uri("fabric:/MyApp"),
    applicationTypeName: "MyAppType",
    applicationTypeVersion: "1.0.0",
    applicationParameters: applicationParameters)
);

await fabricClient.ApplicationManager.CreateApplicationAsync(applicationDescription);
```

## <a name="decrypt-encrypted-secrets-from-service-code"></a>Titkosított titkok visszafejtése a szolgáltatás kódjából
A [Paraméterek][parameters-link] és [környezeti változók][environment-variables-link] elérésére szolgáló API-k lehetővé teszik a titkosított értékek egyszerű visszafejtését. Mivel a titkosított sztring információt tartalmaz a titkosításhoz használt tanúsítványról, nem kell manuálisan megadnia a tanúsítványt. A tanúsítványt csak arra a csomópontra kell telepíteni, amelyen a szolgáltatás fut.

```csharp
// Access decrypted parameters from Settings.xml
ConfigurationPackage configPackage = FabricRuntime.GetActivationContext().GetConfigurationPackageObject("Config");
bool MySecretIsEncrypted = configPackage.Settings.Sections["MySettings"].Parameters["MySecret"].IsEncrypted;
if (MySecretIsEncrypted)
{
    SecureString MySecretDecryptedValue = configPackage.Settings.Sections["MySettings"].Parameters["MySecret"].DecryptValue();
}

// Access decrypted environment variables from ServiceManifest.xml
// Note: you do not have to call any explicit API to decrypt the environment variable.
string MyEnvVariable = Environment.GetEnvironmentVariable("MyEnvVariable");
```

## <a name="next-steps"></a>Következő lépések
* Service Fabric [Secrets áruház](service-fabric-application-secret-store.md) 
* További információ az [alkalmazások és szolgáltatások biztonságáról](service-fabric-application-and-service-security.md)

<!-- Links -->
[parameters-link]:service-fabric-how-to-parameterize-configuration-files.md
[environment-variables-link]: service-fabric-how-to-specify-environment-variables.md
[secret-management-windows-specific-link]: service-fabric-application-secret-management-windows.md
[secret-management-linux-specific-link]: service-fabric-application-secret-management-linux.md
[service fabric secrets store]: service-fabric-application-secret-store.md
