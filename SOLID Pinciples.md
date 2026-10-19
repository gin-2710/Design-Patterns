# SOLID Principles

The SOLID principles were first introduced by the famous Computer Scientist Robert J. Martin (a.k.a Uncle Bob) in his paper in 2000. But the SOLID acronym was introduced later by Michael Feathers.
The five principles are:

- The Single Responsibility Principle
- The Open-Closed Principle
- The Liskov Substitution Principle
- The Interface Segregation Principle
- The Dependency Inversion Principle

## S - Single Responsibility Principle (SRP)

The Single Responsibility Principle states that a class should do one thing and therefore it should have only a single reason to change.

This means that only one potential change (database logic, logging logic, and so on.) in the software’s specification should be able to affect the specification of the class.

For example, If a class is a data container, like a Book class or a Student class, and it has some fields regarding that entity, it should change only when we change the data model.

### Why do we need Single Responsibility Principle?

- **Enhanced Manageability and Maintainability:** By following SRP, a class is designed to have only one reason to change, which makes it easier for different teams to work on the same project without interfering with each other's changes. This reduces the likelihood of incompatible modules.

- **Improved Version Control:** When a class has a single responsibility, it's clear what changes have been made and why. For example, if a persistence class handles database operations, any changes to that file can be easily traced back to storage or database-related work. This clarity helps in identifying the source of changes and reduces the time spent on debugging.

- **Reduced Merge Conflicts:** When different teams work on the same project, conflicts often arise due to changes in the same file. However, if SRP is followed, files will have a single reason to change, and conflicts that do occur will be easier to resolve. This is because the changes will be more localized and related to specific functionalities rather than being spread across multiple unrelated responsibilities.

### Example - Simple

#### Before Applying SRP

``` Java
class Report {
    String content;

    void generateReport() {
        // report generation logic
    }

    void saveToFile() {
        // save to file logic
    }
}
```

#### After Applying SRP

``` Java
// src/main/java/com/example/ReportGenerator.java
public class ReportGenerator {
    private Report report;

    public ReportGenerator(Report report) {
        this.report = report;
    }

    void generateReport() {
        // report generation logic
        System.out.println("Generating report...");
    }
}

// src/main/java/com/example/ReportSaver.java
public class ReportSaver {
    private Report report;

    public ReportSaver(Report report) {
        this.report = report;
    }

    void saveToFile() {
        // save to file logic
        System.out.println("Saving report to file...");
    }
}

// src/main/java/com/example/Report.java
public class Report {
    private String content;

    public Report(String content) {
        this.content = content;
    }

    public String getContent() {
        return content;
    }
}
```

##### Explanation

1. **ReportGenerator:** This class is responsible for generating the report.
2. **ReportSaver:** This class is responsible for saving the report to a file.
3. **Report:** This class remains unchanged and now only contains the report content.

### Example - Detailed

#### Before Applying SRP

``` Java
// src/main/java/com/example/UserManager.java
public class UserManager {
    public void saveUser(User user) {
        // Code to save user to database
    }

    public void deleteUser(User user) {
        // Code to delete user from database
    }

    public void sendEmail(User user, String message) {
        // Code to send email
    }

    public void logUserActivity(User user, String activity) {
        // Code to log user activity
    }
}
```

In this example, the UserManager class has four responsibilities:

1. Saving a user to the database.
2. Deleting a user from the database.
3. Sending an email.
4. Logging user activity.

#### After Applying SRP

``` Java
// src/main/java/com/example/UserRepository.java
public class UserRepository {
    public void saveUser(User user) {
        // Code to save user to database
    }

    public void deleteUser(User user) {
        // Code to delete user from database
    }
}

// src/main/java/com/example/EmailService.java
public class EmailService {
    public void sendEmail(User user, String message) {
        // Code to send email
    }
}

// src/main/java/com/example/ActivityLogger.java
public class ActivityLogger {
    public void logUserActivity(User user, String activity) {
        // Code to log user activity
    }
}

// src/main/java/com/example/UserManager.java
public class UserManager {
    private UserRepository userRepository;
    private EmailService emailService;
    private ActivityLogger activityLogger;

    public UserManager(UserRepository userRepository, EmailService emailService, ActivityLogger activityLogger) {
        this.userRepository = userRepository;
        this.emailService = emailService;
        this.activityLogger = activityLogger;
    }

    public void saveUser(User user) {
        userRepository.saveUser(user);
    }

    public void deleteUser(User user) {
        userRepository.deleteUser(user);
    }

    public void sendEmail(User user, String message) {
        emailService.sendEmail(user, message);
    }

    public void logUserActivity(User user, String activity) {
        activityLogger.logUserActivity(user, activity);
    }
}
```

##### Explanation

1. **UserRepository:** This class is responsible for all database operations related to users.
2. **EmailService:** This class is responsible for sending emails.
3. **ActivityLogger:** This class is responsible for logging user activities.

