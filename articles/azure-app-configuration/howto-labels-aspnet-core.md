---
title: Környezeti konfiguráció használata
titleSuffix: Azure App Configuration
description: A címkék használatával adja meg a környezet konfigurációs értékeit.
ms.service: azure-app-configuration
author: AlexandraKemperMS
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 3/12/2020
ms.author: alkemper
ms.openlocfilehash: 84286df063994f3def15079cb9b190550d5bd977
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96929616"
---
# <a name="use-labels-to-enable-configurations-for-different-environments"></a>Címkék használata különböző környezetek konfigurációinak engedélyezéséhez

Számos alkalmazásnak különböző konfigurációkat kell használnia a különböző környezetekhez. Tegyük fel, hogy egy alkalmazás olyan konfigurációs értékkel rendelkezik, amely meghatározza a háttér-adatbázishoz használandó kapcsolati karakterláncot. Az alkalmazás fejlesztői az éles környezetben használt másik adatbázist használnak. Az alkalmazás által használt adatbázis-kapcsolódási karakterláncnak meg kell változnia, mivel az alkalmazás a fejlesztéstől a Termelésig mozog.

Az Azure-alkalmazások konfigurációjában a *címkék* használatával különböző értékeket határozhat meg ugyanahhoz a kulcshoz. Megadhat például egy olyan kulcsot, amely különböző értékekkel rendelkezik a fejlesztéshez és a gyártáshoz. Megadhatja, hogy melyik címkét kell betölteni az alkalmazás-konfigurációhoz való csatlakozáskor.

A funkció bemutatásához módosítania kell a rövid útmutatóban létrehozott webalkalmazást [: hozzon létre egy ASP.net Core alkalmazást az Azure-alkalmazás konfigurációjában](./quickstart-aspnet-core-app.md) , hogy különböző konfigurációs beállításokat használjon a fejlesztéshez és a termeléshez. A folytatás előtt fejezze be a gyors üzembe helyezést.

## <a name="specify-a-label-when-adding-a-configuration-value"></a>Címke megadása konfigurációs érték hozzáadásakor

A Azure Portal lépjen a **Configuration Explorer** elemre, és keresse meg a *TestApp: Settings: FontColor* kulcsot, amelyet a rövid útmutatóban hozott létre. Válassza ki a helyi menüt, majd válassza az **érték hozzáadása** lehetőséget.

> [!div class="mx-imgBorder"]
> ![Érték menüpont hozzáadása](media/labels-add-value.png)

Az **érték hozzáadása** képernyőn adja meg a **vörös** és a **fejlesztési** **címke** **értékét** . Hagyja üresen a **tartalom típusát** . Kattintson az **Alkalmaz** gombra.

## <a name="load-configuration-values-with-a-specified-label"></a>Konfigurációs értékek betöltése megadott címkével

Alapértelmezés szerint az Azure app Configuration csak a címke nélküli konfigurációs értékeket tölti be. Ha meghatározta a konfigurációs értékek címkéit, meg kell adnia az alkalmazás-konfigurációhoz való csatlakozáskor használni kívánt címkéket.

Az előző szakaszban létrehozott egy másik konfigurációs értéket a fejlesztési környezethez. A változó használatával `HostingEnvironment.EnvironmentName` dinamikusan meghatározhatja, hogy az alkalmazás jelenleg milyen környezetben fut. További információ: [több környezet használata ASP.net Coreban](/aspnet/core/fundamentals/environments).

Az aktuális környezetnek megfelelő címkével töltse be a konfigurációs értékeket a környezet nevének a metódusba való átadásával `Select` :

```csharp
        public static IHostBuilder CreateHostBuilder(string[] args) =>
            Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(options =>
                    options
                        .Connect(Environment.GetEnvironmentVariable("AppConfigConnectionString"))
                        // Load configuration values with no label
                        .Select(KeyFilter.Any, LabelFilter.Null)
                        // Override with any configuration values specific to current hosting env
                        .Select(KeyFilter.Any, hostingContext.HostingEnvironment.EnvironmentName)
                );
            })
            .UseStartup<Startup>());
```

> [!IMPORTANT]
> Az előző kódrészlet egy nevű környezeti változóból tölti be az alkalmazás konfigurációs kapcsolatok sztringjét `AppConfigConnectionString` . Ügyeljen arra, hogy a környezeti változó megfelelően legyen beállítva.

A `Select` metódust kétszer kell meghívni. Az első alkalommal a címke nélküli konfigurációs értékeket tölti be. Ezután betölti a konfigurációs értékeket az aktuális környezetnek megfelelő címkével. Ezek a környezet-specifikus értékek felülbírálják a címkével nem rendelkező megfelelő értékeket. Nem kell meghatároznia az egyes kulcsokhoz tartozó környezeti értékeket. Ha egy kulcs nem rendelkezik olyan értékkel, amely az aktuális környezetnek megfelelő címkével rendelkezik, a címke nélküli értéket használja.

## <a name="test-in-different-environments"></a>Tesztelés különböző környezetekben

Nyissa meg a `launchSettings.json` fájlt a `Properties` könyvtár alatt. Keresse meg a `config` bejegyzést a alatt `profiles` . A `environmentVariables` szakaszban állítsa be a változót a következőre: `ASPNETCORE_ENVIRONMENT` `Production` .

Az új értékek beállítása után hozza létre és futtassa az alkalmazást.

```dotnetcli
dotnet build
dotnet run
```

A böngészőben nyissa meg a következőt: `http://localhost:5000` . Figyelje meg, hogy a betűszín fekete színű.

![Éles konfigurációval futó webalkalmazás](media/labels-website-prod.png)

Módosítsa `launchSettings.json` a változó értékét a következőre: `ASPNETCORE_ENVIRONMENT` `Development` . Futtassa ismét az `dotnet run` parancsot. 

Megfigyelheti, hogy a betűszín most piros színnel jelenik meg. Ennek az az oka, hogy az alkalmazás már használja a `TestApp:Settings:FontColor` `Development` címkével ellátott értéket. Az összes többi konfigurációs érték ugyanaz marad, mint a termelési értékük.

![Fejlesztői konfigurációval futó webalkalmazás](media/labels-website-dev.png)

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Konfiguráció a ASP.NET Coreban](/aspnet/core/fundamentals/configuration/)
