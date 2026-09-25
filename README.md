import random
from datetime import datetime

# Dictionary to store all bank accounts
accounts = {}


# ---------------- CREATE ACCOUNT ----------------
def create_account():
    print("\n========== CREATE ACCOUNT ==========")

    name = input("Enter your name: ")
    phone = input("Enter your phone number: ")

    while True:
        pin = input("Create a 4-digit PIN: ")

        if pin.isdigit() and len(pin) == 4:
            confirm_pin = input("Confirm your PIN: ")

            if pin == confirm_pin:
                break
            else:
                print("PINs do not match. Try again.")
        else:
            print("PIN must contain exactly 4 digits.")

    # Generate a unique 6-digit account number
    while True:
        account_number = str(random.randint(100000, 999999))

        if account_number not in accounts:
            break

    accounts[account_number] = {
        "name": name,
        "phone": phone,
        "pin": pin,
        "balance": 0.0,
        "transactions": []
    }

    print("\nAccount created successfully!")
    print("Your Account Number:", account_number)
    print("Please remember your account number and PIN.")


# ---------------- LOGIN ----------------
def login():
    print("\n========== LOGIN ==========")

    account_number = input("Enter Account Number: ")
    pin = input("Enter PIN: ")

    if account_number in accounts:
        if accounts[account_number]["pin"] == pin:
            print("\nLogin successful!")
            print("Welcome,", accounts[account_number]["name"])
            account_menu(account_number)
        else:
            print("Incorrect PIN.")
    else:
        print("Account not found.")


# ---------------- CHECK BALANCE ----------------
def check_balance(account_number):
    balance = accounts[account_number]["balance"]

    print("\n========== ACCOUNT BALANCE ==========")
    print(f"Current Balance: ₹{balance:.2f}")


# ---------------- DEPOSIT ----------------
def deposit(account_number):
    print("\n========== DEPOSIT MONEY ==========")

    try:
        amount = float(input("Enter amount to deposit: ₹"))

        if amount <= 0:
            print("Amount must be greater than zero.")
            return

        accounts[account_number]["balance"] += amount

        time = datetime.now().strftime("%d-%m-%Y %H:%M:%S")

        transaction = (
            f"{time} | Deposit | +₹{amount:.2f} | "
            f"Balance: ₹{accounts[account_number]['balance']:.2f}"
        )

        accounts[account_number]["transactions"].append(transaction)

        print(f"₹{amount:.2f} deposited successfully.")
        print(f"New Balance: ₹{accounts[account_number]['balance']:.2f}")

    except ValueError:
        print("Please enter a valid amount.")


# ---------------- WITHDRAW ----------------
def withdraw(account_number):
    print("\n========== WITHDRAW MONEY ==========")

    try:
        amount = float(input("Enter amount to withdraw: ₹"))

        if amount <= 0:
            print("Amount must be greater than zero.")
            return

        balance = accounts[account_number]["balance"]

        if amount > balance:
            print("Insufficient balance.")
            return

        accounts[account_number]["balance"] -= amount

        time = datetime.now().strftime("%d-%m-%Y %H:%M:%S")

        transaction = (
            f"{time} | Withdrawal | -₹{amount:.2f} | "
            f"Balance: ₹{accounts[account_number]['balance']:.2f}"
        )

        accounts[account_number]["transactions"].append(transaction)

        print(f"₹{amount:.2f} withdrawn successfully.")
        print(f"New Balance: ₹{accounts[account_number]['balance']:.2f}")

    except ValueError:
        print("Please enter a valid amount.")


# ---------------- TRANSFER ----------------
def transfer(account_number):
    print("\n========== TRANSFER MONEY ==========")

    receiver = input("Enter receiver's Account Number: ")

    if receiver not in accounts:
        print("Receiver account not found.")
        return

    if receiver == account_number:
        print("You cannot transfer money to your own account.")
        return

    try:
        amount = float(input("Enter amount to transfer: ₹"))

        if amount <= 0:
            print("Amount must be greater than zero.")
            return

        if amount > accounts[account_number]["balance"]:
            print("Insufficient balance.")
            return

        # Deduct from sender
        accounts[account_number]["balance"] -= amount

        # Add to receiver
        accounts[receiver]["balance"] += amount

        time = datetime.now().strftime("%d-%m-%Y %H:%M:%S")

        sender_transaction = (
            f"{time} | Transfer to {receiver} | -₹{amount:.2f} | "
            f"Balance: ₹{accounts[account_number]['balance']:.2f}"
        )

        receiver_transaction = (
            f"{time} | Transfer from {account_number} | +₹{amount:.2f} | "
            f"Balance: ₹{accounts[receiver]['balance']:.2f}"
        )

        accounts[account_number]["transactions"].append(
            sender_transaction
        )

        accounts[receiver]["transactions"].append(
            receiver_transaction
        )

        print(f"₹{amount:.2f} transferred successfully.")
        print(f"New Balance: ₹{accounts[account_number]['balance']:.2f}")

    except ValueError:
        print("Please enter a valid amount.")


# ---------------- TRANSACTION HISTORY ----------------
def transaction_history(account_number):
    print("\n========== TRANSACTION HISTORY ==========")

    transactions = accounts[account_number]["transactions"]

    if not transactions:
        print("No transactions found.")
    else:
        for i, transaction in enumerate(transactions, start=1):
            print(f"{i}. {transaction}")


# ---------------- CHANGE PIN ----------------
def change_pin(account_number):
    print("\n========== CHANGE PIN ==========")

    old_pin = input("Enter your old PIN: ")

    if old_pin != accounts[account_number]["pin"]:
        print("Incorrect old PIN.")
        return

    while True:
        new_pin = input("Enter new 4-digit PIN: ")

        if not new_pin.isdigit() or len(new_pin) != 4:
            print("PIN must contain exactly 4 digits.")
            continue

        confirm_pin = input("Confirm new PIN: ")

        if new_pin != confirm_pin:
            print("PINs do not match.")
            continue

        if new_pin == old_pin:
            print("New PIN must be different from old PIN.")
            continue

        break

    accounts[account_number]["pin"] = new_pin

    print("PIN changed successfully.")


# ---------------- ACCOUNT MENU ----------------
def account_menu(account_number):

    while True:
        print("\n===================================")
        print("          ACCOUNT MENU")
        print("===================================")
        print("1. Check Balance")
        print("2. Deposit Money")
        print("3. Withdraw Money")
        print("4. Transfer Money")
        print("5. Transaction History")
        print("6. Change PIN")
        print("7. Logout")
        print("===================================")

        choice = input("Enter your choice: ")

        if choice == "1":
            check_balance(account_number)

        elif choice == "2":
            deposit(account_number)

        elif choice == "3":
            withdraw(account_number)

        elif choice == "4":
            transfer(account_number)

        elif choice == "5":
            transaction_history(account_number)

        elif choice == "6":
            change_pin(account_number)

        elif choice == "7":
            print("\nLogged out successfully.")
            print("Returning to main menu...")
            break

        else:
            print("Invalid choice. Please try again.")


# ---------------- MAIN MENU ----------------
def main():

    while True:
        print("\n")
        print("==========================================")
        print("          PYTHON BANKING SYSTEM")
        print("==========================================")
        print("1. Create Account")
        print("2. Login")
        print("3. Exit")
        print("==========================================")

        choice = input("Enter your choice: ")

        if choice == "1":
            create_account()

        elif choice == "2":
            login()

        elif choice == "3":
            print("\nThank you for using Python Banking System!")
            break

        else:
            print("Invalid choice. Please try again.")


# Start the program
if __name__ == "__main__":
    main()


