---
title: Az Azure App konfigurációs kulcs-érték tároló |} A Microsoft Docs
description: Konfigurációs adatok tárolási módját az Azure-alkalmazások konfigurálása áttekintése
services: azure-app-configuration
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.service: azure-app-configuration
ms.devlang: na
ms.topic: overview
ms.workload: tbd
ms.date: 04/19/2019
ms.author: yegu
ms.openlocfilehash: b25cc8c04aed8cd333ff4de5b12db6674323787d
ms.sourcegitcommit: 51a7669c2d12609f54509dbd78a30eeb852009ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/30/2019
ms.locfileid: "66393601"
---
# <a name="keys-and-values"></a>Kulcsok és értékek

Az Azure App konfigurációs kulcs-érték párok konfigurációs adatokat tárolja. Kulcs-érték párok módon egy egyszerű, de a rugalmas alkalmazásbeállításokat, amelyek a fejlesztők számára is felismerhetők a különböző típusú jelölik.

## <a name="keys"></a>Kulcsok

Kulcsok számára a kulcs-érték párok szolgál, és tárolására és beolvasására a megfelelő értékeket használják. Egy általános gyakorlat a hierarchikus névtér a határolójel, például a kulcsok szervezheti `/` vagy `:`. Az alkalmazás, amely a leginkább megfelelő megoldást szabályt használ. Alkalmazás konfigurálása kulcsok teljes kezeli. Döntse el, hogyan nevük struktúrája vagy kényszerítése minden szabály azokat a kulcsokat, nem elemezni.

Konfigurációs adatok belüli alkalmazás-keretrendszerek használata függhet, adott elnevezési sémát kulcs értékeit. Tegyük fel, a Java Spring Cloud keretrendszerben meghatározása `Environment` megadhatja azokat a beállításokat tartalmazó változókat lehet paraméterezni a Spring-alkalmazás az erőforrások *alkalmazásnév* és *profil*. Kulcsok Spring Cloud kapcsolatos konfigurációs adatok általában indítsa el a két elem egy elválasztó karakter választja el.

Alkalmazáskonfiguráció tárolt kulcsok olyan kis-és nagybetűket, unicode-alapú karakterláncok. A kulcsok *app1* és *App1* nem azonos egy alkalmazás a konfigurációs adattárolónál a. Ne feledje az alkalmazáson belül a konfigurációs beállítások használatakor, mivel bizonyos keretrendszerek konfigurációs kulcsok case-insensitively kezeli. Például az ASP.NET Core konfigurációs kezeli a rendszer kulcsok nem betűérzékeny karakterláncként. Alkalmazások konfigurálása az ASP.NET Core-alkalmazáson belül lekérdezheti, kiszámíthatatlan viselkedés elkerülése érdekében, ne használjon, amelyet csak a kis-és nagybetűhasználatának eltérő kulcsok.

