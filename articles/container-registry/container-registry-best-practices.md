---
title: Ajánlott eljárások a beállításjegyzék használatához
description: Az ajánlott eljárások követésével megismerkedhet az Azure Container Registry leghatékonyabb használatával.
ms.topic: article
ms.date: 01/07/2021
ms.openlocfilehash: 0811cc4a5bffc21ffba19e64a3887eab6bc36fbb
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784136"
---
# <a name="best-practices-for-azure-container-registry"></a>Az Azure Container Registry ajánlott eljárásai

Ezeknek az ajánlott eljárásoknak a segítségével maximalizálhatja a privát regisztrációs adatbázis teljesítményét és költséghatékony használatát az Azure-ban a tároló-rendszerképek és más összetevők tárolására és üzembe helyezésére.

A beállításjegyzékekkel kapcsolatos alapfogalmak hátteréért lásd: [About registry, repositories, and images (A regisztrációs adatbázisokkal,](container-registry-concepts.md)adattárokkal és rendszerképekkel kapcsolatos információk). A [tároló-rendszerképek címkézésére](container-registry-image-tag-version.md) és verziószámozásra vonatkozó javaslatok a regisztrációs adatbázisban található rendszerképek címkézésére és verziószámozásra vonatkozó stratégiákat is tartalmaznak. 

## <a name="network-close-deployment"></a>Hálózatközeli központi telepítés

A tárolóregisztrációs adatbázist ugyanabban az Azure-régióban hozza létre, amelyben a tárolókat helyezi üzembe. Ha a regisztrációs adatbázist a tároló gazdagéphez hálózatban közeli régióba telepíti, az csökkentheti a késést és a költségeket is.

