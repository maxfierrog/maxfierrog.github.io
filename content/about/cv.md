---
title: "Curriculum Vitae"
summary: Long-form summary of my life's work and achievements.
date: 2023-03-20
weight: 1
tags: ["Serious", "Maintained"]
draft: false
ShowReadingTime: false
ShowWordCount: false
---

## 🙋 Introduction

I am a third-year undergraduate, and have found interests in mathematics, computer science, leadership, and low-level programming. I also work in UC Berkeley IT and have recently gotten involved with a research group exploring computational game theory, advised by [Dr. Dan Garcia](https://people.eecs.berkeley.edu/~ddgarcia/?_ga=2.162746047.2021628792.1679746835-1016432887.1673162780).

---

## 🎓 Academic History

### University of California, Berkeley

_**B.A. Computer Science** - In progress_\
My favorite fields are distributed systems and systems programming.

> {{< collapse "summary"="Completed courses..." >}}

* `CS 61A`  _Structure and Interpretation of Computer Programs_
* `CS 61B` _Data Structures and Algorithms_
* `CS 61C` _Great Ideas in Computer Architecture (Machine Structures)_
* `CS 70` _Discrete Mathematics and Probability Theory_

> {{< /collapse >}}

{{< brk >}}

_**B.A. Mathematics** - In progress_ \
I love mathematics, in particular the study of algebraic structures.

> {{< collapse "summary"="Completed courses..." >}}

* `MATH 1A` _Calculus I_
* `MATH 1B` _Calculus II_
* `MATH 54` _Linear Algebra and Differential Equations_

> {{< /collapse >}}

---

## 🛠️ Professional History

### Software Engineer Intern @ [Meta](https://about.meta.com/)

Menlo Park, CA -- May 2023 to Aug 2023

{{< collapse "summary"="Details..." >}}
\
I am currently in one of the teams that contribute to [Meta Ads](https://www.facebook.com/business/ads). I am personally working on a feature related to [ad bidding](https://en.wikipedia.org/wiki/Real-time_bidding), which will be a part of the [Ads Manager tool](https://www.facebook.com/ads/manager) once released.

{{< /collapse >}}

{{< brk >}}

### Lead Endpoint Engineer @ [UC Berkeley SAIT](https://technology.berkeley.edu/SAIT)

Berkeley, CA -- Mar 2022 to now

{{< collapse "summary"="Details..." >}}
\
I lead a team responsible for the development of open-ended IT initiatives in the department of Student Affairs and the management of all workstations and devices used by UC Berkeley Student Affairs groups. Some of the things we have achieved in my time here have been:

* Built infrastructure for and provided 60 iPads to teachers in the [ECE Program](https://ece.berkeley.edu/).
* Created 2 interviewing stations free for all UC Berkeley students at the [Career Center](https://career.berkeley.edu/).
* Set up an accessible station for [CalDining](https://caldining.berkeley.edu/) employees with low tech and English literacy.
* Helped [Cal Esports](https://recsports.berkeley.edu/programs-events/esports/) do a drop-in replacement of 55 gaming PCs' hardware and software.

In addition to these collective achievements, in my specific capacity as team lead I have:

* Conducted 3 successful hiring panels for the [Endpoint Engineer](https://docs.google.com/document/d/17_sCM0PM0WK7ghG6OPg30eWQ8d081LPXzWVUMnY5pMc/edit) position.
* Advised CIO Jenn Stringer and senior IT leaders as part of the [Student Technology Council](https://stc.berkeley.edu/).
* [Contributed](https://drive.google.com/file/d/1JcIjnEDlngqoJxj0I6JEmAplYlQL42iv/view?usp=sharing) to the OneIT initiative instantiating more student programs in Berkeley IT.

{{< /collapse >}}

{{< brk >}}

### MetaU Engineering Intern @ [Meta](https://about.meta.com/)

Menlo Park, CA -- Jun 2022 to Aug 2022

{{< collapse "summary"="Details..." >}}
\
I was taught large-scale application design and developed my own project with the guidance of two (incredibly didactic and supportive) software engineers. While I barely touched Meta's codebase (by the program's design), I did learn a lot about the work of the team I was placed into, which helped [WhatsApp](https://www.whatsapp.com/) transfer and archive data.

The main project I worked on was an iOS task-based project management application I called [Process](https://github.com/maxfierrog/process) whose main feature was a [directed acyclic graph](https://en.wikipedia.org/wiki/Directed_acyclic_graph) of task-to-task dependencies (as opposed to just a [tree](https://en.wikipedia.org/wiki/Tree_(data_structure)) or list) on a recursive user interface. It is built on a [Firebase](https://firebase.google.com/)-d backend and [SwiftUI](https://developer.apple.com/xcode/swiftui/) frontend (in hindsight, should have used [Neo4j](https://neo4j.com/)'s graph DB instead).

{{< /collapse >}}

{{< brk >}}

### Student Technology Consultant @ [UC Berkeley SAIT](https://technology.berkeley.edu/SAIT)

Berkeley, CA -- Aug 2021 to Mar 2022

{{< collapse "summary"="Details..." >}}
\
I provided frontline hardware and software assistance to students, and contributed to our standardized procedures which are still used by the 20+ member [Student Helpdesk](https://studenttech.berkeley.edu/techsupport) team. This was overall incredibly varying work, and I learned a lot about customer service and got to be very scrappy (in a careful way). Students can sometimes get their computers into truly _"wow"_ states.

{{< /collapse >}}

{{< brk >}}

### Crew Member @ [Jolibee](https://www.jollibeefoods.com/)

San Diego, CA -- Mar 2021 to Jun 2021

{{< collapse "summary"="Details..." >}}
\
No meal is complete without the bread, and no CV is complete without fast food experience. With the help of my comrades, I served food at up to 3 orders per minute. I worked 40 hour weeks while being a full-time high school student, which was exhausting. This is the hardest work I have done in my life, including being a Berkeley student (but excluding 40 degree Celsius soccer practices in 2nd grade).

{{< /collapse >}}

---

## 🤝 Afiliations

### R&D group [GamesCrafters](http://gamescrafters.berkeley.edu/)

Berkeley, CA -- Jan 2023 to now

This group researches methodologies for strongly solving abstract strategy games and develops the infrastructure to do it (and to make the solution sets available through a few types of interfaces).

{{< collapse "summary"="Things I am working on..." >}}

> * Re-writing and re-architecting [GamesmanClassic](https://github.com/GamesCrafters/GamesmanClassic), the group's seminal project, in Rust.
> * An [ACID](https://en.wikipedia.org/wiki/ACID)-compliant and more performant re-implementation of the database we use to store our solution sets, which will provide the needed infrastructure to implement...
>   * A [lookup table](https://en.wikipedia.org/wiki/Lookup_table) based database compression scheme.
>   * A [model based](http://mattmahoney.net/dc/mmahoney00.pdf) database compression scheme (memorizing using neural nets).
>   * Parallelized solving algorithms.

{{< /collapse >}}

{{< collapse "summary"="Finished projects I have contributed to..." >}}

> * Solving [Five Field Kono](https://en.wikipedia.org/wiki/Five_Field_Kono) (you can play against the solution [here](https://nyc.cs.berkeley.edu/uni/games/fivefieldkono/variants/regular)).
> * Outreach during [CalDay 2023](https://engineering.berkeley.edu/events/cal-day/)!

{{< /collapse >}}

{{< brk >}}

### FIRST Robotics [Team #6348](https://frc-events.firstinspires.org/team/6348)

Colima, Mexico -- Sep 2019 to Aug 2022

This is the FIRST Robotics Competition group from one of the high schools I went to. It is very close to my heart, because me and my closest friends put in a lot of work for the team. While I still occasionally help out with mentoring, my active involvement has largely ceased because I have gotten increasingly busy. Here are the roles I have held:

* As an **official mentor** I gave professional and academic guidance to the team.
* As **head of the Design Division** I coordinated the design of an award-winning robot.
* As a **driver** I participated in competitions and got to crash our robots a few times.
* As a **member** I made a lot of friends who went on to become incredible individuals.

---

## 🚀 Skills

### GPPLs I know and love

The list of programming languages I can "code" in is longer, but if you ask me to make something great, I will pick one of these.

* Python
* Java
* Rust
* C++

{{< brk >}}

### General Aptitudes

Here are a few activities I am skilled at doing because I fundamentally enjoy them.

* Organizational work (leadership, coordination, advising, etc.)
* Written and verbal communication
* Creating software systems
* Programming in Rust

{{< brk >}}

### Technical Toolchain

A lot of the work I have done has been enabled by different technologies, and this is some of what I have picked up along the way.

* General purpose scripting (Bash, PowerShell, ActionScript)
* Workstation and device management (BigFix, MDM systems, Active Directory)
* UNIX system administration
* Git and Mercurial workflows
* SwiftUI
* React

{{< brk >}}

### Interpersonal Specialties

These are the personal qualities and intents I have worked the hardest to foster.

* Emotional intelligence
* Individual advocacy
* Transparency
