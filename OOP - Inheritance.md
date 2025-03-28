
Created <font style="color:tomato; font-family:Consolas;">17-03-2025</font>

Tags: 

Related: NIL

Sources: NIL

****

## Inheriting other classes

In OOP, some classes have common functionalities or attributes that could be lumped together in another class.

The objects that exhibit similar functionalities should inherit from the same base class, which is also referred to as the super class. Objects that inherits from other classes are called sub classes.

To illustrate what inheritance is in OOP, let's create a `Ship`  and `Torpedo` class.

````python
class Ship(object):
	def __init__(self, position, velocity, ammunition):
		self.pos = poistion
		self.vel = velocity
		self.ammo = ammunition
	
	def move(self, dir):
		self.pos += dir
	
	def attack(self, target):
		if(self.ammo > 0):
			self.ammo -= 1
			self.vel = 0
		else:
			return

class Torpedo(object):
	def __init__(self, position, velocity, explosion_radius):
		self.pos = position
		self.vel = velocity
		self.aoe = explosion_radius
	
	def move(self, dir):
		self.pos += dir
	
	def explode(self, target):
		if(target.pos - self.pos < self.aoe):
			target.health -= 1
		else:
			print("Torpedo missed!")
````


As we can see, both the `Ship` and `Torpedo` class has common attributes and functionalities: position, velocity and some sort of attack. Hence, we can create a new base class called `MovingObjects` to reduce the need to write repetitive code.

````python
class MovingObject(object):
	def __init__(self, position, velocity):
		self.pos = position
		self.vel = velocity
````

### The `super` function

Now that we have a case class setup for any moving object that sets the position and velocity, we can make the `Ship` and `Torpedo` class inherit form this super class as such:

````python
class Ship(MovingObject):
	...

class Torpedo(MovingObject):
	...
````

Now, to initialize the position and velocity in each class, we can reference the `MovingObject` class using `Super()` (since `MovingObject` is the super class and we are inheriting from it) and use the `__init__` function in there.

````python
class Ship(MovingObject):
	def __init__(self, position, velocity, ammunition):
		super().__init__(position, velocity)
		self.ammo = ammunition

class Torpedo(MovingObject):
	def __init__(self, position, velocity, explosion_radius):
		super().__init__(position, velocity)
		self.aoe = explosion_radius
````