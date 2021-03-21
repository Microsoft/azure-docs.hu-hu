---
title: Azure Stream Analytics JobConfig.jsa mezőkön
description: Ez a cikk a Visual Studio Code-ban a feladatok létrehozásához használt fájl Azure Stream Analytics JobConfig.jstámogatott mezőit sorolja fel.
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 02/14/2020
ms.openlocfilehash: 0eebd0b62e973572a40b7b141ae908046700ba3c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98020485"
---
# <a name="azure-stream-analytics-jobconfigjson-fields"></a>Azure Stream Analytics JobConfig.jsa mezőkön

A következő mezők támogatottak a [Azure stream Analytics-feladatok Visual Studio Code használatával történő létrehozásához](quick-create-visual-studio-code.md)használt fájl *JobConfig.js* .

```json
{
    "DataLocale": "string",
    "OutputErrorPolicy": "string",
    "EventsLateArrivalMaxDelayInSeconds": "integer",
    "EventsOutOfOrderMaxDelayInSeconds": "integer",
    "EventsOutOfOrderPolicy": "string",
    "StreamingUnits": "integer",
    "CompatibilityLevel": "string",
    "UseSystemAssignedIdentity": "boolean",
    "GlobalStorage": {
        "AccountName": "string",
        "AccountKey": "string",
    },
    "DataSourceCredentialDomain": "string",
    "ScriptType": "string",
    "Tags": {}
}
```

|Név|Típus|Kötelező|Érték|
|----|----|--------|-----|
|DataLocale|sztring|No|A stream Analytics-feladatok adatterülete. Az értéknek egy támogatott névnek kell lennie. Ha nincs megadva, az alapértelmezett érték az "en-US".|
|OutputErrorPolicy|sztring|No|Azt a házirendet jelzi, amelyet a rendszer a kimenetre érkező eseményekre alkalmaz, és nem írható a külső tárolóba, mert helytelen formátumú (hiányzó oszlop-értékek, helytelen típusú oszlopok vagy méretek). – Leállítás vagy eldobás|
|EventsLateArrivalMaxDelayInSeconds|egész szám|No|Az a maximálisan megengedett késleltetés (másodpercben), ahol a késésben lévő események bekerülhetnek. A támogatott tartomány – 1 – 1814399 (20.23:59:59 nap) és-1 a várakozás határozatlan idejű megadására szolgál. Ha a tulajdonság hiányzik, a rendszer úgy értelmezi, hogy a-1 értékkel rendelkezik.|
|EventsOutOfOrderMaxDelayInSeconds|egész szám|No|Az a maximálisan megengedett késleltetés másodpercben, amelyben a megrendelési események nem állíthatók vissza rendelésre.|
|EventsOutOfOrderPolicy|sztring|No|Azt a házirendet jelzi, amely a bemeneti esemény adatfolyamában a sorrendtől megérkező eseményekre vonatkozik. – Módosítás vagy eldobás|
|StreamingUnits|egész szám|Yes|Meghatározza a folyamatos átviteli egység által használt folyamatos átviteli egységek számát.|
|CompatibilityLevel|sztring|No|A folyamatos átviteli feladatok bizonyos futásidejű viselkedéseit vezérli. -Elfogadható értékek: "1,0", "1,1", "1,2"|
|UseSystemAssignedIdentity|boolean|No|Az igaz érték megadásával engedélyezheti, hogy ez a feladattípus felügyelt Azure Active Directory identitás használatával kommunikáljon más Azure-szolgáltatásokkal.|
|GlobalStorage. AccountName|sztring|No|A globális Storage-fiók a stream Analytics-feladathoz kapcsolódó tartalmak tárolására használatos, például az SQL-hivatkozási adatok pillanatképeit.|
|GlobalStorage. AccountKey|sztring|No|A globális Storage-fiókhoz tartozó kulcs.|
|DataSourceCredentialDomain|sztring|No|A hitelesítő adatok helyi tárolójának fenntartott tulajdonsága.|
|ScriptType|sztring|Yes|Fenntartott tulajdonság a forrásfájl típusának jelzéséhez. A (z) JobConfig.jsesetén elfogadható érték a következő: "JobConfig".|
|Címkék|JSON-kulcs-érték párok|No|A címkék olyan név/érték párok, amelyek lehetővé teszik az erőforrások kategorizálását és az összevont számlázás megtekintését, ha ugyanazt a címkét több erőforrásra és erőforráscsoporthoz alkalmazza. A címkék nevei megkülönböztetik a kis-és nagybetűket, és a címke értéke kis-és nagybetűk megkülönböztetése.|

## <a name="next-steps"></a>Következő lépések

* [Azure Stream Analytics-feladatok létrehozása a Visual Studio Code-ban](quick-create-visual-studio-code.md)
* [A Visual Studio Code segítségével helyileg tesztelheti Stream Analytics lekérdezéseket a mintaadatok használatával](visual-studio-code-local-run.md)
* [A Visual Studio Code használatával helyileg tesztelheti stream Analytics lekérdezéseket élő stream-bevitelsel](visual-studio-code-local-run-live-input.md) 
* [Azure stream Analytics-feladatok üzembe helyezése CI/CD NPM csomag használatával](./cicd-overview.md)