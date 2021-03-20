---
title: Egyéni Azure-szerepkörök – Azure RBAC
description: Ismerje meg, hogyan hozhat létre Azure-beli egyéni szerepköröket az Azure szerepköralapú hozzáférés-vezérléssel (Azure RBAC) az Azure-erőforrások részletes hozzáférés-kezeléséhez.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: conceptual
ms.workload: identity
ms.date: 12/15/2020
ms.author: rolyon
ms.openlocfilehash: 79aaeee942a6d46243ee1c72d5904484b8698ebe
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97617323"
---
# <a name="azure-custom-roles"></a>Egyéni Azure-szerepkörök

> [!IMPORTANT]
> A felügyeleti csoport hozzáadása a `AssignableScopes` jelenleg előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ha az [Azure beépített szerepkörei](built-in-roles.md) nem felelnek meg a szervezet konkrét igényeinek, létrehozhat saját egyéni szerepköröket is. A beépített szerepkörökhöz hasonlóan egyéni szerepköröket is hozzárendelhet a felhasználókhoz, csoportokhoz és egyszerű szolgáltatásokhoz a felügyeleti csoport, az előfizetés és az erőforráscsoport hatókörében.

Az egyéni szerepkörök megoszthatók az azonos Azure AD-címtárban megbízható előfizetések között. A címtárban legfeljebb **5 000** egyéni szerepkör lehet. (Az Azure Germany és az Azure China 21Vianet esetében a korlát 2 000 egyéni szerepkör.) Egyéni szerepkörök hozhatók létre a Azure Portal, a Azure PowerShell, az Azure CLI vagy a REST API használatával.

## <a name="steps-to-create-a-custom-role"></a>Egyéni szerepkör létrehozásának lépései

Az alábbiakban az egyéni szerepkörök létrehozásához szükséges alapvető lépések láthatók.

