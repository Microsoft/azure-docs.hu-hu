---
title: Azure Active Directory jelentési késések | Microsoft Docs
description: Ismerje meg, hogy mennyi ideig tart a jelentéskészítési események megjelenítése a Azure Portal
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 9b88958d-94a2-4f4b-a18c-616f0617a24e
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 05/13/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0498ee1c57cfa661884fe3209d4e089b54996fae
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/01/2020
ms.locfileid: "89231061"
---
# <a name="azure-active-directory-reporting-latencies"></a>Az Azure Active Directory jelentéskészítés késése

A késés az Azure Active Directory (Azure AD) jelentéskészítési adatainak a [Azure Portalban](https://portal.azure.com)való megjelenítéséhez szükséges idő. Ez a cikk a különböző típusú jelentések várható késését sorolja fel. 

## <a name="activity-reports"></a>Tevékenységjelentések

Kétféle tevékenységi jelentés létezik:

- [Bejelentkezések](concept-sign-ins.md) – információkat biztosít a felügyelt alkalmazások és a felhasználói bejelentkezési tevékenységek használatáról
- [Naplók](concept-audit-logs.md) – rendszertevékenységi információkat biztosít a felhasználókról és csoportokról, a felügyelt alkalmazásokról és a címtárbeli tevékenységekről

A következő táblázat a tevékenységi jelentések késési információit sorolja fel. 

> [!NOTE]
> A **késés (95. percentilis)** a naplók 95%-ában jelentett időpontra utal, a **késés (esetek 99% percentilis)** pedig arra az időre utal, ameddig a naplók 99%-a jelenteni fog. 
>

| Jelentés | Késés (95. percentilis) |Késés (esetek 99% percentilis)|
| :-- | --- | --- |
| Naplók | 2 perc  | 5 perc  |
| Bejelentkezések | 2 perc  | 5 perc |

### <a name="how-soon-can-i-see-activities-data-after-getting-a-premium-license"></a>Milyen hamar láthatom a tevékenységek információit a prémium szintű licenc beszerzése után?

Ha már rendelkezik a tevékenységek adataival az ingyenes licenccel, akkor azonnal megtekintheti a frissítést. Ha nem rendelkezik az adataival, a rendszer egy vagy két napot is igénybe vesz, hogy az adatai megjelenjenek a jelentésekben a prémium szintű licencre való frissítés után.

## <a name="security-reports"></a>Biztonsági jelentések

A biztonsági jelentések két típusa létezik:

- [Kockázatos bejelentkezések](../identity-protection/overview-identity-protection.md) – A kockázatos bejelentkezés egy olyan bejelentkezési kísérletet jelöl, amelyet elképzelhető, hogy olyan személy hajtott végre, aki nem a felhasználói fiók jogos tulajdonosa. 
- [Kockázatosként megjelölt felhasználók](../identity-protection/overview-identity-protection.md) – A kockázatos felhasználó egy olyan felhasználói fiókot jelöl, amelynek elképzelhető, hogy sérült a biztonsága. 

A következő táblázat a biztonsági jelentések késési információit sorolja fel.

| Jelentés | Minimum | Average | Maximum |
| :-- | --- | --- | --- |
| Veszélyeztetett felhasználók          | 5 perc   | 15 perc  | 2 óra  |
| Kockázatos bejelentkezések         | 5 perc   | 15 perc  | 2 óra  |

## <a name="risk-detections"></a>Kockázatészlelések

Az Azure AD adaptív gépi tanulási algoritmusokat és heurisztikus műveleteket használ a felhasználói fiókokhoz kapcsolódó gyanús műveletek észlelésére. A rendszer minden észlelt gyanús műveletet egy **kockázati észlelésnek**nevezett rekordban tárol.

A következő táblázat a kockázati észlelések késési információit sorolja fel.

| Jelentés | Minimum | Average | Maximum |
| :-- | --- | --- | --- |
| Bejelentkezések névtelen IP-címről |5 perc |15 perc |2 óra |
| Bejelentkezések ismeretlen helyekről |5 perc |15 perc |2 óra |
| Felhasználók, akiknek kiszivárogtak a hitelesítő adatai |2 óra |4 óra |8 óra |
| Bejelentkezés szokatlan helyekről |5 perc |1 óra |8 óra  |
| Bejelentkezések fertőzött eszközökről |2 óra |4 óra |8 óra  |
| Bejelentkezések gyanús tevékenységeket mutató IP-címekkel |2 óra |4 óra |8 óra  |


## <a name="next-steps"></a>Következő lépések

* [Azure AD-jelentések – áttekintés](overview-reports.md)
* [Programozott hozzáférés az Azure AD-jelentésekhez](concept-reporting-api.md)
* [Azure Active Directory kockázati észlelések](../identity-protection/overview-identity-protection.md)