---
title: Kapcsolódás a helyi fájlrendszerek számára
description: Automatizálja a helyszíni fájlrendszerekhez csatlakozó feladatokat és munkafolyamatokat a helyi adatátjárón keresztül a Azure Logic Apps
services: logic-apps
ms.suite: integration
author: derek1ee
ms.author: deli
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 10/08/2020
ms.openlocfilehash: 4715d7173dd959d12350229e457717c908a83756
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91873234"
---
# <a name="connect-to-on-premises-file-systems-with-azure-logic-apps"></a>Csatlakozás helyszíni fájlrendszerekhez az Azure Logic Appsből

A Azure Logic Apps és a fájlrendszer-összekötővel olyan automatizált feladatokat és munkafolyamatokat hozhat létre, amelyek egy helyszíni fájlmegosztás fájljainak létrehozására és kezelésére használhatók, például:

- Fájlok létrehozása, beolvasása, hozzáfűzése, frissítése és törlése.
- Mappákban vagy gyökérkönyvtárban található fájlok listázása.
- Fájl tartalmának és metaadatainak beolvasása.

  > [!IMPORTANT]
  > A fájlrendszer-összekötő jelenleg csak Windows-fájlrendszereket támogat a Windows operációs rendszereken.  

Ez a cikk bemutatja, hogyan kapcsolódhat a helyi fájlrendszerhez a példa példája szerint: másolja a Dropboxba feltöltött fájlt egy fájlmegosztásba, majd küldjön egy e-mailt. A helyszíni rendszerek biztonságos csatlakoztatásához és eléréséhez a Logic apps a helyszíni [adatátjárót](../logic-apps/logic-apps-gateway-connection.md)használja. Ha most ismerkedik a Logic apps szolgáltatással, tekintse át a következőt: [Mi az Azure Logic apps?](../logic-apps/logic-apps-overview.md). Az összekötő-specifikus technikai információk a fájlrendszer- [összekötő dokumentációjában](/connectors/filesystem/)találhatók.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* Ahhoz, hogy a Logic Apps szolgáltatást a helyi rendszerekhez, például a fájlrendszerhez lehessen kapcsolni, [telepítenie és be kell állítania egy helyszíni adatátjárót](../logic-apps/logic-apps-gateway-install.md). Ily módon megadhatja, hogy az átjáró telepítését a logikai alkalmazás fájlrendszer-kapcsolatainak létrehozásakor használja.

