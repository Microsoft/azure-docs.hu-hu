---
title: Saját gépi tanulási Azure Sentinel | Microsoft Docs
description: Ez a cikk bemutatja, hogyan hozhat létre és használhat saját gépi tanulási algoritmusokat adatelemzéshez a Azure Sentinel.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/23/2020
ms.author: yelevin
ms.openlocfilehash: 2164b8ac6e62b8826d5879da07384769c503bfb5
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2021
ms.locfileid: "107598601"
---
# <a name="bring-your-own-machine-learning-ml-into-azure-sentinel"></a>Saját gépi Machine Learning (ML) a Azure Sentinel

Machine Learning (ML) a Azure Sentinel egyik fő alapja, és az egyik fő attribútum, amely miatt ez a különbség. Azure Sentinel kínál gépi tanulási lehetőségeket számos különböző módon: [beépítetten](fusion.md) a Fusion korrelációs motorba és a Jupyter-jegyzetfüzetekbe, valamint az újonnan elérhető Saját gépi tanulási (BYO ML) platformba. 

A gépi tanulási észlelési modellek alkalmazkodhatnak az egyes környezetekhez és a felhasználói viselkedés változásaihoz a téves riasztások számának csökkentése és a hagyományos megközelítéssel nem észlelt fenyegetések azonosítása érdekében. Számos biztonsági szervezet tisztában van a gépi tanulási biztonsággal kapcsolatos értékével, de közülük nem sok rendelkezik a biztonsággal és a gépi gépekkel kapcsolatos szakértelemmel rendelkező szakemberek luxusával. Az itt bemutatott keretrendszert úgy alakítottuk ki, hogy a biztonsági szervezetek és a szakemberek együtt fejlődjenek a gépi tanulási úton. A gépi tanulási képességekkel nem rendelkező vagy a szükséges szakértelem nélküli szervezetek jelentős védelmi értéket kaphatnak Azure Sentinel gépi tanulási képességekből.

:::image type="content" source="./media/bring-your-own-ml/machine-learning-framework.png" alt-text="gépi tanulási keretrendszer":::

## <a name="what-is-the-bring-your-own-machine-learning-byo-ml-platform"></a>Mi az a saját Machine Learning (BYO-ML) platform?

