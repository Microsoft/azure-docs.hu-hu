---
title: API-k és eszközök fejlesztők számára
description: Megismerheti az Azure Batch szolgáltatással történő megoldásfejlesztéshez elérhető API-kat és eszközöket.
ms.topic: conceptual
ms.date: 05/22/2020
ms.custom: seodec18
ms.openlocfilehash: 502eb08631223215933b75dca882c12c02d17bd9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "89146402"
---
# <a name="overview-of-batch-apis-and-tools"></a>A Batch API-k és eszközök áttekintése

A párhuzamos számítási feladatok Azure Batch használatával végzett feldolgozása általában programozott módon történik az egyik Batch API-val. Az Ön által készített ügyfélalkalmazások vagy szolgáltatások a Batch API-k használatával kommunikálhatnak a Batch szolgáltatással. A Batch API-kkal számítási csomópontok készletét, virtuális gépeket vagy felhőszolgáltatásokat hozhat létre és felügyelhet. Ezt követően pedig a feladatok és tevékenységek ütemezésével futtathatja őket ezeken a csomópontokon.

Hatékonyan dolgozhat fel nagyméretű számítási feladatokat a szervezet számára, vagy szolgáltatási előtérrendszert nyújthat az ügyfeleknek, hogy feladatokat és tevékenységeket futtathassanak – igény szerint vagy ütemterv alapján – egyetlen, több száz vagy akár több ezer csomóponton. Az Azure Batch szolgáltatást a nagyobb munkafolyamatok részeként is felügyelheti olyan eszközökkel, mint például az [Azure Data Factory](../data-factory/transform-data-using-dotnet-custom-activity.md?toc=%2fazure%2fbatch%2ftoc.json).

> [!TIP]
> Ha többet szeretne megtudni a Azure Batchban használt szolgáltatásokról és munkafolyamatokról, tekintse meg a [Batch szolgáltatás munkafolyamatát és erőforrásait](batch-service-workflow-features.md)ismertető részt.

## <a name="azure-accounts-for-batch-development"></a>A Batch-fejlesztéshez szükséges Azure-fiókok

A Batch-megoldások fejlesztésekor a következő fiókokat fogja használni az Azure-előfizetésében:

- **Batch-fiók** – Azure batch erőforrások, beleértve a készleteket, a számítási csomópontokat, a feladatokat és a feladatokat, egy Azure [Batch-fiókkal](accounts.md)vannak társítva. Amikor az alkalmazás egy kérelmet továbbít a Batch szolgáltatás felé, a hitelesítést az Azure Batch-fiók, a fiók URL-címe és egy hozzáférési kulcs vagy Azure Active Directory-jogkivonat használatával hajtja végre a szolgáltatás. [Létrehozhat egy batch-fiókot](batch-account-create-portal.md) Azure Portal vagy programozott módon.
- **Storage-fiók** – A Batch beépített támogatást kínál az [Azure Storage](../storage/index.yml) fájljainak használatához. Szinte mindegyik Batch-forgatókönyv az Azure Blob Storage-ot használja a tevékenységek által futtatott programok és feldolgozott adatok átmeneti tárolásához, valamint a tevékenységek által létrehozott kimeneti adatok tárolásához. Minden batch-fiók általában egy megfelelő Storage-fiókhoz van társítva.

## <a name="service-level-and-management-level-apis"></a>Szolgáltatási szintű és felügyeleti szintű API-k

Azure Batch két API-készlettel rendelkezik, egyet a szolgáltatási szinthez, egyet pedig a felügyeleti szinthez. Az elnevezés gyakran hasonló, de eltérő eredményeket adnak vissza.

A tevékenység naplójában csak a felügyeleti API-k műveletei vannak nyomon követve. A szolgáltatási szint API-jai megkerülik az Azure Resource Management réteget (management.azure.com), és nincsenek naplózva.

A [Batch szolgáltatásnak a készlet törlésére szolgáló API-](/rest/api/batchservice/pool/delete) ját például közvetlenül a Batch-fiókban kell megcélozni: `DELETE {batchUrl}/pools/{poolId}`

