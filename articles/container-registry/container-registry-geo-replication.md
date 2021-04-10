---
title: Regisztrációs adatbázis georeplikálása
description: Ismerkedjen meg a földrajzilag replikált Azure Container Registry létrehozásával és kezelésével, amely lehetővé teszi, hogy a beállításjegyzék több régiót is kiszolgáljon több főkiszolgálós regionális replikával. A Geo-replikáció a prémium szintű szolgáltatási szint egyik funkciója.
author: stevelas
ms.topic: article
ms.date: 07/21/2020
ms.author: stevelas
ms.openlocfilehash: 4e82be0e81e5e8c0182e061a0fba0f880bd45cc6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102632390"
---
# <a name="geo-replication-in-azure-container-registry"></a>Geo-replikálás Azure Container Registry

Azok a vállalatok, amelyek helyi jelenlétet vagy gyors biztonsági mentést kívánnak, több Azure-régióból is futtathatják a szolgáltatásokat. Az ajánlott eljárás az, ha mindegyik olyan régióban elhelyez egy tárolóregisztrációs adatbázist, amelyben rendszerképeket futtat, így hálózatközeli műveletek futtathatók, ami lehetővé teszi a rendszerképek gyors és megbízható átvitelét. A georeplikáció segítségével az Azure Container Registry egyetlen regisztrációs adatbázisként futtatható, amely több régiót szolgál ki több főkiszolgálós regionális regisztrációs adatbázisokkal. 

A georeplikált regisztrációs adatbázis a következő előnyöket nyújtja:

* Az önálló beállításjegyzék, a képek és a címkék nevei több régióban is használhatók
* A regionális központi telepítések teljesítményének és megbízhatóságának javítása a beállításjegyzék bezárásával
* Az adatátviteli költségek csökkentése a képrétegek helyi, replikált beállításjegyzékből való húzásával, a tároló gazdagépével megegyező vagy közeli régióban
* A több régióra kiterjedő regisztrációs adatbázisok egy helyen felügyelhetők
* A beállításjegyzék rugalmassága regionális leállás esetén

> [!NOTE]
> Ha több Azure Container registryben kell karbantartani a tároló lemezképeit, Azure Container Registry is támogatja a [lemezképek importálását](container-registry-import-images.md). Egy DevOps-munkafolyamatban például a rendszerképeket importálhatja egy fejlesztői beállításjegyzékből egy éles beállításjegyzékbe anélkül, hogy a Docker-parancsokat kellene használnia.
>

## <a name="example-use-case"></a>Példa használati esetre
A contoso nyilvános jelenlétű webhelyet futtat az Egyesült Államok, Kanada és Európa területén. Ha ezeket a piacokat helyi és hálózati tartalommal szeretné kiszolgálni, a contoso az USA nyugati régiójában, az USA keleti régiójában, a közép-Kanadában és Nyugat-Európában található [Azure Kubernetes Service](../aks/index.yml) (ak) fürtöket futtatja. A Docker-rendszerképként üzembe helyezett webhely-alkalmazás ugyanazt a kódot és képet használja az összes régióban. Az adott régióhoz tartozó tartalmat a rendszer beolvassa egy adatbázisból, amely az egyes régiókban egyedi módon van kiépítve. Minden regionális központi telepítés egyedi konfigurációval rendelkezik a helyi adatbázishoz hasonló erőforrásokhoz.

A fejlesztői csapat Seattle WA-ben található, amely az USA nyugati régiójának adatközpontját használja.

![Több beállításjegyzékbe való továbbítás](media/container-registry-geo-replication/before-geo-replicate.png)<br />*Több beállításjegyzékbe való továbbítás*

A földrajzi replikálási funkciók használata előtt a contoso az USA nyugati régiójában volt az USA-beli regisztrációs adatbázis, amely a Nyugat-Európában további beállításjegyzéket tartalmaz. A különböző régiók kiszolgálása érdekében a fejlesztői csapat két különböző beállításjegyzékbe küldi a lemezképeket.

```bash
docker push contoso.azurecr.io/public/products/web:1.2
docker push contosowesteu.azurecr.io/public/products/web:1.2
```
![Több beállításjegyzékből való húzás](media/container-registry-geo-replication/before-geo-replicate-pull.png)<br />*Több beállításjegyzékből való húzás*

Több beállításjegyzék tipikus kihívásai többek között a következők:

* Az USA keleti régiója, az USA nyugati régiója és a kanadai középső klaszterek mind lekérik az USA nyugati régiójában.
* A fejlesztői csapatnak képeket kell leküldenie az USA nyugati régiójában és a Nyugat-európai nyilvántartásba.
* A fejlesztői csapatnak minden regionális központi telepítést konfigurálnia kell, és meg kell őriznie a helyi beállításjegyzékre hivatkozó képnevekkel.
* Minden régióhoz konfigurálni kell a beállításjegyzék-hozzáférést.

## <a name="benefits-of-geo-replication"></a>A Geo-replikáció előnyei

![Kihúzás földrajzilag replikált beállításjegyzékből](media/container-registry-geo-replication/after-geo-replicate-pull.png)

A Azure Container Registry geo-replikációs funkciójának használatával ezek az előnyök megvalósulnak:

* Egyetlen beállításjegyzék kezelése minden régióban: `contoso.azurecr.io`
* A rendszerkép központi telepítésének egyetlen konfigurációjának kezelése, mivel az összes régió ugyanazt a rendszerkép-URL-címet használja: `contoso.azurecr.io/public/products/web:1.2`
* Leküldés egyetlen beállításjegyzékbe, míg az ACR kezeli a Geo-replikálást. Az ACR csak egyedi rétegeket replikál, így csökkentve az adatátvitelt a régiók között. 
* A regionális [webhookok](container-registry-webhook.md) konfigurálásával értesítheti az adott replikákban lévő eseményekről.
* Adjon meg egy olyan, magasan elérhető beállításjegyzéket, amely rugalmas a regionális kimaradásokkal szemben.

A Azure Container Registry a [rendelkezésre állási zónákat](zone-redundancy.md) is támogatja egy rugalmas és magas rendelkezésre állású Azure Container Registry létrehozásához az Azure-régión belül. Egy régión belüli redundancia rendelkezésre állási zónáinak és a több régióban történő földrajzi replikálásnak a kombinációja fokozza a beállításjegyzék megbízhatóságát és teljesítményét.

## <a name="configure-geo-replication"></a>Aktív georeplikáció konfigurálása

