---
title: 'Oktatóanyag: HTTPS konfigurálása Azure CDN egyéni tartományon'
description: Ebben az oktatóanyagban megismerheti, hogyan engedélyezheti és tilthatja le a HTTPS-t az Azure CDN-végpont egyéni tartományában.
services: cdn
author: asudbring
ms.service: azure-cdn
ms.topic: tutorial
ms.date: 01/27/2021
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: c4ad270b989e0e212c1d362ae4bfafc91fe07f3e
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98943549"
---
# <a name="tutorial-configure-https-on-an-azure-cdn-custom-domain"></a>Oktatóanyag: HTTPS konfigurálása Azure CDN egyéni tartományon

Ez az oktatóanyag bemutatja, hogyan lehet engedélyezni a HTTPS-protokollt egy Azure CDN-végponthoz kapcsolódó egyéni tartomány esetében. 

Az egyéni tartomány HTTPS-protokollja (például https: \/ /www.contoso.com), gondoskodik róla, hogy a bizalmas adatokat a TLS/SSL protokollon keresztül biztonságosan továbbítsa a rendszer. Ha a böngésző HTTPS-kapcsolaton keresztül csatlakozik, a böngésző ellenőrzi a webhely tanúsítványát. A böngésző ellenőrzi, hogy egy megbízható hitelesítésszolgáltató adta-e ki. Ez az eljárás védelmet nyújt webalkalmazásai számára a támadásokkal szemben.

Az Azure CDN alapértelmezés szerint támogatja a HTTPS-t a CDN-végpontok gazdaneve esetében. Ha például CDN-végpontot hoz létre (pl. https:\//contoso.azureedge.net), a HTTPS automatikusan engedélyezve lesz.  

Az egyéni HTTPS szolgáltatás legfőbb jellemzői a következők:

- Többletköltség nélkül: a tanúsítvány beszerzése vagy megújítása nem jár, és a HTTPS-forgalomra nem kell külön fizetni. Csak a CDN-ből kimenő GB-forgalomért kell fizetnie.

- Egyszerű engedélyezés: a kiépítés egy kattintással elvégezhető az [Azure Portalon](https://portal.azure.com) keresztül. A szolgáltatás engedélyezéséhez REST API-k, valamint más fejlesztői eszközök is használhatók.

- A tanúsítványok teljes körű felügyelete elérhető: 
    * A rendszer az összes tanúsítványt beszerzéssel és felügyelettel kezeli. 
    * A rendszer automatikusan kiépíti és megújítja a tanúsítványokat a lejárat előtt.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> - HTTPS-protokoll engedélyezése az egyéni tartományon
> - CDN által kezelt tanúsítvány használata 
> - Saját tanúsítvány használata
> - A tartomány érvényesítése
> - HTTPS-protokoll letiltása az egyéni tartományon

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)] 

Az oktatóanyag lépéseinek elvégzése előtt hozzon létre egy CDN-profilt és legalább egy CDN-végpontot. További információk: [Gyors útmutató: Azure CDN-profil és -végpont létrehozása](cdn-create-new-endpoint.md)

Azure CDN egyéni tartomány hozzárendelése a CDN-végponthoz. További információ: [oktatóanyag: egyéni tartomány hozzáadása az Azure CDN-végponthoz](cdn-map-content-to-custom-domain.md).

> [!IMPORTANT]
> A CDN által felügyelt tanúsítványok nem érhetők el gyökér-vagy APEX-tartományokhoz. Ha a Azure CDN az egyéni tartomány egy gyökér-vagy APEX-tartomány, akkor a saját tanúsítvány használata funkciót kell használnia. 
>

---

## <a name="tlsssl-certificates"></a>TLS/SSL-tanúsítványok

Ha engedélyezni szeretné a HTTPS-t egy Azure CDN egyéni tartományon, TLS/SSL-tanúsítványt használjon. Ön úgy dönt, hogy egy Azure CDN által felügyelt tanúsítványt használ, vagy használja a tanúsítványt.

