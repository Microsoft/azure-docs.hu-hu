---
title: Oktatóanyag - HTTPS konfigurálása Azure CDN egyéni tartományon | Microsoft Docs
description: Ebben az oktatóanyagban megismerheti, hogyan engedélyezheti és tilthatja le a HTTPS-t az Azure CDN-végpont egyéni tartományában.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: 10337468-7015-4598-9586-0b66591d939b
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/28/2019
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: 10e0f24642d54c43d6c818773d0eb17815ab784b
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/14/2019
ms.locfileid: "70996905"
---
# <a name="tutorial-configure-https-on-an-azure-cdn-custom-domain"></a>Oktatóanyag: HTTPS konfigurálása Azure CDN egyéni tartományon

Ez az oktatóanyag bemutatja, hogyan lehet engedélyezni a HTTPS-protokollt egy Azure CDN-végponthoz kapcsolódó egyéni tartomány esetében. A HTTPS-protokoll egyéni tartományon belüli használatával (például https:\//www.contoso.com) biztosítható, hogy a bizalmas adatokat a rendszer biztonságosan, TLS/SSL-titkosításon keresztül továbbítsa az Interneten. Amikor a böngésző HTTPS-protokollal kapcsolódik egy webhelyhez, akkor ellenőrzi a webhely biztonsági tanúsítványát, és megállapítja, hogy azt arra jogosult hitelesítésszolgáltató adta-e ki. Ez az eljárás védelmet nyújt webalkalmazásai számára a támadásokkal szemben.

Az Azure CDN alapértelmezés szerint támogatja a HTTPS-t a CDN-végpontok gazdaneve esetében. Ha például CDN-végpontot hoz létre (pl. https:\//contoso.azureedge.net), a HTTPS automatikusan engedélyezve lesz.  

Az egyéni HTTPS szolgáltatás legfőbb jellemzői a következők:

- Nincs további díj: A tanúsítvány beszerzése vagy megújítása nem jár, és a HTTPS-forgalomért nem jár további költségek. Csak a CDN-ből kimenő GB-forgalomért kell fizetnie.

- Egyszerű engedélyezés: Az egykattintásos kiépítés elérhető a [Azure Portal](https://portal.azure.com). A szolgáltatás engedélyezéséhez REST API-k, valamint más fejlesztői eszközök is használhatók.

- A tanúsítványok teljes körű felügyelete elérhető: A rendszer az összes tanúsítványt beszerzéssel és felügyelettel kezeli. A tanúsítványok üzembe helyezése és megújítása automatikusan megtörténik a lejárat előtt, így nem kell attól tartani, hogy a szolgáltatás megszakad egy lejárt tanúsítvány miatt.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> - HTTPS-protokoll engedélyezése az egyéni tartományon
> - CDN által kezelt tanúsítvány használata 
> - Saját tanúsítvány használata
> - A tartomány érvényesítése
> - HTTPS-protokoll letiltása az egyéni tartományon

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)] 

Mielőtt elvégezhetné a jelen oktatóanyag lépéseit, először létre kell hoznia egy CDN-profilt, és legalább egy CDN-végpontot. További információ: gyors útmutató [: Hozzon létre egy Azure CDN profilt](cdn-create-new-endpoint.md)és végpontot.

Emellett CDN-végpontjához társítania kell egy Azure CDN egyéni tartományt. További információ [: oktatóanyag: Egyéni tartomány hozzáadása az Azure CDN-végponthoz](cdn-map-content-to-custom-domain.md) 

> [!IMPORTANT]
> A CDN által felügyelt tanúsítványok nem érhetők el gyökér-vagy APEX-tartományokhoz. Ha a Azure CDN az egyéni tartomány egy gyökér-vagy APEX-tartomány, akkor a saját tanúsítvány használata funkciót kell használnia. 
>

---

## <a name="ssl-certificates"></a>SSL-tanúsítványok
Ha HTTPS protokollt szeretne engedélyezni egy egyéni Azure CDN-tartomány tartalmának biztonságos továbbítása érdekében, SSL-tanúsítványt kell használnia. Az Azure CDN által kezelt vagy saját tanúsítványt használhat.


# <a name="option-1-default-enable-https-with-a-cdn-managed-certificatetaboption-1-default-enable-https-with-a-cdn-managed-certificate"></a>[1. lehetőség (alapértelmezett): HTTPS engedélyezése CDN által felügyelt tanúsítvánnyal](#tab/option-1-default-enable-https-with-a-cdn-managed-certificate)

HA a CDN által kezelt tanúsítványt használ, a HTTPS szolgáltatás mindössze néhány kattintással bekapcsolható. Az Azure CDN elvégzi az összes tanúsítványkezelési feladatot, például a beszerzést és a megújítást. A szolgáltatás engedélyezése után a folyamat azonnal elindul. Ha az egyéni tartomány már le van képezve a CDN-végpontra, nincs további teendő. Az Azure CDN automatikusan feldolgozza a lépéseket és végrehajtja a kérést. Ha azonban az egyéni tartomány más helyre van leképezve, meg kell erősítenie a tartomány tulajdonjogát e-mailben.

Kövesse az alábbi lépéseket a HTTPS engedélyezéséhez egy egyéni tartományon:

1. A [Azure Portal](https://portal.azure.com)keresse meg a microsofttól a **Azure CDN standardot**, **Azure CDN standard from Akamai**, **Azure CDN standard from Verizon** vagy **Azure CDN Premium from Verizon** Profile.

2. A CDN-végpont listájából válassza ki az egyéni tartományt tartalmazó végpontot.

    ![Végpontlista](./media/cdn-custom-ssl/cdn-select-custom-domain-endpoint.png)

    Megjelenik a **Végpont** lap.

3. Az egyéni tartományok listájából válassza ki azt az egyéni tartományt, amelyen engedélyezni szeretné a HTTPS-t.

    ![Egyéni tartományok listája](./media/cdn-custom-ssl/cdn-custom-domain.png)

    Megjelenik az **Egyéni tartomány** lap.

4. A Tanúsítványkezelés típusa területen válassza a **CDN által felügyelt** lehetőséget.

5. Válassza a **Bekapcsolás** lehetőséget a HTTPS engedélyezéséhez.

    ![Egyéni tartomány HTTPS-állapota](./media/cdn-custom-ssl/cdn-select-cdn-managed-certificate.png)

6. Folytassa [A tartomány érvényesítése](#validate-the-domain) című szakasszal.


# <a name="option-2-enable-https-with-your-own-certificatetaboption-2-enable-https-with-your-own-certificate"></a>[2. lehetőség: A HTTPS engedélyezése a saját tanúsítvánnyal](#tab/option-2-enable-https-with-your-own-certificate)

> [!IMPORTANT]
> Ez a beállítás csak a **Microsoft Azure CDN** és **Azure CDN Verizon** -profilokból érhető el. 
>
 
A saját tanúsítványát is használhatja a HTTPS szolgáltatás engedélyezéséhez. Ez a folyamat Azure Key Vault-integrációval történik, amely lehetővé teszi a tanúsítványok biztonságos tárolását. Az Azure CDN ezt a biztonságos mechanizmust használja a tanúsítvány beszerzéséhez, és néhány további lépést igényel. SSL-tanúsítványt egy engedélyezett hitelesítésszolgáltatóval (CA) kell létrehoznia. Másként, nem engedélyezett CA használata igénybe vétele esetén a kérelme vissza lesz utasítva. Az engedélyezett hitelesítésszolgáltatók listájának megjelenítéséhez tekintse [meg az engedélyezett hitelesítésszolgáltatók az egyéni HTTPS engedélyezéséhez Azure CDN](cdn-troubleshoot-allowed-ca.md). A **Verizon Azure CDN**esetében minden érvényes hitelesítésszolgáltató el lesz fogadva. 

### <a name="prepare-your-azure-key-vault-account-and-certificate"></a>Az Azure Key Vault-fiók és a tanúsítvány előkészítése
 
1. Azure Key Vault: Az egyéni HTTPS-t engedélyező Azure CDN-profillal és a CDN-végpontokkal azonos előfizetéshez tartozó futtató Azure Key Vault fiókkal kell rendelkeznie. Ha még nem rendelkezik Azure Key Vault-fiókkal, hozzon létre egyet.
 
2. Azure Key Vault tanúsítványok: Ha már rendelkezik tanúsítvánnyal, feltöltheti közvetlenül a Azure Key Vault-fiókjába, vagy létrehozhat egy új tanúsítványt Azure Key Vault közvetlenül az egyik olyan partner hitelesítésszolgáltatótól, amely Azure Key Vault integrálódik a szolgáltatással. 

### <a name="register-azure-cdn"></a>Az Azure CDN regisztrálása

Regisztrálja az Azure CDN-t alkalmazásként az Azure Active Directoryjában PowerShellen keresztül.

1. Szükség esetén telepítse a [Azure PowerShellt](/powershell/azure/install-az-ps) a helyi gépre.

2. Futtassa a PowerShellben az alábbi parancsot:

     `New-AzADServicePrincipal -ApplicationId "205478c0-bd83-4e1b-a9d6-db63a3e1e1c8"`

    ![Azure CDN regisztrálása PowerShellben](./media/cdn-custom-ssl/cdn-register-powershell.png)
              

### <a name="grant-azure-cdn-access-to-your-key-vault"></a>Hozzáférés biztosítása az Azure CDN számára a Key Vaulthoz
 
Adjon engedélyt az Azure CDN számára, hogy hozzáférhessen az Azure Key Vault-fiókjában tárolt tanúsítványokhoz (titkos kódokhoz).

1. A Key Vault-fiók BEÁLLÍTÁSOK területén válassza a **Hozzáférési szabályzatok**, majd az **Új hozzáadása** lehetőséget új szabályzat létrehozásához.

    ![Új hozzáférési szabályzat létrehozása](./media/cdn-custom-ssl/cdn-new-access-policy.png)

2. A **Rendszerbiztonsági tag kijelölése** alatt keressen rá a **205478c0-bd83-4e1b-a9d6-db63a3e1e1c8** azonosítóra és jelölje ki a **Microsoft.Azure.Cdn** elemet. Kattintson a **Kiválasztás** gombra.

    ![Hozzáférési szabályzat beállításai](./media/cdn-custom-ssl/cdn-access-policy-settings.png)

3. Válassza a **tanúsítvány engedélyei**lehetőséget, majd jelölje be a **beolvasás** és **Listázás** jelölőnégyzetet, hogy a CDN engedélyezze ezeket az engedélyeket a tanúsítványok lekéréséhez és listázásához.

4. Kattintson az **OK** gombra. 

    Az Azure CDN most már hozzáférhet a Key Vaulthoz és az abban tárolt tanúsítványokhoz (titkos kódokhoz).
 
### <a name="select-the-certificate-for-azure-cdn-to-deploy"></a>Az Azure CDN által üzembe helyezendő tanúsítvány kiválasztása
 
1. Lépjen vissza az Azure CDN portálra, és válassza ki a profilt és CDN-végpontot, amelyhez engedélyezni szeretné az egyéni HTTPS-t. 

2. Az egyéni tartományok listájából válassza ki azt az egyéni tartományt, amelyen engedélyezni szeretné a HTTPS-t.

    Megjelenik az **Egyéni tartomány** lap.

3. A Tanúsítványkezelés típusa területen válassza a **Saját tanúsítvány használata** lehetőséget. 

    ![Tanúsítvány konfigurálása](./media/cdn-custom-ssl/cdn-configure-your-certificate.png)

4. Válassza ki a Key Vaultot, a tanúsítványt (titkos kódot) és a tanúsítványverziót.

    Az Azure CDN a következő információkat jeleníti meg: 
    - Az előfizetés azonosítójához tartozó Key Vault-fiókok. 
    - A kiválasztott Key Vaultban található tanúsítványok (titkos kódok). 
    - A tanúsítvány elérhető verziói. 
 
5. Válassza a **Bekapcsolás** lehetőséget a HTTPS engedélyezéséhez.
  
6. Saját tanúsítvány használatakor nem szükséges tartományérvényesítés. Lépjen tovább a [Várakozás a propagálásra](#wait-for-propagation) részhez.

---

## <a name="validate-the-domain"></a>A tartomány érvényesítése

Ha már rendelkezik használatban lévő egyéni tartománnyal, amely az egyéni végpontjára van leképezve egy CNAME rekorddal, vagy ha saját tanúsítványt használ, lépjen tovább a következőre:  
[Az egyéni tartomány le van képezve a CDN-végpontra](#custom-domain-is-mapped-to-your-cdn-endpoint-by-a-cname-record) Ellenkező esetben, ha már nem létezik a végpont CNAME rekordjának bejegyzése, vagy a cdnverify altartományt tartalmazza, lépjen tovább a [Az egyéni tartomány nincs leképezve a CDN-végpontra](#custom-domain-is-not-mapped-to-your-cdn-endpoint) részre.

### <a name="custom-domain-is-mapped-to-your-cdn-endpoint-by-a-cname-record"></a>Az egyéni tartomány le van képezve a CDN-végpontra egy CNAME rekord révén

Amikor hozzáadott egy egyéni tartományt a végpontjához, létrehozott egy CNAME rekordot a saját tartományregisztrálójának DNS-táblájában a CDN-végpont gazdanevére való leképezéséhez. Ha ez a CNAME rekord még létezik és nem tartalmazza a cdnverify altartományt, a DigiCert CA arra használja, hogy automatikusan érvényesítse az egyéni tartomány tulajdonjogát. 

Ha saját tanúsítványt használ, nem szükséges tartományérvényesítés.

A CNAME rekordnak a következő formátumban kell lennie, ahol a *Név* az Ön egyéni tartományának neve, az *Érték* pedig a CDN-végpont gazdaneve:

| Name (Név)            | Típus  | Value                 |
|-----------------|-------|-----------------------|
| < a www. contoso. com > | CNAME | contoso.azureedge.net |

A CNAME rekordokkal kapcsolatos további információért tekintse meg a [CNAME DNS-rekord létrehozását ismertető](https://docs.microsoft.com/azure/cdn/cdn-map-content-to-custom-domain) részt.

Ha a CNAME rekordja a megfelelő formátumban van, a DigiCert automatikusan ellenőrzi az egyéni tartománynevet, és létrehoz egy dedikált tanúsítványt. A DigitCert nem küld visszaigazoló e-mailt, és nem kell jóváhagynia a kérést. A tanúsítvány egy évig érvényes, és a lejárata előtt automatikusan megújul. Lépjen tovább a [Várakozás a propagálásra](#wait-for-propagation) részhez. 

Az automatikus érvényesítés általában néhány órát vesz igénybe. Ha nem látja a tartományt 24 órán belül érvényesítve, nyisson meg egy támogatási jegyet.

>[!NOTE]
>Ha van egy Hitelesítésszolgáltatói engedélyezési (CAA-) rekordja a DNS-szolgáltatónál, tartalmaznia kell a DigiCertet mint érvényes hitelesítésszolgáltatót. A CAA-rekord lehetővé teszi a tartomány tulajdonosai számára, hogy megadják a DNS-szolgáltatóknál, hogy melyik hitelesítésszolgáltatók jogosultak a tartomány tanúsítványának kiállítására. Ha egy hitelesítésszolgáltató kérést kap egy CAA-rekorddal rendelkező tartomány tanúsítványának kiállítására, és a hitelesítésszolgáltató nem szerepel az engedélyezett kiállítók listáján, nem adhat ki tanúsítványt a tartománynak vagy altartománynak. További információ a CAA-rekordok kezelésével kapcsolatban: [CAA-rekordok kezelése](https://support.dnsimple.com/articles/manage-caa-record/). A CAA-rekordokhoz való eszközért lásd: [CAA-rekord segítő](https://sslmate.com/caa/).

### <a name="custom-domain-is-not-mapped-to-your-cdn-endpoint"></a>Az egyéni tartomány nincs leképezve a CDN-végpontra

>[!NOTE]
>Ha Azure CDNt használ **a Akamai-ból**, az egyéni tartományt a fentiekben ismertetett CNAME-rekorddal kell leképezni a CDN-végpontot.  Ez a funkció jelenleg a lemaradásban van. 

Ha a CNAME rekord bejegyzése tartalmazza a cdnverify altartományt, kövesse az ebben a lépésben szereplő további utasításokat.

A DigiCert ellenőrző e-mailt küld a következő e-mail-címekre. Ellenőrizze, hogy jóváhagyhatja-e közvetlenül az alábbi címek valamelyikét:

admin@&lt;az-ön-tartományneve.com&gt;  
administrator@&lt;az-ön-tartományneve.com&gt;  
webmaster@&lt;az-ön-tartományneve.com&gt;  
hostmaster@&lt;az-ön-tartományneve.com&gt;  
postmaster@&lt;az-ön-tartományneve.com&gt;  

Pár percen belül a következőhöz hasonló e-mailt kell kapnia, amely a kérés jóváhagyására kéri. Ha spam szűrőt használ, adja hozzá a(z) verification@digicert.com címet az engedélyezési listához. Ha 24 órán belül nem kapja meg az e-mailt, lépjen kapcsolatba a Microsoft támogatási szolgálatával.
    
![Tartományérvényesítési e-mail](./media/cdn-custom-ssl/domain-validation-email.png)

Ha a jóváhagyási hivatkozásra kattint, a rendszer átirányítja a következő online jóváhagyási űrlapra: 
    
![Tartományérvényesítési űrlap](./media/cdn-custom-ssl/domain-validation-form.png)

Kövesse az űrlap utasításait; két ellenőrzési lehetősége van:

- Az ugyanazon gyökértartományhoz tartozó ugyanazon fiók összes jövőbeli kérést jóváhagyhatja; például: contoso.com. Ez akkor ajánlott, ha további egyéni tartományokat tervez hozzáadni ugyanazon gyökértartományhoz.

- Jóváhagyhatja az adott gazdanevet, amelyet a kéréshez használtak. A további kérésekhez további jóváhagyás szükséges.

A jóváhagyás után a DigiCert befejezi az egyéni tartománynév tanúsítványának létrehozását. A tanúsítvány egy évig érvényes, és a lejárata előtt automatikusan megújul.

## <a name="wait-for-propagation"></a>Várakozás a propagálásra

A tartománynév érvényesítése után 6-8 óra szükséges ahhoz, hogy az egyéni tartományhoz tartozó HTTPS szolgáltatás aktiválódjon. Ha a folyamat befejeződött, az egyéni HTTPS állapota **Engedélyezve** értékre vált az Azure Portalon, és a négy műveleti lépés az egyéni tartomány párbeszédpanelében befejezettnek lesz jelölve. Az egyéni tartomány ezzel készen áll a HTTPS használatára.

![HTTPS-párbeszédpanel engedélyezése](./media/cdn-custom-ssl/cdn-enable-custom-ssl-complete.png)

### <a name="operation-progress"></a>Műveleti folyamat

Az alábbi táblázat a műveleti folyamatot mutatja, amely a HTTPS engedélyezésekor megy végbe. Miután engedélyezte a HTTPS-t, négy műveleti lépés jelenik meg az egyéni tartomány párbeszédpaneljében. Ahogy az egyes lépések aktívvá válnak, a folyamat előrehaladtával további allépések részletei jelennek meg a lépés alatt. Nem minden allépés fog előfordulni. Miután egy lépés sikeresen befejeződik, egy zöld pipa jelenik meg mellette. 

| Műveleti lépés | Műveleti allépés részletei | 
| --- | --- |
| 1\. Kérés elküldése | Kérés elküldése |
| | A HTTPS-kérés küldése folyamatban van. |
| | A HTTPS-kérés elküldése sikerült. |
| 2\. Tartományérvényesítés | A tartomány automatikusan érvényesítve lesz, ha a CNAME révén le van képezve a CDN-végpontra. Máskülönben visszaigazolási kérelem érkezik a tartomány regisztrációs rekordjában megadott e-mail-címre (WHOIS regisztráló). Kérjük, minél hamarabb igazolja vissza a tartományt. |
| | Sikerült ellenőrizni a tartomány tulajdonjogát. |
| | A tartomány tulajdonjogának ellenőrzési kérelme lejárt (az ügyfél valószínűleg nem válaszolt 6 napon belül). A HTTPS nem lesz engedélyezve a tartományon. * |
| | A tartomány tulajdonjogának ellenőrzésére vonatkozó kérelem vissza lett utasítva az ügyfél által. A HTTPS nem lesz engedélyezve a tartományon. * |
| 3\. Tanúsítvány üzembe helyezése | A hitelesítésszolgáltató jelenleg azon tanúsítvány kibocsátását végzi, amely a HTTPS tartományban való engedélyezéséhez szükséges. |
| | A tanúsítvány kibocsátása megtörtént, és folyamatban van a CDN-hálózatban való üzembe helyezése. A folyamat akár hat órát is igénybe vehet. |
| | Sikerült üzembe helyezni a tanúsítványt a CDN-hálózatban. |
| 4\. Befejezve | Sikerült engedélyezni a HTTPS-t a tartományban. |

\* Ez az üzenet csak akkor jelenik meg, ha hiba történt. 

Ha a kérelem elküldése előtt hiba történik, a következő hibaüzenet jelenik meg:

<code>
We encountered an unexpected error while processing your HTTPS request. Please try again and contact support if the issue persists.
</code>



## <a name="clean-up-resources---disable-https"></a>Az erőforrások eltávolítása – HTTPS letiltása

Az előző lépések során engedélyezte a HTTPS protokollt az egyéni tartományon. Ha már nem szeretné HTTPS-sel használni az egyéni tartományt, letilthatja a HTTPS-t a következő lépések végrehajtásával:

### <a name="disable-the-https-feature"></a>HTTPS szolgáltatás letiltása 

1. Az [Azure Portalon](https://portal.azure.com) keressen rá a **Microsoft Azure CDN Standard**, **Verizon Azure CDN Standard** vagy a **Verizon Azure CDN Premium** profilra.

2. A végpont listájában kattintson az egyéni tartományt tartalmazó végpontra.

3. Válassza ki azt az egyéni tartományt, amelyen le szeretné tiltani a HTTPS-t.

    ![Egyéni tartományok listája](./media/cdn-custom-ssl/cdn-custom-domain-HTTPS-enabled.png)

4. A HTTPS letiltásához kattintson a **Kikapcsolás** lehetőségre, majd a kattintson az **Alkalmaz** gombra.

    ![Egyéni HTTPS párbeszédpanel](./media/cdn-custom-ssl/cdn-disable-custom-ssl.png)

### <a name="wait-for-propagation"></a>Várakozás a propagálásra

Az egyéni tartomány HTTPS szolgáltatásának letiltása után 6-8 óra szükséges ahhoz, hogy a művelet végbemenjen. Ha a folyamat befejeződött, az egyéni HTTPS állapota **Letiltva** értékre vált az Azure Portalon, és a három műveleti lépés az egyéni tartomány párbeszédpanelében befejezettnek lesz jelölve. Az egyéni tartomány már nem használhatja a HTTPS-t.

![HTTPS letiltása párbeszédpanel](./media/cdn-custom-ssl/cdn-disable-custom-ssl-complete.png)

#### <a name="operation-progress"></a>Műveleti folyamat

Az alábbi táblázat a műveleti folyamatot mutatja, amely a HTTPS letiltásakor megy végbe. Miután letiltotta a HTTPS-t, három műveleti lépés jelenik meg az egyéni tartomány párbeszédpaneljében. Ahogy az egyes lépések aktívvá válnak, további részletek jelennek meg a lépés alatt. Miután egy lépés sikeresen befejeződik, egy zöld pipa jelenik meg mellette. 

| Műveleti folyamat | Művelet részletei | 
| --- | --- |
| 1\. Kérés elküldése | A kérelem elküldése folyamatban van |
| 2\. Tanúsítvány megszüntetése | Tanúsítvány törlése |
| 3\. Befejezve | Tanúsítvány törölve |

## <a name="frequently-asked-questions"></a>Gyakori kérdések

1. *Ki a tanúsítványszolgáltató és milyen típusú tanúsítvány van használatban?*

    A rendszer **Verizon Azure CDN** és a **Microsoft CDN Standard** esetén egy Digicert által biztosított dedikált/egyetlen tanúsítványt használ az egyéni tartományhoz. 

2. *IP-címalapú vagy SNI TLS/SSL-t használ?*

    A **Verizon Azure CDN** és a **Microsoft CDN Standard** is SNI TLS/SSL-t használ.

3. *Mi a teendő, ha nem kapok visszaigazolási e-mailt a DigiCerttől?*

    Ha van olyan CNAME-bejegyzése az egyéni tartomány esetében, amely közvetlenül a gazdanév végpontjára mutat (és nem használja a cdnverify altartománynevet), nem fog a tartomány visszaigazolására vonatkozó e-mailt kapni. A hitelesítés automatikusan történik. Máskülönben, ha nem rendelkezik CNAME-bejegyzéssel, és 24 órán belül nem kapott e-mailt, forduljon a Microsoft támogatási szolgálatához.

4. *A SAN tanúsítvány használata kevésbé biztonságos, mint egy dedikált tanúsítvány használata?*
    
    A SAN-tanúsítvány ugyanolyan titkosítási és biztonsági előírásokat követ, mint a dedikált tanúsítvány. Az összes kiállított SSL-tanúsítvány az SHA-256-ot használja a kiszolgáló fokozott biztonsága érdekében.

5. *Szükségem van hitelesítésszolgáltató engedélyezési rekordra a DNS szolgáltatómnál?*

    Nem, hitelesítésszolgáltatói engedélyezési rekordra jelenleg nincs szükség. Viszont ha van ilyenje, mindenképpen tartalmaznia kell a DigiCertet mint érvényes CA-t.

6. *2018. június 20-tól a Verizon Azure CDN alapértelmezés szerint SNI TLS/SSL-lel használja a dedikált tanúsítványokat. Mi történik a tulajdonos alternatív nevével (SAN) megadott tanúsítványt és IP-cím alapú TLS/SSL-t használó meglévő egyéni tartományaimmal?*

    Ha a Microsoft elemzése szerint az alkalmazásába csak SNI-ügyfélkérelmek érkeznek, a meglévő tartományait a következő hónapokban fokozatosán migráljuk egyetlen tanúsítvány használatára. Ha a Microsoft észleli, hogy nem SNI-ügyfélkérelmek érkeznek az alkalmazásába, a tartományok az IP-cím alapú TLS/SSL-t használó SAN-tanúsítványban maradnak. A szolgáltatása és az ügyfélkérelmek támogatása egyik esetben sem szakad meg, attól függetlenül, hogy a kérelmek SNI- vagy nem SNI-kérelmek.

7. *Hogyan működik a tanúsítvány megújítása a saját tanúsítvánnyal?*

    Annak biztosítása érdekében, hogy egy újabb tanúsítvány legyen üzembe helyezve a PoP-infrastruktúrában, egyszerűen töltse fel az új tanúsítványt az Azure kulcstartóba, majd az Azure CDN SSL-beállításainál válassza a legújabb tanúsítvány verziót, és kattintson a Save (Mentés) gombra. A Azure CDN ekkor propogate az új, frissített tanúsítványt. 

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> - HTTPS-protokoll engedélyezése az egyéni tartományon
> - CDN által kezelt tanúsítvány használata 
> - Saját tanúsítvány használata
> - A tartomány érvényesítése
> - HTTPS-protokoll letiltása az egyéni tartományon

Lépjen tovább a következő oktatóanyagra, amely bemutatja, hogyan lehet konfigurálni a gyorsítótárat a CDN-végponton.

> [!div class="nextstepaction"]
> [Oktatóanyag: Azure CDN gyorsítótárazási szabályok beállítása](cdn-caching-rules-tutorial.md)

