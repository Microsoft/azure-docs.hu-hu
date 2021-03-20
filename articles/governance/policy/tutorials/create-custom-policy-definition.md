---
title: 'Oktatóanyag: egyéni szabályzat-definíció létrehozása'
description: Ebben az oktatóanyagban egy egyéni szabályzat-definíciót Azure Policy az Azure-erőforrásokra vonatkozó egyéni üzleti szabályok érvénybe léptetéséhez.
ms.date: 10/05/2020
ms.topic: tutorial
ms.openlocfilehash: 817e6f494b024b9a789f39a4101236f64d8fa0cd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97882891"
---
# <a name="tutorial-create-a-custom-policy-definition"></a>Oktatóanyag: egyéni szabályzat-definíció létrehozása

Az egyéni szabályzatok definíciója lehetővé teszi, hogy az ügyfelek definiálják saját szabályaikat az Azure használatához. Ezek a szabályok gyakran kényszerítik a következőket:

- Biztonsági eljárások
- Költségkezelés
- Szervezetre vonatkozó szabályok (például elnevezés vagy hely)

Függetlenül attól, hogy az üzleti illesztőprogram egyéni házirendet hoz létre, a lépések ugyanazok, mint az új egyéni házirend definiálásához.

Egyéni házirend létrehozása előtt tekintse meg a [szabályzat mintáit](../samples/index.md) , és ellenőrizze, hogy már létezik-e az igényeinek megfelelő szabályzat.

Az egyéni szabályzatok létrehozásának módszere az alábbi lépésekből áll:

> [!div class="checklist"]
> - Az üzleti követelmények meghatározása
> - Minden követelmény hozzárendelése egy Azure Resource tulajdonsághoz
> - A tulajdonság hozzárendelése aliashoz
> - A használni kívánt effektus meghatározása
> - A szabályzat definíciójának összeállítása

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

## <a name="identify-requirements"></a>Követelmények azonosítása

A házirend-definíció létrehozása előtt fontos megérteni a szabályzat céljait. Ebben az oktatóanyagban egy közös nagyvállalati biztonsági követelményt használunk, amely a következő lépéseket szemlélteti:

- Minden Storage-fióknak engedélyezve kell lennie a HTTPS-hez
- A Storage-fiókoknak le kell tiltaniuk a HTTP-t

A követelményeknek egyértelműen azonosítaniuk kell mind a "to", mind a "nem lehet" erőforrás-állapotot.

