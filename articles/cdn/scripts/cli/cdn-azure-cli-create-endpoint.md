---
title: Azure Content Delivery Network-(CDN-) profil és végpont létrehozása az Azure CLI használatával
description: Azure CLI-szkriptek Azure CDN profil, végpont, Origó, forrás és egyéni tartomány létrehozásához.
author: asudbring
ms.author: allensu
manager: danielgi
ms.date: 03/09/2021
ms.topic: sample
ms.service: azure-cdn
ms.devlang: azurecli
ms.custom: devx-track-azurecli
ms.openlocfilehash: ce01c1f851a5dbaedc85d848b12bf0b0831a16ef
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104726869"
---
# <a name="create-an-azure-cdn-profile-and-endpoint-using-the-azure-cli"></a>Azure CDN-profil és-végpont létrehozása az Azure CLI használatával

A Azure Portal alternatívájaként használhatja ezeket az Azure CLI-parancsfájlokat a következő CDN-műveletek kezeléséhez:

- Hozzon létre egy CDN-profilt.
- CDN-végpont létrehozása.
- Hozzon létre egy CDN-eredetű csoportot, és végezze el az alapértelmezett csoportot.
- Hozzon létre egy CDN-forrást.
- Hozzon létre egy egyéni tartományt, és engedélyezze a HTTPS-t.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="sample-scripts"></a>Mintaparancsfájlok

Ha még nem rendelkezik erőforráscsoporthoz a CDN-profilhoz, hozza létre a következő paranccsal `az group create` :

```azurecli
# Create a resource group to use for the CDN.
az group create --name MyResourceGroup --location eastus

```

A következő Azure CLI-szkript létrehoz egy CDN-profilt és egy CDN-végpontot:

```azurecli
# Create a CDN profile.
az cdn profile create --resource-group MyResourceGroup --name MyCDNProfile --sku Standard_Microsoft

# Create a CDN endpoint.
az cdn endpoint create --resource-group MyResourceGroup --name MyCDNEndpoint --profile-name MyCDNProfile --origin www.contoso.com

```

A következő Azure CLI-szkript létrehoz egy CDN-alapú csoportot, beállítja egy végpont alapértelmezett kezdőpontját, és létrehoz egy új forrást:

```azurecli
# Create an origin group.
az cdn origin-group create --resource-group MyResourceGroup --endpoint-name MyCDNEndpoint --profile-name MyCDNProfile --name MyOriginGroup --origins origin-0

# Make the origin group the default group of an endpoint.
az cdn endpoint update --resource-group MyResourceGroup --name MyCDNEndpoint --profile-name MyCDNProfile --default-origin-group MyOriginGroup
                           
# Create another origin for an endpoint.
az cdn origin create --resource-group MyResourceGroup --endpoint-name MyCDNEndpoint --profile-name MyCDNProfile --name origin-1 --host-name example.contoso.com

```

A következő Azure CLI-szkript létrehoz egy CDN egyéni tartományt, és engedélyezi a HTTPS-t. Ahhoz, hogy egy Azure CDN-végponthoz társítson egy egyéni tartományt, először létre kell hoznia egy kanonikus név (CNAME) rekordot, amelynek Azure DNS vagy a DNS-szolgáltatója a CDN-végpontra mutat. További információ: [CNAME DNS-rekord létrehozása](../../../cdn/cdn-map-content-to-custom-domain.md#create-a-cname-dns-record).

```azurecli
# Associate a custom domain with an endpoint.
az cdn custom-domain create --resource-group MyResourceGroup --endpoint-name MyCDNEndpoint --profile-name MyCDNProfile --name MyCustomDomain --hostname www.example.com

# Enable HTTPS on the custom domain.
az cdn custom-domain enable-https --resource-group MyResourceGroup --endpoint-name MyCDNEndpoint --profile-name MyCDNProfile --name MyCustomDomain

```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A minta parancsfájlok futtatásának befejezése után a következő paranccsal távolíthatja el az erőforráscsoportot és az ahhoz társított összes erőforrást.

```azurecli
# Delete the resource group.
az group delete --name MyResourceGroup

```

## <a name="azure-cli-commands-used-in-this-article"></a>A cikkben használt Azure CLI-parancsok

- [az CDN Endpoint Create](/cli/azure/cdn/endpoint#az_cdn_endpoint_create)
- [az CDN Endpoint Update](/cli/azure/cdn/endpoint#az_cdn_endpoint_update)
- [az CDN Origin Create](/cli/azure/cdn/origin#az_cdn_origin_create)
- [az CDN Origin-Group Create](/cli/azure/cdn/origin-group#az_cdn_origin_group_create)
- [az CDN Profile Create](/cli/azure/cdn/profile#az_cdn_profile_create)
- [az group create](/cli/azure/group#az_group_create)
- [az group delete](/cli/azure/group#az_group_delete)
- [az CDN Custom-domain Create](/cli/azure/cdn/custom-domain#az_cdn_custom_domain_create)
- [az CDN Custom-domain Enable-https](/cli/azure/cdn/custom-domain#az_cdn_custom_domain_enable_https)
