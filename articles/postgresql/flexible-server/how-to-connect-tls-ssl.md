---
title: Titkosított kapcsolat a TLS/SSL használatával Azure Database for PostgreSQL rugalmas kiszolgálón
description: Útmutatás és információk a TLS/SSL használatával történő kapcsolódáshoz Azure Database for PostgreSQL-rugalmas kiszolgálón.
author: ambhatna
ms.author: ambhatna
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: 0ba04ff14e62cd411515a765dc95ef7a3e72b709
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "90935927"
---
# <a name="encrypted-connectivity-using-transport-layer-security-in-azure-database-for-postgresql---flexible-server"></a>Titkosított kapcsolat a Transport Layer Security használatával Azure Database for PostgreSQL – rugalmas kiszolgáló

> [!IMPORTANT]
> Azure Database for PostgreSQL – a rugalmas kiszolgáló előzetes verzióban érhető el

Azure Database for PostgreSQL – a rugalmas kiszolgáló támogatja az ügyfélalkalmazások a PostgreSQL szolgáltatáshoz való csatlakoztatását Transport Layer Security (TLS), korábbi nevén SSL (SSL) használatával. A TLS egy iparági szabványnak megfelelő protokoll, amely biztosítja a titkosított hálózati kapcsolatokat az adatbázis-kiszolgáló és az ügyfélalkalmazások között, így biztosítva a megfelelőségi követelmények betartását.

Azure Database for PostgreSQL – a rugalmas kiszolgáló támogatja a titkosított kapcsolatokat Transport Layer Security (TLS 1.2 +) használatával, és a TLS 1,0 és a TLS 1,1 összes bejövő kapcsolata meg lesz tagadva. A TLS-kapcsolatok kényszerítését engedélyező rugalmas kiszolgálók esetében a TLS/SSL nem tiltható le a rugalmas kiszolgálóhoz való csatlakozáshoz.

## <a name="applications-that-require-certificate-verification-for-tlsssl-connectivity"></a>A TLS/SSL-kapcsolat tanúsítvány-ellenőrzését igénylő alkalmazások
Bizonyos esetekben az alkalmazásoknak egy megbízható hitelesítésszolgáltató (CA) tanúsítványfájl által létrehozott helyi tanúsítványfájl szükségesek a biztonságos kapcsolódáshoz. Azure Database for PostgreSQL – a rugalmas kiszolgáló a *DigiCert globális legfelső szintű hitelesítésszolgáltatóját* használja. Töltse le ezt a tanúsítványt az SSL protokollon keresztüli kommunikációhoz a [DigiCert globális legfelső szintű hitelesítésszolgáltatótól](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem) , és mentse a tanúsítványt a kívánt helyre. Ez az oktatóanyag például a következőt használja: `c:\ssl` .


### <a name="connect-using-psql"></a>Összekapcsolás a psql használatával
Ha a rugalmas kiszolgálót *privát hozzáféréssel (VNet-integrációval)* hozta létre, akkor a kiszolgálóval egy olyan erőforráshoz kell csatlakoznia, amely a kiszolgálóval megegyező VNet belül található. Létrehozhat egy virtuális gépet, és hozzáadhatja azt a rugalmas kiszolgálóval létrehozott VNet.

Ha a rugalmas kiszolgálót *nyilvános hozzáféréssel (engedélyezett IP-címekkel)* hozta létre, akkor a helyi IP-címét felveheti a kiszolgálón található tűzfalszabályok listájára.

Az alábbi példa bemutatja, hogyan csatlakozhat a kiszolgálóhoz a psql parancssori felület használatával. A `sslmode=verify-full` kapcsolati karakterlánc beállításával kényszerítheti a TLS/SSL-tanúsítványok ellenőrzését. Adja át a helyi tanúsítványfájl elérési útját a `sslrootcert` paraméternek.

```bash
 psql "sslmode=verify-full sslrootcert=c:\ssl\DigiCertGlobalRootCA.crt.pem host=mydemoserver.postgres.database.azure.com dbname=postgres user=myadmin"
```
> [!Note]
> Ellenőrizze, hogy az átadott érték megegyezik-e a `sslrootcert` mentett tanúsítvány elérési útjával.

## <a name="ensure-your-application-or-framework-supports-tls-connections"></a>Győződjön meg arról, hogy az alkalmazás vagy a keretrendszer támogatja a TLS-kapcsolatokat

Néhány alkalmazás-keretrendszer, amely a PostgreSQL-t használja az adatbázis-szolgáltatásaihoz, alapértelmezés szerint nem engedélyezi a TLS-t a telepítés során. A PostgreSQL-kiszolgáló kikényszeríti a TLS-kapcsolatokat, de ha az alkalmazás nincs konfigurálva a TLS-hez, előfordulhat, hogy az alkalmazás nem tud csatlakozni az adatbázis-kiszolgálóhoz. A TLS-kapcsolatok engedélyezésével kapcsolatos információkért tekintse meg az alkalmazás dokumentációját.

## <a name="next-steps"></a>Következő lépések
- [Azure Database for PostgreSQL-rugalmas kiszolgálói virtuális hálózat létrehozása és kezelése az Azure CLI-](./how-to-manage-virtual-network-cli.md)vel.
- További információ a [Azure Database for PostgreSQL rugalmas kiszolgáló hálózatkezeléséről](./concepts-networking.md)
- További információ a [Azure Database for PostgreSQL rugalmas kiszolgálói tűzfalszabályok](./concepts-networking.md#public-access-allowed-ip-addresses)
