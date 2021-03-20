---
title: Oktatóanyag – a HTTPS konfigurálása egyéni tartományon az Azure-előtérben | Microsoft Docs
description: Ebből az oktatóanyagból megtudhatja, hogyan engedélyezheti és tilthatja le a HTTPS-t az Azure-beli bejárati ajtó konfigurációjában egy egyéni tartományhoz.
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/21/2020
ms.author: duau
ms.openlocfilehash: 6c6d33a36c4a0b71932e8c19c8f6dd105c33817c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101740783"
---
# <a name="tutorial-configure-https-on-a-front-door-custom-domain"></a>Oktatóanyag: HTTPS konfigurálása Front Door egyéni tartományon

Ez az oktatóanyag bemutatja, hogyan lehet engedélyezni a HTTPS-protokollt az előtérbeli gazdagépek szakaszban egy Front Doorhoz kapcsolódó egyéni tartomány esetében. A HTTPS-protokoll egyéni tartományon belüli használatával (például https:\//www.contoso.com) biztosítható, hogy a bizalmas adatokat a rendszer biztonságosan, TLS/SSL-titkosításon keresztül továbbítsa az Interneten. Amikor a böngésző HTTPS-protokollal kapcsolódik egy webhelyhez, ellenőrzi a webhely biztonsági tanúsítványát, és megállapítja, hogy azt arra jogosult hitelesítésszolgáltató adta-e ki. Ez az eljárás védelmet nyújt webalkalmazásai számára a támadásokkal szemben.

Az Azure bejárati ajtaja alapértelmezés szerint támogatja a HTTPS használatát a bejárati ajtó alapértelmezett állomásneve esetében. Ha például létrehoz egy bejárati ajtót (például `https://contoso.azurefd.net` ), a https automatikusan engedélyezve lesz a következőre irányuló kérésekhez: `https://contoso.azurefd.net` . Azonban a „www.contoso.com” egyéni tartomány regisztrálása után külön engedélyeznie kell a HTTPS-t az előtérbeli gazdagépen.   

Az egyéni HTTPS szolgáltatás legfőbb jellemzői a következők:

- Nincsenek további költségek: a tanúsítvány megszerzése vagy megújítása ingyenes, és a HTTPS-forgalom sem von maga után további költségeket. 

- Egyszerű engedélyezés: a kiépítés egy kattintással elvégezhető az [Azure Portalon](https://portal.azure.com) keresztül. A szolgáltatás engedélyezéséhez REST API-k, valamint más fejlesztői eszközök is használhatók.

- Teljes körű tanúsítványkezelés érhető el: nem kell foglalkoznia a tanúsítványok beszerzésével és kezelésével. A tanúsítványok üzembe helyezése és megújítása automatikusan megtörténik a lejárat előtt, így nem kell attól tartani, hogy a szolgáltatás megszakad egy lejárt tanúsítvány miatt.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> - HTTPS-protokoll engedélyezése az egyéni tartományon
> - AFD által kezelt tanúsítvány használata 
> - Saját tanúsítvány, azaz egyéni TLS/SSL-tanúsítvány használata
> - A tartomány érvényesítése
> - HTTPS-protokoll letiltása az egyéni tartományon


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elvégezhetné a jelen oktatóanyag lépéseit, először létre kell hoznia egy legalább egy egyéni tartománnyal regisztrált Front Doort. További információkért lásd: [Útmutató: Egyéni tartomány hozzáadása a Front Doorhoz](front-door-custom-domain.md)

## <a name="tlsssl-certificates"></a>TLS/SSL-tanúsítványok

Ha engedélyezni szeretné, hogy a HTTPS protokoll biztonságos módon kézbesítse a tartalmat egy bejárati ajtó egyéni tartományán, TLS/SSL-tanúsítványt kell használnia. Dönthet úgy is, hogy az Azure bejárati ajtó által felügyelt tanúsítványt használ, vagy használja a saját tanúsítványát.


### <a name="option-1-default-use-a-certificate-managed-by-front-door"></a>1. lehetőség (alapértelmezett): A Front Door által felügyelt tanúsítvány használata

Ha az Azure bejárati ajtó által kezelt tanúsítványt használ, a HTTPS funkció csak néhány kattintással bekapcsolható. Az Azure bejárati ajtaja teljesen kezeli a Tanúsítványkezelő feladatokat, például a beszerzést és a megújítást. A szolgáltatás engedélyezése után a folyamat azonnal elindul. Ha az egyéni tartomány már hozzá van rendelve a Front Door alapértelmezett előtérbeli gazdagépéhez (`{hostname}.azurefd.net`), nincs további teendő. A Front Door automatikusan feldolgozza a lépéseket és végrehajtja a kérést. Ha azonban az egyéni tartomány más helyre van leképezve, meg kell erősítenie a tartomány tulajdonjogát e-mailben.

Kövesse az alábbi lépéseket a HTTPS engedélyezéséhez egy egyéni tartományon:

1. Az [Azure Portalon](https://portal.azure.com) keresse meg **Front Door**-profilját.

2. Az előtérbeli gazdagépek listájából válassza ki azt az egyéni tartományt, amelyen engedélyezni szeretné a HTTPS-t az egyéni tartomány tárolásához.

3. Az **egyéni tartományi HTTPS** szakaszban kattintson az **Engedélyezve** elemre, és válassza ki a **Front Door által felügyelt** tanúsítványforrást.

4. Kattintson a Mentés gombra.

5. Folytassa [A tartomány érvényesítése](#validate-the-domain) című szakasszal.

> [!NOTE]
> A AFD által felügyelt tanúsítványok esetében a DigiCert 64 karakteres korlátja kényszerítve van. Ha túllépi a korlátot, az érvényesítés sikertelen lesz.


### <a name="option-2-use-your-own-certificate"></a>2. lehetőség: Saját tanúsítvány használata

A saját tanúsítványát is használhatja a HTTPS szolgáltatás engedélyezéséhez. Ez a folyamat Azure Key Vault-integrációval történik, amely lehetővé teszi a tanúsítványok biztonságos tárolását. Az Azure bejárati ajtó ezt a biztonságos mechanizmust használja a tanúsítvány beszerzéséhez, és néhány további lépést is igényel. A TLS/SSL-tanúsítvány létrehozásakor létre kell hoznia egy engedélyezett hitelesítésszolgáltatóval (CA). Másként, nem engedélyezett CA használata igénybe vétele esetén a kérelme vissza lesz utasítva. Az engedélyezett hitelesítésszolgáltatók listájáért lásd: az [Azure-beli bejárati ajtón az egyéni HTTPS engedélyezésének engedélyezése](front-door-troubleshoot-allowed-ca.md)a hitelesítésszolgáltatók számára.

#### <a name="prepare-your-azure-key-vault-account-and-certificate"></a>Az Azure Key Vault-fiók és a tanúsítvány előkészítése
 
1. Azure Key Vault: Rendelkeznie kell egy futó Azure Key Vault-fiókkal abban az előfizetésben, amelyben az a Front Door található, amelyhez egyéni HTTPS-t szeretne engedélyezni. Ha még nem rendelkezik Azure Key Vault-fiókkal, hozzon létre egyet.

> [!WARNING]
> Az Azure bejárati ajtaja jelenleg csak az előfizetésben lévő Key Vault fiókokat támogatja, mint a bejárati ajtó konfigurálása. Ha nem a Front Door-előfizetéshez tartozó Key Vault-tárolót választ, az hibát eredményez.

2. Azure Key Vault-tanúsítványok: Ha már rendelkezik tanúsítvánnyal, feltöltheti közvetlenül az Azure Key Vault-fiókjába, vagy létrehozhat egy új tanúsítványt közvetlenül az Azure Key Vaultban azokkal a hitelesítésszolgáltató (CA) partnerekkel, amelyekkel az Azure Key Vault integrálva van. **Titkos** tanúsítvány helyett a tanúsítványt **tanúsítvány** -objektumként töltse fel.

> [!NOTE]
> Saját TLS/SSL-tanúsítvány esetén a bejárati ajtó nem támogatja az EC titkosítási algoritmusokkal rendelkező tanúsítványokat.

#### <a name="register-azure-front-door"></a>Azure-beli bejárati ajtó regisztrálása

Regisztrálja az Azure-hoz tartozó egyszerű szolgáltatást a Azure Active Directory a PowerShellen keresztüli alkalmazásként.

> [!NOTE]
> Ehhez a művelethez globális rendszergazdai jogosultságok szükségesek, és bérlőn csak **egyszer** kell végrehajtani.

1. Ha szükséges, telepítse az [Azure PowerShell](/powershell/azure/install-az-ps) bővítményt a PowerShellhez a helyi számítógépen.

2. Futtassa a PowerShellben az alábbi parancsot:

     `New-AzADServicePrincipal -ApplicationId "ad0e1c7e-6d38-4ba4-9efd-0bc77ba9f037"`              

#### <a name="grant-azure-front-door-access-to-your-key-vault"></a>Azure-beli előtérben való hozzáférés biztosítása a kulcstartóhoz
 
Adja meg az Azure-előfizetési engedélyt a Azure Key Vault fiókban található tanúsítványok eléréséhez.

1. A Key Vault-fiók BEÁLLÍTÁSOK területén válassza a **Hozzáférési szabályzatok**, majd az **Új hozzáadása** lehetőséget új szabályzat létrehozásához.

2. A **Rendszerbiztonsági tag kijelölése** területen keresse meg az **ad0e1c7e-6d38-4ba4-9efd-0bc77ba9f037** elemet, és válassza ki a **Microsoft.Azure.Frontdoor** lehetőséget. Kattintson a **Kiválasztás** elemre.

3. A **titkos engedélyek** területen válassza a **beolvasás** lehetőséget, hogy a bejárati ajtó beolvassa a tanúsítványt.

4. A **tanúsítvány engedélyei** területen válassza a **beolvasás** lehetőséget, hogy a bejárati ajtó beolvassa a tanúsítványt.

5. Válassza az **OK** lehetőséget. 

    Az Azure bejárati ajtaja mostantól elérheti ezt a Key Vault és a Key Vault tárolt tanúsítványokat.
 
#### <a name="select-the-certificate-for-azure-front-door-to-deploy"></a>Tanúsítvány kiválasztása az Azure-beli bejárati ajtó üzembe helyezéséhez
 
1. Lépjen vissza a Front Doorhoz a portálon. 

2. Az egyéni tartományok listájából válassza ki azt az egyéni tartományt, amelyen engedélyezni szeretné a HTTPS-t.

    Megjelenik az **Egyéni tartomány** lap.

3. A Tanúsítványkezelés típusa területen válassza a **Saját tanúsítvány használata** lehetőséget. 

4. Az Azure bejárati ajtajához a Key Vault fiók előfizetése ugyanaz, mint a bejárati ajtónál. Válassza ki a Key Vaultot, a tanúsítványt (titkos kódot) és a tanúsítványverziót.

    Az Azure bejárati ajtó a következő információkat tartalmazza: 
    - Az előfizetés azonosítójához tartozó Key Vault-fiókok. 
    - A kiválasztott Key Vaultban található tanúsítványok (titkos kódok). 
    - A tanúsítvány elérhető verziói. 

> [!NOTE]
> Ha üresen hagyja a tanúsítvány verzióját, a következőhöz vezetne:
> - A tanúsítvány legújabb verziója kiválasztva.
> - A tanúsítványok a legújabb verzióra való automatikus elforgatása, amikor a tanúsítvány egy újabb verziója érhető el a Key Vault.
 
5. Saját tanúsítvány használatakor nem szükséges tartományérvényesítés. Lépjen tovább a [Várakozás a propagálásra](#wait-for-propagation) részhez.

## <a name="validate-the-domain"></a>A tartomány érvényesítése

Ha már rendelkezik használatban lévő egyéni tartománnyal, amely az egyéni végpontjára van leképezve egy CNAME rekorddal, vagy ha saját tanúsítványt használ, lépjen tovább a következőre:  
[Az egyéni tartomány le van képezve a Front Doorra](#custom-domain-is-mapped-to-your-front-door-by-a-cname-record). Ha már nem létezik a tartomány CNAME rekordjának bejegyzése, vagy az afdverify altartományt tartalmazza, lépjen tovább [Az egyéni tartomány nincs leképezve a Front Doorra](#custom-domain-is-not-mapped-to-your-front-door) című részre.

### <a name="custom-domain-is-mapped-to-your-front-door-by-a-cname-record"></a>Az egyéni tartomány le van képezve a Front Doorra egy CNAME rekorddal

Amikor hozzáadott egy egyéni tartományt a Front Door előtérbeli gazdagépeihez, létrehozott egy CNAME rekordot a saját tartományregisztrálójának DNS-táblájában, hogy leképezze a Front Door alapértelmezett .azurefd.net eszköznevére. Ha ez a CNAME rekord még létezik és nem tartalmazza az afdverify altartományt, a DigiCert hitelesítésszolgáltató arra használja, hogy automatikusan érvényesítse az egyéni tartomány tulajdonjogát. 

Ha saját tanúsítványt használ, nem szükséges tartományérvényesítés.

A CNAME rekordnak a következő formátumban kell lennie, ahol a *Név* az Ön egyéni tartományának neve, az *Érték* pedig a Front Door alapértelmezett .azurefd.net eszközneve:

| Név            | Típus  | Érték                 |
|-----------------|-------|-----------------------|
| <www.contoso.com> | CNAME | contoso.azurefd.net |

A CNAME rekordokkal kapcsolatos további információért tekintse meg a [CNAME DNS-rekord létrehozását ismertető](../cdn/cdn-map-content-to-custom-domain.md) részt.

Ha a CNAME rekordja a megfelelő formátumban van, a DigiCert automatikusan ellenőrzi az egyéni tartománynevet, és létrehoz egy dedikált tanúsítványt. A DigitCert nem küld visszaigazoló e-mailt, és nem kell jóváhagynia a kérést. A tanúsítvány egy évig érvényes, és az érvényesség lejárta előtt automatikusan megújul. Lépjen tovább a [Várakozás a propagálásra](#wait-for-propagation) részhez. 

Az automatikus érvényesítés általában eltart néhány percig. Ha a tartománya egy órán belül sincs érvényesítve, nyisson meg egy támogatási jegyet.

>[!NOTE]
>Ha van egy Hitelesítésszolgáltatói engedélyezési (CAA-) rekordja a DNS-szolgáltatónál, tartalmaznia kell a DigiCertet mint érvényes hitelesítésszolgáltatót. A CAA-rekord lehetővé teszi a tartomány tulajdonosai számára, hogy megadják a DNS-szolgáltatóknál, hogy melyik hitelesítésszolgáltatók jogosultak a tartomány tanúsítványának kiállítására. Ha egy hitelesítésszolgáltató kérést kap egy CAA-rekorddal rendelkező tartomány tanúsítványának kiállítására, és a hitelesítésszolgáltató nem szerepel az engedélyezett kiállítók listáján, nem adhat ki tanúsítványt a tartománynak vagy altartománynak. További információ a CAA-rekordok kezelésével kapcsolatban: [CAA-rekordok kezelése](https://support.dnsimple.com/articles/manage-caa-record/). A CAA-rekordokhoz való eszközért lásd: [CAA-rekord segítő](https://sslmate.com/caa/).

### <a name="custom-domain-is-not-mapped-to-your-front-door"></a>Az egyéni tartomány nincs leképezve a Front Doorra

Ha már nem létezik a végpont CNAME rekordjának bejegyzése, vagy az afdverify altartományt tartalmazza, kövesse az itt ismertetett lépéseket.

Miután engedélyezi a HTTPS-t az egyéni tartományhoz, A DigiCert CA érvényesíti a tartomány tulajdonjogát azáltal, hogy kapcsolatba lép a regisztrálójával a tartomány [WHOIS](http://whois.domaintools.com/) regisztrálójának információja alapján. A kapcsolatfelvétel a WHOIS-regisztrációban megadott e-mail-címen (alapértelmezett) vagy telefonszámon keresztül történik. A HTTPS csak a tartomány hitelesítése után aktiválódik az egyéni tartományon. A tartomány jóváhagyására hat munkanap áll rendelkezésére. A hat munkanapon belül jóvá nem hagyott kérelmek automatikusan törlődnek. 

![WHOIS-rekord](./media/front-door-custom-domain-https/whois-record.png)

A DigiCert további e-mail-címekre is küld megerősítő e-mailt. Ha a WHOIS-regisztráló információja bizalmas, erősítse meg, hogy a jóváhagyást el tudja végezni közvetlenül a következő címek egyikéről:

admin@&lt;az-ön-tartományneve.com&gt;  
administrator@&lt;az-ön-tartományneve.com&gt;  
webmaster@&lt;az-ön-tartományneve.com&gt;  
hostmaster@&lt;az-ön-tartományneve.com&gt;  
postmaster@&lt;az-ön-tartományneve.com&gt;  

Pár percen belül a következőhöz hasonló e-mailt kell kapnia, amely a kérés jóváhagyására kéri. Ha levélszemét-szűrőt használ, adja hozzá az admin@digicert.com engedélyezési listához. Ha 24 órán belül nem kapja meg az e-mailt, lépjen kapcsolatba a Microsoft támogatási szolgálatával.

Ha a jóváhagyási hivatkozásra kattint, a rendszer átirányítja egy online jóváhagyási űrlapra. Kövesse az űrlap utasításait; két ellenőrzési lehetősége van:

- Az ugyanazon gyökértartományhoz tartozó ugyanazon fiók összes jövőbeli kérést jóváhagyhatja; például: contoso.com. Ez akkor ajánlott, ha további egyéni tartományokat tervez hozzáadni ugyanazon gyökértartományhoz.

- Jóváhagyhatja az adott gazdanevet, amelyet a kéréshez használtak. A további kérésekhez további jóváhagyás szükséges.

A jóváhagyás után a DigiCert befejezi az egyéni tartománynév tanúsítványának létrehozását. A tanúsítvány egy évig érvényes, és a lejárta előtt automatikusan megújul.

## <a name="wait-for-propagation"></a>Várakozás a propagálásra

A tartománynév érvényesítése után 6-8 óra szükséges ahhoz, hogy az egyéni tartományhoz tartozó HTTPS szolgáltatás aktiválódjon. Ha a folyamat befejeződött, az egyéni HTTPS állapota **Engedélyezve** értékre vált az Azure Portalon, és a négy műveleti lépés az egyéni tartomány párbeszédpanelében befejezettnek lesz jelölve. Az egyéni tartomány ezzel készen áll a HTTPS használatára.

### <a name="operation-progress"></a>Műveleti folyamat

Az alábbi táblázat a műveleti folyamatot mutatja, amely a HTTPS engedélyezésekor megy végbe. Miután engedélyezte a HTTPS-t, négy műveleti lépés jelenik meg az egyéni tartomány párbeszédpaneljében. Ahogy az egyes lépések aktívvá válnak, a folyamat előrehaladtával további allépések részletei jelennek meg a lépés alatt. Nem minden allépés fog előfordulni. Miután egy lépés sikeresen befejeződik, egy zöld pipa jelenik meg mellette. 

| Műveleti lépés | Műveleti allépés részletei | 
| --- | --- |
| 1. Kérés elküldése | Kérés elküldése |
| | A HTTPS-kérés küldése folyamatban van. |
| | A HTTPS-kérés elküldése sikerült. |
| 2. Tartományérvényesítés | A tartomány automatikusan érvényesítve lesz, ha a CNAME révén le van képezve a Front Door alapértelmezett .azurefd.net előtérbeli gazdagépéhez. Ha nincs, akkor visszaigazolási kérelem érkezik a tartomány regisztrációs rekordjában megadott e-mail-címre (WHOIS regisztráló). Minél hamarabb igazolja vissza a tartományt. |
| | Sikerült ellenőrizni a tartomány tulajdonjogát. |
| | A tartomány tulajdonjogának ellenőrzési kérelme lejárt (az ügyfél valószínűleg nem válaszolt 6 napon belül). A HTTPS nem lesz engedélyezve a tartományon. * |
| | A tartomány tulajdonjogának ellenőrzésére vonatkozó kérelem vissza lett utasítva az ügyfél által. A HTTPS nem lesz engedélyezve a tartományon. * |
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

    Az Azure bejárati ajtaja a SNI TLS/SSL protokollt használja.

3. *Mi a teendő, ha nem kapok visszaigazolási e-mailt a DigiCerttől?*

    Ha van olyan CNAME-bejegyzése az egyéni tartomány esetében, amely közvetlenül a gazdanév végpontjára mutat (és nem használja az afdverify altartománynevet), nem fog a tartomány visszaigazolására vonatkozó e-mailt kapni. A hitelesítés automatikusan történik. Máskülönben, ha nem rendelkezik CNAME-bejegyzéssel, és 24 órán belül nem kapott e-mailt, forduljon a Microsoft támogatási szolgálatához.

4. *A SAN tanúsítvány használata kevésbé biztonságos, mint egy dedikált tanúsítvány használata?*
    
    A SAN-tanúsítvány ugyanolyan titkosítási és biztonsági előírásokat követ, mint a dedikált tanúsítvány. Az összes kiállított TLS/SSL-tanúsítvány SHA-256-t használ a fokozott kiszolgálói biztonsághoz.

5. *Szükségem van hitelesítésszolgáltató engedélyezési rekordra a DNS szolgáltatómnál?*

    Nem, hitelesítésszolgáltatói engedélyezési rekordra jelenleg nincs szükség. Viszont ha van ilyenje, mindenképpen tartalmaznia kell a DigiCertet mint érvényes CA-t.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az előző lépések során engedélyezte a HTTPS protokollt az egyéni tartományon. Ha már nem szeretné HTTPS-sel használni az egyéni tartományt, letilthatja a HTTPS-t a következő lépések végrehajtásával:

### <a name="disable-the-https-feature"></a>HTTPS szolgáltatás letiltása 

1. A [Azure Portal](https://portal.azure.com)tallózással keresse meg az **Azure-beli bejárati ajtó** konfigurációját.

2. Az előtérbeli gazdagépek listájában válassza ki azt az egyéni tartományt, amelyen le szeretné tiltani a HTTPS-t.

3. A HTTPS letiltásához kattintson a **Letiltva**, majd a **Mentés** gombra.

### <a name="wait-for-propagation"></a>Várakozás a propagálásra

Az egyéni tartomány HTTPS szolgáltatásának letiltása után 6-8 óra szükséges ahhoz, hogy a művelet végbemenjen. Ha a folyamat befejeződött, az egyéni HTTPS állapota **Letiltva** értékre vált az Azure Portalon, és a három műveleti lépés az egyéni tartomány párbeszédpanelében befejezettnek lesz jelölve. Az egyéni tartomány már nem használhatja a HTTPS-t.

#### <a name="operation-progress"></a>Műveleti folyamat

Az alábbi táblázat a műveleti folyamatot mutatja, amely a HTTPS letiltásakor megy végbe. Miután letiltotta a HTTPS-t, három műveleti lépés jelenik meg az egyéni tartomány párbeszédpaneljében. Ahogy az egyes lépések aktívvá válnak, további részletek jelennek meg a lépés alatt. Miután egy lépés sikeresen befejeződik, egy zöld pipa jelenik meg mellette. 

| Műveleti folyamat | Művelet részletei | 
| --- | --- |
| 1. Kérés elküldése | A kérelem elküldése folyamatban van |
| 2. Tanúsítvány megszüntetése | Tanúsítvány törlése |
| 3. Befejezve | Tanúsítvány törölve |

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

* Töltse fel a tanúsítványt a Key Vaultba.
* Tartomány érvényesítése.
* Engedélyezze a HTTPS-t az egyéni tartományhoz.

Ha meg szeretné tudni, hogyan állíthatja be a Geo-szűrési szabályzatot a bejárati ajtóhoz, folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [Geo-szűrési szabályzat beállítása](front-door-geo-filtering.md)