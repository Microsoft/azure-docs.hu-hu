---
title: Helyi rendszergazdák kezelése az Azure AD-hez csatlakoztatott eszközökön
description: Ismerje meg, hogyan rendelhet Azure-szerepköröket egy Windows-eszköz helyi rendszergazdák csoportjához.
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
ms.openlocfilehash: d482f21955b76e6b90523afe3b4933378c91d36e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98107361"
---
# <a name="how-to-manage-the-local-administrators-group-on-azure-ad-joined-devices"></a>A helyi Rendszergazdák csoport kezelése az Azure AD-hez csatlakoztatott eszközökön

Windows-eszközök kezeléséhez a helyi Rendszergazdák csoport tagjának kell lennie. A Azure Active Directory (Azure AD) csatlakoztatási folyamatának részeként az Azure AD frissíti a csoport tagságát az eszközön. A tagsági frissítést testreszabhatja az üzleti igények kielégítése érdekében. A tagság frissítése például hasznos, ha engedélyezni szeretné, hogy az ügyfélszolgálat munkatársai olyan feladatokat végezzenek el, amelyeken rendszergazdai jogosultságok szükségesek az eszközön.

Ez a cikk azt ismerteti, hogyan működik a helyi rendszergazdák tagságának frissítése, és hogyan szabható testre az Azure AD JOIN szolgáltatásban. A cikk tartalma nem vonatkozik a **hibrid Azure ad-hez csatlakoztatott** eszközökre.

## <a name="how-it-works"></a>Működés

Ha Azure ad-csatlakozással csatlakoztat egy Windows-eszközt az Azure AD-hez, az Azure AD hozzáadja a következő rendszerbiztonsági tageket a helyi Rendszergazdák csoporthoz az eszközön:

- Az Azure AD globális rendszergazdai szerepköre
- Az Azure AD-eszköz rendszergazdai szerepköre 
- Az Azure AD Joint végző felhasználó   

Az Azure AD-szerepkörök a helyi Rendszergazdák csoporthoz való hozzáadásával frissítheti azokat a felhasználókat, akik az Azure AD-ben bármikor kezelhetik az eszközöket anélkül, hogy bármit módosítani kellene az eszközön. Az Azure AD az Azure AD-eszköz rendszergazdai szerepkörét is hozzáadja a helyi rendszergazdák csoportjához a legalacsonyabb jogosultsági szint (PoLP) elvének támogatásához. A globális rendszergazdák mellett engedélyezheti azon felhasználók számára is, akik *csak* az eszköz rendszergazdai szerepkörét rendelték hozzá az eszközök kezeléséhez. 

## <a name="manage-the-global-administrators-role"></a>A globális rendszergazdák szerepkör kezelése

A globális rendszergazdai szerepkör tagságának megtekintéséhez és frissítéséhez tekintse meg a következőt:

- [Rendszergazdai szerepkör összes tagjának megtekintése Azure Active Directory](../roles/manage-roles-portal.md)
- [Felhasználó hozzárendelése rendszergazdai szerepkörökhöz az Azure Active Directory-ban](../fundamentals/active-directory-users-assign-role-azure-portal.md)


## <a name="manage-the-device-administrator-role"></a>Az eszköz rendszergazdai szerepkörének kezelése 

A Azure Portal az **eszközök** lapon kezelheti az eszköz rendszergazdai szerepkörét. Az **eszközök** lap megnyitása:

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) globális rendszergazdaként.
1. Keresse meg és válassza ki az *Azure Active Directoryt*.
1. A **kezelés** szakaszban kattintson az **eszközök** elemre.
1. Az **eszközök** lapon kattintson az **eszközbeállítások** elemre.

Az eszköz rendszergazdai szerepkörének módosításához **további helyi rendszergazdákat kell konfigurálnia az Azure ad-hez csatlakoztatott eszközökön**.  

![További helyi rendszergazdák](./media/assign-local-admin/10.png)

>[!NOTE]
> Ehhez a beállításhoz prémium szintű Azure AD bérlő szükséges. 

