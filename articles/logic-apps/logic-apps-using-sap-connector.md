---
title: Az SAP-rendszerek – Azure Logic Apps csatlakoztatása
description: Hozzáférés és az SAP-erőforrások kezelése az Azure Logic Apps a munkafolyamatok automatizálásával
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: divswa, LADocs
ms.topic: article
ms.date: 05/09/2019
tags: connectors
ms.openlocfilehash: bccefec80ef3afd6d312bb1048d3be5d8e708728
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66258154"
---
# <a name="connect-to-sap-systems-from-azure-logic-apps"></a>Csatlakozás az Azure Logic Apps a SAP-rendszerek

Ez a cikk azt ismerteti, hogyan férhet hozzá a helyszíni SAP erőforrásokat belül egy logikai alkalmazást az SAP-összekötő használatával. Az összekötő működik az SAP klasszikus kiadásaival, például az R/3-mal és helyszíni ECC-rendszerekkel. Az összekötő emellett lehetővé teszi az integráció az újabb SAP HANA-alapú rendszerek az SAP S/4 HANA, például a, bárhol üzemeltetik azokat – helyszíni vagy felhőben. Az SAP-összekötő üzenet vagy adatok integráció az SAP NetWeaver-alapú rendszerek köztes dokumentum (az idoc-hoz) vagy az üzleti alkalmazás alkalmazásprogramozási felületet (BAPI) vagy a távoli függvény hívása (RFC) keresztül támogatja.

