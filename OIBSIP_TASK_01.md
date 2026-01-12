# ===============================
# COMPLETE BMI CALCULATOR (COLAB)
# ===============================

import csv
import os
import matplotlib.pyplot as plt
from datetime import datetime

DATA_FILE = "bmi_data.csv"

# -------------------------------
# BMI Calculation Function
# -------------------------------
def calculate_bmi(weight, height):
    return weight / (height ** 2)

# -------------------------------
# BMI Category Function
# -------------------------------
def get_bmi_category(bmi):
    if bmi < 18.5:
        return "Underweight"
    elif bmi < 25:
        return "Normal weight"
    elif bmi < 30:
        return "Overweight"
    else:
        return "Obese"

# -------------------------------
# Input Validation Function
# -------------------------------
def get_valid_input(prompt, min_val, max_val):
    while True:
        try:
            value = float(input(prompt))
            if min_val <= value <= max_val:
                return value
            else:
                print(f"❌ Enter a value between {min_val} and {max_val}")
        except ValueError:
            print("❌ Invalid input. Please enter a number.")

# -------------------------------
# Save Data Function
# -------------------------------
def save_data(weight, height, bmi, category):
    file_exists = os.path.isfile(DATA_FILE)
    with open(DATA_FILE, "a", newline="") as file:
        writer = csv.writer(file)
        if not file_exists:
            writer.writerow(["Date", "Weight(kg)", "Height(m)", "BMI", "Category"])
        writer.writerow([
            datetime.now().strftime("%Y-%m-%d %H:%M"),
            weight,
            height,
            round(bmi, 2),
            category
        ])

# -------------------------------
# Load BMI History
# -------------------------------
def load_bmi_history():
    bmi_values = []
    dates = []
    try:
        with open(DATA_FILE, "r") as file:
            reader = csv.DictReader(file)
            for row in reader:
                bmi_values.append(float(row["BMI"]))
                dates.append(row["Date"])
    except FileNotFoundError:
        print("⚠ No previous data found.")
    return dates, bmi_values

# -------------------------------
# Plot BMI History
# -------------------------------
def plot_bmi_history(dates, bmi_values):
    if not bmi_values:
        print("⚠ No data to visualize.")
        return

    plt.figure(figsize=(8, 4))
    plt.plot(bmi_values, marker='o')
    plt.xticks(range(len(dates)), dates, rotation=45, ha='right')
    plt.xlabel("Date")
    plt.ylabel("BMI")
    plt.title("BMI History")
    plt.tight_layout()
    plt.show()

# ===============================
# MAIN PROGRAM
# ===============================
print("====== BMI CALCULATOR ======")

weight = get_valid_input("Enter your weight (kg): ", 10, 300)
height = get_valid_input("Enter your height (m): ", 0.5, 2.5)

bmi = calculate_bmi(weight, height)
category = get_bmi_category(bmi)

print("\n--- BMI RESULT ---")
print(f"BMI Value : {bmi:.2f}")
print(f"Category  : {category}")

# Save data
save_data(weight, height, bmi, category)
print("\n✅ Data saved successfully.")

# Show BMI history
choice = input("\nDo you want to see BMI history graph? (yes/no): ").lower()
if choice == "yes":
    dates, bmi_values = load_bmi_history()
    plot_bmi_history(dates, bmi_values)

print("\nThank you for using the BMI Calculator!")
