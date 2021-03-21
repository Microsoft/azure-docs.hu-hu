---
title: A telefon alapú MFA biztonságossá tétele a Azure AD B2Cban
titleSuffix: Azure AD B2C
description: Ismerkedjen meg a telefonos többtényezős hitelesítés (MFA) Azure AD B2C-bérlőben történő biztonságossá tételével Azure Monitor Log Analytics-jelentések és-riasztások használatával. A munkafüzet használatával azonosíthatja a csalárd telefonos hitelesítéseket, és elháríthatja a csalárd regisztrációkat. =
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.date: 02/01/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: cc9e0be90c138ba33e1b4dfe11ea6f9c8b7da297
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102033554"
---
# <a name="securing-phone-based-multi-factor-authentication-mfa"></a>A telefon alapú többtényezős hitelesítés (MFA) biztonságossá tétele

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

A Azure Active Directory (Azure AD) Multi-Factor Authentication (MFA) segítségével a felhasználók automatikus hanghívást kaphatnak egy olyan telefonszámon, amelyet az ellenőrzéshez regisztrálnak. A rosszindulatú felhasználók több fiók létrehozásával és telefonhívások elvégzése nélkül is kihasználhatják ezt a módszert. Ezek a számos sikertelen bejelentkezés kimerítheti az engedélyezett bejelentkezési kísérleteket, így megakadályozva, hogy a többi felhasználó regisztrálja az új fiókokat a Azure AD B2C bérlőn. A támadások elleni védelem érdekében a Azure Monitor segítségével figyelheti a telefonos hitelesítési hibákat, és elháríthatja a csalárd regisztrációkat.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, hozzon létre egy [log Analytics munkaterületet](azure-monitor.md).

## <a name="create-a-phone-based-mfa-events-workbook"></a>Telefon alapú MFA-események munkafüzetének létrehozása

