---
title: Hozzáférési hitelesítő adatok biztonságos tárolása
titleSuffix: Azure Data Science Virtual Machine
description: Megtudhatja, hogyan tárolhatja biztonságosan a hozzáférési hitelesítő adatokat az adatelemzési virtuális gépen. Megtudhatja, hogyan használhatja a felügyelt szolgáltatásidentitások és az Azure Key Vault hozzáférési hitelesítő adatok tárolására.
keywords: deep learning, AI, data science tools, data science virtual machine, geospatial analytics, team data science process
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 05/08/2018
ms.openlocfilehash: 1cb0c5094d49eac5a1c8f63406a28d2927d8fa94
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477323"
---
# <a name="store-access-credentials-securely-on-an-azure-data-science-virtual-machine"></a>Hozzáférési hitelesítő adatok biztonságos tárolása egy Azure Data Science virtuális gépen

Gyakori, hogy a felhőalapú alkalmazásokban lévő kód hitelesítő adatokat tartalmaz a felhőszolgáltatások hitelesítéséhez. Hogyan kezelheti és biztonságossá ezeket a hitelesítő adatokat egy jól ismert kihívás a felhőalapú alkalmazások létrehozása. Ideális esetben a hitelesítő adatok soha nem jelenhetnek meg a fejlesztői munkaállomásokon, és nem jelentkezhetnek be a forrásellenőrzésbe.

Az [Azure-erőforrások felügyelt identitások](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview) funkció egyszerűbbé teszi a probléma megoldását azáltal, hogy az Azure-szolgáltatások automatikusan felügyelt identitást biztosít az Azure Active Directoryban (Azure AD). Ezzel az identitással bármely, Azure AD-hitelesítést támogató szolgáltatáshoz végezhet hitelesítést anélkül, hogy a hitelesítő adatokat a kódban kellene tárolnia.

A hitelesítő adatok biztonságossá tétele a Windows Installer (MSI) és az [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/), egy felügyelt Azure-szolgáltatás használatával a titkos kulcsok és a kriptográfiai kulcsok biztonságos tárolásának egyik módja. A kulcstartóak a felügyelt identitás használatával, majd az engedélyezett titkos kulcsok és a titkosítási kulcsok a key vault.

Az Azure-erőforrások és a Key Vault felügyelt identitások dokumentációja átfogó erőforrást tartalmaz a szolgáltatásokkal kapcsolatos részletes információkhoz. A cikk további ismerteti az MSI és a Key Vault alapvető használatát az adatelemzési virtuális gépen (DSVM) az Azure-erőforrások eléréséhez. 

## <a name="create-a-managed-identity-on-the-dsvm"></a>Felügyelt identitás létrehozása a DSVM-en

```azurecli-interactive
# Prerequisite: You have already created a Data Science VM in the usual way.

# Create an identity principal for the VM.
az vm assign-identity -g <Resource Group Name> -n <Name of the VM>
# Get the principal ID of the DSVM.
az resource list -n <Name of the VM> --query [*].identity.principalId --out tsv
```

## <a name="assign-key-vault-access-permissions-to-a-vm-principal"></a>Kulcstartó hozzáférési engedélyek hozzárendelése egyszerű virtuális géphez

```azurecli-interactive
# Prerequisite: You have already created an empty Key Vault resource on Azure by using the Azure portal or Azure CLI.

# Assign only get and set permissions but not the capability to list the keys.
az keyvault set-policy --object-id <Principal ID of the DSVM from previous step> --name <Key Vault Name> -g <Resource Group of Key Vault>  --secret-permissions get set
```

## <a name="access-a-secret-in-the-key-vault-from-the-dsvm"></a>Titkos kulcs elérése a kulcstárolóban a DSVM-ből

```bash
# Get the access token for the VM.
x=`curl http://localhost:50342/oauth2/token --data "resource=https://vault.azure.net" -H Metadata:true`
token=`echo $x | python -c "import sys, json; print(json.load(sys.stdin)['access_token'])"`

# Access the key vault by using the access token.
curl https://<Vault Name>.vault.azure.net/secrets/SQLPasswd?api-version=2016-10-01 -H "Authorization: Bearer $token"
```

## <a name="access-storage-keys-from-the-dsvm"></a>Tárolókulcsok elérése a DSVM-ből

```bash
# Prerequisite: You have granted your VMs MSI access to use storage account access keys based on instructions at https://docs.microsoft.com/azure/active-directory/managed-service-identity/tutorial-linux-vm-access-storage. This article describes the process in more detail.

y=`curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true`
ytoken=`echo $y | python -c "import sys, json; print(json.load(sys.stdin)['access_token'])"`
curl https://management.azure.com/subscriptions/<SubscriptionID>/resourceGroups/<ResourceGroup of Storage account>/providers/Microsoft.Storage/storageAccounts/<Storage Account Name>/listKeys?api-version=2016-12-01 --request POST -d "" -H "Authorization: Bearer $ytoken"

# Now you can access the data in the storage account from the retrieved storage account keys.
```

## <a name="access-the-key-vault-from-python"></a>A key vault elérése pythonból

```python
from azure.keyvault import KeyVaultClient
from msrestazure.azure_active_directory import MSIAuthentication

"""MSI Authentication example."""

# Get credentials.
credentials = MSIAuthentication(
    resource='https://vault.azure.net'
)

# Create a Key Vault client.
key_vault_client = KeyVaultClient(
    credentials
)

key_vault_uri = "https://<key Vault Name>.vault.azure.net/"

secret = key_vault_client.get_secret(
    key_vault_uri,  # Your key vault URL.
    # The name of your secret that already exists in the key vault.
    "SQLPasswd",
    ""              # The version of the secret; empty string for latest.
)
print("My secret value is {}".format(secret.value))
```

## <a name="access-the-key-vault-from-azure-cli"></a>A kulcstartó elérése az Azure CLI-ből

```azurecli-interactive
# With managed identities for Azure resources set up on the DSVM, users on the DSVM can use Azure CLI to perform the authorized functions. The following commands enable access to the key vault from Azure CLI without requiring login to an Azure account.
# Prerequisites: MSI is already set up on the DSVM as indicated earlier. Specific permissions, like accessing storage account keys, reading specific secrets, and writing new secrets, are provided to the MSI.

# Authenticate to Azure CLI without requiring an Azure account. 
az login --msi

# Retrieve a secret from the key vault. 
az keyvault secret show --vault-name <Vault Name> --name SQLPasswd

# Create a new secret in the key vault.
az keyvault secret set --name MySecret --vault-name <Vault Name> --value "Helloworld"

# List access keys for the storage account.
az storage account keys list -g <Storage Account Resource Group> -n <Storage Account Name>
```
