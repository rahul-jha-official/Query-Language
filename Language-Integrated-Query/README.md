# Language Integrated Query (LINQ)
LINQ is a set of technologies that allow simple and efficient querying over different kind of data.
Language Integrated Query allows filtering, ordering and transforming the collection elements, and more.

LINQ can work with other types of collections like databases or XML files (Syntax remains the same).

<img src="https://github.com/rahul-jha-official/Query-Language/assets/138975150/c8a5607d-e26f-41d0-97fc-08c8efd7a022" width="65%">

# Benefit of LINQ
- It allows a simple, efficient, and unified way of querying over different kind of data.
- Queries execute only when result is needed.
- Code written with LINQ is cleaner, more readable and shorter.

# Lazy evaluation – “I’ll do it when it’s needed”
Lazy evaluation – or in LINQ’s term, deferred execution – means that something will only be called and evaluated when needed.

>  Lazy evaluation of an object means that its creation is deferred until it is first used. Lazy evaluation is primarily used to improve performance, avoid wasteful computation, and reduce program memory requirements.

In layman’s terms – until you use something in any form of (ToList(), ToArray(), for each, Any(), or any other action that uses the enumerator – the result will not happen, despite the code going over the line.

The difference is miniscule, but very, very important, as we will soon see.
Take the following code, for example:

    var validCustomers = customers.Where(x => IsValid(x));
    var ajay = validCustomers.FirstOrDefault(x => x.Name == “Ajay”);
    var priyansh = validCustomers.FirstOrDefault(x => x.Name == “Priyansh”); 
    var nakun = validCustomers.FirstOrDefault(x => x.Name == “Nakun”);

This code snippet will run the '(x => IsValid(x))' lambda three times per customer instead of once!

If IsValid is an expensive action (such as a DB call, or an external service call), or if it has any side effects (such as counting overall relevant customers) then we absolutely do not want to run it any more than necessary.

In cases such as this, we should enumerate on customers before saving it to the validCustomers variable.

    var validCustomers = customers.Where(x => IsValid(x)).ToList();
    var ajay = validCustomers.FirstOrDefault(x => x.Name == “Ajay”);
    var priyansh = validCustomers.FirstOrDefault(x => x.Name == “Priyansh”); 
    var nakun = validCustomers.FirstOrDefault(x => x.Name == “Nakun”);

Likewise, if we have the following case:

    var validCustomers = customers.Where(x => IsValid(x)).ToList()
    if (shouldReturn)
    {
     return;
    }
    return validCustomers;

We will have called the IsValid method for each customer for no reason.

**Properies**
- Deferred Execution means that the evaulation of a LINQ expression is delayed until the value is actually needed.
- It allows us to work on the latest data.
- It improves the performance, as the query is materialized only when it's actually needed, so we can avoid unnecessary execution.


# Lambda Expression
Lambda Expression allow us to create anonymous function. In C# we can use function like any other types - store them in variables or pass as parameters.

# Extension Methods
An extension method is a method defined outside of a type, that can be called upon this type's objects as a regular member methods.

Extension Methods allow us to add new functionality to a type without modifying it.


