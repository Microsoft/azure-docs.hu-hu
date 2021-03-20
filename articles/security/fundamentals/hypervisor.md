---
title: A hypervisor biztonsága az Azure-flottában – Azure Security
description: A hypervisor biztonságának technikai áttekintése az Azure-flottában.
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/10/2020
ms.openlocfilehash: 766266edd663b75b893a5883e30bb48eed7bbfdf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94696115"
---
# <a name="hypervisor-security-on-the-azure-fleet"></a>A hypervisor biztonsága az Azure-flotta esetében

Az Azure hypervisor rendszer a Windows Hyper-V-n alapul. A hypervisor rendszer lehetővé teszi a számítógép rendszergazdája számára, hogy olyan vendég partíciókat határozzon meg, amelyeken külön címtartomány szerepel. A különálló címterület lehetővé teszi, hogy az (állomás) operációs rendszer párhuzamosan működő operációs rendszerét és alkalmazásait a számítógép gyökérkönyvtárában hajtsa végre. A gazdagép operációs rendszerének (más néven Kiemelt főpartíciónak) közvetlen hozzáférése van az összes fizikai eszközhöz és perifériához a rendszeren (Storage-vezérlők, hálózati adaptációk). A gazdagép operációs rendszere lehetővé teszi a vendég partíciók számára, hogy megosszák a fizikai eszközök használatát a "virtuális eszközök" minden vendég partícióhoz való kiírásával. Így a vendég partíción futó operációs rendszerek hozzáférhetnek a virtualizált perifériás eszközökhöz, amelyeket a legfelső szintű partíción futó virtualizálási szolgáltatások biztosítanak.

Az Azure hypervisor a következő biztonsági célkitűzéseket tartja szem előtt:

| Cél | Forrás |
|--|--|
| Elkülönítés | A biztonsági szabályzatok nem adják át A virtuális gépek közötti adatátvitelt. Ehhez a korlátozáshoz a Virtual Machine Manager (VMM) és a hardver a memória, az eszközök, a hálózat és a felügyelt erőforrások (például a megőrzött adatmennyiség) elkülönítéséhez szükséges képességekre van szükség. |
| VMM integritása | A rendszer általános integritásának eléréséhez az egyes hypervisor-összetevők integritása létrejött és karbantartás alatt áll. |
| Platform integritása | A hypervisor integritása attól függ, hogy a hardver és a szoftver milyen integritású. Bár a hypervisor nem irányítja át a platform integritását, az Azure olyan hardver-és belső vezérlőprogram-mechanizmusokra támaszkodik, mint például a [Cerberus](project-cerberus.md) -lapka a mögöttes platform integritásának védelme és észlelése érdekében. A VMM és a vendégek nem futnak, ha a platform integritása sérült. |
| Korlátozott hozzáférés | A felügyeleti funkciókat csak a biztonságos kapcsolatokon keresztül csatlakoztatott jogosult rendszergazdák gyakorolhatják. Az Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) mechanizmusai a legalacsonyabb jogosultsági szint elvét kényszerítik ki. |
| Naplózás | Az Azure lehetővé teszi, hogy a naplózási funkció rögzítse és megvédje a rendszeren megjelenő információkat, hogy később is megvizsgálható legyen. |

A Microsoft az Azure Hypervisort és a virtualizációs alrendszer megkeményedésének megközelítését a következő három kategóriába lehet bontani.

## <a name="strongly-defined-security-boundaries-enforced-by-the-hypervisor"></a>A hypervisor által kényszerített, szigorúan meghatározott biztonsági határok

Az Azure hypervisor több biztonsági határt kényszerít ki a következők között:

- Virtualizált "vendég" partíciók és privilegizált partíciók ("gazdagép")
- Több vendég
- Saját maga és a gazdagép
- Saját maga és minden vendég

A biztonság, az integritás és a rendelkezésre állás a hypervisor biztonsági határain belül biztosított. A határok különböző támadásokkal szembeni védelmet biztosítanak, beleértve az oldalsó csatornákon alapuló adatszivárgásokat, a szolgáltatásmegtagadás és a jogosultságok kiterjesztését.

A hypervisor biztonsági határ a hálózati forgalom, a virtuális eszközök, a tárolás, a számítási erőforrások és az összes többi virtuálisgép-erőforrás között szegmentálást is biztosít a bérlők között.

## <a name="defense-in-depth-exploit-mitigations"></a>Védelem részletes kiaknázásának enyhítése

Abban az esetben, ha a biztonsági határ nem valószínű, az Azure hypervisor több réteget is tartalmaz, többek között a következőket:

- A gazdagépen alapuló folyamatok elkülönítése több virtuális gépre kiterjedő összetevők üzemeltetése esetén
- Virtualizálás-alapú biztonság (VBS) a felhasználói és kernel módú összetevők biztonságos világból való integritásának biztosításához
- Több szintű kiaknázási megoldás. A megoldáshoz tartozik a Címterület-elrendezés véletlenszerű (ASLR), az adatvégrehajtás-megakadályozás (DEP), a tetszőleges programkód-védelem, a vezérlési folyamat integritása és az adatsérülés megakadályozása.
- A stack változók automatikus inicializálása a fordítóprogram szintjén
- Kernel API-k, amelyek automatikusan nullával inicializálják a Hyper-V által létrehozott kernel heap-foglalásokat

Ezek a megoldások úgy lettek kialakítva, hogy a virtuális gépek közötti sebezhetőség nem valósítható meg.

## <a name="strong-security-assurance-processes"></a>Erős biztonsági megbízhatósági folyamatok

A hypervisorhoz kapcsolódó támadási felület magában foglalja a szoftverek hálózatkezelését, a virtuális eszközöket és az összes több virtuális gépre kiterjedő felületet. A támadási felületet az automatizált Build-integráción keresztül követjük, amely rendszeres biztonsági felülvizsgálatokat indít el.

Az összes virtuális gép támadási felülete a fenyegetések modellezése, a kód áttekintése, a zavaros és a vörös csapat által a biztonsági határ megsértése miatt tesztelt. A Microsoft egy olyan [bug Bounty-programmal](https://www.microsoft.com/msrc/bounty-hyper-v) rendelkezik, amely díjat fizet a Microsoft Hyper-V jogosult termékeinek megfelelő biztonsági réseit illetően.

> [!NOTE]
> További információ a Hyper-V [erős biztonsági megbízhatósági folyamatairól](../../azure-government/azure-secure-isolation-guidance.md#strong-security-assurance-processes) .

## <a name="next-steps"></a>Következő lépések
Ha többet szeretne megtudni a platform integritásának és biztonságának elvégzéséről, olvassa el a következőt:

- [A belső vezérlőprogram biztonsága](firmware.md)
- [Biztonságos rendszerindítás](secure-boot.md)
- [Mért rendszerindítás és gazdagép-igazolás](measured-boot-host-attestation.md)
- [Project Cerberus](project-cerberus.md)
- [Titkosítás inaktív állapotban](encryption-atrest.md)