---
title: Azure többtényezős hitelesítés beállítása a Windows rendszerű virtuális asztali környezethez – Azure
description: Az Azure többtényezős hitelesítés beállítása a fokozott biztonság érdekében a Windows Virtual Desktopban.
author: Heidilohr
ms.topic: how-to
ms.date: 12/10/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 00aba5d169a05eab25dcc63ca813955e71d09598
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97092380"
---
# <a name="enable-azure-multifactor-authentication-for-windows-virtual-desktop"></a>Azure többtényezős hitelesítés engedélyezése a Windows rendszerű virtuális asztali gépeken

>[!IMPORTANT]
> Ha ezt az oldalt a Windowsos virtuális asztal (klasszikus) dokumentációjában látogatja meg, akkor a befejezést követően [térjen vissza a Windows virtuális asztali (klasszikus) dokumentációba](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md) .

A Windows rendszerű virtuális asztal Windows-ügyfele kiváló megoldás a Windows rendszerű virtuális asztalok helyi géppel való integrálására. Ha azonban a Windows rendszerű virtuális asztali fiókját a Windows-ügyfélre konfigurálja, bizonyos intézkedésekre van szükség, hogy a felhasználók biztonságban maradjanak.

Amikor először jelentkezik be, az ügyfél kéri a felhasználónevet, a jelszót és az Azure többtényezős hitelesítést. Ezután, amikor legközelebb bejelentkezik, az ügyfél a Azure Active Directory (AD) vállalati alkalmazásból emlékszik a tokenre. Ha a **megjegyezés** lehetőséget választja a munkamenet-gazdagép hitelesítő adatainak megadásához, a felhasználók a hitelesítő adatok újbóli megadása nélkül jelentkezhetnek be az ügyfél újraindítása után.

Habár a hitelesítő adatok megjegyzése kényelmes, az üzembe helyezést a vállalati forgatókönyvek vagy a személyes eszközök kevésbé biztonságosak is tehetik. A felhasználók számára biztosíthatja, hogy az ügyfél folyamatosan kérjen az Azure többtényezős hitelesítési hitelesítő adatait gyakrabban. Ez a cikk bemutatja, hogyan konfigurálhatja a Windows rendszerű virtuális asztal feltételes hozzáférési szabályzatát a beállítás engedélyezéséhez.

## <a name="prerequisites"></a>Előfeltételek

A következő lépésekkel kell kezdenie:

