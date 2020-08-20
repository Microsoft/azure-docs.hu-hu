---
title: App Service-csomagok
description: Megtudhatja, hogy App Service csomagok hogyan működnek a Azure App Serviceban, hogyan történik a számlázás az ügyfél számára, és Hogyan méretezhetők az igényeinek megfelelően.
keywords: App Service, Azure app Service, skálázás, skálázható, méretezhetőség, app Service-csomag, app Service-díj
ms.assetid: dea3f41e-cf35-481b-a6bc-33d7fc9d01b1
ms.topic: article
ms.date: 08/12/2020
ms.custom: seodec18
ms.openlocfilehash: 3b339b359ddbaf105b95bdf5d94ee0f66f339a82
ms.sourcegitcommit: cd0a1ae644b95dbd3aac4be295eb4ef811be9aaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88611449"
---
# <a name="azure-app-service-plan-overview"></a>Az Azure App Service-csomagok áttekintése

App Service (Web Apps, API Apps vagy Mobile Apps) esetében az alkalmazás mindig egy _app Service_-csomagban fut. Emellett a [Azure functions](../azure-functions/functions-scale.md#app-service-plan) egy _app Service_-csomagban is futtathatja a parancsot. Az App Service-csomagok határozzák meg a futtatni kívánt webalkalmazások számítási erőforrásait. Ezek a számítási erőforrások hasonlóak a [_kiszolgálófarm_](https://wikipedia.org/wiki/Server_farm) számára a hagyományos webes üzemeltetésben. Egy vagy több alkalmazás úgy konfigurálható, hogy ugyanazon a számítási erőforrásokon (vagy ugyanabban a App Service tervben) fusson.

Ha egy App Service tervet hoz létre egy bizonyos régióban (például Nyugat-Európa), akkor az adott régióhoz tartozó számítási erőforrások készlete lesz létrehozva. Minden olyan alkalmazás, amelyet ebbe a App Service csomagba helyezett, a App Service-csomag által meghatározott számítási erőforrásokon futtathatja. Az App Service-csomagok a következőket határozzák meg:

- Régió (USA nyugati régiója, USA keleti régiója, stb.)
- Virtuálisgép-példányok száma
- Virtuálisgép-példányok mérete (kicsi, közepes, nagy)
- Díjszabási szint (ingyenes, közös, alapszintű, standard, prémium, PremiumV2, elkülönített)

Egy App Service-csomag _díjszabási szintje_ határozza meg, hogy milyen app Service funkciókat kap, és mennyit fizet a csomagért. A tarifacsomagok a következő kategóriákba sorolhatók:

- **Megosztott számítás**: az **ingyenes** és a **közös**, a két alapszintű alkalmazás ugyanazon az Azure-beli virtuális gépen futtatja az alkalmazást, mint a többi app Service alkalmazást, beleértve a más ügyfelek alkalmazásait is. Ezek a csomagok CPU-kvótákat rendelnek mindegyik, a közös erőforrásokon futó alkalmazáshoz, és ezek az erőforrások nem skálázhatóak fel horizontálisan.
- **Dedikált számítás** **: az alapszintű**, **standard**, **prémium**és **PremiumV2** szinteken a dedikált Azure-beli virtuális gépeken futó alkalmazások futnak. A számítási erőforrások elosztása csak az ugyanabban az App Service-csomagban lévő alkalmazások között történik. Minél magasabb szintű a csomag, annál több virtuálisgép-példány áll rendelkezésére a horizontális felskálázáshoz.
- **Elkülönített**: Ez a szintű dedikált Azure-beli virtuális gépeket futtat dedikált Azure-beli virtuális hálózatokon. Hálózati elkülönítést biztosít az alkalmazások számára a számítási elkülönítésen. Ez a szint biztosítja a legnagyobb horizontális felskálázási lehetőségeket.

[!INCLUDE [app-service-dev-test-note](../../includes/app-service-dev-test-note.md)]

Az egyes szintek a App Service szolgáltatások egy adott részhalmazát is biztosítják. Ezek a szolgáltatások egyéni tartományokat és TLS/SSL-tanúsítványokat, automatikus skálázást, üzembe helyezési pontokat, biztonsági másolatokat, Traffic Manager integrációt és egyebeket tartalmaznak. Minél magasabb a szintet, annál több funkció érhető el. Ha szeretné megtudni, hogy mely funkciók támogatottak az egyes díjszabási szinten, tekintse meg a [app Service csomag részleteit](https://azure.microsoft.com/pricing/details/app-service/plans/).

<a name="new-pricing-tier-premiumv2"></a>

> [!NOTE]
> Az új **PremiumV2** -díjszabási csomag [Dv2 sorozatú virtuális gépeket](../virtual-machines/dv2-dsv2-series.md) biztosít, amelyek gyorsabb processzorokkal, SSD-tárolóval, valamint a **standard** szinthez képest kétszeres memória-mag aránnyal rendelkeznek. A **PremiumV2** a megnövelt példányszámot is támogatja, miközben továbbra is biztosítja a standard csomagban található összes speciális funkciót. A meglévő **prémium** szinten elérhető összes funkció a **PremiumV2**részét képezi.
>
> A többi dedikált platformhoz hasonlóan három virtuálisgép-méret érhető el ehhez a platformhoz:
>
> - Kicsi (egy CPU mag, 3,5 GiB memória) 
> - Közepes (két CPU-mag, 7 GiB memória) 
> - Nagyméretű (négy CPU-mag, 14 GiB memória)  
>
> A **PremiumV2** díjszabásával kapcsolatos információkért lásd: [app Service díjszabása](https://azure.microsoft.com/pricing/details/app-service/).
>
> Az új **PremiumV2** -díjszabási csomag megkezdéséhez lásd: PremiumV2-csomag [konfigurálása app Servicehoz](app-service-configure-premium-tier.md).

## <a name="how-does-my-app-run-and-scale"></a>Hogyan fut és méretezhető az alkalmazásom?

Az **ingyenes** és a **közös** szinten az alkalmazások CPU-percet kapnak egy megosztott virtuálisgép-példányon, és nem méretezhetők. Más szinteken az alkalmazások a következőképpen futnak és méretezhetők.

Amikor App Serviceban hoz létre alkalmazást, az egy App Service tervbe kerül. Az alkalmazás futtatásakor az a App Service csomagban konfigurált összes virtuálisgép-példányon fut. Ha több alkalmazás is ugyanabban a App Service csomagban van, akkor az összes azonos virtuálisgép-példányon osztoznak. Ha egy alkalmazáshoz több üzembe helyezési pont is tartozik, az összes üzembe helyezési pont ugyanazon a virtuálisgép-példányokon fut. Ha engedélyezi a diagnosztikai naplókat, biztonsági mentést hajt végre, vagy webjobs-feladatokat futtat, akkor ezek a virtuálisgép-példányokon a CPU-ciklusokat és a memóriát is használják.

Így a App Service terv a App Service alkalmazások méretezési egysége. Ha a csomag öt virtuálisgép-példány futtatására van konfigurálva, akkor a tervben szereplő összes alkalmazás mind az öt példányon fut. Ha a terv automatikus skálázásra van konfigurálva, akkor a csomagban lévő összes alkalmazás az automatikus skálázási beállítások alapján ki lesz bővítve.

Az alkalmazások horizontális felskálázásával kapcsolatos információkért tekintse meg a [példányok számának manuális vagy automatikus méretezését](../monitoring-and-diagnostics/insights-how-to-scale.md)ismertető témakört.

<a name="cost"></a>

## <a name="how-much-does-my-app-service-plan-cost"></a>Mennyibe kerül a App Service terv?

Ez a szakasz azt ismerteti, hogyan történik a App Service alkalmazások számlázása. A régióra vonatkozó díjszabással kapcsolatos részletes információkat a [app Service díjszabását](https://azure.microsoft.com/pricing/details/app-service/)ismertető témakörben talál.

Az **ingyenes** csomag kivételével az App Service-csomagok díjat számítunk fel az általa használt számítási erőforrásokért.

- A **megosztott** szinten az egyes alkalmazások CPU-percenként kapják meg a kvótát, így _minden alkalmazás_ díja a CPU-kvóta.
- A dedikált számítási rétegek (**Alapszintű**, **standard**, **prémium**, **PremiumV2**) esetében az App Service-csomag határozza meg, hogy hány virtuálisgép-példányra méretezhetők az alkalmazások, így a app Service csomag minden virtuálisgép- _példánya_ díjköteles. Ezek a virtuálisgép-példányok ugyanarra a számlára vonatkoznak, függetlenül attól, hogy hány alkalmazás fut rajtuk. A váratlan költségek elkerülése érdekében lásd: [app Service terv tisztítása](app-service-plan-manage.md#delete).
- Az **elkülönített** szinten a app Service Environment meghatározza az alkalmazásokat futtató elkülönített _munkavégzők számát, és minden feldolgozót_ felszámítunk. Emellett a App Service Environment futtatásához is van egy egyszerű Stamp-díj.

Nem számítunk fel díjat az Ön számára elérhető App Service szolgáltatások (egyéni tartományok, TLS/SSL-tanúsítványok, üzembe helyezési pontok, biztonsági másolatok stb.) használatára. A kivételek a következők:

- App Service-tartományok – ha az Azure-ban vásárol egyet, és minden évben megújítja.
- App Service tanúsítványok – ha az Azure-ban vásárol egyet, és minden évben megújítja.
- IP-alapú TLS-kapcsolatok – minden egyes IP-alapú TLS-kapcsolathoz óradíjat biztosítunk, de néhány **standard** szint vagy újabb rendszerű TLS-kapcsolat ingyenes. A SNI-alapú TLS-kapcsolatok ingyenesek.

> [!NOTE]
> Ha a App Service egy másik Azure-szolgáltatással integrálja, előfordulhat, hogy a többi szolgáltatás díjait is figyelembe kell vennie. Ha például az Azure Traffic Manager-t használja az alkalmazás földrajzilag való méretezéséhez, az Azure Traffic Manager a használat alapján is felszámítja a díjat. Az Azure-beli szolgáltatások szolgáltatásainak megbecsléséhez tekintse meg a [díjszabási számológépet](https://azure.microsoft.com/pricing/calculator/). 

Szeretné optimalizálni és menteni a Felhőbeli kiadásait?

[!INCLUDE [cost-management-horizontal](../../includes/cost-management-horizontal.md)]

## <a name="what-if-my-app-needs-more-capabilities-or-features"></a>Mi a teendő, ha az alkalmazásnak több funkcióra vagy szolgáltatásra van szüksége?

App Service-csomagját bármikor skálázhatja felfelé és lefelé is. Ez olyan egyszerű, mint a csomag díjszabási szintjének módosítása. Elsőre választhat egy alacsonyabb tarifájú csomagot is, amelyet később felskálázhat, amikor már további App Service-szolgáltatásokra van szüksége.

Megkezdheti például a webalkalmazás tesztelését egy **ingyenes** app Service csomaggal, és semmit sem kell fizetnie. Ha hozzá szeretné adni az [Egyéni DNS-nevet](app-service-web-tutorial-custom-domain.md) a webalkalmazáshoz, egyszerűen méretezheti a tervet a **megosztott** szinten. Később, amikor [TLS-kötést szeretne létrehozni](configure-ssl-bindings.md), az **alapszintű csomagra** méretezheti a tervet. Ha [átmeneti környezeteket](deploy-staging-slots.md)szeretne használni, vertikálisan méretezheti a **standard** szintet. Ha további magokra, memóriára vagy tárterületre van szüksége, akkor az azonos szinten lévő nagyobb virtuálisgép-méretre méretezhető.

Ugyanez fordítva is működik. Ha úgy érzi, hogy már nincs szüksége egy magasabb szintű kapacitásra vagy funkcióra, lekicsinyítheti az alacsonyabb szintet, ami pénzt takarít meg.

Az App Service terv méretezésével kapcsolatos információkért lásd: alkalmazás felskálázása [Az Azure-ban](manage-scale-up.md).

Ha az alkalmazás ugyanabban a App Service csomagban van, és más alkalmazásokkal is rendelkezik, érdemes lehet javítani az alkalmazás teljesítményét a számítási erőforrások elkülönítésével. Ezt úgy teheti meg, hogy áthelyezi az alkalmazást egy külön App Service tervbe. További információ: [alkalmazás áthelyezése másik app Service tervbe](app-service-plan-manage.md#move).

## <a name="should-i-put-an-app-in-a-new-plan-or-an-existing-plan"></a>Be kell helyezni egy alkalmazást egy új csomagba vagy egy meglévő csomagba?

Mivel a számítási erőforrásokért az App Service-csomag lefoglalása után kell fizetnie (lásd: Mennyibe [kerül a app Service tervem?](#cost)), így pénzt takaríthat meg, ha több alkalmazást helyez üzembe egy app Service tervben. Az alkalmazásokat továbbra is hozzáadhatja egy meglévő csomaghoz, ha a csomag elegendő erőforrással rendelkezik a terhelés kezeléséhez. Ne feledje azonban, hogy az azonos App Service tervben szereplő alkalmazások mindegyike ugyanazokat a számítási erőforrásokat használja. Annak megállapításához, hogy az új alkalmazás rendelkezik-e majd a szükséges számítási erőforrásokkal, ismernie kell a meglévő App Service-csomag kapacitását, és a hozzáadni kívánt alkalmazás várható terheltségét. Az App Service-csomag túlterhelése üzemkimaradást okozhat az új és a meglévő alkalmazások esetében is.

Az alkalmazást akkor érdemes egy új App Service-csomagba elkülönítenie, ha:

- Az alkalmazás erőforrás-igényes.
- Az alkalmazást a meglévő csomag többi alkalmazásából függetlenül szeretné méretezni.
- Az alkalmazásnak erőforrásra van szüksége egy másik földrajzi régióban.

Így lefoglalhatja az alkalmazás új készletét, és hatékonyabban vezérelheti alkalmazásait.

## <a name="manage-an-app-service-plan"></a>App Service terv kezelése

> [!div class="nextstepaction"]
> [App Service terv kezelése](app-service-plan-manage.md)
