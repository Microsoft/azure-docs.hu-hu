---
title: Az Azure Container Registry Termékváltozatai
description: Hasonlítsa össze a különböző szolgáltatásszintek elérhető az Azure Container Registryben.
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: article
ms.date: 08/30/2018
ms.author: danlep
ms.openlocfilehash: 3f058a68057d6b84cbbb2dfdb08ea8c2cb12b0b9
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/13/2018
ms.locfileid: "53322092"
---
# <a name="azure-container-registry-skus"></a>Az Azure Container Registry Termékváltozatai

Az Azure Container Registry (ACR) több szolgáltatási szinten, SKU-k más néven érhető el. Termékváltozatokban biztosítanak, kiszámítható díjszabás és a kapacitás és a használati minták, a privát Docker-tárolójegyzék az Azure-nak megfelelően számos lehetőség közül választhat.

| SKU | Managed | Leírás |
| --- | :-------: | ----------- |
| **Basic** | Igen | Költségoptimalizált belépési pont az Azure Container Registryt tanulmányozó fejlesztőknek. Alapszintű beállításjegyzékek ugyanazokat a programozott képességeket, Standard és prémium (a Azure Active Directory hitelesítési integráció, a lemezkép törlése és a webhook) rendelkezik. Azonban a belefoglalt tárterület és a lemezkép átviteli sebesség különösen leginkább megfelelő alacsonyabb használati forgatókönyvek. |
| **Standard** | Igen | Szabványos regisztrációs adatbázisokkal a Basic, a nagyobb foglalt tároló- és képfájlok átviteli sebesség megegyező funkciókat kínál. A Standard beállításjegyzékek a legtöbb éles forgatókönyvhöz megfelelők. |
| **Prémium** | Igen | Prémium szintű beállításjegyzékek adja meg a belefoglalt tárterület és a párhuzamos műveletek nagy mennyiségű forgatókönyveket lehetővé legmagasabb összege. Mellett lemezkép nagyobb átviteli sebességet, prémium szintű szolgáltatásai, például a hozzáadja [georeplikációs] [ container-registry-geo-replication] egyetlen regisztrációs adatbázis kezeléséhez több régióban és [megbízhatóság (előzetes verzió)tartalom](container-registry-content-trust.md)lemezkép-címke aláíráshoz. |
| Klasszikus<sup>1</sup> | Nem | Ez a Termékváltozat engedélyezve van az Azure-ban az Azure Container Registry szolgáltatás kezdeti verziójában. Klasszikus beállításjegyzékek élvezik egy tárfiókot, amelyet az Azure létrehozza az előfizetésében, ami lehetővé teszi az ACR-hez, például a nagyobb átviteli sebesség és georeplikáció útján magasabb szintű képességeket biztosít. |

<sup>1</sup> a klasszikus SKU lesz **elavult** a **március 2019**. Új tároló-beállításjegyzékek alapszintű, Standard vagy prémium szintű használja.

Kiválasztotta azt egy magasabb szintű Termékváltozatot kínál a további teljesítmény és méretezhetőség, azonban az összes felügyelt termékváltozatok adja meg ugyanazokat a programozott képességeket. A több szolgáltatási szintekkel megkezdheti az alapszintű, majd a a rendszerleíró adatbázis használattal növekedése Standard és prémium szintű átalakítása.

## <a name="managed-vs-unmanaged"></a>Nem felügyelt és felügyelt

A Basic, Standard és prémium szintű termékváltozatok összefoglaló néven *felügyelt* beállításjegyzékek, és a klasszikus beállításjegyzékek, *nem felügyelt*. Az elsődleges különbség a kettő között, a tárolólemezképek tárolási módját.

### <a name="managed-basic-standard-premium"></a>Felügyelt (alapszintű, Standard, prémium)

A felügyelt beállításjegyzékek előnyök teljes egészében az Azure által kezelt képet tárból. Azt jelenti egy storage-fiókot, amely tárolja a lemezképeket nem jelenik meg az Azure-előfizetésen belül. Több előnye is van a felügyelt beállításjegyzék Termékváltozatai egyik használatának köszönhetően jutottunk, a részletes tárgyalt [képtárolás tárolót az Azure Container Registry][container-registry-storage]. Ez a cikk a felügyelt beállításjegyzék Termékváltozatai és azok képességeinek összpontosít.

### <a name="unmanaged-classic"></a>Nem felügyelt (klasszikus)

> [!IMPORTANT]
> A klasszikus Termékváltozat hamarosan elavulttá válik, és a március 2019 után nem lesz elérhető. Alapszintű, Standard vagy prémium szintű minden új beállításjegyzékek használata.

