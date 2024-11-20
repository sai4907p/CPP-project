#include <iostream>
#include <string>
#include <iomanip>
using namespace std;

#define MAX_FLIGHTS 10
#define MAX_RESERVATIONS 100

class Member {
public:
    string name;
    string gender;
    int age;

    void getMemberDetails() {
        cout << "Enter Name: ";
        cin.ignore(); // To clear the newline character left in the buffer
        getline(cin, name);
        cout << "Enter Age: ";
        cin >> age;
        cout << "Enter Gender: ";
        cin >> gender;
    }
};

class Reservation {
private:
    Member members[10];  // Assume a maximum of 10 members per reservation
    int memberCount;
    string flightNumber;
    string destination;
    bool paymentStatus;  // False = pending, True = paid

public:
    Reservation() {
        memberCount = 0;
        paymentStatus = false;  // Default payment status is false (pending)
    }

    void makeReservation(string flightNum, string dest, int count) {
        flightNumber = flightNum;
        destination = dest;
        memberCount = count;

        for (int i = 0; i < memberCount; i++) {
            cout << "\nEnter details for Member " << i + 1 << ":\n";
            members[i].getMemberDetails();
        }
    }

    void viewReservation() {
        cout << "\nFlight: " << flightNumber << "\n";
        cout << "Destination: " << destination << "\n";
        cout << "Number of Members: " << memberCount << "\n";

        for (int i = 0; i < memberCount; i++) {
            cout << "  Member " << i + 1 << ": " << members[i].name << ", Age: " << members[i].age
                 << ", Gender: " << members[i].gender << ", Destination: " << destination << "\n";
        }

        // Display payment status
        cout << "Payment Status: " << (paymentStatus ? "Paid" : "Pending") << "\n";
    }

    void makePayment() {
        if (!paymentStatus) {
            paymentStatus = true;
            cout << "Payment successfully completed for reservation!\n";
        } else {
            cout << "The payment for this reservation is already completed.\n";
        }
    }

    bool isPaid() {
        return paymentStatus;
    }

    string getDestination() {
        return destination;
    }

    string getFlightNumber() {
        return flightNumber;
    }

    void cancelMember(string memberName) {
        for (int i = 0; i < memberCount; i++) {
            if (members[i].name == memberName) {
                // Shift remaining members to fill the gap
                for (int j = i; j < memberCount - 1; j++) {
                    members[j] = members[j + 1];
                }
                memberCount--; // Reduce the member count
                cout << "Ticket for member " << memberName << " has been canceled.\n";

                // If no members are left, reset the reservation
                if (memberCount == 0) {
                    flightNumber = "";
                    destination = "";
                }
                return;
            }
        }
        cout << "Member not found!\n";
    }
};

class Flight {
public:
    string destination;
    string flightNumber;
    string departureTime;
    string arrivalTime;

    Flight(string dest, string fNum, string depTime, string arrTime) {
        destination = dest;
        flightNumber = fNum;
        departureTime = depTime;
        arrivalTime = arrTime;
    }
};

Flight flights[MAX_FLIGHTS] = {
    Flight("Hyderabad", "VJ101", "08:00", "09:00"),
    Flight("Chennai", "VJ102", "10:00", "11:30"),
    Flight("Bangalore", "VJ103", "12:00", "13:30"),
    Flight("Mumbai", "VJ104", "15:00", "17:30"),
    Flight("Delhi", "VJ105", "18:00", "21:00"),
    Flight("Kolkata", "VJ106", "06:00", "08:00"),
    Flight("Goa", "VJ107", "07:00", "09:30"),
    Flight("Pune", "VJ108", "09:00", "11:00"),
    Flight("Jaipur", "VJ109", "13:00", "14:30"),
    Flight("Lucknow", "VJ110", "16:00", "18:00")
};

Reservation reservations[MAX_RESERVATIONS];
int reservationCount = 0;

