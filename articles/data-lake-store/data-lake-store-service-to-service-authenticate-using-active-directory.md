---
title: Szolgáltatások közötti hitelesítés – Data Lake Storage Gen1 – Azure
description: Ismerje meg, hogyan valósítható meg a szolgáltatások közötti hitelesítés a Azure Data Lake Storage Gen1 Azure Active Directory használatával.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 9dc195f98310e63cbde06885effe86ea3c239249
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "91576098"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-azure-active-directory"></a>Szolgáltatások közötti hitelesítés Azure Data Lake Storage Gen1 használatával Azure Active Directory
> [!div class="op_single_selector"]
> * [Végfelhasználói hitelesítés](data-lake-store-end-user-authenticate-using-active-directory.md)
> * [Szolgáltatások közötti hitelesítés](data-lake-store-service-to-service-authenticate-using-active-directory.md)
> 
>  

A Azure Data Lake Storage Gen1 Azure Active Directory használ a hitelesítéshez. Az Data Lake Storage Gen1-vel működő alkalmazások készítése előtt el kell döntenie, hogyan hitelesítheti az alkalmazást Azure Active Directory (Azure AD) használatával. A két fő lehetőség közül választhat:

* Végfelhasználói hitelesítés 
* Szolgáltatások közötti hitelesítés (ez a cikk) 

Mindkét lehetőség eredményeként az alkalmazás egy OAuth 2,0-tokenrel van ellátva, amely a Data Lake Storage Gen1hoz kapcsolódó minden kérelemhez csatolva lesz.

Ez a cikk bemutatja, hogyan hozhat létre egy **Azure ad-webalkalmazást a szolgáltatások közötti hitelesítéshez**. A végfelhasználói hitelesítéshez használható Azure AD-alkalmazás konfigurálásával kapcsolatos utasításokért lásd: [végfelhasználói hitelesítés a Data Lake Storage Gen1 Azure Active Directory használatával](data-lake-store-end-user-authenticate-using-active-directory.md).

## <a name="prerequisites"></a>Előfeltételek
* Azure-előfizetés. Lásd: [Ingyenes Azure-fiók létrehozása](https://azure.microsoft.com/pricing/free-trial/).

## <a name="step-1-create-an-active-directory-web-application"></a>1. lépés: Active Directory Webalkalmazás létrehozása

Hozzon létre és konfiguráljon egy Azure AD-webalkalmazást a szolgáltatások közötti hitelesítéshez a Azure Data Lake Storage Gen1 segítségével Azure Active Directory használatával. Útmutatásért lásd: [Azure ad-alkalmazás létrehozása](../active-directory/develop/howto-create-service-principal-portal.md).

Az előző hivatkozás utasításait követve válassza ki a webalkalmazás **/API** az alkalmazás típusaként lehetőséget, ahogyan az a következő képernyőképen látható:

![Webalkalmazás létrehozása](./media/data-lake-store-authenticate-using-active-directory/azure-active-directory-create-web-app.png "Webalkalmazás létrehozása")

## <a name="step-2-get-application-id-authentication-key-and-tenant-id"></a>2. lépés: az alkalmazás-azonosító, a hitelesítési kulcs és a bérlő AZONOSÍTÓjának beolvasása
Ha programozott módon jelentkezik be, szüksége lesz az alkalmazás AZONOSÍTÓJÁRA. Ha az alkalmazás a saját hitelesítő adatai alatt fut, akkor is szüksége lesz egy hitelesítési kulcsra.

* Az alkalmazás AZONOSÍTÓjának és hitelesítési kulcsának (más néven az ügyfél titkos kulcsa) beolvasására vonatkozó utasításokért lásd az [alkalmazás-azonosító és a hitelesítési kulcs beszerzése](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)című témakört.

* A bérlői azonosító lekérésével kapcsolatos utasításokért lásd: [bérlő azonosítójának](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)beolvasása.

## <a name="step-3-assign-the-azure-ad-application-to-the-azure-data-lake-storage-gen1-account-file-or-folder"></a>3. lépés: az Azure AD-alkalmazás kiosztása a Azure Data Lake Storage Gen1 fiók fájljához vagy mappájához


1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). Nyissa meg azt a Data Lake Storage Gen1 fiókot, amelyet a korábban létrehozott Azure Active Directory alkalmazáshoz szeretne rendelni.
2. A Data Lake Storage Gen1-fiók paneljén kattintson az **adatkezelő** elemre.
   
    ![Könyvtárak létrehozása Data Lake Storage Gen1 fiókban](./media/data-lake-store-authenticate-using-active-directory/adl.start.data.explorer.png "Könyvtárak létrehozása Data Lake fiókban")
3. A **adatkezelő** panelen kattintson arra a fájlra vagy mappára, amelyhez hozzáférést szeretne biztosítani az Azure ad-alkalmazáshoz, majd kattintson a **hozzáférés** elemre. Egy fájlhoz való hozzáférés konfigurálásához a **fájl előnézetének** paneljéről kell kattintania a **hozzáférés** lehetőségre.
   
    ![ACL-ek beállítása Data Lake fájlrendszerben](./media/data-lake-store-authenticate-using-active-directory/adl.acl.1.png "ACL-ek beállítása Data Lake fájlrendszerben")
