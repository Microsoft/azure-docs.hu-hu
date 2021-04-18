---
title: Alcide kAudit-adatok csatlakoztatása Azure Sentinel| Microsoft Docs
description: Ismerje meg, hogyan csatlakoztathatja az Alcide kAudit-adatokat Azure Sentinel.
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
ms.date: 06/21/2020
ms.author: yelevin
ms.openlocfilehash: b146e228de13109975a76b0e4c6c9fd183fd362d
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600404"
---
# <a name="connect-your-alcide-kaudit-to-azure-sentinel"></a>Az Alcide kAudit csatlakoztatása a Azure Sentinel

> [!IMPORTANT]
> Az Alcide kAudit adat-összekötő Azure Sentinel jelenleg nyilvános előzetes verzióban érhető el.
> Ez a funkció szolgáltatói szerződés nélkül biztosított, és nem ajánlott éles számítási feladatokhoz. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[Az Alcide kAudit](https://www.alcide.io/kaudit-K8s-forensics/) segítségével azonosíthatja a rendellenes Kubernetes-viselkedéseket, és a Kubernetes-incidensekkel és -incidensekkel kapcsolatos problémákra összpontosíthat, miközben csökkenti az észlelési időt. Ez a cikk azt ismerteti, hogyan csatlakoztathatja az Alcide kAudit megoldást a Azure Sentinel. Az Alcide kAudit adat-összekötő lehetővé teszi a kAudit-naplóadatok egyszerű behozását az Azure Sentinel-ba, így megtekintheti azokat munkafüzetekben, egyéni riasztásokat hozhat létre, és beépítheti őket a vizsgálat javítása érdekében. Az Alcide kAudit és a Azure Sentinel közötti integráció a REST API.

> [!NOTE]
> Az adatok annak a munkaterületnek a földrajzi helyén lesznek tárolva, amelyen a munkaterületet Azure Sentinel.

## <a name="prerequisites"></a>Előfeltételek

- Olvasási és írási engedéllyel kell rendelkeznie a Azure Sentinel munkaterületen.

- A munkaterület megosztott kulcsait olvasási engedélyekkel kell rendelkeznie.

## <a name="configure-and-connect-alcide-kaudit"></a>Az Alcide kAudit konfigurálása és csatlakoztatása

Az Alcide kAudit közvetlenül exportálhatja a naplókat a Azure Sentinel.

1. A Azure Sentinel a navigációs **menüben** kattintson az Adat-összekötők elemre.

1. A katalógusban válassza az **Alcide kAudit** lehetőséget, majd kattintson az **Összekötő oldal megnyitása gombra.**

1. Kövesse az [Alcide kAudit](https://awesomeopensource.com/project/alcideio/kaudit?categoryPage=29#before-installing-alcide-kaudit)telepítési útmutatóban található részletes utasításokat.

1. Ha a rendszer kéri a munkaterület azonosítóját és az elsődleges kulcsot, átmásolhatja őket az Alcide kAudit adat-összekötő oldalára.

    :::image type="content" source="media/connect-alcide-kaudit/alcide-workspace-id-primary-key.png" alt-text="Munkaterület-azonosító és elsődleges kulcs":::

## <a name="find-your-data"></a>Az adatok megkeresása

A sikeres kapcsolat létrejötte után az  adatok a Következő adattípusok alatt jelennek meg a Naplókban a **CustomLogsban:**

- **alcide_kaudit_detections_1_CL** – Alcide kAudit észlelése 
- **alcide_kaudit_activity_1_CL** – Alcide kAudit tevékenységnaplók
- **alcide_kaudit_selections_count_1_CL** – Alcide kAudit-tevékenységek száma
- **alcide_kaudit_selections_details_1_CL** – Alcide kAudit tevékenység részletei

Az Alcide kAudit naplóiban a megfelelő séma használatával keresse meg a fent említett adattípusokat.

Akár 20 percig is eltarthat, amíg a naplók megjelennek a Log Analyticsben.

## <a name="next-steps"></a>Következő lépések

Ebben a dokumentumban megtanulta, hogyan csatlakoztathatja az Alcide kAuditet a Azure Sentinel. Az adat-összekötő beépített képességeinek teljes körű kihasználása  érdekében kattintson az adat-összekötő oldalÁnak További lépések lapjára. Itt talál néhány használatra kész mintalekérdezéseket, hogy elkezdhet hasznos információkat találni.

A további Azure Sentinel a következő cikkekben talál:

- Megtudhatja, [hogyan olvashatja be az adatokat és a lehetséges fenyegetéseket.](quickstart-get-visibility.md)
- A fenyegetések [észlelésének első Azure Sentinel.](tutorial-detect-threats-built-in.md)
- [Munkafüzetek használata](tutorial-monitor-your-data.md) az adatok figyelése érdekében.
