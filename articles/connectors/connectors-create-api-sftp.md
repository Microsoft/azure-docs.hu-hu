---
title: Kapcsolódás SFTP-fiókhoz (elavult)
description: Az SFTP-kiszolgálóhoz tartozó fájlok figyelésére, létrehozására, kezelésére, küldésére és fogadására szolgáló feladatok és folyamatok automatizálása Azure Logic Apps használatával
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 11/01/2019
tags: connectors
ROBOTS: NOINDEX
ms.openlocfilehash: 70fb956af7ff45c7b54f04d7ed441ec39f9d80a5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92673806"
---
# <a name="monitor-create-and-manage-sftp-files-in-azure-logic-apps"></a>SFTP-fájlok figyelése, létrehozása és kezelése Azure Logic Apps

> [!IMPORTANT]
> Használja az [SFTP-SSH összekötőt](../connectors/connectors-sftp-ssh.md) , mivel az SFTP-összekötő elavult. A Logic app Designerben már nem választhat SFTP eseményindítókat és műveleteket.

A [biztonságos File Transfer Protocol (SFTP)](https://www.ssh.com/ssh/sftp/) kiszolgálón található fájlok figyelésére, létrehozására, küldésére és fogadására szolgáló feladatok automatizálásához Azure Logic apps és az SFTP-összekötő használatával hozhat létre és automatizálhat integrációs munkafolyamatokat. Az SFTP olyan hálózati protokoll, amely fájlhozzáférést, fájlátvitelt és fájlfelügyeletet biztosít valamilyen megbízható adatstreamen keresztül. Íme néhány példa a feladatok automatizálására:

* A fájlok hozzáadásának vagy módosításának figyelése.
* Fájlok lekérése, létrehozása, másolása, frissítése, listázása és törlése.
* Fájl tartalmának és metaadatainak beolvasása.
* Archívumok kinyerése mappákba.

Az SFTP-kiszolgálón lévő eseményeket figyelő eseményindítókat használhat, és a kimenetet más műveletek számára is elérhetővé teheti. Az SFTP-kiszolgálón különféle feladatokat végző műveleteket is használhat. A logikai alkalmazásban más műveletek is használhatók az SFTP-műveletek kimenetének használatával. Ha például az SFTP-kiszolgálóról rendszeresen lekéri a fájlokat, e-mail-riasztásokat küldhet a fájlokról és azok tartalmáról az Office 365 Outlook Connector vagy a Outlook.com Connector használatával. Ha most ismerkedik a Logic apps szolgáltatással, tekintse át [a mi az Azure Logic apps?](../logic-apps/logic-apps-overview.md)

## <a name="limits"></a>Korlátok

Az SFTP-összekötő csak a *50 MB vagy annál kisebb* fájlokat kezeli, és nem támogatja az [üzenetek darabolását](../logic-apps/logic-apps-handle-large-messages.md). Nagyobb fájlok esetén használja az [SFTP-SSH összekötőt](../connectors/connectors-sftp-ssh.md). Az SFTP-összekötő és az SFTP-SSH összekötő közötti különbségekért tekintse át a következőt: az SFTP-SSH és az [SFTP összehasonlítása](../connectors/connectors-sftp-ssh.md#comparison) az SFTP-SSH-cikkben.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* Az SFTP-kiszolgáló címe és a fiók hitelesítő adatai, amelyek lehetővé teszik a logikai alkalmazás számára az SFTP-fiók elérését. A [Secure Shell (SSH)](https://www.ssh.com/ssh/protocol/) protokoll használatához hozzá kell férnie egy SSH titkos kulcshoz és a titkos SSH-kulcs jelszavához is.

  > [!NOTE]
  >
  > Az SFTP-összekötő a következő titkos kulcsok formátumait támogatja: OpenSSH, ssh.com és Putty
  >
  > A logikai alkalmazás létrehozása után az SFTP-trigger vagy a kívánt művelet hozzáadása után meg kell adnia az SFTP-kiszolgáló kapcsolódási adatait. 
  > Ha SSH titkos kulcsot használ, győződjön meg arról, hogy a kulcsot a titkos SSH-kulcs fájljából ***másolja*** , majd ***illessze*** be az adott kulcsot a kapcsolat részleteibe, ***ne adja meg manuálisan a kulcsot, vagy szerkessze a kulcsot***, ami miatt a kapcsolat sikertelen lehet. 
  > További információkért tekintse meg a cikk későbbi lépéseit.

* Alapvető ismeretek a [logikai alkalmazások létrehozásáról](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Az a logikai alkalmazás, ahová el szeretné érni az SFTP-fiókját. Ha egy SFTP-triggert szeretne kezdeni, [hozzon létre egy üres logikai alkalmazást](../logic-apps/quickstart-create-first-logic-app-workflow.md). Ha SFTP-műveletet szeretne használni, indítsa el a logikai alkalmazást egy másik eseményindítóval, például az **ismétlődési** eseményindítóval.

## <a name="how-sftp-triggers-work"></a>Az SFTP-eseményindítók működése

Az SFTP-eseményindítók az SFTP fájlrendszer lekérdezésével és a legutóbbi lekérdezés óta módosított fájlok keresésével működnek. Egyes eszközök lehetővé teszik, hogy a fájlok változásakor őrizze meg az időbélyeget. Ezekben az esetekben le kell tiltania ezt a funkciót, így az trigger működhet. Íme néhány gyakori beállítás:

| SFTP-ügyfél | Művelet |
|-------------|--------|
| WinSCP | Ugrás a **Beállítások**  >  **Beállítások**  >  **átvitel**  >  **szerkesztési**  >  **megőrzési időbélyegének**  >  **letiltása** |
| Filezillát | Ugrás az   >  **átvitt fájlok adatmegőrzési időbélyegére –**  >  **Letiltás** |
|||

Ha egy trigger új fájlt talál, az trigger ellenőrzi, hogy az új fájl elkészült-e, és nem részlegesen van-e írva. Előfordulhat például, hogy egy fájl változása folyamatban van, amikor az trigger ellenőrzi a fájlkiszolgálón. Egy részlegesen megírt fájl visszaadásának elkerüléséhez az trigger megállapítja a legutóbbi módosításokat tartalmazó fájl időbélyegét, de nem adja vissza azonnal a fájlt. Az trigger csak akkor adja vissza a fájlt, ha újra kérdezi le a kiszolgálót. Előfordulhat, hogy ez a viselkedés egy késleltetést okoz, amely akár kétszer is meghaladhatja az aktiválás lekérdezési időközét.

## <a name="connect-to-sftp"></a>Kapcsolódás az SFTP-hez

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), és nyissa meg a logikai alkalmazást a Logic app Designerben, ha már nincs megnyitva.

1. Üres logikai alkalmazások esetén a keresőmezőbe írja be a "SFTP" kifejezést a szűrő mezőbe. Válassza ki a kívánt eseményindítót az eseményindítók listából.

   -vagy-

   Meglévő Logic apps esetén az utolsó lépésben, amelyhez műveletet szeretne hozzáadni, válassza az **új lépés** lehetőséget. A keresőmezőbe írja be az "SFTP" kifejezést a szűrőként. A műveletek listában válassza ki a kívánt műveletet.

   A lépések közötti művelet hozzáadásához vigye a mutatót a lépések közötti nyíl fölé. Válassza ki a **+** megjelenő pluszjelet (), majd válassza a **művelet hozzáadása** lehetőséget.

1. Adja meg a kapcsolathoz szükséges adatokat.

   > [!IMPORTANT]
   >
   > Ha megadja az SSH titkos kulcsát az **SSH titkos kulcs** tulajdonságában, kövesse ezeket a további lépéseket, amelyekkel biztosíthatja, hogy a tulajdonság teljes és megfelelő értékét adja meg. 
   > Érvénytelen kulcs miatt a kapcsolódás sikertelen lesz.

   Habár bármilyen szövegszerkesztőt használhat, az alábbi példákban bemutatjuk, hogyan lehet helyesen másolni és beilleszteni a kulcsot Notepad.exe példaként.

   1. Nyissa meg az SSH titkos kulcs fájlját egy szövegszerkesztőben. Ezek a lépések példaként használják a jegyzettömböt.

   1. A Jegyzettömb **Szerkesztés** menüjében válassza az **összes kijelölése** lehetőséget.

   1. Válassza a  >  **Másolás** szerkesztése lehetőséget.

   1. Az SFTP-triggerben vagy a hozzáadott műveletben illessze be az **SSH titkos kulcs** tulajdonságba másolt *teljes* kulcsot, amely több sort is támogat. **_Ügyeljen rá, hogy a kulcsot illessze be_*a kulcsba. _*_ne adja meg manuálisan a kulcsot, vagy szerkessze_ azt**.

1. Ha végzett a kapcsolat részleteinek megadásával, válassza a **Létrehozás** lehetőséget.

1. Adja meg a kiválasztott trigger vagy művelet szükséges adatait, és folytassa a logikai alkalmazás munkafolyamatának összeállítását.

## <a name="examples"></a>Példák

<a name="file-add-modified"></a>

### <a name="sftp-trigger-when-a-file-is-added-or-modified"></a>SFTP-trigger: fájl hozzáadásakor vagy módosításakor

Ez az aktiválás egy logikai alkalmazás munkafolyamatát indítja el, amikor egy fájlt hozzáadnak vagy módosítanak egy SFTP-kiszolgálón. Hozzáadhat például egy olyan feltételt, amely ellenőrzi a fájl tartalmát, és beolvassa a tartalmat attól függően, hogy a tartalom megfelel-e a megadott feltételnek. Ezután hozzáadhat egy műveletet, amely beolvassa a fájl tartalmát, és az SFTP-kiszolgáló egy mappájába helyezi a tartalmat.

**Vállalati példa**: ezt az triggert használhatja az ügyfél-megrendeléseket képviselő új fájlok SFTP-mappájának figyelésére. Ezután használhat egy SFTP-műveletet, például a **fájlok beolvasása** lehetőséget, így a sorrend tartalmát megtekintve további feldolgozást hajthat végre, és a rendelést egy Orders adatbázisban tárolhatja.

<a name="get-content"></a>

### <a name="sftp-action-get-content"></a>SFTP-művelet: tartalom lekérése

Ez a művelet lekérdezi a tartalmat egy SFTP-kiszolgálón lévő fájlból. Így például felveheti az triggert az előző példából, és egy olyan feltételt, amelynek meg kell felelnie a fájl tartalmának. Ha a feltétel igaz, akkor a tartalmat lekérdező művelet futtatható.

## <a name="connector-reference"></a>Összekötő-referencia

Az eseményindítókkal, műveletekkel és korlátokkal kapcsolatos technikai részletekért lásd az összekötő OpenAPI (korábban: hencegés) leírását, tekintse át az összekötő [hivatkozási oldalát](../data-factory/connector-sftp.md).

## <a name="next-steps"></a>Következő lépések

* További Logic Apps- [Összekötők](../connectors/apis-list.md) megismerése