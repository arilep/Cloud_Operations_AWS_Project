# Esivalmistelut

Kurssin Laboratorio ja Activity -harjoitukset olin tähän mennessä tehnyt Windows-koneella, joten päätin harjoituksen vuoksi työstää projektitehtävän alusta loppuun Linux -käyttöjärjestelmällä.

<img width="453" height="273" alt="image" src="https://github.com/user-attachments/assets/0824ab22-a264-44f7-8021-a54e54902723" />

## Pakettienhallinnan päivitys

Aloitin työskentelyn päivittämällä pakettivarastot:

    sudo apt update

<img width="586" height="216" alt="image" src="https://github.com/user-attachments/assets/5a14a5da-fd13-4163-87a8-56ea020a9fa2" />

## Python version tarkistus

Pythonia tarvitaan useammassakin vaiheessa, joten tarkistin että se on asennettuna:

    python3 --version

<img width="233" height="98" alt="image" src="https://github.com/user-attachments/assets/e8e4c918-4ed2-4bc5-bcd4-59d2b9eef3fb" />

## Unzip työkalun tarkistus

Pakattuja hakemistoja varten käytössä unzip-työkalu. Työkalu oli valmiiksi asennettuna koneelleni:

    unzip -v

<img width="647" height="131" alt="image" src="https://github.com/user-attachments/assets/798f7390-e472-4f7e-a385-92b7b0fb396a" />

## AWS CLI asennus

Ladataan pakattu hakemisto aws:n sivuilta. Puretaan paketti ja asennetaan AWS CLI:

    curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
    unzip awscliv2.zip
    sudo ./aws/install

<img width="758" height="132" alt="image" src="https://github.com/user-attachments/assets/7a524c91-010a-48c5-8cc3-c37eb066c2ae" />

<img width="653" height="129" alt="image" src="https://github.com/user-attachments/assets/f480ddce-8a00-4f36-837a-d4ba89b330ea" />

# AWS konfiguraatio

## Aws kirjautumistiedot

Access key, secret access key ja session token -kirjautumistiedot hain aws academy -sivustolta Learner Lab osiosta:

AWS Details > Cloud Access > AWS CLI

<img width="512" height="482" alt="image" src="https://github.com/user-attachments/assets/55c7d3a2-f625-41b9-bb5d-f53a02292989" />

Kirjautumistiedot voi joko tallentaa suoraan polkuun ~/.aws/credentials tai syöttää manuaalisesti, kuten itse tein.

## AWS CLI konfigurointi

Konfiguroidaan AWS CLI, annetaan access key, secret access key ja session token. Regionina toimii labraympäristön oletus us-east-1 ja oletus output formaatiksi valikoitui json:

    aws configure

<img width="1086" height="181" alt="image" src="https://github.com/user-attachments/assets/6fcd68ff-0c57-4dea-95e1-df8d04c9ecab" />



## AWS CLI konfiguraation tarkistus

Komennolla `aws configure list` voidaan vielä silmäillä konfiguraatiota:

    aws configure list

<img width="602" height="141" alt="image" src="https://github.com/user-attachments/assets/8e3ce66e-b205-422c-b0ef-f8d708a4481e" />




# S3 Bucketit

## Bucketien luominen

Luodaan projektityötä varten kaksi erillistä bucketia, input-bucket (leppanen-input) ja output-bucket (leppanen-output). Komennolla `aws s3 ls` voidaan vielä listata ja tarkistaa bucketit:

    aws s3api create-bucket --bucket leppanen-input --region us-east-1
    aws s3api create-bucket --bucket leppanen-output --region us-east-1
    aws s3 ls

<img width="677" height="255" alt="image" src="https://github.com/user-attachments/assets/dbadfd7e-b605-46aa-93e1-ec1600de3412" />

## AWS Management Console -näkymä

Tarkistin vielä että löydän bucketit myös AWS Management Consolen kautta.

<img width="1048" height="348" alt="image" src="https://github.com/user-attachments/assets/5f055b0e-4881-4408-9dc9-ed41ef03900e" />

# Bucketien testaus

## Testitiedostot

Testasin että tiedostojen lataus bucketeihin onnistuu:

    echo "Hello input bucket!" > testinput.txt
    echo "Hello output bucket!" > testoutput.txt

    aws s3 cp testinput.txt s3://leppanen-input
    aws s3 cp testoutput.txt s3://leppanen-output


<img width="529" height="134" alt="image" src="https://github.com/user-attachments/assets/54360a94-5ee7-4a9c-8606-942ed048024d" />

## Bucketien sisällön tarkistus

