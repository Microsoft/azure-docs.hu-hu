---
title: Oktatóanyag – Azure Key Vault használata virtuális géppel Python-| Microsoft Docs
description: Ebben az oktatóanyagban egy Python-alkalmazást fog konfigurálni egy virtuális géphez, hogy beolvasson egy titkos kulcsot a kulcstartóból.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 07/20/2020
ms.author: mbaldwin
ms.custom: mvc, devx-track-python, devx-track-azurecli
ms.openlocfilehash: 25182105db831724565c6bf3dbbbb79832b677f7
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772059"
---
# <a name="tutorial-use-azure-key-vault-with-a-virtual-machine-in-python"></a>Oktatóanyag: Azure Key Vault használata virtuális géppel Pythonban

Azure Key Vault segítségével megvédheti a kulcsokat, titkos kulcsokat és tanúsítványokat, például AZ API-kulcsokat és az adatbázis-kapcsolati sztringeket.

Ebben az oktatóanyagban egy Python-alkalmazást fog beállítani, amely beolvassa az információkat a Azure Key Vault Azure-erőforrások felügyelt identitásával. Az alábbiak végrehajtásának módját ismerheti meg:

> [!div class="checklist"]
> * Kulcstartó létrehozása
> * Titkos adat tárolása a Key Vault
> * Azure Linux rendszerű virtuális gép létrehozása
> * Felügyelt [identitás engedélyezése a](../../active-directory/managed-identities-azure-resources/overview.md) virtuális géphez
> * Adja meg a szükséges engedélyeket a konzolalkalmazás számára, hogy adatokat olvasson Key Vault
> * Titkos adat lekérése a Key Vault

Mielőtt hozzákezd, olvassa [el Key Vault alapfogalmakat.](basic-concepts.md) 

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Előfeltételek

Windows, Mac és Linux esetén:
  * [Git](https://git-scm.com/downloads)
  * Ehhez az oktatóanyaghoz az Azure CLI helyi futtatására van szükség. Az Azure CLI 2.0.4-es vagy újabb verziójával kell lennie. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretné a parancssori felületet: [Az Azure CLI 2.0 telepítése](/cli/azure/install-azure-cli).

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Ha az Azure-ba az Azure CLI használatával szeretne bejelentkezni, írja be a következőt:

```azurecli
az login
```

## <a name="create-a-resource-group-and-key-vault"></a>Erőforráscsoport és kulcstartó létrehozása

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

## <a name="populate-your-key-vault-with-a-secret"></a>Kulcstartó feltöltése titkos kulccsal

[!INCLUDE [Create a secret](../../../includes/key-vault-create-secret.md)]

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

Hozzon létre egy **myVM nevű virtuális** gépet az alábbi módszerek egyikével:

| Linux | Windows |
|--|--|
| [Azure CLI](../../virtual-machines/linux/quick-create-cli.md) | [Azure CLI](../../virtual-machines/windows/quick-create-cli.md) |
| [PowerShell](../../virtual-machines/linux/quick-create-powershell.md) | [PowerShell](../../virtual-machines/windows/quick-create-powershell.md) |
| [Azure Portal](../../virtual-machines/linux/quick-create-portal.md) | [Az Azure Portal](../../virtual-machines/windows/quick-create-portal.md) |

Linux rendszerű virtuális gép Azure CLI használatával való létrehozásához használja [az az vm create](/cli/azure/vm) parancsot.  Az alábbi példa hozzáad egy *azureuser nevű felhasználói fiókot.* A paraméterrel automatikusan létrehozhat egy SSH-kulcsot, és az alapértelmezett kulcshelyre `--generate-ssh-keys` *(~/.ssh) helyezze* el. 

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

Jegyezze fel a `publicIpAddress` kimenetben a értékét.

## <a name="assign-an-identity-to-the-vm"></a>Identitás hozzárendelése a virtuális géphez

Hozzon létre egy rendszer által hozzárendelt identitást a virtuális géphez az Azure CLI [az vm identity assign paranccsal:](/cli/azure/vm/identity#az_vm_identity_assign)

```azurecli
az vm identity assign --name "myVM" --resource-group "myResourceGroup"
```

Figyelje meg a rendszer által hozzárendelt identitást, amely az alábbi kódban látható. Az előző parancs kimenete a következő lesz: 

```output
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

## <a name="assign-permissions-to-the-vm-identity"></a>Engedélyek hozzárendelése a virtuálisgép-identitáshoz

Most már hozzárendelheti a korábban létrehozott identitásengedélyeket a kulcstartóhoz a következő parancs futtatásával:

```azurecli
az keyvault set-policy --name "<your-unique-keyvault-name>" --object-id "<systemAssignedIdentity>" --secret-permissions get list
```

## <a name="log-in-to-the-vm"></a>Bejelentkezés a virtuális gépre

A virtuális gépre való bejelentkezéshez kövesse a Csatlakozás és bejelentkezés [Linux](../../virtual-machines/linux/login-using-aad.md) vagy Connect rendszert futtató Azure-beli virtuális gépre, és jelentkezzen be egy Windows rendszert futtató [Azure-beli virtuális gépre.](../../virtual-machines/windows/connect-logon.md)


A Linux rendszerű virtuális gépre való bejelentkezéshez használhatja az ssh-parancsot a virtuális gép létrehozása lépésben megadott <publicIpAddress> " gombra: [](#create-a-virtual-machine)

```terminal
ssh azureuser@<PublicIpAddress>
```

## <a name="install-python-libraries-on-the-vm"></a>Python-kódtárak telepítése a virtuális gépen

A virtuális gépen telepítse a Python-szkriptben használt két Python-kódtárat: `azure-keyvault-secrets` és `azure.identity` .  

Linux rendszerű virtuális gépen például a használatával telepítheti `pip3` ezeket:

```bash
pip3 install azure-keyvault-secrets

pip3 install azure.identity
```

## <a name="create-and-edit-the-sample-python-script"></a>A Python-példaszkent létrehozása és szerkesztése

A virtuális gépen hozzon létre egy nevű Python-sample.py. Szerkessze a fájlt úgy, hogy tartalmazza a következő kódot, és cserélje le a "<-your-unique-keyvault-name>" kódot a kulcstartó nevére:

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

## <a name="run-the-sample-python-app"></a>A Python-mintaalkalmazás futtatása

Végül futtassa a **sample.py.** Ha minden jól ment, akkor a titkos secret értékét kell visszaadni:

```bash
python3 sample.py

The value of secret 'mySecret' in '<your-unique-keyvault-name>' is: 'Success!'
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rájuk szükség, törölje a virtuális gépet és a kulcstartót.  Ehhez egyszerűen törölje azt az erőforráscsoportot, amelyhez tartoznak:

```azurecli
az group delete -g myResourceGroup
```

## <a name="next-steps"></a>Következő lépések

[Azure Key Vault REST API](/rest/api/keyvault/)