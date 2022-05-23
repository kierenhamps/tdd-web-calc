# INSTRUCTORS GUIDE

This guide is intended to aide in the teaching of TDD using this repo.

It will contain a pathway through the development of the Calculator
class using tests.

Each step will provide tests, code, and details on any refactoring or
observations along the way and a detailed breakdown of how the test and
code was written.

## Setup

Before starting, it is expected that the instructor will need a working system. Using branch `instructor`, this will contain a minimal calculator codebase ready for the `Calculator` class to be created.  

Students can use the main branch of the repo. This will have the `Calculator` class in a state that the class will be in at the end of this instructors guide.  

To run the code please you will need to install the pre-requisites (`pip install -r requirements.txt`) and run the app (`python3 ./app/app.py`).  

The webapp can be opened after running the application by navigating in a web browser to: <http://127.0.0.1:5000>  

*For detailed instructions on setup please follow the [README.md](README.md) in the code repo*

## Contents

[Step 1 - Initialising The Calculator](#step1)  
[Step 2 - Reducing The Calculator](#step2)  
[Step 3 - Implementing the addition operator](#step3)  

## Overview

The Goal of these steps is to go from a blank slate to a semi completed calculator with the add (`+`) operation implementated.  

We will be creating a new class called `Calculator` with the following methods and variables:  

  * **`Calculator.value:`** A Variable that stores the value of the calculation to display on the calculator.  
  * **`Calculator.calculation:`** A Variable that stores the reduced calculation for processing  
  * **`Calculator.parse():`** A Function that takes a stream of inputs in the form of a list and sets the value to the resulting value of the calculation.  
  * **`Calculator.reduce():`** A Function that takes a stream of inputs in the form of a list and returns a list of operations and numbers condensed.  
  * **`Calculator.calculate():`** A Function that takes a reduced list of numbers and operations and returns the resulting value of the calculation.  

A web front end is provided to support the interractions with the `Calculator` class and expects the names of the variables and functions above in order to function.  

<div name="step1"></div>

## Step 1 - Initialising the Calculator

---    
**Observation:** Turning on the calculator web app shows that it does not display 0 as expected  
**The Task:**  
  * Given I turn on my calculator
  * Then it should initialise to 0

---
**Action:** Create a new file `tests/test_calculator.py` and write the test  
**Test Code:**  

``` python
import pytest
from app.calculator import Calculator

def test_calculator_should_zero_on_initialise():
    """
    Given I turn on my calculator
    Then it should initialise to 0 
    """
    calculator = Calculator()
    assert calculator.value is 0
```

**Action:** Run the tests (`python3 -m pytest`)  
**Observation:** <span style="color:red">Test failed</span>  
> <span style="color:red">cannot import name 'Calculator' from 'app.calculator'</span>

---
**Action:** Create a Class named `Calculator` in the `app/calculator.py` file  
**Code:**  
``` python
class Calculator():
    """
    This is the main class to implement the Calculator
    """
```

**Action:** Run the tests  
**Observation:** <span style="color:red">Test failed</span>  
> <span style="color:red">AttributeError: 'Calculator' object has no attribute 'value'</span>

---  
**Action:** Add an initialisation function on the Calculator class  
**Code:** *Inside the Calculator class*  
``` python
    def __init__(self):
        self.value = 0
```

**Action:** Run the tests  
**Observation:** <span style="color:green">Test successful **GREEN**</span>
> <span style="color:lightblue">tests/test_calculator.py </span><span style="color:green">.</span>

**Observation:** <span style="color:green">The calculator initialised to 0</span>

---  
**Refactoring:**  
> No refactoring required

---  
**Completed Test Code:**  

``` python
import pytest
from app.calculator import Calculator

def test_calculator_should_zero_on_initialise():
    """
    Given I turn on my calculator
    Then it should initialise to 0 
    """
    calculator = Calculator()
    assert calculator.value is 0
```

**Completed Calculator Class:**  

``` python
class Calculator():
    """
    This is the main class to implement the Calculator
    """

    def __init__(self):
        self.value = 0
```

---  
<div name="step2"></div>

## Step 2 - Reducing the calculation

**Action:** Using the webapp enter a calculation and finish with "="  
**Observation:** <span style="color:red">An error occurs</span>   
> <span style="color:red">'Calculator' object has no attribute 'parse'</span>
  
**The Task:**  
To calculate the input stream we need to reduce the calculation down into numbers and operators We will do this over multiple tests.  
  * <span style="color:lightblue">Given I have an initialised calculator</span>
  * <span style="color:lightblue">When I enter 2 numbers</span>
  * <span style="color:lightblue">Then I want these numbers to be concatinated</span>

---  
**Test Code:**  

``` python
def test_calculator_should_concatinate_2_numbers():
    """
    Given I have an initialised calculator
    When I enter 2 numbers
    Then I want these numbers to be concatinated
    """
    calculator = Calculator()
    calculation = [1, 2, "="]
    calculator.parse(calculation)
    assert calculator.calculation == [12, "="]
```

**Action:** Run the tests  
**Observation:** <span style="color:red">Test failed</span>  
> <span style="color:red">'Calculator' object has no attribute 'parse'</span>  

**BONUS Obervation:** Note that the first test was run and is still green (green dot before the red F):  
> <span style="color:lightblue">tests/test_calculator.py </span><span style="color:green">.</span><span style="color:red">F</span>

---  
**Action:** Add a `parse` function into the Calculator class  
**Code:** *Inside the Calculator class*  

``` python
    def parse(self, calculation):
        """
        parse is a function that takes a list of numbers and operators and
        sets the value to the resulting calculation
        """

        print(f'Parsing calculation: {calculation}')
```

**Action:** Run the tests  
**Observation:** <span style="color:red">Test failed</span>  
> <span style="color:red">'Calculator' object has no attribute 'calculation'</span>

---  
**Action:** Add to the `parse` function to reduce the calucation and save into Calculator.calculation  
**Code:** *Inside the Calculator Class, parse function*  

``` python
        # First pass reduce the numbers
        self.calculation = []
        temp_value = None

        # Iterate over all items in the calculation
        for item in calculation:
            if isinstance(item, int):
                if temp_value == None:
                    temp_value = item
                else:
                    temp_value = int(str(temp_value) + str(item))
            else:
                ## Item is an operator
                if temp_value != None:
                    self.calculation.append(temp_value)

                self.calculation.append(item)
                temp_value = None

        print(f'Reduces to: {self.calculation}')
```

> We start by initialising the `calculation` variable to an empty list.  
> Then looping over all items in the list of the given input stream we check the type of the input.  
> Using an `if` conditional statement:
>  * If the item is an integer we store it in the `temp_value` variable if its the first item we have processed, otherwise we concatinate it with the existing `temp_value`
>  * If the item is not an integer (i.e. a string) then we add `temp_value` into the `calculation` (reduced list) if we have one and we add the current item to the end (the operator)

**Action:** Run the tests  
**Observation:** <span style="color:gren">All tests successful</span>  
> <span style="color:lightblue">tests/test_calculator.py </span><span style="color:green">..</span>

---
**Refactoring:**  
**Observation:** Notice that our 2 tests share the intitialisation of the Calculator class.

> We can refactor this by using a pytest fixture (a function that gets run before the tests do) and inject the initialised Calculator into our tests.  

**Test Code:** *Added before the fist test, but after the imports*  

``` python
@pytest.fixture
def calculator():
    calculator = Calculator()
    return calculator
```

> We can now use this fixture in our existing tests by adding `calculator` to our test parameters  

**Test Code:** *Just changing the `def <function>` lines of our existing tests*  

``` python
def test_calculator_should_zero_on_initialise(calculator):
```

``` python
def test_calculator_should_concatinate_2_numbers(calculator):
```

> And we can now remove all existing intitialisations of the calculator in
the tests.  

**Action:** Remove the lines `calculator = Calculator()` from both tests.  
**Action:** Run the tests  
**Obervation:** <span style="color:green">All tests continue to pass</span>  

---  
**Completed Test File:**  

``` python
import pytest

from app.calculator import Calculator

@pytest.fixture
def calculator():
    calculator = Calculator()
    return calculator

def test_calculator_should_zero_on_initialise(calculator):
    """
    Given I turn on my calculator
    Then it should initialise to 0 
    """
    assert calculator.value is 0


def test_calculator_should_concatinate_2_numbers(calculator):
    """
    Given I have an initialised calculator
    When I enter 2 numbers
    Then I want these numbers to be concatinated
    """
    calculation = [1, 2, "="]
    calculator.parse(calculation)
    assert calculator.calculation == [12, "="]
```

**Completed Calculator Class:**  

``` python
### Calculator
class Calculator():
    """
    This is the main class to implement the Calculator
    """

    def __init__(self):
        self.value = 0


    def parse(self, calculation):

        print(f'Parsing calculation: {calculation}')

        # First pass reduce the numbers
        self.calculation = []
        temp_value = None

        # Iterate over all items in the calculation
        for item in calculation:
            if isinstance(item, int):
                if temp_value == None:
                    temp_value = item
                else:
                    temp_value = int(str(temp_value) + str(item))
            else:
                ## Item is an operator
                if temp_value != None:
                    self.calculation.append(temp_value)
                
                self.calculation.append(item)
                temp_value = None

        print(f'Reduces to: {self.calculation}')
```

---  
**BONUS Action:** Enter a calculation in the webapp and end with "=".  
**BONUS Observation:** Notice that Reduced calculation is displayed in the python log.  

---  
**The Task:**  
  * Given I have an initialised calculator
  * When I enter 3 numbers
  * Then I want these numbers to be concatinated

---
***Test Code:**  

``` python
def test_calculator_should_concatinate_3_numbers(calculator):
    """
    Given I have an initialised calculator
    When I enter 3 numbers
    Then I want these numbers to be concatinated
    """
    calculation = [1, 2, 3, "="]
    calculator.parse(calculation)
    assert calculator.calculation == [123, "="]
```

**Action:** Run the tests  
**Observation:** <span style="color:green">Tests are sucessful</span>
> <span style="color:lightblue">tests/test_calculator.py </span><span style="color:green">...</span>

---
**Refactoring**:
> No refactoring required

---
**The Task:**  
* Given I have an initialised calculator
* When I enter several numbers with multiple zeros at the beginning
* Then I want these numbers to be concatinated without the leading zeros

---  
**Test Code:** 

``` python
def test_calculator_should_concatinate_numbers_without_leading_zeros(calculator):
    """
    Given I have an initialised calculator
    When I enter 3 numbers
    Then I want these numbers to be concatinated
    """
    calculation = [0, 0, 0, 0, 1, 2, 3, "="]
    calculator.parse(calculation)
    assert calculator.calculation == [123, "="]
```


**Action:** Run the tests  
**Observation:** <span style="color:green">Tests are sucessful</span>
> <span style="color:lightblue">tests/test_calculator.py </span><span style="color:green">...</span>

---
**Refactoring**:
> No refactoring required

---  
<div name="step3"></div>

### Step 3 - Implementing the addition operator  

**Action:** Using the webapp enter a calculation and finish with \"=\"  
**Observation:** An error occurs  
> 'Calculator' object has no attribute 'parse'