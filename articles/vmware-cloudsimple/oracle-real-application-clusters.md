---
title: Azure VMware-megoldás CloudSimple – optimalizálja saját CloudSimple az Oracle RAC-hoz
description: Leírja, hogyan helyezhet üzembe egy új fürtöt, és hogyan optimalizálhat egy virtuális gépet az Oracle Real Application Clusters (RAC) telepítéséhez és konfigurálásához
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3959aae5f490af10c6747cfa67d9960e0c4a203f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97899269"
---
# <a name="optimize-your-cloudsimple-private-cloud-for-installing-oracle-rac"></a>Optimalizálja CloudSimple saját Felhőjét az Oracle RAC telepítéséhez

Az Oracle Real Application Clusters (RAC) üzembe helyezése a CloudSimple saját felhőalapú környezetében végezhető el. Ez az útmutató ismerteti, hogyan helyezhet üzembe egy új fürtöt, és hogyan optimalizálhat egy virtuális gépet az Oracle RAC megoldáshoz. A témakör lépéseinek elvégzése után telepítheti és konfigurálhatja az Oracle RAC-t.

## <a name="storage-policy"></a>Tárolási házirend

Az Oracle RAC sikeres megvalósítása elegendő számú csomópontot igényel a fürtben.  A vSAN tárolási házirendjében az elviselni kívánt adatlemezek az adatbázis, a napló és a lemez-visszaállítási lemezek tárolására használt adatlemezekre vonatkoznak.  A hibák hatékony elvégzéséhez szükséges csomópontok száma 2N + 1, ahol N a tranzakciós adó értéke.

Példa: Ha a kívánt pénzügyi adó 2, akkor a fürtben lévő csomópontok teljes száma csak 2 * 2 + 1 = 5 lehet.

## <a name="overview-of-deployment"></a>Az üzembe helyezés áttekintése

Az alábbi szakaszok azt ismertetik, hogyan állíthatja be az CloudSimple saját felhőalapú környezetét az Oracle RAC-hoz.

1. Ajánlott eljárások a lemezek konfigurálásához
2. A CloudSimple Private Cloud vSphere-fürt üzembe helyezése
3. Az Oracle RAC hálózatkezelésének beállítása
4. VSAN-tárolási házirendek beállítása
5. Oracle-alapú virtuális gépek létrehozása és megosztott virtuálisgép-lemezek létrehozása
6. VIRTUÁLIS gépek közötti affinitási szabályok beállítása

## <a name="best-practices-for-disk-configuration"></a>Ajánlott eljárások a lemezek konfigurálásához

Az Oracle RAC virtuális gépek több lemezzel rendelkeznek, amelyek adott függvényhez használatosak.  A megosztott lemezek az Oracle RAC-fürt által használt összes virtuális gépre vannak csatlakoztatva.  Az operációs rendszer és a szoftver telepítési lemezei csak az egyes virtuális gépekre vannak csatlakoztatva.  

![Az Oracle RAC virtuálisgép-lemezek áttekintése](media/oracle-vm-disks-overview.png)

A következő példa az alábbi táblázatban definiált lemezeket használja.

| Lemez                                      | Cél                                       | Megosztott lemez |
|-------------------------------------------|-----------------------------------------------|-------------|
| Operációs rendszer                                        | Operációsrendszer-lemez                         | No          |
| RÁCS                                      | Az Oracle Grid szoftver telepítési helye     | No          |
| ADATBÁZIS                                  | Az Oracle Database szoftver telepítési helye | No          |
| ORAHOME                                   | Az Oracle Database bináris fájljainak alaphelye    | No          |
| DATA1, DATA2, DATA3, DATA4                | Az Oracle-adatbázisfájlok tárolására szolgáló lemez   | Yes         |
| REDO1, REDO2, REDO3, REDO4, REDO5, REDO6  | Naplófájlok visszaállítása                                | Yes         |
| OCR1, OCR2, OCR3, OCR4, OCR5              | Szavazó lemezek                                  | Yes         |
| FRA1, FRA2                                | Gyors helyreállítási területek lemezei                      | Yes         |

