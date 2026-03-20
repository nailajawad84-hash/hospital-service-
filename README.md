import sys
import time
import random

# ==========================================
# 1. DATA CLASSES
# ==========================================

class Patient:
    def __init__(self, p_id, name, age, disease):
        self.id = p_id
        self.name = name
        self.age = age
        self.disease = disease
        self.bills = 0

    def __str__(self):
        return f"[ID: {self.id}] Name: {self.name} | Age: {self.age} | Disease: {self.disease}"

class Doctor:
    def __init__(self, name, specialty):
        self.name = name
        self.specialty = specialty

    def __str__(self):
        return f"Dr. {self.name} ({self.specialty})"

class Medicine:
    def __init__(self, name, price, stock):
        self.name = name
        self.price = price
        self.stock = stock

    def __str__(self):
        return f"{self.name} | Price: ${self.price} | Stock: {self.stock}"

# ==========================================
# 2. CHATBOT CLASS
# ==========================================

class BahriaBot:
    def start(self):
        print(f"\n{'~'*40}")
        print("🤖 BAHRIA AI ASSISTANT ONLINE")
        print("Ask about: timings, emergency, owner, location, pharmacy")
        print(f"{'~'*40}")

        knowledge = {
            "owner": "Bahria International is owned and led by Mr. Rana.",
            "timings": "OPD: 9 AM - 5 PM. Emergency: 24/7.",
            "emergency": "Dial 1122 for immediate ambulance service.",
            "location": "Bahria Town Phase 8, near the Statue of Liberty.",
            "pharmacy": "Our pharmacy is stocked with international standard medicines.",
            "appointment": "Please contact the reception or log in as Owner to book."
        }

        while True:
            q = input("\n(Chatbot) Ask a question (or 'exit'): ").lower().strip()
            if q == "exit":
                break
            
            found = False
            for key, val in knowledge.items():
                if key in q:
                    print(f"Bahria Bot: {val}")
                    found = True
                    break
            if not found:
                print("Bahria Bot: I can only answer general queries. Please ask the admin.")

# ==========================================
# 3. MAIN HOSPITAL SYSTEM
# ==========================================

class HospitalSystem:
    def __init__(self):
        self.patients = []
        self.doctors = []
        self.appointments = []
        self.operations = []
        self.medicines = []
        self.bot = BahriaBot()
        self.patient_id_counter = 100
        
        # Pre-populate some data
        self.doctors.append(Doctor("Ali", "Cardiology"))
        self.medicines.append(Medicine("Panadol", 10, 50))

    # --- HELPER INPUT FUNCTIONS ---
    def get_int(self, prompt):
        while True:
            try:
                return int(input(prompt))
            except ValueError:
                print("Invalid input. Please enter a number.")

    # --- OWNER ONLY FUNCTIONS ---
    
    def add_doctor(self):
        print("\n--- ADD NEW DOCTOR ---")
        name = input("Doctor Name: ")
        spec = input("Specialty: ")
        self.doctors.append(Doctor(name, spec))
        print("Doctor added successfully.")

    def admit_patient(self):
        print("\n--- ADMIT PATIENT ---")
        name = input("Patient Name: ")
        age = self.get_int("Age: ")
        disease = input("Disease/Department: ")
        
        self.patient_id_counter += 1
        self.patients.append(Patient(self.patient_id_counter, name, age, disease))
        print(f"Patient Admitted! Assigned ID: {self.patient_id_counter}")

    def book_appointment(self):
        print("\n--- BOOK APPOINTMENT ---")
        if not self.doctors:
            print("No doctors available.")
            return

        print("Available Doctors:")
        for idx, doc in enumerate(self.doctors):
            print(f"{idx+1}. {doc}")
        
        try:
            choice = self.get_int("Select Doctor (Number): ") - 1
            if 0 <= choice < len(self.doctors):
                p_name = input("Patient Name for Appointment: ")
                time_slot = input("Time Slot (e.g., 2:00 PM): ")
                appt = f"Patient: {p_name} with {self.doctors[choice].name} at {time_slot}"
                self.appointments.append(appt)
                print("Appointment Confirmed.")
            else:
                print("Invalid selection.")
        except Exception:
            print("Error booking appointment.")

    def schedule_operation(self):
        print("\n--- SCHEDULE OPERATION ---")
        p_name = input("Patient Name: ")
        op_type = input("Operation Type: ")
        date = input("Date (DD-MM-YYYY): ")
        self.operations.append(f"{op_type} for {p_name} on {date}")
        print("Operation Scheduled.")

    def pharmacy_menu(self):
        while True:
            print(f"\n=== PHARMACY MANAGEMENT (Secured) ===")
            print("1. Add Medicine Stock")
            print("2. View Medicines")
            print("3. Back")
            
            ch = input("Choice: ")
            
            if ch == '1':
                name = input("Medicine Name: ")
                price = self.get_int("Price: ")
                stock = self.get_int("Quantity: ")
                self.medicines.append(Medicine(name, price, stock))
                print("Medicine Added.")
            elif ch == '2':
                self.view_medicines()
            elif ch == '3':
                break

    # --- SHARED / VIEW FUNCTIONS ---
    
    def view_patients(self):
        print("\n--- PATIENT LIST ---")
        if not self.patients: print("No patients found.")
        for p in self.patients: print(p)

    def view_doctors(self):
        print("\n--- DOCTOR LIST ---")
        if not self.doctors: print("No doctors found.")
        for d in self.doctors: print(d)

    def view_medicines(self):
        print("\n--- PHARMACY STOCK ---")
        if not self.medicines: print("Pharmacy is empty.")
        for m in self.medicines: print(m)

