---
title: Eszközidentitás és asztali virtualizálás – Azure Active Directory
description: Megtudhatja, hogyan használhatók együtt a VDI- és az Azure AD-eszköz identitások
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 09/14/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6c1d78094effe6919587f24c2262612e4fab347d
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107575377"
---
# <a name="device-identity-and-desktop-virtualization"></a>Eszközidentitás és asztali virtualizálás

A rendszergazdák gyakran telepítik a windowsos operációs rendszereket üzemeltető virtuális asztali infrastruktúrát (VDI) a szervezetben. A rendszergazdák a következőre telepítik a VDI-t:

- A felügyelet leegyszerűsített kezelése.
- Költségek csökkentése az erőforrások konszolidálása és központosítása révén.
- Biztosíthatja a végfelhasználók mobilitását és a virtuális asztalok elérésének szabadságát bármikor, bárhonnan, bármilyen eszközön.

A virtuális asztalok két elsődleges típusa létezik:

- Állandó
- Nem állandó

Az állandó verziók minden felhasználóhoz vagy felhasználói készlethez egyedi asztali rendszerképet használnak. Ezek az egyedi asztalok testreszabhatók és menthetők későbbi használatra. 

A nem állandó verziók olyan asztalok gyűjteményét használják, amelyekhez a felhasználók szükség szerint hozzáférhetnek. Ezek a nem állandó asztalok visszaállnak az eredeti állapotukra, ha a Windows<sup>jelenlegi 1-es</sup> verziója leállítási/újraindítási/operációsrendszer-visszaállítási folyamaton megy keresztül, és a<sup>2.</sup> szintű Windows esetén ez akkor fordul elő, ha egy felhasználó jelentkezik ki.

A nem állandó VDI-környezetek egyre növekednek, mivel továbbra is a távoli munka az új norma. Mivel az ügyfelek nem perzisztens VDI-t helyeznek üzembe, fontos gondoskodni arról, hogy ön felügyelje az eszközváltozást, amelyet a gyakori eszközregisztráció okozhat, és nincs megfelelő stratégia az eszközök életciklusának kezeléséhez.

> [!IMPORTANT]
> Ha nem sikerül kezelni az eszközváltozást, az a bérlői kvóta használatának nyomásának növekedéséhez és a szolgáltatáskimaradás lehetséges kockázathoz vezethet, ha elfogy a bérlői kvóta. Ennek elkerülése érdekében a nem állandó VDI-környezetek üzembe helyezésekor kövesse az alábbi útmutatást.

Ez a cikk a Rendszergazdáknak az eszközidentitás és a VDI támogatásához nyújt útmutatást a Microsoftnak. További információ az eszközidentitásról: [Mi az eszközidentitás?](overview.md)

## <a name="supported-scenarios"></a>Támogatott esetek

Mielőtt konfigurálja az eszköz identitását az Azure AD-ban a VDI-környezethez, ismerkedjen meg a támogatott forgatókönyvekkel. Az alábbi táblázat bemutatja, mely kiépítési forgatókönyvek támogatottak. Ebben a környezetben a kiépítés azt jelenti, hogy a rendszergazda nagy méretekben, a végfelhasználói beavatkozás nélkül konfigurálhatja az eszköz identitását.

| Eszközidentitás típusa | Identitás-infrastruktúra | Windows rendszerű eszközök | VDI-platform verziója | Támogatott |
| --- | --- | --- | --- | --- |
| csatlakozik a Hibrid Azure AD-hez | <sup>3.</sup> összevont | A Jelenlegi Windows és a Windows rendszer | Állandó | Yes |
|   |   | Aktuális Windows | Nem állandó | Igen<sup>5</sup> |
|   |   | Korábbi verziójú Windows | Nem állandó | Igen<sup>6</sup> |
|   | Felügyelt<sup>4</sup> | A Jelenlegi Windows és a Windows down-level | Állandó | Yes |
|   |   | Aktuális Windows | Nem állandó | No |
|   |   | Korábbi verziójú Windows | Nem állandó | Igen<sup>6</sup> |
| Azure AD-hez csatlakoztatva | Összevont | Aktuális Windows | Állandó | No |
|   |   |   | Nem állandó | No |
|   | Felügyelt | Aktuális Windows | Állandó | No |
|   |   |   | Nem állandó | No |
| Az Azure AD-ban regisztrálva | Összevont/Felügyelt | Aktuális Windows/Windows, lefelé | Állandó/nem állandó | Nem alkalmazható |

