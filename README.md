# UI Lab 1

### Для початку додам сюди скрнішот роботи програми, яку тільки-но було дістано з репозиторію, і над нею не було проведено ніяких маніпуляцій
![](https://github.com/ppc-ntu-khpi/34-tui-1-katushhiaa/blob/master/first_work.jpg "Works")

## На "чотири"
Далі я виконала завдання, яке передбачає виконання на оцінку "чотири"(код і скріншот додаю)

Перепишіть метод **ShowCustomerDetails** з використанням класів *Bank, Customer, Account* та ін. з наших попередніх лаб - банк повинен мати як мінімум два клієнти, інформацію про яких (та про перші рахунки, що їм належать) ви маєте побачити, увівши номер клієнта.

```java
//package com.mybank.tui;

import com.mybank.domain.Account;
import com.mybank.domain.Bank;
import com.mybank.domain.Customer;
import com.mybank.domain.SavingsAccount;
import jexer.TAction;
import jexer.TApplication;
import jexer.TField;
import jexer.TText;
import jexer.TWindow;
import jexer.event.TMenuEvent;
import jexer.menu.TMenu;

/**
 *
 * @author Alexander 'Taurus' Babich
 */
public class TUIdemo extends TApplication {

    private static final int ABOUT_APP = 2000;
    private static final int CUST_INFO = 2010;

    public static void main(String[] args) throws Exception {
        TUIdemo tdemo = new TUIdemo();
        (new Thread(tdemo)).start();
    }

    public TUIdemo() throws Exception {
        super(BackendType.SWING);

        addToolMenu();
        //custom 'File' menu
        TMenu fileMenu = addMenu("&File");
        fileMenu.addItem(CUST_INFO, "&Customer Info");
        fileMenu.addDefaultItem(TMenu.MID_SHELL);
        fileMenu.addSeparator();
        fileMenu.addDefaultItem(TMenu.MID_EXIT);
        //end of 'File' menu  

        addWindowMenu();

        //custom 'Help' menu
        TMenu helpMenu = addMenu("&Help");
        helpMenu.addItem(ABOUT_APP, "&About...");
        //end of 'Help' menu 

        setFocusFollowsMouse(true);

        Bank.addCustomer("John", "Doe");
        Bank.getCustomer(0).addAccount(new SavingsAccount(1000.0, 0.05));
        Bank.addCustomer("Jane", "Smith");
        Bank.getCustomer(1).addAccount(new SavingsAccount(200.0, 0.04));
        Bank.addCustomer("Peter", "Quill");
        Bank.getCustomer(2).addAccount(new SavingsAccount(1000.0, 0.06));
        Bank.addCustomer("Kate", "Anurev");
        Bank.getCustomer(3).addAccount(new SavingsAccount(700.0, 0.08));
        //Customer window
        ShowCustomerDetails();
    }

    @Override
    protected boolean onMenu(TMenuEvent menu) {
        if (menu.getId() == ABOUT_APP) {
            messageBox("About", "\t\t\t\t\t   Just a simple Jexer demo.\n\nCopyright \u00A9 2023 Katerina \' Babich").show();
            return true;
        }
        if (menu.getId() == CUST_INFO) {
            ShowCustomerDetails();
            return true;
        }
        return super.onMenu(menu);
    }

    private void ShowCustomerDetails() {
        TWindow custWin = addWindow("Customer Window", 2, 1, 40, 10, TWindow.NOZOOMBOX);
        custWin.newStatusBar("Enter valid customer number and press Show...");

        custWin.addLabel("Enter customer number: ", 2, 2);
        TField custNo = custWin.addField(24, 2, 3, false);
        TText details = custWin.addText("", 2, 4, 38, 8);

        custWin.addButton("&Show", 28, 2, new TAction() {
            @Override
            public void DO() {
                try {
                    int custNum = Integer.parseInt(custNo.getText());
                    if (custNum >= 0 && custNum < Bank.getNumberOfCustomers()) {
                        Customer customer = Bank.getCustomer(custNum);

                        String ownerName = customer.getFirstName() + " " + customer.getLastName();
                        String accountType = "";
                        double balance = 0.0;

                        if (customer.getNumberOfAccounts() > 0) {
                            Account account = customer.getAccount(0);
                            accountType = account.getClass().getSimpleName();
                            balance = account.getBalance();
                        }

                        details.setText("Owner Name: " + ownerName + " (id=" + custNum + ")\n"
                                + "Account Type: '" + accountType + "'\n"
                                + "Account Balance: $" + balance);

                    } else {
                        messageBox("Error", "Invalid customer number!").show();
                    }
                } catch (NumberFormatException e) {
                    messageBox("Error", "You must provide a valid customer number!").show();
                }
            }
        });
    }

}
```
**Скріншот виконання програми**

![](https://github.com/ppc-ntu-khpi/34-tui-1-katushhiaa/blob/master/work_for_4.jpg "For mark 4")
## На "п'ять"

Моя цікавість не змусила мене чекати, і я полізла робити завдання на оцінку "п'ять". Проте тут вилізли деякі "але". В принципі програми сама по собі показує дані з файлу test.dat, проте є деяка плутанина з вводом номеру клієнта. Я довго сиділа над цим, навіть не знаю, що з цим можна зробити, проте я рада сама за себе, що хоча б спробувала.

Перепишіть метод **ShowCustomerDetails** з використанням класів *Bank, Customer, Account* та ін., інформація про клієнтів банку та їх рахунках має читатись з файлу **test.dat** Інформацію про клієнта (та про перший рахунок, що йому належить) ви маєте побачити, увівши номер клієнта.

**Код програми**

```java
//package com.mybank.tui;

import com.mybank.domain.Account;
import com.mybank.domain.CheckingAccount;
import com.mybank.domain.Customer;
import com.mybank.domain.SavingsAccount;
import java.io.BufferedReader;
import java.io.FileReader;
import java.io.IOException;
import jexer.TAction;
import jexer.TApplication;
import jexer.TField;
import jexer.TText;
import jexer.TWindow;
import jexer.event.TMenuEvent;
import jexer.menu.TMenu;

/**
 *
 * @author Alexander 'Taurus' Babich
 */
public class TUIdemo extends TApplication {

    private static final int ABOUT_APP = 2000;
    private static final int CUST_INFO = 2010;

    public static void main(String[] args) throws Exception {
        TUIdemo tdemo = new TUIdemo();
        (new Thread(tdemo)).start();
    }

    public TUIdemo() throws Exception {
        super(BackendType.SWING);

        addToolMenu();
        //custom 'File' menu
        TMenu fileMenu = addMenu("&File");
        fileMenu.addItem(CUST_INFO, "&Customer Info");
        fileMenu.addDefaultItem(TMenu.MID_SHELL);
        fileMenu.addSeparator();
        fileMenu.addDefaultItem(TMenu.MID_EXIT);
        //end of 'File' menu  

        addWindowMenu();

        //custom 'Help' menu
        TMenu helpMenu = addMenu("&Help");
        helpMenu.addItem(ABOUT_APP, "&About...");
        //end of 'Help' menu 

        setFocusFollowsMouse(true);

        /*Bank.addCustomer("John", "Doe");
        Bank.getCustomer(0).addAccount(new SavingsAccount(1000.0, 0.05));
        Bank.addCustomer("Jane", "Smith");
        Bank.getCustomer(1).addAccount(new SavingsAccount(200.0, 0.04));
        Bank.addCustomer("Peter", "Quill");
        Bank.getCustomer(2).addAccount(new SavingsAccount(1000.0, 0.06));
        Bank.addCustomer("Kate", "Anurevа");
        Bank.getCustomer(3).addAccount(new SavingsAccount(700.0, 0.08));*/
        //Customer window
        ShowCustomerDetails();
    }

    @Override
    protected boolean onMenu(TMenuEvent menu) {
        if (menu.getId() == ABOUT_APP) {
            messageBox("About", "\t\t\t\t\t   Just a simple Jexer demo.\n\nCopyright \u00A9 2023 Katerina \'' Anureva").show();
            return true;
        }
        if (menu.getId() == CUST_INFO) {
            ShowCustomerDetails();
            return true;
        }
        return super.onMenu(menu);
    }

    private void ShowCustomerDetails() {
        TWindow custWin = addWindow("Customer Window", 2, 1, 40, 10, TWindow.NOZOOMBOX);
        custWin.newStatusBar("Enter valid customer number and press Show...");

        custWin.addLabel("Enter customer number: ", 2, 2);
        TField custNo = custWin.addField(24, 2, 3, false);
        TText details = custWin.addText("", 2, 4, 38, 8);

        custWin.addButton("&Show", 28, 2, new TAction() {
            @Override
            public void DO() {
                try {
                    int custNum = Integer.parseInt(custNo.getText());
                    Customer customer = readCustomerData(custNum);

                    if (customer != null) {
                        String ownerName = customer.getFirstName() + " " + customer.getLastName();
                        StringBuilder accountDetails = new StringBuilder();

                        for (int i = 0; i < customer.getNumberOfAccounts(); i++) {
                            Account account = customer.getAccount(0);
                            String accountType = account.getClass().getSimpleName();
                            double balance = account.getBalance();

                            accountDetails.append("Account 1: ")
                                    .append(accountType).append(", \nBalance: $").append(balance)
                                    .append("\n");

                        }

                        details.setText("Owner Name: " + ownerName + " (id=" + custNum + ")\n"
                                + accountDetails.toString());
                    } else {
                        messageBox("Error", "Invalid customer number!").show();
                    }
                } catch (NumberFormatException e) {
                    messageBox("Error", "You must provide a valid customer number!").show();
                }
            }
        });
    }

    private Customer readCustomerData(int custNum) {
        Customer customer = null;

        try (BufferedReader reader = new BufferedReader(new FileReader("C:\\Users\\kanur\\OneDrive\\Documents\\NetBeansProjects\\TUIdemo\\src\\test.dat"))) {
            String line;
            int currentCustNum = -1;

            while ((line = reader.readLine()) != null) {
                if (line.trim().isEmpty()) {
                    continue;
                }

                currentCustNum++;

                if (currentCustNum == custNum) {
                    String[] customerInfo = line.split("\t");
                    String firstName = customerInfo[0];
                    String lastName = customerInfo[1];
                    int numOfAccounts = Integer.parseInt(customerInfo[2]);

                    customer = new Customer(firstName, lastName);

                    for (int i = 0; i < numOfAccounts; i++) {
                        line = reader.readLine();

                        if (line != null) {
                            String[] accountInfo = line.split("\t");
                            String accountType = accountInfo[0];
                            double balance = Double.parseDouble(accountInfo[1]);
                            double interestRate = Double.parseDouble(accountInfo[2]);

                            Account account;
                            if (accountType.equals("S")) {
                                account = new SavingsAccount(balance, interestRate);
                            } else {
                                account = new CheckingAccount(balance);
                            }

                            customer.addAccount(account);
                            break;  
                        }

                    }

                    break;
                }
            }
        } catch (IOException e) {
        }
        return customer;
    }

}
```
**Скріншот роботи програми**
![](https://github.com/ppc-ntu-khpi/34-tui-1-katushhiaa/blob/master/for_5_Works.jpg "Work")

А тепер про те, що я говорила раніше, коли я вводжу 2, то вилазить отаке, на жаль :(
![](https://github.com/ppc-ntu-khpi/34-tui-1-katushhiaa/blob/master/for_5_Error.jpg "Error")
