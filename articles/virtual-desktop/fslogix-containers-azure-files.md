---
title: Windows rendszerű virtuális asztali FSLogix-profilok fájljai – Azure
description: Ez a cikk a Windows rendszerű virtuális asztali és az Azure Files FSLogix-profilok tárolóit ismerteti.
author: Heidilohr
ms.topic: conceptual
ms.date: 01/04/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 945bf806a813943d02fb4db1245dfb74eb7fb1f9
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2021
ms.locfileid: "106446893"
---
# <a name="fslogix-profile-containers-and-azure-files"></a>FSLogix-profiltárolók és Azure Files

A Windows Virtual Desktop szolgáltatás felhasználói Profilos megoldásként ajánlja a FSLogix-profilok tárolóit. A FSLogix a távoli számítástechnikai környezetekben, például a Windows Virtual Desktopban található profilok hordozására szolgál. Egy teljes felhasználói profilt tárol egy önálló tárolóban. Bejelentkezéskor a tároló dinamikusan csatlakozik a számítástechnikai környezethez a natív módon támogatott virtuális merevlemez (VHD) és a Hyper-V virtuális merevlemez (VHDX) használatával. A felhasználói profil azonnal elérhető, és pontosan ugyanúgy jelenik meg a rendszeren, mint a natív felhasználói profilok. Ez a cikk azt ismerteti, hogyan használhatók a FSLogix-profilok tárolói a Windows rendszerű virtuális asztal Azure Files funkciójával.

>[!NOTE]
>Ha összehasonlító anyagot keres az Azure-beli különböző FSLogix-profilok tárolási lehetőségeivel kapcsolatban, tekintse meg a [FSLogix-profilok tárolási lehetőségei](store-fslogix-profile.md)című témakört.

## <a name="user-profiles"></a>Felhasználói profilok

A felhasználói profil egy egyedi adatelemet tartalmaz, beleértve a konfigurációs adatokat, például az asztali beállításokat, az állandó hálózati kapcsolatokat és az alkalmazás beállításait. Alapértelmezés szerint a Windows egy helyi felhasználói profilt hoz létre, amely szorosan integrálódik az operációs rendszerbe.

A távoli felhasználói profil egy partíciót biztosít a felhasználói és az operációs rendszer között. Lehetővé teszi az operációs rendszer lecserélését vagy módosítását anélkül, hogy ez hatással lenne a felhasználói értékekre. Távoli asztal munkamenet-gazdagép (RDSH) és a virtuális asztali infrastruktúrák (VDI) esetében az operációs rendszer a következő okok miatt váltható fel:

- Az operációs rendszer frissítése
- Meglévő virtuális gép (VM) cseréje
- Egy felhasználó egy készletezett (nem állandó) RDSH vagy VDI-környezet részét képezi

A Microsoft-termékek számos technológiával működnek a távoli felhasználói profilokhoz, beleértve a következő technológiákat:
- Barangoló felhasználói profilok (központi felhasználói PROFILHOZ)
- Felhasználói profil lemezei (UPD)
- Vállalati állapot barangolása (ESR)

A UPD és a központi felhasználói PROFILHOZ a legszélesebb körben használt technológiák a Távoli asztal munkamenet-gazdagépen (RDSH) és a virtuális merevlemezen (VHD) lévő felhasználói profilokhoz.

### <a name="challenges-with-previous-user-profile-technologies"></a>A korábbi felhasználói profil technológiáinak kihívásai

A meglévő és a régi Microsoft-megoldások a felhasználói profilokhoz különböző kihívásokkal jártak. Egyetlen korábbi megoldás sem kezelte az összes olyan felhasználói profilt, amely RDSH vagy VDI-környezettel rendelkezik. Például a UPD nem tudja kezelni a nagyméretű OST-fájlokat, és a központi felhasználói PROFILHOZ nem őrzi meg a modern beállításokat.

#### <a name="functionality"></a>Funkció

