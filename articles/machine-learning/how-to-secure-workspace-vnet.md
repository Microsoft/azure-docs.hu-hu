---
title: Azure Machine Learning munkaterület biztonságossá tétele virtuális hálózatokkal
titleSuffix: Azure Machine Learning
description: A Azure Machine Learning munkaterület és a kapcsolódó erőforrások védelméhez használjon elkülönített Azure-Virtual Network.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.author: peterlu
author: peterclu
ms.date: 03/17/2021
ms.topic: conceptual
ms.custom: how-to, contperf-fy20q4, tracking-python, contperf-fy21q1
ms.openlocfilehash: a923f65e5c6183d045f4b7455e0a01edda75d499
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104584337"
---
# <a name="secure-an-azure-machine-learning-workspace-with-virtual-networks"></a>Azure Machine Learning munkaterület biztonságossá tétele virtuális hálózatokkal

Ebből a cikkből megtudhatja, hogyan védheti meg egy Azure Machine Learning munkaterületet és a hozzá tartozó erőforrásokat egy virtuális hálózatban.

Ez a cikk egy öt részes sorozat második része, amely végigvezeti egy Azure Machine Learning munkafolyamat biztonságossá tételének lépésein. Javasoljuk, hogy először olvassa el az első [részt: VNet – áttekintés](how-to-network-security-overview.md) az általános architektúra megismeréséhez. 

Tekintse meg a sorozat egyéb cikkeit:

[1. VNet – áttekintés](how-to-network-security-overview.md)  >  **2. A**  >  [3. munkaterület védelme Gondoskodjon a 4. képzési környezet védelméről](how-to-secure-training-vnet.md)  >  [. Gondoskodjon az 5. következtetési környezet védelméről](how-to-secure-inferencing-vnet.md)  >  [. A Studio funkcióinak engedélyezése](how-to-enable-studio-virtual-network.md)

Ebből a cikkből megtudhatja, hogyan engedélyezheti a következő munkaterület-erőforrásokat egy virtuális hálózaton:
> [!div class="checklist"]
> - Azure Machine Learning-munkaterület
> - Azure Storage-fiókok
> - Adattárolók és adatkészletek Azure Machine Learning
> - Azure Key Vault
> - Azure Container Registry

## <a name="prerequisites"></a>Előfeltételek

+ Olvassa el a [hálózati biztonság áttekintése című](how-to-network-security-overview.md) cikket a gyakori virtuális hálózati forgatókönyvek és az általános virtuális hálózati architektúra megismeréséhez.

+ Meglévő virtuális hálózat és alhálózat a számítási erőforrásokkal való használatra.

