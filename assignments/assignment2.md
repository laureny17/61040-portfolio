# Assignment 2

## Problem Statement

### Problem Domain

_Event organization: Help organizers manage logistics for large-scale events._

As an organizer of a hackathon that invites over a thousand students as well as hundreds of mentors, judges, and sponsors, I've seen firsthand just how much work goes into organizing large events. I love being an organizer, but have realized that how well our internal technology functions is truly key to our success. Well-functioning internal tools have the potential to greatly the manual load that organizers otherwise must take on, freeing up our time and energy to work on other tasks.

### Problem

**Application Reading Webapp.** One problem I would like to help solve is that, since application reading is inherently exhausting and repetitive due to the sheer volume, it is key that our application reading software is clear in scoring expectations, intuitive and enjoyable to use, and ensures that applications are fairly read despite being judged by many different people from various backgrounds. The cost of poor tooling is real and concerning: reader fatigue grows, decreasing organizers energy and enthusiasm; reading quickly becomes sloppy and poorly calibrated; and as a result, applicants are less likely to consistently get the careful, unbiased read that they all deserve.

### Stakeholder List

1. Application readers: The people reading and rating applications to participate in an event. This problem impacts them because they are the main users directly impacted by the problem; a good solution would make the application reading process both quicker and more engaging, so that reading quality is maintained while making the task easier to get through.
2. Event participants and applicants: The people attending and wishing to attend the event being organized. Important because how thoroughly applications are read by organizers using the application reading webapp impacts who is let in. The smoother the application reading experience is, the fairer decisions will be, and this would lead to less applicants feeling wronged by the decision they receive.
3. Other organizers: Organizers of other hackathons or other application-only events. Other hackathons have used our software in the past for their tech, so it's possible that if a revamped application reading software works especially well for us, other organizers may want to use it as well.

### Evidence and Comparables

