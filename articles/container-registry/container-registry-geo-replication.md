---
title: Regisztrációs adatbázis georeplikálása
description: Első lépések egy georeplikált Azure Container Registry létrehozásához és kezeléséhez, amely lehetővé teszi, hogy a regisztrációs adatbázis több régiót szolgáljon ki több főkiszolgálós regionális replikával. A georeplikáció a Prémium szolgáltatási szint egyik funkciója.
author: stevelas
ms.topic: article
ms.date: 07/21/2020
ms.author: stevelas
ms.openlocfilehash: 3e5b064ec37b855186f633677e2b1a3f615a6736
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783862"
---
# <a name="geo-replication-in-azure-container-registry"></a>Georeplikáció a Azure Container Registry

Azok a vállalatok, amelyek helyi jelenlétre vagy gyors biztonsági mentésre van szükségem, úgy döntenek, hogy több Azure-régióból futtatnak szolgáltatásokat. Az ajánlott eljárás az, ha mindegyik olyan régióban elhelyez egy tárolóregisztrációs adatbázist, amelyben rendszerképeket futtat, így hálózatközeli műveletek futtathatók, ami lehetővé teszi a rendszerképek gyors és megbízható átvitelét. A georeplikáció segítségével az Azure Container Registry egyetlen regisztrációs adatbázisként futtatható, amely több régiót szolgál ki több főkiszolgálós regionális regisztrációs adatbázisokkal. 

A georeplikált regisztrációs adatbázis a következő előnyöket nyújtja:

* Egyetlen beállításjegyzék-, rendszerkép- és címkenév több régióban is használható
* A regionális üzemelő példányok teljesítményének és megbízhatóságának javítása hálózatbiztos beállításjegyzék-hozzáféréssel
* Csökkentse az adatátviteli költségeket a rendszerképrétegek lekért helyi, replikált regisztrációs adatbázisból a tárológazdaszámítógéppel azonos vagy közeli régióban
* A több régióra kiterjedő regisztrációs adatbázisok egy helyen felügyelhetők
* A beállításjegyzék rugalmassága regionális kimaradás esetén

> [!NOTE]
> Ha egynél több Azure Container Registryben kell megőriznie a tároló rendszerképének másolatát, a Azure Container Registry is támogatja a [rendszerkép importálását.](container-registry-import-images.md) Egy DevOps-munkafolyamatban például Docker-parancsok használata nélkül importálhat egy rendszerképet egy fejlesztési regisztrációs adatbázisból egy éles regisztrációs adatbázisba.
>

## <a name="example-use-case"></a>Példahasználati eset
A Contoso egy nyilvános jelenléti webhelyet futtat az Egyesült Államok, Kanada és Európa területén. Annak érdekében, hogy ezeket a piacokat helyi és hálózatra közeli tartalommal szolgálják ki, [a](../aks/index.yml) Contoso Azure Kubernetes Service (AKS) fürtöt futtat az USA nyugati régiója, az USA keleti régiója, Közép-Kanada és Nyugat-Európa területén. A Docker-rendszerképként üzembe helyezett webhelyalkalmazás ugyanazt a kódot és rendszerképet használja minden régióban. A régió helyi tartalma egy adatbázisból lesz lekérve, amely minden régióban egyedileg van kiépítve. Minden regionális üzemelő példány egyedi konfigurációval rendelkezik az olyan erőforrásokhoz, mint a helyi adatbázis.

A fejlesztői csapat Seattle WA-ban található, és az USA nyugati régiója adatközpontot használja.

![Leküldi a 30000-et több beállításregisztrálóba](media/container-registry-geo-replication/before-geo-replicate.png)<br />*Leküldi a 30000-et több beállításregisztrálóba*

A georeplikációs funkciók használata előtt a Contoso egy USA-alapú regisztrációs adatbázissal rendelkezik az USA nyugati részén, egy további regisztrációs adatbázissal Nyugat-Európában. A különböző régiók kiszolgálása érdekében a fejlesztői csapat két különböző regisztrációs jegyzékbe leküldte a rendszerképeket.

```bash
docker push contoso.azurecr.io/public/products/web:1.2
docker push contosowesteu.azurecr.io/public/products/web:1.2
```
![Leválasztás több beállításregisztrálóból](media/container-registry-geo-replication/before-geo-replicate-pull.png)<br />*Leválasztás több beállításregisztrálóból*

A több regisztrációs jegyzék tipikus kihívásai a következők:

