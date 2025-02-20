# What is SOLID?

SOLID is an acronym **for the first five object-oriented design (OOD)** principles by Robert C. Martin.

These principles establish practices for developing software with considerations for maintaining and extending it as the project grows.


## S: Single Responsibility Principle 

**Definition:**

*Souce:*
[Link](https://www.techtarget.com/whatis/definition/Single-Responsibility-Principle-SRP)
[Link](https://www.freecodecamp.org/news/solid-principles-single-responsibility-principle-explained/)
[Link](https://www.digitalocean.com/community/conceptual-articles/s-o-l-i-d-the-first-five-principles-of-object-oriented-design)

> [!NOTE]
> A class should have one and only one reason to change, meaning that a class should have only one job.
> The idea behind the SRP is that every class, module, or function in a program should have one responsibility/purpose in a program
> The Single Responsibility Principle (SRP) is the concept that any single object in object-oriented programing (OOP) should be made for one specific function.

SRP is intended to help make code modular with fewer difficulties arising from inter-dependencies. Making code more modular and packaged into functions helps make it more reusable and helps avoid time wasted recoding what has already been done. Adoption of SRP is supposed to help when updating code as there are fewer points of concern when a need is found to update a certain function.

### Example

```java
public class Student {

     public void registerStudent() {
         // some logic
     }

     public void calculate_Student_Results() {
         // some logic
     }

     public void sendEmail() {
         // some logic
     }

}
```

The class above violates the single responsibility principle. Why?

**This `Student` class has three responsibilities – registering students, calculating their results, and sending out emails to students.**

The code above will work perfectly but will lead to some challenges. We cannot make this code reusable for other classes or objects. The class has a whole lot of logic interconnected that we would have a hard time fixing errors. And as the codebase grows, so does the logic, making it even harder to understand what is going on.

**Now, let's fix it!**

```java
public class StudentRegister {
    public void registerStudent(){
        // Some logic here...
    }
}
```

```java
public class StudentResult {
    public void calculateStudentResult(){
        // Some logic here...
    }
}
```

```java
public class StudentEmail {
    public void sendEmail() {
        // Some logic here...
    }
}
```

> [!IMPORTANT]
> The examples we used just showed each class having one method – this is mainly for simplicity. **You can have as many methods as you want but they should be linked to the responsibility of the class.**