Az eszközök rendszergazdái az összes Azure AD-hez csatlakoztatott eszközhöz vannak rendelve. Az eszközök rendszergazdái nem állíthatók be egy adott eszközre. Az eszköz rendszergazdai szerepkörének frissítése nem feltétlenül jelent azonnali hatást az érintett felhasználókra. Azokon az eszközökön, amelyeken a felhasználó már be van jelentkezve, a jogosultsági szint megemelése akkor történik meg, ha az alábbi műveletek *is* történnek:

- A 4 óra elteltével az Azure AD új elsődleges frissítési jogkivonatot adott ki a megfelelő jogosultságokkal. 
- A felhasználó kijelentkezik, és visszajelentkezik, nem zárolja vagy oldja fel a profil frissítését.

>[!NOTE]
> A fenti műveletek nem alkalmazhatók azokra a felhasználókra, akik korábban nem jelentkezett be a megfelelő eszközre. Ebben az esetben a rendszergazdai jogosultságokat az eszközre való első bejelentkezés után azonnal alkalmazza a rendszer. 

## <a name="manage-administrator-privileges-using-azure-ad-groups-preview"></a>Rendszergazdai jogosultságok kezelése Azure AD-csoportokkal (előzetes verzió)

>[!NOTE]
> Ez a szolgáltatás jelenleg előzetes kiadásban elérhető.


A Windows 10 2004 Update-től kezdődően az Azure AD-csoportok használatával felügyelheti az Azure AD-hez csatlakoztatott eszközök rendszergazdai jogosultságait a [korlátozott csoportok](/windows/client-management/mdm/policy-csp-restrictedgroups) Mdm házirenddel. Ez a szabályzat lehetővé teszi, hogy egyéni felhasználókat vagy Azure AD-csoportokat rendeljen hozzá a helyi rendszergazdák csoportjához egy Azure AD-hez csatlakoztatott eszközön, és így részletességgel konfigurálja a különböző rendszergazdákat az eszközök különböző csoportjaihoz. 

>[!NOTE]
> A Windows 10 20H2 frissítésének megkezdése előtt javasoljuk a [helyi felhasználók és csoportok](/windows/client-management/mdm/policy-csp-localusersandgroups) házirend használatát a korlátozott csoportok házirend helyett.


Jelenleg nincs felhasználói felület az Intune-ban a házirendek kezeléséhez, és [Egyéni OMA-URI-beállításokkal](/mem/intune/configuration/custom-settings-windows-10)kell konfigurálni őket. Néhány megfontolandó szempont a következő házirendek valamelyikének használatához: 

- Az Azure AD-csoportok házirenden keresztüli hozzáadásához a csoport SID-je szükséges a [Microsoft Graph API csoportok számára](/graph/api/resources/group?view=graph-rest-beta)történő végrehajtásával. A SID-t az API-válasz tulajdonsága határozza meg `securityIdentifier` .
- A korlátozott csoportok házirendjének betartatásakor a rendszer a tagok listáján nem szereplő összes aktuális tagot eltávolítja. Ennek a szabályzatnak az új tagokkal vagy csoportokkal való érvényesítése eltávolítja a meglévő rendszergazdákat, azaz az eszközhöz csatlakozó felhasználót, az eszköz rendszergazdai szerepkörét és a globális rendszergazdai szerepkört az eszközről. A meglévő tagok eltávolításának elkerüléséhez konfigurálnia kell őket a korlátozott csoportok házirendjének tagok listájának részeként. Ez a korlátozás akkor fordul elő, ha a helyi felhasználók és csoportok házirendet használja, amely engedélyezi a növekményes frissítéseket a csoporttagság számára
- A két házirendet használó rendszergazdai jogosultságokat a rendszer csak a következő jól ismert csoportok esetében értékeli ki egy Windows 10-es eszközön: rendszergazdák, felhasználók, vendégek, Kiemelt felhasználók, Távoli asztal felhasználók és távfelügyeleti felhasználók. 
- A helyi rendszergazdák Azure AD-csoportokkal való kezelése nem alkalmazható a hibrid Azure AD-hez csatlakoztatott vagy az Azure AD által regisztrált eszközökre.
- Habár a Windows 10 2004 frissítése előtt már létezett a korlátozott csoportok házirend, az nem támogatja az Azure AD-csoportokat az eszköz helyi rendszergazdák csoportjának tagjaként. 

