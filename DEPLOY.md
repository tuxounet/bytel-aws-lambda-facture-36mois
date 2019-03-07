Voici un rapide mod-op pour la mise en place de l’api sur cette lambda et son api
 
---

## Mise en place de la fonction lambda
1.      Entrer dans le Service Lambda de la console AWS du compte cible
2.      Créer la fonction lambda "jivaros-factures-hackathon18"
3.      Associer un rôle jivaros-facture-hackathon18-role
4.      Utiliser le template de rôle "Generic MicroServices"
5.      Définir le handler avec la valeur suivante handler.handler
6.      Runtime : Node8.10
7.      Augmenter le timeout a 60 secondes
 
 
### Ajouter l'évènement de test :
```json
{
  "resource": "/{proxy+}",
  "requestContext": {
    "resourceId": "123456",
    "apiId": "1234567890",
    "resourcePath": "/{proxy+}",
    "httpMethod": "POST",
    "requestId": "c6af9ac6-7b61-11e6-9a41-93e8deadbeef",
    "accountId": "123456789012",
    "identity": {
      "apiKey": null,
      "userArn": null,
      "cognitoAuthenticationType": null,
      "caller": null,
      "userAgent": "Custom User Agent String",
      "user": null,
      "cognitoIdentityPoolId": null,
      "cognitoIdentityId": null,
      "cognitoAuthenticationProvider": null,
      "sourceIp": "5.51.203.00",
      "accountId": null
    },
    "stage": "prod"
  },
  "queryStringParameters": {
      },
  "headers": {
    "Content-Type": "application/json",
    "Via": "1.1 08f323deadbeefa7af34d5feb414ce27.cloudfront.net (CloudFront)",
    "Accept-Language": "en-US,en;q=0.8",
    "CloudFront-Is-Desktop-Viewer": "true",
    "CloudFront-Is-SmartTV-Viewer": "false",
    "CloudFront-Is-Mobile-Viewer": "false",
    "X-Forwarded-For": "127.0.0.1, 127.0.0.2",
    "CloudFront-Viewer-Country": "US",
    "Accept": "text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,*/*;q=0.8",
    "Upgrade-Insecure-Requests": "1",
    "X-Forwarded-Port": "443",
    "Host": "1234567890.execute-api.us-east-1.amazonaws.com",
    "X-Forwarded-Proto": "https",
    "X-Amz-Cf-Id": "cDehVQoZnx43VYQb9j2-nvCh-9z396Uhbp027Y2JvkCPNLmGJHqlaA==",
    "CloudFront-Is-Tablet-Viewer": "false",
    "Cache-Control": "max-age=0",
    "User-Agent": "Custom User Agent String",
    "CloudFront-Forwarded-Proto": "https",
    "Accept-Encoding": "gzip, deflate, sdch"
  },
  "pathParameters": {
    "proxy": "path/to/resource"
  },
  "httpMethod": "POST",
  "stageVariables": {
    "baz": "qux"
  },
  "path": "/path/to/resource",
  "body": "{\"accessToken\": \"at-XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXX\", \"idPersonne\":\"80000598XXXX\"}"
}
```

8.      Executer l'evenement de test 

Retour du test attendu :

```json
{
  "headers": {
    "Access-Control-Allow-Origin": "*"
  },
  "body": "{\"isError\":true,\"errorCode\":1,\"errorSubCode\":\"GENERAL_FAILURE\",\"error\":\"\\\"Request failed with status code 401\\\"\"}"
}
```

## Mise en place du endpoint HTTP via API Gateway

Entrer dans le service API Gateway
 
1.      Create API
2.      New API

        Parametrage :
        -         API Name : Jivaros-factures-hackathon18-api
        -         Endpoint Type : Regional
        -         Puis Create API
3.      Dans ressources

  -      Cliquez sur actions > Create ressources
  -      Ressources name : factures
  -      Enable cors : coché
  -      Create ressources
  
4.      Dans la ressource factures
  a.      Cliquer sur actions > create method
  b.      Selectionner le verbe post , puis le bouton valider
  c.      Integration type :  "lambda"
  d.      Cocher la case "use proxy lambda integration"
  e.      Lambda function , saisir/selectionner jivaros-factures-hackathon18
  f.      Puis cliquer sur save
  g.      A l'avertissement d'ajout de permission, cliquer sur OK
  
5.      Dans le verbe post

  a.      Faire un test avec pour request body {}
  b.      Resultat attendu :
          ```json
          {
            "isError": true,
            "errorCode": 2,
            "errorSubCode": "MISSING_ID_PERSONNE",
            "error": null
          }
          ```
6.      Dans le menu actions
  a.      Cliquez sur "deploy api"
  b.      Deployment stage , create new stage
  c.      Stage name: prod
  d.      Récupérer l'url dans le bandeau
 
7.      Tester dans postman
  a.      URL :  https://XXXXXXXXXXX.execute-api.eu-west-1.amazonaws.com/prod/factures (remplacer par l’url recuperé au point 6)
  b.      Headers : Content-type : application/json
          Body : (raw)
```json
{
 "idPersonne":"80000598XXXXX"   ,
 "accessToken":"at-XXXXXXXX-XXXXX-XXXX-XXXX-XXXXXXXXX"
}
```
 
