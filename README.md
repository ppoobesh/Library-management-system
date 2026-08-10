# Student Library Management System

A web-based **Student Library Management System** developed using
**Python, Flask, SQLite, HTML, CSS, Jinja2, Pandas, QR-code processing,
and email notifications**.

The system provides separate interfaces for **Administrators** and
**Students**. Administrators can manage students, books, physical book
copies, borrowing, QR-based returns, lost books, reservations, and
reports. Students can browse books, monitor borrowed books, reserve
unavailable books, manage reservations, view their profile, and change
their password.

------------------------------------------------------------------------

## 1. Project Overview

The Student Library Management System is designed to automate common
library operations such as:

-   Student account management
-   Book and physical-copy management
-   Book borrowing
-   QR-code based book returns
-   Lost-book management
-   Book reservations
-   Reservation availability notifications
-   Borrowing limits and due dates
-   Fine calculation
-   Email notifications
-   Administrative reports
-   CSV report export

The application uses **SQLite** as its database and **Flask** as the web
framework.

------------------------------------------------------------------------

## 2. Main Features

### Administrator

The administrator can:

-   Log in through the Admin Login
-   Add students
-   Edit student information
-   Delete students when permitted
-   Configure an individual student's borrow limit
-   Activate or deactivate student accounts
-   Add books
-   Edit books
-   Delete books when permitted
-   Manage multiple physical copies of a book
-   View available copies
-   Issue books to students
-   View currently borrowed books
-   Return books by scanning/uploading the physical-copy QR code
-   Mark borrowed books as lost
-   View lost-book records
-   Manage student reservations
-   View transaction reports
-   View reservation reports
-   Export reports to CSV
-   View library summary statistics
-   Log out

### Student

Students can:

-   Log in using their register number and password
-   View their dashboard
-   View personal information
-   Browse library books
-   Check book availability
-   Reserve books that are unavailable
-   View reservation status
-   Cancel reservations
-   View currently borrowed books
-   View returned and lost-book history
-   View due dates and fines
-   View their complete profile
-   Change their password
-   Log out

------------------------------------------------------------------------

## 3. Technology Stack

  Technology   Purpose
  ------------ ----------------------------------------
  Python       Backend programming
  Flask        Web application framework
  SQLite       Database
  HTML         Page structure
  CSS          User interface styling
  Jinja2       Dynamic HTML templates
  Pandas       Report generation and CSV export
  SMTP         Email notifications
  QR Codes     Identification of physical book copies

------------------------------------------------------------------------

## 4. Project Modules

### Authentication Module

Handles:

-   Admin login
-   Student login
-   Session management
-   Role-based access
-   Logout
-   Password changes

The system uses separate Admin and Student login sections while
validating users through the application's authentication logic.

### Student Management Module

Handles:

-   Adding students
-   Editing students
-   Deleting students
-   Searching students
-   Student status
-   Student-specific borrowing limits
-   Student profile information

Typical student information includes:

-   Register number (`roll_no`)
-   Name
-   Department
-   Year
-   Email
-   Phone
-   Borrow limit
-   Status

### Book Management Module

Handles:

-   Adding books
-   Editing books
-   Deleting books
-   Searching books
-   Tracking total copies
-   Tracking available copies
-   Shelf information
-   Book price
-   Physical book-copy creation

Each book may contain multiple physical copies.

Example:

``` text
BOOK1001
├── BOOK1001-C01
├── BOOK1001-C02
└── BOOK1001-C03
```

Each physical copy has its own status and QR code.

### Borrowing Module

When an administrator issues a book, the application:

1.  Verifies that the student exists.
2.  Checks that the student's account is Active.
3.  Checks the student's borrowing limit.
4.  Checks whether an available physical copy exists.
5.  Prevents the same student from borrowing the same book twice
    simultaneously.
6.  Calculates the issue date.
7.  Calculates the due date.
8.  Creates a transaction.
9.  Marks the physical copy as Borrowed.
10. Decreases the available-book count.
11. Completes a matching Ready reservation when applicable.
12. Sends the applicable borrowing notification when configured.

The borrowing period is controlled by:

``` python
BORROW_DAYS = 14
```

Therefore:

``` text
Due Date = Issue Date + BORROW_DAYS
```

### Return Module

Book returns use the physical copy associated with the transaction.

When a book is returned, the system:

