---
title: Biztonságossá Azure Machine Learning munkaterület virtuális hálózatokkal
titleSuffix: Azure Machine Learning
description: Használjon elkülönített Azure-Virtual Network, hogy biztonságossá Azure Machine Learning munkaterületét és a társított erőforrásokat.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.author: peterlu
author: peterclu
ms.date: 03/17/2021
ms.topic: conceptual
ms.custom: how-to, contperf-fy20q4, tracking-python, contperf-fy21q1
ms.openlocfilehash: 20f75580c425cee9128f9c94123dcf902642eac4
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107531023"
---
# <a name="secure-an-azure-machine-learning-workspace-with-virtual-networks"></a>Biztonságossá Azure Machine Learning munkaterület virtuális hálózatokkal

Ebből a cikkből megtudhatja, hogyan biztosíthatja egy virtuális Azure Machine Learning munkaterület és a társított erőforrások biztonságossá Azure Machine Learning egy virtuális hálózatban.

Ez a cikk egy ötrészes sorozat második része, amely végigvezeti egy Azure Machine Learning biztosításán. Javasoljuk, hogy először olvassa el az [első rész: VNet](how-to-network-security-overview.md) áttekintése részt, hogy először a teljes architektúrát értse meg. 

Tekintse meg a sorozat többi cikkét:

[1. A VNet áttekintése](how-to-network-security-overview.md)  >  **2. A munkaterület biztonságossáése**  >  [3. A betanító környezet védelme](how-to-secure-training-vnet.md)  >  [4. A következtetési környezet](how-to-secure-inferencing-vnet.md)  >  [5. védelme. Studio-funkciók engedélyezése](how-to-enable-studio-virtual-network.md)

Ebből a cikkből megtudhatja, hogyan engedélyezheti a következő munkaterület-erőforrásokat egy virtuális hálózatban:
> [!div class="checklist"]
> - Azure Machine Learning-munkaterület
> - Azure Storage-fiókok
> - Azure Machine Learning és adatkészletek
> - Azure Key Vault
> - Azure Container Registry

## <a name="prerequisites"></a>Előfeltételek

+ A gyakori [virtuális hálózati forgatókönyvekkel](how-to-network-security-overview.md) és a teljes virtuális hálózati architektúrával kapcsolatos további információért olvassa el a Hálózati biztonság áttekintése cikket.

+ Meglévő virtuális hálózat és alhálózat, amely a számítási erőforrásokkal használható.

+ Az erőforrások virtuális hálózaton vagy alhálózaton való üzembe helyezéséhez a felhasználói fióknak engedéllyel kell rendelkeznie a következő műveletekhez az Azure szerepköralapú hozzáférés-vezérlésében (Azure RBAC):

    - "Microsoft.Network/virtualNetworks/join/action" a virtuális hálózati erőforráson.
    - "Microsoft.Network/virtualNetworks/subnet/join/action" az alhálózati erőforráson.

    Az Azure RBAC hálózattal való használatával kapcsolatos további információkért lásd a hálózatba épített [szerepköröket.](../role-based-access-control/built-in-roles.md#networking)


## <a name="secure-the-workspace-with-private-endpoint"></a>A munkaterület biztonságossáése privát végponttal

Azure Private Link lehetővé teszi, hogy privát végponttal csatlakozzon a munkaterülethez. A privát végpont magánhálózati IP-címek készlete a virtuális hálózaton belül. Ezután korlátozhatja a munkaterülethez való hozzáférést, hogy csak a magánhálózati IP-címeken keresztül következtetsen. Private Link csökkenti az adatok kiszivárgásának kockázatát.

A munkaterületek beállításával kapcsolatos további Private Link lásd: A munkaterület [Private Link.](how-to-configure-private-link.md)

> [!Warning]
> A munkaterület privát végpontokkal való biztonságossá tétele önmagában nem biztosítja a végpontok teljes biztonságát. A megoldás egyes összetevőinek biztonságba helyezése érdekében a cikk további részében és a VNet-sorozatban található lépéseket kell követnie.

## <a name="secure-azure-storage-accounts-with-service-endpoints"></a>Azure-tárfiókok biztosítása szolgáltatásvégpontokkal

Azure Machine Learning támogatja a szolgáltatásvégpont vagy privát végpontok használatára konfigurált tárfiókokat. Ebben a szakaszban megtudhatja, hogyan biztosíthatja az Azure Storage-fiók biztonságát szolgáltatásvégpontokkal. Privát végpontok esetén lásd a következő szakaszt.

Ha Azure Storage-fiókot használ a munkaterülethez egy virtuális hálózatban, kövesse az alábbi lépéseket:

1. A Azure Portal a munkaterületen használni kívánt tárolási szolgáltatáshoz.

   [![A munkaterülethez csatolt Azure Machine Learning tárterület](./media/how-to-enable-virtual-network/workspace-storage.png)](./media/how-to-enable-virtual-network/workspace-storage.png#lightbox)

1. A társzolgáltatás-fiók oldalán válassza a __Hálózat lehetőséget.__

   ![Az Azure Storage oldalának hálózatterülete a Azure Portal](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks.png)

1. A __Tűzfalak és virtuális hálózatok lapon__ tegye a következőket:
    1. Válassza a __Kijelölt hálózatok__ lehetőséget.
    1. A __Virtuális hálózatok alatt__ válassza a Meglévő virtuális hálózat hozzáadása __hivatkozást.__ Ez a művelet hozzáadja a virtuális hálózatot, ahol a számítás található (lásd az 1. lépést).

        > [!IMPORTANT]
        > A tárfióknak ugyanabban a virtuális hálózatban és alhálózatban kell lennie, mint a betanításra vagy következtetésre használt számítási példányok vagy fürtök.

    1. Jelölje be az Allow trusted Microsoft-szolgáltatások to access this storage account (Megbízható felhasználók hozzáférhetnek ehhez a __tárfiókhoz)__ jelölőnégyzetet. Ez a módosítás nem biztosít hozzáférést minden Azure-szolgáltatás számára a tárfiókhoz.
    
        * Egyes, az előfizetésében **regisztrált** szolgáltatások erőforrásai hozzáférhetnek az ugyanabban az előfizetésben regisztrált tárfiókhoz **bizonyos** műveletekhez. Például naplók írása vagy biztonsági másolatok létrehozása.
        * Egyes szolgáltatások erőforrásai explicit hozzáférést kaphatnak a tárfiókhoz, ha hozzárendelnek egy Azure-szerepkört __a__ rendszer által hozzárendelt felügyelt identitáshoz.

        További információ: [Azure Storage-tűzfalak és virtuális hálózatok konfigurálása](../storage/common/storage-network-security.md#trusted-microsoft-services).

    > [!IMPORTANT]
    > Az Azure Machine Learning SDK használatakor a fejlesztési környezetnek képesnek kell lennie csatlakozni az Azure Storage-fiókhoz. Ha a tárfiók egy virtuális hálózaton belül van, a tűzfalnak engedélyeznie kell a hozzáférést a fejlesztési környezet IP-címével.
    >
    > A tárfiókhoz való hozzáférés  engedélyezéséhez keresse fel a tárfiók tűzfalait és virtuális hálózatait a fejlesztői *ügyfél webböngészőjében.* Ezután az __Ügyfél IP-címének hozzáadása__ jelölőnégyzet használatával adja hozzá az ügyfél IP-címét a __CÍMTARTOMÁNYhoz.__ A CÍMTARTOMÁNY __mezőben__ manuálisan is megadhatja a fejlesztési környezet IP-címét. Az ügyfél IP-címének hozzáadása után hozzáférhet a tárfiókhoz az SDK használatával.

   [![A "Tűzfalak és virtuális hálózatok" panel a Azure Portal](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/storage-firewalls-and-virtual-networks-page.png#lightbox)

## <a name="secure-azure-storage-accounts-with-private-endpoints"></a>Azure-tárfiókok biztonságossá tevésének biztosítása privát végpontokkal

Azure Machine Learning támogatja a szolgáltatásvégpont vagy privát végpontok használatára konfigurált tárfiókokat. Ha a tárfiók privát végpontokat használ, két privát végpontot kell konfigurálnia az alapértelmezett tárfiókhoz:
1. Egy privát végpont egy **blobcél** alforrásával.
1. Egy privát végpont **egy** fájlcél alforrásával (fájlmegosztás).

![Képernyőkép a privát végpont konfigurációs lapról blob- és fájlbeállításokkal](./media/how-to-enable-studio-virtual-network/configure-storage-private-endpoint.png)

Ha privát végpontot szeretne konfigurálni  egy olyan tárfiókhoz, amely nem az alapértelmezett tárterület, válassza a hozzáadni kívánt tárfióknak megfelelő Cél alforrástípust. 

További információ: [Privát végpontok használata az Azure Storage-hoz](../storage/common/storage-private-endpoints.md)

## <a name="secure-datastores-and-datasets"></a>Biztonságos adattárak és adatkészletek

Ebben a szakaszban megtudhatja, hogyan használhatja az adattárat és az adatkészleteket az SDK felhasználói élményében egy virtuális hálózattal. A Studio felhasználói élményével kapcsolatos további információkért lásd a Azure Machine Learning stúdió [virtuális hálózatokban való használatát.](how-to-enable-studio-virtual-network.md)

Ahhoz, hogy az SDK-val hozzáférjen az adatokhoz, azt a hitelesítési módszert kell használnia, amelyet az adatok tárolására használt egyedi szolgáltatás megkövetel. Ha például regisztrál egy adattárat az Azure Data Lake Store Gen2 eléréséhez, továbbra is egy egyszerű szolgáltatást kell használnia a Csatlakozás az Azure Storage-szolgáltatásokhoz [dokumentumban dokumentáltak szerint.](how-to-access-data.md#azure-data-lake-storage-generation-2)

### <a name="disable-data-validation"></a>Adatérvényesítés letiltása

Alapértelmezés szerint a Azure Machine Learning az adatok érvényességét és a hitelesítő adatok ellenőrzését, amikor az SDK használatával próbál meg hozzáférni az adatokhoz. Ha az adatok egy virtuális hálózat mögött vannak, Azure Machine Learning nem tudja végrehajtani ezeket az ellenőrzéseket. Az ellenőrzés megkerülése érdekében olyan adattárolókat és adatkészleteket kell létrehoznia, amelyek kihagyják az ellenőrzést.

### <a name="use-datastores"></a>Adattárolók használata

 Az Azure Data Lake Store Gen1 és az Azure Data Lake Store Gen2 alapértelmezés szerint kihagyja az ellenőrzést, így nincs szükség további műveletre. A következő szolgáltatások esetén azonban hasonló szintaxissal kihagyhatja az adattár érvényesítését:

- Azure Blob Storage
- Azure-fájlmegosztás
- PostgreSQL
- Azure SQL Database

Az alábbi kódminta létrehoz egy új Azure Blob-adattárat, és beállítja a `skip_validation=True` következőt: .

```python
blob_datastore = Datastore.register_azure_blob_container(workspace=ws,  

                                                         datastore_name=blob_datastore_name,  

                                                         container_name=container_name,  

                                                         account_name=account_name, 

                                                         account_key=account_key, 

                                                         skip_validation=True ) // Set skip_validation to true
```

### <a name="use-datasets"></a>Adatkészletek használata

Az adatkészlet-érvényesítést kihagyó szintaxis a következő adatkészlettípusokhoz hasonló:
- Tagolt fájl
- JSON 
- Parquet
- SQL
- Fájl

Az alábbi kód létrehoz egy új JSON-adatkészletet, és beállítja a `validate=False` következőt: .

```python
json_ds = Dataset.Tabular.from_json_lines_files(path=datastore_paths, 

validate=False) 

```

## <a name="secure-azure-key-vault"></a>Biztonságos Azure Key Vault

Azure Machine Learning társított Key Vault használja a következő hitelesítő adatok tárolására:
* A társított tárfiók kapcsolati sztring
* Az Azure Container Repository-példányok jelszavai
* Adattárak kapcsolati sztringek

A Azure Machine Learning a virtuális Azure Key Vault mögötti virtuális gépekkel való kísérletezéshez kövesse az alábbi lépéseket:

1. Ugrás a Key Vault társított munkaterületre.

1. A __Key Vault__ bal oldali panelen válassza a __Hálózat lehetőséget.__

1. A __Tűzfalak és virtuális hálózatok lapon__ tegye a következőket:
    1. A __Hozzáférés engedélyezése innen: alatt__ válassza a Privát végpont és a kiválasztott hálózatok __lehetőséget.__
    1. A __Virtuális hálózatok alatt__ válassza a Meglévő virtuális hálózatok __hozzáadása__ lehetőséget a kísérletezés számítási helyének megfelelő virtuális hálózat hozzáadásához.
    1. A Allow trusted Microsoft-szolgáltatások to bypass this firewall (Megbízható felhasználók __megkerülhetik ezt a tűzfalat)__ alatt válassza az __Igen lehetőséget.__

   [![A Tűzfalak és virtuális hálózatok szakasz a Key Vault panelen](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png)](./media/how-to-enable-virtual-network/key-vault-firewalls-and-virtual-networks-page.png#lightbox)

## <a name="enable-azure-container-registry-acr"></a>Az Azure Container Registry (ACR) engedélyezése

A virtuális Azure Container Registry való használathoz az alábbi követelményeknek kell megfelelnie:

* A Azure Container Registry prémium verziójúnak kell lennie. A frissítéssel kapcsolatos további információkért lásd: [A SKUs módosítása.](../container-registry/container-registry-skus.md#changing-tiers)

* A Azure Container Registry azonos virtuális hálózatban és alhálózatban kell lennie, mint a betanításra vagy következtetésre használt tárfióknak és számítási céloknak.

* A Azure Machine Learning munkaterületnek tartalmaznia kell egy [Azure Machine Learning számítási fürtöt.](how-to-create-attach-compute-cluster.md)

    Ha az ACR egy virtuális hálózat mögött van, Azure Machine Learning nem használhatja közvetlenül Docker-rendszerképek építésre. Ehelyett a számítási fürtöt használja a rendszerképek felépítéséhez.

    > [!IMPORTANT]
    > A Docker-lemezképek felépítéséhez használt számítási fürtnek hozzá kell tudnia férni a modellek betanítása és üzembe helyezése érdekében használt csomagtárakhoz. Előfordulhat, hogy olyan hálózati biztonsági szabályokat kell hozzáadnia, amelyek engedélyezik a nyilvános adattárakhoz való hozzáférést, privát [Python-csomagokat](how-to-use-private-python-packages.md)használ, vagy olyan egyéni [Docker-rendszerképeket](how-to-train-with-custom-image.md) kell használnia, amelyek már tartalmazzák a csomagokat.

A követelmények teljesítése után a következő lépésekkel engedélyezheti a Azure Container Registry.

> [!TIP]
> Ha nem használt meglévő Azure Container Registry a munkaterület létrehozásakor, előfordulhat, hogy nem létezik ilyen. Alapértelmezés szerint a munkaterület csak akkor hoz létre ACR-példányt, ha szüksége van rá. Ha kényszerítenie kell egy modell létrehozását, az ebben a szakaszban található lépések használata előtt betanítja vagy üzembe helyezheti a modellt a munkaterület használatával.

1. Keresse meg a munkaterület Azure Container Registry nevét az alábbi módszerek egyikével:

    __Azure Portal__

    A munkaterület áttekintési szakaszában a __Beállításazonosító__ hivatkozásokat tartalmaz a Azure Container Registry.

    :::image type="content" source="./media/how-to-enable-virtual-network/azure-machine-learning-container-registry.png" alt-text="Azure Container Registry munkaterülethez" border="true":::

    __Azure CLI__

    Ha telepítette [az Azure CLI Machine Learning bővítményét,](reference-azure-machine-learning-cli.md)az paranccsal is meg tudja jelenni a munkaterület `az ml workspace show` adatait.

    ```azurecli-interactive
    az ml workspace show -w yourworkspacename -g resourcegroupname --query 'containerRegistry'
    ```

    Ez a parancs a következő értékhez hasonló értéket ad `"/subscriptions/{GUID}/resourceGroups/{resourcegroupname}/providers/Microsoft.ContainerRegistry/registries/{ACRname}"` vissza: . A sztring utolsó része a munkaterülethez Azure Container Registry neve.

1. Korlátozza a virtuális hálózathoz való hozzáférést a Hálózati hozzáférés konfigurálása [beállításjegyzékhez lépéseit követve.](../container-registry/container-registry-vnet.md#configure-network-access-for-registry) A virtuális hálózat hozzáadásakor válassza ki a virtuális hálózatot és az alhálózatot a Azure Machine Learning számára.

1. Konfigurálja a munkaterület ACR-ét a [Megbízható szolgáltatások hozzáférésének engedélyezése beállításra.](../container-registry/allow-access-trusted-services.md)

1. A Azure Machine Learning Python SDK használatával konfigurálhatja a számítási fürtöt Docker-rendszerképek építéséhez. A következő kódrészlet ezt mutatja be:

    ```python
    from azureml.core import Workspace
    # Load workspace from an existing config file
    ws = Workspace.from_config()
    # Update the workspace to use an existing compute cluster
    ws.update(image_build_compute = 'mycomputecluster')
    # To switch back to using ACR to build (if ACR is not in the VNet):
    # ws.update(image_build_compute = '')
    ```

    > [!IMPORTANT]
    > A tárfióknak, a számítási Azure Container Registry és a virtuális hálózatnak ugyanabban az alhálózatban kell lennie.
    
    További információért tekintse meg az [update() metódus](/python/api/azureml-core/azureml.core.workspace.workspace#update-friendly-name-none--description-none--tags-none--image-build-compute-none--enable-data-actions-none-) referenciáját.

## <a name="next-steps"></a>Következő lépések

Ez a cikk egy ötrészes virtuális hálózati sorozat második része. A további cikkekből megtudhatja, hogyan biztosíthatja a virtuális hálózatok biztonságát:

* [1. rész: A virtuális hálózat áttekintése](how-to-network-security-overview.md)
* [3. rész: A betanító környezet védelme](how-to-secure-training-vnet.md)
* [4. rész: A következtetési környezet védelme](how-to-secure-inferencing-vnet.md)
* [5. rész: A Studio funkcióinak engedélyezése](how-to-enable-studio-virtual-network.md)

Lásd még az egyéni [DNS névfeloldáshoz való](how-to-custom-dns.md) használatával kapcsolatos cikket.
