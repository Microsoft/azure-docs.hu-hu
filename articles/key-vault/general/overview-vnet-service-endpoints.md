---
title: Virtuális hálózati szolgáltatásvégpontok az Azure Key Vaulthoz
description: Megtudhatja, hogyan korlátozhatja a virtuális hálózati szolgáltatás végpontját Azure Key Vault lehetővé teszi a hozzáférést egy adott virtuális hálózathoz, beleértve a használati forgatókönyveket is.
services: key-vault
author: amitbapat
ms.author: ambapat
manager: rkarlin
ms.date: 01/02/2019
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.openlocfilehash: ae22f07a70f3317b62776e5024b7a3d1084516a1
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2021
ms.locfileid: "105643480"
---
# <a name="virtual-network-service-endpoints-for-azure-key-vault"></a>Virtuális hálózati szolgáltatásvégpontok az Azure Key Vaulthoz

A Azure Key Vault Virtual Network szolgáltatásbeli végpontok lehetővé teszik, hogy korlátozza a hozzáférést egy adott virtuális hálózathoz. A végpontok azt is lehetővé teszik, hogy korlátozza a hozzáférést az IPv4 (Internet Protocol Version 4)-címtartományok listájához. A Key vaulthoz a forrásokon kívülről csatlakozó felhasználók hozzáférése megtagadva.

Erre a korlátozásra vonatkozóan van egy fontos kivétel. Ha egy felhasználó engedélyezte a megbízható Microsoft-szolgáltatások engedélyezését, a szolgáltatással létesített kapcsolatok a tűzfalon keresztül érhetők el. Ilyen szolgáltatások például az Office 365 Exchange Online, az Office 365 SharePoint Online, az Azure számítás, a Azure Resource Manager és a Azure Backup. Az ilyen felhasználóknak továbbra is érvényes Azure Active Directory jogkivonatot kell tartalmazniuk, és a kért művelet végrehajtásához engedélyekkel kell rendelkezniük (hozzáférési házirendként konfigurálva). További információ: [Virtual Network szolgáltatás-végpontok](../../virtual-network/virtual-network-service-endpoints-overview.md).

## <a name="usage-scenarios"></a>Használati forgatókönyvek

A [Key Vault tűzfalak és virtuális hálózatok](network-security.md) konfigurálásával megtagadhatja az adatforgalom elérését az összes hálózatról (beleértve az internetes forgalmat is), alapértelmezés szerint. Hozzáférést biztosíthat bizonyos Azure-beli virtuális hálózatokból és nyilvános internetes IP-címtartományok érkező adatforgalomhoz, így biztonságos hálózati határt hozhat létre az alkalmazásai számára.

