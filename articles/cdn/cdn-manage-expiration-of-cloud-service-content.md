---
title: Webtartalom lejáratának kezelése a Azure CDNban | Microsoft Docs
description: Megtudhatja, hogyan kezelheti az Azure Web Apps/Cloud Services, a ASP.NET vagy az IIS-tartalom lejáratát Azure CDNban.
services: cdn
documentationcenter: .NET
author: asudbring
manager: danielgi
editor: ''
ms.assetid: bef53fcc-bb13-4002-9324-9edee9da8288
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.custom: devx-track-csharp
ms.topic: how-to
ms.date: 02/15/2018
ms.author: allensu
ms.openlocfilehash: d8eb450d2010bf2a525a26f1c5ff48f59732ce43
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93240970"
---
# <a name="manage-expiration-of-web-content-in-azure-cdn"></a>A webes tartalmak elévülésének kezelése Azure CDN-ben
> [!div class="op_single_selector"]
> * [Azure webes tartalom](cdn-manage-expiration-of-cloud-service-content.md)
> * [Azure Blob Storage](cdn-manage-expiration-of-blob-content.md)
> 

A nyilvánosan elérhető forrásból származó webkiszolgálókról származó fájlok az Azure Content Delivery Network (CDN) szolgáltatásban is gyorsítótárazva lesznek, amíg az élettartama (TTL) el nem telik. Az ÉLETTARTAMot a forráskiszolgáló által a `Cache-Control` http-válasz fejléce határozza meg. Ez a cikk azt ismerteti, hogyan lehet `Cache-Control` fejléceket beállítani az Microsoft Azure app Service, az Azure Cloud Services, a ASP.NET-alkalmazások és a Internet Information Services-(IIS-) helyek Web Apps szolgáltatásához, amelyek mindegyike hasonló módon van konfigurálva. A `Cache-Control` fejlécet beállíthatja konfigurációs fájlok használatával vagy programozott módon. 

A gyorsítótár beállításait a Azure Portal a [CDN gyorsítótárazási szabályainak](cdn-caching-rules.md)beállításával is szabályozhatja. Ha egy vagy több gyorsítótárazási szabályt hoz létre, és a gyorsítótárazási viselkedését a gyorsítótár **felülbírálására** vagy **megkerülésére** állítja be, a cikkben tárgyalt forrásként megadott gyorsítótárazási beállításokat a rendszer figyelmen kívül hagyja. További információ az általános gyorsítótárazási fogalmakról: [Hogyan működik a gyorsítótárazás](cdn-how-caching-works.md).

> [!TIP]
> Megadhatja, hogy a fájl ne legyen TTL. Ebben az esetben a Azure CDN automatikusan alkalmazza a hét nap alapértelmezett ÉLETTARTAMát, kivéve, ha beállította a gyorsítótárazási szabályokat a Azure Portal. Ez az alapértelmezett TTL csak az általános webes kézbesítés optimalizálására vonatkozik. Nagyméretű fájlok optimalizálása esetén az alapértelmezett TTL egy nap, és a média-adatfolyam optimalizálásához az alapértelmezett TTL egy év.
> 
> További információ arról, hogy a Azure CDN hogyan használható a fájlokhoz és egyéb erőforrásokhoz való hozzáférés felgyorsításához: [Az Azure Content Delivery Network áttekintése](cdn-overview.md).
> 

## <a name="setting-cache-control-headers-by-using-cdn-caching-rules"></a>Cache-Control fejlécek beállítása CDN gyorsítótárazási szabályok használatával
A webkiszolgáló fejlécének beállítására szolgáló előnyben részesített módszer a `Cache-Control` Azure Portal gyorsítótárazási szabályainak használata. További információ a CDN gyorsítótárazási szabályairól: [Azure CDN gyorsítótárazási viselkedés szabályozása gyorsítótárazási szabályokkal](cdn-caching-rules.md).

> [!NOTE] 
> A gyorsítótárazási szabályok csak a **Verizon Azure CDN standard** csomagból és a Akamai-profilokból **Azure CDN standard** csomagból érhetők el. A Verizon-profilokból **származó Azure CDN Premium** esetén a hasonló funkciók **kezeléséhez** a felügyeleti portálon a [Azure CDN szabályok motort](./cdn-verizon-premium-rules-engine.md) kell használnia.

**A CDN-gyorsítótárazási szabályok lapra** való váltáshoz:

1. A Azure Portal válassza ki a CDN-profilt, majd válassza ki a webkiszolgáló végpontját.

