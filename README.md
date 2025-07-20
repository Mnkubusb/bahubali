```
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define MAX_TABLES        10
#define MAX_MENU_ITEMS     7
#define MAX_ORDER_ITEMS   10

typedef struct {
    char  name[50];
    float price;
} MenuItem;

typedef struct {
    int itemIndex;   // index into menu[]
    int quantity;
} OrderItem;

typedef struct {
    int id;                      // table number
    OrderItem orders[MAX_ORDER_ITEMS];
    int orderCount;
    int isOccupied;
} Table;

// Global data
MenuItem menu[MAX_MENU_ITEMS];
Table    tables[MAX_TABLES];

// Function prototypes
void initializeMenu(void);
void initializeTables(void);
void displayMenu(void);
void takeOrder(void);
void viewOrders(void);
void generateBill(void);

int main(void) {
    int choice;
    initializeMenu();
    initializeTables();

    while (1) {
        printf("\n=== Restaurant Management System ===\n");
        printf("1. Display Menu\n");
        printf("2. Take Order\n");
        printf("3. View Table Orders\n");
        printf("4. Generate Bill\n");
        printf("5. Exit\n");
        printf("Enter your choice: ");

        if (scanf("%d", &choice) != 1) {
            printf("Invalid input. Exiting.\n");
            break;
        }

        switch (choice) {
            case 1: displayMenu();    break;
            case 2: takeOrder();      break;
            case 3: viewOrders();     break;
            case 4: generateBill();   break;
            case 5:
                printf("Thank you! Have a nice day.\n");
                exit(0);
            default:
                printf("Invalid choice. Try again.\n");
        }
    }
    return 0;
}

void initializeMenu(void) {
    strcpy(menu[0].name, "Butter Chicken");        menu[0].price = 250.00f;
    strcpy(menu[1].name, "Paneer Butter Masala");  menu[1].price = 200.00f;
    strcpy(menu[2].name, "Dal Makhani");           menu[2].price = 180.00f;
    strcpy(menu[3].name, "Veg Biryani");           menu[3].price = 150.00f;
    strcpy(menu[4].name, "Tandoori Chicken");      menu[4].price = 300.00f;
    strcpy(menu[5].name, "Butter Naan");           menu[5].price = 40.00f;
    strcpy(menu[6].name, "Gulab Jamun (2 pcs)");   menu[6].price = 60.00f;
}

void initializeTables(void) {
    for (int i = 0; i < MAX_TABLES; i++) {
        tables[i].id         = i + 1;
        tables[i].orderCount = 0;
        tables[i].isOccupied = 0;
    }
}

void displayMenu(void) {
    printf("\n--- Menu (₹) ---\n");
    for (int i = 0; i < MAX_MENU_ITEMS; i++) {
        printf("%d. %-25s ₹%.2f\n", i + 1, menu[i].name, menu[i].price);
    }
}

void takeOrder(void) {
    int tNum, choice, qty;
    printf("\nEnter table number (1-%d): ", MAX_TABLES);
    if (scanf("%d", &tNum) != 1 || tNum < 1 || tNum > MAX_TABLES) {
        printf("Invalid table number.\n");
        return;
    }

    Table *t = &tables[tNum - 1];
    t->isOccupied = 1;

    displayMenu();
    printf("Enter menu item number to order (0 to finish): ");

    while (scanf("%d", &choice) == 1 && choice != 0) {
        if (choice < 1 || choice > MAX_MENU_ITEMS) {
            printf("Invalid item. Try again.\n");
        } else {
            printf("Enter quantity: ");
            if (scanf("%d", &qty) != 1 || qty < 1) {
                printf("Quantity must be at least 1.\n");
                // Clear invalid input if needed
            } else if (t->orderCount >= MAX_ORDER_ITEMS) {
                printf("Order limit reached for this table.\n");
                break;
            } else {
                t->orders[t->orderCount].itemIndex = choice - 1;
                t->orders[t->orderCount].quantity  = qty;
                t->orderCount++;
                printf("Added %d x %s.\n", qty, menu[choice - 1].name);
            }
        }
        printf("Enter next item number (0 to finish): ");
    }
    printf("Order taken for table %d.\n", t->id);
}

void viewOrders(void) {
    int tNum;
    printf("\nEnter table number to view orders (1-%d): ", MAX_TABLES);
    if (scanf("%d", &tNum) != 1 || tNum < 1 || tNum > MAX_TABLES) {
        printf("Invalid table number.\n");
        return;
    }

    Table *t = &tables[tNum - 1];
    if (!t->isOccupied || t->orderCount == 0) {
        printf("No orders for table %d.\n", t->id);
        return;
    }

    printf("\nOrders for table %d:\n", t->id);
    for (int i = 0; i < t->orderCount; i++) {
        int idx = t->orders[i].itemIndex;
        printf("%2d x %-25s ₹%.2f each\n",
               t->orders[i].quantity,
               menu[idx].name,
               menu[idx].price);
    }
}

void generateBill(void) {
    int tNum;
    printf("\nEnter table number to generate bill (1-%d): ", MAX_TABLES);
    if (scanf("%d", &tNum) != 1 || tNum < 1 || tNum > MAX_TABLES) {
        printf("Invalid table number.\n");
        return;
    }

    Table *t = &tables[tNum - 1];
    if (!t->isOccupied || t->orderCount == 0) {
        printf("No orders to bill for table %d.\n", t->id);
        return;
    }

    float total = 0.0f;
    printf("\nBill for table %d:\n", t->id);
    printf("%-25s %3s %8s %10s\n", "Item", "Qty", "Price", "Subtotal");
    printf("---------------------------------------------------------\n");

    for (int i = 0; i < t->orderCount; i++) {
        int   idx      = t->orders[i].itemIndex;
        int   qty      = t->orders[i].quantity;
        float price    = menu[idx].price;
        float subtotal = price * qty;
        total += subtotal;
        printf("%-25s %3d   ₹%7.2f   ₹%8.2f\n",
               menu[idx].name, qty, price, subtotal);
    }

    printf("---------------------------------------------------------\n");
    printf("Total Amount:\t\t\t\t₹%.2f\n", total);

    // Reset table after billing
    t->orderCount = 0;
    t->isOccupied = 0;
    printf("Table %d is now free.\n", t->id);
}
```
