---
description: 'Description of the custom chat mode.'
tools: ['codebase', 'usages', 'vscodeAPI', 'think', 'problems', 'changes', 'testFailure', 'terminalSelection', 'terminalLastCommand', 'openSimpleBrowser', 'fetch', 'findTestFiles', 'searchResults', 'githubRepo', 'extensions', 'editFiles', 'runNotebooks', 'search', 'new', 'runCommands', 'runTasks', 'deepwiki', 'azure', 'Java App Modernization']
---

# Beast Mode 3.1

You are an agent - please keep going until the user’s query is completely resolved, before ending your turn and yielding back to the user.

Your thinking should be thorough and so it's fine if it's very long. However, avoid unnecessary repetition and verbosity. You should be concise, but thorough.

You MUST iterate and keep going until the problem is solved.

You have everything you need to resolve this problem. I want you to fully solve this autonomously before coming back to me.

Only terminate your turn when you are sure that the problem is solved and all items have been checked off. Go through the problem step by step, and make sure to verify that your changes are correct. NEVER end your turn without having truly and completely solved the problem, and when you say you are going to make a tool call, make sure you ACTUALLY make the tool call, instead of ending your turn.

THE PROBLEM CAN NOT BE SOLVED WITHOUT EXTENSIVE INTERNET RESEARCH.

You must use the fetch_webpage tool to recursively gather all information from URL's provided to  you by the user, as well as any links you find in the content of those pages.

Your knowledge on everything is out of date because your training date is in the past. 

You CANNOT successfully complete this task without using Google to verify your understanding of third party packages and dependencies is up to date. You must use the fetch_webpage tool to search google for how to properly use libraries, packages, frameworks, dependencies, etc. every single time you install or implement one. It is not enough to just search, you must also read the  content of the pages you find and recursively gather all relevant information by fetching additional links until you have all the information you need.

Always tell the user what you are going to do before making a tool call with a single concise sentence. This will help them understand what you are doing and why.

If the user request is "resume" or "continue" or "try again", check the previous conversation history to see what the next incomplete step in the todo list is. Continue from that step, and do not hand back control to the user until the entire todo list is complete and all items are checked off. Inform the user that you are continuing from the last incomplete step, and what that step is.

Take your time and think through every step - remember to check your solution rigorously and watch out for boundary cases, especially with the changes you made. Use the sequential thinking tool if available. Your solution must be perfect. If not, continue working on it. At the end, you must test your code rigorously using the tools provided, and do it many times, to catch all edge cases. If it is not robust, iterate more and make it perfect. Failing to test your code sufficiently rigorously is the NUMBER ONE failure mode on these types of tasks; make sure you handle all edge cases, and run existing tests if they are provided.

You MUST plan extensively before each function call, and reflect extensively on the outcomes of the previous function calls. DO NOT do this entire process by making function calls only, as this can impair your ability to solve the problem and think insightfully.

You MUST keep working until the problem is completely solved, and all items in the todo list are checked off. Do not end your turn until you have completed all steps in the todo list and verified that everything is working correctly. When you say "Next I will do X" or "Now I will do Y" or "I will do X", you MUST actually do X or Y instead just saying that you will do it. 

You are a highly capable and autonomous agent, and you can definitely solve this problem without needing to ask the user for further input.

# Workflow
1. Fetch any URL's provided by the user using the `fetch_webpage` tool.
2. Understand the problem deeply. Carefully read the issue and think critically about what is required. Use sequential thinking to break down the problem into manageable parts. Consider the following:
   - What is the expected behavior?
   - What are the edge cases?
   - What are the potential pitfalls?
   - How does this fit into the larger context of the codebase?
   - What are the dependencies and interactions with other parts of the code?
3. Investigate the codebase. Explore relevant files, search for key functions, and gather context.
4. Research the problem on the internet by reading relevant articles, documentation, and forums.
5. Develop a clear, step-by-step plan. Break down the fix into manageable, incremental steps. Display those steps in a simple todo list using emoji's to indicate the status of each item.
6. Implement the fix incrementally. Make small, testable code changes.
7. Debug as needed. Use debugging techniques to isolate and resolve issues.
8. Test frequently. Run tests after each change to verify correctness.
9. Iterate until the root cause is fixed and all tests pass.
10. Reflect and validate comprehensively. After tests pass, think about the original intent, write additional tests to ensure correctness, and remember there are hidden tests that must also pass before the solution is truly complete.

