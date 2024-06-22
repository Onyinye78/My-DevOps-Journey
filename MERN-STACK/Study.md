Data base management system

A Database Management System (DBMS) is a software system designed to manage databases. It provides an interface for users and applications to interact with databases, ensuring data is organized, stored, retrieved, and manipulated efficiently and securely.

Types of DBMS
Relational DBMS (RDBMS): Manages structured data in tables with predefined schemas. Uses SQL for data management (e.g., MySQL, PostgreSQL).
NoSQL DBMS: Handles unstructured and semi-structured data, often schema-less, optimized for specific use cases (e.g., MongoDB, Cassandra).
Object-oriented DBMS (OODBMS): Integrates object-oriented programming principles for managing data as objects.
Hierarchical DBMS: Uses a tree-like structure for data organization.
Network DBMS: Utilizes a graph structure to represent data and relationships.

for the project  we are embarking on the journey to set up a full-stack Todo appilication on an Amazon EC2 instance which involves a myraid of steps, configuring the backend with Nodes.js and Express, contecting to a MongoDB database, and creating a frontend with react.

# Backend configuration with Node.js and Express
setting up the backend using Node.js a flexible web application framework and express provides a robust set of features to develop web and mobile appilications. the setup begins with creating an Express server and defining routes for handling CRUD Operations on Todo items. this includes routes for retrieving all todo items, adding a new todo, and deleting a todo item by its ID.

# Database connection with MongoDB
Connecting to MongoDB involves configuring the mongoose package to connect to a MongoDB Atlas cluster. This requires defining the connection string in a .env file and loading it using the dotenv package. However, an error was encountered due to the MongoDB URI not being correctly retrieved from the environment variables. Ensuring the .env file had the correct MongoDB URI and verifying its retrieval resolved the issue.

# Setting up the Frontend with React

The frontend involved setting up a React application within the project directory. The frontend communicates with the backend via the defined API routes. However, during setup, running the React app setup command appeared to freeze. This issue was resolved by ensuring the environment had sufficient resources and network connectivity.

The React application consists of components such as Input for adding todos and ListTodo for displaying them. The Todo component manages the state and handles API interactions, including fetching todos from the backend and deleting todos. Configuring the proxy in the React app's package.json file ensures API requests during development are proxied to the backend server. This configuration helps in seamless communication between the frontend and backend during development.   