1. Határozza meg a szükséges engedélyeket.

    Egyéni szerepkör létrehozásakor ismernie kell az engedélyek definiálásához elérhető műveleteket. Általában egy meglévő beépített szerepkörrel kell kezdenie, majd módosítania kell az igényeinek megfelelően. Adja hozzá a műveleteket a `Actions` `NotActions` szerepkör- [definíció](role-definitions.md)vagy tulajdonságaihoz. Ha rendelkezik adatműveletekkel, ezeket a vagy tulajdonságokat adja hozzá `DataActions` `NotDataActions` .

    További információt a következő szakaszban talál [a szükséges engedélyek meghatározásáról](#how-to-determine-the-permissions-you-need).

1. Döntse el, hogyan szeretné létrehozni az egyéni szerepkört.

    Egyéni szerepköröket [Azure Portal](custom-roles-portal.md), [Azure POWERSHELL](custom-roles-powershell.md), [Azure CLI](custom-roles-cli.md)vagy a [REST API](custom-roles-rest.md)használatával hozhat létre.

1. Hozza létre az egyéni szerepkört.

    A legegyszerűbb módszer a Azure Portal használata. Az egyéni szerepkörök a Azure Portal használatával történő létrehozásával kapcsolatos lépéseiért lásd: [Azure-beli egyéni szerepkörök létrehozása vagy frissítése a Azure Portal használatával](custom-roles-portal.md).

1. Tesztelje az egyéni szerepkört.

    Ha már rendelkezik az egyéni szerepkörrel, tesztelje, hogy a várt módon működik-e. Ha később módosítania kell a módosításokat, akkor frissítheti az egyéni szerepkört.

## <a name="how-to-determine-the-permissions-you-need"></a>A szükséges engedélyek megállapítása

Az Azure-ban több ezer engedély található, amelyeket esetleg belefoglalhat az egyéni szerepkörbe. Íme néhány módszer, amelyek segítségével meghatározhatja az egyéni szerepkörhöz hozzáadni kívánt engedélyeket:

- Tekintse meg a meglévő [beépített szerepköröket](built-in-roles.md).

    Előfordulhat, hogy módosítani kíván egy meglévő szerepkört, vagy a több szerepkörben használt engedélyeket is össze kívánja állítani.

- Sorolja fel azokat az Azure-szolgáltatásokat, amelyekhez hozzáférést szeretne biztosítani.

- Határozza meg az [Azure-szolgáltatásokhoz hozzárendelt erőforrás-szolgáltatókat](../azure-resource-manager/management/azure-services-resource-providers.md).

    Az Azure-szolgáltatások [erőforrás-szolgáltatókon](../azure-resource-manager/management/overview.md)keresztül teszik elérhetővé szolgáltatásaikat és engedélyeiket. A Microsoft. számítási erőforrás-szolgáltató például szolgáltatja a virtuális gépek erőforrásait és a Microsoft. számlázási erőforrás-szolgáltató az előfizetési és számlázási erőforrásokat. Annak ismerete, hogy az erőforrás-szolgáltatók segíthetnek leszűkíteni és meghatározni az egyéni szerepkörhöz szükséges engedélyeket.

    Ha a Azure Portal használatával hoz létre egyéni szerepkört, akkor az erőforrás-szolgáltatókat kulcsszavak keresésével is meghatározhatja. Ez a keresési funkció az [Azure egyéni szerepkörök létrehozása vagy frissítése a Azure Portal használatával](custom-roles-portal.md#step-4-permissions)című témakörben található.

    ![Engedélyek hozzáadása ablaktábla erőforrás-szolgáltatóval](./media/custom-roles-portal/add-permissions-provider.png)

- Keresse meg az [elérhető engedélyeket](resource-provider-operations.md) a felvenni kívánt engedélyek megkereséséhez.

    Ha a Azure Portal használatával hoz létre egyéni szerepkört, az engedélyek kulcsszava alapján is megkereshetők. Megkeresheti például a *virtuális gépet* vagy a *Számlázási* engedélyeket. Az összes engedélyt CSV-fájlként is letöltheti, majd megkeresheti a fájlt. Ez a keresési funkció az [Azure egyéni szerepkörök létrehozása vagy frissítése a Azure Portal használatával](custom-roles-portal.md#step-4-permissions)című témakörben található.

    ![Engedélyek listájának hozzáadása](./media/custom-roles-portal/add-permissions-list.png)

## <a name="custom-role-example"></a>Példa egyéni szerepkörre

Az alábbi ábrán látható, hogyan néz ki egy egyéni szerepkör a Azure PowerShell JSON formátumban történő megjelenítéséhez. Ezt az egyéni szerepkört a virtuális gépek figyelésére és újraindítására használhatja.

```json
{
  "Name": "Virtual Machine Operator",
  "Id": "88888888-8888-8888-8888-888888888888",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.ResourceHealth/availabilityStatuses/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Insights/diagnosticSettings/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": [
    "/subscriptions/{subscriptionId1}",
    "/subscriptions/{subscriptionId2}",
    "/providers/Microsoft.Management/managementGroups/{groupId1}"
  ]
}
```

A következő az Azure CLI használatával megjelenő egyéni szerepkört jeleníti meg.

```json
[
  {
    "assignableScopes": [
      "/subscriptions/{subscriptionId1}",
      "/subscriptions/{subscriptionId2}",
      "/providers/Microsoft.Management/managementGroups/{groupId1}"
    ],
    "description": "Can monitor and restart virtual machines.",
    "id": "/subscriptions/{subscriptionId1}/providers/Microsoft.Authorization/roleDefinitions/88888888-8888-8888-8888-888888888888",
    "name": "88888888-8888-8888-8888-888888888888",
    "permissions": [
      {
        "actions": [
          "Microsoft.Storage/*/read",
          "Microsoft.Network/*/read",
          "Microsoft.Compute/*/read",
          "Microsoft.Compute/virtualMachines/start/action",
          "Microsoft.Compute/virtualMachines/restart/action",
          "Microsoft.Authorization/*/read",
          "Microsoft.ResourceHealth/availabilityStatuses/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Insights/diagnosticSettings/*",
          "Microsoft.Support/*"
        ],
        "dataActions": [],
        "notActions": [],
        "notDataActions": []
      }
    ],
    "roleName": "Virtual Machine Operator",
    "roleType": "CustomRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

Ha egyéni szerepkört hoz létre, az Azure Portal narancssárga erőforrás ikonnal jelenik meg.

![Egyéni szerepkör ikonja](./media/custom-roles/roles-custom-role-icon.png)

## <a name="custom-role-properties"></a>Egyéni szerepkör tulajdonságai

A következő táblázat leírja, hogy az egyéni szerepkör tulajdonságai mit jelentenek.

| Tulajdonság | Kötelező | Típus | Leírás |
| --- | --- | --- | --- |
| `Name`</br>`roleName` | Igen | Sztring | Az egyéni szerepkör megjelenített neve. Habár a szerepkör-definíció egy felügyeleti csoport vagy előfizetési szintű erőforrás, a szerepkör-definíció több előfizetésben is használható, amelyek ugyanazt az Azure AD-címtárral rendelkeznek. Ennek a megjelenítendő névnek egyedinek kell lennie az Azure AD-címtár hatókörében. Tartalmazhat betűket, számokat, szóközöket és speciális karaktereket is. A karakterek maximális száma 128. |
| `Id`</br>`name` | Igen | Sztring | Az egyéni szerepkör egyedi azonosítója. A Azure PowerShell és az Azure CLI esetében ez az azonosító automatikusan létrejön, amikor új szerepkört hoz létre. |
| `IsCustom`</br>`roleType` | Igen | Sztring | Azt jelzi, hogy ez egy egyéni szerepkör-e. `true` `CustomRole` Egyéni szerepkörökhöz vagy értékhez. `false` `BuiltInRole` Beépített szerepkörökhöz vagy értékhez. |
| `Description`</br>`description` | Igen | Sztring | Az egyéni szerepkör leírása. Tartalmazhat betűket, számokat, szóközöket és speciális karaktereket is. A karakterek maximális száma 1024. |
| `Actions`</br>`actions` | Yes | Karakterlánc [] | Karakterláncok tömbje, amely meghatározza, hogy a szerepkör milyen kezelési műveleteket hajtson végre. További információ: [műveletek](role-definitions.md#actions). |
| `NotActions`</br>`notActions` | No | Karakterlánc [] | Karakterláncok tömbje, amely meghatározza az engedélyezetttől kizárt felügyeleti műveleteket `Actions` . További információkért lásd: a nem- [tapintatok](role-definitions.md#notactions). |
| `DataActions`</br>`dataActions` | No | Karakterlánc [] | Karakterláncok tömbje, amely meghatározza azokat az adatműveleteket, amelyeket a szerepkör engedélyez az adott objektumon belüli adatokon való végrehajtáshoz. Ha egyéni szerepkört hoz létre a `DataActions` alkalmazással, a szerepkör nem rendelhető hozzá a felügyeleti csoport hatóköréhez. További információ: [DataActions](role-definitions.md#dataactions). |
| `NotDataActions`</br>`notDataActions` | No | Karakterlánc [] | Karakterláncok tömbje, amely az engedélyezetttől kizárt adatműveleteket határozza meg `DataActions` . További információ: [NotDataActions](role-definitions.md#notdataactions). |
| `AssignableScopes`</br>`assignableScopes` | Yes | Karakterlánc [] | Karakterláncok tömbje, amely meghatározza az egyéni szerepkör hozzárendeléshez elérhető hatóköreit. Egyéni szerepkörben csak egyetlen felügyeleti csoportot lehet definiálni `AssignableScopes` . A felügyeleti csoport hozzáadása a `AssignableScopes` jelenleg előzetes verzióban érhető el. További információ: [AssignableScopes](role-definitions.md#assignablescopes). |

## <a name="wildcard-permissions"></a>Helyettesítő karakteres engedélyek

`Actions`, `NotActions` , `DataActions` és `NotDataActions` támogatja a helyettesítő karaktereket ( `*` ) az engedélyek definiálásához. A helyettesítő karakter ( `*` ) minden olyan engedélyt kiterjeszt, amely megfelel az Ön által megadott műveleti karakterláncnak. Tegyük fel például, hogy hozzá kívánja adni a Azure Cost Management és az exportáláshoz kapcsolódó összes engedélyt. Az összes művelet sztringjét hozzáadhatja:

```
Microsoft.CostManagement/exports/action
Microsoft.CostManagement/exports/read
Microsoft.CostManagement/exports/write
Microsoft.CostManagement/exports/delete
Microsoft.CostManagement/exports/run/action
```

A karakterláncok hozzáadása helyett csak helyettesítő sztringet adhat hozzá. A következő helyettesítő karakterlánc például az előző öt sztringnek felel meg. Ez magában foglalja az esetleg hozzáadott jövőbeli exportálási engedélyeket is.

```
Microsoft.CostManagement/exports/*
```

Egy karakterláncban több helyettesítő karakter is használható. A következő karakterlánc például a Cost Management összes lekérdezési engedélyét jelöli.

```
Microsoft.CostManagement/*/query/*
```

## <a name="who-can-create-delete-update-or-view-a-custom-role"></a>Kik hozhatnak létre, törölhetnek, frissíthetnek vagy tekinthetnek meg egyéni szerepköröket

A beépített szerepkörökhöz hasonlóan a `AssignableScopes` tulajdonság határozza meg azokat a hatóköröket, amelyekhez a szerepkör elérhető a hozzárendeléshez. Az `AssignableScopes` Egyéni szerepkör tulajdonsága azt is meghatározza, hogy ki hozhatja létre, törölheti, frissítheti vagy megtekintheti az egyéni szerepkört.

| Feladat | Művelet | Description |
| --- | --- | --- |
| Egyéni szerepkör létrehozása/törlése | `Microsoft.Authorization/ roleDefinitions/write` | Azok a felhasználók, akik ezt a műveletet az `AssignableScopes` Egyéni szerepkörön keresztül kaptak, létrehozhatnak (vagy törölhetnek) egyéni szerepköröket az adott hatókörökben való használatra. Például a felügyeleti csoportok, előfizetések és erőforráscsoportok [tulajdonosai](built-in-roles.md#owner) és [felhasználói hozzáférési rendszergazdái](built-in-roles.md#user-access-administrator) . |
| Egyéni szerepkörök frissítése | `Microsoft.Authorization/ roleDefinitions/write` | Azok a felhasználók, akik ezt a műveletet az egyéni szerepkör mindegyikén megkapják, `AssignableScopes` frissíthetik az egyéni szerepköröket a hatókörökben. Például a felügyeleti csoportok, előfizetések és erőforráscsoportok [tulajdonosai](built-in-roles.md#owner) és [felhasználói hozzáférési rendszergazdái](built-in-roles.md#user-access-administrator) . |
| Egyéni szerepkör megtekintése | `Microsoft.Authorization/ roleDefinitions/read` | Azok a felhasználók, akik egy hatókörben engedélyezik ezt a műveletet, megtekinthetik az adott hatókörben való hozzárendeléshez elérhető egyéni szerepköröket. Az összes beépített szerepkör elérhetővé teszi az egyéni szerepköröket a hozzárendeléshez. |

## <a name="custom-role-limits"></a>Egyéni szerepkör korlátai

Az alábbi lista az egyéni szerepkörökre vonatkozó korlátozásokat ismerteti.

- Minden címtárhoz legfeljebb **5000** egyéni szerepkör tartozhat.
- Az Azure Germany és az Azure China 21Vianet az egyes könyvtárak esetében akár 2000 egyéni szerepkört is tartalmazhat.
- `AssignableScopes`A gyökérszintű hatókör () nem állítható be `"/"` .
- A alkalmazásban nem használhatók helyettesítő karakterek ( `*` ) `AssignableScopes` . Ez a helyettesítő karakteres korlátozás segít biztosítani, hogy a felhasználók nem tudják elérni a hatókört a szerepkör-definíció frissítésével.
- Egyéni szerepkörben csak egyetlen felügyeleti csoportot lehet definiálni `AssignableScopes` . A felügyeleti csoport hozzáadása a `AssignableScopes` jelenleg előzetes verzióban érhető el.
- `DataActions`A felügyeleti csoport hatókörében nem lehet hozzárendelni egyéni szerepköröket.
- Azure Resource Manager nem ellenőrzi a felügyeleti csoport létezését a szerepkör-definíció hozzárendelhető hatókörében.

Az egyéni szerepkörökkel és felügyeleti csoportokkal kapcsolatos további információkért lásd: [erőforrások rendszerezése az Azure felügyeleti csoportjaival](../governance/management-groups/overview.md#azure-custom-role-definition-and-assignment).

## <a name="input-and-output-formats"></a>Bemeneti és kimeneti formátumok

Ha a parancssorból szeretne egyéni szerepkört létrehozni, általában a JSON használatával határozza meg az egyéni szerepkörhöz használni kívánt tulajdonságokat. A használt eszközöktől függően a bemeneti és a kimeneti formátumok némileg eltérőek lesznek. Ez a szakasz az eszköztől függően megadja a bemeneti és kimeneti formátumokat.

### <a name="azure-powershell"></a>Azure PowerShell

Ha Azure PowerShell használatával szeretne egyéni szerepkört létrehozni, a következő adatokat kell megadnia.

```json
{
  "Name": "",
  "Description": "",
  "Actions": [],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": []
}
```

Ha Azure PowerShell használatával szeretne egyéni szerepkört frissíteni, meg kell adnia a következő adatokat. Vegye figyelembe, hogy a `Id` tulajdonság hozzá lett adva. 

```json
{
  "Name": "",
  "Id": "",
  "Description": "",
  "Actions": [],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": []
}
```

Az alábbi ábrán egy példa látható a kimenetre, amikor Azure PowerShell és a [ConvertTo-JSON](/powershell/module/microsoft.powershell.utility/convertto-json) parancs használatával egyéni szerepkört listáz. 

```json
{
  "Name": "",
  "Id": "",
  "IsCustom": true,
  "Description": "",
  "Actions": [],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": []
}
```

### <a name="azure-cli"></a>Azure CLI

Ha egyéni szerepkört szeretne létrehozni vagy frissíteni az Azure CLI használatával, meg kell adnia a következő bemenetet. Ez a formátum ugyanaz, amikor egyéni szerepkört hoz létre Azure PowerShell használatával.

```json
{
  "Name": "",
  "Description": "",
  "Actions": [],
  "NotActions": [],
  "DataActions": [],
  "NotDataActions": [],
  "AssignableScopes": []
}
```

Az alábbi példa bemutatja a kimenetet, amikor egyéni szerepkört listáz az Azure CLI használatával.

```json
[
  {
    "assignableScopes": [],
    "description": "",
    "id": "",
    "name": "",
    "permissions": [
      {
        "actions": [],
        "dataActions": [],
        "notActions": [],
        "notDataActions": []
      }
    ],
    "roleName": "",
    "roleType": "CustomRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

### <a name="rest-api"></a>REST API

Egyéni szerepkör a REST API használatával történő létrehozásához vagy frissítéséhez a következő adatokat kell megadnia. Ez a formátum ugyanaz a formátum, amely akkor jön létre, amikor létrehoz egy egyéni szerepkört a Azure Portal használatával.

```json
{
  "properties": {
    "roleName": "",
    "description": "",
    "assignableScopes": [],
    "permissions": [
      {
        "actions": [],
        "notActions": [],
        "dataActions": [],
        "notDataActions": []
      }
    ]
  }
}
```

Az alábbi példa bemutatja a kimenetet, amikor egyéni szerepkört listáz a REST API használatával.

```json
{
    "properties": {
        "roleName": "",
        "type": "CustomRole",
        "description": "",
        "assignableScopes": [],
        "permissions": [
            {
                "actions": [],
                "notActions": [],
                "dataActions": [],
                "notDataActions": []
            }
        ],
        "createdOn": "",
        "updatedOn": "",
        "createdBy": "",
        "updatedBy": ""
    },
    "id": "",
    "type": "Microsoft.Authorization/roleDefinitions",
    "name": ""
}
```

## <a name="next-steps"></a>Következő lépések

- [Oktatóanyag: egyéni Azure-szerepkör létrehozása Azure PowerShell használatával](tutorial-custom-role-powershell.md)
- [Oktatóanyag: egyéni Azure-szerepkör létrehozása az Azure CLI használatával](tutorial-custom-role-cli.md)
- [Az Azure szerepkör-definíciók ismertetése](role-definitions.md)
- [Az Azure RBAC hibáinak megoldása](troubleshooting.md)
