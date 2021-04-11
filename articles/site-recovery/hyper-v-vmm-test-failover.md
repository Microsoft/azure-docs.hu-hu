---
title: NHyper-V vész-helyreállítási gyakorlat futtatása másodlagos helyre Azure Site Recovery
description: Ismerje meg, hogyan futtathat VMM-felhőkben futó Hyper-V virtuális gépekre vonatkozó DR-részletezést egy másodlagos helyszíni adatközpontban Azure Site Recovery használatával.
author: sideeksh
manager: gaggupta
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: sideeksh
ms.openlocfilehash: 675190bee674c745494c2378712afa89bd43cbe4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105727875"
---
# <a name="run-a-dr-drill-for-hyper-v-vms-to-a-secondary-site"></a>A Hyper-V virtuális gépekhez tartozó DR-részletezés futtatása másodlagos helyre


Ez a cikk a (z) System Center Virtual Machine Manager V (PP) felhőkben felügyelt Hyper-V virtuális gépek vész-helyreállítási (DR) gyakorlatát ismerteti, [Azure site Recovery](site-recovery-overview.md)használatával.

A replikációs stratégia ellenőrzéséhez futtasson feladatátvételi tesztet, és végezzen el egy DR-részletezést adatvesztés vagy állásidő nélkül. A feladatátvételi teszt nem befolyásolja a folyamatos replikálást, vagy az éles környezetben. 

## <a name="how-do-test-failovers-work"></a>Hogyan működnek a feladatátvételi teszt?

A feladatátvételi tesztet az elsődleges helyről a másodlagos helyre futtathatja. Ha egyszerűen szeretné ellenőrizni, hogy a virtuális gép feladatátvételt végez-e, futtathatja a feladatátvételi tesztet anélkül, hogy bármit kellene beállítania a másodlagos helyen. Ha ellenőrizni szeretné, hogy az alkalmazás feladatátvétele a várt módon működik-e, a másodlagos helyen kell beállítania a hálózatkezelést és az infrastruktúrát.
- Feladatátvételi tesztet futtathat egyetlen virtuális gépen vagy egy [helyreállítási tervben](site-recovery-create-recovery-plans.md)is.
- A feladatátvételi tesztet hálózat nélkül, meglévő hálózattal vagy automatikusan létrehozott hálózattal is futtathatja. Ezekről a lehetőségekről további részleteket az alábbi táblázatban talál.
    - A feladatátvételi tesztet hálózat nélkül is futtathatja. Ez a beállítás akkor hasznos, ha egyszerűen ellenőrizni szeretné, hogy a virtuális gép képes-e átvenni a feladatátvételt, de nem fogja tudni ellenőrizni a hálózati konfigurációt.
    - Futtassa a feladatátvételt egy meglévő hálózattal. Javasoljuk, hogy ne használjon éles hálózatot.
    - Futtassa a feladatátvételt, és Site Recovery automatikusan hozzon létre egy tesztelési hálózatot. Ebben az esetben a Site Recovery automatikusan létrehozza a hálózatot, és törli a feladatátvételi teszt befejezésekor.
- Ki kell választania egy helyreállítási pontot a feladatátvételi teszthez: 
    - **Legutóbb feldolgozott**: Ez a lehetőség a virtuális gép feladatátvételét a site Recovery által feldolgozott legutóbbi helyreállítási pontra hajtja végre. Ez a lehetőség alacsony helyreállítási időre vonatkozó célkitűzést (RTO) nyújt, mert a rendszer nem tölt időt a feldolgozatlan adatok feldolgozásával.
    - **Legújabb alkalmazás-konzisztens**: Ez a beállítás a virtuális gép feladatátvételét a site Recovery által feldolgozott legújabb, alkalmazás-konzisztens helyreállítási pontra hajtja végre. 
    - **Legújabb**: Ez a lehetőség először feldolgozza a site Recovery szolgáltatásnak elküldett összes adatát, hogy minden virtuális gép számára létrehozzon egy helyreállítási pontot, mielőtt a művelet elvégezte a feladatátvételt. Ez a beállítás a legalacsonyabb RPO (helyreállítási pont célkitűzés) adja meg, mert a feladatátvételt követően létrehozott virtuális gép minden olyan adattal rendelkezik, amelyet a rendszer a feladatátvétel elindításakor Site Recovery replikál.
    - **Legújabb több virtuális gépre feldolgozva**: olyan helyreállítási tervekhez érhető el, amelyek egy vagy több, több virtuális gépre kiterjedő konzisztenciát használó virtuális gépet tartalmaznak. Azok a virtuális gépek, amelyeken engedélyezve van a feladatátvétel, a legújabb közös, több virtuális gépre kiterjedő konzisztens helyreállítási pontot adják át. Más virtuális gépek feladatátvétele a legutóbb feldolgozott helyreállítási pontra történik.
    - **Legújabb több virtuális gépre kiterjedő alkalmazás – konzisztens**: Ez a beállítás olyan helyreállítási tervekhez érhető el, amelyeken engedélyezve van egy vagy több virtuális gépre kiterjedő konzisztencia. A replikációs csoport részét képező virtuális gépek a legújabb közös, több virtuális gépre kiterjedő, alkalmazás-konzisztens helyreállítási pontot adják át. Más virtuális gépek feladatátvétele a legújabb, alkalmazás-konzisztens helyreállítási ponttal történik.
    - **Egyéni**: Ha ezt a lehetőséget választja, az adott virtuális gép egy adott helyreállítási pontra történő feladatátvételét hajthatja végre.



