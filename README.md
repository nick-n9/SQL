# SQL


Users Table

CREATE TABLE Users (
    user_id INT PRIMARY KEY AUTO_INCREMENT,
    phone_number VARCHAR(15) UNIQUE NOT NULL,
    display_name VARCHAR(255),
    profile_picture_url VARCHAR(255),
    status TEXT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);



Chats Table

CREATE TABLE Chats (
    chat_id INT PRIMARY KEY AUTO_INCREMENT,
    chat_type ENUM('single', 'group') NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);


ChatParticipants Table

CREATE TABLE ChatParticipants (
    chat_id INT,
    user_id INT,
    joined_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    PRIMARY KEY (chat_id, user_id),
    FOREIGN KEY (chat_id) REFERENCES Chats(chat_id) ON DELETE CASCADE,
    FOREIGN KEY (user_id) REFERENCES Users(user_id) ON DELETE CASCADE,
    INDEX (user_id) -- Index for faster user-specific queries
);


Messages Table

CREATE TABLE Messages (
    message_id INT PRIMARY KEY AUTO_INCREMENT,
    chat_id INT,
    sender_id INT,
    message_type ENUM('text', 'image', 'video', 'file', 'location', 'contact') NOT NULL,
    content TEXT, -- For text messages or metadata (e.g., file names, URLs)
    media_url VARCHAR(255), -- For media attachments
    sent_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    edited_at TIMESTAMP NULL,
    is_disappearing BOOLEAN DEFAULT FALSE,
    disappearing_expiry TIMESTAMP NULL,
    FOREIGN KEY (chat_id) REFERENCES Chats(chat_id) ON DELETE CASCADE,
    FOREIGN KEY (sender_id) REFERENCES Users(user_id) ON DELETE CASCADE
);



MediaAttachments Table

CREATE TABLE MediaAttachments (
    media_id INT PRIMARY KEY AUTO_INCREMENT,
    message_id INT,
    media_type ENUM('image', 'video', 'audio', 'document') NOT NULL,
    media_url VARCHAR(255) NOT NULL,
    file_size BIGINT,
    FOREIGN KEY (message_id) REFERENCES Messages(message_id) ON DELETE CASCADE
);



MessageEdits Table

CREATE TABLE MessageEdits (
    edit_id INT PRIMARY KEY AUTO_INCREMENT,
    message_id INT,
    edited_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    new_content TEXT,
    FOREIGN KEY (message_id) REFERENCES Messages(message_id) ON DELETE CASCADE
);



DisappearingMessages Table

CREATE TABLE DisappearingMessages (
    chat_id INT PRIMARY KEY,
    expiry_time TIMESTAMP,
    FOREIGN KEY (chat_id) REFERENCES Chats(chat_id) ON DELETE CASCADE
);	
