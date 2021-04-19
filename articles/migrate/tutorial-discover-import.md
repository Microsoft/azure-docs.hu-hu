---
title: Helyszíni kiszolgálók értékelése importált CSV-fájllal az Azure Migrate Server Assessment használatával
description: Ismerteti, hogyan derítheti fel a helyszíni kiszolgálókat az Azure-ba való migráláshoz egy importált CSV-fájl használatával a Azure Migrate Server Assessmentben
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: tutorial
ms.date: 09/14/2020
ms.openlocfilehash: c142cae3e96d800488b67da613181d1a91ba5b5b
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107713317"
---
# <a name="tutorial-assess-servers-using-an-imported-csv-file"></a>Oktatóanyag: Kiszolgálók értékelése importált CSV-fájl használatával

Az Azure-ba való migrálás részeként felderítheti a helyszíni leltárt és a számítási feladatokat. 

Ez az oktatóanyag bemutatja, hogyan értékelheti ki a helyszíni gépeket a Azure Migrate: Server Assessment eszközzel egy importált, vesszővel elválasztott értékeket (CSV) használó fájl használatával. 

Ha CSV-fájlt használ, nem kell beállítania a Azure Migrate kiszolgálók felderítéséhez és értékeléséhez. Szabályozhatja a fájlban megosztani kívánt adatokat, és az adatok nagy része opcionális. Ez a módszer a következő esetben hasznos:

- Gyors, kezdeti felmérést szeretne végezni a berendezés üzembe helyezése előtt.
- Nem tudja a szervezetében üzembe helyezni az Azure Migrate-berendezést.
- Nem tudja megosztani a helyszíni kiszolgálókhoz való hozzáférés engedélyezéséhez szükséges hitelesítő adatokat.
- A biztonsági korlátozások megakadályozzák, hogy a berendezés adatokat gyűjtsön, és hogy ezeket az adatokat elküldje az Azure-ba.

> [!NOTE]
> CSV-fájllal importált kiszolgálók nem miigálhatók.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Azure-fiók beállítása
> * Azure Migrate-projekt beállítása
> * CSV-fájl előkészítése
> * A fájl importálása
> * Kiszolgálók értékelése

> [!NOTE]
> Az oktatóanyagok a forgatókönyv kipróbálásának leggyorsabb útvonalát mutatják be, és ahol lehetséges, az alapértelmezett beállításokat használják. 

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).

## <a name="prerequisites"></a>Előfeltételek

