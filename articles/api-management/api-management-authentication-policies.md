---
title: Azure API Management hitelesítési házirendek | Microsoft Docs
description: Ismerje meg az Azure API Management használható hitelesítési házirendeket.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 061702a7-3a78-472b-a54a-f3b1e332490d
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/27/2021
ms.author: apimpm
ms.openlocfilehash: 22d2960801cac2222f868c384a55b4bf436bc75b
ms.sourcegitcommit: 740698a63c485390ebdd5e58bc41929ec0e4ed2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99492603"
---
# <a name="api-management-authentication-policies"></a>API Management-hitelesítési szabályzatok
Ez a témakör az alábbi API Management szabályzatokra mutató hivatkozást tartalmaz. A házirendek hozzáadásával és konfigurálásával kapcsolatos információkért lásd: [szabályzatok API Management](./api-management-policies.md).

##  <a name="authentication-policies"></a><a name="AuthenticationPolicies"></a> Hitelesítési házirendek

-   Egyszerű hitelesítéssel [hitelesítheti alapszintű](api-management-authentication-policies.md#Basic) hitelesítést a háttér-szolgáltatással.

-   [Hitelesítés az ügyféltanúsítvány](api-management-authentication-policies.md#ClientCertificate) használatával – az Ügyféltanúsítványok segítségével végezzen hitelesítést egy háttér-szolgáltatással.

-   [Hitelesítés felügyelt identitással](api-management-authentication-policies.md#ManagedIdentity) – hitelesítés a API Management szolgáltatás [felügyelt identitásával](../active-directory/managed-identities-azure-resources/overview.md) .

##  <a name="authenticate-with-basic"></a><a name="Basic"></a> Hitelesítés alapszintű
 Az `authentication-basic` alapszintű hitelesítést használó háttér-szolgáltatással történő hitelesítéshez használja a szabályzatot. Ez a házirend hatékonyan állítja be a HTTP-engedélyezési fejlécet a szabályzatban megadott hitelesítő adatoknak megfelelő értékre.

### <a name="policy-statement"></a>Szabályzati utasítás

```xml
<authentication-basic username="username" password="password" />
```

### <a name="example"></a>Példa

```xml
<authentication-basic username="testuser" password="testpassword" />
```

### <a name="elements"></a>Elemek

|Név|Leírás|Kötelező|
|----------|-----------------|--------------|
|hitelesítés – alapszintű|Gyökérelem.|Igen|

### <a name="attributes"></a>Attribútumok

|Név|Leírás|Kötelező|Alapértelmezett|
|----------|-----------------|--------------|-------------|
|username|Megadja az alapszintű hitelesítő adat felhasználónevét.|Igen|N/A|
|jelszó|Megadja az alapszintű hitelesítő adat jelszavát.|Igen|N/A|

### <a name="usage"></a>Használat
 Ez a szabályzat a következő házirend- [részekben](./api-management-howto-policies.md#sections) és [hatókörökben](./api-management-howto-policies.md#scopes)használható.

-   **Házirend fejezetei:** bejövő

-   **Házirend-hatókörök:** az összes hatókör

##  <a name="authenticate-with-client-certificate"></a><a name="ClientCertificate"></a> Hitelesítés ügyféltanúsítvány használatával
 A `authentication-certificate` szabályzat használatával hitelesítheti a háttér-szolgáltatást egy ügyféltanúsítványt használva. Először az [API Management kell telepíteni](./api-management-howto-mutual-certificates.md) a tanúsítványt, és annak ujjlenyomata vagy tanúsítvány-azonosítója (erőforrás neve) azonosítja. 

> [!CAUTION]
> Ha a tanúsítvány Azure Key Vault tárolt tanúsítványra hivatkozik, azonosítsa azt a tanúsítvány-azonosító használatával. A Key Vault-tanúsítvány elforgatásakor az ujjlenyomata API Management módosul, és a házirend nem oldja fel az új tanúsítványt, ha az ujjlenyomat azonosítja.

### <a name="policy-statement"></a>Szabályzati utasítás

```xml
<authentication-certificate thumbprint="thumbprint" certificate-id="resource name"/>
```

### <a name="examples"></a>Példák

Ebben a példában az ügyféltanúsítványt a tanúsítvány azonosítója azonosítja:

```xml  
<authentication-certificate certificate-id="544fe9ddf3b8f30fb490d90f" />  
``` 

Ebben a példában az ügyféltanúsítványt az ujjlenyomata azonosítja:

```xml
<authentication-certificate thumbprint="CA06F56B258B7A0D4F2B05470939478651151984" />
```
Ebben a példában az ügyféltanúsítvány a szabályzatban van beállítva, és nem a beépített tanúsítványtárolóból lett beolvasva:

```xml
<authentication-certificate body="@(context.Variables.GetValueOrDefault<byte[]>("byteCertificate"))" password="optional-certificate-password" />
```

### <a name="elements"></a>Elemek  
  
|Név|Leírás|Kötelező|  
|----------|-----------------|--------------|  
|hitelesítés – tanúsítvány|Gyökérelem.|Igen|  
  
### <a name="attributes"></a>Attribútumok  
  
|Név|Leírás|Kötelező|Alapértelmezett|  
|----------|-----------------|--------------|-------------|  
|ujjlenyomat|Az ügyféltanúsítvány ujjlenyomata.|`thumbprint`Vagy `certificate-id` kell lennie.|N/A|
|tanúsítvány-azonosító|A tanúsítvány erőforrásának neve.|`thumbprint`Vagy `certificate-id` kell lennie.|N/A|
|body (Törzs)|Ügyfél-tanúsítvány bájt tömbként.|Nem|N/A|
|jelszó|Az ügyféltanúsítvány jelszava.|Akkor használatos, ha a ben megadott tanúsítvány `body` jelszavas védelemmel van ellátva.|N/A|
  
### <a name="usage"></a>Használat  
 Ez a szabályzat a következő házirend- [részekben](./api-management-howto-policies.md#sections) és [hatókörökben](./api-management-howto-policies.md#scopes)használható.  
  
-   **Házirend fejezetei:** bejövő  
  
-   **Házirend-hatókörök:** az összes hatókör  

##  <a name="authenticate-with-managed-identity"></a><a name="ManagedIdentity"></a> Hitelesítés felügyelt identitással  
 A `authentication-managed-identity` szabályzat használatával végezzen hitelesítést a háttér-szolgáltatással a felügyelt identitás használatával. Ez a szabályzat lényegében a felügyelt identitás használatával szerez hozzáférési jogkivonatot Azure Active Directoryről a megadott erőforrás eléréséhez. A jogkivonat sikeres beszerzését követően a szabályzat a fejlécben lévő jogkivonat értékét a `Authorization` séma alapján állítja be `Bearer` .

A rendszer által hozzárendelt identitás és a több felhasználó által hozzárendelt identitás is használható a jogkivonat igényléséhez. Ha `client-id` nincs megadva, a rendszer által hozzárendelt identitást feltételezzük. Ha a `client-id` változót a rendszer a felhasználó által hozzárendelt identitásra vonatkozóan kéri Azure Active Directory
  
### <a name="policy-statement"></a>Szabályzati utasítás  
  
```xml  
<authentication-managed-identity resource="resource" client-id="clientid of user-assigned identity" output-token-variable-name="token-variable" ignore-error="true|false"/>  
```  
  
### <a name="example"></a>Példa  
#### <a name="use-managed-identity-to-authenticate-with-a-backend-service"></a>Felügyelt identitás használata a háttér-szolgáltatással történő hitelesítéshez
```xml  
<authentication-managed-identity resource="https://graph.microsoft.com"/> 
```
```xml  
<authentication-managed-identity resource="https://management.azure.com/"/> <!--Azure Resource Manager-->
```
```xml  
<authentication-managed-identity resource="https://vault.azure.net"/> <!--Azure Key Vault-->
```
```xml  
<authentication-managed-identity resource="https://servicebus.azure.net/"/> <!--Azure Service Bus-->
```
```xml  
<authentication-managed-identity resource="https://storage.azure.com/"/> <!--Azure Blob Storage-->
```
```xml  
<authentication-managed-identity resource="https://database.windows.net/"/> <!--Azure SQL-->
```

```xml
<authentication-managed-identity resource="api://Client_id_of_Backend"/> <!--Your own Azure AD Application-->
```

#### <a name="use-managed-identity-and-set-header-manually"></a>Felügyelt identitás használata és a fejléc manuális megadása

```xml
<authentication-managed-identity resource="api://Client_id_of_Backend"
   output-token-variable-name="msi-access-token" ignore-error="false" /> <!--Your own Azure AD Application-->
<set-header name="Authorization" exists-action="override">
   <value>@("Bearer " + (string)context.Variables["msi-access-token"])</value>
</set-header>
```

#### <a name="use-managed-identity-in-send-request-policy"></a>Felügyelt identitás használata a küldési kérelmek házirendjében
```xml  
<send-request mode="new" timeout="20" ignore-error="false">
    <set-url>https://example.com/</set-url>
    <set-method>GET</set-method>
    <authentication-managed-identity resource="ResourceID"/>
</send-request>
```

### <a name="elements"></a>Elemek  
  
|Név|Leírás|Kötelező|  
|----------|-----------------|--------------|  
|hitelesítés – felügyelt – identitás |Gyökérelem.|Igen|  
  
### <a name="attributes"></a>Attribútumok  
  
|Név|Leírás|Kötelező|Alapértelmezett|  
|----------|-----------------|--------------|-------------|  
|erőforrás|Sztring. A célként megadott webes API (biztonságos erőforrás) alkalmazás-azonosítója Azure Active Directoryban.|Igen|N/A|
|ügyfél-azonosító|Sztring. A felhasználó által hozzárendelt identitás alkalmazás-azonosítója Azure Active Directoryban.|Nem|rendszer által hozzárendelt identitás|
|output-token-változó-neve|Sztring. Annak a környezeti változónak a neve, amely a jogkivonat értékét objektum típusúként fogja fogadni `string` . |Nem|N/A|  
|Mellőzés – hiba|Logikai. Ha a értékre `true` van állítva, akkor a házirend-folyamat akkor is végre fog hajtani, ha nem kapott hozzáférési jogkivonatot.|Nem|hamis|  
  
### <a name="usage"></a>Használat  
 Ez a szabályzat a következő házirend- [részekben](./api-management-howto-policies.md#sections) és [hatókörökben](./api-management-howto-policies.md#scopes)használható.  
  
-   **Házirend fejezetei:** bejövő  
  
-   **Házirend-hatókörök:** az összes hatókör

## <a name="next-steps"></a>Következő lépések
További információ a házirendek használatáról:

+ [Szabályzatok API Management](api-management-howto-policies.md)
+ [API-k átalakítása](transform-api.md)
+ Házirend- [hivatkozás](./api-management-policies.md) a szabályzat-utasítások és azok beállításainak teljes listájához
+ [Szabályzatminták](./policy-reference.md)