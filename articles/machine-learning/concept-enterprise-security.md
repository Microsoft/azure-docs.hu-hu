---
title: Vállalati biztonság és irányítás
titleSuffix: Azure Machine Learning
description: 'Biztonságos használat Azure Machine Learning: hitelesítés, engedélyezés, hálózati biztonság, adattitkosítás és figyelés.'
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 11/20/2020
ms.openlocfilehash: a079504872eaf3840416a99e784c4d33a6828b0c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94992029"
---
# <a name="enterprise-security-and-governance-for-azure-machine-learning"></a>Nagyvállalati biztonság és irányítási Azure Machine Learning

Ebben a cikkben megismerheti a Azure Machine Learning számára elérhető biztonsági és irányítási funkciókat. Ezek a funkciók olyan rendszergazdák, DevOps és MLOps számára hasznosak, akik a vállalati szabályzatoknak megfelelő biztonságos konfigurációt kívánnak létrehozni. A Azure Machine Learning és az Azure platformmal a következőket teheti:

* Erőforrásokhoz és műveletekhez való hozzáférés korlátozása felhasználói fiók vagy csoportok szerint
* A bejövő és a kimenő hálózati kommunikáció korlátozása
* Adatok titkosítása az átvitel és a nyugalmi állapotban
* Biztonsági rések keresése
* Konfigurációs házirendek alkalmazása és naplózása

## <a name="restrict-access-to-resources-and-operations"></a>Erőforrásokhoz és műveletekhez való hozzáférés korlátozása

A [Azure Active Directory (Azure ad)](../active-directory/fundamentals/active-directory-whatis.md) a Azure Machine learning identitás-szolgáltatója. Lehetővé teszi az Azure-erőforrásokkal való _hitelesítéshez_ használt biztonsági objektumok (felhasználó, csoport, szolgáltatásnév és felügyelt identitás) létrehozását és kezelését. A többtényezős hitelesítés támogatott, ha az Azure AD konfigurálva van a használatára.

A többtényezős hitelesítés az Azure AD-ben való használatának Azure Machine Learning hitelesítési folyamata:

1. Az ügyfél bejelentkezik az Azure AD-be, és beolvas egy Azure Resource Manager tokent.
1. Az ügyfél az Azure Resource Manager és az összes Azure Machine Learning számára megadja a tokent.
1. A Azure Machine Learning Machine Learning szolgáltatási tokent biztosít a felhasználó számítási céljához (például Azure Machine Learning számítási fürt). Ezt a tokent a felhasználói számítási cél használja a Machine Learning szolgáltatás visszahívására a Futtatás befejezése után. A hatókör a munkaterületre korlátozódik.

