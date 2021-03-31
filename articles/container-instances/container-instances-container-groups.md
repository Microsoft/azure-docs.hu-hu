---
title: A Container groups bemutatása
description: További információ a Azure Container Instances lévő tárolók csoportjairól, az életciklusokat és erőforrásokat, például a processzorokat, a tárhelyet és a hálózatot megosztó példányok gyűjteménye
ms.topic: article
ms.date: 11/01/2019
ms.custom: mvc
ms.openlocfilehash: 72ebe6186da179bc5a1effddcc14327455eb7557
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "89612403"
---
# <a name="container-groups-in-azure-container-instances"></a>Tárolócsoportok az Azure Container Instancesben

Azure Container Instances a legfelső szintű erőforrás a *tároló csoport*. Ez a cikk ismerteti, hogy milyen tároló-csoportokat és milyen típusú forgatókönyveket engedélyeznek.

## <a name="what-is-a-container-group"></a>Mi az a Container Group?

A Container Group olyan tárolók gyűjteménye, amelyek ugyanazon a gazdagépen vannak ütemezve. A tárolók csoportjainak tárolói életciklussal, erőforrásokkal, helyi hálózattal és tárolási kötetekkel rendelkeznek. Ez a fogalom hasonló a [Kubernetes][kubernetes-pod]található *Pod* -hoz.

Az alábbi ábrán egy példa látható egy több tárolót tartalmazó tároló csoportra:

![Container groups diagram][container-groups-example]

Ez a példa tároló Csoport:

* Egy gazdagépen van ütemezve.
* A DNS-név címkéje hozzá van rendelve.
* Egyetlen nyilvános IP-cím közzététele egy elérhető porttal.
* Két tárolóból áll. Az egyik tároló figyeli az 80-es portot, míg a másik a 5000-es porton figyel.
* A két Azure-fájlmegosztást tartalmaz kötet-csatlakoztatásként, és mindegyik tároló helyileg csatlakoztatja az egyik megosztást.

