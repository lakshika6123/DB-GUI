#pip install mysql-connector psycopg2 cx_Oracle
import tkinter as tk
from tkinter import messagebox
import psycopg2
import mysql.connector
import cx_Oracle

# Simulating credentials for the connections (Replace with your actual credentials)
credentials = {
    "Postgres": {
        "host": "localhost",
        "user": "your_postgres_user",
        "password": "your_postgres_password",
        "database": "postgres_db"
    },
    "MySQL": {
        "host": "localhost",
        "user": "your_mysql_user",
        "password": "your_mysql_password",
        "database": "mysql_db"
    },
    "Oracle": {
        "host": "localhost",
        "user": "your_oracle_user",
        "password": "your_oracle_password",
        "database": "oracle_db"
    }
}

class DatabaseApp:
    def __init__(self, root):
        self.root = root
        self.root.title("Database App")
        self.root.geometry("400x400")

        # Database type selection
        self.db_label = tk.Label(self.root, text="Select Database Type:")
        self.db_label.pack(pady=10)

        self.db_var = tk.StringVar()
        self.db_dropdown = tk.OptionMenu(self.root, self.db_var, "Postgres", "MySQL", "Oracle")
        self.db_var.set("Postgres")
        self.db_dropdown.pack(pady=10)

        # Username and password fields
        self.username_label = tk.Label(self.root, text="Username:")
        self.username_label.pack(pady=5)

        self.username_entry = tk.Entry(self.root)
        self.username_entry.pack(pady=5)

        self.password_label = tk.Label(self.root, text="Password:")
        self.password_label.pack(pady=5)

        self.password_entry = tk.Entry(self.root, show="*")
        self.password_entry.pack(pady=5)

        # Connect button
        self.connect_button = tk.Button(self.root, text="Connect", command=self.connect_to_database)
        self.connect_button.pack(pady=20)

        # Status label
        self.status_label = tk.Label(self.root, text="", fg="green")
        self.status_label.pack(pady=10)

        # Import and Export buttons
        self.import_button = tk.Button(self.root, text="Import Data", command=self.import_data, state="disabled")
        self.import_button.pack(pady=10)

        self.export_button = tk.Button(self.root, text="Export Data", command=self.export_data, state="disabled")
        self.export_button.pack(pady=10)

    def connect_to_database(self):
        db_type = self.db_var.get()
        username = self.username_entry.get()
        password = self.password_entry.get()

        # Update the status
        self.status_label.config(text=f"Connecting to {db_type}...", fg="blue")

        # Attempt to connect to the selected database
        connection = None
        if db_type == "Postgres":
            connection = self.connect_postgres(username, password)
        elif db_type == "MySQL":
            connection = self.connect_mysql(username, password)
        elif db_type == "Oracle":
            connection = self.connect_oracle(username, password)

        if connection:
            self.status_label.config(text=f"Connected to {db_type}!", fg="green")
            self.import_button.config(state="normal")
            self.export_button.config(state="normal")
        else:
            self.status_label.config(text=f"Failed to connect to {db_type}.", fg="red")

    def connect_postgres(self, username, password):
        try:
            conn = psycopg2.connect(
                host=credentials["Postgres"]["host"],
                user=username,
                password=password,
                dbname=credentials["Postgres"]["database"]
            )
            return conn
        except Exception as e:
            messagebox.showerror("Connection Error", f"Failed to connect to PostgreSQL: {e}")
            return None

    def connect_mysql(self, username, password):
        try:
            conn = mysql.connector.connect(
                host=credentials["MySQL"]["host"],
                user=username,
                password=password,
                database=credentials["MySQL"]["database"]
            )
            return conn
        except Exception as e:
            messagebox.showerror("Connection Error", f"Failed to connect to MySQL: {e}")
            return None

    def connect_oracle(self, username, password):
        try:
            dsn = cx_Oracle.makedsn(credentials["Oracle"]["host"], 1521, credentials["Oracle"]["database"])
            conn = cx_Oracle.connect(username, password, dsn)
            return conn
        except Exception as e:
            messagebox.showerror("Connection Error", f"Failed to connect to Oracle: {e}")
            return None

    def import_data(self):
        # For demonstration purposes, show an alert
        messagebox.showinfo("Import", "Importing data...")

    def export_data(self):
        # For demonstration purposes, show an alert
        messagebox.showinfo("Export", "Exporting data...")

# Create the Tkinter window
root = tk.Tk()
app = DatabaseApp(root)

# Run the Tkinter event loop
root.mainloop()
