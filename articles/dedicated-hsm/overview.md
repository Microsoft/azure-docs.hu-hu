---
title: Mi az a Dedicated HSM? – Azure dedikált HSM | Microsoft Docs
description: Ismerje meg, hogy az Azure dedikált HSM egy Azure-szolgáltatás, amely kriptográfiai kulcsot biztosít az Azure-ban.
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.custom: mvc, seodec18
ms.date: 03/25/2021
ms.author: keithp
ms.openlocfilehash: 0e07839c3c5ce542335eeadc92e6a3c98fe87856
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105606980"
---
# <a name="what-is-azure-dedicated-hsm"></a>Mi az az Azure Dedicated HSM?

Az Azure dedikált HSM egy Azure-szolgáltatás, amely kriptográfiai kulcsot biztosít az Azure-ban. A dedikált HSM megfelel a legszigorúbb biztonsági követelményeknek. Ez az ideális megoldás olyan ügyfelek számára, akik FIPS 140-2 3. szintű, ellenőrzött eszközöket igényelnek, valamint a HSM-készülék teljes és kizárólagos felügyeletét. 

 A HSM-eszközök világszerte több Azure-régióban is üzembe helyezhetők. Ezek egyszerűen üzembe helyezhetők, és magas rendelkezésre állású eszközökhöz konfigurálhatók. A HSM-eszközök a régiók között is üzembe helyezhetők, így biztosítható a regionális szintű feladatátvétel. A Microsoft a [Thales Luna 7 HSM Model A790](https://cpl.thalesgroup.com/encryption/hardware-security-modules/network-hsms) készülékek használatával biztosítja a dedikált HSM szolgáltatást. Ez az eszköz a legmagasabb szintű teljesítményt és titkosítási integrációs lehetőségeket kínálja. 

A kiépítés után a HSM-eszközök közvetlenül csatlakoznak az ügyfél virtuális hálózatához. A helyszíni alkalmazások és a felügyeleti eszközök is hozzáférhetnek a pont – hely vagy helyek közötti VPN-kapcsolat konfigurálásához. Az ügyfelek a szoftvereket és a dokumentációt a HSM-eszközök [Thales-ügyfél-támogatási portálról](https://supportportal.thalesgroup.com/csm)történő konfigurálásához és kezeléséhez kapják meg.

## <a name="why-use-azure-dedicated-hsm"></a>Miért érdemes az Azure dedikált HSM-et használni?

### <a name="fips-140-2-level-3-compliance"></a>FIPS 140-2 szint – 3 megfelelőség

Számos szervezet rendelkezik olyan szigorú iparági szabályozással, amely azt diktálja, hogy a titkosítási kulcsokat [FIPS 140-2 3. szintű](https://csrc.nist.gov/publications/detail/fips/140/2/final) , ellenőrzött HSM kell tárolni. Az Azure dedikált HSM és egy új, egybérlős ajánlat, [Azure Key Vault felügyelt HSM (előzetes verzió)](https://docs.microsoft.com/azure/key-vault/managed-hsm), a különböző iparági szegmensek, például a pénzügyi szolgáltatások iparága, a kormányzati szervek és más, az FIPS 140-2-es szintű 3. szint követelményeinek való megfelelést segítő ügyfelek. Habár a Microsoft több-bérlős [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) szolgáltatása jelenleg az FIPS 140-2 2-es szintű érvényesített HSM használja. 

### <a name="single-tenant-devices"></a>Egybérlős eszközök

Ügyfeleink többsége a kriptográfiai tárolóeszköz egyszeri bérletének követelménye. Az Azure dedikált HSM szolgáltatás lehetővé teszi, hogy fizikai eszközt helyezzen üzembe a Microsoft globálisan elosztott adatközpontjának egyikéről. Miután kiosztotta az ügyfelet, csak az ügyfél férhet hozzá az eszközhöz.

### <a name="full-administrative-control"></a>Teljes körű felügyeleti felügyelet

Számos ügyfél számára szükséges a teljes körű rendszergazdai ellenőrzés és az eszközre való kizárólagos hozzáférés felügyeleti célból. Az eszköz üzembe helyezése után csak az ügyfél rendelkezik rendszergazdai vagy alkalmazás-hozzáférési jogosultsággal az eszközhöz.

 A Microsoft nem rendelkezik rendszergazdai ellenőrzéssel, miután az ügyfél első alkalommal hozzáfér az eszközhöz, amikor az ügyfél megváltoztatja a jelszót. Ettől kezdve az ügyfél egy teljes körű rendszergazdai vezérléssel és az alkalmazás-felügyeleti képességgel rendelkező, valódi önálló bérlő. A Microsoft a telemetria a soros porton keresztüli kapcsolaton keresztüli figyelési szintű hozzáférést (nem rendszergazdai szerepkört) tart fenn. Ez a hozzáférés magában foglalja a hardveres figyelőket, például a hőmérsékletet, a tápegység állapotát és a ventilátor állapotát. 
 
 Az ügyfél ingyenesen tilthatja le ezt a figyelést. Ha azonban letiltják, akkor nem kapnak proaktív egészségügyi riasztást a Microsofttól.

### <a name="high-performance"></a>Nagy teljesítmény

A Thales eszköz számos okból lett kiválasztva ehhez a szolgáltatáshoz. A titkosítási algoritmusok széles skáláját kínálja, számos támogatott operációs rendszert és széles körű API-támogatást. Az üzembe helyezett adott modell kiváló teljesítményt nyújt az RSA-2048 10 000 művelete másodpercenként. 10 partíciót támogat, amelyek egyedi alkalmazás-példányokhoz is használhatók. Ez az eszköz egy kis késleltetésű, nagy kapacitású és nagy teljesítményű eszköz.

### <a name="unique-cloud-based-offering"></a>Egyedi felhőalapú ajánlat

A Microsoft felismerte, hogy egy egyedi ügyfél-készletre van szükség. Ez az egyetlen olyan felhőalapú szolgáltató, amely új ügyfelek számára biztosít egy dedikált HSM-szolgáltatást, amely az FIPS 140-2 3. szintű, és a felhőalapú és helyszíni alkalmazások integrálását is biztosítja.

## <a name="is-azure-dedicated-hsm-right-for-you"></a>Megfelelő az Azure dedikált HSM?

Az Azure dedikált HSM egy speciális szolgáltatás, amely egy adott típusú nagyméretű szervezet egyedi követelményeit tárgyalja. Ennek eredményeképpen várható, hogy az Azure-ügyfelek nagy része nem fér hozzá a szolgáltatáshoz használt profilhoz. Számos megtalálhatja a Azure Key Vault szolgáltatást, hogy megfelelőbb és költséghatékony legyen. Annak eldöntéséhez, hogy az megfelel-e a követelményeknek, a következő feltételeket azonosították.

### <a name="best-fit"></a>Legjobb illeszkedés

Az Azure dedikált HSM a legmegfelelőbb a "lift-and-SHIFT" forgatókönyvekhez, amelyek közvetlen és kizárólagos hozzáférést igényelnek a HSM-eszközökhöz. Példák:

- Alkalmazások migrálása a helyszínről az Azure-ba Virtual Machines
- Az Amazon AWS EC2 származó alkalmazások migrálása olyan virtuális gépekre, amelyek az AWS Cloud HSM klasszikus szolgáltatást használják (az Amazon nem nyújtja ezt a szolgáltatást új ügyfeleknek)
- Shrink-burkolt szoftverek, például Apache/ngnix SSL-kiszervezési, Oracle TDE-és ADCS futtatása az Azure-ban Virtual Machines 

### <a name="not-a-fit"></a>Nem fér

Az Azure dedikált HSM nem megfelelő a következő típusú forgatókönyvhöz: Microsoft Cloud Services, amely támogatja a titkosítást az ügyfél által felügyelt kulcsokkal (például Azure Information Protection, Azure Disk Encryption, Azure Data Lake Store, Azure Storage, Azure SQL Database és az Office 365-hez tartozó ügyfél-kulcs), amelyek nem integráltak az Azure dedikált HSM-szel.

### <a name="it-depends"></a>Attól függ

Az, hogy az Azure dedikált HSM működni fog-e az Ön számára, a követelmények és a kompromisszumok potenciálisan összetett kombinációján múlik. Ilyen például a FIPS 140-2 3. szintű követelménye. Ez a követelmény gyakori, és az Azure dedikált HSM és egy új, egybérlős ajánlat, [Azure Key Vault felügyelt HSM (előzetes verzió)](https://docs.microsoft.com/azure/key-vault/managed-hsm) jelenleg csak az egyetlen lehetőség az értekezletre. Ha ezek a feltételek nem relevánsak, akkor gyakran a Azure Key Vault és az Azure dedikált HSM között választhat. A döntés meghozatala előtt mérje fel a követelményeket.

Olyan helyzetekben, amelyekben a következőkre lesz szüksége: 

- Az ügyfél Azure-beli virtuális gépén futó új kód
- SQL Server TDE egy Azure-beli virtuális gépen
- Az Azure Storage ügyféloldali titkosítása
- SQL Server és Azure SQL DB Always Encrypted

## <a name="next-steps"></a>Következő lépések

Ez egy nagyon specializált szolgáltatás. Ezért javasoljuk, hogy teljes mértékben Ismerje meg a dokumentáció alapfogalmait, beleértve a díjszabást, a támogatást és a szolgáltatói szerződéseket. 

A [Thales-integrációs útmutatók](https://cpl.thalesgroup.com/partners/overview) segítségével megkönnyítheti a HSM kiépítését egy meglévő virtuális hálózati környezetbe. Az útmutató segítségével megtudhatja, hogyan állíthatja be a telepítési architektúrát.

* [Magas rendelkezésre állás](high-availability.md)
* [Fizikai biztonság](physical-security.md)
* [Hálózat](networking.md)
* [Támogatási lehetőségek](supportability.md)
* [Figyelés](monitoring.md)
