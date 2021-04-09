---
title: API-kulcs hitelesítése
titleSuffix: Azure Cognitive Search
description: Az API-kulcsok vezérlik a szolgáltatás végpontjának bejövő hozzáférését. A rendszergazdai kulcsok írási hozzáférést biztosítanak. A lekérdezési kulcsok csak olvasási hozzáféréshez hozhatók létre.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/03/2021
ms.openlocfilehash: 8b2e85744923fb2e7e474e049df1536aebc56f3c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99536933"
---
# <a name="create-and-manage-api-keys-for-an-azure-cognitive-search-service"></a>Azure Cognitive Search-szolgáltatás API-kulcsainak létrehozása és kezelése

A keresési szolgáltatásnak küldött összes kérésnek egy írásvédett API-kulcsra van szüksége, amely kifejezetten a szolgáltatáshoz lett létrehozva. Az API-kulcs az egyetlen mechanizmus a bejövő kérelmek hitelesítéséhez a keresési szolgáltatás végpontján, és minden kérelem esetében kötelező. 

+ A [Rest-megoldásokban](search-get-started-rest.md) `api-key` általában a kérelem fejlécében van megadva.

+ A [.net-megoldásokban](search-howto-dotnet-sdk.md)a kulcsokat gyakran konfigurációs beállításként kell megadni, majd [AzureKeyCredential](/dotnet/api/azure.azurekeycredential) kell átadni

