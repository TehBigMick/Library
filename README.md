# Library
Library management system that pulls data from ISBN's and stores under user accounts

We had an issue in the school I teach where student's would take books home, as they were encouraged to do so, but nobody cared to track which books were going where. This simple system was a test of my abilities to solve the issue with available tools.
The school has a barcode scanner so the application only has the ISBN ability.

User enters the Student ID, (Could also be via barcode, though our student's do not currently have that), then scans the ISBN on the book, then clicks the "Check-out" or "Return" button depending on what the situation calls for.

There is also a check student ID info button which will bring up any current outstanding books the student has yet to return, we check this before issuing any new books.

Finally there is a button to check all users and all books assigned to them, which we can use to send reminder notices to parents to return the books as soon as possible. This function is password protected, though it is just a plain text stored password.
