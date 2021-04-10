---
title: A Azure Stream Analytics előzetes verziójának funkciói
description: Ez a cikk a jelenleg előzetes verzióban elérhető Azure Stream Analytics-szolgáltatásokat sorolja fel
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/16/2021
ms.openlocfilehash: 55745c022038fa85f5b114f2bc347ed7292665eb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104589650"
---
# <a name="azure-stream-analytics-preview-features"></a>A Azure Stream Analytics előzetes verziójának funkciói

Ez a cikk az összes olyan funkciót összegzi, amely jelenleg előzetes verzióban érhető el a Azure Stream Analytics. Az előzetes verziójú funkciók éles környezetben való használata nem ajánlott.

## <a name="authenticate-to-sql-database-output-with-managed-identities-preview"></a>Hitelesítés SQL Database a kimenet felügyelt identitásokkal (előzetes verzió)

Azure Stream Analytics támogatja a [felügyelt identitások hitelesítését](../active-directory/managed-identities-azure-resources/overview.md) Azure SQL Database kimeneti mosogatók esetében. A felügyelt identitások megszüntetik a felhasználó-alapú hitelesítési módszerek korlátozásait, például a jelszó módosítása miatti újrahitelesítés szükségességét. 

## <a name="real-time-high-performance-scoring-with-custom-ml-models-managed-by-azure-machine-learning"></a>Valós idejű nagy teljesítményű pontozás a Azure Machine Learning által felügyelt egyéni ML-modellekkel

Azure Stream Analytics támogatja a nagy teljesítményű, valós idejű pontozást azáltal, hogy kihasználja a Azure Machine Learning által felügyelt egyéni, előre betanított Machine Learning-modelleket, és az Azure Kubernetes szolgáltatásban (ak) vagy Azure Container Instances (ACI) futtatja azokat a munkafolyamatokat, amelyek nem igénylik a kód írását. [Regisztrálás](https://aka.ms/asapreview1) az előzetes verzióra

## <a name="c-custom-de-serializers"></a>C# egyéni de-szerializálók
A fejlesztők kihasználhatják a Azure Stream Analytics hatékonyságát, hogy Protopuf, XML-ben vagy bármilyen egyéni formátumban dolgozzák fel az adatfeldolgozást. A C#-ban implementálhatja az [Egyéni deszerializáló](custom-deserializer-examples.md) eszközöket, amelyek felhasználhatók a Azure stream Analytics által fogadott események deszerializálása céljából.

## <a name="extensibility-with-c-custom-code"></a>Bővíthetőség C# egyéni kóddal

A felhőben vagy IoT Edge Stream Analytics modulokat létrehozó fejlesztők írhatnak vagy használhatnak egyéni C#-függvényeket, és közvetlenül a lekérdezésben meghívhatják azokat a [felhasználó által definiált függvények](stream-analytics-edge-csharp-udf-methods.md)használatával.

## <a name="debug-query-steps-in-visual-studio"></a>Lekérdezési lépések hibakeresése a Visual Studióban

Az adatdiagramon könnyen megtekintheti a köztes sort, ha helyi tesztelést végez a Visual Studio Azure Stream Analytics eszközein. 


## <a name="live-data-testing-in-visual-studio"></a>Élő adattesztelés a Visual Studióban

A Visual Studio Tools for Azure Stream Analytics fokozza a helyi tesztelési funkciót, amely lehetővé teszi, hogy tesztelje a Felhőbeli forrásokból, például az Event hub-ból vagy az IoT hub-ból származó élő esemény-adatfolyamokat. Ismerje meg, hogyan [tesztelheti helyileg az élő információkat a Visual studióhoz készült Azure stream Analytics Tools használatával](stream-analytics-live-data-local-testing.md).

## <a name="visual-studio-code-for-azure-stream-analytics"></a>Visual Studio Code a Azure Stream Analyticshoz

Azure Stream Analytics feladatokat a Visual Studio Code-ban lehet létrehozni. Tekintse meg a [vs Code – első lépéseket ismertető oktatóanyagot](./quick-create-visual-studio-code.md).

## <a name="local-testing-with-live-data-in-visual-studio-code"></a>Helyi tesztelés élő adatértékekkel a Visual Studio Code-ban

A feladatokat az Azure-ba való elküldés előtt tesztelheti a helyi gépen élő adatokon. Az egyes tesztelési iterációk átlagosan kevesebb mint két-három másodpercet vesznek igénybe, ami nagyon hatékony fejlesztési folyamatot eredményez.

