---
title: Felhasználói adatkezelés Azure Security Centerban | Microsoft Docs
description: Megtudhatja, hogyan kezelheti a felhasználói információkat Azure Security Centerban. A felhasználói adatkezelési funkció magában foglalja az adatelérését, törlését és exportálását.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/23/2018
ms.author: memildin
ms.openlocfilehash: 5b5c78ffec736f29a481aa95426ff663199613b3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100595649"
---
# <a name="manage-user-data-in-azure-security-center"></a>Felhasználói adatok kezelése az Azure Security Centerben
Ez a cikk azt ismerteti, hogyan kezelheti a felhasználói adatokat Azure Security Centerban. A felhasználói adatkezelési funkció magában foglalja az adatelérését, törlését és exportálását.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

Az olvasó, a tulajdonos, a közreműködő vagy a fiók rendszergazdája által hozzárendelt Security Center felhasználó az eszközön belül férhet hozzá az ügyféladatok eléréséhez. A fiók-rendszergazdai szerepkörrel kapcsolatos további tudnivalókért tekintse meg az [Azure szerepköralapú hozzáférés-vezérlés beépített szerepkörei](../role-based-access-control/built-in-roles.md) című témakört, amely további információkat nyújt az olvasóról, a tulajdonosról és a közreműködő szerepkörökről. Lásd: [Azure-előfizetés rendszergazdái](../cost-management-billing/manage/add-change-subscription-administrator.md).

## <a name="searching-for-and-identifying-personal-data"></a>Személyes adatok keresése és azonosítása
A Security Center felhasználók a Azure Portal keresztül tekinthetik meg a személyes adatfájljaikat. Security Center csak a biztonsági kapcsolattartási adatokat, például az e-mail-címeket és a telefonszámokat tárolja. További információ: [a biztonsági kapcsolattartási adatok megadása a Azure Security Centerban](security-center-provide-security-contact-details.md).

A Azure Portalban a felhasználók az engedélyezett IP-konfigurációkat Security Center az igény szerinti virtuálisgép-hozzáférési funkciójával tekinthetik meg. További információ: a [virtuális gépekhez való hozzáférés kezelése igény](security-center-just-in-time.md)szerint.

A Azure Portal a felhasználók megtekinthetik Security Center által biztosított biztonsági riasztásokat, beleértve az IP-címeket és a támadó adatait. További információ: a [Azure Security Center biztonsági riasztások kezelése és válaszadás](security-center-managing-and-responding-alerts.md).

## <a name="classifying-personal-data"></a>Személyes adatosztályozás
Nem szükséges a Security Center biztonsági kapcsolattartási szolgáltatásában található személyes adatosztályozás. A mentett adatmennyiség egy e-mail-cím (vagy több e-mail-cím) és egy telefonszám. A [kapcsolattartási adatforrásokat](security-center-provide-security-contact-details.md) a Security Center érvényesíti.

A Security Center igény [szerinti funkciója által](security-center-just-in-time.md) mentett IP-címeket és portszámokat nem kell besorolnia.

A riasztások Security Centerban való [megtekintésével](security-center-managing-and-responding-alerts.md) csak a rendszergazda szerepkörhöz rendelt felhasználó tud kategorizálni személyes adatmennyiséget.

## <a name="securing-and-controlling-access-to-personal-data"></a>Személyes adatelérés biztosítása és szabályozása
Az olvasó, a tulajdonos, a közreműködő vagy a fiók rendszergazdája által hozzárendelt Security Center felhasználó hozzáférhet a [biztonsági kapcsolattartási adathoz](security-center-provide-security-contact-details.md).

Egy Security Center felhasználó hozzárendelte az olvasó, a tulajdonos, a közreműködő vagy a fiók rendszergazdája szerepkört az igény [szerinti szabályzatokhoz](security-center-just-in-time.md) .

