---
title: 'Oktatóanyag: szabályzatok létrehozása a megfelelőség kikényszerítása érdekében'
description: Ebben az oktatóanyagban szabályzatokat alkalmaz a szabványok betartatására, a költségek szabályozására, a biztonság fenntartására és a vállalati szintű tervezési alapelvek kiszabására.
ms.date: 03/24/2020
ms.topic: tutorial
ms.openlocfilehash: dcebbbfcc2f86ace7ea4400a2fdb6f1392f4efe6
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82190826"
---
# <a name="tutorial-create-and-manage-policies-to-enforce-compliance"></a>Oktatóanyag: szabályzatok létrehozása és kezelése a megfelelőség kikényszerítés érdekében

Fontos megismernie, hogy hogyan hozhat létre és kezelhet szabályzatokat az Azure-ban annak érdekében, hogy megfeleljen a vállalati szabványoknak és a szolgáltatói szerződéseknek. Ez az oktatóanyag bemutatja, hogyan használhatja az Azure Policyt a vállalaton belüli szabályzatok létrehozásához, hozzárendeléséhez és kezeléséhez kapcsolódó gyakori feladatok elvégzésére, például a következőkre:

> [!div class="checklist"]
> - Szabályzat hozzárendelése egy, a jövőben létrehozandó erőforrásokra vonatkozó feltétel kikényszerítésére
> - Kezdeményezési definíció létrehozása és hozzárendelése több erőforrás megfelelőségének nyomon követése céljából
> - Nem megfelelő vagy elutasított erőforrás feloldása
> - Új szabályzat megvalósítása a vállalaton belül

Ha szeretne hozzárendelni egy szabályzatot a meglévő erőforrások aktuális megfelelőségi állapotának azonosításához, a rövid útmutató cikkei ismertetik ennek módját.

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/) .

## <a name="assign-a-policy"></a>Szabályzat hozzárendelése

A megfelelőség Azure Policy használatával történő kikényszerítésének első lépése egy szabályzatdefiníció hozzárendelése. A szabályzatdefiníció határozza meg, hogy milyen feltétel teljesülése esetében lesz kikényszerítve a szabályzat, illetve milyen hatása lesz. Ebben a példában rendelje hozzá a (z) nevű beépített házirend-definíciót az _erőforráscsoport-címke öröklése elemhez, ha a hiányzó_ értékkel adja hozzá a megadott címkét a szülő erőforráscsoporthoz, hogy új vagy frissített erőforrások hiányoznak a címkéből.

1. A házirendek hozzárendeléséhez lépjen a Azure Portal. Keresse meg és válassza ki a **szabályzatot**.

   :::image type="content" source="../media/create-and-manage/search-policy.png" alt-text="Házirend keresése a keresősávban" border="false":::

1. Válassza ki a **Hozzárendelések** elemet az Azure Policy oldal bal oldalán. A hozzárendelés egy olyan szabályzat, amely egy adott hatókörön belül érvényes.

   :::image type="content" source="../media/create-and-manage/select-assignments.png" alt-text="Hozzárendelések kiválasztása a házirend áttekintő oldaláról" border="false":::

1. Válassza a **Szabályzat hozzárendelése** lehetőséget a **Szabályzat – Hozzárendelések** oldal tetején.

   :::image type="content" source="../media/create-and-manage/select-assign-policy.png" alt-text="Házirend-definíció hozzárendelése a hozzárendelések lapról" border="false":::

1. A **házirend kiosztása** lap és alapértelmezések **lapon válassza** ki a **hatókört** a három pont kiválasztásával, majd válasszon ki egy felügyeleti csoportot vagy előfizetést. Ha szeretne, válasszon erőforráscsoportot. A hatókör határozza meg, hogy a szabályzat-hozzárendelés milyen erőforrások vagy erőforráscsoportok esetében lesz kényszerítve.
   Ezután válassza a **kijelölés** elemet a **hatókör** lap alján.

   Ebben a példában a **Contoso** előfizetést használjuk. Saját előfizetése ettől eltérhet.

1. Az erőforrások kizárhatóak a **Hatókör** alapján. A **Kizárások** alacsonyabb szinten kezdődnek, mint a **Hatókör** szintje. A **Kizárások** megadása nem kötelező, ezért most hagyja üresen a mezőt.

