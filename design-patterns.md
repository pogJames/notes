# CREATIONAL PATTERNS

## 1. Singleton
Sometimes you need exactly one instance of a class throughout your system
- Ensures consistency across the application
- Prevents conflicts from multiple instances
- Useful for shared resources, like a database or config manager
> Like a government, A country can have only one official government and everybody can access it
### Singleton Structure
```bash
+------------------+
|   Singleton      |
|------------------|
| -instance: obj   |
|------------------|
| +getInstance()   |
+------------------+
```
1. The **Singleton** class declares the static method `getInstance` that returns the same instance of its own class
2. The **Singleton**’s constructor should be hidden from the client code -> Calling `getInstance` should be the only way to get the object
### So..
**Singleton** lets you ensure that a class has only one instance, while providing a global access point to this instance

## 2. Builder
Because sometimes creating an object is complex, with multiple optional parts, configurations, or steps
- Avoids a constructor with too many parameters
- Makes object creation clear, flexible, and readable
- Lets the same construction process produce different representations of the object
> Like building computers, gaming/office/server computers uses different parts and specifications
### Builder Structure
```bash
Director
   |
   v
+------------------+
| Builder          |
|------------------|
| buildCPU()       |
| buildRAM()       |
| buildStorage()   |
| getResult()      |
+------------------+
     ^
     |
  --------------------------------------------------
  |                            |                   |
  v                            v                   v
+------------------+  +------------------+  +------------------+
| GamingBuilder    |  | OfficeBuilder    |  | ServerBuilder    |
|------------------|  |------------------|  |------------------|
| buildCPU()       |  | buildCPU()       |  | buildCPU()       |
| buildRAM()       |  | buildRAM()       |  | buildRAM()       |
| buildStorage()   |  | buildStorage()   |  | buildStorage()   |
| addGPU() -> opt  |  | addGPU() -> opt  |  | addGPU() -> opt  |
| getResult()      |  | getResult()      |  | getResult()      |
+------------------+  +------------------+  +------------------+
     |                     |                       |
     v                     v                       v
+------------------+
| Product          |
|------------------|
| CPU              |
| RAM              |
| Storage          |
| GPU (optional)   |
+------------------+
```
1. **Builder** interface/class: Defines methods to create parts of the product
2. **ConcreteBuilder**: Implements the Builder, constructs and assembles the product step by step
3. **Director** (optional): Controls the building sequence using the Builder
4. **Product**: Final object being built

**Builder** lets you construct complex objects step by step, enabling production of different types/representations of an object using the same construction code

## 4. Abstract Factory
### So..
**Factory Method** lets you produce families of related objects without specifying their concrete classes
