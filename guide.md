### **Instructions for Students to build a Mini Students Course Registration System**

You are tasked to build a database for a student registration system. The system allows the user to:

1. Add Courses
   
2. Add Students

4. Register Courses to students

5. Delete registrations

6. View all students and their individual registrations

The project will be built using PHP for scripting and MySQL for database.

Requirements: 

1. Wamp server installed on your computer
   *Wamp server provides you with the runtime for both PHP and MySQL*

3. A text editor, preferably Visual Studio Code.

Setup the project:

To set up your project you will:

1. Create the database

a. Login to phpMyAdmin (ensure wamp server is live, then go to "HTTP://localhost/phpmyadmin" on your browser)
  
b. log in to phpmyadmin with the user name "root", leaving the password field empty

c. On the left side of the page, the databases section click on new.

d. enter the database name "course_registration" and click on create.
  
3. Create the database tables.

   The project consists of three tables, Students, Courses and Registrations. To create the tables, click on your course_registration database, then click on SQL on the menu bar. You will be provided with a text input field.

   a. In the field, enter the SQL code below:

   ```
    USE course_registration;

    CREATE TABLE Students (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    registration_date DATE NOT NULL
    ); 
  
 Click on "Go"

 b. Repeat the process for the Courses table:
     
    USE course_registration;
       
    CREATE TABLE Courses (
    id INT AUTO_INCREMENT PRIMARY KEY,
    course_name VARCHAR(100) NOT NULL,
    course_code VARCHAR(10) UNIQUE NOT NULL
    );
      
  c. Repeat the process for the Registrations table:

    USE course_registration;
    
    CREATE TABLE Registrations (
    id INT AUTO_INCREMENT PRIMARY KEY,
    student_id INT,
    course_id INT,
    registration_date DATE,
    FOREIGN KEY (student_id) REFERENCES Students(id) ON DELETE CASCADE,
    FOREIGN KEY (course_id) REFERENCES Courses(id) ON DELETE CASCADE
    );

3. Write the scripts for the php pages.

   For your project to run, its folder must be in the 'www' folder that is a subfolder in your Wamp server installation folder. It is found in c:\wamp\ or c:\wamp64\

   a. Navigate to the www folder in your wamp installation folder and create a new folder called "Your_Name_DBMS" example *"Nusaiba_DBMS"*

   b. Inside the folder, create a file called index.php, paste the following code and save.
   ```
   <!DOCTYPE html>
    <html lang="en">
    <head>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width, initial-scale=1.0">
        <title>Course Registration System</title>
    </head>
    <body>
        <h1>Welcome to the Course Registration System</h1>
        <ul>
            <li><a href="add_student.php">Add Student</a></li>
            <li><a href="add_course.php">Add Course</a></li>
            <li><a href="register_course.php">Register Student for a Course</a></li>
            <li><a href="delete_registration.php">Delete Course Registration</a></li>
            <li><a href="view_students.php">View All Students</a></li>
        </ul>
    </body>
    </html>

