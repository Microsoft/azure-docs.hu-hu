---
title: A gazdagép operációs rendszerének frissítése a számítási fürthöz és a példányhoz
titleSuffix: Azure Machine Learning
description: Frissítse a gazdagép operációs rendszerét a számítási fürt és a számítási példány számára az Ubuntu 16,04 LTS és a 18,04 LTS között.
services: machine-learning
author: nishankgu
ms.author: nigup
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.date: 03/03/2021
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 710a860b1ed87f176b6f42b4963dad17acb323b1
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "104954054"
---
# <a name="upgrade-compute-instance-and-compute-cluster-host-os"></a>Számítási példány és számítási fürt operációs rendszerének frissítése

Azure Machine Learning __számítási fürt__ és __számítási példány__ felügyelt számítási infrastruktúra. Felügyelt szolgáltatásként a Microsoft felügyeli a gazdagép operációs rendszerét, valamint a telepített csomagokat és szoftver-verziókat.

A számítási fürt és a számítási példány gazdagép operációs rendszere Ubuntu 16,04 LTS. Az Ubuntu **2021 április 30-** án véget ér a 16,04-es támogatásnak. A Microsoft a __2021. március 15__-én kezdődően automatikusan frissíti a gazdagép operációs rendszerét az Ubuntu 18,04 LTS-re. Az 18,04-es verzióra való frissítés az Ubuntu-Közösségtől származó folyamatos biztonsági frissítések és támogatás biztosítását teszi lehetővé. Ez a frissítés az Azure-régiókban lesz kivezetve, és minden régióban elérhető lesz a __2021. április 09-__ én. Az Ubuntu 16,04-es befejezési támogatásáról az [Ubuntu kiadási blogjában](https://wiki.ubuntu.com/Releases)olvashat bővebben.

> [!TIP]
> * A gazda operációs rendszer nem az operációsrendszer-verzió, amelyet a [környezet](how-to-use-environments.md) betanítása vagy üzembe helyezése során adhat meg. A környezetek a Docker-ben futnak. A Docker a gazdagép operációs rendszerén fut.
> * Ha jelenleg Ubuntu 16,04-alapú környezetet használ képzéshez vagy üzembe helyezéshez, a Microsoft javasolja, hogy az Ubuntu 18,04-alapú rendszerképeket használja. További információ: a [környezetek](how-to-use-environments.md) és a [Azure Machine learning tárolók tárházának](https://github.com/Azure/AzureML-Containers/tree/master/base)használata.
> * Ubuntu 18,04-alapú számítási példány Azure Machine Learning használatakor az alapértelmezett Python-verzió a _python 3,8_.
## <a name="creating-new-resources"></a>Új erőforrások létrehozása

Az __2021. április 9__ . után létrehozott számítási fürt vagy számítási példányok alapértelmezés szerint az Ubuntu 18,04 LTS-et használják gazda operációs rendszerként. Nem választhat másik gazdagép-operációs rendszert.

## <a name="upgrade-existing-resources"></a>Meglévő erőforrások frissítése

Ha már van olyan számítási fürt vagy számítási példány, amelyet a __2021. március 15__. előtt hozott létre, lépéseket kell tennie a gazdagép operációs rendszerének az Ubuntu 18,04-re való frissítéséhez. Attól függően, hogy milyen régióhoz fér hozzá Azure Machine Learning, javasoljuk, hogy __2021. április 09.__ után végezze el ezeket a műveleteket, hogy a módosítások minden régióban elérhetők legyenek:

* __Azure Machine learning számítási fürt__:

    * Ha a fürt a __minimális csomópontok = 0__ értékkel van konfigurálva, akkor a rendszer automatikusan frissíti az összes feladat befejeződését, és csökkenti a nulla csomópontot.
    * Ha a minimális __csomópontok értéke > 0__, átmenetileg módosítsa a minimum csomópontokat nulla értékre, és engedélyezze, hogy a fürt csökkentse a nulla csomópontokat.

    A minimális csomópontok módosításával kapcsolatos további információkért tekintse meg az az [ml computetarget Update amlcompute](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/update#ext_azure_cli_ml_az_ml_computetarget_update_amlcompute) Azure CLI-parancsot vagy a [amlcompute. Update ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#update-min-nodes-none--max-nodes-none--idle-seconds-before-scaledown-none-) SDK-referenciát.

* __Azure Machine learning számítási példány__: hozzon létre egy új számítási példányt (amely az Ubuntu 18,04-et fogja használni), és törölje a régi példányt.

    * A munkaterület-fájlmegosztás, adattárak és adatkészletek által tárolt jegyzetfüzetek az új számítási példányból lesznek elérhetők.
    * Ha egyéni Conda-környezeteket hozott létre, akkor exportálhatja ezeket a környezeteket a meglévő példányból, és importálhatja az új példányon. Az exportálási és importálási Conda kapcsolatos információkért lásd a [Conda dokumentációját](https://docs.conda.io/) a következő címen: docs.Conda.IO.

    További információ: [Mi a számítási példány](concept-compute-instance.md) , és hogyan [hozható létre és kezelhető egy Azure Machine learning számítási példány](how-to-create-manage-compute-instance.md) cikkei

## <a name="check-host-os-version"></a>Gazdagép operációsrendszer-verziójának keresése

A gazda operációs rendszer verziójának ellenőrzésével kapcsolatos információkért tekintse meg az Ubuntu Community wikit az [Ubuntu-verzió ellenőrzésével](https://help.ubuntu.com/community/CheckingYourUbuntuVersion)foglalkozó oldalon.

> [!TIP]
> A `lsb_release -a` parancs wikiből való használatához a [terminál-munkamenetet használhatja egy számítási példányon](how-to-access-terminal.md).
## <a name="next-steps"></a>Következő lépések

Ha további kérdése vagy problémája van, lépjen kapcsolatba velünk a következő címen: [ubuntu18azureml@service.microsoft.com](mailto:ubuntu18azureml@service.microsoft.com) .