> [!NOTE]
> Key Vault tűzfalak és virtuális hálózati szabályok csak Key Vault [adatsíkon](secure-your-key-vault.md#data-plane-access-control) érvényesek. A tűzfalak és a virtuális hálózati szabályok nem érintik a vezérlési sík műveleteit (például a létrehozási, törlési és módosítási műveleteket, a hozzáférési házirendek beállítását, a tűzfalak beállítását és a virtuális hálózati szabályokat, valamint a titkok vagy kulcsok üzembe helyezését az ARM-sablonokon keresztül). Key Vault

Íme néhány példa a szolgáltatási végpontok használatára:

* A Key Vault használatával tárolja a titkosítási kulcsokat, az alkalmazási titkokat és a tanúsítványokat, és le szeretné tiltani a Key vaulthoz való hozzáférést a nyilvános internetről.
* Le szeretné zárni a kulcstartóhoz való hozzáférést, hogy csak az alkalmazása vagy a kijelölt gazdagépek rövid listája csatlakozhasson a kulcstartóhoz.
* Rendelkezik egy, az Azure-beli virtuális hálózaton futó alkalmazással, és ez a virtuális hálózat le van zárva az összes bejövő és kimenő forgalom esetében. Az alkalmazásnak továbbra is csatlakoznia kell Key Vaulthoz a titkok vagy tanúsítványok lekéréséhez, vagy titkosítási kulcsokat kell használnia.

## <a name="trusted-services"></a>Megbízható szolgáltatások

Itt látható azoknak a megbízható szolgáltatásoknak a listája, amelyek hozzáférhetnek a kulcstartóhoz, ha engedélyezve van a **megbízható szolgáltatások engedélyezése** beállítás.

|Megbízható szolgáltatás|Támogatott használati forgatókönyvek|
| --- | --- |
|Azure Virtual Machines üzembe helyezési szolgáltatás|[Tanúsítványokat telepíthet az ügyfél által felügyelt Key Vault virtuális gépekre](/archive/blogs/kv/updated-deploy-certificates-to-vms-from-customer-managed-key-vault).|
|Azure Resource Manager sablon központi telepítési szolgáltatása|[Biztonságos értékek továbbítása az üzembe helyezés során](../../azure-resource-manager/templates/key-vault-parameter.md).|
|Azure Disk Encryption mennyiségi titkosítási szolgáltatás|Hozzáférés engedélyezése a BitLocker-kulcshoz (Windows VM) vagy DM-jelszóhoz (Linux virtuális gép) és a kulcs titkosítási kulcsához a virtuális gépek telepítése során. Ez lehetővé teszi a [Azure Disk Encryption](../../security/fundamentals/encryption-overview.md).|
|Azure Backup|A megfelelő kulcsok és titkok biztonsági mentésének és helyreállításának engedélyezése az Azure Virtual Machines Backup szolgáltatásban [Azure Backup](../../backup/backup-overview.md)használatával.|
|Exchange Online & SharePoint Online-ban|Hozzáférés engedélyezése az Azure Storage Service Encryptionhoz az [ügyfél kulcsával](/microsoft-365/compliance/customer-key-overview).|
|Azure Information Protection|A bérlői kulcs elérésének engedélyezése [Azure Information Protection számára.](/azure/information-protection/what-is-information-protection)|
|Azure App Service|[Azure webalkalmazás-tanúsítvány üzembe helyezése Key Vault használatával](https://azure.github.io/AppService/2016/05/24/Deploying-Azure-Web-App-Certificate-through-Key-Vault.html).|
|Azure SQL Database|[Transzparens adattitkosítás a Azure SQL Database és az Azure szinapszis Analytics bring your own Key támogatásával](../../azure-sql/database/transparent-data-encryption-byok-overview.md).|
|Azure Storage|[Storage Service encryption az ügyfél által felügyelt kulcsokat Azure Key Vault](../../storage/common/customer-managed-keys-configure-key-vault.md).|
|Azure Data Lake Store|A [Azure Data Lake Storeban lévő adattitkosítás](../../data-lake-store/data-lake-store-encryption.md) ügyfél által felügyelt kulccsal.|
|Azure Synapse Analytics|[Az ügyfél által felügyelt kulcsokat használó adattitkosítás Azure Key Vault](../../synapse-analytics/security/workspaces-encryption.md)|
|Azure Databricks|[Gyors, könnyű és együttműködő Apache Spark-alapú elemzési szolgáltatás](/azure/databricks/scenarios/what-is-azure-databricks)|
|Azure API Management|[Egyéni tartomány tanúsítványainak központi telepítése Key Vault az MSI használatával](../../api-management/api-management-howto-use-managed-service-identity.md#use-ssl-tls-certificate-from-azure-key-vault)|
|Azure Data Factory|[Adattároló hitelesítő adatainak beolvasása a Key Vault Data Factory](https://go.microsoft.com/fwlink/?linkid=2109491)|
|Azure Event Hubs|[Key Vault hozzáférésének engedélyezése az ügyfél által felügyelt kulcsok esetén](../../event-hubs/configure-customer-managed-key.md)|
|Azure Service Bus|[Key Vault hozzáférésének engedélyezése az ügyfél által felügyelt kulcsok esetén](../../service-bus-messaging/configure-customer-managed-key.md)|
|Azure Import/Export| [Az ügyfél által felügyelt kulcsok használata Azure Key Vault importálási/exportálási szolgáltatáshoz](../../import-export/storage-import-export-encryption-key-portal.md)
|Azure Container Registry|[Beállításjegyzék-titkosítás az ügyfél által felügyelt kulcsokkal](../../container-registry/container-registry-customer-managed-keys.md)
|Azure Application Gateway |[Key Vault-tanúsítványok használata a HTTPS-kompatibilis figyelőkhöz](../../application-gateway/key-vault-certs.md)

> [!NOTE]
> Be kell állítania a megfelelő Key Vault hozzáférési szabályzatokat, hogy a megfelelő szolgáltatások hozzáférjenek a Key Vaulthoz.

## <a name="next-steps"></a>Következő lépések

- Részletes útmutatásért lásd: [Azure Key Vault tűzfalak és virtuális hálózatok konfigurálása](network-security.md)
- lásd a [Azure Key Vault biztonsági áttekintése](security-overview.md) című témakört.
