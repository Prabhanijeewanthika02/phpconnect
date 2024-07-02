# phpconnect
Sure! Here’s a comprehensive `README.md` file that includes all the necessary details for setting up and using your login system with PHP and MySQL.

```markdown
# Login System with PHP and MySQL

This project is a simple login system that connects to a MySQL database to verify user credentials. It includes a PHP script for handling the login logic and an HTML form for user input.

## Requirements

- PHP 7 or higher
- MySQL
- A web server (e.g., XAMPP, WAMP, or similar)

## Setup

### Step 1: Set Up the Database

1. **Create the Database:**

   Open your database management tool (like phpMyAdmin if you're using XAMPP or WAMP) and create a new database named `woodn_wonders`.

   ```sql
   CREATE DATABASE woodn_wonders;
   ```

2. **Create the Table:**

   Select the `woodn_wonders` database and create a table named `signup` with columns for `id`, `email`, and `password`.

   ```sql
   CREATE TABLE signup (
       id INT AUTO_INCREMENT PRIMARY KEY,
       email VARCHAR(255) NOT NULL,
       password VARCHAR(255) NOT NULL
   );
   ```

3. **Insert Sample Data:**

   Insert a sample user into the `signup` table. Make sure to hash the password using the `password_hash` function in PHP before inserting it into the database.

   Save the following PHP script as `insert_user.php`:

   ```php
   <?php
   $conn = new mysqli("localhost", "root", "", "woodn_wonders");

   if ($conn->connect_error) {
       die("Connection failed: " . $conn->connect_error);
   }

   $email = "user@example.com";
   $password = "password123";
   $hashed_password = password_hash($password, PASSWORD_DEFAULT);

   $stmt = $conn->prepare("INSERT INTO signup (email, password) VALUES (?, ?)");
   $stmt->bind_param("ss", $email, $hashed_password);

   if ($stmt->execute()) {
       echo "User inserted successfully.";
   } else {
       echo "Error: " . $stmt->error;
   }

   $stmt->close();
   $conn->close();
   ?>
   ```

   Run the script by navigating to `http://localhost/your_project_folder/insert_user.php` in your browser. This will insert a sample user into the database. You can delete this file after running it.

### Step 2: PHP Script for Login

Save the following PHP script in a file named `login.php` within your web server's directory (e.g., `htdocs` for XAMPP or `www` for WAMP).

```php
<?php

if ($_SERVER["REQUEST_METHOD"] == "POST") {
    $email = $_POST['email'];
    $password = $_POST['password'];

    // Database connection
    $conn = new mysqli("localhost", "root", "", "woodn_wonders");

    // Check connection
    if ($conn->connect_error) {
        die("Connection failed: " . $conn->connect_error);
    }

    // Prepare and bind
    $stmt = $conn->prepare("SELECT password FROM signup WHERE email = ?");
    if ($stmt === false) {
        die("Prepare failed: " . $conn->error);
    }
    $stmt->bind_param("s", $email);

    // Execute statement
    if ($stmt->execute() === false) {
        die("Execute failed: " . $stmt->error);
    }

    // Bind result variables
    $stmt->bind_result($loginpw);

    // Fetch value
    if ($stmt->fetch()) {
        if (password_verify($password, $loginpw)) {
            echo "Welcome!!";
        } else {
            echo "Login failed";
        }
    } else {
        echo "Login failed";
    }

    // Close statement and connection
    $stmt->close();
    $conn->close();
} else {
    echo "Please submit the form.";
}
?>
```

### Step 3: HTML Form

Save the following HTML form in a file named `index.html` (or any other suitable name) in the same directory.

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Customer Details Form</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #f0f0f0;
            margin: 0;
            padding: 20px;
        }
        .container {
            max-width: 600px;
            margin: auto;
            background-color: #fff;
            padding: 20px;
            border-radius: 8px;
            box-shadow: 0 0 10px rgba(0,0,0,0.1);
        }
        h2 {
            text-align: center;
            margin-bottom: 20px;
        }
        form {
            max-width: 400px;
            margin: auto;
        }
        label {
            display: block;
            margin-bottom: 8px;
        }
        input[type="email"], input[type="password"] {
            width: 100%;
            padding: 8px;
            margin-bottom: 15px;
            border: 1px solid #ccc;
            border-radius: 4px;
            box-sizing: border-box;
            font-size: 14px;
        }
        input[type="submit"] {
            background-color: #4CAF50;
            color: white;
            padding: 10px 15px;
            border: none;
            border-radius: 4px;
            cursor: pointer;
            font-size: 14px;
        }
        input[type="submit"]:hover {
            background-color: #45a049;
        }
    </style>
</head>
<body>

<div class="container">
    <h2>Sign Up</h2>
    <form method="post" action="login.php">
        <label for="email">Email:</label>
        <input type="email" id="email" name="email" required>

        <label for="password">Password:</label>
        <input type="password" id="password" name="password" required>

        <input type="submit" value="Submit">
        <a href="home.html">Home</a>
    </form>
</div>

</body>
</html>
```


### Testing

1. Start your web server (e.g., XAMPP, WAMP, or another server).
2. Open your web browser and navigate to `http://localhost/your_project_folder/index.html`.
3. Fill out the form and submit it to see the login functionality in action.

This setup ensures a secure connection to the database and proper handling of login functionality using PHP and MySQL.
```
