# Problem Set 1

## Exercise 1: Reading a concept

1. Firstly, for each item in a registry, the sum of the count in the request for that item and the counts for all purchases made for that item in that registry should always equal the sum of the counts for all times the item was added to the registry. Secondly, every purchase made in a registry must be associated with exactly one request in that registry for the same item. The first invariant is most important because it fulfills the purpose of tracking requested gifts by ensuring givers will know the correct number of whichever items are still available for purchase. The action `purchase` is most affected by this, and this action preserves the invariant by decrementing the count in the corresponding request for the item being purchased.

2. removeItem could potentially break the first invariant, as the spec currently does not specify what happens to the counts of removed items that are recorded in the set of purchases; it only states that the corresponding request is removed from the registry, not any of the corresponding purchases for the removed item. Suppose we addItem(A, 10), then a giver purchases 3. The request for that item now has a count of 7 remaining. At this point, the invariant holds: 7 + 3 = 10. But if we perform removeItem(A), and if this resets the count requested for the item to 0 while maintaining the purchases made, then a later addItem(A, 2) might break the invariant: 2 + 3 ≠ 2. This could be remedied by either not allowing the user to add back an item with a count less than the total count already purchased, or creating an archive system that keeps the request in the registry but hides it from view.

3. A registry can be repeatedly opened and closed. A reason to allow this may be that the recipient of the registry may want to temporarily close a registry and add/remove items from it before re-opening it, so that they don't cause any confusion by changing the registry while givers are looking at it.

4. Yes, this may matter in practice, as it may clutter users' profiles (or wherever else a registry is displayed), leading to confusion for both the recipient and givers if the recipient decides to re-make a registry from scratch by creating a new one, as well as annoyance for the recipient if they accidentally create a registry that they then cannot get rid of.

5. Firstly, it's likely that the recipient may want to query for the list of all purchases made so that they can know who gifted them what. Secondly, it's likely that the givers would want to query for the list of all items that still available for purchase.

6. For the state, I would add a purchasesVisibleToOwner Flag to Registry, so that the recipient can choose for each registry they create whether or not they want to be able to see purchases. To the actions, I would add hidePurchasesFromOwner(registry) which sets purchasesVisibleToOwner to false for the provided registry, and a showPurchasesToOwner(registry) that sets purchasesVisibleToOwner to true for the provided registry.

7. SKU codes are preferable because they are unique and is more flexible; it doesn't require a name, price, etc. Instead, we can have a separate concept for item descriptions.

## Exercise 2: Extending a familiar concept

1. See below.

2. See below.

3. No two users share a username. This invariant is preserved by the precondition of the `register` action; it requires that a user with the provided username does not already exist.

4. See below.

**concept** PasswordAuthentication
\
**purpose** limit access to known users
\
**principle** after a user registers with a username and a password,
they can authenticate with that same username and password
and be treated each time as the same user
\
**state**
\
&emsp;a set of Users with
\
&emsp;&emsp;a username String
\
&emsp;&emsp;a password String
\
&emsp;&emsp;a confirmed Bool
\
&emsp;a set of Confirmations with
\
&emsp;&emsp;a username String
\
&emsp;&emsp;a token String
\
**actions**
\
&emsp;register (username: String, password: String): (user: User, token: String)
\
&emsp;&emsp;**requires** user with the provided username does not already exist,
\
&emsp;&emsp;&emsp;and password is nonempty
\
&emsp;&emsp;**effects** create a new user with the provided username and password,
\
&emsp;&emsp;&emsp;and confirmed set to false;
\
&emsp;&emsp;&emsp;create a new confirmation with the provided username and a random token,
\
&emsp;&emsp;&emsp;and return (user, token);
\
&emsp;authenticate (username: String, password: String): (user: User)
\
&emsp;&emsp;**requires** user exists with provided username exists,
\
&emsp;&emsp;&emsp;provided password matches the password corresponding to
\
&emsp;&emsp;&emsp;the user with the provided username,
\
&emsp;&emsp;&emsp;and user has been confirmed
\
&emsp;&emsp;**effects** returns the corresponding user
\
&emsp;confirm (username: String, token: String)
\
&emsp;&emsp;**requires** user with provided username exists,
\
&emsp;&emsp;&emsp;and has not already been confirmed
\
&emsp;&emsp;**effects** change user's confirmed status to true

## Exercise 3: Comparing concepts

