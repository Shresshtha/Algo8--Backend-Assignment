# Auth Backend

Welcome to the **Auth Backend** project! This is a production-ready authentication backend built with **Node.js, Express, and MongoDB**. It features user signup, login, and secure user data retrieval with JWT authentication.

---

## Features 🚀

- **User Signup** 📌: Secure account creation with hashed passwords.
- **User Login** 🔑: Authenticate users and issue JWT tokens.
- **Protected Routes** 🛡️: Access user data with a valid JWT token.
- **Scalable Architecture** ⚡: Modular, structured, and ready for expansion.

---

## Technologies Used 🛠️

- **Node.js**: JavaScript runtime for backend development.
- **Express**: Lightweight and fast web framework.
- **MongoDB**: NoSQL database for scalable user storage.
- **Mongoose**: Object Data Modeling (ODM) library for MongoDB.
- **bcryptjs**: Secure password hashing.
- **jsonwebtoken**: Industry-standard JWT authentication.
- **dotenv**: Environment variable management.
- **cors**: Cross-Origin Resource Sharing middleware.

---

## Getting Started 🏗️

### Prerequisites ✅

- **Install Node.js and npm** ([Download here](https://nodejs.org/))
- **MongoDB installed and running** ([Local or Atlas](https://www.mongodb.com/))

### Installation 📥

1. Clone the repository:
    ```sh
    git clone https://github.com/your-username/auth-backend.git
    cd auth-backend
    ```

2. Install dependencies:
    ```sh
    npm install
    ```

3. Create a `.env` file and configure:
    ```env
    PORT=5000
    MONGO_URI=mongodb://localhost:27017/authDB
    JWT_SECRET=mysecretkey
    ```

4. Start the server:
    ```sh
    npm start
    ```

---

## API Endpoints 🔄

### **1. User Signup** 📝
- **Endpoint:** `POST /api/auth/signup`
- **Request Body:**
    ```json
    {
        "username": "example",
        "email": "example@example.com",
        "password": "password"
    }
    ```
- **Response:**
    ```json
    { "message": "User created successfully!" }
    ```
- **Implementation:**
    ```javascript
    router.post("/signup", async (req, res) => {
        try {
            const { username, email, password } = req.body;
            const hashedPassword = await bcrypt.hash(password, 10);
            const user = new User({ username, email, password: hashedPassword });
            await user.save();
            res.status(201).json({ message: "User created successfully!" });
        } catch (error) {
            res.status(500).json({ error: "Error signing up" });
        }
    });
    ```

---

### **2. User Login** 🔓
- **Endpoint:** `POST /api/auth/login`
- **Request Body:**
    ```json
    {
        "email": "example@example.com",
        "password": "password"
    }
    ```
- **Response:**
    ```json
    { "token": "jwt-token" }
    ```
- **Implementation:**
    ```javascript
    router.post("/login", async (req, res) => {
        try {
            const { email, password } = req.body;
            const user = await User.findOne({ email });
            if (!user || !(await bcrypt.compare(password, user.password))) {
                return res.status(400).json({ error: "Invalid credentials" });
            }
            const token = jwt.sign({ id: user._id }, process.env.JWT_SECRET, { expiresIn: "1h" });
            res.json({ token });
        } catch (error) {
            res.status(500).json({ error: "Error logging in" });
        }
    });
    ```

---

### **3. Get User Data (Protected)** 🛡️
- **Endpoint:** `GET /api/auth/user`
- **Headers:** `{ "Authorization": "Bearer jwt-token" }`
- **Response:**
    ```json
    {
        "username": "example",
        "email": "example@example.com"
    }
    ```
- **Implementation:**
    ```javascript
    router.get("/user", async (req, res) => {
        try {
            const token = req.headers.authorization;
            if (!token) return res.status(401).json({ error: "Unauthorized" });
            const decoded = jwt.verify(token, process.env.JWT_SECRET);
            const user = await User.findById(decoded.id).select("-password");
            res.json(user);
        } catch (error) {
            res.status(500).json({ error: "Error fetching user data" });
        }
    });
    ```

---

## Project Structure 📂
