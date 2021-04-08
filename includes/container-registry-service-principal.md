---
title: fájl belefoglalása
description: fájl belefoglalása
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 12/14/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: f2d2b655e80f5b9694fb1948b136aac918312ca9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "102244794"
---
## <a name="create-a-service-principal"></a>Egyszerű szolgáltatás létrehozása

A tároló-beállításjegyzékhez hozzáféréssel rendelkező egyszerű szolgáltatásnév létrehozásához futtassa az alábbi parancsfájlt az [Azure Cloud Shell](../articles/cloud-shell/overview.md) vagy az [Azure CLI](/cli/azure/install-azure-cli)helyi telepítésekor. A parancsfájl a bash-rendszerhéjhoz van formázva.

A szkript futtatása előtt frissítse a `ACR_NAME` változót a tároló-beállításjegyzék nevével. Az `SERVICE_PRINCIPAL_NAME` értéknek egyedinek kell lennie a Azure Active Directory bérlőn belül. Ha " `'http://acr-service-principal' already exists.` " hibaüzenetet kap, adjon meg egy másik nevet az egyszerű szolgáltatásnév számára.

Igény szerint módosíthatja az az `--role` [ad SP Create-for-RBAC][az-ad-sp-create-for-rbac] parancs értékét, ha más engedélyeket szeretne megadni. A szerepkörök teljes listáját lásd: [ACR-szerepkörök és-engedélyek](https://github.com/Azure/acr/blob/master/docs/roles-and-permissions.md).

A parancsfájl futtatása után jegyezze fel az egyszerű szolgáltatásnév **azonosítóját** és **jelszavát**. A hitelesítő adatai megadásával beállíthatja, hogy alkalmazásai és szolgáltatásai hitelesítsék magukat a tároló-beállításjegyzékben az egyszerű szolgáltatásként.

<!-- https://github.com/Azure-Samples/azure-cli-samples/blob/master/container-registry/service-principal-create/service-principal-create.sh -->
[!code-azurecli-interactive[acr-sp-create](~/cli_scripts/container-registry/service-principal-create/service-principal-create.sh)]

### <a name="use-an-existing-service-principal"></a>Meglévő egyszerű szolgáltatás használata

Ahhoz, hogy a beállításjegyzék hozzáférjen egy meglévő egyszerű szolgáltatáshoz, hozzá kell rendelnie egy új szerepkört az egyszerű szolgáltatáshoz. Az új egyszerű szolgáltatás létrehozásához hasonlóan a lekéréses, leküldéses és lekéréses és tulajdonosi hozzáférés is biztosítható többek között.

Az alábbi szkript az az [role hozzárendelés Create][az-role-assignment-create] parancsot használja a *lekéréses* engedélyek megadására a változóban megadott egyszerű szolgáltatáshoz `SERVICE_PRINCIPAL_ID` . Állítsa be az `--role` értéket, ha egy másik hozzáférési szintet szeretne megadni.


<!-- https://github.com/Azure-Samples/azure-cli-samples/blob/master/container-registry/service-principal-assign-role/service-principal-assign-role.sh -->
[!code-azurecli-interactive[acr-sp-role-assign](~/cli_scripts/container-registry/service-principal-assign-role/service-principal-assign-role.sh)]

<!-- LINKS - Internal -->
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
