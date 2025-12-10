# CREATIONAL PATTERNS

## Singleton

### Problem
Ensures a class has only one instance (e.g., to manage shared resources like a database connection or configuration settings) while providing global access to it  
> Without it, multiple instances could lead to inconsistencies or resource waste...  
> Like a government, A country can have only one official government and everybody must talk to the same one

### Solution
The class controls its own instantiation:  
- a private constructor  
- a static variable to hold the single instance  
- a static method to `get` the instance (creating it if needed)  
> Thread-safety is often added for concurrent environments

### Structure
|Singleton|  
|:---|  
|- instance: Singleton|  
|- Singleton()|  
|+ getInstance(): Singleton|  
```bash
# getInstance()  
if (instance == null) {  
   instance = new Singleton()  
}  
return instance  
```

### Code Examples

#### Python
```python
class Config:
    _instance = None
    
    def __new__(cls):
        if cls._instance is None:
            cls._instance = super().__new__(cls)
            cls._instance.debug = False
            cls._instance.name = "app"
        return cls._instance

# Usage
cfg1 = Config()
cfg2 = Config()
cfg1.debug = True
print(cfg2.debug)  # True - same instance
```

#### Rust
```rust
use once_cell::sync::Lazy;
use std::sync::Mutex;

static CONFIG: Lazy<Mutex<Config>> = Lazy::new(|| {
    Mutex::new(Config { debug: false, name: "gateway".into() })
});

#[derive(Debug)]
struct Config {
    debug: bool,
    name: String,
}

// Usage anywhere
let mut cfg = CONFIG.lock().unwrap();
cfg.debug = true;
```

### Usage
**General**
- Global logger
- Configuration manager

**Technical**
- One Modbus Master per process
- Shared MQTT client or cloud connection pool
- Global device registry

---

## Builder

### Problem
Constructing a complex object step by step. The classic constructor does not allow to construct different representations of the same class.  
> Without it, you'd have telescoping constructors (many overloads with different param combinations) or setters that allow invalid states during construction.  
> Like building a house: you need to add walls, roof, doors step-by-step, and different builders can make different styles (modern vs traditional) from the same process.

### Solution
Separate the construction of a complex object from its representation:  
- A Builder interface with methods for each part (e.g., add_part_a(), add_part_b())  
- Concrete Builders implement the interface for specific variations  
- A Director (optional) to orchestrate the building steps for common recipes  
- The final build() returns the product  
> Allows fluent chaining, immutable products, and reuse of construction logic for different types.

### Structure
| Builder | ConcreteBuilder | Director | Product |
|:---|:---|:---|:---|
| + build_part_a() | - product | - builder | - part_a |
| + build_part_b() | + build_part_a() | + construct() | - part_b |
| + get_result() | + build_part_b() | | |

```bash
# construct()
builder.build_part_a()
builder.build_part_b()
return builder.get_result()
```

### Code Examples

#### Python
```python
class Request:
    def __init__(self, host, port, timeout):
        self.host = host
        self.port = port
        self.timeout = timeout

class RequestBuilder:
    def __init__(self):
        self.host = "localhost"
        self.port = 8080
        self.timeout = 5000

    def host(self, h):    self.host = h;    return self
    def port(self, p):    self.port = p;    return self
    def timeout(self, t): self.timeout = t; return self
    def build(self):      return Request(self.host, self.port, self.timeout)

# Usage
req = RequestBuilder().host("api.com").port(443).timeout(2000).build()
```

#### Rust
```rust
#[derive(Debug)]
struct Request { host: String, port: u16, timeout: u64 }

struct RequestBuilder {
    host: String, port: u16, timeout: u64,
}

impl RequestBuilder {
    fn new() -> Self {
        Self { host: "localhost".into(), port: 8080, timeout: 5000 }
    }
    fn host(mut self, h: &str) -> Self { self.host = h.into(); self }
    fn port(mut self, p: u16) -> Self { self.port = p; self }
    fn timeout(mut self, t: u64) -> Self { self.timeout = t; self }
    fn build(self) -> Request { Request { host: self.host, port: self.port, timeout: self.timeout } }
}

// Usage
let req = RequestBuilder::new()
    .host("192.168.1.10")
    .port(502)
    .timeout(1000)
    .build();
```

