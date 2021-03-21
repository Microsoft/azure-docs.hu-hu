---
title: Felsőbb rétegbeli beállítások az Azure Signaler szolgáltatásban
description: Ismerje meg a felsőbb rétegbeli üzenetek felsőbb rétegbeli beállításainak és protokolljainak bevezetését.
author: chenyl
ms.service: signalr
ms.topic: conceptual
ms.date: 06/11/2020
ms.author: chenyl
ms.openlocfilehash: 6752a9564dc0d9351d1c21f5be14eb626186ac0d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98724056"
---
# <a name="upstream-settings"></a>Felsőbb rétegbeli beállítások

A felsőbb rétegbeli szolgáltatás előzetes funkciója lehetővé teszi, hogy az Azure Signaler szolgáltatás üzeneteket és kapcsolódási eseményeket küldjön a végpontok számára kiszolgáló nélküli módban. A felsőbb réteg használatával kiszolgáló nélküli módban hívhat meg központi metódust az ügyfelektől, és engedélyezheti, hogy a végpontok értesítést kapjanak az ügyfélkapcsolatok csatlakoztatása vagy leválasztása esetén.

> [!NOTE]
> Csak a kiszolgáló nélküli mód konfigurálhatja a felsőbb rétegbeli beállításokat.

## <a name="details-of-upstream-settings"></a>A felsőbb rétegbeli beállítások részletei

A felsőbb rétegbeli beállítások az Order-szenzitív elemek listáját tartalmazzák. Minden elem a következőkből áll:

* URL-sablon, amely megadja, hogy az üzenetek hová legyenek küldve.
* Szabályok halmaza.
* Hitelesítési konfigurációk. 

Ha a megadott esemény bekövetkezik, egy elem szabályait egyenként kell ellenőrizni a sorrendben. Az üzenetek az első megfelelő elemek felsőbb rétegbeli URL-címére lesznek küldve.

### <a name="url-template-settings"></a>URL-sablon beállításai

A különböző mintázatok támogatásához parametrizálja az URL-címet. Három előre definiált paraméter létezik:

|Előre definiált paraméter|Description|
|---------|---------|
|hub| A hub az Azure Signaler szolgáltatás fogalma. A hub elkülönítési egység. A felhasználók és az üzenetek kézbesítési köre korlátozott egy hubhoz.|
|Kategória| A kategória a következő értékek egyike lehet: <ul><li>**kapcsolatok**: kapcsolat élettartamának eseményei. Az ügyfél kapcsolata csatlakoztatva van vagy le van választva. Ide tartozik a csatlakoztatott és a leválasztott események is.</li><li>**üzenetek**: akkor aktiválódik, amikor az ügyfelek egy központi metódust hív meg. Minden egyéb eseményt tartalmaz, kivéve a **kapcsolatok** kategóriájában lévőket.</li></ul>|
|esemény| Az **üzenetek** kategória esetében az esemény a cél egy [Meghívási üzenetben](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocation-message-encoding) , amelyet az ügyfelek küldenek. A kapcsolatok kategória esetében csak a *csatlakoztatott* és a *leválasztott* **kapcsolat** van használatban.|

Ezeket az előre definiált paramétereket az URL-mintában lehet használni. A felsőbb rétegbeli URL-cím kiértékelése során a paraméterek egy megadott értékkel lesznek lecserélve. Például: 
```
http://host.com/{hub}/api/{category}/{event}
```
Ha a "chat" hub-kapcsolat csatlakoztatva van, a rendszer üzenetet küld a következő URL-címre:
```
http://host.com/chat/api/connections/connected
```
Ha a "chat" hub egyik ügyfele meghívja a hub metódust `broadcast` , a rendszer egy üzenetet küld a következő URL-címre:
```
http://host.com/chat/api/messages/broadcast
```

### <a name="key-vault-secret-reference-in-url-template-settings"></a>Key Vault Secret-hivatkozás az URL-sablon beállításaiban

A felsőbb rétegbeli URL-cím nem titkosítható a nyugalmi állapotban. Ha bizalmas információra van szüksége, azt javasoljuk, hogy a Key Vault használatával mentse őket, ahol a hozzáférés-vezérlés jobb biztosítást biztosít. Alapvetően engedélyezheti az Azure Signaler szolgáltatás felügyelt identitását, majd olvasási engedélyt adhat egy Key Vault példányhoz, és az egyszerű szöveges URL-cím helyett Key Vault referenciát használhat.

