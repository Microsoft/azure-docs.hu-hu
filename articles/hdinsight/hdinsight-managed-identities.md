---
title: Felügyelt identitások az Azure HDInsight
description: Áttekintést nyújt a felügyelt identitások megvalósításáról az Azure HDInsight-ben.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 04/15/2020
ms.openlocfilehash: f2b7f6e8421a735db131bc05605936e8cb2d87eb
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944120"
---
# <a name="managed-identities-in-azure-hdinsight"></a>Felügyelt identitások az Azure HDInsight

A felügyelt identitás a Azure Active Directory (Azure AD) szolgáltatásban regisztrált identitás, amelynek hitelesítő adatait az Azure felügyeli. A felügyelt identitásokkal nem kell regisztrálnia az egyszerű szolgáltatásokat az Azure AD-ben. Vagy őrizze meg a hitelesítő adatokat, például a tanúsítványokat.

A felügyelt identitások az Azure HDInsight használhatók az Azure AD tartományi szolgáltatások eléréséhez, illetve a Azure Data Lake Storage Gen2 szükség esetén a fájlok eléréséhez.

A felügyelt identitások két típusa létezik: felhasználó által hozzárendelt és rendszerhez rendelt. Az Azure HDInsight csak a felhasználó által hozzárendelt felügyelt identitásokat támogatja. A HDInsight nem támogatja a rendszerhez rendelt felügyelt identitásokat. A felhasználó által hozzárendelt felügyelt identitás önálló Azure-erőforrásként jön létre, amelyet aztán hozzárendelhet egy vagy több Azure-szolgáltatási példányhoz. Ezzel szemben egy rendszerhez rendelt felügyelt identitás jön létre az Azure AD-ben, majd közvetlenül egy adott Azure-szolgáltatási példányon automatikusan engedélyezve lesz. A rendszer által hozzárendelt felügyelt identitás élettartama ezután a szolgáltatás azon példányának élettartamához kötődik, amelyen engedélyezve van.

## <a name="hdinsight-managed-identity-implementation"></a>HDInsight felügyelt identitás implementálása

Az Azure HDInsight a felügyelt identitások csak a belső összetevőkhöz használhatók a HDInsight szolgáltatásban. A külső szolgáltatások eléréséhez a HDInsight telepített felügyelt identitások használatával jelenleg nincs támogatott módszer a hozzáférési tokenek létrehozásához. Bizonyos Azure-szolgáltatások, például a számítási virtuális gépek esetében a felügyelt identitások olyan végponttal valósulnak meg, amelyet hozzáférési jogkivonatok beszerzésére használhat. Ez a végpont jelenleg nem érhető el a HDInsight-csomópontokban.

Ha be kell állítania az alkalmazásait, hogy elkerülje a titkokat vagy jelszavakat az elemzési feladatokban (pl.: SCALA-feladatok), a fürt csomópontjain a parancsfájlok használatával terjesztheti a saját tanúsítványait, majd ezt a tanúsítványt használhatja a hozzáférési token beszerzéséhez (például az Azure kulcstartó eléréséhez).

## <a name="create-a-managed-identity"></a>Felügyelt identitás létrehozása

A felügyelt identitások a következő módszerek bármelyikével hozhatók létre:

* [Azure Portalra](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)
* [Azure PowerShell](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
* [Azure Resource Manager](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md)
* [Azure CLI](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)

A felügyelt identitás konfigurálásának hátralévő lépései attól függnek, hogy milyen helyzetben lesz használatban.

## <a name="managed-identity-scenarios-in-azure-hdinsight"></a>Felügyelt identitási forgatókönyvek az Azure HDInsight

A felügyelt identitásokat több forgatókönyvben használják az Azure HDInsight. A részletes beállítási és konfigurációs utasításokért tekintse meg a kapcsolódó dokumentumokat:

* [Azure Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2-portal.md#create-a-user-assigned-managed-identity)
* [Enterprise Security Package](domain-joined/apache-domain-joined-configure-using-azure-adds.md#create-and-authorize-a-managed-identity)
* [Ügyfél által felügyelt kulcson alapuló lemeztitkosítás](disk-encryption.md)

A HDInsight automatikusan megújítja az ezekben a forgatókönyvekben használt felügyelt identitásokhoz tartozó tanúsítványokat. Ha azonban több különböző felügyelt identitást használ a hosszú ideig futó fürtökhöz, előfordulhat, hogy a tanúsítvány megújítása nem a várt módon működik az összes felügyelt identitás esetében. E korlátozás miatt, ha hosszú ideig futó fürtöket kíván használni (például több mint 60 nap), javasoljuk, hogy ugyanazt a felügyelt identitást használja az összes fenti forgatókönyvhöz. 

Ha már létrehozott egy hosszú ideig futó fürtöt, amelynek több különböző felügyelt identitása van, és ezek a problémák valamelyikén futnak:
 * Az ESP-fürtökben a fürtszolgáltatás elindít vagy felskálázást végez, és az egyéb műveletek sikertelenek lesznek a hitelesítési hibákkal.
 * Az ESP-fürtökben a HRE-DS LDAPs tanúsítványának módosításakor az LDAPs-tanúsítvány nem frissül automatikusan, így az LDAP-szinkronizálás és a méretezési lépések sikertelenek lesznek.
 * MSI-hozzáférés ADLS Gen2 indítás sikertelen.
 * A titkosítási kulcsokat nem lehet elforgatni a CMK-forgatókönyvben.

ezt követően a fürtben használt összes felügyelt identitáshoz hozzá kell rendelnie a fenti forgatókönyvekhez szükséges szerepköröket és engedélyeket. Ha például különböző felügyelt identitásokat használt ADLS Gen2 és ESP-fürtökhöz, akkor mindkettőnek rendelkeznie kell a "Storage blob-adat tulajdonosával" és a "HDInsight tartományi szolgáltatások közreműködői" szerepkörökkel, hogy elkerülje az ilyen problémákhoz való futást.

## <a name="faq"></a>GYIK

### <a name="what-happens-if-i-delete-the-managed-identity-after-the-cluster-creation"></a>Mi történik, ha törölem a felügyelt identitást a fürt létrehozása után?

A fürt problémákba ütközik, amikor a felügyelt identitásra van szükség. A fürt létrehozása után jelenleg nincs lehetőség felügyelt identitás frissítésére vagy módosítására. Ezért javasoljuk, hogy a fürt futtatókörnyezetében ne törölje a felügyelt identitást. Vagy újra létrehozhatja a fürtöt, és hozzárendelhet egy új felügyelt identitást.

## <a name="next-steps"></a>Következő lépések

* [Mit kell tudni az Azure-erőforrások felügyelt identitásairól?](../active-directory/managed-identities-azure-resources/overview.md)
