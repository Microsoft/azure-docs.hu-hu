---
title: Kapcsolódás az SFTP-kiszolgálóhoz SSH-val
description: Automatizálhatja az SFTP-kiszolgálókhoz tartozó fájlok figyelését, létrehozását, kezelését, küldését és fogadását SSH és Azure Logic Apps használatával.
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.reviewer: estfan, logicappspm
ms.topic: article
ms.date: 11/03/2020
tags: connectors
ms.openlocfilehash: 31714eee2e79481bbc8afb47718ed38e178d5b82
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2020
ms.locfileid: "93324246"
---
# <a name="monitor-create-and-manage-sftp-files-by-using-ssh-and-azure-logic-apps"></a>SFTP-fájlok monitorozása, létrehozása és kezelése SSH és az Azure Logic Apps használatával

A [Secure Shell (SSH)](https://www.ssh.com/ssh/protocol/) protokoll használatával a biztonságos [File Transfer Protocol (SFTP)](https://www.ssh.com/ssh/sftp/) kiszolgálón lévő fájlok figyelésére, létrehozására, küldésére és fogadására szolgáló feladatok automatizálásához Azure Logic apps és az SFTP-SSH összekötő használatával hozhat létre és automatizálhat integrációs munkafolyamatokat. Az SFTP olyan hálózati protokoll, amely fájlhozzáférést, fájlátvitelt és fájlfelügyeletet biztosít valamilyen megbízható adatstreamen keresztül.

> [!NOTE]
> Az SFTP-SSH-összekötő jelenleg nem támogatja ezeket az SFTP-kiszolgálókat:
> 
> * IBM DataPower
> * MessageWay
> * OpenText Secure MFT
> * OpenText GXS

Íme néhány példa a feladatok automatizálására:

* A fájlok hozzáadásának vagy módosításának figyelése.
* Fájlok lekérése, létrehozása, másolása, átnevezése, frissítése, listázása és törlése.
* Mappák létrehozása.
* Fájl tartalmának és metaadatainak beolvasása.
* Archívumok kinyerése mappákba.

Az SFTP-kiszolgálón lévő eseményeket figyelő eseményindítókat használhat, és a kimenetet más műveletek számára is elérhetővé teheti. Az SFTP-kiszolgálón különféle feladatokat végző műveleteket is használhat. A logikai alkalmazásban más műveletek is használhatók az SFTP-műveletek kimenetének használatával. Ha például az SFTP-kiszolgálóról rendszeresen lekéri a fájlokat, e-mail-riasztásokat küldhet a fájlokról és azok tartalmáról az Office 365 Outlook Connector vagy a Outlook.com Connector használatával. Ha most ismerkedik a Logic apps szolgáltatással, tekintse át [a mi az Azure Logic apps?](../logic-apps/logic-apps-overview.md)

Az SFTP-SSH-összekötő és az SFTP-összekötő közötti különbségekért tekintse át a témakör későbbi, az [SFTP-SSH és az SFTP összehasonlítása](#comparison) című szakaszát.

## <a name="limits"></a>Korlátok

* Az SFTP-SSH összekötő a titkos kulcsos hitelesítés vagy a jelszó-hitelesítés használatát is támogatja.

* SFTP – az [adatdarabolást](../logic-apps/logic-apps-handle-large-messages.md) támogató SSH-műveletek legfeljebb 1 GB-tal kezelhetik a fájlokat, míg az olyan SFTP-SSH-műveletek, amelyek nem támogatják a darabolást, akár 50 MB-ot is kezelhetnek. Bár az alapértelmezett méret 15 MB, ez a méret dinamikusan változhat, 5 MB-tól kezdődően, és fokozatosan növekszik a 50 MB-os maximális értékre, az olyan tényezők alapján, mint a hálózati késés, a kiszolgáló válaszideje és így tovább.

  > [!NOTE]
  > Az [integrációs szolgáltatási környezet (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)logikai alkalmazásai esetében ez az összekötő ISE-címkével ellátott verziója adatdarabolást igényel az [ISE-üzenetek használatának korlátozásához](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) .

  Ezt az adaptív viselkedést felülbírálhatja, ha [egy állandó adatméretet ad meg](#change-chunk-size) helyette. Ez a méret 5 MB és 50 MB között lehet. Tegyük fel például, hogy rendelkezik egy 45 MB-os fájllal és egy olyan hálózattal, amely az adott fájlméretet késés nélkül támogatja. Az adaptív adatdarabolás több hívást eredményez, inkább egy hívást. A hívások számának csökkentéséhez próbáljon meg 50 MB-os méretet beállítani. A különböző forgatókönyvekben, ha a logikai alkalmazás időtúllépést mutat be, például 15 MB-os adattömbök használata esetén, a méretet 5 MB-ra csökkentheti.

  Az adatrészlet mérete egy kapcsolatban van társítva, ami azt jelenti, hogy ugyanazt a kapcsolatokat használhatja a darabolást támogató műveletekhez, majd olyan műveletekhez, amelyek nem támogatják a darabolást. Ebben az esetben az adathalmaz mérete olyan műveletek esetében, amelyek nem támogatják az adatdarabolási tartományokat 5 MB-ról 50 MB-ra. Ez a táblázat azt mutatja, hogy mely SFTP-SSH-műveletek támogatják a darabolást:

  | Művelet | Adatdarabolás támogatása | Adatméret-méretezési támogatás felülbírálása |
  |--------|------------------|-----------------------------|
  | **Fájl másolása** | Nem | Nem értelmezhető |
  | **Fájl létrehozása** | Igen | Igen |
  | **Mappa létrehozása** | Nem értelmezhető | Nem értelmezhető |
  | **Fájl törlése** | Nem értelmezhető | Nem értelmezhető |
  | **Archív fájl kibontása a mappába** | Nem értelmezhető | Nem értelmezhető |
  | **Fájl tartalmának beolvasása** | Igen | Igen |
  | **Fájl tartalmának beolvasása elérési út alapján** | Igen | Igen |
  | **Fájl metaadatainak beolvasása** | Nem értelmezhető | Nem értelmezhető |
  | **Fájl metaadatainak beolvasása elérési út használatával** | Nem értelmezhető | Nem értelmezhető |
  | **Mappában található fájlok listázása** | Nem értelmezhető | Nem értelmezhető |
  | **Fájl átnevezése** | Nem értelmezhető | Nem értelmezhető |
  | **Fájl frissítése** | Nem | Nem értelmezhető |
  ||||

* SFTP – az SSH-eseményindítók nem támogatják az üzenetek darabolását. Fájl tartalmának kérésekor az eseményindítók csak a 15 MB vagy annál kisebb fájlokat jelölik ki. A 15 MB-nál nagyobb fájlok lekéréséhez kövesse az alábbi mintát:

  1. Használjon olyan SFTP-SSH-triggert, amely csak a fájl tulajdonságait adja vissza, például **egy fájl hozzáadásakor vagy módosításakor (csak tulajdonságok)**.

  1. Kövesse a triggert az SFTP-SSH- **Get fájl tartalma** művelettel, amely beolvassa a teljes fájlt, és implicit módon használja az üzenetek darabolását.

<a name="comparison"></a>

## <a name="compare-sftp-ssh-versus-sftp"></a>Az SFTP-SSH és az SFTP összehasonlítása

Íme az SFTP-SSH-összekötő és az SFTP-összekötő, ahol az SFTP-SSH összekötő az alábbi képességekkel rendelkezik:

* A a [SSH.net könyvtárat](https://github.com/sshnet/SSH.NET)használja, amely a .NET-et támogató nyílt forráskódú Secure Shell-(SSH-) kódtár.

* Megadja a **mappa létrehozása** műveletet, amely létrehoz egy MAPPÁT az SFTP-kiszolgálón megadott elérési úton.

* Megadja a **fájl átnevezése** műveletet, amely átnevezi az SFTP-kiszolgálón található fájlt.

* *Akár 1 óráig* is gyorsítótárazza a kapcsolatot az SFTP-kiszolgálóval, ami javítja a teljesítményt, és csökkenti a próbálkozások számát a kiszolgálóhoz való csatlakozáskor. A gyorsítótárazási viselkedés időtartamának beállításához szerkessze a [**ClientAliveInterval**](https://man.openbsd.org/sshd_config#ClientAliveInterval) tulajdonságot az SFTP-kiszolgálón található SSH-konfigurációban.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* Az SFTP-kiszolgáló címe és a fiók hitelesítő adatai, amelyek lehetővé teszik a logikai alkalmazás számára az SFTP-fiók elérését. Emellett hozzá kell férnie egy SSH titkos kulcshoz és az SSH titkos kulcs jelszavához. Ha nagyméretű fájlokat tölt fel, a daraboláshoz olvasási és írási engedéllyel kell rendelkeznie az SFTP-kiszolgálón lévő gyökérkönyvtárhoz. Ellenkező esetben "401 jogosulatlan" hibaüzenetet kap.

  > [!IMPORTANT]
  >
  > Az SFTP-SSH összekötő *csak* ezeket a titkos kulcs formátumait, algoritmusait és ujjlenyomatait támogatja:
  >
  > * **Titkos kulcs formátuma** : RSA (Rivest-Adleman) és DSA (digitális aláírási algoritmus) kulcsok az OpenSSH-ban és a SSH.com-formátumokban. Ha a titkos kulcs Putty (. PPK) fájlformátumban van, először [alakítsa át a kulcsot az OpenSSH (. PEM)](#convert-to-openssh)fájlformátumba.
  >
  > * **Titkosítási algoritmusok** : des-EDE3-CBC, des-EDE3-CFB, des-CBC, AES-128-CBC, AES-192-CBC és aes-256-CBC
  >
  > * **Ujjlenyomat** : MD5
  >
  > Miután hozzáadta az SFTP-SSH-triggert vagy a logikai alkalmazáshoz használni kívánt műveletet, meg kell adnia az SFTP-kiszolgáló kapcsolódási adatait. Ha megadja az SSH titkos kulcsát ehhez a csatlakozáshoz, a * *_ne adja meg manuálisan a_* (_) kulcsot, ami miatt a kapcsolódás sikertelen lehet. Ehelyett a _*_kulcsot másolja_*_ a titkos SSH-kulcs fájljából, és _*_illessze_*_ be a kulcsot a kapcsolat részleteibe. 
  > További információkért lásd a jelen cikk a [Kapcsolódás az SFTP-hez SSH-val](#connect) című szakaszát.

_ Alapvető ismeretek a [logikai alkalmazások létrehozásáról](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* Az a logikai alkalmazás, ahová el szeretné érni az SFTP-fiókját. Egy SFTP-SSH triggerrel való kezdéshez [hozzon létre egy üres logikai alkalmazást](../logic-apps/quickstart-create-first-logic-app-workflow.md). Ha SFTP-SSH műveletet szeretne használni, indítsa el a logikai alkalmazást egy másik eseményindítóval, például az **ismétlődési** eseményindítóval.

## <a name="how-sftp-ssh-triggers-work"></a>Az SFTP-SSH-triggerek működése

Az SFTP-SSH elindítja az SFTP fájlrendszer lekérdezésével és a legutóbbi lekérdezés óta módosult fájlok keresésével kapcsolatos munkát. Egyes eszközök lehetővé teszik, hogy a fájlok változásakor őrizze meg az időbélyeget. Ezekben az esetekben le kell tiltania ezt a funkciót, így az trigger működhet. Íme néhány gyakori beállítás:

| SFTP-ügyfél | Művelet |
|-------------|--------|
| WinSCP | Ugrás a **Beállítások**  >  **Beállítások**  >  **átvitel**  >  **szerkesztési**  >  **megőrzési időbélyegének**  >  **letiltása** |
| Filezillát | Ugrás az **Transfer**  >  **átvitt fájlok adatmegőrzési időbélyegére –**  >  **Letiltás** |
|||

Ha egy trigger új fájlt talál, az trigger ellenőrzi, hogy az új fájl elkészült-e, és nem részlegesen van-e írva. Előfordulhat például, hogy egy fájl változása folyamatban van, amikor az trigger ellenőrzi a fájlkiszolgálón. Egy részlegesen megírt fájl visszaadásának elkerüléséhez az trigger megállapítja a legutóbbi módosításokat tartalmazó fájl időbélyegét, de nem adja vissza azonnal a fájlt. Az trigger csak akkor adja vissza a fájlt, ha újra kérdezi le a kiszolgálót. Előfordulhat, hogy ez a viselkedés egy késleltetést okoz, amely akár kétszer is meghaladhatja az aktiválás lekérdezési időközét.

<a name="convert-to-openssh"></a>

## <a name="convert-putty-based-key-to-openssh"></a>Putty-alapú kulcs konvertálása az OpenSSH-ra

Ha a titkos kulcs Putty formátumú, amely a. PPK (Putty titkos kulcs) fájlnévkiterjesztést használja, először alakítsa át a kulcsot az OpenSSH formátumba, amely a. PEM (Privacy Enhanced Mail) fájlnévkiterjesztést használja.

### <a name="unix-based-os"></a>UNIX-alapú operációs rendszer

1. Ha a PuTTY-eszközök még nincsenek telepítve a rendszeren, tegye meg most, például:

   `sudo apt-get install -y putty`

1. Futtassa ezt a parancsot, amely létrehoz egy fájlt, amely az SFTP-SSH összekötővel használható:

   `puttygen <path-to-private-key-file-in-PuTTY-format> -O private-openssh -o <path-to-private-key-file-in-OpenSSH-format>`

   Például:

   `puttygen /tmp/sftp/my-private-key-putty.ppk -O private-openssh -o /tmp/sftp/my-private-key-openssh.pem`

### <a name="windows-os"></a>Windows operációs rendszer

1. Ha még nem tette meg, [töltse le a legújabb Putty Generator (puttygen.exe) eszközt](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html), majd indítsa el az eszközt.

1. Ezen a képernyőn válassza a **Betöltés** lehetőséget.

   ![Válassza a "betöltés" lehetőséget](./media/connectors-sftp-ssh/puttygen-load.png)

1. Tallózással keresse meg a titkos kulcs fájlját Putty formátumban, majd válassza a **Megnyitás** lehetőséget.

1. A **konverziók** menüben válassza az OpenSSH- **kulcs exportálása** lehetőséget.

   ![Válassza az "OpenSSH-kulcs exportálása" lehetőséget](./media/connectors-sftp-ssh/export-openssh-key.png)

1. Mentse a titkos kulcs fájlját a fájlnévkiterjesztés megnyomásával `.pem` .

## <a name="considerations"></a>Megfontolandó szempontok

Ez a szakasz az összekötő eseményindítóinak és műveleteinek áttekintésére vonatkozó szempontokat ismerteti.

<a name="create-file"></a>

### <a name="create-file"></a>Fájl létrehozása

Az SFTP-kiszolgálón található fájl létrehozásához használhatja az SFTP-SSH **create file** műveletet. Amikor ez a művelet létrehozza a fájlt, a Logic Apps szolgáltatás automatikusan meghívja az SFTP-kiszolgálót a fájl metaadatainak beolvasására. Ha azonban áthelyezi az újonnan létrehozott fájlt, mielőtt a Logic Apps szolgáltatás megkéri a metaadatok lekérését, `404` hibaüzenetet kap `'A reference was made to a file or folder which does not exist'` . Ha szeretné kihagyni a fájl metaadatait a fájl létrehozása után, kövesse a következő lépéseket: [az **összes fájl metaadatainak beolvasása** tulajdonság hozzáadásának és beállításának **No**](#file-does-not-exist)lépései.

<a name="connect"></a>

## <a name="connect-to-sftp-with-ssh"></a>Kapcsolódás az SFTP-hez SSH-val

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), és nyissa meg a logikai alkalmazást a Logic app Designerben, ha már nincs megnyitva.

1. Üres logikai alkalmazások esetén a keresőmezőbe írja be a `sftp ssh` szűrőt. Válassza ki a kívánt eseményindítót az eseményindítók listából.

   -vagy-

   Meglévő Logic apps esetén az utolsó lépésben, amelyhez műveletet szeretne hozzáadni, válassza az **új lépés** lehetőséget. A keresőmezőbe írja be `sftp ssh` szűrőként a kifejezést. A műveletek listában válassza ki a kívánt műveletet.

   A lépések közötti művelet hozzáadásához vigye a mutatót a lépések közötti nyíl fölé. Válassza ki a **+** megjelenő pluszjelet (), majd válassza a **művelet hozzáadása** lehetőséget.

1. Adja meg a kapcsolathoz szükséges adatokat.

   > [!IMPORTANT]
   >
   > Ha megadja az SSH titkos kulcsát az **SSH titkos kulcs** tulajdonságában, kövesse ezeket a további lépéseket, amelyekkel biztosíthatja, hogy a tulajdonság teljes és megfelelő értékét adja meg. Érvénytelen kulcs miatt a kapcsolódás sikertelen lesz.

   Habár bármilyen szövegszerkesztőt használhat, az alábbi példákban bemutatjuk, hogyan lehet helyesen másolni és beilleszteni a kulcsot Notepad.exe példaként.

   1. Nyissa meg az SSH titkos kulcs fájlját egy szövegszerkesztőben. Ezek a lépések példaként használják a jegyzettömböt.

   1. A Jegyzettömb **Szerkesztés** menüjében válassza az **összes kijelölése** lehetőséget.

   1. Válassza **Edit** a  >  **Másolás** szerkesztése lehetőséget.

   1. Az SFTP-SSH-trigger vagy a hozzáadott művelet esetében illessze be a *teljes* kulcsot, amelyet a **titkos SSH-kulcs** tulajdonságba másolt, amely több sort is támogat.  **_Ügyeljen rá, hogy a kulcsot illessze be_* a kulcsba. _*_Ne adja meg manuálisan a kulcsot, vagy szerkessze_*_ azt.

1. Ha végzett a kapcsolat részleteinek megadásával, válassza a _ * létrehozás * * lehetőséget.

1. Most adja meg a kiválasztott trigger vagy művelet szükséges adatait, és folytassa a logikai alkalmazás munkafolyamatának összeállítását.

<a name="change-chunk-size"></a>

## <a name="override-chunk-size"></a>Adathalmaz méretének felülbírálása

A darabolást használó alapértelmezett adaptív működés felülbírálásához megadhat egy állandó adatméretet 5 MB és 50 MB között.

1. A művelet jobb felső sarkában válassza az ellipszisek gombot ( **...** ), majd válassza a **Beállítások** lehetőséget.

   ![Az SFTP-SSH beállítások megnyitása](./media/connectors-sftp-ssh/sftp-ssh-connector-setttings.png)

1. A **tartalom átvitele** elemnél, a **darab mérete** tulajdonságban adjon meg egy egész számot a `5` `50` következőből:, például: 

   ![Válassza ki a használni kívánt adatméretet](./media/connectors-sftp-ssh/specify-chunk-size-override-default.png)

1. Ha elkészült, válassza a **Kész** lehetőséget.

## <a name="examples"></a>Példák

<a name="file-added-modified"></a>

### <a name="sftp---ssh-trigger-when-a-file-is-added-or-modified"></a>SFTP-SSH-trigger: fájl hozzáadásakor vagy módosításakor

Ez az aktiválás egy logikai alkalmazás munkafolyamatát indítja el, amikor egy fájlt hozzáadnak vagy módosítanak egy SFTP-kiszolgálón. Hozzáadhat például egy olyan feltételt, amely ellenőrzi a fájl tartalmát, és beolvassa a tartalmat attól függően, hogy a tartalom megfelel-e a megadott feltételnek. Ezután hozzáadhat egy műveletet, amely beolvassa a fájl tartalmát, és az SFTP-kiszolgáló egy mappájába helyezi a tartalmat.

**Vállalati példa** : ezt az triggert használhatja az ügyfél-megrendeléseket képviselő új fájlok SFTP-mappájának figyelésére. Ezután használhat egy SFTP-műveletet, például a **fájlok beolvasása** lehetőséget, így a sorrend tartalmát megtekintve további feldolgozást hajthat végre, és a rendelést egy Orders adatbázisban tárolhatja.

<a name="get-content"></a>

### <a name="sftp---ssh-action-get-file-content-using-path"></a>SFTP-SSH művelet: fájl tartalmának beolvasása elérési út használatával

Ez a művelet lekérdezi a tartalmat egy SFTP-kiszolgálón lévő fájlból a fájl elérési útjának megadásával. Így például felveheti az triggert az előző példából, és egy olyan feltételt, amelynek meg kell felelnie a fájl tartalmának. Ha a feltétel igaz, akkor a tartalmat lekérdező művelet futtatható.

<a name="troubleshooting-errors"></a>

## <a name="troubleshoot-errors"></a>Hibák elhárítása

Ez a szakasz a gyakori hibák és problémák lehetséges megoldásait ismerteti.

<a name="file-does-not-exist"></a>

### <a name="404-error-a-reference-was-made-to-a-file-or-folder-which-does-not-exist"></a>404 hiba: "nem létező fájlra vagy mappára történt hivatkozás."

Ez a hiba akkor fordulhat elő, ha a logikai alkalmazás egy új fájlt hoz létre az SFTP-kiszolgálón az SFTP-SSH **create file** művelettel, de az újonnan létrehozott fájl azonnal át lesz helyezve, mielőtt a Logic Apps szolgáltatás beolvassa a fájl metaadatait. Amikor a logikai alkalmazás futtatja a **fájl létrehozása** műveletet, a Logic Apps szolgáltatás automatikusan meghívja az SFTP-kiszolgálót a fájl metaadatainak beolvasására. Ha azonban a fájl át lett helyezve, a Logic Apps szolgáltatás már nem találja a fájlt, így megkapja a `404` hibaüzenetet.

Ha nem tudja elkerülni vagy késleltetni a fájl áthelyezését, kihagyhatja a fájl metaadatainak olvasását a fájl létrehozása után, a következő lépések végrehajtásával:

1. A **fájl létrehozása** műveletben nyissa meg az **új paraméter hozzáadása** listát, válassza a **minden fájl metaadatainak beolvasása** tulajdonságot, és állítsa a **nem** értékre.

1. Ha később szüksége van erre a fájl-metaadatokra, használhatja a **fájl metaadatainak beolvasása** műveletet.

<a name="connection-attempt-failed"></a>

### <a name="504-error-a-connection-attempt-failed-because-the-connected-party-did-not-properly-respond-after-a-period-of-time-or-established-connection-failed-because-connected-host-has-failed-to-respond-or-request-to-the-sftp-server-has-taken-more-than-000030-seconds"></a>504 hiba: "A kapcsolódási kísérlet sikertelen volt, mert a csatlakoztatott fél nem válaszolt egy adott idő elteltével, vagy a kapcsolat létrejötte sikertelen volt, mert a csatlakoztatott gazdagép nem válaszolt a (z)" vagy "kérelemre az SFTP-kiszolgálónak több mint" 00:00:30 "másodpercet.

Ez a hiba akkor fordulhat elő, ha a logikai alkalmazás nem tud sikeresen kapcsolatot létesíteni az SFTP-kiszolgálóval. Ennek a problémának különböző okai lehetnek, ezért próbálkozzon a következő hibaelhárítási lehetőségekkel:

* A kapcsolat időkorlátja 20 másodperc. Győződjön meg arról, hogy az SFTP-kiszolgáló jó teljesítménnyel és köztes eszközökkel, például tűzfalakkal rendelkezik, és nem növeli a terhelést. 

* Ha beállított tűzfalat, győződjön meg arról, hogy hozzáadja a **felügyelt összekötő IP-** címeit a jóváhagyott listához. A logikai alkalmazás régiójának IP-címeinek megkereséséhez tekintse meg a [Azure Logic apps korlátai és konfigurálása](../logic-apps/logic-apps-limits-and-config.md#multi-tenant-azure---outbound-ip-addresses)című témakört.

* Ha ez a hiba időnként bekövetkezik, módosítsa az SFTP-SSH művelet **újrapróbálkozási házirend** -beállítását az újrapróbálkozások számára az alapértelmezett négy próbálkozásnál nagyobb értékre.

* Győződjön meg arról, hogy az SFTP-kiszolgáló korlátozza-e a kapcsolatok számát az egyes IP-címekről. Ha egy korlát létezik, lehet, hogy korlátozni kell az egyidejű Logic app-példányok számát.

* Ha csökkenteni szeretné a kapcsolatok létesítésének költségeit, az SFTP-kiszolgáló SSH-konfigurációjában növelje a [**ClientAliveInterval**](https://man.openbsd.org/sshd_config#ClientAliveInterval) tulajdonságot körülbelül egy óráig.

* Tekintse át az SFTP-kiszolgáló naplóját annak ellenőrzéséhez, hogy a logikai alkalmazástól érkező kérés elérte-e az SFTP-kiszolgálót. Ha további információkra van szüksége a kapcsolódási problémáról, akkor a tűzfalon és az SFTP-kiszolgálón is futtathat hálózati nyomkövetést.

## <a name="connector-reference"></a>Összekötő-referencia

Az összekötő részletes technikai részleteiről, például az eseményindítók, a műveletek és a korlátok az összekötő hencegő fájljában leírtak alapján: az [összekötő hivatkozási lapja](/connectors/sftpwithssh/).

> [!NOTE]
> Az [integrációs szolgáltatási környezet (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)logikai alkalmazásai esetében az összekötő ISE-címkével ellátott verziója adatdarabolást igényel az [ISE-üzenetek használatának korlátozásához](../logic-apps/logic-apps-limits-and-config.md#message-size-limits) .

## <a name="next-steps"></a>Következő lépések

* További Logic Apps- [Összekötők](../connectors/apis-list.md) megismerése
