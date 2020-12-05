---
title: Azure IoT Hub TLS-támogatás
description: Tudnivalók a biztonságos TLS-kapcsolatok használatáról az IoT Hub kommunikáló eszközökhöz és szolgáltatásokhoz
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 11/25/2020
ms.author: jlian
ms.openlocfilehash: f4438aebcb81d665a19a595ac7ade4fea27fc43f
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2020
ms.locfileid: "96621008"
---
# <a name="transport-layer-security-tls-support-in-iot-hub"></a>Transport Layer Security (TLS) támogatása IoT Hub

A IoT Hub Transport Layer Security (TLS) protokollt használ a IoT-eszközök és-szolgáltatások közötti kapcsolatok biztonságossá tételéhez. A TLS protokoll három verziója jelenleg támogatott, azaz a 1,0, 1,1 és 1,2 verziókat.

A TLS 1,0 és a 1,1 örökölt, és elavultnak számít. További információ: [a TLS 1,0 és a 1,1 elavult a IoT hub](iot-hub-tls-deprecating-1-0-and-1-1.md). A jövőbeli problémák elkerüléséhez használja a TLS 1,2-et az egyetlen TLS-verzióként a IoT Hubhoz való csatlakozáskor.

## <a name="iot-hubs-server-tls-certificate"></a>IoT Hub kiszolgáló TLS-tanúsítványa

A TLS-kézfogás során a IoT Hub RSA-kulcsokkal rendelkező kiszolgálói tanúsítványokat jelenít meg az ügyfelek csatlakoztatásához. A gyökér a Baltimore CyberTrust legfelső szintű HITELESÍTÉSSZOLGÁLTATÓja. A közelmúltban a TLS-kiszolgáló tanúsítványának módosítását is bevezetjük, hogy az új köztes hitelesítésszolgáltatók (ICA) számára legyen kiállítva. További információ: [IOT hub TLS-tanúsítvány frissítése](https://azure.microsoft.com/updates/iot-hub-tls-certificate-update/).

### <a name="elliptic-curve-cryptography-ecc-server-tls-certificate-preview"></a>Elliptikus görbe titkosítási (ECC-) kiszolgáló TLS-tanúsítványa (előzetes verzió)

IoT Hub ECC-kiszolgáló TLS-tanúsítványa elérhető nyilvános előzetes verzióra. Az RSA-tanúsítványok hasonló biztonsága mellett az ECC-tanúsítvány ellenőrzése (csak ECC-csomagokkal) akár 40%-kal kevesebb számítási, memória-és sávszélességet használ. Ezek a megtakarítások a kisebb profilok és a memória miatt fontosak az eszközök IoT, valamint a hálózati sávszélesség korlátozott környezetekben történő használati esetek támogatásához. 

IoT Hub ECC-kiszolgálói tanúsítványának előnézete:

1. [Hozzon létre egy új IoT hubot előzetes módban](iot-hub-preview-mode.md).
1. [Konfigurálja úgy az ügyfelet](#tls-configuration-for-sdk-and-iot-edge) , hogy *csak* ECDSA titkosítási csomagokat tartalmazzon, és *kizárjon* minden RSA-t. Ezek a támogatott titkosítási csomagok az ECC-tanúsítvány nyilvános előzetes verziójához:
    - `TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256`
    - `TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384`
    - `TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA256`
    - `TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA384`
1. Kapcsolja össze az ügyfelet az előzetes verziójú IoT hubhoz.

## <a name="tls-12-enforcement-available-in-select-regions"></a>A TLS 1,2 kényszerítés a kiválasztott régiókban érhető el

A további biztonság érdekében konfigurálja a IoT hubokat úgy, hogy *csak* a 1,2-es TLS-verziót használó ügyfélkapcsolatokat engedélyezze, és kényszerítse a [titkosítási csomagok](#cipher-suites)használatát. Ez a funkció csak a következő régiókban támogatott:

* USA keleti régiója
* USA déli középső régiója
* USA 2. nyugati régiója
* USA-beli államigazgatás – Arizona
* USA-beli államigazgatás – Virginia

Erre a célra hozzon létre egy új IoT Hub a bármelyik támogatott régióban, és állítsa be a `minTlsVersion` tulajdonságot a `1.2` Azure Resource Manager sablon IoT hub erőforrás-specifikációjában:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.Devices/IotHubs",
            "apiVersion": "2020-01-01",
            "name": "<provide-a-valid-resource-name>",
            "location": "<any-of-supported-regions-below>",
            "properties": {
                "minTlsVersion": "1.2"
            },
            "sku": {
                "name": "<your-hubs-SKU-name>",
                "tier": "<your-hubs-SKU-tier>",
                "capacity": 1
            }
        }
    ]
}
```

Az ezzel a konfigurációval létrehozott IoT Hub erőforrás elutasítja az eszköz-és szolgáltatás-ügyfeleket, amelyek a 1,0-es és a 1,1-es TLS-verzióval próbálnak csatlakozni. Hasonlóképpen a TLS-kézfogás elutasítása is megtagadható, ha az `ClientHello` üzenet nem sorolja fel a [javasolt titkosítási algoritmusokat](#cipher-suites).

> [!NOTE]
> A `minTlsVersion` tulajdonság csak olvasható, és a IoT hub-erőforrás létrehozása után nem módosítható. Ezért fontos, hogy megfelelően tesztelje és ellenőrizze *, hogy a* IoT-eszközök és-szolgáltatások kompatibilisek-e a TLS 1,2-mel és az [ajánlott titkosítási](#cipher-suites) megoldásokkal.
> 
> Feladatátvétel esetén a `minTlsVersion` IoT hub tulajdonsága továbbra is érvényben marad a helyszíni, a feladatátvételt követő régióban.

## <a name="cipher-suites"></a>Titkosítási csomagok

A csak a TLS 1,2 használatára konfigurált IoT hubok a következő ajánlott titkosítási csomagok használatát is kikényszerítik:

* `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384`

A TLS 1,2-kényszerítéshez nem konfigurált IoT-hubok esetében a TLS 1,2 továbbra is a következő titkosítási csomagokkal működik:

* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_DHE_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_DHE_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_RSA_WITH_AES_256_CBC_SHA256`
* `TLS_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_AES_256_CBC_SHA`
* `TLS_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_3DES_EDE_CBC_SHA`