Az alábbi táblázat a korábbi felhasználói profilok technológiáinak előnyeit és korlátozásait mutatja be.

| Technológia | Modern beállítások | Win32-beállítások | Operációs rendszer beállításai | Felhasználói adatok | Kiszolgáló SKU-ban támogatott | Háttérbeli tároló az Azure-ban | Helyszíni háttérbeli tároló | Verziók támogatása | Későbbi bejelentkezési idő |Jegyzetek|
| ---------- | :-------------: | :------------: | :---------: | --------: | :---------------------: | :-----------------------: | :--------------------------: | :-------------: | :---------------------: |-----|
| **Felhasználói profil lemezei (UPD)** | Igen | Igen | Igen | Igen | Igen | Nem | Igen | 7. győzelem + | Igen | |
| **Barangoló felhasználói profil (központi felhasználói PROFILHOZ), karbantartási mód** | Nem | Igen | Igen | Igen | Igen| Nem | Igen | 7. győzelem + | Nem | |
| **Enterprise State Roaming (ESR)** | Igen | Nem | Igen | Nem | Lásd: megjegyzések | Igen | Nem | 10. győzelem | Nem | A Server SKU-ban lévő függvények, de nem támogatott felhasználói felület |
| **Felhasználói élmény virtualizálása (UE-V)** | Igen | Igen | Igen | Nem | Igen | Nem | Igen | 7. győzelem + | Nem |  |
| **OneDrive Cloud Files** | Nem | Nem | Nem | Igen | Lásd: megjegyzések | Lásd: megjegyzések  | Lásd: megjegyzések | 10 RS3 | Nem | Nem tesztelt a kiszolgálói SKU-ban. Az Azure-beli háttérbeli tárterület a szinkronizálási ügyféltől függ. Az on-back-end tárolónak szinkronizálási ügyfélre van szüksége. |

#### <a name="performance"></a>Teljesítmény

A UPD a teljesítményre vonatkozó követelmények kielégítéséhez [közvetlen tárolóhelyek (S2D)](/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment/) szükséges. A UPD Server Message Block (SMB) protokollt használ. Átmásolja a profilt arra a virtuális gépre, amelyben a felhasználó naplózva van.

#### <a name="cost"></a>Költségek

Bár a S2D-fürtök a szükséges teljesítményt érik el, a nagyvállalati ügyfelek számára költséges költség, különösen a kis-és közepes méretű vállalkozások (SMB) ügyfelei számára. Ebben a megoldásban a vállalatok a Storage-lemezekért fizetnek, valamint a megosztások lemezeit használó virtuális gépek költségeit.

#### <a name="administrative-overhead"></a>Nagyobb adminisztrációs terhelés

A S2D-fürtök olyan operációs rendszert igényelnek, amely egy javított, frissített és karbantartott, biztonságos állapotban van. Ezek a folyamatok és a S2D vész-helyreállítás beállításának összetettsége csak a dedikált informatikai személyzettel rendelkező vállalatok számára teszi elérhetővé a S2D.

## <a name="fslogix-profile-containers"></a>FSLogix-profilok tárolói