A földrajzi replikálás konfigurálása olyan egyszerű, mintha a térképen a régiók elemre kattintana. A Geo-replikációt olyan eszközökkel is kezelheti, mint az az [ACR Replication](/cli/azure/acr/replication) parancsok az Azure CLI-ben, vagy egy [Azure Resource Manager-sablonnal](https://github.com/Azure/azure-quickstart-templates/tree/master/101-container-registry-geo-replication)történő földrajzi replikáláshoz engedélyezett beállításjegyzéket helyezhet üzembe.

A Geo-replikáció a prémium szintű [kibocsátásiegység-forgalmi jegyzékek](container-registry-skus.md)egyik funkciója. Ha a beállításjegyzék még nem prémium szintű, az alapszintű és a standard csomagról prémiumra válthat a [Azure Portalban](https://portal.azure.com):

![A szolgáltatási szintek váltásának Azure Portal](media/container-registry-skus/update-registry-sku.png)

A prémium szintű beállításjegyzék geo-replikációjának konfigurálásához jelentkezzen be a Azure Portalba a következő címen: https://portal.azure.com .

Navigáljon a Azure Container Registryhoz, és válassza a **replikálások** lehetőséget:

![Replikációk az Azure Portal tárolójegyzékeinek felhasználói felületén](media/container-registry-geo-replication/registry-services.png)

Megjelenik egy Térkép, amely az összes aktuális Azure-régiót megjeleníti:

 ![Régiótérkép az Azure Portalon](media/container-registry-geo-replication/registry-geo-map.png)

* A kék hatszögek az aktuális replikákat jelölik
* A zöld hatszögek a lehetséges replika régiókat jelölik
* A szürke hatszögek a replikáláshoz még nem elérhető Azure-régiókat jelölik

A replika konfigurálásához válasszon ki egy zöld hatszöget, majd válassza a **Létrehozás** lehetőséget:

 ![Replikáció létrehozásának felhasználói felülete az Azure Portalon](media/container-registry-geo-replication/create-replication.png)

További replikák konfigurálásához válassza a többi régió zöld hatszögét, majd kattintson a **Létrehozás** gombra.

Az ACR megkezdi a lemezképek szinkronizálását a konfigurált replikák között. Ha elkészült, a portál *készen áll*. A portálon lévő replika állapota nem frissül automatikusan. A frissítés gomb használatával tekintse meg a frissített állapotot.

## <a name="considerations-for-using-a-geo-replicated-registry"></a>A földrajzilag replikált beállításjegyzék használatának szempontjai

* A földrajzilag replikált beállításjegyzék minden régiója független a beállítás után. Azure Container Registry SLA-kat az egyes földrajzilag replikált régiókban alkalmazza.
* Amikor leküldi vagy lekéri a képeket egy földrajzilag replikált beállításjegyzékből, az Azure Traffic Manager a háttérben elküldi a kérést az Ön számára legközelebb eső régióban található beállításjegyzékbe a hálózati késés szempontjából.
* Miután leküldte a rendszerképet vagy a címkét a legközelebbi régióra, időbe telik, amíg a Azure Container Registry replikálja a jegyzékeket és a rétegeket a többi, Ön által választott régióba. A nagyobb méretű képek replikálásához hosszabb időt is igénybe kell venni, mint a kisebbek. A rendszer a képeket és címkéket szinkronizálja a replikációs régiók között egy végleges konzisztencia-modellel.
* A földrajzilag replikált beállításjegyzék leküldéses frissítéseitől függő munkafolyamatok kezeléséhez javasoljuk, hogy a [webhookokat](container-registry-webhook.md) úgy konfigurálja, hogy válaszoljanak a leküldéses eseményekre. A regionális webhookok a földrajzilag replikált beállításjegyzékben állíthatók be, hogy nyomon kövessék a leküldéses eseményeket a földrajzilag replikált régiókban.
* A tartalmi rétegeket képviselő Blobok kiszolgálásához Azure Container Registry adatvégpontokat használ. Engedélyezheti a beállításjegyzékhez tartozó [dedikált adatvégpontokat](container-registry-firewall-access-rules.md#enable-dedicated-data-endpoints) a beállításjegyzék földrajzilag replikált régióiban. Ezek a végpontok lehetővé teszik a szűk hatókörű tűzfal-hozzáférési szabályok konfigurálását. Hibaelhárítási célból [letilthatja a replikáció útválasztását a](#temporarily-disable-routing-to-replication) replikált adat fenntartása mellett.
* Ha [privát](container-registry-private-link.md) végpontokat használ a beállításjegyzékhez egy virtuális hálózaton, a földrajzilag replikált régiók dedikált adatvégpontja alapértelmezés szerint engedélyezve van. 

## <a name="delete-a-replica"></a>Replika törlése

Miután konfigurálta a beállításjegyzékhez egy replikát, bármikor törölheti, ha már nincs rá szükség. Törölje a replikát a Azure Portal vagy más eszközök használatával, például az az [ACR Replication delete](/cli/azure/acr/replication#az-acr-replication-delete) paranccsal az Azure CLI-ben.

Replika törlése a Azure Portalban:

1. Navigáljon a Azure Container Registry, és válassza a **replikációk** lehetőséget.
1. Jelölje ki a replika nevét, majd válassza a **Törlés** lehetőséget. Erősítse meg, hogy törölni kívánja a replikát.

Az USA keleti régiójában az *myregistry* -replika törléséhez az Azure CLI használatával:

```azurecli
az acr replication delete --name eastus --registry myregistry
```

## <a name="geo-replication-pricing"></a>Geo-replikáció díjszabása

A Geo-replikáció a Azure Container Registry [prémium szintű szolgáltatási](container-registry-skus.md) szintjének egyik funkciója. Ha a beállításjegyzéket a kívánt régióba replikálja, az egyes régiók esetében prémium szintű regisztrációs díjat kell fizetnie.

Az előző példában a contoso két regisztrációs adatbázisba konszolidált egyet, és replikákat ad hozzá az USA keleti régiója, Közép-Kanada és Nyugat-Európa között. A contoso havonta négy alkalommal fizet, további konfiguráció vagy felügyelet nélkül. Minden régió most lekéri a lemezképeket helyileg, a teljesítmény javítása, a megbízhatóság és az USA nyugati régiója és az USA keleti régiója között elérkező költségek nélkül.

## <a name="troubleshoot-push-operations-with-geo-replicated-registries"></a>Leküldéses műveletek hibakeresése földrajzilag replikált beállításjegyzékekkel
 
Előfordulhat, hogy egy olyan Docker-ügyfél, amely egy földrajzi replikált beállításjegyzékbe küld egy rendszerképet, lehetséges, hogy nem küldi le az összes képréteget és annak jegyzékfájlját egyetlen replikált régióba. Ez azért fordulhat elő, mert az Azure Traffic Manager átirányítja a regisztrációs kérelmeket a hálózatra legközelebb replikált beállításjegyzékbe. Ha a beállításjegyzék két *közeli* replikációs régióval rendelkezik, a képrétegek és a jegyzékfájl kiterjeszthető a két helyre, a leküldéses művelet pedig meghiúsul, ha a jegyzékfájl érvényesítve van. Ez a probléma azért fordul elő, mert a beállításjegyzék DNS-neve egyes Linux-gazdagépeken fel van oldva. Ez a probléma nem fordul elő a Windows rendszeren, amely ügyféloldali DNS-gyorsítótárat biztosít.
 
Ha ez a probléma merül fel, az egyik megoldás egy ügyféloldali DNS-gyorsítótár alkalmazása, például `dnsmasq` a Linux-gazdagépen. Ezzel biztosítható, hogy a beállításjegyzék neve következetesen legyen feloldva. Ha Linux rendszerű virtuális gépet használ az Azure-ban egy beállításjegyzékbe való leküldéshez, tekintse meg az [Azure-beli Linux rendszerű virtuális gépek DNS-névfeloldási lehetőségeinek](../virtual-machines/linux/azure-dns.md)beállításai című témakört.

Ha a DNS-feloldást a legközelebbi replikára szeretné optimalizálni, amikor képeket küld, állítson be egy földrajzilag replikált beállításjegyzéket ugyanabban az Azure-régióban, mint a leküldéses műveletek forrása, vagy a legközelebbi régió, amikor az Azure-on kívül dolgozik.

### <a name="temporarily-disable-routing-to-replication"></a>Útválasztás ideiglenes letiltása a replikáláshoz

Ha a földrajzilag replikált beállításjegyzékkel kapcsolatos műveleteket szeretné elhárítani, érdemes lehet ideiglenesen letiltani Traffic Manager útválasztást egy vagy több replikálásra. Az Azure CLI 2,8-es verziójától kezdve megadhat egy `--region-endpoint-enabled` beállítást (előzetes verzió) a replikált régió létrehozásakor vagy frissítésekor. Amikor beállítja a replikálás `--region-endpoint-enabled` lehetőségét `false` , Traffic Manager a már nem irányítja a Docker-leküldéses vagy lekéréses kérelmeket erre a régióra. Alapértelmezés szerint az összes replikációhoz való útválasztás engedélyezve van, és az adatszinkronizálás az összes replikáció során megtörténik, hogy az útválasztás engedélyezve van-e vagy le van tiltva.

Egy meglévő replikáció útválasztásának letiltásához először az [az ACR Replication List][az-acr-replication-list] parancsot kell futtatnia a beállításjegyzékben lévő replikációk listázásához. Ezután futtassa [az ACR Replication Update][az-acr-replication-update] parancsot, és állítson be `--region-endpoint-enabled false` egy adott replikálást. Például a *westus* replikálás beállításának konfigurálása a *myregistry*-ben:

```azurecli
# Show names of existing replications
az acr replication list --registry --output table

# Disable routing to replication
az acr replication update --name westus \
  --registry myregistry --resource-group MyResourceGroup \
  --region-endpoint-enabled false
```

Az Útválasztás visszaállítása a replikálásra:

```azurecli
az acr replication update --name westus \
  --registry myregistry --resource-group MyResourceGroup \
  --region-endpoint-enabled true
```

## <a name="next-steps"></a>Következő lépések

Tekintse meg a három részből álló oktatóanyag-sorozatot, a [geo-replikációt Azure Container Registryban](container-registry-tutorial-prepare-registry.md). Végigvezeti a földrajzilag replikált beállításjegyzék létrehozásán, a tároló kialakításán, majd egyetlen `docker push` paranccsal, több regionális Web Apps a tárolók példányain való üzembe helyezésével.

> [!div class="nextstepaction"]
> [Geo-replikálás Azure Container Registry](container-registry-tutorial-prepare-registry.md)

[az-acr-replication-list]: /cli/azure/acr/replication#az-acr-replication-list
[az-acr-replication-update]: /cli/azure/acr/replication#az-acr-replication-update
