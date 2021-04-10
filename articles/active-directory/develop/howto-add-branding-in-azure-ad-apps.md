---
title: Az alkalmazás arculatával kapcsolatos irányelvek | Azure
titleSuffix: Microsoft identity platform
description: Ismerje meg a Microsoft Identity platform alkalmazás-védjegyezési irányelveit.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 08/31/2020
ms.author: ryanwi
ms.reviewer: arielgo, jiml
ms.custom: aaddev, signin_art
ms.openlocfilehash: 236e82ab97244e1428441f83295f6a5d4ed56350
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99581992"
---
# <a name="branding-guidelines-for-applications"></a>Védjegyzési útmutató az alkalmazásokhoz

Ha az alkalmazásokat a Microsoft Identity platformmal fejleszti, akkor az ügyfelet arra kell irányítani, hogy a saját munkahelyi vagy iskolai fiókját (az Azure AD-ben felügyelt) vagy személyes fiókját használja az alkalmazásba való bejelentkezéshez és bejelentkezéshez.

Ebben a cikkben:

- Megismerheti a Microsoft által felügyelt felhasználói fiók két fajtáját, illetve hogy miként hivatkozhat az Azure AD-fiókokra az alkalmazásában.
- Ismerje meg a Microsoft embléma használatának követelményeit az alkalmazásban
- Letölti a hivatalos **Bejelentkezés** és **Bejelentkezés Microsoft-fiókkal** képet az alkalmazásban való használathoz.
- Megismerheti a védjegyzéssel és navigálással kapcsolatban ajánlott és kerülendő műveleteket.

## <a name="personal-accounts-vs-work-or-school-accounts-from-microsoft"></a>Személyes fiókok és munkahelyi vagy iskolai Microsoft-fiókok összehasonlítása

A Microsoft kétféle felhasználói fiókot kezel:

- **Személyes fiókok** (korábban Windows Live ID). Ezek az *egyes* felhasználók és a Microsoft közötti kapcsolatot képviselik, és a Microsoft fogyasztói eszközökhöz és szolgáltatásokhoz való hozzáférését biztosítják. Ezek a fiókok személyes használatra vannak szánva.
- **Munkahelyi vagy iskolai fiókok.** A fiókokat a Microsoft felügyeli az Azure Active Directory-t használó vállalatok nevében. Ezek a fiókok a Microsoft Microsoft 365 és egyéb üzleti szolgáltatásaiba való bejelentkezéshez használhatók.

A Microsoft munkahelyi vagy iskolai fiókokat a szervezetek (vállalatok, iskolák, kormányhivatalok) jellemzően a végfelhasználókhoz (alkalmazottak, diákok, köztisztviselők) rendelik. Ezek a fiókok közvetlenül a felhőben (az Azure AD platformon) vannak elsajátítva, vagy egy helyszíni címtárból, például a Windows Server Active Directoryról szinkronizálva az Azure AD-be. A Microsoft a munkahelyi vagy iskolai fiókok *felügyelője*, de azok a szervezetek tulajdonában állnak, és a szervezetek felügyelik őket.

## <a name="referring-to-azure-ad-accounts-in-your-application"></a>Azure AD-fiókok hivatkozása az alkalmazásban

A Microsoft nem jeleníti meg a végfelhasználóknak az Azure vagy az Active Directory márkaneveket, és Ön se tegye.

- Bejelentkezett felhasználó esetén, ha csak teheti, használja a szervezet nevét és emblémáját. Ez jobb megoldás, mint az általános, „az Ön szervezete” jellegű kifejezések.
- Ha a felhasználó nincs bejelentkezve, hivatkozzon a fiókra „Munkahelyi vagy iskolai fiók” néven, és használja a Microsoft emblémáját annak jelzésére, hogy a fiókokat a Microsoft kezeli. Ne használja a „vállalati fiók”, „üzleti fiók”, „céges fiók” vagy az ehhez hasonló kifejezéseket, mert ezek összezavarhatják a felhasználót.

## <a name="user-account-pictogram"></a>A felhasználói fiók piktogramja

Az irányelvek korábbi verziójában a „kék jelvény” piktogram használatát javasoltuk. A felhasználói és fejlesztői visszajelzések alapján ehelyett jelenleg a Microsoft embléma használatát javasoljuk. A Microsoft emblémája segít a felhasználóknak megérteni, hogy felhasználhatják a Microsoft 365 vagy más Microsoft üzleti szolgáltatásokkal használt fiókot az alkalmazásba való bejelentkezéshez.