Vielä vilkaisu, että tiedostot löytyvät kummastakin bucketista:

    aws s3 ls
    aws s3 ls s3://leppanen-input
    aws s3 ls s3://leppanen-output

<img width="418" height="163" alt="image" src="https://github.com/user-attachments/assets/feb6f35f-fbaf-4159-bf79-98ccea02f710" />

## Bucketien sisältö management consolessa

Vielä Management Consolen kautta vilkaisu bucketeihin, että tiedostot löytyvät.

<img width="1558" height="310" alt="image" src="https://github.com/user-attachments/assets/c376d169-9fd4-4d21-9e5c-c7f9e2351bf4" />

<img width="1560" height="314" alt="image" src="https://github.com/user-attachments/assets/800f2565-7b9b-443f-b5f0-4b84c63cd970" />

## Poistetaan testitiedostot ja katsotaan että ls-komennot palauttavat tyhjät promptit

Tiedostot palvelivat tehtävänsä, tyhjensin bucketit ennen seuraavaan vaiheeseen etenemistä:

    aws s3 rm s3://leppanen-input/testinput.txt
    aws s3 rm s3://leppanen-output/testoutput.txt
    aws s3 ls s3://leppanen-input
    aws s3 ls s3://leppanen-output
    
<img width="501" height="146" alt="image" src="https://github.com/user-attachments/assets/cb0580a2-bd6d-4813-a6e8-89e19ac0ed68" />

# AWS Lambda

## Lambda funktio

Luodaan ensin tyhjä tiedosto, johon funktio tallennetaan:

    touch lambda_function.py

lambda_function.py -tiedostoon Lambda-funktio, joka käsittelee eventin ja kopioi tiedoston output-bucketiin:

```
import boto3
from datetime import datetime
import os


s3 = boto3.client('s3')


OUTPUT_BUCKET = 'leppanen-output'

def lambda_handler(event, context):
    for record in event['Records']:
        bucket = record['s3']['bucket']['name']
        key = record['s3']['object']['key']
        
        timestamp = datetime.now().strftime("%Y%m%d-%H%M%S")
        new_key = f"{timestamp}-{key}"
        
        copy_source = {'Bucket': bucket, 'Key': key}
        s3.copy_object(CopySource=copy_source, Bucket=OUTPUT_BUCKET, Key=new_key)
        
 
        print(f"Processed {key} -> {OUTPUT_BUCKET}/{new_key}")
```

Funktiossa:
- OUTPUT_BUCKET = 'leppanen-output' - Määritetään bucket, johon tiedostot kopioidaan
- def lambda_handler(event, context): - Lambdan pääfunktio
- for record in event['Records']: - Silmukka käy läpi kaikki tapahtumat (tiedostot)
- bucket = record['s3']['bucket']['name'] - Bucket, josta tiedostot kopioidaan
- key = record['s3']['object']['key'] - Tiedoston nimi, johon timestamp lisätään
- timestamp = datetime.now().strftime("%Y%m%d-%H%M%S") - Haetaan timestampille arvo
- new_key = f"{timestamp}-{key}" - Uusi tiedostonimi timestampilla
- s3.copy_object - Kopioidaan tiedosto
- print(f"Processed {key} -> {OUTPUT_BUCKET}/{new_key}") - Tulostus Lambda lokeihin


## Lambda funktion zippaus

Lambda vaatii, että tiedosto on nimenomaan zip -muodossa, joten pakkasin tiedoston komennolla:

    zip function.zip lambda_function.py

## Lambda funktion luominen

Ajoin seuraavan komennon, jolla lambda funktio luotiin. Python versiona käytin python 3.12, jonka version tarkistin aiemmin:

    aws lambda create-function \
        --function-name leppanen-in-out \
        --runtime python3.12 \
        --role arn:aws:iam::905418040354:role/LabRole \
        --handler lambda_function.lambda_handler \
        --zip-file fileb://function.zip \
        --region us-east-1

## Event notification konfiguraatio

Loin ensin tyhjän tiedoston, johon konfiguraatio tallennetaan:

    touch s3-notification.json

Seuraava json käynnistää Lambda funktion, kun uusi tiedosto lisätään:

    {
        "LambdaFunctionConfigurations": [
            {
                "Id": "NewFileTrigger",
                "LambdaFunctionArn": "arn:aws:lambda:us-east-1:905418040354:function:leppanen-in-out",
                "Events": ["s3:ObjectCreated:*"]
            }
        ]
    }

## S3 permission

Annetaan S3:lle lupa kutsua Lambdaa:

     aws lambda add-permission \
        --function-name leppanen-in-out \
        --principal s3.amazonaws.com \
        --statement-id s3invoke \
        --action "lambda:InvokeFunction" \
        --source-arn arn:aws:s3:::leppanen-input

