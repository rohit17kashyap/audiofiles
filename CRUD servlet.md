##### **Here's a complete CRUD (Create, Read, Update, Delete) flow using HTML + Servlet + JDBC + DAO + POJO + JSP for a Student Management System**





**StudentCRUD**

**│**

**├── src**

**│   ├── com.model**

**│   │     └── Student.java**

**│   ├── com.dao**

**│   │     └── StudentDAO.java**

**│   ├── com.util**

**│   │     └── DBConnection.java**

**│   └── com.servlet**

**│         ├── AddStudentServlet.java**

**│         ├── ViewStudentServlet.java**

**│         ├── EditStudentServlet.java**

**│         ├── UpdateStudentServlet.java**

**│         └── DeleteStudentServlet.java**

**│**

**├── WebContent**

**│   ├── index.html**

**│   ├── view.jsp**

**│   ├── edit.jsp**

**│   └── WEB-INF**

**│       └── web.xml**

**│**

**└── mysql-connector-j.jar**





**Database**



CREATE DATABASE studentdb;



USE studentdb;



CREATE TABLE student(

&#x20;   rollno INT PRIMARY KEY,

&#x20;   name VARCHAR(50),

&#x20;   course VARCHAR(50)

);





**1. Student.java (POJO)**



package com.model;



public class Student {

&#x20;   private int rollno;

&#x20;   private String name;

&#x20;   private String course;



&#x20;   public Student() {}



&#x20;   public Student(int rollno, String name, String course) {

&#x20;       this.rollno = rollno;

&#x20;       this.name = name;

&#x20;       this.course = course;

&#x20;   }



&#x20;   public int getRollno() {

&#x20;       return rollno;

&#x20;   }



&#x20;   public void setRollno(int rollno) {

&#x20;       this.rollno = rollno;

&#x20;   }



&#x20;   public String getName() {

&#x20;       return name;

&#x20;   }



&#x20;   public void setName(String name) {

&#x20;       this.name = name;

&#x20;   }



&#x20;   public String getCourse() {

&#x20;       return course;

&#x20;   }



&#x20;   public void setCourse(String course) {

&#x20;       this.course = course;

&#x20;   }

}





**2. DBConnection.java**



package com.util;



import java.sql.Connection;

import java.sql.DriverManager;



public class DBConnection {



&#x20;   public static Connection getConnection() {



&#x20;       Connection con = null;



&#x20;       try {

&#x20;           Class.forName("com.mysql.cj.jdbc.Driver");



&#x20;           con = DriverManager.getConnection(

&#x20;                   "jdbc:mysql://localhost:3306/studentdb",

&#x20;                   "root",

&#x20;                   "root");



&#x20;       } catch (Exception e) {

&#x20;           e.printStackTrace();

&#x20;       }



&#x20;       return con;

&#x20;   }

}





**3. StudentDAO.java**





package com.dao;



import java.sql.\*;

import java.util.ArrayList;



import com.model.Student;

import com.util.DBConnection;



public class StudentDAO {



&#x20;   public int save(Student s) {



&#x20;       int status = 0;



&#x20;       try {

&#x20;           Connection con = DBConnection.getConnection();



&#x20;           PreparedStatement ps =

&#x20;                   con.prepareStatement(

&#x20;                   "insert into student values(?,?,?)");



&#x20;           ps.setInt(1, s.getRollno());

&#x20;           ps.setString(2, s.getName());

&#x20;           ps.setString(3, s.getCourse());



&#x20;           status = ps.executeUpdate();



&#x20;       } catch (Exception e) {

&#x20;           e.printStackTrace();

&#x20;       }



&#x20;       return status;

&#x20;   }



&#x20;   public ArrayList<Student> getAllStudents() {



&#x20;       ArrayList<Student> list = new ArrayList<>();



&#x20;       try {

&#x20;           Connection con = DBConnection.getConnection();



&#x20;           PreparedStatement ps =

&#x20;                   con.prepareStatement("select \* from student");



&#x20;           ResultSet rs = ps.executeQuery();



&#x20;           while (rs.next()) {



&#x20;               Student s = new Student();



&#x20;               s.setRollno(rs.getInt("rollno"));

&#x20;               s.setName(rs.getString("name"));

&#x20;               s.setCourse(rs.getString("course"));



&#x20;               list.add(s);

&#x20;           }



&#x20;       } catch (Exception e) {

&#x20;           e.printStackTrace();

&#x20;       }



&#x20;       return list;

&#x20;   }



&#x20;   public Student getStudentById(int rollno) {



&#x20;       Student s = null;



&#x20;       try {

&#x20;           Connection con = DBConnection.getConnection();



&#x20;           PreparedStatement ps =

&#x20;                   con.prepareStatement(

&#x20;                   "select \* from student where rollno=?");



&#x20;           ps.setInt(1, rollno);



&#x20;           ResultSet rs = ps.executeQuery();



&#x20;           if (rs.next()) {



&#x20;               s = new Student();



&#x20;               s.setRollno(rs.getInt("rollno"));

&#x20;               s.setName(rs.getString("name"));

&#x20;               s.setCourse(rs.getString("course"));

&#x20;           }



&#x20;       } catch (Exception e) {

&#x20;           e.printStackTrace();

&#x20;       }



&#x20;       return s;

&#x20;   }



&#x20;   public int update(Student s) {



&#x20;       int status = 0;



&#x20;       try {

&#x20;           Connection con = DBConnection.getConnection();



&#x20;           PreparedStatement ps =

&#x20;                   con.prepareStatement(

&#x20;                   "update student set name=?,course=? where rollno=?");



&#x20;           ps.setString(1, s.getName());

&#x20;           ps.setString(2, s.getCourse());

&#x20;           ps.setInt(3, s.getRollno());



&#x20;           status = ps.executeUpdate();



&#x20;       } catch (Exception e) {

&#x20;           e.printStackTrace();

&#x20;       }



&#x20;       return status;

&#x20;   }



&#x20;   public int delete(int rollno) {



&#x20;       int status = 0;



&#x20;       try {

&#x20;           Connection con = DBConnection.getConnection();



&#x20;           PreparedStatement ps =

&#x20;                   con.prepareStatement(

&#x20;                   "delete from student where rollno=?");



&#x20;           ps.setInt(1, rollno);



&#x20;           status = ps.executeUpdate();



&#x20;       } catch (Exception e) {

&#x20;           e.printStackTrace();

&#x20;       }



&#x20;       return status;

&#x20;   }

}





