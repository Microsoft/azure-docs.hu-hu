---
title: AWS-CloudTrail összekötése az Azure Sentinel szolgáltatással | Microsoft Docs
description: Ismerje meg, hogyan csatlakoztatható az AWS CloudTrail-adatszolgáltatások az Azure Sentinelhez.
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
ms.date: 05/27/2020
ms.author: yelevin
ms.openlocfilehash: 6f4b5713aed6bae574c30f555ef7f63307138a96
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84014978"
---
# <a name="connect-azure-sentinel-to-aws-cloudtrail"></a>Az Azure Sentinel összekötése AWS-CloudTrail

Az AWS-összekötő használatával továbbíthatja az AWS-CloudTrail összes eseményét az Azure Sentinelbe. Ez a kapcsolati folyamat delegálja az Azure Sentinel hozzáférését az AWS-erőforrás naplóihoz, és megbízhatósági kapcsolatot létesít az AWS CloudTrail és az Azure Sentinel között. Ez az AWS-ben valósítható meg egy olyan szerepkör létrehozásával, amely engedélyt ad az Azure Sentinelnek az AWS-naplók elérésére.

> [!NOTE]
> Az AWS CloudTrail [beépített korlátozásokkal](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/WhatIsCloudTrail-Limits.html) rendelkezik a LookupEvents API-ban. A szolgáltatás legfeljebb két tranzakció/másodperc (TPS) használatát teszi lehetővé, és mindegyik lekérdezés legfeljebb 50 rekordot tud visszaadni. Ennek következtében, ha egyetlen bérlő egy régióban másodpercenként több mint 100 rekordot generál, az adatfeldolgozás során várakozó fájlok és késések is megmaradnak.

## <a name="prerequisites"></a>Előfeltételek

Írási engedéllyel kell rendelkeznie az Azure Sentinel munkaterületen.

> [!NOTE]
> Az Azure Sentinel minden régióból gyűjt CloudTrail eseményeket. Azt javasoljuk, hogy ne továbbítsa az eseményeket egyik régióból a másikba.

## <a name="connect-aws"></a>Az AWS csatlakoztatása 


1. Az Azure Sentinelben válassza az **adatösszekötők** lehetőséget, majd válassza ki az **Amazon Web Services** sort a táblázatban, majd az AWS ablaktáblán a jobb oldalon kattintson az **összekötő lap megnyitása**lehetőségre.

1. A következő lépések végrehajtásával kövesse a **konfiguráció** szakasz utasításait.
 
1.  A Amazon Web Services-konzol **biztonság, identitás & megfelelőség**területén válassza a **iam**lehetőséget.

    ![AWS1](./media/connect-aws/aws-1.png)

1.  Válassza a **szerepkörök** lehetőséget, majd válassza a **szerepkör létrehozása**lehetőséget.

    ![AWS2](./media/connect-aws/aws-2.png)

1.  Válasszon **egy másik AWS-fiókot.** A **fiókazonosító** mezőben adja meg a Microsoft- **fiók azonosítóját** (**123412341234**), amely az Azure Sentinel portál AWS-összekötő lapján található.

    ![AWS3](./media/connect-aws/aws-3.png)

1.  Győződjön meg arról, hogy a **külső azonosító megkövetelése** van kiválasztva, majd adja meg a külső azonosítót (munkaterület-azonosítót), amely az Azure Sentinel portál AWS-összekötő lapján található.

    ![AWS4](./media/connect-aws/aws-4.png)

1.  Az **engedélyek csatolása házirendben** válassza a **AWSCloudTrailReadOnlyAccess**lehetőséget.

    ![AWS5](./media/connect-aws/aws-5.png)

1.  Adjon meg egy címkét (nem kötelező).

    ![AWS6](./media/connect-aws/aws-6.png)

1.  Ezután adja meg a **szerepkör nevét** , és kattintson a **szerepkör létrehozása** gombra.

    ![AWS7](./media/connect-aws/aws-7.png)

1.  A szerepkörök listában válassza ki a létrehozott szerepkört.

    ![AWS8](./media/connect-aws/aws-8.png)

1.  Másolja az **ARN szerepkört**. Az Azure Sentinel portálon, a Amazon Web Services összekötő képernyőjén illessze be azt a **szerepkörbe a mező hozzáadásához** , majd kattintson a **Hozzáadás**gombra.

    ![AWS9](./media/connect-aws/aws-9.png)

1. Az AWS-események Log Analytics vonatkozó sémájának használatához keresse meg a **AWSCloudTrail**.



## <a name="next-steps"></a>További lépések
Ebből a dokumentumból megtudhatta, hogyan csatlakoztatható az AWS CloudTrail az Azure Sentinelhez. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Ismerje meg, hogyan tekintheti meg [az adatait, és hogyan érheti el a potenciális fenyegetéseket](quickstart-get-visibility.md).
- Ismerje meg [a fenyegetések észlelését az Azure sentinelben](tutorial-detect-threats-built-in.md).
- Az adatait a [munkafüzetek használatával](tutorial-monitor-your-data.md) figyelheti.

