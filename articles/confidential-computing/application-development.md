---
title: Azure bizalmas számítástechnikai fejlesztési eszközök
description: Eszközök és kódtárak használata a bizalmas számítástechnikai alkalmazások fejlesztéséhez
services: virtual-machines
author: JBCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: JenCook
ms.openlocfilehash: 0ba6ee92111da66a2118ba4c490b94e5bc9449e0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102551385"
---
# <a name="application-development-on-intel-sgx"></a>Alkalmazásfejlesztés az Intel SGX ENKLÁVÉHOZ 


A bizalmas számítástechnikai infrastruktúrához speciális eszközök és szoftverek szükségesek. Ez az oldal kifejezetten ismerteti az Azure-beli, az Intel SGX ENKLÁVÉHOZ-on futó Azure bizalmas számítástechnikai virtuális gépek alkalmazás-fejlesztésével kapcsolatos fogalmakat. Az oldal olvasása előtt [olvassa el az Intel SGX enklávéhoz Virtual Machines and enklávés szolgáltatás bevezetését](confidential-computing-enclaves.md). 

Az enklávék és az elszigetelt környezetek hatékonyságának kihasználásához a bizalmas számítástechnikai eszközöket támogató eszközöket kell használnia. Az enklávé-alkalmazások fejlesztését számos eszköz segíti. Használhatja például a következő nyílt forráskódú keretrendszereket: 

- [Az Open enklávé szoftverfejlesztői készlete (OE SDK)](#oe-sdk)
- [A bizalmas konzorciumi keretrendszer (CCF)](#ccf)

## <a name="overview"></a>Áttekintés

Az enklávékkal létrehozott alkalmazások kétféleképpen vannak particionálva:

1. "Nem megbízható" összetevő (a gazdagép)
1. "Megbízható" összetevő (az enklávé)


![Alkalmazás-fejlesztés](media/application-development/oe-sdk.png)


**A gazdagép** az a hely, ahol az enklávé alkalmazása fut, és nem megbízható környezet. A gazdagépen központilag telepített enklávé-kódot nem lehet elérni. 

**Az enklávé** , ahol az alkalmazás kódja és a gyorsítótárazott adatmennyisége/memóriája fut. Az enklávékban biztonságos számításokat kell megtenni a titkok és bizalmas adatok védelme érdekében. 


Az alkalmazás kialakítása során fontos azonosítani és meghatározni, hogy az alkalmazás mely részeit kell futtatni az enklávékban. A megbízható összetevőbe helyezett kód el van különítve az alkalmazás többi részétől. Miután az enklávé inicializálása megtörtént, és a kód betöltődik a memóriába, a kód nem olvasható vagy nem módosítható a nem megbízható összetevőkből. 

## <a name="open-enclave-software-development-kit-oe-sdk"></a>Open enklávé szoftverfejlesztői készlet (OE SDK) <a id="oe-sdk"></a>

Ha egy enklávéban futó kódot szeretne írni, használja a szolgáltató által támogatott függvénytárat vagy keretrendszert. Az [Open ENKLÁVÉ SDK](https://github.com/openenclave/openenclave) (OE SDK) egy nyílt forráskódú SDK, amely lehetővé teszi a különböző bizalmas számítástechnikai hardverek absztrakt használatát. 

Az OE SDK egyetlen absztrakciós rétegként van felépítve bármely CSP hardverén. Az OE SDK az Azure-beli bizalmas számítástechnikai virtuális gépeken is használható a enklávékban található alkalmazások létrehozásához és futtatásához.

## <a name="confidential-consortium-framework-ccf"></a>Bizalmas konzorciumi keretrendszer (CCF) <a id="ccf"></a>

A [CCF](https://github.com/Microsoft/CCF) a csomópontok elosztott hálózata, amelyek mindegyike saját enklávékat futtat. A megbízható csomópont-hálózat lehetővé teszi egy elosztott Főkönyv futtatását. A Főkönyv biztonságos, megbízható összetevőket biztosít a protokoll használatához. 

![CCF-csomópontok](media/application-development/ccf.png)

Ez a nyílt forráskódú keretrendszer lehetővé teszi a magas szintű és részletes titoktartást, valamint a blockchain konzorciumi irányítását. A pólók használatával minden egyes csomópont biztonságos konszenzust és tranzakció-feldolgozást tesz lehetővé.


## <a name="next-steps"></a>Következő lépések 
- [Bizalmas számítástechnikai DCsv2-Series virtuális gép üzembe helyezése](quick-create-portal.md)
- [Töltse le és telepítse az OE SDK-t, és indítsa el az alkalmazások fejlesztését](https://github.com/openenclave/openenclave)