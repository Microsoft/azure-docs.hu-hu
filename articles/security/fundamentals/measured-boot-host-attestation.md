---
title: A belső vezérlőprogram által mért rendszerindítás és a gazdagép-igazolás – Azure Security
description: Az Azure belső vezérlőprogram által mért rendszerindítási és a gazdagép-igazolás technikai áttekintése.
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/10/2020
ms.openlocfilehash: f4906d4e0590df047bac4ee15cb0e996a59c3d5b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96498863"
---
# <a name="measured-boot-and-host-attestation"></a>Mért rendszerindítás és gazdagép-igazolás
Ez a cikk azt ismerteti, hogyan biztosítja a Microsoft a gazdagépek integritását és biztonságát a mért rendszerindítás és a gazdagép-igazolás használatával.

## <a name="measured-boot"></a>Mért rendszerindítás

A [platformmegbízhatósági modul](/windows/security/information-protection/tpm/trusted-platform-module-top-node) (TPM) egy megbízható külső fél által szolgáltatott belső vezérlőprogram által védett, illetéktelenül módosítható, titkosítással védett naplózási összetevő. A rendszerindítási konfigurációs napló a platform konfigurációs regiszterei (PCR) által rögzített, kivonattal láncolt méréseket tartalmaz, ha a gazdagép utoljára a rendszerindítás során következik be. Az alábbi ábra a rögzítési folyamatot mutatja be. A korábban kivonatos mérések növekményes hozzáadása a következő mérték kivonatához és a kivonatolási algoritmus futtatása a Union-ben a kivonatoló láncolást hajtja végre.

![A gazdagép-igazolási szolgáltatás kivonat-láncolását bemutató diagram.](./media/measured-boot-host-attestation/hash-chaining.png)

Az igazolás akkor valósul meg, ha egy állomás a rendszerindítási konfigurációs naplójának (TCGLog) használatával igazolja a konfigurációs állapotát. A rendszerindítási napló hamisítása nehéz, mert a TPM nem teszi elérhetővé az olvasási és a kiterjesztési műveleteken kívüli PCR-értékeit. Továbbá a gazdagép-igazolási szolgáltatás által megadott hitelesítő adatok meghatározott PCR-értékekre vannak lezárva. A kivonatoló láncolás nem teszi lehetővé, hogy a rendszer számítási feladatokkal kizárja a hitelesítő adatokat a sávon kívül.

## <a name="host-attestation-service"></a>Gazdagép-igazolási szolgáltatás