[![Hitelesítés Azure Machine Learning](media/concept-enterprise-security/authentication.png)](media/concept-enterprise-security/authentication.png#lightbox)

Minden munkaterülethez tartozik egy társított, rendszerhez rendelt [felügyelt identitás](../active-directory/managed-identities-azure-resources/overview.md) , amelynek a neve megegyezik a munkaterülettel. Ez a felügyelt identitás a munkaterület által használt erőforrások biztonságos elérésére szolgál. A következő Azure RBAC-engedélyekkel rendelkezik a csatolt erőforrásokon:

| Erőforrás | Engedélyek |
| ----- | ----- |
| Munkaterület | Közreműködő |
| A(z) | Storage-blobadatok közreműködője |
| Key Vault | Hozzáférés az összes kulcshoz, titokhoz, tanúsítványhoz |
| Azure Container Registry | Közreműködő |
| A munkaterületet tartalmazó erőforráscsoport | Közreműködő |
| A kulcstárolót tartalmazó erőforráscsoport (ha eltér a munkaterületet tartalmazótól) | Közreműködő |

Nem javasoljuk, hogy a rendszergazdák visszavonják a felügyelt identitás hozzáférését az előző táblázatban említett erőforrásokhoz. A hozzáférést a [kulcsok újraszinkronizálása művelettel](how-to-change-storage-access-key.md)állíthatja vissza.

A Azure Machine Learning egy további alkalmazást is létrehoz (a név `aml-` vagy `Microsoft-AzureML-Support-App-` ), és az előfizetésében közreműködő szintű hozzáféréssel rendelkezik minden munkaterület-régióhoz. Ha például egy, az USA keleti régiójában és egy észak-európai régióban található egy munkaterülete ugyanabban az előfizetésben, akkor két alkalmazás fog megjelenni. Ezek az alkalmazások lehetővé teszik Azure Machine Learning számára a számítási erőforrások kezelését.

Saját felügyelt identitásokat is konfigurálhat az Azure Virtual Machines és Azure Machine Learning számítási fürttel való használatra. A virtuális géppel a felügyelt identitás használható a munkaterület az SDK-ból való elérésére az egyéni felhasználó Azure AD-fiókja helyett. A számítási fürttel a felügyelt identitás az olyan erőforrások elérésére szolgál, mint például a betanítási feladatot futtató felhasználó által nem férhet hozzá. További információ: Azure Machine Learning- [munkaterület hitelesítése](how-to-setup-authentication.md).

> [!TIP]
> Az Azure AD és az Azure RBAC használatának néhány kivétele Azure Machine Learning belül:
> * Igény szerint engedélyezheti az __SSH__ -hozzáférést a számítási erőforrásokhoz, például a Azure Machine learning számítási példányhoz és a számítási fürthöz. Az SSH-hozzáférés a nyilvános/titkos kulcs párokon alapul, nem az Azure AD-ben. Az SSH-hozzáférésre nem vonatkozik az Azure RBAC.
> * A webszolgáltatásként (következtetési végpontok) üzembe helyezett modelleken __kulcs__ -vagy __jogkivonat__-alapú hitelesítés használatával végezheti el a hitelesítést. A kulcsok statikus karakterláncok, míg a tokeneket egy Azure AD-beli biztonsági objektum használatával kéri le a rendszer. További információkért lásd: [webszolgáltatásként üzembe helyezett modellek hitelesítésének konfigurálása](how-to-authenticate-web-service.md).

További információért tekintse át a következő cikkeket:
* [Azure Machine Learning munkaterület hitelesítése](how-to-setup-authentication.md)
* [Azure Machine Learninghoz való hozzáférés kezelése](how-to-assign-roles.md)
* [Kapcsolódás tárolási szolgáltatásokhoz](how-to-access-data.md)
* [Azure Key Vault használata a titkokhoz tanításkor](how-to-use-secrets-in-runs.md)
* [Az Azure AD által felügyelt identitás használata Azure Machine Learning](how-to-use-managed-identities.md)
* [Az Azure AD által felügyelt identitás használata a webszolgáltatás használatával](how-to-use-azure-ad-identity.md)

## <a name="network-security-and-isolation"></a>Hálózati biztonság és elkülönítés

A Azure Machine Learning erőforrásokhoz való hálózati hozzáférés korlátozásához használhatja az [Azure Virtual Networkt (VNet)](../virtual-network/virtual-networks-overview.md). A virtuális hálózatok lehetővé teszi olyan hálózati környezetek létrehozását, amelyek részben vagy teljesen el vannak különítve a nyilvános internetről. Ez csökkenti a megoldás támadási felületét, valamint az adatkiszűrése esélyét.

Virtuális magánhálózati (VPN) átjárót használhat az egyes ügyfelek vagy a saját hálózatok összekapcsolásához a VNet

A Azure Machine Learning munkaterület az [Azure Private link](../private-link/private-link-overview.md) használatával létrehozhat egy privát végpontot a VNet mögött. Ez több magánhálózati IP-címet is biztosít, amelyek segítségével a munkaterületet elérheti a VNet belülről. Néhány szolgáltatás, amely Azure Machine Learning támaszkodik, az Azure privát hivatkozását is használhatja, néhány pedig hálózati biztonsági csoportokra vagy felhasználó által megadott útválasztásra támaszkodik.

További információ a következő dokumentumokban található:

* [Virtual Network elkülönítés és Adatvédelem – áttekintés](how-to-network-security-overview.md)
* [Biztonságos munkaterületi erőforrások](how-to-secure-workspace-vnet.md)
* [Biztonságos betanítási környezet](how-to-secure-training-vnet.md)
* [Biztonságos következtetési környezet](how-to-secure-inferencing-vnet.md)
* [A Studio használata biztonságos virtuális hálózaton](how-to-enable-studio-virtual-network.md)
* [Egyéni DNS használata](how-to-custom-dns.md)
* [Tűzfal konfigurálása](how-to-access-azureml-behind-firewall.md)

<a id="encryption-at-rest"></a><a id="azure-blob-storage"></a>

## <a name="data-encryption"></a>Adattitkosítás

Azure Machine Learning számos számítási erőforrást és adattárolót használ az Azure platformon. Ha többet szeretne megtudni arról, hogy ezek hogyan támogatják az adattitkosítást a REST és az átvitel során, tekintse meg az [adattitkosítás Azure Machine learning](concept-data-encryption.md)használatával című témakört.

A modellek webszolgáltatásként való üzembe helyezésekor engedélyezheti a Transport Layer Security (TLS) szolgáltatást az átvitel közbeni adattitkosításhoz. További információ: [biztonságos webszolgáltatás konfigurálása](how-to-secure-web-service.md).

## <a name="vulnerability-scanning"></a>Biztonsági rések vizsgálata

Az [Azure Security Center](../security-center/security-center-introduction.md) egységes biztonsági felügyeletet és fejlett fenyegetésvédelmet biztosít a hibrid felhőalapú számítási feladatokhoz. Az Azure Machine learning esetében engedélyeznie kell a [Azure Container Registry](../container-registry/container-registry-intro.md) erőforrás és az Azure Kubernetes szolgáltatás-erőforrások vizsgálatát. További információ: [Azure Container Registry rendszerképek vizsgálata Security Center](../security-center/defender-for-container-registries-introduction.md) és az [Azure Kubernetes Services integrációja Security Center](../security-center/defender-for-kubernetes-introduction.md)használatával.

## <a name="audit-and-manage-compliance"></a>Naplózás és megfelelőség kezelése

A [Azure Policy](../governance/policy/index.yml) egy irányítási eszköz, amely lehetővé teszi, hogy az Azure-erőforrások megfeleljenek a szabályzatoknak. Házirendeket állíthat be bizonyos konfigurációk engedélyezéséhez vagy betartatásához, például azt, hogy a Azure Machine Learning munkaterület használ-e privát végpontot. A Azure Policyről a [Azure Policy dokumentációjában](../governance/policy/overview.md)talál további információt. További információ a Azure Machine Learningra vonatkozó házirendekről: a [megfelelőség naplózása és kezelése Azure Policyokkal](how-to-integrate-azure-policy.md).

## <a name="next-steps"></a>Következő lépések

* [Biztonságos Azure Machine Learning webszolgáltatások TLS-vel](how-to-secure-web-service.md)
* [Webszolgáltatásként üzembe helyezett Machine Learning-modell felhasználása](how-to-consume-web-service.md)
* [Azure Machine Learning használata a Azure Firewall](how-to-access-azureml-behind-firewall.md)
* [Azure Machine Learning használata az Azure-ban Virtual Network](how-to-network-security-overview.md)
* [Adatok titkosítása nyugalmi és átviteli állapotban](concept-data-encryption.md)
* [Valós idejű ajánlási API létrehozása az Azure-ban](/azure/architecture/reference-architectures/ai/real-time-recommendation)