2018. november 19-én a [Microsoft megszerezte a FSLogix](https://blogs.microsoft.com/blog/2018/11/19/microsoft-acquires-fslogix-to-enhance-the-office-365-virtualization-experience/). A FSLogix számos profillal kapcsolatos kihívást jelent. A kulcsok közül a következők:

- **Teljesítmény:** A [FSLogix-profil tárolói](/fslogix/configure-profile-container-tutorial/) nagy teljesítményű és olyan teljesítménnyel kapcsolatos problémák megoldására szolgálnak, amelyek történelmileg letiltották a gyorsítótáras Exchange üzemmódot.
- **OneDrive:** FSLogix-profilok nélkül a OneDrive for Business nem állandó RDSH-vagy VDI-környezetekben nem támogatott. A [ONEDRIVE VDI-támogatás lapján](/onedrive/sync-vdi-support) megtudhatja, hogyan hatnak egymásra. További információ: [a szinkronizálási ügyfél használata a virtuális asztalokon](/deployoffice/rds-onedrive-business-vdi/).
- **További mappák:** A FSLogix lehetővé teszi a felhasználói profilok kiterjesztését további mappák befoglalására.

A beszerzés óta a Microsoft megkezdte a meglévő felhasználói profil-megoldások, például a UPD FSLogix-profilok tárolására való lecserélését.

## <a name="azure-files-integration-with-azure-active-directory-domain-service"></a>Azure Files integráció Azure Active Directory tartományi szolgáltatással

A FSLogix-profil tárolóinak teljesítménye és funkciói kihasználják a felhő előnyeit. 2019. augusztus 7-én Microsoft Azure fájlok Azure Files hitelesítés általános elérhetőségét jelentették be [Azure Active Directory tartományi szolgáltatással (AD DS)](../storage/files/storage-files-active-directory-overview.md). A költségek és az adminisztrációs terhelések kezelésével az Azure AD DS-hitelesítéssel Azure Files a Windows virtuális asztali szolgáltatás felhasználói profiljainak prémium szintű megoldása.

## <a name="best-practices-for-windows-virtual-desktop"></a>Ajánlott eljárások a Windows rendszerű virtuális asztali gépekhez

A Windows rendszerű virtuális asztali szolgáltatás teljes körű vezérlést biztosít az ügyfelek által igénybe vett virtuális gépek méretének, típusának és számának. További információ: [Mi a Windows Virtual Desktop?](overview.md).

Annak biztosítása érdekében, hogy a Windows rendszerű virtuális asztali környezet az ajánlott eljárásokat követi:

- Azure Files Storage-fióknak ugyanabban a régióban kell lennie, mint a munkamenet-gazdagép virtuális gépei.
- Azure Files engedélyeknek meg kell egyezniük a [követelmények – profil tárolók](/fslogix/fslogix-storage-config-ht)című cikkben ismertetett engedélyekkel.
- Minden egyes gazdagép-készlet virtuális gépnek azonos típusú és méretű virtuális gépről kell kiépíteni ugyanazon fő lemezkép alapján.
- Minden egyes gazdagép-készlet virtuális gépnek ugyanabban az erőforráscsoporthoz kell tartoznia, hogy segítse a felügyeletet, a skálázást és a frissítést.
- Az optimális teljesítmény érdekében a tárolási megoldásnak és a FSLogix-profil tárolójának ugyanabban az adatközpont-helyen kell lennie.
- A fő lemezképet tartalmazó Storage-fióknak ugyanabban a régióban és előfizetésben kell lennie, ahol a virtuális gépek üzembe lettek kiépítve.

## <a name="next-steps"></a>Következő lépések

A következő útmutatók segítségével állíthatja be a Windows rendszerű virtuális asztali környezetet.

- Az asztali virtualizálási megoldás létrehozásának megkezdéséhez lásd: [bérlő létrehozása a Windows rendszerű virtuális asztalon](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md).
- A Windows rendszerű virtuális asztali bérlőn belüli gazdagépek létrehozásával kapcsolatban lásd: [állomáslista létrehozása az Azure Marketplace-szel](create-host-pools-azure-marketplace.md).
- A teljes körűen felügyelt fájlmegosztás felhőben való beállításával kapcsolatban lásd: [Azure Files megosztás beállítása](/azure/storage/files/storage-files-active-directory-enable/).
- A FSLogix-profilok tárolók konfigurálásáról további információt a [fájlmegosztás létrehozása a címkészlet számára](create-host-pools-user-profile.md)című témakörben talál.
- A felhasználók egy gazdagéphez való hozzárendelésével kapcsolatban lásd: [Manage app groups for Windows Virtual Desktop](manage-app-groups.md).
- A Windows rendszerű virtuális asztali erőforrások webböngészőből való eléréséhez tekintse meg a [Kapcsolódás a Windows rendszerű virtuális asztalhoz](connect-web.md)című témakört.
