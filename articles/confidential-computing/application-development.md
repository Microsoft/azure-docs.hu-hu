---
title: Azure Confidential Computing fejlesztői eszközök
description: Eszközök és kódtárak használata bizalmas számítástechnikai alkalmazások fejlesztéséhez
services: virtual-machines
author: JBCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: JenCook
ms.openlocfilehash: 571c1a4ce545976db09f46a07d963d5344c02c29
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791012"
---
# <a name="application-development-on-intel-sgx"></a>Alkalmazásfejlesztés az Intel SGX-ben 


A bizalmas számítási infrastruktúrához speciális eszközökre és szoftverekre van szükség. Ez az oldal kifejezetten az Intel SGX-ben futó Azure confidential computing virtuális gépek alkalmazásfejlesztésével kapcsolatos fogalmakat ismerteti. Az oldal elolvasása előtt olvassa el az Intel SGX virtuális gépek és [enklávek bevezetését.](confidential-computing-enclaves.md) 

Az enklávak és az elkülönített környezetek nyújtotta előny kihasználásához olyan eszközöket kell használnia, amelyek támogatják a bizalmas számítástechnikát. Különböző eszközök támogatják az enklávéalkalmazások fejlesztését. Használhatja például a következő nyílt forráskódú keretrendszereket: 

- [Az Open Enclave szoftverfejlesztői készlet (OE SDK)](#oe-sdk)
- [Az EGo szoftverfejlesztői készlet](#ego)
- [A Confidential Consortium Framework (CCF)](#ccf)

## <a name="overview"></a>Áttekintés

Az enklávokkal készült alkalmazások két módon vannak particionálva:

1. Egy "nem megbízható" összetevő (a gazdagép)
1. Egy "megbízható" összetevő (az enklávé)


![Alkalmazás-fejlesztés](media/application-development/oe-sdk.png)


**A gazdagépen** fut az enklávéalkalmazás, amely nem megbízható környezet. A gazdagépen üzembe helyezett enklávékódot a gazdagép nem tudja elérni. 

**Az enklávéban** fut az alkalmazáskód és a gyorsítótárazott adatok/memória. A titkos kulcsok és a bizalmas adatok védelme érdekében az enklávákban biztonságos számításokra van sor. 


Az alkalmazás tervezése során fontos azonosítani és meghatározni, hogy az alkalmazás melyik részének kell futnia az enklávákban. A megbízható összetevőbe tenni választott kód el van különítve az alkalmazás többi részével. Az enklávé inicializálása és a kód memóriába való betöltése után a kód nem olvasható és nem módosítható a nem megbízható összetevőkből. 

## <a name="open-enclave-software-development-kit-oe-sdk"></a>Open Enclave Software Development Kit (OE SDK) <a id="oe-sdk"></a>

Ha enklávéban futó kódot szeretne írni, használja a szolgáltató által támogatott kódtárat vagy keretrendszert. Az [Open Enclave SDK](https://github.com/openenclave/openenclave) (OE SDK) egy nyílt forráskódú SDK, amely absztrakciót tesz lehetővé a különböző bizalmas számításokat engedélyező hardverek felett. 

Az OE SDK egyetlen absztrakciós rétegként készült bármely CSP bármely hardverén. Az OE SDK az Azure Confidential Computing virtuális gépeken is használható alkalmazások létrehozására és futtatására enklávekon.

## <a name="ego-software-development-kit"></a>EGo szoftverfejlesztői készlet <a id="ego"></a>

[Az EGo](https://ego.dev/) egy nyílt forráskódú SDK, amely lehetővé teszi a Go programozási nyelven írt alkalmazások futtatását az enklávékban. Az EGo az OE SDK-ra épül, és tartalmaz egy enklávén keresztüli Go-kódtárat az igazoláshoz és a lezáráshoz. Számos meglévő Go-alkalmazás módosítás nélkül fut az EGo-ban.  

## <a name="confidential-consortium-framework-ccf"></a>Confidential Consortium Framework (CCF) <a id="ccf"></a>

A [CCF](https://github.com/Microsoft/CCF) csomópontok elosztott hálózata, amely mindegyik saját enklávokat futtat. A megbízható csomóponthálózat lehetővé teszi egy elosztott tranzakciókönyv futtatását. A tranzakciókönyv biztonságos, megbízható összetevőket biztosít a protokoll számára. 

![CCF-csomópontok](media/application-development/ccf.png)

Ez a nyílt forráskódú keretrendszer lehetővé teszi a teljes, finomhangolt titkosság és a konzorciumirányítást a blokkláncok számára. A TEE-eket használó csomópontok biztonságos megegyezést és tranzakciófeldolgozást tudnak biztosítani.


## <a name="next-steps"></a>Következő lépések 
- [Bizalmas számítástechnika üzembe helyezése DCsv2-Series virtuális gépen](quick-create-portal.md)
- [Töltse le és telepítse az OE SDK-t, és kezdje el az alkalmazások fejlesztését](https://github.com/openenclave/openenclave)