### Usage
**General**
- Complex immutable objects
- API clients with many options

**Technical**
- Building Modbus requests
- Configuring serial/TCP connections
- Creating telemetry payloads

---

## Prototype

### Problem
Creating an object is expensive or complex (e.g., involves database queries, file loading, or heavy computations), and you need many similar objects with minor variations.  
> Without it, you'd repeat the costly creation process each time, leading to performance issues or code duplication.  
> Like photocopying a form: instead of filling a new blank form from scratch every time, copy a pre-filled template and just change the name/date.

### Solution
Specify the kinds of objects to create using a prototypical instance, and create new objects by copying this prototype:  
- Implement a clone() method that performs a deep copy of the object state  
- Clients clone the prototype and customize the clone as needed  
- Often uses a registry of prototypes for easy access by type  
> Avoids subclasses for variations and reduces initialization overhead.

### Structure
| Prototype |
|:---|
| + clone(): Prototype |
| - state |

```bash
# usage
prototype = load_expensive_template()
new_obj = prototype.clone()
new_obj.modify_specific_fields()
```

### Code Examples

#### Python
```python
import copy

class Device:
    def __init__(self):
        self.id = 0
        self.name = "sensor"
        self.enabled = True

    def clone(self):
        return copy.deepcopy(self)

# Template
template = Device()

dev1 = template.clone()
dev1.id = 101

dev2 = template.clone()
dev2.id = 102
```

#### Rust
```rust
#[derive(Clone, Debug)]
struct Device {
    id: u32,
    name: String,
    enabled: bool,
}

// Template
let template = Device { id: 0, name: "sensor".into(), enabled: true };

let dev1 = template.clone();
let dev2 = template.clone();
```

### Usage
**General**
- Expensive object creation
- Template-based instances

**Technical**
- Provisioning many identical Modbus devices
- Fast boot from pre-configured template
- Test fixtures

---

## Factory Method

### Problem
A class cannot anticipate the class of objects it must create, or subclasses may need to specify the objects they create, to keep code flexible and extensible.  
> Without it, you'd hardcode concrete classes, violating open-closed principle and making changes difficult (e.g., swapping implementations requires editing client code).  
> Like a car factory: the base factory defines how to assemble a car, but subclasses decide if it's a sedan, SUV, or truck.

### Solution
Define an interface for creating an object, but let subclasses decide which class to instantiate:  
- Creator class declares the factory_method() that returns a Product interface  
- ConcreteCreators override factory_method() to return specific ConcreteProducts  
- Clients use the Creator without knowing the concrete types  
> Promotes loose coupling and parallel class hierarchies (creators and products).

### Structure
| Creator | ConcreteCreator | Product | ConcreteProduct |
|:---|:---|:---|:---|
| + factory_method(): Product | + factory_method(): Product | + operation() | + operation() |

```bash
# usage
creator = ConcreteCreator()
product = creator.factory_method()
product.operation()
```

### Code Examples

#### Python
```python
from abc import ABC, abstractmethod

class Logger(ABC):
    @abstractmethod
    def log(self, msg): pass

class ConsoleLogger(Logger):
    def log(self, msg): print(msg)

class FileLogger(Logger):
    def log(self, msg): pass  # write to file

class Application(ABC):
    def create_logger(self): raise NotImplementedError
    def start(self):
        self.create_logger().log("started")

class DevApp(Application):
    def create_logger(self): return ConsoleLogger()

class ProdApp(Application):
    def create_logger(self): return FileLogger()
```

