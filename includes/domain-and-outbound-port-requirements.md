---
title: fájl belefoglalása
description: fájl belefoglalása
services: data-factory
author: nabhishek
ms.service: data-factory
ms.topic: include
ms.date: 10/09/2019
ms.author: abnarain
ms.openlocfilehash: 626074940ced57bdb1ae93f494b7a3847ef83a81
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96444989"
---
| Tartománynevek                  | Kimenő portok | Description                              |
| ----------------------------- | -------------- | ---------------------------------------- |
| `*.servicebus.windows.net`    | 443            | A saját üzemeltetésű integrációs modul szükséges az interaktív létrehozáshoz. |
| `{datafactory}.{region}.datafactory.azure.net`<br> vagy `*.frontend.clouddatahub.net` | 443            | A saját üzemeltetésű integrációs modul számára szükséges a Data Factory szolgáltatáshoz való csatlakozáshoz. <br>Új létrehozott Data Factory esetén keresse meg a teljes tartománynevet a saját üzemeltetésű Integration Runtime-kulcsból, amely a következő formátumban van: {DataFactory}. {Region}. DataFactory. Azure. net. Ha a régi adatelőállító nem látja a teljes tartománynevet a saját üzemeltetésű integrációs kulcsban, használja a *. frontend.clouddatahub.net helyet. |
| `download.microsoft.com`    | 443            | A frissítések letöltéséhez a saját üzemeltetésű Integration Runtime szükséges. Ha letiltotta az automatikus frissítést, akkor kihagyhatja a tartomány konfigurálását. |
| `*.core.windows.net`          | 443            | A saját üzemeltetésű integrációs modul használja az Azure Storage-fiókhoz való csatlakozásra az [előkészített másolási](../articles/data-factory/copy-activity-performance.md#staged-copy) szolgáltatás használatakor. |
| `*.database.windows.net`      | 1433           | Csak akkor szükséges, ha Azure SQL Database vagy az Azure szinapszis Analytics szolgáltatásból másol, vagy nem kötelező. A szakaszos másolási szolgáltatással az 1433-as port megnyitása nélkül másolhat az adatSQL Database vagy az Azure szinapszis Analytics szolgáltatásba. |
| `*.azuredatalakestore.net`<br>`login.microsoftonline.com/<tenant>/oauth2/token`    | 443            | Csak akkor szükséges, ha a vagy a rendszerből másol Azure Data Lake Store és opcionálisan. |
