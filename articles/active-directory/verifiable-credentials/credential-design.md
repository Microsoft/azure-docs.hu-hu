---
title: A Azure Active Directory ellenőrizhető hitelesítő adatainak testreszabása (előzetes verzió)
description: Ebből a cikkből megtudhatja, hogyan hozhatja létre a saját egyéni ellenőrizhető hitelesítő adatait
services: active-directory
author: barclayn
manager: davba
ms.service: identity
ms.subservice: verifiable-credentials
ms.topic: how-to
ms.date: 04/01/2021
ms.author: barclayn
ms.openlocfilehash: a43e734c0a5bfa7c3698dcde5cb5b17f15575d90
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2021
ms.locfileid: "106222928"
---
# <a name="how-to-customize-your-verifiable-credentials-preview"></a>Ellenőrizhető hitelesítő adatok testreszabása (előzetes verzió)

A ellenőrizhető hitelesítő adatok két összetevőből, a szabályokból és a megjelenített fájlokból állnak. A szabályok fájl határozza meg, hogy mit kell megadnia a felhasználónak a ellenőrizhető hitelesítő adatok fogadása előtt. A megjelenítési fájl a jogcímek hitelesítő adatainak és stílusának arculatát vezérli. Ebben az útmutatóban bemutatjuk, hogyan lehet módosítani mindkét fájlt, hogy megfeleljenek a szervezet követelményeinek. 

> [!IMPORTANT]
> Azure Active Directory ellenőrizhető hitelesítő adatok jelenleg nyilvános előzetes verzióban érhetők el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="rules-file-requirements-from-the-user"></a>Szabályok fájl: a felhasználó követelményei

A szabályok fájl egy egyszerű JSON-fájl, amely a ellenőrizhető hitelesítő adatok fontos tulajdonságait ismerteti. Ez különösen azt ismerteti, hogyan történik a jogcímek használata a ellenőrizhető hitelesítő adatok feltöltéséhez.

Jelenleg három bemeneti típus érhető el a szabályok fájlban való konfiguráláshoz. Ezeket a típusokat a ellenőrizhető hitelesítő adat-kiállító szolgáltatás használja a jogcímek ellenőrizhető hitelesítő adatokba való beszúrásához, és tanúsítja az adott információt a DID-val. A következő három típust kell megmagyaráznia.

- AZONOSÍTÓ token
- Ellenőrizhető hitelesítő adatok egy ellenőrizhető bemutatón keresztül.
- Jogcímek Self-Attested

**Azonosító jogkivonat:** A minta alkalmazás és az oktatóanyag az azonosító jogkivonatot használja. Ha ez a beállítás be van állítva, meg kell adnia egy Open ID csatlakozás konfigurációs URI-t, és tartalmaznia kell a VC-ban szerepeltetni kívánt jogcímeket. A rendszer felszólítja a felhasználót, hogy jelentkezzen be a hitelesítő alkalmazásba, hogy megfeleljen ennek a követelménynek, és hozzáadja a társított jogcímeket a fiókjához. 

**Ellenőrizhető hitelesítő adatok:** A kiállítási folyamat végeredménye egy ellenőrizhető hitelesítő adat előállítása, azonban megkérheti, hogy a felhasználó egy ellenőrizhető hitelesítő adatot nyújtson be a kiállításhoz. A szabályok fájl adott jogcímeket képes a bemutatott ellenőrizhető hitelesítő adatokból, és belefoglalni a jogcímeket az újonnan kiadott, ellenőrizhető hitelesítő adatokkal a szervezettől. 

**Saját tanúsítvánnyal rendelkező jogcímek:** Ha ez a beállítás be van jelölve, a felhasználó közvetlenül is beírhatja az adatokat a hitelesítő szolgáltatásba. Jelenleg a sztringek az önigazolt jogcímek egyetlen támogatott bemenete. 

![ellenőrizhető hitelesítő adat kártya részletes áttekintése](media/credential-design/issuance-doc.png) 

**Statikus jogcímek:** Ezen kívül egy statikus jogcímet is deklarálunk a szabályok fájlban, azonban ez a bemenet nem a felhasználótól származik. A kiállító definiál egy statikus jogcímet a szabályok fájlban, és a ellenőrizhető hitelesítő adatokban más jogcímként fog kinézni. Egyszerűen vegyen fel egy credentialSubject a VC után. írja be és deklarálja az attribútumot és a jogcímet. 

