# Assignment 2

## Problem Statement

### Problem Domain

_Event organization: Help organizers manage logistics for large-scale events._

As an organizer of a hackathon that invites over a thousand students as well as hundreds of mentors, judges, and sponsors, I've seen firsthand just how much work goes into organizing large events. I love being an organizer, but have realized that how well our internal technology functions is truly key to our success. Well-functioning internal tools have the potential to greatly decrease the manual load that organizers otherwise must take on, freeing up our time and energy to work on other tasks.

### Problem

**Application Reading Webapp.** One problem I would like to help solve is that, since application reading is inherently exhausting and repetitive due to the sheer volume, it is key that our application reading software is clear in scoring expectations, intuitive and enjoyable to use, and ensures that applications are fairly read despite being judged by many different people from various backgrounds. The cost of poor tooling is real and concerning: reader fatigue grows, decreasing organizers energy and enthusiasm; reading quickly becomes sloppy and poorly calibrated; and as a result, applicants are less likely to consistently get the careful, unbiased read that they all deserve.

### Stakeholder List

1. Application readers: The people reading and rating applications to participate in an event. This problem impacts them because they are the main users directly impacted by the problem; a good solution would make the application reading process both quicker and more engaging, so that reading quality is maintained while making the task easier to get through.
2. Event participants and applicants: The people attending and wishing to attend the event being organized. Important because how thoroughly applications are read by organizers using the application reading webapp impacts who is let in. The smoother the application reading experience is, the fairer decisions will be, and this would lead to less applicants feeling wronged by the decision they receive.
3. Other organizers: Organizers of other hackathons or other application-only events. Other hackathons have used our software in the past for their tech, so it's possible that if a revamped application reading software works especially well for us, other organizers may want to use it as well.

### Evidence and Comparables

