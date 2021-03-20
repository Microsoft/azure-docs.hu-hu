---
title: A szükséges alhálózati méret meghatározása & tartományban
titleSuffix: Azure SQL Managed Instance
description: Ez a témakör azt ismerteti, hogyan számítható ki az az alhálózat, amelyben az Azure SQL felügyelt példánya telepítve lesz.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova
ms.date: 02/22/2019
ms.openlocfilehash: 156a4c74eea24b20c28df88be85cb32c0ebe2981
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96012450"
---
# <a name="determine-required-subnet-size--range-for-azure-sql-managed-instance"></a>A szükséges alhálózati méret meghatározása & az Azure SQL felügyelt példányának tartománya
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Az Azure SQL felügyelt példányát egy Azure-beli [virtuális hálózaton (VNet)](../../virtual-network/virtual-networks-overview.md)belül kell üzembe helyezni.

A VNet alhálózatában üzembe helyezhető felügyelt példányok száma az alhálózat méretétől függ (az alhálózat tartománya).

Felügyelt példány létrehozásakor az Azure számos virtuális gépet foglal le a kiépítés során kiválasztott szintjétől függően. Mivel ezek a virtuális gépek az alhálózathoz vannak társítva, IP-címeket igényelnek. A normál működés és a szolgáltatás karbantartása során a magas rendelkezésre állás biztosítása érdekében az Azure további virtuális gépeket foglalhat le. Ennek eredményeképpen a szükséges IP-címek száma nagyobb, mint az alhálózaton lévő felügyelt példányok száma.

A rendszer úgy lett kialakítva, hogy egy felügyelt példánynak legalább 32 IP-címre van szüksége egy alhálózaton. Ennek eredményeképpen az alhálózat IP-tartományainak meghatározásakor a legkisebb alhálózati maszk a /27 lehet. Ajánlott gondosan megtervezni a felügyelt példányok üzembe helyezéséhez az alhálózat méretét. A tervezés során figyelembe veendő bemenetek:

- A felügyelt példányok száma, beleértve a következő példány-paramétereket:
  - szolgáltatási szintek
  - hardver létrehozása
  - Virtuális mag száma
- A szolgáltatási szintek vertikális felskálázásának vagy módosításának megtervezése

> [!IMPORTANT]
> Az alhálózati méret 16 IP-címmel (alhálózati maszk/28) lehetővé teszi a felügyelt példányok üzembe helyezését, de csak az értékeléshez vagy fejlesztési/tesztelési forgatókönyvekben használt egyetlen példány üzembe helyezéséhez használható, amelyekben a skálázási műveletek nem lesznek elvégezve.

## <a name="determine-subnet-size"></a>Alhálózat méretének meghatározása

Az alhálózat méretezése a jövőbeli példányok központi telepítésének és a skálázási igényeknek megfelelően. A következő paraméterek segíthetnek a számítás kialakításában:

- Az Azure öt IP-címet használ az alhálózaton a saját igényeinek megfelelően
- Minden virtuális fürt további számú címet foglal le 
- Az egyes felügyelt példányok az árképzési szinttől és a hardver-létrehozástól függő címek számát használják.

> [!IMPORTANT]
> Az alhálózat címtartománya nem módosítható, ha található erőforrás az alhálózaton. A felügyelt példányok nem helyezhetők át az alhálózatok között. Amikor csak lehetséges, érdemes nagyobb méretű alhálózatokat használni, és nem kevesebbet, hogy a jövőben ne legyenek probléma.

GP = általános célú; BC = üzleti szempontból kritikus; VC = virtuális fürt

| **Hardveres Gen** | **Tarifacsomag** | **Azure-használat** | **VC-használat** | **Példány használata** | **Teljes*** |
| --- | --- | --- | --- | --- | --- |
| Gen4 | GP | 5 | 1 | 5 | 11 |
| Gen4 | BC | 5 | 1 | 5 | 11 |
| Gen5 | GP | 5 | 6 | 3 | 14 |
| Gen5 | BC | 5 | 6 | 5 | 16 |

  \* Az oszlop összesen azon címek számát jeleníti meg, amelyek akkor történnek, ha az egyik példány üzembe helyezése az alhálózatban történik. Az alhálózatban lévő minden további példány hozzáadja a példány-használat oszlophoz tartozó címek számát. Az Azure használati oszlopával jelölt címek több virtuális fürtön is megoszthatók, míg a VC-használat oszlopmal jelölt címek megosztása megtörténik a virtuális fürtön elhelyezett példányok között.

