---
id: migrating-from-camunda-platform-7
title: Migrating from Camunda Platform 7
description: "Migrate process solutions developed for Camunda Platform 7 to run them on Camunda Platform 8."
keywords: [Camunda 8, Camunda 7, migration guide, transition, transition guide]
---
<span class="badge badge--advanced">Advanced</span>
<span class="badge badge--long">Time estimate: 1 hour</span>

This guide describes how to migrate process solutions developed for Camunda Platform 7 to run them on Camunda Platform 8. 

You will see the basic differences of the products, learn about necessary steps, and also limitations of migration.

It's important to note that migration of existing projects to Camunda Platform 8 is optional. Camunda Platform 7 still has ongoing support.



## Camunda Platform 7 vs. Camunda Platform 8

Before diving into concrete steps on migrating your models and code, let's cover some important conceptual differences between between Camunda version 7 and version 8 and how this affects your projects and solutions. After this section, we'll dive into a concrete how-to.


### Conceptual differences 


This section does not compare Camunda Platform 7 with Camunda Platform 8 in detail, but rather lists differing aspects important to know when thinking about migration.


#### No embedded engine in Camunda Platform 8

Camunda Platform 7 allows embedding the workflow engine as a library in your application. This means both run in the same JVM, share thread pools, and can even use the same datasource and transaction manager.