Az olyan szervezetek számára, amelyek gépi tanulási erőforrásokkal rendelkezik, és személyre szabott gépi tanulási modelleket szeretne felépíteni az egyedi üzleti igényeiknek megfelelően, a **BYO-ML platformot kínáljuk.** A platform a gépi tanulási környezet [Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks) / [Apache Spark](http://spark.apache.org/) Jupyter Notebookokat használja az ML-környezet előállításához. A következő összetevőket biztosítja:

- egy BYO-ML-csomag, amely kódtárakat tartalmaz az adatok eléréséhez és az eredmények Log Analyticsbe (LA) való visszaküldéséhez, hogy az eredményeket integrálni tudja az észleléssel, vizsgálattal és kereséssel. 

- Gépi tanulási algoritmussablonok, amelyek testre szabhatók, hogy illeszkedjenek a szervezet adott biztonsági problémáihoz. 

- mintajegyzetfüzetek a modell betanításához és a modell pontozásának ütemezéséhez. 

Mindezek mellett saját gépi tanulási modelleket és/vagy saját Spark-környezetet is használhatja a gépi tanulási modellekkel való Azure Sentinel.

A BYO-ML platformmal ugorhat a saját ML-modellek építésében: 

- A mintaadatokat tartalmazó jegyzetfüzet teljes gyakorlati tapasztalatot nyújt anélkül, hogy aggódnia kellene az éles adatok kezelése miatt.

- A Spark-környezettel integrált csomag csökkenti az infrastruktúra kezelésével kapcsolatos kihívásokat és súrlódásokat.

- A kódtárak támogatják az adatmozgást. A képzési és pontozási jegyzetfüzetek bemutatják a végpontok között használható élményt, és sablonként szolgálnak a környezethez való alkalmazkodáshoz.

### <a name="use-cases"></a>Használati esetek
 
A BYO-ML platform és -csomag jelentősen csökkenti a saját gépi tanulási észlelések építéséhez szükséges időt és energiát, és lehetővé teszi bizonyos biztonsági problémák megoldását a Azure Sentinel. A platform a következő eseteket támogatja:

**Gépi tanulási algoritmus betanítás egy testreszabott modellhez:** Egy meglévő gépi tanulási algoritmust (amelyet a Microsoft vagy a felhasználói közösség közösen használ), és könnyedén betaníthatja saját adataival, hogy az adatokhoz és a környezethez jobban illeszkedő, testreszabott ML-modellt kap.

**Gépi tanulási algoritmus sablonjának módosítása a testreszabott modell leához:** Módosíthatja a gépi tanulási algoritmus sablonját (amelyet a Microsoft vagy a felhasználói közösség osztott meg), és saját adatai alapján betaníthatja a módosított algoritmust, hogy az adott problémához illeszkedő, testreszabott modellt képezzen le.

**Saját modell létrehozása:** Hozzon létre egy saját modellt az Azure Sentinel BYO-ML platformjának és segédprogramjainak használatával.

**A Databricks-/Spark-környezet integrálása:** Integrálja meglévő Databricks-/Spark-környezetét a Azure Sentinel- és BYO-ML-kódtárakkal és -sablonokkal gépi tanulási modelleket hoz létre az egyedi helyzetekhez.

**Importálja saját gépi tanulási modelljét:** Importálhatja saját gépi tanulási modelljeit, és a BYO-ML platformmal és -segédprogramokkal integrálhatja azokat a Azure Sentinel.

**Gépi tanulási algoritmus megosztása:** Egy gépi tanulási algoritmus megosztása, hogy a közösség beveszi és alkalmazkodjon.

**Az ML segítségével lehetővé teszi a SecOps használatát: saját** egyéni ML-modellt és -eredményeket használhat a kereséshez, az észleléshez, a vizsgálathoz és a válaszadáshoz.

Ez a cikk bemutatja a BYO-ML platform összetevőit, valamint azt, hogyan használhatja a platformot és a Rendellenes erőforrás-hozzáférési algoritmust a gépi tanulási észlelés testreszabott Azure Sentinel.

## <a name="azure-databricksspark-environment"></a>Azure Databricks/Spark-környezet

[Apache Spark™](http://spark.apache.org/) egy ugrást tett a folyamat-big data egyszerűsítése érdekében azáltal, hogy egységes keretrendszert nyújtott az adat folyamatok építéséhez. Azure Databricks a Sparkra épített, felügyelet nélkül nyújtott felhőplatformot biztosít. Javasoljuk, hogy a Databrickset használja a BYO-ML platformhoz, hogy az adat folyamatokkal és platformokkal kapcsolatos problémák kezelése helyett olyan válaszokat találjon, amelyek azonnali hatással vannak az üzletre.
Ha már rendelkezik a Databricks vagy bármely más Spark-környezettel, és inkább a meglévő beállítást szeretné használni, a BYO-ML csomag is jól fog működni. 

## <a name="byo-ml-package"></a>BYO-ML csomag

A BYO ML-csomag a Biztonság érdekében a Microsoft ajánlott eljárásait és kutatásait tartalmazza a gépi tanulási rendszer előterében. Ebben a csomagban a biztonsági problémákhoz szükséges segédprogramok, jegyzetfüzetek és algoritmussablonok alábbi listáját biztosítjuk.

| Fájlnév | Description |
| --------- | ----------- |
| azure_sentinel_utilities.whl | Segédprogramokat tartalmaz a blobok Azure-ból való beolvasáshoz és a Log Analyticsbe való íráshoz. |
| AnomalousRASampleData | A jegyzetfüzet bemutatja a Rendellenes erőforrás-hozzáférési modell használatát a Azure Sentinel betanítás és tesztelés mintaadatokkal. |
| AnomalousRATraining.ipynb | Jegyzetfüzet az algoritmus betanítása, a modellek létrehozása és mentése érdekében. |
| AnomalousRAScoring.ipynb | Jegyzetfüzet, amely ütemezi a modell futtatását, vizualizálja az eredményt, és írja vissza a Azure Sentinel. |
|

Az első elérhető ml-algoritmussablon a [Rendellenes erőforrás-hozzáférés észlelése.](https://github.com/Azure/Azure-Sentinel/tree/master/BYOML) Együttműködésen alapuló szűrési algoritmuson alapul, és Windows-fájlmegosztási hozzáférési naplók (5140-es eseményazonosítójú biztonsági események) használatával van betanítva. A modellhez a naplóban a legfontosabb információ az elért felhasználók és erőforrások párosítása. 

## <a name="example-walkthrough-anomalous-file-share-access-detection"></a>Bemutató: Rendellenes fájlmegosztás-hozzáférés észlelése 

Most, hogy megismerkedett a BYO-ML platform fő összetevőivel, az alábbi példa bemutatja, hogyan használható a platform és az összetevők testreszabott gépi tanulási észlelésre.

### <a name="setup-the-databricksspark-environment"></a>A Databricks-/Spark-környezet beállítása

Ha még nem rendelkezik databricks-környezettel, be kell beállítania a saját Databricks-környezetét. Útmutatásért tekintse meg [a Databricks](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal?tabs=azure-portal) rövid útmutatóját.

### <a name="auto-export-instruction"></a>Automatikus exportálási utasítás

Ahhoz, hogy a Azure Sentinel-ben saját adatai alapján egyéni gépi tanulási modelleket hoz létre, exportálni kell az adatokat a Log Analyticsből egy Blob Storage- vagy Event Hub-erőforrásba, hogy az ML-modell el tudja férni a Databricksből. Ismerje meg, hogyan [lehet adatokat behozni a Azure Sentinel.](connect-data-sources.md)

Ebben a példában szüksége lesz a fájlmegosztás-hozzáférési napló betanítás adataira az Azure Blob Storage-ban. Az adatok formátuma a jegyzetfüzetben és a kódtárakban van dokumentálva.

Az Azure parancssori felület (CLI) használatával automatikusan exportálhatja az adatokat a Log [Analyticsből.](/cli/azure/monitor/log-analytics) 

A parancsok futtatásához Közreműködő szerepkört kell hozzárendelni a Log Analytics-munkaterülethez, a Storage-fiókhoz és az EventHub-erőforráshoz.  

Itt egy példa az automatikus exportálás beállítására irányuló parancskészletre:

```azurecli

az –version

# Login with Azure CLI
 az login

# List all Log Analytics clusters
 az monitor log-analytics cluster list

# Set to specific subscription
 az account set --subscription "SUBSCRIPTION_NAME"
 
# Export to Storage - all tables
 az monitor log-analytics workspace data-export create --resource-group "RG_NAME" --workspace-name "WS_NAME" -n LAExportCLIStr --destination “DESTINATION_NAME" --enable "true" --tables SecurityEvent
 
# Export to EventHub - all tables
 az monitor log-analytics workspace data-export create --resource-group "RG_NAME" --workspace-name "WS_NAME" -n LAExportCLIEH --destination “DESTINATION_NAME" --enable "true" --tables SecurityEvent Heartbeat"]

# List export settings
az monitor log-analytics workspace data-export list --resource-group "RG_NAME" --workspace-name "WS_NAME"

# Delete export setting
 az monitor log-analytics workspace data-export delete --resource-group "RG_NAME" --workspace-name "WS_NAME" --name "NAME"
```

### <a name="export-custom-data"></a>Egyéni adatok exportálása

A Log Analytics automatikus exportálása által nem támogatott egyéni adatok esetén a Logikai alkalmazás vagy más megoldások használatával áthelyezheti az adatokat. Tekintse meg a [Log Analytics-adatok blobtárolóba exportálása blogot](https://techcommunity.microsoft.com/t5/azure-monitor/log-analytics-data-export-preview/ba-p/1783530) és szkriptet.

### <a name="correlate-with-data-outside-of-azure-sentinel"></a>Korrelál az adatokkal a Azure Sentinel

Az adatok külső tárolóból is Azure Sentinel blobtárolóba vagy eseményközpontba, és korrelálhatja őket a Azure Sentinel-adatokkal az ML-modellek felépítéséhez. 
 
### <a name="copy-and-install-the-related-packages"></a>A kapcsolódó csomagok másolása és telepítése

Másolja a BYO-ML csomagot a fent említett Azure Sentinel GitHub-adattárból a Databricks-környezetbe. Ezután nyissa meg a notebookokat, és kövesse a jegyzetfüzetben található utasításokat a szükséges kódtárak fürtökön való telepítéséhez.

### <a name="model-training-and-scoring"></a>Modell betanítás és pontozás

A két jegyzetfüzet utasításait követve módosítsa a konfigurációkat a saját környezetének és erőforrásainak megfelelően, kövesse a modell betanítási és buildelésekor szükséges lépéseket, majd ütemezi a modellt a bejövő fájlmegosztási hozzáférési naplók pontozására.

### <a name="write-results-to-log-analytics"></a>Eredmények írása a Log Analyticsbe

Ha a pontozás ütemezve van, a pontozási jegyzetfüzet modulját használva a pontozási jegyzetfüzetben megírhatja a pontszám eredményeit az Azure Sentinel-példányhoz társított Log Analytics-munkaterületre.

### <a name="check-results-in-azure-sentinel"></a>Eredmények ellenőrzése a Azure Sentinel

A pontozási eredmények és a kapcsolódó naplóadatok együttesen való megtekintése érdekében lépjen vissza a Azure Sentinel portálra. Az **Egyéni** > naplók naplóiban az eredmények  a AnomalousResourceAccessResult_CL táblában (vagy a saját egyéni táblanevében) fognak látszatra. Ezeket az eredményeket felhasználhatja a vizsgálati és keresési élmény továbbfejlesztése érdekében.

:::image type="content" source="./media/bring-your-own-ml/anomalous-resource-access-logs.png" alt-text="rendellenes erőforrás-hozzáférési naplók":::

### <a name="build-custom-analytics-rule-with-ml-results"></a>Egyéni elemzési szabály létrehozása gépi tanulási eredményekkel

Miután meggyőződött arról, hogy a gépi tanulási eredmények az egyéni naplók táblában vannak, és elégedett a pontszámok hűségével, az eredmények alapján észlelést hozhat létre. A **portálon az Elemzés** Azure Sentinel, és [hozzon létre egy új észlelési szabályt.](tutorial-detect-threats-custom.md) Az alábbi példa az észlelés létrehozásához használt lekérdezést mutatja be.

:::image type="content" source="./media/bring-your-own-ml/create-byo-ml-analytics-rule.png" alt-text="egyéni elemzési szabály létrehozása A B Y O M L észleléséhez":::

### <a name="view-and-respond-to-incidents"></a>Incidensek megtekintése és válaszadás az incidensekre
Miután beállította az elemzési szabályt a gépi tanulási eredmények alapján, és az eredmények a lekérdezésben beállított küszöbérték  fölé esnek, a rendszer létrehoz egy incidenst, és felszínre hoz egy incidenst az Azure Sentinel. 

## <a name="next-steps"></a>Következő lépések

Ebben a dokumentumban megtanulta, hogyan használhatja a Azure Sentinel BYO-ML platformját saját gépi tanulási algoritmusok létrehozására vagy importálására az adatok elemzéséhez és a fenyegetések észleléséhez.

- A gépi tanulással kapcsolatos bejegyzéseket és számos egyéb kapcsolódó témakört az [Azure Sentinel blogban talál.](https://aka.ms/azuresentinelblog)