## Lambda-funktion liittäminen S3 triggeriin

Seuraavalla komennolla saatiin Lambda-funktio liitettyä S3 triggeriin:

        aws s3api put-bucket-notification-configuration \
            --bucket leppanen-input \
            --notification-configuration file://s3-notification.json

## Versioinnin käyttöönotto

Lisätään versiointi kumpaankin bucketiin myöhempiä vaiheita varten:

    aws s3api put-bucket-versioning \
    --bucket leppanen-input \
    --versioning-configuration Status=Enabled

    aws s3api put-bucket-versioning \
    --bucket leppanen-output \
    --versioning-configuration Status=Enabled

# Lifecycle rule

Eräs tapa säästää kustannuksissa on poistaa S3 Bucketeihin tallennettujen tiedostojen vanhoja versioita tietyn ajanjakson jälkeen (tai vaihtoehtoisesti siirtää ne pitkäaikaisarkistoon kuten glacier). Kyseisessä projektissa vanhat tiedostoversiot poistetaan automaattisesti kuukauden ajanjakson jälkeen.

Luodaan tiedosto lifecycle.json:

    touch lifecycle.json

Lisätään lifecycle -sääntö:

    {
      "Rules": [
        {
          "ID": "DeleteOldVersions",
          "Status": "Enabled",
          "Filter": {
            "Prefix": ""
          },
          "NoncurrentVersionExpiration": {
            "NoncurrentDays": 30
          }
        }
      ]
    }


## Käyttöönotto

Lifecycle -sääntö täytyy ottaa käyttöön kumpaankin bucketiin erikseen, otin ne käyttöön ajamalla seuraavat komennot:

    aws s3api put-bucket-lifecycle-configuration \
    --bucket leppanen-input \
    --lifecycle-configuration file://lifecycle.json

    aws s3api put-bucket-lifecycle-configuration \
    --bucket leppanen-output \
    --lifecycle-configuration file://lifecycle.json

Seuraavilla komennoilla voidaan vielä varmistua siitä että sääntö on voimassa:

    aws s3api get-bucket-lifecycle-configuration \
    --bucket leppanen-input

    aws s3api get-bucket-lifecycle-configuration \
    --bucket leppanen-output

## Testaus

Luodaan testitiedosto:

    echo "Hello Projekti" > projektitesti.txt

Ajetaan seuraava komento. Jokaisella ajolla input-bucketiin tallentuu tiedosto omalla versio numerollaan:

    aws s3 cp projektitesti.txt s3://leppanen-input
    aws s3 cp projektitesti.txt s3://leppanen-input
    aws s3 cp projektitesti.txt s3://leppanen-input

Ja tarkistetaan vielä että kyseisestä tiedostosta todella löytyy kaivatut kolme eri versiota.

Input-bucket:

    aws s3api list-object-versions --bucket leppanen-input --prefix projektitesti.txt

Lambda luo tiedostoista uudet nimet timestampilla, output-bucketista löytyy 3 eri tiedostoa:

    aws s3 ls s3://leppanen-output

# SNS topic

Luodaan ensin SNS topic hälytyksiä varten:

    aws sns create-topic --name leppanen-alerts

<img width="567" height="100" alt="image" src="https://github.com/user-attachments/assets/acb1caec-d962-4305-8baf-a847dcea3a60" />

Seuraavalla komennolla saadaan lisättyä oma sähköposti vastaanottajaksi:

    aws sns subscribe \
    --topic-arn arn:aws:sns:us-east-1:905418040354:leppanen-alerts \
    --protocol email \
    --notification-endpoint ari-pekka.leppanen@hotmail.com

<img width="569" height="151" alt="image" src="https://github.com/user-attachments/assets/e60e45df-c705-44ed-b8bd-731ddfcbc82f" />

Tarkistetaan että ilmoitus tulee sähköpostiin ja käydään hyväksymässä subscription

<img width="1054" height="390" alt="image" src="https://github.com/user-attachments/assets/b6a26703-8c62-4d22-941d-fe4222a4fe26" />

<img width="545" height="260" alt="image" src="https://github.com/user-attachments/assets/26ac2e09-2c8c-4938-94ce-704eb53160f6" />

Hyväksynnän jälkeen voidaan tarkistaa tila vielä terminaalista:

    aws sns list-subscriptions-by-topic     --topic-arn arn:aws:sns:us-east-1:905418040354:leppanen-alerts

