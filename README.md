# Importance of Clean Code
- A messy code base causes a decrease in productivity over time.
- Code should be elegant, have minimal dependencies, and contain no duplication.
- Code must not only be written cleanly, but the cleanliness must be maintained over time.

Most major software projects have their own style guide: a set of conventions (sometimes arbitrary) about how to write code for that project. It is much easier to understand a large codebase when all the code in it is in a consistent style.

“Style” covers a lot of ground, from “use camelCase for variable names” to “never use global variables” to “never use exceptions.”

You will find the shorter shallower version of the style guide immediately below, and the in-depth guide below that. 

# Quick and Dirty Style Guide

## Meaningful Names
- Avoid **disinformation**
  - Don’t call a variable an `accountList` if the type is not part of the `List` class.
  - `accountList as Queue` **is bad**
  - `accountList as List` **is fine**
  - `accounts` is better
- Use **intention-revealing** names
  - `Dim d as Integer` compared to `Dim daysPassed as Integer`
- **Don’t use similar looking names** like `methodThatCallsUserAccount` and `methodThatCallsUserAccountInfo`
- Use pronounceable names
- Use searchable names
- Class names should be nouns like Customer, Account, etc. not a verb
- Don’t hesitate to rename items in an existing code base

## Functions
- Functions should be small, **about 20 lines is ideal** (although not a strict rule)
- Code within an if, else, while, etc. block should **ideally be one line long** - a function call
  - Functions should not hold nested structures
- **Functions should only do one task**, if they do more, split them
- Use **descriptive and lengthy names**
  - `downloadHTML` vs `downloadPagesForTestingHTML`
- The ideal number of function arguments is zero, one, or two (in that order)
- If a function transforms an input argument, the transformation should be returned by the function
- Flag arguments (passing a boolean) in a function is bad practice
  - Instead of a function called `render(true)` that contains both conditions, it should be split into two functions
  - Example: `renderForSuite()` and `renderForSingleTest()`
- When a function takes too many arguments, they can likely be **wrapped into another class**
  - `Function makeCircle(ByVal x as Double, ByVal y as Double, ByVal radius as Double) as Circle` vs `Function makeCircle(ByVal center as Point, ByVal radiusas Double) as Circle`
- **Avoid ByRef arguments**
  - `Public sub appendFooter(ByRef report as String)` vs `report.appendFooter()`
- A function should either **change the state** or **return some information** of an object, not both.
  - `Sub set(ByVal attribute as String, ByVal value as String)` would be called as `set(“username”, “bob”)`
  - `if(set(“username”, “bob”))` **is bad**
  - Can be improved by splitting up into `Function attributeExists(String attribute) as Boolean` and `Sub setAttribute(String attribute, String value)`
  - `if(attributeExists(“username”)) Then setAttribute(“username”, “bob”)` **is good**
- Functions c**an be messy and complicated at first**, as long as they are **refined afterwards**

## Comments
- The use of comments **compensates for failure** to express ourselves in code
- Old comments possibly become irrelevant over time
- Programmers should be able to update/maintain comments, but that energy **should go toward making code clear and expressive**
  - `if employee.flags and employee.age > 65 ‘Checks employee benefits eligibility` **<-bad comment**
  - `if employee.isEligibleForFullBenefits()` **<- no comment needed**
- **Good types of comments**
  - Legal, lawful, or regulatory comments
  - Informative comments that discuss formatting
  - Explanation of intent, why the implementation was done in a certain way
  - Clarification of something obscure that cannot be changed
  - Warnings
  - To-do comments that serve as reminders
  - Stressing the importance of an item
- **Bad types of comments**
  - Comments written for every single variable and function
  - Change log comments
  - Restating the obvious and redundancy
  - Commented-out code
  - Too much information

## Formatting
- Make liberal use of the **#Region functionality** 
  - #Regions that can be reasonably expected in most every class: #Globals, #Constructors, #Public Methods, #Private Methods
  - Other common #Regions: #BO Calls, #Overrides, interface implementations...
- Closely related functions and variables should be **vertically close** to each other
  - Non-member variables should be declared immediately before their first usage.

## Objects and Data Structures
- We don’t want to expose the implementation of data structures
  - Use abstractions to hide implementation. Consider the following:
  - ```vbnet
	Public Interface Vehicle
		Function getFuelTankCapacityInGallons() as Double
		Function getGallonsOfGasoline() as Double
    ```
  - ```vbnet
	Public Interface Vehicle
		Function getPercentFuelRemaining() as Double    
    ```
  - The first option uses concrete cases that are merely accessors of variables
  - The second option hides the form of the data using the percentage
  - The second option is preferable because the details of the data are hidden
- **Objects hide their data behind abstractions** and expose functions to operate on that data
  - **Object-oriented code** allows new classes to be added without changing existing functions
    - OOP makes it hard to add new functions because all classes must change
    - If you want to add new data types rather than functions, use OOP
- **Data structures expose their data** and have no meaningful functions
  - **Procedural code** allows ne functions to be added without changing the existing data structures
    - Hard to add new data structures because all functions must change
    - If you want to add new functions rather than data types, use procedural code
- A module **should not know** about the inner workings of the objects being manipulated	
  - A method should not invoke methods on objects returned by other functions
  - `Dim output as String = text.getValue().getDirectory()` <- calls getDirectory() on the return value of getValue(), **BAD**
- **A Data Transfer Object (DTO)** is a class with **public variables and no functions**
  - Useful for communicating with databases or providing a large number of variables to a function at once
- Objects **expose behavior and hide data**
  - It’s easy to add new objects without changing existing behaviors
  - It’s hard to add new behaviors to existing objects
