---
title: Saját ML-t hozhat az Azure Sentinelbe | Microsoft Docs
description: Ez a cikk bemutatja, hogyan hozhat létre és használhat saját gépi tanulási algoritmusokat az adatelemzéshez az Azure Sentinelben.
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
ms.openlocfilehash: f8d795dcf64230140e1dd07e3f30ca3aa6825ab4
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2021
ms.locfileid: "99807003"
---
# <a name="bring-your-own-machine-learning-ml-into-azure-sentinel"></a>Saját Machine Learning (ML) bekapcsolása az Azure Sentinelbe

A Machine Learning (ML) az Azure Sentinel egyik fő kitűzése, és az egyik fő attribútum, amelyik egymás mellett van beállítva. Az Azure Sentinel számos különböző élményt kínál: a [fúziós](fusion.md) korrelációs motor és a Jupyter notebookok beépített részét, valamint az újonnan elérhető Build-saját ml (byo ml) platformot. 

A ML-észlelési modellek alkalmazkodnak az egyes környezetekhez és a felhasználói viselkedés változásaihoz, így csökkenthetik a téves pozitív állapotot, és azonosítják azokat a fenyegetéseket, amelyek hagyományos megközelítéssel nem találhatók meg. Számos biztonsági szervezet ismeri az ML-t a biztonság szempontjából, de nem sok közülük olyan szakembereket, akik szakértelemmel rendelkeznek mind a biztonságban, mind a ML-ben. Úgy terveztük, hogy a biztonsági szervezeteknek és szakembereknek szóló, a ML-vel való növekedéshez szükséges keretrendszer. Azok a szervezetek, amelyek a megfelelő szaktudással rendelkeznek, akár nem, akár nem, jelentős mértékű védelmi értéket szerezhetnek az Azure Sentinel beépített ML-képességeiből.

:::image type="content" source="./media/bring-your-own-ml/machine-learning-framework.png" alt-text="Machine learning-keretrendszer":::

## <a name="what-is-the-bring-your-own-machine-learning-byo-ml-platform"></a>Mi a saját Machine Learning (BYO-ML) platform?

