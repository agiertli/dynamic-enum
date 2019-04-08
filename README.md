This example contains a helper class which allows to enumeration values from the database.
These values will be then in business-central for authoring.

Provided that you have RHPAM (or RHDM) 7.3 installed, you can utilize this example like this (PostgreSQL is used in this demo):


- Download PostgreSQL driver from [PostgreSQL website](https://jdbc.postgresql.org/download.html)
- Start the RHPAM (i.e. `./standalone.sh`)
- Deploy the JDBC driver :
~~~
  $ ./jboss-cli.sh
  $ connect
  $ deploy postgresql-42.2.5.jar
  ~~~
- Configure the JBoss EAP datasource - change the values so they fit your environment:
~~~
/subsystem=datasources/data-source=CliDatasource:add(jndi-name=java:/java:jboss/datasources/EnumDS,driver-name=postgresql-42.2.5.jar,connection-url=jdbc:postgresql://localhost:5432/productcodes,user-name=agiertli,password=agiertli)
~~~
- Create the example DB table which will hold your enum values:
~~~
CREATE TABLE productlist (
    id bigint PRIMARY KEY,
    name character varying(255),
    region character varying(255)
);
~~~
- Fill it with sample data:
~~~
INSERT INTO "public"."productlist"("id","name","region")
VALUES
(1,E'Ibalgin',E'India'),
(2,E'Nalgesin',E'India'),
(3,E'Kinedril',E'Japan'),
(4,E'Paralen',E'Japan'),
(5,E'Acylpirin',E'India'),
(6,E'Coldrex',E'Japan');
~~~
- Install the enum-loader project to your local maven repository:
 ~~~
 mvn clean install
 ~~~

 Once the Enum Loader is setup, you can proceed to importing KJAR so you can actually see the Enums in action.

 - Start the RHPAM / RHDM and and log into the business-central
 - Import the repository with the example KJAR. The repository is located at [this URL on github](https://github.com/agiertli/SampleEnumKjar.git)
  ![Import repository](https://ctrlv.cz/shots/2019/04/08/obgU.png)
 - Review the Enumeration definition, which looks like this:
 ~~~
'Product.region' : ['India','Japan']
'Product.name[region]' : '(new org.redhat.appdev.enumloader.EnumLoader()).getProductCodes("@{region}")'
~~~
The above enum includes dependency between attribute region and name. In practice, it means that whenever specific region (India,Japan,..) is selected, only country specific enum values will be displayed to the end user.

Sample values for Japan:
![Japan values](https://ctrlv.cz/shots/2019/04/08/mG5W.png)
Sample values for India:
![India values](https://ctrlv.cz/shots/2019/04/08/RfJ2.png)