In contrast, the workflow engine in Camunda Platform 8 is always a remote resource for your application, while the embedded engine mode is not supported.

 If you are interested in the reasons why we switched our recommendation from embedded to remote workflow engines, please refer to [this blog post](https://blog.bernd-ruecker.com/moving-from-embedded-to-remote-workflow-engines-8472992cc371).

The implications for your process solution and the programming model are describeed below. Conceptually, the only really big difference is that with a remote engine, you cannot share technical [ACID transactions](https://en.wikipedia.org/wiki/ACID) between your code and the workflow engine. You can read more about it in the blog post ["Achieving consistency without transaction managers"](https://blog.bernd-ruecker.com/achieving-consistency-without-transaction-managers-7cb480bd08c).




#### Different data types

In Camunda Platform 7, you can store different data types, including serialized Java objects.

Camunda Platform 8 only allows storage of primary data types or JSON as process variables. This might require some additional data mapping in your code when you set or get process variables.

Camunda Platform 7 provides [Camunda Spin](https://docs.camunda.org/manual/latest/reference/spin/) to ease XML and JSON handling. This is not available with Camunda Platform 8, and ideally you migrate to an own data transformation logic you can fully control (e.g. using Jackson).

To migrate existing process solutions that use Camunda Spin heavily, you can still add the Camunda Spin library to your application itself and use its API to do the same data transformations as before in your application code.

#### Expression language

Camunda Platform 7 uses [JUEL (Java Unified Expression Language)](https://docs.camunda.org/manual/latest/user-guide/process-engine/expression-language/) as the expression language. In the embedded engine scenario, expressions can even read into beans (Java object instances) in the application.

Camunda Platform 8 uses [FEEL (Friendly-Enough Expression Language](/components/modeler/feel/what-is-feel.md) and expressions can only access the process instance data and variables.

Most expressions can be converted (see [this community extension](https://github.com/camunda-community-hub/camunda-7-to-8-migration/blob/main/modeler-plugin-7-to-8-converter/client/JuelToFeelConverter.js as a starting point), some might need to be completely rewritten, and some might require an additional service task to prepare necessary data (which may have been calculated on the fly when using Camunda Platform 7).

#### Different connector infrastructure

Camunda Platform 7 provides several [connectors](https://docs.camunda.org/manual/latest/reference/connect/). Camunda Platform 8 now also offers multiple [connectors](../components/modeler/web-modeler/connectors/available-connectors/index.md) as well.

To migrate existing connectors, create a small bridging layer to invoke these connectors via a custom [job workers](/components/concepts/job-workers.md).

### Process solutions using Spring Boot

With Camunda Platform 7, a frequently used architecture to build a process solution (also known as process applications) is composed out of:

- Java
- Spring Boot
- Camunda Spring Boot Starter with embedded engine
- Glue code implemented in Java Delegates (being Spring beans)

This is visualized on the left-hand side of the picture below. With Camunda Platform 8, a comparable process solution would look like the right-hand side of the picture and leverage:

- Java
- Spring Boot
- Spring Zeebe Starter (embeding the Zeebe client)
- Glue code implemented as workers (being Spring beans)

![spring boot](img/architecture-spring-boot.png)

The difference is that the engine is no longer embedded, which is also our latest [greenfield stack recommendation in Camunda Platform 7](/docs/components/best-practices/architecture/deciding-about-your-stack-c7/#the-java-greenfield-stack). If you are interested in the reasons why we switched our recommendation from embedded to remote workflow engines, please refer to [this blog post](https://blog.bernd-ruecker.com/moving-from-embedded-to-remote-workflow-engines-8472992cc371).

The packaging of a process solution is the same with Camunda Platform 7 and Camunda Platform 8. Your process solution is one Java application that consists of your BPMN and DMN models, as well as all glue code needed for connectivity or data transformation. The big difference is that the configuration of the workflow engine itself is not part of the Spring Boot application anymore.

![Process Solution Packaging](img/process-solution-packaging.png)

Process solution definition taken from ["Practical Process Automation"](https://processautomationbook.com/).

You can find a complete Java Spring Boot example, showing the Camunda Platform 7 process solution alongside the comparable Camunda Platform 8 process solution in the [Camunda Platform 7 to Camunda Platform 8 migration example](https://github.com/camunda-community-hub/camunda-7-to-8-migration/tree/main/example).




### Programming model

The programming model of Camunda Platform 7 and Camunda Platform 8 are very similar if you program in Java and use Spring. 

For example, a worker in Camunda Platform 8 can be implemented like this (using [spring-zeebe](https://github.com/camunda-community-hub/spring-zeebe)):

```java
@ZeebeWorker(type = "payment", autoComplete = true)
public void retrievePayment(ActivatedJob job) {
  // Do whatever you need to, e.g. invoke a remote service:
  String orderId = job.getVariablesMap().get("orderId");
  paymentRestClient.invoke(...);
}
```

You can find more information on the programming model in Camunda Platform 8 in [this blog post](https://blog.bernd-ruecker.com/how-to-write-glue-code-without-java-delegates-in-camunda-cloud-9ec0495d2ba5).

:::note
JUnit testing with an embedded in-memory engine is also possible with Camunda Platform 8, see [spring-zeebe documentation](https://github.com/camunda-community-hub/spring-zeebe#writing-test-cases). 
:::

### Platform deployment

A typical deployment of the workflow engine itself looks different because the workflow engine is no longer embedded into your own deployment artifacts. 

With Camunda Platform 7 a typical deployment includes: 

- Your Spring Boot application with all custom code and the workflow engine, cockpit, and tasklist embedded. This application is typically scaled to at least two instances (for resilience)
- A relational database
- An elastic database (for Optimize)
- Optimize (a Java application)

With Camunda Platform 8 you deploy:

- Your Spring Boot application with all custom code and the Zeebe client embedded. This application is typically scaled to at least two instances (for resilience)
- The Zeebe broker, typically scaled to at least three instances (for resilience) 
- An elastic database (for Operate, Taskliste, and Optimize)
- Optimize, Operate, and Tasklist (each one is a Java application). You can scale those application to increase availability if you want.

![Camunda Platform 7 vs Camunda Platform 8 Deployment View](img/camunda7-vs-camunda-cloud-deployment-view.png)

Camunda Platform 8 deployments happen within Kubernetes. There are [Helm charts available](https://docs.camunda.io/docs/self-managed/zeebe-deployment/kubernetes/helm/) if you want to run Camunda Platform 8 self-managed. 

Camunda Platform 8 is also available as a SaaS offering from Camunda, in this case, you only need to deploy your own process solution and Camunda operates the rest.

:::note
For local development purposes, you can [spin up Camunda Platform 8 on a developer machine using Docker or Docker Compose](https://docs.camunda.io/docs/self-managed/zeebe-deployment/docker/install/). Of course, developers could also create a cluster for development purposes in the SaaS offering of Camunda.
:::

### Other process solution architectures

Besides Spring Boot there are also other environments being used to build process solutions.

#### Container-managed engine (Tomcat, WildFly, Websphere & co)

Camunda Platform 8 doesn't provide integration into Jakarta EE application servers like Camunda Platform 7 does. Instead, Jakarta EE applications need to manually add the Zeebe client library. The implications are comparable to what is described for Spring Boot applications in this guide.

![container-managed](img/architecture-container-managed.png)

#### CDI or OSGI

Due to limited adoption, there is no support for CDI or OSGI in Camunda Platform 8. A lightweight integration layer comparable to [Spring Zeebe](https://github.com/camunda-community-hub/spring-zeebe) might evolve in the feature and we are happy to support this as a community extension to the Zeebe project.

#### Polyglot applications (C#, NodeJS, ...)

When you run your application in for example NodeJS or C#, you exchange one remote engine (Camunda Platform 7) with another (Camunda Platform 8). As Zeebe comes with a different API, you need to adjust your source code. Camunda Platform 8 does not use REST as API technology, but gRPC, and you will need to leverage a [client library](/apis-clients/overview.md) instead.

![polygot architecture](img/architecture-polyglot.png)

### Plugins

[**Process engine plugins**](https://docs.camunda.org/manual/latest/user-guide/process-engine/process-engine-plugins/) are not available in Camunda Platform 8, as such plugins can massively change the behavior or even harm the stabilty of the engine. Some use cases might be implemented using [exporters](/self-managed/concepts/exporters.md). Note that exporters are only available for self-managed Zeebe clusters and not in Camunda Platform 8 SaaS.

Migrating **Modeler Plugins** is generally possible, as the same modeler infrastructure is used. 

**Cockpit or tasklist plugins** *cannot* be migrated.


## Migration overview

Let's discuss if you need to migrate first, before diving into the necessary steps and what tools can help you achieve the migration.

### When to migrate?

New projects should typically be started using Camunda Platform 8.

Existing solutions using Camunda Platform 7 might simply keep running on Camunda Platform 7. The platform has ongoing support, so there is no need to rush on a migration project.

You should consider migrating existing Camunda Platform 7 solutions if:

- You are looking to leverage a SaaS offering (e.g. to reduce the effort for hardware or infrastructure setup and maintenance).
- You are in need of performance at scale and/or improved resilience.
- You are in need of certain features that can only be found in Camunda Platform 8 (e.g. [BPMN message buffering](/docs/components/concepts/messages/#message-buffering), big [multi-instance constructs](/docs/components/modeler/bpmn/multi-instance/), the new connectors framework, or the improved collaboration features in web modeler).


### Migration steps

For migration, you need to look at development artifacts (BPMN models and application code), but also at workflow engine data (runtime and history) in case you migrate a process solution running in production. 

The typical steps are:

1. Migrate development artifacts
   1. Adjust your BPMN models (only in rare cases you have to touch your DMN models)
   2. Adjust your development project (remove embedded engine, add Zeebe client)
   2. Refactor your code to use the Zeebe client API
   3. Refactor your glue code or use [the Java Delegate adapter project](https://github.com/camunda-community-hub/camunda-7-to-8-migration/tree/main/camunda-7-adapter).
2. Migrate workflow engine data


In general, **development artifacts** *can* be migrated:

* **BPMN models:** Camunda Platform 8 uses BPMN like Camunda Platform 7 does, which generally allows use of the same model files, but you might need to configure *different extension atrributes* (at least by using a different namespace). Furthermore, Camunda Platform 8 has a *different coverage* of BPMN concepts that are supported (see [Camunda Platform 8 BPMN coverage](/components/modeler/bpmn/bpmn-coverage.md) vs [Camunda Platform 7 BPMN coverage](https://docs.camunda.org/manual/latest/reference/bpmn20/)), which might require some model changes. Note that the coverage of Camunda Platform 8 will increase over time.

* **DMN models:** Camunda Platform 8 uses DMN like Camunda Platform 7 does. There are no changes in the models necessary. Some rarely used features of Camunda Platform 7 are not supported in Camunda Platform 8. Those are listed below.

* **CMMN models:** It is not possible to run CMMN on Zeebe, *CMMN models cannot be migrated*. You can remodel cases in BPMN according to [Building Flexibility into BPMN Models](https://camunda.com/best-practices/building-flexibility-into-bpmn-models/), keeping in mind the [Camunda Platform 8 BPMN coverage](/components/modeler/bpmn/bpmn-coverage.md).

* **Application code:** The application code needs to use *a different client library and different APIs*. This will lead to code changes you must implement.

* **Architecture:** The different architecture of the core workflow engine might require *changes in your architecture* (e.g. if you used the embedded engine approach). Furthermore, certain concepts of Camunda Platform 7 are no longer possible (like hooking in Java code at various places, or control transactional behavior with asynchronous continuations) which might lead to *changes in your model and code*.



In general, **workflow engine data** is harder to migrate to Camunda Platform 8:

* **Runtime data:** Running process instances of Camunda Platform 7 are stored in the Camunda Platform 7 relational database. Like with a migration from third party workflow engines, you can read this data from Camunda Platform 7 and use it to create the right process instances in Camunda Platform 8 in the right state. This way, you can migrate running process instances from Camunda Platform 7 to Camunda Platform 8, but some manual effort is required.

* **History data:** Historic data from the workflow engine itself cannot be migrated. However, data in Optimize can be kept.






### Migration tooling

The [Camunda Platform 7 to Camunda Platform 8 migration tooling](https://github.com/camunda-community-hub/camunda-7-to-8-migration), available as a community extension, contains two components that will help you with migration:

1. [A Desktop Modeler plugin to convert BPMN models from Camunda Platform 7 to Camunda Platform 8](https://github.com/camunda-community-hub/camunda-7-to-8-migration/tree/main/modeler-plugin-7-to-8-converter). This maps possible BPMN elements and technical attributes into the Camunda Platform 8 format and gives you warnings where this is not possible. This plugin might not fully migrate your model, but should give you a jump-start. It can be extended to add your own custom migration rules. Note that the model conversion requires manual supervision.

2. [The Camunda Platform 7 Adapter](https://github.com/camunda-community-hub/camunda-7-to-8-migration/tree/main/camunda-7-adapter). This is a library providing a worker to hook in Camunda Platform 7-based glue code. For example, it can invoke existing JavaDelegate classes.

In essence, this tooling implements details described in the next sections.



## Adjusting your source code

Camunda Platform 8 has a different API than Camunda Platform 7. As a result, you have to migrate some of your code, especially code that does the following:

* Uses the Client API (e.g. to start process instances)
* Implements [service tasks](../components/modeler/bpmn/service-tasks/service-tasks.md), which can be:
  * [Java code attached to a service task](https://docs.camunda.org/manual/latest/user-guide/process-engine/delegation-code/) and called by the engine directly (in-VM).
  * [External tasks](../components/best-practices/development/invoking-services-from-the-process-c7.md#external-tasks), where workers subscribe to the engine.


<!--
We'll explore these three cases in the sections below.

![spring boot](img/architecture-spring-boot.png)
-->

For example, to migrate an existing Spring Boot application, take the following steps:

1. Adjust Maven dependencies
  * Remove Camunda Platform 7 Spring Boot Starter and all other Camunda dependencies.
  * Add [Spring Zeebe Starter](https://github.com/camunda-community-hub/spring-zeebe).
2. Adjust config
  * Make sure to set [Camunda Platform 8 credentials](https://github.com/camunda-community-hub/spring-zeebe#configuring-camunda-cloud-connection) (for example, in `src/main/resources/application.properties`) and point it to an existing Zeebe cluster.
  * Remove existing Camunda Platform 7 settings.
3. Replace `@EnableProcessApplication` with `@EnableZeebeClient` in your main Spring Boot application class.
4. Add `@ZeebeDeployment(resources = "classpath*:**/*.bpmn")` to automatically deploy all BPMN models.

Finally, adjust your source code and process model as described in the sections below.

### Client API

All Camunda Platform 8 APIs (e.g. to start process instances, subscribe to tasks, or complete them) have been completely redesigned are not compatible with Camunda Platform 7. While conceptually similar, the APIs use different method names, data structures, and protocols.

If this affects large parts of your code base, you could write a small abstraction layer implementing the Camunda Platform 7 API delegating to Camunda Platform 8, probably marking unavailable methods as deprecated. We welcome community extensions that facilitate this but have not yet started own efforts.

### Service tasks with attached Java code (Java Delegates, Expressions)

In Camunda Platform 7, there are three ways to attach Java code to service tasks in the BPMN model using different attributes in the BPMN XML:

* Specify a class that implements a JavaDelegate or ActivityBehavior: ```camunda:class```.
* Evaluate an expression that resolves to a delegation object: ```camunda:delegateExpression```.
* Invoke a method or value expression: ```camunda:expression```.

Camunda Platform 8 cannot directly execute custom Java code. Instead, there must be a [job worker](/components/concepts/job-workers.md) executing code.

The [Camunda Platform 7 Adapter](https://github.com/camunda-community-hub/camunda-7-to-8-migration/tree/main/camunda-7-adapter) implements such a job worker using [Spring Zeebe](https://github.com/camunda-community-hub/spring-zeebe). It subscribes to the task type ```camunda-7-adapter```. [Task headers](/components/modeler/bpmn/service-tasks/service-tasks.md#task-headers) are used to configure a delegation class or expression for this worker.

![Service task in Camunda Platform 7 and Camunda Platform 8](img/migration-service-task.png)

You can use this worker directly, but more often it might serve as a starting point or simply be used for inspiration.

The [Camunda Platform 7 to Camunda Platform 8 Converter Modeler plugin](https://github.com/camunda-community-hub/camunda-7-to-8-migration/tree/main/modeler-plugin-7-to-8-converter) will adjust the service tasks in your BPMN model automatically for this adapter.

The topic ```camunda-7-adapter``` is set and the following attributes/elements are migrated and put into a task header:
* ```camunda:class```
* ```camunda:delegateExpression```
* ```camunda:expression``` and ```camunda:resultVariable```



### Service tasks as external tasks

[External task workers](https://docs.camunda.org/manual/latest/user-guide/process-engine/external-tasks/) in Camunda Platform 7 are conceptually comparable to [job workers](/components/concepts/job-workers.md) in Camunda Platform 8. This means they are generally easier to migrate.

The "external task topic" from Camunda Platform 7 is directly translated in a "task type name" in Camunda Platform 8, therefore ```camunda:topic``` gets ```zeebe:taskDefinition type``` in your BPMN model.

Now, you must adjust your external task worker to become a job worker.



## Adjusting Your BPMN models

To migrate BPMN process models from Camunda Platform 7 to Camunda Platform 8, you must adjust them:

* The namespace of extensions has changed (from ```http://camunda.org/schema/1.0/bpmn``` to ```http://camunda.org/schema/zeebe/1.0```)
* Different configuration attributes are used
* Camunda Platform 8 has a *different coverage* of BPMN elements (see [Camunda Platform 8 BPMN coverage](/components/modeler/bpmn/bpmn-coverage.md) vs [Camunda Platform 7 BPMN coverage](https://docs.camunda.org/manual/latest/reference/bpmn20/)), which might require some model changes. Note that the coverage of Camunda Platform 8 will increase over time.

The following sections describe what the existing [Camunda Platform 7 to Camunda Platform 8 migration tooling](https://github.com/camunda-community-hub/camunda-7-to-8-migration/) does by BPMN symbol and explain unsupported attributes.

### Service tasks

![Service Task](../components/modeler/bpmn/assets/bpmn-symbols/service-task.svg)

Migrating a service task is described in detail in the section about adjusting your source code above.

A service task might have **attached Java code**. In this case, the following attributes/elements are migrated and put into a task header:
* ```camunda:class```
* ```camunda:delegateExpression```
* ```camunda:expression``` and ```camunda:resultVariable```

The topic ```camunda-7-adapter``` is set.

The following attributes/elements cannot be migrated:
* ```camunda:asyncBefore```: Every task in Zeebe is always asyncBefore and asyncAfter.
* ```camunda:asyncAfter```: Every task in Zeebe is always asyncBefore and asyncAfter.
* ```camunda:exclusive```: Jobs are always exclusive in Zeebe.
* ```camunda:jobPriority```: There is no way to prioritize jobs in Zeebe (yet).
* ```camunda:failedJobRetryTimeCycle```: You cannot yet configure the retry time cycle.

A service task might leverage **external tasks** instead. In this case, the following attributes/elements are migrated:
* ```camunda:topic``` gets ```zeebe:taskDefinition type```.

The following attributes/elements cannot be migrated:
* ```camunda:taskPriority```

Service tasks using ```camunda:type``` cannot be migrated.

Service tasks using ```camunda:connector``` cannot be migrated.


### Send tasks

![Send Task](../components/modeler/bpmn/assets/bpmn-symbols/send-task.svg)

In both engines, a send task has the same behavior as a service task. A send task is migrated exactly like a service task.

### Gateways

Gateways rarely need migration. The relevant configuration is mostly in the [expressions](../components/concepts/expressions.md) on outgoing sequence flows.

### Expressions

Expressions need to be in [FEEL (friendly-enough expression language)](/components/concepts/expressions.md#the-expression-language) instead of [JUEL (Java unified expression language)](https://docs.camunda.org/manual/latest/user-guide/process-engine/expression-language/).

Migrating simple expressions is doable (as you can see in [these test cases](https://github.com/camunda-community-hub/camunda-7-to-8-migration/blob/main/modeler-plugin-7-to-8-converter/client/JuelToFeelConverter.test.js)), but not all expressions can be automatically converted.

The following is not possible:

* Calling out to functional Java code using beans in expressions
* Registering custom function definitions within the expression engine

### Human tasks

![User Task](../components/modeler/bpmn/assets/bpmn-symbols/user-task.svg)

Human task management is also available in Camunda Platform 8, but uses a different tasklist user interface and API.

In Camunda Platform 7, you have [different ways to provide forms for user tasks](https://docs.camunda.org/manual/latest/user-guide/task-forms/):

* Embedded Task Forms (embedded custom HTML and JavaScript)
* Camunda Forms (simple forms defined via Desktop Modeler properties)
* External Task Forms (link to custom applications)
* [Camunda Forms](./utilizing-forms.md)

Only Camunda Forms are currently supported in Camunda Platform 8 and can be migrated.

The following attributes/elements can be migrated:

* Task assignment (to users or groups):
  * ```bpmn:humanPerformer```
  * ```bpmn:potentialOwner```
  * ```camunda:assignee```
  * ```camunda:candidateGroups```
  * ```camunda:formKey```, but Camunda Platform 8 requires you to embedd the form definition itself into the root element of your BPMN XML models, see [this guide](/docs/guides/utilizing-forms/#connect-your-form-to-a-bpmn-diagram).

The following attributes/elements cannot (yet) be migrated:

* ```camunda:candidateUsers``` (only candidate groups are supported)
* Form handling:
  * ```camunda:formHandlerClass```
  * ```camunda:formData```
  * ```camunda:formProperty```
* ```camunda:taskListener```
* ```camunda:dueDate```
* ```camunda:followUpDate```
* ```camunda:priority```

### Business rule tasks

![Business Rule Task](../components/modeler/bpmn/assets/bpmn-symbols/business-rule-task.svg)

Camunda Platform 8 support the DMN standard just as Camunda Platform 7 does, so the business rule task can basically be migrated. 

The following attributes/elements can be migrated:
* ```camunda:decisionRef```

The following attributes are not yet supported:

* ```camunda:decisionRefBinding```, ```camunda:decisionRefVersion```, and ```camunda:decisionRefVersionTag```(always use the latest version)
* ```camunda:mapDecisionResult``` (no mapping happens)
* ```camunda:resultVariable``` (result is always mapped to variable 'result' and can be copied or unwrapped using ioMapping).
* ```camunda:decisionRefTenantId```

A business rule task can also *behave like a service task* to allow integration of third-party rule engines. In this case, the following attributes can also be migrated as described above for the service task migration: ```camunda:class```, ```camunda:delegateExpression```, ```camunda:expression```, or ```camunda:topic```.

The following attributes/elements cannot be migrated:
* ```camunda:asyncBefore```, ```camunda:asyncBefore```, ```camunda:asyncAfter```, ```camunda:exclusive```, ```camunda:failedJobRetryTimeCycle```, and ```camunda:jobPriority```
* ```camunda:type``` and ```camunda:taskPriority```
* ```camunda:connector```


### Call activities

![Call Activity](../components/modeler/bpmn/assets/bpmn-symbols/call-activity.svg)

Call activities are generally supported in Zeebe. The following attributes/elements can be migrated:

* ```camunda:calledElement``` will be converted into ```zeebe:calledElement```
* Data Mapping
  * ```camunda:in```
  * ```camunda:out```

The following attributes/elements cannot be migrated:
* ```camunda:calledElementBinding```: Currently Zeebe always assumes 'late' binding.
* ```camunda:calledElementVersionTag```: Zeebe does not know a version tag.
* ```camunda:variableMappingClass```: You cannot execute code to do variable mapping in Zeebe.
* ```camunda:variableMappingDelegateExpression```: You cannot execute code to do variable mapping in Zeebe.



### Script task

![Script Task](../components/modeler/bpmn/assets/bpmn-symbols/script-task.svg)

Script tasks cannot natively be executed by the Zeebe engine. They behave like normal service tasks instead, which means you must run a job worker that can execute scripts. One available option is to use the [Zeebe Script Worker](https://github.com/camunda-community-hub/zeebe-script-worker), provided as a community extension.

If you do this, the following attributes/elements are migrated:
* ```camunda:scriptFormat```
* ```camunda:script```
* ```camunda:resultVariable```

The task type is set to ```script```.

The following attributes/elements cannot be migrated:
* ```camunda:asyncBefore```: Every task in Zeebe is always asyncBefore and asyncAfter.
* ```camunda:asyncAfter```: Every task in Zeebe is always asyncBefore and asyncAfter.
* ```camunda:exclusive```: Jobs are always exclusive in Zeebe.
* ```camunda:jobPriority```: There is no way to priotize jobs in Zeebe (yet).
* ```camunda:failedJobRetryTimeCycle```: You cannot yet configure the retry time cycle.

### Message receive events and receive tasks

Message correlation works slightly different between the two products:

* Camunda Platform 7 simply waits for a message, and the code implementing that the message is received queries for a process instance the message will be correlated to. If no process instance is ready to receive that message, an exception is raised.

* Camunda Platform 8 creates a message subscription for every waiting process instance. This subscription requires a value for a ```correlationKey``` to be generated when entering the receive task. The code receiving the external message correlates using the value of the ```correlationKey```.

This means you must inspect and adjust all message receive events or receive tasks in your model to define a reasonable ```correlationKey```. You also must adjust your client code accordingly.

The ```bpmn message name``` is used in both products and doesn't need migration.


## Adjusting your DMN models

For Camunda Platform 8, [a former community extension](https://github.com/camunda-community-hub/dmn-scala), built by core Camunda developers, is productized. This engine has a higher coverage of DMN elements. This engine can execute DMN models designed for Camunda Platform 7, however, there are some small differences listed below.

You can use the above mentioned tooling to convert your DMN models from Camunda Platform 7 to Camunda Platform 8.

The following elements/attributes are not supported :

  * `Version Tag` is not supported in Camunda Platform 8
  * `History Time to Live` is not supported in Camunda Platform 8
  * You cannot select the `Expression Language` in Camunda Platform 8, only FEEL is supported
  * The property `Input Variable` is removed, in FEEL, the input value can be accessed by using `?` if needed

Furthermore, there are changes that might interesting, but legacy behavior can still be executed:
  * Removed data types `integer` + `long` + `double` in favor of `number` for inputs and outputs (in FEEL, there is only a number type represented as `BigDecimal`) 
  


## Prepare for smooth migrations

Whenever you build a process solution using Camunda Platform 7, you can follow these recommendations to create a process solution that will be easier to migrate later on:

* Use Java, Maven, and Spring Boot.
* Separate your business logic from Camunda API.
* Use external tasks.
* Stick to basic usage of public API (no engine plugins or extensions).
* Don't expose Camunda Platform 7 APIs (REST or Java) to front-end applications.
* Use primitive variable types or JSON payloads only (no XML or serialized Java objects).
* Use JSONPath on JSON payloads (translates easier to FEEL).
* Stick to [BPMN elements supported in Camunda Platform 8](/components/modeler/bpmn/bpmn-coverage.md).
* Use [FEEL as script language in BPMN](https://camunda.github.io/feel-scala/docs/reference/developer-guide/bootstrapping#use-as-script-engine), e.g. on Gateways.
* Use Camunda Forms.

## Open issues

As described earlier in this guide, migration is an ongoing topic and this guide is far from complete. Open issues include the following:

* Describe implications on testing.
* Discuss adapters for Java or REST client.
* Discuss external task adapter for Java code and probably add it to the [Camunda Platform 7 Adapter](https://github.com/camunda-community-hub/camunda-7-to-8-migration/tree/main/camunda-7-adapter).
* Discuss more concepts around BPMN
** [Field injection](https://docs.camunda.org/manual/latest/user-guide/process-engine/delegation-code/#field-injection) that is using ```camunda:field``` available on many BPMN elements.
** Multiple instance markers available on most BPMN elements.
** ```camunda:inputOutput``` available on most BPMN elements.
** ```camunda:errorEventDefinition``` available on several BPMN elements.

Please [reach out to us](/contact/) to discuss your specific migration use case.

## Summary

In this guide, you hopefully gained a better understanding of what migration from Camunda Platform 7 to Camunda Platform 8 means. Specifically, this guide outlined the following:

* Differences in application architecture
* How process models and code can generally be migrated, whereas runtime and history data cannot
* How migration can be very simple for some models, but also marked limitations, where migration might get very complicated
* You need to adjust code that uses the workflow engine API
* How you might be able to reuse glue code
* Community extensions that can help with migration

We are watching all customer migration projects closely and will update this guide in the future.
