---
title: Feladat-visszavételi hibák elhárítása VMware virtuális gépen vész-helyreállítás Azure Site Recovery
description: Ez a cikk bemutatja, hogyan lehet elhárítani a feladat-visszavétel és az ismételt védelem hibáit a VMware virtuális gépek vész-helyreállítás közben az Azure-ba Azure Site Recovery használatával
author: Sharmistha-Rai
manager: gaggupta
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: sharrai
ms.openlocfilehash: ed4e52470264441a99c5ccf0a736bb00233510c1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "87423116"
---
# <a name="troubleshoot-failback-to-on-premises-from-azure"></a>Azure-ból helyszíni rendszerbe történő feladat-visszavétel hibaelhárítása

Ez a cikk az Azure-beli virtuális gépeknek a helyszíni VMware-infrastruktúrába való feladatátvétele után az Azure-ba való feladatátvételt követően a [Azure site Recovery](site-recovery-overview.md)használatával kapcsolatos hibák elhárítását ismerteti.

A feladat-visszavétel lényegében két fő lépést foglal magában. Az első lépésben a feladatátvételt követően újra kell védelemmel ellátnia az Azure-beli virtuális gépeket a helyszíni környezetbe, hogy azok replikációt indítsanak. A második lépés egy feladatátvétel futtatása az Azure-ból a helyszíni helyre való visszatéréshez.

## <a name="common-issues"></a>Gyakori problémák

- Ha csak olvasási jogosultsággal rendelkező felhasználói vCenter-felderítést végez, és a virtuális gépek védelme, a védelem sikeres és a feladatátvétel működik. Az ismételt védelem során a feladatátvétel meghiúsul, mert nem lehet felderíteni az adattárolókat. A tünet az, hogy az adattárolók nem jelennek meg az ismételt védelem során. A probléma megoldásához frissítse a vCenter hitelesítő adatait egy olyan fiókkal, amely rendelkezik engedélyekkel, majd próbálja megismételni a feladatot.
- Ha egy linuxos virtuális gépet hajt végre, és a helyszínen futtatja azt, láthatja, hogy a Network Manager-csomag el lett távolítva a gépről. Ez az Eltávolítás azért fordul elő, mert a Network Manager-csomag el lesz távolítva a virtuális gép Azure-ban való helyreállításakor.
- Ha egy Linux rendszerű virtuális gép statikus IP-címmel van konfigurálva, és a feladatátvétel az Azure-ba történik, az IP-cím beszerzése a DHCP-ből történik. Ha a feladatátvételt a helyszíni rendszerre végzi, a virtuális gép továbbra is DHCP-t használ az IP-cím megvásárlásához. Manuálisan jelentkezzen be a gépre, és szükség esetén állítsa vissza az IP-címet statikusra. A Windows rendszerű virtuális gépek újra megvásárolhatják statikus IP-címét.
- Ha az ESXi 5,5 Free Edition vagy a vSphere 6 hypervisor Free Edition verziót használja, a feladatátvétel sikeres lesz, de a feladat-visszavétel nem sikerül. A feladat-visszavétel engedélyezéséhez frissítsen a program próbaverziós licencére.
- Ha nem tudja elérni a konfigurációs kiszolgálót a folyamat-kiszolgálóról, a Telnet használatával ellenőrizze a 443-es porton futó konfigurációs kiszolgálóval létesített kapcsolatot. A konfigurációs kiszolgálót a Process Serverről is megpróbálhatja pingelni. A folyamat kiszolgálójának szívverése is lehet, ha a konfigurációs kiszolgálóhoz csatlakozik.
- A fizikai helyszíni kiszolgálóként védett Windows Server 2008 R2 SP1-kiszolgálót nem lehet visszaadni az Azure-ból a helyszíni helyre.
- A következő esetekben nem végezhető el a feladat-visszavétel:
    - A gépeket áttelepítette az Azure-ba. 
    - Áthelyezett egy virtuális gépet egy másik erőforráscsoporthoz.
    - Törölte az Azure-beli virtuális gépet.
    - Letiltotta a virtuális gép védelmét.
    - A virtuális gépet manuálisan hozta létre az Azure-ban. Az ismételt védelem előtt a gépet először a helyszínen, a feladatátvételt pedig az Azure-ba kellett volna védeni.
    - Csak egy ESXi-gazdagépre lehet sikertelen. A VMware virtuális gépeket és fizikai kiszolgálókat nem lehet feladat-visszavétel Hyper-V-gazdagépekre, fizikai gépekre vagy VMware-munkaállomásokra.


