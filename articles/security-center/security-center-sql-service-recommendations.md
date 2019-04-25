---
title: Az Azure SQL-szolgáltatás és az Azure Security Center adatok védelme |} A Microsoft Docs
description: Ez a dokumentum címek javaslatok az Azure Security Centerben, amelyekkel védje meg adatait és az Azure SQL-szolgáltatás, és megfelel a biztonsági házirendek maradjon.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: bcae6987-05d0-4208-bca8-6a6ce7c9a1e3
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 1/15/2019
ms.author: rkarlin
ms.openlocfilehash: 479e6d222c9fc7d007252bab12e807ec337ea9e6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60332471"
---
# <a name="protecting-azure-sql-service-and-data-in-azure-security-center"></a>Az Azure SQL-szolgáltatás és az Azure Security Center adatok védelme
Az Azure Security Center elemzi az Azure-erőforrások biztonsági állapotát. Ha a Security Center azonosítja a potenciális biztonsági réseket, javaslatok, amelyek végigvezetik a szükséges vezérlők konfigurálásának folyamatán hoz létre.  Javaslatok alkalmazása az Azure-erőforrástípus: virtuális gépek (VM), hálózati, SQL és az adatokhoz és alkalmazásokhoz.


### <a name="monitor-data-security"></a>Adatbiztonság monitorozása

A **Megelőzés** szakasz **Adatbiztonság** elemére kattintva megnyithatja az **Adatforrások** panelt, amelyen az SQL és a Storage használatával kapcsolatos javaslatokat olvashat. Ezenfelül általános [javaslatokat](security-center-sql-service-recommendations.md) is talál itt az adatbázis állapotára vonatkozóan. A tárolás titkosításáról további információkat az [Azure-tárfiókok titkosításának engedélyezése az Azure Security Centerben](security-center-enable-encryption-for-storage-account.md) című cikkben találhat.

![Adatforrások](./media/security-center-monitoring/security-center-monitoring-fig13-newUI-2017.png)

Az **SQL Recommendations** (Javaslatok az SQL használatával kapcsolatban) területen bármely javaslatra kattintva részletesebb információt kap a probléma megoldásához szükséges további intézkedésről. A következő példában a **Database Auditing & Threat detection on SQL databases** (Naplózás és fenyegetésészlelés az SQL-adatbázisokban) javaslat kibontott nézete látható.

![SQL-javaslat részletei](./media/security-center-monitoring/security-center-monitoring-fig14-ga-new.png)

Az **Naplózás és fenyegetésészlelés engedélyezése az SQL-adatbázisokban** panelen a következő információk láthatók:

* Az SQL-adatbázisok listája
* A kiszolgáló, amely tárolja ezeket
* Az, hogy a beállítás a kiszolgálóról öröklődött, vagy konkrétan az adott adatbázisra vonatkozik
* Az aktuális állapot
* A probléma súlyossága

Ha a javaslat megoldása érdekében rákattint az adatbázisra, az alábbi képernyőn látható módon megnyílik az **Naplózás és fenyegetésészlelés** panel.

![Naplózás és fenyegetésészlelés](./media/security-center-monitoring/security-center-monitoring-fig15-ga.png)

A naplózás engedélyezéséhez módosítsa az **Auditing** (Naplózás) beállítását **ON** (BE) értékre.

## <a name="data-and-storage-recommendations"></a>Adatok és tárolás javaslatok

