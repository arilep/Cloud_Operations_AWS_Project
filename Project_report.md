# S3Pipe

**Opiskelija:** Ari-Pekka Leppänen  
**Päivämäärä:** 2026-05-15  

---

## Sisällysluettelo

1. Johdanto
2. Projektisuunnitelma
   - Vaihe 1 Suunnittelu
   - Vaihe 2 Suunnittelu
3. Toteutus
   - Vaihe 1 & 2 Toteutus ja validointi
4. Parannukset
5. Yhteenveto
6. Liitteet
7. Lähteet 

---

## Johdanto

Projektin aiheeksi valitsin yksinkertaisen datan käsittelyputken (Simple data processing pipeline), joka hyödyntää AWS S3-bucketeja ja Lambda-funktiota.

Projektin taustalla oli tarve varmuuskopioida omia tärkeitä tiedostoja, koulutehtäviä ja raportteja, jotka toistaiseksi olivat vain yhden raudan varassa (eli oman pöytäkoneeni). Tiedostojen varmuuskopiointi pilveen vaikutti kustannustehokkaalta ja luotettavalta ratkaisulta. Ulkoisten levyasemien tai paikallisen RAID-konfiguraation kasaaminen voi nopeasti tulla kalliiksi, eikä silti takaa yhtä luotettavaa menetelmää varmuuskopiointiin. Lisäksi tämä projekti toimii hyvin realistisena harjoituksena, joten saadaan kaksi kärpästä yhdellä iskulla.

Projektin tavoitteet:
- AWS CLI:n asennus ja konfigurointi.
- S3 buckettien luonti, testaus ja hallinta.
- Lambda-funktion luonti, zippaus ja käyttöönotto.
- S3-event-triggerin määrittäminen Lambda-funktiota varten.
- Tiedostojen automaattinen käsittely ja kopiointi.
- Versioinnin ja Lifecycle policyn -käyttöönotto.
- Monitorointi ja hälytykset CloudWatchin ja SNSn avulla.
- Audit logit CloudTrailin avulla
- Prosessin testaus ja lopputuloksen tarkastelu.
---

## Projektisuunnitelma

### Vaihe 1 Suunnittelu

Alustava arkkitehtuuri nojaa näihin AWS:n palveluihin:
- S3 - Tiedostojen tallennus ja varmuuskopiointi (input- ja output-bucketit)
- Lambda - Tiedostojen automaattinen käsittely ja kopiointi output-bucketiin.
- IAM - Lambda tarvitsee oikeudet S3 bucketeihin, mutta projektityön ympäristössä hyödynnetään valmiiksi luotua IAM-rolea, eikä käyttäjä voi luoda uusia.

Alustava arkkitehtuuri:

<img width="933" height="556" alt="image" src="https://github.com/user-attachments/assets/f0b0268c-75dd-4ca8-a144-3ecf7791c357" />

Perustelut valinnoille:
- S3: Skaalautuva, luotettava ja henkilökohtaisessa käytössä, jossa tiedonsiirto on verrattain pientä, erittäin edullinen vaihtoehto.
- Lambda: Serverless-ratkaisu säästää kustannuksissa ja automatisoi prosesseja.
- Event trigger: Nopea ja tehokas työkalu reaaliaikaiseen tiedostojen käsittelyyn.

### Vaihe 2 Suunnittelu

Lopullinen arkkitehtuuri:

<img width="1357" height="763" alt="image" src="https://github.com/user-attachments/assets/78f040d2-5dfd-40b6-9286-dd6430f87297" />

Perustelut valinnoille:
- CloudWatch: Monitorointi, metriikat ja dashboard-näkymä.
- SNS: Vikatilanteissa ilmoitukset käyttäjän sähköpostiin.
- CloudTrail: Tietoturva ja auditointi.

---

## Toteutus

Vaiheiden 1 ja 2 Toteutus ja validointi dokumentoitiin erilliseen raporttiin.

