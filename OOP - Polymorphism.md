
Created <font style="color:tomato; font-family:Consolas;">17-03-2025</font>

Tags: 

Related: NIL

Sources: NIL

****

## Heading 

````python
class Speaker(object):
	def say(self, stuff):
		print(stuff)

class Lecturer(speaker):
	def lecture(self, stuff):
		self.say(stuff)
		self.say("you should be taking notes!!")

seth = Lecturer()
seth.lecture("CS1010x is easy")
# output: CS1010x is easy 
#         you should be taking notes
seth.say("no")
# output: no
# Python searches up the hierarchy and uses the say() method in Speaker superclass

class ArrogantLecturer(Lecturer):
	def __init__(self, fav_phrase):
		self.fav_phrase = fav_phrase
	
	def say(self, stuff):
		super().say(stuff + self.fav_phrase)
````

- If a method (function) is not found within the current class, Python will go up the hierarchy and search for the method in the super class. 