---
title: Fogalmak – vSphere szerepköralapú hozzáférés-vezérlés (vSphere RBAC)
description: Ismerje meg az Azure VMware-megoldás vSphere szerepköralapú hozzáférés-vezérlésének főbb képességeit
ms.topic: conceptual
ms.date: 02/02/2021
ms.openlocfilehash: 9d7c724fba194d86c2d73bb8323439de7c83c170
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2021
ms.locfileid: "99537018"
---
# <a name="vsphere-role-based-access-control-vsphere-rbac-for-azure-vmware-solution"></a>vSphere szerepköralapú hozzáférés-vezérlés (vSphere RBAC) Azure-beli VMware-megoldáshoz

Az Azure VMware megoldásban a vCenter beépített helyi felhasználó nevű cloudadmin, és a beépített CloudAdmin szerepkörhöz van rendelve. A helyi cloudadmin-felhasználó a felhasználók beállítására szolgál az AD-ben. Általánosságban elmondható, hogy a CloudAdmin szerepkör a saját felhőben hozza létre és kezeli a munkaterheléseket. Az Azure VMware megoldásban a CloudAdmin szerepkör olyan vCenter-jogosultságokkal rendelkezik, amelyek eltérnek a többi VMware Cloud-megoldástól.     

> [!NOTE]
> Az Azure VMware-megoldás jelenleg nem biztosít egyéni szerepköröket a vCenter vagy az Azure VMware Solution Portalon. 

A vCenter és az ESXi helyszíni üzembe helyezése során a rendszergazda hozzáférhet a vCenter- administrator@vsphere.local fiókhoz. Emellett további Active Directory (AD) felhasználókhoz vagy csoportokhoz is hozzárendelhetők. 

Egy Azure VMware-megoldás üzembe helyezése esetén a rendszergazdának nincs hozzáférése a rendszergazdai felhasználói fiókhoz. Az AD-felhasználók és-csoportok azonban hozzárendelhetők a CloudAdmin szerepkörhöz a vCenter.  

A saját felhőalapú felhasználó nem férhet hozzá a Microsoft által támogatott és kezelt felügyeleti összetevőkhöz. Például fürtök, gazdagépek, adattárolók és elosztott virtuális kapcsolók.

## <a name="azure-vmware-solution-cloudadmin-role-on-vcenter"></a>Azure VMware-megoldás CloudAdmin-szerepkör a vCenter

Megtekintheti az Azure VMware-megoldás CloudAdmin szerepkörhöz biztosított jogosultságokat az Azure VMware-megoldás saját Felhőbeli vCenter.

1. Jelentkezzen be a SDDC vSphere-ügyfelébe, és lépjen a **menü adminisztráció elemre**  >  .
1. A **Access Control** területen válassza a **szerepkörök** lehetőséget.
1. A szerepkörök listájából válassza a **CloudAdmin** lehetőséget, majd válassza a **jogosultságok** lehetőséget. 

   :::image type="content" source="media/role-based-access-control-cloudadmin-privileges.png" alt-text="A CloudAdmin szerepkörhöz tartozó jogosultságok megtekintése a vSphere-ügyfélben":::

