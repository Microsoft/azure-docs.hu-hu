---
title: SSL/TLS-kapcsolat – Azure Database for MySQL
description: Információk a Azure Database for MySQL és a társított alkalmazások az SSL-kapcsolatok megfelelő használatához való konfigurálásához
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 07/09/2020
ms.openlocfilehash: 1b272755652d018a0d5364e4d67c783774c68da8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104655240"
---
# <a name="ssltls-connectivity-in-azure-database-for-mysql"></a>SSL/TLS-kapcsolat a Azure Database for MySQL

A Azure Database for MySQL a SSL (SSL) használatával támogatja az adatbázis-kiszolgáló és az ügyfélalkalmazások összekapcsolását. Az adatbázis-kiszolgáló és az ügyfélalkalmazások közötti SSL-kapcsolatok kikényszerítése elősegíti a „köztes” támadások elleni védelmet, mert titkosítja a kiszolgáló és az alkalmazás közötti streameket.

> [!NOTE]
> A `require_secure_transport` kiszolgáló paraméter értékének frissítése nem befolyásolja a MySQL szolgáltatás viselkedését. A cikkben ismertetett SSL-és TLS-kényszerítési funkciókkal biztonságossá teheti az adatbázishoz való kapcsolódást.

>[!NOTE]
> Az ügyfelek visszajelzései alapján kibővítettük a meglévő Baltimore legfelső szintű HITELESÍTÉSSZOLGÁLTATÓ főtanúsítványának elavulttá tételét a 2021. február 15-ig (02/15/2021).

> [!IMPORTANT] 
> Az SSL-főtanúsítvány az 2021-as (02/15/2021-as) 15. február 15-től lejár. Frissítse az alkalmazást az [új tanúsítvány](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem)használatára. További információ: [tervezett tanúsítványok frissítései](concepts-certificate-rotation.md)

## <a name="ssl-default-settings"></a>Alapértelmezett SSL-beállítások

Alapértelmezés szerint az adatbázis-szolgáltatást úgy kell konfigurálni, hogy a MySQL-hez való csatlakozáskor SSL-kapcsolatokat igényeljen.  Javasoljuk, hogy ha lehetséges, ne tiltsa le az SSL-beállítást.

Amikor új Azure Database for MySQL kiszolgálót épít ki a Azure Portal és a parancssori felületen, az SSL-kapcsolatok kényszerítése alapértelmezés szerint engedélyezve van. 

A különböző programozási nyelvekhez tartozó kapcsolatok karakterláncai a Azure Portal láthatók. Ezek a kapcsolati karakterláncok tartalmazzák az adatbázishoz való kapcsolódáshoz szükséges SSL-paramétereket. A Azure Portal válassza ki a kiszolgálót. A **Beállítások** fejléc alatt válassza ki a **kapcsolatok karakterláncait**. Az SSL-paraméter az összekötőtől függően változik, például: "SSL = true" vagy "sslmode = require" vagy "sslmode = Required" és egyéb variációk.

Bizonyos esetekben az alkalmazásoknak egy megbízható hitelesítésszolgáltató (CA) tanúsítványfájl által létrehozott helyi tanúsítványfájl szükségesek a biztonságos kapcsolódáshoz. Jelenleg az ügyfelek **csak** az előre definiált tanúsítvány használatával csatlakozhatnak egy Azure Database for MySQL kiszolgálóhoz, amely a következő helyen található: https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem . 

