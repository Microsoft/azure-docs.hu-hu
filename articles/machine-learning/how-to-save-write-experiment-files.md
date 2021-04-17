---
title: A kísérlet fájljainak & mentésének a hely
titleSuffix: Azure Machine Learning
description: Megtudhatja, hová mentheti a bemeneti és kimeneti fájlokat a tárhelykorlátozási hibák és a kísérlet késésének elkerülése érdekében.
services: machine-learning
author: rastala
ms.author: roastala
manager: danielsc
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.date: 03/10/2020
ms.openlocfilehash: 59955b291ce706a77d0dd5ab052809fe725166d9
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107387887"
---
# <a name="where-to-save-and-write-files-for-azure-machine-learning-experiments"></a>Fájlok mentése és írása a Azure Machine Learning kísérletekhez


Ebből a cikkből megtudhatja, hol mentheti a bemeneti fájlokat, és hová írhatja a kimeneti fájlokat a kísérletekből a tárhelykorlát hibáinak és a kísérletek késésének elkerülése érdekében.

A betanítás egy számítási [célon való elindításakor](concept-compute-target.md)el vannak különítve a külső környezettől. Ennek a kialakításnak az a célja, hogy biztosítsa a kísérlet reprodukálhatóságát és hordozhatóságát. Ha ugyanazt a szkriptet kétszer futtatja ugyanazon vagy egy másik számítási célon, ugyanezeket az eredményeket kapja. Ezzel a kialakítással állapot nélküli számítási erőforrásokként kezelheti a számítási célokat, amelyek között nincs affinitás azokkal a feladatokkal, amelyek a futtatásukat követően futnak.

## <a name="where-to-save-input-files"></a>Bemeneti fájlok mentésének helye

Mielőtt kísérletet kezdeményezhet egy számítási célon vagy a helyi gépen, győződjön meg arról, hogy a szükséges fájlok elérhetők az adott számítási cél számára, például függőségi fájlokat és adatfájlokat, amelyek futtatására a kódnak szüksége van.

Azure Machine Learning a teljes forráskönyvtár másolása által futtatja a betanító szkripteket. Ha bizalmas adatokat nem szeretne feltölteni, használjon [.ignore](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) fájlt, vagy ne foglalja bele őket a forráskönyvtárba. Ehelyett egy adattár használatával férhet hozzá [az adatokhoz.](/python/api/azureml-core/azureml.data)

A kísérletek pillanatképeinél a tárhelykorlát 300 MB és/vagy 2000 fájl.

Ezért a következőket javasoljuk:

* **A fájlok tárolása egy Azure Machine Learning [adatkészletben.](/python/api/azureml-core/azureml.data)** Ez megakadályozza a kísérletek késésével kapcsolatos problémákat, és az adatok távoli számítási célról való elérésének előnyeivel rendelkezik, ami azt jelenti, hogy a hitelesítést és a csatlakoztatást a Azure Machine Learning. A Betanítás adatkészletekkel használatával további információt olvashat arról, hogyan adhatja meg az adatkészletet bemeneti adatforrásként a [betanító szkriptben.](how-to-train-with-datasets.md)

* **Ha csak néhány adatfájlra** és függőségi szkriptre van szüksége, és nem tud adattárat használni, helyezze a fájlokat a betanító szkripttel azonos könyvtárba. Ezt a mappát adja meg közvetlenül a betanító szkriptben vagy a betanító szkriptet `source_directory` behívó kódban.

<a name="limits"></a>

### <a name="storage-limits-of-experiment-snapshots"></a>A kísérleti pillanatképek tárolási korlátai

Kísérletekhez a Azure Machine Learning automatikusan pillanatképet készít a kódról a futtatás konfigurálásakor javasolt könyvtár alapján. A maximális korlát 300 MB és/vagy 2000 fájl. Ha túllépi ezt a korlátot, a következő hibaüzenet jelenik meg:

```Python
While attempting to take snapshot of .
Your total snapshot size exceeds the limit of 300.0 MB
```

A hiba elhárításához tárolja a kísérlet fájljait egy adattárban. Ha nem tud adattárat használni, az alábbi táblázatban lehetséges alternatív megoldásokat talál.

Kísérlet &nbsp; leírása|Tárolási korlát megoldás
---|---
Kevesebb mint 2000 & nem használhat adattárat| A pillanatkép méretkorlátának felülbírálása a sel <br> `azureml._restclient.snapshots_client.SNAPSHOT_MAX_SIZE_BYTES = 'insert_desired_size'`<br> Ez a fájlok számától és méretétől függően több percig is eltarthat.
Adott szkriptkönyvtárat kell használnia| [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]
Folyamat|Minden lépéshez használjon egy másik alkönyvtárat
Jupyter-notebookok| Hozzon `.amlignore` létre egy fájlt, vagy helyezze át a jegyzetfüzetet egy új, üres alkönyvtárba, és futtassa újra a kódot.

## <a name="where-to-write-files"></a>Fájlok írásának helye

A betanítási kísérletek elkülönítése miatt a futtatás során végrehajtott fájlok módosításai nem feltétlenül maradnak meg a környezeten kívül. Ha a szkript helyi számításra módosítja a fájlokat, a módosítások nem maradnak meg a következő kísérlet futtatásakor, és nem propagálódnak vissza automatikusan az ügyfélszámítógépre. Ezért az első kísérlet futtatása során végrehajtott módosítások nincsenek hatással a másodikban végrehajtottakra, és nem is lehetnek rá hatással.

A módosítások írásakor javasoljuk, hogy fájlokat írjon a tárolóba egy Azure Machine Learning [outputFileDatasetConfig objektummal.](/python/api/azureml-core/azureml.data.output_dataset_config.outputfiledatasetconfig) Lásd: [OutputFileDatasetConfig létrehozása.](how-to-train-with-datasets.md#where-to-write-training-output)

Egyéb esetben írjon fájlokat az `./outputs` and/or `./logs` mappába.

>[!Important]
> Két mappa, *kimenetek* és *naplók,* speciális kezelést kapnak a Azure Machine Learning. A betanítás során, amikor fájlokat ír a és mappákba, a rendszer automatikusan feltölti a fájlokat a futtatás előzményeibe, így a futtatás befejezése után hozzáférhet `./outputs` `./logs` hozzájuk.

* **Az olyan kimenetek esetén,** mint az állapotüzenetek vagy a pontozási eredmények, írjon fájlokat a mappába, hogy a futtatás előzményeiben összetevőkként `./outputs` megmaradnak. A mappába írt fájlok számát és méretét is szem előtt kell tartani, mivel a késés akkor fordulhat elő, ha a tartalmat feltöltik a futtatás előzményeibe. Ha a késés problémát okoz, javasoljuk, hogy írjon fájlokat egy adattárba.

* **Ha az írott fájlokat naplókként menteni a futtatás előzményeiben, írjon** fájlokat a `./logs` mappába. A naplók valós időben vannak feltöltve, így ez a módszer alkalmas az élő frissítések távoli futtatásból történő streamelésekor.

## <a name="next-steps"></a>Következő lépések

* További információ az [adatok tárolóból való eléréséről.](how-to-access-data.md)

* További információ a [számítási célok létrehozásáról a modellek betanításában és üzembe helyezésében](how-to-create-attach-compute-studio.md)