1.  Finds the active Borrowed transaction.
2.  Calculates any applicable fine.
3.  Records the return date.
4.  Changes the transaction status to Returned.
5.  Marks the physical copy as Available.
6.  Increases the book's available count.
7.  Allows reservation-processing logic to make the copy available to
    waiting students.

### QR-Based Return

Each physical book copy has a unique copy code and QR code.

Example:

``` text
BOOK1002-C01
```

The administrator can open **Scan QR to Return** and upload/scan the QR
code associated with the physical copy. The system uses the copy
identity to verify the return operation.

### Lost Book Module

A currently borrowed book can be marked as **Lost**.

The Lost Books module provides records such as:

-   Student
-   Register number
-   Book code
-   Book title
-   Copy code
-   Issue date
-   Due date
-   Lost date
-   Fine
-   Status

Lost transactions are retained as transaction history rather than being
treated as normal returns.

### Reservation Module

Students can reserve books that currently have no available copies.

Reservation statuses used by the application include:

``` text
Waiting
Ready
Completed
Cancelled
Expired
```

#### Waiting

The student is waiting for a physical copy to become available.

#### Ready

A copy has become available and the reservation is ready for collection.

#### Completed

The reserved book has been issued to the student.

#### Cancelled

The student cancelled the reservation.

#### Expired

The student did not collect the available book within the reservation
hold period.

The reservation hold period is configured using:

``` python
RESERVATION_HOLD_DAYS = 2
```

### Email Notification Module

The project uses SMTP email functionality.

Configured mail settings include:

``` python
MAIL_SERVER
MAIL_PORT
MAIL_USERNAME
MAIL_PASSWORD
MAIL_FROM
```

Email functionality includes reservation-ready and borrowing
notifications, and the project design also supports scheduled reminder
workflows such as due-date and overdue reminders.

Important: **Never commit a real email password or Gmail App Password to
a public repository.**

### Fine Management

Fine rules are controlled through configuration such as:

``` python
FINE_PER_DAY = 5
```

The fine is calculated according to overdue days when applicable.

Conceptually:

``` text
Fine = Number of Overdue Days × Fine Per Day
```

### Reports Module

The Reports dashboard summarizes library activity.

Summary information includes:

-   Total students
-   Total books
-   Total physical copies
-   Available copies
-   Currently borrowed books
-   Returned books
-   Lost books
-   Active reservations
-   Total fines

The system provides:

#### Transaction Report

Contains information such as:

-   Transaction ID
-   Register number
-   Student
-   Book code
-   Book title
-   Copy code
-   Issue date
-   Due date
-   Return date
-   Fine
-   Status

#### Reservation Report

Contains information such as:

-   Reservation ID
-   Register number
-   Student
-   Book code
-   Book title
-   Reservation date
-   Ready date
-   Status
-   Notification status

Reports can be exported as **CSV files using Pandas**.

------------------------------------------------------------------------

## 5. Database

The application uses SQLite.

Typical database location:

``` text
database/library.db
```

The database path is configured in `config.py`.

Example:

``` python
BASE_DIR = os.path.abspath(os.path.dirname(__file__))

DB_PATH = os.path.join(
    BASE_DIR,
    "database",
    "library.db"
)
```

### Main Tables

The project uses tables including:

``` text
users
students
books
book_copies
transactions
reservations
```

The exact schema should be taken from the project's
`database/schema.sql`.

### Transactions

The transaction table stores borrowing history and links a student,
book, and physical copy.

Important transaction fields include:

``` text
id
student_id
book_id
copy_id
issue_date
due_date
return_date
fine
status
```

Common transaction statuses include:

``` text
Borrowed
Returned
Lost
```

### Book Copies

Physical copies are stored separately from the main `books` record.

Important fields include:

``` text
id
book_id
copy_code
status
qrpath
```

Example statuses:

``` text
Available
Borrowed
Lost
```

### Reservations

Reservation records associate students with books and maintain the
reservation lifecycle.

Fields used by the application include values such as:

``` text
id
student_id
book_id
reservation_date
ready_date
status
notification_sent
```

Make sure the actual database schema contains every column referenced by
the Python code.

------------------------------------------------------------------------

## 6. Configuration

Application configuration is stored in:

``` text
config.py
```

Example structure:

``` python
import os

BASE_DIR = os.path.abspath(
    os.path.dirname(__file__)
)

DB_PATH = os.path.join(
    BASE_DIR,
    "database",
    "library.db"
)

SECRET_KEY = "change-this-secret-key"

BORROW_DAYS = 14
FINE_PER_DAY = 5

OVERDUE_REMINDER_DAYS = 3
DUE_REMINDER_DAYS = 3

QR_FOLDER = os.path.join(
    BASE_DIR,
    "static",
    "qrcodes"
)

UPLOAD_FOLDER = os.path.join(
    BASE_DIR,
    "uploads",
    "qr_uploads"
)

REPORT_FOLDER = os.path.join(
    BASE_DIR,
    "reports",
    "csv"
)

SCHEMA_PATH = os.path.join(
    BASE_DIR,
    "database",
    "schema.sql"
)

MAIL_SERVER = "smtp.gmail.com"
MAIL_PORT = 587

MAIL_USERNAME = "your-email@gmail.com"
MAIL_PASSWORD = "your-app-password"
MAIL_FROM = MAIL_USERNAME

RESERVATION_HOLD_DAYS = 2
```

------------------------------------------------------------------------

## 7. Security Configuration

Do **not** upload real secrets to GitHub or another public repository.

Do not publicly commit:

``` text
SECRET_KEY
MAIL_PASSWORD
real production credentials
private database backups containing personal information
```

A better approach is to use environment variables.

Example:

``` python
SECRET_KEY = os.environ.get(
    "SECRET_KEY",
    "development-only-key"
)

MAIL_USERNAME = os.environ.get(
    "MAIL_USERNAME"
)

MAIL_PASSWORD = os.environ.get(
    "MAIL_PASSWORD"
)
```

Then configure the values on the machine/server where the application
runs.

If a real Gmail App Password has already been exposed or committed,
revoke it and generate a new one.

------------------------------------------------------------------------

## 8. Recommended `.gitignore`

Create a `.gitignore` file in the project root:

``` gitignore
# Python
__pycache__/
*.py[cod]

# Virtual environments
venv/
.venv/
env/

# IDE
.vscode/
.idea/

# Environment variables
.env

# SQLite database
database/library.db
*.db-journal

# Uploaded QR images
uploads/qr_uploads/*

# Generated reports
reports/csv/*.csv

# OS files
.DS_Store
Thumbs.db
```

Whether `library.db` should be ignored depends on how the project will
be demonstrated. For a public repository, it is generally safer to
provide `schema.sql` and initialize a fresh database rather than
publishing a database containing student data.

------------------------------------------------------------------------

## 9. Suggested Project Structure

A typical structure for this project is:

``` text
Student_Library_Management_System/
│
├── app.py
├── config.py
├── requirements.txt
├── README.md
├── .gitignore
│
├── database/
│   ├── library.db
│   ├── schema.sql
│   └── ...
│
├── models/
│   └── ...
│
├── services/
│   ├── transaction_service.py
│   └── ...
│
├── utils/
│   ├── validators.py
│   ├── qr.py
│   └── ...
│
├── static/
│   ├── css/
│   │   └── style.css
│   └── qrcodes/
│
├── templates/
│   ├── base.html
│   ├── login.html
│   ├── admin/
│   ├── student/
│   ├── books/
│   ├── transactions/
│   ├── reservations/
│   └── reports/
│
├── uploads/
│   └── qr_uploads/
│
└── reports/
    └── csv/
```

Adjust this tree to match the exact folder names used in the final
project.

------------------------------------------------------------------------

## 10. Installation

### Step 1 --- Clone or download the project

``` bash
git clone <repository-url>
cd Student_Library_Management_System
```

If you are not using Git, extract the project ZIP and open the project
folder.

### Step 2 --- Create a virtual environment

Windows:

``` bash
python -m venv venv
venv\Scripts\activate
```

macOS/Linux:

``` bash
python3 -m venv venv
source venv/bin/activate
```

### Step 3 --- Install dependencies

If the project contains `requirements.txt`:

``` bash
pip install -r requirements.txt
```

Otherwise install the packages required by the final source code and
then generate a requirements file:

``` bash
pip freeze > requirements.txt
```

### Step 4 --- Configure email

Set the required mail configuration/environment variables.

For Gmail SMTP, an App Password may be required depending on the Google
account configuration. Do not use or publish a normal account password.

### Step 5 --- Initialize the database

Ensure:

``` text
database/schema.sql
```

contains the current schema and that the project's database
initialization code has created the required tables.

