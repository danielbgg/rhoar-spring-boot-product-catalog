# Spring Boot - Product Catalog Application
Demo application for Spring Boot.

## Openshift Deployment
```
export CATALOG_PROJECT_NAME=product-catalog
oc new-project $CATALOG_PROJECT_NAME
oc new-app -e POSTGRESQL_USER=luke -ePOSTGRESQL_PASSWORD=secret -ePOSTGRESQL_DATABASE=my_data openshift/postgresql-92-centos7 --name=my-database
mvn clean fabric8:deploy -Popenshift
```

If the Openshift has a self-signed certificate, it is necessary to import it using keytool.
```
keytool -import -noprompt -trustcacerts -alias Openshift -file /etc/pki/ca-trust/source/anchors/ca.crt -keystore $JAVA_HOME/jre/lib/security/cacert -storepass changeit
mvn clean fabric8:deploy -Popenshift -Djavax.net.ssl.trustStore=$JAVA_HOME/jre/lib/security/cacerts -Djavax.net.ssl.trustStorePassword=changeit
```

Test the service
```
export PRODUCT_CATALOG_URL=http://$(oc get route product-catalog -n $CATALOG_PROJECT_NAME -o template --template='{{.spec.host}}')
curl -X GET "$PRODUCT_CATALOG_URL/products"
```