<img width="988" height="230" alt="image" src="https://github.com/user-attachments/assets/b363a979-15f7-4c1f-8f42-c7a8c933fe3f" />

Ja sitten ilmoitusten testaus vielä ajamalla komento:

    aws sns publish \                                                                                     
    --topic-arn arn:aws:sns:us-east-1:905418040354:leppanen-alerts \
    --message "Testataan SNS toiminta" \
    --subject "SNS Toiminnan testaus"

<img width="568" height="170" alt="image" src="https://github.com/user-attachments/assets/ccd88742-c57d-4ead-92f3-a270b9b746cd" />

Ja saadaankin vastaus sähköpostiin

<img width="1086" height="374" alt="image" src="https://github.com/user-attachments/assets/bdfd7778-e12b-40c8-8cfb-f0ba57b7d283" />

# AWS CloudTrail

Lokitietoja varten luodaan ensin CloudTrailille oma bucket ja lopuksi tarkistetaan bucketit komennolla `aws s3 ls`:

    aws s3api create-bucket \
    --bucket leppanen-cloudtrail-logs \
    --region us-east-1

<img width="475" height="229" alt="image" src="https://github.com/user-attachments/assets/691c8bbe-2201-4e40-a45f-1b6ba0b2f062" />

Ja sitten kerrotaan CloudTrailille mihin bucketiin audit tiedot ohjataan:

    aws cloudtrail create-trail \
    --name leppanen-trail \
    --s3-bucket-name leppanen-cloudtrail-logs

Tässä kohtaa tulee tosin ongelma policyn suhteen:

    ap@MSI:~$ aws cloudtrail create-trail \
    --name leppanen-trail \
    --s3-bucket-name leppanen-cloudtrail-logs

    aws: [ERROR]: An error occurred (InsufficientS3BucketPolicyException) when calling the CreateTrail operation: Incorrect S3 bucket policy is detected for bucket: leppanen-cloudtrail-logs
    ap@MSI:~$ 

Ongelma on ilmeisesti melko yleinen ja johtuu siitä, että CloudTrail tarvitsee erilliset S3 bucket policy -oikeudet. Ongelman sai taklattua lisäämällä tuon policyn [lähde](https://github.com/hashicorp/terraform/issues/6388)

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "AWSCloudTrailAclCheck",
      "Effect": "Allow",
      "Principal": {
        "Service": "cloudtrail.amazonaws.com"
      },
      "Action": "s3:GetBucketAcl",
      "Resource": "arn:aws:s3:::leppanen-cloudtrail-logs"
    },
    {
      "Sid": "AWSCloudTrailWrite",
      "Effect": "Allow",
      "Principal": {
        "Service": "cloudtrail.amazonaws.com"
      },
      "Action": "s3:PutObject",
      "Resource": "arn:aws:s3:::leppanen-cloudtrail-logs/AWSLogs/*",
      "Condition": {
        "StringEquals": {
          "s3:x-amz-acl": "bucket-owner-full-control"
        }
      }
    }
  ]
}
```

Policyjen korjauksen jälkeen aiempi komento menee läpi:

<img width="901" height="659" alt="image" src="https://github.com/user-attachments/assets/3db1da70-a9cf-4b77-808e-28b0cd1de4f2" />

Sitten lokitietojen keräys vielä "käyntiin":

    aws cloudtrail start-logging \
    --name leppanen-trail

Ja tarkistetaan palauttaako seuraava komento "true":

    aws cloudtrail get-trail-status \
    --name leppanen-trail

<img width="515" height="292" alt="image" src="https://github.com/user-attachments/assets/a6db91b7-19ee-4bd3-b9f6-32f79efb5f70" />

# CloudWatch alarm

Luodaan alarm, jonka mukaan saadaan SNS-palvelulta ilmoitukset:

```
aws cloudwatch put-metric-alarm \
    --alarm-name leppanen-lambda-errors \
    --namespace AWS/Lambda \
    --metric-name Errors \
    --dimensions Name=FunctionName,Value=leppanen-in-out \
    --statistic Sum \
    --period 60 \
    --evaluation-periods 1 \
    --threshold 0 \
    --comparison-operator GreaterThanThreshold \
    --alarm-actions arn:aws:sns:us-east-1:905418040354:leppanen-alerts
