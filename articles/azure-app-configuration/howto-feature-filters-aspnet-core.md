---
title: Funkciós szűrők használata a feltételes funkciók jelzőjének engedélyezéséhez
titleSuffix: Azure App Configuration
description: Ismerje meg, hogyan használhatók a funkciók szűrői a feltételes funkciók jelzőjének engedélyezéséhez
ms.service: azure-app-configuration
ms.custom: devx-track-csharp
author: AlexandraKemperMS
ms.author: alkemper
ms.topic: conceptual
ms.date: 3/9/2020
ms.openlocfilehash: 39455c4bc193cce036bd169c702b5c020d53d2f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98602286"
---
# <a name="use-feature-filters-to-enable-conditional-feature-flags"></a>Funkciós szűrők használata a feltételes funkciók jelzőjének engedélyezéséhez

A funkciók jelzői lehetővé teszik az alkalmazás funkcióinak aktiválását vagy inaktiválását. Egy egyszerű funkció jelzője be vagy ki van kapcsolva. Az alkalmazás mindig ugyanúgy viselkedik. Létrehozhat például egy új funkciót a szolgáltatás jelölője mögött. Ha a funkció jelzője engedélyezve van, az összes felhasználó látja az új funkciót. A funkció jelző letiltása elrejti az új funkciót.

Ezzel szemben a _feltételes funkciók jelzője_ lehetővé teszi, hogy a szolgáltatás jelölője dinamikusan legyen engedélyezve vagy letiltva. Az alkalmazás a szolgáltatás jelző feltételeitől függően eltérően működhet. Tegyük fel, hogy először a felhasználók kis részhalmazára szeretné megjeleníteni az új funkciót. A feltételes funkciók jelzője lehetővé teszi, hogy egyes felhasználók számára engedélyezze a szolgáltatás jelölőjét, miközben mások számára letiltja azt. A _szolgáltatási szűrők_ határozzák meg a szolgáltatás jelölője állapotát minden egyes kiértékeléskor.

A `Microsoft.FeatureManagement` könyvtár három szolgáltatáskészlet-szűrőt tartalmaz:

- `PercentageFilter` engedélyezi a szolgáltatás jelölőjét a százalék alapján.
- `TimeWindowFilter` engedélyezi a szolgáltatás jelölőjét egy adott időszakban.
- `TargetingFilter` engedélyezi a szolgáltatás jelölőjét a megadott felhasználók és csoportok számára.

Létrehozhat egy saját szolgáltatáskészlet-szűrőt is, amely megvalósítja a [Microsoft. FeatureManagement. IFeatureFilter felületet](/dotnet/api/microsoft.featuremanagement.ifeaturefilter).

## <a name="registering-a-feature-filter"></a>Szolgáltatás szűrőjének regisztrálása

Ha a metódus meghívásával regisztrálja a szolgáltatás szűrőjét `AddFeatureFilter` , adja meg a kívánt szolgáltatás-szűrő típusának nevét. Például a következő kód regisztrálása `PercentageFilter` :

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllersWithViews();
    services.AddFeatureManagement().AddFeatureFilter<PercentageFilter>();
}
```

## <a name="configuring-a-feature-filter-in-azure-app-configuration"></a>Szolgáltatás-szűrő konfigurálása az Azure-alkalmazás konfigurációjában

Egyes szolgáltatási szűrők további beállításokkal rendelkeznek. A szolgáltatás például `PercentageFilter` egy százalék alapján aktiválja a szolgáltatást. Ez a beállítás határozza meg a használni kívánt százalékos arányt.

Ezeket a beállításokat konfigurálhatja az Azure-alkalmazások konfigurációjában definiált funkciók jelzői számára. Az alábbi lépéseket követve például `PercentageFilter` engedélyezheti a funkció jelzőjét a kérelmek 50%-ában egy webalkalmazásnak:

1. Kövesse a gyors üzembe helyezési útmutató [: szolgáltatás-jelzők hozzáadása egy ASP.net Core alkalmazáshoz](./quickstart-feature-flag-aspnet-core.md) webalkalmazás létrehozásához a szolgáltatás jelölőjét.

1. A Azure Portal nyissa meg a konfigurációs tárolót, és kattintson a **Feature Manager** elemre.

1. Kattintson a rövid útmutatóban létrehozott *béta* -szolgáltatás jelölő helyi menüjére. Kattintson a **Szerkesztés** gombra.

    > [!div class="mx-imgBorder"]
    > ![Bétaverzió funkciójának szerkesztése](./media/edit-beta-feature-flag.png)

1. Ha még nincs engedélyezve, a **Szerkesztés** képernyőn jelölje be a **szolgáltatás jelző engedélyezése** jelölőnégyzetet. Jelölje be a **szolgáltatás szűrőjének használata** jelölőnégyzetet, és válassza az **Egyéni** lehetőséget. 

1. A **név** mezőben válassza a *Microsoft. százalék* elemet.

    > [!div class="mx-imgBorder"]
    > ![Szolgáltatás-szűrő hozzáadása](./media/feature-flag-add-filter.png)

1. Kattintson a szolgáltatás szűrőjének neve melletti helyi menüre. Kattintson a **szűrő paramétereinek szerkesztése** elemre.

    > [!div class="mx-imgBorder"]
    > ![Szolgáltatás-szűrő paramétereinek szerkesztése](./media/feature-flags-edit-filter-parameters.png)

1. Adja meg az *érték* **nevét** és a 50 **értéket** . Az **érték** mező jelzi azon kérelmek százalékos arányát, amelyek esetében engedélyezni szeretné a szolgáltatás szűrőjét.

    > [!div class="mx-imgBorder"]
    > ![Szolgáltatás-szűrő paramétereinek megadása](./media/feature-flag-set-filter-parameters.png)

1. Kattintson az **alkalmaz** gombra a **funkció-jelölő szerkesztése** képernyőre való visszatéréshez. Ezután kattintson ismét az **alkalmaz** gombra a szolgáltatás jelölő beállításainak mentéséhez.

1. A Feature **Manager** lapon a szolgáltatás jelölője mostantól az egyéni **szolgáltatás szűrő** értékét is tartalmazhatja. 

    > [!div class="mx-imgBorder"]
    > ![A szolgáltatás jelölője az "egyéni" szolgáltatáskészlet-értékkel szerepel](./media/feature-flag-filter-custom.png)

## <a name="feature-filters-in-action"></a>Funkciós szűrők működés közben

A szolgáltatás jelző hatásainak megtekintéséhez indítsa el az alkalmazást, és a böngészőben többször kattintson a **frissítés** gombra. Láthatja, hogy a *bétaverzió* az idő 50%-ában megjelenik az eszköztáron. A rendszer elrejti a hátralévő időt, mert a a `PercentageFilter` kérések egy részhalmaza inaktiválja a *Beta* funkciót. A következő videó mutatja ezt a viselkedést működés közben.

> [!div class="mx-imgBorder"]
> ![TargetingFilter működés közben](./media/feature-flags-percentagefilter.gif)

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [A funkciók lépcsőzetes bevezetésének engedélyezése a megcélzott célközönségek számára](./howto-targetingfilter-aspnet-core.md)
