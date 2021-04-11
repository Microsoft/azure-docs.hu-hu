---
title: Az Object (blob) tároló használata iOS-ről – Azure | Microsoft Docs
description: Store unstructured data in the cloud with Azure Blob storage (object storage) (Strukturálatlan adatok tárolása a felhőben Azure Blob Storage-fiókkal (objektumtároló)).
author: twooley
ms.author: twooley
ms.date: 11/20/2018
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.openlocfilehash: 1b0653c997930c60950da335e79b0388edd43897
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2021
ms.locfileid: "106278354"
---
# <a name="how-to-use-blob-storage-from-ios"></a>BLOB Storage használata az iOS-ből

Ez a cikk bemutatja, hogyan végezheti el a gyakori forgatókönyveket Microsoft Azure Blob Storage használatával. A minták a Objective-C nyelven íródtak, és az [Azure Storage ügyféloldali kódtárat](https://github.com/Azure/azure-storage-ios)használják az iOS-hez. A tárgyalt forgatókönyvek a Blobok feltöltését, listázását, letöltését és törlését tartalmazzák. A Blobokkal kapcsolatos további információkért tekintse meg a [következő lépések](#next-steps) című szakaszt. Letöltheti a [minta alkalmazást](https://github.com/Azure/azure-storage-ios/tree/master/BlobSample) is, hogy gyorsan láthassa az Azure Storage használatát egy IOS-alkalmazásban.

További információ a blob Storage-ról: [Az Azure Blob Storage bemutatása](storage-blobs-introduction.md).

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="import-the-azure-storage-ios-library-into-your-application"></a>Az Azure Storage iOS-függvénytár importálása az alkalmazásba

Az Azure Storage iOS-függvénytárát az [Azure Storage CocoaPod](https://cocoapods.org/pods/AZSClient) vagy a **keretrendszer** fájljának importálásával importálhatja az alkalmazásba. A CocoaPod az ajánlott módszer, mivel megkönnyíti a könyvtár integrálását, azonban a keretrendszer fájlból való importálás kevésbé zavaró a meglévő projekthez.

A könyvtár használatához a következőkre lesz szüksége:

- iOS 8 +
- Xcode 7 +

## <a name="cocoapod"></a>CocoaPod

1. Ha még nem tette meg, [telepítse a CocoaPods](https://guides.cocoapods.org/using/getting-started.html#toc_3) a számítógépre a terminálablak megnyitásával és a következő parancs futtatásával

    ```shell
    sudo gem install cocoapods
    ```

2. Ezután a projekt könyvtárában (a. xcodeproj fájlt tartalmazó könyvtárban) hozzon létre egy új, _cocoapods_ nevű fájlt (nincs fájlkiterjesztés). Adja hozzá a következőt a _cocoapods_ és a mentéshez.

    ```ruby
    platform :ios, '8.0'

    target 'TargetName' do
      pod 'AZSClient'
    end
    ```

3. A terminál ablakban navigáljon a projekt könyvtárába, és futtassa a következő parancsot:

    ```shell
    pod install
    ```

4. Ha a. xcodeproj meg van nyitva a Xcode-ben, akkor zárjuk le. A projekt könyvtárában nyissa meg az újonnan létrehozott projektfájlt, amely a. xcworkspace kiterjesztéssel fog rendelkezni. Ez az a fájl, amelyről most fog dolgozni.

## <a name="framework"></a>Keretrendszer

A könyvtár használatának másik módja a keretrendszer manuális felépítése:

1. Először töltse le vagy klónozott [Azure-Storage-iOS](https://github.com/azure/azure-storage-ios)-tárházat.
2. Lépjen be az *Azure-Storage-iOS*  ->  *lib*  ->  *Azure Storage ügyféloldali kódtáraba*, és nyissa meg `AZSClient.xcodeproj` a Xcode.
3. A Xcode bal felső részén módosítsa az aktív sémát az "Azure Storage ügyféloldali kódtár" elemről a "Framework" értékre.
4. Hozza létre a projektet (⌘ + B). Ekkor létrejön egy `AZSClient.framework` fájl az asztalon.

Ezután importálhatja a keretrendszer fájlját az alkalmazásba a következő módon:

1. Hozzon létre egy új projektet, vagy nyissa meg a meglévő projektet a Xcode-ben.
2. Húzza a t a `AZSClient.framework` Xcode Project-Navigátorba.
3. Szükség esetén válassza az *elemek másolása* lehetőséget, majd kattintson a *Befejezés* gombra.
4. Kattintson a projektre a bal oldali navigációs sávon, és kattintson a projekt-szerkesztő tetején található *általános* fülre.
5. A *csatolt keretrendszerek és kódtárak* szakaszban kattintson a Hozzáadás gombra (+).
6. A már megadott könyvtárak listájában keresse meg `libxml2.2.tbd` és adja hozzá a projekthez.

## <a name="import-the-library"></a>A könyvtár importálása

```objc
// Include the following import statement to use blob APIs.
#import <AZSClient/AZSClient.h>
```

Ha Swift-t használ, létre kell hoznia egy áthidaló fejlécet, és ott kell importálnia \<AZSClient/AZSClient.h> :

1. Hozzon létre egy header fájlt `Bridging-Header.h` , és adja hozzá a fenti importálási utasítást.
2. Lépjen a *létrehozási beállítások* lapra, és keressen rá az *Objective-C áthidaló fejlécre*.
3. Kattintson duplán a *Objective-C áthidaló fejléc* mezőjére, és adja hozzá a fejléc elérési útját: `ProjectName/Bridging-Header.h`
4. Hozza létre a projektet (⌘ + B) annak ellenőrzéséhez, hogy az áthidaló fejlécet a Xcode választotta-e ki.
5. A könyvtár közvetlen használata bármely Swift-fájlban, nincs szükség importálási utasításokra.

[!INCLUDE [storage-mobile-authentication-guidance](../../../includes/storage-mobile-authentication-guidance.md)]

## <a name="asynchronous-operations"></a>Aszinkron műveletek

> [!NOTE]
> A szolgáltatással kapcsolatos kérelmeket végrehajtó összes módszer aszinkron művelet. A kód példákban azt tapasztalhatja, hogy ezek a metódusok befejező kezelővel rendelkeznek. A befejezési kezelőn belüli kód a kérelem befejeződése **után** fog futni. A rendszer a befejezési **kezelő futtatása után** futtatja a kérést.

## <a name="create-a-container"></a>Tároló létrehozása

Az Azure Storage-ban minden blobnak egy tárolóban kell lennie. Az alábbi példa bemutatja, hogyan hozhat létre egy *newcontainer* nevű tárolót a Storage-fiókban, ha még nem létezik. A tároló nevének kiválasztásakor szem előtt kell lennie a fent említett elnevezési szabályoknak.

```objc
-(void)createContainer{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"newcontainer"];

    // Create container in your Storage account if the container doesn't already exist
    [blobContainer createContainerIfNotExistsWithCompletionHandler:^(NSError *error, BOOL exists) {
        if (error){
            NSLog(@"Error in creating container.");
        }
    }];
}
```

Ennek ellenőrzéséhez tekintse meg a [Microsoft Azure Storage Explorer](https://storageexplorer.com) , és ellenőrizze, hogy a *newcontainer* szerepel-e a Storage-fiókhoz tartozó tárolók listáján.

## <a name="set-container-permissions"></a>Tároló engedélyeinek beállítása

A tároló engedélyei alapértelmezés szerint a **magánhálózati** hozzáférésre vannak konfigurálva. A tárolók azonban néhány különböző lehetőséget biztosítanak a tárolók eléréséhez:

- **Private**: a tároló-és a blob-adatkészleteket csak a fiók tulajdonosa tudja beolvasni.
- **Blob**: a tárolóban található blob-információk névtelen kéréssel olvashatók, de a tárolók nem érhetők el. Az ügyfelek névtelen kéréssel nem tudják enumerálni a tárolóban lévő blobokat.
- **Tároló**: a tárolók és a Blobok nem olvashatók be a névtelen kérelem használatával. Az ügyfelek névtelen kéréssel enumerálják a tárolóban lévő blobokat, de nem tudják enumerálni a tároló fiókjában lévő tárolókat.

Az alábbi példa bemutatja, hogyan hozhat létre **egy tároló-hozzáférési** engedélyekkel rendelkező tárolót, amely lehetővé teszi a nyilvános, csak olvasási hozzáférést az interneten lévő összes felhasználó számára:

```objc
-(void)createContainerWithPublicAccess{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    // Create container in your Storage account if the container doesn't already exist
    [blobContainer createContainerIfNotExistsWithAccessType:AZSContainerPublicAccessTypeContainer requestOptions:nil operationContext:nil completionHandler:^(NSError *error, BOOL exists){
        if (error){
            NSLog(@"Error in creating container.");
        }
    }];
}
```

## <a name="upload-a-blob-into-a-container"></a>Blobok feltöltése a tárolóba

Ahogy azt a Blob service fogalmak szakaszban is említettük, Blob Storage három különböző típusú blobot kínál: Blobok, blobok hozzáfűzése és Blobok. Az Azure Storage iOS-függvénytár a Blobok mindhárom típusát támogatja. A legtöbb esetben a blokkblobok használata javasolt.

Az alábbi példa bemutatja, hogyan tölthet fel egy blokk-blobot egy NSString. Ha a tárolóban már van ilyen nevű blob, a blob tartalma felül lesz írva.

```objc
-(void)uploadBlobToContainer{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    [blobContainer createContainerIfNotExistsWithAccessType:AZSContainerPublicAccessTypeContainer requestOptions:nil operationContext:nil completionHandler:^(NSError *error, BOOL exists)
        {
            if (error){
                NSLog(@"Error in creating container.");
            }
            else{
                // Create a local blob object
                AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob"];

                // Upload blob to Storage
                [blockBlob uploadFromText:@"This text will be uploaded to Blob Storage." completionHandler:^(NSError *error) {
                    if (error){
                        NSLog(@"Error in creating blob.");
                    }
                }];
            }
        }];
}
```

Ennek ellenőrzéséhez tekintse meg a [Microsoft Azure Storage Explorer](https://storageexplorer.com) , és ellenőrizze, hogy a tároló ( *containerpublic*) tartalmazza-e a blobot ( *sampleblob*). Ebben a példában egy nyilvános tárolót használunk, így azt is ellenőrizheti, hogy az alkalmazás működik-e a Blobok URI-ja:

```http
https://nameofyourstorageaccount.blob.core.windows.net/containerpublic/sampleblob
```

A blokk Blobok NSString való feltöltése mellett a NSData, a NSInputStream vagy egy helyi fájlhoz hasonló metódusok is léteznek.

## <a name="list-the-blobs-in-a-container"></a>Tárolóban lévő blobok kilistázása

Az alábbi példa bemutatja, hogyan listázhatja ki a tárolóban lévő összes blobot. A művelet végrehajtásakor a következő paramétereket kell szem előtt tartva:

- **continuationtoken argumentumot használja** – a folytatási token azt jelöli, hogy a listázási művelet mikor induljon el. Ha nem ad meg jogkivonatot, a rendszer az elejéről listázza a blobokat. Tetszőleges számú blobot lehet kilistázni, nullától akár legfeljebb egy készletig. Még akkor is, ha ez a metódus nulla eredményt ad vissza, ha `results.continuationToken` az nem üres, akkor a szolgáltatásban további Blobok jelenhetnek meg, amelyek nem szerepelnek a felsorolásban.
- **előtag** – megadhatja a Blobok listázásához használandó előtagot. Csak a jelen előtaggal kezdődő Blobok jelennek meg.
- **useflatbloblisting paraméterét** – ahogy azt a [tárolók elnevezése és a hivatkozó tárolók és Blobok](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata) szakaszban is említettük, bár a blob Service egy egyszerű tárolási séma, a Blobok elérésiút-információkkal történő elnevezésével létrehozható egy virtuális hierarchia. A nem lapos felsorolás azonban jelenleg nem támogatott. Ez a funkció hamarosan elérhető lesz. Egyelőre ez az érték **Igen**.
- **blobListingDetails** – megadhatja, hogy mely elemek szerepeljenek a Blobok listázásakor
  - _AZSBlobListingDetailsNone_: csak a véglegesített Blobok listázása, és nem ad vissza BLOB-metaadatokat.
  - _AZSBlobListingDetailsSnapshots_: véglegesített blobok és blob-Pillanatképek listázása.
  - _AZSBlobListingDetailsMetadata_: a felsorolásban visszaadott egyes Blobok blob-metaadatainak beolvasása.
  - _AZSBlobListingDetailsUncommittedBlobs_: a véglegesített és nem véglegesített Blobok listázása.
  - _AZSBlobListingDetailsCopy_: a lista másolási tulajdonságait tartalmazza.
  - _AZSBlobListingDetailsAll_: felsorolja az összes elérhető véglegesített blobot, a nem véglegesített blobokat és a pillanatképeket, és visszaküldi a Blobok összes metaadatát és másolási állapotát.
- **maxResults** – a művelethez visszaadni kívánt eredmények maximális száma. Ha nem állít be korlátot, használja az-1 értéket.
- **completionHandler** – a listázási művelet eredményeivel végrehajtandó kód blokkja.

Ebben a példában egy segítő metódust használunk a lista Blobok metódusának rekurzív meghívására, amikor a rendszer minden alkalommal visszaadja a folytatási tokent.

```objc
-(void)listBlobsInContainer{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    //List all blobs in container
    [self listBlobsInContainerHelper:blobContainer continuationToken:nil prefix:nil blobListingDetails:AZSBlobListingDetailsAll maxResults:-1 completionHandler:^(NSError *error) {
        if (error != nil){
            NSLog(@"Error in creating container.");
        }
    }];
}

//List blobs helper method
-(void)listBlobsInContainerHelper:(AZSCloudBlobContainer *)container continuationToken:(AZSContinuationToken *)continuationToken prefix:(NSString *)prefix blobListingDetails:(AZSBlobListingDetails)blobListingDetails maxResults:(NSUInteger)maxResults completionHandler:(void (^)(NSError *))completionHandler
{
    [container listBlobsSegmentedWithContinuationToken:continuationToken prefix:prefix useFlatBlobListing:YES blobListingDetails:blobListingDetails maxResults:maxResults completionHandler:^(NSError *error, AZSBlobResultSegment *results) {
        if (error)
        {
            completionHandler(error);
        }
        else
        {
            for (int i = 0; i < results.blobs.count; i++) {
                NSLog(@"%@",[(AZSCloudBlockBlob *)results.blobs[i] blobName]);
            }
            if (results.continuationToken)
            {
                [self listBlobsInContainerHelper:container continuationToken:results.continuationToken prefix:prefix blobListingDetails:blobListingDetails maxResults:maxResults completionHandler:completionHandler];
            }
            else
            {
                completionHandler(nil);
            }
        }
    }];
}
```

## <a name="download-a-blob"></a>Blob letöltése

Az alábbi példa bemutatja, hogyan tölthető le egy blob egy NSString objektumra.

```objc
-(void)downloadBlobToString{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    // Create a local blob object
    AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob"];

    // Download blob
    [blockBlob downloadToTextWithCompletionHandler:^(NSError *error, NSString *text) {
        if (error) {
            NSLog(@"Error in downloading blob");
        }
        else{
            NSLog(@"%@",text);
        }
    }];
}
```

## <a name="delete-a-blob"></a>Blob törlése

Az alábbi példa bemutatja, hogyan törölhet egy blobot.

```objc
-(void)deleteBlob{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    // Create a local blob object
    AZSCloudBlockBlob *blockBlob = [blobContainer blockBlobReferenceFromName:@"sampleblob1"];

    // Delete blob
    [blockBlob deleteWithCompletionHandler:^(NSError *error) {
        if (error) {
            NSLog(@"Error in deleting blob.");
        }
    }];
}
```

## <a name="delete-a-blob-container"></a>BLOB-tároló törlése

Az alábbi példa bemutatja, hogyan törölhet egy tárolót.

```objc
-(void)deleteContainer{
    NSError *accountCreationError;

    // Create a storage account object from a connection string.
    AZSCloudStorageAccount *account = [AZSCloudStorageAccount accountFromConnectionString:@"DefaultEndpointsProtocol=https;AccountName=your_account_name_here;AccountKey=your_account_key_here" error:&accountCreationError];

    if(accountCreationError){
        NSLog(@"Error in creating account.");
    }

    // Create a blob service client object.
    AZSCloudBlobClient *blobClient = [account getBlobClient];

    // Create a local container object.
    AZSCloudBlobContainer *blobContainer = [blobClient containerReferenceFromName:@"containerpublic"];

    // Delete container
    [blobContainer deleteContainerIfExistsWithCompletionHandler:^(NSError *error, BOOL success) {
        if(error){
            NSLog(@"Error in deleting container");
        }
    }];
}
```

## <a name="next-steps"></a>Következő lépések

Most, hogy megismerte, hogyan használhatja a Blob Storaget iOS-ről, az alábbi hivatkozásokra kattintva további információkat tudhat meg az iOS-könyvtárról és a Storage szolgáltatásról.

- [Azure Storage ügyféloldali kódtár iOS rendszerhez](https://github.com/azure/azure-storage-ios)
- [Az Azure Storage iOS dokumentációja](https://azure.github.io/azure-storage-ios/)
- [Az Azure Storage-szolgáltatások REST API-ja](/rest/api/storageservices/)
- [Az Azure Storage csapat blogja](/archive/blogs/windowsazurestorage/)

Ha kérdése van a könyvtárral kapcsolatban, tegye fel a [kérdést a Microsoft Q&egy kérdés oldalára](/answers/topics/azure-blob-storage.html) vagy [stack overflow](https://stackoverflow.com/questions/tagged/windows-azure-storage+or+windows-azure-storage+or+azure-storage-blobs+or+azure-storage-tables+or+azure-table-storage+or+windows-azure-queues+or+azure-storage-queues+or+azure-storage-emulator+or+azure-storage-files).
Ha az Azure Storage szolgáltatásra vonatkozó javaslatokkal rendelkezik, tegye közzé az [Azure Storage visszajelzéseit](https://feedback.azure.com/forums/217298-storage/).