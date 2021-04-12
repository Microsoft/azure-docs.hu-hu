---
title: Azure AD-alkalmazások hozzáadása és kezelése – Azure Marketplace
description: Ismerje meg, hogyan adhat hozzá és kezelhet Azure AD-alkalmazásokat a kereskedelmi piactér programhoz a partner Centerben.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.date: 04/06/2021
author: varsha-sarah
ms.author: vavargh
ms.custom: contperf-fy21q2
ms.openlocfilehash: be527647466ad76455585e16baabb26e39e42193
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2021
ms.locfileid: "107108348"
---
# <a name="add-and-manage-azure-ad-applications"></a>Azure AD-alkalmazások hozzáadása és kezelése

**Megfelelő szerepkörök**

- Tulajdonos
- Manager

Engedélyezheti a vállalat Azure Active Directory (Azure AD) részét képező alkalmazásokat vagy szolgáltatásokat a partner Center-fiók eléréséhez.

## <a name="add-existing-azure-ad-applications"></a>Meglévő Azure AD-alkalmazások hozzáadása

A vállalat Azure Active Directory már meglévő alkalmazások hozzáadásához:

1. A **felhasználók** lapon (a **Fiókbeállítások** alatt) válassza az **Azure ad-alkalmazások hozzáadása** lehetőséget.
1. Válasszon ki egy vagy több Azure AD-alkalmazást a megjelenő listából. A keresőmező segítségével megkeresheti az adott Azure AD-alkalmazásokat. Ha egynél több Azure AD-alkalmazást választ ki a partner Center-fiókhoz való hozzáadáshoz, akkor ugyanazt a szerepkört vagy egyéni engedélyeket kell rendelnie. Ha több Azure AD-alkalmazást szeretne hozzáadni különböző szerepkörökhöz vagy engedélyekhez, ismételje meg ezeket a lépéseket az egyes szerepkörökhöz vagy egyéni engedélyek készletéhez.
1. Ha végzett az Azure AD-alkalmazások kiválasztásával, válassza a **kijelölt hozzáadása** elemet.
1. A **szerepkörök** szakaszban határozza meg a kijelölt Azure ad-alkalmazás (ok) szerepkörét vagy testreszabott engedélyeit.
1. Kattintson a **Mentés** gombra.

## <a name="add-new-azure-ad-applications"></a>Új Azure AD-alkalmazások hozzáadása

Ha egy új Azure AD-alkalmazáshoz szeretne hozzáférést adni a partneri Központ számára, létrehozhat egyet a **felhasználók** szakaszban. Ezzel létrehoz egy új fiókot a munkahelyi munkafiókjában (Azure AD-bérlő), nem csupán a partner Center-fiókban. Ha elsődlegesen ezt az Azure AD-alkalmazást használja a fiókpartner-hitelesítéshez, és nincs szükség arra, hogy a felhasználók közvetlenül hozzáférjenek, megadhat bármely érvényes címet a **Válasz URL** -címéhez és az **alkalmazás-azonosító URI azonosítóhoz**, feltéve, hogy ezeket az értékeket nem használja más Azure ad-alkalmazás a címtárában.

1. A **felhasználók** lapon (a **Fiókbeállítások** alatt) válassza az **Azure ad-alkalmazások hozzáadása** lehetőséget.
1. A következő lapon válassza az **új Azure ad-alkalmazás** lehetőséget.
1. Adja meg az új Azure AD **-alkalmazás válaszának URL-címét** . Ez az URL-cím, ahol a felhasználók bejelentkezhetnek és használhatják az Azure AD-alkalmazást (más néven az alkalmazás URL-címe vagy Sign-On URL-cím). A *Válasz URL-címe* nem lehet hosszabb 256 karakternél, és a címtárban egyedinek kell lennie.
1. Adja meg az új Azure AD-alkalmazáshoz tartozó **alkalmazás-azonosító URI** -t. Ez az Azure AD-alkalmazáshoz tartozó logikai azonosító, amely akkor jelenik meg, ha egy egyszeri bejelentkezésre vonatkozó kérést küld az Azure AD-nek. Az *alkalmazás-azonosító URI* azonosítójának egyedinek kell lennie minden egyes Azure ad-alkalmazáshoz a címtárban. Ez az azonosító nem lehet hosszabb 256 karakternél. Az alkalmazás-azonosító URI-val kapcsolatos további információkért lásd: [alkalmazások integrálása a Azure Active Directorysal](/azure/active-directory/develop/quickstart-modify-supported-accounts.md#change-the-application-registration-to-support-different-accounts).
1. A **szerepkörök** szakaszban megadhatja az Azure ad-alkalmazáshoz tartozó szerepkör (eke) t vagy testreszabott engedélyeket.
1. Kattintson a **Mentés** gombra.

Az Azure AD-alkalmazás hozzáadása vagy létrehozása után visszatérhet a **felhasználók** szakaszhoz, és kiválaszthatja az alkalmazás nevét az alkalmazás beállításainak áttekintéséhez, beleértve a bérlő azonosítóját, az ügyfél-azonosítót, a válasz URL-címét és az alkalmazás-azonosító URI-t.

## <a name="remove-an-azure-ad-application"></a>Azure AD-alkalmazás eltávolítása

Ha el szeretne távolítani egy alkalmazást a munkahelyi fiókjából (Azure AD-bérlőből), lépjen a **felhasználók** (a **Fiókbeállítások** alatt) lehetőségre, válassza ki azt az alkalmazást, amelyet el szeretne távolítani a jobb szélső oszlopban található jelölőnégyzettel, majd válassza az **Eltávolítás** lehetőséget az elérhető műveletek közül. Megjelenik egy előugró ablak, amely megerősíti, hogy el kívánja távolítani a kijelölt alkalmazás (oka) t.

## <a name="manage-keys-for-an-azure-ad-application"></a>Azure AD-alkalmazás kulcsainak kezelése

Ha az Azure AD-alkalmazás adatokat olvas és ír a Microsoft Azure ADban, szüksége lesz egy kulcsra. Az Azure AD-alkalmazásokhoz kulcsokat hozhat létre a partner Centerben található információk szerkesztésével. A már nem szükséges kulcsok eltávolítására is lehetőség van.

1. A **felhasználók** lapon (a **Fiókbeállítások** alatt) válassza ki az Azure ad-alkalmazás nevét. Ekkor megjelenik az Azure AD-alkalmazás összes aktív kulcsa, beleértve a kulcs létrehozásának dátumát és a 50-os lejárati időpontot is.
1. A már nem szükséges kulcsok eltávolításához válassza az **Eltávolítás** lehetőséget.
1. Új kulcs hozzáadásához válassza az **új kulcs hozzáadása** lehetőséget.
1. Ekkor megjelenik egy képernyő, amely az **ügyfél-azonosítót** és a **kulcs értékeit** mutatja. Ügyeljen arra, hogy nyomtassa ki vagy másolja ezeket az adatokat, mert a lap elhagyása után többé nem fog tudni hozzáférni.
1. Ha további kulcsokat szeretne létrehozni, válassza a **másik kulcs hozzáadása** elemet.
