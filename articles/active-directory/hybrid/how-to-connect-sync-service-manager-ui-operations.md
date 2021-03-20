---
title: Azure AD Connect Synchronization Service Manager műveletek | Microsoft Docs
description: A Azure AD Connect Synchronization Service Manager működés lapjának ismertetése.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 97a26565-618f-4313-8711-5925eeb47cdc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/13/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 64ab5901ad54e37fc05d7da757ce7f31d42fe3b6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "85357103"
---
# <a name="using-the-sync-service-manager-operations-tab"></a>A Sync Service Manager Operations (szinkronizálási műveletek) lap használata

![Service Manager szinkronizálása](./media/how-to-connect-sync-service-manager-ui-operations/operations.png)

Az Operations (műveletek) lapon a legutóbbi műveletek eredményei láthatók. Ez a lap kulcsfontosságú a problémák megértéséhez és hibaelhárításához.

## <a name="understand-the-information-visible-in-the-operations-tab"></a>A műveletek lapon látható információk megismerése
A felső fél az összes futtatást időrendi sorrendben jeleníti meg. Alapértelmezés szerint az operatív napló az elmúlt hét nap adatait tárolja, de ez a beállítás az [ütemező](how-to-connect-sync-feature-scheduler.md)segítségével módosítható. Olyan futtatást szeretne keresni, amely nem mutat sikeres állapotot. A rendezést a fejlécek lehetőségre kattintva módosíthatja.

Az **állapot** oszlop a legfontosabb információ, és megjeleníti a Futtatás legsúlyosabb problémáját. Itt látható a leggyakoribb állapotok rövid összefoglalása, hogy a vizsgálat elsőbbséget kapjon (ahol * több lehetséges hibát jeleznek).

| Állapot | Megjegyzés |
| --- | --- |
| megállt\* |A Futtatás nem fejeződött be. Ha például a távoli rendszer nem érhető el, és nem lehet kapcsolatba lépni vele. |
| leállítva – hiba – korlát |Több mint 5 000 hiba van. A Futtatás a hibák nagy száma miatt automatikusan leállt. |
| befejezve – \* hibák |A Futtatás befejeződött, de vannak olyan hibák (kevesebb mint 5 000), amelyeket meg kell vizsgálni. |
| befejezve – \* figyelmeztetések |A Futtatás befejeződött, de bizonyos adatértékek nem a várt állapotban vannak. Ha hibák léptek fel, akkor ez az üzenet általában csak tünet. Amíg nem válaszol a hibákra, ne vizsgálja meg a figyelmeztetéseket. |
| sikeres |Nincs probléma. |

Amikor kijelöl egy sort, a lenti frissítések megjelenítik a Futtatás részleteit. A lenti bal szélen egy lista jelenik meg, amely a következő **lépést** jelzi: #. Ez a lista csak akkor jelenik meg, ha az erdőben több tartománnyal rendelkezik, ahol minden tartományt egy lépés képvisel. A tartománynév a fejléc **partíció** alatt található. A **szinkronizálási statisztikák** területen további információkat talál a feldolgozott módosítások számáról. A hivatkozásokra kattintva lekérheti a módosított objektumok listáját. Ha hibás objektumok vannak, ezek a hibák a **szinkronizálási hibák** alatt jelennek meg.

További információ: [nem szinkronizált objektumok hibáinak megoldása](tshoot-connect-object-not-syncing.md)

## <a name="next-steps"></a>Következő lépések
További információ a [Azure ad Connect szinkronizálási](how-to-connect-sync-whatis.md) konfigurációról.

További információ: [Helyszíni identitások integrálása az Azure Active Directoryval](whatis-hybrid-identity.md).
