---
title: Endpoint Protection-javaslatok az Azure Security Centers szolgáltatásban
description: Az Endpoint Protection-megoldások felderítése és azonosítása kifogástalanként.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 2730a2f5-20bc-4027-a1c2-db9ed0539532
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/29/2019
ms.author: memildin
ms.openlocfilehash: 1ce20deed8b26dc5f5bebf4656dd3f1c370d766f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102561228"
---
# <a name="endpoint-protection-assessment-and-recommendations-in-azure-security-center"></a>Endpoint Protection-Értékelés és javaslatok a Azure Security Center

A Azure Security Center az Endpoint Protection-megoldások [támogatott](security-center-services.md#endpoint-supported) verzióinak egészségügyi értékelését biztosítja. Ez a cikk ismerteti azokat a forgatókönyveket, amelyek Security Center a következő két javaslat létrehozásához vezetnek:

* **Endpoint Protection-megoldások telepítése a virtuális gépre**
* **Az Endpoint Protection állapotával kapcsolatos problémák megoldása a gépeken**

## <a name="windows-defender"></a>Windows Defender

* Security Center a [Get-MpComputerStatus](/powershell/module/defender/get-mpcomputerstatus) futtatásakor javasolja az **Endpoint Protection-megoldások telepítését a virtuális gépen** , és az eredmény **AMServiceEnabled: false**

* Security Center a [Get-MpComputerStatus](/powershell/module/defender/get-mpcomputerstatus) futtatásakor javasolja az **Endpoint Protection-állapottal kapcsolatos problémák megoldását a gépeken** , valamint a következő esetekben:

  * A következő tulajdonságok bármelyike hamis:

    - **AMServiceEnabled**
    - **AntispywareEnabled**
    - **RealTimeProtectionEnabled**
    - **BehaviorMonitorEnabled**
    - **IoavProtectionEnabled**
    - **OnAccessProtectionEnabled**

  * Ha a következő tulajdonságok közül legalább egy 7 vagy több van:

    - **AntispywareSignatureAge**
    - **AntivirusSignatureAge**

## <a name="microsoft-system-center-endpoint-protection"></a>Microsoft System Center Endpoint Protection

* Security Center javasolja az **Endpoint Protection-megoldások telepítését a virtuális gépen a** SCEPMpModule importálásakor **("$env:P rogramfiles\microsoft Security Client\MpProvider\MpProvider.psd1")** , és a **Get-MProtComputerStatus** eredmények futtatását **AMServiceEnabled = false értékre**.

* Security Center a **Get-MprotComputerStatus** futtatásakor javasolja az **Endpoint Protection-állapottal kapcsolatos problémák megoldását a gépeken** , valamint a következő esetekben:

  * A következő tulajdonságok közül legalább az egyik hamis:

    - **AMServiceEnabled**
    - **AntispywareEnabled**
    - **RealTimeProtectionEnabled**
    - **BehaviorMonitorEnabled**
    - **IoavProtectionEnabled**
    - **OnAccessProtectionEnabled**

  * Ha a következő aláírások egy vagy mindkét frissítése nagyobb vagy egyenlő, mint 7:

    * **AntispywareSignatureAge**
    * **AntivirusSignatureAge**

## <a name="trend-micro"></a>Trend Micro

* Security Center javasolja az **Endpoint Protection-megoldások telepítését a virtuális gépen** , ha a következő ellenőrzések bármelyike nem teljesül:
    - **HKLM: \ SOFTWARE\TrendMicro\Deep biztonsági ügynök** létezik
    - **HKLM: \ SOFTWARE\TrendMicro\Deep biztonsági Agent\InstallationFolder** létezik
    - A **dsa_query. cmd** fájl a telepítési mappában található.
    - **Dsa_query. cmd** eredményének futtatása a **Component. am. Mode: on-Trend Micro Deep Security Agent észlelve**

## <a name="symantec-endpoint-protection"></a>Symantec Endpoint Protection
Security Center javasolja az **Endpoint Protection-megoldások telepítését a virtuális gépen** , ha a következő ellenőrzések bármelyike nem teljesül:

- **HKLM: \ Software\Symantec\Symantec Endpoint Protection\CurrentVersion\PRODUCTNAME = "Symantec Endpoint Protection"**
- **HKLM: \ Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\ASRunningStatus = 1**

Vagy

- **HKLM: \ Software\Wow6432Node\Symantec\Symantec Endpoint Protection\CurrentVersion\PRODUCTNAME = "Symantec Endpoint Protection"**
- **HKLM: \ Software\Wow6432Node\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\ASRunningStatus = 1**

Security Center azt javasolja, hogy **"az Endpoint Protection-állapottal kapcsolatos problémák megoldása a gépeken"** , ha a következő ellenőrzések bármelyike nem teljesül:

- Keresse meg a Symantec verzióját >= 12: beállításjegyzék helye: **HKLM: \ Software\Symantec\Symantec Endpoint Protection\CurrentVersion "-value" PRODUCTVERSION "**
- Real-Time védelmi állapotának keresése: **HKLM: \ Software\Wow6432Node\Symantec\Symantec Endpoint Protection\AV\Storages\Filesystem\RealTimeScan\OnOff = = 1**
- Aláírás frissítési állapotának megtekintése: **HKLM\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\LatestVirusDefsDate <= 7 nap**
- Teljes vizsgálat állapotának ellenőrzése: **HKLM: \ Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\LastSuccessfulScanDateTime <= 7 nap**
- Az aláírás verziószámának elérési útja a Symantec 12 verzióhoz: **Registry paths + "CurrentVersion\SharedDefs"-value "SRTSP"** 
- A Symantec 14 aláírási verziójának elérési útja: **beállításjegyzék elérési útjai + "CurrentVersion\SharedDefs\SDSDefs"-value "SRTSP"**

Beállításjegyzékbeli elérési utak:
- **"HKLM: \ Software\Symantec\Symantec Endpoint Protection" + $Path;**
- **"HKLM: \ Software\Wow6432Node\Symantec\Symantec Endpoint Protection" + $Path**

## <a name="mcafee-endpoint-protection-for-windows"></a>A Windows rendszerhez készült McAfee Endpoint Protection

Security Center javasolja az **Endpoint Protection-megoldások telepítését a virtuális gépen** , ha a következő ellenőrzések bármelyike nem teljesül:

- **HKLM: \ SOFTWARE\McAfee\Endpoint\AV\ProductVersion** létezik
- **HKLM: \ SOFTWARE\McAfee\AVSolution\MCSHIELDGLOBAL\GLOBAL\enableoas = 1**

Security Center azt javasolja, hogy **"az Endpoint Protection-állapottal kapcsolatos problémák megoldása a gépeken"** , ha a következő ellenőrzések bármelyike nem teljesül:

- McAfee-verzió: **HKLM: \ SOFTWARE\McAfee\Endpoint\AV\ProductVersion >= 10**
- Aláírás verziójának keresése: **HKLM: \ Software\McAfee\AVSolution\DS\DS-Value "dwContentMajorVersion"**
- Aláírási dátum keresése: **HKLM: \ Software\McAfee\AVSolution\DS\DS-Value "szContentCreationDate" >= 7 nap**
- Keresési dátum keresése: **HKLM: \ Software\McAfee\Endpoint\AV\ODS-Value "LastFullScanOdsRunTime" >= 7 nap**

## <a name="mcafee-endpoint-security-for-linux-threat-prevention"></a>A McAfee-végpont biztonsága a Linux-veszélyforrások megelőzésére 

Security Center javasolja az **Endpoint Protection-megoldások telepítését a virtuális gépen** , ha a következő ellenőrzések bármelyike nem teljesül:

- A fájl **/opt/ISEC/ENS/threatprevention/bin/isecav** létezik
- **"/opt/ISEC/ENS/threatprevention/bin/isecav--version"** kimenet: **McAfee Name = McAfee Endpoint Security for Linux Threat Prevention and McAfee Version >= 10**

Security Center azt javasolja, hogy **"az Endpoint Protection-állapottal kapcsolatos problémák megoldása a gépeken"** , ha a következő ellenőrzések bármelyike nem teljesül:

- **"/opt/ISEC/ENS/threatprevention/bin/isecav--listtask"** – a **gyors vizsgálat, a teljes ellenőrzés** és mindkét vizsgálat eredménye <= 7 nap
- a **"/opt/ISEC/ENS/threatprevention/bin/isecav--listtask"** függvény a **dat és a motor frissítési idejét** , valamint mindkettőt adja vissza <= 7 nap
- **"/opt/ISEC/ENS/threatprevention/bin/isecav--getoasconfig--Summary"** **a hozzáférési vizsgálat** állapotának visszaadása

## <a name="sophos-antivirus-for-linux"></a>A Linux rendszerhez készült Sophos Antivirus 

Security Center javasolja az **Endpoint Protection-megoldások telepítését a virtuális gépen** , ha a következő ellenőrzések bármelyike nem teljesül:
- A fájl **/opt/Sophos-AV/bin/savdstatus** kilép, vagy megkeresi a **"readlink $ (amely savscan)"** testreszabott helyet
- a **"/opt/Sophos-AV/bin/savdstatus--version"** a Sophos name = **Sophos Anti-Virus és a Sophos >= 9 verzióját** adja vissza.

Security Center azt javasolja, hogy **"az Endpoint Protection-állapottal kapcsolatos problémák megoldása a gépeken"** , ha a következő ellenőrzések bármelyike nem teljesül:
- **"/opt/Sophos-AV/bin/savlog--MaxAge = 7 | GREP-i "ütemezett vizsgálat. \* befejezve: "| farok-1 "**, egy értéket ad vissza.
- **"/opt/Sophos-AV/bin/savlog--MaxAge = 7 | a GREP "vizsgálat elkészült"** | farok-1 ", egy értéket ad vissza.
- a **"/opt/Sophos-AV/bin/savdstatus--LastUpdate"** függvény a LastUpdate értéket adja vissza, amelynek <= 7 nap kell lennie 
- a **"/opt/Sophos-AV/bin/savdstatus-v"** egyenlő **"a hozzáférés-ellenőrzés fut"** értékkel. 
- a **"/opt/Sophos-AV/bin/savconfig Get LiveProtection"** érték engedélyezve

## <a name="troubleshoot-and-support"></a>Hibakeresés és támogatás

### <a name="troubleshoot"></a>Hibaelhárítás

A Microsoft antimalware-bővítmény naplói a következő címen érhetők el: **%SYSTEMDRIVE%\WindowsAzure\Logs\Plugins\Microsoft.Azure.Security.IaaSAntimalware (vagy PaaSAntimalware) \1.5.5.x (Version #) \CommandExecution.log**

### <a name="support"></a>Támogatás

További segítségért forduljon az Azure-szakértőkhöz az [MSDN Azure-ban és stack overflow fórumokon](https://azure.microsoft.com/support/forums/). Vagy egy Azure-támogatási incidenst. Nyissa meg az [Azure támogatási webhelyét](https://azure.microsoft.com/support/options/) , és válassza a támogatás kérése lehetőséget. További információ az Azure-támogatás használatáról: [Microsoft Azure támogatással kapcsolatos gyakori kérdések](https://azure.microsoft.com/support/faq/).