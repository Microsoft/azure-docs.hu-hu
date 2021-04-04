---
title: Bejelentkezéssel kapcsolatos problémák elhárítása feltételes hozzáféréssel – Azure Active Directory
description: Ez a cikk azt ismerteti, mi a teendő, ha a feltételes hozzáférési szabályzatok váratlan eredményeket eredményeznek
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: troubleshooting
ms.date: 10/16/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, martinco
ms.collection: M365-identity-device-management
ms.openlocfilehash: 12f722977329bd5d79d4d0e410a29c730faf00c5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92145099"
---
# <a name="troubleshooting-sign-in-problems-with-conditional-access"></a>A feltételes hozzáféréssel kapcsolatos bejelentkezési problémák elhárítása

A cikkben található információk segítségével a feltételes hozzáféréssel kapcsolatos váratlan bejelentkezési eredmények a hibaüzenetek és az Azure AD bejelentkezési naplójának használatával is orvosolhatók.

## <a name="select-all-consequences"></a>Az "összes" következmény kiválasztása

A feltételes hozzáférési keretrendszer nagyszerű konfigurációs rugalmasságot biztosít. A nagy rugalmasság azonban azt is jelenti, hogy az egyes konfigurációs házirendeket alaposan át kell tekintenie, mielőtt a nemkívánatos eredmények elkerülnek. Ebben a kontextusban különös figyelmet kell fordítani a teljes készleteket érintő hozzárendelésekre, például az **összes felhasználó/csoport/Felhőbeli alkalmazásra**.

A szervezeteknek el kell kerülniük a következő konfigurációkat:

**Minden felhasználó, minden felhőalapú alkalmazás:**

- **Hozzáférés letiltása** – ez a konfiguráció blokkolja a teljes szervezetet.
- **Eszköz megfelelőként való megjelölésének megkövetelése** – azon felhasználók számára, akik még nem regisztrálták az eszközeiket, ez a szabályzat blokkolja az összes hozzáférést, beleértve az Intune-portálhoz való hozzáférést is. Ha a rendszergazda regisztrált eszköz nélkül van, akkor ez a szabályzat megakadályozza, hogy visszalépjen a Azure Portal a szabályzat módosításához.
- **Hibrid Azure ad-tartományhoz csatlakoztatott eszköz megkövetelése** – ez a házirend-blokkolási hozzáférés a szervezet összes felhasználója számára is letilthatja a hozzáférést, ha nem rendelkezik hibrid Azure ad-hez csatlakoztatott eszközzel.
- **Alkalmazás-védelmi házirend megkövetelése** – ez a házirend-blokkolási hozzáférés a szervezet összes felhasználója számára is letilthatja a hozzáférést, ha nem rendelkezik Intune-házirenddel. Ha Ön az Intune app Protection-szabályzattal nem rendelkező ügyfélalkalmazás, akkor ez a szabályzat megakadályozza, hogy visszajusson a portálokra, például az Intune-ba és az Azure-ba.

**Minden felhasználó, minden felhőalapú alkalmazás, minden eszköz platform:**

- **Hozzáférés letiltása** – ez a konfiguráció blokkolja a teljes szervezetet.

## <a name="conditional-access-sign-in-interrupt"></a>Feltételes hozzáférés bejelentkezési megszakítása

Az első módszer a megjelenő hibaüzenet áttekintése. A webböngészők használatakor bejelentkező problémák esetén a hibaüzenetet tartalmazó oldal részletes információkkal rendelkezik. Ez az információ önmagában is leírja, hogy mi a probléma, és hogy lehetséges-e megoldást javasolni.

![Bejelentkezési hiba-kompatibilis eszköz szükséges](./media/troubleshoot-conditional-access/image1.png)

A fenti hiba esetén az üzenet azt jelzi, hogy az alkalmazás csak olyan eszközökről vagy ügyfélalkalmazások számára érhető el, amelyek megfelelnek a vállalat mobileszköz-kezelési házirendjének. Ebben az esetben az alkalmazás és az eszköz nem felel meg az adott szabályzatnak.

## <a name="azure-ad-sign-in-events"></a>Azure AD bejelentkezési események

A második módszer, amellyel részletes információkhoz juthat a bejelentkezési megszakításról, az Azure AD bejelentkezési eseményeinek áttekintésével ellenőrizheti, hogy melyik feltételes hozzáférési szabályzatot vagy házirendeket alkalmazták, és miért.

A problémáról további információt a kezdeti hiba oldalon található **További részletek** hivatkozásra kattintva találhat. A **További részletek** lehetőségre kattintva megtalálhatja azokat a hibaelhárítási információkat, amelyek hasznosak lehetnek az Azure ad bejelentkezési eseményeinek keresésekor a felhasználó által látott, illetve a Microsofttal kapcsolatos támogatási incidens megnyitásakor.

![További információ a feltételes hozzáférés megszakított webböngészőből való bejelentkezésről.](./media/troubleshoot-conditional-access/image2.png)

Annak megállapítása, hogy milyen feltételes hozzáférési szabályzatot vagy házirendeket alkalmaztak, és miért nem az alábbiakat.

