---
title: Azure Key Vault rendelkezésre állás és redundancia – Azure Key Vault | Microsoft Docs
description: További információ Azure Key Vault rendelkezésre állásról és redundanciával kapcsolatban.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 03/31/2021
ms.author: mbaldwin
ms.openlocfilehash: 3c5afc92044fcb109bedd38298b0b027ebeb437d
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107749689"
---
# <a name="azure-key-vault-availability-and-redundancy"></a>Az Azure Key Vault rendelkezésre állása és redundanciája

Azure Key Vault többrétegű redundanciával rendelkezik, így a kulcsok és titkos kulcsok akkor is elérhetők maradnak az alkalmazás számára, ha a szolgáltatás egyes összetevői meghiúsulnak.

> [!NOTE]
> Ez az útmutató a tárolókra vonatkozik. A felügyelt HSM-készletek eltérő magas rendelkezésre állási és vészhelyreállítási modellt használnak. További [információ: Managed HSM Disaster Recovery Guide (Felügyelt HSM vészhelyreállítási](../managed-hsm/disaster-recovery-guide.md) útmutató).

A kulcstartó tartalma a régión belül és egy másodlagos régióba replikálódik legalább 150 mérföld távolságra, de ugyanazon a földrajzi helyen belül a kulcsok és titkos kulcsok magas tartósságának fenntartása érdekében. Az egyes régiópárokkal kapcsolatos részletekért lásd: [Az Azure párosított régiói.](../../best-practices-availability-paired-regions.md) A párosított régiók modellje alól kivétel Dél-Brazília, amely csak a Dél-Brazília régióban található adatok tartását teszi lehetővé. Dél-Brazília zónaredundáns tárolást (ZRS) használ az adatok háromszori replikálása egyetlen helyen/régión belül. Prémium szintű AKV esetén a rendszer a 3 régióból csak 2-t használ az adatok HSM-ről való replikálása érdekében.  

Ha a Key Vault szolgáltatás egyes összetevői meghiúsulnak, a régión belüli alternatív összetevők a kérés kiszolgálásához lépnek be, hogy a működés ne romlása legyen. A folyamat elindítani nem kell semmilyen műveletet, ez automatikusan megtörténik, és átlátható lesz az Ön számára.

Abban a ritka esetben, ha egy teljes Azure-régió nem érhető el, az Azure Key Vault-ről az abban a régióban lekért kérések automatikusan egy másodlagos régióba vannak irányítva *(* a rendszer átveszi ) a dél-brazíliai régió kivételével. Ha az elsődleges régió ismét elérhetővé válik, a kérések *vissza* vannak irányítva (feladat-vissza) az elsődleges régióba. Itt sem kell semmilyen műveletet eltennünk, mert ez automatikusan megtörténik.

A dél-brazíliai régióban meg kell terveznie az Azure-kulcstartók helyreállítását egy régióhiba esetén. Az Azure Key Vault biztonsági mentéséhez és egy választott régióba való visszaállításához kövesse a biztonsági mentést Azure Key Vault [lépéseit.](backup.md) 

Ezzel a magas rendelkezésre állású kialakítással a Azure Key Vault nem igényel állásidőt a karbantartási tevékenységekhez.

Néhány figyelmeztetést figyelembe kell ni:

* Régió feladatátvétele esetén a szolgáltatás feladatátvétele eltarthat néhány percig. Előfordulhat, hogy a feladatátvétel előtt az ebben az időszakban lekért kérések meghiúsulnak.
* Ha privát hivatkozást használ a kulcstartóhoz való csatlakozáshoz, a kapcsolat feladatátvétel esetén akár 20 percig is eltarthat. 
* A feladatátvétel során a kulcstartó csak olvasható módban van. Az ebben a módban támogatott kérések a következőek:
  * Tanúsítványok list való használata
  * Tanúsítványok lekérte
  * Titkos kulcsok listázása
  * Titkos kulcsok lekérte
  * Kulcsok listába sorolva
  * Kulcsok lekért (tulajdonságai)
  * Titkosítás
  * Visszafejtés
  * Betakar
  * Kicsomagzás
  * Ellenőrzés
  * Előjel
  * Backup

* A feladatátvétel során nem lehet módosításokat eszközlni a kulcstartó tulajdonságain. Nem fogja tudni módosítani a hozzáférési szabályzatot, a tűzfal konfigurációit és beállításait.

* A feladatátvétel visszavétele után az összes  kérelemtípus (beleértve az olvasási és írási kéréseket is) elérhetővé válik.
