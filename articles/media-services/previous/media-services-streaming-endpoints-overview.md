---
title: A Azure Media Services streaming Endpoint áttekintése | Microsoft Docs
description: Ez a cikk áttekintést nyújt Azure Media Services streaming-végpontokról.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
writer: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.openlocfilehash: 0961b52ebc7271fabf4cc05ed99eea23d911a2d4
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2021
ms.locfileid: "103009092"
---
# <a name="streaming-endpoints-overview"></a>Folyamatos átviteli végpontok áttekintése  

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> A Media Services v2 nem fog bővülni újabb funkciókkal és szolgáltatásokkal. <br/>Tekintse meg a legújabb, [Media Services v3](../latest/index.yml)verziót. Lásd még: [az áttelepítési útmutató v2-től v3-ig](../latest/migrate-v-2-v-3-migration-introduction.md)

Microsoft Azure Media Services (AMS) esetében a **folyamatos átviteli végpont** olyan adatfolyam-szolgáltatást jelöl, amely közvetlenül egy ügyfél-lejátszó alkalmazásnak, illetve egy Content DELIVERY Network (CDN) számára biztosít tartalmat a további terjesztéshez. A Media Services zökkenőmentes Azure CDN integrációt is biztosít. A Streamvégpontok szolgáltatás kimenő adatfolyama lehet élő stream, igény szerinti videó vagy a Media Services fiókjában lévő eszköz progresszív letöltése. Minden Azure Media Services fiók tartalmaz egy alapértelmezett Streamvégpontok. További StreamingEndpoints hozhatók létre a fiókban. A StreamingEndpoints két verziója létezik: 1,0 és 2,0. A 2017. januártól kezdődően minden újonnan létrehozott AMS-fiók tartalmazni fogja az 2,0-es verziójú **alapértelmezett** streamvégpontok. Az ehhez a fiókhoz hozzáadott további streaming-végpontok a 2,0-es verzióban is megjelennek. Ez a változás nem érinti a meglévő fiókokat; a meglévő StreamingEndpoints a 1,0-es verzió lesz, és frissíthető az 2,0-es verzióra. Ennek a változásnak a viselkedése, a számlázás és a szolgáltatások változásai (további információért lásd az alábbi, a **folyamatos átvitel típusai és verziói** című szakaszt).

Azure Media Services a következő tulajdonságokat adta hozzá a streaming Endpoint entitáshoz: **CdnProvider**, **CdnProfile**, **StreamingEndpointVersion**. A tulajdonságok részletes áttekintését [itt](/rest/api/media/operations/streamingendpoint)tekintheti meg. 

Azure Media Services fiók létrehozásakor a rendszer az alapértelmezett szabványos folyamatos átviteli végpontot hozza létre a **leállított** állapotban. Az alapértelmezett folyamatos átviteli végpont nem törölhető. A célként megadott régióban elérhető Azure CDN rendelkezésre állástól függően az újonnan létrehozott alapértelmezett adatfolyam-végpont az "StandardVerizon" CDN-szolgáltató integrációját is tartalmazza. 
                
> [!NOTE]
> Az Azure CDN integrációja le lehet tiltani a streaming végpont elindítása előtt. A `hostname` és a folyamatos átviteli URL-cím ugyanaz marad, függetlenül attól, hogy engedélyezi-e a CDN-t.

Ez a témakör áttekintést nyújt a folyamatos átviteli végpontok által biztosított főbb funkciókról.

## <a name="naming-conventions"></a>Elnevezési konvenciók

Az alapértelmezett végpont esetében: `{AccountName}.streaming.mediaservices.windows.net`

További végpontok esetén: `{EndpointName}-{AccountName}.streaming.mediaservices.windows.net`

## <a name="streaming-types-and-versions"></a>Folyamatos átviteli típusok és verziók

### <a name="standardpremium-types-version-20"></a>Standard/prémium típusok (2,0-es verzió)

Media Services január 2017-os kiadásával kezdődően két folyamatos átviteli típus érhető el: **standard** (előzetes verzió) és **prémium**. Ezek a típusok az "2,0" streaming Endpoint verzió részét képezik.


|Típus|Leírás|
|--------|--------|  
|**Standard**|Az alapértelmezett folyamatos átviteli végpont egy **szabványos** típus, amely a folyamatos átviteli egységek beállításával módosítható a prémium típusra.|
|**Prémium** |Ez a lehetőség olyan professzionális forgatókönyvekhez alkalmas, amelyek nagyobb méretezést vagy irányítást igényelnek. A **prémium** típusra váltson a folyamatos átviteli egységek beállításával.<br/>A dedikált streaming-végpontok elszigetelt környezetben laknak, és nem versenyeznek az erőforrásokkal.|

Azon ügyfelek számára, akik nagy internetes célközönségeknek szánt tartalmat szeretnének kézbesíteni, javasoljuk, hogy engedélyezze a CDN használatát a streaming végponton.

