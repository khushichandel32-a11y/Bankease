import os
import json
from datetime import datetime

DATA_FILE = "bank_data.json"


def load_data():
    if not os.path.exists(DATA_FILE):
        return {}
    with open(DATA_FILE, "r") as f:
        return json.load(f)


def save_data(data):
    with open(DATA_FILE, "w") as f:
        json.dump(data, f, indent=4)


def create_account():
    data = load_data()
    print("----- Create New Account -----")
    account_number = input("Enter new account number: ")
    if account_number in data:
        print("Account already exists. Try again.")
        return
    name = input("Enter account holder name: ")
    data[account_number] = {"name": name, "balance": 0.0, "transactions": []}
    save_data(data)
    print(
        f"Account created successfully for {name} with Account Number {account_number}."
    )


def deposit():
    data = load_data()
    print("----- Deposit Money -----")
    account_number = input("Enter account number: ")
    if account_number not in data:
        print("Account does not exist.")
        return
    amount = float(input("Enter amount to deposit: "))
    if amount <= 0:
        print("Invalid amount.")
        return
    data[account_number]["balance"] += amount
    data[account_number]["transactions"].append(
        {
            "type": "Deposit",
            "amount": amount,
            "date": datetime.now().strftime("%Y-%m-%d %H:%M:%S"),
        }
    )
    save_data(data)
    print(
        f"Deposited {amount} successfully. New balance: {data[account_number]['balance']}"
    )


def withdraw():
    data = load_data()
    print("----- Withdraw Money -----")
    account_number = input("Enter account number: ")
    if account_number not in data:
        print("Account does not exist.")
        return
    amount = float(input("Enter amount to withdraw: "))
    if amount <= 0:
        print("Invalid amount.")
        return
    if amount > data[account_number]["balance"]:
        print("Insufficient balance.")
        return
    data[account_number]["balance"] -= amount
    data[account_number]["transactions"].append(
        {
            "type": "Withdrawal",
            "amount": amount,
            "date": datetime.now().strftime("%Y-%m-%d %H:%M:%S"),
        }
    )
    save_data(data)
    print(
        f"Withdrawal of {amount} successful. New balance: {data[account_number]['balance']}"
    )


def balance_enquiry():
    data = load_data()
    print("----- Balance Enquiry -----")
    account_number = input("Enter account number: ")
    if account_number not in data:
        print("Account does not exist.")
        return
    print(f"Account Holder: {data[account_number]['name']}")
    print(f"Current Balance: {data[account_number]['balance']}")


def transaction_history():
    data = load_data()
    print("----- Transaction History -----")
    account_number = input("Enter account number: ")
    if account_number not in data:
        print("Account does not exist.")
        return
    transactions = data[account_number]["transactions"]
    if not transactions:
        print("No transactions found.")
        return
    for txn in transactions:
        print(f"{txn['date']} - {txn['type']}: {txn['amount']}")


def main():
    while True:
        print("\n=== BankEase ===")
        print("1. Create Account")
        print("2. Deposit")
        print("3. Withdraw")
        print("4. Balance Enquiry")
        print("5. Transaction History")
        print("6. Exit")
        choice = input("Enter your choice [1-6]: ")

        if choice == "1":
            create_account()
        elif choice == "2":
            deposit()
        elif choice == "3":
            withdraw()
        elif choice == "4":
            balance_enquiry()
        elif choice == "5":
            transaction_history()
        elif choice == "6":
            print("Thank you for using BankEase.")
            break
        else:
            print("Invalid choice. Please select a valid option.")


if __name__ == "__main__":
    main()
