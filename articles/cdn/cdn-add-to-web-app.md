---
title: Oktatóanyag – Azure CDN hozzáadása egy Azure App Service-webalkalmazáshoz | Microsoft Docs
description: Ebben az oktatóanyagban hozzáadunk egy Azure tartalomkézbesítési hálózatot (CDN-t) egy Azure App Service-webalkalmazáshoz, mellyel gyorsítótárazhatja a statikus fájljait,majd kézbesítheti őket a világ különböző pontjain lévő ügyfeleinek a hozzájuk legközelebbi kiszolgálóról.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/14/2018
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 3f1dacb57931913edfb181f023bdf98717777d50
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97562242"
---
# <a name="tutorial-add-azure-cdn-to-an-azure-app-service-web-app"></a>Oktatóanyag: Azure CDN hozzáadása egy Azure App Service-webalkalmazáshoz

Ez az oktatóanyag bemutatja, hogyan adható hozzá az [Azure Content Delivery Network (CDN)](cdn-overview.md)[a webappokhoz az Azure App Service szolgáltatásban](../app-service/overview.md). A Web Apps webalkalmazásokat, REST API-kat és mobilháttereket üzemeltető szolgáltatás. 

Íme a mintaként szolgáló statikus HTML-webhely kezdőlapja, amelyet használni fog:

![Mintaalkalmazás kezdőlapja](media/cdn-add-to-web-app/sample-app-home-page.png)

Ismertetett témák:

> [!div class="checklist"]
> * CDN-végpont létrehozása.
> * Gyorsítótárazott objektumok frissítése.
> * Gyorsítótárazott verziók felügyelete lekérdezési sztringek használatával.


## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

