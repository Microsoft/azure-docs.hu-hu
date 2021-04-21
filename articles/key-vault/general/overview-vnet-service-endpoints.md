---
title: Virtuális hálózati szolgáltatásvégpontok az Azure Key Vaulthoz
description: Megtudhatja, hogyan korlátozhatja egy adott virtuális hálózathoz való Azure Key Vault virtuális hálózati szolgáltatásvégpontokat, beleértve a használati forgatókönyveket is.
services: key-vault
author: amitbapat
ms.author: ambapat
ms.date: 01/02/2019
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.openlocfilehash: 985cbe1128d1dd64fda86ef062750dc5dd068ffe
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751732"
---
# <a name="virtual-network-service-endpoints-for-azure-key-vault"></a>Virtuális hálózati szolgáltatásvégpontok az Azure Key Vaulthoz

A virtuális hálózati szolgáltatásvégpontjai Azure Key Vault lehetővé teszik egy adott virtuális hálózathoz való hozzáférés korlátozását. A végpontok emellett lehetővé teszik az IPv4-címtartományok listájához való hozzáférés korlátozását is. A kulcstartóhoz ezen forrásokon kívülről csatlakozó bármely felhasználónak nincs hozzáférése.

Ez alól a korlátozás alól van egy fontos kivétel. Ha egy felhasználó úgy döntött, hogy engedélyezi a megbízható Microsoft-szolgáltatások, az ezen szolgáltatásokból származó kapcsolatok áthaladnak a tűzfalon. Ilyen szolgáltatás például az Office 365 Exchange Online, az Office 365 SharePoint Online, az Azure Compute, Azure Resource Manager és Azure Backup. Az ilyen felhasználóknak továbbra is be kell állítaniuk egy érvényes Azure Active Directory jogkivonatot, és (hozzáférési szabályzatként konfigurált) engedélyekkel kell rendelkezniük a kért művelet végrehajtásához. További információ: [Virtuális hálózati szolgáltatásvégpont.](../../virtual-network/virtual-network-service-endpoints-overview.md)

## <a name="usage-scenarios"></a>Használati forgatókönyvek

A tűzfalak [Key Vault virtuális](network-security.md) hálózatokat úgy konfigurálhatja, hogy alapértelmezés szerint megtagadják az összes hálózatról (beleértve az internetes forgalmat is) származó forgalomhoz való hozzáférést. Hozzáférést adhat adott Azure-beli virtuális hálózatokról és nyilvános internetes IP-címtartományból származó forgalomhoz, így biztonságos hálózathatárt építhet ki alkalmazásai számára.

