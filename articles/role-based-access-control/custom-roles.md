---
title: Egyéni Azure-szerepkörök – Azure RBAC
description: Megtudhatja, hogyan hozhat létre egyéni Azure-szerepköröket az Azure szerepköralapú hozzáférés-vezérlésével (Azure RBAC) az Azure-erőforrások hozzáférés-felügyeletének finomhangolt kezeléséhez.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: conceptual
ms.workload: identity
ms.date: 12/15/2020
ms.author: rolyon
ms.openlocfilehash: 9779c2a269902d856d1639ce78028d0e658656bb
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107479831"
---
# <a name="azure-custom-roles"></a>Egyéni Azure-szerepkörök

> [!IMPORTANT]
> A felügyeleti csoport hozzáadása jelenleg `AssignableScopes` előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ha az [Azure beépített szerepkörei](built-in-roles.md) nem felelnie meg a szervezet igényeinek, létrehozhatja saját egyéni szerepköreit. A beépített szerepkörökhöz hasonlóan egyéni szerepköröket is hozzárendelhet felhasználókhoz, csoportokhoz és szolgáltatásnévhez a felügyeleti csoport (csak előzetes verzióban), az előfizetés és az erőforráscsoport hatókörében.

Az egyéni szerepkörök megoszthatóak olyan előfizetések között, amelyek megbíznak ugyanabban az Azure AD-címtárban. Címtáranként **5000** egyéni szerepkör van korlátozva. (Az Azure Germany és Azure China 21Vianet esetében a korlát 2000 egyéni szerepkör.) Egyéni szerepkörök a következő szolgáltatásokkal Azure Portal, Azure PowerShell Azure CLI-vel vagy a REST API.

## <a name="steps-to-create-a-custom-role"></a>Egyéni szerepkör létrehozásához szükséges lépések

Az egyéni szerepkör létrehozásához szükséges alapvető lépések a következőek.

