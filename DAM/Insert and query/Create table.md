```sql
USE DamSystem;
GO

-- Create User table
DROP TABLE IF EXISTS [User];
CREATE TABLE [User] (
    user_id INT PRIMARY KEY IDENTITY(1,1),
    username NVARCHAR(50) NOT NULL,
    password NVARCHAR(50) NOT NULL,
    email NVARCHAR(100) NOT NULL,
    role NVARCHAR(50) NOT NULL
);
GO

-- Create Asset table
DROP TABLE IF EXISTS Asset;
CREATE TABLE Asset (
    asset_id INT PRIMARY KEY IDENTITY(1,1),
    asset_name NVARCHAR(100) NOT NULL,
    asset_type_id INT NOT NULL,
    asset_size FLOAT NOT NULL,
    asset_path NVARCHAR(255) NOT NULL,
    owner_id INT NOT NULL,
    created_at DATETIME NOT NULL DEFAULT GETDATE(),
    updated_at DATETIME NULL,
    FOREIGN KEY (owner_id) REFERENCES [User](user_id)
);
GO

-- Create Sharing table
DROP TABLE IF EXISTS Sharing;
CREATE TABLE Sharing (
    sharing_id INT PRIMARY KEY IDENTITY(1,1),
    asset_id INT NOT NULL,
    share_with NVARCHAR(100) NOT NULL,
    owner_id INT NOT NULL,
    permission NVARCHAR(50) NOT NULL,
    share_time DATETIME NOT NULL DEFAULT GETDATE(),
    FOREIGN KEY (asset_id) REFERENCES Asset(asset_id),
    FOREIGN KEY (owner_id) REFERENCES [User](user_id)
);
GO

-- Create Storage table
DROP TABLE IF EXISTS Storage;
CREATE TABLE Storage (
    storage_id INT PRIMARY KEY IDENTITY(1,1),
    used_capacity FLOAT NOT NULL,
    available_capacity FLOAT NOT NULL,
    user_id INT NOT NULL,
    FOREIGN KEY (user_id) REFERENCES [User](user_id)
);
GO

-- Create Folder table
DROP TABLE IF EXISTS Folder;
CREATE TABLE Folder (
    folder_id INT PRIMARY KEY IDENTITY(1,1),
    owner_id INT NOT NULL,
    created_at DATETIME NOT NULL DEFAULT GETDATE(),
    folder_name NVARCHAR(100) NOT NULL,
    FOREIGN KEY (owner_id) REFERENCES [User](user_id)
);
GO

-- Create Comment table
DROP TABLE IF EXISTS Comment;
CREATE TABLE Comment (
    comment_id INT PRIMARY KEY IDENTITY(1,1),
    asset_id INT NOT NULL,
    user_id INT NOT NULL,
    content NVARCHAR(1000) NOT NULL,
    created_at DATETIME NOT NULL DEFAULT GETDATE(),
    FOREIGN KEY (asset_id) REFERENCES Asset(asset_id),
    FOREIGN KEY (user_id) REFERENCES [User](user_id)
);
GO

-- Create AuditLog table
DROP TABLE IF EXISTS AuditLog;
CREATE TABLE AuditLog (
    log_id INT PRIMARY KEY IDENTITY(1,1),
    user_id INT NOT NULL,
    action NVARCHAR(100) NOT NULL,
    asset_id INT NULL,
    timestamp DATETIME NOT NULL DEFAULT GETDATE(),
    details NVARCHAR(1000) NULL,
    FOREIGN KEY (user_id) REFERENCES [User](user_id),
    FOREIGN KEY (asset_id) REFERENCES Asset(asset_id)
);
GO

-- Create Notification table
DROP TABLE IF EXISTS Notification;
CREATE TABLE Notification (
    notification_id INT PRIMARY KEY IDENTITY(1,1),
    user_id INT NOT NULL,
    message NVARCHAR(255) NOT NULL,
    created_at DATETIME NOT NULL DEFAULT GETDATE(),
    Status NVARCHAR (255) NOT NULL DEFAULT 0,
    FOREIGN KEY (user_id) REFERENCES [User](user_id)
);
GO

-- Create DashboardWidget table
DROP TABLE IF EXISTS DashboardWidget;
CREATE TABLE DashboardWidget (
    widget_id INT PRIMARY KEY IDENTITY(1,1),
    type NVARCHAR(50) NOT NULL,
    title NVARCHAR(100) NOT NULL,
    data_source NVARCHAR(255) NOT NULL,
    created_at DATETIME NOT NULL DEFAULT GETDATE(),
    updated_at DATETIME NULL
);
GO

-- Create FolderAsset table
DROP TABLE IF EXISTS FolderAsset;
CREATE TABLE FolderAsset (
    folder_asset_id INT PRIMARY KEY IDENTITY(1,1),
    folder_id INT NOT NULL,
    asset_id INT NOT NULL,
    FOREIGN KEY (folder_id) REFERENCES Folder(folder_id),
    FOREIGN KEY (asset_id) REFERENCES Asset(asset_id)
);
GO

-- Create AssetType table
DROP TABLE IF EXISTS AssetType;
CREATE TABLE AssetType (
    asset_type_id INT PRIMARY KEY IDENTITY(1,1),
    name NVARCHAR(50) NOT NULL
	
);
GO

CREATE TABLE StorageType (
    storagetype_id INT PRIMARY KEY,
    price DECIMAL(10, 2),  
    capacity INT,
    duration INT
);
GO

-- Create Permission table
DROP TABLE IF EXISTS Permission;
CREATE TABLE Permission (
    permission_id INT PRIMARY KEY IDENTITY(1,1),
    name NVARCHAR(50) NOT NULL
);
GO
