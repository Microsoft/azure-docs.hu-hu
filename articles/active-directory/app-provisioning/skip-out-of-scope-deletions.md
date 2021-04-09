---
title: A hatókörön kívüli felhasználók törlésének kihagyása
description: Megtudhatja, hogyan bírálhatja felül a kiépítés alapértelmezett viselkedését a hatókör felhasználói közül.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: how-to
ms.workload: identity
ms.date: 12/10/2019
ms.author: kenwith
ms.reviewer: celested
ms.openlocfilehash: a6cbabe35b223020528d1cf48aa9e0ef9b9f7c05
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99256119"
---
# <a name="skip-deletion-of-user-accounts-that-go-out-of-scope"></a>A hatókörön kívüli felhasználói fiókok törlésének kihagyása

Alapértelmezés szerint az Azure AD-létesítési motor Soft törli vagy letiltja a hatókörön kívüli felhasználókat. Bizonyos forgatókönyvek esetében azonban, például az AD-felhasználók bejövő kiépítéséhez, ez a viselkedés nem a várt módon lehetséges, és érdemes lehet felülbírálni ezt az alapértelmezett viselkedést.  

Ez a cikk azt ismerteti, hogyan használható a Microsoft Graph API és a Microsoft Graph API Explorer a hatókörön kívüli fiókok feldolgozását vezérlő jelző ***SkipOutOfScopeDeletions*** beállításához. 
* Ha a ***SkipOutOfScopeDeletions*** értéke 0 (hamis), akkor a hatókörön kívüli fiókok le lesznek tiltva a célhelyen.
* Ha a ***SkipOutOfScopeDeletions** _ értéke 1 (igaz), akkor a hatókörön kívüli fiókok nem lesznek letiltva a célhelyen. Ez a jelző a _Provisioning alkalmazás * szintjén van beállítva, és a Graph API használatával konfigurálható. 

Mivel ezt a konfigurációt széles körben használják a *Munkanapokon Active Directory a felhasználók kiépítési* alkalmazásához, a következő lépésekben a munkanap alkalmazás képernyőképei szerepelnek. A konfiguráció azonban *más alkalmazásokkal* is használható, például a ServiceNow, a Salesforce és a Dropbox használatával.

## <a name="step-1-retrieve-your-provisioning-app-service-principal-id-object-id"></a>1. lépés: a kiépítési App Service rendszerbiztonsági tag AZONOSÍTÓjának beolvasása (objektumazonosító)

1. Indítsa el a [Azure Portal](https://portal.azure.com), és navigáljon a kiépítési alkalmazás tulajdonságok szakaszába. Például ha a *munkanapokat az ad-felhasználók kiépítési alkalmazás-* hozzárendeléséhez szeretné exportálni, navigáljon az alkalmazás tulajdonságok szakaszába. 
1. A kiépítési alkalmazás tulajdonságok szakaszában másolja az *objektumazonosító* mezőhöz társított GUID értéket. Ezt az értéket az alkalmazás **ServicePrincipalId** is nevezik, és a Graph Explorer műveleteiben fogja használni.

   ![Munkanapok App Service résztvevő azonosítója](./media/skip-out-of-scope-deletions/wd_export_01.png)

## <a name="step-2-sign-into-microsoft-graph-explorer"></a>2. lépés: bejelentkezés Microsoft Graph Explorerbe

1. [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) elindítása
1. Kattintson a "Bejelentkezés Microsofttal" gombra, és jelentkezzen be az Azure AD globális rendszergazdai vagy az alkalmazás-rendszergazdai hitelesítő adataival.

    ![Gráf bejelentkezés](./media/skip-out-of-scope-deletions/wd_export_02.png)

1. A sikeres bejelentkezés után a bal oldali ablaktáblán megjelenik a felhasználói fiók adatai.

## <a name="step-3-get-existing-app-credentials-and-connectivity-details"></a>3. lépés: meglévő alkalmazás hitelesítő adatainak és kapcsolati adatainak beolvasása

A Microsoft Graph Explorerben futtassa a következő GET lekérdezést a [servicePrincipalId] helyett az [1. lépésből](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id)kinyert **servicePrincipalId** .

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/secrets
```

   ![Feladatok lekérdezésének beolvasása](./media/skip-out-of-scope-deletions/skip-03.png)

Másolja a választ egy szövegfájlba. Az alábbi JSON-szöveghez hasonlóan fog kinézni, amely az üzembe helyezéshez tartozó sárga színnel van kiemelve. Adja hozzá a zöld színnel jelölt vonalakat a végponthoz, és frissítse a munkanapokhoz való kapcsolódási jelszót kék színnel. 

   ![Válasz beolvasása](./media/skip-out-of-scope-deletions/skip-04.png)

Itt látható a leképezéshez hozzáadandó JSON-blokk. 

```json
        {
            "key": "SkipOutOfScopeDeletions",
            "value": "True"
        }
```

## <a name="step-4-update-the-secrets-endpoint-with-the-skipoutofscopedeletions-flag"></a>4. lépés: a titkok végpontjának frissítése a SkipOutOfScopeDeletions jelzővel

A Graph Explorerben futtassa az alábbi parancsot a Secrets végpont ***SkipOutOfScopeDeletions*** jelzővel való frissítéséhez. 

Az alábbi URL-címben cserélje le a [servicePrincipalId] elemet az [1. lépésből](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id)kinyert **servicePrincipalId** . 

```http
   PUT https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/secrets
```
Másolja a 3. lépésben szereplő frissített szöveget a "kérés törzse" értékre, és állítsa be a "Content-Type" fejlécet "Application/JSON" értékre a "kérések fejléce" kifejezésben. 

   ![PUT kérelem](./media/skip-out-of-scope-deletions/skip-05.png)

Kattintson a "lekérdezés futtatása" gombra. 

A kimenetet "sikeres – állapotkód 204" értékre kell beolvasni. 

   ![Válasz elhelyezése](./media/skip-out-of-scope-deletions/skip-06.png)

## <a name="step-5-verify-that-out-of-scope-users-dont-get-disabled"></a>5. lépés: annak ellenőrzése, hogy a hatókörön kívüli felhasználók ne legyenek letiltva

Ezt a jelzőt tesztelheti a várt viselkedés alapján, ha frissíti a hatóköri szabályokat egy adott felhasználó kihagyásához. Az alábbi példában egy új hatókör-szabály hozzáadásával kizárja a 21173-as AZONOSÍTÓJÚ alkalmazottat (aki korábban a hatókörben volt): 

   ![A "hatókör-szűrő hozzáadása" szakaszt bemutató képernyőkép, amely egy példával jelölt felhasználóra mutat.](./media/skip-out-of-scope-deletions/skip-07.png)

A következő üzembe helyezési ciklusban az Azure AD kiépítési szolgáltatás megállapítja, hogy a 21173-es felhasználó kikerült a hatókörből, és ha a SkipOutOfScopeDeletions tulajdonság engedélyezve van, akkor az adott felhasználó szinkronizálási szabálya az alábbi képen látható üzenetet jeleníti meg: 

   ![Hatókör – példa](./media/skip-out-of-scope-deletions/skip-08.png)


