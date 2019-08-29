# MongoDB

MongoDB is a cross-platform, document oriented database that provides, high performance, high availability, and easy scalability. MongoDB works on concept of collection and document.


## MongoDB vs RDBMS

|  RDBMS      | MongoDB    |
|-------------|------------|
| Database    | Database   |
| Table       | Collection |
| Tuple/Row   | Document   |
| column      | Field      |
| Table Join  | Embedded Documents |
| Primary Key | Primary Key (Default key _id provided by mongodb itself) |

- Database

  Database is a physical container for collections. Each database gets its own set of files on the file system.

- Collection

  Collection is a group of MongoDB documents. A collection exists within a single database. Collections do not enforce a schema. Documents within a collection can have different fields. 
  
- Document
  
  A document is a set of key-value pairs. Documents have dynamic schema.
  
  
- _id

  _id is a 12 bytes hexadecimal number which assures the uniqueness of every document. You can provide _id while inserting the document. If you don’t provide then MongoDB provides a unique id for every document. These 12 bytes first 4 bytes for the current timestamp, next 3 bytes for machine id, next 2 bytes for process id of MongoDB server and remaining 3 bytes are simple incremental VALUE. 
  
  
  
## Commands

### Create Database

```
use <DATABASE_NAME>
```

The command will create a new database if it doesn't exist, otherwise it will return the existing database.

### Drop Database

```
db.dropDatabase()
```

### Create Collection

```
db.createCollection(<name>, <options>)
```

### Drop Collection

### Data
  
   