1. **Comparables.** [Slate](https://technolutions.com/admissions) is used by many schools to organize and process university applications. However, it's clear that this platform is designed for a much larger scale. Not only are the tools far more complex than necessary to solve this problem, but it also offers licenses starting at $30,000 per year, making it completely inaccessible for smaller organizations.
2. **Ensuring well-thought out decisions despite short read times.** Even for the far longer, more detailed, and more holistic process of reading college applications, [admissions officers often scan through an application in 8 minutes or less](https://www.ivycoach.com/the-ivy-coach-blog/college-admissions/8-minute-rule-college-admissions/). A large volume of applications makes the task of reviewing applications fundamentally difficult and repetitive.
3. **Decision fatigue.** [Application reviewers face significant decision fatigue](https://blog.kiratalent.com/how-fatigue-affects-your-admissions-decisions/), especially as more is added to the decision making process. Tired reviewers will rely more heavily on shortcuts, such as scanning for numbers and statistics over fully and carefully looking over an application. It's important that the reviewing process is as seamless as possible.
4. **Intrinsic and extrinsic motivators.** An [internal system](https://blog.kiratalent.com/inside-holistic-admissions-with-haas-school-of-business/) in place with more intrinsic and extrinsic motivators may help prevent reviewers from developing decision fatigue and burnout. Without motivators and reminders of why the job is rewarding, reviewers can quickly develop burnout. Depending on the context, [gamifying a repetitive process](https://pmc.ncbi.nlm.nih.gov/articles/PMC9220799) can increase users' enjoyment on the platform. Increasing intrinsic motivation in particular gives users autonomy.
5. **Ensuring unbiased reads.** [Blind reviews](https://arxiv.org/abs/2101.02701) of applications noticeably help reduce bias. It's important to build an application reading tool that is as unbiased as possible, as applicants rely on readers to make fair judgements. In particular, it may help to censor any mentions of the school a student may be attending, as [prestige bias is a very real issue](https://pmc.ncbi.nlm.nih.gov/articles/PMC7811873).
6. **The importance of improving our UI.** [Iterating on and redesigning UI can lead to substantial improvements in usability](https://www.nngroup.com/articles/iterative-design). Reading though applications is tough on people currently, and bad UI could be contributing to the large amounts of time spent on making decisions for applicants.

## Application Pitch

### Name

AppReader

### Motivation

A reader-first admissions platform that

### Key Features

1. **Admin control.** Keep application reading secure by requiring admin approval of reader accounts, and allow admins to customize the experience for application readers of their event by inputting a rubric/scoring guidelines. This will centralize the experience for application readers, and allow for a team lead to keep the experience secure and customized to their event.

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
actions
    createEvent (caller: User, name: String, requiredReadsPerApp: Number, rubric: set of RubricDimensions): (event: Event)
        requires: caller is the Admin
        effect: add a new Event and set its active flag to true
    activateEvent (caller: User, name: String)
        requires: caller is the admin, event with name is not active
        effect: sets the event's active flag to true
    inactivateEvent (caller: User, name: String)
        requires: caller is the Admin, event with name is active
        effect: sets the event's active flag to false
    updateEventConfig (caller: User, event: Event, requiredReadsPerApp: Number, rubric: set of RubricDimensions)
        requires: caller is the Admin and event is an active event
        effect: update provided fields
    addReader (caller: User, event: Event, user: User)
        requires: caller is the Admin, event is an active event and user is in Users
        effect: remove user from event.unverifiedUsers and add user in event.Readers
    removeReader (caller: User, event: Event, user: User)
        requires: caller is the Admin, event is an active event and user is in the event's Readers
        effect: remove user from event.Readers and add user to event.unverifiedUsers
    register (name: String, email: String, password: String, event: Event) : (user: User)
        requires: no user with the same email registered under the same event, password at least 8 characters long
        effect: creates a new user to be added to the unverifiedUsers set for the provided event
    login (email: String, password: String, event: Event) : (user: User)
        requires: password provided matches with the password associated with the User with email email under the provided event
        effect: successfully authenticate user
    incrementReadCount (user: User)
        requires: user's event is an active event
        effect: user.readCount += 1
    incrementSkipCount (user: User)
        requires: user's event is an active event
        effect: user.skipCount += 1
    addToTotalTime (user: User, seconds: Number)
        requires: user's event is an active event
        effect: user.totalTime += seconds
```

### ApplicationAssignments

```
concept ApplicationAssignments
purpose Store application data (including read-counts) and assign them one at a time to users to read, allowing skips.
principle The admin can add applications to an active event. This should initialize all applications in the database to
    have 0 reads. Each reader is assigned one application to read at a time. Applications are assigned prioritizing
    those with the fewest reads so far, and a user cannot read an application they have already read. Applications can
    be skipped, and get prioritized if so.
state
    a set of CurrentAssignments with
        a User
        an Application
        a startTime DateTime
    a set of Applications with
        an applicantID String
        an applicantYear String
        a set of QuestionsAndAnswers
        a readsCompleted Number
        a readers set of Users
    a set of QuestionsAndAnswers with
        a question String
        an answer String
actions
    addApplications (adder: User, event: Event, applications: set of Applications): (application: Applications)
        requires: adder is the Admin and event is an active event
        effect: create applications for each applicantID associated with an applicantYear, a set of questions and
        answers from the application, and readsCompleted = 0
    getNextAssignment (user: User, event: Event, startTime: DateTime): (assignment: CurrentAssignments)
        requires: event is the active event, and user is a reader for event
        effect: create a CurrentAssignment for this user with startTime, with an application
            that does not have user in readers set
```

### ReviewRecords

```
concept ReviewRecords
purpose Store reviews of applications, with editing, flagging, and comments
principle A user can submit a review for an application, and overwrite their past reviews with edits. They can also
    add a flag to an application, and write comments for other users to see.
state
    a set of Reviews with
        an Application
        an reader User
        a submittedAt DateTime
        a set of Scores
    a set of Scores with
        a criterion String
        a value Number
    a set of RedFlags with
        an Application
        an author User
    a set of Comments with
        an Application
        an author User
        a text String
        a quotedSnippet String
actions
    submitReview (reader: User, application: Application, scores: set of Scores, currentTime: DateTime): (review: Reviews)
        requires: application is in the active event, reader is a reader for that event,
        reader currently assigned this application
        effect: create a Review with the provided details
    editReview (reader: User, review: Reviews, scores: set of Scores)
        requires: reader is the creator of the review (review.reader)
        effect: overwrite the review’s scores to the provided updated values
    addRedFlag (author: User, application: Application)
        requires: application belongs to active event and author is a reader
        effect: add a RedFlag for this application associated with the author to the set of RedFlags
    addComment (author: User, application: Application, text: String, quotedSnippet: String)
        requires: application belongs to active event and author is currently reading the application
        effect: add comment with provided details to the set of comments
```

### Syncs

```
sync UpdateStatsOnSubmit
when
    ApplicationAssignments.getNextAssignment (user: User, event: Event, startTime: DateTime): (assignment: CurrentAssignments)
    ReviewRecords.submitReview (reader: User, application, currentTime: DateTime)
then
    EventDirectory.incrementReadCount (user)
    EventDirectory.addToTotalTime (user, seconds: (currentTime - startTime) as a Number)
```

```
sync CountRedFlagAsRead
when
    ReviewRecords.addRedFlag (author: User, application)
then
    ApplicationAssignments.getNextAssignment (user, application.event, time): (assignment : CurrentAssignments)
```

```
sync SkipRead
when
    Request.SkipRead (user, application, time)
then
    ApplicationAssignments.getNextAssignment (user, application.event, time): (assignment : CurrentAssignments)
```

## UI Sketches

![AppReader UI Sketches](/assets/assignment2/appreader_ui_sketches.png)

## User Journey

A user logs into the webapp after being approved by the site admin as a reader for an active event. On the home page, they see a button to begin reading applications and a display of their progress so far, with a scatter-plot showing how their application read count (currently at 0) and average time spent reading each application (also currently at 0) compares to others people's statistics, as well as a leaderboard ranking people by the number of applications they have read, also showing average read time and number of skips used.

The user goes to start reading applications by clicking to the "READ" page, where they are automatically greeted with their first application to read. They see a progress bar showing how many applications they have read out of the required number (e.g. 0/200). They click on the [?] button next to the scoring section on the left to read more in detail the scoring rubric. After reading the application, they input a score of 3 for one criterion, "Passion," and a score of 5 for the second criterion, "Skill." Satisfied with their choice, they click “SUBMIT,” and the system selects a different application to read, one with the fewest reads so far.

The next application opens. This time, the user can see a comment left by a previous reader, highlighting one sentence of the application, "I won 100 hackathons in 1 month," and commenting, "We have to admit this person." The user submits a 5 for both criteria, submits the review, and moves on. They then realize that they want to change their mind about the first score they submitted, so they go back to their first read, change the "Skill" score to 4, submit, and continue reading.

As the user starts reading their third application, they realize they recognize the applicant and decide it would be a conflict of interest. They click “Skip” and are given a different, fourth application to read.
