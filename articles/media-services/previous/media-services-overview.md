---
title: Az Azure Media Services áttekintése | Microsoft Docs
description: Microsoft Azure Media Services egy bővíthető, felhőalapú platform, amely lehetővé teszi a fejlesztők számára méretezhető médiafelügyeleti és -továbbítási alkalmazások létrehozását. Ez a cikk áttekintést nyújt a Azure Media Servicesról.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 6e68e53387aa50b99bab8ed4cdba7f1e97fc48c0
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2021
ms.locfileid: "103008233"
---
# <a name="azure-media-services-overview"></a>Az Azure Media Services áttekintése

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector" title1="Válassza ki a használt Media Services verzióját:"]
> * [3\. verzió](../latest/media-services-overview.md)
> * [2-es verzió](media-services-overview.md)

> [!NOTE]
> A Media Services v2 nem fog újabb funkciókkal bővülni. <br/>Tekintse meg a legújabb, [Media Services v3](../latest/index.yml)verziót. Lásd még: [az áttelepítési útmutató v2-től v3-ig](../latest/migrate-v-2-v-3-migration-introduction.md)

Microsoft Azure Media Services (AMS) egy bővíthető, felhőalapú platform, amely lehetővé teszi a fejlesztők számára méretezhető médiafelügyeleti és -továbbítási alkalmazások létrehozását. A Media Services alapjai a REST API-k, amelyek lehetővé teszik különböző videó- és audiotartalmak feltöltését, tárolását, kódolását és becsomagolását, majd igény szerinti és élő adatfolyamként történő továbbítását különböző ügyfelek részére (például tévékészülékekre, számítógépekre és mobileszközökre).

A Media Services használatával teljes, átfogó munkafolyamatokat hozhat létre. A munkafolyamatok bizonyos részeihez harmadik féltől származó összetevőket is szabadon felhasználhat. Például egy harmadik féltől származó kódolóval végezhet kódolást. Ezután a Media Services használatával intézheti a feltöltést, a védelmet, a csomagolást és a továbbítást. A tartalmakat továbbíthatja streamként, illetve az ügyfelek igénye szerint is. 


## <a name="compliance-privacy-and-security"></a>Megfelelőség, adatvédelem és biztonság

Fontos megjegyezni, hogy be kell tartania az összes vonatkozó törvényt a Azure Media Services használatára vonatkozóan, és Ön nem használhatja Media Services vagy bármely Azure-szolgáltatást olyan módon, amely sérti mások jogait, vagy amelyek károsak lehetnek mások számára.

A videók vagy rendszerképek Media Servicesba való feltöltése előtt minden megfelelő jogosultsággal kell rendelkeznie a videó/rendszerkép használatához, beleértve a törvény által előírt jogokat, a videóban/képben, az adatok felhasználásához, feldolgozásához és tárolásához szükséges összes hozzájárulást a Media Services és az Azure-ban. Bizonyos joghatóságok bizonyos adatkategóriák, például biometrikus adatok gyűjtésére, online feldolgozására és tárolására vonatkozó különleges jogi követelményeket állapíthatnak meg. Mielőtt a Media Services és az Azure-t használja a különleges jogi követelmények hatálya alá eső bármilyen adat feldolgozásához és tárolásához, meg kell győződnie arról, hogy megfelel az Ön számára esetlegesen felmerülő jogi követelményeknek.

A megfelelőség, az adatvédelem és a biztonság megismeréséhez Media Services látogasson el a Microsoft [adatvédelmi központba](https://www.microsoft.com/trust-center/?rtc=1). A Microsoft adatvédelmi kötelezettségei, az adatkezelési és adatmegőrzési eljárások, beleértve az adatok törlésének módját is, tekintse át a Microsoft [adatvédelmi nyilatkozatát](https://privacy.microsoft.com/PrivacyStatement), az [online szolgáltatások használati feltételeit](https://www.microsoft.com/licensing/product-licensing/products?rtc=1) ("Ost") és az [adatfeldolgozási kiegészítést](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=67) ("DPA"). Media Services használatával Ön vállalja, hogy az OST, a DPA és az adatvédelmi nyilatkozat köti.
 
## <a name="prerequisites"></a>Előfeltételek

Az Azure Media Services használatának megkezdéséhez rendelkeznie kell a következőkkel:

* Egy Azure-fiók. Ha nincs fiókja, néhány perc alatt létrehozhat egy ingyenes próbafiókot. További információkért lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com).
* Egy Azure Media Services-fiók. További információ: [Fiók létrehozása](media-services-portal-create-account.md)
* (Választható:) A fejlesztési környezet beállítása Válassza ki, hogy a .NET vagy a REST API fejlesztési környezetet kívánja-e használni. További információ: [Környezet beállítása](media-services-dotnet-how-to-use.md)

    Emellett megismerheti az [AMS API-hoz való programozott csatlakozást](media-services-use-aad-auth-to-access-ams-api.md) is.
