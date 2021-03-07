---
title: Az Amazon S3-gyűjtők vizsgálata
description: Ez a útmutató az Amazon S3-gyűjtők vizsgálatának részleteit ismerteti.
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 03/07/2021
ms.custom: references_regions
ms.openlocfilehash: ddd5c5de85da5ae8cec9d24d33dfd2bf035b5b34
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2021
ms.locfileid: "102438728"
---
# <a name="azure-purview-connector-for-amazon-s3"></a>Azure-beli hatáskörébe-összekötő az Amazon S3-hoz

Ez az útmutató ismerteti, hogyan használható az Azure hatáskörébe az Amazon S3 standard gyűjtőben jelenleg tárolt strukturálatlan adatok vizsgálatához, és megismerheti, hogy milyen típusú bizalmas információk találhatók az adatokban. Ez a útmutató ismerteti azt is, hogyan azonosíthatók az Amazon S3-gyűjtők, ahol az adatok tárolása jelenleg az egyszerű adatvédelem és az adatok megfelelősége érdekében történik.

Ehhez a szolgáltatáshoz használja a hatáskörébe egy olyan Microsoft-fiók biztosítását, amely biztonságos hozzáférést biztosít az AWS-hez, ahol a hatáskörébe tartozó képolvasó futni fog. A hatáskörébe tartozó képolvasó ezt a hozzáférést használja az Amazon S3-gyűjtők számára az adatok olvasásához, majd jelentést készít a vizsgálati eredményekről, beleértve a metaadatokat és a besorolást, vissza az Azure-ba. A hatáskörébe besorolás és a címkézési jelentések segítségével elemezheti és tekintheti át az adatvizsgálat eredményeit.

Ebben a útmutatóban megismerheti, hogyan adhat hozzá az Amazon S3-gyűjtőket a hatáskörébe tartozó erőforrásokhoz, és hogyan hozhat létre egy vizsgálatot az Amazon S3-beli adataihoz.

## <a name="purview-scope-for-amazon-s3"></a>Az Amazon S3 hatáskörébe tartozó hatókör

A következő hatókör az Amazon S3-gyűjtők és a hatáskörébe tartozó adatforrások regisztrálására és vizsgálatára vonatkozik.

