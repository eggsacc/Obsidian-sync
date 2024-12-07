
Created <font style="color:tomato; font-family:Consolas;">10-11-2024</font>

Tags: 

Related: NIL

****

## Basic

- `harvest()`: harvest crops
- `do_a_flip()`: drone does a flip

- `can_harvest()`: check if crops are ready for harvesting
- `move(North, South etc)`: move drone
- `plant(Entities.Bush / Carrots etc)`: plant plant
- `get_pos_x()`
- `get_pos_y()`
- `num_items(Items.item)`
- `get_entity_type()`
- `get_ground_type()`
- `till()`

````python
# setup
moveOrigin()
#clear()
#tradeBuckets(82)
field_size = 8

while True:
	#plantPumpkin()
	#plantTree()
	#plantCarrot()
	if(num_items(Items.Hay) < 5000):
		clear(field_size)
		for i in range(5):
			moveOrigin()
			plantGrass(field_size)
			
	if(num_items(Items.Carrot) < 3000 or num_items(Items.Wood) < 3000):
		clear(field_size)
		moveOrigin()
		for i in range(5 * field_size * field_size):
			plantTree()
			plantCarrot()
			Move(5)
			
	if(num_items(Items.Pumpkin) < 7000):
		clear(field_size)
		success = False
		while(not success):
			success = plantPumpkin(field_size)
````

````python
for i in range(10):
	max = 0
	max_x = 0
	max_y = 0
	for i in range(field_size * field_size):
		Till()
		if(get_entity_type() != Entities.Sunflower):
			trade(Items.Sunflower_Seed)
			plant(Entities.Sunflower)
		Move(field_size)
		if(can_harvest() and get_entity_type() == Entities.Sunflower):
			if(measure() > max):
				max_x = get_pos_x()
				max_y = get_pos_y()
				
	move_x = get_pos_x() - max_x
	move_y = get_pos_y() - max_y
	if(move_x < 0):
		for i in range(move_x):
			move(East)
	elif(move_x > 0):
		for i in range(move_x):
			move(West)
	if(move_x < 0):
		for i in range(move_x):
			move(North)
	elif(move_y > 0):
		for i in range(move_x):
			move(South)
	harvest()
````