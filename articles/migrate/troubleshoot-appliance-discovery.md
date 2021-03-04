---
title: Azure Migrate berendezés üzembe helyezésének és felderítésének hibáinak megoldása
description: Segítség kérése a berendezések üzembe helyezéséhez és a gépi felderítéshez.
author: vineetvikram
ms.author: vivikram
ms.manager: abhemraj
ms.topic: troubleshooting
ms.date: 01/02/2020
ms.openlocfilehash: f3331504540e8c23c3a83fe245bae27ca6c49385
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102041280"
---
# <a name="troubleshoot-the-azure-migrate-appliance-and-discovery"></a>A Azure Migrate készülék és a felderítés hibáinak megoldása

Ez a cikk segítséget nyújt a [Azure Migrate](migrate-services-overview.md) berendezés telepítésekor felmerülő problémák megoldásában, és a berendezés használatával felderíti a helyszíni gépeket.


## <a name="whats-supported"></a>Mik támogatottak?

[Tekintse át](migrate-appliance.md) a berendezések támogatási követelményeit.


## <a name="invalid-ovf-manifest-entry"></a>"Érvénytelen OVF manifest-bejegyzés"

Ha "a megadott jegyzékfájl érvénytelen: érvénytelen OVF manifest-bejegyzés" hibaüzenet jelenik meg, tegye a következőket:

1. Ellenőrizze, hogy a Azure Migrate készülék PETESEJTJEInek fájlja helyesen van-e letöltve a kivonatoló értékének ellenőrzésével. [További információk](./tutorial-discover-vmware.md). Ha a kivonatoló érték nem egyezik, töltse le újra a PETESEJT-fájlt, majd próbálja megismételni a telepítést.
2. Ha a telepítés továbbra is meghiúsul, és a VMware vSphere ügyfél használatával telepíti a OVF-fájlt, próbálja meg telepíteni a vSphere webes ügyfelén keresztül. Ha az üzembe helyezés továbbra is sikertelen, próbálkozzon más böngészővel.
3. Ha a vSphere webes ügyfélprogramot használja, és vCenter Server 6,5-es vagy 6,7-es számítógépen szeretné telepíteni, próbálja meg közvetlenül telepíteni a PETESEJTeket az ESXi-gazdagépre:
   - Kapcsolódjon közvetlenül az ESXi-gazdagéphez (vCenter Server helyett) a webes ügyféllel (https://<*gazdagép IP-címe*>/UI).
   - A **saját**  >  **leltár** területen válassza a **fájl**  >  **központi telepítése OVF sablont**. Tallózással keresse meg a PETESEJTeket, és fejezze be a telepítést.
4. Ha az üzembe helyezés továbbra is sikertelen, forduljon Azure Migrate támogatási szolgálathoz.

## <a name="cant-connect-to-the-internet"></a>Nem lehet csatlakozni az internethez

Ez akkor fordulhat elő, ha a berendezés számítógépe proxy mögött van.

- Adja meg az engedélyezési hitelesítő adatokat, ha a proxynak szüksége van rájuk.
- Ha URL-alapú tűzfal-proxyt használ a kimenő kapcsolatok vezérléséhez, adja hozzá [ezeket az URL-címeket](migrate-appliance.md#url-access) egy engedélyezési listához.
- Ha elfogó proxyt használ az internethez való csatlakozáshoz, [ezeket a lépéseket követve](./migrate-appliance.md)importálja a tanúsítványt a berendezés virtuális gépén.


## <a name="cant-sign-into-azure-from-the-appliance-web-app"></a>Nem lehet bejelentkezni az Azure-ba a készülék webalkalmazásból

A "Sajnáljuk, de hiba történt a bejelentkezéskor" üzenet jelenik meg, ha a helytelen Azure-fiókot használja az Azure-ba való bejelentkezéshez. Ez a hiba néhány okból fordul elő:

- Ha bejelentkezik a készülék webalkalmazásba a nyilvános felhőben, a kormányzati felhő portál felhasználói fiókjának hitelesítő adataival.
- Ha a saját felhőalapú portál felhasználói fiókjának hitelesítő adataival jelentkezik be a kormányzati felhőbe.

Győződjön meg arról, hogy a megfelelő hitelesítő adatokat használja.

##  <a name="datetime-synchronization-error"></a>Dátum-és időszinkronizálási hiba

A dátum-és időszinkronizálással (802) kapcsolatos hiba azt jelzi, hogy a kiszolgáló órája több mint öt percen belül nem lehet szinkronizálva az aktuális idővel. A gyűjtő virtuális gép órájának módosítása a jelenlegi időpontra:

1. Nyisson meg egy rendszergazdai parancssort a virtuális gépen.
2. Az időzóna ellenõrzéséhez futtassa a **w32tm/TZ**.
3. Az idő szinkronizálásához futtassa a **w32tm resync**.


## <a name="unabletoconnecttoserver"></a>"UnableToConnectToServer"

Ha ezt a hibaüzenetet kapja, előfordulhat, hogy nem tud csatlakozni a (z) vCenter Server *servername*. com: 9443. A hiba részletei azt jelzik, hogy nem figyelhető végpont, `https://\*servername*.com:9443/sdk` amely fogadja az üzenetet.

- Győződjön meg arról, hogy a készülék legújabb verzióját futtatja-e. Ha nem, frissítse a készüléket a [legújabb verzióra](./migrate-appliance.md).
- Ha a probléma továbbra is a legújabb verzióban következik be, előfordulhat, hogy a készülék nem tudja feloldani a megadott vCenter Server nevet, vagy a megadott port helytelen lehet. Alapértelmezés szerint, ha a port nincs megadva, a gyűjtő megpróbál csatlakozni a 443-as számú portszámhoz.

    1. Pingelje a *servername*. com-t a készülékről.
    2. Ha az 1. lépés meghiúsul, próbáljon meg csatlakozni a vCenter-kiszolgálóhoz az IP-cím használatával.
    3. Azonosítsa a vCenter Serverhoz való csatlakozáshoz használandó portszámot.
    4. Ellenőrizze, hogy a vCenter Server működik-e.


## <a name="error-6005260039-appliance-might-not-be-registered"></a>60052/60039-es hiba: lehet, hogy a berendezés nincs regisztrálva

- 60052-es hiba: "előfordulhat, hogy a készülék nem regisztrálható sikeresen a Azure Migrate projektben", ha a berendezés regisztrálásához használt Azure-fiók nem rendelkezik megfelelő engedélyekkel.
    - Győződjön meg arról, hogy a készülék regisztrálásához használt Azure-beli felhasználói fiók legalább közreműködői engedélyekkel rendelkezik az előfizetésben.
    - [További](./migrate-appliance.md#appliance---vmware) információ a szükséges Azure-szerepkörökről és-engedélyekről.
- 60039-es hiba: "előfordulhat, hogy a készülék nem regisztrálható sikeresen a Azure Migrate projektben" akkor fordulhat elő, ha a regisztráció sikertelen, mert a berendezés regisztrálásához használt Azure Migrate projekt nem található.
    - A Azure Portalon, és győződjön meg arról, hogy a projekt létezik az erőforráscsoporthoz.
    - Ha a projekt nem létezik, hozzon létre egy új Azure Migrate projektet az erőforráscsoporthoz, és regisztrálja újra a készüléket. [Ismerje meg, hogyan](./create-manage-projects.md#create-a-project-for-the-first-time) hozhat létre új projektet.

## <a name="error-6003060031-key-vault-management-operation-failed"></a>60030/60031-es hiba: a Key Vault felügyeleti művelet sikertelen volt.

Ha a 60030-es vagy a 60031-es hibát kapja, "az Azure Key Vault felügyeleti művelet sikertelen", tegye a következőket:
- Győződjön meg arról, hogy a készülék regisztrálásához használt Azure-beli felhasználói fiók legalább közreműködői engedélyekkel rendelkezik az előfizetésben.
- Győződjön meg arról, hogy a fiók rendelkezik hozzáféréssel a hibaüzenetben megadott kulcstartóhoz, majd próbálja megismételni a műveletet.
- Ha a probléma továbbra is fennáll, forduljon a Microsoft támogatási szolgálatához.
- [További](./migrate-appliance.md#appliance---vmware) információ a szükséges Azure-szerepkörökről és-engedélyekről.

## <a name="error-60028-discovery-couldnt-be-initiated"></a>60028-es hiba: nem sikerült elindítani a felderítést

60028-es hiba: "a felderítés hiba miatt nem indítható el. A gazdagépek vagy fürtök megadott listája nem tudta végrehajtani a műveletet, mert nem sikerült elindítani a felderítést a hiba miatt, mert a virtuális gép információinak elérése vagy beolvasása során probléma merült fel. A többi gazdagép hozzáadása sikeresen megtörtént.

- Adja hozzá a hibaüzenetben felsorolt gazdagépeket a **gazdagép hozzáadása** lehetőség használatával.
- Ha érvényesítési hiba történt, tekintse át a Szervizelési útmutatót a hibák kijavításához, majd próbálkozzon újra a **Mentés és a felderítés megkezdése** lehetőséggel.

## <a name="error-60025-azure-ad-operation-failed"></a>60025-es hiba: nem sikerült az Azure AD-művelet 
60025-es hiba: "az Azure AD-művelet nem sikerült. Hiba történt az Azure AD-alkalmazás létrehozásakor vagy frissítésekor: akkor következik be, amikor a felderítés elindításához használt Azure-felhasználói fiók eltér a berendezés regisztrálásához használt fióktól. Tegye a következők egyikét:

- Győződjön meg arról, hogy a felderítést kezdeményező felhasználói fiók megegyezik a berendezés regisztrálásához használttal.
- Adja meg Azure Active Directory alkalmazás hozzáférési engedélyeit ahhoz a felhasználói fiókhoz, amelyhez a felderítési művelet sikertelen.
- Törölje az Azure Migrate projekthez korábban létrehozott erőforráscsoportot. Hozzon létre egy másik erőforráscsoportot az újraindításhoz.
- [További](./migrate-appliance.md#appliance---vmware) információ Azure Active Directory alkalmazás engedélyeiről.


## <a name="error-50004-cant-connect-to-host-or-cluster"></a>50004-es hiba: nem lehet csatlakozni a gazdagéphez vagy a fürthöz

50004-es hiba: "nem lehet csatlakozni egy gazdagéphez vagy fürthöz, mert a kiszolgáló neve nem oldható fel. WinRM-hibakód: 0x803381B9 "akkor fordulhat elő, ha a berendezés Azure DNS szolgáltatása nem tudja feloldani a megadott fürtöt vagy állomásnevet.

- Ha ezt a hibát látja a fürtön, a fürt teljes tartományneve.
- Előfordulhat, hogy ez a hiba a fürtben lévő gazdagépek esetében is megjelenik. Ez azt jelzi, hogy a berendezés tud csatlakozni a fürthöz, de a fürt olyan állomásnévket ad vissza, amelyek nem teljes tartománynevek. A hiba megoldásához frissítse a gazdagépek fájlját a készüléken az IP-cím és az állomásnevek hozzárendelésének hozzáadásával:
    1. Nyissa meg a Jegyzettömböt rendszergazdaként.
    2. Nyissa meg a C:\Windows\System32\Drivers\etc\hosts fájlt.
    3. Adja hozzá az IP-címet és az állomásnevet egy sorban. Ismételje meg az összes olyan gazdagépet vagy fürtöt, ahol ez a hiba látható.
    4. Mentse és zárja be a hosts fájlt.
    5. Győződjön meg arról, hogy a készülék tud-e csatlakozni a gazdagépekhez a berendezés-kezelő alkalmazás használatával. 30 perc elteltével a Azure Portalban láthatja a gazdagépek legfrissebb információit.


## <a name="error-60001-unable-to-connect-to-server"></a>60001-es hiba: nem sikerült csatlakozni a kiszolgálóhoz 

- Ellenőrizze, hogy van-e kapcsolat a készülék és a kiszolgáló között
- Linux-kiszolgáló esetén győződjön meg arról, hogy a jelszó-alapú hitelesítés engedélyezve van a következő lépések végrehajtásával:
    1. Jelentkezzen be a Linux rendszerű gépre, és nyissa meg az SSH konfigurációs fájlt a "VI/etc/ssh/sshd_config" parancs használatával.
    2. Állítsa az "PasswordAuthentication" beállítást Igen értékre. Mentse a fájlt.
    3. Az SSH szolgáltatás újraindítása a "szolgáltatás sshd újraindítása" futtatásával
- Ha ez a Windows Server, győződjön meg arról, hogy a 5985-es port nyitva van, hogy engedélyezze a távoli WMI-hívásokat.
- Ha egy GCP Linux-kiszolgálót vagy egy root felhasználót használ, a következő parancsokkal módosíthatja a gyökérszintű bejelentkezés alapértelmezett beállítását
    1. Jelentkezzen be a Linux rendszerű gépre, és nyissa meg az SSH konfigurációs fájlt a "VI/etc/ssh/sshd_config" parancs használatával.
    2. Állítsa az "PermitRootLogin" beállítást Igen értékre.
    3. Az SSH szolgáltatás újraindítása a "szolgáltatás sshd újraindítása" futtatásával

## <a name="error-no-suitable-authentication-method-found"></a>Hiba: nem található megfelelő hitelesítési módszer

Győződjön meg arról, hogy a jelszó alapú hitelesítés engedélyezve van a Linux-kiszolgálón a következő lépések végrehajtásával:
    1. Jelentkezzen be a Linux rendszerű gépre, és nyissa meg az SSH konfigurációs fájlt a "VI/etc/ssh/sshd_config" parancs használatával.
    2. Állítsa az "PasswordAuthentication" beállítást Igen értékre. Mentse a fájlt.
    3. Az SSH szolgáltatás újraindítása a "szolgáltatás sshd újraindítása" futtatásával


## <a name="discovered-vms-not-in-portal"></a>Felderített virtuális gépek nem a portálon

Ha a felderítési állapot "felderítés folyamatban", de még nem látja a virtuális gépeket a portálon, várjon néhány percet:
- Egy VMware virtuális gép esetében körülbelül 15 percet vesz igénybe.
- A Hyper-V virtuális gépek felderítéséhez minden egyes hozzáadott állomás körülbelül két percet vesz igénybe.

Ha a várakozás után az állapot nem változik, válassza a **kiszolgálók** lapon a **frissítés** lehetőséget. Ennek meg kell jelennie a felderített kiszolgálók számának Azure Migrateban: kiszolgáló értékelése és Azure Migrate: kiszolgáló áttelepítése.

Ha ez nem működik, és a VMware-kiszolgálókat felkeresi:

- Győződjön meg arról, hogy a megadott vCenter-fiókhoz megfelelő engedélyek vannak beállítva, és legalább egy virtuális géphez hozzáfér.
- Azure Migrate nem tudja felderíteni a VMware virtuális gépeket, ha a vCenter-fiókhoz hozzáférés van megadva a vCenter VM-mappa szintjén. [További](set-discovery-scope.md) információ a hatókör-felderítésről.

## <a name="vm-data-not-in-portal"></a>A virtuális gép nem a portálon

Ha a felderített virtuális gépek nem jelennek meg a portálon, vagy ha a VM-adat elavult, várjon néhány percet. Akár 30 percet is igénybe vehet, hogy a felderített virtuális gépek konfigurációs információi megjelenjenek a portálon. Eltarthat néhány órát az alkalmazásadatok változásainak megjelenítéséhez. Ha ez idő után nem állnak rendelkezésre adatsorok, próbálkozzon a frissítéssel, a következőképpen

1. A **kiszolgálók**  >  **Azure Migrate kiszolgáló értékelése** lapon válassza az **Áttekintés** lehetőséget.
2. A **kezelés** területen válassza a **Agent Health** lehetőséget.
3. Válassza az **ügynök frissítése** lehetőséget.
4. Várjon, amíg a frissítési művelet befejeződik. Ekkor megjelenik a naprakész információ.

## <a name="deleted-vms-appear-in-portal"></a>A törölt virtuális gépek megjelennek a portálon

Ha törli a virtuális gépeket, és azok továbbra is megjelennek a portálon, várjon 30 percet. Ha továbbra is megjelennek, frissítse a fentiekben leírtak szerint.

## <a name="discovered-applications-and-sql-server-instances-and-databases-not-in-portal"></a>Felderített alkalmazások és SQL Server példányok és adatbázisok nem a portálon

Miután elindította a felderítést a készüléken, akár 24 óráig is eltarthat, hogy megkezdje a leltár adatainak megjelenítését a portálon.

Ha nem adott meg Windows-hitelesítést vagy SQL Server hitelesítő adatokat a készülék Configuration Managerben, akkor adja hozzá a hitelesítő adatokat, hogy a készülék használhassa őket a megfelelő SQL Server példányokhoz való kapcsolódáshoz.

A csatlakozás után a készülék összegyűjti SQL Server példányok és adatbázisok konfigurációs és teljesítményadatokat. A SQL Server konfigurációs adatai 24 óránként frissülnek, és a teljesítményadatokat 30 másodpercenként rögzíti a rendszer. Így a SQL Server példány és az adatbázisok (például az adatbázis állapota, a kompatibilitási szint stb.) tulajdonságainak módosítása akár 24 órát is igénybe vehet a portálon való frissítéshez.

## <a name="sql-server-instance-is-showing-up-in-not-connected-state-on-portal"></a>SQL Server példány "nincs csatlakoztatva" állapotú a portálon
SQL Server példányok és adatbázisok felderítése során felmerülő problémák megtekintéséhez kattintson a "nincs kapcsolat" állapotra a projekt felderített kiszolgálók lapján található kapcsolat állapota oszlopban.

Az értékelést olyan kiszolgálókon kell létrehozni, amelyek olyan SQL-példányokat tartalmaznak, amelyek nem lettek teljesen felderítve, vagy nem csatlakoztatott állapotban vannak, a készültség "ismeretlen" jelölést eredményezhet.

## <a name="i-do-not-see-performance-data-for-some-network-adapters-on-my-physical-servers"></a>Nem látok teljesítményadatokat a fizikai kiszolgálókon lévő egyes hálózati adapterekhez

Ez akkor fordulhat elő, ha a fizikai kiszolgálón engedélyezve van a Hyper-V-virtualizálás. A termékekkel kapcsolatos hézagok miatt a rendszer rögzíti a hálózati átviteli sebességet a felderített virtuális hálózati adaptereken.

## <a name="error-the-file-uploaded-is-not-in-the-expected-format"></a>Hiba: A feltöltött fájl nem a várt formátumú
Bizonyos eszközök regionális beállításai a CSV-fájl létrehozásakor pontosvesszőt adnak meg elválasztó karakterként. Módosítsa a beállításokat úgy, hogy az elválasztó karakter a vessző legyen.

## <a name="i-imported-a-csv-but-i-see-discovery-is-in-progress"></a>Importáltam egy CSV-fájlt, de a „Felderítés folyamatban” üzenet jelenik meg
Ez az állapot akkor jelenik meg, ha a CSV-feltöltés érvényesítési hiba miatt meghiúsult. Próbálja meg újra importálni a CSV-t. Letöltheti az előző feltöltésre vonatkozó hibajelentést, és követheti a fájlban található útmutatást a hibák kijavításához. A hibajelentés a Gépek felderítése oldal Importálás részletei szakaszából tölthető le.

## <a name="do-not-see-application-details-even-after-updating-guest-credentials"></a>Ne tekintse meg az alkalmazás részleteit még a vendég hitelesítő adatainak frissítése után is
Az alkalmazás-felderítés 24 óránként egyszer fut. Ha azonnal szeretné megtekinteni a részleteket, frissítse az alábbiak szerint. Ez a nem függvénytől függően néhány percet is igénybe vehet. a felderített virtuális gépek.

1. A **kiszolgálók**  >  **Azure Migrate kiszolgáló értékelése** lapon válassza az **Áttekintés** lehetőséget.
2. A **kezelés** területen válassza a **Agent Health** lehetőséget.
3. Válassza az **ügynök frissítése** lehetőséget.
4. Várjon, amíg a frissítési művelet befejeződik. Ekkor megjelenik a naprakész információ.

## <a name="unable-to-export-application-inventory"></a>Nem lehet exportálni az alkalmazás leltárát
Győződjön meg arról, hogy a portálon a leltárt letöltő felhasználó rendelkezik közreműködői jogosultságokkal az előfizetésben.

## <a name="no-suitable-authentication-method-found-to-complete-authentication-publickey"></a>Nem található megfelelő hitelesítési módszer a hitelesítés végrehajtásához (PUBLICKEY)
A kulcs alapú hitelesítés nem fog működni, jelszó-hitelesítés használatával.

## <a name="common-app-discovery-errors"></a>Gyakori alkalmazás-felderítési hibák

Azure Migrate támogatja az alkalmazások, szerepkörök és szolgáltatások felderítését Azure Migrate használatával: Server Assessment. Az alkalmazás-felderítés jelenleg csak VMware esetén támogatott. [További](how-to-discover-applications.md) információ az alkalmazások felderítésének beállításával kapcsolatos követelményekről és lépésekről.

A szokásos alkalmazás-felderítési hibák a táblázatban vannak összegezve. 

| **Hiba** | **Ok** | **Művelet** |
|--|--|--|
| 9000: a VMware-eszköz állapota nem észlelhető. | Előfordulhat, hogy a VMware-eszközök nincsenek telepítve vagy sérültek. | Győződjön meg arról, hogy a VMware-eszközök telepítve vannak és futnak a virtuális gépen. |
| 9001: nincs telepítve a VMware-eszközök. | Előfordulhat, hogy a VMware-eszközök nincsenek telepítve vagy sérültek. | Győződjön meg arról, hogy a VMware-eszközök telepítve vannak és futnak a virtuális gépen. |
| 9002: a VMware-eszközök nem futnak. | Előfordulhat, hogy a VMware-eszközök nincsenek telepítve vagy sérültek. | Győződjön meg arról, hogy a VMware-eszközök telepítve vannak és futnak a virtuális gépen. |
| 9003: a vendég virtuális gép felderítése nem támogatja az operációs rendszer típusát. | A kiszolgálón futó operációs rendszer sem Windows, sem Linux. | A támogatott operációsrendszer-típusok csak Windows és Linux rendszereken érhetők el. Ha a kiszolgáló valóban Windows vagy Linux rendszerű, ellenőrizze a vCenter Serverban megadott operációsrendszer-típust. |
| 9004: a virtuális gép nem fut. | A virtuális gép ki van kapcsolva. | Győződjön meg arról, hogy a virtuális gép be van kapcsolva. |
| 9005: a vendég virtuális gép felderítése nem támogatja az operációs rendszer típusát. | A vendég virtuális gép felderítése nem támogatja az operációs rendszer típusát. | A támogatott operációsrendszer-típusok csak Windows és Linux rendszereken érhetők el. |
| 9006: a metaadat-fájl vendégről való letöltésének URL-címe üres. | Ez akkor fordulhat elő, ha a felderítési ügynök nem a várt módon működik. | A problémának automatikusan fel kell oldania a in24 órát. Ha a probléma továbbra is fennáll, forduljon a Microsoft ügyfélszolgálatahoz. |
| 9007: a felderítési feladat futtatására szolgáló folyamat nem található a vendég virtuális gépen. | Ez akkor fordulhat elő, ha a felderítési ügynök nem működik megfelelően. | A problémát 24 órán belül automatikusan fel kell oldani. Ha a probléma továbbra is fennáll, forduljon a Microsoft ügyfélszolgálatahoz. |
| 9008: a vendég virtuális gép folyamatának állapota nem kérhető le. | A probléma belső hiba miatt fordulhat elő. | A problémát 24 órán belül automatikusan fel kell oldani. Ha a probléma továbbra is fennáll, forduljon a Microsoft ügyfélszolgálatahoz. |
| 9009: a Windows UAC megakadályozta a felderítési feladat végrehajtását a kiszolgálón. | A Windows felhasználói fiókok felügyelete (UAC) beállításai a kiszolgálón korlátozóak, és megakadályozzák a telepített alkalmazások felderítését. | A kiszolgáló "felhasználói fiókok felügyelete" beállításainál konfigurálja az UAC-beállítást úgy, hogy az az alsó két szint egyikének legyen. |
| 9010: a virtuális gép ki van kapcsolva. | A virtuális gép ki van kapcsolva. | Győződjön meg arról, hogy a virtuális gép be van kapcsolva. |
| 9011: a rendszer nem találja a felderített metaadat-fájlt a vendég virtuális fájlrendszerben. | A probléma belső hiba miatt fordulhat elő. | A problémát 24 órán belül automatikusan fel kell oldani. Ha a probléma továbbra is fennáll, forduljon a Microsoft ügyfélszolgálatahoz. |
| 9012: a felderített metaadat-fájl üres. | A probléma belső hiba miatt fordulhat elő. | A problémát 24 órán belül automatikusan fel kell oldani. Ha a probléma továbbra is fennáll, forduljon a Microsoft ügyfélszolgálatahoz. |
| 9013: minden bejelentkezéshez új ideiglenes profil jön létre. | A VMware virtuális géphez minden bejelentkezéshez új ideiglenes profil jön létre. | Megoldásért forduljon Microsoft ügyfélszolgálatahoz. |
| 9014: nem sikerült lekérni a metaadatokat a vendég virtuális fájlrendszerből. | Nincs kapcsolat az ESXi-gazdagéphez | Győződjön meg arról, hogy a készülék csatlakozni tud a 443-es porthoz a virtuális gépet futtató ESXi-gazdagépen |
| 9015: a vendég műveleti szerepkör nincs engedélyezve a vCenter felhasználói fiókján | A vendég műveleti szerepkör nincs engedélyezve a vCenter felhasználói fiókon. | Győződjön meg arról, hogy a vendég műveleti szerepkör engedélyezve van a vCenter felhasználói fiókján. |
| 9016: nem sikerült felderíteni, mert a vendég operatív ügynök elavult. | A VMware-eszközök nincsenek megfelelően telepítve vagy nem naprakészek. | Győződjön meg arról, hogy a VMware-eszközök megfelelően vannak telepítve és naprakészek. |
| 9017: a felderített metaadatokat tartalmazó fájl nem található a virtuális gépen. | A probléma belső hiba miatt fordulhat elő. | Megoldásért forduljon Microsoft ügyfélszolgálatahoz. |
| 9018: a PowerShell nincs telepítve a vendég virtuális gépeken. | A PowerShell nem érhető el a vendég virtuális gépen. | Telepítse a PowerShellt a vendég virtuális gépen. |
| 9019: nem sikerült felderíteni a vendég virtuális gép műveletének hibája miatt. | A VMware Guest művelet sikertelen volt a virtuális gépen. | Győződjön meg arról, hogy a virtuális gép hitelesítő adatai érvényesek, és a vendég virtuális gép hitelesítő adataiban megadott Felhasználónév UPN formátumban van megadva. |
| 9020: a fájl létrehozására vonatkozó engedély megtagadva. | A felhasználóhoz vagy a Csoportházirendhez társított szerepkör korlátozza a felhasználót abban, hogy a fájlt a mappában hozza létre. | Ellenőrizze, hogy a megadott vendég felhasználó rendelkezik-e létrehozási engedéllyel a fájlhoz a mappában. A mappa nevében tekintse meg az **értesítések** a kiszolgáló értékelése című témakört. |
| 9021: nem hozható létre fájl a System Temp elérési úton. | A VMware eszköz a rendszer ideiglenes elérési útját jelenti a felhasználók ideiglenes elérési útja helyett. | Frissítse a VMware-eszköz 10287-es verzióját (NGC/VI ügyfél-formátum). |
| 9022: a WMI-objektumhoz való hozzáférés megtagadva. | A felhasználóhoz vagy a Csoportházirendhez társított szerepkör korlátozza a felhasználót a WMI-objektum elérésére. | Vegye fel a kapcsolatot Microsoft ügyfélszolgálata. |
| 9023: nem lehet futtatni a PowerShellt a SystemRoot környezeti változó értéke üres. | A SystemRoot környezeti változó értéke üres a vendég virtuális gépen. | Megoldásért forduljon Microsoft ügyfélszolgálatahoz. |
| 9024: nem sikerült felderíteni, mert a TEMP környezeti változó értéke üres. | A TEMP környezeti változó értéke üres a vendég virtuális gép esetében. | Vegye fel a kapcsolatot Microsoft ügyfélszolgálata. |
| 9025: a PowerShell sérült a vendég virtuális gépeken. | A PowerShell sérült a vendég virtuális gépen. | Telepítse újra a PowerShellt a vendég virtuális gépen, és ellenőrizze, hogy a PowerShell futtatható-e a vendég virtuális gépen. |
| 9026: nem lehet futtatni a vendég műveleteit a virtuális gépen. | A virtuális gép állapota nem teszi lehetővé, hogy a vendég műveletei a virtuális gépen fussanak. | Megoldásért forduljon Microsoft ügyfélszolgálatahoz. |
| 9027: a vendég műveleti ügynök nem fut a virtuális gépen. | Nem sikerült felvenni a kapcsolatot a virtuális gépen futó vendég műveleti ügynökkel. | Megoldásért forduljon Microsoft ügyfélszolgálatahoz. |
| 9028: a fájl nem hozható létre, mert nincs elég szabad lemezterület a virtuális gépen. | Nincs elég hely a lemezen. | Gondoskodjon arról, hogy elegendő lemezterület álljon rendelkezésre a virtuális gép lemezes tárolásában. |
| 9029: nem érhető el a PowerShell a vendég virtuális gép hitelesítő adatainak megadásakor. | A PowerShellhez való hozzáférés nem érhető el a felhasználó számára. | Győződjön meg arról, hogy a készüléken hozzáadott felhasználó hozzáférhet a PowerShellhez a vendég virtuális gépen. |
| 9030: nem sikerült a felderített metaadatok gyűjtése, mert az ESXi-gazdagép le van választva. | Az ESXi-gazdagép leválasztott állapotban van. | Győződjön meg arról, hogy a virtuális gépet futtató ESXi-gazdagép csatlakoztatva van. |
| 9031: nem sikerült a felderített metaadatok gyűjtése, mert az ESXi-gazdagép nem válaszol. | A távoli gazdagép érvénytelen állapotban van. | Győződjön meg arról, hogy a virtuális gépet futtató ESXi-gazdagép fut és csatlakoztatva van. |
| 9032: egy belső hiba miatt nem sikerült felderíteni. | A probléma belső hiba miatt fordulhat elő. | Megoldásért forduljon Microsoft ügyfélszolgálatahoz. |
| 9033: nem sikerült felderíteni, mert a virtuális gép felhasználóneve érvénytelen karaktereket tartalmaz. | Érvénytelen karakterek észlelhetők a felhasználónévben. | Adja meg a virtuális gép hitelesítő adatait, hogy ne legyenek érvénytelen karakterek. |
| 9034: a megadott Felhasználónév nem UPN-formátumú. | A Felhasználónév nem UPN formátumú. | Győződjön meg arról, hogy a Felhasználónév az egyszerű felhasználónév (UPN) formátumban van. |
| 9035: nem sikerült felderíteni a PowerShell nyelvi üzemmódját, mert nincs "teljes nyelv" beállítás. | A PowerShell nyelvi módja a vendég virtuális gépen nem teljes nyelvre van beállítva. | Győződjön meg arról, hogy a PowerShell nyelvi mód beállítása "teljes nyelv". |
| 9037: az adatgyűjtés ideiglenesen szünetel, mert a virtuális gép válaszideje túl magas. | A felderített virtuális gép túl sokáig tart a válaszadáshoz | Nincs szükség beavatkozásra. Az alkalmazás észlelése 24 órán belül próbálkozik, és a függőségi elemzéshez 3 óra van (ügynök nélkül). |
| 10000: az operációs rendszer típusa nem támogatott. | A kiszolgálón futó operációs rendszer sem Windows, sem Linux. | A támogatott operációsrendszer-típusok csak Windows és Linux rendszereken érhetők el. |
| 10001: a kiszolgáló felderítésére szolgáló parancsfájl nem található a berendezésen. | A felderítés nem a várt módon működik. | Megoldásért forduljon Microsoft ügyfélszolgálatahoz. |
| 10002: a felderítési feladat nem fejeződött be időben. | A felderítési ügynök nem a várt módon működik. | A problémát 24 órán belül automatikusan fel kell oldani. Ha a probléma továbbra is fennáll, forduljon a Microsoft ügyfélszolgálatahoz. |
| 10003: a felderítési feladat végrehajtásának folyamata hiba miatt kilépett. | A felderítési feladat végrehajtásának folyamata hiba miatt kilépett. | A problémát 24 órán belül automatikusan fel kell oldani. Ha a probléma továbbra is fennáll, vegye fel a kapcsolatot Microsoft ügyfélszolgálata. |
| 10004: nincs megadva hitelesítő adat a vendég operációs rendszer típusához. | Az operációs rendszer típusú gépek eléréséhez szükséges hitelesítő adatok nem lettek megadva a Azure Migrate berendezésben. | Hitelesítő adatok hozzáadása a készüléken lévő gépekhez |
| 10005: a megadott hitelesítő adatok nem érvényesek. | A készülékhez a kiszolgáló eléréséhez megadott hitelesítő adatok helytelenek. | Frissítse a berendezésben megadott hitelesítő adatokat, és győződjön meg arról, hogy a kiszolgáló elérhető a hitelesítő adatok használatával. |
| 10006: a vendég operációs rendszer típusa nem támogatott a hitelesítőadat-tárolóban. | A kiszolgálón futó operációs rendszer sem Windows, sem Linux. | A támogatott operációsrendszer-típusok csak Windows és Linux rendszereken érhetők el. |
| 10007: nem sikerült feldolgozni a felderített metaadatokat. | Hiba történt a JSON deszerializálására tett kísérlet során. | Megoldásért forduljon Microsoft ügyfélszolgálatahoz. |
| 10008: nem hozható létre fájl a kiszolgálón. | A probléma belső hiba miatt előfordulhat. | Megoldásért forduljon Microsoft ügyfélszolgálatahoz. |
| 10009: nem lehet felderített metaadatokat írni a-kiszolgálón található fájlba. | A probléma belső hiba miatt fordulhat elő. | Megoldásért forduljon Microsoft ügyfélszolgálatahoz. |

## <a name="next-steps"></a>Következő lépések

Állítson be egy berendezést a [VMware](how-to-set-up-appliance-vmware.md), a [Hyper-V](how-to-set-up-appliance-hyper-v.md)vagy a [fizikai kiszolgálók](how-to-set-up-appliance-physical.md)számára.