- Data structures **expose data but have no significant behavior**
  - It’s easy to add new behaviours to existing data structures
  - It’s hard to add new data structures to existing functions

## Boundaries
- Create accessors so that the data **cannot be directly manipulated**
  - Instead of passing an object around, **create a getter method**
  - The object would be private, while the getter method is public
  - This way the data in the object can be accessed, but not cleared

## Classes
- Classes should be **small**, with a low number of instance variables
- **Single Responsibility Principle (SRP)**, class should have one responsibility
- Organize system such that **new changes minimize the risk** that the entire system won’t function
  - Consider splitting into many classes, **even if they only have one method each**
  - **Avoid** classes that directly depend on others
- Concrete classes contain implementation details
- Abstract classes represent concepts only

## Concurrency
- Concurrency **increases performance when there is a lot of wait time**
- Your design changes when writing concurrent programs
  - Concurrency is **complex**, even for simple problems
  - It requires a **fundamental change in design** strategy  	 

## Environment
- Your development environment settings should be set to the strictest possible settings
  - **My Project > Compile > Option Explicit: On, Option Strict: On, Option Infer: Off, Warning Configurations should have all Notifications set to Error**

# In-Depth Style Guide

## Comments

### C1: Inappropriate Information
---
It is inappropriate for a comment to hold information better held in a different kind of system such as your version control system, your issue tracking system, or any other record-keeping system. Change histories, for example, just clutter up source files with volumes of historical and uninteresting text. In general, meta-data such as authors, last-modified-date, and so on should not appear in comments. Comments should be reserved for technical notes about the code and design.

### C2: Obsolete Comment
---
A comment that has gotten old, irrelevant, and incorrect is obsolete. Comments get old quickly. It is best not to write a comment that will become obsolete. If you find an obsolete comment, it is best to update it or get rid of it as quickly as possible. Obsolete comments tend to migrate away from the code they once described. They become floating islands of irrelevance and misdirection in the code.

### C3: Redundant Comment
---
A comment is redundant if it describes something that adequately describes itself. For example:
```vbnet
	i += 1 ‘increment i
```
Another example is function documentation that says nothing more than (or even less than) the function signature:	
```vbnet   	
	''' <summary>
       ''' Shuffles a DataTable
    ''' </summary>
    ''' <param name="pobjData">DataTable you'd like to randomize</param>
    Public Sub ShuffleDataTable(ByRef pobjData as DataTable)
    	...
    End Sub
```	
Comments should say things that the code cannot say for itself.

### C4: Poorly Written Comment
---
A comment worth writing is worth writing well. If you are going to write a comment, take the time to make sure it is the best comment you can write. Choose your words carefully. Use correct grammar and punctuation. Don’t ramble. Don’t state the obvious. Be brief.

### C5: Commented-Out Code
---
It makes me crazy to see stretches of code that are commented out. Who knows how old it is? Who knows whether or not it’s meaningful? Yet no one will delete it because everyone assumes someone else needs it or has plans for it.

That code sits there and rots, getting less and less relevant with every passing day. It calls functions that no longer exist. It uses variables whose names have changed. It follows conventions that are long obsolete. It pollutes the modules that contain it and distracts the people who try to read it. Commented-out code is an abomination.  

When you see commented-out code, delete it. Don’t worry, the version control system still remembers it. If anyone really needs it, he or she can go back and check out a previous version. Don’t suffer commented-out code to survive.

# Functions

### F1: Too Many Arguments
---
Functions should have a small number of arguments. No argument is best, followed by one, two, and three. More than three is very questionable and should be avoided with prejudice.

### F2: ByRef Arguments
---
ByRef arguments are counterintuitive. Readers expect arguments to be inputs, not outputs. If your function must change the state of something, have it change the state of the object it is called on.

### F3: Flag Arguments
---
Boolean arguments loudly declare that the function does more than one thing. They are confusing and should be eliminated.

### F4: Dead Functions
---
Methods that are never called should be discarded. Keeping dead code around is wasteful. Don’t be afraid to delete the function. Remember, your version control system still remembers it.

# General

### G1: Obvious Behavior is Unimplemented
---
Following “The Principle of Least Surprise,” any function or class should implement the behaviors that another programmer could reasonably expect. For example, consider a function that translates the name of a day to an enum that represents the day.
```vbnet
	Dim day as Day = DayDate.StringToDay(dayName)
```
We would expect the string `“Monday”` to be translated to `Day.MONDAY`. We would also expect the common abbreviations to be translated, and we would expect the function to ignore case.
	When an obvious behavior is not implemented, readers and users of the code can no longer depend on their intuition about function names. They lose their trust in the original author and must fall back on reading the details of the code.

### G2: Incorrect Behavior at the Boundaries
---
It seems obvious to say that the code should behave correctly. The problem is that we seldom realize just how complicated correct behavior is. Developers often write functions that they think will work, and then trust their intuition rather than going to the effort to prove that their code works in all the corner and boundary cases.

There is no replacement for due diligence. Every boundary condition, every corner case, every quirk and exception represents something that can confound an elegant and intuitive algorithm. Don’t rely on your intuition. Look for every boundary condition and write a test for it.

### G3: Overridden Safeties
---
Chernobyl melted down because the plant manager overrode each of the safety mechanisms one by one. The safeties making it inconvenient to run an experiment. The result was that the experiment did not get run, and the world saw it’s first major civilian nuclear catastrophe.

It is risky to override safeties. Exerting manual control over serialVersionUID may be necessary, but it is always risky. Turning off certain compiler warnings may help you get a build to succeed, but at the risk of endless debugging sessions. Turning of failing tests and telling yourself you’ll get them to pass later is as bad as pretending your credit cards are free money.

### G4: Duplication
---
This is one of the most important rules, and you should take it very seriously. 

