**1.Student.java(pojo class)**



package com.model;



public class Student {

&#x09;private int rollno;

&#x09;private String name;

&#x09;private String course;



&#x09;public Student() {

&#x09;}



&#x09;public Student(int rollno, String name, String course) {

&#x09;	this.rollno = rollno;

&#x09;	this.name = name;

&#x09;	this.course = course;

&#x09;}



&#x09;public int getRollno() {

&#x09;	return rollno;

&#x09;}



&#x09;public void setRollno(int rollno) {

&#x09;	this.rollno = rollno;

&#x09;}



&#x09;public String getName() {

&#x09;	return name;

&#x09;}



&#x09;public void setName(String name) {

&#x09;	this.name = name;

&#x09;}



&#x09;public String getCourse() {

&#x09;	return course;

&#x09;}



&#x09;public void setCourse(String course) {

&#x09;	this.course = course;

&#x09;}

}





**2.DBConnection.java**





package com.util;



import java.sql.Connection;

import java.sql.DriverManager;



public class DBConnection {



&#x09;public static Connection getConnection() {



&#x09;	Connection con = null;



&#x09;	try {

&#x09;		Class.forName("com.mysql.cj.jdbc.Driver");



&#x09;		con = DriverManager.getConnection("jdbc:mysql://localhost:3306/studentdb", "root", "root");



&#x09;	} catch (Exception e) {

&#x09;		e.printStackTrace();

&#x09;	}



&#x09;	return con;

&#x09;}

}





**3.StudentDAO.java**





package com.dao;



import java.sql.\*;



import java.sql.Connection;

import java.sql.PreparedStatement;

import java.util.ArrayList;



import com.model.Student;

import com.util.DBConnection;



public class StudentDAO {



&#x09;public int save(Student s) {

&#x09;	int status = 0;

&#x09;	try {

&#x09;		Connection con = DBConnection.getConnection();

&#x09;		PreparedStatement ps = con.prepareStatement("insert into student values(?,?,?)");

&#x09;		ps.setInt(1, s.getRollno());

&#x09;		ps.setString(2, s.getName());

&#x09;		ps.setString(3, s.getCourse());

&#x09;		status = ps.executeUpdate();

&#x09;	} catch (Exception e) {

&#x09;		e.printStackTrace();

&#x09;	}

&#x09;	return status;

&#x09;}



&#x09;public ArrayList<Student> getAllStudents() {

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



&#x09;public Student getStudentById(int rollno) {

&#x20;       Student s = null;

&#x20;       try {

&#x20;           Connection con = DBConnection.getConnection();

&#x20;           PreparedStatement ps =

&#x20;                   con.prepareStatement("select \* from student where rollno=?");

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



&#x09;public int update(Student s) {

&#x09;	int status = 0;

&#x09;	try {

&#x09;		Connection con = DBConnection.getConnection();

&#x09;		PreparedStatement ps = con.prepareStatement("update student set name=?,course=? where rollno=?");

&#x09;		ps.setString(1, s.getName());

&#x09;		ps.setString(2, s.getCourse());

&#x09;		ps.setInt(3, s.getRollno());

&#x09;		status = ps.executeUpdate();

&#x09;	} catch (Exception e) {

&#x09;		e.printStackTrace();

&#x09;	}

&#x09;	return status;

&#x09;}



&#x09;public int delete(int rollno) {

&#x09;	int status = 0;

&#x09;	try {

&#x09;		Connection con = DBConnection.getConnection();

&#x09;		PreparedStatement ps = con.prepareStatement("delete from student where rollno=?");

&#x09;		ps.setInt(1, rollno);

&#x09;		status = ps.executeUpdate();

&#x09;	} catch (Exception e) {

&#x09;		e.printStackTrace();

&#x09;	}

&#x09;	return status;

&#x09;}

}





**4.AddStudentServlet.java**





package com.servlet;



import java.io.IOException;



import javax.servlet.ServletException;

import javax.servlet.http.HttpServlet;

import javax.servlet.http.HttpServletRequest;

import javax.servlet.http.HttpServletResponse;

import javax.servlet.http.HttpSession;



import com.dao.StudentDAO;

import com.model.Student;



public class AddStudentServlet extends HttpServlet {



&#x09;protected void doPost(HttpServletRequest request, HttpServletResponse response)

&#x09;		throws ServletException, IOException {



&#x09;	HttpSession session = request.getSession(false);

&#x09;	if (session == null || session.getAttribute("user") == null) {

&#x09;		response.sendRedirect("login.jsp");

&#x09;		return; 

&#x09;	}



&#x09;	Student s = new Student();

&#x09;	s.setRollno(Integer.parseInt(request.getParameter("rollno")));

&#x09;	s.setName(request.getParameter("name"));

&#x09;	s.setCourse(request.getParameter("course"));



&#x09;	StudentDAO dao = new StudentDAO();

&#x09;	dao.save(s);



&#x09;	response.sendRedirect("view");

&#x09;}

}





