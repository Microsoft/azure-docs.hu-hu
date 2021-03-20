---
title: RosettaNet üzenetek a B2B-integrációhoz
description: Exchange-RosettaNet üzenetek Azure Logic Appsban Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 06/22/2019
ms.openlocfilehash: f02cbdc7ca8822c5fcc91b106856d7f8f547536b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91565105"
---
# <a name="exchange-rosettanet-messages-for-b2b-enterprise-integration-in-azure-logic-apps"></a>Exchange RosettaNet-üzenetek a B2B vállalati integrációhoz Azure Logic Apps

A [RosettaNet](https://resources.gs1us.org) egy non-profit konzorcium, amely az üzleti információk megosztására szolgáló szabványos folyamatokat alakított ki. Ezeket a szabványokat általában az ellátási lánc folyamataihoz használják, és széleskörűek a félvezető-, elektronikai és logisztikai iparágakban. A RosettaNet konzorcium létrehozza és karbantartja a partneri felületi folyamatokat (maggal), amelyek közös üzleti folyamat-definíciókat biztosítanak az összes RosettaNet-üzenet cseréjéhez. A RosettaNet az XML protokollon alapul, és meghatározza az üzenetek irányelveit, az üzleti folyamatokhoz kapcsolódó interfészeket, valamint a vállalatok közötti kommunikáció megvalósítási keretrendszereit.

[Azure Logic apps](../logic-apps/logic-apps-overview.md)a RosettaNet-összekötő segítségével olyan integrációs megoldásokat hozhat létre, amelyek támogatják a RosettaNet-szabványokat. Az összekötő a RosettaNet implementációs keretrendszer (RNIF) 2.0.01-verzióján alapul. A RNIF egy nyílt hálózati alkalmazás-keretrendszer, amely lehetővé teszi az üzleti partnerek számára, hogy a RosettaNet-pip-ket futtassák. Ez a keretrendszer határozza meg az üzenet szerkezetét, a nyugták, a Multipurpose Internet Mail Extensions (MIME) kódolás és a digitális aláírás szükségességét.

Pontosabban, az összekötő a következő funkciókat biztosítja:

* RosettaNet-üzenetek kódolása vagy fogadása.
* RosettaNet üzenetek dekódolása vagy elküldése.
* Várja meg, amíg a válasz és a sikertelen értesítés generálása megtörtént.

Ezekhez a funkciókhoz az összekötő a RNIF 2.0.01 által meghatározott összes pip-et támogatja. A partnerrel folytatott kommunikáció szinkron vagy aszinkron lehet.

## <a name="rosettanet-concepts"></a>RosettaNet fogalmak

Íme néhány olyan fogalom és kifejezés, amely egyedi a RosettaNet-specifikációban, és fontos a RosettaNet-alapú integrációk létrehozásakor:

* **PIP**

  A RosettaNet-szervezet létrehozza és karbantartja a partneri felületi folyamatokat (maggal), amelyek közös üzleti folyamat-definíciókat biztosítanak az összes RosettaNet-üzenet cseréjéhez. Mindegyik PIP-specifikáció egy Document Type Definition (DTD) fájlt és egy üzenet-útmutató dokumentumot tartalmaz. A DTD-fájl határozza meg a szolgáltatás tartalmi üzenetének szerkezetét. Az üzenet – útmutató dokumentum, amely egy emberi olvasásra alkalmas HTML-fájl, az elem szintű korlátozásokat határozza meg. Ezekkel a fájlokkal együtt az üzleti folyamat teljes definícióját is megadhatja.

   A pip-ket egy magas szintű üzleti funkció, egy fürt, egy alfüggvény vagy egy szegmens kategorizálja. Például a "3A4" a beszerzési sorrend PIP-je, míg a "3" a megrendelés felügyeleti funkciója, a "3A" pedig az idézőjel & Order Entry alfüggvény. További információ: [RosettaNet-webhely](https://resources.gs1us.org).

* **Művelet**

  A PIP részét képező műveleti üzenetek olyan üzleti üzenetek, amelyek a partnerek között váltanak át.

* **Jel**

   A PIP részét képező üzenet a műveleti üzenetekre adott válaszként küldött nyugták.

* **Egyszeri művelet és dupla művelet**

  Egyetlen művelettel kapcsolatos PIP esetén az egyetlen válasz a nyugtázás jelzésére szolgáló üzenet. A kétlépéses PIP esetén a kezdeményező válaszüzenetet kap, és az egyszeres művelettel kapcsolatos üzeneteken kívül visszaigazolja a válaszokat.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha még nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* [Integrációs fiók](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) a szerződés és más B2B-összetevők tárolásához. Ezt az integrációs fiókot hozzá kell rendelni az Azure-előfizetéséhez.

* Legalább két, az integrációs fiókban definiált [partner](../logic-apps/logic-apps-enterprise-integration-partners.md) , amely a "Duns" minősítéssel van konfigurálva az **üzleti identitások** alatt

* Egy PIP-folyamat konfigurációja, amely a RosettaNet üzenetek küldéséhez vagy fogadásához szükséges az integrációs fiókban. A folyamat konfigurációja a PIP összes konfigurációs jellemzőjét tárolja. Ezután hivatkozhat erre a konfigurációra, ha szerződést hoz létre a partnerrel. A PIP folyamat konfigurációjának az integrációs fiókban való létrehozásával kapcsolatban lásd: a [pip-folyamat konfigurációjának hozzáadása](#add-pip).

* Nem kötelező [tanúsítványok](../logic-apps/logic-apps-enterprise-integration-certificates.md) az integrációs fiókba feltöltött üzenetek titkosításához, visszafejtéséhez vagy aláírásához. A tanúsítványok csak akkor szükségesek, ha aláírást vagy titkosítást használ.

<a name="add-pip"></a>

## <a name="add-pip-process-configuration"></a>PIP-folyamat konfigurációjának hozzáadása

A PIP-folyamat konfigurációjának az integrációs fiókhoz való hozzáadásához kövesse az alábbi lépéseket:

1. A [Azure Portal](https://portal.azure.com)keresse meg és nyissa meg az integrációs fiókját.

1. Az **Áttekintés** panelen válassza a **RosettaNet pip** csempét.

   ![RosettaNet csempe kiválasztása](media/logic-apps-enterprise-integration-rosettanet/select-rosettanet-tile.png)

1. A **ROSETTANET pip** területen válassza a **Hozzáadás** lehetőséget. Adja meg a PIP részleteit.

   ![RosettaNet PIP részleteinek hozzáadása](media/logic-apps-enterprise-integration-rosettanet/add-rosettanet-pip.png)

   | Tulajdonság | Kötelező | Leírás |
   |----------|----------|-------------|
   | **Név** | Yes | A PIP neve |
   | **PIP-kód** | Yes | A PIP háromjegyű kód. További információ: [RosettaNet maggal](/biztalk/adapters-and-accelerators/accelerator-rosettanet/rosettanet-pips). |
   | **PIP verziója** | Yes | A PIP verziószáma, amely a kiválasztott PIP-kód alapján érhető el |
   ||||

   A PIP-tulajdonságokkal kapcsolatos további információkért látogasson el a [RosettaNet webhelyére](https://resources.gs1us.org/RosettaNet-Standards/Standards-Library/PIP-Directory#1043208-pipsreg).

1. Ha elkészült, kattintson az **OK gombra**, amely létrehozza a pip-konfigurációt.

1. A folyamat konfigurációjának megtekintéséhez vagy szerkesztéséhez válassza ki a PIP-et, és válassza a **Szerkesztés JSON-ként** lehetőséget.

   Az összes folyamat konfigurációs beállítása a PIP specifikációi alapján érhető el. Logic Apps a beállítások többségét a tulajdonságok leggyakrabban használt értékeivel tölti fel.

   ![RosettaNet PIP-konfiguráció szerkesztése](media/logic-apps-enterprise-integration-rosettanet/edit-rosettanet-pip.png)

1. Győződjön meg arról, hogy a beállítások megfelelnek a megfelelő PIP-specifikáció értékeinek, és megfeleljenek az üzleti igényeknek. Ha szükséges, frissítse a JSON-értékeket, és mentse a módosításokat.

## <a name="create-rosettanet-agreement"></a>RosettaNet-szerződés létrehozása

1. A [Azure Portalban](https://portal.azure.com)keresse meg és nyissa meg az integrációs fiókot, ha még nincs megnyitva.

1. Az **Áttekintés** panelen válassza a **szerződések** csempét.

   ![Szerződések kiválasztása csempe](media/logic-apps-enterprise-integration-rosettanet/select-agreement-tile.png)

1. A **szerződések** területen válassza a **Hozzáadás** lehetőséget. Adja meg a szerződés részleteit.

   ![Szerződés részleteinek hozzáadása](media/logic-apps-enterprise-integration-rosettanet/add-agreement-details.png)

   | Tulajdonság | Kötelező | Leírás |
   |----------|----------|-------------|
   | **Név** | Yes | A szerződés neve |
   | **Szerződés típusa** | Yes | Válassza a **RosettaNet** lehetőséget. |
   | **Gazda partner** | Yes | Egy szerződéshez a gazdagép és a vendég partner is szükséges. A fogadó partner a szerződést konfiguráló szervezetet jelöli. |
   | **Gazdagép identitása** | Yes | A gazdagép-partner azonosítója |
   | **Vendég partner** | Yes | Egy szerződéshez a gazdagép és a vendég partner is szükséges. A vendég partner a gazda partnerrel üzleti tevékenységet folytató szervezetet jelöli. |
   | **Vendég identitás** | Yes | A vendég partner azonosítója |
   | **Fogadási beállítások** | Változó | Ezek a tulajdonságok a gazda partner által fogadott összes üzenetre érvényesek. |
   | **Küldési beállítások** | Változó | Ezek a tulajdonságok a gazda partner által küldött összes üzenetre érvényesek. |  
   | **RosettaNet PIP-hivatkozások** | Yes | A szerződéshez tartozó PIP-hivatkozások. Minden RosettaNet-üzenethez PIP-konfigurációk szükségesek. |
   ||||

1. Ha úgy szeretné beállítani a szerződést, hogy fogadja a vendég partnertől érkező üzeneteket, válassza a **fogadási beállítások** lehetőséget.

   ![Fogadási beállítások](media/logic-apps-enterprise-integration-rosettanet/add-agreement-receive-details.png)

   1. A bejövő üzenetek aláírásának vagy titkosításának engedélyezéséhez az **üzenetek** területen válassza az **üzenet aláírását** vagy az **üzenet** titkosítását.

      | Tulajdonság | Kötelező | Leírás |
      |----------|----------|-------------|
      | **Az üzenetet alá kell írni** | No | A bejövő üzenetek aláírása a kiválasztott tanúsítvánnyal. |
      | **Tanúsítvány** | Igen, ha engedélyezve van az aláírás | Az aláíráshoz használandó tanúsítvány |
      | **Üzenetek titkosításának engedélyezése** | No | A bejövő üzenetek titkosítása a kiválasztott tanúsítvánnyal. |
      | **Tanúsítvány** | Igen, ha engedélyezve van a titkosítás | A titkosításhoz használandó tanúsítvány |
      ||||

   1. Az egyes beállítások területen válassza ki a megfelelő [tanúsítványt](./logic-apps-enterprise-integration-certificates.md), amelyet korábban az integrációs fiókjához adott hozzá az aláíráshoz vagy a titkosításhoz.

1. Ha úgy szeretné beállítani a szerződést, hogy üzeneteket küldjön a vendég partnernek, válassza a **Beállítások küldése** lehetőséget.

   ![Küldési beállítások](media/logic-apps-enterprise-integration-rosettanet/add-agreement-send-details.png)

   1. A kimenő üzenetek aláírásának vagy titkosításának engedélyezéséhez az **üzenetek** területen válassza az üzenetek **aláírásának engedélyezése** vagy az üzenetek **titkosításának engedélyezése** lehetőséget. Az egyes beállítások területen válassza ki a megfelelő algoritmust és [tanúsítványt](./logic-apps-enterprise-integration-certificates.md), amelyet korábban az integrációs fiókjához adott hozzá az aláíráshoz vagy a titkosításhoz.

      | Tulajdonság | Kötelező | Leírás |
      |----------|----------|-------------|
      | **Üzenetek aláírásának engedélyezése** | No | A kimenő üzenetek aláírása a kiválasztott aláírási algoritmussal és tanúsítvánnyal. |
      | **Aláírási algoritmus** | Igen, ha engedélyezve van az aláírás | A használni kívánt aláíró algoritmus a kiválasztott tanúsítvány alapján |
      | **Tanúsítvány** | Igen, ha engedélyezve van az aláírás | Az aláíráshoz használandó tanúsítvány |
      | **Üzenetek titkosításának engedélyezése** | No | A kimenő fájlok titkosítása a kiválasztott titkosítási algoritmus és tanúsítvány használatával. |
      | **Titkosítási algoritmus** | Igen, ha engedélyezve van a titkosítás | A használni kívánt titkosítási algoritmus a kiválasztott tanúsítvány alapján |
      | **Tanúsítvány** | Igen, ha engedélyezve van a titkosítás | A titkosításhoz használandó tanúsítvány |
      ||||

   1. A **végpontok** területen határozza meg a műveleti üzenetek és a nyugták küldéséhez használandó szükséges URL-címeket.

      | Tulajdonság | Kötelező | Leírás |
      |----------|----------|-------------|
      | **Művelet URL-címe** |  Yes | A műveleti üzenetek küldéséhez használt URL-cím. Az URL-cím kötelező mező a szinkron és aszinkron üzenetek esetében is. |
      | **Nyugtázás URL-címe** | Yes | A visszaigazolási üzenetek küldéséhez használandó URL-cím. Az URL-cím az aszinkron üzenetek kötelező mezője. |
      ||||

1. Ha meg szeretné határozni a RosettaNet PIP-referenciákkal rendelkező szerződést a partnerek számára, válassza a **ROSETTANET pip-referenciák** lehetőséget. A **pip neve** területen válassza ki a korábban létrehozott pip nevét.

   ![PIP-hivatkozások](media/logic-apps-enterprise-integration-rosettanet/add-agreement-pip-details.png)

   A kijelölés feltölti a fennmaradó tulajdonságokat, amelyek az integrációs fiókban beállított PIP-re épülnek. Szükség esetén módosíthatja a **pip szerepkört**.

   ![Kijelölt PIP](media/logic-apps-enterprise-integration-rosettanet/add-agreement-selected-pip.png)

A lépések elvégzése után készen áll a RosettaNet üzenetek küldésére és fogadására.

## <a name="rosettanet-templates"></a>RosettaNet-sablonok

A fejlesztés felgyorsításához és az integrációs minták támogatásához logikai alkalmazás-sablonokat használhat a RosettaNet-üzenetek dekódolásához és kódolásához. Logikai alkalmazás létrehozásakor kiválaszthatja a Logic app Designer sablon-gyűjteményét. Ezeket a sablonokat az [Azure Logic apps GitHub-tárházában](https://github.com/Azure/logicapps)is megtalálhatja.

![RosettaNet-sablonok](media/logic-apps-enterprise-integration-rosettanet/decode-encode-rosettanet-templates.png)

## <a name="receive-or-decode-rosettanet-messages"></a>RosettaNet üzenetek fogadása vagy dekódolása

1. [Hozzon létre egy üres logikai alkalmazást](quickstart-create-first-logic-app-workflow.md).

1. [Kapcsolja össze integrációs fiókját](logic-apps-enterprise-integration-create-integration-account.md#link-account) a logikai alkalmazással.

1. Ahhoz, hogy hozzá lehessen adni egy műveletet a RosettaNet-üzenet dekódolásához, hozzá kell adnia egy triggert a logikai alkalmazás indításához, például egy kérelem elindításához.

1. Az trigger hozzáadása után válassza az **új lépés** lehetőséget.

   ![Kérelem-trigger hozzáadása](media/logic-apps-enterprise-integration-rosettanet/request-trigger.png)

1. A keresőmezőbe írja be a "RosettaNet" kifejezést, és válassza a következő műveletet: **RosettaNet dekódolása**

   ![A "RosettaNet dekódolása" művelet megkeresése és kiválasztása](media/logic-apps-enterprise-integration-rosettanet/select-decode-rosettanet-action.png)

1. Adja meg a művelet tulajdonságainak adatait:

   ![Képernyőkép, amely megmutatja, hol adja meg a művelet tulajdonságait.](media/logic-apps-enterprise-integration-rosettanet/decode-action-details.png)

   | Tulajdonság | Kötelező | Leírás |
   |----------|----------|-------------|
   | **Üzenetet** | Yes | A dekódolni kívánt RosettaNet-üzenet  |
   | **Fejlécek** | Yes | Azok a HTTP-fejlécek, amelyek megadja a verzió értékeit, amely a RNIF verziója, valamint a válasz típusa, amely a partnerek közötti kommunikációs típust jelzi, és szinkron vagy aszinkron lehet |
   | **Szerepkör** | Yes | A gazda partner szerepe a PIP-ben |
   ||||

   A RosettaNet dekódolása műveletből a kimenet, valamint más tulajdonságok is tartalmazzák a **kimenő jeleket**, amelyekkel kódolhat és visszatérhet a partnernek, vagy bármilyen egyéb műveletet végrehajthat a kimeneten.

## <a name="send-or-encode-rosettanet-messages"></a>RosettaNet üzenetek küldése vagy kódolása

1. [Hozzon létre egy üres logikai alkalmazást](quickstart-create-first-logic-app-workflow.md).

1. [Kapcsolja össze integrációs fiókját](logic-apps-enterprise-integration-create-integration-account.md#link-account) a logikai alkalmazással.

1. Ahhoz, hogy hozzá lehessen adni egy műveletet a RosettaNet-üzenet kódolásához, hozzá kell adnia egy triggert a logikai alkalmazás indításához, például egy kérelem elindításához.

1. Az trigger hozzáadása után válassza az **új lépés** lehetőséget.

   ![Kérelem-trigger hozzáadása](media/logic-apps-enterprise-integration-rosettanet/request-trigger.png)

1. A keresőmezőbe írja be a "RosettaNet" kifejezést, majd válassza a következő műveletet: **RosettaNet kódolása**

   ![A "RosettaNet kódolás" művelet megkeresése és kiválasztása](media/logic-apps-enterprise-integration-rosettanet/select-encode-rosettanet-action.png)

1. Adja meg a művelet tulajdonságainak adatait:

   ![Művelet részleteinek megadása](media/logic-apps-enterprise-integration-rosettanet/encode-action-details.png)

   | Tulajdonság | Kötelező | Leírás |
   |----------|----------|-------------|
   | **Üzenetet** | Yes | A kódolni kívánt RosettaNet-üzenet  |
   | **Gazda partner** | Yes | A gazda partner neve |
   | **Vendég partner** | Yes | A vendég partner neve |
   | **PIP-kód** | Yes | A PIP-kód |
   | **PIP verziója** | Yes | A PIP verziója |  
   | **PIP-példány identitása** | Yes | A PIP-üzenet egyedi azonosítója |  
   | **Üzenettípus** | Yes | A kódolni kívánt üzenet típusa |  
   | **Szerepkör** | Yes | A gazda partner szerepe |
   ||||

   A kódolt üzenet most már készen áll a partnernek való küldésre.

1. A kódolt üzenet elküldéséhez a példa a **http** -műveletet használja, amelyet a rendszer a "http-kódolású üzenet küldése a partnernek" névvel nevez át.

   ![HTTP-művelet RosettaNet-üzenet küldéséhez](media/logic-apps-enterprise-integration-rosettanet/send-rosettanet-message-to-partner.png)

   A RosettaNet-szabványok szerint az üzleti tranzakciók csak akkor tekinthetők befejezettnek, ha a PIP által meghatározott összes lépés befejeződött.

1. Miután a gazdagép elküldi a kódolt üzenetet a partnernek, a gazdagép megvárja a jelzést és a nyugtát. A feladat végrehajtásához adja hozzá a **RosettaNet várakozás a válaszhoz** műveletet.

   !["RosettaNet Wait for Response" művelet hozzáadása](media/logic-apps-enterprise-integration-rosettanet/rosettanet-wait-for-response-action.png)

   A várakozási idő és az újrapróbálkozások száma az integrációs fiókban lévő PIP-konfiguráció alapján történik. Ha a válasz nem érkezik meg, a művelet sikertelen értesítést generál. Az újrapróbálkozások kezeléséhez mindig helyezze el a **kódolást** , és várjon, **amíg** a rendszer a **válaszadási** műveleteket a hurokban.

   ![RosettaNet műveletekkel való hurokig](media/logic-apps-enterprise-integration-rosettanet/rosettanet-loop.png)

## <a name="next-steps"></a>Következő lépések

* Megtudhatja, hogyan érvényesítheti, átalakíthatja és más üzenetkezelési műveleteket a [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md)
* További Logic Apps- [Összekötők](../connectors/apis-list.md) megismerése
