---
title: Üzembe helyezési architektúra – Azure dedikált HSM | Microsoft Docs
description: Alapszintű kialakítási szempontok az Azure dedikált HSM egy alkalmazás-architektúra részeként való használatakor
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/25/2021
ms.author: keithp
ms.openlocfilehash: c454b2e4df7a9ce5fadd33386e5bb413b503c6e4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105608425"
---
# <a name="azure-dedicated-hsm-deployment-architecture"></a>Az Azure Dedicated HSM üzembe helyezési architektúrája

Az Azure dedikált HSM kriptográfiai kulcsot biztosít az Azure-ban. Megfelel a szigorú biztonsági követelményeknek. Az ügyfelek az Azure dedikált HSM használatát fogják kihasználni, ha:

* Meg kell felelnie az [FIPS 140-2 Level-3](https://csrc.nist.gov/publications/detail/fips/140/2/final) minősítésnek
* Megkövetelik, hogy kizárólagos hozzáféréssel rendelkezzenek a HSM-hez
* az eszközök teljes körű vezérlését kell biztosítania

A HSM a Microsoft adatközpontjai között oszlanak el, és egyszerűen üzembe helyezhetők egy olyan eszközként, amely egy magasan elérhető megoldás alapja. A katasztrófa-elhárítási megoldáshoz az egyes régiókban is üzembe helyezhetők. A dedikált HSM-mel elérhető régiók jelenleg a [termékek régiónként lapon](https://azure.microsoft.com/global-infrastructure/services/?products=azure-dedicated-hsm)ellenőrizhetők. 

* USA keleti régiója
* USA 2. keleti régiója
* USA nyugati régiója
* USA 2. nyugati régiója
* USA déli középső régiója
* Délkelet-Ázsia
* Kelet-Ázsia
* Közép-India
* Dél-India
* Kelet-Japán
* Nyugat-Japán
* Észak-Európa
* Nyugat-Európa
* Az Egyesült Királyság déli régiója
* Az Egyesült Királyság nyugati régiója
* Közép-Kanada
* Kelet-Kanada
* Kelet-Ausztrália
* Délkelet-Ausztrália
* Észak-Svájc
* Nyugat-Svájc
* USA-beli államigazgatás – Virginia
* USA-beli államigazgatás – Texas

Mindkét régióban található HSM-állványok két független adatközpontban vagy legalább két független rendelkezésre állási zónában vannak üzembe helyezve. A Dél-Kelet-Ázsia három rendelkezésre állási zónával rendelkezik, az USA 2. keleti régiója pedig kettő. Európa, Ázsia és Észak-Amerika összesen húsz régiót kínál a dedikált HSM szolgáltatáshoz. Az Azure-régiókkal kapcsolatos további információkért tekintse meg a hivatalos  [Azure-régiók információit](https://azure.microsoft.com/global-infrastructure/regions/).
A dedikált HSM-alapú megoldás egyes tervezési tényezői a hely/késés, a magas rendelkezésre állás és más elosztott alkalmazások támogatása.

## <a name="device-location"></a>Eszközhely

Az optimális HSM-eszköz helye a titkosítási műveleteket végző alkalmazások legközelebbi közelsége. A régión belüli késés várhatóan egy számjegyből álló ezredmásodperc. A régiók közötti késés akár 5 – 10-szor is lehet ennél nagyobb.

## <a name="high-availability"></a>Magas rendelkezésre állás

A magas rendelkezésre állás eléréséhez az ügyfélnek két HSM-eszközt kell használnia egy olyan régióban, amely magas rendelkezésre állású pár Thales szoftverrel van konfigurálva. Ez a típusú központi telepítés biztosítja a kulcsok rendelkezésre állását, ha egyetlen eszköz problémát tapasztal, amely megakadályozza a kulcsfontosságú műveletek feldolgozását. Emellett jelentős mértékben csökkenti a kockázatokat a kitörési/javítási feladatok, például a tápegység cseréje során. Fontos, hogy a tervezést bármilyen regionális szintű meghibásodás esetén figyelembe lehessen venni. A regionális szintű hibák akkor fordulnak elő, ha természeti katasztrófák, például hurrikánok, árvizek vagy földrengések állnak fenn. Az ilyen típusú eseményeket a HSM-eszközök egy másik régióban való kiépítési felállításával kell enyhíteni. A másik régióban üzembe helyezett eszközök összekapcsolhatók a Thales szoftveres konfigurációján keresztül. Ez azt jelenti, hogy a magasan elérhető és a katasztrófa-elhárítási megoldás minimális üzembe helyezése négy HSM-eszköz két régióban. A helyi redundancia és a redundancia a régiók között alapkonfigurációként használható, amely további HSM-eszközök üzembe helyezését is lehetővé tenné a késés, a kapacitás vagy más, az alkalmazásspecifikus követelmények teljesítése érdekében.

## <a name="distributed-application-support"></a>Elosztott alkalmazások támogatása

A dedikált HSM-eszközök jellemzően olyan alkalmazások támogatásához vannak telepítve, amelyeknek a fő tárterületet és a kulcsfontosságú lekérési műveleteket kell végrehajtaniuk. A dedikált HSM-eszközök 10 partíciót biztosítanak a független alkalmazások támogatásához. Az eszköz helyének a szolgáltatást használó összes alkalmazás átfogó nézetén kell alapulnia.

## <a name="next-steps"></a>Következő lépések

Az üzembe helyezési architektúra meghatározása után az architektúra megvalósításához szükséges legtöbb konfigurációs tevékenységet a Thales fogja biztosítani. Ez magában foglalja az eszközök konfigurálását, valamint az alkalmazás-integrációs forgatókönyveket is. További információkért használja a [Thales ügyfélszolgálati](https://supportportal.thalesgroup.com/csm) portálját, és töltse le az adminisztrációs és konfigurációs útmutatókat. A Microsoft partner webhelyének számos integrációs útmutatója van.
Javasoljuk, hogy a szolgáltatás alapvető fogalmait, például a magas rendelkezésre állást és a biztonságot, az eszköz kiépítése vagy az alkalmazások tervezése és üzembe helyezése előtt jól megértse.
További fogalmi szintű témakörök:

* [Magas rendelkezésre állás](high-availability.md)
* [Fizikai biztonság](physical-security.md)
* [Hálózat](networking.md)
* [Támogatási lehetőségek](supportability.md)
* [Figyelés](monitoring.md)
