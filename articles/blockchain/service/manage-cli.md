---
title: Az Azure Blockchain szolgáltatás kezelése az Azure CLI-vel
description: Az Azure Blockchain szolgáltatás kezelése az Azure CLI-vel
ms.date: 07/23/2020
ms.topic: how-to
ms.reviewer: ravastra
ms.openlocfilehash: 36b012c486c0c7d3303a81998e88f1605999c899
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "87170865"
---
# <a name="manage-azure-blockchain-service-using-azure-cli"></a>Az Azure Blockchain szolgáltatás kezelése az Azure CLI-vel

A Azure Portalon kívül az Azure CLI használatával is kezelheti az Azure Blockchain szolgáltatás blockchain-tagjait és tranzakciós csomópontjait.

## <a name="launch-azure-cloud-shell"></a>Az Azure Cloud Shell elindítása

Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta.

A Cloud Shell megnyitásához válassza a **Kipróbálás** lehetőséget egy kódblokk jobb felső sarkában. A Cloud Shell egy külön böngészőablakban is elindíthatja [https://shell.azure.com/bash](https://shell.azure.com/bash) . A **Copy** (másolás) gombra kattintva másolja és illessze be a kódot a Cloud Shellbe, majd nyomja le az Enter billentyűt a futtatáshoz.

Ha a parancssori felület helyi telepítését és használatát szeretné használni, tekintse meg az [Azure CLI telepítését](/cli/azure/install-azure-cli)ismertető témakört.

## <a name="prepare-your-environment"></a>A környezet előkészítése

1. Jelentkezzen be.

    Jelentkezzen be az [az login](/cli/azure/reference-index#az-login) paranccsal, ha a CLI helyi telepítését használja.

    ```azurecli
    az login
    ```

    A terminálon megjelenő utasítások alapján végezze el a hitelesítési folyamatot.

1. Telepítse az Azure CLI-bővítményt.

    Ha az Azure CLI bővítményhivatkozásaival dolgozik, először telepítenie kell a bővítményt.  Az Azure CLI-bővítmények hozzáférést biztosítanak azokhoz a kísérleti és kiadás előtti parancsokhoz, amelyek az alap CLI-vel még nincsenek szállítva.  A bővítményekről, beleértve azok frissítését és eltávolítását is, a [Bővítmények használata az Azure CLI-vel](/cli/azure/azure-cli-extensions-overview) című cikkben olvashat.

    Telepítse az [Azure Blockchain szolgáltatás bővítményét](/cli/azure/ext/blockchain/blockchain) a következő parancs futtatásával:

    ```azurecli-interactive
    az extension add --name blockchain
    ```

## <a name="create-blockchain-member"></a>Blockchain-tag létrehozása

Példa [létrehoz egy blockchain tagot](/cli/azure/ext/blockchain/blockchain/member#ext-blockchain-az-blockchain-member-create) az Azure blockchain szolgáltatásban, amely egy új konzorciumban futtatja a kvórum Ledger protokollt.

```azurecli
az blockchain member create \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName> \
                            --location <myBlockchainLocation> \
                            --password <strongMemberAccountPassword> \
                            --protocol "Quorum" \
                            --consortium <myConsortiumName> \
                            --consortium-management-account-password <strongConsortiumManagementPassword> \
                            --sku <skuName>
```

| Paraméter | Leírás |
|---------|-------------|
| **erőforrás-csoport** | Az erőforráscsoport neve, ahol az Azure Blockchain szolgáltatás erőforrásai jönnek létre. |
| **név** | Egy egyedi név, amely az Azure Blockchain szolgáltatás Blockchain-tagját azonosítja. A nevet a rendszer a nyilvános végpont címeként használja. Például: `myblockchainmember.blockchain.azure.com`. |
| **helyen** | Az az Azure-régió, ahol a blockchain-tag létrejött. Például: `eastus`. Válassza ki a felhasználókhoz vagy a többi Azure-alkalmazásához legközelebb eső helyet. Előfordulhat, hogy egyes régiókban nem érhetők el szolgáltatások. |
| **alaphelyzetbe állítása** | A tag alapértelmezett tranzakciós csomópontjának jelszava. Az alapszintű hitelesítéshez használja a jelszót az blockchain-tag alapértelmezett tranzakciós csomópontjának nyilvános végponthoz való csatlakozáskor. A jelszónak meg kell felelnie a következő négy követelmény közül háromnak: a hossznak 12 & 72 karakter, 1 kisbetűs karakter, 1 nagybetű, 1 szám és 1 speciális karakter közé kell esnie, amely nem szám típusú jel (#), százalék (%), vessző (,), csillag (*), vissza idézőjel ( \` ), idézőjel ("), szimpla idézőjel ('), kötőjel (-) és semicolumn (;)|
| **protokoll** | Blockchain protokoll. A *kvórum* protokoll jelenleg támogatott. |
| **Consortium** | A csatlakozáshoz vagy létrehozáshoz használandó konzorcium neve. A konzorciumokkal kapcsolatos további információkért lásd: [Azure Blockchain Service Consortium](consortium.md). |
| **konzorcium – felügyelet – fiók – jelszó** | A Consortium-fiók jelszavát más néven a tag fiók jelszavaként kell megadni. A tag fiók jelszava a tag számára létrehozott Ethereum-fiók titkos kulcsának titkosítására szolgál. A tag fiókja és a fiók jelszava a konzorciumok kezeléséhez. |
| **SKU** | A rétegek típusa. *Standard* vagy *alapszintű*. A fogalmak fejlesztéséhez, teszteléséhez és bizonyításához *használja az alapszintű* csomagot. Használja a *standard* szintű üzemi szintű üzembe helyezést. A *standard* szintet is érdemes használni, ha Blockchain Data Manager használ, vagy nagy mennyiségű privát tranzakciót küld. Az alapszintű és a standard szintű díjszabás a tag létrehozása után történő módosítása nem támogatott. |

## <a name="change-blockchain-member-passwords-or-firewall-rules"></a>Blockchain-tag jelszavának vagy tűzfalszabályok módosítása

Példa [a blockchain-tag](/cli/azure/ext/blockchain/blockchain/member#ext-blockchain-az-blockchain-member-update)jelszavának, a konzorcium felügyeleti jelszavának és a tűzfalszabály frissítésére.

```azurecli
az blockchain member update \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName> \
                     --password <strongMemberAccountPassword> \
                     --consortium-management-account-password <strongConsortiumManagementPassword> \
                     --firewall-rules <firewallRules>
```

| Paraméter | Leírás |
|---------|-------------|
| **erőforrás-csoport** | Az erőforráscsoport neve, ahol az Azure Blockchain szolgáltatás erőforrásai jönnek létre. |
| **név** | Az Azure Blockchain-szolgáltatás tagját azonosító név. |
| **alaphelyzetbe állítása** | A tag alapértelmezett tranzakciós csomópontjának jelszava. Az alapszintű hitelesítéshez használja a jelszót az blockchain-tag alapértelmezett tranzakciós csomópontjának nyilvános végponthoz való csatlakozáskor. A jelszónak meg kell felelnie a következő négy követelmény közül háromnak: a hossznak 12 & 72 karakter, 1 kisbetűs karakter, 1 nagybetű, 1 szám és 1 speciális karakter közé kell esnie, amely nem szám típusú jel (#), százalék (%), vessző (,), csillag (*), vissza idézőjel ( \` ), idézőjel ("), szimpla idézőjel ('), kötőjel (-) és semicolumn (;)|
| **konzorcium – felügyelet – fiók – jelszó** | A Consortium-fiók jelszavát más néven a tag fiók jelszavaként kell megadni. A tag fiók jelszava a tag számára létrehozott Ethereum-fiók titkos kulcsának titkosítására szolgál. A tag fiókja és a fiók jelszava a konzorciumok kezeléséhez. |
| **tűzfal – szabályok** | Az IP-engedélyezési lista kezdő és záró IP-címe. |

## <a name="create-transaction-node"></a>Tranzakciós csomópont létrehozása

[Hozzon létre egy tranzakciós csomópontot](/cli/azure/ext/blockchain/blockchain/transaction-node#ext-blockchain-az-blockchain-transaction-node-create) egy meglévő blockchain-tag belsejében. A tranzakciós csomópontok hozzáadásával növelheti a biztonsági elkülönítést és terjesztheti a terhelést. Rendelkezhet például egy tranzakciós csomópont végponttal különböző ügyfélalkalmazások számára.

```azurecli
az blockchain transaction-node create \
                     --resource-group <myResourceGroup> \
                     --member-name <myMemberName> \
                     --password <strongTransactionNodePassword> \
                     --name <myTransactionNodeName>
```

| Paraméter | Leírás |
|---------|-------------|
| **erőforrás-csoport** | Az erőforráscsoport neve, ahol az Azure Blockchain szolgáltatás erőforrásai jönnek létre. |
| **helyen** | Az blockchain-tag Azure-régiója. |
| **tag neve** | Az Azure Blockchain-szolgáltatás tagját azonosító név. |
| **alaphelyzetbe állítása** | A tranzakciós csomópont jelszava. A tranzakciós csomópont nyilvános végponthoz való csatlakozáskor használja az egyszerű hitelesítéshez használt jelszót. A jelszónak meg kell felelnie a következő négy követelmény közül háromnak: a hossznak 12 & 72 karakter, 1 kisbetűs karakter, 1 nagybetű, 1 szám és 1 speciális karakter közé kell esnie, amely nem szám típusú jel (#), százalék (%), vessző (,), csillag (*), vissza idézőjel ( \` ), idézőjel ("), szimpla idézőjel ('), kötőjel (-) és semicolumn (;)|
| **név** | Tranzakciós csomópont neve. |

## <a name="change-transaction-node-password"></a>Tranzakciós csomópont jelszavának módosítása

Példa: [a tranzakciós csomópont](/cli/azure/ext/blockchain/blockchain/transaction-node#ext-blockchain-az-blockchain-transaction-node-update) jelszavának frissítése.

```azurecli
az blockchain transaction-node update \
                     --resource-group <myResourceGroup> \
                     --member-name <myMemberName> \
                     --password <strongTransactionNodePassword> \
                     --name <myTransactionNodeName>
```

| Paraméter | Leírás |
|---------|-------------|
| **erőforrás-csoport** | Az erőforráscsoport neve, ahol az Azure Blockchain szolgáltatás erőforrásai léteznek. |
| **tag neve** | Az Azure Blockchain-szolgáltatás tagját azonosító név. |
| **alaphelyzetbe állítása** | A tranzakciós csomópont jelszava. A tranzakciós csomópont nyilvános végponthoz való csatlakozáskor használja az egyszerű hitelesítéshez használt jelszót. A jelszónak meg kell felelnie a következő négy követelmény közül háromnak: a hossznak 12 & 72 karakter, 1 kisbetűs karakter, 1 nagybetű, 1 szám és 1 speciális karakter közé kell esnie, amely nem szám típusú jel (#), százalék (%), vessző (,), csillag (*), vissza idézőjel ( \` ), idézőjel ("), szimpla idézőjel ('), kötőjel (-) és semicolumn (;)|
| **név** | Tranzakciós csomópont neve. |

## <a name="list-api-keys"></a>API-kulcsok listázása

Az API-kulcsok a felhasználónévhez és a jelszóhoz hasonló csomópont-hozzáféréshez használhatók. A kulcs rotációjának támogatásához két API-kulcs szükséges. Használja az alábbi parancsot az [API-kulcsok listázásához](/cli/azure/ext/blockchain/blockchain/member#ext-blockchain-az-blockchain-transaction-node-list-api-key).

```azurecli
az blockchain member list-api-key \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName>
```

| Paraméter | Leírás |
|---------|-------------|
| **erőforrás-csoport** | Az erőforráscsoport neve, ahol az Azure Blockchain szolgáltatás erőforrásai léteznek. |
| **név** | Az Azure Blockchain Service Blockchain-tag neve |

## <a name="regenerate-api-keys"></a>API-kulcsok újragenerálása

Használja az alábbi parancsot az [API-kulcsok újragenerálása](/cli/azure/ext/blockchain/blockchain/member#ext-blockchain-az-blockchain-transaction-node-regenerate-api-key)érdekében.

```azurecli
az blockchain member regenerate-api-key \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName> \
                            [--key-name {<keyValue1>, <keyValue2>}]
```

| Paraméter | Leírás |
|---------|-------------|
| **erőforrás-csoport** | Az erőforráscsoport neve, ahol az Azure Blockchain szolgáltatás erőforrásai léteznek. |
| **név** | Az Azure Blockchain Service Blockchain-tag neve. |
| **Kulcsnév** | Cserélje le a változót \<keyValue\> key1, key2 vagy mindkettőre. |

## <a name="delete-a-transaction-node"></a>Tranzakciós csomópont törlése

Példa [töröl egy blockchain-tag tranzakciós csomópontot](/cli/azure/ext/blockchain/blockchain/transaction-node#ext-blockchain-az-blockchain-transaction-node-delete).

```azurecli
az blockchain transaction-node delete \
                     --resource-group <myResourceGroup> \
                     --member-name <myMemberName> \
                     --name <myTransactionNode>
```

| Paraméter | Leírás |
|---------|-------------|
| **erőforrás-csoport** | Az erőforráscsoport neve, ahol az Azure Blockchain szolgáltatás erőforrásai léteznek. |
| **tag neve** | Az Azure Blockchain szolgáltatás Blockchain azon tagja, amely tartalmazza a törölni kívánt tranzakciós csomópont nevét is. |
| **név** | A törlendő tranzakciós csomópont neve. |

## <a name="delete-a-blockchain-member"></a>Blockchain-tag törlése

Példa [egy blockchain-tag törlésére](/cli/azure/ext/blockchain/blockchain/member#ext-blockchain-az-blockchain-member-delete).

```azurecli
az blockchain member delete \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName>

```

| Paraméter | Leírás |
|---------|-------------|
| **erőforrás-csoport** | Az erőforráscsoport neve, ahol az Azure Blockchain szolgáltatás erőforrásai léteznek. |
| **név** | A törlendő Azure Blockchain Service Blockchain-tag neve. |

## <a name="azure-active-directory"></a>Azure Active Directory

### <a name="grant-access-for-azure-ad-user"></a>Hozzáférés biztosítása az Azure AD-felhasználó számára

```azurecli
az role assignment create \
                            --role <role> \
                            --assignee <assignee> \
                            --scope /subscriptions/<subId>/resourceGroups/<groupName>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>
```

| Paraméter | Leírás |
|---------|-------------|
| **szerepkör** | Az Azure AD-szerepkör neve. |
| **megbízott** | Azure AD-felhasználói azonosító. Például: `user@contoso.com` |
| **hatókör** | A szerepkör-hozzárendelés hatóköre. Blockchain-tag vagy tranzakciós csomópont lehet. |

**Példa**

Csomópont-hozzáférés biztosítása az Azure AD-felhasználó számára a blockchain- **tag** számára:

```azurecli
az role assignment create \
                            --role 'myRole' \
                            --assignee user@contoso.com \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

**Példa**

Csomópont-hozzáférés biztosítása az Azure AD-felhasználó számára a blockchain **tranzakciós csomópontja** számára:

```azurecli
az role assignment create \
                            --role 'MyRole' \
                            --assignee user@contoso.com \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1/transactionNodes/contosoTransactionNode1
```

### <a name="grant-node-access-for-azure-ad-group-or-application-role"></a>Csomópont-hozzáférés biztosítása Azure AD-csoport vagy alkalmazás-szerepkör számára

```azurecli
az role assignment create \
                            --role <role> \
                            --assignee-object-id <assignee_object_id>
```

| Paraméter | Leírás |
|---------|-------------|
| **szerepkör** | Az Azure AD-szerepkör neve. |
| **megbízott – objektum-azonosító** | Azure AD-csoport azonosítója vagy az alkalmazás azonosítója. |
| **hatókör** | A szerepkör-hozzárendelés hatóköre. Blockchain-tag vagy tranzakciós csomópont lehet. |

**Példa**

Csomópont-hozzáférés megadása az **alkalmazási szerepkörhöz**

```azurecli
az role assignment create \
                            --role 'myRole' \
                            --assignee-object-id 22222222-2222-2222-2222-222222222222 \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

### <a name="remove-azure-ad-node-access"></a>Azure AD-csomópont-hozzáférés eltávolítása

```azurecli
az role assignment delete \
                            --role <myRole> \
                            --assignee <assignee> \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/<myResourceGroup>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>/transactionNodes/<myTransactionNode>
```

| Paraméter | Leírás |
|---------|-------------|
| **szerepkör** | Az Azure AD-szerepkör neve. |
| **megbízott** | Azure AD-felhasználói azonosító. Például: `user@contoso.com` |
| **hatókör** | A szerepkör-hozzárendelés hatóköre. Blockchain-tag vagy tranzakciós csomópont lehet. |

## <a name="next-steps"></a>Következő lépések

Ismerje meg, hogyan [konfigurálhatja az Azure Blockchain szolgáltatás tranzakciós csomópontjait a Azure Portal](configure-transaction-nodes.md).
