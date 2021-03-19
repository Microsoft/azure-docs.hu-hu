---
title: A kezdeményezési definíció szerkezetének részletei
description: Leírja, hogyan használhatók a házirend-kezdeményezési definíciók a szervezeten belüli Azure-erőforrásokra történő üzembe helyezés csoportházirend-definíciói számára.
ms.date: 03/16/2021
ms.topic: conceptual
ms.openlocfilehash: edd3f25dd528d1a718c9287c9f30988b87fb73e2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104587219"
---
# <a name="azure-policy-initiative-definition-structure"></a>Azure Policy kezdeményezés definíciós szerkezete

A kezdeményezések lehetővé teszik több kapcsolódó szabályzat-definíció csoportosítását a hozzárendelések és a felügyelet egyszerűsítése érdekében, mivel egyetlen elemmel dolgozik egy csoporttal. Például a kapcsolódó címkézési szabályzatok definícióit egyetlen kezdeményezésbe csoportosíthatja. Az egyes szabályzatok egyenkénti kiosztása helyett a kezdeményezést kell alkalmaznia.

A JSON használatával házirend-kezdeményezési definíciót hozhat létre. A házirend-kezdeményezés definíciója a következő elemeket tartalmazza:

- megjelenítendő név
- leírás
- metaadatok
- parameters
- szabályzat-definíciók
- házirend-csoportok (ez a tulajdonság a [szabályozási megfelelőség (előzetes verzió) funkció](./regulatory-compliance.md)része)

Az alábbi példa bemutatja, hogyan hozhat létre egy kezdeményezést két címke kezeléséhez: `costCenter` és `productName` . Két beépített szabályzatot használ az alapértelmezett címke értékének alkalmazásához.

```json
{
    "properties": {
        "displayName": "Billing Tags Policy",
        "policyType": "Custom",
        "description": "Specify cost Center tag and product name tag",
        "metadata": {
            "version": "1.0.0",
            "category": "Tags"
        },
        "parameters": {
            "costCenterValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for Cost Center tag"
                },
                "defaultValue": "DefaultCostCenter"
            },
            "productNameValue": {
                "type": "String",
                "metadata": {
                    "description": "required value for product Name tag"
                },
                "defaultValue": "DefaultProduct"
            }
        },
        "policyDefinitions": [{
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "parameters": {
                    "tagName": {
                        "value": "costCenter"
                    },
                    "tagValue": {
                        "value": "[parameters('costCenterValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
                "parameters": {
                    "tagName": {
                        "value": "costCenter"
                    },
                    "tagValue": {
                        "value": "[parameters('costCenterValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "parameters": {
                    "tagName": {
                        "value": "productName"
                    },
                    "tagValue": {
                        "value": "[parameters('productNameValue')]"
                    }
                }
            },
            {
                "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
                "parameters": {
                    "tagName": {
                        "value": "productName"
                    },
                    "tagValue": {
                        "value": "[parameters('productNameValue')]"
                    }
                }
            }
        ]
    }
}
```

Azure Policy a beépített és a minták a [Azure Policy mintákban](../samples/index.md)találhatók.

## <a name="metadata"></a>Metaadatok

A választható `metadata` tulajdonság a házirend kezdeményezési definíciójának adatait tárolja.
Az ügyfelek a szervezete számára hasznos tulajdonságokat és értékeket adhatnak meg `metadata` . Vannak azonban olyan _általános_ tulajdonságok, amelyeket a Azure Policy és a beépített modulok használnak.

### <a name="common-metadata-properties"></a>Gyakori metaadatok tulajdonságai

- `version` (karakterlánc): nyomon követi a házirend-kezdeményezési definíció tartalmának verziószámát.
- `category` (karakterlánc): meghatározza, hogy a házirend-definíció milyen kategóriában jelenjen meg Azure Portal.

  > [!NOTE]
  > A [szabályozási megfelelőségi](./regulatory-compliance.md) kezdeményezéshez a megfelelőségi `category` **szabályozásnak** kell megfelelnie.

- `preview` (Boolean): igaz vagy hamis jelző, ha a házirend-kezdeményezés definíciója _előnézet_.
- `deprecated` (Boolean): igaz vagy hamis jelző, ha a házirend-kezdeményezés definíciója _elavultként_ van megjelölve.