Az SAP-összekötőt használja a [SAP .NET-összekötő (Ice) könyvtár](https://support.sap.com/en/product/connectors/msnet.html) , és ezek a műveletek vagy műveleteket biztosít:

* **SAP küldése**: TRFC küldeni az idoc-hoz, BAPI-függvények hívása RFC keresztül vagy RFC/tRFC hívja az SAP-rendszereinket.
* **SAP fogadjon**: IDoc fogadjanak tRFC keresztül, BAPI-függvények hívása tRFC keresztül, vagy RFC/tRFC hívja az SAP-rendszereinket.
* **Sémák készítése**: Hozza létre az SAP-összetevők sémákat az idoc-hoz, BAPI vagy RFC.

Az SAP-összekötő a fenti műveletek mindegyikénél alapszintű hitelesítést támogat felhasználónév és jelszó használatával. Az összekötő támogatja a [biztonságos hálózati kommunikációs (SNC)](https://help.sap.com/doc/saphelp_nw70/7.0.31/e6/56f466e99a11d1a5b00000e835363f/content.htm?no_cache=true), amellyel az SAP NetWeaver egyszeri bejelentkezéshez, vagy egy külső biztonsági termék által biztosított további biztonsági funkciók.

Az SAP-összekötő helyszíni SAP-rendszerek keresztül integrálódik a [a helyszíni adatátjáró](../logic-apps/logic-apps-gateway-connection.md). Küldési forgatókönyvekben például a logic apps üzenetet küld egy SAP-rendszerrel, amikor az átjáró funkcionál RFC-ügyfélként és az SAP, a logic apps érkező kérelmeket továbbítja.
Ehhez hasonlóan a Receive esetben az átjáró kiszolgálóként működik egy RFC, amely kéréseket fogad az SAP és a logikai alkalmazás továbbítja.

Ez a cikk bemutatja, hogyan Példa logikai alkalmazásokat hozhat létre, amelyek során a korábban ismertetett integrációs forgatókönyvek kiterjedő SAP integrálhatók.

<a name="pre-reqs"></a>

## <a name="prerequisites"></a>Előfeltételek

Ez a cikk követni, ezek az elemek szükségesek:

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetésem, [regisztráljon egy ingyenes Azure-fiókkal](https://azure.microsoft.com/free/).

* A logikai alkalmazás, ahonnan csak szeretné elérni az SAP-rendszerhez, és a egy eseményindítót, amely elindítja a logikai alkalmazás munkafolyamat. Ha most ismerkedik a logic apps, tekintse át [Mi az Azure Logic Apps](../logic-apps/logic-apps-overview.md) és [a rövid útmutató: Az első logikai alkalmazás létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md).

* A [SAP-alkalmazáskiszolgáló](https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server) vagy [SAP Üzenetkiszolgáló](https://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm)

* Töltse le és telepítse a legújabb [a helyszíni adatátjáró](https://www.microsoft.com/download/details.aspx?id=53127) bármely a helyi számítógépen. Győződjön meg arról, hogy az átjáró beállításához az Azure Portalon a folytatás előtt. Az átjáró segít biztonságos hozzáférés a helyszíni adatokat és erőforrásokat. További információkért lásd: [telepítése a helyszíni adatátjáró Azure Logic Apps](../logic-apps/logic-apps-gateway-install.md).

* Ha SNC az egyszeri bejelentkezés (SSO) használ, ellenőrizze az átjáró fut-e be, szemben a SAP-felhasználó van leképezve. Az alapértelmezett fiók módosításához válassza **fiók módosítása**, és adja meg a hitelesítő adatokat.

  ![Átjáró fiók módosítása](./media/logic-apps-using-sap-connector/gateway-account.png)

* SNC engedélyezi egy külső biztonsági termékkel, másolja a SNC-könyvtár vagy ugyanazon a gépen fájlokat, ahol az átjáró telepítve van. SNC-termékek például [sapseculib](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm), Kerberos, NTLM, és így tovább.

* Töltse le és telepítse a legújabb SAP ügyféloldali kódtár, amely jelenleg [(Ice) 3.0.21.0 SAP-összekötő a Microsoft .NET-keretrendszer 4.0-s és a Windows 64 bites (x64)](https://softwaredownloads.sap.com/file/0020000001865512018), mint a helyszíni adatátjáró ugyanazon a számítógépen. Ezt a verziót telepíteni vagy újabb ezen okok miatt:

  * SAP Ice korábbi verzióiban előfordulhat, hogy válnak holtponti, amikor egynél több IDoc-üzeneteket küld egy időben. Ez a feltétel blokkolja az üzenetek időtúllépést okoz, a SAP célhelyre küldött összes újabb üzenetet.

  * A helyszíni átjáró csak 64 bites rendszeren fut. Ellenkező esetben egy "hibás" hiba beolvasása, mivel a data gateway szolgáltatás nem támogatja a 32 bites szerelvényeket.

  * A data gateway szolgáltatás és a Microsoft az SAP-Adapter is használhatja a .NET-keretrendszer 4.5. A .NET keretrendszer 4.0-s SAP Ice együttműködik a .NET-modul 4.0-s, 4.7.1 használó folyamatokat. Az SAP Ice a .NET-keretrendszer 2.0 együttműködik az olyan folyamatokat, amelyek a .NET-futtatórendszer 2.0-s, 3.5-ös verzióját használja, és többé már nem a legújabb a helyszíni adatátjáróval.

* Üzenet tartalma is elküldheti az SAP-kiszolgálóhoz, például egy mintafájlt az idoc-hoz. Ez a tartalom XML formátumú legyen, és tartalmazza a névteret, az SAP-művelethez használni kívánt.

<a name="add-trigger"></a>

## <a name="send-to-sap"></a>SAP küldése

Ebben a példában egy HTTP-kérelem használ egy logikai alkalmazást, amely is indíthat. A logikai alkalmazás küld egy köztes dokumentumot (az idoc-hoz) SAP-kiszolgálóhoz, és választ küld a kérelmezőnek, amelyek a logikai alkalmazás neve. 

### <a name="add-http-request-trigger"></a>HTTP-kérés eseményindító hozzáadása

Az Azure Logic Appsben, mindegyik logikai alkalmazásnak kell kezdődnie, egy [eseményindító](../logic-apps/logic-apps-overview.md#logic-app-concepts), amely akkor aktiválódik, ha egy adott esemény történik, vagy ha egy adott feltétel teljesül. Minden alkalommal, amikor akkor aktiválódik, a Logic Apps-motor létrehoz egy logikaialkalmazás-példányt, és megkezdi az alkalmazás munkafolyamatában.

Ebben a példában egy logikai alkalmazást fog létrehozni az Azure-ban a végpont az, hogy küldhet *HTTP POST-kérések* a logikai alkalmazáshoz. Amikor a logikai alkalmazás a HTTP-kéréseket fogad, a akkor aktiválódik, majd futtatja a következő lépés a munkafolyamatban.

1. Az a [az Azure portal](https://portal.azure.com), hozzon létre egy üres logikai alkalmazást, amely megnyílik a Logikaialkalmazás-Tervező.

1. A Keresés mezőbe írja be a "http-kérelem" szűrőként. Az eseményindítók listában jelölje ki az eseményindító: **HTTP-kérés fogadásakor**

   ![HTTP-kérelem típusú trigger hozzáadása](./media/logic-apps-using-sap-connector/add-trigger.png)

1. Így létrehozhat egy végponti URL-cím a logikai alkalmazás most már a logikai alkalmazás mentése. A tervező eszköztárán válassza a **Mentés** parancsot.

   A végpont URL-cím mostantól megjelenik az eseményindító, például:

   ![Végpont URL-címet generálni](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

<a name="add-action"></a>

### <a name="add-sap-action"></a>Az SAP-művelet hozzáadása

Az Azure Logic Apps- [művelet](../logic-apps/logic-apps-overview.md#logic-app-concepts) a munkafolyamat egy eseményindító vagy egy másik műveletet a következő lépés. Ha még nem adott hozzá egy eseményindítót a logikai alkalmazáshoz még, és szeretné követni az ebben a példában [adja hozzá az ebben a szakaszban leírt eseményindító](#add-trigger).

1. A Logic App Designerben az eseményindító területén válassza a **új lépés**.

   ![Válassza az "Új lépés"](./media/logic-apps-using-sap-connector/add-action.png)

1. A Keresés mezőbe írja be szűrőként "sap". A műveletek listából válassza a következő műveletet: **Üzenet küldése a SAP**
  
   ![Az SAP-küldési művelet kiválasztása](media/logic-apps-using-sap-connector/select-sap-send-action.png)

   Azt is megteheti, helyett, válassza ki a **vállalati** lapra, és válassza ki az SAP-műveletet.

   ![Select SAP send action from Enterprise tab](media/logic-apps-using-sap-connector/select-sap-send-action-ent-tab.png)

1. Ha a kapcsolat adatait kéri, hozzon létre most az SAP-kapcsolat. Ellenkező esetben, ha a kapcsolat már létezik, folytassa a következő lépés tehát beállíthatja az SAP-műveletet.

   **Helyszíni SAP-kapcsolat létrehozása**

   1. Adja meg a kapcsolati adatokat, az SAP-kiszolgáló. Az a **adatátjáró** tulajdonság, jelölje be az átjárót az átjáró telepítése az Azure Portalon létrehozott.

      Ha a **bejelentkezési típus** tulajdonsága **alkalmazáskiszolgáló**, ezeket a tulajdonságokat, amelyben a általában nem kötelező, szükségesek:

      ![SAP alkalmazás kiszolgálói kapcsolat létrehozása](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      Ha a **bejelentkezési típus** tulajdonsága **csoport**, ezeket a tulajdonságokat, amelyben a általában nem kötelező, szükségesek:

      ![SAP üzenet kiszolgálói kapcsolat létrehozása](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)

      Alapértelmezés szerint erős beírni használatos érvénytelen értékeket a sémának XML-érvényesítés elvégzésével ellenőrizheti. Ez a viselkedés segítséget nyújt a korábbi hibák észlelése. A **biztonságos beírni** lehetőség a visszamenőleges kompatibilitás érdekében érhető el, és csak az a karakterlánc hosszának ellenőrzi. Tudjon meg többet a [ **biztonságos beírni** beállítás](#safe-typing).

   1. Ha elkészült, kattintson a **Létrehozás** gombra.

      A Logic Apps állít be, és gondoskodik róla, hogy a kapcsolat megfelelően működik, a kapcsolat tesztelése.

1. Most már található, és válasszon ki egy műveletet az SAP-kiszolgáló.

   1. Az a **SAP művelet** válassza ki a mappa ikont. A fájl listában keresse meg és válassza ki a használni kívánt SAP-üzenetet. Keresse meg a listában, használja a nyilak.

      Ebben a példában kiválasztja az IDoc- **rendelések** típusa.

      ![Keresse meg és válassza a művelet az idoc-hoz](./media/logic-apps-using-sap-connector/SAP-app-server-find-action.png)

      Ha nem találja a kívánt művelet, manuálisan adhatja meg egy elérési utat, például:

      ![Manuálisan adja meg az idoc-hoz a művelet elérési útja](./media/logic-apps-using-sap-connector/SAP-app-server-manually-enter-action.png)

      > [!TIP]
      > Adja meg az értéket a kifejezésszerkesztőbe keresztül SAP művelet. Így művelettel ugyanazon a különféle típusú üzenet.

      IDoc-műveletekkel kapcsolatos további információkért lásd: [sémák IDOC-műveletek üzenet](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations).

   1. Kattintson a **bemeneti üzenet** mezőre, hogy a dinamikus tartalmak listája jelenik meg. A listából a **amikor egy HTTP-kérés érkezik**, jelölje be a **törzs** mező.

      Ebben a lépésben a HTTP-kérés eseményindító a törzs tartalma, és elküldi, amelyek kimenete az SAP-kiszolgálóhoz.

      !["Törzs" mező kiválasztása](./media/logic-apps-using-sap-connector/SAP-app-server-action-select-body.png)

      Ha elkészült, az SAP-művelet alábbi példára hasonlít:

      ![Teljes SAP-művelet](./media/logic-apps-using-sap-connector/SAP-app-server-complete-action.png)

1. Mentse a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés** parancsot.

<a name="add-response"></a>

### <a name="add-http-response-action"></a>HTTP-válasz művelet hozzáadása

Adja meg a logikai alkalmazás munkafolyamat egy válaszművelet és közé tartozik az SAP-művelet kimenete. Ily módon a logikai alkalmazás adja vissza az eredményeket az SAP-kiszolgáló az eredeti kérelmezőnek.

1. A Logic App Designerben az SAP művelet alatt válassza ki a **új lépés**.

1. A Keresés mezőbe írja be a "response" szűrőként. A műveletek listából válassza a következő műveletet: **Válasz**

1. Kattintson a **törzs** mezőre, hogy a dinamikus tartalmak listája jelenik meg. A listából a **SAP üzenet küldése**, jelölje be a **törzs** mező.

   ![Teljes SAP-művelet](./media/logic-apps-using-sap-connector/select-sap-body-for-response-action.png)

1. Mentse a logikai alkalmazást.

### <a name="test-your-logic-app"></a>A logikai alkalmazás tesztelése

1. Ha a logikai alkalmazás még nincs engedélyezve, a logikai alkalmazás menüjében válassza a **áttekintése**. Az eszköztáron válassza **engedélyezése**.

1. Logikaialkalmazás-Tervező eszköztárán válassza a **futtatása**. Ezzel a lépéssel manuálisan indíthatja a logikai alkalmazást.

1. HTTP POST-kérelmet küld a HTTP-kérés eseményindító URL-cím a logikai alkalmazás elindításához, és a tartalmat a kérés a üzenet hozzáadása. A kérelem küldése, használhatja a eszközt például [Postman](https://www.getpostman.com/apps).

   Ebben a cikkben a kérést küld egy IDoc fájlt, amely XML formátumban kell és tartalmazza a névteret használ, például az SAP-művelet:

   ``` xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <Send xmlns="http://Microsoft.LobServices.Sap/2007/03/Idoc/2/ORDERS05//720/Send">
      <idocData>
         <...>
      </idocData>
   </Send>
   ```

1. Miután elküldte a HTTP-kérés, várja meg a logikai alkalmazás válasza.

   > [!NOTE]
   > A logikai alkalmazás időtúllépés előfordulhat, ha a válasz szükséges összes lépést nem fejeződnek be belül a [kérelem időkorlátja](./logic-apps-limits-and-config.md). Ez az állapot akkor fordul elő, ha a kérések egyébként blokkolná. Könnyebben diagnosztizálhatja a problémákat, megtudhatja, hogyan zajlik [ellenőrzése és a logic apps figyelése](../logic-apps/logic-apps-monitor-your-logic-apps.md).

Gratulálunk, sikeresen létrehozott egy logikai alkalmazást, amely képes kommunikálni az SAP-kiszolgálóval. Most, hogy beállította a logikai alkalmazás egy SAP-kapcsolatot, áttekintheti az egyéb elérhető SAP műveletek, például BAPI és RFC.

## <a name="receive-from-sap"></a>SAP fogadása

Ez a példa egy logikai alkalmazást, amely, amikor egy üzenet fogadása az SAP-rendszer használja.

### <a name="add-sap-trigger"></a>Add SAP trigger

1. Az Azure Portalon hozzon létre egy üres logikai alkalmazást, amely megnyílik a Logikaialkalmazás-Tervező.

1. A Keresés mezőbe írja be szűrőként "sap". Az eseményindítók listában jelölje ki az eseményindító: **SAP-üzenet érkezésekor**

   ![Add SAP trigger](./media/logic-apps-using-sap-connector/add-sap-trigger.png)

   Másik lehetőségként megnyithatja az **vállalati** lapra, és válassza az eseményindító:

   ![Add SAP trigger from Enterprise tab](./media/logic-apps-using-sap-connector/add-sap-trigger-ent-tab.png)

1. Ha a kapcsolat adatait kéri, hozzon létre most az SAP-kapcsolat. Ellenkező esetben, ha a kapcsolat már létezik, folytassa a következő lépés tehát beállíthatja az SAP-műveletet.

   **Helyszíni SAP-kapcsolat létrehozása**

   1. Adja meg a kapcsolati adatokat, az SAP-kiszolgáló. Az a **adatátjáró** tulajdonság, jelölje be az átjárót az átjáró telepítése az Azure Portalon létrehozott.

      Ha a **bejelentkezési típus** tulajdonsága **alkalmazáskiszolgáló**, ezeket a tulajdonságokat, amelyben a általában nem kötelező, szükségesek:

      ![SAP alkalmazás kiszolgálói kapcsolat létrehozása](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      Ha a **bejelentkezési típus** tulajdonsága **csoport**, ezeket a tulajdonságokat, amelyben a általában nem kötelező, szükségesek:

      ![SAP üzenet kiszolgálói kapcsolat létrehozása](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)  

      Alapértelmezés szerint erős beírni használatos érvénytelen értékeket a sémának XML-érvényesítés elvégzésével ellenőrizheti. Ez a viselkedés segítséget nyújt a korábbi hibák észlelése. A **biztonságos beírni** lehetőség a visszamenőleges kompatibilitás érdekében érhető el, és csak az a karakterlánc hosszának ellenőrzi. Tudjon meg többet a [ **biztonságos beírni** beállítás](#safe-typing).

1. Adja meg a szükséges paramétereket, az SAP-rendszer konfigurációja alapján.

   Opcionálisan megadhat egy vagy több SAP műveletet. Ezen a listán szereplő műveletek által az eseményindító az SAP-kiszolgáló az data gatewayen keresztül fogadott üzeneteket adja meg. Üres lista megadja, hogy az eseményindító minden üzenetet megkap. A lista egynél több üzenet rendelkezik, az eseményindító csak a listában megadott üzeneteket kap. Bármely más, az SAP-kiszolgáló küldött üzeneteket a rendszer elutasítja az átjáró.

   A Fájlkereső SAP művelet választhat:

   ![Az SAP-művelet kiválasztása](media/logic-apps-using-sap-connector/select-SAP-action-trigger.png)  

   Vagy manuálisan megadhat egy műveletet:

   ![Adja meg manuálisan az SAP-művelet](media/logic-apps-using-sap-connector/manual-enter-SAP-action-trigger.png) 

   A következő példa bemutatja, hogyan jelenik meg a műveletet a beállításakor az eseményindító egynél több üzenetet.

   ![Eseményindító-példa](media/logic-apps-using-sap-connector/example-trigger.png)  

   Az SAP-művelettel kapcsolatos további információkért lásd: [üzenet sémák IDOC-műveletek](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)

1. A logikai alkalmazás most már mentéséhez, így üzenetek fogadása az SAP-rendszerhez.
A tervező eszköztárán válassza a **Mentés** parancsot.

A logikai alkalmazás most már fogad üzeneteket az SAP-rendszer készen áll.

> [!NOTE]
> Az SAP-eseményindító nincs lekérdezési eseményindítóként, de egy webhook-alapú eseményindító helyette. Az eseményindító neve és az átjáró csak akkor, ha létezik egy üzenet, így nem a lekérdezés nem szükséges.

### <a name="test-your-logic-app"></a>A logikai alkalmazás tesztelése

1. A logikai alkalmazás elindításához, az SAP-rendszer üzenet küldése.

1. A logikai alkalmazás menüjében válassza a **áttekintése**, és tekintse át a **futtatási előzmények** a logikai alkalmazás minden olyan új futtatások.

1. Nyissa meg a legutóbbi futtatás, amelyen az eseményindító kimenetek szakaszban SAP rendszerről az üzenet látható.

## <a name="generate-schemas-for-artifacts-in-sap"></a>Az SAP sémák összetevők létrehozása

Ebben a példában egy HTTP-kérelem használ egy logikai alkalmazást, amely is indíthat. Az SAP-művelet egy kérést küld egy SAP-rendszerrel, a sémák megadott köztes dokumentumot (az idoc-hoz), és BAPI létrehozni. Az Azure Resource Manager-összekötő használatával, amely a válaszban visszaadandó sémák töltenek fel egy integrációs fiókban.

### <a name="add-http-request-trigger"></a>HTTP-kérés eseményindító hozzáadása

1. Az Azure Portalon hozzon létre egy üres logikai alkalmazást, amely megnyílik a Logikaialkalmazás-Tervező.

1. A Keresés mezőbe írja be a "http-kérelem" szűrőként. Az eseményindítók listában jelölje ki az eseményindító: **HTTP-kérés fogadásakor**

   ![HTTP-kérelem típusú trigger hozzáadása](./media/logic-apps-using-sap-connector/add-trigger.png)

1. Így létrehozhat egy végponti URL-cím a logikai alkalmazás most már a logikai alkalmazás mentése.
A tervező eszköztárán válassza a **Mentés** parancsot.

   A végpont URL-cím mostantól megjelenik az eseményindító, például:

   ![Végpont URL-címet generálni](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

### <a name="add-sap-action-to-generate-schemas"></a>Sémák létrehozni az SAP-művelet hozzáadása

1. A Logic App Designerben az eseményindító területén válassza a **új lépés**.

   ![Válassza az "Új lépés"](./media/logic-apps-using-sap-connector/add-action.png)

1. A Keresés mezőbe írja be szűrőként "sap". A műveletek listából válassza a következő műveletet: **Sémák készítése**
  
   ![Az SAP-küldési művelet kiválasztása](media/logic-apps-using-sap-connector/select-sap-schema-generator-action.png)

   Azt is megteheti, azt is beállíthatja a **vállalati** lapra, és válassza ki az SAP-műveletet.

   ![Select SAP send action from Enterprise tab](media/logic-apps-using-sap-connector/select-sap-schema-generator-ent-tab.png)

1. Ha a kapcsolat adatait kéri, hozzon létre most az SAP-kapcsolat. Ellenkező esetben, ha a kapcsolat már létezik, folytassa a következő lépés tehát beállíthatja az SAP-műveletet.

   **Helyszíni SAP-kapcsolat létrehozása**

   1. Adja meg a kapcsolati adatokat, az SAP-kiszolgáló. Az a **adatátjáró** tulajdonság, jelölje be az átjárót az átjáró telepítése az Azure Portalon létrehozott.

      Ha a **bejelentkezési típus** tulajdonsága **alkalmazáskiszolgáló**, ezeket a tulajdonságokat, amelyben a általában nem kötelező, szükségesek:

      ![SAP alkalmazás kiszolgálói kapcsolat létrehozása](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      Ha a **bejelentkezési típus** tulajdonsága **csoport**, ezeket a tulajdonságokat, amelyben a általában nem kötelező, szükségesek:

      ![SAP üzenet kiszolgálói kapcsolat létrehozása](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)

      Alapértelmezés szerint erős beírni használatos érvénytelen értékeket a sémának XML-érvényesítés elvégzésével ellenőrizheti. Ez a viselkedés segítséget nyújt a korábbi hibák észlelése. A **biztonságos beírni** lehetőség a visszamenőleges kompatibilitás érdekében érhető el, és csak az a karakterlánc hosszának ellenőrzi. Tudjon meg többet a [ **biztonságos beírni** beállítás](#safe-typing).

   1. Ha elkészült, kattintson a **Létrehozás** gombra. 
   
      A Logic Apps állít be, és gondoskodik róla, hogy a kapcsolat megfelelően működik, a kapcsolat tesztelése.

1. Adja meg az elérési útját, amelyhez hozzá szeretné létrehozni a sémát az összetevőben.

   A Fájlkereső választhat az SAP-művelet:

   ![Az SAP-művelet kiválasztása](media/logic-apps-using-sap-connector/select-SAP-action-schema-generator.png)  

   Vagy manuálisan adja meg a műveletet:

   ![Adja meg manuálisan az SAP-művelet](media/logic-apps-using-sap-connector/manual-enter-SAP-action-schema-generator.png)

   Egynél több összetevő sémák létrehozni, adja meg az SAP művelet részleteit minden egyes összetevő, például:

   ![Válassza ki az új elem hozzáadása](media/logic-apps-using-sap-connector/schema-generator-array-pick.png)

   ![A két elem megjelenítése](media/logic-apps-using-sap-connector/schema-generator-example.png)

   Az SAP-művelettel kapcsolatos további információkért lásd: [sémák IDOC-műveletek üzenet](https://docs.microsoft.com/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations).

1. Mentse a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés** parancsot.

### <a name="test-your-logic-app"></a>A logikai alkalmazás tesztelése

1. A Tervező eszköztárán válassza **futtatása** , a logikai alkalmazás futásának aktiválásához.

1. Nyissa meg a futtatást, és ellenőrizze a kimeneteit a **készítése a sémák** művelet.

   A kimenetek megjelenítése a generált sémák üzenetek megadott listája.

### <a name="upload-schemas-to-integration-account"></a>Töltse fel a sémákat az integrációs fiók

Szükség esetén töltse le, vagy a létrehozott sémák tárolása adattárak, például blob, a storage vagy az integrációs fiók. Integrációs fiókok más XML műveletekkel fürtkezelési élményt biztosít, így a Ez a példa bemutatja, hogyan tölthet fel sémákat az integrációs fiók ugyanazon a logikai alkalmazáshoz az Azure Resource Manager-összekötő használatával.

1. A Logic App Designerben az eseményindító területén válassza a **új lépés**.

1. A keresőmezőbe írja be a "Resource Manager" szűrőként. Ez a művelet kiválasztása: **Hozzon létre vagy erőforrás frissítése**

   ![Válassza ki az Azure Resource Manager-műveletet](media/logic-apps-using-sap-connector/select-azure-resource-manager-action.png)

1. Adja meg, hogy a művelet, beleértve az Azure-előfizetés, Azure-erőforráscsoportot és integrációs fiókban. SAP-jogkivonatokat a mezők hozzáadásához kattintson az adott mezők a mezők, és válassza ki a dinamikus tartalmú listából, amely akkor jelenik meg.

   1. Nyissa meg a **új paraméter hozzáadása** listában, és válassza ki a **hely** és **tulajdonságok** mezőket.

   1. Adja meg a részleteket az alábbi új mezők ebben a példában látható módon.

      ![Adja meg annak részleteit az Azure Resource Manager-művelet](media/logic-apps-using-sap-connector/azure-resource-manager-action.png)

   Az SAP **készítése a sémák** művelet sémák gyűjteményként, állít elő, ezért a tervező automatikusan hozzáad egy **minden** hurkot, és a műveletet. A következő példa bemutatja, hogyan jelenjen meg ez a művelet:

   ![Az Azure Resource Manager-művelet a "for each" hurok](media/logic-apps-using-sap-connector/azure-resource-manager-action-foreach.png)  

   > [!NOTE]
   > A sémák base64-kódolású formátumot használja. A sémák tölthet fel egy integrációs fiókhoz, akkor kell Nedal dekódovat segítségével a `base64ToString()` függvény. Íme egy példa, amely megjeleníti a kódját a `"properties"` elem:
   >
   > ```json
   > "properties": {
   >    "Content": "@base64ToString(items('For_each')?['Content'])",
   >    "ContentType": "application/xml",
   >    "SchemaType": "Xml"
   > }
   > ```

1. Mentse a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés** parancsot.

### <a name="test-your-logic-app"></a>A logikai alkalmazás tesztelése

1. A Tervező eszköztárán válassza **futtatása** manuálisan elindítani a logikai alkalmazást.

1. Sikeres után futtassa, nyissa meg az integrációs fiókba, és ellenőrizze, hogy létezik-e a létrehozott sémák.

## <a name="enable-secure-network-communications-snc"></a>Biztonságos hálózati kommunikációs (SNC) engedélyezése

Mielőtt elkezdené, győződjön meg arról, hogy megfelel-e a fent felsorolt [Előfeltételek](#pre-reqs):

* A helyszíni adatátjáró az SAP-rendszer ugyanazon a hálózaton lévő gépen telepítve van.

* Az egyszeri bejelentkezést, átjáró fut be, az SAP-felhasználó van leképezve.

* SNC-könyvtár, amely a további biztonsági funkciókat biztosít az adatátjáró ugyanazon a számítógépen telepítve van. Ezek a Példák többek között [sapseculib](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm), Kerberos, NTLM, és így tovább.

A kérelmek, illetve a SAP-rendszerhez SNC engedélyezéséhez jelölje be a **használata SNC** SAP kapcsolat jelölőnégyzetet, és adja meg ezeket a tulajdonságokat:

   ![Kapcsolat az SAP SNC konfigurálása](media/logic-apps-using-sap-connector/configure-sapsnc.png)

   | Tulajdonság | Leírás |
   |----------| ------------|
   | **SNC-könyvtár** | A SNC-könyvtár neve vagy Ice telepítési hely viszonyított elérési utat vagy abszolút elérési utat. Ha például `sapsnc.dll` vagy `.\security\sapsnc.dll` vagy `c:\security\sapsnc.dll` |
   | **SNC SSO** | SNC keresztül kapcsolódik, amikor a SNC identitás általában szolgál a hívó hitelesítéséhez. Egy másik lehetőség, hogy bírálja felül, hogy felhasználói és a jelszót a hívó hitelesítéséhez használható, de a sor továbbra is titkosítva van. |
   | **SNC saját név** | A legtöbb esetben ez a tulajdonság elhagyható. A telepített SNC-megoldás általában tudja a saját SNC nevét. Csak a "több identitást" támogató megoldások szüksége lehet az identitás, az adott cél vagy a kiszolgáló használandó adja meg. |
   | **SNC-Partner neve** | A háttérrendszer SNC neve |
   | **SNC-minőségi védelme** | A cél/kiszolgáló SNC-kommunikációhoz használandó szolgáltatás-minőségi. Alapértelmezett érték a háttérrendszer határozza meg. A biztonsági termék SNC használt határozza meg a maximális értéket. |
   |||

   > [!NOTE]
   > SNC_LIB és SNC_LIB_64 környezeti változók nem állítható a gépen adatátjáró és SNC-könyvtár esetében. Ha beállítása, azok lenne elsőbbséget élveznek az SNC-könyvtár érték az összekötőn keresztül.

<a name="safe-typing"></a>

## <a name="safe-typing"></a>Biztonságos beírása

Alapértelmezés szerint az SAP-kapcsolat létrehozásakor erős beírni szolgál érvénytelen értékeket a sémának XML-érvényesítés elvégzésével ellenőrizheti. Ez a viselkedés segítséget nyújt a korábbi hibák észlelése. A **biztonságos beírni** lehetőség a visszamenőleges kompatibilitás érdekében érhető el, és csak az a karakterlánc hosszának ellenőrzi. Ha úgy dönt, **biztonságos beírni**, DATS típusát és az SAP TIMS típus kezeli az karakterláncok helyett a XML megfelelőjükre `xs:date` és `xs:time` ahol `xmlns:xs="http://www.w3.org/2001/XMLSchema"`. Biztonságos beírni befolyásolja az összes séma létrehozásához, a "lett elküldve" hasznos és a "megkaptuk" választ, és az eseményindító az üzenetküldés viselkedését. 

Erős beírni használatakor (**biztonságos beírni** nincs engedélyezve), a sémát a DATS és TIMS típusú képez le egyszerűbb XML-típusok:

```xml
<xs:element minOccurs="0" maxOccurs="1" name="UPDDAT" nillable="true" type="xs:date"/>
<xs:element minOccurs="0" maxOccurs="1" name="UPDTIM" nillable="true" type="xs:time"/>
```

Erős beírni üzenetek küldésekor a DATS és TIMS válasz megfelel-e a megfelelő XML típusú formátumnak:

```xml
<DATE>9999-12-31</DATE>
<TIME>23:59:59</TIME>
```

Amikor **biztonságos beírni** van engedélyezve, a séma leképezi a DATS és TIMS típusok az XML-karakterlánc-mezők hossza korlátozások csak, például:

```xml
<xs:element minOccurs="0" maxOccurs="1" name="UPDDAT" nillable="true">
  <xs:simpleType>
    <xs:restriction base="xs:string">
      <xs:maxLength value="8" />
    </xs:restriction>
  </xs:simpleType>
</xs:element>
<xs:element minOccurs="0" maxOccurs="1" name="UPDTIM" nillable="true">
  <xs:simpleType>
    <xs:restriction base="xs:string">
      <xs:maxLength value="6" />
    </xs:restriction>
  </xs:simpleType>
</xs:element>
```

Az üzenetek küldésekor **biztonságos beírni** engedélyezve van, a DATS és TIMS válasz a következőhöz hasonlít:

```xml
<DATE>99991231</DATE>
<TIME>235959</TIME>
```


## <a name="known-issues-and-limitations"></a>Ismert problémák és korlátozások

Az alábbiakban a jelenleg ismert problémák és korlátozások az SAP-összekötőhöz:

* SAP hívása, vagy az üzenet csak egyetlen küldés tRFC együttműködik. Az üzleti alkalmazás alkalmazásprogramozási felületet (BAPI) véglegesítés minta, például több tRFC-hívást ugyanabban a munkamenetben, nem támogatott.

* Az SAP-trigger nem támogatja a kötegelt Idoc fogadása SAP. Ez a művelet RFC kapcsolódási hiba az SAP-rendszerhez, és az átjáró közötti eredményezhet.

* Az SAP-trigger nem támogatja az adatok átjárófürtök. Feladatátvételi esetenként az adatok átjárócsomópontnak, amely az SAP-rendszerrel kommunikál eltérhetnek az aktív csomópontra, nem várt viselkedést eredményez. Küldési forgatókönyvek esetén az adatok átjárófürtök támogatottak.

* Az SAP-összekötő jelenleg nem támogatja az SAP-útválasztó karakterláncokat. A helyszíni adatátjáró léteznie kell ugyanazon a helyi hálózaton, mint az SAP-rendszerhez kapcsolódni szeretne.

## <a name="connector-reference"></a>Összekötő-referencia

További technikai részletek korlátok, eseményindítók és műveletek, amely ismerteti az összekötő OpenAPI által (korábbi nevén Swagger) leírását, tekintse át a [összekötő referencialapja](/connectors/sap/).

## <a name="next-steps"></a>További lépések

* [Csatlakozhat a helyszíni rendszerek](../logic-apps/logic-apps-gateway-connection.md) logikai alkalmazásokból
* Ismerje meg, hogyan érvényesítéséhez, alakítson át, és a többi üzenet műveletek a a [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)
* További információk egyéb [Logic Apps-összekötők](../connectors/apis-list.md)
