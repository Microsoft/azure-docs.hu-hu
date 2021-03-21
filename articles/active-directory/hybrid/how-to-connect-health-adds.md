---
title: Az Azure AD Connect Health használata az AD DS szolgáltatással | Microsoft Docs
description: Ez az Azure AD Connect Health-oldal ismerteti az AD DS figyelésének módját.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: curtand
ms.assetid: 19e3cf15-f150-46a3-a10c-2990702cd700
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/18/2017
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0686a4dafae17bafc65a03c985a65aecba70e60c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98017017"
---
# <a name="using-azure-ad-connect-health-with-ad-ds"></a>Az Azure AD Connect Health használata az AD DS szolgáltatással
Az alábbi dokumentáció az Active Directory tartományi szolgáltatások Azure AD Connect Health használatával történő figyelésére vonatkozik. Az AD DS támogatott verziói: Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 és Windows Server 2016.

Az AD FS az Azure AD Connect Health használatával történő megfigyelésére vonatkozó további információkat lásd: [Az Azure AD Connect Health használata az AD FS szolgáltatással](how-to-connect-health-adfs.md). Az Azure AD Connect (szinkronizálási szolgáltatás) az Azure AD Connect Health használatával történő megfigyelésével kapcsolatos információkat [Az Azure AD Connect Health szinkronizálási szolgáltatás használata](how-to-connect-health-sync.md) című témakörben tekintheti meg.

![Azure AD Connect Health for AD DS](./media/how-to-connect-health-adds/domainservicesnapshot.PNG)

## <a name="alerts-for-azure-ad-connect-health-for-ad-ds"></a>Az Azure AD Connect Health for AD DS riasztásai
A Riasztások szakasz az Azure AD Connect Health for AD DS-en belül a tartományvezérlőkhöz kapcsolódó aktív és megoldott riasztásokról nyújt listát. Egy aktív vagy egy megoldott riasztás kiválasztása egy új, további információkat tartalmazó panelt nyit meg, amely a megoldás lépései mellett támogatási dokumentumokra mutató hivatkozásokat jelenít meg. Minden riasztástípusnak egy vagy több példánya lehet, amelyek megfelelnek a meghatározott riasztás által érintett tartományvezérlőknek. A riasztási panel alsó részéhez közel az érintett tartományvezérlőre duplán kattintva megnyílik egy újabb panel, további részletekkel a meghatározott riasztási eseményről.

Ezen a panelen e-mail-értesítéseket engedélyezhet a riasztásokhoz, és a nézetben módosíthatja az időtartományt. Az időtartomány kiterjesztése lehetővé teszi korábbi, megoldott riasztások megjelenítését.

![Azure AD Connect szinkronizálási szolgáltatás – hiba](./media/how-to-connect-health-adds/aadconnect-health-adds-alerts.png)

## <a name="domain-controllers-dashboard"></a>A Tartományvezérlők irányítópult
Ez az irányítópult a környezet topológiai áttekintését teszi lehetővé, a főbb üzemeltetési mérőszámokkal és minden megfigyelt tartományvezérlő állapotadataival. A megjelenített mérőszámok segítenek a további vizsgálatot igénylő tartományvezérlők gyors beazonosításában. Alapértelmezés szerint csak az oszlopok egy része jelenik meg. Azonban az Oszlopok parancsra duplán kattintva az elérhető oszlopok teljes készlete megtalálható. A figyelmet igénylő oszlopok kiválasztásával ezt az irányítópultot egy könnyen használható hellyé alakítja, ahol megtekintheti az AD DS-környezet állapotát.

![Tartományvezérlők](./media/how-to-connect-health-adds/aadconnect-health-adds-domainsandsites-dashboard.png)

A tartományvezérlők csoportosíthatóak a hozzájuk tartozó tartomány vagy hely alapján, ami hasznos lehet a környezeti topológia megértésében. Végül, ha duplán kattint a panel fejlécére, az irányítópult teljes méretben jelenik meg a képernyőterület maximális kihasználásának érdekében. Ez a nagyobb nézet több oszlop megjelenítése esetén hasznos.

## <a name="replication-status-dashboard"></a>A Replikációs állapot irányítópult
Ez az irányítópult a megfigyelt tartományvezérlők replikációs állapotának és a replikáció topológiájának a megtekintését teszi lehetővé. Megjelenik a legutóbbi replikációs kísérlet állapota, a bármely felmerülő hibával kapcsolatos segítséget tartalmazó dokumentációval együtt. Egy hibás tartományvezérlőre duplán kattintva megnyílik egy új panel a következő adatokkal: a hiba részletei, javasolt megoldási lépések, valamint a hibaelhárítási dokumentációra mutató hivatkozások.

![Replikáció állapota](./media/how-to-connect-health-adds/aadconnect-health-adds-replication.png)

## <a name="monitoring"></a>Figyelés
Ez a funkció különböző teljesítményszámlálók grafikus trendjeit jeleníti meg, amelyeket a rendszer folyamatosan gyűjt az egyes megfigyelt tartományvezérlőkről. Egy tartományvezérlő teljesítménye könnyen összehasonlítható más megfigyelt tartományvezérlőkkel az erdőben. Ezenfelül különböző teljesítményszámlálókat láthat egymás mellett, amely a környezetében történő hibaelhárítás során lehet hasznos.

![Figyelés](./media/how-to-connect-health-adds/aadconnect-health-adds-monitoring.png)

Alapértelmezés szerint négy teljesítményszámláló van kiválasztva, de belefoglalhat továbbiakat is úgy, hogy a szűrő parancsra kattint, és kijelöli a kívánt teljesítményszámlálót, vagy törli annak jelölését. Továbbá, egy teljesítményszámláló diagramjára duplán kattintva megnyílik egy új panel, amely az egyes megfigyelt tartományvezérlőkhöz tartozó adatpontokat tartalmazza.

## <a name="related-links"></a>Kapcsolódó hivatkozások
* [Azure AD Connect Health](./whatis-azure-ad-connect.md)
* [Az Azure AD Connect Health-ügynök telepítése](how-to-connect-health-agent-install.md)
* [Azure AD Connect Health Operations (Az Azure AD Connect Health műveletei)](how-to-connect-health-operations.md)
* [Az Azure AD Connect Health használata az AD FS szolgáltatással](how-to-connect-health-adfs.md)
* [Szinkronizálás Azure AD Connect Health használata](how-to-connect-health-sync.md)
* [Azure AD Connect Health FAQ (Azure AD Connect Health – gyakori kérdések)](reference-connect-health-faq.md)
* [Azure AD Connect Health korábbi verziók](reference-connect-health-version-history.md)