---
title: Az Azure Percept tűzfal konfigurációs és biztonsági javaslatai
description: További információ az Azure Percept tűzfal konfigurációs és biztonsági javaslatairól
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: conceptual
ms.date: 03/25/2021
ms.openlocfilehash: 086d7ec9d2bcae96ee64745a4382c4748aea291e
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105572392"
---
# <a name="azure-percept-firewall-configuration-and-security-recommendations"></a>Az Azure Percept tűzfal konfigurációs és biztonsági javaslatai

Tekintse át az alábbi irányelveket a tűzfalak és az általános biztonsági eljárások Azure Percept való konfigurálásával kapcsolatos információkért.

## <a name="configuring-firewalls-for-azure-percept-dk"></a>Tűzfalak konfigurálása az Azure Percept DK-hoz

Ha a hálózatkezelés beállítása megköveteli, hogy explicit módon engedélyezze az Azure Percept DK-eszközökről érkező kapcsolatokat, tekintse át a következő összetevőket.

Ez az ellenőrzőlista a tűzfalszabályok kiindulási pontja:

|URL-cím (* = helyettesítő karakter)|Kimenő TCP-portok|Használat|
|-------------------|------------------|---------|
|*. auth.azureperceptdk.azure.net|443|Azure DK SOM-hitelesítés és-engedélyezés|
|*. auth.projectsantacruz.azure.net|443|Azure DK SOM-hitelesítés és-engedélyezés|

Emellett tekintse át az [Azure IoT Edge által használt kapcsolatok](https://docs.microsoft.com/azure/iot-edge/production-checklist#allow-connections-from-iot-edge-devices)listáját.

## <a name="additional-recommendations-for-deployment-to-production"></a>További javaslatok az éles környezetben történő üzembe helyezéshez

Az Azure Percept DK számos biztonsági képességgel rendelkezik a dobozból. A jelenlegi kiadásban szereplő hatékony biztonsági funkciók mellett a Microsoft a következő irányelveket is javasolja az éles környezetekben üzemelő példányok tervezésekor:

- Az eszköz erős fizikai védelme
- Győződjön meg arról, hogy az inaktív adatok titkosítása engedélyezve van
- Folyamatosan figyelje az eszköz testtartását, és gyorsan reagáljon a riasztásokra
- Az eszközhöz hozzáférő rendszergazdák számának korlátozása
