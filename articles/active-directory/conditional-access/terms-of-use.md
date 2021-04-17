---
title: Használati feltételek – Azure Active Directory | Microsoft Docs
description: Ismerkedés a Azure Active Directory használati feltételekkel, hogy a hozzáférés előtt információt nyújtsunk az alkalmazottaknak vagy a vendégeknek.
services: active-directory
ms.service: active-directory
ms.subservice: compliance
ms.topic: how-to
ms.date: 01/27/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jocastel
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4c8e18979ff1575e1a050244a96e7858cdce46b
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107530249"
---
# <a name="azure-active-directory-terms-of-use"></a>Azure Active Directory használati feltételek

Az Azure AD használati feltételekkel kapcsolatos szabályzatai egyszerű módszert biztosítanak, amely segítségével a szervezetek információkat nyújthatnak a végfelhasználóknak. Ez a bemutató gondoskodik arról, hogy a felhasználók megkapják a jogi vagy megfelelőségi követelményekre vonatkozó nyilatkozatokat. Ez a cikk a használati feltételekre vonatkozó szabályzatok használatának első feltételeit ismerteti.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="overview-videos"></a>Áttekintő videók

Az alábbi videó gyors áttekintést nyújt a toU-szabályzatokról.

>[!VIDEO https://www.youtube.com/embed/tj-LK0abNao]

További videók:
- [Használati feltételekre vonatkozó szabályzat üzembe helyezése a Azure Active Directory](https://www.youtube.com/embed/N4vgqHO2tgY)
- [Használati feltételekre vonatkozó szabályzatok bevezetési Azure Active Directory](https://www.youtube.com/embed/t_hA4y9luCY)

## <a name="what-can-i-do-with-terms-of-use"></a>Mire használhatom a használati feltételeket?

Az Azure AD használati feltételekkel kapcsolatos szabályzatok a következő képességekkel rendelkeznek:

- Megkövetelheti az alkalmazottaktól vagy vendégektől, hogy a hozzáférés előtt elfogadják a használati feltételeket.
- Az alkalmazottaknak vagy vendégeknek minden eszközön el kell fogadniuk a használati feltételeket a hozzáférés előtt.
- Az alkalmazottaknak vagy vendégeknek ismétlődő ütemezés szerint el kell fogadniuk a használati feltételeket.
- A biztonsági információk Azure AD Multi-Factor Authentication (MFA) szolgáltatásban való regisztrálása előtt az alkalmazottaknak vagy vendégeknek el kell fogadniuk a használati feltételeket.
- Az alkalmazottaknak el kell fogadniuk a használati feltételeket, mielőtt biztonsági adatokat regisztrálnak az Azure AD önkiszolgáló jelszó-visszaállítási (SSPR) szolgáltatásban.
- Általános használati feltételeket ismertet a szervezet összes felhasználója számára.
- A használati feltételekre vonatkozó szabályzatok felhasználói attribútumok alapján való használata (például orvosok kontra nővérek, belföldi kontra nemzetközi alkalmazottak [dinamikus csoportjai](../enterprise-users/groups-dynamic-membership.md)).
- Specifikus használati feltételeket biztosít olyan nagy üzleti hatással járó alkalmazásokhoz való hozzáféréskor, mint például a Salesforce.
- A használati feltételeket különböző nyelveken mutatja be.
- Felsorolja, hogy ki fogadta el vagy nem fogadta el a használati feltételeket.
- Segítség az adatvédelmi szabályozások nak való segítségnyújtáshoz.
- A megfelelőségi és auditálási szabályzatok tevékenységére vonatkozó használati feltételek naplóját jeleníti meg.
- Használati feltételek szabályzatok létrehozása és kezelése Microsoft Graph [API-k használatával (jelenleg](/graph/api/resources/agreement) előzetes verzióban érhető el).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD használati feltételekre vonatkozó szabályzatok használatának és konfigurálásának feltételei:

- Prémium szintű Azure AD P1, P2, EMS E3 vagy EMS E5 előfizetés.
   - Ha még nem rendelkezik ezen előfizetések egyikével sem, [beszerezhet Prémium szintű Azure AD előfizetést](../fundamentals/active-directory-get-started-premium.md), vagy [engedélyezheti a Prémium szintű Azure AD előfizetés próbaverzióját](https://azure.microsoft.com/trial/get-started-active-directory/).
- A következő rendszergazda fiókok egyike a konfigurálni kívánt könyvtárhoz:
   - Globális rendszergazda
   - Biztonsági rendszergazda
   - Feltételes hozzáférés rendszergazdája

## <a name="terms-of-use-document"></a>A használati feltételek dokumentuma

Az Azure AD használati feltételekkel kapcsolatos szabályzata a PDF formátumot használja a tartalom bemutatja. A PDF-fájl bármilyen tartalmat jelenthet, akár meglévő szerződéseket is, így begyűjtheti a végfelhasználói megállapodásokat a felhasználók bejelentkezésekor. A felhasználók mobileszközökön való támogatásához a PDF-fájlban javasolt betűméret 24 pontos.

## <a name="add-terms-of-use"></a>Használati feltételek hozzáadása

Miután véglegesítette a használati feltételekkel kapcsolatos szabályzatdokumentumot, az alábbi eljárással használhatja azt.

1. Jelentkezzen be az Azure-ba globális rendszergazdaként, biztonsági rendszergazdaként vagy feltételes hozzáférésű rendszergazdaként.
1. Lépjen a **Használati feltételekre** a következőn: [https://aka.ms/catou](https://aka.ms/catou).

    ![Feltételes hozzáférés – Használati feltételek panel](./media/terms-of-use/tou-blade.png)

1. Kattintson az **Új feltételek** lehetőségre.

    ![Új használati feltételek panel a használati feltételek beállításainak megadásához](./media/terms-of-use/new-tou.png)

1. A **Név mezőben** adja meg a használati feltételek szabályzatának nevét, amely a Azure Portal.
1. A **Megjelenítendő név mezőben** adjon meg egy címet, amely megjelenik a felhasználók számára a bejelentkezéskor.
1. A **Használati feltételek a véglegesített** használati feltételek pdf-fájlját, és jelölje ki.
1. Válassza ki a használati feltételek szabályzatdokumentumának nyelvét. A nyelvi beállítással több használati feltételre vonatkozó szabályzatot is feltölthet, amelyek mindegyikét eltérő nyelvvel töltheti fel. A használati feltételek végfelhasználó számára látható verziója a böngésző beállításaitól függ.
1. Ha meg kell követelni a végfelhasználóktól, hogy az elfogadásuk előtt megtekintsék a használati feltételeket, állítsa a **Kötelező** a felhasználók számára a használati feltételek kibontása beállításnál a **Be beállítását.**
1. Ha meg kell követelni a végfelhasználóktól, hogy elfogadják a  használati feltételeket minden eszközön, amelyről hozzáférnek, állítsa A felhasználók jóváhagyásának megkövetelása minden eszközön beállítás be **van állítva.** Ha ez a beállítás engedélyezve van, előfordulhat, hogy a felhasználóknak további alkalmazásokat kell telepíteniük. További információkért lásd az [eszközönkénti használati feltételeket.](#per-device-terms-of-use)
1. Ha a használati feltételeket ütemezetten szeretné elévülni, állítsa a **Jóváhagyások** lejárata beállításnál a **Be adatokat.** Ha Be van állítva, két további ütemezési beállítás jelenik meg.

    ![A jóváhagyások lejárati beállításainak megadása a kezdési dátum, a gyakoriság és az időtartam beállítására](./media/terms-of-use/expire-consents.png)

1. A Lejárat **kezdő dátuma és** **a** Gyakoriság beállításokkal adhatja meg a használati feltételek lejáratának ütemezését. Az alábbi táblázat néhány példabeállítás eredményét mutatja be:

   | Lejárati idő a következő naptól kezdődően: | Gyakoriság | Eredmény |
   | --- | --- | --- |
   | A mai dátum  | Havonta | A mai naptól kezdve a felhasználóknak el kell fogadniuk a használati feltételeket, majd minden hónapban újra el kell fogadniuk őket. |
   | Jövőbeli dátum  | Havonta | A mai naptól kezdve a felhasználóknak el kell fogadniuk a használati feltételekre vonatkozó szabályzatot. A jövőbeli dátum bekövetkeztével a jóváhagyások lejárnak, majd a felhasználóknak minden hónapban újra el kell érvényt adniuk.  |

   Ha például a lejárati dátumot január **1-re,** a gyakoriságot pedig Havira, akkor két felhasználó lejárati ideje a következő lehet: 

   | Felhasználó | Első elfogadás dátuma | Első lejárati dátum | Második lejárati dátum | Harmadik lejárati dátum |
   | --- | --- | --- | --- | --- |
   | Alice | Jan. 1. | Február 1. | Március 1. | 1. április |
   | Bob | Jan. 15. | Február 1. | Március 1. | 1. április |

1. Használja a **Duration before reacceptance requires (days) (Időtartam** az újravélkedés előtt) beállítást annak megadásához, hogy hány nap után kell a felhasználónak újra eltennie a használati feltételeket. Ez lehetővé teszi, hogy a felhasználók a saját ütemezésüket kövessék. Ha például az időtartamot **30** napra állítva adja meg, két felhasználó elévülési ideje a következő lehet:

   | Felhasználó | Első elfogadás dátuma | Első lejárati dátum | Második lejárati dátum | Harmadik lejárati dátum |
   | --- | --- | --- | --- | --- |
   | Alice | Jan. 1. | Jan 31 | Március 2. | 1. április |
   | Bob | Jan. 15. | Február 14. | Március 16. | 15. április |

   Az Elévülő  jóváhagyások és az Időtartam együttesen is használhatók, mielőtt az átvészés megköveteli a **(nap)** beállításokat, de általában az egyiket vagy a másikat kell használni.

1. A **Feltételes hozzáférés alatt** használja a Kényszerítés **feltételes** hozzáféréssel szabályzatsablonlistát a használati feltételek szabályzatának kikényszeríteni kívánt sablon kiválasztásához.

    ![Feltételes hozzáférés legördülő lista szabályzatsablon kiválasztásához](./media/terms-of-use/conditional-access-templates.png)

   | Sablon | Leírás |
   | --- | --- |
   | **Hozzáférés a felhőalkalmazáshoz minden vendég számára** | A rendszer létrehoz egy feltételes hozzáférési szabályzatot az összes vendég és az összes felhőalkalmazás számára. Ez a szabályzat hatással van a Azure Portal. A létrehozás után előfordulhat, hogy ki kell jelentkeznie és be kell jelentkeznie. |
   | **Hozzáférés a felhőalapú alkalmazásokhoz minden felhasználó számára** | A rendszer létrehoz egy feltételes hozzáférési szabályzatot az összes felhasználóhoz és minden felhőalkalmazáshoz. Ez a szabályzat hatással van a Azure Portal. A létrehozás után ki kell jelentkeznie, és be kell jelentkeznie. |
   | **Egyéni szabályzat** | Válassza ki azokat a felhasználókat, csoportokat és alkalmazásokat, amelyekre a használati feltételek szabályzat vonatkozni fog. |
   | **Feltételes hozzáférési szabályzat létrehozása később** | Ez a használati feltételek szabályzat a feltételes hozzáférési szabályzat létrehozásakor megjelenik a hozzáférés-vezérlési listában. |

   >[!IMPORTANT]
   >A feltételes hozzáférési szabályzat vezérlői (beleértve a használati feltételeket is) nem támogatják a szolgáltatásfiókok kényszerítét. Javasoljuk, hogy minden szolgáltatásfiókot kizárjon a feltételes hozzáférési szabályzatból.

    Az egyéni feltételes hozzáférési szabályzatok lehetővé teszik a használati szabályzatok részletes használati feltételeit egy adott felhőalkalmazásra vagy felhasználói csoportra. További információ: Rövid útmutató: A használati feltételek elfogadottnak megkövetelása [a felhőalkalmazások elérése előtt.](require-tou.md)

1. Kattintson a **Létrehozás** lehetőségre.

    Ha egyéni feltételes hozzáférési sablont választott, megjelenik egy új képernyő, amely lehetővé teszi az egyéni feltételes hozzáférési szabályzat létrehozásához.

   ![Új feltételes hozzáférés panel, ha az egyéni feltételes hozzáférési szabályzat sablonját választotta](./media/terms-of-use/custom-policy.png)

   Most meg kell jelenni az új használati feltételek szabályzatának.

   ![A használati feltételek panelen felsorolt új használati feltételek](./media/terms-of-use/create-tou.png)

## <a name="view-report-of-who-has-accepted-and-declined"></a>Jelentés megtekintése az elfogadó és elutasítóról

A használati feltételek panel megjeleníti a feltételeket elfogadó, illetve elutasító felhasználók számát. Ezeket a darabszámokat és az elfogadó/elutasító adatokat a használati feltételekre vonatkozó szabályzat teljes életciklusa során tárolja a szolgáltatás.

1. Jelentkezzen be az Azure-ba, és lépjen a **Használati feltételekre** a [https://aka.ms/catou](https://aka.ms/catou) címen.

    ![Használati feltételek panelen, amely az elfogadott és elutasított felhasználói megjelenítések számát sorolja fel](./media/terms-of-use/view-tou.png)

1. A használati feltételekre vonatkozó szabályzatok megtekintéséhez kattintson az **Elfogadva** vagy Elutasítva alatti számokra a felhasználók aktuális állapotának megtekintéséhez. 

    ![Használati feltételek jóváhagyások panel az elfogadott felhasználókat listázása](./media/terms-of-use/accepted-tou.png)

1. Egy adott felhasználó előzményeinek megtekintéséhez kattintson a három pontra (**...**), majd az **Előzmények megtekintése elemre.**

    ![Felhasználó Előzmények megtekintése helyi menüje](./media/terms-of-use/view-history-menu.png)

   A Nézetelőzmények panelen megtekintheti az összes elfogadási, elutasítási és lejárati előzményt.

   ![Az Előzmények megtekintése panel felsorolja a felhasználó által elfogadott, elutasított és lejárati előzményeket](./media/terms-of-use/view-history-pane.png)

## <a name="view-azure-ad-audit-logs"></a>Azure AD-auditnaplók megtekintése

Ha további tevékenységeket szeretne megtekinteni, az Azure AD használati feltételek szabályzata auditnaplókat is tartalmaz. Minden felhasználói jóváhagyás aktivál egy eseményt az auditnaplókban, amely **30 napig van tárolva.** Ezeket a naplókat megtekintheti a portálon, vagy letöltheti egy .csv fájlban.

Az Azure AD-auditnaplók használatának első lépésekhez használja az alábbi eljárást:

1. Jelentkezzen be az Azure-ba, és lépjen a **Használati feltételekre** a [https://aka.ms/catou](https://aka.ms/catou) címen.
1. Válasszon ki egy használati feltételekre vonatkozó szabályzatot.
1. Kattintson a **Naplók megtekintése** elemre.

    ![Használati feltételek panelt az Auditnaplók megtekintése lehetőség kiemelésével](./media/terms-of-use/audit-tou.png)

1. Az Azure AD auditnaplók képernyőjén a megadott listák segítségével szűrheti az adatokat adott auditnapló-információk megcélzára.

    A **Letöltés** elemre kattintva az információkat helyi felhasználás céljából egy .csv fájlba is mentheti.

   ![Az Azure AD-auditnaplók képernyője, amely a dátumot, a cél szabályzatot, a kezdeményezőt és a tevékenységet sorolja fel](./media/terms-of-use/audit-logs-tou.png)

   Ha egy naplóra kattint, megjelenik egy panel, amely további tevékenységadatokat tartalmaz.

   ![Tevékenység részletei egy tevékenységnaplóhoz, amely a tevékenység állapotát, a kezdeményezőt, a cél szabályzatot mutatja](./media/terms-of-use/audit-log-activity-details.png)

## <a name="what-terms-of-use-looks-like-for-users"></a>A használati feltételek a felhasználók számára

A toU-szabályzat létrehozása és kényszerítése után a hatókörbe tartozó felhasználók a következő képernyőt látják a bejelentkezés során.

![Példa használati feltételekre, amelyek akkor jelennek meg, amikor egy felhasználó bejelentkezik](./media/terms-of-use/user-tou.png)

A felhasználók megtekinthetik a használati feltételeket, és szükség esetén gombokat használhatnak a nagyításhoz és kicsinyítéshez.

![A nagyítási gombokkal használható használati feltételek megtekintése](./media/terms-of-use/zoom-buttons.png)

Az alábbi képernyő bemutatja, hogyan néz ki a toU-szabályzat a mobileszközökön.

![Példa használati feltételekre, amelyek akkor jelennek meg, amikor egy felhasználó bejelentkezik egy mobileszközre](./media/terms-of-use/mobile-tou.png)

A felhasználóknak csak egyszer kell elfogadniuk a használati feltételeket, és a későbbi bejelentkezések során nem fogják látni a használati feltételeket.

### <a name="how-users-can-review-their-terms-of-use"></a>Használati feltételek áttekintése a felhasználók számára

A felhasználók az alábbi eljárással áttekintheti és láthatják az elfogadott használati feltételeket.

1. Jelentkezzen be itt: [https://myapps.microsoft.com](https://myapps.microsoft.com).
1. A jobb felső sarokban kattintson a nevére, és válassza a **Profil lehetőséget.**

    ![MyApps-webhely megnyitott felhasználói panelvel](./media/terms-of-use/tou14.png)

1. A Profi lapon kattintson a **Használati feltételek áttekintése** elemre.

    ![Felhasználó profiloldala a Használati feltételek áttekintése hivatkozással](./media/terms-of-use/tou13a.png)

1. Itt áttekintheti az elfogadott használati feltételeket.

## <a name="edit-terms-of-use-details"></a>Használati feltételek részleteinek szerkesztése

Szerkesztheti a használati feltételekre vonatkozó szabályzatok egyes részleteit, de meglévő dokumentumot nem módosíthat. Az alábbi eljárás a részletek szerkesztését ismerteti.

1. Jelentkezzen be az Azure-ba, és lépjen a **Használati feltételekre** a [https://aka.ms/catou](https://aka.ms/catou) címen.
1. Válassza ki a szerkeszteni kívánt használati feltételeket.
1. Kattintson a **Feltételek szerkesztése elemre.**
1. A Használati feltételek szerkesztése panelen a következőket módosíthatja:
    - **Név** – a végfelhasználóval nem megosztott tou belső neve
    - **Megjelenített név** – ez a név jelenik meg a végfelhasználók számára a felhasználói tartalom megtekintésekor
    - **A használati feltételek kibővítését** követeli meg a felhasználóktól – Ha ezt a beállítást **Be-be** írják, a használati feltételeket a használati feltételek dokumentumának elfogadásához kell kiterjesztenie.
    - (Előzetes verzió) Frissíthet **egy meglévő használati feltételeket dokumentáló** dokumentumot
    - Nyelvet hozzáadhat egy meglévő tou-hoz

   Ha más beállításokat is módosítani szeretne (például PDF-dokumentumot, amelyek megkövetelik a felhasználóktól, hogy minden eszközhöz jóváhagyást adjanak, elévülnek a jóváhagyások, a jóváhagyás előtti időtartam vagy a feltételes hozzáférési szabályzatot, létre kell hoznia egy új toU-szabályzatot.

    ![Különböző nyelvi beállításokat megjelenítő szerkesztés ](./media/terms-of-use/edit-terms-use.png)

1. Ha végzett, kattintson a **Mentés gombra** a módosítások mentéshez.

## <a name="update-the-version-or-pdf-of-an-existing-terms-of-use"></a>Meglévő használati feltételek verziójának vagy PDF-ének frissítése

1.  Jelentkezzen be az Azure-ba, és lépjen a [Használati feltételek](https://aka.ms/catou)
2.  Válassza ki a szerkeszteni kívánt használati feltételeket.
3.  Kattintson a **Feltételek szerkesztése elemre.**
4.  Ahhoz a nyelvhez, amelyről frissíteni szeretné az új verziót, kattintson az **Update** (Frissítés) elemre a műveletoszlop alatt

    ![A Használati feltételek szerkesztése panel a név és a kibontás lehetőséggel](./media/terms-of-use/edit-terms-use.png)

5.  A jobb oldali panelen töltse fel az új verzió PDF-fájlát
6.  Itt egy váltógombot is  meg kell követelni, ha azt szeretné, hogy a felhasználók a következő bejelentkezéskor elfogadják ezt az új verziót. Ha a felhasználóknak újra el kell fogadniuk a kérést, a rendszer a következő alkalommal, amikor megpróbálják elérni a feltételes hozzáférési szabályzatban meghatározott erőforrást, a rendszer kérni fogja őket, hogy fogadják el ezt az új verziót. Ha nem követeli meg a felhasználóktól az új jóváhagyást, a korábbi hozzájárulásuk aktuális marad, és csak azok az új felhasználók fogják látni az új verziót, akik még nem járultak hozzá a jóváhagyáshoz, vagy akiknek a beleegyezése lejárt.

    ![A használati feltételek szerkesztése – újrabefogadás lehetőség kiemelve](./media/terms-of-use/re-accept.png)

7.  Miután feltöltötte az új PDF-fájlt, és az újraaccept mellett döntött, kattintson a panel alján található Hozzáadás gombra.
8.  A Dokumentum oszlopban most a legújabb verzió látható.

## <a name="view-previous-versions-of-a-tou"></a>A toU korábbi verzióinak megtekintése

1.  Jelentkezzen be az Azure-ba, és lépjen a **Használati feltételekre** a https://aka.ms/catou címen.
2.  Válassza ki azokat a használati feltételeket, amelyekhez meg szeretné tekinteni a verzióelőzményeket.
3.  Kattintson a **Nyelvek és verzióelőzmények elemre**
4.  Kattintson a **Korábbi verziók gombra.**

    ![dokumentum részletei, beleértve a nyelvi verziókat](./media/terms-of-use/document-details.png)

5.  A verzió letöltéséhez kattintson a dokumentum nevére.

## <a name="see-who-has-accepted-each-version"></a>Tekintse meg, hogy ki fogadta el az egyes verziókat

1.  Jelentkezzen be az Azure-ba, és lépjen a **Használati feltételekre** a https://aka.ms/catou címen.
2.  Annak ellenőrzéshez, hogy jelenleg ki fogadta el a feltételeket, kattintson a kívánt feltételek **Elfogadva** oszlopában található számra.
3.  Alapértelmezés szerint a következő oldal a felhasználói feltételek elfogadásának aktuális állapotát mutatja
4.  Ha szeretné látni a korábbi hozzájárulási eseményeket, válassza az Összes lehetőséget **az** Aktuális állapot **legördülő** menüből. Most már láthatja az egyes felhasználói eseményeket az egyes verziók és a történtek részleteiben.
5.  Másik lehetőségként kiválaszthat egy adott  verziót a Verzió legördülő menüből, hogy lássa, ki fogadta el az adott verziót.


## <a name="add-a-tou-language"></a>ToU nyelv hozzáadása

Az alábbi eljárás a toU nyelv hozzáadását ismerteti.

1. Jelentkezzen be az Azure-ba, és lépjen a **Használati feltételekre** a [https://aka.ms/catou](https://aka.ms/catou) címen.
1. Válassza ki a szerkeszteni kívánt használati feltételeket.
1. Kattintson a **Feltételek szerkesztése elemre**
1. Kattintson **a lap** alján található Nyelv hozzáadása gombra.
1. A Használati feltételek hozzáadása nyelvi panelen töltse fel a honosított PDF-fájlt, és válassza ki a nyelvet.

    ![Használati feltételek kijelölve, és a Részletek panel Nyelvek lapjának megjelenítése](./media/terms-of-use/select-language.png)

1. Kattintson **a Nyelv hozzáadása elemre.**
1. Kattintson a **Mentés** gombra

1. A **nyelv hozzáadásához** kattintson a Hozzáadás gombra.

## <a name="per-device-terms-of-use"></a>Eszközönkénti használati feltételek

A **Require users to consent on every device** (Felhasználói jóváhagyás megkövetelása minden eszközhöz) beállítással megkövetelheti a végfelhasználóktól a használati feltételek elfogadását minden eszközön, amelyről hozzáférnek. A végfelhasználónak regisztrálnia kell az eszközét az Azure AD-ban. Az eszköz regisztrálva van, az eszközazonosítóval érvényesíthető a használati feltételek szabályzata az egyes eszközökre.

Az alábbi lista a támogatott platformokat és szoftvereket tartalmazza.

> [!div class="mx-tableFixed"]
> |  | iOS | Android | Windows 10 | Egyéb |
> | --- | --- | --- | --- | --- |
> | **Natív alkalmazás** | Igen | Igen | Igen |  |
> | **Microsoft Edge** | Igen | Igen | Igen |  |
> | **Internet Explorer** | Igen | Igen | Igen |  |
> | **Chrome (bővítvekkel)** | Igen | Igen | Igen |  |

Az eszközönkénti használati feltételekre a következő korlátozások vannak érvényben:

- Egy eszköz csak egy bérlőhöz használhatja.
- A felhasználónak engedéllyel kell rendelkeznie az eszközhöz való csatlakozáshoz.
- Az Intune-regisztráció alkalmazás nem támogatott. Győződjön meg arról, hogy ki van zárva minden olyan feltételes hozzáférési szabályzatból, amely használati feltételekre vonatkozó szabályzatot igényel.
- Az Azure AD B2B-felhasználók nem támogatottak.

Ha a felhasználó eszköze nincs beléptetve, egy üzenetet kap, hogy csatlakoznia kell az eszközéhez. A felhasználói élményük a platformtól és a szoftverektől függ.

### <a name="join-a-windows-10-device"></a>Windows 10-es eszköz csatlakoztatása

Ha egy felhasználó az Windows 10 és Microsoft Edge, az alábbihoz hasonló üzenetet kap az [eszközhöz való csatlakozáshoz.](../user-help/user-help-join-device-on-network.md#to-join-an-already-configured-windows-10-device)

![Windows 10 és Microsoft Edge – Üzenet, amely jelzi, hogy az eszközt regisztrálni kell](./media/terms-of-use/per-device-win10-edge.png)

Ha Chrome böngészőt használ, a rendszer kérni fogja, hogy telepítse a [Windows 10 Accounts bővítményt.](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji)

### <a name="register-an-ios-device"></a>iOS-eszköz regisztrálása

Ha a felhasználó iOS-eszközt használ, a rendszer felkéri a Microsoft Authenticator [telepítésére.](https://apps.apple.com/us/app/microsoft-authenticator/id983156458)

### <a name="register-an-android-device"></a>Android-eszköz regisztrálása

Ha a felhasználó Android-eszközt használ, a rendszer kérni fogja, hogy telepítse [a Microsoft Authenticator alkalmazást.](https://play.google.com/store/apps/details?id=com.azure.authenticator)

### <a name="browsers"></a>Böngészők

Ha egy felhasználó nem támogatott böngészőt használ, a rendszer egy másik böngészőt fog kérni.

![Az eszköz regisztrációját jelző üzenet, de a böngésző nem támogatott](./media/terms-of-use/per-device-browser-unsupported.png)

## <a name="delete-terms-of-use"></a>Használati feltételek törlése

A régi használati feltételeket az alábbi eljárással törölheti.

1. Jelentkezzen be az Azure-ba, és lépjen a **Használati feltételekre** a [https://aka.ms/catou](https://aka.ms/catou) címen.
1. Válassza ki az eltávolítani kívánt használati feltételeket.
1. Kattintson a **Feltételek törlése** lehetőségre.
1. A megjelenő üzenetben, amely a törlés megerősítését kéri, kattintson az **Igen** lehetőségre.

    ![A használati feltételek törlésének megerősítését kérő üzenet](./media/terms-of-use/delete-tou.png)

   Többé nem láthatja a használati feltételekre vonatkozó szabályzatot.

## <a name="user-acceptance-record-deletion"></a>Felhasználói elfogadási rekord törlése

A felhasználói elfogadási rekordok törölve vannak:

- Amikor a rendszergazda explicit módon törli a toU-t. Ha ez történik, az adott feltételekhez társított összes elfogadási rekord is törlődik.
- Ha a bérlő elveszíti a prémium szintű Azure Active Directory licencét.
- A bérlő törlésekor.

## <a name="policy-changes"></a>A szabályzatok változásai

A feltételes hozzáférési szabályzatok azonnal életbe lépnek. Ebben az esetben a rendszergazda "szomorú felhőket" vagy "Azure AD-jogkivonatokkal kapcsolatos problémákat" lát. A rendszergazdának ki kell jelentkeznie, majd újra be kell jelentkeznie, hogy megfeleljen az új szabályzatnak.

> [!IMPORTANT]
> Az érintett felhasználóknak újra be kell jelentkezniük, hogy megfelelhessenek az új szabályzatnak, ha:
>
> - a feltételes hozzáférési szabályzat engedélyezve van a használati feltételekre vonatkozó szabályzatban
> - vagy egy második használati feltételekre vonatkozó szabályzat jön létre

## <a name="b2b-guests"></a>B2B vendégek

A legtöbb szervezetnél van egy folyamat, amely során az alkalmazottak beleegyeznek a szervezet használati helyére, illetve adatvédelmi nyilatkozatukba. De hogyan kényszerítheti ki ugyanezeket a hozzájárulásokat az Azure AD vállalaton keresztüli (B2B) vendégei számára, ha a SharePoint vagy a Teams használatával osztják meg őket? A feltételes hozzáférés és a használati feltételek szabályzatával közvetlenül kikényszeríteni tudja a szabályzatot a B2B vendégfelhasználók számára. A meghívó érvényesítési folyamat során a felhasználó számára jelennek meg a használati feltételek szabályzata. Ez a támogatás jelenleg előzetes verzióban érhető el.

Használati feltételek szabályzatok csak akkor jelennek meg, ha a felhasználó vendégfiókkal rendelkezik az Azure AD-ban. A SharePoint Online [](/sharepoint/what-s-new-in-sharing-in-targeted-release) jelenleg alkalmi külső megosztási címzettekkel rendelkezik egy olyan dokumentum vagy mappa megosztásához, amely nem követeli meg, hogy a felhasználó vendégfiókkal rendelkezik. Ebben az esetben a használati feltételekre vonatkozó szabályzat nem jelenik meg.

![Felhasználók és csoportok panel – A Minden vendégfelhasználó beállítás be van jelölve a Beszúrás lapon](./media/terms-of-use/b2b-guests.png)

## <a name="support-for-cloud-apps"></a>Felhőalkalmazások támogatása

Használati feltételek szabályzatok különböző felhőalkalmazások, például felhőalkalmazások, például Azure Information Protection és Microsoft Intune. Ez a támogatás jelenleg előzetes verzióban érhető el.

### <a name="azure-information-protection"></a>Azure Information Protection

Feltételes hozzáférési szabályzatot konfigurálhat a Azure Information Protection alkalmazáshoz, és használati feltételeket követelhet meg, amikor egy felhasználó egy védett dokumentumhoz fér hozzá. Ez aktivál egy használati feltételeket, mielőtt a felhasználó első alkalommal hozzáfér egy védett dokumentumhoz.

![Felhőalkalmazások panel a kiválasztott Microsoft Azure Information Protection alkalmazással](./media/terms-of-use/cloud-app-info-protection.png)

### <a name="microsoft-intune-enrollment"></a>Microsoft Intune regisztráció

Feltételes hozzáférési szabályzatot konfigurálhat az Microsoft Intune Enrollment alkalmazáshoz, és az eszköz Intune-beli regisztrálása előtt használati feltételekre van szükség. További információkért tekintse meg a Szervezet számára megfelelő feltételek megoldásának kiválasztása a szervezet számára [blogbejegyzést.](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409)

![Felhőalkalmazások panel a kiválasztott Microsoft Intune alkalmazással](./media/terms-of-use/cloud-app-intune.png)

> [!NOTE]
> Az Intune-regisztráció alkalmazás nem támogatott az [eszközönkénti használati feltételekhez.](#per-device-terms-of-use)

## <a name="frequently-asked-questions"></a>Gyakori kérdések

**K: Nem tudok bejelentkezni a PowerShell használatával, ha a használati feltételek engedélyezve vannak.**<br />
A: Használati feltételek csak az interaktív hitelesítéskor fogadható el.

**K: Hogyan,mikor/ha egy felhasználó elfogadta a használati feltételeket?**<br />
A: A Használati feltételek panelen kattintson az Accepted (Elfogadva) **alatt a számra.** Az elfogadási tevékenységet az Azure AD auditnaplóiban is megtekintheti vagy keresheti. További információ: View report of who has accepted and declined (Az elfogadó és elutasító felhasználóról szóló jelentés megtekintése) és View [Azure AD audit logs (Azure AD-auditnaplók megtekintése).](#view-azure-ad-audit-logs)

**K: Mennyi ideig tárolódnak az információk?**<br />
A: A használati feltételekben megszámolnak a felhasználók, és a használati feltételek teljes élettartamán át tárolják, hogy ki fogadta el vagy nem fogadta el őket. Az Azure AD-auditnaplók 30 napig vannak tárolva.

**K: Miért látok eltérő számú jóváhagyást a használati feltételekről szóló jelentésben, és miért nem az Azure AD auditnaplóiban?**<br />
A: A használati feltételekről szóló jelentést a használati feltételekkel kapcsolatos szabályzat teljes élettartama alatt tárolja a rendszer, az Azure AD auditnaplóit pedig 30 napig tárolja a rendszer. Emellett a használati feltételekről szóló jelentés csak a felhasználók aktuális hozzájárulási állapotát jeleníti meg. Ha például egy felhasználó elutasítja, majd elfogadja azt, a használati feltételekről szóló jelentés csak az adott felhasználó elfogadására fog mutatni. Ha meg kell látnia az előzményeket, használhatja az Azure AD auditnaplóit.

**K: Ha szerkesztem a használati feltételekre vonatkozó szabályzat részleteit, a felhasználóknak újra el kell fogadniuk a feltételeket?**<br />
V: Nem, ha egy rendszergazda a használati feltételekre vonatkozó szabályzat részleteit szerkeszti (név, megjelenítendő név, a felhasználóknak ki kell bontani vagy hozzá kell adni egy nyelvet), nem követeli meg a felhasználóktól, hogy újra el kell látni az új feltételeket.

**K: Frissíthetek egy meglévő használati feltételekkel kapcsolatos szabályzatdokumentumot?**<br />
A: Jelenleg nem frissíthet egy meglévő használati feltételekkel kapcsolatos szabályzatdokumentumot. A használati feltételekkel kapcsolatos szabályzatdokumentumok módosításhoz létre kell hoznia egy új használati feltételeket házirendpéldányt.

**K: Ha a hivatkozások a használati feltételekkel kapcsolatos szabályzat PDF-dokumentumában vannak, a végfelhasználók kattint tudnak rájuk?**<br />
VÁLASZ: Igen, a végfelhasználók további oldalak hivatkozásait is kiválaszthatják, de a dokumentum szakaszaira mutató hivatkozások nem támogatottak. Emellett a használati szabályzatban használt hivatkozások PDF-fájlok nem működnek, ha az Azure AD MyApps/MyAccount portálról férnek hozzá.

**K: A használati feltételek több nyelvet is támogatnak?**<br />
V: Igen. Jelenleg 108 különböző nyelvet konfigurálhat a rendszergazda egyetlen használati feltételekre vonatkozó szabályzathoz. Egy rendszergazda több PDF-dokumentumot is feltölthet, és felcímkézheti ezeket a dokumentumokat egy megfelelő nyelvvel (legfeljebb 108). Amikor a végfelhasználók bejelentkeznek, megnézzük a böngésző nyelvi beállítását, és megjelenítjük az egyező dokumentumot. Ha nincs egyezés, az alapértelmezett dokumentumot jelenítjük meg, amely az első feltöltött dokumentum.

**K: Mikor aktiválódnak a használati feltételekre vonatkozó szabályzatok?**<br />
A: A használati feltételeket a bejelentkezési folyamat aktiválja.

**K: Milyen alkalmazásokra célozhatja meg a használati feltételek szabályzatát?**<br />
A: Feltételes hozzáférési szabályzatot modern hitelesítéssel hozhat létre a vállalati alkalmazásokhoz. Bővebb információ: [vállalati alkalmazások](./../manage-apps/view-applications-portal.md).

**K: Több használati feltételt is hozzáadhatok egy adott felhasználóhoz vagy alkalmazáshoz?**<br />
A: Igen, több feltételes hozzáférési szabályzat létrehozásával, amelyek ezeket a csoportokat vagy alkalmazásokat célják meg. Ha egy felhasználó több használati feltételekre vonatkozó szabályzat hatókörére is kiterjed, egyszerre egy használati feltételt fogad el.

**K: Mi történik, ha egy felhasználó elutasítja a használati feltételeket?**<br />
V: Ebben az esetben a felhasználón nem kaphat hozzáférést az alkalmazáshoz. A felhasználónak újra be kell jelentkeznie, és el kell fogadnia a feltételeket a hozzáféréshez.

**K: Lehetséges egy korábban elfogadott használati feltételekre vonatkozó szabályzatot nem elfogadni?**<br />
A: Áttekintheti [a korábban](#how-users-can-review-their-terms-of-use)elfogadott használati feltételeket, de jelenleg nincs mód az el nem fogadható szabályzatok áttekintésére.

**K: Mi történik, ha az Intune használati feltételeit is használom?**<br />
A: Ha az Azure AD használati feltételeit és az [Intune](/intune/terms-and-conditions-create)használati feltételeit is konfigurálta, a felhasználónak mindkét feltételt el kell fogadnia. További információkért tekintse meg a Szervezet számára megfelelő feltételek megoldásának kiválasztása [blogbejegyzést.](https://go.microsoft.com/fwlink/?linkid=2010506&clcid=0x409)

**K: Milyen végpontokat használ a használati feltételek a hitelesítéshez?**<br />
A: Használati feltételek a következő végpontokat használja a hitelesítéshez: https://tokenprovider.termsofuse.identitygovernance.azure.com és https://account.activedirectory.windowsazure.com . Ha a szervezet rendelkezik a regisztrációhoz szükséges URL-címek listájával, ezeket a végpontokat hozzá kell adni az engedélyező listához, valamint az Azure AD-végpontokat a bejelentkezéshez.

## <a name="next-steps"></a>Következő lépések

- [Rövid útmutató: Használati feltételek elfogadottnak kell lennie a felhőalkalmazások elérése előtt](require-tou.md)
