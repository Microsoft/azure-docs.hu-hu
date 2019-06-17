---
title: HSM fizikai biztonság – Azure dedikált HSM-be |} A Microsoft Docs
description: Fizikai biztonság Azure dedikált HSM eszközök adatközpontokban kapcsolatos információk
services: dedicated-hsm
author: barclayn
manager: barbkess
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: barclayn
ms.openlocfilehash: 6fe0557aa90ec3345d4ca0c3d9292cab3435bb2f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "62118060"
---
# <a name="azure-dedicated-hsm-physical-security"></a>Az Azure dedikált HSM fizikai biztonság

Az Azure, a dedikált HSM segít a speciális biztonsági kulcs tárolási igényeinek megfelelően. Célszerű a felügyelt következő szigorú biztonsági eljárások az ügyfelek igényeinek teljes életciklusa során.

## <a name="security-through-procurement"></a>Beszerzés biztonság

A Microsoft egy biztonságos beszerzési folyamatot követi. Azt a felügyeleti lánc kezelése, és ellenőrizze, hogy az adott eszköz rendezett, és tartalmazza a szükséges az eszközre érkező adatközpontjainak. Az eszközök vannak illetéktelen-esemény műanyag készít biztonsági másolatot. Egy biztonságos tárolás területén mindaddig, amíg az adatok katalógus adatközpont üzembe tárolódnak.  Az a hardveres biztonsági modulokhoz tartalmazó állványok nagy üzleti impact(HBI) minősülnek. Az eszköz zárolva van, és videó felügyelet alatt álló mindig előtér, majd vissza.

## <a name="security-through-deployment"></a>Üzembe helyezés biztonság

HSM-EK állványokon és kapcsolódó hálózati összetevők telepítése. Miután telepítette, akkor konfigurálni kell, mielőtt azok elérhetővé válnak az Azure dedikált HSM szolgáltatás részeként. Ez a konfiguráció tevékenység Microsoft-alkalmazottak számára, amely egy háttér-ellenőrzésen estek át végzi. "Csak az időben" (JIT) felügyeleti hozzáférés szükséges használták, és csak a megfelelő alkalmazottak való hozzáférés korlátozására szolgál. Az eljárások és rendszerek is győződjön meg arról, hogy a rendszer naplózza a hardveres biztonsági modulokhoz kapcsolatos összes tevékenységet.

## <a name="security-in-operations"></a>A biztonsági műveletek

Hardveres biztonsági modulokat a hardveres berendezések (a tényleges HSM folyamatban van a berendezés belül PCI-kártya) így lehetséges, hogy összetevő szolgáltatói hibák fordulhatnak elő. Lehetséges problémák közé tartozik, de nem korlátozódnak ventilátor és power ellátási hibák. Ilyen típusú események lesz szükség, vagy hibajavítási tevékenységek cserélhető összetevők helyett.

### <a name="component-replacement"></a>Összetevő cseréje

Eszköz kiépítése után ügyfél felügyelete alatt, a ritkáról gyakori elérésű-cserélhető tápegység pedig csak összetevőt, amelyet szeretne cserélni. Ez az összetevő a biztonsági határon kívül esik, és a egy illetéktelen esemény nem okoz. Hibajegyalapú rendszert egy Microsoft-mérnök eléréséhez a HBI a tor-hátsó hitelesítésére szolgál. A jegy feldolgozásakor a rendszer egy átmeneti fizikai kulcs jelenik meg. Ezt a kulcsot a visszafejtés hozzáférést biztosít az eszközt, és lehetővé teszi, hogy az érintett összetevő felcserélni. Minden más access (azaz meghamisítása esemény okozó) kell végrehajtania, amikor az eszköz fel nem osztott így minimalizálja a biztonság és elérhetőség kockázati ügyfél számára.  

### <a name="device-replacement"></a>Eszköz cseréje

Összes eszköz meghibásodik egy hasonló összetevőhiba során használt folyamat után következik. Ha egy ügyfél nem tudja az eszközt zeroize, vagy az eszközt egy ismeretlen állapotban van, az adatokat tartalmazó eszközök lesznek távolítva, és egy a-állvány megsemmisítését bin helyezi el. A bin elhelyezett eszközök ellenőrzött és biztonságos módon meg kell semmisíteni. Állvány ellátott eszközöket a HBI-adatok nem lesznek ne módosítsa a Microsoft-adatközpontba.

### <a name="other-rack-access-activities"></a>Más Rack hozzáférési tevékenység

Ha egy Microsoft-mérnök kell elérnie az állványra szerelt hardveres biztonsági modulokhoz (például hálózati eszköz-karbantartás) által használt, Standard szintű biztonsági eljárásokkal férnek hozzá a HBI biztonságos rack lesz használható. Minden hozzáférés lesz a videó megfigyelés alatt. A rendszer érvényesíti a hardveres biztonsági modulokhoz történő [3-es szintű FIPS 140-2](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.140-2.pdf) így a hardveres biztonsági modulokhoz jogosulatlan elérését, az ügyfél fogja jelezni, és adatokat fog kell zeroized.

## <a name="logical-level-security-considerations"></a>Logikai biztonsági szempontok

HSM-EK lesznek kiépítve, az ügyfél által létrehozott virtuális hálózatban. Ez az ügyfél titkos IUP címtér.  Ez a konfiguráció értékes logikai hálózati szintű különítve, és biztosítja a hozzáférés csak az ügyfél által. Ez azt jelenti, hogy az összes logikai biztonsági vezérlők az ügyfél felelőssége.

## <a name="next-steps"></a>További lépések

Javasoljuk, hogy a szolgáltatást, például a magas rendelkezésre állás és a biztonság és a támogatási lehetőségek az összes alapfogalmak például megértsük eszközkiépítési, az alkalmazás-tervezés vagy a központi telepítés előtt.

* [Magas rendelkezésre állás](high-availability.md)
* [Hálózat](networking.md)
* [Támogatási lehetőségek](supportability.md)
* [Monitorozás](monitoring.md)
* [Üzembe helyezési architektúrája](deployment-architecture.md)