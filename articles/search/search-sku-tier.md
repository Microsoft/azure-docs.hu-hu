---
title: Árképzési szintek kiválasztása
titleSuffix: Azure Cognitive Search
description: 'További információ az Azure Cognitive Search díjszabási szintjeiről (vagy SKU-ról). A keresési szolgáltatás a következő szinteken állítható be: ingyenes, alapszintű és standard. A standard szint számos erőforrás-konfigurációban és kapacitási szinten érhető el.'
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/15/2021
ms.custom: contperf-fy21q2
ms.openlocfilehash: df218c2a4c066343ef571c6f24554ecaa806b639
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/09/2021
ms.locfileid: "99987883"
---
# <a name="choose-a-pricing-tier-for-azure-cognitive-search"></a>Válasszon árképzési szintet az Azure Cognitive Search

A [keresési szolgáltatás létrehozásának](search-create-service-portal.md) része a szolgáltatás élettartamára rögzített díjszabási csomag (vagy SKU) kiválasztását jelenti. Az árak – vagy a szolgáltatás futtatásának becsült havi díja – a szolgáltatás létrehozásakor a portál **Select díjszabási réteg** lapján jelennek meg. Ha ehelyett a PowerShell vagy az Azure CLI használatával végzi a telepítést, a szintet a **`-Sku`** paraméterrel kell megadni, és a [szolgáltatás díjszabását](https://azure.microsoft.com/pricing/details/search/) kell megadnia a becsült költségek megismeréséhez.

A kiválasztott rétegek a következőket határozzák meg:

+ Az indexek és a szolgáltatásban engedélyezett egyéb objektumok maximális száma
+ Partíciók mérete és sebessége (fizikai tárterület)
+ A számlázható kamatláb rögzített havi költségként, de növekményes költség is, ha kapacitást ad hozzá

Néhány esetben a kiválasztott szint határozza meg a [prémium szintű szolgáltatások](#premium-features)rendelkezésre állását.

> [!NOTE]
> Információt keres az Azure SKU-ról? Kezdje az [Azure díjszabásával](https://azure.microsoft.com/pricing/) , majd görgessen le a szolgáltatásokra vonatkozó díjszabási lapokra mutató hivatkozásokra.

## <a name="tier-descriptions"></a>Szintek leírása

A rétegek közé tartoznak az **ingyenes**, az **alapszintű**, a **standard** és a **Storage optimalizált** funkciók. A standard és a Storage optimalizált szolgáltatás számos konfigurációval és kapacitással érhető el. A következő képernyőkép a Azure Portal megjeleníti az elérhető csomagokat, mínusz a díjszabás (amely a Portálon és a [díjszabási oldalon](https://azure.microsoft.com/pricing/details/search/)található. 

:::image type="content" source="media/search-sku-tier/tiers.png" alt-text="Díjszabási szintek diagramja" border="true":::

Az **ingyenes** szolgáltatás korlátozott keresési szolgáltatást hoz létre kisebb projektekhez, például oktatóanyagokat és mintakód-mintákat futtat. Belsőleg a rendszererőforrások megosztása több előfizető között történik. Az ingyenes szolgáltatás nem méretezhető, és nem futtathat jelentős számítási feladatokat.

Az **alapszintű és a** **standard** a leggyakrabban használt számlázható rétegek, amelyek alapértelmezett értéke a **standard** , mivel nagyobb rugalmasságot biztosít a számítási feladatok skálázásához. A vezérlőhöz tartozó dedikált erőforrásokkal nagyobb projekteket helyezhet üzembe, optimalizálhatja a teljesítményt, és növelheti a kapacitást.

Egyes rétegek bizonyos típusú munkához lettek tervezve. Például a **Standard 3 nagy sűrűségű (S3 HD)** az S3 *üzemeltetési módja* , ahol a mögöttes hardver nagy számú kisebb indexre van optimalizálva, és bérlős forgatókönyvekhez készült. Az S3 HD azonos egységenkénti díjszabással rendelkezik, de a hardver nagy számú kisebb index esetén a gyors fájlokra van optimalizálva.

A **tárolásra optimalizált** csomagok nagyobb tárolókapacitást biztosítanak, mint a standard szinteken a TB-onként alacsonyabb áron. Az elsődleges kompromisszum nagyobb lekérdezési késés, amelyet az adott alkalmazásra vonatkozó követelmények érvényesítéséhez kell érvényesíteni. Ha többet szeretne megtudni a rétegek teljesítményével kapcsolatos megfontolásokról, tekintse meg a [teljesítmény-és optimalizálási szempontokat](search-performance-optimization.md).

További információt a [díjszabási oldalon](https://azure.microsoft.com/pricing/details/search/), a [szolgáltatási korlátok az Azure Cognitive Search](search-limits-quotas-capacity.md) cikkben, valamint a portál lapon talál, amikor egy szolgáltatást kiépít.

<a name="premium-features"></a>

## <a name="feature-availability-by-tier"></a>A szolgáltatás rendelkezésre állása rétegek szerint

A legtöbb funkció minden szinten elérhető, beleértve az ingyenes szintet is. Néhány esetben a kiválasztott rétegek hatással lesznek a funkciók megvalósítására. A következő táblázat a szolgáltatási szintjéhez kapcsolódó szolgáltatásokra vonatkozó korlátozásokat ismerteti.

| Szolgáltatás | Korlátozások |
|---------|-------------|
| [indexelők](search-indexer-overview.md) | Az indexelő nem érhető el az S3 HD-ben.  |
| [MI-bővítés](search-security-manage-encryption-keys.md) | Az ingyenes szinten fut, de nem ajánlott. |
| [A kimenő (indexelő) hozzáféréshez kezelt vagy megbízható identitások](search-howto-managed-identities-data-sources.md) | Az ingyenes szinten nem érhető el.|
| [Ügyfél által felügyelt titkosítási kulcsok](search-security-manage-encryption-keys.md) | Az ingyenes szinten nem érhető el. |
| [IP-tűzfal-hozzáférés](service-configure-firewall.md) | Az ingyenes szinten nem érhető el. |
| [Privát végpont (az Azure Private Linktel való integráció)](service-create-private-endpoint.md) | A keresési szolgáltatáshoz való bejövő kapcsolatok esetében az ingyenes szinten nem érhető el. Az indexelő által más Azure-erőforrásokhoz való kimenő kapcsolatok esetén az ingyenes vagy az S3 HD-on nem érhető el. Az szakértelmével-t használó indexelő esetében az ingyenes, az alapszintű, az S1 vagy az S3 HD nem érhető el.| 
| [Rendelkezésre állási zónák](search-performance-optimization.md) | Az ingyenes szint és az alapszintű csomag nem érhető el. |

Előfordulhat, hogy az erőforrás-igényes funkciók nem működnek megfelelően, hacsak nem biztosít elegendő kapacitást. Például az [AI-dúsítás](cognitive-search-concept-intro.md) olyan hosszan futó képességekkel rendelkezik, amelyek egy ingyenes szolgáltatás esetében időtúllépést mutatnak, kivéve, ha az adatkészlet kicsi.

## <a name="upper-limits"></a>Felső határértékek

A szintek határozzák meg magának a szolgáltatásnak a maximális tárolási kapacitását, valamint az indexek, az indexelő, az adatforrások, a szakértelmével és az Ön által létrehozható szinonimák maximális számát. Az összes korlát kitörése esetén tekintse meg a [szolgáltatási korlátok az Azure Cognitive Search-ban](search-limits-quotas-capacity.md)című témakört. 

## <a name="partition-size-and-speed"></a>Partíció mérete és sebessége

A szint díjszabása tartalmazza a 2 GB-os alapszintű, a tárterületre optimalizált (L2) csomagok esetében 2 TB-ig terjedő partíciós tárolás részleteit. A hardver egyéb jellemzői, például a műveletek sebessége, a késés és az átviteli sebesség nem kerül közzétételre, de az adott megoldási architektúrák számára tervezett rétegek olyan hardverekre épülnek, amelyek az adott forgatókönyvek támogatásához szükséges szolgáltatásokat használják. További információ a partíciókkal kapcsolatban: a [kapacitás becslése és kezelése](search-capacity-planning.md) és [a teljesítmény skálázása](search-performance-optimization.md).

## <a name="billing-rates"></a>Számlázási díjak

A csomagok különböző számlázási díjszabással rendelkeznek, és magasabb díjszabással rendelkeznek, amelyek drágább hardveren futnak, vagy drágább funkciókat biztosítanak. A kétrétegű számlázási díj az Azure Cognitive Search Azure- [díjszabási oldalain](https://azure.microsoft.com/pricing/details/search/) található.

A szolgáltatás létrehozása után a számlázási díj a szolgáltatás óránkénti futásának *fix díja* , valamint egy *növekményes díj* , ha nagyobb kapacitást szeretne hozzáadni.

A keresési szolgáltatások a számítási erőforrások *partíciók* (tárolók számára) formájában vannak kiosztva, és a *replikák* (a lekérdezési motor példányai). Kezdetben a szolgáltatás az egyikével jön létre, és a számlázási díj mindkét erőforrást magában foglalja. Ha azonban a kapacitást méretezi, a költségek felfelé vagy lefelé kerülnek a számlázható díj növekményei között.

Az alábbi példa egy illusztrációt tartalmaz. Tegyük fel, hogy az $100-as feltételezett számlázási díj havonta. Ha megtartja a keresési szolgáltatást egy partíció és egy replika kezdeti kapacitása alapján, akkor a $100 az a hónap végén várhatóan fizetendő. Ha azonban két további replikát ad hozzá a magas rendelkezésre állás eléréséhez, a havi számla $300-ra ($100 az első replika-partíciós párra, majd a két replikára $200-ra) nő.

Ez az árképzési modell a keresési szolgáltatás által használt szám *keresési egységekre* (su) vonatkozó számlázási díjszabás alkalmazásának koncepcióján alapul. Az összes szolgáltatás kezdetben egy SU-ben lett kiépítve, de a nagyobb számítási feladatok kezeléséhez partíciók vagy replikák hozzáadásával növelheti az SUs-t. További információkért lásd: a [keresési szolgáltatás költségeinek becslése](search-sku-manage-costs.md).

## <a name="next-steps"></a>Következő lépések

Az árképzési szintek kiválasztásának legjobb módja, ha a legkevésbé költséghatékony szintet választja, és lehetővé teszi, hogy a tapasztalatok és a tesztelés tájékoztassa a szolgáltatást, vagy hozzon létre egy újat egy magasabb szintű szinten. A következő lépésekhez javasoljuk, hogy hozzon létre egy keresési szolgáltatást egy olyan szinten, amely képes a kívánt tesztelési szint befogadására, majd tekintse át a következő útmutatást a költséghatékonyság és a kapacitás becsléséhez.

+ [Keresési szolgáltatás létrehozása](search-create-service-portal.md)
+ [A költségek megbecslése](search-sku-manage-costs.md)
+ [Kapacitás becslése](search-sku-manage-costs.md)