További részletekért tekintse meg az [adatfolyam-típusok összehasonlítása](#comparing-streaming-types) következő szakaszt.

### <a name="classic-type-version-10"></a>Klasszikus típus (1,0-es verzió)

Azok a felhasználók, akik a januári 10 2017-es kiadás előtt hoztak létre AMS-fiókokat, **klasszikus** típusú adatfolyam-végponttal rendelkeznek. Ez a típus a "1,0" streaming Endpoint verziójának része.

Ha a **"1,0"** adatfolyam-végpont >= 1 Premium streaming Units (su), akkor a prémium szintű streaming végpont lesz, és az összes AMS-funkciót (a **standard/prémium** típushoz hasonlóan) a további konfigurációs lépések nélkül fogja biztosítani.

>[!NOTE]
>A **klasszikus** streaming-végpontok (a "1,0" és a 0 su) korlátozott funkciókat biztosítanak, és nem tartalmaznak SLA-t. Ajánlott áttérni a **standard** típusra, hogy jobb élményt kapjon, és olyan szolgáltatásokat használjon, mint például a dinamikus csomagolás vagy a titkosítás, valamint a **standard** típushoz tartozó egyéb funkciók. A **standard** típusra való átálláshoz lépjen a [Azure Portalra](https://portal.azure.com/) , és válassza a **standard** szintű beléptetés lehetőséget. További információ az áttelepítésről: [áttelepítés](#migration-between-types) szakasz.
>
>Ügyeljen arra, hogy a művelet nem állítható vissza, és a díjszabás hatással van.
>
 
## <a name="comparing-streaming-types"></a>Adatfolyam-típusok összehasonlítása

### <a name="versions"></a>Verziók

|Típus|StreamingEndpointVersion|ScaleUnits|Tartalomkézbesítési hálózat (CDN)|Számlázás|
|--------------|----------|-----------------|-----------------|-----------------|
|Klasszikus|1.0|0|NA|Ingyenes|
|Standard streaming Endpoint (előzetes verzió)|2.0|0|Yes|Fizetős|
|prémium streamelési egység|1.0|>0|Yes|Fizetős|
|prémium streamelési egység|2.0|>0|Yes|Fizetős|

### <a name="features"></a>Funkciók

Szolgáltatás|Standard|Prémium
---|---|---
Teljesítmény |Akár 600 Mbps, és a CDN használata esetén sokkal nagyobb hatékonyságot biztosít.|200 MB/s átviteli egység (SU). Sokkal nagyobb hatékonyságot biztosíthat a CDN használatakor.
Tartalomkézbesítési hálózat (CDN)|Azure CDN, harmadik féltől származó CDN vagy nincs CDN.|Azure CDN, harmadik féltől származó CDN vagy nincs CDN.
A számlázás arányosan történik| Napi|Napi
Dinamikus titkosítás|Igen|Yes
Dinamikus csomagolás|Igen|Yes
Méretezés|Automatikus méretezés a célként megadott átviteli sebességgel.|További folyamatos átviteli egységek.
IP-szűrés/G20/egyéni gazdagép <sup>1</sup>|Igen|Yes
Progresszív letöltés|Igen|Yes
Ajánlott használat |A folyamatos átviteli forgatókönyvek túlnyomó többségét ajánljuk.|Professzionális használat. 

<sup>1</sup> csak akkor használható közvetlenül a folyamatos átviteli végponton, ha a CDN nincs engedélyezve a végponton.<br/>

SLA-információ: [díjszabás és SLA](https://azure.microsoft.com/pricing/details/media-services/).

## <a name="migration-between-types"></a>Áttelepítés típusok között

Forrás | Művelet | Művelet
---|---|---
Klasszikus|Standard|Be kell jelentkeznie
Klasszikus|Prémium| Skála (további folyamatos átviteli egységek)
Standard/prémium|Klasszikus|Nem érhető el (ha a streaming Endpoint verziója 1,0. A klasszikusra való váltás a "0" scaleunits beállítással lehetséges.
Standard (CDN-vel/anélkül)|Prémium ugyanazzal a konfigurációval|**Elindítva** állapotban engedélyezett. (Azure Portal használatával)
Prémium (CDN-vel/anélkül)|Standard ugyanazzal a konfigurációval|Elindított állapotban  engedélyezett (Azure Portalon keresztül)
Standard (CDN-vel/anélkül)|Prémium különböző konfigurációval|Leállított állapotban  engedélyezett (Azure Portalon keresztül). Futó állapotban nem engedélyezett.
Prémium (CDN-vel/anélkül)|Standard különböző konfigurációval|Leállított állapotban  engedélyezett (Azure Portalon keresztül). Futó állapotban nem engedélyezett.
1,0-es verzió, SU >= 1 és CDN|Standard/prémium szintű CDN nélkül|Leállított állapotban  engedélyezett. Az **elindított** állapotban nem engedélyezett.
1,0-es verzió, SU >= 1 és CDN|Standard/CDN nélkül|Leállított állapotban  engedélyezett. Az **elindított** állapotban nem engedélyezett. A 1,0-es verziójú CDN törölve lesz, és a rendszer létrehoz és elindít egy újat.
1,0-es verzió, SU >= 1 és CDN|Prémium/CDN nélkül|Leállított állapotban  engedélyezett. Az **elindított** állapotban nem engedélyezett. A klasszikus CDN törölve lesz, és a rendszer létrehoz és elindít egy újat.

## <a name="next-steps"></a>Következő lépések
Tekintse át a Media Services képzési terveket.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
