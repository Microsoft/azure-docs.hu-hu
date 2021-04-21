---
title: Átmeneti környezetek beállítása
description: Megtudhatja, hogyan helyezhet üzembe alkalmazásokat nem éles környezetben, és hogyan helyezheti üzembe az automatikus átállítást az éles környezetben. Növelheti a megbízhatóságot, és kiküszöbölheti az alkalmazások leállását az üzemelő példányok miatt.
ms.assetid: e224fc4f-800d-469a-8d6a-72bcde612450
ms.topic: article
ms.date: 04/30/2020
ms.custom: fasttrack-edit, devx-track-azurepowershell
ms.openlocfilehash: 8a26332250f5c53116d940a2b625eb8d7991c187
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107833107"
---
# <a name="set-up-staging-environments-in-azure-app-service"></a>Átmeneti környezetek beállítása az Azure App Service-ben
<a name="Overview"></a>

Amikor a webalkalmazást, a webalkalmazást Linux rendszeren, a mobil-háttéralkalmazást vagy az API-alkalmazást a [Azure App Service-ban](./overview.md)telepíti, az alapértelmezett üzemelő példány helyett használhat egy különálló üzembe helyezési helyet, ha a **Standard,** Prémium **vagy** Izolált App Service csomagszinten fut.  Az üzembe helyezési tárolóhelyek olyan élő alkalmazások, amelyek saját gazdanévvel vannak megszabadva. Az alkalmazás tartalmának és konfigurációjának elemei felcserélheti őket két üzembe helyezési hely között, beleértve az üzemelő helyet is. 

Az alkalmazás nem éles üzembe helyezési tárolóhelyen való üzembe helyezése a következő előnyökkel jár:

* Az alkalmazás módosításait ellenőrizheti egy előkészítési üzembe helyezési ponton, mielőtt felcseréli az üzemelő pontra.
* Ha először üzembe helyez egy alkalmazást egy üzembehelyezésre egy üzembehelyezésre, majd felcseréli azt az éles környezetbe, akkor a tárolóhely összes példánya be van melegítve, mielőtt éles környezetbe vált. Ez megszünteti az állásidőt az alkalmazás üzembe helyezésekor. A forgalom átirányítása zökkenőmentes, és a felcserélési műveletek miatt a rendszer nem eldobja a kéréseket. Ezt a teljes munkafolyamatot automatizálhatja az automatikus felcserélés [konfigurálásával,](#Auto-Swap) ha nincs szükség a felcserélés előtti érvényesítésre.
* A felcserélés után a korábban szakaszos alkalmazással létrehozott ponton már az előző éles alkalmazás is található. Ha az éles tárolóhelyre felcserélt módosítások nem a vártnak megfelelőek, akkor azonnal végrehajthatja ugyanezt a felcserélést, hogy "az utolsó ismert jó webhelyet" vissza tudja kapni.

Minden App Service csomagszint különböző számú üzembe helyezési tárolóhelyet támogat. Az üzembehely helyezési tárolóhelyek használata díjmentes. Az alkalmazásszint által támogatott tárolóhelyek számát a következő App Service [találja:](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits). 

Az alkalmazás másik szintre való méretezéséhez győződjön meg arról, hogy a célszint támogatja az alkalmazás által már használt tárolóhelyek számát. Ha például az alkalmazás ötnél több tárolóhelyet tartalmaz, nem  skálázhatja le Standard szintre, mert a **Standard** szint csak öt üzembe helyezési tárolóhelyet támogat. 

<a name="Add"></a>

## <a name="add-a-slot"></a>Pont felvétele
Az alkalmazásnak Standard, **Prémium** vagy  Izolált szinten kell futnia ahhoz, hogy több üzembehelyet engedélyezhet. 


1. a [Azure Portal](https://portal.azure.com/), keresse meg és válassza ki **App Services,** majd válassza ki az alkalmazást. 
   
    ![Keresés a App Services](./media/web-sites-staged-publishing/search-for-app-services.png)
   

2. A bal oldali panelen válassza az Üzembe **helyezési tárolóhelyek**  >  **Üzembe helyezési tárolóhely hozzáadása lehetőséget.**
   
    ![Új üzembehelyezési pont hozzáadása](./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png)
   
   > [!NOTE]
   > Ha az alkalmazás még nem a **Standard,** **Prémium** vagy Izolált szinten van, egy üzenet jelenik meg, amely jelzi a szakaszos közzététel engedélyezéséhez támogatott szinteket.  Ezen a ponton lehetősége van  a Frissítés gombra, és a folytatás előtt az alkalmazás Skálázva lapra lép. 
   > 

3. Az **Add a slot (Üzembe helyezési** hely hozzáadása) párbeszédpanelen nevezze el a tárolóhelyet, és válassza ki, hogy klónozza-e az alkalmazáskonfigurációt egy másik üzembehelyről. A **folytatáshoz válassza** a Hozzáadás lehetőséget.
   
    ![Konfigurációs forrás](./media/web-sites-staged-publishing/ConfigurationSource1.png)
   
    A konfigurációkat bármely meglévő tárolóhelyről klónozhatja. A klónozható beállítások közé tartoznak az alkalmazásbeállítások, a kapcsolati sztringek, a nyelvi keretrendszer verziói, a webes szoftvercsatornák, a HTTP-verzió és a platform bitszáma.

4. A tárolóhely hozzáadása után válassza a **Bezárás lehetőséget** a párbeszédpanel bezárásához. Az új tárolóhely most már megjelenik az Üzembe **helyezési tárolóhelyek** oldalon. Alapértelmezés szerint **a Traffic %** (Forgalom %-os) beállítás 0-ra van állítva az új tárolóhelyhez, és az összes ügyfélforgalom az éles tárolóhelyre van irányítva.

5. Válassza ki az új üzembe helyezési helyet a tárolóhely erőforrásoldalának megnyitásához.
   
    ![Üzembe helyezési hely címe](./media/web-sites-staged-publishing/StagingTitle.png)

    Az előkészítési pont felügyeleti lapja a többi alkalmazáshoz App Service is. Módosíthatja a tárolóhely konfigurációját. Emlékeztetőként, hogy az üzembe helyezési pont megtekintésekor az alkalmazás neve , az alkalmazás típusa pedig **\<app-name>/\<slot-name>** **App Service (pont).** A tárolóhelyet külön alkalmazásként is láthatja az erőforráscsoportban, ugyanazokkal a megjelölésekkel.

6. Válassza ki az alkalmazás URL-címét a tárolóhely erőforráslapján. Az üzembe helyezési hely saját gazdagépnévvel rendelkezik, és egyben élő alkalmazás is. Az üzembe helyezési ponthoz való nyilvános hozzáférés korlátozását lásd: [IP Azure App Service korlátozásai.](app-service-ip-restrictions.md)

Az új üzembe helyezési hely még akkor sem rendelkezik tartalommal, ha a beállításokat egy másik tárolóhelyről klónozza. Például közzéteheti [ezt a helyet a Git használatával.](./deploy-local-git.md) Az üzembe helyezést egy másik adattárágból vagy egy másik adattárból helyezheti üzembe a tárolóhelyen.

<a name="AboutConfiguration"></a>

## <a name="what-happens-during-a-swap"></a>Mi történik a felcserélés során?

### <a name="swap-operation-steps"></a>A felcserélési művelet lépései

Amikor felcserél két tárolóhelyet (általában egy előkészítési pontról az éles pontra), a App Service a következőket teszi annak érdekében, hogy a célhelyen ne legyen állásidő:

1. Alkalmazza a következő beállításokat a célhelyről (például az éles pontról) a forráshely összes példányára: 
    - [Tárolóhely-specifikus alkalmazásbeállítások](#which-settings-are-swapped) és kapcsolati sztringek, ha vannak.
    - [Folyamatos üzembe helyezési](deploy-continuous-deployment.md) beállítások, ha engedélyezve vannak.
    - [App Service meg a hitelesítési](overview-authentication-authorization.md) beállításokat, ha engedélyezve van.
    
    Ezen esetek bármelyike elindítja a forráshelyen található összes példányt az újraindításhoz. Az [előnézettel való felcserélés](#Multi-Phase)során ez az első fázis végét jelzi. A felcserélési művelet fel van függesztve, és ellenőrizheti, hogy a forráshely megfelelően működik-e a célhely beállításaival.

1. Várjon, amíg a forráshelyen található összes példány befejeződik az újraindításhoz. Ha valamelyik példány nem indul újra, a felcserélési művelet visszaállítja a forráshely módosításait, és leállítja a műveletet.

1. Ha [a helyi gyorsítótár](overview-local-cache.md) engedélyezve van, aktiválja a helyi gyorsítótár inicializálását úgy, hogy http-kérést indít az alkalmazás gyökerének ("/") a forráshely egyes példányán. Várjon, amíg az egyes példányok HTTP-választ nem adnak vissza. A helyi gyorsítótár inicializálása minden példányon újabb újraindítást okoz.

1. Ha [az automatikus felcserélés](#Auto-Swap) egyéni [](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) bemelegítő funkcióval van [engedélyezve,](#Warm-up)aktiválja az alkalmazás kezdeményezését úgy, hogy http-kérést indít az alkalmazásgyökér ("/") számára a forráshely egyes példányán.

    Ha nincs megadva, indítsa el a HTTP-kérést a forráshely alkalmazásgyökerének minden `applicationInitialization` példányon. 
    
    Ha egy példány HTTP-választ ad vissza, akkor bemelegítettnek tekinthető.

1. Ha a forráshelyen található összes példány megfelelően be van melegítve, cserélje fel a két tárolóhelyet a két tárolóhely útválasztási szabályainak átváltása által. A lépés után a célhelyen (például az éles környezetben) a korábban a forráshelyen bemelegített alkalmazás található.

1. Most, hogy a forráshelyen korábban a felcserélés előtti alkalmazás található a célhelyen, hajtsa végre ugyanezt a műveletet az összes beállítás alkalmazásával és a példányok újraindításával.

A felcserélési művelet bármely pontján a felcserélett alkalmazások inicializálása a forrásponton történik. A célhely online állapotban marad, amíg a forráshely elő van készítve és be van melegítve, függetlenül attól, hogy a csere hol lesz sikeres vagy sikertelen. Ha felcserél egy előkészítési helyet az éles pontra, győződjön meg arról, hogy az éles pont mindig a célhely. Így a felcserélési művelet nincs hatással az éles alkalmazásra.

### <a name="which-settings-are-swapped"></a>Mely beállításokat cseréli fel a rendszer?

[!INCLUDE [app-service-deployment-slots-settings](../../includes/app-service-deployment-slots-settings.md)]

Ha úgy konfigurál egy alkalmazásbeállítást vagy kapcsolati sztringet,  hogy egy adott (nem felcserélett) tárolóhelyhez maradjon, akkor az adott hely Konfiguráció lapjára kell átmenni. Adjon hozzá vagy szerkesszen egy beállítást, majd válassza az **üzembe helyezésihely-beállítást.** Ennek a jelölőnégyzetnek a be App Service jelzi, hogy a beállítás nem cserélhető fel. 

![Tárolóhely beállítása](./media/web-sites-staged-publishing/SlotSetting.png)

<a name="Swap"></a>

## <a name="swap-two-slots"></a>Két tárolóhely felcserélve 
Az üzembe helyezési helyeket felcserélheti az alkalmazás Üzembe helyezési üzembehelyihelyek **oldalán** és az **Áttekintés oldalon.** A felcserélés technikai részleteiért lásd: Mi történik a [felcserélés során?](#AboutConfiguration)

> [!IMPORTANT]
> Mielőtt üzembe helyezési pontról éles üzembe helyezésre cserél egy alkalmazást, győződjön meg arról, hogy az üzemelés a célhely, és hogy a forráshely összes beállítása pontosan úgy van konfigurálva, ahogyan éles környezetben szeretné.
> 
> 

Az üzembehelyhelyző tárolóhelyek felcserélve:

1. Váltsa az alkalmazás Üzembe helyezési **tárolóhelyek oldalát,** és válassza a **Felcserélés lehetőséget.**
   
    ![Felcserélés gomb](./media/web-sites-staged-publishing/SwapButtonBar.png)

    A **Felcserélés** párbeszédpanelen a kiválasztott forrás- és célhely beállításai jelennek meg, amelyek módosulnak.

2. Válassza ki a kívánt **forrás-** és **célhelyet.** A cél általában az éles környezet. Emellett válassza a **Forrás módosításai** és a Cél módosításai **lapokat,** és ellenőrizze, hogy a konfiguráció módosításai várhatók-e. Ha elkészült, a Felcserélés lehetőség kiválasztásával azonnal felcserélheti a **tárolóhelyeket.**

    ![Csere befejezése](./media/web-sites-staged-publishing/SwapImmediately.png)

    Annak megtekintéséhez, hogy a csere ténylegesen bekövetkezte előtt hogyan futna a célhely az új beállításokkal, ne válassza a Felcserélés **lehetőséget,** de kövesse a Felcserélés előnézettel [útmutatót.](#Multi-Phase)

3. Ha végzett, zárja be a párbeszédpanelt a Bezárás lehetőség **választásával.**

Ha bármilyen problémája van, tekintse meg a [felcserélések hibaelhárítását bemutató témakört.](#troubleshoot-swaps)

<a name="Multi-Phase"></a>

### <a name="swap-with-preview-multi-phase-swap"></a>Felcserélés előnézettel (többfázisú felcserélés)

Mielőtt éles környezetbe vált célként, ellenőrizze, hogy az alkalmazás a felcserélhető beállításokkal fut-e. A felcserélés befejezése előtt a forráshely is be van melegítve, ami kritikus fontosságú alkalmazások esetén kívánatos.

Ha előnézettel végez felcserélést, a App Service végrehajtja ugyanazt a felcserélési [műveletet,](#AboutConfiguration) de az első lépés után szünetel. Ezután ellenőrizheti az eredményt az előkészítési ponton a felcserélés befejezése előtt. 

Ha megszakítja a felcserélést, App Service újraalkotja a konfigurációs elemeket a forráshelyen.

Az előnézettel való felcseréléshez:

1. Kövesse az üzembehely [helyezési üzembehelyhely-felcserélés lépéseit,](#Swap) de válassza **a Felcserélés végrehajtása előnézettel lehetőséget.**

    ![Felcserélés előnézettel](./media/web-sites-staged-publishing/SwapWithPreview.png)

    A párbeszédpanelen látható, hogyan változik a forráshely konfigurációja az 1. fázisban, és hogyan változik a forrás és a célhely a 2. fázisban.

2. Ha készen áll a felcserélésre, válassza a **Felcserélés kezdete lehetőséget.**

    Amikor az 1. fázis befejeződik, a párbeszédpanelen értesítést kap. Tekintse meg a felcserélés előnézetét a forráshelyen a következő gombra: `https://<app_name>-<source-slot-name>.azurewebsites.net` . 

3. Ha készen áll a függőben lévő felcserélés befejezésére, válassza a **Felcserélés** befejezése **felcserélés** közben műveletet, majd válassza **a Felcserélés befejezése lehetőséget.**

    A függőben lévő felcserélés megszakításhoz válassza inkább a **Felcserélés megszakítása** lehetőséget.

4. Ha elkészült, zárja be a párbeszédpanelt a Bezárás lehetőség **választásával.**

Ha bármilyen problémája van, tekintse meg a [Felcserélések hibaelhárítása témakört.](#troubleshoot-swaps)

A többfázisú felcserélés automatizálásával lásd: [Automatizálás a PowerShell használatával.](#automate-with-powershell)

<a name="Rollback"></a>

## <a name="roll-back-a-swap"></a>Felcserélés visszaváltása
Ha hiba történik a célhelyen (például az éles tárolóhelyen) egy pont felcserélését követően, állítsa vissza a pontokat a felcserélés előtti államokra úgy, hogy azonnal felcseréli ugyanazt a két tárolóhelyet.

<a name="Auto-Swap"></a>

## <a name="configure-auto-swap"></a>Automatikus felcserélés konfigurálása

> [!NOTE]
> Az automatikus felcserélés linuxos webalkalmazásokban nem támogatott.

Az automatikus felcserélés leegyszerűsíti az Olyan Azure DevOps-forgatókönyveket, amelyekben az alkalmazást folyamatosan, hideg kezdés és állásidő nélkül szeretné üzembe helyezni az alkalmazás ügyfelei számára. Ha az automatikus felcserélés engedélyezve van egy tárolóhelyről az éles környezetbe, [](#swap-operation-steps) minden alkalommal, amikor leküldi a kód módosításait erre a tárolóhelyre, a App Service automatikusan éles környezetbe cseréli az alkalmazást, miután bemelegített a forráshelyen.

   > [!NOTE]
   > Mielőtt konfigurálja az automatikus felcserélést az éles tárolóhelyhez, fontolja meg az automatikus felcserélés tesztelését egy nem éles célhelyen.
   > 

Az automatikus felcserélés konfigurálása:

1. Ugrás az alkalmazás erőforrásoldalára. Válassza **az Üzembehely helyezési**  >  *\<desired source slot>*  >  **tárolóhelyek Konfigurációs** általános beállítások  >  **lehetőséget.**
   
2. Az Automatikus **felcserélés engedélyezve beállításhoz** válassza a **Be lehetőséget.** Ezután válassza ki a kívánt célhelyet az Automatikus felcserélés üzembe helyezési **tárolóhelyéhez,** majd válassza a Mentés lehetőséget **a** parancssávon. 
   
    ![Az automatikus felcserélés konfigurálásához kiválasztott beállítások](./media/web-sites-staged-publishing/AutoSwap02.png)

3. Hajtson végre egy kódküldést a forráshelyre. Az automatikus felcserélés rövid idő után történik, és a frissítés megjelenik a célhely URL-címében.

Ha bármilyen problémája van, tekintse meg a [felcserélések hibaelhárítását bemutató témakört.](#troubleshoot-swaps)

<a name="Warm-up"></a>

## <a name="specify-custom-warm-up"></a>Egyéni bemelegítés megadása

Egyes alkalmazásokhoz egyéni bemelegítő műveletekre lehet szükség a felcserélés előtt. A `applicationInitialization` konfigurációs elem web.config egyéni inicializálási műveletek megadását teszi lehetővé. A [felcserélési](#AboutConfiguration) művelet megvárja, amíg az egyéni bemelegítés befejeződik, mielőtt felcserél a célhelyre. Itt egy minta web.config töredék.

```xml
<system.webServer>
    <applicationInitialization>
        <add initializationPage="/" hostName="[app hostname]" />
        <add initializationPage="/Home/About" hostName="[app hostname]" />
    </applicationInitialization>
</system.webServer>
```

Az elem testreszabásával kapcsolatos további információkért tekintse meg az üzembehelyezési üzembehelyezés leggyakoribb felcserélési meghibásodásai és a `applicationInitialization` [javításuk mikéntjére vonatkozó információkat.](https://ruslany.net/2017/11/most-common-deployment-slot-swap-failures-and-how-to-fix-them/)

A bemelegítés viselkedését az alábbi alkalmazásbeállítások egyikével vagy mindkettővel [is testre szabhatja:](configure-common.md)

- `WEBSITE_SWAP_WARMUP_PING_PATH`: A webhely bemelegítésének pingelési útvonala. Adja hozzá ezt az alkalmazásbeállítást egy olyan egyéni elérési út megadásával, amely perjellel kezdődik értékként. Például: `/statuscheck`. Az alapértelmezett érték `/`. 
- `WEBSITE_SWAP_WARMUP_PING_STATUSES`: Érvényes HTTP-válaszkódok a bemelegítés művelethez. Adja hozzá ezt az alkalmazásbeállítást a HTTP-kódok vesszővel elválasztott listájával. Ilyen például a `200,202` . Ha a visszaadott állapotkód nem szerepel a listában, a bemelegítés és a felcserélési műveletek leállnak. Alapértelmezés szerint minden válaszkód érvényes.
- `WEBSITE_WARMUP_PATH`: Relatív elérési út a webhelyen, amit a hely újraindításakor meg kell pingelni (nem csak a pontcserék során). Példaértékek a `/statuscheck` következők: vagy a gyökérútvonal `/` ().

> [!NOTE]
> A konfigurációs elem az egyes alkalmazások indítási részei, míg a két bemelegítés viselkedése alkalmazásbeállítás csak a felcserélésre `<applicationInitialization>` vonatkozik.

Ha bármilyen problémája van, tekintse meg a [Felcserélések hibaelhárítása témakört.](#troubleshoot-swaps)

## <a name="monitor-a-swap"></a>Felcserélés figyelése

Ha a [felcserélési](#AboutConfiguration) művelet hosszú időt vesz igénybe, a tevékenységnaplóban információkat kaphat a [felcserélési műveletről.](../azure-monitor/essentials/platform-logs-overview.md)

Az alkalmazás erőforráslapján a portálon a bal oldali panelen válassza a **Tevékenységnapló lehetőséget.**

A naplólekérdezésben felcserélési művelet jelenik meg a `Swap Web App Slots` következőként: . A részleteket kibonthatja, és kiválaszthatja az egyik aloperációt vagy hibát.

## <a name="route-traffic"></a>Forgalom útválasztása

Alapértelmezés szerint az alkalmazás éles URL-címére ( ) vonatkozó összes ügyfélkérés az éles `http://<app_name>.azurewebsites.net` pontra lesz irányítva. A forgalom egy részét egy másik tárolóhelyre irányíthatja. Ez a funkció akkor hasznos, ha felhasználói visszajelzésre van szüksége egy új frissítéshez, de még nem áll készen az éles környezetben való kiadásra.

### <a name="route-production-traffic-automatically"></a>Az éles forgalom automatikus útválasztása

Az éles forgalom automatikus útválasztása:

1. Az alkalmazás erőforráslapján válassza az Üzembe **helyezési tárolóhelyek lehetőséget.**

2. Az **átirányni** kívánt pont Traffic % oszlopában adjon meg egy százalékos értéket (0 és 100 között), amely az átirányni kívánt teljes forgalom mennyiségét adja meg. Kattintson a **Mentés** gombra.

    ![Forgalom százalékos arányának beállítása](./media/web-sites-staged-publishing/RouteTraffic.png)

A beállítás mentése után a rendszer véletlenszerűen az ügyfelek megadott százalékát véletlenszerűen az éles környezeten kívüli tárolóhelyre irányítja. 

Miután a rendszer automatikusan egy adott tárolóhelyre irányít egy ügyfelet, a rendszer az ügyfél-munkamenet végéig erre a tárolóhelyre "tűzi" azt. Az ügyfélböngészőben a HTTP-fejlécek cookie-ját látva láthatja, hogy a munkamenet melyik tárolóhelyre `x-ms-routing-name` van kitűzve. Az "előkészítési" pontra irányítható kérésben található a `x-ms-routing-name=staging` cookie. Az éles tárolóhelyre irányítható kérésben található a `x-ms-routing-name=self` cookie.

   > [!NOTE]
   > A Azure Portal az Azure CLI parancsával is beállíthatja az útválasztás százalékos arányát olyan CI-/CD-eszközökből, mint a DevOps-folyamatok vagy más [`az webapp traffic-routing set`](/cli/azure/webapp/traffic-routing#az_webapp_traffic_routing_set) automatizálási rendszerek.
   > 

### <a name="route-production-traffic-manually"></a>Éles forgalom manuális útválasztása

Az automatikus forgalom-útválasztás mellett a App Service is irányíthatja a kéréseket egy adott tárolóhelyre. Ez akkor hasznos, ha azt szeretné, hogy a felhasználók feliratkozni tudjanak a bétaverziós alkalmazásra, vagy le tudják mondani azt. Az éles forgalom manuális útválasztása a lekérdezési `x-ms-routing-name` paraméterrel történik.

Ha például szeretné, hogy a felhasználók kikapcsolják a bétaalkalmazást, ezt a hivatkozást a weblapon is megadhatja:

```html
<a href="<webappname>.azurewebsites.net/?x-ms-routing-name=self">Go back to production app</a>
```

A `x-ms-routing-name=self` sztring határozza meg az éles tárolóhelyet. Miután az ügyfélböngésző hozzáfér a hivatkozáshoz, a rendszer átirányítja az éles pontra. Minden további kérés rendelkezik a cookie-val, amely kitűzi `x-ms-routing-name=self` a munkamenetet az éles tárolóhelyre.

Ha azt szeretné, hogy a felhasználók a bétaalkalmazást választják, állítsa be ugyanazt a lekérdezési paramétert a nem éles környezetben található tárolóhely nevére. Bemutatunk egy példát:

```
<webappname>.azurewebsites.net/?x-ms-routing-name=staging
```

Alapértelmezés szerint az új tárolóhelyek útválasztási szabálya `0%` , amely szürkével jelenik meg. Ha ezt az értéket explicit módon értékre (fekete szöveggel jelennek meg), a felhasználók manuálisan hozzáférhetnek az előkészítési ponthoz `0%` a `x-ms-routing-name` lekérdezési paraméterrel. Ezek azonban nem lesznek automatikusan a tárolóhelyre irányítva, mert az útválasztás százalékos aránya 0-ra van állítva. Ez egy speciális forgatókönyv, amelyben "elrejtheti" az előkészítési pontokat a nyilvános helyen, miközben a belső csapatok tesztelni is tudja a módosításokat a tárolóhelyen.

<a name="Delete"></a>

## <a name="delete-a-slot"></a>Tárolóhely törlése

Keresse meg és válassza ki az alkalmazást. Válassza az **Üzembehelyhelyi üzembe helyezési**  >  *\<slot to delete>*  >  **tárolóhelyek áttekintése lehetőséget.** Az alkalmazástípus egy **App Service (pont)** jelenik meg, amely emlékezteti, hogy egy üzembe helyezési pont van megtekintve. A **parancssávon** válassza a Törlés lehetőséget.  

![Üzembe helyezési hely törlése](./media/web-sites-staged-publishing/DeleteStagingSiteButton.png)

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>

## <a name="automate-with-powershell"></a>Automatizálás a PowerShell használatával

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure PowerShell modul parancsmagokat biztosít az Azure kezeléséhez a Windows PowerShell használatával, beleértve az üzembehelyhelyeket a Azure App Service.

További információ a virtuális Azure PowerShell telepítéséről és konfigurálásról, valamint a Azure PowerShell Azure-előfizetéssel való hitelesítéséről: How [to install and configure Microsoft Azure PowerShell.](/powershell/azure/)  

---
### <a name="create-a-web-app"></a>Webalkalmazás létrehozása
```powershell
New-AzWebApp -ResourceGroupName [resource group name] -Name [app name] -Location [location] -AppServicePlan [app service plan name]
```

---
### <a name="create-a-slot"></a>Tárolóhely létrehozása
```powershell
New-AzWebAppSlot -ResourceGroupName [resource group name] -Name [app name] -Slot [deployment slot name] -AppServicePlan [app service plan name]
```

---
### <a name="initiate-a-swap-with-a-preview-multi-phase-swap-and-apply-destination-slot-configuration-to-the-source-slot"></a>Kezdeményezzen felcserélést előnézettel (többfázisú felcserélés), és alkalmazza a célhely konfigurációját a forráspontra
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. "production"]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action applySlotConfig -Parameters $ParametersObject -ApiVersion 2015-07-01
```

---
### <a name="cancel-a-pending-swap-swap-with-review-and-restore-the-source-slot-configuration"></a>Függőben lévő felcserélés megszakítása (felcserélés felülvizsgálatra) és a forráshely konfigurációjának visszaállítása
```powershell
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action resetSlotConfig -ApiVersion 2015-07-01
```

---
### <a name="swap-deployment-slots"></a>Üzembehelyezési pontok cseréje
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. "production"]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action slotsswap -Parameters $ParametersObject -ApiVersion 2015-07-01
```

### <a name="monitor-swap-events-in-the-activity-log"></a>Felcserélési események figyelése a tevékenységnaplóban
```powershell
Get-AzLog -ResourceGroup [resource group name] -StartTime 2018-03-07 -Caller SlotSwapJobProcessor  
```

---
### <a name="delete-a-slot"></a>Tárolóhely törlése
```powershell
Remove-AzResource -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots –Name [app name]/[slot name] -ApiVersion 2015-07-01
```

## <a name="automate-with-resource-manager-templates"></a>Automatizálás Resource Manager sablonokkal

[Azure Resource Manager sablonok](../azure-resource-manager/templates/overview.md) deklaratív JSON-fájlok, amelyek az Azure-erőforrások üzembe helyezésének és konfiguráljának automatizálását szolgálják. Ha tárolóhelyeket Resource Manager fel, két tulajdonságot kell beállítania a *Microsoft.Web/sites/slots* és *a Microsoft.Web/sites erőforrásokon:*

- `buildVersion`: ez egy sztringtulajdonság, amely a tárolóhelyen üzembe helyezett alkalmazás aktuális verzióját jelöli. Például: "v1", "1.0.0.1" vagy "2019-09-20T11:53:25.2887393-07:00".
- `targetBuildVersion`: ez egy sztringtulajdonság, amely meghatározza, hogy mit kell a `buildVersion` tárolóhelynek lennie. Ha a targetBuildVersion nem egyenlő az aktuális értéktel, akkor ez elindítja a felcserélési műveletet, ha megkeresi a megadott `buildVersion` `buildVersion` tárolóhelyet.

### <a name="example-resource-manager-template"></a>Példa Resource Manager sablonra

A következő Resource Manager sablon frissíti az előkészítési pont elemét, és beállítja a et `buildVersion` `targetBuildVersion` az éles tárolóhelyen. Ez felcseréli a két tárolóhelyet. A sablon feltételezi, hogy már rendelkezik egy "staging" nevű ponthoz létrehozott webalkalmazással.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "my_site_name": {
            "defaultValue": "SwapAPIDemo",
            "type": "String"
        },
        "sites_buildVersion": {
            "defaultValue": "v1",
            "type": "String"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Web/sites/slots",
            "apiVersion": "2018-02-01",
            "name": "[concat(parameters('my_site_name'), '/staging')]",
            "location": "East US",
            "kind": "app",
            "properties": {
                "buildVersion": "[parameters('sites_buildVersion')]"
            }
        },
        {
            "type": "Microsoft.Web/sites",
            "apiVersion": "2018-02-01",
            "name": "[parameters('my_site_name')]",
            "location": "East US",
            "kind": "app",
            "dependsOn": [
                "[resourceId('Microsoft.Web/sites/slots', parameters('my_site_name'), 'staging')]"
            ],
            "properties": {
                "targetBuildVersion": "[parameters('sites_buildVersion')]"
            }
        }        
    ]
}
```

Ez Resource Manager sablon idempotent, ami azt jelenti, hogy többször is végrehajtható, és a tárolóhelyek azonos állapotát produkálódhat. Az első végrehajtás után a megfelel az aktuálisnak, így a felcserélés `targetBuildVersion` `buildVersion` nem aktiválódik.

<!-- ======== Azure CLI =========== -->

<a name="CLI"></a>

## <a name="automate-with-the-cli"></a>Automatizálás a CLI-val

Az [üzembehely helyezési tárolóhelyekre](https://github.com/Azure/azure-cli) vonatkozó Azure CLI-parancsokért lásd: [az webapp deployment slot](/cli/azure/webapp/deployment/slot).

## <a name="troubleshoot-swaps"></a>Felcserélések hibaelhárítása

Ha hiba történik [a](#AboutConfiguration)felcserélés során, a rendszer bejelentkezi az *D:\home\LogFiles\eventlog.xml.* Az alkalmazásspecifikus hibanaplóba is be van naplózva.

Íme néhány gyakori felcserélési hiba:

- Az alkalmazás gyökerének http-kérése időzít. A felcserélési művelet 90 másodpercet vár minden HTTP-kérésre, és legfeljebb 5 alkalommal újra fog esni. Ha minden újraesetben időkorrekció van, a felcserélési művelet leáll.

- A helyi gyorsítótár inicializálása sikertelen lehet, ha az alkalmazás tartalma meghaladja a helyi gyorsítótárhoz megadott helyi lemezkvótát. További információ: A helyi [gyorsítótár áttekintése.](overview-local-cache.md)

- Az [egyéni bemelegítés során](#Warm-up)a HTTP-kérések belsőleg (a külső URL-cím használata nélkül) vannak meghozva. Meghiúsulhat bizonyos URL-átírási szabályokkal a *Web.config.* Például a tartománynevek átirányításához vagy a HTTPS kényszerítéséhez szükséges szabályok megakadályozhatják, hogy a bemelegítési kérések elérjék az alkalmazás kódját. A probléma megoldásához módosítsa az átírási szabályokat az alábbi két feltétel hozzáadásával:

    ```xml
    <conditions>
      <add input="{WARMUP_REQUEST}" pattern="1" negate="true" />
      <add input="{REMOTE_ADDR}" pattern="^100?\." negate="true" />
      ...
    </conditions>
    ```
- Egyéni bemelegítés nélkül az URL-átírási szabályok továbbra is blokkolni tudnak HTTP-kéréseket. A probléma megoldásához módosítsa az átírási szabályokat a következő feltétel hozzáadásával:

    ```xml
    <conditions>
      <add input="{REMOTE_ADDR}" pattern="^100?\." negate="true" />
      ...
    </conditions>
    ```

- A felcserélések után az alkalmazás váratlan újraindulásokat tapasztalhat. Ennek az az oka, hogy a felcserélés után az állomásnév-kötés konfigurációja nem lesz szinkronizálva, ami önmagában nem okoz újraindítást. Bizonyos mögöttes tárolási események (például a tárkötet feladatátvétele) észlelheti ezeket az eltéréseket, és kényszerítheti az összes munkavégző folyamat újraindítását. Az ilyen típusú újraindítások minimalizálásához állítsa be az [ `WEBSITE_ADD_SITENAME_BINDINGS_IN_APPHOST_CONFIG=1` alkalmazásbeállítást](https://github.com/projectkudu/kudu/wiki/Configurable-settings#disable-the-generation-of-bindings-in-applicationhostconfig) az *összes tárolóhelyen.* Ez az alkalmazásbeállítás azonban nem *működik* a Windows Communication Foundation (WCF) alkalmazásokkal.

## <a name="next-steps"></a>Következő lépések
[Nem éles környezetben található tárolóhelyekhez való hozzáférés blokkolása](app-service-ip-restrictions.md)
