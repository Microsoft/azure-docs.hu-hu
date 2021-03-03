---
title: 'Előzetes verzió: megbízható indítás Azure-beli virtuális gépekhez'
description: Ismerje meg az Azure-beli virtuális gépek megbízható indítását.
author: khyewei
ms.author: khwei
ms.service: virtual-machines
ms.subservice: security
ms.topic: conceptual
ms.date: 02/26/2021
ms.reviewer: cynthn
ms.custom: template-concept; references_regions
ms.openlocfilehash: bc1afa72a0eebd2bb467616237641222b790923c
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101679805"
---
# <a name="trusted-launch-for-azure-virtual-machines-preview"></a>Megbízható indítás az Azure Virtual Machines szolgáltatásban (előzetes verzió)

Az Azure a [2. generációs](generation-2.md) virtuális gépek biztonságának javítása érdekében zökkenőmentesen kínál megbízható indítást. A megbízható indítás a fejlett és állandó támadási technikák ellen nyújt védelmet. A megbízható indítás több, egymástól függetlenül engedélyezhető infrastruktúra-technológiából tevődik össze. Minden technológia egy újabb védelmi réteget biztosít a kifinomult fenyegetések ellen.

> [!IMPORTANT]
> A megbízható indításhoz új virtuális gépeket kell létrehozni. Nem engedélyezheti a megbízható indítást olyan meglévő virtuális gépeken, amelyeket eredetileg anélkül hoztak létre.
>
> A megbízható indítás jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. 
>
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="benefits"></a>Előnyök 

- Biztonságosan telepíthet virtuális gépeket ellenőrzött rendszerindító betöltésekkel, operációsrendszer-kernelekkel és illesztőprogramokkal.
- Biztonságos védelemmel láthatja el a kulcsokat, a tanúsítványokat és a titkokat a virtuális gépeken.
- Betekintést nyerhet a teljes rendszerindítási lánc integritására.
- Győződjön meg arról, hogy a számítási feladatok megbízhatók és ellenőrizhetők.

## <a name="public-preview-limitations"></a>A nyilvános előzetes verzió korlátozásai

**Méret támogatása**: az összes [2. generációs](generation-2.md) VM-méret, kivéve a következőket:

- HBv3 
- Lsv2 sorozat 
- M sorozat 
- Mv2 sorozat 
- NDv4 sorozat 
- NVv4 sorozat

**Operációs rendszer támogatása**:
- RedHat vállalati Linux 8,3
- SUSE 15 SP2
- Ubuntu 20,04 LTS
- Ubuntu 18.04 LTS
- Windows Server 2019
- Windows Server 2016
- Windows 10 Pro
- Windows 10 Enterprise

**Régiók**: 
- USA déli középső régiója
- Észak-Európa

**Díjszabás**: a meglévő virtuális gépek díjszabása nem jár további költséggel.

**Ebben az előzetes verzióban a következő funkciók nem támogatottak**:
- Backup
- Azure Site Recovery
- Megosztott rendszerkép-katalógus
- Ideiglenes operációsrendszer-lemez
- Megosztott lemez
- Felügyelt rendszerkép
- Azure Dedicated Host 

## <a name="secure-boot"></a>Biztonságos rendszerindítás

