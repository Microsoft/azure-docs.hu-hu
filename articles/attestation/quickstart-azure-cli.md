---
title: Az Azure-igazolás beállítása az Azure CLI-vel
description: Igazolási szolgáltató beállítása és konfigurálása az Azure CLI használatával.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: quickstart
ms.date: 11/20/2020
ms.author: mbaldwin
ms.openlocfilehash: ae283785b4d4dc80c6b9b6c3997aaf82c9ff0f2f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102178711"
---
# <a name="quickstart-set-up-azure-attestation-with-azure-cli"></a>Gyors útmutató: Azure-igazolás beállítása az Azure CLI-vel

Az Azure- [igazolások használatának első lépései az Azure CLI használatával](/cli/azure/ext/attestation/attestation).

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="get-started"></a>Bevezetés

1. A bővítmény telepítése az alábbi CLI-parancs használatával

   ```azurecli
   az extension add --name attestation
   ```
   
1. A verzió keresése

   ```azurecli
   az extension show --name attestation --query version
   ```

1. Az Azure-ba való bejelentkezéshez használja a következő parancsot:

   ```azurecli
   az login
   ```

1. Ha szükséges, váltson az Azure-tanúsítványra vonatkozó előfizetésre:

   ```azurecli
   az account set --subscription 00000000-0000-0000-0000-000000000000
   ```

1. Regisztrálja a Microsoft. tanúsító erőforrás-szolgáltatót az előfizetésben az az [Provider Register](/cli/azure/provider#az_provider_register) paranccsal:

   ```azurecli
   az provider register --name Microsoft.Attestation
   ```

   Az Azure erőforrás-szolgáltatókkal és azok konfigurálásával és kezelésével kapcsolatos további információkért lásd: [Azure Resource Providers és types](../azure-resource-manager/management/resource-providers-and-types.md).

   > [!NOTE]
   > Csak egyszer kell regisztrálnia egy erőforrás-szolgáltatót az előfizetéshez.

1. Hozzon létre egy erőforráscsoportot az igazolási szolgáltatóhoz. A többi Azure-erőforrást ugyanabban az erőforráscsoporthoz helyezheti el, beleértve az ügyfélalkalmazás-példánnyal rendelkező virtuális gépeket is. Futtassa az az [Group Create](/cli/azure/group#az_group_create) parancsot egy erőforráscsoport létrehozásához, vagy használjon egy meglévő erőforráscsoportot:

   ```azurecli
   az group create --name attestationrg --location uksouth
   ```

## <a name="create-and-manage-an-attestation-provider"></a>Igazolási szolgáltató létrehozása és kezelése

Az alábbi parancsok segítségével hozhatja létre és kezelheti az igazolási szolgáltatót:

1. Futtassa az az [igazolás létrehozása](/cli/azure/ext/attestation/attestation#ext_attestation_az_attestation_create) parancsot egy igazolási szolgáltató létrehozásához házirend-aláírási követelmény nélkül:

   ```azurecli
   az attestation create --name "myattestationprovider" --resource-group "MyResourceGroup" --location westus
   ```
   
1. Futtassa az az [igazolás megjelenítése](/cli/azure/ext/attestation/attestation#ext_attestation_az_attestation_show) parancsot az igazolási szolgáltató tulajdonságainak, például az állapot-és AttestURI lekéréséhez:

   ```azurecli
   az attestation show --name "myattestationprovider" --resource-group "MyResourceGroup"
   ```

   Ez a parancs az alábbi kimenethez hasonló értékeket jelenít meg:

   ```output
   Id:/subscriptions/MySubscriptionID/resourceGroups/MyResourceGroup/providers/Microsoft.Attestation/attestationProviders/MyAttestationProvider
   Location: MyLocation
   ResourceGroupName: MyResourceGroup
   Name: MyAttestationProvider
   Status: Ready
   TrustModel: AAD
   AttestUri: https://MyAttestationProvider.us.attest.azure.net
   Tags:
   TagsTable:
   ```

A tanúsítvány-szolgáltató törléséhez használja az az [igazolás törlése](/cli/azure/ext/attestation/attestation#ext_attestation_az_attestation_delete) parancsot:

```azurecli
az attestation delete --name "myattestationprovider" --resource-group "sample-resource-group"
```

## <a name="policy-management"></a>Szabályzatkezelés

Az itt ismertetett parancsokkal biztosíthatja a házirendek kezelését egy igazolási szolgáltatóhoz, egyszerre egy igazolási típust.

Az az [igazolási házirend megjelenítése](/cli/azure/ext/attestation/attestation/policy#ext_attestation_az_attestation_policy_show) parancs a megadott Tee aktuális szabályzatát adja vissza:

```azurecli
az attestation policy show --name "myattestationprovider" --resource-group "MyResourceGroup" --attestation-type SGX-IntelSDK
```

> [!NOTE]
> A parancs szöveg-és JWT formátumban jeleníti meg a szabályzatot.

A következők a támogatott TEE-típusok:

- `SGX-IntelSDK`
- `SGX-OpenEnclaveSDK`
- `TPM`

Az az [igazolási szabályzat beállítása](/cli/azure/ext/attestation/attestation/policy#ext_attestation_az_attestation_policy_set) paranccsal állítson be egy új házirendet a megadott igazolási típushoz.

Házirend beállítása szöveges formátumban az adott típusú igazolási típushoz a fájl elérési útja használatával:

```azurecli
az attestation policy set --name testatt1 --resource-group testrg --attestation-type SGX-IntelSDK --new-attestation-policy-file "{file_path}"
```

Házirend beállítása JWT formátumban az adott típusú igazolási típushoz a fájl elérési útja használatával:

```azurecli
az attestation policy set --name "myattestationprovider" --resource-group "MyResourceGroup" \
--attestation-type SGX-IntelSDK -f "{file_path}" --policy-format JWT
```

## <a name="next-steps"></a>Következő lépések

- [Igazolási szabályzat létrehozása és aláírása](author-sign-policy.md)
- [Az igazolás megvalósítása SGX ENKLÁVÉHOZ enklávéban, kód minták használatával](/samples/browse/?expanded=azure&terms=attestation)
