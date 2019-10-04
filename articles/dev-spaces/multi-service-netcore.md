---
title: .NET Core és a VS Code használatával több függő szolgáltatás fut.
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 11/21/2018
ms.topic: tutorial
description: Gyors Kubernetes-fejlesztés tárolókkal és mikroszolgáltatásokkal az Azure-ban
keywords: Docker, Kubernetes, Azure, az AKS, az Azure Kubernetes Service, tárolók, Helm, a szolgáltatás háló, a szolgáltatás háló útválasztás, a kubectl, a k8s
ms.openlocfilehash: 2a1e99ba1c19dfdcaaf1b6709e6d3976968cf623
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2019
ms.locfileid: "67503101"
---
# <a name="multi-service-development-with-azure-dev-spaces"></a>Az Azure fejlesztési tárolóhelyek több szolgáltatásos fejlesztői

Ebben az oktatóanyagban megismerheti, hogyan szóközzel Azure fejlesztői, a fejlesztői, szóközök biztosító előnyökkel némelyike együtt több szolgáltatást alkalmazások fejlesztéséhez kell.

## <a name="call-a-service-running-in-a-separate-container"></a>Különálló tárolóban futó szolgáltatás hívása

Ebben a szakaszban létrehoz egy második szolgáltatást `mywebapi`, és rendelkezik `webfrontend` meghívására. Minden szolgáltatás különálló tárolókban fut. Ezt követően hibakeresést fog futtatni mindkét tárolóban.

![Több tároló](media/common/multi-container.png)

### <a name="download-sample-code-for-mywebapi"></a>Mintakód letöltése a *mywebapi* szolgáltatáshoz
Az egyszerűség kedvéért töltsünk le egy mintakódot a GitHub-adattárból. Navigáljon a https://github.com/Azure/dev-spaces helyre, és válassza a **Klónozás vagy Letöltés** lehetőséget a GitHub-adattár letöltéséhez. Az ehhez a szakaszhoz tartozó kód a következő helyen található: `samples/dotnetcore/getting-started/mywebapi`.

### <a name="run-mywebapi"></a>A *mywebapi* szolgáltatás futtatása
1. Nyissa meg a `mywebapi` mappát egy *különálló VS Code-ablakban*.
1. Nyissa meg a **parancskatalógust** (**Nézet | Parancskatalógus** menü), és az automatikus kitöltés használatával írja be és válassza ki a következő parancsot: `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`. Ez a parancs nem keverendő össze az `azds prep` paranccsal, amely az üzembe helyezéshez konfigurálja a projektet.
1. Nyomja le az F5 billentyűt, és várjon, amíg a rendszer felépíti és telepíti a szolgáltatást. Tudni fogja, készen áll a mikor a *Application started. Nyomja le a Ctrl + C billentyűkombinációval állítsa le.* a hibakeresési konzolt üzenet jelenik meg.
1. A végpont URL-címe valahogy így fog kinézni: `http://localhost:<portnumber>`. **Tipp: A VS Code állapotsor narancssárga kapcsolja be, és a egy kattintható URL-cím megjelenítéséhez.** Úgy tűnhet, hogy a tároló helyileg fut, de valójában az Azure-beli Dev Spaces-terünkben fut. A localhost cím oka az, hogy a `mywebapi` nem határozott meg egy nyilvános végpontot sem, és kizárólag a Kubernetes-példányon belülről lehet hozzáférni. Az Ön kényelme, valamint a helyi gép és a privát szolgáltatás közötti interakció elősegítése érdekében az Azure Dev Spaces egy ideiglenes SSH-csatornát hoz létre az Azure-ban futó tárolóhoz.
1. Ha a `mywebapi` elkészült, nyissa meg a böngészőben a localhost címét. Fűzze a `/api/values` sztringet az URL-címhez a `ValuesController`-hez tartozó alapértelmezett GET API meghívásához.
1. Ha minden lépés sikeres volt, választ kell kapnia a `mywebapi` szolgáltatástól.

### <a name="make-a-request-from-webfrontend-to-mywebapi"></a>Kérés indítása a *webfrontend*-ből a *mywebapi*-ba
Most írjunk olyan kódot a `webfrontend` szolgáltatásban, amely kérést indít a `mywebapi` felé.
1. Váltson a `webfrontend` VS Code-ablakára.
1. *Cserélje le* a kódját a névjegy metódus az `HomeController.cs`:

    ```csharp
    public async Task<IActionResult> About()
    {
        ViewData["Message"] = "Hello from webfrontend";
        
        using (var client = new System.Net.Http.HttpClient())
            {
                // Call *mywebapi*, and display its response in the page
                var request = new System.Net.Http.HttpRequestMessage();
                request.RequestUri = new Uri("http://mywebapi/api/values/1");
                if (this.Request.Headers.ContainsKey("azds-route-as"))
                {
                    // Propagate the dev space routing header
                    request.Headers.Add("azds-route-as", this.Request.Headers["azds-route-as"] as IEnumerable<string>);
                }
                var response = await client.SendAsync(request);
                ViewData["Message"] += " and " + await response.Content.ReadAsStringAsync();
            }

        return View();
    }
    ```

Az előző példakód továbbítja az `azds-route-as` fejlécet a bejövő kérelemből a kimenő kérelemhez. Látni fogja, hogyan később csapatokat, így [együttműködő fejlesztési](team-development-netcore.md).

### <a name="debug-across-multiple-services"></a>Hibakeresés több szolgáltatásban
1. Ezen a ponton a `mywebapi` elvileg még mindig fut a hozzácsatolt hibakeresővel. Ha nem fut, nyomja le az F5 billentyűt a `mywebapi` projektben.
1. Állítson be egy töréspontot belül a `Get(int id)` metódushoz, amely kezeli a `api/values/{id}` GET-kérések. Ez a körül [a 23. sor a *Controllers/ValuesController.cs* fájl](https://github.com/Azure/dev-spaces/blob/master/samples/dotnetcore/getting-started/mywebapi/Controllers/ValuesController.cs#L23).
1. A `webfrontend` projektben állítson be egy töréspontot, mielőtt az GET kérést küld a `mywebapi/api/values` felé. Ez a sor 32 körül a [ *Controllers/HomeController.cs* fájl](https://github.com/Azure/dev-spaces/blob/master/samples/dotnetcore/getting-started/webfrontend/Controllers/HomeController.cs) módosító az előző szakaszban.
1. Nyomja le az F5 billentyűt a `webfrontend` projektben.
1. Hívja meg a webalkalmazást, és tekintse át a kódot mindkét szolgáltatásban.
1. A web app alkalmazásban a névjegy lapra a két szolgáltatás által összefűzött üzenetet jelenít meg: "Hello webfrontend és Hello a mywebapi."

### <a name="well-done"></a>Remek!
Most már rendelkezik egy többtárolós alkalmazással, ahol az egyes tárolók külön-külön fejleszthetők és helyezhetők üzembe.


## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [További információ a fejlesztői, szóközök csoportos fejlesztése](team-development-netcore.md)
