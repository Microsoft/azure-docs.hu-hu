---
# <a name="mandatory-fields-see-more-on-akamsskyeyemeta"></a>Kötelező mezők. További információ: aka.ms/skyeye/meta.
title: streaming-végpontok (forrás): Azure Media Services Leírás: Ismerje meg a streaming endpoints (forrás), a dinamikus csomagolást és a streaming Service-t, amely közvetlenül az ügyfél vagy egy Content Delivery Network (CDN) számára biztosít tartalmat. szolgáltatások: Media-Services documentationcenter: ' ' Author: IngridAtMicrosoft Manager: femila Editor: ' '

MS. Service: Media-Services MS. munkaterhelés: MS. topic: konceptuális MS. Date: 02/13/2020 MS. Author: inhenkel
---

# <a name="streaming-endpoints-origin-in-azure-media-services"></a>Streaming-végpontok (forrás) Azure Media Services

Microsoft Azure Media Services a [folyamatos átviteli végpontok](/rest/api/media/streamingendpoints) egy dinamikus (igény szerinti) csomagolást és forrást jelentenek, amely az élő és az igény szerinti tartalmat közvetlenül egy ügyfél-alkalmazáshoz irányítja, amely a közös Streaming Media protokollok (HLS vagy Dash) egyikét használja. Emellett az adatfolyam- **végpont** dinamikus (igény szerinti) titkosítást biztosít az iparági vezető DRMs számára. 

Media Services fiók létrehozásakor a rendszer egy **alapértelmezett** folyamatos átviteli végpontot hoz létre egy leállított állapotban. A fiók alatt több folyamatos átviteli végpont is létrehozható (lásd a [kvótákat és a korlátozásokat](limits-quotas-constraints.md)).

> [!NOTE]
> A streaming-videók elindításához el kell indítania azt a **folyamatos átviteli végpontot** , amelyről továbbítani szeretné a videót.
>
> Csak akkor számítunk fel díjat, ha a folyamatos átviteli végpont futó állapotban van.

Tekintse át a [dinamikus csomagolással](dynamic-packaging-overview.md) foglalkozó témakört is. 

## <a name="naming-convention"></a>Elnevezési konvenció

A streaming URL-cím állomásnév-formátuma a következő: `{servicename}-{accountname}-{regionname}.streaming.media.azure.net` , ahol `servicename` = a folyamatos átviteli végpont neve vagy az élő esemény neve.

Az alapértelmezett folyamatos átviteli végpont használatakor `servicename` a rendszer kihagyja az URL-címet: `{accountname}-{regionname}.streaming.azure.net` .

### <a name="limitations"></a>Korlátozások

* Az adatfolyam-végpont neve legfeljebb 24 karakterből állhat.
* A névnek ezt a [regex](/dotnet/standard/base-types/regular-expression-language-quick-reference) -mintát kell követnie: `^[a-zA-Z0-9]+(-*[a-zA-Z0-9])*$` .

## <a name="types"></a>Típusok

Kétféle **adatfolyam-végpont** létezik: **standard** (előzetes verzió) és **prémium**. A típust az `scaleUnits` adatfolyam-végpont számára lefoglalt méretezési egységek () száma határozza meg.

