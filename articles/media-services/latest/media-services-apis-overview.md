---
# <a name="mandatory-fields-see-more-on-akamsskyeyemeta"></a>Kötelező mezők. További információ: aka.ms/skyeye/meta.
title: fejlesztés a V3 API-kkal: Azure Media Services Leírás: az entitásokra és API-kra vonatkozó szabályok megismerése a Media Services v3 használatával történő fejlesztés során. szolgáltatások: Media-Services documentationcenter: ' ' Author: IngridAtMicrosoft Manager: femila Editor: ' '

MS. Service: Media-Services MS. munkaterhelés: MS. topic: konceptuális MS. Date: 10/23/2020 MS. Author: inhenkel MS. Custom: seodec18

---

# <a name="develop-with-media-services-v3-apis"></a>Fejlesztés a Media Services v3 API-kkal

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Fejlesztőként Media Services [REST API](/rest/api/media/) vagy ügyféloldali kódtárakat használhat, amelyek lehetővé teszik az REST API használatát az egyéni adathordozó-munkafolyamatok egyszerű létrehozásához, kezeléséhez és karbantartásához. Az [Media Services v3](https://aka.ms/ams-v3-rest-sdk) API a OpenAPI-specifikáción alapul (korábbi nevén a hencegés).

Ez a cikk azokat a szabályokat ismerteti, amelyek az entitásokra és API-kra vonatkoznak Media Services v3-val történő fejlesztéskor.

## <a name="accessing-the-azure-media-services-api"></a>Az Azure Media Services API elérése

A Media Services-erőforrások és a Media Services API eléréséhez először hitelesítenie kell magát. A Media Services az [Azure Active Directory- (Azure AD-) alapú](../../active-directory/fundamentals/active-directory-whatis.md) hitelesítést támogatja. Két gyakran használt hitelesítési lehetőség:
 
* **Szolgáltatásnév-hitelesítés**: Szolgáltatások (például: webalkalmazások, függvényalkalmazások, logikai alkalmazások, API-k és mikroszolgáltatások) hitelesítésére szolgál. Ezt a hitelesítési módszert általában a démonszolgáltatásokat, közepes szintű szolgáltatásokat vagy ütemezett feladatokat futtató alkalmazások használják. A webalkalmazások esetében például mindig olyan közepes rétegnek kell lennie, amely a Media Serviceshoz kapcsolódik egy egyszerű szolgáltatással.
* **Felhasználóhitelesítés**: Olyan személyek hitelesítésére szolgál, akik a Media Services-erőforrásokkal való interakcióhoz használják az alkalmazást. Az interaktív alkalmazásnak először fel kell kérnie a felhasználót a hitelesítési adatainak a megadására. Ilyen alkalmazás lehet például egy olyan felügyeletikonzol-alkalmazás, amelyet a jogosultsággal rendelkező felhasználók a kódolási feladatok és az élő streamelés monitorozására használnak.

A Media Services API megköveteli, hogy a REST API-kéréseket kezdeményező felhasználó vagy alkalmazás hozzáféréssel rendelkezzen a Media Services-fiókerőforráshoz, és **Közreműködő** vagy **Tulajdonos** szerepkört használjon. Az API elérhető az **Olvasó** szerepkörrel, de ekkor csak a **lekérési** vagy a **listázási** művelet érhető el.További információ: [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC) Media Services fiókokhoz](security-rbac-concept.md).

Szolgáltatásnév létrehozása helyett érdemes lehet az Azure-erőforrások felügyelt identitásait használni a Media Services API Azure Resource Managerrel történő eléréséhez. Az Azure-erőforrások felügyelt identitásairól az [Azure-erőforrások felügyelt identitásait](../../active-directory/managed-identities-azure-resources/overview.md) ismertető cikkben tájékozódhat bővebben.

### <a name="azure-ad-service-principal"></a>Azure AD egyszerű szolgáltatás

Az Azure AD-alkalmazásnak és az egyszerű szolgáltatásnak ugyanabban a bérlőben kell lennie. Az alkalmazás létrehozása után adja meg az alkalmazás **közreműködői** vagy **tulajdonosi** szerepkörének hozzáférését az Media Services fiókhoz.

Ha nem biztos abban, hogy rendelkezik-e engedéllyel az Azure AD-alkalmazás létrehozásához, tekintse meg a [szükséges engedélyeket](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app).

A következő ábrán a számok kronológiai sorrendben jelenítik meg a kérelmek folyamatát:

![Közepes szintű alkalmazások hitelesítése webes API-HRE](./media/use-aad-auth-to-access-ams-api/media-services-principal-service-aad-app1.png)