Hasonlóképpen, az alábbi hivatkozások a szuverén felhőkben található kiszolgálók tanúsítványait mutatják be: [Azure Government](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem), az [Azure China](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem)és az [Azure Germany](https://www.d-trust.net/cgi-bin/D-TRUST_Root_Class_3_CA_2_2009.crt).

Ha szeretné megtudni, hogyan engedélyezheti vagy tilthatja le az SSL-kapcsolatokat az alkalmazások fejlesztésekor, tekintse meg az [SSL konfigurálását](howto-configure-ssl.md)ismertető témakört.

## <a name="tls-enforcement-in-azure-database-for-mysql"></a>TLS-kényszerítés Azure Database for MySQL

Azure Database for MySQL támogatja a titkosítást az adatbázis-kiszolgálóhoz Transport Layer Security (TLS) használatával csatlakozó ügyfelek számára. A TLS egy iparági szabványnak megfelelő protokoll, amely gondoskodik az adatbázis-kiszolgáló és az ügyfélalkalmazások közötti biztonságos hálózati kapcsolatokról, ami lehetővé teszi a megfelelőségi követelmények betartását.

### <a name="tls-settings"></a>TLS-beállítások

A Azure Database for MySQL lehetővé teszi az ügyfélkapcsolatok TLS-verziójának betartatását. A TLS-verzió érvénybe léptetéséhez használja a **TLS-verzió minimális** beállítását. Ehhez a beállításhoz a következő értékek engedélyezettek:

|  Minimális TLS-beállítás             | Az ügyfél TLS-verziója támogatott                |
|:---------------------------------|-------------------------------------:|
| TLSEnforcementDisabled (alapértelmezett) | Nincs szükség TLS-re                      |
| TLS1_0                           | TLS 1,0, TLS 1,1, TLS 1,2 és újabb           |
| TLS1_1                           | TLS 1,1, TLS 1,2 és újabb                   |
| TLS1_2                           | TLS 1,2-es és újabb verzió                     |


Ha például a TLS 1,0-es minimális beállítás értéke a TLS, azt jelenti, hogy a kiszolgáló engedélyezi a TLS 1,0, 1,1 és 1.2 + protokollt használó ügyfelek kapcsolódását. Azt is megteheti, hogy a 1,2 értékre állítja azt, hogy csak a TLS 1.2 + protokollt használó ügyfelek kapcsolatait engedélyezzük, és a TLS 1,0 és a TLS 1,1 összes kapcsolata el lesz utasítva.

> [!Note] 
> Alapértelmezés szerint a Azure Database for MySQL nem érvényesíti a minimális TLS-verziót (a beállítás `TLSEnforcementDisabled` ).
>
> Miután kikényszeríti a TLS minimális verzióját, később nem tilthatja le a minimális verzió-kényszerítést.

A TLS minimális verziójának beállításához nincs szükség a kiszolgáló újraindítására, ha a kiszolgáló online állapotú. Ha meg szeretné tudni, hogyan állíthatja be a TLS-beállítást a Azure Database for MySQLhoz, tekintse meg a [TLS-beállítás konfigurálását](howto-tls-configurations.md)ismertető témakört.

## <a name="cipher-support-by-azure-database-for-mysql-single-server"></a>Titkosítási támogatás Azure Database for MySQL egyetlen kiszolgálóval

Az SSL/TLS-kommunikáció részeként a titkosítási csomagok ellenőrzése megtörténik, és csak a titkosító öltönyök támogatása engedélyezett a serer adatbázissal való kommunikációhoz. A titkosító csomag érvényesítése az [átjáró rétegében](concepts-connectivity-architecture.md#connectivity-architecture) van szabályozva, és nem explicit módon a csomóponton. Ha a titkosítási csomagok nem felelnek meg az alább felsorolt lakosztályok egyikének, a rendszer elutasítja a bejövő ügyfélkapcsolatokat.

### <a name="cipher-suite-supported"></a>Titkosítási csomag támogatott

*   TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
*   TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
*   TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
*   TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256

## <a name="next-steps"></a>Következő lépések

- [Azure Database for MySQLhoz tartozó kapcsolatok kódtárai](concepts-connection-libraries.md)
- Ismerje meg, hogyan [konfigurálhatja az SSL](howto-configure-ssl.md) -t
- Útmutató a [TLS konfigurálásához](howto-tls-configurations.md)
