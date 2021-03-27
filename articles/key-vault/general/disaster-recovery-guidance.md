---
title: Azure Key Vault rendelkezésre állás és redundancia – Azure Key Vault | Microsoft Docs
description: További információ a Azure Key Vault rendelkezésre állásáról és a redundanciáról.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/28/2020
ms.author: sudbalas
ms.openlocfilehash: 27184e267bb0472dad6fc9176dfdeee68d5eae58
ms.sourcegitcommit: c94e282a08fcaa36c4e498771b6004f0bfe8fb70
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105611820"
---
# <a name="azure-key-vault-availability-and-redundancy"></a>Az Azure Key Vault rendelkezésre állása és redundanciája

Azure Key Vault több réteget is tartalmaz, így biztosíthatja, hogy a kulcsok és a titkos kódok továbbra is elérhetők legyenek az alkalmazás számára, még akkor is, ha a szolgáltatás egyes összetevői meghibásodnak.

> [!NOTE]
> Ez az útmutató a tárolók esetében érvényes. A felügyelt HSM-készletek eltérő magas rendelkezésre állású és vész-helyreállítási modellt használnak. További információkért lásd: [felügyelt HSM vész-helyreállítási útmutató](../managed-hsm/disaster-recovery-guide.md) .

A Key Vault tartalmát a régión belül, illetve legalább 150 mérfölddel arrébb egy másodlagos régióba replikálja a rendszer, de ugyanazon a földrajzon belül a kulcsok és titkok magas tartósságának fenntartása érdekében. Az adott régió párokkal kapcsolatos részletekért lásd: [Azure párosított régiók](../../best-practices-availability-paired-regions.md). A párosított régiók modelljének kivétele Brazília déli régiója, amely csak a Dél-Brazíliában található tárolt adatmegőrzési lehetőséget teszi lehetővé. Dél-Brazília a Zone redundáns Storage (ZRS) használatával háromszor replikálja az adatait az adott helyen/régióban. A AKV Premium esetében a HSM-ből származó adatok replikálásához a 3 régió közül csak 2 van használatban.  

Ha a Key Vault szolgáltatásban lévő egyes összetevők meghibásodnak, a régión belüli alternatív összetevők, hogy kiszolgálják a kérést, hogy a rendszer ne csökkentse a funkciók romlását. A folyamat elindításához semmilyen műveletet nem kell végrehajtania, automatikusan megtörténik, és transzparens lesz.

Abban a ritka esetben, ha egy teljes Azure-régió nem érhető el, az adott régióban Azure Key Vault a kérelmeket a rendszer automatikusan átirányítja egy másodlagos régióba, kivéve a *Dél-brazíliai* régió esetében. Ha az elsődleges régió újra elérhetővé válik, a rendszer visszairányítja a kérelmeket az elsődleges régióhoz (a *sikertelen visszaállítást*). Többé nem kell semmit tennie, mert ez automatikusan megtörténik.

A Dél-brazíliai régióban meg kell terveznie az Azure Key vaultok helyreállítását egy régió meghibásodása esetén. Az Azure Key Vault biztonsági mentéséhez és visszaállításához válasszon egy tetszőleges régiót, majd hajtsa végre a [Azure Key Vault biztonsági mentésben](backup.md)részletesen ismertetett lépéseket. 

A magas rendelkezésre állás kialakításán keresztül a Azure Key Vault nem igényel állásidőt karbantartási tevékenységekhez.

A következőkkel kapcsolatos figyelmeztetéseket kell figyelembe venni:

* Régió feladatátvétele esetén eltarthat néhány percig, amíg a szolgáltatás feladatátvételt végez. A feladatátvétel meghiúsulása előtt végrehajtott kérelmek.
* Ha privát hivatkozást használ a kulcstartóhoz való csatlakozáshoz, akár 20 percet is igénybe vehet, amíg a kapcsolat újra létre nem jön egy feladatátvétel esetén. 
* A feladatátvétel során a kulcstartó írásvédett módban van. Az ebben a módban támogatott kérelmek a következők:
  * Tanúsítványok listázása
  * Tanúsítványok beolvasása
  * Titkos kulcsok listázása
  * Titkok beolvasása
  * Kulcsok listázása
  * Kulcsok beolvasása (tulajdonságai)
  * Titkosítás
  * Visszafejtés
  * Wrap
  * Kicsomagolása
  * Ellenőrzés
  * Előjel
  * Backup

* A feladatátvétel során a Key Vault tulajdonságai nem módosíthatók. Nem fogja tudni módosítani a hozzáférési házirendet, illetve a tűzfal konfigurációit és beállításait.

* A feladatátvétel meghiúsulása után az összes kérelem típusa (beleértve az olvasási *és* írási kérelmeket is) elérhető.
