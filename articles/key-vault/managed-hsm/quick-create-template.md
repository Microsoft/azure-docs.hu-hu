---
title: Azure rövid útmutató – felügyelt HSM létrehozása Azure Resource Manager sablon használatával
description: Gyors útmutató, amely bemutatja, hogyan hozhat létre Azure-Azure Key Vault felügyelt HSM Resource Manager-sablon használatával
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: quickstart
ms.custom: mvc, devx-track-azurecli
ms.date: 09/15/2020
ms.author: mbaldwin
ms.openlocfilehash: d9fb9e0221ad6a5749899c89bbd9dc5631e7a91c
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102213266"
---
# <a name="quickstart-create-an-key-vault-managed-hsm-using-an-azure-resource-manager-template"></a>Gyors útmutató: Key Vault felügyelt HSM létrehozása Azure Resource Manager sablon használatával

A Managed HSM egy teljes körűen felügyelt, magas rendelkezésre állású, egybérlős, szabványoknak megfelelő felhőalapú szolgáltatás, amely lehetővé teszi a felhőalapú alkalmazások titkosítási kulcsainak védelmét, az **FIPS 140-2 3. szintű** hitelesített HSM használatával.  

Ez a rövid útmutató a Resource Manager-sablonok felügyelt HSM létrehozásához való üzembe helyezésének folyamatát összpontosítja.  A [Resource Manager-sablon](../../azure-resource-manager/templates/overview.md) egy JavaScript Object Notation- (JSON-) fájl, amely meghatározza a projekt infrastruktúráját és konfigurációját. A sablon olyan deklaratív szintaxist használ, amellyel anélkül határozhatja meg, hogy mit szeretne üzembe helyezni, hogy ehhez programozási parancsok sorozatát kellene megírnia. A Resource Manager-sablonokról további információért tekintse meg a [Resource Manager dokumentációját](../../azure-resource-manager/index.yml) és a [sablonreferenciát](/azure/templates/microsoft.keyvault/allversions).

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Előfeltételek

A cikkben szereplő lépések végrehajtásához a következő elemek szükségesek:

- Egy Microsoft Azure-előfizetésre. Ha még nincs fiókja, regisztráljon egy [ingyenes próbaverzióra](https://azure.microsoft.com/pricing/free-trial).
- Az Azure CLI verziója 2.12.0 vagy újabb. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne, tekintse meg [Az Azure CLI telepítését]( /cli/azure/install-azure-cli) ismertető témakört.


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Ha a parancssori felülettel szeretne bejelentkezni az Azure-ba, írja be a következőt:

```azurecli
az login
```

A parancssori felületről való bejelentkezéssel kapcsolatos további információkért lásd: [Bejelentkezés az Azure CLI](/cli/azure/authenticate-azure-cli) használatával

## <a name="create-a-manage-hsm"></a>Kezelő HSM létrehozása

Az ebben a gyorsútmutatóban használt sablon az [Azure gyorsindítási sablontárból](https://azure.microsoft.com/resources/templates/101-managed-hsm-create/) származik.

A sablonban definiált Azure-erőforrás:

* **Microsoft. kulcstartó/managedHSMs**: hozzon létre egy Azure Key Vault Managed HSM-et.

[Itt](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Keyvault)több Azure Key Vault sablon is található.

A sablonhoz a fiókhoz társított objektumazonosító szükséges. A kereséshez használja az Azure CLI az [ad User show](/cli/azure/ad/user#az_ad_user_show) parancsot, és adja át az e-mail-címét a `--id` paraméternek. A kimenetet csak a (z) paraméterrel lehet az objektumazonosító alapján korlátozni `--query` .

```azurecli-interactive
az ad user show --id <your-email-address> --query "objectId"
```

Szüksége lehet a bérlői AZONOSÍTÓra is. A kereséshez használja az Azure CLI az [ad User show](/cli/azure/account#az_account_show) parancsot. A kimenetet csak a (z) paraméterrel lehet a bérlői AZONOSÍTÓra korlátozni `--query` .

 ```azurecli-interactive
 az account show --query "tenantId"
 ```

1. Kattintson az alábbi gombra az Azure-ba való bejelentkezéshez és egy sablon megnyitásához. A sablon létrehoz egy kulcstartót és egy titkos kulcsot.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-managed-hsm-create%2Fazuredeploy.json"><img src="../media/deploy-to-azure.svg" alt="deploy to azure"/></a>

2. Válassza ki vagy adja meg a következő értékeket.

    Ha meg van adva, az alapértelmezett érték használatával hozza létre a kulcstartót és a titkos kulcsot.

    - **Előfizetés**: válasszon ki egy Azure-előfizetést.
    - **Erőforráscsoport**: válassza az **új létrehozása** lehetőséget, adjon meg egy egyedi nevet az erőforráscsoport számára, majd kattintson **az OK** gombra.
    - **Hely**: válasszon ki egy helyet. Például az **USA déli középső** régiója.
    - **managedHSMName**: adja meg a felügyelt HSM nevét.
    - **Bérlő azonosítója**: a sablon függvény automatikusan lekéri a bérlői azonosítót; Ne módosítsa az alapértelmezett értéket.  Ha nincs érték, adja meg az [Előfeltételekben](#prerequisites)lekért bérlői azonosítót.
    * **initialAdminObjectIds**: adja meg az [előfeltételekben](#prerequisites)lekért objektumazonosító-azonosítót.

3. Válassza a **Beszerzés** lehetőséget. A Key Vault sikeres üzembe helyezését követően értesítést kap:

Az Azure Portalon helyezhető üzembe a sablon. A Azure Portalon kívül használhatja a Azure PowerShell, az Azure CLI és a REST API is. További információ az üzembe helyezési módszerekről: [sablonok üzembe helyezése](../../azure-resource-manager/templates/deploy-powershell.md).

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy felügyelt HSM-et. Ez a felügyelt HSM nem lesz teljesen működőképes, amíg be nem aktiválja. A HSM aktiválásához a [felügyelt HSM](quick-create-cli.md#activate-your-managed-hsm) aktiválása című témakörben talál további információt.

- [A Managed HSM áttekintése – áttekintés](overview.md)
- Útmutató a [kulcsok felügyelt HSM-ben való kezeléséhez](key-management.md)
- A [felügyelt HSM ajánlott eljárásainak](best-practices.md) áttekintése