By breaking down the `UserManager` class into multiple classes, each with a single responsibility, we have improved the modularity and maintainability of the code. This separation of concerns makes it easier to test, modify, and debug individual components of the system.

## O - Open/Closed Principle (OCP)

The Open-Closed Principle requires that classes should be open for extension and closed to modification. Modification means changing the code of an existing class, and extension means adding new functionality.

So what this principle wants to say is: We should be able to add new functionality without touching the existing code for the class. This is because whenever we modify the existing code, we are taking the risk of creating potential bugs. So we should avoid touching the tested and reliable (mostly) production code if possible.

It is usually done with the help of interfaces and abstract classes.

#### Before Applying OCP

Let's assume we have a simple `Shape` class and a method to calculate the area of different shapes. However, if we need to add more shapes in the future, we would have to modify the calculateArea method, which violates the OCP.

``` Java
// src/main/java/com/example/Shape.java
public interface Shape {
    double getArea();
}

// src/main/java/com/example/Main.java
public class Main {
    public static void main(String[] args) {
        Shape[] shapes = {
            ShapeFactory.createShape("Circle", 5),
            ShapeFactory.createShape("Rectangle", 4, 6)
        };

        for (Shape shape : shapes) {
            System.out.println("Area: " + shape.getArea());
        }
    }
}

// src/main/java/com/example/ShapeFactory.java
public class ShapeFactory {
    public static Shape createShape(String type, double... dimensions) {
        switch (type) {
            case "Circle":
                return new Circle(dimensions[0]);
            case "Rectangle":
                return new Rectangle(dimensions[0], dimensions[1]);
            default:
                throw new IllegalArgumentException("Unknown shape type");
        }
    }
}

// src/main/java/com/example/Circle.java
public class Circle implements Shape {
    private double radius;

    public Circle(double radius) {
        this.radius = radius;
    }

    @Override
    public double getArea() {
        return Math.PI * radius * radius;
    }
}

// src/main/java/com/example/Rectangle.java
public class Rectangle implements Shape {
    private double width;
    private double height;

    public Rectangle(double width, double height) {
        this.width = width;
        this.height = height;
    }

    @Override
    public double getArea() {
        return width * height;
    }
}
```


#### After Applying OCP

To adhere to the OCP, we can use an interface and a factory to extend the functionality without modifying existing code.

``` Java
// src/main/java/com/example/Shape.java
public interface Shape {
    double getArea();
}

// src/main/java/com/example/Main.java
public class Main {
    public static void main(String[] args) {
        Shape[] shapes = {
            ShapeFactory.createShape("Circle", 5),
            ShapeFactory.createShape("Rectangle", 4, 6)
        };

        for (Shape shape : shapes) {
            System.out.println("Area: " + shape.getArea());
        }
    }
}

// src/main/java/com/example/ShapeFactory.java
public class ShapeFactory {
    public static Shape createShape(String type, double... dimensions) {
        switch (type) {
            case "Circle":
                return new Circle(dimensions[0]);
            case "Rectangle":
                return new Rectangle(dimensions[0], dimensions[1]);
            default:
                throw new IllegalArgumentException("Unknown shape type");
        }
    }
}

// src/main/java/com/example/Circle.java
public class Circle implements Shape {
    private double radius;

    public Circle(double radius) {
        this.radius = radius;
    }

    @Override
    public double getArea() {
        return Math.PI * radius * radius;
    }
}

// src/main/java/com/example/Rectangle.java
public class Rectangle implements Shape {
    private double width;
    private double height;

    public Rectangle(double width, double height) {
        this.width = width;
        this.height = height;
    }

    @Override
    public double getArea() {
        return width * height;
    }
}
```

##### Explanation

1. **Shape Interface:** This interface defines the getArea method, which all shapes must implement.
2. **ShapeFactory Class:** This factory class is responsible for creating instances of different shapes. This way, you can easily extend the functionality by adding new shape types without modifying the existing code.
3. **Circle and Rectangle Classes:** These classes implement the Shape interface and provide the specific logic for calculating their areas.

By following the OCP, we have made the code more flexible and maintainable. If we need to add a new shape in the future, we can simply extend the ShapeFactory class without modifying the existing Circle and Rectangle classes.

## L - Liskov Substitution Principle (LSP)

The Liskov Substitution Principle states that objects of a superclass should be replaceable with objects of a subclass without affecting the correctness of the program. In simpler terms, if a class is a subclass of another, it should behave in a way that is indistinguishable from the superclass.

This means that, given that class B is a subclass of class A, we should be able to pass an object of class B to any method that expects an object of class A and the method should not give any weird output in that case.

This is the expected behavior, because when we use inheritance we assume that the child class inherits everything that the superclass has. The child class extends the behavior but never narrows it down.

Therefore, when a class does not obey this principle, it leads to some nasty bugs that are hard to detect.

### Example

#### Before Applying LSP

``` Java
class Bird {
    void fly() {
        // fly logic
    }
}

class Penguin extends Bird {
    // Penguins can't fly
    void fly() {
        throw new UnsupportedOperationException("Penguins can't fly");
    }
}
```