**4. index.html**



<!DOCTYPE html>

<html>

<body>



<h2>Add Student</h2>



<form action="add" method="post">



Roll No :

<input type="number" name="rollno"><br><br>



Name :

<input type="text" name="name"><br><br>



Course :

<input type="text" name="course"><br><br>



<input type="submit" value="Save">



</form>



<br>



<a href="view">View Students</a>



</body>

</html>



**5. AddStudentServlet.java**



@WebServlet("/add")

public class AddStudentServlet extends HttpServlet {



&#x20;   protected void doPost(HttpServletRequest request,

&#x20;           HttpServletResponse response)

&#x20;           throws ServletException, IOException {



&#x20;       Student s = new Student();



&#x20;       s.setRollno(

&#x20;               Integer.parseInt(

&#x20;               request.getParameter("rollno")));



&#x20;       s.setName(

&#x20;               request.getParameter("name"));



&#x20;       s.setCourse(

&#x20;               request.getParameter("course"));



&#x20;       StudentDAO dao = new StudentDAO();



&#x20;       dao.save(s);



&#x20;       response.sendRedirect("view");

&#x20;   }

}





**6. ViewStudentServlet.java**



@WebServlet("/view")

public class ViewStudentServlet extends HttpServlet {



&#x20;   protected void doGet(HttpServletRequest request,

&#x20;           HttpServletResponse response)

&#x20;           throws ServletException, IOException {



&#x20;       StudentDAO dao = new StudentDAO();



&#x20;       request.setAttribute(

&#x20;               "students",

&#x20;               dao.getAllStudents());



&#x20;       request.getRequestDispatcher("view.jsp")

&#x20;               .forward(request, response);

&#x20;   }

}







**7. EditStudentServlet.java**





@WebServlet("/edit")

public class EditStudentServlet extends HttpServlet {



&#x20;   protected void doGet(HttpServletRequest request,

&#x20;           HttpServletResponse response)

&#x20;           throws ServletException, IOException {



&#x20;       int rollno =

&#x20;               Integer.parseInt(

&#x20;               request.getParameter("rollno"));



&#x20;       StudentDAO dao = new StudentDAO();



&#x20;       Student s =

&#x20;               dao.getStudentById(rollno);



&#x20;       request.setAttribute("student", s);



&#x20;       request.getRequestDispatcher("edit.jsp")

&#x20;               .forward(request, response);

&#x20;   }

}







**8. UpdateStudentServlet.java**





@WebServlet("/update")

public class UpdateStudentServlet extends HttpServlet {



&#x20;   protected void doPost(HttpServletRequest request,

&#x20;           HttpServletResponse response)

&#x20;           throws ServletException, IOException {



&#x20;       Student s = new Student();



&#x20;       s.setRollno(

&#x20;               Integer.parseInt(

&#x20;               request.getParameter("rollno")));



&#x20;       s.setName(

&#x20;               request.getParameter("name"));



&#x20;       s.setCourse(

&#x20;               request.getParameter("course"));



&#x20;       StudentDAO dao = new StudentDAO();



&#x20;       dao.update(s);



&#x20;       response.sendRedirect("view");

&#x20;   }

}





**9. DeleteStudentServlet.java**





@WebServlet("/delete")

public class DeleteStudentServlet extends HttpServlet {



&#x20;   protected void doGet(HttpServletRequest request,

&#x20;           HttpServletResponse response)

&#x20;           throws ServletException, IOException {



&#x20;       int rollno =

&#x20;               Integer.parseInt(

&#x20;               request.getParameter("rollno"));



&#x20;       StudentDAO dao = new StudentDAO();



&#x20;       dao.delete(rollno);



&#x20;       response.sendRedirect("view");

&#x20;   }

}





