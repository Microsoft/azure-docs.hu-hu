---
title: A munkahelyi vagy iskolai fiók hozzáadása a Microsoft Authenticator alkalmazás – az Azure Active Directory |} A Microsoft Docs
description: A munkahelyi vagy iskolai fiók hozzáadása a Microsoft Authenticator alkalmazást a kétfaktoros hitelesítési módját.
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: conceptual
ms.date: 01/24/2019
ms.author: lizross
ms.reviewer: olhaun
ms.collection: M365-identity-device-management
ms.openlocfilehash: 231f3c44344f4119bab6e1efb5546e6fded0c784
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60473871"
---
# <a name="add-your-work-or-school-account"></a>A munkahelyi vagy iskolai fiók hozzáadása
Ha a szervezet kétfaktoros ellenőrzési használja, beállíthatja a munkahelyi vagy iskolai fiók használata a Microsoft Authenticator alkalmazás egy, az ellenőrzési módszerek sorát.

>[!Important]
>A fiók hozzáadása előtt le kell töltenie és telepítse a Microsoft Authenticator alkalmazást. Ha még nem tette, hogy, kövesse a [töltse le és telepítse az alkalmazást](user-help-auth-app-download-install.md) cikk.

## <a name="add-your-work-or-school-account"></a>A munkahelyi vagy iskolai fiók hozzáadása

1. A számítógépen nyissa meg a [további biztonsági ellenőrzés](https://aka.ms/mfasetup) lapot.

    >[!Note]
    >Ha nem látja a **további biztonsági ellenőrzés** lap, akkor lehetséges, hogy a rendszergazda rendelkezik-e kapcsolva a security info (előzetes verzió) felhasználói élményt. Ha ez a helyzet, kövesse az utasításokat a [állítsa be a biztonsági adatok egy hitelesítő alkalmazást használandó](security-info-setup-auth-app.md) szakaszban. Ha nem, hogy az eset, szüksége lesz a szervezetben Help Desk kapcsolatba segítségért. További információ a biztonsági adatok: [Security info (előzetes verzió) – áttekintés](user-help-security-info-overview.md).

2. Jelölje be a jelölőnégyzetet a **hitelesítő alkalmazás**, majd válassza ki **konfigurálása**.

    A **mobilalkalmazás konfigurálása** lap jelenik meg.
    
    ![Képernyő, amely a QR-kódot tartalmaz](./media/user-help-auth-app-download-install/auth-app-barcode.png)

3. Nyissa meg a Microsoft Authenticator alkalmazást, jelölje be **fiók hozzáadása** a a **testreszabása és vezérlési** ikonra a jobb felső sarokban lévő, majd válassza ki **munkahelyi vagy iskolai fiók**.

4. A QR-kód az eszköz kamerájának használata a **mobilalkalmazás konfigurálása** képernyőn a számítógépen, és válassza a **kész**.

    >[!Note]
    >Ha a kamera nem tudja rögzíteni a QR-kódot, manuálisan is hozzáadhat a fiók adatait a Microsoft Authenticator alkalmazást a kétfaktoros ellenőrzéshez. További információ és menete: [manuálisan adja hozzá a fiókját](user-help-auth-app-add-account-manual.md).

5. Tekintse át a **fiókok** képernyőn az alkalmazás az eszközön, győződjön meg arról, hogy a fiók jobbra, és hogy van-e egy kapcsolódó 6 jegyű ellenőrző kódot. A fokozott biztonság érdekében az ellenőrző kód módosítása megakadályozza, hogy valaki a kód többször használatával 30 másodpercenként.

    ![fiókok képernyő](./media/user-help-auth-app-download-install/auth-app-accounts.png)

## <a name="next-steps"></a>További lépések

- Miután hozzáadta a fiókok az alkalmazáshoz, bejelentkezhet az Authenticator alkalmazás használatával az eszközén. További információkért lásd: [jelentkezzen be az alkalmazás](user-help-auth-app-sign-in.md).

- IOS rendszerű eszközökhöz is készíthető a fiók hitelesítő adatait, és a felhőhöz kapcsolódó alkalmazás beállításait, például a fiókok sorrendjét. További információkért lásd: [biztonsági mentés és helyreállítás a Microsoft Authenticator alkalmazás](user-help-auth-app-backup-recovery.md).