**5.DeleteStudentServlet.java**





package com.servlet;



import java.io.IOException;



import javax.servlet.ServletException;

import javax.servlet.http.HttpServlet;

import javax.servlet.http.HttpServletRequest;

import javax.servlet.http.HttpServletResponse;

import javax.servlet.http.HttpSession;



import com.dao.StudentDAO;



public class DeleteStudentServlet extends HttpServlet {



&#x09;protected void doGet(HttpServletRequest request, HttpServletResponse response)

&#x09;		throws ServletException, IOException {



&#x09;	HttpSession session = request.getSession(false);

&#x09;	if (session == null || session.getAttribute("user") == null) {

&#x09;		response.sendRedirect("login.jsp");

&#x09;		return;

&#x09;	}



&#x09;	int rollno = Integer.parseInt(request.getParameter("rollno"));



&#x09;	StudentDAO dao = new StudentDAO();

&#x09;	dao.delete(rollno);



&#x09;	response.sendRedirect("view");

&#x09;}

}





**6.EditStudentServlet.java**





package com.servlet;



import java.io.IOException;



import javax.servlet.ServletException;

import javax.servlet.http.HttpServlet;

import javax.servlet.http.HttpServletRequest;

import javax.servlet.http.HttpServletResponse;

import javax.servlet.http.HttpSession;



import com.dao.StudentDAO;

import com.model.Student;



public class EditStudentServlet extends HttpServlet {



&#x09;protected void doGet(HttpServletRequest request, HttpServletResponse response)

&#x09;		throws ServletException, IOException {

&#x09;	

&#x09;	HttpSession session = request.getSession(false);

&#x09;	if (session == null || session.getAttribute("user") == null) {

&#x09;	    response.sendRedirect("login.jsp");

&#x09;	    return;   

&#x09;	}



&#x09;	int rollno = Integer.parseInt(request.getParameter("rollno"));



&#x09;	StudentDAO dao = new StudentDAO();

&#x09;	Student s = dao.getStudentById(rollno);



&#x09;	request.setAttribute("student", s);

&#x09;	request.getRequestDispatcher("edit.jsp").forward(request, response);

&#x09;}

}





**7.LoginServlet.java**





package com.servlet;



import java.io.IOException;

import javax.servlet.ServletException;

import javax.servlet.http.HttpServlet;

import javax.servlet.http.HttpServletRequest;

import javax.servlet.http.HttpServletResponse;

import javax.servlet.http.HttpSession;



public class LoginServlet extends HttpServlet {



&#x09;protected void doPost(HttpServletRequest request, HttpServletResponse response)

&#x09;		throws ServletException, IOException {



&#x09;	String username = request.getParameter("username");

&#x09;	String password = request.getParameter("password");



&#x09;	if ("admin".equals(username) \&\& "admin123".equals(password)) {



&#x09;		HttpSession session = request.getSession(true);



&#x09;		session.setAttribute("user", username);



&#x09;		response.sendRedirect("view");



&#x09;	} else {

&#x09;		response.sendRedirect("login.jsp?error=1");

&#x09;	}

&#x09;}

}





**8.LogoutServlet.java**





package com.servlet;



import java.io.IOException;

import javax.servlet.ServletException;

import javax.servlet.http.HttpServlet;

import javax.servlet.http.HttpServletRequest;

import javax.servlet.http.HttpServletResponse;

import javax.servlet.http.HttpSession;





public class LogoutServlet extends HttpServlet {



&#x09;protected void doGet(HttpServletRequest request, HttpServletResponse response)

&#x09;		throws ServletException, IOException {



&#x09;	HttpSession session = request.getSession(false);



&#x09;	if (session != null) {

&#x09;		session.invalidate(); 

&#x09;	}



&#x09;	response.sendRedirect("login.jsp");

&#x09;}

}







**9.UpdateStudentServlet.java**





package com.servlet;



import java.io.IOException;



import javax.servlet.ServletException;

import javax.servlet.http.HttpServlet;

import javax.servlet.http.HttpServletRequest;

import javax.servlet.http.HttpServletResponse;

import javax.servlet.http.HttpSession;



import com.dao.StudentDAO;

import com.model.Student;



