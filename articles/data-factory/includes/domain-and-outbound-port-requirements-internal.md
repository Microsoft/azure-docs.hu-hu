---
title: fájl belefoglalása
description: fájl belefoglalása
author: lrtoyou1223
ms.service: data-factory
ms.topic: include
ms.date: 10/09/2019
ms.author: lle
ms.openlocfilehash: 24a541080b580eab967987fa7c92cea64b99d65c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100389499"
---
| Tartománynevek                                          | Kimenő portok | Description                |
| ----------------------------------------------------- | -------------- | ---------------------------|
| Nyilvános felhő: `*.servicebus.windows.net` <br> Azure Government: `*.servicebus.usgovcloudapi.net` <br> Kína `*.servicebus.chinacloudapi.cn`   | 443            | A saját üzemeltetésű integrációs modul szükséges az interaktív létrehozáshoz. |
| Nyilvános felhő: `{datafactory}.{region}.datafactory.azure.net`<br> vagy `*.frontend.clouddatahub.net` <br> Azure Government: `{datafactory}.{region}.datafactory.azure.us` <br> Kína `{datafactory}.{region}.datafactory.azure.cn` | 443            | A saját üzemeltetésű integrációs modul számára szükséges a Data Factory szolgáltatáshoz való csatlakozáshoz. <br>A nyilvános felhőben létrehozott új Data Factory esetén keresse meg a teljes tartománynevet a saját üzemeltetésű Integration Runtime-kulcsból, amely a következő formátumban van: {DataFactory}. {Region}. DataFactory. Azure. net. Ha a régi adatelőállító nem látja a teljes tartománynevet a saját üzemeltetésű integrációs kulcsban, használja a *. frontend.clouddatahub.net helyet. |
| `download.microsoft.com`    | 443            | A frissítések letöltéséhez a saját üzemeltetésű Integration Runtime szükséges. Ha letiltotta az automatikus frissítést, akkor kihagyhatja a tartomány konfigurálását. |
| Key Vault URL | 443           | Azure Key Vault szükséges, ha a hitelesítő adatokat Key Vault tárolja. |