```json
"vc": {
    "type": [ "StaticClaimCredential" ],
    "credentialSubject": {
      "staticClaim": true,
      "anotherClaim": "Your Claim Here"
    },
  }
}
```


## <a name="input-type-id-token"></a>Bemeneti típus: azonosító token

Az azonosító token bemenetként való lekéréséhez a szabályok fájljának konfigurálnia kell a OIDC-kompatibilis identitásrendszer jól ismert végpontját. Ebben a rendszeren regisztrálnia kell egy alkalmazást a [kiállítói szolgáltatás kommunikációs példái](issuer-openid.md)közül a megfelelő információkkal. Emellett a client_id a szabályok fájlba kell helyezni, és a hatókör-paramétert a megfelelő hatókörökkel kell kitölteni. Azure Active Directory például az e-mail-hatókörre van szükség, ha az azonosító jogkivonatban egy e-mail-jogcímet szeretne visszaadni.
```json
    {
      "attestations": {
        "idTokens": [
          {
            "mapping": {
              "firstName": { "claim": "given_name" },
              "lastName": { "claim": "family_name" }
            },
            "configuration": "https://dIdPlayground.b2clogin.com/dIdPlayground.onmicrosoft.com/B2C_1_sisu/v2.0/.well-known/openid-configuration",
            "client_id": "8d5b446e-22b2-4e01-bb2e-9070f6b20c90",
            "redirect_uri": "vcclient://openid/",
            "scope": "openid profile"
          }
        ]
      },
      "validityInterval": 2592000,
      "vc": {
        "type": ["https://schema.org/EducationalCredential", "https://schemas.ed.gov/universityDiploma2020", "https://schemas.contoso.edu/diploma2020" ]
      }
    }
```

| Tulajdonság | Leírás |
| -------- | ----------- |
| `attestations.idTokens` | Az OpenID Connect-szolgáltatók olyan tömbje, amely a felhasználói adatok beszerzését támogatja. |
| `...mapping` | Egy objektum, amely leírja, hogy az egyes azonosító jogkivonatokban lévő jogcímek hogyan vannak leképezve az eredményül kapott ellenőrizhető hitelesítő adatok attribútumaira. |
| `...mapping.{attribute-name}` | Az eredményül kapott ellenőrizhető hitelesítő adatban fel kell tölteni az attribútumot. |
| `...mapping.{attribute-name}.claim` | Az azonosító jogkivonatokban lévő jogcím, amelynek értékét az attribútum feltöltésére kell használni. |
| `...configuration` | Az identitás-szolgáltató konfigurációs dokumentumának helye. Az URL-címnek meg kell felelnie az [OpenID Connect szabványnak az Identity Provider metaadataihoz](https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderMetadata). A konfigurációs dokumentumnak tartalmaznia kell a `issuer` ,, `authorization_endpoint` `token_endpoint` és `jwks_uri` mezőket. |
| `...client_id` | Az ügyfél-regisztrációs folyamat során kapott ügyfél-azonosító. |
| `...scope` | Egy szóközzel tagolt lista, amelyeknek a IDENTITÁSSZOLGÁLTATÓ a megfelelő jogcímeket vissza kell tudnia adni az azonosító jogkivonatban. |
| `...redirect_uri` | Mindig az értéket kell használnia `vcclient://openid/` . |
| `validityInterval` | A ellenőrizhető hitelesítő adatok élettartamát jelző időtartam (másodpercben). Az időtartam lejárta után a ellenőrizhető hitelesítő adat már nem érvényes. Ennek az értéknek a kihagyása azt jelenti, hogy minden ellenőrizhető hitelesítő adat érvényes marad, amíg explicit módon vissza nem vonja. |
| `vc.type` | Karakterláncok tömbje, amely azt jelzi, hogy az Ön által ellenőrizhető hitelesítő adat megfelel-e a séma (ok) nak. További részletekért tekintse meg az alábbi szakaszt. |

### <a name="vctype-choose-credential-types"></a>VC. Type: válassza a hitelesítő adatok típusa (ke) t 