> [!NOTE]
> A több tárolóból álló csoportok jelenleg csak a Linux-tárolókat támogatják. A Windows-tárolók esetében Azure Container Instances csak egyetlen tároló példány üzembe helyezését támogatja. Miközben dolgozunk a Windows-tárolók összes funkciójának bekapcsolásán, megkeresheti a platform aktuális eltéréseit a szolgáltatás [áttekintésében](container-instances-overview.md#linux-and-windows-containers).

## <a name="deployment"></a>Üzembe helyezés

A többtárolós csoportok üzembe helyezésének két gyakori módja van: egy [Resource Manager-sablon][resource-manager template] vagy egy [YAML-fájl][yaml-file]használata. A Resource Manager-sablonok használata akkor ajánlott, ha további Azure-szolgáltatási erőforrásokat (például [Azure Files megosztást][azure-files]) kell üzembe helyeznie a tároló példányainak telepítésekor. A YAML formátumának tömörebb jellege miatt a YAML-fájlok használata akkor ajánlott, ha a központi telepítés csak tároló példányokat tartalmaz. A beállítható tulajdonságokkal kapcsolatos részletekért tekintse meg a [Resource Manager-sablonok referenciáját](/azure/templates/microsoft.containerinstance/containergroups) vagy a [YAML](container-instances-reference-yaml.md) dokumentációját.

A tároló csoport konfigurációjának megőrzése érdekében exportálhatja a konfigurációt egy YAML-fájlba az Azure CLI-parancs az [Container export][az-container-export]paranccsal. Az Exportálás lehetővé teszi, hogy a "konfiguráció as code" verzióban tárolja a tároló csoport konfigurációit. Vagy használja kiindulási pontként az exportált fájlt, amikor új konfigurációt fejleszt a YAML-ben.



## <a name="resource-allocation"></a>Erőforrás-kiosztás

A Azure Container Instances erőforrásokat, például CPU-t, memóriát és opcionálisan [GPU][gpus] -t (előzetes verzió) foglal le egy többtárolós csoportba úgy, hogy hozzáadja a csoport példányainak [erőforrás-kérelmeit][resource-requests] . A CPU-erőforrások például akkor vesznek részt, ha két tároló-példánnyal rendelkező tároló csoportot hoz létre, amelyek mindegyike 1 PROCESSZORt kér, a tároló csoport 2 processzort foglal le.

### <a name="resource-usage-by-container-instances"></a>Erőforrás-használat Container instances alapján

A csoport minden tároló példánya lefoglalja az erőforrás-kérelmében megadott erőforrásokat. Egy csoportban lévő tároló-példány által használt erőforrások maximális száma azonban eltérő lehet, ha a választható [erőforrás-korlát][resource-limits] tulajdonságot konfigurálja. Egy tároló példány erőforrás-korlátjának nagyobbnak vagy egyenlőnek kell lennie a kötelező [erőforrás-kérelem][resource-requests] tulajdonsággal.

* Ha nem ad meg erőforrás-korlátot, a tároló-példány maximális Erőforrás-kihasználtsága megegyezik az erőforrás-kérelemmel.

* Ha egy tároló példányra vonatkozó korlátot ad meg, a példány maximális kihasználtsága a megadott korlátnál nagyobb lehet. Ennek megfelelően a csoport más tároló példányai által használt erőforrás-használat csökkenhet. A tároló-példányhoz beállítható maximális erőforrás-korlát a csoport számára lefoglalt összes erőforrás.
    
Például egy olyan csoportban, amelyben az 1 CPU-t igénylő két tároló példány található, az egyik tároló olyan munkaterhelést futtathat, amelynek több processzort kell futtatnia a többinél.

Ebben az esetben legfeljebb 2 processzort állíthat be a tároló-példányhoz. Ez a konfiguráció lehetővé teszi, hogy a Container-példány legfeljebb 2 CPU-t használjon, ha elérhető.

> [!NOTE]
> A szolgáltatás alapjául szolgáló infrastruktúra egy kis mennyiségű tároló-csoport erőforrásait használja. A tárolók a csoport számára lefoglalt összes erőforráshoz hozzáférhetnek. Ebből kifolyólag tervezzen egy kisebb erőforrás-puffert a csoportban lévő tárolók erőforrásainak kérésekor.

### <a name="minimum-and-maximum-allocation"></a>Minimális és maximális kiosztás

* **Legalább** 1 processzor és 1 GB memória lefoglalása egy tároló csoportba. Egy csoporton belüli egyes tároló-példányok kevesebb mint 1 PROCESSZORral és 1 GB memóriával is üzembe helyezhetők. 

* A tárolói csoportok **maximális** erőforrásaival kapcsolatban tekintse meg az [Erőforrás rendelkezésre állását][region-availability] Azure Container instances a telepítési régióban.

## <a name="networking"></a>Hálózatkezelés

A Container groups megoszthat egy külső elérésű IP-címet, egy vagy több portot az adott IP-címen, valamint egy teljes tartománynevet (FQDN) tartalmazó DNS-címkét is. Ahhoz, hogy a külső ügyfelek elérjék a csoporton belüli tárolókat, ki kell jelölnie a portot az IP-címen és a tárolóból. A rendszer a tároló csoportjának IP-címét és teljes tartománynevét kibocsátja a Container Group törlésekor. 

Egy tároló csoporton belül a Container instances a localhost-on keresztül bármilyen portot elér, még akkor is, ha ezek a portok nem jelennek meg a csoport IP-címén vagy a tárolón kívülről.

A tároló-csoportok üzembe helyezése egy Azure-beli [virtuális hálózatban][virtual-network] , amely lehetővé teszi, hogy a tárolók biztonságosan kommunikáljanak a virtuális hálózat más erőforrásaival.

## <a name="storage"></a>Tárolás

Külső köteteket is megadhat a tároló csoportba való csatlakoztatáshoz. A támogatott kötetek a következők:
* [Azure-fájlmegosztás][azure-files]
* [Titkos][secret]
* [Üres könyvtár][empty-directory]
* [Klónozott git-tárház][volume-gitrepo]

Ezeket a köteteket meghatározott elérési utakra is leképezheti egy csoport egyes tárolói között. 

## <a name="common-scenarios"></a>Gyakori forgatókönyvek

A többtárolós csoportok olyan esetekben hasznosak, amikor egyetlen funkcionális feladatot szeretne megosztani kis számú tároló-lemezképbe. Ezeket a lemezképeket ezután különböző csapatok továbbítják, és külön erőforrás-követelményekkel rendelkezhetnek.

A használati példa a következőket tartalmazhatja:

* Egy webalkalmazást és egy tárolót kiszolgáló tároló a legújabb tartalmat húzza a forrás vezérlőelemből.
* Egy alkalmazás-tároló és egy naplózási tároló. A naplózási tároló gyűjti a naplók és a metrikák kimenetét a fő alkalmazásból, és a hosszú távú tárolásra írja azokat.
* Egy alkalmazás-tároló és egy figyelési tároló. A megfigyelési tároló rendszeres időközönként egy kérést küld az alkalmazásnak, hogy ellenőrizze, hogy fut-e, és hogy megfelelően válaszol-e, és riasztást küld, ha nem.
* Egy előtér-tároló és egy háttér-tároló. Az előtér egy webalkalmazást is kiszolgálhat, és a szolgáltatás futtatásával lekérheti az adatgyűjtést. 

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan helyezhet üzembe egy több tárolóból álló tároló csoportot Azure Resource Manager sablonnal:

> [!div class="nextstepaction"]
> [Tároló-csoport üzembe helyezése][resource-manager template]

<!-- IMAGES -->
[container-groups-example]: ./media/container-instances-container-groups/container-groups-example.png

<!-- LINKS - External -->
[dcos-pod]: https://dcos.io/docs/1.10/deploying-services/pods/
[kubernetes-pod]: https://kubernetes.io/docs/concepts/workloads/pods/

<!-- LINKS - Internal -->
[resource-manager template]: container-instances-multi-container-group.md
[yaml-file]: container-instances-multi-container-yaml.md
[region-availability]: container-instances-region-availability.md
[resource-requests]: /rest/api/container-instances/containergroups/createorupdate#resourcerequests
[resource-limits]: /rest/api/container-instances/containergroups/createorupdate#resourcelimits
[resource-requirements]: /rest/api/container-instances/containergroups/createorupdate#resourcerequirements
[azure-files]: container-instances-volume-azure-files.md
[virtual-network]: container-instances-virtual-network-concepts.md
[secret]: container-instances-volume-secret.md
[volume-gitrepo]: container-instances-volume-gitrepo.md
[gpus]: container-instances-gpu.md
[empty-directory]: container-instances-volume-emptydir.md
[az-container-export]: /cli/azure/container#az-container-export
