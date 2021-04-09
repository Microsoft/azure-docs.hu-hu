---
title: Linuxos OpenSSL konfigurálása
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan konfigurálhatja az OpenSSL-t a Linux rendszerhez.
services: cognitive-services
author: jhakulin
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/16/2020
ms.author: jhakulin
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: a6225fec30a87ca0bbe57e414733bc21489f87ad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104577444"
---
# <a name="configure-openssl-for-linux"></a>Linuxos OpenSSL konfigurálása

Ha bármilyen Speech SDK-verziót használ a 1.9.0 előtt, az [OpenSSL](https://www.openssl.org) dinamikusan van konfigurálva a gazda-rendszer verzióra. A Speech SDK újabb verzióiban az OpenSSL ( [1.1.1](https://mta.openssl.org/pipermail/openssl-announce/2019-February/000147.html)-es verzió) statikusan kapcsolódik a Speech SDK alapvető könyvtárához.

A kapcsolat biztosításához ellenőrizze, hogy az OpenSSL-tanúsítványok telepítve vannak-e a rendszerbe. Parancs futtatása:
```bash
openssl version -d
```

Az Ubuntu/Debian-alapú rendszerek kimenetének a következőnek kell lennie:
```
OPENSSLDIR: "/usr/lib/ssl"
```

Győződjön meg arról, hogy a OPENSSLDIR alatt van-e `certs` alkönyvtár. A fenti példában lenne `/usr/lib/ssl/certs` .

* Ha van, `/usr/lib/ssl/certs` és számos egyéni tanúsítványfájl tartalmaz ( `.crt` vagy `.pem` kiterjesztéssel), nincs szükség további műveletekre.

* Ha a OPENSSLDIR valami más, mint a `/usr/lib/ssl` és/vagy a több különálló fájl helyett egyetlen tanúsítványfájl-fájl, meg kell adnia egy megfelelő SSL környezeti változót, hogy jelezze, hol találhatók a tanúsítványok.

## <a name="examples"></a>Példák

- A OPENSSLDIR `/opt/ssl` . Van `certs` több vagy több fájllal rendelkező alkönyvtár `.crt` `.pem` .
Állítsa be a környezeti változót `SSL_CERT_DIR` úgy, hogy a `/opt/ssl/certs` Speech SDK-t használó program futtatása előtt mutasson. Például:
```bash
export SSL_CERT_DIR=/opt/ssl/certs
```

- A OPENSSLDIR `/etc/pki/tls` (például RHEL/CentOS-alapú rendszereken). Van `certs` alkönyvtár egy tanúsítványfájl-fájllal, például: `ca-bundle.crt` .
`SSL_CERT_FILE`A SPEECH SDK-t használó program futtatása előtt állítsa be a környezeti változót úgy, hogy az adott fájlban mutasson. Például:
```bash
export SSL_CERT_FILE=/etc/pki/tls/certs/ca-bundle.crt
```

## <a name="certificate-revocation-checks"></a>Tanúsítványok visszavonásának ellenőrzése
A beszédfelismerési szolgáltatáshoz való csatlakozáskor a Speech SDK ellenőrzi, hogy a beszédfelismerési szolgáltatás által használt TLS-tanúsítvány nem lett-e visszavonva. Az ellenőrzés elvégzéséhez a Speech SDK-nak hozzá kell férnie a CRL terjesztési pontjaihoz az Azure által használt hitelesítésszolgáltatók számára. A lehetséges CRL letöltési helyeinek listája [ebben a dokumentumban](https://docs.microsoft.com/azure/security/fundamentals/tls-certificate-changes)található. Ha a tanúsítvány vissza lett vonva, vagy a CRL nem tölthető le, a Speech SDK megszakítja a kapcsolatokat, és felveszi a megszakított eseményt.

Abban az esetben, ha az a hálózat, amelyben a beszédfelismerési SDK használatban van, olyan módon van konfigurálva, amely nem engedélyezi a hozzáférést a CRL letöltési helyeihez, a CRL-ellenőrzési funkció letiltható, vagy nem lehet sikertelen, ha a CRL nem olvasható be. Ez a konfiguráció a felismerő objektum létrehozásához használt konfigurációs objektumon keresztül történik.

Ha a visszavonási listát nem lehet lekérni, állítsa be a (OPENSSL_CONTINUE_ON_CRL_DOWNLOAD_FAILURE) tulajdonságot a csatlakozás folytatásához.

::: zone pivot="programming-language-csharp"

```csharp
config.SetProperty("OPENSSL_CONTINUE_ON_CRL_DOWNLOAD_FAILURE", "true");
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
config->SetProperty("OPENSSL_CONTINUE_ON_CRL_DOWNLOAD_FAILURE", "true");
```

::: zone-end

::: zone pivot="programming-language-java"

```java
config.setProperty("OPENSSL_CONTINUE_ON_CRL_DOWNLOAD_FAILURE", "true");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
speech_config.set_property_by_name("OPENSSL_CONTINUE_ON_CRL_DOWNLOAD_FAILURE", "true")?
```

::: zone-end

::: zone pivot="programming-language-more"

```ObjectiveC
[config setPropertyTo:@"true" byName:"OPENSSL_CONTINUE_ON_CRL_DOWNLOAD_FAILURE"];
```

::: zone-end
Ha a "true" értékre van állítva, a rendszer megkísérli lekérni a CRL-t, és ha a beolvasás sikeres, a rendszer visszavonásra kéri a tanúsítványt, ha a lekérés sikertelen, a kapcsolat továbbra is engedélyezett lesz.

A tanúsítvány-visszavonási ellenőrzések teljes letiltásához állítsa a tulajdonságot OPENSSL_DISABLE_CRL_CHECK "true" értékre.
::: zone pivot="programming-language-csharp"

```csharp
config.SetProperty("OPENSSL_DISABLE_CRL_CHECK", "true");
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
config->SetProperty("OPENSSL_DISABLE_CRL_CHECK", "true");
```

::: zone-end

::: zone pivot="programming-language-java"

```java
config.setProperty("OPENSSL_DISABLE_CRL_CHECK", "true");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
speech_config.set_property_by_name("OPENSSL_DISABLE_CRL_CHECK", "true")?
```

::: zone-end

::: zone pivot="programming-language-more"

```ObjectiveC
[config setPropertyTo:@"true" byName:"OPENSSL_DISABLE_CRL_CHECK"];
```

::: zone-end


> [!NOTE]
> Azt is érdemes megjegyezni, hogy egyes Linux-disztribúciók nem rendelkeznek meghatározott TMP vagy TMPDIR környezeti változóval. Ez azt eredményezi, hogy a Speech SDK minden alkalommal letölti a visszavont tanúsítványok listáját (CRL) ahelyett, hogy a CRL-t gyorsítótárazza a lemezre a lejáratig. A kezdeti kapcsolat teljesítményének növeléséhez [létrehozhat egy TMPDIR nevű környezeti változót, és beállíthatja a kiválasztott ideiglenes könyvtár elérési útjára.](https://help.ubuntu.com/community/EnvironmentVariables)

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [A Speech SDK ismertetése](speech-sdk.md)