#### Rust
```rust
trait Logger { fn log(&self, msg: &str); }

struct ConsoleLogger;
struct FileLogger;

impl Logger for ConsoleLogger { fn log(&self, msg: &str) { println!("{}", msg); } }
impl Logger for FileLogger    { fn log(&self, _: &str) { /* file */ } }

trait Application {
    fn logger(&self) -> Box<dyn Logger>;
    fn start(&self) { self.logger().log("App started"); }
}

struct DevApp;
struct ProdApp;

impl Application for DevApp  { fn logger(&self) -> Box<dyn Logger> { Box::new(ConsoleLogger) } }
impl Application for ProdApp { fn logger(&self) -> Box<dyn Logger> { Box::new(FileLogger) } }
```

### Usage
**General**
- Framework extension points
- Runtime type selection

**Technical**
- Choose RTU vs TCP transport
- Create correct parser per device type
- Master vs Slave role

---

## Abstract Factory

### Problem
System needs to create families of related or dependent objects without specifying their concrete classes, to ensure compatibility across products.  
> Without it, client code would mix concrete classes from different families, leading to inconsistencies (e.g., Windows buttons with Mac checkboxes in a UI).  
> Like a furniture store: one factory for Victorian style (chair + table + sofa), another for Modern style, ensuring all pieces match.

### Solution
Provide an interface for creating families of related objects:  
- AbstractFactory declares methods for each product type (create_product_a(), create_product_b())  
- ConcreteFactories implement the methods to produce compatible ConcreteProducts  
- Clients use the AbstractFactory interface, configurable at runtime  
> Isolates concrete classes and allows swapping entire product families easily.

### Structure
| AbstractFactory | ConcreteFactory | AbstractProductA | ConcreteProductA1 | AbstractProductB | ConcreteProductB1 |
|:---|:---|:---|:---|:---|:---|
| + create_a(): AbstractProductA | + create_a(): AbstractProductA | + method_a() | + method_a() | + method_b() | + method_b() |
| + create_b(): AbstractProductB | + create_b(): AbstractProductB | | | | |

```bash
# usage
factory = ConcreteFactory()
product_a = factory.create_a()
product_b = factory.create_b()
```

### Code Examples

#### Python
```python
class Button:  def render(self): pass
class Checkbox: def check(self): pass

class WinButton(Button):  def render(self): print("Win button")
class WinCheckbox(Checkbox): def check(self): print("Win check")
class MacButton(Button):  def render(self): print("Mac button")
class MacCheckbox(Checkbox): def check(self): print("Mac check")

class GUIFactory:
    def create_button(self): raise NotImplementedError
    def create_checkbox(self): raise NotImplementedError

class WindowsFactory(GUIFactory):
    def create_button(self): return WinButton()
    def create_checkbox(self): return WinCheckbox()

class MacFactory(GUIFactory):
    def create_button(self): return MacButton()
    def create_checkbox(self): return MacCheckbox()
```

#### Rust
```rust
trait Button   { fn render(&self); }
trait Checkbox { fn check(&self); }

struct WinButton;   struct WinCheckbox;
struct MacButton;   struct MacCheckbox;

impl Button for WinButton { fn render(&self) { println!("Win button"); } }
impl Button for MacButton { fn render(&self) { println!("Mac button"); } }
impl Checkbox for WinCheckbox { fn check(&self) { println!("Win check"); } }
impl Checkbox for MacCheckbox { fn check(&self) { println!("Mac check"); } }

trait GuiFactory {
    fn button(&self) -> Box<dyn Button>;
    fn checkbox(&self) -> Box<dyn Checkbox>;
}

struct WindowsFactory;
struct MacFactory;

impl GuiFactory for WindowsFactory {
    fn button(&self) -> Box<dyn Button> { Box::new(WinButton) }
    fn checkbox(&self) -> Box<dyn Checkbox> { Box::new(WinCheckbox) }
}

impl GuiFactory for MacFactory {
    fn button(&self) -> Box<dyn Button> { Box::new(MacButton) }
    fn checkbox(&self) -> Box<dyn Checkbox> { Box::new(MacCheckbox) }
}
```

### Usage
**General**
- GUI themes
- Cross-platform widgets

**Technical**
- Vendor-specific device families (Schneider vs ABB)
- Multi-protocol support
- Test vs production drivers (mock vs real)
