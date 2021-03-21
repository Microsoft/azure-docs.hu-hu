---
title: Prémium csomag Azure Functions
description: Részletek és konfigurációs beállítások (VNet, nem hideg indítás, korlátlan végrehajtási időtartam) a Azure Functions Premium csomaghoz.
author: jeffhollan
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: jehollan
ms.custom:
- references_regions
- fasttrack-edit
- devx-track-azurecli
ms.openlocfilehash: 3061329ad9dcb368dab586acc2146e6fb4e23028
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101708713"
---
# <a name="azure-functions-premium-plan"></a>Prémium csomag Azure Functions

A Azure Functions Prémium csomag (más néven a rugalmas Prémium csomag) egy üzemeltetési lehetőség a Function apps számára. Az egyéb üzemeltetési csomagokra vonatkozó további lehetőségekért tekintse meg a [üzemeltetési tervről szóló cikket](functions-scale.md).

A prémium szintű csomag üzemeltetése a következő előnyöket nyújtja a függvények számára:

* Kerülje a megfázást állandóan meleg példányokkal
* Virtuális hálózati kapcsolat.
* Korlátlan végrehajtás időtartama, 60 perc garantált.
* Prémium példányok méretei: egy mag, két mag és négy fő példány.
* Több kiszámítható díjszabás a fogyasztási tervhez képest.
* Nagy sűrűségű alkalmazások foglalása több Function-alkalmazással rendelkező csomagokhoz.

A Prémium csomag használatakor a rendszer a Azure Functions gazdagép példányait hozzáadja és eltávolítja a bejövő események száma alapján, ugyanúgy, mint a használati [terv](consumption-plan.md). Több Function apps is telepíthető ugyanarra a prémium csomagra, és a csomag lehetővé teszi a számítási példány méretének, az alapcsomag méretének és a maximális méretnek a konfigurálását. 

## <a name="billing"></a>Számlázás

