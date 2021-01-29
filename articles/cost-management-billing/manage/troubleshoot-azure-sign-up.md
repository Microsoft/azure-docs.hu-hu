---
title: Új fiók az Azure Portalon vagy az Azure Fiókközpontban történő regisztrálásának hibaelhárítása
description: Azon problémák megoldása, amelyek egy új fiók a Microsoft Azure Portal Fiókközpontjában történő regisztrálása során merülnek fel.
services: cost-management-billing
author: v-miegge
manager: dcscontentpm
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: troubleshooting
ms.date: 01/28/2021
ms.author: v-miegge
ms.openlocfilehash: 015e6058236190a04df152573621f0e57030d23b
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2021
ms.locfileid: "99054161"
---
# <a name="troubleshoot-issues-when-you-sign-up-for-a-new-account-in-azure-portal-or-azure-account-center"></a>Új fiók az Azure Portalon vagy az Azure Fiókközpontban történő regisztrálásának hibaelhárítása

Előfordulhat, hogy problémába ütközik, amikor új fiókot próbál meg regisztrálni a Microsoft Azure Portalon vagy az Azure Fiókközpontban. Ez a rövid útmutató végigvezeti a regisztrációs folyamaton, és minden lépésnél ismerteti a gyakorta felmerülő problémákat.

> [!NOTE]
> Ha már rendelkezik fiókkal, és bejelentkezési problémák elhárításával kapcsolatban van szüksége útmutatásra, tekintse meg [Az Azure-előfizetés bejelentkezési hibáinak elhárítása](./troubleshoot-sign-in-issue.md) című cikket.

## <a name="before-you-begin"></a>Előkészületek

A regisztráció megkezdése előtt ellenőrizze a következőt:

- Az Azure-fiók profiljában megadott adatok (például a kapcsolattartási e-mail-cím, a postai cím és a telefonszám) helyesek.
- A hitelkártyaadatai helyesek.
- Ugyanezekkel az adatokkal még nem hozott létre Microsoft-fiókot.

## <a name="guided-walkthrough-of-azure-sign-up"></a>Az Azure-regisztráció részletes bemutatása

Az Azure-regisztrációs folyamat négy szakaszból áll:

- Saját adatok
- Személyazonosság ellenőrzése telefonon
- Személyazonosság ellenőrzése kártyával
- Megállapodás

Ez az útmutató az Azure-fiók regisztrációja során helyesen megadott információkra mutat be példákat. Az egyes szakaszok emellett a gyakori problémákat és az elhárításuk módját is tartalmazzák.

## <a name="about-you"></a>Saját adatok

![Saját adatok](./media/troubleshoot-azure-sign-up/1.png)
 
### <a name="common-issues-and-solutions"></a>Gyakori problémák és megoldásaik

#### <a name="you-see-the-message-we-cannot-proceed-with-sign-up-due-to-an-issue-with-your-account-please-contact-billing-support"></a>A következő üzenet jelenik meg: „A regisztrációt nem lehet folytatni a fiókkal kapcsolatos probléma miatt. Forduljon a számlázási ügyfélszolgálathoz.”

A probléma megoldásához kövesse ezeket a lépéseket:

1.  Jelentkezzen be az [Azure Fiókközpontba](https://account.azure.com/Profile) a fiókadminisztrátori hitelesítő adatokkal.
1.  Válassza a **Részletek szerkesztése** lehetőséget.
1.  Ellenőrizze, hogy a címet tartalmazó mezők ki vannak-e töltve, és a megadott adatok helyesek-e.
1.  Az Azure-előfizetésre való regisztráció során ellenőrizze, hogy a hitelkártya-regisztrációhoz tartozó számlázási cím megfelel-e a banki adatoknak.

Ha továbbra is megjelenik az üzenet, próbáljon egy másik böngészőből regisztrálni.

Mi a helyzet az InPrivate-böngészéssel?

#### <a name="free-trial-is-not-available"></a>Nem érhető el az ingyenes próbaverzió

Használt már korábban Azure-előfizetést? Az Azure használati feltételeinek értelmében az ingyenes próbaverzió aktiválására csak az Azure új felhasználói jogosultak. Ha korábban már volt bármilyen típusú Azure-előfizetése, akkor nem aktiválhatja az ingyenes próbaverziót. Érdemes lehet regisztrálnia egy [használatalapú fizetéses előfizetésre](https://azure.microsoft.com/offers/ms-azr-0003p/).

#### <a name="you-see-the-message-you-are-not-eligible-for-an-azure-subscription"></a>A következő üzenet jelenik meg: „Jelenleg nem jogosult Azure-előfizetésre”

A probléma megoldásához ellenőrizze, hogy az alábbiak teljesülnek-e:

- Az Azure-fiók profiljában megadott adatok (például a kapcsolattartási e-mail-cím, a postai cím és a telefonszám) helyesek.
- A hitelkártyaadatok helyesek.
- Nincs olyan Microsoft-fiókja, amely ugyanazokat az adatokat használja.

#### <a name="you-see-the-message-your-current-account-type-is-not-supported"></a>A következő üzenet jelenik meg: „A jelenlegi fióktípus nem támogatott”

Ez a probléma akkor fordulhat elő, ha a fiók regisztrálva van egy olyan [nem felügyelt Azure AD-címtárban](../../active-directory/enterprise-users/directory-self-service-signup.md), amely nem a cég vagy szervezet Azure AD-címtára.
A probléma megoldásához regisztráljon egy másik fiókot Azure-fiókként, vagy vegye át a nem felügyelt AD-címtárat. További információkért tekintse meg a [nem felügyelt címtár az Azure Active Directoryban rendszergazdaként történő átvételét](../../active-directory/enterprise-users/domains-admin-takeover.md) ismertető cikket.

## <a name="identity-verification-by-phone"></a>Személyazonosság ellenőrzése telefonon

![Személyazonosság ellenőrzése telefonon](./media/troubleshoot-azure-sign-up/2.png)
 
Ha megkapta a szöveges üzenetet vagy fogadta a telefonhívást, adja meg a kapott kódot a szövegmezőben.

### <a name="common-issues-and-solutions"></a>Gyakori problémák és megoldásaik

#### <a name="no-verification-text-message-or-phone-call"></a>Nincs ellenőrző szöveges üzenet vagy telefonhívás

Bár a regisztráció ellenőrzésének folyamata általában gyors, az ellenőrző kód megérkezéséig akár négy perc is eltelhet.

Néhány további tipp:

- Az ellenőrzéshez bármilyen telefonszám használható, csak meg kell felelnie a követelményeknek. Az ellenőrzéshez megadott telefonszámot a rendszer nem tárolja a fiók kapcsolattartási telefonszámaként.
  - A telefonos ellenőrzéshez nem használható VoIP-telefonszám.
  - Ellenőrizze, hogy a telefonja fogadhat-e hívást vagy SMS-üzenetet egyesült államokbeli telefonszámról.
- Ellenőrizze a megadott telefonszámot, beleértve a legördülő menüből kiválasztott országkódot.
- Ha a telefonja nem fogad szöveges üzeneteket (SMS-eket), próbálkozzon a **Hívást kérek** lehetőséggel.

## <a name="identity-verification-by-card"></a>Személyazonosság ellenőrzése kártyával

![Személyazonosság ellenőrzése kártyával](./media/troubleshoot-azure-sign-up/3.png)
 
### <a name="common-issues-and-solutions"></a>Gyakori problémák és megoldásaik

#### <a name="credit-card-declined-or-not-accepted"></a>A hitelkártya el lett utasítva, vagy nem elfogadott

A virtuális hitelkártyák vagy bankkártyák és a feltöltőkártyák nem használhatók fizetőeszközként Azure-előfizetésekhez. Azzal kapcsolatban, hogy mi okozhatja még a kártya elutasítását, tekintse meg az [Azure-regisztráció során elutasított bankkártyával kapcsolatos probléma elhárítását](./troubleshoot-declined-card.md) ismertető részt.

#### <a name="credit-card-form-doesnt-support-my-billing-address"></a>A hitelkártyaűrlap nem támogatja a számlázási címet

A számlázási címnek a **Saját adatok** szakaszban kiválasztott országban kell lennie. Ellenőrizze, hogy a megfelelő országot adta-e meg.

#### <a name="progress-bar-hangs-in-identity-verification-by-card-section"></a>A Személyazonosság ellenőrzése kártyával szakaszban található folyamatjelző lefagy

A személyazonosság kártyával történő ellenőrzésének elvégzéséhez engedélyeznie kell a harmadik féltől származó cookie-kat a böngészőben.

A böngésző cookie-beállításainak frissítéséhez hajtsa végre az alábbi lépéseket.

1. Frissítse a cookie-beállításokat.
   - **Chrome** használata esetén:
     - Válassza a **Beállítások** > **Speciális beállítások megjelenítése** > **Adatvédelem** > **Webhelybeállítások** lehetőséget. Kapcsolja ki a **Harmadik féltől származó cookie-k és webhelyadatok letiltása** beállítást.

   - **Microsoft Edge** használata esetén:
     - Válassza a **Beállítások** > **Speciális beállítások megnyitása** > **Cookie-k** > **Cookie-k engedélyezése** lehetőséget.

1. Frissítse az Azure regisztrációs lapját, és ellenőrizze, hogy megoldódott-e a probléma.
1. Ha a frissítés nem oldotta meg a problémát, zárja be, és indítsa újra a böngészőt, majd próbálkozzon újra.

### <a name="i-saw-a-charge-on-my-free-trial-account"></a>Megjelent egy díjtétel az ingyenes próbafiókban

A regisztráció után előfordulhat, hogy megjelenik egy átmeneti, kis összegű azonosítási zárolás a hitelkártyához tartozó számláján. Ez az összeg 3–5 napon belül fel lesz oldva. Ha aggódik a költségek kezelése miatt, tekintse meg a [váratlan költségek elemzését](../understand/analyze-unexpected-charges.md) ismertető témakört.

## <a name="agreement"></a>Megállapodás

Töltse ki a Megállapodást.

## <a name="other-issues"></a>Egyéb problémák

### <a name="cant-activate-azure-benefit-plan-like-visual-studio-bizspark-bizsparkplus-or-mpn"></a>Nem aktiválható az Azure-beli juttatási csomag, például a Visual Studio, a BizSpark, a BizSparkPlus vagy az MPN

Ellenőrizze, hogy a megfelelő bejelentkezési hitelesítő adatokat használja-e. Ezután tekintse meg a kedvezményprogramot, és ellenőrizze, hogy jogosult-e erre.
- Visual Studio
  - Ellenőrizze a jogosultsági állapotát a [Visual Studio-fiók oldalán](https://my.visualstudio.com/Benefits).
  - Ha nem tudja ellenőrizni az állapotát, forduljon [Visual Studio Subscription támogatási szolgálathoz](https://visualstudio.microsoft.com/subscriptions/support/).
- Microsoft for Startups
  - Microsoft for Startups-jogosultsági állapotának ellenőrzéséhez jelentkezzen be a [Microsoft for Startups portáljára](https://startups.microsoft.com/#start-two).
  - Ha nem tudja ellenőrizni az állapotát, kérjen segítséget a [Microsoft for Startups fórumain](https://www.microsoftpartnercommunity.com/t5/Microsoft-for-Startups/ct-p/Microsoft_Startups).
- MPN
  - Jogosultsági állapota ellenőrzéséhez jelentkezzen be az [MPN portáljára](https://mspartner.microsoft.com/Pages/Locale.aspx). Ha rendelkezik a megfelelő [Cloud Platform-kompetenciákkal](https://mspartner.microsoft.com/pages/membership/cloud-platform-competency.aspx), lehetséges, hogy további kedvezményekre is jogosult.
  - Ha nem tudja ellenőrizni az állapotát, forduljon az [MPN ügyfélszolgálatához](https://mspartner.microsoft.com/Pages/Support/Premium/contact-support.aspx).

### <a name="cant-activate-new-azure-in-open-subscription"></a>Nem lehet aktiválni az új Azure in Open-előfizetést

Azure in Open-előfizetés létrehozásához rendelkeznie kell egy olyan érvényes, online szolgáltatás aktiválására szolgáló (OSA-) kulccsal, amelyhez legalább egy Azure in Open-jogkivonat hozzá van rendelve. Ha nem rendelkezik OSA-kulccsal, lépjen kapcsolatba a [Microsoft Pinpointban](https://pinpoint.microsoft.com/) felsorolt valamelyik Microsoft-partnerrel.

## <a name="additional-help-resources"></a>További támogatási forrásanyagok

Az Azure-számlázással és -előfizetésekkel kapcsolatos további hibaelhárítási cikkek

- [Elutasított bankkártya](./troubleshoot-declined-card.md)
- [Bejelentkezéssel kapcsolatos problémák az előfizetésben](./troubleshoot-sign-in-issue.md)
- [Nem található előfizetés](./no-subscriptions-found.md)
- [Vállalati költségek nézet letiltva](./enterprise-mgmt-grp-troubleshoot-cost-view.md)

## <a name="contact-us-for-help"></a>Ha segítségre van szüksége, vegye fel velünk a kapcsolatot.

Ha kérdése van vagy segítségre van szüksége, [hozzon létre egy támogatási kérést](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="find-out-more-about-azure-cost-management"></a>További információ az Azure Cost Managementről

- [Az Azure Cost Management and Billing dokumentációja](../index.yml)