## <a name="signing-up-and-signing-in-with-azure-ad"></a>Regisztrálás és bejelentkezés az Azure AD szolgáltatással

Az alkalmazása eltérően működhet regisztráció és bejelentkezés esetén. A következő szakaszokban vizuális útmutatást talál mindkét esethez.

**Ha az alkalmazás támogatja a végfelhasználói regisztrációt (például „free to trial” vagy „freemium” modellek)**: Megjeleníthet egy **Bejelentkezés** gombot, amelyen keresztül a felhasználók a munkahelyi vagy személyes fiókjukkal elérhetik az alkalmazást. Az Azure AD az alkalmazásba való első bejelentkezéskor a jóváhagyásukat fogja kérni.

**Ha az alkalmazás kizárólag rendszergazdák által hozzáférhető engedélyeket, vagy szervezeti licencet igényel**: Különítse el a rendszergazdai beszerzést a felhasználói bejelentkezéstől. Az **„Alkalmazás beszerzése” gomb** átirányítja a rendszergazdákat a bejelentkezéshez, majd a hozzájárulásukat kéri a szervezet felhasználói nevében, aminek az a további előnye, hogy megszünteti a végfelhasználóktól az alkalmazáshoz érkező hozzájárulási kérelmeket.

## <a name="visual-guidance-for-app-acquisition"></a>Vizuális útmutató az alkalmazásbeszerzéshez

Az „Alkalmazás beszerzése” hivatkozás átirányítja a felhasználót az Azure AD hozzáférési megadása (engedélyezés) oldalra, hogy a szervezet rendszergazdája engedélyezhesse, hogy az alkalmazás hozzáférhessen a szervezet Microsoft által szolgáltatott adataihoz. További részletek a hozzáférés kéréséről az [Alkalmazások integrálása az Azure Active Directoryval](./quickstart-register-app.md) cikkben találhatók.