c. create another file called add_course.php, paste the following code and save.
  ```
  <!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Add New Course</title>
</head>
<body>
    <h2>Add a New Course</h2>
    <form action="save_course.php" method="POST">
        <label for="course_name">Course Name:</label>
        <input type="text" id="course_name" name="course_name" required><br><br>

        <label for="course_code">Course Code:</label>
        <input type="text" id="course_code" name="course_code" required><br><br>

        <button type="submit">Add Course</button>
    </form>
</body>
</html>
```
d. create another file called add_student.php, paste the following code and save.

  ```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Add New Student</title>
</head>
<body>
    <h2>Add a New Student</h2>
    <form action="save_student.php" method="POST">
        <label for="name">Student Name:</label>
        <input type="text" id="name" name="name" required><br><br>

        <label for="email">Email:</label>
        <input type="email" id="email" name="email" required><br><br>

        <label for="date">Registration Date:</label>
        <input type="date" id="date" name="registration_date" required><br><br>

        <button type="submit">Add Student</button>
    </form>
</body>
</html>
```
e. create another file called register_course.php, paste the following code and save.
  ```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Register Student for Course</title>
</head>
<body>
    <h2>Register a Student for a Course</h2>
    <form action="save_registration.php" method="POST">
        <label for="student">Select Student:</label>
        <select name="student_id" id="student" required>
            <?php
            $conn = new mysqli("localhost", "root", "", "course_registration");
            $students = $conn->query("SELECT * FROM Students");
            while ($row = $students->fetch_assoc()) {
                echo "<option value='" . $row['id'] . "'>" . $row['name'] . "</option>";
            }
            ?>
        </select><br><br>

        <label for="course">Select Course:</label>
        <select name="course_id" id="course" required>
            <?php
            $courses = $conn->query("SELECT * FROM Courses");
            while ($row = $courses->fetch_assoc()) {
                echo "<option value='" . $row['id'] . "'>" . $row['course_name'] . "</option>";
            }
            ?>
        </select><br><br>

        <label for="date">Registration Date:</label>
        <input type="date" id="date" name="registration_date" required><br><br>

        <button type="submit">Register</button>
    </form>
</body>
</html> 
```
f. create another file called save_course.php, paste the following code and save.

  ```
  <?php
$servername = "localhost";
$username = "root";
$password = "";
$database = "course_registration";

// Database connection
$conn = new mysqli($servername, $username, $password, $database);

if ($conn->connect_error) {
    die("Connection failed: " . $conn->connect_error);
}

if ($_SERVER["REQUEST_METHOD"] == "POST") {
    $course_name = $_POST['course_name'];
    $course_code = $_POST['course_code'];

    $sql = "INSERT INTO Courses (course_name, course_code) VALUES (?, ?)";
    $stmt = $conn->prepare($sql);
    $stmt->bind_param("ss", $course_name, $course_code);

    if ($stmt->execute()) {
        echo "Course added successfully! <a href='add_course.php'>Add Another Course</a>";
    } else {
        echo "Error: " . $stmt->error;
    }

    $stmt->close();
    $conn->close();
}
?>
```
g. create another file called save_registration.php, paste the following code and save.
  ```
  <?php
$servername = "localhost";
$username = "root";
$password = "";
$database = "course_registration";

$conn = new mysqli($servername, $username, $password, $database);
if ($conn->connect_error) {
    die("Connection failed: " . $conn->connect_error);
}

if ($_SERVER["REQUEST_METHOD"] == "POST") {
    $student_id = $_POST['student_id'];
    $course_id = $_POST['course_id'];
    $registration_date = $_POST['registration_date'];

    $sql = "INSERT INTO Registrations (student_id, course_id, registration_date) VALUES (?, ?, ?)";
    $stmt = $conn->prepare($sql);
    $stmt->bind_param("iis", $student_id, $course_id, $registration_date);

    if ($stmt->execute()) {
        echo "Student successfully registered for the course! <a href='index.php'>Back to Home</a>";
    } else {
        echo "Error: " . $stmt->error;
    }

    $stmt->close();
    $conn->close();
}
?>
```
h. create another file called save_student.php, paste the following code and save.

  ```
  <?php
$servername = "localhost";
$username = "root";
$password = "";
$database = "course_registration";

// Database connection
$conn = new mysqli($servername, $username, $password, $database);
if ($conn->connect_error) {
    die("Connection failed: " . $conn->connect_error);
}

if ($_SERVER["REQUEST_METHOD"] == "POST") {
    $name = $_POST['name'];
    $email = $_POST['email'];
    $registration_date = $_POST['registration_date'];

    $sql = "INSERT INTO Students (name, email, registration_date) VALUES (?, ?, ?)";
    $stmt = $conn->prepare($sql);

    // Error Handling
    if (!$stmt) {
        die("SQL Error: " . $conn->error);
    }

    $stmt->bind_param("sss", $name, $email, $registration_date);

    if ($stmt->execute()) {
        echo "Student added successfully! <a href='index.php'>Back to Home</a>";
    } else {
        echo "Error: " . $stmt->error;
    }

    $stmt->close();
    $conn->close();
}
?>
```

i. create another file called view_students.php, paste the following code and save.

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>View All Students</title>
</head>
<body>
    <h2>All Students</h2>
    <table border="1">
        <tr>
            <th>ID</th>
            <th>Name</th>
            <th>Email</th>
            <th>Registration Date</th>
            <th>Action</th>
        </tr>
        <?php
        $conn = new mysqli("localhost", "root", "", "course_registration");
        if ($conn->connect_error) {
            die("Connection failed: " . $conn->connect_error);
        }

        $sql = "SELECT * FROM Students";
        $result = $conn->query($sql);

        if ($result->num_rows > 0) {
            while ($row = $result->fetch_assoc()) {
                echo "<tr>";
                echo "<td>" . $row['id'] . "</td>";
                echo "<td>" . $row['name'] . "</td>";
                echo "<td>" . $row['email'] . "</td>";
                echo "<td>" . $row['registration_date'] . "</td>";
                echo "<td><a href='view_registration.php?student_id=" . $row['id'] . "'>View Registrations</a></td>";
                echo "</tr>";
            }
        } else {
            echo "<tr><td colspan='5'>No students found</td></tr>";
        }
        $conn->close();
        ?>
    </table>
    <br>
    <a href="index.php">Back to Home</a>
