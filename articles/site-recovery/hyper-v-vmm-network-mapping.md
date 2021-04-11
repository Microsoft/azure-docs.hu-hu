---
title: A Hyper-V (VMM) hálózati leképezésének ismertetése Site Recovery
description: Ismerteti, hogyan lehet hálózati leképezést beállítani a VMM-felhőkben felügyelt Hyper-V virtuális gépek vész-helyreállítására az Azure-ba Azure Site Recovery használatával.
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/14/2019
ms.openlocfilehash: 98e6f9e7cb937551e111625845ba07d09482eff5
ms.sourcegitcommit: d63f15674f74d908f4017176f8eddf0283f3fac8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "106581162"
---
# <a name="prepare-network-mapping-for-hyper-v-vm-disaster-recovery-to-azure"></a>Hálózati leképezés előkészítése a Hyper-V virtuális gép vész-helyreállítására az Azure-ba


Ez a cikk segítséget nyújt a hálózati leképezés megismeréséhez és előkészítéséhez, ha System Center Virtual Machine Manager-(VMM-) felhőkben lévő Hyper-V virtuális gépeket az Azure-ba vagy egy másodlagos helyre replikálja a [Azure site Recovery](site-recovery-overview.md) szolgáltatás használatával.


## <a name="prepare-network-mapping-for-replication-to-azure"></a>Hálózati leképezés előkészítése az Azure-ba való replikáláshoz

Ha az Azure-ba replikálódik, a hálózati leképezési leképezések a forrás VMM-kiszolgálón lévő virtuálisgép-hálózatok és az Azure-beli virtuális hálózatok céljára vannak leképezve. A leképezés a következőket hajtja végre:
-  **Hálózati kapcsolat**– gondoskodik arról, hogy a replikált Azure-beli virtuális gépek a csatlakoztatott hálózathoz kapcsolódjanak. Az ugyanazon a hálózaton feladatátvételt okozó összes gép csatlakozhat egymáshoz, még akkor is, ha azok feladatátvétele különböző helyreállítási tervekben történt.
- **Hálózati átjáró**– ha hálózati átjáró van beállítva a cél Azure-hálózaton, a virtuális gépek csatlakozhatnak más helyszíni virtuális gépekhez.

A hálózati megfeleltetés a következőképpen működik:

- A forrás-VMM virtuálisgép-hálózatot egy Azure-beli virtuális hálózatra képezi le.
- Miután a forrásoldali hálózaton lévő feladatátvételi Azure-beli virtuális gépek csatlakozni fognak a leképezett célként megadott virtuális hálózathoz.
- A forrásként szolgáló virtuálisgép-hálózathoz hozzáadott új virtuális gépek a replikáláskor csatlakoznak a leképezett Azure-hálózathoz.
- Ha a célhálózatban már több alhálózat működik, és ezek egyikének ugyanaz a neve, mint annak, amelyen a forrás virtuális gép is található, a replika virtuális gép a feladatátvételt követően ehhez a cél alhálózathoz fog csatlakozni.
- Ha nem létezik egyező nevű alhálózat, a virtuális gép a hálózat első virtuális alhálózatához csatlakozik.

## <a name="prepare-network-mapping-for-replication-to-a-secondary-site"></a>Hálózati leképezés előkészítése másodlagos helyre történő replikáláshoz

Ha másodlagos helyre végzi a replikálást, a hálózati leképezések a forrás VMM-kiszolgálón lévő virtuálisgép-hálózatok és a virtuálisgép-hálózatok között a cél VMM-kiszolgálón vannak. A leképezés a következőket hajtja végre:

- **Hálózati kapcsolat**– a virtuális gépeket a megfelelő hálózatokhoz csatlakoztatja a feladatátvételt követően. A replika virtuális gép a forrás hálózatra leképezett célként megadott hálózathoz fog csatlakozni.
- **Optimális** virtuálisgép-elhelyezés – optimálisan helyezi el a replika virtuális gépeket a Hyper-V-gazdagép-kiszolgálókon. A replika virtuális gépek olyan gazdagépekre vannak elhelyezve, amelyek hozzáférnek a leképezett virtuálisgép-hálózatokhoz.
- **Nincs hálózati leképezés**– ha nem konfigurálja a hálózati leképezést, a replika virtuális gépek nem lesznek csatlakoztatva a feladatátvételt követően bármely virtuálisgép-hálózathoz.

A hálózati megfeleltetés a következőképpen működik:

- A hálózati leképezés két VMM-kiszolgálón vagy egyetlen VMM-kiszolgálón konfigurálható a virtuálisgép-hálózatok között, ha két helyet ugyanazon kiszolgáló felügyel.
- Ha a leképezés megfelelően van konfigurálva, és a replikáció engedélyezve van, az elsődleges helyen található virtuális gép csatlakoztatva lesz egy hálózathoz, és a célhelyen lévő replika csatlakoztatva lesz a csatlakoztatott hálózathoz.
- Amikor kijelöl egy célként megadott virtuálisgép-hálózatot a Site Recovery hálózati leképezés során, a forrásként szolgáló virtuálisgép-hálózatot használó VMM jelenik meg, valamint az elérhető célként megadott virtuálisgép-hálózatok a védelemhez használt cél virtuális gépeken.
- Ha a célként megadott hálózat több alhálózattal rendelkezik, és az egyik alhálózatnak ugyanaz a neve, mint annak az alhálózatnak, amelyen a forrás virtuális gép található, akkor a replika virtuális gép a feladatátvételt követően csatlakozik a célként megadott alhálózathoz. Ha nincs olyan célként megadott alhálózat, amelynek a neve megegyezik, a virtuális gép a hálózat első alhálózatához fog csatlakozni.

