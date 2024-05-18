from datetime import datetime

class Item:
    def __init__(self, title, author, price, stock=0):
        self.title = title
        self.author = author
        self.price = price
        self.stock = stock

    def get_details(self):
        return f"Title: {self.title}, Author: {self.author}, Price: ${self.price}, Stock: {self.stock}"

    def update_details(self, **kwargs):
        for key, value in kwargs.items():
            setattr(self, key, value)

    def apply_discount(self, discount_percentage):
        self.price -= (self.price * (discount_percentage / 100))


class Book(Item):
    def __init__(self, title, author, price, isbn, genre, num_pages, stock=0):
        super().__init__(title, author, price, stock)
        self.isbn = isbn
        self.genre = genre
        self.num_pages = num_pages


class Magazine(Item):
    def __init__(self, title, author, price, issue_number, publication_date, editor, stock=0):
        super().__init__(title, author, price, stock)
        self.issue_number = issue_number
        self.publication_date = publication_date
        self.editor = editor


class DVD(Item):
    def __init__(self, title, author, price, director, duration, genre, stock=0):
        super().__init__(title, author, price, stock)
        self.director = director
        self.duration = duration
        self.genre = genre



class Customer:
    def __init__(self, name, email, username, password):
        self.name = name
        self.email = email
        self.username = username
        self.password = password
        self.cart = []
        self.order_history = []

    def add_to_cart(self, item, quantity=1):
        self.cart.append((item, quantity))

    def remove_from_cart(self, item):
        self.cart = [(i, q) for i, q in self.cart if i != item]

    def view_cart(self):
        total_price = 0
        if not self.cart:
            print("Your cart is empty.")
        else:
            print("Your cart:")
            for item, quantity in self.cart:
                total_item_price = item.price * quantity
                total_price += total_item_price
                print(f"{item.title} - Quantity: {quantity}, Price per item: ${item.price}, Total price: ${total_item_price}")
            print(f"Total Price for all items: ${total_price}")

    def checkout(self):
        total_price = sum(item.price * quantity for item, quantity in self.cart)
        print(f"Total price for your items: ${total_price}")
        confirm = input("Do you want to proceed with the checkout? (yes/no): ")
        if confirm.lower() == "yes":
            print("Thank you for your purchase!")
            self.order_history.append((datetime.now(), self.cart))
            self.cart = []
        else:
            print("Checkout canceled.")

    def add_item_to_cart(self, bookstore):
        print("Available Items in Inventory:")
        bookstore.view_inventory()
        item_title = input("Enter the title of the item you want to add to cart: ")
        quantity = int(input("Enter the quantity: "))
        for item in bookstore.inventory:
            if item.title.lower() == item_title.lower():
                if item.stock >= quantity:
                    self.add_to_cart(item, quantity)
                    item.stock -= quantity
                    print(f"{quantity} {item.title}(s) added to your cart.")
                else:
                    print("Insufficient stock.")
                return
        print("Item not found in inventory.")
     
    def remove_item_from_cart(self, title):
        for i, (item, quantity) in enumerate(self.cart):
            if item.title.lower() == title.lower():
                del self.cart[i]
                print(f"{item.title} removed from your cart.")
                return
        print("Item not found in your cart.")
        
    def view_order_history(self):
        if not self.order_history:
            print("You have no previous orders.")
        else:
            print("Previous Orders:")
            for order_time, order_items in self.order_history:
                print(f"Order placed on {order_time}:")
                for item, quantity in order_items:
                    print(f"{item.title} - Quantity: {quantity}")

    def calculate_order_total(self):
        return sum(item.price * quantity for item, quantity in self.cart)

class Staff:
    def __init__(self, name, email, role):
        self.name = name
        self.email = email
        self.role = role
class Bookstore:
    def __init__(self):
        self.inventory = []
        self.customers = []
        self.staff = [{"username": "OmarSaber", "password": "omar.saber", "role": "admin"}]  

    def login_staff(self):
        username = input("Enter username: ")
        password = input("Enter password: ")
        for staff_member in self.staff:
            if staff_member["username"] == username and staff_member["password"] == password:
                return staff_member
        print("Invalid username or password.")
        return None

    def add_item(self, item, staff):
        if staff["role"] == "admin":
            self.inventory.append(item)
            print(f"{item.title} added to inventory.")
        else:
            print("You don't have permission to perform this action.")

    def remove_item(self, title, staff):
        if staff["role"] == "admin":
            for item in self.inventory:
                if item.title.lower() == title.lower():
                    self.inventory.remove(item)
                    print(f"{item.title} removed from inventory.")
                    break
            else:
                print("Item not found in inventory.")
        else:
            print("You don't have permission to perform this action.")

    def view_inventory(self, staff=None):
        if staff is None or staff["role"] != "admin":
            print("Available options for customers:")
            print("5. Search Items by Title")
            print("6. Search Items by Author")
            print("7. Search Items by Genre")
            print("8. View Cart")
            print("9. Checkout")
        else:
            print("Inventory:")
            for item in self.inventory:
                print(item.get_details())

    def search_items_by_title(self, title):
        matching_items = [item for item in self.inventory if title.lower() in item.title.lower()]
        if matching_items:
            print("Matching Items:")
            for item in matching_items:
                print(item.get_details())
        else:
            print("No items found with that title.")

    def search_items_by_author(self, author):
        matching_items = [item for item in self.inventory if author.lower() in item.author.lower()]
        if matching_items:
            print("Matching Items:")
            for item in matching_items:
                print(item.get_details())
        else:
            print("No items found by that author.")

    def search_items_by_genre(self, genre):
        matching_items = [item for item in self.inventory if genre.lower() in item.genre.lower()]
        if matching_items:
            print("Matching Items:")
            for item in matching_items:
                print(item.get_details())
        else:
            print("No items found in that genre.")

    def add_customer(self, name, email, username, password):
        customer = Customer(name, email, username, password)
        self.customers.append(customer)
        print(f"Customer {name} added.")

    def view_customers(self):
        print("Customers:")
        for customer in self.customers:
            print(customer.name)

    def login_customer(self):
        username = input("Enter username: ")
        password = input("Enter password: ")
        for customer in self.customers:
            if customer.username == username and customer.password == password:
                return customer
        print("Invalid username or password.")
        return None


 
