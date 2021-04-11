---
title: Honnan menthetők a & írási kísérlet fájljai
titleSuffix: Azure Machine Learning
description: Megtudhatja, hol mentheti a bemeneti és kimeneti fájljait a tárolási korlátozási hibák és a kísérletek késésének megakadályozása érdekében.
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
ms.openlocfilehash: ad641c2270f94b9d902a25e8d061fb1137a0cdb7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102518602"
---
# <a name="where-to-save-and-write-files-for-azure-machine-learning-experiments"></a>Fájlok mentése és írása Azure Machine Learning kísérletekhez


Ebből a cikkből megtudhatja, hová mentse a bemeneti fájlokat, és hová írja a kísérletek kimeneti fájljait, hogy megakadályozza a tárolási korlátokkal kapcsolatos hibákat és a kísérlet késését.

A képzés indításakor a [számítási célra](concept-compute-target.md)elkülönített környezetek el vannak különítve. Ennek a kialakításnak a célja a kísérlet reprodukálhatóságának és hordozhatóságának biztosítása. Ha ugyanazt a parancsfájlt kétszer futtatja, akkor ugyanazon vagy egy másik számítási célra ugyanazt az eredményt kapja. Ezzel a kialakítással a számítási célokat állapot nélküli számítási erőforrásokként kezelheti, amelyek mindegyike nem rendelkezik affinitással a befejezésük után futó feladatokhoz.

## <a name="where-to-save-input-files"></a>Honnan menthetők a bemeneti fájlok

Mielőtt kísérletet kezdeményezzen egy számítási célra vagy a helyi gépre, gondoskodnia kell arról, hogy a szükséges fájlok elérhetők legyenek a számítási cél számára, például a függőségi fájlok és az adatfájlok, amelyeket a kódnak futtatnia kell.

Azure Machine Learning a teljes forrás könyvtár másolásával futtatja a betanítási parancsfájlokat. Ha olyan bizalmas adatokkal rendelkezik, amelyeket nem szeretne felvenni, használja a [. ignore fájlt](how-to-save-write-experiment-files.md#storage-limits-of-experiment-snapshots) , vagy ne adja meg a forrás könyvtárában. Ehelyett egy [adattár](/python/api/azureml-core/azureml.data)használatával férhet hozzá az adataihoz.

A kísérletek pillanatképeinél a tárhelykorlát 300 MB és/vagy 2000 fájl.

Ezért javasoljuk, hogy:

* **Fájlok tárolása egy Azure Machine Learning [adattárban](/python/api/azureml-core/azureml.data).** Ez meggátolja a kísérletek késésével kapcsolatos problémákat, és a távoli számítási céltól származó adatok elérésének előnyeit biztosítja, ami azt jelenti, hogy a hitelesítést és a csatlakoztatást Azure Machine Learning kezeli. További információ az adattár forrásként való megadásáról és a fájlok az adattárba való feltöltéséről az [adattárolók című cikk hozzáférési adataiban](how-to-access-data.md) .

* **Ha csak pár adatfájlra és függőségi parancsfájlra van szüksége, és nem tud adattárt használni,** helyezze a fájlokat ugyanabba a mappába, mint a betanítási parancsfájlt. Adja meg ezt a mappát `source_directory` közvetlenül a betanítási parancsfájlban, vagy a betanítási parancsfájlt meghívó kódban.

<a name="limits"></a>

### <a name="storage-limits-of-experiment-snapshots"></a>A kísérleti Pillanatképek tárolási korlátai

A kísérletek esetében a Azure Machine Learning automatikusan létrehoz egy kísérletet a kód alapján a Futtatás konfigurálásakor javasolt címtár alapján. Ez a teljes korlátja 300 MB és/vagy 2000 fájl. Ha túllépi ezt a korlátot, a következő hibaüzenet jelenik meg:

```Python
While attempting to take snapshot of .
Your total snapshot size exceeds the limit of 300.0 MB
```

A hiba megoldásához tárolja a kísérlet fájljait egy adattárban. Ha nem tud adattárt használni, az alábbi táblázat a lehetséges alternatív megoldásokat kínálja.

Kísérlet &nbsp; leírása|Tárolási korlát megoldás
---|---
Kevesebb mint 2000 fájl & nem használhat adattárt| A pillanatképek maximális méretének felülbírálása <br> `azureml._restclient.snapshots_client.SNAPSHOT_MAX_SIZE_BYTES = 'insert_desired_size'`<br> Ez a fájlok számától és méretétől függően több percet is igénybe vehet.
Adott parancsfájl-könyvtárat kell használnia| [!INCLUDE [amlinclude-info](../../includes/machine-learning-amlignore-gitignore.md)]
Folyamat|Egy másik alkönyvtár használata az egyes lépésekhez
Jupyter-notebookok| Hozzon létre egy `.amlignore` fájlt, vagy helyezze át a jegyzetfüzetet egy új, üres, alkönyvtárba, és futtassa újra a kódot.

## <a name="where-to-write-files"></a>Hová kell írni a fájlokat

A betanítási kísérletek elkülönítése miatt a Futtatás során megjelenő fájlok módosításai nem feltétlenül maradnak meg a környezeten kívül. Ha a parancsfájl módosítja a helyi fájlokat a számításhoz, a módosítások nem maradnak meg a következő kísérlet futtatásakor, és a rendszer nem továbbítja automatikusan az ügyfélszámítógépre. Ezért az első kísérlet során végrehajtott módosítások nem működnek, és nem érinthetik a másodikban.

A módosítások írásakor ajánlott fájlokat írni egy Azure Machine Learning adattárba. Lásd [Az adattárolók adatainak elérését](how-to-access-data.md)ismertető témakört.

Ha nincs szüksége adattárra, írja a fájlokat a `./outputs` és/vagy `./logs` mappába.

>[!Important]
> Két mappa, *kimenet* és *napló*, a Azure Machine learning speciális kezelést kap. Ha a betanítás során fájlokat `./outputs` és mappákat ír `./logs` , a fájlok automatikusan feltöltve lesznek a futtatási előzményekbe, hogy a Futtatás befejezése után hozzáférjenek hozzájuk.

* **A kimenetek, például állapotüzenetek vagy pontozási eredmények esetén** fájlokat írhat a `./outputs` mappába, így azok a futtatási előzményekben szereplő összetevőkként megmaradnak. Legyen szem előtt tartva a mappába írt fájlok számát és méretét, mivel a tartalom a futtatási előzményekbe való feltöltésekor késést eredményezhet. Ha a késés aggodalomra ad okot, ajánlott a fájlok adattárba való írása.

* **Ha az írott fájlt naplófájlként szeretné menteni a futtatási előzményekben,** írja a fájlokat a `./logs` mappába. A naplók valós időben lesznek feltöltve, így ez a módszer alkalmas az élő frissítések távoli futtatásból való továbbítására.

## <a name="next-steps"></a>Következő lépések

* További információ az [adattárolók adatainak eléréséről](how-to-access-data.md).

* További információ a [számítási célok létrehozásáról a modell betanításához és üzembe helyezéséhez](how-to-create-attach-compute-studio.md)