# DATA

## Courses

| course_id | name        | hours | dept_id |
| --------- | ----------- | ----- | ------- |
| CS101     | Programming | 4     | CS      |
| CS201     | Algorithms  | 3     | CS      |
| CS202     | Systems     | 3     | CS      |
| MA101     | Algebra     | 3     | Math    |
| MA201     | Calculus    | 4     | Math    |
| MA301     | Analysis    | 4     | Math    |
| MU104     | Jazz        | 3     | Music   |
| EE102     | Circuits    | 3     | EE      |
| IE101     | Probability | 3     | IE      |
| IE102     | Statistics  | 3     | IE      |

## Students

| student_id | name    | gpa |
| ---------- | ------- | --- |
| 11         | Bush    | 3.0 |
| 12         | Cruz    | 3.2 |
| 13         | Clinton | 3.9 |
| 22         | Sanders | 3.0 |
| 33         | Trump   | 3.8 |

## Departments

| dept_id | name                   | dean     | building | room |
| ------- | ---------------------- | -------- | -------- | ---- |
| CS      | Computer Science       | Rubio    | Ajax     | 100  |
| Math    | Mathemagics            | Carson   | Acme     | 300  |
| EE      | Electrical Engineering | Kasich   | Ajax     | 200  |
| IE      | Industrial Engineering | Cruz     |          | 200  |
| Music   | Musicology             | Costello | North    | 100  |

## Enrolled

| course_id | student_id |
| --------- | ---------- |
| CS101     | 11         |
| MA101     | 11         |
| CS101     | 12         |
| CS201     | 22         |
| MA201     | 33         |
| EE102     | 33         |
| MA301     | 22         |

![alt text](cheat_sheet.png 'Logo Title Text 1')

# NEO4J CHEAT SHEET

## 1. LOAD DATA FROM CSV

### Cú pháp

```cypher
LOAD CSV WITH HEADERS FROM "FILE:///file.csv" AS row
MERGE (n:Label {property: row.property})
SET n.property = row.property
```

### Ví dụ

IMPORT DEPARTMENT

```cypher
LOAD CSV WITH HEADERS FROM "FILE:///departments.csv" AS row
MERGE (d:Department {deptId: row.dept_id})
SET d.name = row.name, d.dean = row.dejson, d.building = row.building, d.room = toInteger(row.room)
```

IMPORT COURSE

```cypher
LOAD CSV WITH HEADERS FROM "FILE:///courses.csv" AS row
MERGE (c:Course {courseId: row.course_id})
SET c.name = row.name , c.hours = toInteger(row.hours) , c.dept_id = row.dept_id
```

IMPORT STUDENT

```cypher
LOAD CSV WITH HEADERS FROM "FILE:///students.csv" AS row
MERGE (s:Student {studentId: row.student_id})
SET s.name=row.name, s.gpa = toFloat(row.gpa)
```

## 2. TẠO REATIONSHIP

_Relationship n-n: Do có quan hệ n-n nên ta cần tạo 1 file csv chứa các thông tin về quan hệ giữa các node ví dụ ở đây là file enrolled.csv_

### Quan hệ n - n

Cú pháp

```cypher
LOAD CSV WITH HEADERS FROM "FILE:///file.csv" AS row
MATCH (node1:Label1 {property: row.property1}), (node2:Label2 {property: row.property2})
CREATE (node1)-[:RELATIONSHIP]->(node2)
```

Ví dụ:

```cypher
LOAD CSV WITH HEADERS FROM "FILE:///enrolled.csv" AS row
MATCH (c:Course {courseId: row.course_id})
MATCH (s:Student {studentId: row.student_id})
MERGE (s)-[:ENROLLED]->(c)
```

### Quan hệ 1 - n

Cú pháp

```cypher
MATCH (node1:Label1), (node2:Label2)
WHERE node1.property = node2.property
CREATE (node1)-[:RELATIONSHIP]->(node2)
```

Ví dụ:

```cypher
MATCH (c:Course), (d:Department)
WHERE c.dept_id = d.deptId
CREATE (c)<-[:OWNS]-(d)
```

## 3. TẠO CONSTRAINT, XEM, XÓA

### Tạo constraint

Cú pháp

```cypher
CREATE CONSTRAINT FOR (node:Label) REQUIRE node.property IS UNIQUE
```

Ví dụ:

```cypher
CREATE CONSTRAINT FOR (c:Course) REQUIRE c.courseId IS UNIQUE;
CREATE CONSTRAINT FOR (d:Department) REQUIRE d.deptId IS UNIQUE;
CREATE CONSTRAINT FOR (s:Student) REQUIRE s.studentId IS UNIQUE;
```

### Xem constraint

```cypher
SHOW CONSTRAINT
```

### Xóa constraint

_Sau khi show constraint sẽ có name của constraint đó_

```cypher
DROP CONSTRAINT [name]
```