If an older `library.db` is reused after schema changes, verify that it
contains all new columns. `CREATE TABLE IF NOT EXISTS` does **not** add
missing columns to an existing table.

### Step 6 --- Run the application

From the project root:

``` bash
python app.py
```

The development server commonly runs at:

``` text
http://127.0.0.1:5000
```

Open that address in a browser.

------------------------------------------------------------------------

## 11. Login and Roles

The application provides two account types:

### Admin Login

The administrator uses an administrator username and password.

Admin functionality includes:

``` text
Student Management
Book Management
Issue / Return
Reservations
Reports
Lost Books
```

### Student Login

Students log in using their register number/username and password.

Student functionality includes:

``` text
My Information
My Borrowed Books
Browse Books
My Reservations
My Profile
Change Password
```

Role checks should also be enforced by Flask routes, not only hidden in
the HTML interface.

------------------------------------------------------------------------

## 12. Borrowing Workflow

``` text
Admin selects student
        ↓
Admin selects available book
        ↓
System checks student status
        ↓
System checks borrow limit
        ↓
System finds an Available physical copy
        ↓
Transaction created
        ↓
Copy status → Borrowed
        ↓
Book available count decreases
        ↓
Due date calculated
        ↓
Matching Ready reservation → Completed
        ↓
Borrow confirmation/notification
```

------------------------------------------------------------------------

## 13. Return Workflow

``` text
Admin opens Borrowed Books
        ↓
Selects "Scan QR to Return"
        ↓
Physical-copy QR is verified
        ↓
Return transaction is processed
        ↓
Return date recorded
        ↓
Fine calculated
        ↓
Transaction status → Returned
        ↓
Copy status → Available
        ↓
Available count increases
        ↓
Waiting reservation can be processed
```

------------------------------------------------------------------------

## 14. Reservation Workflow

``` text
Student browses books
        ↓
Book has no available copy
        ↓
Student reserves book
        ↓
Reservation status → Waiting
        ↓
A copy becomes available
        ↓
Reservation status → Ready
        ↓
Ready date recorded
        ↓
Student notified
        ↓
Student collects book
        ↓
Book issued
        ↓
Reservation status → Completed
```

If the student does not collect the book within the configured hold
period, the reservation can become:

``` text
Expired
```

------------------------------------------------------------------------

## 15. Lost Book Workflow

``` text
Borrowed Books
        ↓
Admin selects "Mark as Lost"
        ↓
Confirmation
        ↓
Transaction status → Lost
        ↓
Lost-book information/fine processed
        ↓
Record appears in Lost Books
```

A lost physical copy must not incorrectly become available for another
student.

------------------------------------------------------------------------

## 16. Reports

Open:

``` text
Admin Dashboard
    ↓
Reports
```

The Reports dashboard provides library statistics and access to detailed
reports.

Available detailed reports include:

``` text
Transaction Report
Reservation Report
```

Both can be exported to CSV when the corresponding export routes are
configured.

------------------------------------------------------------------------

## 17. User Interface

The interface uses a responsive CSS layout with:

-   Separate Admin and Student login cards
-   Dashboard cards
-   Responsive grid layout
-   Consistent page headers
-   Form styling
-   Search controls
-   Responsive tables
-   Status badges
-   Success, danger, secondary, and standard buttons
-   Flash messages
-   Empty-state displays
-   Back-navigation buttons

Status styling distinguishes states such as:

``` text
Borrowed
Returned
Waiting
Ready
Completed
Lost
Cancelled
Expired
Active
Inactive
```

------------------------------------------------------------------------

## 18. Important Business Rules

The final application should maintain these rules:

1.  Only Active students can borrow books.
2.  Each student has an individual borrow limit.
3.  A student cannot exceed that borrow limit.
4.  A student cannot simultaneously borrow the same book twice.
5.  A book can only be issued when an Available physical copy exists.
6.  Each physical copy has its own copy code.
7.  Borrowed copies cannot be treated as Available.
8.  Returning a book updates both the transaction and physical-copy
    status.
9.  Lost books must not become available automatically.
10. Books with active borrowing/reservation constraints should not be
    deleted incorrectly.
11. Reservation states must be updated when availability changes.
12. Only authorized roles should access protected pages.
13. Due dates use the configured borrowing period.
14. Fines are calculated using the configured fine rule.

------------------------------------------------------------------------

## 19. Troubleshooting

### `no such column: status`