1. A bal oldali ablaktáblán, a Beállítások alatt válassza a **Gyorsítótárszabályok** lehetőséget.

   ![CDN-gyorsítótárazási szabályok gomb](./media/cdn-manage-expiration-of-cloud-service-content/cdn-caching-rules-btn.png)

   Megjelenik a **Gyorsítótárszabályok** lap.

   ![CDN-gyorsítótárazási oldal](./media/cdn-manage-expiration-of-cloud-service-content/cdn-caching-page.png)


**Webkiszolgáló Cache-Control fejlécének beállítása globális gyorsítótárazási szabályok használatával:**

1. A **globális gyorsítótárazási szabályok** területen állítsa be a **lekérdezési karakterlánc gyorsítótárazási viselkedését** a **lekérdezési karakterláncok figyelmen kívül hagyása** és a **gyorsítótárazási viselkedés** beállítása **felülbírálásra**
      
1. A **gyorsítótár lejárati idejének** esetében adja meg a 3600 értéket a **másodperc** mezőben, vagy 1 az **óra** mezőben. 

   ![CDN globális gyorsítótárazási szabályok – példa](./media/cdn-manage-expiration-of-cloud-service-content/cdn-global-caching-rules-example.png)

   Ez a globális gyorsítótárazási szabály egy óra gyorsítótári időtartamát állítja be, és a végpontra irányuló összes kérést érinti. Felülbírálja `Cache-Control` `Expires` a végpont által megadott forráskiszolgáló által elküldett bármely vagy HTTP-fejlécet.   

1. Kattintson a **Mentés** gombra.

**Webkiszolgáló-fájl Cache-Control fejlécének beállítása egyéni gyorsítótárazási szabályok használatával:**

1. Az **Egyéni gyorsítótárazási szabályok** alatt hozzon létre két egyeztetési feltételt:

     a. Az első egyeztetési feltétel beállításnál állítsa az **egyeztetés feltételt** az **elérési út** értékre, és adja meg `/webfolder1/*` az **egyezési értéket**. Állítsa be a **gyorsítótárazási viselkedést** a **felülbíráláshoz** , és adja meg a 4 értéket a **napok** mezőben.

     b. A második egyeztetési feltételnél állítsa az **egyeztetés feltételt** az **elérési út** értékre, és adja meg `/webfolder1/file1.txt` az **egyezési értéket**. Állítsa be a **gyorsítótárazási viselkedést** a **felülbíráláshoz** , és adja meg a 2 értéket a **napok** mezőben.

    ![CDN – egyéni gyorsítótárazási szabályok – példa](./media/cdn-manage-expiration-of-cloud-service-content/cdn-custom-caching-rules-example.png)

    Az első egyéni gyorsítótárazási szabály a `/webfolder1` végpont által megadott forráskiszolgáló mappájában lévő fájlok esetében négy órányi gyorsítótári időtartamot állít be. A második szabály csak a fájl első szabályát felülbírálja, `file1.txt` és két órás gyorsítótári időtartamot állít be.

1. Kattintson a **Mentés** gombra.


## <a name="setting-cache-control-headers-by-using-configuration-files"></a>Cache-Control fejlécek beállítása konfigurációs fájlok használatával
A statikus tartalom, például a képek és a stíluslapok esetében a frissítés gyakoriságát a webalkalmazás **applicationHost.config** vagy **Web.config** konfigurációs fájljainak módosításával szabályozhatja. A `Cache-Control` tartalom fejlécének beállításához használja az `<system.webServer>/<staticContent>/<clientCache>` elemet valamelyik fájlban.

### <a name="using-applicationhostconfig-files"></a>ApplicationHost.config fájlok használata
A **ApplicationHost.config** fájl az IIS konfigurációs rendszerének gyökérkönyvtára. Egy **ApplicationHost.config** fájlban lévő konfigurációs beállítások hatással vannak a hely összes alkalmazására, de felülbírálják a webalkalmazásokhoz tartozó **Web.config** -fájlok beállításait.

### <a name="using-webconfig-files"></a>Web.config fájlok használata
**Web.config** -fájllal testreszabhatja a webalkalmazás teljes webalkalmazásának vagy egy adott könyvtárának a módját. A webalkalmazás gyökérkönyvtárában általában legalább egy **Web.config** fájl található. Egy adott mappában lévő minden **Web.config** fájl esetében a konfigurációs beállítások hatással vannak a mappában és annak almappáiban található összes elemre, kivéve, ha egy másik **Web.config** fájlja felülbírálja az almappa szintjén. 