Az API-kulcsokat megtekintheti és kezelheti a [Azure Portal](https://portal.azure.com), vagy a [PowerShell](/powershell/module/az.search), az [Azure CLI](/cli/azure/search)vagy a [REST API](/rest/api/searchmanagement/)használatával.

:::image type="content" source="media/search-manage/azure-search-view-keys.png" alt-text="Portál lap, beállítások beolvasása, kulcsok szakasz" border="false":::

## <a name="what-is-an-api-key"></a>Mi az API-kulcs?

Az API-kulcsok olyan egyedi karakterláncok, amelyek véletlenszerűen generált számokból és betűkből állnak, amelyeket a rendszer a keresési szolgáltatásnak küldött összes kérelemhez továbbít. A szolgáltatás elfogadja a kérést, ha maga a kérelem és a kulcs is érvényes. 

A keresési szolgáltatás eléréséhez két típusú kulcs használható: admin (olvasás és írás) és lekérdezés (csak olvasható).

|Kulcs|Leírás|Korlátok|  
|---------|-----------------|------------|  
|Rendszergazda|Minden művelethez teljes körű jogosultságot biztosít, beleértve a szolgáltatás felügyeletének képességét, indexek létrehozását és törlését, indexelő és adatforrásokat.<br /><br /> A szolgáltatás létrehozásakor két, a portál *elsődleges* és *másodlagos* kulcsának nevezett rendszergazdai kulcs jön létre, és igény szerint egyénileg újragenerálható. A két kulcs lehetővé teszi egy kulcs átadását, miközben a második kulcsot használja a szolgáltatás folyamatos eléréséhez.<br /><br /> A rendszergazdai kulcsok csak a HTTP-kérések fejlécében vannak megadva. Egy URL-címben nem helyezhető el rendszergazdai API-kulcs.|Maximum 2/szolgáltatás|  
|Lekérdezés|Csak olvasási hozzáférést biztosít az indexekhez és a dokumentumokhoz, és általában a keresési kérelmeket kibocsátó ügyfélalkalmazások számára történik.<br /><br /> A lekérdezési kulcsok igény szerint jönnek létre.<br /><br /> A lekérdezési kulcsok a keresés, javaslat vagy keresési művelet HTTP-kérelem fejlécében adhatók meg. Azt is megteheti, hogy a lekérdezési kulcsot paraméterként adja át egy URL-címben. Attól függően, hogy az ügyfélalkalmazás hogyan fogalmazza meg a kérést, könnyebb lehet átadni a kulcsot lekérdezési paraméterként:<br /><br /> `GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2020-06-30&api-key=[query key]`|50/szolgáltatás|  

 Vizuálisan, nincs különbség a rendszergazdai kulcs vagy a lekérdezési kulcs között. Mindkét kulcs 32 véletlenszerűen generált alfanumerikus karakterből álló karakterlánc. Ha nem tudja nyomon követni, hogy milyen típusú kulcs van megadva az alkalmazásban, megtekintheti [a kulcs értékeit a portálon](https://portal.azure.com).  

> [!NOTE]  
> A bizalmas adatok (például `api-key` a kérelem URI-ja) számára nem megfelelő biztonsági gyakorlatnak minősülnek. Emiatt az Azure Cognitive Search csak a lekérdezési `api-key` karakterláncot fogadja el a lekérdezési sztringben, és ennek elkerülését kell elkerülnie, kivéve, ha az index tartalmának nyilvánosan elérhetőnek kell lennie. Általános szabályként azt javasoljuk, hogy a `api-key` kérelem fejlécét küldje el.  

## <a name="find-existing-keys"></a>Meglévő kulcsok keresése

A hozzáférési kulcsokat a portálon, illetve a [PowerShell](/powershell/module/az.search), az [Azure CLI](/cli/azure/search)vagy a [REST API](/rest/api/searchmanagement/)használatával szerezheti be.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. Az előfizetéshez tartozó [keresési szolgáltatások](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) listázása.
1. Válassza ki a szolgáltatást, és az Áttekintés lapon kattintson a **Beállítások**  > **kulcsok** elemre a felügyeleti és a lekérdezési kulcsok megtekintéséhez.

   :::image type="content" source="media/search-security-overview/settings-keys.png" alt-text="Portál lap, beállítások megtekintése, kulcsok szakasz" border="false":::

## <a name="create-query-keys"></a>Lekérdezési kulcsok létrehozása

A lekérdezési kulcsok csak olvasási hozzáférést biztosítanak a dokumentumokhoz egy indexen belül a dokumentumok gyűjteményét célzó műveletekhez. A keresés, szűrés és javaslatok lekérdezése minden olyan művelet, amely lekérdezési kulcsot hajt végre. A rendszeradatokat vagy az objektumokat, például az index definícióját vagy az indexelő állapotát visszaadó írásvédett művelethez rendszergazdai kulcs szükséges.

Az ügyfélalkalmazások hozzáférésének és műveleteinek korlátozása elengedhetetlen a szolgáltatásban található keresési eszközök védelméhez. Az ügyfélalkalmazások által létrehozott összes lekérdezéshez mindig használjon egy lekérdezési kulcsot, nem pedig rendszergazdai kulcsot.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Az előfizetéshez tartozó [keresési szolgáltatások](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)  listázása.
3. Válassza ki a szolgáltatást, és az Áttekintés lapon kattintson a **Beállítások**  > **kulcsok** elemre.
4. Kattintson a **lekérdezési kulcsok kezelése** lehetőségre.
5. Használja a szolgáltatáshoz már létrehozott lekérdezési kulcsot, vagy hozzon létre akár 50 új lekérdezési kulcsot. Az alapértelmezett lekérdezési kulcs neve nem, de a kezelhetőséghez további lekérdezési kulcsok is megtekinthetők.

   :::image type="content" source="media/search-security-overview/create-query-key.png" alt-text="Lekérdezési kulcs létrehozása vagy használata" border="false":::

> [!Note]
> A lekérdezési kulcs használatát bemutató példa a [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo)-ben található.

<a name="regenerate-admin-keys"></a>

## <a name="regenerate-admin-keys"></a>Rendszergazdai kulcsok újragenerálása

A rendszer két rendszergazdai kulcsot hoz létre az egyes szolgáltatásokhoz, így az elsődleges kulcsot az üzletmenet folytonossága érdekében a másodlagos kulccsal lehet elforgatni.

1. A **Beállítások**  > **kulcsok** lapon másolja a másodlagos kulcsot.
2. Az összes alkalmazás esetében frissítse az API-kulcs beállításait a másodlagos kulcs használatára.
3. Az elsődleges kulcs újbóli előállítása.
4. Frissítse az összes alkalmazást az új elsődleges kulcs használatára.

Ha véletlenül újragenerálja mindkét kulcsot, a kulcsokat használó összes ügyfél-kérelem sikertelen lesz, és a HTTP 403 Tiltott. A tartalom azonban nincs törölve, és a rendszer véglegesen nem zárolja őket. 

A szolgáltatást a portálon keresztül vagy programozott módon is elérheti. A felügyeleti függvények egy előfizetés-AZONOSÍTÓn keresztül nem a szolgáltatás API-kulcsa, így még akkor is elérhetők, ha az API-kulcsok nem. 

Miután a portálon vagy a felügyeleti rétegen keresztül új kulcsokat hozott létre, a rendszer visszaállítja a hozzáférést a tartalomhoz (indexek, indexelő, adatforrások, szinonimák), ha az új kulcsokkal rendelkezik, és megadja ezeket a kulcsokat a kérésekhez.

## <a name="secure-api-keys"></a>Biztonságos API-kulcsok

A [szerepköralapú engedélyekkel](search-security-rbac.md)törölheti vagy elolvashatja a kulcsokat, de a kulcs nem cserélhető le felhasználó által definiált jelszóval, vagy nem használhatja a Active Directory elsődleges hitelesítési módszerként a keresési műveletek eléréséhez. 

A kulcs biztonsága a portálon vagy a Resource Manager-felületeken (PowerShell vagy parancssori felületen) keresztüli hozzáférés korlátozásával biztosítható. Az előfizetés-rendszergazdák az összes API-kulcsot megtekinthetik és újragenerálják. Elővigyázatosságból tekintse át a szerepkör-hozzárendeléseket, és Ismerje meg, hogy ki férhet hozzá a rendszergazdai kulcsokhoz.

+ A szolgáltatás Irányítópultján kattintson a **hozzáférés-vezérlés (iam)** elemre, majd a **szerepkör-hozzárendelések** fülre a szolgáltatáshoz tartozó szerepkör-hozzárendelések megtekintéséhez.

A következő szerepkörök tagjai megtekinthetik és újragenerálják a kulcsokat: tulajdonos, közreműködő, [Search Service közreműködők](../role-based-access-control/built-in-roles.md#search-service-contributor)

## <a name="see-also"></a>Lásd még

+ [Biztonság az Azure Cognitive Search](search-security-overview.md)
+ [Azure szerepköralapú hozzáférés-vezérlés az Azure-ban Cognitive Search](search-security-rbac.md)
+ [Kezelés a PowerShell-lel](search-manage-powershell.md) 