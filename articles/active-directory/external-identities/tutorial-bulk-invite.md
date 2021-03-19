---
title: Oktatóanyag a B2B együttműködési felhasználók tömeges meghívásához – Azure AD
description: Ebben az oktatóanyagban megismerheti, hogyan küldhet az Azure AD B2B együttműködés külső felhasználói számára tömeges meghívókat a PowerShell és egy CSV-fájl használatával.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: tutorial
ms.date: 03/17/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 01deae46c442fc95c6aead0f11de929f47163c3c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104586557"
---
# <a name="tutorial-bulk-invite-azure-ad-b2b-collaboration-users"></a>Oktatóanyag: Azure AD B2B együttműködési felhasználók tömeges meghívása

Ha Azure Active Directory (Azure AD) B2B együttműködéssel dolgozik együtt külső partnerekkel, egyszerre több vendégfelhasználót meghívhat a szervezetébe. Ebből az oktatóanyagból megtudhatja, hogyan küldhet tömeges meghívásokat a külső felhasználóknak a Azure Portal használatával. A következőket fogja elvégezni:

> [!div class="checklist"]
> * A **felhasználók tömeges meghívásával** készítse el a vesszővel tagolt (. csv) fájlt a felhasználói adatok és a Meghívási beállítások használatával.
> * Töltse fel a. csv fájlt az Azure AD-be
> * A felhasználók címtárhoz való hozzáadásának ellenőrzése

Ha nincs Azure Active Directory, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a Kezdés előtt.

## <a name="understand-the-csv-template"></a>A CSV-sablon megismerése

Töltse le és töltse ki a CSV-fájl tömeges feltöltése sablont, amely segítséget nyújt az Azure AD vendég felhasználóinak tömeges meghívásához. A letöltött CSV-sablon a következő példához hasonló lehet:

![Táblázat a feltöltéshez és a híváshoz – az egyes sorok és oszlopok céljának és értékeinek ismertetése](media/tutorial-bulk-invite/understand-template.png)

### <a name="csv-template-structure"></a>CSV-sablon szerkezete

A letöltött CSV-sablon sorai a következők:

- **Verziószám**: a verziószámot tartalmazó első sornak szerepelnie kell a CSV-fájl feltöltésekor.
- **Oszlopfejlécek**: az oszlopfejlécek formátuma a (z &lt;  &gt; ) [PropertyName] elemnév &lt; *kötelező vagy üres* &gt; . Például: `Email address to invite [inviteeEmail] Required`. Előfordulhat, hogy a sablon néhány régebbi verziója némileg eltérő változatot tartalmaz.
- **Példák sora**: a sablonban szerepel az egyes oszlopok értékeire mutató példák sora. El kell távolítania a példákat tartalmazó sort, és le kell cserélnie a saját bejegyzéseire.

### <a name="additional-guidance"></a>További útmutatás

- A feltöltési sablon első két sora nem távolítható el és nem módosítható, vagy a feltöltés nem dolgozható fel.
- Először a szükséges oszlopok szerepelnek.
- Nem ajánlott új oszlopokat hozzáadni a sablonhoz. A hozzáadott további oszlopokat a rendszer figyelmen kívül hagyja, és nem dolgozza fel.
- Javasoljuk, hogy a lehető legtöbbször töltse le a CSV-sablon legújabb verzióját.

## <a name="prerequisites"></a>Előfeltételek

Szüksége lesz kettő vagy több e-mail-tesztfiókra, ahova a meghívókat küldheti. A fiókoknak a szervezeten kívüli kell lenniük. Bármilyen típusú fiókot használhat, így közösségi fiókokat is, például gmail.com vagy outlook.com végződésűeket.

## <a name="invite-guest-users-in-bulk"></a>Vendég felhasználóinak meghívása ömlesztve

1. Jelentkezzen be a Azure Portalba egy olyan fiókkal, amely a szervezet globális rendszergazdája.
2. A navigációs ablaktáblán válassza a **Azure Active Directory** lehetőséget.
3. A **kezelés** területen válassza a **minden felhasználó** lehetőséget.
4. Válassza a **tömeges műveletek**  >  **tömeges meghívása** lehetőséget.

    ![Tömeges meghívás gomb](media/tutorial-bulk-invite/bulk-invite-button.png)

4. A **felhasználók tömeges meghívása** lapon válassza a **Letöltés** lehetőséget, hogy érvényes. csv sablont Kérjen a Meghívási tulajdonságokkal.

     ![A CSV-fájl letöltése](media/tutorial-bulk-invite/download-button.png)

