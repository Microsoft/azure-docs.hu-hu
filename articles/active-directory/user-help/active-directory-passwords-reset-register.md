---
title: Hitelesítő adatok regisztrálása a saját jelszavának alaphelyzetbe állításához – Azure AD
description: Regisztrálja az Azure AD önkiszolgáló jelszó-visszaállítási szolgáltatásának ellenőrzési módszerének adatait, így rendszergazdai segítség nélkül állíthatja vissza saját jelszavát.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: sahenry
ms.assetid: ''
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: end-user-help
ms.date: 05/28/2020
ms.author: curtand
ms.openlocfilehash: e460f3fd383286b1e61a979b87a9dfa09f272313
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "88799026"
---
# <a name="register-your-verification-method-info-to-reset-your-own-password"></a>A saját jelszavának alaphelyzetbe állításához regisztrálja az ellenőrzési módszer adatait

Ha elfelejtette a munkahelyi vagy iskolai jelszavát, soha nem kapott jelszót a szervezettől, vagy kizárták a fiókjából, a biztonsági adatok és a mobileszköz segítségével alaphelyzetbe állíthatja a munkahelyi vagy iskolai jelszavát.

A rendszergazdának be kell kapcsolnia ezt a funkciót, hogy regisztrálni tudja az adatait, és visszaállítson egy saját jelszót. Ha nem látja az **elfelejtettem a jelszavam** beállítást, az azt jelenti, hogy a rendszergazda nem kapcsolta be a szolgáltatást a szervezet számára. Ha úgy véli, hogy ez helytelen, segítségért forduljon az ügyfélszolgálathoz.

>[!Important]
>Ez a cikk azoknak a felhasználóknak készült, akik a regisztrációt szeretnék használni az önkiszolgáló jelszó-visszaállításra. Ez azt jelenti, hogy a alain@contoso.com rendszergazda segítségének megkövetelése nélkül állíthatja alaphelyzetbe a saját munkahelyi vagy iskolai jelszavát (például). Ha Ön rendszergazda, aki az alkalmazottak vagy más felhasználók önkiszolgáló jelszó-visszaállításának bekapcsolásával kapcsolatos információkat keres, tekintse meg az [Azure ad önkiszolgáló jelszó-visszaállítás üzembe helyezése és egyéb cikkek](../authentication/howto-sspr-deployment.md)témakört.

## <a name="set-up-your-password-reset-verification-method"></a>A jelszó-visszaállítás ellenőrzési módszerének beállítása

