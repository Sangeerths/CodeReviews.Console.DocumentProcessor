# PhoneBook

A console-based contact management application built with **C# / .NET**, **Spectre.Console** for a rich terminal UI, and **Entity Framework Core** with **SQL Server LocalDB** for persistence.

## Features

* **Insert Contact** — Add a new contact with first name, last name, organization, job title, notes, phone numbers, and email addresses.
* **Delete Contact** — Search for a contact by name and remove it after confirmation.
* **Modify Contact** — Search for a contact and update its details, including adding new phone numbers or email addresses.
* **View Contact** — Search by name and display complete contact details.
* **View All Contacts** — Display all contacts in a formatted table.
* **Send Message** — Send a message to a contact through Email or SMS using their stored contact information.
* **Input Validation** — Validate names, phone numbers, and email addresses before saving.
* **Styled Terminal UI** — Interactive headers, panels, tables, prompts, and messages using Spectre.Console.
* **Excel Import** — Import contact data from `.xlsx` or `.xls`  or `csv` files.
* **Excel Reports** — Export contacts, phone numbers, and emails into separate Excel worksheets or PDF files.

## Reports & Data Import

The application supports both **report generation** and **database seeding from external files**.

### Excel Report

Generates an `.xlsx` file containing separate worksheets for:

* `Contacts`
* `PhoneNumbers`
* `Emails`

The Excel report is generated using **ClosedXML**.

### PDF Report

Generates a `.pdf` report containing separate sections for:

* Contacts
* Phone Numbers
* Emails

PDF reports are generated using **QuestPDF** and saved in the `Reports/` folder.

### CSV / Excel Import

The application can seed the database from:

* `.csv`
* `.xls`
* `.xlsx`

CSV files are processed using **CsvHelper**, while Excel files are processed using **ExcelDataReader**.

The seed file should contain the following columns:

```text
ContactId, FirstName, LastName, OrganizationName, JobTitle, Notes, CreatedAt,
PhoneNumber, PhoneLabel, PhoneCreatedAt,
EmailAddress, EmailLabel, EmailCreatedAt, EmailUpdatedAt
```

Place the seed file in the project directory and configure its **Copy to Output Directory** property as:

```text
Copy if newer
```

This ensures that the file is available when the application runs.

## Tech Stack

| Layer            | Technology                 |
| ---------------- | -------------------------- |
| Language         | C#                         |
| Framework        | .NET                       |
| User Interface   | Spectre.Console            |
| ORM              | Entity Framework Core      |
| Database         | SQL Server LocalDB         |
| Excel Processing | ClosedXML, ExcelDataReader |
| CSV Processing   | CsvHelper                  |
| PDF Generation   | QuestPDF                   |
| Email            | Mailgun                    |
| SMS              | Twilio                     |

## Project Structure

```text
PhoneBook/
├── Controller/
│   └── ContactController.cs
│       # Orchestrates service calls and handles success/error UI
│
├── Services/
│   └── ContactService.cs
│       # Handles contact-related business and database operations
│
├── Repository/
│   └── PhoneBookContext.cs
│       # Entity Framework Core DbContext
│
├── Models/
│   ├── Contact.cs
│   ├── PhoneNumberDetail.cs
│   └── EmailDetail.cs
│
├── Messaging/
│   └── MessageSender.cs
│       # Handles Email and SMS messaging
│
├── Validation/
│   └── InputValidator.cs
│       # Handles input validation
│
├── UI/
│   ├── ConsoleMenu.cs
│   │   # Main menu and user interaction flow
│   └── ConsoleUI.cs
│       # Reusable console UI components
│
├── Migrations/
│   # Entity Framework Core migrations
│
└── Program.cs
    # Application entry point
```

## Data Model

A `Contact` contains:

* First name
* Last name
* Organization name
* Job title
* Notes
* One or more phone numbers
* One or more email addresses

Each phone number and email address can have a label.

Supported labels:

```text
Mobile
Home
Work
Other
```

## Getting Started

### Prerequisites

