---
title: Alkalmazáshitelesítési kód írása
titleSuffix: Azure Digital Twins
description: Hitelesítési kód írása ügyfélalkalmazásban
author: baanders
ms.author: baanders
ms.date: 10/7/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 958b0de97b79b447f2570dd9c57c87f380bcd551
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589344"
---
# <a name="write-client-app-authentication-code"></a>Ügyfélalkalmazás hitelesítési kód írása

Miután beállított [egy](how-to-set-up-instance-portal.md)Azure Digital Twins és hitelesítést, létrehozhat egy ügyfélalkalmazást, amely a példányhoz használható. Miután beállított egy kezdő ügyfélprojektet, kódot  kell írnia az ügyfélalkalmazásba, hogy hitelesítse azt a Azure Digital Twins példányban.

Azure Digital Twins Azure AD biztonsági jogkivonatok használatával végez hitelesítést [az OAUTH 2.0 alapján.](../active-directory/develop/security-tokens.md#json-web-tokens-and-claims) Az SDK hitelesítéséhez be kell szereznie egy, az sdk-hoz megfelelő engedélyekkel rendelkező, Azure Digital Twins jogkivonatot, és át kell adnia az API-hívásokkal együtt. 

Ez a cikk azt ismerteti, hogyan szerezheti be a hitelesítő adatokat az `Azure.Identity` ügyféloldali kódtár használatával. Bár ez a cikk C#-ban mutat be kódpécsereket, például hogy mit írna a [.NET (C#)](/dotnet/api/overview/azure/digitaltwins/client)SDK-hoz, a verzióját attól függetlenül használhatja, hogy milyen SDK-t használ (az Azure Digital Twins-hez elérhető SDK-król bővebben `Azure.Identity` [*lásd: How-to: Use the Azure Digital Twins APIs and SDKs*](how-to-use-apis-sdks.md)(Az Azure Digital Twins API-k és SDK-k használata).

## <a name="prerequisites"></a>Előfeltételek

Először is kövesse az [*How-to: Set up an instance and authentication (How-to: Set up an instance and authentication) (How-to: Set up an instance and authentication ) (How-to: Set up an instance and authentication*](how-to-set-up-instance-portal.md)) (A példány és a hitelesítés Ez biztosítja, hogy rendelkezik egy Azure Digital Twins-példánygal, és hogy a felhasználó rendelkezik-e hozzáférési engedélyekkel. A beállítás után készen áll az ügyfélalkalmazás kód írásához.

A folytatáshoz szüksége lesz egy ügyfélalkalmazás-projektre, amelyben megírja a kódot. Ha még nem rendelkezik beállított ügyfélalkalmazás-projekttel, hozzon létre egy alapszintű projektet az oktatóanyagban használni tervezett nyelven.

## <a name="common-authentication-methods-with-azureidentity"></a>Gyakori hitelesítési módszerek az Azure.Identity használatával

`Azure.Identity` A egy ügyféloldali kódtár, amely számos hitelesítőadat-lekért módszert biztosít, amelyek segítségével beszerezheti a jogkivonatot, és hitelesítheti magát az SDK-val. Bár ez a cikk C# nyelven mutat be példákat, számos nyelvet `Azure.Identity` megtekinthet, például...

* [.NET (C#)](/dotnet/api/azure.identity)
* [Java](/java/api/overview/azure/identity-readme)
* [JavaScript](/javascript/api/overview/azure/identity-readme)
* [Python](/python/api/overview/azure/identity-readme)

A három gyakori hitelesítőadat-lekért metódusa `Azure.Identity` a következő:

* [A DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) alapértelmezett hitelesítési folyamatot biztosít az Azure-ban üzembe helyezett alkalmazásokhoz, és helyi fejlesztés esetén `TokenCredential` ez az ajánlott **választás.** Az is engedélyezhető, hogy kipróbálja a cikkben javasolt másik két módszert; A burkot, `ManagedIdentityCredential` és egy `InteractiveBrowserCredential` konfigurációs változóval fér hozzá.
* [A ManagedIdentityCredential](/dotnet/api/azure.identity.managedidentitycredential) nagyszerűen működik olyan esetekben, ahol felügyelt [identitásokra (MSI)](../active-directory/managed-identities-azure-resources/overview.md)van szükség, és jó választás a Azure Functions és az Azure-szolgáltatásokban való üzembe helyezéséhez.
* [Az InteractiveBrowserCredential](/dotnet/api/azure.identity.interactivebrowsercredential) interaktív alkalmazásokhoz készült, és hitelesített SDK-ügyfél létrehozására használható

Az alábbi példa bemutatja, hogyan használhatók ezek a .NET (C#) SDK-val.

## <a name="authentication-examples-net-c-sdk"></a>Hitelesítési példák: .NET (C#) SDK

Ez a szakasz C# használatával mutat be egy példát a megadott .NET SDK hitelesítési kód írásához.

Először is foglalja bele az SDK-csomagot `Azure.DigitalTwins.Core` és a csomagot a `Azure.Identity` projektbe. A választott eszközöktől függően a csomagokat a Visual Studio csomagkezelővel vagy a parancssori `dotnet` eszközzel is használhatja. 

Emellett hozzá kell adni a következő using utasításokat a projektkódhoz:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/authentication.cs" id="Azure_Digital_Twins_dependencies":::

Ezután adja hozzá a hitelesítő adatok beszerzéséhez szükséges kódot a metódusai `Azure.Identity` egyikével.

### <a name="defaultazurecredential-method"></a>DefaultAzureCredential metódus

[A DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) alapértelmezett hitelesítési folyamatot biztosít az Azure-ban üzembe helyezett alkalmazásokhoz, és helyi fejlesztés esetén `TokenCredential` ez az ajánlott **választás.**

Az alapértelmezett Azure-beli hitelesítő adatok megadásához szüksége lesz a Azure Digital Twins-példány URL-címére[(utasításokat a kereséséhez).](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)

Itt egy kódminta, amely egy kódot ad hozzá a `DefaultAzureCredential` projekthez:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/authentication.cs" id="DefaultAzureCredential_full":::

#### <a name="set-up-local-azure-credentials"></a>Helyi Azure-beli hitelesítő adatok beállítása

[!INCLUDE [Azure Digital Twins: local credentials prereq (inner)](../../includes/digital-twins-local-credentials-inner.md)]

### <a name="managedidentitycredential-method"></a>ManagedIdentityCredential metódus

A [ManagedIdentityCredential](/dotnet/api/azure.identity.managedidentitycredential) metódus nagyszerűen működik olyan esetekben, amikor felügyelt identitásokra [(MSI)](../active-directory/managed-identities-azure-resources/overview.md)van szükség – például a felügyelt identitásokkal Azure Functions.

Ez azt jelenti, hogy a vagy a projektben a használatával `ManagedIdentityCredential` `DefaultAzureCredential` `InteractiveBrowserCredential` hitelesítheti a projekt egy másik részét.

Az alapértelmezett Azure-beli hitelesítő adatok megadásához szüksége lesz a Azure Digital Twins-példány URL-címére[(a kereséséhez szükséges utasítások).](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)

Egy Azure-függvényben a következő felügyelt identitás hitelesítő adatait használhatja:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/authentication.cs" id="ManagedIdentityCredential":::

### <a name="interactivebrowsercredential-method"></a>InteractiveBrowserCredential metódus

Az [InteractiveBrowserCredential metódus](/dotnet/api/azure.identity.interactivebrowsercredential) interaktív alkalmazásokhoz használható, és egy webböngészőt hoz létre a hitelesítéshez. Ezt használhatja ahelyett, hogy `DefaultAzureCredential` interaktív hitelesítésre lenne szüksége.

Az interaktív böngésző hitelesítő adatainak megadásához  olyan alkalmazásregisztrációra lesz szüksége, amely rendelkezik engedélyekkel a Azure Digital Twins API-khoz. Az alkalmazásregisztráció beállításának lépéseiért lásd: Alkalmazásregisztráció [*létrehozása.*](how-to-create-app-registration.md) Az alkalmazásregisztráció beállítása után...
* az alkalmazásregisztráció *alkalmazás- (ügyfél-) azonosítója* ([utasítások a kereséshez)](how-to-create-app-registration.md#collect-client-id-and-tenant-id)
* az alkalmazásregisztráció *címtár- (bérlő-) azonosítója* ([utasítások a kereséshez)](how-to-create-app-registration.md#collect-client-id-and-tenant-id)
* Azure Digital Twins példány URL-címe ([utasítások a kereséshez)](how-to-set-up-instance-portal.md#verify-success-and-collect-important-values)

Az alábbi példa egy hitelesített SDK-ügyfél használatával való létrehozására használt kódot mutat `InteractiveBrowserCredential` be.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/authentication.cs" id="InteractiveBrowserCredential":::

>[!NOTE]
> Bár az ügyfél-azonosítót, a bérlőazonosítót és a példány URL-címét közvetlenül a kódba is helyezze a fenti módon, jó ötlet, ha a kód egy konfigurációs fájlból vagy környezeti változóból szerezze be ezeket az értékeket.

#### <a name="other-notes-about-authenticating-azure-functions"></a>Egyéb megjegyzések a hitelesítő Azure Functions

Az [*útmutató: Azure-függvény*](how-to-create-azure-function.md) beállítása adatok feldolgozásához témakörben egy teljesebb példát láthat, amely ismerteti a függvények kontextusában elérhető néhány fontos konfigurációs lehetőséget.

Ha hitelesítést használ egy függvényben, ne felejtse el a következőt:
* [Felügyelt identitás engedélyezése](../app-service/overview-managed-identity.md?tabs=dotnet)
* Környezeti [változók szükség szerint](/sandbox/functions-recipes/environment-variables?tabs=csharp) való használata
* Rendeljen engedélyeket a függvényalkalmazáshoz, amely lehetővé teszi a hozzáférést Digital Twins API-khoz. További információ a Azure Functions folyamatokkal kapcsolatban: [*How-to: Set up an Azure function for processing data (Azure-függvény beállítása adatok feldolgozásához).*](how-to-create-azure-function.md)

## <a name="authenticate-across-tenants"></a>Hitelesítés bérlőkön keresztül

Azure Digital Twins egy olyan szolgáltatás, amely csak egy  [Azure Active Directory-bérlőt (Azure AD-bérlőt)](../active-directory/develop/quickstart-create-new-tenant.md)támogat: a fő bérlőt abból az előfizetésből, amelyben a Azure Digital Twins példány található.

[!INCLUDE [digital-twins-tenant-limitation](../../includes/digital-twins-tenant-limitation.md)]

Ha a Azure Digital Twins-példányhoz olyan szolgáltatásnévvel vagy felhasználói fiókkal kell hozzáférnie, amely a példánytól eltérő bérlőhöz tartozik,  akkor az egyes összevont identitások egy másik bérlőtől kérhetnek jogkivonatot az Azure Digital Twins-példány "otthoni" bérlőjéhez. 

[!INCLUDE [digital-twins-tenant-solution-1](../../includes/digital-twins-tenant-solution-1.md)]

Az otthoni bérlőt a kód hitelesítőadat-beállításaiban is megadhatja. 

[!INCLUDE [digital-twins-tenant-solution-2](../../includes/digital-twins-tenant-solution-2.md)]

## <a name="other-credential-methods"></a>Egyéb hitelesítő módszerek

Ha a fenti kiemelt hitelesítési forgatókönyvek nem fedik le az alkalmazás igényeit, a Microsoft identitásplatformján elérhető egyéb hitelesítési típusokat [**is megismerheti.**](../active-directory/develop/v2-overview.md#getting-started) A platform dokumentációja további hitelesítési forgatókönyveket tartalmaz, alkalmazástípus szerint rendezve.

## <a name="next-steps"></a>Következő lépések

További információ a biztonságról a Azure Digital Twins:
* [*Alapfogalmak: Biztonsági Azure Digital Twins megoldások*](concepts-security.md)

Vagy most, hogy beállította a hitelesítést, lépjen tovább a modellek létrehozására és kezelésére a példányban:
* [*How-to: Manage DTDL models (DTDL-modellek kezelése)*](how-to-manage-model.md)