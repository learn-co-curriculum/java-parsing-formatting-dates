# Parsing and Formatting Dates

## Learning Goals

- Explain how to parse a date from a `String` object.
- Learn about the `DateTimeFormatter` class and formatting dates.

## Parsing a Date from a String

In the last lesson, we saw how we could construct `LocalDate`, `LocalTime`,
and `LocalDateTime` instances using the static methods `now()` or `of()`. But
what if a user enters a date, and we want to take a `String` object and turn
it into a `LocalDate`?

This is where parsing comes to the rescue! We can get a `LocalDate` object by
parsing a `String` object! The `LocalDate` class uses the default format of
yyyy-MM-dd where the four-digit year, two-digit month, and two-digit day is
separated by hyphens (-). The `parse()` method will take a character sequence
argument and obtain an instance of a `LocalDate` based off of the text.

Let's go back to our `Person` class and modify the `setBirthday()` method to
take in a `String` and see how the `parse()` method works:

```java
import java.time.LocalDate;

public class Person {
    private String name;
    private LocalDate birthday;
    
    public Person() {
        this.name = "";
        this.birthday = LocalDate.now();
    }
    
    public String getName() {
        return name;
    }
    
    public void setName(String name) {
        this.name = name;
    }
    
    public LocalDate getBirthday() {
        return birthday;
    }
    
    public void setBirthday(String birthday) {
        this.birthday = LocalDate.parse(birthday);
    }

    public static void main(String[] args) {
        Person ben = new Person();
        ben.setName("Ben Wyatt");
        ben.setBirthday("1974-11-14");
        System.out.println(ben.getBirthday());
    }
}
```

If we were to run the code above, it would output the following:

```text
1974-11-14
```

In the example above, we feed in the exact default format for the `LocalDate`
class. But what if the format we are trying to parse is different from the
default format? Let's introduce a new class to help us out!

## Java `DateTimeFormatter` Class

The `DateTimeFormatter` class is in the `java.time.format` package and acts as
a formatter for printing and helping parse date-time objects. The
`DateTimeFormatter` class allows us to define different patterns for formatting
and printing out date-time objects. Consider the table below of some common
symbols used in creating a date-time object pattern:

| Symbol | Meaning          | Presentation | Examples            |
|--------|------------------|--------------|---------------------|
| u      | year             | year         | 2004; 04            |
| M/L    | month of year    | number/text  | 7; 07; Jul; July; J |
| d      | day of month     | number       | 10                  |
| E      | day of the week  | text         | Tue; Tuesday; T     |
| H      | hour of day      | number       | 0                   |
| m      | minute of hour   | number       | 30                  |
| s      | second of minute | number       | 55                  |
| n      | nano of second   | number       | 987654321           |

There are many other symbols that we could use to create a format for dates, but
the ones listed above are the ones we will see more frequently. To see a full
list of the pattern symbols, please see the
[Java 17 DateTimeFormatter Class](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/time/format/DateTimeFormatter.html)
under the _Patterns for Formatting and Parsing_ section.

### Formatting a Date

Let's actually start talking about how to create a date format using the
`DateTimeFormatter` class now! The one way to construct a `DateTimeFormatter`
that we will use over and over again is to use the static `toPattern()` method.
The `toPattern()` method will take in a `String` pattern as a parameter and
return a `DateTimeFormatter` object:

```java
import java.time.format.DateTimeFormatter;

public class DateFormattingExample {
    
    public static void main(String[] args) {
        DateTimeFormatter formatter = DateTimeFormatter.ofPattern("MM/dd/uuuu");
    }
}
```

In the example above, the `toPattern()` method created a `DateTimeFormatter`
object based on the symbols we defined above. The `formatter` will now format
dates using the pattern "MM/dd/uuuu". Let's look at another example of where we
might define a `DateTimeFormatter` object to also format a time:

```java
import java.time.format.DateTimeFormatter;

public class DateFormattingExample {
    
    public static void main(String[] args) {
        DateTimeFormatter formatter = DateTimeFormatter.ofPattern("MM/dd/uuuu HH:mm:ss");
    }
}
```

Now that we have defined a `DateTimeFormatter` with a date-time pattern we like,
let's put it to use and format some `LocalDateTime` objects! We'll make use of
the `DateTimeFormatter` class' `format()` method. The `format()` method will
take in a date-time object, like `LocalDateTime`, and then return a formatted
`String`:

