---
title: Szerepkörök kezelése a munkaterületen
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan férhet hozzá egy Azure Machine Learning munkaterülethez az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használatával.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: Blackmist
ms.author: nigup
author: nishankgu
ms.date: 01/20/2020
ms.custom: how-to, seodec18, devx-track-azurecli, contperf-fy21q2
ms.openlocfilehash: bdd59c80408910bf8ca51bf787c8ff15dc2a4893
ms.sourcegitcommit: a67b972d655a5a2d5e909faa2ea0911912f6a828
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104889756"
---
# <a name="manage-access-to-an-azure-machine-learning-workspace"></a>Azure Machine Learning-munkaterülethez való hozzáférés kezelése

Ebből a cikkből megtudhatja, hogyan kezelheti a hozzáférés (Engedélyezés) Azure Machine Learning munkaterületre. Az Azure [szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../role-based-access-control/overview.md) az Azure-erőforrásokhoz való hozzáférés kezelésére szolgál, például új erőforrások létrehozására vagy a meglévők használatára. A Azure Active Directoryban (Azure AD) lévő felhasználók meghatározott szerepköröket kapnak, amelyek hozzáférést biztosítanak az erőforrásokhoz. Az Azure beépített szerepköröket és egyéni szerepkörök létrehozását is lehetővé teszi.

> [!TIP]
> Ez a cikk a Azure Machine Learningre összpontosít, és az Azure ML-re támaszkodó egyes szolgáltatások biztosítják a saját RBAC-beállításait. A cikkben szereplő információk alapján például beállíthatja, hogy kik küldhetnek pontozási kérelmeket webszolgáltatásként üzembe helyezett modellbe az Azure Kubernetes szolgáltatásban. Az Azure Kubernetes szolgáltatás azonban saját Azure-szerepköröket is biztosít. A szolgáltatással kapcsolatos olyan RBAC, amelyek hasznosak lehetnek a Azure Machine Learninghoz, tekintse meg az alábbi hivatkozásokat:
>
> * [Az Azure Kubernetes-fürt erőforrásaihoz való hozzáférés szabályozása](../aks/azure-ad-rbac.md)
> * [Az Azure RBAC használata az Kubernetes-hitelesítéshez](../aks/manage-azure-rbac.md)
> * [Az Azure RBAC használata a Blobok eléréséhez](../storage/common/storage-auth-aad-rbac-portal.md)

> [!WARNING]
> Egyes szerepkörök alkalmazása a Azure Machine Learning Studióban korlátozhatja a felhasználói felület funkcióit más felhasználók számára. Ha például egy felhasználó szerepköre nem képes számítási példányt létrehozni, a számítási példány létrehozásának lehetősége nem lesz elérhető a Studióban. Ez a viselkedés várható, és megakadályozza, hogy a felhasználó megkísérelje a hozzáférés-megtagadási hibát visszaadó műveleteket.

## <a name="default-roles"></a>Alapértelmezett szerepkörök

Az Azure Machine Learning-munkaterület egy Azure-erőforrás. A többi Azure-erőforráshoz hasonlóan az új Azure Machine Learning-munkaterületek is három alapértelmezett szerepkörrel rendelkeznek a létrehozásukkor. Felhasználókat adhat hozzá a munkaterülethez, és hozzárendelheti őket a beépített szerepkörök valamelyikéhez.

| Szerepkör | Hozzáférési szint |
| --- | --- |
| **Olvasó** | Csak olvasási műveletek a munkaterületen. Az olvasók listázhatja és megtekintheti az eszközöket, beleértve az [adattár](how-to-access-data.md) hitelesítő adatait is egy munkaterületen. Az olvasók nem tudják létrehozni vagy frissíteni ezeket az eszközöket. |
| **Közreműködő** | Munkaterületen lévő eszközök megtekintése, létrehozása, szerkesztése vagy törlése (ha van ilyen). A közreműködők például létrehozhatnak egy kísérletet, létrehozhatnak vagy csatolhatnak egy számítási fürtöt, futtatást végezhetnek és webszolgáltatásokat helyezhetnek üzembe. |
| **Tulajdonos** | Teljes hozzáférés a munkaterülethez, beleértve a munkaterületen lévő eszközök megtekintését, létrehozását, szerkesztését vagy törlését. Emellett módosíthatja a szerepkör-hozzárendeléseket. |
| **Egyéni szerepkör** | Lehetővé teszi a munkaterületen belüli adott vezérlési vagy adatsík-műveletekhez való hozzáférés testreszabását. Például elküldheti egy futtatást, létrehozhat egy számítási modellt, üzembe helyezheti a modelleket, vagy regisztrálhat egy adatkészletet. |

