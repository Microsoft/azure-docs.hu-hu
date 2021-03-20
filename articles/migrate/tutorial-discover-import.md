---
title: Helyszíni kiszolgálók értékelése az importált CSV-fájllal Azure Migrate Server Assessment használatával
description: Ismerteti, hogyan lehet felderíteni a helyszíni kiszolgálókat az Azure-ba való áttelepítéshez egy importált CSV-fájllal Azure Migrate Server Assessment használatával
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 09/14/2020
ms.openlocfilehash: dfa7ee941e2c373b02fe5fb2f2a648a60a677670
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96753109"
---
# <a name="tutorial-assess-servers-using-an-imported-csv-file"></a>Oktatóanyag: kiszolgálók értékelése importált CSV-fájl használatával

Az Azure-ba való Migrálás részeként felderítheti a helyszíni leltárt és munkaterheléseket. 

Ebből az oktatóanyagból megtudhatja, hogyan értékelheti a helyszíni gépeket a Azure Migrate: Server Assessment Tool eszközzel, az importált vesszővel elválasztott értékeket tartalmazó (CSV) fájl használatával. 

Ha CSV-fájlt használ, nem kell beállítania a Azure Migrate berendezést a kiszolgálók felderítéséhez és értékeléséhez. Megadhatja a fájlban megosztott adatmennyiséget, és az adatmennyiség nagy része nem kötelező. Ez a módszer a következő esetekben hasznos:

- Gyors, kezdeti felmérést szeretne végezni a berendezés üzembe helyezése előtt.
- Nem tudja a szervezetében üzembe helyezni az Azure Migrate-berendezést.
- Nem tudja megosztani a helyszíni kiszolgálókhoz való hozzáférés engedélyezéséhez szükséges hitelesítő adatokat.
- A biztonsági korlátozások megakadályozzák, hogy a berendezés adatokat gyűjtsön, és hogy ezeket az adatokat elküldje az Azure-ba.

> [!NOTE]
> A CSV-fájllal importált kiszolgálók nem telepíthetők át.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Azure-fiók beállítása
> * Azure Migrate-projekt beállítása
> * CSV-fájl előkészítése
> * A fájl importálása
> * Kiszolgálók értékelése

> [!NOTE]
> Az oktatóanyagok a forgatókönyvek kipróbálásának leggyorsabb útvonalát mutatják be, és az alapértelmezett beállításokat használják, ahol lehetséges. 

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).

## <a name="prerequisites"></a>Előfeltételek