* Az USA keleti régiója, az USA nyugati régiója és a Közép-Kanada fürtök mind az USA nyugati régiója beállításjegyzékből származó rendszerképeket húzzák ki, és a távoli tárolók minden egyes gazdagépe az USA nyugati régiója adatközpontból származó rendszerképeket vesz fel.
* A fejlesztői csapatnak képeket kell leküldnie az USA nyugati régiója és a nyugat-európai regisztrációs jegyzékekbe.
* A fejlesztői csapatnak minden regionális üzemelő példányt a helyi regisztrációs adatbázisra hivatkozó rendszerképnevekkel kell konfigurálnia és karbantartani.
* A beállításjegyzékhez való hozzáférést minden régióhoz konfigurálni kell.

## <a name="benefits-of-geo-replication"></a>A georeplikáció előnyei

![Lekért georeplikált beállításjegyzékből](media/container-registry-geo-replication/after-geo-replicate-pull.png)

Az alkalmazás georeplikációs funkciójának Azure Container Registry az alábbi előnyöket biztosítják:

* Egyetlen beállításjegyzék kezelése az összes régióban: `contoso.azurecr.io`
* A rendszerképtelepítések egyetlen konfigurációját kezelheti, mivel minden régió ugyanazt a rendszerkép-URL-címet használja: `contoso.azurecr.io/public/products/web:1.2`
* Leküldés egyetlen beállításjegyzékbe, a georeplikációt pedig az ACR kezeli. Az ACR csak egyedi rétegeket replikál, így csökkenti a régiók közötti adatátvitelt. 
* Regionális [webhookok konfigurálása](container-registry-webhook.md) az adott replikákban történt eseményekről való értesítéshez.
* Magas rendelkezésre állékonyságú regisztrációs adatbázist biztosít, amely rugalmas a regionális kimaradásokkal szemben.

Azure Container Registry a rendelkezésre [állási zónákat](zone-redundancy.md) is támogatja egy rugalmas és magas rendelkezésre állású Azure Container Registry létrehozásához egy Azure-régióban. A rendelkezésre állási zónák egy régión belüli redundanciához és a több régió közötti georeplikáció együttesen növeli a regisztrációs adatbázis megbízhatóságát és teljesítményét.

## <a name="configure-geo-replication"></a>Aktív georeplikáció konfigurálása

