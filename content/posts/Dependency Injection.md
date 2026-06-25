## Dependencies
What an entity depends on to perform it's tasks.

Example:
A bus carries students to and from school. Each student is assigned a seat on a dedicated bus route.

Each student depends on the bus to get to and from school. So the bus is a dependency of the student.

The bus doesn't need the students to do its thing (drive around)

Without dependency injection, two scenarios are possible:
1. The bus has to keep track of each student that boards (creating a new entity for each student). This is complicated and beyond the responsibility of a bus.
2. Each student depends has his/her own bus with its own driver. Not efficient (high resource cost).
## Injection
With dependency injection, a single bus can be instantiated for a specific route and "provided" to the students. Each student that needs a bus to get to/from school has the responsibility to board the bus that has been provided to them.

### Constructor Dependency Injection
Example with constructor dependency injection:

```
class Bus:
	def __init__(self, bus_route, capacity = 30):
		self.bus_route = bus_route
		self.capacity = capacity
		self.passengers = []

	def board(self, passenger_name):
		if len(self.passengers) < self.capacity:
			self.passengers.append(passenger_name)
			return f"{passenger_name} has boarded the bus."
		else:
			return "Bus is full. Cannot board more passengers."

	def leave(self, passenger_name):
		if passenger_name in self.passengers:
			self.passengers.remove(passenger_name)
			return f"{passenger_name} has left the bus."
		else:
			return f"{passenger_name} is not on the bus."

def get_passenger_list(self):
	return self.passengers
	

class Student:
    def __init__(self, name, age, student_id, bus):
        self.name = name
        self.age = age
        self.student_id = student_id
        self.bus = bus

    def get_details(self):
        return f"Name: {self.name}, Age: {self.age}, Student ID: {self.student_id} is assigned to bus route: {self.bus.bus_route}"
    
    def board_bus(self):
        return self.bus.board(self.name)

    def leave_bus(self):
        return self.bus.leave(self.name)


if __name__ == "__main__":       
	bus_22 = bus.Bus("22B", capacity=40)
	
	student_1 = student.Student("Alice", 14, "S12345", bus_22)
	student_2 = student.Student("Bob", 15, "S67890", bus_22)
	student_3 = student.Student("Charlie", 13, "S54321", bus_22)

	student_1.board_bus()
	student_2.board_bus()
	student_3.board_bus()
	
	print(bus_22.get_passenger_list())
```

In this example, all the students assigned to route 22B have the same bus instance as their dependency. They all board the same bus.

### Setter Dependency Injection
Unlike the constructor dependency injection, where the dependency is provided when the object/entity is created, the setter dependency injection uses a specific setter method to assign each student to a bus. The logic of boarding the bus remains unchanged.

```
class Bus:
	def __init__(self, bus_route, capacity = 30):
		self.bus_route = bus_route
		self.capacity = capacity
		self.passengers = []

	def board(self, passenger_name):
		if len(self.passengers) < self.capacity:
			self.passengers.append(passenger_name)
			return f"{passenger_name} has boarded the bus."
		else:
			return "Bus is full. Cannot board more passengers."

	def leave(self, passenger_name):
		if passenger_name in self.passengers:
			self.passengers.remove(passenger_name)
			return f"{passenger_name} has left the bus."
		else:
			return f"{passenger_name} is not on the bus."

	def get_passenger_list(self):
		return self.passengers


class Student:
    def __init__(self, name, age, student_id):
        self.name = name
        self.age = age
        self.student_id = student_id

    def get_details(self):
        return f"Name: {self.name}, Age: {self.age}, Student ID: {self.student_id} is assigned to bus route: {self.bus.bus_route}"

    def set_bus(self, bus):
	    self.bus = bus

    def board_bus(self):
        return self.bus.board(self.name)

    def leave_bus(self):
        return self.bus.leave(self.name)


if __name__ == "__main__":       
	bus_22 = bus.Bus("22B", capacity=40)
	
	student_1 = student.Student("Alice", 14, "S12345")
	student_2 = student.Student("Bob", 15, "S67890")
	student_3 = student.Student("Charlie", 13, "S54321")
	
	student_1.set_bus(bus_22)
	student_2.set_bus(bus_22)
	student_3.set_bus(bus_22)
	
	student_1.board_bus()
	student_2.board_bus()
	student_3.board_bus()
	
	print(bus_22.get_passenger_list())

```

### Method Dependency Injection
Using method dependency injection, the injection is provided as an argument to the method that needs it:
```
class Bus:
	def __init__(self, bus_route, capacity = 30):
		self.bus_route = bus_route
		self.capacity = capacity
		self.passengers = []

	def board(self, passenger_name):
		if len(self.passengers) < self.capacity:
			self.passengers.append(passenger_name)
			return f"{passenger_name} has boarded the bus."
		else:
			return "Bus is full. Cannot board more passengers."

	def leave(self, passenger_name):
		if passenger_name in self.passengers:
			self.passengers.remove(passenger_name)
			return f"{passenger_name} has left the bus."
		else:
			return f"{passenger_name} is not on the bus."

def get_passenger_list(self):
	return self.passengers
	

class Student:
    def __init__(self, name, age, student_id):
        self.name = name
        self.age = age
        self.student_id = student_id

    def get_details(self):
        return f"Name: {self.name}, Age: {self.age}, Student ID: {self.student_id} is assigned to bus route: {self.bus.bus_route}"
    
    def board_bus(self, bus):
        return bus.board(self.name)

    def leave_bus(self, bus):
        return bus.leave(self.name)


if __name__ == "__main__":       
	bus_22 = bus.Bus("22B", capacity=40)
	
	student_1 = student.Student("Alice", 14, "S12345")
	student_2 = student.Student("Bob", 15, "S67890")
	student_3 = student.Student("Charlie", 13, "S54321")

	student_1.board_bus(bus_22)
	student_2.board_bus(bus_22)
	student_3.board_bus(bus_22)
	print(bus_22.get_passenger_list())

	student_3.board_bus(bus_22)
	student_1.board_bus(bus_22)
	student_2.board_bus(bus_22)
	print(bus_22.get_passenger_list())
```
This way of implementing dependency injection can work for some scenarios when dealing with temporary assignments, but it can become problematic as the dependant has no reference to its dependencies.

In other words, the student doesn't know which bus he/she is on other than the one someone said to board.

Since there is no dependent reference to the dependency, it must be provided to every method call that needs it.