## <a name="prepare-networking"></a>Hálózatkezelés előkészítése

A feladatátvételi teszt futtatásakor a rendszer arra kéri, hogy válassza ki a hálózati beállításokat a tesztelési replika-gépek számára a táblázatban foglaltak szerint.

| **Beállítás** | **Részletek** |
| --- | --- |
| **Nincs** | A teszt virtuális gép azon a gazdagépen jön létre, amelyen a replika virtuális gép található. Nincs hozzáadva a felhőhöz, és nincs csatlakoztatva egyetlen hálózathoz sem.<br/><br/> A gépet a létrehozása után a virtuálisgép-hálózathoz is összekapcsolhatjuk.|
| **Meglévő használata** | A teszt virtuális gép azon a gazdagépen jön létre, amelyen a replika virtuális gép található. Nincs hozzáadva a felhőhöz.<br/><br/>Hozzon létre egy, az éles hálózattól elkülönített virtuálisgép-hálózatot.<br/><br/>Ha VLAN-alapú hálózatot használ, javasoljuk, hogy hozzon létre egy külön logikai hálózatot (éles környezetben nem használt) a VMM erre a célra. Ezzel a logikai hálózattal virtuálisgép-hálózatokat lehet létrehozni a feladatátvételi tesztekhez.<br/><br/>A logikai hálózatot társítani kell legalább egy, a virtuális gépeket üzemeltető Hyper-V-kiszolgáló hálózati adapteréhez.<br/><br/>A VLAN logikai hálózatok esetében a logikai hálózathoz hozzáadott hálózati helyeket el kell különíteni.<br/><br/>Ha Windowsos hálózati virtualizálási alapú logikai hálózatot használ, a Azure Site Recovery automatikusan elkülönített virtuálisgép-hálózatokat hoz létre. |
| **Hálózat létrehozása** | A rendszer automatikusan létrehoz egy ideiglenes tesztelési hálózatot a **logikai hálózatban** és a kapcsolódó hálózati telephelyeken megadott beállítás alapján.<br/><br/> A feladatátvétel ellenőrzi, hogy a virtuális gépek létrejöttek-e.<br/><br/> Akkor használja ezt a beállítást, ha a helyreállítási terv egynél több virtuálisgép-hálózatot használ.<br/><br/> Ha Windows-alapú hálózati virtualizálási hálózatokat használ, ez a beállítás automatikusan képes virtuális gépeket létrehozni ugyanazokkal a beállításokkal (alhálózatokkal és IP-címkészlet) a replika virtuális gép hálózatában. Ezek a virtuálisgép-hálózatok a feladatátvételi teszt befejeződése után automatikusan törlődnek.<br/><br/> A teszt virtuális gép azon a gazdagépen jön létre, amelyen a replika virtuális gép létezik. Nincs hozzáadva a felhőhöz.|

### <a name="best-practices"></a>Ajánlott eljárások

- Az üzemi hálózat tesztelése az üzemi számítási feladatokhoz való állásidőt okoz. Kérje meg a felhasználókat, hogy ne használják a kapcsolódó alkalmazásokat, amikor a vész-helyreállítási gyakorlat folyamatban van.

- A tesztelési hálózatnak nem kell megegyeznie a feladatátvételi teszthez használt VMM logikai hálózat típusával. Néhány kombináció azonban nem működik:

     - Ha a replika DHCP-és VLAN-alapú elkülönítést használ, a replikához tartozó virtuálisgép-hálózatnak nincs szüksége statikus IP-címkészlet használatára. A feladatátvételi teszthez használt Windows-hálózati virtualizálás nem fog működni, mert nem érhetők el címkészlet. 
        
     - A feladatátvételi teszt nem fog működni, ha a replika hálózat nem használ elkülönítést, és a teszt hálózat Windowsos hálózati virtualizálás használatával működik. Ennek az az oka, hogy a nem izolált hálózat nem rendelkezik a Windows hálózati virtualizálási hálózat létrehozásához szükséges alhálózatokkal.
        