1. Nyissa meg a webböngészőt az eszközön, és lépjen a [biztonsági adatok lapra](https://account.activedirectory.windowsazure.com/PasswordReset/Register.aspx?regref=ssprsetup).

2. Attól függően, hogy a rendszergazda hogyan állította be a szervezetét, a következő lehetőségek közül egy vagy több elérhető lesz a biztonsági ellenőrzési módszerként való beállításhoz. Ha több lehetőség is rendelkezésre áll, javasoljuk, hogy a biztonsági ellenőrzési módszernek megfelelően egynél több lehetőséget használjon, ha az egyik módszer elérhetetlenné válik.

    - **Hitelesítési alkalmazás.** Válassza ki a Microsoft Authenticator alkalmazást vagy más hitelesítő alkalmazást a biztonsági ellenőrzési módszerként. Az alkalmazás beállításával kapcsolatos további információkért lásd: [az Microsoft Authenticator alkalmazás beállítása ellenőrzési módszerként](security-info-setup-auth-app.md).

    - **Szöveges üzenetküldés.** Válassza a szöveges üzenetek küldését a mobileszközön. A szöveges üzenetkezelés beállításával kapcsolatos további információkért lásd: [SMS-üzenetek beállítása ellenőrzési módszerként](security-info-setup-text-msg.md).

    - **Telefonhívások.** Válassza ki, hogy telefonhívást kap a regisztrált telefonszámára. A telefonhívások beállításával kapcsolatos további információkért tekintse [meg a telefonszám beállítása ellenőrzési módszerként](security-info-setup-phone-number.md)című témakört.

    - **Biztonsági kulcs.** Válassza a Microsoft-kompatibilis biztonsági kulcs használatát. További információ: [biztonsági kulcs beállítása ellenőrzési módszerként](security-info-setup-security-key.md).

    - **E-mail-cím.** Alternatív e-mail-címet is használhat, amely az elfelejtett vagy hiányzó jelszó megkövetelése nélkül használható. Ez csak a jelszó-visszaállításra használható, nem biztonsági ellenőrzési módszerként. Az e-mail-címek beállításával kapcsolatos további információkért tekintse [meg az e-mail-cím beállítása ellenőrzési módszerként](security-info-setup-email.md)című témakört.

    - **Biztonsági kérdések.** A rendszergazda által beállított, előre meghatározott biztonsági kérdések beállításához és megválaszolásához válassza a lehetőséget. Ez csak a jelszó-visszaállításra használható, nem biztonsági ellenőrzési módszerként. A biztonsági kérdésekkel kapcsolatos további információkért tekintse meg [a biztonsági kérdések beállítása ellenőrzési módszerként](security-info-setup-questions.md)című témakört.

3. A módszerek kiválasztása és beállítása után a **Befejezés** gombra kattintva fejezze be a folyamatot.

    > [!Note]
    > A telefonszámhoz vagy e-mail-címéhez hozzáadott adatokat a rendszer nem osztja meg a szervezet globális címtárával. Ezek az adatok csak az Ön és a rendszergazda számára láthatók. Csak a biztonsági kérdésekre adott válaszok láthatók.

## <a name="common-problems-and-their-solutions"></a>Gyakori problémák és megoldásaik

 Íme néhány gyakori hiba eset és megoldásuk:

| Hibaüzenet |  Lehetséges megoldás |
| --- | --- | --- |
| Forduljon a rendszergazdához.<br>A rendszer azt észlelte, hogy a felhasználói fiók jelszavát nem a Microsoft felügyeli. Ennek eredményeképpen nem lehet automatikusan alaphelyzetbe állítani a jelszavát.<br>További segítségért vegye fel a kapcsolatot az informatikai részleggel.| Ha ezt a hibaüzenetet a felhasználói azonosító beírása után kapja meg, az azt jelenti, hogy a szervezet belsőleg kezeli a jelszavát, és nem szeretné, hogy alaphelyzetbe állítsa a **fiókja** hivatkozását. Ebben az esetben a jelszó alaphelyzetbe állításához kapcsolatba kell lépnie a szervezet ügyfélszolgálatával vagy a rendszergazdával a segítségért. |
| A fiókja nincs engedélyezve a jelszó-visszaállításhoz.<br>Sajnos az informatikai részleg nem állította be a fiókját a szolgáltatással való használatra.<br>Ha szeretné, felvehetjük a kapcsolatot a szervezet rendszergazdájával, hogy alaphelyzetbe állítsa a jelszavát. | Ha a felhasználói azonosító beírása után ezt a hibaüzenetet kapja, az azt jelenti, hogy a szervezet nem kapcsolta be a jelszó-visszaállítási funkciót, vagy nem használhatja azt. Ha ebben a helyzetben szeretné visszaállítani a jelszavát, ki kell választania a **kapcsolatot a rendszergazda** hivatkozással. Miután rákattintott a hivatkozásra, a rendszer elküld egy e-mailt a szervezet ügyfélszolgálatának vagy rendszergazdájának, és tudatja Önnel, hogy szeretné visszaállítani a jelszavát. |
| Nem lehetett ellenőrizni a fiókját.<br>Ha szeretné, felvehetjük a kapcsolatot a szervezet rendszergazdájával, hogy alaphelyzetbe állítsa a jelszavát. | Ha a felhasználói azonosító beírása után ezt a hibaüzenetet kapja, az azt jelenti, hogy a szervezete bekapcsolta a jelszó-visszaállítást, és használhatja azt, de nem regisztrált a szolgáltatáshoz. Ebben az esetben kapcsolatba kell lépnie a szervezet ügyfélszolgálatával vagy rendszergazdájával a jelszó alaphelyzetbe állításához. További információ a jelszó-visszaállítás regisztrálásáról az eszközre való visszatérés után: ebben a cikkben. |

## <a name="next-steps"></a>Következő lépések

- [Jelszó módosítása az önkiszolgáló jelszó-visszaállítás használatával](active-directory-passwords-update-your-own-password.md)

- [Biztonsági adatok lap](https://mysignins.microsoft.com/security-info)

- [Jelszó-visszaállítási portál](https://passwordreset.microsoftonline.com/)

- [Ha nem tud bejelentkezni a Microsoft-fiók](https://support.microsoft.com/help/12429/microsoft-account-sign-in-cant)