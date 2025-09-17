#include <iostream>
#include <thread>
#include <fstream>
#include <string>
#include <sstream>
#include <iomanip>
#include <mutex>
#include <unistd.h> 
#include <vector>

using namespace std;

void SIGNIN();
void SIGNUP();
void su_handler(string username, string password, int s);
void si_handler(string username, string password);
void Identification();
void ID(string pass);

// /// /// /// /// /// /// /// /// //// / // / // / /

fstream passfile;
int counter = 0;
mutex mtx;


// /// / / ///// /// /////// ///////// //////// /////
unsigned long djb2(const string& str) {
    unsigned long hash = 5381;
    for (char c : str) {
        hash = ((hash << 5) + hash) + c;  
    }
    return hash;
}



// //// //// //// //// //// //// //// //// //// ///// //
int salt_generate(int id) {
    
    return id + 2024; 
}



// /// //// //// ///// //// //// //// ///// ///// ///// 

class Account {
private:
    int salt;
public:
    int ID;
    bool authentication;
public:
    Account(int id) {
        this->ID = id;
        salt = salt_generate(id);
        authentication = false;
    }
    int get_salt() { return salt; }
    public:
    void access(){
         if(authentication) {
            cout << "Access permitted to user with ID: " << ID << endl;
            
        } else {
            cout << "Access denied. Please log in first." << endl;
        }
    }
    
};

//// /// //// ///// ///// //// ///// //// //// ///// /

vector<Account> accounts;


// // ///// ///// ///// ///// ///// //// ///// //// ///// 
int main() {
    string op;
    bool project = true;
    while (project) {
        cout << "Enter the operation (A : Sign in , B : Sign up , C : Identification) : ";
        cin >> op;
        if (op == "A") {
            SIGNIN();
        } else if (op == "B") {
            SIGNUP();
        } 
        else if (op == "C"){
        Identification();     
        }
        else if(op == "//exit"){
            project = false;
        }
        else  {
            cout << "\nWRONG INPUT!!\n";
        }
    }
    return 0;
}




// //// //// /// //// ///// /// ////// //// ///// ////// //// //
void SIGNIN() {
    string un;
    string pass;
    cout << "Username : ";
    cin >> un;
    cout << "Password : ";
    cin >> pass;
    thread th(si_handler, un, pass);
    th.join();
}




// ///// ///// //// ///// ///// //// ///// ///// ///// ///// //

void SIGNUP() {
    string un;
    string pass;
    string vpass;
    bool istrue = false;

    while (!istrue) {
        cout << "Enter your username : ";
        cin >> un;
        if (un.length() >= 10 || un.length() == 0) {
            cout << "Error: Your username must be at least one character and less than 10 characters. Please try again.\n";
        } else {
            istrue = true;
        }
    }

    istrue = false;
    while (!istrue) {
        cout << "Enter your password : ";
        cin >> pass;
        if (pass.length() >= 10 || pass.length() == 0) {
            cout << "Error: Your password must be at least one character and less than 10 characters. Please try again.\n";
        } else {
            istrue = true;
        }
    }

    istrue = false;
    while (!istrue) {
        cout << "Verify your password : ";
        cin >> vpass;
        if (vpass != pass) {
            cout << "Error: Passwords do not match.\n";
        } else {
            istrue = true;
        }
    }

    accounts.emplace_back(counter);
    thread handler(su_handler, un, pass, salt_generate(counter++));
    handler.detach(); 
}





// ///// ///// ///// ////// //// ////// ///// //// /////// ///// 
void su_handler(string username, string password, int s) {
    lock_guard<mutex> guard(mtx); 
    passfile.open("passwords.txt", ios::out | ios::app);
    if (passfile.is_open()) {
        string salted_password = password + to_string(s); 
        unsigned long hashed_password = djb2(salted_password);  
        passfile << username << "       " << s << "       " << hashed_password << endl;
        passfile.close();
    }
    else{cout<<"File cannot be opend!!"<<endl;}
}






//// ////// ////// ////// //// //// //// /////// //// ///// //

void si_handler(string username, string password) {
    int authenticated_ID;
    lock_guard<mutex> guard(mtx); 
    passfile.open("passwords.txt", ios::in);
    if (passfile.is_open()) {
        string line;
        while (getline(passfile, line)) {
            istringstream iss(line);
            string stored_username;
            int salt;
            unsigned long stored_hashed_password;
            iss >> stored_username >> salt >> stored_hashed_password;
            
            if (stored_username == username) {
               
                string salted_input_password = password + to_string(salt); 
                unsigned long hashed_input_password = djb2(salted_input_password);
                if (hashed_input_password == stored_hashed_password) {
                    cout << "Login successful!" << endl;
                    passfile.close();
                    authenticated_ID = salt - 2024;
                    accounts[authenticated_ID].authentication = true;
                    accounts[authenticated_ID].access();
                    return;
                } else {
                    cout << "Incorrect password." << endl;
                    passfile.close();
                    return;
                }
            }
        }
        cout << "Username not found." << endl;
        passfile.close();
    }
}
// ///// ///// ///// ///// ////// ///// ///// ////// //// ///

void Identification(){
    string pass;
    cout<<"Enter the password : ";
    cin>>pass;
    thread identifier(ID,pass);
    identifier.join();
 
}


// //// //// ////// ///// ////// //// ////// //// //// ///// 
void ID(string pass){
   lock_guard<mutex> guard(mtx); 
    passfile.open("passwords.txt", ios::in);
    if (passfile.is_open()) {
        string line;
        while (getline(passfile, line)) {
            istringstream iss(line);
            string stored_username;
            int salt;
            unsigned long stored_hashed_password;
            iss >> stored_username >> salt >> stored_hashed_password;
            unsigned long HP = djb2(pass + to_string(salt));
            if (stored_hashed_password == HP){
                cout<<"The username is :   "<<stored_username<<endl;
                passfile.close();
                return;
            }

            }
            cout << "No such password." << endl;
            passfile.close();
        }
    else{
        cout<<"File cannot be opened!!"<<endl;
    }
}