Az összes ellenőrizhető hitelesítő adatnak deklarálnia kell a "type" kifejezést a szabályok fájlban. A hitelesítő adatok típusa megkülönbözteti a ellenőrizhető hitelesítő adatokat a más szervezetek által kiadott hitelesítő adatoktól, és biztosítja a kiállítók és a ellenőrzők közötti együttműködést. A hitelesítő adatok típusának jelzéséhez meg kell adnia egy vagy több hitelesítő adatot, amelyet a hitelesítő adatok teljesítenek. Az egyes típusokat egy egyedi karakterlánc jelképezi – gyakran egy URI-t fog használni a globális egyediség biztosításához. Az URI-nak nem kell címezni. Ez karakterláncként van kezelve. 

A Contoso University által kiadott okleveles hitelesítő adatok például a következő típusokat jelenthetik:

| Típus | Cél |
| ---- | ------- |
| `https://schema.org/EducationalCredential` | Kijelenti, hogy a Contoso University által kiadott oklevelek a Schema. org objektum által meghatározott attribútumokat tartalmaznak `EducationaCredential` . |
| `https://schemas.ed.gov/universityDiploma2020` | Kijelenti, hogy a Contoso University által kiadott oklevelek a Egyesült Államok oktatási Minisztériuma által meghatározott attribútumokat tartalmaznak. |
| `https://schemas.contoso.edu/diploma2020` | Kijelenti, hogy a Contoso University által kiadott oklevelek a Contoso University által meghatározott attribútumokat tartalmaznak. |

Mindhárom típus deklarálása esetén a Contoso University diplomái az ellenőrzők különböző kéréseinek kielégítésére használhatók. Egy bank egy felhasználótól kérheti le a-készletet `EducationCredential` , és a diplomát a kérelem teljesítéséhez használhatja. A Contoso University öregdiák-Egyesülete azonban kérheti a típusú hitelesítő adatokat `https://schemas.contoso.edu/diploma2020` , és a diploma is kielégíti a kérést.

A hitelesítő adatok együttműködési képességének biztosítása érdekében ajánlott a kapcsolódó szervezetekkel szorosan együttműködve megadni a hitelesítő adatokat, sémákat és URI-ket az iparágban való használatra. Számos iparági szerv útmutatást nyújt a hivatalos dokumentumok struktúrájához, amelyeket a ellenőrizhető hitelesítő adatok tartalmának meghatározására lehet felhasználni. Emellett szorosan együttműködik a hitelesítő adataival, hogy megtudja, hogyan kérik és használják fel a ellenőrizhető hitelesítő adatokat.

## <a name="input-type-verifiable-credential"></a>Bemeneti típus: ellenőrizhető hitelesítő adatok

>[!NOTE]
>A ellenőrizhető hitelesítő adatokat kérő szabályok fájljai nem használják a megjelenítési Exchange-formátumot a hitelesítő adatok kéréséhez. Ez akkor frissül, ha a kiállító szolgáltatás támogatja a standard, a hitelesítő adatok jegyzékét. 

```json
{
    "attestations": {
      "presentations": [
        {
          "mapping": {
            "first_name": {
              "claim": "$.vc.credentialSubject.firstName",
            },
            "last_name": {
              "claim": "$.vc.credentialSubject.lastName",
              "indexed": true
            }
          },
          "credentialType": "VerifiedCredentialNinja",
          "contracts": [
            "https://beta.did.msidentity.com/v1.0/3c32ed40-8a10-465b-8ba4-0b1e86882668/verifiableCredential/contracts/VerifiedCredentialNinja"
          ],
          "issuers": [
            {
              "iss": "did:ion:123"
            }
          ]
        }
      ]
    },
    "validityInterval": 25920000,
    "vc": {
      "type": [
        "ProofOfNinjaNinja"
      ],
    }
  }
  ```

