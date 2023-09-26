#include <iostream>
#include <string>
#include <fstream>

using namespace std;

struct todolist {
    int serial_No;
    string task;
};

int serial_No = 0; // Initializing the serial number

void intro();
void addtask();
void showtask();
void deletetask();
void marktask();

int main() {
    while (true) {
        int choice;
        intro();
        cout << "\n\t 1.ADD TASK";
        cout << "\n\t 2.SHOW TASK";
        cout << "\n\t 3.DELETE TASK";
        cout << "\n\t 4.MARK TASK";
        cout << "\n\t 5.EXIT";
        cout << "\n\t ENTER CHOICE:  ";
        cin >> choice;

        switch (choice) {
            case 1:
                addtask();
                break;
            case 2:
                showtask();
                break;
            case 3:
                deletetask();
                break;
            case 4:
                marktask();
                break;
            case 5:
                cout << "Exiting the program!" << endl;
                return 0;
            default:
                cout << "INVALID CHOICE! try again " << endl;
        }
    }
    return 0;
}

void intro() {
    cout << "____________________________________________________________________" << endl;
    cout << "\t\t\t\ MY TO-DO LIST " << endl;
    cout << "____________________________________________________________________" << endl;
}

void addtask() {
    todolist todo;
    char save;
    char more;
    cout << "Enter new task:   " << endl;
    cin.get();
    getline(cin, todo.task);

    // Increment serial_No
    serial_No++;

    ofstream fout;
    fout.open("todo.txt", ios::app);
    if (!fout.is_open()) {
        cerr << "Error opening todo.txt for writing." << endl;
        return;
    }

    fout << serial_No << endl;
    fout << todo.task << endl;
    fout.close();

    cout << "Do you want to add more task? (Y/N):  ";
    cin >> more;
    if (more == 'Y') {
        addtask();
    } else {
        cout << "TASK ADDED SUCCESSFULLY!" << endl;
    }
}

void showtask() {
    todolist todo;
    char exit;
    ifstream fin("todo.txt");

    if (!fin.is_open()) {
        cerr << "Error opening todo.txt for reading." << endl;
        return;
    }

    cout << "Your tasks:   " << endl;
    while (fin >> todo.serial_No) {
        fin.ignore();
        getline(fin, todo.task);
        cout << "\t" << todo.serial_No << ":   " << todo.task << endl;
    }
    fin.close();

    cout << "Do you want to exit? (Y/N):    ";
    cin >> exit;
    if (exit != 'Y') {
        showtask();
    }
}

void deletetask() {
    int delete_serial_No;
    char del;
    int index = 1;
    cout << "Enter serial number of the task you want to delete:    ";
    cin >> delete_serial_No;
    cout << "ARE YOU SURE YOU WANT TO DELETE THIS TASK? (Y/N):   ";
    cin >> del;

    if (del == 'Y') {
        todolist todo;
        ofstream temFile;
        temFile.open("tem.txt");
        ifstream fin;
        fin.open("todo.txt");

        while (fin >> todo.serial_No) {
            fin.ignore();
            getline(fin, todo.task);

            if (todo.serial_No != delete_serial_No) {
                temFile << "\n" << index;
                temFile << "\n" << todo.task;
                index++;
            }
        }
        fin.close();
        temFile.close();
        remove("todo.txt");
        rename("tem.txt", "todo.txt");
        cout << "TASK HAS BEEN DELETED SUCCESSFULLY!" << endl;
    } else {
        cout << "NO TASK DELETED!" << endl;
    }
}

void marktask() {
    int mark_serial_No;
    char mark;
    cout << "Enter the serial number of the task you want to mark as completed: ";
    cin >> mark_serial_No;

    ifstream fin("todo.txt");
    ofstream temFile("tem.txt");

    if (!fin.is_open() || !temFile.is_open()) {
        cerr << "Error opening files." << endl;
        return;
    }

    bool found = false;
    todolist todo;

    while (fin >> todo.serial_No) {
        fin.ignore();
        getline(fin, todo.task);

        if (todo.serial_No == mark_serial_No) {
            found = true;
            cout << "Are you sure you want to mark this task as completed? (Y/N): ";
            cin >> mark;

            if (mark == 'Y') {
                todo.task+= "\t\ (completed!)";
            }
        }

        temFile << todo.serial_No << endl;
        temFile << todo.task << endl;
    }

    fin.close();
    temFile.close();

    if (found) {
        remove("todo.txt");
        rename("tem.txt", "todo.txt");
        cout << "Task marked as completed!" << endl;
    } else {
        cout << "Task not found." << endl;
        remove("tem.txt");
    }
}