* [.NET SDK](https://dotnet.microsoft.com/) 10.0 or later
* SQL Server LocalDB

SQL Server LocalDB is included with Visual Studio or can be installed separately through SQL Server Express.

### Clone the Repository

```bash
git clone https://github.com/Sangeerths/PhoneBook.git
cd PhoneBook
```

### Apply Database Migrations

Run the following command to create/update the database:

```bash
dotnet ef database update
```

### Run the Application

```bash
dotnet run
```

The application uses SQL Server LocalDB with the following connection:

```text
Server=(localdb)\mssqllocaldb;Database=PhoneBookDb;Trusted_Connection=True;
```

## Configuration

The **Send Message** feature uses:

* **Mailgun** for Email
* **Twilio** for SMS

Configuration values are loaded from environment variables.

Create a `.env` file based on `.env.sample` and provide your own credentials.

### Environment Variables

| Variable        | Purpose                     |
| --------------- | --------------------------- |
| `MailgunApiKey` | Mailgun private API key     |
| `MailgunDomain` | Mailgun sending domain      |
| `EmailFrom`     | Email sender address        |
| `TwilioSid`     | Twilio Account SID          |
| `TwilioToken`   | Twilio authentication token |
| `SmsFrom`       | Twilio sender phone number  |

> **Important:** Never commit real API keys, authentication tokens, or other secrets to the repository. Only commit `.env.sample` with placeholder values.

## Mailgun Setup

1. Create a Mailgun account.
2. Open **Sending → Domains**.
3. Use the provided sandbox domain for testing or configure your own verified domain.
4. Add authorized recipients when using a sandbox domain.
5. Go to **Settings → API Keys** and copy the Private API Key.
6. Set the following environment variables:

```text
MailgunApiKey
MailgunDomain
EmailFrom
```

For production use, a verified domain with the required DNS records should be configured.

## Twilio Setup

1. Create a Twilio account.
2. Open the Twilio Console.
3. Copy your **Account SID** and **Auth Token**.
4. Obtain a phone number capable of sending SMS.
5. Configure:

```text
TwilioSid
TwilioToken
SmsFrom
```

Trial accounts may require recipient phone numbers to be verified before SMS can be sent.

## Usage

After launching the application, the main menu provides the following operations:

### Insert Contact

Enter the contact's:

* First name
* Last name
* Organization
* Job title
* Notes
* Phone numbers
* Email addresses

The entered information is displayed for review before saving.

### Delete Contact

Search for a contact by name and confirm the deletion.

### Modify Contact

Search for an existing contact, select the information to update, review the changes, and confirm the update.

### View Contact

Search for a contact and display all stored information.

### View All Contacts

Display all contacts in a formatted console table.

### Send Message

Search for a contact and choose between:

* Email
* SMS

The application uses the stored email address or phone number to send the message.

### Reports

Generate:

* Excel reports
* PDF reports

### Import Contacts

Import contact information from:

* CSV
* XLS
* XLSX

### Exit

Close the application.

## Architectural Choices

### Layered Architecture

The application follows a layered structure:

```text
Controller
    ↓
Services
    ↓
Repository
    ↓
Models
```

This separates responsibilities between console interaction, business logic, and data access.

### Controller

The controller coordinates service calls and handles success/error feedback without containing the main business logic.

### Service Layer

`ContactService` handles contact-related operations and database interactions through Entity Framework Core.

### Repository / DbContext

`PhoneBookContext` manages the database connection, entity relationships, migrations, and data seeding.

### Direct Entity Usage

DTOs were avoided because this is a console-only application without an external API consumer. Using the entities directly reduces unnecessary mapping code while validation remains separated into `InputValidator`.

### Messaging Separation

Messaging functionality is isolated inside `MessageSender`.

This keeps Email and SMS provider-specific logic separate from the contact-management functionality and allows providers to be changed without modifying the main contact service.

### Validation

Input validation is handled separately through `InputValidator`, keeping validation rules independent from the UI and database operations.

## Reflection

Working on the messaging, reporting, and data-import features provided practical experience with external services, file processing, and document generation.

The messaging functionality required understanding the difference between API keys, account credentials, and authentication tokens. Testing also highlighted restrictions associated with Mailgun sandbox accounts and Twilio trial accounts.

The reporting and import functionality provided experience working with different file formats such as CSV, Excel, and PDF, while also handling database seeding and maintaining relationships between contacts, phone numbers, and email addresses.

Overall, the project helped strengthen my understanding of **C#, Entity Framework Core, SQL Server, layered architecture, validation, external service integration, file processing, and console application design**.