## <a name="example"></a>Példa

Az alábbi példa szemlélteti ezt a mechanizmust. Vessünk egy olyan szervezetet, amelyben két helyszín található a New York-i és Chicago-ban.

**Hely** | **VMM-kiszolgáló** | **Virtuálisgép-hálózatok** | **Leképezve erre**
---|---|---|---
New York | VMM-NewYork| VMNetwork1-NewYork | Leképezve VMNetwork1-Chicago
 |  | VMNetwork2-NewYork | Nincs leképezve
Chicago | VMM-Chicago| VMNetwork1-Chicago | Leképezve VMNetwork1-NewYork
 | | VMNetwork2-Chicago | Nincs leképezve

Ebben a példában:

- Ha a VMNetwork1-NewYork csatlakoztatott virtuális gépekhez hoz létre replika virtuális gépet, akkor az a VMNetwork1-Chicago-hoz csatlakozik.
- Ha VMNetwork2-NewYork vagy VMNetwork2-Chicago számára hoz létre replika virtuális gépet, az nem fog csatlakozni egyetlen hálózathoz sem.

A VMM-felhők beállítása a példánkban és a felhőkhöz társított logikai hálózatok között történik.

### <a name="cloud-protection-settings"></a>A Cloud Protection beállításai

**Védett felhő** | **A felhő védelme** | **Logikai hálózat (New York)**  
---|---|---
GoldCloud1 | GoldCloud2 |
SilverCloud1| SilverCloud2 |
GoldCloud2 | <p>NA</p><p></p> | <p>LogicalNetwork1-NewYork</p><p>LogicalNetwork1-Chicago</p>
SilverCloud2 | <p>NA</p><p></p> | <p>LogicalNetwork1-NewYork</p><p>LogicalNetwork1-Chicago</p>

### <a name="logical-and-vm-network-settings"></a>Logikai és virtuális gép hálózati beállításai

**Hely** | **Logikai hálózat** | **Társított virtuálisgép-hálózat**
---|---|---
New York | LogicalNetwork1-NewYork | VMNetwork1-NewYork
Chicago | LogicalNetwork1-Chicago | VMNetwork1-Chicago
 | LogicalNetwork2Chicago | VMNetwork2-Chicago

### <a name="target-network-settings"></a>Célként megadott hálózati beállítások

Ezen beállítások alapján a cél virtuálisgép-hálózat kiválasztásakor a következő táblázat a választható lehetőségeket tartalmazza.

**Kiválasztás** | **Védett felhő** | **A felhő védelme** | **Elérhető célként megadott hálózat**
---|---|---|---
VMNetwork1-Chicago | SilverCloud1 | SilverCloud2 | Elérhető
 | GoldCloud1 | GoldCloud2 | Elérhető
VMNetwork2-Chicago | SilverCloud1 | SilverCloud2 | Nem elérhető
 | GoldCloud1 | GoldCloud2 | Elérhető


Ha a célként megadott hálózat több alhálózattal rendelkezik, és az egyik alhálózatnak ugyanaz a neve, mint annak az alhálózatnak, amelyen a forrás virtuális gép található, akkor a replika virtuális gép a feladatátvételt követően a cél alhálózathoz fog csatlakozni. Ha nem létezik egyező nevű alhálózat, a virtuális gép a hálózat első virtuális gépéhez csatlakozik.


### <a name="failback-behavior"></a>Feladat-visszavétel viselkedése

Ha szeretné megtekinteni, hogy mi történik a feladat-visszavétel (visszirányú replikálás) esetén, tegyük fel, hogy VMNetwork1-NewYork a VMNetwork1-Chicago-ra van leképezve a következő beállításokkal.


**VM** | **Csatlakoztatva a VM-hálózathoz**
---|---
VM1 | VMNetwork1-Network
VM2 (VM1-replika) | VMNetwork1-Chicago

Ezekkel a beállításokkal vizsgáljuk meg, hogy mi történik néhány lehetséges forgatókönyvben.

**Forgatókönyv** | **Eredmény**
---|---
A feladatátvétel után a 2. virtuális gép hálózati tulajdonságaiban nincs változás. | A VM-1 továbbra is csatlakoztatva van a forrás hálózathoz.
A 2. virtuális gép hálózati tulajdonságai a feladatátvétel után módosulnak, és a kapcsolat megszakadt. | A VM-1 kapcsolat megszakadt.
A 2. virtuális gép hálózati tulajdonságai a feladatátvétel után módosulnak, és a VMNetwork2-Chicago-hoz csatlakoznak. | Ha VMNetwork2-Chicago nincs leképezve, a VM-1 kapcsolat le lesz választva.
A VMNetwork1-Chicago hálózati leképezése módosult. | A VM-1 a VMNetwork1-Chicago-hoz hozzárendelt hálózathoz lesz csatlakoztatva.



## <a name="next-steps"></a>Következő lépések

- [További](hyper-v-vmm-networking.md) információ IP-címzés feladatátvétel után másodlagos VMM-helyre.
- [További](concepts-on-premises-to-azure-networking.md) információ IP-címzés az Azure-ba történő feladatátvétel után.
