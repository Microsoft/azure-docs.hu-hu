---
title: A gazdagép operációs rendszerének frissítése számítási fürthöz és példányhoz
titleSuffix: Azure Machine Learning
description: Frissítse a számítási fürt és a számítási példány gazda operációs rendszerét az Ubuntu 16.04 LTS-ről a 18.04 LTS-re.
services: machine-learning
author: nishankgu
ms.author: nigup
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.date: 03/03/2021
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 7445de8349d025679b1560e065ed15d9eec3b08f
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107872010"
---
# <a name="upgrade-compute-instance-and-compute-cluster-host-os"></a>A számítási példány és a számításifürt-gazdagép operációs rendszerének frissítése

Azure Machine Learning számítási __fürt és__ __számítási példány felügyelt__ számítási infrastruktúra. Felügyelt szolgáltatásként a Microsoft felügyeli a gazdagép operációs rendszerét, valamint a telepített csomagokat és szoftververziókat.

A számítási fürt és a számítási példány gazda operációs rendszerének verziója Ubuntu 16.04 LTS volt. **2021. április 30-án az** Ubuntu befejezi a 16.04 támogatását. __2021. március 15-től__ kezdve a Microsoft automatikusan Ubuntu 18.04 LTS-re frissíti a gazda operációs rendszert. A 18.04-es frissítés biztosítja az Ubuntu-közösség folyamatos biztonsági frissítéseit és támogatását. Ez a frissítés több Azure-régióban lesz elérhető, és __2021.__ április 9-ig minden régióban elérhető lesz. Az Ubuntu 16.04-es verzióra való támogatásának befejezéséről további információt az [Ubuntu kiadási blogjában talál.](https://wiki.ubuntu.com/Releases)

> [!TIP]
> * A gazda operációs rendszer nem az az operációs rendszer verziója, amely megadható a [környezethez](how-to-use-environments.md) a modell betanítása vagy üzembe helyezése során. A környezetek a Dockerben futnak. A Docker a gazda operációs rendszeren fut.
> * Ha jelenleg Ubuntu 16.04-alapú környezeteket használ a betanításhoz vagy az üzembe helyezéshez, a Microsoft azt javasolja, hogy váltson Ubuntu 18.04-alapú rendszerképekre. További információ: [Környezetek](how-to-use-environments.md) használata és a Azure Machine Learning [tárolók adattárak.](https://github.com/Azure/AzureML-Containers/tree/master/base)
> * Ha Ubuntu 18.04 Azure Machine Learning alapuló számítási példányt használ, az alapértelmezett _Python-verzió a Python 3.8._
## <a name="creating-new-resources"></a>Új erőforrások létrehozása

A __2021. április 9.__ után létrehozott számítási fürtök vagy számítási példányok alapértelmezés szerint az Ubuntu 18.04 LTS-t használják gazda operációs rendszerként. Nem választhat másik gazda operációs rendszert.

## <a name="upgrade-existing-resources"></a>Meglévő erőforrások frissítése

Ha __2021. március 15.__ előtt létrehozott meglévő számítási fürttel vagy számítási példányokkal rendelkezik, akkor meg kell bizonyosodni a gazdagép operációs rendszerének Ubuntu 18.04-es verzióra való frissítéséhez. Attól függően, hogy Azure Machine Learning __2021. április 9.,__ után milyen régióból fér hozzá a változásokhoz:

* __Azure Machine Learning fürt létrehozása:__

    * Ha a fürt minimális __csomópontok = 0__ értékekkel van konfigurálva, automatikusan frissül, amikor az összes feladat befejeződött, és nulla csomópontra csökken.
    * Ha __a minimális csomópontok > 0,__ ideiglenesen módosítsa a minimális csomópontokat nullára, és hagyja, hogy a fürt nullára csökkentse a csomópontok számát.

    A minimális csomópontok módosításával kapcsolatos további információkért tekintse meg az [az ml computetarget update amlcompute](https://docs.microsoft.com/cli/azure/ml/computetarget/update#az_ml_computetarget_update_amlcompute) Azure CLI-parancsot vagy az [AmlCompute.update()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.compute.amlcompute.amlcompute#update-min-nodes-none--max-nodes-none--idle-seconds-before-scaledown-none-) SDK-referenciát.

* __Azure Machine Learning számítási példány:__ Hozzon létre egy új számítási példányt (amely Ubuntu 18.04-et fog használni), és törölje a régi példányt.

    * A munkaterület fájlmegosztásában, adattárában és adattárában tárolt jegyzetfüzetek elérhetők lesznek az új számítási példányról.
    * Ha egyéni Conda-környezeteket hozott létre, exportálhatja ezeket a környezeteket a meglévő példányból, és importálhatja az új példányra. A Conda exportálásával és importálásával kapcsolatos információkért lásd a [Conda dokumentációját a](https://docs.conda.io/) docs.conda.io.

    További információ: [Mi](concept-compute-instance.md) az a számítási példány? és Azure Machine Learning létrehozása és [kezelése](how-to-create-manage-compute-instance.md)

## <a name="check-host-os-version"></a>A gazdagép operációsrendszer-verziójának ellenőrzése

A gazda operációs rendszer verziójának ellenőrzésére vonatkozó információkért tekintse meg az Ubuntu community wikioldalt az [Ubuntu-verzió ellenőrzéséhez.](https://help.ubuntu.com/community/CheckingYourUbuntuVersion)

> [!TIP]
> A wiki parancsának a használatával terminál-munkamenetet is `lsb_release -a` [használhat egy számítási példányon.](how-to-access-terminal.md)
## <a name="next-steps"></a>Következő lépések

Ha további kérdései vagy aggályai vannak, lépjen kapcsolatba velünk a következő elérhetőségen: [ubuntu18azureml@service.microsoft.com](mailto:ubuntu18azureml@service.microsoft.com) .
