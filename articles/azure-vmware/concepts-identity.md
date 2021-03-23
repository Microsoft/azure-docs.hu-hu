---
title: Fogalmak – identitás és hozzáférés
description: Ismerje meg az Azure VMware-megoldás identitás-és hozzáférési fogalmait
ms.topic: conceptual
ms.date: 03/22/2021
ms.openlocfilehash: f3a79c79acef07721bd0883fa70c835bee14ef0d
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2021
ms.locfileid: "104799275"
---
# <a name="azure-vmware-solution-identity-concepts"></a>Az Azure VMware megoldás identitásával kapcsolatos fogalmak

Az Azure VMware-megoldás privát felhők vCenter Server és NSX-T kezelővel vannak kiépítve. A vCenter használatával felügyelheti és kiterjesztheti a virtuális gépek (VM) munkaterheléseit és a NSX-T kezelőjét. A hozzáférés és az Identitáskezelés a CloudAdmin szerepkört használja a vCenter és a korlátozott rendszergazdai jogosultságok NSX-T kezelőhöz. 

További információ: [Private Cloud upgrade Concepts cikk][concepts-upgrades].

## <a name="vcenter-access-and-identity"></a>vCenter-hozzáférés és-identitás

Az Azure VMware megoldásban a vCenter beépített helyi felhasználó nevű cloudadmin, és a CloudAdmin szerepkörhöz van rendelve. A helyi cloudadmin-felhasználó a Active Directory (AD) felhasználóinak beállítására szolgál. Általánosságban elmondható, hogy a CloudAdmin szerepkör a saját felhőben hozza létre és kezeli a munkaterheléseket. Az Azure VMware megoldásban azonban a CloudAdmin szerepkör olyan vCenter-jogosultságokkal rendelkezik, amelyek eltérnek a többi VMware Cloud-megoldástól.     

- A vCenter és az ESXi helyszíni üzembe helyezése során a rendszergazda hozzáférhet a vCenter rendszergazdai \@ vSphere. local fiókhoz. Emellett további AD-felhasználók és-csoportok is hozzárendelhetők a szolgáltatáshoz. 

- Egy Azure VMware-megoldás üzembe helyezése esetén a rendszergazdának nincs hozzáférése a rendszergazdai felhasználói fiókhoz. Azonban az AD-felhasználók és-csoportok hozzárendelhetők a CloudAdmin szerepkörhöz a vCenter.  

A saját felhőalapú felhasználó nem férhet hozzá a Microsoft által támogatott és kezelt felügyeleti összetevőkhöz. Például fürtök, gazdagépek, adattárolók és elosztott virtuális kapcsolók.

