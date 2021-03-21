---
title: Nyelvi Testreszabás az Azure AD felhasználói folyamataiban
description: Ismerje meg, hogyan szabhatja testre a felhasználói folyamatok nyelvi élményét Azure Active Directory.
services: active-directory
author: msmimart
manager: celestedg
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 03/02/2021
ms.author: mimart
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: a199c207e8ea35f1471df9bfd0c4134551b9995f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "101653490"
---
# <a name="language-customization-in-azure-active-directory"></a>Nyelvi Testreszabás a Azure Active Directoryban

A Azure Active Directory (Azure AD) nyelvi testreszabása lehetővé teszi a felhasználói folyamat számára, hogy a felhasználók igényeinek megfelelően különböző nyelveket fogadjon. A Microsoft [36 nyelvekhez](#supported-languages)biztosít fordításokat. Akkor is testreszabhatja az attribútumok nevét az attribútumok gyűjteménye lapon, ha a felhasználói élmény csak egyetlen nyelvhez van megadva.

## <a name="how-language-customization-works"></a>A nyelvi Testreszabás működése

Alapértelmezés szerint a nyelvi Testreszabás engedélyezve van a regisztrált felhasználók számára, hogy konzisztens regisztrációs élményt biztosítsanak. A nyelvek használatával módosíthatja a felhasználók számára megjelenő karakterláncokat az attribútumok gyűjtési folyamatának részeként a regisztráció során.

> [!NOTE]
> Ha egyéni felhasználói attribútumokat használ, saját fordításokat kell megadnia. További információ: [a karakterláncok testreszabása](#customize-your-strings).

## <a name="customize-your-strings"></a>A karakterláncok testreszabása

A nyelvi Testreszabás lehetővé teszi a felhasználói folyamat bármely karakterláncának testreszabását.

1. Jelentkezzen be az [Azure Portalba](https://portal.azure.com) Azure ad-rendszergazdaként.
2. Az **Azure-szolgáltatások** területen válassza a **Azure Active Directory** lehetőséget.
3. A bal oldali menüben válassza a **külső identitások** lehetőséget.
4. Válassza a **felhasználói folyamatok** lehetőséget.
3. Válassza ki a fordításhoz engedélyezni kívánt felhasználói folyamatot.
4. Válassza a **nyelvek** lehetőséget.
5. A felhasználói folyamat **nyelvek** lapján válassza ki a testreszabni kívánt nyelvet.
6. **Attribútum-gyűjtemény lap** kibontása
7. Válassza az **Alapértelmezések letöltése** (vagy a **felülbírálások letöltése** lehetőséget, ha korábban már szerkesztette ezt a nyelvet).

Ezek a lépések egy JSON-fájlt biztosítanak a karakterláncok szerkesztésének megkezdéséhez.

### <a name="change-any-string-on-the-page"></a>Az oldalon lévő bármelyik sztring módosítása

1. Nyissa meg a JSON-szerkesztő korábbi utasításait letöltő JSON-fájlt.
1. Keresse meg a módosítani kívánt elemet. Keresse meg `StringId` a keresett karakterláncot, vagy keresse meg a `Value` módosítani kívánt attribútumot.
1. Frissítse az `Value` attribútumot, hogy mit szeretne megjeleníteni.
1. Minden módosítani kívánt sztring esetében váltson a következőre: `Override` `true` .
1. Mentse a fájlt, és töltse fel a módosításokat. (A feltöltési vezérlőt a JSON-fájl letöltésével megegyező helyen találja.)

> [!IMPORTANT]
> Ha felül kell bírálnia egy karakterláncot, ügyeljen arra, hogy a értékre állítsa be a `Override` értéket `true` . Ha az érték nem változik, a rendszer figyelmen kívül hagyja a bejegyzést.

### <a name="change-extension-attributes"></a>Bővítmény attribútumainak módosítása

Ha módosítani szeretné egy egyéni felhasználói attribútumhoz tartozó karakterláncot, vagy az egyiket szeretné hozzáadni a JSON-hoz, a következő formátumban kell megadnia:

```JSON
{
  "LocalizedStrings": [
    {
      "ElementType": "ClaimType",
      "ElementId": "extension_<ExtensionAttribute>",
      "StringId": "DisplayName",
      "Override": true,
      "Value": "<ExtensionAttributeValue>"
    }
    [...]
}
```

A helyére írja `<ExtensionAttribute>` be az egyéni felhasználói attribútum nevét.

Cserélje le `<ExtensionAttributeValue>` a szöveget a megjelenítendő új sztringre.

### <a name="provide-a-list-of-values-by-using-localizedcollections"></a>Adja meg az értékek listáját a LocalizedCollections használatával

Ha meg szeretné adni a válaszok értékeinek listáját, létre kell hoznia egy `LocalizedCollections` attribútumot. `LocalizedCollections` a és a párok tömbje `Name` `Value` . Az elemek sorrendje a megjelenő sorrendben jelenik meg. A hozzáadáshoz `LocalizedCollections` használja a következő formátumot:

```JSON
{
  "LocalizedStrings": [...],
  "LocalizedCollections": [{
      "ElementType":"ClaimType",
      "ElementId":"<UserAttribute>",
      "TargetCollection":"Restriction",
      "Override": true,
      "Items":[
           {
                "Name":"<Response1>",
                "Value":"<Value1>"
           },
           {
                "Name":"<Response2>",
                "Value":"<Value2>"
           }
     ]
  }]
}
```

* `ElementId` a felhasználó attribútuma, amelyhez ez az `LocalizedCollections` attribútum válaszol.
* `Name` a felhasználó számára megjelenített érték.
* `Value` Ha ez a beállítás be van jelölve, a rendszer a jogcímben visszaadott értéket adja vissza.

### <a name="upload-your-changes"></a>A módosítások feltöltése

1. A JSON-fájl módosításainak befejezése után térjen vissza a bérlőhöz.
1. Válassza a **felhasználói folyamatok** lehetőséget, majd kattintson arra a felhasználói folyamatra, amelyet engedélyezni szeretne a fordítások számára.
1. Válassza a **nyelvek** lehetőséget.
1. Válassza ki a nyelvet, amelyet le szeretne fordítani.
1. Válassza ki az **attribútum-gyűjtemény lapot**.
1. Válassza ki a mappa ikont, és válassza ki a feltölteni kívánt JSON-fájlt.

A módosításokat a rendszer automatikusan menti a felhasználói folyamatba.

## <a name="additional-information"></a>További információ

### <a name="page-ui-customization-labels-as-overrides"></a>Oldal felhasználói felületének testreszabása feliratok felülbírálásként

Ha engedélyezi a nyelvi testreszabást, az oldal felhasználói felületének testreszabását használó címkék korábbi szerkesztési adatai megmaradnak az angol (en) JSON-fájlban. A nyelvi testreszabási nyelvi erőforrások feltöltésével továbbra is módosíthatja a címkéket és más karakterláncokat.

### <a name="up-to-date-translations"></a>Naprakész fordítások

A Microsoft elkötelezte magát arra, hogy a legnaprakészebb fordításokat biztosítson a használathoz. A Microsoft folyamatosan fejleszti a fordításokat, és megfelel az Ön számára. A Microsoft azonosítja a hibákat és a globális terminológiában bekövetkező változásokat, és olyan frissítéseket tesz elérhetővé, amelyek zökkenőmentesen fognak működni a felhasználói folyamat során.

### <a name="support-for-right-to-left-languages"></a>Jobbról balra író nyelvek támogatása

A Microsoft jelenleg nem nyújt támogatást a jobbról balra író nyelvekhez. Ezt az egyéni területi beállítások és a CSS használatával végezheti el a karakterláncok megjelenítésének megváltoztatásához. Ha erre a szolgáltatásra van szüksége, szavazzon rá az [Azure-visszajelzésre](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/19393000-provide-language-support-for-right-to-left-languag).

### <a name="social-identity-provider-translations"></a>Közösségi identitás-szolgáltatói fordítások

A Microsoft biztosítja a `ui_locales` OIDC paramétert a közösségi bejelentkezésekhez. De egyes közösségi identitás-szolgáltatók, például a Facebook és a Google is, ne tartsák tiszteletben őket.

### <a name="browser-behavior"></a>Böngésző viselkedése

A Chrome és a Firefox is a beállított nyelvre irányuló kérelem. Ha ez egy támogatott nyelv, az alapértelmezés szerint megjelenik. A Microsoft Edge jelenleg nem kér nyelvet, és egyenesen az alapértelmezett nyelvre lép.

## <a name="supported-languages"></a>Támogatott nyelvek

Az Azure AD az alábbi nyelvek támogatását tartalmazza. A felhasználói folyamatok nyelvei az Azure AD-ben érhetők el. A multi-Factor Authentication (MFA) értesítési nyelveit az [Azure ad MFA](../authentication/concept-mfa-howitworks.md)nyújtja.

| Nyelv              | Nyelvkód | Felhasználói folyamatok         | MFA-értesítések  |
|-----------------------| :-----------: | :----------------: | :----------------: |
| Arab                | AR            | ![X, jelezve: nem.](./media/user-flow-customize-language/no.png) | ![Zöld pipa.](./media/user-flow-customize-language/yes.png) |
| Bolgár             | BG            | ![X, jelezve: nem.](./media/user-flow-customize-language/no.png) | ![Zöld pipa.](./media/user-flow-customize-language/yes.png) |
| Bangla                | Mrd USD            | ![Zöld pipa.](./media/user-flow-customize-language/yes.png) | ![X, jelezve: nem.](./media/user-flow-customize-language/no.png) |
| Katalán               | CA            | ![X, jelezve: nem.](./media/user-flow-customize-language/no.png) | ![Zöld pipa.](./media/user-flow-customize-language/yes.png) |
| Cseh                 | CS            | ![Zöld pipa.](./media/user-flow-customize-language/yes.png) | ![Zöld pipa.](./media/user-flow-customize-language/yes.png) |
| Dán                | da            | ![Zöld pipa.](./media/user-flow-customize-language/yes.png) | ![Zöld pipa.](./media/user-flow-customize-language/yes.png) |
| Német                | de            | ![Zöld pipa.](./media/user-flow-customize-language/yes.png) | ![Zöld pipa.](./media/user-flow-customize-language/yes.png) |
| Görög                 | el            | ![Zöld pipa.](./media/user-flow-customize-language/yes.png) | ![Zöld pipa.](./media/user-flow-customize-language/yes.png) |
| Angolul               | hu            | ![Zöld pipa.](./media/user-flow-customize-language/yes.png) | ![Zöld pipa.](./media/user-flow-customize-language/yes.png) |
| spanyol               | es            | ![Zöld pipa.](./media/user-flow-customize-language/yes.png) | ![Zöld pipa.](./media/user-flow-customize-language/yes.png) |
| Észt              | et            | ![X, jelezve: nem.](./media/user-flow-customize-language/no.png) | ![Zöld pipa.](./media/user-flow-customize-language/yes.png) |
| Baszk                | EU            | ![X, jelezve: nem.](./media/user-flow-customize-language/no.png) | ![Zöld pipa.](./media/user-flow-customize-language/yes.png) |
| finn               | Fi            | ![Zöld pipa.](./media/user-flow-customize-language/yes.png) | ![Zöld pipa.](./media/user-flow-customize-language/yes.png) |
| Francia                | fr            | ![Zöld pipa.](./media/user-flow-customize-language/yes.png) | ![Zöld pipa.](./media/user-flow-customize-language/yes.png) |
| Gallego              | GL            | ![X, jelezve: nem.](./media/user-flow-customize-language/no.png) | ![Zöld pipa.](./media/user-flow-customize-language/yes.png) |
| gudzsaráti              | Gu            | ![Zöld pipa.](./media/user-flow-customize-language/yes.png) | ![X, jelezve: nem.](./media/user-flow-customize-language/no.png) |
| héber                | ő            | ![X, jelezve: nem.](./media/user-flow-customize-language/no.png) | ![Zöld pipa.](./media/user-flow-customize-language/yes.png) |
| Hindi                 | szia            | ![Zöld pipa.](./media/user-flow-customize-language/yes.png) | ![Zöld pipa.](./media/user-flow-customize-language/yes.png) |
| Horvát              | ó            | ![Zöld pipa.](./media/user-flow-customize-language/yes.png) | ![Zöld pipa.](./media/user-flow-customize-language/yes.png) |
| Magyar             | hu            | ![Zöld pipa.](./media/user-flow-customize-language/yes.png) | ![Zöld pipa.](./media/user-flow-customize-language/yes.png) |
| Indonéz            | id            | ![X, jelezve: nem.](./media/user-flow-customize-language/no.png) | ![Zöld pipa.](./media/user-flow-customize-language/yes.png) |
| Olasz               | Ez            | ![Zöld pipa.](./media/user-flow-customize-language/yes.png) | ![Zöld pipa.](./media/user-flow-customize-language/yes.png) |
| Japán              | Japán            | ![Zöld pipa.](./media/user-flow-customize-language/yes.png) | ![Zöld pipa.](./media/user-flow-customize-language/yes.png) |
| Kazak                | KK            | ![X, jelezve: nem.](./media/user-flow-customize-language/no.png) | ![Zöld pipa.](./media/user-flow-customize-language/yes.png) |
| kannada               | KN            | ![Zöld pipa.](./media/user-flow-customize-language/yes.png) | ![X, jelezve: nem.](./media/user-flow-customize-language/no.png) |
| Koreai                | Ko            | ![Zöld pipa.](./media/user-flow-customize-language/yes.png) | ![Zöld pipa.](./media/user-flow-customize-language/yes.png) |
| Litván            | lt            | ![X, jelezve: nem.](./media/user-flow-customize-language/no.png) | ![Zöld pipa.](./media/user-flow-customize-language/yes.png) |
| Lett               | lv            | ![X, jelezve: nem.](./media/user-flow-customize-language/no.png) | ![Zöld pipa.](./media/user-flow-customize-language/yes.png) |
| malajálam             | ml            | ![Zöld pipa.](./media/user-flow-customize-language/yes.png) | ![X, jelezve: nem.](./media/user-flow-customize-language/no.png) |
| marathi               | Mr            | ![Zöld pipa.](./media/user-flow-customize-language/yes.png) | ![X, jelezve: nem.](./media/user-flow-customize-language/no.png) |
| Maláj                 | MS            | ![Zöld pipa.](./media/user-flow-customize-language/yes.png) | ![Zöld pipa.](./media/user-flow-customize-language/yes.png) |
| Norvég bokmal      | NB            | ![Zöld pipa.](./media/user-flow-customize-language/yes.png) | ![X, jelezve: nem.](./media/user-flow-customize-language/no.png) |
| Holland                 | nl            | ![Zöld pipa.](./media/user-flow-customize-language/yes.png) | ![Zöld pipa.](./media/user-flow-customize-language/yes.png) |
| Norvég             | nem            | ![X, jelezve: nem.](./media/user-flow-customize-language/no.png) | ![Zöld pipa.](./media/user-flow-customize-language/yes.png) |
| pandzsábi               | PA            | ![Zöld pipa.](./media/user-flow-customize-language/yes.png) | ![X, jelezve: nem.](./media/user-flow-customize-language/no.png) |
| Lengyel                | pl            | ![Zöld pipa.](./media/user-flow-customize-language/yes.png) | ![Zöld pipa.](./media/user-flow-customize-language/yes.png) |
| Portugál - Brazília   | pt-br         | ![Zöld pipa.](./media/user-flow-customize-language/yes.png) | ![Zöld pipa.](./media/user-flow-customize-language/yes.png) |
| Portugál - Portugália | pt-pt         | ![Zöld pipa.](./media/user-flow-customize-language/yes.png) | ![Zöld pipa.](./media/user-flow-customize-language/yes.png) |
| Román              | ro            | ![Zöld pipa.](./media/user-flow-customize-language/yes.png) | ![Zöld pipa.](./media/user-flow-customize-language/yes.png) |
| Orosz               | ru            | ![Zöld pipa.](./media/user-flow-customize-language/yes.png) | ![Zöld pipa.](./media/user-flow-customize-language/yes.png) |
| Szlovák                | sk            | ![Zöld pipa.](./media/user-flow-customize-language/yes.png) | ![Zöld pipa.](./media/user-flow-customize-language/yes.png) |
| Szlovén             | SL            | ![X, jelezve: nem.](./media/user-flow-customize-language/no.png) | ![Zöld pipa.](./media/user-flow-customize-language/yes.png) |
| Szerb – cirillbetűs    | SR-cryl-cs    | ![X, jelezve: nem.](./media/user-flow-customize-language/no.png) | ![Zöld pipa.](./media/user-flow-customize-language/yes.png) |
| Szerb – latinbetűs       | SR-Latn-cs    | ![X, jelezve: nem.](./media/user-flow-customize-language/no.png) | ![Zöld pipa.](./media/user-flow-customize-language/yes.png) |
| svéd               | Sv            | ![Zöld pipa.](./media/user-flow-customize-language/yes.png) | ![Zöld pipa.](./media/user-flow-customize-language/yes.png) |
| tamil                 | ta            | ![Zöld pipa.](./media/user-flow-customize-language/yes.png) | ![X, jelezve: nem.](./media/user-flow-customize-language/no.png) |
| telugu                | ttelepítése            | ![Zöld pipa.](./media/user-flow-customize-language/yes.png) | ![X, jelezve: nem.](./media/user-flow-customize-language/no.png) |
| Thai                  | adik            | ![Zöld pipa.](./media/user-flow-customize-language/yes.png) | ![Zöld pipa.](./media/user-flow-customize-language/yes.png) |
| Török               | TR            | ![Zöld pipa.](./media/user-flow-customize-language/yes.png) | ![Zöld pipa.](./media/user-flow-customize-language/yes.png) |
| Ukrán             | uk            | ![X, jelezve: nem.](./media/user-flow-customize-language/no.png) | ![Zöld pipa.](./media/user-flow-customize-language/yes.png) |
| Vietnámi            | VI            | ![X, jelezve: nem.](./media/user-flow-customize-language/no.png) | ![Zöld pipa.](./media/user-flow-customize-language/yes.png) |
| Kínai – egyszerűsített  | zh-Hans       | ![Zöld pipa.](./media/user-flow-customize-language/yes.png) | ![Zöld pipa.](./media/user-flow-customize-language/yes.png) |
| Kínai – hagyományos | zh-Hant       | ![Zöld pipa.](./media/user-flow-customize-language/yes.png) | ![Zöld pipa.](./media/user-flow-customize-language/yes.png) |