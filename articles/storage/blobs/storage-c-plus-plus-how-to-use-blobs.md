---
title: How to use object (Blob) storage from C++ - Azure | Microsoft Docs
description: Store unstructured data in the cloud with Azure Blob (object) storage.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 03/21/2018
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.openlocfilehash: be3dc27823c09823133d5b9a3a3f34afe52ec57d
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227904"
---
# <a name="how-to-use-blob-storage-from-c"></a>How to use Blob storage from C++

This guide demonstrates how to perform common scenarios using Azure Blob storage. The examples show how to upload, list, download, and delete blobs. A kódminták C++ nyelven íródtak, és az [Azure Storage C++ programnyelvhez készült ügyféloldali kódtárát](https://github.com/Azure/azure-storage-cpp/blob/master/README.md) használják.   

To learn more about Blob storage, see [Introduction to Azure Blob storage](storage-blobs-introduction.md).

> [!NOTE]
> Ez az útmutató az Azure Storage C++ programnyelvhez készült ügyféloldali kódtárának 1.0.0-s és újabb verziójára vonatkozik. Microsoft recommends using the latest version of the Storage Client Library for C++, available via [NuGet](https://www.nuget.org/packages/wastorage) or [GitHub](https://github.com/Azure/azure-storage-cpp).

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-c-application"></a>C++-alkalmazás létrehozása
In this guide, you will use storage features which can be run within a C++ application.  

Ehhez telepítenie kell az Azure Storage C++ programnyelvhez készült ügyféloldali kódtárát, és létre kell hoznia egy Azure Storage-fiókot az Azure-előfizetésben.   

Az Azure Storage C++ programnyelvhez készült ügyféloldali kódtárát az alábbi módszerekkel telepítheti:

* **Linux:** Follow the instructions given in the [Azure Storage Client Library for C++ README: Getting Started on Linux](https://github.com/Azure/azure-storage-cpp#getting-started-on-linux) page.
* **Windows:** On Windows, use [vcpkg](https://github.com/microsoft/vcpkg) as the dependency manager. Follow the [quick-start](https://github.com/microsoft/vcpkg#quick-start) to initialize vcpkg. Then, use the following command to install the library:

```powershell
.\vcpkg.exe install azure-storage-cpp
```

You can find a guide for how to build the source code and export to Nuget in the [README](https://github.com/Azure/azure-storage-cpp#download--install) file.

## <a name="configure-your-application-to-access-blob-storage"></a>Configure your application to access Blob storage
Add the following include statements to the top of the C++ file where you want to use the Azure storage APIs to access blobs:  

```cpp
#include <was/storage_account.h>
#include <was/blob.h>
#include <cpprest/filestream.h>  
#include <cpprest/containerstream.h> 
```

## <a name="setup-an-azure-storage-connection-string"></a>Setup an Azure storage connection string
Az Azure Storage-ügyfél egy tárolási kapcsolati sztringet használ az adatkezelési szolgáltatások elérésére szolgáló végpontok és hitelesítő adatok tárolásához. When running in a client application, you must provide the storage connection string in the following format, using the name of your storage account and the storage access key for the storage account listed in the [Azure Portal](https://portal.azure.com) for the *AccountName* and *AccountKey* values. For information on storage accounts and access keys, see [About Azure Storage Accounts](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json). Ez a példa bemutatja, hogyan deklarálhat statikus mezőt a kapcsolati sztring tárolására:  

```cpp
// Define the connection-string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=your_storage_account;AccountKey=your_storage_account_key"));
```

To test your application in your local Windows computer, you can use the Microsoft Azure [storage emulator](../storage-use-emulator.md) that is installed with the [Azure SDK](https://azure.microsoft.com/downloads/). The storage emulator is a utility that simulates the Blob, Queue, and Table services available in Azure on your local development machine. A következő példa bemutatja, hogyan deklarálhat statikus mezőt a helyi Storage Emulatorhoz használható kapcsolati sztring tárolására:

```cpp
// Define the connection-string with Azure Storage Emulator.
const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  
```

To start the Azure storage emulator, Select the **Start** button or press the **Windows** key. Begin typing **Azure Storage Emulator**, and select **Microsoft Azure Storage Emulator** from the list of applications.  

Az alábbi minták azt feltételezik, hogy az ezen két módszer egyikével kérte le a Storage kapcsolati sztringjét.  

## <a name="retrieve-your-storage-account"></a>Retrieve your storage account
You can use the **cloud_storage_account** class to represent your Storage Account information. A Storage-fiók információit a **parse** metódussal kérheti le a Storage kapcsolati sztringjéből.  

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
```

Next, get a reference to a **cloud_blob_client** class as it allows you to retrieve objects that represent containers and blobs stored within Blob storage. The following code creates a **cloud_blob_client** object using the storage account object we retrieved above:  

```cpp
// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();  
```

## <a name="how-to-create-a-container"></a>How to: Create a container
[!INCLUDE [storage-container-naming-rules-include](../../../includes/storage-container-naming-rules-include.md)]

A példa bemutatja, hogyan hozhat létre tárolót, ha még nem rendelkezik vele:  

```cpp
try
{
    // Retrieve storage account from connection string.
    azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

    // Create the blob client.
    azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

    // Retrieve a reference to a container.
    azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

    // Create the container if it doesn't already exist.
    container.create_if_not_exists();
}
catch (const std::exception& e)
{
    std::wcout << U("Error: ") << e.what() << std::endl;
}  
```

By default, the new container is private and you must specify your storage access key to download blobs from this container. If you want to make the files (blobs) within the container available to everyone, you can set the container to be public using the following code:  

```cpp
// Make the blob container publicly accessible.
azure::storage::blob_container_permissions permissions;
permissions.set_public_access(azure::storage::blob_container_public_access_type::blob);
container.upload_permissions(permissions);  
```

Anyone on the Internet can see blobs in a public container, but you can modify or delete them only if you have the appropriate access key.  

## <a name="how-to-upload-a-blob-into-a-container"></a>How to: Upload a blob into a container
Azure Blob storage supports block blobs and page blobs. A legtöbb esetben a blokkblobok használata javasolt.  

Fájlok blokkblobba való feltöltéséhez szerezze be a tároló hivatkozását, és annak segítségével kérje le a blokkblob hivatkozását. Once you have a blob reference, you can upload any stream of data to it by calling the **upload_from_stream** method. Ez az eljárás létrehozza a blobot, ha az még nem létezett, vagy felülírja, ha már igen. Az alábbi példák azt mutatják be, hogyan tölthetők fel blobok egy tárolóba, és feltételezik, hogy a tároló már létre lett hozva.  

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Retrieve reference to a blob named "my-blob-1".
azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

// Create or overwrite the "my-blob-1" blob with contents from a local file.
concurrency::streams::istream input_stream = concurrency::streams::file_stream<uint8_t>::open_istream(U("DataFile.txt")).get();
blockBlob.upload_from_stream(input_stream);
input_stream.close().wait();

// Create or overwrite the "my-blob-2" and "my-blob-3" blobs with contents from text.
// Retrieve a reference to a blob named "my-blob-2".
azure::storage::cloud_block_blob blob2 = container.get_block_blob_reference(U("my-blob-2"));
blob2.upload_text(U("more text"));

// Retrieve a reference to a blob named "my-blob-3".
azure::storage::cloud_block_blob blob3 = container.get_block_blob_reference(U("my-directory/my-sub-directory/my-blob-3"));
blob3.upload_text(U("other text"));  
```

Alternatively, you can use the **upload_from_file** method to upload a file to a block blob.

## <a name="how-to-list-the-blobs-in-a-container"></a>How to: List the blobs in a container
A tárolóban lévő blobok listázásához először kérje le a tároló hivatkozását. You can then use the container's **list_blobs** method to retrieve the blobs and/or directories within it. To access the rich set of properties and methods for a returned **list_blob_item**, you must call the **list_blob_item.as_blob** method to get a  **cloud_blob** object, or the **list_blob.as_directory** method to get a  cloud_blob_directory object. The following code demonstrates how to retrieve and output the URI of each item in the **my-sample-container** container:

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Output URI of each item.
azure::storage::list_blob_item_iterator end_of_results;
for (auto it = container.list_blobs(); it != end_of_results; ++it)
{
    if (it->is_blob())
    {
        std::wcout << U("Blob: ") << it->as_blob().uri().primary_uri().to_string() << std::endl;
    }
    else
    {
        std::wcout << U("Directory: ") << it->as_directory().uri().primary_uri().to_string() << std::endl;
    }
}
```

For more details on listing operations, see [List Azure Storage Resources in C++](../storage-c-plus-plus-enumeration.md).

## <a name="how-to-download-blobs"></a>How to: Download blobs
To download blobs, first retrieve a blob reference and then call the **download_to_stream** method. The following example uses the **download_to_stream** method to transfer the blob contents to a stream object that you can then persist to a local file.  

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Retrieve reference to a blob named "my-blob-1".
azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

// Save blob contents to a file.
concurrency::streams::container_buffer<std::vector<uint8_t>> buffer;
concurrency::streams::ostream output_stream(buffer);
blockBlob.download_to_stream(output_stream);

std::ofstream outfile("DownloadBlobFile.txt", std::ofstream::binary);
std::vector<unsigned char>& data = buffer.collection();

outfile.write((char *)&data[0], buffer.size());
outfile.close();  
```

Alternatively, you can use the **download_to_file** method to download the contents of a blob to a file.
In addition, you can also use the **download_text** method to download the contents of a blob as a text string.  

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Retrieve reference to a blob named "my-blob-2".
azure::storage::cloud_block_blob text_blob = container.get_block_blob_reference(U("my-blob-2"));

// Download the contents of a blog as a text string.
utility::string_t text = text_blob.download_text();
```

## <a name="how-to-delete-blobs"></a>How to: Delete blobs
To delete a blob, first get a blob reference and then call the **delete_blob** method on it.  

```cpp
// Retrieve storage account from connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the blob client.
azure::storage::cloud_blob_client blob_client = storage_account.create_cloud_blob_client();

// Retrieve a reference to a previously created container.
azure::storage::cloud_blob_container container = blob_client.get_container_reference(U("my-sample-container"));

// Retrieve reference to a blob named "my-blob-1".
azure::storage::cloud_block_blob blockBlob = container.get_block_blob_reference(U("my-blob-1"));

// Delete the blob.
blockBlob.delete_blob();
```

## <a name="next-steps"></a>Következő lépések
Now that you've learned the basics of blob storage, follow these links to learn more about Azure Storage.  

* [How to use Queue Storage from C++](../storage-c-plus-plus-how-to-use-queues.md)
* [How to use Table Storage from C++](../../cosmos-db/table-storage-how-to-use-c-plus.md)
* [List Azure Storage Resources in C++](../storage-c-plus-plus-enumeration.md)
* [Storage Client Library for C++ Reference](https://azure.github.io/azure-storage-cpp)
* [Az Azure Storage dokumentációja](https://azure.microsoft.com/documentation/services/storage/)
* [Adatátvitel az AzCopy parancssori segédprogrammal](../storage-use-azcopy.md)

