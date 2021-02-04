---
title: Az Apache HTTP-kiszolgáló és az Azure Sentinel összekötése | Microsoft Docs
description: Megtudhatja, hogyan használhatja az Apache HTTP Server-összekötőt az Apache-naplók Azure Sentinelbe való lekéréséhez. Megtekintheti az Apache-t a munkafüzetekben, riasztásokat hozhat létre, és javíthatja a vizsgálatot.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2021
ms.author: yelevin
ms.openlocfilehash: 59ef34f9d6d42277d8e83bf4e0ebb8ee29096f52
ms.sourcegitcommit: f82e290076298b25a85e979a101753f9f16b720c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2021
ms.locfileid: "99566768"
---
# <a name="connect-your-apache-http-server-to-azure-sentinel"></a>Az Apache HTTP-kiszolgáló és az Azure Sentinel összekötése

> [!IMPORTANT]
> Az Apache HTTP Server-összekötő jelenleg **előzetes** verzióban érhető el. Tekintse meg a kiegészítő [használati feltételeket a Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) előzetes verziókra vonatkozó további jogi feltételekhez, amelyek olyan Azure-szolgáltatásokra vonatkoznak, amelyek a bétaverzióban, az előzetes verzióban vagy más esetben még nem jelent meg általánosan elérhetővé.

Ez a cikk azt ismerteti, hogyan csatlakoztatható az Apache HTTP-kiszolgáló az Azure Sentinelhez. Az Apache HTTP Server Connector segítségével egyszerűen betöltheti az Apache HTTP-kiszolgáló naplóit az Azure Sentinel szolgáltatásba, így megtekintheti a munkafüzetek adatait, lekérdezheti, hogy egyéni riasztásokat hozzon létre, és hogyan építheti be a vizsgálat javítására. Az Apache HTTP-kiszolgáló és az Azure Sentinel közötti integráció a Log Analytics ügynök általi helyi fájlok feldolgozását is lehetővé teszi.

> [!NOTE]
> Az Azure Sentinel-t futtató munkaterület földrajzi helye tárolja az adatmennyiséget.

## <a name="prerequisites"></a>Előfeltételek

- Írási engedéllyel kell rendelkeznie az Azure Sentinel munkaterületen.

## <a name="configure-and-integrate-apache-http-server-logs-via-log-analytics-agent"></a>Apache HTTP Server-naplók konfigurálása és integrálása Log Analytics ügynök használatával

Konfigurálja az Apache HTTP-kiszolgáló naplófájljainak helyét, hogy az Log Analytics ügynökön keresztül továbbítsa az adatait az Azure-munkaterületre.
Log Analytics ügynök konfigurálása az Apache HTTP-kiszolgáló naplófájljainak olvasásához.

1. Kövesse az utasításokat a https://httpd.apache.org/docs/2.4/logs.html naplófájlok helyének Apache HTTP-kiszolgálón történő beállításához.

1. Az Azure Sentinel navigációs menüjében válassza az **adatösszekötők** lehetőséget, majd válassza az **Apache HTTP-kiszolgáló (előzetes verzió)** lehetőséget.

1. Válassza az **összekötő megnyitása lapot**.

1. Kövesse az Apache HTTP-kiszolgáló lapon megjelenő utasításokat.

## <a name="find-your-data"></a>Az adatai megkeresése

A sikeres kapcsolatok létrejötte után az adat a ApacheHTTPServer_CL alatt Log Analytics jelenik meg.

## <a name="validate-connectivity"></a>Kapcsolat ellenőrzése

Akár 20 percet is igénybe vehet, amíg a naplók meg nem kezdődnek a Log Analytics.

## <a name="next-steps"></a>Következő lépések

Ebből a dokumentumból megtudta, hogyan csatlakoztatható az Apache HTTP-kiszolgáló az Azure Sentinelhez. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

- Ismerje meg, hogyan [érheti el az adatait és a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).
- Az adatait a [munkafüzetek használatával](tutorial-monitor-your-data.md) figyelheti.