Every time you see duplication in the code, it represents a missed opportunity for abstraction. That duplication could probably become a subroutine or perhaps another class outright. By folding the duplication into such an abstraction, you increase the vocabulary of the language of your design. Other programmers can use the abstract facilities you create. Coding becomes faster and less error prone because you have raised the abstraction level.

The most obvious form of duplication is when you have clumps of identical code that looks like someone went wild pasting the same code over and over. These should be replaced with simple methods.

A more subtle form is the select case or if/else chain that appears again and again in various modules, always testing for the same set of conditions. These should be replaced with polymorphism.

Still more subtle are the modules that have similar algorithms, but don’t share similar lines of code.

Find and eliminate duplication wherever you can.

### G5: Code at Wrong Level of Abstraction
---
It is important to create abstractions that separate higher level general concepts from lower level detailed concepts. Sometimes we do this by creating abstract classes to hold the higher level concepts and derivatives to hold the lower level concepts. When we do this, we need to make sure that the separation is complete. We want all the lower level concepts to be in the derivatives and all the higher level concepts to be in the base class.

For example, constants, variables, or utility functions that pertain only to the detailed implementation should not be present in the base class. The base class should know nothing about them.

This rule also pertains to source files, components, and modules. Good software design requires that we separate concepts at different levels and place them in different containers. Sometimes these containers are base classes or derivatives and sometimes that are source files, modules, or components. Whatever the case may be, the separation needs to be complete. We don’t want lower and higher level concepts mixed together.

Consider the following code:
```vbnet
	Public Interface Stack
		Function pop() as Object 
		Sub push(ByVal o as Object) 
		Function percentFull() as Double	
```
The `percentFull` function is at the wrong level of abstraction. Although there are many implementations of `Stack` where the concept of fullness is reasonable, there are other implementations that simply could not know how full they are. So the function would be better placed in a derivative interface such as `BoundedStack`.

Perhaps you are thinking that the implementation could just return zero is the stack were boundless. The problem with that is that no stack is truly boundless. You cannot really prevent an `OutOfMemoryException` by checking for
	`stack.percentFull() < 50.0`.
Implementing the function to return 0 would be telling a lie.
	The point is that you cannot lie or fake your way out of a misplaced abstraction. Isolating abstractions is one of the hardest things that software developers do, and there is no quick fix when you get it wrong.

### G6: Base Classes Depending on Their Derivatives
---
The most common reason for partitioning concepts into base and derivative classes is so that the higher level base class concepts can be independent of the lower level derivative class concepts. Therefore, when we see base classes mentioning the names of their derivatives, we suspect a problem. In general, base classes should know nothing about their derivatives.

There are exceptions to this rule, of course. Sometimes the number of derivatives is strictly fixed, and the base class has code that selects between derivatives. We see this a lot in finite state machine implementations. However, in that case the derivatives and base class are strongly coupled and always deploy together in the same .dll file. In the general case we want to be able to deploy derivatives and bases in different .dll files.

Deploying derivatives and bases in different .dll files and making sure the base .dll files know nothing about the contents of the derivative .dll files allow us to deploy our systems in discrete and independent components. When such components are modified, they can be redeployed without having to redeploy the base components. This means that the impact of the change is greatly lessened, and maintaining systems in the field is made much simpler.

### G7: Too Much Information
---
Well-defined modules have very small interfaces that allow you to do a lot with a little. Poorly defined modules have wide and weep interfaces that force you to use many different gestures to get simple things done. A well-defined interface does not offer very many functions to depend upon, so coupling is low. A poorly defined interface provides lots of functions that you must call, so coupling is high.

Good software developers learn to limit what they expose at the interfaces of their classes and modules. The fewer methods a class has, the better. The fewer variables a function knows about, the better. The fewer instance variables a class has, the better.

Hide your data. Hide your utility functions. Hide you constants and your temporaries. Don’t create classes with lots of methods or lots of instance variables. Don’t create lots of protected variables and functions for your subclasses. Concentrate on keeping interfaces very tight and very small. Help keep coupling low by limiting information.

### G8: Dead Code
---
Dead code is code that isn’t executed. You find it in the body of an if statement that checks for a condition that can’t happen. You find it in the catch block of a try that never throws. You find it in little utility methods that are never called or select/case conditions that never occur.

The problem with dead code is that after a while it starts to smell. The older it is, the stronger and sourer the odor becomes. This is because dead code is not completely updated when designs change. It still compiles, but it does not follow newer conventions or rules. It was written at a time when the system was different. When you find dead code, do the right thing and delete it from the system.

### G9: Vertical Separation
---
Variables and functions should be defined close to where they are used. Local variables should be declared just above their first usage and should have a small vertical scope. We don’t want local variables declared hundreds of lines distant from their usages.	

### G10: Inconsistency
---
If you do something a certain way, do all similar things in the same way. This goes back to the principle of least surprise. Be careful with the conventions you choose, and once chosen, be careful to continue to follow them.

If within a particular function you use a variable named response to hold an `HttpServletResponse`, then use the same variable name consistently in the other functions that use `HttpServletResponse` objects. If you name a method `ProcessVerificationRequest`, then use a similar name, such as `ProcessDeletionRequest`, for the methods that process other kinds of requests.

Simple consistency like this, when reliably applied, can make code much easier to read and modify.

### G11: Clutter
---
Of what use is a default constructor with no implementation? All it serves to do is clutter up the code with meaningless artifacts. Variables that aren’t used, functions that are never called, comments that add no information, and so forth. All these things are clutter and should be removed. Keep your source files clean, well organized, and free of clutter.

### G12: Artificial Coupling
---
Things that don’t depend upon each other should not be artificially coupled. For example, general enums should not be contained within more specific classes because this forces the whole application to know about these more specific classes. The same goes for general purpose functions being declared in specific classes.

