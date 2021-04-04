---
title: A Azure Batch nagyméretű párhuzamos feladatokat futtat a felhőben
description: Megismerheti, hogyan használhatja az Azure Batch szolgáltatást nagyméretű párhuzamos és HPC számítási feladatokhoz.
ms.topic: overview
ms.date: 07/30/2020
ms.openlocfilehash: dbd27dc1a00966a2d71952335cfb47c7ca55bc24
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98743100"
---
# <a name="what-is-azure-batch"></a>Mi az Azure Batch?

Az Azure Batch használatával hatékonyan futtathat nagy méretű párhuzamos és nagy teljesítményű feldolgozási (high-performance computing, HPC) Batch-feladatokat az Azure-ban. Az Azure Batch egy számítási csomópontokból (virtuális gépekből) álló készletet hoz létre és felügyel, telepíti a futtatni kívánt alkalmazásokat, és ütemezi a feladatok csomópontokon való futtatását. Nincs telepítve, felügyelni vagy méretezni a fürt vagy a Feladatütemező szoftverét. Ehelyett [Batch API-k és -eszközök](batch-apis-tools.md), parancssori szkriptek vagy az Azure Portal használatával konfigurálhatók, kezelhetők és monitorozhatók a feladatok.

A fejlesztők a Batch platformszolgáltatásként való használatával olyan SaaS-alkalmazásokat vagy ügyfélalkalmazásokat készíthetnek, amelyek esetében nagy mennyiségű végrehajtás szükséges. Létrehozhat például egy olyan szolgáltatást a Batch használatával, amely egy Monte Carlo-beli kockázati szimulációt futtat egy pénzügyi szolgáltató vállalat számára, vagy egy olyan szolgáltatás, amely sok lemezképet dolgoz fel.

A Batch felár nélkül használható. Csak a mögöttes erőforrások, például a virtuális gépek, a tárolók és a hálózatkezelés használatáért kell fizetnie.

Az Azure batch és egyéb HPC-megoldási lehetőségeinek összehasonlítását lásd: [nagy teljesítményű számítástechnika (HPC) az Azure](/azure/architecture/topics/high-performance-computing/)-ban.

## <a name="run-parallel-workloads"></a>Párhuzamos számítási feladatok futtatása

A Batch nagyszerűen működik a belsőleg párhuzamos (más néven „zavaróan párhuzamos”) számítási feladatokkal. A belsőleg párhuzamos számítási feladatok olyan alkalmazásokkal rendelkeznek, amelyek egymástól függetlenül futtathatók, és mindegyik példány befejezte a munka részét. Az alkalmazások végrehajtásakor előfordulhat, hogy bizonyos gyakori adatmennyiségekhez férnek hozzá, de nem kommunikálnak az alkalmazás más példányaival. A belsőleg párhuzamos számítási feladatok ezért nagy méretben futtathatók, amit csupán az alkalmazások egyidejű futtatásához rendelkezésre álló számítási erőforrások mennyisége határoz meg.

Néhány példa a Batchbe bevonható belsőleg párhuzamos számítási feladatokra:

- Pénzügyi kockázatmodellezés Monte Carlo-szimuláció használatával
- VFX és 3D képek renderelése
- Képelemzés és -feldolgozás
- Médiakonvertálás
- génszekvenciák elemzése,
- Optikai karakterfelismerés (OCR)
- Adatbetöltés, -feldolgozás és ETL-műveletek
- Szoftvertesztek végrehajtása

A Batch használatával a [szorosan összekapcsolt munkaterheléseket is futtathatja](batch-mpi.md), ahol a futtatott alkalmazásoknak egymással kell kommunikálni, nem pedig egymástól függetlenül. A szorosan összekapcsolt alkalmazások általában a Message Passing Interface (MPI) API-t használják. A szorosan összekapcsolt számítási feladatokat a Batchben a [Microsoft MPI](/message-passing-interface/microsoft-mpi) vagy az Intel MPI használatával futtathatja. Az alkalmazások teljesítményét specializált [HPC](../virtual-machines/sizes-hpc.md) és [GPU-optimalizált](../virtual-machines/sizes-gpu.md) virtuálisgép-méretek használatával javíthatja.

Néhány példa szorosan összekapcsolt számítási feladatokra:

- Végeselem-elemzés
- Folyadékdinamika
- Több csomópontos MI-betanítás

Számos szorosan összekapcsolt feladat futtatható párhuzamosan a Batch használatával. Például egyszerre több szimulációt is létrehozhat egy adott folyadék egy csőben való áramlására változó csőátmérővel.

## <a name="additional-batch-capabilities"></a>A Batch további képességei

Magasabb szintű, számításifeladat-specifikus képességek is elérhetők az Azure Batchhez:

- A Batch támogatja a nagy méretű [renderelési számítási feladatokat](batch-rendering-service.md) az Autodesk Maya, a 3ds Max, az Arnold, a V-Ray és az egyéb renderelő eszközök használatával. 
- Az R-felhasználók a [doAzureParallel R-csomag](https://github.com/Azure/doAzureParallel) telepítésével könnyen felskálázhatják horizontálisan az R-algoritmusok végrehajtását a Batch-készleteken.

A Batch feladatokat nagyobb, adatok átalakítását végző Azure-munkafolyamatok részeként is futtathatja az [Azure Data Factory](../data-factory/transform-data-using-dotnet-custom-activity.md) vagy hasonló eszközök felügyelete alatt.

## <a name="how-it-works"></a>Működés

Egy általános Batch-forgatókönyv a belsőleg párhuzamos munkák (például egy 3D jelenet képeinek renderelése) horizontális felskálázása a számítási csomópontok készletén. Ez a készlet lehet a "render Farm", amely tíz, több száz vagy akár több ezer magot biztosít a renderelési feladatokhoz.

Az alábbi diagram egy általános Batch-munkafolyamat lépéseit mutatja be, ahol az ügyfélalkalmazás vagy az üzemeltetett szolgáltatás Batch szolgáltatással futtat egy párhuzamos számítási feladatot.

![A Batch-megoldás lépéseinek ábrája.](./media/batch-technical-overview/tech_overview_03.png)

|Lépés  |Leírás  |
|---------|---------|
|1. Töltse fel a **bemeneti fájlokat** és az **alkalmazásokat** , és dolgozza fel ezeket a fájlokat az Azure Storage-fiókjába.     |A bemeneti fájlok az alkalmazás által feldolgozható bármilyen adatok lehetnek, például pénzügyi modellezési adatok vagy átkódolni kívánt videofájlok. Az alkalmazásfájlok az adatokat feldolgozó szkriptek vagy alkalmazások lehetnek, például adathordozó-átkódolók.|
|2. hozzon létre egy számítási csomópontokból álló batch- **készletet** a Batch-fiókban, egy **feladatot** a készleten futó munkaterhelés és a feladat **tevékenységeinek** futtatásához.     | A [számítási csomópontok](nodes-and-pools.md) azok a virtuális gépek, amelyek végrehajtják a [feladatokat](jobs-and-tasks.md). Adja meg a készlet tulajdonságait, például a csomópontok számát és méretét, egy Windows-vagy Linux-alapú virtuálisgép-rendszerképet, valamint egy alkalmazást, amelyet akkor kell telepíteni, amikor a csomópontok a készlethez csatlakoznak. Az [alacsony prioritású virtuális gépekkel](batch-low-pri-vms.md) vagy a csomópontok számának [automatikus méretezésével](batch-automatic-scaling.md) kezelheti a készlet költségeit és méretét. <br/><br/>Amikor tevékenységeket ad hozzá egy munkához, a Batch szolgáltatás automatikusan ütemezi a tevékenységeket a készletben lévő számítási csomópontokon. Mindegyik tevékenység a bemeneti fájlok feldolgozásához feltöltött alkalmazást használja. |
|3. **bemeneti fájlok** és **alkalmazások** kötegelt letöltése     |Az egyes feladatok végrehajtása előtt letöltheti azokat a bemeneti adatokat, amelyeket a hozzárendelt csomóponthoz fog feldolgozni. Ha az alkalmazás még nincs telepítve a készlet csomópontjain, helyette ide tölthető le. Amikor az Azure Storage-ból való letöltés befejeződött, a tevékenység lefut a hozzá rendelt csomóponton.|
|4. a **feladatok végrehajtásának** figyelése     |A tevékenységek futtatásakor lekérdezheti a Batch szolgáltatást a feladat és a tevékenységei állapotának figyeléséhez. Az Ön által készített ügyfélalkalmazások vagy szolgáltatások HTTPS használatával kommunikálhatnak a Batch szolgáltatással. Mivel előfordulhat, hogy számítási csomópontok ezrein futó több ezer tevékenységet kell figyelnie, gondoskodjon arról, hogy [hatékonyan kérdezi le a Batch szolgáltatást](batch-efficient-list-queries.md).|
|5. **feladat kimenetének** feltöltése     |A tevékenységek befejeződésekor a tevékenységek feltöltik eredményadataikat az Azure Storage-ba. Közvetlenül a számítási csomóponton lévő fájlrendszerből is lekérhet fájlokat.|
|6. **kimeneti fájlok** letöltése     |Amikor a megfigyelés észleli, hogy a feladat tevékenységei befejeződtek, az ügyfélalkalmazás vagy szolgáltatás letöltheti a kimeneti adatokat további feldolgozás céljából.|

Ne feledje, hogy a fent ismertetett munkafolyamat csak egy módszer a Batch használatára, és számos más funkció és lehetőség is rendelkezésre áll. Futtathat például [párhuzamosan több tevékenységet](batch-parallel-node-tasks.md) mindegyik számítási csomóponton, A feladatok [előkészítési és befejezési feladatait](batch-job-prep-release.md) felhasználva felkészítheti a csomópontokat a feladatokhoz, majd tisztítást végezhet.

Lásd: a [Batch szolgáltatás munkafolyamata és erőforrásai](batch-service-workflow-features.md) a készletek, csomópontok, feladatok és feladatok áttekintéséhez. Lásd továbbá a [Batch szolgáltatás legújabb frissítéseit](https://azure.microsoft.com/updates/?product=batch).

## <a name="in-region-data-residency"></a>Régión belüli adattárolás
Azure Batch nem helyezi át és nem tárolja az ügyféladatokat azon a régión kívül, amelyben üzembe van helyezve. 

## <a name="next-steps"></a>Következő lépések

Ismerkedjen az Azure Batch használatának első lépéseivel az alábbi rövid útmutatókkal:
- [Az első Batch-feladat futtatása az Azure CLI használatával](quick-create-cli.md)
- [Az első Batch-feladat futtatása az Azure Portal használatával](quick-create-portal.md)
- [Az első Azure Batch-feladat futtatása a .NET API használatával](quick-run-dotnet.md)
- [Az első Azure Batch-feladat futtatása a Python API használatával](quick-run-python.md)