A hálózatközeli központi telepítés a privát tárolóregisztrációs adatbázis használatának egyik fő oka. A Docker-rendszerképek hatékony [rétegezett felépítésüknek](https://docs.docker.com/engine/userguide/storagedriver/imagesandcontainers/) köszönhetően növekményesen is telepíthetők. Az új csomópontoknak azonban egy adott rendszerképhez minden szükséges réteget le kell kérniük. A kezdeti `docker pull` hamar akár több gigabájttá nőheti ki magát. Az üzemelő példányához közeli privát regisztrációs adatbázis csökkenti a hálózati késést.
A nyilvános felhők – köztük az Azure is – hálózati forgalmi díjat számítanak fel. A rendszerképek egyes adatközpontok közötti mozgatása hálózati forgalmi díjjal is jár a nagyobb mértékű késés mellett.

## <a name="geo-replicate-multi-region-deployments"></a>Többrégiós üzemelő példányok georeplikációja

Ha több régióban telepít tárolókat, használja az Azure Container Registry [georeplikációs](container-registry-geo-replication.md) funkcióját. Ha globális ügyfélbázist szolgál ki helyi adatközpontokból, vagy fejlesztői csapatának tagjai különböző helyeken tartózkodnak, a regisztrációs adatbázis georeplikálásával egyszerűsítheti a regisztrációs adatbázis kezelését és minimalizálhatja a késést. A regionális [webhookokat](container-registry-webhook.md) úgy is konfigurálhatja, hogy bizonyos replikák eseményeiről, például a rendszerképek lek lekért eseményéről értesítsenek.

A georeplikáció prémium [regisztrációs](container-registry-skus.md) beállításokkal érhető el. A georeplikáció használatának megismeréséhez tekintse meg háromrészes útmutatónkat: [Georeplikáció az Azure Container Registry-ben](container-registry-tutorial-prepare-registry.md).

## <a name="maximize-pull-performance"></a>A lekért teljesítmény maximalizálása

Amellett, hogy a rendszerképeket az üzemelő példányok közelében helyezi el, a rendszerképek jellemzői is hatással lehetnek a lekért teljesítményre.

* **Képméret** – Minimalizálja a képek méretét [](container-registry-concepts.md#manifest) a szükségtelen rétegek eltávolításával vagy a rétegek méretének csökkentésével. A rendszerképméret csökkentésének egyik módja a [többszakaszos Docker-build](https://docs.docker.com/develop/develop-images/multistage-build/) megközelítés használata, amely csak a szükséges futásidejű összetevőket tartalmazza. 

  Azt is ellenőrizze, hogy a rendszerkép tartalmaz-e világosabb alap operációsrendszer-képet. Ha pedig olyan üzembe helyezési környezetet használ(Azure Container Instances, amely bizonyos alapként használt rendszerképeket gyorsítótáraz, ellenőrizze, hogy felcserélhet-e egy képréteget az egyik gyorsítótárazott lemezképpel. 
* **Rétegek száma –** A használt rétegek számának kiegyensúlyozása. Ha túl kevés van, nem használhatja ki a réteg újrafelhasználásának és gyorsítótárazásának előnyét a gazdagépen. Túl sok, és az üzembe helyezési környezet több időt tölt le- és kibontva. Az 5–10 réteg optimális.

A [teljesítménybeli igényeknek megfelelő](container-registry-skus.md) Azure Container Registry szolgáltatási szintet is választhat. Nagy mennyiségű üzemelő példány esetén a Prémium szint biztosítja a legnagyobb sávszélességet és az egyidejű olvasási és írási műveletek legnagyobb számát.

## <a name="repository-namespaces"></a>Adattárnévterek

Adattárnévterek használatával engedélyezheti egyetlen regisztrációs adatbázis megosztását több csoport között a szervezeten belül. A regisztrációs adatbázisok több környezeten és csoporton keresztül is megoszthatók. Az Azure Container Registry támogatja a beágyazott névtereket, amelyekkel elkülöníthetők a csoportok. A beállításjegyzék azonban az összes adattárat egymástól függetlenül kezeli, nem hierarchiaként.

Vegyük példaként a következő tárolórendszerkép-címkéket. A vállalati szinten használt rendszerképek (például a) a gyökérnévtérben vannak elhelyezve, míg a Termékek és Marketing csoportok tulajdonában lévő tároló-rendszerképek `aspnetcore` saját névtereket használnak.

- *contoso.azurecr.io/aspnetcore:2.0*
- *contoso.azurecr.io/products/widget/web:1*
- *contoso.azurecr.io/products/bettermousetrap/refundapi:12.3*
- *contoso.azurecr.io/marketing/2017-fall/concertpromotions/campaign:218.42*

## <a name="dedicated-resource-group"></a>Dedikált erőforráscsoport

Mivel a tárolóregisztrálók olyan erőforrások, amelyek több tároló gazdagépen is használatosak, a regisztrációs adatbázisnak a saját erőforráscsoportban kell lennie.

Bár kísérletezhet egy adott gazdagéptípussal, például a [Azure Container Instances,](../container-instances/container-instances-overview.md)valószínűleg törölnie kell a tárolópéldányt, amikor végzett. Előfordulhat azonban, hogy meg szeretné tartani azokat a rendszerképeket, amelyeket átvitt az Azure Container Registry-be. Azzal, hogy a regisztrációs adatbázis a saját erőforráscsoportjába helyezi, csökkentheti annak esélyét, hogy véletlenül törli a rendszerképeket, amikor törli a tárolópéldány erőforráscsoportját.

## <a name="authentication-and-authorization"></a>Hitelesítés és engedélyezés

Azure tárolóregisztrációs adatbázissal való hitelesítéskor két fő forgatókönyv fordulhat elő: az egyéni hitelesítés és a szolgáltatásos (vagy „távfelügyelt”) hitelesítés. A következő táblázat röviden bemutatja ezeket a forgatókönyveket és a hozzájuk fűződő ajánlott hitelesítési módokat.

| Típus | Példaforgatókönyv | Javasolt módszer |
|---|---|---|
| Egyéni identitás | Egy fejlesztő rendszerképeket hív le a saját számítógépére, vagy helyez át onnan. | [az acr login](/cli/azure/acr#az_acr_login) |
| Távfelügyelt/szolgáltatásos identitás | Buildelési és üzembe helyezési folyamatok, amelyekben a felhasználó nem vesz közvetlenül részt. | [Szolgáltatásnév](container-registry-authentication.md#service-principal) |

Az ezekkel és más hitelesítési forgatókönyvekkel kapcsolatos részletes információkért lásd Azure Container Registry Azure Container Registryvel való [hitelesítést.](container-registry-authentication.md)

Azure Container Registry támogatja a szervezetben a feladatok és jogosultságok különböző identitások közötti elosztására vonatkozó biztonsági eljárásokat. Szerepköralapú [hozzáférés-vezérlés](container-registry-roles.md)használatával rendeljen megfelelő engedélyeket a különböző beállításjegyzék-műveleteket végző különböző felhasználókhoz, szolgáltatásnévhez vagy más identitásokhoz. Például rendeljen leküldéses engedélyeket egy összeállítási folyamatban használt szolgáltatásnévhez, és rendeljen leküldéses engedélyeket egy másik, üzembe helyezéshez használt identitáshoz. [Jogkivonatok létrehozása](container-registry-repository-scoped-permissions.md) az egyes adattárakhoz való részletes, korlátozott hozzáféréshez.

## <a name="manage-registry-size"></a>Regisztrációs adatbázis méretének kezelése      

Az egyes tároló-beállításjegyzékek szolgáltatási rétegének tárolási korlátai a tipikus forgatókönyvhöz igazodnak: Alapszintű az első lépésekhez, **Standard** a legtöbb éles alkalmazáshoz, valamint **Premium** a nagy teljesítményű és [][container-registry-skus] georeplikációhoz.  [][container-registry-geo-replication] A regisztrációs adatbázis élettartama során érdemes felügyelnie annak méretét a nem használt tartalmak törlésével.

Használja az [az acr show-usage Azure CLI-parancsot][az-acr-show-usage] a regisztrációs adatbázis aktuális méretének megjelenítéséhez:

```azurecli
az acr show-usage --resource-group myResourceGroup --name myregistry --output table
```

```output
NAME      LIMIT         CURRENT VALUE    UNIT
--------  ------------  ---------------  ------
Size      536870912000  185444288        Bytes
Webhooks  100                            Count
```

A használt aktuális tárterületet  a regisztrációs adatbázis áttekintését ismertető témakörben is Azure Portal:

![Adattár használati adatainak megtekintése az Azure Portalon][registry-overview-quotas]

### <a name="delete-image-data"></a>Képadatok törlése

Azure Container Registry a rendszerképadatok tároló-beállításjegyzékből való törlésének számos metódusát támogatja. Képeket címkék vagy jegyzékkivonatok alapján törölhet, vagy akár teljes adattárat is törölhet.

A rendszerképadatok beállításjegyzékből való törlésével kapcsolatos részletekért, beleértve a nem megjelölt (néha "dangling" vagy "árva") rendszerképeket, lásd: Tároló rendszerképeinek törlése a [Azure Container Registry.](container-registry-delete.md)

## <a name="next-steps"></a>Következő lépések

Azure Container Registry több szinten (más néven SKUs) érhető el, amelyek különböző képességeket biztosítanak. Az elérhető szolgáltatásszintekkel kapcsolatos részletekért lásd: Azure Container Registry [szolgáltatási szintek.](container-registry-skus.md)

A tárolóregisztrációk biztonsági javítására vonatkozó javaslatokat az Azure biztonsági alapkonfigurációja a [tárolóregisztrációk Azure Container Registry.](security-baseline.md)

<!-- IMAGES -->
[delete-repository-portal]: ./media/container-registry-best-practices/delete-repository-portal.png
[registry-overview-quotas]: ./media/container-registry-best-practices/registry-overview-quotas.png

<!-- LINKS - Internal -->
[az-acr-repository-delete]: /cli/azure/acr/repository#az_acr_repository_delete
[az-acr-show-usage]: /cli/azure/acr#az_acr_show_usage
[azure-cli]: /cli/azure
[azure-portal]: https://portal.azure.com
[container-registry-geo-replication]: container-registry-geo-replication.md
[container-registry-skus]: container-registry-skus.md