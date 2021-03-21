---
title: Tanúsítvány elforgatása Azure SQL Database & SQL felügyelt példányhoz
description: Ismerje meg a legfelső szintű tanúsítványok változásainak a Azure SQL Database és az Azure SQL felügyelt példányát befolyásoló közelgő változásait
author: srdan-bozovic-msft
ms.author: srbozovi
ms.service: sql-db-mi
ms.subservice: service
ms.topic: conceptual
ms.date: 09/13/2020
ms.openlocfilehash: 96367b143711c4ec5f3f8d609f048c72c6fded16
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97590851"
---
# <a name="understanding-the-changes-in-the-root-ca-change-for-azure-sql-database--sql-managed-instance"></a>A legfelső szintű HITELESÍTÉSSZOLGÁLTATÓ változásának megismerése Azure SQL Database & SQL felügyelt példányon

A Azure SQL Database & SQL felügyelt példánya a biztonságos TDS-kapcsolat létrehozásához használja az SSL használatával engedélyezett ügyfélalkalmazás vagy illesztőprogram főtanúsítványát. A [jelenlegi főtanúsítvány](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) a standard karbantartási és biztonsági eljárások keretében a 2020. október 26-án lejár. Ez a cikk további részleteket tartalmaz a közelgő változásokról, az érintett erőforrásokról és azokról a lépésekről, amelyekkel biztosítható, hogy az alkalmazás fenntartsa az adatbázis-kiszolgálóval való kapcsolatot.

## <a name="what-update-is-going-to-happen"></a>Milyen frissítés fog történni?

