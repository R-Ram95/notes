---
tags:
  - Project-Notes
---
  

> [!important]  
> KanbanKanban - EarnNLearnNameStatusAssigned toType[[register page needs work]]DoneFE[[Make a Profile Page]]Donequenten FE[[Refactor header bar]]DoneRohinesh RamFE[[Add Authentication to FE]]DoneRohinesh RamFE[[Add Zod Schema Validation]]DoneRohinesh RamBE[[Add child functionality in profile select]]DoneBE[[JWT - Verification]]DoneBE[[Make chore API endpoints]]Donequenten BE[[Make Route for Logout]]DoneRohinesh RamBE[[Connect frontend to Chore API endpoints]]Donequenten BE[[Make another route to register children]]Donequenten BE[[Select BE Framework]]DoneRohinesh RamBE[[Front end - Child fetch]]DoneFE[[Select DB]]DoneBE[[Setup Controllers and Routes Folders]]DoneBE[[Select Authentication]]Donequenten BE[[Make get children BE]]DoneBE[[Setup Accounts page on FE]]Donequenten FE[[Setup Themes in MUI]]DoneRohinesh RamFE[[Remove Hamburger Menu for Children]]TODOFE[[Login Page]]In progressquenten FE[[BUG - date picker form validation in CreateChore]]TODOFE[[ProfileChores Page]]In progressRohinesh RamFE[[Child View of application]]TODOFE[[Transactions 2]]TODOFE[[Add Role for user on Login Route]]TODOBE[[Transactions]]TODOquenten Rohinesh RamBE[[Overdue chore]]TODOquenten BE[[Add logging for request and response]]TODOquenten BE[[JWT - Refresh Token]]TODORohinesh RamBE[[Refactor Profiles Page]]DoneRohinesh RamFE[[Refactor FE]]DoneRohinesh RamFE[[Register User Route]]DoneBE[[Create Chores Model in Prisma]]DoneBE[[BUG - Form validation, date picker, Create Chore]]TODO[[Untitled]]TODOquenten [[Register page needs work]]TODO  

  

- Tech Stack> [!important]  
    > Tech StackFE = ReactBE = ExpressJSDB = Firebase Firestore or Firebase Realtime DBAuthentications = ?Potential additions:Google SSO (Authentication with Clerk)email server - proton mail  
    
- Hosting Considerations
    
    **Firebase**
    
    > [!important]  
    > DatabaseFirebase features two databases - both are NoSQL (key-value)Firestore - Stores data as collections of documents (similar to MongoDB)Realtime Database - Stores data as one large JSON tree  
      
    > [!important]  
    > Backenduse CloudFunctions + ExpressJS to build BE APIs Need to be on Blaze plan but it is free up to certain limits:![[/Untitled 29.png|Untitled 29.png]]Add the Admin SDK to BE, this enables us to:implement custom Authmanage firebase authread/write to Realtime DB  
    
    **MongoDB**
    
    MongoDB - NoSQL database
    
    **PlanetScale**
    
    Planetscale - SQL
    
- Data Modelling
    
    **Objects:**
    
    Chores
    
    - assigned by parents
    - assigned to children
    
    Feedback
    
    - Submit feedback for babysitter
    - feedback notification/read for parents
    
    Users
    
    - Children
    - Parent
    - Baby Sitter
    
    Withdrawal
    
    - child makes request for withdrawal
    - parent gets notification
    
      
    
    > [!important]  
    > Database Designhttps://excalidraw.com/\#room=65c778e1751bc5365ba2,nrmXiL6cenhIVHdYyE_Yaw  
    
      
    
    **SQL Data models**
    
    ```JavaScript
    CREATE TABLE Users (
        id VARCHAR(255) PRIMARY KEY,
        is_parent BOOLEAN,
        is_child BOOLEAN,
        first_name VARCHAR(255),
        last_name VARCHAR(255),
        p_word VARCHAR(255),
        u_name VARCHAR(255) UNIQUE
    );
    
    CREATE TABLE Transactions (
        id VARCHAR(36) PRIMARY KEY,  -- 36 character UUID
        Child_ID VARCHAR(255),
        Title VARCHAR(255),
        Amount INT,
        Type BOOLEAN,
        timestamp DATE,
        Approved BOOLEAN,
        FOREIGN KEY (Child_ID) REFERENCES Users(id)
    );
    
    CREATE TABLE Chores (
        id VARCHAR(36) PRIMARY KEY,  -- 36 character UUID
        Child_ID VARCHAR(255),
        Title VARCHAR(255),
        Amount INT,
        timestamp DATE,
        Status VARCHAR(255),
        FOREIGN KEY (Child_ID) REFERENCES Users(id)
    );
    ```
    
- Auth
    
    - use JWT method (method 4)
        
        > [!info] Everything You Need To Know About Frontend And Backend Authentication: Ultimate Guide  
        > Ever been asked about frontend authentication in an interview and felt completely lost?  
        > [https://zivukushingai.medium.com/everything-you-need-to-know-about-frontend-and-backend-authentication-ultimate-guide-7142a752249c](https://zivukushingai.medium.com/everything-you-need-to-know-about-frontend-and-backend-authentication-ultimate-guide-7142a752249c)  
        
        ![[/Untitled 1 17.png|Untitled 1 17.png]]
        
    
      
    
    > [!important]  
    > PHP Solution - DOES NOT USE JWT<?php  
    session_start();  
    $dbHost = getenv('DB_HOST');  
    $dbUser = getenv('DB_USER');  
    $dbPassword = getenv('DB_PASSWORD');  
    $dbName = getenv('DB_NAME');  
      
    $conn = new mysqli($dbHost, $dbUser, $dbPassword, $dbName);  
      
    if ($conn->connect_error) {  
    die("Connection failed: " . $conn->connect_error);  
    }  
      
    if ($_SERVER["REQUEST_METHOD"] == "POST") {  
    $username = $conn->real_escape_string($_POST['username']);  
    $password = $_POST['password']; // Ensure this variable is defined  
      
    $sql = "SELECT id, password, is_admin FROM users WHERE username = '$username'";  
    $result = $conn->query($sql);  
      
    if ($result->num_rows > 0) {  
    $row = $result->fetch_assoc();  
    if (password_verify($password, $row['password'])) {  
    $_SESSION['user_id'] = $row['id'];  
    $_SESSION['is_admin'] = $row['is_admin'];  
    // Redirect based on user role  
    if ($row['is_admin']) {  
    header("Location: admin.php");  
    } else {  
    header("Location: dashboard.php");  
    }  
    exit();  
    }  
    } else {  
    echo "Invalid username or password";  
    }  
      
    $conn->close();  
    }  
    ?>  
    
- FE Refactor
    - header bar
    - ProfileSelect
        - show skeleton loading state for lists
        - move to use styled components
        - add profile should show a modal with the form to create a new user
        - pass child id to Profile
    - Profile
        - renders two components:
            - toggle switch to switch between components
            - re-render the progress bar with a nice animation
            - one for chores
                - list the chores in a list
                - add sorting for due-date?
                
            - one for transactions
            - this eliminates the need to have the child id in the query param
    - redeem-request
        - this should be a modal
    - chore creation
        - this should be a modal