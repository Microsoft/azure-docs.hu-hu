---
title: Bevezetés a tárolócsoportokba
description: Megismeri a tárolócsoportokat a Azure Container Instances, amely egy életcikluson és erőforrásokon osztozó példányok gyűjteménye, például processzorok, tárolók és hálózatok
ms.topic: article
ms.date: 11/01/2019
ms.custom: mvc
ms.openlocfilehash: a2cb3eac5baa5b1035749d28b9fb99bbb45b9ee6
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790886"
---
# <a name="container-groups-in-azure-container-instances"></a>Tárolócsoportok az Azure Container Instancesben

A tárolócsoport legfelső Azure Container Instances a *tárolócsoport.* Ez a cikk ismerteti, hogy mik azok a tárolócsoportok, és milyen típusú forgatókönyveket engedélyeznek.

## <a name="what-is-a-container-group"></a>Mi az a tárolócsoport?

A tárolócsoportok tárolók gyűjteményei, amelyek ugyanazon a gazdagépen vannak ütemezve. A tárolócsoportban található tárolók közös életciklussal, erőforrásokkal, helyi hálózattal és tárolókötetekkel osztoznak. A koncepciójuk hasonló a [Kubernetes-hez][kubernetes-pod]használt *podhoz.*

Az alábbi ábrán egy példa látható egy több tárolót tartalmazó tárolócsoportra:

![Tárolócsoportok diagramja][container-groups-example]

Ez a példa tárolócsoport:

* Egyetlen gazdagépen van ütemezve.
* A rendszer DNS-névcímkét rendel hozzá.
* Egyetlen nyilvános IP-címet ad meg, egy elérhető porttal.
* Két tárolóból áll. Az egyik tároló a 80-as portot, a másik az 5000-es portot figyeli.
* Két Azure-fájlmegosztást tartalmaz kötet-csatlakoztatásként, és mindegyik tároló helyileg csatlakoztatja az egyik megosztást.

