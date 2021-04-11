---
title: Eszközök kezelése a Azure Portal használatával | Microsoft Docs
description: Ismerje meg, hogyan kezelheti az eszközöket a Azure Portal használatával.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 03/23/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: hafowler
ms.collection: M365-identity-device-management
ms.openlocfilehash: 11182b8331f218b970d867764f575ba5b7854d62
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "106550693"
---
# <a name="manage-device-identities-using-the-azure-portal"></a>Eszközidentitások kezelése az Azure Portal használatával

Az Azure AD központi helyet biztosít az eszközök identitásának kezeléséhez.

A **minden eszköz** oldal a következőket teszi lehetővé:

- Eszközök azonosítása, beleértve a következőket:
   - Az Azure AD-ben csatlakozott vagy regisztrált eszközök.
   - A [Windows Autopilot](/windows/deployment/windows-autopilot/windows-autopilot)használatával üzembe helyezett eszközök.
   - [Univerzális nyomtatást](/universal-print/fundamentals/universal-print-getting-started) használó nyomtatók
- Az eszköz identitás-felügyeleti feladatait (például engedélyezés, letiltás, törlés vagy kezelés) hajthatja végre.
   - A [nyomtatók](/universal-print/fundamentals/) és a [Windows Autopilot](/windows/deployment/windows-autopilot/windows-autopilot) -eszközök korlátozott felügyeleti lehetőségekkel rendelkeznek az Azure ad-ben. A rendszergazdákat a megfelelő felügyeleti felületéről kell felügyelni.
- Adja meg az eszköz identitásának beállításait.
- Enterprise State Roaming engedélyezése vagy letiltása.
- Az eszközhöz kapcsolódó naplók áttekintése
- Eszközök letöltése (előzetes verzió)

