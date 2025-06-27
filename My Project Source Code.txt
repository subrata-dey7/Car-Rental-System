
// PROJECT: CAR RENTAL SYSYTEM

package Projects.CarRentalSystem;

import java.util.ArrayList;
import java.util.List;
import java.util.Scanner;

class Car {
    private String carId;
    private String brand;
    private String model;
    private double basePricePerDay;  // use double so that we can use decimal values
    private boolean isAvailable;

    /* Constructor (Automatically called when object is created)
    (We can also use a method, but have to write one extra line) */
    public Car (String carId, String brand, String model, double basePricePerDay) {
        this.carId = carId;
        this.brand = brand;
        this.model = model;
        this.basePricePerDay = basePricePerDay;
        this.isAvailable = true; // (By default = We keep this as true)
        // (As the car is available for rent now)
    }

    public String getCarId () {
        return carId;
    }

    public String getBrand () {
        return brand;
    }

    public String getModel () {
        return model;
    }

    public double calculatePrice (int rentalDays) {
        double basePrice = basePricePerDay * rentalDays;
        double discountPrice = 0.0;

        // Apply discount if rental is longer than 7 days
        if (rentalDays > 7) {
            discountPrice = basePrice * 0.10;   // 10% discount
        }

        // Apply GST (e.g., 18%)
        double gst = basePrice * 0.18;

        return (basePrice + gst - discountPrice);
    }

    public boolean isAvailable () {
        return isAvailable;
    }

    // When the car isn't available for rent
    public void rent () {
        isAvailable = false;
    }

    public void returnCar () {
        isAvailable = true;
    }

    public double getBasePricePerDay () {
        return basePricePerDay;
    }
}

class Customer {
    private String customerId;
    private String name;

    public Customer (String name, String customerId) {
        this.name = name;
        this.customerId = customerId;
    }

    public String getName () {
        return name;
    }

    public String getCustomerId () {
        return customerId;
    }
}

class Rental {
    private Car car; // car is an object of class type Car)
    private Customer customer;
    private int days;

    public Rental (Car car, Customer customer, int days) {
        this.car = car;
        this.customer = customer;
        this.days = days;
    }

    public Car getCar () {
        return car;
    }

    public Customer getCustomer () {
        return customer;
    }

    public int getDays () {
        return days;
    }
}

class CarRentalSystem {
    // We have to include several cars, so use "ArrayList" to store them 
    // (as we dont know Database) (Best is - Database)
    private List <Car> cars = new ArrayList <> (); // Stores Cars data
    private List <Customer> customers = new ArrayList <> ();  // Stores Customers data
    private List <Rental> rentals = new ArrayList <> ();
    // Stores data of which customer rents which car
    
    public void addCar (Car car) {
        cars.add(car);
    }

    public void addCustomer (Customer customer) {
        customers.add(customer);
    }

    // Renting a car
    public void rentCar (Car car, Customer customer, int days) {
        if (car.isAvailable()) {  // if this condition is true, then call car.rent()
            car.rent(); // this makes rent() false ie car is rented
            rentals.add(new Rental (car, customer, days));
        } else {
            System.out.println("Car is not available for rent");
        }
    }

    // Return a Car
    public void returnCar (Car car) {
        car.returnCar(); // Calling returnCar() becomes true makes the car available 
        Rental rentalToRemove = null; // intitialize the reference to null so that no 
        // random value is stored already

        // Use Enhanced for loop to check the car rented in the arraylist
        // num takes on each value from the array numbers in sequence.
        for (Rental rental : rentals) {
            if (rental.getCar() == car) { /* Whether the car is present in the rent list 
                which is we want to return */
                rentalToRemove = rental;
                break;
            }
        }
        if (rentalToRemove != null) {
            rentals.remove(rentalToRemove);
        } else {
            System.out.println("Car was not Rented");
        }
    }

    // Add lists to store customer system-wide ratings and feedbacks:
    private List <Double> systemRatings = new ArrayList<>(); 
    private List <String> systemFeedbacks = new ArrayList<>();

    public void addSystemFeedback (double rating, String feedback) {
        if (rating >= 1.0 && rating <= 5.0) {
            systemRatings.add(rating);
            systemFeedbacks.add(feedback);
        }
    }