A Prémium csomag számlázása a különböző példányok által lefoglalt fő másodpercek és memória száma alapján történik. Ez a számlázás eltér a használati tervtől, amely a végrehajtás és a felhasznált memória alapján történik. A Prémium csomag esetében nincs végrehajtási díj. Legalább egy példányt le kell osztani a csomag minden időpontjában. Ez a számlázás az aktív csomagokra vonatkozó minimális havi költséget eredményezi, függetlenül attól, hogy a függvény aktív vagy üresjáratban van-e. Vegye figyelembe, hogy a prémium szintű csomag összes funkciója a lefoglalt példányok megosztására szolgál. További információkért tekintse meg a [Azure functions díjszabását ismertető oldalt](https://azure.microsoft.com/pricing/details/functions/).

## <a name="create-a-premium-plan"></a>Prémium csomag létrehozása

Ha a Azure Portalban hoz létre egy Function alkalmazást, a használati terv az alapértelmezett. Prémium szintű csomagban futó Function alkalmazás létrehozásához explicit módon létre kell hoznia egy App Service tervet az egyik _rugalmas prémium_ SKU használatával. A rendszer ezután a létrehozott Function alkalmazást futtatja ebben a csomagban. A Azure Portal használatával egyszerűen létrehozható a Prémium csomag és a Function alkalmazás is. Egyszerre több Function alkalmazást is futtathat ugyanabban a prémium csomagban, de a leggyakrabban ugyanazon az operációs rendszeren (Windows vagy Linux) futnak. 

A következő cikkek bemutatják, hogyan hozhat létre egy prémium csomaggal rendelkező Function alkalmazást programozott módon vagy a Azure Portal:

+ [Azure Portal](create-premium-plan-function-app-portal.md)
+ [Azure CLI](scripts/functions-cli-create-premium-plan.md)
+ [Azure Resource Manager-sablon](functions-infrastructure-as-code.md#deploy-on-premium-plan)

## <a name="eliminate-cold-starts"></a>A hidegindító eltávolítása

Ha a használati tervben nem fordulnak elő események vagy végrehajtások, akkor az alkalmazás nulla példányra méretezhető. Amikor új események érkeznek, egy új példánynak, amelyen az alkalmazás fut, speciálisnak kell lennie. Az új példányok specializálása az alkalmazástól függően hosszabb időt is igénybe vehet. Ezt a további késleltetést az első hívásnál gyakran az App _Cold Start_ metódusnak nevezik.

A prémium szintű csomag két olyan funkciót kínál, amelyek együttműködve hatékonyan kiszűrik a feladatait: _mindig kész példányok_ és _előre bemelegítő példányok_. 

### <a name="always-ready-instances"></a>Mindig kész példányok

A prémium csomaggal az alkalmazás mindig egy adott számú példányon áll készen. A mindig kész példányok maximális száma 20. Amikor az események elkezdik elindítani az alkalmazást, először a mindig kész példányokhoz lesznek irányítva. Ahogy a függvény aktívvá válik, a további példányok pufferként lesznek bemelegítve. Ez a puffer megakadályozza, hogy a méretezés során új példányok ne legyenek hidegen. Ezeket a pufferelt példányokat [előre bemelegítő példányoknak](#pre-warmed-instances)nevezzük. Az Always Ready-példányok és az előre bemelegítő puffer együttes használatával az alkalmazás hatékonyan kiszűrheti a hidegindító-használatot.

> [!NOTE]
> Minden prémium csomagnak legalább egy aktív (számlázott) példánya van.

# <a name="portal"></a>[Portál](#tab/portal)

A Azure Portal mindig kész példányok számát beállíthatja úgy, hogy kijelöli a **függvényalkalmazás**, majd a **platform szolgáltatásai** lapra kattint, majd kiválasztja a **kiskálázási** lehetőségeket. Az alkalmazás szerkesztése ablakban a mindig kész példányok az adott alkalmazásra vonatkoznak.

![Rugalmas méretezési beállítások](./media/functions-premium-plan/scale-out.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azurecli)

Az Azure CLI-vel is konfigurálhatja az alkalmazások mindig kész példányait.

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.minimumElasticInstanceCount=<desired_always_ready_count> --resource-type Microsoft.Web/sites
```
---

### <a name="pre-warmed-instances"></a>Előre bemelegítő példányok

Az előre bemelegítő példányok a méretezési és aktiválási események során pufferként vannak bemelegítve. Az előre bemelegített példányok továbbra is a pufferig maradnak, amíg el nem éri a maximális kibővíthető korlátot. Az alapértelmezett előre bemelegítő példányszám értéke 1, és a legtöbb forgatókönyv esetén az értéknek 1-nek kell maradnia.

Ha egy alkalmazás hosszú bemelegítőt használ (például egy egyéni tároló képét), akkor előfordulhat, hogy ezt a puffert kell megemelnie. Egy előre bemelegítő példány csak akkor válik aktívvá, ha az összes aktív példány megfelelően használatban van.

Gondolja át ezt a példát arra, hogy a mindig kész példányok és az előre bemelegítő példányok hogyan működnek együtt. A Premium Function alkalmazáshoz öt mindig kész példány van konfigurálva, és az alapértelmezett egy előre bemelegítő példány. Ha az alkalmazás üresjáratban van, és nincsenek aktiválva események, az alkalmazás üzembe helyezése és futtatása öt példányban történik. Jelenleg nem számítunk fel olyan előre bemelegítő példányt, amelyet a mindig kész példányok nem használnak, és a rendszer nem foglal le előre bemelegítő példányt.

Amint az első trigger bekövetkezik, az öt mindig kész példány aktívvá válik, és egy előre bemelegítő példány van lefoglalva. Az alkalmazás már hat kiosztott példányon fut: az öt most aktív, mindig kész példány, valamint a hatodik előre bemelegítő és inaktív puffer. Ha a végrehajtások aránya továbbra is növekszik, az öt aktív példány használatban lesz. Amikor a platform úgy dönt, hogy az öt példányon túli skálázást hajt végre, az előre bemelegített példányba kerül. Ebben az esetben már hat aktív példány van, és a rendszer azonnal kiépíti a hetedik példányt, és kitölti az előre bemelegítő puffert. Ez a skálázási és előmelegítési folyamat addig folytatódik, amíg el nem éri az alkalmazáshoz tartozó példányok maximális számát. A rendszer nem rendelkezik előre bemelegedett vagy aktivált példányokkal a maximumon túl.

Az Azure CLI használatával módosíthatja az alkalmazások előre bemelegítő példányainak számát.

```azurecli-interactive
az resource update -g <resource_group> -n <function_app_name>/config/web --set properties.preWarmedInstanceCount=<desired_prewarmed_count> --resource-type Microsoft.Web/sites
```

### <a name="maximum-function-app-instances"></a>Function app-példányok maximális száma

A [Példányszám maximális száma](#plan-and-sku-settings)mellett az alkalmazások maximális számát is beállíthatja. Az alkalmazás maximálisan konfigurálható az [alkalmazás méretezési korlátja](./event-driven-scaling.md#limit-scale-out)alapján.

## <a name="private-network-connectivity"></a>Magánhálózati kapcsolat

A prémium csomagokra telepített functions-alkalmazások kihasználhatják a [Web Apps VNet-integrációját](../app-service/web-sites-integrate-with-vnet.md). Ha be van állítva, az alkalmazás képes kommunikálni a VNet lévő erőforrásokkal, vagy szolgáltatási végpontokon keresztül. Az alkalmazásban az IP-korlátozások is elérhetők a bejövő forgalom korlátozására.

Amikor prémium szintű csomagban rendel hozzá egy alhálózatot a Function alkalmazáshoz, szüksége van egy alhálózatra, amely minden lehetséges példányhoz elegendő IP-címmel rendelkezik. Legalább 100 rendelkezésre álló címmel rendelkező IP-blokkra van szükség.

További információ: [a Function app integrálása VNet](functions-create-vnet.md).

## <a name="rapid-elastic-scale"></a>Gyors rugalmas skála

Az alkalmazáshoz további számítási példányok is automatikusan hozzáadódnak a használati tervvel megegyező gyors skálázási logikával. Az azonos App Service csomagbeli alkalmazások egymástól függetlenül méretezhetők, egy adott alkalmazás igényeitől függően. Ugyanakkor a functions-alkalmazások ugyanabban a App Service csomagban megosztják a virtuális gépek erőforrásait a költségek csökkentése érdekében, ha lehetséges. A virtuális géphez társított alkalmazások száma az egyes alkalmazások lábnyomával és a virtuális gép méretétől függ.

További információ a skálázás működéséről: [eseményvezérelt skálázás Azure Functionsban](event-driven-scaling.md).

## <a name="longer-run-duration"></a>Hosszabb Futtatás időtartama

A Azure Functions a használati terv egyetlen végrehajtás esetén 10 percre van korlátozva. A Prémium csomag esetében a futtatási időtartam alapértelmezett értéke 30 perc, hogy megakadályozza a Runaway végrehajtást. Azonban [módosíthatja a host.jsa konfigurációban](./functions-host-json.md#functiontimeout) , hogy a prémium szintű csomag alkalmazásaihoz ne legyen kötve. Ha egy korlát nélküli időtartamra van beállítva, a Function alkalmazás legalább 60 percen belül futtatható. 

## <a name="plan-and-sku-settings"></a>Csomag-és SKU-beállítások

A terv létrehozásakor kétféle séma-beállítás létezik: a példányok minimális száma (vagy a csomag mérete) és a maximális burst korlát.

Ha az alkalmazás a mindig kész példányokon túli példányokat igényel, akkor továbbra is kibővíthető, amíg a példányok száma eléri a maximális burst korlátot. A csomag mérete meghaladja a példányszámot, és a rendszer csak akkor futtatja és rendeli hozzájuk az Ön számára, ha azok a tervek szerint vannak kiosztva. A platform a legjobb megoldás, ha az alkalmazást a meghatározott maximális korlátig szeretné méretezni.

A csomag méretének és Azure Portal maximális értékének konfigurálásához válassza ki a csomag **kibővítő** lehetőségeit vagy az adott tervhez üzembe helyezett Function alkalmazást (a **platform szolgáltatásai** alatt).

Az Azure CLI maximális burst korlátját is megnövelheti:

```azurecli-interactive
az functionapp plan update -g <resource_group> -n <premium_plan_name> --max-burst <desired_max_burst>
```

Minden csomag minimuma legalább egy példány lesz. A példányok tényleges minimális száma automatikusan konfigurálva lesz az Ön számára a csomagban lévő alkalmazások által kért mindig kész példányok alapján. Például ha az alkalmazás egy öt mindig kész példányt kér, és a B alkalmazás két mindig kész példányt kér ugyanabban a csomagban, a minimális terv mérete öt lesz. Az A alkalmazás mind az 5-öt futtatja, a B alkalmazás pedig csak 2 lesz.

> [!IMPORTANT]
> A rendszer minden egyes, a példányok minimális száma alatt lefoglalt példány díját terheli, függetlenül attól, hogy a függvények végrehajtása történik-e.

A legtöbb esetben ez az autoszámított minimum elegendő. A minimálisan szükséges skálázás azonban a lehető leghatékonyabban történik. Ez azonban nem valószínű, hogy egy adott időkorláton késleltethető, ha további példányok nem érhetők el. Ha a minimumnál magasabb értéket állít be, akkor a Kibővítés előtt le kell foglalni a példányokat.

Az Azure CLI használatával megnövelhető egy csomag kiszámított minimuma.

```azurecli-interactive
az functionapp plan update -g <resource_group> -n <premium_plan_name> --min-instances <desired_min_instances>
```

### <a name="available-instance-skus"></a>Rendelkezésre álló példányok SKU-ban

A csomag létrehozásakor vagy skálázásakor három példány mérete közül választhat. Az egyes példányok kiosztása után a rendszer a másodpercenként kiosztott magok és memóriák teljes számát számlázza. Az alkalmazás igény szerint automatikusan több példányra is kibővíthető.

|Termékváltozat|Cores|Memória|Tárolás|
|--|--|--|--|
|EP1|1|3.5 GB|250GB|
|EP2|2|7GB|250GB|
|EP3|4|14 GB|250GB|

### <a name="memory-usage-considerations"></a>Memóriahasználat szempontjai

Ha egy olyan gépen fut, amely több memóriát használ, nem mindig jelenti azt, hogy a Function alkalmazás az összes rendelkezésre álló memóriát használja.

A JavaScript-függvények alkalmazásait például a Node.js alapértelmezett memória-korlátja korlátozza. A rögzített memória korlátjának növeléséhez adja hozzá az alkalmazás `languageWorkers:node:arguments` értékét a következő értékkel: `--max-old-space-size=<max memory in MB>` .

A 4 GB-nál több memóriával rendelkező csomagok esetében ügyeljen arra, hogy a bitszáma platform beállítása az `64 Bit` [általános beállítások](../app-service/configure-common.md#configure-general-settings)területen legyen.

## <a name="region-max-scale-out"></a>Régió maximális felskálázása

Az alábbiakban láthatók az egyes régiók és operációs rendszerek konfigurációjának egyetlen csomagjának jelenleg támogatott maximális Felskálázási értékei. A növekedés igényléséhez nyisson meg egy támogatási jegyet.

Tekintse meg a függvények teljes regionális elérhetőségét az [Azure webhelyén](https://azure.microsoft.com/global-infrastructure/services/?products=functions).

|Region| Windows | Linux |
|--| -- | -- |
|Ausztrália középső régiója| 100 | Nem érhető el |
|Ausztrália 2. középső régiója| 100 | Nem érhető el |
|Kelet-Ausztrália| 100 | 20 |
|Délkelet-Ausztrália | 100 | 20 |
|Dél-Brazília| 100 | 20 |
|Közép-Kanada| 100 | 20 |
|Az USA középső régiója| 100 | 20 |
|Kelet-Kína 2| 100 | 20 |
|Észak-Kína 2| 100 | 20 |
|Kelet-Ázsia| 100 | 20 |
|USA keleti régiója | 100 | 20 |
|USA 2. keleti régiója| 100 | 20 |
|Közép-Franciaország| 100 | 20 |
|Középnyugat-Németország| 100 | Nem érhető el |
|Kelet-Japán| 100 | 20 |
|Nyugat-Japán| 100 | 20 |
|Dél-Korea középső régiója| 100 | 20 |
|Dél-Korea déli régiója| Nem érhető el | 20 |
|USA északi középső régiója| 100 | 20 |
|Észak-Európa| 100 | 20 |
|Kelet-Norvégia| 100 | 20 |
|USA déli középső régiója| 100 | 20 |
|Dél-India | 100 | Nem érhető el |
|Délkelet-Ázsia| 100 | 20 |
|Észak-Svájc| 100 | Nem érhető el |
|Nyugat-Svájc| 100 | Nem érhető el |
|Az Egyesült Királyság déli régiója| 100 | 20 |
|Az Egyesült Királyság nyugati régiója| 100 | 20 |
|USGov Arizona| 100 | 20 |
|USGov Virginia| 100 | 20 |
|USNat keleti régiója| 100 | Nem érhető el |
|USNat nyugati régiója| 100 | Nem érhető el |
|Nyugat-Európa| 100 | 20 |
|Nyugat-India| 100 | 20 |
|USA nyugati középső régiója| 100 | 20 |
|USA nyugati régiója| 100 | 20 |
|USA 2. nyugati régiója| 100 | 20 |

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Az Azure Functions üzemeltetési lehetőségeinek megismerése](functions-scale.md)