---
title: Átmeneti környezetek beállítása
description: Megtudhatja, hogyan telepíthet alkalmazásokat egy nem éles környezetbe, és hogyan végezheti el az autoswap szolgáltatást éles környezetben. Növelje a megbízhatóságot, és távolítsa el az alkalmazások leállását az üzembe helyezésből.
ms.assetid: e224fc4f-800d-469a-8d6a-72bcde612450
ms.topic: article
ms.date: 04/30/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: c779e95b790d91b801d5d35b4702191f5e7986d5
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104802964"
---
# <a name="set-up-staging-environments-in-azure-app-service"></a>Átmeneti környezetek beállítása az Azure App Service-ben
<a name="Overview"></a>

Amikor üzembe helyezi a webalkalmazást, a webalkalmazást a Linuxon, a mobil háttérben vagy az API-alkalmazáson [Azure app Service](./overview.md), az alapértelmezett üzemi tárolóhely helyett külön üzembe helyezési pontot használhat a **standard**, **prémium** vagy **elkülönített** app Service csomag szintjén. Az üzembe helyezési pontok a saját állomásneveket tartalmazó élő alkalmazások. Az alkalmazás tartalmának és konfigurációjának elemei két üzembe helyezési pont között, beleértve az üzemi tárolóhelyet is. 

Az alkalmazás nem éles tárolóhelyre való üzembe helyezése a következő előnyökkel jár:

* Egy átmeneti telepítési tárolóhelyen ellenőrizheti az alkalmazások változásait, mielőtt az éles tárolóhelyre cseréli.
* Ha először helyez üzembe egy alkalmazást egy tárolóhelyen, és az éles környezetbe kerül, akkor győződjön meg arról, hogy a tárolóhely összes példánya bemelegszik az éles környezetbe való váltás előtt. Ezzel kiküszöbölheti az állásidőt az alkalmazás telepítésekor. A forgalom átirányítása zökkenőmentes, és a swap-műveletek miatt a rendszer nem távolít el kérelmeket. Ez a teljes munkafolyamat automatizálható úgy, hogy az [automatikus swap](#Auto-Swap) konfigurálásával nem szükséges az előzetes swap érvényesítése.
* A swap után a korábban előkészített alkalmazáshoz tartozó tárolóhely már az előző éles alkalmazásban van. Ha az éles tárolóhelyre való váltás nem a várt módon történik, akkor azonnal végrehajthatja ugyanezt a cserét, hogy az "utolsó ismert jó hely" vissza legyen hajtva.

Minden App Service díjcsomag különböző számú üzembe helyezési tárolóhelyet támogat. Az üzembe helyezési pontok használata nem jár további költségekkel. Az alkalmazás szintjei által támogatott résidők számának megállapításához tekintse meg a [app Service korlátok](../azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits)című témakört. 

Ha az alkalmazást egy másik szinten szeretné méretezni, győződjön meg arról, hogy a cél szintje támogatja az alkalmazás által már használt bővítőhelyek számát. Ha például az alkalmazás ötnél több tárolóhelye van, nem méretezheti le a **standard** szintű csomagra, mert a **standard** szint csak öt üzembe helyezési helyet támogat. 

<a name="Add"></a>

## <a name="add-a-slot"></a>Pont felvétele
Az alkalmazásnak a **standard**, **prémium** vagy **elkülönített** szinten kell futnia, hogy több üzembe helyezési tárolóhelyet engedélyezzen.


1. a [Azure Portal](https://portal.azure.com/)keresse meg és válassza ki a **app Services** , majd válassza ki az alkalmazást. 
   
    ![App Services keresése](./media/web-sites-staged-publishing/search-for-app-services.png)
   

2. A bal oldali panelen válassza az **üzembe helyezési bővítőhelyek**  >  **hozzáadása tárolóhely** elemet.
   
    ![Új üzembehelyezési pont hozzáadása](./media/web-sites-staged-publishing/QGAddNewDeploymentSlot.png)
   
   > [!NOTE]
   > Ha az alkalmazás még nem szerepel a **standard**, **prémium** vagy **elkülönített** szinten, egy üzenet jelenik meg, amely a szakaszos közzététel engedélyezésének támogatott szintjeire utal. Ezen a ponton lehetősége van a **frissítés** lehetőség kiválasztására, és a folytatás előtt lépjen az alkalmazás **skálázás** lapjára.
   > 

3. A **tárolóhely hozzáadása** párbeszédpanelen adja meg a tárolóhely nevét, és válassza ki, hogy az alkalmazás konfigurációját egy másik üzembe helyezési pontról szeretné-e klónozott. A folytatáshoz válassza a **Hozzáadás** lehetőséget.
   
    ![Konfiguráció forrása](./media/web-sites-staged-publishing/ConfigurationSource1.png)
   
    A konfigurációt bármely meglévő tárolóhelyről klónozással végezheti el. A klónozott beállítások közé tartozik az Alkalmazásbeállítások, a kapcsolati karakterláncok, a nyelvi keretrendszer verziói, a webes szoftvercsatornák, a HTTP-verzió és a platform bitszáma.

4. A tárolóhely hozzáadása után kattintson a **Bezárás** gombra a párbeszédpanel bezárásához. Az új tárolóhely mostantól megjelenik az **üzembe helyezési** pontok oldalon. Alapértelmezés szerint a **(z)%** értéke 0-ra van állítva az új tárolóhely esetében, és az összes ügyfél-forgalom az éles tárolóhelyre van irányítva.

5. Válassza ki az új üzembe helyezési tárolóhelyet a tárolóhely erőforrás-oldalának megnyitásához.
   
    ![Üzembe helyezési pont címe](./media/web-sites-staged-publishing/StagingTitle.png)

    Az előkészítési pont olyan felügyeleti oldallal rendelkezik, mint bármely más App Service alkalmazás. A tárolóhely konfigurációját módosíthatja. Ha szeretné megtekinteni az üzembe helyezési pont megtekintését, az alkalmazás neve jelenik meg **\<app-name>/\<slot-name>** , az alkalmazás típusa pedig **app Service (tárolóhely)**. A tárolóhelyet külön alkalmazásként is megtekintheti az erőforráscsoporthoz, ugyanazzal a megjelöléssel.

6. Válassza ki az alkalmazás URL-címét a tárolóhely erőforrás lapján. Az üzembe helyezési pont saját állomásnévvel rendelkezik, és egy élő alkalmazás is. Az üzembe helyezési pont nyilvános hozzáférésének korlátozásához tekintse meg [Azure app Service IP-korlátozásokat](app-service-ip-restrictions.md).

Az új üzembe helyezési pontnak nincs tartalma, még akkor sem, ha a beállításokat egy másik tárolóhelyről klónozott. Például [közzéteheti ezt a tárolóhelyet a git](./deploy-local-git.md)használatával. Egy másik adattárból vagy egy másik adattárból is telepítheti a tárolóhelyre.

<a name="AboutConfiguration"></a>

## <a name="what-happens-during-a-swap"></a>Mi történik a swap során

### <a name="swap-operation-steps"></a>Műveletek cseréje lépések

Ha két tárolóhelyet cserél le (általában egy átmeneti tárolóhelyről az üzemi tárolóhelyre), App Service a következőket biztosítja annak biztosításához, hogy a cél tárolóhelye ne legyen leállás:

1. Alkalmazza a következő beállításokat a cél tárolóhelyről (például az üzemi tárolóhelyről) a forrás-tárolóhely összes példányára: 
    - A [tárolóhely-specifikus](#which-settings-are-swapped) Alkalmazásbeállítások és a kapcsolatok karakterláncai, ha vannak ilyenek.
    - [Folyamatos üzembe helyezési](deploy-continuous-deployment.md) beállítások, ha engedélyezve vannak.
    - [App Service hitelesítési](overview-authentication-authorization.md) beállítások, ha engedélyezve vannak.
    
    Ezen esetek bármelyike elindítja a forrás-tárolóhely összes példányának újraindítását. Az [előzetes verzióra való csere](#Multi-Phase)során ez az első fázis végét jelöli. A swap művelet szüneteltetve van, és ellenőrizheti, hogy a forrás bővítőhely megfelelően működik-e a cél tárolóhely beállításaival.

1. Várjon, amíg a forrás-tárolóhely minden példánya el nem végzi az újraindítást. Ha valamelyik példány nem indul újra, a swap művelet visszaállítja a forrás tárolóhelyének összes módosítását, és leállítja a műveletet.

1. Ha a [helyi gyorsítótár](overview-local-cache.md) engedélyezve van, aktiválja a helyi gyorsítótár inicializálását úgy, hogy egy HTTP-kérést küld az alkalmazás gyökerének ("/") a forrás tárolóhely minden példányán. Várjon, amíg az egyes példányok HTTP-válaszokat nem adnak vissza. A helyi gyorsítótár inicializálása egy másik újraindítást okoz az egyes példányokon.

1. Ha az [automatikus swap](#Auto-Swap) engedélyezve van az [Egyéni bemelegítővel](#Warm-up), aktiválja az [alkalmazás indítását](/iis/get-started/whats-new-in-iis-8/iis-80-application-initialization) úgy, hogy egy HTTP-kérést küld az alkalmazás gyökerének ("/") a forrás tárolóhely minden példányán.

    Ha `applicationInitialization` nincs megadva, indítson el egy HTTP-kérelmet az egyes példányok forrásoldali tárolóhelyének alkalmazás gyökerébe. 
    
    Ha egy példány bármilyen HTTP-választ ad vissza, a rendszer felmelegszik.

1. Ha a forrás tárolóhelyén lévő összes példány sikeresen befejeződik, cserélje le a két tárolóhelyet a két tárolóhely útválasztási szabályainak átváltásával. Ezt a lépést követően a cél tárolóhely (például az éles tárolóhely) azt az alkalmazást alkalmazza, amely korábban a forrás tárolóhelyen van bemelegítve.

1. Most, hogy a forrás-tárolóhely korábban a cél tárolóhelyen lévő előzetes swap alkalmazást használja, végezze el ugyanezt a műveletet az összes beállítás alkalmazásával és a példányok újraindításával.

A swap művelet bármely pontján a felcserélt alkalmazások inicializálásának minden munkája a forrás tárolóhelyen történik. A cél tárolóhely online marad, amíg a forrás tárolóhelye fel van készítve, és bemelegszik, függetlenül attól, hogy a swap sikeres vagy sikertelen volt-e. Ha egy átmeneti tárolóhelyet szeretne cserélni az üzemi tárolóhelyre, győződjön meg arról, hogy az éles tárolóhely mindig a cél tárolóhely. Így a swap művelet nem befolyásolja az üzemi alkalmazást.

### <a name="which-settings-are-swapped"></a>Mely beállítások lesznek felcserélve?

[!INCLUDE [app-service-deployment-slots-settings](../../includes/app-service-deployment-slots-settings.md)]

Ha egy adott tárolóhelyre vagy kapcsolódási sztringre kíván beállítani egy adott tárolóhelyet (nem cseréli le), lépjen az adott tárolóhely **konfiguráció** lapjára. Adjon hozzá vagy szerkesszen egy beállítást, majd válassza a **telepítési tárolóhely beállítása** lehetőséget. Ha bejelöli ezt a jelölőnégyzetet, App Service, hogy a beállítás nem cserélhető. 

![Tárolóhely beállítása](./media/web-sites-staged-publishing/SlotSetting.png)

<a name="Swap"></a>

## <a name="swap-two-slots"></a>Két tárolóhely cseréje 
Az üzembe helyezési pontokat az alkalmazás **üzembe helyezési** pontjainak oldalán és az **Áttekintés** oldalon lehet cserélni. A tárolóhelyek swap szolgáltatásával kapcsolatos technikai részletekért lásd: [Mi történik a swap során](#AboutConfiguration).

> [!IMPORTANT]
> Mielőtt egy alkalmazást becserél egy üzembe helyezési pontról az éles környezetbe, győződjön meg arról, hogy az éles környezet a cél tárolóhelye, és hogy a forrás tárolóhelyén lévő összes beállítás pontosan úgy van konfigurálva, ahogy éles környezetben szeretné.
> 
> 

Az üzembe helyezési pontok cseréje:

1. Nyissa meg az alkalmazás **üzembe helyezési** pontjai lapot, és válassza a **Csere** lehetőséget.
   
    ![Csere gomb](./media/web-sites-staged-publishing/SwapButtonBar.png)

    A **Csere** párbeszédpanel a kiválasztott forrás és cél tárolóhelyek beállításait jeleníti meg, amelyeket a rendszer megváltoztat.

2. Válassza ki a kívánt **forrás** -és **cél** tárolóhelyeket. A cél általában az üzemi tárolóhely. Emellett válassza ki a **forrás módosításait** és a **cél módosítása** lapokat, és ellenőrizze, hogy a konfigurációs változások várhatóak-e. Ha elkészült, azonnal lecserélheti a tárolóhelyeket a **swap** lehetőség kiválasztásával.

    ![Csere befejezése](./media/web-sites-staged-publishing/SwapImmediately.png)

    Ha szeretné megtekinteni, hogy a cél tárolóhelye Mikor fusson az új beállításokkal, mielőtt a swap ténylegesen megtörténne, ne válassza a **swap** lehetőséget, de kövesse a [swap with Preview (előzetes](#Multi-Phase)verzió) című témakör

3. Ha elkészült, a **Bezárás** gombra kattintva zárjuk be a párbeszédpanelt.

Ha bármilyen problémája van, olvassa el a [swap-hibák elhárítása](#troubleshoot-swaps)című témakört.

<a name="Multi-Phase"></a>

### <a name="swap-with-preview-multi-phase-swap"></a>Felcserélés előzetes verzióval (többfázisú swap)

Az éles környezetbe való váltás előtt ellenőrizze, hogy az alkalmazás a megcserélt beállításokkal fut-e. A forrásként szolgáló tárolóhely a swap befejeződése előtt is felmelegszik, ami a kritikus fontosságú alkalmazások számára is kívánatos.

Ha az előzetes verzióra cserél, App Service végrehajtja ugyanazt a [swap-műveletet](#AboutConfiguration) , de az első lépés után szünetel. Ezután ellenőrizheti az eredményét az átmeneti tárolóhelyen a csere befejezése előtt. 

Ha megszakítja a cserét, App Service a konfigurációs elemeket újra alkalmazza a forrás tárolóhelyre.

Az előzetes verzióra való váltáshoz:

1. Kövesse az [üzembe helyezési pontok cseréje](#Swap) szakasz lépéseit, de válassza a **csere elvégzése előzetes** verzióval lehetőséget.

    ![Felcserélés előzetes verzióval](./media/web-sites-staged-publishing/SwapWithPreview.png)

    A párbeszédpanelen megtekintheti, hogy a forrás tárolóhelye hogyan változik az 1. fázisban, és hogyan változik a forrás és a cél tárolóhelye a 2. fázisban.

2. Ha készen áll a swap elindítására, válassza a **Csere indítása** lehetőséget.

    Ha az 1. fázis befejeződik, a rendszer értesítést küld a párbeszédpanelen. Nyissa meg a felcserélés a forrás tárolóhelyen a következő helyre: `https://<app_name>-<source-slot-name>.azurewebsites.net` . 

3. Ha készen áll a függőben lévő csere befejezésére, válassza a swap-csere **befejezése** **műveletet** , és válassza a **Csere befejezése** lehetőséget.

    A függőben lévő csere megszakításához válassza a **Csere megszakítása** helyet.

4. Ha elkészült, a **Bezárás** gombra kattintva zárjuk be a párbeszédpanelt.

Ha bármilyen problémája van, olvassa el a [swap-hibák elhárítása](#troubleshoot-swaps)című témakört.

A többfázisú swap automatizálásához lásd: [automatizálás a PowerShell használatával](#automate-with-powershell).

<a name="Rollback"></a>

## <a name="roll-back-a-swap"></a>Swap visszaállítása
Ha a cél tárolóhelyen (például az üzemi tárolóhelyen) a tárolóhelyek cseréje után valamilyen hiba fordul elő, állítsa vissza a tárolóhelyeket az előcserélés előtti állapotba úgy, hogy az azonos két tárolóhelyet azonnal felcseréli.

<a name="Auto-Swap"></a>

## <a name="configure-auto-swap"></a>Automatikus felcserélés konfigurálása

> [!NOTE]
> Az automatikus swap szolgáltatás nem támogatott a Linuxon futó webalkalmazásokban.

Az automatikus swap szolgáltatás egyszerűsíti az Azure DevOps-forgatókönyveket, amelyekben az alkalmazás folyamatos üzembe helyezését, valamint az alkalmazás ügyfeleinek nulla állásidőt kell alkalmaznia. Ha az automatikus swap engedélyezve van egy tárolóhelyről az éles környezetbe, minden alkalommal, amikor leküldi a kód módosításait az adott tárolóhelyre, App Service automatikusan [kicseréli az alkalmazást éles üzembe](#swap-operation-steps) , miután bemelegedett a forrás tárolóhelyen.

   > [!NOTE]
   > Mielőtt konfigurálja az automatikus swap-t az üzemi tárolóhelyre, érdemes lehet tesztelni az automatikus swap szolgáltatást egy nem éles tárolóhelyen.
   > 

Az automatikus swap beállítása:

1. Nyissa meg az alkalmazás erőforrás-lapját. Válassza ki az **üzembe helyezési** pontok  >  *\<desired source slot>*  >  **konfigurációjának**  >  **általános beállításait**.
   
2. Ha **engedélyezve van az automatikus csere**, válassza **a be** lehetőséget. Ezután válassza ki a kívánt cél tárolóhelyet az **automatikus swap üzembe helyezési** ponthoz, és válassza a **Mentés** parancsot a parancssáv számára. 
   
    ![Automatikus felcserélés konfigurálásának kiválasztása](./media/web-sites-staged-publishing/AutoSwap02.png)

3. Hajtson végre egy kódot a forrás tárolóhelyre való leküldéssel. Az automatikus felcserélés rövid idő elteltével történik, és a frissítés a cél tárolóhelyének URL-címén jelenik meg.

Ha bármilyen problémája van, olvassa el a [swap-hibák elhárítása](#troubleshoot-swaps)című témakört.

<a name="Warm-up"></a>

## <a name="specify-custom-warm-up"></a>Egyéni bemelegítő beállítása

Egyes alkalmazások esetében előfordulhat, hogy a swap előtt egyéni Warm-up műveletekre van szükség. A `applicationInitialization` web.config konfigurációs eleme lehetővé teszi egyéni inicializálási műveletek megadását. A [swap művelet](#AboutConfiguration) megvárja, amíg ez az egyéni bemelegítő befejeződik a cél tárolóhelyre való váltás előtt. Íme egy példa web.config töredék.

```xml
<system.webServer>
    <applicationInitialization>
        <add initializationPage="/" hostName="[app hostname]" />
        <add initializationPage="/Home/About" hostName="[app hostname]" />
    </applicationInitialization>
</system.webServer>
```

Az elem testreszabásával kapcsolatos további információkért `applicationInitialization` lásd: az [üzembe helyezési pontok leggyakoribb felcserélésekor fellépő hibák és azok kijavítása](https://ruslany.net/2017/11/most-common-deployment-slot-swap-failures-and-how-to-fix-them/).

A bemelegítő viselkedést a következő [Alkalmazásbeállítások](configure-common.md)egyikével vagy mindkettővel is testreszabhatja:

- `WEBSITE_SWAP_WARMUP_PING_PATH`: A hely bemelegítésének elérési útja. Adja hozzá ezt az alkalmazás-beállítást egy olyan egyéni elérési út megadásával, amely egy perjelként kezdődik az értékként. Például: `/statuscheck`. Az alapértelmezett érték `/`. 
- `WEBSITE_SWAP_WARMUP_PING_STATUSES`: Érvényes HTTP-válasz kódok a bemelegítő művelethez. Adja hozzá ezt az alkalmazás-beállítást a HTTP-kódok vesszővel tagolt listájához. Példa: `200,202` . Ha a visszaadott állapotkód nem szerepel a listában, a bemelegedési és a swap művelet leáll. Alapértelmezés szerint az összes válasz kódja érvényes.
- `WEBSITE_WARMUP_PATH`: Egy relatív elérési út a helyen, amelyet a hely újraindításakor kell pingelni (nem csak a tárolóhelyek cseréjekor). Az értékek például a következők lehetnek: `/statuscheck` vagy a gyökér elérési útja `/` .

> [!NOTE]
> A `<applicationInitialization>` konfigurációs elem az egyes alkalmazások indításának része, míg a két Warm-up viselkedési alkalmazás beállításai csak a tárolóhelyek felcserélésekor érvényesek.

Ha bármilyen problémája van, olvassa el a [swap-hibák elhárítása](#troubleshoot-swaps)című témakört.

## <a name="monitor-a-swap"></a>Swap figyelése

Ha a [swap művelet](#AboutConfiguration) végrehajtása hosszú időt vesz igénybe, akkor a [tevékenység naplójának](../azure-monitor/essentials/platform-logs-overview.md)swap műveletével kapcsolatos információkat kaphat.

Az alkalmazás erőforrás oldalán a portálon, a bal oldali ablaktáblán válassza a **műveletnapló** elemet.

A napló lekérdezésében egy swap művelet jelenik meg `Swap Web App Slots` . Kibonthatja, és kiválaszthatja az egyik alműveletet vagy hibát a részletek megjelenítéséhez.

## <a name="route-traffic"></a>Útvonal forgalma

Alapértelmezés szerint az alkalmazás éles URL-címére () irányuló összes ügyfél-kérelem át `http://<app_name>.azurewebsites.net` lesz irányítva az üzemi tárolóhelyre. A forgalom egy részét átirányíthatja egy másik tárolóhelyre. Ez a funkció akkor hasznos, ha új frissítéshez felhasználói visszajelzésre van szüksége, de nem áll készen az éles környezetbe való kiadásra.

### <a name="route-production-traffic-automatically"></a>Éles forgalom automatikus irányítása

A termelési forgalom automatikus irányítása:

1. Nyissa meg az alkalmazás erőforrás-lapját, és válassza az **üzembe helyezési** pontok lehetőséget.

2. Az átirányítani kívánt tárolóhely **%** oszlopában meg kell adni a százalékos értéket (0 és 100 között), hogy az összes átirányítani kívánt forgalom mennyiségét képviseljék. Kattintson a **Mentés** gombra.

    ![Forgalmi arány beállítása](./media/web-sites-staged-publishing/RouteTraffic.png)

A beállítás mentése után a rendszer véletlenszerűen továbbítja az ügyfelek megadott százalékos arányát a nem éles tárolóhelyre. 

Miután egy ügyfél automatikusan átirányítja egy adott tárolóhelyre, az adott ügyfél-munkamenet élettartamára "rögzítve" lesz az adott tárolóhelyre. Az ügyfél böngészőjében megtekintheti, hogy a munkamenet melyik tárolóhelyre van rögzítve a `x-ms-routing-name` HTTP-fejlécekben található cookie megkeresésével. Az "átmeneti" tárolóhelyre átirányított kérelem a cookie-val rendelkezik `x-ms-routing-name=staging` . Az éles tárolóhelyre átirányított kérelem a cookie-val rendelkezik `x-ms-routing-name=self` .

   > [!NOTE]
   > A Azure Portal mellett az [`az webapp traffic-routing set`](/cli/azure/webapp/traffic-routing#az-webapp-traffic-routing-set) Azure CLI parancsával is megadhatja az útválasztási százalékokat a CI/CD-eszközökről, például a DevOps-folyamatokról vagy más automatizálási rendszerekből.
   > 

### <a name="route-production-traffic-manually"></a>Éles forgalom manuális irányítása

Az automatikus forgalmi útválasztás mellett App Service a kérelmeket egy adott tárolóhelyre irányíthatja. Ez akkor hasznos, ha azt szeretné, hogy a felhasználók bejelentkezhetnek a bétaverzióba, vagy letiltsák azokat. A termelési forgalom manuális átirányításához használja a `x-ms-routing-name` lekérdezési paramétert.

Ha szeretné, hogy a felhasználók elhagyják a bétaverzióját, például a következő hivatkozásra kattintva adhatja meg a weblapon:

```html
<a href="<webappname>.azurewebsites.net/?x-ms-routing-name=self">Go back to production app</a>
```

A karakterlánc `x-ms-routing-name=self` meghatározza az üzemi tárolóhelyet. Miután az ügyfél böngészője hozzáfér a hivatkozáshoz, a rendszer átirányítja az éles tárolóhelyre. Minden további kérelem rendelkezik a `x-ms-routing-name=self` cookie-val, amely a munkamenetet az üzemi tárolóhelyre PIN-kódra kéri.

Ha engedélyezni szeretné, hogy a felhasználók belépjenek a bétaverzióba, állítsa ugyanazt a lekérdezési paramétert a nem üzemi tárolóhely nevére. Bemutatunk egy példát:

```
<webappname>.azurewebsites.net/?x-ms-routing-name=staging
```

Alapértelmezés szerint az új bővítőhelyek útválasztási szabályt kapnak, amely `0%` szürke színnel jelenik meg. Ha explicit módon beállítja ezt az értéket `0%` (fekete szövegben látható), a felhasználók a lekérdezési paraméter használatával manuálisan érhetik el az átmeneti tárolóhelyet `x-ms-routing-name` . De a rendszer nem irányítja át automatikusan a tárolóhelyre, mert az útválasztási százalék értéke 0. Ez egy speciális forgatókönyv, ahol "elrejtheti" az átmeneti tárolóhelyet a nyilvános környezetből, miközben lehetővé teszi a belső csapatok számára a tárolóhelyek változásainak tesztelését.

<a name="Delete"></a>

## <a name="delete-a-slot"></a>Tárolóhely törlése

Keresse meg és válassza ki az alkalmazást. Válassza az **üzembe helyezési** pontok  >  *\<slot to delete>*  >  **Áttekintés** elemet. Az alkalmazás típusa **app Service (tárolóhelyként)** jelenik meg, és emlékezteti arra, hogy az üzembe helyezési pont megtekintése folyamatban van. Válassza a **delete (Törlés** ) lehetőséget a parancssáv sávon.  

![Üzembe helyezési pont törlése](./media/web-sites-staged-publishing/DeleteStagingSiteButton.png)

<!-- ======== AZURE POWERSHELL CMDLETS =========== -->

<a name="PowerShell"></a>

## <a name="automate-with-powershell"></a>Automatizálás a PowerShell használatával

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure PowerShell egy modul, amely parancsmagokat biztosít az Azure-nak a Windows PowerShellen keresztüli felügyeletéhez, beleértve az üzembe helyezési pontok Azure App Service-ben való felügyeletének támogatását.

További információ a Azure PowerShell telepítéséről és konfigurálásáról, valamint az Azure-előfizetéssel végzett Azure PowerShell hitelesítéséről: [Microsoft Azure PowerShell telepítése és konfigurálása](/powershell/azure/).  

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
### <a name="initiate-a-swap-with-a-preview-multi-phase-swap-and-apply-destination-slot-configuration-to-the-source-slot"></a>Váltás kezdeményezése az előzetes verzióval (többfázisú swap) és a tárolóhely konfigurációjának alkalmazása a forrás tárolóhelyre
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. "production"]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action applySlotConfig -Parameters $ParametersObject -ApiVersion 2015-07-01
```

---
### <a name="cancel-a-pending-swap-swap-with-review-and-restore-the-source-slot-configuration"></a>Függőben lévő felcserélés megszakítása (áttekintéssel való csere) és a forrás tárolóhely konfigurációjának visszaállítása
```powershell
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action resetSlotConfig -ApiVersion 2015-07-01
```

---
### <a name="swap-deployment-slots"></a>Üzembehelyezési pontok cseréje
```powershell
$ParametersObject = @{targetSlot  = "[slot name – e.g. "production"]"}
Invoke-AzResourceAction -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots -ResourceName [app name]/[slot name] -Action slotsswap -Parameters $ParametersObject -ApiVersion 2015-07-01
```

### <a name="monitor-swap-events-in-the-activity-log"></a>A tevékenység naplójában lévő swap-események figyelése
```powershell
Get-AzLog -ResourceGroup [resource group name] -StartTime 2018-03-07 -Caller SlotSwapJobProcessor  
```

---
### <a name="delete-a-slot"></a>Tárolóhely törlése
```powershell
Remove-AzResource -ResourceGroupName [resource group name] -ResourceType Microsoft.Web/sites/slots –Name [app name]/[slot name] -ApiVersion 2015-07-01
```

## <a name="automate-with-resource-manager-templates"></a>Automatizálás Resource Manager-sablonokkal

[Azure Resource Manager sablonok](../azure-resource-manager/templates/overview.md) deklaratív JSON-fájlok, amelyek az Azure-erőforrások üzembe helyezésének és konfigurálásának automatizálására szolgálnak. Ha Resource Manager-sablonok használatával kívánja felcserélni a tárolóhelyeket, a *Microsoft. Web/Sites/Slots* és a *Microsoft. Web/Sites* erőforrásain két tulajdonságot kell beállítania:

- `buildVersion`: ez egy karakterlánc-tulajdonság, amely a tárolóhelyen üzembe helyezett alkalmazás aktuális verzióját jelöli. Például: "v1", "1.0.0.1" vagy "2019-09-20T11:53:25.2887393-07:00".
- `targetBuildVersion`: ez egy karakterlánc-tulajdonság, amely megadja, hogy `buildVersion` a tárolóhelynek milyennek kell lennie. Ha a targetBuildVersion nem egyezik a jelenlegivel `buildVersion` , akkor ez a művelet a megadott tárolóhely megkeresésével elindítja a swap műveletet `buildVersion` .

### <a name="example-resource-manager-template"></a>Példa Resource Manager-sablonra

A következő Resource Manager-sablon frissíti az `buildVersion` átmeneti tárolóhelyet, és beállítja az `targetBuildVersion` üzemi tárolóhelyre. Ez a két tárolóhelyet fogja cserélni. A sablon feltételezi, hogy már van egy "előkészítés" nevű tárolóhelytel létrehozott WebApp.

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

Ez a Resource Manager-sablon idempotens, ami azt jelenti, hogy többször is végrehajtható, és a tárolóhelyek azonos állapotba hozhatók. Az első végrehajtás után az `targetBuildVersion` megfelel a jelenleginek `buildVersion` , így a swap nem lesz aktiválva.

<!-- ======== Azure CLI =========== -->

<a name="CLI"></a>

## <a name="automate-with-the-cli"></a>Automatizálás a parancssori felülettel

Az üzembe helyezési pontok [Azure CLI](https://github.com/Azure/azure-cli) -parancsaival kapcsolatban lásd: [az WebApp Deployment slot](/cli/azure/webapp/deployment/slot).

## <a name="troubleshoot-swaps"></a>Swap-hibák megoldása

Ha bármilyen hiba fordul elő a [tárolóhelyek cseréjekor](#AboutConfiguration), *D:\home\LogFiles\eventlog.xml* bejelentkezett. Az alkalmazásspecifikus hibanapló is be van jelentkezve.

Íme néhány gyakori swap-hiba:

- Az alkalmazás gyökerére irányuló HTTP-kérelem időkorlátja. A swap művelet minden HTTP-kérelem esetében 90 másodpercig vár, és legfeljebb 5 alkalommal próbálkozik újra. Ha az összes újrapróbálkozás időkorlátja lejárt, a swap művelet leáll.

- A helyi gyorsítótár inicializálása sikertelen lehet, ha az alkalmazás tartalma meghaladja a helyi gyorsítótárhoz megadott helyi lemezkvóta-kvótát. További információ: [helyi gyorsítótár – áttekintés](overview-local-cache.md).

- Az [Egyéni felmelegedés](#Warm-up)során a HTTP-kérések belsőleg történnek (a külső URL-cím nélkül). Előfordulhat, hogy a *Web.config* egyes URL-re vonatkozó Újraírási szabályokkal meghiúsulnak. Például a tartománynevek átirányítására vagy a HTTPS érvényesítésére vonatkozó szabályok megakadályozhatják a bemelegítő kérések elérését az alkalmazás kódjának elérésekor. A probléma megkerüléséhez módosítsa az Újraírási szabályokat úgy, hogy hozzáadja a következő két feltételt:

    ```xml
    <conditions>
      <add input="{WARMUP_REQUEST}" pattern="1" negate="true" />
      <add input="{REMOTE_ADDR}" pattern="^100?\." negate="true" />
      ...
    </conditions>
    ```
- Egyéni bemelegítő nélkül az URL-írási szabályok továbbra is letilthatják a HTTP-kérelmeket. A probléma megkerüléséhez módosítsa az Újraírási szabályokat a következő feltétel hozzáadásával:

    ```xml
    <conditions>
      <add input="{REMOTE_ADDR}" pattern="^100?\." negate="true" />
      ...
    </conditions>
    ```

- A tárolóhelyek cseréje után az alkalmazás váratlan újraindítást tapasztalhat. Ennek az az oka, hogy a cserét követően az állomásnév-kötési konfiguráció elkerül a szinkronizálásból, ami önmagában nem okozza az újraindítást. Előfordulhat azonban, hogy bizonyos mögöttes tárolási események (például a tárolási kötetek feladatátvétele) észlelik ezeket az eltéréseket, és kényszerítik az összes munkavégző folyamat újraindítását. Az ilyen típusú újraindítások minimalizálásához állítsa az [ `WEBSITE_ADD_SITENAME_BINDINGS_IN_APPHOST_CONFIG=1` alkalmazás beállításait](https://github.com/projectkudu/kudu/wiki/Configurable-settings#disable-the-generation-of-bindings-in-applicationhostconfig) az *összes tárolóhelyre*. Ez az Alkalmazásbeállítás azonban *nem* működik Windows COMMUNICATION Foundation (WCF) alkalmazásokkal.

## <a name="next-steps"></a>Következő lépések
[Nem éles tárolóhelyekhez való hozzáférés letiltása](app-service-ip-restrictions.md)