> [!NOTE]
> Key Vault tűzfalak és virtuális hálózati szabályok csak a virtuális gépek [adatsíkja](security-overview.md#privileged-access) Key Vault. Key Vault vezérlősík műveleteire (például létrehozási, törlési és módosítási műveletek, hozzáférési szabályzatok beállítása, tűzfalak és virtuális hálózati szabályok beállítása, valamint titkos kulcsok vagy kulcsok ARM-sablonokon keresztüli üzembe helyezése) nincsenek hatással a tűzfalakra és a virtuális hálózati szabályokra.

Íme néhány példa a szolgáltatásvégpont használatára:

* A titkosítási kulcsok Key Vault titkos kódokat és tanúsítványokat tárolja, és szeretné letiltani a kulcstartó nyilvános internetről való hozzáférését.
* Szeretné zárolni a kulcstartóhoz való hozzáférést, hogy csak az alkalmazás vagy a kijelölt gazdagépek rövid listája kapcsolódjon a kulcstartóhoz.
* Az Azure-beli virtuális hálózatán egy alkalmazás fut, és ez a virtuális hálózat minden bejövő és kimenő forgalom számára zárolva van. Az alkalmazásnak továbbra is csatlakoznia kell a Key Vault titkos kulcsok vagy tanúsítványok lekérése vagy titkosítási kulcsok használata érdekében.

## <a name="trusted-services"></a>Megbízható szolgáltatások

Az alábbi lista azokat a megbízható szolgáltatásokat sorolja fel, amelyek hozzáférhetnek a kulcstartóhoz, ha a Megbízható **szolgáltatások** engedélyezése beállítás engedélyezve van.

|Megbízható szolgáltatás|Támogatott használati forgatókönyvek|
| --- | --- |
|Azure Virtual Machines üzembe helyezési szolgáltatás|[Tanúsítványokat telepíthet virtuális gépekre az](/archive/blogs/kv/updated-deploy-certificates-to-vms-from-customer-managed-key-vault)ügyfél által felügyelt Key Vault.|
|Azure Resource Manager üzembe helyezési szolgáltatás létrehozása|[Az üzembe helyezés során biztonságos értékeket ad át.](../../azure-resource-manager/templates/key-vault-parameter.md)|
|Azure Disk Encryption kötettitkosítási szolgáltatás|BitLocker-kulcshoz (Windows VM) vagy DM-jelszóhoz (Linux VM) és kulcstitkosítási kulcshoz való hozzáférés engedélyezése a virtuális gép üzembe helyezése során. Ez lehetővé teszi [a Azure Disk Encryption.](../../security/fundamentals/encryption-overview.md)|
|Azure Backup|Engedélyezze a releváns kulcsok és titkos kulcsok biztonsági mentését és visszaállítását az Azure Virtual Machines során a [következő Azure Backup.](../../backup/backup-overview.md)|
|Exchange Online & SharePoint Online|Hozzáférés engedélyezése az Azure-beli Storage Service Encryption [ügyfélkulcshoz.](/microsoft-365/compliance/customer-key-overview)|
|Azure Information Protection|Hozzáférés engedélyezése a bérlőkulcshoz a [Azure Information Protection.](/azure/information-protection/what-is-information-protection)|
|Azure App Service|[Telepítse az Azure-webalkalmazás tanúsítványát a Key Vault.](https://azure.github.io/AppService/2016/05/24/Deploying-Azure-Web-App-Certificate-through-Key-Vault.html)|
|Azure SQL Database|[transzparens adattitkosítás és Bring Your Own Key támogatással Azure SQL Database és Azure Synapse Analytics.](../../azure-sql/database/transparent-data-encryption-byok-overview.md)|
|Azure Storage|[Storage Service Encryption által kezelt kulcsok használata a Azure Key Vault.](../../storage/common/customer-managed-keys-configure-key-vault.md)|
|Azure Data Lake Store|[Az Azure-beli adattitkosítás Data Lake Store](../../data-lake-store/data-lake-store-encryption.md) felhasználó által felügyelt kulccsal.|
|Azure Synapse Analytics|[Adatok titkosítása ügyfél által kezelt kulcsokkal a Azure Key Vault](../../synapse-analytics/security/workspaces-encryption.md)|
|Azure Databricks|[Gyors, egyszerű és együttműködésen alapuló Apache Spark-alapú elemzési szolgáltatás](/azure/databricks/scenarios/what-is-azure-databricks)|
|Azure API Management|[Tanúsítványok központi telepítése Custom Domain Key Vault MSI használatával](../../api-management/api-management-howto-use-managed-service-identity.md#use-ssl-tls-certificate-from-azure-key-vault)|
|Azure Data Factory|[Adattár hitelesítő adatainak beolvasása Key Vault a Data Factory](https://go.microsoft.com/fwlink/?linkid=2109491)|
|Azure Event Hubs|[Kulcstartóhoz való hozzáférés engedélyezése ügyfél által kezelt kulcsok esetén](../../event-hubs/configure-customer-managed-key.md)|
|Azure Service Bus|[Kulcstartóhoz való hozzáférés engedélyezése ügyfél által kezelt kulcsok esetén](../../service-bus-messaging/configure-customer-managed-key.md)|
|Azure Import/Export| [Felhasználó által kezelt kulcsok használata a Azure Key Vault importálási/exportálási szolgáltatáshoz](../../import-export/storage-import-export-encryption-key-portal.md)
|Azure Container Registry|[Beállításjegyzék-titkosítás ügyfél által kezelt kulcsokkal](../../container-registry/container-registry-customer-managed-keys.md)
|Azure Application Gateway |[Tanúsítvány Key Vault HTTPS-kompatibilis figyelők számára](../../application-gateway/key-vault-certs.md)

> [!NOTE]
> Be kell állítania a megfelelő Key Vault, hogy a megfelelő szolgáltatások hozzáférjenek a Key Vault.

## <a name="next-steps"></a>Következő lépések

- Részletes útmutatásért lásd: [Tűzfalak és virtuális hálózatok Azure Key Vault konfigurálása](network-security.md)
- lásd a [Azure Key Vault áttekintését](security-overview.md)
