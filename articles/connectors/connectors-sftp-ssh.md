---
title: Csatlakozás SFTP-kiszolgálóhoz SSH-val
description: SFTP-kiszolgáló fájljait figyelő, létrehozására, kezelésére, elküldére és fogadására vonatkozó feladatok automatizálása SSH és Azure Logic Apps
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.reviewer: estfan, logicappspm, azla
ms.topic: conceptual
ms.date: 04/19/2021
tags: connectors
ms.openlocfilehash: a19253e117f748b4d4045bfd2a29552018bba91e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781558"
---
# <a name="create-and-manage-sftp-files-using-ssh-and-azure-logic-apps"></a>SFTP-fájlok létrehozása és kezelése SSH és Azure Logic Apps

A [Secure File Transfer Protocol-kiszolgálón (SFTP)](https://www.ssh.com/ssh/sftp/) fájlokat a [Secure Shell (SSH)](https://www.ssh.com/ssh/protocol/) protokoll használatával automatizáló feladatok automatizálásához az Azure Logic Apps és az SFTP-SSH-összekötő használatával hozhat létre automatizált integrációs munkafolyamatokat. Az SFTP olyan hálózati protokoll, amely fájlhozzáférést, fájlátvitelt és fájlfelügyeletet biztosít valamilyen megbízható adatstreamen keresztül.

Íme néhány példa a automatizálható feladatokra:

* Figyelheti, hogy mikor adnak hozzá vagy módosítottak fájlokat.
* Fájlok lekérte, létrehozhatja, másolhatja, átnevezheti, frissítheti, listázhatja és törölheti a fájlokat.
* Mappák létrehozása.
* Fájltartalom és metaadatok lekérte.
* Bontsa ki az archívumokat a mappákba.

A munkafolyamatban használhat egy eseményindítót, amely figyeli az SFTP-kiszolgálón található eseményeket, és elérhetővé teszi a kimenetet más műveletek számára. Ezután műveletek használatával különböző feladatokat hajthat végre az SFTP-kiszolgálón. Egyéb műveleteket is használhat, amelyek az SFTP-SSH-műveletek kimenetét használják. Ha például rendszeresen lekér fájlokat az SFTP-kiszolgálóról, az Office 365 Outlook-összekötő vagy az Outlook.com használatával e-mailes riasztásokat küldhet a fájlokról és azok tartalmáról. Ha még nem tudja a logikai alkalmazásokat, tekintse át a [Mi a Azure Logic Apps?](../logic-apps/logic-apps-overview.md)

Az SFTP-SSH-összekötő és az SFTP-összekötő közötti különbségekért tekintse át az [SFTP-SSH és az SFTP](#comparison) összehasonlítása című szakaszt a témakör későbbi részében.

## <a name="limits"></a>Korlátok

* Az SFTP-SSH-összekötő jelenleg nem támogatja ezeket az SFTP-kiszolgálókat:

  * IBM DataPower
  * MessageWay
  * OpenText Secure MFT
  * OpenText GXS

* Az adattömbök feldarabolását támogató SFTP-SSH-műveletek legfeljebb 1 GB méretű fájlokat kezelnek, az adattömbök ledarabolását nem támogató SFTP-SSH-műveletek pedig legfeljebb 50 MB méretű fájlokat kezelnek. [](../logic-apps/logic-apps-handle-large-messages.md) Az alapértelmezett adattömbméret 15 MB. Ez a méret azonban dinamikusan változhat, 5 MB-tól kezdve a maximális 50 MB-os méretig. A dinamikus méretezés olyan tényezőktől függ, mint a hálózati késés, a kiszolgáló válaszidejéhez és hasonlók.

  > [!NOTE]
  > Az integrációs szolgáltatási [környezetben (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)található logikai alkalmazások esetében az összekötő ISE-címkével ellátott verziójához adattömbökre van szükség az ISE üzenetkorlátok [használatához.](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)

  Ezt az adaptív viselkedést felülírhatja, ha állandó [adattömbméretet](#change-chunk-size) ad meg helyette. Ez a méret 5 MB és 50 MB között lehet. Tegyük fel például, hogy van egy 45 MB-os fájlja és egy hálózata, amely támogatja ezt a fájlméretet késés nélkül. Az adaptív adattömbösedés több hívást, és nem egyetlen hívást ad vissza. A hívások számának csökkentéséhez próbáljon meg 50 MB-os adattömbméretet megszűkülni. Ha a logikai alkalmazás például 15 MB-os adattömbök használata esetén időkorlikációt használ, akkor megpróbálhatja 5 MB-osra csökkenteni a méretet.

  Az adattömbméret egy kapcsolathoz van társítva. Ez az attribútum azt jelenti, hogy ugyanazt a kapcsolatot használhatja az adattömbök és az adattömbök használatát nem támogató műveletek esetén is. Ebben az esetben a darabolást nem támogató műveletek adattömbmérete 5 MB és 50 MB között lehet. Ez a táblázat azt mutatja be, hogy mely SFTP-SSH-műveletek támogatják az adattömbök ledarabolását:

  | Művelet | Adattömbök támogatása | Adattömbméret-támogatás felülbírálása |
  |--------|------------------|-----------------------------|
  | **Fájl másolása** | No | Nem alkalmazható |
  | **Fájl létrehozása** | Igen | Yes |
  | **Mappa létrehozása** | Nem alkalmazható | Nem alkalmazható |
  | **Fájl törlése** | Nem alkalmazható | Nem alkalmazható |
  | **Archívum kinyerése mappába** | Nem alkalmazható | Nem alkalmazható |
  | **Fájltartalom lekérte** | Igen | Yes |
  | **Fájltartalom lekérte elérési út használatával** | Igen | Yes |
  | **Fájlmetaadatok lekérte** | Nem alkalmazható | Nem alkalmazható |
  | **Fájlmetaadatok lekérte elérési út használatával** | Nem alkalmazható | Nem alkalmazható |
  | **A mappában található fájlok listása** | Nem alkalmazható | Nem alkalmazható |
  | **Fájl átnevezése** | Nem alkalmazható | Nem alkalmazható |
  | **Fájl frissítése** | No | Nem alkalmazható |
  ||||

* Az SFTP-SSH-eseményindítók nem támogatják az üzenettömbök feldarabolását. Fájltartalom igénylésekor az eseményindítók csak a 15 MB vagy annál kisebb fájlokat választják ki. 15 MB-nál nagyobb fájlok lekért ehelyett kövesse az alábbi mintát:

  1. Olyan SFTP-SSH-eseményindítót használjon, amely csak fájltulajdonságokat ad vissza. Ezeknek az eseményindítóknak a neve tartalmazza a leírást **(csak tulajdonságok).**

  1. Kövesse az eseményindítót az SFTP-SSH **Get file content műveletben.** Ez a művelet beolvassa a teljes fájlt, és implicit módon üzenetek darabolását használja.

<a name="comparison"></a>

## <a name="compare-sftp-ssh-versus-sftp"></a>Az SFTP-SSH és az SFTP összehasonlítása

Az alábbi lista az SFTP-SSH-összekötőtől eltérő fő SFTP-SSH-képességeket ismerteti:

* A SSH.NET [kódtárat](https://github.com/sshnet/SSH.NET)használja, amely egy .NET-et támogató nyílt forráskódú Secure Shell- (SSH-) kódtár.

* A **Mappa létrehozása műveletet adja** meg, amely egy mappát hoz létre az SFTP-kiszolgálón megadott elérési úton.

* A Fájl **átnevezése** műveletet biztosítja, amely átnevez egy fájlt az SFTP-kiszolgálón.

* Legfeljebb 1 óráig gyorsítótárazza a kapcsolatot az *SFTP-kiszolgálóval.* Ez a képesség javítja a teljesítményt, és csökkenti, hogy az összekötő milyen gyakran próbál csatlakozni a kiszolgálóhoz. A gyorsítótárazás időtartamának beállításához szerkessze a [ **ClientAliveInterval**](https://man.openbsd.org/sshd_config#ClientAliveInterval) tulajdonságot az SSH-konfigurációban az SFTP-kiszolgálón.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* Az SFTP-kiszolgáló címe és a fiók hitelesítő adatai, hogy a munkafolyamat hozzáférjen az SFTP-fiókhoz. Egy titkos SSH-kulcshoz és az SSH titkos kulcs jelszavához is hozzá kell férni. Nagy fájlok adattömbök használatával való feltöltéséhez olvasási és írási hozzáférésre is szüksége lesz az SFTP-kiszolgálón található gyökérmappában. Ellenkező esetben "401 – Jogosulatlan" hibaüzenet jelenik meg.

  Az SFTP-SSH-összekötő a titkos kulcsos hitelesítést és a jelszó-hitelesítést is támogatja. Az SFTP-SSH-összekötő azonban csak ezeket *a* titkos kulcsformátumokat, algoritmusokat és ujjlenyomatokat támogatja:

  * **Titkos kulcsformátumok:** OpenSSH- és ssh.com RSA- és DSA- (digitális aláírási algoritmus) kulcsok. Ha a titkos kulcs PuTTY (.ppk) fájlformátumban van, először konvertálja a kulcsot [OpenSSH (.pem) fájlformátumra.](#convert-to-openssh)
  * **Titkosítási algoritmusok:** DES-EDE3-CBC, DES-EDE3-CFB, DES-CBC, AES-128-CBC, AES-192-CBC és AES-256-CBC
  * **Ujjlenyomat:** MD5

  Miután hozzáadta az SFTP-SSH-eseményindítót vagy -műveletet a munkafolyamathoz, meg kell adnia az SFTP-kiszolgáló kapcsolati adatait. Ha ehhez a kapcsolathoz adja meg a titkos SSH-kulcsot,***** ne adja meg vagy szerkessze manuálisan a _kulcsot, ami a kapcsolat meghiúsulhat. Ehelyett győződjön meg _**_ arról, hogy kimásolta a kulcsot a titkos SSH-kulcsfájlból, és *_illessze_* be a * kulcsot a kapcsolat részleteibe. További információt a cikk későbbi, Csatlakozás [SFTP-hez SSH-val](#connect) című szakaszában talál.

* Alapszintű ismeretek a [logikai alkalmazások létrehozásáról](../logic-apps/quickstart-create-first-logic-app-workflow.md)

* A logikai alkalmazás munkafolyamata, ahol hozzá szeretne férni az SFTP-fiókjához. Az SFTP-SSH-eseményindítóval való kezdéshez hozzon létre [egy üres logikaialkalmazás-munkafolyamatot.](../logic-apps/quickstart-create-first-logic-app-workflow.md) SFTP-SSH-művelethez indítsa el a munkafolyamatot egy másik eseményindítóval, például az **Ismétlődés eseményindítóval.**

## <a name="how-sftp-ssh-triggers-work"></a>Az SFTP-SSH-triggerek működése

<a name="polling-behavior"></a>

### <a name="polling-behavior"></a>Lekérdezési viselkedés

Az SFTP-SSH-eseményindítók lekérdezik az SFTP fájlrendszert, és az utolsó lekérdezés óta módosult fájlokat keresnek. Egyes eszközökkel megőrizze a fájlok módosításakor szükséges időbélyeget. Ezekben az esetekben le kell tiltania ezt a funkciót, hogy az eseményindító működjön. Íme néhány gyakori beállítás:

| SFTP-ügyfél | Művelet |
|-------------|--------|
| Megnyerő | Ugrás a Beállítások **beállítások**  >  **átvitele**  >    >  **Szerkesztés**  >  **megőrzése időbélyeg letiltása**  >  **elemre** |
| Filezilla | Ugrás az **Átvitt**  >  **fájlok időbélyegének megőrzése Letiltás**  >  **beállításra** |
|||

Amikor egy eseményindító új fájlt talál, az eseményindító ellenőrzi, hogy az új fájl elkészült-e, és nem részlegesen van-e megírva. Előfordulhat például, hogy egy fájl módosításai folyamatban vannak, amikor az eseményindító ellenőrzi a fájlkiszolgálót. A részlegesen megírt fájlok visszaküldésének elkerülése érdekében az eseményindító megjegyzéseket ad a fájl időbélyegzőjére, amely legutóbbi módosításokat végzett, de nem adja vissza azonnal ezt a fájlt. Az eseményindító csak akkor adja vissza a fájlt, amikor ismét lekérdezi a kiszolgálót. Előfordulhat, hogy ez a viselkedés olyan késést okozhat, amely akár kétszer annyi is lehet, mint az eseményindító lekérdezési időköze.

<a name="trigger-recurrence-shift-drift"></a>

### <a name="trigger-recurrence-shift-and-drift"></a>Ismétlődési váltás és eltérés kiváltása

A kapcsolatalapú eseményindítók, amelyekben először létre kell hoznia egy kapcsolatot , például az SFTP-SSH-eseményindító, eltérnek a natív módon a Azure Logic Apps-ben futó beépített eseményindítóktól, például az Ismétlődés [eseményindítótól.](../connectors/connectors-native-recurrence.md) Az ismétlődő kapcsolatalapú eseményindítókban nem az ismétlődési ütemezés az egyetlen olyan illesztő, amely szabályozza a végrehajtást, és az időzóna csak a kezdeti kezdési időt határozza meg. A további futtatásokat az ismétlődés ütemezése, az eseményindító utolsó végrehajtása *és* egyéb tényezők befolyásolják, amelyek miatt a futási idők eltatolnak vagy váratlan viselkedést okozhatnak. A váratlan viselkedés például a megadott ütemezés fenntartásának sikertelenségére is vezethet, amikor a nyári időszámítás (DST) elindul és véget ér. Annak érdekében, hogy az ismétlődési idő ne váltsa el a DST effektusát, manuálisan módosítsa az ismétlődést. Így a munkafolyamat továbbra is a várt időpontban fog futni. Ellenkező esetben a kezdési idő egy órával előretul, amikor a DST elindul, és egy órával visszafelé, amikor a DST véget ér. További információ: [Ismétlődés kapcsolatalapú eseményindítók esetén.](../connectors/apis-list.md#recurrence-for-connection-based-triggers)

<a name="convert-to-openssh"></a>

## <a name="convert-putty-based-key-to-openssh"></a>PuTTY-alapú kulcs konvertálása OpenSSH-vá

A PuTTY és az OpenSSH formátum különböző fájlnévkiterjesztéseket használ. A PuTTY formátum a .ppk vagy PuTTY titkos kulcsot, a fájlnévkiterjesztést használja. Az OpenSSH formátum a .pem vagy Privacy Enhanced Mail fájlnévkiterjesztést használja. Ha a titkos kulcs PuTTY formátumú, és OpenSSH formátumot kell használnia, először konvertálja a kulcsot OpenSSH formátumba az alábbi lépésekkel:

### <a name="unix-based-os"></a>Unix-alapú operációs rendszer

1. Ha a PuTTY-eszközök még nincsenek telepítve a rendszeren, tegye meg most, például:

   `sudo apt-get install -y putty`

1. Futtassa ezt a parancsot, amely létrehoz egy fájlt, amelyet az SFTP-SSH-összekötővel használhat:

   `puttygen <path-to-private-key-file-in-PuTTY-format> -O private-openssh -o <path-to-private-key-file-in-OpenSSH-format>`

   Például:

   `puttygen /tmp/sftp/my-private-key-putty.ppk -O private-openssh -o /tmp/sftp/my-private-key-openssh.pem`

### <a name="windows-os"></a>Windows operációs rendszer

1. Ha még nem tette meg, töltse le a [legújabb PuTTY Generator (puttygen.exe)](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)eszközt, majd indítsa el az eszközt.

1. Ezen a képernyőn válassza a Betöltés **lehetőséget.**

   ![Válassza a "Betöltés" lehetőséget](./media/connectors-sftp-ssh/puttygen-load.png)

1. Keresse meg a titkos kulcsfájlt PuTTY formátumban, és válassza a **Megnyitás lehetőséget.**

1. A **Konverziók menüben** válassza az **OpenSSH-kulcs exportálása lehetőséget.**

   ![Válassza az "OpenSSH-kulcs exportálása" lehetőséget](./media/connectors-sftp-ssh/export-openssh-key.png)

1. Mentse a titkos kulcsfájlt `.pem` fájlnévkiterjesztéssel.

## <a name="considerations"></a>Megfontolandó szempontok

Ez a szakasz az összekötő eseményindítóinak és műveletének használata során megfontolandó szempontokat ismerteti.

<a name="different-folders-trigger-processing-file-storage"></a>

### <a name="use-different-sftp-folders-for-file-upload-and-processing"></a>Különböző SFTP-mappák használata fájlfeltöltéshez és -feldolgozáshoz

Az SFTP-kiszolgálón használjon külön mappákat a feltöltött fájlok tárolásához, valamint az eseményindítót a fájlok feldolgozásának monitorzához. Ellenkező esetben az eseményindító nem aktiválódik, és kiszámíthatatlan módon viselkedik, például kihagyja az eseményindító által feldolgozható fájlok véletlenszerű számát. Ez a követelmény azonban azt jelenti, hogy a fájlokat át kell áthelyezni ezek között a mappák között. 

Ha ez az eseményindító probléma történik, távolítsa el a fájlokat az eseményindító által figyelt mappából, és használjon egy másik mappát a feltöltött fájlok tárolására.

<a name="create-file"></a>

### <a name="create-file"></a>Fájl létrehozása

Fájl SFTP-kiszolgálón való létrehozásához használhatja az SFTP-SSH **Fájl létrehozása műveletet.** Amikor ez a művelet létrehozza a fájlt, a Logic Apps szolgáltatás automatikusan az SFTP-kiszolgálót is hívja a fájl metaadatainak lehívása érdekében. Ha azonban az újonnan létrehozott fájlt azelőtt áthelyezi, hogy a Logic Apps szolgáltatás hívással le tudja hívni a metaadatokat, a következő hibaüzenet `404` jelenik meg: `'A reference was made to a file or folder which does not exist'` . Ha ki szeretne hagyni egy fájl metaadatainak olvasását a fájl létrehozása után, kövesse a Fájl metaadatainak beolvasása tulajdonság hozzáadásának lépéseit, és állítsa a Get all file metadata (Az összes fájl metaadatainak beolvasása) [ **tulajdonságot No (Nem) beállításra.**](#file-does-not-exist)

<a name="connect"></a>

## <a name="connect-to-sftp-with-ssh"></a>Csatlakozás SFTP-hez SSH-val

[!INCLUDE [Create connection general intro](../../includes/connectors-create-connection-general-intro.md)]

1. Jelentkezzen be a [Azure Portal,](https://portal.azure.com)és nyissa meg a logikai alkalmazást a Logic App Designerben, ha még nincs megnyitva.

1. Üres logikai alkalmazások esetén a keresőmezőbe írja be `sftp ssh` szűrőként a következőt: . Az eseményindítók listájában válassza ki a kívánt eseményindítót.

   -vagy-

   Meglévő logikai alkalmazások esetén a művelet hozzáadásának utolsó lépése alatt válassza az Új **lépés lehetőséget.** A keresőmezőbe írja be `sftp ssh` szűrőként a következőt: . A műveletek listájában válassza ki a kívánt műveletet.

   Ha műveletet szeretne hozzáadni a lépések között, helyezze a mutatót a lépések közötti nyíl fölé. Válassza a megjelenő pluszjelet ( **+** ), majd a Művelet hozzáadása **lehetőséget.**

1. Adja meg a kapcsolathoz szükséges adatokat.

   > [!IMPORTANT]
   >
   > Amikor beírja a titkos SSH-kulcsot az **SSH** titkos kulcs tulajdonságba, kövesse ezeket a további lépéseket, amelyek segítségével megadhatja a tulajdonság teljes és helyes értékét. Érvénytelen kulcs esetén a kapcsolat sikertelen lesz.

   Bár bármilyen szövegszerkesztőt használhat, íme néhány példa a kulcs megfelelő másolására és beillesztésre példaként Notepad.exe lépésekkel.

   1. Nyissa meg a titkos SSH-kulcsfájlt egy szövegszerkesztőben. Ezek a lépések példaként a Jegyzettömböt használják.

   1. A Jegyzettömb **szerkesztési menüjében** válassza az **Összes kijelölése lehetőséget.**

   1. Válassza a **Másolás**  >  **szerkesztése lehetőséget.**

   1. Az SFTP-SSH-eseményindítóban  vagy -műveletben illessze be a teljes másolt kulcsot a több sort támogató **titkos SSH-kulcs** tulajdonságba. **_Ne adja meg vagy szerkessze manuálisan a kulcsot._**

1. A kapcsolati adatok megadása után válassza a **Létrehozás lehetőséget.**

1. Most adja meg a kiválasztott eseményindító vagy művelet szükséges adatait, és folytassa a logikai alkalmazás munkafolyamatának építését.

<a name="change-chunk-size"></a>

## <a name="override-chunk-size"></a>Adattömbméret felülbírálása

Az adattömbök által használt alapértelmezett adaptív viselkedés felülbírálásaként megadhat egy állandó adattömbméretet 5 MB és 50 MB között.

1. A művelet jobb felső sarkában válassza a három pont **(...**) gombot, majd a **Beállítások lehetőséget.**

   ![SFTP-SSH-beállítások megnyitása](./media/connectors-sftp-ssh/sftp-ssh-connector-setttings.png)

1. A **Content Transfer (Tartalomátvitel)** alatt a Chunk size (Adattömbméret) tulajdonságban adjon meg egy egész értéket a értéktől a  `5` -hez, `50` például: 

   ![A használni kívánt adattömbméret megadása](./media/connectors-sftp-ssh/specify-chunk-size-override-default.png)

1. Ha végzett, válassza a Kész **lehetőséget.**

## <a name="examples"></a>Példák

<a name="file-added-modified"></a>

### <a name="sftp---ssh-trigger-when-a-file-is-added-or-modified"></a>SFTP – SSH-eseményindító: Fájl hozzáadásakor vagy módosításakor

Ez az eseményindító elindít egy munkafolyamatot, amikor egy fájlt adnak hozzá vagy megváltoztatnak egy SFTP-kiszolgálón. Követő műveletekként a munkafolyamat egy feltétellel ellenőrizheti, hogy a fájl tartalma megfelel-e a megadott feltételeknek. Ha a tartalom megfelel a feltételnek, a **Get file content** SFTP-SSH művelet le tudja szerezni a tartalmat, majd egy másik SFTP-SSH művelet a fájlt az SFTP-kiszolgáló egy másik mappájába tudja tenni.

**Vállalati példa:** Ezzel az eseményindítóval figyelheti az SFTP-mappákban az ügyfélrendeléseket képviselő új fájlokat. Ezután használhatja az SFTP-SSH-műveletet, például a **Fájl** tartalmának lekért műveletét, hogy a rendelés tartalmát további feldolgozás céljából le tudja kapni, és a rendelést egy rendelési adatbázisban tárolja.

<a name="get-content"></a>

### <a name="sftp---ssh-action-get-file-content-using-path"></a>SFTP – SSH-művelet: Fájltartalom lekérte az elérési út használatával

Ez a művelet lekérte a tartalmat egy SFTP-kiszolgálón található fájlból a fájl elérési útjának megadásával. Így például hozzáadhatja az előző példában található eseményindítót és egy feltételt, amely szerint a fájl tartalmának meg kell felelnie. Ha a feltétel igaz, futtatható a tartalmat lekért művelet.

<a name="troubleshooting-errors"></a>

## <a name="troubleshoot-problems"></a>Problémák elhárítása

Ez a szakasz a gyakori hibák vagy problémák lehetséges megoldásait ismerteti.

<a name="connection-attempt-failed"></a>

### <a name="504-error-a-connection-attempt-failed-because-the-connected-party-did-not-properly-respond-after-a-period-of-time-or-established-connection-failed-because-connected-host-has-failed-to-respond-or-request-to-the-sftp-server-has-taken-more-than-000030-seconds"></a>504-es hiba: "A csatlakozási kísérlet meghiúsult, mert a csatlakoztatott fél egy idő elteltével nem válaszolt megfelelően, vagy a létrehozott kapcsolat meghiúsult, mert a csatlakoztatott gazdagép nem válaszolt" vagy "Az SFTP-kiszolgálóra vonatkozó kérés több mint 00:00:30 másodpercet vett igénybe"

Ez a hiba akkor fordulhat elő, ha a logikai alkalmazás nem tud sikeresen kapcsolatot létesíteni az SFTP-kiszolgálóval. Ennek a problémának több oka is lehet, ezért próbálkozzon az alábbi hibaelhárítási lehetőségekkel:

* A kapcsolat időkorlátja 20 másodperc. Ellenőrizze, hogy az SFTP-kiszolgáló jó teljesítménnyel rendelkezik-e, és hogy a köztes eszközök, például a tűzfalak nem eredményeznek többletterhelést. 

* Ha be van állítva tűzfal, győződjön  meg arról, hogy hozzáadta a felügyelt összekötő IP-címeit a jóváhagyott listához. A logikai alkalmazás régiójának IP-címeit a Következő cikk tartalmazza: A logikai alkalmazás [Azure Logic Apps.](../logic-apps/logic-apps-limits-and-config.md#multi-tenant-azure---outbound-ip-addresses)

* Ha ez a hiba időszakosan  fordul elő, módosítsa az SFTP-SSH művelet Újrapróbálkozási házirend beállítását az alapértelmezett négy újrapróbálkozási számnál magasabbra.

* Ellenőrizze, hogy az SFTP-kiszolgáló korlátozza-e az egyes IP-címekről származó kapcsolatok számát. Ha létezik korlát, előfordulhat, hogy korlátoznia kell az egyidejű logikaialkalmazás-példányok számát.

* A kapcsolat létesítés költségeinek csökkentése érdekében az SFTP-kiszolgáló SSH-konfigurációjában növelje a [**ClientAliveInterval**](https://man.openbsd.org/sshd_config#ClientAliveInterval) tulajdonságot körülbelül egy órára.

* Tekintse át az SFTP-kiszolgáló naplóját, és ellenőrizze, hogy a logikai alkalmazás kérése elérte-e az SFTP-kiszolgálót. A csatlakozási problémával kapcsolatos további információkért futtatassa a hálózati nyomkövetést a tűzfalon és az SFTP-kiszolgálón.

<a name="file-does-not-exist"></a>

### <a name="404-error-a-reference-was-made-to-a-file-or-folder-which-does-not-exist"></a>404-es hiba: "Nem létező fájlra vagy mappára hivatkoznak"

Ez a hiba akkor fordulhat elő, ha a munkafolyamat létrehoz egy fájlt az SFTP-kiszolgálón az SFTP-SSH **Fájl** létrehozása művelettel, de azonnal áthelyezi a fájlt, mielőtt az Logic Apps szolgáltatás le tudja szerezni a fájl metaadatait. Amikor a munkafolyamat futtatja a **Fájl** létrehozása műveletet, a Logic Apps szolgáltatás automatikusan hívja meg az SFTP-kiszolgálót a fájl metaadatainak lehívásához. Ha azonban a logikai alkalmazás áthelyezi a fájlt, Logic Apps szolgáltatás már nem találja a fájlt, ezért `404` hibaüzenetet kap.

Ha nem tudja elkerülni vagy késleltetni a fájl áthelyezését, a fájl létrehozása után kihagyhatja a fájl metaadatainak olvasását az alábbi lépések helyett:

1. A **Fájl létrehozása műveletben** nyissa meg az **Add new parameter (Új** paraméter hozzáadása) listát, válassza a **Get all file metadata** (Az összes fájl metaadatának be lekérdezése) tulajdonságot, és állítsa a No (Nem) **értékre.**

1. Ha később szüksége lesz a fájl metaadataira, használhatja a **Fájl metaadatainak le szolgáltatását.**

## <a name="connector-reference"></a>Összekötő-referencia

Az összekötővel kapcsolatos további technikai részletekért, például az eseményindítókról, műveletekről és az összekötő Swagger-fájljában leírt korlátokról lásd az összekötő [referenciaoldalát.](/connectors/sftpwithssh/)

> [!NOTE]
> Az integrációs szolgáltatási [környezetben (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)található logikai alkalmazások esetében az összekötő ISE-címkével ellátott verziója adattömbökre van szükség az [ISE üzenetkorlátok használatára.](../logic-apps/logic-apps-limits-and-config.md#message-size-limits)

## <a name="next-steps"></a>Következő lépések

* További tudnivalók az egyéb [Logic Apps összekötőkről](../connectors/apis-list.md)
