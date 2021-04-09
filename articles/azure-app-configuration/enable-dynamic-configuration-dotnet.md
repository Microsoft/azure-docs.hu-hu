---
title: 'A .NET-keretrendszer oktatóanyaga: dinamikus konfiguráció az Azure-alkalmazás konfigurációjában'
description: Ebből az oktatóanyagból megtudhatja, hogyan frissítheti dinamikusan a .NET-keretrendszer alkalmazásaihoz tartozó konfigurációs információkat az Azure app Configuration használatával.
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.devlang: csharp
ms.custom: devx-track-csharp
ms.topic: tutorial
ms.date: 07/24/2020
ms.author: alkemper
ms.openlocfilehash: 03940a86176d0bc93c5066977fdc87de5c456060
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96932761"
---
# <a name="tutorial-use-dynamic-configuration-in-a-net-framework-app"></a>Oktatóanyag: dinamikus konfiguráció használata .NET-keretrendszerbeli alkalmazásokban

Az alkalmazás-konfiguráció .NET ügyféloldali kódtár támogatja az igény szerinti konfigurációs beállítások frissítését anélkül, hogy újra kellene indítani az alkalmazást. Ezt úgy teheti meg, hogy először beolvassa a `IConfigurationRefresher` konfigurációs szolgáltató beállításai közül a példányát, majd a `TryRefreshAsync` kódban bárhol meghívja a példányt.

A beállítások frissítésének megtartásához és a konfigurációs tárolóhoz való túl sok hívás elkerüléséhez minden beállításhoz gyorsítótárat kell használni. Amíg a beállítás gyorsítótárazott értéke lejárt, a frissítési művelet nem frissíti az értéket, még akkor is, ha az érték módosult a konfigurációs tárolóban. Az egyes kérések alapértelmezett lejárati ideje 30 másodperc, de szükség esetén felül lehet bírálni.

