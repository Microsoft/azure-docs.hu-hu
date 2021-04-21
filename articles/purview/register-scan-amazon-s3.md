---
title: Amazon S3-gyűjtők beolvasása
description: Ez az útmutató az Amazon S3-gyűjtők vizsgálatának részleteit ismerteti.
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 04/21/2021
ms.custom: references_regions
ms.openlocfilehash: 75a7cba1e47509e3186ab519d0d8ca82dd315373
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107815521"
---
# <a name="azure-purview-connector-for-amazon-s3"></a>Azure Purview-összekötő Amazon S3-hoz

Ez az útmutató bemutatja, hogyan használható az Azure Purview az Amazon S3 standard gyűjtőkben tárolt strukturálatlan adatok vizsgálatára, és az adatokban található bizalmas adatok típusának felderítésére. Ez az útmutató azt is ismerteti, hogyan azonosíthatók azok az Amazon S3-gyűjtők, amelyekben az adatok jelenleg tárolva vannak az egyszerű adatvédelem és az adat megfelelőség érdekében.

Ehhez a szolgáltatáshoz a Purview használatával biztosítson biztonságos Microsoft-fiók hozzáférést az AWS-hez, ahol a Purview scanner futni fog. A Purview Scanner ezt a hozzáférést használja az Amazon S3-gyűjtőkben az adatok beolvasására, majd jelenti a vizsgálati eredményeket, csak a metaadatokat és a besorolást az Azure-ba. A Purview besorolási és címkézési jelentésekkel elemezheti és áttekintheti az adatvizsgálat eredményeit.

Ebből az útmutatóból megtudhatja, hogyan adhat hozzá Amazon S3-gyűjtőket Purview-erőforrásként, és hogyan hozhat létre vizsgálatot az Amazon S3-adatokhoz.

## <a name="purview-scope-for-amazon-s3"></a>Az Amazon S3 hatókörének véglegesnézete

Az alábbi hatókör az Amazon S3-gyűjtők regisztrálására és vizsgálatára vonatkozik Purview adatforrásként.

