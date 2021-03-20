---
title: A kiépítés konfigurációjának exportálása és visszavonása a vész-helyreállítás ismert jó állapotára
description: Megtudhatja, hogyan exportálhatja a kiépítési konfigurációt, és visszaállíthatja a vész-helyreállítás ismert megfelelő állapotát.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: how-to
ms.workload: identity
ms.date: 03/19/2020
ms.author: kenwith
ms.openlocfilehash: c6af42c78bda66c4b397cbb99b26af7d6a5c7f07
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99256373"
---
# <a name="how-to-export-provisioning-configuration-and-roll-back-to-a-known-good-state"></a>Útmutató: az üzembe helyezési konfiguráció exportálása és visszavonása egy ismert megfelelő állapotba

Ebből a cikkből megtudhatja, hogyan:

- Kiépítési konfiguráció exportálása és importálása a Azure Portal
- A kiépítési konfiguráció exportálása és importálása a Microsoft Graph API használatával

## <a name="export-and-import-your-provisioning-configuration-from-the-azure-portal"></a>Kiépítési konfiguráció exportálása és importálása a Azure Portal

### <a name="export-your-provisioning-configuration"></a>A kiépítési konfiguráció exportálása

A konfiguráció exportálása:

1. A [Azure Portal](https://portal.azure.com/)a bal oldali navigációs panelen válassza a **Azure Active Directory** lehetőséget.
1. A **Azure Active Directory** ablaktáblán válassza a **vállalati alkalmazások** lehetőséget, majd válassza ki az alkalmazást.
1. A bal oldali navigációs panelen válassza a **kiépítés** lehetőséget. A létesítési konfiguráció lapon kattintson az **attribútum-hozzárendelések** elemre, majd **jelenítse meg a speciális beállításokat**, és végül **tekintse át a sémát**. Ekkor megnyílik a sémakezelő szerkesztő.
1. A séma letöltéséhez kattintson a lap tetején található parancssáv Letöltés gombjára.

### <a name="disaster-recovery---roll-back-to-a-known-good-state"></a>Vész-helyreállítás – visszaállítás egy ismert jó állapotba

A konfiguráció exportálása és mentése lehetővé teszi, hogy visszaállítsa a konfiguráció egy korábbi verzióját. Javasoljuk a kiépítési konfiguráció exportálását és későbbi használatra mentését, amikor módosítja az attribútum-hozzárendeléseket vagy a hatóköri szűrőket. Mindössze annyit kell tennie, hogy megnyitja a fenti lépésekben letöltött JSON-fájlt, másolja a JSON-fájl teljes tartalmát, cserélje le a JSON-adattartalom teljes tartalmát a sémakezelő szerkesztőben, majd mentse. Ha aktív kiépítési ciklus van, akkor a művelet befejeződik, és a következő ciklus a frissített sémát fogja használni. A következő ciklus egy kezdeti ciklus is, amely az új konfiguráció alapján újraértékeli az összes felhasználót és csoportot. Egy korábbi konfigurációra való visszalépéskor vegye figyelembe a következőket:

- A rendszer ismét kiértékeli a felhasználókat annak megállapítása érdekében, hogy a hatókörük legyen-e. Ha a hatókörhöz tartozó szűrők módosítva lettek, a felhasználó nem tartozik többé, akkor azok le lesznek tiltva. Habár a legtöbb esetben ez a kívánt viselkedés, időnként előfordulhat, hogy meg szeretné akadályozni ezt, és használhatja a [hatókör törlése funkció kihagyása](./skip-out-of-scope-deletions.md) funkciót. 
- A kiépítési konfiguráció módosítása újraindítja a szolgáltatást, és elindítja a [kezdeti ciklust](./how-provisioning-works.md#provisioning-cycles-initial-and-incremental).

## <a name="export-and-import-your-provisioning-configuration-by-using-the-microsoft-graph-api"></a>A kiépítési konfiguráció exportálása és importálása a Microsoft Graph API használatával

A Microsoft Graph API-val és a Microsoft Graph Explorerrel exportálhatja a felhasználók kiépítési attribútumait egy JSON-fájlba, majd importálhatja azt az Azure AD-be. Az itt rögzített lépéseket az üzembe helyezési konfiguráció biztonsági másolatának létrehozásához is használhatja.

### <a name="step-1-retrieve-your-provisioning-app-service-principal-id-object-id"></a>1. lépés: a kiépítési App Service rendszerbiztonsági tag AZONOSÍTÓjának beolvasása (objektumazonosító)

1. Indítsa el a [Azure Portal](https://portal.azure.com), és navigáljon a kiépítési alkalmazás tulajdonságok szakaszába. Ha például azt szeretné, hogy az AD- *felhasználó kiépítési alkalmazásához* tartozó munkaterületet exportálja, navigáljon az alkalmazás tulajdonságok szakaszába.
1. A kiépítési alkalmazás tulajdonságok szakaszában másolja az *objektumazonosító* mezőhöz társított GUID értéket. Ezt az értéket az alkalmazás **ServicePrincipalId** is nevezik, és Microsoft Graph Explorer-műveletekben lesz használva.

   ![Munkanapok App Service résztvevő azonosítója](./media/export-import-provisioning-configuration/wd_export_01.png)

### <a name="step-2-sign-into-microsoft-graph-explorer"></a>2. lépés: bejelentkezés Microsoft Graph Explorerbe

1. [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) elindítása
1. Kattintson a "Bejelentkezés Microsofttal" gombra, és jelentkezzen be az Azure AD globális rendszergazdai vagy az alkalmazás-rendszergazdai hitelesítő adataival.

    ![Bejelentkezés Microsoft Graph](./media/export-import-provisioning-configuration/wd_export_02.png)

1. A sikeres bejelentkezés után a bal oldali ablaktáblán megjelenik a felhasználói fiók adatai.

### <a name="step-3-retrieve-the-provisioning-job-id-of-the-provisioning-app"></a>3. lépés: a kiépítési alkalmazáshoz tartozó kiépítési feladatok AZONOSÍTÓjának beolvasása

A Microsoft Graph Explorerben futtassa a következő GET lekérdezést a [servicePrincipalId] helyett az [1. lépésből](#step-1-retrieve-your-provisioning-app-service-principal-id-object-id)kinyert **servicePrincipalId** .

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs
```

Az alább látható válasz jelenik meg. Másolja az "id" attribútumot a válaszban. Ez az érték a **ProvisioningJobId** , és a rendszer az alapul szolgáló séma metaadatainak beolvasására szolgál.

   [![Kiépítési feladatok azonosítója](./media/export-import-provisioning-configuration/wd_export_03.png)](./media/export-import-provisioning-configuration/wd_export_03.png#lightbox)

### <a name="step-4-download-the-provisioning-schema"></a>4. lépés: a kiépítési séma letöltése

A Microsoft Graph Explorerben futtassa a következő GET lekérdezést, és cserélje le a [servicePrincipalId] és a [ProvisioningJobId] kifejezést az előző lépésben lekért ServicePrincipalId és ProvisioningJobId.

```http
   GET https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

Másolja a JSON-objektumot a válaszból, és mentse fájlba a séma biztonsági másolatának létrehozásához.

### <a name="step-5-import-the-provisioning-schema"></a>5. lépés: a kiépítési séma importálása

> [!CAUTION]
> Ezt a lépést csak akkor hajtsa végre, ha módosítania kell a sémát olyan konfigurációhoz, amely nem módosítható a Azure Portal használatával, vagy ha egy korábban mentett fájl konfigurációját egy érvényes és működő sémával kell visszaállítani.

A Microsoft Graph Explorerben konfigurálja a következő PUT lekérdezést, és cserélje le a [servicePrincipalId] és a [ProvisioningJobId] kifejezést az előző lépésben lekért ServicePrincipalId és ProvisioningJobId.

```http
    PUT https://graph.microsoft.com/beta/servicePrincipals/[servicePrincipalId]/synchronization/jobs/[ProvisioningJobId]/schema
```

A "kérelem törzse" lapon másolja át a JSON-séma fájljának tartalmát.

   [![Kérelem törzse](./media/export-import-provisioning-configuration/wd_export_04.png)](./media/export-import-provisioning-configuration/wd_export_04.png#lightbox)

Az "igénylési fejlécek" lapon adja hozzá a Content-Type fejléc attribútumot az "Application/JSON" értékkel.

   [![Kérések fejlécei](./media/export-import-provisioning-configuration/wd_export_05.png)](./media/export-import-provisioning-configuration/wd_export_05.png#lightbox)

Válassza a **lekérdezés futtatása** lehetőséget az új séma importálásához.