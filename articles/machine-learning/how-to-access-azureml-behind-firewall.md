---
title: Tűzfal használata
titleSuffix: Azure Machine Learning
description: Azure Machine Learning-munkaterületek Azure-tűzfalakkal való hozzáférésének szabályozása. Ismerje meg azokat a gazdagépeket, amelyeket engedélyeznie kell a tűzfalon.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 11/18/2020
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 295228e9eaa3529b05055869bd46f9aefc938a6f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102212773"
---
# <a name="use-workspace-behind-a-firewall-for-azure-machine-learning"></a>Munkaterület használata tűzfal mögött Azure Machine Learning

Ebből a cikkből megtudhatja, hogyan konfigurálhatja a Azure Firewallt a Azure Machine Learning-munkaterülethez és a nyilvános internethez való hozzáférés szabályozásához. Ha többet szeretne megtudni a Azure Machine Learning biztonságossá tételéről, tekintse meg a [Azure Machine learning vállalati biztonsági szolgáltatásait](concept-enterprise-security.md)ismertető témakört.

> [!WARNING]
> A tűzfal mögötti adattárakhoz való hozzáférés csak a Code First experiences szolgáltatásban támogatott. A [Azure Machine learning Studio](overview-what-is-machine-learning-studio.md) használatával férhet hozzá a tűzfal mögötti adataihoz. Ahhoz, hogy az adattárolást egy privát hálózaton a Studióval működjön, először [be kell állítania egy virtuális hálózatot](../virtual-network/quick-create-portal.md) , és [a stúdiónak hozzá kell férnie a virtuális hálózaton belül tárolt](how-to-enable-studio-virtual-network.md)adatmennyiséghez.

## <a name="azure-firewall"></a>Azure Firewall

