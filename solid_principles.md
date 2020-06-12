# SOLID Principles

- ###### [SRP [S] - Принцип единой ответственности](#srp_principle)
- ###### [OCP [O] - Принцип открытости закрытости](#ocp_principle)
- ###### [LSP [L] - Принцип подстановки Барбары Лисков](#lsp_principle)





#### srp_principle
``` python
class Journal:

	def __init__(self):
		self.entries = []
		self.count = 0

	def add_entry(self, text):
		self.count += 1
		self.entries.append(f'{self.count}: {text}')

	def remove_entry(self, pos):
		del self.entries[pos]

	def __str__(self):
		return '\n'.join(self.entries)

	# Плохая идея помимо работы с журналом еще добавлять дополнительные ответственности
	# не нужно создавать GOD object, т.е. объект в котором будет все, к примеру
	# сохранение журнала на диск, загрузку журнала в память и т.д.
	# Так как этот Обьект будет отвечать уже за несколько 
	# вещей и SRP Принцип единой ответственности будет сломан
	#
	#
	# def save(self, filename):
	# 	file = open(filename, 'w')
	# 	file.write(str(self))
	# 	file.close()

	# def load(self, filename):
	# 	pass

	# def low_from_web(self, uri):
	# 	pass


class PersistenceManager:

	@staticmethod
	def save_to_file(journal, filename):
		file = open(filename, 'w')
		file.write(str(journal))
		file.close()


if __name__ == '__main__':

	# работаем с журналом
	j = Journal()
	j.add_entry("I'm read")
	j.add_entry("I'm eat something")

	print(f'Journal entries:\n{j}')

	# отдельно персистим его на диск через менеджер сохранения
	file = r'/tmp/journal.txt'
	PersistenceManager.save_to_file(j, file)
	with open(file) as fh:
		print(fh.read())
```



#### ocp_principle
```python
from enum import Enum


class Color(Enum):
	RED = 1
	GREEN = 2
	BLUE = 3


class Size(Enum):
	SMALL = 1
	MEDIUM = 2
	LARGE = 3


class Product:
	def __init__(self, name, color, size):
		self.size = size
		self.color = color
		slf.name = name


# class ProductFilter:

# 	def filter_by_color(self, products, color):
# 		for p in products:
# 			if p.color == color:
# 				yield p

# 	def filter_by_size(self, products, size):
# 		for p in products:
# 			if p.size == size:
# 				yield p

# 	def filter_by_size_and_color(self, products, size, color):
# 		for p in products:
# 			if p.color == color and p.size == size:
# 				return p


# Enterprise Patterns: Speccification
class Specification:

	def is_satisfied(self, item):
		pass

	def __and__(self, other):
		return AndSpecification(self, other)

class Filter:
	def filter(self, items, spec):
		pass

class ColorSpecification(Specification):

	def __init__(self, color):
		self.color = color

	def is_satisfied(self, item):
		return  item.color == self.color

class SizeSpecification(Specification):

	def __init__(self, size):
		self.size = size

	def is_satisfied(self, item):
		return item.size == self.size


class AndSpecification(Specification):

	def __init__(self, spec1, spec2):
		self.spec1 = spec1
		self.spec2 = spec2

	def is_satisfied(self, item):
		return self.spec1.is_satisfied(item) and \
		       self.spec2.is_satisfied(item)


class BetterFilter(Filter):
	def filter(self, items, spec):
		for item in items:
			if spec.is_satisfied(item):
				yield item


apple = Product('Apple', Color.GREEN, Size.SMALL)
tree = Product('Tree', Color.GREEN, Size.LARGE)
house = Product('House', Color.BLUE, Size.LARGE)

products = [apple, tree, house]

# pf = ProductFilter()
# print('Gree products (old):')
# for p in pf.filter_by_color(products, Color.GREEN):
# 	print(f' - {p.name} is green')


bf = BetterFilter()
print('Green products(new):')
green = ColorSpecification(Color.GREEN)
for p in bf.filter(products, green):
	print(f' - {p.name} is green')

print('Large products:')
large = SizeSpecification(Size.LARGE)
for p in bf.filter(products, large):
	print(f' - {p.name} is large')

print('Large blue items')
# large_blue = AndSpecification(large, ColorSpecification(Color.BLUE))

large_blue = large and ColorSpecification(Color.BLUE)
for p in bf.filter(products, large_blue):
	print(f' - {p.name} is large and blue')

```


#### lsp_principle
```python

class Rectangle:

	def __init__(self, width, height):
		self._height = height
		self._width = width

	@property
	def area(self):
		return self._width * self._height

	def __str__(self):
		return f'Width: {self.width}, height: {self.height}'
	

	@property
	def width(self):
		return self._width

	@width.setter
	def width(self, value):
		self._width = value

	@property
	def height(self):
		return self._height

	@height.setter
	def height(self, value):
		self._height = value

# Не использовать наследование в данной случае
# иначе use_it ломается и нарушается LSP лучше
# имплментировать от базового клса свой 
# обьект, делать проверку на квадрат
class Square(Rectangle):

	def __init__(self, size):
		Rectangle.__init__(self, size, size)

	@Rectangle.width.setter
	def width(self, value):
		self._width = self._height = value

	@Rectangle.height.setter
	def height(self, value):
		self._width = self._height = value


def use_it(rc):
	w = rc.width
	rc.height = 10
	expected = int(w * 10)
	print(f'Expected an area of {expected}, got {rc.area}')


rc = Rectangle(2, 3)
use_it(rc)
	

sq = Square(5)
use_it(sq)	
```
