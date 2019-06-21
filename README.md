**Testing Compilation of JavaScript in Camunda BPM 7.11.0**

*NOTE*: This feature has been requested via a [pull request](https://github.com/camunda/camunda-bpm-platform/pull/360). The Camunda team has also created a [Feature Request](https://app.camunda.com/jira/browse/CAM-10506) in the Camunda CAM project.

*Making the Change in the Engine*

As a first step, you'll need to make the change to the `org.camunda.bpm.engine.impl.scripting.SourceExecutableScript` class in the Camunda engine you'll use for your testing. In our case, I made the change externally, compiled it and then updated the JAR file within a Camunda BPM 7.11.0 installation directly using these two commands:

1. `zip -d camunda-engine-7.11.0.jar org/camunda/bpm/engine/impl/scripting/SourceExecutableScript.class`
2. `jar uf camunda-engine-7.11.0.jar org/camunda/bpm/engine/impl/scripting/SourceExecutableScript.class`

*Testing the Change*

To get useful results, the change must be tested using a full Camunda BPM environment with the Job Executor running. For example, you could use the standard Tomcat distribution, or you could start Camunda BPM via Spring Boot. If you do the latter, you could use a Spring Boot test to generate your simulated results. 

This repository includes two files: 

1. The process definition that was used for our tests, entitled "create-list-of-json.bpmn".
2. The source for the modified `org.camunda.bpm.engine.impl.scripting.SourceExecutableScript` class.

Please see the section below if you want to run your test in the same manner that we did.

*Our Testing*

We ran our test by standing up a Tomcat installation of Camunda BPM 7.11.0 (Community Edition), deploying the included process definition and running it 100 times via [Postman Runner](https://learning.getpostman.com/docs/postman/collection_runs/starting_a_collection_run/). The results were then extracted using a single SQL statement with a join. The test was run using:

- Ubuntu Linux 18.04 LTS
- OpenJDK 11.0.2
- PostgreSQL 10.8.4

Here were the observed results with 100 executions of the included process definition:

- The average (mean) execution time for the sample Script Task without compilation was 129 ms.
- The average (mean) execution time for the sample Script Task with compilation was 9.2 ms.

The process definition as included in this repository has an included print statement to illustrate thread safety. The tests referenced above were not run with the included print statement.