1. Kattintson a **Szabályzatdefiníció** melletti három pontra az elérhető definíciók listájának megjelenítéséhez. A szabályzatdefiníció **Típus** tulajdonságát szűrheti _Beépített_ érték alapján az összes megtekintéséhez és a leírásaik elolvasásához.

1. **Ha hiányzik, válassza a címke öröklése az erőforráscsoporthoz**lehetőséget. Ha nem találja meg azonnal, írja be a **címke öröklése** a keresőmezőbe kifejezést, majd nyomja le az ENTER billentyűt, vagy válassza ki a keresőmezőbe.
   Ha megtalálta és kiválasztotta a házirend-definíciót, válassza az **elérhető definíciók** lap alján található **Kiválasztás lehetőséget** .

   :::image type="content" source="../media/create-and-manage/select-available-definition.png" alt-text="Keresési szűrő használata házirend megkereséséhez":::

1. A **Hozzárendelés neve** mező automatikusan kitöltődik a kiválasztott szabályzat nevével, de megadhat más nevet is. Ebben a példában hagyja a _címke öröklése az erőforráscsoporthoz, ha hiányzik_. Ha szeretné hozzáadhat egy **Leírást**. A leírás a szabályzat-hozzárendeléssel kapcsolatos információkat adja meg.

1. Hagyja _engedélyezve_a **házirend-kényszerítést** . Ha _le van tiltva_, ez a beállítás lehetővé teszi a házirend eredményének tesztelését a hatás elindítása nélkül. További információ: [kényszerítési mód](../concepts/assignment-structure.md#enforcement-mode).

1. A **hozzárendelt** érték automatikusan ki van töltve a bejelentkezett felhasználó alapján. Ennek a mezőnek a kitöltése nem kötelező, tehát megadhatók egyedi értékek.

1. Válassza a **Paraméterek** fület a varázsló tetején.

1. A **címke neve**mezőben adja meg a _környezet_értéket.

1. Válassza a **szervizelés** fület a varázsló tetején.

1. Ne törölje **a Szervizelési feladat létrehozása műveletet** . Ez a mező lehetővé teszi egy feladat létrehozását, amely az új vagy frissített erőforrások mellett módosítja a meglévő erőforrásokat. További információ: [erőforrások szervizelése](../how-to/remediate-resources.md).

1. A **felügyelt identitás létrehozása automatikusan be** van jelölve, mivel ez a házirend-definíció a [módosítás](../concepts/effects.md#modify) hatását használja. Az **engedélyek** a házirend-definíció alapján automatikusan a _közreműködőre_ vannak beállítva. További információkért lásd a [felügyelt identitásokat](../../../active-directory/managed-identities-azure-resources/overview.md) és a [szervizelési biztonsági működését](../how-to/remediate-resources.md#how-remediation-security-works).

1. Válassza a **felülvizsgálat + létrehozás** fület a varázsló tetején.

1. Tekintse át a beállításokat, majd kattintson a **Létrehozás** gombra a lap alján.

## <a name="implement-a-new-custom-policy"></a>Új egyéni szabályzat megvalósítása

Most, hogy hozzárendelt egy beépített szabályzatdefiníciót, még többet végezhet el az Azure Policyvel. Ezután hozzon létre egy új egyéni szabályzatot a költségek megtakarításához, ha ellenőrzi, hogy a környezetben létrehozott virtuális gépek nem lehetnek-e a G sorozatban. Így valahányszor a cég egy felhasználója megpróbál létrehozni egy G sorozatú virtuális gépet, a rendszer megtagadja a kérelmet.

1. A Azure Policy lap bal oldalán, a **szerzői műveletek** területen válassza a **definíciók** lehetőséget.

   :::image type="content" source="../media/create-and-manage/definition-under-authoring.png" alt-text="Definíciós lap szerzői csoport alatt" border="false":::

1. Kattintson a **+ Szabályzatdefiníció** elemre a lap tetején. Ez a gomb megnyílik a **házirend-definíció** lapon.

1. Adja meg a következő információkat:

   - Az a felügyeleti csoport vagy előfizetés, amelyben a szabályzatdefiníció mentve lett. A kiválasztáshoz kattintson a **Definíció helye** alatt található három pontra.

     > [!NOTE]
     > Ha ezt a szabályzatdefiníciót több előfizetésre szeretné alkalmazni, a helynek egy olyan felügyeleti csoportnak kell lennie, amely tartalmazza azokat az előfizetéseket, amelyekhez hozzárendeli a szabályzatot. Ugyanez vonatkozik a kezdeményezési definíciókra.

   - A házirend-definíció neve – _ * a_G sorozatnál kisebb virtuálisgép-SKU megkövetelése_
   - A szabályzatdefiníciók céljának leírása – _Ez a szabályzatdefiníció kikényszeríti, hogy a költségek csökkentése érdekében az ebben a hatókörben létrehozott minden virtuális gép termékváltozata a G sorozat alatti legyen._
   - Válasszon a meglévő lehetőségek közül (például _Compute_), vagy hozzon létre egy új kategóriát ehhez a szabályzatdefinícióhoz.
   - Másolja le az alábbi JSON-kódot, és frissítse az igényeinek megfelelően az alábbiakkal:
      - A szabályzat paraméterei.
      - A szabályzat szabályai/feltételei, ebben az esetben: a virtuális gép termékváltozata megegyezik a G sorozattal
      - A szabályzat hatása, ebben az esetben: **Megtagadás**.

   A JSON-kódnak így kell kinéznie. Illessze be a módosított kódot az Azure Portalra.

   ```json
   {
       "policyRule": {
           "if": {
               "allOf": [{
                       "field": "type",
                       "equals": "Microsoft.Compute/virtualMachines"
                   },
                   {
                       "field": "Microsoft.Compute/virtualMachines/sku.name",
                       "like": "Standard_G*"
                   }
               ]
           },
           "then": {
               "effect": "deny"
           }
       }
   }
   ```

   A házirend-szabály _mező_ tulajdonságának támogatott értéknek kell lennie. Az értékek teljes listája megtalálható a házirend- [definíció szerkezetének mezőiben](../concepts/definition-structure.md#fields). Példa egy aliasra: `"Microsoft.Compute/VirtualMachines/Size"`.

   További Azure Policy minták megtekintéséhez lásd: [Azure Policy minták](../samples/index.md).

1. Kattintson a **Mentés** gombra.

## <a name="create-a-policy-definition-with-rest-api"></a>Szabályzatdefiníció létrehozása REST API-val

Létrehozhat egy szabályzatot Azure Policy-definíciók REST API. A REST API lehetővé teszi a szabályzatdefiníciók létrehozását és törlését, valamint a meglévő definíciók információinak lekérését. Szabályzatdefiníció létrehozásához használja a következő példát:

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.authorization/policydefinitions/{policyDefinitionName}?api-version={api-version}
```

Használjon az alábbi példában láthatóhoz hasonló kéréstörzset:

```json
{
    "properties": {
        "parameters": {
            "allowedLocations": {
                "type": "array",
                "metadata": {
                    "description": "The list of locations that can be specified when deploying resources",
                    "strongType": "location",
                    "displayName": "Allowed locations"
                }
            }
        },
        "displayName": "Allowed locations",
        "description": "This policy enables you to restrict the locations your organization can specify when deploying resources.",
        "policyRule": {
            "if": {
                "not": {
                    "field": "location",
                    "in": "[parameters('allowedLocations')]"
                }
            },
            "then": {
                "effect": "deny"
            }
        }
    }
}
```

## <a name="create-a-policy-definition-with-powershell"></a>Szabályzatdefiníció létrehozása a PowerShell használatával

Mielőtt továbblép a PowerShell-példával, győződjön meg arról, hogy telepítette az Azure PowerShell legújabb verzióját az modulban.

Szabályzatdefiníciót a `New-AzPolicyDefinition` parancsmag használatával is létrehozhat.

Szabályzatdefiníció fájlból történő létrehozásához adja meg a fájl elérési útját. Külső fájl esetében használja az alábbi példát:

```azurepowershell-interactive
$definition = New-AzPolicyDefinition `
    -Name 'denyCoolTiering' `
    -DisplayName 'Deny cool access tiering for storage' `
    -Policy 'https://raw.githubusercontent.com/Azure/azure-policy-samples/master/samples/Storage/storage-account-access-tier/azurepolicy.rules.json'
```

Helyi fájl esetében használja a következő példát:

```azurepowershell-interactive
$definition = New-AzPolicyDefinition `
    -Name 'denyCoolTiering' `
    -Description 'Deny cool access tiering for storage' `
    -Policy 'c:\policies\coolAccessTier.json'
```

Beágyazott szabállyal rendelkező szabályzatdefiníció létrehozásához használja a következő példát:

```azurepowershell-interactive
$definition = New-AzPolicyDefinition -Name 'denyCoolTiering' -Description 'Deny cool access tiering for storage' -Policy '{
    "if": {
        "allOf": [{
                "field": "type",
                "equals": "Microsoft.Storage/storageAccounts"
            },
            {
                "field": "kind",
                "equals": "BlobStorage"
            },
            {
                "field": "Microsoft.Storage/storageAccounts/accessTier",
                "equals": "cool"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}'
```

A kimenetet egy `$definition` objektumban tárolja a rendszer, amely a szabályzat-hozzárendelés során használatos. Az alábbi példában paramétereket tartalmazó szabályzatdefiníciót hozunk létre:

```azurepowershell-interactive
$policy = '{
    "if": {
        "allOf": [{
                "field": "type",
                "equals": "Microsoft.Storage/storageAccounts"
            },
            {
                "not": {
                    "field": "location",
                    "in": "[parameters(''allowedLocations'')]"
                }
            }
        ]
    },
    "then": {
        "effect": "Deny"
    }
}'

$parameters = '{
    "allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of locations that can be specified when deploying storage accounts.",
            "strongType": "location",
            "displayName": "Allowed locations"
        }
    }
}'

$definition = New-AzPolicyDefinition -Name 'storageLocations' -Description 'Policy to specify locations for storage accounts.' -Policy $policy -Parameter $parameters
```

### <a name="view-policy-definitions-with-powershell"></a>Szabályzatdefiníciók megtekintése a PowerShell használatával

Az előfizetés összes szabályzatdefiníciójának megtekintéséhez használja az alábbi parancsot:

```azurepowershell-interactive
Get-AzPolicyDefinition
```

Visszaadja az összes elérhető szabályzatdefiníciót, köztük a beépített szabályzatokat is. Minden szabályzat az alábbi formátumban lesz visszaadva:

```output
Name               : e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceId         : /providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceName       : e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceType       : Microsoft.Authorization/policyDefinitions
Properties         : @{displayName=Allowed locations; policyType=BuiltIn; description=This policy enables you to
                     restrict the locations your organization can specify when deploying resources. Use to enforce
                     your geo-compliance requirements.; parameters=; policyRule=}
PolicyDefinitionId : /providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c
```

## <a name="create-a-policy-definition-with-azure-cli"></a>Szabályzatdefiníció létrehozása az Azure CLI használatával

Létrehozhat egy szabályzat-definíciót az Azure CLI használatával a `az policy definition` paranccsal. Beágyazott szabállyal rendelkező szabályzatdefiníció létrehozásához használja a következő példát:

```azurecli-interactive
az policy definition create --name 'denyCoolTiering' --description 'Deny cool access tiering for storage' --rules '{
    "if": {
        "allOf": [{
                "field": "type",
                "equals": "Microsoft.Storage/storageAccounts"
            },
            {
                "field": "kind",
                "equals": "BlobStorage"
            },
            {
                "field": "Microsoft.Storage/storageAccounts/accessTier",
                "equals": "cool"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}'
```

### <a name="view-policy-definitions-with-azure-cli"></a>Szabályzatdefiníciók megtekintése az Azure CLI használatával

Az előfizetés összes szabályzatdefiníciójának megtekintéséhez használja az alábbi parancsot:

```azurecli-interactive
az policy definition list
```

Visszaadja az összes elérhető szabályzatdefiníciót, köztük a beépített szabályzatokat is. Minden szabályzat az alábbi formátumban lesz visszaadva:

```json
{
    "description": "This policy enables you to restrict the locations your organization can specify when deploying resources. Use to enforce your geo-compliance requirements.",
    "displayName": "Allowed locations",
    "id": "/providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c",
    "name": "e56962a6-4747-49cd-b67b-bf8b01975c4c",
    "policyRule": {
        "if": {
            "not": {
                "field": "location",
                "in": "[parameters('listOfAllowedLocations')]"
            }
        },
        "then": {
            "effect": "Deny"
        }
    },
    "policyType": "BuiltIn"
}
```

## <a name="create-and-assign-an-initiative-definition"></a>Kezdeményezési definíció létrehozása és hozzárendelése

A kezdeményezési definícióval több szabályzatdefiníciót csoportosíthat egy átfogó cél eléréséhez. A kezdeményezés a befoglalt szabályzatoknak való megfelelés érdekében kiértékeli az erőforrásokat a hozzárendelés hatókörén belül. További információt a kezdeményezési definíciókról az [Azure Policy – áttekintés](../overview.md) című részben talál.

### <a name="create-an-initiative-definition"></a>Kezdeményezési definíció létrehozása

1. A Azure Policy lap bal oldalán, a **szerzői műveletek** területen válassza a **definíciók** lehetőséget.

   :::image type="content" source="../media/create-and-manage/definition-under-authoring.png" alt-text="Definíció kiválasztása a definíciók lapról" border="false":::

1. A **Kezdeményezési definíció** oldal megnyitásához kattintson a **+ Kezdeményezési definíció** elemre az oldal tetején.

   :::image type="content" source="../media/create-and-manage/initiative-definition.png" alt-text="Kezdeményezés definíciójának áttekintése lap" border="false":::

1. Kattintson a **Definíció helye** melletti három pontra a definíciót tároló felügyeleti csoport vagy előfizetés kiválasztásához. Ha az előző lap hatóköre egyetlen felügyeleti csoportra vagy előfizetésre terjed ki, a **Definíció helye** automatikusan kitöltődik. A kiválasztást követően a rendszer kitölti a **rendelkezésre álló definíciókat** .

1. Adja meg a kezdeményezés **nevét** és **leírását**.

   Ez a példa azt ellenőrzi, hogy az erőforrások megfelelnek-e a biztonságos állapotra vonatkozó szabályzat-definícióknak. Adja a kezdeményezésnek a **Biztonságossá tétel** nevet, és állítsa be a következő leírást: **Ez a kezdeményezés az erőforrások biztosításához kapcsolódó szabályzatdefiníciók kezelésére lett létrehozva**.

1. A **Kategória** megadásakor válasszon a meglévő lehetőségek közül, vagy hozzon létre új kategóriát.

1. Tekintse át az **Elérhető definíciók** listáját a **Kezdeményezési definíció** oldal jobb oldalán, és válassza ki azt a szabályzatdefiníciót (vagy definíciókat), amelye(ke)t hozzá szeretne adni a kezdeményezéshez. A **biztonságos** kezdeményezéshez adja hozzá a következő beépített szabályzat-definíciókat a házirend-definíciós információk **+** melletti, vagy a házirend-definíciós sor kiválasztása, majd a Részletek lapon a **+ Hozzáadás** lehetőség kiválasztásával:

   - Engedélyezett helyek
   - Hiányzó Endpoint Protection figyelése Azure Security Center
   - Az internetre irányuló virtuális gépek hálózati biztonsági csoportjának szabályait meg kell szigorítani
   - A Azure Backup engedélyezni kell a Virtual Machines
   - A lemezes titkosítást a virtuális gépeken kell alkalmazni

   Miután kiválasztotta a házirend-definíciót a listából, a rendszer mindegyiket hozzáadja a **Kategória**alá.

   :::image type="content" source="../media/create-and-manage/initiative-definition-2.png" alt-text="Kezdeményezési definíció paramétereinek áttekintése" border="false":::

1. Ha a kezdeményezéshez hozzáadott házirend-definíció paraméterekkel rendelkezik, akkor a házirend neve alatt, a **Kategória** terület területen jelennek meg. Az _érték_ beállítható „Megadott érték” (nem módosítható érték az adott kezdeményezés minden hozzárendelésében) vagy „A kezdeményezési paraméter használata” (az egyes kezdeményezés-hozzárendelésekkor kell megadni az értéket) állapotra. Ha a "set Value" (érték beállítása) lehetőség van kiválasztva, az _érték (ek)_ jobb oldalán lévő legördülő lista lehetővé teszi az érték (ek) beírását vagy kiválasztását. Ha „A kezdeményezési paraméter használata” lehetőséget választja, megjelenik egy új, **Kezdeményezési paraméterek** szakasz, amelyben meghatározható a kezdeményezés hozzárendelésekor beállítandó paraméter. Az ebben a kezdeményezési paraméterben szereplő engedélyezett értékek tovább korlátozhatják, hogy mi adható meg a kezdeményezés hozzárendelésekor.

   :::image type="content" source="../media/create-and-manage/initiative-definition-3.png" alt-text="Kezdeményezési definíció paramétereinek módosítása az engedélyezett értékekről" border="false":::

   > [!NOTE]
   > Egyes `strongType` paraméterek esetében az értékek listája nem határozható meg automatikusan. Ezekben az esetekben három pont jelenik meg a paraméterek sorától jobbra. Ha kiválasztja, megnyílik a "paraméter hatóköre&lt;(&gt;paraméter neve)" oldal. Ezen az oldalon válassza ki az értéklehetőségek biztosítására szolgáló előfizetést. Ez a paraméter-hatókör kizárólag a kezdeményezés hozzárendelésének létrehozásakor használatos, és hozzárendelésekor nincs hatással a szabályzat-kiértékelésre vagy a kezdeményezés hatókörére.

   Állítsa az "engedélyezett helyszínek" paramétert az "USA 2. keleti régiója" értékre, és hagyja a többit alapértelmezett "AuditifNotExists".

1. Kattintson a **Mentés** gombra.

#### <a name="create-a-policy-initiative-definition-with-azure-cli"></a>Házirend-kezdeményezési definíció létrehozása az Azure CLI-vel

Az Azure CLI-vel a `az policy set-definition` paranccsal hozhat létre házirend-kezdeményezési definíciót. Ha házirend-kezdeményezési definíciót szeretne létrehozni egy meglévő szabályzat-definícióval, használja a következő példát:

```azurecli-interactive
az policy set-definition create -n readOnlyStorage --definitions '[
        {
            "policyDefinitionId": "/subscriptions/mySubId/providers/Microsoft.Authorization/policyDefinitions/storagePolicy",
            "parameters": { "storageSku": { "value": "[parameters(\"requiredSku\")]" } }
        }
    ]' \
    --params '{ "requiredSku": { "type": "String" } }'
```

#### <a name="create-a-policy-initiative-definition-with-azure-powershell"></a>Házirend-kezdeményezési definíció létrehozása Azure PowerShell

Létrehozhat egy házirend-kezdeményezési definíciót Azure PowerShell használatával a `New-AzPolicySetDefinition` parancsmaggal. Ha egy házirend-kezdeményezési definíciót szeretne létrehozni egy meglévő szabályzat-definícióval, használja a következő `VMPolicySet.json`házirend-kezdeményezési definíciós fájlt:

```json
[
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
        "parameters": {
            "tagName": {
                "value": "Business Unit"
            },
            "tagValue": {
                "value": "Finance"
            }
        }
    },
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/464dbb85-3d5f-4a1d-bb09-95a9b5dd19cf"
    }
]
```

```azurepowershell-interactive
New-AzPolicySetDefinition -Name 'VMPolicySetDefinition' -Metadata '{"category":"Virtual Machine"}' -PolicyDefinition C:\VMPolicySet.json
```

### <a name="assign-an-initiative-definition"></a>Kezdeményezési definíció hozzárendelése

1. A Azure Policy lap bal oldalán, a **szerzői műveletek** területen válassza a **definíciók** lehetőséget.

1. Keresse meg és válassza ki a korában létrehozott **Biztonságossá tétel** kezdeményezési definíciót. A lap tetején válassza a **hozzárendelés** lehetőséget, hogy megnyissa a **biztonságos Get: hozzárendelés kezdeményezése** oldalon.

   :::image type="content" source="../media/create-and-manage/assign-definition.png" alt-text="Definíció társítása a kezdeményezési definíciós lapról" border="false":::

   Azt is megteheti, hogy a jobb gombbal a kijelölt sorra kattint, vagy kiválasztja a sor végén található három pontot a helyi menühöz. Ott válassza a **Hozzárendelés** parancsot.

   :::image type="content" source="../media/create-and-manage/select-right-click.png" alt-text="A kezdeményezés alternatív lehetőségei" border="false":::

1. A **Biztonságossá tétel: kezdeményezés hozzárendelése** oldalon adja meg a következő példaadatokat. A saját adatait is használhatja.

   - Hatókör: A felügyeleti csoport vagy előfizetés, ahová a kezdeményezést mentette lesz az alapértelmezett.
     A hatókör módosításához mentse a kezdeményezést egy előfizetésbe vagy erőforráscsoportba a mentési helyen belül.
   - Kizárások: a hatókörön belül bármely erőforrást konfigurálhatja úgy, hogy a kezdeményezési hozzárendelés ne legyen alkalmazva rá.
   - Kezdeményezési definíció és hozzárendelés neve: Biztonságossá tétel (a rendszer automatikusan a hozzárendelt kezdeményezés nevével tölti fel a mezőt).
   - Leírás: Ez a kezdeményezési hozzárendelés ennek a szabályzatdefiníció-csoportnak a kikényszerítésére lett létrehozva.
   - Házirend-kényszerítés: hagyja meg az _alapértelmezett beállítást_.
   - Hozzárendelte: A kitöltése automatikus az éppen bejelentkezett felhasználó alapján. Ennek a mezőnek a kitöltése nem kötelező, tehát megadhatók egyedi értékek.

1. Válassza a **Paraméterek** fület a varázsló tetején. Ha az előző lépések során konfigurált egy kezdeményezési paramétert, itt állítson be egy értéket.

1. Válassza a **szervizelés** fület a varázsló tetején. A **Felügyelt identitás létrehozása** jelölőnégyzetet hagyja üresen. Ezt a jelölőnégyzetet _meg_ kell adni, ha a hozzárendelt házirend vagy kezdeményezés olyan szabályzatot tartalmaz, amely a [deployIfNotExists](../concepts/effects.md#deployifnotexists) vagy a [módosítás](../concepts/effects.md#modify) hatásait tartalmazza. Mivel az oktatóanyaghoz használt szabályzat nem, hagyja üresen. További információkért lásd a [felügyelt identitásokat](../../../active-directory/managed-identities-azure-resources/overview.md) és a [szervizelési biztonsági működését](../how-to/remediate-resources.md#how-remediation-security-works).

1. Válassza a **felülvizsgálat + létrehozás** fület a varázsló tetején.

1. Tekintse át a beállításokat, majd kattintson a **Létrehozás** gombra a lap alján.

## <a name="check-initial-compliance"></a>Kezdeti megfelelőség ellenőrzése

1. Válassza a **Megfelelőség** lehetőséget az Azure Policy lap bal oldalán.

1. Keresse meg a **biztonságos** kezdeményezést. Valószínűleg továbbra is a **nem indult el** _megfelelőségi állapotban_ van.
   Válassza ki a kezdeményezést, amely részletesen ismerteti a hozzárendelés előrehaladását.

   :::image type="content" source="../media/create-and-manage/compliance-status-not-started.png" alt-text="Kezdeményezés megfelelőségi lapja – nem indított értékelések" border="false":::

1. A kezdeményezés hozzárendelésének befejezését követően a megfelelőségi lap frissül a **Megfelelő**_Megfelelőségi állapottal_.

   :::image type="content" source="../media/create-and-manage/compliance-status-compliant.png" alt-text="Kezdeményezési megfelelőség lap – az erőforrások megfelelőek" border="false":::

1. A kezdeményezés megfelelősége lapon található bármely szabályzat kiválasztásával megnyílik az adott szabályzat megfelelőségi adatok lapja. Ez a lap az erőforrás szintjén tartalmaz részleteket a megfelelőséghez.

## <a name="exempt-a-non-compliant-or-denied-resource-using-exclusion"></a>Nem megfelelő vagy elutasított erőforrás mentesítése Kizárás használatával

Miután hozzárendelt egy házirend-kezdeményezést egy adott hely megköveteléséhez, a rendszer megtagadja a másik helyen létrehozott összes erőforrást. Ebben a szakaszban egy megtagadási kérelem feloldását mutatja be egy adott erőforráscsoport kizárásának létrehozásával. A kizárás megakadályozza az adott erőforráscsoport szabályzatának (vagy kezdeményezésének) kényszerítését. A következő példában a kizárt erőforráscsoport bármelyik helye engedélyezett. Kizárás alkalmazható egy előfizetésre, egy erőforráscsoport vagy egy egyedi erőforrásra.

A hozzárendelt házirend vagy kezdeményezés által megakadályozott központi telepítések megtekinthetők az üzemelő példány által megnevezett erőforráscsoport esetében: válassza a **központi telepítések** lehetőséget a lap bal oldalán, majd válassza ki a sikertelen telepítés **központi telepítési nevét** . Az elutasított erőforrás _Tiltott_ állapotúként jelenik meg a listában. Az erőforrást megtagadó házirend vagy kezdeményezés és hozzárendelés meghatározásához válassza a **sikertelen lehetőséget. Kattintson ide a részletekért – >** a központi telepítés áttekintése lapon. A lap jobb oldalán megnyílik egy ablak a hibára vonatkozó információkkal. A **hiba részletei** szakaszban a kapcsolódó házirend-objektumok GUID azonosítói láthatók.

:::image type="content" source="../media/create-and-manage/rg-deployment-denied.png" alt-text="Szabályzat-hozzárendelés által elutasított üzembe helyezés" border="false":::

A Azure Policy oldalon: válassza a **megfelelőség** lehetőséget az oldal bal oldalán, és válassza a **biztonságos házirend beolvasása** kezdeményezést. Ezen az oldalon megnőtt a letiltott erőforrások **megtagadási** száma. Az **Events (események** ) lapon a szabályzat-definíció által megtagadott erőforrás létrehozására vagy központi telepítésére tett kísérletekről talál részleteket.

:::image type="content" source="../media/create-and-manage/compliance-overview.png" alt-text="Hozzárendelt szabályzatok megfelelőségének áttekintése" border="false":::

Ebben a példában a Trent Baker, az egyik contoso SR. Virtualization specialista szükséges munkát végzett. Meg kell adni a Trent helyet a kivételekhez. Létrehozott egy új erőforráscsoportot, a **LocationsExcluded**, és a következő megadása alól kivételt jelent a szabályzat-hozzárendelés.

### <a name="update-assignment-with-exclusion"></a>Hozzárendelés frissítése egy kizárással

1. Válassza ki a **hozzárendelések** elemet a Azure Policy lap bal oldalán található **authoring (szerzői műveletek** ) területen.

1. Tallózással keresse meg az összes házirend-hozzárendelést, és nyissa meg a _biztonságos_ szabályzat-hozzárendelést.

1. Állítsa be a **kizárást** úgy, hogy kiválasztja a három pontot, és kiválasztja a kizárni kívánt erőforráscsoportot, _LocationsExcluded_ ebben a példában. Válassza **a Hozzáadás a kiválasztott hatókörhöz** lehetőséget, majd válassza a **Mentés**lehetőséget.

   :::image type="content" source="../media/create-and-manage/request-exclusion.png" alt-text="Kizárt erőforráscsoport hozzáadása a szabályzat-hozzárendeléshez" border="false":::

   > [!NOTE]
   > A házirend-definíciótól és annak hatásaitól függően a kizárást a hozzárendelés hatókörén belül egy erőforráscsoport adott erőforrásaihoz is meg lehet adni. Ennek az oktatóanyagnak a **megtagadási** hatása miatt nem érdemes megállapítani a kizárást egy már létező erőforráson.

1. Válassza a **felülvizsgálat + mentés** lehetőséget, majd kattintson a **Mentés**gombra.

Ebben a szakaszban megoldotta a megtagadott kérelmet úgy, hogy egyetlen erőforráscsoport számára létrehoz egy kizárást.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha végzett az oktatóanyag erőforrásainak használatával, a következő lépésekkel törölheti a fent létrehozott szabályzat-hozzárendeléseket vagy definíciókat:

1. Válassza a **definíciók** **(vagy** hozzárendelések) lehetőséget a Azure Policy lap bal oldalán található **authoring (szerzői műveletek** ) elemnél.

1. Keresse meg az eltávolítani kívánt új kezdeményezést vagy szabályzatdefiníciót (vagy hozzárendelést).

1. Kattintson a jobb gombbal a sorra, vagy a bal gombbal a definíció (vagy a hozzárendelés) mellett található három pontra, majd a **Definíció törlése** (vagy a **Hozzárendelés törlése**) parancsra.

## <a name="review"></a>Áttekintés

Ebben az oktatóanyagban sikeresen elvégezte a következőket:

> [!div class="checklist"]
> - Hozzárendelt egy szabályzatot egy, a jövőben létrehozandó erőforrásokra vonatkozó feltétel kikényszerítésére
> - Létrehozott és hozzárendelt egy kezdeményezési definíciót több erőforrás megfelelőségének nyomon követése céljából
> - Feloldott egy nem megfelelő vagy elutasított erőforrást
> - Megvalósított egy új szabályzatot a vállalaton belül

## <a name="next-steps"></a>További lépések

A szabályzatdefiníciók szerkezetéről szóló további információkért lásd az alábbi cikket:

> [!div class="nextstepaction"]
> [Azure szabályzatdefiníciók struktúrája](../concepts/definition-structure.md)