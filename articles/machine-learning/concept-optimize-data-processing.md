---
title: Adatfeldolgozás optimalizálása
titleSuffix: Azure Machine Learning
description: Ismerje meg az adatfeldolgozási sebességek optimalizálásának ajánlott eljárásait, valamint azt, hogy milyen integrációs Azure Machine Learning támogatja az adatfeldolgozást a skálán.
services: machine-learning
ms.service: machine-learning
ms.author: sgilley
author: sdgilley
ms.subservice: core
ms.reviewer: nibaccam
ms.topic: conceptual
ms.date: 06/26/2020
ms.custom: data4ml
ms.openlocfilehash: 5ab7bac635a0b670087800212727b0d2e2b96934
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103472206"
---
# <a name="optimize-data-processing-with-azure-machine-learning"></a>Az adatfeldolgozás optimalizálása Azure Machine Learning

Ebből a cikkből megismerheti az adatfeldolgozási sebesség helyi és nagy méretekben történő optimalizálását segítő ajánlott eljárásokat.

A Azure Machine Learning nyílt forráskódú csomagokkal és adatfeldolgozási keretrendszerekkel van integrálva. Ezen integrációk használatával és a jelen cikkben ajánlott eljárási javaslatok alkalmazásával az adatfeldolgozási sebességet helyileg és méretben is javíthatja.

## <a name="parquet-and-csv-file-formats"></a>A parketta és a CSV-fájlformátumok

A vesszővel tagolt (CSV) fájlok az adatfeldolgozáshoz gyakran használt fájlformátumok. A parketta-fájlformátumokat azonban a gépi tanulási feladatokhoz ajánlott használni.

