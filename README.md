# Assignment and Lab Report 03
**Course:** Database Management System Sessional (CSEC-322)<br />

**Prepared By:**<br />
Ashikujjaman Himel<br />
ID: 2222081021<br />
Batch: 57A Day<br />
Semester: Fall-2024<br />

## 3.10 (A)
```sql
UPDATE employee
SET city = 'Newtown'
WHERE ID = '12345';
```

## 3.10 (B)
```sql
UPDATE works
SET salary = CASE
  WHEN salary * 1.10 <= 100000 THEN salary * 1.10
  ELSE salary * 1.03
END
WHERE ID IN (
  SELECT m.ID
  FROM manages m
  JOIN employee e ON m.ID = e.ID
  JOIN works w ON e.ID = w.ID
  JOIN company c ON w.company_name = c.company_name
  WHERE c.company_name = 'First Bank Corporation'
);
```

## 3.11 (A)
```sql
SELECT DISTINCT S.ID, S.name
FROM Student S
JOIN Takes T ON S.ID = T.ID
JOIN Section Sec ON T.course_id = Sec.course_id AND T.sec_id = Sec.sec_id AND T.semester = Sec.semester AND T.year = Sec.year
JOIN Course C ON Sec.course_id = C.course_id
WHERE C.dept_name = 'Comp. Sci.';
```

## 3.11 (B)
```sql
SELECT S.ID, S.name
FROM Student S
WHERE S.ID NOT IN (
  SELECT T.ID
  FROM Takes T
  JOIN Section Sec ON T.course_id = Sec.course_id AND T.sec_id = Sec.sec_id AND T.semester = Sec.semester AND T.year = Sec.year
  WHERE Sec.year < 2017
);
```

## 3.11 (C)
```sql
SELECT dept_name, MAX(salary) AS max_salary
FROM Instructor
GROUP BY dept_name;
```

## 3.11 (D)
```sql
SELECT MIN(max_salary) AS lowest_max_salary
FROM (SELECT dept_name, MAX(salary) AS max_salary
  FROM Instructor
  GROUP BY dept_name) AS DeptMax;
```

## 3.12 (A)
```sql
INSERT INTO course (course_id, title, dept_name, credits)
VALUES ('CS-001', 'Weekly Seminar', 'Comp. Sci.', 0);
```

## 3.12 (B)
```sql
INSERT INTO section (course_id, sec_id, semester, year, building, room_number)
VALUES ('CS-001', 1, 'Fall', 2017, NULL, NULL);
```

## 3.12 (C)
```sql
INSERT INTO takes (ID, course_id, sec_id, semester, year)
SELECT ID, 'CS-001', 1, 'Fall', 2017
FROM student
WHERE dept_name = 'Comp. Sci.';
```

## 3.12 (D)
```sql
DELETE FROM takes
WHERE ID = 12345
  AND course_id = 'CS-001'
  AND sec_id = 1
  AND semester = 'Fall'
  AND year = 2017;
```

## 3.12 (E)
```sql
DELETE FROM section
WHERE course_id = 'CS-001';

DELETE FROM course
WHERE course_id = 'CS-001';
```

## 3.12 (F)
```sql
DELETE FROM takes
WHERE course_id IN (
  SELECT course_id
  FROM course
  WHERE LOWER(title) LIKE '%advanced%'
);
```

## 3.24
```sql
SELECT s.student_name, s.student_id
FROM Student s
JOIN Instructor i ON s.advisor_id = i.instructor_id
WHERE s.major = 'Accounting' AND i.department = 'Physics';
```

## 3.30
In SQL, AVG() and (SUM() / COUNT()) are computed separately, and they may produce slightly different results due to differences in how floating-point precision is handled.

### Example
Consider the following `instructor` table with three entries:
<table style="width:100%;border:1px solid red;border-collapse:collapse;text-align:center">
  <tr>
    <th style="border:1px solid red;text-align:center">ID</th>
    <th style="border:1px solid red;text-align:center">Salary</th>
  </tr>

  <tr>
    <td style="border:1px solid red;">1</td>
    <td style="border:1px solid red;">50000.12</td>
  </tr>
  <tr>
    <td style="border:1px solid red;">2</td>
    <td style="border:1px solid red;">50000.13</td>
  </tr>
  <tr>
    <td style="border:1px solid red;">3</td>
    <td style="border:1px solid red;">50000.14</td>
  </tr>
</table>

### Calculations:
1. **AVG(salary):** This will likely compute an average with higher precision, potentially using a `FLOAT` or `DOUBLE` internally.
```
AVG(salary) = (50000.12 + 50000.13 + 50000.14) / 3 = 50000.13
```

2. **(SUM(salary) / COUNT(*)):** Here, the calculation might truncate or round the result to a different precision after summing and dividing.
```
SUM(salary) = 50000.12 + 50000.13 + 50000.14 = 150000.39
SUM(salary) / COUNT(*) = 150000.39 / 3 = 50000.13
```

Even though these calculations look the same mathematically, slight differences in precision at each step could cause a tiny difference in the final results.

### Why This Happens
Floating-point arithmetic in computers can introduce small errors due to limited precision, especially when combining multiple operations (like division and summing separately). The difference may be very small, but it would make the result of the query non-zero in certain instances.

### Conclusion
This floating-point precision issue is common in databases and computer systems and demonstrates why results that should be equivalent can differ slightly due to the nuances of data types and precision in computation.