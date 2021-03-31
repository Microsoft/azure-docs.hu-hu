---
title: SSL/TLS – Azure Database for PostgreSQL – egyetlen kiszolgáló
description: Útmutatás és információk a TLS-kapcsolat konfigurálásához Azure Database for PostgreSQL – egyetlen kiszolgáló esetén.
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/08/2020
ms.openlocfilehash: c98ee8f747975d4237c2906be2060eddbc7b9990
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96000946"
---
# <a name="configure-tls-connectivity-in-azure-database-for-postgresql---single-server"></a>TLS-kapcsolat konfigurálása Azure Database for PostgreSQL – egyetlen kiszolgálón

Azure Database for PostgreSQL az ügyfélalkalmazások a PostgreSQL szolgáltatáshoz való csatlakoztatását részesíti előnyben Transport Layer Security (TLS), korábbi nevén SSL (SSL) használatával. Az adatbázis-kiszolgáló és az ügyfélalkalmazások közötti TLS-kapcsolatok érvényesítésével megvédheti a védelmet a kiszolgáló és az alkalmazás közötti adatfolyam titkosításával.

Alapértelmezés szerint a PostgreSQL-adatbázis szolgáltatás úgy van konfigurálva, hogy TLS-kapcsolatokat igényeljen. Dönthet úgy, hogy letiltja a TLS megkövetelését, ha az ügyfélalkalmazás nem támogatja a TLS-kapcsolatot.

>[!NOTE]
> Az ügyfelek visszajelzései alapján kibővítettük a meglévő Baltimore legfelső szintű HITELESÍTÉSSZOLGÁLTATÓ főtanúsítványának elavulttá tételét a 2021. február 15-ig (02/15/2021).

> [!IMPORTANT] 
> Az SSL-főtanúsítvány az 2021-as (02/15/2021-as) 15. február 15-től lejár. Frissítse az alkalmazást az [új tanúsítvány](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem)használatára. További információ: [tervezett tanúsítványok frissítései](concepts-certificate-rotation.md)


## <a name="enforcing-tls-connections"></a>TLS-kapcsolatok kényszerítése

A Azure Portal és a CLI használatával kiépített összes Azure Database for PostgreSQL-kiszolgáló esetében a TLS-kapcsolatok kényszerítése alapértelmezés szerint engedélyezve van. 

Hasonlóképpen, a "kapcsolati karakterláncok" beállításokban előre definiált kapcsolati karakterláncok a Azure Portalban tartalmazzák az adatbázis-kiszolgáló TLS-vel való csatlakozásához szükséges paramétereket a közös nyelvekhez. A TLS-paraméter az összekötőtől függően változik, például: "SSL = true" vagy "sslmode = require" vagy "sslmode = Required" és egyéb variációk.

## <a name="configure-enforcement-of-tls"></a>A TLS kényszerítésének konfigurálása

Igény szerint letilthatja a TLS-kapcsolat kényszerítését. Microsoft Azure azt javasolja, hogy mindig engedélyezze az **SSL-kapcsolat kikényszerített** beállítását a fokozott biztonság érdekében.

### <a name="using-the-azure-portal"></a>Az Azure Portal használata

Keresse fel Azure Database for PostgreSQL-kiszolgálóját, és kattintson a **kapcsolatbiztonsági** elemre. A váltógomb használatával engedélyezheti vagy letilthatja az **SSL-kapcsolat érvényesítése** beállítást. Ezután kattintson a **Mentés** gombra.

:::image type="content" source="./media/concepts-ssl-connection-security/1-disable-ssl.png" alt-text="Kapcsolatbiztonsági – letiltás kényszerített TLS/SSL":::

A beállítás megerősítéséhez tekintse meg az **Áttekintés** lapot az SSL- **érvényesítési állapot** jelző megjelenítéséhez.

### <a name="using-azure-cli"></a>Az Azure parancssori felület használata

Az **SSL-kényszerítési** paramétert engedélyezheti vagy letilthatja az `Enabled` Azure CLI-ben, illetve az `Disabled` értékek használatával.

```azurecli
az postgres server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Enabled
```

## <a name="ensure-your-application-or-framework-supports-tls-connections"></a>Győződjön meg arról, hogy az alkalmazás vagy a keretrendszer támogatja a TLS-kapcsolatokat

Néhány alkalmazás-keretrendszer, amely a PostgreSQL-t használja az adatbázis-szolgáltatásaihoz, alapértelmezés szerint nem engedélyezi a TLS-t a telepítés során. Ha a PostgreSQL-kiszolgáló kikényszeríti a TLS-kapcsolatokat, de az alkalmazás nincs konfigurálva a TLS-hez, előfordulhat, hogy az alkalmazás nem tud csatlakozni az adatbázis-kiszolgálóhoz. A TLS-kapcsolatok engedélyezésével kapcsolatos információkért tekintse meg az alkalmazás dokumentációját.

## <a name="applications-that-require-certificate-verification-for-tls-connectivity"></a>A TLS-kapcsolat tanúsítvány-ellenőrzését igénylő alkalmazások