| Tulajdonság | Leírás |
| -------- | ----------- |
| `attestations.presentations` | Ellenőrizhető hitelesítő adatok tömbje bemenetként való kérésre. |
| `...mapping` | Egy objektum, amely leírja, hogy az egyes bemutatott ellenőrizhető hitelesítő adatok jogcímei hogyan vannak leképezve az eredményül kapott ellenőrizhető hitelesítő adatok attribútumaira. |
| `...mapping.{attribute-name}` | Az eredményül kapott ellenőrizhető hitelesítő adatban fel kell tölteni az attribútumot. |
| `...mapping.{attribute-name}.claim` | A hitelesítési hitelesítő adatban szereplő jogcím, amelynek értékét az attribútum feltöltésére kell használni. |
| `...mapping.{attribute-name}.indexed` | Csak egy ellenőrizhető hitelesítő adatokkal engedélyezhető a visszavonás. További információkért tekintse [meg a hitelesítő adatok visszavonásáról szóló cikket](how-to-issuer-revoke.md) . |
| `credentialType` | Annak a ellenőrizhető hitelesítő adatnak a credentialType, amelyre a felhasználót kéri. |
| `contracts` | A tanúsítvány URI-ja a ellenőrizhető hitelesítőadat-szolgáltatás portálon. |
| `issuers.iss` | A kibocsátó a felhasználótól kapott, ellenőrizhető hitelesítő adatokat adott meg. |
| `validityInterval` | A ellenőrizhető hitelesítő adatok élettartamát jelző időtartam (másodpercben). Az időtartam lejárta után a ellenőrizhető hitelesítő adat már nem érvényes. Ennek az értéknek a kihagyása azt jelenti, hogy minden ellenőrizhető hitelesítő adat érvényes marad, amíg explicit módon vissza nem vonja. |
| `vc.type` | Karakterláncok tömbje, amely azt jelzi, hogy az Ön által ellenőrizhető hitelesítő adat megfelel-e a séma (ok) nak. |


## <a name="input-type-self-attested-claims"></a>Bemenet típusa: önigazolt jogcímek

A kiállítási folyamat során a felhasználó megkérheti, hogy adjon meg néhány önigazolt információt. Mostantól az egyetlen bemeneti típus a "string". 
```json
{
  "attestations": {
    "selfIssued": {
      "mapping": {
        "alias": {
          "claim": "name"
        }
      },
    },
    "validityInterval": 25920000,
    "vc": {
      "type": [
        "ProofOfNinjaNinja"
      ],
    }
  }



```
| Tulajdonság | Leírás |
| -------- | ----------- |
| `attestations.selfIssued` | Olyan önálló jogcímek tömbje, amelyek felhasználói beavatkozást igényelnek. |
| `...mapping` | Egy objektum, amely leírja, hogyan vannak leképezve a saját kibocsátású jogcímek az eredményül kapott ellenőrizhető hitelesítő adatok attribútumaira. |
| `...mapping.alias` | Az eredményül kapott ellenőrizhető hitelesítő adatban fel kell tölteni az attribútumot. |
| `...mapping.alias.claim` | A hitelesítési hitelesítő adatban szereplő jogcím, amelynek értékét az attribútum feltöltésére kell használni. |
| `validityInterval` | A ellenőrizhető hitelesítő adatok élettartamát jelző időtartam (másodpercben). Az időtartam lejárta után a ellenőrizhető hitelesítő adat már nem érvényes. Ennek az értéknek a kihagyása azt jelenti, hogy minden ellenőrizhető hitelesítő adat érvényes marad, amíg explicit módon vissza nem vonja. |
| `vc.type` | Karakterláncok tömbje, amely azt jelzi, hogy az Ön által ellenőrizhető hitelesítő adat megfelel-e a séma (ok) nak. |


## <a name="display-file-verifiable-credentials-in-microsoft-authenticator"></a>Fájl megjelenítése: ellenőrizhető hitelesítő adatok Microsoft Authenticator

A ellenőrizhető hitelesítő adatok korlátozott számú lehetőséget kínálnak, amelyek a márka megjelenítésére használhatók. Ez a cikk útmutatást nyújt a hitelesítő adatok testreszabásához és az ajánlott eljárásokat a felhasználók számára kiállított hitelesítő adatok megtervezéséhez.

A felhasználók számára kiállított ellenőrizhető hitelesítő adatok a Microsoft Authenticator kártyáként jelennek meg. Rendszergazdaként kiválaszthatja a kártya színét, ikonját és szöveges karakterláncait, hogy azok megfeleljenek a szervezet márkájának.

![kiállítási dokumentáció](media/credential-design/detailed-view.png) 

