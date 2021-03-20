---
title: Azure Service Bus – áttelepítés közös hozzáférésű aláírás engedélyezésére
description: Tudnivalók a Azure Active Directory Access Control Service áttelepítéséről a közös hozzáférésű aláírás engedélyezésére.
ms.topic: article
ms.date: 06/23/2020
ms.openlocfilehash: e8cd12ac97020417f9958beded1fd198dd485fff
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "88064621"
---
# <a name="service-bus---migrate-from-azure-active-directory-access-control-service-to-shared-access-signature-authorization"></a>Service Bus – Migrálás Azure Active Directory Access Control Serviceról a közös hozzáférésű aláírás engedélyezésére

Service Bus az alkalmazások korábban két különböző engedélyezési modell közül választhattak: a közvetlenül a Service Bus által biztosított [közös hozzáférésű aláírási (SAS)](service-bus-sas.md) jogkivonat-modell, valamint egy összevont modell, amelyben az engedélyezési szabályok kezelését a [Azure Active Directory](../active-directory/index.yml) Access Control Service (ACS) végzi, és a rendszer átadja az ACS-től kapott jogkivonatokat a kívánt funkciókhoz való hozzáférés engedélyezéséhez Service Bus számára.

Az ACS engedélyezési modelljét az [sas-hitelesítés](service-bus-authentication-and-authorization.md) az előnyben részesített modellként váltotta fel, és az összes dokumentáció, útmutató és minta kizárólag az SAS-t használja. Emellett már nem lehet új Service Bus-névtereket létrehozni, amelyek az ACS-vel párosítva vannak.

Az SAS azzal az előnnyel jár, hogy az nem függ azonnal egy másik szolgáltatástól, de közvetlenül egy ügyfélről is használhatja, ha az ügyfél hozzáférést ad az SAS-szabály nevének és a szabály kulcsának. Az SAS könnyen integrálható egy olyan megközelítéssel, amelyben az ügyfélnek először át kell adnia egy engedélyezési vizsgálatot egy másik szolgáltatással, majd ki kell állítani egy jogkivonatot. Az utóbbi módszer hasonló az ACS használati mintához, de lehetővé teszi a hozzáférési jogkivonatok kiállítását olyan alkalmazásspecifikus feltételek alapján, amelyek nehezen használhatók az ACS-ben.

Az ACS-től függő összes meglévő alkalmazás esetében arra buzdítjuk az ügyfeleket, hogy az alkalmazásokat az SAS-re támaszkodva használják.

## <a name="migration-scenarios"></a>Migrálási forgatókönyvek

Az ACS és a Service Bus az *aláírási kulcs* megosztott ismerete révén integrálhatók. Az aláíró kulcsot egy ACS-névtér használja az engedélyezési jogkivonatok aláírásához, és a Service Bus használja annak ellenőrzéséhez, hogy a tokent a párosított ACS-névtér adta-e ki. Az ACS-névtér a szolgáltatás identitásait és engedélyezési szabályait tartalmazza. Az engedélyezési szabályok határozzák meg, hogy melyik szolgáltatás-identitás vagy a külső Identitáskezelő által kiállított jogkivonat milyen típusú hozzáférést kap a Service Bus névtér-gráf egy részéhez, egy leghosszabb előtagú egyezés formájában.

Egy ACS-szabály például megadhatja a **küldési** jogcímet az elérési út előtagjaként `/` egy szolgáltatás identitásának, ami azt jelenti, hogy az ACS által az adott szabály alapján kiadott jogkivonat megadja az ügyfél jogosultságait a névtérben lévő összes entitásnak való küldéshez. Ha az elérési út előtagja `/abc` , az identitás csak az `abc` előtagja alatt elnevezett vagy az előtag alá rendezett entitások küldésére korlátozódik. Feltételezzük, hogy az áttelepítési útmutató olvasói már ismerik ezeket a fogalmakat.

Az áttelepítési forgatókönyvek három széles kategóriába sorolhatók:

1.  **Változatlan alapértékek**. Néhány ügyfél egy [SharedSecretTokenProvider](/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider) objektumot használ, átadja az automatikusan generált **tulajdonosi** szolgáltatás identitását és annak titkos kulcsát az ACS-névtérhez, párosítva a Service Bus névtérrel, és ne vegyen fel új szabályokat.

