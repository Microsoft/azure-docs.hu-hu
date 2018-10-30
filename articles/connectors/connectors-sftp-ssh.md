---
title: Csatlakozhat SFTP-fiókjához, az Azure Logic Apps |} A Microsoft Docs
description: Automatizálhatja a feladatokat és a munkafolyamatok, amelyek figyelése, létrehozása, kezelése, küldése és fogadása fájlok SFTP-kiszolgálóra az Azure Logic Apps használatával
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
tags: connectors
ms.date: 09/24/2018
ms.openlocfilehash: 2250c6952aeac7b10dcb1a1a35419941e5cad507
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50233208"
---
# <a name="monitor-create-and-manage-sftp-files-by-using-azure-logic-apps-and-sftp-ssh-connector"></a>Figyelheti, létrehozása és kezelése az SFTP-fájlok Azure Logic Apps és az SFTP-SSH-összekötő használatával

Az Azure Logic Apps és az SFTP-SSH-összekötő, automatizált feladatokat és figyelése, létrehozása, küldése és fájlokat a fiókján keresztül fogad a munkafolyamatokat hozhat létre egy [SFTP](https://www.ssh.com/ssh/sftp/) kiszolgáló, valamint más műveletek, például:

* Ez a figyelő fájlok hozzáadásakor vagy módosítani.
* Első, létrehozása, másolja, átnevezése, frissítse a listát, és fájlok törlése.
* Mappa létrehozása.
* Fájl tartalom és metaadatok beolvasása.
* Mappák archívumok kibontása.

Használhatja az eseményindítókat, amelyek választ kaphat az SFTP-kiszolgáló és a kimenetet más műveletek számára elérhetővé tenni. Műveletek a logic Apps segítségével az SFTP-kiszolgálón lévő fájlok feladatokat. Az SFTP-műveletek kimenetét használják más műveleteket is rendelkezhet. Például ha rendszeresen kérnek le fájlok az SFTP-kiszolgálóról, elküldheti e-mailek ezeket a fájlokat és a tartalom az Office 365 Outlook-összekötőt vagy Outlook.com-összekötő használatával.
Ha most ismerkedik a logic apps, tekintse át [Mi az Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

## <a name="sftp-ssh-versus-sftp"></a>Az SFTP-SSH SFTP és

Az alábbiakban az SFTP-SSH-összekötő néhány lényeges különbség, és a [SFTP](../connectors/connectors-create-api-sftp.md) összekötő. Az SFTP-SSH-összekötő ezeket a képességeket biztosítja:

* Használja a <a href="https://github.com/sshnet/SSH.NET" target="_blank"> **SSH.NET** </a> könyvtár, amely egy nyílt forráskódú Secure Shell (SSH) könyvtár a .NET-hez.

* Támogatást biztosít a nagy méretű fájlok akár **1 GB-os**. Az összekötő olvashatja vagy írhatja, amely akár 1 GB méretű fájlokat.

* Itt a **mappa létrehozása** művelet, amely egy mappát hoz létre az SFTP-kiszolgáló a megadott elérési úton.

* Itt a **fájl átnevezése tevékenység** művelet, amely az SFTP-kiszolgáló egy fájlt nevez át.

* Gyorsítótárazza a kapcsolat az SFTP-kiszolgálóra, amely növeli a teljesítményt, és csökkenti a kiszolgáló kapcsolódási kísérletek számát. 

  Az időtartam gyorsítótárazza a kapcsolat beállításával szabályozhatja a <a href="http://man.openbsd.org/sshd_config#ClientAliveInterval" target="_blank"> **ClientAliveInterval** </a> tulajdonsága az SFTP-kiszolgálóján. 

## <a name="how-trigger-polling-works"></a>Hogyan aktiválhat lekérdezési működése

Az SFTP-SSH eseményindítók működnek az SFTP-fájlrendszer lekérdezésével, és keres olyan fájlt, amely a legutóbbi lekérdezés óta módosult. Egyes eszközök teszik lehetővé a megőrzése történő küldés időbélyegzője legyen, amikor módosítja a fájlokat, így ezekben az esetekben le kell tiltania a funkció működéséhez az eseményindító. Az alábbiakban néhány gyakori beállítások:

| Az SFTP-ügyfél | Műveletek | 
|-------------|--------| 
| Winscp | Beállítások → beállítások... → Átviteli → szerkesztése... Tiltsa le a Preserve időbélyeg → → |
| Filezillát | Az átvitt fájlok → átviteli → Preserve időbélyegeket letiltása | 
||| 

Ha az eseményindító egy új fájlt talál, a trigger ellenőrzi, hogy az új fájl teljes és részlegesen írásos. Például egy fájl előfordulhat módosítások folyamatban, amikor a trigger ellenőriz a fájlkiszolgálón. Részlegesen írásos fájl visszaadó elkerüléséhez az eseményindító feljegyzi az időbélyeg, amely rendelkezik a legutóbbi módosítások, de nem ad vissza a fájlt közvetlenül a fájl. A trigger a fájl adja vissza, csak akkor, ha a kiszolgáló ismét lekérdezés. Egyes esetekben ez a viselkedés, amely legfeljebb kétszer az eseményindító a lekérdezési időköz késleltetés okozhatja. 

Fájl tartalmának kérésekor az eseményindító nem tölti le 50 MB-nál nagyobb méretű fájlokat. Csomópontmetrikák 50 MB-nál nagyobb méretű fájlokat, hajtsa végre ezt a mintát:

* Használjon egy eseményindítót, amely visszaadja a fájl tulajdonságait, például **fájl hozzáadásakor vagy módosításakor (csak tulajdonságok)**. 
* Hajtsa végre az eseményindítót egy műveletet, amely beolvassa a teljes fájlt, mint például a **fájl tartalmának beolvasása elérési út segítségével**.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, <a href="https://azure.microsoft.com/free/" target="_blank">regisztráljon egy ingyenes Azure-fiókra</a>. 

* Az SFTP gazdagép kiszolgáló címét és a fiók hitelesítő adatait, amelyek engedélyezik a logikai alkalmazás, hozzon létre egy kapcsolatot, és hozzáférhet az SFTP-fiókjához.

  Másolja és illessze be a titkos SSH-kulcs, a teljes tartalmát a **titkos SSH-kulcs** tulajdonság a következő, többsoros formátumban. 
  Az alábbiakban a mintául szolgáló lépéseket, amelyek bemutatják a Notepad.exe használatával adja meg a titkos SSH-kulcs:
    
  1. Nyissa meg az SSH titkos kulcs fájlját a Notepad.exe
  2. Az a **szerkesztése** menüjében válassza **válassza ki az összes**.
  3. Válassza ki **szerkesztése** > **másolási**.
  4. Amikor a kapcsolatot hoz létre a a **titkos SSH-kulcs** tulajdonság, illessze be a kulcsot. Manuálisan ne módosítsa a **titkos SSH-kulcs** tulajdonság.

     Az összekötő támogatja-e SSH-titkos kulcs formátumot, és csak az MD5 ujjlenyomat szalagtárban SSH.NET használja:

     * RSA 
     * DSA

* Alapvető ismeretek szerezhetők [logikai alkalmazások létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A logikai alkalmazás, ahol szeretné elérni az SFTP-fiókjához. Szeretne kezdeni egy SFTP-triggert [hozzon létre egy üres logikai alkalmazás](../logic-apps/quickstart-create-first-logic-app-workflow.md). SFTP-művelet használatához indítsa el a logikai alkalmazás egy másik eseményindítóval, például a **ismétlődési** eseményindító.

## <a name="connect-to-sftp"></a>Csatlakozhat SFTP

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com), és nyissa meg a logikai alkalmazás a Logikaialkalmazás-Tervező, ha nem, nyissa meg a már.

1. Üres logic Apps a keresőmezőbe írja be szűrőként "sftp". Eseményindítók listája alatt válassza ki a kívánt az eseményindító. 

   – vagy –

   Meglévő logic Apps alkalmazások, az utolsó lépés, adjon hozzá egy műveletet, amelyre a válasszon **új lépés**. 
   A Keresés mezőbe írja be szűrőként "sftp". 
   Műveletek listája alatt válassza ki a kívánt művelet.

   Lépések közötti művelet hozzáadása, helyezze az egérmutatót a nyíl lépések között. 
   Válassza a plusz jelre (**+**), amely akkor jelenik meg, és válassza ki **művelet hozzáadása**.

1. Adja meg a szükséges adatokat a kapcsolatot, és válassza a **létrehozás**.

1. Adja meg a szükséges adatokat a kijelölt eseményindítót vagy műveletet, és továbbra is használhatja a logic app-munkafolyamatot.

## <a name="examples"></a>Példák

### <a name="sftp-trigger-when-a-file-is-added-or-modified"></a>Az SFTP-eseményindító: amikor felvesznek vagy módosítanak egy fájlt

Ez az eseményindító egy logikai alkalmazás munkafolyamatának kezdődik meg az eseményindító észleli, ha egy fájl hozzáadásakor vagy módosítja a SFTP-kiszolgálóra. Így például hozzáadhat egy feltételt, amely ellenőrzi a fájl tartalmát, és úgy dönt, hogy kapják meg a tartalmat a e tartalom megfelel-e a megadott feltétel alapján. Végül adjon hozzá egy műveletet, amely a fájl tartalmának beolvasása, és helyezi a tartalmat egy mappába az SFTP-kiszolgáló. 

**Példa vállalati**: a trigger használatával figyelheti a egy új megrendelések képviselő fájlok SFTP-mappába. Ezután használhatja az SFTP-művelet például **fájl tartalmának beolvasása**, hogy a rendelés tartalmának beolvasása a további feldolgozás céljából, valamint egy rendelési adatbázisba sorrendben tárolja.

### <a name="sftp-action-get-content"></a>SFTP-művelet: tartalom lekérése

Ez a művelet a tartalom olvas be egy fájl az SFTP-kiszolgálóra. Így például az előző példában és a egy feltételt, amely a fájl tartalmának meg kell felelnie az eseményindító is hozzáadhat. Ha a feltétel teljesül, a műveletet, amely lekérdezi a tartalmat futtathatja. 

## <a name="connector-reference"></a>Összekötő-referencia

További technikai részletek korlátok, eseményindítók és műveletek, amely ismerteti az összekötő OpenAPI által (korábbi nevén Swagger) leírását, tekintse át az összekötő [referencialapja](/connectors/sftpconnector/).

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* A funkciókkal kapcsolatos ötletek elküldéséhez vagy megszavazásához látogasson el a [Logic Apps felhasználói visszajelzéseinek oldalára](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>További lépések

* További információk egyéb [Logic Apps-összekötők](../connectors/apis-list.md)