---
title: Az automatikus skálázás használatának első lépései az Azure-ban
description: Ismerje meg, hogyan méretezheti az Azure-ban az erőforrás-webalkalmazást, a Cloud Service-t, a virtuális gépet vagy a virtuálisgép-méretezési készletet
ms.topic: conceptual
ms.date: 07/07/2017
ms.subservice: autoscale
ms.openlocfilehash: 3662f6007049a5531e11c193adf71e8f8442dcdb
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2020
ms.locfileid: "93377020"
---
# <a name="get-started-with-autoscale-in-azure"></a>Ismerkedés az Azure-beli autoskálázással
Ez a cikk azt ismerteti, hogyan állíthatja be az erőforráshoz tartozó autoskálázási beállításokat a Microsoft Azure Portalban.

Azure Monitor az autoscale csak [Virtual Machine Scale sets](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [Cloud Services](https://azure.microsoft.com/services/cloud-services/), [app Service-Web Apps](https://azure.microsoft.com/services/app-service/web/)és [API Management szolgáltatásokra](../../api-management/api-management-key-concepts.md)vonatkozik.

## <a name="discover-the-autoscale-settings-in-your-subscription"></a>Az előfizetéshez tartozó automatikus skálázási beállítások felderítése

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4u7ts]

Felderítheti az összes olyan erőforrást, amelyre az automatikus skálázás alkalmazható Azure Monitorban. Kövesse az alábbi lépéseket a lépésenkénti útmutatóhoz:

1. Nyissa meg a [Azure Portal.][1]
1. Kattintson a Azure Monitor ikonra a bal oldali ablaktáblán.
  ![Azure Monitor megnyitása][2]
1. Az **autoskálázás** lehetőségre kattintva megtekintheti az összes olyan erőforrást, amely esetében az autoskálázás alkalmazható, valamint az aktuális autoskálázási állapotot.
  ![Automatikus méretezés észlelése Azure Monitor][3]

A felső szűrő ablaktáblán a lista hatóköre alapján kiválaszthatja az erőforrásokat egy adott erőforráscsoport, egy adott erőforrástípus vagy egy adott erőforrás számára.

Az egyes erőforrásokhoz az aktuális példányszámot és az autoskálázási állapotot fogja megtalálni. Az autoskálázás állapota a következőket teheti:

- **Nincs konfigurálva** : ennél az erőforrásnál még nem engedélyezte az autoskálázást.
- **Engedélyezve** : engedélyezve van az erőforráshoz tartozó autoskálázás.
- **Letiltva** : letiltotta az erőforrás-méretezést.

## <a name="create-your-first-autoscale-setting"></a>Az első méretezési beállítás létrehozása

Most ugorjon végig egy egyszerű, lépésenkénti útmutatót, amellyel létrehozhatja az első autoskálázási beállítást.

1. Nyissa meg Azure Monitor az **autoscale** (méretezés) panelt, és válassza ki a méretezni kívánt erőforrást. (A következő lépések egy webalkalmazáshoz társított App Service tervet használnak. Az [első ASP.net-webalkalmazását 5 perc alatt létrehozhatja az Azure][4]-ban.)
1. Vegye figyelembe, hogy a példányok aktuális száma 1. Kattintson az **autoskálázás engedélyezése** lehetőségre.
  ![Méretezési beállítás az új webalkalmazáshoz][5]
1. Adja meg a skálázási beállítás nevét, majd kattintson **a szabály hozzáadása** elemre. Figyelje meg, hogy a méretezési szabály beállításai a jobb oldalon a helyi ablaktáblán nyílnak meg. Alapértelmezés szerint ez a beállítás a példányszám 1 értékre való skálázását adja meg, ha az erőforrás CPU-százaléka meghaladja a 70 százalékot. Hagyja meg az alapértelmezett értékeket, majd kattintson a **Hozzáadás** gombra.
  ![Méretezési beállítás létrehozása egy webalkalmazáshoz][6]
1. Most létrehozta az első skálázási szabályt. Vegye figyelembe, hogy az UX az ajánlott eljárásokat javasolja, és azt állítja be, hogy "a szabálynak legalább egy méretezése van." Ehhez tegye a következőket:

    a. Kattintson a **Szabály hozzáadása** elemre.

    b. Az **operátor** beállítása **kisebb, mint**.

    c. Állítsa a **küszöbértéket** **20** -ra.

    d. Állítsa be a **műveletet** úgy, hogy **csökkentse a darabszámot**.

   Most olyan méretezési beállítással kell rendelkeznie, amely a CPU-használat alapján kibővíti/méretezi a skálázást.
   ![Skálázás CPU alapján][8]
1. Kattintson a **Mentés** gombra.

Gratulálunk! Most sikeresen létrehozta az első méretezési beállítást a webalkalmazás CPU-használat alapján történő méretezéséhez.

> [!NOTE]
> Ugyanezek a lépések érvényesek a virtuálisgép-méretezési csoport vagy a Cloud Service-szerepkör megkezdésére.

## <a name="other-considerations"></a>További szempontok
### <a name="scale-based-on-a-schedule"></a>Méretezés ütemterv alapján
A CPU-alapú skálázáson kívül a hét adott napjain különbözőképpen is beállíthatja a méretezést.

1. Kattintson **a skálázási feltétel hozzáadása** elemre.
1. A skálázási mód beállítása és a szabályok megegyeznek az alapértelmezett feltétellel.
1. Válassza az **adott napok megismétlése** az ütemtervhez lehetőséget.
1. Válassza ki a napokat és a kezdő/záró időpontot, ha alkalmazni szeretné a méretezési feltételt.

![Feltétel skálázása ütemterv alapján][9]
### <a name="scale-differently-on-specific-dates"></a>Eltérő méretezés adott dátumokon
A PROCESSZORon alapuló skálázás mellett az adott dátumok esetében különbözőképpen is beállíthatja a méretezést.

1. Kattintson **a skálázási feltétel hozzáadása** elemre.
1. A skálázási mód beállítása és a szabályok megegyeznek az alapértelmezett feltétellel.
1. Válassza az ütemterv **kezdő/záró dátumának megadása** lehetőséget.
1. Válassza ki a kezdő/befejező dátumokat és a kezdő/záró időpontot a skálázási feltétel alkalmazásához.

![Feltétel skálázása dátumok alapján][10]

### <a name="view-the-scale-history-of-your-resource"></a>Az erőforrás skálázási előzményeinek megtekintése
Ha az erőforrás fel-vagy leskálázásra kerül, egy eseményt naplóznak a tevékenység naplójában. Az elmúlt 24 órában megtekintheti az erőforrás-méretezési előzményeket a **futtatási előzmények** lapra való váltással.

![Előzmények][11]

Ha meg szeretné tekinteni a teljes méretezési előzményeket (akár 90 napig), **kattintson ide a további részletek megtekintéséhez**. Megnyílik a tevékenység naplója, amely az erőforrás és a kategória esetében előre be van jelölve.

### <a name="view-the-scale-definition-of-your-resource"></a>Az erőforrás méretezési definíciójának megtekintése
Az autoscale egy Azure Resource Manager erőforrás. Megtekintheti a méretezési definíciót a JSON-ban a **JSON** lapra való váltással.

![Méretezési definíció][12]

Ha szükséges, közvetlenül a JSON-ben végezheti el a módosításokat. Ezek a módosítások a mentés után is megjelennek.

### <a name="disable-autoscale-and-manually-scale-your-instances"></a>Az autoskálázás letiltása és a példányok manuális méretezése
Előfordulhat, hogy a jelenlegi méretezési beállítás letiltására és az erőforrás manuális méretezésére van szükség.

Kattintson a felül lévő **autoskálázás letiltása** gombra.
![Az autoskálázás letiltása][13]

> [!NOTE]
> Ez a beállítás letiltja a konfigurációt. Az autoskálázás újbóli engedélyezése után azonban visszatérhet.

Most már beállíthatja a manuálisan méretezni kívánt példányok számát.

![Manuális skálázás beállítása][14]

Az **autoskálázás engedélyezése** és a **Mentés** lehetőségre kattintva bármikor visszatérhet az autoscale méretezéshez.

## <a name="route-traffic-to-healthy-instances-app-service"></a>Forgalom átirányítása kifogástalan állapotú példányokra (App Service)

Ha több példányra bővíti a méretezést, App Service végezhet állapot-ellenőrzéseket a példányokon, hogy csak az egészséges példányokra irányítsa a forgalmat. Ehhez nyissa meg a portált a App Service, majd a **figyelés** területen válassza az **állapot-ellenőrzés** lehetőséget. Válassza az **Engedélyezés** lehetőséget, és adjon meg egy érvényes URL-útvonalat az alkalmazáson, például: `/health` vagy `/api/health` . Kattintson a **Mentés** gombra.

A szolgáltatás ARM-sablonokkal való engedélyezéséhez állítsa az `healthcheckpath` erőforrás tulajdonságát `Microsoft.Web/sites` a hely állapot-ellenőrzési útvonalára, például: `"/api/health/"` . A funkció letiltásához állítsa vissza a tulajdonságot az üres karakterláncra `""` .

### <a name="health-check-path"></a>Állapot-ellenőrzési útvonal

Az elérési útnak egy percen belül kell válaszolnia a 200 és a 299 közötti állapotkódot (beleértve a szolgáltatást is). Ha az elérési út egy percen belül nem válaszol, vagy a tartományon kívüli állapotkódot ad vissza, akkor a példány "nem megfelelő" állapotnak minősül. A App Service nem követi a 302-es átirányítást az állapot-ellenőrzési útvonalon. Az állapot-ellenőrzési funkció a App Service hitelesítési és engedélyezési funkcióival integrálódik, a rendszer akkor is eléri a végpontot, ha ezek a Secuity funkciók engedélyezve vannak. Ha saját hitelesítési rendszerét használja, az állapot-ellenőrzési útvonalnak engedélyeznie kell a névtelen hozzáférést. Ha a hely csak HTTP **-t** engedélyez, a Healthcheck kérelmet a rendszer http-n keresztül küldi **el.**

Az állapot-ellenőrzési útvonalnak ellenőriznie kell az alkalmazás kritikus összetevőit. Ha például az alkalmazás egy adatbázistól és egy üzenetkezelő rendszertől függ, az állapot-ellenőrzési végpontnak csatlakoznia kell ezekhez az összetevőkhöz. Ha az alkalmazás nem tud csatlakozni egy kritikus összetevőhöz, az elérési útnak egy 500 szintű választ kell visszaadnia, amely azt jelzi, hogy az alkalmazás nem kifogástalan állapotú.

#### <a name="security"></a>Biztonsági őr 

A nagyvállalati fejlesztési csapatoknak gyakran kell megfelelniük a kitett API-k biztonsági követelményeinek. Az Healthcheck végpont biztonságossá tételéhez először olyan szolgáltatásokat kell használnia, mint például az [IP-korlátozások](../../app-service/app-service-ip-restrictions.md#adding-ip-address-rules), az [ügyféltanúsítványok](../../app-service/app-service-ip-restrictions.md#adding-ip-address-rules)vagy egy Virtual Network az alkalmazáshoz való hozzáférés korlátozásához. A Healthcheck végpontot úgy is biztonságossá teheti, hogy a `User-Agent` bejövő kérelemnek megfelel `ReadyForRequest/1.0` . A User-Agentt nem lehet meghamisítani, mert a kérést az előző biztonsági funkciók már biztosították.

### <a name="behavior"></a>Működés

Az állapot-ellenőrzési útvonal megadásakor App Service fogja pingelni az elérési utat az összes példányon. Ha a sikeres válasz kódja 5 pingelés után nem érkezik meg, akkor a példány "nem megfelelő" állapotnak minősül. A nem kifogástalan állapotú példányok kimaradnak a terheléselosztó forgása alól. Megadhatja, hogy a sikertelen pingelések száma az `WEBSITE_HEALTHCHECK_MAXPINGFAILURES` alkalmazás beállításával történjen. Ez az Alkalmazásbeállítások 2 és 10 közötti egész számra állítható be. Ha például ezt a értékre állítja `2` , a rendszer a példányokat két sikertelen pingelés után eltávolítja a terheléselosztó közül. Emellett, ha a vertikális felskálázást végzi, App Service az állapot-ellenőrzési útvonal pingelésével biztosítja, hogy az új példányok készen álljanak a kérelmekre a terheléselosztó hozzáadása előtt.

> [!NOTE]
> Ne feledje, hogy a terheléselosztó kizárása érdekében a App Service-tervet 2 vagy több példányra kell méretezni. Ha csak 1 példánya van, akkor nem lesz eltávolítva a terheléselosztó közül, még akkor sem, ha az állapota nem megfelelő. 

A fennmaradó kifogástalan állapotú példányok nagyobb terhelést tapasztalhatnak. A fennmaradó példányok túlnyomó számának elkerülése érdekében a példányok több mint fele ki lesz zárva. Ha például egy App Service csomag 4 példányra van kibővítve, és 3 nem kifogástalan állapotú, legfeljebb 2 lesz kizárva a terheléselosztó forgásból. A másik 2 példány (1 kifogástalan és 1 sérült) továbbra is fogadja a kéréseket. Abban a legrosszabb esetben, ha az összes példány állapota nem kifogástalan, a rendszer nem zárja ki az egyiket sem. Ha szeretné felülbírálni ezt a viselkedést, a `WEBSITE_HEALTHCHECK_MAXUNHEALTYWORKERPERCENT` és a közötti értékre állíthatja az alkalmazás beállítását `0` `100` . Ha magasabb értékre állítja ezt a beállítást, a rendszer eltávolítja a nem megfelelő állapotú példányokat (az alapértelmezett érték 50).

Ha egy példány nem kifogástalan állapotú egy órára, az új példánnyal lesz lecserélve. Legfeljebb egy példány óránként lesz lecserélve, és App Service-csomag esetében legfeljebb három példány naponta.

### <a name="monitoring"></a>Figyelés

Az alkalmazás állapot-ellenőrzési útvonalának megadása után Azure Monitor használatával figyelheti a webhely állapotát. A portál **állapot-ellenőrzési** paneljén kattintson a felső eszköztár **metrikái** elemére. Ekkor megnyílik egy új panel, amelyen megtekintheti a hely korábbi állapotának állapotát, és létrehozhat egy új riasztási szabályt. A helyek figyelésével kapcsolatos további információkért [tekintse meg a következő útmutatót: Azure monitor](../../app-service/web-sites-monitor.md).

## <a name="moving-autoscale-to-a-different-region"></a>Az autoskálázás áthelyezése egy másik régióba
Ez a szakasz azt ismerteti, hogyan helyezheti át az Azure-beli autoskálázást egy másik régióba ugyanazon előfizetés és erőforráscsoport alatt. Az autoskálázási beállítások áthelyezéséhez REST API használható.
### <a name="prerequisite"></a>Előfeltétel
1. Győződjön meg arról, hogy az előfizetés és az erőforráscsoport elérhető, és hogy a forrás-és a célhelyek adatai azonosak.
1. Győződjön meg arról, hogy az Azure-beli autoskálázás elérhető az [Azure-régióban, ahová át kíván térni](https://azure.microsoft.com/global-infrastructure/services/?products=monitor&regions=all).

### <a name="move"></a>Áthelyezés
A [REST API](https://docs.microsoft.com/rest/api/monitor/autoscalesettings/createorupdate) használatával hozzon létre egy autoskálázási beállítást az új környezetben. A célként megadott régióban létrehozott automatikusan skálázási beállítás a forrás régiójában lévő autoskálázási beállítás másolata lesz.

A forrás régióban az autoscale beállítással létrehozott [diagnosztikai beállítások](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostic-settings) nem helyezhetők át. A cél régióban újra létre kell hoznia a diagnosztikai beállításokat, miután befejeződött az autosales beállítások létrehozása. 

### <a name="learn-more-about-moving-resources-across-azure-regions"></a>További információ az erőforrások Azure-régiók közötti áthelyezéséről
Ha többet szeretne megtudni az erőforrások régiók közötti áthelyezéséről és az Azure-beli vész-helyreállításról, olvassa el az [erőforrások áthelyezése új erőforráscsoporthoz vagy előfizetésbe](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources) című témakört.

## <a name="next-steps"></a>Következő lépések
- [Műveletnapló-riasztás létrehozása az összes autoskálázási motor műveleteinek figyeléséhez az előfizetésen](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-alert)
- [Műveletnapló-riasztás létrehozása az összes sikertelen, az előfizetésen kívüli méretezési és kibővítő művelet figyeléséhez](https://github.com/Azure/azure-quickstart-templates/tree/master/monitor-autoscale-failed-alert)

<!--Reference-->
[1]:https://portal.azure.com
[2]: ./media/autoscale-get-started/azure-monitor-launch.png
[3]: ./media/autoscale-get-started/discover-autoscale-azure-monitor.png
[4]: ../../app-service/quickstart-dotnetcore.md
[5]: ./media/autoscale-get-started/scale-setting-new-web-app.png
[6]: ./media/autoscale-get-started/create-scale-setting-web-app.png
[7]: ./media/autoscale-get-started/scale-in-recommendation.png
[8]: ./media/autoscale-get-started/scale-based-on-cpu.png
[9]: ./media/autoscale-get-started/scale-condition-schedule.png
[10]: ./media/autoscale-get-started/scale-condition-dates.png
[11]: ./media/autoscale-get-started/scale-history.png
[12]: ./media/autoscale-get-started/scale-definition-json.png
[13]: ./media/autoscale-get-started/disable-autoscale.png
[14]: ./media/autoscale-get-started/set-manualscale.png