A [készlet törléséhez a Batch Management API-t](/rest/api/batchmanagement/pool/delete)  a Management.Azure.com réteg célozza meg: `DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Batch/batchAccounts/{accountName}/pools/{poolName}`

## <a name="batch-service-apis"></a>A Batch szolgáltatás API-jai

Az alkalmazások és szolgáltatások közvetlen REST API-hívásokat hajthatnak végre, illetve a következő ügyfélkódtárak legalább egyikének használatával futtathatják és kezelhetik az Azure Batch számítási feladatait.

| API | API-referencia | Letöltés | Oktatóanyag | Kódminták | További információ |
| --- | --- | --- | --- | --- | --- |
| **Batch REST** |[Azure REST API – docs](/rest/api/batchservice/) |N/A |- |- | [Támogatott verziók](/rest/api/batchservice/batch-service-rest-api-versioning) |
| **Batch .NET** |[Azure SDK for .NET – docs](/dotnet/api/overview/azure/batch) |[NuGet](https://www.nuget.org/packages/Microsoft.Azure.Batch/) |[Oktatóanyag](tutorial-parallel-dotnet.md) |[GitHub](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp) | [Kibocsátási megjegyzések](https://aka.ms/batch-net-dataplane-changelog) |
| **Batch Python** |[Pythonhoz készült Azure SDK – docs](/python/api/overview/azure/batch/client) |[PyPI](https://pypi.org/project/azure-batch/) |[Oktatóanyag](tutorial-parallel-python.md)|[GitHub](https://github.com/Azure-Samples/azure-batch-samples/tree/master/Python/Batch) | [Olvass el](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/batch/azure-batch/README.md) |
| **Batch Node.js** |[Azure SDK a JavaScripthez – docs](/javascript/api/overview/azure/batch/client) |[npm](https://www.npmjs.com/package/azure-batch) |[Oktatóanyag](batch-nodejs-get-started.md) |- | [Olvass el](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/batch) |
| **Batch Java** |[Java-hoz készült Azure SDK – docs](/java/api/overview/azure/batch) |[Maven](https://search.maven.org/search?q=a:azure-batch) |- |[GitHub](https://github.com/Azure-Samples/azure-batch-samples/tree/master/Java) | [Olvass el](https://github.com/Azure/azure-batch-sdk-for-java)|

## <a name="batch-management-apis"></a>Batch Management API-k

A Batch Azure Resource Manager API-jai programozott hozzáférést biztosítanak a Batch-fiókokhoz. Ezen API-k használatával programozott módon kezelheti a Batch-fiókokat, a kvótákat, az alkalmazáscsomagokat és az erőforrásokat a Microsoft.Batch szolgáltató segítségével.  

| API | API-referencia | Letöltés | Oktatóanyag | Kódminták |
| --- | --- | --- | --- | --- |
| **Batch Management REST** |[Azure REST API – docs](/rest/api/batchmanagement/) |- |- |[GitHub](https://github.com/Azure-Samples/batch-dotnet-manage-batch-accounts) |
| **Batch Management .NET** |[Azure SDK for .NET – docs](/dotnet/api/overview/azure/batch/management) |[NuGet](https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/) | [Oktatóanyag](batch-management-dotnet.md) |[GitHub](https://github.com/Azure-Samples/azure-batch-samples/tree/master/CSharp) |
| **Batch Management Python** |[Pythonhoz készült Azure SDK – docs](/python/api/overview/azure/batch/management) |[PyPI](https://pypi.org/project/azure-mgmt-batch/) |- |- |
| **Batch Management Node.js** |[Azure SDK a JavaScripthez – docs](/javascript/api/overview/azure/batch/management) |[npm](https://www.npmjs.com/package/azure-arm-batch) |- |- | 
| **Batch Management Java** |[Java-hoz készült Azure SDK – docs](/java/api/overview/azure/batch/management) |[Maven](https://search.maven.org/search?q=a:azure-batch) |- |- |

## <a name="batch-command-line-tools"></a>A Batch parancssori eszközei

Ezek a parancssori eszközök ugyanazt a funkcionalitást biztosítják, mint a Batch szolgáltatás API-jai és a Batch Management API-k: 

- [Batch PowerShell-parancsmagok](/powershell/module/az.batch/): Az [Azure PowerShell](/powershell/azure/) modulban található Azure Batch-parancsmagokkal felügyelheti a Batch-erőforrásokat a PowerShell használatával.
- [Azure CLI](/cli/azure): Az Azure parancssori felület (Azure CLI) egy többplatformos eszközkészlet, amely rendszerhéjparancsokat biztosít sok Azure-szolgáltatásokkal, például a Batch szolgáltatással és a Batch Management szolgáltatással való interakcióhoz. Az Azure CLI a Batch szolgáltatással való használatával kapcsolatos további információkért lásd: [Batch-erőforrások kezelése az Azure CLI-vel](batch-cli-get-started.md).

## <a name="other-tools-for-application-development"></a>Egyéb alkalmazásfejlesztési eszközök

Ezek a további eszközök hasznosak lehetnek a Batch-alkalmazások és-szolgáltatások kiépítése és hibakeresése során.

- [Azure Portal](https://portal.azure.com/): Batch-készleteket, -feladatokat és -tevékenységeket hozhat létre, figyelhet meg és törölhet az Azure Portalon. Ezen és egyéb erőforrásokra vonatkozó állapotinformációkat a feladatok futtatásakor, valamint a készletekben lévő számítási csomópontok fájljainak letöltésével is megtekintheti. Letöltheti például egy sikertelen feladat `stderr.txt` fájlját a hibaelhárítás során. Távoli asztali (RDP-) fájlokat is letölthet, amelyekkel bejelentkezhet a számítási csomópontokba.
- [Azure Batch Explorer](https://azure.github.io/BatchExplorer/): A Batch Explorer (korábbi nevén BatchLabs) egy ingyenes, számos funkcióval ellátott, önálló ügyféleszköz Azure Batch-alkalmazások létrehozásához, hibakereséséhez és monitorozásához. Töltse le a [telepítőcsomagot](https://azure.github.io/BatchExplorer/) Mac, Linux vagy Windows rendszerre.
- [Azure batch hajógyár](https://github.com/Azure/batch-shipyard): a Batch hajógyár egy olyan eszköz, amellyel a tároló-alapú batch-feldolgozási és HPC-munkaterhelések kiépítése, végrehajtása és monitorozása Azure Batch.
- [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/): Habár nem szigorúan Azure batch eszköz, a Storage Explorer egy másik értékes eszköz a Batch-megoldások fejlesztése és hibakeresése során.

## <a name="additional-resources"></a>További források

- További információ a Batch-alkalmazás eseményeinek naplózásáról: [Batch-metrikák, riasztások és naplók a diagnosztika kiértékeléséhez és figyeléséhez](batch-diagnostics.md).
- A Batch szolgáltatás által kiváltott eseményekkel kapcsolatos információkért lásd: [Batch Analytics](batch-analytics.md).
- További információ a számítási csomópontok környezeti változókról: [Azure batch futtatókörnyezet környezeti változói](batch-compute-node-environment-variables.md).

## <a name="next-steps"></a>Következő lépések

- Ismerje meg a [Batch szolgáltatás munkafolyamatát és az elsődleges erőforrásokat](batch-service-workflow-features.md) , például a készleteket, a csomópontokat, a feladatokat és a feladatokat.
- [Ismerkedjen meg az Azure Batch .NET-es kódtárával](tutorial-parallel-dotnet.md), hogy megtudja, hogyan használhatja a C# nyelvet és a Batch .NET-es kódtárat egy egyszerű számítási feladat végrehajtásához egy általános Batch-munkafolyamattal. Egy [Python-verzió](tutorial-parallel-python.md) és egy [Node.js-oktatóanyag](batch-nodejs-get-started.md) is elérhető.
- Töltse le a [GitHubon található kódmintákat](https://github.com/Azure-Samples/azure-batch-samples), hogy lássa, hogyan használható a C# és a Python a Batch eszközzel a mintául szolgáló számítási feladatok ütemezése és feldolgozása során.
