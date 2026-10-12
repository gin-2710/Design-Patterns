## The Problem
``` Java
// Class Phone
public class Phone {
  private:
    String os;
    int ram;
    String processor;
    double screenSize;
    int battery;

    public Phone(String os, int ram, String processor, double screenSize, int battery) {
      super();
      this.os = os;
      this.ram = ram;
      this.processor = processor;
      this.screenSize = screenSize;
      this.battery = battery;
    }

    @Override
    public String toString() {
        return "Phone [os=" + os + ", ram=" + ram + ", processor=" + processor + ", screenSize=" + screenSize
                + ", battery=" + battery + "]";
    }
}

// Class shop
public class Shop {
  public static void main(String args[]) {
    // Using constructor to initialize Phone object
    Phone p = new Phone("Android", 8, "Mediatek", 5.3, 4000);
  }
}
```
In the example above, all the data needed to create an instance of the Phone class must be passed to the constructor.
- The constructor takes 5 parameters and they must be passed in the correct order when creating an instance of the Phone class.
- It is also mandatory for the caller (in this case, the Shop class) to pass all the 5 parameters to the constructor.
- This can be tedious when the caller must create hundreds of Phone instances.

## Using the Builder Pattern
``` Java
// Class Phone
public class Phone {
  private:
    String os;
    int ram;
    String processor;
    double screenSize;
    int battery;

    public Phone(String os, int ram, String processor, double screenSize, int battery) {
      super();
      this.os = os;
      this.ram = ram;
      this.processor = processor;
      this.screenSize = screenSize;
      this.battery = battery;
    }

    @Override
    public String toString() {
        return "Phone [os=" + os + ", ram=" + ram + ", processor=" + processor + ", screenSize=" + screenSize
                + ", battery=" + battery + "]";
    }
}

// Class PhoneBuider
public class PhoneBuider {
  private:
    String os;
    int ram;
    String processor;
    double screenSize;
    int battery;

    public PhoneBuider setOs(String os) {
      this.os = os;
      return this;
    }
    
    public PhoneBuider setRam(int ram) {
      this.ram = ram;
      return this;
    }
    
    public PhoneBuider setProcessor(String processor) {
      this.processor = processor;
      return this;
    }

    public PhoneBuider setScreenSize(double screenSize) {
      this.screenSize = screenSize;
      return this;
    }

    public PhoneBuider setBattery(int battery) {
      this.battery = battery;
      return this;
    }

    public Phone getPhone() {
      return new Phone(os, ram, processor, screenSize, battery);
    }
}

// Class shop
public class Shop {
  public static void main(String args[]) {
    // Using the builder class
    Phone p = new PhoneBuilder().setOs("Android").setRam(2).setBattery(4000).getPhone();
  }
}
```
- Using the builder pattern, the caller would not have to worry about the order of parameters to be passed to the constructor to create an instance of the Phone class.
- Additionally, the caller now has the flexibility to set only the parameters they require rather than pass all the mandatory parameters.
