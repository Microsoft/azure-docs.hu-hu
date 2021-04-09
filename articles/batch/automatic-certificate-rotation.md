---
title: A tanúsítvány automatikus elforgatásának engedélyezése batch-készletben
description: Létrehozhat egy felügyelt identitással rendelkező batch-készletet és egy automatikusan megújítható tanúsítványt.
ms.topic: conceptual
ms.date: 03/23/2021
ms.custom: references_regions
ms.openlocfilehash: e8bea49b2980deb8f20258ab7ea5619ece8cd2bf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104962509"
---
# <a name="enable-automatic-certificate-rotation-in-a-batch-pool"></a>A tanúsítvány automatikus elforgatásának engedélyezése batch-készletben

 Létrehozhat egy olyan batch-készletet, amelynek a tanúsítványa automatikusan meg lesz újítva. Ehhez a készletet olyan [felhasználó által hozzárendelt felügyelt identitással](managed-identity-pools.md) kell létrehozni, amely hozzáfér a tanúsítványhoz [Azure Key Vault](../key-vault/general/overview.md).

> [!IMPORTANT]
> A felhasználó által hozzárendelt felügyelt identitásokkal rendelkező Azure Batch-készletek támogatása jelenleg nyilvános előzetes verzióban érhető el a következő régiókban: USA 2. nyugati régiója, USA déli középső régiója, USA keleti régiója, US Gov Arizona és US Gov Virginia.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="create-a-user-assigned-identity"></a>Felhasználó által hozzárendelt identitás létrehozása

Először [hozza létre a felhasználó által hozzárendelt felügyelt identitást](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity) ugyanabban a bérlőben, mint a Batch-fiók. Ennek a felügyelt identitásnak nem kell ugyanabba az erőforráscsoporthoz lennie, vagy még ugyanabban az előfizetésben sem kell lennie.

Ügyeljen arra, hogy a felhasználó által hozzárendelt felügyelt identitás **ügyfél-azonosítóját** jegyezze fel. Ezt az értéket később kell megadnia.

:::image type="content" source="media/automatic-certificate-rotation/client-id.png" alt-text="Képernyőfelvétel: a felhasználóhoz rendelt felügyelt identitás ügyfél-azonosítója a Azure Portalban.":::

## <a name="create-your-certificate"></a>A tanúsítvány létrehozása

Ezután létre kell hoznia egy tanúsítványt, és hozzá kell adnia a Azure Key Vaulthoz. Ha még nem hozott létre kulcstartót, előbb meg kell tennie. Útmutatásért lásd: gyors útmutató [: tanúsítvány beállítása és lekérése Azure Key Vault a Azure Portal használatával](../key-vault/certificates/quick-create-portal.md).

A tanúsítvány létrehozásakor ügyeljen arra, hogy a rendszer automatikusan megújítsa az **élettartam-művelet típusát** , és adja meg, hogy hány nap elteltével kell megújítani a tanúsítványt.

:::image type="content" source="media/automatic-certificate-rotation/certificate.png" alt-text="Képernyőkép a tanúsítvány-létrehozási képernyőről a Azure Portal.":::

A tanúsítvány létrehozása után jegyezze fel a **titkos azonosítóját**. Ezt az értéket később kell megadnia.

:::image type="content" source="media/automatic-certificate-rotation/secret-identifier.png" alt-text="A tanúsítvány titkos azonosítóját ábrázoló képernyőfelvétel.":::

## <a name="add-an-access-policy-in-azure-key-vault"></a>Hozzáférési szabályzat hozzáadása Azure Key Vault

A Key vaultban rendeljen hozzá egy Key Vault hozzáférési szabályzatot, amely lehetővé teszi, hogy a felhasználó által hozzárendelt felügyelt identitás hozzáférhessen a titkokhoz és a tanúsítványokhoz. Részletes útmutatásért lásd: [Key Vault hozzáférési házirend társítása a Azure Portal használatával](../key-vault/general/assign-access-policy-portal.md).

## <a name="create-a-batch-pool-with-a-user-assigned-managed-identity"></a>Batch-készlet létrehozása felhasználó által hozzárendelt felügyelt identitással

