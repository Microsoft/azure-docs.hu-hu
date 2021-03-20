---
title: A bejövő hívások ügyfél-hitelesítésének konfigurálása – Azure Event Grid IoT Edge | Microsoft Docs
description: Ismerje meg a Event Grid modul lehetséges ügyfél-hitelesítési konfigurációit.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 9525da4204e270d033f0c2354318bd71874eaf54
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91290772"
---
# <a name="configure-client-authentication-of-incoming-calls"></a>A bejövő hívások ügyfél-hitelesítésének konfigurálása

Ez az útmutató példákat mutat be a Event Grid modul lehetséges ügyfél-hitelesítési konfigurációjáról. A Event Grid modul két típusú ügyfél-hitelesítést támogat:

* Közös hozzáférésű aláírás (SAS) kulcs alapú
* Tanúsítvány alapú

Tekintse meg a [biztonsági és hitelesítési](security-authentication.md) útmutatót az összes lehetséges konfigurációhoz.

## <a name="enable-certificate-based-client-authentication-no-self-signed-certificates"></a>Tanúsítvány alapú ügyfél-hitelesítés engedélyezése, önaláírt tanúsítványok nélkül

```json
 {
  "Env": [
    "inbound__clientAuth__sasKeys__enabled=false",
    "inbound__clientAuth__clientCert__enabled=true",
    "inbound__clientAuth__clientCert__source=IoTEdge",
    "inbound__clientAuth__clientCert__allowUnknownCA=false"
  ]
}
 ```

## <a name="enable-certificate-based-client-authentication-allow-self-signed-certificates"></a>Tanúsítvány alapú ügyfél-hitelesítés engedélyezése, önaláírt tanúsítványok engedélyezése

```json
 {
  "Env": [
    "inbound__clientAuth__sasKeys__enabled=false",
    "inbound__clientAuth__clientCert__enabled=true",
    "inbound__clientAuth__clientCert__source=IoTEdge",
    "inbound__clientAuth__clientCert__allowUnknownCA=true"
  ]
}
```

>[!NOTE]
>Állítsa a tulajdonságot a **inbound__clientAuth__clientCert__allowUnknownCA** **igaz** értékre csak tesztkörnyezetben, mivel általában önaláírt tanúsítványokat is használhat. Éles számítási feladatokhoz azt javasoljuk, hogy ezt a tulajdonságot **hamis** értékre állítsa be, és egy hitelesítésszolgáltatótól (CA) származó tanúsítványokat.

## <a name="enable-certificate-based-and-sas-key-based-client-authentication"></a>Tanúsítvány-alapú és Sas-kulcs alapú ügyfél-hitelesítés engedélyezése

```json
 {
  "Env": [
    "inbound__clientAuth__sasKeys__enabled=true",
    "inbound__clientAuth__sasKeys__key1=<some-secret1-here>",
    "inbound__clientAuth__sasKeys__key2=<some-secret2-here>",
    "inbound__clientAuth__clientCert__enabled=true",
    "inbound__clientAuth__clientCert__source=IoTEdge",
    "inbound__clientAuth__clientCert__allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>Az SAS-kulcs alapú ügyfél-hitelesítés lehetővé teszi a nem IoT Edge-modul számára a felügyeleti és futtatókörnyezeti műveletek elvégzését, feltételezve, hogy az API-portok a IoT Edge hálózaton kívül is elérhetők.