1. Nyissa meg a. csv-sablont, és adjon hozzá egy sort minden vendég felhasználóhoz. A szükséges értékek a következők:

   * **Meghívó e-mail-cím** – a meghívót kérő felhasználó

   * **Átirányítási URL** -cím – az az URL-cím, amelyre a meghívott felhasználó továbbítva lett a meghívó elfogadása után. Ha továbbítani szeretné a felhasználót a saját alkalmazások lapra, ezt az értéket a vagy a értékre kell módosítania https://myapps.microsoft.com https://myapplications.microsoft.com .

    ![Példa a vendég felhasználókat tartalmazó CSV-fájlra](media/tutorial-bulk-invite/bulk-invite-csv.png)

   > [!NOTE]
   > Ne használjon vesszőket a **testreszabott Meghívási üzenetben** , mert megakadályozza, hogy az üzenet elemzése sikeres legyen.

6. Mentse a fájlt.
7. A **felhasználók tömeges meghívása** lapon a **CSV-fájl feltöltése** területen keresse meg a fájlt. A fájl kiválasztásakor elindul a. csv-fájl érvényesítése. 
8. A fájl tartalmának ellenőrzésekor a **fájl feltöltése sikeresen** megtörténik. Ha hibák léptek fel, ezeket a feladatok elküldése előtt ki kell javítania.
9. Ha a fájl érvényesíti az ellenőrzést, válassza a **Submit (Küldés** ) lehetőséget a meghívókat felvenni kívánó Azure tömeges művelet elindításához. 
10. A feladatok állapotának megtekintéséhez válassza a **kattintson ide az egyes műveletek állapotának megtekintéséhez**. Vagy kiválaszthatja a **tömeges művelet eredményeit** is a **tevékenység** szakaszban. A tömeges művelet minden egyes sorával kapcsolatos részletekért jelölje ki az értékeket a **# sikeres**, **# sikertelen** vagy az **összes kérelem** oszlopban. Ha hiba történt, a hiba okai lesznek felsorolva.

    ![Példa tömeges művelet eredményeire](media/tutorial-bulk-invite/bulk-operation-results.png)

11. Amikor a feladatok befejeződik, megjelenik egy értesítés arról, hogy a tömeges művelet sikeresen befejeződött.

## <a name="verify-guest-users-in-the-directory"></a>Vendég felhasználók ellenőrzése a címtárban

Ellenőrizze, hogy a hozzáadott vendég felhasználók szerepelnek-e a könyvtárban a Azure Portal vagy a PowerShell használatával.

### <a name="view-guest-users-in-the-azure-portal"></a>Vendég felhasználók megtekintése a Azure Portalban

1. Jelentkezzen be a Azure Portalba egy olyan fiókkal, amely a szervezet felhasználói rendszergazdája.
2. A navigációs ablaktáblán válassza a **Azure Active Directory** lehetőséget.
3. A **Kezelés** menüpontban válassza a **Felhasználók** lehetőséget.
4. A **Megjelenítés** területen válassza ki a **csak vendég felhasználók** elemet, és ellenőrizze, hogy a hozzáadott felhasználók szerepelnek-e a listáján.

### <a name="view-guest-users-with-powershell"></a>Vendég felhasználók megtekintése a PowerShell-lel

Futtassa az alábbi parancsot:

```powershell
 Get-AzureADUser -Filter "UserType eq 'Guest'"
```

A meghívott felhasználókat a következő formátumban kell megjelennie: *emailaddress*#EXT # \@ *tartomány*. Például *lstokes_fabrikam. com # ext # \@ contoso.onmicrosoft.com*, ahol a contoso.onmicrosoft.com az a szervezet, amelyről elküldötte a meghívókat.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölheti a felhasználói fiókokat a Azure Portal a felhasználók lapon, ha bejelöli a vendég felhasználó melletti jelölőnégyzetet, majd kiválasztja a **Törlés** lehetőséget. 

Vagy futtathatja a következő PowerShell-parancsot egy felhasználói fiók törléséhez:

```powershell
 Remove-AzureADUser -ObjectId "<UPN>"
```

Például: `Remove-AzureADUser -ObjectId "lstokes_fabrikam.com#EXT#@contoso.onmicrosoft.com"`

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban tömeges meghívókat küldött szervezeten kívüli vendégfelhasználóknak. A következő szakaszban megtudhatja, hogyan működik a meghívások érvényesítési folyamata.

> [!div class="nextstepaction"]
> [További információ az Azure AD B2B együttműködés meghívóérvényesítési folyamatáról](redemption-experience.md)