public class UpdateStudentServlet extends HttpServlet {



&#x09;protected void doPost(HttpServletRequest request, HttpServletResponse response)

&#x09;		throws ServletException, IOException {



&#x09;	HttpSession session = request.getSession(false);

&#x09;	if (session == null || session.getAttribute("user") == null) {

&#x09;		response.sendRedirect("login.jsp");

&#x09;		return; 

&#x09;	}



&#x09;	Student s = new Student();

&#x09;	s.setRollno(Integer.parseInt(request.getParameter("rollno")));

&#x09;	s.setName(request.getParameter("name"));

&#x09;	s.setCourse(request.getParameter("course"));



&#x09;	StudentDAO dao = new StudentDAO();

&#x09;	dao.update(s);



&#x09;	response.sendRedirect("view");

&#x09;}

}





**10.ViewStudentServlet.java**





package com.servlet;



import java.io.IOException;



import javax.servlet.ServletException;

import javax.servlet.http.HttpServlet;

import javax.servlet.http.HttpServletRequest;

import javax.servlet.http.HttpServletResponse;

import javax.servlet.http.HttpSession;



import com.dao.StudentDAO;



public class ViewStudentServlet extends HttpServlet {



&#x09;protected void doGet(HttpServletRequest request, HttpServletResponse response)

&#x09;		throws ServletException, IOException {



&#x09;	HttpSession session = request.getSession(false);

&#x09;	if (session == null || session.getAttribute("user") == null) {

&#x09;		response.sendRedirect("login.jsp");

&#x09;		return;

&#x09;	}



&#x09;	StudentDAO dao = new StudentDAO();

&#x09;	request.setAttribute("students", dao.getAllStudents());

&#x09;	request.getRequestDispatcher("view.jsp").forward(request, response);

&#x09;}

}





**11.login.jsp**





<%@ page language="java" contentType="text/html; charset=UTF-8"%>

<!DOCTYPE html>

<html>

<body>



&#x09;<h2>Login</h2>



&#x09;<%

&#x09;// login fail hone par error message dikhao

&#x09;String error = request.getParameter("error");

&#x09;if (error != null) {

&#x09;%>

&#x09;<p style="color: red;">Invalid user\_name or password!</p>

&#x09;<%

&#x09;}

&#x09;%>



&#x09;<form action="login" method="post">



&#x09;	User\_name : <input type="text" name="username"><br>

&#x09;	<br> Password : <input type="password" name="password"><br>

&#x09;	<br> <input type="submit" value="Login">



&#x09;</form>



</body>

</html>





**12.view.jsp**





<%@ page import="java.util.\*,com.model.Student"%>

<%@ page session="true"%>



<%

&#x20;   // logged-in user nikaal lo (session pehle se hai)

&#x20;   String user = (session.getAttribute("user") != null)

&#x20;                   ? (String) session.getAttribute("user")

&#x20;                   : "Guest";

%>



<html>

<body>



&#x09;<!-- 🆕 logged-in user + logout link -->

&#x09;<p>

&#x09;	Welcome, <b><%= user %></b> | <a href="logout">Logout</a>

&#x09;</p>



&#x09;<h2>Student List</h2>



&#x09;<a href="index.html">Add New Student</a>



&#x09;<br>

&#x09;<br>



&#x09;<table border="1">



&#x09;	<tr>

&#x09;		<th>RollNo</th>

&#x09;		<th>Name</th>

&#x09;		<th>Course</th>

&#x09;		<th>Edit</th>

&#x09;		<th>Delete</th>

&#x09;	</tr>



&#x09;	<%

ArrayList<Student> list =

(ArrayList<Student>)request.getAttribute("students");



for(Student s:list){

%>



&#x09;	<tr>

&#x09;		<td><%=s.getRollno()%></td>

&#x09;		<td><%=s.getName()%></td>

&#x09;		<td><%=s.getCourse()%></td>



&#x09;		<td><a href="edit?rollno=<%=s.getRollno()%>">Edit</a></td>

&#x09;		<td><a href="delete?rollno=<%=s.getRollno()%>">Delete</a></td>

&#x09;	</tr>



&#x09;	<%

}

%>



&#x09;</table>



</body>

</html>





**13.edit.jsp**





<%@ page import="com.model.Student"%>



<%

Student s = (Student) request.getAttribute("student");

%>



<html>

<body>



&#x09;<h2>Update Student</h2>



&#x09;<form action="update" method="post">



&#x09;	Roll No : <input type="text" name="rollno" value="<%=s.getRollno()%>"

&#x09;		readonly> <br>

&#x09;	<br> Name : <input type="text" name="name"

&#x09;		value="<%=s.getName()%>"> <br>

