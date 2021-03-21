---
title: Mennyi ideig tart az Azure AD Store jelentéskészítési adatai? | Microsoft Docs
description: Megtudhatja, hogy az Azure milyen hosszú ideig tárolja a különböző jelentési adattípusokat.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 183e53b0-0647-42e7-8abe-3e9ff424de12
ms.service: active-directory
ms.devlang: ''
ms.topic: reference
ms.tgt_pltfrm: ''
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/05/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4e240e8ec53ce007be1a858af7b6e41273ca8831
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97093638"
---
# <a name="how-long-does-azure-ad-store-reporting-data"></a>Mennyi ideig tart az Azure AD Store jelentéskészítési adatai?


Ebből a cikkből megtudhatja, hogyan használhatók az adatmegőrzési szabályzatok a Azure Active Directory különböző tevékenységi jelentéseihez. 

### <a name="when-does-azure-ad-start-collecting-data"></a>Mikor kezdi az Azure AD az adatok gyűjtését?

| Azure AD-kiadás | Gyűjtemény kezdete |
| :--              | :--   |
| Prémium szintű Azure AD P1 <br /> Prémium szintű Azure AD P2 | Ha előfizetést regisztrál |
| Azure AD Free| Amikor először nyitja meg a [Azure Active Directory](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) panelt, vagy a [jelentéskészítő API-kat](./overview-reports.md) használja  |

---

### <a name="when-is-the-activity-data-available-in-the-azure-portal"></a>Mikor érhetők el a tevékenységek a Azure Portal?

- **Azonnal** – ha már dolgozik a Azure Portal jelentéseivel.
- **2 órán belül** – ha nem kapcsolta be a jelentéskészítést a Azure Portalban.

---

### <a name="how-soon-can-i-see-activities-data-after-getting-a-premium-license"></a>Milyen hamar láthatom a tevékenységek információit a prémium szintű licenc beszerzése után?

Ha már rendelkezik a tevékenységek adataival az ingyenes licenccel, akkor azonnal megtekintheti a frissítést. Ha nem rendelkezik az adataival, akkor az adatjelentések a prémium szintű licencre való frissítés után akár három napig is eltarthat.

---

### <a name="when-does-azure-ad-start-collecting-security-signal-data"></a>Mikor kezdi meg az Azure AD a biztonsági szignálok adatgyűjtését?  

A biztonsági jelek esetében a begyűjtési folyamat elindul, amikor bekapcsolja az **Identity Protection centert**. 

---

### <a name="how-long-does-azure-ad-store-the-data"></a>Mennyi ideig tárolja az Azure AD az adattárolást?

**Tevékenységjelentések**    

| Jelentés                 | Azure AD Free | Prémium szintű Azure AD P1 | Prémium szintű Azure AD P2 |
| :--                    | :--           | :--                 | :--                 |
| Naplók             | 7 nap        | 30 nap             | 30 nap             |
| Bejelentkezések               | 7 nap        | 30 nap             | 30 nap             |
| Azure AD MFA-használat        | 30 nap       | 30 nap             | 30 nap             |

A naplózási és a bejelentkezési tevékenységek adatai hosszabbak maradnak, mint az alapértelmezett megőrzési időtartam, amelyet a fentiekben ismertetünk, ha Azure Monitor használatával irányítja át az Azure Storage-fiókba. További információ: [Azure ad-naplók archiválása Azure Storage-fiókba](quickstart-azure-monitor-route-logs-to-storage-account.md).

**Biztonsági jelek**

| Jelentés         | Azure AD Free | Prémium szintű Azure AD P1 | Prémium szintű Azure AD P2 |
| :--            | :--           | :--                 | :--                 |
| Veszélyeztetett felhasználók  | 7 nap        | 30 nap             | 90 nap             |
| Kockázatos bejelentkezések | 7 nap        | 30 nap             | 90 nap             |

---

### <a name="can-i-see-last-months-data-after-getting-an-azure-ad-premium-license"></a>A prémium szintű Azure AD-licenc beszerzése után láthatom a múlt havi adatszolgáltatást?

**Nem**. Az Azure akár hét napos tevékenységi adatmennyiséget tárol az ingyenes verzióhoz. Ez azt jelenti, hogy ha az ingyenesről a prémium verzióra vált, legfeljebb 7 nap adatait láthatja.

---