**10. view.jsp**



<%@ page import="java.util.\*,com.model.Student"%>



<html>

<body>



<h2>Student List</h2>



<a href="index.html">Add New Student</a>



<br><br>



<table border="1">



<tr>

<th>RollNo</th>

<th>Name</th>

<th>Course</th>

<th>Edit</th>

<th>Delete</th>

</tr>



<%

ArrayList<Student> list =

(ArrayList<Student>)request.getAttribute("students");



for(Student s:list){

%>



<tr>



<td><%=s.getRollno()%></td>

<td><%=s.getName()%></td>

<td><%=s.getCourse()%></td>



<td>

<a href="edit?rollno=<%=s.getRollno()%>">

Edit

</a>

</td>



<td>

<a href="delete?rollno=<%=s.getRollno()%>">

Delete

</a>

</td>



</tr>



<%

}

%>



</table>



</body>

</html>





**11. edit.jsp**





<%@ page import="com.model.Student"%>



<%

Student s =

(Student)request.getAttribute("student");

%>



<html>

<body>



<h2>Update Student</h2>



<form action="update" method="post">



Roll No :

<input type="text"

name="rollno"

value="<%=s.getRollno()%>"

readonly>



<br><br>



Name :

<input type="text"

name="name"

value="<%=s.getName()%>">



<br><br>



Course :

<input type="text"

name="course"

value="<%=s.getCourse()%>">



<br><br>



<input type="submit"

value="Update">



</form>



</body>

</html>





**12.web.xml**



<?xml version="1.0" encoding="UTF-8"?>



<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"

&#x20;        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"

&#x20;        xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee

&#x20;        http://xmlns.jcp.org/xml/ns/javaee/web-app\_4\_0.xsd"

&#x20;        version="4.0">



&#x20;   <display-name>StudentCRUD</display-name>



&#x20;   <welcome-file-list>

&#x20;       <welcome-file>index.html</welcome-file>

&#x20;   </welcome-file-list>



&#x20;   <!-- Add Student -->

&#x20;   <servlet>

&#x20;       <servlet-name>AddStudentServlet</servlet-name>

&#x20;       <servlet-class>com.servlet.AddStudentServlet</servlet-class>

&#x20;   </servlet>



&#x20;   <servlet-mapping>

&#x20;       <servlet-name>AddStudentServlet</servlet-name>

&#x20;       <url-pattern>/add</url-pattern>

&#x20;   </servlet-mapping>



&#x20;   <!-- View Student -->

&#x20;   <servlet>

&#x20;       <servlet-name>ViewStudentServlet</servlet-name>

&#x20;       <servlet-class>com.servlet.ViewStudentServlet</servlet-class>

&#x20;   </servlet>



&#x20;   <servlet-mapping>

&#x20;       <servlet-name>ViewStudentServlet</servlet-name>

&#x20;       <url-pattern>/view</url-pattern>

&#x20;   </servlet-mapping>



&#x20;   <!-- Edit Student -->

&#x20;   <servlet>

&#x20;       <servlet-name>EditStudentServlet</servlet-name>

&#x20;       <servlet-class>com.servlet.EditStudentServlet</servlet-class>

&#x20;   </servlet>



&#x20;   <servlet-mapping>

&#x20;       <servlet-name>EditStudentServlet</servlet-name>

&#x20;       <url-pattern>/edit</url-pattern>

&#x20;   </servlet-mapping>



&#x20;   <!-- Update Student -->

&#x20;   <servlet>

&#x20;       <servlet-name>UpdateStudentServlet</servlet-name>

&#x20;       <servlet-class>com.servlet.UpdateStudentServlet</servlet-class>

&#x20;   </servlet>



&#x20;   <servlet-mapping>

&#x20;       <servlet-name>UpdateStudentServlet</servlet-name>

&#x20;       <url-pattern>/update</url-pattern>

&#x20;   </servlet-mapping>



&#x20;   <!-- Delete Student -->

&#x20;   <servlet>

&#x20;       <servlet-name>DeleteStudentServlet</servlet-name>

&#x20;       <servlet-class>com.servlet.DeleteStudentServlet</servlet-class>

&#x20;   </servlet>



&#x20;   <servlet-mapping>

&#x20;       <servlet-name>DeleteStudentServlet</servlet-name>

&#x20;       <url-pattern>/delete</url-pattern>

&#x20;   </servlet-mapping>



</web-app>





## **CRUD Flow**



**CREATE**



index.html

&#x20;   ↓

AddStudentServlet

&#x20;   ↓

DAO.save()

&#x20;   ↓

MySQL



**READ**



ViewStudentServlet

&#x20;   ↓

DAO.getAllStudents()

&#x20;   ↓

view.jsp



**UPDATE**



EditStudentServlet

&#x20;   ↓

edit.jsp

&#x20;   ↓

UpdateStudentServlet

&#x20;   ↓

DAO.update()



**DELETE**



DeleteStudentServlet

&#x20;   ↓

DAO.delete()























