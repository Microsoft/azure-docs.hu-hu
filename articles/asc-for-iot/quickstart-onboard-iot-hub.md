---
title: IoT-szolgáltatás IoT Hub előzetes verzióban érhető el az Azure Security Center |} A Microsoft Docs
description: Ismerje meg, hogyan engedélyezhető az Azure Security Center az IoT hub IoT-szolgáltatás.
services: asc-for-iot
ms.service: ascforiot
documentationcenter: na
author: mlottner
manager: barbkess
editor: ''
ms.assetid: 670e6d2b-e168-4b14-a9bf-51a33c2a9aad
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/29/2019
ms.author: mlottner
ms.openlocfilehash: 0236050ffcf7ad1d18ff3a8a763d0469d91eeeb5
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/30/2019
ms.locfileid: "64919927"
---
# <a name="quickstart-enable-service-in-iot-hub"></a>Gyors útmutató: Az IoT Hub szolgáltatás engedélyezése

> [!IMPORTANT]
> Az Azure Security Center az IoT jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ez a cikk az Azure Security Center (ASC) az IoT hub IoT-előzetes szolgáltatás engedélyezése a magyarázattal szolgál.  

> [!NOTE]
> Az Azure Security Center az IoT jelenleg csak a standard szintű és a magasabb szintű IoT-központok támogatja.
> Az Azure Security Center az IoT hub egyetlen megoldás is. Ha több van szüksége, több megoldás szükség. 

## <a name="prerequisites-for-enabling-the-service"></a>A szolgáltatás engedélyezéséhez szükséges előfeltételek

- Log Analytics-munkaterület
  - Két típusú adatok tárolódnak a Log Analytics-munkaterület az ASC alapértelmezés szerint az IoT; **biztonsági riasztások** és **javaslatok**. 
  - Dönthet úgy, hogy adjon hozzá további információk típusa, a storage **nyers események**. Vegye figyelembe, hogy tárolására **nyers események** a Log Analytics sorozatéhoz további tárolási költségeket. 
- Az IoT Hub (standard szintű vagy újabb)
- Igazíthatja [prerequities szolgáltatás](service-prerequisites.md) 

## <a name="enable-asc-for-iot-on-your-iot-hub"></a>Az IoT hub IoT ASC engedélyezése 

Ahhoz, hogy az IoT hub biztonsági, tegye a következőket: 

1. Nyissa meg a **az IoT Hub** az Azure Portalon. 
2. Alatt a **biztonsági** menüben kattintson a **áttekintése**, majd kattintson a **Start preview**. 
3. Válasszon **IoT-biztonság engedélyezése**. 
4. Adja meg a Log Analytics-munkaterület adatait. 
   - Tárolhatja **nyers események** mellett az alapértelmezett típusokat. Ha tárolási a **nyers események** váltógomb **a**. 
   - Dönthet úgy, hogy engedélyezze, hogy **ikereszköz gyűjtemény** távozó a **ikereszköz gyűjtemény** váltógomb **a**. 
5. Kattintson a **Save** (Mentés) gombra. 

Gratulálunk! Ön teljesítette az IoT hub IoT ASC engedélyezéséhez. 

## <a name="next-steps"></a>További lépések

Folytassa a következő cikkben talál a megoldás konfigurálása...

> [!div class="nextstepaction"]
> [A megoldás konfigurálása](quickstart-configure-your-solution.md)