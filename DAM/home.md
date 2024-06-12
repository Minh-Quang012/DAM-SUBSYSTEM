**Schema (MySQL v8.0)**

    CREATE TABLE User (
        user_id INT PRIMARY KEY,
        username VARCHAR(255),
        password VARCHAR(255),
        email VARCHAR(255),
        role VARCHAR(50)
    );
    
    -- Insert into User table
    INSERT INTO User (user_id, username, password, email, role) 
    VALUES 
    (1, 'john_doe', 'password123', 'john.doe@example.com', 'admin'),
    (2, 'jane_smith', 'securepass', 'jane.smith@example.com', 'user'),
    (3, 'alice_jones', 'mypassword', 'alice.jones@example.com', 'user');
    
    CREATE TABLE Asset (
        asset_id INT PRIMARY KEY,
        asset_name VARCHAR(255),
        asset_type VARCHAR(255),
        asset_size INT,
        asset_path VARCHAR(255),
        owner_id INT,
        created_at DATETIME,
        updated_at DATETIME,
        FOREIGN KEY (owner_id) REFERENCES User(user_id)
    );
    
    -- Insert into Asset table
    INSERT INTO Asset (asset_id, asset_name, asset_type, asset_size, asset_path, owner_id, created_at, updated_at) 
    VALUES 
    (1, 'Asset 1', 'image', 1024, '/path/to/asset1.jpg', 1, '2024-01-01 10:00:00', '2024-01-01 10:00:00'),
    (2, 'Asset 2', 'video', 20480, '/path/to/asset2.mp4', 2, '2024-02-01 11:00:00', '2024-02-01 11:00:00'),
    (5, 'Asset 23', 'image', 1025, '/path/to/asset1.jpg', 1, '2024-01-01 10:00:00', '2024-01-01 10:00:00'),
    (3, 'Asset 3', 'document', 512, '/path/to/asset3.pdf', 3, '2024-03-01 12:00:00', '2024-03-01 12:00:00');
    
    CREATE TABLE Sharing (
        sharing_id INT PRIMARY KEY,
        asset_id INT,
        share_with INT,
        share_by INT,
        permission VARCHAR(50),
        share_time DATETIME,
        FOREIGN KEY (asset_id) REFERENCES Asset(asset_id),
        FOREIGN KEY (share_with) REFERENCES User(user_id),
        FOREIGN KEY (share_by) REFERENCES User(user_id)
    );
    
    -- Insert into Sharing table
    INSERT INTO Sharing (sharing_id, asset_id, share_with, share_by, permission, share_time) 
    VALUES 
    (1, 1, 2, 1, 'read', '2024-01-02 10:00:00'),
    (2, 2, 3, 2, 'edit', '2024-02-02 11:00:00'),
    (3, 3, 1, 3, 'read', '2024-03-02 12:00:00');
    
    CREATE TABLE Storage (
        storage_id INT PRIMARY KEY,
        used_capacity INT,
        available_capacity INT,
        user_id INT,
        FOREIGN KEY (user_id) REFERENCES User(user_id)
    );
    
    -- Insert into Storage table
    INSERT INTO Storage (storage_id, used_capacity, available_capacity, user_id) 
    VALUES 
    (1, 1000, 9000, 1),
    (2, 5000, 5000, 2),
    (3, 2000, 8000, 3);
    
    CREATE TABLE Folder (
        folder_id INT PRIMARY KEY,
        owner_id INT,
        asset_id INT,
        created_at DATETIME,
        updated_at DATETIME,
        FOREIGN KEY (owner_id) REFERENCES User(user_id),
        FOREIGN KEY (asset_id) REFERENCES Asset(asset_id)
    );
    
    -- Insert into Folder table
    INSERT INTO Folder (folder_id, owner_id, asset_id, created_at, updated_at) 
    VALUES 
    (1, 1, 1, '2024-01-01 10:00:00', '2024-01-01 10:00:00'),
    (2, 2, 2, '2024-02-01 11:00:00', '2024-02-01 11:00:00'),
    (3, 3, 3, '2024-03-01 12:00:00', '2024-03-01 12:00:00');
    
    
    
    
    
    
    
    
    
    

---

**Query #1**

    SELECT * 
    FROM Asset 
    WHERE owner_id = 1;

| asset_id | asset_name | asset_type | asset_size | asset_path          | owner_id | created_at          | updated_at          |
| -------- | ---------- | ---------- | ---------- | ------------------- | -------- | ------------------- | ------------------- |
| 1        | Asset 1    | image      | 1024       | /path/to/asset1.jpg | 1        | 2024-01-01 10:00:00 | 2024-01-01 10:00:00 |
| 5        | Asset 23   | image      | 1025       | /path/to/asset1.jpg | 1        | 2024-01-01 10:00:00 | 2024-01-01 10:00:00 |

---
**Query #2** 

    Select * from Folder;

| folder_id | owner_id | asset_id | created_at          | updated_at          |
| --------- | -------- | -------- | ------------------- | ------------------- |
| 1         | 1        | 1        | 2024-01-01 10:00:00 | 2024-01-01 10:00:00 |
| 2         | 2        | 2        | 2024-02-01 11:00:00 | 2024-02-01 11:00:00 |
| 3         | 3        | 3        | 2024-03-01 12:00:00 | 2024-03-01 12:00:00 |

---
**Query #3**

    SELECT DISTINCT u.*
    FROM User u
    JOIN Sharing s ON u.user_id = s.share_with;

| user_id | username    | password    | email                   | role  |
| ------- | ----------- | ----------- | ----------------------- | ----- |
| 1       | john_doe    | password123 | john.doe@example.com    | admin |
| 2       | jane_smith  | securepass  | jane.smith@example.com  | user  |
| 3       | alice_jones | mypassword  | alice.jones@example.com | user  |

---
**Query #4**

    select * from Sharing;

| sharing_id | asset_id | share_with | share_by | permission | share_time          |
| ---------- | -------- | ---------- | -------- | ---------- | ------------------- |
| 1          | 1        | 2          | 1        | read       | 2024-01-02 10:00:00 |
| 2          | 2        | 3          | 2        | edit       | 2024-02-02 11:00:00 |
| 3          | 3        | 1          | 3        | read       | 2024-03-02 12:00:00 |

---
**Query #5**

    select * from Asset 
    Where Asset_type = "image";

| asset_id | asset_name | asset_type | asset_size | asset_path          | owner_id | created_at          | updated_at          |
| -------- | ---------- | ---------- | ---------- | ------------------- | -------- | ------------------- | ------------------- |
| 1        | Asset 1    | image      | 1024       | /path/to/asset1.jpg | 1        | 2024-01-01 10:00:00 | 2024-01-01 10:00:00 |
| 5        | Asset 23   | image      | 1025       | /path/to/asset1.jpg | 1        | 2024-01-01 10:00:00 | 2024-01-01 10:00:00 |

---

[View on DB Fiddle](https://www.db-fiddle.com/f/2wx9iHwK9qujPMGLRKaDic/3)
---

[View on DB Fiddle](https://www.db-fiddle.com/f/2wx9iHwK9qujPMGLRKaDic/2)