> [!NOTE]
> A Azure Policy szolgáltatás a, a `version` `preview` és a tulajdonságot használja a `deprecated` beépített szabályzat-definícióra vagy kezdeményezésre és állapotra való váltáshoz. A formátuma `version` : `{Major}.{Minor}.{Patch}` . Bizonyos állapotokat (például az _elavult_ vagy az _előnézet_) a `version` tulajdonsághoz vagy egy másik tulajdonsághoz ( **Boolean**) kell hozzáfűzni. További információ a Azure Policy-verziókról: [beépített verziószámozás](https://github.com/Azure/azure-policy/blob/master/built-in-policies/README.md).

## <a name="parameters"></a>Paraméterek

A paraméterek segítségével egyszerűsítheti a házirendek kezelését a házirend-definíciók számának csökkentésével. Gondoljon olyan paraméterekre, mint például az űrlap mezői – `name` , `address` , `city` , `state` . Ezek a paraméterek mindig azonosak maradnak, de az értékek az űrlap kitöltése alapján változnak.
A paraméterek ugyanúgy működnek, mint a házirend-kezdeményezések létrehozásakor. A szabályzatok definíciójában szereplő paraméterekkel együtt felhasználhatja ezt a paramétert is a belefoglalt házirendekben.

> [!NOTE]
> A kezdeményezés kiosztása után a kezdeményezésére szintjének paraméterei nem módosíthatók. Ennek oka, hogy a paraméter megadásakor a rendszer a következőt adja meg: **defaultValue** .

### <a name="parameter-properties"></a>Paraméter tulajdonságai

A paraméter a következő tulajdonságokkal rendelkezik, amelyek a házirend kezdeményezési definíciójában használatosak:

- `name`: A paraméter neve. A házirend- `parameters` szabályon belüli központi telepítési függvény használja. További információ: [paraméter értékének használata](#passing-a-parameter-value-to-a-policy-definition).
- `type`: Meghatározza, hogy a paraméter **karakterlánc**, **tömb**, **objektum**, **logikai**, **egész**, **lebegőpontos** vagy **datetime**.
- `metadata`: Meghatározza a Azure Portal által elsődlegesen használt altulajdonságokat a felhasználóbarát információk megjelenítéséhez:
  - `description`: Annak magyarázata, hogy mit használ a paraméter. A használható az elfogadható értékek példáinak megadására.
  - `displayName`: A (z) paraméterben a portálon megjelenő rövid név.
  - `strongType`: (Nem kötelező) a szabályzat definíciójának a portálon való hozzárendeléséhez használatos. Környezetfüggő listát biztosít. További információ: [strongType](#strongtype).
- `defaultValue`: (Nem kötelező) megadja a paraméter értékét egy hozzárendelésben, ha nincs megadva érték.
- `allowedValues`: (Nem kötelező) az értékek egy tömbjét adja meg, amelyet a paraméter elfogad a hozzárendelés során.

Például meghatározhat egy házirend-kezdeményezési definíciót, amely korlátozza az erőforrások helyét a különböző belefoglalt házirend-definíciókban. A házirend-kezdeményezés definíciójának egyik paramétere lehet **allowedLocations**. A paraméter ezután elérhetővé válik minden egyes belefoglalt házirend-definícióban, és a házirend-kezdeményezés hozzárendelése során lett meghatározva.

```json
"parameters": {
    "init_allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": [ "westus2" ],
        "allowedValues": [
            "eastus2",
            "westus2",
            "westus"
        ]
    }
}
```

### <a name="passing-a-parameter-value-to-a-policy-definition"></a>Paraméter értékének átadása házirend-definícióba

Deklarálja, hogy mely kezdeményezési paramétereket adja meg, amelyekbe beletartozik a szabályzat definíciója a kezdeményezés definíciójának [policyDefinitions](#policy-definitions) -tömbben. Míg a paraméter neve megegyező lehet, a szabályzat-definíciókban szereplő különböző nevek használatával egyszerűbbé válik a kód olvashatósága.

Például a korábban definiált **init_allowedLocations** Initiative paraméter több belefoglalt szabályzat-definíciónak, valamint a hozzájuk tartozó paramétereknek, **sql_locations** és **vm_locationsnak** is átadható, például:

```json
"policyDefinitions": [
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/0ec8fc28-d5b7-4603-8fec-39044f00a92b",
        "policyDefinitionReferenceId": "allowedLocationsSQL",
        "parameters": {
            "sql_locations": {
                "value": "[parameters('init_allowedLocations')]"
            }
        }
    },
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/aa09bd0f-aa5f-4343-b6ab-a33a6a6304f3",
        "policyDefinitionReferenceId": "allowedLocationsVMs",
        "parameters": {
            "vm_locations": {
                "value": "[parameters('init_allowedLocations')]"
            }
        }
    }
]
```

Ez a példa a [paraméter tulajdonságainál](#parameter-properties)bemutatott **init_allowedLocations** paraméterre hivatkozik.

### <a name="strongtype"></a>strongType

A `metadata` tulajdonságon belül a **strongType** használatával több választási lehetőséget is megadhat a Azure Portalon belül. a **strongType** lehet egy támogatott _erőforrástípus_ vagy egy megengedett érték. Annak megállapításához, hogy az _erőforrástípus_ érvényes-e a **strongType**, használja a [Get-AzResourceProvider](/powershell/module/az.resources/get-azresourceprovider).

Bizonyos, a **Get-AzResourceProvider** által nem visszaadott erőforrástípusok támogatottak. Ezek az erőforrástípusok a következők:

- `Microsoft.RecoveryServices/vaults/backupPolicies`

A **strongType** által engedélyezett nem erőforrás típusú értékek a következők:

- `location`
- `resourceTypes`
- `storageSkus`
- `vmSKUs`
- `existingResourceGroups`

## <a name="policy-definitions"></a>Szabályzatdefiníciók

A `policyDefinitions` kezdeményezés definíciójának része egy _tömb_ , amelyből a rendszer a meglévő szabályzat-definíciókat tartalmazza a kezdeményezésben. A [paraméter értékének a házirend-definícióba való átadása](#passing-a-parameter-value-to-a-policy-definition)során ez a tulajdonság határozza meg, hogy a rendszer hogyan adja át a [kezdeményezési paramétereket](#parameters) a házirend-definíciónak.

### <a name="policy-definition-properties"></a>Házirend-definíció tulajdonságai

A házirend-definíciót jelölő összes _tömb_ elem a következő tulajdonságokkal rendelkezik:

- `policyDefinitionId` (karakterlánc): a belefoglalni kívánt egyéni vagy beépített szabályzat definíciójának azonosítója.
- `policyDefinitionReferenceId` (karakterlánc): a belefoglalt szabályzat definíciójának rövid neve.
- `parameters`: (Nem kötelező) a kezdeményezési paraméternek a házirend-definícióban szereplő tulajdonságként való átadására szolgáló név/érték párok. További információ: [Paraméterek](#parameters).
- `groupNames` (karakterláncok tömbje): (nem kötelező) a csoport, amely a házirend-definíció tagja. További információ: [Policy groups](#policy-definition-groups).

Íme egy példa arra, `policyDefinitions` hogy két olyan házirend-definíciója van, amelyek mindegyike azonos kezdeményezési paramétert kapott:

```json
"policyDefinitions": [
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/0ec8fc28-d5b7-4603-8fec-39044f00a92b",
        "policyDefinitionReferenceId": "allowedLocationsSQL",
        "parameters": {
            "sql_locations": {
                "value": "[parameters('init_allowedLocations')]"
            }
        }
    },
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/aa09bd0f-aa5f-4343-b6ab-a33a6a6304f3",
        "policyDefinitionReferenceId": "allowedLocationsVMs",
        "parameters": {
            "vm_locations": {
                "value": "[parameters('init_allowedLocations')]"
            }
        }
    }
]
```

## <a name="policy-definition-groups"></a>Házirend-definíciós csoportok

A kezdeményezési definícióban szereplő szabályzat-definíciók csoportosíthatók és kategorizálva is lehetnek. A Azure Policy [megfelelőségi (előzetes](./regulatory-compliance.md) ) funkciója ezt a tulajdonságot használja a definíciók **vezérlőkbe** és **megfelelőségi tartományba** való csoportosítására. Ez az információ a `policyDefinitionGroups` _Array_ tulajdonságban van definiálva. További csoportosítási részletek a Microsoft által létrehozott **policyMetadata** -objektumokban találhatók. További információ: [metaadat-objektumok](#metadata-objects).

### <a name="policy-definition-groups-parameters"></a>Házirend-definíciós csoportok paraméterei

Az egyes _tömb_ elemeinek `policyDefinitionGroups` a következő tulajdonságokkal kell rendelkezniük:

- `name` (karakterlánc) \[ kötelező \] : a **csoport** rövid neve. A szabályozás megfelelősége alatt a **vezérlő**. A tulajdonság értékét a következő használja: `groupNames` `policyDefinitions` .
- `category` (karakterlánc): a csoporthoz tartozó hierarchia. A szabályozás megfelelősége területen a vezérlő **megfelelőségi tartománya** .
- `displayName` (karakterlánc): a **csoport** vagy a **vezérlőelem** rövid neve. A portál használja.
- `description` (karakterlánc): annak leírása, hogy a **csoport** vagy a **vezérlőelem** mit takar.
- `additionalMetadataId` (karakterlánc): a [policyMetadata](#metadata-objects) objektum helye, amely további részleteket tartalmaz a **vezérlő** és a **megfelelőség tartományáról**.

  > [!NOTE]
  > Előfordulhat, hogy az ügyfelek egy meglévő [policyMetadata](#metadata-objects) -objektumra mutatnak. Ezek az objektumok azonban csak _olvashatók_ , és csak a Microsoft hoztak létre.

Az `policyDefinitionGroups` ehhez tartozó NIST beépített kezdeményezés definíciójának tulajdonsága például a következőképpen néz ki:

```json
"policyDefinitionGroups": [
    {
        "name": "NIST_SP_800-53_R4_AC-1",
        "additionalMetadataId": "/providers/Microsoft.PolicyInsights/policyMetadata/NIST_SP_800-53_R4_AC-1"
    }
]
```

### <a name="metadata-objects"></a>Metaadat-objektumok

A Microsoft által létrehozott, a szabályozással kapcsolatos megfelelőségi előírások további információkat biztosítanak az egyes vezérlőkről.
Ez az információ a következő:

- Egy szabályozási megfelelőségi kezdeményezés **vezérlésének** áttekintésében Azure Portal jelenik meg.
- Elérhető REST APIon keresztül. Tekintse meg az `Microsoft.PolicyInsights` erőforrás-szolgáltatót és a [policyMetadata műveleti csoportot](/rest/api/policy/policymetadata/getresource).
- Az Azure CLI-n keresztül érhető el. Tekintse meg az az [Policy metadata](/cli/azure/policy/metadata) parancsot.

> [!IMPORTANT]
> A jogszabályi megfelelőséghez tartozó metaadat _-objektumok csak olvashatók_ , és az ügyfelek nem hozhatnak létre.

A házirend-csoportosítás metaadatainak a következő információkkal rendelkeznek a `properties` csomópontban:

- `metadataId`: Az a **VEZÉRLŐELEM azonosítója** , amelyhez a csoportosítás kapcsolódik.
- `category`(kötelező): a **vezérlőhöz** tartozó **megfelelőségi tartomány** .
- `title` (kötelező): a **VEZÉRLŐELEM azonosítójának** rövid neve.
- `owner` (kötelező): meghatározza, hogy ki felelős az Azure-beli vezérlésért: _ügyfél_, _Microsoft_, _közös_.
- `description`: További információ a vezérlőről.
- `requirements`: A vezérlő megvalósításának felelősségével kapcsolatos részletek.
- `additionalContentUrl`: A vezérlőelemmel kapcsolatos további információkra mutató hivatkozás. Ez a tulajdonság általában a megfelelőségi szabványban ez a vezérlőt tartalmazó dokumentáció szakaszára mutató hivatkozás.

Alább látható egy példa a **policyMetadata** objektumra. A példában szereplő metaadatok a _NIST SP 800-53 R4 AC-1_ vezérlőhöz tartoznak.

```json
{
  "properties": {
    "metadataId": "NIST SP 800-53 R4 AC-1",
    "category": "Access Control",
    "title": "Access Control Policy and Procedures",
    "owner": "Shared",
    "description": "**The organization:**    \na. Develops, documents, and disseminates to [Assignment: organization-defined personnel or roles]:  \n1. An access control policy that addresses purpose, scope, roles, responsibilities, management commitment, coordination among organizational entities, and compliance; and  \n2. Procedures to facilitate the implementation of the access control policy and associated access controls; and  \n  
\nb. Reviews and updates the current:  \n1. Access control policy [Assignment: organization-defined frequency]; and  \n2. Access control procedures [Assignment: organization-defined frequency].",
    "requirements": "**a.**  The customer is responsible for developing, documenting, and disseminating access control policies and procedures. The customer access control policies and procedures address access to all customer-deployed resources and customer system access (e.g., access to customer-deployed virtual machines, access to customer-built applications).  \n**b.**  The customer is responsible for reviewing and updating access control policies and procedures in accordance with FedRAMP requirements.",
    "additionalContentUrl": "https://nvd.nist.gov/800-53/Rev4/control/AC-1"
  },
  "id": "/providers/Microsoft.PolicyInsights/policyMetadata/NIST_SP_800-53_R4_AC-1",
  "name": "NIST_SP_800-53_R4_AC-1",
  "type": "Microsoft.PolicyInsights/policyMetadata"
}
```

## <a name="next-steps"></a>Következő lépések

- Lásd a [definíciós struktúrát](./definition-structure.md)
- Tekintse át a példákat [Azure Policy mintákon](../samples/index.md).
- A [Szabályzatok hatásainak ismertetése](effects.md).
- Megtudhatja, hogyan [hozhat létre programozott módon házirendeket](../how-to/programmatically-create.md).
- Ismerje meg, hogyan [kérheti le a megfelelőségi információkat](../how-to/get-compliance-data.md).
- Ismerje meg, hogyan javíthatja a [nem megfelelő erőforrásokat](../how-to/remediate-resources.md).
- Tekintse át, hogy a felügyeleti csoport hogyan [rendezi az erőforrásokat az Azure felügyeleti csoportjaival](../../management-groups/overview.md).