In general an artificial coupling is a coupling between two modules that serves no direct purpose. It is a result of putting a variable, constant, or function in a temporarily convenient, though inappropriate, location. This is lazy and careless.

Take the time to figure out where functions, constants, and variables ought to be declared. Don’t just toss them in the most convenient place at hand and then leave them there.

### G13: Feature Envy
---
The methods of a class should be interested in the variables and functions of the class they belong to, and not the variables and functions of other classes. When a method uses accessors and mutators of some other object to manipulate the data within that object, then it envies the scope of the class of that other object. It wishes that is were inside that other class so that it could have direct access to the variables it is manipulating. For example:
```vbnet
	Public Class HourlyPayCalculator
		Public Money calculateWeeklyPay(HourlyEmployee e)
			Dim tenthRate as Integer = e.getTenthRate().getPennies()
			Dim tenthsWorked as Integer = e.getTenthsWorked()
			Dim straightTime as Integer = Math.min(400, tenthsWorked)
			Dim overTime as Integer = Math.Max(0, tenthsWorked - straightTime)
			Dim straightPay as Integer = straightTime * tenthRate
			Dim overtimePay as Integer = Math.round(overTime*tenthRate*1.5)
			Return new Money(straightPay + overtimePay)	
 ```
	
The `calculateWeeklyPay` method reaches into the `HourlyEmployee` object to get the data on which it operates. The `calculateWeeklyPay` methods envies the scope of `HourlyEmployee`. It ‘wishes’ that it could be inside `HourlyEmployee`.

All else being equal, we want to eliminate Feature Envy because it exposes the internals of one class to another. Sometimes, however, Feature Envy is a necessary evil. Consider the following:
```vbnet
	Public Class HourlyEmployeeReport
		Private employee as HourlyEmployee
		
		Public Sub New(ByVal e as HourlyEmployee) 
			Me.employee = e		

		Function reportHours() as String
			Return String.format(
				“Name: %s\tHours:%d.%1d\n”,
				employee.getName(),
				employee.getTenthsWorked()/10,
				employee.getTenthsWorked()%10)
 ```

Clearly, the `reportHours` method envies the `HourlyEmployee` class. On the other hand, we don’t want `HourlyEmployee` to have to know about the format of the report. Moving that format string into the `HourlyEmployee` class would violate several principles of object oriented design. It would couple `HourlyEmployee` to the format of the report, exposing it to changes in that format.

### G14: Selector Arguments
---
There is hardly anything more abominable than a dangling false argument at the end of a function call. What does it mean? What would it change if it were true? Not only is the purpose of a selector argument difficult to remember, each selector argument combines many functions into one. Selector arguments are just a lazy way to avoid splitting a large function into several smaller functions. Consider:
```vbnet
	Public Function calculateWeeklyPay(ByVal overtime as Boolean) as Integer
		Dim tenthRate as Integer = getTenthRate()
		Dim tenthsWorked as Integer = getTenthsWorked()
		Dim straightTime as Integer = Math.min(400, tenthsWorked) 
		Dim overTime as Integer = Math.max(0, tenthsWorked - straightTime)
		Dim straightPay as Integer = straightTime * tenthRate
		Dim overtimeRate as Double = overtime ? 1.5 : 1.0 * tenthRate
		Dim overtimePay as Integer = Math.round(overTime*overtimeRate)
		Return straightPay + overtimePay
```
You call this function with a true if overtime is paid as time and a half, and with a false if overtime is paid as straight time. It’s bad enough that you must remember what calculateWeeklyPay(false) means whenever you happen to stumble across it. But the real shame of a function like this is that the author missed the opportunity to write the following:
```vbnet	
	Public Function straightPay() as Integer
		Return getTenthsWorked() * getTenthRate()

	Public Function overTimePay() as Integer
		Dim overTimeTenths as Integer = Math.max(0, getTenthsWorked() - 400)
		Dim overTimePay as Integer = overTimeBonus(overTimeTenths)
		Return straightPay() + overTimePay

	Private Function overTimeBonus(int overTimeTenths) as Integer
		Dim bonus as Double = 0.5 * getTenthRate() * overTimeTenths 
		Return Math.round(bonus) 
```
Of course, selecters need not be boolean. They can be enums, integers, or any other type of argument that is used to select the behavior of a function. In general it is better to have many functions than to pass some code into a function to select the behavior.

### G15: Obscured Intent
---
We want code to be as expressive as possible. Run-on expressions, Hungarian notation, and magic numbers all obscure the author’s intent. For example, here is the overTimePay function as it might have appeared:
```vbnet
		Public Function m_otCalc() as Integer 
			Return iThsWkd * iThsRte + CInt(Math.round(0.5 * iThsRte * Math.max(0, iThsWkd - 400)))
```
Small and dense as this might appear, it’s also virtually impenetrable. It is worth taking the time to make the intent of our code visible to our readers.

### G16: Misplaced Responsibility
---
One of the most important decisions a software developer can make is where to put code. For example, where should the `PI` constant go? Should it be in the `Math` class? Perhaps it belongs in the `Trigonometry` class? Or maybe in the `Circle` class?

The principle of least surprise comes into play here. Code should be placed where a reader would naturally expect it to be. The `PI` constant should go where the trig functions are declared. The `OVERTIME_RATE` constant should be declared in the `HourlyPay-Calculator` class.

