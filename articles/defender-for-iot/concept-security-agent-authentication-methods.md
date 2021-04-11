---
title: Biztonsági ügynök hitelesítési módszerei
description: Ismerje meg a Defender for IoT szolgáltatás használatakor elérhető különböző hitelesítési módszereket.
ms.topic: conceptual
ms.date: 01/24/2021
ms.openlocfilehash: bd4d89d8664dae2421ff7f268b3710b5a906427b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104784611"
---
# <a name="security-agent-authentication-methods"></a>Biztonsági ügynök hitelesítési módszerei

Ez a cikk ismerteti a különböző hitelesítési módszereket, amelyekkel a AzureIoTSecurity-ügynökkel végezheti el a hitelesítést a IoT Hub.

A Defender által a IoT Hub IoT bekészített összes eszközhöz a Defender-IoT-Micro-Agent szükséges. Az eszköz hitelesítéséhez a Defender for IoT két módszer egyikét használhatja. Válassza ki a meglévő IoT-megoldáshoz legjobban illő módszert.

- SecurityModule beállítás
- Eszköz beállítás

## <a name="authentication-methods"></a>Hitelesítési módszerek

Az IoT AzureIoTSecurity-ügynök két módszere a hitelesítés elvégzéséhez:

- **Defender-IoT-Micro-Agent** hitelesítési mód<br>
Az ügynök hitelesítése a Defender-IoT-Micro-Agent identitástól függetlenül történik az eszköz identitása alapján.
Akkor használja ezt a hitelesítési típust, ha azt szeretné, hogy a biztonsági ügynök dedikált hitelesítési módszert használjon a Defender-IoT-Micro-Agent használatával (csak szimmetrikus kulcs esetén).

- **Eszköz** hitelesítési módja<br>
Ebben a metódusban a biztonsági ügynök először hitelesíti magát az eszköz identitásával. A kezdeti hitelesítés után a Defender for IoT-ügynök **Rest** -hívást hajt végre a IoT hub az eszköz hitelesítési adataival REST API használatával. A Defender for IoT Agent ezt követően kéri a Defender-IoT-Micro-Agent hitelesítési módszert és a IoT Hub adatait. Az utolsó lépésben a Defender for IoT ügynök hitelesítést végez a Defender for IoT modulban.

Akkor használja ezt a hitelesítési típust, ha azt szeretné, hogy a biztonsági ügynök felhasználja a meglévő eszköz-hitelesítési módszert (önaláírt tanúsítvány vagy szimmetrikus kulcs).

A konfigurálásának megismeréséhez tekintse meg a [biztonsági ügynök telepítési paramétereit](#security-agent-installation-parameters) .

## <a name="authentication-methods-known-limitations"></a>Ismert hitelesítési módszerek

- A **SecurityModule** hitelesítési mód csak a szimmetrikus kulcsos hitelesítést támogatja.
- Az **eszköz** -hitelesítési mód nem támogatja CA-Signed a tanúsítványt.

## <a name="security-agent-installation-parameters"></a>Biztonsági ügynök telepítési paraméterei

[Biztonsági ügynök telepítésekor a](how-to-deploy-agent.md)hitelesítési adatokat argumentumként kell megadni.
Ezek az argumentumok a következő táblázatban vannak dokumentálva.

|Linux-paraméter neve | Windows-paraméter neve | Gyorsírás paraméter |Leírás|Beállítások|
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