- Rendeljen hozzá prémium szintű Azure Active Directory P1 vagy P2-t tartalmazó licencet a felhasználókhoz.
- Egy Azure Active Directory csoport, amely a felhasználók csoport tagjaként van hozzárendelve.
- Az Azure többtényezős hitelesítés engedélyezése az összes felhasználó számára. Ennek módjáról további információt a [felhasználó kétlépéses ellenőrzésének megkövetelése](../active-directory/authentication/howto-mfa-userstates.md#view-the-status-for-a-user)című témakörben talál.

> [!NOTE]
> A következő beállítás a [Windows rendszerű virtuális asztali webes ügyfélprogramra](https://rdweb.wvd.microsoft.com/arm/webclient/index.html)is érvényes.

## <a name="create-a-conditional-access-policy"></a>Feltételes hozzáférési szabályzat létrehozása

Ebből a témakörből megtudhatja, hogyan hozhat létre többtényezős hitelesítést igénylő feltételes hozzáférési szabályzatot a Windows rendszerű virtuális asztalhoz való csatlakozáskor:

1. Jelentkezzen be a **Azure Portal** globális rendszergazdaként, biztonsági rendszergazdaként vagy feltételes hozzáférést biztosító rendszergazdaként.
2. Keresse meg **Azure Active Directory**  >  **biztonsági**  >  **feltételes hozzáférését**.
3. Válassza az **új szabályzat** lehetőséget.
4. Adjon nevet a szabályzatnak. Javasoljuk, hogy a szervezetek értelmes szabványt hozzanak létre a szabályzatok nevében.
5. A **Hozzárendelések** alatt válassza a **Felhasználók és csoportok** lehetőséget.
6. A **Belefoglalás** területen válassza a **felhasználók és csoportok kiválasztása**  >  **felhasználók és csoportok** lehetőséget > válassza ki az [Előfeltételek](#prerequisites) szakaszban létrehozott csoportot.
7. Válassza a **Kész** lehetőséget.
8. A **Cloud apps vagy a műveletek** területen  >  válassza az **alkalmazások kiválasztása** lehetőséget.
9. Válassza ki a következő alkalmazások egyikét, amely alapján a Windows rendszerű virtuális asztali verziót használja.
   
   - Ha a Windows Virtual Desktopot (klasszikus) használja, válassza a következő alkalmazásokat:
       
       - **Windows rendszerű virtuális asztal** (alkalmazás-azonosító 5a0aa725-4958-4b0c-80a9-34562e23f3b7)
       - **Windows rendszerű virtuális asztali ügyfél** (alkalmazás-azonosító fa4345a4-a730-4230-84a8-7d9651b86739), amely lehetővé teszi szabályzatok beállítását a webes ügyfélen
       
        Ezt követően ugorjon előre a 11. lépésre.

   - Ha a Windows rendszerű virtuális asztalt használja, válassza a következő alkalmazást:
       
       -  **Windows rendszerű virtuális asztal** (alkalmazás-azonosító 9cdead84-A844-4324-93f2-b2e6bb768d07)
       
        Ezt követően folytassa a 10. lépéssel.

   >[!IMPORTANT]
   > Ne válassza ki a Windows rendszerű virtuális asztali Azure Resource Manager Provider (50e95039-B200-4007-bc97-8d5790743a63) nevű alkalmazást. Ez az alkalmazás csak a felhasználói csatorna lekérésére használható, és nem lehet többtényezős hitelesítés.
   > 
   > Ha a Windowsos virtuális asztalt (klasszikus) használja, ha a feltételes hozzáférési házirend blokkolja az összes hozzáférést, és csak a Windows rendszerű virtuális asztali alkalmazás-azonosítókat zárja ki, ezt úgy is megteheti, hogy hozzáadja az alkalmazás-azonosító 9cdead84-A844-4324-93f2-b2e6bb768d07 a szabályzathoz. Ha nem adja hozzá az alkalmazás AZONOSÍTÓját, a Windows rendszerű virtuális asztali (klasszikus) erőforrások hírcsatornáinak felderítését letiltja.

10. Lépjen a **feltételek**  >  **ügyfélalkalmazások** elemre, majd válassza ki, hogy hová szeretné alkalmazni a szabályzatot:
    
    - Válassza a **böngésző** lehetőséget, ha azt szeretné, hogy a házirend a webes ügyfélre vonatkozzon.
    - Válassza a **Mobile apps és asztali ügyfelek** lehetőséget, ha a szabályzatot más ügyfelekre szeretné alkalmazni.
    - Jelölje be mindkét jelölőnégyzetet, ha az összes ügyfélre alkalmazni kívánja a házirendet.
   
    > [!div class="mx-imgBorder"]
    > ![Az ügyfélalkalmazások oldal képernyőképe. A felhasználó bejelölte a Mobile apps és az asztali ügyfelek jelölőnégyzetet.](media/select-apply.png)

11. Miután kiválasztotta az alkalmazást, válassza a **kiválasztás** lehetőséget, majd kattintson a **kész** gombra.

    > [!div class="mx-imgBorder"]
    > ![Képernyőkép a Cloud apps vagy a Actions lapról. A Windows rendszerű virtuális asztali és a Windows rendszerű virtuális asztali ügyfélprogramok piros színnel vannak kiemelve.](media/cloud-apps-enterprise.png)

    >[!NOTE]
    >A kijelölni kívánt alkalmazás AZONOSÍTÓjának megkereséséhez nyissa meg a **vállalati alkalmazások** elemet, és válassza a **Microsoft-alkalmazások** lehetőséget az alkalmazás típusa legördülő menüből.

12. A **hozzáférés-vezérlés**  >  **megadása** területen válassza a **hozzáférés biztosítása**, **többtényezős hitelesítés megkövetelése**, majd a **lehetőséget**.
13. A **hozzáférés-vezérlési**  >  **munkamenet** területen válassza a **bejelentkezési gyakoriság** lehetőséget, állítsa be az értéket a kérések között, majd válassza a **kiválasztás** lehetőséget. Ha például az értéket 1 értékre állítja, és a mértékegységet **(** **óra**), a többtényezős hitelesítést igényel, ha a rendszer az utolsót követően egy órával elindítja a kapcsolódást.
14. Erősítse meg a beállításokat, és állítsa be az engedélyezési **szabályzatot** **bekapcsolva** értékre.
15. Válassza a **Létrehozás** lehetőséget a szabályzat engedélyezéséhez.

>[!NOTE]
>Ha a webes ügyfélprogram használatával jelentkezik be a Windows rendszerű virtuális asztalra a böngészőben, a napló az ügyfélalkalmazás AZONOSÍTÓját a85cf173-4192-42f8-81fa-777a763e6e2c (Windows rendszerű virtuális asztali ügyfélként) listázza. Ennek az az oka, hogy az ügyfélalkalmazás belsőleg kapcsolódik ahhoz a kiszolgálói alkalmazás-AZONOSÍTÓhoz, ahol a feltételes hozzáférési házirend be lett állítva. 

## <a name="next-steps"></a>Következő lépések

- [További információ a feltételes hozzáférési házirendekről](../active-directory/conditional-access/concept-conditional-access-policies.md)

- [További információ a felhasználói bejelentkezés gyakoriságáról](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md#user-sign-in-frequency)