[Linkki raporttiin](https://github.com/arilep/Cloud_Operations_AWS_Project/blob/main/Implementation.md)

## Parannukset

- Nykyisellään ympäristö ei vielä tarjoa ratkaisua erittäin pitkäaikaiseen arkistointiin, johon Glacier olisi luonteva ratkaisu.
- Lifecycle policy poistaa vanhat versiot 30 päivän jälkeen, nämä voisi mahdollisesti arkistoida Glacieriin, riippuen tarpeesta.
- Ympäristön rakentaminen onnistuu sellaisenaan CLI-komennot suorittamalla, mutta huomattavasti tehokkaampi ratkaisu olisi toteuttaa ympäristö Terraformilla taikka CloudFormationilla.
- Kustannusseurantaa varten voitaisiin hyödyntää tageja, mutta toistaiseksi näin pienillä kustannuksilla en kokenut sitä tarpeelliseksi.
- Tietoturva näkökulmasta käyttäjän monivaiheinen tunnistautuminen olisi ehdoton, myös CloudTrail log bucket tulisi suojata asettamalla siihen rajoitetut oikeudet.

---

## Yhteenveto

Mikäli ympäristö lähdettäisiin rakentamaan uudelleen, toteuttaisin sen ehdottomasti IaC -ratkaisulla (Terraform/CloudFormation). Tällöin saisi manuaalisen työn määrää tiputettua huomattavasti ja ympäristöä olisi verrattain helpompi muokata (tai rakentaa kokonaan uudelleen vaikka uudelle käyttäjälle). Ennen automatisointia koin kuitenkin hyödylliseksi harjoitukseksi lähteä kasaamaan ympäristöä palanen kerrallaan. Ongelmia ei tullut juuri vastaan, mutta ongelmanratkonnan kannalta toteuttaisin ensimmäisen harjoituksen näin. Myös AWS CLI:n käyttö tuli tietysti projektin aikana tutuksi ja sitä oli
mielestäni hyvä harjoitella, vaikkei kaikkia komentoja millään pysty ulkoa muistamaankaan.

Tietoturvan näkökulmasta vaiheen 2 askartelu oli mielenkiintoista erityisesti nyt omassa projektissa. Harjoituksissa näihin pääsi onneksi myös jonkin verran tutustumaan.

Kyseiseen arkkitehtuuriin päätyminen tehtiin ensisijaisesti kustannus näkökulmasta, sillä se oli alunperinkin suunniteltu vain henkilökohtaiseen käyttöön. Projektia olisi tietysti mahdollisuus muokata suuremmankin käyttäjäjoukon käyttöön, mikä voisi olla luonteva siirtymä mikäli projektia lähdettäisiin edelleen työstämään.

---

## Liitteet

Omia muistiinpanoja projektityötä varten: [Linkki](https://github.com/arilep/Cloud_Operations_AWS_Project/blob/main/Notes.md)

---

## Lähteet

Documentation, tutorials, or other resources used.

AWS. Accessing Amazon SNS in the AWS CLI. https://docs.aws.amazon.com/cli/latest/userguide/cli-services-sns.html

AWS. AWS Academy Cloud Operations -opintojakson esitysmateriaali aws academy -alustalla.

AWS. AWS CLI Command Reference. https://docs.aws.amazon.com/cli/latest/

AWS. Event notification types and destinations. https://docs.aws.amazon.com/AmazonS3/latest/userguide/notification-how-to-event-types-and-destinations.html#supported-notification-event-types

AWS. Setting an S3 Lifecycle configuration on a bucket. https://docs.aws.amazon.com/AmazonS3/latest/userguide/how-to-set-lifecycle-configuration-intro.html

AWS. Tutorial: Using an Amazon S3 trigger to invoke a Lambda function. https://docs.aws.amazon.com/lambda/latest/dg/with-s3-example.html

AWS. Using Amazon SQS, Amazon SNS, and Lambda. https://docs.aws.amazon.com/AmazonS3/latest/userguide/how-to-enable-disable-notification-intro.html

bluematador. AWS CLI Cheatsheet. https://www.bluematador.com/learn/aws-cli-cheatsheet

ChatGPT. Koodin ja tekstin oikoluku tekoälyllä. "Review this code for errors and suggest fixes".

Diagrams.net. Flowchart Maker and Online Diagram Software. https://app.diagrams.net/

hashicorp. InsufficientS3BucketPolicyException. https://github.com/hashicorp/terraform/issues/6388

Heinonen, J. Cloud Operations AWS -opintojakson esitysmateriaali Moodlessa. Haaga-Helia ammattikorkeakoulu.