> [!IMPORTANT]
> Az Azure VMware megoldás egyéni szerepköröket kínál a vCenter, de jelenleg nem biztosít számukra az Azure VMware Solution portálon. További információt a cikk későbbi, [Egyéni szerepkörök létrehozása a vCenter](#create-custom-roles-on-vcenter) című szakaszában talál. 

### <a name="view-the-vcenter-privileges"></a>A vCenter jogosultságok megtekintése

Megtekintheti az Azure VMware-megoldás CloudAdmin szerepkörhöz biztosított jogosultságokat az Azure VMware-megoldás saját Felhőbeli vCenter.

1. Jelentkezzen be a SDDC vSphere-ügyfelébe, és lépjen a **menü adminisztráció elemre**  >  .
1. A **Access Control** területen válassza a **szerepkörök** lehetőséget.
1. A szerepkörök listájából válassza a **CloudAdmin** lehetőséget, majd válassza a **jogosultságok** lehetőséget. 

   :::image type="content" source="media/role-based-access-control-cloudadmin-privileges.png" alt-text="A CloudAdmin szerepkörhöz tartozó jogosultságok megtekintése a vSphere-ügyfélben":::

Az Azure VMware-megoldás CloudAdmin szerepköre a következő jogosultságokkal rendelkezik a vCenter. További részletekért tekintse meg a [VMware termék dokumentációját](https://docs.vmware.com/en/VMware-vSphere/7.0/com.vmware.vsphere.security.doc/GUID-ED56F3C4-77D0-49E3-88B6-B99B8B437B62.html).

| Privilege | Leírás |
| --------- | ----------- |
| **Riasztások** | Riasztás nyugtázása<br />Riasztás létrehozása<br />Riasztási művelet letiltása<br />Riasztás módosítása<br />Riasztás eltávolítása<br />Riasztás állapotának beállítása |
| **Tartalomtár** | Függvénytár-elem hozzáadása<br />Előfizetés létrehozása közzétett könyvtárhoz<br />Helyi könyvtár létrehozása<br />Előfizetett tár létrehozása<br />Függvénytár-elemek törlése<br />Helyi könyvtár törlése<br />Előfizetett tár törlése<br />Közzétett könyvtár előfizetésének törlése<br />Fájlok letöltése<br />Függvénytár-elemek kizárása<br />Előfizetett tár kizárása<br />Tároló importálása<br />Mintavételi előfizetés adatai<br />Függvénytár-elemek közzététele az előfizetők számára<br />Tár közzététele az előfizetők számára<br />Tár olvasása<br />Könyvtár-elemek szinkronizálása<br />Előfizetett tár szinkronizálása<br />Betekintés típusa<br />Konfigurációs beállítások frissítése<br />Fájlok frissítése<br />Könyvtár frissítése<br />Függvénytár-elemek frissítése<br />Helyi könyvtár frissítése<br />Előfizetett tár frissítése<br />Közzétett könyvtár előfizetésének frissítése<br />Konfigurációs beállítások megtekintése |
| **Titkosítási műveletek** | Közvetlen hozzáférés |
| **Adattár** | Tárterület foglalása<br />Adattár tallózása<br />Adattár konfigurálása<br />Alacsony szintű fájlok műveletei<br />Fájlok eltávolítása<br />Virtuális gép metaadatainak frissítése |
| **Mappa** | Mappa létrehozása<br />Mappa törlése<br />Mappa áthelyezése<br />Mappa átnevezése |
| **Globális** | Feladat megszakítása<br />Globális címke<br />Egészségügy<br />Esemény naplózása<br />Egyéni attribútumok kezelése<br />Service managerek<br />Egyéni attribútum beállítása<br />Rendszercímke |
| **Állomás** | vSphere-replikáció<br />&#160;&#160;&#160;&#160;a replikáció kezelése |
| **Hálózat** | Hálózat kiosztása |
| **Engedélyek** | Engedélyek módosítása<br />Szerepkör módosítása |
| **Profil** | Profil vezérelt tárolási nézet |
| **Erőforrás** | Javaslat alkalmazása<br />VApp erőforrás-készlethez rendelése<br />Virtuális gép erőforrás-készlethez rendelése<br />Erőforráskészlet létrehozása<br />Virtuális gép migrálása<br />Migrálás virtuális gépen<br />Erőforráskészlet módosítása<br />Erőforráskészlet áthelyezése<br />VMotion lekérdezése<br />Erőforráskészlet eltávolítása<br />Erőforráskészlet átnevezése |
| **Ütemezett feladat** | Feladat létrehozása<br />Feladat módosítása<br />Feladat eltávolítása<br />Feladat futtatása |
| **Munkamenetek** | Üzenet<br />Munkamenet ellenőrzése |
| **Tárolási nézet** | Nézet |
| **vApp** | Virtuális gép hozzáadása<br />Erőforráskészlet kiosztása<br />VApp-hozzárendelés<br />Klónozás<br />Létrehozás<br />Törlés<br />Exportálás<br />Importálás<br />Áthelyezés<br />Kikapcsolás<br />Bekapcsolás<br />Átnevezés<br />Felfüggesztés<br />Regisztrálás törlése<br />OVF-környezet megtekintése<br />vApp-alkalmazás konfigurációja<br />vApp-példány konfigurációja<br />vApp többé-konfiguráció<br />vApp erőforrás-konfiguráció |
| **Virtuális gép** | Konfiguráció módosítása<br />&#160;&#160;&#160;&#160;lemez bérletének beolvasása<br />&#160;&#160;&#160;&#160;meglévő lemez hozzáadása<br />&#160;&#160;&#160;&#160;új lemez hozzáadása<br />Eszköz hozzáadása vagy eltávolítása &#160;&#160;&#160;&#160;<br />&#160;&#160;&#160;&#160;speciális konfiguráció<br />&#160;&#160;&#160;&#160;a PROCESSZORok számának módosítása<br />&#160;&#160;&#160;&#160;memória módosítása<br />&#160;&#160;&#160;&#160;beállítások módosítása<br />&#160;&#160;&#160;&#160;swapfile elhelyezésének módosítása<br />&#160;&#160;&#160;&#160;erőforrás módosítása<br />&#160;&#160;&#160;&#160;gazdagép USB-eszközének konfigurálása<br />&#160;&#160;&#160;&#160;nyers eszköz konfigurálása<br />Többé konfigurálása &#160;&#160;&#160;&#160;<br />&#160;&#160;&#160;&#160;megjelenítési kapcsolatok beállításai<br />&#160;&#160;&#160;&#160;virtuális lemez kibővítése<br />Eszközbeállítások &#160;&#160;&#160;&#160;módosítása<br />&#160;&#160;&#160;&#160;lekérdezés hibatűrésének kompatibilitása<br />&#160;&#160;&#160;&#160;a nem birtokolt fájlok lekérdezése<br />&#160;&#160;&#160;&#160;Újratöltés az elérési utakról<br />&#160;&#160;&#160;&#160;lemez eltávolítása<br />&#160;&#160;&#160;&#160;átnevezése<br />&#160;&#160;&#160;&#160;a vendég adatainak visszaállítása<br />&#160;&#160;&#160;&#160;jegyzet beállítása<br />&#160;&#160;&#160;&#160;váltás a lemez változásának nyomon követésére<br />&#160;&#160;&#160;&#160;az elágazás szülőjének kikapcsolása<br />A virtuális gépek kompatibilitásának &#160;&#160;&#160;&#160;frissítése<br />Leltár szerkesztése<br />&#160;&#160;&#160;&#160;létrehozás meglévőből<br />&#160;&#160;&#160;&#160;új létrehozása<br />&#160;&#160;&#160;&#160;áthelyezés<br />&#160;&#160;&#160;&#160;regisztráció<br />&#160;&#160;&#160;&#160;eltávolítás<br />&#160;&#160;&#160;&#160;regisztrációjának törlése<br />Vendég műveletei<br />&#160;&#160;&#160;&#160;vendég műveleti alias módosítása<br />&#160;&#160;&#160;&#160;vendég műveleti alias lekérdezése<br />&#160;&#160;&#160;&#160;vendég művelet módosításai<br />&#160;&#160;&#160;&#160;vendég műveleti program végrehajtása<br />Vendég műveleti lekérdezések &#160;&#160;&#160;&#160;<br />Interakció<br />&#160;&#160;&#160;&#160;válasz kérdése<br />Biztonsági mentési művelet &#160;&#160;&#160;&#160;virtuális gépen<br />&#160;&#160;&#160;&#160;CD-adathordozó konfigurálása<br />Hajlékonylemez-adathordozó &#160;&#160;&#160;&#160;konfigurálása<br />&#160;&#160;&#160;&#160;eszközök csatlakoztatása<br />&#160;&#160;&#160;&#160;konzol interakciója<br />Képernyőkép &#160;&#160;&#160;&#160;létrehozása<br />&#160;&#160;&#160;&#160;az összes lemez töredezettségmentesítése<br />&#160;&#160;&#160;&#160;drag and drop<br />&#160;&#160;&#160;&#160;vendég operációs rendszer felügyelete a VIX API-val<br />&#160;&#160;&#160;&#160;USB HID-beolvasó kódok behelyezése<br />&#160;&#160;&#160;&#160;VMware-eszközök telepítése<br />&#160;&#160;&#160;&#160;szüneteltetése vagy szüneteltetése<br />&#160;&#160;&#160;&#160;a törlési vagy a zsugorodó művelet<br />Kikapcsolás &#160;&#160;&#160;&#160;<br />&#160;&#160;&#160;&#160;bekapcsolás<br />&#160;&#160;&#160;&#160;rögzítési munkamenet a virtuális gépen<br />Újralejátszási munkamenet &#160;&#160;&#160;&#160;a virtuális gépen<br />&#160;&#160;&#160;&#160;felfüggesztés<br />&#160;&#160;&#160;&#160;felfüggesztési hibatűrés<br />&#160;&#160;&#160;&#160;feladatátvételi teszt<br />&#160;&#160;&#160;&#160;teszt másodlagos virtuális gép újraindítása<br />A hibatűrés kikapcsolása &#160;&#160;&#160;&#160;<br />A hibatűrés bekapcsolása &#160;&#160;&#160;&#160;<br />Kiépítés<br />&#160;&#160;&#160;&#160;a lemez elérésének engedélyezése<br />&#160;&#160;&#160;&#160;a fájlok elérésének engedélyezése<br />&#160;&#160;&#160;&#160;írásvédett lemezes hozzáférés engedélyezése<br />&#160;&#160;&#160;&#160;a virtuális gép letöltésének engedélyezése<br />&#160;&#160;&#160;&#160;klónozási sablon<br />&#160;&#160;&#160;&#160;virtuális gép klónozása<br />&#160;&#160;&#160;&#160;sablon létrehozása virtuális gépről<br />Vendég &#160;&#160;&#160;&#160;testreszabása<br />Sablon &#160;&#160;&#160;&#160;üzembe helyezése<br />&#160;&#160;&#160;&#160;megjelölés sablonként<br />&#160;&#160;&#160;&#160;testreszabási specifikáció módosítása<br />&#160;&#160;&#160;&#160;lemezek előléptetése<br />&#160;&#160;&#160;&#160;olvasási testreszabási specifikáció<br />Szolgáltatáskonfiguráció<br />Értesítések &#160;&#160;&#160;&#160;engedélyezése<br />A globális eseményekre vonatkozó értesítések lekérdezésének &#160;&#160;&#160;&#160;engedélyezése<br />&#160;&#160;&#160;&#160;a szolgáltatás konfigurációjának kezelése<br />&#160;&#160;&#160;&#160;a szolgáltatás konfigurációjának módosítása<br />&#160;&#160;&#160;&#160;lekérdezési szolgáltatás konfigurációi<br />&#160;&#160;&#160;&#160;olvasási szolgáltatás konfigurációja<br />Pillanatképek kezelése<br />Pillanatkép létrehozása &#160;&#160;&#160;&#160;<br />Pillanatkép &#160;&#160;&#160;&#160;eltávolítása<br />&#160;&#160;&#160;&#160;pillanatkép átnevezése<br />&#160;&#160;&#160;&#160;a pillanatkép visszaállítása<br />vSphere-replikáció<br />&#160;&#160;&#160;&#160;replikáció konfigurálása<br />&#160;&#160;&#160;&#160;a replikáció kezelése<br />&#160;&#160;&#160;&#160;a replikáció figyelése |
| **vService** | Függőség létrehozása<br />Függőség megsemmisítése<br />Függőség konfigurációjának újrakonfigurálása<br />Frissítési függőség |
| **vSphere címkézése** | VSphere-címke hozzárendelésének és hozzárendelésének megszüntetése<br />VSphere címke létrehozása<br />VSphere-címke kategória létrehozása<br />VSphere-címke törlése<br />VSphere törlése<br />VSphere címke szerkesztése<br />VSphere szerkesztése címke kategóriája<br />Kategória UsedBy-mezőjének módosítása<br />Címke UsedBy-mezőjének módosítása |

### <a name="create-custom-roles-on-vcenter"></a>Egyéni szerepkörök létrehozása a vCenter-on

Az Azure VMware-megoldás támogatja a CloudAdmin szerepkörrel egyenlő vagy kisebb jogosultságokkal rendelkező egyéni szerepkörök használatát. 

A CloudAdmin szerepkör olyan egyéni szerepköröket hozhat létre, módosíthat vagy törölhet, amelyek az aktuális szerepkörnél kisebb vagy azzal egyenlő jogosultságokkal rendelkeznek. Előfordulhat, hogy olyan szerepköröket hozhat létre, amelyek a CloudAdmin-nál nagyobb jogosultságokkal rendelkeznek, de nem tudja hozzárendelni a szerepkört bármely felhasználóhoz vagy csoporthoz, vagy törölni a szerepkört.

A nem hozzárendelhető vagy nem törölhető szerepkörök létrehozásának megakadályozása érdekében az Azure VMware-megoldás azt javasolja, hogy a CloudAdmin szerepkör klónozása az új egyéni szerepkörök létrehozása alapján történjen.

#### <a name="create-a-custom-role"></a>Egyéni szerepkör létrehozása
1. Jelentkezzen be a vCenter cloudadmin \@ vSphere. local vagy a cloudadmin szerepkörrel rendelkező felhasználóval.
2. Navigáljon a **szerepkörök** konfiguráció szakaszhoz, és válassza a **menü**  >  **Adminisztráció**  >  **Access Control**  >  **szerepkörök** lehetőséget.
3. Válassza ki a **CloudAdmin** szerepkört, és válassza a **klón szerepkör művelet** ikont.

   > [!NOTE] 
   > Ne klónozása a **rendszergazdai** szerepkört. Ez a szerepkör nem használható, és a létrehozott egyéni szerepkör nem törölhető a cloudadmin \@ vSphere. local használatával.

4. Adja meg a klónozott szerepkörhöz használni kívánt nevet.
5. Adja hozzá vagy távolítsa el a szerepkörhöz tartozó jogosultságokat, majd kattintson **az OK gombra**. A klónozott szerepkörnek most már láthatónak kell lennie a **szerepkörök** listájában.


#### <a name="use-a-custom-role"></a>Egyéni szerepkör használata

1. Navigáljon a hozzáadott engedélyt igénylő objektumhoz. Ha például egy mappára szeretné alkalmazni az engedélyt, navigáljon a   >  **virtuális gépek és a sablonok**  >  **mappa neve** elemre.
1. Kattintson a jobb gombbal az objektumra, és válassza az **engedély hozzáadása** elemet.
1. Az **engedély hozzáadása** ablakban válassza ki az identitás forrását a **felhasználó** legördülő menüből, ahol a csoport vagy a felhasználó megtalálható.
1. Keresse meg a felhasználót vagy a csoportot, miután kiválasztotta az identitás forrását a **felhasználó** szakaszban. 
1. Válassza ki azt a szerepkört, amelyet alkalmazni kíván a felhasználó vagy csoport számára.
1. Ha szükséges, jelölje be a **propagálás a gyermekek számára** jelölőnégyzetet, majd kattintson **az OK gombra**.
   A hozzáadott engedély az objektum **engedélyek** szakaszában jelenik meg.

## <a name="nsx-t-manager-access-and-identity"></a>NSX – T Manager-hozzáférés és-identitás

Az NSX-T kezelőjének eléréséhez használja a *rendszergazdai* fiókot. Teljes körű jogosultságokkal rendelkezik, és lehetővé teszi az 1. szintű (T1) átjárók, szegmensek (logikai kapcsolók) és minden szolgáltatás létrehozását és felügyeletét. A jogosultságok hozzáférést biztosítanak az NSX-T réteg-0 (T0) átjáróhoz. A t0-átjáró módosítása a hálózati teljesítmény romlása vagy a saját Felhőbeli hozzáférés nélkül is csökkenhet. Nyisson meg egy támogatási kérést a Azure Portal az NSX-T T0-átjáró módosításainak igényléséhez.

 
## <a name="next-steps"></a>Következő lépések

Most, hogy lefedette az Azure VMware-megoldás hozzáférési és identitási fogalmait, érdemes megismernie az alábbiakat:

- [Saját Felhőbeli frissítési fogalmak](concepts-upgrades.md).
- [Az Azure VMware megoldás erőforrásának engedélyezése](enable-azure-vmware-solution.md).
- [Az egyes jogosultságok részletei](https://docs.vmware.com/en/VMware-vSphere/7.0/com.vmware.vsphere.security.doc/GUID-ED56F3C4-77D0-49E3-88B6-B99B8B437B62.html).
- [Hogyan figyeli és javítja az Azure VMware-megoldás a privát felhőket](concepts-monitor-repair-private-cloud.md).
- [Az Azure VMware megoldás erőforrásának engedélyezése](enable-azure-vmware-solution.md).


<!-- LINKS - external-->
[VMware product documentation]: https://docs.vmware.com/en/VMware-vSphere/7.0/com.vmware.vsphere.security.doc/GUID-ED56F3C4-77D0-49E3-88B6-B99B8B437B62.html

<!-- LINKS - internal -->
[concepts-upgrades]: ./concepts-upgrades.md
