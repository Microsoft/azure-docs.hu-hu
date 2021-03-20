---
title: A tevékenység naplóinak hiányzó adathibáinak megoldása | Microsoft Docs
description: Az Azure Active Directory-tevékenységnaplókból hiányzó adatok problémájára nyújt megoldást.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 7cbe4337-bb77-4ee0-b254-3e368be06db7
ms.service: active-directory
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2f48c26a65314e2f23513fba155f07db3805a516
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93123064"
---
# <a name="troubleshoot-missing-data-in-the-azure-active-directory-activity-logs"></a>Hibaelhárítás: Hiányzó adatok az Azure Active Directory-tevékenységnaplókban 

## <a name="i-cant-find-audit-logs-for-recent-actions-in-the-azure-portal"></a>Nem találom a legutóbbi tevékenységek auditnaplóit az Azure Portalon

### <a name="symptoms"></a>Hibajelenségek

Végrehajtottam bizonyos műveleteket az Azure Portalon, és arra számítottam, hogy látom a műveletek naplóit a `Activity logs > Audit Logs` panelen, de nem találtam meg őket.

 ![A képernyőképen a naplók bejegyzései láthatók.](./media/troubleshoot-missing-audit-data/01.png)
 
### <a name="cause"></a>Ok

A műveletek nem jelennek meg azonnal a tevékenységnaplókban. Az alábbi táblázat a tevékenységnaplók késési számait sorolja fel. 

| Jelentés | Késés (P95) | Késés (P99) |
|--------|---------------|---------------|
| Címtárnaplózás | 2 perc | 5 perc |
| Bejelentkezési tevékenység | 2 perc | 5 perc |

### <a name="resolution"></a>Feloldás

Várjon, hogy a műveletek úgy 15 perc és két óra közötti időtartam elteltével megjelennek-e a naplóban. Ha két óra elteltével sem látja a naplókat, [küldjön be egy támogatási jegyet](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest), és kivizsgáljuk az ügyet.

## <a name="i-cant-find-recent-user-sign-ins-in-the-azure-active-directory-sign-ins-activity-log"></a>Nem találom a legutóbbi felhasználói bejelentkezéseket az Azure Active Directory bejelentkezési tevékenységnaplójában

### <a name="symptoms"></a>Hibajelenségek

Nemrég bejelentkeztem az Azure Portalra, és arra számítottam, hogy látom ezeknek a bejelentkezéseknek a naplóit a `Activity logs > Sign-ins` panelen, de nem találom őket.

 ![A képernyőfelvételen a tevékenység naplójában láthatók a bejelentkezések.](./media/troubleshoot-missing-audit-data/02.png)
 
### <a name="cause"></a>Ok

A műveletek nem jelennek meg azonnal a tevékenységnaplókban. Az alábbi táblázat a tevékenységnaplók késési számait sorolja fel. 

| Jelentés | Késés (P95) | Késés (P99) |
|--------|---------------|---------------|
| Címtárnaplózás | 2 perc | 5 perc |
| Bejelentkezési tevékenység 2 perc | 5 perc |

### <a name="resolution"></a>Feloldás

Várjon, hogy a műveletek úgy 15 perc és két óra közötti időtartam elteltével megjelennek-e a naplóban. Ha két óra elteltével sem látja a naplókat, [küldjön be egy támogatási jegyet](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest), és kivizsgáljuk az ügyet.

## <a name="i-cant-view-more-than-30-days-of-report-data-in-the-azure-portal"></a>Nem jelenik meg 30 napnál több adat a jelentésekben az Azure Portalon

### <a name="symptoms"></a>Hibajelenségek

Nem jelenik meg 30 napnál több bejelentkezési és auditadat az Azure Portalról. Miért? 

 ![Képernyőfelvétel: a dátum menü.](./media/troubleshoot-missing-audit-data/03.png)

### <a name="cause"></a>Ok

A licencétől függően az Azure Active Directory-műveletek az alábbi időtartamokig tárolják a tevékenységjelentéseket:

| Jelentés           | Azure AD Free | Prémium szintű Azure AD P1 | Prémium szintű Azure AD P2 |
| ---              | ---           | ---                 | ---                 |
| Címtárnaplózás  |  7 nap       | 30 nap             | 30 nap             |
| Bejelentkezési tevékenységek | Nem érhető el. A saját bejelentkezéseit 7 napig érheti el az egyedi felhasználói profil panelről | 30 nap | 30 nap             |

További információk: [Az Azure Active Directory jelentésmegőrzési házirendje](reference-reports-data-retention.md).  

### <a name="resolution"></a>Feloldás

Az adatokat kétféleképpen őrizheti meg 30 napnál hosszabban. Az [Azure AD Reporting API-kkal](concept-reporting-api.md) az adatokat programozott módon kérheti le és tárolhatja egy adatbázisban. Másik megoldásként az auditnaplókat egy külső SIEM-rendszerbe, például a Splunk vagy a SumoLogic rendszerbe integrálhatja.

## <a name="next-steps"></a>Következő lépések

* [Azure AD-jelentésmegőrzés](reference-reports-data-retention.md).
* [Azure Active Directory jelentési késések](reference-reports-latencies.md).
* [Jelentéskészítés az Azure Active Directoryban – gyakori kérdések](reports-faq.md).

