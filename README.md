# Income and Expense Survey Tool

## Project Overview

This project is a comprehensive survey tool developed to collect and analyze participant data regarding income and spending habits. The primary objective is to gather insights for a new product launch in the healthcare industry. The tool comprises a Flask web application for data collection, MongoDB for data storage, and Python for data processing and visualization. The final application is hosted on AWS.

## Table of Contents

1. [Installation](#installation)
2. [Configuration](#configuration)
3. [Usage](#usage)
4. [Data Processing](#data-processing)
5. [Data Visualization](#data-visualization)
6. [Deployment](#deployment)
7. [Project Structure](#project-structure)

## Installation

### Prerequisites

- Python 3.6+
- MongoDB
- pip (Python package installer)
- Virtualenv
- AWS account

### Step-by-Step Installation

1. **Clone the repository:**
   ```bash
   git clone https://github.com/your-username/income-expense-survey-tool.git
   cd income-expense-survey-tool
   ```

2. **Set up a virtual environment:**
   ```bash
   virtualenv venv
   source venv/bin/activate
   ```

3. **Install the dependencies:**
   ```bash
   pip install -r requirements.txt
   ```

4. **Start MongoDB service:**
   ```bash
   sudo service mongod start
   ```

5. **Run the Flask application:**
   ```bash
   flask run
   ```

## Configuration

### Flask Configuration

- The Flask application is configured to connect to a local MongoDB instance by default. If you need to change the MongoDB URI, you can do so in the `app.py` file:
  ```python
  client = pymongo.MongoClient("mongodb://localhost:27017/")
  ```

## Usage

### Accessing the Web Application

1. Open your web browser and navigate to `http://127.0.0.1:5000`.
2. Fill out the survey form with your details (Age, Gender, Total Income, and Expenses).
3. Submit the form to store the data in MongoDB.

## Data Processing

### User Class

The `User` class is used to process the collected data and store it in a CSV file.

```python
import csv

class User:
    def __init__(self, age, gender, total_income, expenses):
        self.age = age
        self.gender = gender
        self.total_income = total_income
        self.expenses = expenses

    def to_dict(self):
        return {
            "age": self.age,
            "gender": self.gender,
            "total_income": self.total_income,
            "utilities": self.expenses.get("utilities", 0),
            "entertainment": self.expenses.get("entertainment", 0),
            "school_fees": self.expenses.get("school_fees", 0),
            "shopping": self.expenses.get("shopping", 0),
            "healthcare": self.expenses.get("healthcare", 0)
        }

def save_to_csv(users, filename='user_data.csv'):
    keys = users[0].to_dict().keys()
    with open(filename, 'w', newline='') as output_file:
        dict_writer = csv.DictWriter(output_file, fieldnames=keys)
        dict_writer.writeheader()
        dict_writer.writerows([user.to_dict() for user in users])
```

### Fetching Data and Saving to CSV

```python
def fetch_data():
    users = []
    for user_data in collection.find():
        user = User(
            user_data['age'],
            user_data['gender'],
            user_data['total_income'],
            user_data['expenses']
        )
        users.append(user)
    return users

users = fetch_data()
save_to_csv(users)
```

## Data Visualization

### Jupyter Notebook

1. **Load Data:**
   ```python
   import pandas as pd

   df = pd.read_csv('user_data.csv')
   df.head()
   ```

2. **Perform Visualizations:**
   ```python
   import matplotlib.pyplot as plt
   import seaborn as sns

   # Show the ages with the highest income
   plt.figure(figsize=(10, 6))
   sns.barplot(data=df, x='age', y='total_income', ci=None)
   plt.title('Ages with the Highest Income')
   plt.xlabel('Age')
   plt.ylabel('Total Income')
   plt.xticks(rotation=45)
   plt.show()

   # Show the gender distribution across spending categories
   expense_categories = ['utilities', 'entertainment', 'school_fees', 'shopping', 'healthcare']
   df_melted = df.melt(id_vars=['gender'], value_vars=expense_categories, var_name='category', value_name='amount')

   plt.figure(figsize=(10, 6))
   sns.boxplot(data=df_melted, x='category', y='amount', hue='gender')
   plt.title('Gender Distribution Across Spending Categories')
   plt.xlabel('Expense Category')
   plt.ylabel('Amount Spent')
   plt.xticks(rotation=45)
   plt.show()
   ```

3. **Export Charts:**
   ```python
   plt.savefig('ages_highest_income.png')
   plt.savefig('gender_spending_distribution.png')
   ```

## Deployment

### Deploying on AWS

1. **Launch an EC2 Instance:**
   - Go to the EC2 Dashboard and launch a new instance.
   - Choose an Amazon Machine Image (AMI) like Ubuntu Server.
   - Configure instance details, add storage, and configure security group to allow HTTP (port 80).

2. **Connect to EC2 Instance:**
   ```bash
   ssh -i "your-key-pair.pem" ubuntu@your-ec2-public-ip
   ```

3. **Install Required Packages:**
   ```bash
   sudo apt update
   sudo apt install python3-pip python3-dev nginx
   sudo pip3 install virtualenv
   ```

4. **Deploy Flask App:**
   - Transfer your Flask app files to the EC2 instance.
   - Set up a virtual environment and install dependencies.
   - Configure Nginx as a reverse proxy.

## Project Structure

```
income-expense-survey-tool/
│
├── app.py
├── templates/
│   └── index.html
├── static/
├── user_class.py
├── data_processing.py
├── requirements.txt
├── README.md
└── user_data.csv
```

- `app.py`: Main Flask application file.
- `templates/index.html`: HTML template for the survey form.
- `user_class.py`: Defines the `User` class.
- `data_processing.py`: Script for fetching data from MongoDB and saving it to CSV.
- `requirements.txt`: Lists the Python dependencies.
- `user_data.csv`: CSV file containing the collected user data.

---

Make sure to customize the repository URL, EC2 instance details, and any other specific information before using this README.