* Elindított állapotú standard vagy prémium szintű streamvégpont.  További információ: [Streamvégpontok felügyelete](media-services-portal-manage-streaming-endpoints.md)

## <a name="sdks-and-tools"></a>SDK-k és eszközök

A Media Services-megoldások létrehozásához a következőket használhatja:

* [Media Services REST API](/rest/api/media/operations/azure-media-services-rest-api-reference)
* Az elérhető ügyféloldali SDK-k valamelyike:
    * A .NET-hez készült Azure Media Services SDK
    
        * [NuGet-csomag](https://www.nuget.org/packages/windowsazure.mediaservices/)
        * [GitHub forráskódja](https://github.com/Azure/azure-sdk-for-media-services)
    * [Javához készült Azure SDK](https://github.com/Azure/azure-sdk-for-java),
    * [Azure PHP SDK](https://github.com/Azure/azure-sdk-for-php),
    * [Node.js-hez készült Azure Media Services](https://github.com/michelle-becker/node-ams-sdk/blob/master/lib/request.js) (Ez a Node.js SDK nem Microsoft által készített verziója. Ennek a karbantartását egy közösség végzi, és jelenleg nem fedi le 100%-osan az AMS API-k tartalmát.)
* Meglévő eszközök:
    * [Azure Portal](https://portal.azure.com/)
    * [Azure-Media-Services-Explorer](https://github.com/Azure/Azure-Media-Services-Explorer) (Az Azure Media Services Explorer (AMSE) egy Winforms/C#-alkalmazás Windows rendszerre)

> [!NOTE]
> A Java SDK legújabb verziójának beszerzéséhez és a Java-fejlesztés megkezdéséhez tekintse meg [Az Azure Media Services Java ügyfél-SDK használatának megkezdése](./media-services-java-how-to-use.md) című cikket. <br/>
> A legfrissebb Media Services PHP SDK letöltéséhez keresse meg a Microsoft Azure-csomag 0.5.7-es verzióját a [Packagist-adattárban](https://packagist.org/packages/microsoft/windowsazure#v0.5.7).  

## <a name="code-samples"></a>Kódminták

Az **Azure-kódminták** katalógusban számos kódmintát talál: [Azure Media Services-kódminták](https://azure.microsoft.com/resources/samples/?service=media-services&sort=0).

## <a name="concepts"></a>Alapelvek

Az Azure Media Services alapfogalmaiért lásd: [Fogalmak](media-services-concepts.md)

## <a name="supported-scenarios-and-availability-of-media-services-across-data-centers"></a>Támogatott helyzetek és a Media Services rendelkezésre állása az egyes adatközpontokban

További információ az Azure gyakori forgatókönyvekről: [AMS-forgatókönyvek](scenarios-and-availability.md).
További információ a regionális elérhetőségről: a [Media Service elérhetősége](availability-regions-v-2.md).

## <a name="service-level-agreement-sla"></a>Szolgáltatói szerződés (SLA)

További információ: [Microsoft Azure SLA](https://azure.microsoft.com/support/legal/sla/)

## <a name="support"></a>Támogatás

[Az Azure-támogatási szolgáltatások](https://azure.microsoft.com/support/options/) támogatási lehetőségeket biztosítanak az Azure szolgáltatásaival, köztük a Media Services szolgáltatással kapcsolatban.

## <a name="provide-feedback"></a>Visszajelzés küldése

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
