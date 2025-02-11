# SQLite Example with Expo React Native

This is a simple React Native app that demonstrates how to use SQLite for local data storage in an Expo project. The app allows you to add, view, and delete users from a SQLite database.

## Table of Contents

1. [Prerequisites](#prerequisites)
2. [Installation](#installation)
3. [Usage](#usage)
4. [Code Explanation](#code-explanation)
5. [Key Functions](#key-functions)
6. [Running the App](#running-the-app)
7. [License](#license)

---

## Prerequisites

Before you begin, ensure you have the following installed:

- **Node.js**: Download and install Node.js from [here](https://nodejs.org/).
- **Expo CLI**: Install the Expo CLI globally by running:
  ```bash
  npm install -g expo-cli
  ```

---

## Installation

1. **Create a New Expo Project**:
   If you haven't already created a project, you can create one using the following command:
   ```bash
   npx create-expo-app my-sqlite-app
   cd my-sqlite-app
   ```

2. **Install Dependencies**:
   Install the `expo-sqlite` package:
   ```bash
   npx expo install expo-sqlite
   ```

3. **Replace `App.js`**:
   Replace the contents of your `App.js` file with the code provided below.

---

## Usage

### App Features

- **Add Users**: Enter a name and age, then press "Add User" to store the user in the SQLite database.
- **View Users**: A list of all users is displayed below the input fields.
- **Delete Users**: Each user has a "Delete" button next to their name, which removes them from the database.

---

## Code Explanation

The app uses the `expo-sqlite` library to interact with a SQLite database. Below is a breakdown of the key components:

### 1. **Database Initialization**

The database is initialized asynchronously using `SQLite.openDatabaseAsync`. The `initDatabase` function creates a table named `users` if it doesn't already exist.

```javascript
const dbPromise = SQLite.openDatabaseAsync('mydatabase.db');

const initDatabase = async () => {
  const db = await dbPromise;
  try {
    await db.execAsync(`
      PRAGMA journal_mode = WAL;
      CREATE TABLE IF NOT EXISTS users (
        id INTEGER PRIMARY KEY AUTOINCREMENT,
        name TEXT NOT NULL,
        age INTEGER NOT NULL
      );
    `);
    console.log('Database initialized');
  } catch (error) {
    console.error('Error initializing database:', error);
  }
};
```

### 2. **Fetching Users**

The `fetchUsers` function retrieves all users from the `users` table and updates the state.

```javascript
const fetchUsers = async () => {
  const db = await dbPromise;
  try {
    const result = await db.getAllAsync('SELECT * FROM users;');
    setUsers(result);
  } catch (error) {
    console.error('Error fetching users:', error);
  }
};
```

### 3. **Adding Users**

The `addUser` function inserts a new user into the `users` table using parameterized queries to prevent SQL injection.

```javascript
const addUser = async () => {
  const db = await dbPromise;
  try {
    await db.runAsync('INSERT INTO users (name, age) VALUES (?, ?);', [name, age]);
    fetchUsers(); // Refresh the list of users after adding
    setName('');
    setAge('');
  } catch (error) {
    console.error('Error adding user:', error);
  }
};
```

### 4. **Deleting Users**

The `removeUser` function deletes a user by their `id`.

```javascript
const removeUser = async (id) => {
  const db = await dbPromise;
  try {
    await db.runAsync('DELETE FROM users WHERE id = ?;', [id]);
    fetchUsers(); // Refresh the list of users after deletion
  } catch (error) {
    console.error('Error deleting user:', error);
  }
};
```

### 5. **React Component**

The main component (`Index`) renders input fields for adding users, a list of users, and buttons for deleting users.

```javascript
export default function Index() {
  const [users, setUsers] = useState([]);
  const [name, setName] = useState('');
  const [age, setAge] = useState('');

  useEffect(() => {
    initDatabase().then(() => {
      fetchUsers();
    });
  }, []);

  return (
    <View style={styles.container}>
      <TextInput
        placeholder="Name"
        value={name}
        onChangeText={setName}
        style={styles.input}
      />
      <TextInput
        placeholder="Age"
        value={age}
        onChangeText={setAge}
        keyboardType="numeric"
        style={styles.input}
      />
      <Button title="Add User" onPress={addUser} />
      <FlatList
        data={users}
        keyExtractor={(item) => item.id.toString()}
        renderItem={({ item }) => (
          <View style={styles.userItem}>
            <Text>{item.name}, {item.age}</Text>
            <Button title="Delete" onPress={() => removeUser(item.id)} />
          </View>
        )}
      />
    </View>
  );
}
```

---

## Key Functions

- **`initDatabase()`**: Initializes the SQLite database and creates the `users` table if it doesn't exist.
- **`fetchUsers()`**: Fetches all users from the database and updates the state.
- **`addUser(name, age)`**: Inserts a new user into the database.
- **`removeUser(id)`**: Deletes a user by their `id`.

---

## Running the App

1. **Start the Development Server**:
   Run the following command to start the Expo development server:
   ```bash
   npx expo start
   ```

2. **Run on a Device or Emulator**:
   - Use the Expo Go app on your mobile device to scan the QR code.
   - Alternatively, run the app on an emulator (iOS Simulator or Android Emulator).

---

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for details.

---

Feel free to modify and expand this project to suit your needs! Let me know if you have any questions or need further clarification.
