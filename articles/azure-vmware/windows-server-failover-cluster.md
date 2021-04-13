---
title: A Windows Server feladatátvevő fürt konfigurálása az Azure VMware megoldás vSAN
description: Állítsa be a Windows Server feladatátvevő fürtöt (WSFC) az Azure VMware megoldás vSAN natív megosztott lemezekkel.
ms.topic: how-to
ms.date: 04/09/2021
ms.openlocfilehash: f1bc8199eb0d3317e4b6e07a6a297b4ebfe95cc8
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107308683"
---
# <a name="configure-windows-server-failover-cluster-on-azure-vmware-solution-vsan"></a>A Windows Server feladatátvevő fürt konfigurálása az Azure VMware megoldás vSAN

Ebből a cikkből megtudhatja, hogyan állíthatja be a Windows Server feladatátvevő fürtöt az Azure VMware megoldás vSAN natív megosztott lemezekkel. 

>[!IMPORTANT]
>A jelen cikkben ismertetett megvalósítás a koncepciók és a tesztelési célok igazolására szolgál. Azt javasoljuk, hogy a helyszíni (CIB) konfigurációt csak akkor használja, ha az elhelyezési házirendek elérhetővé válnak.

A Windows Server feladatátvételi fürt (WSFC), korábbi nevén Microsoft Service cluster Service (MSCS), a Windows Server operációs rendszer (OS) egyik funkciója. A WSFC egy üzleti szempontból kritikus funkció, és számos alkalmazásra van szükség. A következő konfigurációk esetében például a WSFC szükséges:

- SQL Server konfigurálva:
  - Mindig a feladatátvételi fürt példányán (verzió), a magas rendelkezésre állás érdekében.
  - Always On rendelkezésre állási csoport (AG), adatbázis szintű magas rendelkezésre álláshoz.
- Windows-Fájlszolgáltatások:
  - Az aktív fürt csomópontján futó általános fájlmegosztás.
  - Scale-Out fájlkiszolgáló (SOFS), amely a fürt megosztott köteteiben (CSV) tárolja a fájlokat.
  - Közvetlen tárolóhelyek (S2D); a különböző fürtcsomópontok közötti tárolási készletek létrehozásához használt helyi lemezek.

A WSFC-fürtöt különböző Azure VMware-megoldási példányokon, más néven fürtön keresztül is üzemeltetheti. A WSFC-fürtöt egyetlen Azure VMware-megoldási csomóponton is elhelyezheti. Ezt a konfigurációt nevezzük fürt-a-a-Box (CIB) néven. Az üzemi megvalósításhoz nem ajánlott CIB megoldást használni. Az egyetlen Azure VMware-megoldás csomópontja sikertelen volt, az összes WSFC ki lesz kapcsolva, és az alkalmazás leállást tapasztal. Az Azure VMware-megoldáshoz legalább három csomópontra van szükség egy privát felhőalapú fürtben.

