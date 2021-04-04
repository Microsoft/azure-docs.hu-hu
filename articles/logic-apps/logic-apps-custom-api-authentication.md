---
title: Hitelesítés hozzáadása az egyéni API-khoz irányuló hívások biztonságossá tételéhez
description: A hitelesítés beállítása az egyéni API-khoz érkező hívások biztonságának növeléséhez Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 09/22/2017
ms.openlocfilehash: 5e0dcd478c6eb6696a0e07d35d4dccddac68ac1c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "95993181"
---
# <a name="increase-security-for-calls-to-custom-apis-from-azure-logic-apps"></a>A Azure Logic Apps egyéni API-khoz érkező hívások biztonságának fokozása

Az API-k hívásának biztonsága érdekében beállíthatja Azure Active Directory (Azure AD) hitelesítését a Azure Portal segítségével, így nem kell frissítenie a kódot. Alternatív megoldásként a hitelesítés előírásához és kényszerítéséhez az API kódját is használhatja.

## <a name="authentication-options-for-your-api"></a>Az API hitelesítési beállításai

Az egyéni API-hívások biztonsága az alábbi módokon javítható:

* [Nem változik a kód módosítása](#no-code): az API-t az [Azure Active Directory (Azure ad)](../active-directory/fundamentals/active-directory-whatis.md) segítségével az Azure Portal keresztül biztosíthatja, így nem kell frissítenie a kódot, vagy újra kell telepítenie az API-t.

  > [!NOTE]
  > Alapértelmezés szerint a Azure Portal bekapcsolt Azure AD-hitelesítés nem biztosít részletes engedélyt. Ez a hitelesítés például zárolja az API-t egy adott bérlőre, nem pedig egy adott felhasználóra vagy alkalmazásra. 

* [Frissítse API-kódját](#update-code): gondoskodjon az API-k biztonságáról a [tanúsítványalapú hitelesítés](#certificate), az [alapszintű hitelesítés](#basic)vagy az [Azure ad-hitelesítés](#azure-ad-code) kód használatával történő betartatásával.

<a name="no-code"></a>

### <a name="authenticate-calls-to-your-api-without-changing-code"></a>A kód módosítása nélkül hitelesítheti az API-hívásokat

A metódus általános lépései:

1. Hozzon létre két Azure Active Directory (Azure AD) alkalmazás-identitást: egyet a logikai alkalmazáshoz, egy pedig a webalkalmazáshoz (vagy egy API-alkalmazáshoz).

2. Az API-hívások hitelesítéséhez használja a logikai alkalmazáshoz tartozó Azure AD-alkalmazás identitásához társított egyszerű szolgáltatás hitelesítő adatait (ügyfél-azonosító és titkos kulcs).

3. Adja meg az alkalmazás azonosítóit a logikai alkalmazás definíciójában.

#### <a name="part-1-create-an-azure-ad-application-identity-for-your-logic-app"></a>1. rész: Azure AD-alkalmazás identitásának létrehozása a logikai alkalmazáshoz

A logikai alkalmazás ezt az Azure AD-alkalmazás identitását használja az Azure AD-vel való hitelesítéshez. Ezt az identitást csak egyszer kell beállítania a címtárhoz. Dönthet például úgy, hogy ugyanazt az identitást használja az összes logikai alkalmazáshoz, de az egyes logikai alkalmazások egyedi identitásait is létrehozhatja. Ezeket az identitásokat beállíthatja a Azure Portalban, vagy használhatja a [PowerShellt](#powershell).

**Hozza létre az alkalmazás identitását a logikai alkalmazáshoz a Azure Portal**

1. A [Azure Portal](https://portal.azure.com "https://portal.azure.com")válassza a **Azure Active Directory** lehetőséget. 

2. Győződjön meg arról, hogy a webalkalmazás vagy az API-alkalmazás ugyanazon a címtárban van.

   > [!TIP]
   > A címtárak váltásához válassza ki a profilt, és válasszon másik könyvtárat. Vagy válassza az **Áttekintés**  >  **kapcsoló könyvtár** lehetőséget.

3. A könyvtár menü **kezelés** területén válassza **Alkalmazásregisztrációk**  >  **új alkalmazás regisztrálása** lehetőséget.

   > [!TIP]
   > Alapértelmezés szerint az alkalmazás-regisztrációk lista megjeleníti az összes alkalmazás regisztrációját a címtárban. Ha csak az alkalmazás regisztrációit szeretné megtekinteni, a keresőmező mellett válassza a **saját alkalmazások** lehetőséget. 

   ![Új alkalmazásregisztráció létrehozása](./media/logic-apps-custom-api-authentication/new-app-registration-azure-portal.png)

4. Adjon nevet az alkalmazásnak, hagyja üresen az **alkalmazás típusát** a **Web App/API** értékre, adjon meg egy egyedi karakterláncot a **bejelentkezési URL-cím** tartományként, majd válassza a **Létrehozás** lehetőséget.

   ![Adja meg az alkalmazás identitásának nevét és bejelentkezési URL-címét](./media/logic-apps-custom-api-authentication/logic-app-identity-azure-portal.png)

   A logikai alkalmazáshoz létrehozott alkalmazás-identitás most megjelenik az alkalmazás-regisztrációk listájában.

   ![A logikai alkalmazás alkalmazás-identitása](./media/logic-apps-custom-api-authentication/logic-app-identity-created.png)

5. Az alkalmazás-regisztrációk listában válassza ki az új alkalmazás-identitást. Másolja ki és mentse a 3. részben található logikai alkalmazás "ügyfél-AZONOSÍTÓként" használandó **alkalmazás-azonosítóját** .

   ![A Logic App-alkalmazás AZONOSÍTÓjának másolása és mentése](./media/logic-apps-custom-api-authentication/logic-app-application-id.png)

6. Ha az alkalmazás-identitás beállításai nem láthatók, válassza a **Beállítások** vagy **az összes beállítás** lehetőséget.

7. Az **API-hozzáférés** területen válassza a **kulcsok** lehetőséget. A **Leírás** mezőben adja meg a kulcs nevét. A **lejárat** alatt válassza ki a kulcs időtartamát.

   Az Ön által létrehozott kulcs az alkalmazás identitásának "titkos" vagy jelszava a logikai alkalmazáshoz.

   ![Kulcs létrehozása a logikai alkalmazás identitásához](./media/logic-apps-custom-api-authentication/create-logic-app-identity-key-secret-password.png)

8. Az eszköztáron válassza a **Mentés** lehetőséget. Az **érték** alatt a kulcs most megjelenik. 
Ügyeljen arra, hogy a kulcsot a későbbi használatra **másolja és mentse** , mert a kulcs rejtve marad a **kulcsok** lapon.

   Ha a logikai alkalmazást a 3. részben konfigurálja, a kulcsot a "titkos" vagy a "jelszó" értékre kell beállítania.

   ![A kulcs másolása és mentése később](./media/logic-apps-custom-api-authentication/logic-app-copy-key-secret-password.png)

<a name="powershell"></a>

**Az alkalmazás identitásának létrehozása a logikai alkalmazáshoz a PowerShellben**

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ezt a feladatot Azure Resource Manager a PowerShell használatával hajthatja végre. Futtassa a következő parancsokat a PowerShellben:

1. `Add-AzAccount`

1. `$SecurePassword = Read-Host -AsSecureString`

1. Adja meg a jelszót, majd nyomja le az ENTER billentyűt.

1. `New-AzADApplication -DisplayName "MyLogicAppID" -HomePage "http://mydomain.tld" -IdentifierUris "http://mydomain.tld" -Password $SecurePassword`

1. Ügyeljen arra, hogy a **bérlői azonosítót** (az Azure ad-bérlő GUID azonosítóját), az **alkalmazás azonosítóját** és a használt jelszót másolja.

További információ: [egyszerű szolgáltatásnév létrehozása a PowerShell-lel az erőforrások eléréséhez](../active-directory/develop/howto-authenticate-service-principal-powershell.md).

#### <a name="part-2-create-an-azure-ad-application-identity-for-your-web-app-or-api-app"></a>2. rész: Azure AD-alkalmazás identitásának létrehozása webalkalmazáshoz vagy API-alkalmazáshoz

Ha a webalkalmazás vagy az API-alkalmazás már telepítve van, bekapcsolhatja a hitelesítést, és létrehozhatja az alkalmazás identitását a Azure Portal. Ellenkező esetben [a Azure Resource Manager sablonnal való üzembe helyezéskor bekapcsolhatja a hitelesítést](#authen-deploy). 

**Az alkalmazás identitásának létrehozása és a hitelesítés bekapcsolása a Azure Portal a telepített alkalmazásokhoz**

1. A [Azure Portal](https://portal.azure.com "https://portal.azure.com")keresse meg és válassza ki a webalkalmazást vagy API-alkalmazást. 

2. A **Beállítások** területen válassza a **hitelesítés/engedélyezés** lehetőséget. A **app Service hitelesítés** területen kapcsolja be **a** hitelesítést. A **hitelesítésszolgáltatók** területen válassza a **Azure Active Directory** lehetőséget.

   ![Hitelesítés bekapcsolása](./media/logic-apps-custom-api-authentication/custom-web-api-app-authentication.png)

3. Most hozzon létre egy alkalmazás-identitást a webalkalmazáshoz vagy az API-alkalmazáshoz az itt látható módon. A **Azure Active Directory beállítások** lapon állítsa a **felügyeleti módot** **expressz** értékre. Válassza az **új ad-alkalmazás létrehozása** lehetőséget. Adjon nevet az alkalmazásnak, majd kattintson **az OK gombra**. 

   ![Alkalmazás-identitás létrehozása webalkalmazáshoz vagy API-alkalmazáshoz](./media/logic-apps-custom-api-authentication/custom-api-application-identity.png)

4. A **Hitelesítés/Engedélyezés** lapon kattintson a **Mentés** gombra.

Most meg kell keresnie a webalkalmazáshoz vagy API-alkalmazáshoz társított alkalmazás-identitás ügyfél-AZONOSÍTÓját és a bérlői AZONOSÍTÓját. Ezeket az azonosítókat a 3. részben tekintjük át. Ezért folytassa ezeket a lépéseket a Azure Portal.

**A webalkalmazás vagy az API-alkalmazás ügyfél-AZONOSÍTÓjának és bérlői AZONOSÍTÓjának megkeresése a Azure Portal**

1. A **hitelesítésszolgáltatók** területen válassza a **Azure Active Directory** lehetőséget. 

   ![Válassza az „Azure Active Directory” elemet](./media/logic-apps-custom-api-authentication/custom-api-app-identity-client-id-tenant-id.png)

2. A **Azure Active Directory beállítások** lapon állítsa be a **felügyeleti módot** a **speciális** értékre.

3. Másolja ki az **ügyfél-azonosítót**, és mentse a GUID azonosítót a 3. részben való használatra.

   > [!TIP] 
   > Ha az **ügyfél-azonosító** és a **kiállító URL-címe** nem jelenik meg, próbálja meg frissíteni a Azure Portal, és ismételje meg az 1. lépést.

4. A **kiállító URL-címe** területen másolja és mentse a 3. rész GUID azonosítóját. Ezt a GUID-t a webalkalmazás vagy az API-alkalmazás telepítési sablonjában is használhatja, ha szükséges.

   Ez a GUID az adott bérlő GUID-azonosítója ("bérlői azonosító"), és ebben az URL-ben kell megjelennie: `https://sts.windows.net/{GUID}`

5. A módosítások mentése nélkül zárjuk be a **Azure Active Directory beállítások** lapot.

<a name="authen-deploy"></a>

**Hitelesítés bekapcsolása Azure Resource Manager sablonnal való üzembe helyezéskor**

Továbbra is létre kell hoznia egy Azure AD-alkalmazás identitását a webalkalmazáshoz vagy API-alkalmazáshoz, amely eltér a logikai alkalmazás alkalmazás-identitásával. Az alkalmazás identitásának létrehozásához kövesse az Azure Portal 2. részében szereplő előző lépéseket. 

Követheti az 1. részben leírt lépéseket is, de ügyeljen arra, hogy a webalkalmazást vagy az API-alkalmazást a tényleges `https://{URL}` **bejelentkezési URL-cím** és az **alkalmazás-azonosító URI** használatával használja. A fenti lépésekben az ügyfél-azonosítót és a bérlő AZONOSÍTÓját is el kell menteni az alkalmazás telepítési sablonjában, valamint a 3. részhez.

> [!NOTE]
> Amikor létrehozza az Azure AD-alkalmazás identitását a webalkalmazáshoz vagy az API-alkalmazáshoz, a Azure Portalt kell használnia, nem a PowerShellt. A PowerShell-parancsmagot nem állítja be a felhasználók webhelyekre való aláírásához szükséges engedélyeket.

Az ügyfél-azonosító és a bérlői azonosító beszerzése után adja meg ezeket az azonosítókat a webalkalmazás vagy API-alkalmazás alforrásaként a telepítési sablonban:

``` json
"resources": [ 
   {
      "apiVersion": "2015-08-01",
      "name": "web",
      "type": "config",
      "dependsOn": ["[concat('Microsoft.Web/sites/','parameters('webAppName'))]"],
      "properties": {
         "siteAuthEnabled": true,
         "siteAuthSettings": {
            "clientId": "<client-ID>",
            "issuer": "https://sts.windows.net/<tenant-ID>/"
         }
      }
   } 
]
```

Ha üres webalkalmazást és logikai alkalmazást szeretne automatikusan telepíteni Azure Active Directory hitelesítéssel együtt, [tekintse meg a teljes sablont itt](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-custom-api/azuredeploy.json), vagy kattintson az **üzembe helyezés az Azure** -ba lehetőségre:

[![Üzembe helyezés az Azure-ban](media/logic-apps-custom-api-authentication/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-logic-app-custom-api%2Fazuredeploy.json)

#### <a name="part-3-populate-the-authorization-section-in-your-logic-app"></a>3. rész: a logikai alkalmazás engedélyezési szakaszának feltöltése

Az előző sablonban már be van állítva ez az engedélyezési szakasz, de ha közvetlenül készíti elő a logikai alkalmazást, meg kell adnia a teljes körű engedélyezési szakaszt.

Nyissa meg a logikai alkalmazás definícióját a kód nézetben, nyissa meg a **http** -művelet definícióját, keresse meg az **engedélyezési** szakaszt, és adja meg a következő tulajdonságokat:

```json
{
   "tenant": "<tenant-ID>",
   "audience": "<client-ID-from-Part-2-web-app-or-API app>", 
   "clientId": "<client-ID-from-Part-1-logic-app>",
   "secret": "<key-from-Part-1-logic-app>", 
   "type": "ActiveDirectoryOAuth"
}
```

| Tulajdonság | Kötelező | Leírás | 
| -------- | -------- | ----------- | 
| Bérlő | Yes | Az Azure AD-bérlő GUID azonosítója | 
| célközönség | Yes | Az elérni kívánt cél erőforrás GUID azonosítója, amely a webalkalmazás vagy API-alkalmazás alkalmazás-identitásának ügyfél-azonosítója | 
| ügyfél-azonosító | Yes | A hozzáférést kérő ügyfél GUID azonosítója, amely a logikai alkalmazás alkalmazás-identitásának ügyfél-azonosítója | 
| titkos kód | Yes | A hozzáférési jogkivonatot kérő ügyfélhez tartozó alkalmazás identitásának kulcsa vagy jelszava | 
| típus | Yes | A hitelesítési típus. A ActiveDirectoryOAuth-hitelesítés esetében az érték a következő: `ActiveDirectoryOAuth` . | 
|||| 

Például:

``` json
{
   "actions": {
      "HTTP": {
         "inputs": {
            "method": "POST",
            "uri": "https://your-api-azurewebsites.net/api/your-method",
            "authentication": {
               "tenant": "tenant-ID",
               "audience": "client-ID-from-azure-ad-app-for-web-app-or-api-app",
               "clientId": "client-ID-from-azure-ad-app-for-logic-app",
               "secret": "key-from-azure-ad-app-for-logic-app",
               "type": "ActiveDirectoryOAuth"
            }
         }
      }
   }
}
```

<a name="update-code"></a>

### <a name="secure-api-calls-through-code"></a>Biztonságos API-hívások kód használatával

<a name="certificate"></a>

#### <a name="certificate-authentication"></a>Tanúsítványhitelesítés

Ha ellenőrizni szeretné a logikai alkalmazás bejövő kérelmeit a webalkalmazásba vagy az API-alkalmazásba, használhat ügyféltanúsítványt. A kód beállításához megtudhatja, [hogyan konfigurálhatja a TLS kölcsönös hitelesítését](../app-service/app-service-web-configure-tls-mutual-auth.md).

Az **Engedélyezés** szakaszban adja meg a következő tulajdonságokat:

```json
{
   "type": "ClientCertificate",
   "password": "<password>",
   "pfx": "<long-pfx-key>"
} 
```

| Tulajdonság | Kötelező | Leírás |
| -------- | -------- | ----------- |
| `type` | Igen | A hitelesítési típus. A TLS/SSL-Ügyféltanúsítványok esetében az értéknek a következőnek kell lennie: `ClientCertificate` . |
| `password` | No | Az ügyféltanúsítvány eléréséhez használt jelszó (PFX-fájl) |
| `pfx` | Yes | Az ügyféltanúsítvány Base64 kódolású tartalma (PFX-fájl) |
||||

<a name="basic"></a>

#### <a name="basic-authentication"></a>Alapszintű hitelesítés

Ha ellenőrizni szeretné a logikai alkalmazás bejövő kérelmeit a webalkalmazásba vagy az API-alkalmazásba, használhatja az egyszerű hitelesítést, például a felhasználónevet és a jelszót. Az egyszerű hitelesítés egy gyakori minta, amely a webalkalmazás vagy API-alkalmazás felépítéséhez használt bármilyen nyelven használható.

Az **Engedélyezés** szakaszban adja meg a következő tulajdonságokat:

```json
{
   "type": "Basic",
   "username": "<username>",
   "password": "<password>"
}
```

| Tulajdonság | Kötelező | Leírás | 
| -------- | -------- | ----------- | 
| típus | Yes | A használni kívánt hitelesítési típus. Alapszintű hitelesítés esetén az értéknek a következőnek kell lennie: `Basic` . | 
| username | Yes | A hitelesítéshez használni kívánt Felhasználónév | 
| jelszó | Yes | A hitelesítéshez használni kívánt jelszó | 
|||| 

<a name="azure-ad-code"></a>

#### <a name="azure-active-directory-authentication-through-code"></a>Hitelesítés Azure Active Directory kóddal

Alapértelmezés szerint a Azure Portal bekapcsolt Azure AD-hitelesítés nem biztosít részletes engedélyt. Ez a hitelesítés például zárolja az API-t egy adott bérlőre, nem pedig egy adott felhasználóra vagy alkalmazásra. 

Ha korlátozni szeretné a logikai alkalmazás API-hozzáférését a kódban, bontsa ki a JSON webes tokent (JWT) tartalmazó fejlécet. Keresse meg a hívó identitását, és utasítsa el a nem egyező kérelmeket.

<!-- Going further, to implement this authentication entirely in your own code, 
and not use the Azure portal, learn how to 
[authenticate with on-premises Active Directory in your Azure app](../app-service/overview-authentication-authorization.md).

To create an application identity for your logic app and use that identity to call your API, 
you must follow the previous steps. -->

## <a name="next-steps"></a>Következő lépések

* [Egyéni API-k üzembe helyezése és meghívása a Logic app-munkafolyamatokból](../logic-apps/logic-apps-custom-api-host-deploy-call.md)
