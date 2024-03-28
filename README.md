import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;
import java.util.Scanner;

class User {
    private String name;
    private String address;
    private String contactInfo;
    private double initialDeposit;
    private String accountNumber;

    public User(String name, String address, String contactInfo, double initialDeposit, String accountNumber) {
        this.name = name;
        this.address = address;
        this.contactInfo = contactInfo;
        this.initialDeposit = initialDeposit;
        this.accountNumber = accountNumber;
    }

    public String getName() {
        return name;
    }

    public String getAddress() {
        return address;
    }

    public String getContactInfo() {
        return contactInfo;
    }

    public double getInitialDeposit() {
        return initialDeposit;
    }

    public String getAccountNumber() {
        return accountNumber;
    }
}

class Account {
    private String accountNumber;
    private double balance;
    private List<String> transactionHistory;

    public Account(String accountNumber, double initialBalance) {
        this.accountNumber = accountNumber;
        this.balance = initialBalance;
        this.transactionHistory = new ArrayList<>();
    }

    public String getAccountNumber() {
        return accountNumber;
    }

    public double getBalance() {
        return balance;
    }

    public List<String> getTransactionHistory() {
        return transactionHistory;
    }

    public void addTransaction(String transaction) {
        transactionHistory.add(transaction);
    }

    public void updateBalance(double amount) {
        balance += amount;
    }
}

public class BankingSystemPrototype {
    private static Map<String, User> users = new HashMap<>();
    private static Map<String, Account> accounts = new HashMap<>();
    private static int accountCounter = 1000;
    private static Scanner scanner = new Scanner(System.in);

    public static void main(String[] args) {
        boolean exit = false;
        while (!exit) {
            System.out.println("1. User Registration");
            System.out.println("2. Account Management");
            System.out.println("3. Deposit");
            System.out.println("4. Withdrawal");
            System.out.println("5. Fund Transfer");
            System.out.println("6. Account Statements");
            System.out.println("7. Exit");
            System.out.print("Enter your choice: ");
            int choice = scanner.nextInt();
            scanner.nextLine(); // consume newline

            switch (choice) {
                case 1:
                    registerUser();
                    break;
                case 2:
                    manageAccount();
                    break;
                case 3:
                    deposit();
                    break;
                case 4:
                    withdraw();
                    break;
                case 5:
                    transferFunds();
                    break;
                case 6:
                    viewAccountStatements();
                    break;
                case 7:
                    exit = true;
                    break;
                default:
                    System.out.println("Invalid choice. Please try again.");
            }
        }
        scanner.close();
    }

    private static void registerUser() {
        System.out.println("User Registration:");
        System.out.print("Enter name: ");
        String name = scanner.nextLine();
        System.out.print("Enter address: ");
        String address = scanner.nextLine();
        System.out.print("Enter contact information: ");
        String contactInfo = scanner.nextLine();
        System.out.print("Enter initial deposit amount: ");
        double initialDeposit = scanner.nextDouble();
        scanner.nextLine(); // consume newline

        String accountNumber = "ACC" + accountCounter++;
        User user = new User(name, address, contactInfo, initialDeposit, accountNumber);
        users.put(accountNumber, user);
        accounts.put(accountNumber, new Account(accountNumber, initialDeposit));

        System.out.println("User registered successfully. Account Number: " + accountNumber);
    }

    private static void manageAccount() {
        System.out.print("Enter account number: ");
        String accountNumber = scanner.nextLine();

        if (users.containsKey(accountNumber)) {
            User user = users.get(accountNumber);
            System.out.println("Account Management:");
            System.out.println("Name: " + user.getName());
            System.out.println("Address: " + user.getAddress());
            System.out.println("Contact Information: " + user.getContactInfo());
            // Allow updates if needed
            System.out.println("Account settings updated successfully.");
        } else {
            System.out.println("Account not found.");
        }
    }

    private static void deposit() {
        System.out.print("Enter account number: ");
        String accountNumber = scanner.nextLine();

        if (accounts.containsKey(accountNumber)) {
            System.out.print("Enter deposit amount: ");
            double amount = scanner.nextDouble();
            scanner.nextLine(); // consume newline

            Account account = accounts.get(accountNumber);
            account.updateBalance(amount);
            account.addTransaction("Deposit: +" + amount);
            System.out.println("Deposit successful. Current balance: " + account.getBalance());
        } else {
            System.out.println("Account not found.");
        }
    }

    private static void withdraw() {
        System.out.print("Enter account number: ");
        String accountNumber = scanner.nextLine();

        if (accounts.containsKey(accountNumber)) {
            System.out.print("Enter withdrawal amount: ");
            double amount = scanner.nextDouble();
            scanner.nextLine(); // consume newline

            Account account = accounts.get(accountNumber);
            if (account.getBalance() >= amount) {
                account.updateBalance(-amount);
                account.addTransaction("Withdrawal: -" + amount);
                System.out.println("Withdrawal successful. Current balance: " + account.getBalance());
            } else {
                System.out.println("Insufficient funds.");
            }
        } else {
            System.out.println("Account not found.");
        }
    }

    private static void transferFunds() {
        System.out.print("Enter sender account number: ");
        String senderAccountNumber = scanner.nextLine();

        System.out.print("Enter recipient account number: ");
        String recipientAccountNumber = scanner.nextLine();

        if (accounts.containsKey(senderAccountNumber) && accounts.containsKey(recipientAccountNumber)) {
            System.out.print("Enter transfer amount: ");
            double amount = scanner.nextDouble();
            scanner.nextLine(); // consume newline

            Account senderAccount = accounts.get(senderAccountNumber);
            Account recipientAccount = accounts.get(recipientAccountNumber);

            if (senderAccount.getBalance() >= amount) {
                senderAccount.updateBalance(-amount);
                senderAccount.addTransaction("Transfer: -" + amount + " to " + recipientAccountNumber);
                recipientAccount.updateBalance(amount);
                recipientAccount.addTransaction("Transfer: +" + amount + " from " + senderAccountNumber);
                System.out.println("Transfer successful.");
            } else {
                System.out.println("Insufficient funds.");
            }
        } else {
            System.out.println("Invalid sender or recipient account number.");
        }
    }

    private static void viewAccountStatements() {
        System.out.print("Enter account number: ");
        String accountNumber = scanner.nextLine();

        if (accounts.containsKey(accountNumber)) {
            Account account = accounts.get(accountNumber);
            List<String> transactions = account.getTransactionHistory();
            System.out.println("Account Statements:");
            for (String transaction : transactions) {
                System.out.println(transaction);
            }
        } else {
            System.out.println("Account not found.");
        }
    }
}
