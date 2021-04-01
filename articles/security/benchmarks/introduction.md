---
title: Az Azure biztonsági teljesítménytesztjének ismertetése
description: Biztonsági teljesítményteszt bemutatása
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 32aa64a9343a8e3c62af4322b3320b28ce805064
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97369191"
---
# <a name="azure-security-benchmark-introduction"></a>Az Azure Security teljesítményteszt bemutatása

Az Azure-ban naponta megjelent új szolgáltatások és szolgáltatások, a fejlesztők pedig gyorsan közzéteszik a szolgáltatásokra épülő új felhőalapú alkalmazásokat, a támadók pedig mindig új módszereket keresnek a hibásan konfigurált erőforrások kihasználására. A felhő gyorsan mozog, a fejlesztők gyorsan elmozdulnak, a támadók pedig mindig mozgásban vannak. Hogyan lehet lépést tartani a Felhőbeli üzemelő példányok biztonságának biztosításával? Miben különböznek a különböző felhőalapú rendszerek biztonsági eljárásai a helyszíni rendszerekből? Hogyan figyelheti a több független fejlesztői csapat közötti konzisztenciát?

A Microsoft úgy találta, hogy a *biztonsági referenciaértékek* használatával gyorsan biztonságossá teheti a Felhőbeli központi telepítéseket. A felhőalapú szolgáltatótól származó teljesítményteszt-javaslatok kiindulási pontot biztosítanak a környezet adott biztonsági konfigurációs beállításainak kiválasztásához, és lehetővé teszik a szervezet számára a kockázatok gyors csökkentését.

Az Azure biztonsági teljesítményteszt olyan nagy hatású biztonsági javaslatokat tartalmaz, amelyek segítségével biztonságossá teheti az Azure-ban használt szolgáltatásokat:

- **Biztonsági vezérlők**: ezek a javaslatok általánosan érvényesek az Azure-bérlő és az Azure-szolgáltatások között. Mindegyik javaslat azonosítja azon érdekelt felek listáját, akik általában részt vesznek a teljesítményteszt megtervezésében, jóváhagyásában vagy megvalósításában. 
- **Szolgáltatás** alapkonfigurációi: ezek a vezérlők az egyes Azure-szolgáltatásokra vonatkoznak, hogy javaslatokat szolgáltassanak a szolgáltatás biztonsági beállításairól.

## <a name="implement-the-azure-security-benchmark"></a>Az Azure biztonsági teljesítményteszt implementálása
- **Tervezze** meg az Azure biztonsági teljesítményteszt megvalósítását a vállalati vezérlők és a szolgáltatás-specifikus alapkonfigurációk [dokumentációjának](overview.md) áttekintésével, hogy megtervezze a vezérlési keretrendszert, és hogy miként képezi le a CIS (Controls v 7.1) és a NIST (SP 800-53) keretrendszerhez hasonló útmutatást.
- Az Azure biztonsági teljesítményteszt-állapotának (és más vezérlők) megfelelőségének **monitorozása** a Azure Security Center [szabályozási megfelelőségi irányítópult](../../security-center/security-center-compliance-dashboard.md)használatával.
- **Hozzon létre guardrails** a biztonságos konfigurációk automatizálásához és az Azure biztonsági teljesítményteszt (és a szervezeten belüli egyéb követelmények) megfelelőségének érvényesítéséhez az Azure tervrajzokkal és Azure Policyával.
 
Vegye figyelembe, hogy az Azure Security benchmark v2 összhangban van a [Microsoft biztonsági eljárásaival](/security/compass/microsoft-security-compass-introduction) (korábban Azure Security Compass), így az Azure biztonsági teljesítményteszt egyetlen összevont nézetet biztosít a Microsoft Azure biztonsági javaslatairól.

## <a name="common-use-cases"></a>Gyakori használati esetek

Az Azure biztonsági teljesítménytesztet gyakran használják a következő gyakori kihívások kezelésére az ügyfelek vagy a szolgáltatási partnerek számára:
- Az Azure-ban új és ajánlott biztonsági eljárásokat keresve a biztonságos üzembe helyezés biztosításához.
- A meglévő Azure-beli üzemelő példányok biztonsági helyzetének javítása a leggyakoribb kockázatok és enyhítések rangsorolása érdekében.
- A technológiai és üzleti célú Azure-szolgáltatások jóváhagyása az egyes biztonsági irányelveknek való megfelelés érdekében.
- A kormányzati vagy a kifejezetten szabályozott iparágak, például a Pénzügy és az egészségügyi ellátás (vagy olyan szolgáltatók, akiknek szükségük van a rendszerek kiépítésére) vonatkozó szabályozási követelmények teljesítése. Ezeknek az ügyfeleknek biztosítaniuk kell, hogy az Azure konfigurációja megfeleljen az iparági keretrendszerben (például CIS, NIST vagy PCI) meghatározott biztonsági képességeknek. Az Azure biztonsági teljesítményteszt hatékony megközelítést biztosít az iparági referenciaértékekhez már előre hozzárendelt vezérlőkhöz.

## <a name="terminology"></a>Terminológia

A "Control", a "benchmark" és az "Baseline" kifejezések gyakran az Azure biztonsági teljesítményteszt dokumentációjában használatosak, és fontos tisztában lenni azzal, hogy az Azure hogyan használja ezeket a feltételeket.


| Időszak | Leírás | Példa |
|--|--|--|
| Szabályozás | A vezérlő egy olyan szolgáltatás vagy tevékenység magas szintű leírása, amelyet kezelni kell, és nem a technológiára vagy a megvalósításra jellemző. | Az adatvédelem az egyik biztonsági vezérlő. Ez a vezérlő olyan konkrét műveleteket tartalmaz, amelyeket meg kell oldani az adatvédelem biztosítása érdekében. |
| Külső viszonyítási alap | A teljesítménytesztek biztonsági javaslatokat tartalmaznak egy adott technológiára, például az Azure-ra. Az ajánlásokat azon vezérlőelem kategorizálja, amelyhez tartoznak. | Az Azure biztonsági teljesítményteszt az Azure platformra jellemző biztonsági ajánlásokat tartalmazza |
| Alapkonfiguráció | Az alapkonfiguráció a teljesítményteszt megvalósítása az egyes Azure-szolgáltatásokon. Az Azure implementációs hatókörében minden szervezet a teljesítménytesztek ajánlását és a megfelelő konfigurációkat határozza meg. | A contoso vállalat úgy néz ki, hogy az Azure SQL Security alapkonfigurációjában javasolt konfigurációt követve engedélyezi az Azure SQL biztonsági funkcióinak használatát.

Szívesen fogadjuk visszajelzését az Azure biztonsági teljesítményteszttel kapcsolatban! Javasoljuk, hogy adjon meg megjegyzéseket az alábbi visszajelzési területeken. Ha inkább a saját bemenetét szeretné megosztani az Azure Security teljesítményteszt csapatával, kérjük, töltse ki a következő űrlapot: https://aka.ms/AzSecBenchmark
