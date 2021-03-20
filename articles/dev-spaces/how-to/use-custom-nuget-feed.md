---
title: Egyéni NuGet-hírcsatorna használata
services: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 07/17/2019
ms.topic: conceptual
description: Egyéni NuGet-hírcsatorna használata NuGet-csomagok eléréséhez és használatához az Azure fejlesztői tárhelyén.
keywords: Docker, Kubernetes, Azure, AK, Azure Container Service, tárolók
manager: gwallace
ms.openlocfilehash: d60d7142d9b9979be76eebb3d324a448bd76638f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91960218"
---
# <a name="use-a-custom-nuget-feed-with-azure-dev-spaces"></a>Egyéni NuGet-hírcsatorna használata az Azure dev Spaces használatával

[!INCLUDE [Azure Dev Spaces deprecation](../../../includes/dev-spaces-deprecation.md)]

A NuGet-hírcsatornák kényelmes módszert biztosítanak a projektek csomagjainak belefoglalására. Az Azure dev Spaces szolgáltatásnak el kell érnie ezt a hírcsatornát ahhoz, hogy a függőségeket megfelelően lehessen telepíteni a Docker-tárolóba.

## <a name="set-up-a-nuget-feed"></a>NuGet-hírcsatorna beállítása

Adjon hozzá egy [csomag-referenciát](/nuget/consume-packages/package-references-in-project-files) a függőséghez a `*.csproj` csomópont alatt található fájlban `PackageReference` . Például:

```xml
<ItemGroup>
    <!-- ... -->
    <PackageReference Include="Contoso.Utility.UsefulStuff" Version="3.6.0" />
    <!-- ... -->
</ItemGroup>
```

Hozzon létre egy [NuGet.Config](/nuget/reference/nuget-config-file) fájlt a Project mappában, és állítsa be a `packageSources` és a NuGet- `packageSourceCredentials` hírcsatornához tartozó szakaszt. A `packageSources` szakasz tartalmazza a hírcsatorna URL-címét, amelynek elérhetőnek kell lennie az AK-fürtből. A a `packageSourceCredentials` hírcsatorna eléréséhez szükséges hitelesítő adatok. Például:

```xml
<packageSources>
    <add key="Contoso" value="https://contoso.com/packages/" />
</packageSources>

<packageSourceCredentials>
    <Contoso>
        <add key="Username" value="user@contoso.com" />
        <add key="ClearTextPassword" value="33f!!lloppa" />
    </Contoso>
</packageSourceCredentials>
```

Frissítse a Dockerfiles, és másolja a `NuGet.Config` fájlt a rendszerképbe. Például:

```console
COPY ["<project folder>/NuGet.Config", "./NuGet.Config"]
```

> [!TIP]
> Windows, `NuGet.Config` ,, `Nuget.Config` és `nuget.config` minden érvényes fájlnevet működik. Linux rendszeren a `NuGet.Config` fájl csak érvényes fájlnév. Mivel az Azure dev Spaces a Docker és a Linux szolgáltatást használja, ezt a fájlt kell megnevezni `NuGet.Config` . Az elnevezést manuálisan vagy a futtatásával is kijavíthatja `dotnet restore --configfile nuget.config` .


Ha a git-t használja, nem kell a NuGet-csatornához tartozó hitelesítő adatokkal rendelkeznie. Adja hozzá `NuGet.Config` a `.gitignore` projektet a projekthez, hogy a `NuGet.Config` fájl ne legyen felvéve a verziókövetésba. Az Azure dev Spaces-nek szüksége lesz erre a fájlra a tároló képfordítási folyamata során, de alapértelmezés szerint az a `.gitignore` és a szinkronizálás során definiált szabályokat is figyelembe veszi `.dockerignore` . Ha módosítani szeretné az alapértelmezett beállítást, és engedélyezi az Azure dev Spaces számára a fájl szinkronizálását `NuGet.Config` , frissítse a `azds.yaml` fájlt:

```yaml
build:
useGitIgnore: true
ignore:
- "!NuGet.Config"
```

Ha nem a git-t használja, kihagyhatja ezt a lépést.

Amikor legközelebb a `azds up` `F5` Visual Studio Code-ban vagy a Visual Studióban futtatja, az Azure dev Spaces szinkronizálja a `NuGet.Config` fájlt a csomagok függőségeinek telepítéséhez.

## <a name="next-steps"></a>Következő lépések

További információ a [NuGet és működéséről](/nuget/what-is-nuget).
