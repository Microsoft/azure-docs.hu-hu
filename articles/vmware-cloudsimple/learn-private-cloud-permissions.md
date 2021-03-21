---
title: Azure VMware-megoldás CloudSimple-Private Cloud Permission Model
description: A CloudSimple saját felhőalapú engedélyezési modelljét, csoportjait és kategóriáit ismerteti
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 1c8cfeda008955006f2fbad1df58c8047bd36541
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97898045"
---
# <a name="cloudsimple-private-cloud-permission-model-of-vmware-vcenter"></a>A VMware vCenter CloudSimple saját felhőalapú engedélyezési modellje

A CloudSimple teljes körű rendszergazdai hozzáférést tart fenn a saját felhőalapú környezethez. Minden CloudSimple-ügyfél megfelelő rendszergazdai jogosultságokkal rendelkezik ahhoz, hogy a virtuális gépeket a környezetében lehessen üzembe helyezni és felügyelni.  Ha szükséges, átmenetileg kiterjesztheti a jogosultságokat a rendszergazdai funkciók végrehajtásához.

## <a name="cloud-owner"></a>Felhő tulajdonosa

Amikor létrehoz egy privát felhőt, a rendszer létrehoz egy **CloudOwner** -felhasználót a vCenter egyetlen Sign-On tartományban, és a **Felhőbeli tulajdonosi szerepkör** -hozzáféréssel felügyeli az objektumokat a privát felhőben. Ez a felhasználó további [vCenter-identitási forrásokat](set-vcenter-identity.md)és más felhasználókat is beállíthat a saját Felhőbeli vCenter.

> [!NOTE]
> Az CloudSimple saját Felhőbeli vCenter alapértelmezett felhasználója cloudowner@cloudsimple.local a privát felhő létrehozásakor.

## <a name="user-groups"></a>Felhasználói csoportok

Egy **Cloud-Owner-Group** nevű csoport jön létre egy privát felhő üzembe helyezésekor. A csoport felhasználói a vSphere-környezet különböző részeit felügyelhetik a privát felhőben. Ez a csoport automatikusan megkapja a  **felhőalapú tulajdonosi szerepkörhöz** tartozó jogosultságokat, és a  **CloudOwner** -felhasználót a csoport tagjaként adja hozzá a rendszer.  A CloudSimple további, korlátozott jogosultságokkal rendelkező csoportokat hoz létre a könnyű kezelés érdekében.  Hozzáadhat felhasználókat az előre létrehozott csoportokhoz, és az alább megadott jogosultságokat a rendszer automatikusan hozzárendeli a csoportokhoz tartozó felhasználókhoz.

### <a name="pre-created-groups"></a>Előre létrehozott csoportok

| Csoportnév | Cél | Szerepkör |
| -------- | ------- | ------ |
| Felhőbeli tulajdonos – csoport | Ennek a csoportnak a tagjai rendszergazdai jogosultságokkal rendelkeznek a saját felhőalapú vCenter | [Felhőbeli tulajdonos – szerepkör](#cloud-owner-role) |
| Felhő-globális-fürt-rendszergazda-csoport | Ennek a csoportnak a tagjai rendszergazdai jogosultságokkal rendelkeznek a Private Cloud vCenter-fürtön | [Felhő – fürt – rendszergazda – szerepkör](#cloud-cluster-admin-role) |
| Cloud-Global-Storage-felügyeleti csoport | Ennek a csoportnak a tagjai kezelhetik a tárhelyet a saját felhő vCenter | [Felhőalapú tárolás – rendszergazda – szerepkör](#cloud-storage-admin-role) |
| Cloud-Global-Network-admin-Group | Ennek a csoportnak a tagjai kezelhetik a hálózat és az elosztott portok csoportját a saját felhőalapú vCenter | [Cloud-Network-admin-role](#cloud-network-admin-role) |
| Cloud-Global-VM-admin-Group | A csoport tagjai kezelhetik a virtuális gépeket a saját felhőalapú vCenter | [Cloud-VM-admin-role](#cloud-vm-admin-role) |

Ha az egyes felhasználók számára engedélyezni szeretné a saját felhő felügyeletét, hozzon létre felhasználói fiókokat a megfelelő csoportokhoz való hozzáadáshoz.

> [!CAUTION]
> Az új felhasználókat csak a *Cloud-Owner-Group*, a *Cloud-Global-cluster-admin-Group*, a *Cloud-Global-Storage-admin-Group*, a *Cloud-Global-Network-admin-Group* vagy a *Cloud-Global-VM-admin-Group* szolgáltatáshoz kell hozzáadni.  A *rendszergazdák* csoportba felvett felhasználók automatikusan el lesznek távolítva.  A vSphere webes felhasználói felületén csak a szolgáltatási fiókokat kell felvenni a *rendszergazdák* csoportjába, és a szolgáltatásfiókok nem használhatók.

## <a name="list-of-vcenter-privileges-for-default-roles"></a>Az alapértelmezett szerepkörökhöz tartozó vCenter-jogosultságok listája

### <a name="cloud-owner-role"></a>Felhőbeli tulajdonos – szerepkör

| **Kategória** | **Privilege** |
|----------|-----------|
| **Riasztások** | Riasztás nyugtázása <br> Riasztás létrehozása <br> Riasztási művelet letiltása <br> Riasztás módosítása <br> Riasztás eltávolítása <br> Riasztás állapotának beállítása |
| **Engedélyek** | Módosítási engedély |
| **Tartalomtár** | Függvénytár-elem hozzáadása <br> Helyi könyvtár létrehozása <br> Előfizetett tár létrehozása <br> Függvénytár-elemek törlése <br> Helyi könyvtár törlése <br> Előfizetett tár törlése <br> Fájlok letöltése <br> Függvénytár-elemek kizárása <br> Előfizetett tár kizárása <br> Tároló importálása <br> Mintavételi előfizetés adatai <br> Tár olvasása <br> Könyvtár-elemek szinkronizálása <br> Előfizetett tár szinkronizálása <br> Betekintés típusa <br> Konfigurációs beállítások frissítése <br> Fájlok frissítése <br> Könyvtár frissítése <br> Függvénytár-elemek frissítése <br> Helyi könyvtár frissítése <br> Előfizetett tár frissítése <br> Konfigurációs beállítások megtekintése |
| **Titkosítási műveletek** | Lemez hozzáadása <br> Klónozás <br> Visszafejtés <br> Direct Access <br> Titkosítás <br> Új titkosítás <br> KMS kezelése <br> Titkosítási házirendek kezelése <br> Kulcsok kezelése <br> Migrate <br> Recrypt <br> Virtuális gép regisztrálása <br> Gazdagép regisztrálása |
| **dvPort csoport** | Létrehozás <br> Törlés <br> Módosítás <br> Házirend-művelet <br> Hatókör-művelet |
| **Adattár** | Tárterület foglalása <br> Adattár tallózása <br> Adattár konfigurálása <br> Alacsony szintű fájlok műveletei <br> Adattár áthelyezése <br> Adattár eltávolítása <br> Fájl eltávolítása <br> Adattár átnevezése <br> Virtuálisgép-fájlok frissítése <br> Virtuális gép metaadatainak frissítése |
| **ESX-ügynök kezelője** | Konfigurálás <br> Módosítás <br> Nézet |
| **Mellék** | Bővítmény regisztrálása <br> Bővítmény regisztrációjának törlése <br> Bővítmény frissítése |
| **Külső statisztikai szolgáltató**| Regisztráció <br> Regisztrálás törlése <br> Frissítés |
| **Mappa** | Mappa létrehozása <br> Mappa törlése <br> Mappa áthelyezése <br> Mappa átnevezése |
| **Globális** | Feladat megszakítása <br> Kapacitástervezés <br> Diagnosztika <br> Metódusok letiltása <br> Metódusok engedélyezése <br> Globális címke <br> Egészségügy <br> Licencek <br> Esemény naplózása <br> Egyéni attribútumok kezelése <br> Proxy <br> Parancsfájl művelete <br> Service managerek <br> Egyéni attribútum beállítása <br> Rendszercímke |
| **Állapot-frissítési szolgáltató** | Regisztráció <br> Regisztrálás törlése <br> Frissítés |
| **Gazdagép > konfigurálása** | Tárolási partíció konfigurációja |
| **Gazdagép > leltározása** | Fürt módosítása |
| **vSphere címkézése** | VSphere-címke hozzárendelésének vagy hozzárendelésének megszüntetése <br> VSphere címke létrehozása <br> VSphere-címke kategória létrehozása <br> VSphere-címke törlése <br> VSphere törlése <br> VSphere címke szerkesztése <br> VSphere szerkesztése címke kategóriája <br> Kategória UsedBy-mezőjének módosítása <br> Címke UsedBy-mezőjének módosítása |
| **Hálózat** | Hálózat kiosztása <br> Konfigurálás <br> Hálózat áthelyezése <br> Eltávolítás |
| **Teljesítmény** | Intervallumok módosítása |
| **Gazdagép profilja** | Nézet |
| **Erőforrás** | Javaslat alkalmazása <br> VApp erőforrás-készlethez rendelése <br> Virtuális gép erőforrás-készlethez rendelése <br> Erőforráskészlet létrehozása <br> Virtuális gép migrálása <br> Migrálás virtuális gépen <br> Erőforráskészlet módosítása <br> Erőforráskészlet áthelyezése <br> VMotion lekérdezése <br> Erőforráskészlet eltávolítása <br> Erőforráskészlet átnevezése |
| **Ütemezett feladat** | Tevékenységek létrehozása <br> Feladat módosítása <br> Feladat eltávolítása <br> Feladat futtatása |
| **Munkamenetek** | Felhasználó megszemélyesítése <br> Üzenet <br> Munkamenet ellenőrzése <br> Munkamenetek megtekintése és leállítása |
| **Adattár-fürt** | Adattár-fürt konfigurálása |
| **Profil alapú tárolás** | Profil-vezérelt tároló frissítése <br> Profil alapú tárolási nézet |
| **Tárolási nézetek** | Szolgáltatás konfigurálása <br> Nézet |
| **Feladatok** | Feladat létrehozása <br> Feladat frissítése |
| **Szállítási szolgáltatás**| Kezelés <br> Monitor |
| **vApp** | Virtuális gép hozzáadása <br> Erőforráskészlet kiosztása <br> VApp-hozzárendelés <br> Klónozás <br> Létrehozás <br> Törlés <br> Exportálás <br> Importálás <br> Áthelyezés <br> Kikapcsolás <br> Bekapcsolás <br> Átnevezés <br> Felfüggesztés <br> Regisztrálás törlése <br> OVF-környezet megtekintése <br> vApp-alkalmazás konfigurációja <br> vApp-példány konfigurációja <br> vApp többé-konfiguráció <br> vApp erőforrás-konfiguráció |
| **VRMPolicy** | VRMPolicy lekérdezése <br> VRMPolicy frissítése |
| **Virtuális gép > konfigurációja** | Meglévő lemez hozzáadása <br> Új lemez hozzáadása <br> Eszköz hozzáadása vagy eltávolítása <br> Felsőfokú <br> CPU-szám módosítása <br> Erőforrás módosítása <br> Többé konfigurálása <br> Lemez változásának követése <br> Lemez bérlete <br> Kapcsolatbeállítások megjelenítése <br> Virtuális lemez kiterjesztése <br> Gazdagép USB-eszköze <br> Memória <br> Eszközbeállítások módosítása <br> Hibatűrő kompatibilitási lekérdezés <br> A nem birtokolt fájlok lekérdezése <br> Nyers eszköz <br> Újratöltés az elérési útról <br> Lemez eltávolítása <br> Átnevezés <br> Vendég adatainak alaphelyzetbe állítása <br> Jegyzet beállítása <br> Beállítások <br> Swapfile elhelyezése <br> Elágazás szülőjének váltása <br> Virtuális gép feloldása <br> A virtuális gépek kompatibilitásának frissítése |
| **Virtuális gép > vendég műveletei** | Vendég műveleti alias módosítása <br> Vendég műveleti alias lekérdezése <br> Vendég művelet módosításai <br> Vendég műveleti program végrehajtása <br> Vendég műveleti lekérdezések |
| **Virtuális gép > interakció** | Válasz kérdése <br> Biztonsági mentési művelet a virtuális gépen <br> CD-adathordozó konfigurálása <br> Hajlékonylemezes adathordozó konfigurálása <br> Konzol interakciója <br> Képernyőkép létrehozása <br> Az összes lemez töredezettségmentesítése <br> Eszköz csatlakoztatása <br> Drag and drop <br> Vendég operációs rendszer felügyelete a VIX API-val <br> USB-HID-beolvasó kódok behelyezése <br> Szüneteltetés vagy szüneteltetés <br> Törlési vagy zsugorodási műveletek végrehajtása <br> Kikapcsolás <br> Bekapcsolás <br> Munkamenet rögzítése a virtuális gépen <br> Visszajátszás-munkamenet a virtuális gépen <br> Alaphelyzetbe állítás <br> Hibatűrés folytatása <br> Felfüggesztés <br> Hibatűrés felfüggesztése <br> Feladatátvétel tesztelése <br> Másodlagos virtuális gép újraindításának tesztelése <br> Hibatűrés kikapcsolása <br> Hibatűrés bekapcsolása <br> VMware-eszközök telepítése |
| **Virtuális gép > leltározása** | Létrehozás meglévőből <br> Új létrehozása <br> Áthelyezés <br> Regisztráció <br> Eltávolítás <br> Regisztrálás törlése |
| **Virtuális gép > kiépítés** | Lemezes hozzáférés engedélyezése <br> Fájl-hozzáférés engedélyezése <br> Írásvédett lemezes hozzáférés engedélyezése <br> Virtuális gép letöltésének engedélyezése <br> Virtuális gépek fájljainak feltöltésének engedélyezése <br> Sablon klónozása <br> Virtuális gép klónozása <br> Sablon létrehozása virtuális gépről <br> Testreszabás <br> Sablon üzembe helyezése <br> Megjelölés sablonként <br> Megjelölés virtuális gépnek <br> Testreszabási specifikáció módosítása <br> Lemezek előléptetése <br> Testreszabási specifikációk olvasása |
| **Virtuális gép > szolgáltatás konfigurációja** | Értesítések engedélyezése <br> Globális események értesítéseinek lekérdezésének engedélyezése <br> Szolgáltatási konfigurációk kezelése <br> Szolgáltatás konfigurációjának módosítása <br> Lekérdezési szolgáltatás konfigurációi <br> Szolgáltatás konfigurációjának olvasása |
| **Virtuális gép > Pillanatképek kezelése** | Pillanatkép készítése <br> Pillanatkép eltávolítása <br> Pillanatkép átnevezése <br> Helyreállítás pillanatképre |
| **Virtuális gép > vSphere-replikáció** | Replikáció konfigurálása <br> Replikáció kezelése <br> Replikáció monitorozása |
| **vService** | Függőség létrehozása <br> Függőség megsemmisítése <br> Függőség konfigurációjának újrakonfigurálása <br> Frissítési függőség |

### <a name="cloud-cluster-admin-role"></a>Felhő – fürt – rendszergazda – szerepkör

| **Kategória** | **Privilege** |
|----------|-----------|
| **Adattár** | Tárterület foglalása <br> Adattár tallózása <br> Adattár konfigurálása <br> Alacsony szintű fájlok műveletei <br> Adattár eltávolítása <br> Adattár átnevezése <br> Virtuálisgép-fájlok frissítése <br> Virtuális gép metaadatainak frissítése |
| **Mappa** | Mappa létrehozása <br> Mappa törlése <br> Mappa áthelyezése <br> Mappa átnevezése |
| **Gazdagép > konfigurálása**  | Tárolási partíció konfigurációja |
| **vSphere címkézése** | VSphere-címke hozzárendelésének vagy hozzárendelésének megszüntetése <br> VSphere címke létrehozása <br> VSphere-címke kategória létrehozása <br> VSphere-címke törlése <br> VSphere törlése <br> VSphere címke szerkesztése <br> VSphere szerkesztése címke kategóriája <br> Kategória UsedBy-mezőjének módosítása <br> Címke UsedBy-mezőjének módosítása |
| **Hálózat** | Hálózat kiosztása |
| **Erőforrás** | Javaslat alkalmazása <br> VApp erőforrás-készlethez rendelése <br> Virtuális gép erőforrás-készlethez rendelése <br> Erőforráskészlet létrehozása <br> Virtuális gép migrálása <br> Migrálás virtuális gépen <br> Erőforráskészlet módosítása <br> Erőforráskészlet áthelyezése <br> VMotion lekérdezése <br> Erőforráskészlet eltávolítása <br> Erőforráskészlet átnevezése |
| **vApp** | Virtuális gép hozzáadása <br> Erőforráskészlet kiosztása <br> VApp-hozzárendelés <br> Klónozás <br> Létrehozás <br> Törlés <br> Exportálás <br> Importálás <br> Áthelyezés <br> Kikapcsolás <br> Bekapcsolás <br> Átnevezés <br> Felfüggesztés <br> Regisztrálás törlése <br> OVF-környezet megtekintése <br> vApp-alkalmazás konfigurációja <br> vApp-példány konfigurációja <br> vApp többé-konfiguráció <br> vApp erőforrás-konfiguráció |
| **VRMPolicy** | VRMPolicy lekérdezése <br> VRMPolicy frissítése |
| **Virtuális gép > konfigurációja** | Meglévő lemez hozzáadása <br> Új lemez hozzáadása <br> Eszköz hozzáadása vagy eltávolítása <br> Felsőfokú <br> CPU-szám módosítása <br> Erőforrás módosítása <br> Többé konfigurálása <br> Lemez változásának követése <br> Lemez bérlete <br> Kapcsolatbeállítások megjelenítése <br> Virtuális lemez kiterjesztése <br> Gazdagép USB-eszköze <br> Memória <br> Eszközbeállítások módosítása <br> Hibatűrő kompatibilitási lekérdezés <br> A nem birtokolt fájlok lekérdezése <br> Nyers eszköz <br> Újratöltés az elérési útról <br> Lemez eltávolítása <br> Átnevezés <br> Vendég adatainak alaphelyzetbe állítása <br> Jegyzet beállítása <br> Beállítások <br> Swapfile elhelyezése <br> Elágazás szülőjének váltása <br> Virtuális gép feloldása <br> A virtuális gépek kompatibilitásának frissítése |
| **Virtuális gép > vendég műveletei** | Vendég műveleti alias módosítása <br> Vendég műveleti alias lekérdezése <br> Vendég művelet módosításai <br> Vendég műveleti program végrehajtása <br> Vendég műveleti lekérdezések |
| **Virtuális gép > interakció** | Válasz kérdése <br> Biztonsági mentési művelet a virtuális gépen <br> CD-adathordozó konfigurálása <br> Hajlékonylemezes adathordozó konfigurálása <br> Konzol interakciója <br> Képernyőkép létrehozása <br> Az összes lemez töredezettségmentesítése <br> Eszköz csatlakoztatása <br> Drag and drop <br> Vendég operációs rendszer felügyelete a VIX API-val <br> USB-HID-beolvasó kódok behelyezése <br> Szüneteltetés vagy szüneteltetés <br> Törlési vagy zsugorodási műveletek végrehajtása <br> Kikapcsolás <br> Bekapcsolás <br> Munkamenet rögzítése a virtuális gépen <br> Visszajátszás-munkamenet a virtuális gépen <br> Alaphelyzetbe állítás <br> Hibatűrés folytatása <br> Felfüggesztés <br> Hibatűrés felfüggesztése <br> Feladatátvétel tesztelése <br> Másodlagos virtuális gép újraindításának tesztelése <br> Hibatűrés kikapcsolása <br> Hibatűrés bekapcsolása <br> VMware-eszközök telepítése
| **Virtuális gép > leltározása** | Létrehozás meglévőből <br> Új létrehozása <br> Áthelyezés <br> Regisztráció <br> Eltávolítás <br> Regisztrálás törlése |
| **Virtuális gép > kiépítés** | Lemezes hozzáférés engedélyezése <br> Fájl-hozzáférés engedélyezése <br> Írásvédett lemezes hozzáférés engedélyezése <br> Virtuális gép letöltésének engedélyezése <br> Virtuális gépek fájljainak feltöltésének engedélyezése <br> Sablon klónozása <br> Virtuális gép klónozása <br> Sablon létrehozása virtuális gépről <br> Testreszabás <br> Sablon üzembe helyezése <br> Megjelölés sablonként <br> Megjelölés virtuális gépnek <br> Testreszabási specifikáció módosítása <br> Lemezek előléptetése  <br> Testreszabási specifikációk olvasása |
| **Virtuális gép > szolgáltatás konfigurációja** | Értesítések engedélyezése <br> Globális események értesítéseinek lekérdezésének engedélyezése <br> Szolgáltatási konfigurációk kezelése <br> Szolgáltatás konfigurációjának módosítása <br> Lekérdezési szolgáltatás konfigurációi <br> Szolgáltatás konfigurációjának olvasása
| **Virtuális gép > Pillanatképek kezelése** | Pillanatkép készítése <br> Pillanatkép eltávolítása <br> Pillanatkép átnevezése <br> Helyreállítás pillanatképre |
| **Virtuális gép > vSphere-replikáció** | Replikáció konfigurálása <br> Replikáció kezelése <br> Replikáció monitorozása |
| **vService** | Függőség létrehozása <br> Függőség megsemmisítése <br> Függőség konfigurációjának újrakonfigurálása <br> Frissítési függőség |

### <a name="cloud-storage-admin-role"></a>Felhőalapú tárolás – rendszergazda – szerepkör

| **Kategória** | **Privilege** |
|----------|-----------|
| **Adattár** | Tárterület foglalása <br> Adattár tallózása <br> Adattár konfigurálása <br> Alacsony szintű fájlok műveletei <br> Adattár eltávolítása <br> Adattár átnevezése <br> Virtuálisgép-fájlok frissítése <br> Virtuális gép metaadatainak frissítése |
| **Gazdagép > konfigurálása** | Tárolási partíció konfigurációja |
| **Adattár-fürt** | Adattár-fürt konfigurálása |
| **Profil alapú tárolás** | Profil-vezérelt tároló frissítése <br> Profil alapú tárolási nézet |
| **Tárolási nézetek** | Szolgáltatás konfigurálása <br> Nézet |

### <a name="cloud-network-admin-role"></a>Cloud-Network-admin-role

| **Kategória** | **Privilege** |
|----------|-----------|
| **dvPort csoport** | Létrehozás <br> Törlés <br> Módosítás <br> Házirend-művelet <br> Hatókör-művelet |
| **Hálózat** | Hálózat kiosztása <br> Konfigurálás <br> Hálózat áthelyezése <br> Eltávolítás |
| **Virtuális gép > konfigurációja** | Eszközbeállítások módosítása |

### <a name="cloud-vm-admin-role"></a>Cloud-VM-admin-role

| **Kategória** | **Privilege** |
|----------|-----------|
| **Adattár** | Tárterület foglalása <br> Adattár tallózása |
| **Hálózat** | Hálózat kiosztása |
| **Erőforrás** | Virtuális gép erőforrás-készlethez rendelése <br> Virtuális gép migrálása <br> Migrálás virtuális gépen
| **vApp** | Exportálás <br> Importálás |
| **Virtuális gép > konfigurációja** | Meglévő lemez hozzáadása <br> Új lemez hozzáadása <br> Eszköz hozzáadása vagy eltávolítása <br> Felsőfokú <br> CPU-szám módosítása <br> Erőforrás módosítása <br> Többé konfigurálása <br> Lemez változásának követése <br> Lemez bérlete <br> Kapcsolatbeállítások megjelenítése <br> Virtuális lemez kiterjesztése <br> Gazdagép USB-eszköze <br> Memória <br> Eszközbeállítások módosítása <br> Hibatűrő kompatibilitási lekérdezés <br> A nem birtokolt fájlok lekérdezése <br> Nyers eszköz <br> Újratöltés az elérési útról <br> Lemez eltávolítása <br> Átnevezés <br> Vendég adatainak alaphelyzetbe állítása <br> Jegyzet beállítása <br> Beállítások <br> Swapfile elhelyezése <br> Elágazás szülőjének váltása <br> Virtuális gép feloldása <br> A virtuális gépek kompatibilitásának frissítése |
| **Virtuális gép >vendég műveletei** | Vendég műveleti alias módosítása <br> Vendég műveleti alias lekérdezése <br> Vendég művelet módosításai <br> Vendég műveleti program végrehajtása <br> Vendég műveleti lekérdezések    |
| **Virtuális gép >interakció** | Válasz kérdése <br> Biztonsági mentési művelet a virtuális gépen <br> CD-adathordozó konfigurálása <br> Hajlékonylemezes adathordozó konfigurálása <br> Konzol interakciója <br> Képernyőkép létrehozása <br> Az összes lemez töredezettségmentesítése <br> Eszköz csatlakoztatása <br> Drag and drop <br> Vendég operációs rendszer felügyelete a VIX API-val <br> USB-HID-beolvasó kódok behelyezése <br> Szüneteltetés vagy szüneteltetés <br> Törlési vagy zsugorodási műveletek végrehajtása <br> Kikapcsolás <br> Bekapcsolás <br> Munkamenet rögzítése a virtuális gépen <br> Visszajátszás-munkamenet a virtuális gépen <br> Alaphelyzetbe állítás <br> Hibatűrés folytatása <br> Felfüggesztés <br> Hibatűrés felfüggesztése <br> Feladatátvétel tesztelése <br> Másodlagos virtuális gép újraindításának tesztelése <br> Hibatűrés kikapcsolása <br> Hibatűrés bekapcsolása <br> VMware-eszközök telepítése |
| **Virtuális gép >leltározása** | Létrehozás meglévőből <br> Új létrehozása <br> Áthelyezés <br> Regisztráció <br> Eltávolítás <br> Regisztrálás törlése |
| **Virtuális gép >kiépítés** | Lemezes hozzáférés engedélyezése <br> Fájl-hozzáférés engedélyezése <br> Írásvédett lemezes hozzáférés engedélyezése <br> Virtuális gép letöltésének engedélyezése <br> Virtuális gépek fájljainak feltöltésének engedélyezése <br> Sablon klónozása <br> Virtuális gép klónozása <br> Sablon létrehozása virtuális gépről <br> Testreszabás <br> Sablon üzembe helyezése <br> Megjelölés sablonként <br> Megjelölés virtuális gépnek <br> Testreszabási specifikáció módosítása <br> Lemezek előléptetése <br> Testreszabási specifikációk olvasása |
| **Virtuális gép >szolgáltatás konfigurációja** | Értesítések engedélyezése <br> Globális események értesítéseinek lekérdezésének engedélyezése <br> Szolgáltatási konfigurációk kezelése <br> Szolgáltatás konfigurációjának módosítása <br> Lekérdezési szolgáltatás konfigurációi <br> Szolgáltatás konfigurációjának olvasása
| **Virtuális gép >Pillanatképek kezelése** | Pillanatkép készítése <br> Pillanatkép eltávolítása <br> Pillanatkép átnevezése <br> Helyreállítás pillanatképre |
| **Virtuális gép >vSphere-replikáció** | Replikáció konfigurálása <br> Replikáció kezelése <br> Replikáció monitorozása |
| **vService** | Függőség létrehozása <br> Függőség megsemmisítése <br> Függőség konfigurációjának újrakonfigurálása <br> Frissítési függőség |
