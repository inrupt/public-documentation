# Java-Specific Guidelines

## Java Security Guidelines

- [Java testing tools](https://www.owasp.org/index.php/Source_Code_Analysis_Tools) 

- [OWASP guide](https://www.owasp.org/index.php/Category:Java) 

- [Oracle guide](https://www.oracle.com/technetwork/java/seccodeguide-139067.html) 

- [CERT guide](https://wiki.sei.cmu.edu/confluence/display/java/Java+Coding+Guidelines) 

- [Secure Coding in Java Professional Certificate](https://www.sei.cmu.edu/education-outreach/credentials/credential.cfm?customel_datapageid_14047=15141)

## Code Security Vendor Guidelines

- [Whitesource](https://www.whitesourcesoftware.com/open-source-bug-tracking/)  (“composition” assessment with dependency/library policy)
- [Contrast](https://docs.contrastsecurity.com/admin-policymgmt.html)  (vulnerability assessment)
- [Semmle](https://semmle.com/#book-a-demo) 
- Leverage GitHub security scans for all projects
    1. List [dependencies](https://help.github.com/en/articles/listing-the-packages-that-a-repository-depends-on)  in a format supported by GitHub
    2. [Opt in](https://help.github.com/en/articles/opting-into-or-out-of-data-use-for-your-private-repository)  to dependency graph access
    3. Enable [automated security fixes](https://help.github.com/en/articles/configuring-automated-security-fixes)  (pull requests)
    4. Enable immediate vulnerability [notification](https://help.github.com/en/articles/choosing-the-delivery-method-for-your-notifications#choosing-the-delivery-method-for-security-alerts-for-vulnerable-dependencies) to multiple key developers
    5. Test the process on a dummy vulnerable dependency in every repo (e.g., by adding such a dependency so as to trigger an alert and pull request, and stepping through the full procedure)

## Java Code Quality Guidelines/Tools

1. All Java code must conform to the [Google Java convention](https://checkstyle.sourceforge.io/google_style.html), and we explicitly enforce this by running  [CheckStyle](https://checkstyle.sourceforge.io/)  on all code

3. All Java projects must adhere to the standard Maven Standard [Directory Layout](https://maven.apache.org/guides/introduction/introduction-to-the-standard-directory-layout.html) 

5. We currently enforce 100% branch code coverage across our entire code base. These tests are written in the industry standard of [JUnit](https://junit.org/junit5/) , using the latest version 5 features. Helpful article on how to test [complex conditionals](https://storage.googleapis.com/gtb/TotT-2008-02-21.pdf)  (which can sometimes be awkward to get 100% branch coverage, but is often where the nastiest bugs lurk!)

7. We use [Mockito](https://site.mockito.org/)  as our unit testing mocking library

9. We use [Jacoco](https://www.eclemma.org/jacoco/)  to gather and report on all code coverage statistics across the entire code base

11. For static-code analysis we run [PMD](https://pmd.github.io/)  and [Spotbugs](https://spotbugs.github.io/) . For both tools, we do not turn off any warnings. For the rare cases where we suppress a warning, we must provide a detailed explanation and justification as a comment in the code.

13. Our overall ESS architecture is fundamentally based on MicroServices, and we use the latest MicroProfile support in Java to provide the supporting services (e.g. packaging, health-checks, metrics, etc.), using [Quarkus](https://quarkus.io/)  as our implementation.

15. For API unit testing we use [RESTEasy-client](http://www.jboss.org/resteasy) , which tests against our industry standard JAX-RS server API’s. This library fully supports our internal [MicroProfile](https://github.com/eclipse/microprofile-rest-client)  architecture

17. For End-to-End testing we leverage Quarkus directly (i.e. via the ‘@QuarkusTest’ annotation), and [REST Assured](http://rest-assured.io/)   for exercising and validating all system endpoints.

19. For low-level performance testing we use [ContiPerf](https://github.com/javatlacati/contiperf)  (i.e. just annotating our existing unit tests), which provides an easy and quick protection against performance regression issues, and also very convenient multi-threaded testing.

21. For high-level overall system performance testing we use the comprehensive [DRAS-TIC](https://github.com/UMD-DCIC/gatling-testbed)  framework, and we are actively contributing back to that project. DRAS-TIC allows us run ESS in multiple cluster sizes and execute high-performance loads on our deployed environments, gathering and reporting on the performance of the overall system.

23. DRAS-TIC uses the [Gatling](https://gatling.io/)  load testing tool to emulate any number of client connections.

25. We run a [license-checking tool](https://plugins.gradle.org/plugin/com.github.hierynomus.license)  on our entire code base to ensure that all our source code has the correct license information.

27. We run SonarQube as our overall code-quality monitoring and reporting tool.

29. We use [Hamcrest Matchers](http://hamcrest.org/JavaHamcrest/)  in our unit tests to ease validation and test assertions. (NOTE: Mockito 2.x has its own ArgumentMatcher interface, which decouples Mockito from Hamcrest. Hamcrest is still used under the hood, but one won't see it any more in user-level code.)

31. We may provide ‘test-support’ modules too - i.e. code that is useful for testing a library or component from our system. For instance the LIT provides Hamcrest matchers for LIT exceptions (e.g. that assert an exception has the correct IRI and contains specific string values), or the LIT Dataset. Although these Matchers are test code, we need to provide them as ‘main’ code so that higher-level code can reuse them, and ‘test-support’ module provide the means to do this.

33. For testing JSON request and response payloads we use [JSONAssert](https://github.com/skyscreamer/JSONassert) .

35. To ease mocking of HTTP we use [WireMock](http://wiremock.org/) . Note: WireMock comes bundled with Quarkus, so there’s no need to depend on a specific version if using Quarkus already!

37. We’ve integrated [WhiteSource](https://www.whitesourcesoftware.com/open-source-bug-tracking/)  into our CI process to automatically detect open-source library vulnerabilities.

39. We’ve also integrated [dependabot](https://dependabot.com/)  into our CI process to automatically detect dependent library updates, that automatically creates PR’s to upgrade to latest (secure) versions.

## Java Code Quality Practices

1. All our build scripts are using [Gradle](https://gradle.org/) . We are currently (Oct 2019) porting all our scripts to us the latest Kotlin syntax (to benefit from it’s type-safety features over Groovy).

2. We make extensive use of industry standard design patterns (e.g. from  [Design Patterns](https://en.wikipedia.org/wiki/Design_Patterns) ), but we also make extensive use of [Apache Camel](https://camel.apache.org/) . Camel allows us to apply the full suite of [Enterprise Design Patterns](https://www.enterpriseintegrationpatterns.com/)  across all our MicroService implementations (i.e. allowing us to plug-and-play them in highly dynamic and configurable patterns).

3. We enforce a consistent approach to error handling across our entire code base, ensuring that all error conditions have access to rich contextual data (both static context (such as server up-time, server region, administrative preferred languages, etc.) and run-time context (such as request headers, client request WebID, client provided meta-data, etc.).

4. We enforce full internationalization across our code base, including supporting multilingual error messages, log messages, event messages, auditing messages, etc. We implement this support in a fully standardized way by using RDF vocabularies for all messages in the system.

5. We provide the same consistent access to multilingual contextual data across all areas of our system (i.e. not just for error handling), including logging, auditing, events and notifications. To support the variability of use-cases we again leverage the extensibility and interoperability of RDF as our underlying data model.

## Java Conventions We Use

1. All method parameters should be marked `final`(except in `interface`s, where they are redundant, yet can actually cause linters to complain and so shouldn’t be used at all!), and also `@NotNull` by default (this just makes it clearer when NULL exceptions are actually needed). `requireNonNull()` should also be used in the body of methods, but we don’t yet enforce this convention across the codebase (although we may in future).

2. All variables (member and local) should by default be marked as `final` - this just makes is clearer when non-final exceptions are actually needed.

