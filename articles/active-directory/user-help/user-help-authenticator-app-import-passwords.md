---
title: Jelszavak importálása a Microsoft Authenticator alkalmazásba – Azure AD
description: Jelszavak importálása a Microsoft-hitelesítési alkalmazásba a népszerű jelszavas kezelők használatával.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 01/28/2021
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: ecc6580148dfba92077336a26ff9160fbe88eb2c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99806155"
---
# <a name="import-passwords-into-the-microsoft-authenticator-app"></a>Jelszavak importálása a Microsoft Authenticator alkalmazásba

A Microsoft Authenticator támogatja a jelszavak importálását a Google Chrome, a Firefox, a LastPass, a Bitwarden és a RoboForm használatával. Ha a Microsoft jelenleg nem támogatja a meglévő jelszavas kezelőt, [manuálisan is megadhatja a bejelentkezési hitelesítő adatokat a sablon CSV](https://go.microsoft.com/fwlink/?linkid=2134938)-fájljában. A meglévő jelszavak importálásához és a hitelesítő alkalmazásban való kezeléséhez egyszerűen exportálja a jelszavakat a meglévő Password managerből a vesszővel tagolt (CSV) formátumba. Ezután importálja az exportált CSV-fájlt a hitelesítő szolgáltatásba a Chrome böngésző bővítményében vagy közvetlenül a hitelesítő alkalmazásban (Android és iOS).

## <a name="import-from-google-chrome-or-android-smart-lock"></a>Importálás a Google Chrome-ból vagy az Androidból Smart Lock

A jelszavakat a Google Chrome-ból vagy az Android-Smart Lockból is importálhatja a hitelesítő szolgáltatásba az okostelefonján vagy az asztali számítógépén. A következőket teheti:

- [Importálás a Chrome-ból Androidon és iOS-en](#import-from-chrome-on-android-and-ios)
- [Importálás a Chrome asztali böngészőjéből](#import-from-chrome-desktop-browser)

### <a name="import-from-chrome-on-android-and-ios"></a>Importálás a Chrome-ból Androidon és iOS-en

A Google Chrome-felhasználók Android-és Apple-telefonokon közvetlenül a telefonjáról is importálhatók a jelszavukat, néhány egyszerű lépéssel.

1. Telepítse a hitelesítő alkalmazást a telefonjára, és nyissa meg a **jelszavak** lapot.

1. Jelentkezzen be a Google Chrome-ba a telefonjára.

1. Koppintson az ![ androidos telefonok vagy a jobb felső sarokban található Google Chrome három pontra ](./media/user-help-authenticator-app-import-passwords/ellipsis-chrome.png) , majd a beállítások elemre **.**

   Platform | Hivatkozás
   ---------- | --------
   Android | ![Google Chrome-beállítások menü helye](./media/user-help-authenticator-app-import-passwords/android-settings-menu.png)
   iOS | ![Google Chrome-beállítások menü ikon](./media/user-help-authenticator-app-import-passwords/apple-settings-menu.png)

1. A **Beállítások** területen nyissa meg a **jelszavakat**.

   Platform | Hivatkozás
   ---------- | --------
   Android | ![Andoid Chrome passwords parancs helye](./media/user-help-authenticator-app-import-passwords/android-passwords-location.png)
   iOS | ![Apple Chrome-jelszavak parancs helye](./media/user-help-authenticator-app-import-passwords/apple-passwords-location.png)

1. Az androidos eszközökön koppintson az ![ androidos telefonok jobb felső sarkában található Google Chrome három pontra, majd a ](./media/user-help-authenticator-app-import-passwords/ellipsis-chrome.png) jobb oldalon az iOS-eszközök elemre, és koppintson a **jelszavak exportálása** lehetőségre.

   Platform | Hivatkozás
   ---------- | --------
   Android | ![Android Chrome-beli jelszavak exportálásának helye](./media/user-help-authenticator-app-import-passwords/android-export-passwords-location.png)
   iOS | ![Apple Chrome-beli jelszavak exportálásának helye](./media/user-help-authenticator-app-import-passwords/apple-export-passwords-location.png)

   Meg kell adnia egy PIN-kódot, ujjlenyomatot vagy Arcfelismerés. Erősítse meg az identitását, és koppintson ismét a **jelszavak exportálása** elemre az Exportálás megkezdéséhez.

1. A jelszavak exportálása után a Chrome arra kéri, hogy válassza ki, hogy melyik alkalmazást importálja. Válassza a **hitelesítő** lehetőséget a jelszavak importálásának megkezdéséhez. Ha elkészült az importálás állapotával, a rendszer értesítést kap.

   Platform | Hivatkozás
   ---------- | --------
   Android | ![Android Chrome-beli importálási jelszavak helye](./media/user-help-authenticator-app-import-passwords/android-chrome-import.png)
   iOS | ![Apple Chrome-beli importálási jelszavak helye](./media/user-help-authenticator-app-import-passwords/apple-chrome-import.png)

### <a name="import-from-chrome-desktop-browser"></a>Importálás a Chrome asztali böngészőjéből

Mielőtt elkezdené, telepítenie kell a [Microsoft automatikus kitöltés bővítményt](https://chrome.google.com/webstore/detail/microsoft-autofill/fiedbfgcleddlbcmgdigjgdfcggjcion) , és be kell jelentkeznie a Chrome böngészőben.

1. Nyissa meg a [Google Password Managert](https://passwords.google.com) bármely böngészőben. Ha még nem tette meg, jelentkezzen be Google-fiókjába.

1. Válassza a fogaskerék ikont ![A Desktop Password Manager fogaskerék ikonja](./media/user-help-authenticator-app-import-passwords/desktop-password-manager-gear.png) a jelszó-beállítási oldal megnyitásához.

1. Válassza az **Exportálás** lehetőséget, majd a következő lapon válassza az **Exportálás** újra lehetőséget a jelszavak exportálásának megkezdéséhez. Adja meg a Google-jelszavát, ha a rendszer kéri, hogy erősítse meg a személyazonosságát. Ha elkészült az importálás állapotával, a rendszer értesítést kap.

   ![Desktop Chrome böngésző exportálási jelszavak parancsának helye](./media/user-help-authenticator-app-import-passwords/desktop-chrome-export-passwords-location.png)

1. Nyissa meg az automatikus kitöltés Chrome bővítményt, és válassza a **Beállítások** lehetőséget.

   ![Asztali Chrome böngésző automatikus kitöltés-bővítményi beállításainak helye](./media/user-help-authenticator-app-import-passwords/desktop-chrome-autofill-settings.png)

1. A párbeszédpanel megnyitásához válassza az **adatimportálás** lehetőséget. Ezután válassza a **fájl kiválasztása** elemet a CSV-fájl megkereséséhez és importálásához.

   ![Asztali Chrome-böngésző – az adatcsv-hely importálása](./media/user-help-authenticator-app-import-passwords/desktop-chrome-import-csv.png)

## <a name="import-from-firefox"></a>Importálás a Firefoxból

A Firefox csak az asztali böngészőből engedélyezi a jelszavak exportálását, ezért győződjön meg arról, hogy hozzáférése van a Firefox asztali böngészőjéhez, mielőtt importálja a jelszavakat a Firefoxból.

1. Jelentkezzen be a Firefox legújabb verziójára az asztalon, és válassza a ![A Firefox "hamburger" menüje](./media/user-help-authenticator-app-import-passwords/desktop-firefox-ellipsis-icon.png) menü a képernyő jobb felső részén.

1. Válassza **a bejelentkezések és jelszavak** lehetőséget.

   ![Asztali Firefox böngészőbeli bejelentkezések és jelszavak helye](./media/user-help-authenticator-app-import-passwords/desktop-firefox-passwords-location.png)

1. A Firefox Lockwise lapon válassza ki a ![ Firefox három pont menüjét ](./media/user-help-authenticator-app-import-passwords/desktop-firefox-ellipsis-icon.png) , válassza a **bejelentkezések exportálása** lehetőséget, majd erősítse meg a szándékát az **Exportálás** lehetőség kiválasztásával. A rendszer a PIN-kód, az eszköz jelszava vagy az ujjlenyomatok vizsgálatával azonosítja magát. A sikeres azonosítást követően a Firefox CSV formátumban exportálja a jelszavakat a kiválasztott helyre.

   ![Asztali Firefox böngésző-exportálási jelszavak helye](./media/user-help-authenticator-app-import-passwords/desktop-firefox-export-passwords-location.png)

1. A jelszavakat egy asztali böngészőből vagy iOS-vagy Android-telefonokba importálhatja a hitelesítő szolgáltatásba. Importálás a hitelesítő alkalmazásba a telefonján:

      1. Vigye át az exportált CSV-fájlt az Android vagy iOS rendszerű telefonjára az előnyben részesített és biztonságos módszer használatával, majd töltse le. Ezután ossza meg a CSV-fájlt a hitelesítő alkalmazással az importálás elindításához.

         Platform | Hivatkozás
         ---------- | --------
         Android | ![Android Chrome-beli importálási jelszavak helye](./media/user-help-authenticator-app-import-passwords/android-chrome-import.png)
         iOS | ![Apple Chrome-beli importálási jelszavak helye](./media/user-help-authenticator-app-import-passwords/apple-chrome-import.png)

      1. Miután sikeresen importálta a jelszavát a hitelesítő szolgáltatásba, törölje a CSV-fájlt az asztalról vagy a mobil telefonról.

## <a name="import-from-lastpass"></a>Importálás a LastPass

A LastPass csak asztali böngészőből támogatja a jelszavak exportálását, ezért győződjön meg arról, hogy hozzáférése van egy asztali böngészőhöz, mielőtt megkezdené a jelszavak importálását.

1. Jelentkezzen be [a LastPass webhelyre](https://lastpass.com) , és válassza a **Speciális beállítások lehetőséget**, majd válassza az **Exportálás** lehetőséget.

   ![Asztali LastPass-exportálási jelszavak helye](./media/user-help-authenticator-app-import-passwords/desktop-lastpass-export-passwords-location.png)

1. Azonosítsa magát, ha a rendszer kéri, hogy adja meg a fő jelszavát. Ezután az exportált jelszavak megjelennek a weblapon.

1. Másolja a weblap tartalmát.

1. Nyissa meg a Jegyzettömböt (vagy a kedvenc szövegszerkesztőjét), és illessze be a másolt tartalmat.

1. Mentse ezt a Jegyzettömb-fájlt a **fájl** &gt; **Mentés másként lehetőség** kiválasztásával. Adjon meg egy ". csv" (például LastPass.csv) végződésű nevet az asztal biztonságos helyén.

   ![Asztali LastPass CSV-fájl mentése](./media/user-help-authenticator-app-import-passwords/desktop-lastpass-save-import-file.png)

1. A jelszavakat egy asztali böngészőben vagy iOS-vagy Android-telefonokon is importálhatja a hitelesítő szolgáltatásba. Importálás a hitelesítő alkalmazásba a telefonján:

      1. Vigye át az exportált CSV-fájlt az okostelefonra előnyben részesített és biztonságos módon, majd töltse le. Ezután ossza meg a CSV-fájlt a hitelesítő alkalmazással az importálás elindításához.

         Platform | Hivatkozás
         ---------- | --------
         Android | ![Android LastPass-importálási jelszavak helye](./media/user-help-authenticator-app-import-passwords/android-chrome-import.png)
         iOS | ![Apple LastPass-importálási jelszavak helye](./media/user-help-authenticator-app-import-passwords/apple-chrome-import.png)

      1. Miután sikeresen importálta a jelszavát a hitelesítő szolgáltatásba, törölje a CSV-fájlt az asztalról vagy a mobil telefonról.

## <a name="import-from-bitwarden"></a>Importálás a Bitwarden

A Bitwarden csak asztali böngészőből támogatja a jelszavak exportálását, ezért győződjön meg arról, hogy hozzáférése van egy asztali böngészőhöz, mielőtt megkezdené a jelszavak importálását.

1. Jelentkezzen be https://vault.bitwarden.com/ , és válassza az **eszközök** &gt; **Exportálás** tároló elemet. Válassza ki a fájlformátumot CSV-ként, adja meg a fő jelszavát, majd válassza a tároló **exportálása** lehetőséget az Exportálás megkezdéséhez.

   ![Bitwarden exportálási tár helye](./media/user-help-authenticator-app-import-passwords/desktop-bitwarden-export-command-location.png)

1. A jelszavakat egy asztali böngészőben vagy iOS-vagy Android-telefonokon is importálhatja a hitelesítő szolgáltatásba. Importálás a hitelesítő alkalmazásba a telefonján:

      1. Vigye át az exportált CSV-fájlt az okostelefonra előnyben részesített és biztonságos módon, majd töltse le. Ezután ossza meg a CSV-fájlt a hitelesítő alkalmazással az importálás elindításához.

         Platform | Hivatkozás
         ---------- | --------
         Android | ![Android Bitwarden-importálási jelszavak helye](./media/user-help-authenticator-app-import-passwords/android-chrome-import.png)
         iOS | ![Apple Bitwarden-importálási jelszavak helye](./media/user-help-authenticator-app-import-passwords/apple-chrome-import.png)

      1. Miután sikeresen importálta a jelszavát a hitelesítő szolgáltatásba, törölje a CSV-fájlt az asztalról vagy a mobil telefonról.

## <a name="import-from-roboform"></a>Importálás a RoboForm-ból

A RoboForm csak az asztali alkalmazásból engedélyezi a jelszavak exportálását, ezért az importálás megkezdése előtt ellenőrizze, hogy van-e hozzáférése a RoboForm alkalmazáshoz az asztalon.

1. Indítsa el a RoboForm-t az asztali ügyfélről, és jelentkezzen be a fiókjába.

1. Válassza a **Beállítások lehetőséget** a **RoboForm** menüből.

   ![Asztali RoboForm-beállítások menü](./media/user-help-authenticator-app-import-passwords/desktop-roboform-options.png)

1. Válassza ki a **fiók & az** &gt; **adatexportálás** elemet.

   ![A Desktop RoboForm exportálási parancsának helye](./media/user-help-authenticator-app-import-passwords/desktop-roboform-accounts-data.png)

1. Válassza ki a biztonságos helyet az exportált fájl mentéséhez. Válassza a **bejelentkezések** **adattípust** , és válassza ki a CSV-fájlt formátumként, majd válassza az **Exportálás** lehetőséget.

   ![Asztali RoboForm exportálás párbeszédpanel](./media/user-help-authenticator-app-import-passwords/desktop-roboform-export-dialog.png)

1. Erősítse meg a szándékát, és a CSV-fájlt exportálja a rendszer a kiválasztott helyre.

   ![A Desktop RoboForm exportálás megerősítő párbeszédpanelje](./media/user-help-authenticator-app-import-passwords/desktop-roboform-confirmation.png)

1. A jelszavakat egy asztali böngészőben vagy iOS-vagy Android-telefonokon is importálhatja a hitelesítő szolgáltatásba. Importálás a hitelesítő alkalmazásba a telefonján:

      1. Vigye át az exportált CSV-fájlt az okostelefonra előnyben részesített és biztonságos módon, majd töltse le. Ezután ossza meg a CSV-fájlt a hitelesítő alkalmazással az importálás elindításához.

         Platform | Hivatkozás
         ---------- | --------
         Android | ![Android RoboForm-beli importálási jelszavak helye](./media/user-help-authenticator-app-import-passwords/android-chrome-import.png)
         iOS | ![Az Apple RoboForm importálási jelszavainak helye](./media/user-help-authenticator-app-import-passwords/apple-chrome-import.png)

      1. Miután sikeresen importálta a jelszavát a hitelesítő szolgáltatásba, törölje a CSV-fájlt az asztalról vagy a mobil telefonról.

## <a name="import-by-creating-a-csv"></a>Importálás CSV-fájl létrehozásával

Ha a Password managerből származó jelszavak importálásának lépései nem szerepelnek ebben a cikkben, létrehozhat egy CSV-fájlt, amely segítségével importálhatja a jelszavakat a hitelesítő szolgáltatásba. A Microsoft azt javasolja, hogy kövesse ezeket a lépéseket egy asztalon a formázás megkönnyítéséhez.

1. Az asztalon [töltse le és nyissa meg az importálási sablont](https://go.microsoft.com/fwlink/?linkid=2134938). Ha Ön Apple iPhone, Safari és kulcstartó felhasználó, most ugorjon a 4. lépésre.

1. Exportálja a jelszavakat a meglévő Password managerből egy nem titkosított CSV-fájlban.

1. Másolja a megfelelő oszlopokat az exportált CSV-fájlból a CSV-sablonba, majd mentse.

1. Ha nem rendelkezik exportált CSV-vel, a meglévő Password managerből másolhatja be az egyes bejelentkezéseket a CSV-sablonba. Ne távolítsa el vagy módosítsa a fejléc sorát. Ha végzett, ellenőrizze az adatok integritását, mielőtt megkezdené a következő lépést.

1. A jelszavakat egy asztali böngészőben vagy iOS-vagy Android-telefonokon is importálhatja a hitelesítő szolgáltatásba. Importálás a hitelesítő alkalmazásba a telefonján:

      1. Vigye át az exportált CSV-fájlt az okostelefonra előnyben részesített és biztonságos módon, majd töltse le. Ezután ossza meg a CSV-fájlt a hitelesítő alkalmazással az importálás elindításához.

         Platform | Hivatkozás
         ---------- | --------
         Android | ![Androidos CSV-importálási jelszavak helye](./media/user-help-authenticator-app-import-passwords/android-chrome-import.png)
         iOS | ![Apple CSV importálási jelszavak helye](./media/user-help-authenticator-app-import-passwords/apple-chrome-import.png)

      1. Miután sikeresen importálta a jelszavát a hitelesítő szolgáltatásba, törölje a CSV-fájlt az asztalról vagy a mobil telefonról.

## <a name="troubleshooting-steps"></a>Hibaelhárítási lépések

A sikertelen importálások leggyakoribb oka a CSV-fájl formázása helytelen. A probléma megoldásához hajtsa végre a következő lépéseket.

- Ebből a cikkből megtudhatja, hogy van-e már támogatva a jelszavak importálása a jelenlegi Password managerből. Ha ezt teszi, érdemes megismételni az importálást a megfelelő szolgáltatóhoz tartozó lépések követésével.

- Ha jelenleg nem támogatja a Password Manager formátumának importálását, próbálkozzon újra [a CSV-fájl manuális létrehozásával](#import-by-creating-a-csv).

- A CSV-adatok integritását a következő javaslatok segítségével ellenőrizheti:

  - Az első sorban három oszlopból álló fejlécnek kell szerepelnie: **URL-cím**, **Felhasználónév** és **jelszó**.

  - Minden sornak tartalmaznia kell egy értéket az **URL-cím** és a **jelszó** oszlopban.

- A CSV-sablonfájl tartalmának beillesztésével újból létrehozhatja a CSV- [fájlt](https://go.microsoft.com/fwlink/?linkid=2134938).

- Ha semmi más nem működik, jelentse a problémát a hitelesítő alkalmazásra vonatkozó **visszajelzés küldése** hivatkozás használatával.