This means the Python code references a column that is missing from the
actual SQLite table being used.

Inspect the table:

``` sql
PRAGMA table_info(reservations);
```

or:

``` sql
PRAGMA table_info(transactions);
```

depending on the failing query.

Do not assume editing `schema.sql` modifies an existing database.

### `no such column: return_date`

Check:

``` sql
PRAGMA table_info(transactions);
```

If an existing database lacks the field, migrate the database
appropriately.

### Book borrowed successfully but not displayed

Check that:

-   The transaction has `status = 'Borrowed'`.
-   The transaction uses the correct `student_id`.
-   The student dashboard queries that same student.
-   `get_borrowed_books(student_id)` returns the new transaction.
-   The Jinja template filters the same status string used in the
    database.

### Reservation remains Waiting when a copy is available

Check:

-   `books.available`
-   `book_copies.status`
-   Reservation status
-   Reservation-update function
-   Whether the reservation-processing function runs after return or
    book availability changes

The aggregate `books.available` value and physical `book_copies`
statuses should remain consistent.

### Login immediately redirects to an old dashboard

A Flask session may still exist in the browser.

If `/login` contains logic that redirects already-authenticated users,
it will send the existing session directly to the appropriate dashboard.

For a login page that should always display when explicitly opened, do
not automatically redirect GET `/login` solely because a session exists.
Logout should call:

``` python
session.clear()
```

------------------------------------------------------------------------

## 20. Testing Checklist

Before final submission/deployment, test:

-   Admin login
-   Student login
-   Invalid login
-   Role mismatch login
-   Logout
-   Add student
-   Edit student
-   Delete student
-   Student search
-   Student status restriction
-   Student borrow-limit restriction
-   Add book
-   Edit book
-   Increase/decrease copy handling
-   Delete book restrictions
-   Book search
-   Physical copy generation
-   QR generation
-   Borrow book
-   Duplicate borrowing prevention
-   Due-date calculation
-   Borrow confirmation
-   QR return
-   Fine calculation
-   Mark as lost
-   Lost-book history
-   Student borrowed-book history
-   Create reservation
-   Cancel reservation
-   Waiting → Ready transition
-   Reservation notification
-   Ready → Completed transition
-   Reservation expiry
-   Transaction report
-   Reservation report
-   CSV export
-   Responsive UI
-   Back buttons and navigation

------------------------------------------------------------------------

## 21. Future Enhancements

Possible future improvements include:

-   Automated task scheduling for email reminders
-   Daily due-date reminder emails
-   Periodic overdue reminders with current fine amount
-   Email delivery logging
-   Password-reset workflow
-   Pagination for large tables
-   Advanced report filtering by date
-   Dashboard charts
-   Book-cover images
-   Barcode scanner support
-   Audit logs
-   Database migrations
-   PostgreSQL/MySQL deployment
-   REST API
-   Cloud deployment

------------------------------------------------------------------------

## 22. Deployment Notes

Before deployment:

-   Disable Flask debug mode.
-   Replace development secrets.
-   Use environment variables for credentials.
-   Do not publish the Gmail App Password.
-   Use a strong `SECRET_KEY`.
-   Back up the production database.
-   Protect student personal information.
-   Configure generated/upload directories correctly.
-   Verify all dependencies are in `requirements.txt`.
-   Verify database schema and migrations.
-   Test email delivery on the deployment environment.

Do not deploy using Flask's built-in development server for a production
system.

------------------------------------------------------------------------

## 23. Project Purpose

This project demonstrates practical implementation of:

-   Python programming
-   Flask web development
-   SQLite database management
-   CRUD operations
-   Authentication and sessions
-   Role-based access
-   Relational database design
-   Business-rule validation
-   QR-code integration
-   SMTP email integration
-   Reservation management
-   Fine calculation
-   Reporting with Pandas
-   CSV generation
-   Responsive frontend development

------------------------------------------------------------------------

## 24. Conclusion

The **Student Library Management System** provides an integrated
solution for managing students, books, physical copies, borrowing,
returns, lost books, reservations, notifications, fines, and
administrative reports.

The separation between book titles and physical copies allows individual
copies to be tracked accurately, while transaction and reservation
records preserve the history of library activity. Role-based dashboards
provide appropriate functionality to administrators and students.

Before publishing or deploying the project, review configuration
secrets, database contents, email credentials, generated files, and
environment-specific settings.