- Akár 20 000 kiszolgálót is hozzáadhat egyetlen CSV-fájlban és egy Azure Migrate projektben. 
- A CSV-fájlban megadott operációsrendszer-neveknek tartalmazni kell a támogatott neveket, és meg [kell egyezniük a következőkkel:](#supported-operating-system-names).


## <a name="prepare-an-azure-user-account"></a>Azure-beli felhasználói fiók előkészítése

Egy új Azure Migrate létrehozásához a következővel kell fiókot létrehoznia:
- Közreműködői vagy tulajdonosi engedélyek egy Azure-előfizetésben.
- Engedélyek a Azure Active Directory regisztráláshoz.

Ha most hozott létre egy ingyenes Azure-fiókot, akkor Ön az előfizetés tulajdonosa. Ha nem Ön az előfizetés tulajdonosa, a tulajdonossal együtt rendelje hozzá az engedélyeket az alábbiak szerint:

1. A Azure Portal keressen rá az "előfizetések" kifejezésre, majd a **Szolgáltatások alatt** válassza az **Előfizetések lehetőséget.**

    ![Keresőmező az Azure-előfizetés kereséséhez](./media/tutorial-discover-import/search-subscription.png)

2. Az **Előfizetések lapon** válassza ki azt az előfizetést, amelyben létre szeretne hozni egy Azure Migrate projektet. 
3. Az előfizetésben válassza a **Hozzáférés-vezérlés (IAM)**  >  **Hozzáférés ellenőrzése lehetőséget.**
4. A **Hozzáférés ellenőrzése között** keresse meg a megfelelő felhasználói fiókot.
5. A **Szerepkör-hozzárendelés hozzáadása mezőben válassza a** Hozzáadás **lehetőséget.**

    ![Felhasználói fiók keresése a hozzáférés ellenőrzéshez és szerepkör hozzárendeléshez](./media/tutorial-discover-import/azure-account-access.png)

6. A **Szerepkör-hozzárendelés hozzáadása mezőben** válassza a Közreműködő vagy a Tulajdonos szerepkört, majd a fiókot (a példánkban az azmigrateuser). Kattintson a **Mentés** gombra.

    ![Megnyitja a Szerepkör-hozzárendelés hozzáadása oldalt, hogy hozzárendelje a szerepkört a fiókhoz](./media/tutorial-discover-import/assign-role.png)

7. A portálon keressen rá a felhasználókra, és a **Szolgáltatások alatt válassza** a Felhasználók **lehetőséget.**
8. A **Felhasználói beállítások lapon** ellenőrizze, hogy az Azure AD-felhasználók regisztrálnak-e alkalmazásokat (alapértelmezés szerint **igen).**

    ![Ellenőrizze a Felhasználói beállításokban, hogy a felhasználók regisztrálnak-e Active Directory alkalmazásokat](./media/tutorial-discover-import/register-apps.png)



## <a name="set-up-a-project"></a>Projekt beállítása

Ha még nem Azure Migrate, állítson be egy új projektprojektet.

1. Az Azure Portal > **Minden szolgáltatás** területén keressen az **Azure Migrate** szolgáltatásra.
2. A **Szolgáltatások** területen válassza az **Azure Migrate** lehetőséget.
3. Az **Áttekintés oldalon** válassza a Projekt létrehozása **lehetőséget.**
5. A **Projekt létrehozása mezőben** válassza ki az Azure-előfizetését és -erőforráscsoportját. Ha még nincs erőforráscsoportja, hozzon létre egyet.
6. A **Project Details (Projekt részletei)** alatt adja meg a projekt nevét és a földrajzi helyeket, ahol létre szeretné hozni a projektet. Tekintse át a nyilvános és kormányzati [felhők](migrate-support-matrix.md#supported-geographies-public-cloud) [támogatott földrajzi helyeket.](migrate-support-matrix.md#supported-geographies-azure-government)

   ![A projekt nevének és régiójának mezői](./media/tutorial-discover-import/new-project.png)  
    > [!Note]
    > A Speciális **konfiguráció** szakaszban létrehozhat egy privát végpontkapcsolatú Azure Migrate-projektet. [További információ](how-to-use-azure-migrate-with-private-endpoints.md#create-a-project-with-private-endpoint-connectivity)

7. Válassza a **Létrehozás** lehetőséget.
8. Várjon néhány percet, amíg az Azure Migrate-projekt telepítése megtörténik.

A **Azure Migrate: Server Assessment** eszköz alapértelmezés szerint hozzá van adva az új projekthez.

![Az alapértelmezetten hozzáadott Server Assessment eszközt megjelenítő oldal](./media/tutorial-discover-import/added-tool.png)

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
**Minden lemez olvasási műveletei** | No | Lemez olvasási műveleteinek másodpercenkénti száma.
**Minden lemez írási műveletei** | No | Lemezírási műveletek másodpercenként.
**Minden lemez olvasási teljesítménye** | No | A lemezről beolvasott adatok, MB/s-ban.
**Minden lemez írási teljesítménye** | No | Lemezre írt adatok, MB/s-ban.
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

Miután adatokat adott a CSV-sablonhoz, importálja a CSV-fájlt a Server Assessmentbe.

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
4. Ha az importálás állapota **Befejezve**, akkor a kiszolgálói adatok importálva lettek. Frissítsen, ha úgy tűnik, hogy az importálási folyamat nem fejeződött be.

## <a name="update-server-information"></a>Kiszolgálói adatok frissítése

A kiszolgáló adatait úgy frissítheti, ha ugyanazzal a **kiszolgálónévvel** újból importálja a kiszolgálói adatokat. A **Kiszolgáló neve** mező nem módosítható. A kiszolgálók törlése jelenleg nem támogatott.

## <a name="verify-servers-in-the-portal"></a>Kiszolgálók ellenőrzése a portálon

Annak ellenőrzése, hogy a kiszolgálók megjelennek-e az Azure Portalon a felderítés után:

1. Nyissa meg az Azure Migrate irányítópultját.
2. Az **Azure Migrate – Kiszolgálók** > **Azure Migrate: Kiszolgáló értékelése** oldalon kattintson a **felderített kiszolgálók** számát megjelenítő ikonra.
3. Válassza az **Importálásalapú** lapot.



## <a name="supported-operating-system-names"></a>Támogatott operációsrendszer-nevek

A CSV-fájlban megadott operációsrendszer-neveknek tartalmazni kell a és a egyezniük kell. Ha nem, akkor nem fogja tudni felmérni őket. 

**A-H** | **I-R** | **S-T** | **U-Z**
--- | --- | --- | ---
Asianux 3<br/>Asianux 4<br/>Asianux 5<br/>CentOS<br/>CentOS 4/5<br/>CoreOS Linux<br/>Debian GNU/Linux 4<br/>Debian GNU/Linux 5<br/>Debian GNU/Linux 6<br/>Debian GNU/Linux 7<br/>Debian GNU/Linux 8<br/>FreeBSD | IBM OS/2<br/>macOS X 10<br/>MS-DOS<br/>Novell NetWare 5<br/>Novell NetWare 6<br/>Oracle Linux<br/>Oracle Linux 4/5<br/>Oracle Solaris 10<br/>Oracle Solaris 11<br/>Red Hat Enterprise Linux 2<br/>Red Hat Enterprise Linux 3<br/>Red Hat Enterprise Linux 4<br/>Red Hat Enterprise Linux 5<br/>Red Hat Enterprise Linux 6<br/>Red Hat Enterprise Linux 7<br/>Red Hat Fedora | SCO OpenServer 5<br/>SCO OpenServer 6<br/>SCO UnixWare 7<br/> Serenity Systems eComStation 1<br/>Serenity Systems eComStation <br/>Sun Microsystems Solaris 8<br/>Sun Microsystems Solaris 9<br/><br/>SUSE Linux Enterprise 10<br/>SUSE Linux Enterprise 11<br/>SUSE Linux Enterprise 12<br/>SUSE Linux Enterprise 8/9<br/>SUSE Linux Enterprise 11<br/>SUSE openSUSE | Ubuntu Linux<br/>VMware ESXi 4<br/>VMware ESXi 5<br/>VMware ESXi 6<br/>Windows 10<br/>Windows 2000<br/>Windows 3<br/>Windows 7<br/>Windows 8<br/>Windows 95<br/>Windows 98<br/>Windows NT<br/>Windows Server (R) 2008<br/>Windows Server 2003<br/>Windows Server 2008<br/>Windows Server 2008 R2<br/>Windows Server 2012<br/>Windows Server 2012 R2<br/>Windows Server 2016<br/>Windows Server 2019<br/>Windows Server Threshold<br/>Windows Vista<br/>Windows Web Server 2008 R2<br/>Windows XP Professional

## <a name="next-steps"></a>További lépések

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

> [!div class="checklist"]
> * Létrehozott egy Azure Migrate projektet 
> * Importált CSV-fájllal felderített kiszolgálók. Most futtatassa a VMware virtuális gépek Azure-beli virtuális gépekre [való migrálásának értékelését.](./tutorial-assess-vmware-azure-vm.md)