Beállíthat például egy `<clientCache>` elemet egy **Web.config** fájlban a webalkalmazás gyökérkönyvtárában, hogy három napig gyorsítótárazza a webalkalmazás összes statikus tartalmát. Hozzáadhat egy **Web.config** fájlt is egy olyan almappában, amely több változó tartalmú tartalommal rendelkezik (például `\frequent` ) `<clientCache>` . Ennek a nettó eredménye az, hogy a teljes webhely tartalma három napig van gyorsítótárazva, kivéve a címtárban található tartalmakat `\frequent` , amelyek csak hat órán át vannak gyorsítótárazva.  

A következő XML-konfigurációs fájl példája azt mutatja be, hogyan állíthatja be az `<clientCache>` elemet úgy, hogy legfeljebb három napos kort határozzon meg:  

```xml
<configuration>
    <system.webServer>
        <staticContent>
            <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00" />
        </staticContent>
    </system.webServer>
</configuration>
```

A **cacheControlMaxAge** attribútum használatához be kell állítania a **cacheControlMode** attribútum értékét a következőre: `UseMaxAge` . Ez a beállítás a HTTP-fejlécet és direktívát okozta a `Cache-Control: max-age=<nnn>` válaszhoz való hozzáadáshoz. A **cacheControlMaxAge** attribútum TimeSpan-értékének formátuma a következő: `<days>.<hours>:<min>:<sec>` . Az érték másodpercre van átalakítva, és az irányelv értékeként lesz használva `Cache-Control` `max-age` . További információ az `<clientCache>` elemről: ügyfél- [ \<clientCache> gyorsítótár ](https://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache).  

## <a name="setting-cache-control-headers-programmatically"></a>Cache-Control fejlécek programozott módon történő beállítása
A ASP.NET alkalmazások esetében a CDN gyorsítótárazási viselkedését programozott módon szabályozhatja a .NET API **HttpResponse. cache** tulajdonságának beállításával. További információ a **HttpResponse. cache** tulajdonságról: [HttpResponse. cache Property](/dotnet/api/system.web.httpresponse.cache#System_Web_HttpResponse_Cache) és [HttpCachePolicy osztály](/dotnet/api/system.web.httpcachepolicy).  

Az alkalmazás tartalmának programozott gyorsítótárazásához a ASP.NET-ben kövesse az alábbi lépéseket:
   1. A beállítással ellenőrizze, hogy a tartalom gyorsítótárazható `HttpCacheability` -e `Public` . 
   1. Állítsa be a gyorsítótár-érvényesítő a következő módszerek egyikének meghívásával `HttpCachePolicy` :
      - Hívja meg a `SetLastModified` fejléc időbélyeg-értékének megadását `Last-Modified` .
      - Hívja meg a `SetETag` fejléc értékének megadását `ETag` .
   1. Szükség esetén a gyorsítótár lejárati idejét is megadhatja a `SetExpires` fejléc értékének meghívásával `Expires` . Ellenkező esetben a jelen dokumentumban korábban ismertetett alapértelmezett gyorsítótár-heurisztikus beállítások érvényesek.

Ha például egy órára szeretné gyorsítótárazni a tartalmakat, adja hozzá a következő C#-kódot:  

```csharp
// Set the caching parameters.
Response.Cache.SetExpires(DateTime.Now.AddHours(1));
Response.Cache.SetCacheability(HttpCacheability.Public);
Response.Cache.SetLastModified(DateTime.Now);
```

## <a name="testing-the-cache-control-header"></a>A Cache-Control fejléc tesztelése
Könnyedén ellenőrizheti a webes tartalom ÉLETTARTAMának beállításait. A böngésző [fejlesztői eszközeivel](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/)ellenőrizze, hogy a webes tartalom tartalmazza-e a `Cache-Control` Válasz fejlécét. Olyan eszközt is használhat, mint például a **wget**, a [Poster](https://www.getpostman.com/)vagy a [Hegedűs](https://www.telerik.com/fiddler) , és megvizsgálhatja a válasz fejléceit.

## <a name="next-steps"></a>Következő lépések
* [A **clientCache** elem részleteinek olvasása](https://www.iis.net/ConfigReference/system.webServer/staticContent/clientCache)
* [Olvassa el a **HttpResponse. cache** tulajdonság dokumentációját.](/dotnet/api/system.web.httpresponse.cache#System_Web_HttpResponse_Cache) 
* [A **HttpCachePolicy osztály** dokumentációjának elolvasása](/dotnet/api/system.web.httpcachepolicy)  
* [Tudnivalók a gyorsítótárazási fogalmakról](cdn-how-caching-works.md)
