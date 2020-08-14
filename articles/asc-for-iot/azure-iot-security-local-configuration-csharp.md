---
title: Biztonsági ügynök helyi konfigurációja (C#)
description: További információ a IoT biztonsági szolgáltatás, a biztonsági ügynök helyi konfigurációs fájlja a C#-hoz Azure Security Center.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 2cf6a49b-5d35-491f-abc3-63ec24eb4bc2
ms.subservice: asc-for-iot
ms.devlang: na
ms.custom: devx-track-csharp
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/26/2019
ms.author: mlottner
ms.openlocfilehash: eab7764ede300ca355ec6193312279c5726f8a01
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/14/2020
ms.locfileid: "88212399"
---
# <a name="understanding-the-local-configuration-file-c-agent"></a>A helyi konfigurációs fájl (C# ügynök) ismertetése

A IoT biztonsági ügynök Azure Security Center konfigurációkat használ egy helyi konfigurációs fájlból.

A biztonsági ügynök az ügynök indításakor egyszer beolvassa a konfigurációs fájlt. A helyi konfigurációs fájlban található konfigurációk a hitelesítési konfigurációt és az egyéb ügynökkel kapcsolatos konfigurációkat is tartalmazzák.

A C# biztonsági ügynök több konfigurációs fájlt használ:

- **General.config** -ügynökhöz kapcsolódó konfigurációk.
- **Authentication.config** – hitelesítéssel kapcsolatos konfiguráció (beleértve a hitelesítés részleteit is).
- **SecurityIotInterface.config** IoT kapcsolódó konfigurációk.

A konfigurációs fájlok tartalmazzák az alapértelmezett konfigurációt. A hitelesítési konfiguráció az ügynök telepítése során töltődik fel, és a konfigurációs fájl módosításai az ügynök újraindításakor jönnek létre.

## <a name="configuration-file-location"></a>Konfigurációs fájl helye

Linux esetén:

- Az operációs rendszer konfigurációs fájljai a ben találhatók `/var/ASCIoTAgent` .

Windows esetén:

- Az operációs rendszer konfigurációs fájljai a biztonsági ügynök könyvtárán belül találhatók.

### <a name="generalconfig-configurations"></a>General.config konfigurációk

| Konfiguráció neve | Lehetséges értékek | Részletek |
|:-----------|:---------------|:--------|
| ügynökazonosító | GUID | Ügynök egyedi azonosítója |
| readRemoteConfigurationTimeout | időtartam | A távoli konfiguráció IoT Hubból való beolvasásának időszaka. Ha az ügynök a megadott időn belül nem tudja lekérni a konfigurációt, a művelet időtúllépést eredményez.|
| schedulerInterval | időtartam | Belső ütemező időköze |
| producerInterval | időtartam | Esemény-előállító munkavégző időköze. |
| consumerInterval | időtartam | Esemény fogyasztói munkavégző időköze. |
| highPriorityQueueSizePercentage | 0 < szám < 1 | A magas prioritású üzenetekhez dedikált teljes gyorsítótár része. |
| Naplózási szint | "Off", "végzetes", "Error", "Warning", "Information", "debug"  | A naplózási üzenetek a súlyossági szinttel megegyező, a hibakeresési konzolon (syslog in Linux) vannak naplózva. |
| fileLogLevel |  "Off", "végzetes", "Error", "Warning", "Information", "debug"| A súlyosságot meghaladó naplófájlok naplózása a következő fájlba történik: (syslog in Linux). |
| diagnosticVerbosityLevel | "None", "some", "all", | A diagnosztikai események részletességi szintje. Nincs – a rendszer a diagnosztikai eseményeket nem továbbítja, csak a nagy fontossággal bíró diagnosztikai eseményeket, a rendszer az összes naplót diagnosztikai eseményként is elküldje. |
| logFilePath | Fájl elérési útja | Ha a fileLogLevel > ki, a rendszer a naplókat erre a fájlba írja. |
| defaultEventPriority | "Magas", "alacsony", "off" | Alapértelmezett esemény prioritása. |

### <a name="generalconfig-example"></a>General.config példa

```xml
<?xml version="1.0" encoding="utf-8"?>
<General>
  <add key="agentId" value="da00006c-dae9-4273-9abc-bcb7b7b4a987" />
  <add key="readRemoteConfigurationTimeout" value="00:00:30" />
  <add key="schedulerInterval" value="00:00:01" />
  <add key="producerInterval" value="00:02:00" />
  <add key="consumerInterval" value="00:02:00" />
  <add key="highPriorityQueueSizePercentage" value="0.5" />
  <add key="logLevel" value="Information" />
  <add key="fileLogLevel" value="Off"/>
  <add key="diagnosticVerbosityLevel" value="Some" />
  <add key="logFilePath" value="IotAgentLog.log" />
  <add key="defaultEventPriority" value="Low"/>
</General>
```

### <a name="authenticationconfig"></a>Authentication.config

| Konfiguráció neve | Lehetséges értékek | Részletek |
|:-----------|:---------------|:--------|
| moduleName | sztring | A biztonsági modul identitásának neve. Ennek a névnek meg kell egyeznie a modul identitásának nevével az eszközön. |
| deviceId | sztring | Az eszköz azonosítója (az Azure IoT Hubban regisztrálva). || schedulerInterval | TimeSpan karakterlánc | Belső ütemező időköze |
| gatewayHostname | sztring | Az Azure IOT hub állomásneve. Általában <My-hub>. azure-devices.net |
| filePath | karakterlánc – fájl elérési útja | A hitelesítési titkot tartalmazó fájl elérési útja.|
| típus | "SymmetricKey", "SelfSignedCertificate" | A hitelesítő felhasználói titok. Válassza a *SymmetricKey* lehetőséget, ha a felhasználói titok szimmetrikus kulcs, válassza az *önaláírt tanúsítvány* lehetőséget, ha a titok egy önaláírt tanúsítvány. |
| identity | "DPS", "modul", "eszköz" | Hitelesítési identitás – DPS if hitelesítés a DPS-n keresztül történik, modul, ha a hitelesítés modul hitelesítő adataival történik, vagy ha hitelesítés történik az eszköz hitelesítő adataival.
| certificateLocationKind |  "LocalFile", "Store" | LocalFile, ha a tanúsítvány egy fájlban van tárolva, tárolja, hogy a tanúsítvány tanúsítványtárolóban található-e. |
| idScope | sztring | A DPS azonosító hatóköre |
| regisztrációban | sztring  | DPS-eszköz regisztrációs azonosítója. |
|

### <a name="authenticationconfig-example"></a>Authentication.config példa

```xml
<?xml version="1.0" encoding="utf-8"?>
<Authentication>
  <add key="moduleName" value="azureiotsecurity"/>
  <add key="deviceId" value="d1"/>
  <add key="gatewayHostname" value=""/>
  <add key="filePath" value="c:\p-dps-d1.pfx"/>
  <add key="type" value="SelfSignedCertificate" />                     <!-- SymmetricKey, SelfSignedCertificate-->
  <add key="identity" value="DPS" />                 <!-- Device, Module, DPS -->
  <add key="certificateLocationKind" value="LocalFile" />  <!-- LocalFile, Store -->
  <add key="idScope" value="0ne0005335B"/>
  <add key="registrationId" value="d1"/>
</Authentication>
```

### <a name="securityiotinterfaceconfig"></a>SecurityIotInterface.config

| Konfiguráció neve | Lehetséges értékek | Részletek |
|:-----------|:---------------|:--------|
| transportType | "Ampq" "Mqtt" | IoT Hub átviteli típus. |
|

### <a name="securityiotinterfaceconfig-example"></a>SecurityIotInterface.config példa

```xml
<ExternalInterface>
  <add key="facadeType"  value="Microsoft.Azure.Security.IoT.Agent.Common.SecurityIoTHubInterface, Security.Common" />
  <add key="transportType" value="Amqp"/>
</ExternalInterface>
```

## <a name="next-steps"></a>Következő lépések

- A IoT-szolgáltatás [áttekintésének](overview.md) Azure Security Center olvasása
- További információ a IoT- [architektúra](architecture.md) Azure Security Center
- A IoT [szolgáltatás](quickstart-onboard-iot-hub.md) Azure Security Centerának engedélyezése
- A IoT szolgáltatással kapcsolatos [Gyakori kérdések](resources-frequently-asked-questions.md) Azure Security Center beolvasása
- Ismerje meg, hogyan érheti el a [nyers biztonsági információkat](how-to-security-data-access.md)
- [Javaslatok](concept-recommendations.md) ismertetése
- Biztonsági [riasztások](concept-security-alerts.md) ismertetése
