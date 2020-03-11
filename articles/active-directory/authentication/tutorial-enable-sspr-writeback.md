---
title: Azure Active Directory jelszó visszaírási engedélyezése
description: Ebből az oktatóanyagból megtudhatja, hogyan engedélyezheti az Azure AD önkiszolgáló jelszó-visszaállítási visszaírási az Azure AD Connect használatával, hogy szinkronizálja a módosításokat a helyszíni Active Directory tartományi szolgáltatások környezetbe.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: tutorial
ms.date: 02/18/2020
ms.author: iainfou
author: iainfoulds
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: ccc64fb8dd8bd8abc198d9bfc9d643ef618188ea
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2020
ms.locfileid: "78967780"
---
# <a name="tutorial-enable-azure-active-directory-self-service-password-reset-writeback-to-an-on-premises-environment"></a>Oktatóanyag: Azure Active Directory önkiszolgáló jelszó-visszaállítási visszaírási engedélyezése helyszíni környezetbe

A Azure Active Directory (Azure AD) önkiszolgáló jelszó-visszaállítás (SSPR) használatával a felhasználók frissíthetik a jelszavukat, vagy feloldhatók a fiókjuk a böngészőben. A hibrid környezetekben, ahol az Azure AD egy helyszíni Active Directory tartományi szolgáltatások (AD DS) környezethez csatlakozik, ez a forgatókönyv azt eredményezheti, hogy a jelszavak eltérőek lehetnek a két könyvtár között.

A jelszó-visszaírási az Azure AD-ben a helyi AD DS-környezetbe való visszatéréshez is szinkronizálhatja a jelszó módosításait. A Azure AD Connect biztonságos mechanizmust biztosít a jelszavak visszaküldéséhez egy meglévő helyszíni címtárba az Azure AD-ből.

Ez az oktatóanyag bemutatja, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * A jelszó visszaírási szükséges engedélyek konfigurálása
> * A jelszó-visszaírási beállítás engedélyezése Azure AD Connect
> * Jelszó visszaírási engedélyezése az Azure AD-SSPR

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következő erőforrásokra és jogosultságokra van szüksége:

* Egy működő Azure AD-bérlő, legalább próbaverziós licenccel.
    * Ha szükséges, [hozzon létre egyet ingyen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
    * További információ: [licencelési követelmények az Azure ad SSPR](concept-sspr-licensing.md).
* *Globális rendszergazdai* jogosultságokkal rendelkező fiók.
* Az Azure AD konfigurálva van az önkiszolgáló jelszó-visszaállításhoz.
    * Ha szükséges, [fejezze be az előző oktatóanyagot az Azure ad-SSPR engedélyezéséhez](tutorial-enable-sspr.md).
* Egy meglévő helyszíni AD DS-környezet, amely a Azure AD Connect aktuális verziójával van konfigurálva.
    * Ha szükséges, konfigurálja Azure AD Connect az [expressz](../hybrid/how-to-connect-install-express.md) vagy az [Egyéni](../hybrid/how-to-connect-install-custom.md) beállítások használatával.
    * A jelszó-visszaírási használatához a tartományvezérlőknek Windows Server 2008 R2 vagy újabb verziójúnak kell lenniük.

## <a name="configure-account-permissions-for-azure-ad-connect"></a>Fiók engedélyeinek konfigurálása Azure AD Connecthoz

Azure AD Connect segítségével szinkronizálhatja a felhasználókat, a csoportokat és a hitelesítő adatokat egy helyszíni AD DS környezet és az Azure AD között. A Azure AD Connect általában a helyszíni AD DS tartományhoz csatlakoztatott Windows Server 2012 vagy újabb rendszerű számítógépre telepíti.

Ahhoz, hogy megfelelően működjön a SSPR visszaírási, a Azure AD Connectban megadott fióknak rendelkeznie kell a megfelelő engedélyekkel és beállításokkal. Ha nem tudja biztosan, hogy melyik fiók használatban van, nyissa meg Azure AD Connect és válassza az **aktuális konfiguráció megtekintése** lehetőséget. A fiók, amelyhez engedélyeket kell hozzáadnia, megjelenik a **szinkronizált könyvtárak**területen. A következő engedélyeket és beállításokat kell beállítani a fiókhoz:

* **Új jelszó létrehozása**
* **Jelszó módosítása**
* **Írási engedélyek** `lockoutTime`
* **Írási engedélyek** `pwdLastSet`
* **Kiterjesztett jogosultságok** a következők közül:
   * Az erdő *minden tartományának* legfelső szintű objektuma
   * A SSPR hatókörében használni kívánt felhasználói szervezeti egységek (OU-ket)

Ha nem rendeli hozzá ezeket az engedélyeket, úgy tűnik, hogy a visszaírási megfelelően van konfigurálva, de a felhasználók a felhőből származó helyszíni jelszavaik kezelésekor hibákba ütköznek.

A jelszó visszaírási vonatkozó megfelelő engedélyek beállításához hajtsa végre a következő lépéseket:

1. A helyszíni AD DS környezetében nyissa meg **Active Directory felhasználókat és számítógépeket** egy olyan fiókkal, amely rendelkezik a megfelelő *tartományi rendszergazdai* engedélyekkel.
1. Ellenőrizze, hogy a **nézet** menüben be van-e kapcsolva a **Speciális funkciók** .
1. A bal oldali panelen válassza ki a tartomány gyökerét képviselő objektumot, és válassza a **tulajdonságok** > **biztonsági** > **speciális**lehetőséget.
1. Az **engedélyek** lapon válassza a **Hozzáadás**lehetőséget.
1. A **rendszerbiztonsági tag**mezőben válassza ki azt a fiókot, amelyre az engedélyeket alkalmazni kell (a Azure ad Connect által használt fiókhoz).
1. Az **érintettek** legördülő listában válassza a **leszármazott felhasználói objektumok**elemet.
1. Az *engedélyek*területen jelölje be a következő beállításokhoz tartozó mezőket:
    * **Jelszó módosítása**
    * **Új jelszó létrehozása**
1. A *Tulajdonságok*területen jelölje be a következő beállításokhoz tartozó mezőket. Ezen beállítások megkereséséhez görgessen végig a listában, amelyek már alapértelmezetten is megadhatók:
    * **LockoutTime írása**
    * **PwdLastSet írása**

    [![](media/tutorial-enable-sspr-writeback/set-ad-ds-permissions-cropped.png "Set the appropriate permissions in Active Users and Computers for the account that is used by Azure AD Connect")](media/tutorial-enable-sspr-writeback/set-ad-ds-permissions.png#lightbox)

1. Ha elkészült, kattintson az **alkalmaz/OK gombra** a módosítások alkalmazásához és a megnyitott párbeszédpanelek bezárásához.

Az engedélyek frissítésekor akár egy órát is igénybe vehet, hogy ezek az engedélyek replikálódnak a címtárban lévő összes objektumra.

A helyi AD DS környezetében lévő jelszóházirendek megakadályozhatják, hogy a jelszó-visszaállítások megfelelően legyenek feldolgozva. Ahhoz, hogy a jelszó visszaírási megfelelően működjön, a *jelszó minimális életkorának* a csoportházirendjét 0-ra kell állítani. Ez a beállítás a **Számítógép konfigurációja > házirendek területen található > Windows-beállítások > biztonsági beállítások > fiók házirendek** `gpedit.msc`belül.

Ha frissíti a csoportházirendet, várja meg a frissített házirend replikálását, vagy használja a `gpupdate /force` parancsot.

## <a name="enable-password-writeback-in-azure-ad-connect"></a>Jelszó visszaírási engedélyezése a Azure AD Connectban

Azure AD Connect egyik konfigurációs beállítása a jelszó-visszaírási. Ha ez a beállítás engedélyezve van, a jelszó-módosítási események miatt Azure AD Connect a frissített hitelesítő adatok szinkronizálása a helyszíni AD DS környezetbe.

Az önkiszolgáló jelszó-visszaállítási visszaírási engedélyezéséhez először engedélyezze a visszaírási lehetőséget a Azure AD Connect. A Azure AD Connect-kiszolgálóról végezze el a következő lépéseket:

1. Jelentkezzen be a Azure AD Connect-kiszolgálóra, és indítsa el a **Azure ad Connect** konfigurációs varázslót.
1. Az **üdvözlőlapon** kattintson a **Konfigurálás** gombra.
1. A **További feladatok** lapon válassza a **Szinkronizálási beállítások testreszabása** elemet, majd kattintson a **Tovább** gombra.
1. A **Kapcsolódás az Azure ad-hoz** lapon adja meg az Azure-bérlő globális rendszergazdai hitelesítő adatait, majd kattintson a **tovább**gombra.
1. A **Címtárak csatlakoztatása** és a **Tartomány/szervezeti egység** szűrőoldalakon kattintson a **Tovább** gombra.
1. A **Választható funkciók** lapon jelölje be a **Jelszóvisszaíró** melletti jelölőnégyzetet, és kattintson a **Tovább** gombra.

    ![Azure AD Connect konfigurálása jelszó-visszaírási](media/tutorial-enable-sspr-writeback/enable-password-writeback.png)

1. A **Konfigurálásra kész** lapon kattintson a **Konfigurálás** gombra, és várja meg, amíg a folyamat véget ér.
1. Ha látja, hogy a konfigurálás befejeződött, kattintson a **Kilépés** gombra.

## <a name="enable-password-writeback-for-sspr"></a>Jelszó visszaírási engedélyezése a SSPR

Ha a jelszó visszaírási engedélyezve van a Azure AD Connectban, most konfigurálja az Azure AD-SSPR a visszaírási. Ha engedélyezi a SSPR számára a jelszó-visszaírási használatát, a jelszó módosítására vagy alaphelyzetbe állítására jogosult felhasználók a frissített jelszót a helyszíni AD DS környezetbe is visszaszinkronizálják.

A jelszó visszaírási a SSPR-ben való engedélyezéséhez hajtsa végre a következő lépéseket:

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) globális rendszergazdai fiók használatával.
1. Keresse meg és válassza ki a **Azure Active Directory**, válassza a **jelszó alaphelyzetbe állítása**lehetőséget, majd válassza **a helyszíni integráció**lehetőséget.
1. Beállítja a **jelszavak visszaírásának** lehetőségét a helyszíni könyvtárba? *Igen*értékre.
1. Beállíthatja, hogy a **felhasználók a jelszavuk visszaállítása nélkül is feloldják a fiókok zárolását?** . *Igen*.

    ![Az Azure AD önkiszolgáló jelszó-visszaállítás engedélyezése a jelszó visszaírási](media/tutorial-enable-sspr-writeback/enable-sspr-writeback.png)

1. Ha elkészült, válassza a **Mentés**lehetőséget.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nem szeretné használni az oktatóanyag részeként konfigurált SSPR visszaírási funkciót, hajtsa végre a következő lépéseket:

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com).
1. Keresse meg és válassza ki a **Azure Active Directory**, válassza a **jelszó alaphelyzetbe állítása**lehetőséget, majd válassza **a helyszíni integráció**lehetőséget.
1. Beállítja a **jelszavak visszaírásának lehetőségét a helyszíni címtárra?** a *nem*értékre.
1. Beállíthatja, hogy a **felhasználók a jelszavuk visszaállítása nélkül tudják feloldani a fiókokat?** a *nem*értékre.

