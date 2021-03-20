---
title: Transport Layer Security (TLS) – nagy kapacitású (Citus) – Azure Database for PostgreSQL
description: Útmutatás és információk a Azure Database for PostgreSQL-nagy kapacitású (Citus) és a társított alkalmazások konfigurálásához a TLS-kapcsolatok megfelelő használatához.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 07/16/2020
ms.openlocfilehash: 659f86a107e4b08db4ec5195635ea32d2260d677
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "87071453"
---
# <a name="configure-tls-in-azure-database-for-postgresql---hyperscale-citus"></a>A TLS konfigurálása Azure Database for PostgreSQL-nagy kapacitású (Citus)
Az nagy kapacitású (Citus) koordinátor csomópontjának az Transport Layer Security (TLS) szolgáltatással való kapcsolódáshoz az ügyfélalkalmazások szükségesek. Az adatbázis-kiszolgáló és az ügyfélalkalmazások közötti TLS-kényszerítés segíti az adatok átvitelét. Az alábbiakban ismertetett további ellenőrzési beállítások a "személyek közti" támadásokkal szembeni védelmet is biztosítanak.

## <a name="enforcing-tls-connections"></a>TLS-kapcsolatok kényszerítése
Az alkalmazások "kapcsolódási karakterláncot" használnak a céladatbázis és a kapcsolatok beállításainak azonosítására. A különböző ügyfelek eltérő beállításokat igényelnek. Ha meg szeretné tekinteni az általános ügyfelek által használt kapcsolódási karakterláncok listáját, tekintse meg a Azure Portalban található kiszolgálói csoport **kapcsolódási karakterláncok** szakaszát.

A TLS-paraméterek `ssl` és az `sslmode` összekötő képességeitől függően változhatnak, például: `ssl=true` vagy `sslmode=require` vagy `sslmode=required` .

## <a name="ensure-your-application-or-framework-supports-tls-connections"></a>Győződjön meg arról, hogy az alkalmazás vagy a keretrendszer támogatja a TLS-kapcsolatokat
Egyes alkalmazás-keretrendszerek alapértelmezés szerint nem engedélyezik a TLS-kapcsolatokat a PostgreSQL-kapcsolatok esetében. Biztonságos kapcsolat nélkül azonban az alkalmazás nem tud csatlakozni egy nagy kapacitású (Citus) koordinátori csomóponthoz. A TLS-kapcsolatok engedélyezésével kapcsolatos információkért tekintse meg az alkalmazás dokumentációját.

## <a name="applications-that-require-certificate-verification-for-tls-connectivity"></a>A TLS-kapcsolat tanúsítvány-ellenőrzését igénylő alkalmazások
Bizonyos esetekben az alkalmazásoknak egy megbízható hitelesítésszolgáltató (CA) tanúsítványfájl (. cer) alapján létrehozott helyi tanúsítványfájl szükségesek a biztonságos kapcsolódáshoz. A Azure Database for PostgreSQL-nagy kapacitású (Citus) szolgáltatáshoz való kapcsolódáshoz szükséges tanúsítvány a következő helyen található: https://cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem . Töltse le a tanúsítványfájl, és mentse a kívánt helyre.

> [!NOTE]
>
> A tanúsítvány hitelességének ellenőrzéséhez az OpenSSL parancssori eszköz használatával ellenőrizheti az SHA-256 ujjlenyomatot:
>
> ```sh
> openssl x509 -in DigiCertGlobalRootCA.crt.pem -noout -sha256 -fingerprint
>
> # should output:
> # 43:48:A0:E9:44:4C:78:CB:26:5E:05:8D:5E:89:44:B4:D8:4F:96:62:BD:26:DB:25:7F:89:34:A4:43:C7:01:61
> ```

### <a name="connect-using-psql"></a>Összekapcsolás a psql használatával
Az alábbi példa bemutatja, hogyan csatlakozhat a nagy kapacitású (Citus) koordinátor-csomóponthoz a psql parancssori segédprogram használatával. A `sslmode=verify-full` TLS-tanúsítvány ellenőrzésének érvényesítéséhez használja a kapcsolódási karakterlánc beállítást. Adja át a helyi tanúsítványfájl elérési útját a `sslrootcert` paraméternek.

Az alábbi példa a psql-kapcsolatok karakterláncát mutatja be:
```
psql "sslmode=verify-full sslrootcert=DigiCertGlobalRootCA.crt.pem host=mydemoserver.postgres.database.azure.com dbname=citus user=citus password=your_pass"
```
> [!TIP]
> Ellenőrizze, hogy az átadott érték megegyezik-e a `sslrootcert` mentett tanúsítvány elérési útjával.

## <a name="next-steps"></a>Következő lépések
[A Azure Database for PostgreSQL-nagy kapacitású (Citus) tűzfalszabályok](concepts-hyperscale-firewall-rules.md)további biztonságának fokozása.
