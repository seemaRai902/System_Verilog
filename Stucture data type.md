#### using stucture data type

```system verilog
module stuctures;

  // Define an enumeration for person types
  typedef enum {Student, Employee, Retired} person_type;

  // Define a packed structure for person details
  typedef struct {
      byte id;          // Salary of the person
      string name;         // Name of the person
      person_type person;  // Type of person (Student, Employee, Retired)
  } details_t; // Create a type alias for the structure

  initial begin
      // Declare a variable of the structure type
      details_t d1;

      // Assign values to the structure fields
      d1.id = 'h44;
      d1.name = "Shinchan";
      d1.person =+1 ;// = Student; // Use the enumeration value directly 
      
      // Display the details
      $display("Name: %s", d1.name);
    $display("ID: %0d", d1.id);
      $display("Person Type: %s", (d1.person == Student) ? "Student" : 
                                   (d1.person == Employee) ? "Employee" : 
                                   "Retired");
  end
endmodule
```

#### Output:
# run -all
# Name: Shinchan
# ID: 68
# Person Type: Employee
# exit
