# CourtPilot — Product Requirements

## 1. Overview

CourtPilot is an AI-powered application that helps users discover and reserve sports courts.

The initial version focuses exclusively on municipal padel courts owned and managed by the Community of Madrid and made available through its official reservation system.

---

## 2. Problem

Users currently need to navigate the existing reservation system manually to find available padel courts.

The process can require multiple steps:

- finding a suitable sports facility
- selecting a date
- finding available courts
- selecting a time
- completing the reservation process

Users should be able to express their preferences more naturally and receive suitable available options.

The current booking system opens reservations at 00:00, 48 hours before the selected date. This requires users to be available at that exact time to secure a court, creating a competitive booking process where courts can quickly become unavailable.

---

## 3. Target User

The initial target user is a person who wants to reserve a municipal padel court owned and managed by the Community of Madrid, without having to adapt their schedule to the limitations of the current booking system.

The user is assumed to:

- have access to the required reservation account
- be eligible to make reservations
- have the required payment method if payment is necessary

---

## 4. Goals

### Primary goals

- Allow users to find available padel courts.
- Allow users to search using natural language.
- Allow users to compare available options.
- Allow users to complete a reservation.
- Allow users to plan bookings in advance.
- Make the reservation process easier than navigating the existing system manually.
- Reduce the need for users to be available when reservations become available.

### Secondary goals

- Provide clear information about facilities and available courts.
- Provide a summary before confirming the reservation.
- Provide confirmation after a successful reservation.
- Reduce the need to repeatedly check the official reservation system.

---

## 5. Non-Goals

The first version will NOT:

- support sports other than padel
- support facilities or administrative areas outside the defined Community of Madrid scope
- provide social networking functionality
- provide matchmaking with other players
- bypass authentication or reservation restrictions
- attempt to bypass rate limits or other restrictions imposed by the official reservation system
- predict availability that is not provided by the reservation system
- report a reservation as successful without confirmation from the official reservation system
- automatically make reservations without prior user authorization
- perform reservations based solely on a user's search or stated preferences
- allow users to cancel scheduled reservations after the cancellation deadline

---

## 6. Core User Flows

### 6.1 Search for a court

A user can specify:

- date
- preferred time
- location or facility
- light or no light

Example:

> "Find me a padel court tomorrow after 18:00 near Chamartín."

The system interprets the request and searches for matching availability.

---

### 6.2 Review available options

The system presents available courts including:

- facility
- court
- date
- start time
- duration
- price

The user can select an option.

---

### 6.3 Make a reservation

The user selects an available option.

Before performing an irreversible action, the system must clearly display the reservation details and request confirmation.

Example:

> Chamartín Sports Centre  
> Court 4  
> Thursday, October 8  
> No light  
> 19:00–20:30  
> €XX

The user explicitly confirms the reservation.

---

### 6.4 Reservation confirmation

After successful reservation, the application displays:

- reservation details
- reservation identifier
- facility
- court
- date and time

If the result of the reservation cannot be confirmed, the application must not report the reservation as successful.

---

### 6.5 Schedule a reservation

A user can configure and authorize a reservation in advance.

The user specifies the required reservation criteria, such as:

- date
- preferred time
- location or facility
- court preferences
- light or no light

The system stores the authorized reservation request and schedules the booking attempt for when the requested booking becomes available.

The system shall determine the booking attempt time according to the availability rules of the official reservation system.

The user can view the status of the scheduled reservation.

---

### 6.6 Cancel a scheduled reservation

A user can cancel a scheduled reservation before the cancellation deadline.

The cancellation deadline is 30 minutes before the scheduled booking attempt.

Once the cancellation deadline has been reached, the user can no longer cancel the scheduled reservation through the application.

---

### 6.7 Scheduled reservation status

A scheduled reservation attempt may have the following states:

- scheduled
- cancelled
- booking in progress
- confirmed
- failed
- unable to confirm

A scheduled reservation attempt is not considered an actual reservation until the official reservation system confirms that the reservation was created.

The application shall clearly communicate the current status to the user.

---

### 6.8 View active and past reservations

The user can view their confirmed reservations in the application.

The application shall separate confirmed reservations into:

- **Active reservations:** confirmed reservations whose scheduled start date and time are still in the future.
- **Past reservations:** confirmed reservations whose scheduled start date and time have been reached or have already passed.

A confirmed reservation shall remain an active reservation until its scheduled start date and time is reached. It shall then be classified as a past reservation.

Scheduled reservation attempts that have not yet been confirmed by the official reservation system shall not be shown as confirmed active reservations. They shall remain in the scheduled reservation section until they are confirmed, cancelled, failed, or become unable to confirm.

For each past or active reservation, the application should display, at minimum:

- facility
- court
- date and time
- duration
- price
- reservation identifier
- current or final reservation status

