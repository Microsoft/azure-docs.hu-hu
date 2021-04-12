---
title: Általános Azure bizalmas számítástechnikai forgatókönyvek és használati esetek
description: Ismerje meg, hogyan használható a bizalmas számítástechnika a forgatókönyvben.
services: virtual-machines
author: JBCook
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.topic: overview
ms.date: 9/22/2020
ms.author: jencook
ms.openlocfilehash: 47938f3a44c3a47f8b444b59d7e2f0867a274f33
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102566617"
---
# <a name="common-scenarios-for-azure-confidential-computing"></a>Az Azure bizalmas számítástechnika gyakori forgatókönyvei

Ez a cikk áttekintést nyújt az Azure bizalmas számítástechnika számos gyakori forgatókönyvéről. A cikkben szereplő javaslatok kiindulási pontként szolgálnak az alkalmazás bizalmas számítástechnikai szolgáltatásokkal és keretrendszerekkel történő fejlesztése során. 

A cikk elolvasása után a következő kérdésekre tud válaszolni:

- Milyen forgatókönyvek vonatkoznak az Azure bizalmas számítástechnikai szolgáltatására? –
- Milyen előnyökkel jár az Azure bizalmas számítástechnika használata több féltől származó forgatókönyvek esetén?
- Milyen előnyökkel jár az Azure bizalmas számítástechnika blockchain-hálózaton való használata?


## <a name="secure-multi-party-computation"></a>Biztonságos többrésztvevős számítási funkció
Az Azure bizalmas számítástechnika lehetővé teszi, hogy több forrásból származó adatokat dolgozzon fel anélkül, hogy a bemeneti adatokat más feleknek tenné. Az ilyen típusú biztonságos számítások többek között a következőkhöz hasonlóak: a pénzmosás elleni védelem, a csalások észlelése és az egészségügyi adatok biztonságos elemzése.

Több forrás is feltöltheti az adatok egy enklávéba egy virtuális gépen. Az egyik fél arra utasítja az enklávét, hogy számítást végezzen vagy dolgozza fel az adatfeldolgozást. Egyetlen fél sem (még az elemzés végrehajtása) ténylegesen láthatja a más feleknek az enklávéba feltöltött adataikat. 

A biztonságos többrésztvevős számítástechnika esetében a titkosított adatok bekerülnek az enklávéba, az enklávé pedig visszafejti az adatokat egy kulccsal, elemzést végez, eredményt kap, és titkosított eredményt küld, amelyet a fél visszafejt a kijelölt kulccsal. 