Bizonyos esetekben az alkalmazásoknak egy megbízható hitelesítésszolgáltató (CA) tanúsítványfájl által létrehozott helyi tanúsítványfájl szükségesek a biztonságos kapcsolódáshoz. A Azure Database for PostgreSQL kiszolgálóhoz való kapcsolódáshoz szükséges tanúsítvány a következő helyen található: https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem . Töltse le a tanúsítványfájl, és mentse a kívánt helyre. 

Tekintse meg a következő hivatkozásokat a szuverén felhőkben található kiszolgálók tanúsítványainak esetében: [Azure Government](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem), [Azure China](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem)és [Azure Germany](https://www.d-trust.net/cgi-bin/D-TRUST_Root_Class_3_CA_2_2009.crt).

### <a name="connect-using-psql"></a>Összekapcsolás a psql használatával

Az alábbi példa bemutatja, hogyan csatlakozhat a PostgreSQL-kiszolgálóhoz a psql parancssori segédprogram használatával. A `sslmode=verify-full` kapcsolati karakterlánc beállításával kényszerítheti a TLS/SSL-tanúsítványok ellenőrzését. Adja át a helyi tanúsítványfájl elérési útját a `sslrootcert` paraméternek.

A következő parancs egy példa a psql-kapcsolatok karakterláncára:

```shell
psql "sslmode=verify-full sslrootcert=BaltimoreCyberTrustRoot.crt host=mydemoserver.postgres.database.azure.com dbname=postgres user=myusern@mydemoserver"
```

> [!TIP]
> Ellenőrizze, hogy az átadott érték megegyezik-e a `sslrootcert` mentett tanúsítvány elérési útjával.

## <a name="tls-enforcement-in-azure-database-for-postgresql-single-server"></a>TLS-kényszerítés Azure Database for PostgreSQL egyetlen kiszolgálón

Azure Database for PostgreSQL – az egyetlen kiszolgáló támogatja a titkosítást az adatbázis-kiszolgálóhoz Transport Layer Security (TLS) használatával csatlakozó ügyfelek számára. A TLS egy iparági szabványnak megfelelő protokoll, amely gondoskodik az adatbázis-kiszolgáló és az ügyfélalkalmazások közötti biztonságos hálózati kapcsolatokról, ami lehetővé teszi a megfelelőségi követelmények betartását.

### <a name="tls-settings"></a>TLS-beállítások

Azure Database for PostgreSQL egyetlen kiszolgáló lehetővé teszi az ügyfélkapcsolatok TLS-verziójának betartatását. A TLS-verzió érvénybe léptetéséhez használja a **TLS-verzió minimális** beállítását. Ehhez a beállításhoz a következő értékek engedélyezettek:

|  Minimális TLS-beállítás             | Az ügyfél TLS-verziója támogatott                |
|:---------------------------------|-------------------------------------:|
| TLSEnforcementDisabled (alapértelmezett) | Nincs szükség TLS-re                      |
| TLS1_0                           | TLS 1,0, TLS 1,1, TLS 1,2 és újabb |
| TLS1_1                           | TLS 1,1, TLS 1,2 és újabb          |
| TLS1_2                           | TLS 1,2-es és újabb verzió           |


Ha például ezt a TLS-beállítást a TLS 1,0 értékre állítja be, akkor a kiszolgáló engedélyezi a TLS 1,0, 1,1 és 1.2 + protokollt használó ügyfelek kapcsolódását. Azt is megteheti, hogy a 1,2 értékre állítja azt, hogy csak a TLS 1.2 + protokollt használó ügyfelek kapcsolatait engedélyezzük, és a TLS 1,0 és a TLS 1,1 összes kapcsolata el lesz utasítva.

> [!Note] 
> Alapértelmezés szerint a Azure Database for PostgreSQL nem érvényesíti a minimális TLS-verziót (a beállítás `TLSEnforcementDisabled` ).
>
> Miután kikényszeríti a TLS minimális verzióját, később nem tilthatja le a minimális verzió-kényszerítést.

Ha meg szeretné tudni, hogyan állíthatja be a TLS-beállítást a Azure Database for PostgreSQL egyetlen kiszolgálóhoz, tekintse meg a [TLS-beállítás konfigurálását](howto-tls-configurations.md)ismertető témakört.

## <a name="cipher-support-by-azure-database-for-postgresql-single-server"></a>Titkosítási támogatás Azure Database for PostgreSQL egyetlen kiszolgálóval

Az SSL/TLS-kommunikáció részeként a titkosítási csomagok ellenőrzése megtörténik, és csak a titkosító öltönyök támogatása engedélyezett a serer adatbázissal való kommunikációhoz. A titkosító csomag érvényesítése az [átjáró rétegében](concepts-connectivity-architecture.md#connectivity-architecture) van szabályozva, és nem explicit módon a csomóponton. Ha a titkosítási csomagok nem felelnek meg az alább felsorolt lakosztályok egyikének, a rendszer elutasítja a bejövő ügyfélkapcsolatokat.

### <a name="cipher-suite-supported"></a>Titkosítási csomag támogatott

*   TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
*   TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
*   TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384
*   TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256

## <a name="next-steps"></a>Következő lépések

Tekintse át a [Azure Database for PostgreSQLhoz tartozó kapcsolódási könyvtárak](concepts-connection-libraries.md)különböző alkalmazás-csatlakozási lehetőségeit.

- Útmutató a [TLS konfigurálásához](howto-tls-configurations.md)