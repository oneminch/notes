---
alias: Data Access Object
---

- Data Access Objects (DAOs) are a design pattern commonly used in [[Java]] programs to manage the interaction between the application logic and the data persistence layer.
    - They provide an abstraction layer that separates the business logic from the underlying data access mechanisms, such as databases or APIs.
- **Benefits**
    - Allows data access mechanisms to change independently of the code that uses the information.
    - Can provide access to a particular resource without coupling to the business logic.
    - Allows for no direct relation to the data resource access mechanism.
    - Follows a clean and standardized interface performing CRUD Operations.
    - Help abstract the database operations, provide a layer of security, and ensure data integrity.
- To apply the DAO pattern to an application:
    - Create the class for entity that the DAO is going to be based on. (e.g. `Employee`)
    - Define a DAO interface with the desired CRUD operation methods.
    - Create subclasses to implement the interface.

```java
public interface EmployeeDao{
    // Add a new employee
    public Employee createEmployee(Employee newEmployee);

    // Find all employees
    public List<Employee> findAllEmployees();

    // Find a specific employee
    public Employee findEmployeeByEmail(String email);

    /* ... */
}

public class EmployeeDaoImpl implements EmployeeDao {
    /* ... */

    @Override
    public Employee findEmployeeByEmail(String email) {
        String sql = "SELECT * FROM employees WHERE email = ?";

        try(Connection conn = ConnectionFactory.getConnectionFactory().getConnection()){

            PreparedStatement ps = conn.prepareStatement(sql);

            ps.setString(1, email);

            ResultSet rs = ps.executeQuery();

            if(!rs.next()){
                throw new InvalidUserInputException("Employee Not Found");
            }

            Employee emp = new Employee();

            emp.setEmail(rs.getString(1));
            emp.setPassword(rs.getString(2));
            emp.setFullName(rs.getString(3));

            return employee;
        } catch (SQLException e){
            e.printStackTrace();
        }

        return null;
    }

    /* ... */
}
```
