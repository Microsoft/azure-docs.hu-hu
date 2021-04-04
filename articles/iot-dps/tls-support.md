---
title: Az Azure IoT Device kiépítési szolgáltatás (DPS) TLS-támogatása
description: Ajánlott eljárások a IoT Device kiépítési szolgáltatással (DPS) kommunikáló eszközök és szolgáltatások biztonságos TLS-kapcsolatainak használatával
services: iot-dps
author: wesmc7777
ms.service: iot-dps
ms.topic: conceptual
ms.date: 09/14/2020
ms.author: wesmc
ms.openlocfilehash: 3a8910cf0e81bd041d74ef95f45220f1c1e0b34c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91761294"
---
# <a name="tls-support-in-azure-iot-hub-device-provisioning-service-dps"></a>TLS-támogatás az Azure IoT Hub Device Provisioning Serviceban (DPS)

A DPS [Transport Layer Security (TLS)](http://wikipedia.org/wiki/Transport_Layer_Security) protokollt használ a IoT-eszközök közötti kapcsolatok biztonságossá tételéhez. 

A DPS által támogatott jelenlegi TLS protokoll-verziók a következők: 
* TLS 1.2

A TLS 1,0 és a 1,1 örökölt, és elavultnak számít. További információ: [a TLS 1,0 és a 1,1 elavult a IoT hub](../iot-hub/iot-hub-tls-deprecating-1-0-and-1-1.md). 

## <a name="restrict-connections-to-tls-12"></a>A TLS 1,2-kapcsolat korlátozása

A további biztonság érdekében javasoljuk, hogy konfigurálja a DPS-példányokat úgy, hogy *csak* az 1,2-es TLS-t használó eszközök ügyfélkapcsolatait engedélyezze, és kényszerítse az [ajánlott titkosítási algoritmusok](#recommended-ciphers)használatát.

Ehhez hozzon létre egy új DPS-erőforrást, amely a `minTlsVersion` tulajdonságot a `1.2` Azure Resource Manager sablon DPS erőforrás-specifikációjában állítja be. A következő példában szereplő sablon JSON `minTlsVersion` egy új DPS-példány tulajdonságát határozza meg.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.Devices/ProvisioningServices",
            "apiVersion": "2020-01-01",
            "name": "<provide-a-valid-DPS-resource-name>",
            "location": "<any-region>",
            "properties": {
                "minTlsVersion": "1.2"
            },
            "sku": {
                "name": "S1",
                "capacity": 1
            },
        }     
    ]
}
```

A sablont a következő Azure CLI-parancs használatával telepítheti. 

```azurecli
az deployment group create -g <your resource group name> --template-file template.json
```

A DPS-erőforrások Resource Manager-sablonokkal való létrehozásával kapcsolatos további információkért lásd: a [DPS beállítása Azure Resource Manager sablonnal](quick-setup-auto-provision-rm.md).

Az ezzel a konfigurációval létrehozott DPS-erőforrás elutasítja azokat az eszközöket, amelyek a 1,0-es és a 1,1-es TLS-verzióval próbálnak csatlakozni. Hasonlóképpen, a TLS-kézfogás visszautasítva lesz, ha az ügyfél HELLO üzenete nem sorolja fel a [javasolt titkosítási algoritmusokat](#recommended-ciphers).

> [!NOTE]
> A `minTlsVersion` tulajdonság csak olvasható, és a DPS-erőforrás létrehozása után nem módosítható. Ezért fontos, hogy megfelelően ellenőrizze és ellenőrizze, hogy az *összes* IoT-eszköz kompatibilis-e a TLS 1,2-mel és az [ajánlott titkosítási](#recommended-ciphers) megoldásokkal.


> [!NOTE]
> Feladatátvétel esetén a `minTlsVersion` DPS tulajdonsága továbbra is érvényben marad a feladatátvételt követően a Geo-párosítási régióban.

## <a name="recommended-ciphers"></a>Ajánlott titkosítási algoritmusok

A csak a TLS 1,2 használatára konfigurált DPS-példányok a következő titkosítási csomagok használatát is kikényszerítik:


| Ajánlott TLS 1,2 titkosítási csomagok |
| :--- |
| `TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`<br>`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`<br>`TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384`<br>`TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256` |


### <a name="legacy-cipher-suites"></a>Örökölt titkosítási csomagok 

A DPS jelenleg továbbra is támogatja ezeket a titkosítási csomagokat, de a rendszer az értékcsökkenést is elvégzi. Ha lehetséges, használja a fentiekben javasolt titkosítási csomagokat.

| #1 lehetőség (jobb biztonság) |
| :--- |
| `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA_P384   (uses SHA-1)`<br>`TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA_P256   (uses SHA-1)`<br>`TLS_RSA_WITH_AES_256_GCM_SHA384           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_128_GCM_SHA256           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_256_CBC_SHA256           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_128_CBC_SHA256           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_256_CBC_SHA              (uses SHA-1, lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_128_CBC_SHA              (uses SHA-1, lack of Perfect Forward Secrecy)` |

| Option #2 (jobb teljesítmény) |
| :--- |
| `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA_P256   (uses SHA-1)`<br>`TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA_P384   (uses SHA-1)`<br>`TLS_RSA_WITH_AES_128_GCM_SHA256           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_256_GCM_SHA384           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_128_CBC_SHA256           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_256_CBC_SHA256           (lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_128_CBC_SHA              (uses SHA-1, lack of Perfect Forward Secrecy)`<br>`TLS_RSA_WITH_AES_256_CBC_SHA              (uses SHA-1, lack of Perfect Forward Secrecy)` |


## <a name="use-tls-12-in-the-iot-sdks"></a>A TLS 1,2 használata a IoT SDK-k használatával

Az alábbi hivatkozásokkal konfigurálhatja a TLS 1,2 és az engedélyezett titkosítási algoritmusokat az Azure IoT ügyféloldali SDK-k segítségével.

| Nyelv | A TLS 1,2-et támogató verziók | Dokumentáció |
|----------|------------------------------------|---------------|
| C        | 2019-12-11 vagy újabb címke            | [Hivatkozás](https://aka.ms/Tls_C_SDK_IoT) |
| Python   | 2.0.0 vagy újabb verzió             | [Hivatkozás](https://aka.ms/Tls_Python_SDK_IoT) |
| C#       | 1.21.4 vagy újabb verzió            | [Hivatkozás](https://aka.ms/Tls_CSharp_SDK_IoT) |
| Java     | 1.19.0 vagy újabb verzió            | [Hivatkozás](https://aka.ms/Tls_Java_SDK_IoT) |
| NodeJS   | 1.12.2 vagy újabb verzió            | [Hivatkozás](https://aka.ms/Tls_Node_SDK_IoT) |

## <a name="use-tls-12-with-iot-hub"></a>A TLS 1,2 használata IoT Hub

A IoT Hub konfigurálható úgy, hogy a TLS 1,2-et használja az eszközökkel való kommunikációhoz. További információ: [a TLS 1,0 és a 1,1 elavult a IoT hub](../iot-hub/iot-hub-tls-deprecating-1-0-and-1-1.md).

## <a name="use-tls-12-with-iot-edge"></a>A TLS 1,2 használata IoT Edge

IoT Edge eszközök a TLS 1,2 használatára konfigurálhatók a IoT Hub és a DPS szolgáltatással való kommunikáció során. További információ: [IoT Edge dokumentációs oldal](https://github.com/Azure/iotedge/blob/master/edge-modules/edgehub-proxy/README.md).
