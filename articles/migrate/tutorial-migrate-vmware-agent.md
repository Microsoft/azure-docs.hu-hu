---
title: VMware virtuális gépek áttelepítése ügynökalapú kiszolgálómigrálással Azure Migrate áttelepítése
description: Megtudhatja, hogyan futtathat ügynökalapú migrálást VMware virtuális gépekre a Azure Migrate.
author: rahulg1190
ms.author: rahugup
ms.manager: bsiva
ms.topic: tutorial
ms.date: 06/09/2020
ms.custom: MVC
ms.openlocfilehash: 60b58f7cf67a22e019ff186e4e1811ff5b001d84
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107714451"
---
# <a name="migrate-vmware-vms-to-azure-agent-based"></a>VMware virtuális gépek áttelepítése az Azure-ba (ügynökalapú)

Ez a cikk bemutatja, hogyan migrálhatóak a helyszíni VMware virtuális gépek az Azure-ba a [Azure Migrate:Server Migration](migrate-services-overview.md#azure-migrate-server-migration-tool) eszköz és az ügynökalapú migrálás használatával.  VMware virtuális gépeket ügynök nélküli migrálás használatával is migrálhat. [Hasonlítsa](server-migrate-overview.md#compare-migration-methods) össze a metódusokat.


 Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Készítse elő az Azure-t a Azure Migrate.
> * Felkészülés az ügynökalapú migrálásra. Állítson be egy VMware-fiókot, Azure Migrate felderítheti a migráláshoz elérhető gépeket. Állítson be egy fiókot, hogy a Mobility szolgáltatás-ügynök telepítheti az át telepíteni kívánt gépekre, és előkészítse a gépet a replikációs berendezésként való használatra.
> * A Azure Migrate:Server Migration eszköz hozzáadása
> * A replikációs berendezés beállítása.
> * Virtuális gépek replikálása.
> * Egy migrálási teszt futtatásával ellenőrizze, hogy minden a várt módon működik-e.
> * Teljes migrálás futtatása az Azure-ba.

> [!NOTE]
> Az oktatóanyagok egy forgatókönyv legegyszerűbb üzembe helyezési útvonalát mutatják be, hogy gyorsan be tudja állítani a koncepció igazolását. Ahol lehet, az oktatóanyagok az alapértelmezett beállításokat használják, és nem mutatják be az összes lehetséges beállítást és módot. 

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).


## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag megkezdése előtt tekintse át [a](./agent-based-migration-architecture.md) VMware-ügynökalapú migrálási architektúrát.

## <a name="prepare-azure"></a>Az Azure előkészítése

A táblázatban található feladatok elvégzésével előkészítheti az Azure-t az ügynökalapú migrálásra.

**Feladat** | **Részletek**
--- | ---
**Azure Migrate-projekt létrehozása** | Az Azure-fióknak Közreműködői vagy Tulajdonosi engedélyekkel kell rendelkeznie a projekt létrehozásához.
**Azure-fiók engedélyeinek ellenőrzése** | Az Azure-fióknak engedélyekkel kell rendelkeznie egy virtuális gép létrehozásához és egy Azure-beli felügyelt lemezre való íráshoz.
**Azure-hálózat beállítása** | Állítson be egy hálózatot, amelybe az Azure-beli virtuális gépek csatlakozni fognak a migrálás után.

### <a name="assign-permissions-to-create-project"></a>Engedélyek hozzárendelése projekt létrehozásához
Ha még nem rendelkezik saját Azure Migrate, ellenőrizze a létrehozási engedélyeket.


1. Az Azure Portalon nyissa meg az előfizetést, majd válassza a **Hozzáférés-vezérlés (IAM)** lehetőséget.
2. A **Hozzáférés ellenőrzése menüben** keresse meg a megfelelő fiókot, és kattintson rá az engedélyek megtekintéséhez.
3. Ellenőrizze, hogy **közreműködői vagy** **tulajdonosi engedélyekkel** rendelkezik-e.

    - Ha most hozott létre egy ingyenes Azure-fiókot, akkor Ön az előfizetés tulajdonosa.
    - Ha nem Ön az előfizetés tulajdonosa, akkor a tulajdonossal együttműködve rendelje hozzá a szerepkört.
    
### <a name="assign-azure-account-permissions"></a>Azure-fiókengedélyek hozzárendelése

Rendelje hozzá a Virtuális gépek közreműködője szerepkört a fiókhoz, hogy rendelkezik a következő engedélyekkel:

- Virtuális gépek létrehozása a kiválasztott erőforráscsoportban.
- Virtuális gépek létrehozása a kiválasztott virtuális hálózaton.
- Írás azure-beli felügyelt lemezre. 


### <a name="set-up-an-azure-network"></a>Azure-hálózat beállítása

