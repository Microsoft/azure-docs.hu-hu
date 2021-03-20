---
title: Adatfeldolgozási & Automation
titleSuffix: Azure Machine Learning
description: Ismerje meg a gépi tanulási modellek betanításához rendelkezésre álló adatfeldolgozási lehetőségek előnyeit és hátrányait.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 02/26/2020
ms.custom: devx-track-python, data4ml
ms.openlocfilehash: a096375e32e3d8a6760da88fe5ec86a70d364aff
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98872095"
---
# <a name="data-ingestion-options-for-azure-machine-learning-workflows"></a>Adatfeldolgozási lehetőségek Azure Machine Learning munkafolyamatokhoz

Ebből a cikkből megtudhatja, milyen előnyökkel és hátrányokkal érheti el az adatfeldolgozási lehetőségeket a Azure Machine Learning. 

A következő lehetőségek közül választhat:
+ [Azure Data Factory](#azure-data-factory) folyamatok, amelyek kifejezetten az adatok kinyerésére, betöltésére és átalakítására készültek

+ [Azure Machine learning PYTHON SDK](#azure-machine-learning-python-sdk)-t, amely egyéni kódot biztosít az adatfeldolgozási feladatokhoz.

+ a kettő kombinációja

Az adatfeldolgozás az a folyamat, amelyben a strukturálatlan adatok kinyerése egy vagy több forrásból történik, majd a gépi tanulási modellek előkészítése. Az is időigényes, különösen ha kézzel történik, és ha nagy mennyiségű adattal rendelkezik több forrásból. A tevékenység automatizálása felszabadítja az erőforrásokat, és gondoskodik arról, hogy a modellek a legfrissebb és a vonatkozó adatait használják.

## <a name="azure-data-factory"></a>Azure Data Factory

A [Azure Data Factory](../data-factory/introduction.md) natív támogatást nyújt az adatfeldolgozási folyamatokhoz használt adatforrás-figyeléshez és-eseményindítóhoz.  

Az alábbi táblázat összefoglalja az adatfeldolgozási munkafolyamatok Azure Data Factory használatának előnyeit és hátrányait.

|Előnyök|Hátrányok
---|---
Kifejezetten az adatok kinyerésére, betöltésére és átalakítására készült.|A jelenleg korlátozott számú Azure Data Factory folyamattal kapcsolatos feladatot biztosít 
Lehetővé teszi, hogy adatvezérelt munkafolyamatokat hozzon létre az adatáthelyezés és az átalakítások méretezése érdekében.|Költséges a létrehozás és a karbantartás. További információért tekintse meg Azure Data Factory [díjszabási lapját](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/) .
Különböző Azure-eszközökkel integrált, például [Azure Databricks](../data-factory/transform-data-using-databricks-notebook.md) és [Azure functions](../data-factory/control-flow-azure-function-activity.md) | A parancsfájlok nem futnak natív módon, hanem külön számítási feladatokra támaszkodnak a parancsfájlok futtatásához. 
Natív módon támogatja az adatforráshoz indított adatfeldolgozást| 
Az adatelőkészítési és-modell-betanítási folyamatok elkülönül.|
Beágyazott adatbányászati képesség Azure Data Factory adatfolyamok|
Alacsony kódú [felhasználói felületet](../data-factory/quickstart-create-data-factory-portal.md) biztosít a nem parancsfájl-kezelési módszerekhez |

Ezek a lépések és a következő ábra a Azure Data Factory adatfeldolgozási munkafolyamatát szemlélteti.

1. Az adatok lekérése a forrásaiból
1. Alakítsa át és mentse az adatokat egy kimeneti blob-tárolóba, amely adattárolóként szolgál Azure Machine Learning
1. Az előkészített adattárolással a Azure Data Factory folyamat meghívja a képzési Machine Learning folyamatot, amely a modell betanításához felkészített adatfeldolgozást fogadja.


    ![ADF-adatfeldolgozás](media/concept-data-ingestion/data-ingest-option-one.svg)
    
Megtudhatja, hogyan hozhat létre adatfeldolgozási folyamatot a [Azure Data Factory](how-to-data-ingest-adf.md)Machine Learninghoz.

## <a name="azure-machine-learning-python-sdk"></a>Python SDK Azure Machine Learning 

A [PYTHON SDK](/python/api/overview/azure/ml)-val beépítheti az adatfeldolgozási feladatokat egy [Azure Machine learning folyamat](./how-to-create-machine-learning-pipelines.md) lépésbe.

Az alábbi táblázat összefoglalja az SDK-t és az adatfeldolgozási feladatokhoz szükséges ML folyamatokat.

Előnyök| Hátrányok
---|---
Saját Python-parancsfájlok konfigurálása | A nem támogatja natív módon az adatforrás módosításának aktiválását. Logic app-vagy Azure-függvények megvalósításához szükséges
Az adatelőkészítés az összes modell betanításának részeként|Fejlesztési ismereteket igényel az adatfeldolgozási parancsfájlok létrehozásához
Támogatja az adatelőkészítési parancsfájlokat különböző számítási céloknál, beleértve a [Azure Machine learning számítást](concept-compute-target.md#azure-machine-learning-compute-managed) is |Nem biztosít felhasználói felületet a betöltési mechanizmus létrehozásához.

A következő ábrán a Azure Machine Learning folyamat két lépésből áll: az adatfeldolgozás és a modell betanítása. Az adatfeldolgozási lépés magában foglalja a Python-kódtárak és a Python SDK használatával elvégezhető feladatokat, például az adatok helyi/webes forrásokból való kinyerését, valamint az adatok átalakítását, például a hiányzó imputálási. A betanítási lépés ezután az előkészített adatokat bemenetként használja a betanítási szkriptnek a gépi tanulási modell betanításához. 

![Azure-folyamat + SDK-adatfeldolgozás](media/concept-data-ingestion/data-ingest-option-two.png)

## <a name="next-steps"></a>Következő lépések

Kövesse az alábbi útmutatókat:
* [Adatfeldolgozási folyamat összeállítása Azure Data Factory](how-to-data-ingest-adf.md)

* [Az Azure-folyamatokkal automatizálhatja és kezelheti az adatfeldolgozási folyamatokat](how-to-cicd-data-ingestion.md).