Klasszikus beállításjegyzékek vannak "nem felügyelt" abban az értelemben, hogy a tárfiók, amely a klasszikus beállításjegyzékben található belül *a* Azure-előfizetést. Így Ön a tárfiók, amelyben a tárolórendszerképek tárolt kezeléséért felelős. A nem felügyelt beállításjegyzékek, nem lehet átállítani a termékváltozatok között a változó igényeknek megfelelő (eltérő [frissítése] [ container-registry-upgrade] a felügyelt beállításjegyzékre), és a felügyelt beállításjegyzékek több szolgáltatás (például nem érhető el tároló kép törléséhez, [georeplikációs][container-registry-geo-replication], és [webhookok][container-registry-webhook]).

Klasszikus beállításjegyzék frissítése a felügyelt termékváltozatok egyik kapcsolatos további információkért lásd: [klasszikus beállításjegyzék frissítése][container-registry-upgrade].

## <a name="sku-feature-matrix"></a>Termékváltozat funkció mátrix

Az alábbi táblázat ismerteti az a funkciók és az alapszintű, Standard és prémium szolgáltatásszintek korlátait.

[!INCLUDE [container-instances-limits](../../includes/container-registry-limits.md)]

## <a name="changing-skus"></a>SKU-k módosítása

Módosíthatja a beállításjegyzék-Termékváltozat az Azure CLI-vel vagy az Azure Portalon. Áthelyezheti szabadon felügyelt termékváltozatok közötti mindaddig, amíg a Termékváltozat való váltás rendelkezik a szükséges maximális tárolási kapacitását. Ha klasszikusról az egyik a felügyelt termékváltozatok vált, nem válthat vissza klasszikus--egy egyirányú átalakítás.

### <a name="azure-cli"></a>Azure CLI

Az Azure CLI-ben termékváltozatok közötti áthelyezéséhez használja a [az acr update] [ az-acr-update] parancsot. Ha például szeretne váltani a prémium szintű:

```azurecli
az acr update --name myregistry --sku Premium
```

### <a name="azure-portal"></a>Azure Portal

A tároló-beállításjegyzék **áttekintése** az Azure Portalon válassza ki a **frissítés**, majd egy új **Termékváltozat** a Termékváltozat legördülő listából.

![Az Azure Portalon a Termékváltozat a tárolóregisztrációs adatbázis frissítése][update-registry-sku]

Ha klasszikus beállításjegyzék, az Azure Portalon a felügyelt Termékváltozat nem választhat. Ehelyett először [frissítése] [ container-registry-upgrade] egy felügyelt beállításjegyzéket, (tekintse meg [módosítása klasszikusról](#changing-from-classic)).

## <a name="changing-from-classic"></a>Kapcsolatcsoportok módosítása klasszikusról módosítása

További szempontot figyelembe kell venni egy nem felügyelt klasszikus registry egy felügyelt a Basic, Standard vagy prémium szintű termékváltozatok áttelepítésekor. Ha a klasszikus beállításjegyzékben tartalmaz egy képeket nagy számú és méretű számos gigabájtig terjedhetnek, az áttelepítési folyamat eltarthat egy ideig. Ezenkívül `docker push` műveletek le vannak tiltva, amíg az áttelepítés akkor fejeződött be.

A klasszikus beállításjegyzék frissítése a felügyelt termékváltozatok közül a részletekért lásd: [klasszikus tároló-beállításjegyzék frissítése][container-registry-upgrade].

## <a name="pricing"></a>Díjszabás

Az egyes az Azure Container Registry Termékváltozatai díjszabásról lásd: [Container Registry díjszabás][container-registry-pricing].

Az adatforgalmi díjszabás részleteiért lásd: [adatforgalmi díjszabás részletei](https://azure.microsoft.com/pricing/details/bandwidth/). 

## <a name="next-steps"></a>További lépések

**Az Azure Container Registry-ütemterv**

Látogasson el a [ACR ütemterv] [ acr-roadmap] a közeljövőben megjelenő funkciókról információkat találhat a szolgáltatás a Githubon.

**Az Azure Container Registry uservoice-on**

Küldje el, és szavazzon az új szolgáltatási javaslataikat [ACR UserVoice][container-registry-uservoice].

<!-- IMAGES -->
[update-registry-sku]: ./media/container-registry-skus/update-registry-sku.png

<!-- LINKS - External -->
[acr-roadmap]: https://aka.ms/acr/roadmap
[container-registry-pricing]: https://azure.microsoft.com/pricing/details/container-registry/
[container-registry-uservoice]: https://feedback.azure.com/forums/903958-azure-container-registry

<!-- LINKS - Internal -->
[az-acr-update]: /cli/azure/acr#az-acr-update
[container-registry-geo-replication]: container-registry-geo-replication.md
[container-registry-upgrade]: container-registry-upgrade.md
[container-registry-storage]: container-registry-storage.md
[container-registry-webhook]: container-registry-webhook.md
