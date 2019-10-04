---
title: Hibrid identitás tervezési - kezelési követelmények Azure |} A Microsoft Docs
description: A Tartalomkezelés a vállalat követelményeinek meghatározása betekintést nyújt. Általában akkor, amikor egy felhasználó a saját eszköz rendelkezik, azok is szükség lehet, hogy a rendszer a szerint az alkalmazás által használt váltakozó több hitelesítő adatok. Fontos különbséget tenni a tartalmat a saját hitelesítő adataival, és a vállalati hitelesítő adatok használatával létrehozottakra használatával hoztak. Az identitáskezelési megoldás tudnia kell interakcióba felhőalapú szolgáltatások zökkenőmentes élményt biztosít a végfelhasználók számára, miközben adataikhoz biztosítására, és növelje az adatszivárgás elleni védelem.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: dd1ef776-db4d-4ab8-9761-2adaa5a4f004
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d970fd133f8c43319e7f1fdb6b3a50c3c05f687
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64918441"
---
# <a name="determine-content-management-requirements-for-your-hybrid-identity-solution"></a>A hibrid identitáskezelési megoldás a kezelési követelmények meghatározása
A vállalkozása számára a kezelési követelmények ismertetése közvetlen hatással lehet a döntést a melyik hibrid identitáskezelési megoldás használata. Több eszköz és a saját eszközeiket használják a felhasználók képességét elterjedése ([BYOD](https://aka.ms/byodcg)), a vállalat a saját adatok kell védelme, de azt is kell érintetlenül felhasználók adatait. Általában akkor, amikor egy felhasználó a saját eszköz rendelkezik, azok is szükség lehet, hogy a rendszer a szerint az alkalmazás által használt váltakozó több hitelesítő adatok. Fontos különbséget tenni a tartalmat a saját hitelesítő adataival, és a vállalati hitelesítő adatok használatával létrehozottakra használatával hoztak. Az identitáskezelési megoldás tudnia kell interakcióba felhőalapú szolgáltatások zökkenőmentes élményt biztosít a végfelhasználók számára, miközben adataikhoz biztosítására, és növelje az adatszivárgás elleni védelem. 

Az identitáskezelési megoldás lesz adatbáziscsoportok különböző technikai vezérlők, annak érdekében, hogy a Tartalomkezelés az alábbi ábrán látható módon:

![Biztonsági vezérlők](./media/plan-hybrid-identity-design-considerations/securitycontrols.png)

**Biztonsági ellenőrzéseket, amelyeket az identitáskezelési rendszerekkel kihasználva lesz**

Általánosságban véve tartalomkezelési követelményeit a következő területeken az identitáskezelési rendszerekkel használja:

* Adatvédelem: azonosítja a felhasználót, hogy az erőforrás tulajdonosa, és alkalmazza a megfelelő szabályozásokkal integritásának fenntartása.
* Adatok besorolása: azonosítsa a felhasználó vagy csoport és a egy objektumot a besorolás alapján való hozzáférésének szintjét. 
* Adatok kiszivárgását védelem: biztonsági ellenőrzéseket az adatok kiszivárgásának elkerülésére védelmének felelős a rendszer a felhasználó identitásának érvényesítése interakcióba kell. Ez fontos is ellenőrzési célú naplózásának.

> [!NOTE]
> Olvasási [adatbesorolás a felhőre való előkészületként](https://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) további információ az ajánlott eljárások és útmutató az adatok besorolása érdekében.
> 
> 

Ha a hibrid identitáskezelési megoldás tervezése győződjön meg arról, hogy a szervezet igényeinek megfelelően válaszok a következő kérdéseket:

* A vállalat rendelkezik biztonsági vezérlők kényszerítésére az adatvédelem?
  * Ha igen, a biztonsági vezérlőket lesz integrálhatják a hibrid identitáskezelési megoldás, amely fogad el kívánja?
* A vállalat adatainak besorolása használ?
  * Ha igen, az a hibrid identitáskezelési megoldás, amely fogad el kívánja integrálhatják az aktuális megoldáshoz?
* Rendelkezik a vállalata jelenleg az adatszivárgás megoldással? 
  * Ha igen, az a hibrid identitáskezelési megoldás, amely fogad el kívánja integrálhatják az aktuális megoldáshoz?
* A vállalati erőforrásokhoz való hozzáférés naplózása nem kell?
  * Ha igen, milyen típusú erőforrások?
  * Ha igen, milyen szintű adatokat szükség?
  * Ha igen, ahol a felügyeleti napló kell lennie? A helyszíni vagy a felhőben?
* A vállalati e-mailt, bizalmas adatok (taj számok esetében, hitelkártyaszámokat tartalmazó stb.) titkosításához nem kell?
* Nem a vállalat titkosítania kell az összes dokumentumok/contents megosztva a külső üzleti partnerek?
* Vállalatának meg kell bizonyos típusú e-maileket a vállalati házirendeknek az érvényesítését (ne nincs válasz mindenkinek, nem továbbítandó)?

> [!NOTE]
> Ügyeljen arra, hogy minden válaszról feljegyzéseket, és megismerheti a válaszok indokait. [Data Protection stratégia kidolgozása](plan-hybrid-identity-design-considerations-data-protection-strategy.md) halad keresztül a rendelkezésre álló lehetőségek előnyeit és hátrányait az egyes lehetőségek.  A fenti melyik leginkább megfelelő lehetőséget az üzleti kiválaszthatja kérdések megválaszolása szükséges.
> 
> 

## <a name="next-steps"></a>További lépések
[Hozzáférés-vezérlési követelményeinek meghatározása](plan-hybrid-identity-design-considerations-accesscontrol-requirements.md)

## <a name="see-also"></a>Lásd még:
[Kialakítási szempontok áttekintése](plan-hybrid-identity-design-considerations-overview.md)

