---
title: Felügyelt identitások konfigurálása a Batch-készletekben
description: Megtudhatja, hogyan engedélyezheti a felhasználó által hozzárendelt felügyelt identitásokat a Batch-készletekben, és hogyan használhatja a felügyelt identitásokat a csomópontokon belül.
ms.topic: conceptual
ms.date: 02/10/2021
ms.custom: references_regions
ms.openlocfilehash: 4a59383d119f88bb3ee180f629ba0a6ea6ac2f44
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100417618"
---
# <a name="configure-managed-identities-in-batch-pools"></a>Felügyelt identitások konfigurálása a Batch-készletekben

Az [Azure-erőforrások felügyelt identitásai](../active-directory/managed-identities-azure-resources/overview.md) szükségtelenné teszik a fejlesztők számára a hitelesítő adatok felügyeletét azáltal, hogy identitást biztosítanak az Azure-erőforráshoz Azure Active Directoryban (Azure ad), és használják Azure Active Directory (Azure ad) jogkivonatok beszerzéséhez.

Ez a témakör bemutatja, hogyan engedélyezheti a felhasználó által hozzárendelt felügyelt identitásokat a Batch-készleteken, és hogyan használhatja a felügyelt identitásokat a csomópontokon belül.

> [!IMPORTANT]
> A felhasználó által hozzárendelt felügyelt identitásokkal rendelkező Azure Batch-készletek támogatása jelenleg nyilvános előzetes verzióban érhető el a következő régiókban: USA 2. nyugati régiója, USA déli középső régiója, USA keleti régiója, US Gov Arizona és US Gov Virginia.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="create-a-user-assigned-identity"></a>Felhasználó által hozzárendelt identitás létrehozása

Először [hozza létre a felhasználó által hozzárendelt felügyelt identitást](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity) ugyanabban a bérlőben, mint a Batch-fiók. Ennek a felügyelt identitásnak nem kell ugyanabba az erőforráscsoporthoz lennie, vagy még ugyanabban az előfizetésben sem kell lennie.

## <a name="create-a-batch-pool-with-user-assigned-managed-identities"></a>Felhasználó által hozzárendelt felügyelt identitásokkal rendelkező batch-készlet létrehozása

Miután létrehozott egy vagy több felhasználó által hozzárendelt felügyelt identitást, létrehozhat egy, a felügyelt identitással rendelkező batch-készletet a [Batch .net felügyeleti könyvtár](/dotnet/api/overview/azure/batch#management-library)használatával.

> [!IMPORTANT]
> A készleteket a [virtuális gép konfigurációjának](nodes-and-pools.md#virtual-machine-configuration) használatával kell konfigurálni a felügyelt identitások használatához.

```csharp
var poolParameters = new Pool(name: "yourPoolName")
    {
        VmSize = "standard_d1_v2",
        ScaleSettings = new ScaleSettings
        {
            FixedScale = new FixedScaleSettings
            {
                TargetDedicatedNodes = 1
            }
        },
        DeploymentConfiguration = new DeploymentConfiguration
        {
            VirtualMachineConfiguration = new VirtualMachineConfiguration(
                new ImageReference(
                    "Canonical",
                    "UbuntuServer",
                    "18.04-LTS",
                    "latest"),
                "batch.node.ubuntu 18.04")
        };
        Identity = new BatchPoolIdentity
        {
            Type = PoolIdentityType.UserAssigned,
            UserAssignedIdentities = new Dictionary<string, BatchPoolIdentityUserAssignedIdentitiesValue>
            {
                ["Your Identity Resource Id"] =
                    new BatchPoolIdentityUserAssignedIdentitiesValue()
            }
        }
    };

var pool = await managementClient.Pool.CreateWithHttpMessagesAsync(
    poolName:"yourPoolName",
    resourceGroupName: "yourResourceGroupName",
    accountName: "yourAccountName",
    parameters: poolParameters,
    cancellationToken: default(CancellationToken)).ConfigureAwait(false);    
```

> [!NOTE]
> A kezelt identitásokkal rendelkező készletek létrehozása jelenleg nem támogatott a [Batch .net Ügyféloldali kódtár](/dotnet/api/overview/azure/batch#client-library)esetében.

## <a name="use-user-assigned-managed-identities-in-batch-nodes"></a>Felhasználó által hozzárendelt felügyelt identitások használata a Batch-csomópontokban

A készletek létrehozása után a felhasználó által hozzárendelt felügyelt identitások Secure Shell (SSH) vagy Távoli asztal (RDP) használatával férhetnek hozzá a készlet csomópontjaihoz. A feladatokat úgy is megadhatja, hogy a felügyelt identitások közvetlenül hozzáférhessenek a [felügyelt identitásokat támogató Azure-erőforrásokhoz](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md).

A Batch-csomópontokon a felügyelt identitási jogkivonatok beszerezhetők, és az Azure AD-hitelesítésen keresztül az [azure instance metadata Service](../virtual-machines/windows/instance-metadata-service.md)használatával hitelesíthetők.

Windows esetén a hitelesítő hozzáférési token beszerzéséhez szükséges PowerShell-parancsfájl a következő:

```powershell
$Response = Invoke-RestMethod -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource={Resource App Id Url}' -Method GET -Headers @{Metadata="true"} 
```

Linux esetén a bash-szkript a következő:

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource={Resource App Id Url}' -H Metadata:true
```

További információkért lásd: a [felügyelt identitások használata Azure-beli virtuális gépeken az Azure-erőforrásokhoz hozzáférési jogkivonat beszerzéséhez](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md).

## <a name="next-steps"></a>Következő lépések

- További információ az [Azure-erőforrások felügyelt identitásáról](../active-directory/managed-identities-azure-resources/overview.md).
- Megtudhatja, hogyan használhatja az [ügyfél által felügyelt kulcsokat felhasználó által felügyelt identitásokkal](batch-customer-managed-key.md).
