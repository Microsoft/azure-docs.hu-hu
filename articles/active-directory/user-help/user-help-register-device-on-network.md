---
title: Személyes eszközök regisztrálása a szervezet hálózatán – Azure AD
description: Ismerje meg, hogyan regisztrálhatja személyes eszközét a szervezet hálózatán, hogy hozzáférhessen a szervezet védett erőforrásaihoz.
services: active-directory
author: curtand
manager: daveba
ms.assetid: 54e1b01b-03ee-4c46-bcf0-e01affc0419d
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 08/31/2020
ms.author: curtand
ms.reviewer: jairoc
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: 0435b99525c34eb72d7cc5315ccb4359859cd528
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "90033047"
---
# <a name="register-your-personal-device-on-your-organizations-network"></a>Személyes eszköz regisztrálása a szervezet hálózatán
Regisztrálja személyes eszközét (jellemzően telefon vagy tabletta) a szervezet hálózatán. Az eszköz regisztrálása után hozzáférhet a szervezet korlátozott erőforrásaihoz.

>[!Note]
>Ez a cikk a Windows-eszközt bemutató célokra használja, de iOS, Android vagy macOS rendszerű eszközöket is regisztrálhat.

## <a name="what-happens-when-you-register-your-device"></a>Mi történik az eszköz regisztrálásakor?
Az eszköz a szervezet hálózatán való regisztrálása közben a következő műveletek történnek:

- A Windows regisztrálja az eszközt a szervezet hálózatán.

- A szervezete által választott lehetőségek alapján előfordulhat, hogy a [Kéttényezős hitelesítés](multi-factor-authentication-end-user-first-time.md) vagy a [biztonsági információ](./security-info-setup-signin.md)segítségével kétlépéses ellenőrzést kell beállítania.

- A szervezete által választott lehetőségek alapján előfordulhat, hogy automatikusan regisztrálva van a mobileszköz-kezelésben, például Microsoft Intune. További információ a Microsoft Intune való regisztrálásáról: [eszköz regisztrálása az Intune-ban](/intune-user-help/enroll-your-device-in-intune-all).

- A bejelentkezési folyamatot a munkahelyi vagy iskolai fiókjához tartozó felhasználónévvel és jelszóval végezheti el.

## <a name="to-register-your-windows-device"></a>Windows-eszköz regisztrálása

Az alábbi lépéseket követve regisztrálja a személyes eszközét a hálózaton.

1. Nyissa meg a **Beállítások**, majd a **fiókok** elemet.

    ![Fiókok a beállítások képernyőn](./media/user-help-register-device-on-network/register-device-settings-accounts.png)

2. Válassza a **hozzáférés munkahelyi** vagy iskolai rendszerhez lehetőséget, majd válassza a **Kapcsolódás** a **munkahelyi vagy iskolai** képernyőről lehetőséget.

    ![Hozzáférés munkahelyi vagy iskolai képernyőhöz a kapcsolódási lehetőség kijelölve](./media/user-help-register-device-on-network/register-device-access-work-school-connect.png)

3. A **munkahelyi vagy iskolai fiók hozzáadása** képernyőn írja be a munkahelyi vagy iskolai fiókjához tartozó e-mail-címét, majd kattintson a **tovább** gombra. Például: alain@contoso.com.

4. Jelentkezzen be a munkahelyi vagy iskolai fiókjába, majd válassza **a bejelentkezés** lehetőséget.

5. Fejezze be a regisztrációs folyamat hátralévő részét, beleértve a személyazonosság-ellenőrzési kérelem jóváhagyását (ha kétlépéses ellenőrzést használ) és a Windows Hello (ha szükséges) beállítását.

## <a name="to-verify-that-youre-registered"></a>Annak ellenőrzése, hogy regisztrálva van-e
Győződjön meg arról, hogy regisztrálta a beállításait.

1. Nyissa meg a **Beállítások**, majd a **fiókok** elemet.

    ![Fiókok a beállítások képernyőn](./media/user-help-register-device-on-network/register-device-settings-accounts.png)

2. Válassza a **hozzáférés munkahelyi vagy iskolai** rendszerhez lehetőséget, és győződjön meg róla, hogy a munkahelyi vagy iskolai fiókja látható.

    ![Munkahelyi vagy iskolai képernyő elérése csatlakoztatott contoso-fiókkal](./media/user-help-register-device-on-network/register-device-setup-verify.png)

## <a name="next-steps"></a>Következő lépések
Miután regisztrálta a személyes eszközét a szervezet hálózatán, el kell tudnia érni a legtöbb erőforrást.

- Ha a szervezete szeretne csatlakozni a munkahelyi eszközhöz, tekintse [meg a munkahelyi eszköz csatlakoztatása a szervezet hálózatához](user-help-join-device-on-network.md)című témakört.