Miután a rendszergazdák beleegyeznek az alkalmazásba, dönthetnek úgy, hogy hozzáadják a felhasználók Microsoft 365 app Launcher felületéhez (elérhető a Waffle és a from verzióból [https://portal.office.com/myapps](https://portal.office.com/myapps) ). Ha hirdetni szeretné ezt a képességet, használja az „Adja a szervezetéhez ezt az alkalmazást” vagy egy hasonló kifejezést, és jelenítsen meg egy, a példához hasonló gombot:

![A Microsoft emblémát és a "Hozzáadás a saját szervezethez" szöveget megjelenítő gomb](./media/howto-add-branding-in-azure-ad-apps/add-to-my-org.png)

Gombok használata helyett inkább magyarázó szövegek írását ajánljuk. Például:

> *Ha már használja Microsoft 365 vagy más üzleti szolgáltatást a Microsofttól, <your_app_name> hozzáférést biztosíthat a szervezet adataihoz. Ez lehetővé teszi, hogy a felhasználók hozzáférhessenek <your_app_name> a meglévő munkahelyi fiókjaikkal.*

A hivatalos Microsoft embléma letöltéséhez az alkalmazáshoz kattintson a jobb gombbal a választott emblémára, majd mentse a gépére.

| Objektum                                | PNG formátum | SVG formátum |
| ------------------------------------ | ---------- | ---------- |
| Microsoft embléma  | ![Letölthető Microsoft-embléma PNG formátumban](./media/howto-add-branding-in-azure-ad-apps/ms-symbollockup_mssymbol_19.png) | ![Letölthető Microsoft-embléma SVG formátumban](./media/howto-add-branding-in-azure-ad-apps/ms-symbollockup_mssymbol_19.svg) |

## <a name="visual-guidance-for-sign-in"></a>Vizuális útmutató a bejelentkezéshez

Az alkalmazás jelenítsen meg egy bejelentkezés gombot, amely átirányítja a felhasználókat a bejelentkezési végpontra, amely megfelel az Azure AD integráláshoz használt protokollnak. A következő szakasz részletesen bemutatja, hogyan nézzen ki a gomb.

### <a name="pictogram-and-sign-in-with-microsoft"></a>Piktogram és „Bejelentkezés Microsofttal”

A Microsoft embléma és a „Bejelentkezés Microsofttal” kifejezés együttese egyedileg jelöli az Azure AD-t az alkalmazás által támogatott egyéb identitásszolgáltatók között. Ha nincs elég hely a „Bejelentkezés Microsofttal” megjelenítéséhez, használhatja a rövidebb „Bejelentkezés” szöveget. A gombokhoz világos vagy sötét téma használható.

Az alábbi diagramok mutatják a Microsoft által ajánlott korlátozásokat az objektumok alkalmazással való használata esetén. A „Bejelentkezés Microsofttal” vagy a rövidebb „Bejelentkezés” verzióra vonatkozó korlátozások.

![A "Bejelentkezés Microsoft-fiókkal" javítási ábrákat jeleníti meg](./media/howto-add-branding-in-azure-ad-apps/sign-in-with-microsoft-redlines.png)

Ha hivatalos képeket szeretne letölteni az alkalmazáshoz, kattintson a jobb gombbal a választott képre, majd mentse a számítógépére.

| Objektum                                | PNG formátum | SVG formátum |
| ------------------------------------ | ---------- | ---------- |
| Bejelentkezés Microsofttal (sötét téma)  | ![Letölthető "Bejelentkezés Microsoft-fiókkal" gomb sötét téma PNG](./media/howto-add-branding-in-azure-ad-apps/ms-symbollockup_signin_dark.png) | ![Letölthető "Bejelentkezés Microsoft-fiókkal" gomb sötét téma SVG](./media/howto-add-branding-in-azure-ad-apps/ms-symbollockup_signin_dark.svg) |
| Bejelentkezés Microsofttal (világos téma) | ![Letölthető "Bejelentkezés Microsoft-fiókkal" gomb Light téma PNG](./media/howto-add-branding-in-azure-ad-apps/ms-symbollockup_signin_light.png) | ![Letölthető "Bejelentkezés Microsoft-fiókkal" gomb Light téma SVG](./media/howto-add-branding-in-azure-ad-apps/ms-symbollockup_signin_light.svg) |
| Bejelentkezés (sötét téma)                 | ![Letölthető "Bejelentkezés" rövid gomb sötét téma PNG](./media/howto-add-branding-in-azure-ad-apps/ms-symbollockup_signin_dark_short.png) | ![Letölthető "Bejelentkezés" rövid gomb sötét téma SVG](./media/howto-add-branding-in-azure-ad-apps/ms-symbollockup_signin_dark_short.svg) |
| Bejelentkezés (világos téma)                | ![Letölthető "Bejelentkezés" rövid gombos világos téma PNG](./media/howto-add-branding-in-azure-ad-apps/ms-symbollockup_signin_light_short.png) | ![Letölthető "Bejelentkezés" rövid gomb – világos téma SVG](./media/howto-add-branding-in-azure-ad-apps/ms-symbollockup_signin_light_short.svg) |

## <a name="branding-dos-and-donts"></a>Ajánlott és kerülendő műveletek a védjegyzéssel kapcsolatban

**HASZNÁLJA** a „Munkahelyi vagy iskolai fiók” és „Bejelentkezés Microsofttal” gombok kombinációját, hogy több információt szolgáltasson a végfelhasználóknak arról, hogy használhatják-e a fiókjukat a bejelentkezéshez. **NE HASZNÁLJA** a „vállalati fiók”, „üzleti fiók”, „céges fiók” vagy más ezekhez hasonló kifejezéseket.

**Ne** használja a "Microsoft 365 id" vagy az "Azure id" kifejezést. A Microsoft 365 a Microsoft vásárlói ajánlatának neve is, amely nem használja az Azure AD-t a hitelesítéshez.

**NE** változtassa meg a Microsoft emblémát.

**NE** jelenítse meg a végfelhasználóknak az Azure vagy az Active Directory márkaneveket. Fejlesztők, informatikai szakemberek és rendszergazdák esetében azonban megengedett a használatuk.

## <a name="navigation-dos-and-donts"></a>Ajánlott és kerülendő műveletek a navigációval kapcsolatban

**ADJON** módot arra, hogy a felhasználók kijelentkezhessenek, és másik felhasználói fiókra válthassanak. Bár a legtöbb felhasználó egyetlen személyes Microsoft-/Facebook-/Google-/Twitter-fiókkal rendelkezik, gyakran egynél több szervezettel is kapcsolatban áll. A többszörösen bejelentkezett felhasználók támogatása hamarosan elérhetővé válik.