---

## 7. AI Requirements

The AI component should help users interact with the reservation system using natural language.

The AI may:

- interpret user requests
- extract reservation preferences
- ask clarification questions
- select appropriate tools
- search for available courts
- explain available options

The AI must NOT:

- invent availability
- invent prices
- invent reservation information
- bypass authentication
- bypass reservation restrictions
- perform irreversible actions without prior user authorization
- create or attempt to create a reservation based solely on a user's search or stated preferences
- request a second confirmation when a valid prior authorization explicitly covers a scheduled reservation attempt
- claim that a reservation was successful without confirmation from the reservation system

The system should prefer deterministic backend data over LLM-generated information whenever both are available.

---

## 8. Functional Requirements

### FR-001 — Facility search

The system shall allow users to search for municipal sports facilities.

### FR-002 — Availability search

The system shall retrieve available padel courts for a specified date and time range.

### FR-003 — Natural language search

The system shall allow users to express search criteria using natural language.

### FR-004 — Reservation

The system shall allow users to create a reservation for an available court.

### FR-005 — Reservation authorization

The system shall require explicit user authorization before creating or attempting to create a reservation.

A user may provide this authorization in advance, allowing the system to attempt the reservation automatically when the requested booking becomes available.

The system shall only attempt the reservation according to the criteria previously authorized by the user.

### FR-006 — Scheduled reservations

The system shall allow users to schedule a reservation attempt for a future booking window.

The system shall store the user's reservation criteria and authorization until the scheduled booking attempt is executed or cancelled.

### FR-007 — Scheduled reservation cancellation

The system shall allow users to cancel a scheduled reservation before the cancellation deadline.

The cancellation deadline shall be 30 minutes before the scheduled booking attempt.

### FR-008 — Cancellation deadline

The system shall prevent users from cancelling a scheduled reservation once the cancellation deadline has been reached.

### FR-009 — Reservation execution

When the booking window becomes available, the system shall attempt to create the reservation according to the user's previously authorized criteria.

The system shall not require additional user confirmation at the time of execution if valid prior authorization exists.

### FR-010 — Reservation attempt status

The system shall provide the current status of each reservation attempt.

The system shall distinguish between at least:

- scheduled
- booking in progress
- confirmed
- cancelled
- failed
- unable to confirm

A reservation attempt shall only enter the `confirmed` state after the official reservation system confirms that the reservation was successfully created.

### FR-011 — External system errors

If the official reservation system is unavailable or returns an unexpected error, the application shall inform the user of the reservation outcome.

If the application cannot determine whether the reservation was created, it shall use the `unable to confirm` state and shall not report the reservation as successful.

### FR-012 — Duplicate reservation prevention

The system shall prevent duplicate reservation attempts for the same user, court, date, and time slot.

### FR-013 — Booking conflict handling

The system shall handle cases where multiple users attempt to reserve the same court and time slot.

The system shall process competing reservation attempts in a controlled and deterministic order and shall not treat multiple users as successful for the same court and time slot.

The system shall rely on the official reservation system as the final authority on whether a reservation was successfully created.

### FR-014 — Active reservations

The system shall provide users with a view of their confirmed active reservations.

A confirmed reservation shall be considered active while its scheduled start date and time are in the future.

### FR-015 — Past reservations

The system shall provide users with a history of their confirmed past reservations.

A confirmed reservation shall be classified as a past reservation when its scheduled start date and time is reached.

This classification shall be based on the current server-side date and time and shall not require the user to manually move or update the reservation.

### FR-016 — Reservation history details

The system shall store and display sufficient information for users to review their confirmed reservations, including at least the facility, court, date, start time, duration, price, and official reservation identifier when available.


---

## 9. Constraints

The application must respect:

- the reservation system's authentication requirements
- reservation rules
- availability restrictions
- cancellation rules
- applicable terms of service
- applicable laws and regulations

The application must not rely on undocumented assumptions about the external reservation system.

### Scheduled reservation constraints

- A scheduled reservation must be cancelled at least 30 minutes before its scheduled booking attempt.
- Once the cancellation deadline has been reached, the scheduled reservation must be treated as locked for execution.
- The system must not attempt to execute a scheduled reservation that was successfully cancelled before the cancellation deadline.
- Booking and cancellation deadlines must be calculated using a consistent server-side time reference and the applicable local time zone.

### Usage restrictions

The application must implement safeguards against abusive or excessive use of the reservation service.

These safeguards may include:

- a maximum number of reservations per user within a defined period
- a maximum total duration of reservations within a defined period
- limits on the number of simultaneous scheduled reservation requests
- restrictions on repeated or overlapping reservations
- temporary restrictions when abnormal reservation activity is detected

Usage limits shall be designed to prevent users from unnecessarily occupying a disproportionate number of courts while allowing normal use of the service.