A gazdagép-igazolási szolgáltatás egy megelőző intézkedés, amely ellenőrzi, hogy a gazdagépek megbízhatóak-e, mielőtt az ügyfelek adataival vagy munkaterhelésekkel kommunikálhatnak. A gazdagép-igazolási szolgáltatás ellenőrzi a megfelelőségi nyilatkozat (a gazdagép megfelelőségének ellenőrizhető igazolása) ellenőrzésével, amelyet az egyes gazdagépek az igazolási szabályzattal (a biztonságos állapot definíciója) küldenek. A rendszer integritását a TPM által biztosított [megbízhatósági kapcsolat](https://www.uefi.org/sites/default/files/resources/UEFI%20RoT%20white%20paper_Final%208%208%2016%20%28003%29.pdf) biztosítja.

A gazdagép-igazolási szolgáltatás minden egyes Azure-fürtben megtalálható egy speciális, zárolt környezeten belül. A zárolt környezet a gazdagépek rendszerindítási protokolljában részt vevő más forgalomirányító szolgáltatásokat is magában foglal. A nyilvános kulcsokra épülő infrastruktúra (PKI) közvetítőként működik az igazolási kérelmek és identitás-kibocsátók (a sikeres gazdagép-igazolások alapján történő) ellenőrzéséhez. Az igazolási gazdagép számára kiadott igazolási hitelesítő adatokat a rendszer lezárta az identitásával. Csak a kérelmező gazdagép tudja kizárni a hitelesítő adatokat, és kihasználhatja azokat a növekményes engedélyek beszerzéséhez. Ez megakadályozza a támadásokat a középső és a hamisítás elleni támadások ellen.

Ha egy Azure-gazdagép biztonsági hibás konfigurációval érkezik a gyárból, vagy az adatközpontban módosítva van, a TCGLog a következő igazoláskor a gazdagép-igazolási szolgáltatás által megjelölt veszélyeket tartalmazza, ami az igazolási hibát okoz. Az igazolási hibák megakadályozzák, hogy az Azure-flotta megbízzon a jogsértő gazdagépen. Ez a megelőzés hatékonyan blokkolja a gazdagépről érkező és onnan érkező összes kommunikációt, és elindítja az incidensek munkafolyamatát. A vizsgálat és a részletes utólagos elemzések a kiváltó okok megállapítására, valamint a kompromisszum esetleges feladatainak meghatározására szolgálnak. Csak azt követően végezhető el az elemzés, hogy a gazdagép szervizelhető, és lehetősége van az Azure-flottához való csatlakozásra, valamint az ügyfelek számítási feladatainak elvégzésére.

A következő a gazdagép-igazolási szolgáltatás magas szintű architektúrája:

![A gazdagép-igazolási szolgáltatás architektúráját bemutató ábra.](./media/measured-boot-host-attestation/host-attestation-arch.png)

## <a name="attestation-measurements"></a>Igazolási mérések

Az alábbiakban néhány példát láthat a ma rögzített mérésekre.

### <a name="secure-boot-and-secure-boot-keys"></a>Biztonságos rendszerindítás és biztonságos rendszerindító kulcsok
Ha ellenőrzi, hogy az aláírás-adatbázis és a visszavont aláírások adatbázis-kivonatai helyesek-e, a gazdagép-igazolási szolgáltatás biztosítja, hogy az ügyfél ügynöke a megfelelő szoftvert bízza meg. A nyilvános kulcsú beléptetési kulcs adatbázisának és a nyilvános platform kulcsának aláírásának ellenőrzésével a gazdagép-igazolási szolgáltatás megerősíti, hogy csak a megbízható felek rendelkeznek engedéllyel a szoftverek megbízhatónak tekintett definícióinak módosításához. Végül annak biztosításával, hogy a biztonságos rendszerindítás aktív legyen, a gazdagép-igazolási szolgáltatás érvényesíti ezeket a definíciókat.

### <a name="debug-controls"></a>Hibakeresési vezérlők
A debuggerek hatékony eszközöket biztosítanak a fejlesztők számára. A memóriához és más hibakeresési parancsokhoz való korlátlan hozzáférés azonban gyengítheti az adatvédelmet és a rendszer integritását, ha az nem megbízható fél számára van megadva. A gazdagép-igazolási szolgáltatás gondoskodik arról, hogy az éles gépeken történő rendszerindítás le legyen tiltva.

### <a name="code-integrity"></a>Kód integritása
A UEFI [biztonságos rendszerindítás](secure-boot.md) biztosítja, hogy csak megbízható, alacsony szintű szoftverek futhatnak a rendszerindítási folyamat során. Ugyanezeket az ellenőrzéseket is alkalmazni kell a rendszerindítás utáni környezetben az illesztőprogramok és más végrehajtható fájlok kernel módú hozzáféréssel való használatával. Ebből a célból egy kód-integritási (CI) szabályzattal határozható meg, hogy mely illesztőprogramokat, bináris fájlokat és más végrehajtható fájlokat tekinti megbízhatónak az érvényes és érvénytelen aláírások megadásával. Ezeket a szabályzatokat a rendszer kényszeríti. A szabályzat megsértése riasztásokat állít elő a biztonsági incidensek válasza csapatának a vizsgálathoz.

## <a name="next-steps"></a>Következő lépések
Ha többet szeretne megtudni a platform integritásának és biztonságának elvégzéséről, olvassa el a következőt:

- [A belső vezérlőprogram biztonsága](firmware.md)
- [Biztonságos rendszerindítás](secure-boot.md)
- [Project Cerberus](project-cerberus.md)
- [Titkosítás inaktív állapotban](encryption-atrest.md)
- [A hipervizor biztonsága](hypervisor.md)