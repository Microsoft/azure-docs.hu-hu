---
title: A Azure Data Factory hibáinak megoldása | Microsoft Docs
description: Megtudhatja, hogyan lehet elhárítani a Azure Data Factory külső vezérlési tevékenységeit.
author: nabhishek
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 12/30/2020
ms.author: abnarain
ms.openlocfilehash: 101e55188b8021040e2fd6bd573c1c6330241e72
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2021
ms.locfileid: "100382803"
---
# <a name="troubleshoot-azure-data-factory"></a>Az Azure Data Factory hibaelhárítása

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Ez a cikk a Azure Data Factory külső ellenőrzési tevékenységeinek gyakori hibaelhárítási módszereit vizsgálja.

## <a name="connector-and-copy-activity"></a>Összekötő és másolási tevékenység

A másolási tevékenységgel kapcsolatos kapcsolódási hibák, például a következő témakörben talál további információt: [Azure Data Factory összekötők hibaelhárítása](connector-troubleshoot-guide.md).

## <a name="azure-databricks"></a>Azure Databricks

### <a name="error-code-3200"></a>Hibakód: 3200

- **Üzenet**: 403-es hiba.

- **OK**: `The Databricks access token has expired.`

- **Javaslat**: alapértelmezés szerint a Azure Databricks hozzáférési jogkivonat 90 napig érvényes. Hozzon létre egy új jogkivonatot, és frissítse a társított szolgáltatást.

### <a name="error-code-3201"></a>Hibakód: 3201

- **Üzenet**: `Missing required field: settings.task.notebook_task.notebook_path.`

- **OK**: `Bad authoring: Notebook path not specified correctly.`

- **Javaslat**: a jegyzetfüzet elérési útjának megadása a Databricks tevékenységben.

<br/> 

- **Üzenet**: `Cluster... does not exist.`

- **OK**: `Authoring error: Databricks cluster does not exist or has been deleted.`

- **Javaslat**: Ellenőrizze, hogy létezik-e a Databricks-fürt.

<br/> 

- **Üzenet**: `Invalid Python file URI... Please visit Databricks user guide for supported URI schemes.`

- **OK**: `Bad authoring.`

- **Javaslat**: adjon meg abszolút elérési utat a munkaterület-címzési sémák vagy `dbfs:/folder/subfolder/foo.py` a Databricks fájlrendszerben (DFS) tárolt fájlokhoz.

<br/> 

- **Üzenet**: `{0} LinkedService should have domain and accessToken as required properties.`

- **OK**: `Bad authoring.`