[Állítson be egy Azure-hálózatot.](../virtual-network/manage-virtual-network.md#create-a-virtual-network) A helyszíni gépek azure-beli felügyelt lemezekre vannak replikálva. Amikor feladatátvételt ad át az Azure-ba a migráláshoz, a rendszer Azure-beli virtuális gépeket hoz létre ezekről a felügyelt lemezekről, és csatlakozik a beállított Azure-hálózathoz.

## <a name="prepare-for-migration"></a>Előkészületek a migráláshoz

Ellenőrizze a támogatási követelményeket és az engedélyeket, és készítse elő a replikációs berendezés üzembe helyezését. 

### <a name="prepare-an-account-to-discover-vms"></a>Fiók előkészítése virtuális gépek felderítése számára

Azure Migrate Server Migrationnek hozzáférésre van szüksége a VMware-kiszolgálókhoz a migrálni kívánt virtuális gépek felderítéséhez. Hozza létre a fiókot az alábbiak szerint:

1. Dedikált fiók használatához hozzon létre egy szerepkört a vCenter-szinten. Adjon nevet a szerepkörnek, például: **Azure_Migrate.**
2. Rendelje hozzá a szerepkörhöz az engedélyeket az alábbi táblázatban összefoglaltaknak megfelelően.
3. Hozzon létre egy felhasználót a vCenter-kiszolgálón vagy vSphere-gazdagépen. Rendelje hozzá a szerepkört a felhasználóhoz.

#### <a name="vmware-account-permissions"></a>A VMware-fiók engedélyei

**Feladat** | **Szerepkör/Engedélyek** | **Részletek**
--- | --- | ---
**Virtuális gépek felderítése** | Legalább egy csak olvasási jogosultsággal rendelkező felhasználó<br/><br/> Adatközpont-objektum –> Gyermekobjektumba propagálás, szerepkör = csak olvasható | A felhasználó az adatközpontszinten hozzárendelve, és hozzáféréssel rendelkezik az adatközpontban lévő összes objektumhoz.<br/><br/> A hozzáférés korlátozásához  rendelje hozzá  a Gyermekobjektumba propagálás objektummal a Nincs hozzáférés szerepkört a gyermekobjektumok (vSphere-gazdagépek, adattárolók, virtuális gépek és hálózatok) számára.
**Replikáció** |  Hozzon létre egy szerepkört (Azure_Site_Recovery) a szükséges engedélyekkel, majd rendelje hozzá a szerepkört egy VMware-felhasználóhoz vagy -csoporthoz<br/><br/> Adatközpont-objektum –> Gyermekobjektumba propagálás, szerepkör = Azure_Site_Recovery<br/><br/> Adattároló -> Terület lefoglalása, adattároló böngészése, alacsony szintű fájlműveletek, fájl eltávolítása, virtuális gépek fájljainak frissítése<br/><br/> Hálózat -> Hálózat hozzárendelése<br/><br/> Erőforrás -> Virtuális gép hozzárendelése az erőforráskészlethez, kikapcsolt virtuális gép migrálása, bekapcsolt virtuális gép migrálása<br/><br/> Feladatok -> Feladat létrehozása, feladat frissítése<br/><br/> Virtuális gép -> Konfiguráció<br/><br/> Virtuális gép -> Használat -> Kérdés megválaszolása, eszközkapcsolat, CD-adathordozó konfigurálása, hajlékonylemezes adathordozó, kikapcsolás, bekapcsolás, VMware-eszközök telepítése<br/><br/> Virtuális gép -> Leltár -> Létrehozás, regisztrálás, regisztráció törlése<br/><br/> Virtuális gép -> Üzembe helyezés -> Virtuális gép letöltésének engedélyezése, virtuálisgépfájlok feltöltésének engedélyezése<br/><br/> Virtuális gép -> Pillanatképek -> Pillanatképek eltávolítása | A felhasználó az adatközpontszinten hozzárendelve, és hozzáféréssel rendelkezik az adatközpontban lévő összes objektumhoz.<br/><br/> A hozzáférés korlátozásához  rendelje hozzá  a Gyermekobjektumba propagálás objektummal a Nincs hozzáférés szerepkört a gyermekobjektumok (vSphere-gazdagépek, adattárolók, virtuális gépek és hálózatok) számára.

### <a name="prepare-an-account-for-mobility-service-installation"></a>Fiók előkészítése a mobilitási szolgáltatás telepítéséhez

A mobilitási szolgáltatásnak telepítve kell lennie a replikálni kívánt gépeken.

- A Azure Migrate replikációs berendezés leküldéses telepítést is tud elvégezni a szolgáltatáson, ha engedélyezi a replikációt egy gépen, vagy manuálisan vagy telepítési eszközökkel telepíti.
- Ebben az oktatóanyagban leküldéses telepítéssel telepítjük a mobilitási szolgáltatást.
- A leküldéses telepítéshez elő kell készítenie egy fiókot, Azure Migrate Server Migration a virtuális gép eléréséhez használhatja. Ezt a fiókot csak a leküldéses telepítéshez használja a rendszer, ha nem telepíti manuálisan Mobility szolgáltatás a fiókot.

Készítse elő a fiókot az alábbiak szerint:

1. Készítsen elő egy tartományi vagy helyi fiókot, amely rendelkezik a virtuális gépre való telepítéshez szükséges engedélyekkel.
2. Windows rendszerű virtuális gépek esetén, ha nem használ tartományi fiókot, tiltsa le a távoli felhasználói hozzáférés-vezérlést a helyi gépen a **LocalAccountTokenFilterPolicy** DWORD bejegyzés hozzáadásával, a beállításjegyzékben a következő értékkel: ,HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System
3. Linux rendszerű virtuális gépekhez készítsen elő egy gyökérfiókot a forrás Linux-kiszolgálón.


### <a name="prepare-a-machine-for-the-replication-appliance"></a>Gép előkészítése a replikációs berendezéshez

A berendezés az Azure-ba replikálja a gépeket. A berendezés egyetlen, magas rendelkezésre áll rendelkezésre áll, helyszíni VMware virtuális gép, amely az alábbi összetevőket tartalmazza:

- **Konfigurációs kiszolgáló:** A konfigurációs kiszolgáló koordinálja a helyszíni rendszer és az Azure közötti kommunikációt, és kezeli az adatreplikációt.
- **Folyamatkiszolgáló:** A folyamatkiszolgáló replikációs átjáróként működik. Replikációs adatokat fogad; gyorsítótárazást, tömörítést és titkosítást használ, és egy gyorsítótár-tárfiókba küldi az Azure-ban. A folyamatkiszolgáló a mobilitási szolgáltatási ügynököt is telepíti a replikálni kívánt virtuális gépekre, és elvégzi a helyszíni VMware virtuális gépek automatikus felderítését.

Készítse elő a berendezést a következőképpen:

- [Tekintse át a berendezésre vonatkozó követelményeket.](migrate-replication-appliance.md#appliance-requirements) A replikációs berendezést általában egy letöltött OVA-fájllal állíthatja be egy VMware virtuális gépként. A sablon létrehoz egy berendezést, amely megfelel az összes követelménynek.
- A MySQL-t telepíteni kell a berendezésre. [Tekintse át a](migrate-replication-appliance.md#mysql-installation) telepítési módszereket.
- Tekintse át [a nyilvános felhőBELI URL-címeket,](migrate-replication-appliance.md#url-access) [Azure Government](migrate-replication-appliance.md#azure-government-url-access) url-címeket, amelyekhez a berendezés gépének hozzá kell férni.
- [Tekintse át a portokat,](migrate-replication-appliance.md#port-access) amelyekhez a replikációs berendezés gépének hozzá kell férni.



### <a name="check-vmware-requirements"></a>A VMware követelményeinek ellenőrzése

Győződjön meg arról, hogy a VMware-kiszolgálók és virtuális gépek megfelelnek az Azure-ba való migrálás követelményeinek. 

1. [Ellenőrizze](migrate-support-matrix-vmware-migration.md#vmware-requirements-agent-based) a VMware-kiszolgáló követelményeit.
2. [Ellenőrzés](migrate-support-matrix-vmware-migration.md#vm-requirements-agent-based) A virtuális gép migrálásra vonatkozó követelményei.
3. Ellenőrizze az Azure beállításait. Az Azure-ba replikált helyszíni virtuális gépeknek meg kell felelniük az [Azure-beli virtuális gépekre vonatkozó követelményeknek.](migrate-support-matrix-vmware-migration.md#azure-vm-requirements)
4. A virtuális gépeken bizonyos módosításokra van szükség az Azure-ba való áttelepítés előtt.
    - A migrálás megkezdése előtt fontos, hogy ezeket a módosításokat el is készítse. Ha a módosítás előtt átkullál egy virtuális gépet, előfordulhat, hogy a virtuális gép nem indul el az Azure-ban.
    - Tekintse át a [szükséges Windows-](prepare-for-migration.md#windows-machines) és Linux-módosításokat. [](prepare-for-migration.md#linux-machines)

> [!NOTE]
> Az ügynökalapú áttelepítés Azure Migrate Server Migration a Azure Site Recovery szolgáltatásain alapul. Egyes követelmények a dokumentációra Site Recovery hivatkoznak.

## <a name="set-up-the-replication-appliance"></a>A replikációs berendezés beállítása

Ez az eljárás bemutatja, hogyan állíthatja be a berendezést egy letöltött Open Virtualization Application - OVA-sablonnal. Ha nem tudja használni ezt a módszert, a berendezést egy szkript [használatával állíthatja be.](tutorial-migrate-physical-virtual-machines.md#set-up-the-replication-appliance) 

### <a name="download-the-replication-appliance-template"></a>A replikációs berendezés sablonjának letöltése

Töltse le a sablont a következőképpen:

1. A Azure Migrate a **Migrálási** célok alatt kattintson a **Kiszolgálók elemre.**
2. A **Azure Migrate - Servers**  >  **Azure Migrate: Server Migration (Kiszolgálók áttelepítése: Server Migration)** menüben kattintson a **Discover (Felderítés) parancsra.**

    ![Virtuális gépek felderítése](./media/tutorial-migrate-vmware-agent/migrate-discover.png)

3. A **Gépek felderítése**  >  **Virtualizáltak a gépek?** menüben kattintson az **Igen, VMware vSphere hipervizorral lehetőségre.**
4. A **Hogyan szeretné az áttelepítést? beállításban** válassza az **Ügynökalapú replikáció használata lehetőséget.**
5. A **Célterület területen** válassza ki azt az Azure-régiót, amelybe a gépeket át szeretné miolni.
6. Válassza **a Győződjön meg arról, hogy a migrálás célterülete régiónév.**
7. Kattintson az **Erőforrások létrehozása elemre.** Ez létrehoz egy Azure Site Recovery a háttérben. Miután erre a gombra kattintott, nem módosíthatja a projekt célterületét, és minden további migrálás ebbe a régióba lesz.

    ![Helyreállítási tár létrehozása](./media/tutorial-migrate-vmware-agent/create-resources.png)  

    > [!NOTE]
    > Ha a létrehozáskor a privát végpontot választotta a Azure Migrate-projekt kapcsolódási módszereként, a recovery services-tároló is konfigurálva lesz a privát végpontok csatlakoztatására. Győződjön meg arról, hogy a privát végpontok elérhetőek a replikációs berendezésről: [ **További információ**](how-to-use-azure-migrate-with-private-endpoints.md#troubleshoot-network-connectivity)


8. A **Szeretne új replikációs** berendezést telepíteni? beállításban válassza a **Replikációs berendezés telepítése lehetőséget.**
9. Kattintson a **Letöltés** gombra. Ezzel letölt egy OVF-sablont.
    ![Az OVA letöltése](./media/tutorial-migrate-vmware-agent/download-ova.png)
10. Jegyezze fel az erőforráscsoport és a Recovery Services-tároló nevét. Ezekre a berendezés üzembe helyezése során lesz szükség.


### <a name="import-the-template-in-vmware"></a>A sablon importálása a VMware-ben

Az OVF-sablon letöltése után importálja azt a VMware-be a replikációs alkalmazás létrehozásához egy Windows Server 2016-ot futtató VMware virtuális gépen.

1. Jelentkezzen be a VMware vCenter-kiszolgálóra vagy a vSphere ESXi-gazdagépre a VMware vSphere Client használatával.
2. A File **(Fájl) menüben** válassza **a Deploy OVF Template (OVF-sablon** üzembe helyezése) lehetőséget az **OVF-sablon telepítése varázsló megnyitásához.** 
3. A **Forrás kiválasztása mezőben** adja meg a letöltött OVF helyét.
4. A **Részletek áttekintése mezőben** válassza a **Tovább lehetőséget.**
5. A **Select name and folder (Név és mappa kiválasztása) és** a Select configuration **(Konfiguráció kiválasztása)** lapon fogadja el az alapértelmezett beállításokat.
6. A **Select storage (Tároló kiválasztása)** Select virtual disk format (Virtuális lemezformátum kiválasztása) beállításnál a legjobb teljesítmény érdekében válassza  >  a Thick Provision Eager **Zeroed lehetőséget.**
7. A varázsló többi lapján fogadja el az alapértelmezett beállításokat.
8. A **Ready to complete (Befejezésre** kész) területen válassza a Power on after deployment Finish **(Bekapcsolás az** üzembe helyezést követően ) lehetőséget a virtuális gép alapértelmezett beállításokkal való  >  **beállításának befejezéséhez.**

   > [!TIP]
   > Ha további hálózati adaptert szeretne hozzáadni, törölje a Power on after deployment Finish (Bekapcsolás az üzembe helyezést követően ) finish **(Bekapcsolás az** üzembe helyezést  >  **követően) gombra.** Alapértelmezés szerint a sablon egyetlen hálózati adaptert tartalmaz. Az üzembe helyezés után további hálózati adaptereket is hozzáadhat.

### <a name="start-appliance-setup"></a>Berendezés beállításának első lépése

1. Az VMware vSphere-konzolon kapcsolja be a virtuális gépet. A virtuális gép a bekapcsolásakor egy Windows Server 2016 telepítési folyamatot indít el.
2. Fogadja el a licencszerződést, és adjon meg egy rendszergazdai jelszót.
3. A telepítés befejezése után jelentkezzen be rendszergazdaként a virtuális gépre a rendszergazdai jelszóval. Az első indításakor néhány másodpercen belül elindul a replikációs berendezés telepítőeszköze (Azure Site Recovery Configuration Tool).
5. Adja meg a berendezés a Server Migrationben való regisztrálásának nevét. Ezután kattintson a **Tovább** gombra.
6. Az eszköz ellenőrzi, hogy a virtuális gép tud-e csatlakozni az Azure-hoz. A kapcsolat létrejötte után a **Sign in** (Bejelentkezés) gombra kattintva jelentkezzen be az Azure-előfizetésbe.
7. Várjon, amíg az eszköz befejezi egy Azure AD-alkalmazás regisztrálását a berendezés azonosításához. A berendezés újraindul.
1. Jelentkezzen be újra a gépre. A konfigurációs kiszolgáló felügyeleti varázslója néhány másodpercen belül automatikusan elindul.

### <a name="register-the-replication-appliance"></a>A replikációs berendezés regisztrálása

Fejezze be a replikációs berendezés beállítását és regisztrálását.

1. A berendezés beállítása párbeszédpanelen válassza a **Kapcsolat beállítása lehetőséget.**
2. Válassza ki azt a hálózati adaptert (alapértelmezés szerint csak egy hálózati adapter van), amelyet a replikációs berendezés a virtuális gépek felderítéséhez használ, és hogy leküldéses telepítést Mobility szolgáltatás a forrásgépeken.
3. Válassza ki azt a hálózati adaptert, amit a replikációs berendezés az Azure-ral való kapcsolathoz használ. Kattintson a **Mentés** gombra. Ezt a beállítást a konfigurálás után már nem módosíthatja.
4. Ha a berendezés proxykiszolgáló mögött található, meg kell adnia a proxybeállításokat.
    - Adja meg a proxy nevét **http://ip-address** , vagy **http://FQDN** néven. A HTTPS-proxykiszolgálók nem támogatottak.
5. Amikor a rendszer kéri az előfizetés, az erőforráscsoportok és a tároló részleteit, adja meg a berendezéssablon letöltésekor feljegyzett adatokat.
6. A **Független gyártótól származó szoftver telepítése** területen fogadja el a licencszerződést. Kattintson a **Letöltés és telepítés** gombra a MySQL-kiszolgáló telepítéséhez.
7. Válassza a **VMware PowerCLI telepítése** elemet. Mielőtt ezt megtenné, zárja be az összes böngészőablakot. Ezután válassza a **Folytatás** elemet.
8. A **Berendezés konfigurációjának ellenőrzése** területen a rendszer ellenőrzi az előfeltételeket, mielőtt továbblépne.
9. A **VCenter-kiszolgáló vagy vSphere ESXi-kiszolgáló hitelesítő adatainak konfigurálása** területen adja meg azon vCenter-kiszolgáló vagy VSphere-gazdagép teljes tartománynevét vagy IP-címét, ahol a replikálni kívánt virtuális gépek találhatók. Adja meg a portot, amelyen a kiszolgáló figyel. Adja meg a VMware-kiszolgáló rövid nevét, amelyet a tárolóban használni fog.
10. Adja meg a VMware-felderítéshez létrehozott [fiók](#prepare-an-account-to-discover-vms) hitelesítő adatait. Válassza a **Folytatás**  >  **hozzáadása lehetőséget.**
11. A **Virtuális gép hitelesítő adatainak konfigurálása** [](#prepare-an-account-for-mobility-service-installation) részen adja meg a virtuális gép leküldéses telepítéséhez létrehozott hitelesítő Mobility szolgáltatás, amikor engedélyezi a virtuális gépek replikációját.  
    - A Windows-alapú gépek esetében a fióknak helyi rendszergazdai jogosultságokkal kell rendelkeznie a replikálni kívánt gépeken.
    - Linux-alapú gépeken a rendszergazdai fiók adatait adja meg.
12. Válassza a **Konfiguráció véglegesítése** elemet a regisztráció befejezéséhez.


A replikációs berendezés regisztrációja után a Azure Migrate Server Assessment a megadott beállításokkal csatlakozik a VMware-kiszolgálókhoz, és felderíti a virtuális gépeket. A felderített virtuális gépeket a **Felderített** elemek kezelése  >  lapon, az **Egyéb lapon tudja** megtekinteni.



## <a name="replicate-vms"></a>Virtuális gépek replikálása

Válassza a Virtuális gépek lehetőséget a migráláshoz.

> [!NOTE]
> A portálon egyszerre legfeljebb 10 gépet választhat ki a replikációhoz. Ha többet kell replikálni, csoportosítsa őket 10-es kötegekbe.

1. A kiszolgálók Azure Migrate projektben **>:** **Azure Migrate Server Migration**, kattintson a **Replikálás elemre.**

    ![Képernyőkép a kiszolgálók képernyőről a Azure Migrate. A Replikálás gomb a következő Azure Migrate: Kiszolgálóáttelepítés az Áttelepítési eszközök alatt.](./media/tutorial-migrate-vmware-agent/select-replicate.png)

2. A **Replikálás**, > **Forrásbeállítások** > **Virtualizáltak a gépek?** területen válassza az **Igen, a VMware vSphere szolgáltatással** lehetőséget.
3. A **Helyszíni berendezés mezőben** válassza ki a Azure Migrate beállított berendezés nevét.
4. A **vCenter-kiszolgálón** adja meg a virtuális gépeket kezelő vCenter-kiszolgáló nevét, vagy azt a vSphere-kiszolgálót, amelyen a virtuális gépek üzemelnek.
5. A **Folyamatkiszolgáló részen** válassza ki a replikációs berendezés nevét.
6. A **Vendég hitelesítő adatai területen** adja meg azt a virtuális gép rendszergazdai fiókját, amely a virtuális gép leküldéses telepítéséhez Mobility szolgáltatás. Ezután kattintson a **Tovább: Virtuális gépek elemre.**

    ![A Replikálás képernyő Forrásbeállítások lapjának képernyőképe. A Vendég hitelesítő adatai mező ki van emelve, az értéke pedig VM-admin-account.](./media/tutorial-migrate-vmware-agent/source-settings.png)

7. A **Virtual Machines** válassza ki a replikálni kívánt gépeket.

    - Ha futtatta a virtuális gépek értékelését, alkalmazhatja az eredményekben szereplő, a virtuális gépek méretezésére és a lemeztípusra (prémium/standard) vonatkozó javaslatokat. Ehhez a **Importálja a migrálási beállításokat az Azure Migrate-értékelésből?** területen válassza az **Igen** lehetőséget.
    - Ha nem futtatott értékelést, vagy nem szeretné használni az értékelés beállításait, válassza a **Nem** lehetőséget.
    - Ha az értékelés használatát választotta, válassza ki a virtuálisgép-csoportot és az értékelés nevét.
8. A **Rendelkezésre állási beállítások között válassza** a következőt:
    -  Rendelkezésre állási zóna a migrált gép adott rendelkezésre állási zónában való rögzítéshez a régióban. Ezzel a beállítással elosztja a többcsomópontos alkalmazásréteget Availability Zones. Ha ezt a lehetőséget választja, a Számítás lapon meg kell adnia az egyes kiválasztott gépekhez használni kívánt rendelkezésre állási zónát. Ez a lehetőség csak akkor érhető el, ha a migráláshoz kiválasztott célterület támogatja a Availability Zones
    -  Rendelkezésre állási készlet az áttelepített gép rendelkezésre állási készletbe való beállításhoz. A kiválasztott célerőforrás-csoportnak egy vagy több rendelkezésre állási csoporttal kell lennie a beállítás használatának érdekében.
    - Nincs szükség infrastruktúra-redundanciával kapcsolatos beállításra, ha a migrált gépekhez nincs szükség egyik rendelkezésre állási konfigurációra sem.
9. Ellenőrizze az összes átemelni kívánt virtuális gépet. Ezután kattintson **a Tovább: Célbeállítások elemre.**
10. A **Célbeállítások** között válassza ki az előfizetést és a célrégiót, ahova migrálni szeretne, majd adja meg az erőforráscsoportot, amelyben az Azure-beli virtuális gépek megtalálhatóak lesznek a migrálást követően.
11. A **Virtuális hálózat** területen válassza ki az Azure-beli virtuális hálózatot/alhálózatot, amelyhez az Azure-beli virtuális gépek a migrálást követően csatlakoznak majd.  
12. A  **Gyorsítótár tárfiókja beállításban** tartsa meg az alapértelmezett beállítást a projekthez automatikusan létrehozott gyorsítótár-tárfiók használatára. Ha egy másik tárfiókot szeretne használni a replikáció gyorsítótár-tárfiókjaként, használja a legördülő menüt. <br/> 
    > [!NOTE]
    >
    > - Ha a privát végpontot választotta a Azure Migrate projekt kapcsolódási módszereként, adjon hozzáférést a Recovery Services-tárolónak a gyorsítótár-tárfiókhoz. [**További információ**](how-to-use-azure-migrate-with-private-endpoints.md#grant-access-permissions-to-the-recovery-services-vault)
    > - Privát társviszony-létesítésű ExpressRoute használatával való replikáláshoz hozzon létre egy privát végpontot a gyorsítótár tárfiókja számára. [**További információ**](how-to-use-azure-migrate-with-private-endpoints.md#create-a-private-endpoint-for-the-storage-account-optional) 
13. A **Rendelkezésre állási beállítások között válassza** a következőt:
    -  Rendelkezésre állási zóna az áttelepített gép adott rendelkezésre állási zónában való rögzítéshez a régióban. Ezzel a beállítással elosztja a többcsomópontos alkalmazásréteget Availability Zones. Ha ezt a lehetőséget választja, a Számítás lapon meg kell adnia az egyes kiválasztott gépekhez használni kívánt rendelkezésre állási zónát. Ez a lehetőség csak akkor érhető el, ha a migráláshoz kiválasztott célterület támogatja a Availability Zones
    -  Rendelkezésre állási készlet az áttelepített gép rendelkezésre állási készletbe való beállításhoz. A kiválasztott célerőforrás-csoportnak egy vagy több rendelkezésre állási csoporttal kell lennie a beállítás használatának érdekében.
    - Nincs szükség infrastruktúra-redundanciának a beállításra, ha a migrált gépek egyik rendelkezésre állási konfigurációját sem kell megadnia.
14. A **Lemeztitkosítás típusa beállításban** válassza a következőt:
    - Titkosítás az rest-at-rest kulccsal platform által felügyelt kulccsal
    - Titkosítás az ügyfelek által felügyelt kulccsal
    - Kettős titkosítás platform által felügyelt és felhasználó által kezelt kulcsokkal

   > [!NOTE]
   > A virtuális gépek CMK-val való replikálása érdekében létre kell hoznia egy lemeztitkosítási [készletet a](../virtual-machines/disks-enable-customer-managed-keys-portal.md#set-up-your-disk-encryption-set) célként meghatározott erőforráscsoportban. A lemeztitkosítási készlet objektuma Managed Disks leképezi azokat egy Key Vault, amely tartalmazza az SSE-hez használni kívánt CMK-t.
  
15. Az **Azure Hybrid Benefit** területen:

    - Válassza a **Nem** lehetőséget, ha nem szeretné alkalmazni az Azure Hybrid Benefit szolgáltatást. Ezután kattintson a **Tovább** gombra.
    - Válassza az **Igen** lehetőséget, ha aktív frissítési garanciával vagy Windows Server-előfizetéssel rendelkező gépei vannak, és alkalmazni szeretné az előnyöket a migrált gépekre. Ezután kattintson a **Tovább** gombra.

    ![Célbeállítások](./media/tutorial-migrate-vmware/target-settings.png)

16. A **Számítás területen** tekintse át a virtuális gép nevét, méretét, operációsrendszer-lemez típusát és rendelkezésre állási konfigurációját (ha az előző lépésben kiválasztotta). A virtuális gépeknek meg kell felelniük az [Azure-követelményeknek](migrate-support-matrix-vmware-migration.md#azure-vm-requirements).

   - **Virtuális gép mérete:** Ha értékelési javaslatokat használ, a virtuális gép mérete legördülő menüben megjelenik az ajánlott méret. Egyéb esetben az Azure Migrate az Azure-előfizetésben található leginkább megfelelő érték alapján választja ki a méretet. Alternatív megoldásként az **Azure-beli virtuális gép mérete** területen manuálisan is kiválaszthatja a méretet. 
    - **Operációsrendszer-lemez:** Adja meg a virtuális gép operációsrendszer- (rendszerindítási) lemezét. Az operációsrendszer-lemez az a lemez, amelyen az operációs rendszer rendszerbetöltője és telepítője található. 
    - **Rendelkezésre állási zóna:** Adja meg a használni kívánt rendelkezésre állási zónát.
    - **Rendelkezésre állási készlet:** Adja meg a használni kívánt rendelkezésre állási készletet.

17. A **Lemezek területen** adja meg, hogy a virtuálisgép-lemezeket replikálni kell-e az Azure-ba, majd válassza ki a lemez típusát (standard SSD/HDD vagy prémium felügyelt lemezek) az Azure-ban. Ezután kattintson a **Tovább** gombra.
    - Kizárhat lemezeket a replikációból.
    - Ha kizárja a lemezeket, azok nem lesznek jelen az Azure-beli virtuális gépen a migrációt követően. 

18. Az **Áttekintés és a replikáció megkezdése** területen tekintse át a beállításokat, majd kattintson a **Replikálás** gombra a kiszolgálók kezdeti replikálásának elindításához.

> [!NOTE]
> A replikációs beállításokat a replikáció kezdete előtt bármikor frissítheti: **Gépek**  >  **replikálása kezelése.** A replikáció kezdete után a beállítások már nem módosíthatók.


## <a name="track-and-monitor"></a>Nyomon követése és figyelése

1. Nyomon követheti a feladat állapotát a portál értesítései között. 

    ![Feladat nyomon követése](./media/tutorial-migrate-vmware-agent/jobs.png)
    
2. A replikáció állapotának figyelése érdekében kattintson **a Kiszolgálók** replikálása elemre **a Azure Migrate: Kiszolgálóáttelepítés gombra.**

    ![Replikáció monitorozása](./media/tutorial-migrate-vmware-agent/replicate-servers.png)

A replikáció a következőképpen történik:
- Amikor a Replikáció megkezdése feladat sikeresen befejeződik, a gépek megkezdik a kezdeti replikációt az Azure-ba.
- A kezdeti replikáció befejeződése után megkezdődik a változásreplikáció. A helyszíni lemezek növekményes módosításait a rendszer rendszeres időközönként replikálja az Azure-beli replikalemezekbe.


## <a name="run-a-test-migration"></a>Migrálási teszt futtatása


A változásreplikáció megkezdésekor futtathat egy migrálási tesztet a virtuális gépeken, mielőtt teljes migrálást futtat az Azure-ba. Javasoljuk, hogy ezt minden gépnél legalább egyszer, az áttelepítés előtt tegye meg.

- A migrálási teszt futtatása ellenőrzi, hogy a migrálás a várt módon működik-e anélkül, hogy ez hatással lenne a továbbra is működőképes helyszíni gépekre, és folytatja a replikálás működését. 
- A migrálási teszt egy Azure-beli virtuális gép replikált adatokkal (általában az Azure-előfizetésben található nem éles virtuális hálózatra való migrálás) használatával szimulálja a migrálást.
- A replikált Azure-beli virtuális gép használatával ellenőrizheti a migrálást, tesztelheti az alkalmazásokat, és a teljes migrálás előtt megoldhatja a problémákat.

A következőképpen hajtsa végre a migrálási tesztet:


1. Az **Áttelepítési célok**  >  **Kiszolgálók**  >  **Azure Migrate: Kiszolgálóáttelepítés alatt** kattintson a **Migrált kiszolgálók tesztelése elemre.**

     ![Migrált kiszolgálók tesztelése](./media/tutorial-migrate-vmware-agent/test-migrated-servers.png)

2. Kattintson a jobb gombbal a tesztelni kívánt virtuális gépre, majd válassza a **Migrálás tesztelése** lehetőséget.

    ![Migrálási teszt](./media/tutorial-migrate-vmware-agent/test-migrate.png)

3. A **Migrálási teszt** területen válassza ki az Azure-beli virtuális hálózatot, amelyen az Azure-beli virtuális gép a migrálást követően megtalálható lesz. Javasoljuk, hogy ne éles virtuális hálózatot használjon.
4. A **Migrálási teszt** feladat elindul. A feladatot a portál értesítései között követheti nyomon.
5. A migrálás befejeztével az Azure Portal **Virtuális gépek** területén tekintheti meg a migrált Azure-beli virtuális gépet. A gép nevében a **-Test** utótag szerepel.
6. A teszt elvégzése után a **Gépek replikálása** területen kattintson a jobb gombbal az Azure-beli virtuális gépre, majd kattintson a **Migrálási teszt törlése** gombra.

    ![Migrálás törlése](./media/tutorial-migrate-vmware-agent/clean-up.png)


## <a name="migrate-vms"></a>Virtuális gép migrálása

Miután ellenőrizte, hogy a migrálási teszt a várt módon működik-e, migrálhatja a helyszíni gépeket.

1. Az Azure Migrate server migration **>: Server** Migration (Kiszolgálók áttelepítése Azure Migrate) projektben kattintson a  >   **Replicating servers (Kiszolgálók replikálása) elemre.**

    ![Kiszolgálók replikálása](./media/tutorial-migrate-vmware-agent/replicate-servers.png)

2. A **Gépek replikálása** területen kattintson jobb gombbal a virtuális gépre, majd kattintson a **Migrálás** elemre.
3. Az **Migrate** Shut down virtual machines and perform a planned migration with no data loss (Virtuális gépek migrálásának áttelepítése adatvesztés nélkül) beállításnál válassza az  >   **Igen**  >  **OK lehetőséget.**
    - Alapértelmezés szerint Azure Migrate a minimális adatvesztés biztosítása érdekében leállítja a helyszíni virtuális gépet. 
    - Ha nem szeretné leállítani a virtuális gépet, válassza a **Nem** lehetőséget.
4. A virtuálisgép-migrálási feladat elindul. A feladatot az Azure-értesítések között követheti nyomon.
5. A feladat befejeztével a virtuális gépet a **Virtuális gépek** oldalon tekintheti meg és kezelheti.

## <a name="complete-the-migration"></a>A migrálás befejezése

1. A migrálás után kattintson a jobb gombbal a virtuális gépre, > **migrálásának leállítása parancsra.** Ez a következőket teszi:
    - Leállítja a helyszíni gép replikációját.
    - Eltávolítja a gépet a **replikáló** kiszolgálók számból a Azure Migrate: Server Migration gombra.
    - Megtisztítja a virtuális gép replikációs állapotinformációit.
2. Telepítse az Azure-beli virtuális gépet [Windows-](../virtual-machines/extensions/agent-windows.md) vagy [Linux-ügynökként](../virtual-machines/extensions/agent-linux.md) a migrált gépekre.
3. Hajtson végre minden áttelepítés utáni módosítást az alkalmazáson (például adatbázis-kapcsolati sztringek frissítése és webes kiszolgálók konfigurálása).
4. Végezze el a végső alkalmazás- és áttelepítés-elfogadás teszteket az Azure-on jelenleg futó alkalmazásoknál.
5. Forgalom átvágása a migrált Azure-beli virtuálisgép-példányra.
6. Távolítsa el a helyszíni virtuális gépeket a helyi virtuálisgép-készletéből.
7. Távolítsa el a helyszíni virtuális gépeket helyi biztonsági mentésekből.
8. Frissítse minden belső dokumentációját az Azure virtuális gépek új helyével és IP-címével. 

## <a name="post-migration-best-practices"></a>Ajánlott eljárások a migrálást követő folyamathoz

- Helyszíni
    - Helyezze át az alkalmazás-forgalmat az áttelepített Azure virtuálisgép-példányon futó alkalmazásra.
    - Távolítsa el a helyszíni virtuális gépeket a helyi virtuálisgép-készletéből.
    - Távolítsa el a helyszíni virtuális gépeket helyi biztonsági mentésekből.
    - Frissítse minden belső dokumentációját az Azure virtuális gépek új helyével és IP-címével.
- Az Azure-beli virtuális gépek beállításainak finomhangolása a migrálás után:
    - Az [Azure virtuálisgép-ügynök](../virtual-machines/extensions/agent-windows.md) kezeli a virtuális gépek kommunikációját az Azure-hálóvezérlővel. Erre egyes Azure-szolgáltatások, például az Azure Backup, a Site Recovery és az Azure Security esetében van szükség. Amikor ügynökalapú migrálással migrál virtuális gépeket, a mobilitási szolgáltatás telepítője Azure-beli virtuálisgép-ügynököt telepít Windows rendszerű gépekre. Linux rendszerű virtuális gépeken javasoljuk, hogy a migrálás után telepítse az ügynököt.
    - Manuálisan távolítsa el Mobility szolgáltatás azure-beli virtuális gépről a migrálás után.
    - Manuálisan távolítsa el a VMware-eszközöket a migrálás után.
- Az Azure-ban:
    - Hajtson végre minden áttelepítés utáni módosítást az alkalmazáson (például adatbázis-kapcsolati sztringek frissítése és webes kiszolgálók konfigurálása).
    - Végezze el a végső alkalmazás- és áttelepítés-elfogadás teszteket az Azure-on jelenleg futó alkalmazásoknál.
- Üzletmenet-folytonosság/vészhelyreállítás
    - Biztonságba helyezheti az adatokat, ha biztonsági másolatot készít az Azure virtuális gépekről az Azure Backup szolgáltatással. [További információ](../backup/quick-backup-vm-portal.md).
    - Biztosíthatja a számítási feladatok folyamatos futtatását és rendelkezésre állását, ha az Azure virtuális gépeket egy másodlagos régióba replikálja a Site Recovery használatával. [További információ](../site-recovery/azure-to-azure-tutorial-enable-replication.md).
- A biztonság fokozása érdekében:
    - Zárolja és korlátozza a bejövő forgalomhoz való hozzáférést [a Azure Security Center – Időben elérhető adminisztráció.](../security-center/security-center-just-in-time.md)
    - Korlátozza a forgalmat felügyeleti végpontokra [hálózati biztonsági csoportok](../virtual-network/network-security-groups-overview.md) használatával.
    - Az [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md) üzembe helyezésével biztonságba helyezheti a lemezeket, és megóvhatja az adatokat a lopási kísérletektől és a jogosulatlan hozzáféréstől.
    - Látogasson el a [az Azure Security Center](https://azure.microsoft.com/services/security-center/) webhelyére, és tudjon meg többet az [IaaS-erőforrások biztosításáról](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/).
- Figyelési és felügyeleti eszközök:
    - Fontolja meg az [Azure Cost Management](../cost-management-billing/cloudyn/overview.md) üzembe helyezését az erőforrás-használat és a költségek figyeléséhez.




 ## <a name="next-steps"></a>Következő lépések

A felhőbe [való migrálás folyamatának kivizsgálása](/azure/architecture/cloud-adoption/getting-started/migrate) az Azure felhőadaptálási keretrendszer.