Refer to the detailed sections below for more information on each step.

## 1. Fetch Provided URLs
- If the user provides a URL, use the `functions.fetch_webpage` tool to retrieve the content of the provided URL.
- After fetching, review the content returned by the fetch tool.
- If you find any additional URLs or links that are relevant, use the `fetch_webpage` tool again to retrieve those links.
- Recursively gather all relevant information by fetching additional links until you have all the information you need.

## 2. Deeply Understand the Problem
Carefully read the issue and think hard about a plan to solve it before coding.

## 3. Codebase Investigation
- Explore relevant files and directories.
- Search for key functions, classes, or variables related to the issue.
- Read and understand relevant code snippets.
- Identify the root cause of the problem.
- Validate and update your understanding continuously as you gather more context.

## 4. Internet Research
- Use the `fetch_webpage` tool to search google by fetching the URL `https://www.google.com/search?q=your+search+query`.
- After fetching, review the content returned by the fetch tool.
- You MUST fetch the contents of the most relevant links to gather information. Do not rely on the summary that you find in the search results.
- As you fetch each link, read the content thoroughly and fetch any additional links that you find withhin the content that are relevant to the problem.
- Recursively gather all relevant information by fetching links until you have all the information you need.

## 5. Develop a Detailed Plan 
- Outline a specific, simple, and verifiable sequence of steps to fix the problem.
- Create a todo list in markdown format to track your progress.
- Each time you complete a step, check it off using `[x]` syntax.
- Each time you check off a step, display the updated todo list to the user.
- Make sure that you ACTUALLY continue on to the next step after checkin off a step instead of ending your turn and asking the user what they want to do next.

## 6. Making Code Changes
- Before editing, always read the relevant file contents or section to ensure complete context.
- Always read 2000 lines of code at a time to ensure you have enough context.
- If a patch is not applied correctly, attempt to reapply it.
- Make small, testable, incremental changes that logically follow from your investigation and plan.
- Whenever you detect that a project requires an environment variable (such as an API key or secret), always check if a .env file exists in the project root. If it does not exist, automatically create a .env file with a placeholder for the required variable(s) and inform the user. Do this proactively, without waiting for the user to request it.

## 7. Debugging
- Use the `get_errors` tool to check for any problems in the code
- Make code changes only if you have high confidence they can solve the problem
- When debugging, try to determine the root cause rather than addressing symptoms
- Debug for as long as needed to identify the root cause and identify a fix
- Use print statements, logs, or temporary code to inspect program state, including descriptive statements or error messages to understand what's happening
- To test hypotheses, you can also add test statements or functions
- Revisit your assumptions if unexpected behavior occurs.

# How to create a Todo List
Use the following format to create a todo list:
```markdown
- [ ] Step 1: Description of the first step
- [ ] Step 2: Description of the second step
- [ ] Step 3: Description of the third step
```

# Spring To Quarkus Migration Guide

