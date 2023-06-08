# Attendancse Sheet

```rust
module my_addrx::AttendanceSheet{
    
    use std::vector;

    struct Students has store, key, drop , copy
    {
        students: vector<Student>,
    }
    struct Student has store, key, drop , copy
    {
        age: u8,
        fName: vector<u8>,
        lName: vector<u8>,
        attendanceValue: u8,
        rollNo: u8
    }

    public fun create_student(_student: Student,_students: &mut Students): Student{
        let newStudent = Student {
        age: _student.age,
        fName: _student.fName,
        lName: _student.lName,
        attendanceValue: _student.attendanceValue,
        rollNo: _student.rollNo,
        };
        add_student(_students,newStudent);
        return newStudent
    }

    public fun add_student(_students: &mut Students,_student: Student){
        vector::push_back(&mut _students.students,_student);
    }

    public fun incrementAttendance(student : &mut Student){
        student.attendanceValue = student.attendanceValue + 1;
    }

    public fun getParticularStudent(student : &Student):&Student{
        return student
    }

    public fun getTotalNoOfStudent(student : &Students):u64{
        let totalStudent = vector::length(&student.students);
        return totalStudent
    }

    #[test]
    fun test_create_student()
    {
        let harry = Student{
            age: 31,
            fName: b"Harry",
            lName: b"Potter",
            attendanceValue: 0,
            rollNo: 1
        };
        let stud = Students{
            students : (vector[harry]),
        };
        let carry = Student{
            age: 31,
            fName: b"Carry",
            lName: b"Minati",
            attendanceValue: 0,
            rollNo: 2
        };
        let stud2 = Students{
            students : (vector[carry])
        };
        let yoyo = Student{
            age: 31,
            fName: b"Honey",
            lName: b"Singh",
            attendanceValue: 0,
            rollNo: 3
        };
        let stud3 = Students{
            students : (vector[yoyo])
        };

        let createdStud = create_student(harry,&mut stud);
        assert!(createdStud.fName == harry.fName,0);

        let createdStud2 = create_student(carry,&mut stud2);
        let createdStud3 = create_student(carry,&mut stud3);

        assert!(createdStud2.rollNo == carry.rollNo,0);

        incrementAttendance(&mut createdStud);//1
        incrementAttendance(&mut createdStud2);//2
        incrementAttendance(&mut createdStud3);//3
        incrementAttendance(&mut createdStud);//1
        incrementAttendance(&mut createdStud2);//2
        incrementAttendance(&mut createdStud);//1

        let p_stud1 = getParticularStudent(&mut createdStud);
        let p_stud2 = getParticularStudent(&mut createdStud2);
        let p_stud3 = getParticularStudent(&mut createdStud3);

        assert!(p_stud1.attendanceValue == 3,0);
        assert!(p_stud2.attendanceValue == 2,0);
        assert!(p_stud3.attendanceValue == 1,0);
        let _total = getTotalNoOfStudent(&mut stud3);
    }
}
```
