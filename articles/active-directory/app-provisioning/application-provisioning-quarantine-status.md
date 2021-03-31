---
title: Karanténba helyezés alkalmazás-létesítési állapota | Microsoft Docs
description: Amikor konfigurált egy alkalmazást a felhasználók automatikus kiépítési felállításához, megtudhatja, mi a karanténba helyezési állapot, és hogyan törölheti azt.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: troubleshooting
ms.date: 03/18/2021
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 49590c46588ad0d0f1c1b7b095679a3c3fce96eb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104579501"
---
# <a name="application-provisioning-in-quarantine-status"></a>Alkalmazás üzembe helyezése a karantén állapotában

Az Azure AD-kiépítési szolgáltatás figyeli a konfiguráció állapotát. Nem kifogástalan állapotú alkalmazásokat is elhelyez a "karantén" állapotban. Ha a célként megadott rendszerre irányuló hívások többsége vagy mindegyike következetesen meghiúsul, akkor a kiépítési feladat karanténba helyezéssel van megjelölve. Egy hiba például érvénytelen rendszergazdai hitelesítő adatok miatt kapott hibaüzenet.

Karanténba helyezés közben:
- A növekményes ciklusok gyakorisága naponta egyszer csökken.
- A kiépítési feladatot a rendszer eltávolítja a karanténból az összes hiba javítása és a következő szinkronizálási ciklus elindítása után. 
- Ha a kiépítési feladatok több mint négy hétig maradnak karanténban, a kiépítési feladatok le vannak tiltva (a rendszer leáll).

## <a name="how-do-i-know-if-my-application-is-in-quarantine"></a>Hogyan tudni, hogy az alkalmazás karanténban van-e?

Három módon ellenőrizhető, hogy egy alkalmazás karanténba helyezve van-e:
  
- A Azure Portal Azure Active Directory navigáljon a   >  **vállalati alkalmazások**  >  &lt; *alkalmazás neve* &gt;  >  **kiépítés** elemre, és tekintse át a karanténba helyezett üzenet folyamatjelző sávját.   

  ![Üzembe helyezési állapotsor, amely a karantén állapotát mutatja](./media/application-provisioning-quarantine-status/progress-bar-quarantined.png)

- A Azure Portal navigáljon **Azure Active Directory**  >  **naplók** > szűrés **tevékenység: karanténba helyezés** , és tekintse át a karanténba helyezési előzményeket. A fent ismertetett folyamatjelzőn látható nézet azt mutatja, hogy a kiépítés jelenleg karanténban van-e. A naplók az alkalmazások karanténba helyezési előzményeit jelenítik meg. 

- Használja a Microsoft Graph kérelmet a [synchronizationJob beszerzéséhez](/graph/api/synchronization-synchronizationjob-get?tabs=http&view=graph-rest-beta&preserve-view=true) a kiépítési feladatok állapotának programozott beszerzéséhez:

```microsoft-graph
        GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/
```

- Nézze meg az e-mailjeit. Ha egy alkalmazás Karanténba kerül, egy egyszeri értesítő e-mailt kap. Ha a karanténba helyezés oka megváltozik, a rendszer egy frissített e-mailt küld, amely a karanténba helyezés új okát mutatja. Ha nem lát e-mailt:

  - Győződjön meg arról, hogy érvényes **értesítő e-mailt** adott meg az alkalmazás kiépítési konfigurációjában.
  - Győződjön meg róla, hogy nincs levélszemét-szűrés az értesítő e-mailek mappájában.
  - Győződjön meg arról, hogy nincs leiratkozott az e-mailekben.
  - E-mailek keresése a következőből: `azure-noreply@microsoft.com`

## <a name="why-is-my-application-in-quarantine"></a>Miért van a karanténba helyezett alkalmazásom?

