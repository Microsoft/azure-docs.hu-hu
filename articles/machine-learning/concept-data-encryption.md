---
title: Adattitkosítás az Azure Machine learning szolgáltatással
titleSuffix: Azure Machine Learning
description: Ismerje meg, hogyan biztosít a Azure Machine Learning a számítások és az adattárak adattitkosítást a nyugalmi és a továbbítási szolgáltatásokban.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jhirono
author: jhirono
ms.reviewer: larryfr
ms.date: 11/09/2020
ms.openlocfilehash: 13d5c02fbb4ae06c7a5279ab7c5d3af90c263f71
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2021
ms.locfileid: "102521067"
---
# <a name="data-encryption-with-azure-machine-learning"></a>Adattitkosítás Azure Machine Learning

Azure Machine Learning különböző Azure-adattárolási szolgáltatásokat és számítási erőforrásokat használ a modellek betanítása és a következtetések végrehajtása során. Ezek mindegyike saját történetet tartalmaz arról, hogy miként biztosítják a titkosítást a nyugalmi és a továbbítási adatok számára. Ebből a cikkből megtudhatja, melyik a legmegfelelőbb a forgatókönyvhöz.

> [!IMPORTANT]
> Az éles környezetben való titkosításhoz __a Microsoft__ Azure Machine learning számítási fürt használatát javasolja. A Microsoft az Azure Kubernetes Service használatát javasolja az éles környezetbeli adattitkosításhoz a __következtetések__ során.
>
> Azure Machine Learning számítási példány fejlesztési és tesztelési környezet. A használatakor azt javasoljuk, hogy a fájlokat, például jegyzetfüzeteket és parancsfájlokat egy fájlmegosztás tárolja. Az adatait adattárban kell tárolni.

## <a name="encryption-at-rest"></a>Titkosítás inaktív állapotban

