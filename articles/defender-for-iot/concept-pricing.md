---
title: Díjszabás és kapcsolódó költségek
description: Ismerje meg a Defender for IoT szolgáltatással kapcsolatos költségeket, valamint azt, hogyan vezérelheti őket.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/08/2020
ms.author: shhazam
ms.openlocfilehash: a97bcbf5ba47289a2e68b0eaa587ea39d7fb705a
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/31/2020
ms.locfileid: "97832352"
---
# <a name="pricing-and-associated-costs"></a>Díjszabás és kapcsolódó költségek

Ez a cikk a Defender for IoT díjszabási modelljét ismerteti, összefoglalja az összes kapcsolódó költséget, és ismerteti, hogyan kezelheti őket.

## <a name="pricing"></a>Díjszabás

A Defender for IoT díjszabási modellje két részből áll, és akkor számítunk fel díjat, ha IoT Hub [engedélyezve](quickstart-onboard-iot-hub.md) van a Defender for IoT:

- A IoT Hub naplók elemzése alapján az eszközök által beépített biztonsági funkciók költséghatékonyak.

- A IoT Edge-vagy Leaf-eszközökről származó biztonsági üzeneteken alapuló, üzenetekkel bővített biztonsági képességek díja.

További információ: [Security Center díjszabása](https://azure.microsoft.com/pricing/details/security-center/).

## <a name="associated-costs"></a>Kapcsolódó költségek

A IoT Defender olyan költségeket tartalmaz, amelyek nem részei a közvetlen díjszabásnak:

- Tárolási költségek Log Analytics

Az egyes megoldási funkciók leválasztásával csökkentheti a kapcsolódó költségeket. A beállítások módosításával letilthatja a beállításokat.

Beállítások módosítása:

1. Nyissa meg IoT Hub.

1. A **Biztonság** alatt kattintson a **Beállítások** elemre.

1. Kattintson **az adatgyűjtés** elemre.

Az alábbi táblázat az egyes lehetőségek kapcsolódó költségeinek és következményeinek összegzését tartalmazza.

| Beállítás | Használat | Megjegyzés |
| --- | --- | --- |
| **Log Analytics Storage** |  |
| Eszközökre vonatkozó javaslatok és riasztások| A szolgáltatás által létrehozott biztonsági javaslatok és riasztások | Nem kötelező |
| Nyers biztonsági adatértékek| A biztonsági ügynökök által gyűjtött IoT-eszközökből származó nyers biztonsági adatok | A _nyers eszközök biztonsági eseményeinek tárolása_ letiltva |
|

>[!Important]
> A kivonás jelentős hatással van a Defender IoT biztonsági funkcióinak rendelkezésre állására.

| Leiratkozás | Következmények |
| --- | --- |
| _Twin metadata-gyűjtemény_ | [Egyéni riasztások](quickstart-create-custom-alerts.md) letiltása |
| | IoT Edge manifest-javaslatok letiltása |
| | Eszköz-identitás-alapú javaslatok és riasztások letiltása |
| _Nyers eszközök biztonsági eseményeinek tárolása_ | Az eszköz operációsrendszer-alapkonfigurációjának javaslatairól nem érhetők el adatok |
| | A [riasztások](concept-security-alerts.md) és [javaslatok](concept-recommendations.md) vizsgálatának részletei nem érhetők el |
|

## <a name="see-also"></a>További információ

- A [nyers biztonsági adataihoz](how-to-security-data-access.md) való hozzáférés
- [Eszköz vizsgálata](how-to-investigate-device.md)
- A [biztonsági javaslatok](concept-recommendations.md) megismerése és megismerése
- A [biztonsági riasztások](concept-security-alerts.md) megismerése és megismerése