    public double getAverageSystemRating () {
        if (systemRatings.isEmpty()) {
            return 0.0;
        }
        double total = 0;
        for (double r : systemRatings) {
            total += r; 
        }
        return Math.round((total / systemRatings.size()) * 10.0) / 10.0;
    }

    public void showSystemFeedback () {
        System.out.println("\n ======= System Feedback ======= \n");
        System.out.printf("Average System Rating : %.1f / 5\n",getAverageSystemRating());
        for (String feedback : systemFeedbacks) {
            System.out.println("- " + feedback);
        }
    }

    public void menu() {
        Scanner scanner = new Scanner (System.in);

        while (true) {
            System.out.println("\n========== Car Rental System ============\n");
            System.out.println("Book more than 7 days to avail === EXTRA 10% DISCOUNT ===\n");
            System.out.println("1. Rent a Car");
            System.out.println("2. Return a Car");
            System.out.println("3. Add / Remove Car");
            System.out.println("4. Give Rating and Feedback");
            System.out.println("5. Exit");
            System.out.print("Enter your Choice : ");

            int choice = scanner.nextInt();
            scanner.nextLine();  // consume newline

            if (choice == 1) {
                System.out.println("\n=== Rent a Car ===\n");
                System.out.print("Enter Your Name : ");
                String customerName = scanner.nextLine();

                System.out.println("\nAvailable Cars : \n");
                for (Car car : cars) {
                    if (car.isAvailable()) {
                        System.out.println(car.getCarId() + " - " + car.getBrand() + " " + car.getModel() + " (Rs " + car.getBasePricePerDay() + ")");
                    }
                }
                System.out.print("\nEnter The Car Id you want to Rent : ");
                String carId = scanner.nextLine();

                System.out.print("Enter The no of days for Rental : ");
                int rentalDays = scanner.nextInt();
                scanner.nextLine();  // consume newline

                Customer newCustomer = new Customer (customerName, "CustID-" + (customers.size() + 1));
                addCustomer(newCustomer);

                Car selectedCar = null;
                for (Car car : cars) {  // cars is the arrayList
                    if (car.getCarId().equals(carId) && car.isAvailable()) {
                        // check if getCarId equals with carId & car is available ?
                        selectedCar = car;
                        break;
                    }
                }

                if (selectedCar != null) {
                    double totalPrice = selectedCar.calculatePrice(rentalDays);

                    // Calculate breakdown
                    double basePrice = selectedCar.getBasePricePerDay() * rentalDays;
                    boolean discountApplied = rentalDays > 7 ;

                    double discountPrice = 0.0; 
                    /* discountAmount is both declared and initialized. */

                    if (discountApplied) {
                        discountPrice = basePrice * 0.10 ;  // Apply 10% discount
                    }
                    double gst = basePrice * 0.18;

                    System.out.println("\n === Rental Information === ");
                    System.out.println("\nCustomer ID : " + newCustomer.getCustomerId());
                    System.out.println("Customer Name : " + newCustomer.getName());
                    System.out.println("Car : " + selectedCar.getBrand() + " " + selectedCar.getModel());
                    System.out.println("Rental Days : " + rentalDays);
                    System.out.printf("Base Price = Rs %.2f%n", basePrice);
                    System.out.printf("GST (18%%) = Rs %.2f%n", gst);
                    /* why %% ? To print a literal percent sign (%), you need to escape it by writing %%.*/
                    if (discountApplied) {
                        System.out.println("Discount Applied (10%) = " + discountPrice + " (for rental over 7 days)");
                    }
                    System.out.printf("Total Price : Rs %.2f%n", totalPrice);

                    System.out.print("\nConfirm Rental (Y/N) : ");
                    String confirm = scanner.nextLine();

                    if (confirm.equalsIgnoreCase("Y")) {
                        rentCar (selectedCar, newCustomer, rentalDays);
                        System.out.println("\nCar Rented Successfully");
                    } else {
                        System.out.println("Rental Cancelled");
                    }
                } else {
                    System.out.println("\nInvalid Car selection or Car is not available for Rent");
                }
            } else if (choice == 2) {
                System.out.println("\n === Return A Car === \n");
                System.out.print("Enter the car ID you want to return : ");
                String carId = scanner.nextLine();

                Car carToReturn = null;
                for (Car car : cars) {
                    if (car.getCarId().equals(carId) && !car.isAvailable()) {
                        carToReturn = car;
                        break;
                    }
                }

                if (carToReturn != null) {
                    Customer customer = null;
                    for (Rental rental : rentals) {
                        if (rental.getCar() == carToReturn) {
                            customer = rental.getCustomer();
                            break;
                        }
                    }
                    if (customer != null) {
                        returnCar(carToReturn);
                        System.out.println("Car Returned successfully by " + customer.getName());
                    } else {
                        System.out.println("Car was not Rented or rental information is missing");
                    }
                } else {
                    System.out.println("Invalid car ID or car is not rented");
                }
            } else if (choice == 3) {
                System.out.println("\n ======= Add / Remove Car =======");
                System.out.println("\n1. Add a Car");
                System.out.println("2. Remove a Car");
                System.out.print("Enter your Option : ");
                int subChoice = scanner.nextInt();
                scanner.nextLine();    // consume newline

                if (subChoice == 1) {
                    System.out.print("\nEnter CarId : ");
                    String id = scanner.nextLine();
                    System.out.print("Enter Brand : ");
                    String brand = scanner.nextLine();
                    System.out.print("Enter Model : ");
                    String model = scanner.nextLine();
                    System.out.print("Enter Base Price Per Day : ");
                    double price = scanner.nextDouble();
                    scanner.nextLine();    // consume newline

                    Car newCar = new Car(id,brand,model,price);  // memory is allocated
                    addCar(newCar);
                    System.out.println("\nCar is added successfully....");

                } else if (subChoice == 2) {
                    System.out.print("\nEnter CarId to Remove : ");
                    String id = scanner.nextLine();

                    Car carToRemove = null;
                    for (Car car : cars) {
                        if (car.getCarId().equals(id)) {
                            carToRemove = car;
                            break;
                        }
                    }

                    if (carToRemove != null && carToRemove.isAvailable()) {
                        cars.remove(carToRemove);
                        System.out.println("Car is Removed successfully !!!");
                    } else if (carToRemove != null) {
                        System.out.println("Car is currently Rented. Can not remove");
                    } else {
                        System.out.println("Car Id not found");
                    }
                } else {
                    System.out.println("Invalid Option...");
                }
            } else if (choice == 4) {
                System.out.println("\n ====== Rate the Car Rental System ======\n");
                System.out.print("Enter Rating (1.0 to 5.0): ");
                double rating = scanner.nextDouble();
                scanner.nextLine();   // consume newline

                System.out.print("Leave your feedback : ");
                String feedback = scanner.nextLine();

                addSystemFeedback(rating, feedback);
                System.out.println("\nThank you for rating the system !!!");

            } else if (choice == 5) {
                break;
            } else {
                System.out.println("Invalid choice. Please Enter a valid option.");
            }
        }
        scanner.close();
        System.out.println("\nThank you for Using the CAR RENTAL SYSTEM !!!");
        System.out.println("Have a Nice Day.....");
    }
}