* Egy [Dropbox-fiók](https://www.dropbox.com/), amelyet ingyenesen regisztrálhat. A fiók hitelesítő adatai szükségesek a logikai alkalmazás és a Dropbox-fiók közötti kapcsolat létrehozásához.

* Hozzáférés a használni kívánt fájlrendszerrel rendelkező számítógéphez. Ha például ugyanarra a számítógépre telepíti az adatátjárót, mint a fájlrendszert, szüksége lesz a számítógép fiókjának hitelesítő adataira.

* Egy Logic Apps által támogatott szolgáltató által használt e-mail-fiók, például Office 365 Outlook, Outlook.com vagy gmail. Más szolgáltatók esetén [tekintse át az itt felsorolt összekötőket](/connectors/). Ez a logikai alkalmazás munkahelyi vagy iskolai fiókot használ. Ha más e-mail-fiókot használ, a lépések ugyanazok, de a felhasználói felület kissé eltérhet.

  > [!IMPORTANT]
  > Ha a Gmail-összekötőt szeretné használni, akkor csak a G-Suite üzleti fiókok használhatják ezt az összekötőt a Logic apps korlátozás nélkül. Ha rendelkezik Gmail-fiókkal, akkor ezt az összekötőt csak meghatározott Google által jóváhagyott szolgáltatásokkal használhatja, vagy [létrehozhat egy Google-ügyfélprogramot, amelyet a Gmail-összekötővel történő hitelesítéshez használhat](/connectors/gmail/#authentication-and-bring-your-own-application). További információkért lásd: [adatbiztonsági és adatvédelmi szabályzatok a Google-összekötők számára a Azure Logic apps](../connectors/connectors-google-data-security-privacy-policy.md).

* Alapvető ismeretek a [logikai alkalmazások létrehozásáról](../logic-apps/quickstart-create-first-logic-app-workflow.md). Ebben a példában egy üres logikai alkalmazásra van szükség.

## <a name="add-trigger"></a>Trigger hozzáadása

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), és nyissa meg a logikai alkalmazást a Logic app Designerben, ha már nincs megnyitva.

1. A keresőmezőbe írja be a "Dropbox" kifejezést a szűrőként. Az eseményindítók listából válassza ki ezt az eseményindítót: **fájl létrehozásakor**

   ![Dropbox-trigger kiválasztása](media/logic-apps-using-file-connector/select-dropbox-trigger.png)

1. Jelentkezzen be a Dropbox-fiókja hitelesítő adataival, és engedélyezze a hozzáférést a Dropbox-adatokhoz Azure Logic Apps.

1. Adja meg az triggerhez szükséges adatokat.

   ![Dropbox-trigger](media/logic-apps-using-file-connector/dropbox-trigger.png)

## <a name="add-actions"></a>Műveletek hozzáadása

1. A trigger alatt válassza a **következő lépés** lehetőséget. A keresőmezőbe írja be szűrőként a "File System" (fájlrendszer) kifejezést. A műveletek listából válassza a következő műveletet: **fájl létrehozása**

   ![Fájlrendszer-összekötő keresése](media/logic-apps-using-file-connector/find-file-system-action.png)

1. Ha még nem rendelkezik a fájlrendszerrel létesített csatlakozással, a rendszer megkéri, hogy hozzon létre egy-egy kapcsolódást.

   ![Kapcsolat létrehozása](media/logic-apps-using-file-connector/file-system-connection.png)

   | Tulajdonság | Kötelező | Érték | Leírás |
   | -------- | -------- | ----- | ----------- |
   | **Kapcsolat neve** | Yes | <*kapcsolattípus*> | A kapcsolatok kívánt neve |
   | **Gyökérmappa** | Yes | <*gyökér-mappa neve*> | A fájlrendszer gyökérkönyvtára, például ha telepítette a helyszíni adatátjárót, például egy helyi mappát azon a számítógépen, amelyen a helyszíni adatátjáró telepítve van, vagy egy hálózati megosztás mappája, amelyhez a számítógép hozzáférhet. <p>Például: `\\PublicShare\\DropboxFiles` <p>A gyökérmappa a fő szülő mappa, amely a fájlokkal kapcsolatos összes művelet relatív elérési útjaihoz használható. |
   | **Hitelesítés típusa** | No | <*hitelesítés típusa*> | A fájlrendszer által használt hitelesítés típusa: **Windows** |
   | **Felhasználónév** | Yes | < > \\ tartomány < *Felhasználónév*> <p>-vagy- <p><*helyi – számítógép* > \\ < *Felhasználónév*> | Annak a számítógépnek a felhasználóneve, amelyen a fájlrendszer mappája található. <p>Ha a fájlrendszer mappája ugyanazon a számítógépen található, mint a helyszíni adatátjáró, akkor <*helyi számítógép* > \\ < *felhasználónevét*> használhatja. |
   | **Jelszó** | Yes | <*saját jelszó*> | A fájlrendszert futtató számítógép jelszava |
   | **átjáró** | Yes | <*telepítve – átjáró – név*> | A korábban telepített átjáró neve |
   |||||

1. Ha elkészült, kattintson a **Létrehozás** gombra.

   Logic Apps konfigurálja és teszteli a-kapcsolatokat, így biztosítva, hogy a kapcsolatok megfelelően működnek. Ha a kapcsolat megfelelően van beállítva, a beállítások megjelennek a korábban kiválasztott művelethez.

1. A **fájl létrehozása** műveletben adja meg a fájlok a dropboxból a helyi fájlmegosztás gyökérkönyvtárba való másolásának részleteit. Az előző lépésekből származó kimenetek hozzáadásához kattintson a dobozok elemre, majd válassza ki az elérhető mezőkből lehetőséget a dinamikus tartalmak listájának megjelenésekor.

   ![Fájl létrehozása művelet](media/logic-apps-using-file-connector/create-file-filled.png)

1. Most adjon hozzá egy olyan Outlook-műveletet, amely e-mailt küld, hogy a megfelelő felhasználók tudják az új fájlt. Adja meg az e-mail címzettjeit, címét és törzsét. A teszteléshez használhatja a saját e-mail-címét.

   ![E-mail küldése művelet](media/logic-apps-using-file-connector/send-email.png)

1. Mentse a logikai alkalmazást. Tesztelje az alkalmazást úgy, hogy feltölt egy fájlt a Dropboxba.

   A logikai alkalmazásnak át kell másolnia a fájlt a helyszíni fájlmegosztásba, és el kell küldenie a címzetteknek egy e-mailt a másolt fájlról.

## <a name="connector-reference"></a>Összekötő-referencia

Az összekötő részletes technikai részleteiről, például az eseményindítók, a műveletek és a korlátok az összekötő hencegő fájljában leírtak alapján: az [összekötő hivatkozási lapja](/connectors/fileconnector/).

> [!NOTE]
> Az [integrációs szolgáltatási környezet (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)logikai alkalmazásai esetében az összekötő ISE által címkézett verziója az [ISE-üzenetek korlátait](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) használja helyette.

## <a name="next-steps"></a>Következő lépések

* Ismerje meg, hogyan [csatlakozhat a helyszíni adatbázisokhoz](../logic-apps/logic-apps-gateway-connection.md) 
* További Logic Apps- [Összekötők](../connectors/apis-list.md) megismerése
