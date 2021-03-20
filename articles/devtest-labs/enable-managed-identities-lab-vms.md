---
title: Felügyelt identitások engedélyezése a tesztkörnyezet virtuális gépei Azure DevTest Labs
description: Ez a cikk bemutatja, hogyan engedélyezhető a tesztkörnyezet tulajdonosa a felhasználó által hozzárendelt, felügyelt identitások számára a tesztkörnyezet virtuális gépei esetében.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: b4bf2900acebaeecd5cbc4cb65635aee6de87dda
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "88717632"
---
# <a name="enable-user-assigned-managed-identities-on-lab-virtual-machines-in-azure-devtest-labs"></a>A felhasználó által hozzárendelt felügyelt identitások engedélyezése a tesztkörnyezet virtuális gépei Azure DevTest Labs
A labor tulajdonosaként engedélyezheti a felhasználó által hozzárendelt felügyelt identitásokat a tesztkörnyezet virtuális gépei (VM) Azure DevTest Labs.

A felügyelt identitás bármely olyan szolgáltatás hitelesítéséhez használható, amely támogatja a Azure Active Directory (AD) hitelesítést, beleértve a Key Vault is, a kódban szereplő hitelesítő adatok átadása nélkül. A felügyelt identitásokkal kapcsolatos további információkért lásd: [Mi az Azure-erőforrások felügyelt identitása?](../active-directory/managed-identities-azure-resources/overview.md).

Ezzel a szolgáltatással a labor felhasználói megoszthatnak Azure-erőforrásokat, például a Azure SQL Databaset a labor környezetében. Az erőforráshoz való hitelesítést maga az identitás gondoskodik. A konfigurálást követően minden meglévő/újonnan létrehozott Tesztkörnyezet-virtuális gép engedélyezve lesz ezzel az identitással. A labor felhasználói hozzáférhetnek az erőforrásokhoz, miután bejelentkezett a számítógépeire.

> [!NOTE]
> Több felhasználóhoz rendelt felügyelt identitást is hozzáadhat a tesztkörnyezet virtuális gépei számára.

## <a name="use-azure-portal"></a>Az Azure Portal használata
Az alábbi lépéseket követve adhat hozzá egy felhasználóhoz rendelt felügyelt identitást a labor virtuális gépekhez:

1. [Felhasználóhoz rendelt felügyelt identitás létrehozása az előfizetésben](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)
1. Navigáljon a laborhoz tartozó **konfiguráció és szabályzatok** lapra.
1. A bal oldali menüben válassza az **identitás (előzetes verzió)** lehetőséget.
1. Válassza a **virtuális gép** fület.
1. Válassza a **Hozzáadás** lehetőséget, ha egy meglévő identitást szeretne kijelölni egy előre feltöltött legördülő listából. 

    > [!div class="mx-imgBorder"]
    > ![Identity gomb hozzáadása](./media/enable-managed-identities-lab-vms/add-identity-button.png)
1. Válasszon ki egy meglévő **felhasználó által felügyelt identitást** a legördülő listából, majd kattintson **az OK gombra**. 

    > [!div class="mx-imgBorder"]
    > ![Identitás hozzáadása](./media/enable-managed-identities-lab-vms/add-identity.png)

## <a name="use-api"></a>API használata

1.  Az identitás létrehozása után jegyezze fel az identitás erőforrás-AZONOSÍTÓját. A következő mintához hasonlóan kell kinéznie: 

    `/subscriptions/0000000000-0000-0000-0000-00000000000000/resourceGroups/<RESOURCE GROUP NAME> /providers/Microsoft.ManagedIdentity/userAssignedIdentities/<NAME of USER IDENTITY>`.
2. Futtasson egy PUT HTTPS-metódust egy új **ServiceRunner** -erőforrás a laborba való felvételéhez, ahogy az az alábbi példában is látható. 

    A Service Runner-erőforrás egy proxy erőforrás a felügyelt identitások kezeléséhez és vezérléséhez a DevTest Labs szolgáltatásban. A szolgáltatás futó neve lehet bármilyen érvényes név, de javasoljuk, hogy használja a felügyelt identitás erőforrásának nevét.

    ```json
    {
        "identity": {
            "type": "userAssigned",
            "userAssignedIdentities": { 
                "[userAssignedIdentityResourceId]": {}
            }
            },
        "location": "southeastasia",
        "properties": {
            "identityUsageType": "VirtualMachine"
        }
    }
    ```

## <a name="next-steps"></a>Következő lépések
A felügyelt identitásokkal kapcsolatos további tudnivalókért tekintse meg a [Mi az Azure-erőforrások felügyelt identitásai?](../active-directory/managed-identities-azure-resources/overview.md)című témakört.







