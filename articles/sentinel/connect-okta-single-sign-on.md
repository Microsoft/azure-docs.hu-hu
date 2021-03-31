---
title: Okta-Sign-On-adatkapcsolat összekötése az Azure Sentinel szolgáltatással | Microsoft Docs
description: Ismerje meg, hogyan csatlakoztathatók a okta-Sign-Oni az Azure Sentinel szolgáltatáshoz.
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
ms.date: 07/17/2020
ms.author: yelevin
ms.openlocfilehash: e76aea8a3fc59827664900a6d5686e2e725e258d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100093115"
---
# <a name="connect-your-okta-single-sign-on-to-azure-sentinel-with-azure-function"></a>Az okta egyetlen Sign-On összekötése az Azure Sentinel szolgáltatással az Azure Function használatával

> [!IMPORTANT]
> A okta Single Sign-On adatösszekötő az Azure Sentinel szolgáltatásban jelenleg nyilvános előzetes verzióban érhető el.
> Ez a szolgáltatás szolgáltatói szerződés nélkül érhető el, és éles számítási feladatokhoz nem ajánlott. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Az okta Single Sign-On (SSO) összekötővel könnyedén csatlakoztathatja az összes [Sign-On okta-alapú (SSO)](https://www.okta.com/products/single-sign-on/) biztonsági megoldás naplóit az Azure Sentinel használatával, megtekintheti az irányítópultokat, egyéni riasztásokat hozhat létre, és javíthatja a vizsgálatot. A okta Single Sign-On és az Azure Sentinel közötti integráció lehetővé teszi, hogy a Azure Functions REST API használatával lekérje a naplózási adataikat.

> [!NOTE]
> Az Azure Sentinel-t futtató munkaterület földrajzi helye tárolja az adatmennyiséget.

## <a name="configure-and-connect-okta-single-sign-on"></a>Okta egyetlen Sign-On konfigurálása és összekapcsolása

Azure Functions az eseményeket és naplókat közvetlenül az okta egyetlen Sign-On integrálhatja és lehívhatja, és továbbíthatja őket az Azure Sentinel szolgáltatásba.

1. Az Azure Sentinel portálon kattintson az **adatösszekötők** lehetőségre, és válassza az **okta egyszeri bejelentkezés** összekötőt.

1. Válassza az **összekötő megnyitása lapot**.

1. Kövesse az **okta egyszeri bejelentkezés** oldalán található utasításokat.

## <a name="find-your-data"></a>Az adatai megkeresése

A sikeres kapcsolatok létrejötte után az adat a **Okta_CL** táblázat alatt log Analytics jelenik meg.

## <a name="validate-connectivity"></a>Kapcsolat ellenőrzése

Akár 20 percet is igénybe vehet, amíg a naplók meg nem kezdődnek a Log Analytics.

## <a name="next-steps"></a>Következő lépések

Ebből a dokumentumból megtudhatta, hogyan csatlakoztatható az okta egyetlen Sign-On az Azure Sentinelhez az Azure Function Apps használatával. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:

- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).
- Az adatait a [munkafüzetek használatával](tutorial-monitor-your-data.md) figyelheti.