A frissítési művelethez általában a virtuális fürtök átméretezése szükséges. Bizonyos esetekben a frissítési művelethez a virtuális fürt létrehozása szükséges (további részletekért lásd a [kezelési műveletek című cikket](sql-managed-instance-paas-overview.md#management-operations)). Virtuális fürt létrehozása esetén a további címek számának meg kell egyeznie a VC-használat oszlop által képviselt címek számával, és a virtuális fürtben lévő példányokhoz szükséges címekkel (a példányok használati oszlopa).

**1. példa**: egy általános célú felügyelt példány (Gen4 hardver) és egy üzleti szempontból kritikus felügyelt példány (Gen5 hardver) megtervezése. Ez azt jelenti, hogy legalább 5 + 1 + 1 * 5 + 6 + 1 * 5 = 22 IP-címet kell telepítenie. Mivel az IP-címtartományok meghatározása 2, az alhálózat legalább 32 (2 ^ 5) IP-címtartományt igényel ehhez a központi telepítéshez.<br><br>
A fentiekben említettek szerint a frissítési művelethez szükség van a virtuális fürt létrehozására. Ez azt jelenti, hogy a Gen5 üzleti szempontból kritikus felügyelt példányának egy virtuális fürt létrehozását igénylő frissítése esetén további 6 + 5 = 11 IP-címmel kell rendelkeznie. Mivel már a 32-es címek közül 22-et használ, a művelethez nem tartoznak elérhető címek. Ezért le kell foglalni az alhálózatot a/26 alhálózati maszkkal (64 cím).

**2. példa**: azt tervezi, hogy három általános célú (Gen5 hardver) és két üzleti szempontból kritikus felügyelt példányt (Gen5 hardver) helyez el ugyanahhoz az alhálózathoz. Ez azt jelenti, hogy 5 + 6 + 3 * 3 + 2 * 5 = 30 IP-címet kell használnia. Ezért le kell foglalni az alhálózatot a/26 alhálózati maszkkal. Ha a/27 alhálózati maszkot választja, a megmaradt címek száma 2 (32 – 30) lesz, ami megakadályozza az összes példány frissítési műveleteit, mivel további címek szükségesek az alhálózatban a példány skálázásának végrehajtásához.

**3. példa**: azt tervezi, hogy egy általános célú felügyelt példányt (Gen5 hardvert) használ, és időről időre végrehajtja a virtuális mag frissítési műveletét. Ez azt jelenti, hogy 5 + 6 + 1 * 3 + 3 = 17 IP-címet kell használnia. Mivel az IP-címtartományok a 2. Powerben vannak meghatározva, a 32-es (2 ^ 5) IP-címekre vonatkozó IP-címtartomány szükséges. Ezért le kell foglalni az alhálózatot a/27 alhálózati maszkkal.

### <a name="address-requirements-for-update-scenarios"></a>Frissítési forgatókönyvek címeire vonatkozó követelmények

A skálázási művelet példányaiban átmenetileg további IP-kapacitásra van szükség, amely az árképzési szinttől és a hardverek létrehozástól függ.

| **Hardveres Gen** | **Tarifacsomag** | **Forgatókönyv** | **További címek*** |
| --- | --- | --- | --- |
| Gen4 | GP vagy BC | Virtuális mag skálázása | 5 |
| Gen4 | GP vagy BC | Tárterület skálázása | 5 |
| Gen4 | GP vagy BC | Váltás a GP-ről a BC vagy a BC rendszerre a GP-re | 5 |
| Gen4 | GP | Váltás a Gen5 * | 9 |
| Gen4 | BC | Váltás a Gen5 * | 11 |
| Gen5 | GP | Virtuális mag skálázása | 3 |
| Gen5 | GP | Tárterület skálázása | 0 |
| Gen5 | GP | Váltás a BC-re | 5 |
| Gen5 | BC | Virtuális mag skálázása | 5 |
| Gen5 | BC | Tárterület skálázása | 5 |
| Gen5 | BC | Váltás a GP-re | 3 |

  \* A Gen4 hardverek fokozatos kiépítése folyamatban van, és már nem érhető el az új üzemelő példányokhoz. Frissítse a Gen4-ről a Gen5-re vonatkozó hardver-előállítást, hogy kihasználhassa a Gen5-hardverek létrehozásával kapcsolatos képességeket.

## <a name="next-steps"></a>Következő lépések

- Az áttekintést lásd: [Mi az az Azure SQL felügyelt példánya?](sql-managed-instance-paas-overview.md).
- További információ az [SQL felügyelt példányának kapcsolati architektúráról](connectivity-architecture-overview.md).
- Tekintse meg, hogyan [hozhat létre olyan VNet, ahol telepíteni fogja az SQL felügyelt példányát](virtual-network-subnet-create-arm-template.md).
- DNS-problémák esetén tekintse meg az [Egyéni DNS konfigurálása](custom-dns-configure.md)című témakört.