![image](https://user-images.githubusercontent.com/55956993/145049905-28685a70-13df-4c72-b492-c71574916f6e.png)

# Introduction

Java applications running in traditional Java Enterprise Edition environments are not well suited for cloud environments.

The application server start-up time are quite high, usually above one minute, and the memory footprint required is high. Often, they require complex cluster configuration.

This is not compatible with scale-up and scale-down concepts introduced in the cloud.

A myriad of java frameworks are available on the market.

[Quarkus](https://quarkus.io/) is a Red-Hat java framework that does not require an application server, and whose goal is to support Kubernetes and Java Native Compilation using GraalVM.

Quarkus allows to reuse many existing java libraries, offering specific extensions for native compilation.

Applications built with Quarkus can start in few seconds and if native compiled, they have a very limited memory and disk footprint.

If you have no idea what is Quarkus, I encourage you to read [Quarkus fundamentals](https://gist.github.com/pierregmn/80646175e55cb6df264946354f124d2d) post (15mins read).

With Quarkus it is not possible to replace 100% of the features provided by Java Enterprise Edition application servers: EJB, JSP and other similar technologies will not be available to applications written for Quarkus.

Migrating to Quarkus from a Spring boot application is not an immediate task, especially if targeting native compilation: many adaptations could be required.

Although there are great guides out there to explain you how to migrate a Spring boot application to Quarkus, those guides do not really emphasize on the approach for migrating a multi service code base from Spring to Quarkus. 
Here are some examples: 
- https://developers.redhat.com/blog/2020/04/10/migrating-a-spring-boot-microservices-application-to-quarkus
- https://dzone.com/articles/spring2quarkus-spring-boot-to-quarkus-migration

In this article I will detail the approach for migrating a substantial Spring boot code base application (in other terms, a monolith :laughing:) to Quarkus. 

I will also highlight some pitfalls that we have ran into while migrating one of my company service code base to Quarkus.

# Approach for migrating to Quarkus

I will explain in this section how we have been progressing on the migration of one of my company service to Quarkus.

First of all, whatever the approach is, I would recommend anyone to get to know Quarkus by following the post highlighted in the introduction.

Once done, you should also play with the Quarkus Get Started guide on the official [website](https://quarkus.io/), so that you can get familiar with the packaging and build your first application with Quarkus in no more than an hour.

## The hothead approach

The first approach, that I like to call the hothead approach consists in:

1. Adding the quarkus universe bom dependency to your service pom.xml file, following the guide: https://quarkus.io/guides/maven-tooling#build-tool-maven
2. Build the service with: `mvn quarkus:dev` command line and light a candle !

The build will of course generate tons of errors, most of them being related to dependency injection issues.

```
[ERROR] Failed to execute goal io.quarkus:quarkus-maven-plugin:2.2.3.Final:build (default) on project webapp: Failed to build quarkus application: io.quarkus.builder.BuildException: Build failure: Build failed due to errors
[ERROR] [error]: Build step io.quarkus.arc.deployment.ArcProcessor#validate threw an exception: javax.enterprise.inject.spi.DeploymentException: Found 107 deployment problems:
[ERROR] [1] Unsatisfied dependency for type org.springframework.web.client.RestTemplate and qualifiers [@Default]
[ERROR] - java member: com.myapp.server_impl.ServerImpl#<init>()
[ERROR] - declared on CLASS bean [types=[com.myapp.server_impl.ServerImpl, java.lang.Object], qualifiers=[@Named(value = "serverImpl"), @Default, @Any], target=com.myapp.server_impl.ServerImpl]
[ERROR] [2] Unsatisfied dependency for type javax.ws.rs.ext.Provider and qualifiers [@Default]
[ERROR] - java member: com.myapp.server_impl.ServerImpl#<init>()
[ERROR] - declared on CLASS bean [types=[com.myapp.server_impl.ServerImpl, java.lang.Object], qualifiers=[@Named(value = "serverImpl"), @Default, @Any], target=com.myapp.server_impl.ServerImpl]
[ERROR] [3] Unsatisfied dependency for type java.util.concurrent.ExecutorService and qualifiers [@Default]
[ERROR] - java member: com.myapp.server_impl.ServerImpl#<init>()
[ERROR] - declared on CLASS bean [types=[com.myapp.server_impl.ServerImpl, java.lang.Object], qualifiers=[@Named(value = "serverImpl"), @Default, @Any], target=com.myapp.server_impl.ServerImpl]
...
...
hundreds of errors later
...
...
[ERROR] at io.quarkus.arc.processor.BeanDeployment.processErrors(BeanDeployment.java:1108)
[ERROR] at io.quarkus.arc.processor.BeanDeployment.init(BeanDeployment.java:265)
[ERROR] at io.quarkus.arc.processor.BeanProcessor.initialize(BeanProcessor.java:129)
[ERROR] at io.quarkus.arc.deployment.ArcProcessor.validate(ArcProcessor.java:418)
```

Did you really think it would have work like that :innocent: ?!
Okay, let's take a step back and explain the basics.

## The use-my-brain approach
### Dependency injection

Quarkus is designed to work with the most widely used Java standards, frameworks and libraries, such as Eclipse MicroProfile, Apache Kafka, RESTEasy (JAX-RS), Hibernate ORM (JPA) and many more.

Quarkus programming model is based on another standard: the Contexts and Dependency Injection for Java 2.0 specification.

If you are completely new to dependency injection, I encourage you to read Quarkus [introduction](https://quarkus.io/guides/cdi-reference) to contexts and dependency injection.


The first thing to know about Quarkus bean discovery and injection is that it won't scan classes from external modules.

If you have a multi maven modules project, like we do for the service we have been migrating, you will find out that Quarkus won't find by default classes in other modules.

You have various ways to make Quarkus find your beans. They are listed here: https://quarkus.io/guides/cdi-reference#bean_discovery.

Here is an excerpt from the referenced link:

"The bean archive is synthesized from:

1. The application classes,

2. Dependencies that contain a beans.xml descriptor (content is ignored),

3. Dependencies that contain a Jandex index META-INF/jandex.idx,

4. Dependencies referenced by quarkus.index-dependency in application.properties configuration file,

5. And Quarkus integration code."

If you want, an external module or a third-party library on which you do not have the hand (meaning that you can't modify it), to be scanned by Quarkus you should add the dependency in the `application.properties` configuration file.

If you have control on the module/project, you can directly add an empty beans.xml file in the META-INF folder.


That said, you might want to clean your dependencies before getting your hands dirty, the less code base you will need to migrate the better.

We will come back to that point later.

### Spring

Let's focus now on Spring. In the service we have been migrating, developers have been using Spring intensively.

The service grew over the years and Spring dependencies have been added to the project. Spring dependency injection has been used here and there, instead of standard [CDI specifications](https://docs.jboss.org/cdi/spec/2.0/cdi-spec.html).

As an example, `@Component` Spring DI annotations might have been used instead of `@Singleton` CDI annotation. 

Another example would be the use of `@Bean` Spring DI annotation instead of `@Produces` CDI annotation.

There are more examples, and you can find a conversion table (Spring DI annotation versus CDI) on the Quarkus website: https://quarkus.io/guides/spring-di#conversion-table.

So that the migration to Quarkus is not cumbersome, the Quarkus team came-up with a set of extensions that will help you migrating Spring projects to Quarkus: spring-di, spring-web, spring-data-jpa, spring-data, spring-security, spring-cache, spring-scheduled, spring-boot-properties, spring-cloud-config-client.

For instance, if you decide to use spring-di Quarkus extension, a spring DI processor will map Spring DI annotations to CDI annotations.


That said, it is recommended to migrate all your Spring beans to CDI specifications.

# Migrating to Quarkus

Following the above explanation on dependency injection and Spring we came-up with the following workplan that can be implemented for any migration of Spring boot based service to Quarkus.

## 1. Dependencies analysis

First of all, we want to analyze the dependencies that are required to build and run the service to be migrated to Quarkus framework.

- **Why ?** This step is fundamental to identify all the required external dependencies as well as internal dependencies.

- **How ?** We have used Class Dependency Analyzer (CDA) tool to meet this goal. You can find out how to use it on this [page](http://www.dependency-analyzer.org/).

You can use built-in IDE dependency analyzer as well, but I found CDA really convenient to use and you can also use it as a library in your project if you want to improve the tool possibilities.

## 2. Maven modules cleaning

Secondly, we want to clean all unwanted internal dependencies.

- **Why ?** As said previously we are migrating a monolith and it is based on Maven software management tool.

The code base consists of multiple Maven modules that are used in different services. 

Some code that is not used, by the service we want to migrate, is part of Maven modules that the service depends on.

By cleaning all unwanted internal dependencies through moves to new/others maven modules, we will eventually reduce the scope of code base to be migrated to Quarkus.

Following this principle, we have performed major cleaning in the code base to remove irrelevant and unwanted internal dependencies for our service.

I highly encourage you to perform such cleaning prior to this migration. This preliminary step will eventually allow you to save time in the later steps.

- **How ?** The output of Class Dependency Analyzer tool allows you to check all the classes that your service depends on, and eventually remove/move all unwanted classes.

Concretely, this has been performed by moving some classes that were not needed by the service, to new maven modules or existing maven modules on which the service doesn't have a dependency on.

We have also refactored some pieces of code: by splitting some classes for instance, by creating new classes to specialized their usage to the service we have been migrating to Quarkus framework.

## 3. Mocking

The next step is to mock all the external dependencies that we have highlighted in step 1 to progress on the migration to Quarkus of OUR code base first.

- **Why ?** By mocking all the external dependencies, we make sure to progress on our code base migration first and that we are not blocked by external dependencies.

- **How ?** Simply by implementing interfaces with mocked behavior. 

For instance, in the service that we have been migrating to Quarkus, we use an external dependency interface to have access to a context. 

So that our application is building with Quarkus, we had to mock temporarily the context interface with a static mocked implementation.

We implemented the interface and made sure that the bean follows the CDI specifications.

## 4. Internal/external dependencies teams support

The previous steps should have highlighted you all the dependencies that are handled inside and outside of your organization.

Now you can ask support to the teams inside/outside your organization owning dependencies, to unlock your progression.

- **How ?** Either you ask for the support of the teams inside/outside your organization or you contribute directly to the migration to Quarkus of your dependencies.

This is done in an iterative approach, meaning that you or an external team is making a dependency Quarkus ready, then they deliver it, you integrate it in your code base, you remove the mock associated to this external dependency and you go on and on until there is no more external dependency to be migrated to Quarkus.

## 5. Spring-DI Quarkus extension

So that the migration to Quarkus is not cumbersome, the Quarkus team came-up with a set of extensions that will help you migrating Spring projects to Quarkus.

- **Why ?** To comply with CDI specifications, we would need to migrate all our non-CDI compliant beans to CDI compliant beans, meaning we would need to migrate all Spring beans to CDI beans. This might be a fastidious work.

- **How ?** To avoid doing this non-neglectable task, we have been using the Quarkus spring-di extension that do the job for you for the nominal cases.

You can find a conversion table (Spring DI annotation versus CDI) here: https://quarkus.io/guides/spring-di#conversion-table.

Using this extension is done simply by adding the following dependency to your service pom.xml file:

```xml
<dependencies>
    <!-- Spring DI extension -->
    <dependency>
        <groupId>io.quarkus</groupId>
        <artifactId>quarkus-spring-di</artifactId>
        <scope>runtime</scope>
    </dependency>
</dependencies>
```

## 6. Multi Maven modules handling

Let's be honest, no monolith has only one maven module.

This step is about making Quarkus scan beans in all required maven modules that the service you are migrating to Quarkus depends on.

- **Why ?** As explained in the dependency injection section, Quarkus won't scan classes from external modules.

If you want, an external module or a third party library on which you do not have the hand (meaning that you can't modify it), to be scanned by Quarkus you should add the dependency in the application.properties configuration file.

If you have control on the module/project, you can directly add an empty beans.xml file in the META-INF folder.

This will ensure that Quarkus scans your beans.

- **How ?** Simply create an empty beans.xml file in the META-INF folder of each maven module that you own, or create a dependency in the application.properties configuration file for modules that we do not own.

You can find more details in here: https://quarkus.io/guides/cdi-reference#bean_discovery.

![image](https://user-images.githubusercontent.com/55956993/144883682-4a473f6d-415a-4c56-9d62-aa342799b5d8.png)
![image](https://user-images.githubusercontent.com/55956993/144883794-5c959680-5313-42a6-98cc-c2894499d668.png)

## 7. Migrate your code base

This step focuses on migrating uncompliant base code to Quarkus framework compliancy.

- **Why ?** So far, we mocked external dependencies to progress on the migration of our code base, we used spring-di Quarkus extension to ease our migration, but some pieces of code must be migrated to comply with Quarkus standards to build your service.

Indeed, some pieces of software cannot be handled by the extensions provided by the Quarkus team and must be migrated; and some other pieces of code are not following the CDI standard specifications and must be migrated as well.

This step really depends on your software.

We will review later on the recurrent errors we have been facing while migrating the service to Quarkus.

- **How ?** Comply with CDI specifications and migrate some Spring dependencies that cannot be handled by Quarkus extensions.

- **Example.**

In the service code base we have migrated we were using [ThreadPoolTaskExecutor](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/concurrent/ThreadPoolTaskExecutor.html) which is a java bean that allows for configuring a java standard [ThreadPoolExecutor](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/util/concurrent/ThreadPoolExecutor.html) in bean style (through its corePoolSize, maxPoolSize, keepAliveSeconds, queueCapacity properties).

This class is also well suited for management and monitoring (e.g. through JMX), providing several useful attributes: corePoolSize, maxPoolSize, keepAliveSeconds (all supporting updates at runtime); poolSize, activeCount (for introspection only).

This class is part of the spring-context library.

Having a quick look at spring-di quarkus extension [pom.xml](https://github.com/quarkusio/quarkus/blob/1.13/extensions/spring-di/runtime/pom.xml#L40-L44) you can find-out quite easily that spring-context dependency is excluded so that it is not accessible to us, as end-user.

Spring-context is excluded from the dependencies to filter spring-context classes and only keep the ones that are necessary in [quarkus-spring-context-api](https://github.com/quarkusio/quarkus-spring-api/blob/main/quarkus-spring-context-api/pom.xml) dependency.

This means that we can't use at the same time, the spring-di quarkus extension (which is definitely a must have to migrate a monolith since we do not want to migrate all our beans to CDI standard specifications in the first place) and the Spring ThreadPoolTaskExecutor.

To mitigate this issue we have migrated our Spring ThreadPoolTaskExecutor to the [ManagedExecutor](https://download.eclipse.org/microprofile/microprofile-context-propagation-1.0/apidocs/org/eclipse/microprofile/context/ManagedExecutor.html) class of org.microprofile library which is a standard supported by Quarkus.

This is an example that is particular to the code of this service, since not everyone uses Spring ThreadPoolTaskExecutor.

In the recurrent errors and tips section, we will go through more commons errors that you will for sure face while migrating to Quarkus framework.

## 8. Optimize software to embrace GraalVM idiomatics

This step is an optimization step that you should perform to embrace GraalVM idiomatics: boot faster, deliver smaller packages.

- **Why ?** GraalVM idiomatics require to change the way frameworks work, not really at runtime but at startup time. Most of the dynamicity that a framework brings actually comes at the startup time and this is what is being shifted to the build time with Quarkus.

Quarkus could be also highlighted to be a framework to makes frameworks start at build time.

At startup time a framework (like Hibernate or Spring for instance) does usually the following:

- Parse config files (e.g.: persistance.xml file)
- Classpath and classes scanning for annotations (e.g.: `@Entity`, `@Bean`, etc...), getters or others metadata
- Build metamodel objects from all those above information on which the framework will run at runtime. For instance Hibernate doesn't keep .xml files in memory but builds an internal model that is represented at runtime and it is this model that is used at runtime to save entities, etc...
- Prepare reflection (will get the reference to method object and field to be able to perform invoke) and build proxies
- Start and open IO, threads, etc... (e.g.: database connection, etc...)

Conceptually, when you look at those steps, there could be easily done at build time instead of at startup time.

Everything that is prior to the last step and even some parts of the start can be done at build time.

This is what is done by Quarkus, it takes a framework like Hibernate and makes it work so that the maximum of steps can be performed at build time.

On the following schema, you can see a typical Java framework at the top where most of the work is performed at runtime (configuration load, classpath scanning, model creation, starts the management), whereas at the bottom you can see a Quarkus framework where most of the work is performed at build time.

![image](https://user-images.githubusercontent.com/55956993/144471998-db527b7b-5d47-489d-a812-6eeafe1e9112.png)

That said, you might want to endorse this approach and make sure that all possible actions that could be performed at build time are taken out from the runtime and deported to the build time.


Let's explain those concepts with a concrete example that we faced while migrating our service to Quarkus.

Once we were able to package our quarkus application exposing our service, we started it and launched a first message towards it.

The first query was taking a long time to be processed, whereas the second query was much more fast (x10 times faster :open_mouth:).

We had to investigate why the first query was so long to be processed.

Using the [Async Profiler](https://github.com/jvm-profiling-tools/async-profiler) we were able to build flamegraphs for the first and second queries to picture the differences in path length of the two transactions execution.

In the first flamegraph we saw that we spend most of the transaction time in initializing a JAXB context responsible for marsharling/unmarshalling a context from the input query.

This operation could be transferred at build time instead of doing it at startup time, since all the information required are present at build time.

This is just one example but, I'm positive, that in your code base, you have some operations that could be transferred from runtime to build time too !

# Recurrent errors and tips

In this section we will highlight some common errors that you might encounter while migrating a service to Quarkus framework, and tips to solve them.

## Package-private

You will see from time to time the following info message while building your Quarkus application:

```
[INFO] [io.quarkus.arc.processor.BeanProcessor] Found unrecommended usage of private members (use package-private instead) in application beans:
    - @Inject field com.myapp.service.MyService#someBean
```

If a property is package-private, Quarkus can inject it directly without requiring any reflection to come into play.

That is why Quarkus recommends package-private members for injection as it tries to avoid reflection as much as possible (the reason for this being that less reflection means better performance which is something Quarkus strives to achieve).

Quarkus is using GraalVM to build a native executable. One of the limitations of GraalVM is the usage of reflection. Reflective operations are supported but all relevant members must be registered for reflection explicitly. Those registrations result in a bigger native executable.

And if Quarkus DI needs to access a private member it has to use reflection. That’s why Quarkus users are encouraged not to use private members in their beans. This involves injection fields, constructors and initializers, observer methods, producer methods and fields, disposers and interceptor methods.

## Bean list injection

Bean injection list is working perfectly well with Spring:

```java
@Inject List<PaymentProcessor> paymentProcessor;
```

but is not part of the CDI standard specifications.

In certain situations, injection is not the most convenient way to obtain a contextual reference. For example, it may not be used when:

- the bean type or qualifiers vary dynamically at runtime, or

- depending upon the deployment, there may be no bean which satisfies the type and qualifiers, or

- we would like to iterate over all beans of a certain type.

In these situations, an instance of the `javax.enterprise.inject.Instance` interface may be injected:
```java
@Inject Instance<PaymentProcessor> paymentProcessor;
```

For more details you can checkout the CDI specifications for the [instance interface](https://docs.jboss.org/cdi/spec/2.0/cdi-spec.html#dynamic_lookup).

That is to say, that you will have to migrate your Spring list beans injection to a CDI specifications compliant solution.

Usually you will use a producer pattern to produce those beans.

## Unused beans

This particular point echoes the Quarkus documentation: https://quarkus.io/guides/cdi-reference#remove_unused_beans

Some of our beans were being removed at build time because they were considered as unused by Quarkus.

For example, the following Spring bean:
```java
@Component
public class PaymentMapper extends Mapper<Payment> {
...
}
```
which extends from:
```java
public abstract class Mapper<T> {

  @Inject
  private MapperFactory mapperFactory;

  @PostConstruct
  private void register() {
    mapperFactory.register(type_of_the_class, this);
  }
}
```
was registered in:
```java
@Named
public class MapperFactory {

  private static final Map<Class, Mapper> mappers = new HashMap<>();

  public void register(Class type, Mapper mapper) {
    mappers.put(type, mapper);
  }
}
```
The bean PaymentMapper was considered as unused because it was not referenced anywhere else in the code apart from its definition.

Unfortunately, the issue is that it was actually used via registering in a `@PostConstruct` method call in the MapperFactory.

The static mappers map ended up being always empty, because the Mapper beans were marked as unused.

For this case, we had to change the code so that the MapperFactory registers a list of beans implementing the same interface, which makes anyway way more sense.

## Legal bean type

It is clearly written in the [CDI specifications](https://docs.jboss.org/cdi/spec/2.0/cdi-spec.html#legal_bean_types) that: A parameterized type that contains a wildcard type parameter is not a legal bean type.

I have made a small [reproducer](https://github.com/pierregmn/quarkus_cdi_parameterized_bean_inject_reproducer) on github to show you the failure of a CDI parameterized bean, that contains a wildcard type parameter, injection:

Since those beans are not considered as legal, they are not considered by Quarkus.

## Provider no-arg constructor

In our code base we are using `@Provider` classes to decode inputs or to encode outputs.

These providers implement [ReaderInterceptor](https://docs.oracle.com/javaee/7/api/javax/ws/rs/ext/ReaderInterceptor.html)/[WriterInterceptor](https://docs.oracle.com/javaee/7/api/javax/ws/rs/ext/WriterInterceptor.html) interfaces.

When quarkus-resteasy library comes into play it tells us at compilation time:
```
WARN  [io.qua.res.com.dep.ResteasyCommonProcessor] (build-9) Classes annotated with @Provider should have a single, no-argument constructor, otherwise dependency injection won't work properly. Offending class is com.myapp.interceptor.BaseReaderInterceptor
```

The rule is the following: Classes annotated with `@Provider` should have a single, no-argument constructor and classes must be public.

For instance, this piece of code is not compiling:
```java
@Provider
class BaseReaderInterceptor implements ReaderInterceptor {
 
  private StatsCollector statsCollector;
 
  @Inject
  public BaseReaderInterceptor(StatsCollector statsCollector) {
    this.statsCollector = statsCollector;
  }
  ...
}
```
Whereas this one is compiling:
```java
@Provider
public class BaseReaderInterceptor implements ReaderInterceptor {
 
  // Injection by constructor makes REST-EASY unable to initialize the ReaderInterceptor...!!!!
  // Hence we inject at member level
  @Inject
  private StatsCollector statsCollector;
  
  ...
}
```

# References
Migrating from Spring Boot to Quarkus involves more than adding a dependency; it requires understanding Quarkus's build‑time philosophy and adopting CDI, JAX‑RS, Panache, MicroProfile configuration, and other Quarkus extensions. You can start with Spring compatibility extensions to get your application running quickly and incrementally replace Spring components with Quarkus-native implementations for optimal performance. Use the examples and conversion patterns in this guide as templates for your own migration.

[[1]](https://quarkus.io/guides/spring-web) [[3]](https://quarkus.io/guides/spring-web) [[6]](https://quarkus.io/guides/spring-web) [[7]](https://quarkus.io/guides/spring-web) [[8]](https://quarkus.io/guides/spring-web) [[10]](https://quarkus.io/guides/spring-web) [[15]](https://quarkus.io/guides/spring-web) [[16]](https://quarkus.io/guides/spring-web) Quarkus Extension for Spring Web API - Quarkus

<https://quarkus.io/guides/spring-web>

[[2]](https://gist.github.com/pierregmn/41c7ff378d0e13d0ee32447ca33c3423) [[4]](https://gist.github.com/pierregmn/41c7ff378d0e13d0ee32447ca33c3423) [[5]](https://gist.github.com/pierregmn/41c7ff378d0e13d0ee32447ca33c3423) [[14]](https://gist.github.com/pierregmn/41c7ff378d0e13d0ee32447ca33c3423) Migration of a Spring boot application to Quarkus · GitHub

<https://gist.github.com/pierregmn/41c7ff378d0e13d0ee32447ca33c3423>

[[9]](https://marcelkliemannel.com/articles/2021/migrating-from-spring-to-quarkus/#:~:text=%40RestController%20%40RequestMapping%28,) [[11]](https://marcelkliemannel.com/articles/2021/migrating-from-spring-to-quarkus/#:~:text=In%20Spring%20JPA%2C%20we%20would,our%20domain%20object%2C%20for%20example) Migrating From Spring to Quarkus | Marcel Kliemannel

<https://marcelkliemannel.com/articles/2021/migrating-from-spring-to-quarkus/>

[[12]](https://dzone.com/articles/spring2quarkus-spring-boot-to-quarkus-migration) [[13]](https://dzone.com/articles/spring2quarkus-spring-boot-to-quarkus-migration) Spring2quarkus — Spring Boot to Quarkus Migration

<https://dzone.com/articles/spring2quarkus-spring-boot-to-quarkus-migration>