&#x09;	<br> Course : <input type="text" name="course"

&#x09;		value="<%=s.getCourse()%>"> <br>

&#x09;	<br> <input type="submit" value="Update">



&#x09;</form>



</body>

</html>





**14.index.html**





<!DOCTYPE html>

<html>

<body>



&#x09;<h2>Add Student</h2>



&#x09;<form action="add" method="post">



&#x09;	Roll No : <input type="number" name="rollno"><br>

&#x09;	<br> Name : <input type="text" name="name"><br>

&#x09;	<br> Course : <input type="text" name="course"><br>

&#x09;	<br> <input type="submit" value="Save">



&#x09;</form>



&#x09;<br>



&#x09;<a href="view">View Students</a>



</body>

</html>





**15.web.xml**





<?xml version="1.0" encoding="UTF-8"?>



<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"

&#x09;xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"

&#x09;xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee

&#x20;        http://xmlns.jcp.org/xml/ns/javaee/web-app\_4\_0.xsd"

&#x09;version="4.0">



&#x09;<display-name>StudentSession</display-name>



&#x09;<welcome-file-list>

&#x09;	<welcome-file>login.jsp</welcome-file>

&#x09;</welcome-file-list>



&#x09;<!-- ===== Login ===== -->

&#x09;<servlet>

&#x09;	<servlet-name>LoginServlet</servlet-name>

&#x09;	<servlet-class>com.servlet.LoginServlet</servlet-class>

&#x09;</servlet>

&#x09;<servlet-mapping>

&#x09;	<servlet-name>LoginServlet</servlet-name>

&#x09;	<url-pattern>/login</url-pattern>

&#x09;</servlet-mapping>



&#x09;<!-- ===== Logout ===== -->

&#x09;<servlet>

&#x09;	<servlet-name>LogoutServlet</servlet-name>

&#x09;	<servlet-class>com.servlet.LogoutServlet</servlet-class>

&#x09;</servlet>

&#x09;<servlet-mapping>

&#x09;	<servlet-name>LogoutServlet</servlet-name>

&#x09;	<url-pattern>/logout</url-pattern>

&#x09;</servlet-mapping>



&#x09;<!-- ===== Add Student ===== -->

&#x09;<servlet>

&#x09;	<servlet-name>AddStudentServlet</servlet-name>

&#x09;	<servlet-class>com.servlet.AddStudentServlet</servlet-class>

&#x09;</servlet>

&#x09;<servlet-mapping>

&#x09;	<servlet-name>AddStudentServlet</servlet-name>

&#x09;	<url-pattern>/add</url-pattern>

&#x09;</servlet-mapping>



&#x09;<!-- ===== View Student ===== -->

&#x09;<servlet>

&#x09;	<servlet-name>ViewStudentServlet</servlet-name>

&#x09;	<servlet-class>com.servlet.ViewStudentServlet</servlet-class>

&#x09;</servlet>

&#x09;<servlet-mapping>

&#x09;	<servlet-name>ViewStudentServlet</servlet-name>

&#x09;	<url-pattern>/view</url-pattern>

&#x09;</servlet-mapping>



&#x09;<!-- ===== Edit Student ===== -->

&#x09;<servlet>

&#x09;	<servlet-name>EditStudentServlet</servlet-name>

&#x09;	<servlet-class>com.servlet.EditStudentServlet</servlet-class>

&#x09;</servlet>

&#x09;<servlet-mapping>

&#x09;	<servlet-name>EditStudentServlet</servlet-name>

&#x09;	<url-pattern>/edit</url-pattern>

&#x09;</servlet-mapping>



&#x09;<!-- ===== Update Student ===== -->

&#x09;<servlet>

&#x09;	<servlet-name>UpdateStudentServlet</servlet-name>

&#x09;	<servlet-class>com.servlet.UpdateStudentServlet</servlet-class>

&#x09;</servlet>

&#x09;<servlet-mapping>

&#x09;	<servlet-name>UpdateStudentServlet</servlet-name>

&#x09;	<url-pattern>/update</url-pattern>

&#x09;</servlet-mapping>



&#x09;<!-- ===== Delete Student ===== -->

&#x09;<servlet>

&#x09;	<servlet-name>DeleteStudentServlet</servlet-name>

&#x09;	<servlet-class>com.servlet.DeleteStudentServlet</servlet-class>

&#x09;</servlet>

&#x09;<servlet-mapping>

&#x09;	<servlet-name>DeleteStudentServlet</servlet-name>

&#x09;	<url-pattern>/delete</url-pattern>

&#x09;</servlet-mapping>



</web-app>







































