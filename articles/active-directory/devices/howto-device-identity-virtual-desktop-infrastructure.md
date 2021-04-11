---
title: Eszköz-identitás és asztali virtualizáció – Azure Active Directory
description: Ismerje meg, hogyan használhatók együtt a VDI és az Azure AD-eszköz identitásai
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
ms.openlocfilehash: cfea22c10d98adf3b8c89491c248bf7a934ba1ed
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104798884"
---
# <a name="device-identity-and-desktop-virtualization"></a>Eszköz-identitás és asztali virtualizálási

A rendszergazdák gyakran telepítik a Windows operációs rendszert üzemeltető virtuális asztali infrastruktúrát (VDI) használó platformokat a szervezetekben. A rendszergazdák a következőre telepítik a VDI-t:

- Egyszerűsítse a felügyeletet.
- Az erőforrások összevonásával és központosításával csökkentheti a költségeket.
- A végfelhasználók mobilitását és a virtuális asztalok elérésének szabadságát bármikor, bárhonnan, bármely eszközről elérheti.

A virtuális asztalok két elsődleges típusa létezik:

- Állandó
- Nem állandó

Az állandó verziók minden felhasználóhoz vagy felhasználói készlethez egyedi asztali rendszerképet használnak. Ezek az egyedi asztalok testreszabhatók és későbbi használatra menthetők. 

A nem állandó verziók olyan asztali számítógépek gyűjteményét használják, amelyekhez a felhasználók igény szerint hozzáférhetnek. Ezek a nem állandó asztalok visszaállnak eredeti állapotukba, ha a Windows current<sup>1</sup> ez akkor történik meg, amikor egy virtuális gép leállítási/újraindítási/operációs rendszer-visszaállítási folyamaton halad át, és a Windows-os Down-Level<sup>2</sup> esetében ez akkor történik meg, amikor egy felhasználó kijelentkezik.

A nem állandó VDI-telepítések egyre növekednek, mivel a távoli munka továbbra is az új norma. Mivel az ügyfelek nem állandó VDI-t telepítenek, fontos, hogy felügyelje az eszköz adatforgalmát, amely a gyakori eszközök regisztrációja miatt okozhatta, anélkül, hogy megfelelő stratégiát kellene megadnia az eszközök életciklusának kezeléséhez.

> [!IMPORTANT]
> Az eszközök adatforgalmának kezelése nem sikerült, ami növelheti a bérlői kvóta használatát és a szolgáltatás megszakadásának lehetséges kockázatát, ha kifogyott a bérlői kvóta. Ha el szeretné kerülni ezt a helyzetet, kövesse az alábbi, a nem állandó VDI-környezetek üzembe helyezése során leírt útmutatást.

Ez a cikk a Microsoft útmutatását ismerteti a rendszergazdáknak az eszköz identitásának és a VDI-nek a támogatásával kapcsolatban. Az eszköz identitásával kapcsolatos további információkért tekintse meg az [eszköz identitását](overview.md)ismertető cikket.

## <a name="supported-scenarios"></a>Támogatott esetek

Mielőtt az eszköz identitásait az Azure AD-ben konfigurálja a VDI-környezethez, ismerkedjen meg a támogatott forgatókönyvekkel. Az alábbi táblázat bemutatja, hogy mely kiépítési forgatókönyvek támogatottak. A kiépítés ebben a kontextusban azt jelenti, hogy a rendszergazdák a teljes felhasználói beavatkozás nélkül konfigurálhatják az eszköz identitásait.

| Eszköz identitásának típusa | Identitás-infrastruktúra | Windows rendszerű eszközök | VDI platform verziója | Támogatott |
| --- | --- | --- | --- | --- |
| csatlakozik a Hibrid Azure AD-hez | Összevont<sup>3</sup> | Windows jelenlegi és Windows Down-Level | Állandó | Yes |
|   |   | Windows jelenlegi | Nem állandó | Igen<sup>5</sup> |
|   |   | Korábbi verziójú Windows | Nem állandó | Igen<sup>6</sup> |
|   | <sup>4</sup> . felügyelt | Windows jelenlegi és Windows Down-Level | Állandó | Yes |
|   |   | Windows jelenlegi | Nem állandó | No |
|   |   | Korábbi verziójú Windows | Nem állandó | Igen<sup>6</sup> |
| Azure AD-hez csatlakoztatva | Összevont | Windows jelenlegi | Állandó | No |
|   |   |   | Nem állandó | No |
|   | Felügyelt | Windows jelenlegi | Állandó | No |
|   |   |   | Nem állandó | No |
| Az Azure AD-ban regisztrálva | Összevont/felügyelt | Windows jelenlegi/Windows lefelé – szint | Állandó/nem állandó | Nem alkalmazható |