```

- period 60 - määritetään alarmille ajanjaksoksi 1 minuutti
- evaluation-periods 1 - hälytyksiin vaadittavien jaksojen määrä = 1, eli pienin mahdollinen
- threshold 0 - Raja-arvoksi määritetään 0, jolloin yksikin virhe aiheuttaa hälytyksen
- comparison-operator - Vertailu operaattori, asetetaan GreaterThanThreshold, eli jos virheet > 0, seuraa hälytys

Voidaan tarkistaa vielä että alarm määritykset menivät oikein:

    aws cloudwatch describe-alarms     --alarm-names leppanen-lambda-errors

<img width="851" height="621" alt="image" src="https://github.com/user-attachments/assets/be6afc25-9cfb-4a80-940d-7fa01b4cc7b6" />

### Dashboard

Ensin täytyy luoda tyhjä pohja dashboardia varten:

    aws cloudwatch put-dashboard \
    --dashboard-name leppanen-dashboard \
    --dashboard-body '{
        "widgets": []
    }'

<img width="353" height="190" alt="image" src="https://github.com/user-attachments/assets/2db421cf-e903-4d10-ab05-db4d91d37277" />

Dashboardin valmis komento:

```
aws cloudwatch put-dashboard \
--dashboard-name leppanen-dashboard \
--dashboard-body '{
    "widgets": [
        {
            "type": "metric",
            "x": 0,
            "y": 0,
            "width": 24,
            "height": 6,
            "properties": {
                "metrics": [
                    [ "AWS/Lambda", "Invocations", "FunctionName", "leppanen-in-out" ],
                    [ ".", "Errors", ".", "." ]
                ],
                "period": 60,
                "stat": "Sum",
                "region": "us-east-1",
                "title": "leppanen-dashboard"
            }
        },
        {
            "type": "metric",
            "x": 0,
            "y": 6,
            "width": 24,
            "height": 6,
            "properties": {
                "metrics": [
                    [ "AWS/Lambda", "Duration", "FunctionName", "leppanen-in-out" ]
                ],
                "period": 60,
                "stat": "Average",
                "region": "us-east-1",
                "title": "leppanen-dashboard"
            }
        }
    ]
}'
```

<img width="740" height="764" alt="image" src="https://github.com/user-attachments/assets/4e543007-8fb7-4f11-858b-3b71080af7d8" />

Dashboard näkymä voidaan hakea Management Consolesta CloudWatch > Dashboards > leppanen-dashboard

<img width="1390" height="708" alt="image" src="https://github.com/user-attachments/assets/39f2d830-f2c9-4485-8ed8-b9ac56fb7ad2" />

# Testaukset

Vielä loppuun tarkastin että ympäristöni toimii kuten olin suunnitellut.

## End-to-end ja versiointi

Poistin bucketeista kaiken sisällön ennen testausta:

<img width="351" height="92" alt="image" src="https://github.com/user-attachments/assets/ab7542ea-4ac5-4e5e-91d6-7e80cb72fbb0" />

Kopioitava tekstitiedosto:

<img width="538" height="83" alt="image" src="https://github.com/user-attachments/assets/a42bdc1c-0783-423c-ba5f-974c6d5964c2" />

Kopioidaan tiedosto kolmeen kertaan, jotta voidaan varmistua myös versioinnin toiminnasta:

<img width="510" height="144" alt="image" src="https://github.com/user-attachments/assets/7ef73b17-7486-4532-b524-ae3e056bf128" />

Ja lopuksi tarkastetaan bucketien sisältö:

<img width="508" height="137" alt="image" src="https://github.com/user-attachments/assets/5585f04c-bc70-41cd-aeb1-9263ec10cdf6" />

Lokitiedot tapahtumista:

<img width="1217" height="440" alt="image" src="https://github.com/user-attachments/assets/b08726ca-6bc5-4648-9c92-1d4d1b3605a7" />


## SNS

Testasin SNS toiminnan ajamalla seuraavan komennon, jolla saadaan testi sähköposti lähetettyä:

<img width="940" height="112" alt="image" src="https://github.com/user-attachments/assets/b659028a-e0d4-4145-b1ac-50871164e572" />

Sähköposti vastaanotettu:

<img width="399" height="178" alt="image" src="https://github.com/user-attachments/assets/6e11f858-52c4-4d0e-a826-6ab3dc6e2373" />

## CloudWatch

CloudWatchin toimintaa kävin tarkastelemassa Management Consolen kautta, ympäristössä ei ole tapahtunut alarmia nostavia hälytyksiä toistaiseksi:

<img width="1448" height="341" alt="image" src="https://github.com/user-attachments/assets/01b313b9-f01a-49db-8bf9-d32cbe65049f" />

## CloudTrail

Management Console näkymästä voidaan käydä tarkastelemassa CloudTrailin tallentamia lokitietoja:

<img width="1363" height="433" alt="image" src="https://github.com/user-attachments/assets/c4957bb1-cd1d-4632-887e-1a1177c8dc88" />
