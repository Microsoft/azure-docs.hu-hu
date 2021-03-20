---
title: Az adatműveletekhez való felhasználói hozzáférés korlátozása kizárólag Azure Cosmos DB
description: Megtudhatja, hogyan korlátozhatja az adatműveletekhez való hozzáférést Azure Cosmos DB
author: voellm
ms.service: cosmos-db
ms.topic: how-to
ms.date: 12/9/2019
ms.author: tvoellm
ms.openlocfilehash: 61e8ee2cbcd1252704b2d1e220fcfaaff17c2f28
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93085989"
---
# <a name="restrict-user-access-to-data-operations-in-azure-cosmos-db"></a>Az adatműveletekhez való felhasználói hozzáférés korlátozása Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB két módon hitelesítheti az interakciókat az adatbázis-szolgáltatással:

- a Azure Active Directory identitás használata a Azure Portal való interakció során
- Azure Cosmos DB [kulcsok](database-security.md#primary-keys) vagy [erőforrás-tokenek](secure-access-to-data.md#resource-tokens) használata az API-k és SDK-k által kezdeményezett hívások kibocsátásakor.

Az egyes hitelesítési módszerek különböző műveletekhez biztosítanak hozzáférést, néhány átfedéssel:

:::image type="content" source="./media/how-to-restrict-user-data/operations.png" alt-text="Műveletek felosztása hitelesítési típus szerint" border="false":::

Bizonyos esetekben előfordulhat, hogy korlátozni szeretné a szervezet egyes felhasználói számára az adatműveletek (azaz a szifilisz-kérelmek és-lekérdezések) elvégzését. Ez általában olyan fejlesztőknek szól, akik nem szükségesek erőforrásokat létrehozni vagy törölni, vagy módosítani az általuk használt tárolók kiépített átviteli sebességét.

A hozzáférést a következő lépések alkalmazásával korlátozhatja:
1. Egyéni Azure Active Directory szerepkör létrehozása azon felhasználók számára, akik számára korlátozni kívánja a hozzáférést. Az egyéni Active Directory szerepkörnek részletes hozzáférési szintűnek kell lennie a műveletekhez Azure Cosmos DB [szemcsés](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)műveleteinek használatával.
1. A nem adatműveletek végrehajtásának letiltása a kulcsokkal. Ezt úgy érheti el, ha korlátozza ezeket a műveleteket csak a hívások Azure Resource Manager.

A cikk következő fejezetei bemutatják, hogyan hajthatja végre ezeket a lépéseket.

> [!NOTE]
> A következő szakaszokban szereplő parancsok végrehajtásához telepítenie kell Azure PowerShell modul 3.0.0 vagy újabb verzióját, valamint az [Azure tulajdonosi szerepkört](../role-based-access-control/built-in-roles.md#owner) a módosítani kívánt előfizetéshez.

A következő szakaszban található PowerShell-parancsfájlokban cserélje le a következő helyőrzőket a környezetre jellemző értékekre:
- `$MySubscriptionId` – Az az előfizetés-azonosító, amely tartalmazza azt az Azure Cosmos-fiókot, amelyben korlátozni szeretné az engedélyeket. Példa: `e5c8766a-eeb0-40e8-af56-0eb142ebf78e`.
- `$MyResourceGroupName` – Az Azure Cosmos-fiókot tartalmazó erőforráscsoport. Példa: `myresourcegroup`.
- `$MyAzureCosmosDBAccountName` – Az Azure Cosmos-fiók neve. Példa: `mycosmosdbsaccount`.
- `$MyUserName` – Annak a felhasználónak a felhasználóneve ( username@domain ), akivel korlátozni kívánja a hozzáférést. Példa: `cosmosdbuser@contoso.com`.

## <a name="select-your-azure-subscription"></a>Válassza ki az Azure-előfizetését

Azure PowerShell parancsok használatához be kell jelentkeznie, és ki kell választania az előfizetést a parancsok végrehajtásához:

```azurepowershell
Login-AzAccount
Select-AzSubscription $MySubscriptionId
```

## <a name="create-the-custom-azure-active-directory-role"></a>Az egyéni Azure Active Directory szerepkör létrehozása

Az alábbi szkript létrehoz egy Azure Active Directory szerepkör-hozzárendelést az Azure Cosmos-fiókok "kulcs csak" hozzáférésével. A szerepkör az [Azure egyéni szerepkörein](../role-based-access-control/custom-roles.md) és a [Azure Cosmos db részletes lépésein](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)alapul. Ezek a szerepkörök és műveletek a Azure Active Directory névtér részét képezik `Microsoft.DocumentDB` .

1. Először hozzon létre egy nevű JSON-dokumentumot `AzureCosmosKeyOnlyAccess.json` a következő tartalommal:

    ```
    {
        "Name": "Azure Cosmos DB Key Only Access Custom Role",
        "Id": "00000000-0000-0000-0000-0000000000",
        "IsCustom": true,
        "Description": "This role restricts the user to read the account keys only.",
        "Actions":
        [
            "Microsoft.DocumentDB/databaseAccounts/listKeys/action"
        ],
        "NotActions": [],
        "DataActions": [],
        "NotDataActions": [],
        "AssignableScopes":
        [
            "/subscriptions/$MySubscriptionId"
        ]
    }
    ```

1. Futtassa a következő parancsokat a szerepkör-hozzárendelés létrehozásához és a felhasználóhoz való hozzárendeléséhez:

    ```azurepowershell
    New-AzRoleDefinition -InputFile "AzureCosmosKeyOnlyAccess.json"
    New-AzRoleAssignment -SignInName $MyUserName -RoleDefinitionName "Azure Cosmos DB Key Only Access Custom Role" -ResourceGroupName $MyResourceGroupName -ResourceName $MyAzureCosmosDBAccountName -ResourceType "Microsoft.DocumentDb/databaseAccounts"
    ```

## <a name="disallow-the-execution-of-non-data-operations"></a>Nem adatműveletek végrehajtásának letiltása

A következő parancsokkal eltávolítható a kulcsok használatának lehetősége a következőkhöz:
- erőforrások létrehozása, módosítása vagy törlése
- a tároló beállításainak frissítése (beleértve az indexelési házirendeket, az átviteli sebességet stb.).

```azurepowershell
$cdba = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName $MyResourceGroupName -ResourceName $MyAzureCosmosDBAccountName
$cdba.Properties.disableKeyBasedMetadataWriteAccess="True"
$cdba | Set-AzResource -Force
```

## <a name="next-steps"></a>Következő lépések

- További információ a [Cosmos db szerepköralapú hozzáférés-vezérléséről](role-based-access-control.md)
- Áttekintés a [Cosmos db lévő adathozzáférések biztonságos eléréséről](secure-access-to-data.md)