1. Jelentkezzen be a **Azure Portal** globális rendszergazdaként, biztonsági rendszergazdaként vagy globális olvasóként.
1. Tallózással keresse meg **Azure Active Directory** a  >  **bejelentkezéseket**.
1. Keresse meg az eseményt, amelyről át szeretné tekinteni a bejelentkezést. Szűrők és oszlopok hozzáadása vagy eltávolítása a szükségtelen információk kiszűréséhez.
   1. Szűrők hozzáadása a hatókör szűkítéséhez:
      1. **Korrelációs azonosító** , ha egy konkrét eseményt kell kivizsgálni.
      1. **Feltételes hozzáférés** a házirend hibájának és sikerességének megtekintéséhez. A szűrő hatóköre csak az eredmények korlátozására vonatkozó hibákat jeleníti meg.
      1. A **felhasználónevet** az adott felhasználókkal kapcsolatos információk megjelenítéséhez.
      1. A kérdéses időszakra **vonatkozó hatókör.**

   ![A feltételes hozzáférési szűrő kiválasztása a bejelentkezések naplójában](./media/troubleshoot-conditional-access/image3.png)

1. Ha a felhasználó bejelentkezési hibájának megfelelő bejelentkezési esemény található, válassza a **feltételes hozzáférés** lapot. A feltételes hozzáférés lapon megjelenik a bejelentkezési megszakítást eredményező házirend vagy szabályzat.
   1. A **Hibaelhárítás és támogatás** lapon található információk egyértelmű indoklást adhatnak arról, hogy miért nem sikerült a bejelentkezés, például olyan eszköz, amely nem felelt meg a megfelelőségi követelményeknek.
   1. Ha további vizsgálatot szeretne végezni, tekintse meg a házirendek konfigurációját a **házirend nevére** kattintva. A **szabályzat nevére** kattintva megtekintheti a házirend-konfiguráció felhasználói felületét a kiválasztott házirend felülvizsgálatához és szerkesztéséhez.
   1. A feltételes hozzáférési szabályzat értékeléséhez használt **ügyfél-felhasználó** és- **eszköz adatai** az **alapinformációk**, a **hely**, az **eszköz adatai**, a **hitelesítés részletei** és a bejelentkezési esemény **További részletek** lapjain is elérhetők.

### <a name="policy-details"></a>Szabályzat részletei

Ha kijelöli a házirend jobb oldalán lévő három pontot a bejelentkezési eseményben, a szabályzat részletei jelennek meg. Ez további információkat ad a rendszergazdáknak arról, hogy miért volt sikeres a szabályzat alkalmazása.

   ![Bejelentkezés az Event feltételes hozzáférés lapra](./media/troubleshoot-conditional-access/image5.png)

   ![Szabályzat részletei (előzetes verzió)](./media/troubleshoot-conditional-access/policy-details.png)

A bal oldali oldalon a bejelentkezéskor gyűjtött adatok szerepelnek, és a jobb oldalon részletesen ismertetjük, hogy az adatok megfelelnek-e az alkalmazott feltételes hozzáférési szabályzatok követelményeinek. A feltételes hozzáférési szabályzatok csak akkor érvényesek, ha az összes feltétel teljesül vagy nincs konfigurálva.

Ha az eseményben szereplő információk nem elegendőek a bejelentkezési eredmények megismeréséhez, vagy a szabályzatot úgy állítja be, hogy a kívánt eredményt kapja, akkor előfordulhat, hogy egy támogatási incidens is megnyitható. Navigáljon a bejelentkezési esemény **hibaelhárítási és támogatási** lapjára, és válassza az **új támogatási kérelem létrehozása** lehetőséget.

![A bejelentkezési esemény hibaelhárítás és támogatás lapja](./media/troubleshoot-conditional-access/image6.png)

Az incidens elküldésekor adja meg a kérelem AZONOSÍTÓját, valamint az időt és a dátumot az incidens beküldési adataiban található bejelentkezési eseményből. Ez az információ lehetővé teszi, hogy a Microsoft támogatási szolgálata megkeresse az érintett eseményt.

### <a name="conditional-access-error-codes"></a>Feltételes hozzáférési hibakódok

| Bejelentkezési hibakód | Hiba karakterlánca |
| --- | --- |
| 53000 | DeviceNotCompliant |
| 53001 | DeviceNotDomainJoined |
| 53002 | ApplicationUsedIsNotAnApprovedApp |
| 53003 | BlockedByConditionalAccess |
| 53004 | ProofUpBlockedDueToRisk |

## <a name="what-to-do-if-you-are-locked-out-of-the-azure-portal"></a>Mi a teendő, ha ki van zárva a Azure Portal?

Ha a feltételes hozzáférési házirend helytelen beállítása miatt kizárja a Azure Portalt:

- Győződjön meg arról, hogy a szervezet más rendszergazdái még nincsenek letiltva. A Azure Portalhoz hozzáféréssel rendelkező rendszergazda letilthatja a bejelentkezést befolyásoló házirendet. 
- Ha a szervezet egyik rendszergazdája sem tudja frissíteni a szabályzatot, küldjön be támogatási kérelmet. A Microsoft támogatási szolgálata megtekintheti és megerősítheti a hozzáférést megakadályozó feltételes hozzáférési szabályzatokat.

## <a name="next-steps"></a>Következő lépések

- [Bejelentkezési tevékenységre vonatkozó jelentések az Azure Active Directory portálon](../reports-monitoring/concept-sign-ins.md)
- [Feltételes hozzáférés hibaelhárítása a What If eszköz használatával](troubleshoot-conditional-access-what-if.md)