# <a name="option-1-default-enable-https-with-a-cdn-managed-certificate"></a>[1. lehetőség (alapértelmezett): A HTTPS engedélyezése a CDN által kezelt tanúsítvánnyal](#tab/option-1-default-enable-https-with-a-cdn-managed-certificate)

Azure CDN kezeli a Tanúsítványkezelő feladatait, például a beszerzést és a megújítást. A szolgáltatás engedélyezése után a folyamat azonnal elindul. 

Ha az egyéni tartomány már le van képezve a CDN-végpontra, nincs szükség további műveletre. Az Azure CDN automatikusan feldolgozza a lépéseket és végrehajtja a kérést.

Ha az egyéni tartomány máshol van leképezve, az e-mail-cím használatával ellenőrizheti a tartomány tulajdonjogát.

Kövesse az alábbi lépéseket a HTTPS engedélyezéséhez egy egyéni tartományon:

1. Nyissa meg a [Azure Portal](https://portal.azure.com) a Azure CDN által felügyelt tanúsítvány megkereséséhez. Keresse meg és válassza ki a **CDN-profilokat**. 

2. Válassza ki a profilt:
    * **Azure CDN standard a Microsofttól**
    * **Azure CDN standard a Akamai**
    * **Azure CDN standard a Verizontól**
    * **Prémium Azure CDN a Verizontól**

3. A CDN-végpont listájából válassza ki az egyéni tartományt tartalmazó végpontot.

    ![Végpontlista](./media/cdn-custom-ssl/cdn-select-custom-domain-endpoint.png)

    Megjelenik a **Végpont** lap.

4. Az egyéni tartományok listájából válassza ki azt az egyéni tartományt, amelyen engedélyezni szeretné a HTTPS-t.

    ![Képernyőfelvétel: az egyéni tartomány oldal, a saját tanúsítvány használatára szolgáló lehetőséggel.](./media/cdn-custom-ssl/cdn-custom-domain.png)

    Megjelenik az **Egyéni tartomány** lap.

5. A Tanúsítványkezelés típusa területen válassza a **CDN által felügyelt** lehetőséget.

6. Válassza a **Bekapcsolás** lehetőséget a HTTPS engedélyezéséhez.

    ![Egyéni tartomány HTTPS-állapota](./media/cdn-custom-ssl/cdn-select-cdn-managed-certificate.png)

7. Folytassa [a tartomány érvényességének ellenőrzésével](#validate-the-domain).


# <a name="option-2-enable-https-with-your-own-certificate"></a>[2. lehetőség: A HTTPS engedélyezése saját tanúsítvánnyal](#tab/option-2-enable-https-with-your-own-certificate)

> [!IMPORTANT]
> Ez a beállítás csak a **Microsoft Azure CDN** és **Azure CDN Verizon** -profilokból érhető el. 
>
 
A saját tanúsítványát is használhatja a HTTPS szolgáltatás engedélyezéséhez. Ez a folyamat Azure Key Vault-integrációval történik, amely lehetővé teszi a tanúsítványok biztonságos tárolását. Azure CDN ezt a biztonságos mechanizmust használja a tanúsítvány beszerzéséhez, és néhány további lépést is igényel. A TLS/SSL-tanúsítvány létrehozásakor létre kell hoznia egy engedélyezett hitelesítésszolgáltatóval (CA). Másként, nem engedélyezett CA használata igénybe vétele esetén a kérelme vissza lesz utasítva. Az engedélyezett CA-k listája megtalálható az [Engedélyezett hitelesítésszolgáltatók egyéni HTTPS engedélyezéséhez az Azure CDN-en](cdn-troubleshoot-allowed-ca.md). A **Verizon Azure CDN** esetében minden érvényes hitelesítésszolgáltató el lesz fogadva. 

### <a name="prepare-your-azure-key-vault-account-and-certificate"></a>Az Azure Key Vault-fiók és a tanúsítvány előkészítése
 
1. Azure Key Vault: Rendelkeznie kell egy futó Azure Key Vault-fiókkal abban az előfizetésben, amelyben az Azure CDN-profil és azok a CDN-végpontok találhatók, amelyekhez egyéni HTTPS-t szeretne engedélyezni. Ha még nem rendelkezik Azure Key Vault-fiókkal, hozzon létre egyet.
 
2. Azure Key Vault tanúsítványok: Ha rendelkezik tanúsítvánnyal, töltse fel közvetlenül a Azure Key Vault-fiókjába. Ha nem rendelkezik tanúsítvánnyal, hozzon létre egy új tanúsítványt közvetlenül a Azure Key Vaulton keresztül.

### <a name="register-azure-cdn"></a>Az Azure CDN regisztrálása

Regisztrálja az Azure CDN-t alkalmazásként az Azure Active Directoryjában PowerShellen keresztül.

1. Szükség esetén telepítse a [Azure PowerShellt](/powershell/azure/install-az-ps) a helyi gépre.

2. Futtassa a PowerShellben az alábbi parancsot:

     `New-AzADServicePrincipal -ApplicationId "205478c0-bd83-4e1b-a9d6-db63a3e1e1c8"`
    > [!NOTE]
    > a **205478c0-bd83-4e1b-a9d6-db63a3e1e1c8** a **Microsoft. AzureFrontDoor-CDN** szolgáltatás egyszerű szolgáltatása.

    ```bash  
    New-AzADServicePrincipal -ApplicationId "205478c0-bd83-4e1b-a9d6-db63a3e1e1c8"

    Secret                : 
    ServicePrincipalNames : {205478c0-bd83-4e1b-a9d6-db63a3e1e1c8, 
                                https://microsoft.onmicrosoft.com/033ce1c9-f832-4658-b024-ef1cbea108b8}
    ApplicationId         : 205478c0-bd83-4e1b-a9d6-db63a3e1e1c8
    ObjectType            : ServicePrincipal
    DisplayName           : Microsoft.AzureFrontDoor-Cdn
    Id                    : c87be08f-686a-4d9f-8ef8-64707dbd413e
    Type                  :
    ```
### <a name="grant-azure-cdn-access-to-your-key-vault"></a>Hozzáférés biztosítása az Azure CDN számára a Key Vaulthoz
 
Adjon engedélyt az Azure CDN számára, hogy hozzáférhessen az Azure Key Vault-fiókjában tárolt tanúsítványokhoz (titkos kódokhoz).

1. A **Settings (beállítások** ) szakaszban a Key vaultban válassza a **hozzáférési szabályzatok** lehetőséget. A jobb oldali ablaktáblán válassza a **+ hozzáférési házirend hozzáadása** lehetőséget:

    :::image type="content" source="./media/cdn-custom-ssl/cdn-new-access-policy.png" alt-text="Kulcstartó-hozzáférési szabályzat létrehozása a CDN-hez" border="true":::

2. A **hozzáférési szabályzat hozzáadása** lapon válassza a **nincs kiválasztva** lehetőséget a **rendszerbiztonsági tag kiválasztása** elemnél. A **résztvevő** lapon adja meg a **205478c0-bd83-4e1b-a9d6-db63a3e1e1c8**. Válassza a **Microsoft. AzureFrontdoor – CDN** lehetőséget.  Válassza a **kiválasztás** lehetőséget:

2. A **rendszerbiztonsági tag kiválasztása** területen keressen **205478c0-bd83-4e1b-a9d6-db63a3e1e1c8**, majd válassza a **Microsoft. AzureFrontDoor-CDN** lehetőséget. Válassza a **Kiválasztás** lehetőséget

    :::image type="content" source="./media/cdn-custom-ssl/cdn-access-policy-settings.png" alt-text="Azure CDN egyszerű szolgáltatásnév kiválasztása" border="true":::
    
3. Válassza ki a **tanúsítványok engedélyeit**. Jelölje be a **beolvasás** és a **lista** jelölőnégyzetét, hogy a CDN engedélyt kapjon a tanúsítványok lekéréséhez és listázásához.

4. Válassza a **titkos engedélyek** lehetőséget. Jelölje be a Get és List ( **beolvasás** és **Listázás** ) jelölőnégyzetet a CDN-engedélyek beszerzéséhez és a titkok listázásához:

    :::image type="content" source="./media/cdn-custom-ssl/cdn-vault-permissions.png" alt-text="Válassza ki a CDN engedélyeit a kulcstartóhoz" border="true":::

5. Válassza a **Hozzáadás** elemet. 

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
  
6. A tanúsítvány használatakor a tartomány érvényesítése nem kötelező. Továbbra is [várjon a propagálásra](#wait-for-propagation).

---

## <a name="validate-the-domain"></a>A tartomány érvényesítése

Ha a használatban lévő egyéni tartomány egy CNAME rekorddal rendelkező egyéni végpontra van leképezve, vagy saját tanúsítványt használ, folytassa a [CDN-végpontra leképezett egyéni tartománnyal](#custom-domain-is-mapped-to-your-cdn-endpoint-by-a-cname-record). 

Ellenkező esetben, ha a végpont CNAME rekordjának bejegyzése már nem létezik, vagy a cdnverify altartományt tartalmazza, folytassa a [CDN-végpontra nem leképezett egyéni tartománnyal](#custom-domain-isnt-mapped-to-your-cdn-endpoint).

### <a name="custom-domain-is-mapped-to-your-cdn-endpoint-by-a-cname-record"></a>Az egyéni tartomány le van képezve a CDN-végpontra egy CNAME rekord révén

Amikor egyéni tartományt adott hozzá a végponthoz, létrehozott egy CNAME rekordot a CDN-végponthoz hozzárendelt DNS-tartományregisztrálónál. 

Ha a CNAME rekord még létezik, és nem tartalmazza a cdnverify altartományt, akkor a DigiCert-HITELESÍTÉSSZOLGÁLTATÓ használja az egyéni tartomány tulajdonjogának automatikus ellenőrzésére. 

Ha saját tanúsítványt használ, a tartomány érvényesítése nem szükséges.

A CNAME-rekordnak a következő formátumúnak kell lennie:

* A *név* az Ön egyéni tartománynevét adja meg.
* Az *érték* a CDN-végpont állomásneve.

| Név            | Típus  | Érték                 |
|-----------------|-------|-----------------------|
| <www.contoso.com> | CNAME | contoso.azureedge.net |

A CNAME rekordokkal kapcsolatos további információért tekintse meg a [CNAME DNS-rekord létrehozását ismertető](./cdn-map-content-to-custom-domain.md) részt.

Ha a CNAME-rekord formátuma megfelelő, a DigiCert automatikusan ellenőrzi az egyéni tartománynevet, és létrehoz egy tanúsítványt a tartományhoz. A DigitCert nem küld visszaigazoló e-mailt, és nem kell jóváhagynia a kérést. A tanúsítvány egy évig érvényes, és az érvényesség lejárta előtt automatikusan megújul. Továbbra is [várjon a propagálásra](#wait-for-propagation). 

Az automatikus érvényesítés általában néhány órát vesz igénybe. Ha nem látja a tartományt 24 órán belül érvényesítve, nyisson meg egy támogatási jegyet.

>[!NOTE]
>Ha van egy Hitelesítésszolgáltatói engedélyezési (CAA-) rekordja a DNS-szolgáltatónál, tartalmaznia kell a DigiCertet mint érvényes hitelesítésszolgáltatót. A CAA-rekord lehetővé teszi a tartomány tulajdonosai számára, hogy megadják a DNS-szolgáltatóknál, hogy melyik hitelesítésszolgáltatók jogosultak a tartomány tanúsítványának kiállítására. Ha egy hitelesítésszolgáltató kérést kap egy CAA-rekorddal rendelkező tartomány tanúsítványának kiállítására, és a hitelesítésszolgáltató nem szerepel az engedélyezett kiállítók listáján, nem adhat ki tanúsítványt a tartománynak vagy altartománynak. További információ a CAA-rekordok kezelésével kapcsolatban: [CAA-rekordok kezelése](https://support.dnsimple.com/articles/manage-caa-record/). A CAA-rekordokhoz való eszközért lásd: [CAA-rekord segítő](https://sslmate.com/caa/).

### <a name="custom-domain-isnt-mapped-to-your-cdn-endpoint"></a>Az egyéni tartomány nincs leképezve a CDN-végpontra

>[!NOTE]
>Ha **Azure CDNt** használ a Akamai-ból, a következő CNAME-t be kell állítani az automatikus tartomány-ellenőrzés engedélyezéséhez. "_acme-Challenge. &lt; egyéni tartomány hostname &gt; -> CNAME-> &lt; egyéni tartomány hostname &gt; . AK-Acme-Challenge.azureedge.net "

Ha a CNAME rekord bejegyzése tartalmazza a cdnverify altartományt, kövesse az ebben a lépésben szereplő további utasításokat.

A DigiCert ellenőrző e-mailt küld a következő e-mail-címekre. Ellenőrizze, hogy jóváhagyhatja-e közvetlenül az alábbi címek valamelyikét:

* **admin@your-domain-name.com** 
* **administrator@your-domain-name.com** 
* **webmaster@your-domain-name.com** 
* **hostmaster@your-domain-name.com**  
* **postmaster@your-domain-name.com**  

Egy e-mailt kell kapnia néhány percen belül, hogy jóváhagyja a kérést. Ha levélszemét-szűrőt használ, adja hozzá az verification@digicert.com engedélyezési listához. Ha 24 órán belül nem kapja meg az e-mailt, lépjen kapcsolatba a Microsoft támogatási szolgálatával.
    
![Tartományérvényesítési e-mail](./media/cdn-custom-ssl/domain-validation-email.png)

A jóváhagyási hivatkozás kiválasztásakor a következő online jóváhagyási űrlapot kell átirányítani: 
    
![Tartományérvényesítési űrlap](./media/cdn-custom-ssl/domain-validation-form.png)

Kövesse az űrlap utasításait; két ellenőrzési lehetősége van:

- Az ugyanazon gyökértartományhoz tartozó ugyanazon fiók összes jövőbeli kérést jóváhagyhatja; például: contoso.com. Ez a módszer akkor ajánlott, ha más egyéni tartományokat szeretne hozzáadni ugyanahhoz a gyökértartomány-tartományhoz.

- Jóváhagyhatja az adott gazdanevet, amelyet a kéréshez használtak. További jóváhagyásra van szükség a későbbi kérésekhez.

A jóváhagyás után a DigiCert befejezi az egyéni tartománynév tanúsítványának létrehozását. A tanúsítvány egy évig érvényes, és a lejárta előtt automatikusan megújul.

## <a name="wait-for-propagation"></a>Várakozás a propagálásra

A tartománynév érvényesítése után 6-8 óra szükséges ahhoz, hogy az egyéni tartományhoz tartozó HTTPS szolgáltatás aktiválódjon. Ha a folyamat befejeződik, a Azure Portal egyéni HTTPS-állapota **engedélyezve** értékre változik. Az egyéni tartomány párbeszédpanel négy műveletének lépései befejezettként vannak megjelölve. Az egyéni tartomány ezzel készen áll a HTTPS használatára.

![HTTPS-párbeszédpanel engedélyezése](./media/cdn-custom-ssl/cdn-enable-custom-ssl-complete.png)

### <a name="operation-progress"></a>Műveleti folyamat

Az alábbi táblázat a műveleti folyamatot mutatja, amely a HTTPS engedélyezésekor megy végbe. Miután engedélyezte a HTTPS-t, négy műveleti lépés jelenik meg az egyéni tartomány párbeszédpaneljében. Ahogy az egyes lépések aktívvá válnak, az egyéb allépések részletei a lépés alatt jelennek meg, ahogy halad. Nem minden allépés fog előfordulni. Miután egy lépés sikeresen befejeződik, egy zöld pipa jelenik meg mellette. 

| Műveleti lépés | Műveleti allépés részletei | 
| --- | --- |
| 1. Kérés elküldése | Kérés elküldése |
| | A HTTPS-kérés küldése folyamatban van. |
| | A HTTPS-kérés elküldése sikerült. |
| 2. Tartományérvényesítés | A tartomány automatikusan érvényesítve lesz, ha a CNAME-végponthoz van rendelve. Máskülönben visszaigazolási kérelem érkezik a tartomány regisztrációs rekordjában megadott e-mail-címre (WHOIS regisztráló).|
| | Sikerült ellenőrizni a tartomány tulajdonjogát. |
| | A tartomány tulajdonjogának ellenőrzési kérelme lejárt (az ügyfél valószínűleg nem válaszolt 6 napon belül). A HTTPS-t nem lehet engedélyezni a tartományon. * |
| | A tartomány tulajdonjogának ellenőrzésére vonatkozó kérelem vissza lett utasítva az ügyfél által. A HTTPS-t nem lehet engedélyezni a tartományon. * |
| 3. Tanúsítvány üzembe helyezése | A hitelesítésszolgáltató jelenleg azon tanúsítvány kibocsátását végzi, amely a HTTPS tartományban való engedélyezéséhez szükséges. |
| | A tanúsítvány kibocsátása megtörtént, és folyamatban van a CDN-hálózatban való üzembe helyezése. A folyamat akár hat órát is igénybe vehet. |
| | Sikerült üzembe helyezni a tanúsítványt a CDN-hálózatban. |
| 4. Befejezve | Sikerült engedélyezni a HTTPS-t a tartományban. |

\* Ez az üzenet csak akkor jelenik meg, ha hiba történt. 

Ha a kérelem elküldése előtt hiba történik, a következő hibaüzenet jelenik meg:

<code>
We encountered an unexpected error while processing your HTTPS request. Please try again and contact support if the issue persists.
</code>



## <a name="clean-up-resources---disable-https"></a>Az erőforrások eltávolítása – HTTPS letiltása

Ebből a szakaszból megtudhatja, hogyan tilthatja le a HTTPS-t az egyéni tartományhoz.

### <a name="disable-the-https-feature"></a>HTTPS szolgáltatás letiltása 

1. A [Azure Portal](https://portal.azure.com)keresse meg és válassza ki a **CDN-profilokat**. 

2. Válassza ki a **Microsofttól származó Azure CDN szabványt**, **Azure CDN a standardot a verizontól**, vagy **Azure CDN Premiumot a Verizon** -profilból.

3. A végpontok listájában válassza ki az egyéni tartományt tartalmazó végpontot.

4. Válassza ki azt az egyéni tartományt, amely esetében le szeretné tiltani a HTTPS-t.

    ![Egyéni tartományok listája](./media/cdn-custom-ssl/cdn-custom-domain-HTTPS-enabled.png)

5. A HTTPS letiltásához válassza a **ki** lehetőséget, majd kattintson az **alkalmaz** gombra.

    ![Egyéni HTTPS párbeszédpanel](./media/cdn-custom-ssl/cdn-disable-custom-ssl.png)

### <a name="wait-for-propagation"></a>Várakozás a propagálásra

Az egyéni tartomány HTTPS szolgáltatásának letiltása után 6-8 óra szükséges ahhoz, hogy a művelet végbemenjen. Ha a folyamat befejeződött, a Azure Portal egyéni HTTPS-állapota **Letiltva** értékre változik. Az egyéni tartomány párbeszédpanelen a három művelet lépéseit befejezettként jelöli meg a rendszer. Az egyéni tartomány már nem használhatja a HTTPS-t.

![HTTPS letiltása párbeszédpanel](./media/cdn-custom-ssl/cdn-disable-custom-ssl-complete.png)

#### <a name="operation-progress"></a>Műveleti folyamat

Az alábbi táblázat a műveleti folyamatot mutatja, amely a HTTPS letiltásakor megy végbe. A HTTPS letiltása után három művelet lépés jelenik meg az egyéni tartomány párbeszédpanelen. Ha egy lépés aktívvá válik, a részletek a lépés alatt jelennek meg. Miután egy lépés sikeresen befejeződik, egy zöld pipa jelenik meg mellette. 

| Műveleti folyamat | Művelet részletei | 
| --- | --- |
| 1. Kérés elküldése | A kérelem elküldése folyamatban van |
| 2. Tanúsítvány megszüntetése | Tanúsítvány törlése |
| 3. Befejezve | Tanúsítvány törölve |

## <a name="frequently-asked-questions"></a>Gyakori kérdések

1. *Ki a tanúsítványszolgáltató és milyen típusú tanúsítvány van használatban?*

    A Digicert által biztosított dedikált tanúsítvány a következő egyéni tartományhoz használható:
    
    * **Azure CDN a Verizontól**
    * **Azure CDN a Microsofttól**

2. *IP-címalapú vagy SNI TLS/SSL-t használ?*

    A **Verizon Azure CDN** és a **Microsoft CDN Standard** is SNI TLS/SSL-t használ.

3. *Mi a teendő, ha nem kapok visszaigazolási e-mailt a DigiCerttől?*

    Ha nem használja a *cdnverify* altartományt, és a CNAME-bejegyzés a végponti állomásnévhez van hozzárendelve, akkor nem fog tartomány-ellenőrző e-mailt kapni.
     
    A hitelesítés automatikusan történik. Máskülönben, ha nem rendelkezik CNAME-bejegyzéssel, és 24 órán belül nem kapott e-mailt, forduljon a Microsoft támogatási szolgálatához.

4. *A SAN tanúsítvány használata kevésbé biztonságos, mint egy dedikált tanúsítvány használata?*
    
    A SAN-tanúsítvány ugyanolyan titkosítási és biztonsági előírásokat követ, mint a dedikált tanúsítvány. Az összes kiállított TLS/SSL-tanúsítvány SHA-256-t használ a fokozott kiszolgálói biztonsághoz.

5. *Szükségem van hitelesítésszolgáltató engedélyezési rekordra a DNS szolgáltatómnál?*

    A hitelesítésszolgáltató engedélyezési rekordja jelenleg nem szükséges. Viszont ha van ilyenje, mindenképpen tartalmaznia kell a DigiCertet mint érvényes CA-t.

6. *2018. június 20-án a Verizon Azure CDN a SNI TLS/SSL-lel rendelkező dedikált tanúsítvány használatával indult el. Mi történik a meglévő egyéni tartományokkal a tulajdonos alternatív nevek (SAN) tanúsítvány és az IP-alapú TLS/SSL használatával?*

    Ha a Microsoft elemzése szerint az alkalmazásába csak SNI-ügyfélkérelmek érkeznek, a meglévő tartományait a következő hónapokban fokozatosán migráljuk egyetlen tanúsítvány használatára. 
    
    Ha a rendszer nem SNI ügyfeleket észlel, a tartományok a TÁROLÓHÁLÓZATI tanúsítványban maradnak az IP-alapú TLS/SSL protokollal. A szolgáltatásnak vagy a nem SNI ügyfeleknek küldött kérések nem érintettek.

7. *Hogyan működik a tanúsítvány megújítása a saját tanúsítvánnyal?*

    Ahhoz, hogy egy újabb tanúsítvány legyen telepítve a PoP-infrastruktúrában, töltse fel az új tanúsítványt az Azure kulcstartóba. Azure CDN TLS-beállításainál válassza a legújabb tanúsítvány verzióját, és kattintson a Mentés gombra. A Azure CDN ezután propagálja az új frissített tanúsítványt. 

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
> [Oktatóanyag: Azure CDN gyorsítótárazási szabályainak beállítása](cdn-caching-rules-tutorial.md)