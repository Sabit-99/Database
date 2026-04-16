DROP DATABASE university_join_lab;
CREATE DATABASE university_join_lab;
USE university_join_lab;

CREATE TABLE DEPARTMENT (
    DeptID INT PRIMARY KEY,
    DeptName VARCHAR(50) NOT NULL UNIQUE,
    Building VARCHAR(50) NOT NULL
);

CREATE TABLE STUDENT (
    StudentID INT PRIMARY KEY,
    FullName VARCHAR(100) NOT NULL,
    DeptID INT,
    Batch VARCHAR(20) NOT NULL,
    CGPA DECIMAL(3,2),
    FOREIGN KEY (DeptID) REFERENCES DEPARTMENT(DeptID)
);

CREATE TABLE COURSE (
    CourseID INT PRIMARY KEY,
    CourseTitle VARCHAR(100) NOT NULL,
    Credit INT CHECK (Credit > 0),
    DeptID INT,
    FOREIGN KEY (DeptID) REFERENCES DEPARTMENT(DeptID)
);

CREATE TABLE ENROLLMENT (
    EnrollID INT PRIMARY KEY,
    StudentID INT,
    CourseID INT,
    Semester VARCHAR(20) NOT NULL,
    Marks INT CHECK (Marks >= 0 AND Marks <= 100),
    FOREIGN KEY (StudentID) REFERENCES STUDENT(StudentID),
    FOREIGN KEY (CourseID) REFERENCES COURSE(CourseID)
);

INSERT INTO DEPARTMENT VALUES
(1, 'CSE', 'Building A'),
(2, 'EEE', 'Building B'),
(3, 'BBA', 'Building C'),
(4, 'ENG', 'Building D');

INSERT INTO STUDENT VALUES
(101, 'Sabbir Rahman', 1, '2021', 3.50),
(102, 'Sadia Islam', 1, '2022', 3.80),
(103, 'Rakib Hasan', 2, '2021', 3.20),
(104, 'Sumaiya Akter', 2, '2022', 3.70),
(105, 'Tanvir Ahmed', 3, '2021', 3.10),
(106, 'Nusrat Jahan', 3, '2022', 3.60),
(107, 'Sakib Al Hasan', 1, '2021', 3.90),
(108, 'Arafat Hossain', 4, '2022', 3.00);

INSERT INTO COURSE VALUES
(201, 'Data Structures', 3, 1),
(202, 'Algorithms', 3, 1),
(203, 'Circuit Analysis', 3, 2),
(204, 'Marketing', 3, 3),
(205, 'English Literature', 2, 4),
(206, 'Database Systems', 3, 1);

INSERT INTO ENROLLMENT VALUES
(1, 101, 201, 'Fall 2023', 75),
(2, 101, 202, 'Fall 2023', 80),
(3, 102, 201, 'Fall 2023', 85),
(4, 102, 206, 'Fall 2023', 90),
(5, 103, 203, 'Fall 2023', 65),
(6, 104, 203, 'Fall 2023', 70),
(7, 105, 204, 'Fall 2023', 60),
(8, 106, 204, 'Fall 2023', 88),
(9, 107, 201, 'Fall 2023', 92),
(10, 107, 202, 'Fall 2023', 89),
(11, 108, 205, 'Fall 2023', 72),
(12, 106, 206, 'Fall 2023', 84);

-- 1. Student + Department
SELECT s.FullName, d.DeptName
FROM STUDENT s
INNER JOIN DEPARTMENT d ON s.DeptID = d.DeptID;

-- 2. Student + Course + Semester + Marks
SELECT s.FullName, c.CourseTitle, e.Semester, e.Marks
FROM ENROLLMENT e
INNER JOIN STUDENT s ON e.StudentID = s.StudentID
INNER JOIN COURSE c ON e.CourseID = c.CourseID;

-- 3. All Students (including not enrolled)
SELECT s.FullName, e.CourseID
FROM STUDENT s
LEFT JOIN ENROLLMENT e ON s.StudentID = e.StudentID;

-- 4. All Courses (including no enrollment)
SELECT c.CourseTitle, e.StudentID
FROM COURSE c
LEFT JOIN ENROLLMENT e ON c.CourseID = e.CourseID;

-- 5. Department-wise total students
SELECT d.DeptName, COUNT(s.StudentID) AS TotalStudents
FROM DEPARTMENT d
LEFT JOIN STUDENT s ON d.DeptID = s.DeptID
GROUP BY d.DeptName;

-- 6. Course-wise total enrolled students
SELECT c.CourseTitle, COUNT(e.StudentID) AS TotalEnrolled
FROM COURSE c
LEFT JOIN ENROLLMENT e ON c.CourseID = e.CourseID
GROUP BY c.CourseTitle;

-- 7. Avg, Max, Min marks per course
SELECT c.CourseTitle,
       AVG(e.Marks) AS AvgMarks,
       MAX(e.Marks) AS MaxMarks,
       MIN(e.Marks) AS MinMarks
FROM COURSE c
JOIN ENROLLMENT e ON c.CourseID = e.CourseID
GROUP BY c.CourseTitle;

-- 8. Student total courses + average marks
SELECT s.FullName,
       COUNT(e.CourseID) AS TotalCourses,
       AVG(e.Marks) AS AvgMarks
FROM STUDENT s
LEFT JOIN ENROLLMENT e ON s.StudentID = e.StudentID
GROUP BY s.FullName;

-- 9. Top 3 students by average marks
SELECT s.FullName, AVG(e.Marks) AS AvgMarks
FROM STUDENT s
JOIN ENROLLMENT e ON s.StudentID = e.StudentID
GROUP BY s.FullName
ORDER BY AvgMarks DESC
LIMIT 3;

-- 10. Departments having more than 2 students
SELECT d.DeptName, COUNT(s.StudentID) AS TotalStudents
FROM DEPARTMENT d
JOIN STUDENT s ON d.DeptID = s.DeptID
GROUP BY d.DeptName
HAVING COUNT(s.StudentID) > 2;

-- 11. Students with marks between 60 and 85
SELECT s.FullName, e.Marks
FROM STUDENT s
JOIN ENROLLMENT e ON s.StudentID = e.StudentID
WHERE e.Marks BETWEEN 60 AND 85;

-- 12. Students name starts with 'S' or contains 'a'
SELECT FullName
FROM STUDENT
WHERE FullName LIKE 'S%' OR FullName LIKE '%a%';
