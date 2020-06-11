# SOLID Principles

- ###### [SRP [S] - Принцип единой ответственности](#srp_principle)









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
