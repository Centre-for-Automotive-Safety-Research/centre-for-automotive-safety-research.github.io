# Public crash data analysis: Understanding typical crash data

Before we dive into downloading and analysing crash data, it’s important to understand how this kind of data is usually organised.

Crashes aren’t just single events with one record. They often involve multiple vehicles and people, each with their own details. For example:

- Imagine a crash involving **two vehicles**.  
- The first vehicle has a **driver and a passenger**.  
- The second vehicle has only a **driver**.  

In this example, the crash involves **one crash record**, **two vehicle records**, and **three people records**. Here's a visual demonstration of this example:

<div class="mermaid">
graph TD
    Crash[Crash];
    Crash --> VehicleA[Vehicle A];
    Crash --> VehicleB[Vehicle B];
    VehicleA --> Driver1[A: Driver];
    VehicleA --> Passenger1[A: Left-front-seat passenger];
    VehicleB --> DriverB[B: Driver];
</div>

Each of these levels has its own characteristics:

- **Crash:** location, date and time, weather conditions, crash type, etc.  
- **Vehicle:** type (car, truck, motorbike, etc.), vehicle age, condition, etc.  
- **Person:** position in vehicle (driver, passenger, etc.), age, sex, injuries, etc.


---

### Multi-level data structures and linking variables

Because these records come in separate tables (crash, vehicle, person), and because the number of records differs at each level, we call this a **multi-level data structure**.

To connect these tables, each record uses **linking variables** (sometimes called keys or IDs). For example:  
- A **Crash ID** connects vehicles to the crash they belong to.  
- A **Vehicle ID** connects people to the vehicle they were in.

This way, we can join data across tables to analyse the full picture.
This is explained later, in [Public crash data analysis: Merging crash data]().
