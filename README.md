# OIDC Mapper

OIDC Mapper to map custom attributes to custom claims in the JWT.

## Prerequisites
1. Podman
2. Java 11+
3. Maven 3.x

## Setup
1. Run Keycloak

        podman run -p 8080:8080 -e KEYCLOAK_USER=admin -e KEYCLOAK_PASSWORD=admin quay.io/keycloak/keycloak:12.0.2

2. Create a realm
3. Create a user, give it custom attribute with key `station` and value of whatever string you want.  Also make sure to create a role and map that role to this user.
4. Create a client and test it following these [instructions](https://developers.redhat.com/blog/2020/01/29/api-login-and-jwt-token-generation-using-keycloak#test_your_new_client)
5. Use [jwt.io](https://jwt.io/) to decode the `access_token` in your response from step 4

## Deploy
1. Build the app

        build.sh

2. Deploy your app to the running keycloak container, replacing `CONTAINER_ID` below with yours

        podman cp target/keycloak-customProtocolMapper-1.0-SNAPSHOT.jar CONTAINER_ID:/opt/jboss/keycloak/standalone/deployments/keycloak-customProtocolMapper-1.0-SNAPSHOT.jar

3. Log in again using the instructions from step 4 above.  Something like:

        curl -L -X POST 'http://localhost:8080/auth/realms/whatever-realm/protocol/openid-connect/token' \
        -H 'Content-Type: application/x-www-form-urlencoded' \
        --data-urlencode 'client_id=your-client-id' \
        --data-urlencode 'grant_type=password' \
        --data-urlencode 'client_secret=ec78c6bb-8339-4bed-9b1b-e973d27107dc' \
        --data-urlencode 'scope=openid' \
        --data-urlencode 'username=jkeam' \
        --data-urlencode 'password=password'

4. You should see `roles` and `station` in the payload