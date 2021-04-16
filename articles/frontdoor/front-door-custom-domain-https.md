---
title: Oktatóanyag – HTTPS konfigurálása egyéni tartományon Azure Front Door | Microsoft Docs
description: Ez az oktatóanyag bemutatja, hogyan engedélyezheti és tilthatja le a HTTPS-t a Azure Front Door egyéni tartomány konfigurációjában.
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/26/2021
ms.author: duau
ms.openlocfilehash: 4291a7d46c723f799cf9d09ca0e7a3f6d614971f
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389740"
---
# <a name="tutorial-configure-https-on-a-front-door-custom-domain"></a>Oktatóanyag: HTTPS konfigurálása Front Door egyéni tartományon

Ez az oktatóanyag bemutatja, hogyan lehet engedélyezni a HTTPS-protokollt az előtérbeli gazdagépek szakaszban egy Front Doorhoz kapcsolódó egyéni tartomány esetében. Ha a HTTPS protokollt használja az egyéni tartományon (például https: /www.contoso.com), gondoskodhat arról, hogy a bizalmas adatok biztonságosan, TLS/SSL-titkosítással küldjük el őket az \/ interneten keresztül. Amikor a böngésző HTTPS-protokollal kapcsolódik egy webhelyhez, ellenőrzi a webhely biztonsági tanúsítványát, és megállapítja, hogy azt arra jogosult hitelesítésszolgáltató adta-e ki. Ez az eljárás védelmet nyújt webalkalmazásai számára a támadásokkal szemben.

Azure Front Door támogatja a HTTPS-t Front Door alapértelmezett gazdanéven. Ha például létrehoz egy Front Door (például ), a HTTPS automatikusan engedélyezve lesz a számára `https://contoso.azurefd.net` lekért kérések `https://contoso.azurefd.net` számára. Az "www.contoso.com" egyéni tartományt azonban ezen az előtere gazdagépen is engedélyeznie kell.   

Az egyéni HTTPS szolgáltatás legfőbb jellemzői a következők:

- Nincs további költség: A tanúsítvány beszerzése vagy megújítása nem jár többletköltséggel, és a HTTPS-forgalom nem jár többletköltséggel. 

