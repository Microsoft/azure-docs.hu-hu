---
title: Az Azure BluePrints funkciói
description: Ismerteti azokat a funkciókat, amelyek az Azure-tervrajzok és-hozzárendelések tervrajzi összetevőihez használhatók.
ms.date: 01/27/2021
ms.topic: reference
ms.openlocfilehash: 92cb906e87179073b7a69aa0bd4eab22c77087f9
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/27/2021
ms.locfileid: "98919274"
---
# <a name="functions-for-use-with-azure-blueprints"></a>Az Azure-tervezetekhez használható függvények

Az Azure-tervrajzok olyan funkciókat biztosítanak, amelyek a tervrajzok meghatározását dinamikusabban teszik lehetővé. Ezek a függvények tervezet-definíciókkal és tervrajz-összetevőkkel használhatók. Egy Azure Resource Manager sablon (ARM-sablon) összetevő támogatja a Resource Manager függvények teljes használatát, továbbá a terv paraméterén keresztül dinamikus érték beszerzését is lehetővé teszi.

A következő függvények támogatottak:

- [leletek](#artifacts)
- [concat](#concat)
- [paraméterek](#parameters)
- [resourceGroup](#resourcegroup)
- [resourceGroups](#resourcegroups)
- [előfizetés](#subscription)

## <a name="artifacts"></a>leletek

`artifacts(artifactName)`

Egy olyan objektumot ad vissza, amely az adott tervrajz-összetevők kimenetével van feltöltve.

> [!NOTE]
> A `artifacts()` függvény nem használható ARM-sablonon belülről. A függvény csak a Blueprint definition JSON-ban vagy az összetevő JSON-ban használható, amikor a tervezetet Azure PowerShell vagy REST API a [tervrajzok](https://github.com/Azure/azure-blueprints/blob/master/README.md)részeként.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Description |
|:--- |:--- |:--- |:--- |
| artifactName |Yes |sztring |Egy tervrajz-összetevő neve. |

### <a name="return-value"></a>Visszatérési érték

A kimeneti tulajdonságok egy objektuma. A **kimenetek** tulajdonságai függnek a hivatkozott tervi összetevő típusától. Az összes típus formátuma a következő:

```json
{
  "outputs": {collectionOfOutputProperties}
}
```

#### <a name="policy-assignment-artifact"></a>Házirend-hozzárendelési összetevő

```json
{
    "outputs": {
        "policyAssignmentId": "{resourceId-of-policy-assignment}",
        "policyAssignmentName": "{name-of-policy-assignment}",
        "policyDefinitionId": "{resourceId-of-policy-definition}",
    }
}
```

#### <a name="arm-template-artifact"></a>ARM-sablon összetevője

A visszaadott objektum **kimenet** tulajdonságai az ARM-sablonban vannak definiálva, és az üzemelő példány adja vissza.

#### <a name="role-assignment-artifact"></a>Szerepkör-hozzárendelési összetevő

```json
{
    "outputs": {
        "roleAssignmentId": "{resourceId-of-role-assignment}",
        "roleDefinitionId": "{resourceId-of-role-definition}",
        "principalId": "{principalId-role-is-being-assigned-to}",
    }
}
```

### <a name="example"></a>Példa

ARM-sablon a következő minta kimeneti tulajdonságot tartalmazó _myTemplateArtifact_ :

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    ...
    "outputs": {
        "myArray": {
            "type": "array",
            "value": ["first", "second"]
        },
        "myString": {
            "type": "string",
            "value": "my string value"
        },
        "myObject": {
            "type": "object",
            "value": {
                "myProperty": "my value",
                "anotherProperty": true
            }
        }
    }
}
```

Néhány példa az adatok a _myTemplateArtifact_ mintából való beolvasására:

| Expression | Típus | Érték |
|:---|:---|:---|
|`[artifacts("myTemplateArtifact").outputs.myArray]` | Tömb | \["első", "Second"\] |
|`[artifacts("myTemplateArtifact").outputs.myArray[0]]` | Sztring | első |
|`[artifacts("myTemplateArtifact").outputs.myString]` | Sztring | "saját karakterlánc értéke" |
|`[artifacts("myTemplateArtifact").outputs.myObject]` | Objektum | {"myproperty": "saját érték", "anotherProperty": true} |
|`[artifacts("myTemplateArtifact").outputs.myObject.myProperty]` | Sztring | "saját érték" |
|`[artifacts("myTemplateArtifact").outputs.myObject.anotherProperty]` | Logikai | Igaz |

## <a name="concat"></a>concat

`concat(string1, string2, string3, ...)`

Több karakterlánc-értéket egyesít, és visszaadja az összefűzött karakterláncot.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Description |
|:--- |:--- |:--- |:--- |
| karakterlánc1 |Yes |sztring |Az Összefűzés első értéke. |
| További argumentumok |No |sztring |További értékek szekvenciális sorrendben az összefűzéshez |

### <a name="return-value"></a>Visszatérési érték

Összefűzött értékek karakterlánca.

### <a name="remarks"></a>Megjegyzések

A Azure Blueprint függvény eltér az ARM-sablon függvénytől, hogy csak karakterláncokkal működjön.

### <a name="example"></a>Példa

`concat(parameters('organizationName'), '-vm')`

## <a name="parameters"></a>parameters

`parameters(parameterName)`

Egy terv paramétereinek értékét adja vissza. A megadott paraméter nevét meg kell határozni a terv definíciójában vagy a tervrajz-összetevőkben.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Description |
|:--- |:--- |:--- |:--- |
| parameterName |Yes |sztring |A visszaadni kívánt paraméter neve. |

### <a name="return-value"></a>Visszatérési érték

A megadott terv vagy tervrajz-összetevő paraméterének értéke.

### <a name="remarks"></a>Megjegyzések

A Azure Blueprint függvény eltér az ARM-sablon függvénytől, hogy csak a terv paramétereivel működik.

### <a name="example"></a>Példa

Adja meg a _principalIds_ paramétert a terv definíciójában:

```json
{
    "type": "Microsoft.Blueprint/blueprints",
    "properties": {
        ...
        "parameters": {
            "principalIds": {
                "type": "array",
                "metadata": {
                    "displayName": "Principal IDs",
                    "description": "This is a blueprint parameter that any artifact can reference. We'll display these descriptions for you in the info bubble. Supply principal IDs for the users,groups, or service principals for the Azure role assignment.",
                    "strongType": "PrincipalId"
                }
            }
        },
        ...
    }
}
```

Ezután használja a _principalIds_ argumentumként `parameters()` egy tervrajz-összetevőben:

```json
{
    "type": "Microsoft.Blueprint/blueprints/artifacts",
    "kind": "roleAssignment",
    ...
    "properties": {
        "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
        "principalIds": "[parameters('principalIds')]",
        ...
    }
}
```

## <a name="resourcegroup"></a>resourceGroup

`resourceGroup()`

Egy olyan objektumot ad vissza, amely az aktuális erőforráscsoportot jelképezi.

### <a name="return-value"></a>Visszatérési érték

A visszaadott objektum formátuma a következő:

```json
{
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
}
```

### <a name="remarks"></a>Megjegyzések

A Azure Blueprint függvény eltér az ARM-sablon függvénytől. A `resourceGroup()` függvény nem használható előfizetési szintű összetevőben vagy a terv definíciójában. Csak olyan tervrajz-összetevőkben használható, amelyek egy erőforráscsoport-összetevő részét képezik.

A függvény gyakori funkciója, `resourceGroup()` hogy az erőforrásokat az erőforráscsoport-összetevővel megegyező helyen hozza létre.

### <a name="example"></a>Példa

Ha az erőforráscsoport helyét szeretné használni a terv definíciójában vagy a hozzárendelés során, akkor egy másik összetevő helyeként deklaráljon egy erőforráscsoport-helyőrző objektumot a terv definíciójában. Ebben a példában a _NetworkingPlaceholder_ az erőforráscsoport helyőrzőjának neve.

```json
{
    "type": "Microsoft.Blueprint/blueprints",
    "properties": {
        ...
        "resourceGroups": {
            "NetworkingPlaceholder": {
                "location": "eastus"
            }
        }
    }
}
```

Ezután használja a `resourceGroup()` függvényt egy olyan tervrajz-összetevő kontextusában, amely egy erőforráscsoport-helyőrző objektumot céloz meg. Ebben a példában a sablon-összetevőt telepíti a rendszer a _NetworkingPlaceholder_ erőforráscsoporthoz, és megadja a _resourceLocation_ dinamikusan kitöltött paramétert a sablonhoz a _NetworkingPlaceholder_ erőforráscsoport helyével. A _NetworkingPlaceholder_ erőforráscsoport helye statikusan definiálva lett a terv definíciójában, vagy dinamikusan definiálva lett a hozzárendelés során. Mindkét esetben a sablon összetevő az információ paraméterként van megadva, és a használatával telepíti az erőforrásokat a megfelelő helyre.

```json
{
  "type": "Microsoft.Blueprint/blueprints/artifacts",
  "kind": "template",
  "properties": {
      "template": {
        ...
      },
      "resourceGroup": "NetworkingPlaceholder",
      ...
      "parameters": {
        "resourceLocation": {
          "value": "[resourceGroup().location]"
        }
      }
  }
}
```

## <a name="resourcegroups"></a>resourceGroups

`resourceGroups(placeholderName)`

Egy olyan objektumot ad vissza, amely a megadott erőforráscsoport-összetevőt jelképezi. A (z `resourceGroup()` ) rendszertől eltérően, amely az összetevő környezetét igényli, ez a függvény egy adott erőforráscsoport-helyőrző tulajdonságainak beolvasására szolgál, ha az adott erőforráscsoport kontextusában nem.

### <a name="parameters"></a>Paraméterek

| Paraméter | Kötelező | Típus | Description |
|:--- |:--- |:--- |:--- |
| placeholderName |Yes |sztring |A visszaadni kívánt erőforráscsoport-összetevő helyőrző neve. |

### <a name="return-value"></a>Visszatérési érték

A visszaadott objektum formátuma a következő:

```json
{
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
}
```

### <a name="example"></a>Példa

Ha az erőforráscsoport helyét szeretné használni a terv definíciójában vagy a hozzárendelés során, akkor egy másik összetevő helyeként deklaráljon egy erőforráscsoport-helyőrző objektumot a terv definíciójában. Ebben a példában a _NetworkingPlaceholder_ az erőforráscsoport helyőrzőjának neve.

```json
{
    "type": "Microsoft.Blueprint/blueprints",
    "properties": {
        ...
        "resourceGroups": {
            "NetworkingPlaceholder": {
                "location": "eastus"
            }
        }
    }
}
```

Ezután használja a `resourceGroups()` függvényt bármely tervrajzi összetevő környezetében, hogy beolvassa az erőforráscsoport helyőrző objektumra mutató hivatkozást. Ebben a példában a sablon összetevőt a _NetworkingPlaceholder_ -erőforráscsoport kívül helyezi üzembe, és a _artifactLocation_ dinamikusan kitöltött paramétert ad a sablonhoz a _NetworkingPlaceholder_ erőforráscsoport helyével. A _NetworkingPlaceholder_ erőforráscsoport helye statikusan definiálva lett a terv definíciójában, vagy dinamikusan definiálva lett a hozzárendelés során. Mindkét esetben a sablon összetevő az információ paraméterként van megadva, és a használatával telepíti az erőforrásokat a megfelelő helyre.

```json
{
  "kind": "template",
  "properties": {
      "template": {
          ...
      },
      ...
      "parameters": {
        "artifactLocation": {
          "value": "[resourceGroups('NetworkingPlaceholder').location]"
        }
      }
  },
  "type": "Microsoft.Blueprint/blueprints/artifacts",
  "name": "myTemplate"
}
```

## <a name="subscription"></a>előfizetést

`subscription()`

Az aktuális terv-hozzárendelésre vonatkozó előfizetés részleteit adja vissza.

### <a name="return-value"></a>Visszatérési érték

A visszaadott objektum formátuma a következő:

```json
{
    "id": "/subscriptions/{subscriptionId}",
    "subscriptionId": "{subscriptionId}",
    "tenantId": "{tenantId}",
    "displayName": "{name-of-subscription}"
}
```

### <a name="example"></a>Példa

Használja az előfizetés megjelenített nevét és a `concat()` függvényt a _resourcename_ paraméterként átadott elnevezési konvenció létrehozásához.

```json
{
  "kind": "template",
  "properties": {
      "template": {
          ...
      },
      ...
      "parameters": {
        "resourceName": {
          "value": "[concat(subscription().displayName, '-vm')]"
        }
      }
  },
  "type": "Microsoft.Blueprint/blueprints/artifacts",
  "name": "myTemplate"
}
```

## <a name="next-steps"></a>További lépések

- Tudnivalók a [tervek életciklusáról](../concepts/lifecycle.md).
- A [statikus és dinamikus paraméterek](../concepts/parameters.md) használatának elsajátítása.
- A [tervekkel kapcsolatos műveleti sorrend](../concepts/sequencing-order.md) testreszabásának elsajátítása.
- A [tervek erőforrás-zárolásának](../concepts/resource-locking.md) alkalmazásával kapcsolatos részletek.
- A [meglévő hozzárendelések frissítésének](../how-to/update-existing-assignments.md) elsajátítása.
- A tervek hozzárendelése során felmerülő problémák megoldása [általános hibaelhárítással](../troubleshoot/general.md).