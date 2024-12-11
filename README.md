# Desis-Ascend
Assignment
#include <iostream>
#include <vector>
#include <string>

using namespace std;

// Base class for all items in the bookstore
class Item {
protected:
    int id;
    string title;
    double price;
    double cost;
    int quantity;

public:
    // Constructor with initializer list
    Item(int id, string title, double price, double cost, int quantity) 
        : id(id), title(title), price(price), cost(cost), quantity(quantity) {
        // Constructor body can be empty or used for additional initialization
    }

    // Display item details
    virtual void display() const {
        cout << "ID: " << id << ", Title: " << title << ", Price: $" << price
             << ", Cost: $" << cost << ", Quantity: " << quantity << endl;
    }
    
    //Getter functions for various details
    int getId() const { return id; }
    string getTitle() const { return title; }
    double getPrice() const { return price; }
    double getCost() const { return cost; }
    int getQuantity() const { return quantity; }

    // Update stock quantity
    void updateStock(int quantity) {
        this->quantity += quantity;
    }

    // Update item price
    void updatePrice(double price) {
        this->price = price;
    }

    // Update item cost
    void updateCost(double cost) {
        this->cost = cost;
    }
};
//use of Inheritance
// Derived class for books
class Book : public Item {
public:
    // Constructor with initializer list
    Book(int id, string title, double price, double cost, int quantity) 
        : Item(id, title, price, cost, quantity) {}

    // Override display method
    void display() const override {
        cout << "Book - ";
        Item::display();
    }
};

// Derived class for magazines
class Magazine : public Item {
public:
    // Constructor with initializer list
    Magazine(int id, string title, double price, double cost, int quantity) 
        : Item(id, title, price, cost, quantity) {}

    // Override display method
    void display() const override {
        cout << "Magazine - ";
        Item::display();
    }
};

// Base class for all persons in the bookstore
class Person {
protected:
    string name;

public:
    // Constructor
    Person(string name) : name(name) {}
    virtual void display() const = 0; // Pure virtual function
};

// Derived class for employees (store manager and cashier)
class Employee : public Person {
public:
    // Constructor
    Employee(string name) : Person(name) {}

    void display() const override {
        cout << "Employee: " << name << endl;
    }

    void addItem(vector<Item*>& inventory, Item* item) {
        inventory.push_back(item);
    }

    void updateItem(Item* item, double newPrice, double newCost, int newQuantity) {
        item->updatePrice(newPrice);
        item->updateCost(newCost);
        item->updateStock(newQuantity);
    }

    void removeItem(vector<Item*>& inventory, int itemId) {
        for (auto it = inventory.begin(); it != inventory.end(); ++it) {
            if ((*it)->getId() == itemId) {
                inventory.erase(it);
                break;
            }
        }
    }
};

// Derived class for customers
class Customer : public Person {
public:
    // Constructor
    Customer(string name) : Person(name) {}

    void display() const override {
        cout << "Customer: " << name << endl;
    }
};

// Bookstore class to manage all operations
class Bookstore {
private:
    vector<Item*> inventory;
    Employee* manager;
    Employee* cashier;

public:
    // Constructor
    Bookstore(Employee* mgr, Employee* csh) : manager(mgr), cashier(csh) {}

    void addItem(Item* item) {
        manager->addItem(inventory, item);
    }

    void updateItem(int itemId, double newPrice, double newCost, int newQuantity) {
        for (auto& item : inventory) {
            if (item->getId() == itemId) {
                manager->updateItem(item, newPrice, newCost, newQuantity);
                break;
            }
        }
    }

    void removeItem(int itemId) {
        manager->removeItem(inventory, itemId);
    }

    void sellItem(int itemId, int quantity) {
        for (auto& item : inventory) {
            if (item->getId() == itemId) {
                cashier->updateItem(item, item->getPrice(), item->getCost(), -quantity);
                cout << "Sold " << quantity << " of " << item->getTitle() << endl;
                break;
            }
        }
    }

    void displayInventory() const {
        cout << "Inventory:" << endl;
        for (const auto& item : inventory) {
            item->display();
        }
    }

    ~Bookstore() {
        // Clean up dynamically allocated memory
        for (auto item : inventory) {
            delete item;
        }
    }
};

// Main function
int main() {
   Employee manager("Sam Smith");
    Employee cashier("Jane Derry");
    Bookstore bookstore(&manager, &cashier);

    int choice;
    do {
        cout << "\nMenu:\n";
        cout << "1. Add Item\n";
        cout << "2. Update Item\n";
        cout << "3. Sell Item\n";
        cout << "4. Remove Item\n";
        cout << "5. Display Inventory\n";
        cout << "6. Exit\n";
        cout << "Enter your choice: ";
        cin >> choice;

        switch (choice) {
            case 1: {
                int id, quantity;
                string title;
                double price, cost;
                int itemType;
                
                cout << "Enter Item Type (1 for Book, 2 for Magazine): ";
                cin >> itemType;
                cout << "Enter Item ID: ";
                cin >> id;
                cout << "Enter Title: ";
                cin.ignore(); // to ignore the newline character left by previous input
                getline(cin, title);
                cout << "Enter Price: ";
                cin >> price;
                cout << "Enter Cost: ";
                cin >> cost;
                cout << "Enter Quantity: ";
                cin >> quantity;

                if (itemType == 1) {
                    bookstore.addItem(new Book(id, title, price, cost, quantity));
                } else if (itemType == 2) {
                    bookstore.addItem(new Magazine(id, title, price, cost, quantity));
                } else {
                    cout << "Invalid Item Type!" << endl;
                }
                break;
            }

            case 2: {
                int id;
                double newPrice, newCost;
                int newQuantity;
                
                cout << "Enter Item ID to Update: ";
                cin >> id;
                cout << "Enter New Price: ";
                cin >> newPrice;
                cout << "Enter New Cost: ";
                cin >> newCost;
                cout << "Enter New Quantity: ";
                cin >> newQuantity;

                bookstore.updateItem(id, newPrice, newCost, newQuantity);
                break;
            }

            case 3: {
                int id, quantity;
                
                cout << "Enter Item ID to Sell: ";
                cin >> id;
                cout << "Enter Quantity to Sell: ";
                cin >> quantity;

                bookstore.sellItem(id, quantity);
                break;
            }

            case 4: {
                int id;
                cout << "Enter Item ID to Remove: ";
                cin >> id;
                bookstore.removeItem(id);
                break;
            }

            case 5:
                bookstore.displayInventory();
                break;

            case 6:
                cout << "Exiting the system..." << endl;
                break;

            default:
                cout << "Invalid choice! Please try again." << endl;
        }
    } while (choice != 6);

    return 0;
}
