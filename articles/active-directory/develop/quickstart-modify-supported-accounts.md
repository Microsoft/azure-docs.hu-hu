---
title: 'Gyors útmutató: alkalmazás által támogatott fióktípus módosítása | Azure'
titleSuffix: Microsoft identity platform
description: Ebben a rövid útmutatóban a Microsoft Identity platformmal regisztrált alkalmazást konfigurálhatja, hogy megváltoztassa, kik vagy milyen fiókok férhessenek hozzá az alkalmazáshoz.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 10/27/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: marsma, aragra, lenalepa, sureshja
ms.openlocfilehash: 2382eedcc14f683d354b88bf2eb8d53b2af40dbd
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93083269"
---
# <a name="quickstart-modify-the-accounts-supported-by-an-application"></a>Gyors útmutató: alkalmazás által támogatott fiókok módosítása

Ha az alkalmazást a Microsoft Identity platformmal regisztrálta, akkor meg kell határoznia, hogy ki és milyen fióktípus esetén férhet hozzá. Előfordulhat például, hogy csak a szervezetében adta meg a fiókokat, amely *egyetlen bérlős* alkalmazás. Vagy bármilyen szervezetnél (beleértve a tiéd) is megadhatja a fiókokat, amely egy *több-bérlős* alkalmazás.

Ebből a rövid útmutatóból megtudhatja, hogyan módosíthatja az alkalmazás konfigurációját, hogy megváltoztassa, kik vagy milyen típusú fiókok férhessenek hozzá az alkalmazáshoz.

## <a name="prerequisites"></a>Előfeltételek

* A rövid útmutató befejezése [: alkalmazás regisztrálása a Microsoft Identity platformon](quickstart-register-app.md)

## <a name="change-the-application-registration-to-support-different-accounts"></a>Alkalmazásregisztráció módosítása különböző fiókok támogatásának engedélyezéséhez

Egy meglévő alkalmazás-regisztráció által támogatott fióktípus eltérő beállításának megadásához:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Ha több bérlőhöz fér hozzá, a felső menüben a **könyvtár + előfizetés** szűrő használatával :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: válassza ki azt a bérlőt, amelyben regisztrálni kíván egy alkalmazást.
1. Keresse meg és válassza ki az **Azure Active Directoryt** .
1. A **kezelés** területen válassza a **Alkalmazásregisztrációk** lehetőséget, majd válassza ki az alkalmazást.
1. Most határozza meg, hogy ki használhatja az alkalmazást, más néven a *bejelentkezési célközönségnek* .

    | Támogatott fióktípusok | Leírás |
    |-------------------------|-------------|
    | **Csak az ebben a szervezeti címtárban található fiókok** | Akkor válassza ezt a lehetőséget, ha olyan alkalmazást hoz létre, amelyet csak felhasználók (vagy vendégek) használhatnak *a* bérlőben.<br><br>Gyakran *üzletági (LOB* ) alkalmazásnak nevezik, ez egy **egybérlős** alkalmazás a Microsoft Identity platformon. |
    | **Tetszőleges szervezeti címtárban található fiókok** | Akkor válassza ezt a lehetőséget, ha *bármely* Azure ad-bérlő felhasználói számára szeretné használni az alkalmazást. Ez a beállítás akkor lehet hasznos, ha például olyan szoftveres (SaaS) alkalmazást készít, amelyet több szervezet számára szeretne biztosítani.<br><br>Ezt nevezzük **több-bérlős** alkalmazásnak a Microsoft Identity platformon. |
1. Válassza a **Mentés** lehetőséget.

### <a name="why-changing-to-multi-tenant-can-fail"></a>Miért sikertelen a több-bérlős váltás

Az alkalmazások regisztrációjának egyetlen – több bérlőre való váltása esetenként meghiúsulhat az alkalmazás-azonosító URI-ja (az alkalmazás-azonosító URI-ja) ütközése miatt. Példa az alkalmazás-azonosító URI-ra `https://contoso.onmicrosoft.com/myapp` .

Az alkalmazásazonosító URI egy módszer, amellyel az alkalmazás a protokollüzenetekben azonosítható. Egybérlős alkalmazások esetében az alkalmazás AZONOSÍTÓjának URI-ja csak a bérlőn belüli egyedi lehet. Több-bérlős alkalmazás esetében globálisan egyedinek kell lennie, hogy az Azure AD az összes bérlőn megtalálja az alkalmazást. A globális egyediség kikényszeríthető azáltal, hogy az alkalmazás-azonosító URI állomásneve az egyik Azure AD-bérlő [ellenőrzött közzétevői tartományának](howto-configure-publisher-domain.md)felel meg.

Ha például a bérlő neve *contoso.onmicrosoft.com* , akkor `https://contoso.onmicrosoft.com/myapp` egy érvényes alkalmazás-azonosító URI. Ha a bérlője *contoso.com* ellenőrzött tartománya van, akkor érvényes alkalmazásspecifikus URI azonosító is lesz `https://contoso.com/myapp` . Ha az alkalmazás AZONOSÍTÓjának URI-ja nem követi a második mintát, `https://contoso.com/myapp` az alkalmazás regisztrációjának átalakítása több-bérlőre sikertelen.

Az ellenőrzött közzétevők tartományának konfigurálásával kapcsolatos további információkért lásd: [ellenőrzött tartomány konfigurálása](quickstart-modify-supported-accounts.md).

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Útmutató: az alkalmazás átalakítása több-bérlős környezetbe](howto-convert-app-to-be-multi-tenant.md)
