---
title: Azure CDN-eszközök védelme jogkivonat-hitelesítéssel | Microsoft Docs
description: Megtudhatja, hogyan használható a jogkivonat-hitelesítés a Azure CDN-eszközökhöz való hozzáférés biztonságossá tételéhez.
services: cdn
documentationcenter: .net
author: zhangmanling
manager: zhangmanling
editor: ''
ms.assetid: 837018e3-03e6-4f9c-a23e-4b63d5707a64
ms.service: azure-cdn
ms.devlang: multiple
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 11/17/2017
ms.author: mazha
ms.openlocfilehash: 21ef06f37e6840df08b1477f9c0ff24f6e15d1a3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "95978037"
---
# <a name="securing-azure-cdn-assets-with-token-authentication"></a>Azure CDN-eszközök védelme jogkivonat-hitelesítéssel

[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Áttekintés

A jogkivonat-hitelesítés lehetővé teszi, hogy megakadályozza az Azure Content Delivery Network (CDN) számára az eszközök jogosulatlan ügyfelek számára történő kiszolgálását. A jogkivonat-hitelesítés általában a *hotlinking* megelőzése érdekében történik, amelyben egy másik webhely, például egy üzenőfal, engedély nélkül használja az eszközöket. A hotlinking hatással lehet a tartalom kézbesítési költségeire. Ha engedélyezi a jogkivonatok hitelesítését a CDN-ben, a kérelmeket a CDN Edge Server hitelesíti, mielőtt a CDN kézbesíti a tartalmat. 

## <a name="how-it-works"></a>Működés

A jogkivonat-hitelesítés azt ellenőrzi, hogy a kérelmeket egy megbízható hely hozza-e létre, mert a kérelmeknek tartalmaznia kell egy olyan jogkivonat-értéket, amely a kérelmezőnek kódolt adatokat tárol. A rendszer csak akkor kézbesíti a tartalmat a kérelmezőnek, ha a kódolt információk megfelelnek a követelményeknek. Ellenkező esetben a rendszer megtagadja a kérelmeket. A követelményeket a következő paraméterek közül egy vagy több használatával állíthatja be:

- Ország/régió: az [ország/régió kódja](/previous-versions/azure/mt761717(v=azure.100))által meghatározott országokból/régiókból származó kérelmek engedélyezése vagy megtagadása.
- URL: csak a megadott objektumnak vagy elérési útnak megfelelő kérelmek engedélyezése.
- Gazdagép: engedélyezheti vagy megtagadhatja a megadott gazdagépeket használó kérelmeket a kérelem fejlécében.
- Hivatkozó: a megadott hivatkozótól érkező kérés engedélyezése vagy elutasítása.
- IP-cím: csak adott IP-címről vagy IP-alhálózatból származó kérelmek engedélyezése.
- Protokoll: kérelmek engedélyezése vagy megtagadása a tartalom kéréséhez használt protokoll alapján.
- Lejárati idő: rendeljen egy dátumot és egy időszakot annak biztosításához, hogy a hivatkozás csak korlátozott ideig érvényes maradjon.

További információkért tekintse meg a [jogkivonat-hitelesítés beállításával](#setting-up-token-authentication)kapcsolatos részletes konfigurációs példákat az egyes paraméterekhez.

>[!IMPORTANT] 
> Ha a jogkivonat-hitelesítés engedélyezve van a fiók bármely elérési útjához, a szabványos gyorsítótárazási mód az egyetlen mód, amely a lekérdezési karakterláncok gyorsítótárazásához használható. További információkért lásd: [Az Azure CDN gyorsítótárazási viselkedésének vezérlése lekérdezési sztringekkel](cdn-query-string-premium.md).

## <a name="reference-architecture"></a>Referenciaarchitektúra

A következő munkafolyamat-diagram azt ismerteti, hogyan használja a CDN a jogkivonat-hitelesítést a webalkalmazással való együttműködéshez.

![CDN-jogkivonat hitelesítési munkafolyamata](./media/cdn-token-auth/cdn-token-auth-workflow2.png)

## <a name="token-validation-logic-on-cdn-endpoint"></a>Jogkivonat-ellenőrzési logika a CDN-végponton
    
A következő folyamatábra azt ismerteti, hogyan Azure CDN érvényesít egy ügyfél-kérelmet, ha a jogkivonat-hitelesítés a CDN-végponton van konfigurálva.

![CDN-jogkivonat érvényesítési logikája](./media/cdn-token-auth/cdn-token-auth-validation-logic.png)

## <a name="setting-up-token-authentication"></a>Jogkivonat-hitelesítés beállítása

1. A [Azure Portal](https://portal.azure.com)keresse meg a CDN-profilját, majd válassza a **kezelés** lehetőséget a kiegészítő portál elindításához.

    ![CDN-profil kezelése gomb](./media/cdn-token-auth/cdn-manage-btn.png)

2. Vigye a kurzort a **nagyméretű http**-n keresztül, majd válassza a **jogkivonat-hitelesítés** elemet a menüben. A titkosítási kulcs és a titkosítási paraméterek a következőképpen állíthatók be:

   1. Hozzon létre egy vagy több titkosítási kulcsot. A titkosítási kulcs megkülönbözteti a kis-és nagybetűket, és alfanumerikus karakterek tetszőleges kombinációját tartalmazhatja. Bármilyen más típusú karakter, beleértve a szóközöket, nem megengedett. A maximális hossz 250 karakter. Annak érdekében, hogy a titkosítási kulcsok véletlenszerűek legyenek, azt javasoljuk, hogy az [OpenSSL eszköz](https://www.openssl.org/)használatával hozza létre őket. 

      Az OpenSSL eszköz szintaxisa a következő:

      ```rand -hex <key length>```

      Például:

      ```OpenSSL> rand -hex 32``` 

      Az állásidő elkerüléséhez hozzon létre egy elsődleges és egy biztonsági mentési kulcsot is. A biztonsági mentési kulcs folyamatos hozzáférést biztosít a tartalomhoz az elsődleges kulcs frissítésekor.
    
   2. Adjon meg egy egyedi titkosítási kulcsot az **elsődleges kulcs** mezőben, és szükség esetén adjon meg egy biztonsági mentési kulcsot a **biztonsági mentési kulcs** mezőben.

   3. Válassza ki az egyes kulcsok minimális titkosítási verzióját a **titkosítási verziók minimális** listájáról, majd válassza a **frissítés** elemet:
      - **V2**: azt jelzi, hogy a kulcs a 2,0-es és 3,0-es verziójú tokenek előállítására használható. Ezt a lehetőséget csak akkor használja, ha örökölt 2,0-es verziójú titkosítási kulcsról 3,0-es verzióra való áttérést végez.
      - **V3**: (ajánlott) azt jelzi, hogy a kulcs csak 3,0-es verziójú tokenek létrehozásához használható.

      ![CDN-jogkivonat hitelesítési kulcsának beállítása](./media/cdn-token-auth/cdn-token-auth-setupkey.png)
    
   4. A titkosítás eszköz használatával állítsa be a titkosítási paramétereket, és hozzon létre egy jogkivonatot. A titkosítás eszközzel engedélyezheti vagy megtagadhatja a kérelmeket a lejárati idő, az ország/régió, a hivatkozó, a protokoll és az ügyfél IP-címe (bármilyen kombinációban) alapján. Bár a tokenek létrehozásához használható paraméterek száma és kombinációja nincs korlátozva, a jogkivonat teljes hossza 512 karakterre van korlátozva. 

      ![CDN-titkosítás eszköz](./media/cdn-token-auth/cdn-token-auth-encrypttool.png)

      Adja meg a következő titkosítási paraméterek egy vagy több értékét a **titkosító eszköz** szakaszban: 

      > [!div class="mx-tdCol2BreakAll"] 
      > <table>
      > <tr>
      >   <th>Paraméter neve</th> 
      >   <th>Leírás</th>
      > </tr>
      > <tr>
      >    <td><b>ec_expire</b></td>
      >    <td>Lejárati időt rendel egy jogkivonathoz, amely után a jogkivonat lejár. A lejárati idő lejárta után küldött kérések megtagadva. Ez a paraméter egy UNIX-időbélyeget használ, amely a normál UNIX-kor óta eltelt másodpercek számától függ `1/1/1970 00:00:00 GMT` . (A standard idő és a Unix-idő közötti váltáshoz online eszközöket használhat.)> 
      >    Ha például azt szeretné, hogy a token lejárjon `12/31/2016 12:00:00 GMT` , adja meg a Unix timestamp értékét `1483185600` . 
      > </tr>
      > <tr>
      >    <td><b>ec_url_allow</b></td> 
      >    <td>Lehetővé teszi a tokenek egy adott objektumra vagy elérési útra való személyre szabását. Korlátozza a hozzáférést azokhoz a kérelmekhez, amelyek URL-címe egy adott relatív elérési úttal kezdődik. Az URL-címek kis-és nagybetűket érintenek. Több elérési utat is megadhat, ha az egyes elérési utakat vesszővel választja el. ne adjon hozzá szóközt. A követelményektől függően különböző értékeket állíthat be a különböző szintű hozzáférés biztosításához.> 
      >    Az URL-cím esetében például a `http://www.mydomain.com/pictures/city/strasbourg.png` következő bemeneti értékekhez engedélyezett a kérelmek: 
      >    <ul>
      >       <li>Bemeneti érték `/` : minden kérelem engedélyezett.</li>
      >       <li>Bemeneti érték `/pictures` , a következő kérelmek engedélyezettek: <ul>
      >          <li>`http://www.mydomain.com/pictures.png`</li>
      >          <li>`http://www.mydomain.com/pictures/city/strasbourg.png`</li>
      >          <li>`http://www.mydomain.com/picturesnew/city/strasbourgh.png`</li>
      >       </ul></li>
      >       <li>Bemeneti érték `/pictures/` : csak az `/pictures/` elérési utat tartalmazó kérelmek engedélyezettek. Például: `http://www.mydomain.com/pictures/city/strasbourg.png`.</li>
      >       <li>Bemeneti érték `/pictures/city/strasbourg.png` : csak az adott elérési útra és objektumra vonatkozó kérelmek engedélyezettek.</li>
      >    </ul>
      > </tr>
      > <tr>
      >    <td><b>ec_country_allow</b></td> 
      >    <td>Csak egy vagy több megadott országból/régióból származó kérelmeket engedélyez. Az összes többi országból/régióból származó kérelmek megtagadva. Mindegyik országhoz/régióhoz használjon kétbetűs [ISO 3166 országot vagy régiót](/previous-versions/azure/mt761717(v=azure.100)) , és mindegyiket vesszővel válassza el egymástól. ne adjon hozzá szóközt. Ha például csak a Egyesült Államok és Franciaországból szeretné engedélyezni a hozzáférést, írja be a következőt: `US,FR` .</td>
      > </tr>
      > <tr>
      >    <td><b>ec_country_deny</b></td> 
      >    <td>Megtagadja a kérelmeket, amelyek egy vagy több megadott országból/régióból származnak. Az összes többi országból/régióból származó kérelmek engedélyezettek. A megvalósítás megegyezik a <b>ec_country_allow</b> paraméterrel. Ha egy ország/régió kódja szerepel a <b>ec_country_allow</b> és a <b>ec_country_deny</b> paraméterekben is, a <b>ec_country_allow</b> paraméter elsőbbséget élvez.</td>
      > </tr>
      > <tr>
      >    <td><b>ec_ref_allow</b></td>
      >    <td>A csak a megadott hivatkozótól érkező kéréseket engedélyezi. A hivatkozó a kért erőforráshoz kapcsolódó weblap URL-címét azonosítja. Ne adja meg a protokollt a paraméter értékében.> 
      >    A következő típusú bemeneti típusok engedélyezettek:
      >    <ul>
      >       <li>Egy állomásnév vagy egy állomásnév és egy elérési út.</li>
      >       <li>Több hivatkozó. Több hivatkozó hozzáadásához vesszővel válassza el az egyes hivatkozókat; ne adjon hozzá szóközt. Ha egy hivatkozó értéket ad meg, de a rendszer nem küldi el a hivatkozói adatokat a kérésben a böngésző konfigurációja miatt, a rendszer alapértelmezés szerint elutasítja a kérést.</li> 
      >       <li>Hiányzó vagy üres hivatkozói adatokat tartalmazó kérelmek. Alapértelmezés szerint a <b>ec_ref_allow</b> paraméter blokkolja az ilyen típusú kérelmeket. A kérések engedélyezéséhez írja be a "hiányzó" szöveget, vagy adjon meg egy üres értéket (záró vessző használatával).</li> 
      >       <li>Altartományt. Az altartományok engedélyezéséhez írjon be egy csillag ( \* ) karaktert. Ha például az összes altartományát engedélyezni szeretné, adja meg a következőt: `contoso.com` `*.contoso.com` .</li>
      >    </ul> 
      >    Ha például engedélyezni kívánja a `www.contoso.com` , a és az összes altartományát, `contoso2.com` valamint az üres vagy hiányzó hivatkozó kérelmeket, adja meg a következőt: `www.contoso.com,*.contoso.com,missing` .</td>
      > </tr>
      > <tr> 
      >    <td><b>ec_ref_deny</b></td>
      >    <td>Megtagadja a megadott hivatkozótól érkező kéréseket. A megvalósítás megegyezik a <b>ec_ref_allow</b> paraméterrel. Ha egy referrer szerepel a <b>ec_ref_allow</b> és a <b>ec_ref_deny</b> paraméterekben is, a <b>ec_ref_allow</b> paraméter elsőbbséget élvez.</td>
      > </tr>
      > <tr> 
      >    <td><b>ec_proto_allow</b></td> 
      >    <td>A csak a megadott protokolltól érkező kéréseket engedélyezi. Az érvényes értékek: `http` , `https` , vagy `http,https` .</td>
      > </tr>
      > <tr>
      >    <td><b>ec_proto_deny</b></td>
      >    <td>Megtagadja a megadott protokolltól érkező kéréseket. A megvalósítás megegyezik a <b>ec_proto_allow</b> paraméterrel. Ha egy protokoll megtalálható a <b>ec_proto_allow</b> és a <b>ec_proto_deny</b> paraméterekben is, a <b>ec_proto_allow</b> paraméter elsőbbséget élvez.</td>
      > </tr>
      > <tr>
      >    <td><b>ec_clientip</b></td>
      >    <td>Korlátozza a hozzáférést a megadott kérelmező IP-címéhez. Az IPV4 és az IPV6 is támogatott. Egy adott alhálózathoz tartozó egyetlen kérelem IP-címét vagy IP-címét is megadhatja. Például `11.22.33.0/22` lehetővé teszi, hogy az IP-címekről érkező kérések 11.22.32.1 11.22.35.254.</td>
      > </tr>
      > </table>

   5. Miután befejezte a titkosítási paraméterek értékének megadását, jelöljön ki egy titkosítani kívánt kulcsot (ha a **kulcsból** egy elsődleges és egy biztonságimásolat-kulcsot hozott létre), válassza ki a titkosítást a listából.
    
   6. Válasszon egy titkosítási verziót a **titkosítási verziók** listájáról: **v2** 2. vagy **v3** -as verzió esetén a 3-as verzióhoz (ajánlott). 

   7. Válassza a **titkosítás** lehetőséget a jogkivonat létrehozásához.

      A jogkivonat létrehozása után az megjelenik a **generált jogkivonat** mezőben. A jogkivonat használatához fűzze hozzá lekérdezési karakterláncként a fájl végéhez az URL-cím elérési útjában. Például: `http://www.domain.com/content.mov?a4fbc3710fd3449a7c99986b`.
        
   8. Ha szeretné, tesztelje a tokent a visszafejtés eszközzel, hogy megtekintse a jogkivonat paramétereit. Illessze be a jogkivonat értékét a **visszafejteni kívánt jogkivonat** mezőbe. Válassza ki a **kulcsról a visszafejtéshez** használni kívánt titkosítási kulcsot, majd válassza a **Visszafejtés** lehetőséget.

      A jogkivonat visszafejtése után a paraméterek az **eredeti paraméterek** mezőben jelennek meg.

   9. Igény szerint testre szabhatja a kérelem elutasításakor visszaadott válasz kódját. Válassza az **engedélyezve** lehetőséget, majd válassza ki a válasz kódját a **Válasz kódja** listából. A **fejléc neve** automatikusan **helyre** van állítva. Válassza a **Mentés** lehetőséget az új válasz kódjának megvalósításához. Bizonyos hibakódok esetén a **fejléc értéke** mezőben meg kell adnia a hiba oldal URL-címét is. Alapértelmezés szerint a **403** -es válasz kód (tiltott) van kiválasztva. 

3. A **nagyméretű http** területen válassza a **szabályok motor** elemet. A szabályok motor használatával határozhatja meg a szolgáltatás alkalmazásához szükséges elérési utakat, engedélyezheti a jogkivonat-hitelesítés funkciót, és engedélyezheti a további jogkivonat-hitelesítéssel kapcsolatos képességeket. További információ: [szabályok motor referenciája](./cdn-verizon-premium-rules-engine-reference.md).

   1. Válasszon ki egy meglévő szabályt, vagy hozzon létre egy új szabályt annak az eszköznek vagy elérési útnak a megadásához, amelynek a jogkivonat-hitelesítését alkalmazni kívánja. 
   2. Ha engedélyezni szeretné a jogkivonat-hitelesítést egy szabályon, válassza a **[jogkivonat](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth.htm)** -hitelesítés elemet a **szolgáltatások** listából, majd válassza az **engedélyezve** lehetőséget. Válassza a **frissítés** lehetőséget, ha a szabályt vagy a **hozzáadást** egy szabály létrehozásakor frissíti.
        
      ![CDN-szabályok a motor jogkivonat-hitelesítésének engedélyezése példa](./media/cdn-token-auth/cdn-rules-engine-enable2.png)

4. A szabályok motorban engedélyezheti a további jogkivonat-hitelesítéssel kapcsolatos funkciókat is. A következő szolgáltatások bármelyikének engedélyezéséhez válassza ki a **szolgáltatások** listából, majd válassza az **engedélyezve** lehetőséget.
    
   - **[Jogkivonat-Auth-elutasítási kód](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth-Denial-Code.htm)**: meghatározza, hogy a rendszer milyen típusú választ ad vissza a felhasználónak, ha a kérelem megtagadva. Az itt beállított szabályok felülbírálják a jogkivonat-alapú hitelesítés lapon a **Custom tagadás-kezelési** szakaszban beállított hibakódot.

   - **[Jogkivonat-hitelesítés figyelmen kívül hagyása URL-cím esetén](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth-Ignore-URL-Case.htm)**: meghatározza, hogy a jogkivonat érvényesítéséhez használt URL-cím megkülönbözteti-e a kis-és

   - **[Jogkivonat-hitelesítési paraméter](https://docs.vdms.com/cdn/Content/HRE/F/Token-Auth-Parameter.htm)**: átnevezi a jogkivonat-hitelesítés lekérdezési karakterláncának paraméterét, amely megjelenik a kért URL-címen. 
        
     ![CDN-szabályok a motor token hitelesítési beállításai – példa](./media/cdn-token-auth/cdn-rules-engine2.png)

5. A jogkivonatot testre szabhatja a [githubon](https://github.com/VerizonDigital/ectoken)elérhető forráskód elérésével.
   Az elérhető nyelvek a következők:
    
   - C#
   - C#
   - PHP
   - Perl
   - Java
   - Python    

## <a name="azure-cdn-features-and-provider-pricing"></a>A Azure CDN szolgáltatásai és szolgáltatói díjszabása

A szolgáltatásokkal kapcsolatos további információkért tekintse meg [Azure CDN termék funkcióit](cdn-features.md). További információ a díjszabásról: [Content Delivery Network díjszabása](https://azure.microsoft.com/pricing/details/cdn/).