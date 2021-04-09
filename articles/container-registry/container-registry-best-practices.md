---
title: Ajánlott eljárások a beállításjegyzék használatához
description: Az ajánlott eljárások követésével megismerkedhet az Azure Container Registry leghatékonyabb használatával.
ms.topic: article
ms.date: 01/07/2021
ms.openlocfilehash: 01c8c7f547be9dd225022fb3315a4bdecc48c2bf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100578144"
---
# <a name="best-practices-for-azure-container-registry"></a>Az Azure Container Registry ajánlott eljárásai

Az ajánlott eljárások követésével maximalizálhatja az Azure-beli privát beállításjegyzék teljesítményének és költséghatékony felhasználásának hatékonyságát a tároló lemezképek és egyéb összetevők tárolására és üzembe helyezésére.

A beállításjegyzék-fogalmak hátterével kapcsolatban lásd: [a kibocsátásiegység-forgalmi jegyzékek, a Tárházak és a rendszerképek](container-registry-concepts.md). Lásd még: [javaslatok a lemezképek címkézésére és verziószámozására](container-registry-image-tag-version.md) a beállításjegyzékben található rendszerképek címkézéséhez és verziószámozásához. 

## <a name="network-close-deployment"></a>Hálózatközeli központi telepítés

A tárolóregisztrációs adatbázist ugyanabban az Azure-régióban hozza létre, amelyben a tárolókat helyezi üzembe. Ha a regisztrációs adatbázist a tároló gazdagéphez hálózatban közeli régióba telepíti, az csökkentheti a késést és a költségeket is.