A georeplikáció konfigurálása egyszerű, csak kattintson a régiókra a térképen. A georeplikációt olyan eszközökkel is kezelheti, mint az Azure CLI [az acr replication](/cli/azure/acr/replication) parancsai, vagy üzembe helyezhet egy georeplikációhoz engedélyezett beállításjegyzéket egy Azure Resource Manager [sablonnal.](https://github.com/Azure/azure-quickstart-templates/tree/master/101-container-registry-geo-replication)

A georeplikáció a [prémium beállításregisztrációs rendszer egyik funkciója.](container-registry-skus.md) Ha a beállításjegyzék még nem Prémium szintű, az alapszintűről a Standardról a Prémiumra válthat a [Azure Portal:](https://portal.azure.com)

![Szolgáltatási szintek váltása a Azure Portal](media/container-registry-skus/update-registry-sku.png)

A prémium beállításjegyzék georeplikációjának konfigurálához jelentkezzen be a(z) Azure Portal: https://portal.azure.com .

Lépjen a Azure Container Registry, és válassza a **Replikációk lehetőséget:**

![Replikációk az Azure Portal tárolójegyzékeinek felhasználói felületén](media/container-registry-geo-replication/registry-services.png)

Megjelenik egy térkép, amely az összes aktuális Azure-régiót megjeleníti:

 ![Régiótérkép az Azure Portalon](media/container-registry-geo-replication/registry-geo-map.png)

* A kék hatszögek az aktuális replikákat képviselik
* A zöld hatszögek a lehetséges replikarégiókat képviselik
* A szürke hatszögek olyan Azure-régiókat képviselnek, amelyek még nem érhetők el replikációhoz

A replika konfiguráláshoz válasszon egy zöld hatszöget, majd válassza a **Létrehozás lehetőséget:**

 ![Replikáció létrehozásának felhasználói felülete az Azure Portalon](media/container-registry-geo-replication/create-replication.png)

További replikák konfigurálhoz válassza ki a többi régióhoz a zöld hatszögeket, majd kattintson a **Létrehozás gombra.**

Az ACR megkezdi a rendszerképek szinkronizálását a konfigurált replikákon. Ha elkészült, a portálon a Ready (Kész) *értéknek kell állnia.* A replika állapota a portálon nem frissül automatikusan. A frissítés gombbal láthatja a frissített állapotot.

## <a name="considerations-for-using-a-geo-replicated-registry"></a>Georeplikált beállításjegyzékek használatának szempontjai

* A georeplikált regisztrációs adatbázis minden régiója a beállítás után független. Azure Container Registry az összes georeplikált régióra érvényesek.
* Amikor rendszerképeket küld le vagy kér le egy georeplikált regisztrációs adatbázisból, a Azure Traffic Manager a háttérben elküldi a kérést az Önhöz legközelebbi régióban található regisztrációs adatbázisnak a hálózati késés tekintetében.
* Miután leküld egy rendszerképet vagy címkefrissítést a legközelebbi régióba, némi időt vesz igénybe, hogy a Azure Container Registry replikálja a jegyzékeket és a rétegeket a többi régióba, amelyekre feliratkozott. A nagyobb rendszerképek replikálása hosszabb ideig tart, mint a kisebbek. A rendszerképeket és címkéket egy esetleges konzisztenciamodellel szinkronizálja a rendszer a replikációs régiók között.
* A georeplikált beállításjegyzékbe leküldéses frissítésektől függő munkafolyamatok kezeléséhez javasoljuk, hogy a [webhookokat](container-registry-webhook.md) úgy konfigurálja, hogy válaszoljanak a leküldéses eseményekre. Regionális webhookokat állíthat be egy georeplikált regisztrációs adatbázisban a leküldéses események nyomon követéséhez, amint azok befejeződnek a georeplikált régiókban.
* A tartalomrétegeket képviselő blobok kiszolgálására a Azure Container Registry adatvégpontokat használ. A regisztrációs [adatbázis minden](container-registry-firewall-access-rules.md#enable-dedicated-data-endpoints) georeplikált régiójában engedélyezheti a dedikált adatvégpontokat a regisztrációs adatbázishoz. Ezek a végpontok lehetővé teszik a szoros hatókörű tűzfal-hozzáférési szabályok konfigurálást. Hibaelhárítási célokra letilthatja a replikációra történő [útválasztást a](#temporarily-disable-routing-to-replication) replikált adatok megőrzése mellett.
* Ha privát kapcsolatot [konfigurál](container-registry-private-link.md) a regisztrációs adatbázishoz privát végpontok használatával egy virtuális hálózatban, alapértelmezés szerint a dedikált adatvégpontokat az egyes georeplikált régiókban engedélyezi a rendszer. 

## <a name="delete-a-replica"></a>Replika törlése

Miután konfigurált egy replikát a beállításjegyzékhez, bármikor törölheti, ha már nincs rá szükség. Töröljön egy replikát a Azure Portal más eszközökkel, például az [az acr replication delete](/cli/azure/acr/replication#az_acr_replication_delete) paranccsal az Azure CLI-ban.

Replika törlése a Azure Portal:

1. Keresse meg a Azure Container Registry, majd válassza **a Replikációk lehetőséget.**
1. Válassza ki a replika nevét, majd válassza a **Törlés lehetőséget.** Erősítse meg, hogy törölni szeretné a replikát.

Az Azure CLI használata a *myregistry replika* törléséhez az USA keleti régiójában:

```azurecli
az acr replication delete --name eastus --registry myregistry
```

## <a name="geo-replication-pricing"></a>A georeplikáció díjszabása

A georeplikáció a prémium szolgáltatási szint [egyik](container-registry-skus.md) szolgáltatása, Azure Container Registry. Amikor egy regisztrációs adatbázist replikál a kívánt régiókba, prémium regisztrációs adatbázis-díjakat kell fizetnie minden egyes régióért.

Az előző példában a Contoso összevont két regisztrációs adatbázist egyre, és replikákat ad hozzá az USA keleti régiója, Közép-Kanada és Nyugat-Európa számára. A Contoso havonta négyszer fizet a Prémium díjért, további konfiguráció és felügyelet nélkül. Mostantól minden régió helyileg leküldi a rendszerképeket, így javítja a teljesítményt és a megbízhatóságot az USA nyugati régiójából Kanadába és az USA keleti régiójába irányuló hálózati forgalom díja nélkül.

## <a name="troubleshoot-push-operations-with-geo-replicated-registries"></a>Leküldéses műveletek hibaelhárítása georeplikált regisztrációs adatbázisokkal
 
Előfordulhat, hogy egy docker-ügyfél, amely egy rendszerképet egy georeplikált regisztrációs adatbázisba ad le, nem minden rendszerképréteget és annak jegyzékfájlját egyetlen replikált régióba. Ez azért fordulhat elő, Azure Traffic Manager a tárolójegyzék-kérelmeket a hálózat legközelebbi replikált beállításjegyzékéhez. Ha a beállításjegyzék két *közeli replikációs* régióval rendelkezik, a rendszerképrétegek és a jegyzék szétosztható a két hely között, és a leküldéses művelet meghiúsul a jegyzék érvényesítésekor. Ez a probléma a beállításjegyzék DNS-nevének egyes Linux-gazdagépek esetén való feloldása miatt merül fel. Ez a probléma nem fordul elő Windows rendszeren, amely ügyféloldali DNS-gyorsítótárat biztosít.
 
Ha ez a probléma jelentkezik, az egyik megoldás egy ügyféloldali DNS-gyorsítótár alkalmazása, például a `dnsmasq` Linux-gazdagépen. Ez segít biztosítani, hogy a beállításjegyzék neve konzisztensen legyen feloldva. Ha Linux rendszerű virtuális gépet használ az Azure-ban egy regisztrációs adatbázisba való leküldéshez, tekintse meg az Azure-beli Linux rendszerű virtuális gépek DNS-névfeloldási [beállításaival kapcsolatos lehetőségeket.](../virtual-machines/linux/azure-dns.md)

A rendszerképek leküldésekor a legközelebbi replikához való DNS-feloldás optimalizálásához konfiguráljon egy georeplikált regisztrációs adatbázist ugyanabban az Azure-régióban, ahol a leküldéses műveletek forrása, vagy a legközelebbi régióban, ha az Azure-ban dolgozik.

### <a name="temporarily-disable-routing-to-replication"></a>A replikációra történő útválasztás ideiglenes letiltása

A georeplikált beállításjegyzékek műveleteinek hibaelhárításához érdemes ideiglenesen letiltani a Traffic Manager egy vagy több replikációra történő útválasztást. Az Azure CLI 2.8-as verziójától kezdődően konfigurálhat egy beállítást (előzetes verzió), amikor replikált régiót hoz létre vagy `--region-endpoint-enabled` frissít. Ha a replikációs beállítást , a Traffic Manager már nem ad át Docker-leküldéses vagy lekéréses kérelmeket `--region-endpoint-enabled` `false` erre a régióra. Alapértelmezés szerint az útválasztás az összes replikációra engedélyezve van, és az összes replikáció adatszinkronizálása történik, függetlenül attól, hogy az útválasztás engedélyezve van vagy le van tiltva.

Ha le szeretné tiltani az útválasztást egy meglévő replikációhoz, először futtassa [az az acr replication list (az az acr replication list)][az-acr-replication-list] futtatásával a replikációkat a beállításjegyzékben. Ezután futtassa [az az acr replication update tartományvezérlőt,][az-acr-replication-update] és állítsa `--region-endpoint-enabled false` be az halmazt egy adott replikációhoz. Például a *westus* replikációs beállításának konfigurálása a *myregistry-ban:*

```azurecli
# Show names of existing replications
az acr replication list --registry --output table

# Disable routing to replication
az acr replication update --name westus \
  --registry myregistry --resource-group MyResourceGroup \
  --region-endpoint-enabled false
```

Az útválasztás replikációra való visszaállítása:

```azurecli
az acr replication update --name westus \
  --registry myregistry --resource-group MyResourceGroup \
  --region-endpoint-enabled true
```

## <a name="next-steps"></a>Következő lépések

Tekintse meg a háromrészes oktatóanyag-sorozatot, a georeplikációt [a Azure Container Registry.](container-registry-tutorial-prepare-registry.md) Végig kell mennie egy georeplikált regisztrációs adatbázis létrehozásán, egy tároló létrehozásán, majd egyetlen paranccsal történő üzembe helyezésén több regionális `docker push` tárolópéldányon Web Apps Tárolópéldányok esetén.

> [!div class="nextstepaction"]
> [Georeplikáció a Azure Container Registry](container-registry-tutorial-prepare-registry.md)

[az-acr-replication-list]: /cli/azure/acr/replication#az_acr_replication_list
[az-acr-replication-update]: /cli/azure/acr/replication#az_acr_replication_update