> [!IMPORTANT]
> Ha a munkaterület bizalmas adatokat tartalmaz, javasoljuk, hogy a munkaterület létrehozásakor a [hbi_workspace jelzőt](/python/api/azureml-core/azureml.core.workspace%28class%29#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-) állítsa be. A `hbi_workspace` jelző csak akkor állítható be, ha létrehoznak egy munkaterületet. A meglévő munkaterületek esetében nem módosítható.

A `hbi_workspace` jelző szabályozza a Microsoft által [diagnosztikai célokra gyűjtött adatok](#microsoft-collected-data) mennyiségét, és lehetővé teszi a [további titkosítást a Microsoft által felügyelt környezetekben](../security/fundamentals/encryption-atrest.md). Emellett a következő műveleteket is lehetővé teszi:

* Megkezdi a helyi lemez titkosítását a Azure Machine Learning számítási fürtben, ha nem hozott létre egy korábbi fürtöt az előfizetésben. Más esetben egy támogatási jegyet kell létrehoznia, amely lehetővé teszi a számítási fürtökhöz tartozó kaparós lemez titkosítását 
* Kiüríti az ideiglenes lemezt a futtatások között
* Biztonságosan továbbítja a Storage-fiók, a tároló-beállításjegyzék és az SSH-fiók hitelesítő adatait a végrehajtási rétegből a kulcstartó használatával a számítási fürtökbe
* Engedélyezi az IP-szűrést annak érdekében, hogy a mögöttes batch-készletek ne legyenek meghívva a AzureMachineLearningService-től eltérő külső szolgáltatásokkal.
* Vegye figyelembe, hogy a számítási példányok nem támogatottak a HBI-munkaterületen

### <a name="azure-blob-storage"></a>Azure Blob Storage

A Azure Machine Learning a pillanatképeket, a kimeneteket és a naplókat a Azure Machine Learning munkaterülethez és az előfizetéséhez kötött Azure Blob Storage-fiókban tárolja. Az Azure Blob Storage-ban tárolt összes adatok titkosítva vannak a Microsoft által felügyelt kulcsokkal.

Az Azure Blob Storage-ban tárolt adatok saját kulcsaival kapcsolatos információkért lásd: az [Azure Storage titkosítása az ügyfél által felügyelt kulcsokkal Azure Key Vault](../storage/common/customer-managed-keys-configure-key-vault.md).

A betanítási adatok általában az Azure Blob Storage-ban is tárolódnak, hogy elérhetők legyenek a számítási célok betanításához. Ezt a tárolót nem Azure Machine Learning felügyeli, de távoli fájlrendszerként van csatlakoztatva a számítási célokhoz.

Ha el kell __forgatnia vagy vissza kell vonnia__ a kulcsot, bármikor megteheti. A kulcsok elforgatásakor a Storage-fiók az új kulccsal (legújabb verzióval) kezdi meg az adatok titkosítását a nyugalmi állapotban. A kulcsok visszavonása (letiltása) esetén a Storage-fiók gondoskodik a sikertelen kérelmekről. Ez általában egy órát vesz igénybe a rotáció vagy a visszavonás érvénybe léptetéséhez.

A hozzáférési kulcsok újragenerálásával kapcsolatos információkért lásd: [tároló-hozzáférési kulcsok](how-to-change-storage-access-key.md)újragenerálása.

### <a name="azure-cosmos-db"></a>Azure Cosmos DB

Azure Machine Learning a metaadatokat egy Azure Cosmos DB példányban tárolja. Ez a példány egy Azure Machine Learning által felügyelt Microsoft-előfizetéshez van társítva. A Azure Cosmos DB tárolt összes adatok titkosítva vannak a Microsoft által felügyelt kulcsokkal.

Ha saját (ügyfél által felügyelt) kulcsokat kíván használni a Azure Cosmos DB-példány titkosításához, létrehozhat egy dedikált Cosmos DB-példányt a munkaterülethez való használatra. Ezt a megközelítést javasoljuk, ha a Microsoft-előfizetésben üzemeltetett több-bérlős Cosmos DB példányon kívül szeretné tárolni az adatokat, például a futtatási előzmények információit. 

Az ügyfél által felügyelt kulcsokkal rendelkező Cosmos DB példány kiépítés engedélyezéséhez hajtsa végre a következő műveleteket:

* Regisztrálja a Microsoft. MachineLearning és a Microsoft.DocumentDB erőforrás-szolgáltatót az előfizetésében, ha még nem tette meg.

* A Azure Machine Learning munkaterület létrehozásakor használja a következő paramétereket. Mindkét paraméter kötelező és támogatott az SDK-ban, a CLI-ben, a REST API-kon és a Resource Manager-sablonokban.

    * `resource_cmk_uri`: Ez a paraméter a Key vaultban lévő ügyfél által felügyelt kulcs teljes erőforrás-URI-ja, beleértve a [kulcs verziószámát](../key-vault/general/about-keys-secrets-certificates.md#objects-identifiers-and-versioning)is. 

    * `cmk_keyvault`: Ez a paraméter az előfizetésében található kulcstartó erőforrás-azonosítója. Ennek a kulcstartónak ugyanabban a régióban és előfizetésben kell lennie, amelyet az Azure Machine Learning munkaterülethez fog használni. 
    
        > [!NOTE]
        > Ez a Key Vault-példány különbözhet a munkaterület kiépítésekor Azure Machine Learning által létrehozott kulcstartótól. Ha ugyanazt a Key Vault-példányt szeretné használni a munkaterülethez, a [key_vault paraméter](/python/api/azureml-core/azureml.core.workspace%28class%29#create-name--auth-none--subscription-id-none--resource-group-none--location-none--create-resource-group-true--sku--basic---friendly-name-none--storage-account-none--key-vault-none--app-insights-none--container-registry-none--cmk-keyvault-none--resource-cmk-uri-none--hbi-workspace-false--default-cpu-compute-target-none--default-gpu-compute-target-none--exist-ok-false--show-output-true-)használatával adja meg ugyanazt a kulcstartót, miközben a munkaterületet kiépíti. 

[!INCLUDE [machine-learning-customer-managed-keys.md](../../includes/machine-learning-customer-managed-keys.md)]

Ha el kell __forgatnia vagy vissza kell vonnia__ a kulcsot, bármikor megteheti. A kulcsok elforgatásakor Cosmos DB megkezdi az új kulcs (legújabb verzió) használatát az inaktív adatok titkosításához. Kulcs visszavonása (letiltása) esetén Cosmos DB a sikertelen kérelmeket. Ez általában egy órát vesz igénybe a rotáció vagy a visszavonás érvénybe léptetéséhez.

Az ügyfél által felügyelt Cosmos DB kulcsokkal kapcsolatos további információkért lásd: az [ügyfél által felügyelt kulcsok konfigurálása a Azure Cosmos db-fiókhoz](../cosmos-db/how-to-setup-cmk.md).

### <a name="azure-container-registry"></a>Azure Container Registry

A beállításjegyzékben található összes tároló lemezkép (Azure Container Registry) inaktív állapotban van. Az Azure automatikusan titkosítja a képeket a tárolás előtt, és visszafejti azt, amikor Azure Machine Learning lekéri a rendszerképet.

Ha saját (felhasználó által felügyelt) kulcsokat kíván használni a Azure Container Registry titkosításához, létre kell hoznia a saját ACR-t, és csatolnia kell azt a munkaterület kiépítés során, vagy titkosítania kell az alapértelmezett példányt, amelyet a rendszer a munkaterület üzembe helyezésének időpontjában hoz létre.

> [!IMPORTANT]
> A Azure Machine Learning megköveteli, hogy a rendszergazdai fiók engedélyezve legyen a Azure Container Registry. Alapértelmezés szerint ez a beállítás le van tiltva a tároló-beállításjegyzék létrehozásakor. A rendszergazdai fiók engedélyezésével kapcsolatos információkért lásd: [rendszergazdai fiók](../container-registry/container-registry-authentication.md#admin-account).
>
> Ha egy munkaterülethez Azure Container Registry lett létrehozva, ne törölje. Ezzel megszakítja Azure Machine Learning munkaterületét.

A munkaterületek meglévő Azure Container Registry használatával történő létrehozásának példáját a következő cikkekben találja:

* [Hozzon létre egy munkaterületet Azure Machine learning az Azure CLI-vel](how-to-manage-workspace-cli.md).
* [Hozzon létre egy munkaterületet a PYTHON SDK-val](how-to-manage-workspace.md?tabs=python#create-a-workspace).
* [Munkaterületek létrehozása Azure Machine Learninghez Azure Resource Manager sablon használatával](how-to-create-workspace-template.md)

### <a name="azure-container-instance"></a>Azure Container Instance

Az üzembe helyezett Azure Container instance-(ACI-) erőforrások titkosítása az ügyfél által felügyelt kulcsokkal végezhető el. Az ACI-hoz használt ügyfél által felügyelt kulcs a munkaterület Azure Key Vault tárolható. A kulcsok létrehozásával kapcsolatos információkért lásd: [adatok titkosítása ügyfél által felügyelt kulccsal](../container-instances/container-instances-encrypt-data.md#generate-a-new-key).

Ahhoz, hogy a kulcsot a modell Azure Container-példányra történő telepítésekor használja, hozzon létre egy új központi telepítési konfigurációt a használatával `AciWebservice.deploy_configuration()` . Adja meg a kulcs adatait a következő paraméterekkel:

* `cmk_vault_base_url`: A kulcsot tartalmazó kulcstartó URL-címe.
* `cmk_key_name`: A kulcs neve.
* `cmk_key_version`: A kulcs verziószáma.

A központi telepítési konfiguráció létrehozásával és használatával kapcsolatos további információkért tekintse meg a következő cikkeket:

* [AciWebservice.deploy_configuration ()](/python/api/azureml-core/azureml.core.webservice.aci.aciwebservice#deploy-configuration-cpu-cores-none--memory-gb-none--tags-none--properties-none--description-none--location-none--auth-enabled-none--ssl-enabled-none--enable-app-insights-none--ssl-cert-pem-file-none--ssl-key-pem-file-none--ssl-cname-none--dns-name-label-none--primary-key-none--secondary-key-none--collect-model-data-none--cmk-vault-base-url-none--cmk-key-name-none--cmk-key-version-none-) hivatkozás
* [Az üzembe helyezés módja és helye](how-to-deploy-and-where.md)
* [Modell üzembe helyezése az Azure Container Instances szolgáltatásban](how-to-deploy-azure-container-instance.md)

Az ACI-val rendelkező, ügyfél által felügyelt kulcs használatával kapcsolatos további információkért lásd: [adatok titkosítása ügyfél által felügyelt kulccsal](../container-instances/container-instances-encrypt-data.md#encrypt-data-with-a-customer-managed-key).

### <a name="azure-kubernetes-service"></a>Azure Kubernetes Service

Az üzembe helyezett Azure Kubernetes szolgáltatásbeli erőforrásokat bármikor titkosíthatja az ügyfél által felügyelt kulcsokkal. További információ: [saját kulcsok használata az Azure Kubernetes szolgáltatással](../aks/azure-disk-customer-managed-keys.md). 

Ez a folyamat lehetővé teszi a Kubernetes-fürtben lévő telepített virtuális gépek és az operációs rendszer lemezének titkosítását.

> [!IMPORTANT]
> Ez a folyamat csak az K8s 1,17-es vagy újabb verziójával működik. Azure Machine Learning a 1,17-es, Jan. január 13-án a 2020-os támogatást adta hozzá.

### <a name="machine-learning-compute"></a>Machine Learning Compute

Az Azure Storage-ban tárolt összes számítási csomópont operációsrendszer-lemeze titkosítva van a Microsoft által felügyelt kulcsokkal Azure Machine Learning Storage-fiókokban. Ez a számítási cél elmúló, és a fürtöket jellemzően akkor kell lekicsinyíteni, ha nincsenek várólistán lévő futtatások. A mögöttes virtuális gép kiépítve, az operációsrendszer-lemez pedig törlődik. Az operációsrendszer-lemez nem támogatja a Azure Disk Encryption.

Minden virtuális gép helyi ideiglenes lemezzel is rendelkezik az operációs rendszer műveleteihez. Ha szeretné, használhatja a lemezt a betanítási adatgyűjtéshez. Alapértelmezés szerint a lemez titkosítva van a (z `hbi_workspace` ) paraméterrel beállított munkaterületek esetében `TRUE` . Ez a környezet csak a Futtatás időtartamára vonatkozik, és a titkosítási támogatás csak a rendszer által felügyelt kulcsokra korlátozódik.

### <a name="azure-databricks"></a>Azure Databricks

Azure Databricks használható Azure Machine Learning folyamatokban. Alapértelmezés szerint a Azure Databricks által használt Databricks fájlrendszer (DBFS) Microsoft által felügyelt kulccsal van titkosítva. Az ügyfél által felügyelt kulcsok használatára Azure Databricks konfigurálását lásd: az [ügyfél által felügyelt kulcsok konfigurálása az alapértelmezett (root) DBFS](/azure/databricks/security/customer-managed-keys-dbfs).

### <a name="microsoft-generated-data"></a>Microsoft által generált adatszolgáltatások

Ha olyan szolgáltatásokat használ, mint például az automatizált Machine Learning, a Microsoft átmeneti, előre feldolgozott adatfeldolgozást eredményezhet több modell betanításához. Ezeket az adatait a munkaterület egy adattárában tárolja, amely lehetővé teszi a hozzáférés-vezérlés és a titkosítás megfelelő betartatását.

Érdemes lehet titkosítani az [üzembe helyezett végpontból naplózott diagnosztikai adatokat](how-to-enable-app-insights.md) az Azure Application Insights-példányba.

## <a name="encryption-in-transit"></a>Titkosítás az átvitel során

A Azure Machine Learning a TLS-t használja a különböző Azure Machine Learning-szolgáltatások közötti belső kommunikáció biztonságossá tételéhez. Az Azure Storage összes hozzáférése egy biztonságos csatornán is megtörténik.

A pontozási végponton végzett külső hívások biztonságossá tételéhez Azure Machine Learning a TLS protokollt használja. További információkért lásd: [webszolgáltatások biztonságossá tétele a TLS használatával Azure Machine Learningon keresztül](./how-to-secure-web-service.md).

## <a name="data-collection-and-handling"></a>Adatgyűjtés és-kezelés

### <a name="microsoft-collected-data"></a>Microsoft összegyűjtött adatok

A Microsoft a nem felhasználótól származó azonosító adatokat (például az adathalmaz nevét vagy a Machine learning-kísérlet nevét) vagy a munkahelyi környezeti változókat diagnosztikai célokra gyűjtheti. Az összes ilyen adatokat a Microsoft által felügyelt kulcsok tárolják a Microsoft tulajdonában lévő előfizetésekben üzemeltetett tárolókban, és a [Microsoft szabványos adatvédelmi szabályzatát és adatkezelési szabványait](https://privacy.microsoft.com/privacystatement)követi.

A Microsoft azt is javasolja, hogy ne tárolja a bizalmas adatokat (például a fiók kulcsának titkos adatait) a környezeti változókban. A környezeti változók naplózása, titkosítása és tárolása az USA-ban történik. Hasonlóképpen a [run_id](/python/api/azureml-core/azureml.core.run%28class%29)elnevezése esetén Kerülje a bizalmas adatokat, például a felhasználóneveket vagy a titkos projektek nevét. Ezek az információk megjelenhetnek Microsoft ügyfélszolgálata mérnökök számára elérhető telemetria-naplókban.

A gyűjtött diagnosztikai adatok közül kiválaszthatja, `hbi_workspace` hogy a paramétert úgy állítja be, hogy `TRUE` kiépítse a munkaterületet. Ez a funkció a AzureML Python SDK, a CLI, a REST API-k vagy a Azure Resource Manager-sablonok használata esetén támogatott.

## <a name="using-azure-key-vault"></a>Azure Key Vault használata

Azure Machine Learning a munkaterülethez társított Azure Key Vault-példányt használja a különböző típusú hitelesítő adatok tárolásához:

* A társított Storage-fiókhoz tartozó kapcsolatok karakterlánca
* Az Azure Container repository példányainak jelszavai
* Adattárakhoz való kapcsolódási karakterláncok

Az SSH-jelszavak és-kulcsok a számítási célokhoz, például az Azure HDInsight és a virtuális gépekhez a Microsoft-előfizetéshez társított külön kulcstartóban tárolódnak. Azure Machine Learning nem tárolja a felhasználók által biztosított jelszavakat vagy kulcsokat. Ehelyett saját SSH-kulcsokat hoz létre, engedélyez és tárol a virtuális gépekhez és HDInsight való kapcsolódáshoz a kísérletek futtatásához.

Minden munkaterülethez tartozik egy társított, rendszerhez rendelt felügyelt identitás, amelynek a neve megegyezik a munkaterülettel. Ez a felügyelt identitás hozzáfér a kulcstartóban található összes kulcshoz, titokhoz és tanúsítványhoz.

## <a name="next-steps"></a>Következő lépések

* [Csatlakozás az Azure Storage-hoz](how-to-access-data.md)
* [Adatok lekérése adattárból](how-to-create-register-datasets.md)
* [Csatlakozás adatokhoz](how-to-connect-data-ui.md)
* [Betanítás adathalmazok használatával](how-to-train-with-datasets.md)