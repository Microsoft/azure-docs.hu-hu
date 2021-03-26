---
title: HSM fizikai biztonság – Azure dedikált HSM | Microsoft Docs
description: Információk az Azure dedikált HSM-eszközök fizikai biztonságáról az adatközpontokban
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/25/2021
ms.author: keithp
ms.openlocfilehash: 8437d12075a148a3e8062f62e195bc019d89a85b
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105606963"
---
# <a name="azure-dedicated-hsm-physical-security"></a>Azure dedikált HSM fizikai biztonság

Az Azure dedikált HSM a legfontosabb tárterület speciális biztonsági követelményeinek teljesítéséhez nyújt segítséget. Az ügyfelek igényeihez igazodva szigorú biztonsági eljárásokat követve teljes életciklusa alatt kezelhető.

## <a name="security-through-procurement"></a>Biztonság a beszerzéseken keresztül

A Microsoft biztonságos beszerzési folyamatot követ. Felügyeljük a felügyeleti láncot, és biztosítjuk, hogy az adott eszköz az adatközpontokban legyen elküldve. Az eszközök szerializált illetéktelenek, műanyag zacskókban és tárolókban találhatók. Ezeket biztonságos tárolóhelyen tárolják addig, amíg az adatközpont adatgalériájában üzembe nem kerül.  A HSM-eszközöket tartalmazó állványok nagy üzleti hatásnak számítanak (HBI). Az eszközök zárolása és a videó-megfigyelés alatt minden alkalommal elölről és vissza történik.

## <a name="security-through-deployment"></a>Biztonság az üzembe helyezéssel

A HSM a társított hálózatkezelési összetevőkkel együtt települnek. A telepítést követően konfigurálni kell őket, mielőtt elérhetővé válnak az Azure dedikált HSM szolgáltatás részeként. Ezt a konfigurációs tevékenységet olyan Microsoft-alkalmazottak hajtják végre, akik háttér-ellenőrzésen estek át. Az "igény szerinti" (JIT) felügyelettel csak a megfelelő alkalmazottakhoz férhet hozzá, a hozzáféréshez pedig csak a szükséges időt kell használni. A használt eljárások és rendszerek azt is biztosítják, hogy a rendszer naplózza a HSM-eszközökhöz kapcsolódó összes tevékenységet.

## <a name="security-in-operations"></a>Biztonság a műveletekben

A HSM (a tényleges HSM a berendezésen belül PCI-kártya), így lehetséges, hogy az összetevők szintjén problémák merülhetnek fel. A lehetséges problémák például a ventilátor-és áramellátási hibákra nem korlátozódnak. Az ilyen típusú eseményekhez karbantartási vagy megszakítási/javítási tevékenységek szükségesek a cserélhető összetevők cseréjéhez.

### <a name="component-replacement"></a>Összetevő cseréje

Az eszköz üzembe helyezése és az Ügyfélkapcsolatok kezelése után a lecserélhető áramellátás az egyetlen olyan összetevő, amelyet a rendszer lecserél. Ez az összetevő kívül esik a biztonsági határon, és nem okoz illetéktelen módosítási eseményt. A Ticketing rendszer használatával engedélyezhető a Microsoft mérnöke, hogy hozzáférjen a HBI állványhoz. A jegy feldolgozásakor a rendszer ideiglenes fizikai kulcsot állít ki. Ez a kulcs biztosítja a mérnök számára az eszköz elérését, és lehetővé teszi, hogy az érintett összetevőt felcserélje. Minden más hozzáférés (azaz a módosítási esemény okozta) akkor kerül végrehajtásra, ha egy eszköz nincs lefoglalva az ügyfél számára, így minimalizálva a biztonsági és a rendelkezésre állási kockázatot.  

### <a name="device-replacement"></a>Eszköz cseréje

A teljes eszköz meghibásodása esetén a rendszer az összetevő meghibásodása során használt folyamathoz hasonló folyamatot követ. Ha az ügyfél nem tudja zeroize az eszközt, vagy ha az eszköz ismeretlen állapotban van, a rendszer eltávolítja az adattároló eszközöket, és egy tárolóban lévő megsemmisítési raktárhelyen helyezi el azokat. A tárolóban elhelyezett eszközöket a rendszer ellenőrzött és biztonságos módon fogja megsemmisíteni. A HBI rackből egyetlen eszköz sem fog elhagyni egy Microsoft-adatközpontot.

### <a name="other-rack-access-activities"></a>Egyéb rack-hozzáférési tevékenységek

Ha egy Microsoft-mérnöknek hozzá kell férnie a HSM-eszközök által használt állványhoz (például hálózati eszközök karbantartása), a rendszer szabványos biztonsági eljárásokat használ a HBI Secure rackhez való hozzáféréshez. Minden hozzáférés a videó-megfigyelés alatt lesz. A HSM-eszközök ellenőrzése a [FIPS 140-2 3. szintre](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.140-2.pdf) történik, így a HSM-eszközök jogosulatlan hozzáférését a rendszer a felhasználó felé fogja jelezni.

## <a name="logical-level-security-considerations"></a>Logikai szintű biztonsági megfontolások

A HSM egy ügyfél által létrehozott virtuális hálózatra vannak kiépítve az ügyfél magánhálózati IP-címén belül.  Ez a konfiguráció értékes logikai hálózati szintű elkülönítést biztosít, és biztosítja, hogy csak az ügyfél férhessen hozzá. Ez azt jelenti, hogy az összes logikai szintű biztonsági ellenőrzés az ügyfél felelőssége.

## <a name="next-steps"></a>Következő lépések

Javasoljuk, hogy a szolgáltatás alapvető fogalmait, például a magas rendelkezésre állást és a biztonságot és a támogatást például jól megértse az eszköz kiépítése, az alkalmazások tervezése vagy üzembe helyezése előtt.

* [Magas rendelkezésre állás](high-availability.md)
* [Hálózat](networking.md)
* [Támogatási lehetőségek](supportability.md)
* [Figyelés](monitoring.md)
* [Üzembe helyezési architektúra](deployment-architecture.md)
