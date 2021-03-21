---
ms.openlocfilehash: b1be5e903994f0a2c4a3d457f0c2bb5572a889c3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98663029"
---
## <a name="add-secret-manager"></a>Secret Manager hozzáadása

A Secret Manager nevű eszköz bizalmas adatokat tárol a projekt fáján kívüli fejlesztési munkához. Ez a módszer megakadályozza, hogy véletlenül megossza az alkalmazás forráskódbeli titkos kódjait. A következő lépések végrehajtásával engedélyezheti a Secret Manager használatát a ASP.NET Core projektben:

#### <a name="net-5x"></a>[.NET 5. x](#tab/core5x)

Navigáljon a projekt gyökérkönyvtárához, és futtassa a következő parancsot a titkok tárolásának engedélyezéséhez a projektben:

```dotnetcli
dotnet user-secrets init
```

A `UserSecretsId` rendszer hozzáadja a GUID azonosítót tartalmazó elemet a *. csproj* fájlhoz:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
    
    <PropertyGroup>
        <TargetFramework>net5.0</TargetFramework>
        <UserSecretsId>79a3edd0-2092-40a2-a04d-dcb46d5ca9ed</UserSecretsId>
    </PropertyGroup>

</Project>
```

#### <a name="net-core-3x"></a>[.NET Core 3. x](#tab/core3x)

Navigáljon a projekt gyökérkönyvtárához, és futtassa a következő parancsot a titkok tárolásának engedélyezéséhez a projektben:

```dotnetcli
dotnet user-secrets init
```

A `UserSecretsId` rendszer hozzáadja a GUID azonosítót tartalmazó elemet a *. csproj* fájlhoz:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
    
    <PropertyGroup>
        <TargetFramework>netcoreapp3.1</TargetFramework>
        <UserSecretsId>79a3edd0-2092-40a2-a04d-dcb46d5ca9ed</UserSecretsId>
    </PropertyGroup>

</Project>
```

#### <a name="net-core-2x"></a>[.NET Core 2. x](#tab/core2x)

1. Nyissa meg a *. csproj* fájlt.

1. Adjon hozzá egy `UserSecretsId` elemet a *. csproj* fájlhoz az itt látható módon. Használhatja ugyanazt a GUID azonosítót, vagy lecserélheti ezt az értéket a saját értékére is.

    ```xml
    <Project Sdk="Microsoft.NET.Sdk.Web">
    
        <PropertyGroup>
            <TargetFramework>netcoreapp2.1</TargetFramework>
            <UserSecretsId>79a3edd0-2092-40a2-a04d-dcb46d5ca9ed</UserSecretsId>
        </PropertyGroup>
    
        <ItemGroup>
            <PackageReference Include="Microsoft.AspNetCore.App" />
            <PackageReference Include="Microsoft.AspNetCore.Razor.Design" Version="2.1.2" PrivateAssets="All" />
        </ItemGroup>
    
    </Project>
    ```
    
1. Mentse a *. csproj* fájlt.

---

> [!TIP]
> A Secret Managerrel kapcsolatos további információkért lásd: az [alkalmazás-titkok biztonságos tárolása a fejlesztésben ASP.net Core](/aspnet/core/security/app-secrets).
