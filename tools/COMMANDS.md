```
export=https_proxy=localhost:8080

cf login -u andrew.lunde@sap.com

export globalacctguid=71f51ce6-c6b5-4583-a6ac-49e392506619

export sapcpuser=primaryuser03@gmail.com
export sapcppass=PrimaryUs3r03


cf create-service cis central ACC_CIS_CEN
cf create-service-key ACC_CIS_CEN ACC_CIS_CEN_Key
cf service-key ACC_CIS_CEN ACC_CIS_CEN_Key

authurlcen=$(cf service-key ACC_CIS_CEN ACC_CIS_CEN_Key | tail -n +3 | jq .uaa.url | tr -ds '"' '')
clientidcen=$(cf service-key ACC_CIS_CEN ACC_CIS_CEN_Key | tail -n +3 | jq .uaa.clientid | tr -ds '"' '')
clientsecretcen=$(cf service-key ACC_CIS_CEN ACC_CIS_CEN_Key | tail -n +3 | jq .uaa.clientsecret | tr -ds '"' '')
accountsurlcen=$(cf service-key ACC_CIS_CEN ACC_CIS_CEN_Key | tail -n +3 | jq .endpoints.accounts_service_url | tr -ds '"' '')
provisioningurlcen=$(cf service-key ACC_CIS_CEN ACC_CIS_CEN_Key | tail -n +3 | jq .endpoints.provisioning_service_url | tr -ds '"' '')
bearercen=$(curl -u $clientidcen:$clientsecretcen $authurlcen/oauth/token --silent --location --insecure --header 'Content-Type: application/x-www-form-urlencoded' --data-urlencode 'grant_type=password' --data-urlencode 'username='$sapcpuser --data-urlencode 'password='$sapcppass | jq .access_token | tr -ds '"' '')

cf create-service cis local ACC_CIS_LOC
cf create-service-key ACC_CIS_LOC ACC_CIS_LOC_Key
cf service-key ACC_CIS_LOC ACC_CIS_LOC_Key

authurlloc=$(cf service-key ACC_CIS_LOC ACC_CIS_LOC_Key | tail -n +3 | jq .uaa.url | tr -ds '"' '')
clientidloc=$(cf service-key ACC_CIS_LOC ACC_CIS_LOC_Key | tail -n +3 | jq .uaa.clientid | tr -ds '"' '')
clientsecretloc=$(cf service-key ACC_CIS_LOC ACC_CIS_LOC_Key | tail -n +3 | jq .uaa.clientsecret | tr -ds '"' '')
accountsurlloc=$(cf service-key ACC_CIS_LOC ACC_CIS_LOC_Key | tail -n +3 | jq .endpoints.accounts_service_url | tr -ds '"' '')
provisioningurlloc=$(cf service-key ACC_CIS_LOC ACC_CIS_LOC_Key | tail -n +3 | jq .endpoints.provisioning_service_url | tr -ds '"' '')
bearerloc=$(curl -u $clientidloc:$clientsecretloc $authurlloc/oauth/token --silent --location --insecure --header 'Content-Type: application/x-www-form-urlencoded' --data-urlencode 'grant_type=password' --data-urlencode 'username='$sapcpuser --data-urlencode 'password='$sapcppass | jq .access_token | tr -ds '"' '')

curl $accountsurlcen/accounts/v1/subaccounts --silent --location --insecure --header 'Content-Type: application/json' --header 'Authorization: Bearer '$bearercen | jq .

curl $accountsurlcen/accounts/v1/subaccounts --silent --location --insecure --request POST --header 'Content-Type: application/json' --header 'Accept: application/json' --header 'Authorization: Bearer '$bearercen --data-raw '{"betaEnabled": true, "customProperties": [ { "key": "creator", "value": "primaryuser03" } ], "description": "Subaccount created via API", "displayName": "ViaAPI", "parentGUID": "'$globalacctguid'", "region": "us21", "subaccountAdmins": [ "andrew.lunde@sap.com" ], "subdomain": "viaapi", "usedForProductionSetting": "USED_FOR_PRODUCTION"}' | jq .

curl $accountsurlcen/accounts/v1/subaccounts/$lastguid --silent --location --insecure --header 'Content-Type: application/json' --header 'Authorization: Bearer '$bearercen | jq .state | tr -ds '"' ''


curl $provisioningurlloc/provisioning/v1/availableEnvironments --silent --location --insecure --header 'Content-Type: application/json' --header 'Authorization: Bearer '$bearerloc | jq .

curl $provisioningurlloc/provisioning/v1/environments --silent --location --insecure --header 'Content-Type: application/json' --header 'Authorization: Bearer '$bearerloc | jq .


{
  "description": "BLAH",
  "environmentType": "cloudfoundry",
  "landscapeLabel": "cf-us21",
  "name": "blah-test",
  "origin": "",
  "parameters": {"instance_name":"blah-test","users":[{"email":"andrew.lunde@sap.com"}]},
  "planName": "standard",
  "serviceName": "cloudfoundry",
  "technicalKey": "string",
  "user": "andrew.lunde@sap.com"
}

curl $provisioningurlloc/provisioning/v1/environments --silent --location --insecure --request POST --header 'Content-Type: application/json' --header 'Authorization: Bearer '$bearerloc --data-raw '{"displayName": "blah-test", "environmentType": "cloudfoundry", "landscapeLabel": "cf-us21", "parameters": {"instance_name":"blah-test","users":[{"email":"andrew.lunde@sap.com"}]}, "plan": "standard", "service": "cloudfoundry", "subaccount": "c96d49c7-bdbf-436c-a35a-322b90ba2793"}' | jq .

#close to working???
curl $provisioningurlloc/provisioning/v1/environments --silent --location --insecure --request POST --header 'Content-Type: application/json' --header 'Authorization: Bearer '$bearerloc --data-raw '{"displayName": "blah-test", "environmentType": "cloudfoundry", "landscapeLabel": "cf-us21", "dataCenterTechnicalKey": "cf-us21", "name": "blah-test", "parameters": {"instance_name":"blah-test","users":[{"email":"andrew.lunde@sap.com"}]}, "planName": "standard", "serviceName": "cloudfoundry", "subaccount": "c96d49c7-bdbf-436c-a35a-322b90ba2793"}' | jq .

#sapcp login --url https://cpcli.cf.eu10.hana.ondemand.com --subdomain partner-eng --user andrew.lunde@sap.com
# sapcp create accounts/environment-instance --subaccount c96d49c7-bdbf-436c-a35a-322b90ba2793 --parameters '{"instance_name":"ypartner-sub1","users":[{"email":"andrew.lunde@sap.com"}]}' --display-name ypartner-sub1 --environment cloudfoundry --landscape cf-us21 --service cloudfoundry --plan standard


source orgs_create orgs1.txt -f
source orgs_create orgs2.txt -f
```
