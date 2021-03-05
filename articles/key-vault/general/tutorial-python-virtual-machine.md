---
title: Oktatóanyag – Azure Key Vault használata virtuális géppel a Pythonban | Microsoft Docs
description: Ebben az oktatóanyagban egy Python-alkalmazással konfigurál egy virtuális gépet, hogy beolvassa a titkos kulcsot a kulcstartóból.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 07/20/2020
ms.author: mbaldwin
ms.custom: mvc, devx-track-python, devx-track-azurecli
ms.openlocfilehash: 2fc77d0cdfb6bd8a62555951c0b6dc7e9b732f93
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2021
ms.locfileid: "102203538"
---
# <a name="tutorial-use-azure-key-vault-with-a-virtual-machine-in-python"></a>Oktatóanyag: Azure Key Vault használata virtuális géppel a Pythonban

A Azure Key Vault segítségével megvédheti a kulcsokat, a titkokat és a tanúsítványokat, például az API-kulcsokat és az adatbázis-kapcsolódási karakterláncokat.

Ebben az oktatóanyagban egy Python-alkalmazást állít be, amely az Azure-erőforrások felügyelt identitások használatával olvassa el Azure Key Vault adatokat. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Kulcstartó létrehozása
> * Titkos kód tárolása Key Vault
> * Azure Linux rendszerű virtuális gép létrehozása
> * [Felügyelt identitás](../../active-directory/managed-identities-azure-resources/overview.md) engedélyezése a virtuális géphez
> * Adja meg a szükséges engedélyeket a konzol alkalmazás számára az adatok olvasásához Key Vault
> * Titkos kód beolvasása Key Vault

Mielőtt elkezdené, olvassa el [Key Vault alapvető fogalmakat](basic-concepts.md). 

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Előfeltételek

Windows, Mac és Linux rendszerekhez:
  * [Git](https://git-scm.com/downloads)
  * Ehhez az oktatóanyaghoz helyileg kell futtatnia az Azure CLI-t. Telepítenie kell az Azure CLI 2.0.4 vagy újabb verzióját. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretné a parancssori felületet: [Az Azure CLI 2.0 telepítése](/cli/azure/install-azure-cli).

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Ha az Azure-ba az Azure CLI használatával szeretne bejelentkezni, írja be a következőt:

```azurecli
az login
```

## <a name="create-a-resource-group-and-key-vault"></a>Erőforráscsoport és kulcstartó létrehozása

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

## <a name="populate-your-key-vault-with-a-secret"></a>A Key Vault feltöltése titkos kulccsal

[!INCLUDE [Create a secret](../../../includes/key-vault-create-secret.md)]

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

Hozzon létre egy **myVM** nevű virtuális gépet az alábbi módszerek egyikének használatával:

| Linux | Windows |
|--|--|
| [Azure CLI](../../virtual-machines/linux/quick-create-cli.md) | [Azure CLI](../../virtual-machines/windows/quick-create-cli.md) |
| [PowerShell](../../virtual-machines/linux/quick-create-powershell.md) | [PowerShell](../../virtual-machines/windows/quick-create-powershell.md) |
| [Azure Portal](../../virtual-machines/linux/quick-create-portal.md) | [Az Azure Portal](../../virtual-machines/windows/quick-create-portal.md) |

Linuxos virtuális gép Azure CLI használatával történő létrehozásához használja az az [VM Create](/cli/azure/vm) parancsot.  A következő példa egy *azureuser* nevű felhasználói fiókot vesz fel. A `--generate-ssh-keys` paraméter használatával automatikusan létrejön egy SSH-kulcs, és elhelyezhető az alapértelmezett kulcs helyén (*~/.ssh*). 

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

Jegyezze `publicIpAddress` fel a kimenetben lévő értéket.

## <a name="assign-an-identity-to-the-vm"></a>Identitás kiosztása a virtuális géphez

Hozzon létre egy rendszer által hozzárendelt identitást a virtuális géphez az Azure CLI az [VM Identity assign](/cli/azure/vm/identity#az-vm-identity-assign) parancs használatával:

```azurecli
az vm identity assign --name "myVM" --resource-group "myResourceGroup"
```

Jegyezze fel a rendszer által hozzárendelt identitást, amely a következő kódban látható. Az előző parancs kimenete a következő lesz: 

```output
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

## <a name="assign-permissions-to-the-vm-identity"></a>Engedélyek kiosztása a virtuális gép identitásához

Most a következő parancs futtatásával rendelheti hozzá a korábban létrehozott identitási engedélyeket a kulcstartóhoz:

```azurecli
az keyvault set-policy --name "<your-unique-keyvault-name>" --object-id "<systemAssignedIdentity>" --secret-permissions get list
```

## <a name="log-in-to-the-vm"></a>Bejelentkezés a virtuális gépre

A virtuális gépre való bejelentkezéshez kövesse a [Kapcsolódás, majd jelentkezzen be egy Linux rendszerű Azure-beli virtuális gépre](../../virtual-machines/linux/login-using-aad.md) , [és](../../virtual-machines/windows/connect-logon.md)jelentkezzen be egy Windows rendszerű Azure-beli virtuális gépre.


Linux rendszerű virtuális gépre való bejelentkezéshez használhatja az SSH-parancsot a <publicIpAddress> [virtuális gép létrehozása](#create-a-virtual-machine) lépésben megadott "" értékkel:

```terminal
ssh azureuser@<PublicIpAddress>
```

## <a name="install-python-libraries-on-the-vm"></a>Python-kódtárak telepítése a virtuális gépen

A virtuális gépen telepítse a Python-szkriptben használt két Python-kódtárat: `azure-keyvault-secrets` és `azure.identity` .  

Linux rendszerű virtuális gépen például a következő paranccsal telepítheti ezeket `pip3` :

```bash
pip3 install azure-keyvault-secrets

pip3 install azure.identity
```

## <a name="create-and-edit-the-sample-python-script"></a>A minta Python-szkript létrehozása és szerkesztése

A virtuális gépen hozzon létre egy **sample.py** nevű Python-fájlt. Szerkessze a fájlt, hogy tartalmazza a következő kódot, majd cserélje le a "<a-Unique-kulcstartó-Name>" kifejezést a Key Vault nevével:

```python
from azure.keyvault.secrets import SecretClient
from azure.identity import DefaultAzureCredential

keyVaultName = "<your-unique-keyvault-name>"
KVUri = f"https://{keyVaultName}.vault.azure.net"
secretName = "mySecret"

credential = DefaultAzureCredential()
client = SecretClient(vault_url=KVUri, credential=credential)
retrieved_secret = client.get_secret(secretName)

print(f"The value of secret '{secretName}' in '{keyVaultName}' is: '{retrieved_secret.value}'")
```

## <a name="run-the-sample-python-app"></a>A Python-alkalmazás mintájának futtatása

Végül futtassa a **sample.py**. Ha minden jól ment, vissza kell adni a titkos kulcs értékét:

```bash
python3 sample.py

The value of secret 'mySecret' in '<your-unique-keyvault-name>' is: 'Success!'
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rájuk szükség, törölje a virtuális gépet és a kulcstartót.  Ezt gyorsan elvégezheti, ha egyszerűen törli azt az erőforráscsoportot, amelyhez tartoznak:

```azurecli
az group delete -g myResourceGroup
```

## <a name="next-steps"></a>Következő lépések

[Azure Key Vault REST API](/rest/api/keyvault/)