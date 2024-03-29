# Apache Ignite (Experimental)

[Apache Ignite](http://ignite.apache.org/) data grid is an in-memory distributed key-value store which can be viewed 
as a distributed partitioned hash map, with every cluster node owning a portion of the overall data.


## Configuring Ignite
### Adding Ignite dependencies
To add the dependencies via Maven, add the following module:

~~~~
<dependency>
    <groupId>org.hibernate.ogm</groupId>
    <artifactId>hibernate-ogm-ignite</artifactId>
    <version>5.1.0.Final</version>
</dependency>
~~~~

This will pull the Ignite libraries transparently.

If you are not using a dependency management tool, copy all the dependencies from the distribution in the directories:

* `/lib/required`
* `/lib/ignite`
* Optionally - depending on your container - you might need some of the jars from `/lib/provided`

### Ignite specific configuration properties

hibernate.ogm.ignite.configuration_resource_name::
	property for specifying the name of the Ignite configuration file.
	
To use this property add following module:

~~~
<dependency>
    <groupId>org.apache.ignite</groupId>
    <artifactId>ignite-spring</artifactId>
    <version>${ignite.version}</version>
</dependency>
~~~~

hibernate.ogm.ignite.configuration_class_name::
	property for specifying class name. 
Class must implements `org.hibernate.ogm.datastore.ignite.IgniteConfigurationBuilder`

Here is an example of configuration builder class

~~~~
import org.apache.ignite.configuration.IgniteConfiguration;
import org.hibernate.ogm.datastore.ignite.IgniteConfigurationBuilder;

public class SimpleIgniteConfigurationBuilder implements IgniteConfigurationBuilder {
	@Override
	public IgniteConfiguration build() {
		IgniteConfiguration config = new IgniteConfiguration();
		
		//do initialization here 
		
		return config;
	}
}
~~~~

hibernate.ogm.ignite.instance_name::
property for specifying the Ignite instance name 
Use this property if you want to connect to existing Ignite instance that was started earlier in the same JVM.

## Storage principles
Each Entity type maps to cache. Default cache name equals entity class name.
Use `@Table` annotation to change delaults.

~~~~
@Table(schema="custom cache name", name="custom object name")
~~~~

Embedded collections are stored as field with value of type BinaryObject[] (array of BinaryObject)

All join columns for associations should be declared as indexed fields in underlying cache. 

Composite ids are stored as BinaryObject.

## Transactions
Ignite supports transactions and integrates with any standard JTA `TransactionManager`.

## Queries
You can express queries in a few different ways:

* using JP-QL
* using native Ignite SQL

Hibernate OGM is a work in progress, so only a sub-set of JP-QL constructs is available when using the JP-QL query support.
This includes:

* simple comparisons using "<", "<=", "=", ">=" and ">"
* IS NULL and IS NOT NULL
* the boolean operators AND, OR, NOT
* LIKE, IN and BETWEEN
* ORDER BY

Queries using these constructs will be transformed into equivalent native Ignite SQL queries.

