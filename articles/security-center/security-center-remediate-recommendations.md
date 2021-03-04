---
title: Javaslatok szervizelése Azure Security Centerban | Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan lehet reagálni a Azure Security Center ajánlásaira az erőforrások védelme és a biztonsági szabályzatok kielégítése érdekében.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 8be947cc-cc86-421d-87a6-b1e23077fd50
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/08/2020
ms.author: memildin
ms.openlocfilehash: 6222491d35317e549a3d21f29395b9c25813ea9f
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102100355"
---
# <a name="remediate-recommendations-in-azure-security-center"></a>Helyreállítási javaslatok az Azure Security Centerben

A javaslatok segítséget nyújtanak az erőforrások jobb biztonságossá tételéhez. A javaslatot a javaslatban megadott szervizelési lépések követésével hajthatja végre.

## <a name="remediation-steps"></a>Szervizelési lépések <a name="remediation-steps"></a>

Az összes javaslat áttekintése után döntse el, hogy melyiket kell elsőként kijavítani. Javasoljuk, hogy rangsorolja a biztonsági ellenőrzéseket a lehető legnagyobb potenciállal a biztonságos pontszám növeléséhez.

1. Válasszon ki egy javaslatot a listából.

1. Kövesse a **szervizelési lépések** szakaszban található utasításokat. Minden javaslathoz saját utasítások vannak megadva. Az alábbi képernyőképen az alkalmazások konfigurálásához szükséges szervizelési lépések láthatók a HTTPS protokollon keresztüli adatforgalom engedélyezéséhez.

    :::image type="content" source="./media/security-center-remediate-recommendations/security-center-remediate-recommendation.png" alt-text="Javaslatok manuális szervizelési lépései" lightbox="./media/security-center-remediate-recommendations/security-center-remediate-recommendation.png":::

1. Ha elkészült, egy értesítés jelenik meg, amely tájékoztatja, hogy a probléma megoldódott-e.

## <a name="quick-fix-remediation"></a>Gyors javítás szervizelése

A szervizelés egyszerűsítése és a környezet biztonságának javítása érdekében (és a biztonságos pontszám növeléséhez) számos javaslat tartalmaz egy gyors javítási lehetőséget.

A gyors javítással gyorsan szervizelheti a több erőforrásra vonatkozó javaslatait.

> [!TIP]
> A gyors javítási megoldások csak bizonyos javaslatok esetében érhetők el. Az elérhető gyors javítással rendelkező javaslatok megkereséséhez használja a **Response Actions** szűrőt a javaslatok listájához:
> 
> :::image type="content" source="media/security-center-remediate-recommendations/quick-fix-filter.png" alt-text="A javaslatok lista fölötti szűrők használatával megtalálhatja a gyors javítás lehetőséget tartalmazó javaslatokat":::

Gyors javítási megoldás megvalósítása:

1. A **gyors javítást** tartalmazó javaslatok listájáról címkére, válasszon ki egy javaslatot.

    [![Válassza a gyors javítás lehetőséget.](media/security-center-remediate-recommendations/security-center-quick-fix-select.png)](media/security-center-remediate-recommendations/security-center-quick-fix-select.png#lightbox)

1. A nem kifogástalan **erőforrások** lapon válassza ki azokat az erőforrásokat, amelyekhez javaslatot kíván végrehajtani, majd válassza a **szervizelés** lehetőséget.

    > [!NOTE]
    > Előfordulhat, hogy a felsorolt erőforrások némelyike le van tiltva, mert nem rendelkezik a megfelelő engedélyekkel a módosításhoz.

1. A megerősítés mezőben olvassa el a szervizelés részleteit és a következményeket.

    ![Gyors javítás](./media/security-center-remediate-recommendations/security-center-quick-fix-view.png)

    > [!NOTE]
    > A következmények a javítás után megnyíló **erőforrások szervizelése** ablak szürke mezőjében jelennek **meg.** Felsorolja, hogy milyen változások történnek a gyors javítás szervizelésének folytatásakor.

1. Szükség esetén szúrja be a megfelelő paramétereket, majd hagyja jóvá a szervizelést.

    > [!NOTE]
    > A szervizelés befejezése után több percet is igénybe vehet, hogy az erőforrások megjelenjenek az **egészséges erőforrások** lapon. A Szervizelési műveletek megtekintéséhez tekintse meg a [tevékenység naplóját](#activity-log).

1. Ha elkészült, egy értesítés jelenik meg, amely tájékoztatja, ha a szervizelés sikeres volt.

## <a name="quick-fix-remediation-logging-in-the-activity-log"></a>Szervizelési napló gyors javítása a műveletnapló esetében <a name="activity-log"></a>

A Szervizelési művelet sablon-telepítési vagy REST-javító API-hívást használ a konfiguráció alkalmazásához az erőforráson. Ezeket a műveleteket az [Azure-tevékenység naplójában](../azure-resource-manager/management/view-activity-logs.md)naplózza a rendszer.


## <a name="next-steps"></a>Következő lépések

Ebből a dokumentumból megtudhatta, hogyan javíthatja az ajánlásokat a Security Centerban. Ha többet szeretne megtudni a Security Centerről, tekintse meg a következő lapokat:

* [Biztonsági szabályzatok beállítása Azure Security Centerban](tutorial-security-policy.md) – megtudhatja, hogyan konfigurálhat biztonsági házirendeket az Azure-előfizetések és-erőforráscsoportok számára
* [Mik azok a biztonsági szabályzatok, kezdeményezések és javaslatok?](security-policy-concept.md)