|Hatókör  |Leírás  |
|---------|---------|
|**Adatkorlátok**     |    A hatáskörébe tartozó képolvasó szolgáltatás jelenleg legfeljebb 100 GB-nyi adaton támogatja az Amazon S3-gyűjtők beolvasását.     |
|**Fájltípusok**     | A hatáskörébe tartozó képolvasó szolgáltatás jelenleg a következő fájltípusokat támogatja: <br><br>. Avro,. csv,. doc,. docm,. docx,. dot,. JSON,. ODP,. ODS,. odt,. ork,. parketta,. pdf,. pot,. pps,. ppsx,. ppt,. pptm,. pptx,. xlsm,. xlsx,. xlt,. txt,. xlc,. xls,. xlsb,.,. xlsx,. xlt,. XML        |
|**Régiók**     | Az Amazon S3 szolgáltatáshoz tartozó hatáskörébe-összekötőt jelenleg csak az **AWS US East (Ohio)** és **Európa (Frankfurt)** régiókban helyezik üzembe. <br><br>További információ: [tárolási és ellenőrzési régiók](#storage-and-scanning-regions).   |
|     |         |

További információt a következő témakörben talál:

- [Erőforrások kvótáinak kezelése és növelése az Azure hatáskörébe](how-to-manage-quotas.md)
- [Támogatott adatforrások és fájltípusok az Azure hatáskörébe](sources-and-scans.md)
### <a name="storage-and-scanning-regions"></a>Tárolási és keresési régiók

Az alábbi táblázat azokat a régiókat ismerteti, amelyekben a rendszer az Azure-beli hatáskörébe tartozó régiót tárolja.

> [!IMPORTANT]
> A rendszer az összes kapcsolódó adatátviteli díjat felszámítja a gyűjtőjük régiója szerint.
>

| Tárolási régió | Ellenőrzési régió |
| ------------------------------- | ------------------------------------- |
| USA keleti régiója (Ohio)                  | USA keleti régiója (Ohio)                        |
| USA keleti régiója (N. Virginia           | USA keleti régiója (Ohio)                        |
| USA nyugati régiója (N. Kalifornia         | USA keleti régiója (Ohio)                        |
| USA nyugati régiója (Oregon)                | USA keleti régiója (Ohio)                        |
| Afrika (Cape Town)              | Európa (Frankfurt)                    |
| Ázsia és a Csendes-óceáni térség (Hongkong)        | Európa (Frankfurt)                    |
| Ázsia és a Csendes-óceáni térség (Mumbai)           | Európa (Frankfurt)                    |
| Ázsia és a Csendes-óceáni térség (Osaka-local)      | Európa (Frankfurt)                    |
| Ázsia és a Csendes-óceáni térség (Szöul)            | Európa (Frankfurt)                    |
| Ázsia és a Csendes-óceáni térség (Szingapúr)        | Európa (Frankfurt)                    |
| Ázsia és a Csendes-óceáni térség (Sydney)           | Európa (Frankfurt)                    |
| Ázsia és a Csendes-óceáni térség (Tokió)            | Európa (Frankfurt)                    |
| Kanada (Közép)                | USA keleti régiója (Ohio)                        |
| Kína (Peking)                 | Nem támogatott                    |
| Kína (Ningxia)                 | Nem támogatott                   |
| Európa (Frankfurt)              | Európa (Frankfurt)                    |
| Európa (Írország)                | Európa (Frankfurt)                    |
| Európa (London)                 | Európa (Frankfurt)                    |
| Európa (Milan)                  | Európa (Frankfurt)                    |
| Európa (Párizs)                  | Európa (Frankfurt)                    |
| Európa (Stockholm)              | Európa (Frankfurt)                    |
| Közel-Kelet (Bahrein)           | Európa (Frankfurt)                    |
| Dél-Amerika (São Paulo)       | USA keleti régiója (Ohio)                        |
| | |
## <a name="prerequisites"></a>Előfeltételek

Győződjön meg arról, hogy végrehajtotta az alábbi előfeltételeket, mielőtt az Amazon S3-gyűjtőket a hatáskörébe tartozó adatforrásként hozzáadja, és beolvassa az S3-adatait.

- Az Azure-beli adatforrások rendszergazdájának kell lennie.

- Ha a gyűjtőket a hatáskörébe tartozó erőforrásként adja hozzá, szüksége lesz az [AWS ARN](#retrieve-your-new-role-arn), a [gyűjtő neve](#retrieve-your-amazon-s3-bucket-name), és néha az [AWS-fiókja azonosítójának](#locate-your-aws-account-id)értékeire.

### <a name="create-a-purview-account"></a>Hatáskörébe tartozó fiók létrehozása

- **Ha már van egy hatáskörébe tartozó fiókja,** akkor folytathatja az AWS S3 támogatáshoz szükséges konfigurációkat. Első lépésként [hozzon létre egy hatáskörébe tartozó hitelesítő adatokat az AWS-gyűjtő vizsgálatához](#create-a-purview-credential-for-your-aws-bucket-scan).

- **Ha egy hatáskörébe tartozó fiókot kell létrehoznia,** kövesse az [Azure hatáskörébe tartozó fiók létrehozása](create-catalog-portal.md)című témakör utasításait. A fiók létrehozása után térjen vissza ide a konfigurálás befejezéséhez, és kezdje el használni a hatáskörébe-összekötőt az Amazon S3-hoz.

### <a name="create-a-purview-credential-for-your-aws-bucket-scan"></a>Az AWS-gyűjtő vizsgálatának hatáskörébe tartozó hitelesítő adatok létrehozása

Ez az eljárás azt ismerteti, hogyan hozható létre az AWS-gyűjtők vizsgálatakor használandó új, a hatáskörébe tartozó hitelesítő adatok.

> [!TIP]
> A folyamat közepén új hitelesítő adatokat is létrehozhat a [vizsgálat konfigurálásakor](#create-a-scan-for-your-amazon-s3-bucket). Ebben az esetben a **hitelesítő adatok** mezőben válassza az **új** lehetőséget.
>

1. A hatáskörébe területen navigáljon a **felügyeleti központhoz**, és a **Biztonság és hozzáférés** területen válassza a **hitelesítő adatok** lehetőséget.

1. Válassza az **új** lehetőséget, majd a jobb oldalon megjelenő **új hitelesítő adatok** ablaktáblán a következő mezők használatával hozza létre a hatáskörébe tartozó hitelesítő adatokat:

    |Mező |Leírás  |
    |---------|---------|
    |**Név**     |Írjon be egy értelmes nevet ehhez a hitelesítő adathoz, vagy használja az alapértelmezett értéket.        |
    |**Leírás**     |Adja meg a hitelesítő adat opcionális leírását, például: `Used to scan the tutorial S3 buckets`         |
    |**Hitelesítési módszer**     |Válassza az **ARN szerepkört**, mivel az ARN szerepkört használja a gyűjtőhöz való hozzáféréshez.         |
    |**Microsoft-fiók azonosítója**     |Kattintson ide az érték vágólapra másolásához. Ezt az értéket használja **Microsoft-fiók-azonosítóként** az [AWS-es szerepkör létrehozásakor](#create-a-new-aws-role-for-purview).           |
    |**Külső azonosító**     |Kattintson ide az érték vágólapra másolásához. Használja ezt az értéket **külső azonosítóként** , ha az [AWS-ben létrehozza az ARN szerepkört.](#create-a-new-aws-role-for-purview)        |
    |**ARN szerepkör**     | Miután [létrehozta az Amazon iam-szerepkört](#create-a-new-aws-role-for-purview), navigáljon a szerepkörhöz a iam területen, másolja az **ARN szerepkört** , és adja meg itt. Példa: `arn:aws:iam::284759281674:role/S3Role`. <br><br>További információkért lásd: [az új szerepkörének beolvasása](#retrieve-your-new-role-arn). |
    | | |

    Ha elkészült, válassza a **Létrehozás** lehetőséget a hitelesítő adatok létrehozásának befejezéséhez.

A hatáskörébe tartozó hitelesítő adatokkal kapcsolatos további információkért tekintse meg az Azure-beli [nyilvános előzetes verzió dokumentációját](manage-credentials.md).

### <a name="create-a-new-aws-role-for-purview"></a>Új AWS-szerepkör létrehozása a hatáskörébe

1.  Nyissa meg a **Amazon Web Services** konzolt, és a **biztonság, identitás és megfelelőség** területen válassza a **iam** lehetőséget.

1. Válassza a **szerepkörök** lehetőséget, majd **hozzon létre egy szerepkört**.

1. Válasszon **másik AWS-fiókot**, majd adja meg a következő értékeket:

    |Mező  |Leírás  |
    |---------|---------|
    |**Fiókazonosító**     |    Adja meg a Microsoft-fiókja AZONOSÍTÓját. Például: `615019938638`     |
    |**Külső azonosító**     |   A beállítások területen válassza a **külső azonosító megkövetelése...** lehetőséget, majd a kijelölt mezőben adja meg a külső azonosítót. <br>Például: `e7e2b8a3-0a9f-414f-a065-afaf4ac6d994`    <br><br>Ezt a külső azonosítót a következő esetekben találja:.  |
    | | |

    > [!NOTE]
    > A **Microsoft-fiók azonosítójának** és **külső azonosítójának** értékeit a hatáskörébe tartozó **felügyeleti központ**  >  **hitelesítő adatai** területen találja, ahol [létrehozta a hatáskörébe tartozó hitelesítő adatokat](#create-a-purview-credential-for-your-aws-bucket-scan).
    >

    Például:

    ![Adja hozzá a Microsoft-fiók AZONOSÍTÓját az AWS-fiókhoz.](./media/register-scan-amazon-s3/aws-create-role-amazon-s3.png)

1. A **szerepkör létrehozása > csatolt engedélyek házirendek** területen szűrje az **S3** számára megjelenített engedélyeket. Válassza a **AmazonS3ReadOnlyAccess** lehetőséget, majd kattintson a **Tovább gombra: címkék**.

    ![Válassza ki a ReadOnlyAccess szabályzatot az új Amazon S3-ellenőrzési szerepkörhöz.](./media/register-scan-amazon-s3/aws-permission-role-amazon-s3.png)

1. A **Címkék hozzáadása (nem kötelező)** területen dönthet úgy is, hogy értelmes címkét hoz létre ehhez az új szerepkörhöz. A hasznos címkék lehetővé teszik a létrehozott szerepkörök hozzáférésének rendszerezését, nyomon követését és szabályozását.

    Szükség szerint adjon meg egy új kulcsot és egy értéket a címkéhez. Ha elkészült, vagy ha ki szeretné hagyni ezt a lépést, kattintson a Next (tovább) gombra **: az Áttekintés** elemre kattintva tekintse át a szerepkör részleteit, és fejezze be a szerepkör létrehozását.

    ![Adjon hozzá egy értelmes címkét az új szerepkörhöz való hozzáférés rendszerezéséhez, nyomon követéséhez vagy vezérléséhez.](./media/register-scan-amazon-s3/add-tag-new-role.png)

1. A **felülvizsgálati** területen tegye a következőket:

    - A **szerepkör neve** mezőbe írjon be egy értelmes nevet a szerepkör számára.
    - A szerepkör **leírása** mezőben adjon meg egy opcionális leírást a szerepkör céljának azonosításához.
    - A **szabályzatok** szakaszban ellenőrizze, hogy a megfelelő szabályzat (**AmazonS3ReadOnlyAccess**) hozzá van-e rendelve a szerepkörhöz.

    Ezután válassza a **szerepkör létrehozása** elemet a folyamat befejezéséhez.

    Például:

    ![A szerepkör létrehozása előtt tekintse át a részleteket.](./media/register-scan-amazon-s3/review-role.png)


### <a name="configure-scanning-for-encrypted-amazon-s3-buckets"></a>Titkosított Amazon S3-gyűjtők keresésének konfigurálása

Az AWS-gyűjtők több titkosítási típust támogatnak. Az **AWS-KMS** titkosítást használó gyűjtők esetében speciális konfigurációra van szükség a vizsgálat engedélyezéséhez.

> [!NOTE]
> A titkosítást, AES-256 vagy AWS-KMS S3 titkosítást használó gyűjtők esetén ugorja át ezt a szakaszt, és folytassa az [Amazon S3-gyűjtő nevének beolvasásával](#retrieve-your-amazon-s3-bucket-name).
>

**Az Amazon S3-gyűjtőben használt titkosítás típusának ellenõrzéséhez:**

1. Az AWS-ben navigáljon a **Storage**  >  **S3** > elemre, és válassza a bal oldali menüben a **gyűjtők** lehetőséget.

    ![Válassza az Amazon S3-gyűjtők fület.](./media/register-scan-amazon-s3/check-encryption-type-buckets.png)

1. Válassza ki az áttekinteni kívánt gyűjtőt. A gyűjtő részletei lapon válassza a **Tulajdonságok** fület, és görgessen le az **alapértelmezett titkosítási** területre.

    - Ha a kiválasztott gyűjtő minden, de **AWS-KMS** titkosításra van konfigurálva, például ha a gyűjtőhöz tartozó alapértelmezett titkosítás **le van tiltva**, ugorja át az eljárás hátralévő részét, és folytassa az [Amazon S3-gyűjtő nevének beolvasásával](#retrieve-your-amazon-s3-bucket-name).

    - Ha a kiválasztott gyűjtő az **AWS-KMS** titkosításhoz van konfigurálva, folytassa az alábbi lépésekkel, és adjon hozzá egy új szabályzatot, amely lehetővé teszi a gyűjtők egyéni **AWS-KMS** titkosítással való vizsgálatát.

    Például:

    ![AWS-KMS titkosítással konfigurált Amazon S3-gyűjtő megtekintése](./media/register-scan-amazon-s3/default-encryption-buckets.png)

**Új szabályzat hozzáadásával engedélyezheti a gyűjtők egyéni AWS-KMS titkosítással történő vizsgálatát:**

1. Az AWS-ben navigáljon a **szolgáltatások**  >   **iam**  >   **házirendek** elemre, és válassza a **házirend létrehozása** lehetőséget.

1. A **házirend létrehozása**  >  **vizuális szerkesztő** lapon adja meg a szabályzatot a következő értékekkel:

    |Mező  |Leírás  |
    |---------|---------|
    |**Szolgáltatás**     |  Adja meg és válassza a **KMS** lehetőséget.       |
    |**Műveletek**     | A **hozzáférési szint** alatt válassza az **írás** elemet az **írás** szakasz kibontásához.<br>A kibontás után válassza a csak a **Visszafejtés** lehetőséget.        |
    |**Erőforrások**     |Válasszon ki egy adott erőforrást vagy **az összes erőforrást**.         |
    | | |

    Ha elkészült, válassza a **házirend áttekintése** lehetőséget a folytatáshoz.

    ![Hozzon létre egy szabályzatot a gyűjtő AWS-KMS titkosítással való vizsgálatához.](./media/register-scan-amazon-s3/create-policy-kms.png)

1. A **házirend áttekintése** lapon adjon meg egy értelmezhető nevet a szabályzatnak, és adja meg a leírását, majd válassza a **házirend létrehozása** lehetőséget.

    Az újonnan létrehozott szabályzat hozzá lesz adva a házirendek listájához.

1. Csatolja az új szabályzatot a vizsgálathoz hozzáadott szerepkörhöz.

    1. Váltson vissza a **iam**-  >  **szerepkörök** lapra, és válassza ki a [korábban](#create-a-new-aws-role-for-purview)hozzáadott szerepkört.

    1. Az **engedélyek** lapon válassza a **házirendek csatolása** lehetőséget.

        ![A szerepkör engedélyei lapon válassza a házirendek csatolása lehetőséget.](./media/register-scan-amazon-s3/iam-attach-policies.png)

    1. Az **engedélyek csatolása** lapon keresse meg és válassza ki a fent létrehozott új szabályzatot. Válassza a **házirend csatolása** lehetőséget a szabályzatnak a szerepkörhöz való csatolásához.

        Az **Összefoglalás** lap frissül, és az új szabályzat hozzá van rendelve a szerepkörhöz.

        ![A szerepkörhöz csatolt új szabályzattal megtekintheti a frissített összefoglaló lapot.](./media/register-scan-amazon-s3/attach-policy-role.png)

### <a name="retrieve-your-new-role-arn"></a>Új, az ARN szerepkör beolvasása

Rögzítenie kell az AWS-szerepkört, és át kell másolnia a hatáskörébe az [Amazon S3-gyűjtő vizsgálatának létrehozásakor](#create-a-scan-for-your-amazon-s3-bucket).

**Az ARN szerepkör beolvasása:**

1. Az AWS **identitás és hozzáférés-kezelés (iam)**  >  **szerepkörök** területen keresse meg és válassza ki a [hatáskörébe tartozó](#create-a-purview-credential-for-your-aws-bucket-scan)új szerepkört.

1. A szerepkör **összegzése** lapon válassza a másolás a **vágólapra** gombot a **role ARN** értéktől jobbra.

    ![Másolja át a szerepkör ARN értékét a vágólapra.](./media/register-scan-amazon-s3/aws-copy-role-purview.png)

1. Illessze be ezt az értéket egy biztonságos helyre, amely használatra kész az [Amazon S3-gyűjtő vizsgálatának létrehozásakor](#create-a-scan-for-your-amazon-s3-bucket).

### <a name="retrieve-your-amazon-s3-bucket-name"></a>Az Amazon S3-gyűjtő nevének lekérése

Szüksége lesz az Amazon S3-gyűjtő nevére, hogy bemásolja a hatáskörébe az [Amazon S3-gyűjtő vizsgálatának létrehozásakor](#create-a-scan-for-your-amazon-s3-bucket)

**A gyűjtő nevének lekérése:**

1. Az AWS-ben navigáljon a **Storage**  >  **S3** > elemre, és válassza a bal oldali menüben a **gyűjtők** lehetőséget.

    ![Tekintse meg az Amazon S3-gyűjtők lapot.](./media/register-scan-amazon-s3/check-encryption-type-buckets.png)

1. Keresse meg és válassza ki a gyűjtőt a gyűjtő részletei lap megtekintéséhez, majd másolja a gyűjtő nevét a vágólapra.

    Például:

    ![Az S3 gyűjtő URL-címének lekérése és másolása.](./media/register-scan-amazon-s3/retrieve-bucket-url-amazon.png)

    Illessze be a gyűjtő nevét egy biztonságos fájlba, és adjon hozzá egy `s3://` előtagot ahhoz, hogy létrehozza azt az értéket, amelyet be kell írnia, amikor a gyűjtőt hatáskörébe szeretné beállítani.

    Például: `s3://purview-tutorial-bucket`

> [!NOTE]
> A rendszer csak a gyűjtő legfelső szintjét támogatja a hatáskörébe tartozó adatforrásként. A következő URL-cím, amely tartalmaz egy almappát, *nem* támogatott: `s3://purview-tutorial-bucket/view-data`
>

### <a name="locate-your-aws-account-id"></a>Az AWS-fiók AZONOSÍTÓjának megkeresése

Az AWS-fiók AZONOSÍTÓjának szüksége lesz arra, hogy regisztrálja az AWS-fiókját a hatáskörébe tartozó adatforrásként, valamint az összes gyűjtőjét.

Az AWS-fiók azonosítója az AWS-konzolra való bejelentkezéshez használt azonosító. Azt is megteheti, hogy miután bejelentkezett a IAM-irányítópulton, a navigációs beállítások alatt a bal oldalon, a bejelentkezési URL-cím numerikus részeként pedig:

Például:

![Az AWS-fiók AZONOSÍTÓjának beolvasása.](./media/register-scan-amazon-s3/aws-locate-account-id.png)


## <a name="add-a-single-amazon-s3-bucket-as-a-purview-resource"></a>Egyetlen Amazon S3-gyűjtő hozzáadása a hatáskörébe-erőforrásként

Akkor használja ezt az eljárást, ha csak egyetlen S3 gyűjtővel szeretne regisztrálni a hatáskörébe, mint adatforrásként, vagy ha több gyűjtője van az AWS-fiókban, de nem kívánja regisztrálni az összeset a hatáskörébe.

1. Indítsa el a hatáskörébe portált az Amazon S3 URL-címéhez tartozó dedikált hatáskörébe-összekötő használatával. Ezt az URL-címet az Amazon S3 hatáskörébe-összekötő termék-felügyeleti csapata adta meg.

    ![Indítsa el a hatáskörébe portált.](./media/register-scan-amazon-s3/purview-portal-amazon-s3.png)

1. Navigáljon az Azure hatáskörébe tartozó **források** oldalra, és **válassza a regisztráció regisztrálása** ![ ikont.](./media/register-scan-amazon-s3/register-button.png) > **Amazon S3**  >  **Folytatás**.

    ![Vegyen fel egy Amazon AWS-gyűjtőt a hatáskörébe tartozó adatforrásként.](./media/register-scan-amazon-s3/add-s3-datasource-to-purview.png)

    > [!TIP]
    > Ha több [gyűjteménye](manage-data-sources.md#manage-collections) van, és hozzá szeretné adni az Amazon S3-t egy adott gyűjteményhez, kattintson a jobb felső sarokban található **Térkép nézetre** , majd **válassza a regisztráció regisztrálása** ![ ikont.](./media/register-scan-amazon-s3/register-button.png) gomb a gyűjteményen belül.
    >

1. A megnyíló **Register sources (Amazon S3)** panelen adja meg a következő adatokat:

    |Mező  |Leírás  |
    |---------|---------|
    |**Név**     |Írjon be egy értelmes nevet, vagy használja a megadott alapértelmezett értéket.         |
    |**Gyűjtő URL-címe**     | Adja meg az AWS-gyűjtő URL-címét a következő szintaxis használatával:   `s3://<bucketName>`     <br><br>**Megjegyzés**: Ügyeljen arra, hogy csak a gyűjtő legfelső szintjét használja almappák nélkül. További információ: [az Amazon S3-gyűjtő nevének beolvasása](#retrieve-your-amazon-s3-bucket-name). |
    |**Gyűjtemény kiválasztása** |Ha az adatforrások gyűjteményből való regisztrálását választotta, akkor a gyűjtemény már fel van sorolva. <br><br>Szükség **szerint válasszon** egy másik gyűjteményt, és ne rendeljen hozzá gyűjteményt, vagy hozzon létre **új** gyűjteményt. <br><br>További információ a hatáskörébe-gyűjteményekről: [adatforrások kezelése az Azure hatáskörébe](manage-data-sources.md#manage-collections).|
    | | |

    Ha elkészült, válassza a **Befejezés** lehetőséget a regisztráció befejezéséhez.

Folytassa a [vizsgálat létrehozását az Amazon S3-gyűjtőhöz.](#create-a-scan-for-your-amazon-s3-bucket)

## <a name="add-all-of-your-amazon-s3-buckets-as-purview-resources"></a>Az összes Amazon S3-gyűjtő felvétele a hatáskörébe tartozó erőforrásként

Akkor használja ezt az eljárást, ha több S3 gyűjtője van az Amazon-fiókjában, és szeretné regisztrálni az összes, a hatáskörébe tartozó adatforrást.

1. Indítsa el a hatáskörébe portált az Amazon S3 URL-címéhez tartozó dedikált hatáskörébe-összekötő használatával. Ezt az URL-címet az Amazon S3 hatáskörébe-összekötő termék-felügyeleti csapata adta meg.

    ![Összekötő elindítása az Amazon S3 dedikált hatáskörébe portál](./media/register-scan-amazon-s3/purview-portal-amazon-s3.png)

1. Navigáljon az Azure hatáskörébe tartozó **források** oldalra, és **válassza a regisztráció regisztrálása** ![ ikont.](./media/register-scan-amazon-s3/register-button.png) > **Amazon-fiókok**  >  **Folytatás**.

    ![Vegyen fel egy Amazon-fiókot a hatáskörébe adatforrásként.](./media/register-scan-amazon-s3/add-s3-account-to-purview.png)

    > [!TIP]
    > Ha több [gyűjteménye](manage-data-sources.md#manage-collections) van, és hozzá szeretné adni az Amazon S3-t egy adott gyűjteményhez, kattintson a jobb felső sarokban található **Térkép nézetre** , majd **válassza a regisztráció regisztrálása** ![ ikont.](./media/register-scan-amazon-s3/register-button.png) gomb a gyűjteményen belül.
    >

1. A megnyíló **Register sources (Amazon S3)** panelen adja meg a következő adatokat:

    |Mező  |Leírás  |
    |---------|---------|
    |**Név**     |Írjon be egy értelmes nevet, vagy használja a megadott alapértelmezett értéket.         |
    |**AWS-fiók azonosítója**     | Adja meg az AWS-fiók AZONOSÍTÓját. További információ: [az AWS-fiók azonosítójának megkeresése](#locate-your-aws-account-id)|
    |**Gyűjtemény kiválasztása** |Ha az adatforrások gyűjteményből való regisztrálását választotta, akkor a gyűjtemény már fel van sorolva. <br><br>Szükség **szerint válasszon** egy másik gyűjteményt, és ne rendeljen hozzá gyűjteményt, vagy hozzon létre **új** gyűjteményt. <br><br>További információ a hatáskörébe-gyűjteményekről: [adatforrások kezelése az Azure hatáskörébe](manage-data-sources.md#manage-collections).|
    | | |

    Ha elkészült, válassza a **Befejezés** lehetőséget a regisztráció befejezéséhez.

Folytassa az [Amazon S3-gyűjtő vizsgálatának létrehozásával](#create-a-scan-for-your-amazon-s3-bucket).

## <a name="create-a-scan-for-your-amazon-s3-bucket"></a>Az Amazon S3-gyűjtő vizsgálatának létrehozása

Ha a gyűjtőket a hatáskörébe tartozó adatforrásként adta hozzá, beállíthatja, hogy a vizsgálat ütemezett időközönként vagy azonnal fusson.

1. Keresse meg az Azure hatáskörébe tartozó **források** területét, majd tegye a következők egyikét:

    - A **Térkép nézetben** válassza az **új** vizsgálat ![ új vizsgálat ikont.](./media/register-scan-amazon-s3/new-scan-button.png) az adatforrás mezőben.
    - A **listanézet** lapon vigye az egérmutatót az adatforrás sorára, **és válassza az új vizsgálat** új ![ vizsgálat ikont. ](./media/register-scan-amazon-s3/new-scan-button.png)

1. A jobb oldalon megnyíló **vizsgálat...** ablaktáblán adja meg a következő mezőket, majd kattintson a **Folytatás** gombra:

    |Mező  |Leírás  |
    |---------|---------|
    |**Név**     |  Írjon be egy értelmes nevet a vizsgálathoz, vagy használja az alapértelmezett értéket.       |
    |**Típus** |Csak akkor jelenik meg, ha az AWS-fiókját felvette az összes gyűjtőbe. <br><br>Az aktuális beállítások csak **az összes**  >  **Amazon S3**-t tartalmazzák. Ha további lehetőségeket szeretne kiválasztani, válassza a hatáskörébe tartozó támogatási mátrix kibontása lehetőséget. |
    |**Hitelesítőadat**     |  Válassza ki a hatáskörébe tartozó hitelesítő adatokat az ARN szerepkörrel. <br><br>**Tipp**: Ha most új hitelesítő adatokat szeretne létrehozni, válassza az **új** lehetőséget. További információ: [a hatáskörébe tartozó hitelesítő adatok létrehozása az AWS-gyűjtők vizsgálatához](#create-a-purview-credential-for-your-aws-bucket-scan).     |
    |     |         |

    A hatáskörébe automatikusan ellenőrzi, hogy az ARN szerepkör érvényes-e, és hogy a gyűjtő és az objektum a gyűjtőn belül elérhető-e, majd folytatja a sikeres kapcsolatok sikerességét.

    > [!TIP]
    > Ha más értékeket szeretne megadni, és a folytatás előtt tesztelni szeretné a kapcsolódást, válassza a jobb alsó sarokban található **Kapcsolódás tesztelése** lehetőséget a **Folytatás** elem kiválasztásához.
    >

1. A **vizsgálati szabálykészlet kiválasztása** ablaktáblán válassza ki az alapértelmezett **AmazonS3** , vagy az új egyéni szabálykészlet létrehozásához válassza az **új bevizsgálati szabály** beállítást. Miután kiválasztotta a szabályt, válassza a **Folytatás** lehetőséget.

    Ha új egyéni ellenőrzési szabálykészlet létrehozását választja, a varázslóval adja meg a következő beállításokat:

    |Ablaktábla  |Description  |
    |---------|---------|
    |**Új ellenőrzési szabály beállítva** /<br>**Vizsgálati szabály leírása**    |   Adjon meg egy értelmes nevet és egy opcionális leírást a szabálykészlet számára      |
    |**Fájltípusok kiválasztása**     | Válassza ki az összes olyan fájltípust, amelyet fel szeretne venni a vizsgálatba, majd válassza a **Folytatás** lehetőséget.<br><br>Új fájltípus hozzáadásához válassza az **új fájltípus** lehetőséget, és adja meg a következőket: <br>– A hozzáadni kívánt fájlkiterjesztés <br>– Opcionális Leírás  <br>– Azt határozza meg, hogy a fájl tartalma rendelkezik-e egyéni elválasztóval vagy rendszerfájl-típussal. Ezután adja meg az egyéni határolójelet, vagy válassza ki a rendszerfájl típusát. <br><br>Válassza a **Létrehozás** lehetőséget az egyéni fájltípus létrehozásához.     |
    |**Besorolási szabályok kiválasztása**     |   Navigáljon a elemre, és válassza ki az adatkészleten futtatni kívánt besorolási szabályokat.      |
    |     |         |

    Válassza a **Létrehozás** lehetőséget, amikor elkészült a szabálykészlet létrehozásához.

1. A **vizsgálati trigger beállítása** panelen válasszon egyet az alábbiak közül, majd válassza a **Folytatás** lehetőséget:

    - **Ismétlődő** vizsgálatra ütemezett ütemterv konfigurálása
    - **Egyszer** egy azonnal elinduló vizsgálat konfigurálásához

1. A **vizsgálat áttekintése** panelen tekintse meg a vizsgálat részleteit, és ellenőrizze, hogy helyesek-e, majd válassza a **Mentés** vagy **Mentés és Futtatás** lehetőséget, ha az előző ablaktáblán **egyszer** van bejelölve.

    > [!NOTE]
    > Az indítás után a vizsgálat akár 24 óráig is elvégezhető. Áttekintheti a **betekintési jelentéseket** , és az egyes ellenőrzések elkezdése után 24 órával keresheti meg a katalógusban.
    >

További információ: a [hatáskörébe tartozó keresési eredmények megismerése](#explore-purview-scanning-results).

## <a name="explore-purview-scanning-results"></a>A hatáskörébe tartozó keresési eredmények megismerése

Miután az Amazon S3-gyűjtőn befejeződik a hatáskörébe való vizsgálat, megtekintheti a hatáskörébe tartozó **források** területen a vizsgálati előzményeket.

Válasszon ki egy adatforrást a részletek megtekintéséhez, majd válassza a **vizsgálatok** lapot a jelenleg futó vagy befejezett vizsgálatok megtekintéséhez.
Ha több gyűjtővel rendelkező AWS-fiókot adott hozzá, az egyes gyűjtők vizsgálati előzményei a fiók alatt jelennek meg.

Például:

![Az AWS S3 gyűjtő által megkeresett ellenőrzések megjelenítése az AWS-fiók forrásaként.](./media/register-scan-amazon-s3/account-scan-history.png)

A hatáskörébe tartozó többi területen megtudhatja, hogy az adatközpontban található tartalomról milyen részletek találhatók, beleértve az Amazon S3-gyűjtőket:

- **Keresse meg a hatáskörébe tartozó adatkatalógust,** és szűrje egy adott gyűjtőt. Például:

    ![Keresse meg az AWS S3-eszközök katalógusát.](./media/register-scan-amazon-s3/search-catalog-screen-aws.png)

- **Tekintse** meg az áttekintési jelentéseket a besorolási, érzékenységi címkék, fájltípusok és a tartalom részletes adatainak megtekintéséhez.

    Az összes hatáskörébe betekintési jelentés tartalmazza az Amazon S3 keresési eredményeit, valamint az Azure-adatforrások további eredményeit. Ha szükséges, egy további **Amazon S3** típusú eszköz lett hozzáadva a jelentés szűrési beállításaihoz.

    További információ: az [Azure hatáskörébe tartozó információk megismerése](concept-insights.md).

## <a name="next-steps"></a>Következő lépések

További információ az Azure-beli hatáskörébe Insight-jelentésekről:

> [!div class="nextstepaction"]
> [Elemzések értelmezése az Azure Purview-ban](concept-insights.md)