Sometimes we get ‘clever’ about where to put certain functionality. We’ll put it in a function that’s convenient for us, but not necessarily intuitive to the reader. For example, perhaps we need to print a report with the total number of hours that an employee worked. We could sum up those hours in the code that prints the report, or we could try to keep a running total in the code that accepts time cards. One way to make this decision is to look at the names of functions. Let’s say that our report module has a function named `getTotalHours`. Let’s also say that the module that accepts time cards has a `saveTimeCard` function. Which of these two functions, by its name, implies that it calculates the total? The answer should be obvious.

Clearly, there are sometimes performance reasons why the total should be calculated as time cards are accepted rather than when the report is printed. That’s fine, but the names of the functions ought to reflect this. For example, there should be a `computeRunning-TotalOfHours` function in the timecard module.

### G17: Use Explanatory Variables
---
One of the most powerful ways to make a program readable is to break the calculations up into intermediate values that are held in variables with meaningful names. Consider the following:
```vbnet
	Dim match as Matcher = headerPattern.matcher(line)
	If match.find()	Then	
		Dim key as String = match.group(1)
		Dim value as String = match.group(2)
		headers.put(key.toLowerCase(), value)
```
The simple use of explanatory variables makes it clear that the first matched group is the key, and the second matched group is the value.

It is hard to overdo this. More explanatory variables are generally better than fewer. It is remarkable how an opaque module can suddenly become transparent simply by breaking the calculations up into well-named intermediate values.

### G18: Function Names Should Say What They Do
---
Look at this code:
```vbnet
		Dim newDate as Date = date.add(5)
```
Would you expect this to add five days to the date? Or is it weeks, or hours? Is the date instance changed or does the function just return a new `Date` without changing the old one? You can’t tell from the call what the function does.

If the function adds five days to the date and changes the date, then it should be called `addDaysTo` or `increaseByDays`. If, on the other hand, the function returns a new date that is five days later but does not change the date instance, it should be called `daysLater` or `daysSince`.

If you have to look at the implementation (or documentation) of the function to know what it does, then you should work to find a better name or rearrange the functionality so that it can be placed in functions with better names.

### G19: Understand the Algorithm
---
Lots of very funny code is written because people don’t take the time to understand the algorithm. They get something to work by plugging in enough if statements and flags, without really stopping to consider what is really going on.

Programming is often an exploration. You think you know the right algorithm for something, but then you wind up fiddling with it, prodding and poking at it, until you get it to ‘work.’ How do you know it ‘works’? Because it pases the test cases you can think of.

There is nothing wrong with this approach. Indeed, often it is the only way to get a function to do what you think it should. However, it is not sufficient to leave the quotation marks around the word ‘work.’

Before you consider yourself to be done with a function, make sure you understand how it works. It is not good enough that it passes all the tests. You must know that the solution is correct.

Often the best way to gain this knowledge and understanding is to refactor the function into something that is so clean and expressive that it is obvious how it works.

### G20: Make Logical Dependencies Physical
---
If one module depends upon another, that dependency should be physical, not just logical. The dependent module should not make assumptions (in other words, logical dependencies) about the module it depends upon. Rather it should explicitly ask that module for all the information it depends upon.
For example, imagine that you are writing a function that prints a plain text report of hours worked by employees. One class named `HourlyReporter` gathers all the data into a convenient form and then passes it to `HourlyReportFormatter` to print it.
```vbnet	
	Public class HourlyReporter
		Private formatter as HourlyReportFormatter
		Private page as List(Of LineItem) 
		Private const PAGE_SIZE as Integer = 55 

		Public Sub New(ByVal formatter as HourlyReportFormatter)
			Me.formatter = formatter 
			page = New ArrayList(Of LineItem) 

		Public Sub generateReport(ByVal employees as List(Of HourlyEmployee))  
			For Each e as employee in employees: 
				addLineItemToPage(e)
				If page.size() == PAGE_SIZE Then 
					printAndClearItemList()
			If page.size() > 0 Then
				printAndClearItemList()

		Private Sub printAndClearItemList() 
			formatter.format(page)
			page.clear() 

		Private Sub addLineItemToPage(ByVal e as HourlyEmployee)  
			Dim item as New LineItem()
			item.name = e.getName()
			item.hours = e.getTenthsWorked() / 10 
			item.tenths = e.getTenthsWorked() % 10 
			page.add(item)

	Public Class LineItem
		Public name as String
		Public hours as Integer
		Public tenths as Integer
```
This code has a logical dependency that has not been physicalized. It is the constant `PAGE_SIZE`. Why should `HourlyReporter` know the size of the page? Page size should be the responsibility of the `HourlyReportFormatter`.

The fact that `PAGE_SIZE` is declared in `HourlyReporter` represents a misplaced responsibility that causes `HourlyReporter` to assume that it knows what the page size ought to be. Such an assumption is a logical dependency. `HourlyReporter` depends on the fact that `HourlyReporterFormatter` can deal with page sizes of 55. If some implementation of `HourlyReportFormatter` could not deal with such sizes, then there would be an error.

We can physicalize this dependency by creating a new method in `HourlyReportFormatter` named `getMaxPageSize()`. `HourlyReporter` will then call that function rather than using the `PAGE_SIZE` constant.

### G21: Replace Magic Numbers with Named Constants
---
In general it is a bad idea to have raw numbers in your code. You should hide them behind well-named constants.

For example, the number 86,400 should be hidden behind the constant `SECONDS_PER_DAY`. If you are printing 55 lines per page, then the constant 55 should be hidden behind the constant `LINES_PER_PAGE`.

