# Data Model AI Prompts

## 1. Find the Database Connection Details

Search the project files for the Database Connection information. Look in the docker-compose.yml and .env files to determine the Database type and the ORM used by the project.



## 2. Identify the Database Type

In the project files, determine what the Database engine and version are.



## 3. Map the ORM to the Database

Determine how the application interacts with the database through the ORM. Where is the database configuration defined and how are models mapped to tables? Open LearningAPI/models/ and examine the model definitions. Show the Python field names next to the SQL column names and data types. Then, open LearningAPI/views/book_view.py and examine how the create method is used. What is the SQL statement that book.save() generates?



## 4. Generate a Database Diagram

Create an entity-relationship diagram of the database as a Mermaid erDiagram. The models can be found at learn-ops-api/LearningAPI/models be sure to include every field, table and relationship. Output only the Mermaid code block.



## 5. Find Relationship Examples

In the model files, find an example of a one-to-one, one-to-many and a many-to-many relationship. Include the file paths and the field names for each. Include the junction for the many-to-many relationship.
