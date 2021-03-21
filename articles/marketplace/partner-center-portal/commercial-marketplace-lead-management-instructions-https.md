---
title: Vezető felügyelet egy HTTPS-végponttal – Microsoft kereskedelmi piactér
description: Ismerje meg, hogyan kezelheti a Power automatizálható és egy HTTPS-végpontot a Microsoft AppSource és az Azure Marketplace-en található érdeklődők kezeléséhez.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: trkeya
ms.author: trkeya
ms.date: 03/30/2020
ms.openlocfilehash: 5bea2cf256e30bd896957bbee0e0ad824057a569
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98247182"
---
# <a name="use-an-https-endpoint-to-manage-commercial-marketplace-leads"></a>HTTPS-végpont használata a kereskedelmi Piactéri érdeklődők felügyeletéhez

Ha az Ügyfélkapcsolat-kezelési (CRM) rendszer nincs kifejezetten támogatva a fiókpartner számára a Microsoft AppSource és az Azure Marketplace-beli érdeklődők fogadására, akkor a [Power gyorsbüféban](https://powerapps.microsoft.com/automate-processes/) HTTPS-végpontot használhat az ilyen érdeklődők kezelésére. A HTTPS-végpontok esetében a kereskedelmi Piactéri érdeklődők e-mail-értesítésként küldhetők el, vagy a Power automatizáló által támogatott CRM rendszerbe írhatók.

Ez a cikk azt ismerteti, hogyan hozhat létre egy új folyamatot a Power Gyorsbüféban, hogy létrehozza a HTTP POST URL-címet, amelyet az érdeklődők konfigurálásához fog használni a partner Centerben. A folyamat a [Poster](https://www.getpostman.com/downloads/)használatával történő tesztelésének lépéseit is tartalmazza.

>[!NOTE]
>Az ebben az útmutatóban használt energiagazdálkodási összekötőhöz fizetős előfizetés szükséges a Power automatizáláshoz. A folyamat konfigurálása előtt győződjön meg róla, hogy ez a fiók.

## <a name="create-a-flow-by-using-power-automate"></a>Folyamat létrehozása a Power automatizálás használatával

1. Nyissa meg a [Power automatizáló](https://flow.microsoft.com/) weblapot. Válassza a **Bejelentkezés** lehetőséget. Ha még nem rendelkezik fiókkal, válassza a **regisztráció ingyenes** lehetőséget egy ingyenes Power automatizáló fiók létrehozásához.

1. Jelentkezzen be, majd a menüben válassza a **saját folyamatok** lehetőséget.

    ![Bejelentkezés a saját folyamatok között](./media/commercial-marketplace-lead-management-instructions-https/my-flows-automated.png)

1. Az **+ új** alatt válassza **a + azonnali** lehetőséget.

    ![Saját folyamatok + automatizált – üresről](./media/commercial-marketplace-lead-management-instructions-https/https-myflows-create-fromblank.png)

1. Adja meg a folyamat nevét, majd a folyamat kiváltási **módjának kiválasztása** területen válassza ki **a HTTP-kérés fogadásakor** lehetőséget.

    ![Automatikus folyamat ablakának kihagyása gomb](./media/commercial-marketplace-lead-management-instructions-https/https-myflows-pick-request-trigger.png)

1. Kattintson a flow lépésre a kibontásához.

    ![A folyamat lépésének kibontása](./media/commercial-marketplace-lead-management-instructions-https/expand-flow-step.png)

1. A **kérelem törzse JSON-séma** konfigurálásához használja a következő módszerek egyikét:

    - Másolja a JSON-sémát a **kérelem törzse JSON-sémájának** szövegmezőbe.
    - Válassza a **Séma létrehozása hasznosadat-minta használatával** lehetőséget. Az **írja be vagy illessze be a minta JSON-adattartalom** szövegmezőbe illessze be a JSON-példát. Válassza a **kész** lehetőséget a séma létrehozásához.

    **JSON-séma**

    ```JSON
    {
      "$schema": "https://json-schema.org/draft-04/schema#",
      "definitions": {},
      "id": "http://example.com/example.json",
      "properties": {
        "ActionCode": {
          "id": "/properties/ActionCode",
          "type": "string"
        },
        "OfferTitle": {
          "id": "/properties/OfferTitle",
          "type": "string"
        },
        "LeadSource": {
          "id": "/properties/LeadSource",
          "type": "string"
        },
        "Description": {
          "id": "/properties/Description",
          "type": "string"
        },
        "UserDetails": {
          "id": "/properties/UserDetails",
          "properties": {
            "Company": {
              "id": "/properties/UserDetails/properties/Company",
              "type": "string"
            },
            "Country": {
              "id": "/properties/UserDetails/properties/Country",
              "type": "string"
            },
            "Email": {
              "id": "/properties/UserDetails/properties/Email",
              "type": "string"
            },
            "FirstName": {
              "id": "/properties/UserDetails/properties/FirstName",
              "type": "string"
            },
            "LastName": {
              "id": "/properties/UserDetails/properties/LastName",
              "type": "string"
            },
            "Phone": {
              "id": "/properties/UserDetails/properties/Phone",
              "type": "string"
            },
            "Title": {
              "id": "/properties/UserDetails/properties/Title",
              "type": "string"
            }
          },
          "type": "object"
        }
      },
      "type": "object"
    }
    ```

    **JSON-példa**
    
    ```json
    {
      "UserDetails": {
        "FirstName": "Some",
        "LastName": "One",
        "Email": "someone@contoso.com",
        "Phone": "16175555555",
        "Country": "USA",
        "Company": "Contoso",
        "Title": "Esquire"
     },
      "LeadSource": "AzureMarketplace",
      "ActionCode": "INS",
      "OfferTitle": "Test Microsoft",
      "Description": "Test run through Power Automate"
    }
    ```

>[!NOTE]
>A konfiguráció ezen pontján lehetőség van arra, hogy egy CRM rendszerhez kapcsolódjon, vagy e-mailes értesítést konfiguráljon. Kövesse az Ön által választott további utasításokat.

### <a name="connect-to-a-crm-system"></a>Kapcsolódás CRM rendszerhez

1. Válassza az **+ Új lépés** gombot.
1. Válassza ki az Ön által választott CRM-rendszert úgy, hogy megkeresi az **Összekötők és a műveletek keresését**. Válassza ki a **műveletek** lapon az új rekord létrehozásához szükséges műveletet. A következő képernyőn látható **egy új rekord létrehozása (Dynamics 365)** példaként.

    ![Új rekord létrehozása](./media/commercial-marketplace-lead-management-instructions-https/create-new-record.png)

1. Adja meg a CRM rendszerhez társított **szervezet nevét** . Az **entitás neve** legördülő listából válassza az **érdeklődők** lehetőséget.

    ![Érdeklődők kiválasztása](./media/commercial-marketplace-lead-management-instructions-https/select-leads.png)

1. A Power automatizálás egy űrlapot jelenít meg az érdeklődői információk biztosításához. A bemeneti kérelem elemeit leképezheti a dinamikus tartalom hozzáadására szolgáló lehetőség választásával. A következő képernyőn a **OfferTitle** látható példaként.

    ![Dinamikus tartalom hozzáadása](./media/commercial-marketplace-lead-management-instructions-https/add-dynamic-content.png)

1. Képezze le a kívánt mezőket, majd válassza a **Mentés** lehetőséget a folyamat mentéséhez. A rendszer létrehoz egy HTTP POST URL-címet, és elérhetővé válik a **http-kérelem fogadása** ablakban. Másolja ezt az URL-t a másolás vezérlőelem használatával, amely a HTTP POST URL-cím jobb oldalán található. A másolási vezérlő használata fontos, hogy ne hagyjon ki a teljes URL-cím egyik részét sem. Mentse ezt az URL-címet, mert szüksége lesz rá, amikor a közzétételi portálon konfigurálja az érdeklődők felügyeletét.

    ![HTTP-kérés fogadásakor](./media/commercial-marketplace-lead-management-instructions-https/when-http-request-received.png)

### <a name="set-up-email-notification"></a>E-mail értesítések beállítása

1. Most, hogy befejezte a JSON-sémát, válassza az **+ új lépés** lehetőséget.
1. A **válasszon műveletet** területen válassza a **műveletek** lehetőséget.
1. A **műveletek** lapon válassza az **E-mail küldése (Office 365 Outlook)** lehetőséget.

    >[!NOTE]
    >Ha más e-mail-szolgáltatót szeretne használni, keresse meg az **e-mailes értesítés küldése (e-mail)** lehetőséget a művelet helyett.

    ![E-mail művelet hozzáadása](./media/commercial-marketplace-lead-management-instructions-https/https-request-received-send-email.png)

1. Az **E-mail küldése** ablakban adja meg a következő kötelező mezőket:

   - **Címzett**: adjon meg legalább egy érvényes e-mail-címet, ahol a rendszer elküldi az érdeklődőket.
   - **Subject**: a Power automatizálás lehetőséget ad a dinamikus tartalmak hozzáadására, például a következő képernyőn látható **LeadSource** . Kezdje a mező nevének megadásával. Ezután válassza ki a dinamikus tartalom kiválasztása listát az előugró ablakban. 

        >[!NOTE] 
        > Mezők neveinek hozzáadásakor az egyes neveket kettősponttal (:) majd az **ENTER** gombra kattintva hozzon létre egy új sort. Miután hozzáadta a mezőneveket, hozzáadhatja az egyes társított paramétereket a dinamikus kivételezések listájából.

        ![E-mail művelet hozzáadása dinamikus tartalom használatával](./media/commercial-marketplace-lead-management-instructions-https/add-email-using-dynamic-content.png)

   - **Törzs**: a dinamikus tartalom kiválasztása listából adja hozzá a kívánt adatokat az e-mail törzsébe. Használhatja például a LastName, a FirstName, az E-mail és a Company címet. Ha elkészült az e-mail-értesítés beállításával, az a következő képernyőn látható példához hasonlóan néz ki.


       ![Példa e-mail-értesítésre](./media/commercial-marketplace-lead-management-instructions-https/send-an-email.png)

1. A folyamat befejezéséhez kattintson a **Mentés** gombra. A rendszer létrehoz egy HTTP POST URL-címet, és elérhetővé válik a **http-kérelem fogadása** ablakban. Másolja ezt az URL-t a másolás vezérlőelem használatával, amely a HTTP POST URL-cím jobb oldalán található. A vezérlő használata fontos, hogy ne hagyjon ki a teljes URL-cím egyik részét sem. Mentse ezt az URL-címet, mert szüksége lesz rá, amikor a közzétételi portálon konfigurálja az érdeklődők felügyeletét.

   ![HTTP POST URL-CÍM](./media/commercial-marketplace-lead-management-instructions-https/http-post-url.png)

### <a name="testing"></a>Tesztelés

A konfigurációt a [Poster](https://app.getpostman.com/app/download/win64)segítségével tesztelheti. A Poster online letöltése elérhető a Windowsban. 

1. Indítsa el a Poster alkalmazást, és válassza az **új**  >  **kérelem** lehetőséget a tesztelési eszköz beállításához. 

   ![A tesztelési eszköz beállításához szükséges kérelem](./media/commercial-marketplace-lead-management-instructions-https/postman-request.png)

1. Töltse ki a **mentési kérelem** űrlapot, majd mentse a létrehozott mappába.

   ![Kérelem űrlapjának mentése](./media/commercial-marketplace-lead-management-instructions-https/postman-save-to-test.png)

1. Válassza a **Közzététel** lehetőséget a legördülő listából. 

   ![A folyamat tesztelése](./media/commercial-marketplace-lead-management-instructions-https/test-my-flow.png)

1. Illessze be a HTTP POST URL-címet a Power automatizálásban létrehozott folyamatból, ahol az **adja meg a kérelem URL-címét**.

   ![A HTTP POST URL-cím beillesztése](./media/commercial-marketplace-lead-management-instructions-https/paste-http-post-url.png)

1. Térjen vissza a [Power automatizáláshoz](https://flow.microsoft.com/). Keresse meg az érdeklődők küldéséhez létrehozott folyamatot a Power automatizáló menüsorból a **saját folyamatok** lehetőségre kattintva. Kattintson a folyamat neve melletti három pontra a további beállítások megjelenítéséhez, majd válassza a **Szerkesztés** lehetőséget.


1. Válassza a **tesztelés** lehetőséget a jobb felső sarokban, válassza **az indítás műveletet**, majd a **teszt** lehetőséget. Megjelenik egy jelzés a képernyő felső részén, amelyen a teszt elindult.

   ![Elvégezem az trigger művelet lehetőséget](./media/commercial-marketplace-lead-management-instructions-https/test-flow-trigger-action.png)

1. Lépjen vissza a Poster-alkalmazásba, és válassza a **Küldés** lehetőséget.

   ![Küldés gomb](./media/commercial-marketplace-lead-management-instructions-https/postman-send.png)

1. Térjen vissza a folyamathoz, és tekintse át az eredményt. Ha minden a várt módon működik, egy üzenet jelenik meg, amely jelzi, hogy a folyamat sikeres volt.

   ![Eredmények keresése](./media/commercial-marketplace-lead-management-instructions-https/my-flow-check-results.png)

1. E-mailt is kell kapnia. Keresse meg az e-mailek mappáját. 

    >[!NOTE] 
    >Ha nem lát e-mailt a tesztből, ellenőrizze a levélszemét és a levélszemét mappáját. A következő képernyőn megfigyelheti, hogy csak az e-mail-értesítések konfigurálásakor hozzáadott mezők szerepelnek. Ha ez az ajánlat által generált tényleges érdeklődő volt, akkor a törzsben és a Tárgy sorban lévő érdeklődői kapcsolattartótól származó tényleges adatokat is látni fogja.

   ![Fogadott e-mail](./media/commercial-marketplace-lead-management-instructions-https/email-received.png)

## <a name="configure-your-offer-to-send-leads-to-the-https-endpoint"></a>Az ajánlat beállítása a HTTPS-végpontnak küldött érdeklődők küldésére

Ha készen áll az ajánlathoz tartozó érdeklődő-felügyeleti információk konfigurálására a közzétételi portálon, kövesse az alábbi lépéseket.

1. Jelentkezzen be a [partner központba](https://partner.microsoft.com/dashboard/home).

1. Válassza ki az ajánlatot, és nyissa meg az **ajánlat beállítása** lapot.

1. Az **ügyfél-érdeklődők** szakaszban válassza a **kapcsolat** lehetőséget.

    :::image type="content" source="./media/commercial-marketplace-lead-management-instructions-https/customer-leads.png" alt-text="Ügyfél-érdeklődők":::

1. A **kapcsolat részletei** előugró ablakban válassza a https- **végpont** lehetőséget az **érdeklődő célhelyéhez**. Illessze be a HTTP POST URL-címet a létrehozott folyamatból, a korábbi lépéseket követve a **https-végpont URL-címe** mezőbe.
    ![Kapcsolat részletei – kapcsolattartási e-mail cím](./media/commercial-marketplace-lead-management-instructions-https/https-connection-details.png)

1. A **kapcsolattartási e-mail** cím alatt adja meg a vállalat azon tagjainak e-mail-címeit, akiknek új érdeklődő fogadása esetén e-mail-értesítéseket kell kapniuk. A pontosvesszővel elválasztva több e-mailt is megadhat.

1. Válassza az **OK** lehetőséget.

Az **Érvényesítés** gombra kattintva győződjön meg arról, hogy sikeresen csatlakozott egy érdeklődői célhoz. Ha a művelet sikeres, a vezető célhelyen egy teszt vezet.

>[!NOTE] 
>Be kell fejeznie az ajánlat többi részének konfigurálását, és közzé kell tennie az ajánlathoz tartozó érdeklődők fogadása előtt.

Az érdeklődők létrehozásakor a Microsoft elküldi az érdeklődőket a folyamatnak. Az érdeklődők a beállított CRM-rendszerhez vagy e-mail-címre vannak irányítva.