A hálózatközeli központi telepítés a privát tárolóregisztrációs adatbázis használatának egyik fő oka. A Docker-rendszerképek hatékony [rétegezett felépítésüknek](https://docs.docker.com/engine/userguide/storagedriver/imagesandcontainers/) köszönhetően növekményesen is telepíthetők. Az új csomópontoknak azonban egy adott rendszerképhez minden szükséges réteget le kell kérniük. A kezdeti `docker pull` hamar akár több gigabájttá nőheti ki magát. Az üzemelő példányához közeli privát regisztrációs adatbázis csökkenti a hálózati késést.
A nyilvános felhők – köztük az Azure is – hálózati forgalmi díjat számítanak fel. A rendszerképek egyes adatközpontok közötti mozgatása hálózati forgalmi díjjal is jár a nagyobb mértékű késés mellett.

## <a name="geo-replicate-multi-region-deployments"></a>Többrégiós üzemelő példányok georeplikációja

Ha több régióban telepít tárolókat, használja az Azure Container Registry [georeplikációs](container-registry-geo-replication.md) funkcióját. Ha globális ügyfélbázist szolgál ki helyi adatközpontokból, vagy fejlesztői csapatának tagjai különböző helyeken tartózkodnak, a regisztrációs adatbázis georeplikálásával egyszerűsítheti a regisztrációs adatbázis kezelését és minimalizálhatja a késést. A regionális [webhookokat](container-registry-webhook.md) úgy is konfigurálhatja, hogy értesítést kapjon bizonyos replikákban lévő eseményekről, például a képek leküldésekor.

A Geo-replikáció [prémium](container-registry-skus.md) szintű kibocsátásiegység-forgalmi jegyzékekkel érhető el. A georeplikáció használatának megismeréséhez tekintse meg háromrészes útmutatónkat: [Georeplikáció az Azure Container Registry-ben](container-registry-tutorial-prepare-registry.md).

## <a name="maximize-pull-performance"></a>A lekéréses teljesítmény maximalizálása

Amellett, hogy a rendszerképeket az üzembe helyezésekhez közelíti, a rendszerképek jellemzői hatással lehetnek a lekérési teljesítményre.

* **Kép mérete** – a felesleges [rétegek](container-registry-concepts.md#manifest) eltávolításával vagy a rétegek méretének csökkentésével csökkentheti a képek méretét. A képméret csökkentésének egyik módja a [többfázisú Docker-létrehozási](https://docs.docker.com/develop/develop-images/multistage-build/) módszer használata, amely csak a szükséges futtatókörnyezet-összetevőket tartalmazza. 

  Győződjön meg arról is, hogy a rendszerkép tartalmazhat-e világosabb alap operációsrendszer-rendszerképet. Ha pedig olyan központi telepítési környezetet használ, mint például a Azure Container Instances, amely bizonyos alaplemezképek gyorsítótárazását hajtja végre, ellenőrizze, hogy képes-e a képréteget felcserélni valamelyik gyorsítótárazott rendszerképre. 
* **Rétegek száma** – a felhasznált rétegek számának kiegyensúlyozása. Ha túl kevés van, a rendszer nem használja a réteg újrafelhasználását és gyorsítótárazását a gazdagépen. Túl sok, és az üzembe helyezési környezet több időt tölt a húzással és a kibontással. Az öt – 10 réteg optimális.

Olyan Azure Container Registry [szolgáltatási szintet](container-registry-skus.md) is választhat, amely megfelel a teljesítmény igényeinek. A prémium szint biztosítja a legnagyobb sávszélességet és az egyidejű olvasási és írási műveletek legmagasabb arányát, ha nagy mennyiségű üzemelő példánya van.

## <a name="repository-namespaces"></a>Adattárnévterek

A tárház névtereit használva engedélyezheti egyetlen beállításjegyzék megosztását a szervezeten belüli több csoport között. A regisztrációs adatbázisok több környezeten és csoporton keresztül is megoszthatók. Az Azure Container Registry támogatja a beágyazott névtereket, amelyekkel elkülöníthetők a csoportok. A beállításjegyzék azonban egymástól függetlenül kezeli az összes tárházat, nem pedig a hierarchiát.

Vegyük példaként a következő tárolórendszerkép-címkéket. A vállalati szintű, például a-ben használt rendszerképek `aspnetcore` a legfelső szintű névtérbe kerülnek, míg a termékek és a marketing csoportok által birtokolt tároló lemezképek saját névtereket használnak.

- *contoso.azurecr.io/aspnetcore:2.0*
- *contoso.azurecr.io/products/widget/web:1*
- *contoso.azurecr.io/products/bettermousetrap/refundapi:12.3*
- *contoso.azurecr.io/marketing/2017-fall/concertpromotions/campaign:218.42*

## <a name="dedicated-resource-group"></a>Dedikált erőforráscsoport

Mivel a tároló-beállításjegyzékek olyan erőforrások, amelyeket több tároló gazdagépe használ, a beállításjegyzéknek a saját erőforráscsoporthoz kell tartoznia.

Bár előfordulhat, hogy egy adott gazdagép típusával kísérletezik, például [Azure Container instances](../container-instances/container-instances-overview.md), valószínűleg törölni szeretné a tároló példányát, ha elkészült. Előfordulhat azonban, hogy meg szeretné tartani azokat a rendszerképeket, amelyeket átvitt az Azure Container Registry-be. Azzal, hogy a regisztrációs adatbázis a saját erőforráscsoportjába helyezi, csökkentheti annak esélyét, hogy véletlenül törli a rendszerképeket, amikor törli a tárolópéldány erőforráscsoportját.

## <a name="authentication-and-authorization"></a>Hitelesítés és engedélyezés

Azure tárolóregisztrációs adatbázissal való hitelesítéskor két fő forgatókönyv fordulhat elő: az egyéni hitelesítés és a szolgáltatásos (vagy „távfelügyelt”) hitelesítés. A következő táblázat röviden bemutatja ezeket a forgatókönyveket és a hozzájuk fűződő ajánlott hitelesítési módokat.

| Típus | Példaforgatókönyv | Javasolt módszer |
|---|---|---|
| Egyéni identitás | Egy fejlesztő rendszerképeket hív le a saját számítógépére, vagy helyez át onnan. | [az acr login](/cli/azure/acr#az-acr-login) |
| Távfelügyelt/szolgáltatásos identitás | Buildelési és üzembe helyezési folyamatok, amelyekben a felhasználó nem vesz közvetlenül részt. | [Szolgáltatásnév](container-registry-authentication.md#service-principal) |

Ezen és más Azure Container Registry hitelesítési forgatókönyvekkel kapcsolatos részletes információkért lásd: [hitelesítés Azure Container registryvel](container-registry-authentication.md).

Azure Container Registry támogatja a szervezeten belüli biztonsági eljárásokat a feladatok és a jogosultságok különböző identitásokra való terjesztéséhez. [Szerepköralapú hozzáférés-vezérlés](container-registry-roles.md)használatával rendeljen hozzá megfelelő engedélyeket a különböző felhasználókhoz, szolgáltatásokhoz vagy más, különböző beállításjegyzék-műveleteket végző identitásokhoz. Például leküldéses engedélyeket rendelhet egy olyan egyszerű szolgáltatásnév számára, amelyet egy összeállítási folyamat használ, és lekéréses engedélyeket rendel a telepítéshez használt másik identitáshoz. Hozzon létre [jogkivonatokat](container-registry-repository-scoped-permissions.md) a részletes, időkorlátos hozzáféréshez adott adattárakhoz.

## <a name="manage-registry-size"></a>Regisztrációs adatbázis méretének kezelése      

Az egyes [tárolók beállításjegyzék-szolgáltatási szintjeinek][container-registry-skus] tárolási korlátozásai egy tipikus forgatókönyvhöz igazodnak: **alapszintű** , első lépések, **standard** a legtöbb üzemi alkalmazáshoz, **prémium** szintű teljesítmény és [földrajzi replikálás][container-registry-geo-replication]. A regisztrációs adatbázis élettartama során érdemes felügyelnie annak méretét a nem használt tartalmak törlésével.

Használja az Azure CLI-parancsot az [ACR show-][az-acr-show-usage] use paranccsal a beállításjegyzék aktuális méretének megjelenítéséhez:

```azurecli
az acr show-usage --resource-group myResourceGroup --name myregistry --output table
```

```output
NAME      LIMIT         CURRENT VALUE    UNIT
--------  ------------  ---------------  ------
Size      536870912000  185444288        Bytes
Webhooks  100                            Count
```

A Azure Portalban található beállításjegyzék **áttekintésében** használt aktuális tárterületet is megtalálhatja:

![Adattár használati adatainak megtekintése az Azure Portalon][registry-overview-quotas]

### <a name="delete-image-data"></a>Rendszerkép-adatok törlése

Azure Container Registry számos módszert támogat a képadatoknak a tároló-beállításjegyzékből való törléséhez. A képeket címkével vagy jegyzékfájl-kivonattal is törölheti, vagy törölheti a teljes tárházat.

A képadatok beállításjegyzékből való törlésével kapcsolatos részletekért, beleértve a címkézett (más néven "lelógó" vagy "árva") lemezképeket, lásd: [tároló lemezképek törlése Azure Container Registry](container-registry-delete.md).

## <a name="next-steps"></a>Következő lépések

A Azure Container Registry számos különböző szinten, más néven SKU-ban is elérhető. Az elérhető szolgáltatási szintek részletes ismertetését lásd: [Azure Container Registry szolgáltatási szintek](container-registry-skus.md).

A tároló-beállításjegyzékek biztonsági helyzetének javítására vonatkozó javaslatokért lásd: [Azure Container Registry Azure biztonsági alapterve](security-baseline.md).

<!-- IMAGES -->
[delete-repository-portal]: ./media/container-registry-best-practices/delete-repository-portal.png
[registry-overview-quotas]: ./media/container-registry-best-practices/registry-overview-quotas.png

<!-- LINKS - Internal -->
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-acr-show-usage]: /cli/azure/acr#az-acr-show-usage
[azure-cli]: /cli/azure
[azure-portal]: https://portal.azure.com
[container-registry-geo-replication]: container-registry-geo-replication.md
[container-registry-skus]: container-registry-skus.md