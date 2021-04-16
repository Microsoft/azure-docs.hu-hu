---
title: Mi az a Dedicated HSM? – Azure Dedicated HSM | Microsoft Docs
description: Megtudhatja, Azure Dedicated HSM azure-szolgáltatás hogyan biztosít titkosítási kulcstárolót az Azure-ban.
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
ms.openlocfilehash: 418c8f0844bf2336ce0d4a681071f237d81877ca
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107505717"
---
# <a name="what-is-azure-dedicated-hsm"></a>Mi az az Azure Dedicated HSM?

Azure Dedicated HSM azure-szolgáltatás kriptográfiai kulcstárolót biztosít az Azure-ban. Dedicated HSM megfelel a legszigorúbb biztonsági követelményeknek. Ez az ideális megoldás az olyan ügyfelek számára, akik FIPS 140-2 3. szintű, 3. szintű eszközt igényelnek, és teljes körű és kizárólagos ellenőrzést igényelnek a HSM-berendezés felett. 

 A HSM-eszközök globálisan vannak üzembe stb. több Azure-régióban. Ezek egyszerűen kiépíthetők két eszközként, és magas rendelkezésre állásra konfigurálhatók. A HSM-eszközök régiók között is kiépítve biztosítják a regionális szintű feladatátvételt. A Microsoft a Dedicated HSM szolgáltatást a [Thales Luna 7 HSM A790-es modellel biztosítja.](https://cpl.thalesgroup.com/encryption/hardware-security-modules/network-hsms) Ez az eszköz kínálja a legmagasabb szintű teljesítményt és titkosítási integrációs lehetőségeket. 

A kiépítés után a HSM-eszközök közvetlenül csatlakoznak az ügyfél virtuális hálózatához. A helyszíni alkalmazás- és felügyeleti eszközök a pont–hely VPN-kapcsolat konfigurálásakor is elérhetők. Az ügyfelek a Thales ügyféltámogatási portálján keresztül kapják meg a HSM-eszközök konfigurálását és kezelését lehetővé t vevő [szoftvereket és dokumentációt.](https://supportportal.thalesgroup.com/csm)

## <a name="why-use-azure-dedicated-hsm"></a>Miért érdemes a Azure Dedicated HSM?

### <a name="fips-140-2-level-3-compliance"></a>FIPS 140-2 3. szintű megfelelőség

Számos szervezet szigorú iparági követelményeket szab meg, amelyek előírják, hogy a titkosítási kulcsokat [a FIPS 140-2 3.](https://csrc.nist.gov/publications/detail/fips/140/2/final) szintje szerint ellenőrzött HSM-eken kell tárolni. Azure Dedicated HSM és egy új egybérlős ajánlat, a [Azure Key Vault Managed HSM,](https://docs.microsoft.com/azure/key-vault/managed-hsm)segít a különböző iparági szegmensek ügyfeleinek, például a pénzügyi szolgáltatási iparágban, a kormányzati szervekben és másokban a FIPS 140-2 3. szintű követelményeinek való megfelelésben. Bár a Microsoft több-bérlős [Azure Key Vault](https://docs.microsoft.com/azure/key-vault) a FIPS 140-2 2. szintje szerint ellenőrzött HSM-eket használ. 

### <a name="single-tenant-devices"></a>Egybérlős eszközök

Számos ügyfelünknek követelmény a kriptográfiai tárolóeszköz egyetlen élettartamának használata. A Azure Dedicated HSM lehetővé teszi számukra, hogy fizikai eszközt létesítsen a Microsoft egyik globálisan elosztott adatközpontjában. Miután kiépíti az ügyfelet, csak az ügyfél férhet hozzá az eszközhöz.

### <a name="full-administrative-control"></a>Teljes körű felügyeleti vezérlés

Sok ügyfélnek teljes felügyeleti felügyeletre van szüksége, és csak felügyeleti célokra van szüksége az eszközéhez való hozzáférésre. Az eszköz kiépítése után csak az ügyfél rendelkezik rendszergazdai vagy alkalmazásszintű hozzáféréssel az eszközhöz.

 A Microsoft nem rendelkezik rendszergazdai ellenőrzéssel, miután az ügyfél először fér hozzá az eszközhöz, és amikor az ügyfél megváltoztatja a jelszót. Ettől a ponttól az ügyfél egy valódi egybérlős, teljes körű felügyeleti vezérlési és alkalmazáskezelési képességgel. A Microsoft monitorszintű hozzáférést tart fenn (nem rendszergazdai szerepkört) a telemetriában soros portkapcsolaton keresztül. Ez a hozzáférés olyan hardverfigyelőket fed le, mint a hőmérséklet, az energiaellátás állapota és a ventilátorok állapota. 
 
 Az ügyfél szabadon letilthatja ezt a monitorozási figyelőt. Ha azonban letiltják, nem kap proaktív állapotriasztásokat a Microsofttól.

### <a name="high-performance"></a>Nagy teljesítmény

A Thales-eszköz több okból is ki lett választva ehhez a szolgáltatáshoz. Széles körű titkosítási algoritmustámogatást, számos támogatott operációs rendszert és széles körű API-támogatást nyújt. Az üzembe helyezett modell kiváló teljesítményt nyújt másodpercenként 10 000 művelettel az RSA-2048 esetében. 10 partíciót támogat, amelyek egyedi alkalmazáspéldányokhoz használhatók. Ez az eszköz egy kis késésű, nagy kapacitású és nagy átviteli sebességű eszköz.

### <a name="unique-cloud-based-offering"></a>Egyedi felhőalapú ajánlat

A Microsoft felismerte, hogy egyedi ügyfélkészletre van szükség. Ez az egyetlen felhőszolgáltató, amely fiPS 140-2 3. szintű, dedikált HSM-szolgáltatást kínál az új ügyfeleknek, és ilyen mértékű felhőalapú és helyszíni alkalmazásintegrációt kínál.

## <a name="is-azure-dedicated-hsm-right-for-you"></a>Megfelelő Azure Dedicated HSM az Ön számára?

Azure Dedicated HSM egy speciális szolgáltatás, amely egy adott típusú nagy léptékű szervezet egyedi követelményeit elégi ki. Emiatt az Azure-ügyfelek nagy része várhatóan nem fér hozzá a szolgáltatáshoz használt profilhoz. Sokan találhatják a Azure Key Vault szolgáltatást megfelelőbbnek és költséghatékonyabbnak. Annak eldöntésében, hogy ez megfelel-e a követelményeknek, az alábbi feltételeket azonosítottuk.

### <a name="best-fit"></a>Legjobb illeszkedés

Azure Dedicated HSM az olyan "átemlhető" forgatókönyvekhez a legmegfelelőbb, amelyek közvetlen és kizárólagos hozzáférést igényelnek a HSM-eszközökhöz. Példák:

- Alkalmazások mirating from on-premises to Azure Virtual Machines
- Alkalmazások áttelepítése Amazon AWS EC2-ről klasszikus AWS Cloud HSM szolgáltatást használó virtuális gépekre (az Amazon nem kínálja ezt a szolgáltatást új ügyfeleknek)
- Zsugorba csomagolt szoftverek, például az Apache/Ngnix SSL-kiszervezés, az Oracle TDE és az ADCS futtatása az Azure Virtual Machines 

### <a name="not-a-fit"></a>Nem illeszkedés

Azure Dedicated HSM a következő típusú forgatókönyvekhez nem alkalmas: Az ügyfél által kezelt kulcsokkal (például Azure Information Protection, Azure Disk Encryption, Azure Data Lake Store, Azure Storage, Azure SQL Database és Az Office 365 ügyfélkulcsával) való titkosítást támogató Microsoft-felhőszolgáltatások, amelyek nincsenek integrálva az Azure Dedicated HSM.

### <a name="it-depends"></a>Attól függ

Attól Azure Dedicated HSM, hogy a rendszer működni fog-e az Ön számára, a követelmények és a biztonsági problémák potenciálisan összetett kombinációitól függ, amelyek nem tehetjük meg vagy nem tehetjük meg. Ilyen például a FIPS 140-2 3. szintű követelménye. Ez a követelmény gyakori, Azure Dedicated HSM egy új egybérlős ajánlat, Azure Key Vault [felügyelt HSM](https://docs.microsoft.com/azure/key-vault/managed-hsm) jelenleg az egyetlen lehetőség a követelményeknek való megfelelőre. Ha ezek a kötelező követelmények nem relevánsak, akkor gyakran választani kell a Azure Key Vault és a Azure Dedicated HSM. A döntés meghozatala előtt mérje fel a követelményeket.

Az alábbi helyzetekben kell mérlegelnie a lehetőségeket: 

- Az ügyfél Azure-beli virtuális gépében futó új kód
- SQL Server TDE létrehozása Azure-beli virtuális gépen
- Az Azure Storage ügyféloldali titkosítása
- SQL Server adatbázis Azure SQL és Always Encrypted

## <a name="next-steps"></a>Következő lépések

Ez egy rendkívül specializált szolgáltatás. Ezért azt javasoljuk, hogy teljes mértékben megértse a jelen dokumentációs készlet fő fogalmait, beleértve a díjszabást, a támogatást és a szolgáltatásiszint-szerződéseket. 

A [Thales integrációs útmutatói](https://cpl.thalesgroup.com/partners/overview) segítségével a HSM-eket meglévő virtuális hálózati környezetben is üzembe lehet venni. Az üzembe helyezési architektúra beállításának meghatározásához útmutatók is rendelkezésre állnak.

* [Magas rendelkezésre állás](high-availability.md)
* [Fizikai biztonság](physical-security.md)
* [Hálózat](networking.md)
* [Támogatási lehetőségek](supportability.md)
* [Figyelés](monitoring.md)