2.  **Egyéni szolgáltatás-identitások egyszerű szabályokkal**. Egyes ügyfelek új szolgáltatás-identitásokat adnak hozzá, és minden új szolgáltatás identitásának **küldési**, **figyelési** és **kezelési** engedélyeit biztosítják egy adott entitáshoz.

3.  **Egyéni szolgáltatásbeli identitások összetett szabályokkal**. Nagyon kevés ügyfél rendelkezik olyan összetett szabályokkal, amelyekben a külsőleg kiállított jogkivonatok a továbbítási jogosultságokra vannak leképezve, vagy ha egy szolgáltatás identitása több szabályon keresztül különböző névtérbeli elérési utakon eltérő jogokat kap.

Az összetett szabálykészlet áttelepítésével kapcsolatos segítségért forduljon az [Azure támogatási szolgálatához](https://azure.microsoft.com/support/options/). A másik két forgatókönyv egyszerű áttelepítést tesz lehetővé.

### <a name="unchanged-defaults"></a>Változatlan alapértékek

Ha az alkalmazás nem módosította az ACS alapértelmezett értékeit, lecserélheti az összes [SharedSecretTokenProvider](/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider) -használatot egy [SharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) objektumra, és az ACS- **tulajdonos** fiók helyett az előre konfigurált **RootManageSharedAccessKey** is használhatja. Vegye figyelembe, hogy még az ACS **tulajdonosi** fiókjával is, ez a konfiguráció (és még mindig) nem ajánlott, mert ez a fiók/szabály teljes körű felügyeleti szolgáltatót biztosít a névtérben, beleértve az entitások törlésére vonatkozó engedélyeket is.

### <a name="simple-rules"></a>Egyszerű szabályok

Ha az alkalmazás egyéni szolgáltatásbeli identitásokat használ egyszerű szabályokkal, az áttelepítés egyértelmű abban az esetben, ha egy ACS-szolgáltatás identitását hozták létre egy adott várólista hozzáférés-vezérlésének biztosításához. Ez a forgatókönyv gyakran előfordul olyan SaaS-stílusú megoldásokban, ahol az egyes várólisták a bérlői hely vagy a fiókirodák számára hidat képeznek, és az adott helyhez létrejön a szolgáltatás identitása. Ebben az esetben a megfelelő szolgáltatási identitás áttelepíthető egy megosztott hozzáférési aláírási szabályba, közvetlenül a várólistán. A szolgáltatás identitásának neve lehet az SAS-szabály neve, a szolgáltatás identitásának kulcsa pedig az SAS-szabály kulcsa. Az SAS-szabály jogosultságait a rendszer az entitáshoz tartozó, illetve a vonatkozó ACS-szabályokkal egyenértékűként konfigurálja.

Ezt az új és további konfigurációt is elvégezheti a SAS-ben az ACS-vel összevont meglévő névtérben, és az ACS-ről való áttelepítést a [SharedSecretTokenProvider](/dotnet/api/microsoft.servicebus.sharedsecrettokenprovider)helyett a [SharedAccessSignatureTokenProvider](/dotnet/api/microsoft.servicebus.sharedaccesssignaturetokenprovider) használatával végezheti el. A névtér nem szükséges az ACS-ből való leválasztáshoz.

### <a name="complex-rules"></a>Összetett szabályok

Az SAS-szabályok nem jelentenek fióknak, de a jogosultságokhoz társított aláíró kulcsok névvel rendelkeznek. Ilyenek például azok a forgatókönyvek, amelyekben az alkalmazás számos szolgáltatásbeli identitást hoz létre, és hozzáférést biztosít számukra a különböző entitások számára, vagy a teljes névtér továbbra is jogkivonatot kiállító közvetítőt igényel. Ehhez [forduljon a támogatási szolgálathoz](https://azure.microsoft.com/support/options/), és kérjen útmutatást ehhez a közvetítőhöz.

## <a name="next-steps"></a>Következő lépések

A Service Bus hitelesítéssel kapcsolatos további tudnivalókért tekintse meg a következő témaköröket:

* [Service Bus-hitelesítés és -engedélyezés](service-bus-authentication-and-authorization.md)
* [Service Bus hitelesítés közös hozzáférési aláírásokkal](service-bus-sas.md)