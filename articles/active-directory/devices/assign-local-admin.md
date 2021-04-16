---
title: Helyi rendszergazdák kezelése az Azure AD-hez csatlakozott eszközökön
description: Megtudhatja, hogyan rendelhet Hozzá Azure-szerepköröket a Windows-eszközök helyi rendszergazdák csoportjához.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 56e0f92593d185890e34a1a5120093d68cf45484
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107388415"
---
# <a name="how-to-manage-the-local-administrators-group-on-azure-ad-joined-devices"></a>A helyi rendszergazdák csoportjának kezelése az Azure AD-hez csatlakozott eszközökön

Windows-eszköz kezeléséhez a helyi rendszergazdák csoportjának kell lennie. Az Azure AD Azure Active Directory (Azure AD) részeként az Azure AD frissíti ennek a csoportnak a tagságát egy eszközön. A tagság frissítését az üzleti igényeinek megfelelően testreszabhatja. A tagság frissítése például akkor hasznos, ha lehetővé szeretné tenni a munkatársak számára az eszközön rendszergazdai jogosultságot igénylő feladatok elvégzését.

Ez a cikk azt ismerteti, hogyan működik a helyi rendszergazdák tagságfrissítése, és hogyan szabhatja testre az Azure AD-csatlakozás során. A cikk tartalma nem vonatkozik a hibrid **Azure AD-hez** csatlakozott eszközökre.

## <a name="how-it-works"></a>Működés

Amikor Azure AD-csatlakozással csatlakoztat egy Windows-eszközt az Azure AD-hez, az Azure AD a következő rendszerbiztonsági tagokat hozzáadja az eszköz helyi rendszergazdák csoportjához:

- Az Azure AD globális rendszergazdai szerepköre
- Az Azure AD-eszköz rendszergazdai szerepköre 
- Az Azure AD-csatlakozást végző felhasználó   

Ha Azure AD-szerepköröket ad hozzá a helyi rendszergazdák csoportjához, bármikor frissítheti azokat a felhasználókat, akik az Azure AD-ban kezelhetik az eszközt anélkül, hogy bármit módosítanának az eszközön. Az Azure AD emellett hozzáadja az Azure AD eszköz-rendszergazdai szerepkört is a helyi rendszergazdák csoportjához, hogy támogassa a legkevesebb jogosultság elvének (PoLP) alkalmazását. A globális rendszergazdákon kívül engedélyezheti a csak  az eszközadminisértő szerepkörhöz rendelt felhasználók számára is az eszközök kezelését. 

## <a name="manage-the-global-administrators-role"></a>A globális rendszergazdai szerepkör kezelése

A globális rendszergazdai szerepkör tagságának megtekintéséhez és frissítéséhez lásd:

- [Az összes megtekintése rendszergazdai szerepkör tagjainak Azure Active Directory](../roles/manage-roles-portal.md)
- [Felhasználó hozzárendelése rendszergazdai szerepkörökhöz az Azure Active Directory-ban](../fundamentals/active-directory-users-assign-role-azure-portal.md)


## <a name="manage-the-device-administrator-role"></a>Az eszköz-rendszergazdai szerepkör kezelése 

A Azure Portal az Eszközök lapon kezelheti az eszköz **rendszergazdai szerepkörét.** Az Eszközök **lap megnyitása:**

