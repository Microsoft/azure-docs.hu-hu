---
title: Azure Policy-erőforrások exportálása
description: Megtudhatja, hogyan exportálhat Azure Policy erőforrásokat a GitHubra, például házirend-definíciók és szabályzat-hozzárendelések számára.
ms.date: 10/29/2020
ms.topic: how-to
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 923b063244f6f47def1c3e6a63d6e4d6b3b88083
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94919565"
---
# <a name="export-azure-policy-resources"></a>Azure Policy-erőforrások exportálása

Ez a cikk a meglévő Azure Policy erőforrásainak exportálásáról nyújt információt. Az erőforrások exportálása hasznos és ajánlott a biztonsági mentéshez, de fontos lépés a felhő irányításával és a [szabályzatok szerinti kód](../concepts/policy-as-code.md)kezelésével is. Azure Policy erőforrások exportálhatók a [Azure Portal](#export-with-azure-portal), az [Azure CLI](#export-with-azure-cli), a [Azure PowerShell](#export-with-azure-powershell)és a támogatott SDK-k használatával.

## <a name="export-with-azure-portal"></a>Exportálás Azure Portal

A házirend-definíció Azure Portalból való exportálásához kövesse az alábbi lépéseket:

1. Indítsa el az Azure Policy szolgáltatást az Azure Portalon. Ehhez kattintson a **Minden szolgáltatás** elemre, majd keresse meg, és válassza ki a **Szabályzat** elemet.

1. A Azure Policy lap bal oldalán válassza a **definíciók** lehetőséget.

1. Használja a **definíciók exportálása** gombot, vagy válasszon egy házirend-definíció sorában található három pontot, majd válassza az **Exportálás definícióját**.

1. Válassza a **Bejelentkezés a GitHub** gombbal lehetőséget. Ha még nem hitelesítette a GitHubot, hogy engedélyezze Azure Policy az erőforrás exportálását, tekintse át a megnyíló új ablakban a [GitHub-műveletre](https://github.com/features/actions) vonatkozó igényeket, és válassza a **AzureGitHubActions engedélyezése** lehetőséget az exportálási folyamat folytatásához. Ha elkészült, az új ablak saját bezárása.

1. Az **alapvető** beállítások lapon adja meg a következő beállításokat, majd válassza a **házirendek** lapot vagy a **Tovább: házirendek** gombot az oldal alján.

   - **Adattár szűrője**: állítsa a saját _Tárházak_ lehetőségre, hogy csak az Ön tulajdonában lévő adattárakat, illetve az _összes tárházat_ lássuk, hogy a GitHub-művelethez való hozzáférése megtörténjen.
   - **Adattár**: állítsa be azt a tárházat, amelybe exportálni szeretné a Azure Policy erőforrásokat.
   - **Ág**: az ág beállítása a tárházban. Az alapértelmezetttől eltérő ág használata jó módszer a frissítések érvényesítésére, mielőtt a forráskódba összevonja őket.
   - **Könyvtár**: a _legfelső szintű mappa_ , amelybe exportálni szeretné a Azure Policy erőforrásokat. A könyvtár alatti almappákat a rendszer az exportált erőforrások alapján hozza létre.

1. A **házirendek** lapon állítsa be a keresési hatókört úgy, hogy kijelöli a három pontot, és kiválasztja a felügyeleti csoportok, előfizetések vagy erőforráscsoportok kombinációját.
   
1. A **házirend-definíció (k) hozzáadása** gombbal keresheti meg azt a hatókört, amelybe exportálni kívánja az objektumokat. A megnyíló oldalsó ablakban válassza ki az exportálandó objektumokat. Szűrje a kijelölést a keresőmező vagy a típus alapján. Miután kiválasztotta az összes exportálandó objektumot, használja az oldal alján található **Hozzáadás** gombot.

1. Minden kiválasztott objektumnál válassza ki a kívánt exportálási beállításokat, például a _definíciót_ vagy a definíciót, valamint a házirend-definícióhoz tartozó _hozzárendelés (eke)_ t. Ezután válassza a **felülvizsgálat + exportálás** lapot vagy a **Tovább: felülvizsgálat + exportálás** gombot az oldal alján.

   > [!NOTE]
   > Ha a beállítás _definícióját és a hozzárendelés (eke)_ t választja, csak a szűrő által beállított hatókörön belüli szabályzat-hozzárendelések lesznek exportálva.

1. A **felülvizsgálat + exportálás** lapon ellenőrizze a részletek egyezését, majd használja az oldal alján található **Exportálás** gombot.

1. Tekintse meg a GitHub-tárház, az ág és a _gyökér szintű mappát_ , és ellenőrizze, hogy a kiválasztott erőforrások mostantól a verziókövetés felé lettek-e exportálva.

A Azure Policy-erőforrások a kiválasztott GitHub-adattár és a _gyökér szintű mappa_ következő struktúrájába lesznek exportálva:

```text
|
|- <root level folder>/  ________________ # Root level folder set by Directory property
|  |- policies/  ________________________ # Subfolder for policy objects
|     |- <displayName>_<name>____________ # Subfolder based on policy displayName and name properties
|        |- policy.json _________________ # Policy definition
|        |- assign.<displayName>_<name>__ # Each assignment (if selected) based on displayName and name properties
|
```

## <a name="export-with-azure-cli"></a>Exportálás az Azure CLI-vel

Azure Policy definíciók, kezdeményezések és hozzárendelések mindegyike JSON-ként exportálható az [Azure CLI](/cli/azure/install-azure-cli)használatával. Ezek a parancsok egy **Name** paraméterrel határozzák meg, hogy melyik objektum kapja meg a JSON-t. A **Name** tulajdonság gyakran _GUID_ , és nem az objektum **DisplayName** .

- Definíció – [az Policy definition show](/cli/azure/policy/definition#az_policy_definition_show)
- Kezdeményezés – [az Policy set-definition show](/cli/azure/policy/set-definition#az_policy_set_definition_show)
- Hozzárendelés – [az Policy-hozzárendelés megjelenítése](/cli/azure/policy/assignment#az_policy_assignment_show)

Íme egy példa arra, hogyan történik a JSON beszerzése egy _VirtualMachineStorage_ **nevű** szabályzat-definícióhoz:

```azurecli-interactive
az policy definition show --name 'VirtualMachineStorage'
```

## <a name="export-with-azure-powershell"></a>Exportálás Azure PowerShell

Azure Policy a definíciók, a kezdeményezések és a hozzárendelések ugyanúgy exportálhatók JSON-ként, [Azure PowerShell](/powershell/azure/). Ezen parancsmagok mindegyike egy **Name** paramétert használ annak megadásához, hogy melyik objektumot kapja meg a JSON. A **Name** tulajdonság gyakran _GUID_ , és nem az objektum **DisplayName** .

- Definíció – [Get-AzPolicyDefinition](/powershell/module/az.resources/get-azpolicydefinition)
- Kezdeményezés – [Get-AzPolicySetDefinition](/powershell/module/az.resources/get-azpolicysetdefinition)
- Hozzárendelés – [Get-AzPolicyAssignment](/powershell/module/az.resources/get-azpolicyassignment)

Íme egy példa arra, hogyan történik a JSON beszerzése egy _VirtualMachineStorage_ **nevű** szabályzat-definícióhoz:

```azurepowershell-interactive
Get-AzPolicyDefinition -Name 'VirtualMachineStorage' | ConvertTo-Json -Depth 10
```

## <a name="next-steps"></a>Következő lépések

- Tekintse át a példákat [Azure Policy mintákon](../samples/index.md).
- Tekintse meg az [Azure szabályzatdefiníciók struktúrája](../concepts/definition-structure.md) szakaszt.
- A [Szabályzatok hatásainak ismertetése](../concepts/effects.md).
- Megtudhatja, hogyan [hozhat létre programozott módon házirendeket](programmatically-create.md).
- Ismerje meg, hogyan javíthatja a [nem megfelelő erőforrásokat](remediate-resources.md).
- Tekintse át, hogy a felügyeleti csoport hogyan [rendezi az erőforrásokat az Azure felügyeleti csoportjaival](../../management-groups/overview.md).