# ==========================================
# 4. MENU LOGIC
# ==========================================

def main():
    hospital = HospitalSystem()
    
    while True:
        print("\n" + "="*50)
        print("   BAHRIA INTERNATIONAL HOSPITAL SYSTEM")
        print("="*50)
        print("1. Owner Login (Full Access)")
        print("2. Normal User / Staff (View Only)")
        print("3. Exit")
        
        role = input("Select Role: ")
        
        if role == '1':
            # --- SECURITY CHECK ---
            key = input("Enter Owner Key: ")
            if key == "7272":
                print("\n" + "*"*65)
                print(" ACCESS GRANTED: Welcome, Owner Mr. Rana to Bahria International")
                print("*"*65)
                
                # OWNER MENU LOOP
                while True:
                    print(f"\n[OWNER PANEL: MR. RANA]")
                    print("1. Admit Patient")
                    print("2. Add Doctor")
                    print("3. Book Appointment")
                    print("4. Schedule Operation")
                    print("5. Pharmacy Section")
                    print("6. View All Data (Patients/Doctors)")
                    print("7. Launch Chatbot")
                    print("8. Logout")
                    
                    op = input("Choice: ")
                    
                    if op == '1': hospital.admit_patient()
                    elif op == '2': hospital.add_doctor()
                    elif op == '3': hospital.book_appointment()
                    elif op == '4': hospital.schedule_operation()
                    elif op == '5': hospital.pharmacy_menu()
                    elif op == '6': 
                        hospital.view_patients()
                        hospital.view_doctors()
                    elif op == '7': hospital.bot.start()
                    elif op == '8': break
                    else: print("Invalid option.")
            else:
                print("\n[ACCESS DENIED] Wrong Security Key.")
        
        elif role == '2':
            # NORMAL USER MENU LOOP
            print("\n[NORMAL USER MODE - READ ONLY]")
            while True:
                print("1. View Doctors")
                print("2. View Patients")
                print("3. View Medicines")
                print("4. Ask Chatbot")
                print("5. Back to Main Menu")
                
                op = input("Choice: ")
                
                if op == '1': hospital.view_doctors()
                elif op == '2': hospital.view_patients()
                elif op == '3': hospital.view_medicines()
                elif op == '4': hospital.bot.start()
                elif op == '5': break
                else:
                    # STRICT RULE: DENY WRITE ACCESS
                    print("Error: You do not have permission to perform actions. Contact Mr. Rana.")

        elif role == '3':
            print("System Shutdown.")
            sys.exit()

if __name__ == "__main__":
    main()