1. A középső rétegbeli alkalmazás olyan Azure AD hozzáférési jogkivonatot kér, amely a következő paraméterekkel rendelkezik:  

   * Azure AD-bérlői végpont.
   * Media Services erőforrás URI-ja.
   * A REST Media Services erőforrás-URI-ja.
   * Azure AD-alkalmazás értékei: az ügyfél-azonosító és az ügyfél titka.

   Az összes szükséges érték beszerzéséhez tekintse meg a következőt: [hozzáférés Azure Media Services API](./access-api-howto.md)-hoz.

2. Az Azure AD hozzáférési jogkivonatot a középső szinten küldik el.
4. A középső szintű kérelem küldése az Azure Media REST API az Azure AD-jogkivonattal.
5. A középső szinten a Media Services származó adatok kerülnek vissza.

### <a name="samples"></a>Példák

Tekintse meg a következő mintákat, amelyek bemutatják, hogyan csatlakozhat az Azure AD egyszerű szolgáltatásához:

* [Kapcsolódás a REST-tel](setup-postman-rest-how-to.md)  
* [Kapcsolódás Javával](configure-connect-java-howto.md)
* [Kapcsolódás .NET-tel](configure-connect-dotnet-howto.md)
* [Kapcsolódás Node.js-sel](configure-connect-nodejs-howto.md)
* [Kapcsolódás Pythonnal](configure-connect-python-howto.md)

## <a name="naming-conventions"></a>Elnevezési konvenciók

Az Azure Media Services v3 erőforrásneveire is (pl. Adategység, Feladatok, Átalakítások) az Azure Resource Manager elnevezési korlátozásai vonatkoznak. Az Azure Resource Manager szolgáltatásnak megfelelően az erőforrásnevek mindig egyediek. Így bármilyen egyedi azonosító sztringet (pl. GUID-ok) használhat erőforrásnévként.

Media Services erőforrásnevek nem tartalmazhatják a következőket: "<", ">", "%", "&", ":", "&#92;", "?", "/", "*", "+", ".", egyetlen idézőjel karakter vagy bármely vezérlőelem-karakter. Minden egyéb karakter engedélyezett. Az erőforrásnév maximális hossza 260 karakter.

