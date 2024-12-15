# library-management-system

from datetime import datetime

class User:
    def __init__(self, user_id, name, email):
        self.user_id = user_id
        self.name = name
        self.email = email
        self.borrowed_books = []

    def borrow_book(self, book):
        if book.is_available():
            self.borrowed_books.append(book)
            book.borrow(self)
        else:
            raise Exception(f"The book '{book.title}' is currently not available.")

    def return_book(self, book):
        if book in self.borrowed_books:
            self.borrowed_books.remove(book)
            book.return_book()
        else:
            raise Exception(f"The book '{book.title}' is not borrowed by {self.name}.")


class Book:
    def __init__(self, book_id, title, author, total_copies):
        self.book_id = book_id
        self.title = title
        self.author = author
        self.total_copies = total_copies
        self.borrowed_copies = 0

    def is_available(self):
        return self.total_copies > self.borrowed_copies

    def borrow(self, user):
        if self.is_available():
            self.borrowed_copies += 1
        else:
            raise Exception(f"The book '{self.title}' is currently unavailable.")

    def return_book(self):
        if self.borrowed_copies > 0:
            self.borrowed_copies -= 1
        else:
            raise Exception(f"All copies of '{self.title}' are already in the library.")


class LibraryManagementSystem:
    def __init__(self):
        self.users = {}
        self.books = {}

    # User management
    def add_user(self, user_id, name, email):
        if user_id not in self.users:
            self.users[user_id] = User(user_id, name, email)
        else:
            raise Exception(f"User with ID {user_id} already exists.")
    def remove_user(self, user_id):
        if user_id in self.users:
            del self.users[user_id]
        else:
            raise Exception(f"User with ID {user_id} does not exist.")

    def get_user(self, user_id):
        return self.users.get(user_id, None)

    # Book management
    def add_book(self, book_id, title, author, total_copies):
        if book_id not in self.books:
            self.books[book_id] = Book(book_id, title, author, total_copies)
        else:
            raise Exception(f"Book with ID {book_id} already exists.")

    def remove_book(self, book_id):
        if book_id in self.books:
            del self.books[book_id]
        else:
            raise Exception(f"Book with ID {book_id} does not exist.")

    def get_book(self, book_id):
        return self.books.get(book_id, None)

     # Borrowing/returning process
    def borrow_book(self, user_id, book_id):
        user = self.get_user(user_id)
        book = self.get_book(book_id)
        if user and book:
            user.borrow_book(book)
        else:
            raise Exception("Invalid user ID or book ID.")

    def return_book(self, user_id, book_id):
        user = self.get_user(user_id)
        book = self.get_book(book_id)
        if user and book:
            user.return_book(book)
        else:
            raise Exception("Invalid user ID or book ID.")

# Example usage
if __name__ == "__main__":
    lms = LibraryManagementSystem()

    # Add users
    lms.add_user(1, "Alice", "alice@example.com")
    lms.add_user(2, "Bob", "bob@example.com")

    # Add books
    lms.add_book(101, "1984", "George Orwell", 3)
    lms.add_book(102, "To Kill a Mockingbird", "Harper Lee", 2)

    # Borrow books
    lms.borrow_book(1, 101)
    lms.borrow_book(2, 101)

    # Return books
    lms.return_book(1, 101)

    # Check available copies
    book_1984 = lms.get_book(101)
    print(f"'{book_1984.title}' available copies: {book_1984.total_copies - book_1984.borrowed_copies}")

        