A folyamatos átviteli egység maximális korlátja általában 10. Lépjen kapcsolatba [velünk,](https://azure.microsoft.com/support/create-ticket/) hogy növelje a fiók korlátját.

A táblázat a típusokat írja le:

|Típus|Skálázási egységek|Description|
|--------|--------|--------|  
|**Standard**|0|Az alapértelmezett folyamatos átviteli végpont egy **szabványos** típus, amely módosítható a prémium típusra `scaleUnits` .|
|**Prémium**|>0|**Prémium** szintű A streaming-végpontok speciális számítási feladatokhoz és dedikált és skálázható sávszélesség-kapacitás biztosításához alkalmasak. A **prémium** típusra a `scaleUnits` (folyamatos átviteli egységek) beállításával léphet be. `scaleUnits` dedikált kimenő kapacitást kínál, amely 200 Mbps-os növekményekben vásárolható meg. A **prémium** típus használata esetén minden engedélyezett egység további sávszélesség-kapacitást biztosít az alkalmazás számára. |

> [!NOTE]
> Azon ügyfelek számára, akik nagy internetes célközönségeknek szánt tartalmat szeretnének kézbesíteni, javasoljuk, hogy engedélyezze a CDN használatát a streaming végponton.

SLA-információ: [díjszabás és SLA](https://azure.microsoft.com/pricing/details/media-services/).

## <a name="comparing-streaming-types"></a>Adatfolyam-típusok összehasonlítása

Szolgáltatás|Standard|Prémium
---|---|---
Teljesítmény |Akár 600 Mbps, és a CDN használata esetén sokkal nagyobb hatékonyságot biztosít.|200 MB/s átviteli egység (SU). Sokkal nagyobb hatékonyságot biztosíthat a CDN használatakor.
Tartalomkézbesítési hálózat (CDN)|Azure CDN, harmadik féltől származó CDN vagy nincs CDN.|Azure CDN, harmadik féltől származó CDN vagy nincs CDN.
A számlázás arányosan történik| Napi|Napi
Dinamikus titkosítás|Igen|Yes
Dinamikus csomagolás|Igen|Yes
Méretezés|Automatikus méretezés a célként megadott átviteli sebességgel.|További SUs
IP-szűrés/G20/egyéni gazdagép <sup>1</sup>|Igen|Yes
Progresszív letöltés|Igen|Yes
Ajánlott használat |A folyamatos átviteli forgatókönyvek túlnyomó többségét ajánljuk.|Professzionális használat.

<sup>1</sup> csak akkor használható közvetlenül a folyamatos átviteli végponton, ha a CDN nincs engedélyezve a végponton.<br/>

## <a name="streaming-endpoint-properties"></a>Adatfolyam-végpont tulajdonságai

Ez a szakasz részletesen ismerteti a folyamatos átviteli végpontok tulajdonságait. Az új adatfolyam-végpontok és az összes tulajdonság leírásának ismertetését példákat a [streaming Endpoint (adatfolyam-végpont](/rest/api/media/streamingendpoints/create)) című témakörben talál.

- `accessControl`: A következő biztonsági beállítások konfigurálásához használható a folyamatos átviteli végponthoz: Akamai aláírási fejléc-hitelesítési kulcsok és IP-címek, amelyek számára engedélyezett a végponthoz való kapcsolódás. Ez a tulajdonság csak akkor állítható be `cdnEnabled` , ha hamis értékre van állítva.

- `cdnEnabled`: Azt jelzi, hogy engedélyezve van-e a folyamatos átviteli végpont Azure CDN integrációja (alapértelmezés szerint le van tiltva). Ha az értéke `cdnEnabled` true (igaz), a következő konfigurációk le vannak tiltva: `customHostNames` és `accessControl` .

    Nem minden adatközpont támogatja a Azure CDN integrációt. Az alábbi lépéseket követve ellenőrizheti, hogy az adatközpont rendelkezik-e a Azure CDN integrációs szolgáltatással:

  - Próbálja meg a igaz értéket beállítani `cdnEnabled` .
  - Tekintse át a (z `HTTP Error Code 412` ) (PreconditionFailed) visszaadott eredményét "a streaming Endpoint CdnEnabled tulajdonság nem állítható igaz értékre, mert a CDN-képesség nem érhető el az aktuális régióban."

    Ha ezt a hibaüzenetet kapja, az adatközpont nem támogatja azt. Próbálkozzon egy másik adatközpontgal.

- `cdnProfile`: Ha a `cdnEnabled` értéke TRUE (igaz), akkor értékeket is megadhat `cdnProfile` . `cdnProfile` annak a CDN-profilnak a neve, amelybe a CDN-végpontot létrehozza a rendszer. Megadhat egy meglévő cdnProfile, vagy használhat egy újat. Ha az érték NULL `cdnEnabled` , és igaz, a rendszer az alapértelmezett "AzureMediaStreamingPlatformCdnProfile" értéket használja. Ha a megadott `cdnProfile` érték már létezik, a rendszer létrehoz egy végpontot. Ha a profil nem létezik, a rendszer automatikusan létrehoz egy új profilt.
- `cdnProvider`: Ha a CDN engedélyezve van, értékeket is megadhat `cdnProvider` . `cdnProvider` meghatározza, hogy melyik szolgáltatót fogja használni. Jelenleg három érték támogatott: "StandardVerizon", "PremiumVerizon" és "StandardAkamai". Ha nincs megadva érték, és `cdnEnabled` igaz, a rendszer a "StandardVerizon" értéket használja (ez az alapértelmezett érték).
- `crossSiteAccessPolicies`: A különböző ügyfelekhez tartozó helyek közötti hozzáférési házirendek megadására használatos. További információkért lásd: tartományok [közötti házirend fájljának specifikációja](https://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html) és [egy szolgáltatás elérhetővé tétele a tartomány határain belül](/previous-versions/azure/azure-services/gg185950(v=azure.100)). A beállítások csak Smooth Streamingra vonatkoznak.
- `customHostNames`: Egy adatfolyam-végpont konfigurálására szolgál az egyéni állomásnévre irányuló forgalom fogadásához. Ez a tulajdonság a standard és a prémium szintű streaming végpontok esetében érvényes, és a következő esetekben állítható be `cdnEnabled` : false.

    Media Servicesnak meg kell erősítenie a tartománynév tulajdonjogát. Media Services ellenőrzi a tartománynevek tulajdonjogát úgy, hogy egy olyan rekordot igényel, `CName` amely a Media Services fiók azonosítóját tartalmazza a használatban lévő tartományhoz hozzáadandó összetevőként. Ha például az "sports.contoso.com" nevet szeretné használni a streaming végpont egyéni állomásneveként, akkor egy rekordot úgy kell `<accountId>.contoso.com` konfigurálni, hogy az Media Services ellenőrző állomásnév egyikére mutasson. Az ellenőrző gazdagép neve verifydns. \<mediaservices-dns-zone> .

    A következő, a különböző Azure-régiókra vonatkozó ellenőrzési rekordban használandó DNS-zónák a következők:
  
  - Észak-Amerika, Európa, Szingapúr, Hongkong KKT, Japán:

    - `media.azure.net`
    - `verifydns.media.azure.net`

  - Kína

    - `mediaservices.chinacloudapi.cn`
    - `verifydns.mediaservices.chinacloudapi.cn`

    Egy olyan rekord például, `CName` amely a "945a4c4e-28ea-45cd-8ccb-a519f6b700ad.contoso.com" és a "verifydns.Media.Azure.net" értékre mutat, igazolja, hogy a 945a4c4e-28ea-45cd-8ccb-a519f6b700ad-azonosító a contoso.com tartomány tulajdonjogát képezi, így Media Services a contoso.com alatti bármely név az adott fiókhoz tartozó streaming-végpont egyéni állomásneveként használható. A Media Service ID értékének megkereséséhez nyissa meg a [Azure Portal](https://portal.azure.com/) , és válassza ki a Media Service-fiókját. A **fiók azonosítója** megjelenik az oldal jobb felső sarkában.

    Ha egy egyéni állomásnév beállítása a rekord megfelelő ellenőrzése nélkül történt meg `CName` , a DNS-válasz meghiúsul, majd egy ideig gyorsítótárazva lesz. Ha megfelelő rekord van érvényben, eltarthat egy ideig, amíg újra nem érvényesítette a gyorsítótárazott választ. Az egyéni tartomány DNS-szolgáltatójától függően néhány perctől akár egy óráig is eltarthat, hogy újraérvényesítse a rekordot.

    Az `CName` adott leképezésen kívül `<accountId>.<parent domain>` `verifydns.<mediaservices-dns-zone>` létre kell hoznia egy másikat, `CName` amely leképezi az egyéni állomásnevet (például `sports.contoso.com` ) a Media Services streaming-végpont állomásneve (például `amstest-usea.streaming.media.azure.net` ).

    > [!NOTE]
    > Az ugyanabban az adatközpontban található adatfolyam-végpontok nem oszthatják meg ugyanazt az egyéni állomásnevet.

    A Media Services jelenleg nem támogatja a TLS-t egyéni tartományokkal.

- `maxCacheAge` – Felülbírálja a streaming végpont által a média töredékeken és az igény szerinti jegyzékeken beállított alapértelmezett Max-Age HTTP-gyorsítótár-vezérlő fejlécet. Az érték másodpercben van megadva.
- `resourceState` -

    - Leállítva: egy adatfolyam-végpont kezdeti állapota a létrehozás után
    - Indítás: váltás a futó állapotra
    - Futtatás: képes a tartalom továbbítására az ügyfeleknek
    - Skálázás: a méretezési egységek növelése vagy csökkentése folyamatban van
    - Leállítás: a leállított állapotba való váltás folyamatban van
    - Törlés: törlése folyamatban van

- `scaleUnits`: Olyan dedikált kimenő kapacitást biztosít, amelyet 200 Mbps-os növekményekben lehet megvásárolni. Ha **prémium** szintű típusra szeretne lépni, módosítsa a következőt: `scaleUnits` .

## <a name="why-use-multiple-streaming-endpoints"></a>Miért érdemes több folyamatos átviteli végpontot használni?

Egyetlen Streaming végpont is képes az élő és igény szerinti videók továbbítására, és a legtöbb ügyfél csak egy folyamatos átviteli végpontot használ. Ez a szakasz példákat tartalmaz arra, hogy miért van szükség több folyamatos átviteli végpont használatára.

* Minden fenntartott egység 200 Mbps sávszélességet tesz lehetővé. Ha több mint 2 000 Mbps (2 GB/s) sávszélességre van szüksége, a második adatfolyam-végpontot és a terheléselosztást használva további sávszélességet biztosíthat.

    A CDN azonban a legjobb módszer a tartalmak továbbítására, de ha olyan tartalmat hoz létre, amelyet a CDN több mint 2 GB-nál nagyobb mértékben húz, akkor további folyamatos átviteli végpontokat (Origins) adhat hozzá. Ebben az esetben olyan tartalmi URL-címeket kell kiadnia, amelyek kiegyensúlyozottak a két folyamatos átviteli végpont között. Ez a megközelítés nagyobb gyorsítótárazást tesz lehetővé, mint az egyes forrásokra irányuló kérések véletlenszerűen történő küldése (például egy Traffic Manageren keresztül). 
    
    > [!TIP]
    > Általában, ha a CDN több mint 2 GB-ot húz, akkor előfordulhat, hogy valami helytelenül van konfigurálva (például nem származik a forrás elleni védelem).
    
* Különböző CDN-szolgáltatók terheléselosztása. Például beállíthatja az alapértelmezett folyamatos átviteli végpontot a Verizon CDN használatára, és létrehoz egy másodikat a Akamai használatához. Ezután vegyen fel némi terheléselosztást a kettő között a többszörös CDN-egyensúly eléréséhez. 

    Az ügyfél azonban gyakran végez terheléselosztást több CDN-szolgáltató között egyetlen forrás használatával.
* Vegyes tartalom folyamatos átvitele: igény szerint élő és videó. 

    Az élő és igény szerinti tartalmakhoz való hozzáférési minták nagyon különbözőek. Az élő tartalom általában sok igényt kap ugyanarra a tartalomra. Az igény szerinti videó (például hosszú farok archiválási tartalma) esetében a tartalom használata alacsony. Így a gyorsítótárazás nagyon jól működik az élő tartalomon, de nem a hosszú farok tartalmán is.

    Vegyünk egy olyan forgatókönyvet, amelyben az ügyfelek elsősorban élő tartalmakat nézik, de csak alkalmanként figyelik az igény szerinti tartalmakat, és ugyanabból a streaming-végpontból lesznek kézbesítve. Az igény szerinti tartalom alacsony használata olyan gyorsítótár-területet foglal magában, amely jobb lesz az élő tartalomhoz. Ebben a forgatókönyvben azt javasoljuk, hogy az élő tartalmat egy streaming végpontból és egy másik adatfolyam-végpontból származó hosszú farok tartalma alapján szolgálja ki. Ez növeli az élő esemény tartalmának teljesítményét.
    
## <a name="scaling-streaming-with-cdn"></a>Streamelés méretezése CDN-nel

Lásd az alábbi cikkeket:

- [A CDN áttekintése](../../cdn/cdn-overview.md)
- [Streamelés méretezése CDN-nel](scale-streaming-cdn.md)

## <a name="ask-questions-and--get-updates"></a>Kérdések és frissítések kérése

Tekintse meg a [Azure Media Services közösségi](media-services-community.md) cikket, amely különböző módokon jelenítheti meg a kérdéseket, visszajelzéseket küldhet, és frissítéseket kaphat a Media Servicesról.

## <a name="see-also"></a>Lásd még

[Dinamikus csomagolás](dynamic-packaging-overview.md)

## <a name="next-steps"></a>Következő lépések

[Streamvégpontok kezelése](manage-streaming-endpoints-howto.md)
