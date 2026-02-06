---
title: "Resume"
summary: Long-form summary of my life's work and achievements.
date: 2025-05-25
weight: 1
---

## Introduction

I graduated from [UC Berkeley](https://www.berkeley.edu/) intellectually invested in mathematics and computer science. Concretely, I am both a systems programmer and machine learning practitioner. I spent all four of my summers at [Meta](https://about.meta.com/) (building machine learning infrastructure, among other things) and have two years of leadership experience as an engineering lead at [UC Berkeley SAIT](https://technology.berkeley.edu/SAIT).

---

## Academic History

### University of California, Berkeley

**B.A. Computer Science** -- Aug 2021 to Dec 2025

{{< collapse "summary"="Coursework..." >}}

* `CS 70` _Discrete Mathematics & Probability Theory_ ([Prof. Rao](https://www2.eecs.berkeley.edu/Faculty/Homepages/rao.html#main-content), [Prof. Ayazifar](https://www2.eecs.berkeley.edu/Faculty/Homepages/ayazifar.html#main-content))
* `CS 61A`  _Structure & Interpretation of Computer Programs_ ([<u>Prof. DeNero</u>](https://denero.org/))
* `CS 61B` _Data Structures & Algorithms_ ([<u>Prof. Hilfinger</u>](https://www2.eecs.berkeley.edu/Faculty/Homepages/hilfinger.html#main-content))
* `CS 61C` **_Great Ideas in Computer Architecture_** ([Prof. Garcia](https://people.eecs.berkeley.edu/~ddgarcia/))
* `CS 170` **_Efficient Algorithms & Intractable Problems_** ([<u>Prof. Haghtalab</u>](https://people.eecs.berkeley.edu/~nika/), [Prof. Wright](https://www2.eecs.berkeley.edu/Faculty/Homepages/jswright.html#main-content))
* `CS 186` _Introduction to Database Systems_ ([Prof. Cheung](https://people.eecs.berkeley.edu/~akcheung/))
* `CS 162` **_Operating Systems & Systems Programming_** ([<u>Prof. Kubiatowicz</u>](https://people.eecs.berkeley.edu/~kubitron/))
* `CS 189` **_Introduction to Machine Learning_** ([<u>Prof. Listgarten</u>](http://www.jennifer.listgarten.com/), [Prof. Saremi](https://www.linkedin.com/in/saeed-saremi-71935916/))
* `CS 182` **_Designing, Visualizing and Understanding Deep Neural Networks_** ([<u>Prof. Sahai</u>](https://www2.eecs.berkeley.edu/Faculty/Homepages/sahai.html))

<!-- -->

* `MATH 1A` _Calculus I_ ([Prof. Vojta](https://math.berkeley.edu/~vojta/))
* `MATH 1B` _Calculus II_ ([<u>Prof. Paulin</u>](https://math.berkeley.edu/~apaulin/))
* `MATH 54` **_Linear Algebra & Differential Equations_** ([Prof. Lott](https://math.berkeley.edu/~lott/))
* `MATH 110` **_Abstract Linear Algebra_** ([<u>Prof. Frenkel</u>](https://www.edwardfrenkel.com/))
* `MATH 113` _Abstract Algebra_ [Audited] ([Prof. Utiralova](https://math.berkeley.edu/~sashau/))

**Courses **in bold** and professors <u>underlined</u> are favorites of mine!* 

{{< /collapse >}}

---

## Professional History 

### Software Engineer (IC3) @ [Meta](https://about.meta.com/)

New York, NY -- Feb 2025 to present 

{{< collapse "summary"="Details..." >}}

TODO (I just started working.)


[]() <!-- This is for rendering the above as a <p> element -->

{{< /collapse >}}

### Software Engineer Intern @ [Meta](https://about.meta.com/)

New York, NY -- May 2025 to Aug 2025

{{< collapse "summary"="Details..." >}}

Experimentation products at Meta (e.g. [ServiceLab](https://www.usenix.org/system/files/osdi24-chow.pdf)) rely on common experimentation infrastructure in the form of a unified platform. This platform underwent migration to a modularized alternative in the interest of extensibility into the use cases of new experimentation products (among other things). 

As part of the Systems Experimentation team, I migrated a feature that allowed experimentation products to track the status of collections of individual trials under an SLA of eventual consistency and retry-based fault tolerance. This required the design and implementation of distributed workflows written in Python and Rust, extensions to our external API, and close rollout monitoring via internal observability tooling.

I also detailed a state log data pipeline and model-predictive workflow controller to optimize actions (such as retries) according to our SLOs. This took the form of a ~15 page RFC that I presented to the team.

[]() <!-- This is for rendering the above as a <p> element -->

{{< /collapse >}}

### Software Engineer Intern @ [Meta](https://about.meta.com/)

New York, NY -- May 2024 to Aug 2024

{{< collapse "summary"="Details..." >}}

Improved automatic tuning of hyper- and production parameters for models that are online-trained by lowering the overhead of trial-based experimentation, which is used in their automatic tuning routines. Examples of these models are those that estimate the probability of a user's click on a given ad on their feed. Tuning these parameters helps minimize both loss and compute-related cost overhead. 

This mostly looked like writing [typed Python](https://pyre-check.org/) and working with a very wide breadth of internal tooling, including HPC schedulers, database services, and [distributed computing frameworks](https://engineering.fb.com/2016/05/09/core-infra/introducing-fblearner-flow-facebook-s-ai-backbone/).

{{< /collapse >}}

### Software Engineer Intern @ [Meta](https://about.meta.com/)

Menlo Park, CA -- May 2023 to Aug 2023

{{< collapse "summary"="Details..." >}}

I contributed to the [Meta Ads](https://www.facebook.com/business/ads) platform. Specifically, I worked on a pre-alpha feature related to [ad bidding](https://en.wikipedia.org/wiki/Real-time_bidding) which will help advertisers express granular adjustments to the value they attribute to different audiences based on various user dimensions, and which will be a part of the [Ads Manager tool](https://www.facebook.com/ads/manager) once released. This was full-stack product work.

[]() <!-- This is for rendering the above as a <p> element -->

{{< /collapse >}}

### MetaU Engineering Intern @ [Meta](https://about.meta.com/)

Menlo Park, CA -- Jun 2022 to Aug 2022

{{< collapse "summary"="Details..." >}}

I was taught large-scale application design and developed my own project with the guidance of two (incredibly didactic and supportive) software engineers. While I barely touched Meta's codebase (by the program's design), I did learn a lot about the work of the team I was placed into, which helped [WhatsApp](https://www.whatsapp.com/) transfer and archive data. 

The main project I worked on was an iOS task-based project management application I called [Process](https://github.com/maxfierrog/process) whose main feature was a [directed acyclic graph](https://en.wikipedia.org/wiki/Directed_acyclic_graph) of task-to-task dependencies (as opposed to just a [tree](https://en.wikipedia.org/wiki/Tree_(data_structure)) or list) on a recursive user interface. It is built on a [Firebase](https://firebase.google.com/)-d backend and [SwiftUI](https://developer.apple.com/xcode/swiftui/) frontend (in hindsight, should have used [Neo4j](https://neo4j.com/)'s graph DB instead).

{{< /collapse >}}

### Lead Endpoint Engineer @ [UC Berkeley SAIT](https://technology.berkeley.edu/SAIT)

Berkeley, CA -- Mar 2022 to Feb 2024

{{< collapse "summary"="Details..." >}}

I led a team responsible for the development of open-ended IT initiatives in the department of Student Affairs and the management of all workstations and devices used by UC Berkeley Student Affairs groups. Here are some of the things we achieved together during my time there:

* Built infrastructure for and provided 60 iPads to teachers in the [ECE Program](https://ece.berkeley.edu/).
* Created 2 interviewing stations free for all UC Berkeley students at the [Career Center](https://career.berkeley.edu/).
* Set up an accessible station for [CalDining](https://caldining.berkeley.edu/) employees with low tech and English literacy.
* Helped [Cal Esports](https://recsports.berkeley.edu/programs-events/esports/) do a drop-in replacement of 55 gaming PCs' hardware and software.

In addition to these collective achievements, in my specific capacity as team lead I was able to:

* Conduct 4 successful hiring panels for positions on my team (including my own position).
* Advise ex-CIO Jenn Stringer and IT leadership as part of the [Student Technology Council](https://stc.berkeley.edu/).
* [Contribute](https://drive.google.com/file/d/1JcIjnEDlngqoJxj0I6JEmAplYlQL42iv/view?usp=sharing) to the OneIT initiative instantiating more student programs in Berkeley IT.

{{< /collapse >}}

### Senior Endpoint Engineer @ [UC Berkeley SAIT](https://technology.berkeley.edu/SAIT)

Berkeley, CA -- Feb 2024 to Nov 2024 

{{< collapse "summary"="Details..." >}}

After leading the Endpoint Engineering team for about 2 years, I transitioned to a Senior role to enjoy additional time for pursuing my research interests. I focused on supporting my team with additional onboarding in addition to other efforts, such as improving mobile device management.

[]() <!-- This is for rendering the above as a <p> element -->

{{< /collapse >}}

### Technology Consultant @ [UC Berkeley SAIT](https://technology.berkeley.edu/SAIT)

Berkeley, CA -- Aug 2021 to Mar 2022 

{{< collapse "summary"="Details..." >}}

I provided frontline hardware and software assistance to students, and contributed to our standardized procedures which are still used by the 20+ member [Student Helpdesk](https://studenttech.berkeley.edu/techsupport) team. This was overall incredibly varying work, and I learned a lot about customer service and got to be very scrappy (in a careful way). Students can sometimes get their computers into truly _"wow"_ states.

[]() <!-- This is for rendering the above as a <p> element -->

{{< /collapse >}}

### Crew Member @ [Jolibee](https://www.jollibeefoods.com/)

San Diego, CA -- Mar 2021 to Jun 2021

{{< collapse "summary"="Details..." >}}

No meal is complete without the bread, and no CV is complete without fast food experience. With the help of my comrades, I served food at up to 3 orders per minute. I worked 40 hour weeks while being a full-time high school student, which was exhausting. This is the hardest work I have done in my life, including being a Berkeley student (but excluding 40 degree Celsius soccer practices in 2nd grade).

[]() <!-- This is for rendering the above as a <p> element -->

{{< /collapse >}}

---

## Afiliations

### Research & Development @ [GamesCrafters](https://gamescrafters.github.io/)

Berkeley, CA -- Jan 2023 to Dec 2025 

{{< collapse "summary"="Details..." >}}

I own a Rust platform for analyzing sequential games called [GamesmanNova](https://github.com/GamesCrafters/GamesmanNova). It was originally built as an informed re-architecture of my advisor's original C software [Gamesman](https://github.com/GamesCrafters/GamesmanClassic).

My recent work has been aimed towards translating encodings of provably perfect strategy in sequential games to representations that are intuitive to humans in a reproducible and abstract fashion by expressing them in terms of observable features of game states. In the past, I have helped with:

* Desigining domain-specfic databse systems and algorithms for analyzing sequential games.
* Administrating the instructional component of our group, teaching 20+ students per semester.
* Solving [Five-Field Kono](https://en.wikipedia.org/wiki/Five_Field_Kono) (you can play against the solution [here](https://nyc.cs.berkeley.edu/uni/games/fivefieldkono/variants/regular)).
* Outreach during [CalDay '23 and '24](https://engineering.berkeley.edu/events/cal-day/)!

{{< /collapse >}}

### Competition Robotics @ [FIRST Team #6348](https://frc-events.firstinspires.org/team/6348)

Colima, Mexico -- Sep 2019 to Aug 2022

{{< collapse "summary"="Details..." >}}

This is the FIRST Robotics Competition group from one of the high schools I went to. It is very close to my heart, because me and my closest friends put in a lot of work for the team. While I still occasionally help out with mentoring, my active involvement has largely ceased because I have gotten increasingly busy. Here are the roles I have held:

* As an **official mentor** I gave professional and academic guidance to the team.
* As **head of the Design Division** I coordinated the design of an award-winning robot.
* As a **driver** I participated in competitions and got to crash our robots a few times.
* As a **member** I made a lot of friends who went on to become incredible individuals.

{{< /collapse >}}

---

## Skills

The items below are things I have experience with, which are also building blocks of at least some things I am excited to contribute to. For example, I have much JavaScript experience but I am not interested in web development, so I do not include it here.

### GPPLs

* Python
* Java
* Rust
* C/C++

### Technical Toolchain

* General purpose scripting (Bash, Python, PowerShell, etc.)
* Leadership (meeting scheduling is definitely NP-complete)
* Understanding of mathematics, especially for ML
* UNIX system administration & programming
* Git and Mercurial development workflows
