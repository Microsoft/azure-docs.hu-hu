---
title: Az Azure-ban támogatott mainframe-munkaterhelések | Microsoft Docs
description: A Microsoft partnereitől származó mainframe-emulátorral és más szolgáltatásokkal a nagyvállalati munkaterheléseket, például az IBM Z-alapú rendszereket Microsoft Azure használatával helyezheti át.
services: virtual-machines
ms.service: virtual-machines
ms.subservice: mainframe-rehosting
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
ms.author: larryme
ms.date: 05/09/2020
ms.topic: article
ms.openlocfilehash: 467cae97f9b4efe4782ac802cccd84271d475ea7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104955431"
---
# <a name="mainframe-workloads-supported-on-azure"></a>Az Azure-ban támogatott nagyszámítógépi munkaterhelések

Az itt felsorolt megoldások segítségével áttelepítheti a mainframe-munkaterhelést Microsoft Azurera. Bizonyos számítási feladatok migrálása viszonylag egyszerű. A régi rendszerszoftvertől függő egyéb munkaterhelések is áthelyezhetők. 

Ha további segítségre van a nagyszámítógépi emuláció és a szolgáltatások terén, tekintse meg az [Azure mainframe Migration centert](https://azure.microsoft.com/migration/mainframe/).

## <a name="migrate-mainframe-closer-to-azure"></a>Nagyszámítógépek migrálása az Azure-ba

- [Szabó Visual RPG for .net](https://asna.com/us/products/visual-rpg) -fordító a .net-keretrendszerhez a Visual Studio beépülő modul használatával.
- [ASYSCO amt Cobol](https://www.asysco.com/cobol/) fejlesztési környezet (UNISYS, IBM mainframe-EK és más Cobol-dialektusok, mint például a Micro Focus Cobol).
- A [ASYSCO amt](https://www.asysco.com/amt-go/) felhőalapú üzembe helyezési architektúrát biztosít a nagy teljesítményű számítási feladatokhoz.
- [ASYSCO amt átalakító](https://www.asysco.com/amt-transform/) az adatátalakításhoz, a kód, a parancsfájlkezelés, a biztonság, a felületek és az egyéb mainframe-összetevők konvertálásához.
- A [Fujitsu NetCOBOL](https://www.fujitsu.com/global/products/software/developer-tool/netcobol/) fejlesztési és integrációs eszközei.
- A [Micro Focus Visual Cobol](https://www.microfocus.com/products/visual-cobol/) fejlesztési és integrációs eszközei.
- [Micro Focus pl/i](https://www.microfocus.com/campaign/download/pli-modernization/) örökölt fordítóprogram a .net platformhoz, amely támogatja a nagyszámítógépeken a pl/i szintaxist, az adattípusokat és a viselkedést.
- [Micro Focus Enterprise Server](https://www.microfocus.com/products/enterprise-suite/enterprise-server/) mainframe integrációs platform.
- [Modern rendszerek CTU (Cobol-univerzális)](https://modernsystems.com/automatic-cobol-to-java-conversion/) fejlesztési és integrációs eszközök.
- [Az NTT-Adatvállalat Cobol](https://us.nttdata.com/en/digital/application-development-and-modernization) -fejlesztési és-integrációs eszközei.
- [NTT Open pl/i](https://us.nttdata.com/en/digital/application-development-and-modernization) örökölt fordítóprogram a .net platformhoz, amely támogatja a nagyszámítógépeken a pl/i szintaxist, az adattípusokat és a viselkedést.
- [RAINCODE Cobol-fordító](https://www.raincode.com/products/cobol/) fejlesztési és integrációs eszközei.
- A .NET platformhoz készült [RAINCODE pl/i fordítóprogram](https://www.raincode.com/products/pli/) támogatja a nagyszámítógépi (pl/i) szintaxist, az adattípusokat és a viselkedést.
- [RAINCODE ASM370-fordító](https://www.raincode.com/technical-landscape/asm370/) a nagyszámítógépi szerelvény 370 és HLASM szintaxisa.

## <a name="deploy-an-emulation-environment-for-online-and-batch-processing"></a>Emulációs környezet üzembe helyezése online és kötegelt feldolgozáshoz

- A [ASYSCO amt go](https://www.asysco.com/amt-go/) üzembe helyezési architektúrája támogatja a CICS, az IMS, a tip, a HVTIP és az egyéb gyakori mainframe-környezeteket.
- [Micro Focus Enterprise Server](https://www.microfocus.com/products/enterprise-suite/enterprise-server/) mainframe integrációs platform.
- A fejlesztői környezet natív tranzakció-feldolgozási környezetét [átüzemeltető NTT-alapú adatmainframe](https://us.nttdata.com/en/-/media/assets/white-paper/apps-mainframe-re-hosting-development-environment-whitepaper.pdf) .
- [Az NTT adatköteg-feldolgozási környezet](https://us.nttdata.com/en/-/media/assets/white-paper/apps-mainframe-re-hosting-development-environment-whitepaper.pdf) (bpe szolgáltatáskapcsoló), beleértve a JCL tranzakciós képességeket.
- [RAINCODE CICS](https://www.raincode.com/technical-landscape/cics/) Emulator .net-és Azure-platformokhoz.
- [RAINCODE JCL](https://www.raincode.com/products/jcl/) beépülő MODULLAL kompatibilis JCL-értelmező.

## <a name="code-conversion"></a>Kód átalakítása

- A [Asysco](https://www.asysco.com/azure-cloud/) rendszerátalakítási technológiája, amely a forráskódot, az adatköteget, az ütemezést, a TP-figyelőket, a csatolókat, a biztonságot, a felügyeletet
- [ASYSCO amt-szolgáltatások](https://www.asysco.com/migration-services/) teljes körű szolgáltatásai áttelepítési projektekhez, beleértve a leltározást és elemzést, a tervezési képzést, a jelmezes próbákat, a Go-Live-t és az áttelepítés utáni támogatást.
- [Blu Age](https://www.bluage.com/) -eszközök a régi üzleti alkalmazások és adatbázisok digitalizálásához.
- [Ereklye számítástechnikai](https://www.heirloomcomputing.com/tag/convert-cobol-to-java/) szolgáltatások a Cobol, a CICS és a VSAM konvertálása javára.
- A [LzLabs nagyszámítógép által](https://www.lzlabs.com/) felügyelt szoftveres tárolót biztosít a nagyszámítógépi alkalmazások Linux rendszerű számítógépekre vagy privát, nyilvános és hibrid felhőalapú környezetbe való áttelepítéséhez.

## <a name="modernization-services"></a>Modernizációs szolgáltatások

Microsoft-partnerek globális rendszerintegrátorokkal (GSIs), akik segíthetnek a nagyméretű szervezetek számára a megoldások tervezésében, létrehozásában és kezelésében. 

- [Azure mainframe áttelepítési központ](https://azure.microsoft.com/migration/mainframe/)