## <a name="troubleshoot-reprotection-errors"></a>Ismételt védelmi hibák elhárítása

Ez a szakasz a gyakori ismételt védelmi hibákat és azok javítását ismerteti.

### <a name="error-code-95226"></a>95226-es hibakód

**Az ismételt védelem sikertelen volt, mert az Azure-beli virtuális gép nem tudta elérni a helyszíni konfigurációs kiszolgálót.**

Ez a hiba a következő esetekben fordul elő:

* Az Azure-beli virtuális gép nem tudja elérni a helyszíni konfigurációs kiszolgálót. A virtuális gép nem deríthető fel és nem regisztrálható a konfigurációs kiszolgálón.
* Az inmage Scout Application szolgáltatás nem fut az Azure-beli virtuális gépen a feladatátvételt követően. A szolgáltatás a helyszíni konfigurációs kiszolgálóval folytatott kommunikációhoz szükséges.

A probléma megoldása:

* Győződjön meg arról, hogy az Azure VM-hálózat lehetővé teszi, hogy az Azure-beli virtuális gép kommunikáljon a helyszíni konfigurációs kiszolgálóval. Létrehozhat egy helyek közötti VPN-t a helyszíni adatközponthoz, vagy konfigurálhat egy Azure-beli ExpressRoute-kapcsolatot az Azure-beli virtuális gép virtuális hálózatán található privát kapcsolattal.
* Ha a virtuális gép tud kommunikálni a helyszíni konfigurációs kiszolgálóval, jelentkezzen be a virtuális gépre. Ezután jelölje be az inmage Scout Application szolgáltatást. Ha úgy látja, hogy nem fut, indítsa el manuálisan a szolgáltatást. Győződjön meg arról, hogy a szolgáltatás indítási típusa **automatikus** értékre van beállítva.

### <a name="error-code-78052"></a>78052-es hibakód

**Nem sikerült befejezni a virtuális gép védelmét.**

Ez a probléma akkor fordulhat elő, ha már létezik egy azonos nevű virtuális gép a fő célkiszolgálón, amelyre a rendszer visszaküldi a feladatokat.

A probléma megoldása:

* Válasszon másik fő célkiszolgáló egy másik gazdagépen, hogy az ismételt védelem a gépet egy másik gazdagépen hozza létre, ahol a nevek nem ütköznek.
* A VMotion használatával is áthelyezheti a fő célt egy másik gazdagépre, ahol a név ütközése nem történik meg. Ha a meglévő virtuális gép egy elkóborolt számítógép, nevezze át úgy, hogy az új virtuális gép ugyanazon ESXi-gazdagépen is létrehozható.


### <a name="error-code-78093"></a>78093-es hibakód

**A virtuális gép nem fut, nem válaszol, vagy nem érhető el.**

A probléma megoldása:

A feladatátvételt végző virtuális gép újravédéséhez az Azure-beli virtuális gépnek futnia kell, hogy a mobilitási szolgáltatás regisztrálja magát a helyszíni konfigurációs kiszolgálóval, és a folyamat-kiszolgálóval folytatott kommunikációval megkezdheti a replikálást. Ha a gép helytelen hálózaton van, vagy nem fut (nem válaszol vagy leáll), a konfigurációs kiszolgáló nem tudja elérni a virtuális gépen a mobilitási szolgáltatást a védelem megkezdéséhez.

* Indítsa újra a virtuális gépet, hogy el tudja kezdeni a helyszíni kommunikációt.
* Indítsa újra a védelmi feladatot az Azure-beli virtuális gép elindítása után.

### <a name="error-code-8061"></a>8061-es hibakód

**Az adattár nem érhető el az ESXi-gazdagépről.**

A feladat-visszavételhez keresse meg a [fő cél előfeltételeit és a támogatott adattárakat](vmware-azure-prepare-failback.md#deploy-a-separate-master-target-server) .


## <a name="troubleshoot-failback-errors"></a>Feladat-visszavételi hibák elhárítása

Ez a szakasz a feladat-visszavétel során felmerülő gyakori hibákat ismerteti.

### <a name="error-code-8038"></a>8038-es hibakód

**A hiba miatt nem sikerült a helyszíni virtuális gép üzembe helyezése.**

Ez a probléma akkor fordul elő, ha a helyszíni virtuális gép olyan gazdagépen található, amelyen nincs elég memória kiépítve. 

A probléma megoldása:

* Több memória kiépítése az ESXi-gazdagépen.
* Emellett a VMotion segítségével áthelyezheti a virtuális gépet egy másik ESXi-gazdagépre, amely elegendő memóriával rendelkezik a virtuális gép elindításához.
