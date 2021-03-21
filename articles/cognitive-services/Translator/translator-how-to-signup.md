---
title: Fordítói erőforrás létrehozása
titleSuffix: Azure Cognitive Services
description: Ez a cikk bemutatja, hogyan hozhat létre Azure Cognitive Services Translator-erőforrást, és hogyan szerezhet be előfizetési kulcsot és végponti URL-címet.
services: cognitive-services
author: laujan
ms.author: lajanuar
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: how-to
ms.date: 02/16/2021
ms.openlocfilehash: a0d8532d19aff41bc5e7defb3b58462e81018749
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101712929"
---
# <a name="create-a-translator-resource"></a>Fordítói erőforrás létrehozása

Ebből a cikkből megtudhatja, hogyan hozhat létre fordítói erőforrásokat a Azure Portalban. Az [Azure Translator](translator-info-overview.md) egy felhőalapú gépi fordítási szolgáltatás, amely az [Azure Cognitive Services](../what-are-cognitive-services.md) REST API-k részét képezi. Az Azure-erőforrások az Ön által létrehozott szolgáltatások példányai. Az Azure-szolgáltatásoknak küldött összes API-kéréshez szükség van egy **végpont** URL-címére és egy írásvédett **előfizetési kulcsra** a hozzáférés hitelesítéséhez.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként aktív [**Azure-fiókra**](https://azure.microsoft.com/free/cognitive-services/)lesz szüksége.  Ha még nem rendelkezik ilyennel, [**létrehozhat egy ingyenes 12 hónapos előfizetést**](https://azure.microsoft.com/free/).

## <a name="translator-resource-types"></a>Fordítói erőforrástípusok

A Translator szolgáltatás két különböző típusú erőforráson keresztül érhető el:

* Az **egyszolgáltatásos** erőforrástípusok lehetővé teszik a hozzáférést egyetlen szolgáltatás API-kulcshoz és végponthoz.  

* A **több szolgáltatásból** álló erőforrástípusok lehetővé teszik több Cognitive Services elérését egyetlen API-kulcs és végpont használatával. A Cognitive Services erőforrás jelenleg a következő szolgáltatásokhoz érhető el:
  * Nyelv ([Translator](../translator/translator-info-overview.md), [Language Understanding (LUIS)](../luis/what-is-luis.md), [text Analytics](../text-analytics/overview.md))  
  * Vízió ([Computer Vision](../computer-vision/overview.md)), ([Face](../face/overview.md))  
  * Döntés ([Content moderator](../content-moderator/overview.md))  

## <a name="create-your-resource"></a>Az erőforrás létrehozása

* A projekt részleteinek befejezéséhez navigáljon közvetlenül a [**fordító létrehozása**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation) lapra a Azure Portal.

* A projekt részleteinek befejezéséhez navigáljon közvetlenül a Azure Portal [**létrehozás Cognitive Services**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne) lapjára.

>[!TIP]
>Ha szeretné, az Azure Portal kezdőlapján elkezdheti a **Létrehozás** folyamatát a következőképpen:
>
> 1. Navigáljon az [**Azure Portal**](https://ms.portal.azure.com/#home) kezdőlapjára.
> 1. Válassza ki ➕**erőforrás létrehozása**  az Azure-szolgáltatások menüjéből.
>1. A **Keresés a piactéren** mezőbe írja be, majd válassza a **Translator** (egykiszolgálós erőforrás) vagy a **Cognitive Services** (több szolgáltatásból álló erőforrás) lehetőséget.  *Lásd* [a fenti erőforrástípus kiválasztása](#create-your-resource)című témakört.
> 1. Válassza a **Létrehozás** lehetőséget, és a Project Details (projekt részletei) lapra kerül.
><br/><br/>

## <a name="complete-your-project-and-instance-details"></a>Fejezze be a projekt és a példány részleteit

1. **Előfizetés**. Válassza ki az egyik elérhető Azure-előfizetést.

1. **Erőforráscsoport**. A kiválasztott Azure-erőforráscsoport az új erőforráshoz tartozó virtuális tárolóként szolgál. Létrehozhat egy új erőforráscsoportot, vagy hozzáadhatja az erőforrást egy korábban már meglévő erőforráscsoporthoz, amely ugyanazokat az életciklusokat, engedélyeket és házirendeket is megosztja.

1. **Erőforrás-régió**. Válassza a **globális** lehetőséget, ha vállalatának vagy alkalmazásának egy adott régióra van szüksége. A Translator egy nem regionális szolgáltatás, amely egy adott Azure-régiótól függ. *Lásd:* [régiók és Availability Zones az Azure-ban](../../availability-zones/az-overview.md).

1. **Név**. Adja meg az erőforráshoz választott nevet. A választott névnek egyedinek kell lennie az Azure-on belül.

> [!NOTE]
> Ha olyan fordítói funkciót használ, amely egyéni tartományi végpontot igényel, a név mezőben megadott érték lesz a végponthoz tartozó egyéni tartománynév paraméter.

5. **Tarifacsomag**. Válasszon egy [díjszabási szintet](https://azure.microsoft.com/pricing/details/cognitive-services/translator) , amely megfelel az igényeinek:

   * Az egyes előfizetések ingyenes szintűek.
   * Az ingyenes csomag ugyanazokkal a szolgáltatásokkal és funkciókkal rendelkezik, mint a fizetős csomagok, és nem jár le.
   * Fiók esetében csak egy ingyenes előfizetés adható meg.</li></ul>

1. Ha több szolgáltatást tartalmazó erőforrást hozott létre, akkor a jelölőnégyzetek segítségével meg kell erősítenie a további használati adatokat.

1. Válassza a **Felülvizsgálat és létrehozás** lehetőséget.

1. Tekintse át a szolgáltatási feltételeket, és válassza a **Létrehozás** lehetőséget az erőforrás telepítéséhez.

1. Az erőforrás sikeres üzembe helyezését követően válassza az **Ugrás erőforráshoz** lehetőséget.

### <a name="authentication-keys-and-endpoint-url"></a>Hitelesítési kulcsok és végpont URL-címe

Az összes Cognitive Services API-kérelemhez szükség van egy végpont URL-címére és egy írásvédett kulcsra a hitelesítéshez.

* **Hitelesítési kulcsok**. A kulcs egy egyedi karakterlánc, amelyet a rendszer a fordítási szolgáltatásnak küldött minden kérelem esetében átad. A kulcsot lekérdezési karakterlánc paraméterrel vagy a HTTP-kérelem fejlécében megadott értékkel adhatja át.

* **Végpont URL-címe** Az API-kérelemben használja a globális végpontot, kivéve, ha egy adott Azure-régióra van szüksége. *Lásd:* [alap URL-címek](reference/v3-0-reference.md#base-urls). A globális végpont URL-címe: `api.cognitive.microsofttranslator.com` .

## <a name="get-your-authentication-keys-and-endpoint"></a>Hitelesítő kulcsok és végpont beszerzése

1. Az új erőforrás üzembe helyezése után válassza az **erőforrás keresése** lehetőséget, vagy navigáljon közvetlenül az erőforrás oldalára.
1. A bal oldali sín erőforrás- *kezelés* területén válassza a **kulcsok és végpont** lehetőséget.
1. Másolja és illessze be az előfizetési kulcsokat és a végponti URL-címet egy kényelmes helyen, például a *Microsoft Jegyzettömbben*.

:::image type="content" source="../media/cognitive-services-apis-create-account/get-cog-serv-keys.png" alt-text="Kulcs és végpont beolvasása.":::

## <a name="how-to-delete-a--resource-or-resource-group"></a>Erőforrás vagy erőforráscsoport törlése

> [!Warning]
> Egy erőforráscsoport törlése a csoportban található összes erőforrást is törli.

Cognitive Services vagy Translator erőforrás eltávolításához **törölheti az erőforrást** , vagy **törölheti az erőforráscsoportot**.

Az erőforrás törlése:

1. Navigáljon az erőforráscsoporthoz a Azure Portal.
1. Jelölje ki a törölni kívánt erőforrásokat a szomszédos jelölőnégyzet bejelölésével.
1. A felső menüben válassza a **Törlés** lehetőséget a jobb szél közelében.
1. A **törölt erőforrások** párbeszédpanelen írja be az *Igen* értéket.
1. Válassza a **Törlés** elemet.

Az erőforráscsoport törlése:

1. Navigáljon az erőforráscsoporthoz a Azure Portal.
1. Válassza az **erőforráscsoport törlése** elemet a felső menüsorban a képernyő bal szélének közelében.
1. Erősítse meg a törlési kérést az erőforráscsoport nevének megadásával, majd a **Törlés** lehetőség kiválasztásával.

## <a name="how-to-get-started-with-translator"></a>Ismerkedés a Translator használatával

A rövid útmutatóból megtudhatja, hogyan használhatja a Translator szolgáltatást REST API-kkal.

> [!div class="nextstepaction"]
> [Ismerkedés a Translatorrel](quickstart-translator.md)

## <a name="more-resources"></a>További erőforrások

* [Microsoft Translator Code-minták](https://github.com/MicrosoftTranslator).  Többnyelvű fordítói kódok a GitHubon érhetők el.
* [Microsoft Translator támogatási fórum](https://www.aka.ms/TranslatorForum)
* [Ismerkedés az Azure-ban (3 perces videó)](https://azure.microsoft.com/get-started/?b=16.24)