|Erőforrás típusa|Biztonsági pontszám|Ajánlás|Leírás|
|----|----|----|----|
|Tárfiók|20|Biztonságos átvitel megkövetelése a tárfiókba|Biztonságos átvitel, amely arra kényszeríti a storage-fiók csak a biztonságos kapcsolat (HTTPS) érkező kérések fogadására lehetőség. HTTPS használata biztosítja, hogy a kiszolgáló és a szolgáltatás közötti hitelesítéshez, és man-in-the-middle, lehallgatást, és a munkamenet-eltérítés – például a hálózati réteg támadások ellen védi a az átvitt adatokat.|
|Redis|20|Az Azure Cache redis csak biztonságos kapcsolatok engedélyezése|Engedélyezi a Redis Azure Cache SSL-en keresztül csak kapcsolatokat. Biztonságos kapcsolatok használata biztosítja, hogy a kiszolgáló és a szolgáltatás közötti hitelesítéshez, és az átvitt adatokat védi a hálózati réteg támadásoktól, például a man-in-the-middle, lehallgatást, és munkamenet-eltérítés.|
|SQL|15|Az SQL Database-adatbázisok transzparens adattitkosítás engedélyezése|Transzparens adattitkosítás engedélyezése az inaktív adatok védelméhez és a megfelelőségi követelmények betartásához.|
|SQL|15|SQL Serverek naplózásának engedélyezése|Az Azure SQL-kiszolgáló naplózásának engedélyezése. (Csak az azure SQL-szolgáltatás esetében. Nem tartalmazza a virtuális gépeken futó SQL.)|
|SQL|15|SQL-adatbázis naplózásának engedélyezése|Az Azure SQL-adatbázis naplózásának engedélyezése. (Csak az azure SQL-szolgáltatás esetében. Nem tartalmazza a virtuális gépeken futó SQL.)|
|A Data lake analytics|15|Titkosítás inaktív állapotban, a Data Lake Analytics engedélyezése|A Data Lake Analytics az inaktív adatok védelme transzparens adattitkosítás engedélyezése. Titkosítás inaktív állapotban transzparens módon történik, ami azt jelenti, hogy a Data Lake Analytics automatikusan titkosítja az adatokat, és mindig visszafejti az adatokat lekérés előtt. Nem változtak, szükséges az alkalmazások és szolgáltatások, amelyek a titkosítás miatt a Data Lake Analytics interakciót. Titkosítás inaktív állapotban minimalizálja az adatvesztést a lopás veszélye, és emellett segít az előírt megfelelőségi követelmények.|
|A Data lake store|15|Engedélyezze a titkosítást a Data Lake Store REST|A Data Lake Store az inaktív adatok védelme transzparens adattitkosítás engedélyezése. Titkosítás inaktív állapotban transzparens módon történik, ami azt jelenti, hogy a Data Lake Store automatikusan titkosítja az adatokat, és mindig visszafejti az adatokat lekérés előtt. Az alkalmazás- és titkosítási befogadásához Data Lake Store-ral kommunikáló szolgáltatások semmilyen módosítást nem kell. Titkosítás inaktív állapotban minimalizálja az adatvesztést a lopás veszélye, és emellett segít az előírt megfelelőségi követelmények.|
|A Data lake analytics|5|Diagnosztikai naplók engedélyezése a Data Lake Analytics szolgáltatásban|Naplók engedélyezése és legfeljebb egy évig megőrizheti azokat. Ez lehetővé teszi, hogy a tevékenység nyomot hagyjanak maguk után a támadások hatékonyabb kivizsgálásához hozza létre újra, amikor egy biztonsági incidens következik be, vagy a hálózat biztonsága sérül. |
|A Data lake store|5|Diagnosztikai naplók engedélyezése az Azure Data Lake Store szolgáltatásban|Naplók engedélyezése és legfeljebb egy évig megőrizheti azokat. Ez lehetővé teszi, hogy a tevékenység nyomot hagyjanak maguk után a támadások hatékonyabb kivizsgálásához hozza létre újra, amikor egy biztonsági incidens következik be, vagy a hálózat biztonsága sérül. |
|SQL|30|SQL-adatbázisok biztonsági réseinek javítása|SQL-sebezhetőségi felmérés megvizsgálja az adatbázis a biztonsági kockázatokat, és elérhetővé teszi a bármely gyakorlattól eltérő jelenségeket, például a konfigurációs hibák, a túlságosan széleskörű engedélyeket és a bizalmas adatok nem védett. A biztonsági réseket talált feloldása nagy mértékben javíthatják a az adatbázis biztonsági helyzetében.|
|SQL|20|Azure AD-rendszergazdai fiók kiépítése az SQL Serverhez|Az Azure AD-hitelesítés engedélyezése az SQL Serverhez Azure AD-rendszergazda üzembe helyezhető. Az Azure AD-hitelesítés lehetővé teszi, hogy egyszerűsített engedélyek kezelését és az adatbázis-felhasználók és más Microsoft-szolgáltatások központi identitáskezeléssel.|
|Tárfiók|15|Tárfiók korlátlan hálózati elérésének letiltása|A storage-fiók tűzfal beállításait a nem korlátozott hálózati hozzáférés naplózása. Konfigurálja a hálózati szabályok, az engedélyezett hálózatok csak alkalmazások férhetnek hozzá a tárfiók. Ahhoz, hogy a kapcsolatok meghatározott internetről, vagy olyan helyszíni ügyfelek, biztosíthat hozzáférést az adott Azure virtuális hálózatok forgalmát, vagy nyilvános internetes IP-címtartományok.|
|Tárfiók|1|Storage-fiókok az Azure Resource Manager-modell erőforrások áttelepítése|A storage-fiókok új Azure Resource Manager-v2 használatával adja meg például a biztonsági fejlesztések: erősebb hozzáférés-vezérlés (RBAC), a jobb naplózás, a Resource Manager-alapú üzembe helyezés és a cégirányítási, elérését a felügyelt identitások, a key vaulthoz való hozzáférés titkos adatait, és az Azure AD-alapú hitelesítést és címkék támogatása és -erőforráscsoportok egyszerűbb biztonság kezelése.|

## <a name="see-also"></a>Lásd még
Javaslatok, amelyek vonatkoznak a többi Azure-erőforrásokkal kapcsolatos további információkért tekintse meg a következőket:

* [Virtuális gépek védelme az Azure Security Centerben](security-center-virtual-machine-recommendations.md)
* [Alkalmazások védelme az Azure Security Centerben](security-center-application-recommendations.md)
* [Hálózat védelme az Azure Security Centerben](security-center-network-recommendations.md)

A Security Centerrel kapcsolatos további információkért olvassa el a következőket:

* [Biztonsági szabályzatok beállítása az Azure Security Centerben](tutorial-security-policy.md) – Ez a cikk bemutatja, hogyan konfigurálhat biztonsági házirendeket Azure-előfizetései és -erőforráscsoportjai számára.
* [Biztonsági riasztások kezelése és válaszadás a riasztásokra az Azure Security Centerben](security-center-managing-and-responding-alerts.md) – A biztonsági riasztások kezelése és az azokra való reagálás.
* [Azure Security Center – gyakran ismételt kérdések](security-center-faq.md) – Gyakran ismételt kérdések a szolgáltatás használatával kapcsolatban.
