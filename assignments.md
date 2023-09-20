---
title: Assignments
layout: default
start: 01 Jan 2023, 00:00 (Europe/Zurich)
index: 3
---

### Administrivia

Over the course of the semester, we will post five assignments that will require you to code up five moderately complex Scala applications (one per each assignment) that highlight certain theoretical aspects of the course.

Every assignment will become available on this website at due time (usually after the deadline of the previous assignment).

### Team Registration

We encourage you to team up when working on assignments.
Teams can consist of one, two or three students and cannot intersect with each other.
It is okay to share ideas between teams, but sharing code is prohibited.

### Getting started with Scala

All development during the course will be done in Scala.
Here we provide the instructions how to setup the Scala development tools.

First, you need to install [the sbt build tool](http://www.scala-sbt.org/download.html).
See the instructions for installing JVM, Scala and SBT [here](https://gitlab.epfl.ch/lamp/cs206/-/blob/master/labs/tools-setup.md#step-2-installing-the-java-development-kit-jdk-and-sbt-via-coursier).
For each assignment, you can run `sbt compile` and `sbt run` in the console to compile and run the project.
Running `sbt` will start an interactive SBT session, after which you can repeatedly compile the project faster compared to running `sbt compile` each time.

We recommend using [VS Code](https://code.visualstudio.com/) with the [Metals](https://marketplace.visualstudio.com/items?itemName=scalameta.metals) extension for development.
You can find instructions for installing VS Code [here](https://gitlab.epfl.ch/lamp/cs206/-/blob/4f1694f836db0eed2e99da8a0eed617a5c403f28/labs/tools-setup.md#step-6-installing-code).
You will need to make sure you have installed JVM __version 11 or 17__, after which it suffices to install Metals and open the assignment directory in VS Code.
VS Code will allow you to use Scala worksheets, which will let you test and interact with your solutions much more easily.

You can also use [IntelliJ IDEA](https://www.jetbrains.com/idea/download) for development.
You can import the project into the IDE:

1. Click `Import Project`.
1. Select the directory where you unpacked the assignment.
1. Click `Open`.
1. Select `Import project from external model > SBT`, click `Next`.
1. Click `Finish`.

If you don't like IntelliJ IDEA, feel free to use alternative editors or even develop in the console,
as long as your project passes our tests.

### Submitting your solutions

You may create a zip file containing all your source code (contained in `src/main/scala`) and submit it through our [submission website](https://adapted-asp-shortly.ngrok-free.app/).

~~There should be a Scala script to help you with preparing the zip file, `submit.scala`. It requires [Scala CLI](https://scala-cli.virtuslab.org).~~

You can create the zip file by calling `sbt packageSrc` (or type `packageSrc` from an sbt shell within your Terminal or Intellij), and the resulting file will be located in `target/scala-3.3.0/for-submit.zip`.

Otherwise, if you are on Unix operating systems (Linux, MacOS, ...) a simple
```sh
zip submit.zip -r src
```
from the root of the project directory shall do.

If you are on Windows, simply zipping the `src` folder (by right-clicking on the folder) does the job.

### Late submissions

If you miss a deadline, you can still submit your project, however your grade will be reduced. Late submissions will be graded with 30% deduction of points for every day after the deadline.

<!-- ### Feedback on submissions -->

<!-- If you need more detailed feedback on your submission, please send an email to -->
<!-- one of the assistants to reserve an office slot, so that we may study your code -->
<!-- together. -->

### FAQ

In case of error, please check the following list:

1. You need to submit assignments using your _EPFL email address_.
2. Make sure your email is not encrypted.