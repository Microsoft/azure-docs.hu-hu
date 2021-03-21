---
title: Munkaterületek létrehozása a portálon
titleSuffix: Azure Machine Learning
description: Megtudhatja, hogyan hozhat létre, tekinthet meg és törölhet Azure Machine Learning munkaterületeket a Azure Portal vagy a Python SDK-val.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: sgilley
author: sdgilley
ms.date: 09/30/2020
ms.topic: conceptual
ms.custom: how-to, fasttrack-edit
ms.openlocfilehash: 472bc66c75881d622e8ecfe23031f58db773a919
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102518925"
---
# <a name="create-and-manage-azure-machine-learning-workspaces"></a>Azure Machine Learning-munkaterületek létrehozása és kezelése 

Ebben a cikkben [**Azure Machine learning munkaterületeket**](concept-workspace.md) hozhat létre, tekinthet meg és törölhet [Azure Machine Learning](overview-what-is-azure-ml.md)számára a Azure Portal vagy a [Python SDK](/python/api/overview/azure/ml/) használatával

Az igények változásának vagy az automatizálásra vonatkozó követelményeknek megfelelően a parancssori felület vagy [a vs Code bővítmény](tutorial-setup-vscode-extension.md) [használatával](reference-azure-machine-learning-cli.md)is létrehozhat és törölhet munkaterületeket.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy ingyenes fiókot. Próbálja ki a [Azure Machine learning ingyenes vagy fizetős verzióját](https://aka.ms/AMLFree) még ma.
* Ha a Python SDK-t használja, [telepítse az SDK](/python/api/overview/azure/ml/install)-t.

## <a name="limitations"></a>Korlátozások

[!INCLUDE [register-namespace](../../includes/machine-learning-register-namespace.md)]

Alapértelmezés szerint a Munkaterületek létrehozása is létrehoz egy Azure Container Registry (ACR).  Mivel az ACR jelenleg nem támogatja a Unicode-karaktereket az erőforráscsoport-nevekben, olyan erőforráscsoportot használjon, amely nem tartalmazza ezeket a karaktereket.

## <a name="create-a-workspace"></a>Munkaterület létrehozása

# <a name="python"></a>[Python](#tab/python)

* **Alapértelmezett specifikáció.** Alapértelmezés szerint a függő erőforrások és az erőforráscsoport is automatikusan létrejön. Ez a kód létrehoz egy nevű munkaterületet `myworkspace` és egy nevű erőforráscsoportot `myresourcegroup` `eastus2` .
    
    ```python
    from azureml.core import Workspace
    
    ws = Workspace.create(name='myworkspace',
                   subscription_id='<azure-subscription-id>',
                   resource_group='myresourcegroup',
                   create_resource_group=True,
                   location='eastus2'
                   )
    ```
    Állítsa hamis értékre, `create_resource_group` Ha rendelkezik egy meglévő Azure-erőforráscsoporthoz, amelyet használni szeretne a munkaterülethez.

* <a name="create-multi-tenant"></a>**Több bérlő.**  Ha több fiókkal rendelkezik, adja meg a használni kívánt Azure Active Directory bérlői AZONOSÍTÓját.  Keresse meg a bérlői AZONOSÍTÓját a [Azure Portal](https://portal.azure.com) **Azure Active Directory, külső identitások** területen.

    ```python
    from azureml.core.authentication import InteractiveLoginAuthentication
    from azureml.core import Workspace
    
    interactive_auth = InteractiveLoginAuthentication(tenant_id="my-tenant-id")
    ws = Workspace.create(name='myworkspace',
                subscription_id='<azure-subscription-id>',
                resource_group='myresourcegroup',
                create_resource_group=True,
                location='eastus2',
                auth=interactive_auth
                )
    ```

* **[Szuverén felhő](reference-machine-learning-cloud-parity.md)**. Ha szuverén felhőben dolgozik, további kódokat kell megadnia az Azure-ban való hitelesítéshez.

    ```python
    from azureml.core.authentication import InteractiveLoginAuthentication
    from azureml.core import Workspace
    
    interactive_auth = InteractiveLoginAuthentication(cloud="<cloud name>") # for example, cloud="AzureUSGovernment"
    ws = Workspace.create(name='myworkspace',
                subscription_id='<azure-subscription-id>',
                resource_group='myresourcegroup',
                create_resource_group=True,
                location='eastus2',
                auth=interactive_auth
                )
    ```

* **Meglévő Azure-erőforrások használata**.  Létrehozhat egy olyan munkaterületet is, amely meglévő Azure-erőforrásokat használ az Azure erőforrás-azonosító formátumával. Keresse meg az adott Azure-erőforrás azonosítóit a Azure Portal vagy az SDK-val. Ez a példa feltételezi, hogy az erőforráscsoport, a Storage-fiók, a Key Vault, az alkalmazás-felismerés és a tároló-beállításjegyzék már létezik.

   ```python
   import os
   from azureml.core import Workspace
   from azureml.core.authentication import ServicePrincipalAuthentication

   service_principal_password = os.environ.get("AZUREML_PASSWORD")

   service_principal_auth = ServicePrincipalAuthentication(
      tenant_id="<tenant-id>",
      username="<application-id>",
      password=service_principal_password)

                        auth=service_principal_auth,
                             subscription_id='<azure-subscription-id>',
                             resource_group='myresourcegroup',
                             create_resource_group=False,
                             location='eastus2',
                             friendly_name='My workspace',
                             storage_account='subscriptions/<azure-subscription-id>/resourcegroups/myresourcegroup/providers/microsoft.storage/storageaccounts/mystorageaccount',
                             key_vault='subscriptions/<azure-subscription-id>/resourcegroups/myresourcegroup/providers/microsoft.keyvault/vaults/mykeyvault',
                             app_insights='subscriptions/<azure-subscription-id>/resourcegroups/myresourcegroup/providers/microsoft.insights/components/myappinsights',
                             container_registry='subscriptions/<azure-subscription-id>/resourcegroups/myresourcegroup/providers/microsoft.containerregistry/registries/mycontainerregistry',
                             exist_ok=False)
   ```

További információ: [munkaterület SDK-referenciája](/python/api/azureml-core/azureml.core.workspace.workspace).

Ha problémája merül fel az előfizetés elérésekor, tekintse [meg a Azure Machine learning erőforrások és munkafolyamatok hitelesítésének beállítása](how-to-setup-authentication.md), valamint a Azure Machine learning jegyzetfüzetben végzett [hitelesítés](https://aka.ms/aml-notebook-auth) című témakört.

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com/) az Azure-előfizetéséhez tartozó hitelesítő adatok használatával. 

1. A Azure Portal bal felső sarkában válassza az **+ erőforrás létrehozása** lehetőséget.

      ![Új erőforrás létrehozása](./media/how-to-manage-workspace/create-workspace.gif)

1. A keresősáv használatával megkeresheti **Machine learning**.

1. Válassza a **Machine learning** lehetőséget.

1. A **Machine learning** ablaktáblán kattintson a **Létrehozás** elemre a kezdéshez.

1. Adja meg az alábbi adatokat az új munkaterület konfigurálásához:

   Mező|Leírás 
   ---|---
   Munkaterület neve |Adjon meg egy egyedi nevet, amely azonosítja a munkaterületet. Ebben a példában a **docs-ws-** t használjuk. A névnek egyedinek kell lennie az erőforráscsoport között. Használjon könnyen felhívható nevet, és a mások által létrehozott munkaterületek megkülönböztetését. A munkaterület neve megkülönbözteti a kis-és nagybetűket.
   Előfizetés |Válassza ki a használni kívánt Azure-előfizetést.
   Erőforráscsoport | Az előfizetés valamelyik meglévő erőforráscsoportját használja, vagy adjon meg egy nevet új erőforráscsoport létrehozásához. Egy erőforráscsoport kapcsolódó erőforrásokat tárol egy Azure-megoldáshoz. Ebben a példában a **docs-pénzmosást** használjuk. Meglévő erőforráscsoport használatához *közreműködői* vagy *tulajdonosi* szerepkörre van szükség.  További információ a hozzáférésről: [Azure Machine learning munkaterület hozzáférésének kezelése](how-to-assign-roles.md).
   Region | Válassza ki a felhasználókhoz legközelebb eső Azure-régiót, valamint az adatforrásokat a munkaterület létrehozásához.
   | A(z) | A munkaterület alapértelmezett Storage-fiókja. Alapértelmezés szerint a rendszer létrehoz egy újat. |
   | Key Vault | A munkaterület által használt Azure Key Vault. Alapértelmezés szerint a rendszer létrehoz egy újat. |
   | Application Insights | A munkaterület Application Insight-példánya. Alapértelmezés szerint a rendszer létrehoz egy újat. |
   | Container Registry | A munkaterület Azure Container Registry. Alapértelmezés szerint a rendszer _nem_ hoz létre egy újat a munkaterülethez. Ehelyett akkor jön létre, amikor szüksége lesz rá, amikor egy Docker-rendszerképet hoz létre a betanítás vagy az üzembe helyezés során. |

   :::image type="content" source="media/how-to-manage-workspace/create-workspace-form.png" alt-text="Konfigurálja a munkaterületet.":::

1. Ha befejezte a munkaterület konfigurálását, válassza a **felülvizsgálat + létrehozás** lehetőséget. Igény szerint a [hálózatkezelés](#networking) és a [speciális](#advanced) szakaszban is konfigurálhatja a munkaterület további beállításait.

1. Tekintse át a beállításokat, és végezze el a további módosításokat vagy helyesbítéseket. Ha elégedett a beállításokkal, válassza a **Létrehozás** lehetőséget.

   > [!Warning] 
   > Több percet is igénybe vehet, hogy a munkaterületet a felhőben hozza létre.

   Ha a folyamat elkészült, megjelenik egy központi telepítés sikerességét jelző üzenet. 
 
 1. Az új munkaterület megtekintéséhez válassza az **Ugrás erőforráshoz** lehetőséget.
 
---



### <a name="networking"></a>Hálózatkezelés  

> [!IMPORTANT]  
> További információ a saját munkaterülettel rendelkező privát végpontok és virtuális hálózatok használatáról: [hálózati elkülönítés és adatvédelem](how-to-network-security-overview.md).


# <a name="python"></a>[Python](#tab/python)

A Azure Machine Learning Python SDK biztosítja a [PrivateEndpointConfig](/python/api/azureml-core/azureml.core.privateendpointconfig) osztályt, amely a [munkaterülettel együtt használható. hozzon létre ()](/python/api/azureml-core/azureml.core.workspace.workspace#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---tags-none--friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--adb-workspace-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--private-endpoint-config-none--private-endpoint-auto-approval-true--exist-ok-false--show-output-true-) egy olyan munkaterületet, amely privát végponttal rendelkezik. Ehhez az osztályhoz egy meglévő virtuális hálózat szükséges.

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. Az alapértelmezett hálózati konfiguráció egy __nyilvános végpont__ használata, amely a nyilvános interneten érhető el. Ha a munkaterülethez való hozzáférést egy Ön által létrehozott Azure-Virtual Network szeretné korlátozni, válassza a __magánhálózati végpont__ (előzetes verzió) lehetőséget a __kapcsolati módszerként__, majd a __+ Hozzáadás__ paranccsal konfigurálja a végpontot.   

   :::image type="content" source="media/how-to-manage-workspace/select-private-endpoint.png" alt-text="Privát végpont kiválasztása":::  

1. A __privát végpont létrehozása__ űrlapon állítsa be a használni kívánt helyet, nevet és virtuális hálózatot. Ha saját DNS zónával szeretné használni a végpontot, válassza a __saját DNS-zóna integrálása__ lehetőséget, majd a __saját DNS zóna__ mező használatával válassza ki a zónát. A végpont létrehozásához kattintson __az OK gombra__ .   

   :::image type="content" source="media/how-to-manage-workspace/create-private-endpoint.png" alt-text="Magánhálózati végpont létrehozása":::   

1. Ha befejezte a hálózatkezelés konfigurálását, válassza a __felülvizsgálat + létrehozás__ lehetőséget, vagy lépjen a választható __speciális__ konfigurációra.

---

> [!IMPORTANT]  
> A Azure Machine Learning munkaterülettel rendelkező privát végpontok jelenleg nyilvános előzetes verzióban érhetők el. Ez az előzetes verzió szolgáltatói szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.     
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

### <a name="multiple-workspaces-with-private-endpoint"></a>Több munkaterület privát végponttal

Privát végpont létrehozásakor létrejön egy új, __privatelink.API.azureml.MS__ nevű saját DNS zóna. Ez a virtuális hálózatra mutató hivatkozást tartalmaz. Ha több munkaterületet hoz létre egy privát végponttal ugyanabban az erőforráscsoporthoz, akkor csak az első privát végpont virtuális hálózata adható hozzá a DNS-zónához. A további munkaterületek/privát végpontok által használt virtuális hálózatok bejegyzéseinek hozzáadásához kövesse az alábbi lépéseket:

1. A [Azure Portal](https://portal.azure.com)válassza ki a munkaterületet tartalmazó erőforráscsoportot. Ezután válassza ki a __privatelink.API.azureml.MS__ nevű saját DNS zóna erőforrást.
2. A __Beállítások__ területen válassza a __virtuális hálózati kapcsolatok__ elemet.
3. Válassza a __Hozzáadás__ lehetőséget. A __virtuális hálózat hozzáadása hivatkozás__ lapon adjon meg egy egyedi __hivatkozási nevet__, majd válassza ki a hozzáadni kívánt __virtuális hálózatot__ . A hálózati kapcsolat hozzáadásához kattintson __az OK gombra__ .

További információ: [Azure Private Endpoint DNS-konfiguráció](../private-link/private-endpoint-dns.md).

### <a name="vulnerability-scanning"></a>Biztonsági rések vizsgálata

Az Azure Security Center egységes biztonsági felügyeletet és fejlett fenyegetésvédelmet biztosít a hibrid felhőalapú számítási feladatokhoz. Az erőforrások vizsgálatához és a javaslatainak követéséhez engedélyeznie kell Azure Security Center. További információ:  [Azure Container Registry rendszerképek vizsgálata Security Center](../security-center/defender-for-container-registries-introduction.md) és az [Azure Kubernetes Services integrációja Security Center](../security-center/defender-for-kubernetes-introduction.md)használatával.

### <a name="advanced"></a>Felsőfokú

Alapértelmezés szerint a munkaterület metaadatai a Microsoft által fenntartott Azure Cosmos DB-példányban vannak tárolva. Ezeket az adatfájlokat a Microsoft által felügyelt kulcsokkal titkosítjuk.

Ha korlátozni szeretné a Microsoft által a munkaterületre összegyűjtött adatokat, válassza a __magas üzleti hatás munkaterületet__ a portálon, vagy állítsa be a `hbi_workspace=true ` Pythont. További információ erről a beállításról: inaktív [titkosítás](concept-data-encryption.md#encryption-at-rest).

> [!IMPORTANT]  
> A magas üzleti hatás kiválasztása csak munkaterületek létrehozásakor végezhető el. Ez a beállítás a munkaterület létrehozása után nem módosítható.   

#### <a name="use-your-own-key"></a>Saját kulcs használata

Az adattitkosításhoz saját kulcsot is megadhat. Ezzel létrehozza az Azure-előfizetésében található metaadatokat tároló Azure Cosmos DB-példányt.

[!INCLUDE [machine-learning-customer-managed-keys.md](../../includes/machine-learning-customer-managed-keys.md)]

A saját kulcs megadásához kövesse az alábbi lépéseket:

> [!IMPORTANT]  
> Mielőtt végrehajtaná ezeket a lépéseket, először el kell végeznie a következő műveleteket:   
>
> 1. Engedélyezze az előfizetéshez tartozó közreműködői engedélyekkel rendelkező __Machine learning alkalmazást__ (az identitás-és hozzáférés-kezelésben).  
> 1. Kövesse az [ügyfél által felügyelt kulcsok konfigurálása](../cosmos-db/how-to-setup-cmk.md) a következőhöz című témakör lépéseit:
>     * A Azure Cosmos DB-szolgáltató regisztrálása
>     * Azure Key Vault létrehozása és konfigurálása
>     * Kulcs létrehozása
>   
>     Nem kell manuálisan létrehoznia a Azure Cosmos DB példányt, a rendszer létrehoz egyet a munkaterület létrehozása során. Ez a Azure Cosmos DB-példány egy külön erőforráscsoporthoz jön létre a következő minta alapján: `<your-workspace-resource-name>_<GUID>` .   
>   
> Ez a beállítás a munkaterület létrehozása után nem módosítható. Ha törli a munkaterület által használt Azure Cosmos DB, törölnie kell az azt használó munkaterületet is.

# <a name="python"></a>[Python](#tab/python)

`cmk_keyvault`A és a használatával `resource_cmk_uri` adhatja meg a felhasználó által felügyelt kulcsot.

```python
from azureml.core import Workspace
   ws = Workspace.create(name='myworkspace',
               subscription_id='<azure-subscription-id>',
               resource_group='myresourcegroup',
               create_resource_group=True,
               location='eastus2'
               cmk_keyvault='subscriptions/<azure-subscription-id>/resourcegroups/myresourcegroup/providers/microsoft.keyvault/vaults/<keyvault-name>', 
               resource_cmk_uri='<key-identifier>'
               )

```

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. Válassza az __ügyfél által felügyelt kulcsok__ lehetőséget, majd __kattintson a kulcs kiválasztásához__.

    :::image type="content" source="media/how-to-manage-workspace/advanced-workspace.png" alt-text="Felhasználó által kezelt kulcsok":::

1. A __válasszon kulcsot Azure Key Vault__ űrlapon válassza ki a meglévő Azure Key Vault, a benne található kulcsot, valamint a kulcs verzióját. Ezzel a kulccsal titkosíthatja a Azure Cosmos DB tárolt adataikat. Végül használja a __Select (kiválasztás__ ) gombot a kulcs használatához.

   :::image type="content" source="media/how-to-manage-workspace/select-key-vault.png" alt-text="Válassza ki a kulcsot":::

---

### <a name="download-a-configuration-file"></a>Konfigurációs fájl letöltése

Ha [számítási példányt](tutorial-1st-experiment-sdk-setup.md#azure)fog létrehozni, hagyja ki ezt a lépést.  A számítási példány már létrehozta a fájl másolatát.

# <a name="python"></a>[Python](#tab/python)

Ha azt tervezi, hogy kódot használ a munkaterületre hivatkozó helyi környezetben ( `ws` ), írja be a konfigurációs fájlt:

```python
ws.write_config()
```

# <a name="portal"></a>[Portál](#tab/azure-portal)

Ha azt tervezi, hogy a munkaterületre hivatkozó helyi környezet programkódját használja, válassza a  **config.jsletöltése** elemet a munkaterület **Áttekintés** szakaszában.  

   ![config.jsletöltése](./media/how-to-manage-workspace/configure.png)

---

Helyezze a fájlt a címtár-struktúrába a Python-szkriptekkel vagy a Jupyter notebookokkal. Ez lehet ugyanabban a címtárban, egy *. azureml* nevű alkönyvtár vagy egy szülő könyvtárban. Számítási példány létrehozásakor a rendszer hozzáadja ezt a fájlt a virtuális gép megfelelő könyvtárába.

## <a name="connect-to-a-workspace"></a>Kapcsolódás munkaterülethez

A Python-kódban létre kell hoznia egy munkaterület-objektumot a munkaterülethez való kapcsolódáshoz.  Ez a kód beolvassa a konfigurációs fájl tartalmát a munkaterület megkereséséhez.  Ha még nincs hitelesítve, a rendszer kérni fogja, hogy jelentkezzen be.

```python
from azureml.core import Workspace

ws = Workspace.from_config()
```

* <a name="connect-multi-tenant"></a>**Több bérlő.**  Ha több fiókkal rendelkezik, adja meg a használni kívánt Azure Active Directory bérlői AZONOSÍTÓját.  Keresse meg a bérlői AZONOSÍTÓját a [Azure Portal](https://portal.azure.com) **Azure Active Directory, külső identitások** területen.

    ```python
    from azureml.core.authentication import InteractiveLoginAuthentication
    from azureml.core import Workspace
    
    interactive_auth = InteractiveLoginAuthentication(tenant_id="my-tenant-id")
    ws = Workspace.from_config(auth=interactive_auth)
    ```

* **[Szuverén felhő](reference-machine-learning-cloud-parity.md)**. Ha szuverén felhőben dolgozik, további kódokat kell megadnia az Azure-ban való hitelesítéshez.

    ```python
    from azureml.core.authentication import InteractiveLoginAuthentication
    from azureml.core import Workspace
    
    interactive_auth = InteractiveLoginAuthentication(cloud="<cloud name>") # for example, cloud="AzureUSGovernment"
    ws = Workspace.from_config(auth=interactive_auth)
    ```
    
Ha problémája merül fel az előfizetés elérésekor, tekintse [meg a Azure Machine learning erőforrások és munkafolyamatok hitelesítésének beállítása](how-to-setup-authentication.md), valamint a Azure Machine learning jegyzetfüzetben végzett [hitelesítés](https://aka.ms/aml-notebook-auth) című témakört.

## <a name="find-a-workspace"></a><a name="view"></a>Munkaterület keresése

Tekintse meg a használható munkaterületek listáját.

# <a name="python"></a>[Python](#tab/python)

A [Azure Portal előfizetések lapján](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade)megkeresheti az előfizetéseit.  Másolja ki az azonosítót, és használja az alábbi kódban az előfizetéshez elérhető összes munkaterület megtekintéséhez.

```python
from azureml.core import Workspace

Workspace.list('<subscription-id>')
```

# <a name="portal"></a>[Portál](#tab/azure-portal)

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

1. A felső Keresés mezőbe írja be a következőt: **Machine learning**.  

1. Válassza a **Machine learning** lehetőséget.

   ![Azure Machine Learning munkaterület keresése](./media/how-to-manage-workspace/find-workspaces.png)

1. Tekintse át a talált munkaterületek listáját. Az előfizetés, az erőforráscsoportok és a helyszínek alapján szűrhet.  

1. Válasszon ki egy munkaterületet a tulajdonságainak megjelenítéséhez.

---


## <a name="delete-a-workspace"></a>Munkaterület törlése

Ha már nincs szüksége munkaterületre, törölje azt.  

# <a name="python"></a>[Python](#tab/python)

Munkaterület törlése `ws` :

```python
ws.delete(delete_dependent_resources=False, no_wait=False)
```

Az alapértelmezett művelet nem törli a munkaterülethez társított erőforrásokat, például a tároló-beállításjegyzéket, a Storage-fiókot, a kulcstartót és az Application bepillantást.  Az igaz értékre állítva `delete_dependent_resources` Törölje ezeket az erőforrásokat is.

# <a name="portal"></a>[Portál](#tab/azure-portal)

A [Azure Portal](https://portal.azure.com/)a törölni kívánt munkaterület tetején válassza a **Törlés**  lehetőséget.

:::image type="content" source="./media/how-to-manage-workspace/delete-workspace.png" alt-text="Munkaterület törlése":::

---

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

## <a name="troubleshooting"></a>Hibaelhárítás

* A **Azure Machine learning Studio által támogatott böngészők**: javasoljuk, hogy az operációs rendszerével kompatibilis legújabb böngészőt használja. A következő böngészők támogatottak:
  * Microsoft Edge (az új Microsoft Edge, legújabb verzió. Nem a Microsoft Edge örökölt)
  * Safari (csak Mac, legújabb verzió)
  * Chrome (legújabb verzió)
  * Firefox (legújabb verzió)

* **Azure Portal**: 
  * Ha az SDK-ból vagy a Azure Portalból közvetlenül a munkaterületre lép, nem tekintheti meg a szabványos **Áttekintés** lapot, amely előfizetési adatokat tartalmaz a bővítményben. Ebben a forgatókönyvben nem válthat másik munkaterületre is. Ha egy másik munkaterületet szeretne megtekinteni, lépjen közvetlenül a [Azure Machine learning studióra](https://ml.azure.com) , és keresse meg a munkaterület nevét.
  * Minden eszköz (adatkészletek, kísérletek, számítások stb.) csak [Azure Machine learning Studióban](https://ml.azure.com)érhető el. *Nem* érhetők el a Azure Portal.

### <a name="resource-provider-errors"></a>Erőforrás-szolgáltatói hibák

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>A munkaterület áthelyezése

> [!WARNING]
> Ha áthelyezi a Azure Machine Learning munkaterületet egy másik előfizetésbe, vagy áthelyezi a tulajdonosi előfizetést egy új bérlőre, nem támogatott. Ez hibákhoz vezethet.

### <a name="deleting-the-azure-container-registry"></a>A Azure Container Registry törlése

A Azure Machine Learning munkaterület egyes műveletekhez Azure Container Registry (ACR) használ. Automatikusan létrehoz egy ACR-példányt, amikor először szüksége lesz rá.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="examples"></a>Példák

Példák a munkaterület létrehozására:
* [Munkaterület és számítási példány létrehozása](tutorial-1st-experiment-sdk-setup.md) Azure Portal használatával
* [Munkaterület létrehozása a saját környezetében](tutorial-1st-experiment-sdk-setup-local.md) a Python SDK használatával

## <a name="next-steps"></a>Következő lépések

Ha már rendelkezik munkaterülettel, megismerheti a [modellek betanítását és üzembe helyezését](tutorial-train-models-with-aml.md).