public class Main {
    public static void main(String[] args) {

        CarRentalSystem rentalSystem = new CarRentalSystem();

        Car car1 = new Car("C001","Hyundai","Santro",2250.0);
        Car car2 = new Car("C002","Maruti","Swift",3600.0);
        Car car3 = new Car("C003","Maruti","Baleno",5400.0);
        Car car4 = new Car("C004","Toyota","Innova",7000.0);
        Car car5 = new Car("C005","Maruti","Vitara",6000.0);
        Car car6 = new Car("C006","Hyundai","Creta",4000.0);
        Car car7 = new Car("C007","Maruti","Ertiga",3500.0);
        Car car8 = new Car("C008","Tata","Nexon",3800.0);
        Car car9 = new Car("C009","Honda","City",4500.0);
        Car car10 = new Car("C010","Toyota","Glanza",3200.0);
    
        rentalSystem.addCar(car1);  // car1 added
        rentalSystem.addCar(car2);  // car2 added  
        rentalSystem.addCar(car3);  // car3 added
        rentalSystem.addCar(car4);  
        rentalSystem.addCar(car5);   
        rentalSystem.addCar(car6);
        rentalSystem.addCar(car7);  
        rentalSystem.addCar(car8);   
        rentalSystem.addCar(car9);
        rentalSystem.addCar(car10);

        rentalSystem.menu();
    }
}