|Leírás|Javasolt művelet|
|---|---|
|**Scim megfelelőségi probléma:** HTTP/404 nem található válasz lett visszaadva a várt HTTP/200 OK válasz helyett. Ebben az esetben az Azure AD kiépítési szolgáltatása kérelmet küldött a célalkalmazás számára, és váratlan választ kapott.|Keresse meg a rendszergazdai hitelesítő adatok szakaszt. Ellenőrizze, hogy az alkalmazás a bérlői URL-cím megadását igényli-e, és hogy az URL-cím helyes-e. Ha nem jelenik meg a probléma, forduljon az alkalmazás fejlesztői szolgálatához, és győződjön meg arról, hogy a szolgáltatásuk SCIM-kompatibilis. https://tools.ietf.org/html/rfc7644#section-3.4.2 |
|**Érvénytelen hitelesítő adatok:** Ha engedélyezi a hozzáférést a célalkalmazás számára, a rendszer választ kapott a megcélzott alkalmazástól, amely jelzi, hogy a megadott hitelesítő adatok érvénytelenek.|Navigáljon a létesítési konfiguráció felhasználói felületének rendszergazdai hitelesítő adatok szakaszához, és engedélyezze újra a hozzáférést érvényes hitelesítő adatokkal. Ha az alkalmazás szerepel a katalógusban, tekintse át az alkalmazás konfigurálására vonatkozó oktatóanyagot a már szükséges lépések elvégzéséhez.|
|**Ismétlődő szerepkörök:** Az egyes alkalmazásokból (például a Salesforce és a zendesk) importált szerepköröknek egyedieknek kell lenniük. |Navigáljon az alkalmazás [jegyzékfájlhoz](../develop/reference-app-manifest.md) a Azure Portalban, és távolítsa el az ismétlődő szerepkört.|

 A kiépítési feladatok állapotának beolvasására Microsoft Graph kérelem a karanténba helyezés következő okát mutatja be:
- `EncounteredQuarantineException` azt jelzi, hogy érvénytelen hitelesítő adatok lettek megadva. A kiépítési szolgáltatás nem tud kapcsolatot létesíteni a forrásrendszer és a célként megadott rendszer között.
- `EncounteredEscrowProportionThreshold` azt jelzi, hogy a kiépítés túllépte a letéti küszöbértéket. Ez az állapot akkor fordul elő, ha a kiépítési események több mint 40%-a nem sikerült. További információkért lásd alább a letéti küszöbértékek részleteit.
- `QuarantineOnDemand` azt jelenti, hogy az alkalmazással kapcsolatos problémát észlelt, és manuálisan beállította a karanténba helyezését.

**Letéti küszöbértékek**

Ha a arányos letéti küszöbérték teljesül, a kiépítési feladatok karanténba kerülnek. Ez a logika változhat, de nagyjából az alábbiak szerint működik: 

A feladatok a karanténba helyezhetők, függetlenül a hibák számától, például a rendszergazdai hitelesítő adatoktól vagy a SCIM megfelelőségtől. Általánosságban azonban a 5 000-es hibák miatt a rendszer megkezdi az értékelést, hogy túl sok hibát okoz-e a karanténba helyezve. Például az 4 000-es hibákkal rendelkező feladatok nem kerülnek karanténba. Az 5 000-es hibákkal rendelkező feladatok azonban kiértékelést indítanak. A kiértékelés a következő feltételeket használja:  
- Ha a kiépítési események több mint 40%-a meghibásodik, vagy több mint 40 000 hiba történt, a kiépítési feladat Karanténba kerül. A hivatkozási hibákat a 40%-os küszöbérték vagy a 40 000-es küszöbérték részeként nem számítjuk fel. Például a kezelő vagy a csoporttag frissítése nem sikerült.
- Egy olyan feladatot, amelyben az 45 000-felhasználók sikertelenül lettek kiépítve, a karanténhoz vezetne, mivel az meghaladja a 40 000 küszöbértéket.
- Egy olyan feladatokhoz, amelyekben a 30 000-felhasználók sikertelenek voltak, és a 5 000 sikeres volt, a karanténhoz vezethetnek, mivel az meghaladja a 40%-os küszöbértéket és a 5 000 minimális
- Az 20 000-es hibákkal és a 100 000-es sikerrel kapcsolatos feladatok nem kerülnek be a karanténba, mert a probléma nem lépi túl a 40%-os meghibásodási küszöbértéket vagy az 40 000-es hibát  
- Az 60 000-es abszolút küszöbérték a hivatkozási és a nem hivatkozó hibákra is kiterjed. Például 40 000 felhasználót nem sikerült kiépíteni, és a 21 000 Manager frissítései sikertelenek voltak. A teljes érték 61 000, és meghaladja a 60 000 korlátot.


