---
title: Ügyfél által felügyelt kulcsok konfigurálása a FHIR készült Azure API-hoz
description: A FHIR Azure API-n keresztül a saját kulcs funkcióját is támogatja a Cosmos DB
services: healthcare-apis
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.date: 09/28/2020
ms.author: ginle
ms.openlocfilehash: ae78aa80594e46b02d77adcafed961e801780d4f
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99430259"
---
# <a name="configure-customer-managed-keys-at-rest"></a>Ügyfél által felügyelt kulcsok konfigurálása nyugalmi állapotban

Amikor új Azure API-t hoz létre a FHIR-fiókhoz, a rendszer alapértelmezés szerint a Microsoft által felügyelt kulcsokkal titkosítja adatait. Most hozzáadhat egy második titkosítási réteget az adataihoz saját maga által választott és felügyelt kulcs használatával.

Az Azure-ban ez általában az ügyfél Azure Key Vault található titkosítási kulcs használatával valósítható meg. Az Azure SQL, az Azure Storage és a Cosmos DB néhány példa erre a képességre. A FHIR készült Azure API a Cosmos DB által nyújtott támogatást használja. Fiók létrehozásakor lehetősége van Azure Key Vault kulcs URI azonosítójának megadására. Ezt a kulcsot a rendszer a Cosmos DB az adatbázis-fiók üzembe helyezésekor adja át. FHIR-kérés esetén Cosmos DB lekéri a kulcsot, és a használatával titkosítja vagy visszafejti az adatait. Az első lépésekhez tekintse át az alábbi hivatkozásokat:

- [Az Azure-előfizetéshez tartozó Azure Cosmos DB erőforrás-szolgáltató regisztrálása](../cosmos-db/how-to-setup-cmk.md#register-resource-provider) 
- [Az Azure Key Vault-példány konfigurálása](../cosmos-db/how-to-setup-cmk.md#configure-your-azure-key-vault-instance)
- [Hozzáférési szabályzat hozzáadása az Azure Key Vault-példányhoz](../cosmos-db/how-to-setup-cmk.md#add-an-access-policy-to-your-azure-key-vault-instance)
- [Kulcs létrehozása Azure Key Vault](../cosmos-db/how-to-setup-cmk.md#generate-a-key-in-azure-key-vault)

## <a name="specify-the-azure-key-vault-key"></a>Azure Key Vault kulcs meghatározása

Az Azure API FHIR-Azure Portal fiókhoz való létrehozásakor a "További beállítások" lapon a "Database Settings" (adatbázis-beállítások) alatt az "adattitkosítási" konfigurációs beállítás látható. Alapértelmezés szerint a szolgáltatás által felügyelt kulcs beállítás lesz kiválasztva. 

A kulcsot a kulcstartóból választhatja ki:

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-keypicker.png" alt-text="Kiválasztó":::

Itt megadhatja a Azure Key Vault kulcsot az "ügyfél által felügyelt kulcs" lehetőség kiválasztásával. Itt megadhatja a kulcs URI-JÁT:

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-create.png" alt-text="Azure API létrehozása a FHIR-hez":::

A meglévő FHIR-fiókok esetében az alábbi módon tekintheti meg a kulcs titkosítási lehetőségeit (szolgáltatás vagy ügyfél által felügyelt kulcs) az "adatbázis" panelen. A konfigurációs beállítás kiválasztása után nem módosítható. A kulcsot azonban módosíthatja és frissítheti.

:::image type="content" source="media/bring-your-own-key/bring-your-own-key-database.png" alt-text="Adatbázis":::

Emellett a megadott kulcs új verzióját is létrehozhatja, amely után az adatok a szolgáltatás megszakítása nélkül lesznek titkosítva az új verzióval. A kulcshoz való hozzáférés eltávolításával is eltávolíthatja az adathozzáférést. Ha a kulcs le van tiltva, a lekérdezések hibát eredményeznek. Ha a kulcs újból engedélyezve van, a lekérdezések ismét sikeresek lesznek.

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta, hogyan konfigurálhatja az ügyfelek által felügyelt kulcsokat a nyugalmi állapotban. Ezután tekintse meg a Azure Cosmos DB GYIK szakaszt: 
 
>[!div class="nextstepaction"]
>[Cosmos DB: a CMK beállítása](https://docs.microsoft.com/azure/cosmos-db/how-to-setup-cmk#frequently-asked-questions)