Az ügyfél javasolhatja, hogy a rendszer milyen magasabb titkosítási csomagokat használjon `ClientHello` . Előfordulhat azonban, hogy néhányat IoT Hub nem támogat (például: `ECDHE-ECDSA-AES256-GCM-SHA384` ). Ebben az esetben a IoT Hub megpróbálja követni az ügyfél beállításait, de végül egyezteti a titkosító csomagot a használatával `ServerHello` .

## <a name="tls-configuration-for-sdk-and-iot-edge"></a>Az SDK és a IoT Edge TLS-konfigurációja

Az alábbi hivatkozásokkal konfigurálhatja a TLS 1,2 és az engedélyezett titkosítási algoritmusokat IoT Hub ügyféloldali SDK-k használatával.

| Nyelv | A TLS 1,2-et támogató verziók | Dokumentáció |
|----------|------------------------------------|---------------|
| C#        | 2019-12-11 vagy újabb címke            | [Hivatkozás](https://aka.ms/Tls_C_SDK_IoT) |
| Python   | 2.0.0 vagy újabb verzió             | [Hivatkozás](https://aka.ms/Tls_Python_SDK_IoT) |
| C#       | 1.21.4 vagy újabb verzió            | [Hivatkozás](https://aka.ms/Tls_CSharp_SDK_IoT) |
| Java     | 1.19.0 vagy újabb verzió            | [Hivatkozás](https://aka.ms/Tls_Java_SDK_IoT) |
| NodeJS   | 1.12.2 vagy újabb verzió            | [Hivatkozás](https://aka.ms/Tls_Node_SDK_IoT) |

A IoT Edge-eszközök úgy konfigurálhatók, hogy a TLS 1,2-et használják a IoT Hubval való kommunikáció során. Erre a célra használja a [IoT Edge dokumentációs oldalát](https://github.com/Azure/iotedge/blob/master/edge-modules/edgehub-proxy/README.md).

## <a name="device-authentication"></a>Eszköz hitelesítése

A sikeres TLS-kézfogás után a IoT Hub szimmetrikus kulccsal vagy X. 509 tanúsítvánnyal tud hitelesíteni egy eszközt. Tanúsítványalapú hitelesítés esetén ez lehet bármilyen X. 509 tanúsítvány, beleértve az ECC-t is. IoT Hub érvényesíti a tanúsítványt az Ön által megadott ujjlenyomat vagy hitelesítésszolgáltató (CA) ellen. További információ: [támogatott X. 509 tanúsítványok](iot-hub-devguide-security.md#supported-x509-certificates).

## <a name="tls-maximum-fragment-length-negotiation-preview"></a>TLS-adattöredékek maximális hosszának egyeztetése (előzetes verzió)

A IoT Hub támogatja a TLS maximális töredékes egyeztetését is, ami más néven a TLS-keret méretének egyeztetése. Ez a funkció nyilvános előzetes verzióban érhető el. 

Ezzel a szolgáltatással adhatja meg, hogy a rendszer legfeljebb az alapértelmezett 2 ^ 14 bájtnál kisebb értéket válasszon az egyszerű szöveges töredék hosszának. Az egyeztetést követően a IoT Hub és az ügyfél megkezdi az üzenetek töredezettségének megkezdését, hogy az összes töredék kisebb legyen, mint az egyeztetett hossz. Ez a viselkedés hasznos a számítási és a memóriában korlátozott eszközök esetében. További információ: a [hivatalos TLS-bővítmény specifikációja](https://tools.ietf.org/html/rfc6066#section-4).

A nyilvános előzetes verzióhoz tartozó hivatalos SDK-támogatás még nem érhető el. Első lépések

1. [Hozzon létre egy új IoT hubot előzetes módban](iot-hub-preview-mode.md).
1. Az OpenSSL használatakor hívja meg a [SSL_CTX_set_tlsext_max_fragment_lengtht](https://manpages.debian.org/testing/libssl-doc/SSL_CTX_set_max_send_fragment.3ssl.en.html) a töredék méretének megadásához.
1. Kapcsolja össze az ügyfelet az előzetes verzió IoT Hubával.

## <a name="next-steps"></a>További lépések

- A IoT Hub biztonsági és hozzáférés-vezérléssel kapcsolatos további tudnivalókért tekintse meg a [IoT hub hozzáférésének szabályozása](iot-hub-devguide-security.md)című témakört.
- További információ az X509-tanúsítvány használatáról az eszközök hitelesítéséhez: [eszközök hitelesítése X. 509 hitelesítésszolgáltatói tanúsítványokkal](iot-hub-x509ca-overview.md)