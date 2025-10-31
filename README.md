Part (a): Connecting to MySQL and Fetching Data from Employee Table
Code: 

import java.sql.*;
public class FetchEmployeeData {
    public static void main(String[] args) {
        String url = "jdbc:mysql://localhost:3306/testdb"; // Replace with your DB name
        String user = "root"; // Replace with your MySQL username
        String password = "yourpassword"; // Replace with your MySQL password

        try {
            // 1. Load JDBC Driver
            Class.forName("com.mysql.cj.jdbc.Driver");

            // 2. Establish Connection
            Connection con = DriverManager.getConnection(url, user, password);

            // 3. Create Statement
            Statement stmt = con.createStatement();

            // 4. Execute Query
            String query = "SELECT EmpID, Name, Salary FROM Employee";
            ResultSet rs = stmt.executeQuery(query);

            // 5. Display Results
            System.out.println("EmpID\tName\t\tSalary");
            System.out.println("---------------------------------");
            while (rs.next()) {
                int id = rs.getInt("EmpID");
                String name = rs.getString("Name");
                double salary = rs.getDouble("Salary");

                System.out.println(id + "\t" + name + "\t\t" + salary);
            }

            // 6. Close Resources
            rs.close();
            stmt.close();
            con.close();

        } catch (Exception e) {
            e.printStackTrace();
        }
    }
}


Part (b): CRUD Operations on Product Table Using JDBC
Code:

import java.sql.*;
import java.util.Scanner;

public class ProductCRUD {
    static final String URL = "jdbc:mysql://localhost:3306/testdb";
    static final String USER = "root";
    static final String PASSWORD = "yourpassword";

    public static void main(String[] args) {
        Scanner sc = new Scanner(System.in);

        try (Connection con = DriverManager.getConnection(URL, USER, PASSWORD)) {
            Class.forName("com.mysql.cj.jdbc.Driver");

            while (true) {
                System.out.println("\n=== PRODUCT MANAGEMENT MENU ===");
                System.out.println("1. Add Product");
                System.out.println("2. View Products");
                System.out.println("3. Update Product");
                System.out.println("4. Delete Product");
                System.out.println("5. Exit");
                System.out.print("Enter your choice: ");
                int choice = sc.nextInt();

                switch (choice) {
                    case 1 -> addProduct(con, sc);
                    case 2 -> viewProducts(con);
                    case 3 -> updateProduct(con, sc);
                    case 4 -> deleteProduct(con, sc);
                    case 5 -> {
                        System.out.println("Exiting...");
                        sc.close();
                        System.exit(0);
                    }
                    default -> System.out.println("Invalid choice!");
                }
            }

        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    private static void addProduct(Connection con, Scanner sc) throws SQLException {
        String sql = "INSERT INTO Product (ProductName, Price, Quantity) VALUES (?, ?, ?)";
        try (PreparedStatement ps = con.prepareStatement(sql)) {
            System.out.print("Enter Product Name: ");
            sc.nextLine();
            String name = sc.nextLine();
            System.out.print("Enter Price: ");
            double price = sc.nextDouble();
            System.out.print("Enter Quantity: ");
            int qty = sc.nextInt();

            ps.setString(1, name);
            ps.setDouble(2, price);
            ps.setInt(3, qty);

            ps.executeUpdate();
            System.out.println("Product added successfully!");
        }
    }

    private static void viewProducts(Connection con) throws SQLException {
        String sql = "SELECT * FROM Product";
        try (Statement st = con.createStatement();
             ResultSet rs = st.executeQuery(sql)) {
            System.out.println("ProductID\tName\t\tPrice\tQuantity");
            while (rs.next()) {
                System.out.println(rs.getInt("ProductID") + "\t\t" +
                        rs.getString("ProductName") + "\t\t" +
                        rs.getDouble("Price") + "\t" +
                        rs.getInt("Quantity"));
            }
        }
    }

    private static void updateProduct(Connection con, Scanner sc) throws SQLException {
        con.setAutoCommit(false);
        try {
            String sql = "UPDATE Product SET ProductName=?, Price=?, Quantity=? WHERE ProductID=?";
            try (PreparedStatement ps = con.prepareStatement(sql)) {
                System.out.print("Enter Product ID to update: ");
                int id = sc.nextInt();
                sc.nextLine();
                System.out.print("Enter new Product Name: ");
                String name = sc.nextLine();
                System.out.print("Enter new Price: ");
                double price = sc.nextDouble();
                System.out.print("Enter new Quantity: ");
                int qty = sc.nextInt();

                ps.setString(1, name);
                ps.setDouble(2, price);
                ps.setInt(3, qty);
                ps.setInt(4, id);

                int rows = ps.executeUpdate();
                if (rows > 0) {
                    con.commit();
                    System.out.println("Product updated successfully!");
                } else {
                    con.rollback();
                    System.out.println("Product not found!");
                }
            }
        } catch (Exception e) {
            con.rollback();
            e.printStackTrace();
        } finally {
            con.setAutoCommit(true);
        }
    }

    private static void deleteProduct(Connection con, Scanner sc) throws SQLException {
        con.setAutoCommit(false);
        try {
            String sql = "DELETE FROM Product WHERE ProductID=?";
            try (PreparedStatement ps = con.prepareStatement(sql)) {
                System.out.print("Enter Product ID to delete: ");
                int id = sc.nextInt();

                ps.setInt(1, id);
                int rows = ps.executeUpdate();

                if (rows > 0) {
                    con.commit();
                    System.out.println("Product deleted successfully!");
                } else {
                    con.rollback();
                    System.out.println("Product not found!");
                }
            }
        } catch (Exception e) {
            con.rollback();
            e.printStackTrace();
        } finally {
            con.setAutoCommit(true);
        }
    }
}

Part (c): Student Management Application Using MVC Architecture


public class Student {
    private int studentID;
    private String name;
    private String department;
    private double marks;

    public Student(int studentID, String name, String department, double marks) {
        this.studentID = studentID;
        this.name = name;
        this.department = department;
        this.marks = marks;
    }

    public Student(String name, String department, double marks) {
        this.name = name;
        this.department = department;
        this.marks = marks;
    }

    public int getStudentID() { return studentID; }
    public String getName() { return name; }
    public String getDepartment() { return department; }
    public double getMarks() { return marks; }

    public void setName(String name) { this.name = name; }
    public void setDepartment(String department) { this.department = department; }
    public void setMarks(double marks) { this.marks = marks; }
}