</body>
</html>
```
j. create another file called view_registration.php, paste the following code and save.
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>View Student Registrations</title>
</head>
<body>
    <h2>Student Course Registrations</h2>
    <?php
    if (isset($_GET['student_id'])) {
        $student_id = $_GET['student_id'];

        $conn = new mysqli("localhost", "root", "", "course_registration");
        if ($conn->connect_error) {
            die("Connection failed: " . $conn->connect_error);
        }

        // Fetch student details
        $student_result = $conn->query("SELECT * FROM Students WHERE id = $student_id");
        $student = $student_result->fetch_assoc();
        echo "<h3>Student: " . $student['name'] . " (" . $student['email'] . ")</h3>";

        // Fetch course registrations
        $sql = "SELECT Courses.course_name, Courses.course_code, Registrations.registration_date FROM Registrations JOIN Courses ON Registrations.course_id = Courses.id WHERE Registrations.student_id = $student_id";
        $result = $conn->query($sql);

        if ($result->num_rows > 0) {
            echo "<table border='1'>";
            echo "<tr><th>Course Name</th><th>Course Code</th><th>Registration Date</th></tr>";
            while ($row = $result->fetch_assoc()) {
                echo "<tr>";
                echo "<td>" . $row['course_name'] . "</td>";
                echo "<td>" . $row['course_code'] . "</td>";
                echo "<td>" . $row['registration_date'] . "</td>";
                echo "</tr>";
            }
            echo "</table>";
        } else {
            echo "<p>No course registrations found for this student.</p>";
        }
        $conn->close();
    } else {
        echo "<p>Invalid student ID.</p>";
    }
    ?>
    <br>
    <a href="view_students.php">Back to All Students</a>
</body>
</html>
```
k. create another file called delete_registration.php, paste the following code and save.
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Delete Course Registration</title>
</head>
<body>
    <h2>Delete Course Registration</h2>
    <form action="remove_registration.php" method="POST">
        <label for="student">Select Student:</label>
        <select name="student_id" id="student" required>
            <?php
            $conn = new mysqli("localhost", "root", "", "course_registration");
            $students = $conn->query("SELECT * FROM Students");
            while ($row = $students->fetch_assoc()) {
                echo "<option value='" . $row['id'] . "'>" . $row['name'] . "</option>";
            }
            ?>
        </select><br><br>

        <label for="course">Select Course:</label>
        <select name="course_id" id="course" required>
            <?php
            $courses = $conn->query("SELECT * FROM Courses");
            while ($row = $courses->fetch_assoc()) {
                echo "<option value='" . $row['id'] . "'>" . $row['course_name'] . "</option>";
            }
            ?>
        </select><br><br>

        <button type="submit">Delete Registration</button>
    </form>
</body>
</html>
```
l. create another file called remove_registration.php, paste the following code and save.
```
<?php
$servername = "localhost";
$username = "root";
$password = "";
$database = "course_registration";

$conn = new mysqli($servername, $username, $password, $database);
if ($conn->connect_error) {
    die("Connection failed: " . $conn->connect_error);
}

if ($_SERVER["REQUEST_METHOD"] == "POST") {
    $student_id = $_POST['student_id'];
    $course_id = $_POST['course_id'];

    $sql = "DELETE FROM Registrations WHERE student_id = ? AND course_id = ?";
    $stmt = $conn->prepare($sql);
    $stmt->bind_param("ii", $student_id, $course_id);

    if ($stmt->execute()) {
        echo "Registration successfully deleted! <a href='index.php'>Back to Home</a>";
    } else {
        echo "Error: " . $stmt->error;
    }

    $stmt->close();
    $conn->close();
}
?>
```
 
4. Run and test the project.

   You now have your complete students registration system. In the process, you have successfully created a database and have created 3 tables for the database. You are already familiar with SQL.

   For the php files, you should not bother about understanding them now, just make sure you gave them the right name and paste the correct code for each of them.

   You will now run the project; if everything is done right, it will be perfectly executed.


   ### **Running your project**

   a. Launch any web browser on your computer and type the address "HTTP://localhost/Your_Name_DBMS/" and hit enter. *remember Your_Name_DBMS is the name of your project folder*

   b. If step a is successful, you will be in the home page of your registration system. A very basic system with links to 5 pages. Add course, Add student, register student for a course, delete course registration and view all students. You are to add 5 different students in the Add students page.

   c. Add any 10 of the courses you take in RUBK at the Add course page.

   d. For each of the 5 students you have added, register them for 8 courses.

   e. Click on the view all students page and explore, viewing the registration form for each student.

   f. Now you have done something awesome and beleive me, it is not magic.

   g. You will now go back to phpMyAdmin, click on your database and on the menu bar click on export, check that the format is SQL and click on Go.

   h. Go back to your registration system and go to the delete registration page. Here you will delete one course registration for each of the 5 students.

   i. Repeat step g.

   j. Go to your downloads folder, you will find 2 SQL files. course_registration and course_registration(1). You can open and have a look at them, you will see the text format of your database with the tables you created and the registrations you added.

** Congratulations !!! **

** Happy Coding **

** This is just the beginning **