![Oracle virtuális gép lemezének konfigurációja](media/oracle-vmdk.png)

### <a name="virtual-machine-configuration"></a>Virtuális gép konfigurációja

* Minden virtuális gép négy SCSI-vezérlővel van konfigurálva.
* Az SCSI-vezérlő típusa VMware paravirtual van beállítva.
* Több virtuális lemez (. VMDK) jön létre.
* A lemezek különböző SCSI-vezérlőkhöz vannak csatlakoztatva.
* A többszörös írók megosztási típusa a megosztott fürtözött lemezek esetében van beállítva.
* a vSAN tárolási házirendje a lemezek magas rendelkezésre állásának biztosítására van meghatározva.

### <a name="operating-system-and-software-disk-configuration"></a>Operációs rendszer és szoftver lemezének konfigurációja

Minden Oracle-virtuális gép több lemezzel van konfigurálva a gazda operációs rendszer, a swap, a Szoftvertelepítés és más operációsrendszer-funkciók számára.  Ezeket a lemezeket a rendszer nem osztja meg a virtuális gépek között.  

* Az egyes virtuális gépek három lemeze virtuális lemezként van konfigurálva, és az Oracle RAC virtuális gépekre van csatlakoztatva.
    * Operációsrendszer-lemez
    * Az Oracle Grid tárolására szolgáló lemez fájlok telepítése
    * Az Oracle Database telepítési fájljainak tárolására szolgáló lemez
* A lemezek dinamikusan **kiosztottként** konfigurálhatók.
* Az egyes lemezek az első SCSI-vezérlőre (SCSI0) vannak csatlakoztatva.  
* A megosztás beállítása **nem megosztás**.
* A redundancia a vSAN szabályzatok használatával van definiálva a tárolón.  

![Az Oracle RAC operációsrendszer-lemez fizikai konfigurációját bemutató diagram.](media/oracle-vm-os-disks.png)

### <a name="data-disk-configuration"></a>Adatlemez-konfiguráció

Az adatlemezeket elsődlegesen az adatbázisfájlok tárolására használják.  

* Négy lemez virtuális lemezként van konfigurálva, és minden Oracle RAC virtuális gépen csatlakoztatva van.
* Minden lemez egy másik SCSI-vezérlőre van csatlakoztatva.
* Minden virtuális lemez **sűrűn kiépítve, türelmetlenül** van konfigurálva.  
* A megosztás a **többszörös író** értékre van állítva.  
* A lemezeket automatikus Storage Management-(ASM-) lemezcsoportként kell konfigurálni.  
* A redundancia a vSAN szabályzatok használatával van definiálva a tárolón.  
* Az ASM-redundancia **külső** redundanciára van beállítva.

![Az Oracle RAC adatlemez-csoportjának konfigurációja](media/oracle-vm-data-disks.png)

### <a name="redo-log-disk-configuration"></a>A naplózási lemez konfigurációjának visszaállítása

A naplófájlok az adatbázison végrehajtott módosítások másolatának tárolására szolgálnak.  A naplófájlokat akkor használja a rendszer, amikor a hibák után vissza kell állítani az adathelyreállítást.

* A naplófájlok visszaállítását több lemezcsoport-csoportként kell konfigurálni.  
* A rendszer hat lemezt hoz létre és csatlakoztat az összes Oracle RAC virtuális géphez.
* A lemezek különböző SCSI-vezérlőkhöz vannak csatlakoztatva
* Minden virtuális lemez **sűrűn kiépítve, türelmetlenül** van konfigurálva.
* A megosztás a **többszörös író** értékre van állítva.  
* A lemezeket két ASM-lemez csoportként kell konfigurálni.
* Minden ASM-lemez csoport három lemezt tartalmaz, amelyek különböző SCSI-vezérlőkön találhatók.  
* Az ASM-redundancia **normál** redundanciára van beállítva.
* A rendszer öt visszaadott naplófájlt hoz létre az ASM-visszaadott naplófájlban