> [!NOTE]
> A többtárolós csoportok jelenleg csak a Linux-tárolókat támogatják. Windows-tárolók esetén a Azure Container Instances csak egyetlen tárolópéldány üzembe helyezését támogatja. Miközben dolgozunk a Windows-tárolók összes funkcióján, az aktuális platformbeli különbségeket a szolgáltatás áttekintésében [találja.](container-instances-overview.md#linux-and-windows-containers)

## <a name="deployment"></a>Üzembe helyezés

A többtárolós csoportok üzembe helyezésének két [][resource-manager template] gyakori módja a következő: egy Resource Manager sablon vagy egy [YAML-fájl használata.][yaml-file] A Resource Manager sablon használata akkor ajánlott, ha további Azure-szolgáltatási erőforrásokat (például egy Azure Files-megosztást) [][azure-files]kell üzembe helyeznie a tárolópéldányok üzembe helyezésekor. A YAML formátum tömörebb jellege miatt ajánlott YAML-fájlt használni, ha az üzemelő példány csak tárolópéldányokat tartalmaz. A beállítható tulajdonságokkal kapcsolatos részletekért tekintse meg a Resource Manager a [YAML referenciadokumentációját.](container-instances-reference-yaml.md) [](/azure/templates/microsoft.containerinstance/containergroups)

A tárolócsoport konfigurációjának megőrzéséhez exportálhatja a konfigurációt YAML-fájlba az az container export Azure [CLI-paranccsal.][az-container-export] Az exportálással a tárolócsoport konfigurációit a verzióvezérlőben tárolhatja a "kódként történő konfigurálás" érdekében. Az exportált fájlt kiindulási pontként is használhatja, amikor új konfigurációt fejleszt a YAML-ben.



## <a name="resource-allocation"></a>Erőforrás-elosztás

Azure Container Instances foglal le erőforrásokat, például processzorokat, memóriát és opcionálisan GPU-kat [(előzetes][gpus] verzió) egy [többtárolós][resource-requests] csoporthoz a csoportban található példányok erőforrás-kérésének hozzáadásával. Ha például cpu-erőforrásokat használ, és létrehoz egy tárolócsoportot két tárolópéldánysal, amelyek mind 1 CPU-t kérnek le, akkor a tárolócsoport 2 PROCESSZORt rendel hozzá.

### <a name="resource-usage-by-container-instances"></a>Erőforrás-használat tárolópéldányok szerint

A csoportban található egyes tárolópéldányok az erőforráskérésben megadott erőforrásokat osztják ki. A tárolópéldányok által egy csoportban használt erőforrások maximális használata azonban eltérő lehet, ha konfigurálja a választható erőforráskorlát [tulajdonságát.][resource-limits] A tárolópéldányok erőforráskorlátja nem lehet nagyobb vagy egyenlő, mint a kötelező [erőforrás-kérelem tulajdonság.][resource-requests]

* Ha nem ad meg erőforráskorlátot, a tárolópéldány maximális erőforrás-használata megegyezik az erőforrás-kérésével.

* Ha egy tárolópéldányra vonatkozó korlátot ad meg, a példány maximális kihasználtsága a kérésnél nagyobb lehet, a beállított korlátig. Ennek megfelelően a csoportban található más tárolópéldányok erőforrás-használata is csökkenhet. A tárolópéldányhoz beállítható maximális erőforráskorlát a csoporthoz lefoglalt összes erőforrás.
    
Például egy olyan csoportban, ahol két tárolópéldány 1 CPU-t kér, az egyik tároló olyan számítási feladatot futtathat, amely több processzor futtatását igényli, mint a másik.

Ebben a forgatókönyvben legfeljebb 2 PROCESSZORos erőforráskorlátot állíthat be a tárolópéldányhoz. Ez a konfiguráció lehetővé teszi, hogy a tárolópéldány legfeljebb 2 CPU-t használjon, ha elérhető.

> [!NOTE]
> A szolgáltatás mögöttes infrastruktúrája kis mennyiségű tárolócsoport-erőforrást használ fel. A tárolók a legtöbb, de nem az összes, a csoporthoz lefoglalt erőforráshoz hozzáférhetnek. Ezért tervezze meg a kis erőforráspuffert, amikor erőforrásokat kér a csoportban található tárolókhoz.

### <a name="minimum-and-maximum-allocation"></a>Minimális és maximális kiosztás

* Legalább **1** CPU és 1 GB memória lefoglalása egy tárolócsoport számára. Egy csoporton belül az egyes tárolópéldányok 1 PROCESSZORnál és 1 GB-nál kevesebb memóriával is kiépítve lehetnek. 

* A **tárolócsoportban** található erőforrások maximális [][region-availability] értékéhez tekintse meg az üzembe helyezési régióban Azure Container Instances erőforrások rendelkezésre állását.

## <a name="networking"></a>Hálózatkezelés

A tárolócsoportok megoszthatnak egy külső IP-címet, egy vagy több portot ezen az IP-címen, valamint egy DNS-címkét teljes tartománynévvel (FQDN). Ha engedélyezni szeretné, hogy a külső ügyfelek elérnek egy tárolót a csoporton belül, elérhetővé kell tennie a portot az IP-címen és a tárolóból. A tárolócsoport IP-címe és teljes tartománya a tárolócsoport törlésekor szabadul fel. 

A tárolócsoporton belül a tárolópéldányok bármely port localhoston keresztül elérhetik egymást, még akkor is, ha ezek a portok nincsenek külsőleg elérhetővé téve a csoport IP-címében vagy a tárolóban.

Szükség esetén tárolócsoportokat helyezhet üzembe egy Azure-beli virtuális [hálózatban,][virtual-network] hogy a tárolók biztonságosan kommunikáljanak a virtuális hálózat más erőforrásaival.

## <a name="storage"></a>Tárolás

A tárolócsoporton belül csatlakoztatni kívánt külső köteteket is megadhat. A támogatott kötetek a következők:
* [Azure-fájlmegosztás][azure-files]
* [Titkos][secret]
* [Üres könyvtár][empty-directory]
* [Klónozott Git-adattár][volume-gitrepo]

Ezeket a köteteket leképezheti adott elérési utakra a csoport egyes tárolóiban. 

## <a name="common-scenarios"></a>Gyakori forgatókönyvek

A többtárolós csoportok olyan esetekben hasznosak, amikor egyetlen funkcionális feladatot szeretne néhány tárolólemezképre osztani. Ezeket a rendszerképeket aztán különböző csapatok kézbesítik, és külön erőforrás-követelményekkel kell feleskedniük.

Példa a használatra:

* Egy webalkalmazást kiszolgáló tároló és egy tároló, amely a legújabb tartalmat húzza le a verziókezelőből.
* Egy alkalmazástároló és egy naplózási tároló. A naplózási tároló összegyűjti a fő alkalmazás naplóit és metrikáit, és hosszú távú tárolóba írja őket.
* Egy alkalmazástároló és egy monitorozási tároló. A monitorozási tároló rendszeres időközönként kérést küld az alkalmazásnak, hogy ellenőrizze, megfelelően működik-e és megfelelően válaszol-e, és riasztást küld, ha nem az.
* Egy előoldali tároló és egy háttértároló. Az előoldal egy webalkalmazást is kiszolgálhat, amely háttérszolgáltatást futtat az adatok lekéréséhez. 

## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan helyezhet üzembe egy többtárolós tárolócsoportot egy Azure Resource Manager sablonnal:

> [!div class="nextstepaction"]
> [Tárolócsoport üzembe helyezése][resource-manager template]

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
[az-container-export]: /cli/azure/container#az_container_export