Az olvasó, a tulajdonos, a közreműködő vagy a fiók rendszergazdája által hozzárendelt Security Center felhasználó megtekintheti a [riasztásokat](security-center-managing-and-responding-alerts.md).

## <a name="updating-personal-data"></a>Személyes adatok frissítése
A tulajdonos, közreműködő vagy rendszergazda szerepkört hozzárendelő Security Center felhasználók a Azure Portal használatával frissíthetik a [biztonsági kapcsolattartási](security-center-provide-security-contact-details.md) adatvédelmet.

A tulajdonos, közreműködő vagy rendszergazda szerepkört hozzárendelő Security Center felhasználó frissítheti az igény szerinti [házirendeket](security-center-just-in-time.md).

A fiók rendszergazdája nem szerkesztheti a riasztási incidenseket. A [riasztási incidensek](security-center-managing-and-responding-alerts.md) biztonsági adatként számítanak, és csak olvashatók.

## <a name="deleting-personal-data"></a>Személyes adatok törlése
A tulajdonos, közreműködő vagy rendszergazda szerepkört hozzárendelő Security Center felhasználók a Azure Portal használatával törölhetik a [biztonsági kapcsolattartási](security-center-provide-security-contact-details.md) adatvédelmet.

A tulajdonos, közreműködő vagy rendszergazda szerepkört hozzárendelő Security Center felhasználó a Azure Portal használatával törölheti az igény szerinti [házirendeket](security-center-just-in-time.md) .

Egy Security Center felhasználó nem törölhet riasztási incidenseket. Biztonsági okokból a [riasztási incidens](security-center-managing-and-responding-alerts.md) csak olvasható adatként számít.

## <a name="exporting-personal-data"></a>Személyes adatok exportálása
Az olvasó, a tulajdonos, a közreműködő vagy a fiók rendszergazdája által hozzárendelt Security Center felhasználó a következőket teheti a [biztonsági kapcsolattartási adat](security-center-provide-security-contact-details.md) exportálásával:

- Másolás a Azure Portalból
- Az Azure REST API hívásának végrehajtása, HTTP beolvasása:
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/providers/Microsoft.Security/securityContacts?api-version={api-version}
  ```

A rendszergazda szerepkört hozzárendelő Security Center felhasználók az IP-címeket tartalmazó igény szerinti [szabályzatokat](security-center-just-in-time.md) az alábbiak szerint exportálhatók:

- Másolás a Azure Portalból
- Az Azure REST API hívásának végrehajtása, HTTP beolvasása:
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Security/locations/{location}/jitNetworkAccessPolicies/default?api-version={api-version}
  ```

A fiók rendszergazdája a következőket végezheti el a riasztás részleteinek exportálásával:

- Másolás a Azure Portalból
- Az Azure REST API hívásának végrehajtása, HTTP beolvasása:
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/providers/microsoft.Security/alerts?api-version={api-version}
  ```

További információ: [biztonsági riasztások beolvasása (gyűjtemény BEolvasása)](/previous-versions/azure/reference/mt704050(v=azure.100)).

## <a name="restricting-the-use-of-personal-data-for-profiling-or-marketing-without-consent"></a>Személyes adatfelhasználás korlátozása profilkészítés vagy marketing jóváhagyás nélkül
Egy Security Center felhasználó dönthet úgy, hogy törli a [biztonsági kapcsolattartási adatvédelmet](security-center-provide-security-contact-details.md).

Az igény szerinti [adatok](security-center-just-in-time.md) nem azonosítható adatoknak minősülnek, és 30 napig őrzik meg őket.

A [riasztási adat](security-center-managing-and-responding-alerts.md) biztonsági adatnak minősül, és két évig őrzi meg a rendszer.

## <a name="auditing-and-reporting"></a>Naplózás és jelentéskészítés
A biztonsági kapcsolattartási naplókat, az igény szerinti és a riasztási frissítéseket a rendszer az [Azure-tevékenységek naplóiban](../azure-monitor/essentials/platform-logs-overview.md)tartja karban.