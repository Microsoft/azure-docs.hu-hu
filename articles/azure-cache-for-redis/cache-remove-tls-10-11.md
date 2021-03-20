---
title: A TLS 1,0-es és 1,1-es verziójának eltávolítása az Azure cache használatával a Redis-hez
description: Ismerje meg, hogyan távolíthatja el a TLS 1,0 és 1,1 alkalmazást az alkalmazásból az Azure cache Redis való kommunikációja során
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: yegu
ms.openlocfilehash: fd0e6f893d152259c46ff06e9ec20af54395c5e6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95994383"
---
# <a name="remove-tls-10-and-11-from-use-with-azure-cache-for-redis"></a>A TLS 1,0-es és 1,1-es verziójának eltávolítása az Azure cache használatával a Redis-hez

A Transport Layer Security (TLS) 1,2-es vagy újabb verziójának kizárólagos használatára az egész iparágra kiterjedő leküldés. A 1,0-es és 1,1-as TLS-verziók ismertek olyan támadásokra, mint például a BEAST és az USZKÁR, valamint más gyakori biztonsági rések és a kitettségek (CVE) gyengeségei. Emellett nem támogatják a Payment Card Industry (PCI) megfelelőségi szabványai által ajánlott modern titkosítási módszereket és titkosítási csomagokat. Ez a [TLS biztonsági blog](https://www.acunetix.com/blog/articles/tls-vulnerabilities-attacks-final-part/) részletesebben ismerteti ezeket a biztonsági réseket.

Ennek a tevékenységnek a részeként a következő módosításokat hajtjuk végre az Azure cache Redis:

* **1. fázis:** Az újonnan létrehozott gyorsítótár-példányok esetében a 1,2-as alapértelmezett minimális TLS-verziót fogjuk konfigurálni (korábban TLS 1,0 volt). Ezen a ponton nem frissülnek a meglévő gyorsítótár-példányok. Továbbra is használhatja a Azure Portal vagy más felügyeleti API-kat, hogy [a minimális TLS-verziót](cache-configure.md#access-ports) 1,0-re vagy 1,1-re módosítsa a visszamenőleges kompatibilitás érdekében, ha szükséges.
* **2. fázis:** Leállítjuk a TLS 1,1 és a TLS 1,0 támogatását. A módosítás után az alkalmazásnak a TLS 1,2-es vagy újabb verzióját kell használnia a gyorsítótárral való kommunikációhoz. A Redis szolgáltatás Azure cache-je várhatóan elérhető lesz, amíg csak a TLS 1,2-es vagy újabb verziójának támogatására telepítik.

  > [!NOTE]
  > A 2. fázis előreláthatólag nem korábbi, mint 2020. december 31-ig kezdődik. Azt javasoljuk azonban, hogy kezdje el most megtervezni ezt a módosítást, és proaktív módon frissítse az ügyfeleket a TLS 1,2-es vagy újabb verzióinak támogatásához. 
  >

Ennek a változásnak a részeként a régebbi, nem biztonságos Cypher-csomagok támogatását is eltávolítja. A támogatott Cypher-csomagok a következő csomagokra lesznek korlátozva, ha a gyorsítótár legalább TLS 1,2-mel van konfigurálva:

* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384_P384
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256_P256

Ez a cikk általános útmutatást nyújt a korábbi TLS-verziók függőségeinek észleléséhez és az alkalmazásból való eltávolításához.

A módosítások érvénybe léptetésének dátuma:

| Felhőbeli                | 1. fázis kezdési dátuma | 2. fázis kezdő dátuma         |
|----------------------|--------------------|----------------------------|
| Azure (globális)       |  2020. január 13.  | Elhalasztott COVID-19 miatt  |
| Azure Government     |  Március 13., 2020    | Elhalasztott COVID-19 miatt  |
| Azure Germany        |  Március 13., 2020    | Elhalasztott COVID-19 miatt  |
| Azure China 21Vianet |  Március 13., 2020    | Elhalasztott COVID-19 miatt  |

> [!NOTE]
> A 2. fázis előreláthatólag nem korábbi, mint 2020. december 31-ig kezdődik. Ez a cikk akkor frissül, ha meghatározott dátumok vannak beállítva.
>

## <a name="check-whether-your-application-is-already-compliant"></a>Annak ellenőrzését, hogy az alkalmazás már megfelelő-e

A legkönnyebben megtudhatja, hogy az alkalmazás a TLS 1,2-mel működik-e, ha a TLS- **verzió minimális** értékét a TLS 1,2-re állítja be egy tesztelési vagy előkészítési gyorsítótárban, majd futtatja a teszteket. A **TLS minimális verziója** beállítás a Azure Portal gyorsítótár-példányának [speciális beállításaiban](cache-configure.md#advanced-settings) található.  Ha az alkalmazás a változás után is továbbra is a várt módon működik, valószínűleg megfelelőnek kell lennie. Előfordulhat, hogy konfigurálnia kell az alkalmazás által használt Redis ügyféloldali kódtárat a TLS 1,2 engedélyezéséhez ahhoz, hogy csatlakozni lehessen az Azure cache-hez a Redis.

## <a name="configure-your-application-to-use-tls-12"></a>Az alkalmazás konfigurálása a TLS 1,2 használatára

A legtöbb alkalmazás Redis-kódtárakat használ a gyorsítótárral való kommunikáció kezelésére. Az alábbiakban útmutatást talál néhány, a különböző programozási nyelveken és keretrendszerekben található népszerű ügyféloldali függvénytár konfigurálásához a TLS 1,2 használatához.

### <a name="net-framework"></a>.NET-keretrendszer

A Redis .NET-ügyfelek alapértelmezés szerint a legkorábbi TLS-verziót használják a .NET-keretrendszer 4.5.2-es vagy korábbi verziójában, és a legújabb TLS-verziót használják a .NET-keretrendszer 4,6-es vagy újabb verziójára Ha a .NET-keretrendszer régebbi verzióját használja, a TLS 1,2-et manuálisan is engedélyezheti:

* **StackExchange. Redis:** Állítsa `ssl=true` be `sslprotocols=tls12` a és a karakterláncot a kapcsolatok karakterláncában.
* **ServiceStack. Redis:** Kövesse a [ServiceStack. Redis](https://github.com/ServiceStack/ServiceStack.Redis#servicestackredis-ssl-support) utasításokat, és a ServiceStack. Redis v 5.6 minimálisan szükséges.

### <a name="net-core"></a>.NET Core

A .NET Core-ügyfelek alapértelmezés szerint az operációs rendszer alapértelmezett TLS-verziójára Redis, ami nyilvánvalóan az operációs rendszertől függ. 

Az operációs rendszer verziójától és az alkalmazott javításoktől függően a jelenlegi alapértelmezett TLS-verzió változhat. Ez a cikk egy, a Windows rendszerre vonatkozó [információt is biztosít](/dotnet/framework/network-programming/tls#support-for-tls-12) . 

Ha azonban egy régebbi operációs rendszert használ, vagy csak azt szeretné, hogy biztosan biztos legyen benne, javasoljuk, hogy az ügyfélen keresztül manuálisan konfigurálja az elsődleges TLS-verziót.


### <a name="java"></a>Java

A Redis Java-ügyfelek TLS 1,0-et használnak a Java 6-os vagy korábbi verzióján. A jedis, a saláta és a Redisson nem tud csatlakozni az Azure cache-hez a Redis, ha a TLS 1,0 le van tiltva a gyorsítótárban. A Java-keretrendszer frissítése új TLS-verziók használatára.

A Java 7 esetében a Redis-ügyfelek alapértelmezés szerint nem használják a TLS 1,2-et, de konfigurálható rá. A jedis segítségével megadhatja a mögöttes TLS-beállításokat a következő kódrészlettel:

``` Java
SSLSocketFactory sslSocketFactory = (SSLSocketFactory) SSLSocketFactory.getDefault();
SSLParameters sslParameters = new SSLParameters();
sslParameters.setEndpointIdentificationAlgorithm("HTTPS");
sslParameters.setProtocols(new String[]{"TLSv1.2"});
 
URI uri = URI.create("rediss://host:port");
JedisShardInfo shardInfo = new JedisShardInfo(uri, sslSocketFactory, sslParameters, null);
 
shardInfo.setPassword("cachePassword");
 
Jedis jedis = new Jedis(shardInfo);
```

A saláta és a Redisson-ügyfelek még nem támogatják a TLS-verzió megadását, ezért a gyorsítótár csak TLS 1,2-kapcsolatokat fogad el. Az ügyfelekre vonatkozó javítások felülvizsgálata folyamatban van, ezért ezeket a csomagokat egy frissített verzióra kell ellenőrizni.

A Java 8 esetében a TLS 1,2 alapértelmezés szerint használatos, és a legtöbb esetben nem szükséges frissíteni az ügyfél konfigurációját. A biztonság érdekében tesztelje az alkalmazást.

### <a name="nodejs"></a>Node.js

A Node Redis és a IORedis alapértelmezés szerint a TLS 1,2-et használja.

### <a name="php"></a>PHP

#### <a name="predis"></a>Predis
 
* PHP 7-es verziónál korábbi verziók: a Predis csak a TLS 1,0-et támogatja. Ezek a verziók nem működnek a TLS 1,2; a TLS 1,2 használatára kell frissítenie.
 
* PHP 7,0 – PHP 7.2.1: a Predis alapértelmezés szerint csak a TLS 1,0 vagy 1,1 protokollt használja. A TLS 1,2 használatát a következő megkerülő megoldással végezheti el. A TLS 1,2 megadása az ügyfél példányának létrehozásakor:

  ``` PHP
  $redis=newPredis\Client([
      'scheme'=>'tls',
      'host'=>'host',
      'port'=>6380,
      'password'=>'password',
      'ssl'=>[
          'crypto_type'=>STREAM_CRYPTO_METHOD_TLSv1_2_CLIENT,
      ],
  ]);
  ```

* PHP 7,3 és újabb verziók: a Predis a legújabb TLS-verziót használja.

#### <a name="phpredis"></a>PhpRedis

A PhpRedis nem támogatja a TLS használatát bármilyen PHP-verzióban.

### <a name="python"></a>Python

A Redis-másolási szolgáltatás alapértelmezés szerint a TLS 1,2 protokollt használja.

### <a name="go"></a>GO

A Redigo alapértelmezés szerint a TLS 1,2-et használja.

## <a name="additional-information"></a>További információ

- [Az Azure cache konfigurálása a Redis-hez](cache-configure.md)