void showFlights() {
    cout << "Available Flights from Vijayawada:\n";
    cout << setw(10) << "Flight" << setw(15) << "Destination" << setw(15) << "Departure" << setw(15) << "Arrival\n";
    for (int i = 0; i < MAX_FLIGHTS; i++) {
        cout << setw(10) << flights[i].flightNumber
             << setw(15) << flights[i].destination
             << setw(15) << flights[i].departureTime
             << setw(15) << flights[i].arrivalTime << "\n";
    }
}

void makeReservation() {
    if (reservationCount >= MAX_RESERVATIONS) {
        cout << "No more reservations can be made!\n";
        return;
    }

    string destination, flightNumber;
    showFlights();

    cout << "Enter Destination City: ";
    cin >> destination;

    bool found = false;
    for (int i = 0; i < MAX_FLIGHTS; i++) {
        if (flights[i].destination == destination) {
            flightNumber = flights[i].flightNumber;
            found = true;
            break;
        }
    }

    if (!found) {
        cout << "No flight available to " << destination << " from Vijayawada.\n";
        return;
    }

    int memberCount;
    cout << "Enter Number of Members (Max 10): ";
    cin >> memberCount;

    if (memberCount > 10 || memberCount <= 0) {
        cout << "Invalid number of members. Maximum is 10.\n";
        return;
    }

    reservations[reservationCount].makeReservation(flightNumber, destination, memberCount);
    reservationCount++;

    cout << "Reservation Successful! Flight Number: " << flightNumber << "\n";
}

void viewReservations() {
    if (reservationCount == 0) {
        cout << "No reservations available.\n";
        return;
    }

    for (int i = 0; i < reservationCount; i++) {
        cout << "\nReservation " << i + 1 << ":\n";
        reservations[i].viewReservation();
    }
}

void cancelMember() {
    if (reservationCount == 0) {
        cout << "No reservations to cancel members from.\n";
        return;
    }

    string name;
    cout << "Enter Name of Member to Cancel Ticket: ";
    cin.ignore();
    getline(cin, name);

    for (int i = 0; i < reservationCount; i++) {
        reservations[i].cancelMember(name);

        // If there are no members in the reservation, we delete the reservation
        if (reservations[i].getFlightNumber() == "") {
            // Shift the reservations to fill the gap
            for (int j = i; j < reservationCount - 1; j++) {
                reservations[j] = reservations[j + 1];
            }
            reservationCount--;
        }
    }
}

void markPayment() {
    string flightNumber, destination;
    cout << "Enter Flight Number to Mark Payment: ";
    cin >> flightNumber;
    cout << "Enter Destination: ";
    cin >> destination;

    bool found = false;
    for (int i = 0; i < reservationCount; i++) {
        if (reservations[i].getFlightNumber() == flightNumber && reservations[i].getDestination() == destination) {
            reservations[i].makePayment();
            found = true;
            break;
        }
    }

    if (!found) {
        cout << "Reservation not found.\n";
    }
}

void menu() {
    while (true) {
        cout << "\n1. Show Available Flights\n2. Make Reservation\n3. View Reservations\n4. Cancel Member Ticket\n5. Mark Payment\n6. Exit\n";
        int choice;
        cout << "Enter Choice: ";
        cin >> choice;

        // Check for invalid input
        if (cin.fail()) {
            cin.clear();            // Clear the error flag
            cin.ignore(1000, '\n'); // Discard invalid input
            cout << "Invalid input! Please enter a number between 1 and 6.\n";
            continue;               // Prompt user again
        }

        switch (choice) {
            case 1: showFlights(); break;
            case 2: makeReservation(); break;
            case 3: viewReservations(); break;
            case 4: cancelMember(); break;
            case 5: markPayment(); break;
            case 6: cout << "Exiting the program. Thank you!\n"; return;
            default: cout << "Invalid choice! Please enter a number between 1 and 6.\n";
        }
    }
}

int main() {
    cout << "Welcome to Vijayawada Flight Reservation System\n";
    menu();
    return 0;
}
