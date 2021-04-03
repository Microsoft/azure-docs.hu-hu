---
title: A Microsoft Defender for Office 365 (korábban Office 365 ATP) szolgáltatáshoz való kapcsolódás az Azure Sentinelhez | Microsoft Docs
description: A Microsoft Defender for Office 365-adatok az Azure Sentinelbe való betöltésével megtekintheti és automatizálhatja az automatizált válaszadási forgatókönyveket.
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
ms.date: 09/06/2020
ms.author: yelevin
ms.openlocfilehash: d5140bcd85606213f00185d4ba1f50dbe70dba63
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94655494"
---
# <a name="connect-alerts-from-microsoft-defender-for-office-365"></a>Riasztások összekötése a Microsoft Defender for Office 365 

> [!IMPORTANT]
>
> - **A Microsoft Defender for Office 365** korábban **Office 365 Advanced Threat Protection (ATP)** néven ismert.
>
>     Előfordulhat, hogy a régi név még használatban van a termékben (beleértve az Azure Sentinel adatösszekötőjét) egy adott időszakban.
>
> - A Microsoft Defender for Office 365-riasztások betöltése jelenleg nyilvános előzetes verzióban érhető el. Ez a szolgáltatás szolgáltatói szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 
A [Microsoft Defender for Office 365](/office365/servicedescriptions/office-365-advanced-threat-protection-service-description) az e-mail-üzenetekben, a kártékony URL-hivatkozásokban és az együttműködési eszközökben az ismeretlen kártevők által okozott zéró és egyéb fejlett veszélyforrások ellen biztosít védelmet a szervezetnek. A Microsoft Defender for Office 365-riasztások Azure Sentinelbe való betöltésével a biztonsági műveletek során a levelezés, a fájlmegosztás és az URL-cím alapú fenyegetésekkel kapcsolatos információkat is igénybe veheti. Ezután átfogóbban elemezheti a biztonsági eseményeket a szervezeten belül, és a hatékony és azonnali reagálásra szolgáló forgatókönyveket hozhat létre.

Az összekötő importálja a következő riasztásokat:

- A rendszer potenciálisan rosszindulatú URL-címet észlelt 

- A kézbesítést követően eltávolított kártevőket tartalmazó e-mail üzenetek

- Adathalász URL-címeket tartalmazó e-mailek 

- A felhasználó által a kártevőként vagy adathalászként jelentett e-mail 

- Gyanús e-mail küldési minták észlelhetők 

- A felhasználó e-mail-küldésre korlátozott 

Ezeket a riasztásokat az Office-ügyfelek az **Office biztonsági és megfelelőségi központban** tekinthetik meg.

## <a name="prerequisites"></a>Előfeltételek

- Az összekötő engedélyezésekor olvasási és írási engedélyekkel kell rendelkeznie az Azure Sentinel munkaterületen.

- Globális rendszergazdának vagy biztonsági rendszergazdának kell lennie az Azure Sentinel munkaterület bérlője számára.

- Érvényes licenccel kell rendelkeznie az [office 365 ATP 2-es csomaghoz](/microsoft-365/security/office-365-security/office-365-atp#office-365-atp-plan-1-and-plan-2) (az Office 365 E5, az Office 365 a5 és a Microsoft 365 E5 licenccel együtt), és megvásárolható külön megvásárolható. 

## <a name="connect-to-microsoft-defender-for-office-365"></a>Kapcsolódás a Microsoft Defender for Office 365-hez

Ha a Microsoft Defender for Office 365 telepítve van, és a házirendek konfigurálása megtörtént, a riasztások könnyedén betölthetők az Azure Sentinelbe.

1. Az Azure Sentinelben válassza az **adatösszekötők** lehetőséget a navigációs menüből.

1. Válassza a **Microsoft Defender for Office 365** (továbbra is az *Office 365 komplex veszélyforrások elleni védelem*) lehetőséget az összekötők galériában, és válassza az **összekötő lap megnyitása** lehetőséget.

1. A **konfiguráció** szakaszban kattintson a **kapcsolat** elemre. 

1. Az **incidensek létrehozása** szakaszban kattintson az **Engedélyezés** elemre.

1. Ha a megfelelő sémát szeretné használni az Office 365 ATP-riasztások lekérdezéséhez, keresse meg a **SecurityAlert** , és adja meg a **szolgáltató nevét** **OATP**.

1. A **következő lépések** lapon megtekintheti és használhatja a Microsoft Defender for Office 365-összekötőhöz mellékelt lekérdezési mintákat és elemzési szabályok sablonjait.

## <a name="next-steps"></a>Következő lépések

Ebből a dokumentumból megtudhatta, hogyan csatlakoztatható a Microsoft Defender for Office 365 az Azure Sentinelhez. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](./tutorial-detect-threats-built-in.md).