+ Ha erőforrásokat szeretne telepíteni egy virtuális hálózatba vagy alhálózatba, a felhasználói fióknak engedéllyel kell rendelkeznie a következő műveletekhez az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) szolgáltatásban:

    - "Microsoft. Network/virtualNetworks/JOIN/Action" a virtuális hálózati erőforráson.
    - "Microsoft. Network/virtualNetworks/subnet/JOIN/Action" az alhálózati erőforráson.

    A hálózatkezeléssel rendelkező Azure RBAC kapcsolatos további információkért tekintse meg a [hálózatkezelés beépített szerepköreit](../role-based-access-control/built-in-roles.md#networking) .


## <a name="secure-the-workspace-with-private-endpoint"></a>Munkaterület védelme privát végponttal

Az Azure Private link segítségével privát végponton keresztül csatlakozhat a munkaterülethez. A magánhálózati végpont a virtuális hálózaton belüli magánhálózati IP-címek készlete. Ezután korlátozhatja a hozzáférést a munkaterülethez, hogy csak a magánhálózati IP-címeken keresztül történjen. A privát hivatkozás segít csökkenteni az adatkiszűrése kockázatát.

A privát kapcsolati munkaterület beállításával kapcsolatos további információkért lásd: [privát hivatkozás konfigurálása](how-to-configure-private-link.md).

> [!Warning]
> Ha privát végpontokkal rendelkező munkaterületet biztosít, a saját maga nem biztosítja a végpontok közötti biztonságot. A megoldás egyes összetevőinek biztonságossá tételéhez a cikk további részében ismertetett lépéseket és a VNet sorozatot kell követnie.

## <a name="secure-azure-storage-accounts-with-service-endpoints"></a>Azure Storage-fiókok biztonságossá tétele szolgáltatási végpontokkal

Azure Machine Learning támogatja a szolgáltatás-végpontok vagy a magánhálózati végpontok használatára konfigurált Storage-fiókokat. Ebből a szakaszból megtudhatja, hogyan védheti meg az Azure Storage-fiókot a szolgáltatási végpontok használatával. Privát végpontok esetén tekintse meg a következő szakaszt.

Ha egy virtuális hálózatban lévő munkaterülethez Azure Storage-fiókot szeretne használni, kövesse az alábbi lépéseket:

1. A Azure Portal lépjen a munkaterületen használni kívánt tárolási szolgáltatáshoz.

   [![Az Azure Machine Learning munkaterülethez csatolt tárterület](./media/how-to-enable-virtual-network/workspace-storage.png)](./media/how-to-enable-virtual-network/workspace-storage.png#lightbox)

1. A Storage-szolgáltatásfiók lapon válassza a __hálózatkezelés__ lehetőséget.

   ![A Azure Portal Azure Storage lapján található hálózatkezelési felület](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks.png)

1. A __tűzfalak és virtuális hálózatok__ lapon végezze el a következő műveleteket:
    1. Válassza a __Kijelölt hálózatok__ lehetőséget.
    1. A __virtuális hálózatok__ területen válassza a __meglévő virtuális hálózati kapcsolat hozzáadása__ elemet. Ez a művelet hozzáadja azt a virtuális hálózatot, ahol a számítás található (lásd: 1. lépés).

        > [!IMPORTANT]
        > A Storage-fióknak ugyanabban a virtuális hálózatban és alhálózatban kell lennie, mint a képzéshez vagy következtetéshez használt számítási példányok vagy fürtök.

    1. Jelölje be a __megbízható Microsoft-szolgáltatások számára a Storage-fiók elérésének engedélyezése__ jelölőnégyzetet. Ez a változás nem biztosít hozzáférést az összes Azure-szolgáltatáshoz a Storage-fiókhoz.
    
        * Az **előfizetésében regisztrált** egyes szolgáltatások erőforrásai hozzáférhetnek az **előfizetésben található Storage-** fiókhoz a Select műveletekhez. Például naplókat írhat vagy biztonsági másolatokat készíthet.
        * Egyes szolgáltatások erőforrásai explicit módon férhetnek hozzá a Storage-fiókhoz, ha __egy Azure-szerepkört rendel__ hozzá a rendszerhez rendelt felügyelt identitáshoz.

        További információ: [Azure Storage-tűzfalak és virtuális hálózatok konfigurálása](../storage/common/storage-network-security.md#trusted-microsoft-services).

    > [!IMPORTANT]
    > Ha a Azure Machine Learning SDK-val dolgozik, a fejlesztési környezetnek képesnek kell lennie csatlakozni az Azure Storage-fiókhoz. Ha a Storage-fiók egy virtuális hálózaton belül van, a tűzfalnak engedélyeznie kell a hozzáférést a fejlesztői környezet IP-címéről.
    >
    > A Storage-fiókhoz való hozzáférés engedélyezéséhez keresse fel a Storage-fiókhoz tartozó __tűzfalakat és virtuális hálózatokat__ a *fejlesztői ügyfél webböngészőjéből*. Ezután használja az __ügyfél IP-címének hozzáadása__ jelölőnégyzetet az ügyfél IP-címének a __címtartományból__ való hozzáadásához. A __címtartomány__ mező használatával manuálisan is megadhatja a fejlesztési környezet IP-címét. Miután hozzáadta az ügyfél IP-címét, az SDK-val elérheti a Storage-fiókot.

   [![A Azure Portal tűzfalak és virtuális hálózatok panelje](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png#lightbox)

## <a name="secure-azure-storage-accounts-with-private-endpoints"></a>Privát végpontokkal rendelkező Azure Storage-fiókok biztonságossá tétele

Azure Machine Learning támogatja a szolgáltatás-végpontok vagy a magánhálózati végpontok használatára konfigurált Storage-fiókokat. Ha a Storage-fiók privát végpontokat használ, két magánhálózati végpontot kell konfigurálnia az alapértelmezett Storage-fiókhoz:
1. Egy **blob** Target alerőforrással rendelkező privát végpont.
1. Egy privát végpont egy **fájlmegosztási** alerőforrással (fájlmegosztás).

![A privát végpontok konfigurációs lapját bemutató képernyőfelvétel a blob és a fájl beállításaival](./media/how-to-enable-studio-virtual-network/configure-storage-private-endpoint.png)

Ha olyan Storage-fiókhoz szeretne magánhálózati végpontot beállítani, amely **nem** az alapértelmezett tároló, válassza ki a hozzáadni kívánt Storage-fióknak megfelelő **cél alerőforrás** -típust.

További információ: [privát végpontok használata az Azure Storage](../storage/common/storage-private-endpoints.md) -hoz

## <a name="secure-datastores-and-datasets"></a>Biztonságos adattárolók és adatkészletek

Ebből a szakaszból megtudhatja, hogyan használhatók az adattár és az adatkészletek az SDK-ban egy virtuális hálózattal. A Studio-felülettel kapcsolatos további információkért lásd: [Azure Machine learning Studio használata virtuális hálózaton](how-to-enable-studio-virtual-network.md).

Az SDK-val való hozzáféréshez az egyes szolgáltatásokhoz szükséges hitelesítési módszert kell használnia, amelyet az adatai tárolnak. Ha például regisztrál egy adattárt Azure Data Lake Store Gen2 eléréséhez, akkor továbbra is az [Azure Storage-szolgáltatásokhoz való kapcsolódáshoz](how-to-access-data.md#azure-data-lake-storage-generation-2)szükséges egyszerű szolgáltatásnevet kell használnia.

### <a name="disable-data-validation"></a>Adatérvényesítés letiltása

Alapértelmezés szerint a Azure Machine Learning az adatok érvényességét és a hitelesítő adatokat ellenőrzi, amikor az SDK használatával próbál hozzáférni az adatokhoz. Ha az adatközpont egy virtuális hálózat mögött található, Azure Machine Learning nem tudja befejezni ezeket az ellenőrzéseket. Az ellenőrzés megkerüléséhez létre kell hoznia az érvényesítést kihagyó adattárokat és adatkészleteket.

### <a name="use-datastores"></a>Adattárolók használata

 Azure Data Lake Store Gen1 és Azure Data Lake Store a Gen2 alapértelmezés szerint kihagyja az érvényesítést, így nincs szükség további műveletre. A következő szolgáltatások esetében azonban hasonló szintaxist használhat az adattár-érvényesítés kihagyása érdekében:

- Azure Blob Storage
- Azure-fájlmegosztás
- PostgreSQL
- Azure SQL Database

A következő mintakód egy új Azure Blob-adattárat és-készletet hoz létre `skip_validation=True` .

```python
blob_datastore = Datastore.register_azure_blob_container(workspace=ws,  

                                                         datastore_name=blob_datastore_name,  

                                                         container_name=container_name,  

                                                         account_name=account_name, 

                                                         account_key=account_key, 

                                                         skip_validation=True ) // Set skip_validation to true
```

### <a name="use-datasets"></a>Adatkészletek használata

Az adatkészlet-ellenőrzés kihagyásának szintaxisa hasonló a következő adatkészletek típusaihoz:
- Tagolt fájl
- JSON 
- Parquet
- SQL
- Fájl

A következő kód egy új JSON-adatkészletet és-készletet hoz létre `validate=False` .

```python
json_ds = Dataset.Tabular.from_json_lines_files(path=datastore_paths, 

validate=False) 

```

## <a name="secure-azure-key-vault"></a>Biztonságos Azure Key Vault

Azure Machine Learning egy társított Key Vault-példányt használ a következő hitelesítő adatok tárolásához:
* A társított Storage-fiókhoz tartozó kapcsolatok karakterlánca
* Az Azure Container repository példányainak jelszavai
* Adattárakhoz való kapcsolódási karakterláncok

Ha Azure Machine Learning kísérletezési képességeket szeretne használni a virtuális hálózat mögötti Azure Key Vault, kövesse az alábbi lépéseket:

1. Lépjen a munkaterülethez társított Key Vault.

1. A __Key Vault__ oldalon, a bal oldali ablaktáblán válassza a __hálózatkezelés__ lehetőséget.

1. A __tűzfalak és virtuális hálózatok__ lapon végezze el a következő műveleteket:
    1. __A hozzáférés engedélyezése lehetőségnél__ válassza a __privát végpont és a kiválasztott hálózatok__ elemet.
    1. A __virtuális hálózatok__ területen válassza a __meglévő virtuális hálózatok hozzáadása__ lehetőséget annak a virtuális hálózatnak a hozzáadásához, ahol a kísérletezési számítás található.
    1. A __megbízható Microsoft-szolgáltatások a tűzfal megkerülésének engedélyezése__ területen válassza az __Igen__ lehetőséget.

   [![A Key Vault panel "tűzfalak és virtuális hálózatok" szakasza](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png#lightbox)

## <a name="enable-azure-container-registry-acr"></a>Azure Container Registry engedélyezése (ACR)

Ha Azure Container Registryt szeretne használni egy virtuális hálózaton belül, meg kell felelnie a következő követelményeknek:

* A Azure Container Registrynak prémium verziójúnak kell lennie. További információ a frissítésről: a [SKU módosítása](../container-registry/container-registry-skus.md#changing-tiers).

* A Azure Container Registrynak ugyanabban a virtuális hálózatban és alhálózatban kell lennie, mint a betanításhoz vagy következtetéshez használt Storage-fióknak és számítási céloknak.

* A Azure Machine Learning-munkaterületnek tartalmaznia kell egy [Azure Machine learning számítási fürtöt](how-to-create-attach-compute-cluster.md).

    Ha az ACR egy virtuális hálózat mögött van, Azure Machine Learning nem tud közvetlenül Docker-lemezképeket felépíteni. Ehelyett a rendszer a számítási fürtöt használja a rendszerképek létrehozásához.

    > [!IMPORTANT]
    > A Docker-rendszerképek létrehozásához használt számítási fürtnek képesnek kell lennie a modellek betanításához és üzembe helyezéséhez használt csomag-Tárházak elérésére. Előfordulhat, hogy olyan hálózati biztonsági szabályokat kell felvennie, amelyek engedélyezik a nyilvános repók elérését, [privát Python-csomagokat használhatnak](how-to-use-private-python-packages.md), vagy olyan [Egyéni Docker-rendszerképeket](how-to-train-with-custom-image.md) használnak, amelyek már tartalmazzák a csomagokat

A követelmények teljesítése után a következő lépésekkel engedélyezheti a Azure Container Registry.

> [!TIP]
> Ha nem használ meglévő Azure Container Registry a munkaterület létrehozásakor, akkor előfordulhat, hogy az egyik nem létezik. Alapértelmezés szerint a munkaterület nem hoz létre ACR-példányt, amíg erre szüksége van. Ha a szakasz lépéseinek használata előtt szeretné kényszeríteni egy adott modell létrehozását, betanítását vagy üzembe helyezését a munkaterületen.

1. Keresse meg a munkaterülethez tartozó Azure Container Registry nevét az alábbi módszerek egyikének használatával:

    __Azure Portal__

    A munkaterület Áttekintés szakaszában a __beállításjegyzék__ értéke a Azure Container Registryra hivatkozik.

    :::image type="content" source="./media/how-to-enable-virtual-network/azure-machine-learning-container-registry.png" alt-text="Azure Container Registry a munkaterülethez" border="true":::

    __Azure CLI__

    Ha [telepítette az Azure CLI Machine learning bővítményét](reference-azure-machine-learning-cli.md), a `az ml workspace show` parancs használatával jelenítheti meg a munkaterület adatait.

    ```azurecli-interactive
    az ml workspace show -w yourworkspacename -g resourcegroupname --query 'containerRegistry'
    ```

    A parancs a következőhöz hasonló értéket ad vissza: `"/subscriptions/{GUID}/resourceGroups/{resourcegroupname}/providers/Microsoft.ContainerRegistry/registries/{ACRname}"` . A karakterlánc utolsó része a munkaterület Azure Container Registry neve.

1. Korlátozza a virtuális hálózathoz való hozzáférést a [hálózati hozzáférés konfigurálása a beállításjegyzékben](../container-registry/container-registry-vnet.md#configure-network-access-for-registry)című témakör lépéseit követve. A virtuális hálózat hozzáadásakor válassza ki a virtuális hálózatot és az alhálózatot a Azure Machine Learning erőforrásaihoz.

1. Konfigurálja az ACR-t a munkaterület számára a [megbízható szolgáltatások általi hozzáférés engedélyezéséhez](../container-registry/allow-access-trusted-services.md).

1. A Azure Machine Learning Python SDK használatával konfigurálhat egy számítási fürtöt a Docker-rendszerképek létrehozásához. A következő kódrészlet bemutatja, hogyan teheti meg ezt:

    ```python
    from azureml.core import Workspace
    # Load workspace from an existing config file
    ws = Workspace.from_config()
    # Update the workspace to use an existing compute cluster
    ws.update(image_build_compute = 'mycomputecluster')
    # To switch back to using ACR to build (if ACR is not in the VNet):
    # ws.update(image_build_compute = None)
    ```

    > [!IMPORTANT]
    > A Storage-fióknak, a számítási fürtnek és a Azure Container Registrynek a virtuális hálózat azonos alhálózatán kell lennie.
    
    További információkért tekintse meg a [frissítési ()](/python/api/azureml-core/azureml.core.workspace.workspace#update-friendly-name-none--description-none--tags-none--image-build-compute-none--enable-data-actions-none-) metódus-referenciát.

## <a name="next-steps"></a>Következő lépések

Ez a cikk egy öt részből álló virtuális hálózati sorozat második része. A virtuális hálózatok biztonságossá tételéhez tekintse meg a cikkek további részeit:

* [1. rész: a Virtual Network áttekintése](how-to-network-security-overview.md)
* [3. rész: a képzési környezet biztonságossá tétele](how-to-secure-training-vnet.md)
* [4. rész: a következtetési környezet biztonságossá tétele](how-to-secure-inferencing-vnet.md)
* [5. rész: a Studio funkcióinak engedélyezése](how-to-enable-studio-virtual-network.md)

Tekintse meg az [Egyéni DNS](how-to-custom-dns.md) használata névfeloldáshoz című cikket is.