4. A **hozzáférés** panel felsorolja a gyökérhez már hozzárendelt normál hozzáférést és egyéni hozzáférést. Az egyéni szintű ACL-ek hozzáadásához kattintson a **Hozzáadás** ikonra.
   
    ![Szabványos és egyéni hozzáférés listázása](./media/data-lake-store-authenticate-using-active-directory/adl.acl.2.png "Szabványos és egyéni hozzáférés listázása")
5. Kattintson a **Hozzáadás** ikonra az **Egyéni hozzáférés hozzáadása** panel megnyitásához. Ezen a panelen kattintson a **felhasználó vagy csoport kiválasztása** elemre, majd a **felhasználó vagy csoport kiválasztása** panelen keresse meg a korábban létrehozott Azure Active Directory alkalmazást. Ha sok csoporttal szeretne keresni, használja a felső szövegmezőt a csoport nevének szűréséhez. Kattintson a hozzáadni kívánt csoportra, majd a **kiválasztás** elemre.
   
    ![Csoport hozzáadása](./media/data-lake-store-authenticate-using-active-directory/adl.acl.3.png "Csoport hozzáadása")
6. Kattintson az **engedélyek kiválasztása** elemre, jelölje ki az engedélyeket, valamint azt, hogy az engedélyeket alapértelmezett ACL-ként, hozzáférési ACL-ként vagy mindkettőként kívánja-e hozzárendelni. Kattintson az **OK** gombra.
   
    ![Képernyőkép az egyéni hozzáférés hozzáadása panelről, ahol az engedélyek kiválasztása lehetőség ki van választva, a Select permissions (engedélyek kiválasztása) panelen pedig az OK lehetőség.](./media/data-lake-store-authenticate-using-active-directory/adl.acl.4.png "Engedélyek kiosztása a csoportnak")
   
    A Data Lake Storage Gen1 engedélyeivel és az alapértelmezett/hozzáférési ACL-ekkel kapcsolatos további információkért lásd: [Access Control Data Lake Storage Gen1](data-lake-store-access-control.md).
7. Az **Egyéni hozzáférés hozzáadása** panelen kattintson az **OK** gombra. A társított engedélyekkel rendelkező újonnan hozzáadott csoportok a **hozzáférés** panelen jelennek meg.
   
    ![Képernyőfelvétel a hozzáférés panelről az újonnan hozzáadott csoporttal az egyéni hozzáférés szakaszban.](./media/data-lake-store-authenticate-using-active-directory/adl.acl.5.png "Engedélyek kiosztása a csoportnak")

> [!NOTE]
> Ha azt tervezi, hogy a Azure Active Directory alkalmazást egy adott mappába korlátozza, ugyanezt az Azure Active Directory-alkalmazás **végrehajtási** engedélyének kell megadnia a gyökérkönyvtárhoz, hogy engedélyezze a fájl-létrehozási hozzáférést a .net SDK-n keresztül.

> [!NOTE]
> Ha az SDK-kat szeretné használni egy Data Lake Storage Gen1-fiók létrehozásához, akkor az Azure AD-webalkalmazást szerepkörként kell hozzárendelni ahhoz az erőforráscsoporthoz, amelyben létrehozta a Data Lake Storage Gen1 fiókot.
> 
>

## <a name="step-4-get-the-oauth-20-token-endpoint-only-for-java-based-applications"></a>4. lépés: a OAuth 2,0 token végpontjának lekérése (csak Java-alapú alkalmazásokhoz)

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és kattintson a bal oldali ablaktábla Active Directory elemére.

2. A bal oldali ablaktáblán kattintson a **Alkalmazásregisztrációk** elemre.

3. A Alkalmazásregisztrációk panel felső részén kattintson a **végpontok** elemre.

    ![Képernyőkép a Active Directoryről a Alkalmazásregisztrációk kapcsolóval és a végpontok lehetőséggel.](./media/data-lake-store-authenticate-using-active-directory/oauth-token-endpoint.png "OAuth jogkivonat-végpont")

4. A végpontok listájából másolja a OAuth 2,0 jogkivonat-végpontot.

    ![Képernyőkép a végpontok panelről, az O AUTH 2 pont O JOGKIVONAT-VÉGPONT másolási ikonja.](./media/data-lake-store-authenticate-using-active-directory/oauth-token-endpoint-1.png "OAuth jogkivonat-végpont")   

## <a name="next-steps"></a>Következő lépések
Ebben a cikkben egy Azure AD-webalkalmazást hozott létre, és összegyűjtötte a szükséges információkat a .NET SDK, a Java, a Python, a REST API stb. használatával létrehozott ügyfélalkalmazások számára. A következő cikkekkel megtudhatja, hogyan használhatja az Azure AD natív alkalmazást az első hitelesítéshez Data Lake Storage Gen1, majd más műveleteket hajthat végre az áruházban.

* [Szolgáltatások közötti hitelesítés a Data Lake Storage Gen1 Java használatával](data-lake-store-service-to-service-authenticate-java.md)
* [Szolgáltatások közötti hitelesítés a Data Lake Storage Gen1 .NET SDK használatával](data-lake-store-service-to-service-authenticate-net-sdk.md)
* [Szolgáltatások közötti hitelesítés Data Lake Storage Gen1 a Python használatával](data-lake-store-service-to-service-authenticate-python.md)
* [Szolgáltatások közötti hitelesítés Data Lake Storage Gen1 használatával REST API](data-lake-store-service-to-service-authenticate-rest-api.md)