- Azt javasoljuk, hogy ne használja a hálózati leképezéshez kiválasztott hálózatot a feladatátvételi teszthez.

- A replika virtuális gépek csatlakoztatása a leképezett virtuálisgép-hálózatokhoz a feladatátvételt követően, attól függ, hogy a VM-hálózat hogyan van konfigurálva a VMM-konzolon.


### <a name="vm-network-configured-with-no-isolation-or-vlan-isolation"></a>Elkülönítés vagy VLAN elkülönítés nélkül konfigurált virtuálisgép-hálózat

Ha a virtuálisgép-hálózat elkülönítés vagy VLAN-elkülönítés nélkül van konfigurálva a VMM, vegye figyelembe a következőket:

- Ha a virtuálisgép-hálózathoz a DHCP van meghatározva, a replika virtuális gép a társított logikai hálózat hálózati helyén megadott beállításokon keresztül csatlakozik a VLAN-AZONOSÍTÓhoz. A virtuális gép megkapja az IP-címét az elérhető DHCP-kiszolgálóról.
- Nem kell statikus IP-címkészletet definiálnia a cél virtuálisgép-hálózathoz. Ha a virtuálisgép-hálózathoz statikus IP-címkészlet van használatban, a replika virtuális gép a társított logikai hálózat hálózati helyén megadott beállításokon keresztül csatlakozik a VLAN-AZONOSÍTÓhoz.
- A virtuális gép megkapja az IP-címét a virtuálisgép-hálózathoz meghatározott készletből. Ha nincs definiálva statikus IP-címkészlet a cél virtuálisgép-hálózaton, az IP-címek lefoglalása sikertelen lesz. Hozza létre az IP-címkészletet mind a forrás-, mind a cél VMM-kiszolgálón, amelyet a védelemhez és a helyreállításhoz fog használni.

### <a name="vm-network-with-windows-network-virtualization"></a>Virtuálisgép-hálózat Windows hálózati virtualizálási szolgáltatással

Ha egy virtuálisgép-hálózat a VMM-ben van konfigurálva a Windows-alapú hálózati virtualizálási szolgáltatással, vegye figyelembe a következőket:

- Statikus készletet kell definiálni a cél virtuálisgép-hálózathoz, függetlenül attól, hogy a forrásoldali virtuálisgép-hálózat DHCP vagy statikus IP-címkészlet használatára van-e konfigurálva. 
- Ha DHCP-t határoz meg, a célként megadott VMM-kiszolgáló DHCP-kiszolgálóként működik, és a célként megadott virtuálisgép-hálózathoz definiált készletből IP-címet biztosít.
- Ha a forráskiszolgáló statikus IP-címkészletet használ, a célként megadott VMM-kiszolgáló IP-címet foglal le a készletből. Az IP-címek kiosztása mindkét esetben sikertelen lesz, ha nincs definiálva statikus IP-címkészlet.



## <a name="prepare-the-infrastructure"></a>Az infrastruktúra előkészítése

Ha egyszerűen ellenőrizni szeretné, hogy a virtuális gép feladatátvételt hajt végre, infrastruktúra nélkül is futtathat feladatátvételi tesztet. Ha teljes körű DR-részletezést szeretne végezni az alkalmazások feladatátvételének teszteléséhez, elő kell készítenie az infrastruktúrát a másodlagos helyen:

- Ha egy meglévő hálózat használatával futtatja a feladatátvételi tesztet, Active Directory, DHCP-t és DNS-t kell előkészítenie a hálózaton.
- Ha feladatátvételi tesztet futtat egy virtuálisgép-hálózat automatikus létrehozásával, akkor a feladatátvételi teszt futtatása előtt infrastruktúra-erőforrásokat kell hozzáadnia az automatikusan létrehozott hálózathoz. Egy helyreállítási tervben ezt úgy teheti meg, ha egy manuális lépést ad hozzá a helyreállítási tervben szereplő 1. csoporthoz, amelyet a feladatátvételi teszthez használni fog. Ezután adja hozzá az infrastruktúra-erőforrásokat az automatikusan létrehozott hálózathoz a feladatátvételi teszt futtatása előtt.


### <a name="prepare-dhcp"></a>DHCP előkészítése
Ha a feladatátvételi tesztben részt vevő virtuális gépek DHCP-t használnak, hozzon létre egy teszt DHCP-kiszolgálót az elkülönített hálózaton belül a feladatátvételi teszt érdekében.