Hozzon létre egy batch-készletet a felügyelt identitással a [Batch .net felügyeleti könyvtár](/dotnet/api/overview/azure/batch#management-library)használatával. További információ: [felügyelt identitások konfigurálása a Batch-készletekben](managed-identity-pools.md).

A következő példa a Batch felügyeleti REST API használatával hoz létre egy készletet. Ügyeljen arra, hogy a tanúsítványhoz tartozó **titkos azonosítót** `observedCertificates` és a felügyelt identitása **ügyfél-azonosítóját** használja `msiClientId` , és cserélje le az alábbi példában szereplő adatokra.

REST API URI

```http
PUT https://management.azure.com/subscriptions/<subscriptionid>/resourceGroups/<resourcegroupName>/providers/Microsoft.Batch/batchAccounts/<batchaccountname>/pools/<poolname>?api-version=2021-01-01
```

Kérelem törzse

```json
{
    "name": "test2",
    "type": "Microsoft.Batch/batchAccounts/pools",
    "properties": {
        "vmSize": "STANDARD_DS2_V2",
        "taskSchedulingPolicy": {
            "nodeFillType": "Pack"
        },
        "deploymentConfiguration": {
            "virtualMachineConfiguration": {
                "imageReference": {
                    "publisher": "canonical",
                    "offer": "ubuntuserver",
                    "sku": "18.04-lts",
                    "version": "latest"
                },
                "nodeAgentSkuId": "batch.node.ubuntu 18.04",
                "extensions": [
                    {
                        "name": "KVExtensions",
                        "type": "KeyVaultForLinux",
                        "publisher": "Microsoft.Azure.KeyVault",
                        "typeHandlerVersion": "1.0",
                        "autoUpgradeMinorVersion": true,
                        "settings": {
                            "secretsManagementSettings": {
                                "pollingIntervalInS": "300",
                                "certificateStoreLocation": "/var/lib/waagent/Microsoft.Azure.KeyVault",
                                "requireInitialSync": true,
                                "observedCertificates": [
                                    "https://testkvwestus2s.vault.azure.net/secrets/authcertforumatesting/8f5f3f491afd48cb99286ba2aacd39af"
                                ]
                            },
                            "authenticationSettings": {
                                "msiEndpoint": "http://169.254.169.254/metadata/identity",
                                "msiClientId": "b9f6dd56-d2d6-4967-99d7-8062d56fd84c"
                            }  }, "protectedSettings":{}
                    }                ]            }
        },
        "scaleSettings": {
            "fixedScale": {
                "targetDedicatedNodes": 1,
                "resizeTimeout": "PT15M"
            }
        },
    },
    "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
            "/subscriptions/042998e4-36dc-4b7d-8ce3-a7a2c4877d33/resourceGroups/ACR/providers/Microsoft.ManagedIdentity/userAssignedIdentities/testumaforpools": {}
        }
    }
}

```

## <a name="validate-the-certificate"></a>A tanúsítvány ellenőrzése

A tanúsítvány sikeres üzembe helyezésének ellenőrzéséhez jelentkezzen be a számítási csomópontba. A következőhöz hasonló kimenetnek kell megjelennie:

```
root@74773db5fe1b42ab9a4b6cf679d929da000000:/var/lib/waagent/Microsoft.Azure.KeyVault.KeyVaultForLinux-1.0.1363.13/status# cat 1.status
[{"status":{"code":0,"formattedMessage":{"lang":"en","message":"Successfully started Key Vault extension service. 2021-03-03T23:12:23Z"},"operation":"Service start.","status":"success"},"timestampUTC":"2021-03-03T23:12:23Z","version":"1.0"}]root@74773db5fe1b42ab9a4b6cf679d929da000000:/var/lib/waagent/Microsoft.Azure.KeyVault.KeyVaultForLinux-1.0.1363.13/status#
```

## <a name="next-steps"></a>Következő lépések

- További információ az [Azure-erőforrások felügyelt identitásáról](../active-directory/managed-identities-azure-resources/overview.md).
- Megtudhatja, hogyan használhatja az [ügyfél által felügyelt kulcsokat felhasználó által felügyelt identitásokkal](batch-customer-managed-key.md).