A hitelesítésszolgáltató [(CA) böngésző fórumának](https://cabforum.org/) nemrégiben közzétett jelentései a CA-szállítók által kiállított több tanúsítványról, amelyek nem megfelelőek.

Az iparág megfelelőségi követelményeinek megfelelően a CA-szállítók megkezdték a CA-tanúsítványok visszavonását a nem megfelelő hitelesítésszolgáltatók számára, és a kiszolgálókat a megfelelő hitelesítésszolgáltatók által kiadott tanúsítványok használatára kötelezik, valamint a megfelelő hitelesítésszolgáltatóktól származó HITELESÍTÉSSZOLGÁLTATÓI tanúsítványok aláírásával. Mivel a Azure SQL Database & SQL felügyelt példány jelenleg a nem megfelelő tanúsítványok egyikét használja, amelyeket az ügyfélalkalmazások az SSL-kapcsolataik ellenőrzéséhez használnak, biztosítaniuk kell, hogy az Azure SQL-kiszolgálók lehetséges hatásainak csökkentése érdekében meg kell győződnie a megfelelő műveletekről (lásd alább).

Az új tanúsítvány a 2020. október 26-án kezdődően lesz használatban. Ha az SQL-ügyfélről (TrustServerCertificate = true) való csatlakozáskor teljes körűen érvényesíti a kiszolgálói tanúsítványt, gondoskodnia kell arról, hogy az SQL-ügyfél az 2020-as október 26. előtt is ellenőrizni tudja az új főtanúsítvány érvényességét.

## <a name="how-do-i-know-if-my-application-might-be-affected"></a>Hogyan tudni, hogy az alkalmazás hatással lehet-e?

Minden SSL/TLS protokollt használó alkalmazás, és ellenőrizze, hogy a főtanúsítványnak frissítenie kell-e a főtanúsítványt a Azure SQL Database & SQL felügyelt példányához való kapcsolódáshoz. 

Ha jelenleg nem használ SSL/TLS-t, az alkalmazás rendelkezésre állása nincs hatással. A kapcsolódási karakterlánc megtekintésével ellenőrizheti, hogy az ügyfélalkalmazás megpróbál-e ellenőrizni a főtanúsítványt. Ha a TrustServerCertificate explicit módon igaz értékre van állítva, akkor a rendszer nem érinti.

Ha az ügyfél-illesztőprogram az operációs rendszer tanúsítványtárolóját használja, az illesztőprogramok többsége elvégezhető, és az operációs rendszer rendszeres időközönként ezt a változást valószínűleg nem fogja érinteni, mivel a legfelső szintű tanúsítványnak már elérhetőnek kell lennie a megbízható főtanúsítvány-tárolóban. Keresse meg a Baltimore CyberDigiCert GlobalRoot G2-t, és ellenőrizze, hogy jelen van-e.

Ha az ügyfél-illesztőprogram helyi tanúsítványtárolót használ, hogy elkerülje az alkalmazás rendelkezésre állását a váratlanul visszavont tanúsítványok miatt, vagy egy visszavont tanúsítvány frissítéséhez, tekintse meg a mi a teendő a [**kapcsolat fenntartásához**](./ssl-root-certificate-expiring.md#what-do-i-need-to-do-to-maintain-connectivity) című szakaszt.

## <a name="what-do-i-need-to-do-to-maintain-connectivity"></a>Mit kell tennem a kapcsolat fenntartásához

Az alábbi lépések végrehajtásával elkerülhető, hogy az alkalmazás rendelkezésre állása megszakadjon a tanúsítványok váratlan visszavonása miatt, vagy egy visszavont tanúsítvány frissítése:

*   Töltse le a Baltimore CyberTrust root & DigiCert GlobalRoot G2 legfelső szintű HITELESÍTÉSSZOLGÁLTATÓT az alábbi hivatkozások közül:
    *   https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem
    *   https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem

*   Kombinált HITELESÍTÉSSZOLGÁLTATÓI tanúsítványtároló létrehozása a **BaltimoreCyberTrustRoot** -és **DigiCertGlobalRootG2** -tanúsítványokkal együtt.

## <a name="what-can-be-the-impact"></a>Milyen hatással lehet?
Ha az itt leírtak szerint hitelesíti a kiszolgálói tanúsítványokat, előfordulhat, hogy az alkalmazás rendelkezésre állása megszakad, mert az adatbázis nem érhető el. Az alkalmazástól függően különböző hibaüzenetek jelenhetnek meg, többek között a következők:
*   Érvénytelen tanúsítvány/visszavont tanúsítvány
*   A kapcsolat időtúllépés miatt megszakadt
*   Hiba, ha van ilyen

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="if-i-am-not-using-ssltls-do-i-still-need-to-update-the-root-ca"></a>Ha nem használok SSL/TLS-t, továbbra is frissíteni kell a legfelső szintű HITELESÍTÉSSZOLGÁLTATÓT?
Ha nem SSL/TLS protokollt használ, nincs szükség erre a változásra vonatkozó műveletekre. Még mindig be kell állítania egy tervet a legújabb TLS-verzió használatának megkezdéséhez, ahogy a közeljövőben a TLS-kényszerítést tervezzük.

### <a name="what-will-happen-if-i-do-not-update-the-root-certificate-before-october-26-2020"></a>Mi történik, ha nem frissítem a főtanúsítványt a 2020. október 26. előtt?
Ha nem frissíti a főtanúsítványt a 2020. november 30. előtt, az SSL/TLS protokollon keresztül csatlakozó alkalmazásokat, és ellenőrzi, hogy a főtanúsítvány nem fog-e kommunikálni a Azure SQL Database & SQL felügyelt példányát, és az alkalmazás kapcsolódási problémákat tapasztal a Azure SQL Database & SQL felügyelt példánya számára.

### <a name="do-i-need-to-plan-a-maintenance-downtime-for-this-changebr"></a>Meg kell tervezni egy karbantartási állásidőt ehhez a változáshoz?<BR>
Nem. Mivel a változás csak az ügyféloldali oldalon történik a kiszolgálóhoz való kapcsolódáshoz, ehhez a változáshoz nem szükséges karbantartási állásidő.

### <a name="what-if-i-cannot-get-a-scheduled-downtime-for-this-change-before-october-26-2020"></a>Mi a teendő, ha nem tudok ütemezett állásidőt beolvasni ehhez a változáshoz a 2020. október 26. előtt?
Mivel a kiszolgálóhoz való csatlakozáshoz használt ügyfeleknek frissíteniük kell a tanúsítvány adatait a [javítás szakaszban leírtak szerint,](./ssl-root-certificate-expiring.md#what-do-i-need-to-do-to-maintain-connectivity)ebben az esetben nem kell leállást biztosítani a kiszolgálónak.

### <a name="if-i-create-a-new-server-after-november-30-2020-will-i-be-impacted"></a>Ha a 2020. november 30. után új kiszolgálót hozunk létre, hatással leszek rá?
Az 2020. október 26. után létrehozott kiszolgálók esetében használhatja az alkalmazásokhoz az újonnan kiállított tanúsítványt az SSL használatával történő kapcsolódáshoz.

### <a name="how-often-does-microsoft-update-their-certificates-or-what-is-the-expiry-policy"></a>Milyen gyakran frissíti a Microsoft a tanúsítványokat, vagy mi a lejárati szabályzat?
A Azure SQL Database & SQL felügyelt példány által használt tanúsítványokat a megbízható hitelesítésszolgáltatók (CA) biztosítják. Így a tanúsítványok Azure SQL Database & SQL felügyelt példányon való támogatása a CA által támogatott tanúsítványok támogatásához van kötve. Ebben az esetben azonban előfordulhat, hogy az előre meghatározott tanúsítványokban nem előre látható hibák vannak, amelyeket a lehető leghamarabb meg kell oldani.

### <a name="if-i-am-using-read-replicas-do-i-need-to-perform-this-update-only-on-primary-server-or-all-the-read-replicas"></a>Ha olvasási replikákat használok, ezt a frissítést csak az elsődleges kiszolgálón vagy az összes olvasási replikán kell elvégezni?
Mivel ez a frissítés ügyféloldali módosítás, ha az ügyfél a másodpéldány-kiszolgálóról olvassa az adatok olvasását, akkor a módosításokat is alkalmazni kell az ügyfeleken. 

### <a name="do-we-have-server-side-query-to-verify-if-ssl-is-being-used"></a>Van kiszolgálóoldali lekérdezés annak ellenőrzéséhez, hogy az SSL használatban van-e?
Mivel ez a konfiguráció ügyféloldali, az információk nem érhetők el a kiszolgálói oldalon.

### <a name="what-if-i-have-further-questions"></a>Mi a teendő, ha további kérdéseim vannak?
Ha támogatási csomaggal rendelkezik, és technikai segítségre van szüksége, hozzon létre Azure-támogatási kérést, és tekintse meg [Az Azure-támogatási kérelem létrehozása](../../azure-portal/supportability/how-to-create-azure-support-request.md)című témakört.