## Lesson 1: Introduction to Databases & SQL

🧠 What is a Database?
A database is an organized collection of data stored electronically, designed to be easily accessed, managed, and updated.
Examples:
Relational: MySQL, PostgreSQL, SQLite, SQL Server
Non-relational: MongoDB
🧠 What is SQL?
SQL (Structured Query Language) is used to communicate with relational databases.
Main categories:
DDL (Data Definition Language): CREATE, ALTER, DROP
DML (Data Manipulation Language): INSERT, UPDATE, DELETE
DQL (Data Query Language): SELECT
DCL (Data Control Language): GRANT, REVOKE
TCL (Transaction Control Language): COMMIT, ROLLBACK

## Lesson 2: Creating and Managing Databases

📘 SQL Example:
-- Create a new database
CREATE DATABASE school_db;

-- Use the database
USE school_db;

-- Delete database
DROP DATABASE school_db;
⚙️ TypeORM Equivalent:
// In TypeORM, database is configured in the data source config file
// data-source.ts
import { DataSource } from "typeorm";

export const AppDataSource = new DataSource({
  type: "mysql",
  host: "localhost",
  username: "root",
  password: "password",
  database: "school_db",
  synchronize: true,
  logging: false,
});
💎 Prisma Equivalent:
// schema.prisma
datasource db {
  provider = "mysql"
  url      = env("DATABASE_URL")
}


## Lesson 3: Creating Tables and Data Types
🧠 Common SQL Data Types
Type	Description	Example
INT	Integer numbers	1, 200
VARCHAR(n)	String of length n	'John'
TEXT	Long text	'This is a note'
DATE / DATETIME	Dates	'2025-01-01'
BOOLEAN	True/False	true
DECIMAL / FLOAT	Decimal numbers	12.99
📘 SQL Example:
CREATE TABLE students (
  id INT PRIMARY KEY AUTO_INCREMENT,
  name VARCHAR(100),
  age INT,
  grade VARCHAR(10),
  created_at DATETIME DEFAULT CURRENT_TIMESTAMP
);
⚙️ TypeORM Equivalent:
import { Entity, PrimaryGeneratedColumn, Column, CreateDateColumn } from "typeorm";

@Entity()
export class Student {
  @PrimaryGeneratedColumn()
  id: number;

  @Column()
  name: string;

  @Column()
  age: number;

  @Column()
  grade: string;

  @CreateDateColumn()
  created_at: Date;
}
💎 Prisma Equivalent:
model Student {
  id         Int      @id @default(autoincrement())
  name       String
  age        Int
  grade      String
  created_at DateTime @default(now())
}


## Lesson 4: Inserting Data
📘 SQL Example:
INSERT INTO students (name, age, grade)
VALUES ('Alice', 18, 'A'),
       ('Bob', 19, 'B');
⚙️ TypeORM Equivalent:
const studentRepo = AppDataSource.getRepository(Student);
await studentRepo.save([
  { name: "Alice", age: 18, grade: "A" },
  { name: "Bob", age: 19, grade: "B" },
]);
💎 Prisma Equivalent:
await prisma.student.createMany({
  data: [
    { name: "Alice", age: 18, grade: "A" },
    { name: "Bob", age: 19, grade: "B" },
  ],
});


## Lesson 5: Selecting Data
📘 SQL Example:
SELECT * FROM students;
SELECT name, grade FROM students WHERE age > 18 ORDER BY name ASC;
⚙️ TypeORM Equivalent:
const allStudents = await studentRepo.find();
const filtered = await studentRepo.find({
  where: { age: MoreThan(18) },
  order: { name: "ASC" },
});
💎 Prisma Equivalent:
const allStudents = await prisma.student.findMany();
const filtered = await prisma.student.findMany({
  where: { age: { gt: 18 } },
  orderBy: { name: "asc" },
});


## Lesson 6: Updating and Deleting Data
📘 SQL Example:
-- Update
UPDATE students SET grade = 'A+' WHERE name = 'Bob';

-- Delete
DELETE FROM students WHERE age < 18;
⚙️ TypeORM Equivalent:
await studentRepo.update({ name: "Bob" }, { grade: "A+" });
await studentRepo.delete({ age: LessThan(18) });
💎 Prisma Equivalent:
await prisma.student.updateMany({
  where: { name: "Bob" },
  data: { grade: "A+" },
});

await prisma.student.deleteMany({
  where: { age: { lt: 18 } },
});

✅ You now understand:
Database basics
Creating tables
Inserting, selecting, updating, deleting data
Mapping all to TypeORM and Prisma
Would you like me to continue with Module 2: Intermediate SQL (Filtering, Aggregation, and Joins) next?
That’s where we’ll dive into WHERE, LIKE, GROUP BY, JOINS — with ORM equivalents and sample datasets.


## Lesson 7: Filtering Data (WHERE, LIKE, IN, BETWEEN, IS NULL)

🧠 What it is:
WHERE filters data based on conditions.
📘 SQL Examples:
-- WHERE: filter by condition
SELECT * FROM students WHERE age > 18;

-- LIKE: pattern matching
SELECT * FROM students WHERE name LIKE 'A%';   -- starts with A

-- IN: multiple values
SELECT * FROM students WHERE grade IN ('A', 'A+');

-- BETWEEN: range
SELECT * FROM students WHERE age BETWEEN 18 AND 22;

-- IS NULL: check for missing data
SELECT * FROM students WHERE grade IS NULL;
⚙️ TypeORM Equivalent:
import { Like, In, Between, IsNull, MoreThan } from "typeorm";