Azure Firewall használatakor a __cél hálózati címfordítás (DNAT)__ használatával hozzon létre NAT-szabályokat a bejövő forgalom számára. A kimenő forgalom esetében hozzon létre __hálózati__ és/vagy __alkalmazási__ szabályokat. Ezek a szabálygyűjtemény-gyűjtemények részletesebben ismertetik a [Azure Firewall fogalmakat](../firewall/firewall-faq.yml#what-are-some-azure-firewall-concepts)ismertető témakört.

### <a name="inbound-configuration"></a>Bejövő konfiguráció

Ha Azure Machine Learning __számítási példányt__ vagy __számítási fürtöt__ használ, adjon hozzá egy [felhasználó által megadott útvonalakat (UDR)](../virtual-network/virtual-networks-udr-overview.md) a Azure Machine learning erőforrásokat tartalmazó alhálózathoz. Ez __az útvonal__ a és az erőforrások IP-címeiről továbbítja a forgalmat `BatchNodeManagement` `AzureMachineLearning` a számítási példány és a számítási fürt nyilvános IP-címére.

Ezek a UDR lehetővé teszik a Batch szolgáltatás számára a feladatütemezés számítási csomópontjaival való kommunikációját. Adja hozzá a Azure Machine Learning szolgáltatáshoz tartozó IP-címet is, mivel ez szükséges a számítási példányokhoz való hozzáféréshez. A Azure Machine Learning szolgáltatás IP-címének hozzáadásakor hozzá kell adnia az IP-címet mind az __elsődleges,__ mind a másodlagos Azure-régióhoz. Az elsődleges régió, ahol a munkaterület található.

A másodlagos régió megtalálásához tekintse meg az [üzletmenet folytonosságának biztosítása & a vész-helyreállítás az Azure párosított régiók használatával](../best-practices-availability-paired-regions.md#azure-regional-pairs)című témakört. Ha például az Azure Machine Learning-szolgáltatás az USA 2. keleti régiójában található, a másodlagos régió az USA középső régiója. 

A Batch szolgáltatás és a Azure Machine Learning szolgáltatás IP-címeinek listájának megjelenítéséhez használja a következő módszerek egyikét:

* Töltse le az [Azure IP-címtartományok és a szolgáltatás címkéit](https://www.microsoft.com/download/details.aspx?id=56519) , és keresse meg a és a fájlt `BatchNodeManagement.<region>` `AzureMachineLearning.<region>` , ahol `<region>` az az Azure-régió.

* Az adatok letöltéséhez használja az [Azure CLI](/cli/azure/install-azure-cli) -t. Az alábbi példa letölti az IP-cím adatait, és kiszűri az USA 2. keleti régiójában (elsődleges) és az USA középső régiójában (másodlagos) található adatokat:

    ```azurecli-interactive
    az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'Batch')] | [?properties.region=='eastus2']"
    # Get primary region IPs
    az network list-service-tags -l "East US 2" --query "values[?starts_with(id, 'AzureMachineLearning')] | [?properties.region=='eastus2']"
    # Get secondary region IPs
    az network list-service-tags -l "Central US" --query "values[?starts_with(id, 'AzureMachineLearning')] | [?properties.region=='centralus']"
    ```

    > [!TIP]
    > Ha az USA-Virginia, US-Arizona régiókat vagy a kínai – Kelet-2 régiót használja, ezek a parancsok nem adnak vissza IP-címet. Ehelyett használja az alábbi hivatkozások egyikét az IP-címek listájának letöltéséhez:
    >
    > * [Azure IP-címtartományok és szolgáltatás-címkék Azure Government](https://www.microsoft.com/download/details.aspx?id=57063)
    > * [Azure IP-címtartományok és szolgáltatás-címkék az Azure China-hoz](https://www.microsoft.com//download/details.aspx?id=57062)

A UDR hozzáadásakor adja meg az útvonalat az egyes kapcsolódó batch IP-címek előtagjaként, és állítsa be a __következő ugrás típusát__ az __Internet__ értékre. Az alábbi képen látható példa erre a UDR mutat a Azure Portalban:

![UDR – példa a címek előtagjaként való használatra](./media/how-to-enable-virtual-network/user-defined-route.png)

> [!IMPORTANT]
> Az IP-címek idővel változhatnak.

További információ: [Azure batch készlet létrehozása egy virtuális hálózaton](../batch/batch-virtual-network.md#user-defined-routes-for-forced-tunneling).

### <a name="outbound-configuration"></a>Kimenő konfiguráció

1. Adja hozzá a __hálózati szabályokat__, és engedélyezze a következő szolgáltatási címkékre __irányuló__ és __onnan érkező__ forgalmat:

    * AzureActiveDirectory
    * AzureMachineLearning
    * AzureResourceManager
    * Storage. region
    * Kulcstartó. régió
    * ContainerRegistry. region

    Ha a Microsoft által biztosított alapértelmezett Docker-rendszerképeket szeretné használni, és engedélyezi a felhasználó által felügyelt függőségeket, akkor a következő szolgáltatási címkéket is hozzá kell adnia:

    * MicrosoftContainerRegistry. region
    * AzureFrontDoor.FirstParty

    A-t tartalmazó bejegyzések esetében `region` cserélje le a elemet az Ön által használt Azure-régióra. Például: `keyvault.westus`.

    A __protokoll__ esetében válassza a lehetőséget `TCP` . A forrás és a cél __portok__ esetében válassza a lehetőséget `*` .

1. Adja hozzá az __alkalmazáshoz tartozó szabályokat__ a következő gazdagépekhez:

    > [!NOTE]
    > Ez nem teljes lista az interneten található összes Python-erőforráshoz szükséges gazdagépekről, csak a leggyakrabban használt. Ha például hozzá kell férnie egy GitHub-adattárhoz vagy egy másik gazdagéphez, meg kell adnia és hozzá kell adnia a szükséges gazdagépeket az adott forgatókönyvhöz.

    | **Állomásnév** | **Cél** |
    | ---- | ---- |
    | **graph.windows.net** | Azure Machine Learning számítási példány/fürt használja. |
    | **anaconda.com**</br>**\*. anaconda.com** | Az alapértelmezett csomagok telepítéséhez használatos. |
    | **\*. anaconda.org** | A tárház-adatgyűjtéshez használatos. |
    | **pypi.org** | Az alapértelmezett indexből származó függőségek listázására használatos, és az indexet a felhasználói beállítások nem írják felül. Ha a rendszer felülírja az indexet, a **\* . pythonhosted.org** is engedélyeznie kell. |
    | **cloud.r-project.org** | Az R-fejlesztéshez használt CRAN-csomagok telepítésekor használatos. |
    | **\*pytorch.org** | A PyTorch alapján néhány példa használ. |
    | **\*. tensorflow.org** | A Tensorflow alapján néhány példa használ. |

    A __protokoll: Port__ beállításnál válassza a __http használata, HTTPS__ lehetőséget.

    Az alkalmazási szabályok konfigurálásával kapcsolatos további információkért lásd: [Azure Firewall telepítése és konfigurálása](../firewall/tutorial-firewall-deploy-portal.md#configure-an-application-rule).

1. Az Azure Kubernetes szolgáltatásban (ak) üzembe helyezett modellekhez való hozzáférés korlátozása: [a kimenő forgalom korlátozása az Azure Kubernetes szolgáltatásban](../aks/limit-egress-traffic.md).

## <a name="other-firewalls"></a>Egyéb tűzfalak

Az ebben a szakaszban található útmutatás általános, mivel minden egyes tűzfal saját terminológiával és specifikus konfigurációval rendelkezik. Ha kérdése van a tűzfalon keresztüli kommunikáció engedélyezésével kapcsolatban, tekintse meg az Ön által használt tűzfal dokumentációját.

Ha nincs megfelelően konfigurálva, a tűzfal problémákat okozhat a munkaterület használatával. A Azure Machine Learning munkaterület egyaránt használ különböző állomásnévket. A következő fejezetei a Azure Machine Learningához szükséges gazdagépeket sorolja fel.

### <a name="microsoft-hosts"></a>Microsoft-gazdagépek

Az ebben a szakaszban található gazdagépek a Microsoft tulajdonában vannak, és biztosítják a munkaterület megfelelő működéséhez szükséges szolgáltatásokat. Az alábbi táblázatok felsorolják az Azure Public, Azure Government és az Azure China 21Vianet-régiók állomásneveit.

**Általános Azure-gazdagépek**

| **Kötelező ehhez:** | **Azure – nyilvános** | **Azure Government** | **Azure China 21Vianet** |
| ----- | ----- | ----- | ----- |
| Azure Active Directory | login.microsoftonline.com | login.microsoftonline.us | login.chinacloudapi.cn |
| Azure Portal | management.azure.com | management.azure.us | management.azure.cn |
| Azure Resource Manager | management.azure.com | management.usgovcloudapi.net | management.chinacloudapi.cn |

**Gazdagépek Azure Machine Learning**

| **Kötelező ehhez:** | **Azure – nyilvános** | **Azure Government** | **Azure China 21Vianet** |
| ----- | ----- | ----- | ----- |
| Azure Machine Learning Studio | ml.azure.com | ml.azure.us | studio.ml.azure.cn |
| API |\*. azureml.ms | \*. ml.azure.us | \*. ml.azure.cn |
| Kísérletezés, előzmények, HyperDrive, címkézés | \*. experiments.azureml.net | \*. ml.azure.us | \*. ml.azure.cn |
| Modellkezelés | \*. modelmanagement.azureml.net | \*. ml.azure.us | \*. ml.azure.cn |
| Folyamat | \*. aether.ms | \*. ml.azure.us | \*. ml.azure.cn |
| Designer (Studio szolgáltatás) | \*. studioservice.azureml.com | \*. ml.azure.us | \*. ml.azure.cn |
| Integrált jegyzetfüzet | \*. notebooks.azure.net | \*. notebooks.usgovcloudapi.net |\*. notebooks.chinacloudapi.cn |
| Integrált jegyzetfüzet | \*. file.core.windows.net | \*. file.core.usgovcloudapi.net | \*. file.core.chinacloudapi.cn |
| Integrált jegyzetfüzet | \*. dfs.core.windows.net | \*. dfs.core.usgovcloudapi.net | \*. dfs.core.chinacloudapi.cn |
| Integrált jegyzetfüzet | \*.blob.core.windows.net | \*. blob.core.usgovcloudapi.net | \*. blob.core.chinacloudapi.cn |
| Integrált jegyzetfüzet | graph.microsoft.com | graph.microsoft.us | graph.chinacloudapi.cn |
| Integrált jegyzetfüzet | \*. aznbcontent.net |  | |

**Azure Machine Learning számítási példány és számítási fürt gazdagépei**

| **Kötelező ehhez:** | **Azure – nyilvános** | **Azure Government** | **Azure China 21Vianet** |
| ----- | ----- | ----- | ----- |
| Számítási fürt/példány | \*. batchai.core.windows.net | \*. batchai.core.usgovcloudapi.net |\*. batchai.ml.azure.cn |
| Számítási fürt/példány | graph.windows.net | graph.windows.net | graph.chinacloudapi.cn |
| Számítási példány | \*. instances.azureml.net | \*. instances.azureml.us | \*. instances.azureml.cn |
| Számítási példány | \*. instances.azureml.ms |  |  |

**Az Azure Machine Learning által használt társított erőforrások**

| **Kötelező ehhez:** | **Azure – nyilvános** | **Azure Government** | **Azure China 21Vianet** |
| ----- | ----- | ----- | ----- |
| Azure Storage-tárfiók neve | core.windows.net | core.usgovcloudapi.net | core.chinacloudapi.cn |
| Azure Key Vault | vault.azure.net | vault.usgovcloudapi.net | vault.azure.cn |
| Azure Container Registry | azurecr.io | azurecr.us | azurecr.cn |
| Microsoft Container Registry | mcr.microsoft.com | mcr.microsoft.com | mcr.microsoft.com |


> [!TIP]
> Ha összevont identitást szeretne használni, kövesse az [ajánlott eljárásokat Active Directory összevonási szolgáltatások (AD FS) cikk biztonságossá tételéhez](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs) .

Továbbá a [kényszerített Bújtatásban](how-to-secure-training-vnet.md#forced-tunneling) található információk használatával adja hozzá a és a IP-címeit `BatchNodeManagement` `AzureMachineLearning` .

További információ az Azure Kubernetes szolgáltatásban (ak) üzembe helyezett modellekhez való hozzáférés korlátozásáról: [a kimenő forgalom korlátozása az Azure Kubernetes szolgáltatásban](../aks/limit-egress-traffic.md).

### <a name="python-hosts"></a>Python-gazdagépek

Az ebben a szakaszban található gazdagépek a Python-csomagok telepítéséhez használatosak. A fejlesztés, a képzés és a telepítés során szükségesek. 

> [!NOTE]
> Ez nem teljes lista az interneten található összes Python-erőforráshoz szükséges gazdagépekről, csak a leggyakrabban használt. Ha például hozzá kell férnie egy GitHub-adattárhoz vagy egy másik gazdagéphez, meg kell adnia és hozzá kell adnia a szükséges gazdagépeket az adott forgatókönyvhöz.

| **Állomásnév** | **Cél** |
| ---- | ---- |
| **anaconda.com**</br>**\*. anaconda.com** | Az alapértelmezett csomagok telepítéséhez használatos. |
| **\*. anaconda.org** | A tárház-adatgyűjtéshez használatos. |
| **pypi.org** | Az alapértelmezett indexből származó függőségek listázására használatos, és az indexet a felhasználói beállítások nem írják felül. Ha a rendszer felülírja az indexet, a **\* . pythonhosted.org** is engedélyeznie kell. |
| **\*pytorch.org** | A PyTorch alapján néhány példa használ. |
| **\*. tensorflow.org** | A Tensorflow alapján néhány példa használ. |

### <a name="r-hosts"></a>R-gazdagépek

A jelen szakaszban található gazdagépek az R-csomagok telepítéséhez használatosak. A fejlesztés, a képzés és a telepítés során szükségesek.

> [!NOTE]
> Ez a lista nem tartalmazza az interneten található összes R-erőforráshoz szükséges gazdagépek teljes listáját, csak a leggyakrabban használt. Ha például hozzá kell férnie egy GitHub-adattárhoz vagy egy másik gazdagéphez, meg kell adnia és hozzá kell adnia a szükséges gazdagépeket az adott forgatókönyvhöz.

| **Állomásnév** | **Cél** |
| ---- | ---- |
| **cloud.r-project.org** | A CRAN-csomagok telepítésekor használatos. |

> [!IMPORTANT]
> Belsőleg az R SDK for Azure Machine Learning Python-csomagokat használ. Így a Python-gazdagépeket is engedélyeznie kell a tűzfalon.
## <a name="next-steps"></a>Következő lépések

* [Oktatóanyag: Az Azure Firewall üzembe helyezése és konfigurálása az Azure Portalon](../firewall/tutorial-firewall-deploy-portal.md)
* [Biztonságos Azure ML-kísérletezés és következtetési feladatok egy Azure-beli virtuális hálózaton belül](how-to-network-security-overview.md)
