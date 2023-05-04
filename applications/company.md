# Company

* Basic example for better understanding of operation and struct implementation.
* `Test` are also called for the `public` function like: `create_employee`, `decrease_income`, and `is_employee_age_even`.

```rust
module 0x42::CompanyEmployee{

    const CONTRACT:address = @0x42;
    
    use std::vector;

    const CONTRACT:address = @0x42;

    struct Employees has store, key, drop , copy
    {
        people: vector<Employee>
    }
    struct Employee has store, key, drop , copy
    {
        name: vector<u8>,
        age: u8,
        income: u64,
    }

    public fun create_employee(_employee: Employee,_employees: &mut Employees): Employee{
        let newEmploye = Employee {
        name: _employee.name,
        age: _employee.age,
        income: _employee.income,
        };
        add_employee(_employees,newEmploye);
        return newEmploye
    }

    public fun add_employee(_employees: &mut Employees,_employee: Employee){
        vector::push_back(&mut _employees.people,_employee)
    }

    public fun increase_income(employee : &mut Employee,bonus: u64):&mut Employee{
        employee.income = employee.income + bonus;
        return employee
    }

    public fun decrease_income(employee : &mut Employee,penalty: u64):&mut Employee{
        employee.income = employee.income - penalty;
        return employee
    }

    public fun multiple_income(employee : &mut Employee,factor: u64):&mut Employee{
        employee.income = employee.income * factor;
        return employee
    }

    public fun divide_income(employee : &mut Employee,dividor: u64):&mut Employee{
        employee.income = employee.income / dividor;
        return employee
    }

    public fun is_employee_age_even(employee: &Employee): bool{
        let isEven: bool;

        if(employee.age % 2 == 0){
            isEven = true;
        } else {
            isEven = false;
        };
        return isEven
    }

    #[test]
    fun test_create_employee()
    {
        let richand = Employee{
            name: b"Richard",
            age: 31,
            income: 100
        };
        let employees = Employees{
            people : (vector[richand])
        };
        let createdEmployee = create_employee(richand,&mut employees);
        assert!(createdEmployee.name == richand.name,0)
    }

    #[test]
    fun test_decrease_income()
    {
        let richand = Employee{
            name: b"Richard",
            age: 31,
            income: 100
        };
        let employees = Employees{
            people : (vector[richand])
        };
        let createdEmployee = create_employee(richand,&mut employees);
        let dereasedincome = decrease_income(&mut createdEmployee,51);
        assert!(dereasedincome.income == 49,0)
    }

    #[test]
    fun test_is_employee_age_even()
    {
        let richand = Employee{
            name: b"Richard",
            age: 31,
            income: 100
        };
        let employees = Employees{
            people : (vector[richand])
        };
        let createdEmployee = create_employee(richand,&mut employees);
        let isEvens = is_employee_age_even(&mut createdEmployee);
        assert!(isEvens == false,0)
    }
}
```

## Here the another example using `Friends` declaration:

* `CompanyInfo` contains `get_Info` function which called `SisterCompany` module.
* `get_Info` function calls `get_company_name` function from `SisterCompany` module.
* Test are also written for verification.

```rust
module 0x42::CompanyInfo{

    const CONTRACT:address = @0x42;

    struct Info has drop
    {
        company_name: vector<u8>,
        owns: vector<u8>
    }

    public fun get_Info(): Info {
        let sisterCompanyName = 0x42::SisterCompany::get_company_name();

        let info = Info{
            company_name: b"the comapany",
            owns: sisterCompanyName,
        };
        return info
    }

    #[test]
    fun test_get_Info()
    {
        let check_info = Info{
            company_name: b"Richard",
            owns: b"Sister Company"
        };
        let real_info = get_Info();

        assert!(check_info.owns == real_info.owns,0);
    }
}
```

```rust
module 0x42::SisterCompany{
    friend 0x42::CompanyInfo;

    public(friend) fun get_company_name():vector<u8>{
        return b"Sister Company"
    }
} 
```
