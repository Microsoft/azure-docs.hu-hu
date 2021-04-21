---
title: Konfigurációs gyakori kérdések
description: Választ kaphat a problémák konfigurációs és felügyeleti problémáival kapcsolatos gyakori Azure App Service.
author: genlin
manager: dcscontentpm
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.topic: article
ms.date: 10/30/2018
ms.author: genli
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d4b6ceef837d985db004e9d925b554c54c287424
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107834533"
---
# <a name="configuration-and-management-faqs-for-web-apps-in-azure"></a>Az Azure-beli Web Apps és kezelése – gyakori kérdések

Ez a cikk választ ad az alkalmazás Web Apps funkciójának konfigurálási és kezelési problémáival kapcsolatos gyakori [kérdésekre Azure App Service.](https://azure.microsoft.com/services/app-service/web/)

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="are-there-limitations-i-should-be-aware-of-if-i-want-to-move-app-service-resources"></a>Vannak olyan korlátozások, amelyekről érdemes tisztában lenni, ha át szeretném App Service erőforrásokat?

Ha azt tervezi, App Service erőforrásokat egy új erőforráscsoportba vagy előfizetésbe, néhány korlátozást figyelembe kell vegye. További információ: App Service [korlátozásai.](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)

## <a name="how-do-i-use-a-custom-domain-name-for-my-web-app"></a>Hogyan egyéni tartománynevet használ a webalkalmazásomhoz?

Az egyéni tartománynév Azure-webalkalmazással való használatával kapcsolatos gyakori kérdésekre a következő hétperces videóban talál választ: [Egyéni tartománynév hozzáadása.](https://channel9.msdn.com/blogs/Azure-App-Service-Self-Help/Add-a-Custom-Domain-Name) A videó bemutatja, hogyan adhat hozzá egyéni tartománynevet. Leírja, hogyan használhatja a saját URL-címét a *.azurewebsites.net URL helyett a App Service webalkalmazással. Egy egyéni tartománynév leképezésének részletes útmutatóját [is láthatja.](app-service-web-tutorial-custom-domain.md)


## <a name="how-do-i-purchase-a-new-custom-domain-for-my-web-app"></a>Hogyan új egyéni tartományt vásárol a webalkalmazásomhoz?

Ha meg szeretne ismerkedni egy egyéni tartomány megvásárlásával és beállítással a App Service-webalkalmazáshoz, olvassa el az Egyéni tartománynév vásárlása és konfigurálása [a](manage-custom-dns-buy-domain.md)App Service.


## <a name="how-do-i-upload-and-configure-an-existing-tlsssl-certificate-for-my-web-app"></a>Hogyan fel és konfigurál egy meglévő TLS-/SSL-tanúsítványt a webalkalmazásomhoz?

Meglévő egyéni TLS-/SSL-tanúsítvány feltöltésének és beállításának elsajátításért [lásd: TLS/SSL-tanúsítvány](configure-ssl-certificate.md)hozzáadása az App Service alkalmazáshoz.


## <a name="how-do-i-purchase-and-configure-a-new-tlsssl-certificate-in-azure-for-my-web-app"></a>Hogyan és konfigurál egy új TLS-/SSL-tanúsítványt az Azure-ban a webalkalmazásomhoz?

TLS-/SSL-tanúsítvány vásárlását és beállítását a App Service-webalkalmazáshoz lásd: [TLS-/SSL-tanúsítvány](configure-ssl-certificate.md)hozzáadása a App Service alkalmazáshoz.


## <a name="how-do-i-move-application-insights-resources"></a>Hogyan áthelyezi Application Insights erőforrásokat?

Az Azure Application Insights nem támogatja az áthelyezési műveletet. Ha az eredeti erőforráscsoport tartalmaz egy Application Insights erőforrást, akkor az erőforrás nem áthelyezhető. Ha a Application Insights erőforrást az alkalmazás áthelyezésekor App Service, a teljes áthelyezési művelet meghiúsul. A Application Insights és App Service csomagnak azonban nem kell ugyanabban az erőforráscsoportban lennie, mint az alkalmazásnak ahhoz, hogy az alkalmazás megfelelően működjön.

További információ: App Service [korlátozásai.](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)

## <a name="where-can-i-find-a-guidance-checklist-and-learn-more-about-resource-move-operations"></a>Hol találhatok útmutató ellenőrzőlistát, és hol találhatok további információt az erőforrás-áthelyezési műveletekről?

[App Service bemutatja,](../azure-resource-manager/management/move-limitations/app-service-move-limitations.md) hogyan helyezze át az erőforrásokat egy új előfizetésbe vagy egy új erőforráscsoportba ugyanabban az előfizetésben. További információt kaphat az erőforrás-áthelyezési ellenőrzőlistról, megismerheti, hogy mely szolgáltatások támogatják az áthelyezési műveletet, és App Service és egyéb témakörökről.

## <a name="how-do-i-set-the-server-time-zone-for-my-web-app"></a>Hogyan a webalkalmazásom kiszolgálói időzónát?

A webalkalmazás kiszolgálói időzónájának beállítása:

1. A Azure Portal előfizetésében App Service az Alkalmazásbeállítások **menüt.**
2. Az **Alkalmazásbeállítások alatt** adja hozzá ezt a beállítást:
    * Kulcs = WEBSITE_TIME_ZONE
    * Érték = *A kívánt időzóna*
3. Kattintson a **Mentés** gombra.

A Windows rendszeren futó alkalmazásszolgáltatások esetén tekintse meg a Windows-parancs `tzutil /L` kimenetét. Minden bejegyzés második sorában használja az értéket. Például: "Tonga Standard Time". Ezen értékek némelyike az Alapértelmezett időzónák oszlop **Időzóna** [oszlopában is szerepel.](/windows-hardware/manufacture/desktop/default-time-zones)

A Linuxon futó alkalmazásszolgáltatásokhoz állítson be egy értéket az [IANA TZ-adatbázisból.](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones) Például: "America/Adak".

## <a name="why-do-my-continuous-webjobs-sometimes-fail"></a>Miért időnként meghiúsulnak a folyamatos WebJobs-feladatom?

Alapértelmezés szerint a rendszer eltávolítja a webalkalmazásokat, ha adott ideig tétlenek. Ez lehetővé teszi a rendszer számára az erőforrások felhasználását. Az Alapszintű és Standard csomagokban bekapcsolhatja az **Always On** beállítást, hogy a webalkalmazás folyamatosan be legyen töltve. Ha a webalkalmazás folyamatos WebJobs-feladatokat futtat, kapcsolja be az **Always On**(Mindig bekapcsolva) üzemmódot, különben előfordulhat, hogy a WebJobs-feladat nem fut megbízhatóan. További információ: [Folyamatosan futó WebJobs-feladat létrehozása.](webjobs-create.md#CreateContinuous)

## <a name="how-do-i-get-the-outbound-ip-address-for-my-web-app"></a>Hogyan a webalkalmazásom kimenő IP-címét?

A webalkalmazás kimenő IP-címeinek listájáért:

1. A Azure Portal a webalkalmazás panelen válassza a Tulajdonságok **menüt.**
2. Keresse meg a **kimenő IP-címeket.**

Megjelenik a kimenő IP-címek listája.

Ha meg szeretne ismerkedni a kimenő IP-cím lekért címével, ha a webhelye egy App Service Environment található, tekintse meg a [kimenő hálózati címeket.](environment/app-service-app-service-environment-network-architecture-overview.md#outbound-network-addresses)

## <a name="how-do-i-get-a-reserved-or-dedicated-inbound-ip-address-for-my-web-app"></a>Hogyan vagy dedikált bejövő IP-címet kap a webalkalmazásomhoz?

Ha dedikált vagy fenntartott IP-címet szeretné beállítani az Azure-alkalmazás webhelyére irányuló bejövő hívásokhoz, telepítsen és konfigurálja az IP-alapú TLS-/SSL-tanúsítványt.

Vegye figyelembe, hogy ha dedikált vagy fenntartott IP-címet használ a bejövő hívásokhoz, a App Service csomagnak alapszintű vagy magasabb szolgáltatási csomagba kell lennie.

## <a name="can-i-export-my-app-service-certificate-to-use-outside-azure-such-as-for-a-website-hosted-elsewhere"></a>Exportálható a App Service tanúsítványom az Azure-n kívüli használatra, például egy máshol üzemeltetett webhelyhez? 

Igen, exportálhatja őket az Azure-n kívüli használatra. További információkért lásd a tanúsítványokkal és egyéni App Service kapcsolatos gyakori [kérdések között.](https://social.msdn.microsoft.com/Forums/azure/f3e6faeb-5ed4-435a-adaa-987d5db43b80/faq-on-app-service-certificates-and-custom-domains?forum=windowsazurewebsitespreview)

## <a name="can-i-export-my-app-service-certificate-to-use-with-other-azure-cloud-services"></a>Exportálható a App Service tanúsítványom más Azure-felhőszolgáltatásokkal való használatra?

A portál első osztályú felhasználói élményt nyújt a tanúsítvány App Service alkalmazásokon keresztüli Azure Key Vault App Service telepítéséhez. Az ügyfelektől azonban kéréseket kapott, hogy ezeket a tanúsítványokat a App Service platformon kívül használják, például az Azure Virtual Machines. Az App Service-tanúsítvány helyi PFX App Service másolatának más Azure-erőforrásokkal való használatára való létrehozásáról lásd: Create a local PFX copy of an App Service certificate (Tanúsítvány helyi [PFX App Service létrehozása.](https://blogs.msdn.microsoft.com/appserviceteam/2017/02/24/creating-a-local-pfx-copy-of-app-service-certificate/)

További információkért lásd a tanúsítványokkal és egyéni App Service kapcsolatos gyakori [kérdések között.](https://social.msdn.microsoft.com/Forums/azure/f3e6faeb-5ed4-435a-adaa-987d5db43b80/faq-on-app-service-certificates-and-custom-domains?forum=windowsazurewebsitespreview)


## <a name="why-do-i-see-the-message-partially-succeeded-when-i-try-to-back-up-my-web-app"></a>Miért jelenik meg a "Részben sikeres" üzenet, amikor megpróbálom biztonságimentet küldeni a webalkalmazásomról?

A biztonsági mentés sikertelenségének gyakori oka, hogy egyes fájlokat az alkalmazás használ. A rendszer zárolja a használatban lévő fájlokat a biztonsági mentés végrehajtása közben. Ez megakadályozza a fájlok biztonsági mentéset, és "Részlegesen sikeres" állapotot eredményezhet. Ennek megelőzése érdekében kizárhatja a fájlokat a biztonsági mentési folyamatból. Dönthet úgy, hogy csak a szükséges biztonsági ről biztonságimentet. További információ: Backup just the important parts of your site with Azure web apps (A webhely fontos részeinek biztonsági mentése [Azure-webalkalmazásokkal).](https://zainrizvi.io/blog/creating-partial-backups-of-your-site-with-azure-web-apps/)

## <a name="how-do-i-remove-a-header-from-the-http-response"></a>Hogyan egy fejlécet a HTTP-válaszból?

Ha el szeretné távolítani a fejléceket a HTTP-válaszból, frissítse a webhely web.config fájlját. További információ: [Standard kiszolgálófejlécek eltávolítása az Azure-webhelyekről.](https://azure.microsoft.com/blog/removing-standard-server-headers-on-windows-azure-web-sites/)

## <a name="is-app-service-compliant-with-pci-standard-30-and-31"></a>Megfelel App Service PCI Standard 3.0 és 3.1 szabványnak?

Jelenleg a Web Apps szolgáltatás Azure App Service PCI Data Security Standard (DSS) 3.0 Level 1 verziójának felel meg. PCI DSS 3.1-es verziója már szerepel az ütemtervünkben. A legújabb szabvány bevezetésének tervezése már folyamatban van.

PCI DSS 3.1-es verziójú tanúsítvány használatához le kell tiltani a Transport Layer Security (TLS) 1.0-s verzióját. A TLS 1.0 letiltása jelenleg nem lehetséges a legtöbb App Service esetén. Ha azonban a számítási feladatokat App Service Environment, vagy hajlandó átemelni a App Service Environment, jobban átveheti az irányítást a környezet felett. Ehhez le kell tiltani a TLS 1.0-t úgy, hogy kapcsolatba lépünk a Azure ügyfélszolgálata. A közeljövőben tervezzük elérhetővé tenni ezeket a beállításokat a felhasználók számára.

További információ: Microsoft Azure App Service [PCI Standard 3.0 és 3.1 szabványnak való megfelelésének biztosítása.](https://support.microsoft.com/help/3124528)

## <a name="how-do-i-use-the-staging-environment-and-deployment-slots"></a>Hogyan az átmeneti környezetet és az üzembe helyezési pontokat?

Standard és Prémium App Service esetén, amikor a webalkalmazást az App Service-ban telepíti, az alapértelmezett üzemelő példány helyett egy különálló üzembe helyezési üzembehelyre is üzembe helyezheti. Az üzembe helyezési tárolóhelyek olyan élő webalkalmazások, amelyek saját gazdanevekkel rendelkezik. A webalkalmazás tartalma és a konfigurációs elemek felcserélheti őket két üzembe helyezési hely között, beleértve az üzemelő helyet is.

További információ az üzembe helyezési pontokkal kapcsolatban: Átmeneti környezet [beállítása a App Service.](deploy-staging-slots.md)

## <a name="how-do-i-access-and-review-webjob-logs"></a>Hogyan a WebJobs-naplókat?

A WebJobs-naplók áttekintése:

1. Jelentkezzen be a **Kudu webhelyére** ( `https://*yourwebsitename*.scm.azurewebsites.net` ).
2. Válassza a WebJobs-feladat lehetőséget.
3. Válassza **a Kimenet váltása gombot.**
4. A kimeneti fájl letöltéséhez kattintson a **Letöltés hivatkozásra.**
5. Egyéni futtatásként válassza az Egyéni **meghívás lehetőséget.**
6. Válassza **a Kimenet váltása gombot.**
7. Válassza a letöltési hivatkozást.

## <a name="im-trying-to-use-hybrid-connections-with-sql-server-why-do-i-see-the-message-systemoverflowexception-arithmetic-operation-resulted-in-an-overflow"></a>Hibrid kapcsolatokat próbálok használni a SQL Server. Miért jelenik meg a "System.OverflowException: Arithmetic operation result in an overflow" (A System.OverflowException: Aritmetikai művelet túlcsordulást eredményezett) üzenet?

Ha hibrid kapcsolatokkal fér hozzá a SQL Server, a Microsoft .NET 2016. május 10-i frissítése a kapcsolatok meghiúsulhat. A következő üzenet jelenik meg:

```
Exception: System.Data.Entity.Core.EntityException: The underlying provider failed on Open. —> System.OverflowException: Arithmetic operation resulted in an overflow. or (64 bit Web app) System.OverflowException: Array dimensions exceeded supported range, at System.Data.SqlClient.TdsParser.ConsumePreLoginHandshake
```

### <a name="resolution"></a>Feloldás

A kivételt a hiba okozta a hibridkapcsolat-kezelő, amely azóta ki lett javítva. A probléma [megoldásához frissítse hibridkapcsolat-kezelő](https://go.microsoft.com/fwlink/?LinkID=841308) a problémát.

## <a name="how-do-i-add-a-url-rewrite-rule"></a>Hogyan URL-átírási szabályt?

URL-átírási szabály hozzáadásához hozzon létre egy web.config fájlt a **wwwroot** mappában található megfelelő konfigurációs bejegyzésekkel. További információ: [Azure App Services: Az URL-átírás ismertetése.](/archive/blogs/madhurabharadwaj/azure-app-services-understanding-url-re-write)

## <a name="how-do-i-control-inbound-traffic-to-app-service"></a>Hogyan a bejövő forgalmat a App Service?

A webhely szintjén két lehetőség áll rendelkezésre a bejövő forgalom vezérlésére a App Service:

* Kapcsolja be a dinamikus IP-korlátozásokat. A dinamikus IP-korlátozások bekapcsolásának mikéntjére vonatkozó információkért lásd: IP- és [tartománykorlátozások az Azure-webhelyeken.](https://azure.microsoft.com/blog/ip-and-domain-restrictions-for-windows-azure-web-sites/)
* Kapcsolja be a Modulbiztonságot. A modulbiztonság bekapcsolására vonatkozó információkért lásd: [ModSecurity webalkalmazási tűzfal az Azure-webhelyeken.](https://azure.microsoft.com/blog/modsecurity-for-azure-websites/)

Ha a tűzfalat App Service Environment, a [Barracuda tűzfalat is használhatja.](https://azure.microsoft.com/blog/configuring-barracuda-web-application-firewall-for-azure-app-service-environment/)

## <a name="how-do-i-block-ports-in-an-app-service-web-app"></a>Hogyan blokkolni a portokat egy App Service-webalkalmazásban?

A App Service bérlői környezetben az infrastruktúra természetéből adódóan nem lehet blokkolni bizonyos portokat. A 4020-as, 4022-es és 4024-es TCP-portok is nyitva lehetnek Visual Studio távoli hibakereséshez.

Ebben App Service Environment a bejövő és kimenő forgalom teljes körű vezérlését. A hálózati biztonsági csoportokkal korlátozhatja vagy blokkolhatja a megadott portokat. További információ a App Service Environment: [Introducing App Service Environment](https://azure.microsoft.com/blog/introducing-app-service-environment/).

## <a name="how-do-i-capture-an-f12-trace"></a>Hogyan F12 nyomkövetést rögzít?

Az F12 nyomkövetés rögzítésére két lehetőség közül választhat:

* F12 HTTP-nyomkövetés
* F12 konzolkimenet

### <a name="f12-http-trace"></a>F12 HTTP-nyomkövetés

1. A Internet Explorer meg a webhelyét. Fontos, hogy a következő lépések előtt jelentkezzen be. Ellenkező esetben az F12 nyomkövetés bizalmas bejelentkezési adatokat rögzít.
2. Nyomja le az F12 billentyűt.
3. Ellenőrizze, hogy a **Hálózat lap** van-e kiválasztva, majd kattintson a zöld **Lejátszás gombra.**
4. Kövesse a problémát reprodukáló lépéseket.
5. Válassza a piros **Leállítás gombot.**
6. Válassza a **Mentés** gombot (lemez ikon), és mentse a HAR-fájlt (Internet Explorer és *Microsoft Edge),* vagy kattintson a jobb gombbal a HAR-fájlra, majd válassza a Mentés **HAR-fájlként** tartalommal (Chrome-ban) lehetőséget.

### <a name="f12-console-output"></a>F12 konzolkimenet

1. Válassza a **Konzol** fület.
2. Minden nulla elemnél több elemet tartalmazó lapon válassza ki a lapot (**Hiba,** **Figyelmeztetés**, vagy **Információ**). Ha a lap nincs kiválasztva, a tabulátor ikon szürke vagy fekete lesz, amikor a kurzort eltürkedi róla.
3. Kattintson a jobb gombbal a panel üzenetterületére, majd válassza az Összes **másolása lehetőséget.**
4. Illessze be a másolt szöveget egy fájlba, majd mentse a fájlt.

HAR-fájl megtekintéséhez használhatja a [HAR-megjelenítőt.](http://www.softwareishard.com/har/viewer/)

## <a name="why-do-i-get-an-error-when-i-try-to-connect-an-app-service-web-app-to-a-virtual-network-that-is-connected-to-expressroute"></a>Miért kapok hibaüzenetet, amikor egy App Service-webalkalmazást egy ExpressRoute-hoz csatlakozó virtuális hálózathoz próbálok csatlakoztatni?

Ha egy Azure-webalkalmazást egy olyan virtuális hálózathoz próbál csatlakoztatni, amely Azure ExpressRoute, az sikertelen lesz. A következő üzenet jelenik meg: "Az átjáró nem VPN-átjáró."

Jelenleg nem lehet pont–hely VPN-kapcsolat az ExpressRoute-hoz csatlakozó virtuális hálózathoz. A pont–hely VPN és az ExpressRoute nem egyidejűleg használható ugyanazon a virtuális hálózaton. További információ: Az ExpressRoute és a hely közötti [VPN-kapcsolatok korlátozásai és korlátozásai.](../expressroute/expressroute-howto-coexist-classic.md#limits-and-limitations)

## <a name="how-do-i-connect-an-app-service-web-app-to-a-virtual-network-that-has-a-static-routing-policy-based-gateway"></a>Hogyan egy App Service-webalkalmazást egy statikus útválasztású (házirendalapú) átjáróval rendelkezik virtuális hálózathoz?

A webalkalmazások App Service statikus útválasztású (házirendalapú) átjáróval rendelkezik virtuális hálózathoz való csatlakoztatása jelenleg nem támogatott. Ha a cél virtuális hálózat már létezik, engedélyeznie kell a pont–hely VPN-t dinamikus útválasztási átjáróval, mielőtt csatlakoztatható lenne egy alkalmazáshoz. Ha az átjáró statikus útválasztásra van beállítva, nem engedélyezheti a pont–hely VPN-t. 

További információ: [Alkalmazás integrálása Azure-beli virtuális hálózattal.](web-sites-integrate-with-vnet.md)

## <a name="in-my-app-service-environment-why-can-i-create-only-one-app-service-plan-even-though-i-have-two-workers-available"></a>A saját App Service Environment hozhatok létre csak egy App Service, annak ellenére, hogy két dolgozóm van?

A hibatűrés érdekében App Service Environment feldolgozókészletnek legalább egy további számítási erőforrásra van szüksége. A további számítási erőforráshoz nem rendelhető számítási feladat.

További információ: [How to create an App Service Environment.](environment/app-service-web-how-to-create-an-app-service-environment.md)

## <a name="why-do-i-see-timeouts-when-i-try-to-create-an-app-service-environment"></a>Miért látok időtúllépéseket, amikor megpróbálom létrehozni a App Service Environment?

Néha sikertelen egy App Service Environment létrehozása. Ebben az esetben a következő hiba jelenik meg a tevékenységnaplókban:
```
ResourceID: /subscriptions/{SubscriptionID}/resourceGroups/Default-Networking/providers/Microsoft.Web/hostingEnvironments/{ASEname}
Error:{"error":{"code":"ResourceDeploymentFailure","message":"The resource provision operation did not complete within the allowed timeout period."}}
```

A probléma megoldásához győződjön meg arról, hogy az alábbi feltételek egyike sem teljesül:
* Az alhálózat túl kicsi.
* Az alhálózat nem üres.
* Az ExpressRoute megakadályozza a hálózati kapcsolati követelmények App Service Environment.
* A rossz hálózati biztonsági csoportok megakadályozzák a hálózati kapcsolat követelményeit a App Service Environment.
* A kényszerített bújtatás be van kapcsolva.

További információ: Új alkalmazás [telepítésekor (létrehozásakor) előforduló gyakori Azure App Service Environment.](/archive/blogs/waws/most-frequent-issues-when-deploying-creating-a-new-azure-app-service-environment-ase)

## <a name="why-cant-i-delete-my-app-service-plan"></a>Miért nem tudom törölni a App Service csomagomat?

Nem törölhet egy App Service, ha App Service alkalmazás társítva van a App Service csomaggal. Mielőtt töröl egy App Service, távolítsa el az összes társított App Service a App Service csomagból.

## <a name="how-do-i-schedule-a-webjob"></a>Hogyan WebJobs-feladat ütemezése?

Ütemezett WebJobs-feladat Cron-kifejezésekkel hozható létre:

1. Hozzon létre egy settings.job fájlt.
2. Ebben a JSON-fájlban foglaljon bele egy schedule tulajdonságot egy Cron-kifejezés használatával: 
    ```json
    { "schedule": "{second}
    {minute} {hour} {day}
    {month} {day of the week}" }
    ```

Az ütemezett WebJobs-feladatokkal kapcsolatos további információkért lásd: Ütemezett WebJobs-feladat létrehozása [Cron-kifejezés használatával.](webjobs-create.md#CreateScheduledCRON)

## <a name="how-do-i-perform-penetration-testing-for-my-app-service-app"></a>Hogyan a behatolási teszteket a App Service alkalmazáshoz?

Behatolási teszt végrehajtásához küldjön [el egy kérést.](https://portal.msrc.microsoft.com/engage/pentest)

## <a name="how-do-i-configure-a-custom-domain-name-for-an-app-service-web-app-that-uses-traffic-manager"></a>Hogyan egyéni tartománynevet konfigurál egy olyan App Service-webalkalmazáshoz, amely Traffic Manager?

Ha meg szeretne ismerkedni azzal, hogyan használható egyéni tartománynév egy App Service-alkalmazással, amely Azure Traffic Manager-t használ a terheléselosztáshoz, tekintse meg az Egyéni tartománynév konfigurálása [Azure-webalkalmazáshoz](configure-domain-traffic-manager.md)a Traffic Manager.

## <a name="my-app-service-certificate-is-flagged-for-fraud-how-do-i-resolve-this"></a>Az App Service-tanúsítványom csalás miatt van megjelölve. Hogyan oldhatom meg ezt?

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A tanúsítvány megvásárlásakor a App Service alábbi üzenet jelenik meg:

"A tanúsítványt lehetséges csalás miatt megjelölték. A kérés jelenleg felülvizsgálat alatt áll. Ha a tanúsítvány 24 órán belül nem használható, lépjen kapcsolatba a Azure ügyfélszolgálata."

Ahogy az üzenet is jelzi, a csalások ellenőrzési folyamata akár 24 órát is igénybe vehet. Ez idő alatt az üzenet továbbra is megjelenik.

Ha a App Service tanúsítványa 24 óra után is ezt az üzenetet mutatja, futtassa a következő PowerShell-szkriptet. A szkript közvetlenül a [tanúsítványszolgáltatóhoz](https://www.godaddy.com/) lép kapcsolatba a probléma megoldása érdekében.

```powershell
Connect-AzAccount
Set-AzContext -SubscriptionId <subId>
$actionProperties = @{
    "Name"= "<Customer Email Address>"
    };
Invoke-AzResourceAction -ResourceGroupName "<App Service Certificate Resource Group Name>" -ResourceType Microsoft.CertificateRegistration/certificateOrders -ResourceName "<App Service Certificate Resource Name>" -Action resendRequestEmails -Parameters $actionProperties -ApiVersion 2015-08-01 -Force   
```

## <a name="how-do-authentication-and-authorization-work-in-app-service"></a>Hogyan működik a hitelesítés és az engedélyezés App Service?

A hitelesítéssel és engedélyezéssel kapcsolatos részletes dokumentációt a App Service a különféle azonosításszolgáltatói bejelentkezések dokumentációjában találhatja:
* [Azure Active Directory](configure-authentication-provider-aad.md)
* [Facebook](configure-authentication-provider-facebook.md)
* [Google](configure-authentication-provider-google.md)
* [Microsoft-fiók](configure-authentication-provider-microsoft.md)
* [Twitter](configure-authentication-provider-twitter.md)

## <a name="how-do-i-redirect-the-default-azurewebsitesnet-domain-to-my-azure-web-apps-custom-domain"></a>Hogyan az alapértelmezett *.azurewebsites.net tartományt az Azure-webalkalmazás egyéni tartományának?

Amikor új webhelyet hoz létre az Azure-Web Apps használatával, a rendszer egy *alapértelmezett*.azurewebsites.net tartománynevet rendel a webhelyhez. Ha egyéni állomásnevet ad a webhelyhez, és nem szeretné, hogy a felhasználók hozzáférjenek az alapértelmezett *.azurewebsites.net tartományhoz, átirányíthatja az alapértelmezett URL-címet. Ha meg szeretne tudni arról, hogyan irányíthatja át az összes forgalmat a webhely alapértelmezett tartományáról az egyéni tartományba, tekintse meg az Alapértelmezett tartomány átirányítása az Azure-webalkalmazások egyéni [tartományáracímű cikkeket.](https://zainrizvi.io/blog/block-default-azure-websites-domain/)

## <a name="how-do-i-determine-which-version-of-net-version-is-installed-in-app-service"></a>Hogyan,hogy a .NET melyik verziója van telepítve a App Service?

A kudu-konzolon Microsoft .NET a App Service a leggyorsabban megkeresi a szoftververziót. A Kudu-konzolt a portálról vagy a saját alkalmazásának URL-címével App Service el. Részletes útmutatásért [lásd: A telepített .NET-verzió meghatározása a App Service.](/archive/blogs/waws/how-to-determine-the-installed-net-version-in-azure-app-services)

## <a name="why-isnt-autoscale-working-as-expected"></a>Miért nem a várt módon működik az automatikus skálázás?

Ha az Azure AutoScale nem a várt módon skálázható le vagy nem skálázta fel a webalkalmazás-példányt, előfordulhat, hogy egy olyan forgatókönyvbe fog belefutni, amelyben szándékosan úgy döntünk, hogy nem méretezünk, hogy elkerülhető legyen egy végtelen hurok a "lobbanás" miatt. Ez általában akkor fordul elő, ha nincs megfelelő különbség a horizontális felskálás és a leskálás küszöbértéke között. Az "átméretezés" elkerüléséről és az automatikus skálázás egyéb ajánlott eljárásait az Automatikus skálázás ajánlott [eljárásokról olvashat.](../azure-monitor/autoscale/autoscale-best-practices.md#autoscale-best-practices)

## <a name="why-does-autoscale-sometimes-scale-only-partially"></a>Miért csak részlegesen skáláz az automatikus skálázás?

Az automatikus méretezés akkor aktiválódik, ha a metrikák túllépik az előre konfigurált határokat. Előfordulhat, hogy azt veszi észre, hogy a kapacitás csak részben van feltöltve ahhoz képest, amit várt. Ez akkor fordulhat elő, ha a kívánt példányok száma nem érhető el. Ebben a forgatókönyvben az automatikus skálázás részlegesen kitölti a rendelkezésre álló példányszámot. Az automatikus skálázás ezután futtatja az újraegyensúlyozási logikát a kapacitás növelése érdekében. Lefoglalja a fennmaradó példányokat. Vegye figyelembe, hogy ez eltarthat néhány percig.

Ha néhány perc után nem látja a várt példányszámot, annak az lehet az oka, hogy a részleges újratöltés elég volt ahhoz, hogy a metrikákat a határvonalakon belülre hozza. Az is lehetséges, hogy az automatikus skálázás le lett skálázva, mert elérte az alacsonyabb metrikahatárt.

Ha ezen feltételek egyike sem áll fenn, és a probléma továbbra is fennáll, küldjön támogatási kérést.

## <a name="how-do-i-turn-on-http-compression-for-my-content"></a>Hogyan http-tömörítést a tartalomhoz?

A statikus és dinamikus tartalomtípusok tömörítésének bekapcsolához adja hozzá a következő kódot az alkalmazásszintű web.config fájlhoz:

```xml
<system.webServer>
    <urlCompression doStaticCompression="true" doDynamicCompression="true" />
</system.webServer>
```

A tömöríteni kívánt dinamikus és statikus MIME-típusokat is megadhatja. További információt egy egyszerű Azure-webhely [httpCompression-beállításaiban](https://social.msdn.microsoft.com/Forums/azure/890b6d25-f7dd-4272-8970-da7798bcf25d/httpcompression-settings-on-a-simple-azure-website?forum=windowsazurewebsitespreview)található fórumra adott válaszunkban talál.

## <a name="how-do-i-migrate-from-an-on-premises-environment-to-app-service"></a>Hogyan a helyszíni környezetből a App Service?

A windowsos és linuxos webkiszolgálók webhelyeinek áttelepítéséhez App Service a Azure App Service Migration Assistant. A migrálási eszköz szükség szerint webalkalmazásokat és adatbázisokat hoz létre az Azure-ban, majd közzéteszi a tartalmat. További információ: [Azure App Service Migration Assistant.](https://appmigration.microsoft.com/)

## <a name="why-is-my-certificate-issued-for-11-months-and-not-for-a-full-year"></a>Miért van a tanúsítványom 11 hónapra kiállítva, és nem egy teljes évre?

Jelenleg a 2020. szeptember 1. után kiadott összes tanúsítvány maximális időtartama 397 nap. A 2020. szeptember 1. előtt kiadott tanúsítványok legfeljebb 825 napig érvényesek, mielőtt szükségessé válna a megújításuk, a kulcsuk újbóli megadása stb. Ez a változás érinti a 2020. szeptember 1. után megújított tanúsítványokat, így a felhasználók azzal szembesülhetnek, hogy a megújított tanúsítványaik rövidebb ideig érvényesek.
A GoDaddy bevezetett egy olyan előfizetési szolgáltatást, amely megfelel az új követelményeknek, miközben a meglévő ügyfél-tanúsítványokat is elfogadja. Az újonnan kiállított tanúsítványok lejárta előtt 30 nappal a szolgáltatás automatikusan kiad egy második tanúsítványt, amely meghosszabbítja az érvényességi időtartamot az eredeti lejárati dátumig. Az App Service együttműködik a GoDaddyvel ezen változást illetően, hogy ügyfeleink mindenképp kihasználhassák a tanúsítványuk teljes időtartamát.