bookstore = Bookstore()

while True:
    print("\nAre you a staff member? (yes/no)")
    is_staff = input().lower()
    if is_staff == "yes":
        user = bookstore.login_staff()
        if user is None:
            continue
    elif is_staff == "no":
        user = None
    else:
        print("Invalid input.")
        continue

    if user is not None and user["role"] == "admin":
        print("\n1. Add Book")
        print("2. Add Magazine")
        print("3. Add DVD")
        print("4. View Inventory")
        print("10. Add Customer")
        print("11. View Customers")
        choice = input("Enter your choice: ")
        if choice == "1" or choice == "2" or choice == "3":
            if user is not None and user["role"] == "admin":
                if choice == "1":
                    title = input("Enter book title: ")
                    author = input("Enter author name: ")
                    price = float(input("Enter price: "))
                    isbn = input("Enter ISBN: ")
                    genre = input("Enter genre: ")
                    num_pages = int(input("Enter number of pages: "))
                    stock = int(input("Enter stock: "))
                    book = Book(title, author, price, isbn, genre, num_pages, stock)
                    bookstore.add_item(book, user)
                elif choice == "2":
                    title = input("Enter magazine title: ")
                    author = input("Enter author name: ")
                    price = float(input("Enter price: "))
                    issue_number = int(input("Enter issue number: "))
                    publication_date = input("Enter publication date: ")
                    editor = input("Enter editor: ")
                    stock = int(input("Enter stock: "))
                    magazine = Magazine(title, author, price, issue_number, publication_date, editor, stock)
                    bookstore.add_item(magazine, user)
                else:
                    title = input("Enter DVD title: ")
                    author = input("Enter author name: ")
                    price = float(input("Enter price: "))
                    director = input("Enter director: ")
                    duration = input("Enter duration: ")
                    genre = input("Enter genre: ")
                    stock = int(input("Enter stock: "))
                    dvd = DVD(title, author, price, director, duration, genre, stock)
                    bookstore.add_item(dvd, user)
            else:
                print("You don't have permission to perform this action.")
        elif choice == "4":
            bookstore.view_inventory(user)
        elif choice == "10":
            name = input("Enter customer name: ")
            email = input("Enter customer email: ")
            username = input("Enter customer username: ")
            password = input("Enter customer password: ")
            bookstore.add_customer(name, email, username, password)
        elif choice == "11":
            bookstore.view_customers()
        else:
            print("Invalid choice.")
    else:
        print("\n1. Login as Customer")

        choice = input("Enter your choice: ")
        if choice == "1":
            customer = bookstore.login_customer()
            if customer:
                while True:
                    print("\n1. Search Items by Title")
                    print("2. Search Items by Author")
                    print("3. Search Items by Genre")
                    print("4. View Cart")
                    print("5. Add Item to Cart")
                    print("6. Checkout")
                    print("7. Logout")
                    print("8. View Order History")
                    print("9. Remove from cart")
                    customer_choice = input("Enter your choice: ")
                    if customer_choice == "1":
                        title = input("Enter the title to search: ")
                        bookstore.search_items_by_title(title)
                    elif customer_choice == "2":
                        author = input("Enter the author to search: ")
                        bookstore.search_items_by_author(author)
                    elif customer_choice == "3":
                        genre = input("Enter the genre to search: ")
                        bookstore.search_items_by_genre(genre)
                    elif customer_choice == "4":
                        customer.view_cart()
                    elif customer_choice == "5":
                        customer.add_item_to_cart(bookstore)
                    elif customer_choice == "6":
                        customer.checkout()
                    elif customer_choice == "7":
                        print("Logging out.")
                        break
                    elif customer_choice == "8":
                        customer.view_order_history()  
                    elif customer_choice == "9":  
                         title_to_remove = input("Enter the title of the item to remove from your cart: ")
                         customer.remove_item_from_cart(title_to_remove)
                    else:
                        print("Invalid choice.")
        
        else:
            print("Invalid choice.")
        