## <a name="manage-regular-users"></a>Normál felhasználók kezelése

Alapértelmezés szerint az Azure AD hozzáadja az Azure AD-csatlakozást végző felhasználót az eszközön található rendszergazdai csoporthoz. Ha meg szeretné akadályozni, hogy a normál felhasználók helyi rendszergazdák legyenek, a következő lehetőségek közül választhat:

- [Windows Autopilot](/windows/deployment/windows-autopilot/windows-10-autopilot) – a Windows Autopilot lehetővé teszi, hogy megakadályozza, hogy az összekapcsolást végző elsődleges felhasználó helyi rendszergazda legyen. Ezt az [Autopilot-profil létrehozásával](/intune/enrollment-autopilot#create-an-autopilot-deployment-profile)végezheti el.
- [Tömeges beléptetés](/intune/windows-bulk-enroll) – egy olyan Azure ad-csatlakozás, amely egy tömeges beléptetés kontextusában történik, egy automatikusan létrehozott felhasználó kontextusában történik. Az eszköz csatlakoztatása után bejelentkezett felhasználók nem lesznek hozzáadva a rendszergazdák csoporthoz.   

## <a name="manually-elevate-a-user-on-a-device"></a>Felhasználó manuális megemelése egy eszközön 

Az Azure AD JOIN folyamatán kívül manuálisan is beállíthatja, hogy egy normál felhasználó helyi rendszergazda legyen egy adott eszközön. Ehhez a lépéshez már a helyi Rendszergazdák csoport tagjának kell lennie. 

A **Windows 10 1709** kiadástól kezdve ezt a feladatot elvégezheti a **Beállítások – > fiókok – > más felhasználók**. Válassza a **munkahelyi vagy iskolai felhasználó hozzáadása** lehetőséget, írja be a felhasználó egyszerű felhasználónevét a **felhasználói fiók** területen, és válassza a *rendszergazda* elemet a **Fiók típusa** területen.  
 
Emellett a parancssor használatával is hozzáadhat felhasználókat:

- Ha a bérlő felhasználóit szinkronizálják a helyszíni Active Directory, használja a következőt: `net localgroup administrators /add "Contoso\username"` .
- Ha a bérlői felhasználók az Azure AD-ban jönnek létre, használja a `net localgroup administrators /add "AzureAD\UserUpn"`

## <a name="considerations"></a>Megfontolandó szempontok 

Nem rendelhet hozzá csoportokat az eszköz rendszergazdai szerepköréhez, csak az egyes felhasználók engedélyezettek.

Az eszközök rendszergazdái az összes Azure AD-hez csatlakoztatott eszközhöz vannak rendelve. Nem lehetnek hatókörük egy adott eszközre.

Ha eltávolítja a felhasználókat az eszköz rendszergazdai szerepkörből, akkor továbbra is a helyi rendszergazdai jogosultsággal rendelkezik az eszközön, amíg be van jelentkezve. A rendszer visszavonja a jogosultságot a következő bejelentkezéskor, amikor új elsődleges frissítési jogkivonatot állít ki. Ez a visszavonás a jogosultság megemeléséhez hasonlóan akár 4 órát is igénybe vehet.

## <a name="next-steps"></a>Következő lépések

- További információk az eszközök Azure Portalon végzett felügyeletéről: [Eszközfelügyelet az Azure Portalon](device-management-azure-portal.md).
- Ha többet szeretne megtudni az eszközökön alapuló feltételes hozzáférésről, tekintse meg [Azure Active Directory eszközön alapuló feltételes hozzáférési szabályzatok konfigurálása](../conditional-access/require-managed-devices.md)című témakört.
