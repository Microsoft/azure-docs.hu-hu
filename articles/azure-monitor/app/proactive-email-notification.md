---
title: Intelligens észlelési értesítés módosítása – Azure Application Insights
description: Váltson az alapértelmezett értesítési címzettekre az intelligens észleléstől. Az intelligens észlelés lehetővé teszi az alkalmazások nyomkövetésének figyelését az Azure Application Insights a nyomkövetési telemetria szokatlan mintáit.
ms.topic: conceptual
author: harelbr
ms.author: harelbr
ms.date: 03/13/2019
ms.reviewer: mbullwin
ms.openlocfilehash: 8e2bf4e451ebc3c9ebba2c01dae6703fc79aa606
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87324131"
---
# <a name="smart-detection-e-mail-notification-change"></a>Az e-mail értesítések intelligens észlelésének változása

Az ügyfelek visszajelzései alapján 2019 április 1-jén módosítjuk azokat az alapértelmezett szerepköröket, amelyek e-mail-értesítéseket fogadnak az intelligens észleléstől.

## <a name="what-is-changing"></a>Mi változik?

Jelenleg az intelligens észlelési értesítő e-maileket a rendszer alapértelmezés szerint az _előfizetés tulajdonosa_, az _előfizetés közreműködői_és az _előfizetés-olvasó_ szerepkörei számára küldi el. Ezek a szerepkörök gyakran tartalmaznak olyan felhasználókat, akik aktívan nem vesznek részt a figyelésben, ami miatt a felhasználók sok esetben szükségtelenül kapják meg az értesítéseket. A felhasználói élmény javítása érdekében az e-mail értesítések csak a [figyelési olvasó](../../role-based-access-control/built-in-roles.md#monitoring-reader) és a [közreműködői szerepkörök figyelését](../../role-based-access-control/built-in-roles.md#monitoring-contributor) teszik elérhetővé.

## <a name="scope-of-this-change"></a>A módosítás hatóköre

Ez a módosítás az összes intelligens észlelési szabályra hatással lesz, a következők kivételével:

* Az intelligens észlelési szabályok előzetesként vannak megjelölve. Ezek az intelligens észlelési szabályok jelenleg nem támogatják az e-mail-értesítéseket.

* Hiba anomália szabály. Ez a szabály elindítja az új alapértelmezett szerepkörök célzását, miután áttelepítette azt egy klasszikus riasztásból az egyesített riasztások platformra (További információ [itt](../platform/monitoring-classic-retirement.md)található.)

## <a name="how-to-prepare-for-this-change"></a>Felkészülés ehhez a változáshoz

Annak biztosítása érdekében, hogy az intelligens észlelésről érkező e-mailes értesítések a megfelelő felhasználók számára legyenek elküldve, ezeket a felhasználókat hozzá kell rendelni a [figyelési olvasóhoz](../../role-based-access-control/built-in-roles.md#monitoring-reader) vagy az előfizetés [figyelő közreműködői](../../role-based-access-control/built-in-roles.md#monitoring-contributor) szerepköreihez.

Ha felhasználókat szeretne hozzárendelni a figyelési olvasóhoz vagy a közreműködő szerepkörökhöz a Azure Portalon keresztül, kövesse a [szerepkör-hozzárendelés hozzáadása](../../role-based-access-control/role-assignments-portal.md#add-a-role-assignment) című cikkben ismertetett lépéseket. Ügyeljen arra, hogy a _figyelési olvasót_ vagy a _figyelő közreműködőt_ adja meg a felhasználókhoz hozzárendelt szerepkörhöz.

> [!NOTE]
> Ez a változás nem érinti az intelligens észlelési értesítések adott címzettjeit, amelyeket a szabály beállításai között a _további e-mail címzettek_ lehetőséggel konfiguráltak. A címzettek továbbra is megkapják az e-mailes értesítéseket.

Ha bármilyen kérdése vagy problémája van a változással kapcsolatban, ne habozzon [kapcsolatba lépni velünk](mailto:smart-alert-feedback@microsoft.com).

## <a name="next-steps"></a>További lépések

További információ az intelligens észlelésről:

- [Hibaanomáliák](./proactive-failure-diagnostics.md)
- [Memóriavesztés](./proactive-potential-memory-leak.md)
- [Teljesítményanomáliák](./proactive-performance-diagnostics.md)

