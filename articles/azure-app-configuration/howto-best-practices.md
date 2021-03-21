---
title: Az Azure app Configuration – ajánlott eljárások | Microsoft Docs
description: Ismerje meg az Azure-alkalmazások konfigurációjának használatakor ajánlott eljárásokat. A témakörök közé tartoznak a főbb csoportok, a kulcs-érték összetételek, az alkalmazások konfigurációjának indítása és egyebek.
services: azure-app-configuration
documentationcenter: ''
author: AlexandraKemperMS
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: alkemper
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 33661eafee6b180819b18d9a9a980eff1e2aeceb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100371549"
---
# <a name="azure-app-configuration-best-practices"></a>Az Azure app Configuration ajánlott eljárásai

Ez a cikk az Azure-alkalmazások konfigurációjának használatával kapcsolatos gyakori mintákat és ajánlott eljárásokat ismerteti.

## <a name="key-groupings"></a>Kulcsfontosságú csoportok

Az alkalmazás konfigurálása két lehetőséget biztosít a kulcsok rendszerezésére:

* Kulcs előtagjai
* Címkék

A kulcsok csoportosításához használhatja az egyiket vagy mindkét lehetőséget is.

A *kulcs előtagjai* a kulcsok kezdetének részei. A kulcsok készletét logikailag csoportosíthatja a nevükben szereplő előtag használatával. Az előtagok több olyan összetevőt is tartalmazhatnak, amelyek elválasztóval, például `/` egy URL-címhez hasonlóan egy névtér létrehozásához. Ilyen hierarchiák akkor hasznosak, ha sok alkalmazás, összetevő-szolgáltatás és környezet kulcsait tárolja egy alkalmazás-konfigurációs tárolóban.

Fontos szem előtt tartani, hogy a kulcsok az alkalmazás kódjára hivatkoznak a megfelelő beállítások értékeinek lekéréséhez. A kulcsok nem változnak, különben minden alkalommal módosítania kell a kódot.

A *címkék* a kulcsok attribútumai. Egy kulcs változatának létrehozásához használatosak. Kijelölhet például címkéket egy kulcs több verziójához. Egy verzió lehet iteráció, környezet vagy más környezetfüggő információ. Az alkalmazás egy másik címke megadásával kérheti a kulcs értékeinek egy teljesen eltérő készletét. Ennek eredményeképpen az összes fontos hivatkozás változatlan marad a kódban.

## <a name="key-value-compositions"></a>Kulcs-érték kompozíciók

Az alkalmazás konfigurációja a vele együtt tárolt összes kulcsot független entitásként kezeli. Az alkalmazás konfigurációja nem kísérli meg a kulcsok közötti kapcsolat következtetését, illetve a kulcs értékének öröklését a hierarchiájuk alapján. A kulcsok több készletét is összesítheti, ha az alkalmazás kódjában a megfelelő konfiguráció halmozásával párosított címkéket használ.

Lássunk erre egy példát. Tegyük fel, hogy van egy **Asset1** nevű beállítása, amelynek értéke változó lehet a fejlesztési környezettől függően. Hozzon létre egy "Asset1" nevű kulcsot egy üres címkével és egy "fejlesztés" nevű címkével. Az első címkében a **Asset1** alapértelmezett értékét helyezi el, és az utóbbiban egy adott értéket helyez el a "fejlesztés" értékre.

A kódban először le kell kérnie a kulcs értékeit címkék nélkül, majd a "fejlesztés" címkével megegyező számú kulcs-értéket kell lekérnie. Ha a második alkalommal kéri le az értékeket, a kulcsok korábbi értékei felül lesznek írva. A .NET Core konfigurációs rendszer lehetővé teszi, hogy az egymásra épülő konfigurációs adat több készletét "verem". Ha egy kulcs egynél több készletben található, az azt tartalmazó utolsó készletet használja. A modern programozási keretrendszer, például a .NET Core esetében ez a halmozási képesség ingyenesen elérhető, ha natív konfigurációs szolgáltatót használ az alkalmazás konfigurálásához. A következő kódrészlet azt mutatja be, hogyan valósítható meg a halmozás egy .NET Core-alkalmazásban:

```csharp
// Augment the ConfigurationBuilder with Azure App Configuration
// Pull the connection string from an environment variable
configBuilder.AddAzureAppConfiguration(options => {
    options.Connect(configuration["connection_string"])
           .Select(KeyFilter.Any, LabelFilter.Null)
           .Select(KeyFilter.Any, "Development");
});
```

[Használjon címkéket a különböző környezetek különböző konfigurációinak engedélyezéséhez](./howto-labels-aspnet-core.md) .

## <a name="app-configuration-bootstrap"></a>Alkalmazás-konfiguráció rendszerindítási szolgáltatása

Az alkalmazás-konfigurációs tároló eléréséhez használhatja a kapcsolati karakterláncát, amely a Azure Portalban érhető el. Mivel a kapcsolatok karakterlánca hitelesítő adatokat tartalmaz, azok titkos kulcsnak tekintendők. Ezeket a titkokat Azure Key Vault kell tárolni, és a kódnak hitelesítenie kell a Key Vault a lekéréséhez.

A jobb lehetőség az Azure Active Directory felügyelt identitások funkciójának használata. A felügyelt identitások esetében csak az alkalmazás-konfigurációs végpont URL-címére van szükség az alkalmazás-konfigurációs tároló eléréséhez. Az URL-címet beágyazhatja az alkalmazás kódjába (például a fájl *appsettings.js* ). A részletekért lásd: az [Azure által felügyelt identitások integrálása](howto-integrate-azure-managed-service-identity.md) .

