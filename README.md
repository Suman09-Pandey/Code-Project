# Code-Project
Code Project on Student Grade Calculator Description
import tkinter as tk
from tkinter import messagebox
import json
import os

# JSON file to store users
USER_FILE = "users.json"

# Load user data from file
def load_users():
    if os.path.exists(USER_FILE):
        try:
            with open(USER_FILE, "r") as f:
                return json.load(f)
        except json.decoder.JSONDecodeError:
            return {}
    return {}

# Save user data to file
def save_users(users):
    with open(USER_FILE, "w") as f:
        json.dump(users, f)

# Initialize user_db from file
user_db = load_users()

class GradeCalculatorApp:
    def __init__(self, root):
        self.root = root
        self.root.geometry("650x650")
        self.root.resizable(False, False)
        self.show_login()

    def clear_window(self):
        for widget in self.root.winfo_children():
            widget.destroy()

    def show_login(self):
        self.root.title("Login - Grade Calculator")
        self.clear_window()
        self.root.configure(bg="#e0f7fa")

        tk.Label(self.root, text="Student Login", font=("Helvetica", 22, "bold"), bg="#e0f7fa", fg="#00796b").pack(pady=30)

        tk.Label(self.root, text="Username", bg="#e0f7fa").pack()
        self.username_entry = tk.Entry(self.root, width=25)
        self.username_entry.pack(pady=5)

        password_frame = tk.Frame(self.root, bg="#e0f7fa")
        password_frame.pack()
        
        tk.Label(password_frame, text="Password", bg="#e0f7fa").pack(side=tk.LEFT)

        self.show_password_var = tk.BooleanVar()
        show_password_check = tk.Checkbutton(
            password_frame,
            text="Show",
            variable=self.show_password_var,
            bg="#e0f7fa",
            command=self.toggle_password_visibility
        )
        show_password_check.pack(side=tk.LEFT, padx=5)

        self.password_entry = tk.Entry(self.root, show="*", width=25)
        self.password_entry.pack(pady=5)

        tk.Button(self.root, text="Login", width=15, bg="#4db6ac", fg="white", activebackground="#00897b",
                  command=self.login).pack(pady=10)
        tk.Button(self.root, text="Create New Account", bg="white", fg="#00796b", bd=0, command=self.show_signup).pack()

    def toggle_password_visibility(self, entry=None, var=None):
        entry = entry if entry else self.password_entry
        var = var if var else self.show_password_var
        if var.get():
            entry.config(show="")
        else:
            entry.config(show="*")

    def login(self):
        username = self.username_entry.get()
        password = self.password_entry.get()

        if username in user_db and user_db[username] == password:
            messagebox.showinfo("Success", "Login Successful!")
            self.show_calculator()
        else:
            messagebox.showerror("Error", "Invalid username or password")

    def show_signup(self):
        self.root.title("Create Account")
        self.clear_window()
        self.root.configure(bg="#ffe0b2")

        tk.Label(self.root, text="Create New Account", font=("Helvetica", 20, "bold"), bg="#ffe0b2", fg="#e65100").pack(pady=30)

        tk.Label(self.root, text="Username", bg="#ffe0b2").pack()
        self.new_username = tk.Entry(self.root, width=25)
        self.new_username.pack(pady=5)

        password_frame = tk.Frame(self.root, bg="#ffe0b2")
        password_frame.pack()

        tk.Label(password_frame, text="Password", bg="#ffe0b2").pack(side=tk.LEFT)

        self.show_password_var_signup = tk.BooleanVar()
        show_password_check = tk.Checkbutton(
            password_frame,
            text="Show",
            variable=self.show_password_var_signup,
            bg="#ffe0b2",
            command=lambda: self.toggle_password_visibility(self.new_password, self.show_password_var_signup)
        )
        show_password_check.pack(side=tk.LEFT, padx=5)

        self.new_password = tk.Entry(self.root, show="*", width=25)
        self.new_password.pack(pady=5)

        tk.Button(self.root, text="Create Account", width=15, bg="#ff9800", fg="white", command=self.create_account).pack(pady=10)
        tk.Button(self.root, text="Back to Login", bg="white", fg="#e65100", bd=0, command=self.show_login).pack()

    def create_account(self):
        username = self.new_username.get()
        password = self.new_password.get()

        if not username or not password:
            messagebox.showwarning("Warning", "All fields are required.")
        elif username in user_db:
            messagebox.showerror("Error", "Username already exists.")
        else:
            user_db[username] = password
            save_users(user_db)
            messagebox.showinfo("Success", "Account created successfully.")
            self.show_login()

    def show_calculator(self):
        self.root.title("Student Grade Calculator")
        self.clear_window()
        self.root.configure(bg="#f1f8e9")

        self.subject_entries = []
        self.subject_labels = []
        self.subject_status_labels = []

        tk.Label(self.root, text="Grade Calculator", font=("Helvetica", 20, "bold"), bg="#f1f8e9", fg="#33691e").pack(pady=10)

        self.form_frame = tk.Frame(self.root, bg="#f1f8e9")
        self.form_frame.pack(pady=10)

        self.result_frame = tk.Frame(self.root, bg="#f1f8e9")
        self.result_frame.pack(pady=10)

        self.total_label = tk.Label(self.result_frame, text="", font=("Arial", 12), bg="#f1f8e9")
        self.percentage_label = tk.Label(self.result_frame, text="", font=("Arial", 12), bg="#f1f8e9")
        self.grade_label = tk.Label(self.result_frame, text="", font=("Arial", 12, "bold"), fg="#1b5e20", bg="#f1f8e9")

        self.button_frame = tk.Frame(self.root, bg="#f1f8e9")
        self.button_frame.pack(pady=10)

        tk.Button(self.button_frame, text="Add Subject", bg="#aed581", command=self.add_subject).grid(row=0, column=0, padx=10)
        tk.Button(self.button_frame, text="Remove Subject", bg="#ff8a65", command=self.remove_subject).grid(row=0, column=1, padx=10)
        tk.Button(self.button_frame, text="Calculate Grade", bg="#4db6ac", fg="white", command=self.calculate_grade).grid(row=0, column=2, padx=10)
        tk.Button(self.button_frame, text="Reset", bg="#fdd835", command=self.reset_fields).grid(row=0, column=3, padx=10)
        tk.Button(self.button_frame, text="Logout", bg="white", fg="#33691e", command=self.show_login).grid(row=0, column=4, padx=10)

        for _ in range(5):
            self.add_subject()

    def add_subject(self):
        index = len(self.subject_entries) + 1
        row_frame = tk.Frame(self.form_frame, bg="#f1f8e9")
        row_frame.grid(row=index, column=0, sticky="w", pady=5, padx=10)

        label = tk.Label(row_frame, text=f"Subject {index} Marks (0-100):", font=("Arial", 12), bg="#f1f8e9")
        label.pack(side=tk.LEFT)

        entry = tk.Entry(row_frame, width=10, font=("Arial", 12), bg="lightyellow")
        entry.pack(side=tk.LEFT, padx=5)

        status_label = tk.Label(row_frame, text="", font=("Arial", 10), bg="#f1f8e9")
        status_label.pack(side=tk.LEFT, padx=5)

        self.subject_labels.append(label)
        self.subject_entries.append(entry)
        self.subject_status_labels.append(status_label)

    def remove_subject(self):
        if self.subject_entries:
            self.subject_entries.pop().destroy()
            self.subject_labels.pop().destroy()
            self.subject_status_labels.pop().destroy()
        else:
            messagebox.showinfo("Info", "No subjects to remove.")

    def calculate_grade(self):
        marks = []
        any_failed = False

        for i, (entry, status_label) in enumerate(zip(self.subject_entries, self.subject_status_labels)):
            value = entry.get()
            if value == "":
                messagebox.showwarning("Input Missing", f"Please enter marks for Subject {i+1}")
                return
            try:
                mark = float(value)
                if 0 <= mark <= 100:
                    marks.append(mark)
                    if mark < 40:
                        status_label.config(text="(Fail)", fg="red")
                        any_failed = True
                    else:
                        status_label.config(text="(Pass)", fg="green")
                else:
                    messagebox.showerror("Invalid Input", f"Subject {i+1} marks must be between 0 and 100")
                    return
            except ValueError:
                messagebox.showerror("Error", f"Invalid number in Subject {i+1}")
                return

        total = sum(marks)
        percentage = total / len(marks)

        if any_failed:
            grade = "Fail"
        elif percentage >= 90:
            grade = "A+"
        elif percentage >= 80:
            grade = "A"
        elif percentage >= 70:
            grade = "B"
        elif percentage >= 60:
            grade = "C"
        elif percentage >= 50:
            grade = "D"
        elif percentage >= 40:
            grade = "E"
        else:
            grade = "Fail"

        self.total_label.config(text=f"Total Marks: {total}")
        self.percentage_label.config(text=f"Percentage: {percentage:.2f}%")
        self.grade_label.config(text=f"Grade: {grade}", fg="red" if grade == "Fail" else "#1b5e20")

        self.total_label.pack()
        self.percentage_label.pack()
        self.grade_label.pack()

    def reset_fields(self):
        for entry in self.subject_entries:
            entry.delete(0, tk.END)
        for status_label in self.subject_status_labels:
            status_label.config(text="")
        self.total_label.config(text="")
        self.percentage_label.config(text="")
        self.grade_label.config(text="")

# ---------- RUN APP ----------
if __name__ == "__main__":
    root = tk.Tk()
    app = GradeCalculatorApp(root)
    root.mainloop()
