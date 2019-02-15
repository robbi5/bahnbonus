bahn.bonus API
==============

In Feburary 2019, the [BahnCard-Service released](https://www.bahn.de/p/view/service/mobile/bahnbonus-app.shtml) the Bahn.Bonus App for [iOS](https://itunes.apple.com/de/app/bahnbonus/id1436948507) and [Android](https://play.google.com/store/apps/details?id=com.deutschebahn.bahnbonus). This is another App from DB people may want to install - but more interesting: this opens up the bahn.bonus loyality status as an API.

This repository describes the used API endpoints, so anybody can use the bahn.bonus loyality status in more creative ways. Have fun!


Authentication
--------------

Authentication is implemented with OpenID Connect - using [keycloak](https://www.keycloak.org). The App is using [the standard OpenID Connect SDK _AppAuth-iOS_](https://github.com/openid/AppAuth-iOS) with the autodiscovery with following URL:

```
https://auth.businesshub.deutschebahn.com/auth/realms/kubi/.well-known/openid-configuration
```

The following parameters are used for the authorization request:

```
scope:          openid profile user:read user:write
client_id:      kubi
redirect_uri:   kubi://authentication/redirect
response_type:	code
```

After receiving the redirect to `kubi://authentication/redirect?state=...&session_state=...&code=...`, send a POST request with the code and with `client_id, redirect_uri, grant_type, code_verifier` to the `token_endpoint` from the autodiscovery url.

API
---

For the following requests to `api.businesshub.deutschebahn.com` the Access Token from the OpenID Connect Flow is used as the `Authorization: Bearer {token}` header. Additionally, the header `Key: xtLA2lhamHx3ujzoISx2RfE1N9AoXXLZ` is sent with every request.


#### customer status
GET `https://api.businesshub.deutschebahn.com/self-service/v1/customerdata/customerStatus`
```
{
    "firstname": "Max",
    "isBahnbonusUser": true,
    "isHandshaked": true,
    "lastname": "Mustermann",
    "salutation": "Herr",
    "title": null
}
```

#### loyality service configuration
GET `https://api.businesshub.deutschebahn.com/loyalty-service/v1/configuration`
```
{
    "bonusPointsExpirationPeriod": 36,
    "comfortStatusPointsRequirement": 2000,
    "statusPointsExpirationPeriod": 12
}
```

#### loyality points
GET `https://api.businesshub.deutschebahn.com/loyalty-service/v1/customer/loyalty`
```
{
    "collectedPoints": {
        "bonus": 1783,
        "status": 1750
    },
    "comfortStatus": false,
    "locked": false,
    "registered": true
}
```

#### customer data
GET `https://api.businesshub.deutschebahn.com/self-service/v1/customerdata`
```
{
    "address": {
        "addressAddition": null,
        "city": "Sample_Town",
        "countryCode": "DEU",
        "postalCode": "12345",
        "street": "Sample_Street 12"
    },
    "bcNumber": null,
    "birthday": "2012-04-23",
    "email": "maxmustermann@test.com",
    "firstName": "Max",
    "lastName": "Mustermann",
    "phone": "+49123456789",
    "salutation": "Herr",
    "title": null
}
```

#### customer card
GET `https://api.businesshub.deutschebahn.com/loyalty-service/v1/customer/card`
```
{
    "cardNumber": 7081000000000000,
    "ownerName": "Max Mustermann",
    "trainClass": 2,
    "type": "BC50",
    "validityEndDate": "2018-09-30",
    "validityStartDate": "2017-01-01"
}
```

#### loyality transactions
GET `https://api.businesshub.deutschebahn.com/loyalty-service/v1/customer/transactions?page-offset=0&limit=15`

```
[
    {
        "amount": 42.0,
        "category": "Fahrscheinkauf",
        "currency": "EUR",
        "date": "2019-05-08",
        "description": " ",
        "logoUrl": null,
        "points": {
            "bonus": 42,
            "status": 42
        },
        "ticketInformation": {
            "firstDayOfValidity": "2019-05-08",
            "stationOfArrivalForOutwardTrip": "Hamburg Hbf",
            "stationOfArrivalForReturnTrip": null,
            "stationOfDepartureForOutwardTrip": "Berlin Hbf",
            "stationOfDepartureForReturnTrip": null,
            "trainClass": 2
        }
    },
    {
        "amount": 0.0,
        "category": "Prämienbestellung",
        "currency": "EUR",
        "date": "2018-05-05",
        "description": "6 x Upgrade in die 1.Klasse (einfache Fahrt)",
        "logoUrl": null,
        "points": {
            "bonus": -3000,
            "status": 0
        },
        "ticketInformation": null
    },
    ...
]
```

Notes
-----
Have fun with your data. If you find something interesting you can do with it, feel free to open a pull request with a link to your project.

License: [CC-0](https://creativecommons.org/publicdomain/zero/1.0/deed)
