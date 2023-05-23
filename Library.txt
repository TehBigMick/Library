import tkinter as tk
import json
from tkinter import messagebox
import isbnlib
from isbnlib import meta


def check_user_info(username):
    with open('user_accounts.json', 'r') as file:
        for line in file:
            user_account = json.loads(line)
            if user_account['username'] == username:
                return user_account
    return None


def check_assigned_books(username):
    user_account = check_user_info(username)
    if user_account:
        assigned_books = user_account.get('assigned_books', [])
        if assigned_books:
            assigned_book_info = "Books assigned to user " + username + ":\n\n"
            for book in assigned_books:
                assigned_book_info += f"ISBN: {book.get('isbn', 'N/A')}\n"
                assigned_book_info += f"Title: {book.get('title', 'N/A')}\n\n"
            messagebox.showinfo("Assigned Books", assigned_book_info)
        else:
            messagebox.showinfo("Assigned Books", f"No books assigned to user {username}.")
    else:
        messagebox.showinfo("Error", "User not found.")


def assign_book(username, isbn):
    user_account = check_user_info(username)
    if user_account:
        assigned_books = user_account.get('assigned_books', [])
        assigned_book = {'isbn': isbn, 'title': get_book_name(isbn)}
        assigned_books.append(assigned_book)
        user_account['assigned_books'] = assigned_books
        with open('user_accounts.json', 'r+') as file:
            accounts = [json.loads(line) for line in file]
            updated_accounts = [user_account if acc['username'] == username else acc for acc in accounts]
            file.seek(0)
            for account in updated_accounts:
                file.write(json.dumps(account) + '\n')
            file.truncate()
        messagebox.showinfo("Book Assignment", f"Assigned book with ISBN {isbn} to user {username}.")
    else:
        messagebox.showinfo("Error", "User not found.")


def remove_book(username, isbn):
    user_account = check_user_info(username)
    if user_account:
        assigned_books = user_account.get('assigned_books', [])
        removed_books = [book for book in assigned_books if book['isbn'] == isbn]
        if removed_books:
            assigned_books = [book for book in assigned_books if book['isbn'] != isbn]
            user_account['assigned_books'] = assigned_books
            with open('user_accounts.json', 'r+') as file:
                accounts = [json.loads(line) for line in file]
                updated_accounts = [user_account if acc['username'] == username else acc for acc in accounts]
                file.seek(0)
                for account in updated_accounts:
                    file.write(json.dumps(account) + '\n')
                file.truncate()
            messagebox.showinfo("Book Return", f"Removed book with ISBN {isbn} from user {username}.")
        else:
            messagebox.showinfo("Error", f"No book with ISBN {isbn} found for user {username}.")
    else:
        messagebox.showinfo("Error", "User not found.")


def create_user_account():
    username = username_entry.get()
    user_account = {'username': username, 'assigned_books': []}
    with open('user_accounts.json', 'a') as file:
        json.dump(user_account, file)
        file.write('\n')
    messagebox.showinfo("User Account", "User account created successfully.")


def get_book_name(isbn):
    book = meta(isbn, service='goob')
    if book:
        return book.get('Title')
    else:
        return "No book information found for the given ISBN."


def retrieve_book_info():
    isbn = entry.get()
    book_name = get_book_name(isbn)
    messagebox.showinfo("Book Information", f"Book Name: {book_name}")


def show_all_accounts(password):
    if password == 'admin':
        with open('user_accounts.json', 'r') as file:
            user_accounts = file.readlines()
            accounts_info = "List of User Accounts:\n\n"
            for line in user_accounts:
                line = line.strip()  # Remove the trailing newline character
                if line:
                    user_account = json.loads(line)
                    username = user_account['username']
                    assigned_books = user_account.get('assigned_books', [])
                    accounts_info += f"Username: {username}\n"
                    if assigned_books:
                        accounts_info += "Assigned Books:\n"
                        for book in assigned_books:
                            accounts_info += f"  - ISBN: {book.get('isbn', 'Unknown')}\n"
                            accounts_info += f"    Title: {book.get('title', 'Unknown')}\n"
                    else:
                        accounts_info += "No books assigned.\n"
                    accounts_info += "\n"
            messagebox.showinfo("User Accounts", accounts_info)
    else:
        messagebox.showinfo("Error", "Invalid password.")


root = tk.Tk()
root.title("Library Management System")
root.geometry("500x500")

frame = tk.Frame(root)
frame.pack(pady=20)

check_account_button = tk.Button(frame, text="Check Account Info",
                                 command=lambda: check_assigned_books(username_entry.get()))
check_account_button.pack(pady=5)

assign_book_button = tk.Button(frame, text="Assign a Book",
                               command=lambda: assign_book(username_entry.get(), entry.get()))
assign_book_button.pack(pady=5)

return_book_button = tk.Button(frame, text="Return a Book",
                               command=lambda: remove_book(username_entry.get(), entry.get()))
return_book_button.pack(pady=5)

create_user_button = tk.Button(root, text="Create New User", command=create_user_account)
create_user_button.pack(pady=5)

username_label = tk.Label(root, text="Enter username:")
username_label.pack()

username_entry = tk.Entry(root)
username_entry.pack()

isbn_label = tk.Label(root, text="Enter ISBN:")
isbn_label.pack()

entry = tk.Entry(root)
entry.pack()

retrieve_info_button = tk.Button(root, text="Retrieve Book Info", command=retrieve_book_info)
retrieve_info_button.pack(pady=10)

show_accounts_button = tk.Button(root, text="Show All User Accounts",
                                 command=lambda: show_all_accounts(password_entry.get()))
show_accounts_button.pack(pady=5)

password_label = tk.Label(root, text="Enter password:")
password_label.pack()

password_entry = tk.Entry(root, show="*")
password_entry.pack()

root.mainloop()
