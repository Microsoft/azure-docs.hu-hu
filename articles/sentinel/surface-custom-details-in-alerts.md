---
title: Surface – egyéni adatok az Azure Sentinel-riasztásokban | Microsoft Docs
description: Az Azure Sentinel Analytics-szabályokban az egyéni esemény részleteinek kinyerése és felszíne az incidensek jobb és teljesebb körű tájékoztatása érdekében
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
ms.date: 02/10/2021
ms.author: yelevin
ms.openlocfilehash: 45f0ef5366d97c275c40d4d436020dbaf3501d42
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2021
ms.locfileid: "102456225"
---
# <a name="surface-custom-event-details-in-alerts-in-azure-sentinel"></a>A Surface egyéni esemény részletei a riasztásokban az Azure Sentinelben 

> [!IMPORTANT]
>
> - Az egyéni részletek funkció **előzetes** verzióban érhető el. Tekintse meg a kiegészítő [használati feltételeket a Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) előzetes verziókra vonatkozó további jogi feltételekhez, amelyek olyan Azure-szolgáltatásokra vonatkoznak, amelyek a bétaverzióban, az előzetes verzióban vagy más esetben még nem jelent meg általánosan elérhetővé.

## <a name="introduction"></a>Bevezetés

Az [ütemezett lekérdezési elemzési szabályok](tutorial-detect-threats-custom.md) az Azure sentinelhez kapcsolódó adatforrásokból elemzik az **eseményeket** , és **riasztásokat** hoznak létre, ha az események tartalma jelentős biztonsági szempontból. Ezeket a riasztásokat az Azure Sentinel különböző motorjai elemzik, csoportosítják és szűrik, és olyan **incidensekre** pároljuk őket, amelyek a SoC elemző figyelmét igénylik. Ha azonban az elemző megtekinti az incidenst, a rendszer csak az összetevő-riasztások tulajdonságait látja el azonnal. A tényleges tartalom beolvasása – az eseményekben található információk – a használatához némi ásás szükséges.

Az **elemzési szabály varázsló** **Custom Details (egyéni** adatok) funkciójával az események adatait felhasználhatja az ezekből az eseményekről készített riasztásokban, így a riasztási tulajdonságok az Event adat részét képezik. Ennek hatására a szolgáltatás azonnali esemény-tartalmi láthatóságot biztosít az incidensekben, így lehetővé teszi a következtetések osztályozását, kivizsgálását, leállítását, valamint a sokkal nagyobb sebesség és hatékonyság érdekében történő reagálást.

Az alább részletezett eljárás az elemzési szabály létrehozása varázsló részét képezi. Itt egymástól függetlenül kezeljük az egyéni adatok hozzáadásának vagy módosításának forgatókönyvét egy meglévő elemzési szabályban.

## <a name="how-to-surface-custom-event-details"></a>Az egyéni esemény részleteinek beszerzése

1. Az Azure Sentinel navigációs menüjében válassza az **elemzés** lehetőséget.

1. Válasszon ki egy ütemezett lekérdezési szabályt, és kattintson a **Szerkesztés** gombra. Vagy hozzon létre egy új szabályt a képernyő felső részén található **&#10132; ütemezett lekérdezési szabály létrehozása** elemre kattintva.

1. Kattintson a **szabály logikájának beállítása** fülre.

1. A **riasztás javítása** szakaszban válassza az **Egyéni adatok** lehetőséget.

    :::image type="content" source="media/surface-custom-details-in-alerts/alert-enhancement.png" alt-text="Egyéni adatok keresése és kiválasztása":::

1. A most kibontott **Egyéni részletek** szakaszban adja hozzá a felszínhez használni kívánt adatoknak megfelelő kulcs-érték párokat:

    1. A **kulcs** mezőben adja meg az Ön által választott nevet a riasztások mezőjében.

    1. Az **érték** mezőben válassza ki azt az Event paramétert, amelyet a riasztásokban szeretne felvenni a legördülő listából. A lista a szabály lekérdezésének tárgyát képező táblák mezőinek megfelelő értékek alapján lesz feltöltve.
    
        :::image type="content" source="media/surface-custom-details-in-alerts/custom-details.png" alt-text="Egyéni adatok hozzáadása":::

1. Kattintson az **új hozzáadása** a Surface további részletekhez lehetőségre, és ismételje meg az utolsó lépéseket a kulcs-érték párok definiálásához. 

    Ha meggondolja magát, vagy ha elvégezte a hibát, eltávolíthat egy egyéni részletet az **érték** legördülő lista melletti Kuka ikonra kattintva.

1. Ha befejezte az egyéni adatok definiálását, kattintson a **felülvizsgálat és létrehozás** fülre. Ha a szabály ellenőrzése sikeres, kattintson a **Mentés** gombra.

    > [!NOTE]
    > 
    >**Szolgáltatási korlátozások**
    > - Egyetlen elemzési szabályban **legfeljebb 20 egyéni részletet** adhat meg.
    >
    > - Az összes egyéni részletre vonatkozó méretkorlát (együttesen) **2 kb**.

## <a name="next-steps"></a>Következő lépések
Ebből a dokumentumból megtudhatta, hogyan végezheti el az egyéni adatok felszínét a riasztásokban az Azure Sentinel Analytics szabályainak használatával. Az Azure Sentinel szolgáltatással kapcsolatos további tudnivalókért tekintse meg a következő cikkeket:
- Az [ütemezett lekérdezési elemzési szabályok](tutorial-detect-threats-custom.md)teljes képének beolvasása.
- További információ az [Azure Sentinel-beli entitásokról](entities-in-azure-sentinel.md).