1. Jelentkezzen be [a](https://portal.azure.com) Azure Portal globális rendszergazdaként.
1. Keresse meg és válassza ki az *Azure Active Directoryt*.
1. A Kezelés **szakaszban** kattintson az Eszközök **elemre.**
1. Az Eszközök **lapon** kattintson az **Eszközbeállítások elemre.**

Az eszköz rendszergazdai szerepkörének módosításához konfigurálja a **További helyi rendszergazdákat az Azure AD-hez csatlakozott eszközökön.**  

![További helyi rendszergazdák](./media/assign-local-admin/10.png)

>[!NOTE]
> Ehhez a lehetőséghez egy prémium szintű Azure AD szükséges. 

eszközrendszergazda azure AD-hez csatlakozott összes eszközhöz hozzá van rendelve. Az eszközadministályokat nem lehet adott eszközkészletre beállítani. Az eszköz-rendszergazdai szerepkör frissítése nem feltétlenül van azonnali hatással az érintett felhasználókra. Az olyan eszközökön, amelyeken a felhasználó már be van jelentkezve, a jogosultsági szint emelése akkor történik meg, ha *mindkét* alábbi műveletre sor kerül:

- Az Azure AD legfeljebb 4 óra alatt ad ki egy új elsődleges frissítési jogkivonatot a megfelelő jogosultságokkal. 
- A felhasználó bejelentkezik, és nem zárolja/feloldja a profil frissítését.

>[!NOTE]
> A fenti műveletek nem alkalmazhatók az olyan felhasználókra, akik korábban nem jelentkezték be a megfelelő eszközre. Ebben az esetben a rendszergazdai jogosultságokat a rendszer közvetlenül az eszközre való első bejelentkezés után alkalmazza. 

## <a name="manage-administrator-privileges-using-azure-ad-groups-preview"></a>Rendszergazdai jogosultságok kezelése Azure AD-csoportokkal (előzetes verzió)

A Windows 10 2004-es verziójától kezdve az Azure AD-csoportokkal rendszergazdai jogosultságokat kezelhet az Azure AD-hez csatlakozott eszközökön a [korlátozott](/windows/client-management/mdm/policy-csp-restrictedgroups) csoportokra vonatkozó MDM-szabályzattal. Ez a szabályzat lehetővé teszi, hogy egyéni felhasználókat vagy Azure AD-csoportokat rendeljen a helyi rendszergazdák csoportjához egy Azure AD-hez csatlakozott eszközön, így részletesen konfigurálhatja a különböző rendszergazdákat a különböző eszközcsoportokhoz. 

>[!NOTE]
> A Windows 10 20H2 frissítéssel kezdődően [](/windows/client-management/mdm/policy-csp-localusersandgroups) javasoljuk, hogy korlátozott csoportok szabályzata helyett használja a Helyi felhasználók és csoportok szabályzatot.


Jelenleg nincs felhasználói felület az Intune-ban a szabályzatok kezeléséhez, és egyéni [OMA-URI-beállításokkal kell konfigurálni őket.](/mem/intune/configuration/custom-settings-windows-10) Néhány megfontolandó szempont az alábbi szabályzatok valamelyikének használatával kapcsolatban: 

- Az Azure AD-csoportok szabályzaton keresztüli hozzáadásához a csoport biztonsági azonosítójának kell lennie, amely a [Microsoft Graph API-jának](/graph/api/resources/group?view=graph-rest-beta)futtatásával szerezhető be. Az SID-t az `securityIdentifier` API-válasz tulajdonsága határozza meg.
- Korlátozott csoportokra vonatkozó szabályzat érvényesítésekor a rendszer eltávolítja a csoportnak a Tagok listán nem található tagjait. A szabályzat új tagokkal vagy csoportokkal való kényszerítésével eltávolítja a meglévő rendszergazdákat, vagyis az eszközt beléptető felhasználót, az Eszközadminis globális rendszergazda szerepkört az eszközről. A meglévő tagok eltávolításának elkerülése érdekében konfigurálnia kell őket a Korlátozott csoportok szabályzat Tagok listájának részeként. Ez a korlátozás a Helyi felhasználók és csoportok szabályzat használata esetén van kijavítva, amely lehetővé teszi a csoporttagság növekményes frissítését
- A két szabályzatot használó rendszergazdai jogosultságok csak a következő jól ismert csoportokra vannak kiértékelve egy Windows 10-eszközön – Rendszergazdák, Felhasználók, Vendégek, Kiemelt felhasználók, Távoli asztal Felhasználók és Távfelügyeleti felhasználók. 
- A helyi rendszergazdák Azure AD-csoportokkal való kezelése nem alkalmazható a hibrid Azure AD-hez csatlakozott vagy az Azure AD-ben regisztrált eszközökre.
- Bár a korlátozott csoportok szabályzat a 2004-es Windows 10 előtt is létezett, nem támogatja az Azure AD-csoportokat az eszköz helyi rendszergazdák csoportjának tagjaként. 

## <a name="manage-regular-users"></a>Normál felhasználók kezelése

Alapértelmezés szerint az Azure AD hozzáadja az Azure AD-csatlakozást végző felhasználót az eszköz rendszergazdai csoportjához. Ha meg szeretné akadályozni, hogy a normál felhasználók helyi rendszergazdává válnak, a következő lehetőségek közül választhat:

- [Windows Autopilot –](/windows/deployment/windows-autopilot/windows-10-autopilot) Windows Autopilot lehetőséget biztosít annak megakadályozására, hogy a csatlakozást végző elsődleges felhasználó helyi rendszergazda legyen. Ezt egy [Autopilot-profil létrehozásával valósíthatja meg.](/intune/enrollment-autopilot#create-an-autopilot-deployment-profile)
- [Csoportos regisztráció](/intune/windows-bulk-enroll) – A csoportos regisztráció kontextusában végrehajtott Azure AD-beléptetés egy automatikusan létrehozott felhasználó környezetében történik. Az eszközhöz való csatlakozás után bejelentkező felhasználók nem kerülnek be a rendszergazdák csoportjába.   

## <a name="manually-elevate-a-user-on-a-device"></a>Felhasználó manuális jogosultságszint-ének megemele az eszközön 

Az Azure AD-beléptetés folyamata mellett manuálisan is megemelheti egy normál felhasználó jogosultságszintét, hogy helyi rendszergazda legyen egy adott eszközön. Ehhez a lépéshez már a helyi rendszergazdák csoportjának tagja kell lennie. 

A Windows 10 **1709-es** kiadástól kezdve ezt a feladatot a **Beállítások -> fiókok -> felhasználók lapon hajthatja végre.** Válassza **a Munkahelyi vagy iskolai felhasználó hozzáadása lehetőséget,** adja meg a felhasználó upn-ját a Felhasználói fiók alatt, majd válassza a *Rendszergazda* lehetőséget a **Fiók típusa alatt**   
 
Emellett felhasználókat is hozzáadhat a parancssor használatával:

- Ha a bérlő felhasználói szinkronizálva vannak a helyi Active Directory, használja a `net localgroup administrators /add "Contoso\username"` et.
- Ha a bérlői felhasználók az Azure AD-ban vannak létrehozva, használja a következőt: `net localgroup administrators /add "AzureAD\UserUpn"`

## <a name="considerations"></a>Megfontolandó szempontok 

Az eszköz-rendszergazdai szerepkörhöz nem rendelhet hozzá csoportokat, csak az egyes felhasználók engedélyezettek.

eszközrendszergazda azure AD-hez csatlakozott összes eszközhöz hozzá van rendelve. Adott eszközkészletre nem lehet hatókört beállítani.

Amikor eltávolítja a felhasználókat az eszköz rendszergazdai szerepköréről, azok továbbra is helyi rendszergazdai jogosultságokkal fognak rendelkezik az eszközön, ha be vannak jelentkezve az eszközre. Az új elsődleges frissítési jogkivonatok kibocsátását a rendszer visszavonja a következő bejelentkezés során. A jogosultságszint-emeléshez hasonlóan ez a visszavonás akár 4 órát is igénybe vehet.

## <a name="next-steps"></a>Következő lépések

- További információk az eszközök Azure Portalon végzett felügyeletéről: [Eszközfelügyelet az Azure Portalon](device-management-azure-portal.md).
- További információ az eszközalapú feltételes hozzáférésről: [eszközalapú feltételes hozzáférési](../conditional-access/require-managed-devices.md)Azure Active Directory konfigurálása.
