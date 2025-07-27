# Multi-User Chat App

A modern, feature-rich **chat application** built with **JavaFX** for clients and Java backend sockets, allowing multiple users to communicate over the same network with support for **personal (private) and group chats**. The application integrates with a **SQLite database** to persist users, groups, and full chat history (including messages and images) for both individual and group conversations.

## Features

- **Multi-User Chat:** Connect any number of clients over the same local network, each with a unique login.
- **Persistent Messaging:** All messages are **stored in a SQLite database**, allowing users to access their past chat history upon login.
- **Private (1:1) Messaging:** Users can select and chat privately with any other online or registered user.
- **Group Chats:** Users can **create, join, leave, and message groups**. Only group members can view group messages.
- **Rich Media Support:** Allows sending and receiving images in both group and private conversations.
- **Authentication:** Secure login and user registration with hashed password storage.
- **Live Presence:** User lists reflect real-time online/offline status.
- **Modern UI:** JavaFX-based interface offering a clean layout, chat bubbles, file/image selection dialogs, and real-time updates.

## System Architecture

- **Client:** JavaFX desktop application (`ChatClient.java`)
  - Presents user interface, handles user interactions and displays chat, users, groups, and messages.
  - Connects via TCP socket to the backend server.
  - Supports image sharing by base64-encoding images delivered over the socket.
  - Maintains in-memory per-user and per-group chat histories, synchronized from the database at login.

- **Server:** Java server application (`ChatServer.java`)
  - Manages all client connections, broadcasting messages and handling routing for private and group chats.
  - Persists all users, groups, and messages to **SQLite** using the `DatabaseHelper` class.
  - Handles authentication, registration, group management, status broadcast, and message delivery.
  - Restores full message and group history to clients at login.

- **Database:** SQLite, managed with JDBC
  - **users** table: stores registered usernames and hashed passwords.
  - **groups** table: maps group names and member lists.
  - **messages** table: stores each message, sender, recipient/group, content, and timestamp.

## Key Components Overview

### 1. Authentication & User Management
- **New users** register by entering a unique username and password. Passwords are securely hashed (SHA-256).
- **Existing users** log in with credentials. Server authenticates against the database.
- The server broadcasts live user status (online/offline) to all connected clients.

### 2. Messaging & Persistence
- **Personal Chats:** Messages are routed only to the intended recipient and logged in the database. Upon login, history is replayed from the database.
- **Group Chats:** Messages sent to a group are delivered to all online group members and persisted in the database, available to group members even after reconnecting.
- **Media Messages:** Images are base64-encoded and transferred as text payloads, then decoded and displayed in the chat UI.

### 3. Groups
- **Creation:** Any user can create a group with a unique name.
- **Joining/Leaving:** Users may join or leave any group; only joined members receive and can send group messages.
- **Membership Tracking:** Server maintains live group membership, and the database persists group membership.

### 4. Modern JavaFX User Interface
- Displays user and group lists, chat areas, message input, and image/file chooser.
- Chat bubbles distinguish between sent and received messages.
- Visual highlights for selected user/group, feedback for connection errors, and scroll-to-bottom on new messages.

## How to Run

### Prerequisites

- **Java 11 or higher**
- **JavaFX SDK** (if not using a bundled JDK)
- No setup for SQLite required; the database file is created automatically
- Both server and client must run in the same network (LAN); adjust IP as needed

### 1. Start the Server

- Compile and run `ChatServer.java`:


The server listens by default on `10.17.235.2:8000` (change as needed in the source if your local IP differs).

### 2. Run the Client(s)

- Compile and run `ChatClient.java` on any number of devices on the same network:


- Enter the server address as prompted (or modify directly in `connectToServer()`).

### 3. Initial Setup and Usage

- Clients log in or register with a username and password.
- The app displays current users and available groups. You can:
  - Start private chats
  - Create new groups, join/leave groups
  - Send and receive text and images in chats

## Database Layer

- All messages, users, groups, and chat history are stored in a local file `chat.db` on the server.
- Structure includes:

  - **users (id, username, password, ...)**  
  - **groups (id, groupname, groupmembers, ...)**  
  - **messages (id, sender, recipient, groupname, content, timestamp)**  

- Automatically manages migration and persistence.

## Security Notice

- **Passwords are stored as SHA-256 hashes**. Never store plaintext user credentials.
- Chat is LAN-local. For WAN/internet exposure, consider adding TLS encryption and enhanced authentication.

## File Overview

| File                | Purpose                                              |
|---------------------|------------------------------------------------------|
| ChatServer.java     | Server main, client handler, group/user management   |
| ChatClient.java     | JavaFX UI and socket client logic                    |
| DatabaseHelper.java | (Inner class) DB schema and SQL operations           |
| chat.db             | SQLite persistent storage (created server-side)      |
| styles.css          | (Optional) JavaFX user interface styling             |