Bármely unicode karaktert használhatja az Alkalmazáskonfigurációt, kivéve a megadott kulcsnevek `*`, `,`, és `\`. Ezek a karakterek vannak fenntartva. Egy fenntartott karaktert használniuk kell, ha meg kell escape azt használatával `\{Reserved Character}`. Az egy kulcs-érték pár 10 000 karakterek összesített mérete korlátozva van. Ezt a korlátot a kulcsot, az értékét az összes karaktert tartalmaz, és minden ahhoz kapcsolódó attribútumok nem kötelező. Ezt a határt, belül számos hierarchikus szintek, a kulcsok rendelkezhet.

### <a name="design-key-namespaces"></a>Kialakítási kulcs névterek

Kétféleképpen általános konfigurációs adatok használt kulcsok elnevezési: egybesimított vagy hierarchikus. Ezek a metódusok alkalmazás használati szempontjából hasonló, de hierarchikus elnevezési kínál számos előnnyel jár:

* Könnyebben olvasható. Egy hosszú megadott karaktersorozattal, helyett egy hierarchikus kulcsnév az elválasztó karakterek mondaton belüli tárolóhelyek szerepét. Természetes szünetek a szavak közötti is biztosítanak.
* Könnyebben kezelhető. A kulcsnév hierarchia logikai csoportok konfigurációs adatokat jelöli.
* Könnyebben használható. Sokkal egyszerűbb, írhat egy lekérdezést, amely a minta-egyezések hierarchikus kulcsokat, és beolvassa a konfigurációs adatok egy részét. Emellett számos újabb programozási keretrendszer rendelkeznie natív módon támogatja a hierarchikus konfigurációs adatok úgy, hogy az alkalmazás győződjön meg arról is, használja az adott csoportok konfigurációjának.

Kulcsokat az Alkalmazáskonfigurációt számos módon hierarchikusan rendezhetők. Az ilyen kulcsok, gondoljon [URI-k](https://en.wikipedia.org/wiki/Uniform_Resource_Identifier). Minden hierarchikus kulcs egy olyan erőforrás *elérési* mikroszolgáltatásokból álló, egy vagy több összetevőt, amely az elválasztó karakterek össze vannak kapcsolva. Válassza ki a használandó milyen az alkalmazást, programozási nyelvet vagy keretrendszert igények alapján elválasztó karaktere. Több elválasztó karakterek használata az alkalmazások konfigurálásának különböző kulccsal.

Íme néhány példa hogyan strukturálhatja a kulcsnevek hierarchiára:

* A Komponensszolgáltatások alapján

        AppName:Service1:ApiEndpoint
        AppName:Service2:ApiEndpoint

* A telepítési régió alapján

        AppName:Region1:DbEndpoint
        AppName:Region2:DbEndpoint

### <a name="label-keys"></a>Címke kulcsok

Az alkalmazás konfigurációs értékek igény szerint rendelkezhet egy címke attribútum. Címkék segítségével különbséget tenni a kulcs értékeit ugyanazzal a kulccsal. A kulcs *app1* , a címkék *A* és *B* forms-alkalmazás a konfigurációs adattárolónál a két külön kulcs. Alapértelmezés szerint a kulcs értékét a címke az üres, vagy `null`.

Címke hozzon létre egy kulcsot változatának kényelmes módot biztosít. Egyik gyakori felhasználási címkéket, hogy adja meg az ugyanazon kulcshoz több környezetet:

    Key = AppName:DbEndpoint & Label = Test
    Key = AppName:DbEndpoint & Label = Staging
    Key = AppName:DbEndpoint & Label = Production

### <a name="version-key-values"></a>Verzió kulcsértékek

Alkalmazáskonfiguráció éppen módosított automatikusan nem verzió kulcs értékeit. Címkék használata arra, hogy a kulcs értékét több verzióját. Például megadhatja, hogy egy alkalmazás verziószáma, vagy egy adott build társított egy Git véglegesítési Azonosítóját, a címkék értékek azonosításához.

A címkék kivételével bármely unicode karaktert használhatja `*`, `,`, és `\`. Ezek a karakterek vannak fenntartva. A fenntartott karaktert tartalmaz, meg kell escape-, használatával `\{Reserved Character}`.

### <a name="query-key-values"></a>Lekérdezési kulcs értékeit

Minden kulcsérték egyedileg azonosítja a kulcsok és a egy címke, amely lehet `null`. Adjon meg egy minta lekérdezheti, ha egy alkalmazás-konfigurációs áruházban, a kulcs értékeit. Az alkalmazás a konfigurációs adattárolónál az összes kulcsérték, amelyek megfelelnek a minta és a megfelelő értékeket és attribútumokat adja vissza. A következő kulcs minták használja a REST API-hívások konfigurálása:

| Kulcs | |
|---|---|
| `key` Nincs megadva, vagy `key=*` | Megfelel az összes kulcs |
| `key=abc` | Egyezések kulcsnév **abc** pontosan |
| `key=abc*` | Egyezések kulcs karakterekkel **abc** |
| `key=*abc` | Egyezések kulcs karakterre végződjön **abc** |
| `key=*abc*` | Egyezések kulcsneveinek tartalmazó **abc** |
| `key=abc,xyz` | Egyezések kulcsneveinek **abc** vagy **xyz**, legfeljebb öt CSV-k |

A következő címke minták is lehetnek:

| Címke | |
|---|---|
| `label` Nincs megadva, vagy `label=*` | Bármely címke, amely tartalmazza a megfelelő `null` |
| `label=%00` | Egyezések `null` felirat |
| `label=1.0.0` | Egyezések címke **1.0.0-s** pontosan |
| `label=1.0.*` | Megegyezik a kezdődő címkéket **1.0-t.** |
| `label=*.0.0` | Megegyezik a címkék, amelyek végződhet **.0.0** |
| `label=*.0.*` | Megegyezik a címkék tartalmazó **.0.** |
| `label=%00,1.0.0` | Megegyezik a címkék `null` vagy **1.0.1**, legfeljebb öt CSV-k |

## <a name="values"></a>Értékek

Kulcsok rendelt értékeket is olyan unicode karakterláncok. Az összes unicode-karaktereket értékeket is használhat. Egy nem kötelező felhasználói tartalomtípus társított minden egyes érték van. Ez az attribútum használatával kapcsolatos információk tárolására, például egy kódolási sémát egy értéket, amely segít az alkalmazás megfelelően feldolgozni azt.

Egy alkalmazás a konfigurációs adattároló, amely tartalmazza az összes kulcsokat és értékeket, a tárolt konfigurációs adatok inaktív és átvitel közben titkosítva vannak. Alkalmazások konfigurálása egy helyettesítő megoldás az Azure Key Vault nem. Titkos alkalmazáskulcsok ne tároljon azt.

## <a name="next-steps"></a>További lépések

* [Időponthoz pillanatkép](./concept-point-time-snapshot.md)  
* [A szolgáltatás kezelése](./concept-feature-management.md)  
