---
title: Biztonsági ügynök hitelesítési módszerei
description: Ismerje meg a Defender for IoT szolgáltatás használatakor elérhető különböző hitelesítési módszereket.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/24/2021
ms.author: shhazam
ms.openlocfilehash: 85a5edc1485cb5a1eeb4428b201380ad9ca89422
ms.sourcegitcommit: f6193c2c6ce3b4db379c3f474fdbb40c6585553b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2021
ms.locfileid: "102449169"
---
# <a name="security-agent-authentication-methods"></a>Biztonsági ügynök hitelesítési módszerei

Ez a cikk ismerteti a különböző hitelesítési módszereket, amelyekkel a AzureIoTSecurity-ügynökkel végezheti el a hitelesítést a IoT Hub.

Egy biztonsági modulra van szükség minden olyan eszközhöz, amely a Defender IoT Hub IoT van bevezetve. Az eszköz hitelesítéséhez a Defender for IoT két módszer egyikét használhatja. Válassza ki a meglévő IoT-megoldáshoz legjobban illő módszert.

- SecurityModule beállítás
- Eszköz beállítás

## <a name="authentication-methods"></a>Hitelesítési módszerek

Az IoT AzureIoTSecurity-ügynök két módszere a hitelesítés elvégzéséhez:

- **SecurityModule** hitelesítési mód<br>
Az ügynököt a biztonsági modul identitása alapján hitelesíti a rendszer az eszköz identitástól függetlenül.
Akkor használja ezt a hitelesítési típust, ha azt szeretné, hogy a biztonsági ügynök egy dedikált hitelesítési módszert használjon a biztonsági modulon keresztül (csak szimmetrikus kulcs).

- **Eszköz** hitelesítési módja<br>
Ebben a metódusban a biztonsági ügynök először hitelesíti magát az eszköz identitásával. A kezdeti hitelesítés után a Defender for IoT-ügynök **Rest** -hívást hajt végre a IoT hub az eszköz hitelesítési adataival REST API használatával. A Defender for IoT-ügynök ezután kéri a biztonsági modul hitelesítési módszerét és adatait a IoT Hubból. Az utolsó lépésben a Defender for IoT ügynök hitelesítést végez a Defender for IoT modulban.

Akkor használja ezt a hitelesítési típust, ha azt szeretné, hogy a biztonsági ügynök felhasználja a meglévő eszköz-hitelesítési módszert (önaláírt tanúsítvány vagy szimmetrikus kulcs).

A konfigurálásának megismeréséhez tekintse meg a [biztonsági ügynök telepítési paramétereit](#security-agent-installation-parameters) .

## <a name="authentication-methods-known-limitations"></a>Ismert hitelesítési módszerek

- A **SecurityModule** hitelesítési mód csak a szimmetrikus kulcsos hitelesítést támogatja.
- Az **eszköz** -hitelesítési mód nem támogatja CA-Signed a tanúsítványt.

## <a name="security-agent-installation-parameters"></a>Biztonsági ügynök telepítési paraméterei

[Biztonsági ügynök telepítésekor a](how-to-deploy-agent.md)hitelesítési adatokat argumentumként kell megadni.
Ezek az argumentumok a következő táblázatban vannak dokumentálva.

|Linux-paraméter neve | Windows-paraméter neve | Gyorsírás paraméter |Description|Beállítások|
|---------------------|---------------|---------|---------------|---------------|
|hitelesítés – identitás|AuthenticationIdentity|AUI|Hitelesítési identitás| **SecurityModule** vagy- **eszköz**|
|hitelesítés – metódus|AuthenticationMethod|Aum|Hitelesítési módszer|**SymmetricKey** vagy **SelfSignedCertificate**|
|fájl elérési útja|FilePath|f|A tanúsítványt vagy a szimmetrikus kulcsot tartalmazó fájl teljes elérési útja| |
|gazdagép neve|HostName|HN|A IoT Hub teljes tartományneve|Példa: ContosoIotHub.azure-devices.net|
|eszköz azonosítója|DeviceId|bú|Eszközazonosító|Példa: MyDevice1|
|tanúsítvány – hely típusú|CertificateLocationKind|CL|Tanúsítvány tárolási helye|**LocalFile** vagy- **tároló**|
|

A biztonsági ügynök telepítése parancsfájl használatakor a rendszer automatikusan elvégzi a következő konfigurációt. A biztonsági ügynök hitelesítésének manuális szerkesztéséhez szerkessze a konfigurációs fájlt.

## <a name="change-authentication-method-after-deployment"></a>Hitelesítési módszer módosítása az üzembe helyezés után

Amikor telepítési parancsfájllal telepít biztonsági ügynököt, a rendszer automatikusan létrehoz egy konfigurációs fájlt.

Az üzembe helyezést követően a hitelesítési módszerek módosításához szükség van a konfigurációs fájl manuális szerkesztésére.

### <a name="c-based-security-agent"></a>C#-alapú biztonsági ügynök

Szerkessze _Authentication.config_ a következő paraméterekkel:

```xml
<Authentication>
  <add key="deviceId" value=""/>
  <add key="gatewayHostname" value=""/>
  <add key="filePath" value=""/>
  <add key="type" value=""/>
  <add key="identity" value=""/>
  <add key="certificateLocationKind" value="" />
</Authentication>
```

### <a name="c-based-security-agent"></a>C-alapú biztonsági ügynök

Szerkessze _LocalConfiguration.jsa_ következő paraméterekkel:

```json
"Authentication" : {
    "Identity" : "",
    "AuthenticationMethod" : "",
    "FilePath" : "",
    "DeviceId" : "",
    "HostName" : ""
}
```

## <a name="see-also"></a>Lásd még

- [Biztonsági ügynökök áttekintése](security-agent-architecture.md)
- [Biztonsági ügynök telepítése](how-to-deploy-agent.md)
- [Hozzáférés a nyers biztonsági adatokhoz](how-to-security-data-access.md)
