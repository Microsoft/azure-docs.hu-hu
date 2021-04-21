---
title: Beállításjegyzék szolgáltatási rétegei és funkciói
description: A szolgáltatások és korlátozások (kvóták) megismerése az alapszintű, standard és prémium szolgáltatási szintek (SKU-k) Azure Container Registry.
ms.topic: article
ms.date: 05/18/2020
ms.openlocfilehash: 323d36fe022d8b8e9618b8beb1facae93d22df4e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781252"
---
# <a name="azure-container-registry-service-tiers"></a>Azure Container Registry szolgáltatási szintek

Azure Container Registry szolgáltatásszinten (más néven SKUS-ban) érhető el. Ezek a szintek kiszámítható díjszabást és számos lehetőséget biztosítanak az Azure-beli privát Docker-regisztrációs adatbázis kapacitási és használati mintáihoz való igazításhoz.

| Szint | Description |
| --- | ----------- |
| **Basic** | Költségoptimalizált belépési pont az Azure Container Registryt tanulmányozó fejlesztőknek. Az alapszintű beállítástárak ugyanolyan szoftveres képességekkel rendelkeznek, mint a Standard és a Prémium (például a [Azure Active Directory-hitelesítés](container-registry-authentication.md#individual-login-with-azure-ad) [integrációja,][container-registry-delete]a rendszerkép törlése és [a webhookok).][container-registry-webhook] A benne foglalt tárolási és képi átviteli sebesség azonban az alacsonyabb használati forgatókönyvekhez a legmegfelelőbb. |
| **Standard** | A standard beállításregisztrálók ugyanazon képességeket kínálják, mint az Alapszintű, és megnövelt tárterületet és képátvitelt biztosítanak. A Standard beállításjegyzékek a legtöbb éles forgatókönyvhöz megfelelők. |
| **Prémium** | A prémium beállításregisztrálók biztosítják a legnagyobb mennyiségű tárolási és egyidejű műveletet, ami nagy mennyiségű forgatókönyvet biztosít. A magasabb képátvitel mellett a Premium [][container-registry-geo-replication] olyan funkciókat is biztosít, mint például a georeplikáció egyetlen beállításjegyzék több régióban való kezeléséhez, [](container-registry-content-trust.md) a tartalommegbűnítés a rendszerképcímkék aláírásához, [](container-registry-private-link.md) privát végpontokkal való privát kapcsolat a beállításjegyzékhez való hozzáférés korlátozása érdekében. |

Az Alapszintű, Standard és Prémium szintek mind ugyanazt a szoftveres képességet biztosítják. Emellett a teljes mértékben az Azure által felügyelt [rendszerképtárolást][container-registry-storage] is kihasználják. A magasabb szintű szint kiválasztása nagyobb teljesítményt és méretezést biztosít. Több szolgáltatási szint esetén elkezdheti az Alapszintű használatát, majd a beállításjegyzék használatának növekedésével átválthat Standard és Prémium szintre.

## <a name="service-tier-features-and-limits"></a>Szolgáltatási szint funkciói és korlátai

Az alábbi táblázat az Alapszintű, Standard és Prémium szolgáltatási szintek funkcióit és beállításjegyzékbeli korlátait részletezi.

[!INCLUDE [container-instances-limits](../../includes/container-registry-limits.md)]

## <a name="changing-tiers"></a>Szintek módosítása

A regisztrációs adatbázis szolgáltatási rétegét az Azure CLI-val vagy a felhőben módosíthatja Azure Portal. Szabadon válthat a szintek között, ha az átváltott szint rendelkezik a szükséges maximális tárkapacitással. 

A szolgáltatásszintek közötti áthelyezés során nincs leállás a beállításjegyzékben, és nincs hatással a beállításjegyzék műveleteire.

### <a name="azure-cli"></a>Azure CLI

Az Azure CLI-beli szolgáltatási szintek közötti áthelyezéshez használja az [az acr update][az-acr-update] parancsot. Például a Premiumra való váltáshoz:

```azurecli
az acr update --name myregistry --sku Premium
```

### <a name="azure-portal"></a>Azure Portal

A tároló-beállításjegyzék **Áttekintés** Azure Portal válassza a Frissítés lehetőséget, majd válasszon egy új **termékváltozatot** a Termékváltozat legördülő menüből.

![A tároló-beállításjegyzék termékváltozatának frissítése a Azure Portal][update-registry-sku]

## <a name="pricing"></a>Díjszabás

Az egyes szolgáltatásszintekkel kapcsolatos díjszabási Azure Container Registry lásd: Container Registry [díjszabása.][container-registry-pricing]

Az adatátvitel díjszabásának részleteiért lásd: [Bandwidth Pricing Details (Sávszélesség díjszabásának részletei).](https://azure.microsoft.com/pricing/details/bandwidth/) 

## <a name="next-steps"></a>Következő lépések

**Azure Container Registry ütemterv**

A GitHub [ACR roadmap (ACR-ütemterv][acr-roadmap] a GitHubon) oldalon további információt talál a szolgáltatás jövőbeli funkcióiról.

**Azure Container Registry UserVoice**

Küldje el az új funkciókra vonatkozó javaslatait az [ACR UserVoice-ban, és szavazzon rá.][container-registry-uservoice]

<!-- IMAGES -->
[update-registry-sku]: ./media/container-registry-skus/update-registry-sku.png

<!-- LINKS - External -->
[acr-roadmap]: https://aka.ms/acr/roadmap
[container-registry-pricing]: https://azure.microsoft.com/pricing/details/container-registry/
[container-registry-uservoice]: https://feedback.azure.com/forums/903958-azure-container-registry

<!-- LINKS - Internal -->
[az-acr-update]: /cli/azure/acr#az_acr_update
[container-registry-geo-replication]: container-registry-geo-replication.md
[container-registry-storage]: container-registry-storage.md
[container-registry-delete]: container-registry-delete.md
[container-registry-webhook]: container-registry-webhook.md