- **Javaslat**: Ellenőrizze a [társított szolgáltatás definícióját](compute-linked-services.md#azure-databricks-linked-service).

<br/> 

- **Üzenet**: `{0} LinkedService should specify either existing cluster ID or new cluster information for creation.`

- **OK**: `Bad authoring.`

- **Javaslat**: Ellenőrizze a [társított szolgáltatás definícióját](compute-linked-services.md#azure-databricks-linked-service).

<br/> 

- **Üzenet**: `Node type Standard_D16S_v3 is not supported. Supported node types: Standard_DS3_v2, Standard_DS4_v2, Standard_DS5_v2, Standard_D8s_v3, Standard_D16s_v3, Standard_D32s_v3, Standard_D64s_v3, Standard_D3_v2, Standard_D8_v3, Standard_D16_v3, Standard_D32_v3, Standard_D64_v3, Standard_D12_v2, Standard_D13_v2, Standard_D14_v2, Standard_D15_v2, Standard_DS12_v2, Standard_DS13_v2, Standard_DS14_v2, Standard_DS15_v2, Standard_E8s_v3, Standard_E16s_v3, Standard_E32s_v3, Standard_E64s_v3, Standard_L4s, Standard_L8s, Standard_L16s, Standard_L32s, Standard_F4s, Standard_F8s, Standard_F16s, Standard_H16, Standard_F4s_v2, Standard_F8s_v2, Standard_F16s_v2, Standard_F32s_v2, Standard_F64s_v2, Standard_F72s_v2, Standard_NC12, Standard_NC24, Standard_NC6s_v3, Standard_NC12s_v3, Standard_NC24s_v3, Standard_L8s_v2, Standard_L16s_v2, Standard_L32s_v2, Standard_L64s_v2, Standard_L80s_v2.`

- **OK**: `Bad authoring.`

- **Javaslat**: Tekintse meg a hibaüzenetet.

<br/>

### <a name="error-code-3202"></a>Hibakód: 3202

- **Üzenet**: `There were already 1000 jobs created in past 3600 seconds, exceeding rate limit: 1000 job creations per 3600 seconds.`

- **OK**: `Too many Databricks runs in an hour.`

- **Javaslat**: a Databricks-munkaterületet használó összes folyamat kiértékelése a feladatok létrehozási arányára. Ha a folyamatok túl sok Databricks-t futtatnak összesítésben, telepítsen át bizonyos folyamatokat egy új munkaterületre.

<br/> 

- **Üzenet**: `Could not parse request object: Expected 'key' and 'value' to be set for JSON map field base_parameters, got 'key: "..."' instead.`

- **OK**: `Authoring error: No value provided for the parameter.`

- **Javaslat**: vizsgálja meg a folyamat JSON-fájlját, és győződjön meg arról, hogy a baseParameters-jegyzetfüzet összes paramétere nem üres értéket ad meg.

<br/> 

- **Üzenet**: `User: ` SimpleUserContext {userId =..., név = user@company.com , orgId =...}` is not authorized to access cluster.`

- **OK**: a hozzáférési tokent létrehozó felhasználó nem férhet hozzá a társított szolgáltatásban megadott Databricks-fürthöz.

- **Javaslat**: Ellenőrizze, hogy a felhasználó rendelkezik-e a szükséges engedélyekkel a munkaterületen.

### <a name="error-code-3203"></a>Hibakód: 3203

- **Üzenet**: `The cluster is in Terminated state, not available to receive jobs. Please fix the cluster or retry later.`

- **OK**: a fürt meg lett szakítva. Az interaktív fürtök esetében ez a probléma lehet a verseny feltétele.

- **Javaslat**: a hiba elkerüléséhez használja a feladatok fürtöket.

### <a name="error-code-3204"></a>Hibakód: 3204

- **Üzenet**: `Job execution failed.`

- **OK**: a hibaüzenetek különböző problémákat jeleznek, például egy váratlan fürt állapotát vagy egy adott tevékenységet. Gyakran nem jelenik meg hibaüzenet.

- **Javaslat**: N/A

### <a name="error-code-3208"></a>Hibakód: 3208

- **Üzenet**: `An error occurred while sending the request.`

- **OK**: a Databricks szolgáltatáshoz való hálózati kapcsolódás megszakadt.

- **Javaslat**: Ha saját üzemeltetésű integrációs modult használ, győződjön meg arról, hogy a hálózati kapcsolatok megbízhatók az Integration Runtime csomópontjain. Ha Azure Integration Runtime-t használ, az Újrapróbálkozás általában működik.
 
## <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics

A következő táblázat az U-SQL-re vonatkozik.
 
### <a name="error-code-2709"></a>Hibakód: 2709

- **Üzenet**: `The access token is from the wrong tenant.`

- **OK**: helytelen Azure Active Directory (Azure ad) bérlő.

- **Javaslat**: helytelen Azure Active Directory (Azure ad) bérlő.

<br/>

- **Üzenet**: `We cannot accept your job at this moment. The maximum number of queued jobs for your account is 200. `

- **OK**: ezt a hibát a Data Lake Analytics szabályozása okozza.

- **Javaslat**: csökkentse a beküldött feladatok számát Data Lake Analyticsra. Módosítsa Data Factory eseményindítók és a Egyidejűség beállításait a tevékenységeken, vagy növelje a korlátozásokat a Data Lake Analytics.

<br/> 

- **Üzenet**: `This job was rejected because it requires 24 AUs. This account's administrator-defined policy prevents a job from using more than 5 AUs.`

- **OK**: ezt a hibát a Data Lake Analytics szabályozása okozza.

- **Javaslat**: csökkentse a beküldött feladatok számát Data Lake Analyticsra. Módosítsa Data Factory eseményindítók és a Egyidejűség beállításait a tevékenységeken, vagy növelje a korlátozásokat a Data Lake Analytics.

### <a name="error-code-2705"></a>Hibakód: 2705

- **Üzenet**: `Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/> <br/> User is not able to access Data Lake Store. <br/> <br/> User is not authorized to use Data Lake Analytics.`

- **OK**: az egyszerű szolgáltatásnév vagy tanúsítvány nem fér hozzá a fájlhoz a tárolóban.

- **Javaslat**: Ellenőrizze, hogy a felhasználó által Data Lake Analytics feladatokhoz megadott szolgáltatásnév vagy tanúsítvány rendelkezik-e hozzáféréssel mind a Data Lake Analytics fiókhoz, mind a gyökérmappa alapértelmezett Data Lake Storage példányához.

### <a name="error-code-2711"></a>Hibakód: 2711

- **Üzenet**: `Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/> <br/> User is not able to access Data Lake Store. <br/> <br/> User is not authorized to use Data Lake Analytics.`

- **OK**: az egyszerű szolgáltatásnév vagy tanúsítvány nem fér hozzá a fájlhoz a tárolóban.

- **Javaslat**: Ellenőrizze, hogy a felhasználó által Data Lake Analytics feladatokhoz megadott szolgáltatásnév vagy tanúsítvány rendelkezik-e hozzáféréssel mind a Data Lake Analytics fiókhoz, mind a gyökérmappa alapértelmezett Data Lake Storage példányához.

<br/> 

- **Üzenet**: `Cannot find the 'Azure Data Lake Store' file or folder.`

- **OK**: az U-SQL-fájl elérési útja helytelen, vagy a társított szolgáltatás hitelesítő adatai nem rendelkeznek hozzáféréssel.

- **Javaslat**: Ellenőrizze a társított szolgáltatásban megadott elérési utat és a hitelesítő adatokat.

### <a name="error-code-2704"></a>Hibakód: 2704

- **Üzenet**: `Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/> <br/> User is not able to access Data Lake Store. <br/> <br/> User is not authorized to use Data Lake Analytics.`

- **OK**: az egyszerű szolgáltatásnév vagy tanúsítvány nem fér hozzá a fájlhoz a tárolóban.

- **Javaslat**: Ellenőrizze, hogy a felhasználó által Data Lake Analytics feladatokhoz megadott szolgáltatásnév vagy tanúsítvány rendelkezik-e hozzáféréssel mind a Data Lake Analytics fiókhoz, mind a gyökérmappa alapértelmezett Data Lake Storage példányához.

### <a name="error-code-2707"></a>Hibakód: 2707

- **Üzenet**: `Cannot resolve the account of AzureDataLakeAnalytics. Please check 'AccountName' and 'DataLakeAnalyticsUri'.`

- **OK**: a társított szolgáltatás Data Lake Analytics fiókja nem megfelelő.

- **Javaslat**: Ellenőrizze, hogy a megfelelő fiók van-e megadva.

### <a name="error-code-2703"></a>Hibakód: 2703

- **Üzenet**: `Error Id: E_CQO_SYSTEM_INTERNAL_ERROR (or any error that starts with "Error Id:").`

- **OK**: a hiba Data Lake Analytics.

- **Javaslat**: a feladatot elküldték Data Lake Analyticsba, és a parancsfájl is sikertelen volt. Data Lake Analytics vizsgálata. A portálon lépjen a Data Lake Analytics fiókra, és keresse meg a feladatot a Data Factory tevékenység futtatási AZONOSÍTÓjának használatával (ne használja a folyamat futtatási AZONOSÍTÓját). A feladattal kapcsolatban további információkat talál a hibáról, és segítséget nyújt a hibák megoldásához.

   Ha a megoldás nem egyértelmű, lépjen kapcsolatba a Data Lake Analytics támogatási csapatával, és adja meg a feladatokhoz tartozó univerzális erőforrás-keresőt (URL-címet), amely tartalmazza a fiók nevét és a feladattípust.
 
## <a name="azure-functions"></a>Azure-függvények

### <a name="error-code-3602"></a>Hibakód: 3602

- **Üzenet**: `Invalid HttpMethod: '%method;'.`

- **OK**: az Azure Function tevékenység nem támogatja a tevékenység hasznos HttpMethod megadott értéket.

- **Javaslat**: a támogatott Httpmethods a következők: Put, post, Get, DELETE, Options, Head és trace.

### <a name="error-code-3603"></a>Hibakód: 3603

- **Üzenet**: `Response Content is not a valid JObject.`

- **OK**: a hívott Azure-függvény nem adott vissza JSON-adattartalmat a válaszban. Azure Data Factory (ADF) az Azure Function tevékenység csak a JSON-válaszok tartalmát támogatja.

- **Javaslat**: frissítse az Azure-függvényt egy érvényes JSON-adattartalom, például egy C#-függvény visszaküldéséhez `(ActionResult)new OkObjectResult("{\"Id\":\"123\"}");`

### <a name="error-code-3606"></a>Hibakód: 3606

- **Üzenet**: az Azure Function tevékenysége hiányzik a funkcióbillentyű.

- **OK**: az Azure Function tevékenység definíciója nem fejeződött be.

- **Javaslat**: Győződjön meg arról, hogy a bemeneti Azure Function tevékenység JSON-definíciója tartalmaz egy nevű tulajdonságot `functionKey` .

### <a name="error-code-3607"></a>Hibakód: 3607

- **Üzenet**: `Azure function activity missing function name.`

- **OK**: az Azure Function tevékenység definíciója nem fejeződött be.

- **Javaslat**: Győződjön meg arról, hogy a bemeneti Azure Function tevékenység JSON-definíciója tartalmaz egy nevű tulajdonságot `functionName` .

### <a name="error-code-3608"></a>Hibakód: 3608

- **Üzenet**: `Call to provided Azure function '%FunctionName;' failed with status-'%statusCode;' and message - '%message;'.`

- **OK**: az Azure-függvény részletei a tevékenység definíciójában helytelenek lehetnek.

- **Javaslat**: javítsa ki az Azure-függvény részleteit, és próbálkozzon újra.

### <a name="error-code-3609"></a>Hibakód: 3609

- **Üzenet**: `Azure function activity missing functionAppUrl.`

- **OK**: az Azure Function tevékenység definíciója nem fejeződött be.

- **Javaslat**: Győződjön meg arról, hogy a bemeneti Azure Function tevékenység JSON-definíciója tartalmaz egy nevű tulajdonságot `functionAppUrl` .

### <a name="error-code-3610"></a>Hibakód: 3610

- **Üzenet**: `There was an error while calling endpoint.`

- **OK**: lehet, hogy a függvény URL-címe helytelen.

- **Javaslat**: Ellenőrizze, hogy a `functionAppUrl` tevékenység JSON-fájljának értéke helyes-e, majd próbálkozzon újra.

### <a name="error-code-3611"></a>Hibakód: 3611

- **Üzenet**: `Azure function activity missing Method in JSON.`

- **OK**: az Azure Function tevékenység definíciója nem fejeződött be.

- **Javaslat**: Győződjön meg arról, hogy a bemeneti Azure Function tevékenység JSON-definíciója tartalmaz egy nevű tulajdonságot `method` .

### <a name="error-code-3612"></a>Hibakód: 3612

- **Üzenet**: `Azure function activity missing LinkedService definition in JSON.`

- **OK**: az Azure Function tevékenység definíciója nem fejeződött be.

- **Javaslat**: Győződjön meg róla, hogy a bemeneti Azure Function tevékenység JSON-definíciója társított szolgáltatás részleteit tartalmazta.

## <a name="azure-machine-learning"></a>Azure Machine Learning

### <a name="error-code-4101"></a>Hibakód: 4101

- **Üzenet**: `AzureMLExecutePipeline activity '%activityName;' has invalid value for property '%propertyName;'.`

- **OK**: a tulajdonság helytelen formátumú vagy hiányzó definíciója `%propertyName;` .

- **Javaslat**: Ellenőrizze, hogy a tevékenység rendelkezik-e `%activityName;` a `%propertyName;` megfelelő adatokkal megadott tulajdonsággal.

### <a name="error-code-4110"></a>Hibakód: 4110

- **Üzenet**: `AzureMLExecutePipeline activity missing LinkedService definition in JSON.`

- **OK**: a AzureMLExecutePipeline tevékenység definíciója nem fejeződött be.

- **Javaslat**: Győződjön meg róla, hogy a bemeneti AZUREMLEXECUTEPIPELINE tevékenység JSON-definíciója helyesen csatolta a szolgáltatáshoz tartozó adatokat.

### <a name="error-code-4111"></a>Hibakód: 4111

- **Üzenet**: `AzureMLExecutePipeline activity has wrong LinkedService type in JSON. Expected LinkedService type: '%expectedLinkedServiceType;', current LinkedService type: Expected LinkedService type: '%currentLinkedServiceType;'.`

- **OK**: helytelen tevékenység-definíció.

- **Javaslat**: Győződjön meg róla, hogy a bemeneti AZUREMLEXECUTEPIPELINE tevékenység JSON-definíciója helyesen csatolta a szolgáltatáshoz tartozó adatokat.

### <a name="error-code-4112"></a>Hibakód: 4112

- **Üzenet**: `AzureMLService linked service has invalid value for property '%propertyName;'.`

- **OK**: helytelen formátumú vagy hiányzó definíció a (z)% propertyName; tulajdonsághoz.

- **Javaslat**: Ellenőrizze, hogy a társított szolgáltatás rendelkezik-e a `%propertyName;` megfelelő adatokkal megadott tulajdonsággal.

### <a name="error-code-4121"></a>Hibakód: 4121

- **Üzenet**: `Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **OK**: a Azure Machine learning eléréséhez használt hitelesítő adat lejárt.

- **Javaslat**: Ellenőrizze, hogy a hitelesítő adatok érvényesek-e, majd próbálkozzon újra.

### <a name="error-code-4122"></a>Hibakód: 4122

- **Üzenet**: `Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **OK**: a Azure Machine learning társított szolgáltatásban megadott hitelesítő adat érvénytelen, vagy nem rendelkezik engedéllyel a művelethez.

- **Javaslat**: Ellenőrizze, hogy érvényes-e a hitelesítő adatok a társított szolgáltatásban, és hogy van-e hozzáférési engedélye Azure Machine Learninghoz.

### <a name="error-code-4123"></a>Hibakód: 4123

- **Üzenet**: `Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **OK**: a tevékenység tulajdonságai, például `pipelineParameters` a Azure Machine learning (ml) folyamat esetében érvénytelenek.

- **Javaslat**: Győződjön meg arról, hogy a tevékenység tulajdonságainak értéke megegyezik a társított szolgáltatásban megadott közzétett Azure ml-folyamat várt hasznos adattartalmával.

### <a name="error-code-4124"></a>Hibakód: 4124

- **Üzenet**: `Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **OK**: a közzétett Azure ml-folyamat végpontja nem létezik.

- **Javaslat**: Ellenőrizze, hogy a társított szolgáltatásban megadott közzétett Azure Machine learning folyamat-végpont létezik-e Azure Machine Learningban.

### <a name="error-code-4125"></a>Hibakód: 4125

- **Üzenet**: `Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **OK**: a Azure Machine learning kiszolgálóhiba történt.

- **Javaslat**: próbálkozzon újra később. Ha a probléma továbbra is fennáll, lépjen kapcsolatba a Azure Machine Learning csapatával.

### <a name="error-code-4126"></a>Hibakód: 4126

- **Üzenet**: `Azure ML pipeline run failed with status: '%amlPipelineRunStatus;'. Azure ML pipeline run Id: '%amlPipelineRunId;'. Please check in Azure Machine Learning for more error logs.`

- **OK**: az Azure ml-folyamat futtatása nem sikerült.

- **Javaslat**: jelölje be Azure Machine learning a további hibák naplóit, majd javítsa ki a ml-folyamatot.

## <a name="common"></a>Közös

### <a name="error-code-2103"></a>Hibakód: 2103

- **Üzenet**: `Please provide value for the required property '%propertyName;'.`

- **OK**: a tulajdonsághoz szükséges érték nincs megadva.

- **Javaslat**: adja meg az értéket az üzenetből, és próbálkozzon újra.

### <a name="error-code-2104"></a>Hibakód: 2104

- **Üzenet**: `The type of the property '%propertyName;' is incorrect.`

- **OK**: a megadott tulajdonság típusa nem megfelelő.

- **Javaslat**: javítsa ki a tulajdonság típusát, és próbálkozzon újra.

### <a name="error-code-2105"></a>Hibakód: 2105

- **Üzenet**: `An invalid json is provided for property '%propertyName;'. Encountered an error while trying to parse: '%message;'.`

- **OK**: a tulajdonság értéke érvénytelen vagy nem a várt formátumban van.

- **Javaslat**: Tekintse át a tulajdonság dokumentációját, és ellenőrizze, hogy a megadott érték tartalmazza-e a megfelelő formátumot és típust.

### <a name="error-code-2106"></a>Hibakód: 2106

- **Üzenet**: `The storage connection string is invalid. %errorMessage;`

- **OK**: a tárolóhoz tartozó kapcsolatok karakterlánca érvénytelen vagy helytelen formátumú.

- **Javaslat**: lépjen a Azure Portalra, keresse meg a tárhelyét, majd másolja és illessze be a kapcsolati karakterláncot a társított szolgáltatásba, majd próbálkozzon újra.

### <a name="error-code-2108"></a>Hibakód: 2108

- **Üzenet**: `Error calling the endpoint '%url;'. Response status code: '%code;'`

- **OK**: a kérelem végrehajtása egy mögöttes probléma miatt meghiúsult, például a hálózati kapcsolat, a DNS-hiba, a kiszolgálói tanúsítvány érvényesítése vagy az időtúllépés.

- **Javaslat**: a Hegedűs/Poster használatával ellenőrizze a kérést.

### <a name="error-code-2110"></a>Hibakód: 2110

- **Üzenet**: `The linked service type '%linkedServiceType;' is not supported for '%executorType;' activities.`

- **OK**: a tevékenységben megadott társított szolgáltatás helytelen.

- **Javaslat**: Ellenőrizze, hogy a társított szolgáltatás típusa a tevékenység által támogatott típusok egyike-e. A HDI tevékenységekhez tartozó társított szolgáltatástípus például HDInsight vagy HDInsightOnDemand lehet.

### <a name="error-code-2111"></a>Hibakód: 2111

- **Üzenet**: `The type of the property '%propertyName;' is incorrect. The expected type is %expectedType;.`

- **OK**: a megadott tulajdonság típusa nem megfelelő.

- **Javaslat**: javítsa ki a tulajdonság típusát, és próbálkozzon újra.

### <a name="error-code-2112"></a>Hibakód: 2112

- **Üzenet**: `The cloud type is unsupported or could not be determined for storage from the EndpointSuffix '%endpointSuffix;'.`

- **OK**: a felhő típusa nem támogatott, vagy nem határozható meg a tárolóhoz a EndpointSuffix.

- **Javaslat**: használjon egy másik Felhőbeli tárolót, és próbálkozzon újra.

### <a name="error-code-2128"></a>Hibakód: 2128

- **Üzenet**: `No response from the endpoint. Possible causes: network connectivity, DNS failure, server certificate validation or timeout.`

- **OK**: hálózati kapcsolat, DNS-hiba, kiszolgálói tanúsítvány ellenőrzése vagy időtúllépés.

- **Javaslat**: Ellenőrizze, hogy a elérni kívánt végpont válaszol-e a kérelmekre. Használhat olyan eszközöket is, mint a Hegedűs vagy a Poster.

## <a name="custom"></a>Egyéni

A következő táblázat a Azure Batchra vonatkozik.
 
### <a name="error-code-2500"></a>Hibakód: 2500

- **Üzenet**: `Hit unexpected exception and execution failed.`

- **OK**: `Can't launch command, or the program returned an error code.`

- **Javaslat**: Ellenőrizze, hogy létezik-e a végrehajtható fájl. Ha a program elindult, ellenőrizze, hogy a rendszer feltöltötte-e *stdout.txt* és *stderr.txt* a Storage-fiókba. Érdemes bemutatni a naplókat a kódban a hibakereséshez.

### <a name="error-code-2501"></a>Hibakód: 2501

- **Üzenet**: `Cannot access user batch account; please check batch account settings.`

- **OK**: helytelen a Batch-hozzáférési kulcs vagy a készlet neve.

- **Javaslat**: Ellenőrizze a készlet nevét és a Batch-elérési kulcsot a társított szolgáltatásban.

### <a name="error-code-2502"></a>Hibakód: 2502

- **Üzenet**: `Cannot access user storage account; please check storage account settings.`

- **OK**: helytelen a Storage-fiók neve vagy elérési kulcsa.

- **Javaslat**: Ellenőrizze a Storage-fiók nevét és a hozzáférési kulcsot a társított szolgáltatásban.

### <a name="error-code-2504"></a>Hibakód: 2504

- **Üzenet**: `Operation returned an invalid status code 'BadRequest'.`

- **OK**: túl sok fájl szerepel az `folderPath` Egyéni tevékenységben. A teljes méret `resourceFiles` nem lehet hosszabb 32 768 karakternél.

- **Javaslat**: távolítsa el a szükségtelen fájlokat, vagy zip-fájlt, és adjon hozzá egy unzip parancsot a kinyeréséhez.
   
   Használja például a következőt: `powershell.exe -nologo -noprofile -command "& { Add-Type -A 'System.IO.Compression.FileSystem'; [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ; $folder\yourProgram.exe`

### <a name="error-code-2505"></a>Hibakód: 2505

- **Üzenet**: `Cannot create Shared Access Signature unless Account Key credentials are used.`

- **OK**: az egyéni tevékenységek csak a hozzáférési kulcsot használó tárolási fiókokat támogatják.

- **Javaslat**: Tekintse meg a hiba leírását.

### <a name="error-code-2507"></a>Hibakód: 2507

- **Üzenet**: `The folder path does not exist or is empty: ...`

- **OK**: a megadott elérési úton egyetlen fájl sem található a Storage-fiókban.

- **Javaslat**: a mappa elérési útjának tartalmaznia kell a futtatni kívánt végrehajtható fájlokat.

### <a name="error-code-2508"></a>Hibakód: 2508

- **Üzenet**: `There are duplicate files in the resource folder.`

- **OK**: több azonos nevű fájl található a folderPath különböző almappáiban.

- **Javaslat**: az egyéni tevékenységek lelapulják a folderPath a mappa szerkezetét. Ha meg kell őriznie a mappa szerkezetét, zip-fájlt kell kibontania Azure Batch egy unzip paranccsal.
   
   Használja például a következőt: `powershell.exe -nologo -noprofile -command "& { Add-Type -A 'System.IO.Compression.FileSystem'; [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ; $folder\yourProgram.exe`

### <a name="error-code-2509"></a>Hibakód: 2509

- **Üzenet**: `Batch url ... is invalid; it must be in Uri format.`

- **OK**: a Batch URL-címeinek hasonlónak kell lenniük `https://mybatchaccount.eastus.batch.azure.com`

- **Javaslat**: Tekintse meg a hiba leírását.

### <a name="error-code-2510"></a>Hibakód: 2510

- **Üzenet**: `An error occurred while sending the request.`

- **OK**: a Batch URL-címe érvénytelen.

- **Javaslat**: Ellenőrizze a Batch URL-címét.
 
## <a name="hdinsight"></a>HDInsight

### <a name="error-code-206"></a>Hibakód: 206

- **Üzenet**: `The batch ID for Spark job is invalid. Please retry your job.`

- **OK**: a hibát okozó szolgáltatás belső hibába ütközött.

- **Javaslat**: Ez a probléma átmeneti lehet. Némi várakozás után próbálja megismételni a feladatot.

### <a name="error-code-207"></a>Hibakód: 207

- **Üzenet**: `Could not determine the region from the provided storage account. Please try using another primary storage account for the on demand HDI.`

- **OK**: belső hiba történt a régió elsődleges Storage-fiókból való megállapítására tett kísérlet során.

- **Javaslat**: próbálkozzon egy másik tárolóval. 

### <a name="error-code-208"></a>Hibakód: 208

- **Üzenet**: `Service Principal or the MSI authenticator are not instantiated. Please consider providing a Service Principal in the HDI on demand linked service which has permissions to create an HDInsight cluster in the provided subscription and try again.`

- **OK**: belső hiba történt a szolgáltatásnév beolvasására tett kísérlet során vagy az MSI-hitelesítés példányának létrehozásakor.

- **Javaslat**: érdemes megfontolni egy egyszerű szolgáltatásnév biztosítását, amely jogosult a HDInsight-fürt létrehozására a megadott előfizetésben, majd próbálkozzon újra. Ellenőrizze, hogy az [Identitások kezelése helyesen van](../hdinsight/hdinsight-managed-identities.md)-e beállítva.

### <a name="error-code-2300"></a>Hibakód: 2300

- **Üzenet**: `Failed to submit the job '%jobId;' to the cluster '%cluster;'. Error: %errorMessage;.`

- **OK**: a hibaüzenet a következőhöz hasonló üzenetet tartalmaz: `The remote name could not be resolved.` . Lehetséges, hogy a megadott fürt URI azonosítója érvénytelen.

- **Javaslat**: Ellenőrizze, hogy a fürtöt nem törölték-e, és hogy helyes-e a megadott URI-azonosító. Amikor megnyitja az URI-t egy böngészőben, megjelenik a Ambari felhasználói felülete. Ha a fürt virtuális hálózatban található, az URI-nak a privát URI-nak kell lennie. A megnyitásához használjon egy virtuális gépet (VM), amely ugyanannak a virtuális hálózatnak a részét képezi.

   További információ: [közvetlen kapcsolódás Apache Hadoop szolgáltatásokhoz](../hdinsight/hdinsight-plan-virtual-network-deployment.md#directly-connect-to-apache-hadoop-services).
 
 </br>

- **OK**: Ha a hibaüzenet a következőhöz hasonló üzenetet tartalmaz `A task was canceled.` , a feladatok beküldésének időkorlátja lejárt.

- **Javaslat**: a probléma lehet általános HDInsight-kapcsolat vagy hálózati kapcsolat. Először ellenőrizze, hogy a HDInsight Ambari felhasználói felülete elérhető-e bármely böngészőből. Ezután győződjön meg arról, hogy a hitelesítő adatai még érvényesek.
   
   Ha saját üzemeltetésű integrált futtatókörnyezetet (IR) használ, hajtsa végre ezt a lépést abban a virtuális gépről vagy gépen, ahol a saját üzemeltetésű IR telepítve van. Ezután próbálja meg újra elküldeni a feladatot Data Factory újra.

   További információkért olvassa el a [Ambari webes felhasználói felületét](../hdinsight/hdinsight-hadoop-manage-ambari.md#ambari-web-ui).

 </br>

- **OK**: Ha a hibaüzenet a vagy a-hoz hasonló üzenetet tartalmaz `User admin is locked out in Ambari` `Unauthorized: Ambari user name or password is incorrect` , a HDInsight tartozó hitelesítő adatok helytelenek vagy lejártak.

- **Javaslat**: javítsa ki a hitelesítő adatokat, és telepítse újra a társított szolgáltatást. Először ellenőrizze, hogy a hitelesítő adatok a HDInsight működnek-e. Ehhez nyissa meg a fürt URI-JÁT bármely böngészőben, és próbáljon meg bejelentkezni. Ha a hitelesítő adatok nem működnek, alaphelyzetbe állíthatja azokat a Azure Portal.

   ESP-fürt esetén állítsa alaphelyzetbe a jelszót az [önkiszolgáló jelszó-visszaállítás](../active-directory/user-help/active-directory-passwords-update-your-own-password.md)használatával.

 </br>

- **OK**: Ha a hibaüzenet a következőhöz hasonló üzenetet tartalmaz `502 - Web server received an invalid response while acting as a gateway or proxy server` , ezt a hibát a HDInsight szolgáltatás adja vissza.

- **Javaslat**: a 502-es hiba gyakran fordul elő, amikor a Ambari-kiszolgáló folyamata leállt. A Ambari-szolgáltatásokat a fő csomópont újraindításával indíthatja újra.

    1. Kapcsolódjon a HDInsight egyik csomópontjához az SSH használatával.
    1. A futtatásával azonosítsa az aktív fő csomópont-gazdagépet `ping headnodehost` .
    1. Csatlakozzon az aktív főcsomóponthoz, mert a Ambari-kiszolgáló az SSH használatával az aktív főcsomóponton található. 
    1. Indítsa újra az aktív fő csomópontot.

       További információkért tekintse meg az Azure HDInsight hibaelhárítási dokumentációját. Például:

       * [502-es hiba az Ambari felhasználói felületén](https://hdinsight.github.io/ambari/ambari-ui-502-error.html)
       * [RpcTimeoutException Apache Spark takarékossági kiszolgálóhoz](../hdinsight/spark/apache-spark-troubleshoot-rpctimeoutexception.md)
       * [Hibás átjárókkal kapcsolatos hibák elhárítása Application Gatewayban](../application-gateway/application-gateway-troubleshooting-502.md).

 </br>

- **OK**: Ha a hibaüzenet a vagy a-hoz hasonló üzenetet tartalmaz `Unable to service the submit job request as templeton service is busy with too many submit job requests` `Queue root.joblauncher already has 500 applications, cannot accept submission of application` , túl sok feladatot küld a HDInsight egy időben.

- **Javaslat**: korlátozza a HDInsight küldött egyidejű feladatok számát. Ha ugyanaz a tevékenység küldi el a feladatokat, tekintse meg Data Factory tevékenység egyidejűségét. Módosítsa az eseményindítókat úgy, hogy az egyidejű folyamat-futtatások egyszerre legyenek kiosztva.

   A hiba feltételeit a [HDInsight dokumentációjában](../hdinsight/hdinsight-hadoop-templeton-webhcat-debug-errors.md) találja `templeton.parallellism.job.submit` .

### <a name="error-code-2301"></a>Hibakód: 2301

- **Üzenet**: `Could not get the status of the application '%physicalJobId;' from the HDInsight service. Received the following error: %message;. Please refer to HDInsight troubleshooting documentation or contact their support for further assistance.`

- **OK**: a HDInsight-fürt vagy-szolgáltatás problémái vannak.

- **Javaslat**: Ez a hiba akkor fordul elő, ha az ADF nem kap választ a HDInsight-fürttől a futó feladatokhoz tartozó állapot igénylésére tett kísérlet során. Előfordulhat, hogy a probléma maga a fürtön található, vagy a HDInsight szolgáltatás leállás miatt leáll.

   További segítségért tekintse meg a HDInsight hibaelhárítási dokumentációját https://docs.microsoft.com/azure/hdinsight/hdinsight-troubleshoot-guide , vagy forduljon a támogatási szolgálathoz.

### <a name="error-code-2302"></a>Hibakód: 2302

- **Üzenet**: `Hadoop job failed with exit code '%exitCode;'. See '%logPath;/stderr' for more details. Alternatively, open the Ambari UI on the HDI cluster and find the logs for the job '%jobId;'. Contact HDInsight team for further support.`

- **OK**: a feladatot elküldték a HDI-fürtnek, és ott volt a hiba.

- **Javaslat**: 

 1. Ambari felhasználói felületének keresése:
    1. Győződjön meg arról, hogy az összes szolgáltatás továbbra is fut.
    1. A Ambari felhasználói felületén tekintse meg az irányítópult riasztások szakaszát.
       1. A riasztásokra vonatkozó riasztásokkal és megoldásokkal kapcsolatos további információkért lásd: [fürt kezelése és figyelése](https://docs.cloudera.com/HDPDocuments/Ambari-2.7.5.0/managing-and-monitoring-ambari/content/amb_predefined_alerts.html).
    1. Tekintse át a fonal memóriáját. Ha a szál mérete magas, a feladatok feldolgozása késleltethető lehet. Ha nem rendelkezik elegendő erőforrással a Spark-alkalmazás/-feladatokhoz, a fürt vertikális felskálázásával gondoskodhat arról, hogy a fürt elegendő memóriával és maggal rendelkezzen. 
 1. Egy minta tesztelési feladatot futtasson.
    1. Ha ugyanezt a feladatot a HDInsight-háttérön futtatja, ellenőrizze, hogy az sikeres volt-e. Példák a minták [futtatására: az HDInsight-ben található MapReduce-példák futtatása](../hdinsight/hadoop/apache-hadoop-run-samples-linux.md) . 
 1. Ha a feladatnak a HDInsight továbbra is sikertelen, ellenőrizze az alkalmazás naplóit és információit, amelyek a támogatás biztosításához szükségesek:
    1. Győződjön meg arról, hogy a feladatot elküldték-e a FONALba. Ha a feladatot nem küldi el a fonalnak, használja a következőt: `--master yarn` .
    1. Ha az alkalmazás befejezte a végrehajtást, gyűjtsön a fonal-alkalmazás kezdési és befejezési időpontját. Ha az alkalmazás nem fejezte be a végrehajtást, gyűjtse be a kezdési időt/indítást.
    1. Keresse meg és Gyűjtse össze az alkalmazás naplóját `yarn logs -applicationId <Insert_Your_Application_ID>` .
    1. Keresse meg és Gyűjtse össze a fonál Resource Manager-naplókat a `/var/log/hadoop-yarn/yarn` címtárban.
    1. Ha ezek a lépések nem elegendőek a probléma megoldásához, lépjen kapcsolatba az Azure HDInsight csapatával, és adja meg a fenti naplókat és időbélyegeket.

### <a name="error-code-2303"></a>Hibakód: 2303

- **Üzenet**: `Hadoop job failed with transient exit code '%exitCode;'. See '%logPath;/stderr' for more details. Alternatively, open the Ambari UI on the HDI cluster and find the logs for the job '%jobId;'. Try again or contact HDInsight team for further support.`

- **OK**: a feladatot elküldték a HDI-fürtnek, és ott volt a hiba.

- **Javaslat**: 

 1. Ambari felhasználói felületének keresése:
    1. Győződjön meg arról, hogy az összes szolgáltatás továbbra is fut.
    1. A Ambari felhasználói felületén tekintse meg az irányítópult riasztások szakaszát.
       1. A riasztásokra vonatkozó riasztásokkal és megoldásokkal kapcsolatos további információkért lásd: [fürt kezelése és figyelése](https://docs.cloudera.com/HDPDocuments/Ambari-2.7.5.0/managing-and-monitoring-ambari/content/amb_predefined_alerts.html).
    1. Tekintse át a fonal memóriáját. Ha a szál mérete magas, a feladatok feldolgozása késleltethető lehet. Ha nem rendelkezik elegendő erőforrással a Spark-alkalmazás/-feladatokhoz, a fürt vertikális felskálázásával gondoskodhat arról, hogy a fürt elegendő memóriával és maggal rendelkezzen. 
 1. Egy minta tesztelési feladatot futtasson.
    1. Ha ugyanezt a feladatot a HDInsight-háttérön futtatja, ellenőrizze, hogy az sikeres volt-e. Példák a minták [futtatására: az HDInsight-ben található MapReduce-példák futtatása](../hdinsight/hadoop/apache-hadoop-run-samples-linux.md) . 
 1. Ha a feladatnak a HDInsight továbbra is sikertelen, ellenőrizze az alkalmazás naplóit és információit, amelyek a támogatás biztosításához szükségesek:
    1. Győződjön meg arról, hogy a feladatot elküldték-e a FONALba. Ha a feladatot nem küldi el a fonalnak, használja a következőt: `--master yarn` .
    1. Ha az alkalmazás befejezte a végrehajtást, gyűjtsön a fonal-alkalmazás kezdési és befejezési időpontját. Ha az alkalmazás nem fejezte be a végrehajtást, gyűjtse be a kezdési időt/indítást.
    1. Keresse meg és Gyűjtse össze az alkalmazás naplóját `yarn logs -applicationId <Insert_Your_Application_ID>` .
    1. Keresse meg és Gyűjtse össze a fonál Resource Manager-naplókat a `/var/log/hadoop-yarn/yarn` címtárban.
    1. Ha ezek a lépések nem elegendőek a probléma megoldásához, lépjen kapcsolatba az Azure HDInsight csapatával, és adja meg a fenti naplókat és időbélyegeket.

### <a name="error-code-2304"></a>Hibakód: 2304

- **Üzenet**: `MSI authentication is not supported on storages for HDI activities.`

- **OK**: a HDINSIGHT (HDI) társított szolgáltatásban vagy a HDI-tevékenységben használt Storage társított szolgáltatások olyan MSI-hitelesítéssel vannak konfigurálva, amely nem támogatott.

- **Javaslat**: teljes kapcsolati karakterláncokat biztosít a HDI társított szolgáltatásban vagy a HDI-tevékenységben használt Storage-fiókok számára.

### <a name="error-code-2305"></a>Hibakód: 2305

- **Üzenet**: `Failed to initialize the HDInsight client for the cluster '%cluster;'. Error: '%message;'`

- **OK**: a HDI-fürt kapcsolati adatai helytelenek, a megadott felhasználónak nincs engedélye a szükséges művelet végrehajtására, vagy a HDInsight szolgáltatás nem válaszol az ADF kéréseire.

- **Javaslat**: Ellenőrizze, hogy a felhasználói adatok helyesek-e, és hogy a HDI-fürt Ambari felhasználói felülete megnyitható-e böngészőben a virtuális gépen, AMELYEN az IR telepítve van (saját üzemeltetésű integrációs modul esetén), vagy bármely gépről megnyitható (Azure IR).

### <a name="error-code-2306"></a>Hibakód: 2306

- **Üzenet**: `An invalid json is provided for script action '%scriptActionName;'. Error: '%message;'`

- **OK**: a parancsfájl műveletéhez megadott JSON érvénytelen.

- **Javaslat**: a hibaüzenetnek segítséget kell nyújtania a probléma azonosításához. Javítsa ki a JSON-konfigurációt, és próbálkozzon újra.

   További információért tekintse [meg az Azure HDInsight igény szerinti társított szolgáltatását](./compute-linked-services.md#azure-hdinsight-on-demand-linked-service) .

### <a name="error-code-2310"></a>Hibakód: 2310

- **Üzenet**: `Failed to submit Spark job. Error: '%message;'`

- **OK**: Az ADF megpróbált létrehozni egy köteget egy Spark-fürtön a Livy API (Livy/batch) használatával, de hibaüzenetet kapott.

- **Javaslat**: a probléma megoldásához kövesse a hibaüzenetet. Ha nincs elegendő információ az eszköz feloldásához, lépjen kapcsolatba a HDI csapatával, és adja meg a Batch-azonosítót és a feladatot AZONOSÍTÓját, amely a tevékenység futtatása kimenete ADF-figyelés oldalon található. A további hibák megoldásához Gyűjtse össze a Batch-feladatok teljes naplóját.

   A teljes napló begyűjtésével kapcsolatos további információkért lásd: [Batch-feladatok teljes naplójának beolvasása](/rest/api/hdinsightspark/hdinsight-spark-batch-job#get-the-full-log-of-a-batch-job).

### <a name="error-code-2312"></a>Hibakód: 2312

- **Üzenet**: `Spark job failed, batch id:%batchId;. Please follow the links in the activity run Output from ADF Monitoring page to troubleshoot the run on HDInsight Spark cluster. Please contact HDInsight support team for further assistance.`

- **OK**: a HDInsight Spark-fürtön nem sikerült végrehajtani a feladatot.

- **Javaslat**: a HDInsight Spark-fürtön futtatott futtatási hibák megoldásához kövesse a tevékenység futtatása kimenetben az ADF figyelése oldalon található hivatkozásokat. További segítségért forduljon a HDInsight támogatási csapatához.

   A teljes napló begyűjtésével kapcsolatos további információkért lásd: [Batch-feladatok teljes naplójának beolvasása](/rest/api/hdinsightspark/hdinsight-spark-batch-job#get-the-full-log-of-a-batch-job).

### <a name="error-code-2313"></a>Hibakód: 2313

- **Üzenet**: `The batch with ID '%batchId;' was not found on Spark cluster. Open the Spark History UI and try to find it there. Contact HDInsight support for further assistance.`

- **OK**: a köteg törölve lett a HDInsight Spark-fürtön.

- **Javaslat**: kötegek hibakeresése a HDInsight Spark-fürtön. További segítségért forduljon a HDInsight támogatási szolgálatához. 

   A teljes napló begyűjtésével kapcsolatos további információkért lásd: [a Batch-feladatok teljes naplójának beolvasása](/rest/api/hdinsightspark/hdinsight-spark-batch-job#get-the-full-log-of-a-batch-job), valamint a HDInsight-támogatással rendelkező teljes napló megosztása további segítségért.

### <a name="error-code-2328"></a>Hibakód: 2328

- **Üzenet**: `Failed to create the on demand HDI cluster. Cluster or linked service name: '%clusterName;', error: '%message;'`

- **OK**: a hibaüzenetből megtudhatja, hogy mi volt a hiba.

- **Javaslat**: a hibaüzenetnek segítenie kell a probléma megoldásában.

### <a name="error-code-2329"></a>Hibakód: 2329

- **Üzenet**: `Failed to delete the on demand HDI cluster. Cluster or linked service name: '%clusterName;', error: '%message;'`

- **OK**: a hibaüzenetből megtudhatja, hogy mi volt a hiba.

- **Javaslat**: a hibaüzenetnek segítenie kell a probléma megoldásában.

### <a name="error-code-2331"></a>Hibakód: 2331

- **Üzenet**: `The file path should not be null or empty.`

- **OK**: a megadott fájl elérési útja üres.

- **Javaslat**: adjon meg egy elérési utat a létező fájlhoz.

### <a name="error-code-2340"></a>Hibakód: 2340

- **Üzenet**: `HDInsightOnDemand linked service does not support execution via SelfHosted IR. Your IR name is '%IRName;'. Please select an Azure IR instead.`

- **OK**: a HDInsightOnDemand társított szolgáltatás nem támogatja a végrehajtást a SelfHosted IR-n keresztül.

- **Javaslat**: válasszon ki egy Azure IR, majd próbálkozzon újra.

### <a name="error-code-2341"></a>Hibakód: 2341

- **Üzenet**: `HDInsight cluster URL '%clusterUrl;' is incorrect, it must be in URI format and the scheme must be 'https'.`

- **OK**: a megadott URL-cím nem megfelelő formátumú.

- **Javaslat**: javítsa ki a fürt URL-címét, és próbálkozzon újra.

### <a name="error-code-2342"></a>Hibakód: 2342

- **Üzenet**: `Failed to connect to HDInsight cluster: '%errorMessage;'.`

- **OK**: a megadott hitelesítő adatok nem megfelelőek a fürthöz, vagy hálózati konfiguráció vagy kapcsolódási probléma lépett fel, vagy az IR probléma merült fel a fürthöz való csatlakozás során.

- **Javaslat**: 
    1. Ellenőrizze, hogy a hitelesítő adatok helyesek-e. Ehhez nyissa meg a HDInsight-fürt Ambari felhasználói felületét egy böngészőben.
    1. Ha a fürt Virtual Networkban (VNet) van használatban, és egy saját üzemeltetésű IR-t használ, a HDI URL-címnek a virtuális hálózatok lévő privát URL-címnek kell lennie, és a fürt neve után "-int" értékűnek kell lennie.
    
       Például váltson a következőre: `https://mycluster.azurehdinsight.net/` `https://mycluster-int.azurehdinsight.net/` . Jegyezze fel a következőt `-int` `mycluster` , de előtte `.azurehdinsight.net`
    1. Ha a fürt VNet van, a rendszer a saját üzemeltetésű integrációs modult használja, és a magánhálózati URL-címet használta, és még a kapcsolat továbbra is meghiúsult, akkor az a virtuális gép, amelyen az IR telepítve volt, problémák léptek fel a HDI-hez való csatlakozás során. 
    
       Kapcsolódjon ahhoz a virtuális géphez, amelyen az IR telepítve van, és nyissa meg a Ambari felhasználói felületét egy böngészőben. Használja a fürt magánhálózati URL-címét. A hálózatnak a böngészőben kell működnie. Ha nem, további segítségért lépjen kapcsolatba a HDInsight támogatási csapatával.
    1. Ha a saját üzemeltetésű integrációs modul nincs használatban, akkor a HDI-fürtnek nyilvánosan elérhetőnek kell lennie. Nyissa meg a Ambari felhasználói felületét egy böngészőben, és győződjön meg róla, hogy megnyílik. Ha problémák merülnek fel a fürttel vagy a szolgáltatásokkal kapcsolatban, kérjen segítséget a HDInsight támogatási csapattól.

       Az ADF-alapú társított szolgáltatásban használt HDI-fürt URL-címének elérhetőnek kell lennie az ADF-hez (helyi vagy Azure-hoz) ahhoz, hogy a teszt kapcsolata sikeres legyen, és a futtatások működjenek. Ez az állapot ellenőrizhető úgy, hogy megnyit egy böngészőből származó URL-címet vagy egy virtuális gépről, vagy egy nyilvános gépről.

### <a name="error-code-2343"></a>Hibakód: 2343

- **Üzenet**: `User name and password cannot be null or empty to connect to the HDInsight cluster.`

- **OK**: a Felhasználónév vagy a jelszó üres.

- **Javaslat**: adja meg a megfelelő hitelesítő adatokat a HDI-hez való kapcsolódáshoz, majd próbálkozzon újra.

### <a name="error-code-2345"></a>Hibakód: 2345

- **Üzenet**: `Failed to read the content of the hive script. Error: '%message;'`

- **OK**: a parancsfájl nem létezik, vagy az ADF nem tudott csatlakozni a parancsfájl helyéhez.

- **Javaslat**: Ellenőrizze, hogy létezik-e a parancsfájl, és hogy a társított társított szolgáltatás rendelkezik-e a megfelelő hitelesítő adatokkal a kapcsolathoz.

### <a name="error-code-2346"></a>Hibakód: 2346

- **Üzenet**: `Failed to create ODBC connection to the HDI cluster with error message '%message;'.`

- **OK**: Az ADF megpróbált létrehozni egy Open Database Connectivity (ODBC) kapcsolatot a HDI-fürthöz, és hiba miatt meghiúsult.

- **Javaslat**: 

   1. Győződjön meg arról, hogy helyesen állította be az ODBC/Java-adatbázis kapcsolati (JDBC) kapcsolatát.
      1. A JDBC esetében, ha ugyanazt a virtuális hálózatot használja, a következőt érheti el:<br>
        `Hive -> Summary -> HIVESERVER2 JDBC URL`
      1. A megfelelő JDBC-beállítás biztosításához tekintse meg a következőt: [lekérdezés Apache Hive a JDBC-illesztőprogramon keresztül a HDInsight-ben](../hdinsight/hadoop/apache-hadoop-connect-hive-jdbc-driver.md).
      1. A nyílt adatbázisok (ODB) esetében tekintse meg az [oktatóanyag: lekérdezés Apache HIVE ODBC és PowerShell](../hdinsight/interactive-query/apache-hive-query-odbc-driver-powershell.md) használatával című témakört, amely biztosítja, hogy a megfelelő beállítással rendelkezzen. 
   1. Ellenőrizze, hogy az Hiveserver2, a kaptár Metaadattár és a Hiveserver2 Interactive aktív-e és működik-e. 
   1. Tekintse meg a Ambari felhasználói felületét (UI):
      1. Győződjön meg arról, hogy az összes szolgáltatás továbbra is fut.
      1. Az Ambari felhasználói felületén tekintse meg az irányítópult riasztások szakaszát.
         1. A riasztásokra vonatkozó riasztásokkal és megoldásokkal kapcsolatos további információkért lásd: [fürt kezelése és figyelése ](https://docs.cloudera.com/HDPDocuments/Ambari-2.7.5.0/managing-and-monitoring-ambari/content/amb_predefined_alerts.html).
   1. Ha ezek a lépések nem elegendőek a probléma megoldásához, lépjen kapcsolatba az Azure HDInsight csapatával.

### <a name="error-code-2347"></a>Hibakód: 2347

- **Üzenet**: `Hive execution through ODBC failed with error message '%message;'.`

- **OK**: Az ADF elküldte a kaptár-szkriptet a HDI-fürthöz való végrehajtáshoz az ODBC-kapcsolatok használatával, és a parancsfájl a HDI-on meghiúsult.

- **Javaslat**: 

   1. Győződjön meg arról, hogy helyesen állította be az ODBC/Java-adatbázis kapcsolati (JDBC) kapcsolatát.
      1. A JDBC esetében, ha ugyanazt a virtuális hálózatot használja, a következőt érheti el:<br>
        `Hive -> Summary -> HIVESERVER2 JDBC URL`
      1. A megfelelő JDBC-beállítás biztosításához tekintse meg a következőt: [lekérdezés Apache Hive a JDBC-illesztőprogramon keresztül a HDInsight-ben](../hdinsight/hadoop/apache-hadoop-connect-hive-jdbc-driver.md).
      1. A nyílt adatbázisok (ODB) esetében tekintse meg az [oktatóanyag: lekérdezés Apache HIVE ODBC és PowerShell](../hdinsight/interactive-query/apache-hive-query-odbc-driver-powershell.md) használatával című témakört, amely biztosítja, hogy a megfelelő beállítással rendelkezzen. 
   1. Ellenőrizze, hogy az Hiveserver2, a kaptár Metaadattár és a Hiveserver2 Interactive aktív-e és működik-e. 
   1. Tekintse meg a Ambari felhasználói felületét (UI):
      1. Győződjön meg arról, hogy az összes szolgáltatás továbbra is fut.
      1. Az Ambari felhasználói felületén tekintse meg az irányítópult riasztások szakaszát.
         1. A riasztásokra vonatkozó riasztásokkal és megoldásokkal kapcsolatos további információkért lásd: [fürt kezelése és figyelése ](https://docs.cloudera.com/HDPDocuments/Ambari-2.7.5.0/managing-and-monitoring-ambari/content/amb_predefined_alerts.html).
   1. Ha ezek a lépések nem elegendőek a probléma megoldásához, lépjen kapcsolatba az Azure HDInsight csapatával.

### <a name="error-code-2348"></a>Hibakód: 2348

- **Üzenet**: `The main storage has not been initialized. Please check the properties of the storage linked service in the HDI linked service.`

- **OK**: a Storage társított szolgáltatás tulajdonságai nincsenek megfelelően beállítva.

- **Javaslat**: csak a teljes kapcsolati karakterláncok támogatottak a fő tárolóhoz társított szolgáltatásban a HDI-tevékenységekhez. Ellenőrizze, hogy nem használ-e MSI-engedélyeket vagy-alkalmazásokat.

### <a name="error-code-2350"></a>Hibakód: 2350

- **Üzenet**: `Failed to prepare the files for the run '%jobId;'. HDI cluster: '%cluster;', Error: '%errorMessage;'`

- **OK**: a megadott hitelesítő adatok nem megfelelőek ahhoz a tárolóhoz, ahol a fájlok találhatók, vagy a fájlok nem léteznek.

- **Javaslat**: Ez a hiba akkor fordul elő, ha az ADF HDI-tevékenységeket készít elő, és a feladatoknak a HDI szolgáltatásba való elküldése előtt megpróbál fájlokat másolni a fő tárolóba. Győződjön meg arról, hogy a fájlok szerepelnek a megadott helyen, és hogy helyesek-e a tárolási kapcsolatok. Mivel az ADF HDI-tevékenységek nem támogatják az MSI-hitelesítést a HDI-tevékenységekhez kapcsolódó tárolási fiókokon, győződjön meg arról, hogy ezek a társított szolgáltatások teljes kulccsal rendelkeznek, vagy a Azure Key Vault használják.

### <a name="error-code-2351"></a>Hibakód: 2351

- **Üzenet**: `Could not open the file '%filePath;' in container/fileSystem '%container;'.`

- **OK**: a fájl nem létezik a megadott elérési úton.

- **Javaslat**: Győződjön meg arról, hogy a fájl valóban létezik-e, és hogy a társított szolgáltatás a fájlra mutató kapcsolódási információkkal rendelkezik-e a megfelelő hitelesítő adatokkal.

### <a name="error-code-2352"></a>Hibakód: 2352

- **Üzenet**: `The file storage has not been initialized. Please check the properties of the file storage linked service in the HDI activity.`

- **OK**: a file Storage társított szolgáltatás tulajdonságai nincsenek megfelelően beállítva.

- **Javaslat**: Ellenőrizze, hogy a file Storage társított szolgáltatásának tulajdonságai megfelelően vannak-e konfigurálva.

### <a name="error-code-2353"></a>Hibakód: 2353

- **Üzenet**: `The script storage has not been initialized. Please check the properties of the script storage linked service in the HDI activity.`

- **OK**: a parancsfájl-tárolási társított szolgáltatás tulajdonságai nincsenek megfelelően beállítva.

- **Javaslat**: Ellenőrizze, hogy a parancsfájl-tárolási társított szolgáltatás tulajdonságai megfelelően vannak-e konfigurálva.

### <a name="error-code-2354"></a>Hibakód: 2354

- **Üzenet**: `The storage linked service type '%linkedServiceType;' is not supported for '%executorType;' activities for property '%linkedServicePropertyName;'.`

- **OK**: a tevékenység nem támogatja a Storage társított szolgáltatás típusát.

- **Javaslat**: Ellenőrizze, hogy a kiválasztott társított szolgáltatás rendelkezik-e a tevékenység által támogatott típusok egyikével. A HDI-tevékenységek támogatják a AzureBlobStorage és a AzureBlobFSStorage társított szolgáltatásokat.

   További információért olvassa el az [Azure HDInsight-fürtökkel való használatra vonatkozó tárolási lehetőségek összehasonlítása](../hdinsight/hdinsight-hadoop-compare-storage-options.md) című témakört.

### <a name="error-code-2355"></a>Hibakód: 2355

- **Üzenet**: `The '%value' provided for commandEnvironment is incorrect. The expected value should be an array of strings where each string has the format CmdEnvVarName=CmdEnvVarValue.`

- **OK**: a megadott érték `commandEnvironment` helytelen.

- **Javaslat**: Ellenőrizze, hogy a megadott érték a következőhöz hasonló:
 
    ``` \"commandEnvironment\": [
    \"variableName=variableValue\"
    ]
    ```

    Azt is ellenőrizze, hogy az egyes változók csak egyszer jelennek-e meg a listában.

### <a name="error-code-2356"></a>Hibakód: 2356

- **Üzenet**: `The commandEnvironment already contains a variable named '%variableName;'.`

- **OK**: a megadott érték `commandEnvironment` helytelen.

- **Javaslat**: Ellenőrizze, hogy a megadott érték a következőhöz hasonló:
 
    ``` \"commandEnvironment\": [
    \"variableName=variableValue\"
    ]
    ```

    Azt is ellenőrizze, hogy az egyes változók csak egyszer jelennek-e meg a listában.

### <a name="error-code-2357"></a>Hibakód: 2357

- **Üzenet**: `The certificate or password is wrong for ADLS Gen 1 storage.`

- **OK**: a megadott hitelesítő adatok helytelenek.

- **Javaslat**: Ellenőrizze, hogy a ADLS 1. generációs kapcsolati adatai a szolgáltatáshoz vannak-e kapcsolva, és ellenőrizze, hogy a kapcsolat tesztelése sikeres-e.

### <a name="error-code-2358"></a>Hibakód: 2358

- **Üzenet**: `The value '%value;' for the required property 'TimeToLive' in the on demand HDInsight linked service '%linkedServiceName;' has invalid format. It should be a timespan between '00:05:00' and '24:00:00'.`

- **OK**: a kötelező tulajdonsághoz megadott érték `TimeToLive` formátuma érvénytelen. 

- **Javaslat**: frissítse az értéket a javasolt tartományra, és próbálkozzon újra.

### <a name="error-code-2359"></a>Hibakód: 2359

- **Üzenet**: `The value '%value;' for the property 'roles' is invalid. Expected types are 'zookeeper', 'headnode', and 'workernode'.`

- **OK**: a tulajdonsághoz megadott érték `roles` érvénytelen.

- **Javaslat**: frissítse az értéket úgy, hogy az egyik javaslat legyen, és próbálkozzon újra.

### <a name="error-code-2360"></a>Hibakód: 2360

- **Üzenet**: `The connection string in HCatalogLinkedService is invalid. Encountered an error while trying to parse: '%message;'.`

- **OK**: a megadott "a" karakterlánc `HCatalogLinkedService` érvénytelen.

- **Javaslat**: frissítse az értéket egy megfelelő Azure SQL-kapcsolódási sztringre, és próbálkozzon újra.

### <a name="error-code-2361"></a>Hibakód: 2361

- **Üzenet**: `Failed to create on demand HDI cluster. Cluster name is '%clusterName;'.`

- **OK**: a fürt létrehozása nem sikerült, és az ADF nem adott vissza hibát a HDInsight szolgáltatástól.

- **Javaslat**: Nyissa meg a Azure Portal, és próbálja meg megkeresni a HDI-erőforrást a megadott névvel, majd ellenőrizze a kiépítési állapotot. További segítségért forduljon a HDInsight támogatási csapatához.

### <a name="error-code-2362"></a>Hibakód: 2362

- **Üzenet**: `Only Azure Blob storage accounts are supported as additional storages for HDInsight on demand linked service.`

- **OK**: a megadott további tárterület nem Azure Blob Storage.

- **Javaslat**: adjon meg egy Azure Blob Storage-fiókot további tárolóként a HDInsight igény szerinti társított szolgáltatáshoz.

### <a name="ssl-error-when-adf-linked-service-using-hdinsight-esp-cluster"></a>SSL-hiba a társított szolgáltatás HDInsight ESP-fürttel való csatolásakor

- **Üzenet**: `Failed to connect to HDInsight cluster: 'ERROR [HY000] [Microsoft][DriverSupport] (1100) SSL certificate verification failed because the certificate is missing or incorrect.`

- **OK**: a probléma valószínűleg a rendszer-megbízhatósági tárolóval kapcsolatos.

- **Megoldás**: Nyissa meg az elérési utat a **Microsoft Integration RUNTIME\4.0\SHARED\ODBC Drivers\Microsoft kaptár ODBC Driver\lib** , és nyissa meg DriverConfiguration64.exe a beállítás módosításához.

    ![Törölje a rendszermegbízhatósági tároló használata jelölőnégyzet jelölését.](./media/connector-troubleshoot-guide/system-trust-store-setting.png)

## <a name="web-activity"></a>Webes tevékenység

### <a name="error-code-2128"></a>Hibakód: 2128

- **Üzenet**: `No response from the endpoint. Possible causes: network connectivity, DNS failure, server certificate validation or timeout.`

- **OK**: Ez a probléma a hálózati kapcsolat, a DNS-hiba, a kiszolgálói tanúsítvány érvényesítése vagy időtúllépés miatt fordul elő.

- **Javaslat**: Ellenőrizze, hogy a elérni kívánt végpont válaszol-e a kérelmekre. Használhat olyan eszközöket is, mint a **Hegedűs vagy a Poster**.

### <a name="error-code-2108"></a>Hibakód: 2108

- **Üzenet**: `Error calling the endpoint '%url;'. Response status code: '%code;'`

- **OK**: a kérelem végrehajtása egy mögöttes probléma miatt meghiúsult, például a hálózati kapcsolat, a DNS-hiba, a kiszolgálói tanúsítvány érvényesítése vagy időtúllépés.

- **Javaslat**: a Hegedűs/Poster használatával ellenőrizze a kérést.

#### <a name="more-details"></a>További részletek
A **Hegedűs** használata a figyelt webalkalmazás http-munkamenetének létrehozásához:

1. A [Hegedűs](https://www.telerik.com/download/fiddler)letöltése, telepítése és megnyitása.

1. Ha a webalkalmazás HTTPS protokollt használ, lépjen az **eszközök**  >  **Hegedűs beállítások**  >  **https** elemre.

   1. A HTTPS lapon válassza a **rögzítés HTTPS** -kapcsolat és a **https-forgalom visszafejtése** lehetőséget.

      ![Hegedűs beállításai](media/data-factory-troubleshoot-guide/fiddler-options.png)

1. Ha az alkalmazás TLS/SSL-tanúsítványokat használ, adja hozzá a Hegedűs tanúsítványát az eszközhöz.

   Ugrás: az **eszközök**  >  a **Hegedűs beállításai**  >  **https**  >  -**műveletekkel**  >  **exportálják a főtanúsítványt az asztalra**.

1. Kapcsolja ki a rögzítést a **fájl**  >  **rögzítési forgalmának** kikapcsolásával. Vagy nyomja le az **F12** billentyűt.

1. Törölje a böngésző gyorsítótárát úgy, hogy minden gyorsítótárazott elem el legyen távolítva, és újra le kell tölteni.

1. Kérelem létrehozása:

1. Válassza a **zeneszerző** fület.

   1. Állítsa be a HTTP-metódust és az URL-címet.
 
   1. Ha szükséges, adja hozzá a fejléceket és a kérelem törzsét.

   1. Válassza a **Végrehajtás** lehetőséget.

1. Kapcsolja be újra a forgalom rögzítését, és fejezze be a problémás tranzakciót az oldalon.

1. Ugrás: **fájl**–  >    >  **minden munkamenet** mentése.

További információ: [Bevezetés a Hegedűs](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/ConfigureFiddler)használatába.

## <a name="general"></a>Általános

### <a name="activity-stuck-issue"></a>A tevékenység beragadt probléma

Ha azt tapasztalja, hogy a tevékenység sokkal hosszabb ideig fut, mint a normál Futtatás, és alig van előrehaladás, előfordulhat, hogy elakad. Megpróbálkozhat a megszakítással, és újra megtekintheti, hogy segít-e a szolgáltatás. Ha ez egy másolási tevékenység, megismerheti a teljesítmény figyelését és a hibaelhárítást a [másolási tevékenység teljesítményének hibaelhárítása](copy-activity-performance-troubleshooting.md)során. Ha ez egy adatfolyam, tanulja meg az [Adatáramlások teljesítményének](concepts-data-flow-performance.md) és hangolási útmutatójának feltérképezését.

### <a name="payload-is-too-large"></a>A hasznos adat túl nagy

**Hibaüzenet:**`The payload including configurations on activity/dataSet/linked service is too large. Please check if you have settings with very large value and try to reduce its size.`

**OK:** Az egyes tevékenységekhez tartozó adattartalom magában foglalja a tevékenység konfigurációját, a kapcsolódó adatkészleteket és a társított szolgáltatás (oka) t, és ha vannak ilyenek, a Rendszertulajdonságok egy kis részét. Az ilyen hasznos adatok mérete 896 KB, [Data Factory korlátok](../azure-resource-manager/management/azure-subscription-service-limits.md#data-factory-limits) szakaszban említettek szerint.

**Javaslat:** Ezt a korlátot valószínűleg azért éri el, mert egy vagy több nagyobb paraméter-értéket ad át a felsőbb rétegbeli tevékenységek kimenetéről vagy külsőről, különösen akkor, ha tényleges adatokat ad át a vezérlési folyamat tevékenységei között. Ellenőrizze, hogy a nagyméretű paraméterek méretének csökkentése vagy a folyamat logikájának hangolása révén elkerülhető-e az értékek átadása a tevékenységek között, és hogyan kezelheti azt a tevékenységen belül.

## <a name="next-steps"></a>Következő lépések

További hibaelhárítási segítségért próbálja ki ezeket az erőforrásokat:

* [Data Factory blog](https://azure.microsoft.com/blog/tag/azure-data-factory/)
* [Data Factory szolgáltatásra vonatkozó kérelmek](https://feedback.azure.com/forums/270578-data-factory)
* [Data Factory Stack Overflow fóruma](https://stackoverflow.com/questions/tagged/azure-data-factory)
* [Twitter-információk a Data Factory](https://twitter.com/hashtag/DataFactory)
* [Azure-videók](https://azure.microsoft.com/resources/videos/index/)
* [Microsoft Q&egy kérdés oldal](/answers/topics/azure-data-factory.html)
