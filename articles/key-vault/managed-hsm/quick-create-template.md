---
title: Azure rövid útmutató – Felügyelt HSM létrehozása Azure Resource Manager sablonnal
description: Rövid útmutató, amely bemutatja, hogyan hozhat létre felügyelt HSM Azure Key Vault Azure-beli Resource Manager sablon használatával
services: key-vault
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/15/2020
ms.topic: quickstart
ms.service: key-vault
ms.subservice: managed-hsm
tags:
- azure-resource-manager
ms.custom:
- mvc
- devx-track-azurecli
- mode-arm
ms.openlocfilehash: 33c262c61d50b45663a627e40ea186f1f0dcde41
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532972"
---
# <a name="quickstart-create-an-key-vault-managed-hsm-using-an-azure-resource-manager-template"></a>Rövid útmutató: Felügyelt HSM Key Vault létrehozása Azure Resource Manager sablonnal

A Managed HSM egy teljes körűen felügyelt, magas rendelkezésre állású, egybérlős, szabványnak megfelelő felhőszolgáltatás, amellyel a **FIPS 140-2 3.** szintje szerint ellenőrzött HSM-ekkel védheti a felhőalkalmazások titkosítási kulcsait.  

Ez a rövid útmutató a felügyelt HSM létrehozásához Resource Manager folyamatra összpontosít.  A [Resource Manager-sablon](../../azure-resource-manager/templates/overview.md) egy JavaScript Object Notation- (JSON-) fájl, amely meghatározza a projekt infrastruktúráját és konfigurációját. A sablon olyan deklaratív szintaxist használ, amellyel anélkül határozhatja meg, hogy mit szeretne üzembe helyezni, hogy ehhez programozási parancsok sorozatát kellene megírnia. A Resource Manager-sablonokról további információért tekintse meg a [Resource Manager dokumentációját](../../azure-resource-manager/index.yml) és a [sablonreferenciát](/azure/templates/microsoft.keyvault/allversions).

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Előfeltételek

A cikkben található lépések befejezéséhez a következő elemekre lesz szükség:

- Egy Microsoft Azure-előfizetésre. Ha még nincs fiókja, regisztráljon egy [ingyenes próbaverzióra](https://azure.microsoft.com/pricing/free-trial).
- Az Azure CLI 2.12.0-s vagy újabb verziója. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissítenie kell, tekintse meg az [Azure CLI telepítését]( /cli/azure/install-azure-cli)


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Az Azure-ba a cli használatával való bejelentkezéshez írja be a következőt:

```azurecli
az login
```

A CLI-n keresztüli bejelentkezési lehetőségekkel kapcsolatos további információkért lásd: [Bejelentkezés az Azure CLI-val](/cli/azure/authenticate-azure-cli)

## <a name="create-a-manage-hsm"></a>Manage HSM létrehozása

Az ebben a gyorsútmutatóban használt sablon az [Azure gyorsindítási sablontárból](https://azure.microsoft.com/resources/templates/101-managed-hsm-create/) származik.

A sablonban meghatározott Azure-erőforrás:

* **Microsoft.KeyVault/managedHSMs:** hozzon létre Azure Key Vault HSM-et.

További Azure Key Vault itt talál. [](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Keyvault)

A sablonhoz szükség van a fiókjához társított objektumazonosítóra. A kereséshez használja az Azure CLI [az ad user show parancsot,](/cli/azure/ad/user#az_ad_user_show) és továbbadja az e-mail-címét a `--id` paraméternek. A kimenetet csak a paraméterrel korlátozhatja az `--query` objektumazonosítóra.

```azurecli-interactive
az ad user show --id <your-email-address> --query "objectId"
```

Szüksége lehet a bérlőazonosítójára is. A kereséshez használja az Azure CLI [az ad user show](/cli/azure/account#az_account_show) parancsot. A kimenetet csak a paraméterrel korlátozhatja a `--query` bérlőazonosítóra.

 ```azurecli-interactive
 az account show --query "tenantId"
 ```

1. Kattintson az alábbi gombra az Azure-ba való bejelentkezéshez és egy sablon megnyitásához. A sablon létrehoz egy kulcstartót és egy titkos kulcsot.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-managed-hsm-create%2Fazuredeploy.json"><img src="../media/deploy-to-azure.svg" alt="deploy to azure"/></a>

2. Válassza ki vagy adja meg a következő értékeket.

    Ha nincs megadva, használja az alapértelmezett értéket a kulcstartó és egy titkos kulcs létrehozásához.

    - **Előfizetés:** Válasszon ki egy Azure-előfizetést.
    - **Erőforráscsoport:** Válassza az **Új létrehozása** lehetőséget, adjon egyedi nevet az erőforráscsoportnak, majd kattintson az **OK gombra.**
    - **Hely:** Válasszon ki egy helyet. Például: USA **déli középső középső országa.**
    - **managedHSMName:** Adja meg a felügyelt HSM nevét.
    - **Bérlőazonosító:** A sablonfunkció automatikusan lekéri a bérlőazonosítót; ne módosítsa az alapértelmezett értéket.  Ha nincs érték, adja meg az Előfeltételek mezőben lekért [bérlőazonosítót.](#prerequisites)
    * **initialAdminObjectIds:** Adja meg az Előfeltételek szakaszban lekért [objektumazonosítót.](#prerequisites)

3. Válassza a **Beszerzés** lehetőséget. A kulcstartó sikeres üzembe helyezése után értesítést kap:

Az Azure Portalon helyezhető üzembe a sablon. A Azure Portal mellett az Azure PowerShell, az Azure CLI és a REST API. További információ az egyéb üzembe helyezési módszerekről: [Sablonok üzembe helyezése.](../../azure-resource-manager/templates/deploy-powershell.md)

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban egy felügyelt HSM-et hozott létre. Ez a felügyelt HSM nem lesz teljesen működőképes, amíg nincs aktiválva. A [HSM aktiválását](quick-create-cli.md#activate-your-managed-hsm) lásd: A felügyelt HSM aktiválása.

- A Managed [HSM áttekintése](overview.md)
- Tudnivalók a [kulcsok felügyelt HSM-ben való kezeléséről](key-management.md)
- Tekintse át [a Managed HSM ajánlott eljárásait](best-practices.md)