`Penguin` inherits from `Bird` but cannot substitute for it without throwing an exception when `fly()` is invoked.

#### After Applying LSP

``` Java
// Base class for shared bird attributes and behaviors
abstract class Bird {
    // common behaviors, e.g., eat(), sleep()
}

// Interface for birds that can fly
interface Flyable {
    void fly();
}

// Flying bird implementation
class Sparrow extends Bird implements Flyable {
    @Override
    public void fly() {
        // flight logic
    }
}

// Flightless bird implementation
class Penguin extends Bird {
    // only inherits valid Bird behaviors; no fly() method exposed
}
```

- Any code expecting a `Bird` will never call `fly()`on an instance that cannot fly.
- Flight capability is extracted into `Flyable`, allowing non-flying birds (like ostriches or penguins) to avoid inheriting incompatible methods.
- Design-level constraints prevent invalid calls at compile time instead of throwing `UnsupportedOperationException` at runtime.

## I - Interface Segregation Principle (ISP)

The principle states that many client-specific interfaces are better than one general-purpose interface. Clients should not be forced to implement a function they do no need.

### Example

#### Before Applying ISP

Let's assume we have an interface for a `Printer` that includes both printing and faxing capabilities. This violates ISP because a client that only needs printing functionality will still be forced to implement faxing methods.

``` Java
// src/main/java/com/example/Printer.java
public interface Printer {
    void print();
    void fax();
}

// src/main/java/com/example/AllInOnePrinter.java
public class AllInOnePrinter implements Printer {
    @Override
    public void print() {
        System.out.println("Printing...");
    }

    @Override
    public void fax() {
        System.out.println("Faxing...");
    }
}

// src/main/java/com/example/PrintOnlyPrinter.java
public class PrintOnlyPrinter implements Printer {
    @Override
    public void print() {
        System.out.println("Printing...");
    }

    @Override
    public void fax() {
        throw new UnsupportedOperationException("Faxing not supported");
    }
}
```

#### After Applying ISP

To adhere to ISP, we can break down the Printer interface into smaller, more specific interfaces.

``` Java
// src/main/java/com/example/Printable.java
public interface Printable {
    void print();
}

// src/main/java/com/example/Faxable.java
public interface Faxable {
    void fax();
}

// src/main/java/com/example/AllInOnePrinter.java
public class AllInOnePrinter implements Printable, Faxable {
    @Override
    public void print() {
        System.out.println("Printing...");
    }

    @Override
    public void fax() {
        System.out.println("Faxing...");
    }
}

// src/main/java/com/example/PrintOnlyPrinter.java
public class PrintOnlyPrinter implements Printable {
    @Override
    public void print() {
        System.out.println("Printing...");
    }
}
```

## D - Dependency Inversion Principle

The Dependency Inversion Principle states that high-level modules should not depend on low-level modules, but both should depend on abstractions. Additionally, abstractions should not depend on details; details should depend on abstractions.

This means that our classes should depend upon interfaces or abstract classes instead of concrete classes and functions.

In his article (2000), Uncle Bob summarizes this principle as follows:

>"If the OCP states the goal of OO architecture, the DIP states the primary mechanism".

### Example:

``` Java
// Interface for version control system
interface IVersionControl {
    void commit(String message);
    void push();
    void pull();
}

// Git version control implementation
class GitVersionControl implements IVersionControl {
    @Override
    public void commit(String message) {
        System.out.println("Committing changes to Git with message: " + message);
    }

    @Override
    public void push() {
        System.out.println("Pushing changes to remote Git repository.");
    }

    @Override
    public void pull() {
        System.out.println("Pulling changes from remote Git repository.");
    }
}

// Team class that relies on version control
class DevelopmentTeam {
    private IVersionControl versionControl;

    public DevelopmentTeam(IVersionControl vc) {
        this.versionControl = vc;
    }

    public void makeCommit(String message) {
        versionControl.commit(message);
    }

    public void performPush() {
        versionControl.push();
    }

    public void performPull() {
        versionControl.pull();
    }
}

public class Main {
    public static void main(String[] args) {
        GitVersionControl git = new GitVersionControl();
        DevelopmentTeam team = new DevelopmentTeam(git);

        team.makeCommit("Initial commit");
        team.performPush();
        team.performPull();
    }
}
```

## References

1. [The SOLID Principles of Object-Oriented Programming Explained in Plain English - FreeCodeCamp](https://www.freecodecamp.org/news/solid-principles-explained-in-plain-english/)
2. [Mastering SOLID Principles: A Comprehensive Guide for Software Engineers - Medium](https://medium.com/@GetInRhythm/mastering-solid-principles-a-comprehensive-guide-for-software-engineers-da53b054c9e1)
3. [SOLID Principles with Real Life Examples - GeeksForGeeks](https://www.geeksforgeeks.org/system-design/solid-principle-in-programming-understand-with-real-life-examples/)