Some constants are so easy to recognize that they don’t always need a named constant to hide behind so long as they are used in conjunction with very self-explanatory code. For example:
```vbnet
	Dim milesWalked as Double= feetWalked/5280.0 
	Dim dailyPay as Integer = hourlyRate * 8 
	Dim circumference as Double = radius * Math.PI * 2
```
Do we really need the constants `FEET_PER_MILE`, `WORK_HOURS_PER_DAY`, and `TWO` in the above examples? Clearly, the last case is absurd. There are some formulae in which constants are simply better written as raw numbers. You might argue the `WORK_HOURS_PER_DAY` case because the laws or conventions might change. On the other hand, the formula reads so nicely with the 8 in it that I would be reluctant to add 17 extra characters to the readers’ burden. And in the `FEET_PER_MILE` case, the number 5280 is so very well known and so unique a constant that readers would recognize it even if it stood alone on a page with no context surrounding it.

Constants like 3.14159265 are also very well known and easily recognizable. However, the chance for error is too great to leave them raw. Every time someone sees 3.14159275, they know that it is `PI`, and so they fail to scrutinize it. (Did you catch the single-digit error?) We also don’t want people using 3.14, 3.14159, 3.142, and so forth. Therefore, it is a good thing that `Math.PI` has already been defined for us.

The term “Magic Number” does not apply only to numbers. It applies to any token that has a value that is not self-describing. For example:
```vbnet
	assertEquals(7777, Employee.find(“John Doe”).employeeNumber())
```
There are two magic numbers in this assertion. The first is obviously 7777, thought what it might mean is not obvious. The second magic number is “John Doe,” and again the intent is not clear.

Imagine that “John Doe” is the name of employee 7777 in a well-known test database created by your team. Everyone in the team knows that when you connect to this database, it will have several employees already cooked into it with well-known values and attributes. It also turns out that “John Doe” represents the sole hourly employee in that test database. So this this should really read:	
```vbnet
	assertEquals(HOURLY_EMPLOYEE_ID, Employee.find(HOURLY_EMPLOYEE_NAME).employeeNumber())
```

### G22: Be Precise
---
Expecting the first match to be the only match to a query is probably naive. Using floating point numbers to represent currency is almost criminal. Avoiding locks and/or transaction management because you don’t think concurrent update is likely is lazy. Declaring a variable to be an `ArrayList` when a `List` will do is overly constraining. Making all variables protected by default is not constraining enough.

When you make a decision in your code, make sure you make it precisely. Know why you have made it and how you will deal with any exceptions. Don’t be lazy about the precision of your decisions. If you decide to call a function that might return null, make sure the check for null. If you query for what you think is the only record in the database, make sure your code checks to be sure there aren’t others. If you need to deal with currency, use integers and deal with rounding appropriately. If there is the possibility of concurrent update, make sure you implement some kind of locking mechanism.

Ambiguities and imprecision in code are either a result of disagreements or laziness. In either case they should be eliminated.

### G23: Structure over Convention
---
Enforce design decisions with structure over convention. Naming conventions are good, but they are inferior to structures that force compliance. For example, `select/cases` with nicely named enumerations are inferior to base classes with abstract methods. No one is forced to implement the `select/case` statement the same way each time; but the base classes do enforce that concrete classes have all abstract methods implemented.

### G24: Encapsulate Conditionals
---
Boolean logic is hard enough to understand without having to see it in the context of an if or while statement. Extract functions that explain the intent of the conditional.

For example:
```vbnet
	If shouldBeDeleted(timer)
```
Is preferable to
```vbnet
	if timer.hasExpired() and Not timer.isRecurrent()
```
### G25: Avoid Negative Conditionals
---
Negatives are just a bit harder to understand than positives. So, when possible, conditionals should be expressed as positives. 

For example:
```vbnet
	If buffer.shouldCompact()
```
Is preferable to
```vbnet
	If Not buffer.shouldNotCompact()
```
### G26: Functions Should Do One Thing
---
It is often tempting to create functions that have multiple sections that perform a series of operations. Functions of this kind do more than one thing, and should be converted into many smaller functions, each of which does one thing.

For example:	
```vbnet
	Public Sub pay()  
		For Each e as Employee in employees
			If e.isPayday() Then
				Dim pay as Money = e.calculatePay() 
				e.deliverPay(pay) 
```

This bit of code does three things. It loops over all the employees, checks to see whether each employee ought to be paid, and then pays the employee. This code would be better written as:
```vbnet
	Public Sub pay() 
		For Each e as Employee in employees:
			payIfNecessary(e)

	Private Sub payIfNecessary(ByVal e as Employee)  
		If e.isPayday() Then
			calculateAndDeliverPay(e) 

	Private Sub calculateAndDeliverPay(ByVal e as Employee) 
		Dim pay as Money = e.calculatePay()
		e.deliverPay(pay)
```
Each of these functions does one thing.

### G27: Hidden Temporal Couplings
---
Temporal couplings are often necessary, but you should not hide the coupling. Structure the arguments of your functions such that the order in which they should be called is obvious. Consider the following:
```vbnet
	Public Class MoogDiver 
		Private gradient as Gradient
		Private splines as List(Of Spline)

		Public Sub dive(ByVal reason as String) 
			saturateGradient()
			reticulateSplines() 
			diveForMoog(reason)             
		… 
```
The order of the three functions is important. You must saturate the gradient before you can reticulate the splines, and only then can you dive for the moog. Unfortunately, the code does not enforce this temporal coupling. Another programmer could call `reticulateSplines` before `saturateGradient` was called, leading to an `UnsaturatedGradientException`. A better solution is:
```vbnet
	Public Class MoogDiver
		Private gradient as Gradient
		Private splines as List(Of Spline)

		Public Sub dive(ByVal reason as String) 
			Dim gradient as Gradient= saturateGradient() 
			Dim splines as List(Of Spline) = reticulateSplines(gradient)
			diveForMoog(splines, reason)
		…
```
This exposes the temporal coupling by creating a bucket brigade. Each function produces a result that the next function needs, so there is no reasonable way to call them out of order.

