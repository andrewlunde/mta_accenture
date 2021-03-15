```
export=https_proxy=localhost:8080

cf create-service cis central ACC_CIS
cf create-service-key ACC_CIS ACC_CIS_Key
cf service-key ACC_CIS ACC_CIS_Key


export globalacctguid=71f51ce6-c6b5-4583-a6ac-49e392506619
authurl=$(cf service-key ACC_CIS ACC_CIS_Key | tail -n +3 | jq .uaa.url | tr -ds '"' '')
clientid=$(cf service-key ACC_CIS ACC_CIS_Key | tail -n +3 | jq .uaa.clientid | tr -ds '"' '')
clientsecret=$(cf service-key ACC_CIS ACC_CIS_Key | tail -n +3 | jq .uaa.clientsecret | tr -ds '"' '')
accountsurl=$(cf service-key ACC_CIS ACC_CIS_Key | tail -n +3 | jq .endpoints.accounts_service_url | tr -ds '"' '')
export sapcpuser=primaryuser03@gmail.com
export sapcppass=PrimaryUs3r03

bearer=$(curl -u $clientid:$clientsecret $authurl/oauth/token --silent --location --insecure --header 'Content-Type: application/x-www-form-urlencoded' --data-urlencode 'grant_type=password' --data-urlencode 'username='$sapcpuser --data-urlencode 'password='$sapcppass | jq .access_token | tr -ds '"' '')

curl $accountsurl/accounts/v1/subaccounts --silent --location --insecure --header 'Content-Type: application/json' --header 'Authorization: Bearer '$bearer | jq .

curl $accountsurl/accounts/v1/subaccounts --silent --location --insecure --request POST --header 'Content-Type: application/json' --header 'Accept: application/json' --header 'Authorization: Bearer '$bearer --data-raw '{"betaEnabled": true, "customProperties": [ { "key": "creator", "value": "primaryuser03" } ], "description": "Subaccount created via API", "displayName": "ViaAPI", "parentGUID": "'$globalacctguid'", "region": "us21", "subaccountAdmins": [ "andrew.lunde@sap.com" ], "subdomain": "viaapi", "usedForProductionSetting": "USED_FOR_PRODUCTION"}' | jq .

```