<sup>1</sup> a **Windows jelenlegi** eszközei a Windows 10, a Windows Server 2016 v1803 vagy újabb, valamint a Windows Server 2019.
<sup>2</sup> a **Windows leállási szintű** eszközei a Windows 7, a Windows 8,1, a Windows Server 2008 R2, a Windows Server 2012 és a Windows Server 2012 R2 rendszernek felelnek meg. A Windows 7 támogatási információi: a [Windows 7 támogatása véget ér](https://www.microsoft.com/microsoft-365/windows/end-of-windows-7-support). A Windows Server 2008 R2-vel kapcsolatos támogatási információk: [felkészülés a Windows server 2008](https://www.microsoft.com/cloud-platform/windows-server-2008)-re a támogatás befejezése után.

<sup>3</sup> az **összevont** identitás-infrastruktúra környezete egy olyan identitás-szolgáltatót jelöl, mint például a AD FS vagy más, harmadik féltől származó identitásszolgáltató.

<sup>4</sup> A **felügyelt** identitás-infrastruktúra környezete az Azure ad-környezettel rendelkezik, mint a [jelszó-kivonatolási szinkronizálással (PHS)](../hybrid/whatis-phs.md) vagy [átmenő hitelesítéssel (PTA ESP)](../hybrid/how-to-connect-pta.md) telepített identitás-szolgáltató, [zökkenőmentes egyszeri bejelentkezéssel](../hybrid/how-to-connect-sso.md).

<sup>5</sup> nem kitartó **támogatás a Windows jelenlegi** verziójában az útmutató szakaszban leírt módon további szempontokat kell figyelembe venni. Ehhez a forgatókönyvhöz a Windows 10 1803, a Windows Server 2019 vagy a Windows Server (féléves csatorna) verziója szükséges a 1803-as verziótól kezdődően

<sup>6</sup> a **Windows leállási szintjének nem** kitartó támogatásának további megfontolásra van szüksége az útmutató szakaszban leírt módon.


## <a name="microsofts-guidance"></a>A Microsoft útmutatása

A rendszergazdáknak az identitás-infrastruktúrájuk alapján kell megismerniük a következő cikkeket, hogy megtudja, hogyan konfigurálhatja a hibrid Azure AD Joint.

- [Hibrid Azure Active Directory csatlakozás konfigurálása összevont környezethez](hybrid-azuread-join-federated-domains.md)
- [Hibrid Azure Active Directory csatlakozás konfigurálása felügyelt környezethez](hybrid-azuread-join-managed-domains.md)

### <a name="non-persistent-vdi"></a>Nem állandó VDI

Nem állandó VDI telepítésekor a Microsoft javasolja, hogy a rendszergazdák implementálják az alábbi útmutatást. Ennek elmulasztása azt eredményezi, hogy a címtárban sok olyan elavult hibrid Azure AD-csatlakoztatott eszköz található, amely a nem állandó VDI-platformról lett regisztrálva, ami nagyobb terhelést eredményezett a bérlői kvótán, és a szolgáltatás megszakadásának kockázata a bérlői kvóta elfogyása miatt.

- Ha a rendszer-előkészítő eszközre (sysprep.exe) támaszkodik, és ha a telepítés előtt Windows 10 1809 rendszerképet használ, győződjön meg arról, hogy a rendszerkép nem egy olyan eszközről származik, amely már regisztrálva van az Azure AD-ben hibrid Azure AD-hez.
- Ha virtuális gép (VM) pillanatképét szeretné létrehozni további virtuális gépek létrehozásához, győződjön meg arról, hogy a pillanatkép nem olyan virtuális gépről származik, amely már regisztrálva van az Azure AD-ben hibrid Azure AD-csatlakozásként.
- Hozzon létre és használjon egy előtagot annak a számítógépnek a megjelenítendő nevéhez (például NPVDI), amely az asztalt nem állandó VDI-alapúként jelöli meg.
- A Windows leállási szintje:
   - **Autoworkplacejoin/Leave** -parancs implementálása a kijelentkezési parancsfájl részeként. Ezt a parancsot a felhasználó környezetében kell elindítani, és a felhasználó teljes kijelentkezése előtt végre kell hajtani, és még mindig van hálózati kapcsolat.
- Összevont környezetekben (például AD FS) a Windows jelenlegi verziójában:
   - **Dsregcmd-/JOIN** implementálása a virtuális gép rendszerindítási sorrendjének részeként.
   - **Ne hajtsa végre a** dsregcmd/Leave a virtuális gép leállítása/újraindítási folyamata részeként.
- Az [elavult eszközök kezelésére](manage-stale-devices.md)szolgáló folyamat meghatározása és implementálása.
   - Ha már van egy stratégia a nem állandó hibrid Azure AD-hez csatlakoztatott eszközök azonosításához (például a számítógép megjelenített nevének előtagjának használatával), agresszíven kell lennie ezeknek az eszközöknek a tisztításához, hogy a címtár ne legyen felhasználva sok elavult eszközzel.
   - A Windows aktuális és alacsonyabb szintű, nem állandó VDI-telepítések esetén olyan eszközöket kell törölni, amelyeknek 15 napnál régebbi **ApproximateLastLogonTimestamp** van.

### <a name="persistent-vdi"></a>Állandó VDI

Állandó VDI telepítésekor a Microsoft javasolja, hogy a rendszergazdák implementálják az alábbi útmutatást. Ha ezt nem teszi meg, az üzembe helyezési és hitelesítési problémákhoz vezethet. 

- Ha a rendszer-előkészítő eszközre (sysprep.exe) támaszkodik, és ha a telepítés előtt Windows 10 1809 rendszerképet használ, győződjön meg arról, hogy a rendszerkép nem egy olyan eszközről származik, amely már regisztrálva van az Azure AD-ben hibrid Azure AD-hez.
- Ha virtuális gép (VM) pillanatképét szeretné létrehozni további virtuális gépek létrehozásához, győződjön meg arról, hogy a pillanatkép nem olyan virtuális gépről származik, amely már regisztrálva van az Azure AD-ben hibrid Azure AD-csatlakozásként.

Emellett azt javasoljuk, hogy az [elavult eszközök felügyeletének](manage-stale-devices.md)folyamatát is implementálja. Ezzel biztosíthatja, hogy a címtára ne legyen felhasználva sok elavult eszközzel, ha rendszeresen alaphelyzetbe állítja a virtuális gépeket.
 
## <a name="next-steps"></a>Következő lépések

[Hibrid Azure Active Directory illesztés konfigurálása összevont környezethez](hybrid-azuread-join-federated-domains.md)