- [A Git telepítése](https://git-scm.com/)
- [Az Azure CLI összetevő telepítése](/cli/azure/install-azure-cli)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-the-web-app"></a>A webapp létrehozása

A használni kívánt webalkalmazás létrehozásához kövesse a [statikus HTML-es rövid útmutató](../app-service/quickstart-html.md)**Az alkalmazás megkeresése tallózással** lépését.

## <a name="log-in-to-the-azure-portal"></a>Bejelentkezés az Azure Portalra

Nyisson meg egy böngészőt, és keresse fel az [Azure Portalt](https://portal.azure.com).

### <a name="dynamic-site-acceleration-optimization"></a>Dinamikus helygyorsítás optimalizálása
Ha optimalizálni szeretné a CDN-végpontot a dinamikus helygyorsításhoz (DSA), akkor a [CDN-portál](cdn-create-new-endpoint.md) használatával kell létrehoznia a profilját és a végpontot. A [DSA-optimalizálásnak](cdn-dynamic-site-acceleration.md) köszönhetően a dinamikus tartalmakkal rendelkező weboldalak teljesítménye jelentősen javul. A CDN-végpont dinamikus helygyorsításhoz való optimalizálásával kapcsolatos további útmutatást a [CDN-végpontok konfigurálása a dinamikus fájlok kézbesítésének felgyorsításához](cdn-dynamic-site-acceleration.md#cdn-endpoint-configuration-to-accelerate-delivery-of-dynamic-files) című cikkben talál. Ha nem kívánja optimalizálni az új végpontot, akkor a webalkalmazás-portál használatával is létrehozhatja azt a következő szakaszban leírtak alapján. Fontos, hogy a **Verizon Azure CDN** típusú profilok esetén nem módosíthatja a CDN-végpont optimalizálását a végpont létrehozása után.

## <a name="create-a-cdn-profile-and-endpoint"></a>CDN-profil és -végpont létrehozása

A bal oldali navigációs felületen válassza az **App Services** lehetőséget, majd válassza ki a [statikus HTML gyorsútmutató](../app-service/quickstart-html.md) segítségével létrehozott alkalmazást.

![Az App Service alkalmazás kiválasztása a portálon](media/cdn-add-to-web-app/portal-select-app-services.png)

Az **App Service** lap **Beállítások** területén válassza a **Hálózatkezelés > Az Azure CDN konfigurálása az alkalmazáshoz** lehetőséget.

![A CDN kiválasztása a portálon](media/cdn-add-to-web-app/portal-select-cdn.png)

Az **Azure Content Delivery Network** lapon adja meg az **Új végpont** beállításait az alábbi táblának megfelelően.

![Profil és végpont létrehozása a portálon](media/cdn-add-to-web-app/portal-new-endpoint.png)

| Beállítás | Ajánlott érték | Leírás |
| ------- | --------------- | ----------- |
| **CDN-profil** | myCDNProfile | A CDN-profil ugyanabba a tarifacsomagba tartozó CDN-végpontok gyűjteménye. |
| **Tarifacsomag** | Standard Akamai | A [tarifacsomag](cdn-features.md) határozza meg a szolgáltatót és az elérhető szolgáltatásokat. Ez az oktatóanyag a *Standard Akamai* szolgáltatást használja. |
| **CDN-végpont neve** | Bármely egyedi név az azureedge.net tartományban | A gyorsítótárazott erőforrásokat a *&lt; végpontneve &gt;*. azureedge.net tartományba érheti el.

Válassza a **Létrehozás** lehetőséget egy új CDN-profil létrehozásához.

Az Azure létrehozza a profilt és a végpontot. Az új végpont megjelenik a **Végpontok** listában, és a kiosztása után **Fut** állapotra vált.

![Új végpont a listában](media/cdn-add-to-web-app/portal-new-endpoint-in-list.png)

### <a name="test-the-cdn-endpoint"></a>A CDN-végpont tesztelése

 Mivel némi időre van szükség a regisztráció propagálásához, a végpont nem vehető használatba azonnal: 
   - A **Microsoft Azure CDN Standard** típusú profilok propagálása általában 10 perc alatt fejeződik be. 
   - Az **Akamai Azure CDN Standard** típusú profilok propagálása általában egy percen belül befejeződik. 
   - A **Verizon Azure CDN Standard** és a **Verizon Azure CDN Premium** típusú profilok propagálása általában 90 percen belül fejeződik be. 

Ugyanehhez az alkalmazáshoz tartozik egy *index.html* fájl, valamint *css*, *img* és *js* mappák is, amelyek egyéb statikus objektumokat tartalmaznak. Az összes fájl tartalmának elérési útjai megegyezik a CDN-végponton. Például a következő két URL egyaránt a *bootstrap.css* fájlra mutat a *css* mappában:

```
http://<appname>.azurewebsites.net/css/bootstrap.css
```

```
http://<endpointname>.azureedge.net/css/bootstrap.css
```

A böngészőben nyissa meg a következő URL-címet:

```
http://<endpointname>.azureedge.net/index.html
```

![A mintaalkalmazás CDN által szolgáltatott kezdőlapja](media/cdn-add-to-web-app/sample-app-home-page-cdn.png)

 Ugyanaz a lap jelenik meg, amelyet korábban egy Azure-webalkalmazásban futtatott. Az Azure CDN lekérte a forrás webalkalmazás objektumait, és a CDN-végpontról szolgálja ki azokat.

Annak érdekében, hogy a CDN gyorsítótárazza a lapot, frissítse azt. Néha két kérés is szükséges egyazon objektumra vonatkozóan, hogy a CDN gyorsítótárazza a kért tartalmat.

Az Azure CDN-profilok és -végpontok létrehozásával kapcsolatos további információkért lásd: [Az Azure CDN használatának első lépései](cdn-create-new-endpoint.md).

## <a name="purge-the-cdn"></a>A CDN végleges törlése

A CDN rendszeres időközönként frissíti az erőforrásait a forrásként szolgáló webappból az élettartam (TTL) konfigurációja alapján. Az alapértelmezett élettartam hét nap.

Esetenként az élettartam lejárta előtt is szükséges lehet a CDN frissítése; például amikor frissített tartalmat továbbít a webalkalmazásba. A frissítés indításához törölje manuálisan a CDN-erőforrásokat. 

Az oktatóanyag jelen szakaszában egy módosítást fog telepíteni a webappba, és törli a CDN-t, hogy az frissítse a gyorsítótárát.

### <a name="deploy-a-change-to-the-web-app"></a>Módosítás telepítése a webappba

Nyissa meg az *index.html* fájlt, és adja hozzá a *- V2* utótagot a H1 fejléchez, ahogy az az alábbi példában látható: 

```
<h1>Azure App Service - Sample Static HTML Site - V2</h1>
```

Véglegesítse a módosítást, és telepítse a webappba.

```bash
git commit -am "version 2"
git push azure main
```

Miután befejeződött a telepítés, a böngészőben nyissa meg a webalkalmazás URL-címét, és láthatja a módosított tartalmat.

```
http://<appname>.azurewebsites.net/index.html
```

![V2 a webapp címében](media/cdn-add-to-web-app/v2-in-web-app-title.png)

Ha a böngészőben megnyitja a kezdőlap CDN-végponti URL-címét, nem fogja látni a módosítást, mert a CDN-ben gyorsítótárazott verzió még nem járt le. 

```
http://<endpointname>.azureedge.net/index.html
```

![A V2 nem jelenik meg a CDN-beli címben](media/cdn-add-to-web-app/no-v2-in-cdn-title.png)

### <a name="purge-the-cdn-in-the-portal"></a>A CDN törlése a portálon

Ahhoz, hogy a CDN frissítse a gyorsítótárazott verziót, törölje a CDN-t.

A portál bal oldali navigációs felületén válassza az **Erőforráscsoportok** elemet, majd válassza ki a webapphoz létrehozott erőforráscsoportot (myResourceGroup).

![Erőforráscsoport kiválasztása](media/cdn-add-to-web-app/portal-select-group.png)

Az erőforrások listájában válassza ki a CDN-végpontot.

![Végpont kiválasztása](media/cdn-add-to-web-app/portal-select-endpoint.png)

A **Végpont** lap tetején válassza a **Végleges törlés** gombot.

![Végleges törlés kiválasztva](media/cdn-add-to-web-app/portal-select-purge.png)

Adja meg a törölni kívánt tartalmak elérési útjait. Megadhat egy teljes elérési útvonalat egy adott fájl törléséhez, vagy egy részleges útvonalat egy adott mappa teljes tartalmának törléséhez és frissítéséhez. Mivel az *index.html* fájlt módosította, mindenképp ez legyen az egyik útvonal.

A lap alján kattintson a **Végleges törlés** gombra.

![Oldal végleges törlése](media/cdn-add-to-web-app/app-service-web-purge-cdn.png)

### <a name="verify-that-the-cdn-is-updated"></a>A CDN frissítésének ellenőrzése

Várjon, amíg a végleges törlési kérés feldolgozása befejeződik. Ez általában eltart néhány percig. Az aktuális állapot megtekintéséhez válassza a harang ikont a lap tetején. 

![Törlési értesítés](media/cdn-add-to-web-app/portal-purge-notification.png)

Amikor a böngészőben megnyitja az *index.html* fájl CDN-végponti URL-címét, láthatja a kezdőlap címéhez hozzáadott *V2* utótagot, mely jelzi, hogy a CDN-gyorsítótár frissítve lett.

```
http://<endpointname>.azureedge.net/index.html
```

![V2 a CDN-beli címben](media/cdn-add-to-web-app/v2-in-cdn-title.png)

További információkért lásd az [Azure CDN-végpontok végleges törléséről](../cdn/cdn-purge-endpoint.md) szóló cikket. 

## <a name="use-query-strings-to-version-content"></a>Tartalmak verziószámozása lekérdezési sztringek használatával

Az Azure CDN az alábbi gyorsítótárazási lehetőségeket kínálja:

* Lekérdezési sztringek figyelmen kívül hagyása
* Lekérdezési sztringek gyorsítótárazásának megkerülése
* Minden egyedi URL gyorsítótárazása 

Az első az alapértelmezett beállítás, amely azt jelenti, hogy minden objektumnak csak egy gyorsítótárazott verziója van, függetlenül az URL-címben lévő lekérdezési sztringtől. 

Az oktatóanyag ezen szakaszában a gyorsítótárazás működésének módosításával minden egyedi URL-címet gyorsítótárazni fog.

### <a name="change-the-cache-behavior"></a>A gyorsítótárazás működésének módosítása

Az Azure Portal **CDN-végpont** lapján válassza a **Gyorsítótár** lehetőséget.

Válassza a **Minden egyedi URL-cím gyorsítótárazása** lehetőséget a **Lekérdezési sztringek gyorsítótárazásának működése** legördülő menüben.

Kattintson a **Mentés** gombra.

![Lekérdezési sztringek gyorsítótárazási működésének kiválasztása](media/cdn-add-to-web-app/portal-select-caching-behavior.png)

### <a name="verify-that-unique-urls-are-cached-separately"></a>Az egyedi URL-címek külön gyorsítótárazásának ellenőrzése

Nyissa meg a böngészőben a kezdőlapot a CDN-végpont címén úgy, hogy egy lekérdezési sztringet is hozzáad a címhez: 

```
http://<endpointname>.azureedge.net/index.html?q=1
```

Az Azure CDN visszaadja a webalkalmazás aktuális tartalmát, melynek a fejlécében szerepel a *V2* utótag. 

Annak érdekében, hogy a CDN gyorsítótárazza a lapot, frissítse azt. 

Nyissa meg az *index.html* fájlt, módosítsa a *V2* értéket *V3* értékre, majd tegye közzé a módosítást. 

```bash
git commit -am "version 3"
git push azure main
```

Böngészőben nyissa meg a CDN-végponti URL-címet egy új lekérdezési sztringgel, például a következővel: `q=2`. Az Azure CDN ekkor lekéri az aktuális *index.html* fájlt, és megjelenik a *V3* utótag. Ha azonban a `q=1` lekérdezési sztringgel nyitja meg a CDN-végpontot, a *V2* utótag látható.

```
http://<endpointname>.azureedge.net/index.html?q=2
```

![V3 a CDN-beli címben, 2. lekérdezési sztring](media/cdn-add-to-web-app/v3-in-cdn-title-qs2.png)

```
http://<endpointname>.azureedge.net/index.html?q=1
```

![V2 a CDN-beli címben, 1. lekérdezési sztring](media/cdn-add-to-web-app/v2-in-cdn-title-qs1.png)

Ez a kimenet mutatja, hogy a rendszer minden lekérdezési sztringet máshogy kezel:

* Korábban a q=1 volt használatban, amely a gyorsítótárazott tartalmakat adja vissza (V2).
* A q=2 viszont új, ezért a webalkalmazás legfrissebb tartalmait kéri le és adja vissza (V3).

További információkért lásd: [Az Azure CDN gyorsítótárazási viselkedésének vezérlése lekérdezési sztringekkel](../cdn/cdn-query-string.md).

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Következő lépések

Az alábbiak elvégzését ismerte meg:

> [!div class="checklist"]
> * CDN-végpont létrehozása.
> * Gyorsítótárazott objektumok frissítése.
> * Gyorsítótárazott verziók felügyelete lekérdezési sztringek használatával.

A CDN teljesítményének optimalizálását a következő cikkekben sajátíthatja el:

> [!div class="nextstepaction"]
> [Oktatóanyag: Egyéni tartomány hozzáadása az Azure CDN-végponthoz](cdn-map-content-to-custom-domain.md)