A ML-erőforrásokkal rendelkező és egyedi üzleti igényeknek megfelelő egyéni ML-modelleket használó szervezetek esetében a **byo-ml platformot** kínáljuk. A platform lehetővé teszi a [Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks) / [Apache Spark](http://spark.apache.org/) -környezet és a Jupyter notebookok használatát a ml-környezet létrehozásához. A következő összetevőket tartalmazza:

- egy BYO-ML csomag, amely olyan kódtárakat tartalmaz, amelyek segítenek az adatelérésben és az eredmények visszaküldésében Log Analytics (LA), így az eredmények az észleléssel, a vizsgálattal és a vadászattal is integrálhatók. 

- ML algoritmus-sablonok, amelyekkel testre szabhatja a szervezet bizonyos biztonsági problémáit. 

- minta jegyzetfüzetek a modell betanításához és a modell pontozási tervének megadásához. 

Emellett a saját ML-modelljeit és/vagy saját Spark-környezetét is használhatja az Azure Sentinel integrálásához.

A BYO-ML platform használatával a saját ML-modelljeinek kiépítésekor a következő lépésekkel kezdheti meg a munkát: 

- A mintaadatok használatával teljes körű tapasztalatokat szerezhet, így nem kell aggódnia a termelési adat kezelésével kapcsolatban.

- A Spark-környezettel integrált csomag csökkenti az infrastruktúra kezelése során felmerülő kihívásokat és súrlódásokat.

- A kódtárak támogatják az adatáthelyezést. A betanítási és pontozási jegyzetfüzetek a teljes körű élményt mutatják be, és sablonként szolgálnak a környezethez való alkalmazkodáshoz.

### <a name="use-cases"></a>Használati esetek
 
A BYO-ML platform és csomag jelentősen csökkenti a saját ML-észlelések kialakításához szükséges időt és erőfeszítést, és felszabadítja az Azure Sentinel bizonyos biztonsági problémáinak elhárítására alkalmas képességet. A platform a következő használati eseteket támogatja:

**Egy ml-algoritmus betanítása testreszabott modell beszerzéséhez:** Egy meglévő ML-algoritmust (amelyet a Microsoft vagy a felhasználói közösség is megoszthat) és egyszerűen betaníthat a saját adataiba, hogy egy testreszabott ML-modellt kapjon, amely jobban megfelel az adatainak és a környezetnek.

**Egy ml-algoritmus sablonjának módosítása testreszabott modell beszerzéséhez:** Módosítható egy ML-algoritmus sablon (amelyet a Microsoft vagy a felhasználói közösség is megoszthat), és betaníthatja a módosított algoritmust a saját adataira, hogy egy testreszabott modellt származtatjon az adott problémához.

**Saját modell létrehozása:** Hozzon létre saját modellt az Azure Sentinel BYO-ML platformjának és segédprogramjainak használatával.

**A Databricks/Spark-környezet integrálása:** Integrálja meglévő Databricks-vagy Spark-környezetét az Azure Sentinelbe, és használjon BYO ML-kódtárakat és-sablonokat, hogy az egyedi helyzetekhez ML modelleket hozzon létre.

**Importálja a saját ml-modelljét:** Importálhatja saját ML-modelljeit, és a BYO-ML platformot és segédprogramokat használva integrálhatja őket az Azure Sentinel használatával.

**Ml-algoritmus megosztása:** A Közösség által elfogadott és alkalmazkodó ML-algoritmus megosztása.

**Használja a ml-t a SecOps meghatalmazása érdekében:** saját egyéni ml-modelljét és a vadászat, az észlelés, a vizsgálat és a válasz eredményét használja.

Ez a cikk bemutatja a BYO-ML platform összetevőit, valamint azt, hogyan használhatja a platformot és a rendellenes erőforrás-hozzáférési algoritmust, hogy testreszabott ML-észlelést nyújtson az Azure Sentinel használatával.

## <a name="azure-databricksspark-environment"></a>Azure Databricks/Spark-környezet

[Apache Spark™](http://spark.apache.org/) az adatfolyamatok létrehozásához szükséges egységes keretrendszer kialakításával elvégezte a Big Data egyszerűsítését. A Azure Databricks a Sparkon alapuló, zéró felügyeletű felhőalapú platformot biztosít. Javasoljuk, hogy a Databricks-t használja a BYO-ML platformhoz, hogy az adatfolyamatok és a platformmal kapcsolatos problémák megoldása helyett olyan válaszokat találjon, amelyek azonnali hatást gyakorolnak a vállalatra.
Ha már rendelkezik Databricks vagy bármely más Spark-környezettel, és inkább a meglévő telepítést szeretné használni, akkor a BYO-ML csomag is jól működik rajtuk. 

## <a name="byo-ml-package"></a>BYO-ML csomag

A BYO ML-csomag tartalmazza a Microsoft ajánlott eljárásait és kutatását az ML-ben a biztonság érdekében. Ebben a csomagban a biztonsági problémákkal kapcsolatos segédprogramok, jegyzetfüzetek és algoritmus-sablonok következő listáját biztosítjuk.

| Fájlnév | Description |
| --------- | ----------- |
| azure_sentinel_utilities. WHL | A Blobok Azure-ból való olvasására és Log Analyticsba írására szolgáló segédprogramokat tartalmaz. |
| AnomalousRASampleData | A notebook a rendellenes erőforrás-hozzáférési modell használatát mutatja be az Azure Sentinel-ben az előállított képzési és tesztelési mintaadatok használatával. |
| AnomalousRATraining. ipynb | Jegyzetfüzet az algoritmus betanításához, a modellek létrehozásához és mentéséhez. |
| AnomalousRAScoring. ipynb | Jegyzetfüzet a modell futtatásához, az eredmény megjelenítéséhez és a pontszám visszaírásához vissza az Azure Sentinelbe. |
|

Az általunk kínált első ML-algoritmus a [rendellenes erőforrás-hozzáférés észlelésére](https://github.com/Azure/Azure-Sentinel/tree/master/BYOML)szolgál. Ez egy együttműködési szűrési algoritmuson alapul, és a Windows fájlmegosztás-hozzáférési naplóival van betanítva (biztonsági események a 5140-es AZONOSÍTÓJÚ eseménnyel). A naplóban a modellhez szükséges legfontosabb információk az elért felhasználók és erőforrások párosítása. 

## <a name="example-walkthrough-anomalous-file-share-access-detection"></a>Példa a forgatókönyvre: rendellenes fájlmegosztás hozzáférésének észlelése 

Most, hogy megismerte a BYO-ML platform főbb összetevőit, itt látható egy példa arra, hogy miként használható a platform és az összetevők a testreszabott ML-észlelések továbbítására.

### <a name="setup-the-databricksspark-environment"></a>A Databricks/Spark-környezet beállítása

Ha még nem rendelkezik ilyennel, a saját Databricks-környezetét kell beállítania. Útmutatásért tekintse meg a [Databricks](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal?tabs=azure-portal) gyors üzembe helyezési dokumentumát.

### <a name="auto-export-instruction"></a>Automatikus exportálási utasítás

Ha saját adatai alapján szeretne egyéni ML-modelleket létrehozni az Azure Sentinelben, exportálnia kell az adatait Log Analytics egy blob Storage vagy Event hub-erőforrásba, hogy a ML-modell hozzáférhessen a Databricks. Ismerje meg, hogyan lehet az [Azure sentinelbe bevenni az adatmennyiséget](connect-data-sources.md).

Ebben a példában az Azure Blob Storage-ban be kell állítania a fájlmegosztás hozzáférési naplójának betanítási adatait. Az adat formátuma a jegyzetfüzetben és a tárakban van dokumentálva.

Az [Azure parancssori felületének (CLI)](/cli/azure/monitor/log-analytics)használatával automatikusan exportálhatja log Analytics adatait. 

A parancsok futtatásához hozzá kell rendelnie a **közreműködő** szerepkört a log Analytics munkaterületen, a Storage-fiókban és a EventHub-erőforrásban. 

Az alábbi példa az automatikus exportálás beállítására szolgáló parancsokat mutat be:

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

### <a name="export-custom-data"></a>Egyéni adatértékek exportálása

Az automatikus exportálás Log Analytics által nem támogatott egyéni adatértékek esetén a Logic app vagy más megoldások használatával helyezheti át az adatait. A [log Analytics-adatexportálás a blob Store](https://www.borninthecloud.com/exporting-log-analytics-data-to-blob-store/?preview=true) -ba blogon és a parancsfájlon keresztül is megtekinthető.

### <a name="correlate-with-data-outside-of-azure-sentinel"></a>Az Azure Sentinelen kívüli adatkezelés

Az Azure Sentinelen kívülről is átviheti az adatait a blob Storage-ba vagy az Event hub-ra, és összekapcsolhatja őket az Azure Sentinel-adatokkal, hogy felkészítse az ML-modell 
 
### <a name="copy-and-install-the-related-packages"></a>A kapcsolódó csomagok másolása és telepítése

Másolja a BYO-ML csomagot a fent említett Azure Sentinel GitHub-adattárból a Databricks-környezetbe. Ezután nyissa meg a jegyzetfüzeteket, és kövesse a jegyzetfüzetben található utasításokat a szükséges kódtárak telepítéséhez a fürtökön.

### <a name="model-training-and-scoring"></a>Modellek betanítása és pontozása

Kövesse a két jegyzetfüzetben található utasításokat a konfigurációk saját környezetének és erőforrásainak megfelelően történő módosításához, kövesse a modell betanítása és kiépítésének lépéseit, majd ütemezze a modellt a bejövő fájlmegosztás hozzáférési naplóihoz.

### <a name="write-results-to-log-analytics"></a>Eredmények Log Analyticsba írása

Miután beolvasta a pontozást, a pontozási jegyzetfüzet moduljának használatával megírhatja a pontszám eredményeit az Azure Sentinel-példányhoz társított Log Analytics munkaterületre.

### <a name="check-results-in-azure-sentinel"></a>Eredmények keresése az Azure Sentinelben

Ha a kapcsolódó napló részleteivel együtt szeretné megtekinteni az eredményül kapott eredményeket, térjen vissza az Azure Sentinel-portálra. A **naplók** > egyéni naplókban az eredményeket a **AnomalousResourceAccessResult_CL** táblában (vagy a saját egyéni táblanév) fogja látni. Ezeket az eredményeket használhatja a nyomozási és a vadászati élmény fokozásához.

:::image type="content" source="./media/bring-your-own-ml/anomalous-resource-access-logs.png" alt-text="rendellenes erőforrás-hozzáférési naplók":::

### <a name="build-custom-analytics-rule-with-ml-results"></a>Egyéni elemzési szabály készítése ML-eredményekkel

Miután megerősítette, hogy a ML-eredmények szerepelnek az egyéni naplók táblában, és elégedett a pontszámok hűségével, az eredmények alapján létrehozhat egy észlelést. Nyissa meg az **Analytics szolgáltatást** az Azure Sentinel portálon, és [hozzon létre egy új észlelési szabályt](tutorial-detect-threats-custom.md). Az alábbi példa az észlelés létrehozásához használt lekérdezést mutatja be.

:::image type="content" source="./media/bring-your-own-ml/create-byo-ml-analytics-rule.png" alt-text="Egyéni elemzési szabály létrehozása B Y O M L-észlelésekhez":::

### <a name="view-and-respond-to-incidents"></a>Incidensek megtekintése és reagálás
Miután beállította az elemzési szabályt az ML-eredmények alapján, ha a lekérdezésben beállított küszöbérték felett van eredmény, akkor az Azure Sentinel **incidensek** lapján létrejön egy incidens. 

## <a name="next-steps"></a>Következő lépések

Ebből a dokumentumból megtanulta, hogyan használhatja az Azure Sentinel BYO-ML platformját saját gépi tanulási algoritmusok létrehozására vagy importálására az adatelemzés és a fenyegetések észlelése érdekében.

- Tekintse meg az [Azure Sentinel blogjában](https://aka.ms/azuresentinelblog)a gépi tanulással és sok más témával kapcsolatos témakört.