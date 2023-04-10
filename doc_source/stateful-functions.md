# Apache Flink Stateful Functions<a name="stateful-functions"></a>

[Stateful Functions](https://nightlies.apache.org/flink/flink-statefun-docs-stable/) is an API that simplifies building distributed stateful applications\. It’s based on functions with persistent state that can interact dynamically with strong consistency guarantees\.

A Stateful Functions application is basically just an Apache Flink Application and hence can be deployed to Kinesis Data Analytics\. However, there are a couple of differences between packaging Stateful Functions for a Kubernetes cluster and for Kinesis Data Analytics\. The most important aspect of a Stateful Functions application is the [module configuration](https://nightlies.apache.org/flink/flink-statefun-docs-master/docs/deployment/module/) contains all necessary runtime information to configure the Stateful Functions runtime\. This configuration is usually packaged into a Stateful Functions specific container and deployed on Kubernetes\. But that is not possible with Kinesis Data Analytics\.

Following is an adaptation of the StateFun Python example for Kinesis Data Analytics:

## Apache Flink Application Template<a name="stateful-functions-template"></a>

Instead of using a customer container for the Stateful Functions runtime, customers can compile a Flink application jar that just invokes the Stateful Functions runtime and contains the required dependencies\. For Flink 1\.13, the required dependencies look similar to this:

```
<dependency>
 <groupId>org.apache.flink</groupId>
 <artifactId>statefun-flink-distribution</artifactId>
 <version>3.1.0</version>
 <exclusions>
  <exclusion>
   <groupId>org.slf4j</groupId>
   <artifactId>slf4j-log4j12</artifactId>
  </exclusion>
  <exclusion>
   <groupId>log4j</groupId>
   <artifactId>log4j</artifactId>
  </exclusion>
 </exclusions>
</dependency>
```

And the main method of the Flink application to invoke the Stateful Function runtime looks like this:

```
public static void main(String[] args) throws Exception {
 final StreamExecutionEnvironment env = StreamExecutionEnvironment.getExecutionEnvironment();

 StatefulFunctionsConfig stateFunConfig = StatefulFunctionsConfig.fromEnvironment(env);

 stateFunConfig.setProvider((StatefulFunctionsUniverseProvider) (classLoader, statefulFunctionsConfig) -> {
  Modules modules = Modules.loadFromClassPath();
  return modules.createStatefulFunctionsUniverse(stateFunConfig);
 });

 StatefulFunctionsJob.main(env, stateFunConfig);
}
```

Note that these components are generic and independent of the logic that is implemented in the Stateful Function\. 

## Location of the module configuration<a name="stateful-functions-module-configuration"></a>

The Stateful Functions module configuration needs to be included in the class path to be discoverable for the Stateful Functions runtime\. It's best to include it in the resources folder of the Flink application and package it into the jar file\.

Similar to a common Apache Flink application, you can then use maven to create an uber jar file and deploy that on Kinesis Data Analytics\.