- Egyszerű engedélyezés: a kiépítés egy kattintással elvégezhető az [Azure Portalon](https://portal.azure.com) keresztül. A szolgáltatás engedélyezéséhez REST API-k, valamint más fejlesztői eszközök is használhatók.

- Teljes körű tanúsítványkezelés érhető el: nem kell foglalkoznia a tanúsítványok beszerzésével és kezelésével. A tanúsítványok kiépítése és megújítása automatikusan megtörténik a lejárat előtt, ami megszünteti a szolgáltatáskimaradás kockázatát a tanúsítvány lejárata miatt.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> - HTTPS-protokoll engedélyezése az egyéni tartományon
> - AFD által kezelt tanúsítvány használata 
> - Saját tanúsítvány, például egyéni TLS-/SSL-tanúsítvány használata
> - A tartomány érvényesítése
> - HTTPS-protokoll letiltása az egyéni tartományon


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elvégezhetné a jelen oktatóanyag lépéseit, először létre kell hoznia egy legalább egy egyéni tartománnyal regisztrált Front Doort. További információkért lásd: [Útmutató: Egyéni tartomány hozzáadása a Front Doorhoz](front-door-custom-domain.md)

## <a name="tlsssl-certificates"></a>TLS-/SSL-tanúsítványok

Ha engedélyezni szeretné a HTTPS-protokollt egy egyéni Front Door tartalom biztonságos kézbesítéséhez, TLS-/SSL-tanúsítványt kell használnia. Választhat, hogy olyan tanúsítványt használ, amelyet a Azure Front Door vagy a saját tanúsítványát használja.


### <a name="option-1-default-use-a-certificate-managed-by-front-door"></a>1. lehetőség (alapértelmezett): A Front Door által felügyelt tanúsítvány használata

Ha a tanúsítvány által felügyelt tanúsítványt Azure Front Door, a HTTPS szolgáltatás mindössze néhány kattintással bekapcsolható. Azure Front Door teljes mértékben kezeli a tanúsítványkezelési feladatokat, például a beszerzést és a megújítást. A szolgáltatás engedélyezése után a folyamat azonnal elindul. Ha az egyéni tartomány már hozzá van rendelve a Front Door alapértelmezett előtérbeli gazdagépéhez (`{hostname}.azurefd.net`), nincs további teendő. A Front Door automatikusan feldolgozza a lépéseket és végrehajtja a kérést. Ha azonban az egyéni tartomány más helyre van leképezve, meg kell erősítenie a tartomány tulajdonjogát e-mailben.

Kövesse az alábbi lépéseket a HTTPS engedélyezéséhez egy egyéni tartományon:

1. Az [Azure Portalon](https://portal.azure.com) keresse meg **Front Door**-profilját.

2. Az előtérbeli gazdagépek listájából válassza ki azt az egyéni tartományt, amelyen engedélyezni szeretné a HTTPS-t az egyéni tartomány tárolásához.

3. Az Egyéni tartomány **HTTPS szakaszában** válassza az **Engedélyezve** lehetőséget, majd Front Door **felügyelt** tanúsítványforrásként lehetőséget.

4. Kattintson a Mentés gombra.

5. Folytassa a [tartomány ellenőrzéséhez.](#validate-the-domain)

> [!NOTE]
> * Az AFD által felügyelt tanúsítványok esetében a DigiCert 64 karakteres korlátja van érvényben. A korlát túllépése esetén az érvényesítés sikertelen lesz.
> * A HTTPS felügyelt tanúsítvánnyal Front Door engedélyezése nem támogatott a legfelső szintű/gyökértartományok (például: contoso.com). Ehhez a forgatókönyvhöz használhatja a saját tanúsítványát.  További részletekért folytassa a 2. lehetőséggel.

### <a name="option-2-use-your-own-certificate"></a>2. lehetőség: Saját tanúsítvány használata

A saját tanúsítványát is használhatja a HTTPS szolgáltatás engedélyezéséhez. Ez a folyamat Azure Key Vault-integrációval történik, amely lehetővé teszi a tanúsítványok biztonságos tárolását. Azure Front Door ezt a biztonságos mechanizmust használja a tanúsítvány lekérni, és néhány további lépést igényel. A TLS-/SSL-tanúsítvány létrehozásakor azt egy engedélyezett hitelesítésszolgáltatóval (CA) kell létrehoznia. Másként, nem engedélyezett CA használata igénybe vétele esetén a kérelme vissza lesz utasítva. Az engedélyezett hitelesítésszolgáltatói listájáért lásd: Engedélyezett hitelesítésszolgáltató az egyéni HTTPS engedélyezéséhez [a Azure Front Door.](front-door-troubleshoot-allowed-ca.md)

#### <a name="prepare-your-azure-key-vault-account-and-certificate"></a>Az Azure Key Vault-fiók és a tanúsítvány előkészítése
 
1. Azure Key Vault: Rendelkeznie kell egy futó Azure Key Vault-fiókkal abban az előfizetésben, amelyben az a Front Door található, amelyhez egyéni HTTPS-t szeretne engedélyezni. Ha még nem rendelkezik Azure Key Vault-fiókkal, hozzon létre egyet.

> [!WARNING]
> Azure Front Door jelenleg csak Key Vault fiókokat támogat, amelyek ugyanabban az előfizetésben vannak, mint a Front Door konfigurációja. Ha nem a Front Door-előfizetéshez tartozó Key Vault-tárolót választ, az hibát eredményez.

2. Azure Key Vault-tanúsítványok: Ha már rendelkezik tanúsítvánnyal, feltöltheti közvetlenül az Azure Key Vault-fiókjába, vagy létrehozhat egy új tanúsítványt közvetlenül az Azure Key Vaultban azokkal a hitelesítésszolgáltató (CA) partnerekkel, amelyekkel az Azure Key Vault integrálva van. A tanúsítványt nem **titkos,** hanem tanúsítványobjektumként töltse **fel.**

> [!NOTE]
> Saját TLS-/SSL-tanúsítványa esetében a Front Door ec titkosítási algoritmusokkal nem támogatja a tanúsítványokat.

#### <a name="register-azure-front-door"></a>Regisztráció Azure Front Door

Regisztrálja a szolgáltatásnév Azure Front Door alkalmazásként a Azure Active Directory PowerShell használatával.

> [!NOTE]
> Ehhez a művelethez globális rendszergazdai engedélyekre van szükség, és bérlőnként csak **egyszer kell** elvégezni.

1. Ha szükséges, telepítse az [Azure PowerShell](/powershell/azure/install-az-ps) bővítményt a PowerShellhez a helyi számítógépen.

2. Futtassa a PowerShellben az alábbi parancsot:

     `New-AzADServicePrincipal -ApplicationId "ad0e1c7e-6d38-4ba4-9efd-0bc77ba9f037"`              

#### <a name="grant-azure-front-door-access-to-your-key-vault"></a>Hozzáférés Azure Front Door kulcstartóhoz
 
Adjon Azure Front Door a fiókban található tanúsítványokhoz való hozzáféréshez Azure Key Vault engedélyt.

1. A Key Vault-fiók BEÁLLÍTÁSOK területén válassza a **Hozzáférési szabályzatok**, majd az **Új hozzáadása** lehetőséget új szabályzat létrehozásához.

2. A **Rendszerbiztonsági tag kijelölése** területen keresse meg az **ad0e1c7e-6d38-4ba4-9efd-0bc77ba9f037** elemet, és válassza ki a **Microsoft.Azure.Frontdoor** lehetőséget. Kattintson a **Kiválasztás** elemre.

3. A **Titkos adatok engedélyei mezőben** válassza a **Beolvasás lehetőséget,** Front Door lekérheti a tanúsítványt.

4. A **Tanúsítványengedélyek mezőben** válassza **a Beolvasás lehetőséget,** Front Door lekérheti a tanúsítványt.

5. Válassza az **OK** lehetőséget. 

    Azure Front Door hozzáférhet ehhez a Key Vault és az ebben a tárolóban tárolt tanúsítványokhoz Key Vault.
 
#### <a name="select-the-certificate-for-azure-front-door-to-deploy"></a>Válassza ki a központi telepítéshez Azure Front Door tanúsítványt
 
1. Lépjen vissza a Front Doorhoz a portálon. 

2. Az egyéni tartományok listájából válassza ki azt az egyéni tartományt, amelyen engedélyezni szeretné a HTTPS-t.

    Megjelenik az **Egyéni tartomány** lap.

3. A Tanúsítványkezelés típusa területen válassza a **Saját tanúsítvány használata** lehetőséget. 

4. Azure Front Door szükséges, hogy a Key Vault-fiók előfizetése ugyanaz, mint a Front Door. Válassza ki a Key Vault, a Titkos kulcs és a Titkos kulcs verzióját.

    Azure Front Door az alábbi információkat tartalmazza: 
    - Az előfizetés azonosítójához tartozó Key Vault-fiókok. 
    - A kiválasztott kulcstartó alatti titkos kulcsok. 
    - Az elérhető titkos verziók.

    > [!NOTE]
    >  Ahhoz, hogy a rendszer automatikusan a legújabb verzióra váltsa a tanúsítványt, amikor a tanúsítvány újabb verziója elérhető a Key Vault, állítsa a titkos adatokat "Latest" (Legújabb) verzióra. Ha egy adott verzió van kiválasztva, manuálisan újra ki kell választania az új verziót a tanúsítványrotációhoz. A tanúsítvány/titkos tanúsítvány új verziójának üzembe helyezése akár 24 órát is igénybe vesz. 
 
5. Ha saját tanúsítványt használ, nincs szükség tartományérvényesítésre. Folytassa [a Várakozás a propagálásra gombra.](#wait-for-propagation)

## <a name="validate-the-domain"></a>A tartomány érvényesítése

Ha már van használatban egy egyéni tartománya, amely le van leképezve az egyéni végpontra egy CNAME rekordtal, vagy ha saját tanúsítványt [használ,](#custom-domain-is-mapped-to-your-front-door-by-a-cname-record)folytassa az Egyéni tartomány leképezve a saját Front Door. Ellenkező esetben, ha a tartomány CNAME rekordbejegyzése már nem létezik, vagy az afdverify altartományt tartalmazza, folytassa az Egyéni tartomány nincs leképezve a [Front Door.](#custom-domain-is-not-mapped-to-your-front-door)

### <a name="custom-domain-is-mapped-to-your-front-door-by-a-cname-record"></a>Az egyéni tartomány le van képezve a Front Doorra egy CNAME rekorddal

Amikor hozzáadott egy egyéni tartományt a Front Door előtérbeli gazdagépeihez, létrehozott egy CNAME rekordot a saját tartományregisztrálójának DNS-táblájában, hogy leképezze a Front Door alapértelmezett .azurefd.net eszköznevére. Ha ez a CNAME rekord még létezik, és nem tartalmazza az afdverify altartományt, a DigiCert hitelesítésszolgáltató automatikusan érvényesíti az egyéni tartomány tulajdonjogát. 

Ha saját tanúsítványt használ, nincs szükség tartományérvényesítésre.

A CNAME rekordnak a következő formátumban kell lennie, ahol a *Név* az Ön egyéni tartományának neve, az *Érték* pedig a Front Door alapértelmezett .azurefd.net eszközneve:

| Név            | Típus  | Érték                 |
|-----------------|-------|-----------------------|
| <www.contoso.com> | CNAME | contoso.azurefd.net |

A CNAME rekordokkal kapcsolatos további információért tekintse meg a [CNAME DNS-rekord létrehozását ismertető](../cdn/cdn-map-content-to-custom-domain.md) részt.

Ha a CNAME rekordja a megfelelő formátumban van, a DigiCert automatikusan ellenőrzi az egyéni tartománynevet, és létrehoz egy dedikált tanúsítványt. A DigitCert nem küld visszaigazoló e-mailt, és nem kell jóváhagynia a kérést. A tanúsítvány egy évig érvényes, és a lejárata előtt automatikusan újra fog újatni. Folytassa [a Várakozás a propagálásra gombra.](#wait-for-propagation) 

Az automatikus érvényesítés általában eltart néhány percig. Ha a tartománya egy órán belül sincs érvényesítve, nyisson meg egy támogatási jegyet.

>[!NOTE]
>Ha van egy Hitelesítésszolgáltatói engedélyezési (CAA-) rekordja a DNS-szolgáltatónál, tartalmaznia kell a DigiCertet mint érvényes hitelesítésszolgáltatót. A CAA-rekord lehetővé teszi a tartomány tulajdonosai számára, hogy megadják a DNS-szolgáltatóknál, hogy melyik hitelesítésszolgáltatók jogosultak a tartomány tanúsítványának kiállítására. Ha egy hitelesítésszolgáltató kérést kap egy CAA-rekorddal rendelkező tartomány tanúsítványának kiállítására, és a hitelesítésszolgáltató nem szerepel az engedélyezett kiállítók listáján, nem adhat ki tanúsítványt a tartománynak vagy altartománynak. További információ a CAA-rekordok kezelésével kapcsolatban: [CAA-rekordok kezelése](https://support.dnsimple.com/articles/manage-caa-record/). A CAA-rekordokhoz való eszközért lásd: [CAA-rekord segítő](https://sslmate.com/caa/).

### <a name="custom-domain-is-not-mapped-to-your-front-door"></a>Az egyéni tartomány nincs leképezve a Front Doorra

Ha már nem létezik a végpont CNAME rekordjának bejegyzése, vagy az afdverify altartományt tartalmazza, kövesse az itt ismertetett lépéseket.

Miután engedélyezi a HTTPS-t az egyéni tartományhoz, A DigiCert CA érvényesíti a tartomány tulajdonjogát azáltal, hogy kapcsolatba lép a regisztrálójával a tartomány [WHOIS](http://whois.domaintools.com/) regisztrálójának információja alapján. A kapcsolatfelvétel a WHOIS-regisztrációban megadott e-mail-címen (alapértelmezett) vagy telefonszámon keresztül történik. A HTTPS csak a tartomány hitelesítése után aktiválódik az egyéni tartományon. A tartomány jóváhagyására hat munkanap áll rendelkezésére. A hat munkanapon belül jóvá nem hagyott kérelmeket a rendszer automatikusan visszavonja. 

![WHOIS-rekord](./media/front-door-custom-domain-https/whois-record.png)

A DigiCert egy ellenőrző e-mailt is küld más e-mail-címekre. Ha a WHOIS-regisztráló információja bizalmas, erősítse meg, hogy a jóváhagyást el tudja végezni közvetlenül a következő címek egyikéről:

admin@&lt;az-ön-tartományneve.com&gt;  
administrator@&lt;az-ön-tartományneve.com&gt;  
webmaster@&lt;az-ön-tartományneve.com&gt;  
hostmaster@&lt;az-ön-tartományneve.com&gt;  
postmaster@&lt;az-ön-tartományneve.com&gt;  

Pár percen belül a következőhöz hasonló e-mailt kell kapnia, amely a kérés jóváhagyására kéri. Ha levélszemétszűrőt használ, adja hozzá a admin@digicert.com et az engedélyezési listához. Ha 24 órán belül nem kapja meg az e-mailt, lépjen kapcsolatba a Microsoft támogatási szolgálatával.

A jóváhagyási hivatkozás kiválasztásakor a rendszer egy online jóváhagyási űrlapra irányítja át. Kövesse az űrlap utasításait; két ellenőrzési lehetősége van:

- Az ugyanazon gyökértartományhoz tartozó ugyanazon fiók összes jövőbeli kérést jóváhagyhatja; például: contoso.com. Ez a módszer akkor ajánlott, ha több egyéni tartományt szeretne hozzáadni ugyanabba a gyökértartományba.

- Jóváhagyhatja az adott gazdanevet, amelyet a kéréshez használtak. További jóváhagyásra van szükség a további kérések esetén.

A jóváhagyás után a DigiCert befejezi az egyéni tartománynév tanúsítványának létrehozását. A tanúsítvány egy évig érvényes, és a lejárta előtt automatikusan újra lesz újra automatizálva.

## <a name="wait-for-propagation"></a>Várakozás a propagálásra

A tartománynév érvényesítése után 6-8 óra szükséges ahhoz, hogy az egyéni tartományhoz tartozó HTTPS szolgáltatás aktiválódjon. Ha a folyamat befejeződött, az egyéni HTTPS állapota **Engedélyezve** értékre vált az Azure Portalon, és a négy műveleti lépés az egyéni tartomány párbeszédpanelében befejezettnek lesz jelölve. Az egyéni tartomány ezzel készen áll a HTTPS használatára.

### <a name="operation-progress"></a>Műveleti folyamat

Az alábbi táblázat a műveleti folyamatot mutatja, amely a HTTPS engedélyezésekor megy végbe. Miután engedélyezte a HTTPS-t, négy műveleti lépés jelenik meg az egyéni tartomány párbeszédpaneljében. Ahogy az egyes lépések aktívvá válnak, további részlépési részletek jelennek meg a lépés alatt a folyamat előrehaladása során. Nem minden allépés fog előfordulni. Miután egy lépés sikeresen befejeződik, egy zöld pipa jelenik meg mellette. 

| Műveleti lépés | Műveleti allépés részletei | 
| --- | --- |
| 1. Kérés elküldése | Kérés elküldése |
| | A HTTPS-kérés küldése folyamatban van. |
| | A HTTPS-kérés elküldése sikerült. |
| 2. Tartományérvényesítés | A rendszer automatikusan érvényesíti a tartományt, ha a CNAME az alapértelmezett .azurefd.net gazdagépre van leképezve a Front Door. Ha nincs, akkor visszaigazolási kérelem érkezik a tartomány regisztrációs rekordjában megadott e-mail-címre (WHOIS regisztráló). Minél hamarabb igazolja vissza a tartományt. |
| | Sikerült ellenőrizni a tartomány tulajdonjogát. |
| | A tartomány tulajdonjogának ellenőrzési kérelme lejárt (az ügyfél valószínűleg nem válaszolt 6 napon belül). A HTTPS nem lesz engedélyezve a tartományban. * |
| | A tartomány tulajdonjogának ellenőrzésére vonatkozó kérelem vissza lett utasítva az ügyfél által. A HTTPS nem lesz engedélyezve a tartományban. * |
| 3. Tanúsítvány üzembe helyezése | A hitelesítésszolgáltató jelenleg azon tanúsítvány kibocsátását végzi, amely a HTTPS tartományban való engedélyezéséhez szükséges. |
| | A tanúsítvány kibocsátása megtörtént, és a Front Doorban való üzembe helyezése folyamatban van. A folyamat akár 1 órát is igénybe vehet. |
| | A tanúsítvány sikeresen üzembe lett helyezve a Front Doorhoz. |
| 4. Befejezve | Sikerült engedélyezni a HTTPS-t a tartományban. |

\* Ez az üzenet csak akkor jelenik meg, ha hiba történt. 

Ha a kérelem elküldése előtt hiba történik, a következő hibaüzenet jelenik meg:

<code>
We encountered an unexpected error while processing your HTTPS request. Please try again and contact support if the issue persists.
</code>

## <a name="frequently-asked-questions"></a>Gyakori kérdések

1. *Ki a tanúsítványszolgáltató és milyen típusú tanúsítvány van használatban?*

    A rendszer egy Digicert által biztosított dedikált/egyetlen tanúsítványt használ az egyéni tartományhoz. 

2. *IP-címalapú vagy SNI TLS/SSL-t használ?*

    Azure Front Door SNI TLS/SSL-t használ.

3. *Mi a teendő, ha nem kapok visszaigazolási e-mailt a DigiCerttől?*

    Ha az egyéni tartományhoz olyan CNAME-bejegyzése van, amely közvetlenül a végpont gazdanevére mutat (és nem az afdverify altartománynevet használja), nem fog tartomány-ellenőrző e-mailt kapni. A hitelesítés automatikusan történik. Máskülönben, ha nem rendelkezik CNAME-bejegyzéssel, és 24 órán belül nem kapott e-mailt, forduljon a Microsoft támogatási szolgálatához.

4. *A SAN tanúsítvány használata kevésbé biztonságos, mint egy dedikált tanúsítvány használata?*
    
    A SAN-tanúsítvány ugyanolyan titkosítási és biztonsági előírásokat követ, mint a dedikált tanúsítvány. Minden kiállított TLS/SSL-tanúsítvány SHA-256-ot használ a kiszolgáló fokozott biztonsága érdekében.

5. *Szükségem van hitelesítésszolgáltató engedélyezési rekordra a DNS szolgáltatómnál?*

    Nem, hitelesítésszolgáltatói engedélyezési rekordra jelenleg nincs szükség. Viszont ha van ilyenje, mindenképpen tartalmaznia kell a DigiCertet mint érvényes CA-t.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az előző lépések során engedélyezte a HTTPS protokollt az egyéni tartományon. Ha már nem szeretné HTTPS-sel használni az egyéni tartományt, az alábbi lépésekkel tilthatja le a HTTPS-t:

### <a name="disable-the-https-feature"></a>HTTPS szolgáltatás letiltása 

1. A [Azure Portal](https://portal.azure.com)keresse meg a Azure Front Door **konfigurációját.**

2. Az előtere gazdagépek listájában válassza ki azt az egyéni tartományt, amelyhez le szeretné tiltani a HTTPS-t.

3. A HTTPS letiltásához kattintson a **Letiltva**, majd a **Mentés** gombra.

### <a name="wait-for-propagation"></a>Várakozás a propagálásra

Az egyéni tartomány HTTPS szolgáltatásának letiltása után 6-8 óra szükséges ahhoz, hogy a művelet végbemenjen. Ha a folyamat befejeződött, az egyéni HTTPS-állapot a  Azure Portal le lesz tiltva, és az egyéni tartomány párbeszédpanelén a három műveleti lépés befejezettként lesz megjelölve. Az egyéni tartomány már nem használhatja a HTTPS-t.

#### <a name="operation-progress"></a>Műveleti folyamat

Az alábbi táblázat a műveleti folyamatot mutatja, amely a HTTPS letiltásakor megy végbe. Miután letiltotta a HTTPS-t, három műveleti lépés jelenik meg az egyéni tartomány párbeszédpaneljében. Ahogy az egyes lépések aktívvá válnak, további részletek jelennek meg a lépés alatt. Miután egy lépés sikeresen befejeződik, egy zöld pipa jelenik meg mellette. 

| Műveleti folyamat | Művelet részletei | 
| --- | --- |
| 1. Kérés elküldése | A kérelem elküldése folyamatban van |
| 2. Tanúsítvány megszüntetése | Tanúsítvány törlése |
| 3. Befejezve | Tanúsítvány törölve |

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

* Töltsön fel egy tanúsítványt a Key Vault.
* Tartomány ellenőrzése.
* Engedélyezze a HTTPS-t az egyéni tartományhoz.

A következő oktatóanyagból megtudhatja, hogyan állíthat be földrajzi szűrési szabályzatot a Front Door számára.

> [!div class="nextstepaction"]
> [Geoszűrés szabályzatának beállítása](front-door-geo-filtering.md)