1. **Built-in training.** [Training for application readers can measurably increase reliability of their ratings](https://pmc.ncbi.nlm.nih.gov/articles/PMC4468126), and building an application reading webapp with training built-in can ensure that fair judgments are made even if the pool of application readers is large and diverse in opinions and experience.
2. **Comparables.** [Slate](https://technolutions.com/admissions) is used by many schools to organize and process university applications. However, it's clear that this platform is designed for a much larger scale. Not only are the tools far more complex than necessary to solve this problem, but it also offers licenses starting at $30,000 per year, making it completely inaccessible for smaller organizations.
3. **Ensuring well-thought out decisions despite short read times.** Even for the far longer, more detailed, and more holistic process of reading college applications, [admissions officers often scan through an application in 8 minutes or less](https://www.ivycoach.com/the-ivy-coach-blog/college-admissions/8-minute-rule-college-admissions/). A large volume of applications makes the task of reviewing applications fundamentally difficult and repetitive.
4. **Decision fatigue.** [Application reviewers face significant decision fatigue](https://blog.kiratalent.com/how-fatigue-affects-your-admissions-decisions/), especially as more is added to the decision making process. Tired reviewers will rely more heavily on shortcuts, such as scanning for numbers and statistics over fully and carefully looking over an application. It's important that the reviewing process is as seamless as possible.
5. **Intrinsic and extrinsic motivators.** An [internal system](https://blog.kiratalent.com/inside-holistic-admissions-with-haas-school-of-business/) in place with more intrinsic and extrinsic motivators may help prevent reviewers from developing decision fatigue and burnout. Without motivators and reminders of why the job is rewarding, reviewers can quickly develop burnout. Depending on the context, [gamifying a repetitive process](https://pmc.ncbi.nlm.nih.gov/articles/PMC9220799) can increase users' enjoyment on the platform. Increasing intrinsic motivation in particular gives users autonomy.
6. **Ensuring unbiased reads.** [Blind reviews](https://arxiv.org/abs/2101.02701) of applications noticeably help reduce bias. It's important to build an application reading tool that is as unbiased as possible, as applicants rely on readers to make fair judgements. In particular, it may help to censor any mentions of the school a student may be attending, as [prestige bias is a very real issue](https://pmc.ncbi.nlm.nih.gov/articles/PMC7811873).
7. **The importance of improving our UI.** [Iterating on and redesigning UI can lead to substantial improvements in usability](https://www.nngroup.com/articles/iterative-design). Reading though applications is tough on people currently, and bad UI could be contributing to the large amounts of time spent on making decisions for applicants.

## Application Pitch

### Name

### Motivation

A reader-first admissions surface that keeps reviews fast, fair, and fatigue-resistant—without outsourcing judgment to AI.

### Key Features

1. **Admin control.** Keep application reading secure by requiring admin approval of reader accounts, and allow admins to customize the experience for application readers of their event by inputting a rubric/scoring guidelines and toggling things listed in Feature 3. This will centralize the experience for application readers, and allow for a team lead to keep the experience secure and customized to their event.

2. **Fair flow of reading.** Give each reader one application at a time, with the rubric/scoring guidelines easily accessible from the reading page, personal history allowing for edits to be made post-decision. Readers can manually flag suspicious or ineligible applications, or skip applicants they think they may know. This should ensure fairer decisions for applicants by letting readers fix mistakes, giving them a sense of control by letting them flag applications, and preventing personal bias from affecting decisions.

3. **Community engagement and statistics.** Introduce a sense of community and responsibility by allowing readers to comment on applications; including a statistics page with a reader scatter-plot (apps read vs. average time/app); milestone announcing notifications (e.g. “Lauren hit 100 reads!”); and self-pacing notifications to remind readers if their scores have insufficient variation or significantly shorter than average read times. This should help promote consistency and sustainably pacing and read quality without introducing too-heavy extrinsic rewards or punishments.

## Concept Design

### Event Directory

```
concept EventDirectory
purpose Manage past and present events and their associated details.
principle Users can register for a chosen event.
    The admin can create and archive events and manage rubric/scoring guidelines, number of required reads per application, and approved readers for the active event.
state
    a set of Users with
        an email String
        a name String
        a password String
        an Event
        a readCount Number
        a totalTime Number
        a skipCount Number

    an Admin with
        a User

    a set of Events with
        a name String
        an active Flag
        a requiredReadsPerApp Number
        a set of RubricDimensions
        a readers set of Users
        an unverifiedUsers set of Users

    a set of RubricDimensions with
        a name String
        a description String
        a scaleMin Number
        a scaleMax Number

    an ActiveEvent with
        an Event
actions
    createEvent (caller: User, name: String, requiredReadsPerApp: Number, rubric: set of RubricDimensions): (event: Events)
        requires: caller is the Admin
        effect: add a new Event and set it as the active event (ActiveEvent.event = event)

    updateEventConfig (caller: User, event: Event, requiredReadsPerApp: Number, rubric: set of RubricDimensions)
        requires: caller is the Admin and event is the ActiveEvent
        effect: update provided fields

    addReader (caller: User, event: Event, user: User)
        requires: caller is the Admin, event is the ActiveEvent and user is in Users
        effect: remove user from event.unverifiedUsers and add user in event.Readers

    removeReader (caller: User, event: Event, user: User)
        requires: caller is the Admin, event is the ActiveEvent and user is in the event's Readers
        effect: remove user from event.Readers and add user to event.unverifiedUsers
```

### Applications

```
concept Applications
purpose Store applications and track their read-counts.
principle The admin can add applications to the active event. This should initialize all applications in the database to have 0 reads.
state
    a set of Applications with
        an applicantID String
        an applicantYear String
        a set of QuestionsAndAnswers
        a readsCompleted Number

    a set of QuestionsAndAnswers with
        a question String
        an answer String
actions
    addApplications (caller: User, event: Event, applications: set of Applications): (application: Applications)
        requires: caller is the Admin and event is the Active event
        effect: create applications for each applicantID associated with an applicantYear, a set of questions and
        answers from the application, and readsCompleted = 0
```

## UI Sketches

<object data="/assets/assignment2/appreader_ui_sketches.pdf" type="application/pdf">
    <embed src="/assets/assignment2/appreader_ui_sketches.pdf">
        <p>This browser does not support PDFs. Please download the PDF to view it: <a href="/assets/assignment2/appreader_ui_sketches.pdf">Download PDF</a>.</p>
    </embed>
</object>

## User Journey