```
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
```

![Oracle RAC a naplófájlok csoportjának visszaállítása](media/oracle-vm-redo-log-disks.png)

### <a name="oracle-voting-disk-configuration"></a>Oracle-szavazási lemez konfigurációja

A szavazó lemezek további kommunikációs csatornaként biztosítják a kvórum lemez funkcióit, így elkerülhetők a megosztott agyi helyzetek.

* A rendszer öt lemezt hoz létre és csatlakoztat az összes Oracle RAC virtuális géphez.
* A lemezek egy SCSI-vezérlőhöz vannak csatlakoztatva
* Minden virtuális lemez **sűrűn kiépítve, türelmetlenül** van konfigurálva.
* A megosztás a **többszörös író** értékre van állítva.  
* A lemezeket ASM-lemez csoportként kell konfigurálni.  
* Az ASM-redundancia **magas** redundanciára van beállítva.

![Oracle-RAC szavazó lemez csoportjának konfigurációja](media/oracle-vm-voting-disks.png)

### <a name="oracle-fast-recovery-area-disk-configuration-optional"></a>Oracle gyors helyreállítási körzet – lemez konfigurációja (nem kötelező)

A gyors helyreállítási területet (FRA) az Oracle ASM-lemez csoport által kezelt fájlrendszer.  Az FRA megosztott tárolási helyet biztosít a biztonsági mentési és helyreállítási fájlok számára. Az Oracle archivált naplókat és flashback naplókat hoz létre a gyors helyreállítás területén. Az Oracle Recovery Manager (Oláh Anna) opcionálisan képes tárolni a biztonságimásolat-készleteket és a képmásolatokat a gyors helyreállítási területen, és a fájlok a média helyreállítása során történő visszaállításakor használja azt.

* A rendszer két lemezt hoz létre és csatlakoztat az összes Oracle RAC virtuális géphez.
* A lemezek különböző SCSI-vezérlőkhöz vannak csatlakoztatva
* Minden virtuális lemez **sűrűn kiépítve, türelmetlenül** van konfigurálva.
* A megosztás a **többszörös író** értékre van állítva.  
* A lemezeket ASM-lemez csoportként kell konfigurálni.  
* Az ASM-redundancia **külső** redundanciára van beállítva.

![Az Oracle RAC szavazási lemez csoportjának konfigurációját bemutató diagram.](media/oracle-vm-fra-disks.png)

## <a name="deploy-cloudsimple-private-cloud-vsphere-cluster"></a>A CloudSimple Private Cloud vSphere-fürt üzembe helyezése

A vSphere-fürt saját felhőben történő üzembe helyezéséhez kövesse az alábbi eljárást:

1. [Hozzon létre egy privát felhőt](create-private-cloud.md)a CloudSimple portálon. A CloudSimple létrehoz egy "cloudowner" nevű alapértelmezett vCenter-felhasználót az újonnan létrehozott privát felhőben. A saját felhőalapú felhasználói és engedélyezési modellel kapcsolatos részletekért tekintse [meg a privát felhőalapú engedélyezési modell megismerése](learn-private-cloud-permissions.md)című témakört.  Ez a lépés létrehozza az elsődleges felügyeleti fürtöt a saját felhőhöz.

2. A CloudSimple-portálon [bontsa ki a privát felhőt](expand-private-cloud.md) egy új fürttel.  Ezt a fürtöt fogja használni az Oracle RAC telepítéséhez.  Válassza ki a csomópontok számát a kívánt hibatűrés (legalább három csomópont) alapján.

## <a name="set-up-networking-for-oracle-rac"></a>Az Oracle RAC hálózatkezelésének beállítása