[![A Azure Portal összes eszköz nézete](./media/device-management-azure-portal/all-devices-azure-portal.png)](./media/device-management-azure-portal/all-devices-azure-portal.png#lightbox)

Az eszközök portál a következő lépésekkel érhető el:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Tallózással keresse meg **Azure Active Directory**  >  **eszközöket**.

## <a name="manage-devices"></a>Eszközök kezelése

Az Azure AD-ben két helyen kezelheti az eszközöket:

- **Azure Portal**  >  **Azure Active Directory**  >  **Eszközök**
- **Azure Portal**  >  **Azure Active Directory**  >  **Felhasználók** > válasszon ki egy felhasználói > **eszközt**

Mindkét beállítás lehetővé teszi, hogy a rendszergazdák a következőket tudják:

- Eszközök keresése.
- Tekintse meg az eszköz adatait, beleértve a következőket:
    - Eszköz neve
    - Eszközazonosító
    - Operációs rendszer és verzió
    - Illesztés típusa
    - Tulajdonos
    - Mobileszköz-kezelés és-megfelelőség
    - BitLocker helyreállítási kulcs
- Eszközállapot-kezelési feladatok (például, engedélyezés, letiltás, törlés vagy kezelés) végrehajtása.
   - A [nyomtatók](/universal-print/fundamentals/) és a [Windows Autopilot](/windows/deployment/windows-autopilot/windows-autopilot) -eszközök korlátozott felügyeleti lehetőségekkel rendelkeznek az Azure ad-ben. A rendszergazdákat a megfelelő felügyeleti felületéről kell felügyelni.

> [!TIP]
> - A hibrid Azure AD-hez csatlakoztatott Windows 10-es eszközökhöz nem tartozik tulajdonos. Ha a tulajdonostól származó eszközt keres, és nem találta meg, akkor keressen rá az eszköz azonosítójával.
>
> - Ha megjelenik egy "hibrid Azure AD-hez csatlakoztatott" állapotú eszköz, amely a regisztrált oszlopban a "függőben" állapotban van, az azt jelzi, hogy az eszköz szinkronizálva lett az Azure AD-kapcsolattal, és az ügyféltől való regisztráció befejezésére vár. További információ a [hibrid Azure ad-csatlakozás megvalósításának megtervezéséről](hybrid-azuread-join-plan.md). További információt a cikkben, az [eszközök gyakori kérdéseiben](faq.yml)találhat.
>
> - Egyes iOS-eszközök esetében az aposztrófokat tartalmazó eszközök nevei különböző karaktereket használhatnak, amelyek hasonlóak az aposztrófokhoz. Így az ilyen eszközök keresése kicsit trükkös – ha nem látja megfelelően a keresési eredményeket, ügyeljen arra, hogy a keresési karakterlánc a megfelelő aposztróf-karaktert tartalmazza.

### <a name="manage-an-intune-device"></a>Intune-eszköz kezelése

Ha Ön Intune-rendszergazda, akkor felügyelheti azokat az eszközöket, amelyeken a MDM jelölése **Microsoft Intune**. Ha az eszköz nincs regisztrálva Microsoft Intune, a "kezelés" beállítás szürkén jelenik meg.

### <a name="enable-or-disable-an-azure-ad-device"></a>Azure AD-eszköz engedélyezése vagy letiltása

Az eszközök engedélyezéséhez vagy letiltásához két lehetőség közül választhat:

- Egy vagy több eszköz kijelölése után a **minden eszköz** lapon lévő eszköztár.
- Az eszköztár egy adott eszközre történő lefúrása után.

> [!IMPORTANT]
> - Az eszközök engedélyezéséhez vagy letiltásához globális rendszergazdai vagy felhőalapú eszköz-rendszergazdának kell lennie az Azure AD-ben. 
> - Az eszköz letiltásával megakadályozható, hogy az eszköz sikeresen hitelesítse az Azure AD-t, így megakadályozva, hogy az eszköz hozzáférjen az eszköz alapú feltételes hozzáférés által védett Azure AD-erőforrásokhoz, vagy a vállalati Windows Hello hitelesítő adataihoz.
> - Az eszköz letiltásával az eszközön az elsődleges frissítési jogkivonat (PRT) és a frissítési tokenek (RT) is visszavonhatók.
> - A nyomtatók nem engedélyezhetők és nem tilthatók le az Azure AD-ben.

### <a name="delete-an-azure-ad-device"></a>Azure AD-eszköz törlése

Egy eszköz törléséhez két lehetőség közül választhat:

- Egy vagy több eszköz kijelölése után a **minden eszköz** lapon lévő eszköztár.
- Az eszköztár egy adott eszközre történő lefúrása után.

> [!IMPORTANT]
> - Az eszköz törléséhez hozzá kell rendelnie a felhőalapú eszköz rendszergazdája, az Intune-rendszergazda vagy a globális rendszergazdai szerepkört az Azure AD-ben.
> - A nyomtatók és a Windows Autopilot-eszközök nem törölhetők az Azure AD-ben
> - Eszköz törlése:
>    - Megakadályozza, hogy az eszköz hozzáférjen az Azure AD-erőforrásokhoz.
>    - Eltávolítja az eszközhöz csatolt összes adatot, például a BitLocker-kulcsokat a Windows-eszközökhöz.  
>    - Nem helyreállítható tevékenységet jelöl, és nem ajánlott, ha szükséges.

Ha egy eszközt egy másik felügyeleti hatóság felügyel (például Microsoft Intune), győződjön meg róla, hogy az eszköz törölve lett vagy ki lett vonva, mielőtt törölné az eszközt az Azure AD-ben. Tekintse át, hogyan [kezelheti az elavult eszközöket](manage-stale-devices.md) az eszközök törlése előtt.

### <a name="view-or-copy-device-id"></a>Eszköz AZONOSÍTÓjának megtekintése vagy másolása

Az eszköz AZONOSÍTÓjának használatával ellenőrizheti az eszköz AZONOSÍTÓjának adatait, vagy a PowerShell használatával végezheti el a hibaelhárítást. A másolási lehetőség eléréséhez kattintson az eszközre.

![Eszköz AZONOSÍTÓjának megtekintése](./media/device-management-azure-portal/35.png)
  
### <a name="view-or-copy-bitlocker-keys"></a>BitLocker-kulcsok megtekintése vagy másolása

Megtekintheti és átmásolhatja a BitLocker-kulcsokat, hogy a felhasználók helyre tudják állítani a titkosított meghajtókat. Ezek a kulcsok csak a titkosított Windows-eszközökön érhetők el, és a kulcsaikat az Azure AD-ben tárolják. Ezek a kulcsok a **helyreállítási kulcs megjelenítése** lehetőség kiválasztásával érhetik el az eszközök adatait. A **helyreállítási kulcs megjelenítése** lehetőség kiválasztásával a rendszer egy naplót hoz majd, amely a `KeyManagement` kategóriában található.

![BitLocker-kulcsok megtekintése](./media/device-management-azure-portal/device-details-show-bitlocker-key.png)

A BitLocker-kulcsok megtekintéséhez vagy másolásához az eszköz tulajdonosának kell lennie, vagy egy olyan felhasználónak, amely legalább az alábbi szerepkörök valamelyikével rendelkezik:

- Felhőalapú eszköz rendszergazdája
- Globális rendszergazda
- Segélyszolgálat rendszergazdája
- Intune szolgáltatás rendszergazdája
- Biztonsági rendszergazda
- Biztonsági olvasó

### <a name="device-list-filtering-preview"></a>Eszközök listájának szűrése (előzetes verzió)

Korábban csak tevékenység és engedélyezett állapot alapján szűrheti az eszközök listáját. Ez az előzetes verzió lehetővé teszi az eszközök listájának szűrését az eszköz következő attribútumai alapján:

- Engedélyezett állapot
- Megfelelő állapot
- Csatlakozás típusa (Azure AD-hez csatlakoztatott, hibrid Azure AD-hez csatlakoztatott, Azure AD-regisztráció)
- Tevékenység-időbélyeg
- Operációs rendszer
- Eszköz típusa (nyomtatók, biztonságos virtuális gépek, megosztott eszközök, regisztrált eszközök)

Az előnézet szűrési funkciójának engedélyezése a **minden eszköz** nézetben:

![A szűrési Előnézet funkció engedélyezése](./media/device-management-azure-portal/device-filter-preview-enable.png)

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Tallózással keresse meg **Azure Active Directory**  >  **eszközöket**.
1. Válassza ki a szalagcímet, amely szerint **kipróbálhatja az új eszközök szűrésének újdonságait. Ide kattintva engedélyezheti az előzetes verziót.**

Mostantól lehetősége lesz **szűrők hozzáadására** a **minden eszköz** nézethez.

### <a name="download-devices-preview"></a>Eszközök letöltése (előzetes verzió)

A Cloud Device Administrators, az Intune-rendszergazdák és a globális rendszergazdák az **eszközök letöltése (előzetes verzió)** lehetőséget is használhatják az eszközök CSV-fájljának exportálására az alkalmazott szűrők alapján. Ha nem alkalmaz szűrőket a listára, akkor minden eszköz exportálva lesz. Az Exportálás akár egy óráig is futhat a következőtől függően: 

Az exportált lista a következő eszköz-identitási attribútumokat tartalmazza:

`accountEnabled, approximateLastLogonTimeStamp, deviceOSType, deviceOSVersion, deviceTrustType, dirSyncEnabled, displayName, isCompliant, isManaged, lastDirSyncTime, objectId, profileType, registeredOwners, systemLabels, registrationTime, mdmDisplayName`

## <a name="configure-device-settings"></a>Eszközbeállítások megadása

Az eszköz identitásának az Azure AD portálon való kezeléséhez az eszközöknek [regisztrálva](overview.md) kell lenniük, vagy hozzá kell csatlakozniuk az Azure ad-hez. Rendszergazdaként a következő eszközbeállítások konfigurálásával szabályozhatja az eszközök regisztrálásának és csatlakoztatásának folyamatát.

A Azure Portal eszköz beállításainak megtekintéséhez vagy kezeléséhez a következő szerepkörök egyikét kell hozzárendelni:

- Globális rendszergazda
- Felhőalapú eszköz rendszergazdája
- Globális olvasó
- Címtár-olvasó

![Az Azure AD-hez kapcsolódó eszközbeállítások](./media/device-management-azure-portal/device-settings-azure-portal.png)

- A **felhasználók csatlakozhatnak az eszközökhöz az Azure ad** -ben – ezzel a beállítással kiválaszthatja azokat a felhasználókat, akik regisztrálhatják az eszközeiket az Azure ad-hez csatlakoztatott eszközökként. Az alapértelmezett érték az **összes**.

> [!NOTE]
> Előfordulhat, hogy a **felhasználók az Azure ad-be való csatlakoztatáshoz** csak az Azure ad-csatlakozásra vonatkoznak a Windows 10 rendszeren. Ez a beállítás nem vonatkozik a hibrid Azure AD-hez csatlakoztatott eszközökre, az Azure [ad-hez csatlakoztatott virtuális gépekre az Azure-ban és az](./howto-vm-sign-in-azure-ad-windows.md#enabling-azure-ad-login-in-for-windows-vm-in-azure) Azure ad-hez csatlakoztatott eszközökön a [Windows Autopilot önálló üzembe helyezési módjával](/mem/autopilot/self-deploying) , mivel ezek a módszerek felhasználó nélküli környezetben működnek.

- **További helyi rendszergazdák az Azure ad-hez csatlakoztatott eszközökön** : kiválaszthatja azokat a felhasználókat, akik helyi rendszergazdai jogosultságokat kapnak az eszközön. Ezeket a felhasználókat a rendszer az Azure AD-ben adja hozzá az *eszköz-rendszergazdák* szerepkörhöz. A globális rendszergazdák az Azure AD-ben és az eszközök tulajdonosai alapértelmezés szerint helyi rendszergazdai jogosultságokat kapnak. Ez a lehetőség egy prémium kiadású funkció, amely olyan termékeken keresztül érhető el, mint a prémium szintű Azure AD vagy a nagyvállalati mobilitási csomag (EMS).
- A **felhasználók regisztrálhatják eszközeiket az Azure ad** -ben – ezt a beállítást úgy kell konfigurálni, hogy engedélyezze a Windows 10 személyes, iOS-, Android-és MacOS-eszközök regisztrálását az Azure ad-ben. Ha a **nincs** lehetőséget választja, az eszközök nem regisztrálhatnak az Azure ad-ben. A regisztráláshoz Microsoft Intune vagy mobileszköz-felügyelettel (MDM) regisztrálni kell a Microsoft 365. Ha ezen szolgáltatások valamelyikét konfigurálta, akkor az **összes** kiválasztva, a **none** érték nem érhető el.
- **Az Azure ad-hez csatlakoztatott vagy az Azure ad-ben regisztrált eszközöknek multi-Factor Authentication** – megadhatja, hogy szükség van-e további hitelesítési tényezőre az eszköznek az Azure ad-hez való csatlakoztatásához vagy regisztrálásához. Az alapértelmezett érték a **nem**. A többtényezős hitelesítés megkövetelése az eszköz regisztrálása vagy csatlakoztatása során ajánlott. Mielőtt engedélyezi a többtényezős hitelesítést a szolgáltatáshoz, meg kell győződnie arról, hogy a többtényezős hitelesítés konfigurálva van az eszközeit regisztráló felhasználók számára. Az Azure AD Multi-Factor Authentication szolgáltatásaival kapcsolatos további információkért tekintse meg az [Azure ad multi-Factor Authentication első lépéseivel foglalkozó](../authentication/concept-mfa-howitworks.md)témakört. 

> [!NOTE]
> Az Azure ad- **hez csatlakoztatott vagy az Azure ad-ben regisztrált eszközökre multi-Factor Authentication** beállítás az Azure ad-hez csatlakoztatott (bizonyos kivételekkel rendelkező) vagy az Azure ad által regisztrált eszközökre vonatkozik. Ez a beállítás nem vonatkozik a hibrid Azure AD-hez csatlakoztatott eszközökre, az Azure [ad-hez csatlakoztatott virtuális gépekre az Azure-ban és az](./howto-vm-sign-in-azure-ad-windows.md#enabling-azure-ad-login-in-for-windows-vm-in-azure) Azure ad-hez csatlakoztatott eszközökön a [Windows Autopilot öntelepítő üzemmód](/mem/autopilot/self-deploying)használatával.

> [!IMPORTANT]
> - Javasoljuk, hogy a feltételes hozzáférésben a ["regisztráció vagy csatlakozás eszközökhöz" felhasználói beavatkozást](../conditional-access/concept-conditional-access-cloud-apps.md#user-actions) használja a többtényezős hitelesítés kényszerítéséhez az eszközök csatlakoztatásához vagy regisztrálásához. 
> - Ezt a beállítást a **nem** értékre kell állítani, ha feltételes hozzáférési szabályzatot használ a multi-Factor authencation megköveteléséhez. 

- **Eszközök maximális száma** – ez a beállítás lehetővé teszi, hogy kiválassza az Azure ad-hez csatlakoztatott vagy az Azure ad-ban regisztrált eszközök maximális számát, amelyet a felhasználók az Azure ad-ben használhatnak. Ha a felhasználó eléri ezt a kvótát, nem tud további eszközöket hozzáadni, amíg a meglévő eszközök közül egy vagy több el nem távolítva. Az alapértelmezett érték a **50**. Növelheti az értéket akár 100-ra, és ha a 100-nál nagyobb értéket ad meg, az Azure AD beállítja az 100-ra. Korlátlan számú értéket is használhat, ha a korlátozás nem korlátozza a meglévő kvótákat.

> [!NOTE]
> **Az eszközök maximális száma** beállítás az Azure ad-hez csatlakoztatott vagy az Azure ad-ban regisztrált eszközökre vonatkozik. Ez a beállítás nem vonatkozik a hibrid Azure AD-hez csatlakoztatott eszközökre.

- [Vállalati állapothordozás](enterprise-state-roaming-overview.md)

## <a name="audit-logs"></a>Naplók

Az eszközök tevékenységeit a tevékenység naplóiban érheti el. Ezek a naplók az eszköz regisztrációs szolgáltatása és a felhasználók által aktivált tevékenységeket foglalják magukban:

- Eszközök létrehozása és tulajdonosok/felhasználók hozzáadása az eszközhöz
- Eszközbeállítások módosítása
- Eszközök műveletei, például az eszközök törlése vagy frissítése

A naplózási adathoz való belépési pont az **eszközök** lap **tevékenység** szakaszában található **naplók** .

A napló egy alapértelmezett listanézet, amely a következőket jeleníti meg:

- Az előfordulás dátuma és időpontja
- A célok
- Egy tevékenység kezdeményezője/szereplője
- A tevékenység (mi)

:::image type="content" source="./media/device-management-azure-portal/63.png" alt-text="Képernyőfelvétel az eszközök lap tevékenység szakaszának táblázatáról, amely felsorolja a dátumot, a célt, a szereplőt és a tevékenységet négy naplóban." border="false":::

A listanézetet az eszköztár **Oszlopok** elemére kattintva lehet testre szabni.

:::image type="content" source="./media/device-management-azure-portal/64.png" alt-text="Az eszközök lap eszköztárát ábrázoló képernyőkép. Az oszlopok eleme ki van emelve." border="false":::

A jelentésben lévő adatok megfelelő szintű szűkítéséhez az alábbi mezőkkel szűrheti a naplózott adatokat:

- Kategória
- Tevékenység erőforrástípusa
- Tevékenység
- Dátumtartomány
- Cél
- Kezdeményező (színész)

A szűrők mellett megkeresheti az adott bejegyzéseket is.

:::image type="content" source="./media/device-management-azure-portal/65.png" alt-text="Képernyőkép a naplózási adatszűrő vezérlőelemekről, a kategória, a tevékenység erőforrástípus, a tevékenység, a dátumtartomány, a cél és a Actor mezők és a keresési mezők közül." border="false":::

## <a name="next-steps"></a>Következő lépések

[Elavult eszközök kezelése az Azure AD-ben](manage-stale-devices.md)

[Vállalati állapothordozás](enterprise-state-roaming-overview.md)