await studentRepo.find({ where: { age: MoreThan(18) } });
await studentRepo.find({ where: { name: Like("A%") } });
await studentRepo.find({ where: { grade: In(["A", "A+"]) } });
await studentRepo.find({ where: { age: Between(18, 22) } });
await studentRepo.find({ where: { grade: IsNull() } });
💎 Prisma Equivalent:
await prisma.student.findMany({ where: { age: { gt: 18 } } });
await prisma.student.findMany({ where: { name: { startsWith: "A" } } });
await prisma.student.findMany({ where: { grade: { in: ["A", "A+"] } } });
await prisma.student.findMany({ where: { age: { gte: 18, lte: 22 } } });
await prisma.student.findMany({ where: { grade: null } });

## Lesson 8: Aggregate Functions (COUNT, SUM, AVG, MAX, MIN)

🧠 What it is:
Aggregate functions summarize data across rows.
📘 SQL Examples:
SELECT COUNT(*) AS total_students FROM students;
SELECT AVG(age) AS avg_age FROM students;
SELECT MAX(age) AS oldest, MIN(age) AS youngest FROM students;
⚙️ TypeORM Equivalent:
await studentRepo.count();
await studentRepo.createQueryBuilder("student").select("AVG(student.age)", "avg_age").getRawOne();
await studentRepo.createQueryBuilder("student")
  .select("MAX(student.age)", "oldest")
  .addSelect("MIN(student.age)", "youngest")
  .getRawOne();
💎 Prisma Equivalent:
await prisma.student.count();
await prisma.student.aggregate({ _avg: { age: true } });
await prisma.student.aggregate({ _max: { age: true }, _min: { age: true } });


## Lesson 9: Grouping and Aggregation (GROUP BY, HAVING)
🧠 What it is:
Used to group data and apply aggregate functions per group.
📘 SQL Example:
SELECT grade, COUNT(*) AS student_count, AVG(age) AS avg_age
FROM students
GROUP BY grade
HAVING COUNT(*) > 2;  -- filter groups
⚙️ TypeORM Equivalent:
await studentRepo.createQueryBuilder("student")
  .select("student.grade", "grade")
  .addSelect("COUNT(*)", "student_count")
  .addSelect("AVG(student.age)", "avg_age")
  .groupBy("student.grade")
  .having("COUNT(*) > :count", { count: 2 })
  .getRawMany();
💎 Prisma Equivalent:
await prisma.student.groupBy({
  by: ["grade"],
  _count: { _all: true },
  _avg: { age: true },
  having: { _count: { _all: { gt: 2 } } },
});


## Lesson 10: Table Relationships (Primary & Foreign Keys)
🧠 What it is:
Primary Key: uniquely identifies a row.
Foreign Key: links one table to another.
We’ll add a new courses table.
📘 SQL Example:
CREATE TABLE courses (
  id INT PRIMARY KEY AUTO_INCREMENT,
  title VARCHAR(100)
);

ALTER TABLE students ADD COLUMN course_id INT;
ALTER TABLE students
ADD FOREIGN KEY (course_id) REFERENCES courses(id);
⚙️ TypeORM Equivalent:
@Entity()
export class Course {
  @PrimaryGeneratedColumn()
  id: number;

  @Column()
  title: string;

  @OneToMany(() => Student, (student) => student.course)
  students: Student[];
}

@Entity()
export class Student {
  @PrimaryGeneratedColumn()
  id: number;

  @Column()
  name: string;

  @ManyToOne(() => Course, (course) => course.students)
  course: Course;
}
💎 Prisma Equivalent:
model Course {
  id       Int       @id @default(autoincrement())
  title    String
  students Student[]
}

model Student {
  id        Int      @id @default(autoincrement())
  name      String
  age       Int
  grade     String
  course_id Int?
  course    Course?  @relation(fields: [course_id], references: [id])
}


## Lesson 11: Joins (INNER, LEFT, RIGHT, FULL)

🧠 What it is:
Used to combine rows from multiple tables.
📘 SQL Examples:
-- INNER JOIN (only matches)
SELECT students.name, courses.title
FROM students
INNER JOIN courses ON students.course_id = courses.id;

-- LEFT JOIN (all students, even if no course)
SELECT students.name, courses.title
FROM students
LEFT JOIN courses ON students.course_id = courses.id;
⚙️ TypeORM Equivalent:
await studentRepo.createQueryBuilder("student")
  .innerJoinAndSelect("student.course", "course")
  .getMany();

await studentRepo.createQueryBuilder("student")
  .leftJoinAndSelect("student.course", "course")
  .getMany();
💎 Prisma Equivalent:
await prisma.student.findMany({
  include: { course: true },
});
(Prisma automatically performs a LEFT JOIN when you use include.)


## Lesson 12: Subqueries

🧠 What it is:
A subquery is a query inside another query.
📘 SQL Example:
-- Find students older than the average age
SELECT * FROM students
WHERE age > (SELECT AVG(age) FROM students);
⚙️ TypeORM Equivalent:
await studentRepo.createQueryBuilder("student")
  .where("student.age > (SELECT AVG(age) FROM students)")
  .getMany();
💎 Prisma Equivalent:
Prisma doesn’t support subqueries directly, but you can perform it in two steps:
const avg = await prisma.student.aggregate({ _avg: { age: true } });
await prisma.student.findMany({ where: { age: { gt: avg._avg.age! } } });

✅ After Module 2, you know how to:
Filter data precisely
Aggregate and group results
Create relationships and joins
Write and replicate subqueries in ORM