- Legfeljebb 20 000 kiszolgálót adhat hozzá egyetlen CSV-fájlban és egy Azure Migrate-projektben. 
- A CSV-fájlban megadott operációsrendszer-neveknek tartalmaznia kell és meg kell egyezniük a [támogatott nevekkel](#supported-operating-system-names).


## <a name="prepare-an-azure-user-account"></a>Azure-beli felhasználói fiók előkészítése

Azure Migrate projekt létrehozásához az alábbiakkal rendelkező fiókra van szükség:
- Közreműködő vagy tulajdonosi engedélyek egy Azure-előfizetéshez.
- Azure Active Directory alkalmazások regisztrálásához szükséges engedélyek.

Ha most hozott létre egy ingyenes Azure-fiókot, akkor Ön az előfizetés tulajdonosa. Ha nem Ön az előfizetés tulajdonosa, a tulajdonossal együtt az alábbi módon rendelheti hozzá az engedélyeket:

1. A Azure Portal keressen rá az "előfizetések" kifejezésre, és a **szolgáltatások** területen válassza az **előfizetések** lehetőséget.

    ![Az Azure-előfizetés kereséséhez használt keresőmező](./media/tutorial-discover-import/search-subscription.png)

2. Az **előfizetések** lapon válassza ki azt az előfizetést, amelyben Azure Migrate projektet kíván létrehozni. 
3. Az előfizetésben válassza a hozzáférés- **vezérlés (iam)**  >  **jelölőnégyzetet**.
4. A **hozzáférés-ellenőrzési** területen keresse meg a megfelelő felhasználói fiókot.
5. A **szerepkör-hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzáadás** gombra.

    ![Felhasználói fiók keresése a hozzáférés ellenőrzését és a szerepkör hozzárendelését](./media/tutorial-discover-import/azure-account-access.png)

6. A **szerepkör-hozzárendelés hozzáadása** lapon válassza ki a közreműködő vagy a tulajdonos szerepkört, és válassza ki a fiókot (azmigrateuser a példánkban). Ezután kattintson a **Mentés** gombra.

    ![A szerepkör-hozzárendelés hozzáadása lap megnyitása a szerepkör a fiókhoz való hozzárendeléséhez](./media/tutorial-discover-import/assign-role.png)

7. A portálon keressen felhasználókat, és a **szolgáltatások** területen válassza a **felhasználók** lehetőséget.
8. A **felhasználói beállítások** területen ellenőrizze, hogy az Azure ad-felhasználók regisztrálhatják-e az alkalmazásokat (alapértelmezés szerint az **Igen** értékre van állítva).

    ![A felhasználók által Active Directory alkalmazások regisztrálásához használt felhasználói beállítások ellenőrzése](./media/tutorial-discover-import/register-apps.png)



## <a name="set-up-a-project"></a>Projekt beállítása

Hozzon létre egy új Azure Migrate projektet, ha még nem rendelkezik ilyennel.

1. Az Azure Portal > **Minden szolgáltatás** területén keressen az **Azure Migrate** szolgáltatásra.
2. A **Szolgáltatások** területen válassza az **Azure Migrate** lehetőséget.
3. Az **Áttekintés** területen válassza a **projekt létrehozása** lehetőséget.
5. A **projekt létrehozása** lapon válassza ki az Azure-előfizetést és az erőforráscsoportot. Ha nem rendelkezik ilyennel, hozzon létre egy erőforráscsoportot.
6. A **Project details**(projekt részletei) mezőben adja meg a projekt nevét és a földrajzot, amelyben létre kívánja hozni a projektet. Tekintse át a nyilvános és a [kormányzati felhők](migrate-support-matrix.md#supported-geographies-azure-government)támogatott földrajzi [területeit](migrate-support-matrix.md#supported-geographies-public-cloud) .

   ![A projekt neve és a régió mezői](./media/tutorial-discover-import/new-project.png)

7. Válassza a **Létrehozás** lehetőséget.
8. Várjon néhány percet, amíg az Azure Migrate-projekt telepítése megtörténik.

A **Azure Migrate: a Server Assessment** eszköz alapértelmezés szerint hozzá lett adva az új projekthez.

![Az alapértelmezés szerint hozzáadott kiszolgáló-értékelési eszközt megjelenítő oldal](./media/tutorial-discover-import/added-tool.png)

## <a name="prepare-the-csv"></a>A CSV-fájl előkészítése

Töltse le a CSV-sablont, és adja hozzá a kiszolgálói adatokat.

### <a name="download-the-template"></a>A sablon letöltése

1. A **Migrálási célok** > **Kiszolgálók** > **Azure Migrate: Kiszolgáló értékelése** területen válassza a **Felderítés** lehetőséget.
2. A **Gépek felderítése** területen válassza az **Importálás CSV-fájl használatával** lehetőséget.
3. Válassza a **Letöltés** lehetőséget a CSV-sablon letöltéséhez. Másik lehetőségként [közvetlenül is letöltheti](https://go.microsoft.com/fwlink/?linkid=2109031) a sablont.

    ![CSV-sablon letöltése](./media/tutorial-discover-import/download-template.png)

### <a name="add-server-information"></a>Kiszolgálói adatok hozzáadása

Gyűjtse össze a kiszolgálói adatokat, majd adja hozzá őket a CSV-fájlhoz.

- Az adatgyűjtéshez exportálhatja az adatokat a helyszíni kiszolgálófelügyelethez használt eszközökből, ilyenek például a VMware, a vSphere vagy a konfigurációkezelési adatbázis (CMDB).
- A mintaadatok áttekintéséhez töltse le a [példafájlt](https://go.microsoft.com/fwlink/?linkid=2108405).

Az alábbi táblázat a kitöltendő fájlmezőket összegzését tartalmazza:

**Mezőnév** | **Kötelező** | **Részletek**
--- | --- | ---
**Kiszolgáló neve** | Igen | Azt javasoljuk, hogy a teljes tartománynevet (FQDN) adja meg.
**IP-cím** | No | Kiszolgáló címe.
**Cores** | Igen | A kiszolgáló számára lefoglalt processzormagok száma.
**Memória** | Igen | A kiszolgáló számára lefoglalt memória teljes mérete, MB-ban.
**Operációs rendszer neve** | Igen | Kiszolgálói operációs rendszer. <br/> Az értékelés felismeri azon operációsrendszer-neveket, amelyek megegyeznek az [ezen](#supported-operating-system-names) a listán szereplő nevekkel, vagy tartalmazzák őket.
**Operációs rendszer verziója** | No | Kiszolgálói operációs rendszer verziója.
**Operációs rendszer architektúrája** | No | Kiszolgálói operációs rendszer architektúrája <br/> Az érvényes értékek a következők: x64, x86, amd64, 32 bites vagy 64 bites
**Lemezek száma** | No | Nem szükséges, ha az egyes lemezek adatai meg vannak adva.
**1. lemez mérete**  | No | A lemez maximális mérete, GB-ban.<br/>Több lemez adatait is hozzáadhatja, ha [további oszlopokat ad hozzá](#add-multiple-disks) a sablonhoz. Legfeljebb nyolc lemez adható hozzá.
**1. lemez olvasási műveletei** | No | A lemez olvasási műveleteinek száma másodpercenként.
**1. lemez írási műveletei** | No | A lemez írási műveleteinek száma másodpercenként.
**1. lemez olvasási teljesítménye** | No | A lemezről másodpercenként beolvasott adatok mennyisége, MB/s-ben.
**1. lemez írási teljesítménye** | No | A lemezre másodpercenként írt adatok mennyisége, MB/s-ben.
**Processzor százalékos kihasználtsága** | No | A processzorhasználat százalékos aránya.
**Memória százalékos kihasználtsága** | No | A memóriahasználat százalékos aránya.
**Minden lemez olvasási műveletei** | No | Lemezes olvasási műveletek másodpercenként.
**Minden lemez írási műveletei** | No | Lemezes írási műveletek másodpercenként.
**Minden lemez olvasási teljesítménye** | No | A lemezről beolvasott adatok (MB/s).
**Minden lemez írási teljesítménye** | No | Lemezre írt, másodpercenként MB-ban tárolt adatmennyiség.
**Bejövő hálózati teljesítmény** | No | A kiszolgáló által fogadott adatok mennyisége, MB/s-ben.
**Kimenő hálózati teljesítmény** | No | A kiszolgáló által továbbított adatok mennyisége, MB/s-ben.
**Belső vezérlőprogram típusa** | No | A kiszolgáló belső vezérlőprogramja. Az érték „BIOS” vagy „UEFI” lehet.
**MAC-cím**| No | Kiszolgáló MAC-címe.


### <a name="add-operating-systems"></a>Operációs rendszerek hozzáadása

A kiértékelés felismer bizonyos operációsrendszer-neveket. A megadott névnek pontosan meg kell egyeznie a [támogatott nevek listáján](#supported-operating-system-names) szereplő sztringek valamelyikével.

### <a name="add-multiple-disks"></a>Több lemez hozzáadása

A sablon tartalmazza az első lemez alapértelmezett mezőit. Legfeljebb nyolc lemezhez adhat hozzá hasonló oszlopokat.

Például a második lemez minden mezőjének megadásához adja hozzá a következő oszlopokat:

- 2\. lemez mérete
- 2\. lemez olvasási műveletei
- 2\. lemez írási műveletei
- 2\. lemez olvasási teljesítménye
- 2\. lemez írási teljesítménye


## <a name="import-the-server-information"></a>Kiszolgálói adatok importálása

Miután hozzáadta az adatokat a CSV-sablonhoz, importálja a CSV-fájlt a kiszolgáló-értékelésbe.

1. Az Azure Migrate **Gépek felderítése** területén keresse meg a véglegesített sablont.
2. Válassza az **Importálás** lehetőséget.
3. Megjelenik az importálás állapota.
    - Ha figyelmeztetések jelennek meg az állapotban, dönthet úgy, hogy kijavítja őket, de figyelmen kívül is hagyhatja őket, és továbbléphet.
    - A kiértékelés pontosságának javítása érdekében javítsa a kiszolgálói adatokat a figyelmeztetésekben javasolt módon.
    - A figyelmeztetések megtekintéséhez és javításához válassza a **figyelmeztetések részleteit tartalmazó CSV-fájl letöltéséhez** tartozó lehetőséget. Ez a művelet letölti a figyelmeztetéseket tartalmazó CSV-fájlt. Tekintse át a figyelmeztetéseket, és szükség szerint javítsa a hibákat.
    - Ha a hibák jelennek meg az állapotban, amelyek szerint az importálás állapota **Meghiúsult**, akkor az importálás folytatása előtt ki kell javítania ezeket a hibákat:
        1. Töltse le a CSV-fájlt, amely tartalmazza a hiba részleteit.
        1. Szükség szerint tekintse át és kezelje a hibákat. 
        1. Töltse fel ismét a módosított fájlt.
4. Ha az importálás állapota **Befejezve**, akkor a kiszolgálói adatok importálva lettek. Frissítés, ha az importálási folyamat úgy tűnik, hogy nem fejeződött be.

## <a name="update-server-information"></a>Kiszolgálói adatok frissítése

A kiszolgáló adatait úgy frissítheti, ha ugyanazzal a **kiszolgálónévvel** újból importálja a kiszolgálói adatokat. A **Kiszolgáló neve** mező nem módosítható. A kiszolgálók törlése jelenleg nem támogatott.

## <a name="verify-servers-in-the-portal"></a>Kiszolgálók ellenőrzése a portálon

Annak ellenőrzése, hogy a kiszolgálók megjelennek-e az Azure Portalon a felderítés után:

1. Nyissa meg az Azure Migrate irányítópultját.
2. Az **Azure Migrate – Kiszolgálók** > **Azure Migrate: Kiszolgáló értékelése** oldalon kattintson a **felderített kiszolgálók** számát megjelenítő ikonra.
3. Válassza az **Importálásalapú** lapot.



## <a name="supported-operating-system-names"></a>Támogatott operációsrendszer-nevek

A CSV-fájlban megadott operációs rendszer nevének tartalmaznia kell és egyeznie kell. Ha nem, akkor nem fogja tudni értékelni őket. 

**A – H** | **I-R** | **S-T** | **U-Z**
--- | --- | --- | ---
Apple Mac OS X 10<br/>Asianux 3<br/>Asianux 4<br/>Asianux 5<br/>CentOS<br/>CentOS 4/5<br/>CoreOS Linux<br/>Debian GNU/Linux 4<br/>Debian GNU/Linux 5<br/>Debian GNU/Linux 6<br/>Debian GNU/Linux 7<br/>Debian GNU/Linux 8<br/>FreeBSD | IBM OS/2<br/>MS-DOS<br/>Novell NetWare 5<br/>Novell NetWare 6<br/>Oracle Linux<br/>Oracle Linux 4/5<br/>Oracle Solaris 10<br/>Oracle Solaris 11<br/>Red Hat Enterprise Linux 2<br/>Red Hat Enterprise Linux 3<br/>Red Hat Enterprise Linux 4<br/>Red Hat Enterprise Linux 5<br/>Red Hat Enterprise Linux 6<br/>Red Hat Enterprise Linux 7<br/>Red Hat Fedora | SCO OpenServer 5<br/>SCO OpenServer 6<br/>SCO UnixWare 7<br/> Serenity Systems eComStation 1<br/>Serenity Systems eComStation <br/>Sun Microsystems Solaris 8<br/>Sun Microsystems Solaris 9<br/><br/>SUSE Linux Enterprise 10<br/>SUSE Linux Enterprise 11<br/>SUSE Linux Enterprise 12<br/>SUSE Linux Enterprise 8/9<br/>SUSE Linux Enterprise 11<br/>SUSE openSUSE | Ubuntu Linux<br/>VMware ESXi 4<br/>VMware ESXi 5<br/>VMware ESXi 6<br/>Windows 10<br/>Windows 2000<br/>Windows 3<br/>Windows 7<br/>Windows 8<br/>Windows 95<br/>Windows 98<br/>Windows NT<br/>Windows Server (R) 2008<br/>Windows Server 2003<br/>Windows Server 2008<br/>Windows Server 2008 R2<br/>Windows Server 2012<br/>Windows Server 2012 R2<br/>Windows Server 2016<br/>Windows Server 2019<br/>Windows Server Threshold<br/>Windows Vista<br/>Windows Web Server 2008 R2<br/>Windows XP Professional

## <a name="next-steps"></a>További lépések

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Létrehozott egy Azure Migrate projektet 
> * Egy importált CSV-fájlt használó felderített kiszolgálók. Most futtasson egy értékelést a [VMWare virtuális gép Azure-beli virtuális gépekre való áttelepítéséről](./tutorial-assess-vmware-azure-vm.md).