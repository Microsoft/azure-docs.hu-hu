---
title: Konfigurációs gyakori kérdések
description: Választ kaphat a Azure App Service konfigurálásával és kezelésével kapcsolatos gyakori kérdésekre.
author: genlin
manager: dcscontentpm
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.topic: article
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: 843e11e7f2df404a6bf1866da7b383ee9d1097de
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100574781"
---
# <a name="configuration-and-management-faqs-for-web-apps-in-azure"></a>Konfigurációs és felügyeleti GYIK az Azure-beli Web Apps

Ez a cikk válaszokat tartalmaz a [Azure App Service Web Apps funkciójának](https://azure.microsoft.com/services/app-service/web/)konfigurálásával és felügyeletével kapcsolatos gyakori kérdésekre (GYIK).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="are-there-limitations-i-should-be-aware-of-if-i-want-to-move-app-service-resources"></a>Van-e korlátozás arra vonatkozóan, ha App Service erőforrásokat szeretnék áthelyezni?

Ha App Service erőforrásait új erőforráscsoporthoz vagy előfizetésbe szeretné helyezni, néhány korlátozást érdemes figyelembe vennie. További információ: [app Service korlátozások](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md).

## <a name="how-do-i-use-a-custom-domain-name-for-my-web-app"></a>Hogyan használhat egyéni tartománynevet a webalkalmazáshoz?

Az egyéni tartománynevek Azure-webalkalmazással való használatával kapcsolatos gyakori kérdésekre adott válaszokért tekintse meg a hét perces videó [Egyéni tartománynév hozzáadását](https://channel9.msdn.com/blogs/Azure-App-Service-Self-Help/Add-a-Custom-Domain-Name)ismertető témakört. A videó bemutatja, hogyan adhat hozzá egyéni tartománynevet. Ez a cikk azt ismerteti, hogyan használható a saját URL-cím a *. azurewebsites.net URL-cím helyett a App Service webalkalmazással. Az [Egyéni tartománynevek leképezésének](app-service-web-tutorial-custom-domain.md)részletes ismertetését is láthatja.


## <a name="how-do-i-purchase-a-new-custom-domain-for-my-web-app"></a>Hogyan új egyéni tartományt vásárolni a webalkalmazáshoz?

A App Service webalkalmazás egyéni tartományának megvásárlásáról és beállításáról további információt az [Egyéni tartománynév megvásárlása és konfigurálása a app Service-ben](manage-custom-dns-buy-domain.md)című témakörben talál.


## <a name="how-do-i-upload-and-configure-an-existing-tlsssl-certificate-for-my-web-app"></a>Hogyan egy meglévő TLS/SSL-tanúsítvány feltöltését és konfigurálását a webes alkalmazáshoz?

A meglévő egyéni TLS/SSL-tanúsítványok feltöltéséről és beállításáról további információt a következő témakörben talál: [TLS/SSL-tanúsítvány hozzáadása a app Service alkalmazáshoz](configure-ssl-certificate.md).


## <a name="how-do-i-purchase-and-configure-a-new-tlsssl-certificate-in-azure-for-my-web-app"></a>Hogyan egy új TLS/SSL-tanúsítvány megvásárlását és konfigurálását az Azure-ban a webes alkalmazáshoz?

Ha meg szeretné tudni, hogyan vásárolhat meg és állíthat be TLS/SSL-tanúsítványt a App Service webalkalmazáshoz, tekintse meg a [TLS/SSL-tanúsítvány hozzáadása a app Service alkalmazáshoz](configure-ssl-certificate.md)című témakört.


## <a name="how-do-i-move-application-insights-resources"></a>Hogyan Application Insights erőforrások áthelyezése?

Az Azure Application Insights jelenleg nem támogatja az áthelyezési műveletet. Ha az eredeti erőforráscsoport Application Insights erőforrást tartalmaz, nem helyezheti át az adott erőforrást. Ha a Application Insights erőforrást egy App Service alkalmazás áthelyezésére való kísérlet során is felveszi, a teljes áthelyezési művelet meghiúsul. Azonban Application Insights és a App Service tervnek nem kell ugyanabban az erőforráscsoporthoz lennie, mint az alkalmazás megfelelő működéséhez.

További információ: [app Service korlátozások](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md).

## <a name="where-can-i-find-a-guidance-checklist-and-learn-more-about-resource-move-operations"></a>Hol találhatok útmutatási ellenőrzőlistát, és többet tudhat meg az erőforrás-áthelyezési műveletekről?

A [app Service korlátozások](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md) azt mutatják be, hogy miként helyezhetők át erőforrások egy új előfizetésbe vagy egy új erőforráscsoporthoz ugyanabba az előfizetésbe. Az erőforrás-áthelyezési ellenőrzőlistával kapcsolatos információkat megtudhatja, hogy mely szolgáltatások támogatják az áthelyezési műveletet, valamint további tudnivalókat App Service korlátozásokról és egyéb témakörökről.

## <a name="how-do-i-set-the-server-time-zone-for-my-web-app"></a>Hogyan beállítani a webalkalmazás kiszolgálói időzónáját?

A webalkalmazás kiszolgálói időzónájának beállítása:

1. Az Azure Portal App Service előfizetésében lépjen az **Alkalmazásbeállítások** menüre.
2. Az **Alkalmazásbeállítások** területen adja hozzá a következő beállítást:
    * Kulcs = WEBSITE_TIME_ZONE
    * Value = *a kívánt időzóna*
3. Kattintson a **Mentés** gombra.

A Windows rendszeren futó app Services esetében tekintse meg a Windows parancs kimenetét `tzutil /L` . Használja az egyes bejegyzések második sorában található értéket. Például: "Tonga téli idő". Ezen értékek némelyike az [alapértelmezett időzónák](/windows-hardware/manufacture/desktop/default-time-zones) **timezone** oszlopában is szerepel.

A Linuxon futó app Services esetében állítson be egy értéket az [IANA TZ-adatbázisból](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones). Például: "America/Adak".

## <a name="why-do-my-continuous-webjobs-sometimes-fail"></a>Miért nem sikerül a folyamatos webjobs-feladatok néha?

Alapértelmezés szerint a webalkalmazások el lesznek távolítva, ha egy meghatározott időtartam alatt tétlenek. Ez lehetővé teszi, hogy a rendszerek megőrizze erőforrásaikat. Az alapszintű és a standard csomagokban az **Always On** beállítás bekapcsolásával megtarthatja a webalkalmazás betöltésének időpontját. Ha a webalkalmazás folyamatos webjobs-feladatokat futtat, akkor a **mindig** be kell kapcsolnia, vagy előfordulhat, hogy a webjobs nem megbízhatóan fut. További információt a [folyamatosan futó Webjobs létrehozása](webjobs-create.md#CreateContinuous)című témakörben talál.

## <a name="how-do-i-get-the-outbound-ip-address-for-my-web-app"></a>Hogyan lekérni a webes alkalmazás kimenő IP-címét?

A webalkalmazáshoz tartozó kimenő IP-címek listájának lekérése:

1. A Azure Portal a webalkalmazás paneljén lépjen a **Tulajdonságok** menüre.
2. Keresse meg a **kimenő IP-címeket**.

Megjelenik a kimenő IP-címek listája.

Ha szeretné megtudni, hogyan kérheti le a kimenő IP-címet, ha webhelye egy App Service Environmentban található, tekintse meg a [kimenő hálózati címeket](environment/app-service-app-service-environment-network-architecture-overview.md#outbound-network-addresses).

## <a name="how-do-i-get-a-reserved-or-dedicated-inbound-ip-address-for-my-web-app"></a>Hogyan lekérni a webalkalmazáshoz fenntartott vagy dedikált bejövő IP-címet?

Ha dedikált vagy fenntartott IP-címet szeretne beállítani az Azure-alkalmazás webhelyére érkező bejövő hívásokhoz, telepítsen és konfiguráljon egy IP-alapú TLS/SSL-tanúsítványt.

Vegye figyelembe, hogy ha dedikált vagy fenntartott IP-címet szeretne használni a bejövő hívások számára, a App Service tervnek alapszintű vagy magasabb szolgáltatási csomagban kell lennie.

## <a name="can-i-export-my-app-service-certificate-to-use-outside-azure-such-as-for-a-website-hosted-elsewhere"></a>Exportálhatók az App Service-tanúsítvány az Azure-on kívüli használatra, például egy máshol üzemeltetett webhelyre? 

Igen, az Azure-on kívül is exportálhatja őket. További információ: App Service- [tanúsítványok és egyéni tartományok – gyakori kérdések](https://social.msdn.microsoft.com/Forums/azure/f3e6faeb-5ed4-435a-adaa-987d5db43b80/faq-on-app-service-certificates-and-custom-domains?forum=windowsazurewebsitespreview).

## <a name="can-i-export-my-app-service-certificate-to-use-with-other-azure-cloud-services"></a>Exportálhatók a App Service-tanúsítványom más Azure Cloud Services-szolgáltatásokkal való használatra?

A portál egy első osztályú élményt nyújt az App Service-tanúsítványok Azure Key Vault használatával történő üzembe helyezéséhez App Service alkalmazásokhoz. Az ügyfelektől érkező kéréseket azonban a App Service platformon kívül is használni fogjuk, például az Azure Virtual Machines. Ha meg szeretné tudni, hogyan hozhat létre helyi PFX-példányt a App Service-tanúsítványból, hogy a tanúsítványt más Azure-erőforrásokkal is használhassa, tekintse meg a [app Service tanúsítvány helyi pfx-példányának létrehozása](https://blogs.msdn.microsoft.com/appserviceteam/2017/02/24/creating-a-local-pfx-copy-of-app-service-certificate/)című témakört.

További információ: App Service- [tanúsítványok és egyéni tartományok – gyakori kérdések](https://social.msdn.microsoft.com/Forums/azure/f3e6faeb-5ed4-435a-adaa-987d5db43b80/faq-on-app-service-certificates-and-custom-domains?forum=windowsazurewebsitespreview).


## <a name="why-do-i-see-the-message-partially-succeeded-when-i-try-to-back-up-my-web-app"></a>Miért jelenik meg a "részlegesen sikeres" üzenet, amikor megpróbálok biztonsági másolatot készíteni a webalkalmazásról?

A biztonsági mentési hiba gyakori oka az, hogy egyes fájlokat az alkalmazás használ. A használatban lévő fájlok zárolva vannak a biztonsági mentés végrehajtása közben. Ez megakadályozza, hogy ezek a fájlok biztonsági mentést lehessen készíteni, és a "részlegesen sikeres" állapotot eredményeznek. Lehetséges, hogy a biztonsági mentési folyamatból kizárják a fájlokat. Dönthet úgy is, hogy csak a szükséges biztonsági mentést készíti el. További információ: [biztonsági másolat készítése a webhely fontos részeiről az Azure Web Apps](https://zainrizvi.io/blog/creating-partial-backups-of-your-site-with-azure-web-apps/)szolgáltatással.

## <a name="how-do-i-remove-a-header-from-the-http-response"></a>Hogyan eltávolítani a HTTP-válasz fejlécét?

Ha el szeretné távolítani a fejléceket a HTTP-válaszból, frissítse a hely web.config fájlját. További információ: a [szabványos kiszolgálók fejlécének eltávolítása az Azure-webhelyeken](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/).

## <a name="is-app-service-compliant-with-pci-standard-30-and-31"></a>App Service megfelel a PCI standard 3,0-es és 3,1-es verziójának?

Jelenleg a Azure App Service Web Apps funkciója megfelel az 1. szintű PCI adatbiztonsági szabványnak (DSS) 3,0-es verziójának. A PCI DSS 3,1-es verziója az ütemterven található. A tervezés már folyamatban van a legújabb szabvány bevezetésének módjával kapcsolatban.

PCI DSS 3,1-es verziójának minősítéséhez le kell tiltania a Transport Layer Security (TLS) 1,0. Jelenleg a TLS 1,0 letiltása nem lehetséges a legtöbb App Service csomag esetében. Ha azonban App Service Environmentt használ, vagy ha a számítási feladatot App Service Environmentre kívánja áttelepíteni, akkor nagyobb mértékben vezérelheti a környezetét. Ez magában foglalja a TLS 1,0 letiltását az Azure-támogatással való kapcsolatfelvételsel. A közeljövőben azt tervezzük, hogy ezek a beállítások elérhetők legyenek a felhasználók számára.

További információ: [Microsoft Azure app Service web app Compliance a PCI Standard 3,0 és 3,1](https://support.microsoft.com/help/3124528).

## <a name="how-do-i-use-the-staging-environment-and-deployment-slots"></a>Hogyan az átmeneti környezet és az üzembe helyezési pontok használatát?

A standard és a prémium szintű App Service csomagok esetében a webalkalmazásnak a App Service üzembe helyezése során az alapértelmezett üzemi tárolóhely helyett egy különálló üzembe helyezési pontra is telepíthető. Az üzembe helyezési pontok olyan élő webalkalmazások, amelyek saját állomásnévvel rendelkeznek. A webalkalmazás tartalmait és konfigurációs elemeit két üzembe helyezési pont között lehet felcserélni, beleértve az üzemi tárolóhelyet is.

További információ az üzembe helyezési pontok használatáról: [átmeneti környezet beállítása app Serviceban](deploy-staging-slots.md).

## <a name="how-do-i-access-and-review-webjob-logs"></a>Hogyan a hozzáférés és a Webjobs-naplók áttekintése?

Webjobs-naplók áttekintése:

1. Jelentkezzen be a **kudu webhelyére** ( `https://*yourwebsitename*.scm.azurewebsites.net` ).
2. Válassza ki a Webjobs.
3. Válassza a **kimenet váltása** gombot.
4. A kimeneti fájl letöltéséhez válassza a **letöltési** hivatkozást.
5. Az egyes futtatások esetében válassza az **Egyéni meghívás** lehetőséget.
6. Válassza a **kimenet váltása** gombot.
7. Válassza ki a letöltési hivatkozást.

## <a name="im-trying-to-use-hybrid-connections-with-sql-server-why-do-i-see-the-message-systemoverflowexception-arithmetic-operation-resulted-in-an-overflow"></a>A SQL Server használatával Próbálom használni a Hibrid kapcsolatok. Miért látom a "System. OverflowException: aritmetikai művelet túlcsordulást eredményezett" üzenetet?

Ha Hibrid kapcsolatokt használ a SQL Server eléréséhez, Microsoft .NET a 2016-as számú frissítés sikertelen lehet, mert a kapcsolat meghiúsul. A következő üzenet jelenhet meg:

```
Exception: System.Data.Entity.Core.EntityException: The underlying provider failed on Open. —> System.OverflowException: Arithmetic operation resulted in an overflow. or (64 bit Web app) System.OverflowException: Array dimensions exceeded supported range, at System.Data.SqlClient.TdsParser.ConsumePreLoginHandshake
```

### <a name="resolution"></a>Feloldás

A kivételt az a hibridkapcsolat-kezelő, amely azóta megoldódott. Ügyeljen rá, hogy a probléma megoldásához [frissítse a hibridkapcsolat-kezelő](https://go.microsoft.com/fwlink/?LinkID=841308) .

## <a name="how-do-i-add-a-url-rewrite-rule"></a>Hogyan adjon hozzá egy URL-átírási szabályt?

URL-re vonatkozó Újraírási szabály hozzáadásához hozzon létre egy web.config fájlt a megfelelő konfigurációs bejegyzésekkel a **wwwroot** mappában. További információt az [Azure app Services: az URL-cím újraírásának ismertetése](/archive/blogs/madhurabharadwaj/azure-app-services-understanding-url-re-write)című témakörben talál.

## <a name="how-do-i-control-inbound-traffic-to-app-service"></a>Hogyan a bejövő forgalom vezérlését App Servicere?

A hely szintjén két lehetőség közül választhat a App Service bejövő forgalmának szabályozására:

* Kapcsolja be a dinamikus IP-korlátozásokat. A dinamikus IP-korlátozások bekapcsolásának megismeréséhez tekintse meg [Az Azure websites IP-és tartományi korlátozásait](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/)ismertető témakört.
* A modul biztonságának bekapcsolása. A modul biztonságának bekapcsolásával kapcsolatos további információkért lásd: [ModSecurity webalkalmazási tűzfal az Azure websites szolgáltatásban](https://azure.microsoft.com/blog/modsecurity-for-azure-websites/).

Ha App Service Environment használ, a [Barracuda-tűzfalat](https://azure.microsoft.com/blog/configuring-barracuda-web-application-firewall-for-azure-app-service-environment/)használhatja.

## <a name="how-do-i-block-ports-in-an-app-service-web-app"></a>Hogyan letiltani a portokat egy App Service webalkalmazásban?

A App Service megosztott bérlői környezetben az infrastruktúra jellegéből adódóan nem lehet blokkolni bizonyos portokat. A 4020, 4022 és 4024 TCP-portok is megnyithatók a Visual Studio távoli hibakereséshez.

App Service Environment a bejövő és a kimenő forgalmat teljes mértékben szabályozhatja. A hálózati biztonsági csoportokkal korlátozhatja vagy letilthatja az adott portok használatát. További információ a App Service Environmentről: [app Service Environment bemutatása](https://azure.microsoft.com/blog/introducing-app-service-environment/).

## <a name="how-do-i-capture-an-f12-trace"></a>Hogyan egy F12-nyomkövetés rögzítését?

Az F12-nyomkövetés rögzítésére két lehetőség áll rendelkezésre:

* F12 HTTP-nyomkövetés
* F12-konzol kimenete

### <a name="f12-http-trace"></a>F12 HTTP-nyomkövetés

1. Az Internet Explorerben lépjen a webhelyre. A következő lépések elvégzése előtt fontos bejelentkezni. Ellenkező esetben az F12 nyomkövetés a bizalmas bejelentkezési adatokat rögzíti.
2. Nyomja meg az F12 billentyűt.
3. Ellenőrizze, hogy a **hálózat** lap ki van-e választva, majd kattintson a zöld **Lejátszás** gombra.
4. Végezze el a probléma ismételt előállításához szükséges lépéseket.
5. Kattintson a vörös **Leállítás** gombra.
6. Válassza a **Mentés** gombot (lemez ikon), és mentse a har-fájlt (az Internet Explorerben és a Microsoft Edge-ben), *vagy* kattintson a jobb gombbal a har-fájlra, majd válassza a **Mentés másként har a tartalommal** (a Chrome-ban) lehetőséget.

### <a name="f12-console-output"></a>F12-konzol kimenete

1. Válassza a **konzol** fület.
2. Minden olyan lap esetében, amely nullánál több elemet tartalmaz, válassza ki a fület (**hiba**, **Figyelmeztetés** vagy **információ**). Ha a lap nincs bejelölve, a TAB ikon szürke vagy fekete, ha a kurzort onnan helyezi át.
3. Kattintson a jobb gombbal a panel üzenet területére, majd válassza az **összes másolása** lehetőséget.
4. Illessze be a másolt szöveget egy fájlba, majd mentse a fájlt.

Egy HAR-fájl megtekintéséhez használhatja a [har Viewert](http://www.softwareishard.com/har/viewer/).

## <a name="why-do-i-get-an-error-when-i-try-to-connect-an-app-service-web-app-to-a-virtual-network-that-is-connected-to-expressroute"></a>Miért kapok hibaüzenetet, amikor megpróbálok csatlakoztatni egy App Service webalkalmazást egy ExpressRoute-hez csatlakozó virtuális hálózathoz?

Ha Azure-webalkalmazást próbál csatlakoztatni egy Azure-ExpressRoute csatlakoztatott virtuális hálózathoz, az sikertelen lesz. A következő üzenet jelenik meg: "az átjáró nem VPN-átjáró."

Jelenleg nem rendelkezhet pont – hely típusú VPN-kapcsolattal olyan virtuális hálózattal, amely a ExpressRoute-hez csatlakozik. Egy pont – hely típusú VPN-és ExpressRoute nem létezhet egyszerre ugyanahhoz a virtuális hálózathoz. További információ: a [ExpressRoute és a helyek közötti VPN-kapcsolatok korlátai és korlátozásai](../expressroute/expressroute-howto-coexist-classic.md#limits-and-limitations).

## <a name="how-do-i-connect-an-app-service-web-app-to-a-virtual-network-that-has-a-static-routing-policy-based-gateway"></a>Hogyan egy App Service webalkalmazást egy statikus útválasztási (házirend-alapú) átjáróval rendelkező virtuális hálózathoz?

Jelenleg nem támogatott egy App Service webalkalmazás csatlakoztatása egy statikus útválasztási (házirend-alapú) átjáróval rendelkező virtuális hálózathoz. Ha a célként megadott virtuális hálózat már létezik, akkor az alkalmazáshoz való csatlakozás előtt a pont – hely típusú VPN-nek engedélyezve kell lennie, és dinamikus útválasztási átjárót kell használnia. Ha az átjáró statikus útválasztásra van beállítva, akkor nem engedélyezheti pont – hely típusú VPN-t. 

További információ: [alkalmazás integrálása Azure-beli virtuális hálózattal](web-sites-integrate-with-vnet.md).

## <a name="in-my-app-service-environment-why-can-i-create-only-one-app-service-plan-even-though-i-have-two-workers-available"></a>A saját App Service Environmenton miért hozhatok létre csak egy App Service csomagot, még ha van két feldolgozóm?

A hibatűrés biztosításához App Service Environment megköveteli, hogy mindegyik feldolgozó készletnek legalább egy további számítási erőforrásra van szüksége. A további számítási erőforrás nem rendelhető hozzá munkaterheléshez.

További információ: [app Service Environment létrehozása](environment/app-service-web-how-to-create-an-app-service-environment.md).

## <a name="why-do-i-see-timeouts-when-i-try-to-create-an-app-service-environment"></a>Miért látok időtúllépéseket, amikor megpróbálok létrehozni egy App Service Environment?

Előfordulhat, hogy egy App Service Environment létrehozása sikertelen. Ebben az esetben a következő hibaüzenet jelenik meg a tevékenység naplófájljaiban:
```
ResourceID: /subscriptions/{SubscriptionID}/resourceGroups/Default-Networking/providers/Microsoft.Web/hostingEnvironments/{ASEname}
Error:{"error":{"code":"ResourceDeploymentFailure","message":"The resource provision operation did not complete within the allowed timeout period."}}
```

A probléma megoldásához győződjön meg arról, hogy a következő feltételek egyike sem teljesül:
* Az alhálózat túl kicsi.
* Az alhálózat nem üres.
* A ExpressRoute meggátolja egy App Service Environment hálózati kapcsolati követelményeit.
* A hibás hálózati biztonsági csoport megakadályozza a App Service Environment hálózati kapcsolati követelményeit.
* A kényszerített bújtatás be van kapcsolva.

További információkért lásd: [új Azure app Service Environment telepítésekor (létrehozásakor) gyakori problémák](/archive/blogs/waws/most-frequent-issues-when-deploying-creating-a-new-azure-app-service-environment-ase).

## <a name="why-cant-i-delete-my-app-service-plan"></a>Miért nem tudom törölni a App Service csomagot?

App Service-csomag nem törölhető, ha bármely App Service alkalmazás társítva van az App Service-csomaggal. App Service csomag törlése előtt távolítsa el az összes társított App Service-alkalmazást az App Service-csomagból.

## <a name="how-do-i-schedule-a-webjob"></a>Hogyan ütemezhet egy Webjobs?

A cron-kifejezések használatával ütemezett Webjobs is létrehozhat:

1. Hozzon létre egy Settings. job fájlt.
2. A JSON-fájlban adjon meg egy Schedule tulajdonságot a cron kifejezés használatával: 
    ```json
    { "schedule": "{second}
    {minute} {hour} {day}
    {month} {day of the week}" }
    ```

Az ütemezett webjobs-feladatokkal kapcsolatos további információkért tekintse meg az [ütemezett Webjobs cron-kifejezés használatával történő létrehozását](webjobs-create.md#CreateScheduledCRON)ismertető témakört.

## <a name="how-do-i-perform-penetration-testing-for-my-app-service-app"></a>Hogyan a App Service alkalmazáshoz való behatolás-tesztelést?

A behatolási teszt elvégzéséhez [küldje el a kérelmet](https://portal.msrc.microsoft.com/engage/pentest).

## <a name="how-do-i-configure-a-custom-domain-name-for-an-app-service-web-app-that-uses-traffic-manager"></a>Hogyan a Traffic Managert használó App Service webalkalmazáshoz egyéni tartománynevet konfigurálhat?

Ha szeretné megismerni, hogyan használhat egyéni tartománynevet egy olyan App Service alkalmazással, amely az Azure Traffic Managert használja a terheléselosztáshoz, tekintse meg az [Egyéni tartománynév konfigurálása az Azure-webalkalmazásokhoz Traffic Manager](configure-domain-traffic-manager.md)használatával című témakört.

## <a name="my-app-service-certificate-is-flagged-for-fraud-how-do-i-resolve-this"></a>Az App Service-tanúsítványom csalás miatt van megjelölve. Hogyan oldhatom meg ezt?

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Az App Service-tanúsítvány megvásárlásának tartományi ellenőrzése során a következő üzenet jelenhet meg:

"A tanúsítvány a lehetséges csalások miatt lett megjelölve. A kérelem jelenleg felülvizsgálat alatt áll. Ha a tanúsítvány 24 órán belül nem lesz használható, forduljon az Azure ügyfélszolgálatához. "

Az üzenet azt jelzi, hogy a csalás ellenőrzési folyamata akár 24 órát is igénybe vehet. Ebben az időszakban továbbra is megjelenik az üzenet.

Ha a App Service-tanúsítvány 24 óra elteltével továbbra is megjeleníti ezt az üzenetet, futtassa a következő PowerShell-szkriptet. A parancsfájl közvetlenül kapcsolatba lép a [hitelesítésszolgáltatóval](https://www.godaddy.com/) a probléma megoldásához.

```powershell
Connect-AzAccount
Set-AzContext -SubscriptionId <subId>
$actionProperties = @{
    "Name"= "<Customer Email Address>"
    };
Invoke-AzResourceAction -ResourceGroupName "<App Service Certificate Resource Group Name>" -ResourceType Microsoft.CertificateRegistration/certificateOrders -ResourceName "<App Service Certificate Resource Name>" -Action resendRequestEmails -Parameters $actionProperties -ApiVersion 2015-08-01 -Force   
```

## <a name="how-do-authentication-and-authorization-work-in-app-service"></a>Hogyan működik a hitelesítés és az engedélyezés App Service?

A App Service hitelesítésével és engedélyezésével kapcsolatos részletes dokumentációért lásd: a különböző azonosítási szolgáltatói bejelentkezésekhez szükséges dokumentumok.
* [Azure Active Directory](configure-authentication-provider-aad.md)
* [Facebook](configure-authentication-provider-facebook.md)
* [Google](configure-authentication-provider-google.md)
* [Microsoft-fiók](configure-authentication-provider-microsoft.md)
* [Twitter](configure-authentication-provider-twitter.md)

## <a name="how-do-i-redirect-the-default-azurewebsitesnet-domain-to-my-azure-web-apps-custom-domain"></a>Hogyan átirányítja az alapértelmezett *. azurewebsites.net tartományt az Azure-webalkalmazás egyéni tartományára?

Új webhely Azure-beli Web Apps használatával történő létrehozásakor a rendszer egy alapértelmezett *sitename*. azurewebsites.net tartományt rendel hozzá a webhelyhez. Ha egyéni állomásnevet ad hozzá a webhelyhez, és nem szeretné, hogy a felhasználók hozzáférhessenek az alapértelmezett *. azurewebsites.net tartományhoz, akkor átirányíthatja az alapértelmezett URL-címet. Ha meg szeretné tudni, hogyan irányíthatja át a webhely alapértelmezett tartományában lévő összes forgalmat az egyéni tartományba, tekintse meg [az alapértelmezett tartomány átirányítása az egyéni tartományba az Azure Web Apps szolgáltatásban](https://zainrizvi.io/blog/block-default-azure-websites-domain/)című témakört.

## <a name="how-do-i-determine-which-version-of-net-version-is-installed-in-app-service"></a>Hogyan határozza meg, hogy a .NET-verzió melyik verziója van telepítve a App Service-ben?

A App Serviceban telepített Microsoft .NET verziószámának leggyorsabb módja a kudu-konzol használata. A kudu-konzolt a portálról vagy a App Service alkalmazás URL-címének használatával érheti el. Részletes útmutatás: [a telepített .NET-verzió meghatározása app Service](/archive/blogs/waws/how-to-determine-the-installed-net-version-in-azure-app-services).

## <a name="why-isnt-autoscale-working-as-expected"></a>Miért nem a várt módon működik az autoskálázás?

Ha az Azure-beli autoskálázás a várt módon nem méretezi át vagy bővíti a webalkalmazási példányt, előfordulhat, hogy olyan forgatókönyvben fut, amelyben szándékosan nem méretezhető, hogy elkerülje a végtelen hurkot a "csapkodás" miatt. Ez általában akkor fordul elő, ha nincs megfelelő árrés a kibővített és a skálázási küszöbértékek között. Ha szeretné megtudni, hogyan kerülheti el a "csapkodás" és az egyéb, az autoscale ajánlott eljárásaival kapcsolatos információkat, tekintse meg az [ajánlott eljárásokat](../azure-monitor/autoscale/autoscale-best-practices.md#autoscale-best-practices).

## <a name="why-does-autoscale-sometimes-scale-only-partially"></a>Miért érdemes a méretezést időnként csak részben méretezni?

Az autoskálázás akkor aktiválódik, ha a metrikák túllépik az előre konfigurált határokat. Időnként észreveheti, hogy a kapacitás csak részben lett kitöltve, mint a várt módon. Ez akkor fordulhat elő, ha a kívánt példányok száma nem érhető el. Ebben a forgatókönyvben a részlegesen kitölti az automatikusan a rendelkezésre álló példányszámot. Az autoscale ezt követően az egyensúly logikáját futtatja, hogy nagyobb kapacitást kapjon. A fennmaradó példányokat foglalja le. Vegye figyelembe, hogy ez eltarthat néhány percig.

Ha nem látja a várt számú példányt néhány perc múlva, annak oka az lehet, hogy a részleges Újratöltés elég volt ahhoz, hogy a metrikák a határokon belül legyenek. Vagy az autoscale méretezése megnövelhető, mert elérte az alsó metrikák határát.

Ha a feltételek egyike sem teljesül, és a probléma továbbra is fennáll, küldjön be egy támogatási kérést.

## <a name="how-do-i-turn-on-http-compression-for-my-content"></a>Hogyan bekapcsolja a saját tartalom HTTP-tömörítését?

A statikus és a dinamikus tartalomtípusok tömörítésének bekapcsolásához adja hozzá a következő kódot az alkalmazás szintű web.config fájlhoz:

```xml
<system.webServer>
    <urlCompression doStaticCompression="true" doDynamicCompression="true" />
</system.webServer>
```

Megadhatja a tömöríteni kívánt dinamikus és statikus MIME-típusokat is. További információkért tekintse [meg a httpCompression-beállítások egy egyszerű Azure-webhelyen](https://social.msdn.microsoft.com/Forums/azure/890b6d25-f7dd-4272-8970-da7798bcf25d/httpcompression-settings-on-a-simple-azure-website?forum=windowsazurewebsitespreview)történő megválaszolása című témakörre adott választ.

## <a name="how-do-i-migrate-from-an-on-premises-environment-to-app-service"></a>Hogyan Migrálás egy helyszíni környezetből a App Serviceba?

Ha Windows és Linux rendszerű webkiszolgálókról kíván áttelepíteni helyeket App Serviceba, használhatja a Azure App Service Migration Assistant. Az áttelepítési eszköz szükség szerint hozza létre a webalkalmazásokat és-adatbázisokat az Azure-ban, majd közzéteszi a tartalmat. További információ: [Azure App Service Migration Assistant](https://appmigration.microsoft.com/).

## <a name="why-is-my-certificate-issued-for-11-months-and-not-for-a-full-year"></a>Miért van a tanúsítványom 11 hónapra kiállítva, és nem egy teljes évre?

Jelenleg a 2020. szeptember 1. után kiadott összes tanúsítvány maximális időtartama 397 nap. A 2020. szeptember 1. előtt kiadott tanúsítványok legfeljebb 825 napig érvényesek, mielőtt szükségessé válna a megújításuk, a kulcsuk újbóli megadása stb. Ez a változás érinti a 2020. szeptember 1. után megújított tanúsítványokat, így a felhasználók azzal szembesülhetnek, hogy a megújított tanúsítványaik rövidebb ideig érvényesek.
A GoDaddy bevezetett egy olyan előfizetési szolgáltatást, amely megfelel az új követelményeknek, miközben a meglévő ügyfél-tanúsítványokat is elfogadja. Az újonnan kiállított tanúsítványok lejárta előtt 30 nappal a szolgáltatás automatikusan kiad egy második tanúsítványt, amely meghosszabbítja az érvényességi időtartamot az eredeti lejárati dátumig. Az App Service együttműködik a GoDaddyvel ezen változást illetően, hogy ügyfeleink mindenképp kihasználhassák a tanúsítványuk teljes időtartamát.