További információ a Azure Resource Manager elnevezéséről: [elnevezési követelmények](https://github.com/Azure/azure-resource-manager-rpc/blob/master/v1.0/resource-api-reference.md#arguments-for-crud-on-resource) és [elnevezési konvenciók](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging).

### <a name="names-of-filesblobs-within-an-asset"></a>Egy eszközön belüli fájlok/Blobok nevei

Az eszközön belüli fájlok/Blobok nevének a [blob neve](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) és az [NTFS-név követelményeit](/windows/win32/fileio/naming-a-file)is követnie kell. Ennek a követelménynek az oka, hogy a fájlok a blob Storage-ból egy helyi NTFS-lemezre másolhatók feldolgozásra.

## <a name="long-running-operations"></a>Hosszan futó műveletek

A `x-ms-long-running-operation` Azure Media Services [hencegő fájlokban](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/streamingservice.json) megjelölt műveletek hosszú ideig futó műveletek. 

Az aszinkron Azure-műveletek nyomon követésével kapcsolatos részletekért lásd: [aszinkron műveletek](../../azure-resource-manager/management/async-operations.md).

Media Services a következő hosszan futó műveletekkel rendelkezik:

* [Élő események létrehozása](/rest/api/media/liveevents/create)
* [Élő események frissítése](/rest/api/media/liveevents/update)
* [Élő esemény törlése](/rest/api/media/liveevents/delete)
* [Élő esemény indítása](/rest/api/media/liveevents/start)
* [LiveEvent leállítása](/rest/api/media/liveevents/stop)

  A `removeOutputsOnStop` paraméter használatával törölje az összes társított élő kimenetet az esemény leállításakor.  
* [LiveEvent alaphelyzetbe állítása](/rest/api/media/liveevents/reset)
* [LiveOutput létrehozása](/rest/api/media/liveevents/create)
* [LiveOutput törlése](/rest/api/media/liveevents/delete)
* [Streamvégpontok létrehozása](/rest/api/media/streamingendpoints/create)
* [Streamvégpontok frissítése](/rest/api/media/streamingendpoints/update)
* [Streamvégpontok törlése](/rest/api/media/streamingendpoints/delete)
* [Streamvégpontok elindítása](/rest/api/media/streamingendpoints/start)
* [Streamvégpontok leállítása](/rest/api/media/streamingendpoints/stop)
* [Streamvégpontok skálázása](/rest/api/media/streamingendpoints/scale)

A hosszú művelet sikeres beküldésekor egy "201-hoz létrehozott" érték jelenik meg, és a művelet befejezéséhez a visszaadott művelet azonosítója alapján kell lekérdezni a műveletet.

Az [aszinkron Azure-műveletek nyomon követése](../../azure-resource-manager/management/async-operations.md) című cikk részletesen ismerteti, hogyan követheti nyomon az aszinkron Azure-műveletek állapotát a válaszban visszaadott értékek alapján.

Egy adott élő esemény vagy bármely hozzá tartozó élő kimenet esetében csak egy hosszan futó művelet támogatott. Az indítás után a hosszú ideig futó műveletnek meg kell felelnie, mielőtt egy későbbi, hosszan futó műveletet elindítson ugyanazon a LiveEvent vagy a kapcsolódó élő kimeneteken. Több élő kimenettel rendelkező élő események esetén várnia kell egy hosszú ideig futó művelet befejezését egy élő kimeneten, mielőtt a hosszú ideig futó műveletet aktivál egy másik élő kimeneten.

## <a name="sdks"></a>SDK-k

> [!NOTE]
> A Azure Media Services v3 SDK-k nem garantáltak, hogy a szál biztonságos legyen. Többszálas alkalmazások fejlesztésekor saját szál-szinkronizálási logikát kell hozzáadnia az ügyfél védeleméhez, vagy a szálon egy új AzureMediaServicesClient objektumot kell használnia. Ügyeljen arra, hogy a kód által az ügyfélnek (például egy .NET-HttpClient-példány) származó opcionális objektumok által bevezetett többszálas problémák is körültekintőek legyenek.

|SDK|Referencia|
|---|---|
|[.NET SDK](https://aka.ms/ams-v3-dotnet-sdk)|[.NET-referencia](/dotnet/api/overview/azure/mediaservices/management)|
|[Java SDK](https://aka.ms/ams-v3-java-sdk)|[Java-referencia](/java/api/overview/azure/mediaservices/management)|
|[Python SDK](https://aka.ms/ams-v3-python-sdk)|[Python-referencia](/python/api/overview/azure/mediaservices/management)|
|[Node.js SDK](https://aka.ms/ams-v3-nodejs-sdk) |[Node.js-referencia](/javascript/api/overview/azure/mediaservices/management)| 
|[Go SDK](https://aka.ms/ams-v3-go-sdk) |[Go-referencia](https://aka.ms/ams-v3-go-ref)|
|[Ruby SDK](https://aka.ms/ams-v3-ruby-sdk)||

### <a name="see-also"></a>Lásd még

- [A Media Service-eseményeket tartalmazó EventGrid .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/)
- [Media Services események definíciói](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/eventgrid/data-plane/Microsoft.Media/stable/2018-01-01/MediaServices.json)

## <a name="azure-media-services-explorer"></a>Azure Media Services Explorer

A [Azure Media Services Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (AMSE) a Windows-ügyfelek számára elérhető eszköz, akik a Media Servicesről szeretnének többet megtudni. A AMSE egy WinForms/C#-alkalmazás, amely feltölti, letölti, kódolja, továbbítja a VOD-t és az élő tartalmakat Media Services. A AMSE eszköz olyan ügyfelek számára készült, akik kód írása nélkül szeretnék tesztelni Media Services. A AMSE-kód olyan ügyfelek számára biztosít erőforrásként, akik Media Serviceskal szeretnének fejleszteni.

A AMSE egy nyílt forráskódú projekt, amely a Közösség által biztosított támogatással kapcsolatos https://github.com/Azure/Azure-Media-Services-Explorer/issues) . A projekt a Microsoft nyílt forráskódú projekteket szabályozó etikai kódexe, a [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/) hatálya alá esik. További információkért tekintse meg a [viselkedési szabályzatot](https://opensource.microsoft.com/codeofconduct/faq/) , vagy forduljon opencode@microsoft.com más kérdésekhez vagy megjegyzésekhez.

## <a name="filtering-ordering-paging-of-media-services-entities"></a>Media Services entitások szűrése, rendezése és lapozása

Lásd: [Azure Media Services entitások szűrése, rendezése és lapozása](filter-order-page-entitites-how-to.md).

## <a name="ask-questions-give-feedback-get-updates"></a>Kérdések feltevése, visszajelzés küldése, frissítések beszerzése

Tekintse meg a [Azure Media Services közösségi](media-services-community.md) cikket, amely különböző módokon jelenítheti meg a kérdéseket, visszajelzéseket küldhet, és frissítéseket kaphat a Media Servicesról.

## <a name="see-also"></a>Lásd még

Az összes szükséges érték beszerzéséhez tekintse meg a következőt: [hozzáférés Azure Media Services API](./access-api-howto.md)-hoz.

## <a name="next-steps"></a>Következő lépések

* [Kapcsolódás Media Services Javával](configure-connect-java-howto.md)
* [Kapcsolódás Media Services a .NET-tel](configure-connect-dotnet-howto.md)
* [Kapcsolódás Media Serviceshoz Node.js](configure-connect-nodejs-howto.md)
* [Kapcsolódás Media Services a Python használatával](configure-connect-python-howto.md)