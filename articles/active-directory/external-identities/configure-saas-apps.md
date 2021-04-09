---
title: SaaS-alkalmazások konfigurálása B2B-együttműködéshez – Azure AD
description: Megtudhatja, hogyan konfigurálhat SaaS-alkalmazásokat Azure Active Directory B2B-együttműködéshez, és hogyan tekinthet meg további rendelkezésre álló erőforrásokat.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 05/23/2017
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: e0db5e609a5e6b7d74809810a50f86d72ae85675
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "90705510"
---
# <a name="configure-saas-apps-for-b2b-collaboration"></a>SaaS-alkalmazások B2B-együttműködéshez

Azure Active Directory (Azure AD) VÁLLALATKÖZI együttműködés az Azure AD-vel integrált legtöbb alkalmazással működik. Ebben a szakaszban részletesen ismertetjük az Azure AD B2B-vel való használatra szolgáló, népszerű SaaS-alkalmazások konfigurálására vonatkozó utasításokat.

Az alkalmazásra vonatkozó utasítások megtekintése előtt itt talál néhány szabályt:

* A legtöbb alkalmazás esetében a felhasználónak manuálisan kell történnie. Ezt a felhasználókat manuálisan kell létrehozni az alkalmazásban is.

* Az automatikus telepítést támogató alkalmazások (például a Dropbox) esetében a különálló meghívások az alkalmazásokból jönnek létre. A felhasználóknak mindenképpen el kell fogadniuk minden meghívást.

* A felhasználói attribútumok között, hogy enyhítse az összekeveredett felhasználói profil lemezével (UPD) kapcsolatos problémákat a vendég felhasználóinál, mindig állítsa be a **felhasználói azonosítót** a **User. mail** értékre.


## <a name="dropbox-business"></a>Dropbox Business

Annak engedélyezéséhez, hogy a felhasználók a szervezeti fiókjával jelentkezzenek be, manuálisan kell konfigurálnia a Dropbox vállalatot, hogy az Azure AD-t használja Security Assertion Markup Language (SAML) identitás-szolgáltatóként. Ha a Dropbox Business nem erre van konfigurálva, nem tud rákérdezni vagy más módon engedélyezni a felhasználókat az Azure AD használatával történő bejelentkezéshez.

1. Ha a Dropbox üzleti alkalmazást az Azure AD-be szeretné adni, válassza ki a **vállalati alkalmazások** lehetőséget a bal oldali ablaktáblán, majd kattintson a **Hozzáadás** gombra.

   ![A vállalati alkalmazások oldal "Hozzáadás" gombja](media/configure-saas-apps/add-dropbox.png)

2. Az **alkalmazás hozzáadása** ablakban adja meg a **Dropbox** kifejezést a keresőmezőbe, majd válassza a **Dropbox vállalat számára** lehetőséget az eredmények listájában.

   ![A "Dropbox" kifejezés keresése az alkalmazás hozzáadása oldalon](media/configure-saas-apps/add-app-dialog.png)

3. Az **egyszeri bejelentkezés** lapon válassza az **egyszeri bejelentkezés** lehetőséget a bal oldali ablaktáblán, majd írja be a **User. mail** elemet a **felhasználói azonosító** mezőbe. (Az alapértelmezett UPN-ként van beállítva.)

   ![Egyszeri bejelentkezés konfigurálása az alkalmazáshoz](media/configure-saas-apps/configure-app-sso.png)

4. Ha le szeretné tölteni a Dropbox-konfigurációhoz használni kívánt tanúsítványt, válassza a **Dropbox konfigurálása** lehetőséget, majd válassza ki az **SAML egyszeri bejelentkezés szolgáltatás URL-címét** a listában.

   ![A Dropbox-konfiguráció tanúsítványának letöltése](media/configure-saas-apps/download-certificate.png)

5. Jelentkezzen be a Dropboxba az **egyszeri bejelentkezési** oldal bejelentkezési URL-címével.

   ![A Dropbox bejelentkezési lapját ábrázoló képernyőfelvétel](media/configure-saas-apps/sign-in-to-dropbox.png)