1. Rendszerhez rendelt identitás vagy felhasználó által hozzárendelt identitás hozzáadása. Lásd: [felügyelt identitás hozzáadása az Azure Portalon](./howto-use-managed-identity.md#add-a-system-assigned-identity)

2. Adja meg a titkos olvasási engedélyt a felügyelt identitáshoz a Key Vault hozzáférési házirendjeiben. Lásd: [Key Vault hozzáférési szabályzat kiosztása a Azure Portal használatával](../key-vault/general/assign-access-policy-portal.md)

3. Cserélje le a bizalmas szöveget a `{@Microsoft.KeyVault(SecretUri=<secret-identity>)}` felsőbb rétegbeli URL-minta szintaxisára.

> [!NOTE]
> A titkos tartalom csak a felsőbb rétegbeli beállítások módosításakor vagy a felügyelt identitás módosításakor kerül újraolvasásra. Győződjön meg arról, hogy a titkos olvasási engedélyt adta a felügyelt identitásnak a Key Vault Secret-hivatkozás használata előtt.

### <a name="rule-settings"></a>Szabály beállításai

Megadhatja a *központi szabályok*, a *Kategóriák* és az *események szabályait* külön-külön. Az egyeztetési szabály három formátumot támogat. Példaként vegyen fel az események szabályait:
- Használjon csillag (*) karaktert az események egyeztetéséhez.
- Több esemény csatlakoztatásához használjon vesszőt (,). Például `connected, disconnected` megfelel a csatlakoztatott és a leválasztott eseményeknek.
- A teljes esemény nevét használja az eseménynek megfelelően. Például `connected` megfelel a csatlakoztatott eseménynek.

> [!NOTE]
> Ha Azure Functions és a [signaler triggert](../azure-functions/functions-bindings-signalr-service-trigger.md)használja, a signaler trigger egyetlen végpontot tesz elérhetővé a következő formátumban: `<Function_App_URL>/runtime/webhooks/signalr?code=<API_KEY>` .
> Egyszerűen beállíthatja az URL **-sablon beállításait** erre az URL-címre, és megtarthatja a **szabály beállításainak** alapértelmezett beállításait. A és a keresésével kapcsolatos részletekért lásd a [signaler szolgáltatás integrációját](../azure-functions/functions-bindings-signalr-service-trigger.md#signalr-service-integration) ismertető témakört `<Function_App_URL>` `<API_KEY>` .

### <a name="authentication-settings"></a>Hitelesítési beállítások

Az egyes felsőbb rétegbeli beállítási elemek hitelesítését külön is konfigurálhatja. A hitelesítés konfigurálásakor a jogkivonat a `Authentication` felsőbb rétegbeli üzenet fejlécében van beállítva. Jelenleg az Azure Signaler szolgáltatás a következő hitelesítési típusokat támogatja:
- `None`
- `ManagedIdentity`

Ha kiválasztja `ManagedIdentity` , a felügyelt identitást előre kell engedélyeznie az Azure signaler szolgáltatásban, és opcionálisan meg kell adnia egy erőforrást. További részletekért lásd: [felügyelt identitások az Azure signaler szolgáltatáshoz](howto-use-managed-identity.md) .

## <a name="create-upstream-settings-via-the-azure-portal"></a>Felsőbb rétegbeli beállítások létrehozása a Azure Portal használatával

1. Nyissa meg az Azure Signaler szolgáltatást.
2. Válassza a **Beállítások** és a **szolgáltatás mód** váltása **kiszolgáló** nélkülire lehetőséget. A felsőbb rétegbeli beállítások a következőket fogják megjelenni:

    :::image type="content" source="media/concept-upstream/upstream-portal.png" alt-text="Felsőbb rétegbeli beállítások":::

3. URL-címek hozzáadása a **felsőbb rétegbeli URL-minta** alatt. Ezután a beállítások, például a **hub-szabályok** az alapértelmezett értéket fogják megjeleníteni.
4. A **hub-szabályok**, az **események**, a **Kategória-szabályok** és a **felsőbb rétegbeli hitelesítés** beállításainak megadásához válassza ki a **hub-szabályok** értékét. Megjelenik egy olyan oldal, amely lehetővé teszi a beállítások szerkesztését:

    :::image type="content" source="media/concept-upstream/upstream-detail-portal.png" alt-text="Felsőbb rétegbeli beállítás részletei":::

5. A **felsőbb rétegbeli hitelesítés** beállításához először engedélyezze a felügyelt identitást. Ezután válassza a **felügyelt identitás használata** lehetőséget. Az igényeknek megfelelően az **Auth erőforrás-azonosító** területen bármelyik lehetőség közül választhat. További részletekért lásd: [felügyelt identitások az Azure signaler szolgáltatáshoz](howto-use-managed-identity.md) .

## <a name="create-upstream-settings-via-resource-manager-template"></a>Felsőbb rétegbeli beállítások létrehozása Resource Manager-sablon használatával

A felsőbb rétegbeli beállítások [Azure Resource Manager sablon](../azure-resource-manager/templates/overview.md)használatával történő létrehozásához állítsa be a tulajdonságot `upstream` a `properties` tulajdonságban. Az alábbi kódrészletből megtudhatja, hogyan állíthatja be a `upstream` tulajdonságot a felsőbb rétegbeli beállítások létrehozásához és frissítéséhez.

```JSON
{
  "properties": {
    "upstream": {
      "templates": [
        {
          "UrlTemplate": "http://host.com/{hub}/api/{category}/{event}",
          "EventPattern": "*",
          "HubPattern": "*",
          "CategoryPattern": "*",
          "Auth": {
            "Type": "ManagedIdentity",
            "ManagedIdentity": {
              "Resource": "<resource>"
            }
          }
        }
      ]
    }
  }
}
```

## <a name="serverless-protocols"></a>Kiszolgáló nélküli protokollok

Az Azure Signaler szolgáltatás üzeneteket küld a következő protokollokat követő végpontoknak. A [signaler szolgáltatás triggerének kötését](../azure-functions/functions-bindings-signalr-service-trigger.md) használhatja függvényalkalmazásekkel, amelyek kezelik ezeket a protokollokat.

### <a name="method"></a>Metódus

POST

### <a name="request-header"></a>Kérelem fejléce

|Név |Leírás|
|---------|---------|
|X-ASRS-kapcsolatazonosító |Az ügyfélkapcsolathoz tartozó kapcsolatazonosító.|
|X-ASRS – hub |Az az elosztó, amelyhez az Ügyfélkapcsolat tartozik.|
|X-ASRS – kategória |Az a kategória, amelyhez az üzenet tartozik.|
|X-ASRS-Event |Az az esemény, amelyhez az üzenet tartozik.|
|X-ASRS – aláírás |Az érvényesítéshez használt kivonatoló üzenet-hitelesítési kód (HMAC). Részletekért lásd az [aláírást](#signature) .|
|X-ASRS-User-jogcím |Az Ügyfélkapcsolat jogcímeinek egy csoportja.|
|X-ASRS-User-ID |Az üzenetet küldő ügyfél felhasználói identitása.|
|X-ASRS – ügyfél – lekérdezés |A kérés lekérdezése, amikor az ügyfelek csatlakoznak a szolgáltatáshoz.|
|Hitelesítés |Egy választható jogkivonat a használatakor `ManagedIdentity` . |

### <a name="request-body"></a>A kérés törzse

#### <a name="connected"></a>Csatlakozva

Content-Type: Application/JSON

#### <a name="disconnected"></a>Leválasztott

Content-Type: `application/json`

|Név  |Típus  |Description  |
|---------|---------|---------|
|Hiba |sztring |Egy lezárt kapcsolatok hibaüzenete. Üres, ha a kapcsolatok hiba nélkül zárulnak.|

#### <a name="invocation-message"></a>Meghívási üzenet

Content-Type: `application/json` vagy `application/x-msgpack`

|Név  |Típus  |Description  |
|---------|---------|---------|
|InvocationId |sztring | Egy Meghívási üzenetet jelölő, nem kötelező karakterlánc. Részletek keresése a [hívásokban](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocations).|
|Cél |sztring | Ugyanaz, mint a [Meghívási üzenetben](https://github.com/dotnet/aspnetcore/blob/master/src/SignalR/docs/specs/HubProtocol.md#invocation-message-encoding)szereplő esemény és a cél. |
|Argumentumok |Objektum tömbje |Egy tömb, amely a alkalmazásban hivatkozott metódusra alkalmazandó argumentumokat tartalmaz `Target` . |

### <a name="signature"></a>Aláírás

A szolgáltatás a `X-ASRS-Connection-Id` kulcsként az elsődleges hozzáférési kulcsot és a másodlagos elérési kulcsot is használja az értékhez tartozó sha256-kód kiszámításához `HMAC` . A szolgáltatás a fejlécbe állítja be, `X-ASRS-Signature` Amikor http-kéréseket hoz létre a felsőbb rétegbe:
```
Hex_encoded(HMAC_SHA256(accessKey, connection-id))
```

## <a name="next-steps"></a>Következő lépések

- [Felügyelt identitások az Azure Signaler szolgáltatáshoz](howto-use-managed-identity.md)
- [Az Azure Functions fejlesztése és konfigurálása az Azure SignalR szolgáltatással](signalr-concept-serverless-development-config.md)
- [A Signaler szolgáltatás üzeneteinek kezelése (trigger kötése)](../azure-functions/functions-bindings-signalr-service-trigger.md)
- [A signaler szolgáltatás Triggerének kötési mintája](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/BidirectionChat)