<sup>1</sup> **A jelenlegi Windows-eszközök** a Windows 10, a Windows Server 2016 v1803 vagy újabb, valamint a Windows Server 2019 rendszert képviselik.
<sup>2</sup> A **windowsos** eszközök a Windows 7, Windows 8.1, Windows Server 2008 R2, Windows Server 2012 és Windows Server 2012 R2 rendszereket képviselik. A Windows 7-re vonatkozó támogatási információkért lásd: [A Windows 7 támogatása véget ér.](https://www.microsoft.com/microsoft-365/windows/end-of-windows-7-support) A Windows Server 2008 R2 rendszerrel kapcsolatos támogatási információkért lásd: [Prepare for Windows Server 2008 end of support (Felkészülés a Windows Server 2008 2008 támogatásának végét)](https://www.microsoft.com/cloud-platform/windows-server-2008)

<sup>3</sup> Az **összevont identitás-infrastruktúra-környezetek** identitásszolgáltatóval (például identitásszolgáltatóval AD FS vagy más külső identitásszolgáltatóval rendelkező környezetet képviselnek.

<sup>4</sup> **A** felügyelt identitás-infrastruktúra környezete olyan környezetet jelent, amely az Azure AD-t használja jelszó-kivonatszinkronizálással [(PHS)](../hybrid/whatis-phs.md) vagy átmenő hitelesítéssel [(PTA)](../hybrid/how-to-connect-pta.md) üzembe helyezett identitásszolgáltatóként, zökkenőmentes egyszeri [bejelentkezéssel.](../hybrid/how-to-connect-sso.md)

<sup>5 A</sup> **Windows jelenlegi nem adatmegőrzési** támogatása további megfontolást igényel, amint azt az útmutató szakaszban alább olvashatja. Ehhez a forgatókönyvhöz Windows 10 1803-as, a Windows Server 2019-es vagy a Windows Server (féléves csatorna) 1803-as verziójára van szükség

<sup>6</sup> **A Windows nem állandósítási** támogatása további megfontolást igényel, amint azt az útmutató szakaszban alább is olvashatja.


## <a name="microsofts-guidance"></a>A Microsoft útmutatója

A rendszergazdáknak az identitás-infrastruktúrájuk alapján az alábbi cikkekre kell hivatkoznia, amelyekből megtudhatja, hogyan konfigurálhatja a hibrid Azure AD-csatlakozást.

- [Hibrid hálózati Azure Active Directory konfigurálása összevont környezethez](hybrid-azuread-join-federated-domains.md)
- [Hibrid hálózati Azure Active Directory konfigurálása felügyelt környezethez](hybrid-azuread-join-managed-domains.md)

### <a name="non-persistent-vdi"></a>Nem állandó VDI

A nem állandó VDI telepítésekor a Microsoft azt javasolja, hogy a rendszergazdák valósítják meg az alábbi útmutatást. Ennek elmulasztása azt eredményezi, hogy a címtárban sok elavult hibrid Azure AD-hez csatlakozott eszköz van regisztrálva a nem állandó VDI-platformról, ami a bérlői kvótára nehezedő nyomás növekedéséhez és a szolgáltatás megszakadásának kockázatához vezet a bérlői kvóta elajánlása miatt.

- Ha a rendszer-előkészítési eszközre (sysprep.exe) támaszkodik, és az Windows 10 1809-es előtti rendszerképet használja a telepítéshez, győződjön meg arról, hogy a rendszerkép nem olyan eszközről van-e, amely már regisztrálva van az Azure AD-ben hibrid Azure AD-hez csatlakozva.
- Ha virtuálisgép-pillanatképre támaszkodik további virtuális gépek létrehozásához, győződjön meg arról, hogy a pillanatkép nem olyan virtuális gépről készült, amely már regisztrálva van az Azure AD-ban hibrid Azure AD-csatlakozásként.
- Hozzon létre és használjon előtagot a számítógép megjelenítendő nevéhez (pl. NPVDI), amely nem állandó VDI-alapúként jelöli az asztalt.
- A windowsos verziók esetén:
   - Implementálja **az autoworkplacejoin /leave parancsot** az logoff szkript részeként. Ezt a parancsot a felhasználó környezetében kell aktiválni, és még azelőtt kell végrehajtani, hogy a felhasználó teljesen kijelentkezett volna, és amíg továbbra is van hálózati kapcsolat.
- Összevont környezetben (például összevont környezetben) aktuális Windows AD FS:
   - Implementálja **a dsregcmd /join** parancsot a virtuális gép rendszerindítási sorozatának/sorrendjének részeként, és mielőtt a felhasználó bejelentkezne.
   - **NE hajtsa** végre a dsregcmd /leave kódot a virtuális gép leállítási/újraindítási folyamatának részeként.
- Definiálja és implementálja az [elavult eszközök kezelésének folyamatát.](manage-stale-devices.md)
   - Ha már van stratégiája a nem állandó hibrid Azure AD-hez csatlakozott eszközök azonosítására (például a számítógép megjelenítendő nevének előtagját használva), agresszívabbnak kell lennie ezen eszközök tisztításán, hogy a címtár ne legyen felhasználva sok elavult eszközzel.
   - A nem állandó VDI-környezetek esetében a jelenlegi és régebbi Windows-környezetek esetében törölnie kell azokat az eszközöket, amelyeken a **ApproximateLastLogonTimestamp** értéke 15 napnál régebbi.

> [!NOTE]
> Nem állandó VDI használata esetén, ha meg szeretné akadályozni az eszköz beléptetését, győződjön meg arról, hogy a következő beállításkulcs van beállítva:  
> `HKLM\SOFTWARE\Policies\Microsoft\Windows\WorkplaceJoin: "BlockAADWorkplaceJoin"=dword:00000001`    
>
> Győződjön meg arról, hogy Windows 10 1803-as vagy újabb verziót futtat.  
>
> Az elérési úton található adatok barangolása `%localappdata%` nem támogatott. Ha úgy dönt, hogy áthelyezi a tartalmat a(z) alatt, győződjön meg arról, hogy az alábbi mappák és beállításkulcsok tartalma soha nem hagyja el `%localappdata%` az eszközt semmilyen körülmények között.  Például: A profiláttelepítési eszközöknek ki kell hagyniuk a következő mappákat és kulcsokat:
>
> * `%localappdata%\Packages\Microsoft.AAD.BrokerPlugin_cw5n1h2txyewy`
> * `%localappdata%\Packages\Microsoft.Windows.CloudExperienceHost_cw5n1h2txyewy`
> * `%localappdata%\Packages\<any app package>\AC\TokenBroker`
> * `%localappdata%\Microsoft\TokenBroker`
> * `HKEY_CURRENT_USER\SOFTWARE\Microsoft\IdentityCRL`
> * `HKEY_CURRENT_USER\SOFTWARE\Microsoft\Windows\CurrentVersion\AAD`
>


### <a name="persistent-vdi"></a>Állandó VDI

Állandó VDI telepítésekor a Microsoft azt javasolja, hogy a rendszergazdák valósítják meg az alábbi útmutatást. Ennek elmulasztása üzembe helyezési és hitelesítési problémákhoz vezet. 

- Ha a rendszer-előkészítési eszközre (sysprep.exe) támaszkodik, és egy 1809 Windows 10-es előtti rendszerképet használ a telepítéshez, győződjön meg arról, hogy a rendszerkép nem olyan eszközről van-e, amely már regisztrálva van az Azure AD-ben hibrid Azure AD-hez csatlakozva.
- Ha virtuálisgép-pillanatképre támaszkodik további virtuális gépek létrehozásához, győződjön meg arról, hogy a pillanatkép nem olyan virtuális gépről készült, amely már regisztrálva van az Azure AD-ban hibrid Azure AD-csatlakozásként.

Emellett azt is javasoljuk, hogy implementálja az elavult [eszközök kezelésének folyamatát.](manage-stale-devices.md) Így biztosítható, hogy a címtár ne legyen felhasználva sok elavult eszközzel, ha rendszeres időközönként alaphelyzetbe állítja a virtuális gépeket.
 
## <a name="next-steps"></a>Következő lépések

[Hibrid hálózati Azure Active Directory konfigurálása összevont környezethez](hybrid-azuread-join-federated-domains.md)
