---
title: 'Oktatóanyag: dinamikus konfiguráció használata .NET Core-alkalmazásokban'
titleSuffix: Azure App Configuration
description: Ebből az oktatóanyagból megtudhatja, hogyan frissítheti dinamikusan a .NET Core-alkalmazások konfigurációs információit
services: azure-app-configuration
documentationcenter: ''
author: abarora
manager: zhenlan
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.custom: devx-track-csharp
ms.topic: tutorial
ms.date: 07/01/2019
ms.author: abarora
ms.openlocfilehash: 95c48bded224a40879328dba5861b3564f0ebcf8
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2021
ms.locfileid: "99979861"
---
# <a name="tutorial-use-dynamic-configuration-in-a-net-core-app"></a>Oktatóanyag: dinamikus konfiguráció használata .NET Core-alkalmazásokban

Az App Configuration .NET Core ügyféloldali függvénytár támogatja a konfiguráció igény szerinti frissítését anélkül, hogy újra kellene indítani az alkalmazást. Ezt úgy teheti meg, hogy először beolvassa a `IConfigurationRefresher` konfigurációs szolgáltató beállításai közül a példányát, majd a `TryRefreshAsync` kódban bárhol meghívja a példányt.

A beállítások frissítésének megtartásához és a konfigurációs tárolóhoz való túl sok hívás elkerüléséhez minden beállításhoz gyorsítótárat kell használni. Amíg a beállítás gyorsítótárazott értéke lejárt, a frissítési művelet nem frissíti az értéket, még akkor is, ha az érték módosult a konfigurációs tárolóban. Az egyes kérések alapértelmezett lejárati ideje 30 másodperc, de szükség esetén felül lehet bírálni.

Ez az oktatóanyag bemutatja, hogyan valósítható meg a dinamikus konfigurációs frissítések a kódban. Ez a gyors üzembe helyezési útmutatóban bemutatott alkalmazásra épül. A folytatás előtt fejezze be [a .net Core-alkalmazás létrehozása az alkalmazás konfigurációjával](./quickstart-dotnet-core-app.md) című részből.

Az oktatóanyag lépéseihez bármilyen Kódszerkesztő használható. A [Visual Studio Code](https://code.visualstudio.com/) egy kiváló lehetőség, amely a Windows, MacOS és Linux platformokon érhető el.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Állítsa be a .NET Core-alkalmazást úgy, hogy az alkalmazás konfigurációs tárolójának változásaira reagálva frissítse a konfigurációját.
> * Használja fel a legújabb konfigurációt az alkalmazásban.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez telepítse a [.net Core SDK](https://dotnet.microsoft.com/download).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="reload-data-from-app-configuration"></a>Adatok újratöltése az alkalmazás konfigurációjától

Nyissa meg a *program.cs* , és frissítse a fájlt, és adjon hozzá egy hivatkozást a `System.Threading.Tasks` névtérhez, és adja meg a frissítési konfigurációt a `AddAzureAppConfiguration` metódusban, valamint a manuális frissítést a `TryRefreshAsync` metódus használatával.

```csharp
using System;
using System.Threading.Tasks;

namespace TestConsole
{
class Program
{
    private static IConfiguration _configuration = null;
    private static IConfigurationRefresher _refresher = null;

    static void Main(string[] args)
    {
        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(options =>
        {
            options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
                    .ConfigureRefresh(refresh =>
                    {
                        refresh.Register("TestApp:Settings:Message")
                               .SetCacheExpiration(TimeSpan.FromSeconds(10));
                    });
                    
                    _refresher = options.GetRefresher();
        });

        _configuration = builder.Build();
        PrintMessage().Wait();
    }

    private static async Task PrintMessage()
    {
        Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");

        // Wait for the user to press Enter
        Console.ReadLine();

        await _refresher.TryRefreshAsync();
        Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");
    }
}
}
```

A `ConfigureRefresh` metódussal adhatja meg azokat a beállításokat, amelyeket a konfigurációs adatainak az alkalmazás konfigurációs tárolójával való frissítéséhez használ a frissítési művelet elindításakor. A metódus `IConfigurationRefresher` Meghívási metódusával lekérhető egy példány `GetRefresher` `AddAzureAppConfiguration` , és az `TryRefreshAsync` adott példány metódusa használható a kód bármely pontjára a frissítési művelet elindításához.
    
> [!NOTE]
> A konfigurációs beállítás alapértelmezett gyorsítótár-lejárati ideje 30 másodperc, de felülbírálható úgy, hogy meghívja a metódust az `SetCacheExpiration` inicializálási beállítások argumentumként megadott metódusban `ConfigureRefresh` .

## <a name="build-and-run-the-app-locally"></a>Az alkalmazás helyi létrehozása és futtatása

1. Állítson be egy **ConnectionString** nevű környezeti változót, és állítsa be az alkalmazás konfigurációs tárolójának hozzáférési kulcsára. Ha a Windows-parancssort használja, futtassa a következő parancsot, és indítsa újra a parancssort, hogy a módosítás érvénybe lépjen:

    ```console
     setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Ha a Windows PowerShellt használja, futtassa a következő parancsot:

    ```powershell
     $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    Ha macOS vagy Linux rendszert használ, futtassa a következő parancsot:

    ```console
     export ConnectionString='connection-string-of-your-app-configuration-store'
    ```

1. Futtassa a következő parancsot a konzol alkalmazás létrehozásához:

    ```console
     dotnet build
    ```

1. A létrehozás sikeres befejezése után futtassa a következő parancsot az alkalmazás helyi futtatásához:

    ```console
     dotnet run
    ```

    ![Gyorsindítás alkalmazás elindítása helyi](./media/quickstarts/dotnet-core-app-run.png)

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **minden erőforrás** lehetőséget, majd válassza ki a gyors útmutatóban létrehozott app Configuration Store-példányt.

1. Válassza a **Configuration Explorer** lehetőséget, és frissítse a következő kulcsok értékeit:

    | Kulcs | Érték |
    |---|---|
    | TestApp: beállítások: üzenet | Adatok az Azure-alkalmazás konfigurációjában – frissítve |

1. Nyomja le az ENTER billentyűt a frissítés elindításához és a frissített érték kinyomtatásához a parancssorban vagy a PowerShell-ablakban.

    ![Rövid útmutató alkalmazás helyi frissítése](./media/quickstarts/dotnet-core-app-run-refresh.png)
    
    > [!NOTE]
    > Mivel a gyorsítótár lejárati ideje 10 másodpercre van állítva a `SetCacheExpiration` metódusnak a frissítési művelethez való megadása során, a rendszer csak akkor frissíti a konfigurációs beállítás értékét, ha az adott beállítás utolsó frissítése óta legalább 10 másodperc eltelt.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban engedélyezte a .NET Core-alkalmazást, hogy dinamikusan frissítse a konfigurációs beállításokat az alkalmazás konfigurációjában. Ha meg szeretné tudni, hogyan használható az Azure felügyelt identitása az alkalmazás-konfigurációhoz való hozzáférés egyszerűsítéséhez, folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [Felügyelt identitások integrációja](./howto-integrate-azure-managed-service-identity.md)