You might complain that this increases the complexity of the functions, and you’d be right. But that extra syntactic complexity exposes the true temporal complexity of the situation.	

### G28: Don’t be Arbitrary
---
Have a reason for the way you structure your code, and make sure that reason is communicated by the structure of the code. If a structure appears consistently throughout the system, others will use it and preserve the convention. 

For example:
```vbnet	
	Public Class AliasLinkWidget
		Inherits ParentWidget 

		public NotInheritable Class VariableExpandingWidgetRoot 
			… 
		… 
```
The problem with this is that `VariableExpandingWidgetRoot` had no need to be inside the scope of `AliasLinkWidget`. Moreover, other unrelated classes made use of `AliasLinkWidget.VariableExpandingWidgetRoot`. These classes had no need to know about `AliasLinkWidget`. Perhaps the programmer had plopped the `VariableExpandingWidgetRoot` into `AliasWidget` as a matter of convenience, or perhaps he thought it really needed to be scoped inside `AliasWidget`. Whatever the reason, the result wound up being arbitrary. Public classes that are not utilities of some other class should not be scoped inside another class. The convention is to make them public at the top level of their package.

### G29: Encapsulate Boundary Conditions
---
Boundary conditions are hard to keep track of. Put the processing for them in one place. Don’t let them leak all over the code. We don’t want swarms of +1s and -1s scattered all over our code.

Consider the following:
```vbnet
	If level + 1 < tags.length Then
		Dim parts as New Parse(body, tags, level + 1, offset + endTag)
		Dim body as String = null 
```
Notice that `level+1` appears twice. This is a boundary condition that should be encapsulated within a variable a variable named something like `nextLevel`.
```vbnet
	Dim nextLevel as Integer = level + 1
	If nextLevel < tags.length 
 		Dim parts as New Parse(body, tags, nextLevel, offset + endTag)
		Dim body as String = null
```
### G30: Functions Should Descend Only One Level of Abstraction
---
The statements within a function should all be written at the same level of abstraction, which should be one level below the operation described by the name of the function. This may be the hardest of these heuristics to interpret and follow. Though the idea is plain enough, humans are just far too good at seamlessly mixing levels of abstraction.

Consider the following:
```vbnet
	Public Function render() as String
 	Dim html as StringBuffer = new StringBuffer(“<hr”)
 	if size > 0 Then
		html.append(” size=\“”).append(size + 1).append(”\“”)
 	html.append(“>”)
 	Return html.toString()
```
This function constructs the HTML tag that draws a horizontal rule across the page. The height of that rule is specified in the size variable.
This method is mixing at least two levels of abstraction. The first is the notion that a horizontal rule has a size. The second is the syntax of the HR tag itself. This code comes from the `HruleWidget` module. This module detects a row of four or more dashes and converts it into the appropriate HR tag. The more dashes, the larger the size.
I refactored this bit of code as follows. Note that I changed the name of the size field to reflect its true purpose, it held the number of extra dashes.
```vbnet
	Public Function render() as String
 		Dim hr as HtmlTag = New HtmlTag(“hr”)
 		If extraDashes > 0 Then
 			hr.addAttribute(“size”, hrSize(extraDashes))
 		Return hr.html()

	Private Function hrSize(ByVal height as Integer) as String
 		Dim hrSize as Integer = height + 1
 		Return String.format(“%d”, hrSize)
```
This change separates the two levels of abstraction nicely. The render function simply constructs an HR tag, without having to know anything about the HTML syntax of that tag. The `HtmlTag` module takes care of all the nasty syntax issues.

Separating levels of abstraction is one of the most important functions of refactoring, and it’s one of the hardest to do well.

## G31: Keep Configurable Data at High Levels
---
If you have a constant such as a default or configuration value that is known and expected at a high level of abstraction, do not bury it in a low-level function. Expose it as an argument to that low-level function called from the high-level function. 

Consider the following:
```vbnet
	Public Sub main(String[] args) 
 		Dim arguments as Arguments = parseCommandLine(args)
 		… 

	Public Class Arguments
 		Public const DEFAULT_PATH as String = “.”
 		Public const DEFAULT_ROOT as String = “FitNesseRoot”
 		Public const DEFAULT_PORT as Integer = 80
 		Public const DEFAULT_VERSION_DAYS as Integer = 14
 		… 
```
The command-line arguments are parsed in the very first executable line. The default values of those arguments are specified at the top of the `Arguments` class. You don’t have to go looking in low levels of the system for statements like this one:
```vbnet
		If (arguments.port = 0)	‘use 80 by default
```
The configuration constants reside at a very high level and are easy to change. They get passed down to the rest of the application. The lower levels of the application do not own the values of these constants.

### G32: Avoid Transitive Navigation
---
In general we don’t want a single module to know much about its collaborators. More specifically, if A collaborates with B, and B collaborates with C, we don’t want modules that use A to know about C. (We don’t want `a.getB().getC().doSomething()`)

It is important to make sure that modules know only about their immediate collaborators and do not know the navigation map of the whole system.

If many modules used some form of the statement `a.getB().getC()`, then it would be difficult to change the design and architecture to interpose a Q between B and C. You’d have to find every instance of `a.getB().getC()` and convert it to `a.getB().getQ().getC()`. This is how architectures become rigid. Too many modules know too much about the architecture.

Rather, we want our immediate collaborators to offer all the services we need. We should not have to roam through the object graph of the system, hunting for the method we want to call. Rather we should simply be able to say:
		`myCollaborator.doSomething()`

## Names

### N1: Choose Descriptive Names
---
Don’t be too quick to choose a name. Make sure the name is descriptive. Remember that things tend to drift as software evolves, so frequently reevaluate the appropriateness of the names you choose.