6. A menüben válassza a **felügyeleti konzol** lehetőséget.

   ![A "felügyeleti konzol" hivatkozás a Dropbox menüben](media/configure-saas-apps/dropbox-menu.png)

7. A **hitelesítés** párbeszédpanelen válassza a **továbbiak** lehetőséget, töltse fel a tanúsítványt, majd a **bejelentkezési URL-cím** mezőbe írja be az SAML egyszeri bejelentkezési URL-címet.

   ![A "további" hivatkozás az összecsukott hitelesítés párbeszédpanelen](media/configure-saas-apps/dropbox-auth-01.png)

   ![A "bejelentkezési URL" a kibontott hitelesítés párbeszédpanelen](media/configure-saas-apps/paste-single-sign-on-URL.png)

8. Ha az automatikus felhasználó beállítását szeretné konfigurálni a Azure Portalban, válassza a **kiépítés** lehetőséget a bal oldali ablaktáblán, válassza az **automatikus** lehetőséget a **létesítési mód** mezőben, majd válassza az **Engedélyezés** lehetőséget.

   ![Az automatikus felhasználó-kiépítés konfigurálása a Azure Portalban](media/configure-saas-apps/set-up-automatic-provisioning.png)

Ha a vendég vagy a tag felhasználói már be lettek állítva a Dropbox alkalmazásban, a Dropboxtól külön meghívót kapnak. A Dropbox egyszeri bejelentkezés használatához a meghívásoknak el kell fogadniuk a meghívót, ha egy hivatkozásra kattintanak.

## <a name="box"></a>Box
Az SAML protokollon alapuló összevonás használatával engedélyezheti a felhasználóknak a Box Guest Users szolgáltatás Azure AD-fiókjával történő hitelesítését. Ebben az eljárásban metaadatokat tölt fel a Box.com.

1. Adja hozzá a Box alkalmazást a vállalati alkalmazásokból.

2. Az egyszeri bejelentkezés konfigurálása a következő sorrendben történik:

   ![Az egyszeri bejelentkezés konfigurációs beállításait bemutató képernyőkép](media/configure-saas-apps/configure-box-sso.png)

   a. A **bejelentkezési URL-cím** mezőben ellenőrizze, hogy a bejelentkezési URL-cím megfelelően van-e beállítva a Azure Portal található dobozhoz. Ez az URL-cím a Box.com-bérlő URL-címe. Az elnevezési konvenciónak kell követnie *https://.box.com* .  
   Az **azonosító** nem vonatkozik erre az alkalmazásra, de továbbra is kötelező mezőként jelenik meg.

   b. A **felhasználói azonosító** mezőben adja meg a **User. mail** (egyszeri bejelentkezés a vendég fiókokhoz) értéket.

   c. Az **SAML aláíró tanúsítvány** területen kattintson az **új tanúsítvány létrehozása** lehetőségre.

   d. Ha szeretné megkezdeni a Box.com-bérlő konfigurálását az Azure AD identitás-szolgáltatóként való használatára, töltse le a metaadat-fájlt, majd mentse a helyi meghajtóra.

   e. Továbbítsa a metaadat-fájlt a Box support csapatának, amely az egyszeri bejelentkezést konfigurálja.

3. Az Azure AD automatikus felhasználói beállításához a bal oldali ablaktáblán válassza ki a **kiépítés** elemet, majd válassza az **Engedélyezés** lehetőséget.

   ![Az Azure AD-hez való kapcsolódás engedélyezése a Box-hoz](media/configure-saas-apps/auth-azure-ad-to-connect-to-box.png)

A Dropbox meghívásokhoz hasonlóan a Box-meghívásoknak is be kell váltaniuk a meghívót a Box alkalmazásban.

## <a name="next-steps"></a>Következő lépések

Tekintse meg a következő cikkeket az Azure AD B2B együttműködésről:

- [Mi az az Azure AD B2B együttműködés?](what-is-b2b.md)
- [Dinamikus csoportok és B2B-együttműködés](use-dynamic-groups.md)
- [B2B együttműködés felhasználói jogcímek leképezése](claims-mapping.md)
- [Külső megosztás Microsoft 365](o365-external-user.md)

