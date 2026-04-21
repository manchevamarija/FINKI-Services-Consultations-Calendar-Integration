# FINKI Consultations Calendar Integration

I extended the FINKI consultations system with calendar synchronization support for professors and calendar invitation support for students.

The main goal of this project is to keep consultation terms synchronized with external calendar providers, primarily **Google Calendar** and **Outlook Calendar**. When a professor creates, updates, moves, deletes, activates, or deactivates a consultation in the consultations system, the same change is reflected in the connected professor calendars.

Students are also included through calendar invitations. When a student registers for a consultation, the student is added as an attendee to the professor's calendar event and receives a calendar invitation.

## Main Goal

Professors often manage their schedule through external calendars such as Outlook Calendar or Google Calendar.

With this project, I wanted to make the consultations system and the professor calendars work together. If a consultation is changed in the application, the corresponding calendar event should also be changed automatically.

The project also supports **reverse synchronization**. This means that if a professor deletes a consultation event directly from Google Calendar or Outlook Calendar, the consultations system can detect that change and update the consultation status accordingly.

## Implemented Features

### Professor Calendar Integration

I implemented synchronization with:

- Google Calendar
- Outlook Calendar

Supported operations:

- Create consultation events in Google Calendar
- Create consultation events in Outlook Calendar
- Update calendar events when consultation data changes
- Delete calendar events when a consultation is deleted or deactivated
- Reactivate consultations and recreate missing calendar events
- Handle consultation status changes: `ACTIVE` / `INACTIVE`
- Store calendar event IDs for safe synchronization
- Synchronize both regular and additional consultation terms

### Reverse Synchronization

I also implemented reverse synchronization for professor calendars.

If a consultation event is deleted directly from:

- Google Calendar
- Outlook Calendar

the system detects the deletion and updates the consultation in the application.

When a deletion is detected in one calendar provider, the system also removes the matching event from the other connected calendar provider and marks the consultation as inactive.

This keeps the internal consultations data consistent with the professor's external calendars.

### Multi-threaded Reverse Sync

Reverse synchronization is executed through a dedicated scheduler and worker setup.

This allows the system to process multiple professor calendars more efficiently without blocking the rest of the application.

The reverse sync process avoids duplicate work for the same professor and calendar provider, while still allowing multiple calendar checks to run in parallel.

### Student Calendar Support

Student registration for consultations is supported through the existing attendance flow and calendar invitations.

When a student registers for a consultation, the student is added as an attendee to the professor's calendar event.

This means that:

- if the consultation exists in Outlook Calendar, the student receives an Outlook calendar invitation;
- the event can appear in the student's Outlook calendar through the invitation;
- if the consultation is also synchronized with Google Calendar, the student can also receive a Google Calendar invitation;
- in Outlook, the event is usually added to the student's calendar through the invitation flow;
- in Google Calendar, the event may appear after the student accepts the invitation, depending on the student's Google Calendar settings.

When a student cancels their registration, the student is removed from the attendee list of the calendar event.

Direct creation of separate calendar events inside each student's personal calendar is not implemented. Instead, the project uses the attendee invitation approach, which avoids requiring every student to connect their personal Outlook or Google account to the application.

## Future Improvements

Possible future improvements include:

- Webhook-based calendar synchronization instead of polling
- More efficient filtering of reverse-sync candidates
- Separate student calendar synchronization
- Direct student Outlook Calendar integration
- Direct student Google Calendar integration
- Better calendar notification control
- Calendar sync monitoring/dashboard

## Technologies Used

- Java 17
- Spring Boot
- Spring MVC
- Spring Data JPA
- Hibernate
- PostgreSQL
- Thymeleaf
- Maven
- Google Calendar API
- Microsoft Graph / Outlook Calendar API

## Project Structure

The project is organized around:

- consultation management
- attendance management
- calendar synchronization services
- reverse synchronization schedulers
- persistence layer with JPA repositories
- Thymeleaf-based UI

## Running the Project

### Prerequisites

Before running the project, the following tools and services are needed:

- Java 17
- Maven
- PostgreSQL
- Google Calendar API credentials
- Microsoft Graph / Outlook credentials

### Configuration

The application uses local configuration for the database and calendar credentials.

For local setup, create an `application.properties` file and add the needed values for:

- database connection
- Google Calendar credentials
- Outlook / Microsoft Graph credentials
- reverse sync settings

These values are not included in the repository.


### Start the Application

On Windows:

```bash
mvnw.cmd spring-boot:run
```

On Linux / macOS:

```bash
./mvnw spring-boot:run
```

The application runs at:

```text
http://localhost:8080
```

## Build

To compile the project:

On Windows:

```bash
mvnw.cmd clean compile
```

On Linux / macOS:

```bash
./mvnw clean compile
```

## Example Local Configuration

```properties
spring.datasource.url=jdbc:postgresql://localhost:5432/your_db
spring.datasource.username=your_username
spring.datasource.password=your_password
spring.datasource.driver-class-name=org.postgresql.Driver

spring.jpa.hibernate.ddl-auto=validate

calendar.reverse-sync.fixed-delay-ms=10000
calendar.reverse-sync.initial-delay-ms=5000
calendar.reverse-sync.worker-pool-size=4
calendar.reverse-sync.queue-capacity=50
calendar.scheduler.pool-size=4

google.calendar.client-id=YOUR_GOOGLE_CLIENT_ID
google.calendar.client-secret=YOUR_GOOGLE_CLIENT_SECRET

ms.graph.client-id=YOUR_MICROSOFT_CLIENT_ID
ms.graph.client-secret=YOUR_MICROSOFT_CLIENT_SECRET
ms.graph.tenant-id=common
```

## Summary

This project adds practical calendar integration to the FINKI consultations system.

The main focus is professor calendar synchronization with Google Calendar and Outlook Calendar, including support for:

- create
- update
- delete
- activate / deactivate
- reverse synchronization

The project also includes student calendar invitation support through attendee invitations.

The project is designed as an extension of the existing consultations workflow, with a focus on keeping external calendars and internal consultation data consistent.

