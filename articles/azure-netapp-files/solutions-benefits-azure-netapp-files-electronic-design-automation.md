---
title: A Azure NetApp Files használatának előnyei az elektronikus tervezési automatizáláshoz | Microsoft Docs
description: A megoldás ismerteti, Azure NetApp Files a félvezető és a chip design Industry igényeinek kielégítéséhez nyújt segítséget. Olyan tesztelési forgatókönyveket mutat be, amelyek a szabványos iparági teljesítménytesztet futtatják az elektronikus tervezési automatizáláshoz (EDA) Azure NetApp Files használatával.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/24/2020
ms.author: b-juche
ms.openlocfilehash: fcede16619e8488796adc6f4c60af30643c1aadf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "82160153"
---
# <a name="benefits-of-using-azure-netapp-files-for-electronic-design-automation"></a>Az Azure NetApp elektronikus tervautomatizálással való használatának előnyei

A félvezető-és a chip-kialakítási iparág szempontjából a piacra jutási idő (TTM) kritikus szempont. Az iparág nagy sávszélességgel és kis késleltetéssel kell rendelkeznie a tároláshoz. Ez a cikk ismerteti az iparági igényeknek való megfelelést biztosító Azure NetApp Files megoldást. Olyan tesztelési forgatókönyveket mutat be, amelyek a szabványos iparági teljesítménytesztet futtatják az elektronikus tervezési automatizáláshoz (EDA) Azure NetApp Files használatával. 

## <a name="test-scenario-configurations"></a>Forgatókönyv-konfigurációk tesztelése

A tesztek három forgatókönyvet tartalmaznak a következő konfigurációkkal. 

|    Eset    |    Kötetek    |    Ügyfelek<br> SLES15 D16s_v3  |
|----------------|---------------|--------------------------------|
|    Eggyel         |    1          |    1                           |
|    Kettővel         |    6          |    24                          |
|    Három       |    12         |    24                          |

Az első forgatókönyv azt mutatja, hogy meddig lehet egy kötetet meghajtani.  

A második és a harmadik forgatókönyv egyetlen Azure NetApp Files végpont korlátait értékeli ki. Megvizsgálják az I/O felső határértékek és késések lehetséges előnyeit.

## <a name="test-scenario-results"></a>Tesztelési forgatókönyv eredményei

A következő táblázat összefoglalja a tesztelési forgatókönyvek eredményeit.

|    Eset       |    I/O-arány<br>  2 MS     |    I/O-arány<br>  a peremen     |    Teljesítmény<br>  2 MS     |    Teljesítmény<br>  a peremen     |
|-------------------|---------------------------|--------------------------------|-----------------------------|----------------------------------|
|    1 kötet       |    39 601                 |    49 502                      |    692 MiB/s                 |    866 MiB/s                      |
|    6 kötet      |    255 613                |    317 000                     |    4 577 MiB/s               |    5 568 MiB/s                    |
|    12 kötet     |    256 612                |    319 196                     |    4 577 MiB/s               |    5 709 MiB/s                    |

Az egykötetes forgatókönyv az alkalmazás alapvető konfigurációját jelöli. Ez az alapszintű forgatókönyv a követő tesztelési forgatókönyvekhez.  

A hat kötetes forgatókönyv lineáris növekedést mutat (600%) az egykötetes munkaterheléshez képest.  Egyetlen virtuális hálózaton belüli összes kötet egyetlen IP-címen érhető el.  

A 12 kötetes forgatókönyv a hat kötetre vonatkozó általános késést mutatja. Az elérhető átviteli sebesség azonban nem növekszik.   

Az alábbi ábra az EDA számítási feladatok késését és műveleti arányát mutatja Azure NetApp Fileson.  

![Az EDA munkaterhelésének késése és műveleti aránya Azure NetApp Files](../media/azure-netapp-files/solutions-electronic-design-automation-workload-latency-operation-rate.png)   

A következő gráf szemlélteti az EDA számítási feladatának késését és átviteli sebességét Azure NetApp Fileson.  

![Az EDA számítási feladatok késése és átviteli sebessége Azure NetApp Files](../media/azure-netapp-files/solutions-electronic-design-automation-workload-latency-throughput.png) 

## <a name="layout-of-test-scenarios"></a>Tesztelési forgatókönyvek elrendezése 

Az alábbi táblázat összefoglalja a tesztelési forgatókönyvek elrendezését.

|    Tesztelési forgatókönyv     |    Könyvtárak száma összesen     |    Fájlok teljes száma     |
|----------------------|------------------------------------|------------------------------|
|    1 kötet          |    88 000                          |    880 000                   |
|    6 kötet         |    568 000                         |    5 680 000                 |
|    12 kötet        |    568 000                         |    5 680 000                 |

A teljes munkaterhelés egy párhuzamosan futó funkcionális és fizikai fázisok keveréke. Ez egy tipikus, az EDA-eszközök egyik készletéről egy másikra irányuló folyamat.   

A funkcionális fázis kezdeti specifikációkat és logikai kialakítást tartalmaz. A fizikai fázis akkor zajlik le, amikor a logikai terv fizikai chipre lett konvertálva. A kijelentkezési és a kilépési fázisban a végső ellenőrzés befejeződött, és a rendszer a kialakítást egy öntödei üzemben tárolja.  

A funkcionális fázis szekvenciális és véletlenszerű olvasási és írási I/O-műveleteket tartalmaz. A funkcionális fázis a metaadatok intenzív, például a fájl stat és a hozzáférési hívások. Bár a metaadat-műveletek mérete gyakorlatilag nem méret, az olvasási és írási műveletek tartománya kevesebb, mint 1 K és 16 K közé esik. A legtöbb olvasási érték 4 és 16 K között van.  A legtöbb írás 4 K vagy kevesebb. A fizikai fázis a szekvenciális olvasási és írási műveletekből tevődik össze, és 32 K és 64 K OP-méretek keverékével rendelkezik.  

A fenti gráfokban a teljesítmény nagy része a számítási feladatok szekvenciális fizikai fázisában található. Az I/O a kis véletlenszerű és a metaadatokat használó funkcionális fázisból származik. Mindkét fázis párhuzamosan zajlik. 

Összefoglalva, az Azure-beli számítási feladatokhoz a Azure NetApp Files for EDA kialakításával méretezhető sávszélességet érhet el. 

## <a name="next-steps"></a>Következő lépések

- [Megoldásarchitektúrák az Azure NetApp Filesszal](azure-netapp-files-solution-architectures.md)