A [Azure ad B2C jelentések &](https://github.com/azure-ad-b2c/siem#phone-authentication-failures) a githubon lévő riasztások tárháza olyan összetevőket tartalmaz, amelyekkel jelentések, riasztások és irányítópultok hozhatók létre és tehetők közzé Azure ad B2C naplók alapján. Az alábbi ábrán látható ábra a telefonnal kapcsolatos hibákat emeli ki.

### <a name="overview-tab"></a>Áttekintés lap

Az **Áttekintés** lapon az alábbi információk láthatók:

- Meghibásodási okok (a sikertelen telefonos hitelesítések teljes száma az egyes okok miatt)
- Rossz Hírnev miatt blokkolva
- Sikertelen telefonos hitelesítésű IP-cím (az egyes IP-címek sikertelen telefonos hitelesítésének teljes száma)
- Telefonszámok IP-címmel – sikertelen telefonos hitelesítés
- Böngésző (telefonos hitelesítési hibák egy ügyféloldali böngészőben)
- Operációs rendszer (telefonos hitelesítési hibák ügyféloldali operációs rendszeren)

![Áttekintés lap](media/phone-based-mfa/overview-tab.png)

### <a name="details-tab"></a>Részletek lap

A **részletek** lapon a következő információk szerepelnek:

- Azure AD B2C házirend – sikertelen telefonos hitelesítések
- Telefonos hitelesítési hibák telefonszámon – idődiagram (állítható idővonal)
- Telefonos hitelesítési hibák Azure AD B2C szabályzattal – idődiagram (állítható időkeret)
- Telefonos hitelesítési hibák IP-cím szerint – idődiagram (állítható idővonal)
- Válassza ki a telefonszámot a hiba részleteinek megtekintéséhez (válasszon egy telefonszámot a hibák részletes listájához)

![Részletek lap 1/3](media/phone-based-mfa/details-tab-1.png)

![Részletek lap 2 – 3](media/phone-based-mfa/details-tab-2.png)

![Részletek lap 3/3](media/phone-based-mfa/details-tab-3.png)

## <a name="use-the-workbook-to-identify-fraudulent-sign-ups"></a>A munkafüzet használata a csalárd regisztrációk azonosítására

A munkafüzet használatával megismerheti a telefonos MFA-eseményeket, és azonosíthatja a telefonos szolgáltatás potenciálisan rosszindulatú használatát.

1. Az alábbi kérdések megválaszolásával megismerheti, hogy mi a szokásos a bérlő számára:

   - Hol vannak azok a régiók, amelyeken a telefon alapú MFA várható?
   - Vizsgálja meg a sikertelen telefon alapú MFA-kísérletek okait. normálisnak vagy vártnak számítanak?

2. A csalárd regisztráció jellemzőinek felismerése:

   - **Hely-alapú**: vizsgálja meg a **telefonos hitelesítési hibákat IP-cím szerint** minden olyan fiókhoz, amely olyan helyekhez van társítva, amelyekről nem várható a felhasználók regisztrációja.

   > [!NOTE]
   > A megadott IP-cím egy hozzávetőleges régió.

   - **Sebesség-alapú**: Tekintse meg a **sikertelen telefonos hitelesítések túlórát (naponta)**, amely azokat a telefonszámokat jelzi, amelyek rendellenes számú sikertelen telefonos hitelesítési kísérletet tesznek elérhetővé naponta, a legmagasabb (bal oldali) és a legalacsonyabb (jobbra) értéktől számítva.

3. A megtévesztő regisztrációk enyhítése a következő szakaszban leírt lépéseket követve.
 

## <a name="mitigate-fraudulent-sign-ups"></a>Csalárd regisztrációk enyhítése

Tegye a következő műveleteket a csalárd regisztrációk enyhítése érdekében.

- Használja a felhasználói folyamatok **ajánlott** verzióit a következők elvégzéséhez:
     
   - [Engedélyezze az egyszeri jelszó (OTP) szolgáltatást](phone-authentication-user-flows.md) az MFA-hoz (a regisztrálási és bejelentkezési folyamatokra is vonatkozik).
   - [Feltételes hozzáférési szabályzat konfigurálása](conditional-access-user-flow.md) a bejelentkezések hely szerinti blokkolásához (csak a bejelentkezési folyamatokra vonatkozik, nem kell regisztrálni a folyamatokat).
   - Az API-összekötők használatával [integrálhat egy olyan anti-bot-megoldással, mint például a reCAPTCHA](https://github.com/Azure-Samples/active-directory-b2c-node-sign-up-user-flow-captcha) (a regisztrációs folyamatokra vonatkozik).

- Távolítsa el a szervezete számára nem releváns országkódok számát a legördülő menüből, ahol a felhasználó ellenőrzi a telefonszámát (ez a változás a jövőbeli bejelentkezésekre fog vonatkozni):
    
   1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) az Azure AD B2C-bérlő globális rendszergazdájaként.

   2. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja, majd a felső menüben válassza ki a **címtár + előfizetés** szűrőt, és válassza ki a bérlőt tartalmazó könyvtárat.

   3. Válassza az Azure Portal bal felső sarkában található **Minden szolgáltatás** lehetőséget, majd keresse meg és válassza ki az **Azure AD B2C**-t.

   4. Válassza ki a felhasználói folyamatot, majd válassza a **nyelvek** lehetőséget. Válassza ki a szervezet földrajzi helyének nyelvét a nyelvi részletek panel megnyitásához. (Ebben a példában az **angol en** lehetőséget választjuk a Egyesült Államok). Válassza ki a **többtényezős hitelesítés lapot**, majd válassza az **Alapértelmezések letöltése (en)** lehetőséget.
 
      ![Új felülbírálások feltöltése az alapértelmezett beállítások letöltéséhez](media/phone-based-mfa/download-defaults.png)

   5. Nyissa meg az előző lépésben letöltött JSON-fájlt. A fájlban keresse meg a kifejezést, `DEFAULT` és cserélje le a sort a következőre: `"Value": "{\"DEFAULT\":\"Country/Region\",\"US\":\"United States\"}"` . Ügyeljen arra, hogy a következőre legyen beállítva: `Overrides` `true` .

   > [!NOTE]
   > Testre szabhatja az engedélyezett országkódok listáját a `countryList` elemben (lásd a [telefonos faktor hitelesítési oldalát](localization-string-ids.md#phone-factor-authentication-page-example)).

   7. Mentse a JSON-fájlt. A nyelv részletei panel **új felülbírálások feltöltése** területén válassza ki a módosított JSON-fájlt a feltöltéshez.

   8. Zárjuk be a panelt, és válassza a **felhasználói folyamat futtatása** lehetőséget. Ebben a példában ellenőrizze, hogy **Egyesült Államok** az egyetlen országkód a legördülő menüben:
 
      ![Országkód legördülő lista](media/phone-based-mfa/country-code-drop-down.png)

## <a name="next-steps"></a>Következő lépések

- Tudnivalók az [Identity Protectionről és a feltételes hozzáférésről Azure ad B2C](conditional-access-identity-protection-overview.md) 

- [Feltételes hozzáférés alkalmazása a Azure Active Directory B2C felhasználói folyamataihoz](conditional-access-user-flow.md)