A kártyák olyan testreszabható mezőket is tartalmaznak, amelyekkel a felhasználók megismerhetik a kártya célját, a benne foglalt attribútumokat és egyebeket.

## <a name="create-a-credential-display-file"></a>Hitelesítő adat megjelenítési fájljának létrehozása

Akárcsak a szabályok fájl, a megjelenítési fájl egy egyszerű JSON-fájl, amely leírja, hogyan jelenjen meg a ellenőrizhető hitelesítő adatok tartalma a Microsoft Authenticator alkalmazásban. 

>[!NOTE]
> Jelenleg ezt a megjelenítési modellt csak Microsoft Authenticator használja.

A megjelenítési fájl a következő szerkezettel rendelkezik.

```json
{
    "default": {
      "locale": "en-US",
      "card": {
        "title": "University Graduate",
        "issuedBy": "Contoso University",
        "backgroundColor": "#212121",
        "textColor": "#FFFFFF",
        "logo": {
          "uri": "https://contoso.edu/images/logo.png",
          "description": "Contoso University Logo"
        },
        "description": "This digital diploma is issued to students and alumni of Contoso University."
      },
      "consent": {
        "title": "Do you want to get your digital diploma from Contoso U?",
        "instructions": "Please log in with your Contoso U account to receive your digital diploma."
      },
      "claims": {
        "vc.credentialSubject.name": {
          "type": "String",
          "label": "Name"
        }
      }
    }
}
```

| Tulajdonság | Leírás |
| -------- | ----------- |
| `locale` | A ellenőrizhető hitelesítő adat nyelve. Későbbi használatra fenntartva. | 
| `card.title` | Megjeleníti a felhasználó hitelesítő adatainak típusát. Az ajánlott maximális hossz 25 karakter. | 
| `card.issuedBy` | Megjeleníti a kiállító szervezet nevét a felhasználó számára. Az ajánlott maximális hossz 40 karakter. |
| `card.backgroundColor` | Meghatározza a kártya hátterének színét hexadecimális formátumban. A rendszer az összes kártyára alkalmazza a finom átmenetet. |
| `card.textColor` | Meghatározza a kártya szövegének színét hexadecimális formátumban. Fekete vagy fehér használata javasolt. |
| `card.logo` | A kártyán megjelenő embléma. A megadott URL-címnek nyilvánosan elérhetőnek kell lennie. A javasolt maximális magasság 36 px, a telefon méretétől függetlenül a 100-es maximális szélesség. A PNG használatát transzparens háttérrel ajánljuk. | 
| `card.description` | Az egyes kártyák mellett megjelenő kiegészítő szöveg. Bármilyen célra használható. Az ajánlott maximális hossz 100 karakter. |
| `consent.title` | A kártya kiállításakor megjelenő kiegészítő szöveg. A kiállítási folyamat részleteinek megadására szolgál. Az ajánlott 100 karakter hosszúságú. |
| `consent.instructions` | A kártya kiállításakor megjelenő kiegészítő szöveg. A kiállítási folyamat részleteinek megadására szolgál. Az ajánlott 100 karakter hosszúságú. |
| `claims` | Lehetővé teszi az egyes hitelesítő adatokban található attribútumok címkéjének megadását. |
| `claims.{attribute}` | Megadja annak a hitelesítő adatnak az attribútumát, amelyre a címke vonatkozik. |
| `claims.{attribute}.type` | Megadja az attribútum típusát. Jelenleg csak a "string" támogatott. |
| `claims.{attribute}.label` | Az attribútum címkéként használandó értéke, amely megjelenik a hitelesítő mezőben. Ez eltérő lehet a szabályok fájlban használt címkétől. Az ajánlott maximális hossz 40 karakter. |

>[!NOTE]
  >Ha egy jogcím szerepel a szabályok fájlban, majd kihagyja a megjelenítési fájlban, két különböző típusú élmény van. IOS rendszeren a rendszer nem jeleníti meg a jogcímet a fenti képen látható Részletek szakaszban, míg az Androidon a jogcím megjelenik.  

## <a name="next-steps"></a>Következő lépések

Most már jobban megértette a hitelesíthető hitelesítőadatok kialakítását, és hogyan hozhatja létre saját igényeinek megfelelő saját szükségleteit.

- [A kiállítói szolgáltatás kommunikációs példái](issuer-openid.md)