> [!IMPORTANT]
> A szerepkör-hozzáférés az Azure több szintjére is kiterjed. Előfordulhat például, hogy valaki tulajdonosi hozzáféréssel rendelkezik a munkaterülethez, és nem rendelkezik tulajdonosi hozzáféréssel a munkaterületet tartalmazó erőforráscsoporthoz. További információt az [Azure RBAC működéséről](../role-based-access-control/overview.md#how-azure-rbac-works)szóló témakörben talál.

Jelenleg nincsenek a Azure Machine Learningra jellemző további beépített szerepkörök. A beépített szerepkörökkel kapcsolatos további információkért tekintse meg az [Azure beépített szerepkörei](../role-based-access-control/built-in-roles.md)című témakört.

## <a name="manage-workspace-access"></a>Munkaterület-hozzáférés kezelése

Ha Ön a munkaterület tulajdonosa, szerepköröket adhat hozzá és távolíthat el a munkaterülethez. Szerepköröket is hozzárendelhet a felhasználókhoz. A következő hivatkozások segítségével megismerheti a hozzáférés kezelését:
- [Azure Portal felhasználói felület](../role-based-access-control/role-assignments-portal.md)
- [PowerShell](../role-based-access-control/role-assignments-powershell.md)
- [Azure CLI](../role-based-access-control/role-assignments-cli.md)
- [REST API](../role-based-access-control/role-assignments-rest.md)
- [Azure Resource Manager-sablonok](../role-based-access-control/role-assignments-template.md)

Ha telepítette a [Azure Machine learning CLI](reference-azure-machine-learning-cli.md)-t, a parancssori felület parancsaival rendelhet hozzá szerepköröket a felhasználókhoz:

```azurecli-interactive 
az ml workspace share -w <workspace_name> -g <resource_group_name> --role <role_name> --user <user_corp_email_address>
```

A `user` mező egy meglévő felhasználó e-mail-címe a Azure Active Directory példányában, ahol a munkaterület szülő-előfizetése él. Az alábbi példa bemutatja, hogyan használhatja ezt a parancsot:

```azurecli-interactive 
az ml workspace share -w my_workspace -g my_resource_group --role Contributor --user jdoe@contoson.com
```

> [!NOTE]
> "az ml Workspace Share" parancs nem működik összevont fiók esetén Azure Active Directory B2B-vel. A parancs helyett használja az Azure UI Portalt.

## <a name="create-custom-role"></a>Egyéni szerepkör létrehozása

Ha a beépített szerepkörök nem elegendőek, egyéni szerepköröket is létrehozhat. Előfordulhat, hogy az egyéni szerepkörök olvasási, írási, törlési és számítási erőforrás-jogosultságokkal rendelkeznek az adott munkaterületen. A szerepkört egy adott munkaterület-szinten, egy adott erőforráscsoport-szinten vagy egy adott előfizetési szinten is elérhetővé teheti.

> [!NOTE]
> Ezen a szinten az erőforrás tulajdonosának kell lennie ahhoz, hogy egyéni szerepköröket hozzon létre az adott erőforráson belül.

Egyéni szerepkör létrehozásához először létre kell hoznia egy szerepkör-definíciós JSON-fájlt, amely meghatározza a szerepkör engedélyeit és hatókörét. Az alábbi példa az "adattudós egyéni" nevű egyéni szerepkört definiálja egy adott munkaterület szintjén:

`data_scientist_custom_role.json` :
```json
{
    "Name": "Data Scientist Custom",
    "IsCustom": true,
    "Description": "Can run experiment but can't create or delete compute.",
    "Actions": ["*"],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/*/delete",
        "Microsoft.MachineLearningServices/workspaces/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/delete", 
        "Microsoft.Authorization/*/write"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>/resourceGroups/<resource_group_name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace_name>"
    ]
}
```

> [!TIP]
> A mező módosításával `AssignableScopes` beállíthatja az egyéni szerepkör hatókörét az előfizetés szintjén, az erőforráscsoport szintjén vagy egy adott munkaterület szintjén.
> A fenti egyéni szerepkör csak példaként szolgál [a Azure Machine learning szolgáltatáshoz javasolt egyéni szerepkörökkel](#customroles)kapcsolatban.

Ez az egyéni szerepkör a munkaterületen mindent megtehet a következő műveletek kivételével:

- Nem hozható létre vagy nem frissíthető számítási erőforrás.
- Nem törölhet számítási erőforrást.
- Szerepkör-hozzárendeléseket nem lehet hozzáadni, törölni vagy módosítani.
- A munkaterület nem törölhető.

Az egyéni szerepkör üzembe helyezéséhez használja az alábbi Azure CLI-parancsot:

```azurecli-interactive 
az role definition create --role-definition data_scientist_role.json
```

Az üzembe helyezés után ez a szerepkör elérhetővé válik a megadott munkaterületen. Most hozzáadhatja és hozzárendelheti ezt a szerepkört a Azure Portal. A szerepkört hozzárendelheti egy felhasználóhoz a `az ml workspace share` CLI parancs használatával:

```azurecli-interactive
az ml workspace share -w my_workspace -g my_resource_group --role "Data Scientist" --user jdoe@contoson.com
```

További információ az egyéni szerepkörökről: [Egyéni Azure-szerepkörök](../role-based-access-control/custom-roles.md). 

### <a name="azure-machine-learning-operations"></a>Azure Machine Learning műveletek

Az egyéni szerepkörökkel használható műveletekkel (műveletekkel és nem műveletekkel) kapcsolatos további információkért lásd: [erőforrás-szolgáltatói műveletek](../role-based-access-control/resource-provider-operations.md#microsoftmachinelearningservices). A következő Azure CLI-parancsot is használhatja a műveletek listázásához:

```azurecli-interactive
az provider operation show –n Microsoft.MachineLearningServices
```

## <a name="list-custom-roles"></a>Egyéni szerepkörök listázása

Az Azure CLI-ben futtassa a következő parancsot:

```azurecli-interactive
az role definition list --subscription <sub-id> --custom-role-only true
```

Egy adott egyéni szerepkör szerepkör-definíciójának megtekintéséhez használja az alábbi Azure CLI-parancsot. Az értéknek `<role-name>` a fenti parancs által visszaadott formátumban kell szerepelnie:

```azurecli-interactive
az role definition list -n <role-name> --subscription <sub-id>
```

## <a name="update-a-custom-role"></a>Egyéni szerepkörök frissítése

Az Azure CLI-ben futtassa a következő parancsot:

```azurecli-interactive
az role definition update --role-definition update_def.json --subscription <sub-id>
```

Az új szerepkör-definíció teljes hatókörére vonatkozó engedélyekkel kell rendelkeznie. Ha például az új szerepkör hatóköre három előfizetésben található, akkor mindhárom előfizetéshez engedélyekkel kell rendelkeznie. 

> [!NOTE]
> A szerepkör frissítései 15 percet is igénybe vehetnek, hogy az adott hatókörben lévő összes szerepkör-hozzárendelésre érvényesek legyenek.

## <a name="use-azure-resource-manager-templates-for-repeatability"></a>Azure Resource Manager-sablonok használata az ismételhetőséghez

Ha azt tervezi, hogy újra létre kell hoznia egy összetett szerepkör-hozzárendelést, egy Azure Resource Manager sablon nagy segítség lehet. Az [201-Machine-learning-függőségek-szerepkör-hozzárendelési sablon](https://github.com/Azure/azure-quickstart-templates/tree/master/201-machine-learning-dependencies-role-assignment) azt mutatja be, hogyan adható meg a szerepkör-hozzárendelés a forráskódban az újrafelhasználáshoz. 

## <a name="common-scenarios"></a>Gyakori forgatókönyvek

A következő táblázat a Azure Machine Learning tevékenységek összegzését, valamint a minimális hatókörön belüli végrehajtáshoz szükséges engedélyeket tartalmazza. Ha például egy tevékenység a munkaterület hatókörével (4. oszlop) végezhető el, akkor az ezzel az engedéllyel rendelkező összes magasabb hatókör automatikusan is működni fog:

> [!IMPORTANT]
> Az ebben a táblázatban szereplő összes elérési `/` út **relatív elérési utak** a következőhöz `Microsoft.MachineLearningServices/` :

| Tevékenység | Előfizetés szintű hatókör | Erőforráscsoport-szintű hatókör | Munkaterület-szintű hatókör |
| ----- | ----- | ----- | ----- |
| Új munkaterület létrehozása | Nem szükséges | Tulajdonos vagy közreműködő | N/A (A létrehozása után válik a tulajdonos vagy A magasabb hatókörű szerepkör örökli) |
| Kérelem előfizetési szintjének Amlcompute-kvótája vagy a munkaterület szintjének beállítása kvóta | Tulajdonos vagy közreműködő, vagy egyéni szerepkör </br>így `/locations/updateQuotas/action`</br> az előfizetés hatókörében | Nincs engedélyezve | Nincs engedélyezve |
| Új számítási fürt létrehozása | Nem szükséges | Nem szükséges | Tulajdonos, közreműködő vagy egyéni szerepkör, amely lehetővé teszi a következőket: `/workspaces/computes/write` |
| Új számítási példány létrehozása | Nem szükséges | Nem szükséges | Tulajdonos, közreműködő vagy egyéni szerepkör, amely lehetővé teszi a következőket: `/workspaces/computes/write` |
| Bármilyen típusú Futtatás elküldése | Nem szükséges | Nem szükséges | Tulajdonos, közreműködő vagy egyéni szerepkör, amely lehetővé teszi a következőket: `"/workspaces/*/read", "/workspaces/environments/write", "/workspaces/experiments/runs/write", "/workspaces/metadata/artifacts/write", "/workspaces/metadata/snapshots/write", "/workspaces/environments/build/action", "/workspaces/experiments/runs/submit/action", "/workspaces/environments/readSecrets/action"` |
| Folyamatok és végpontok közzététele | Nem szükséges | Nem szükséges | Tulajdonos, közreműködő vagy egyéni szerepkör, amely lehetővé teszi a következőket: `"/workspaces/endpoints/pipelines/*", "/workspaces/pipelinedrafts/*", "/workspaces/modules/*"` |
| Regisztrált modell üzembe helyezése AK/ACI-erőforráson | Nem szükséges | Nem szükséges | Tulajdonos, közreműködő vagy egyéni szerepkör, amely lehetővé teszi a következőket: `"/workspaces/services/aks/write", "/workspaces/services/aci/write"` |
| Egy üzembe helyezett AK-végponton keresztüli pontozás | Nem szükséges | Nem szükséges | Tulajdonos, közreműködő vagy egyéni szerepkör, amely lehetővé teszi `"/workspaces/services/aks/score/action", "/workspaces/services/aks/listkeys/action"` a következőket: (ha nem használja Azure Active Directory auth) vagy `"/workspaces/read"` (jogkivonat-hitelesítés használatakor) |
| A tárterület elérése interaktív jegyzetfüzetekkel | Nem szükséges | Nem szükséges | Tulajdonos, közreműködő vagy egyéni szerepkör, amely lehetővé teszi a következőket: `"/workspaces/computes/read", "/workspaces/notebooks/samples/read", "/workspaces/notebooks/storage/*", "/workspaces/listKeys/action"` |
| Új egyéni szerepkör létrehozása | Tulajdonos, közreműködő vagy egyéni szerepkör, amely lehetővé teszi `Microsoft.Authorization/roleDefinitions/write` | Nem szükséges | Tulajdonos, közreműködő vagy egyéni szerepkör, amely lehetővé teszi a következőket: `/workspaces/computes/write` |

> [!TIP]
> Ha a munkaterület első alkalommal történő létrehozásakor hiba lép fel, győződjön meg arról, hogy a szerepköre engedélyezi `Microsoft.MachineLearningServices/register/action` . Ez a művelet lehetővé teszi, hogy regisztrálja az Azure Machine Learning erőforrás-szolgáltatót az Azure-előfizetésében.

### <a name="user-assigned-managed-identity-with-azure-ml-compute-cluster"></a>Felhasználó által hozzárendelt felügyelt identitás az Azure ML számítási fürttel

Ha egy felhasználóhoz rendelt identitást szeretne hozzárendelni egy Azure Machine Learning számítási fürthöz, írási engedéllyel kell rendelkeznie a számítási és a [felügyelt identitás-kezelő szerepkör](../role-based-access-control/built-in-roles.md#managed-identity-operator)létrehozásához. A felügyelt identitásokkal rendelkező Azure RBAC kapcsolatos további információkért olvassa el a [felhasználó által hozzárendelt identitás kezelése](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) című témakört.

### <a name="mlflow-operations"></a>MLflow-műveletek

A MLflow-műveletek a Azure Machine Learning munkaterületen való végrehajtásához használja az egyéni szerepkör következő hatóköreit:

| MLflow művelet | Hatókör |
| --- | --- |
| A munkaterület-követési tárolóban található összes kísérlet felsorolása azonosító alapján, a kísérlet név alapján történő beszerzése | `Microsoft.MachineLearningServices/workspaces/experiments/read` |
| Hozzon létre egy kísérletet egy névvel, adjon meg egy címkét egy kísérleten, állítsa vissza a törlésre kijelölt kísérletet.| `Microsoft.MachineLearningServices/workspaces/experiments/write` | 
| Kísérlet törlése | `Microsoft.MachineLearningServices/workspaces/experiments/delete` |
| Futtasson egy futtatási és kapcsolódó adatokat és metaadatokat, és szerezzen be egy listát az adott futtatáshoz tartozó megadott metrika összes értékéről, és sorolja fel a futtatások listáját. | `Microsoft.MachineLearningServices/workspaces/experiments/runs/read` |
| Hozzon létre egy új futtatást egy kísérleten belül, törölje a futtatásokat, a törölt futtatások visszaállítását, a jelenlegi Futtatás alatt lévő naplózási mérőszámokat, a Run címkéit, a címkék törlését a futtatáskor, a naplófájlok (kulcs-érték párok) futtatásához | `Microsoft.MachineLearningServices/workspaces/experiments/runs/write` |
| A regisztrált modell neve alapján beolvashatja a beállításjegyzék összes regisztrált modelljét, megkeresheti a regisztrált modelleket, az egyes kérelmek fázisának legújabb modelljeit, beszerezhet egy regisztrált modell verzióját, a keresési modell verzióit, az URI-t, ahol a modell verziójának összetevőit tárolja a rendszer, a kísérletek azonosítóinak keresése | `Microsoft.MachineLearningServices/workspaces/models/read` |
| Új regisztrált modell létrehozása, a regisztrált modell nevének/leírásának frissítése, a meglévő regisztrált modell átnevezése, a modell új verziójának létrehozása, a modell verziójának leírásának frissítése, a regisztrált modell átváltása az egyik fázisra | `Microsoft.MachineLearningServices/workspaces/models/write` |
| Regisztrált modell törlése az összes verziójával együtt, a regisztrált modellek adott verzióinak törlése | `Microsoft.MachineLearningServices/workspaces/models/delete` |

<a id="customroles"></a>

## <a name="example-custom-roles"></a>Példa egyéni szerepkörökre

### <a name="data-scientist"></a>Adatelemző

Lehetővé teszi, hogy az adattudós a munkaterületen belül minden műveletet elvégezzen, **kivéve** a következőket:

* Számítás létrehozása
* Modellek üzembe helyezése üzemi AK-fürtön
* Folyamat-végpont üzembe helyezése éles környezetben

`data_scientist_custom_role.json` :
```json
{
    "Name": "Data Scientist Custom",
    "IsCustom": true,
    "Description": "Can run experiment but can't create or delete compute or deploy production endpoints.",
    "Actions": [
        "Microsoft.MachineLearningServices/workspaces/*/read",
        "Microsoft.MachineLearningServices/workspaces/*/action",
        "Microsoft.MachineLearningServices/workspaces/*/delete",
        "Microsoft.MachineLearningServices/workspaces/*/write"
    ],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/delete",
        "Microsoft.MachineLearningServices/workspaces/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/delete", 
        "Microsoft.Authorization/*",
        "Microsoft.MachineLearningServices/workspaces/computes/listKeys/action",
        "Microsoft.MachineLearningServices/workspaces/listKeys/action",
        "Microsoft.MachineLearningServices/workspaces/services/aks/write",
        "Microsoft.MachineLearningServices/workspaces/services/aks/delete",
        "Microsoft.MachineLearningServices/workspaces/endpoints/pipelines/write"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>"
    ]
}
```

### <a name="data-scientist-restricted"></a>Adattudós korlátozott

Egy korlátozott szerepkör-definíció a helyettesítő karakterek nélkül az engedélyezett műveletekben. A munkaterületen belül minden műveletet elvégezhet, **kivéve** a következőket:

* Számítás létrehozása
* Modellek üzembe helyezése üzemi AK-fürtön
* Folyamat-végpont üzembe helyezése éles környezetben

`data_scientist_restricted_custom_role.json` :
```json
{
    "Name": "Data Scientist Restricted Custom",
    "IsCustom": true,
    "Description": "Can run experiment but can't create or delete compute or deploy production endpoints",
    "Actions": [
        "Microsoft.MachineLearningServices/workspaces/*/read",
        "Microsoft.MachineLearningServices/workspaces/computes/start/action",
        "Microsoft.MachineLearningServices/workspaces/computes/stop/action",
        "Microsoft.MachineLearningServices/workspaces/computes/restart/action",
        "Microsoft.MachineLearningServices/workspaces/computes/applicationaccess/action",
        "Microsoft.MachineLearningServices/workspaces/notebooks/storage/read",
        "Microsoft.MachineLearningServices/workspaces/notebooks/storage/write",
        "Microsoft.MachineLearningServices/workspaces/notebooks/storage/delete",
        "Microsoft.MachineLearningServices/workspaces/notebooks/samples/read",
        "Microsoft.MachineLearningServices/workspaces/experiments/runs/write",
        "Microsoft.MachineLearningServices/workspaces/experiments/write",
        "Microsoft.MachineLearningServices/workspaces/experiments/runs/submit/action",
        "Microsoft.MachineLearningServices/workspaces/pipelinedrafts/write",
        "Microsoft.MachineLearningServices/workspaces/metadata/snapshots/write",
        "Microsoft.MachineLearningServices/workspaces/metadata/artifacts/write",
        "Microsoft.MachineLearningServices/workspaces/environments/write",
        "Microsoft.MachineLearningServices/workspaces/models/write",
        "Microsoft.MachineLearningServices/workspaces/modules/write",
        "Microsoft.MachineLearningServices/workspaces/datasets/registered/write", 
        "Microsoft.MachineLearningServices/workspaces/datasets/registered/delete",
        "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/write",
        "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/delete",
        "Microsoft.MachineLearningServices/workspaces/computes/listNodes/action",
        "Microsoft.MachineLearningServices/workspaces/environments/build/action"
    ],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/computes/write",
        "Microsoft.MachineLearningServices/workspaces/write",
        "Microsoft.MachineLearningServices/workspaces/computes/delete",
        "Microsoft.MachineLearningServices/workspaces/delete",
        "Microsoft.MachineLearningServices/workspaces/computes/listKeys/action",
        "Microsoft.MachineLearningServices/workspaces/listKeys/action",
        "Microsoft.Authorization/*",
        "Microsoft.MachineLearningServices/workspaces/datasets/registered/profile/read",
        "Microsoft.MachineLearningServices/workspaces/datasets/registered/preview/read",
        "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/profile/read",
        "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/preview/read",
        "Microsoft.MachineLearningServices/workspaces/datasets/registered/schema/read",    
        "Microsoft.MachineLearningServices/workspaces/datasets/unregistered/schema/read",
        "Microsoft.MachineLearningServices/workspaces/datastores/write",
        "Microsoft.MachineLearningServices/workspaces/datastores/delete"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>"
    ]
}
```
     
### <a name="mlflow-data-scientist"></a>MLflow adattudós

Lehetővé teszi, hogy az adattudós az összes MLflow-AzureML támogatott műveletet hajtson végre, **kivéve** a következőket:

* Számítás létrehozása
* Modellek üzembe helyezése üzemi AK-fürtön
* Folyamat-végpont üzembe helyezése éles környezetben

`mlflow_data_scientist_custom_role.json` :
```json
{
    "Name": "MLFlow Data Scientist Custom",
    "IsCustom": true,
    "Description": "Can perform azureml mlflow integrated functionalities that includes mlflow tracking, projects, model registry",
    "Actions": [
        "Microsoft.MachineLearningServices/workspaces/experiments/read",
        "Microsoft.MachineLearningServices/workspaces/experiments/write",
        "Microsoft.MachineLearningServices/workspaces/experiments/delete",
        "Microsoft.MachineLearningServices/workspaces/experiments/runs/read",
        "Microsoft.MachineLearningServices/workspaces/experiments/runs/write",
        "Microsoft.MachineLearningServices/workspaces/models/read",
        "Microsoft.MachineLearningServices/workspaces/models/write",
        "Microsoft.MachineLearningServices/workspaces/models/delete"
    ],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/delete",
        "Microsoft.MachineLearningServices/workspaces/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/write",
        "Microsoft.MachineLearningServices/workspaces/computes/*/delete", 
        "Microsoft.Authorization/*",
        "Microsoft.MachineLearningServices/workspaces/computes/listKeys/action",
        "Microsoft.MachineLearningServices/workspaces/listKeys/action",
        "Microsoft.MachineLearningServices/workspaces/services/aks/write",
        "Microsoft.MachineLearningServices/workspaces/services/aks/delete",
        "Microsoft.MachineLearningServices/workspaces/endpoints/pipelines/write"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>"
    ]
}
```   

### <a name="mlops"></a>MLOps

Lehetővé teszi egy szerepkör hozzárendelését egy egyszerű szolgáltatáshoz, és a segítségével automatizálhatja a MLOps-folyamatokat. Például egy már közzétett folyamaton való futtatáshoz:

`mlops_custom_role.json` :
```json
{
    "Name": "MLOps Custom",
    "IsCustom": true,
    "Description": "Can run pipelines against a published pipeline endpoint",
    "Actions": [
        "Microsoft.MachineLearningServices/workspaces/read",
        "Microsoft.MachineLearningServices/workspaces/endpoints/pipelines/read",
        "Microsoft.MachineLearningServices/workspaces/metadata/artifacts/read",
        "Microsoft.MachineLearningServices/workspaces/metadata/snapshots/read",
        "Microsoft.MachineLearningServices/workspaces/environments/read",    
        "Microsoft.MachineLearningServices/workspaces/metadata/secrets/read",
        "Microsoft.MachineLearningServices/workspaces/modules/read",
        "Microsoft.MachineLearningServices/workspaces/experiments/runs/read",
        "Microsoft.MachineLearningServices/workspaces/datasets/registered/read",
        "Microsoft.MachineLearningServices/workspaces/datastores/read",
        "Microsoft.MachineLearningServices/workspaces/environments/write",
        "Microsoft.MachineLearningServices/workspaces/experiments/runs/write",
        "Microsoft.MachineLearningServices/workspaces/metadata/artifacts/write",
        "Microsoft.MachineLearningServices/workspaces/metadata/snapshots/write",
        "Microsoft.MachineLearningServices/workspaces/environments/build/action",
        "Microsoft.MachineLearningServices/workspaces/experiments/runs/submit/action"
    ],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/computes/write",
        "Microsoft.MachineLearningServices/workspaces/write",
        "Microsoft.MachineLearningServices/workspaces/computes/delete",
        "Microsoft.MachineLearningServices/workspaces/delete",
        "Microsoft.MachineLearningServices/workspaces/computes/listKeys/action",
        "Microsoft.MachineLearningServices/workspaces/listKeys/action",
        "Microsoft.Authorization/*"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>"
    ]
}
```

### <a name="workspace-admin"></a>Munkaterület rendszergazdája

Lehetővé teszi az összes művelet végrehajtását egy munkaterület hatókörén belül, **kivéve** a következőket:

* Új munkaterület létrehozása
* Előfizetés vagy munkaterület szintű kvóták kiosztása

A munkaterület rendszergazdája szintén nem tud új szerepkört létrehozni. A meglévő beépített vagy egyéni szerepköröket csak a munkaterület hatókörén belül lehet hozzárendelni:

`workspace_admin_custom_role.json` :
```json
{
    "Name": "Workspace Admin Custom",
    "IsCustom": true,
    "Description": "Can perform all operations except quota management and upgrades",
    "Actions": [
        "Microsoft.MachineLearningServices/workspaces/*/read",
        "Microsoft.MachineLearningServices/workspaces/*/action",
        "Microsoft.MachineLearningServices/workspaces/*/write",
        "Microsoft.MachineLearningServices/workspaces/*/delete",
        "Microsoft.Authorization/roleAssignments/*"
    ],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/write"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>"
    ]
}
```

<a name="labeler"></a>
### <a name="data-labeler"></a>Az adatlabeler

Lehetővé teszi, hogy csak az adat címkézésére szolgáló szerepkört definiáljon:

`labeler_custom_role.json` :
```json
{
    "Name": "Labeler Custom",
    "IsCustom": true,
    "Description": "Can label data for Labeling",
    "Actions": [
        "Microsoft.MachineLearningServices/workspaces/read",
        "Microsoft.MachineLearningServices/workspaces/labeling/projects/read",
        "Microsoft.MachineLearningServices/workspaces/labeling/labels/write"
    ],
    "NotActions": [
        "Microsoft.MachineLearningServices/workspaces/labeling/projects/summary/read"
    ],
    "AssignableScopes": [
        "/subscriptions/<subscription_id>"
    ]
}
```

## <a name="troubleshooting"></a>Hibaelhárítás

Íme néhány tudnivaló az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) használata során:

- Ha az Azure-ban hoz létre egy erőforrást, például egy munkaterületet, akkor Ön nem közvetlenül az erőforrás tulajdonosa. A szerepkör örökölt a legmagasabb hatókörű szerepkörből, amelyet az adott előfizetésben engedélyez. Például ha Ön hálózati rendszergazda, és rendelkezik a Machine Learning munkaterület létrehozásához szükséges engedélyekkel, akkor a hálózati rendszergazda szerepkört a munkaterülethez kell rendelni, nem a tulajdonosi szerepkörhöz.

- Ha kvótát szeretne végrehajtani egy munkaterületen, előfizetési szintű engedélyekre van szükség. Ez azt jelenti, hogy az előfizetési szint kvótájának vagy a munkaterületnek a felügyelt számítási erőforrásokra vonatkozó kvótájának beállítása csak akkor fordulhat elő, ha az előfizetés hatókörében írási engedélyekkel rendelkezik.

- Ha két szerepkör-hozzárendelés van ugyanahhoz a Azure Active Directory-felhasználóhoz, amely ütközik a műveletek és a tevékenységek ütköző részeivel, akkor előfordulhat, hogy az egyik szerepkörtől való kizárásban felsorolt műveletek nem lépnek érvénybe, ha egy másik szerepkör Műveleteiként is szerepelnek. Ha többet szeretne megtudni arról, hogy az Azure hogyan elemzi a szerepkör-hozzárendeléseket, olvassa el, [hogyan határozza meg, hogy a felhasználó rendelkezik-e erőforrás-hozzáféréssel az Azure RBAC](../role-based-access-control/overview.md#how-azure-rbac-determines-if-a-user-has-access-to-a-resource)

- A számítási erőforrások VNet való üzembe helyezéséhez explicit módon rendelkeznie kell a következő műveletekhez szükséges engedélyekkel:
    - `Microsoft.Network/virtualNetworks/*/read` a VNet-erőforrásokon.
    - `Microsoft.Network/virtualNetworks/subnet/join/action` az alhálózati erőforráson.
    
    A hálózatkezeléssel rendelkező Azure RBAC kapcsolatos további információkért tekintse meg a [hálózatkezelés beépített szerepköreit](../role-based-access-control/built-in-roles.md#networking).

- Időnként akár 1 órát is igénybe vehet, amíg az új szerepkör-hozzárendelések érvénybe lépnek a gyorsítótárban tárolt engedélyekkel szemben a veremben.

## <a name="next-steps"></a>Következő lépések

- [Vállalati biztonság áttekintése](concept-enterprise-security.md)
- [Virtual Network elkülönítés és Adatvédelem – áttekintés](how-to-network-security-overview.md)
- [Oktatóanyag: modellek betanítása](tutorial-train-models-with-aml.md)
- [Erőforrás-szolgáltatói műveletek](../role-based-access-control/resource-provider-operations.md#microsoftmachinelearningservices)
