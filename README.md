# internship

STEP1:
**Javascript**:-
document.getElementById("loginForm").addEventListener("submit", function(event) {
    event.preventDefault();

    var username = document.getElementById("username").value;
    var password = document.getElementById("password").value;

    // Perform a query to check if the provided credentials are valid
    Auth.findOne({ where: { username: username, password: password } })
        .then(function(user) {
            // Check if the query returned any rows
            if (user) {
                // Authentication successful
                if (user.username === "admin") {
                    // Admin logged in, show data view
                    document.getElementById("customerForm").style.display = "none";
                    document.getElementById("dataView").style.display = "block";

                    // Retrieve customer data
                    Customer.findAll().then(function(customers) {
                        var customerTable = document.getElementById("customerTable");

                        // Clear existing table rows
                        while (customerTable.rows.length > 1) {
                            customerTable.deleteRow(1);
                        }

                        // Add customer data to the table
                        customers.forEach(function(customer) {
                            var row = customerTable.insertRow();
                            var idCell = row.insertCell();
                            idCell.textContent = customer.id;
                            var nameCell = row.insertCell();
                            nameCell.textContent = customer.name;
                            var emailCell = row.insertCell();
                            emailCell.textContent = customer.email;
                        });
                    });
                } else {
                    // Customer logged in, show customer form
                    document.getElementById("customerForm").style.display = "block";
                    document.getElementById("dataView").style.display = "none";

                    // Submit form
                    document.getElementById("submitForm").addEventListener("submit", function(event) {
                        event.preventDefault();

                        var name = document.getElementById("name").value;
                        var email = document.getElementById("email").value;

                        // Create a new customer record
                        Customer.create({ name: name, email: email })
                            .then(function(customer) {
                                console.log("Form submitted successfully for customer: " + customer.name);
                                document.getElementById("submitForm").reset();
                            });
                    });
                }
            } else {
                // Authentication failed
                console.log("Invalid username or password.");
            }
        });
});

**HTML:-**
<!DOCTYPE html>
<html>
<head>
    <title>Login Form</title>
</head>
<body>
    <h2>Login Form</h2>
    <form method="post" action="login.js">
        <label for="username">Username:</label>
        <input type="text" id="username" name="username" required><br><br>

        <label for="password">Password:</label>
        <input type="password" id="password" name="password" required><br><br>

        <input type="submit" value="Login">
    </form>
</body>
</html>

**STEP2:-**
const db = require('./db'); // Include the Sequelize configuration

const Auth = require('./models/Auth');
const Customer = require('./models/Customer');

// Retrieve the values submitted in the login form
const username = req.body.username;
const password = req.body.password;

// Perform a query to check if the provided credentials are valid
Auth.findOne({ where: { username: username, password: password } })
    .then((user) => {
        // Check if the query returned any rows
        if (user) {
            // Authentication successful
            if (user.username === 'admin') {
                // Admin logged in, show data view
                Customer.findAll()
                    .then((customers) => {
                        // Display data for admin
                        let dataView = "<h2>Data View</h2>";
                        dataView += "<table>";
                        dataView += "<tr><th>ID</th><th>Name</th><th>Email</th></tr>";
                        customers.forEach((customer) => {
                            dataView += "<tr>";
                            dataView += "<td>" + customer.id + "</td>";
                            dataView += "<td>" + customer.name + "</td>";
                            dataView += "<td>" + customer.email + "</td>";
                            dataView += "</tr>";
                        });
                        dataView += "</table>";
                        console.log(dataView);
                    });
            } else {
                // Customer logged in, show form
                let customerForm = "<h2>Customer Form</h2>";
                customerForm += "<form method='post' action='submit_form.php'>";
                customerForm += "<label for='name'>Name:</label>";
                customerForm += "<input type='text' id='name' name='name' required><br><br>";
                customerForm += "<label for='email'>Email:</label>";
                customerForm += "<input type='email' id='email' name='email' required><br><br>";
                customerForm += "<input type='hidden' name='username' value='" + user.username + "'>";
                customerForm += "<input type='submit' value='Submit'>";
                customerForm += "</form>";
                console.log(customerForm);
            }
        } else {
            // Authentication failed
            console.log("Invalid username or password.");
        }
    });

**STEP3:-**
const db = require('./db'); // Include the Sequelize configuration

const Customer = require('./models/Customer');

// Retrieve the values submitted in the form
const name = req.body.name;
const email = req.body.email;
const username = req.body.username;

// Create a new customer record using Sequelize
Customer.create({ name: name, email: email })
    .then((customer) => {
        console.log(`Form submitted successfully for customer: ${customer.name}`);
    });

