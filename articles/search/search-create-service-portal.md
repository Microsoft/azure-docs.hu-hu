---
title: Keresési szolgáltatás létrehozása a portálon
titleSuffix: Azure Cognitive Search
description: Ebből a portálból megtudhatja, hogyan állíthat be egy Azure Cognitive Search-erőforrást a Azure Portalban. Válassza az erőforráscsoportok, a régiók, az SKU vagy az árképzési szintet.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 10/05/2020
ms.openlocfilehash: 3ccc5ab07416f65270a7bd720f1e0884027b1fe7
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/14/2020
ms.locfileid: "92057722"
---
# <a name="quickstart-create-an-azure-cognitive-search-service-in-the-portal"></a>Rövid útmutató: Azure Cognitive Search-szolgáltatások létrehozása a portálon

Az Azure Cognitive Search egy önálló erőforrás, amely a keresési élmény egyéni alkalmazásokba való csatlakoztatására szolgál. A Cognitive Search könnyedén integrálható más Azure-szolgáltatásokkal, a hálózati kiszolgálókon futó alkalmazásokkal vagy más felhőalapú platformokon futó szoftverekkel.

Ebből a cikkből megtudhatja, hogyan hozhat létre erőforrásokat a [Azure Portalban](https://portal.azure.com/).

[![Animált GIF](./media/search-create-service-portal/AnimatedGif-AzureSearch-small.gif)](./media/search-create-service-portal/AnimatedGif-AzureSearch.gif#lightbox)

Szívesebben használja a PowerShellt? Használja az Azure Resource Manager [szolgáltatássablonját](https://azure.microsoft.com/resources/templates/101-azure-search-create/). Az első lépésekkel kapcsolatos segítségért lásd: az [Azure Cognitive Search kezelése a PowerShell](search-manage-powershell.md)-lel.

## <a name="before-you-start"></a>Előkészületek

A szolgáltatás élettartamára a következő szolgáltatási tulajdonságok vannak rögzítve – a változtatások bármelyike új szolgáltatást igényel. Mivel ezek rögzítettek, vegye figyelembe a használati szempontokat az egyes tulajdonságok kitöltése során:

* A szolgáltatás neve az URL-végpont részévé válik (a hasznos szolgáltatások neveire vonatkozó[tippek áttekintése](#name-the-service) ).
* A szolgáltatási szinten [befolyásolja a számlázást](search-sku-tier.md) , és beállítja a kapacitás felső határát. Néhány funkció nem érhető el az ingyenes szinten.
* A szolgáltatási régió képes meghatározni bizonyos forgatókönyvek rendelkezésre állását. Ha [magas szintű biztonsági funkciókra](search-security-overview.md) vagy [AI](cognitive-search-concept-intro.md)-bővítésre van szüksége, az Azure Cognitive Search-t a többi szolgáltatással azonos régióba kell helyeznie, vagy a szóban forgó szolgáltatást biztosító régiókban. 

## <a name="subscribe-free-or-paid"></a>Feliratkozás (ingyenes vagy fizetős)

[Nyisson meg egy ingyenes Azure-fiókot](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F), és fordítsa az ingyenes krediteket a fizetős Azure-szolgáltatások kipróbálására. A kreditek felhasználása után megtarthatja a fiókot, és továbbra is használhatja az olyan ingyenes Azure-szolgáltatásokat, mint a Websites. A bankkártyáját semmilyen költség nem terheli, hacsak Ön kifejezetten nem módosítja beállításait ennek engedélyezéséhez.

Választhatja az [MSDN-előfizetői értékelemek aktiválását](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F) is. MSDN-előfizetéssel havonta kap fizetős Azure-szolgáltatásokra fordítható krediteket. 

## <a name="find-azure-cognitive-search"></a>Azure-Cognitive Search keresése

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. Kattintson a plusz jelre ("+ Create Resource") a bal felső sarokban.

1. A keresősáv segítségével keresse meg az "Azure Cognitive Search", vagy navigáljon az erőforráshoz a **web**  >  **Azure Cognitive Search**használatával.

:::image type="content" source="media/search-create-service-portal/find-search3.png" alt-text="Erőforrás létrehozása a portálon" border="false":::

## <a name="choose-a-subscription"></a>Előfizetés kiválasztása

Ha egynél több előfizetéssel rendelkezik, válasszon egyet a keresési szolgáltatáshoz. Ha a [kettős titkosítást](search-security-overview.md#double-encryption) vagy más olyan szolgáltatásokat valósít meg, amelyek a felügyelt szolgáltatásbeli identitástól függenek, válassza ki ugyanazt az előfizetést, mint a Azure Key Vault vagy más olyan szolgáltatások esetében, amelyekhez a felügyelt identitások vannak használatban.

## <a name="set-a-resource-group"></a>Erőforráscsoport beállítása

Az erőforráscsoportok olyan tárolók, amelyek az Azure-megoldáshoz kapcsolódó erőforrásokat tárolnak. A keresési szolgáltatáshoz szükséges. Emellett az erőforrások teljes körű felügyeletére is hasznos, beleértve a költségeket is. Egy erőforráscsoport állhat egy szolgáltatásból vagy több, együtt használt szolgáltatásból is. Ha például Azure Cognitive Searcht használ egy Azure Cosmos DB-adatbázis indexeléséhez, akkor az adott erőforráscsoport mindkét szolgáltatását felügyeleti célokra is elvégezheti. 

Ha egyetlen csoporthoz sem egyesít erőforrásokat, vagy ha a meglévő erőforráscsoportok a nem kapcsolódó megoldásokban használt erőforrásokkal vannak kitöltve, hozzon létre egy új erőforráscsoportot az Azure Cognitive Search-erőforráshoz. 

:::image type="content" source="media/search-create-service-portal/new-resource-group.png" alt-text="Erőforrás létrehozása a portálon" border="false":::

Idővel nyomon követheti az aktuális és a tervezett költségeket, vagy megtekintheti az egyes erőforrások díját. Az alábbi képernyőképen látható, hogy milyen költségadatok várhatóak, ha több erőforrást egyesít egy csoportba.

:::image type="content" source="media/search-create-service-portal/resource-group-cost-management.png" alt-text="Erőforrás létrehozása a portálon" border="false":::

> [!TIP]
> Az erőforráscsoportok leegyszerűsítik a tisztítást, mert egy csoport törlése törli a benne lévő összes szolgáltatást. Több szolgáltatást igénybe vevő prototípus-projektek végén könnyebb mindent törölni, ha az összeset egy erőforráscsoportban helyezi el.

## <a name="name-the-service"></a>A szolgáltatás neve

A példány részletei területen adja meg a szolgáltatás nevét az **URL** mezőben. A név annak az URL-végpontnak a része, amelyen az API-hívások ki vannak bocsátva: `https://your-service-name.search.windows.net` . Ha például azt szeretné, hogy a végpont legyen `https://myservice.search.windows.net` , a következőt kell megadnia: `myservice` .

A szolgáltatásnévre vonatkozó követelmények:

* Egyedinek kell lennie a search.windows.net névtérben
* Hosszának 2 és 60 karakter közöttinek kell lennie
* Kisbetűket, számokat vagy kötőjeleket ("-") kell használnia
* Ne használjon kötőjeleket ("-") az első 2 karakterben, vagy az utolsó egy karaktert.
* Bárhol nem használhat egymást követő kötőjeleket ("--")

> [!TIP]
> Ha úgy gondolja, hogy több szolgáltatást fog használni, javasoljuk, hogy a szolgáltatás neveként a régiót (vagy helyet) nevezze el elnevezési konvencióként. Az azonos régión belüli szolgáltatások díjmentesen cserélhetik az adatcserét, így ha az Azure Cognitive Search az USA nyugati régiójában van, és más szolgáltatásokkal is rendelkezik az USA nyugati régiójában, akkor egy olyan név is lehet, mint `mysearchservice-westus` a Properties (Tulajdonságok) oldalra, amikor az erőforrások egyesítésével vagy csatlakoztatásával dönt.

## <a name="choose-a-location"></a>Hely kiválasztása

Az Azure Cognitive Search a legtöbb régióban elérhető. A támogatott régiók listája a [díjszabási oldalon](https://azure.microsoft.com/pricing/details/search/)található.

> [!Note]
> A közép-India és az Észak-Egyesült Arab Emírségek jelenleg nem érhető el új szolgáltatásokhoz. A régiókban már meglévő szolgáltatások esetében korlátozás nélkül méretezhető, és a szolgáltatás teljes mértékben támogatott az adott régióban. A korlátozások ideiglenesek, és csak az új szolgáltatásokra korlátozódnak. Ezt a megjegyzést akkor távolítjuk el, ha a korlátozások már nem érvényesek.
>
> A kettős titkosítás csak bizonyos régiókban érhető el. További információ: [dupla titkosítás](search-security-overview.md#double-encryption).

### <a name="requirements"></a>Követelmények

 Ha AI-bővítést használ, hozza létre a keresési szolgáltatást ugyanabban a régióban, mint Cognitive Services. *Az Azure Cognitive Search és az ugyanazon régióban lévő Cognitive Services együttes elhelyezése az AI-bővítés követelménye*.

 Az üzletmenet-folytonossággal és a vész-helyreállítási (BCDR) követelményekkel rendelkező ügyfeleknek [regionális párokban](../best-practices-availability-paired-regions.md#azure-regional-pairs)kell létrehozniuk a szolgáltatásaikat. Ha például Észak-Amerika működik, az USA keleti régiója és az USA nyugati régiója, illetve az USA északi középső régiója és a Dél-Amerikai Egyesült Államok az egyes szolgáltatások esetében is megadható.

### <a name="recommendations"></a>Javaslatok

Ha több Azure-szolgáltatást használ, válasszon egy olyan régiót, amely az Ön adatait vagy az alkalmazásait is üzemelteti. Így a kimenő adatok sávszélességének csökkentése vagy érvénytelenítése (a kimenő adatokért nem számítunk fel díjat, ha a szolgáltatások ugyanabban a régióban találhatók).

## <a name="choose-a-pricing-tier-sku"></a>Válasszon árképzési szintet (SKU)

Az [Azure Cognitive Search jelenleg több díjszabási szinten is elérhető](https://azure.microsoft.com/pricing/details/search/): ingyenes, alapszintű vagy standard. Mindegyik szintet saját [kapacitás és korlátozások](search-limits-quotas-capacity.md) jellemzik. Útmutatást talál a [Tarifacsomag vagy SKU kiválasztása](search-sku-tier.md) című cikkben.

Az alapszintű és a standard az éles munkaterhelések leggyakoribb lehetőségei, de a legtöbb ügyfél az ingyenes szolgáltatással kezdődik. A szintek közötti fő különbségek a partíció mérete és sebessége, valamint a létrehozható objektumok számának korlátai.

Ne feledje, hogy a szolgáltatás létrehozása után nem módosítható az árképzési szintek. Ha magasabb vagy alacsonyabb szintű kapacitásra van szüksége, akkor újra létre kell hoznia a szolgáltatást.

## <a name="create-your-service"></a>A szolgáltatás létrehozása

Miután megadta a szükséges bemeneteket, lépjen tovább, és hozza létre a szolgáltatást. 

:::image type="content" source="media/search-create-service-portal/new-service3.png" alt-text="Erőforrás létrehozása a portálon" border="false":::

A szolgáltatás percek alatt üzembe helyezhető. Az Azure-értesítéseken keresztül nyomon követheti a folyamat előrehaladását. Érdemes lehet a szolgáltatást az irányítópultra rögzíteni, hogy később könnyebben hozzáférhessen.

:::image type="content" source="media/search-create-service-portal/monitor-notifications.png" alt-text="Erőforrás létrehozása a portálon" border="false":::

## <a name="get-a-key-and-url-endpoint"></a>Kulcs és URL-cím végpontjának beolvasása

Ha nem használja a portált, az új szolgáltatáshoz való programozott hozzáféréshez az URL-végpontot és egy hitelesítési API-kulcsot kell megadnia.

1. Az **Áttekintés** oldalon keresse meg és másolja ki az URL-végpontot az oldal jobb oldalán.

2. A **kulcsok** lapon másolja át a rendszergazdai kulcsok egyikét (ezek egyenértékűek). Felügyeleti API-kulcsok szükségesek a szolgáltatásban található objektumok létrehozásához, frissítéséhez és törléséhez. Ezzel szemben a lekérdezési kulcsok olvasási hozzáférést biztosítanak a tartalom indexeléséhez.

   :::image type="content" source="media/search-create-service-portal/get-url-key.png" alt-text="Erőforrás létrehozása a portálon" border="false":::

A portálon alapuló feladatokhoz nincs szükség végpontra és kulcsra. A portál már társítva van az Azure Cognitive Search-erőforráshoz rendszergazdai jogosultságokkal. A portál bemutatójának első lépései a gyors üzembe helyezési útmutató [: Azure Cognitive Search index létrehozása a portálon](search-get-started-portal.md).

## <a name="scale-your-service"></a>A szolgáltatás méretezése

A szolgáltatást annak üzembe helyezése után igényei szerint méretezheti. Ha az Azure Cognitive Search szolgáltatás standard csomagját választotta, akkor a szolgáltatás két dimenzióban méretezhető: replikák és partíciók. Ha az alapszintű csomagot választotta volna, akkor csak replikákat adhatna hozzá. Ingyenes szolgáltatás üzembe helyezése esetén a méretezés nem elérhető.

A ***partíciókkal*** a szolgáltatás több dokumentum tárolására és keresésére alkalmas.

A ***replikákkal*** a szolgáltatás több keresési kérelem kiszolgálására alkalmas.

Az erőforrások hozzáadása növeli a havi költségeket. A [díjkalkulátor](https://azure.microsoft.com/pricing/calculator/) segíthet áttekinteni az erőforrások hozzáadásának számlázási vonatkozásait. Vegye figyelembe, hogy az erőforrások mennyisége a terheléshez igazítható. Például növelheti az erőforrások mennyiségét egy teljes körű első index létrehozásához, majd később csökkentheti azt a növekményes indexeléshez jobban megfelelő szintre.

> [!Important]
> A szolgáltatásnak [csak olvasható SLA esetén 2, írási/olvasási SLA esetén 3 replikával](https://azure.microsoft.com/support/legal/sla/search/v1_0/) kell rendelkeznie.

1. Nyissa meg a keresési szolgáltatás oldalát az Azure Portalon.
2. A bal oldali navigációs panelen válassza a **Beállítások**  >  **skála**lehetőséget.
3. A csúszka segítségével bármelyik típusú erőforrásokat hozzáadhatja.

:::image type="content" source="media/search-create-service-portal/settings-scale.png" alt-text="Erőforrás létrehozása a portálon" border="false":::

> [!Note]
> A partíción belüli tárolás és a gyorsabb növekedés magasabb szintű szinten történik. További információ: [kapacitás és korlátok](search-limits-quotas-capacity.md).

## <a name="when-to-add-a-second-service"></a>Mikor érdemes hozzáadni egy második szolgáltatást?

A legtöbb ügyfél csak egy olyan szolgáltatást használ, amely az [erőforrások megfelelő egyensúlyát](search-sku-tier.md)biztosító szinten van kiépítve. Egy szolgáltatásban több index is fenntartható [a választott csomag korlátozásainak megfelelő számban](search-capacity-planning.md), ha elkülönülnek egymástól. Az Azure Cognitive Searchban a kérelmek csak egyetlen indexre irányíthatók, ami minimalizálja az egyazon szolgáltatásban lévő más indexekről érkező véletlen vagy szándékos adatlekérdezés esélyét.

Bár az ügyfelek többsége csak egy szolgáltatást használ, szükség lehet a szolgáltatás redundanciájára, ha az üzemeltetési körülmények között az alábbiak szerepelnek:

+ [Üzletmenet-folytonosság és vész-helyreállítás (BCDR)](../best-practices-availability-paired-regions.md). Az Azure Cognitive Search áramszünet esetén nem biztosít azonnali feladatátvételt.

+ A [több-bérlős architektúrák](search-modeling-multitenant-saas-applications.md) időnként két vagy több szolgáltatást hívhatnak meg.

+ A globálisan telepített alkalmazások esetében előfordulhat, hogy az egyes földrajzi helyeken keresési szolgáltatások szükségesek a késés csökkentése érdekében.

> [!NOTE]
> Az Azure Cognitive Searchban nem különítheti el az indexelési és lekérdezési műveleteket; így soha nem hozhat létre több szolgáltatást az elkülönített munkaterhelésekhez. Az indexek lekérdezése mindig abban a szolgáltatásban történik, amelyben létre lettek hozva (nem lehet egy indexet egy szolgáltatásban létrehozni és egy másikba átmásolni).

A magas rendelkezésre álláshoz nincs szükség második szolgáltatásra. A lekérdezések magas rendelkezésre állása úgy érhető el, hogy egyazon szolgáltatáson belül 2 vagy több replikát használ. A replika frissítései szekvenciálisak, ami azt jelenti, hogy legalább egy működőképes a szolgáltatás frissítésekor. További információ az üzemidőről: [szolgáltatói szerződések](https://azure.microsoft.com/support/legal/sla/search/v1_0/).

## <a name="next-steps"></a>Következő lépések

A szolgáltatás kiépítés után folytathatja a portálon az első index létrehozását.

> [!div class="nextstepaction"]
> [Rövid útmutató: Azure Cognitive Search index létrehozása a portálon](search-get-started-portal.md)

Szeretné optimalizálni és menteni a Felhőbeli kiadásait?

> [!div class="nextstepaction"]
> [A költségek elemzésének megkezdése a Cost Management](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)