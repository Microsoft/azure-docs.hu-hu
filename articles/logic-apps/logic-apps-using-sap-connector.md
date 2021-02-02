---
title: Kapcsolódás SAP-rendszerekhez
description: Az SAP-erőforrások elérése és kezelése a munkafolyamatok automatizálásával Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: estfan, daviburg, logicappspm
ms.topic: article
ms.date: 02/01/2021
tags: connectors
ms.openlocfilehash: edf4ce188c9239e697e2148d4fff51966d91f85a
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99252610"
---
# <a name="connect-to-sap-systems-from-azure-logic-apps"></a>Csatlakozás SAP-rendszerekhez az Azure Logic Appsből

Ez a cikk azt ismerteti, hogyan érheti el az SAP-erőforrásait Logic Apps az [SAP-összekötő](https://docs.microsoft.com/connectors/sap/)használatával.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha még nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* Egy logikai alkalmazás, amelyről el szeretné érni az SAP-erőforrásokat. Ha most ismerkedik a Logic Appsával, tekintse meg a [Logic Apps szolgáltatás áttekintése](../logic-apps/logic-apps-overview.md) című cikket, és az [első logikai alkalmazás létrehozásához szükséges rövid útmutatót a Azure Portal](../logic-apps/quickstart-create-first-logic-app-workflow.md).

    * Ha az SAP-összekötő korábbi, elavult verzióját használta, akkor az SAP-kiszolgálóhoz való csatlakozás előtt [át kell térnie az aktuális összekötőre](#migrate-to-current-connector) .

    * Ha a logikai alkalmazást több-bérlős Azure-ban futtatja, tekintse meg a [több-bérlős előfeltételek](#multi-tenant-azure-prerequisites)című témakört.

    * Ha a logikai alkalmazást prémium szintű[ integrációs szolgáltatási környezetben (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)futtatja, tekintse meg az [ISE előfeltételeit](#ise-prerequisites).

* Egy [SAP-alkalmazáskiszolgáló](https://wiki.scn.sap.com/wiki/display/ABAP/ABAP+Application+Server) vagy egy [SAP-üzenetkezelő kiszolgáló](https://help.sap.com/saphelp_nw70/helpdata/en/40/c235c15ab7468bb31599cc759179ef/frameset.htm) , amelyhez Logic apps szeretne hozzáférni. További információ az összekötővel használható SAP-kiszolgálókkal és SAP-műveletekkel kapcsolatban: [SAP-kompatibilitás](#sap-compatibility).

* Az SAP-kiszolgálónak küldendő üzenet tartalma, például egy minta IDoc-fájl. Ennek a tartalomnak XML formátumúnak kell lennie, és tartalmaznia kell a használni kívánt SAP-művelet névterét. A [IDocs egy XML-borítékba tördeléssel is elküldheti](#send-flat-file-idocs).

* Ha azt szeretné használni, hogy **mikor érkezik üzenet az SAP** triggerből, a következőket is el kell végeznie:

    * Állítsa be az SAP Gateway biztonsági engedélyeit a következő beállítással: `"TP=Microsoft.PowerBI.EnterpriseGateway HOST=<gateway-server-IP-address> ACCESS=*"`

    * Állítsa be az SAP Gateway biztonsági naplózását, hogy megkeresse Access Control listát (ACL). További információ: [SAP súgó témakör az átjárók naplózásának beállításához](https://help.sap.com/erp_hcm_ias2_2015_02/helpdata/en/48/b2a710ca1c3079e10000000a42189b/frameset.htm). Ellenkező esetben a következő hibaüzenet jelenik meg: `"Registration of tp Microsoft.PowerBI.EnterpriseGateway from host <host-name> not allowed"`

    > [!NOTE]
    > Ez az trigger ugyanazt az URI-helyet használja a megújításhoz és a leiratkozáshoz egy webhook-előfizetésből. A megújítási művelet a HTTP `PATCH` metódust használja, míg a leiratkozási művelet a http- `DELETE` metódust használja. Ez a viselkedés megújíthatja a megújítási műveletet az aktiválási előzményekben lemondási műveletként, de a művelet továbbra is megújítható, mert az trigger `PATCH` http-metódusként működik, nem `DELETE` .

### <a name="sap-compatibility"></a>SAP-kompatibilitás

Az SAP-összekötő a következő típusú SAP-rendszerekkel kompatibilis:

* Helyszíni és felhőalapú HANA-alapú SAP-rendszerek, például S/4 HANA.

* Klasszikus helyszíni SAP-rendszerek, például R/3 és ECC.

Az SAP-összekötő a következő üzenet-és adatintegrációs típusokat támogatja az SAP NetWeaver-alapú rendszerekből:

* Közbenső dokumentum (IDoc)

* Üzleti alkalmazásprogramozási felület (BAPI)

* Remote Function Call (RFC) és tranzakciós RFC (tRFC)

Az SAP-összekötő az [SAP .net Connector (NKH) függvénytárat](https://support.sap.com/en/product/connectors/msnet.html)használja. A következő SAP-műveleteket és triggereket használhatja az összekötővel:

* **Üzenet küldése az SAP** -nak, hogy [IDocs küldjön a tRFC](#send-idoc-action) művelethez, amely a következőhöz használható:

    * [BAPI függvények meghívása az RFC-en keresztül](#call-bapi-action)

    * RFC/tRFC hívása SAP-rendszerekben

    * Állapot-nyilvántartó munkamenetek létrehozása vagy lezárása

    * BAPI-tranzakciók véglegesítve vagy visszaállítása

    * Tranzakció azonosítójának megerősítése

    * IDocs küldése, IDoc állapotának beolvasása a számból, valamint a tranzakció IDocs listájának lekérése

    * SAP-táblázat beolvasása

* Az SAP triggertől kapott üzenet, amelyet a **következőhöz** használhat:

    * IDocs fogadása a tRFC-en keresztül

    * BAPI függvények hívása a tRFC-en keresztül

    * RFC/tRFC hívása SAP-rendszerekben

* **Sémák létrehozása** művelet, amely a IDOC, BAPI vagy RFC-hez készült SAP-összetevők sémáinak előállítására használható.

Ezeknek az SAP-műveleteknek a használatához először hitelesítenie kell a kapcsolódást a felhasználónévvel és a jelszóval. Az SAP-összekötő a [biztonságos hálózati kommunikációt (Snc)](https://help.sap.com/doc/saphelp_nw70/7.0.31/e6/56f466e99a11d1a5b00000e835363f/content.htm?no_cache=true)is támogatja. Használhatja a SNC-t az SAP NetWeaver egyszeri bejelentkezéshez (SSO), vagy a külső termékekből származó további biztonsági képességekhez. Ha a SNC-t használja, tekintse meg a [Snc előfeltételeit](#snc-prerequisites).

### <a name="migrate-to-current-connector"></a>Migrálás az aktuális összekötőre

A korábbi SAP-alkalmazáskiszolgáló és az SAP Message Server-összekötők 2020. február 29-én elavultak. Az aktuális SAP-összekötőre való áttéréshez kövesse az alábbi lépéseket:

1. Frissítse a helyszíni [adatátjárót](https://www.microsoft.com/download/details.aspx?id=53127) az aktuális verzióra. További információ: [helyszíni adatátjáró telepítése Azure Logic Appshoz](../logic-apps/logic-apps-gateway-install.md).

1. Az elavult SAP-összekötőt használó logikai alkalmazásban törölje a Küldés az **SAP** -ba műveletet.

1. Adja hozzá az **üzenet küldése az SAP** -művelethez az aktuális SAP-összekötőből.

1. Kapcsolódjon újra az SAP-rendszerhez az új műveletben.

1. Mentse a logikai alkalmazást.

### <a name="multi-tenant-azure-prerequisites"></a>A több-bérlős Azure előfeltételei

Ezek az előfeltételek akkor érvényesek, ha a logikai alkalmazás több-bérlős Azure-ban fut. A felügyelt SAP-összekötő nem fut natív módon egy [ISE](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)-ben.

> [!TIP]
> Ha prémium szintű ISE-t használ, akkor a felügyelt SAP-összekötő helyett használhatja az SAP ISE-összekötőt. További információ: az [ISE előfeltételei](#ise-prerequisites).

A felügyelt SAP-összekötő a helyszíni [adatátjárón](../logic-apps/logic-apps-gateway-connection.md)keresztül INTEGRÁLÓDIK az SAP-rendszerekkel. Az üzenetküldési forgatókönyvek esetében például, amikor egy logikai alkalmazásból egy SAP-rendszernek küldenek üzenetet, az adatátjáró RFC-ügyfélként működik, és továbbítja a logikai alkalmazástól az SAP-nek fogadott kérelmeket. Hasonlóképpen, a fogadási üzenetekben az adatátjáró olyan RFC-kiszolgálóként működik, amely fogadja az SAP-kéréseket, és továbbítja őket a logikai alkalmazásnak.

* [Töltse le és telepítse a helyszíni adatátjárót](../logic-apps/logic-apps-gateway-install.md) egy olyan gazdagépre vagy virtuális gépre, amely ugyanabban a virtuális hálózatban található, mint az a rendszer, amelyhez csatlakozik.

* [Hozzon létre egy Azure Gateway-erőforrást](../logic-apps/logic-apps-gateway-connection.md#create-azure-gateway-resource) a helyszíni adatátjáróhoz a Azure Portal. Ez az átjáró segítséget nyújt a helyszíni információk és erőforrások biztonságos elérésében. Győződjön meg arról, hogy az átjáró támogatott verzióját használja.

    * Ha az átjáróval kapcsolatos problémát tapasztal, próbálkozzon [a legújabb verzióra való frissítéssel](https://aka.ms/on-premises-data-gateway-installer), amely a probléma megoldásához szükséges frissítéseket is tartalmazhatja.

* [Töltse le és telepítse a legújabb SAP ügyféloldali kódtárat](#sap-client-library-prerequisites) ugyanarra a helyi számítógépre, mint a helyszíni adatátjárót.

### <a name="ise-prerequisites"></a>ISE előfeltételek

Ezek az előfeltételek akkor lépnek érvénybe, ha a logikai alkalmazást a prémium szintű ISE-ban futtatja. Azonban nem vonatkoznak a fejlesztői szintű ISE-ben futó logikai alkalmazásokra. Az ISE hozzáférést biztosít az Azure Virtual Network által védett erőforrásokhoz, és más ISE-natív összekötőket biztosít, amelyek lehetővé teszik a Logic apps számára, hogy a helyszíni adatátjáró használata nélkül közvetlenül hozzáférhessenek a helyszíni erőforrásokhoz.

> [!NOTE]
> Míg az SAP ISE-összekötő a fejlesztői szintű ISE-ben látható, az összekötő telepítése sikertelen lesz.

1. Ha még nem rendelkezik blob-tárolóval rendelkező Azure Storage-fiókkal, hozzon létre egy tárolót a [Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md) vagy a [Azure Storage Explorer](../storage/blobs/storage-quickstart-blobs-storage-explorer.md)használatával.

1. [Töltse le és telepítse a legújabb SAP ügyféloldali kódtárat](#sap-client-library-prerequisites) a helyi számítógépen. A következő szerelvény-fájloknak kell szerepelniük:

   * libicudecnumber.dll

   * rscp4n.dll

   * sapnco.dll

   * sapnco_utils.dll

1. Hozzon létre egy. zip fájlt, amely tartalmazza ezeket a szerelvény-fájlokat. Töltse fel a csomagot a blob-tárolóba az Azure Storage-ban.

1. A Azure Portal vagy Azure Storage Explorer területen keresse meg azt a tárolót, ahová a. zip fájlt feltöltötte.

1. Másolja a tároló helyének URL-címét. Ügyeljen arra, hogy tartalmazza a közös hozzáférés-aláírási (SAS) tokent, hogy az SAS-jogkivonat engedélyezve legyen. Ellenkező esetben az SAP ISE-összekötő üzembe helyezése meghiúsul.

1. Telepítse és helyezze üzembe az SAP-összekötőt az ISE-ben. További információ: ISE- [Összekötők hozzáadása](../logic-apps/add-artifacts-integration-service-environment-ise.md#add-ise-connectors-environment).

   1. A [Azure Portal](https://portal.azure.com)keresse meg és nyissa meg az ISE-t.

   1. Az ISE menüben válassza a **felügyelt összekötők** &gt; **Hozzáadás** elemet. Az összekötők listájában keresse meg és válassza ki az **SAP** elemet.

   1. Az **új felügyelt összekötő hozzáadása** ablaktábla **SAP-csomag** mezőjébe illessze be az SAP-szerelvényekkel rendelkező. zip-fájl URL-címét. Ismét ügyeljen arra, hogy tartalmazza az SAS-tokent.
 
  1. A **Létrehozás** gombra kattintva fejezze be az ISE-összekötő létrehozását.

1. Ha az SAP-példány és az ISE különböző virtuális hálózatokban vannak, akkor [ezeket a hálózatokat](../virtual-network/tutorial-connect-virtual-networks-portal.md) is össze kell kapcsolni, hogy azok csatlakoztatva legyenek.

### <a name="sap-client-library-prerequisites"></a>Az SAP-ügyfél függvénytárának előfeltételei

Ezek a-összekötővel használt SAP-ügyfél függvénytárának előfeltételei.

* Győződjön meg arról, hogy a legújabb verziót, az [SAP Connectort (nkh 3,0) telepíti Microsoft .net 3.0.22.0 a .NET-keretrendszer 4,0-Windows 64-bit (x64) használatával lefordítva](https://support.sap.com/en/product/connectors/msnet.html). Az SAP-NKH korábbi verziói problémákat tapasztalhatnak, ha egyszerre több IDoc üzenetet küld. Ez az állapot blokkolja az SAP-célhelyre küldött összes újabb üzenetet, ami miatt az üzenetek időtúllépést okoznak.

* Az SAP ügyféloldali kódtár 64 bites verzióját kell telepíteni, mert az adatátjáró csak 64 bites rendszereken fut. A nem támogatott 32-bites verzió telepítése "hibás rendszerkép" hibát eredményez.

* Másolja a szerelvény fájljait az alapértelmezett telepítési mappából egy másik helyre, az alábbiak alapján.

    * Az ISE-ben futó Logic apps esetén kövesse az [ISE előfeltételeit](#ise-prerequisites) .

    * A több-bérlős Azure-ban és a helyszíni adatátjárót használó Logic apps esetében másolja a szerelvény fájljait az adatátjáró telepítési mappájába. 

        
        * Ha az SAP-kapcsolat sikertelen a hibaüzenettel, **ellenőrizze a fiók adatait és/vagy engedélyeit, és próbálkozzon újra, és** ellenőrizze, hogy a szerelvény fájljait a adatátjáró telepítési mappájába másolta-e.
        
        * A [.net-szerelvény kötési naplófájljának](/dotnet/framework/tools/fuslogvw-exe-assembly-binding-log-viewer)használatával kapcsolatos további problémák elhárítása. Ezzel az eszközzel ellenőrizhető, hogy a szerelvény fájljai a megfelelő helyen találhatók-e. 
        
        * Szükség esetén a **globális szerelvény-gyorsítótár regisztrációja** lehetőséget is kiválaszthatja az SAP ügyféloldali kódtár telepítésekor.

Vegye figyelembe a következő kapcsolatokat az SAP ügyféloldali kódtár, a .NET-keretrendszer, a .NET-futtatókörnyezet és az átjáró között:

* A Microsoft SAP-adapter és az átjáró-gazda szolgáltatás egyaránt a .NET-keretrendszer 4.7.2 használja.

* A .NET-keretrendszer 4,0-es verziójának SAP-NKH a .NET Runtime 4,0-et 4,8-ot használó folyamatokkal működik.

* A .NET-keretrendszer 2,0-es verziójának SAP-NKH olyan folyamatokkal működik, amelyek a .NET Runtime 2,0-et használják a 3,5-ra, de már nem működik a legújabb átjáróval.

### <a name="snc-prerequisites"></a>A SNC előfeltételei

Ha helyszíni adatátjárót használ opcionális SNC-vel, amely csak a több-bérlős Azure-ban támogatott, ezeket a további beállításokat kell konfigurálnia.

Ha a SNC-t egyszeri bejelentkezéssel használja, győződjön meg arról, hogy az adatátjáró szolgáltatás olyan felhasználóként fut, aki az SAP-felhasználóhoz van leképezve. Az alapértelmezett fiók módosításához válassza a **fiók módosítása** lehetőséget, és adja meg a felhasználói hitelesítő adatokat.

![Képernyőkép a helyszíni adatátjáró beállításairól az Azure Portalon, a Szolgáltatásbeállítások lap megjelenítése a gomb használatával a kiválasztott átjárószolgáltatás módosításához.](./media/logic-apps-using-sap-connector/gateway-account.png)

Ha külső biztonsági termék használatával engedélyezi a SNC-t, másolja a SNC-könyvtárat vagy-fájlokat ugyanazon a számítógépen, ahol az adatátjáró telepítve van. Néhány példa a SNC-termékekre: [sapseculib](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm), KERBEROS és NTLM. További információ az adatátjáróhoz tartozó SNC engedélyezéséről: a [biztonságos hálózati kommunikáció engedélyezése](#enable-secure-network-communications).

## <a name="send-idoc-messages-to-sap-server"></a>IDoc-üzenetek küldése az SAP-kiszolgálónak

Ezeket a példákat követve hozzon létre egy logikai alkalmazást, amely egy IDoc üzenetet küld egy SAP-kiszolgálónak, és választ ad vissza:

1. [HTTP-kérelem által aktivált logikai alkalmazás létrehozása.](#create-http-request-trigger)

1. [Hozzon létre egy műveletet a munkafolyamatban, hogy üzenetet küldjön az SAP-nak.](#create-sap-action-to-send-message)

1. [Hozzon létre egy HTTP-válasz műveletet a munkafolyamatban.](#create-http-response-action)

1. [Hozzon létre egy Remote Function Call (RFC) kérelem-válasz mintát, ha egy RFC-t használ az SAP ABAP-ből érkező válaszok fogadásához.](#create-rfc-request-response)

1. [Tesztelje a logikai alkalmazást.](#test-logic-app)

### <a name="create-http-request-trigger"></a>HTTP-kérelem trigger létrehozása

> [!NOTE]
> Ha egy logikai alkalmazás IDocs kap az SAP-ból, a [kérelem-trigger](../connectors/connectors-native-reqres.md) mostantól támogatja az SAP egyszerű XML-formátumot. Ha egyszerű XML-ként szeretné fogadni a IDocs, használja az triggert, **Ha üzenet érkezik az SAP-ból**. Állítsa a **IDOC** paramétert a **SapPlainXml** értékre.

Először hozzon létre egy logikai alkalmazást egy Azure-végponttal, hogy *http post* -kérelmeket küldjön a logikai alkalmazásnak. Ha a logikai alkalmazás fogadja ezeket a HTTP-kéréseket, az [eseményindító elindít](../logic-apps/logic-apps-overview.md#logic-app-concepts) és futtatja a következő lépést a munkafolyamatban.

1. A [Azure Portal](https://portal.azure.com)hozzon létre egy üres logikai alkalmazást, amely megnyitja a **Logic apps designert**.

1. A keresőmezőbe írja be `http request` szűrőként a kifejezést. Az **Eseményindítók** listából válassza ki, **hogy mikor érkezik HTTP-kérelem**.

   ![Képernyőkép a Logic Apps Designerről, amely egy új HTTP-kérési triggert mutat be a logikai alkalmazásba.](./media/logic-apps-using-sap-connector/add-http-trigger-logic-app.png)

1. Mentse a logikai alkalmazást, hogy létrehozzon egy végponti URL-címet a logikai alkalmazáshoz. A tervező eszköztárán válassza a **Mentés** lehetőséget. A végpont URL-címe most megjelenik az triggerben. 

   ![Képernyőkép a Logic Apps Designerről, amely a lemásolt POST URL-címmel rendelkező HTTP-kérelem triggerét jeleníti meg.](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

### <a name="create-sap-action-to-send-message"></a>SAP-művelet létrehozása üzenet küldéséhez

Ezután hozzon létre egy műveletet, amely elküldi a IDoc-üzenetet az SAP-nak, amikor a [http-kérelem elindítja](#create-http-request-trigger) a tüzet.

1. A Logic Apps Designerben az trigger alatt válassza az **új lépés** lehetőséget.

   ![Képernyőkép a Logic Apps Designerről, amely egy új lépés hozzáadására szolgáló, szerkesztett logikai alkalmazást jelenít meg.](./media/logic-apps-using-sap-connector/add-sap-action-logic-app.png)

1. A keresőmezőbe írja be `sap` szűrőként a kifejezést. A **műveletek** listából válassza az **üzenet küldése az SAP**-nek lehetőséget.
  
   ![Képernyőkép a Logic Apps Designerről, amely a "üzenet küldése az SAP-be" műveletet mutatja.](media/logic-apps-using-sap-connector/select-sap-send-action.png)

   Vagy válassza a **vállalat** lapot, és válassza ki az SAP-műveletet.

   ![Képernyőkép a Logic Apps Designerről, amely az "üzenet küldése az SAP-nek" műveletet mutatja a vállalati lapon.](media/logic-apps-using-sap-connector/select-sap-send-action-ent-tab.png)

1. Ha a kapcsolódás már létezik, folytassa a következő lépéssel. Ha a rendszer kéri, hogy hozzon létre egy új kapcsolatot, adja meg a következő információkat a helyszíni SAP-kiszolgálóhoz való csatlakozáshoz.

   1. Adja meg a kapcsolatok nevét.

   1. Ha az adatátjárót használja, kövesse az alábbi lépéseket:
   
      1. Az **adatátjáró** szakaszban az **előfizetés** területen válassza ki azt az Azure-előfizetést az adatátjáró-erőforráshoz, amelyet az adatátjáró-telepítés Azure Portalban hozott létre.
   
      1. A **csatlakoztatási átjáró** területen válassza ki az adatátjáró-erőforrást az Azure-ban.

   1. Folytassa a kapcsolatok adatainak biztosítását. A **bejelentkezési típus** tulajdonságnál kövesse a lépést attól függően, hogy a tulajdonság az **Application Server** vagy a **Group** értékre van-e állítva:
   
      * Az **alkalmazáskiszolgáló** esetében ezek a tulajdonságok, amelyek általában opcionálisak, kötelezőek:

        ![SAP Application Server-kapcsolatok létrehozása](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * A **Group (csoport**) esetében ezek a tulajdonságok, amelyek általában opcionálisak, kötelezőek:

        ![SAP-üzenetkezelő kiszolgáló kapcsolatainak létrehozása](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)  

      Alapértelmezés szerint az erős beírással ellenőrizhető az érvénytelen értékek ellenőrzése a séma XML-érvényesítésének végrehajtásával. Ez a viselkedés segítséget nyújt a korábbi problémák észlelésében. A **biztonságos gépelési** lehetőség visszamenőleges kompatibilitáshoz érhető el, és csak a karakterlánc hosszát ellenőrzi. További információ a [biztonságos gépelési lehetőségről](#safe-typing).

   1. Ha elkészült, válassza a **Létrehozás** lehetőséget.

      Logic Apps beállítja és teszteli a kapcsolódást, hogy a kapcsolódás megfelelően működjön.

    > [!NOTE]

    > Ha a következő hibaüzenetet kapja, probléma merült fel az SAP NKH ügyféloldali függvénytárának telepítésekor: 
    >
    > **Nem sikerült a kapcsolatok tesztelése. Hiba: nem sikerült feldolgozni a kérést. Hiba részletei: "nem sikerült betölteni a (z)" sapnco, Version = 3.0.0.42, Culture = semleges, PublicKeyToken 50436dca5c7f7d23 "vagy annak egyik függőségét. A rendszeren nem található a megadott fájl.**
    >
    > Győződjön meg arról, hogy az [SAP NKH ügyféloldali függvénytárának szükséges verzióját telepíti, és teljesíti az összes többi előfeltételt](#sap-client-library-prerequisites).

1. Most keresse meg és válasszon ki egy műveletet az SAP-kiszolgálóról.

   1. Az **SAP-művelet** mezőben válassza a mappa ikont. A fájl listában keresse meg és válassza ki a használni kívánt SAP-üzenetet. A lista a nyilak használatával navigálható.

      Ez a példa egy IDoc jelöl ki a **megrendelések** típusával.

      ![IDoc művelet keresése és kiválasztása](./media/logic-apps-using-sap-connector/SAP-app-server-find-action.png)

      Ha nem találja a kívánt műveletet, manuálisan is megadhat egy elérési utat, például:

      ![Adja meg manuálisan a IDoc művelet elérési útját](./media/logic-apps-using-sap-connector/SAP-app-server-manually-enter-action.png)

      > [!TIP]
      > Adja meg az **SAP-művelet** értékét a kifejezés-szerkesztőben. Így ugyanazt a műveletet használhatja a különböző típusú üzenetekhez.

      További információ a IDoc-műveletekről: [IDoc-műveletek üzenet-sémái](/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations).

   1. Kattintson a **beviteli üzenet** szövegmezőbe, hogy megjelenjen a dinamikus tartalmak listája. A listából válassza ki a **törzs** MEZŐT a **http-kérelem fogadása** alatt.

      Ez a lépés a HTTP-kérelmi trigger törzsének tartalmát tartalmazza, és elküldi a kimenetet az SAP-kiszolgálónak.

      ![Válassza ki a "Body" tulajdonságot az triggerből](./media/logic-apps-using-sap-connector/SAP-app-server-action-select-body.png)

      Ha elkészült, az SAP-művelet a következő példához hasonlóan néz ki:

      ![Az SAP-művelet befejezése](./media/logic-apps-using-sap-connector/SAP-app-server-complete-action.png)

1. Mentse a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés** lehetőséget.

#### <a name="send-flat-file-idocs"></a>Sima fájl IDocs küldése

Ha XML-borítékba csomagolja őket, használhatja a IDocs. Ha IDoc szeretne küldeni, az általános utasítások alapján [hozzon létre egy SAP-műveletet a IDoc-üzenet elküldéséhez](#create-sap-action-to-send-message) a következő módosításokkal.

1. Az **üzenet küldése az SAP** -művelethez az SAP-műveleti URI-t használja `http://microsoft.lobservices.sap/2007/03/Idoc/SendIdoc` .

1. A bemeneti üzenet formázása XML-borítékkal. Példaként tekintse meg a következő példát:

```xml
<?xml version="1.0" encoding="utf-8"?>
<SendIdoc xmlns="http://Microsoft.LobServices.Sap/2007/03/Idoc/">
  <idocData>EDI_DC    300                      ORDERS052SAPMSS    LIMSFTABCSWI                                                                                           ED  93AORDERSOLP     VLTRFC    KUMSFTABCSWI                                                                                           13561                       231054476                                                                           20190523085430ORDERSORDERS05          US
E2EDK01005300                1     E2EDK010050     1       USD                                                                        Z4O14506907554
E2EDK03   300                2     E2EDK03   0     2   02220190523
E2EDKA1   300                3     E2EDKA1   0     2   RE                  MSFTASWI
E2EDKA1   300                4     E2EDKA1   0     2   US                  MSFTASWI
E2EDKA1   300                5     E2EDKA1   0     2   WE                  MSFTASWILIC
E2EDKA1   300                6     E2EDKA1   0     2   Z1 KKKKKKK                           ABC YYYYYYYYYYY ZZ                                                                                                                          BBBBBBBBBBBBBBBB 11                                                                                      ttttttttttt                                 6666              US                                                                                                999 999 99 99                                                                                                                SSSSSSS SSS SSSSSS                                                                                                                                SSSSSSS SSS SSSSSS
E2EDKA1   300                7     E2EDKA1   0     2   Z2 KKKKKKK                           BBBBBBBBBBBBBBBB DDDDDDDD ZZ                                                                                                                EEEEEEEEEEE 86                                                                                           rrrrrrrr                                    8888              US                                                                                                999 999 99 99                                                                                                                NNNNNN NNNNNN                                                                                                                                     NNNNNN NNNNNN
E2EDK02   300                8     E2EDK02   0     2   901Z
E2EDK02   300                9     E2EDK02   0     2   90399680096ZZS2002
E2EDK02   300                10    E2EDK02   0     2   902S
E2EDKT1   300                11    E2EDKT1   0     2   Z1EME
E2EDKT2   300                12    E2EDKT2   0     3   xxx@xxx-xx.xx
E2EDKT1   300                13    E2EDKT1   0     2   Z2EME
E2EDKT2   300                14    E2EDKT2   0     3   x.xxxxxx@xxxxxxxx-xxxxxxxxxx.xx
E2EDP01001300                15    E2EDP010010     2   10         1              EA                          999.9
E2EDP19   300                16    E2EDP19   0     3   00AAAA-11111</idocData>
</SendIdoc>
```



### <a name="create-http-response-action"></a>HTTP-válasz létrehozása művelet

Most adjon hozzá egy válasz műveletet a logikai alkalmazás munkafolyamataihoz, és adja meg az SAP-művelet kimenetét. Így a logikai alkalmazás az SAP-kiszolgáló eredményeit visszaadja az eredeti kérelmezőnek.

1. A Logic Apps Designer SAP művelet területén válassza az **új lépés** lehetőséget.

1. A keresőmezőbe írja be `response` szűrőként a kifejezést. A **műveletek** listából válassza a **Válasz** lehetőséget.

1. Kattintson a **törzs** mezőbe, hogy megjelenjen a dinamikus tartalmak listája. A listából válassza az **üzenet küldése az SAP**-be lehetőséget, majd a **törzs** mezőt.

   ![SAP-művelet végrehajtása](./media/logic-apps-using-sap-connector/select-sap-body-for-response-action.png)

1. Mentse a logikai alkalmazást.

#### <a name="create-rfc-request-response"></a>RFC-kérelem létrehozása – válasz

> [!NOTE]
> Az SAP-trigger IDocs kap a tRFC-en keresztül, amely nem rendelkezik a Response paraméterrel a kialakítás alapján. 

A kérések és válaszok mintáját akkor kell létrehoznia, ha egy távoli függvényhívás (RFC) használatával szeretne válaszokat kapni az SAP ABAP Logic Apps. Ahhoz, hogy IDocs kapjon a logikai alkalmazásban, első lépésként egy [http-kérelmet](../connectors/connectors-native-reqres.md#add-a-response-action) kell megtennie, `200 OK` és nem kell tartalmat tartalmaznia. Ez az ajánlott lépés a tRFC-en keresztüli aszinkron átvitelt azonnal végrehajtja az SAP-LUW, így az SAP CPIC-beszélgetés újra elérhetővé válik. Ezután hozzáadhat további műveleteket a logikai alkalmazásban a kapott IDoc feldolgozásához a további átvitelek blokkolása nélkül.

A kérelem és a válasz minta megvalósításához először fel kell derítenie az RFC-sémát a [ `generate schema` parancs](#generate-schemas-for-artifacts-in-sap)használatával. A létrehozott sémának két lehetséges legfelső csomópontja van: 

1. A kérelem csomópontja, amely az SAP-ból kapott hívás.

1. A válasz csomópont, amely a válasz az SAP-re.

A következő példában egy kérelem és egy válasz minta jön létre az RFC- `STFC_CONNECTION` modulból. A rendszer elemzi a kérés XML-fájlját egy olyan csomópont-érték kinyeréséhez, amelyben az SAP-kérelmek szerepelnek `<ECHOTEXT>` . A válasz dinamikus értékként szúrja be az aktuális időbélyeget. Hasonló választ kap, ha egy `STFC_CONNECTION` logikai alkalmazásból az SAP-be küld egy RFC-t.

```http

<STFC_CONNECTIONResponse xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
  <ECHOTEXT>@{first(xpath(xml(triggerBody()?['Content']), '/*[local-name()="STFC_CONNECTION"]/*[local-name()="REQUTEXT"]/text()'))}</ECHOTEXT>
  <RESPTEXT>Azure Logic Apps @{utcNow()}</RESPTEXT>


```

### <a name="test-logic-app"></a>A logikai alkalmazás tesztelése

1. Ha a logikai alkalmazás még nincs engedélyezve, a logikai alkalmazás menüjében válassza az **Áttekintés** lehetőséget. Az eszköztáron válassza az **Engedélyezés** lehetőséget.

1. A tervező eszköztárán válassza a **Futtatás** lehetőséget. Ez a lépés manuálisan elindítja a logikai alkalmazást.

1. Aktiválja a logikai alkalmazást egy HTTP POST-kérelem küldésével a HTTP-kérelemben megadott triggerben.
Adja meg az üzenet tartalmát a kérelemmel. A kérelem elküldéséhez olyan eszközt is használhat, mint például a [Poster](https://www.getpostman.com/apps).

   Ebben a cikkben a kérelem egy IDoc-fájlt küld, amelynek XML-formátumúnak kell lennie, és tartalmaznia kell az Ön által használt SAP-művelet névterét, például:

   ```xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <Send xmlns="http://Microsoft.LobServices.Sap/2007/03/Idoc/2/ORDERS05//720/Send">
      <idocData>
         <...>
      </idocData>
   </Send>
   ```

1. A HTTP-kérés elküldése után várjon a logikai alkalmazás válaszára.

   > [!NOTE]
   > Előfordulhat, hogy a logikai alkalmazás túllépi az időkorlátot, ha a válaszhoz szükséges összes lépés nem fejeződik be a [kérés időkorlátján](./logic-apps-limits-and-config.md)belül. Ha ez az állapot történik, előfordulhat, hogy a kérések le vannak tiltva. A problémák diagnosztizálásához megtudhatja, hogyan [ellenőrizheti és figyelheti a logikai alkalmazásokat](../logic-apps/monitor-logic-apps.md).

Ezzel létrehozott egy logikai alkalmazást, amely képes kommunikálni az SAP-kiszolgálóval. Most, hogy beállított egy SAP-csatlakozást a logikai alkalmazáshoz, megismerheti a többi elérhető SAP-műveletet, például a BAPI és az RFC-t is.

## <a name="receive-message-from-sap"></a>Üzenet fogadása az SAP-től

Ez a példa egy olyan logikai alkalmazást használ, amely akkor aktiválódik, amikor az alkalmazás egy SAP-rendszerből kap üzenetet.

### <a name="add-an-sap-trigger"></a>SAP-trigger hozzáadása

1. A Azure Portal hozzon létre egy üres logikai alkalmazást, amely megnyitja a Logic Apps designert.

1. A keresőmezőbe írja be `sap` szűrőként a kifejezést. Az **Eseményindítók** listából válassza ki, **hogy mikor érkezik üzenet az SAP-től**.

   ![SAP-trigger hozzáadása](./media/logic-apps-using-sap-connector/add-sap-trigger-logic-app.png)

   Vagy válassza a **vállalat** lapot, majd válassza ki az triggert:

   ![SAP-trigger hozzáadása a vállalati lapról](./media/logic-apps-using-sap-connector/add-sap-trigger-ent-tab.png)

1. Ha a kapcsolat már létezik, folytassa a következő lépéssel, hogy beállítsa az SAP-műveletet. Ha azonban a rendszer megkéri a kapcsolat részleteinek megadását, adja meg az adatokat, hogy most létrehozza a kapcsolatát a helyszíni SAP-kiszolgálóval.

   1. Adja meg a kapcsolatok nevét.

   1. Ha az adatátjárót használja, kövesse az alábbi lépéseket:

      1. Az **adatátjáró** szakaszban az **előfizetés** területen válassza ki azt az Azure-előfizetést az adatátjáró-erőforráshoz, amelyet az adatátjáró-telepítés Azure Portalban hozott létre.

      1. A **csatlakoztatási átjáró** területen válassza ki az adatátjáró-erőforrást az Azure-ban.

   1. Folytassa a kapcsolatok adatainak biztosítását. A **bejelentkezési típus** tulajdonságnál kövesse a lépést attól függően, hogy a tulajdonság az **Application Server** vagy a **Group** értékre van-e állítva:

      * Az **alkalmazáskiszolgáló** esetében ezek a tulajdonságok, amelyek általában opcionálisak, kötelezőek:

        ![SAP Application Server-kapcsolatok létrehozása](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * A **Group (csoport**) esetében ezek a tulajdonságok, amelyek általában opcionálisak, kötelezőek:

        ![SAP-üzenetkezelő kiszolgáló kapcsolatainak létrehozása](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)

      Alapértelmezés szerint az erős beírással ellenőrizhető az érvénytelen értékek ellenőrzése a séma XML-érvényesítésének végrehajtásával. Ez a viselkedés segítséget nyújt a korábbi problémák észlelésében. A **biztonságos gépelési** lehetőség visszamenőleges kompatibilitáshoz érhető el, és csak a karakterlánc hosszát ellenőrzi. További információ a [biztonságos gépelési lehetőségről](#safe-typing).

   1. Ha elkészült, válassza a **Létrehozás** lehetőséget.

      Logic Apps beállítja és teszteli a kapcsolódást, hogy a kapcsolódás megfelelően működjön.

1. Adja meg a [szükséges paramétereket](#parameters) az SAP rendszerkonfigurációja alapján. 

   Az SAP [-kiszolgálótól kapott üzeneteket szűrheti az SAP-műveletek listájának megadásával](#filter-with-sap-actions).

   Kiválaszthat egy SAP-műveletet a file pickerből:

   ![SAP-művelet hozzáadása a logikai alkalmazáshoz](media/logic-apps-using-sap-connector/select-SAP-action-trigger.png)  

   Vagy manuálisan is megadhat egy műveletet:

   ![Adja meg manuálisan a használni kívánt SAP-műveletet](media/logic-apps-using-sap-connector/manual-enter-SAP-action-trigger.png)

   Az alábbi példa bemutatja, hogyan jelenik meg a művelet, amikor beállítja, hogy a trigger egynél több üzenetet kapjon.

   ![Több üzenetet fogadó trigger – példa](media/logic-apps-using-sap-connector/example-trigger.png)

   Az SAP-művelettel kapcsolatos további információkért lásd: [IDoc-műveletek üzenet-sémái](/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)

1. Most mentse a logikai alkalmazást, így elkezdheti az SAP-rendszerből érkező üzenetek fogadását. A tervező eszköztárán válassza a **Mentés** lehetőséget.

A logikai alkalmazás most már készen áll az SAP-rendszerből érkező üzenetek fogadására.

> [!NOTE]
> Az SAP-trigger nem egy lekérdezési trigger, de egy webhook-alapú trigger helyette. Ha az adatátjárót használja, az trigger csak akkor lesz meghívva az adatátjáróról, ha van ilyen üzenet, ezért nincs szükség lekérdezésre.

#### <a name="parameters"></a>Paraméterek

Az egyszerű karakterlánc-és szám típusú bemenetekkel együtt az SAP-összekötő a következő táblázatos paramétereket ( `Type=ITAB` bemeneteket) fogadja el:

* Táblázat irányának paraméterei, a bemenet és a kimenet is a régebbi SAP-kiadásokhoz.

* A paraméterek módosítása, amelyek lecserélik a tábla irányának paramétereit az újabb SAP-kiadásokra.

* Hierarchikus tábla paramétereinek

#### <a name="filter-with-sap-actions"></a>Szűrés SAP-műveletekkel

Lehetősége van arra, hogy a logikai alkalmazás által az SAP-kiszolgálótól kapott üzeneteket egy vagy több SAP-művelettel rendelkező lista vagy tömb megadásával szűrheti. Alapértelmezés szerint ez a tömb üres, ami azt jelenti, hogy a logikai alkalmazás a szűrés nélkül megkapja az összes üzenetet az SAP-kiszolgálótól. 

A tömb szűrő beállításakor az trigger csak a megadott SAP-tevékenységtípusok üzeneteit fogadja, és elutasítja az SAP-kiszolgálóról érkező összes többi üzenetet. Ez a szűrő azonban nem befolyásolja, hogy a kapott tartalom beírása gyenge vagy erős.

Az SAP-műveletek szűrése a helyszíni adatátjáróhoz tartozó SAP-adapter szintjén történik. További információ: [hogyan küldhet teszt IDocs az SAP-Logic apps](#test-sending-idocs-from-sap).

Ha nem tud IDoc-csomagokat küldeni az SAP-ból a logikai alkalmazás triggerére, tekintse meg a tranzakciós RFC (tRFC) Call elutasítás üzenetet az SAP tRFC párbeszédpanelen (T-Code SM58). Az SAP-felületen a következő hibaüzenetek jelenhetnek meg, amelyek az **állapot szövegmezőben** lévő alsztringek korlátai miatt lesznek levágva.

* `The RequestContext on the IReplyChannel was closed without a reply being`: Váratlan hibák történnek, amikor a csatorna összes kezelője leállítja a csatornát egy hiba miatt, és újraépíti a csatornát más üzenetek feldolgozására.

  * Ha szeretné megismerni, hogy a logikai alkalmazás fogadta a IDoc, [adjon hozzá egy](../connectors/connectors-native-reqres.md#add-a-response-action) , az állapotkódot visszaadó Response műveletet `200 OK` . A IDoc a tRFC-en keresztül történik, ami nem teszi lehetővé a válasz adattartalommal való elszállítását.

  * Ha ehelyett a IDoc el kell utasítania, akkor az SAP-adaptertől eltérő HTTP-állapotkódot kell válaszolnia az `200 OK` Ön nevében. 

* `The segment or group definition E2EDK36001 was not found in the IDoc meta`: A várt hibák más hibákkal történnek, például a IDoc XML-adattartalom létrehozása nem sikerült, mert a szegmenseit nem az SAP bocsátja ki, így az átalakításhoz szükséges szegmens típusú metaadatok hiányoznak. 

  * Ahhoz, hogy ezeket a szegmenseket az SAP felszabadítsa, forduljon az SAP-rendszerhez készült ABAP mérnökhez.
### <a name="asynchronous-request-reply-for-triggers"></a>Aszinkron kérelem – válasz az eseményindítók számára

Az SAP-összekötő támogatja az Azure [aszinkron kérelem-válasz mintát](/azure/architecture/patterns/async-request-reply.md) Logic apps eseményindítók esetében. Ezzel a mintával olyan sikeres kérelmek hozhatók létre, amelyek az alapértelmezett szinkron kérés-válasz mintával eltérően lettek volna. 

> [!TIP]
> A több Response művelettel rendelkező Logic apps esetében az összes válasz műveletnek ugyanazt a kérés-válasz mintát kell használnia. Ha például a logikai alkalmazás több lehetséges választ tartalmazó kapcsoló vezérlőt használ, akkor az összes válasz műveletet úgy kell konfigurálnia, hogy ugyanazt a kérés-válasz mintát használja, szinkron vagy aszinkron módon. 

A válasz aszinkron válaszának engedélyezése lehetővé teszi, hogy a logikai alkalmazás válaszoljon a `202 Accepted` válaszra, ha a kérést elfogadták a feldolgozásra. A válasz tartalmaz egy Location fejlécet, amely a kérelem végső állapotának lekérésére használható.

Aszinkron kérelem – válasz minta beállítása a logikai alkalmazáshoz az SAP Connector használatával:

1. Nyissa meg a logikai alkalmazást a **Logic apps Designerben**.

1. Győződjön meg arról, hogy az SAP-összekötő a logikai alkalmazás triggere.

1. Nyissa meg a logikai alkalmazás **válaszának** műveletét. A művelet címsorában válassza a menüt (**..**.) &gt; **Beállítások**.

1. A válasz **beállításainál** kapcsolja be a váltást az **aszinkron válasz** alatt. Válassza a kész lehetőséget.

1. Mentse a logikai alkalmazás módosításait.

## <a name="find-extended-error-logs"></a>Bővített hibanaplók keresése

A teljes hibaüzeneteket az SAP-adapter kiterjesztett naplófájljaiban találja. [Kibővített naplófájlt is engedélyezhet az SAP-összekötőhöz](#extended-sap-logging-in-on-premises-data-gateway).

A helyszíni adatátjárók 2020-es és újabb verzióiban az [alkalmazás beállításaiban engedélyezheti az átjáró naplóit](/data-integration/gateway/service-gateway-tshoot#collect-logs-from-the-on-premises-data-gateway-app). 

* Az alapértelmezett naplózási szint a **Figyelmeztetés**.

* Ha engedélyezi a  **további naplózást** a helyszíni adatátjáró-alkalmazás **diagnosztikai** beállításaiban, a naplózási szint a **tájékoztatási** szintre nő.

* A naplózási szint **részletességének** növeléséhez frissítse a következő beállítást a konfigurációs fájlban. A konfigurációs fájl általában a következő helyen található: `C:\Program Files\On-premises data gateway\Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config` .

```json
<setting name="SapTraceLevel" serializeAs="String">
   <value>Verbose</value>
</setting>
```

A helyszíni adatátjárók esetében az április 2020-es és korábbi verzióiban a naplók alapértelmezés szerint le vannak tiltva.

### <a name="extended-sap-logging-in-on-premises-data-gateway"></a>Kiterjesztett SAP-naplózás a helyszíni adatátjáróban

Ha helyszíni [adatátjárót használ a Logic Appshoz](../logic-apps/logic-apps-gateway-install.md), beállíthat egy kiterjesztett naplófájlt az SAP-összekötőhöz. A helyszíni adatátjáróval átirányíthatja Windows esemény-nyomkövetés (ETW) eseményeit az átjáró naplózási. zip fájljaiban található, forgó naplófájlokra. 

Az átjáró [konfigurációjának és szolgáltatásának összes naplóját exportálhatja](https://docs.microsoft.com/data-integration/gateway/service-gateway-tshoot#collect-logs-from-the-on-premises-data-gateway-app) egy. zip fájlba az átjáró alkalmazás beállításaiban.

> [!NOTE]
> Ha mindig engedélyezve van, a kiterjesztett naplózás hatással lehet a Logic apps teljesítményére. Az ajánlott eljárás a kiterjesztett naplófájlok kikapcsolására, miután végzett a probléma elemzésével és hibaelhárításával.

#### <a name="capture-etw-events"></a>ETW események rögzítése

Igény szerint a haladó felhasználók közvetlenül is rögzíthetik a ETW eseményeket. Ezután felhasználhatja az [adatokat Event Hubs Azure Diagnosticsban,](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-stream-event-hubs) vagy [adatokat gyűjthet Azure monitor naplókba](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-logs). További információ: az [adatok gyűjtésével és tárolásával kapcsolatos ajánlott eljárások](https://docs.microsoft.com/azure/architecture/best-practices/monitoring#collecting-and-storing-data). A [perfview eszköz](https://github.com/Microsoft/perfview/blob/master/README.md) használatával dolgozhat a létrejövő ETL-fájlokkal, vagy írhat saját programot is. Ez az útmutató az Perfview eszköz-t használja:

1. Az események rögzítéséhez a perfview eszköz **menüben válassza a gyűjtés összegyűjtése lehetőséget** &gt;  .

1. A **további szolgáltató** mezőben adja meg a SAP- `*Microsoft-LobAdapter` adapterek eseményeinek rögzítéséhez szükséges SAP-szolgáltatót. Ha nem adja meg ezt az információt, a nyomkövetés csak általános ETW eseményeket tartalmaz.

1. Tartsa meg a többi alapértelmezett beállítást. Ha szeretné, módosíthatja a fájl nevét vagy helyét az **adatfájl** mezőben.

1. Válassza a **gyűjtemény elindítása** lehetőséget a nyomkövetés megkezdéséhez.

1. A probléma reprodukálása vagy elég elemzési adatok gyűjtése után válassza a **gyűjtemény leállítása** lehetőséget.

Az adatok más féllel, például Azure-támogatási mérnökökkel való megosztásához tömörítse az ETL-fájlt.

A nyomkövetés tartalmának megtekintése:

1. A perfview eszköz területen válassza a **fájl** &gt; **megnyitása** lehetőséget, és válassza ki az imént generált ETL-fájlt.

1. A Perfview eszköz oldalsávban az ETL-fájl **események** szakasza látható.

1. A **Filter (szűrés**) elemnél a szűréshez `Microsoft-LobAdapter` a következőt kell megtekintenie: a kapcsolódó események és átjárók

### <a name="test-your-logic-app"></a>A logikai alkalmazás tesztelése

1. A logikai alkalmazás elindításához küldjön üzenetet az SAP-rendszerből.

1. A logikai alkalmazás menüjében válassza az **Áttekintés** lehetőséget. Tekintse át a logikai alkalmazás **futtatási előzményeit** .

1. Nyissa meg a legutóbbi futtatást, amely az SAP-rendszerből a trigger kimenetek szakaszban küldött üzenetet mutatja.

### <a name="test-sending-idocs-from-sap"></a>IDocs küldésének tesztelése az SAP-ból

A IDocs SAP-ból logikai alkalmazásba való elküldéséhez a következő minimális konfiguráció szükséges:

> [!IMPORTANT]
> Ezeket a lépéseket csak akkor használja, ha a logikai alkalmazással teszteli az SAP-konfigurációt. Az éles környezetek további konfigurálást igényelnek.

1. [RFC-cél konfigurálása az SAP-ben](#create-rfc-destination)

1. [ABAP-kapcsolatok létrehozása az RFC-célhoz](#create-abap-connection)

1. [Fogadó port létrehozása](#create-receiver-port)

1. [Feladó portjának létrehozása](#create-sender-port)

1. [Logikai rendszerpartner létrehozása](#create-logical-system-partner)

1. [Partneri profil létrehozása](#create-partner-profiles)

1. [Üzenetek küldésének tesztelése](#test-sending-messages)

#### <a name="create-rfc-destination"></a>RFC-cél létrehozása

1. Az RFC- **kapcsolatok beállításainak konfigurálásához** az SAP-felületen használja az **sm59** -tranzakció kódját (T-Code) a **/n** előtaggal.

1. Válassza a **TCP/IP-kapcsolatok**  >  **Létrehozás** lehetőséget.

1. Hozzon létre egy új RFC-célhelyet a következő beállításokkal:
    
    * Az **RFC-cél** mezőben adjon meg egy nevet.
    
    * A **technikai beállítások** lapon az **aktiválás típusa** beállításnál válassza a **regisztrált kiszolgáló program** elemet. A **program azonosítójának** megadásához adjon meg egy értéket. Az SAP-ben a logikai alkalmazás triggere az azonosító használatával lesz regisztrálva.
    
    * A **Unicode** lapon a **rendszerhez tartozó kommunikációs típushoz** válassza a **Unicode** lehetőséget.

1. Mentse a módosításokat.

1. Regisztrálja az új **program azonosítóját** a Azure Logic apps.

1. A kapcsolat teszteléséhez az SAP-felületen az új **RFC-cél** területen válassza a **kapcsolat tesztelése** elemet.

#### <a name="create-abap-connection"></a>ABAP-kapcsolatok létrehozása

1. Az RFC- **kapcsolatok beállításainak konfigurálásához** az SAP-felületen használja az **sm59** _ tranzakciós kódot (T-Code) az _ */n** előtaggal.

1. Válassza ki a **ABAP kapcsolatok**  >  **létrehozása** lehetőséget.

1. Az **RFC-cél** mezőben adja meg a [teszt SAP-rendszer](#create-rfc-destination)azonosítóját.

1. Mentse a módosításokat.

1. A kapcsolódás teszteléséhez válassza a **kapcsolódási teszt** lehetőséget.

#### <a name="create-receiver-port"></a>Fogadó port létrehozása

1. A IDOC- **feldolgozási** beállításokban található portok megnyitásához az SAP-felületen használja az **we21** -tranzakció kódját (T-Code) a **/n** előtaggal.

1. Válassza a **portok**  >  **tranzakciós RFC**-  >  **Létrehozás** lehetőséget.

1. A megnyíló beállítások mezőben válassza a **saját port neve** lehetőséget. A tesztelési porthoz adjon meg egy **nevet**. Mentse a módosításokat.

1. Az új fogadó port beállításainál az **RFC-cél** mezőben adja meg a [teszt RFC-cél](#create-rfc-destination)azonosítóját.

1. Mentse a módosításokat.

#### <a name="create-sender-port"></a>Feladó portjának létrehozása

1.  A IDOC- **feldolgozási** beállításokban található portok megnyitásához az SAP-felületen használja az **we21** -tranzakció kódját (T-Code) a **/n** előtaggal.

1. Válassza a **portok**  >  **tranzakciós RFC**-  >  **Létrehozás** lehetőséget.

1. A megnyíló beállítások mezőben válassza a **saját port neve** lehetőséget. A tesztelési porthoz adjon meg egy olyan **nevet** , amely az **SAP**-vel kezdődik. Az összes küldő port nevének az **SAP** betűvel kell kezdődnie, például **SAPTEST**. Mentse a módosításokat.

1. Az új feladó portjának beállításaiban az RFC- **cél** mezőben adja meg a ABAP- [kapcsolatok](#create-abap-connection)azonosítóját.

1. Mentse a módosításokat.

#### <a name="create-logical-system-partner"></a>Logikai rendszerpartner létrehozása

1. A **"logikai rendszerek" nézet módosítása: az áttekintő** beállítások az SAP-felületen a **bd54** -tranzakciós kód (T-Code) használatával nyitható meg.

1. Fogadja el a megjelenő figyelmeztető üzenetet: **Vigyázat: a tábla több ügyfél**

1. A meglévő logikai rendszereket megjelenítő lista felett válassza az **új bejegyzések** lehetőséget.

1. Az új logikai rendszer esetében adjon meg egy **Log.System** -azonosítót és egy rövid **nevet** . Mentse a módosításokat.

1. Amikor megjelenik a **Workbench Rákérdezése** , hozzon létre egy új kérést egy leírás megadásával, vagy ha már létrehozott egy kérést, hagyja ki ezt a lépést.

1. Miután létrehozta a Workbench-kérelmet, csatolja a kérést a tábla frissítési kérelméhez. A táblázat frissítésének megerősítéséhez mentse a módosításokat.

#### <a name="create-partner-profiles"></a>Partneri profilok létrehozása

Éles környezetekben két partneri profilt kell létrehoznia. Az első profil a küldő, amely a szervezet és az SAP-rendszer. A második profil a fogadó, amely a logikai alkalmazás.

1. A **partneri profilok** beállításainak megnyitásához az SAP-felületen használja az **we20** -tranzakció kódját (T-Code) a **/n** előtaggal.

1. A **partneri profilok** területen válassza az **ls Create partner típusa** lehetőséget  >  .

1. Hozzon létre egy új partner-profilt a következő beállításokkal:

    * A **partner nem.** mezőben adja meg [a logikai rendszer partnerének azonosítóját](#create-logical-system-partner).

    * **Partn. Írja be a következőt**: **ls**.

    * Az **ügynök** mezőben adja meg az SAP-felhasználói fiók azonosítóját, amelyet akkor kell használni, ha Azure Logic apps vagy más nem SAP rendszerekhez regisztrálja a program azonosítóit.

1. Mentse a módosításokat. Ha még nem [hozta létre a logikai rendszerpartnert](#create-logical-system-partner), a hibaüzenetet kapja meg, és **adjon meg egy érvényes partneri számot**.

1. A partner profiljának beállításaiban a **kimenő parmtrs** alatt válassza a **kimenő paraméter létrehozása** lehetőséget.

1. Hozzon létre egy új kimenő paramétert a következő beállításokkal:

    * Adja meg az **üzenet típusát**(például: **cremas**).

    * Adja meg a [fogadó portjának azonosítóját](#create-receiver-port).

    * Adja meg a csomag IDoc méretét **. Méret**. Vagy ha az [SAP-ból egyszerre csak egy IDocs szeretne küldeni](#receive-idoc-packets-from-sap), válassza a **IDoc azonnali továbbítása** lehetőséget.

1. Mentse a módosításokat.

#### <a name="test-sending-messages"></a>Üzenetek küldésének tesztelése

1. A IDoc- **feldolgozási beállítások tesztelési eszközének** megnyitásához az SAP-felületen használja az **we19** -tranzakció kódját (T-Code) a **/n** előtaggal.

1. A **sablon a teszteléshez** területen válassza az **üzenet típusa** lehetőséget, majd írja be az üzenet típusát (például: **cremas**). Válassza a **Létrehozás** lehetőséget.

1. A **Folytatás** gombra kattintva erősítse meg, hogy **melyik IDoc-típust?** üzenet jelenik meg.

1. Válassza ki a **EDIDC** csomópontot. Adja meg a fogadó és a küldő portok megfelelő értékeit. Válassza a **Folytatás** lehetőséget.

1. Válassza a **normál kimenő feldolgozás** lehetőséget.

1. A kimenő IDoc-feldolgozás indításához válassza a **Folytatás** lehetőséget. A feldolgozás befejezésekor a IDoc az **SAP-rendszerbe vagy külső programba küldött** üzenet jelenik meg.

1.  A feldolgozási hibák kereséséhez használja a **sm58** -tranzakció kódját (T-Code) a **/n** előtaggal.

## <a name="receive-idoc-packets-from-sap"></a>IDoc-csomagok fogadása az SAP-ból

Beállíthatja az SAP-t, hogy [IDocs küldjön a csomagokban](https://help.sap.com/viewer/8f3819b0c24149b5959ab31070b64058/7.4.16/4ab38886549a6d8ce10000000a42189c.html), amelyek kötegek vagy IDocs-csoportok. A IDoc-csomagok fogadásához az SAP-összekötőt, és különösen az triggert nem kell külön konfigurálni. Ahhoz azonban, hogy az trigger megkapja a csomagot, a IDoc-csomagok minden egyes elemét fel kell dolgozni, néhány további lépés szükséges a csomag különálló IDocs való felosztásához.

Az alábbi példa bemutatja, hogyan lehet kinyerni az egyes IDocs egy csomagból a következő [ `xpath()` függvény](./workflow-definition-language-functions-reference.md#xpath)használatával:

1. A Kezdés előtt egy SAP-triggerrel rendelkező logikai alkalmazásra van szükség. Ha még nem rendelkezik ezzel a logikai alkalmazással, az ebben a témakörben ismertetett lépéseket követve [beállíthat egy SAP-triggerrel rendelkező logikai alkalmazást](#receive-message-from-sap).

   Például:

   ![SAP-trigger hozzáadása a logikai alkalmazáshoz](./media/logic-apps-using-sap-connector/first-step-trigger.png)

1. Szerezze be a gyökér névteret a logikai alkalmazás által az SAP-től kapott XML-IDoc. A névtér XML-dokumentumból való kinyeréséhez adjon hozzá egy olyan lépést, amely létrehoz egy helyi karakterlánc-változót, és egy kifejezéssel tárolja a névteret `xpath()` :

   `xpath(xml(triggerBody()?['Content']), 'namespace-uri(/*)')`

   ![Gyökérszintű névtér beolvasása a IDoc](./media/logic-apps-using-sap-connector/get-namespace.png)

1. Egy adott IDoc kinyeréséhez adjon hozzá egy olyan lépést, amely létrehoz egy tömböt változót, és egy másik kifejezéssel tárolja a IDoc gyűjteményt `xpath()` :

   `xpath(xml(triggerBody()?['Content']), '/*[local-name()="Receive"]/*[local-name()="idocData"]')`

   ![Elemek tömbének beolvasása](./media/logic-apps-using-sap-connector/get-array.png)

   A Array változó lehetővé teszi, hogy minden IDoc elérhető legyen a logikai alkalmazás számára a gyűjteményre való enumerálással. Ebben a példában a logikai alkalmazás az egyes IDoc egy SFTP-kiszolgálóra továbbítja egy hurok használatával:

   ![IDoc küldése SFTP-kiszolgálónak](./media/logic-apps-using-sap-connector/loop-batch.png)

   Minden IDoc tartalmaznia kell a legfelső szintű névteret, ami azt okozza, hogy a fájl tartalma miért van becsomagolva egy `<Receive></Receive` elembe a legfelső szintű névtérrel együtt, mielőtt elküldi a IDoc az alsóbb rétegbeli alkalmazásba vagy SFTP-kiszolgálóra ebben az esetben.

A minta rövid útmutató sablonjának használatával új logikai alkalmazás létrehozásakor a Logic Apps Designer alkalmazásban kiválaszthatja ezt a sablont.

![Batch Logic app-sablon kiválasztása](./media/logic-apps-using-sap-connector/select-batch-logic-app-template.png)

## <a name="generate-schemas-for-artifacts-in-sap"></a>Sémák előállítása összetevők számára az SAP-ban

Ez a példa egy logikai alkalmazást használ, amely HTTP-kéréssel aktiválható. A megadott IDoc és BAPI sémáinak létrehozásához a **séma-létrehozási** SAP-művelet elküld egy kérést egy SAP-rendszernek.

Ez az SAP-művelet egy [XML-sémát](#sample-xml-schemas)ad vissza, nem pedig maga az XML-dokumentum tartalmát vagy adatmennyiségét. A válaszban visszaadott sémákat az Azure Resource Manager-összekötő használatával kell feltölteni egy integrációs fiókba. A sémák a következő részeket tartalmazzák:

* A kérelem üzenetének szerkezete. Ezeket az információkat a BAPI listájának megalkotása céljából használhatja `get` .

* A válaszüzenet szerkezete. Ezekkel az információkkal elemezheti a választ. 

A kérelem üzenetének elküldéséhez használja az általános SAP-művelet **üzenetet küld az SAP**-nek, vagy a **meghívott hívási BAPI** műveleteket.

### <a name="sample-xml-schemas"></a>Minta XML-sémák

Ha megtanulja, hogyan hozhat létre egy XML-sémát a minta dokumentum létrehozásához, tekintse meg a következő mintákat. Ezek a példák bemutatják, hogyan dolgozhat számos típusú hasznos adattal, többek között a következőkkel:

* [RFC-kérelmek](#xml-samples-for-rfc-requests)

* [BAPI kérelmek](#xml-samples-for-bapi-requests)

* [IDoc kérelmek](#xml-samples-for-idoc-requests)

* Egyszerű vagy összetett XML-séma adattípusai

* Tábla paramétereinek

* Nem kötelező XML-viselkedés

Az XML-sémát egy opcionális XML-Prolog is elindíthatja. Az SAP-összekötő az XML-Prolog vagy anélkül is működik.

```xml

<?xml version="1.0" encoding="utf-8">

```

#### <a name="xml-samples-for-rfc-requests"></a>XML-minták RFC-kérelmekhez

A következő példa egy alapszintű RFC-hívást mutat be. Az RFC neve: `STFC_CONNECTION` . Ez a kérelem az alapértelmezett névteret használja `xmlns=` , azonban névtér-aliasokat is hozzárendelhet és használhat, például: `xmmlns:exampleAlias=` . A névtér értéke a Microsoft-szolgáltatások SAP-ban található összes RFC-dokumentum névtere. A kérelemben van egy egyszerű bemeneti paraméter `<REQUTEXT>` .

```xml

<STFC_CONNECTION xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
  <REQUTEXT>exampleInput</REQUTEXT>
</STFC_CONNECTION>

```

Az alábbi példa egy Table paraméterrel rendelkező RFC-hívást mutat be. Ez a példás hívás és a tesztelési RFC-k csoportja az összes SAP-rendszer részeként elérhető. A Table paraméter neve: `TCPICDAT` . A tábla vonaltípusa a `ABAPTEXT` (z), és ez az elem ismétlődik a tábla minden egyes sorában. Ez a példa egyetlen sort tartalmaz, amelynek a neve `LINE` . A Table paraméterrel rendelkező kérelmek tetszőleges számú mezőt tartalmazhatnak, ahol a szám pozitív egész szám (*n*). 

```xml

<STFC_WRITE_TO_TCPIC xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
  <RESTART_QNAME>exampleQName</RESTART_QNAME>
    <TCPICDAT>
      <ABAPTEXT xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
        <LINE>exampleFieldInput1</LINE>
      <ABAPTEXT xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
        <LINE>exampleFieldInput2</LINE>
      <ABAPTEXT xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
        <LINE>exampleFieldInput3</LINE>
      </ABAPTEXT>
    </TCPICDAT>
</STFC_WRITE_TO_TCPIC>

```

A következő példa egy olyan RFC-hívás, amely egy olyan Table paramétert tartalmaz, amelynek van egy névtelen mezője. Névtelen mező, ha a mezőhöz nincs hozzárendelve név. Az összetett típusok egy különálló névtérben vannak deklarálva, amelyben a deklaráció új alapértelmezett értéket állít be az aktuális csomóponthoz és annak összes alárendelt eleméhez. A példa a hexadecimális kódot használja `x002F` Escape-karakterként a szimbólumhoz */* , mert ez a szimbólum az SAP-mező nevében van lefoglalva.

```xml

<RFC_XML_TEST_1 xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
  <IM_XML_TABLE>
    <RFC_XMLCNT xmlns="http://Microsoft.LobServices.Sap/2007/03/Rfc/">
      <_x002F_AnonymousField>exampleFieldInput</_x002F_AnonymousField>
    </RFC_XMLCNT>
  </IM_XML_TABLE>
</RFC_XML_TEST_1>

```

A következő példa a névterek előtagjait tartalmazza. Az összes előtagokat egyszerre deklarálhatja, vagy tetszőleges számú előtagot deklarálhat egy csomópont attribútumaiként. Az RFC-névtér aliasa az `ns0` alapszintű típushoz tartozó gyökérként és paraméterekként használható.

> [!NOTE]
> az összetett típusok egy másik névtérben vannak deklarálva az RFC-típusoknál az aliassal, a `ns3` normál RFC-névtér helyett az aliassal `ns0` .

```xml

<ns0:BBP_RFC_READ_TABLE xmlns:ns0="http://Microsoft.LobServices.Sap/2007/03/Rfc/" xmlns:ns3="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc/">
  <ns0:DELIMITER>0</ns0:DELIMITER>
  <ns0:QUERY_TABLE>KNA1</ns0:QUERY_TABLE>
  <ns0:ROWCOUNT>250</ns0:ROWCOUNT>
  <ns0:ROWSKIPS>0</ns0:ROWSKIPS>
  <ns0:FIELDS>
    <ns3:RFC_DB_FLD>
      <ns3:FIELDNAME>KUNNR</ns3:FIELDNAME>
    </ns3:RFC_DB_FLD>
  </ns0:FIELDS>
</ns0:BBP_RFC_READ_TABLE>

```

#### <a name="xml-samples-for-bapi-requests"></a>XML-minták BAPI-kérelmekhez

A következő XML-minták például [a BAPI metódus meghívására](#call-bapi-action)irányuló kérelmek.

> [!NOTE]
> Az SAP lehetővé teszi, hogy az üzleti objektumok elérhetők legyenek a külső rendszerek számára az RFC-re adott válaszként `RPY_BOR_TREE_INIT` , amely Logic apps a bemeneti szűrő nélküli problémákat. Logic Apps megvizsgálja a kimeneti táblát `BOR_TREE` . A `SHORT_TEXT` mező az üzleti objektumok neveire szolgál. Az SAP által a kimeneti táblában nem visszaadott üzleti objektumok nem érhetők el Logic Apps.
> 
> Ha egyéni üzleti objektumokat használ, győződjön meg róla, hogy közzéteszi és felszabadítja ezeket az üzleti objektumokat az SAP-ben. Ellenkező esetben az SAP nem sorolja fel az egyéni üzleti objektumokat a kimeneti táblában `BOR_TREE` . Nem férhet hozzá Logic Apps egyéni üzleti objektumaihoz, amíg nem teszi elérhetővé az üzleti objektumokat az SAP-ból. 

A következő példa a BAPI metódust használó bankok listáját kéri le `GETLIST` . Ez a minta egy bank üzleti objektumát tartalmazza `BUS1011` . 

```xml

<GETLIST xmlns="http://Microsoft.LobServices.Sap/2007/03/Bapi/BUS1011">
  <BANK_CTRY>US</BANK_CTRY>
  <MAX_ROWS>10</MAX_ROWS>
</GETLIST>

```

A következő példa egy bank objektumot hoz létre a `CREATE` metódus használatával. Ez a példa ugyanazt az üzleti objektumot használja, mint az előző példában `BUS1011` . Ha a metódus használatával hoz `CREATE` létre egy bankot, ügyeljen arra, hogy véglegesítse a módosításokat, mert ez a módszer alapértelmezés szerint nem véglegesítve van.

> [!TIP]
> Győződjön meg arról, hogy az XML-dokumentum az SAP-rendszeren konfigurált összes érvényesítési szabályt követi. Ebben a példában például a bank kulcsának ( `<BANK_KEY>` ) Bank-útválasztási számnak, más néven ABA-számnak kell lennie az Egyesült Államokban.

```xml

<CREATE xmlns="http://Microsoft.LobServices.Sap/2007/03/Bapi/BUS1011">
  <BANK_ADDRESS>
    <BANK_NAME xmlns="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc">ExampleBankName</BANK_NAME>
    <REGION xmlns="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc">ExampleRegionName</REGION>
    <STREET xmlns="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc">ExampleStreetAddress</STREET>
    <CITY xmlns="http://Microsoft.LobServices.Sap/2007/03/Types/Rfc">Redmond</CITY>
  </BANK_ADDRESS>
  <BANK_COUNTRY>US</BANK_COUNTRY>
  <BANK_KEY>123456789</BANK_KEY>
</CREATE>

```

A következő példa beolvas egy bank adatait a bank útválasztási számával, a értékét `<BANK_KEY>` . 

```xml

<GETDETAIL xmlns="http://Microsoft.LobServices.Sap/2007/03/Bapi/BUS1011">
  <BANK_COUNTRY>US</BANK_COUNTRY>
  <BANK_KEY>123456789</BANK_KEY>
</GETDETAIL>

```

#### <a name="xml-samples-for-idoc-requests"></a>XML-minták IDoc-kérelmekhez

Egyszerű SAP IDoc XML-séma létrehozásához használja az **SAP-bejelentkezési** alkalmazást és a T-kódot `WE-60` . Az SAP-dokumentációt a grafikus felhasználói felületen keresztül érheti el, és XSD formátumban hozhatja meg az XML-sémákat a IDoc típusaihoz és bővítményeihez. Az általános SAP-formátumok és-adattartalomok, valamint a beépített párbeszédpanelek magyarázatát az [SAP dokumentációjában](https://help.sap.com/viewer/index)találja.

Ez a példa deklarálja a legfelső szintű csomópontot és a névtereket. A mintakód URI-ja a `http://Microsoft.LobServices.Sap/2007/03/Idoc/3/ORDERS05//700/Send` következő konfigurációt deklarálja:

* `/IDoc` a legfelső szintű Megjegyzés az összes IDocs
* `/3` a rekordtípusok verziója a gyakori szegmensek definícióinak
* `/ORDERS05` a IDoc típusa
* `//` üres szegmens, mert nincs IDoc-bővítmény
* `/700` az SAP verziója
* `/Send` az adatok SAP-be való küldésének művelete

```xml

<ns0:Send xmlns:ns0="http://Microsoft.LobServices.Sap/2007/03/Idoc/3/ORDERS05//700/Send" xmlns:ns3="http://schemas.microsoft.com/2003/10/Serialization" xmlns:ns1="http://Microsoft.LobServices.Sap/2007/03/Types/Idoc/Common/" xmlns:ns2="http://Microsoft.LobServices.Sap/2007/03/Idoc/3/ORDERS05//700">
  <ns0:idocData>

```

Megismételheti a `idocData` csomópontot, hogy egyetlen hívásban küldjön egy köteget a IDocs. Az alábbi példában egyetlen vezérlő rekord, `EDI_DC40` és több adatrekord található.

```xml

<...>
  <ns0:idocData>
    <ns2:EDI_DC40>
      <ns1:TABNAM>EDI_DC40</ns1:TABNAM>
<...>
      <ns1:ARCKEY>Cor1908207-5</ns1:ARCKEY>
    </ns2:EDI_DC40>
    <ns2:E2EDK01005>
      <ns2:DATAHEADERCOLUMN_SEGNAM>E23DK01005</ns2:DATAHEADERCOLUMN_SEGNAM>
      <ns2:CURCY>USD</ns2:CURCY>
    </ns2:E2EDK01005>
    <ns2:E2EDK03>
<...>
  </ns0:idocData>

```

A következő példa egy minta IDoc-vezérlő rekord, amely az előtagot használja `EDI_DC` . Frissítenie kell az értékeket az SAP-telepítés és a IDoc típusának megfelelően. Előfordulhat például, hogy a IDoc-ügyfél kódja nem `800` . Forduljon az SAP-csapathoz, és győződjön meg róla, hogy a megfelelő értékeket használja az SAP-telepítéshez.

```xml

<ns2:EDI_DC40>
  <ns:TABNAM>EDI_DC40</ns1:TABNAM>
  <ns:MANDT>800</ns1:MANDT>
  <ns:DIRECT>2</ns1:DIRECT>
  <ns:IDOCTYP>ORDERS05</ns1:IDOCTYP>
  <ns:CIMTYP></ns1:CIMTYP>
  <ns:MESTYP>ORDERS</ns1:MESTYP>
  <ns:STD>X</ns1:STD>
  <ns:STDVRS>004010</ns1:STDVRS>
  <ns:STDMES></ns1:STDMES>
  <ns:SNDPOR>SAPENI</ns1:SNDPOR>
  <ns:SNDPRT>LS</ns1:SNDPRT>
  <ns:SNDPFC>AG</ns1:SNDPFC>
  <ns:SNDPRN>ABAP1PXP1</ns1:SNDPRN>
  <ns:SNDLAD></ns1:SNDLAD>
  <ns:RCVPOR>BTSFILE</ns1:RCVPOR>
  <ns:RCVPRT>LI</ns1:RCVPRT>

```

Az alábbi példa egy egyszerű szegmenseket tartalmazó minta adatrekord. Ez a példa az SAP-dátumformátum használatát használja. Az erős típusú dokumentumok natív XML-formátumú formátumot használhatnak, például: `2020-12-31 23:59:59` .

```xml

<ns2:E2EDK01005>
  <ns2:DATAHEADERCOLUMN_SEGNAM>E2EDK01005</ns2:DATAHEADERCOLUMN_SEGNAM>
    <ns2:CURCY>USD</ns2:CURCY>
    <ns2:BSART>OR</ns2:BSART>
    <ns2:BELNR>1908207-5</ns2:BELNR>
    <ns2:ABLAD>CC</ns2:ABLAD>
  </ns2>
  <ns2:E2EDK03>
    <ns2:DATAHEADERCOLUMN_SEGNAM>E2EDK03</ns2:DATAHEADERCOLUMN_SEGNAM>
      <ns2:IDDAT>002</ns2:IDDAT>
      <ns2:DATUM>20160611</ns2:DATUM>
  </ns2:E2EDK03>

```

A következő példa egy csoportosított szegmenseket tartalmazó adatrekord. A rekord tartalmaz egy csoport szülő csomópontot, `E2EDKT1002GRP` és több alárendelt csomópontot is, beleértve a és a-t is `E2EDKT1002` `E2EDKT2001` . 

```xml

<ns2:E2EDKT1002GRP>
  <ns2:E2EDKT1002>
    <ns2:DATAHEADERCOLUMN_SEGNAM>E2EDKT1002</ns2:DATAHEADERCOLUMN_SEGNAM>
      <NS2:TDID>ZONE</ns2:TDID>
  </ns2:E2EDKT1002>
  <ns2:E2EDKT2001>
    <ns2:DATAHEADERCOLUMN_SEGNAM>E2EDKT2001</ns2:DATAHEADERCOLUMN_SEGNAM>
      <ns2:TDLINE>CRSD</ns2:TDLINE>
  </ns2:E2EDKT2001>
</ns2:E2EDKT1002GRP>

```

Az ajánlott módszer egy IDoc-azonosító létrehozása a tRFC való használathoz. Ezt a tranzakció-azonosítót beállíthatja az `tid` SAP-összekötő API [IDoc küldése műveletének](/connectors/sap/#send-idoc) használatával.

A következő példa egy alternatív módszer a tranzakció azonosítójának megadására, vagy `tid` . Ebben a példában az utolsó adatrekord szegmens csomópontja és a IDoc adatcsomópontja le van zárva. Ezt követően a GUID `guid` azonosítót használja a rendszer a duplikált elemek észlelésére szolgáló tRFC. 

```xml

    </E2STZUM002GRP>
  </idocData>
  <guid>8820ea40-5825-4b2f-ac3c-b83adc34321c</guid>
</Send>

```

### <a name="add-an-http-request-trigger"></a>HTTP-kérelem triggerének hozzáadása

1. A Azure Portal hozzon létre egy üres logikai alkalmazást, amely megnyitja a Logic Apps designert.

1. A keresőmezőbe írja be `http request` szűrőként a kifejezést. Az **Eseményindítók** listából válassza ki, **hogy mikor érkezik HTTP-kérelem**.

   ![HTTP-kérési trigger hozzáadása](./media/logic-apps-using-sap-connector/add-http-trigger-logic-app.png)

1. Most mentse a logikai alkalmazást, hogy létrehozzon egy végponti URL-címet a logikai alkalmazáshoz.
A tervező eszköztárán válassza a **Mentés** lehetőséget.

   A végpont URL-címe most megjelenik az triggerben, például:

   ![URL-cím előállítása a végponthoz](./media/logic-apps-using-sap-connector/generate-http-endpoint-url.png)

### <a name="add-an-sap-action-to-generate-schemas"></a>SAP-művelet hozzáadása sémák létrehozásához

1. A Logic Apps Designerben az trigger alatt válassza az **új lépés** lehetőséget.

   ![Új lépés hozzáadása a logikai alkalmazáshoz](./media/logic-apps-using-sap-connector/add-sap-action-logic-app.png)

1. A keresőmezőbe írja be `sap` szűrőként a kifejezést. A **műveletek** listából válassza a **sémák előállítása** lehetőséget.
  
   !["Sémák előállítása" művelet hozzáadása a logikai alkalmazáshoz](media/logic-apps-using-sap-connector/select-sap-schema-generator-action.png)

   Vagy válassza a **vállalat** lapot, és válassza ki az SAP-műveletet.

   ![SAP-küldési művelet kiválasztása a vállalati lapon](media/logic-apps-using-sap-connector/select-sap-schema-generator-ent-tab.png)

1. Ha a kapcsolat már létezik, folytassa a következő lépéssel, hogy beállítsa az SAP-műveletet. Ha azonban a rendszer megkéri a kapcsolat részleteinek megadását, adja meg az adatokat, hogy most létrehozza a kapcsolatát a helyszíni SAP-kiszolgálóval.

   1. Adja meg a kapcsolatok nevét.

   1. Az **adatátjáró** szakaszban az **előfizetés** területen válassza ki azt az Azure-előfizetést az adatátjáró-erőforráshoz, amelyet az adatátjáró-telepítés Azure Portalban hozott létre. 
   
   1. A **csatlakoztatási átjáró** területen válassza ki az adatátjáró-erőforrást az Azure-ban.

   1. Folytassa a kapcsolatok adatainak biztosítását. A **bejelentkezési típus** tulajdonságnál kövesse a lépést attól függően, hogy a tulajdonság az **Application Server** vagy a **Group** értékre van-e állítva:
   
      * Az **alkalmazáskiszolgáló** esetében ezek a tulajdonságok, amelyek általában opcionálisak, kötelezőek:

        ![SAP Application Server-kapcsolatok létrehozása](media/logic-apps-using-sap-connector/create-SAP-application-server-connection.png)

      * A **Group (csoport**) esetében ezek a tulajdonságok, amelyek általában opcionálisak, kötelezőek:

        ![SAP-üzenetkezelő kiszolgáló kapcsolatainak létrehozása](media/logic-apps-using-sap-connector/create-SAP-message-server-connection.png)  

      Alapértelmezés szerint az erős beírással ellenőrizhető az érvénytelen értékek ellenőrzése a séma XML-érvényesítésének végrehajtásával. Ez a viselkedés segítséget nyújt a korábbi problémák észlelésében. A **biztonságos gépelési** lehetőség visszamenőleges kompatibilitáshoz érhető el, és csak a karakterlánc hosszát ellenőrzi. További információ a [biztonságos gépelési lehetőségről](#safe-typing).

   1. Ha elkészült, válassza a **Létrehozás** lehetőséget.

      Logic Apps beállítja és teszteli a kapcsolódást, hogy a kapcsolódás megfelelően működjön.

1. Adja meg annak az összetevőnek az elérési útját, amelyhez el szeretné készíteni a sémát.

   Kiválaszthatja az SAP-műveletet a file pickerből:

   ![SAP-művelet kiválasztása](media/logic-apps-using-sap-connector/select-SAP-action-schema-generator.png)  

   Vagy manuálisan is megadhatja a műveletet:

   ![SAP-művelet manuális megadása](media/logic-apps-using-sap-connector/manual-enter-SAP-action-schema-generator.png)

   Több összetevőhöz tartozó sémák létrehozásához adja meg az egyes összetevőkre vonatkozó SAP-művelet részleteit, például:

   ![Válassza az új elem hozzáadása lehetőséget.](media/logic-apps-using-sap-connector/schema-generator-array-pick.png)

   ![Két elem megjelenítése](media/logic-apps-using-sap-connector/schema-generator-example.png)

   Az SAP-művelettel kapcsolatos további információkért tekintse meg az [IDoc-műveletek üzenet-sémái](/biztalk/adapters-and-accelerators/adapter-sap/message-schemas-for-idoc-operations)című témakört.

1. Mentse a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés** lehetőséget.

### <a name="test-your-logic-app"></a>A logikai alkalmazás tesztelése

1. A tervező eszköztárán válassza a **Futtatás** elemet a logikai alkalmazás futtatásának elindításához.

1. Nyissa meg a futtatást, és keresse meg a **sémák előállítása** művelet kimeneteit.

   A kimenetek megjelenítik a létrehozott sémákat az üzenetek adott listájához.

### <a name="upload-schemas-to-an-integration-account"></a>Sémák feltöltése integrációs fiókba

A létrehozott sémákat (például blob, Storage vagy integrációs fiók) is letöltheti vagy tárolhatja a tárházban. Az integrációs fiókok minden más XML-művelettel rendelkeznek, ezért ez a példa azt mutatja be, hogyan tölthet fel sémákat egy integrációs fiókba ugyanahhoz a logikai alkalmazáshoz az Azure Resource Manager-összekötő használatával.

1. A Logic Apps Designerben az trigger alatt válassza az **új lépés** lehetőséget.

1. A keresőmezőbe írja be `Resource Manager` szűrőként a kifejezést. Válassza **az erőforrás létrehozása vagy frissítése** lehetőséget.

   ![Azure Resource Manager művelet kiválasztása](media/logic-apps-using-sap-connector/select-azure-resource-manager-action.png)

1. Adja meg a művelet részleteit, beleértve az Azure-előfizetést, az Azure-erőforráscsoportot és az integrációs fiókot. Ha SAP-tokeneket szeretne hozzáadni a mezőkhöz, kattintson a mezők mezőire, és válassza ki a megjelenő dinamikus tartalom listából.

   1. Nyissa meg az **új paraméter hozzáadása** listát, és válassza ki a **hely** és a **Tulajdonságok** mezőket.

   1. Adja meg az új mezők részleteit az ebben a példában látható módon.

      ![Adja meg Azure Resource Manager művelet részleteit](media/logic-apps-using-sap-connector/azure-resource-manager-action.png)

   Az SAP- **létrehozási sémák** művelet gyűjteményként hoz létre sémákat, így a tervező automatikusan hozzáadja az **egyes** hurkokat a művelethez. Az alábbi példa bemutatja, hogyan jelenik meg ez a művelet:

   ![Azure Resource Manager művelet az "for each" ciklussal](media/logic-apps-using-sap-connector/azure-resource-manager-action-foreach.png)

   > [!NOTE]
   > A sémák Base64 kódolású formátumot használnak. A sémák integrációs fiókba való feltöltéséhez a függvény használatával dekódolni kell őket `base64ToString()` . Az alábbi példa az elem kódját mutatja be `"properties"` :
   >
   > ```json
   > "properties": {
   >    "Content": "@base64ToString(items('For_each')?['Content'])",
   >    "ContentType": "application/xml",
   >    "SchemaType": "Xml"
   > }
   > ```

1. Mentse a logikai alkalmazást. A tervező eszköztárán válassza a **Mentés** lehetőséget.

### <a name="test-your-logic-app"></a>A logikai alkalmazás tesztelése

1. A tervező eszköztárán válassza a **Futtatás** lehetőséget a logikai alkalmazás manuális elindításához.

1. Sikeres Futtatás után lépjen az integrációs fiókra, és győződjön meg arról, hogy a létrehozott sémák léteznek.

## <a name="enable-secure-network-communications"></a>Biztonságos hálózati kommunikáció engedélyezése

Mielőtt elkezdené, győződjön meg arról, hogy teljesítette a korábban felsorolt [előfeltételeket](#prerequisites), amelyek csak akkor érvényesek, ha az adatátjárót és a logikai alkalmazásokat a több-bérlős Azure-ban futtatja:

* Győződjön meg arról, hogy a helyszíni adatátjáró olyan számítógépre van telepítve, amely ugyanabban a hálózatban van, mint az SAP-rendszer.

* Egyszeri bejelentkezés esetén az adatátjáró egy SAP-felhasználóhoz hozzárendelt felhasználóként fut.

* A további biztonsági funkciókat biztosító SNC-függvénytár ugyanarra a gépre van telepítve, mint az adatátjáró. Ilyenek például a [sapseculib](https://help.sap.com/saphelp_nw74/helpdata/en/7a/0755dc6ef84f76890a77ad6eb13b13/frameset.htm), a Kerberos és az NTLM.

   Ha engedélyezni szeretné a SNC-t az SAP-rendszerből vagy azokból érkező kérésekhez, jelölje be az SAP-kapcsolatban a **Snc használata** jelölőnégyzetet, és adja meg a következő tulajdonságokat:

   ![Az SAP SNC konfigurálása a kapcsolatban](media/logic-apps-using-sap-connector/configure-sapsnc.png)

   | Tulajdonság | Leírás |
   |----------| ------------|
   | **SNC-könyvtár elérési útja** | A SNC-könyvtár neve vagy elérési útja NKH-telepítési helyhez vagy abszolút elérési úthoz viszonyítva. Ilyenek például a következők: `sapsnc.dll` vagy `.\security\sapsnc.dll` `c:\security\sapsnc.dll` . |
   | **SNC SSO** | Ha a-t a SNC-n keresztül kapcsolódik, a rendszer általában a-hívó hitelesítésére használja a SNC-identitást. Egy másik lehetőség, hogy felülbírálja a felhasználó és a jelszó információit a hívó hitelesítéséhez, de a sor továbbra is titkosítva van. |
   | **SNC nevem** | A legtöbb esetben ez a tulajdonság nem hagyható el. A telepített SNC-megoldás általában ismeri a saját SNC-nevét. Csak a több identitást támogató megoldások esetében lehet, hogy meg kell adnia az adott célhelyhez vagy kiszolgálóhoz használni kívánt identitást. |
   | **SNC-partner neve** | A háttér-végpont neve. |
   | **SNC-védelem minősége** | Az adott cél vagy kiszolgáló SNC-kommunikációjához használt szolgáltatás minősége. Az alapértelmezett értéket a háttérrendszer határozza meg. A maximális értéket a SNC-hez használt biztonsági termék határozza meg. |
   |||

   > [!NOTE]
   > Ne állítsa be a környezeti változókat SNC_LIB és SNC_LIB_64 azon a gépen, amelyen az adatátjáró és a SNC-könyvtár található. Ha be van állítva, elsőbbséget élveznek az összekötőn átadott SNC-függvénytár értékével szemben.

## <a name="safe-typing"></a>Biztonságos gépelés

Alapértelmezés szerint az SAP-kapcsolatok létrehozásakor a rendszer erős beírással ellenőrzi az érvénytelen értékeket a séma XML-érvényesítésének végrehajtásával. Ez a viselkedés segítséget nyújt a korábbi problémák észlelésében. A **biztonságos gépelési** lehetőség visszamenőleges kompatibilitáshoz érhető el, és csak a karakterlánc hosszát ellenőrzi. Ha a **biztonságos gépelés** lehetőséget választja, a dats típusa és a Tims típus az SAP-ban karakterláncként lesz kezelve, nem pedig az XML-megfelelő, `xs:date` és `xs:time` ahol `xmlns:xs="http://www.w3.org/2001/XMLSchema"` . A biztonságos gépelés hatással van a séma összes generációjának viselkedésére, az "elküldött" adattartalomra és a "kapott" válaszra, valamint az aktiválásra vonatkozó üzenet küldésére. 

Ha erős gépelés van használatban (a **biztonságos gépelés** nincs engedélyezve), a séma a dats és a Tims típusait egyszerűbb XML-típusokra képezi le:

```xml
<xs:element minOccurs="0" maxOccurs="1" name="UPDDAT" nillable="true" type="xs:date"/>
<xs:element minOccurs="0" maxOccurs="1" name="UPDTIM" nillable="true" type="xs:time"/>
```

Ha erős gépeléssel küld üzeneteket, a DATS és a TIMS válasza megfelel a megfelelő XML-típus formátumának:

```xml
<DATE>9999-12-31</DATE>
<TIME>23:59:59</TIME>
```

Ha a **biztonságos gépelés** engedélyezve van, a séma leképezi a dats és a Tims típusú XML-karakterlánc-mezőket csak hosszúsági korlátozásokkal, például:

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

Ha az üzenetek **biztonságos gépeléssel** való küldése engedélyezve van, a dats és a Tims válasz a következő példához hasonlít:

```xml
<DATE>99991231</DATE>
<TIME>235959</TIME>
```

## <a name="advanced-scenarios"></a>Speciális forgatókönyvek

### <a name="change-language-headers"></a>Nyelvi fejlécek módosítása

Ha Logic Apps-ból kapcsolódik az SAP-hoz, a kapcsolat alapértelmezett nyelve az angol. Megadhatja a kapcsolatok nyelvét [a szabványos http- `Accept-Language` fejléc](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.4) és a bejövő kérések használatával.

> [!TIP]
> A legtöbb webböngésző egy `Accept-Language` fejlécet ad hozzá a felhasználó beállításai alapján. A webböngésző akkor alkalmazza ezt a fejlécet, amikor új SAP-kapcsolat jön létre a Logic Apps Designerben. Ha nem szeretne SAP-kapcsolatokat létrehozni a webböngésző előnyben részesített nyelvén, frissítse a webböngésző beállításait az előnyben részesített nyelv használatára, vagy hozza létre az SAP-kapcsolatot Azure Resource Manager használatával a Logic Apps Designer helyett. 

Küldhet például egy kérelmet a `Accept-Language` logikai alkalmazás fejlécével a **http-kérelem** trigger használatával. A logikai alkalmazás összes művelete megkapja a fejlécet. Ezt követően az SAP a megadott nyelveket használja a rendszerüzenetekben, például a BAPI hibaüzeneteket.

A logikai alkalmazások SAP-kapcsolatainak paraméterei nem rendelkeznek nyelvi tulajdonsággal. Ha tehát a `Accept-Language` fejlécet használja, a következő hibaüzenet jelenhet meg: **ellenőrizze a fiók adatait és/vagy engedélyeit, és próbálkozzon újra.** Ebben az esetben az SAP-összetevő naplófájljait kell megnéznie. A hiba valójában a fejlécet használó SAP-összetevőben történik, így előfordulhat, hogy a következő hibaüzenetek valamelyikét kapja:

* `"SAP.Middleware.Connector.RfcLogonException: Select one of the installed languages"`
* `"SAP.Middleware.Connector.RfcAbapMessageException: Select one of the installed languages"`

### <a name="confirm-transaction-explicitly"></a>Tranzakció explicit megerősítése

Ha Logic Appsről küld tranzakciókat az SAP-nak, ez az Exchange két lépésben történik az SAP-dokumentumban, a [tranzakciós RFC-kiszolgáló programjaiban](https://help.sap.com/doc/saphelp_nwpi71/7.1/22/042ad7488911d189490000e829fbbd/content.htm?no_cache=true)leírtak szerint. Alapértelmezés szerint a **Küldés az SAP-be** művelet a függvények átvitelének lépéseit és a tranzakciós megerősítést egyetlen hívásban kezeli. Az SAP-összekötő lehetővé teszi, hogy leválasztsa ezeket a lépéseket. IDoc küldhet, és nem lehet automatikusan megerősíteni a tranzakciót, a explicit tranzakció- **azonosító megerősítése** műveletet használhatja.

Ez a tranzakció-azonosító megerősítése leválasztásának lehetősége akkor hasznos, ha nem szeretne ismétlődő tranzakciókat használni az SAP-ban, például olyan helyzetekben, ahol a hibák oka lehet például a hálózati problémák miatt. A tranzakció-azonosító külön megerősítésével a tranzakció csak egyszer lesz végrehajtva az SAP-rendszeren.

Az alábbi példa a következő mintát mutatja be:

1. Hozzon létre egy üres logikai alkalmazást, és adjon hozzá egy HTTP-triggert.

1. Az SAP-összekötőben adja hozzá a **Send IDOC** műveletet. Adja meg az SAP-rendszernek küldött IDoc adatait.

1. Ha explicit módon szeretné megerősíteni a tranzakció AZONOSÍTÓját egy külön lépésben, a **TID megerősítése** mezőben válassza a **nem** lehetőséget. A nem kötelező **tranzakció-azonosító GUID-azonosítója** mezőben manuálisan is megadhatja az értéket, vagy az összekötő automatikusan létrehozhatja és visszaküldheti ezt a GUID azonosítót a Send IDOC művelet válaszában.

   ![IDOC művelet tulajdonságainak küldése](./media/logic-apps-using-sap-connector/send-idoc-action-details.png)

1. A tranzakció AZONOSÍTÓjának explicit megerősítéséhez adja hozzá a **tranzakció-azonosító megerősítése** műveletet, ügyelve arra, hogy [ne küldjön ismétlődő IDocs az SAP-](#avoid-sending-duplicate-idocs)nek. Kattintson a **tranzakció-azonosító** mezőbe, hogy megjelenjen a dinamikus tartalmak listája. Ebből a listából válassza ki a **tranzakció-azonosító** értékét, amelyet a **Send IDOC** művelet adott vissza.

   ![Tranzakció-azonosító művelet megerősítése](./media/logic-apps-using-sap-connector/explicit-transaction-id.png)

   A lépés futtatása után az aktuális tranzakció mindkét végén meg van jelölve, az SAP-összekötő oldalon és az SAP-rendszer oldalon.

#### <a name="avoid-sending-duplicate-idocs"></a>Ne küldjön ismétlődő IDocs

Ha a logikai alkalmazásból duplikált IDocs küldésével kapcsolatos problémát tapasztal, kövesse az alábbi lépéseket egy olyan karakterlánc-változó létrehozásához, amely IDoc-tranzakciós azonosítóként szolgál. A tranzakció-azonosító létrehozása segít megelőzni az ismétlődő hálózati átviteleket, ha olyan problémák merülnek fel, mint például az ideiglenes kimaradások, a hálózati problémák vagy az elveszett nyugták.

> [!NOTE]
> Az SAP-rendszerek egy megadott idő elteltével vagy 24 órán belül elfelejtették a tranzakció azonosítóját. Ennek eredményeképpen az SAP soha nem tudja megerősíteni a tranzakció azonosítóját, ha az azonosító vagy a GUID ismeretlen.
> Ha a tranzakció-azonosító megerősítése sikertelen, akkor ez a hiba azt jelzi, hogy az SAP-rendszerrel való communcation sikertelen volt, mielőtt az SAP elismerte a megerősítést.

1. A Logic Apps Designerben adja hozzá a művelet **inicializálása változót** a logikai alkalmazáshoz. 

1. A művelet **inicializálása változó** szerkesztőjében adja meg a következő beállításokat. Ezután mentse a módosításokat.

    1. A **név** mezőben adja meg a változó nevét. Például: `IDOCtransferID`.

    1. A **Típus mezőben** válassza a **karakterlánc** lehetőséget a változó típusaként.

    1. Az **érték** mezőben jelölje be a **kezdeti érték megadására** szolgáló szövegmezőt a dinamikus tartalom menü megnyitásához. Válassza a **kifejezések** lapot. A függvények listájában adja meg a függvényt `guid()` . Ezután kattintson **az OK** gombra a módosítások mentéséhez. Az **érték** mező most a `guid()` függvényre van beállítva, amely létrehoz egy GUID azonosítót.

1. A **változó inicializálása** művelet után adja hozzá a **IDOC küldése** műveletet.

1. A **IDOC küldő** művelet szerkesztőjében adja meg a következő beállításokat. Ezután mentse a módosításokat.

    1. A **IDOC típusnál** válassza ki az üzenet típusát, és a **bemeneti IDOC üzenetnél** adja meg az üzenetet.

    1. Az **SAP**-verziók esetében válassza ki az SAP-konfiguráció értékeit.

    1. A **rekordtípusok verziójának** kiválasztásához válassza ki az SAP-konfiguráció értékeit.

    1. A **TID megerősítése** beállításnál válassza a **nem** lehetőséget.

    1. Válassza az **új paraméter hozzáadása**  >  **tranzakció-azonosító GUID** elemet. A dinamikus tartalom menü megnyitásához kattintson a szövegmezőre. A **változók** lapon válassza ki a létrehozott változó nevét. Például: `IDOCtransferID`.

1. A **IDOC küldése** művelet címsorán válassza a **...**  >  lehetőséget. **Beállítások**. Az **újrapróbálkozási szabályzat** esetében ajánlott kiválasztani az **alapértelmezett** &gt; **kész** elemet. Ehelyett azonban egyéni szabályzatot is beállíthat az adott igényekhez. Egyéni házirendek esetében ajánlott legalább egy újrapróbálkozást beállítani az ideiglenes hálózati kimaradások leállásához.

1. Miután a művelet **elküldte a IDOC**, adja hozzá a **tranzakció-azonosító megerősítése** műveletet.

1. A **tranzakció-azonosító megerősítése** művelet szerkesztőjében adja meg a következő beállításokat. Ezután mentse a módosításokat.

    1. A **tranzakció-azonosító** mezőben adja meg újra a változó nevét. Például: `IDOCtransferID`.

1. Szükség esetén ellenőrizheti a deduplikálás tesztelési környezetét. Ismételje meg a **Send IDOC** műveletet ugyanazzal a **tranzakciónapló** -GUID azonosítóval, amelyet az előző lépésben használt. Ha ugyanezt a IDoc kétszer küldi el, ellenőrizheti, hogy az SAP képes-e azonosítani a tRFC-hívás ismétlődését, és a két hívást egyetlen bejövő IDoc-üzenetre oldja fel.

## <a name="known-issues-and-limitations"></a>Ismert problémák és korlátozások

A felügyelt (nem ISE) SAP-összekötő jelenleg ismert problémái és korlátozásai:

* Az SAP-trigger nem támogatja az adatátjáró-fürtöket. Bizonyos feladatátvételi esetekben az SAP-rendszerrel kommunikáló adatátjáró-csomópont eltérhet az aktív csomóponttól, ami váratlan viselkedést eredményez. A küldési forgatókönyvek esetében az adatátjáró-fürtök támogatottak.

* Az SAP-összekötő jelenleg nem támogatja az SAP útválasztó karakterláncait. A helyszíni adatátjárónak ugyanazon a helyi hálózaton kell lennie, mint a csatlakozni kívánó SAP-rendszernek.

## <a name="connector-reference"></a>Összekötő-referencia

Az összekötő részletes technikai részleteiről, például az eseményindítók, a műveletek és a korlátok az összekötő hencegő fájljában leírtak alapján: az [összekötő hivatkozási lapja](/connectors/sap/). A Logic Apps további dokumentációja a következő műveletekhez biztosítható:

* [BAPI hívása](#call-bapi-action)

* [IDOC küldése](#send-idoc-action)

> [!NOTE]
> Az [integrációs szolgáltatási környezet (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)logikai alkalmazásai esetében az összekötő ISE által címkézett verziója az [ISE-üzenetek korlátait](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) használja helyette.

### <a name="call-bapi-action"></a>BAPI művelet hívása

A [Call BAPI ( `CallBapi` )](
https://docs.microsoft.com/connectors/sap/#call-bapi-(preview)) művelet meghívja a BAPI METÓDUSt az SAP-kiszolgálón. 

A következő paramétereket kell használnia a híváshoz: 

* **Üzleti objektum** ( `businessObject` ), amely egy kereshető legördülő menü.

* A **metódus** ( `method` ), amely az **üzleti objektum** kiválasztása után kitölti az elérhető metódusokat. Az elérhető módszerek a kiválasztott **üzleti objektumtól** függően változnak.

* **Bemeneti BAPI paraméterek** ( `body` ), amelyben meghívja az XML-dokumentumot, amely az BAPI metódus Bemeneti paraméterének értékeit tartalmazza a híváshoz, vagy a BAPI paramétereit tartalmazó Storage-blob URI-ját.

A Call BAPI művelet használatával kapcsolatos részletes példákért tekintse meg a [BAPI-kérelmek XML-mintáit](#xml-samples-for-bapi-requests).

> [!TIP]
> Ha a Logic Apps Designer használatával szerkeszti a BAPI-kérelmet, a következő keresési funkciókat használhatja: 
> 
> * Válasszon ki egy objektumot a tervezőben az elérhető módszerek legördülő menüjének megjelenítéséhez.
> * Az üzleti objektumok típusait a kulcsszó alapján szűrheti a BAPI API-hívás által megadott kereshető listával.

### <a name="send-idoc-action"></a>IDoc művelet küldése

A [Send IDoc ( `SendIDoc` )](https://docs.microsoft.com/connectors/sap/#send-idoc-(preview)) művelet elküldi a IDOC üzenetet az SAP-kiszolgálónak.

A következő paramétereket kell használnia a híváshoz: 

* A **IDOC típusa opcionális kiterjesztéssel** ( `idocType` ), amely egy kereshető legördülő menü.

    * A választható **SAP kiadási verzió** ( `releaseVersion` ) a IDoc típus kiválasztása után tölti fel az értékeket, és a kiválasztott IDoc függ.

* **Bemeneti IDOC üzenet** ( `body` ), amelyben meghívja a IDOC hasznos adatokat tartalmazó XML-dokumentumot vagy a IDOC XML-dokumentumát tartalmazó tárolási blob URI-ját. Ennek a dokumentumnak meg kell felelnie az SAP IDOC XML-sémának a WE60 IDoc dokumentációjának, vagy a megfelelő SAP IDoc műveleti URI-hoz létrehozott sémának.

A IDoc küldése művelet használatával kapcsolatos részletes példákért tekintse meg a [IDoc üzenetek SAP-kiszolgálóra történő küldésének](#send-idoc-messages-to-sap-server)bemutatóját.

Ha nem szeretné, hogy a választható paraméterek ne legyenek **megerősítve a TID** ( `confirmTid` ), tekintse meg a bemutatót, [amely explicit módon megerősíti a tranzakciót](#confirm-transaction-explicitly).

## <a name="next-steps"></a>Következő lépések

* [Kapcsolódjon a helyi rendszerekhez](../logic-apps/logic-apps-gateway-connection.md) Azure Logic Appsról.

* Megtudhatja, hogyan érvényesítheti, átalakíthatja és használhatja más üzenetkezelési műveleteket a [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md).

* További [Logic apps összekötők](../connectors/apis-list.md)megismerése.