A [Parquet-fájlok](https://parquet.apache.org/) bináris oszlopos formátumban tárolják az adattárolást. Ez a formátum akkor hasznos, ha több fájlra is szükség van az adatmegosztásra. Emellett ez a formátum lehetővé teszi a gépi tanulási kísérletek megfelelő mezőinek megcélzását. Ahelyett, hogy a 20 GB-nyi adatfájlt kellene beolvasnia, csökkentheti az adatterhelést, ha kiválasztja a szükséges oszlopokat a ML-modell betanításához. A Parquet-fájlok a feldolgozási teljesítmény csökkentése érdekében is tömöríthetők, és kevesebb helyet foglalnak el.

A CSV-fájlokat általában az adatimportáláshoz és-exportáláshoz használják, mivel ezek könnyen szerkeszthetők és olvashatók az Excelben. A CSV tárolt adatok karakterláncként vannak tárolva, és a fájlok tömörítve lehetnek az adatátviteli terhelések csökkentése érdekében. A tömörítetlen CSV a 2-10-es és a tömörített CSV-faktorral bővíthető, még tovább növelheti. Így a memóriában lévő 5 GB-os CSV-fájl a gépen található 8 GB RAM-ra is kiterjed. Ez a tömörítési viselkedés növelheti az adatátviteli késést, ami nem ideális, ha nagy mennyiségű adattal dolgoz fel. 

## <a name="pandas-dataframe"></a>Panda dataframe

A [pandák dataframes](https://pandas.pydata.org/pandas-docs/stable/getting_started/overview.html) általában adatkezeléshez és elemzéshez használják. `Pandas` az 1 GB-nál kisebb adatméretek esetében is jól működik, de a dataframes feldolgozási ideje `pandas` lelassul, ha a fájlméretek körülbelül 1 GB-ot érnek el. Ez a lassulás azért van, mert a tárolóban tárolt adatai mérete nem egyezik meg a dataframe lévő adatmérettel. Például a CSV-fájlokban lévő adat akár 10 alkalommal is kiterjeszthető egy dataframe, így egy 1 GB-os CSV-fájl 10 GB-nyi lehet dataframe.

`Pandas` egyszálas, ami azt jelenti, hogy a műveleteket egyetlen PROCESSZORon, egyszerre hajtják végre. Egyszerűen integrálással a számítási feladatokat több virtuális processzorra egyetlen Azure Machine Learning számítási példányon olyan csomagokkal, mint például [](https://modin.readthedocs.io/en/latest/) az `Pandas` elosztott háttérrendszer használatával burkolt Modin.

Ahhoz, hogy integrálással a feladatait `Modin` és [Dask](https://dask.org), egyszerűen módosítsa a kód ezen sorát a következőre: `import pandas as pd` `import modin.pandas as pd` .

## <a name="dataframe-out-of-memory-error"></a>Dataframe: memórián kívüli hiba 

Általában *kevés a memóriabeli* hiba, ha a dataframe a GÉPEN elérhető RAM fölé terjeszkedik. Ez a fogalom a (z) vagy rendszerhez hasonló elosztott keretrendszerre is vonatkozik `Modin` `Dask` .  Ez azt eredményezi, hogy a művelet megkísérli betölteni a dataframe a fürt minden csomópontján, de nem áll rendelkezésre elegendő memória.

Az egyik megoldás az, hogy növelje a RAM-ot a memóriában lévő dataframe. Javasoljuk, hogy a számítási méret és a feldolgozási teljesítmény a RAM méretének kétszeresét tartalmazzon. Tehát ha a dataframe 10 GB, használjon legalább 20 GB RAM-mal rendelkező számítási célt, hogy a dataframe kényelmesen illeszkedjen a memóriához, és feldolgozza azokat. 

Több virtuális processzor esetében ne feledje, hogy egy partíciónak kényelmesen el kell férnie az egyes vCPU vCPU a gépen. Azaz ha 16 GB RAM 4 vCPU van, akkor minden egyes vCPU 2 GB-dataframes szeretne.

### <a name="local-vs-remote"></a>Helyi és távoli

Előfordulhat, hogy bizonyos Panda dataframe-parancsok gyorsabban működnek, amikor a helyi számítógépen dolgozik, és egy Azure Machine Learning-vel kiépített távoli virtuális gépet. A helyi számítógép általában engedélyezve van egy lapozófájl, amely lehetővé teszi, hogy a fizikai memóriához képest nagyobb terhelést biztosítson, ami a merevlemez kiterjesztéseként használható. Jelenleg Azure Machine Learning a virtuális gépek lapozófájl nélkül futnak, ezért csak annyi adatmennyiséget tud betölteni, amennyit a fizikai RAM elérhetővé vált. 

A számítási feladatokhoz ajánlott nagyobb virtuális gépet választani a feldolgozási sebesség javítása érdekében.

További információ a Azure Machine Learning [elérhető virtuálisgép-sorozatáról és méretéről](concept-compute-target.md#supported-vm-series-and-sizes) . 

A RAM-specifikációk esetében tekintse meg a megfelelő virtuálisgép-adatsorozat-lapokat, például:, [Dv2-Dsv2 sorozat](../virtual-machines/dv2-dsv2-series-memory.md) vagy [NC sorozat](../virtual-machines/nc-series.md).

### <a name="minimize-cpu-workloads"></a>CPU-munkaterhelések csökkentése

Ha nem ad hozzá több RAM memóriát a géphez, a következő módszerekkel csökkentheti a processzor-munkaterheléseket, és optimalizálhatja a feldolgozási időt. Ezek az ajánlások az önálló és az elosztott rendszerekre egyaránt vonatkoznak.

Módszer | Leírás
----|----
Tömörítés | Az adataihoz eltérő ábrázolást használhat, így kevesebb memóriát használ, és nem befolyásolja jelentősen a számítás eredményét.<br><br>*Példa:* Ahelyett, hogy a bejegyzéseket karakterláncként, körülbelül 10 bájt vagy több bejegyzést tartalmazó sztringként tárolja, tárolja őket logikai, igaz vagy hamis értékként, amelyet 1 bájtban tárolhat.
Dobogás | Betöltheti a memóriába az adathalmazokat (darabokat), az adathalmazok feldolgozását, vagy párhuzamosan több részhalmazt is. Ez a módszer akkor működik a legjobban, ha fel kell dolgoznia az összes adatmennyiséget, de egyszerre nem kell betölteni az összes adatmennyiséget a memóriába. <br><br>*Példa:* A teljes éves adatmennyiséget egyszerre csak egy hónappal kell betölteni és feldolgozni.
Indexelés | Alkalmazzon és használjon egy összegzést, amely megadja, hogy hol találhatóak az Ön számára fontos információk. Az indexelés akkor hasznos, ha csak az adat egy részhalmazát kell használnia a teljes készlet helyett<br><br>*Példa:* Ha a teljes éves értékesítési adatmennyiséget havi sorrendbe rendezi, az index segítségével gyorsan megkeresheti a kívánt hónapot, amelyet fel szeretne dolgozni.

## <a name="scale-data-processing"></a>Az adatfeldolgozás skálázása

Ha az előző javaslatok nem elégek, és nem tud beolvasni egy olyan virtuális gépet, amely megfelel az adatainak, 

* Használjon hasonló keretrendszert `Spark` , vagy `Dask` dolgozza fel az adatmennyiséget a memóriából. Ebben a beállításban a dataframe a partíció és a feldolgozás alatt lévő RAM-partícióba töltődik be, és a végeredmény a végén gyűlik össze.  

* Elosztott keretrendszer használatával bővítheti a fürtöket. Ebben a beállításban az adatfeldolgozási terhelések felosztása és feldolgozása több, párhuzamosan működő processzoron történik, az utolsó eredmény pedig a végén gyűlik össze.

### <a name="recommended-distributed-frameworks"></a>Ajánlott elosztott keretrendszerek

Az alábbi táblázat a kód-preferencia vagy az adatméret alapján Azure Machine Learningba integrált elosztott keretrendszerek használatát javasolja.

Élmény vagy adatméret | Ajánlás
------|------
Ha már ismeri a `Pandas`| `Modin` vagy `Dask` dataframe
Ha szeretné `Spark` | `PySpark`
1 GB-nál kisebb adatmennyiség esetén | `Pandas` helyi **vagy** távoli Azure Machine learning számítási példány
10 GB-nál nagyobb adatmennyiség esetén| Áthelyezés fürtre a `Ray` , `Dask` , vagy rendszer használatával `Spark`

## <a name="next-steps"></a>Következő lépések

* [Adatfeldolgozási lehetőségek Azure Machine Learningsal](concept-data-ingestion.md).
* [Adatkészletek létrehozása és regisztrálása](how-to-create-register-datasets.md).