**concept** PersonalAccessToken
\
**purpose** lets users create a secure alternative to a password that allows
\
&emsp;for defined scopes/permissions and automatic expiry when
\
&emsp;authenticating to GitHub in the command line or with the API
\
**principle** after a user creates a token with chosen scopes to grant (and optional expiration),
\
&emsp;they can authenticate with that token when using the GitHub API or the command line;
\
&emsp;access to private resources is limited by the scopes that the token has been granted,
\
&emsp;and tokens can expire or be deleted.
\
**state**
\
&emsp; a set of Tokens with
\
&emsp;&emsp;an owner User
\
&emsp;&emsp;a note String
\
&emsp;&emsp;a value String
\
&emsp;&emsp;an optional scope String
\
&emsp;&emsp;an optional expiry Number
\
**actions**
\
&emsp;createToken (owner: User, note: String, scope: String, expiry: Number): (value: String)
\
&emsp;&emsp;**requires** the provided owner is an existing user, scope is the name of one of
\
&emsp;&emsp;&emsp;the [available scopes](https://docs.github.com/en/apps/oauth-apps/building-oauth-apps/scopes-for-oauth-apps#available-scopes), and expiry, if provided, is a number between 1 and 366
\
&emsp;&emsp;**effects** create a new token for owner with a description provided by note,
\
&emsp;&emsp;&emsp;a randomly generated 40-character long value consisting of letters, numbers, and special characters,
\
&emsp;&emsp;&emsp;a scope corresponding to the [description](<(https://docs.github.com/en/apps/oauth-apps/building-oauth-apps/scopes-for-oauth-apps#available-scopes)>) of the chosen scope (no scope if nothing provided),
\
&emsp;&emsp;&emsp;and an expiration date of expiry days after creation (no expiration if nothing was provided)
\
&emsp;&emsp;&emsp;and returns the randomly generated value
\
&emsp;authenticate (value: String): (owner: User)
\
&emsp;&emsp;**requires** value is a token that exists and has not expired
\
&emsp;&emsp;**effects** returns the user that created the token associated with value
\
&emsp;deleteToken (owner: User, value: String)
\
&emsp;&emsp;**requires** owner is an existing user, and value is a token that exists for that user
\
&emsp;&emsp;**effects** deletes the token

_PersonalAccessToken_ differs from _PasswordAuthentication_ in several ways. For one, the username isn't used for authentication. Also, unlike with _PasswordAuthentication_, a PAT has a scope that can restrict access to resources to a subset of what is available to the user. Thirdly, one can create multiple PATs, each of which can optionally have an expiration date of up to 366 days into the future, unlike how with _PasswordAuthentication_ there is a singular password that never expires. I would probably add a section on the GitHub documentation page listing out these stated differences for users unfamiliar with these concepts.

## Exercise 4: Defining familiar Concepts

### Billable Hours Tracking

**concept** BillableHoursTracking
\
**purpose** Store work time at a company by allowing employees to mark the beginning and end of work sessions
\
&emsp;labeled by project and work to be done so that clients can be billed the total for the number of hours
\
&emsp;put in by employees on their project.
\
**principle** when an employee starts a timed work session on a project, labeled with a description of
\
&emsp;the work to be done, the system records its start; when the employee ends the session, the system records its end,
\
&emsp;with employees having the ability to edit the end time in the case that they forget to end a session;
\
&emsp;a client's total billable time equals the sum of the durations of all closed sessions for that employee and project
\
**state**
\
&emsp;a set of Employees with
\
&emsp;&emsp;an employeeID Number
\
&emsp;a set of Projects with
\
&emsp;&emsp;a projectID Number
\
&emsp;&emsp;a set of Sessions
\
&emsp;a set of Sessions with
\
&emsp;&emsp;an Employee
\
&emsp;&emsp;a description String
\
&emsp;&emsp;a start Time
\
&emsp;&emsp;an optional end Time
\
**actions**
\
&emsp;startSession (employee: Employee, projectID: Number, description: String): (session: Session)
\
&emsp;&emsp;**requires** employee is an existing employee and projectID exists in Projects,
\
&emsp;&emsp;&emsp;and there is currently no session associated with employee with no end time
\
&emsp;&emsp;**effects** create a new session under the associated project, labeled with the employee,
\
&emsp;&emsp;&emsp;provided description, start time set to the current time, and end time unset
\
&emsp;endSession (employee: Employee, end: Time)
\
&emsp;&emsp;**requires** employee is an existing employee with an associated session that
\
&emsp;&emsp;&emsp;has no end time, the start time of the session comes before the current time, and
\
&emsp;&emsp;&emsp;end, if provided, comes after the start time of the session but before the current time
\
&emsp;&emsp;**effects** if end is not provided, sets the end of the employee's session to the current time, adds the
\
&emsp;&emsp;&emsp;session to the set of sessions associated with the projectID of the session, and removes
\
&emsp;&emsp;&emsp;the session from the set of ongoing sessions; if end is provided, set end of employee's session
\
&emsp;&emsp;&emsp;to the provided time end; also increment the total hour count associated with the project
\
&emsp;&emsp;&emsp;by the difference between the session's end time and start time
\
&emsp;getCurrentProjectHours (projectID: Number): (totalHours: Number)
\
&emsp;&emsp;**requires** projectID is the ID of an existing project of a client
\
&emsp;&emsp;**effects** return the total number of hours currently recorded for the project by employees

**notes**

- Invariants: at most one open session per employee, end time > start time for every session that has an end time
- Forgetting to end a session is handled by endSession; when an employee ends a session, they can optionally input an end time
- Billing uses closed sessions only and does not count ongoing sessions that haven't been ended yet

### Conference Room Booking

**concept** ConferenceRoomBooking
\
**purpose** Allow users to book conference rooms for specific intervals of time with guarantee
\
&emsp;of no overlap with other people.
\
**principle** User books a room for an available time interval, causing others to be unable to book the
\
&emsp;room at the same time; bookings can be canceled by the user before the scheduled time
\
**state**
\
&emsp;a set of Users with
\
&emsp;&emsp;a username String
\
&emsp;&emsp;a password String
\
&emsp;a set of Rooms with
\
&emsp;&emsp;a roomName String
\
&emsp;&emsp;a set of Bookings
\
&emsp;a set of Bookings with
\
&emsp;&emsp;a User
\
&emsp;&emsp;a start DateTime
\
&emsp;&emsp;an end DateTime
\
**actions**
\
&emsp;bookRoom (roomName: String, user: User, start: DateTime, end: DateTime)
\
&emsp;&emsp;**requires** room with name roomName exists, user exists, start < end, end - start < 24 hours,
\
&emsp;&emsp;&emsp;and there is no existing booking for the same room that starts before end or ends before start
\
&emsp;&emsp;**effects** create a Booking for the associated room under the provided user for the provided time interval
\
&emsp;cancelBooking (booking: Booking, username: String, password: String)
\
&emsp;&emsp;**requires** booking exists, current time is before the start time of the booking, user with username exists,
\
&emsp;&emsp;&emsp;password is the correct password associated with the user, and user is the organizer of the booking
\
&emsp;&emsp;**effects** cancels the booking
\
&emsp;updateBooking (booking: Booking, newStart: DateTime, newEnd: DateTime, username: String, password: String)
\
&emsp;&emsp;**requires** booking exists, newStart < newEnd, end - start < 24 hours, user with username exists,
\
&emsp;&emsp;&emsp;password is the correct password associated with the user, and user is the organizer of the booking,
\
&emsp;&emsp;&emsp;and not including this booking, there is no existing booking for the same room that starts before
\
&emsp;&emsp;&emsp;newEnd or ends before newStart
\
&emsp;&emsp;**effects** update booking's start time to newStart and end time to newEnd

**notes**

- Invariants: for each room, there are no two bookings that overlap, i.e. if bookings are sorted by start time, the end time of the ith booking is before the start time of the (i + 1)th booking, if there is one; the start time comes before the end time for every booking
- Intentionally did not include restriction for users to only be able to book one conference room at a time, as a user may want to book multiple rooms for many people

### Electronic Boarding Pass

**concept** ElectronicBoardingPass
\
**purpose** Provide digital passes to passengers that allow them to board their flight and reflects
\
&emsp;up-to-date flight information
\
**principle** When a passenger receives their digital boarding pass, they can use it at the airport
\
&emsp;to automatically prove their status as passenger for their flight, and the information on the pass
\
&emsp;stays up to date with current flight data; the pass only works for their specific flight
\
**state**
\
&emsp;a set of Flights with
\
&emsp;&emsp;a flightID String
\
&emsp;&emsp;a set of Passengers
\
&emsp;&emsp;a set of Passes
\
&emsp;a set of Passengers with
\
&emsp;&emsp;a username String
\
&emsp;&emsp;a password String
\
&emsp;a set of Passes with
\
&emsp;&emsp;a Passenger
\
&emsp;&emsp;a departureLocation String
\
&emsp;&emsp;an arrivalLocation String
\
&emsp;&emsp;a departureTime Time
\
&emsp;&emsp;an arrivalTime Time
\
&emsp;&emsp;an active Flag
\
&emsp;&emsp;an optional gate String
\
**actions**
\
&emsp;issuePass (passenger: Passenger, flight: Flight): (pass: Pass)
\
&emsp;&emsp;**requires** passenger exists, flight exists, and passenger is a passenger on the flight
\
&emsp;&emsp;**effects** create a Pass for the passenger with all of the flight information on it, and active set to true
\
&emsp;updateFlightTime (flight: Flight, departureTime: Time, arrivalTime: Time)
\
&emsp;&emsp;**requires** flight exists
\
&emsp;&emsp;**effects** for all passes for this flight, update the departure time to departureTime and arrival time to arrivalTime
\
&emsp;updateFlightGate (flight: Flight, newGate: String)
\
&emsp;&emsp;**requires** flight exists, newGate is an existing gate at the airport
\
&emsp;&emsp;**effects** for all passes for this flight, update the gate to newGate
\
&emsp;usePass (flight: Flight, pass: Pass, passenger: Passenger)
\
&emsp;&emsp;**requires** flight exists, pass is a pass for this flight, pass is still active, and
\
&emsp;&emsp;&emsp; passenger is the passenger associated with the presented pass
\
&emsp;&emsp;**effects** set active to false

**notes**

- Invariants: all passes can only be used once for boarding, no two boarding passes for the same flight have different flight information
