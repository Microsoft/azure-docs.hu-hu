---
title: A "nem érhető el innen" hibaüzenettel kapcsolatos javítások – Azure AD
description: A "nem érhető el innen" hibaüzenetből megtudhatja, hogyan érheti el a lehetséges javításokat.
services: active-directory
author: curtand
manager: daveba
ms.assetid: 8ad0156c-0812-4855-8563-6fbff6194174
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 10/10/2018
ms.author: curtand
ms.reviewer: jairoc
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: df3941c895ce67862eb53b8e96bc7a6d53c1ed02
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88799417"
---
# <a name="possible-fixes-for-the-you-cant-get-there-from-here-error-message"></a>A "nem érhető el innen" hibaüzenet lehetséges javításai

A szervezet belső webes alkalmazásaihoz vagy szolgáltatásaihoz való hozzáférés **során a következő** hibaüzenet jelenhet meg: Ez az üzenet azt jelenti, hogy a szervezete olyan szabályzatot állított be, amely megakadályozza, hogy az eszköz hozzáférjen a szervezet erőforrásaihoz. Habár előfordulhat, hogy a probléma megoldásához kapcsolatba kell lépnie az ügyfélszolgálattal, íme néhány dolog, amelyet először is kipróbálhat.

## <a name="make-sure-youre-using-a-supported-browser"></a>Győződjön meg arról, hogy támogatott böngészőt használ
Ha úgy látja, hogy **nem tud** innen üzenetet kapni, mondván, hogy nem támogatott böngészőből próbál hozzáférni a szervezet webhelyeihez, ellenőrizze, hogy melyik böngészőt futtatja.

![A böngésző támogatásával kapcsolatos hibaüzenet](media/user-help-device-remediation/browser-version.png)

A probléma megoldásához telepítenie és futtatnia kell egy támogatott böngészőt az operációs rendszere alapján. Ha a Windows 10 rendszert használja, a támogatott böngészők közé tartoznak a Microsoft Edge, az Internet Explorer és a Google Chrome. Ha más operációs rendszert használ, a [támogatott böngészők](../conditional-access/concept-conditional-access-conditions.md#supported-browsers)teljes listáját is megtekintheti.

## <a name="make-sure-youre-using-a-supported-operating-system"></a>Győződjön meg arról, hogy támogatott operációs rendszert használ
Győződjön meg arról, hogy az operációs rendszer támogatott verzióját futtatja, beleértve a következőket:

- **Windows-ügyfél.** Windows 7 vagy újabb.

- **Windows Server.** Windows Server 2008 R2 vagy újabb.

- **MacOS.** macOS X vagy újabb

- **Android és iOS.** Az Android és iOS rendszerű mobil operációs rendszerek legújabb verziója

A probléma megoldásához telepítenie és futtatnia kell egy támogatott operációs rendszert.

## <a name="make-sure-your-device-is-joined-to-your-network"></a>Győződjön meg arról, hogy az eszköz csatlakoztatva van a hálózathoz
Ha a **következő** üzenet jelenik meg, amely azt jelzi, hogy az eszköz nem felel meg a szervezet hozzáférési házirendjének, győződjön meg róla, hogy csatlakoztatta az eszközt a szervezet hálózatához.

![Hibaüzenet, hogy a hálózata van-e](media/user-help-device-remediation/network-version.png)

### <a name="to-check-whether-your-device-is-joined-to-your-network"></a>Annak ellenőrzését, hogy az eszköz csatlakozik-e a hálózathoz
1. Jelentkezzen be a Windowsba munkahelyi vagy iskolai fiókjával. Például: alain@contoso.com.

2. Kapcsolódjon a szervezet hálózatához egy virtuális magánhálózat (VPN) vagy a DirectAccess használatával.

3. A csatlakozás után a **Windows billentyű + L** billentyű lenyomásával zárolhatja az eszközt.

4. Oldja fel az eszközét munkahelyi vagy iskolai fiókjával, majd próbálja meg újra elérni a problémás alkalmazást vagy szolgáltatást.

    Ha úgy látja, hogy **nem tud többé ide-** hibaüzenetet kapni, válassza a **További részletek** hivatkozást, majd lépjen kapcsolatba az ügyfélszolgálattal a részletekkel.

### <a name="to-join-your-device-to-your-network"></a>Az eszköz csatlakoztatása a hálózathoz
Ha az eszköz nincs csatlakoztatva a szervezet hálózatához, akkor két dolog közül választhat:

- **Csatlakoztassa munkahelyi eszközét.** Csatlakoztassa a munkahelyi Windows 10-es eszközét a szervezet hálózatához, hogy hozzáférhessen a potenciálisan korlátozott erőforrásokhoz. További információt és részletes útmutatást a [munkahelyi eszköz csatlakoztatása a szervezet hálózatához](user-help-join-device-on-network.md)című témakörben talál.

- **Regisztrálja a személyes eszközét a munkához.** Regisztrálja személyes eszközét (jellemzően telefon vagy Tablet) a szervezet hálózatán. Az eszköz regisztrálása után hozzáférhet a szervezet korlátozott erőforrásaihoz. További információk és részletes útmutatás: [személyes eszköz regisztrálása a szervezet hálózatán](user-help-register-device-on-network.md).

## <a name="next-steps"></a>Következő lépések
- [Mi a MyApps-portál?](./my-apps-portal-end-user-access.md)

- [Bejelentkezés telefonnal a jelszó helyett](user-help-auth-app-sign-in.md)