|Hatókör  |Leírás  |
|---------|---------|
|**Adatkorlátok**     |    A Purview Scanner szolgáltatás jelenleg támogatja az Amazon S3-gyűjtők bérlőnként legfeljebb 100 GB adat keresését.     |
|**Fájltípusok**     | A Purview Scanner szolgáltatás jelenleg a következő fájltípusokat támogatja: <br><br>.avro, .csv, .doc, .docm, .docx, .dot, .json, .odp, .ods, .odt, .orc, .parquet, .pdf, .pot, .pps, .ppsx, .ppt, .pptm, .pptx, .psv, .ssv, .tsv, .txt, .xlc, .xls, .xlsb, .xlsm, .xlsx, .xlt, .xml        |
|**Régiók**     | Az Amazon S3 szolgáltatás Purview-összekötője jelenleg csak az AWS USA keleti **régiójában (Ohio)** és Európában **(Fog)** van telepítve. <br><br>További információ: [Tárolási és ellenőrzési régiók.](#storage-and-scanning-regions)   |
|     |         |

További információért tekintse meg a dokumentumokban található Nézetkorlátok:

- [Erőforrások kvótáinak kezelése és növelése az Azure Purview használatával](how-to-manage-quotas.md)
- [Az Azure Purview által támogatott adatforrások és fájltípusok](sources-and-scans.md)
- [Privát végpontok használata a Purview-fiókhoz](catalog-private-link.md)
### <a name="storage-and-scanning-regions"></a>Tárolási és ellenőrzési régiók

Az alábbi táblázat leképezi az adatokat tároló régiókat arra a régióra, ahol az Azure Purview beolvassa az adatokat.

> [!IMPORTANT]
> Az ügyfelek a gyűjtőjük régiójának megfelelően díjat számítunk fel az összes kapcsolódó adatátviteli díjért.
>

| Tárolási régió | Beolvasási régió |
| ------------------------------- | ------------------------------------- |
| USA keleti régiója (Ohio)                  | USA keleti régiója (Ohio)                        |
| USA keleti régiója (N. Virginia)           | USA keleti régiója (N. Virginia)                       |
| USA nyugati régiója (N. California)         | USA keleti régiója (Ohio)                        |
| USA nyugati régiója (Oregon)                | USA keleti régiója (Ohio)                        |
| Afrika (Fokváros)              | Európa (Közép-Európa)                    |
| Ázsia és a Csendes-óceáni térség (Hongkong)        | Ázsia és a Csendes-óceáni térség (Sydney)                   |
| Ázsia és a Csendes-óceáni térség (Mumbai)           | Ázsia és a Csendes-óceáni térség (Sydney)                   |
| Ázsia és a Csendes-óceáni térség (Oszaka-Local)      | Ázsia és a Csendes-óceáni térség (Sydney)                   |
| Ázsia és a Csendes-óceáni térség (Szöul)            | Ázsia és a Csendes-óceáni térség (Sydney)                   |
| Ázsia és a Csendes-óceáni térség (Szingapúr)        | Ázsia és a Csendes-óceáni térség (Sydney)                   |
| Ázsia és a Csendes-óceáni térség (Sydney)           | Ázsia és a Csendes-óceáni térség (Sydney)                  |
| Ázsia és a Csendes-óceáni térség (Tokió)            | Ázsia és a Csendes-óceáni térség (Sydney)                 |
| Kanada (Közép-Kanada)                | USA keleti régiója (Ohio)                        |
| Kína (Kína)                 | Nem támogatott                    |
| Kína (Ningxi)                 | Nem támogatott                   |
| Európa (Közép-Európa)              | Európa (Közép-Európa)                    |
| Európa (Írország)                | Európa (Írország)                   |
| Európa (London)                 | Európa (Írország)                   |
| Európa (Közép-Európa)                  | Európa (Közép-Európa)                    |
| Európa (Párizs)                  | Európa (Közép-Európa)                    |
| Európa (Közép-Európa)              | Európa (Közép-Európa)                    |
| Közel-Kelet (Bahrain)           | Európa (Közép-Európa)                    |
| Dél-Amerika (Sâo Paulo)       | USA keleti régiója (Ohio)                        |
| | |

## <a name="prerequisites"></a>Előfeltételek

Győződjön meg arról, hogy végrehajtotta az alábbi előfeltételeket, mielőtt Amazon S3-gyűjtőket ad hozzá Purview adatforrásként, és beolvassa az S3-adatokat.

> [!div class="checklist"]
> * Az Azure Purview adatforrás rendszergazdának kell lennie.
> * [Hozzon létre egy Purview-fiókot,](#create-a-purview-account) ha még nincs fiókja
> * [A Purview hitelesítő adat létrehozása az AWS-gyűjtő vizsgálathoz](#create-a-purview-credential-for-your-aws-bucket-scan)
> * [Új AWS-szerepkör létrehozása a Végleges nézethez](#create-a-new-aws-role-for-purview)
> * [Titkosított Amazon S3-gyűjtők keresésének konfigurálása,](#configure-scanning-for-encrypted-amazon-s3-buckets)ha szükséges
> * Ha a gyűjtőket Purview-erőforrásként adja hozzá, szüksége lesz az [AWS ARN](#retrieve-your-new-role-arn)értékeire, [](#retrieve-your-amazon-s3-bucket-name)a gyűjtő nevére és néha az [AWS-fiók azonosítójára.](#locate-your-aws-account-id)

### <a name="create-a-purview-account"></a>Purview-fiók létrehozása

- **Ha már rendelkezik Purview-fiókkal,** folytathatja az AWS S3-támogatáshoz szükséges konfigurációkat. Kezdje a [Create a Purview credential for your AWS bucket scan (A Purview hitelesítő adatok létrehozása az AWS-gyűjtő vizsgálathoz) gombra.](#create-a-purview-credential-for-your-aws-bucket-scan)

- **Ha létre kell hoznia egy Purview-fiókot,** kövesse az Azure Purview-fiókpéldány létrehozása [útmutatását.](create-catalog-portal.md) A fiók létrehozása után térjen vissza ide a konfigurálás befejezéséhez, és kezdje el használni az Amazon S3-hoz használható Purview-összekötőt.

### <a name="create-a-purview-credential-for-your-aws-bucket-scan"></a>A Purview hitelesítő adat létrehozása az AWS-gyűjtő vizsgálathoz

Ez az eljárás bemutatja, hogyan hozhat létre új Purview hitelesítő adatokat az AWS-gyűjtők vizsgálatakor.

> [!TIP]
> A vizsgálat konfigurálása közben is létrehozhat egy új hitelesítő [adatokat a folyamat közepén.](#create-a-scan-for-one-or-more-amazon-s3-buckets) Ebben az esetben a Hitelesítő **adatok mezőben** válassza az Új **lehetőséget.**
>

1. A Purview nézetben lépjen a **Felügyeleti központra,** majd a Biztonság **és** hozzáférés alatt válassza a **Hitelesítő adatok lehetőséget.**

1. Válassza **a New (Új)** lehetőséget, majd a jobb oldalon megjelenő New **credential** (Új hitelesítő adat) panelen használja a következő mezőket a Purview hitelesítő adatok létrehozásához:

    |Mező |Leírás  |
    |---------|---------|
    |**Név**     |Adjon kifejező nevet a hitelesítő adatnak, vagy használja az alapértelmezettet.        |
    |**Leírás**     |Adjon meg egy nem kötelező leírást a hitelesítő adathoz, például: `Used to scan the tutorial S3 buckets`         |
    |**Hitelesítési módszer**     |Válassza **a Role ARN (Szerepkör ARN-e)** lehetőséget, mivel az ARN szerepkörrel fér hozzá a gyűjtőhöz.         |
    |**Microsoft-fiók azonosítója**     |Kattintson ide az érték vágólapra másoláshoz. Használja ezt az értéket Microsoft-fiók **ARN-nek** az [AWS-hez való létrehozásakor.](#create-a-new-aws-role-for-purview)           |
    |**Külső azonosító**     |Kattintson ide az érték vágólapra másoláshoz. Ezt az értéket használja **külső azonosítóként,** amikor [létrehozza a szerepkör ARN-ját az AWS-ban.](#create-a-new-aws-role-for-purview)        |
    |**Szerepkör ARN-e**     | Miután létrehozta az [Amazon IAM-szerepkört,](#create-a-new-aws-role-for-purview)navigáljon a szerepkörhöz az IAM területen, másolja ki a Szerepkör **ARN-értékét,** és adja meg itt. Példa: `arn:aws:iam::284759281674:role/S3Role`. <br><br>További információ: [Az új szerepkör ARN-ének lekérése.](#retrieve-your-new-role-arn) |
    | | |

    Ha **végzett,** válassza a Létrehozás lehetőséget a hitelesítő adatok létrehozásának befejezéséhez.

1. Ha még nem, másolja és illessze be a **Microsoft-fiók ID** és a External **ID** értékeket, amelyeket a következő lépésként a Purview új [AWS-szerepkörének](#create-a-new-aws-role-for-purview)létrehozásakor használhat.

További információ a purview hitelesítő adatokról: Credentials for source authentication in Azure Purview (Hitelesítő adatok forráshitelesítéshez az [Azure Purview-ban).](manage-credentials.md)

### <a name="create-a-new-aws-role-for-purview"></a>Új AWS-szerepkör létrehozása a Nézethez

Ehhez az eljáráshoz meg kell adnia az Azure-fiókazonosító és a külső azonosító értékét az AWS-szerepkör létrehozásakor.

Ha nem találja ezeket az értékeket, először keresse meg őket a [Purview hitelesítő adatai között.](#create-a-purview-credential-for-your-aws-bucket-scan)

**A Microsoft-fiókazonosító és a külső azonosító megkeresi:**

1. A Purview nézetben lépjen a **Felügyeleti központ biztonsági és** hozzáférési hitelesítő  >    >  **adataihoz.**

1. Válassza ki az [AWS-gyűjtő](#create-a-purview-credential-for-your-aws-bucket-scan)vizsgálathoz létrehozott hitelesítő adatokat, majd az eszköztáron válassza a **Szerkesztés lehetőséget.**

1. A jobb **oldalon** megjelenő Hitelesítő adatok szerkesztése panelen másolja a **Microsoft-fiók ID** és a **External ID** (Külső azonosító) értékeket egy külön fájlba, vagy tegye őket kézbe az AWS megfelelő mezőjébe való beillesztéshez.

    Például:

    [![Keresse meg a Microsoft-fiók azonosítót és a Külső azonosító értékeket. ](./media/register-scan-amazon-s3/locate-account-id-external-id.png)](./media/register-scan-amazon-s3/locate-account-id-external-id.png#lightbox)


**Az AWS-szerepkör létrehozása a Purview számára:**

1.  Nyissa meg **Amazon Web Services** konzolját, és a **Biztonság, Identitás és Megfelelőség** alatt válassza az **IAM lehetőséget.**

1. Válassza **a Szerepkörök,** majd a **Szerepkör létrehozása lehetőséget.**

1. Válassza **az Another AWS account (Másik AWS-fiók)** lehetőséget, majd adja meg a következő értékeket:

    |Mező  |Leírás  |
    |---------|---------|
    |**Fiókazonosító**     |    Adja meg a Microsoft-fiók azonosítóját. Például: `615019938638`     |
    |**Külső azonosító**     |   A beállítások alatt válassza a Külső azonosító **szükséges...** lehetőséget, majd írja be a külső azonosítóját a kijelölt mezőbe. <br>Például: `e7e2b8a3-0a9f-414f-a065-afaf4ac6d994`     |
    | | |

    Például:

    ![Adja hozzá a Microsoft-fiók azonosítóját az AWS-fiókjához.](./media/register-scan-amazon-s3/aws-create-role-amazon-s3.png)

1. A **Create role > Attach permissions policies (Engedélyek** csatolása szabályzatok csatolása) területen szűrje az S3-ra a megjelenített **engedélyeket.** Válassza **az AmazonS3ReadOnlyAccess** lehetőséget, majd a **Tovább: Címkék lehetőséget.**

    ![Válassza ki a ReadOnlyAccess szabályzatot az új Amazon S3 vizsgálat szerepkörhöz.](./media/register-scan-amazon-s3/aws-permission-role-amazon-s3.png)

    > [!IMPORTANT]
    > Az **AmazonS3ReadOnlyAccess** szabályzat biztosítja az S3-gyűjtők vizsgálatához szükséges minimális engedélyeket, és más engedélyeket is tartalmazhat.
    >
    >Ha csak a gyűjtők vizsgálatához szükséges minimális engedélyeket szeretné alkalmazni, hozzon létre egy új szabályzatot az [AWS-szabályzat](#minimum-permissions-for-your-aws-policy)minimális engedélyei között felsorolt engedélyekkel attól függően, hogy egyetlen gyűjtőt vagy a fiókjában található összes gyűjtőt szeretné-e átvizsgálni. 
    >
    >Alkalmazza az új szabályzatot a szerepkörre az **AmazonS3ReadOnlyAccess helyett.**

1. A **Címkék hozzáadása (nem kötelező) területen** igény szerint létrehozhat egy jelentéssel bíró címkét ehhez az új szerepkörhöz. A hasznos címkékkel rendszerezheti, nyomon követheti és szabályozhatja a hozzáférést minden létrehozott szerepkörhöz.

    Szükség szerint adjon meg egy új kulcsot és értéket a címkéhez. Ha végzett, vagy ki szeretné hagyni ezt a lépést, válassza a **Tovább:** Áttekintés lehetőséget a szerepkör részleteinek áttekintéshez és a szerepkör létrehozásának befejezéséhez.

    ![Adjon hozzá egy jelentéssel bíró címkét az új szerepkör rendszerezése, nyomon követése és hozzáférésének szabályozása érdekében.](./media/register-scan-amazon-s3/add-tag-new-role.png)

1. A Felülvizsgálat **területen** tegye a következőket:

    - A Szerepkör **neve mezőben** adjon kifejező nevet a szerepkörnek
    - A Szerepkör **leírása mezőben** adjon meg egy leírást (nem kötelező) a szerepkör céljának azonosításához
    - A **Házirendek szakaszban** ellenőrizze, hogy a megfelelő szabályzat **(AmazonS3ReadOnlyAccess)** van-e csatolva a szerepkörhöz.

    Ezután válassza **a Szerepkör létrehozása lehetőséget** a folyamat befejezéséhez.

    Például:

    ![Tekintse át a részleteket a szerepkör létrehozása előtt.](./media/register-scan-amazon-s3/review-role.png)


### <a name="configure-scanning-for-encrypted-amazon-s3-buckets"></a>Titkosított Amazon S3-gyűjtők keresésének konfigurálása

Az AWS-gyűjtők több titkosítási típust támogatnak. **AWS-KMS-titkosítást** használó gyűjtők esetén speciális konfiguráció szükséges a vizsgálat engedélyezéséhez.

> [!NOTE]
> A titkosítás nélküli gyűjtők, AES-256- vagy AWS-KMS S3-titkosítások használata után hagyja ki ezt a szakaszt, és folytassa az Amazon S3-gyűjtő nevének [lekérésével.](#retrieve-your-amazon-s3-bucket-name)
>

**Az Amazon S3-gyűjtőkben használt titkosítás típusának ellenőrzése:**

1. Az AWS-ban lépjen a **Storage** S3 >, és válassza a Bal oldali menü  >   Gyűjtők  parancsát.

    ![Válassza az Amazon S3 Buckets (Amazon S3-gyűjtők) lapot.](./media/register-scan-amazon-s3/check-encryption-type-buckets.png)

1. Válassza ki az ellenőrizni kívánt gyűjtőt. A gyűjtő részletek lapján válassza  a Tulajdonságok lapot, és görgessen le az **Alapértelmezett titkosítás területre.**

    - Ha a kiválasztott gyűjtő az **AWS-KMS** titkosításon túl bármire konfigurálva van, beleértve azt is, ha a gyűjtő alapértelmezett titkosítása **Le** van tiltva, hagyja ki az eljárás további részét, és folytassa az [Amazon S3-gyűjtő](#retrieve-your-amazon-s3-bucket-name)nevének lekérésével.

    - Ha a kiválasztott gyűjtő AWS-KMS-titkosításra van konfigurálva, a folytatáshoz adjon hozzá egy új házirendet, amely lehetővé teszi a gyűjtők egyéni **AWS-KMS-titkosítással** való vizsgálatát. 

    Például:

    ![AWS-KMS-titkosítással konfigurált Amazon S3-gyűjtő megtekintése](./media/register-scan-amazon-s3/default-encryption-buckets.png)

**Új szabályzat hozzáadása, amely lehetővé teszi egy gyűjtő egyéni AWS-KMS-titkosítással történő vizsgálatát:**

1. Az AWS-ban lépjen a **Szolgáltatások**  >   **IAM-házirendek**  >   **lapra, és** válassza a Házirend létrehozása **lehetőséget.**

1. A Szabályzat **létrehozása Vizualizációszerkesztő**  >  **lapon** adja meg a szabályzatot a következő értékekkel:

    |Mező  |Leírás  |
    |---------|---------|
    |**Szolgáltatás**     |  Írja be és válassza a **KMS lehetőséget.**       |
    |**Műveletek**     | A **Hozzáférési szint területen** válassza az Írás **lehetőséget** az Írás **szakasz kibontásához.**<br>A kibontás után csak a **Visszafejtés lehetőséget válassza.**        |
    |**Erőforrások**     |Válasszon ki egy adott erőforrást vagy a **Minden erőforrás lehetőséget.**         |
    | | |

    Ha végzett, válassza a Szabályzat **áttekintése lehetőséget** a folytatáshoz.

    ![Szabályzat létrehozása egy gyűjtő AWS-KMS-titkosítással történő vizsgálathoz.](./media/register-scan-amazon-s3/create-policy-kms.png)

1. A Szabályzat **áttekintése lapon** adjon meg egy kifejező nevet a szabályzatnak, és ha szükséges, egy leírást, majd válassza a Szabályzat **létrehozása lehetőséget.**

    Az újonnan létrehozott szabályzat megjelenik a szabályzatok listájában.

1. Csatolja az új szabályzatot a vizsgálathoz hozzáadott szerepkörhöz.

    1. Lépjen vissza az **IAM-szerepkörök**  >  **lapra,** és válassza ki a korábban hozzáadott [szerepkört.](#create-a-new-aws-role-for-purview)

    1. Az Engedélyek **lapon** válassza a **Szabályzatok csatolása lehetőséget.**

        ![A szerepkör Engedélyek lapján válassza a Szabályzatok csatolása lehetőséget.](./media/register-scan-amazon-s3/iam-attach-policies.png)

    1. Az Engedélyek **csatolása lapon** keresse meg és válassza ki a fent létrehozott új szabályzatot. Válassza **a Szabályzat csatolása** lehetőséget, hogy a szabályzatot a szerepkörhöz csatolja.

        Frissül **az** Összefoglalás lap, és az új szabályzat hozzá van csatolva a szerepkörhöz.

        ![Tekintsen meg egy frissített Összegzés oldalt, amely a szerepkörhöz csatolt új szabályzatot tárolja.](./media/register-scan-amazon-s3/attach-policy-role.png)

### <a name="retrieve-your-new-role-arn"></a>Az új szerepkör ARN-ének lekérése

Az Amazon S3-gyűjtő vizsgálatának létrehozásakor rögzítenie kell az AWS-szerepkör ARN-ját, és be kell másolnia [a Nézetbe.](#create-a-scan-for-one-or-more-amazon-s3-buckets)

**A szerepkör ARN-ének lekérése:**

1. Az AWS **Identitás- és hozzáférés-kezelés (IAM)** szerepkörei területen keresse meg és válassza ki a Nézethez létrehozott új  >   [szerepkört.](#create-a-purview-credential-for-your-aws-bucket-scan)

1. A szerepkör Összegzés **lapján** válassza  a Másolás a vágólapra gombot a Szerepkör **ARN-értéke** jobb oldalán.

    ![Másolja a szerepkör ARN-értékét a vágólapra.](./media/register-scan-amazon-s3/aws-copy-role-purview.png)

1. Illessze be ezt az értéket egy biztonságos helyre, amely készen áll [az Amazon S3-gyűjtő vizsgálatának létrehozására.](#create-a-scan-for-one-or-more-amazon-s3-buckets)

### <a name="retrieve-your-amazon-s3-bucket-name"></a>Az Amazon S3-gyűjtő nevének lekérése

Az Amazon S3-gyűjtő vizsgálatának létrehozásakor szüksége lesz az Amazon S3-gyűjtő nevére, hogy bemásolódnia a [Nézetbe](#create-a-scan-for-one-or-more-amazon-s3-buckets)

**A gyűjtő nevének lekérése:**

1. Az AWS-ban lépjen a **Storage** S3 >, és válassza a Bal oldali menü  >   Gyűjtők  parancsát.

    ![Tekintse meg az Amazon S3 Buckets (Amazon S3-gyűjtők) lapot.](./media/register-scan-amazon-s3/check-encryption-type-buckets.png)

1. Keresse meg és válassza ki a gyűjtőt a gyűjtő részletei lap megtekintéséhez, majd másolja a gyűjtő nevét a vágólapra.

    Például:

    ![Olvassa be és másolja ki az S3-gyűjtő URL-címét.](./media/register-scan-amazon-s3/retrieve-bucket-url-amazon.png)

    Illessze be a gyűjtő nevét egy biztonságos fájlba, és adjon hozzá egy előtagot, hogy létrehozza azt az értéket, amit a gyűjtő Purview erőforrásként való konfigurálásakor meg `s3://` kell adnia.

    Például: `s3://purview-tutorial-bucket`

> [!NOTE]
> Csak a gyűjtő gyökérszintje támogatott Purview adatforrásként. Például a következő URL-cím, amely tartalmaz egy almappát, *nem támogatott:*`s3://purview-tutorial-bucket/view-data`
>

### <a name="locate-your-aws-account-id"></a>Keresse meg az AWS-fiók azonosítóját

Az AWS-fiók azonosítójára azért van szükség, hogy az összes gyűjtővel együtt a Purview adatforrásként regisztrálja az AWS-fiókot.

Az AWS-fiók azonosítója az AWS-konzolra való bejelentkezéshez használt azonosító. Miután bejelentkezett az IAM-irányítópultra, a navigációs lehetőségek bal oldalára, felül pedig a bejelentkezési URL-cím numerikus részeként is megkeresheti:

Például:

![Az AWS-fiók azonosítójának lekérése.](./media/register-scan-amazon-s3/aws-locate-account-id.png)


## <a name="add-a-single-amazon-s3-bucket-as-a-purview-resource"></a>Egyetlen Amazon S3-gyűjtő hozzáadása Purview-erőforrásként

Akkor használja ezt az eljárást, ha csak egyetlen S3-gyűjtőt szeretne regisztrálni a Purview-ra adatforrásként, vagy ha több gyűjtő van az AWS-fiókjában, de nem szeretné mindegyiket regisztrálni a Purview-ban.

**A gyűjtő hozzáadása:** 

1. Indítsa el a Purview portált az Amazon S3 URL-címhez tartozó dedikált Purview-összekötővel. Ezt az URL-címet az Amazon S3 Purview összekötő termékkezelő csapata biztosította Önnek.

    ![Indítsa el a Purview portált.](./media/register-scan-amazon-s3/purview-portal-amazon-s3.png)

1. Lépjen az Azure **Purview-források oldalra,** és válassza a **Regisztráció** ![ ikont.](./media/register-scan-amazon-s3/register-button.png) > **Amazon S3**  >  **Folytatás:**.

    ![Adjon hozzá egy Amazon AWS-gyűjtőt a Purview adatforrásként.](./media/register-scan-amazon-s3/add-s3-datasource-to-purview.png)

    > [!TIP]
    > Ha több [](manage-data-sources.md#manage-collections) gyűjteménye van, és hozzá szeretné adni az Amazon  S3-at egy adott gyűjteményhez, válassza a jobb felső sarokban található Térkép nézetet, majd kattintson a **Regisztráció** ![ ikonra.](./media/register-scan-amazon-s3/register-button.png) gombra kattintva.
    >

1. A **megnyíló Források regisztrálása (Amazon S3)** panelen adja meg a következő adatokat:

    |Mező  |Leírás  |
    |---------|---------|
    |**Név**     |Adjon meg egy kifejező nevet, vagy használja a megadott alapértelmezett nevet.         |
    |**Gyűjtő URL-címe**     | Adja meg az AWS-gyűjtő URL-címét a következő szintaxissal:   `s3://<bucketName>`     <br><br>**Megjegyzés:** Ügyeljen arra, hogy csak a gyűjtő gyökérszintje legyen használva, almappák nélkül. További információ: [Az Amazon S3-gyűjtő nevének lekérése.](#retrieve-your-amazon-s3-bucket-name) |
    |**Gyűjtemény kiválasztása** |Ha az adatforrás gyűjteményen belüli regisztrálását választotta, akkor az a gyűjtemény már szerepel a listán. <br><br>Szükség szerint válasszon egy másik gyűjteményt, **a Nincs** lehetőséget, ha nem szeretne gyűjteményt hozzárendelni, vagy az **Új** lehetőséget egy új gyűjtemény létrehozásához. <br><br>A purview gyűjteményekkel kapcsolatos további információkért lásd: Manage data sources in Azure Purview (Adatforrások kezelése az [Azure Purview-ban).](manage-data-sources.md#manage-collections)|
    | | |

    Ha elkészült, kattintson a Befejezés **gombra** a regisztráció befejezéséhez.

Folytassa a Vizsgálat létrehozása egy vagy több [Amazon S3-gyűjtővel.](#create-a-scan-for-one-or-more-amazon-s3-buckets).

## <a name="add-an-amazon-account-as-a-purview-resource"></a>Amazon-fiók hozzáadása Purview-erőforrásként

Akkor használja ezt az eljárást, ha több S3-gyűjtő van az Amazon-fiókjában, és mindegyiket végleges adatforrásként szeretné regisztrálni.

A [vizsgálat konfigurálásakor](#create-a-scan-for-one-or-more-amazon-s3-buckets)kiválaszthatja a vizsgálni kívánt gyűjtőket, ha nem szeretné az összeset együtt átvizsgálni.

**Az Amazon-fiók hozzáadása:**
1. Indítsa el a Purview portált az Amazon S3 URL-címhez tartozó dedikált Purview-összekötővel. Ezt az URL-címet az Amazon S3 Purview összekötő termékkezelő csapata biztosította Önnek.

    ![Összekötő indítása az Amazon S3 dedikált Purview portálhoz](./media/register-scan-amazon-s3/purview-portal-amazon-s3.png)

1. Lépjen az Azure **Purview-források oldalra,** és válassza a **Regisztráció** ![ ikont.](./media/register-scan-amazon-s3/register-button.png) > **Amazon-fiókok**  >  **Folytatás:**.

    ![Adjon hozzá egy Amazon-fiókot Purview adatforrásként.](./media/register-scan-amazon-s3/add-s3-account-to-purview.png)

    > [!TIP]
    > Ha több [](manage-data-sources.md#manage-collections) gyűjteménye van, és hozzá szeretné adni az Amazon  S3-at egy adott gyűjteményhez, válassza a jobb felső sarokban található Térkép nézetet, majd válassza a **Regisztráció regisztrálása** ![ ikont.](./media/register-scan-amazon-s3/register-button.png) gombra kattintva.
    >

1. A **megnyíló Források regisztrálása (Amazon S3)** panelen adja meg a következő adatokat:

    |Mező  |Leírás  |
    |---------|---------|
    |**Név**     |Adjon meg egy kifejező nevet, vagy használja a megadott alapértelmezett nevet.         |
    |**AWS-fiók azonosítója**     | Adja meg az AWS-fiók azonosítóját. További információ: [Az AWS-fiók azonosítójának megkerese](#locate-your-aws-account-id)|
    |**Gyűjtemény kiválasztása** |Ha az adatforrás gyűjteményen belüli regisztrálását választotta, akkor az a gyűjtemény már szerepel a listán. <br><br>Szükség szerint válasszon egy másik gyűjteményt,  **a Nincs** lehetőséget a gyűjtemény hozzárendeléséhez, vagy az Új lehetőséget egy új gyűjtemény létrehozásához. <br><br>A Purview-gyűjteményekkel kapcsolatos további információkért lásd: [Adatforrások kezelése az Azure Purview-ban.](manage-data-sources.md#manage-collections)|
    | | |

    Ha elkészült, válassza a Befejezés **lehetőséget** a regisztráció befejezéséhez.

Folytassa a Create a scan for one or more Amazon S3 buckets (Vizsgálat létrehozása egy vagy több [Amazon S3-gyűjtőre) gombra.](#create-a-scan-for-one-or-more-amazon-s3-buckets)

## <a name="create-a-scan-for-one-or-more-amazon-s3-buckets"></a>Egy vagy több Amazon S3-gyűjtő vizsgálatának létrehozása

Miután hozzáadta a gyűjtőket a Purview adatforrásként, konfigurálhat egy vizsgálatot úgy, hogy ütemezett időközönként vagy azonnal fusson.

1. Lépjen az Azure Purview **Sources területre,** és tegye a következők egyikét:

    - A Térkép **nézetben válassza** az **Új vizsgálat Új vizsgálat** ![ ikont.](./media/register-scan-amazon-s3/new-scan-button.png) az adatforrás mezőben.
    - A Lista **nézetben vigye** az egérmutatót az adatforrás sorára, és válassza az Új **vizsgálat Új vizsgálat** ![ ikon. ](./media/register-scan-amazon-s3/new-scan-button.png) .

1. A jobb **oldalon megnyíló Vizsgálat...** panelen adja meg a következő mezőket, majd válassza a Folytatás **lehetőséget:**

    |Mező  |Leírás  |
    |---------|---------|
    |**Név**     |  Adjon kifejező nevet a vizsgálatnak, vagy használja az alapértelmezettet.       |
    |**Típus** |Csak akkor jelenik meg, ha az összes gyűjtővel együtt hozzáadta az AWS-fiókot. <br><br>A jelenlegi lehetőségek között csak **az All**  >  **Amazon S3 szerepel.** A Purview támogatási mátrixának kibontása után további lehetőségeket is választhat. |
    |**Megbízólevél**     |  Válasszon ki egy Hitelesítő adatok véglegesnézete lehetőséget a szerepkör ARN-jével. <br><br>**Tipp:** Ha jelenleg új hitelesítő adatokat szeretne létrehozni, válassza az Új **lehetőséget.** További információ: Create a Purview credential for your AWS bucket scan (A Purview hitelesítő adatok létrehozása [az AWS-gyűjtővizsgálathoz).](#create-a-purview-credential-for-your-aws-bucket-scan)     |
    | **Amazon S3**    |   Csak akkor jelenik meg, ha hozzáadta az AWS-fiókját, az összes gyűjtővel együtt. <br><br>Jelöljön ki egy vagy több  gyűjtőt a vizsgálathoz, vagy válassza az Összes kijelölése lehetőséget a fiók összes gyűjtőjének vizsgálathoz.      |
    | | |

    A Purview automatikusan ellenőrzi, hogy a szerepkör ARN-e érvényes, és hogy a gyűjtőkben lévő gyűjtők és objektumok elérhetők-e, majd folytatódik, ha a kapcsolat sikeresen létrejön.

    > [!TIP]
    > Ha különböző értékeket ad meg,  és a folytatás előtt teszteli a kapcsolatot, válassza a kapcsolat tesztelése lehetőséget a jobb alsó sarokban, mielőtt a **Folytatás lehetőséget választná.**
    >

1. A Vizsgálati **szabálykészlet** kiválasztása panelen válassza ki az **AmazonS3** alapértelmezett szabálykészletét, vagy válassza az Új vizsgálati szabálykészlet lehetőséget egy új egyéni szabálykészlet létrehozásához.  Miután kiválasztotta a szabálykészletet, válassza a Folytatás **lehetőséget.**

    Ha új egyéni vizsgálati szabálykészletet hoz létre, a varázslóval adja meg a következő beállításokat:

    |Ablaktábla  |Description  |
    |---------|---------|
    |**Új vizsgálati szabálykészlet** /<br>**Vizsgálati szabály leírása**    |   Adjon meg egy kifejező nevet és egy leírást (nem kötelező) a szabálykészlethez      |
    |**Fájltípusok kiválasztása**     | Válassza ki a vizsgálatba foglalni kívánt fájltípusokat, majd válassza a Folytatás **lehetőséget.**<br><br>Új fájltípus hozzáadásához válassza az Új **fájltípus** lehetőséget, és határozza meg a következőket: <br>– A hozzáadni kívánt fájlkiterjesztés <br>– Nem kötelező leírás  <br>– Azt határozza meg, hogy a fájl tartalmának egyéni elválasztójele van-e, vagy rendszerfájltípus. Ezután adja meg az egyéni elválasztó karaktert, vagy válassza ki a rendszerfájl típusát. <br><br>Válassza **a Létrehozás** lehetőséget az egyéni fájltípus létrehozásához.     |
    |**Besorolási szabályok kiválasztása**     |   Keresse meg és válassza ki az adatkészleten futtatni kívánt besorolási szabályokat.      |
    |     |         |

    Ha **végzett,** válassza a Létrehozás lehetőséget a szabálykészlet létrehozásához.

1. A Vizsgálat **eseményindító beállítása panelen** válassza ki a következők egyikét, majd válassza a Folytatás **lehetőséget:**

    - **Ismétlődő** egy ismétlődő vizsgálat ütemezésének konfiguráláshoz
    - **Egy** azonnal elinduló vizsgálat konfigurálása

1. A Vizsgálat áttekintése **panelen** ellenőrizze a vizsgálat részleteit, és ellenőrizze,  hogy helyesek-e,  majd válassza a Mentés vagy a Mentés **és** futtatás lehetőséget, ha az Előző panelen az Egyszer lehetőséget választotta.

    > [!NOTE]
    > Az első lépések után a vizsgálat akár 24 órát is igénybe vehet. Az egyes vizsgálatok elkezdését követően 24 órával áttekinti az **Insight-jelentéseket,** és kereshet a katalógusban.
    >

További információ: [Vizsgálati vizsgálati eredmények vizsgálata.](#explore-purview-scanning-results)

## <a name="explore-purview-scanning-results"></a>A Purview vizsgálati eredményeinek megismerése

Miután befejeződött a Purview-vizsgálat az Amazon S3-gyűjtőkön, a Vizsgálati források területen részletezve megtekintheti a vizsgálati előzményeket. 

Válasszon ki egy adatforrást a részleteinek  megtekintéséhez, majd válassza a Vizsgálatok lapot az aktuálisan futó vagy befejezett vizsgálatok megtekintéséhez.
Ha több gyűjtővel rendelkező AWS-fiókot adott hozzá, az egyes gyűjtők vizsgálati előzményei a fiók alatt jelennek meg.

Például:

![Az AWS S3-gyűjtővizsgálatok megjelenítése az AWS-fiók forrásában.](./media/register-scan-amazon-s3/account-scan-history.png)

A Purview egyéb területeit használva további részleteket is megtudat az adatterület tartalmáról, beleértve az Amazon S3-gyűjtőket is:

- **Keresse meg a Purview adatkatalógust, és** szűrje egy adott gyűjtőt. Például:

    ![Keresse meg a katalógusban az AWS S3-eszközöket.](./media/register-scan-amazon-s3/search-catalog-screen-aws.png)

- **Az Insight-jelentések** megtekintése a besorolás, a bizalmasság címkék, a fájltípusok és a tartalom további részleteinek megtekintéséhez.

    Minden Purview Insight-jelentés tartalmazza az Amazon S3 vizsgálati eredményeit, valamint az Azure-adatforrások többi eredményét. Ha szükséges, egy további **Amazon S3-eszköztípus** lett hozzáadva a jelentés szűrési beállításaihoz.

    További információkért lásd: Az Azure Purview insights (Elemzések az [Azure Purview-ban)](concept-insights.md).

## <a name="minimum-permissions-for-your-aws-policy"></a>Az AWS-szabályzat minimális engedélyei

Az S3-gyűjtők vizsgálatakor a [Purview-hoz](#create-a-new-aws-role-for-purview) használható AWS-szerepkör létrehozásának alapértelmezett eljárása az **AmazonS3ReadOnlyAccess házirendet** használja.

Az **AmazonS3ReadOnlyAccess** szabályzat biztosítja az S3-gyűjtők vizsgálatához szükséges minimális engedélyeket, és más engedélyeket is tartalmazhat.

Ha csak a gyűjtők vizsgálatához szükséges minimális engedélyeket szeretné alkalmazni, hozzon létre egy új szabályzatot a következő szakaszokban felsorolt engedélyekkel, attól függően, hogy egyetlen gyűjtőt vagy a fiókban található összes gyűjtőt szeretné-e átvizsgálni.

Alkalmazza az új szabályzatot a szerepkörre az **AmazonS3ReadOnlyAccess helyett.**

### <a name="individual-buckets"></a>Egyéni gyűjtők

Az egyes S3-gyűjtők vizsgálatakor a minimális AWS-engedélyek a következők:

- `GetBucketLocation`
- `GetBucketPublicAccessBlock`
- `GetObject`
- `ListBucket`

Ügyeljen arra, hogy az erőforrást az adott gyűjtőnévvel határozza meg. Például:

```json
{
"Version": "2012-10-17",
"Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetBucketLocation",
                "s3:GetBucketPublicAccessBlock",
                "s3:GetObject",
                "s3:ListBucket"
            ],
            "Resource": "arn:aws:s3:::<bucketname>"
        },
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetObject"
            ],
            "Resource": "arn:aws:s3::: <bucketname>/*"
        }
    ]
}
```

### <a name="all-buckets-in-your-account"></a>A fiókban minden gyűjtő

Az AWS-fiókban az összes gyűjtő vizsgálatakor a minimális AWS-engedélyek a következők:

- `GetBucketLocation`
- `GetBucketPublicAccessBlock`
- `GetObject`
- `ListAllMyBuckets`
- `ListBucket`.

Ügyeljen arra, hogy az erőforrást helyettesítő karakter használatával határozza meg. Például:

```json
{
"Version": "2012-10-17",
"Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetBucketLocation",
                "s3:GetBucketPublicAccessBlock",
                "s3:GetObject",
                "s3:ListAllMyBuckets",
                "s3:ListBucket"
            ],
            "Resource": "*"
        },
        {
            "Effect": "Allow",
            "Action": [
                "s3:GetObject"
            ],
            "Resource": "*"
        }
    ]
}
```

## <a name="next-steps"></a>Következő lépések

További információ az Azure Purview Insight-jelentésekről:

> [!div class="nextstepaction"]
> [Elemzések értelmezése az Azure Purview-ban](concept-insights.md)
