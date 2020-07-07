---
title: A szolgáltatás-jelzők a .NET-keretrendszer alkalmazásaihoz való hozzáadásának gyors útmutatója | Microsoft Docs | Microsoft Docs
description: Útmutató a funkció-jelzők a .NET-keretrendszer alkalmazásaihoz való hozzáadásához és az Azure-alkalmazások konfigurációjában való kezeléséhez
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
manager: maiye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: quickstart
ms.tgt_pltfrm: .NET
ms.workload: tbd
ms.date: 10/21/2019
ms.author: lcozzens
ms.openlocfilehash: bf2ccd93a94036c6ab5b6224456cd1f5a13239c5
ms.sourcegitcommit: 9b5c20fb5e904684dc6dd9059d62429b52cb39bc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85855008"
---
# <a name="quickstart-add-feature-flags-to-a-net-framework-app"></a>Gyors útmutató: szolgáltatás-jelzők hozzáadása .NET-keretrendszerbeli alkalmazáshoz

Ebben a rövid útmutatóban az Azure-alkalmazások konfigurációját egy .NET-keretrendszerbeli alkalmazásba foglalja bele a szolgáltatások felügyeletének végpontok közötti megvalósításához. Az alkalmazás konfigurációs szolgáltatásával központilag tárolhatja az összes funkció jelzőjét, és szabályozhatja az állapotukat. 

A .NET-szolgáltatások felügyeleti kódtárai kiterjesztik a keretrendszert a funkció-jelölő átfogó támogatásával. Ezek a kódtárak a .NET konfigurációs rendszerére épülnek. Zökkenőmentesen integrálható az alkalmazások konfigurációjával a .NET-konfigurációs szolgáltatón keresztül.

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs)
- [.NET-keretrendszer 4,8](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>Alkalmazás-konfigurációs tároló létrehozása

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Válassza a **funkció-kezelő**  >  **+ Hozzáadás** elemet a szolgáltatáshoz tartozó jelző hozzáadásához `Beta` .

    > [!div class="mx-imgBorder"]
    > ![A szolgáltatás jelölő engedélyezése Beta néven](media/add-beta-feature-flag.png)

    `label`Most hagyja meg a nem definiált lehetőséget.

## <a name="create-a-net-console-app"></a>.NET-konzol alkalmazás létrehozása

1. Indítsa el a Visual studiót, és válassza a **fájl**  >  **új**  >  **projekt**lehetőséget.

1. A **create a New Project (új projekt létrehozása**) területen szűrje a **konzol** projekt típusát, és kattintson a **Console app (.NET-keretrendszer)** elemre. Kattintson a **Tovább** gombra.

1. Az **új projekt konfigurálása**területen adja meg a projekt nevét. A **keretrendszer**területen válassza a **.net-keretrendszer 4,8** vagy újabb lehetőséget. Kattintson a **Létrehozás** lehetőségre.

## <a name="connect-to-an-app-configuration-store"></a>Kapcsolódás alkalmazás-konfigurációs tárolóhoz

1. Kattintson a jobb gombbal a projektre, és válassza a **NuGet-csomagok kezelése**lehetőséget. A **Tallózás** lapon keresse meg és adja hozzá a következő NuGet-csomagokat a projekthez. Ha nem találja őket, jelölje be az **előzetes verzió belefoglalása** jelölőnégyzetet.

    ```
    Microsoft.Extensions.DependencyInjection
    Microsoft.Extensions.Configuration.AzureAppConfiguration
    Microsoft.FeatureManagement
    ```

1. Nyissa meg a *program.cs* , és adja hozzá a következő utasításokat:

    ```csharp
    using Microsoft.Extensions.DependencyInjection;
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    using Microsoft.FeatureManagement;
    ```

1. Frissítse a `Main` metódust az alkalmazás-konfigurációhoz való kapcsolódáshoz, és adja meg a beállítást, hogy `UseFeatureFlags` lekérje a szolgáltatás jelzőit. Ekkor megjelenik egy üzenet, ha a `Beta` funkció jelzője engedélyezve van.

    ```csharp
        public static async Task Main(string[] args)
        {         
            IConfigurationRoot configuration = new ConfigurationBuilder()
                .AddAzureAppConfiguration(options =>
                {
                    options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
                           .UseFeatureFlags();
                }).Build();

            IServiceCollection services = new ServiceCollection();

            services.AddSingleton<IConfiguration>(configuration).AddFeatureManagement();

            using (ServiceProvider serviceProvider = services.BuildServiceProvider())
            {
                IFeatureManager featureManager = serviceProvider.GetRequiredService<IFeatureManager>();

                if (await featureManager.IsEnabledAsync("Beta"))
                {
                    Console.WriteLine("Welcome to the beta!");
                }
            }

            Console.WriteLine("Hello World!");
        }
    ```

## <a name="build-and-run-the-app-locally"></a>Az alkalmazás helyi létrehozása és futtatása

1. Állítson be egy **ConnectionString** nevű környezeti változót az alkalmazás konfigurációs tárolójának kapcsolati karakterláncára. Ha Windows-parancssort használ, futtassa a következő parancsot:

    ```console
        setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Ha a Windows PowerShellt használja, futtassa a következő parancsot:

    ```powershell
        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

1. Indítsa újra a Visual studiót, hogy a módosítás érvénybe lépjen. 

1. A konzol alkalmazás létrehozásához és futtatásához nyomja le a CTRL + F5 billentyűkombinációt.

    ![Az alkalmazás és a funkció jelzője engedélyezve](./media/quickstarts/dotnet-app-feature-flag.png)

## <a name="clean-up-resources"></a>Erőforrások felszabadítása

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozta a szolgáltatás jelölőjét az alkalmazás konfigurációjában, és felhasználta azt egy .NET Framework Console alkalmazással. Ha meg szeretné tudni, hogyan lehet dinamikusan frissíteni a szolgáltatás jelölőit és az egyéb konfigurációs értékeket az alkalmazás újraindítása nélkül, folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [Dinamikus konfiguráció engedélyezése](./enable-dynamic-configuration-dotnet.md)
