---
title: A Azure Active Directory eszköz üzembe helyezésének megtervezése
description: Válassza ki a szervezeti igényeknek megfelelő Azure AD-eszközök integrációs stratégiáit.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 06/15/2020
ms.author: baselden
author: BarbaraSelden
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: faa18b619c785ad23e1da037621597684b1c5c63
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "106551781"
---
# <a name="plan-your-azure-active-directory-device-deployment"></a>A Azure Active Directory eszköz üzembe helyezésének megtervezése

Ez a cikk segítséget nyújt az eszköz Azure AD-vel való integrálásának módszereinek kiértékeléséhez, a megvalósítási terv kiválasztásához, valamint a támogatott Eszközkezelő eszközökre mutató legfontosabb hivatkozásokat.

Azok az eszközök, amelyekről a felhasználók bejelentkeznek, bővülnek. A szervezetek asztali számítógépeket, laptopokat, telefonokat, tablettákat és más eszközöket is biztosíthatnak. A felhasználók saját eszközeiket hozhatnak, és különböző helyekről érhetik el az információkat. Ebben a környezetben a rendszergazda feladata, hogy a szervezet erőforrásai biztonságban maradjanak az összes eszközön.

A Azure Active Directory (Azure AD) lehetővé teszi, hogy a szervezet teljesítse ezeket a célokat az eszközök identitásának kezelésével. Most már lekérheti az eszközeit az Azure AD-ben, és a [Azure Portal](https://portal.azure.com/)egy központi helyén vezérelheti őket. Ez egységes felhasználói élményt és fokozott biztonságot nyújt, és csökkenti az új eszközök konfigurálásához szükséges időt.

Az eszközök Azure AD-be való integrálásának több módja is van:

* Az [eszközök regisztrálása](concept-azure-ad-register.md) az Azure ad-vel

* [Eszközök csatlakoztatása](concept-azure-ad-join.md) az Azure ad-hez (csak felhőalapú) vagy

* [Hibrid Azure ad-csatlakozás létrehozása](concept-azure-ad-join-hybrid.md) a helyszíni Active Directory és az Azure ad-ban található eszközök között. 

## <a name="learn"></a>Learn

Mielőtt elkezdené, győződjön meg róla, hogy már ismeri az [Eszközállapot-kezelés áttekintését](overview.md).

### <a name="benefits"></a>Előnyök

Az eszközök Azure AD-identitásként való megadásának legfőbb előnyei:

* Növelje termelékenységét – az Azure AD-vel a felhasználók [zökkenőmentes bejelentkezést (SSO)](./azuread-join-sso.md) végezhetnek a helyszíni és a Felhőbeli erőforrásokhoz, ami lehetővé teszi számukra, hogy bárhol is legyenek.

* A biztonság fokozása – az Azure AD-eszközök lehetővé teszik, hogy [feltételes hozzáférési szabályzatokat](../conditional-access/require-managed-devices.md) alkalmazzon az eszközök vagy felhasználók identitása alapján. A feltételes hozzáférési szabályzatok további védelmet biztosíthatnak [Azure ad Identity Protection](../identity-protection/overview-identity-protection.md)használatával. Az eszköz az Azure AD-hez való csatlakoztatásának előfeltétele, hogy a biztonságot egy [jelszóval](../authentication/concept-authentication-passwordless.md) nem rendelkező hitelesítési stratégiával növelje.

* A felhasználói élmény javítása – az eszközök identitásával az Azure AD-ben a felhasználók könnyedén hozzáférhetnek a szervezet felhőalapú erőforrásaihoz mind a személyes, mind a vállalati eszközökről. A rendszergazdák az összes Windows-eszközön lehetővé teszik a [Enterprise State roaming](enterprise-state-roaming-overview.md) egységes felhasználói élményét.

* Egyszerűsítse az üzembe helyezést és a felügyeletet – az eszközök identitásának kezelése leegyszerűsíti az eszközök Azure AD-be való bevezetésének folyamatát a [Windows Autopilot](/windows/deployment/windows-autopilot/windows-10-autopilot), a [tömeges kiépítés](/mem/intune/enrollment/windows-bulk-enroll)és az önkiszolgáló [szolgáltatás: kezdőélmény (OOBE)](../user-help/user-help-join-device-on-network.md)használatával. Ezeket az eszközöket a mobileszköz-kezelési (MDM) eszközök, például a [Microsoft Intune](/mem/intune/fundamentals/what-is-intune)és az [Azure Portalokban](https://portal.azure.com/)lévő identitások kezelésére használhatja.

### <a name="training-resources"></a>Erőforrások betanítása

Videó:  [feltételes hozzáférés eszköz-vezérlőkkel](https://youtu.be/NcONUf-jeS4)

Gyakori kérdések: [Azure ad-eszközkezelés – gyakori kérdések](faq.yml)  és [beállítások és adatroaming – gyakori kérdések](enterprise-state-roaming-faqs.md) 

## <a name="plan-the-deployment-project"></a>Az üzembe helyezési projekt megtervezése

A környezetében érdemes figyelembe vennie a szervezeti igényeket is.

### <a name="engage-the-right-stakeholders"></a>A megfelelő résztvevők bevonása

A technológiai projektek meghibásodása esetén általában a hatás, az eredmények és a felelősségek eltérő elvárásai miatt válnak elérhetővé. A buktatók elkerülése érdekében [Győződjön meg arról, hogy a megfelelő érintett feleket folytatja](../fundamentals/active-directory-deployment-plans.md) , és hogy a projektben szereplő, az érintett felek szerepkörei jól megértettek. 

Ehhez a csomaghoz adja hozzá a következő érintett feleket a listához:

| Szerepkör| Leírás |
| - | - |
| Eszköz rendszergazdája| Az eszköz csapatának képviselője, amely képes ellenőrizni, hogy a csomag megfelel-e a szervezete igényeinek. |
| Hálózati rendszergazda| A hálózati csapat képviselője, amely gondoskodik a hálózati követelmények teljesítéséről. |
| Eszközkezelő csapat| Az eszközök leltárát kezelő csoport. |
| Operációs rendszer-specifikus felügyeleti csapatok| Adott operációsrendszer-verziókat támogató és kezelő csapatok. Előfordulhat például, hogy egy Mac-vagy iOS-beli irányított csapat van. |

### <a name="plan-communications"></a>A kommunikáció tervezése

A kommunikáció minden új szolgáltatás sikeressége szempontjából kritikus fontosságú. Proaktív módon kommunikálhat a felhasználókkal, hogy a felhasználói élmény hogyan módosuljon, mikor módosul, és hogyan szerezhet támogatást, ha problémákat tapasztal.

### <a name="plan-a-pilot"></a>Pilóta megtervezése

Javasoljuk, hogy az integrációs módszer kezdeti konfigurációját tesztkörnyezetben vagy tesztelési eszközökkel rendelkező kis csoporttal ellenőrizze. Tekintse [meg a próbaüzem ajánlott eljárásait](../fundamentals/active-directory-deployment-plans.md)ismertető témakört.

A hibrid Azure AD JOIN üzembe helyezése egyszerű, és 100%-a rendszergazda feladata a végfelhasználói beavatkozás nélkül. Előfordulhat, hogy a [hibrid Azure ad-csatlakozás ellenőrzött érvényesítését](hybrid-azuread-join-control.md) szeretné elvégezni, mielőtt a teljes szervezeten belül engedélyezte az egészet.

## <a name="choose-your-integration-methods"></a>Integrációs módszerek kiválasztása

A szervezete több eszköz-integrációs módszert is használhat egyetlen Azure AD-bérlőben. A cél az, hogy kiválassza azokat a metódusokat, amelyek megfelelőek az eszközök Azure AD-ben való biztonságos kezeléséhez. Számos paramétert kell megcélozni, beleértve a tulajdonjogot, az eszközök típusát, az elsődleges célközönséget és a szervezet infrastruktúráját.

A következő információk segítségével eldöntheti, hogy mely integrációs módszereket kívánja használni.

### <a name="decision-tree-for-devices-integration"></a>Eszközök integrációjának döntési fája

A fa használatával meghatározhatja a szervezeti tulajdonú eszközök beállításait. 

> [!NOTE]
> A személyes vagy a saját eszköz (BYOD) forgatókönyvei nem jelennek meg ebben a diagramban. Mindig az Azure AD-regisztrációt eredményezik.

 ![Döntési fa](./media/plan-device-deployment/flowchart.png)

### <a name="comparison-matrix"></a>Összehasonlító mátrix

az iOS-és Android-eszközök csak az Azure AD regisztrálva lehetnek. A következő táblázat a Windows-ügyféleszközök magas szintű szempontjait mutatja be. Használja áttekintésként, majd vizsgálja meg a különböző integrációs módszereket részletesen.

| Megfontolandó | Az Azure AD-ban regisztrálva| Azure AD-csatlakozás| Hibrid Azure AD-csatlakozás |
| - | - | - | - |
| **Ügyféloldali operációs rendszerek**| | |  |
| Windows 10-eszközök| ![Jelölje be ezeket az értékeket.](./media/plan-device-deployment/check.png)| ![Jelölje be ezeket az értékeket.](./media/plan-device-deployment/check.png)| ![Jelölje be ezeket az értékeket.](./media/plan-device-deployment/check.png) |
| Windows Down szintű eszközök (Windows 8,1 vagy Windows 7)| | | ![Jelölje be ezeket az értékeket.](./media/plan-device-deployment/check.png) |
|**Bejelentkezési beállítások**| | |  |
| Végfelhasználói helyi hitelesítő adatok| ![Jelölje be ezeket az értékeket.](./media/plan-device-deployment/check.png)| |  |
| Jelszó| ![Jelölje be ezeket az értékeket.](./media/plan-device-deployment/check.png)| ![Jelölje be ezeket az értékeket.](./media/plan-device-deployment/check.png)| ![Jelölje be ezeket az értékeket.](./media/plan-device-deployment/check.png) |
| PIN-kód az eszközhöz| ![Jelölje be ezeket az értékeket.](./media/plan-device-deployment/check.png)| |  |
| Windows Hello| ![Jelölje be ezeket az értékeket.](./media/plan-device-deployment/check.png)| |  |
| Vállalati Windows Hello| | ![Jelölje be ezeket az értékeket.](./media/plan-device-deployment/check.png)| ![Jelölje be ezeket az értékeket.](./media/plan-device-deployment/check.png) |
| 2,0 biztonsági kulcsok| | ![Jelölje be ezeket az értékeket.](./media/plan-device-deployment/check.png)| ![Jelölje be ezeket az értékeket.](./media/plan-device-deployment/check.png) |
| Microsoft Authenticator alkalmazás (jelszóval nem rendelkező)| ![Jelölje be ezeket az értékeket.](./media/plan-device-deployment/check.png)| ![Jelölje be ezeket az értékeket.](./media/plan-device-deployment/check.png)| ![Jelölje be ezeket az értékeket.](./media/plan-device-deployment/check.png) |
|**Főbb képességek**| | |  |
| Egyszeri bejelentkezés a Felhőbeli erőforrásokhoz| ![Jelölje be ezeket az értékeket.](./media/plan-device-deployment/check.png)| ![Jelölje be ezeket az értékeket.](./media/plan-device-deployment/check.png)| ![Jelölje be ezeket az értékeket.](./media/plan-device-deployment/check.png) |
| Egyszeri bejelentkezés a helyszíni erőforrásokba| | ![Jelölje be ezeket az értékeket.](./media/plan-device-deployment/check.png)| ![Jelölje be ezeket az értékeket.](./media/plan-device-deployment/check.png) |
| Feltételes hozzáférés <br> (Az eszközök megfelelőségének megjelölése kötelező) <br> (A MDM-nek kell kezelnie)| ![Jelölje be ezeket az értékeket.](./media/plan-device-deployment/check.png) | ![Jelölje be ezeket az értékeket.](./media/plan-device-deployment/check.png)|![Jelölje be ezeket az értékeket.](./media/plan-device-deployment/check.png) |
Feltételes hozzáférés <br>(Hibrid Azure AD-hez csatlakoztatott eszközök megkövetelése)| | | ![Jelölje be ezeket az értékeket.](./media/plan-device-deployment/check.png)
| Önkiszolgáló jelszó-visszaállítás a Windows bejelentkezési képernyőjéről| | ![Jelölje be ezeket az értékeket.](./media/plan-device-deployment/check.png)| ![Jelölje be ezeket az értékeket.](./media/plan-device-deployment/check.png) |
| Windows Hello PIN-kód alaphelyzetbe állítása| | ![Jelölje be ezeket az értékeket.](./media/plan-device-deployment/check.png)| ![Jelölje be ezeket az értékeket.](./media/plan-device-deployment/check.png) |
| Vállalati állapot barangolása az eszközök között| | ![Jelölje be ezeket az értékeket.](./media/plan-device-deployment/check.png)| ![Jelölje be ezeket az értékeket.](./media/plan-device-deployment/check.png) |


## <a name="azure-ad-registration"></a>Azure AD-regisztráció 

A regisztrált eszközöket gyakran [Microsoft Intune](/mem/intune/enrollment/device-enrollment)kezelik. Az eszközöket az operációs rendszertől függően számos módon regisztrálják az Intune-ban. 

Az Azure AD-ban regisztrált eszközök támogatást nyújtanak a saját eszközök (BYOD) és a vállalati tulajdonú eszközök számára az egyszeri bejelentkezéshez a Felhőbeli erőforrásokhoz. Az erőforrásokhoz való hozzáférés az eszközre és a felhasználóra alkalmazott Azure AD [feltételes hozzáférési szabályzatokon](../conditional-access/require-managed-devices.md) alapul.

### <a name="registering-devices"></a>Eszközök regisztrálása

A regisztrált eszközöket gyakran [Microsoft Intune](/mem/intune/enrollment/device-enrollment)kezelik. Az eszközöket az operációs rendszertől függően számos módon regisztrálják az Intune-ban. 

A BYOD és a vállalati tulajdonú mobileszköz a vállalati portál alkalmazást telepítő felhasználók által regisztrálva van.

* [iOS](/mem/intune/user-help/install-and-sign-in-to-the-intune-company-portal-app-ios)

* [Android](/mem/intune/user-help/enroll-device-android-company-portal)

* [Windows 10](/mem/intune/user-help/enroll-windows-10-device)

Ha az eszközök regisztrálása a legjobb megoldás a szervezet számára, tekintse meg a következő forrásokat:

* Az [Azure ad regisztrált eszközeinek](concept-azure-ad-register.md)áttekintése.

* Ez a végfelhasználói dokumentáció a [személyes eszköz regisztrálása a szervezet hálózatán](../user-help/user-help-register-device-on-network.md).

## <a name="azure-ad-join"></a>Azure AD-csatlakozás

Az Azure AD JOIN lehetővé teszi, hogy áttérjen a Windowsos Felhőbeli első modellre. Nagyszerű alapot biztosít, ha az eszközkezelés korszerűsítését tervezi, és csökkenti az eszközökhöz kapcsolódó informatikai költségeket. Az Azure AD JOIN csak Windows 10-es eszközökön használható. Vegye figyelembe, hogy az új eszközök első választása.

Az [Azure ad-hez csatlakoztatott eszközök azonban egyszeri bejelentkezést is végezhetnek a helyszíni erőforrásokhoz](azuread-join-sso.md) , amikor azok a szervezet hálózatán vannak, a hitelesítést végezhetnek a helyszíni kiszolgálókon, például a fájlokhoz, a nyomtatáshoz és más alkalmazásokhoz.

Ha a szervezete számára a legjobb megoldás, tekintse meg a következő forrásokat:

* Az [Azure ad-hez csatlakoztatott eszközök](concept-azure-ad-join.md)áttekintése.

* Ismerkedjen meg az [Azure ad JOIN implementációs csomaggal](azureadjoin-plan.md).

### <a name="provisioning-azure-ad-join-to-your-devices"></a>Azure AD-csatlakozás üzembe helyezése az eszközökön

Az Azure AD JOIN kiépítéséhez a következő megközelítések állnak rendelkezésre:

* Önkiszolgáló: a [Windows 10 első futtatásának élménye](azuread-joined-devices-frx.md)

Ha egy eszközön a Windows 10 Professional vagy a Windows 10 Enterprise rendszer van telepítve, az FRX élmény alapértelmezés szerint a vállalati eszközök beállítási folyamatát indítja el.

* [A Windows beépített felhasználói felülete (OOBE) vagy a Windows beállításai](../user-help/user-help-join-device-on-network.md)

* [Windows Autopilot](/windows/deployment/windows-autopilot/windows-autopilot)

* [Tömeges beléptetés](/mem/intune/enrollment/windows-bulk-enroll)

A [módszerek körültekintő összehasonlítása](azureadjoin-plan.md)után válassza ki az üzembe helyezési eljárást.

Azt is meghatározhatja, hogy az Azure AD JOIN a legjobb megoldás-e az eszközhöz, és hogy az eszköz már különböző állapotokban van. A frissítési szempontokat itt találja.

| Aktuális eszköz állapota| Kívánt eszköz állapota| Használati útmutató |
| - | - | - |
| Helyi tartományhoz csatlakoztatva| Azure AD-csatlakozás| Az eszköz csatlakoztatása a helyszíni tartományból az Azure AD-hez való csatlakozás előtt |
| Hibrid Azure AD-csatlakozás| Azure AD-csatlakozás| Az eszköz csatlakoztatása a helyszíni tartományból és az Azure AD-ből az Azure AD-be való csatlakozás előtt |
| Az Azure AD-ban regisztrálva| Azure AD-csatlakozás| Az eszköz regisztrációjának törlése az Azure AD-hez való csatlakozás előtt |


## <a name="hybrid-azure-ad-join"></a>Hibrid Azure AD-csatlakozás

Ha helyszíni Active Directory-környezettel rendelkezik, és az Active Directory-tartományhoz csatlakoztatott számítógépeit az Azure AD-hez szeretné csatlakoztatni, ezt a hibrid Azure AD JOIN használatával végezheti el. A Windows- [eszközök széles körét](hybrid-azuread-join-plan.md)támogatja, beleértve a Windows jelenlegi és a Windows rendszerbeli régebbi eszközöket is.

A legtöbb szervezet már rendelkezik tartományhoz csatlakoztatott eszközökkel, és felügyelheti őket Csoportházirend vagy System Center Configuration Manager (SCCM) használatával. Ebben az esetben javasoljuk, hogy a hibrid Azure AD-csatlakozást úgy konfigurálja, hogy kihasználja a meglévő befektetések kihasználásával járó előnyöket.

Ha a hibrid Azure AD JOIN a legjobb megoldás a szervezet számára, tekintse meg a következő forrásokat:

* A [hibrid Azure ad-hez csatlakoztatott eszközök](concept-azure-ad-join-hybrid.md)áttekintése.

* Ismerkedjen meg a [hibrid Azure ad JOIN implementációs](hybrid-azuread-join-plan.md) csomaggal.

### <a name="provisioning-hybrid-azure-ad-join-to-your-devices"></a>Hibrid Azure AD-csatlakozás üzembe helyezése az eszközökön

[Tekintse át az identitás-infrastruktúrát](hybrid-azuread-join-plan.md). A Azure AD Connect egy varázslót biztosít a hibrid Azure AD-csatlakozás konfigurálásához a következőhöz:

* [Összevont tartományok](hybrid-azuread-join-federated-domains.md)

* [Felügyelt tartományok](hybrid-azuread-join-managed-domains.md)

Ha a Azure AD Connect szükséges verzióját telepíti, a következő témakörben talál további információt: [hibrid Azure ad JOIN manuális konfigurálása](hybrid-azuread-join-manual.md). 

> [!NOTE] 
> A helyszíni tartományhoz csatlakoztatott Windows 10 rendszerű eszköz megpróbál automatikusan csatlakozni az Azure AD-hez, hogy alapértelmezés szerint a hibrid Azure AD-hez csatlakozzon. Ez csak akkor lesz sikeres, ha beállította a megfelelő környezetet. 

Előfordulhat, hogy a hibrid Azure AD JOIN a legjobb megoldás az eszközhöz, és az eszköz már más állapotban van. A frissítési szempontokat itt találja.

| Aktuális eszköz állapota| Kívánt eszköz állapota| Használati útmutató |
| - | - | - |
| Helyszíni tartományhoz való csatlakozás| Hibrid Azure AD-csatlakozás| Az Azure AD-csatlakozás vagy a AD FS használata az Azure-ba való csatlakozáshoz |
| Helyszíni munkacsoport csatlakoztatva vagy új| Hibrid Azure AD-csatlakozás| A [Windows Autopilot](/windows/deployment/windows-autopilot/windows-autopilot)támogatja. Ellenkező esetben az eszköznek a hibrid Azure AD-csatlakozás előtt csatlakoznia kell a helyszíni tartományhoz |
| Azure AD-hez csatlakoztatva| Hibrid Azure AD-csatlakozás| Csatlakozzon az Azure AD-ből, amely a helyszíni munkacsoportba vagy új állapotba helyezi. |
| Azure AD-regisztráció| Hibrid Azure AD-csatlakozás| A Windows-verziótól függ. [Tekintse meg ezeket a szempontokat](hybrid-azuread-join-plan.md). |

## <a name="manage-your-devices"></a>Eszközök kezelése

Miután regisztrálta vagy csatlakoztatta az eszközeit az Azure AD-hez, az eszköz identitásának kezeléséhez használja a [Azure Portal](https://portal.azure.com/) központi helyen. A Azure Active Directory eszközök lap a következőket teszi lehetővé:

* [Az eszközbeállítások konfigurálása](device-management-azure-portal.md#configure-device-settings)
* A Windows-eszközök felügyeletéhez helyi rendszergazdának kell lennie. Az [Azure ad frissíti ezt a tagságot az Azure ad-hez csatlakoztatott eszközökhöz](assign-local-admin.md), automatikusan hozzáadja az Eszközkezelő szerepkört az összes csatlakoztatott eszközhöz.

Győződjön meg arról, hogy az [elavult eszközök felügyeletével](manage-stale-devices.md)megőrzi a környezetet, és koncentrálja az erőforrásokat az aktuális eszközök kezelésére.

* [Az eszközhöz kapcsolódó naplók áttekintése](device-management-azure-portal.md#audit-logs)

### <a name="supported-device-management-tools"></a>Támogatott eszközök felügyeleti eszközei

A rendszergazdák további Eszközkezelő eszközökkel biztosíthatják a regisztrált és csatlakoztatott eszközök védelmét és további szabályozását. Ezek az eszközök lehetővé teszik a szervezet által szükséges konfigurációk betartatását, például a tárolás titkosítását, a jelszó bonyolultságát, a szoftverek telepítését és a szoftverfrissítések használatát. 

A támogatott és nem támogatott platformok áttekintése integrált eszközökhöz:

| Eszközfelügyeleti eszközök| Az Azure AD-ban regisztrálva| Azure AD-csatlakozás| Hibrid Azure AD-csatlakozás|
| - | - | - | - |
| [Mobileszköz-felügyelet (MDM) ](/windows/client-management/mdm/azure-active-directory-integration-with-mdm) <br>Példa: Microsoft Intune| ![Jelölje be ezeket az értékeket.](./media/plan-device-deployment/check.png)| ![Jelölje be ezeket az értékeket.](./media/plan-device-deployment/check.png)| ![Jelölje be ezeket az értékeket.](./media/plan-device-deployment/check.png)| 
| [Közös felügyelet Microsoft Intune és Microsoft végponttal Configuration Manager](/mem/configmgr/comanage/overview) <br>(Windows 10 és újabb)| | ![Jelölje be ezeket az értékeket.](./media/plan-device-deployment/check.png)| ![Jelölje be ezeket az értékeket.](./media/plan-device-deployment/check.png)| 
| [Csoportházirend](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831791(v=ws.11))<br>(Csak Windows)| | | ![Jelölje be ezeket az értékeket.](./media/plan-device-deployment/check.png)| 



 Javasoljuk, hogy vegye fontolóra [Microsoft Intune Mobile Application Management (MAM)](/mem/intune/apps/app-management) használatát a regisztrált iOS-vagy Android-eszközökön vagy anélkül.

 A rendszergazdák a szervezet Windows operációs rendszereit üzemeltető [virtuális asztali infrastruktúra-(VDI-) platformokon is üzembe](howto-device-identity-virtual-desktop-infrastructure.md) helyezhetik a felügyeletet, és csökkenthetik a költségeket az erőforrások konszolidálásával és központosításával. 

### <a name="troubleshoot-device-identities"></a>Eszközidentitásokkal kapcsolatos problémák elhárítása

* [Eszközök hibaelhárítása a dsregcmd parancs használatával](troubleshoot-device-dsregcmd.md)

* [A Azure Active Directory Enterprise State Roaming beállításainak hibaelhárítása](enterprise-state-roaming-troubleshooting.md)

Ha a tartományhoz csatlakoztatott Windows-eszközök hibrid Azure AD-csatlakozásának befejezésével kapcsolatos problémákat tapasztal, tekintse meg a következőt:

* [Hibrid Azure AD-csatlakozás a Windows aktuális eszközeihez – problémamegoldás](troubleshoot-hybrid-join-windows-current.md)

* [Hibrid Azure AD-csatlakozás a Windows Down szintű eszközökhöz – problémamegoldás](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>Következő lépések

* [Az Azure AD JOIN megvalósításának megtervezése](azureadjoin-plan.md)
* [A hibrid Azure AD JOIN megvalósításának megtervezése](hybrid-azuread-join-plan.md)
* [Eszközidentitások kezelése](device-management-azure-portal.md)
