<h2>Цель: Практически применить изученные темы: ООП, классы и ООП в
Python.</h2>
Задание:
<ol>
<li>Создайте класс "Круг", который имеет атрибуты радиус и цвет, и
методы вычисления площади и длины окружности. Создайте несколько
объектов этого класса и вызовите его методы для каждого объекта.</li>

      class Circle:
        def __init__(self, radius, color):
            self.radius = radius
            self.color = color
    
        def square(self):
            square = 3.14*(self.radius**2)
            return (f"Square of circle = {square}")
    
        def circumference(self):
            circumference = 2*3.14*self.radius
            return(f"Circumference of circle = {circumference}")
    
    def main():
        circle_green = Circle(6, "green")
        circle_red = Circle(4, "red")
        circle_blue = Circle(3, "blue")
    
        print (f"circle_green: {circle_green.square()} AND {circle_green.circumference()}")
        print (f"circle_red: {circle_red.square()} AND {circle_red.circumference()}")
        print (f"circle_blue: {circle_blue.square()} AND {circle_blue.circumference()}")
    
    if __name__=="__main__":
        main()
***
    python3 circle.py 
    circle_green: Square of circle = 113.04 AND Circumference of circle = 37.68
    circle_red: Square of circle = 50.24 AND Circumference of circle = 25.12
    circle_blue: Square of circle = 28.26 AND Circumference of circle = 18.84
<li>Создайте класс "Банковский счет", который имеет атрибуты номер
счета, имя владельца, баланс и методы пополнения и снятия денег со
счета. Создайте несколько объектов этого класса и вызовите его методы
для каждого объекта.</li>

      class BankAccount():
          def __init__(self, number, owner, balance):
              self.number = number
              self.owner = owner
              self.balance = balance
      
          def deposit(self, amount):
              self.amount = amount
              self.balance+= self.amount
              return(f"Пополнение баланса на {self.amount}, остаток средств: {self.balance}")
      
          
          def withdraw(self, amount):
              self.amount = amount
              self.balance-= self.amount
              return(f"Списание средств в размере - {self.amount}, остаток средств: {self.balance}")
      
      def main():
          ivan = BankAccount(666, "Ivan Khodyrev", 5000000000)
          john = BankAccount(777, "John Doe", 123.34)
      
          print(f"Ivan Khodyrev account: {ivan.deposit(10000000)}")
          print(f"Ivan Khodyrev account: {ivan.withdraw(26.99)}")
      
          print(f"John Doe account: {john.deposit(5000)}")
          print(f"John Doe account: {john.withdraw(99.99)}")
      
      
      if __name__ == "__main__":
          main()
***
      python3 bank_account.py 
      Ivan Khodyrev account: Пополнение баланса на 10000000, остаток средств: 5010000000
      Ivan Khodyrev account: Списание средств в размере - 26.99, остаток средств: 5009999973.01
      John Doe account: Пополнение баланса на 5000, остаток средств: 5123.34
      John Doe account: Списание средств в размере - 99.99, остаток средств: 5023.35
<li>Создайте класс "Студент", который имеет атрибуты имя, возраст и
средний балл. Создайте методы для вычисления среднего балла и
определения статуса студента (отличник, хорошист, троечник).Создайте
несколько объектов этого класса и вызовите его методы для каждого
объекта.</li>

      class Student:
          def __init__(self, name, age, average = []):
              self.name = name
              self.age = age
              self.average = average
      
          def calculate_average(self, grades):
              self.grades = grades
              self.average = sum(self.grades)/len(self.grades)
              return self.average
      
          def status(self):
              
              if self.average == 5:
                  return "отличник"
              elif self.average >= 4:
                  return "хорошист"
              elif self.average >= 3:
                  return "отличник"
      
      def main():
          ivan = Student("Ivan Khodyrev", 28)
          print(f"Средний балл Ивана Ходырев: {ivan.calculate_average([5,3,4,3,4,4,5,2])}")
          print(f"Оценки Ивана: {ivan.grades}")
          print(f"Статус Ивана: {ivan.status()}")
          print(f"Средний балл Ивана Ходырев: {ivan.calculate_average([5,3,4,3,4,4,5,2,5,5,5,5,5,5])}")
          print(f"Оценки Ивана: {ivan.grades}")
          print(f"Статус Ивана: {ivan.status()}")
      
      if __name__ == "__main__":
          main()
***
      Средний балл Ивана Ходырев: 3.75
      Оценки Ивана: [5, 3, 4, 3, 4, 4, 5, 2]
      Статус Ивана: отличник
      Средний балл Ивана Ходырев: 4.285714285714286
      Оценки Ивана: [5, 3, 4, 3, 4, 4, 5, 2, 5, 5, 5, 5, 5, 5]
      Статус Ивана: хорошист
<li>Создайте класс "Книга", который имеет атрибуты название, автор и год
издания. Создайте методы для получения и изменения этих
атрибутов.Создайте несколько объектов этого класса и вызовите его
методы для каждого объекта.</li>

      class Book:
          def __init__(self, title, author, year):
              self.title = title
              self.author = author
              self.year = year
      
          def books_info(self):
              print(f"Название: {self.title}")
              print(f"Автор: {self.author}")
              print(f"Год издания: {self.year}")
              
          def change_info(self, title=None, author=None, year=None):
              if title:
                  self.title = title
              if author:
                  self.author = author
              if year:
                  self.year = year
              print("Информация о книге обновлена")
      
      def main():
          hobbit = Book("Hobbit", "J. R. R. Tolkien", 1981)
          hobbit.books_info()
      
          hobbit.change_info("The Hobbit", "John Ronald Reuel Tolkien")
          hobbit.books_info()
      
      if __name__ == "__main__":
          main()
***
      Название: Hobbit
      Автор: J. R. R. Tolkien
      Год издания: 1981
      Информация о книге обновлена
      Название: The Hobbit
      Автор: John Ronald Reuel Tolkien
      Год издания: 1981
<li>Создайте класс "Автомобиль", который имеет атрибуты марка, модель,
цвет и год выпуска. Создайте методы для получения и изменения этих
атрибутов. Создайте несколько объектов этого класса и вызовите его
методы для каждого объекта</li>

</ol>