**A használatban lévő adatai védelme**: 
- Használjon DCsv2-Series virtuális gépet (VM) az Azure-ban az engedélyezett Intel SGX ENKLÁVÉHOZ-támogatással. Ezek a virtuális gépek olyan megbízható végrehajtási környezetekkel (TEEs) rendelkeznek, amelyek az alkalmazásadatok és a kód egyes részeit védik és elszigetelik.
- Az enklávét támogató SDK használatával létrehozhat egy enklávét a virtuális gépen belül. Az enklávéban az adattárolók nem lesznek elérhetők a virtuális gép szolgáltatója számára sem. Az enklávéban tárolt adatszolgáltatások hardveres támogatással lesznek titkosítva.
    - Használhatja például az [OE SDK](https://github.com/openenclave/openenclave) -t a kiszolgálóoldali feldolgozáshoz. 

**Az átvitel alatt álló adatok védelme** 
- Az igazolt TLS biztonságos csatornaként való használata az adatforgalom biztonságának garantálása érdekében
- Az ügyfél gondoskodik arról, hogy az adatküldés csak ugyanarra a kiszolgálóra történjen, amelyet az enklávé véd. 

**Az inaktív adatok védelme**
- Védett és biztonságos adattárolók használata az adatok biztonságának biztosításához a nyugalmi állapotban 

### <a name="anti-money-laundering"></a>Pénzmosás elleni küzdelem
Ebben a biztonságos, többrésztvevős számítási példában több Bank osztozik az adatokon anélkül, hogy az ügyfeleik személyes adatokkal kellene kitéve. A bankok a közös bizalmas adathalmazon való elemzéssel elfogadják a jóváhagyott adatokat. Az összesített adatkészleten lévő elemzések egyetlen felhasználó által több Bank közötti mozgást tudnak érzékelni, anélkül, hogy a bankok hozzáférjenek egymáshoz.

A bizalmas számítástechnika révén ezek a pénzügyi intézmények növelhetik a csalások észlelési arányát, a pénzmosás-forgatókönyveket, csökkentik a hamis pozitív állapotokat, és folytatják a nagyobb adatkészletek megismerését. 

### <a name="drug-development-in-healthcare"></a>Kábítószer-fejlesztés az egészségügyben
A partneri állapotú létesítmények a saját állapotú adatkészleteket egy ML-modell betanításához járulnak hozzá. Minden létesítmény csak a saját adatkészletét láthatja. Nincs más létesítmény, vagy akár a felhőalapú szolgáltató is, láthatja az adattípust vagy a betanítási modellt. 

![Beteg állapotának elemzése](./media/use-cases-scenarios/patient-data.png)

Minden létesítmény a betanított modell használatát élvezi. A modell több adattal való létrehozásával a modell pontosabb lett. Minden olyan létesítmény, amely hozzájárult a modell képzéséhez, használhatja azt, és hasznos eredményeket kaphat. 

## <a name="blockchain"></a>Blockchain

A blockchain-hálózat csomópontok decentralizált hálózata. Ezeket a csomópontokat olyan operátorok vagy validatorok futtatják és kezelik, akik az integritást szeretnék biztosítani, és konszenzust érnek el a hálózat állapotáról. A csomópontok maguk a főkönyvek replikái, és a blockchain-tranzakciók nyomon követésére szolgálnak. Mindegyik csomópont teljes másolattal rendelkezik a tranzakciók előzményeiről, biztosítva az integritást és a rendelkezésre állást egy elosztott hálózatban.

A bizalmas számítástechnikai technológiára épülő Blockchain-technológiák hardveres adatvédelmet használhatnak az adatok titkosságának és biztonságos számításának engedélyezéséhez. Bizonyos esetekben a teljes Főkönyv titkosítva van az adathozzáférés védelme érdekében. Előfordulhat, hogy maga a tranzakció egy, az enklávén belüli számítási modulban is előfordulhat a csomóponton belül.

### <a name="confidential-consortium-framework-ccf"></a>Bizalmas konzorciumi keretrendszer (CCF)
A [CCF](https://www.microsoft.com/research/project/confidential-consortium-framework/) az Azure bizalmas számítástechnikai szolgáltatására épülő elosztott blockchain-keretrendszer egyik példája. A Microsoft Research által szorgalmazott ez a keretrendszer kihasználja a megbízható végrehajtási környezetek (pólók) erejét, hogy létrehoz egy távoli enklávés hálózatot az igazoláshoz. A csomópontok az Azure Virtual Machines ([DCsv2 sorozat](confidential-computing-enclaves.md)) felett futnak, és kihasználhatják az enklávé infrastruktúráját. Az igazolási protokollok használatával a blockchain felhasználói ellenőrizhetik egy CCF-csomópont integritását, és ellenőrizhetik a teljes hálózatot. 

![Csomópontok hálózata](./media/use-cases-scenarios/ccf.png)

A CCF-ban a decentralizált Főkönyv az összes hálózati csomóponton replikált Key-Value-tárolóban rögzített módosításokat hajt végre. Ezen csomópontok mindegyike egy tranzakciós motort futtat, amely a blockchain felhasználói által aktiválható a TLS protokollon keresztül. Ha egy végpontot indít el, az Key-Value-tárolót. A titkosított módosítások a decentralizált főkönyvbe való rögzítése előtt bizonyos számú csomópontnak meg kell egyeznie a konszenzus eléréséhez. 

## <a name="next-steps"></a>Következő lépések
DCsv2-Series virtuális gép [üzembe helyezése](quick-create-marketplace.md) .


