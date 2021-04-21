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
ms.openlocfilehash: 592f62eaf1627733f8cf20460b57e3f474f17963
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107799875"
---
## <a name="create-a-service-principal"></a>Egyszerű szolgáltatás létrehozása

A tároló-beállításjegyzékhez hozzáféréssel bíró szolgáltatásnév létrehozásához [](../articles/cloud-shell/overview.md) futtassa a következő szkriptet a Azure Cloud Shell vagy az Azure CLI helyi [telepítésében.](/cli/azure/install-azure-cli) A szkript a Bash-rendszerhéjhoz van formázva.

A szkript futtatása előtt frissítse a `ACR_NAME` változót a tároló-beállításjegyzék nevére. Az `SERVICE_PRINCIPAL_NAME` értéknek egyedinek kell lennie a Azure Active Directory bérlőn belül. Ha "" hibaüzenetet kap, adjon meg egy másik nevet `'http://acr-service-principal' already exists.` a szolgáltatásnévnek.

Ha más engedélyeket szeretne megadni, módosíthatja az `--role` [az ad sp create-for-rbac parancs][az-ad-sp-create-for-rbac] értékét. A szerepkörök teljes listájáért lásd: [ACR-szerepkörök és -engedélyek.](https://github.com/Azure/acr/blob/master/docs/roles-and-permissions.md)

A szkript futtatása után jegyezze fel a szolgáltatásnév azonosítóját **és** **jelszavát.** Ha már megvan a hitelesítő adatai, konfigurálhatja az alkalmazásokat és szolgáltatásokat, hogy szolgáltatásnévként hitelesítsék magukat a tároló-beállításjegyzékben.

<!-- https://github.com/Azure-Samples/azure-cli-samples/blob/master/container-registry/service-principal-create/service-principal-create.sh -->
[!code-azurecli-interactive[acr-sp-create](~/cli_scripts/container-registry/service-principal-create/service-principal-create.sh)]

### <a name="use-an-existing-service-principal"></a>Meglévő szolgáltatásnév használata

Egy meglévő szolgáltatásnévhez való beállításjegyzék-hozzáférés megadásához új szerepkört kell hozzárendelnie a szolgáltatásnévhez. Az új szolgáltatásnév létrehozásához a leküldéses, leküldéses és leküldéses, valamint tulajdonosi hozzáférést is biztosíthatja, többek között.

A következő szkript az az role  assignment [create][az-role-assignment-create] parancsot használja a változóban megadott szolgáltatásnév le pull-engedélyeinek `SERVICE_PRINCIPAL_ID` megadásához. Módosítsa az értéket, ha más hozzáférési szintet `--role` szeretne beállítani.


<!-- https://github.com/Azure-Samples/azure-cli-samples/blob/master/container-registry/service-principal-assign-role/service-principal-assign-role.sh -->
[!code-azurecli-interactive[acr-sp-role-assign](~/cli_scripts/container-registry/service-principal-assign-role/service-principal-assign-role.sh)]

<!-- LINKS - Internal -->
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
