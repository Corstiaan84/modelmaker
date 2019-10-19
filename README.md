# ModelMaker for SQL Server
Welcome to the world of power and simplicity. ModelMaker is a simple and lightweight ORM built on ADO.NET foundation. ModelMaker is for those who loves SQL queries and optimization. Let's start!!


## Installation
To install ModelMaker from NuGet Package Manager CLI, <br><br>
``PM> Install-Package ModelMaker -Version 1.0.0`` <br><br>
or, goto: https://www.nuget.org/packages/ModelMaker/


## Documentation
We need to understand the basic structure of a data model in ModelMaker. We make data models in the Data Access Layer (DAL) in a 3-Layer or N-Layer Application which are responsible for handling database operations. There will one data model for each table.

### ModelMaker.Model (string connectionString) Class
A data model class will be a derived class of base class ``ModelMaker.Model`` and will consist of some of it's properties and methods. It's a basic structure of a data model:

``` c#

class StudentModel : Model
    {
        private static string connStr = "connection string";   
        public StudentModel() : base(connStr) 
        { 
        
        }
    }

```

This is how we make a subclass of ``ModelMaker.Model`` class. Now let's dive deep into it.

There are two basic operations in ModelMaker:
1. ``Exec() [void]`` --> for queries that return no results (insert, update, delete)
2. ``Read<T>(string query, EntityMap entityMap)`` --> for queries that return results (select)


At first, let's see a basic example:

``` c#
using ModelMaker;

namespace modelMakerTest
{
    class Program
    {
        static void Main(string[] args)
        {
            StudentModel student = new StudentModel();
            student.student_name = "Mahfuz";
            student.enrolled_dept = "CSE";
            student.Insert();
        }
    }

    class StudentModel : Model
    {
        public string id { get; set; }
        public string student_name { get; set; }
        public string enrolled_dept { get; set; }

        private static string connStr = "connection string";   

        public StudentModel() : base(connStr) { }

        public void Save()
        {
            string query = "insert into.....";
            Exec(query);
        }
    }
}
```

See, no extra code hassles. Just a single function to execute queries. This example was for no return command. Now, it's time to read our data tables. Before we begin let's know about a class that is required to used ``Read<T>()`` method:

### ModelMaker.EntityMap (string[] connections) Class
We need to create an EntityMap object to map our class properties to table columns. We will use a spacial syntax for this: <br>
```class-property-name : table-column-name``` <br>
[this is for one connection]

We need an array of these connections to map all our properties to columns. Let's create one:

``` c#

string[] connections = new string[] {
  "id : id",
  "student_name : name",
  "enrolled_dept : dept"
}

```

That's it!! Now let's read some data with ModelMaker:

``` c#
using ModelMaker;

namespace modelMakerTest
{
    class Program
    {
        static void Main(string[] args)
        {
            StudentModel student = new StudentModel();
            Console.WriteLine(student.Select().FirstOrDefault().student_name);           
        }
    }

    class StudentModel : Model
    {
        public string id { get; set; }
        public string student_name { get; set; }
        public string enrolled_dept { get; set; }

        private static string connStr = "connection string";   

        public StudentModel() : base(connStr) { }

        public void Save()
        {
            string query = "insert into.....";
            Exec(query);
        }
        
        public List<StudentModel> Select(){
            string query = "select from.....";
            
            string[] connections = new string[] {
              "id : id",
              "student_name : name",
              "enrolled_dept : dept"
            }
            
            EntityMap map = new EntityMap(connections);
            
            return Read<StudentModel>(query, map);
        }
    }
}
```

### ModelMaker.Model.getStatus() [int] Method
It's really necessary to check whether we are successfully connected to our database or not. ModelMaker also provides a method for that. Let's make changes to our previous example: 

``` c#

class Program
    {
        static void Main(string[] args)
        {
            StudentModel student = new StudentModel();
            if(student.getStatus() == 200){
                Console.WriteLine(student.Select().FirstOrDefault().student_name);
            }
            else{
                Console.WriteLine("Failed to connect with database!");
            }
                       
        }
    }
    
```
    
This method returns two interger values:
1. ``200`` if a successful connection occurs
2. ``500`` if it fails to connect to databse

That's it! Simple, Lightweight and Pure ADO.NET :smile:

