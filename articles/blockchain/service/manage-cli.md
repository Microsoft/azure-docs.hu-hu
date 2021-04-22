---
title: Az Azure Blockchain Service kezelése az Azure CLI-vel
description: Az azure Azure Blockchain Service kezelése az Azure CLI-val
ms.date: 07/23/2020
ms.topic: how-to
ms.reviewer: ravastra
ms.openlocfilehash: 55df56274aa5baa946b60c27cf49723d59c928a1
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107865926"
---
# <a name="manage-azure-blockchain-service-using-azure-cli"></a>Az Azure Blockchain Service kezelése az Azure CLI-vel

A Azure Portal mellett az Azure CLI-t is használhatja a blokklánctagok és a tranzakciós csomópontok kezeléséhez a Azure Blockchain Service.

## <a name="launch-azure-cloud-shell"></a>Az Azure Cloud Shell elindítása

Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta.

A Cloud Shell megnyitásához válassza a **Kipróbálás** lehetőséget egy kódblokk jobb felső sarkában. Az alkalmazásokat Cloud Shell böngészőlapon is elindíthatja a következő gombra: [https://shell.azure.com/bash](https://shell.azure.com/bash) . A **Copy** (másolás) gombra kattintva másolja és illessze be a kódot a Cloud Shellbe, majd nyomja le az Enter billentyűt a futtatáshoz.

Ha inkább helyileg szeretné telepíteni és használni a CLI-t, tekintse meg az [Azure CLI telepítését.](/cli/azure/install-azure-cli)

## <a name="prepare-your-environment"></a>A környezet előkészítése

1. Jelentkezzen be.

    Jelentkezzen be az [az login](/cli/azure/reference-index#az_login) paranccsal, ha a CLI helyi telepítését használja.

    ```azurecli
    az login
    ```

    A terminálon megjelenő utasítások alapján végezze el a hitelesítési folyamatot.

1. Telepítse az Azure CLI-bővítményt.

    Ha az Azure CLI bővítményhivatkozásaival dolgozik, először telepítenie kell a bővítményt.  Az Azure CLI-bővítmények hozzáférést biztosítanak azokhoz a kísérleti és kiadás előtti parancsokhoz, amelyek az alap CLI-vel még nincsenek szállítva.  A bővítményekről, beleértve azok frissítését és eltávolítását is, a [Bővítmények használata az Azure CLI-vel](/cli/azure/azure-cli-extensions-overview) című cikkben olvashat.

    Telepítse [a bővítményt a Azure Blockchain Service](/cli/azure/blockchain) a következő parancs futtatásával:

    ```azurecli-interactive
    az extension add --name blockchain
    ```

## <a name="create-blockchain-member"></a>Blokklánctag létrehozása

A [példa létrehoz egy blokklánctagot a](/cli/azure/blockchain/member#az_blockchain_member_create) Azure Blockchain Service, amely egy új konzorciumban futtatja a Quorum ledger protokollt.

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
| **erőforráscsoport** | Az erőforráscsoport neve, Azure Blockchain Service létre az erőforrásokat. |
| **név** | Egy egyedi név, amely azonosítja Azure Blockchain Service blokklánctagját. A nyilvános végpont címe a név. Például: `myblockchainmember.blockchain.azure.com`. |
| **Helyen** | Az Azure-régió, ahol a blokklánctag létrejön. Például: `eastus`. Válassza ki a felhasználókhoz vagy a többi Azure-alkalmazásához legközelebb eső helyet. Előfordulhat, hogy egyes régiókban a funkciók nem érhetők el. |
| **alaphelyzetbe állítása** | A tag alapértelmezett tranzakciós csomópontjának jelszava. Alapszintű hitelesítéshez használja a jelszót, amikor a blokklánctag alapértelmezett tranzakciós csomópontjának nyilvános végpontjára csatlakozik. A jelszónak az alábbi négy követelmény közül háromnak kell megfelelnie: a hossznak 12 & 72 karakter, 1 kisbetű, 1 nagybetű, 1 szám és 1 speciális karakter közé kell esnie, amely nem számjel (#), percent(%), vessző(,), csillag(*), back quote( ), dupla idézőjel("), egyszeres \` idézőjel('), dash(-) és semicolumn(;)|
| **Protokoll** | Blokklánc protokoll. A kvórum *protokoll jelenleg* támogatott. |
| **Konzorcium** | A csatlakozáshoz vagy létrehozáshoz használt konzorcium neve. További információ a konzorciumról: Azure Blockchain Service [konzorcium.](consortium.md) |
| **consortium-management-account-password** | A konzorciumfiók jelszavát tagfiókjelszónak is nevezik. A tagfiók jelszava a tag számára létrehozott Ethereum-fiók titkos kulcsának titkosítására használatos. A konzorciumkezeléshez a tagfiókot és a tagfiók jelszavát használja. |
| **Sku** | Réteg típusa. *Standard* vagy *Alapszintű.* Az *Alapszintű csomag* fejlesztési, tesztelési és koncepció igazolási célokra használható. Éles *környezetben való* üzembe helyezéshez használja a Standard szintet. Akkor is érdemes a *Standard* szintet használni, ha Blockchain Data Manager nagy mennyiségű privát tranzakciót küld. A tarifacsomag alapszintű és standard szint közötti módosítása a tag létrehozása után nem támogatott. |

## <a name="change-blockchain-member-passwords-or-firewall-rules"></a>Blokklánctagok jelszavának vagy tűzfalszabályainak módosítása

A [példa frissíti a blokklánctag](/cli/azure/blockchain/member#az_blockchain_member_update)jelszavát, a konzorciumkezelési jelszót és a tűzfalszabályt.

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
| **erőforráscsoport** | Az erőforráscsoport neve, Azure Blockchain Service létrejönnek az erőforrások. |
| **név** | A nevet, amely a Azure Blockchain Service azonosítja. |
| **alaphelyzetbe állítása** | A tag alapértelmezett tranzakciós csomópontjának jelszava. Alapszintű hitelesítéshez használja a jelszót, amikor a blokklánctag alapértelmezett tranzakciós csomópontjának nyilvános végpontjára csatlakozik. A jelszónak az alábbi négy követelmény közül háromnak kell megfelelnie: a hossznak 12 & 72 karakter, 1 kisbetű, 1 nagybetű, 1 szám és 1 speciális karakter közé kell esnie, amely nem számjel (#), percent(%), vessző(,), csillag(*), back quote( ), dupla idézőjel("), egyszeres \` idézőjel('), dash(-) és semicolumn(;)|
| **consortium-management-account-password** | A konzorciumfiók jelszavát tagfiókjelszónak is nevezik. A tagfiók jelszava a tag számára létrehozott Ethereum-fiók titkos kulcsának titkosítására használatos. A konzorciumkezeléshez a tagfiókot és a tagfiók jelszavát használhatja. |
| **tűzfalszabályok** | Kezdő és záró IP-cím az IP-címek engedélyezése listához. |

## <a name="create-transaction-node"></a>Tranzakciós csomópont létrehozása

[Tranzakciós csomópont létrehozása](/cli/azure/blockchain/transaction-node#az_blockchain_transaction_node_create) egy meglévő blokklánctagon belül. Tranzakciós csomópontok hozzáadásával növelheti a biztonsági elkülönítést és eloszthatja a terhelést. Például létrehozhat egy tranzakciós csomópont végpontot a különböző ügyfélalkalmazások számára.

```azurecli
az blockchain transaction-node create \
                     --resource-group <myResourceGroup> \
                     --member-name <myMemberName> \
                     --password <strongTransactionNodePassword> \
                     --name <myTransactionNodeName>
```

| Paraméter | Leírás |
|---------|-------------|
| **erőforráscsoport** | Az erőforráscsoport neve, Azure Blockchain Service létre az erőforrásokat. |
| **Helyen** | A blokklánctag Azure-régiója. |
| **tagnév** | A tag azonosítására Azure Blockchain Service név. |
| **alaphelyzetbe állítása** | A tranzakciós csomópont jelszava. A tranzakciós csomópont nyilvános végponthoz való csatlakozáskor használja az alapszintű hitelesítéshez használt jelszót. A jelszónak a következő négy követelmény közül háromnak kell megfelelnie: a hossznak 12 & 72 karakter, 1 kisbetű, 1 nagybetű, 1 szám és 1 speciális karakternek kell lennie, amely nem számjel (#), percent(%), vessző(,), star(*), back quote( \` ), double quote("), single quote('), dash(-) és semicolumn(;)|
| **név** | Tranzakciós csomópont neve. |

## <a name="change-transaction-node-password"></a>Tranzakciós csomópont jelszavának módosítása

A [példa frissíti a tranzakciós csomópont jelszavát.](/cli/azure/blockchain/transaction-node#az_blockchain_transaction_node_update)

```azurecli
az blockchain transaction-node update \
                     --resource-group <myResourceGroup> \
                     --member-name <myMemberName> \
                     --password <strongTransactionNodePassword> \
                     --name <myTransactionNodeName>
```

| Paraméter | Leírás |
|---------|-------------|
| **erőforráscsoport** | Az erőforráscsoport neve, Azure Blockchain Service erőforrás létezik. |
| **tag neve** | A Azure Blockchain Service nevét. |
| **alaphelyzetbe állítása** | A tranzakciós csomópont jelszava. A tranzakciós csomópont nyilvános végponthoz való csatlakozáskor használja az alapszintű hitelesítéshez használt jelszót. A jelszónak a következő négy követelmény közül háromnak kell megfelelnie: a hossznak 12 & 72 karakter, 1 kisbetű, 1 nagybetű, 1 szám és 1 speciális karakternek kell lennie, amely nem számjel (#), percent(%), vessző(,), star(*), back quote( \` ), double quote("), single quote('), dash(-) és semicolumn(;)|
| **név** | Tranzakciós csomópont neve. |

## <a name="list-api-keys"></a>API-kulcsok listozása

Az API-kulcsok a felhasználónévhez és jelszóhoz hasonló csomópont-hozzáféréshez használhatók. A kulcsrotációt két API-kulcs támogatja. Az API-kulcsok listához használja [a következő parancsot.](/cli/azure/blockchain/member#az_blockchain_transaction_node_list-api-key)

```azurecli
az blockchain member list-api-key \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName>
```

| Paraméter | Leírás |
|---------|-------------|
| **erőforráscsoport** | Az erőforráscsoport neve, Azure Blockchain Service erőforrás létezik. |
| **név** | A blokklánctag Azure Blockchain Service neve |

## <a name="regenerate-api-keys"></a>API-kulcsok újragenerálása

A következő paranccsal [újragenerálja az API-kulcsokat.](/cli/azure/blockchain/member#az_blockchain_transaction_node_regenerate-api-key)

```azurecli
az blockchain member regenerate-api-key \
                            --resource-group <myResourceGroup> \
                            --name <myMemberName> \
                            [--key-name {<keyValue1>, <keyValue2>}]
```

| Paraméter | Leírás |
|---------|-------------|
| **erőforráscsoport** | Az erőforráscsoport neve, Azure Blockchain Service erőforrás létezik. |
| **név** | Az Azure Blockchain Service blokklánctag neve. |
| **Kulcsnév** | Cserélje \<keyValue\> le a helyére a key1, key2 vagy mindkettőt. |

## <a name="delete-a-transaction-node"></a>Tranzakciós csomópont törlése

A [példa töröl egy blokklánctag tranzakciós csomópontot.](/cli/azure/blockchain/transaction-node#az_blockchain_transaction_node_delete)

```azurecli
az blockchain transaction-node delete \
                     --resource-group <myResourceGroup> \
                     --member-name <myMemberName> \
                     --name <myTransactionNode>
```

| Paraméter | Leírás |
|---------|-------------|
| **erőforráscsoport** | Az erőforráscsoport neve, Azure Blockchain Service erőforrás létezik. |
| **tagnév** | A Azure Blockchain Service blokklánctag neve, amely tartalmazza a törölni szükséges tranzakciós csomópont nevét is. |
| **név** | A törölni kívánt tranzakciós csomópont neve. |

## <a name="delete-a-blockchain-member"></a>Blokklánctag törlése

A [példa töröl egy blokklánctagot.](/cli/azure/blockchain/member#az_blockchain_member_delete)

```azurecli
az blockchain member delete \
                     --resource-group <myResourceGroup> \
                     --name <myMemberName>

```

| Paraméter | Leírás |
|---------|-------------|
| **erőforráscsoport** | Az erőforráscsoport neve, Azure Blockchain Service erőforrás létezik. |
| **név** | A törölni Azure Blockchain Service blokklánctag neve. |

## <a name="azure-active-directory"></a>Azure Active Directory

### <a name="grant-access-for-azure-ad-user"></a>Hozzáférés megadása Azure AD-felhasználó számára

```azurecli
az role assignment create \
                            --role <role> \
                            --assignee <assignee> \
                            --scope /subscriptions/<subId>/resourceGroups/<groupName>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>
```

| Paraméter | Leírás |
|---------|-------------|
| **Szerepet** | Az Azure AD-szerepkör neve. |
| **Megbízott** | Azure AD-felhasználói azonosító. Például: `user@contoso.com` |
| **Hatókör** | A szerepkör-hozzárendelés hatóköre. Blokklánctag vagy tranzakciós csomópont is lehet. |

**Példa**

Csomópont-hozzáférés megadása az Azure AD-felhasználó számára a **blokklánctag számára:**

```azurecli
az role assignment create \
                            --role 'myRole' \
                            --assignee user@contoso.com \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

**Példa**

Csomópont-hozzáférés megadása az Azure AD-felhasználó számára a blokklánctranzakciós **csomópont számára:**

```azurecli
az role assignment create \
                            --role 'MyRole' \
                            --assignee user@contoso.com \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1/transactionNodes/contosoTransactionNode1
```

### <a name="grant-node-access-for-azure-ad-group-or-application-role"></a>Csomópont-hozzáférés megadása Azure AD-csoport vagy alkalmazásszerepk számára

```azurecli
az role assignment create \
                            --role <role> \
                            --assignee-object-id <assignee_object_id>
```

| Paraméter | Leírás |
|---------|-------------|
| **Szerepet** | Az Azure AD-szerepkör neve. |
| **assignee-object-id** | Azure AD-csoportazonosító vagy alkalmazásazonosító. |
| **Hatókör** | A szerepkör-hozzárendelés hatóköre. Blokklánctag vagy tranzakciós csomópont is lehet. |

**Példa**

Csomópont-hozzáférés megadása az **alkalmazásszerepk számára**

```azurecli
az role assignment create \
                            --role 'myRole' \
                            --assignee-object-id 22222222-2222-2222-2222-222222222222 \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/contosoResourceGroup/providers/Microsoft.Blockchain/blockchainMembers/contosoMember1
```

### <a name="remove-azure-ad-node-access"></a>Azure AD-csomópontok hozzáférésének eltávolítása

```azurecli
az role assignment delete \
                            --role <myRole> \
                            --assignee <assignee> \
                            --scope /subscriptions/mySubscriptionId/resourceGroups/<myResourceGroup>/providers/Microsoft.Blockchain/blockchainMembers/<myMemberName>/transactionNodes/<myTransactionNode>
```

| Paraméter | Leírás |
|---------|-------------|
| **Szerepet** | Az Azure AD-szerepkör neve. |
| **Megbízott** | Azure AD-felhasználói azonosító. Például: `user@contoso.com` |
| **Hatókör** | A szerepkör-hozzárendelés hatóköre. Blokklánctag vagy tranzakciós csomópont is lehet. |

## <a name="next-steps"></a>Következő lépések

Ismerje meg, [hogyan konfigurálhatja Azure Blockchain Service tranzakciós csomópontokat a Azure Portal.](configure-transaction-nodes.md)