Noha meghatározta az erőforrás várható állapotát, még nem definiálta, hogy mit szeretne tenni a nem megfelelő erőforrásokkal. Azure Policy számos [effektust](../concepts/effects.md)támogat. Ebben az oktatóanyagban definiáljuk az üzleti követelményt, amely megakadályozza az erőforrások létrehozását, ha nem felelnek meg az üzleti szabályoknak. Ennek a célnak a teljesítéséhez a [Megtagadás](../concepts/effects.md#deny) effektust fogjuk használni. Azt is szeretnénk, hogy az adott hozzárendelésekre vonatkozó szabályzatot felfüggessze. Ezért a [letiltott](../concepts/effects.md#disabled) effektust fogjuk használni, és a [paramétereket](../concepts/definition-structure.md#parameters) a házirend-definícióban kell végrehajtani.

## <a name="determine-resource-properties"></a>Erőforrás-tulajdonságok meghatározása

Az üzleti követelménytől függően az Azure-erőforrás, amellyel a Azure Policy naplózni kívánja a Storage-fiókot. Azonban nem tudjuk, hogy milyen tulajdonságokat kell használni a házirend-definícióban. Azure Policy kiértékeli az erőforrás JSON-ábrázolását, ezért meg kell értenünk az adott erőforráshoz elérhető tulajdonságokat.

Az Azure-erőforrások tulajdonságai számos módon meghatározhatók. Ebben az oktatóanyagban a következőket fogjuk megtekinteni:

- Azure Policy-bővítmény VSCode-hoz
- Azure Resource Manager sablonok (ARM-sablonok)
  - Meglévő erőforrás exportálása
  - Létrehozási élmény
  - Gyorsindítás sablonok (GitHub)
  - Sablon-dokumentációs dokumentumok
- Azure Resource Explorer

### <a name="view-resources-in-vs-code-extension"></a>Erőforrások megtekintése a VS Code-bővítményben

A [vs Code bővítmény](../how-to/extension-for-vscode.md#search-for-and-view-resources) használatával böngészheti az erőforrásokat a környezetben, és megtekintheti a Resource Manager-tulajdonságokat az egyes erőforrásokon.

### <a name="arm-templates"></a>ARM-sablonok

A felügyelni kívánt tulajdonságot több módon [](../../../azure-resource-manager/templates/template-tutorial-use-template-reference.md) is megtekintheti.

#### <a name="existing-resource-in-the-portal"></a>Meglévő erőforrás a portálon

A tulajdonságok megkeresésének legegyszerűbb módja egy azonos típusú meglévő erőforrás megkeresése. A kényszeríteni kívánt beállítással már konfigurált erőforrások is megadják az összehasonlításhoz használandó értéket.
Tekintse meg a **sablon exportálása** lapot (a **Beállítások** alatt) az adott erőforráshoz tartozó Azure Portalban.

> [!WARNING]
> Az Azure Portal által exportált ARM-sablon nem csatlakoztatható egyenesen egy `deployment` ARM-sablon tulajdonságához egy [deployIfNotExists](../concepts/effects.md#deployifnotexists) házirend-definícióban.

:::image type="content" source="../media/create-custom-policy-definition/export-template.png" alt-text="A Azure Portal meglévő erőforrásának sablon exportálása oldalának képernyőképe." border="false":::

A Storage-fiók esetében a következőhöz hasonló sablon látható:

```json
...
"resources": [{
    "comments": "Generalized from resource: '/subscriptions/{subscriptionId}/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount'.",
    "type": "Microsoft.Storage/storageAccounts",
    "sku": {
        "name": "Standard_LRS",
        "tier": "Standard"
    },
    "kind": "Storage",
    "name": "[parameters('storageAccounts_mystorageaccount_name')]",
    "apiVersion": "2018-07-01",
    "location": "westus",
    "tags": {
        "ms-resource-usage": "azure-cloud-shell"
    },
    "scale": null,
    "properties": {
        "networkAcls": {
            "bypass": "AzureServices",
            "virtualNetworkRules": [],
            "ipRules": [],
            "defaultAction": "Allow"
        },
        "supportsHttpsTrafficOnly": false,
        "encryption": {
            "services": {
                "file": {
                    "enabled": true
                },
                "blob": {
                    "enabled": true
                }
            },
            "keySource": "Microsoft.Storage"
        }
    },
    "dependsOn": []
}]
...
```

A **Tulajdonságok** területen a **supportsHttpsTrafficOnly** nevű érték false ( **hamis**) értékűre van állítva. Ez a tulajdonság úgy tűnik, hogy a keresett tulajdonság lehet. Emellett az erőforrás **típusa** **Microsoft. Storage/storageAccounts**. A típus lehetővé teszi, hogy csak az ilyen típusú erőforrásokra korlátozza a szabályzatot.

#### <a name="create-a-resource-in-the-portal"></a>Erőforrás létrehozása a portálon

A portálon keresztül egy másik módszer az erőforrás-létrehozási élmény. Amikor a portálon keresztül hoz létre egy Storage-fiókot, a **speciális** lapon található lehetőség a **biztonságos átvitelre van szükség**. Ez a tulajdonság _letiltott_ és _engedélyezett_ beállításokat tartalmaz. Az info ikon további szöveggel rendelkezik, amely megerősíti, hogy ez a beállítás valószínűleg a kívánt tulajdonság. A portálon azonban nem jelennek meg a tulajdonságok neve ezen a képernyőn.

A **felülvizsgálat + létrehozás** lapon egy hivatkozás található az oldal alján egy **sablon az automatizáláshoz való letöltéséhez**. A hivatkozás kiválasztásával megnyílik a konfigurált erőforrást létrehozó sablon. Ebben az esetben két fő információt látunk:

```json
...
"supportsHttpsTrafficOnly": {
    "type": "bool"
}
...
"properties": {
    "accessTier": "[parameters('accessTier')]",
    "supportsHttpsTrafficOnly": "[parameters('supportsHttpsTrafficOnly')]"
}
...
```

Ez az információ azt jelzi, hogy a tulajdonság típusa és a **supportsHttpsTrafficOnly** is megerősíti a keresett tulajdonságot.

#### <a name="quickstart-templates-on-github"></a>Gyors útmutató sablonok a GitHubon

A GitHubon található Azure rövid útmutató- [sablonok](https://github.com/Azure/azure-quickstart-templates) több száz ARM-sablonból állnak, amelyek különböző erőforrásokhoz készültek. Ezek a sablonok nagyszerű módot biztosítanak a keresett erőforrás-tulajdonság megkeresésére. Előfordulhat, hogy egyes tulajdonságok úgy tűnik, hogy mit keres, de mást is vezérel.

#### <a name="resource-reference-docs"></a>Erőforrás-referenciák dokumentációja

A **supportsHttpsTrafficOnly** helyes tulajdonságának ellenőrzéséhez ellenőrizze a Storage- [fiók erőforrásának](/azure/templates/microsoft.storage/2018-07-01/storageaccounts) ARM-sablonra vonatkozó hivatkozását a tárolási szolgáltatón. A Properties objektum érvényes paraméterek listáját tartalmazza. Az [StorageAccountPropertiesCreateParameters-Object](/azure/templates/microsoft.storage/2018-07-01/storageaccounts#storageaccountpropertiescreateparameters-object) hivatkozás kiválasztásával egy elfogadható tulajdonságokat tartalmazó táblázat látható. **supportsHttpsTrafficOnly** jelennek meg, és a Leírás megfelel az üzleti igényeknek.

### <a name="azure-resource-explorer"></a>Azure Resource Explorer

Az Azure-erőforrások megismerésének egy másik módja a [Azure erőforrás-kezelő](https://resources.azure.com) (előzetes verzió). Ez az eszköz az előfizetés kontextusát használja, ezért az Azure-beli hitelesítő adataival kell hitelesítenie a webhelyet. A hitelesítés után a szolgáltatók, előfizetések, erőforráscsoportok és erőforrások között böngészhet.

Keresse meg a Storage-fiók erőforrását, és tekintse meg a tulajdonságokat. Itt a **supportsHttpsTrafficOnly** tulajdonság is látható. A **dokumentáció** lapon láthatjuk, hogy a tulajdonság leírása megegyezik a korábban a hivatkozási dokumentációban találhatókkal.

## <a name="find-the-property-alias"></a>A tulajdonság aliasnevének megkeresése

Azonosította az erőforrás-tulajdonságot, de a tulajdonságot egy [aliasra](../concepts/definition-structure.md#aliases)kell leképeznie.

Az Azure-erőforrások aliasait többféleképpen is meghatározhatja. Ebben az oktatóanyagban a következőket fogjuk megtekinteni:

- Azure Policy-bővítmény VSCode-hoz
- Azure CLI
- Azure PowerShell

### <a name="get-aliases-in-vs-code-extension"></a>Aliasok beolvasása a VS Code bővítménnyel

A VS Code bővítmény Azure Policy bővítménye megkönnyíti az erőforrások tallózását és az [aliasok felderítését](../how-to/extension-for-vscode.md#discover-aliases-for-resource-properties).

> [!NOTE]
> A VS Code bővítmény csak a Resource Manager-üzemmód tulajdonságait mutatja be, és nem jeleníti meg az [erőforrás-szolgáltatói mód](../concepts/definition-structure.md#mode) tulajdonságait.

### <a name="azure-cli"></a>Azure CLI

Az Azure CLI-ben a `az provider` parancs az erőforrás-aliasok keresésére szolgál. A **Microsoft. Storage** névterét a korábban az Azure-erőforrással kapcsolatban kapott részletek alapján szűrheti.

```azurecli-interactive
# Login first with az login if not using Cloud Shell

# Get Azure Policy aliases for type Microsoft.Storage
az provider show --namespace Microsoft.Storage --expand "resourceTypes/aliases" --query "resourceTypes[].aliases[].name"
```

Az eredmények között a **supportsHttpsTrafficOnly** nevű Storage-fiókok által támogatott alias látható. Ennek az aliasnak a létezése azt jelenti, hogy megírhatjuk a szabályzatot az üzleti követelmények érvénybe léptetéséhez.

### <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell a parancsmag az `Get-AzPolicyAlias` erőforrás-aliasok keresésére szolgál. A **Microsoft. Storage** névterét a korábban az Azure-erőforrással kapcsolatban kapott részletek alapján szűrheti.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Use Get-AzPolicyAlias to list aliases for Microsoft.Storage
(Get-AzPolicyAlias -NamespaceMatch 'Microsoft.Storage').Aliases
```

Az Azure CLI-hez hasonlóan az eredmények a **supportsHttpsTrafficOnly** nevű Storage-fiókok által támogatott aliast jelenítik meg.

## <a name="determine-the-effect-to-use"></a>A használandó effektus meghatározása

Annak eldöntése, hogy mi a teendő a nem megfelelő erőforrásokkal, majdnem olyan fontos, mint az első helyen való kiértékelés eldöntése. A nem megfelelő erőforrásokra adott lehetséges válaszokat a rendszer [effektusnak](../concepts/effects.md)nevezzük. A hatás azt szabályozza, hogy a nem megfelelő erőforrás van-e naplózva, letiltva, van-e hozzáfűzve vagy van-e hozzárendelve az erőforrás megfelelő állapotba helyezéséhez szükséges központi telepítéshez.

A megtagadási példa a mi is az, ha nem szeretnénk, hogy az Azure-környezetben létrehozott nem megfelelő erőforrások ne legyenek elérhetők. A naplózás jó választás a házirendek hatására, hogy megtudja, milyen hatással van a szabályzat, mielőtt a rendszer megtagadja a beállítást. Az egyes hozzárendelések hatásának megváltoztatásának egyik módja a parametrizálja. A részletekért lásd az alábbi [paramétereket](#parameters) .

## <a name="compose-the-definition"></a>A definíció összeállítása

Most már megtörtént a tulajdonságok részletei és aliasa a felügyelni kívánt tervekhez. Ezután saját maga alkotja a házirend-szabályt. Ha még nem ismeri a házirend nyelvét, a hivatkozási szabályzat [definíciójának struktúrája](../concepts/definition-structure.md) a házirend-definíció strukturálása. Itt látható egy üres sablon arról, hogy a szabályzat definíciója hogyan néz ki:

```json
{
    "properties": {
        "displayName": "<displayName>",
        "description": "<description>",
        "mode": "<mode>",
        "parameters": {
                <parameters>
        },
        "policyRule": {
            "if": {
                <rule>
            },
            "then": {
                "effect": "<effect>"
            }
        }
    }
}
```

### <a name="metadata"></a>Metaadatok

Az első három összetevő a szabályzat metaadatai. Ezek az összetevők egyszerűen biztosítanak értékeket, mert tudjuk, mi hozza létre a szabályt. A [Mode](../concepts/definition-structure.md#mode) elsődlegesen a címkék és az erőforrás helye. Mivel nem kell a címkéket támogató erőforrásokra korlátozni a kiértékelést, a _minden_ értéket használni fogjuk **.**

```json
"displayName": "Deny storage accounts not using only HTTPS",
"description": "Deny storage accounts not using only HTTPS. Checks the supportsHttpsTrafficOnly property on StorageAccounts.",
"mode": "all",
```

### <a name="parameters"></a>Paraméterek

Habár nem használunk paramétert a kiértékelés módosításához, egy paraméterrel szeretnénk engedélyezni a hibaelhárítási **effektus** módosítását. Definiálunk egy **effectType** paramétert, és csak a **Megtagadás** és a **Letiltva** értékre korlátozzuk. Ez a két lehetőség megfelel az üzleti követelményeknek. A befejezett paraméterek blokk a következő példához hasonlít:

```json
"parameters": {
    "effectType": {
        "type": "string",
        "defaultValue": "Deny",
        "allowedValues": [
            "Deny",
            "Disabled"
        ],
        "metadata": {
            "displayName": "Effect",
            "description": "Enable or disable the execution of the policy"
        }
    }
},
```

### <a name="policy-rule"></a>Házirend-szabály

A házirend- [szabály](../concepts/definition-structure.md#policy-rule) összeállítása az egyéni szabályzat-definíció kiépítése utolsó lépése. A teszteléshez két utasítást azonosítottak:

- A Storage-fiók **típusa** **Microsoft. Storage/storageAccounts**
- A Storage-fiók **supportsHttpsTrafficOnly** nem **igaz**

Mivel mindkét utasításnak igaz értékűnek kell lennie, a **allOf** [logikai operátort](../concepts/definition-structure.md#logical-operators)használjuk. Statikus deklaráció helyett a **effectType** paramétert adjuk át a hatásnak. A befejezett szabály a következő példához hasonlít:

```json
"if": {
    "allOf": [
        {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
        },
        {
            "field": "Microsoft.Storage/storageAccounts/supportsHttpsTrafficOnly",
            "notEquals": "true"
        }
    ]
},
"then": {
    "effect": "[parameters('effectType')]"
}
```

### <a name="completed-definition"></a>Befejezett definíció

A szabályzat mindhárom részének meghatározása után itt látható a befejezett definíció:

```json
{
    "properties": {
        "displayName": "Deny storage accounts not using only HTTPS",
        "description": "Deny storage accounts not using only HTTPS. Checks the supportsHttpsTrafficOnly property on StorageAccounts.",
        "mode": "all",
        "parameters": {
            "effectType": {
                "type": "string",
                "defaultValue": "Deny",
                "allowedValues": [
                    "Deny",
                    "Disabled"
                ],
                "metadata": {
                    "displayName": "Effect",
                    "description": "Enable or disable the execution of the policy"
                }
            }
        },
        "policyRule": {
            "if": {
                "allOf": [
                    {
                        "field": "type",
                        "equals": "Microsoft.Storage/storageAccounts"
                    },
                    {
                        "field": "Microsoft.Storage/storageAccounts/supportsHttpsTrafficOnly",
                        "notEquals": "true"
                    }
                ]
            },
            "then": {
                "effect": "[parameters('effectType')]"
            }
        }
    }
}
```

Az elkészült definíció használatával új szabályzat hozható létre. A portál és az egyes SDK-kat (Azure CLI, Azure PowerShell és REST API) különböző módokon fogadja el a definíciót, ezért tekintse át az egyes parancsokat a helyes használat ellenőrzéséhez. Ezután rendelje hozzá a paraméteres hatás használatával a megfelelő erőforrásokhoz a Storage-fiókok biztonságának kezeléséhez.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha végzett az oktatóanyag erőforrásainak használatával, a következő lépésekkel törölheti a fent létrehozott hozzárendeléseket vagy definíciókat:

1. Válassza a **definíciók** **(vagy** hozzárendelések) lehetőséget a Azure Policy lap bal oldalán található **authoring (szerzői műveletek** ) elemnél.

1. Keresse meg az eltávolítani kívánt új kezdeményezést vagy szabályzatdefiníciót (vagy hozzárendelést).

1. Kattintson a jobb gombbal a sorra, vagy a bal gombbal a definíció (vagy a hozzárendelés) mellett található három pontra, majd a **Definíció törlése** (vagy a **Hozzárendelés törlése**) parancsra.

## <a name="review"></a>Áttekintés

Ebben az oktatóanyagban sikeresen elvégezte a következőket:

> [!div class="checklist"]
> - Az üzleti igények azonosítása
> - Az egyes követelmények leképezve egy Azure Resource tulajdonsághoz
> - A tulajdonság hozzárendelése egy aliashoz
> - A használandó effektus meghatározása
> - A szabályzat definíciójának tagjai

## <a name="next-steps"></a>Következő lépések

Ezután az egyéni házirend-definíció használatával hozzon létre és rendeljen hozzá egy házirendet:

> [!div class="nextstepaction"]
> [Szabályzat-definíció létrehozása és társítása](../how-to/programmatically-create.md#create-and-assign-a-policy-definition)