Names in software are 90% of what makes it readable. You need to take the time to choose them wisely and keep them relevant. Names are too important to treat carelessly.

Consider the code below. What does it do? Without proper names it’s just a hodge-podge of symbols and magic numbers.
```vbnet
	Public Function x() as Integer 
		Dim q as Integer = 0 
		Dim z as Integer = 0
		For kk as Integer = 0 to 9 
			If l(z) = 10 
 				q += 10 + (l(z + 1) + l(z + 2))
 				z += 1
			Else If l(z) + l(z + 1) = 10 
 				q += 10 + l(z + 2)
 				z += 2
			Else 
				q += l(z) + l(z + 1)
 				z += 2
		Return q
```	
Here is the code the way it should be written. This snippet is actually less complete than the one above, yet you can infer immediately what it is trying to do, and you could very likely write the missing functions based on that inferred meaning.
```vbnet
	Public Function score() as Integer 
		Dim score as Integer = 0
		Dim frame as Integer = 0
		For frameNumber as Integer = 0 to 9
			If isStrike(frame) Then
 				score += 10 + nextTwoBallsForStrike(frame)
 				frame += 1
 			Else If (isSpare(frame)) 
 				score += 10 + nextBallForSpare(frame)
 				frame += 2
 			Else 
 				score += twoBallsInFrame(frame)
 				frame += 2
		Return score
```
The power of carefully chosen names is that they overload the structure of the code with description. That overloading sets the readers’ expectations about what the other functions in the module do. You can infer the implementation of `isStrike()` by looking at the code above. When you read the `isStrike()` method, it will be pretty much what you expected.
```vbnet		
	Private Function isStrike(int frame) as Boolean 
		Return rolls(frame) = 10
```
### N2: Choose Names at the Appropriate Level of Abstraction
---
Don’t pick names that communicate implementation; choose names that reflect the level of abstraction of the class or function you are working in. This is hard to do. Again, people are too good at mixing levels of abstractions. Each time you make a pass over your code, you will likely find some variable that is named at too low a level. You should take the opportunity to change those names when you find them. Making code readable requires a dedication to continuous improvement. Consider the `Modem` implementation below:
```vbnet
	Public Interface Modem
		Function dial(ByVal phoneNumber as String) as Boolean
		Function disconnect() as Boolean
		Function send(ByVal c as char) as Boolean
		Function recv() as Char
		Function getConnectedPhoneNumber() as String
```
At first this looks fine. The functions all seem appropriate. Indeed, for many applications they are. But now consider an application in which some modems aren’t connected by dialling. Rather they are connected permanently by hard wiring them together. Perhaps some are connected by sending a port number to a switch over a USB connection. Clearly the notion of phone numbers is at the wrong level of abstraction. A better naming strategy for this scenario might be:
```vbnet
	Public Interface Modem
		Function connect(ByVal connectionLocator as String) as Boolean
		Function disconnect() as Boolean
		Function send(ByVal c as Char) as Boolean
		Function recv() as Char
		Function getConnectedLocator() as String
```
Now the names don’t make any commitments about phone numbers. They can still be used for phone numbers, or they could be used for any other kind of connection strategy.

### N3: Use Standard Nomenclature Where Possible
---
Names are easier to understand if they are based on existing convention or usage. For example, if you are using the DECORATOR pattern, you should use the word Decorator in the names of the decorating classes. For example, `AutoHangupModemDecorator` might be the name of a class that decorates a `Modem` with the ability to automatically hang up at the end of a session.

Patterns are just one kind of standard. In Visual Basic, for example, functions that convert objects to string representations are often named `toString()`. It is better to follow conventions like these than to invent your own.

Teams will often invent their own standard system of names for a particular project. Your code should use the terms from this language extensively. In short, the more you can use names that are overloaded with special meanings that are relevant to your project, the easier it will be for readers to know what your code is talking about.

### N4: Unambiguous Names
---
Choose names that make the workings of a function or variable unambiguous. 

Consider the following:		
```vbnet
	Private Function doRename() as String
 		If refactorReferences
 			renameReferences()
		renamePage()
		pathToRename.removeNameFromEnd()
		pathToRename.addNameToEnd(newName)
		Return PathParser.render(pathToRename)
```
The name of this function does not say what the function does except in broad and vague terms. This is emphasized by the fact that there is a function named `renamedPage` inside the function named `doRename`. What do the names tell you about the difference between the two functions? Nothing.

A better name for that function is `renamePageAndOptionallyAllReferences`. This may seem long, and it is, but it’s only called from one place in the module, so it’s explanatory value outweighs the length.

### N5: Use Long Names for Long Scopes
---
The length of a name should be related to the length of the scope. You can use very short variable names for tiny scopes, but for big scopes you should use longer names.

Variable names like `i` and `j` are fine if their scope is five lines long. 

Consider the following:
```vbnet
	Private Sub rollMany(int n, int pins) 
		For i as Integer = 0 to n
 			g.roll(pins)
```
This is perfectly clear and would be obfuscated if the variable i were replaced with something annoying like `rollCount`. On the other hand, variables and functions with short names lose their meaning over long distances. So the longer the scope of the name, the longer and more precise the name should be.

### N6: Names Should Describe Side-Effects
---
Names should describe everything that a function, variable, or class is or does. Don’t hide side effects with a name. Don’t use a simple verb to describe a function that does more than just that simple action. For example:
```vbnet
	Public Function getOos() as ObjectOutputStream
		If m_oos = null then 
			m_oos = new ObjectOutputStream(m_socket.getOutputStream())
		Return m_oos
```
This function does a bit more than get an “oos”; it creates the “oos” if it hasn’t been created already. Thus, a better name might be `createOrReturnOos`.	
		
		
