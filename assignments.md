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
You can find instructions for installing VS Code [here](https://gitlab.epfl.ch/lamp/cs206/-/blob/master/labs/tools-setup.md#step-6-installing-code).
You will need to make sure you have installed JVM version 11 or 17, after which it suffices to install Metals and open the assignment directory in VS Code.
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

1. Send your project to the grading bot. You can do so by running the `submit.py` script in the project directory, or manually by following the steps you can find a bit below.
1. Wait for a response from the grading bot. You should receive _two_
responses - first one indicates that your email has reached the bot's inbox and
so got past Google's anti-virus. Second response will either contain a rejection
notice along with the detailed error message or an acceptance notice with the
results of running your submission against our test suite. The bot should
normally respond in less than five minutes, although depending on the current
load it could take longer.

1. If you don't receive a response from the bot, or you get your submission rejected without a good reason, contact the staff.

1. If you're not content with your results, follow the instructions in the reply email to try and improve your score. You can retry the submission as many times as you want before the deadline hits, and that won't result in any penalties to your final score.

#### Submitting manually

1. Zip the `src` directory, making sure that:
  * you haven't created or removed any files from the provided template for the corresponding project. For instance, if the template contained files Arithmetic.scala and Terms.scala in /src/main/scala/fos, don't move these files around or create additional helper files.
  * you don't zip any other directory, in particular the `target` directory, which contains binary files that might trip Google's anti-virus.
1. Send the archive produced during the previous step to grading bot's email address: [lamp.fos.bot@gmail.com](mailto:lamp.fos.bot@gmail.com)
  * The subject of the email should be "Project X (YYYYYY, ZZZZZZ, ...)", where X is the number of the project, and YYYYYY/ZZZZZZ/... are SCIPER numbers of the authors.
  * The body of the email doesn't matter, because our grading bot can't read.
  * Don't forget the attachment.

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