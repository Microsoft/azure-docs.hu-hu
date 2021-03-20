---
title: VMware virtuális gépek migrálása ügynökön alapuló Azure Migrate kiszolgáló áttelepítése
description: Ismerje meg, hogyan futtathatja a VMware virtuális gépek ügynök-alapú áttelepítését Azure Migrate használatával.
author: rahulg1190
ms.author: rahugup
ms.manager: bsiva
ms.topic: tutorial
ms.date: 06/09/2020
ms.custom: MVC
ms.openlocfilehash: 15bf8f4fde2128181664fa7b94f2479bac7ad5b9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98881517"
---
# <a name="migrate-vmware-vms-to-azure-agent-based"></a>VMware virtuális gépek migrálása az Azure-ba (ügynök-alapú)

Ebből a cikkből megtudhatja, hogyan telepítheti át a helyszíni VMware virtuális gépeket az Azure-ba a [Azure Migrate: Server áttelepítési](migrate-services-overview.md#azure-migrate-server-migration-tool) eszközzel, az ügynök-alapú áttelepítés segítségével.  A VMware virtuális gépeket ügynök nélküli áttelepítéssel is áttelepítheti. [Hasonlítsa össze](server-migrate-overview.md#compare-migration-methods) a metódusokat.


 Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Készítse elő az Azure-t a Azure Migrate való együttműködéshez.
> * Felkészülés az ügynök alapú áttelepítésre. Hozzon létre egy VMware-fiókot, hogy Azure Migrate képes legyen felderíteni a gépeket az áttelepítéshez. Állítson be egy fiókot, hogy a mobilitási szolgáltatás ügynöke telepíthető legyen az áttelepíteni kívánt gépekre, és készítse elő a gépet, hogy a replikációs berendezésként működjön.
> * Adja hozzá a Azure Migrate: Server áttelepítési eszközt
> * Állítsa be a replikációs készüléket.
> * Virtuális gépek replikálása.
> * Futtasson egy teszt-áttelepítést, és győződjön meg róla, hogy minden a várt módon működik-e.
> * Futtasson teljes áttelepítést az Azure-ba.

> [!NOTE]
> Az oktatóanyagok bemutatják a forgatókönyvek legegyszerűbb telepítési útvonalát, így gyorsan beállíthatja a rendszer megvalósíthatóságát. Ahol lehet, az oktatóanyagok az alapértelmezett beállításokat használják, és nem mutatják be az összes lehetséges beállítást és módot. 

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).


## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag megkezdése előtt [tekintse át](./agent-based-migration-architecture.md) a VMware-ügynökön alapuló áttelepítési architektúrát.

## <a name="prepare-azure"></a>Az Azure előkészítése

A táblázatban szereplő feladatok végrehajtásával készítse elő az Azure-t az ügynökön alapuló áttelepítéshez.

**Feladat** | **Részletek**
--- | ---
**Azure Migrate-projekt létrehozása** | Az Azure-fióknak közreműködői vagy tulajdonosi engedélyekkel kell rendelkeznie a projekt létrehozásához.
**Azure-fiók engedélyeinek ellenőrzése** | Az Azure-fióknak rendelkeznie kell a virtuális gép létrehozásához szükséges engedélyekkel, és írnia kell egy Azure-beli felügyelt lemezre.
**Azure-hálózat beállítása** | Hozzon létre egy hálózatot, amelyet az Azure-beli virtuális gépek a Migrálás után csatlakozni fognak.

### <a name="assign-permissions-to-create-project"></a>Engedélyek kiosztása projekt létrehozásához
Ha nem rendelkezik Azure Migrate-projekttel, ellenőrizze az engedélyek létrehozását.


1. Az Azure Portalon nyissa meg az előfizetést, majd válassza a **Hozzáférés-vezérlés (IAM)** lehetőséget.
2. A **hozzáférés engedélyezése** területen keresse meg a megfelelő fiókot, és kattintson rá az engedélyek megtekintéséhez.
3. Győződjön meg arról, hogy **közreműködői** vagy **tulajdonosi** engedélyekkel rendelkezik.

    - Ha most hozott létre egy ingyenes Azure-fiókot, akkor Ön az előfizetés tulajdonosa.
    - Ha nem Ön az előfizetés tulajdonosa, akkor a tulajdonossal együttműködve rendelje hozzá a szerepkört.
    
### <a name="assign-azure-account-permissions"></a>Azure-fiók engedélyeinek kiosztása

Rendelje hozzá a virtuális gépi közreműködő szerepkört a fiókhoz, hogy rendelkezzen a következő engedélyekkel:

- Virtuális gépek létrehozása a kiválasztott erőforráscsoportban.
- Virtuális gépek létrehozása a kiválasztott virtuális hálózaton.
- Írás egy Azure-beli felügyelt lemezre. 


### <a name="set-up-an-azure-network"></a>Azure-hálózat beállítása