A megbízható indítás gyökerében a biztonságos rendszerindítás a virtuális gép számára. A platform belső vezérlőprogramja által megvalósított mód a kártevő-alapú rootkitek és rendszerindító csomagok telepítésével szembeni védelmet nyújt. A biztonságos rendszerindítással gondoskodhat arról, hogy csak az aláírt operációs rendszerek és illesztőprogramok induljon el. "Megbízhatósági kapcsolatot" hoz létre a virtuális gépen lévő szoftveres verem számára. A biztonságos rendszerindítás engedélyezésével minden operációs rendszer rendszerindítási összetevőjét (rendszerindítási betöltő, kernel, kernel-illesztőprogramok) megbízható közzétevőnek kell aláírnia. Mind a Windows, mind a Linux-disztribúciók támogatják a biztonságos rendszerindítást. Ha a biztonságos rendszerindítás nem tudja hitelesíteni, hogy a képet megbízható közzétevő írta alá, akkor a virtuális gép nem indítható el. További információ: [Biztonságos rendszerindítás](https://docs.microsoft.com/windows-hardware/design/device-experiences/oem-secure-boot).

## <a name="vtpm"></a>vTPM

A megbízható indítás az Azure-beli virtuális gépek vTPM is bemutatja. Ez a hardveres [platformmegbízhatósági modul](/windows/security/information-protection/tpm/trusted-platform-module-overview)virtualizált verziója, amely megfelel a TPM 2.0 specifikációjának. Dedikált biztonságos tárolóként szolgál a kulcsok és a mérések számára. A megbízható indítás a saját dedikált TPM-példánnyal biztosítja a virtuális gépet, amely a virtuális gépek elérésén kívüli biztonságos környezetben fut. A vTPM lehetővé teszi az [igazolást](/windows/security/information-protection/tpm/tpm-fundamentals#measured-boot-with-support-for-attestation) a virtuális gép teljes rendszerindítási láncának (UEFI, os, rendszer és illesztőprogramok) mérésével. 

A megbízható indítás a vTPM használatával végez távoli igazolást a felhőben. Ez használatos a platform állapotának ellenőrzéséhez és a megbízhatóságon alapuló döntések meghozatalához. Ha állapot-ellenőrzésre van beállítva, a megbízható indítás kriptográfiailag igazolhatja, hogy a virtuális gép megfelelően elindult. Ha a folyamat meghiúsul, valószínűleg azért, mert a virtuális gép nem engedélyezett összetevőt futtat, Azure Security Center integritási riasztásokat fog kibocsátani. A riasztások olyan adatokat tartalmaznak, amelyekkel az összetevők nem tudták átadni a integritási ellenőrzéseket.

## <a name="virtualization-based-security"></a>Virtualizálás-alapú biztonság

A [virtualizálás-alapú biztonság](/windows-hardware/design/device-experiences/oem-vbs) (vbs) a hypervisor használatával biztonságos és elkülönített memóriát hoz létre. A Windows ezeket a régiókat használja különböző biztonsági megoldások futtatására a biztonsági rések és a rosszindulatú támadások elleni védelem érdekében. A megbízható indítás lehetővé teszi a hypervisor kód integritásának (HVCI) és a Windows Defender hitelesítőadat-kezelőjének engedélyezését.

A HVCI egy hatékony rendszer-csökkentés, amely védelmet biztosít a Windows kernel módú folyamatoknak a kártékony vagy nem ellenőrzött kódok injektálásával és végrehajtásával szemben. A futtatása előtt ellenőrzi a kernel módú illesztőprogramokat és a bináris fájlokat, megakadályozva a nem aláírt fájlok memóriába való betöltését. Ez biztosítja, hogy az ilyen végrehajtható kód nem módosítható, ha a betöltése engedélyezett. További információ a VBS és a HVCI szolgáltatásról: [virtualizálás-alapú biztonság (vbs) és a hypervisor által kényszerített kód integritása (HVCI)](https://techcommunity.microsoft.com/t5/windows-insider-program/virtualization-based-security-vbs-and-hypervisor-enforced-code/m-p/240571).

A megbízható indítás és a VBS használatával engedélyezheti a Windows Defender hitelesítőadat-őr szolgáltatását. Ez a szolgáltatás elkülöníti és védi a titkos kulcsokat, hogy csak a rendszerjogosultságú rendszerszoftverek férhessenek hozzájuk. Segít megakadályozni a titkos kulcsokhoz és a hitelesítő adatok ellopásához való illetéktelen hozzáférést, például pass-The-hash (PtH) támadásokat. További információ: hitelesítőadat- [őr](https://docs.microsoft.com/windows/security/identity-protection/credential-guard/credential-guard).


## <a name="security-center-integration"></a>Security Center integráció

A megbízható indítás a Azure Security Center integrálva biztosítja a virtuális gépek megfelelő konfigurálását. Azure Security Center folyamatosan értékelni fogja a kompatibilis virtuális gépeket, és kiadja a kapcsolódó javaslatokat.

- A **biztonságos rendszerindítás engedélyezésének ajánlása** – ez a javaslat csak a megbízható indítást támogató virtuális gépekre vonatkozik. Azure Security Center azonosítja a biztonságos rendszerindítást engedélyező virtuális gépeket, de azt le is tilthatja. Ez a megoldás alacsony súlyosságú javaslatot ad ki az engedélyezéshez.
- **Javaslat a vTPM engedélyezéséhez** – ha a virtuális gépnek engedélyezve van a vTPM, Azure Security Center használhatja a vendég-igazolást, és azonosíthatja a komplex veszélyforrások mintáit. Ha Azure Security Center azonosítja a megbízható indítást támogató virtuális gépeket, és a vTPM le van tiltva, akkor a rendszer alacsony súlyosságú javaslatot ad ki az engedélyezéséhez. 
- **Igazolási állapot értékelése** – ha a virtuális gépnek engedélyezve van a vTPM, a Azure Security Center egy bővítménye távolról ellenőrizheti, hogy a virtuális gép kifogástalan módon elindult-e. Ezt nevezik távoli igazolásnak. Azure Security Center kiértékeli az értékelést, jelezve a távoli igazolás állapotát.

## <a name="azure-defender-integration"></a>Azure Defender-integráció

Ha a virtuális gépek megfelelően vannak beállítva a megbízható indítással, az Azure Defender felismeri és riasztást küld a virtuális gép állapotával kapcsolatos problémákról.

- **Riasztás a VM-igazolási hiba esetén** – az Azure Defender időnként a virtuális gépeken végez igazolást. Ez a virtuális gép indításakor is megtörténik. Ha az igazolás sikertelen, közepes súlyosságú riasztást fog kiváltani.
    A virtuális gép igazolása a következő okok miatt sikertelen lehet:
    - A rendszerindítási naplót tartalmazó igazolt információk egy megbízható alaptervtől eltérnek. Ez azt jelezheti, hogy a nem megbízható modulok be lettek töltve, és az operációs rendszer biztonsága is fennáll.
    - Az igazolási idézőjelet nem lehetett ellenőrizni, hogy az igazolt virtuális gép vTPM származik. Ez arra utalhat, hogy a kártevő szoftver jelen van, és a vTPM érkező forgalom elfogására is képes.
    - A virtuális gépen lévő igazolási bővítmény nem válaszol. Ez a kártevő vagy az operációs rendszer rendszergazdája általi szolgáltatásmegtagadási támadást jelezhet.

    > [!NOTE]
    >  Ez a riasztás olyan virtuális gépek számára érhető el, amelyeken engedélyezve van a vTPM, és telepítve van az igazolási bővítmény. A biztonságos rendszerindítást engedélyezni kell az igazolások továbbításához. Az igazolás sikertelen lesz, ha a biztonságos rendszerindítás le van tiltva. Ha le kell tiltania a biztonságos rendszerindítást, a riasztást a hamis pozitív érték elkerülése érdekében letilthatja.

- **Riasztás a nem megbízható Linux kernel-modulhoz** – a biztonságos rendszerindítás engedélyezése esetén lehetséges, hogy a virtuális gép akkor is elindítható, ha a kernel-illesztőprogram nem tudja végrehajtani az ellenőrzést, és a betöltés nem engedélyezett. Ha ez történik, az Azure Defender alacsony súlyosságú riasztást ad ki. Habár nincs azonnali fenyegetés, mert a nem megbízható illesztőprogram nincs betöltve, ezeket az eseményeket meg kell vizsgálni. A következőket ajánljuk figyelmébe:
    - Melyik kernel-illesztőprogram sikertelen? Megismertem ezt az illesztőprogramot, és szeretném betölteni?
    - Az illesztőprogram pontos verziója várható? Az illesztőprogram bináris fájljai érintetlenek? Ha ez egy külső gyártótól származó illesztőprogram, a szállító átadta az operációs rendszer megfelelőségi tesztjét, hogy aláírja azt?


## <a name="faq"></a>GYIK

Gyakori kérdések a megbízható indításról.

### <a name="why-should-i-use-trusted-launch-what-does-trusted-launch-guard-against"></a>Miért érdemes a megbízható indítást használni? Mit jelent a megbízható indítás elleni védelem?

A megbízható indítási őrök a rendszerindítási készletek, a rootkitek és a kernel szintű kártevők ellen. Ezek a kifinomult típusú kártevő szoftverek kernel módban futnak, és rejtve maradnak a felhasználók elől. Például:
- Belső vezérlőprogram-rootkitek: ezek a készletek felülírják a virtuális gép BIOS-ának belső vezérlőprogramot, így a rootkit az operációs rendszer előtt is elindítható. 
- Rendszerindító készletek: ezek a készletek lecserélik az operációs rendszer rendszerbetöltő eszközét, hogy a virtuális gép az operációs rendszer előtt betöltse a rendszerindító csomagot
- Kernel rootkitek: ezek a készletek az operációs rendszer kernelének egy részét lecserélik, így a rootkit automatikusan elindul, amikor az operációs rendszer betöltődik.
- Illesztőprogram-rootkitek: ezek a készletek az operációs rendszer által a virtuális gép összetevőivel való kommunikációhoz használt megbízható illesztőprogramok egyike.

### <a name="what-are-the-differences-between-secure-boot-and-measured-boot"></a>Mi a különbség a biztonságos rendszerindítás és a mért rendszerindítás között?

A biztonságos rendszerindítási láncban a rendszerindítási folyamat minden lépése ellenőrzi a következő lépések titkosítási aláírását. A BIOS-ban például ellenőrizhető egy aláírás a betöltőn, a betöltő pedig az összes betöltött kernel-objektum aláírásait és így tovább. Ha bármelyik objektum sérült, az aláírás nem egyezik, és a virtuális gép nem fog elindulni. További információ: [Biztonságos rendszerindítás](/windows-hardware/design/device-experiences/oem-secure-boot). A mért rendszerindítás nem állítja le a rendszerindítási folyamatot, méri vagy kiszámítja a láncban lévő következő objektumok kivonatát, és a kivonatokat a platform konfigurációs regiszterekben (PCR) tárolja a vTPM. A rendszer a rendszerindítási integritás figyelésére a mért rendszerindító rekordokat használja.

### <a name="what-happens-when-an-integrity-fault-is-detected"></a>Mi történik az integritási hibák észlelésekor?

Az Azure-beli virtuális gépek megbízható elindítása a speciális fenyegetések esetében figyelhető. Ha ilyen fenyegetések észlelhetők, a rendszer riasztást vált ki. A riasztások csak a [standard szintű](/azure/security-center/security-center-pricing) Azure Security Center érhetők el.
Azure Security Center rendszeresen végez igazolást. Ha az igazolás sikertelen, közepes súlyosságú riasztás lesz aktiválva. A megbízható indítási igazolás a következő okok miatt sikertelen lehet: 
- A megbízható számítástechnikai bázis (TCB) naplóját tartalmazó igazolt információk a megbízható alaptervtől (például a biztonságos rendszerindítás engedélyezésével) térnek el. Ez azt jelezheti, hogy a nem megbízható modulok be lettek töltve, és az operációs rendszer biztonsága sérülhet.
- Az igazolási idézőjelet nem lehetett ellenőrizni, hogy az igazolt virtuális gép vTPM származik. Ez azt jelezheti, hogy a kártevő szoftver megtalálható, és a TPM-re irányuló forgalom elfogására is képes. 
- A virtuális gépen lévő igazolási bővítmény nem válaszol. Ez a kártevő vagy az operációs rendszer rendszergazdája általi szolgáltatásmegtagadási támadást jelezhet.

  
### <a name="how-does-trusted-launch-compared-to-hyper-v-shielded-vm"></a>Hogyan működik a megbízható indítás a Hyper-V védett virtuális géphez képest?
A Hyper-V védett virtuális gép jelenleg csak a Hyper-V-ben érhető el. A [Hyper-V védett virtuális gép](/windows-server/security/guarded-fabric-shielded-vm/guarded-fabric-and-shielded-vms) jellemzően védett hálóval együtt települ. A védett hálók egy gazdagép Guardian-szolgáltatásból (HGS), egy vagy több védett gazdagépből és egy védett virtuális gépekből állnak. A Hyper-V védelemmel ellátott virtuális gépek olyan hálón való használatra készültek, ahol a virtuális gép adatait és állapotát védeni kell a Hyper-V-gazdagépeken esetlegesen futó háló-rendszergazdák és nem megbízható szoftverek között. A megbízható indítás másrészt önálló virtuális gép vagy virtuálisgép-méretezési csoportként is üzembe helyezhető az Azure-ban a HGS további üzembe helyezése és kezelése nélkül. Az összes megbízható indítási funkció engedélyezhető egy egyszerű módosítással a központi telepítési kódban, vagy egy jelölőnégyzetet a Azure Portal.  

### <a name="how-can-i-convert-existing-vms-to-trusted-launch"></a>Hogyan válthatok a meglévő virtuális gépek megbízható indításra?
A 2. generációs virtuális gépek esetében a megbízható indításra való átalakítás áttelepítési útvonala az általánosan elérhető (GA).

## <a name="next-steps"></a>Következő lépések

[Megbízható indítású virtuális gép üzembe helyezése a portál használatával](trusted-launch-portal.md).
