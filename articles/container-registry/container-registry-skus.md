---
title: A beállításjegyzék szolgáltatási szintjei és funkciói
description: Ismerje meg a Azure Container Registry alapszintű, standard és prémium szolgáltatási szintjein található szolgáltatásokat és korlátokat (kvótákat).
ms.topic: article
ms.date: 05/18/2020
ms.openlocfilehash: b55fc16dcd6dcb544ed4597ce4bdc6ba17b52646
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98015664"
---
# <a name="azure-container-registry-service-tiers"></a>Azure Container Registry szolgáltatási szintek

Azure Container Registry több szolgáltatási rétegben (más néven SKU) is elérhető. Ezek a szintek kiszámítható díjszabást és számos lehetőséget biztosítanak az Azure-beli privát Docker-beállításjegyzék kapacitás-és használati mintáinak összehangolásához.

| Szint | Leírás |
| --- | ----------- |
| **Basic** | Költségoptimalizált belépési pont az Azure Container Registryt tanulmányozó fejlesztőknek. Az alapszintű beállításjegyzékek ugyanazokkal a programozási képességekkel rendelkeznek, mint a standard és a prémium (például Azure Active Directory [Authentication Integration](container-registry-authentication.md#individual-login-with-azure-ad), a [képek törlése][container-registry-delete]és a [webhookok][container-registry-webhook]). A tartalmazott tárolási és képátviteli teljesítmény azonban a legmegfelelőbb az alacsonyabb használati forgatókönyvek esetében. |
| **Standard** | A standard szintű beállításjegyzékek ugyanazokat a képességeket kínálnak, mint az alapszintű, a megnövekedett tárterület és a képátviteli teljesítmény. A Standard beállításjegyzékek a legtöbb éles forgatókönyvhöz megfelelők. |
| **Prémium** | A prémium szintű kibocsátásiegység-forgalmi jegyzékek biztosítják a legnagyobb mennyiségű foglalt tárterületet és az egyidejű műveleteket, ami lehetővé teszi a nagy mennyiségű forgatókönyvek A magasabb képátviteli teljesítmény mellett a Premium olyan funkciókat is tartalmaz, mint például a [geo-replikáció][container-registry-geo-replication] több régióban, a Képcímke-aláírásban a [tartalom megbízhatósága](container-registry-content-trust.md) , a privát [végpontokkal való privát hivatkozás](container-registry-private-link.md) a beállításjegyzékhez való hozzáférés korlátozása érdekében. |

Az alapszintű, a standard és a prémium szintű csomag mind ugyanazokat a programozási képességeket biztosítja. Emellett minden előnyt élveznek az Azure által teljes mértékben felügyelt [képtárolóból][container-registry-storage] . A magasabb szintű szint kiválasztásával nagyobb teljesítményt és méretezést biztosít. Több szolgáltatási szint esetén megkezdheti az alapszintű, majd a standard és a prémium szintű konverziót, ha a beállításjegyzék-használat növekszik.

## <a name="service-tier-features-and-limits"></a>Szolgáltatási rétegek funkciói és korlátai

A következő táblázat ismerteti az alapszintű, standard és prémium szintű szolgáltatási szintek funkcióit és beállításjegyzékbeli korlátozásait.

[!INCLUDE [container-instances-limits](../../includes/container-registry-limits.md)]

## <a name="changing-tiers"></a>Rétegek módosítása

A beállításjegyzék szolgáltatási szintje az Azure CLI-vel vagy a Azure Portal is módosítható. A rétegek között szabadon mozoghat, ha a kívánt szinten a maximális tárolókapacitás szükséges. 

A szolgáltatási szintek közötti váltás során nincs szükség a beállításjegyzék működésének leállására vagy hatására.

### <a name="azure-cli"></a>Azure CLI

Az Azure CLI szolgáltatási szintjei közötti váltáshoz használja az az [ACR Update][az-acr-update] parancsot. Ha például a prémium szintre szeretne váltani:

```azurecli
az acr update --name myregistry --sku Premium
```

### <a name="azure-portal"></a>Azure Portal

A Azure Portal tároló beállításjegyzékének **Áttekintés** területén válassza a **frissítés** lehetőséget, majd válasszon ki egy új **SKU** -t az SKU legördülő menüből.

![Tároló beállításjegyzékbeli SKU frissítése Azure Portal][update-registry-sku]

## <a name="pricing"></a>Díjszabás

A Azure Container Registry szolgáltatási szintjeinek díjszabásáról a [Container Registry díjszabása][container-registry-pricing]című témakörben olvashat.

Az adatforgalom díjszabásával kapcsolatos részletekért lásd: a [sávszélesség díjszabása](https://azure.microsoft.com/pricing/details/bandwidth/). 

## <a name="next-steps"></a>Következő lépések

**Azure Container Registry ütemterv**

Látogasson el az [ACR-ütemtervre][acr-roadmap] a githubon, ahol információt talál a szolgáltatás közelgő szolgáltatásairól.

**Azure Container Registry UserVoice**

Küldje el és szavazzon az [ACR UserVoice][container-registry-uservoice]új funkcióinak javaslataira.

<!-- IMAGES -->
[update-registry-sku]: ./media/container-registry-skus/update-registry-sku.png

<!-- LINKS - External -->
[acr-roadmap]: https://aka.ms/acr/roadmap
[container-registry-pricing]: https://azure.microsoft.com/pricing/details/container-registry/
[container-registry-uservoice]: https://feedback.azure.com/forums/903958-azure-container-registry

<!-- LINKS - Internal -->
[az-acr-update]: /cli/azure/acr#az-acr-update
[container-registry-geo-replication]: container-registry-geo-replication.md
[container-registry-storage]: container-registry-storage.md
[container-registry-delete]: container-registry-delete.md
[container-registry-webhook]: container-registry-webhook.md