1. A saját felhőben [hozzon létre két VLAN](create-vlan-subnet.md)-t, egyet az Oracle nyilvános hálózatához, egyet pedig az Oracle-magánhálózat számára, és rendelje hozzá a megfelelő alhálózati CIDRs.
2. A VLAN-ok létrehozása után hozza létre az [elosztott portok csoportjait a saját felhő vCenter](create-vlan-subnet.md#use-vlan-information-to-set-up-a-distributed-port-group-in-vsphere).
3. Állítsa be a [DHCP-és DNS-kiszolgáló virtuális gépet](dns-dhcp-setup.md) a felügyeleti fürtön az Oracle-környezethez.
4. [Konfigurálja a DNS-továbbítást](on-premises-dns-setup.md#create-a-conditional-forwarder) a privát felhőbe telepített DNS-kiszolgálón.

## <a name="set-up-vsan-storage-policies"></a>VSAN-tárolási házirendek beállítása

a vSAN-szabályzatok meghatározzák a virtuálisgép-lemezeken tárolt adattárolási és lemezes csíkozási hibákat.  A létrehozott tárolási szabályzatot a virtuális gép létrehozásakor kell alkalmazni a virtuálisgép-lemezeken.

1. [Jelentkezzen be a privát felhő vSphere-ügyfelébe](./vcenter-access.md) .
2. A felső menüben válassza a **házirendek és profilok** lehetőséget.
3. A bal oldali menüben válassza ki a virtuálisgép- **tárolási házirendek** elemet, majd válassza **a virtuális gép tárolási házirendjének létrehozása** lehetőséget.
4. Írjon be egy értelmes nevet a Szabályzathoz, majd kattintson a **tovább** gombra.
5. A **házirend szerkezete** szakaszban válassza a **vSAN-tárolás szabályainak engedélyezése** lehetőséget, és kattintson a **tovább** gombra.
6. A **vSAN**  >  **rendelkezésre állása** szakaszban válassza a **nincs lehetőséget** a hely vész-tűréshatára elemnél. Az elviselni kívánt meghibásodások esetén válassza ki a **RAID-tükrözés** lehetőséget a kívánt pénzügyi tranzakcióhoz.
    ![vSAN beállításai ](media/oracle-rac-storage-wizard-vsan.png) .
7. A **speciális** szakaszban válassza ki a csíkozott lemezek számát. Az objektum területének foglalásához válassza a **vastag kiosztott** lehetőséget. Jelölje be az **objektum ellenőrzőösszegének letiltása** jelölőnégyzetet. Kattintson a **tovább** gombra.
8. Kövesse a képernyőn megjelenő utasításokat a kompatibilis vSAN-adattárolók listájának megtekintéséhez, tekintse át a beállításokat, és fejezze be a telepítést.

## <a name="create-oracle-vms-and-create-shared-vm-disks-for-oracle"></a>Oracle-alapú virtuális gépek létrehozása és megosztott VM-lemezek létrehozása az Oracle-hoz

Ha Oracle-alapú virtuális gépet szeretne létrehozni, klónozott egy meglévő virtuális gépet, vagy újat létrehozni.  Ez a szakasz azt ismerteti, hogyan hozhat létre egy új virtuális gépet, majd klónozással hozzon létre egy másodikat az alapszintű operációs rendszer telepítése után.  A virtuális gépek létrehozása után létrehozhat lemezeket.  Az Oracle-fürt megosztott lemezeket használ a naplók tárolásához, feldolgozásához, naplózásához és visszaállításához.

### <a name="create-vms"></a>Virtuális gépek létrehozása

1. A vCenter-ben kattintson a **gazdagépek és fürtök** ikonra. Válassza ki az Oracle-hoz létrehozott fürtöt.
2. Kattintson a jobb gombbal a fürtre, és válassza az **új virtuális gép** lehetőséget.
3. Válassza az **új virtuális gép létrehozása** lehetőséget, majd kattintson a **tovább** gombra.
4. Nevezze el a gépet, válassza ki az Oracle virtuális gép helyét, majd kattintson a **tovább** gombra.
5. Válassza ki a fürterőforrás-t, és kattintson a **tovább** gombra.
6. Válassza ki a fürt vSAN adattárát, és kattintson a **tovább** gombra.
7. Tartsa meg az alapértelmezett ESXi 6,5 kompatibilitási beállítást, majd kattintson a **tovább** gombra.
8. Válassza ki az ISO-t a létrehozandó virtuális géphez tartozó vendég operációs rendszernek, és kattintson a **tovább** gombra.
9. Válassza ki az operációs rendszer telepítéséhez szükséges merevlemez méretét.
10. Ha az alkalmazást egy másik eszközön szeretné telepíteni, kattintson az **új eszköz hozzáadása** lehetőségre.
11. Válassza a hálózati beállítások lehetőséget, és rendelje hozzá a nyilvános hálózathoz létrehozott elosztott portot.
12. További hálózati adapterek hozzáadásához kattintson az **új eszköz hozzáadása** lehetőségre, és válassza ki a magánhálózaton létrehozott elosztott portot.
13. Új DC/DVD-meghajtó esetén válassza ki az adattároló ISO-fájlját, amely az elsődleges operációs rendszer telepítéséhez szükséges ISO-fájlt tartalmazza. Válassza ki az ISO-fájlok és Sablonok mappába korábban feltöltött fájlt, majd kattintson **az OK** gombra.
14. Tekintse át a beállításokat, majd kattintson az **OK** gombra az új virtuális gép létrehozásához.
15. Kapcsolja be a virtuális gépet. Telepítse az operációs rendszert és a szükséges frissítéseket

Az operációs rendszer telepítése után egy második virtuális gépet is klónozott. Kattintson a jobb gombbal a VM-bejegyzésre, és válassza a és a klónozás lehetőséget.

### <a name="create-shared-disks-for-vms"></a>Megosztott lemezek létrehozása virtuális gépekhez

Az Oracle megosztott lemezt használ az adatfájlok, a naplók és a naplófájlok tárolásához.  Létrehozhat egy megosztott lemezt a vCenter, és csatlakoztathatja a virtuális gépeken is.  A nagyobb teljesítmény érdekében helyezze el az adatlemezeket a különböző SCSI-vezérlőkben az alábbi lépésekben azt mutatja, hogyan kell létrehozni egy megosztott lemezt a vCenter, majd csatlakoztatni azt egy virtuális géphez. a virtuális gép tulajdonságainak módosítására a vCenter Flash-ügyfél használható.

#### <a name="create-disks-on-the-first-vm"></a>Lemezek létrehozása az első virtuális gépen

1. A vCenter kattintson a jobb gombbal az egyik Oracle virtuális gépre, és válassza a **beállítások szerkesztése** lehetőséget.
2. Az új eszköz szakaszban válassza az **SCSI-vezérlő** elemet, majd kattintson a **Hozzáadás** gombra.
3. Az új eszköz szakaszban válassza az **új merevlemez** elemet, majd kattintson a **Hozzáadás** gombra.
4. Bontsa ki az új merevlemez tulajdonságait.
5. A merevlemez méretének megadása.
6. Itt adhatja meg, hogy a virtuális gép tárolási szabályzata a korábban megadott vSAN-tárolási házirend legyen.
7. Válassza ki a helyet mappaként a vSAN adattárában. A hely segít megkeresni és csatlakoztatni a lemezeket egy második virtuális géphez.
8. A lemezes kiépítés esetében válassza a **vastag kiépítés lelkesen nulla értéket**.
9. A megosztáshoz válassza a **többszörös író**.
10. A virtuális eszköz csomópontnál válassza ki a 2. lépésben létrehozott új SCSI-vezérlőt.

    ![Képernyőfelvétel: a lemezek első virtuális gépen való létrehozásához szükséges mezők kiemelése.](media/oracle-rac-new-hard-disk.png)

Ismételje meg a 2 – 10. lépést az Oracle-adatfájlokhoz,-naplókhoz és-naplófájlokhoz szükséges összes új lemez esetében.

#### <a name="attach-disks-to-second-vm"></a>Lemezek csatlakoztatása a második virtuális géphez

1. A vCenter kattintson a jobb gombbal az egyik Oracle virtuális gépre, és válassza a **beállítások szerkesztése** lehetőséget.
2. Az új eszköz szakaszban válassza az **SCSI-vezérlő** elemet, majd kattintson a **Hozzáadás** gombra.
3. Az új eszköz szakaszban válassza a **meglévő merevlemez** elemet, majd kattintson a **Hozzáadás** gombra.
4. Keresse meg azt a helyet, ahol az első virtuális géphez létrehozta a lemezt, és válassza ki a VMDK fájlt.
5. Itt adhatja meg, hogy a virtuális gép tárolási szabályzata a korábban megadott vSAN-tárolási házirend legyen.
6. A lemezes kiépítés esetében válassza a **vastag kiépítés lelkesen nulla értéket**.
7. A megosztáshoz válassza a **többszörös író**.
8. A virtuális eszköz csomópontnál válassza ki a 2. lépésben létrehozott új SCSI-vezérlőt.

    ![Lemezek létrehozása az első virtuális gépen](media/oracle-rac-existing-hard-disk.png)

Ismételje meg a 2 – 7. lépést minden olyan új lemez esetében, amely az Oracle-adatfájlokhoz, a naplókhoz és a naplófájlok visszaállításához szükséges.

## <a name="set-up-vm-host-affinity-rules"></a>Virtuálisgép-gazda affinitási szabályok beállítása

A virtuális gépek közötti affinitási szabályok biztosítják, hogy a virtuális gép a kívánt gazdagépen fusson.  Megadhat szabályokat a vCenter annak biztosításához, hogy az Oracle-alapú virtuális gép a megfelelő erőforrásokkal rendelkező gazdagépen fusson, és kielégítse az adott licencelési követelményeket.

1. A CloudSimple-portálon a cloudowner-felhasználó [jogosultságait is kiterjesztheti](escalate-private-cloud-privileges.md) .
2. Jelentkezzen be a privát felhő vSphere-ügyfelébe.
3. Az vSphere-ügyfélen válassza ki azt a fürtöt, amelyen az Oracle virtuális gépek telepítve vannak, majd kattintson a **Konfigurálás** gombra.
4. A konfigurálás területen válassza a **virtuális gép/gazda csoportok** lehetőséget.
5. Kattintson a elemre **+** .
6. Adjon hozzá egy virtuálisgép-csoportot. Válassza ki a virtuálisgép- **csoportot** típusként. Adja meg a csoport nevét. Válassza ki a virtuális gépeket, majd kattintson az **OK** gombra a csoport létrehozásához.
6. Adjon hozzá egy gazda csoportot. Válassza ki a **gazda csoportot** típusként. Adja meg a csoport nevét. Válassza ki azokat a gazdagépeket, amelyeken a virtuális gépek futni fognak, majd kattintson az **OK** gombra a csoport létrehozásához.
7. Szabály létrehozásához kattintson a **virtuális gép/gazdagép szabályai** elemre.
8. Kattintson a elemre **+** .
9. Adja meg a szabály nevét, és jelölje be az **Engedélyezés** gombot.
10. A szabály típusa beállításnál válassza a **Virtual Machines a gazdagéphez** lehetőséget.
11. Válassza ki az Oracle-alapú virtuális gépeket tartalmazó virtuálisgép-csoportot.
12. Válassza **a kötelező beállítást a csoportban lévő gazdagépeken**.
13. Válassza ki a létrehozott gazda csoportot.
14. A szabály létrehozásához kattintson az **OK** gombra.

## <a name="references"></a>Hivatkozások

* [Tudnivalók a vSAN-szabályzatokról](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.virtualsan.doc/GUID-08911FD3-2462-4C1C-AE81-0D4DBC8F7990.html)
* [VMware multi-Writer attribútum megosztott VMDK](https://docs.vmware.com/en/VMware-Cloud-on-AWS/solutions/VMware-Cloud-on-AWS.df6735f8b729fee463802083d46fdc75/GUID-A7642A82B3D6C5F7806DB40A3F2766D9.html)
