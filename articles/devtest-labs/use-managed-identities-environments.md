---
title: Környezetek létrehozása az Azure által felügyelt identitásokkal a DevTest Labs szolgáltatásban | Microsoft Docs
description: Megtudhatja, hogyan használhatja a felügyelt identitásokat az Azure-ban környezetek üzembe helyezéséhez egy Azure DevTest Labsban található laborban.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 0f3e4b4d7030eb26c25b291e03caaa430d1979c4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98185784"
---
# <a name="use-azure-managed-identities-to-deploy-environments-in-a-lab"></a>Környezetek üzembe helyezése az Azure által felügyelt identitások használatával tesztkörnyezetben 

A tesztkörnyezet tulajdonosaként felügyelt identitást használhat a környezetek tesztkörnyezetben való üzembe helyezéséhez. Ez a funkció olyan esetekben hasznos, amikor a környezet olyan Azure-erőforrásokra hivatkozik, mint a kulcstartók, a megosztott képtárak és a környezet erőforráscsoporthoz tartozó hálózatok. Lehetővé teszi olyan homokozó környezetek létrehozását, amelyek nem korlátozódnak az adott környezet erőforráscsoporthoz.

> [!NOTE]
> Jelenleg egyetlen felhasználó által hozzárendelt identitás támogatott egy laborban. 

## <a name="prerequisites"></a>Előfeltételek

- [Szerepkörök létrehozása, listázása, törlése vagy hozzárendelése egy felhasználóhoz rendelt felügyelt identitáshoz a Azure Portal használatával](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md). 
    
    Győződjön meg arról, hogy a felügyelt identitás ugyanabban a régióban és előfizetésben lett létrehozva, mint a labor. A felügyelt identitásnak nem kell ugyanabban az erőforráscsoporthoz lennie.

## <a name="use-azure-portal"></a>Az Azure Portal használata

Ebben a szakaszban a tesztkörnyezet tulajdonosaként a Azure Portal használatával adhat hozzá felhasználót felügyelt identitást a laborhoz. 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Keressen rá a **DevTest Labs** kifejezésre.
1. A Labs listából válassza ki a kívánt labort.
1. Válassza **a konfiguráció és szabályzatok**  ->  **identitás (előzetes verzió)** lehetőséget. 
1. Felhasználóhoz rendelt identitás hozzáadásához válassza a **felhasználó által hozzárendelt** lapot.
1. Kattintson a **Hozzáadás** gombra.
1. Válasszon ki egy már létrehozott és/vagy hozzáféréssel rendelkező felhasználót a legördülő menüből.
 
    ![Felhasználó által felügyelt identitás hozzáadása](./media/use-managed-identities-environments/add-user-managed-identity.png)
1. A lap tetején kattintson a **Mentés** gombra.

    A mentés után a labor ezt az identitást fogja használni az összes tesztkörnyezet környezetének telepítésekor. Az identitás erőforrását az Azure-ban is elérheti, ha kijelöli az identitást a listából. 

A labor tulajdonosának semmilyen különleges teendőt nem kell tennie a környezet üzembe helyezésekor, ha a laborhoz hozzáadott identitás rendelkezik a környezethez hozzáférő külső erőforrásokhoz szükséges engedélyekkel. 

Ha módosítani szeretné a laborhoz rendelt felhasználó által felügyelt identitást, először távolítsa el a laborhoz csatolt identitást, majd adjon hozzá egy másikat a laborhoz. A laborhoz csatolt identitás eltávolításához válassza a **... lehetőséget. (három pont)**, majd kattintson az **Eltávolítás** gombra. 

## <a name="use-api"></a>API használata

1. Az identitás létrehozása után jegyezze fel az identitás erőforrás-AZONOSÍTÓját. A következő mintához hasonlóan kell kinéznie: 

    `/subscriptions/0000000000-0000-0000-0000-00000000000000/resourceGroups/<RESOURCE GROUP NAME> /providers/Microsoft.ManagedIdentity/userAssignedIdentities/<NAME of USER IDENTITY>`.
1. Ha egy PUT HTTPS-metódust hoz létre, az alábbi példához hasonló módon vegyen fel egy új `ServiceRunner` erőforrást a laborba. A Service Runner-erőforrás egy proxy erőforrás a felügyelt identitások kezeléséhez és vezérléséhez a DevTest Labs szolgáltatásban. A szolgáltatás futó neve lehet bármilyen érvényes név, de javasoljuk, hogy használja a felügyelt identitás erőforrásának nevét. 
 
    ```json
    PUT https://management.azure.com/subscriptions/{subId}/resourceGroups/{rg}/providers/Microsoft.Devtestlab/labs/{yourlabname}/serviceRunners/{serviceRunnerName}

    {
        "location": "{location}",
        "identity":{
            "type": "userAssigned",
            "userAssignedIdentities":{
                "[userAssignedIdentityResourceId]":{}
            }
        }
        "properties":{
            "identityUsageType":"Environment"
                     }
          
    }
    ```
 
    Bemutatunk egy példát: 

    ```json
    PUT https://management.azure.com/subscriptions/0000000000-0000-0000-0000-000000000000000/resourceGroups/exampleRG/providers/Microsoft.Devtestlab/labs/mylab/serviceRunners/sampleuseridentity

    {
        "location": "eastus",
        "identity":{
            "type": "userAssigned",
            "userAssignedIdentities":{
                "/subscriptions/0000000000-0000-0000-0000-000000000000000/resourceGroups/exampleRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/sampleuseridentity":{}
            }
        }
        "properties":{
            "identityUsageType":"Environment"
                     }
    }
    ```
 
Miután hozzáadta a felhasználóhoz rendelt identitást a laborhoz, a Azure DevTest Labs szolgáltatás Azure Resource Manager környezetek telepítésekor fogja használni. Ha például szüksége van a Resource Manager-sablonra egy külső megosztott képkatalógus rendszerképének eléréséhez, győződjön meg arról, hogy a laborhoz hozzáadott identitásnak minimálisan szükséges engedélyei vannak a megosztott Képtár erőforrásához. 