The application must not:

- overload the official reservation system with unnecessary requests
- circumvent technical or business restrictions imposed by the official system
- assume that an available court remains available until the reservation is confirmed

The official reservation system shall be considered the final source of truth for:

- court availability
- reservation status
- reservation confirmation
- reservation identifiers
- prices and applicable fees

---

## 10. Non-Functional Requirements

### NFR-001 — Concurrency

The system shall handle concurrent booking attempts for the same court and time slot without treating multiple users as successful.

### NFR-002 — Request control

The system shall control the number and frequency of requests sent to the official reservation system.

The application shall avoid generating unnecessary or duplicate requests.

### NFR-003 — Scalability

The system shall be able to handle a large number of users requesting reservations simultaneously without generating an equivalent number of uncontrolled requests to the official reservation system.

### NFR-004 — Reliability

The system shall handle temporary failures, timeouts, and unavailability of the official reservation system gracefully.

### NFR-005 — Data consistency

The system shall not consider a court available solely because it was previously reported as available.

Availability shall be revalidated when necessary before attempting to complete a reservation.

### NFR-006 — Reservation integrity

The system shall never report a reservation as successful unless the official reservation system has confirmed it.

### NFR-007 — Failure recovery

If the result of a reservation request cannot be determined, the system shall not automatically repeat the request in a way that could create a duplicate reservation.

### NFR-008 — User feedback

The system shall clearly communicate the outcome of each reservation attempt to the user.

### NFR-009 — Scheduled reservation consistency

The system shall ensure that a scheduled reservation cannot transition from cancelled back to executable after the cancellation deadline.

### NFR-010 — Cancellation and execution concurrency

The system shall handle simultaneous cancellation and booking execution requests safely.

The system must prevent a scheduled reservation from being both cancelled and executed as a result of concurrent operations.

### NFR-011 — Time consistency

All booking and cancellation deadlines shall be calculated using a consistent server-side clock and the applicable local time zone.

### NFR-012 — Usage control

The system shall enforce the configured limits on the number, duration, and frequency of reservations and scheduled reservation attempts.

The system shall reject or prevent new reservation requests when a user exceeds an applicable usage limit.

### NFR-013 — Abuse prevention

The system shall detect and restrict abnormal reservation patterns that could indicate abusive use of the application.

The system shall apply restrictions without affecting users whose activity remains within the configured usage limits.

---

### NFR-014 — Reservation history consistency

The system shall preserve confirmed reservation records so that users can view their reservation history after the scheduled reservation time has passed.

The system shall determine whether a confirmed reservation is active or past using the current server-side date and time.

The classification shall update automatically when the scheduled start date and time is reached.

The system shall not require a background job to change a reservation from active to past if the same classification can be derived reliably from stored reservation data and the current server-side time.

---

## 11. Success Criteria

The MVP is considered successful when a user can:

1. Express a reservation request using natural language.
2. Receive real available options.
3. Select an option.
4. Either explicitly confirm an immediate reservation or authorize a future reservation attempt.
5. View the scheduled reservation and its status.
6. Cancel the scheduled reservation before the cancellation deadline.
7. Have the application attempt the reservation when the booking window opens.
8. Successfully complete the reservation when the official system confirms it.
9. Receive confirmation.
10. View confirmed reservations as active until their scheduled start date and time is reached.
11. View confirmed reservations as past reservations after their scheduled start date and time is reached.

The MVP should also handle the following scenarios correctly:

1. Multiple users attempt to reserve the same court and time slot.
2. A large number of users request reservations simultaneously.
3. A user attempts to reserve more courts or hours than the configured usage limits allow.
4. A user creates an excessive number of scheduled reservation requests.
5. The official reservation system becomes temporarily unavailable.
6. A reservation request times out.
7. The official system rejects a reservation because the court is no longer available.
8. The application cannot determine whether a reservation was successfully created.
9. A user cancels a scheduled reservation before the cancellation deadline.
10. A user attempts to cancel a scheduled reservation after the cancellation deadline.
11. A cancellation request and a booking execution occur at approximately the same time.
12. A cancelled scheduled reservation is not executed.
13. A confirmed reservation remains classified as active until its scheduled start date and time is reached.
14. A confirmed reservation is classified as past once its scheduled start date and time is reached.
15. Scheduled reservation attempts are not incorrectly displayed as confirmed reservations.

AI-specific success criteria:

- The system correctly interprets common reservation requests.
- The agent selects the appropriate tools.
- The system does not hallucinate availability.
- The agent asks for missing information when necessary.
- Irreversible actions require explicit confirmation unless the user has already provided valid authorization that explicitly covers the scheduled reservation attempt.
- The agent accurately communicates reservation failures and uncertain outcomes.