Ez az oktatóanyag bemutatja, hogyan valósítható meg a dinamikus konfigurációs frissítések a kódban. Ez a gyors üzembe helyezési útmutatóban bemutatott alkalmazásra épül. A folytatás előtt fejezze be [a .net-keretrendszerbeli alkalmazás létrehozását az alkalmazás konfigurálásával](./quickstart-dotnet-app.md) először.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Állítsa be a .NET-keretrendszerbeli alkalmazást, hogy frissítse a konfigurációját az alkalmazás konfigurációs tárolójában történt változásokra reagálva.
> * Adja meg a legújabb konfigurációt az alkalmazásban.
## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs)
- [.NET-keretrendszer 4.7.1 vagy újabb verzió](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>Alkalmazás-konfigurációs tároló létrehozása

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7.   >    >  A következő kulcs-érték párok hozzáadásához válassza a Configuration Explorer +**kulcs létrehozása-érték** elemet:

    | Kulcs | Érték |
    |---|---|
    | TestApp: beállítások: üzenet | Adatok az Azure-alkalmazás konfigurációjától |

    Most hagyja üresen a **címke** és a **tartalom típusát** .

8. Kattintson az **Alkalmaz** gombra.

## <a name="create-a-net-framework-console-app"></a>.NET-keretrendszer Console-alkalmazás létrehozása

1. Indítsa el a Visual studiót, és válassza a **fájl**  >  **új**  >  **projekt** lehetőséget.

1. A **create a New Project (új projekt létrehozása**) területen szűrje a **konzol** projekt típusát, és kattintson a **Console app (.NET-keretrendszer)** elemre. Kattintson a **Tovább** gombra.

1. Az **új projekt konfigurálása** területen adja meg a projekt nevét. A **keretrendszer** területen válassza a **.NET-keretrendszer 4.7.1** vagy újabb lehetőséget. Kattintson a **Létrehozás** lehetőségre.

## <a name="reload-data-from-app-configuration"></a>Adatok újratöltése az alkalmazás konfigurációjától
1. Kattintson a jobb gombbal a projektre, és válassza a **NuGet-csomagok kezelése** lehetőséget. A **Tallózás** lapon keresse meg és adja hozzá a *Microsoft.Extensions.Configszülő. AzureAppConfiguration* NuGet-csomagot a projekthez. Ha nem találja, jelölje be az **előzetes verzió belefoglalása** jelölőnégyzetet.

1. Nyissa meg a *program. cs programot*, és adjon hozzá egy hivatkozást a .net Core alkalmazás-konfigurációs szolgáltatóhoz.

    ```csharp
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

1. Két változó hozzáadása a konfigurációval kapcsolatos objektumok tárolásához.

    ```csharp
    private static IConfiguration _configuration = null;
    private static IConfigurationRefresher _refresher = null;
    ```

1. Frissítse a `Main` metódust az alkalmazás konfigurációjához való kapcsolódáshoz a megadott frissítési beállításokkal.

    ```csharp
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
    ```
    A `ConfigureRefresh` metódussal adhatja meg azokat a beállításokat, amelyeket a konfigurációs adatainak az alkalmazás konfigurációs tárolójával való frissítéséhez használ a frissítési művelet elindításakor. A metódus meghívásával lekérhető egy példány a metódushoz `IConfigurationRefresher` `GetRefresher` megadott beállításokkal `AddAzureAppConfiguration` , és az `TryRefreshAsync` ebben a példányban található metódus a kód bármely pontjára kiválthatja a frissítési műveletet.

    > [!NOTE]
    > A konfigurációs beállítás alapértelmezett gyorsítótár-lejárati ideje 30 másodperc, de felülbírálható úgy, hogy meghívja a metódust az `SetCacheExpiration` inicializálási beállítások argumentumként megadott metódusban `ConfigureRefresh` .

1. Adjon hozzá egy nevű metódust `PrintMessage()` , amely elindítja a konfigurációs adatok manuális frissítését az alkalmazás konfigurációjától.

    ```csharp
    private static async Task PrintMessage()
    {
        Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");

        // Wait for the user to press Enter
        Console.ReadLine();

        await _refresher.TryRefreshAsync();
        Console.WriteLine(_configuration["TestApp:Settings:Message"] ?? "Hello world!");
    }
    ```

## <a name="build-and-run-the-app-locally"></a>Az alkalmazás helyi létrehozása és futtatása

1. Állítson be egy **ConnectionString** nevű környezeti változót, és állítsa be az alkalmazás konfigurációs tárolójának hozzáférési kulcsára. Ha a Windows-parancssort használja, futtassa a következő parancsot, és indítsa újra a parancssort, hogy a módosítás érvénybe lépjen:

    ```console
        setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Ha a Windows PowerShellt használja, futtassa a következő parancsot:

    ```powershell
        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

1. Indítsa újra a Visual studiót, hogy a módosítás érvénybe lépjen. 

1. A konzol alkalmazás létrehozásához és futtatásához nyomja le a CTRL + F5 billentyűkombinációt.

    ![Alkalmazás elindítása helyi](./media/dotnet-app-run.png)

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Válassza a **minden erőforrás** lehetőséget, majd válassza ki a gyors útmutatóban létrehozott app Configuration Store-példányt.

1. Válassza a **Configuration Explorer** lehetőséget, és frissítse a következő kulcsok értékeit:

    | Kulcs | Érték |
    |---|---|
    | TestApp: beállítások: üzenet | Adatok az Azure-alkalmazás konfigurációjában – frissítve |

1. A futó alkalmazásban lépjen vissza az ENTER billentyűt a frissítés elindításához, és nyomtassa ki a frissített értéket a parancssorban vagy a PowerShell-ablakban.

    ![Alkalmazás helyi frissítése](./media/dotnet-app-run-refresh.png)
    
    > [!NOTE]
    > Mivel a gyorsítótár lejárati ideje 10 másodpercre van állítva a `SetCacheExpiration` metódusnak a frissítési művelethez való megadása során, a rendszer csak akkor frissíti a konfigurációs beállítás értékét, ha az adott beállítás utolsó frissítése óta legalább 10 másodperc eltelt.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban engedélyezte a .NET-keretrendszer alkalmazását, hogy dinamikusan frissítse a konfigurációs beállításokat az alkalmazás konfigurációjában. Ha meg szeretné tudni, hogyan használható az Azure felügyelt identitása az alkalmazás-konfigurációhoz való hozzáférés egyszerűsítéséhez, folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [Felügyelt identitások integrációja](./howto-integrate-azure-managed-service-identity.md)