```java
import java.time.format.DateTimeFormatter;
import java.time.LocalDateTime;

public class DateFormattingExample {
    
    public static void main(String[] args) {
        DateTimeFormatter formatter = DateTimeFormatter.ofPattern("MM/dd/uuuu HH:mm:ss");
        
        LocalDateTime dateTime = LocalDateTime.now();
        String formattedDate = formatter.format(dateTime);
        System.out.println(formattedDate);    // <-- May print something like this: 08/18/2022 16:21:38

        dateTime = LocalDateTime.of(1955, 11, 5, 6, 15, 0);
        formattedDate = formatter.format(dateTime);
        System.out.println(formattedDate);    // <-- Will print 11/05/1955 06:15:00
    }
}
```

We could even format `LocalTime` and `LocalDate` objects too using the
`format()` method:

```java
import java.time.format.DateTimeFormatter;
import java.time.LocalDate;
import java.time.LocalTime;

public class DateFormattingExample {
    
    public static void main(String[] args) {
        DateTimeFormatter dateFormatter = DateTimeFormatter.ofPattern("EEE LLL dd, uu");
        String formattedDate = formatter.format(LocalDate.now());
        System.out.println(formattedDate);    // <-- May print something like this: Thu Aug 18, 22

        DateTimeFormatter timeFormatter = DateTimeFormatter.ofPattern("HH:mm:ss:nnnnnn");
        String formattedTime = formatter.format(LocalTime.now());
        System.out.println(formattedTime);    // <-- May print something like this: 16:21:38:154360
    }
}
```

### Back to Parsing

Now the question from above still stands: What if the format we are trying to
parse is different from the `LocalDate` class' default format? Luckily for us,
there is another `parse()` method that will take in the character sequence and
a `DateTimeFormatter` argument.

Let's revisit the `setBirthday()` method again in the `Person` class. Only this
time, let's say we expect the birthday to be in the format "MM/dd/uuuu":

```java
public void setBirthday(String birthday) {
    this.birthday = LocalDate.parse(birthday, DateTimeFormatter.ofPattern("MM/dd/uuuu"));
}
```

The `parse()` method, in this case, will obtain an instance of `LocalDate` from
the `birthday` text using the `DateTimeFormatter`. The text will first be
formatted using the `DateTimeFormatter` specified and will then return a date.
Let's tie this all together now:

```java
public static void main(String[] args) {
    Person ben = new Person();
    ben.setName("Ben Wyatt");
    ben.setBirthday("11/14/1974");
    System.out.println(ben.getBirthday());
}
```

Since we are using the `parse()` method that takes in both the `String` and the
`DateTimeFormatter` arguments, the above code will compile and return the same
output as before:

```text
1974-11-14
```

The `parse()` method is not limited to the `LocalDate` class. It also exists
in the `LocalTime` and `LocalDateTime` classes:

```java
import java.time.format.DateTimeFormatter;
import java.time.LocalDateTime;
import java.time.LocalTime;

public class DateFormattingExample {
    
    public static void main(String[] args) {
        DateTimeFormatter dateTimeFormatter = DateTimeFormatter.ofPattern("EEE LLL dd, uu HH:mm:ss");
        LocalDateTime dateTime = LocalDateTime.parse("Thu Aug 18, 22 16:21:38", dateTimeFormatter);
        System.out.println(dateTime);    // <-- Will print 2022-08-18T16:21:38

        DateTimeFormatter timeFormatter = DateTimeFormatter.ofPattern("HH:mm");
        LocalTime time = LocalTime.parse("16:21", timeFormatter);
        System.out.println(time);    // <-- Will print 16:21
    }
}
```

## Validating a Date-Time Object

Say we have a command-line application where we ask a user for a date-time
object. When this occurs, we want to check two things:

1. The date-time object is the correct format we specify.
    1. Example: If we ask the user for a date in "MM/dd/yyyy HH:mm" format, we
       do not want to accept a `String` object like "8-18-2022 16:21".
    2. This will throw a `DateTimeParseException`.
2. The date-time object is a valid date-time.
    1. Example: We don't want to accept a date like this: "09/31/2022 12:00"
       since September only has 30 days in it.

We can use the `DateTimeFormatter` class to validate the `String` is in the
correct format. But first, we will introduce the enum `ResolverStyle`.

The `ResolverStyle` enum has three enum constants: `LENIENT`, `SMART`, and
`STRICT`. When we parse a `String`, it occurs in two phases:

1. "Basic text parse according to the fields."
2. "Resolves the parsed field-value pairs into date and/or time objects."

The `ResolverStyle` is how we control the second phase. Let's look at what each
of these styles do in detail:

```java
import java.time.LocalDateTime;
import java.time.format.ResolverStyle;
import java.time.format.DateTimeFormatter;

public class ValidatingDateTimeExample {

    public static void main(String[] args) {
        
        // Define a formatter with a LENIENT style
        DateTimeFormatter formatter = DateTimeFormatter.ofPattern("MM/dd/uuuu HH:mm:ss").withResolverStyle(ResolverStyle.LENIENT);

        // LENIENT resolves dates and times leniently
        // If we try to parse a date that does not exist, then it will adjust the date-time object
        // For example: if we put in a date of "09/31/2022", it will evaluate to the next month
        LocalDateTime dateTime = LocalDateTime.parse("09/31/2022 12:00:00", formatter);
        System.out.println(dateTime);    // <-- This will output 2022-10-01T12:00

        // Define a formatter with a SMART style; this is the default resolver style of a DateTimeFormatter
        // SMART resolves date and times in a smart, or intelligent, manner
        // This style can sometimes act the same as LENIENT or STRICT
        formatter = formatter.withResolverStyle(ResolverStyle.SMART);
        dateTime = LocalDateTime.parse("09/31/2022 12:00:00", formatter);
        System.out.println(dateTime);    // <-- This will output 2022-09-30T12:00 

        // Define a formatter with STRICT style
        // STRICT resolves date and times strictly and will ensure that all parsed values are valid
        formatter = formatter.withResolverStyle(ResolverStyle.STRICT);
        dateTime = LocalDateTime.parse("09/31/2022 12:00:00", formatter);
        System.out.println(dateTime);    // <-- Throw a DateTimeParseException; Invalid date 'SEPTEMBER 31'

    }
}
```

Understanding how we parse a date-time object from a `String` provides us with
insight in how we can set up a validator. If we want to possibly correct a
user's mistake of an invalid date, then we could use the `SMART` resolver style.
But most of the time we'll want to use the `STRICT` resolver style.

Let's add a method to our example code to add a validator using the
`withResolverStyle()` method we saw above that sets the resolver style. We can
also use the `parse()` method from the `DateTimeFormatter` class to see if the
date-time object will parse correctly.

```java
import java.time.LocalDateTime;
import java.time.format.ResolverStyle;
import java.time.format.DateTimeFormatter;
import java.time.format.DateTimeParseException;

public class ValidatingDateTimeExample {

    public static void main(String[] args) {
        
        // Define a formatter with a STRICT resolver style
        DateTimeFormatter formatter = DateTimeFormatter.ofPattern("MM/dd/uuuu HH:mm:ss").withResolverStyle(ResolverStyle.STRICT);
        
        // Test two String objects to see if they are a valid date-time or not
        String validDateTime = "09/30/2022 12:00:00";
        String invalidDateTime = "09/31/2022 12:00:00";

        // Make sure that the valid date-time String is indeed valid
        if (isValid(validDateTime, formatter)) {
            System.out.println(validDateTime + " is valid! Storing in a LocalDateTime object...");
            LocalDateTime dateTime = LocalDateTime.parse(validDateTime, formatter);
            System.out.println("valid date time: " + dateTime);

        } else {
            System.out.println(validDateTime + " is invalid.");
        }

        // Make sure the invalid date-time String is indeed invalid
        if (isValid(invalidDateTime, formatter)) {
            System.out.println(invalidDateTime + " is valid! Storing in a LocalDateTime object...");
            LocalDateTime dateTime = LocalDateTime.parse(invalidDateTime, formatter);
            System.out.println("invalid date time: " + dateTime);
        } else {
            System.out.println(invalidDateTime + " is invalid.");
        }

    }

   /**
    * Validate a date-time object based on the DateTimeFormatter format
    * @param dateTime : String - text that is to be parsed and is checked for validity
    * @param formatter : DateTimeFormatter - specifies how a date-time object should be formatted
    * @return boolean - returns whether the String dateTime is a valid date-time
    */
    private static boolean isValid(String dateTime, DateTimeFormatter formatter) {
        try {
            formatter.parse(dateTime);
        } catch (DateTimeParseException dateTimeParseException) {
            return false;
        }
        return true;
    }
}
```

The `isValid()` method wil check both the conditions we outlined above to see if
the `String` object is in the correct format and if it is a valid date-time. To
test it, we can feed it two hardcoded values where we know for sure the one is a
valid date-time and the other is an invalid date-time. Here is the output from
the above example:

```text
09/30/2022 12:00:00 is valid! Storing in a LocalDateTime object...
valid date time: 2022-09-30T12:00
09/31/2022 12:00:00 is invalid.
```

## Resources

- [Java 17 DateTimeFormatter Class](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/time/format/DateTimeFormatter.html)
- [Java 17 ResolverStyle Enum](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/time/format/ResolverStyle.html)