## <a name="app-or-function-access-to-app-configuration"></a>Alkalmazás-konfigurációhoz való hozzáférés alkalmazása vagy funkciója

A következő módszerek bármelyikével biztosíthat hozzáférést a webalkalmazások vagy függvények alkalmazás-konfigurációjához:

* A Azure Portalon adja meg a kapcsolódási karakterláncot az alkalmazás konfigurációs tárolójához a App Service Alkalmazásbeállítások között.
* Tárolja a kapcsolódási karakterláncot az alkalmazás-konfigurációs tárolóban Key Vault és [hivatkozzon a app Service](../app-service/app-service-key-vault-references.md).
* Az Azure által felügyelt identitások használatával érheti el az alkalmazás konfigurációs tárolóját. További információ: [integrálás az Azure felügyelt identitásokkal](howto-integrate-azure-managed-service-identity.md).
* Az alkalmazás konfigurációjának leküldéses konfigurációja App Servicera. Az alkalmazás konfigurációja olyan exportálási függvényt biztosít (Azure Portal és az Azure CLI-ben), amely közvetlenül a App Serviceba küld adatokat. Ezzel a módszerrel egyáltalán nem kell módosítania az alkalmazás kódját.

## <a name="reduce-requests-made-to-app-configuration"></a>Az alkalmazás konfigurálására tett kérelmek csökkentése

Az alkalmazások konfigurálására irányuló túlzott kérelmek szabályozást vagy túlterhelést okozhatnak. A kérelmek számának csökkentése:

* Növelje a frissítés időtúllépését, különösen akkor, ha a konfigurációs értékek gyakran nem változnak. A [ `SetCacheExpiration` metódus](/dotnet/api/microsoft.extensions.configuration.azureappconfiguration.azureappconfigurationrefreshoptions.setcacheexpiration)használatával új frissítési időtúllépés adható meg.

* Egyetlen Sentinel- *kulcsot* tekinthet meg, és nem figyelheti az egyes kulcsokat. Az összes konfiguráció frissítése csak akkor, ha a Sentinel-kulcs módosul. Lásd: a [dinamikus konfiguráció használata egy ASP.net Core alkalmazásban](enable-dynamic-configuration-aspnet-core.md) példaként.

* A Azure Event Grid használatával fogadhat értesítéseket, amikor a konfiguráció megváltozik, és nem mindig kérdezi le a módosításokat. További információ: az [Azure-alkalmazás konfigurációs eseményeinek átirányítása webes végpontra](./howto-app-configuration-event.md)

## <a name="importing-configuration-data-into-app-configuration"></a>Konfigurációs adatbázis importálása az alkalmazás-konfigurációba

Az alkalmazás konfigurációja lehetőséget biztosít a konfigurációs beállítások tömeges [importálására](./howto-import-export-data.md) az aktuális konfigurációs fájlokból a Azure Portal vagy a parancssori felület használatával. Ugyanezeket a beállításokat használhatja az alkalmazások konfigurációjában lévő értékek exportálására is, például a kapcsolódó tárolók között. Ha egy folyamatos szinkronizálást szeretne beállítani a GitHub-adattárral, a [GitHub-művelettel](./concept-github-action.md) is használhatja, hogy továbbra is használhassa a meglévő verziókövetés gyakorlatait, miközben az alkalmazások konfigurációjának előnyeit is kihasználhatja.

## <a name="multi-region-deployment-in-app-configuration"></a>Többrégiós üzembe helyezés az alkalmazás konfigurációjában

Az alkalmazás konfigurációja regionális szolgáltatás. Régiónként eltérő konfigurációval rendelkező alkalmazások esetén a konfigurációk egyetlen példányban való tárolása egyetlen meghibásodási pontot is létrehozhat. Egy alkalmazás-konfigurációs példányok régiónként több régióban való üzembe helyezése jobb megoldás lehet. Segíthet a regionális katasztrófa-helyreállításban, a teljesítményben és a biztonsági silóban. A régió szerinti konfigurálás Emellett javítja a késést, és elválasztott szabályozási kvótákat használ, mivel a szabályozás egy példányon alapul. A vész-helyreállítási enyhítés alkalmazásához [több konfigurációs tárolót](./concept-disaster-recovery.md)is használhat. 

## <a name="client-applications-in-app-configuration"></a>Ügyfélalkalmazások az alkalmazások konfigurációjában 

Az alkalmazások konfigurálására irányuló túlzott kérelmek szabályozást vagy túlterhelést okozhatnak. Az alkalmazások a jelenleg elérhető gyorsítótárazás és intelligens frissítés előnyeit használják az általuk küldött kérések számának optimalizálására. Ez a folyamat nagy mennyiségű ügyfélalkalmazások számára is tükrözhető azáltal, hogy elkerüli a közvetlen kapcsolatot a konfigurációs tárolóval. Ehelyett az ügyfélalkalmazások egy egyéni szolgáltatáshoz csatlakoznak, és ez a szolgáltatás kommunikál a konfigurációs tárolóval. Ez a proxy-megoldás biztosítja, hogy az ügyfélalkalmazások ne közelítsék meg a konfigurációs tároló szabályozási korlátját. A szabályozással kapcsolatos további információkért tekintse meg [a gyakori kérdéseket](./faq.yml#are-there-any-limits-on-the-number-of-requests-made-to-app-configuration).  

## <a name="next-steps"></a>Következő lépések

* [Kulcsok és értékek](./concept-key-value.md)