## <a name="how-do-i-get-my-application-out-of-quarantine"></a>Hogyan az alkalmazás karanténba helyezését?

Először javítsa ki azt a problémát, amely az alkalmazás karanténba helyezését okozta.

- Ellenőrizze az alkalmazás kiépítési beállításait, és győződjön meg arról, hogy [érvényes rendszergazdai hitelesítő adatokat](../app-provisioning/configure-automatic-user-provisioning-portal.md#configuring-automatic-user-account-provisioning)adott meg. Az Azure AD-nek megbízhatósági kapcsolatot kell létesítenie a célalkalmazás alkalmazásával. Győződjön meg arról, hogy érvényes hitelesítő adatokat adott meg, és a fiókja rendelkezik a szükséges engedélyekkel.

- Tekintse át a [kiépítési naplókat](../reports-monitoring/concept-provisioning-logs.md) , és vizsgálja meg, hogy milyen hibák okozzák a karanténba helyezést, és oldja meg a hibát. A tevékenység szakaszban keresse fel **Azure Active Directory** &gt; **vállalati alkalmazások** &gt; **kiépítési naplóit (előzetes verzió)** . 

A probléma megoldása után indítsa újra a kiépítési feladatot. Az alkalmazás üzembe helyezési beállításainak (például az attribútum-hozzárendelések vagy a hatóköri szűrők) bizonyos módosításai automatikusan újraindulnak a kiépítés során. Az alkalmazás **üzembe** helyezési lapjának folyamatjelző sávján a kiépítés utolsó indításakor látható. Ha manuálisan kell újraindítani a kiépítési feladatot, használja az alábbi módszerek egyikét:  

- A kiépítési feladatok újraindításához használja a Azure Portal. Az alkalmazás **üzembe** helyezése lapon válassza a **kiépítés újraindítása** lehetőséget. Ez a művelet teljesen újraindítja a kiépítési szolgáltatást, amely hosszabb időt is igénybe vehet. A teljes kezdeti ciklus újra futni fog, ami törli a letéteket, eltávolítja az alkalmazást a karanténból, és törli az összes vízjelet. A szolgáltatás ezután újra kiértékeli a forrásrendszer összes felhasználóját, és megállapítja, hogy a kiépítés hatókörében van-e. Ez akkor lehet hasznos, ha az alkalmazás jelenleg karanténban van, mivel ez a cikk azt tárgyalja, vagy módosítania kell az attribútum-hozzárendeléseket. Vegye figyelembe, hogy a kezdeti ciklus a kiértékeléshez szükséges objektumok száma miatt hosszabb időt vesz igénybe, mint a szokásos növekményes ciklus. A kezdeti és a növekményes ciklusok teljesítményéről [itt](application-provisioning-when-will-provisioning-finish-specific-user.md)olvashat bővebben.

- [A kiépítési feladatok újraindításához használja a](/graph/api/synchronization-synchronizationjob-restart?tabs=http&view=graph-rest-beta&preserve-view=true)Microsoft Graph. Az újraindítást teljes mértékben szabályozhatja. Dönthet úgy, hogy törli a letéteket (a karantén állapotának újraindításához), a karantén törlését (az alkalmazás karanténból való eltávolítását) vagy a vízjelek törlését. Használja az alábbi kérelmet:
 
```microsoft-graph
        POST /servicePrincipals/{id}/synchronization/jobs/{jobId}/restart
```

Cserélje le az "{ID}" értéket az alkalmazás-azonosító értékére, és cserélje le a (z) {jobId} kifejezést a [szinkronizálási feladatokhoz tartozó azonosítóra](/graph/api/resources/synchronization-configure-with-directory-extension-attributes?tabs=http&view=graph-rest-beta&preserve-view=true#list-synchronization-jobs-in-the-context-of-the-service-principal).