### <a name="prepare-active-directory"></a>Active Directory előkészítése
Ha feladatátvételi tesztet szeretne futtatni az alkalmazás teszteléséhez, szüksége lesz az üzemi Active Directory környezet egy példányára a tesztkörnyezetben. További információkért tekintse át a [Active Directory feladatátvételi teszttel kapcsolatos](site-recovery-active-directory.md#test-failover-considerations)tudnivalókat.

### <a name="prepare-dns"></a>DNS előkészítése
Készítse elő a DNS-kiszolgálót a feladatátvételi teszthez a következőképpen:

* **DHCP**: Ha a virtuális gépek DHCP-t használnak, a teszt DNS IP-címét frissíteni kell a teszt DHCP-kiszolgálón. Ha a Windows hálózati virtualizálás hálózati típusát használja, a VMM-kiszolgáló DHCP-kiszolgálóként működik. Ezért a DNS IP-címét frissíteni kell a feladatátvételi teszt hálózaton. Ebben az esetben a virtuális gépek regisztrálják magukat a megfelelő DNS-kiszolgálón.
* **Statikus cím**: Ha a virtuális gépek statikus IP-címet használnak, a teszt DNS-kiszolgáló IP-címét frissíteni kell a feladatátvételi teszt hálózatban. Előfordulhat, hogy frissítenie kell a DNS-t a teszt virtuális gépek IP-címével. Erre a célra a következő minta parancsfájlt használhatja:

  ```powershell
  Param(
  [string]$Zone,
  [string]$name,
  [string]$IP
  )
  $Record = Get-DnsServerResourceRecord -ZoneName $zone -Name $name
  $newrecord = $record.clone()
  $newrecord.RecordData[0].IPv4Address  =  $IP
  Set-DnsServerResourceRecord -zonename $zone -OldInputObject $record -NewInputObject $Newrecord
  ```

## <a name="run-a-test-failover"></a>Feladatátvételi teszt futtatása

Ez az eljárás azt ismerteti, hogyan futtathat feladatátvételi tesztet helyreállítási tervhez. Azt is megteheti, hogy a feladatátvételt egyetlen virtuális géphez futtatja a **Virtual Machines** lapon.

1. Válassza a **helyreállítási tervek**  >  *recoveryplan_name* elemet. Kattintson a **feladatátvételi**  >  **teszt feladatátvétele** lehetőségre.
2. A **feladatátvételi teszt** panelen határozza meg, hogy a replika virtuális gépek hogyan kapcsolódjanak a hálózatokhoz a feladatátvételi teszt után.
3. A **feladatok** lapon nyomon követheti a feladatátvétel folyamatát.
4. A feladatátvétel befejeződése után ellenőrizze, hogy a virtuális gépek sikeresen elindulnak-e.
5. Ha elkészült, kattintson a helyreállítási terv **feladatátvételi teszt törlése** elemére. A **jegyzetek** területen jegyezze fel és mentse a feladatátvételi teszttel kapcsolatos megfigyeléseket. Ez a lépés törli azokat a virtuális gépeket és hálózatokat, amelyeket Site Recovery hozott létre a feladatátvételi teszt során. 

![Feladatátvétel tesztelése](./media/hyper-v-vmm-test-failover/TestFailover.png)
 


> [!TIP]
> A feladatátvételi teszt során egy virtuális géphez megadott IP-cím ugyanaz az IP-cím, amelyet a virtuális gép a tervezett vagy nem tervezett feladatátvételhez is kap (feltételezve, hogy az IP-cím elérhető a feladatátvételi teszt hálózaton). Ha ugyanaz az IP-cím nem érhető el a feladatátvételi teszt hálózaton, a virtuális gép egy másik, a feladatátvételi tesztben elérhető IP-címet kap.



### <a name="run-a-test-failover-to-a-production-network"></a>Feladatátvételi teszt futtatása üzemi hálózatra

Azt javasoljuk, hogy ne futtasson feladatátvételi tesztet a hálózati leképezés során megadott üzemi helyreállítási hely hálózatán. Ha azonban szükség van a végpontok közötti hálózati kapcsolat érvényesítésére egy feladatátvételi virtuális gépen, vegye figyelembe a következő szempontokat:

* Ellenőrizze, hogy az elsődleges virtuális gép le van-e állítva a feladatátvételi teszt végrehajtásakor. Ha nem, két azonos identitású virtuális gép fog futni ugyanabban a hálózatban egyszerre. Ez a helyzet nem kívánt következményekhez vezethet.
* A feladatátvételi teszt virtuális gépeken végrehajtott módosítások elvesznek, amikor törli a feladatátvételi tesztet futtató virtuális gépeket. Ezek a módosítások nem replikálódnak vissza az elsődleges virtuális gépekre.
* A teszteléshez hasonlóan az éles környezetbeli alkalmazás leállásához vezet. Kérje meg az alkalmazás felhasználóit, hogy ne használják az alkalmazást, amikor a DR-részletezés folyamatban van.  


## <a name="next-steps"></a>Következő lépések
A DR-részletezés sikeres futtatása után [futtathatja a teljes feladatátvételt](site-recovery-failover.md).