Az Azure VMware-megoldás CloudAdmin szerepköre a következő jogosultságokkal rendelkezik a vCenter. Az egyes jogosultságok részletes ismertetését a [VMware termékdokumentációban](https://docs.vmware.com/en/VMware-vSphere/7.0/com.vmware.vsphere.security.doc/GUID-ED56F3C4-77D0-49E3-88B6-B99B8B437B62.html) találja.

| Privilege | Leírás |
| --------- | ----------- |
| **Riasztások** | Riasztás nyugtázása<br />Riasztás létrehozása<br />Riasztási művelet letiltása<br />Riasztás módosítása<br />Riasztás eltávolítása<br />Riasztás állapotának beállítása |
| **Engedélyek** | Engedélyek módosítása |
| **Tartalomtár** | Függvénytár-elem hozzáadása<br />Előfizetés létrehozása közzétett könyvtárhoz<br />Helyi könyvtár létrehozása<br />Előfizetett tár létrehozása<br />Függvénytár-elemek törlése<br />Helyi könyvtár törlése<br />Előfizetett tár törlése<br />Közzétett könyvtár előfizetésének törlése<br />Fájlok letöltése<br />Függvénytár-elemek kizárása<br />Előfizetett tár kizárása<br />Tároló importálása<br />Mintavételi előfizetés adatai<br />Függvénytár-elemek közzététele az előfizetők számára<br />Tár közzététele az előfizetők számára<br />Tár olvasása<br />Könyvtár-elemek szinkronizálása<br />Előfizetett tár szinkronizálása<br />Betekintés típusa<br />Konfigurációs beállítások frissítése<br />Fájlok frissítése<br />Könyvtár frissítése<br />Függvénytár-elemek frissítése<br />Helyi könyvtár frissítése<br />Előfizetett tár frissítése<br />Közzétett könyvtár előfizetésének frissítése<br />Konfigurációs beállítások megtekintése |
| **Titkosítási műveletek** | Közvetlen hozzáférés |
| **Adattár** | Tárterület foglalása<br />Adattár tallózása<br />Adattár konfigurálása<br />Alacsony szintű fájlok műveletei<br />Fájlok eltávolítása<br />Virtuális gép metaadatainak frissítése |
| **Mappa** | Mappa létrehozása<br />Mappa törlése<br />Mappa áthelyezése<br />Mappa átnevezése |
| **Globális** | Feladat megszakítása<br />Globális címke<br />Egészségügy<br />Esemény naplózása<br />Egyéni attribútumok kezelése<br />Service managerek<br />Egyéni attribútum beállítása<br />Rendszercímke |
| **Állomás** | vSphere-replikáció<br />&#160;&#160;&#160;&#160;a replikáció kezelése |
| **vSphere címkézése** | VSphere-címke hozzárendelésének és hozzárendelésének megszüntetése<br />VSphere címke létrehozása<br />VSphere-címke kategória létrehozása<br />VSphere-címke törlése<br />VSphere törlése<br />VSphere címke szerkesztése<br />VSphere szerkesztése címke kategóriája<br />Kategória UsedBy-mezőjének módosítása<br />Címke UsedBy-mezőjének módosítása |
| **Hálózat** | Hálózat kiosztása |
| **Erőforrás** | Javaslat alkalmazása<br />VApp erőforrás-készlethez rendelése<br />Virtuális gép erőforrás-készlethez rendelése<br />Erőforráskészlet létrehozása<br />Virtuális gép migrálása<br />Migrálás virtuális gépen<br />Erőforráskészlet módosítása<br />Erőforráskészlet áthelyezése<br />VMotion lekérdezése<br />Erőforráskészlet eltávolítása<br />Erőforráskészlet átnevezése |
| **Ütemezett feladat** | Feladat létrehozása<br />Feladat módosítása<br />Feladat eltávolítása<br />Feladat futtatása |
| **Munkamenetek** | Üzenet<br />Munkamenet ellenőrzése |
| **Profil** | Profil vezérelt tárolási nézet |
| **Tárolási nézet** | Nézet |
| **vApp** | Virtuális gép hozzáadása<br />Erőforráskészlet kiosztása<br />VApp-hozzárendelés<br />Klónozás<br />Létrehozás<br />Törlés<br />Exportálás<br />Importálás<br />Áthelyezés<br />Kikapcsolás<br />Bekapcsolás<br />Átnevezés<br />Felfüggesztés<br />Regisztrálás törlése<br />OVF-környezet megtekintése<br />vApp-alkalmazás konfigurációja<br />vApp-példány konfigurációja<br />vApp többé-konfiguráció<br />vApp erőforrás-konfiguráció |
| **Virtuális gép** | Konfiguráció módosítása<br />&#160;&#160;&#160;&#160;lemez bérletének beolvasása<br />&#160;&#160;&#160;&#160;meglévő lemez hozzáadása<br />&#160;&#160;&#160;&#160;új lemez hozzáadása<br />Eszköz hozzáadása vagy eltávolítása &#160;&#160;&#160;&#160;<br />&#160;&#160;&#160;&#160;speciális konfiguráció<br />&#160;&#160;&#160;&#160;a PROCESSZORok számának módosítása<br />&#160;&#160;&#160;&#160;memória módosítása<br />&#160;&#160;&#160;&#160;beállítások módosítása<br />&#160;&#160;&#160;&#160;swapfile elhelyezésének módosítása<br />&#160;&#160;&#160;&#160;erőforrás módosítása<br />&#160;&#160;&#160;&#160;gazdagép USB-eszközének konfigurálása<br />&#160;&#160;&#160;&#160;nyers eszköz konfigurálása<br />Többé konfigurálása &#160;&#160;&#160;&#160;<br />&#160;&#160;&#160;&#160;megjelenítési kapcsolatok beállításai<br />&#160;&#160;&#160;&#160;virtuális lemez kibővítése<br />Eszközbeállítások &#160;&#160;&#160;&#160;módosítása<br />&#160;&#160;&#160;&#160;lekérdezés hibatűrésének kompatibilitása<br />&#160;&#160;&#160;&#160;a nem birtokolt fájlok lekérdezése<br />&#160;&#160;&#160;&#160;Újratöltés az elérési utakról<br />&#160;&#160;&#160;&#160;lemez eltávolítása<br />&#160;&#160;&#160;&#160;átnevezése<br />&#160;&#160;&#160;&#160;a vendég adatainak visszaállítása<br />&#160;&#160;&#160;&#160;jegyzet beállítása<br />&#160;&#160;&#160;&#160;váltás a lemez változásának nyomon követésére<br />&#160;&#160;&#160;&#160;az elágazás szülőjének kikapcsolása<br />A virtuális gépek kompatibilitásának &#160;&#160;&#160;&#160;frissítése<br />Leltár szerkesztése<br />&#160;&#160;&#160;&#160;létrehozás meglévőből<br />&#160;&#160;&#160;&#160;új létrehozása<br />&#160;&#160;&#160;&#160;áthelyezés<br />&#160;&#160;&#160;&#160;regisztráció<br />&#160;&#160;&#160;&#160;eltávolítás<br />&#160;&#160;&#160;&#160;regisztrációjának törlése<br />Vendég műveletei<br />&#160;&#160;&#160;&#160;vendég műveleti alias módosítása<br />&#160;&#160;&#160;&#160;vendég műveleti alias lekérdezése<br />&#160;&#160;&#160;&#160;vendég művelet módosításai<br />&#160;&#160;&#160;&#160;vendég műveleti program végrehajtása<br />Vendég műveleti lekérdezések &#160;&#160;&#160;&#160;<br />Interakció<br />&#160;&#160;&#160;&#160;válasz kérdése<br />Biztonsági mentési művelet &#160;&#160;&#160;&#160;virtuális gépen<br />&#160;&#160;&#160;&#160;CD-adathordozó konfigurálása<br />Hajlékonylemez-adathordozó &#160;&#160;&#160;&#160;konfigurálása<br />&#160;&#160;&#160;&#160;eszközök csatlakoztatása<br />&#160;&#160;&#160;&#160;konzol interakciója<br />Képernyőkép &#160;&#160;&#160;&#160;létrehozása<br />&#160;&#160;&#160;&#160;az összes lemez töredezettségmentesítése<br />&#160;&#160;&#160;&#160;drag and drop<br />&#160;&#160;&#160;&#160;vendég operációs rendszer felügyelete a VIX API-val<br />&#160;&#160;&#160;&#160;USB HID-beolvasó kódok behelyezése<br />&#160;&#160;&#160;&#160;VMware-eszközök telepítése<br />&#160;&#160;&#160;&#160;szüneteltetése vagy szüneteltetése<br />&#160;&#160;&#160;&#160;a törlési vagy a zsugorodó művelet végrehajtása<br />Kikapcsolás &#160;&#160;&#160;&#160;<br />&#160;&#160;&#160;&#160;bekapcsolás<br />&#160;&#160;&#160;&#160;rögzítési munkamenet a virtuális gépen<br />Újralejátszási munkamenet &#160;&#160;&#160;&#160;a virtuális gépen<br />&#160;&#160;&#160;&#160;felfüggesztés<br />&#160;&#160;&#160;&#160;felfüggesztési hibatűrés<br />&#160;&#160;&#160;&#160;feladatátvételi teszt<br />&#160;&#160;&#160;&#160;teszt másodlagos virtuális gép újraindítása<br />A hibatűrés kikapcsolása &#160;&#160;&#160;&#160;<br />A hibatűrés bekapcsolása &#160;&#160;&#160;&#160;<br />Kiépítés<br />&#160;&#160;&#160;&#160;a lemez elérésének engedélyezése<br />&#160;&#160;&#160;&#160;a fájlok elérésének engedélyezése<br />&#160;&#160;&#160;&#160;írásvédett lemezes hozzáférés engedélyezése<br />&#160;&#160;&#160;&#160;a virtuális gép letöltésének engedélyezése<br />&#160;&#160;&#160;&#160;klónozási sablon<br />&#160;&#160;&#160;&#160;virtuális gép klónozása<br />&#160;&#160;&#160;&#160;sablon létrehozása virtuális gépről<br />Vendég &#160;&#160;&#160;&#160;testreszabása<br />Sablon &#160;&#160;&#160;&#160;üzembe helyezése<br />&#160;&#160;&#160;&#160;megjelölés sablonként<br />&#160;&#160;&#160;&#160;testreszabási specifikáció módosítása<br />&#160;&#160;&#160;&#160;lemezek előléptetése<br />&#160;&#160;&#160;&#160;olvasási testreszabási specifikáció<br />Szolgáltatáskonfiguráció<br />Értesítések &#160;&#160;&#160;&#160;engedélyezése<br />A globális eseményekre vonatkozó értesítések lekérdezésének &#160;&#160;&#160;&#160;engedélyezése<br />&#160;&#160;&#160;&#160;a szolgáltatás konfigurációjának kezelése<br />&#160;&#160;&#160;&#160;a szolgáltatás konfigurációjának módosítása<br />&#160;&#160;&#160;&#160;lekérdezési szolgáltatás konfigurációi<br />&#160;&#160;&#160;&#160;olvasási szolgáltatás konfigurációja<br />Pillanatképek kezelése<br />Pillanatkép létrehozása &#160;&#160;&#160;&#160;<br />Pillanatkép &#160;&#160;&#160;&#160;eltávolítása<br />&#160;&#160;&#160;&#160;pillanatkép átnevezése<br />&#160;&#160;&#160;&#160;a pillanatkép visszaállítása<br />vSphere-replikáció<br />&#160;&#160;&#160;&#160;replikáció konfigurálása<br />&#160;&#160;&#160;&#160;a replikáció kezelése<br />&#160;&#160;&#160;&#160;a replikáció figyelése |
| **vService** | Függőség létrehozása<br />Függőség megsemmisítése<br />Függőség konfigurációjának újrakonfigurálása<br />Frissítési függőség |



## <a name="next-steps"></a>Következő lépések

Most, hogy már lefedette az Azure VMware-megoldás vSphere szerepköralapú hozzáférés-vezérlésének alapjait, érdemes megismernie az alábbiakat:

- A [VMware termékdokumentációban](https://docs.vmware.com/en/VMware-vSphere/7.0/com.vmware.vsphere.security.doc/GUID-ED56F3C4-77D0-49E3-88B6-B99B8B437B62.html)található egyes jogosultságok részletei.
- [Hogyan figyeli és javítja az Azure VMware-megoldás a privát felhőket](concepts-monitor-repair-private-cloud.md).
- [Az Azure VMware megoldás erőforrásának engedélyezése](enable-azure-vmware-solution.md).

<!-- LINKS - internal -->

<!-- LINKS - external-->
[VMware product documentation]: https://docs.vmware.com/en/VMware-vSphere/7.0/com.vmware.vsphere.security.doc/GUID-ED56F3C4-77D0-49E3-88B6-B99B8B437B62.html