Fontos, hogy telepítsen egy támogatott WSFC-konfigurációt. Érdemes támogatni a megoldást a vSphere és az Azure VMware megoldással. A VMware részletes dokumentumot nyújt a vSphere 6,7 WSFC, a [feladatátvételi fürtszolgáltatás és a Microsoft fürtszolgáltatásának beállításáról](https://docs.vmware.com/en/VMware-vSphere/6.7/vsphere-esxi-vcenter-server-67-setup-mscs.pdf).

Ez a cikk a Windows Server 2016 és a Windows Server 2019 WSFC foglalkozik. A régebbi Windows Server-verziók nem rendelkeznek [általános támogatással](https://support.microsoft.com/lifecycle/search?alpha=windows%20server) , ezért nem vesszük figyelembe őket.

Először [létre kell hoznia egy WSFC](/windows-server/failover-clustering/create-failover-cluster). További információ a WSFC: [feladatátvételi fürtszolgáltatás a Windows Serveren](/windows-server/failover-clustering/failover-clustering-overview). Az ebben a cikkben ismertetett információk az Azure VMware-megoldás WSFC-telepítésének sajátosságait ismertetik.

## <a name="prerequisites"></a>Előfeltételek

- Azure VMware-megoldás környezet
- Microsoft Windows Server operációs rendszer telepítési adathordozója

## <a name="reference-architecture"></a>Referenciaarchitektúra

Az Azure VMware-megoldás natív támogatást nyújt a virtualizált WSFC. Támogatja az SCSI-3 állandó foglalásokat (SCSI3PR) a virtuális lemez szintjén. Ezt a támogatást úgy kell megkövetelni, hogy a WSFC a csomópontok közötti megosztott lemezre döntsön. A SCSI3PRs támogatása lehetővé teszi a WSFC konfigurációját a virtuális gépek között a vSAN-adattárolókban natív módon megosztott lemezes erőforrással.

Az alábbi ábra az Azure VMware-megoldás saját Felhőbeli WSFC virtuális csomópontjainak architektúráját mutatja be. Itt látható, hogy hol találhatók az Azure VMware-megoldások, beleértve a WSFC virtuális kiszolgálókat (piros doboz) a szélesebb körű Azure platformmal kapcsolatban. Ez az ábra egy tipikus küllős architektúrát mutat be, de egy hasonló beállítás is lehetséges az Azure Virtual WAN használatával. Mindkettő az összes többi Azure-szolgáltatást is felhasználhatja.

:::image type="content" source="media/windows-server-failover-cluster/windows-server-failover-architecture.svg" alt-text="A Windows Server feladatátvételi fürt virtuális csomópontjainak architektúra ábrája egy Azure VMware-megoldás saját felhőben." border="false" lightbox="media/windows-server-failover-cluster/windows-server-failover-architecture.svg":::

## <a name="supported-configurations"></a>Támogatott konfigurációk

Jelenleg a következő konfigurációk támogatottak:

- Microsoft Windows Server 2012 vagy újabb
- Legfeljebb öt feladatátvételi fürtszolgáltatási csomópont/fürt
- Legfeljebb négy PVSCSI adapter virtuális gépenként
- Akár 64 lemez/PVSCSI adapter

## <a name="virtual-machine-configuration-requirements"></a>A virtuális gép konfigurációs követelményei

### <a name="wsfc-node-configuration-parameters"></a>WSFC-csomópont konfigurációs paraméterei

- Telepítse a legújabb VMware-eszközöket minden WSFC-csomópontra.
- A nem megosztott és a megosztott lemezek egyetlen virtuális SCSI-adapteren való keverése nem támogatott. Ha például a rendszerlemez (C:) csatolva van a (z) SCSI0:0, az első megosztott lemez a következő SCSI1 lesz csatolva: 0. Egy WSFC virtuálisgép-csomópontjának a virtuális SCSI-vezérlővel azonosnak kell lennie, mint egy hagyományos virtuális gép – négy (4) virtuális SCSI-vezérlő.
- A virtuális lemezek SCSI-azonosítóinak konzisztensnek kell lenniük az azonos WSFC csomópontjait üzemeltető összes virtuális gép között.

| **Összetevő** | **Követelmények** |
| --- | --- |
| VM-hardver verziója | 11 vagy újabb verzió az élő vMotion támogatásához. |
| Virtuális hálózati adapter | VMXNET3 paravirtualizált hálózati kártya (NIC); Engedélyezze a vendég Windows fogadási oldali skálázást (RSS) a virtuális hálózati adapteren. |
| Memória | A WSFC-fürt csomópontjaihoz teljes körű virtuálisgép-foglalási memóriát használhat. |
| Növelje az egyes WSFC-csomópontok I/O-időtúllépését. | Módosítsa \_ a HKEY helyi \_ MACHINE\System\CurrentControlSet\Services\Disk\TimeOutValueSet 60 másodpercre vagy többre. (Ha újra létrehozza a fürtöt, lehetséges, hogy ez az érték visszaáll az alapértelmezettre, ezért újra meg kell változtatnia.) |
| Windows-fürt állapotának figyelése | A Windows-fürt állapotának figyelése SameSubnetThreshold paraméter értékét úgy kell módosítani, hogy legalább 10 kihagyott szívverést engedélyezzen. Ez [az alapértelmezett érték a Windows Server 2016 rendszerben](https://techcommunity.microsoft.com/t5/failover-clustering/tuning-failover-cluster-network-thresholds/ba-p/371834). Ez az ajánlás minden, a WSFC-t használó alkalmazásra vonatkozik, beleértve a megosztott és a nem megosztott lemezeket is. |

### <a name="wsfc-node---boot-disks-configuration-parameters"></a>WSFC-csomópont – rendszerindító lemezek konfigurációs paraméterei


| **Összetevő** | **Követelmények** |
| --- | --- |
| SCSI-vezérlő típusa | LSI Logic SAS |
| Lemez mód | Virtuális |
| SCSI-busz megosztása | Nincsenek |
| A rendszerindító eszközt üzemeltető virtuális SCSI-vezérlő speciális beállításainak módosítása. | Adja hozzá a következő speciális beállításokat az egyes WSFC-csomópontokhoz:<br /> scsiX. returnNoConnectDuringAPD = "TRUE"<br />scsiX. returnBusyOnNoConnectStatus = "FALSE"<br />Ahol az X a rendszerindító eszköz SCSI-busz-vezérlőjének azonosítószáma. Alapértelmezés szerint az X értéke 0. |

### <a name="wsfc-node---shared-disks-configuration-parameters"></a>WSFC-csomópont – megosztott lemezek konfigurációs paraméterei


| **Összetevő** | **Követelmények** |
| --- | --- |
| SCSI-vezérlő típusa | VMware Paravirtualize (PVSCSI) |
| Lemez mód | Független – állandó (2. lépés az alábbi ábrán). Ezzel a beállítással biztosíthatja, hogy a rendszer az összes lemezt kizárja a pillanatképből. A WSFC-alapú virtuális gépek pillanatképei nem támogatottak. |
| SCSI-busz megosztása | Fizikai (1. lépés az alábbi ábrán) |
| Többszörös író jelző | Nincs használatban |
| Lemezformátum | Sűrűn kiépített. (A vSAN esetében nem szükséges a türelmetlenül nulla értékű vastag (ISMÉTLÉSEK).) |

:::image type="content" source="media/windows-server-failover-cluster/edit-settings-virtual-hardware.png" alt-text="A virtuális hardverek beállításainak szerkesztése lapját ábrázoló képernyőkép.":::

## <a name="non-supported-scenarios"></a>Nem támogatott helyzetek

A következő funkciók nem támogatottak az Azure VMware-megoldás WSFC:

- NFS-adattárak
- Tárolóhelyek
- iSCSI-szolgáltatást használó vSAN
- vSAN kiterjesztett fürt
- Továbbfejlesztett vMotion-kompatibilitás (EVC)
- vSphere hibatűrése (FT)
- Pillanatképek
- Élő (online) tárolási vMotion
- N-port azonosító virtualizálása (NPIV)

Előfordulhat, hogy a virtuális gép hardverének gyakori módosításai megszakítják a szívverést a WSFC-csomópontok között.

A következő tevékenységek nem támogatottak, és a WSFC csomópont feladatátvételét okozhatják:

- Gyors Hozzáadás a memóriához
- Gyakori Hozzáadás a CPU-hoz
- Pillanatképek használata
- Megosztott lemez méretének növelése
- A virtuális gép állapotának szüneteltetése és folytatása
- A memóriához képest felvállalt elkötelezettség, amely az ESXi-csere vagy a virtuális gép memóriájának bevezetéséhez vezet
- Helyi VMDK-fájl gyors kiterjesztése, még akkor is, ha nincs az SCSI Bus-megosztási vezérlőhöz társítva

## <a name="configure-wsfc-with-shared-disks-on-azure-vmware-solution-vsan"></a>WSFC konfigurálása megosztott lemezekkel az Azure VMware megoldás vSAN

1. Győződjön meg arról, hogy rendelkezésre áll Active Directory környezet.
2. Hozzon létre virtuális gépeket (VM) a vSAN adattárán.
3. Kapcsolja be az összes virtuális gépet, konfigurálja a gazdagépet, az IP-címeket, csatlakoztassa az összes virtuális gépet egy Active Directory tartományhoz, és telepítse a legújabb elérhető operációsrendszer-frissítéseket.
4. Telepítse a legújabb VMware-eszközöket.
5. Engedélyezze és konfigurálja a Windows Server feladatátvételi fürtszolgáltatást minden egyes virtuális gépen.
6. Konfiguráljon egy tanúsító fürtöt a kvórum számára (a tanúsító fájlmegosztás jól működik).
7. A WSFC-fürt összes csomópontjának kikapcsolása.
8. Adjon hozzá egy vagy több Paravirtual SCSI-vezérlőt (legfeljebb négyet) a WSFC minden egyes virtuális géphez. Használja az előző bekezdésekben megadott beállításokat.
9. Az első fürtcsomóponton adja hozzá az összes szükséges megosztott lemezt az **új eszköz**  >  **merevlemezének** hozzáadása paranccsal. A lemezes megosztásnak nem **meghatározott** (alapértelmezett) és a lemezes üzemmódnak kell lennie **független – állandóként**. Csatolja az előző lépésekben létrehozott vezérlő (k) hoz.
10. Folytassa a fennmaradó WSFC-csomópontokkal. Adja hozzá az előző lépésben létrehozott lemezeket az **új eszköz**  >  **meglévő merevlemez** hozzáadása lehetőség kiválasztásával. Ügyeljen arra, hogy az összes WSFC-csomóponton azonos lemezes SCSI-azonosítókat őrizzen meg.
11. Kapcsolja be az első WSFC-csomópontot; Jelentkezzen be, és nyissa meg a Lemezkezelés konzolt (MMC). Győződjön meg arról, hogy a hozzáadott megosztott lemezeket az operációs rendszer felügyeli, és inicializálva van. Formázza a lemezeket, és rendeljen hozzá egy meghajtóbetűjelet.
12. Kapcsolja be a többi WSFC-csomópontot.
13. Adja hozzá a lemezt a WSFC-fürthöz a **lemez hozzáadása varázsló** segítségével, és vegye fel őket egy fürt megosztott köteteba.
14. Tesztelje a feladatátvételt a **lemez áthelyezése varázslóval** , és ellenőrizze, hogy a megosztott lemezekkel rendelkező WSFC-fürt megfelelően működik-e.
15. Futtassa az **ellenőrzési fürt varázslót** annak megerősítéséhez, hogy a fürt és a csomópontjai megfelelően működnek-e.

    Fontos, hogy a fürt ellenőrzési tesztje a következő konkrét elemeket tartsa szem előtt:

       - A **tárolóhelyek állandó foglalásának ellenőrzése**. Ha nem használja a tárolóhelyeket a fürttel (például az Azure VMware megoldás vSAN), ez a teszt nem alkalmazható. Figyelmen kívül hagyhatja a tárolóhelyek állandó foglalásának ellenőrzésének eredményét, beleértve a figyelmeztetést is. A figyelmeztetések elkerüléséhez kizárhatja ezt a tesztet.
        
      - A **hálózati kommunikáció ellenőrzése**. A fürt-ellenőrzési teszt arra figyelmeztet, hogy a fürtben csak egy hálózati adapter érhető el. Ezt a figyelmeztetést figyelmen kívül hagyhatja. Az Azure VMware-megoldás biztosítja a szükséges rendelkezésre állást és teljesítményt, mivel a csomópontok az egyik NSX-T szegmenshez csatlakoznak. Tartsa azonban ezt az elemeket a fürt ellenőrzési tesztje részeként, mivel a hálózati kommunikáció egyéb szempontjait is érvényesíti.

16. Hozzon létre egy DRS-szabályt, amely a WSFC virtuális gépeket ugyanarra az Azure VMware megoldás-csomópontra helyezi. Ehhez szükség van egy gazdagép – VM affinitási szabályra. Így a fürtcsomópontok ugyanazon az Azure VMware Solution gazdagépen futnak. Ez még kísérleti célokra szolgál, amíg nem állnak rendelkezésre elhelyezési szabályzatok.

    >[!NOTE]
    > Ehhez létre kell hoznia egy támogatási kérési jegyet. Az Azure-támogatási szervezetünk segítségére lesz.

## <a name="related-information"></a>Kapcsolódó információk

- [Feladatátvételi fürtszolgáltatás a Windows Serverben](/windows-server/failover-clustering/failover-clustering-overview)
- [Irányelvek a Microsoft fürtözéshez a vSphere (1037959) (vmware.com)](https://kb.vmware.com/s/article/1037959)
- [A feladatátvételi fürtszolgáltatás és a Microsoft fürtszolgáltatás (vmware.com) beállítása](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.mscs.doc/GUID-1A2476C0-CA66-4B80-B6F9-8421B6983808.html)
- [vSAN 6,7 U3 – WSFC megosztott lemezekkel, &amp; SCSI-3 állandó foglalások (VMware.com)](https://blogs.vmware.com/virtualblocks/2019/08/23/vsan67-u3-wsfc-shared-disksupport/)
- [Azure VMware-megoldás korlátai](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-vmware-solution-limits)

## <a name="next-steps"></a>Következő lépések

Most, hogy az Azure VMware-megoldásban beállított egy WSFC, érdemes megismernie az alábbiakat:

- Új WSFC beállítása további alkalmazások hozzáadásával, amelyekhez szükség van a WSFC képességre. Például SQL Server és SAP ASCS.
- Biztonsági mentési megoldás beállítása.
  - [Azure Backup Server beállítása Azure VMware-megoldáshoz](./set-up-backup-server-for-azure-vmware-solution.md)
  - [Azure VMware-megoldás virtuális gépekre vonatkozó biztonsági mentési megoldások](./ecosystem-back-up-vms.md)