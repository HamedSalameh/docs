## Enumeration classes ##
---
problem statement  

sample code:
```csharp
public void ProcessBonus(Employee employee)
{
    switch(employee.Type)
    {
        case EmployeeType.Manager:
            employee.Bonus = 1000m;
            break;
        case EmployeeType.Servant:
            employee.Bonus = 0.01m;
            break;
        case EmployeeType.AssistantToTheRegionalManager:
            employee.Bonus = 1.0m;
            break;
        default:
            throw new ArgumentOutOfRangeException();
    }
}
```

There are a few problems with enumerations like this:

* Behavior related to the enumeration gets scattered around the application
* New enumeration values require shotgun surgery
* Enumerations don’t follow the Open-Closed Principle

Adding a new enumeration value is quite a large pain, as there are lots of these switch statements around to go back and modify.  In the case above, we want the “default” behavior for defensive coding purposes, but a new enumeration value will cause an exception to be thrown.

-- show here what is the benefit of using enumeration classes over normal Enums.   
With example on code.

based on :

[Enumation Classes - I](https://lostechies.com/jimmybogard/2008/08/12/enumeration-classes)  

[Steve smith](https://ardalis.com/enum-alternatives-in-c/)  

  