[Hozzon létre egy Azure-hálózatot](../virtual-network/manage-virtual-network.md#create-a-virtual-network). A helyszíni gépek replikálódnak az Azure Managed Disks szolgáltatásba. Az Azure-ba történő áttelepítéskor az Azure-beli virtuális gépek ezekből a felügyelt lemezekről jönnek létre, és a beállított Azure-hálózathoz csatlakoznak.

## <a name="prepare-for-migration"></a>Előkészületek a migráláshoz

Ellenőrizze a támogatási követelményeket és az engedélyeket, és készítse elő a replikációs berendezés üzembe helyezését. 

### <a name="prepare-an-account-to-discover-vms"></a>Fiók előkészítése a virtuális gépek felderítéséhez

Azure Migrate kiszolgáló áttelepítéséhez hozzáféréssel kell rendelkeznie a VMware-kiszolgálókhoz az áttelepíteni kívánt virtuális gépek felderítéséhez. Hozza létre a fiókot az alábbiak szerint:

1. Dedikált fiók használatához hozzon létre egy szerepkört a vCenter-szinten. Adja meg a szerepkör nevét, például **Azure_Migrate**.
2. Rendelje hozzá a szerepkörhöz az engedélyeket az alábbi táblázatban összefoglaltaknak megfelelően.
3. Hozzon létre egy felhasználót a vCenter-kiszolgálón vagy vSphere-gazdagépen. Rendelje hozzá a szerepkört a felhasználóhoz.

#### <a name="vmware-account-permissions"></a>A VMware-fiók engedélyei

**Feladat** | **Szerepkör/Engedélyek** | **Részletek**
--- | --- | ---
**Virtuális gépek felderítése** | Legalább egy csak olvasási jogosultsággal rendelkező felhasználó<br/><br/> Adatközpont-objektum –> Gyermekobjektumba propagálás, szerepkör = csak olvasható | A felhasználó az adatközpontszinten hozzárendelve, és hozzáféréssel rendelkezik az adatközpontban lévő összes objektumhoz.<br/><br/> A hozzáférés korlátozásához rendelje hozzá a **nincs hozzáférési** szerepkört a **propagálás a gyermek** objektumhoz (vSphere gazdagépek, adattárolók, virtuális gépek és hálózatok).
**Replikáció** |  Hozzon létre egy szerepkört (Azure_Site_Recovery) a szükséges engedélyekkel, majd rendelje hozzá a szerepkört egy VMware-felhasználóhoz vagy -csoporthoz<br/><br/> Adatközpont-objektum –> Gyermekobjektumba propagálás, szerepkör = Azure_Site_Recovery<br/><br/> Adattároló -> Terület lefoglalása, adattároló böngészése, alacsony szintű fájlműveletek, fájl eltávolítása, virtuális gépek fájljainak frissítése<br/><br/> Hálózat -> Hálózat hozzárendelése<br/><br/> Erőforrás -> Virtuális gép hozzárendelése az erőforráskészlethez, kikapcsolt virtuális gép migrálása, bekapcsolt virtuális gép migrálása<br/><br/> Feladatok -> Feladat létrehozása, feladat frissítése<br/><br/> Virtuális gép -> Konfiguráció<br/><br/> Virtuális gép -> Használat -> Kérdés megválaszolása, eszközkapcsolat, CD-adathordozó konfigurálása, hajlékonylemezes adathordozó, kikapcsolás, bekapcsolás, VMware-eszközök telepítése<br/><br/> Virtuális gép -> Leltár -> Létrehozás, regisztrálás, regisztráció törlése<br/><br/> Virtuális gép -> Üzembe helyezés -> Virtuális gép letöltésének engedélyezése, virtuálisgépfájlok feltöltésének engedélyezése<br/><br/> Virtuális gép -> Pillanatképek -> Pillanatképek eltávolítása | A felhasználó az adatközpontszinten hozzárendelve, és hozzáféréssel rendelkezik az adatközpontban lévő összes objektumhoz.<br/><br/> A hozzáférés korlátozásához rendelje hozzá a **nincs hozzáférési** szerepkört a **propagálás a gyermek** objektumhoz (vSphere gazdagépek, adattárolók, VMsa, ND hálózatok).

### <a name="prepare-an-account-for-mobility-service-installation"></a>Fiók előkészítése a mobilitási szolgáltatás telepítéséhez

A mobilitási szolgáltatásnak telepítve kell lennie a replikálni kívánt gépeken.

- A Azure Migrate replikációs berendezés elvégezheti a szolgáltatás leküldéses telepítését, amikor engedélyezi a replikálást egy gép számára, vagy manuálisan telepítheti, vagy a telepítési eszközöket is használhatja.
- Ebben az oktatóanyagban leküldéses telepítéssel telepítjük a mobilitási szolgáltatást.
- A leküldéses telepítéshez elő kell készítenie egy fiókot, amely Azure Migrate a kiszolgáló áttelepítése a virtuális gép elérésére használható. Ezt a fiókot csak a leküldéses telepítéshez használja a rendszer, ha nem telepíti manuálisan a mobilitási szolgáltatást.

Készítse elő a fiókot az alábbiak szerint:

1. Készítsen elő egy tartományi vagy helyi fiókot, amely rendelkezik a virtuális gépre való telepítéshez szükséges engedélyekkel.
2. Windows rendszerű virtuális gépek esetén, ha nem használ tartományi fiókot, tiltsa le a távoli felhasználói hozzáférés-vezérlést a helyi gépen a **LOCALACCOUNTTOKENFILTERPOLICY** DWORD-bejegyzés hozzáadásával, a beállításjegyzékben a **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**
3. Linux rendszerű virtuális gépek esetében készítse elő a root fiókot a forrás Linux-kiszolgálón.


### <a name="prepare-a-machine-for-the-replication-appliance"></a>Számítógép előkészítése a replikációs berendezéshez

A berendezést a gépek az Azure-ba történő replikálására használják. A berendezés egyetlen, magasan elérhető, helyszíni VMware virtuális gép, amely a következő összetevőket tárolja:

- **Konfigurációs kiszolgáló**: a konfigurációs kiszolgáló koordinálja a helyszíni és az Azure közötti kommunikációt, és felügyeli az adatreplikációt.
- **Folyamat kiszolgálója**: a Process Server replikációs átjáróként működik. Replikációs adatkérést kap; a gyorsítótárazással, tömörítéssel és titkosítással optimalizálja, és egy gyorsítótárbeli Storage-fiókba küldi az Azure-ban. A Process Server a mobilitási szolgáltatás ügynökét is telepíti a replikálni kívánt virtuális gépekre, és elvégzi a helyszíni VMware virtuális gépek automatikus felderítését.

Készítse elő a készüléket a következőképpen:

- [Tekintse át a készülékre vonatkozó követelményeket](migrate-replication-appliance.md#appliance-requirements). Általában egy letöltött PETESEJT-fájllal állíthatja be a replikációs készüléket egy VMware virtuális gépre. A sablon olyan berendezést hoz létre, amely megfelel az összes követelménynek.
- A készülékre telepíteni kell a MySQL-t. A telepítési módszerek [áttekintése](migrate-replication-appliance.md#mysql-installation) .
- Tekintse át a [nyilvános Felhőbeli URL-címeket](migrate-replication-appliance.md#url-access), és [Azure Government URL-](migrate-replication-appliance.md#azure-government-url-access) címeket, amelyekhez a készüléknek hozzá kell férnie.
- [Tekintse át azokat a portokat](migrate-replication-appliance.md#port-access) , amelyeknek a replikációs berendezésnek el kell érnie.



### <a name="check-vmware-requirements"></a>A VMware követelményeinek ellenőrzése

Győződjön meg arról, hogy a VMware-kiszolgálók és a virtuális gépek megfelelnek az Azure-ba való Migrálás követelményeinek. 

1. [Ellenőrizze](migrate-support-matrix-vmware-migration.md#vmware-requirements-agent-based) a VMware-kiszolgáló követelményeit.
2. [Ellenőrzés](migrate-support-matrix-vmware-migration.md#vm-requirements-agent-based) VIRTUÁLIS gépekre vonatkozó követelmények az áttelepítéshez.
3. Ellenőrizze az Azure beállításait. Az Azure-ba replikált helyszíni virtuális gépeknek meg kell felelniük az Azure-beli [virtuális gép követelményeinek](migrate-support-matrix-vmware-migration.md#azure-vm-requirements).
4. Az Azure-ba történő Migrálás előtt néhány módosításra van szükség a virtuális gépeken.
    - Az áttelepítés megkezdése előtt fontos, hogy elvégezze ezeket a módosításokat. Ha a módosítás előtt áttelepíti a virtuális gépet, előfordulhat, hogy a virtuális gép nem indul el az Azure-ban.
    - Tekintse át a [Windows](prepare-for-migration.md#windows-machines) -és [Linux](prepare-for-migration.md#linux-machines) -módosításokat, amelyeket el kell végeznie.

> [!NOTE]
> Az ügynök-alapú áttelepítés Azure Migrate kiszolgáló áttelepítésével a Azure Site Recovery szolgáltatás szolgáltatásain alapul. Néhány követelmény Site Recovery dokumentációra mutató hivatkozásokat is tartalmazhat.

## <a name="set-up-the-replication-appliance"></a>A replikációs berendezés beállítása

Ez az eljárás leírja, hogyan állíthatja be a készüléket egy letöltött Open Virtualization Application (PETESEJT) sablonnal. Ha nem tudja használni ezt a metódust, beállíthatja a készüléket [parancsfájl használatával](tutorial-migrate-physical-virtual-machines.md#set-up-the-replication-appliance). 

### <a name="download-the-replication-appliance-template"></a>A replikációs berendezés sablonjának letöltése

Töltse le a sablont a következőképpen:

1. A Azure Migrate projektben kattintson az **áttelepítési célok** területen található **kiszolgálók** elemre.
2. A **Azure Migrate-** Servers  >  **Azure Migrate: kiszolgáló áttelepítése** területen kattintson a **felderítés** gombra.

    ![Virtuális gépek felderítése](./media/tutorial-migrate-vmware-agent/migrate-discover.png)

3. A **felderítési gépek** a  >  **gépek virtualizáltak?**, kattintson **az igen, VMware vSphere hypervisor**.
4. A **hogyan kívánja áttelepíteni?** területen válassza az **ügynök-alapú replikáció használata** lehetőséget.
5. A **cél régióban** válassza ki azt az Azure-régiót, amelyre át szeretné telepíteni a gépeket.
6. Válassza **a megerősítés lehetőséget, hogy az áttelepítéshez a régió neve legyen**.
7. Kattintson az **erőforrások létrehozása** gombra. Ezzel létrehoz egy Azure Site Recovery tárolót a háttérben. A projekthez tartozó célként megadott régiót a gombra való kattintás után nem módosíthatja, és az összes további áttelepítést erre a régióra kell váltania.

    ![Helyreállítási tár létrehozása](./media/tutorial-migrate-vmware-agent/create-resources.png)

8. A szeretné **telepíteni az új replikációs készüléket?** területen válassza **a replikációs berendezés telepítése** lehetőséget.
9. Kattintson a **Letöltés** gombra. Ezzel letölt egy OVF-sablont.
    ![PETESEJTEK letöltése](./media/tutorial-migrate-vmware-agent/download-ova.png)
10. Jegyezze fel az erőforráscsoport és a Recovery Services tároló nevét. Ezeket a berendezések üzembe helyezése során kell megadnia.


### <a name="import-the-template-in-vmware"></a>A sablon importálása a VMware-ben

A OVF-sablon letöltése után importálja a VMware-be a replikációs alkalmazás létrehozásához egy Windows Server 2016 rendszert futtató VMware virtuális gépen.

1. Jelentkezzen be a VMware vCenter-kiszolgálóra vagy a vSphere ESXi-gazdagépre a VMware vSphere-ügyféllel.
2. A **fájl** menüben válassza a **OVF-sablon telepítése** lehetőséget a **OVF-sablon központi telepítése varázsló** elindításához. 
3. A **forrás kiválasztása** területen adja meg a letöltött OVF helyét.
4. A **részletek áttekintése** lapon válassza a **tovább** lehetőséget.
5. A **név és mappa kiválasztása** és a **konfiguráció kiválasztása lapon** fogadja el az alapértelmezett beállításokat.
6. A **tárterület kiválasztása** területen  >  **válassza a virtuális lemez formátum** lehetőséget, a legjobb teljesítményhez válassza a **vastag kiépítés lelkesen nulla értéket**.
7. A varázsló többi lapján fogadja el az alapértelmezett beállításokat.
8. A **készen áll**, ha a virtuális gépet az alapértelmezett beállításokkal szeretné beállítani, válassza a **bekapcsolás az üzembe helyezés után** lehetőséget  >  .

   > [!TIP]
   > Ha további hálózati adaptert szeretne hozzáadni, törölje **a bekapcsolást az üzembe helyezés**  >  **befejezése** után. Alapértelmezés szerint a sablon egyetlen hálózati adaptert tartalmaz. Az üzembe helyezés után további hálózati adaptereket is hozzáadhat.

### <a name="start-appliance-setup"></a>Berendezés telepítőjének indítása

1. A VMware vSphere-ügyfél konzolon kapcsolja be a virtuális gépet. A virtuális gép a bekapcsolásakor egy Windows Server 2016 telepítési folyamatot indít el.
2. Fogadja el a licencszerződést, és adjon meg egy rendszergazdai jelszót.
3. A telepítés befejezése után jelentkezzen be rendszergazdaként a virtuális gépre a rendszergazdai jelszó használatával. Amikor először jelentkezik be, a replikációs berendezés telepítési eszköze (Azure Site Recovery konfigurációs eszköz) néhány másodpercen belül elindul.
5. Adja meg a készülék kiszolgáló-áttelepítéssel való regisztrálásához használandó nevet. Ezután kattintson a **Tovább** gombra.
6. Az eszköz ellenőrzi, hogy a virtuális gép tud-e csatlakozni az Azure-hoz. A kapcsolat létrejötte után a **Sign in** (Bejelentkezés) gombra kattintva jelentkezzen be az Azure-előfizetésbe.
7. Várja meg, amíg az eszköz befejeződik egy Azure AD-alkalmazás regisztrálása a berendezés azonosításához. A készülék újraindul.
1. Jelentkezzen be újra a gépre. A konfigurációs kiszolgáló felügyeleti varázslója néhány másodpercen belül automatikusan elindul.

### <a name="register-the-replication-appliance"></a>A replikációs berendezés regisztrálása

A replikációs berendezés beállításának és regisztrálásának befejezése.

1. A készülék beállítása területen válassza a **kapcsolat beállítása** lehetőséget.
2. Válassza ki a hálózati adaptert (alapértelmezés szerint csak egy hálózati adapter van), amelyet a replikációs berendezés használ a virtuális gépek felderítéséhez, és a mobilitási szolgáltatás leküldéses telepítését a forrásszámítógépen.
3. Válassza ki azt a hálózati adaptert, amelyet a replikációs berendezés az Azure-hoz való kapcsolódáshoz használ. Kattintson a **Mentés** gombra. Ez a beállítás a konfigurálás után nem módosítható.
4. Ha a készülék proxykiszolgáló mögött található, meg kell adnia a proxybeállításokat.
    - Adja meg a proxy nevét a következőképpen: **http://ip-address** vagy **http://FQDN** . A HTTPS-proxy kiszolgálók nem támogatottak.
5. Ha a rendszer rákérdez az előfizetés, az erőforráscsoport és a tár részleteire, adja meg a berendezés sablonjának letöltésekor feljegyzett adatokat.
6. A **Független gyártótól származó szoftver telepítése** területen fogadja el a licencszerződést. Kattintson a **Letöltés és telepítés** gombra a MySQL-kiszolgáló telepítéséhez.
7. Válassza a **VMware PowerCLI telepítése** elemet. Mielőtt ezt megtenné, zárja be az összes böngészőablakot. Ezután válassza a **Folytatás** elemet.
8. A **Berendezés konfigurációjának ellenőrzése** területen a rendszer ellenőrzi az előfeltételeket, mielőtt továbblépne.
9. A **VCenter-kiszolgáló vagy vSphere ESXi-kiszolgáló hitelesítő adatainak konfigurálása** területen adja meg azon vCenter-kiszolgáló vagy VSphere-gazdagép teljes tartománynevét vagy IP-címét, ahol a replikálni kívánt virtuális gépek találhatók. Adja meg a portot, amelyen a kiszolgáló figyel. Adja meg a VMware-kiszolgáló rövid nevét, amelyet a tárolóban használni fog.
10. Adja meg a VMware discoveryhez [létrehozott](#prepare-an-account-to-discover-vms) fiók hitelesítő adatait. Válassza a **Hozzáadás**  >  **Folytatás** lehetőséget.
11. A **virtuális gép hitelesítő adatainak konfigurálása** területen adja meg a mobilitási szolgáltatás leküldéses telepítéséhez [létrehozott](#prepare-an-account-for-mobility-service-installation) hitelesítő adatokat, amikor engedélyezi a replikációt a virtuális gépek számára.  
    - A Windows-alapú gépek esetében a fióknak helyi rendszergazdai jogosultságokkal kell rendelkeznie a replikálni kívánt gépeken.
    - Linux-alapú gépeken a rendszergazdai fiók adatait adja meg.
12. Válassza a **Konfiguráció véglegesítése** elemet a regisztráció befejezéséhez.


A replikációs berendezés regisztrálása után Azure Migrate Server Assessment a megadott beállítások használatával csatlakozik a VMware-kiszolgálókhoz, és felfedi a virtuális gépeket.   >  A felderített virtuális gépeket a **többi** lapon tekintheti meg a **felderített elemek** kezelése lehetőséggel.



## <a name="replicate-vms"></a>Virtuális gépek replikálása

Válassza ki a virtuális gépeket az áttelepítéshez.

> [!NOTE]
> A portálon legfeljebb 10 gépet választhat egyszerre a replikáláshoz. Ha további replikálásra van szüksége, akkor a csoportosítsa azokat 10 kötegben.

1. A Azure Migrate projekt > **kiszolgálók**, **Azure Migrate: kiszolgáló áttelepítése** elemre, majd kattintson a **replikálás** gombra.

    ![Képernyőkép a Azure Migrate kiszolgálók képernyőjéről. A replikálás gomb a Azure Migrate: kiszolgáló áttelepítése áttelepítési eszközök területen van kiválasztva.](./media/tutorial-migrate-vmware-agent/select-replicate.png)

2. A **Replikálás**, > **Forrásbeállítások** > **Virtualizáltak a gépek?** területen válassza az **Igen, a VMware vSphere szolgáltatással** lehetőséget.
3. A helyszíni **készülék** területen válassza ki a beállított Azure Migrate berendezés nevét.
4. A **vCenter-kiszolgáló** lapon adja meg a virtuális gépeket kezelő vCenter-kiszolgáló nevét, vagy azt a vSphere-kiszolgálót, amelyen a virtuális gépek futnak.
5. A **Process Server** lapon válassza ki a replikációs berendezés nevét.
6. A **vendég hitelesítő adatai** területen határozza meg a mobilitási szolgáltatás leküldéses telepítéséhez használni kívánt virtuális gép rendszergazdai fiókját. Ezután kattintson a **Tovább gombra: Virtual Machines**.

    ![Képernyőfelvétel a replikálás képernyő forrásoldali beállítások lapján. A vendég hitelesítő adatok mező kiemelve értékre van állítva, és a beállítás a VM-admin-Account.](./media/tutorial-migrate-vmware-agent/source-settings.png)

7. **Virtual Machines** válassza ki a replikálni kívánt gépeket.

    - Ha futtatta a virtuális gépek értékelését, alkalmazhatja az eredményekben szereplő, a virtuális gépek méretezésére és a lemeztípusra (prémium/standard) vonatkozó javaslatokat. Ehhez a **Importálja a migrálási beállításokat az Azure Migrate-értékelésből?** területen válassza az **Igen** lehetőséget.
    - Ha nem futtatott értékelést, vagy nem szeretné használni az értékelés beállításait, válassza a **Nem** lehetőséget.
    - Ha az értékelés használatát választotta, válassza ki a virtuálisgép-csoportot és az értékelés nevét.
8. A **rendelkezésre állási beállítások** területen válassza a következőket:
    -  Rendelkezésre állási zóna az áttelepített gép egy adott rendelkezésre állási zónába való rögzítéséhez a régióban. Ezzel a beállítással olyan kiszolgálókat oszthat szét, amelyek több csomópontos alkalmazási szintet alkotnak Availability Zoneson belül. Ha ezt a lehetőséget választja, a számítási lapon meg kell adnia a rendelkezésre állási zónát, amelyet a kiválasztott gépekhez használni szeretne. Ez a beállítás csak akkor érhető el, ha az áttelepítéshez kiválasztott cél régió támogatja a Availability Zones
    -  Rendelkezésre állási csoport, amely az áttelepített gépet egy rendelkezésre állási csoportba helyezi. A beállítás használatához legalább egy rendelkezésre állási csoportnak kell lennie.
    - Nincs szükség infrastruktúra-redundancia beállításra, ha az áttelepített gépekhez nem szükséges a rendelkezésre állási konfigurációk egyike sem.
9. Tekintse át az áttelepíteni kívánt virtuális gépeket. Ezután kattintson a **Tovább: cél beállítások** elemre.
10. A **Célbeállítások** között válassza ki az előfizetést és a célrégiót, ahova migrálni szeretne, majd adja meg az erőforráscsoportot, amelyben az Azure-beli virtuális gépek megtalálhatóak lesznek a migrálást követően.
11. A **Virtuális hálózat** területen válassza ki az Azure-beli virtuális hálózatot/alhálózatot, amelyhez az Azure-beli virtuális gépek a migrálást követően csatlakoznak majd.
12. A **rendelkezésre állási beállítások** területen válassza a következőket:
    -  Rendelkezésre állási zóna az áttelepített gép egy adott rendelkezésre állási zónába való rögzítéséhez a régióban. Ezzel a beállítással olyan kiszolgálókat oszthat szét, amelyek több csomópontos alkalmazási szintet alkotnak Availability Zoneson belül. Ha ezt a lehetőséget választja, a számítási lapon meg kell adnia a rendelkezésre állási zónát, amelyet a kiválasztott gépekhez használni szeretne. Ez a beállítás csak akkor érhető el, ha az áttelepítéshez kiválasztott cél régió támogatja a Availability Zones
    -  Rendelkezésre állási csoport, amely az áttelepített gépet egy rendelkezésre állási csoportba helyezi. A beállítás használatához legalább egy rendelkezésre állási csoportnak kell lennie.
    - Nincs szükség infrastruktúra-redundancia beállításra, ha az áttelepített gépekhez nem szükséges a rendelkezésre állási konfigurációk egyike sem.
13. A **lemez titkosítása típusnál** válassza a következő lehetőséget:
    - Titkosítás – a platform által felügyelt kulccsal
    - Titkosítás – az ügyfél által felügyelt kulccsal
    - Dupla titkosítás a platform által felügyelt és az ügyfél által felügyelt kulcsokkal

   > [!NOTE]
   > A virtuális gépek CMK-vel való replikálásához létre kell [hoznia egy lemezes titkosítási](../virtual-machines/disks-enable-customer-managed-keys-portal.md#set-up-your-disk-encryption-set) csoportot a cél erőforráscsoport alatt. A lemezes titkosítási készlet objektum Managed Diskst képez le egy olyan Key Vaultra, amely az SSE-hoz használandó CMK tartalmazza.
  
14. Az **Azure Hybrid Benefit** területen:

    - Válassza a **Nem** lehetőséget, ha nem szeretné alkalmazni az Azure Hybrid Benefit szolgáltatást. Ezután kattintson a **Tovább** gombra.
    - Válassza az **Igen** lehetőséget, ha aktív frissítési garanciával vagy Windows Server-előfizetéssel rendelkező gépei vannak, és alkalmazni szeretné az előnyöket a migrált gépekre. Ezután kattintson a **Tovább** gombra.

    ![Cél beállításai](./media/tutorial-migrate-vmware/target-settings.png)

15. A **számításban** tekintse át a virtuális gép nevét, méretét, operációsrendszer-lemezének típusát és rendelkezésre állási konfigurációját (ha az előző lépésben van kiválasztva). A virtuális gépeknek meg kell felelniük az [Azure-követelményeknek](migrate-support-matrix-vmware-migration.md#azure-vm-requirements).

   - Virtuálisgép- **méret**: Ha értékelési javaslatokat használ, a virtuális gép mérete legördülő menüben az ajánlott méret látható. Egyéb esetben az Azure Migrate az Azure-előfizetésben található leginkább megfelelő érték alapján választja ki a méretet. Alternatív megoldásként az **Azure-beli virtuális gép mérete** területen manuálisan is kiválaszthatja a méretet. 
    - **Operációsrendszer-lemez**: a virtuális gép operációsrendszer-(rendszerindító) lemezének megadása. Az operációsrendszer-lemez az a lemez, amelyen az operációs rendszer rendszerbetöltője és telepítője található. 
    - **Rendelkezésre állási zóna**: Itt adhatja meg a használni kívánt rendelkezésre állási zónát.
    - **Rendelkezésre állási csoport**: adja meg a használni kívánt rendelkezésre állási készletet.

16. A **lemezek** területen adja meg, hogy a virtuálisgép-lemezeket replikálni kell-e az Azure-ba, majd válassza ki a lemez típusát (standard SSD/HDD vagy prémium szintű felügyelt lemez) az Azure-ban. Ezután kattintson a **Tovább** gombra.
    - Kizárhat lemezeket a replikációból.
    - Ha kizárja a lemezeket, azok nem lesznek jelen az Azure-beli virtuális gépen a migrációt követően. 

17. Az **Áttekintés és a replikáció megkezdése** területen tekintse át a beállításokat, majd kattintson a **Replikálás** gombra a kiszolgálók kezdeti replikálásának elindításához.

> [!NOTE]
> A replikálási beállításokat a replikáció elindítása előtt bármikor frissítheti, **kezelheti** a  >  **replikáló gépeket**. A replikáció kezdete után a beállítások már nem módosíthatók.


## <a name="track-and-monitor"></a>Nyomon követés és figyelés

1. A feladatok állapotának nyomon követése a portál értesítéseiben. 

    ![Feladatok nyomon követése](./media/tutorial-migrate-vmware-agent/jobs.png)
    
2. A replikáció állapotának figyeléséhez kattintson a **kiszolgálók replikálásának** **Azure Migrate: kiszolgáló áttelepítése** elemre.

    ![Replikáció monitorozása](./media/tutorial-migrate-vmware-agent/replicate-servers.png)

A replikáció a következőképpen történik:
- Ha a replikáció elindítása feladatainak végrehajtása sikeresen befejeződött, a gépek megkezdik a kezdeti replikálást az Azure-ba.
- A kezdeti replikálás befejeződése után a különbözeti replikáció megkezdődik. A helyszíni lemezek növekményes módosításait a rendszer rendszeres időközönként replikálja az Azure-beli replika-lemezekre.


## <a name="run-a-test-migration"></a>Migrálási teszt futtatása


A különbözeti replikáció megkezdése előtt futtasson egy teszt-áttelepítést a virtuális gépek számára, mielőtt az Azure-ba történő teljes áttelepítést futtatná. Azt javasoljuk, hogy az áttelepítés előtt legalább egyszer végezze el ezt az egyes gépeken.

- Az áttelepítési teszt futtatása ellenőrzi, hogy az áttelepítés a várt módon fog-e működni, anélkül, hogy ez hatással lenne a helyszíni gépekre, amelyek továbbra is működőképesek maradnak. 
- A tesztelési áttelepítés szimulálja az áttelepítést egy Azure-beli virtuális gép replikálási adataival történő létrehozásával (általában az Azure-előfizetése nem üzemi VNet való áttelepítéssel).
- A replikált Azure-beli virtuális gép használatával ellenőrizheti az áttelepítést, elvégezheti az alkalmazások tesztelését, és bármilyen problémát megtehet a teljes áttelepítés előtt.

Végezzen el egy teszt-áttelepítést a következőképpen:


1. Az **áttelepítési célok**  >  **kiszolgálói**  >  **Azure Migrate: kiszolgáló áttelepítése** területen kattintson az **áttelepített kiszolgálók tesztelése** elemre.

     ![Migrált kiszolgálók tesztelése](./media/tutorial-migrate-vmware-agent/test-migrated-servers.png)

2. Kattintson a jobb gombbal a tesztelni kívánt virtuális gépre, majd válassza a **Migrálás tesztelése** lehetőséget.

    ![Migrálási teszt](./media/tutorial-migrate-vmware-agent/test-migrate.png)

3. A **Migrálási teszt** területen válassza ki az Azure-beli virtuális hálózatot, amelyen az Azure-beli virtuális gép a migrálást követően megtalálható lesz. Javasoljuk, hogy ne éles virtuális hálózatot használjon.
4. A **Migrálási teszt** feladat elindul. A feladatot a portál értesítései között követheti nyomon.
5. A migrálás befejeztével az Azure Portal **Virtuális gépek** területén tekintheti meg a migrált Azure-beli virtuális gépet. A gép nevében a **-Test** utótag szerepel.
6. A teszt elvégzése után a **Gépek replikálása** területen kattintson a jobb gombbal az Azure-beli virtuális gépre, majd kattintson a **Migrálási teszt törlése** gombra.

    ![Migrálás törlése](./media/tutorial-migrate-vmware-agent/clean-up.png)


## <a name="migrate-vms"></a>Virtuális gép migrálása

Miután ellenőrizte, hogy a teszt áttelepítése a várt módon működik-e, áttelepítheti a helyszíni gépeket.

1. A Azure Migrate projekt > **kiszolgálók**  >  **Azure Migrate: kiszolgáló áttelepítése** területen kattintson a **kiszolgálók replikálása** elemre.

    ![Kiszolgálók replikálása](./media/tutorial-migrate-vmware-agent/replicate-servers.png)

2. A **Gépek replikálása** területen kattintson jobb gombbal a virtuális gépre, majd kattintson a **Migrálás** elemre.
3. Az **áttelepítés** során  >  **állítsa le a virtuális gépeket, és végezze el az adatvesztés nélküli tervezett áttelepítést**, válassza az **Igen**  >  **OK** lehetőséget.
    - Alapértelmezés szerint Azure Migrate leállítja a helyszíni virtuális gépet a minimális adatvesztés biztosításához. 
    - Ha nem szeretné leállítani a virtuális gépet, válassza a **Nem** lehetőséget.
4. A virtuálisgép-migrálási feladat elindul. A feladatot az Azure-értesítések között követheti nyomon.
5. A feladat befejeztével a virtuális gépet a **Virtuális gépek** oldalon tekintheti meg és kezelheti.

## <a name="complete-the-migration"></a>Az áttelepítés befejezése

1. Az áttelepítés befejezése után kattintson a jobb gombbal a virtuális gépre > az **áttelepítés leállítása** elemre. Ez a következő műveleteket végzi el:
    - Leállítja a helyszíni gép replikálását.
    - Eltávolítja a gépet a **replikálási kiszolgálók** száma Azure Migrate: kiszolgáló áttelepítése.
    - A virtuális gép replikációs állapotára vonatkozó információk tisztítása.
2. Telepítse az Azure-beli VM [Windows](../virtual-machines/extensions/agent-windows.md) -vagy [Linux](../virtual-machines/extensions/agent-linux.md) -ügynököt az áttelepített gépekre.
3. Hajtson végre minden áttelepítés utáni módosítást az alkalmazáson (például adatbázis-kapcsolati sztringek frissítése és webes kiszolgálók konfigurálása).
4. Végezze el a végső alkalmazás- és áttelepítés-elfogadás teszteket az Azure-on jelenleg futó alkalmazásoknál.
5. Az áttelepített Azure virtuálisgép-példány felé irányuló forgalom kivágása.
6. Távolítsa el a helyszíni virtuális gépeket a helyi virtuálisgép-készletéből.
7. Távolítsa el a helyszíni virtuális gépeket helyi biztonsági mentésekből.
8. Frissítse minden belső dokumentációját az Azure virtuális gépek új helyével és IP-címével. 

## <a name="post-migration-best-practices"></a>Az áttelepítés utáni ajánlott eljárások

- Helyszíni
    - Helyezze át az alkalmazás-forgalmat az áttelepített Azure virtuálisgép-példányon futó alkalmazásra.
    - Távolítsa el a helyszíni virtuális gépeket a helyi virtuálisgép-készletéből.
    - Távolítsa el a helyszíni virtuális gépeket helyi biztonsági mentésekből.
    - Frissítse minden belső dokumentációját az Azure virtuális gépek új helyével és IP-címével.
- Azure-beli virtuális gép beállításainak csípése az áttelepítés után:
    - Az [Azure virtuálisgép-ügynök](../virtual-machines/extensions/agent-windows.md) kezeli a virtuális gépek kommunikációját az Azure-hálóvezérlővel. Erre egyes Azure-szolgáltatások, például az Azure Backup, a Site Recovery és az Azure Security esetében van szükség. VMare-alapú virtuális gépek ügynök szerinti áttelepítéssel történő áttelepítésekor a mobilitási szolgáltatás telepítője telepíti az Azure VM Agent ügynököt a Windows rendszerű gépekre. Linux rendszerű virtuális gépeken azt javasoljuk, hogy telepítse az ügynököt az áttelepítés után.
    - A Migrálás után manuálisan távolítsa el a mobilitási szolgáltatást az Azure virtuális gépről.
    - Az áttelepítés után manuálisan távolítsa el a VMware-eszközöket.
- Az Azure-ban:
    - Hajtson végre minden áttelepítés utáni módosítást az alkalmazáson (például adatbázis-kapcsolati sztringek frissítése és webes kiszolgálók konfigurálása).
    - Végezze el a végső alkalmazás- és áttelepítés-elfogadás teszteket az Azure-on jelenleg futó alkalmazásoknál.
- Üzletmenet-folytonosság/vész-helyreállítás
    - Biztonságba helyezheti az adatokat, ha biztonsági másolatot készít az Azure virtuális gépekről az Azure Backup szolgáltatással. [További információ](../backup/quick-backup-vm-portal.md).
    - Biztosíthatja a számítási feladatok folyamatos futtatását és rendelkezésre állását, ha az Azure virtuális gépeket egy másodlagos régióba replikálja a Site Recovery használatával. [További információ](../site-recovery/azure-to-azure-tutorial-enable-replication.md).
- A biztonság fokozása érdekében:
    - Zárolja és korlátozza a bejövő adatforgalom elérését [Azure Security Center – igény szerinti felügyelettel](../security-center/security-center-just-in-time.md).
    - Korlátozza a forgalmat felügyeleti végpontokra [hálózati biztonsági csoportok](../virtual-network/network-security-groups-overview.md) használatával.
    - Az [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md) üzembe helyezésével biztonságba helyezheti a lemezeket, és megóvhatja az adatokat a lopási kísérletektől és a jogosulatlan hozzáféréstől.
    - Látogasson el a [az Azure Security Center](https://azure.microsoft.com/services/security-center/) webhelyére, és tudjon meg többet az [IaaS-erőforrások biztosításáról](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/).
- Figyelési és felügyeleti eszközök:
    - Fontolja meg az [Azure Cost Management](../cost-management-billing/cloudyn/overview.md) üzembe helyezését az erőforrás-használat és a költségek figyeléséhez.




 ## <a name="next-steps"></a>Következő lépések

Vizsgálja meg a [felhőalapú migrációs utat](/azure/architecture/cloud-adoption/getting-started/migrate) az Azure Cloud bevezetési keretrendszerében.