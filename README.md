## Summary

This repo is a practice of Unit Test with Python.

On the way of knowing UnitTest of the framework, I also practice how to implement Context Manager.

*All the steps is followed by [youtube channel](https://www.youtube.com/watch?v=kr0mpwqttM0) created by Corey Schafer.*


## [Context Manager](https://www.youtube.com/results?search_query=context+manager)

Context Manager is a convenience method for you to manage the code when you want to start to do something and automatically close that thing.

For example:
 
```   
    with open('sample.txt', 'w') as f:
        f.write('Practice of Context Manager')
```

The example shows that you can open the file 'sample.txt' as f, and write some text in that file. When you leave the indention scope here. It will automatically close the file for you.

But when you start and leave the context manager tt still depends on the implementation of context manager.

#### Implement with Class

```
    class Open_File(object):

        def __init__(self, filename, mode):
            self.filename = filename
            self.mode = mode

        def __enter__(self):
            self.file = open(self.filename, self.mode)
            return self.file

        def __exit__(self):
            self.file.close()

```

This code is trying to copy the implementation of open() function. __init__ method will get the essential parameters, __enter__ will process the file, return the object if you want to and __exit__ will execute when you leave the scope.

#### Implement with Decorator

```
    from contextlib import contextmanager

    @contextmanager
    def open_file(file, mode):
        try:
            f = open(file, mode)
            yield f
        finally:
            f.close()
```

f = open(file, mode) will initiate the open() function, and yield the result. After yield, will immediately goes to scope where function will do when leave the context manager. In here, f.close() will close the file.

## [Unit Testing](https://www.youtube.com/watch?v=6tNS--WetLI&t=2109s)

When there is multiple developers there trying to add new features to the same code base. We will try to add Unit Test to assure other developer doesn't break your features. Unit Test can assure your features don't break when other developer add new features in your code base.

For example, if there is a class called Employee as follow, and we want to test every piece of features each employee has.
 
```   
    class Employee:
        """A sample Employee class"""

        raise_amt = 1.05

        def __init__(self, first, last, pay):
            self.first = first
            self.last = last
            self.pay = pay

        @property
        def email(self):
            return '{}.{}@email.com'.format(self.first, self.last)

        def monthly_schedule(self, month):
            response = requests.get(f"http://company.com/{self.last}/{month}")

            if response.ok:
                return response.text
            else:
                return 'Bad Response!'
```

We can write unit test:

```
    import unittest
    from unittest.mock import patch
    from employee import Employee


    class TestEmployee(unittest.TestCase):

        def setUp(self):
            print('setUp')
            self.emp_1 = Employee('Corey', 'Schafer', 50000)
            self.emp_2 = Employee('Sue', 'Smith', 60000)

        def tearDown(self):
            print('tearDown\n')

        def test_email(self):
            print('test_email')
            self.assertEqual(self.emp_1.email, 'Corey.Schafer@email.com')
            self.assertEqual(self.emp_2.email, 'Sue.Smith@email.com')

            self.emp_1.first = 'John'
            self.emp_2.first = 'Jane'

            self.assertEqual(self.emp_1.email, 'John.Schafer@email.com')
            self.assertEqual(self.emp_2.email, 'Jane.Smith@email.com')

        def test_monthly_schedule(self):
            with patch('employee.requests.get') as mocked_get:
                mocked_get.return_value.ok = True
                mocked_get.return_value.text = 'Success'

                schedule = self.emp_1.monthly_schedule('May')
                mocked_get.assert_called_with('http://company.com/Schafer/May')
                self.assertEqual(schedule, 'Success')

                mocked_get.return_value.ok = False

                schedule = self.emp_2.monthly_schedule('June')
                mocked_get.assert_called_with('http://company.com/Smith/June')
                self.assertEqual(schedule, 'Bad Response!')


    if __name__ == '__main__':
        unittest.main()
```

First we import unittest module, and the module we are going to test. Then create a class inherit from unittest.TestCase, methods in the class will begin with "test_" to normally execute the testcase. The setUp and Teardwon will be the execute before and after ease test method.

unittest.mock is a useful module when your function may have multiple functions, by isolate the function in the testcase and mocking other functions, then you can separately test your function.


## Conclusion

In this project, we practice Python Context Manager and Unit Testing.

## Reference

1. [Context Manager]https://www.youtube.com/results?search_query=context+manager
2. [Unit Tesing](https://www.youtube.com/watch?v=6tNS--WetLI)