Ha már nem szeretne jelszó-funkciót használni, hajtsa végre a következő lépéseket a Azure AD Connect-kiszolgálóról:

1. Jelentkezzen be a Azure AD Connect-kiszolgálóra, és indítsa el a **Azure ad Connect** konfigurációs varázslót.
1. Az **üdvözlőlapon** kattintson a **Konfigurálás** gombra.
1. A **További feladatok** lapon válassza a **Szinkronizálási beállítások testreszabása** elemet, majd kattintson a **Tovább** gombra.
1. A **Kapcsolódás az Azure ad-hoz** lapon adja meg az Azure-bérlő globális rendszergazdai hitelesítő adatait, majd kattintson a **tovább**gombra.
1. A **Címtárak csatlakoztatása** és a **Tartomány/szervezeti egység** szűrőoldalakon kattintson a **Tovább** gombra.
1. A **választható szolgáltatások** lapon törölje a **jelszó visszaírási** melletti jelölőnégyzet jelölését, majd válassza a **tovább**lehetőséget.
1. A **Konfigurálásra kész** lapon kattintson a **Konfigurálás** gombra, és várja meg, amíg a folyamat véget ér.
1. Ha látja, hogy a konfigurálás befejeződött, kattintson a **Kilépés** gombra.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban engedélyezte az Azure AD-SSPR visszaírási egy helyszíni AD DS-környezetben. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * A jelszó visszaírási szükséges engedélyek konfigurálása
> * A jelszó-visszaírási beállítás engedélyezése Azure AD Connect
> * Jelszó visszaírási engedélyezése az Azure AD-SSPR

> [!div class="nextstepaction"]
> [Kockázat értékelése bejelentkezéskor](tutorial-risk-based-sspr-mfa.md)