1. Határozza meg a szükséges engedélyeket.

    Egyéni szerepkör létrehozásakor meg kell határoznia az engedélyek meghatározásához rendelkezésre álló műveleteket. Általában egy meglévő beépített szerepkörből indul ki, majd az igényeinek megfelelően módosítja azt. A műveleteket hozzá fogja adni a szerepkör-definíció `Actions` vagy `NotActions` [tulajdonságaihoz.](role-definitions.md) Ha rendelkezik adatműveletekkel, ezeket hozzá kell adni a vagy a `DataActions` `NotDataActions` tulajdonsághoz.

    További információért tekintse meg a szükséges engedélyek meghatározásának [következő szakaszát.](#how-to-determine-the-permissions-you-need)

1. Döntse el, hogyan szeretné létrehozni az egyéni szerepkört.

    Egyéni szerepköröket a következő Azure Portal [hozhat](custom-roles-portal.md) [Azure PowerShell,](custom-roles-powershell.md) [az Azure CLI](custom-roles-cli.md)vagy a [REST API.](custom-roles-rest.md)

1. Hozza létre az egyéni szerepkört.

    A legegyszerűbb módszer a Azure Portal. Az egyéni szerepkörök létrehozásáról a Azure Portal az Egyéni [Azure-szerepkörök](custom-roles-portal.md)létrehozása vagy frissítése a Azure Portal.

1. Az egyéni szerepkör tesztelése.

    Ha már megvan az egyéni szerepköre, tesztelnie kell, hogy a vártnak megfelelő-e. Ha később módosításokat kell végeznie, frissítheti az egyéni szerepkört.

## <a name="how-to-determine-the-permissions-you-need"></a>A szükséges engedélyek meghatározása

Az Azure több ezer olyan engedéllyel rendelkezik, amelyek az egyéni szerepkörbe foglalhatóak. Az alábbi módszerek segíthetnek meghatározni az egyéni szerepkörhöz hozzáadni kívánt engedélyeket:

- Nézze meg a [meglévő beépített szerepköröket.](built-in-roles.md)

    Előfordulhat, hogy módosítani szeretne egy meglévő szerepkört, vagy kombinálni szeretné a több szerepkörben használt engedélyeket.

- Sorolja fel azokat az Azure-szolgáltatásokat, amelyekhez hozzáférést kíván biztosít.

- Határozza meg [az Azure-szolgáltatásokhoz leképező erőforrás-szolgáltatókat.](../azure-resource-manager/management/azure-services-resource-providers.md)

    Az Azure-szolgáltatások funkciójukat és engedélyeiket az erőforrás-szolgáltatókon [keresztül teszi elérhetővé.](../azure-resource-manager/management/overview.md) A Microsoft.Compute erőforrás-szolgáltató például virtuálisgép-erőforrásokat, a Microsoft.Billing erőforrás-szolgáltató pedig előfizetési és számlázási erőforrásokat biztosít. Az erőforrás-szolgáltatók ismerete segíthet leszűkíteni és meghatározni az egyéni szerepkörhöz szükséges engedélyeket.

    Amikor egyéni szerepkört hoz létre a Azure Portal, kulcsszavak kereséssel is meghatározhatja az erőforrás-szolgáltatókat. Ennek a keresési funkciónak a leírását az [Egyéni Azure-szerepkörök](custom-roles-portal.md#step-4-permissions)létrehozása vagy frissítése a Azure Portal.

    ![Engedélyek hozzáadása panel erőforrás-szolgáltatóval](./media/custom-roles-portal/add-permissions-provider.png)

- Az elérhető [engedélyek között keresse](resource-provider-operations.md) meg a kívánt engedélyeket.

    Amikor egyéni szerepkört hoz létre a Azure Portal, kulcsszó alapján kereshet rá az engedélyekre. Kereshet például virtuális gépre *vagy* számlázási *engedélyekre.* Az engedélyeket CSV-fájlként is letöltheti, majd ebben a fájlban kereshet. Ennek a keresési funkciónak a leírását az [Egyéni Azure-szerepkörök](custom-roles-portal.md#step-4-permissions)létrehozása vagy frissítése a Azure Portal.

    ![Engedélylista hozzáadása](./media/custom-roles-portal/add-permissions-list.png)

## <a name="custom-role-example"></a>Példa egyéni szerepkörre

Az alábbiakban egy egyéni szerepkör látható, ahogyan az Azure PowerShell JSON formátumban jelenik meg. Ez az egyéni szerepkör a virtuális gépek monitorozására és újraindítására használható.

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

Az alábbiakban az Azure CLI használatával megjelenített egyéni szerepkör látható.

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

Amikor létrehoz egy egyéni szerepkört, az megjelenik a Azure Portal egy narancssárga erőforrás ikonnal.

![Egyéni szerepkör ikon](./media/custom-roles/roles-custom-role-icon.png)

## <a name="custom-role-properties"></a>Egyéni szerepkör tulajdonságai

Az alábbi táblázat ismerteti, hogy mit jelentenek az egyéni szerepkör tulajdonságai.

| Tulajdonság | Kötelező | Típus | Leírás |
| --- | --- | --- | --- |
| `Name`</br>`roleName` | Igen | Sztring | Az egyéni szerepkör megjelenített neve. Bár a szerepkör-definíció egy felügyeleti csoport vagy előfizetés szintű erőforrás, egy szerepkör-definíció több olyan előfizetésben is használható, amelyek ugyanazt az Azure AD-címtárat használják. Ennek a megjelenített névnek egyedinek kell lennie az Azure AD-címtár hatókörében. Betűket, számokat, szóközöket és speciális karaktereket tartalmazhat. A karakterek maximális száma 128. |
| `Id`</br>`name` | Igen | Sztring | Az egyéni szerepkör egyedi azonosítója. A Azure PowerShell Azure CLI-hez és az Azure CLI-hez a rendszer automatikusan létrehozza ezt az azonosítót, amikor új szerepkört hoz létre. |
| `IsCustom`</br>`roleType` | Igen | Sztring | Azt jelzi, hogy egyéni szerepkörről van-e szó. Egyéni `true` szerepkörökhöz állítsa a vagy `CustomRole` a beállítását. A beépített szerepkörökhöz állítsa vagy a `false` `BuiltInRole` következőt: . |
| `Description`</br>`description` | Igen | Sztring | Az egyéni szerepkör leírása. Betűket, számokat, szóközöket és speciális karaktereket tartalmazhat. A karakterek maximális száma 1024. |
| `Actions`</br>`actions` | Igen | Sztring[] | Sztringek tömbje, amely meghatározza a szerepkör által elvégezhető felügyeleti műveleteket. További információ: [Műveletek.](role-definitions.md#actions) |
| `NotActions`</br>`notActions` | Nem | Sztring[] | Sztringek tömbje, amely meghatározza az engedélyezettből kizárt felügyeleti `Actions` műveleteket. További információ: [NotActions.](role-definitions.md#notactions) |
| `DataActions`</br>`dataActions` | Nem | Sztring[] | Sztringek tömbje, amely meghatározza azokat az adatműveleteket, amelyek a szerepkör számára lehetővé teszik az adatokon való végrehajtásához az adott objektumon belül. Ha a segítségével hoz létre egyéni szerepkört, az nem rendelhető hozzá `DataActions` a felügyeleti csoport hatókörében. További információ: [DataActions.](role-definitions.md#dataactions) |
| `NotDataActions`</br>`notDataActions` | Nem | Sztring[] | Sztringek tömbje, amely meghatározza az engedélyezettből kizárt adatműveleteket. `DataActions` További információ: [NotDataActions.](role-definitions.md#notdataactions) |
| `AssignableScopes`</br>`assignableScopes` | Igen | Sztring[] | Sztringek tömbje, amely meghatározza azokat a hatóköreket, amelyek hozzárendelése az egyéni szerepkör számára elérhető. Egy egyéni szerepkörben csak egy felügyeleti csoportot `AssignableScopes` definiálhat. A felügyeleti csoport hozzáadása jelenleg `AssignableScopes` előzetes verzióban érhető el. További információ: [AssignableScopes.](role-definitions.md#assignablescopes) |

## <a name="wildcard-permissions"></a>Helyettesítő karakterek engedélyei

`Actions`, `NotActions` , és támogatja a helyettesítő `DataActions` `NotDataActions` karaktereket ( ) az engedélyek `*` meghatározásához. A helyettesítő karakter () minden olyan műveletre kiterjeszti az engedélyt, amely megfelel az Ön `*` által adott műveletsringnek. Tegyük fel például, hogy a hozzáféréshez és az exportáláshoz kapcsolódó összes Azure Cost Management hozzá. Az alábbi műveletsringeket is hozzáadhatja:

```
Microsoft.CostManagement/exports/action
Microsoft.CostManagement/exports/read
Microsoft.CostManagement/exports/write
Microsoft.CostManagement/exports/delete
Microsoft.CostManagement/exports/run/action
```

Az összes sztring hozzáadása helyett hozzáadhat egy helyettesítő karakteres sztringet is. Az alábbi helyettesítő karakteres sztring például az előző öt sztringnek felel meg. Ez a jövőbeli exportálási engedélyekre is kiterjed, amelyek hozzáadhatók.

```
Microsoft.CostManagement/exports/*
```

Egy sztringben több helyettesítő karakter is lehet. Az alábbi sztring például az összes lekérdezési engedélyt Cost Management.

```
Microsoft.CostManagement/*/query/*
```

## <a name="who-can-create-delete-update-or-view-a-custom-role"></a>Ki hozhat létre, törölhet, frissíthet vagy megtekinthet egyéni szerepkört?

A beépített szerepkörökhöz hasonló tulajdonság határozza meg, hogy a szerepkör milyen hatókörökhöz érhető `AssignableScopes` el hozzárendeléshez. Az egyéni szerepkör tulajdonsága azt is szabályozza, hogy ki hozhat `AssignableScopes` létre, törölhet, frissíthet vagy megtekinthet egyéni szerepkört.

| Feladat | Művelet | Leírás |
| --- | --- | --- |
| Egyéni szerepkör létrehozása/törlése | `Microsoft.Authorization/ roleDefinitions/write` | Azok a felhasználók, akik az összes egyéni szerepkörhöz megadták ezt a műveletet, létrehozhatnak (vagy törölhet) egyéni szerepköröket az adott `AssignableScopes` hatókörökben való használatra. Például a [felügyeleti](built-in-roles.md#owner) csoportok, előfizetések és erőforráscsoportok tulajdonosai és felhasználói hozzáférés-rendszergazdái. [](built-in-roles.md#user-access-administrator) |
| Egyéni szerepkörök frissítése | `Microsoft.Authorization/ roleDefinitions/write` | Azok a felhasználók, akik az összes egyéni szerepkörhöz megadták ezt a műveletet, frissítheti az egyéni `AssignableScopes` szerepköröket az adott hatókörökben. Például a [felügyeleti](built-in-roles.md#owner) csoportok, előfizetések és erőforráscsoportok tulajdonosai és felhasználói hozzáférés-rendszergazdái. [](built-in-roles.md#user-access-administrator) |
| Egyéni szerepkör megtekintése | `Microsoft.Authorization/ roleDefinitions/read` | Azok a felhasználók, akik egy hatókörben kapják meg ezt a műveletet, megtekinthetik az adott hatókörhöz hozzárendelhető egyéni szerepköröket. Az összes beépített szerepkör elérhetővé teszi az egyéni szerepköröket a hozzárendeléshez. |

## <a name="custom-role-limits"></a>Egyéni szerepkör korlátai

Az alábbi lista az egyéni szerepkörök korlátait ismerteti.

- Minden címtár legfeljebb **5000 egyéni szerepkört** kaphat.
- Az Azure Germany Azure China 21Vianet címtárak legfeljebb 2000 egyéni szerepkört használhatnak.
- Nem állíthatja `AssignableScopes` be a gyökérhatókört ( `"/"` ).
- A-ban nem használhat helyettesítő karaktereket ( `*` `AssignableScopes` ). Ez a helyettesítő karakteres korlátozás segít biztosítani, hogy a felhasználó ne fér hozzá egy hatókörhöz a szerepkör-definíció frissítésével.
- Egy egyéni szerepkörben csak egy felügyeleti csoportot `AssignableScopes` definiálhat. A felügyeleti csoport hozzáadása jelenleg `AssignableScopes` előzetes verzióban érhető el.
- Az egyéni `DataActions` szerepkörei nem rendelhetők hozzá a felügyeleti csoport hatókörében.
- Azure Resource Manager nem ellenőrzi a felügyeleti csoport meglétét a szerepkör-definíció hozzárendelhető hatókörében.

További információ az egyéni szerepkörökről és felügyeleti csoportokról: [Erőforrások rendszerezése Azure-beli felügyeleti csoportokkal.](../governance/management-groups/overview.md#azure-custom-role-definition-and-assignment)

## <a name="input-and-output-formats"></a>Bemeneti és kimeneti formátumok

Ha a parancssor használatával szeretne egyéni szerepkört létrehozni, általában JSON használatával adhatja meg az egyéni szerepkör kívánt tulajdonságait. A használt eszközöktől függően a bemeneti és kimeneti formátumok kissé eltérőek lesznek. Ez a szakasz az eszköztől függően felsorolja a bemeneti és kimeneti formátumokat.

### <a name="azure-powershell"></a>Azure PowerShell

Egyéni szerepkör létrehozásához a Azure PowerShell meg kell adnia a következő bemenetet.

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

Ha egyéni szerepkört a Azure PowerShell frissíteni, meg kell adnia a következő bemenetet. Figyelje `Id` meg, hogy a tulajdonság hozzá lett adva. 

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

Az alábbiakban egy példa látható a kimenetre, amikor egyéni szerepkört listához ad meg a Azure PowerShell és a [ConvertTo-Json paranccsal.](/powershell/module/microsoft.powershell.utility/convertto-json) 

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

Ha egyéni szerepkört hoz létre vagy frissít az Azure CLI használatával, meg kell adnia a következő bemenetet. Ez a formátum ugyanaz a formátum, mint amikor egyéni szerepkört hoz létre a Azure PowerShell.

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

Az alábbiakban egy példát mutatunk be a kimenetre, amikor egyéni szerepkört sorol fel az Azure CLI használatával.

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

Egyéni szerepkör létrehozásához vagy frissítéséhez a REST API meg kell adnia a következő bemenetet. Ez a formátum megegyezik a formátummal, amely akkor jön létre, amikor egyéni szerepkört hoz létre a Azure Portal.

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

Az alábbiakban egy példát mutatunk be a kimenetre, amikor egyéni szerepkört sorol fel a REST API.

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

- [Oktatóanyag: Egyéni Azure-szerepkör létrehozása a Azure PowerShell](tutorial-custom-role-powershell.md)
- [Oktatóanyag: Egyéni Azure-szerepkör létrehozása az Azure CLI használatával](tutorial-custom-role-cli.md)
- [Az Azure-beli szerepkör-definíciók](role-definitions.md)
- [Az Azure RBAC hibaelhárítása](troubleshooting.md)
